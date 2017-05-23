---
title: "C++ oktatóanyag az Azure Cosmos DB-hez | Microsoft Docs"
description: "Ez egy C++ oktatóanyag, amely egy C++ adatbázis és egy konzolalkalmazás létrehozását ismerteti az Azure Cosmos DB által támogatott C++ SDK használatával. Az Azure Cosmos DB egy világméretű adatbázis-szolgáltatás."
services: cosmosdb
documentationcenter: cpp
author: asthana86
manager: jhubbard
editor: 
ms.assetid: b8756b60-8d41-4231-ba4f-6cfcfe3b4bab
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: cpp
ms.topic: hero-article
ms.date: 12/25/2016
ms.author: aasthan
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 17cf6de0256983f383a417573d02fddd81ccd104
ms.contentlocale: hu-hu
ms.lasthandoff: 05/10/2017


---
# <a name="azure-cosmos-db-c-console-application-tutorial-for-the-documentdb-api"></a>Azure Cosmos DB: C++ konzolalkalmazás oktatóanyaga DocumentDB API-hoz
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Node.js MongoDB-hez](documentdb-mongodb-samples.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 
 

Üdvözöljük az Azure Cosmos DB DocumentDB API által támogatott C++ SDK-hoz készült C++ oktatóanyagban! Az oktatóanyag lépéseinek követésével egy olyan konzolalkalmazást hozhat létre, amely Azure Cosmos DB-erőforrásokat (például C++ adatbázisokat) hoz létre és kérdez le.

Az oktatóanyag a következőket ismerteti:

* Azure Cosmos DB-fiók létrehozása és csatlakoztatása
* Az alkalmazás beállítása
* C++ Azure Cosmos DB-adatbázis létrehozása
* Gyűjtemény létrehozása
* JSON-dokumentumok létrehozása
* A gyűjtemény lekérdezése
* Dokumentum cseréje
* Dokumentum törlése
* C++ Azure Cosmos DB-adatbázis törlése

Nincs elég ideje? Ne aggódjon! A teljes megoldás elérhető a [GitHubon](https://github.com/stalker314314/DocumentDBCpp). Gyors útmutatásért tekintse meg [A teljes megoldás beszerzése](#GetSolution) című szakaszt.

A C++ oktatóanyag befejezése után a lap alján található szavazógombok használatával küldhet visszajelzést. 

Ha szeretne közvetlenül kapcsolatba lépni velünk, a hozzászólásaiban tüntesse fel az e-mail-címét, vagy [lépjen kapcsolatba velünk itt](https://www.research.net/r/8BKRJ3Z). 

Most pedig lássunk neki!

## <a name="prerequisites-for-the-c-tutorial"></a>A C++ oktatóanyag előfeltételei
Győződjön meg róla, hogy rendelkezik az alábbiakkal:

* Aktív Azure-fiók. Ha még nincs fiókja, regisztrálhat az [Azure ingyenes próbaverziójára](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio](https://www.visualstudio.com/downloads/), telepített C++ nyelvi összetevőkkel.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>1. lépés: Azure Cosmos DB-fiók létrehozása
Hozzunk létre egy Azure Cosmos DB-fiókot. Ha már rendelkezik egy használni kívánt fiókkal, folytassa [A C++ alkalmazás beállítása](#SetupNode) című lépéssel.

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="SetupC++"></a>2. lépés: A C++ alkalmazás beállítása
1. Nyissa meg a Visual Studiót, majd a **File** (Fájl) menüben kattintson a **New** (Új) elemre, majd kattintson a **Project** (Projekt) elemre. 
2. A **New Project** (Új projekt) ablakban, az **Installed** (Telepítve) panelen bontsa ki a **Visual C++** elemet, kattintson a **Win32** elemre, majd kattintson a **Win32 Console Application** (Win32 konzolalkalmazás) lehetőségre. Adja a hellodocumentdb nevet a projektnek, majd kattintson az **OK** gombra. 
   
    ![A (New project) (Új projekt) varázsló képernyőképe](media/documentdb-cpp-get-started/hellodocumentdb.png)
3. A Win32 alkalmazás varázslójának elindulásakor kattintson a **Finish** (Befejezés) gombra.
4. Ha létrejött a projekt, nyissa meg a NuGet-csomagkezelőt. Ehhez kattintson a jobb gombbal a **hellodocumentdb** projektre a **Solution Explorer** (Megoldáskezelő) felületén, és kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) lehetőségre. 
   
    ![A projektmenüben a Manage NuGet Package (NuGet-csomagok kezelése) parancsot bemutató képernyőkép](media/documentdb-cpp-get-started/nuget.png)
5. A **NuGet: hellodocumentdb** lapon kattintson a **Browse** (Tallózás) gombra, majd keressen a *documentdbcpp* kifejezésre. Az eredményekben válassza a DocumentDbCPP elemet az alábbi képernyőképen látható módon. Ez a csomag a C++ REST SDK hivatkozásait telepíti, amely a DocumentDbCPP függősége.  
   
    ![A kiemelt DocumentDbCpp csomagot bemutató képernyőkép](media/documentdb-cpp-get-started/documentdbcpp.png)
   
    Miután a csomagokat a projekthez adta, készen állunk a kódírásra.   

## <a id="Config"></a>3. lépés: Kapcsolat részleteinek másolása az Azure Portalról az Azure Cosmos DB-adatbázisba
Nyissa meg az [Azure Portalt](https://portal.azure.com), és lépjen a létrehozott Azure Cosmos DB-adatbázisfiókra. A következő lépésben szükségünk lesz az URI-re és az elsődleges kulcsra az Azure Portalról, hogy kapcsolatot hozzunk létre a C++ kódrészletből. 

![Azure Cosmos DB URI és kulcsok az Azure Portalon](media/documentdb-cpp-get-started/nosql-tutorial-keys.png)

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
   
    Most, hogy rendelkezik a documentdb-ügyfél elindításához szükséges kóddal, vessünk egy pillantást az Azure Cosmos DB-erőforrások használatára.

## <a id="CreateDBColl"></a>5. lépés: C++ adatbázis és gyűjtemény létrehozása
A lépés elvégzése előtt az Azure Cosmos DB-t nem ismerő felhasználók érdekében vegyük át az adatbázis, a gyűjtemény és a dokumentumok kapcsolatát. Az [adatbázisok](documentdb-resources.md#databases) a dokumentumtároló gyűjtemények között particionált logikai tárolói. A [gyűjtemények](documentdb-resources.md#collections) JSON-dokumentumokat és a kapcsolódó JavaScript alkalmazáslogikát tartalmazó tárolók. Az Azure Cosmos DB hierarchikus erőforrásmodellről és fogalmakról további információt az [Azure Cosmos DB hierarchikus erőforrásmodell és fogalmak](documentdb-resources.md) című cikkben talál.

Egy adatbázis és egy megfelelő gyűjtemény létrehozása érdekében adja a következő kódot a fő függvény végére. Ez létrehozza a „FamilyRegistry” nevű adatbázist és a „FamilyCollection” nevű gyűjteményt az előző lépésben megadott ügyfél-konfigurációval.

    try {
      shared_ptr<Database> db = client.CreateDatabase(L"FamilyRegistry");
      shared_ptr<Collection> coll = db->CreateCollection(L"FamilyCollection");
    } catch (DocumentDBRuntimeException ex) {
      wcout << ex.message();
    }


## <a id="CreateDoc"></a>6. lépés: Dokumentum létrehozása
A [dokumentumok](documentdb-resources.md#documents) a felhasználó által megadott (tetszőleges) JSON-tartalmak. Most már beszúrhat egy dokumentumot az Azure Cosmos DB-be. Egy dokumentum létrehozásához másolja a következő kódot a fő függvény végére. 

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

Összefoglalva, ez a kód Azure Cosmos DB-adatbázist, -gyűjteményt és -dokumentumokat hoz létre, amelyeket a Dokumentumkezelőben kérhet le az Azure Portalon. 

![C++ oktatóanyag – A fiók, az adatbázis, a gyűjtemény és a dokumentumok hierarchikus kapcsolatát ábrázoló diagram](media/documentdb-cpp-get-started/documentdbdocs.png)

## <a id="QueryDB"></a>7. lépés: Az Azure Cosmos DB-erőforrások lekérdezése
Az Azure Cosmos DB támogatja az egyes gyűjteményekben tárolt JSON-dokumentumokon végzett [részletes lekérdezéseket](documentdb-sql-query.md). Az alábbi mintakód egy SQL szintaxissal készített lekérdezést mutat be, amelyet az előző lépésben létrehozott dokumentumokra vonatkozóan futtathat le.

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
Ezzel hozzáadtuk a különböző Azure Cosmos DB-erőforrások létrehozására, lekérdezésére, módosítására és törlésére szolgáló kódot.  Mindezek összegzéséhez adjunk hívásokat a különböző függvényekhez a hellodocumentdb.cpp fájlban lévő fő függvényből néhány diagnosztikai üzenettel együtt.

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

Meg kell jelennie az első lépések alkalmazás kimenetének. A kimenetnek meg kell egyeznie az alábbi képernyőképpel.

![Azure Cosmos DB C++ alkalmazás kimenete](media/documentdb-cpp-get-started/docdbconsole.png)

Gratulálunk! Ezennel befejezte a C++-oktatóanyagot, és létrehozta első saját Azure Cosmos DB-konzolalkalmazását!

## <a id="GetSolution"></a>A C++ oktatóanyagban szereplő teljes megoldás beszerzése
A cikkben szereplő összes példát tartalmazó GetStarted-megoldás lefordításához az alábbiakra lesz szüksége:

* [Azure Cosmos DB-fiók][documentdb-create-account].
* A GitHubon elérhető [GetStarted](https://github.com/stalker314314/DocumentDBCpp) megoldás.

## <a name="next-steps"></a>Következő lépések
* Ismerje meg, hogyan [figyelhet egy Azure Cosmos DB-fiókot](documentdb-monitor-accounts.md).
* Futtasson lekérdezéseket a minta-adatkészleteken a [Query Playground](https://www.documentdb.com/sql/demo) (Tesztlekérdezések) használatával.
* A programozási modellel kapcsolatos további tudnivalókat az [Azure Cosmos DB-dokumentációs oldalának](https://azure.microsoft.com/documentation/services/documentdb/) Develop (Fejlesztés) szakaszában találja.

[documentdb-create-account]: documentdb-create-account.md



