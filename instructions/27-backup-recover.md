# Module 11: Monitor and troubleshoot an Azure Cosmos DB SQL API solution

### Estimated Timing: 30 minutes

## Lab 3: Recover a database or container from a recovery point 

Azure Automatically takes encrypted backups of your data. These backups are taken in two modes, **Periodic** and **Continuous** backup modes.

In this lab, you'll do `backup` and `restores` using the continuous backup mode. First, you'll Create an Azure Cosmos DB account. You'll then create two containers and add a few documents to them. Next, you'll then update a couple of the documents in those containers. Finally you'll create restores of the account to a point before each delete.

### Task 1: Create an Azure Cosmos DB SQL API account

Azure Cosmos DB is a cloud-based NoSQL database service that supports multiple APIs. When provisioning an Azure Cosmos DB account for the first time, you'll select which of the APIs you want the account to support (for example, **Mongo API** or **SQL API**). Once the Azure Cosmos DB SQL API account is done provisioning, you can retrieve the endpoint and key. Use the endpoint and key to connect to the Azure Cosmos DB SQL API account programatically. Use the endpoint and key on the connection strings of the Azure SDK for .NET or any other SDK.

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
    | **Global Distribution** TAB | Disable Multi-region Writes |

    >**Note** : DeploymentID is the a unique id associated to each environment. You can find the value inside the environment details page.
    
    >&#128221; Note that you can enabled **Continuous** mode during the creation of the Azure Cosmos DB account, by selecting it under the **Backup Policy** tab. In this Lab you have the choice of enabling this feature during account creation or after the account is created in the optional section below. **Enabling the feature *after* the account is created *could take longer than 5 minutes*.**
    
    > &#128221; Note that *[Multi-regions write accounts are not currently supported for continuous backups][/azure/cosmos-db/continuous-backup-restore-introduction]*.
    
    > &#128221; Your lab environments may have restrictions preventing you from creating a new resource group. If that is the case, use the existing pre-created resource group.
   
1. Click on **Next: Networking**, in Networking blade leave it default. Click on **Next: Backup Policy**.

1. In Backup Policy blade select Backup policy to **Continuous (7 days)**.

1. Click on **Review + Create** and after validation get Success click on **Create**.

### Task 2: Add a database and two containers to the account

Let's create a database and a couple of containers.

1. On the Azure portal, navigate to your Azure Cosmos DB account page.

1. In the **Data Explorer** pane, expand **New Container** and then select **New Database**.

1. In the **New Database** popup, enter the following values for each setting, and then select **OK**:

    | **Setting** | **Value** |
    | ---: | :--- |
    | **Database id** | *`Sales`* |
    | **Provision throughput** | *Do not select* |

1. In the **Data Explorer** pane, select **New Container**.

1. In the **New Container** popup, enter the following values for each setting, and then select **OK**:

    | **Setting** | **Value** |
    | ---: | :--- |
    | **Database id** | *Use existing* name: *Sales* |
    | **Container id** | *`customer`* |
    | **Partition key** | *`/id`* |
    | **Container throughput (400 - unlimited RU/s)** | *Manual* throughput: *400*|

1. In the **Data Explorer** pane, select **New Container**.

1. In the **New Container** popup, enter the following values for each setting, and then select **OK**:

    | **Setting** | **Value** |
    | ---: | :--- |
    | **Database id** | *Use existing* name: *Sales* |
    | **Container id** | *`salesOrder`* |
    | **Partition key** | *`/id`* |
    | **Container throughput (400 - unlimited RU/s)** | *Manual* throughput: *400*|

### Task 3: Add items to the containers

Let's add some documents to those containers.

1. On the Azure portal, navigate to your Azure Cosmos DB account page.

1. Under **Data Explorer**, add the following two documents to the **customer** container.

1. Under **customer** container. Select **Items**, click on **New Items**, and click on **Save**.

```
  {
    "id": "0012D555-C7DE-4C4B-B4A4-2E8A6B8E1161",
    "title": "",
    "firstName": "Franklin",
    "lastName": "Ye",
    "emailAddress": "franklin9@adventure-works.com",
    "phoneNumber": "1 (11) 500 555-0139",
    "creationDate": "2014-02-05T00:00:00",
    "addresses": [
      {
        "addressLine1": "1796 Westbury Dr.",
        "addressLine2": "",
        "city": "Melton",
        "state": "VIC",
        "country": "AU",
        "zipCode": "3337"
      }
    ],
    "password": {
      "hash": "GQF7qjEgMl3LUppoPfDDnPtHp1tXmhQBw0GboOjB8bk=",
      "salt": "12C0F5A5"
    }
  }
```

1. Select **Items**, click on **New Items**, and click on **Save**.

```
  {
    "id": "001C8C0B-9B91-47A5-A198-8770E60CFF38",
    "title": "",
    "firstName": "Victor",
    "lastName": "Moreno",
    "emailAddress": "victor8@adventure-works.com",
    "phoneNumber": "1 (11) 500 555-0134",
    "creationDate": "2011-10-09T00:00:00",
    "addresses": [
      {
        "addressLine1": "Parkstr 42",
        "addressLine2": "",
        "city": "Hamburg",
        "state": "HH ",
        "country": "DE",
        "zipCode": "20354"
      }
    ],
    "password": {
      "hash": "n8l+wY/klP/hwTC3wSr8BLMA9tm3tGTyDsCgG/Q9EYI=",
      "salt": "AC22BC8C"
    }
  }
```
1. Under **Data Explorer**, add the following three documents to the **salesOrder** container.

1. Select **Items**, click on **New Items**, and click on **Save**.

```
  {
    "id": "000C23D8-B8BC-432E-9213-6473DFDA2BC5",
    "customerId": "0012D555-C7DE-4C4B-B4A4-2E8A6B8E1161",
    "orderDate": "2014-02-16T00:00:00",
    "shipDate": "2014-02-23T00:00:00",
    "details": [
      {
        "sku": "BK-R64Y-42",
        "name": "Road-550-W Yellow, 42",
        "price": 1120.49,
        "quantity": 1
      },
      {
        "sku": "HL-U509-B",
        "name": "Sport-100 Helmet, Blue",
        "price": 34.99,
        "quantity": 1
      }
    ]
  }
  ```

1. Select **Items**, click on **New Items**, and click on **Save**.

  ```
  {
    "id": "001676F7-0B70-400B-9B7D-24BA37B97F70",
    "customerId": "001C8C0B-9B91-47A5-A198-8770E60CFF38",
    "orderDate": "2013-06-02T00:00:00",
    "shipDate": "2013-06-09T00:00:00",
    "details": [
      {
        "sku": "HL-U509-R",
        "name": "Sport-100 Helmet, Red",
        "price": 34.99,
        "quantity": 1
      },
      {
        "sku": "BK-T79Y-50",
        "name": "Touring-1000 Yellow, 50",
        "price": 2384.07,
        "quantity": 1
      }
    ]
  }
  ```

1. Select **Items**, click on **New Items**, and click on **Save**.

  ```
  {
    "id": "0019092E-BD25-48F5-8050-7051B2655BC5",
    "customerId": "0012D555-C7DE-4C4B-B4A4-2E8A6B8E1161",
    "orderDate": "2013-09-14T00:00:00",
    "shipDate": "2013-09-21T00:00:00",
    "details": [
      {
        "sku": "TI-T723",
        "name": "Touring Tire",
        "price": 28.99,
        "quantity": 1
      },
      {
        "sku": "BK-T79Y-50",
        "name": "Touring-1000 Yellow, 50",
        "price": 2384.07,
        "quantity": 1
      },
      {
        "sku": "TT-T092",
        "name": "Touring Tire Tube",
        "price": 4.99,
        "quantity": 1
      }
    ]
  }
```

### Task 4: Change the default backup mode to continuous (Optional if feature not enabled during the account creation)

*If you didn't enable the feature during the Azure Cosmos DB account creation, you'll need to do it now.*  Changing the backup mode is simple, all that is needed is to change one setting to **On**. Let's change it now.

1. On the Azure portal, navigate to your Azure Cosmos DB account page.

1. Under the **Settings**, section, select **Features**.

1. Select the **Continuous Backup** Option to turn on the feature. Selecting this option will bring up window, select the **Enable** button.  Enabling this feature could take over five minutes.

    > &#128221; Note that *[Multi-regions write accounts are not currently supported for continuous backups][/azure/cosmos-db/continuous-backup-restore-introduction]*. If you did not disable Multi-region writes when you created your Azure Cosmos DB account, you will need to do it now or enabling the continuous backup feature will fail.  You can disable multi-region writes unde the **Replicate data globally** *Settings* section.

### Task 5: Delete one of the salesOrder documents

1. Under **Data Explorer**, run the following query to get the current date and time. Copy that timestamp to notepad. This Time stamp should be in UTC.

    ```
    SELECT GetCurrentDateTime ()
    ```

1. Under **Data Explorer**, locate the **salesOrder** document with **id** `0019092E-BD25-48F5-8050-7051B2655BC5`. Delete the Document, verify the document is no longer there.

### Task 6: Restore the database to the point before you deleted the salesOrder document

1. On the Azure portal, navigate to your Azure Cosmos DB account page.

1. Under the *Settings* section, select **Point in Time Restore**. Use the following settings, then click on **Submit**.

    | **Setting** | **Value** |
    | ---: | :--- |
    | **Restore Point (UTC)** | Convert the date and time appropriately. The time will need to be in AM/PM format|
    | **Location** | *Selected an available location* |
    | **Select resources you would like to restore** | *Selected database/containers* |
    | **Restore Resource** | *salesOrder* |
    | **Resource Group** | _DP-420-DeploymentID_|
    | **Restore Target Account** | *choose a* ***new*** *Azure Cosmos DB account name* |

    > &#128221; For Azure Cosmos DB restores, you ***never*** restore on top of and *existing* account, and will always have to create a new Azure Cosmos DB account.

    > &#128221; While you could have chosen to restore the whole database or even the whole account, in a real production environment, the databases could be huge. In many scenarios it might be quicker to just restore the containers or the databases needed.

1. This restore could take 15 minutes or more, go to the next section and leave this restore running in the background.

### Task 7: Delete the customer container

1. Under **Data Explorer**, run the following query to get the current date and time. Copy that timestamp to notepad.

    ```
    SELECT GetCurrentDateTime ()
    ```

1. Delete the **customer** container.

### Task 8: Restore the database to the point before you deleted the salesOrder document

1. On the Azure portal, navigate to your Azure Cosmos DB account page.

1. Under the *Settings* section, select **Point in Time Restore**. Use the following settings:

    | **Setting** | **Value** |
    | ---: | :--- |
    | **Location** | *Selected an available location* |
    | **Restore Point (UTC)** | Convert the date and time appropriately. The time will need to be in AM/PM format|
    | **Select resources you would like to restore** | *Selected database/containers* |
    | **Restore Resource** | *`customer`* |
    | **Restore Target Account** | *choose a* ***new*** *Azure Cosmos DB account name* |

    > &#128221; For Azure Cosmos DB restores, you ***never*** restore on top of and *existing* account, and will always have to create a new Azure Cosmos DB account.

    > &#128221; While you could have chosen to restore whole database or even the whole accoount, in a real production environment, the databases could be huge. In many scenarios it might be quicker to just restore the containers or the databases needed.

1. This restore could take 15 minutes or more, go to the next section and leave this restore running in the background.

### Task 9: Review the data restored

Restores can take a long time depending on the size of the database and other factors. Once the Azure Cosmos DB account restores are finished:

1. For our first restore, make sure that the third document has been recovered.

1. For the second restore, we should have restored the customer table.

## Cleanup

1. Delete the two new Azure Cosmos DB accounts that were created by the account restores.

1. Delete the Sales database and if needed, delete the original Azure Cosmos DB account.


