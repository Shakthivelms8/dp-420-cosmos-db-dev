# Lab 2 :  Configure an Azure Cosmos DB SQL API container's index policy with the portal

Indexing policies can be managed from any of the Azure Cosmos DB SDKs. The .NET SDK specifically includes a set of classes that can be used to architect and push a new indexing policy to a container in Azure Cosmos DB SQL API.

In this lab, you'll create a custom indexing policy for a container using the .NET SDK

### Prepare your development environment

If you have not already cloned the lab code repository for **DP-420** to the environment where you're working on this lab, follow these steps to do so. Otherwise, open the previously cloned folder in **Visual Studio Code**.

1. Start **Visual Studio Code**.

    > &#128221; If you are not already familiar with the Visual Studio Code interface, review the [Get Started guide for Visual Studio Code][code.visualstudio.com/docs/getstarted]

1.  Start Visual Studio Code (the program icon is pinned to the Desktop).

2.  Open a file, From the top-left options, Click on **file->Open Folder** and navigate to **C:\AllFiles**.

3.  Select the folder **dp-420-cosmos-db-dev** and Click on **Select Folder**.

## Create a new indexing policy using the .NET SDK

The .NET SDK contains a suite of classes related to the parent [Microsoft.Azure.Cosmos.IndexingPolicy][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.indexingpolicy] class to build new indexing policies in code.

1. In **Visual Studio Code**, in the **Explorer** pane, browse to the **12-custom-index-policy** folder.

1. Open the **script.cs** code file.

1. Update the existing variable named **endpoint** with its value set to the **endpoint** of the Azure Cosmos DB account you created in previous lab.
  
    ```
    string endpoint = "<cosmos-endpoint>";
    ```

    > &#128221; For example, if your endpoint is: **https&shy;://dp420.documents.azure.com:443/**, then the C# statement would be: **string endpoint = "https&shy;://dp420.documents.azure.com:443/";**.

1. Update the existing variable named **key** with its value set to the **key** of the Azure Cosmos DB account you created in previous lab.

    ```
    string key = "<cosmos-key>";
    ```

    > &#128221; For example, if your key is: **fDR2ci9QgkdkvERTQ==**, then the C# statement would be: **string key = "fDR2ci9QgkdkvERTQ==";**.

1. Create a new variable of type [IndexingPolicy][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.indexingpolicy] named **policy** using the default empty constructor:

    ```
    IndexingPolicy policy = new ();
    ```

1. Set the [IndexingMode][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.indexingpolicy.indexingmode] property of the **policy** variable to a value of [IndexingMode.Consistent][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.indexingmode#fields]:

    ```
    policy.IndexingMode = IndexingMode.Consistent;
    ```

1. Add a new object of type [ExcludedPath][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.excludedpath] with its [Path][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.excludedpath.path] property set to a value of **/*** to the [ExcludedPaths][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.indexingpolicy.excludedpaths] collection property in the **policy** variable:

    ```
    policy.ExcludedPaths.Add(
        new ExcludedPath{ Path = "/*" }
    );
    ```

1. Add a new object of type [IncludedPath][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.includedpath] with its [Path][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.includedpath.path] property set to a value of **/name/?** to the [IncludedPaths][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.indexingpolicy.includedpaths] collection property in the **policy** variable:

    ```
    policy.IncludedPaths.Add(
        new IncludedPath{ Path = "/name/?" }
    );
    ```

1. Create a new variable of type [ContainerProperties][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.containerproperties] named **options** passing in the values ``products`` and ``/categoryId`` as constructor parameters:

    ```
    ContainerProperties options = new ("products", "/categoryId");
    ```

1. Assign the **policy** variable to the [IndexingPolicy][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.containerproperties.indexingpolicy] property of the **options** variable:

    ```
    options.IndexingPolicy = policy;
    ```

1. Asynchronously invoke the [CreateContainerIfNotExistsAsync][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync] method of the **database** variable passing in the **options** variable as a constructor parameter and storing the result in a variable of type [Container][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container] named **container**:

    ```
    Container container = await database.CreateContainerIfNotExistsAsync(options);
    ```

1. Use the built-in **Console.WriteLine** static method to print the [Id][docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.id] property of the Container class with a header titled **Container Created**:

    ```
    Console.WriteLine($"Container Created [{container.Id}]");
    ```

1. Once you are done, your code file should now include:
  
    ```
    using System;
    using Microsoft.Azure.Cosmos;

    string endpoint = "<cosmos-endpoint>";

    string key = "<cosmos-key>";

    CosmosClient client = new CosmosClient(endpoint, key);

    Database database = await client.CreateDatabaseIfNotExistsAsync("cosmicworks");
    
    IndexingPolicy policy = new ();
    policy.IndexingMode = IndexingMode.Consistent;
    policy.ExcludedPaths.Add(
        new ExcludedPath{ Path = "/*" }
    );
    policy.IncludedPaths.Add(
        new IncludedPath{ Path = "/name/?" }
    );

    ContainerProperties options = new ("products", "/categoryId");
    options.IndexingPolicy = policy;

    Container container = await database.CreateContainerIfNotExistsAsync(options);
    Console.WriteLine($"Container Created [{container.Id}]");
    ```

1. **Save** the **script.cs** file.

1. In **Visual Studio Code**, open the context menu for the **12-custom-index-policy** folder and then select **Open in Integrated Terminal** to open a new terminal instance.

1. Build and run the project using the [dotnet run][docs.microsoft.com/dotnet/core/tools/dotnet-run] command:

    ```
    dotnet run
    ```

1. The script will now output the name of the newly created container:

    ```
    Container Created [products]
    ```

1. Close the integrated terminal.

1. Close **Visual Studio Code**.

## Observe an indexing policy created by the .NET SDK using the Data Explorer

Just like with any other indexing policy, you can use the Data Explorer to view policies that you pushed using the .NET SDKs. You will now use the portal to review the policy you created in this lab from code.

1. In a web browser, navigate to the Azure portal (``portal.azure.com``).

1. Select **Resource groups**, then select the resource group you created or viewed earlier in this lab, and then select the **Azure Cosmos DB account** resource you created in this lab.

1. Within the **Azure Cosmos DB** account resource, navigate to the **Data Explorer** pane.

1. In the **Data Explorer**, expand the **cosmicworks** database node, then observe the new **products** container node within the **SQL API** navigation tree.

1. Within the **products** container node of the **SQL API** navigation tree, select **Scale & Settings**.

1. Observe the indexing policy within the **Indexing Policy** section:

    ```
    {
      "indexingMode": "consistent",
      "automatic": true,
      "includedPaths": [
        {
          "path": "/name/?"
        }
      ],
      "excludedPaths": [
        {
          "path": "/*"
        },
        {
          "path": "/\"_etag\"/?"
        }
      ]
    }
    ```

    > &#128221; This is the JSON representation of the indexing policy you created using the .NET SDK in this lab.

1. Close your web browser window or tab.
