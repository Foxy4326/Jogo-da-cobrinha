using UnityEngine;
using UnityEngine.XR.ARFoundation;
using UnityEngine.XR.ARSubsystems;
using System.Collections.Generic;

// O componente ARRaycastManager é essencial para a interação com o plano de RA.
[RequireComponent(typeof(ARRaycastManager))]
public class ARSNAKE_GameManager : MonoBehaviour
{
    // === VINCULAÇÕES NO INSPECTOR (Configurável no Unity Editor) ===

    [Header("Assets 3D e Prefabs")]
    public GameObject snakeHeadPrefab;      // O modelo 3D da cabeça da cobra
    public GameObject bodySegmentPrefab;    // O modelo 3D de cada segmento do corpo
    public GameObject foodPrefab;           // O modelo 3D da comida (frutas/orbes)

    // Você deve vincular e desativar este no Inspector após o início do jogo para ocultar os planos.
    [Header("Gerenciadores de RA")]
    public ARPlaneManager arPlaneManager;

    // === CONFIGURAÇÕES DE GAMEPLAY ===

    [Header("Configurações da Cobra")]
    public float snakeSpeed = 5f;          // Velocidade de movimento
    public float rotationSpeed = 100f;     // Velocidade de rotação (sensibilidade do toque)
    public float distanceBetweenSegments = 0.5f; // Distância que a cabeça deve percorrer antes de mover o corpo

    [Header("Configurações da Comida")]
    public float foodSpawnRadius = 5f;     // Raio máximo para gerar nova comida
    public float foodSpawnHeightOffset = 0.1f; // Pequeno ajuste vertical para a comida não afundar no chão

    // === VARIÁVEIS INTERNAS DO JOGO ===

    private ARRaycastManager arRaycastManager;
    private static List<ARRaycastHit> hits = new List<ARRaycastHit>();

    private GameObject snakeHeadInstance;
    private List<GameObject> bodySegments = new List<GameObject>();
    private Vector3 lastHeadPosition;
    private bool gameStarted = false;
    private GameObject currentFoodInstance;

    // === UNITY LIFECYCLE ===

    void Awake()
    {
        // Obtém o componente ARRaycastManager que deve estar no mesmo objeto
        arRaycastManager = GetComponent<ARRaycastManager>();
    }

    void Update()
    {
        if (!gameStarted)
        {
            HandleGameStartTouch();
        }
        else
        {
            // O jogo está rodando:
            HandleSnakeInputAndMovement();
            HandleBodyTracking();
        }
    }

    // --- MÉTODOS DE INÍCIO DO JOGO (AR FOUNDATION) ---

    private void HandleGameStartTouch()
    {
        // Espera por um toque e se a cobra já existe, não faz nada
        if (snakeHeadInstance != null || Input.touchCount == 0)
            return;

        Touch touch = Input.GetTouch(0);

        // Se o toque começou
        if (touch.phase == TouchPhase.Began)
        {
            // Tenta fazer um Raycast a partir do toque em um plano detectado
            if (arRaycastManager.Raycast(touch.position, hits, TrackableType.PlaneWithinPolygon))
            {
                // Se acertar um plano, inicia a cobra
                Pose hitPose = hits[0].pose;
                StartSnakeGame(hitPose.position);
            }
        }
    }

    private void StartSnakeGame(Vector3 startPosition)
    {
        // 1. Instancia a Cabeça da Cobra
        snakeHeadInstance = Instantiate(snakeHeadPrefab, startPosition, Quaternion.identity);
        lastHeadPosition = startPosition;
        gameStarted = true;

        // 2. Configura a Colisão na Cabeça (Manual para protótipo)
        Rigidbody rb = snakeHeadInstance.AddComponent<Rigidbody>();
        rb.isKinematic = true;
        SphereCollider collider = snakeHeadInstance.AddComponent<SphereCollider>();
        collider.isTrigger = true;

        // ESTE É O COMPONENTE DELEGAR O EVENTO DE COLISÃO
        // ANEXAMOS A CLASSE DELEGADA (AGORA ANINHADA) À CABEÇA DA COBRA
        CollisionTriggerDelegate triggerDelegate = snakeHeadInstance.AddComponent<CollisionTriggerDelegate>();
        triggerDelegate.gameManager = this; // Vincula o GameManager à nova instância de colisão

        // 3. Desativa o visualizador de planos
        if (arPlaneManager != null)
        {
            arPlaneManager.enabled = false;
            foreach (var plane in arPlaneManager.trackables)
            {
                plane.gameObject.SetActive(false);
            }
        }

        // 4. Inicia o Jogo
        AddSegment(); // Adiciona o primeiro segmento
        SpawnFood();
    }

    // --- MÉTODOS DE JOGABILIDADE ---

    private void HandleSnakeInputAndMovement()
    {
        // 1. Movimento Constante para Frente
        snakeHeadInstance.transform.Translate(Vector3.forward * snakeSpeed * Time.deltaTime);

        // 2. Rotação (Baseada no arrasto do toque)
        if (Input.touchCount > 0)
        {
            Touch touch = Input.GetTouch(0);

            if (touch.phase == TouchPhase.Moved)
            {
                // Usa a mudança horizontal do toque para girar a cobra
                float rotationAmount = touch.deltaPosition.x * Time.deltaTime * rotationSpeed;
                snakeHeadInstance.transform.Rotate(Vector3.up, rotationAmount);
            }
        }
    }

    private void HandleBodyTracking()
    {
        // Verifica se a cabeça se moveu o suficiente (maior que 'distanceBetweenSegments')
        if (Vector3.Distance(snakeHeadInstance.transform.position, lastHeadPosition) > distanceBetweenSegments)
        {
            // Move todos os segmentos, do último ao primeiro, para a posição do anterior
            for (int i = bodySegments.Count - 1; i > 0; i--)
            {
                bodySegments[i].transform.position = bodySegments[i - 1].transform.position;
            }

            // Move o primeiro segmento para a posição anterior da cabeça
            if (bodySegments.Count > 0)
            {
                bodySegments[0].transform.position = lastHeadPosition;
            }

            // Atualiza a posição de referência
            lastHeadPosition = snakeHeadInstance.transform.position;
        }
    }

    public void AddSegment()
    {
        GameObject newSegment;
        Vector3 spawnPos = Vector3.zero;

        if (bodySegments.Count == 0)
        {
            spawnPos = snakeHeadInstance.transform.position;
        }
        else
        {
            GameObject lastSegment = bodySegments[bodySegments.Count - 1];
            spawnPos = lastSegment.transform.position;
        }

        newSegment = Instantiate(bodySegmentPrefab, spawnPos, Quaternion.identity);

        // Configuração de colisão para o segmento (IMPORTANTE: Tag "Body")
        newSegment.tag = "Body";
        if (newSegment.GetComponent<Collider>() == null)
        {
            newSegment.AddComponent<BoxCollider>().isTrigger = true;
        }

        bodySegments.Add(newSegment);
    }

    private void SpawnFood()
    {
        if (currentFoodInstance != null) return;

        // Gera um ponto aleatório em um círculo no plano XZ em torno da cobra
        Vector2 randomCircle = Random.insideUnitCircle.normalized * Random.Range(1f, foodSpawnRadius);
        Vector3 spawnPosition = snakeHeadInstance.transform.position + new Vector3(randomCircle.x, 0, randomCircle.y);

        // Ajusta a altura (Y) para que a comida flutue um pouco acima do plano
        spawnPosition.y = snakeHeadInstance.transform.position.y + foodSpawnHeightOffset;

        currentFoodInstance = Instantiate(foodPrefab, spawnPosition, Quaternion.identity);
        currentFoodInstance.tag = "Food";

        if (currentFoodInstance.GetComponent<Collider>() == null)
        {
             currentFoodInstance.AddComponent<SphereCollider>().isTrigger = true;
        }
    }

    // Este é o método que será chamado quando a cabeça da cobra colidir com algo.
    public void HandleCollision(Collider other)
    {
        if (!gameStarted) return;

        // ** 1. Colisão com a Comida **
        if (other.CompareTag("Food"))
        {
            Destroy(other.gameObject);
            currentFoodInstance = null;
            AddSegment();
            SpawnFood();
            snakeSpeed += 0.2f; // Opcional: Aumenta a velocidade
            Debug.Log("Comida coletada! Tamanho: " + bodySegments.Count);
        }

        // ** 2. Colisão com o Corpo (Game Over)**
        else if (other.CompareTag("Body"))
        {
            GameOver();
        }
    }

    private void GameOver()
    {
        gameStarted = false;
        snakeSpeed = 0f;

        Debug.LogWarning("GAME OVER! Sua pontuação: " + (bodySegments.Count - 1));

        // Lógica de UI de Game Over
    }


    // === CLASSE DELEGADA ANINHADA PARA LIDAR COM COLISÃO (ÚNICA FORMA DE FAZER TUDO EM 1 ARQUIVO) ===
    // Esta classe deve ser anexada à cabeça da cobra (snakeHeadInstance) para pegar o evento OnTriggerEnter.
    // O Unity permite que classes MonoBehaviour sejam definidas DENTRO da classe principal (como aninhadas),
    // o que resolve o problema de ter duas classes MonoBehaviour separadas.
    public class CollisionTriggerDelegate : MonoBehaviour
    {
        public ARSNAKE_GameManager gameManager; // O GameManager é público para ser vinculado

        private void OnTriggerEnter(Collider other)
        {
            if (gameManager != null)
            {
                // Encaminha a colisão para o método centralizado no GameManager
                gameManager.HandleCollision(other);
            }
        }
    }
}
