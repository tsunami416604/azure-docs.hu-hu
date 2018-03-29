---
title: C++ oktatóanyag az Azure Cosmos DB-hez | Microsoft Docs
description: Ez egy C++ oktatóanyag, amely egy C++ adatbázis és egy konzolalkalmazás létrehozását ismerteti az Azure Cosmos DB által támogatott C++ SDK használatával. Az Azure Cosmos DB egy világméretű adatbázis-szolgáltatás.
services: cosmos-db
documentationcenter: cpp
author: asthana86
manager: jhubbard
editor: ''
ms.assetid: b8756b60-8d41-4231-ba4f-6cfcfe3b4bab
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: cpp
ms.topic: article
ms.date: 12/25/2016
ms.author: aasthan
ms.openlocfilehash: 86bf4fbc683efde60765b14b379a2b88c77d3103
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-c-console-application-tutorial-for-the-sql-api"></a>Az Azure Cosmos DB: C++ konzol oktatóanyag az SQL API-hoz.
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Node.js MongoDB-hez](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [C++](sql-api-cpp-get-started.md)
>  
> 

Az Azure Cosmos DB SQL API által támogatott SDK for C++ – üdvözli a C++-oktatóanyag! Az oktatóanyag lépéseinek követésével egy olyan konzolalkalmazást hozhat létre, amely Azure Cosmos DB-erőforrásokat (például C++ adatbázisokat) hoz létre és kérdez le.

A gyors üzembe helyezés a következőkkel foglalkozik:

* Azure Cosmos DB-fiók létrehozása és csatlakoztatása
* Az alkalmazás beállítása
* C++ Azure Cosmos DB-adatbázis létrehozása
* Gyűjtemény létrehozása
* JSON-dokumentumok létrehozása
* A gyűjtemény lekérdezése
* Dokumentum cseréje
* Dokumentum törlése
* C++ Azure Cosmos DB-adatbázis törlése

Nincs elég ideje? Ne aggódjon! A teljes megoldás elérhető a [GitHubon](https://github.com/stalker314314/sql-apiCpp). Gyors útmutatásért tekintse meg [A teljes megoldás beszerzése](#GetSolution) című szakaszt.

Most pedig lássunk neki!

## <a name="prerequisites-for-the-c-tutorial"></a>A C++ oktatóanyag előfeltételei
Győződjön meg arról, hogy a következőket:

* Aktív Azure-fiók. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [A Visual Studio 2017](https://www.visualstudio.com/downloads/), a C++ nyelvi összetevői telepítve. Ha nincs telepítve a Visual Studio 2017, letöltheti és használhatja az **ingyenes** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)t. Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>1. lépés: Azure Cosmos DB-fiók létrehozása
Hozzunk létre egy Azure Cosmos DB-fiókot. Ha már rendelkezik egy használni kívánt fiókkal, folytassa [A C++ alkalmazás beállítása](#SetupC++) című lépéssel.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupC++"></a>2. lépés: A C++ alkalmazás beállítása
1. Nyissa meg a Visual Studiót, majd a **File** (Fájl) menüben kattintson a **New** (Új) elemre, majd kattintson a **Project** (Projekt) elemre. 
2. A **New Project** (Új projekt) ablakban, az **Installed** (Telepítve) panelen bontsa ki a **Visual C++** elemet, kattintson a **Win32** elemre, majd kattintson a **Win32 Console Application** (Win32 konzolalkalmazás) lehetőségre. Adja a hellodocumentdb nevet a projektnek, majd kattintson az **OK** gombra. 
   
    ![A (New project) (Új projekt) varázsló képernyőképe](media/sql-api-cpp-get-started/hello.png)
3. A Win32 alkalmazás varázslójának elindulásakor kattintson a **Finish** (Befejezés) gombra.
4. Ha létrejött a projekt, nyissa meg a NuGet-csomagkezelőt. Ehhez kattintson a jobb gombbal a **hellodocumentdb** projektre a **Solution Explorer** (Megoldáskezelő) felületén, és kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) lehetőségre. 
   
    ![A projektmenüben a Manage NuGet Package (NuGet-csomagok kezelése) parancsot bemutató képernyőkép](media/sql-api-cpp-get-started/nuget.png)
5. A **NuGet: hellodocumentdb** lapon kattintson a **Browse** (Tallózás) gombra, majd keressen a *documentdbcpp* kifejezésre. Az eredményeket válassza ki a DocumentDbCPP, az alábbi képernyőfelvételen látható módon:   
   
    ![A kiemelt DocumentDbCpp csomagot bemutató képernyőkép](media/sql-api-cpp-get-started/cpp.png)
   
    Ez a csomag a C++ REST SDK hivatkozásait telepíti, amely a DocumentDbCPP függősége. A csomagok érhetőek el a projekt, ha minden beállítás néhány kódot ír elindításához áll.   

## <a id="Config"></a>3. lépés: Kapcsolat részleteinek másolása az Azure Portalról az Azure Cosmos DB-adatbázisba
Nyithat [Azure-portálon](https://portal.azure.com) , és keresse meg a létrehozott Azure Cosmos DB fiókkal. A C++ kódrészletet a kapcsolatot létesíteni az URI és az elsődleges kulcs a következő lépésben az Azure portálról van szükség. 

![Azure Cosmos DB URI és kulcsok az Azure Portalon](media/sql-api-cpp-get-started/nosql-tutorial-keys.png)

## <a id="Connect"></a>4. lépés: Csatlakozás egy Azure Cosmos DB-fiókhoz
1. Adja a következő fejléceket és névtereket a forráskódhoz, az `#include "stdafx.h"` elem után.
   
        #include <cpprest/json.h>
        #include <documentdbcpp\DocumentClient.h>
        #include <documentdbcpp\exceptions.h>
        #include <documentdbcpp\TriggerOperation.h>
        #include <documentdbcpp\TriggerType.h>
        using namespace documentdb;
        using namespace std;
        using namespace web::json;
2. Ezután adja a következő kódot a fő függvényhez, és cserélje le a fiók konfigurációját és az elsődleges kulcsot, hogy egyezzenek a 3. lépés Azure Cosmos DB beállításaival. 
   
        DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
        DocumentClient client (conf);
   
    Most, hogy az ügyfél kóddal, vessen egy pillantást a Azure Cosmos DB erőforrásokat.

## <a id="CreateDBColl"></a>5. lépés: C++ adatbázis és gyűjtemény létrehozása
Mielőtt elvégezné ezt a lépést, hogyan működnek együtt a adatbázis, gyűjtemény és dokumentumok azok is, akik az Azure Cosmos Adatbázishoz új keresztül ugorjunk. Az [adatbázisok](sql-api-resources.md#databases) a dokumentumtároló gyűjtemények között particionált logikai tárolói. A [gyűjtemények](sql-api-resources.md#collections) JSON-dokumentumokat és a kapcsolódó JavaScript alkalmazáslogikát tartalmazó tárolók. Az Azure Cosmos DB hierarchikus erőforrásmodellről és fogalmakról további információt az [Azure Cosmos DB hierarchikus erőforrásmodell és fogalmak](sql-api-resources.md) című cikkben talál.

Egy adatbázis és egy megfelelő gyűjtemény létrehozása érdekében adja a következő kódot a fő függvény végére. Ez létrehozza a „FamilyRegistry” nevű adatbázist és a „FamilyCollection” nevű gyűjteményt az előző lépésben megadott ügyfél-konfigurációval.

    try {
      shared_ptr<Database> db = client.CreateDatabase(L"FamilyRegistry");
      shared_ptr<Collection> coll = db->CreateCollection(L"FamilyCollection");
    } catch (DocumentDBRuntimeException ex) {
      wcout << ex.message();
    }


## <a id="CreateDoc"></a>6. lépés: Dokumentum létrehozása
A [dokumentumok](sql-api-resources.md#documents) a felhasználó által megadott (tetszőleges) JSON-tartalmak. Most már beszúrhat egy dokumentumot az Azure Cosmos DB-be. Egy dokumentum létrehozásához másolja a következő kódot a fő függvény végére. 

    try {
      value document_family;
      document_family[L"id"] = value::string(L"AndersenFamily");
      document_family[L"FirstName"] = value::string(L"Thomas");
      document_family[L"LastName"] = value::string(L"Andersen");
      shared_ptr<Document> doc = coll->CreateDocumentAsync(document_family).get();

      document_family[L"id"] = value::string(L"WakefieldFamily");
      document_family[L"FirstName"] = value::string(L"Lucy");
      document_family[L"LastName"] = value::string(L"Wakefield");
      doc = coll->CreateDocumentAsync(document_family).get();
    } catch (ResourceAlreadyExistsException ex) {
      wcout << ex.message();
    }

Összefoglalva, ez a kód egy Azure Cosmos DB adatbázis, gyűjtemény és dokumentumok, amely lekérheti az adatkezelő Azure-portálon hoz létre. 

![C++ oktatóanyag – A fiók, az adatbázis, a gyűjtemény és a dokumentumok hierarchikus kapcsolatát ábrázoló diagram](media/sql-api-cpp-get-started/docs.png)

## <a id="QueryDB"></a>7. lépés: Az Azure Cosmos DB-erőforrások lekérdezése
Az Azure Cosmos DB támogatja az egyes gyűjteményekben tárolt JSON-dokumentumokon végzett [részletes lekérdezéseket](sql-api-sql-query.md). Az alábbi mintakód bemutatja az előző lépésben létrehozott is futtathatók a dokumentumok SQL-szintaxis használatával létrehozott lekérdezést.

A függvény az adatbázis és a gyűjtemény egyedi azonosítóját vagy erőforrás-azonosítóját veszi fel argumentumokként a dokumentum ügyfelével együtt. Adja ezt a kódot a fő függvény elé.

    void executesimplequery(const DocumentClient &client,
                            const wstring dbresourceid,
                            const wstring collresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        wstring coll_name = coll->id();
        shared_ptr<DocumentIterator> iter =
            coll->QueryDocumentsAsync(wstring(L"SELECT * FROM " + coll_name)).get();
        wcout << "\n\nQuerying collection:";
        while (iter->HasMore()) {
          shared_ptr<Document> doc = iter->Next();
          wstring doc_name = doc->id();
          wcout << "\n\t" << doc_name << "\n";
          wcout << "\t"
                << "[{\"FirstName\":"
                << doc->payload().at(U("FirstName")).as_string()
                << ",\"LastName\":" << doc->payload().at(U("LastName")).as_string()
                << "}]";
        }
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="Replace"></a>8. lépés: Dokumentum cseréje
Az Azure Cosmos DB támogatja a JSON-dokumentumok cseréjét, ahogyan az a következő kódban is látható. Adja ezt a kódot az executesimplequery függvény után.

    void replacedocument(const DocumentClient &client, const wstring dbresourceid,
                         const wstring collresourceid,
                         const wstring docresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        value newdoc;
        newdoc[L"id"] = value::string(L"WakefieldFamily");
        newdoc[L"FirstName"] = value::string(L"Lucy");
        newdoc[L"LastName"] = value::string(L"Smith Wakefield");
        coll->ReplaceDocument(docresourceid, newdoc);
      } catch (DocumentDBRuntimeException ex) {
        throw;
      }
    }

## <a id="Delete"></a>9. lépés: Dokumentum törlése
Az Azure Cosmos DB támogatja a JSON-dokumentumok törlését. Ehhez másolja és illessze be a következő kódot a replacedocument függvény után. 

    void deletedocument(const DocumentClient &client, const wstring dbresourceid,
                        const wstring collresourceid, const wstring docresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        coll->DeleteDocumentAsync(docresourceid).get();
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="DeleteDB"></a>10. lépés: Adatbázis törlése
A létrehozott adatbázis törlésével az adatbázis és az összes gyermekerőforrás (gyűjtemények, dokumentumok stb.) is törlődik.

Másolja és illessze be a következő kódrészletet (cleanup (tisztítás) függvény) a deletedocument függvény után az adatbázis, valamint minden gyermekerőforrásának törléséhez.

    void deletedb(const DocumentClient &client, const wstring dbresourceid) {
      try {
        client.DeleteDatabase(dbresourceid);
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="Run"></a>11. lépés: A teljes C++ alkalmazás futtatása!
Ezzel hozzáadta a kód létrehozása, lekérdezése, módosítása és törlése különböző Azure Cosmos DB erőforrások.  Most hozzá kell fűznie ez mentése mellett néhány diagnosztikai üzenetek hellodocumentdb.cpp fő funkciója különböző funkcióhoz hívásainak hozzáadásával.

Ehhez cserélje le az alkalmazás fő függvényét a következő kóddal. Ez felülírja a 3. lépésben a kódba másolt account_configuration_uri és primary_key elemet, ezért mentse a sort, vagy másolja le az értékeket ismét a portálból. 

    int main() {
        try {
            // Start by defining your account's configuration
            DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
            // Create your client
            DocumentClient client(conf);
            // Create a new database
            try {
                shared_ptr<Database> db = client.CreateDatabase(L"FamilyDB");
                wcout << "\nCreating database:\n" << db->id();
                // Create a collection inside database
                shared_ptr<Collection> coll = db->CreateCollection(L"FamilyColl");
                wcout << "\n\nCreating collection:\n" << coll->id();
                value document_family;
                document_family[L"id"] = value::string(L"AndersenFamily");
                document_family[L"FirstName"] = value::string(L"Thomas");
                document_family[L"LastName"] = value::string(L"Andersen");
                shared_ptr<Document> doc =
                    coll->CreateDocumentAsync(document_family).get();
                wcout << "\n\nCreating document:\n" << doc->id();
                document_family[L"id"] = value::string(L"WakefieldFamily");
                document_family[L"FirstName"] = value::string(L"Lucy");
                document_family[L"LastName"] = value::string(L"Wakefield");
                doc = coll->CreateDocumentAsync(document_family).get();
                wcout << "\n\nCreating document:\n" << doc->id();
                executesimplequery(client, db->resource_id(), coll->resource_id());
                replacedocument(client, db->resource_id(), coll->resource_id(),
                    doc->resource_id());
                wcout << "\n\nReplaced document:\n" << doc->id();
                executesimplequery(client, db->resource_id(), coll->resource_id());
                deletedocument(client, db->resource_id(), coll->resource_id(),
                    doc->resource_id());
                wcout << "\n\nDeleted document:\n" << doc->id();
                deletedb(client, db->resource_id());
                wcout << "\n\nDeleted db:\n" << db->id();
                cin.get();
            }
            catch (ResourceAlreadyExistsException ex) {
                wcout << ex.message();
            }
        }
        catch (DocumentDBRuntimeException ex) {
            wcout << ex.message();
        }
        cin.get();
    }

Most az F5 billentyűt lenyomva vagy a terminálablakban az alkalmazást megkeresve és a végrehajtható fájlt futtatva felépítheti és futtathatja a kódot a Visual Studióban. 

Meg kell jelennie az első lépések alkalmazás kimenetének. A kimenetnek meg kell egyeznie az alábbi képernyőfelvételen:

![Azure Cosmos DB C++ alkalmazás kimenete](media/sql-api-cpp-get-started/console.png)

Gratulálunk! Ezennel befejezte a C++-oktatóanyagot, és létrehozta első saját Azure Cosmos DB-konzolalkalmazását!

## <a id="GetSolution"></a>A C++ oktatóanyagban szereplő teljes megoldás beszerzése
A cikkben szereplő összes példát tartalmazó GetStarted-megoldás lefordításához az alábbiakra lesz szüksége:

* [Azure Cosmos DB-fiók][create-account].
* A GitHubon elérhető [GetStarted](https://github.com/stalker314314/DocumentDBCpp) megoldás.

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [figyelhet egy Azure Cosmos DB-fiókot](monitor-accounts.md).
* Lekérdezések futtatása egy minta-adatkészleteken a [Tesztlekérdezéseket](https://www.documentdb.com/sql/demo).
* A programozási modellel kapcsolatos további tudnivalókat az [Azure Cosmos DB-dokumentációs oldalának](https://azure.microsoft.com/documentation/services/cosmos-db/) Develop (Fejlesztés) szakaszában találja.

[create-account]: create-sql-api-dotnet.md#create-account


