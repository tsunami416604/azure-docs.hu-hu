---
title: "A webalkalmazás létrehozása a mongodb Azure Cosmos DB API használatával |} Microsoft Docs"
description: "Egy Azure Cosmos adatbázis-oktatóanyag, amely egy online adatbázis webalkalmazás mongodb API használatával."
keywords: "mongodb-példák"
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 61a2ab3a-2fc3-4d49-a263-ed87c66628f6
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: anhoh
ms.custom: mvc
ms.openlocfilehash: f7aed1c69e930bdd5e5b451fa2726a75b78515eb
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="azure-cosmos-db-connect-to-a-mongodb-app-using-net"></a>Az Azure Cosmos DB: MongoDB-alkalmazásokhoz .NET használatával csatlakozzon

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum-, kulcs/érték és gráf típusú adatbázisokat, melyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket. 

Ez az oktatóanyag azt mutatja be, az Azure portál használatával Azure Cosmos DB-fiók létrehozása, és hozzon létre egy adatbázisban és gyűjteményben kívánja tárolni az adatokat, és a [MongoDB API](mongodb-introduction.md). 

Ez az oktatóanyag ismerteti a következő feladatokat:

> [!div class="checklist"]
> * Azure Cosmos DB-fiók létrehozása 
> * A kapcsolati karakterlánc frissítése
> * A virtuális gépen a MongoDB-alkalmazás létrehozása 


## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

Először hozzon létre egy Azure Cosmos DB fiókot az Azure portálon.  

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

> [!TIP]
> * Már van Azure Cosmos DB fiókja? Ha igen, ugorjon előre [a Visual Studio megoldás beállítása](#SetupVS)
> * Ha az Azure Cosmos DB Emulator használ, adja kövesse a [Azure Cosmos DB emulátor](local-emulator.md) kell beállítania az emulátor, és ugorjon előre [a Visual Studio megoldás beállítása](#SetupVS). 
>
>

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>A kapcsolati karakterlánc frissítése

1. Az Azure portálon a a **Azure Cosmos DB** lapon, válassza ki az API a MongoDB-fiók. 
2. A fiók panel bal oldali sávon kattintson **gyors üzembe helyezési**. 
3. Válassza ki a platformot (*.NET illesztőprogram*, *Node.js illesztőprogram*, *MongoDB rendszerhéj*, *Java illesztőprogram*, *Python illesztőprogram*). Ha nem látja az illesztőprogram vagy eszköz szerepel, ne aggódjon, azt folyamatosan dokumentum további kapcsolat-kódrészleteket. 
4. Másolás és beillesztés a kódrészletet a MongoDB-alkalmazást, és készen állnak.

## <a name="set-up-your-mongodb-app"></a>A MongoDB-alkalmazás beállítása

Használhatja a [egy webalkalmazás létrehozása az Azure, amely kapcsolódik a virtuális gépen futó MongoDB](../app-service/app-service-web-tutorial-nodejs-mongodb-app.md) oktatóanyag, minimális módosítással, gyorsan a MongoDB-alkalmazás beállítása (vagy helyileg vagy egy Azure webalkalmazás számára közzétett), amely csatlakozik egy API-t a MongoDB-fiók.  

1. Az oktatóanyag a, egy módosítással.  Ez lecseréli a Dal.cs kódot:

    ```csharp   
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using MyTaskListApp.Models;
    using MongoDB.Driver;
    using MongoDB.Bson;
    using System.Configuration;
    using System.Security.Authentication;
   
    namespace MyTaskListApp
    {
        public class Dal : IDisposable
        {
            //private MongoServer mongoServer = null;
            private bool disposed = false;
   
            // To do: update the connection string with the DNS name
            // or IP address of your server. 
            //For example, "mongodb://testlinux.cloudapp.net
            private string connectionString = "mongodb://localhost:27017";
            private string userName = "<your user name>";
            private string host = "<your host>";
            private string password = "<your password>";
   
            // This sample uses a database named "Tasks" and a 
            //collection named "TasksList".  The database and collection 
            //will be automatically created if they don't already exist.
            private string dbName = "Tasks";
            private string collectionName = "TasksList";
   
            // Default constructor.        
            public Dal()
            {
            }
   
            // Gets all Task items from the MongoDB server.        
            public List<MyTask> GetAllTasks()
            {
                try
                {
                    var collection = GetTasksCollection();
                    return collection.Find(new BsonDocument()).ToList();
                }
                catch (MongoConnectionException)
                {
                    return new List<MyTask>();
                }
            }
   
            // Creates a Task and inserts it into the collection in MongoDB.
            public void CreateTask(MyTask task)
            {
                var collection = GetTasksCollectionForEdit();
                try
                {
                    collection.InsertOne(task);
                }
                catch (MongoCommandException ex)
                {
                    string msg = ex.Message;
                }
            }
   
            private IMongoCollection<MyTask> GetTasksCollection()
            {
                MongoClientSettings settings = new MongoClientSettings();
                settings.Server = new MongoServerAddress(host, 10255);
                settings.UseSsl = true;
                settings.SslSettings = new SslSettings();
                settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;
   
                MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
                MongoIdentityEvidence evidence = new PasswordEvidence(password);
   
                settings.Credentials = new List<MongoCredential>()
                {
                    new MongoCredential("SCRAM-SHA-1", identity, evidence)
                };
   
                MongoClient client = new MongoClient(settings);
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }
   
            private IMongoCollection<MyTask> GetTasksCollectionForEdit()
            {
                MongoClientSettings settings = new MongoClientSettings();
                settings.Server = new MongoServerAddress(host, 10255);
                settings.UseSsl = true;
                settings.SslSettings = new SslSettings();
                settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;
   
                MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
                MongoIdentityEvidence evidence = new PasswordEvidence(password);
   
                settings.Credentials = new List<MongoCredential>()
                {
                    new MongoCredential("SCRAM-SHA-1", identity, evidence)
                };
                MongoClient client = new MongoClient(settings);
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }
   
            # region IDisposable
   
            public void Dispose()
            {
                this.Dispose(true);
                GC.SuppressFinalize(this);
            }
   
            protected virtual void Dispose(bool disposing)
            {
                if (!this.disposed)
                {
                    if (disposing)
                    {
                    }
                }
   
                this.disposed = true;
            }
   
            # endregion
        }
    }
    ```

2. A fiók beállításait az Azure-portálon kulcsok oldaláról / Dal.cs fájlban a következő változók módosítása:

    ```csharp   
    private string userName = "<your user name>";
    private string host = "<your host>";
    private string password = "<your password>";
    ```

3. Az alkalmazás használatát!

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az alkalmazást már nem használja, a következő lépések használatával törölje az oktatóanyag során létrehozott összes erőforrást az Azure Portalon. 

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a létrehozott erőforrás nevére. 
2. Az erőforráscsoport lapján kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban ezt a következők:

> [!div class="checklist"]
> * Azure Cosmos DB-fiók létrehozása 
> * A kapcsolati karakterlánc frissítése
> * A virtuális gépen a MongoDB-alkalmazás létrehozása

Folytassa a következő oktatóanyagot, és a MongoDB-adatok importálása az Azure Cosmos-Adatbázishoz.  

> [!div class="nextstepaction"]
> [MongoDB adatok importálása az Azure Cosmos DB-be](mongodb-migrate.md)

