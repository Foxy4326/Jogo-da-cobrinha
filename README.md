using UnityEngine;
using System.Collections.Generic;

public class SNAKE_GameManager : MonoBehaviour
{
    [Header("Assets 3D e Prefabs")]
    public GameObject snakeHeadPrefab;
    public GameObject bodySegmentPrefab;
    public GameObject foodPrefab;
    public GameObject groundPlane;

    [Header("Configurações da Cobra")]
    public float snakeSpeed = 5f;
    public float rotationSpeed = 100f;
    public float distanceBetweenSegments = 0.5f;

    [Header("Configurações da Comida")]
    public float foodSpawnRadius = 5f;
    public float foodSpawnHeightOffset = 0.1f;

    [Header("Configurações de Controle")]
    public bool useKeyboard = true; // Teclado para Web, Toque para Mobile

    // Variáveis do jogo
    private GameObject snakeHeadInstance;
    private List<GameObject> bodySegments = new List<GameObject>();
    private Vector3 lastHeadPosition;
    private bool gameStarted = false;
    private GameObject currentFoodInstance;
    private float groundSize;

    void Start()
    {
        InitializeGame();
    }

    void Update()
    {
        if (!gameStarted)
        {
            HandleGameStart();
        }
        else
        {
            HandleSnakeInputAndMovement();
            HandleBodyTracking();
            CheckBoundaries();
        }
    }

    private void InitializeGame()
    {
        // Configura o tamanho do terreno
        if (groundPlane != null)
        {
            groundSize = groundPlane.transform.localScale.x * 5f; // Assume plane scale 10x10
        }
        else
        {
            groundSize = 20f;
        }

        Debug.Log("Pressione ESPAÇO para iniciar o jogo");
    }

    private void HandleGameStart()
    {
        if (Input.GetKeyDown(KeyCode.Space) && snakeHeadInstance == null)
        {
            StartSnakeGame(Vector3.zero);
        }
    }

    private void StartSnakeGame(Vector3 startPosition)
    {
        snakeHeadInstance = Instantiate(snakeHeadPrefab, startPosition, Quaternion.identity);
        lastHeadPosition = startPosition;
        gameStarted = true;

        // Configura componentes físicos
        SetupSnakeHead();

        // Inicia o jogo
        AddSegment();
        SpawnFood();

        Debug.Log("Jogo Iniciado! Use A/D ou ←/→ para girar");
    }

    private void SetupSnakeHead()
    {
        Rigidbody rb = snakeHeadInstance.AddComponent<Rigidbody>();
        rb.isKinematic = true;
        rb.useGravity = false;
        
        SphereCollider collider = snakeHeadInstance.AddComponent<SphereCollider>();
        collider.isTrigger = true;
        collider.radius = 0.2f;

        // Componente de colisão
        CollisionTriggerDelegate triggerDelegate = snakeHeadInstance.AddComponent<CollisionTriggerDelegate>();
        triggerDelegate.gameManager = this;
    }

    private void HandleSnakeInputAndMovement()
    {
        // Movimento constante para frente
        snakeHeadInstance.transform.Translate(Vector3.forward * snakeSpeed * Time.deltaTime);

        // Controles - Web (Teclado) e Mobile (Toque)
        float rotationInput = 0f;

        if (useKeyboard)
        {
            // Controle por teclado
            if (Input.GetKey(KeyCode.A) || Input.GetKey(KeyCode.LeftArrow))
                rotationInput = -1f;
            else if (Input.GetKey(KeyCode.D) || Input.GetKey(KeyCode.RightArrow))
                rotationInput = 1f;
        }
        else
        {
            // Controle por toque (mobile)
            if (Input.touchCount > 0)
            {
                Touch touch = Input.GetTouch(0);
                if (touch.phase == TouchPhase.Moved)
                {
                    rotationInput = touch.deltaPosition.x * 0.1f;
                }
            }
        }

        // Aplica rotação
        if (rotationInput != 0f)
        {
            float rotationAmount = rotationInput * rotationSpeed * Time.deltaTime;
            snakeHeadInstance.transform.Rotate(Vector3.up, rotationAmount);
        }
    }

    private void HandleBodyTracking()
    {
        if (Vector3.Distance(snakeHeadInstance.transform.position, lastHeadPosition) > distanceBetweenSegments)
        {
            for (int i = bodySegments.Count - 1; i > 0; i--)
            {
                bodySegments[i].transform.position = bodySegments[i - 1].transform.position;
                bodySegments[i].transform.rotation = bodySegments[i - 1].transform.rotation;
            }

            if (bodySegments.Count > 0)
            {
                bodySegments[0].transform.position = lastHeadPosition;
                bodySegments[0].transform.rotation = snakeHeadInstance.transform.rotation;
            }

            lastHeadPosition = snakeHeadInstance.transform.position;
        }
    }

    private void CheckBoundaries()
    {
        Vector3 headPosition = snakeHeadInstance.transform.position;
        
        // Verifica se saiu dos limites do terreno
        if (Mathf.Abs(headPosition.x) > groundSize/2 || Mathf.Abs(headPosition.z) > groundSize/2)
        {
            GameOver();
        }
    }

    public void AddSegment()
    {
        Vector3 spawnPos = bodySegments.Count == 0 ? 
            snakeHeadInstance.transform.position : 
            bodySegments[bodySegments.Count - 1].transform.position;

        GameObject newSegment = Instantiate(bodySegmentPrefab, spawnPos, Quaternion.identity);
        
        // Configura colisão
        newSegment.tag = "Body";
        BoxCollider segmentCollider = newSegment.GetComponent<BoxCollider>();
        if (segmentCollider == null)
            segmentCollider = newSegment.AddComponent<BoxCollider>();
        segmentCollider.isTrigger = true;

        bodySegments.Add(newSegment);
    }

    private void SpawnFood()
    {
        if (currentFoodInstance != null) return;

        Vector3 spawnPosition;
        int attempts = 0;
        bool validPosition = false;

        // Tenta encontrar uma posição válida
        do
        {
            Vector2 randomCircle = Random.insideUnitCircle.normalized * Random.Range(2f, foodSpawnRadius);
            spawnPosition = new Vector3(randomCircle.x, foodSpawnHeightOffset, randomCircle.y);
            
            // Verifica se não está muito perto da cobra
            float distanceToSnake = Vector3.Distance(spawnPosition, snakeHeadInstance.transform.position);
            validPosition = distanceToSnake > 3f;
            
            attempts++;
        } while (!validPosition && attempts < 10);

        currentFoodInstance = Instantiate(foodPrefab, spawnPosition, Quaternion.identity);
        currentFoodInstance.tag = "Food";

        // Configura colisão da comida
        SphereCollider foodCollider = currentFoodInstance.GetComponent<SphereCollider>();
        if (foodCollider == null)
            foodCollider = currentFoodInstance.AddComponent<SphereCollider>();
        foodCollider.isTrigger = true;
        foodCollider.radius = 0.3f;
    }

    public void HandleCollision(Collider other)
    {
        if (!gameStarted) return;

        if (other.CompareTag("Food"))
        {
            Destroy(other.gameObject);
            currentFoodInstance = null;
            AddSegment();
            SpawnFood();
            snakeSpeed += 0.1f; // Aumento gradual de velocidade
            Debug.Log($"Comida coletada! Tamanho: {bodySegments.Count - 1}");
        }
        else if (other.CompareTag("Body"))
        {
            GameOver();
        }
    }

    private void GameOver()
    {
        gameStarted = false;
        snakeSpeed = 0f;

        int score = bodySegments.Count - 1;
        Debug.LogWarning($"GAME OVER! Pontuação: {score}");

        // Opcional: Mostrar UI de game over
        Invoke("RestartGame", 3f);
    }

    public void RestartGame()
    {
        // Limpa objetos antigos
        if (snakeHeadInstance != null) Destroy(snakeHeadInstance);
        foreach (var segment in bodySegments)
        {
            if (segment != null) Destroy(segment);
        }
        if (currentFoodInstance != null) Destroy(currentFoodInstance);

        // Reseta variáveis
        bodySegments.Clear();
        gameStarted = false;
        snakeSpeed = 5f;
        currentFoodInstance = null;
        snakeHeadInstance = null;

        Debug.Log("Pressione ESPAÇO para jogar novamente");
    }
}