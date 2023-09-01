# Module 9 - Design and implement a replication strategy for Azure Cosmos DB SQL API

### Estimated Timing: 60 minutes

## Lab 2: Configure consistency models in the portal and the Azure Cosmos DB SQL API SDK

The default consistency level for new Azure Cosmos DB SQL API accounts is session consistency. This default setting can be modified for all future requests. At an individual request level, you can go a step further and relax the consistency level for that specific request.

In this lab, we will configure the default consistency level for an Azure Cosmos DB SQL API account and then configure a consistency level for an individual operation using the SDK.

### Task 1: Prepare your development environment

1. Start Visual Studio Code (the program icon is pinned to the Desktop).

2. Select the **Extension (1)** icon from the left pane. Enter **C# (2)** in the search bar and select the **extension (3)** that shows up and finally **Install (4)** on the extension. 

    ![](media/C-hash-extension.png)

3. Select the **file** option on the top left of the screen, from the pane options, select **Open Folder** and navigate to **C:\AllFiles**.

4. Select the folder **dp-420-cosmos-db-dev** and click on **Select Folder**.

### Task 2: Create an Azure Cosmos DB SQL API account

Azure Cosmos DB is a cloud-based NoSQL database service that supports multiple APIs. When provisioning an Azure Cosmos DB account for the first time, you will select which of the APIs you want the account to support (for example, **Mongo API** or **SQL API**). Once the Azure Cosmos DB SQL API account is done provisioning, you can retrieve the endpoint and key and use them to connect to the Azure Cosmos DB SQL API account using the Azure SDK for .NET or any other SDK of your choice.

1. In a new web browser window or tab, navigate to the Azure portal (``portal.azure.com``).

1. Sign into the portal using the Microsoft credentials associated with your subscription.

1. Within the **Azure services** category, select **Create a resource**, and then select **Azure Cosmos DB**.

    > &#128161; Alternatively; expand the **&#8801;** menu, select **All Services**, in the **Databases** category, select **Azure Cosmos DB**, and then select **Create**.

1. In the **Select API option** pane, select the **Create** option within the **Azure Cosmos DB for NoSQL** section.

1. Within the **Create Azure Cosmos DB Account** pane, observe the **Basics** tab.

    | **Setting** | **Value** |
    | ---: | :--- |
    | **Subscription** | *Your existing Azure subscription* |
    | **Resource group** | *DP-420-DeploymentID* |
    | **Account Name** | *Enter a globally unique name* |
    | **Location** | *Choose any available region* |
    | **Capacity mode** | *Provisioned throughput* |
    | **Apply Free Tier Discount** | *Do Not Apply* |

    >**Note** : DeploymentID is the a unique id associated to each environment. You can find the value inside the environment details page.

1. Click on **Next: Global Distribution**, **Enable** the Geo-Redundancy, click on **Review + Create** and after validation get Success click on **Create**.

1. Wait for the deployment task to complete before continuing with this task.

1. Go to the newly created **Azure Cosmos DB** account resource and navigate to the **Replicate data globally** pane.

1. In the **Replicate data globally** pane, add two extra read regions to the account and then **Save** your changes.

1. Wait for the replication task to complete before continuing with this task.

    > **Note:** This operation can take approximately 5-10 minutes.and navigate to the **Default consistency** pane.

1. In the resource blade, navigate to the **Default consistency** pane.

1. In the **Default consistency** pane, select the **Strong** option and then **Save** your changes.

1. Wait for the change to the default consistency level to persist before continuing with this task.

1. In the resource blade, navigate to the **Data Explorer** pane.

1. In the **Data Explorer** pane, select **New Container**.

1. In the **New Container** popup, enter the following values for each setting, and then select **OK**:

    | **Setting** | **Value** |
    | --: | :-- |
    | **Database id** | *Create new* &vert; *cosmicworks* |
    | **Share throughput across containers** | *Do not select* |
    | **Container id** | *products* |
    | **Partition key** | */categoryId* |
    | **Container throughput** | *Manual* &vert; *400* |

1. Back in the **Data Explorer** pane, expand the **cosmicworks** database node and then observe the **products** container node within the hierarchy.

1. In the **Data Explorer** pane, expand the **cosmicworks** database node, expand the **products** container node, and then select **Items**.

1. Still in the **Data Explorer** pane, select **New Item** from the command bar. In the editor, replace the placeholder JSON item with the following content:

    ```
    {
      "id": "7d9273d9-5d91-404c-bb2d-126abb6e4833",
      "categoryId": "78d204a2-7d64-4f4a-ac29-9bfc437ae959",
      "categoryName": "Components, Pedals",
      "sku": "PD-R563",
      "name": "ML Road Pedal",
      "price": 62.09
    }
    ```

1. Select **Save** from the command bar to add the JSON item:

1. In the **Items** tab, observe the new item in the **Items** pane.

1. In the resource blade, navigate to the **Keys** pane.

1. This pane contains the connection details and credentials necessary to connect to the account from the SDK. Specifically:

    1. Record the value of the **URI** field. You will use this **endpoint** value later in this exercise.

    1. Record the value of the **PRIMARY KEY** field. You will use this **key** value later in this exercise.

1. Close your web browser window or tab.

### Task 3: Connect to the Azure Cosmos DB SQL API account from the SDK

Using the credentials from the newly created account, you will connect with the SDK classes and create a new database and container instance. Then, you will use the Data Explorer to validate that the instances exist in the Azure portal.

1. In **Visual Studio Code**, in the **Explorer** pane, browse to the **21-sdk-consistency-model** folder.

1. Open the context menu for the **21-sdk-consistency-model** folder and then select **Open in Integrated Terminal** to open a new terminal instance.

    > **Note:** This command will open the terminal with the starting directory already set to the **21-sdk-consistency-model** folder.

1. Build the project using the [dotnet build][docs.microsoft.com/dotnet/core/tools/dotnet-build] command:

    ```
    dotnet build
    ```

    > **Note:** You may see a compiler warning that the **endpoint** and **key** variables are current unused. You can safely ignore this warning as you will use these variable in this task.

1. Close the integrated terminal.

1. Open the **product.cs** code file.

1. Observe the **Product** record and its corresponding properties. Specifically, this lab will use the **id**, **name**, and **categoryId** properties.

1. Back in the **Explorer** pane of **Visual Studio Code**, open the **script.cs** code file.

    > **Note:** The **[Microsoft.Azure.Cosmos][nuget.org/packages/microsoft.azure.cosmos/3.22.1]** library has already been pre-imported from NuGet.

1. Locate the **string** variable named **endpoint**. Set its value to the **endpoint** of the Azure Cosmos DB account you created earlier.
  
    ```
    string endpoint = "<cosmos-endpoint>";
    ```

    > **Note:** For example, if your endpoint is: **https&shy;://dp420.documents.azure.com:443/**, then the C# statement would be: **string endpoint = "https&shy;://dp420.documents.azure.com:443/";**.

1. Locate the **string** variable named **key**. Set its value to the **key** of the Azure Cosmos DB account you created earlier.

    ```
    string key = "<cosmos-key>";
    ```

    > **Note:** For example, if your key is: **fDR2ci9QgkdkvERTQ==**, then the C# statement would be: **string key = "fDR2ci9QgkdkvERTQ==";**.

1. **Save** the **script.cs** code file.

### Task 4: Configure consistency level for a point operation

The **ItemRequestOptions** class contains configuration properties on a per-request basis. Using this class, you will relax the consistency level from the current default of strong to eventual consistency.

1. Create a string variable named **id** with a value of **7d9273d9-5d91-404c-bb2d-126abb6e4833**:

    ```
    string id = "7d9273d9-5d91-404c-bb2d-126abb6e4833";
    ```

1. Create a string variable named **categoryId** with a value of **78d204a2-7d64-4f4a-ac29-9bfc437ae959**:

    ```
    string categoryId = "78d204a2-7d64-4f4a-ac29-9bfc437ae959";
    ```

1. Create a variable of type **PartitionKey** named **partitionKey** passing in the **categoryId** variable as a constructor parameter:

    ```
    PartitionKey partitionKey = new (categoryId);
    ```

1. Asynchronously invoke the generic **ReadItemAsync\<\>** method of the **container** variable passing in the **id** and **partitionkey** variables as method parameters, using **Product** as the generic type, and storing the result in a variable named **response** of type **ItemResponse\<Product\>**:

    ```
    ItemResponse<Product> response = await container.ReadItemAsync<Product>(id, partitionKey);
    ```

1. Invoke the static **Console.WriteLine** method to print the request charge using a formatted output string:

    ```
    Console.WriteLine($"STRONG Request Charge:\t{response.RequestCharge:0.00} RUs");
    ```

1. Once you are done, your code file should now include:

    ```
    using Microsoft.Azure.Cosmos;

    string endpoint = "<cosmos-endpoint>";
    string key = "<cosmos-key>";

    CosmosClient client = new CosmosClient(endpoint, key);
    
    Container container = client.GetContainer("cosmicworks", "products");
    
    string id = "7d9273d9-5d91-404c-bb2d-126abb6e4833";
    
    string categoryId = "78d204a2-7d64-4f4a-ac29-9bfc437ae959";
    PartitionKey partitionKey = new (categoryId);
    
    ItemResponse<Product> response = await container.ReadItemAsync<Product>(id, partitionKey);
    
    Console.WriteLine($"STRONG Request Charge:\t{response.RequestCharge:0.00} RUs");
    ```

1. **Save** the **script.cs** code file.

1. In **Visual Studio Code**, open the context menu for the **21-sdk-consistency-model** folder and then select **Open in Integrated Terminal** to open a new terminal instance.

1. Build and run the project using the **[dotnet run][docs.microsoft.com/dotnet/core/tools/dotnet-run]** command:

    ```
    dotnet run
    ```

1. Observe the output from the terminal. The request charge (in RUs) should be printed to the console.

    > **Note:** The current request charge should be **2 RUs**. This is due to the strong consistency requiring a read from at least two replicas to ensure that it has the latest write.

1. Close the integrated terminal.

1. Return to the editor tab for the **script.cs** code file.

1. Delete the following lines of code:

    ```
    ItemResponse<Product> response = await container.ReadItemAsync<Product>(id, partitionKey);
    
    Console.WriteLine($"Request Charge:\t{response.RequestCharge:0.00} RUs");
    ```

1. Create a new variable named **options** of type [ItemRequestOptions][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.itemrequestoptions] setting the [ConsistencyLevel][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.itemrequestoptions.consistencylevel] property to the [ConsistencyLevel.Eventual][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.consistencylevel] enum value:

    ```
    ItemRequestOptions options = new()
    { 
        ConsistencyLevel = ConsistencyLevel.Eventual 
    };
    ```

1. Asynchronously invoke the generic **ReadItemAsync\<\>** method of the **container** variable passing in the **id**, **partitionKey**, and **options** variables as  method parameters, using **Product** as the generic type, and storing the result in a variable named **response** of type **ItemResponse\<Product\>**:

    ```
    ItemResponse<Product> response = await container.ReadItemAsync<Product>(id, partitionKey, requestOptions: options);
    ```

1. Invoke the static **Console.WriteLine** method to print the request charge using a formatted output string:

    ```
    Console.WriteLine($"EVENTUAL Request Charge:\t{response.RequestCharge:0.00} RUs");
    ```

1. Once you are done, your code file should now include:

    ```
    using Microsoft.Azure.Cosmos;

    string endpoint = "<cosmos-endpoint>";
    string key = "<cosmos-key>";

    CosmosClient client = new CosmosClient(endpoint, key);
    
    Container container = client.GetContainer("cosmicworks", "products");
    
    string id = "7d9273d9-5d91-404c-bb2d-126abb6e4833";
    
    string categoryId = "78d204a2-7d64-4f4a-ac29-9bfc437ae959";
    PartitionKey partitionKey = new (categoryId);

    ItemRequestOptions options = new()
    { 
        ConsistencyLevel = ConsistencyLevel.Eventual 
    };
    
    ItemResponse<Product> response = await container.ReadItemAsync<Product>(id, partitionKey, requestOptions: options);
    
    Console.WriteLine($"EVENTUAL Request Charge:\t{response.RequestCharge:0.00} RUs");
    ```

1. **Save** the **script.cs** code file.

1. In **Visual Studio Code**, open the context menu for the **21-sdk-consistency-model** folder and then select **Open in Integrated Terminal** to open a new terminal instance.

1. Build and run the project using the **[dotnet run][docs.microsoft.com/dotnet/core/tools/dotnet-run]** command:

    ```
    dotnet run
    ```

1. Observe the output from the terminal. The request charge (in RUs) should be printed to the console.

    > **Note:** The current request charge should be **1 RUs**. This is due to the eventual consistency only requiring a read from a single replica.

1. Close the integrated terminal.

1. Close **Visual Studio Code**.


## Cleanup

1. Delete the Azure Cosmos DB accounts that were created by in this lab.
