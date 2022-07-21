## Lab 2 : Paginate cross-product query results with the Azure Cosmos DB SQL API SDK

Azure Cosmos DB queries will typically have multiple pages of results. Pagination is done automatically server-side when Azure Cosmos DB cannot return all query results in one single execution. In many applications, you will want to write code using the SDK to process your query results in batches in a performant manner.

In this lab, you'll create a feed iterator that can be used in a loop to iterate over your entire result set.

## Prepare your development environment

If you have not already cloned the lab code repository for **DP-420** to the environment where you're working on this lab, follow these steps to do so. Otherwise, open the previously cloned folder in **Visual Studio Code**.

1. Start **Visual Studio Code** (the program icon is pinned to the Desktop).

    > Note: If you are not already familiar with the Visual Studio Code interface, review the [Get Started guide for Visual Studio Code][code.visualstudio.com/docs/getstarted]

1. Open a File, From the top-left options, Click on File->Open Folder and navigate to C:\AllFiles.

1. Select the folder dp-420-cosmos-db-dev-stage and Click on Select Folder.

## Seed the Azure Cosmos DB SQL API account with data

The [cosmicworks][nuget.org/packages/cosmicworks] command-line tool deploys sample data to any Azure Cosmos DB SQL API account. The tool is open-source and available through NuGet. You will install this tool to the Azure Cloud Shell and then use it to seed your database.

1. In **Visual Studio Code**, open the **Terminal** menu and then select **New Terminal** to open a new terminal instance.

1. Install the [cosmicworks][nuget.org/packages/cosmicworks] command-line tool for global use on your machine.

    ```
    dotnet tool install --global cosmicworks
    ```

    > Note: This command may take a couple of minutes to complete. This command will output the warning message (*Tool 'cosmicworks' is already installed') if you have already installed the latest version of this tool in the past.

1. Run cosmicworks to seed your Azure Cosmos DB account with the following command-line options:

    | **Option** | **Value** |
    | ---: | :--- |
    | **--endpoint** | *The endpoint value you copied earlier in this lab* |
    | **--key** | *The key value you coped earlier in this lab* |
    | **--datasets** | *product* |

    ```
    cosmicworks --endpoint <cosmos-endpoint> --key <cosmos-key> --datasets product
    ```

    > Note: For example, if your endpoint is: **https&shy;://dp420.documents.azure.com:443/** and your key is: **fDR2ci9QgkdkvERTQ==**, then the command would be:
    > ``cosmicworks --endpoint https://dp420.documents.azure.com:443/ --key fDR2ci9QgkdkvERTQ== --datasets product``

1. Wait for the **cosmicworks** command to finish populating the account with a database, container, and items.

1. Close the integrated terminal.

## Paginate through small result sets of a SQL query using the SDK

When processing query results, you must make sure your code progresses through all pages of results and checks to see if any more pages are remaining before making subsequent requests.

1. In **Visual Studio Code**, in the **Explorer** pane, browse to the **10-paginate-results-sdk** folder.

1. Open the **product.cs** code file.

1. Observe the **Product** class and its corresponding properties. Specifically, this lab will use the **id**, **name**, and **price** properties.

1. Back in the **Explorer** pane of **Visual Studio Code**, open the **script.cs** code file.

1. Update the existing variable named **endpoint** with its value set to the **endpoint** of the Azure Cosmos DB account  you created in previous lab..
  
    ```
    string endpoint = "<cosmos-endpoint>";
    ```

    >Note: For example, if your endpoint is: **https&shy;://dp420.documents.azure.com:443/**, then the C# statement would be: **string endpoint = "https&shy;://dp420.documents.azure.com:443/";**.

1. Update the existing variable named **key** with its value set to the **key** of the Azure Cosmos DB account  you created in previous lab.

    ```
    string key = "<cosmos-key>";
    ```

    > Note: For example, if your key is: **fDR2ci9QgkdkvERTQ==**, then the C# statement would be: **string key = "fDR2ci9QgkdkvERTQ==";**.

1. Create a new variable named **sql** of type *string* with a value of **SELECT p.id, p.name, p.price FROM products p**:

    ```
    string sql = "SELECT p.id, p.name, p.price FROM products p ";
    ```

1. Create a new variable of type [QueryDefinition][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.querydefinition] passing in the **sql** variable as a parameter to the constructor:

    ```
    QueryDefinition query = new (sql);
    ```

1. Create a new variable of type [QueryRequestOptions][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.queryrequestoptions] named **options** using the default empty constructor:

    ```
    QueryRequestOptions options = new ();
    ```

1. Set the [MaxItemCount][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.queryrequestoptions.maxitemcount] property of the **options** variable to a value of **50**:

    ```
    options.MaxItemCount = 50;
    ```

1. Create a new variable named **iterator** of type [FeedIterator<>][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.feediterator-1] by invoking the generic [GetItemQueryIterator][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator] method of the [Container][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container] class passing in the **query** and **options** variables as parameters:

    ```
    FeedIterator<Product> iterator = container.GetItemQueryIterator<Product>(query, requestOptions: options);
    ```

1. Create a **while** loop that checks the [HasMoreResults][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.feediterator-1.hasmoreresults] property of the **iterator** variable:

    ```
    while (iterator.HasMoreResults)
    {
        
    }
    ```

1. Within the **while** loop, asynchronously invoke the [ReadNextAsync][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.feediterator-1.readnextasync] method of the **iterator** variable storing the result in a variable named **products** of generic type [FeedResponse][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.feedresponse-1] using the **Product** class:

    ```
    FeedResponse<Product> products = await iterator.ReadNextAsync();
    ```

1. Still within the **while** loop, create a new **foreach** loop by iterating over the **products** variable using the variable **product** to represent an instance of type **Product**:

    ```
    foreach (Product product in products)
    {

    }
    ```

1. Within the **foreach** loop, use the built-in **Console.WriteLine** static method to format and print the **id**, **name**, and **price** properties of the **product** variable:

    ```
    Console.WriteLine($"[{product.id}]\t[{product.name,40}]\t[{product.price,10}]");
    ```

1. Back within the **while** loop, use the built-in **Console.WriteLine** static method to print the message *Press any key to get more results*:

    ```
    Console.WriteLine("Press any key to get more results");
    ```

1. Still within the **while** loop, use the built-in **Console.ReadKey** static method to listen for the next keypress input:

    ```
    Console.ReadKey();
    ```

1. Once you are done, your code file should now include:
  
    ```
    using System;
    using Microsoft.Azure.Cosmos;

    string endpoint = "<cosmos-endpoint>";

    string key = "<cosmos-key>";

    CosmosClient client = new CosmosClient(endpoint, key);

    Database database = await client.CreateDatabaseIfNotExistsAsync("cosmicworks");

    Container container = await database.CreateContainerIfNotExistsAsync("products", "/categoryId");

    string sql = "SELECT p.id, p.name, p.price FROM products p ";
    QueryDefinition query = new (sql);

    QueryRequestOptions options = new ();
    options.MaxItemCount = 50;

    FeedIterator<Product> iterator = container.GetItemQueryIterator<Product>(query, requestOptions: options);

    while (iterator.HasMoreResults)
    {
        FeedResponse<Product> products = await iterator.ReadNextAsync();
        foreach (Product product in products)
        {
            Console.WriteLine($"[{product.id}]\t[{product.name,40}]\t[{product.price,10}]");
        }

        Console.WriteLine("Press any key for next page of results");
        Console.ReadKey();        
    }
    ```

1. **Save** the **script.cs** file.

1. In **Visual Studio Code**, open the context menu for the **10-paginate-results-sdk** folder and then select **Open in Integrated Terminal** to open a new terminal instance.

1. Build and run the project using the [dotnet run][docs.microsoft.com/dotnet/core/tools/dotnet-run] command:

    ```
    dotnet run
    ```

1. The script will now output the first set of 50 items that match the query. Press any key to get the next set of 50 items until the query has iterated over all matching items.

    > Note: The query will match hundreds of items in the products container.

1. Close the integrated terminal.

1. Close **Visual Studio Code**.

[code.visualstudio.com/docs/getstarted]: https://code.visualstudio.com/docs/getstarted/tips-and-tricks
[docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container]: https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container
[docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator]: https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator
[docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.feediterator-1]: https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.feediterator-1
[docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.feediterator-1.hasmoreresults]: https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.feediterator-1.hasmoreresults
[docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.feediterator-1.readnextasync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.feediterator-1.readnextasync
[docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.feedresponse-1]: https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.feedresponse-1
[docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.querydefinition]: https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.querydefinition
[docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.queryrequestoptions]: https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.queryrequestoptions
[docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.queryrequestoptions.maxitemcount]: https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.queryrequestoptions.maxitemcount
[docs.microsoft.com/dotnet/core/tools/dotnet-run]: https://docs.microsoft.com/dotnet/core/tools/dotnet-run
[nuget.org/packages/cosmicworks]: https://www.nuget.org/packages/cosmicworks/
