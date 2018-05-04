---
title: Webalkalmazás készítése az Azure Cosmos DB MongoDB API-jaival | Microsoft Docs
description: Azure Cosmos DB-oktatóanyag, amely egy online adatbázis-webalkalmazást hoz létre a MongoDB-hez készült API használatával.
keywords: mongodb példák
services: cosmos-db
author: AndrewHoh
manager: kfile
editor: ''
documentationcenter: ''
ms.assetid: 61a2ab3a-2fc3-4d49-a263-ed87c66628f6
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: anhoh
ms.custom: mvc
ms.openlocfilehash: 76a8e19bacdbde938758bf41ed7f209521f513aa
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2018
---
# <a name="azure-cosmos-db-connect-to-a-mongodb-app-using-net"></a>Azure Cosmos DB: Csatlakozás MongoDB-alkalmazásokhoz a .NET használatával

Az Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum, kulcs/érték és gráf típusú adatbázisokat, amelyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket. 

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre az Azure Portalon egy Azure Cosmos DB-fiókot, majd egy adatbázist és egy gyűjteményt az adatok tárolására a [MongoDB API-val](mongodb-introduction.md). 

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Azure Cosmos DB-fiók létrehozása 
> * A kapcsolati karakterlánc frissítése
> * MongoDB-alkalmazás létrehozása virtuális gépen 


## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

Először hozzon létre egy Azure Cosmos DB-fiókot az Azure Portalon.  

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

> [!TIP]
> * Már rendelkezik Azure Cosmos DB-fiókkal? Ez esetben ugorjon [A Visual Studio-megoldás beállítása](#SetupVS) című részhez.
> * Ha az Azure Cosmos DB Emulatort használja, kövesse az [Azure Cosmos DB Emulatornál](local-emulator.md) leírt lépéseket az emulátor telepítéséhez, majd ugorjon előre [A Visual Studio-megoldás beállítása](#SetupVS) című lépésre. 
>
>

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>A kapcsolati karakterlánc frissítése

1. Az Azure Portal **Azure Cosmos DB** oldalán válassza ki a MongoDB-fiók API-ját. 
2. A fiók panel bal oldali sávján kattintson a **Gyors üzembe helyezés** elemre. 
3. Válassza ki a platformot (*.NET illesztőprogram*, *Node.js illesztőprogram*, *MongoDB rendszerhéj*, *Java illesztőprogram* vagy *Python illesztőprogram*). Ha az illesztőprogram vagy eszköz nem szerepel a listán, ne aggódjon, mivel a dokumentáció folyamatosan bővül további kapcsolati kódrészletekkel. 
4. Másolja ki és illessze be a kódrészletet a MongoDB-alkalmazásba, és már készen is van.

## <a name="set-up-your-mongodb-app"></a>A MongoDB-alkalmazás beállítása

A [Webalkalmazás létrehozása az Azure-ban, amely virtuális gépen futó MongoDB-csatlakozással rendelkezik](../app-service/app-service-web-tutorial-nodejs-mongodb-app.md) című oktatóanyag minimális módosítással használható a MongoDB-fiókok API-jaihoz kapcsolódó MongoDB-alkalmazások gyors létrehozásához (vagy helyi környezetben, vagy egy Azure-webalkalmazáson közzétéve).  

1. Kövesse az oktatóanyag lépéseit egy eltéréssel.  Cserélje le a Dal.cs kódot a következőre:

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

2. Módosítsa a Dal.cs fájl alábbi változóit az Azure Portal Kulcsok oldalán található fiókbeállításai alapján:

    ```csharp   
    private string userName = "<your user name>";
    private string host = "<your host>";
    private string password = "<your password>";
    ```

3. Használja az alkalmazást!

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az alkalmazást már nem használja, a következő lépések használatával törölje az oktatóanyag során létrehozott összes erőforrást az Azure Portalon. 

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a létrehozott erőforrás nevére. 
2. Az erőforráscsoport lapján kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következőket hajtotta végre:

> [!div class="checklist"]
> * Azure Cosmos DB-fiók létrehozása 
> * A kapcsolati karakterlánc frissítése
> * MongoDB-alkalmazás létrehozása virtuális gépen

Továbbléphet a következő oktatóanyagra, amelyben importálhatja MongoDB-adatait az Azure Cosmos DB-be.  

> [!div class="nextstepaction"]
> [MongoDB adatok importálása az Azure Cosmos DB-be](mongodb-migrate.md)

