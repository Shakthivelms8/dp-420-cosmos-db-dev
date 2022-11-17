# Module 9 - Design and implement a replication strategy for Azure Cosmos DB SQL API

## Lab 1: Connect to different regions with the Azure Cosmos DB SQL API SDK

When you enable geo-redundancy for an Azure Cosmos DB SQL API account, you can then use the SDK to read data from regions in any order you configure. This technique is beneficial when you distribute your read requests across all of your available read regions.

In this lab, you will configure the CosmosClient class to connect to read regions in a fallback order that you manually configure.

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

1. Click on **Review + Create** and after validation get Success click on **Create**.

1. Wait for the deployment task to complete before continuing with this task.

1. Go to the newly created **Azure Cosmos DB** account resource and navigate to the **Replicate data globally** pane.

1. In the **Replicate data globally** pane, add two extra read regions to the account and then **Save** your changes.

1. Wait for the replication task to complete before continuing with this task.

    > **Note:** This operation can take approximately 5-10 minutes.

1. Record the names of the **Write** (primary) region and the two **Read** regions. You will use these region names later in this exercise.

    > **Note:** For example, if your primary region is **North Europe**, and your two read secondary regions are **East US 2**, and **South Africa North**; you will record all three of those names as-is.

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

Using the credentials from the newly created account, you will connect with the SDK classes and access the database and container instance from a different region.

1. In **Visual Studio Code**, in the **Explorer** pane, browse to the **20-sdk-regions** folder.

1. Open the context menu for the **20-sdk-regions** folder and then select **Open in Integrated Terminal** to open a new terminal instance.

    > **Note:** This command will open the terminal with the starting directory already set to the **20-sdk-regions** folder.

1. Build the project using the [dotnet build][docs.microsoft.com/dotnet/core/tools/dotnet-build] command:

    ```
    dotnet build
    ```

    > **Note:** You may see a compiler warning that the **endpoint** and **key** variables are current unused. You can safely ignore this warning as you will use these variable in this task.

1. Close the integrated terminal.

1. Open the **script.cs** code file within the **20-sdk-regions** folder.

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

### Task 4: Configure the .NET SDK with a preferred region list

The **CosmosClientOptions** class includes a property to configure the list of regions you would like to connect to with the SDK. The list is ordered by failover priority, attempting to connect to each region in the order that you configure.

1. Create a new variable of generic type **List\<string\>** that contains a list of the regions you configured with your account, starting with the third region and ending with the first (primary) region. For example, if you created your Azure Cosmos DB SQL API account in the **West US** region, and then added **South Africa North**, and finally added **East Asia**; then your list variable would contain:

    ```
    List<string> regions = new()
    {
        "East Asia",
        "South Africa North",
        "West US"
    };
    ```

    > **Note:** Alternatively; you can use the [Microsoft.Azure.Cosmos.Regions][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.regions] static class which includes built-in string properties for various Azure regions.

1. Create a new instance of the **CosmosClientOptions** named **options** class with the [ApplicationPreferredRegions][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationpreferredregions] property set to the **regions** variable:

    ```
    CosmosClientOptions options = new () 
    { 
        ApplicationPreferredRegions = regions
    };
    ```

1. Create a new instance of the **CosmosClient** class named **client** passing in the **endpoint**, **key**, and **options** variables as constructor parameters:

    ```
    CosmosClient client = new (endpoint, key, options); 
    ```

1. Use the **GetContainer** method of the **client** variable to retrieve the existing container using the name of the database (*cosmicworks*) and the name of the container (*products*):

    ```
    Container container = client.GetContainer("cosmicworks", "products");
    ```

1. Use the [ReadItemAsync][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.readitemasync] method of the **container** variable to retrieve the a specific item from the server and store the result in a variable named **response** of the nullable [ItemResponse][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.itemresponse] type:

    ```
    ItemResponse<dynamic> response = await container.ReadItemAsync<dynamic>(
        "7d9273d9-5d91-404c-bb2d-126abb6e4833",
        new PartitionKey("78d204a2-7d64-4f4a-ac29-9bfc437ae959")
    );
    ```

1. Invoke the static **Console.WriteLine** method to print the current item identifier and the JSON diagnostic data:

    ```
    Console.WriteLine($"Item Id:\t{response.Resource.Id}");
    Console.WriteLine($"Response Diagnostics JSON");
    Console.WriteLine($"{response.Diagnostics}");
    ```

1. Once you are done, your code file should now include:
  
    ```
    using Microsoft.Azure.Cosmos;

    string endpoint = "<cosmos-endpoint>";
    string key = "<cosmos-key>";

    List<string> regions = new()
    {
        "<read-region-2>",
        "<read-region-1>",
        "<write-region>"
    };
    
    CosmosClientOptions options = new () 
    { 
        ApplicationPreferredRegions = regions
    };
    
    using CosmosClient client = new(endpoint, key, options);
    
    Container container = client.GetContainer("cosmicworks", "products");
    
    ItemResponse<dynamic> response = await container.ReadItemAsync<dynamic>(
        "7d9273d9-5d91-404c-bb2d-126abb6e4833",
        new PartitionKey("78d204a2-7d64-4f4a-ac29-9bfc437ae959")
    );
    
    Console.WriteLine($"Item Id:\t{response.Resource.Id}");
    Console.WriteLine("Response Diagnostics JSON");
    Console.WriteLine($"{response.Diagnostics}");
    ```

1. **Save** the **script.cs** code file.

1. In **Visual Studio Code**, open the context menu for the **20-sdk-regions** folder and then select **Open in Integrated Terminal** to open a new terminal instance.

1. Build and run the project using the **[dotnet run][docs.microsoft.com/dotnet/core/tools/dotnet-run]** command:

    ```
    dotnet run
    ```

1. Observe the output from the terminal. The name of the container and the JSON diagnostic data should print to the console output.

1. Review the JSON diagnostic data. Search for a property named **HttpResponseStats** and a child property named **RequestUri**. The value of this property should be a URI that includes the name and region you configured earlier in this lab.

    > **Note:** For example, if your account name is: **dp420** and the first region you configured is **East Asia**; then the value of the JSON property would be: **dp420-eastasia.documents.azure.com/dbs/cosmicworks/colls/products**.

1. Close the integrated terminal.

1. Close **Visual Studio Code**.

## Cleanup

1. Delete the Azure Cosmos DB accounts that were created by in this lab.

