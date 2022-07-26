# Module 9 - Design and implement a replication strategy for Azure Cosmos DB SQL API

## Lab 3: Connect to a multi-region write account with the Azure Cosmos DB SQL API SDK

The **CosmosClientBuilder** class is a fluent class designed to build the SDK client to connect to your container and perform operations. Using the builder, you can configure a preferred application region for write operations if your Azure Cosmos DB SQL API account is already configured for multi-region writes.

In this lab, you will configure an Azure Cosmos DB SQL API account with multiple regions and enable multi-region writes. You will then use the SDK to perform operations against a specific region.

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

1. Select **+ Create a resource**, search for *Cosmos DB*, and then create a new **Azure Cosmos DB SQL API** account resource with the following settings, leaving all remaining settings to their default values:

    | **Setting** | **Value** |
    | ---: | :--- |
    | **Subscription** | *Your existing Azure subscription* |
    | **Resource group** | *DP-420-DeploymentID* |
    | **Account Name** | *Enter a globally unique name* |
    | **Location** | *Choose any available region* |
    | **Capacity mode** | *Provisioned throughput* |
    | **Apply Free Tier Discount** | *Do Not Apply* |

    >**Note** : DeploymentID is the a unique id associated to each environment. You can find the value inside the environment details page.

1. Wait for the deployment task to complete before continuing with this task.

1. Go to the newly created **Azure Cosmos DB** account resource and navigate to the **Replicate data globally** pane.

1. In the **Replicate data globally** pane, add at least one extra region to the account.

1. Still within the **Replicate data globally** pane, enable **Multi-region writes** and then **Save** your changes.

1. Wait for the replication task to complete before continuing with this task.

    > **Note:** This operation can take approximately 5-10 minutes.

1. Record the value of at least one of the extra regions you created. You will use this region value later in this exercise.

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

1. In the resource blade, navigate to the **Keys** pane.

1. This pane contains the connection details and credentials necessary to connect to the account from the SDK. Specifically:

    1. Record the value of the **URI** field. You will use this **endpoint** value later in this exercise.

    1. Record the value of the **PRIMARY KEY** field. You will use this **key** value later in this exercise.

1. Close your web browser window or tab.

### Task 3: Connect to the Azure Cosmos DB SQL API account from the SDK

Using the credentials from the newly created account, you will connect with the SDK classes and create a new database and container instance. Then, you will use the Data Explorer to validate that the instances exist in the Azure portal.

1. In **Visual Studio Code**, in the **Explorer** pane, browse to the **22-sdk-multi-region** folder.

1. Open the context menu for the **22-sdk-multi-region** folder and then select **Open in Integrated Terminal** to open a new terminal instance.

    > **Note:** This command will open the terminal with the starting directory already set to the **22-sdk-multi-region** folder.

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

### Task 4: Configure write region for the SDK

The fluent **WithApplicationRegion** method is used to configure the preferred region for the subsequent operations using the builder class.

1. Create a new instance of the [CosmosClientBuilder][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.fluent.cosmosclientbuilder] class named **builder** passing in the **endpoint** and **key** variables as constructor parameters:

    ```
    CosmosClientBuilder builder = new (endpoint, key);
    ```

1. Create a new variable named **region** of type **string** with the name of the extra region you created earlier in the lab. For example, if you created your Azure Cosmos DB SQL API account in the **East US** region, and then added **Brazil South**; then your string variable would contain:

    ```
    string region = "Brazil South"; 
    ```

    > **Note:** Alternatively; you can use the [Microsoft.Azure.Cosmos.Regions][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.regions] static class which includes built-in string properties for various Azure regions.

1. Invoke the [WithApplicationRegion][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.fluent.cosmosclientbuilder.withapplicationregion] method with a parameter of **region** and the [Build][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.fluent.cosmosclientbuilder.build] method fluently on the **builder** variable storing the result in a variable named **client** of type **CosmosClient** that is encapsulated within a using statement:

    ```
    using CosmosClient client = builder
        .WithApplicationRegion(region)
        .Build();
    ```

1. Use the **GetContainer** method of the **client** variable to retrieve the existing container using the name of the database (*cosmicworks*) and the name of the container (*products*):

    ```
    Container container = client.GetContainer("cosmicworks", "products");
    ```

1. Create two **string** variables named **id** and **categoryId** by generating a new **Guid** value and then storing the result as a string:

    ```
    string id = $"{Guid.NewGuid()}";
    string categoryId = $"{Guid.NewGuid()}";
    ```

1. Create a new variable named **item** of type **Product** passing in the **id** variable, a string value of **Polished Bike Frame**, and the **categoryId** variable as constructor parameters:

    ```
    Product item = new (id, "Polished Bike Frame", categoryId);
    ```

1. Asynchronously invoke the **CreateItemAsync\<\>** method of the **container** variable passing in the **item** variable as a parameter and storing the result in a variable named **response**:

    ```
    var response = await container.CreateItemAsync<Product>(item);
    ```

1. Invoke the static **Console.WriteLine** method to print the response's HTTP status code and request charge (in request units):

    ```
    Console.WriteLine($"Status Code:\t{response.StatusCode}");
    Console.WriteLine($"Charge (RU):\t{response.RequestCharge:0.00}");
    ```

1. Once you are done, your code file should now include:

    ```
    using Microsoft.Azure.Cosmos;
    using Microsoft.Azure.Cosmos.Fluent;

    string endpoint = "<cosmos-endpoint>";
    string key = "<cosmos-key>";    

    CosmosClientBuilder builder = new (endpoint, key);            
    
    string region = "West Europe";
    
    using CosmosClient client = builder
        .WithApplicationRegion(region)
        .Build();
    
    Container container = client.GetContainer("cosmicworks", "products");
    
    string id = $"{Guid.NewGuid()}";
    string categoryId = $"{Guid.NewGuid()}";
    Product item = new (id, "Polished Bike Frame", categoryId);
    
    var response = await container.CreateItemAsync<Product>(item);
    
    Console.WriteLine($"Status Code:\t{response.StatusCode}");
    Console.WriteLine($"Charge (RU):\t{response.RequestCharge:0.00}");
    ```

1. **Save** the **script.cs** code file.

1. In **Visual Studio Code**, open the context menu for the **22-sdk-multi-region** folder and then select **Open in Integrated Terminal** to open a new terminal instance.

1. Build and run the project using the **[dotnet run][docs.microsoft.com/dotnet/core/tools/dotnet-run]** command:

    ```
    dotnet run
    ```

1. Observe the output from the terminal. The HTTP status code and request charge (in RUs) should be printed to the console.

1. Close the integrated terminal.

1. Close **Visual Studio Code**.

