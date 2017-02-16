---
title: "DocumentDB-fiók létrehozása | Microsoft Docs"
description: "NoSQL-adatbázis felépítése az Azure DocumentDB szolgáltatással. Kövesse ezeket az utasításokat egy DocumentDB-fiók létrehozásához és a rendkívül gyors, globális méretű NoSQL-adatbázis felépítésének elkezdéséhez."
keywords: "adatbázis felépítése"
services: documentdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: monicar
ms.assetid: 0e7f8488-7bb7-463e-b6fd-3ae91a02c03a
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/16/2016
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: ed44ca2076860128b175888748cdaa8794c2310d
ms.openlocfilehash: b922127076a8cb29ffb89c7fd184d4b8fa2d7e13


---
# <a name="how-to-create-a-documentdb-nosql-account-using-the-azure-portal"></a>DocumentDB NoSQL-fiók létrehozása az Azure Portal használatával
> [!div class="op_single_selector"]
> * [Azure Portal](documentdb-create-account.md)
> * [Azure parancssori felület és Azure Resource Manager](documentdb-automation-resource-manager-cli.md)
>
>

Egy adatbázisnak a Microsoft Azure DocumentDB szolgáltatással végzett felépítéséhez a következőkre van szükség:

* Azure-fiókkal kell rendelkeznie. Ha még nem rendelkezik fiókkal, beszerezhet egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free).
* Létre kell hoznia egy DocumentDB-fiókot.  

DocumentDB-fiókot az Azure Portallal, Azure Resource Manager-sablonokkal vagy Azure parancssori felülettel (CLI-vel) hozhat létre. Ez a cikk bemutatja, hogyan hozhat létre egy DocumentDB-fiókot az Azure Portal használatával. Ha az Azure Resource Manager vagy az Azure parancssori felület használatával szeretne fiókot létrehozni: [DocumentDB-adatbázisfiók létrehozásának automatizálása](documentdb-automation-resource-manager-cli.md).

Nem ismeri még a DocumentDB szolgáltatást? Nézze meg [ezt](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) a Scott Hanselman által készített négyperces videót arról, hogyan végezheti el a legáltalánosabb feladatokat az online portálon.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/).
2. Az ugrósávon kattintson az **Új**, majd az **Adatbázisok**, és végül az **Azure DocumentDB (NoSQL)** elemre.

   ![Képernyőfelvétel az Azure Portalról, a További szolgáltatások és a DocumentDB (NoSQL) menüpont kiemelve](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-1.png)  
3. Az **Új fiók** panelen adja meg a DocumentDB-fiók kívánt beállításait.

    ![Képernyőfelvétel az Új DocumentDB panelről](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-2.png)

   * Az **Azonosító** mezőben adja meg a DocumentDB-fiókot azonosító nevet.  Az **azonosító** érvényesítése után egy zöld pipa jelenik meg az **Azonosító** mezőben. Az **Azonosító** mező értéke lesz az állomásnév az URI-n belül. Az **azonosító** kizárólag kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja, és legalább 3, de legfeljebb 50 karakter lehet. Figyelje meg, hogy a rendszer hozzáfűzi a *documents.azure.com* címet a választott végpontnévhez, és így létrehozza a DocumentDB-fiók végpontját.
   * Az **NoSQL API** panelen válassza ki a programozási modellt:

     * **DocumentDB**: A DocumentDB API .NET, Java, Node.js, Python és JavaScript [SDK-kon](documentdb-sdk-dotnet.md), valamint HTTP [REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) modelleken keresztül érhető el, és a DocumentDB minden funkciójához programozott hozzáférést biztosít.
     * **MongoDB**: A DocumentDB [protokollszintű támogatást](documentdb-protocol-mongodb.md) is kínál a **MongoDB** API-khoz. Ha a MongoDB API lehetőséget választja, használhatja a meglévő MongoDB SDK-kat és [eszközöket](documentdb-mongodb-mongochef.md) a DocumentDB-vel való kommunikációhoz. Meglévő MongoDB-alkalmazásait [módosíthatja](documentdb-import-data.md) a DocumentDB használatára, akár [kódmódosítás nélkül](documentdb-connect-mongodb-account.md) is, így kihasználhatja a teljes körűen felügyelt adatbázis szolgáltatás nyújtotta előnyöket, köztük a korlátlan skálázhatóságot, a globális replikációt és egyéb hasznos képességeket.
   * Az **Előfizetés** mezőben válassza ki a DocumentDB-fiókhoz használni kívánt Azure-előfizetést. Ha a fiókja csak egyetlen előfizetéssel rendelkezik, a rendszer alapértelmezés szerint azt választja ki.
   * Az **Erőforráscsoport** területen válasszon ki vagy hozzon létre egy erőforráscsoportot a DocumentDB-fiókhoz.  Alapértelmezés szerint új erőforráscsoport jön létre. További információk: [Az Azure Portal használata az Azure-erőforrások kezeléséhez](../azure-portal/resource-group-portal.md).
   * A **Hely** beállítás segítségével megadhatja a földrajzi helyet, ahol a DocumentDB-fiókot üzemeltetni szeretné.
4. A DocumentDB-fiók beállításainak konfigurálása után kattintson a **Létrehozás** gombra. Az üzembe helyezés állapotának ellenőrzéséhez nyissa meg az értesítési központot.  

   ![Adatbázisok gyors létrehozása – képernyőfelvétel az értesítési központról, ahol nyomon követhető a DocumentDB-fiók létrehozásának folyamata](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-4.png)  

   ![Képernyőfelvétel az értesítésközpontról, amelyben az látszik, hogy a DocumentDB-fiók sikeresen létrejött, és telepítve lett egy erőforráscsoportba – online adatbázis-készítő értesítése](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-5.png)
5. A létrehozás után a DocumentDB-fiók (az alapértelmezett beállításokkal) azonnal készen áll a használatra. A DocumentDB-fiók alapértelmezett konzisztenciája a **Munkamenet**.  Az alapértelmezett konzisztenciát az erőforrásmenü **Alapértelmezett konzisztencia** elemére kattintva módosíthatja. További információ a DocumentDB által kínált konzisztenciaszintekről: [Konzisztenciaszintek a DocumentDB-ben](documentdb-consistency-levels.md).

   ![Képernyőfelvétel az Erőforráscsoport panelről – alkalmazás fejlesztésének megkezdése](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-6.png)  

   ![Képernyőfelvétel a Konzisztenciaszint panelről – munkamenet-konzisztencia](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-7.png)  

[How to: Create a DocumentDB account]: #Howto
[Next steps]: #NextSteps


## <a name="next-steps"></a>Következő lépések
Most, hogy már rendelkezik DocumentDB-fiókkal, a következő lépés a DocumentDB- gyűjtemény és -adatbázis létrehozása.

A következő módszerekkel hozhat létre új gyűjteményt és adatbázist:

* Az Azure Portal használatával, amelyet a [DocumentDB-gyűjtemény létrehozása az Azure Portallal](documentdb-create-collection.md) című szakasz ismertet.
* Az átfogó, mintaadatokat is tartalmazó oktatóanyagokkal: [.NET](documentdb-get-started.md), [.NET MVC](documentdb-dotnet-application.md), [Java](documentdb-java-application.md), [Node.js](documentdb-nodejs-application.md) vagy [Python](documentdb-python-application.md).
* A GitHubon elérhető [.NET-](documentdb-dotnet-samples.md#database-examples), [Node.js-](documentdb-nodejs-samples.md#database-examples), vagy [Python-](documentdb-python-samples.md#database-examples) mintakódokkal.
* A [.NET](documentdb-sdk-dotnet.md), [.NET Core](documentdb-sdk-dotnet-core.md), [Node.js](documentdb-sdk-node.md), [Java](documentdb-sdk-java.md), [Python](documentdb-sdk-python.md), és [REST](https://msdn.microsoft.com/library/azure/mt489072.aspx) SDK-kkal.

Az adatbázis és a gyűjtemény létrehozása után [dokumentumokat kell hozzáadnia](documentdb-view-json-document-explorer.md) a gyűjteményekhez.

Miután dokumentumokat adott a gyűjteményhez, a [DocumentDB SQL](documentdb-sql-query.md) használatával [lekérdezéseket végezhet](documentdb-sql-query.md#executing-sql-queries) a dokumentumokon. A lekérdezéseket a portálon található [Lekérdezéskezelővel](documentdb-query-collections-query-explorer.md), a [REST API-val](https://msdn.microsoft.com/library/azure/dn781481.aspx) vagy az egyik [SDK-val](documentdb-sdk-dotnet.md) végezheti el.

### <a name="learn-more"></a>További információ
Az alábbi forrásokból többet is megtudhat a DocumentDB-ről:

* [DocumentDB képzési terv](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
* [DocumentDB hierarchikus erőforrásmodell és fogalmak](documentdb-resources.md)



<!--HONumber=Jan17_HO2-->


