---
title: 'Azure Cosmos DB: DocumentDB API | Microsoft Docs'
description: "Megtudhatja, hogyan tárolhat és kérdezhet le alacsony késleltetésű, nagyméretű JSON-dokumentumköteteket az Azure Cosmos DB-ben SQL-lel és JavaScripttel."
keywords: "json-adatbázis, dokumentum-adatbázis"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 686cdd2b-704a-4488-921e-8eefb70d5c63
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/22/2017
ms.author: mimig
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: a8854c0f6313238ee846ad1c537d23d8fd20c66f
ms.contentlocale: hu-hu
ms.lasthandoff: 08/11/2017

---
# <a name="introduction-to-azure-cosmos-db-documentdb-api"></a>Bevezetés az Azure Cosmos DB: DocumentDB API-ba

Az [Azure Cosmos DB](introduction.md) a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása az alapvető fontosságú alkalmazásokhoz. Az Azure Cosmos DB az [iparág legjobb szolgáltatásiszint-szerződései](https://azure.microsoft.com/support/legal/sla/cosmos-db/) által biztosított [teljes körű, globális terjesztést](distribute-data-globally.md) kínál, valamint [a teljesítmény és a tárterület rugalmas méretezését](partition-data.md) világszerte, az esetek 99%-ában egyszámjegyű ezredmásodperces késéseket, [öt jól meghatározott konzisztenciaszintet](consistency-levels.md) és garantált magas rendelkezésre állást. Az Azure Cosmos DB [automatikusan indexeli az adatokat](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) anélkül, hogy a felhasználónak sémákat és indexeket kellene kezelnie. Többmodelles szolgáltatás, amely támogatja a dokumentumokat, a kulcs-értékeket, a diagramokat és az oszlopos adatmodelleket. 

![Azure DocumentDB API](./media/documentdb-introduction/cosmosdb-documentdb.png) 

A DocumentDB API-val az Azure Cosmos DB az [SQL lekérdezési képességek](documentdb-sql-query.md) gazdag és ismerős skáláját nyújtja a séma nélküli JSON-adatok egységesen rövid késleltetésével. A cikkben áttekintést kap az Azure Cosmos DB DocumentDB API-járól, és megtudhatja, hogyan tárolhat vele nagyméretű JSON-adatköteteket, kérdezheti le őket ezredmásodperces késéssel, és hogyan fejlesztheti tovább könnyedén a sémát. 

## <a name="what-capabilities-and-key-features-does-azure-cosmos-db-offer"></a>Milyen lehetőségeket és fő összetevőket kínál a Azure Cosmos DB?
Az Azure Cosmos DB a DocumentDB API-n keresztül az alábbi főbb képességeket és előnyöket kínálja:

* **Rugalmasan méretezhető átviteli sebesség és tárterület:** Könnyedén növelheti vagy csökkentheti JSON-adatbázisának méretét az alkalmazás igényeinek megfelelően. Az adatok tárolása tartós állapotú meghajtón (SSD) történik az alacsony, előre jelezhető késés érdekében. Az Azure Cosmos DB támogatja a gyűjteményeknek nevezett, JSON-adatok tárolására szolgáló tárolókat, amelyek szinte korlátlanul méretezhető tárterületet és átviteli sebességet biztosítanak. Ahogy az alkalmazás növekszik, kiszámítható teljesítmény mellett, rugalmasan és zökkenőmentesen méretezheti az Azure Cosmos DB-t. 


* **Többrégiós replikáció:** Az Azure Cosmos DB transzparensen replikálja az Ön adatait az Azure Cosmos DB-fiókjához társított összes régióba, lehetővé téve a globális adathozzáférést igénylő alkalmazások fejlesztését, és kompromisszumot kínál a konzisztencia, a rendelkezésre állás és a teljesítmény között, a megfelelő garanciákkal. Az Azure Cosmos DB transzparens regionális feladatátvételt biztosít a többkiszolgálós API-k segítségével, valamint világszerte rugalmasan méretezhető teljesítményt és tárolókapacitást nyújt. További információ: [Globális adatterjesztés az Azure Cosmos DB-vel](distribute-data-globally.md).

* **Eseti lekérdezések a megszokott SQL-szintaxissal:** Heterogén JSON-dokumentumokat tárolhat, és a már ismerős SQL-szintaxis használatával kérdezheti le ezeket. Az Azure Cosmos DB az egyidejűséget támogató, zárolásmentes, naplószerkezetű indexelési technológiát alkalmaz a dokumentumok teljes tartalmának indexeléséhez. Ez részletes, valós idejű lekérdezéseket tesz lehetővé sémamutatók, másodlagos indexek vagy nézetek megadása nélkül. További információk: [Az Azure Cosmos DB lekérdezése](documentdb-sql-query.md). 
* **JavaScript végrehajtása az adatbázison belül:** Az alkalmazáslogikát tárolt eljárások, eseményindítók és a felhasználó által megadott függvények formájában is kifejezheti a standard JavaScript használatával. Ez lehetővé teszi, hogy az alkalmazáslogika anélkül használja az adatokat, hogy az alkalmazás és az adatbázis-séma közötti eltérések miatt kellene aggódni. A DocumentDB API a JavaScript-alkalmazáslogika teljes tranzakciós végrehajtását biztosítja közvetlenül az adatbázis motorjában. A JavaScript szoros integrációja lehetővé teszi az INSERT, REPLACE, DELETE és SELECT műveletek elkülönített tranzakcióként történő végrehajtását a JavaScript-programból. További információk: [DocumentDB kiszolgálóoldali programozása](programming.md).

* **Aprólékosan beállítható konzisztenciaszintek:** Öt jól meghatározott konzisztenciaszint közül választhat a konzisztencia és a teljesítmény közötti optimális kompromisszum elérése érdekében. A lekérdezések és olvasási műveletek esetében az Azure Cosmos DB öt különböző konzisztenciaszintet kínál: erős, kötött elavulás, munkamenet, konzisztens előtag és végleges. Ezek a részletes, jól meghatározott konzisztenciaszintek lehetővé teszik, hogy ésszerű kompromisszumot alakítson ki a konzisztencia, a rendelkezésre állás és a késleltetés között. További információk: [A rendelkezésre állás és a teljesítmény maximalizálása a konzisztenciaszintek használatával](consistency-levels.md).

* **Teljes körűen felügyelt:** Nincs szükség az adatbázis és a gép erőforrásainak kezelésére. Teljes körűen felügyelt Microsoft Azure szolgáltatásként nincs szükség virtuális gépek kezelésére, szoftverek telepítésére és konfigurálására, a méretezés kezelésére vagy az összetett adatrétegek frissítésére. Minden adatbázisról automatikus biztonsági mentés készül, és védelmet élveznek a regionális meghibásodásokkal szemben. Könnyedén elvégezheti egy Azure Cosmos DB-fiók hozzáadását és a kapacitás szükség szerinti kiosztását, ami lehetővé teszi, hogy az adatbázis üzemeltetése és kezelése helyett az alkalmazásra összpontosítson. 

* **Nyílt kialakítás:** Hamar munkához láthat a meglévő ismeretei és eszközei használatával. A DocumentDB API programozása könnyű, elérhető és nem igényli új eszközök megismerését vagy egyéni kiterjesztések használatát a JSON és a JavaScript formátumhoz. Egy egyszerű RESTful HTTP-felületen keresztül érheti el az adatbázis összes funkcióját, mint a CRUD, a lekérdezés és a JavaScript-feldolgozás. A DocumentDB API támogatja a meglévő formátumokat, nyelveket és standardokat, miközben értékes adatbázis-képességeket is kínál.

* **Automatikus indexelés:** Alapértelmezés szerint az Azure Cosmos DB automatikusan indexeli az adatbázisban szereplő összes dokumentumot, nem vár vagy igényel semmilyen sémát, és nem szükséges másodlagos indexek létrehozása sem. Szeretne mindent indexelni? Ne aggódjon, [a JSON-fájlok elérési útjainak indexelését](indexing-policies.md) is ki lehet kapcsolni.

## <a name="data-management"></a>Hogyan kezelhetők az adatok a DocumentDB API-val?
A DocumentDB API jól meghatározott adatbázis-erőforrások használatával kezeli a JSON-adatokat. A magas rendelkezésre állás érdekében a rendszer replikálja ezeket az erőforrásokat, amelyek a logikai URI-juk alapján egyedi módon címezhetők. A DocumentDB API egy egyszerű, HTTP-alapú RESTful programozási modellt kínál minden erőforráshoz. 


Az Azure Cosmos DB-adatbázisfiók egy egyedi névtér, amely hozzáférést biztosít az Azure Cosmos DB-hez. Ahhoz, hogy létrehozhasson egy adatbázis-fiókot, Azure-előfizetéssel kell rendelkeznie, amely számos különböző Azure-szolgáltatáshoz biztosít hozzáférést. 

Az Azure Cosmos DB összes erőforrásának modellezése és tárolása JSON-dokumentumként történik. Az erőforrások elemekként és csatornákként vannak kezelve. Az elemek metaadatokat tartalmazó JSON-dokumentumok, a csatornák pedig elemek gyűjteményei. Az elemkészletek a megfelelő csatornákban találhatók.

Az alábbi képen láthatók az Azure Cosmos DB erőforrásainak kapcsolatai:

![Az Azure Cosmos DB erőforrásainak hierarchikus kapcsolatai][1] 

Egy adatbázis-fiók adatbázis-készletekből áll, amelyek mindegyike több gyűjteményt tartalmaz. A gyűjtemények tárolt eljárásokat, eseményindítókat, felhasználó által megadott függvényeket, dokumentumokat és kapcsolódó mellékleteket tartalmazhatnak. Egy adatbázisnak továbbá felhasználói is vannak, amelyek mindegyike rendelkezik egy engedélykészlettel a különféle egyéb gyűjtemények, tárolt eljárások, eseményindítók, felhasználó által megadott függvények, dokumentumok és mellékletek eléréséhez. Amíg az adatbázisok, felhasználók és gyűjtemények rendszer által meghatározott, jól ismert sémákkal rendelkező erőforrások, addig a dokumentumok, tárolt eljárások, eseményindítók, felhasználó által megadott függvények és mellékletek tetszőleges, felhasználó által megadott JSON-tartalmak lehetnek.  

> [!NOTE]
> Mivel a DocumentDB API előzőleg Azure DocumentDB szolgáltatás néven volt elérhető, továbbra is kiépítheti, felügyelheti és kezelheti az Azure Resource Management REST API-val létrehozott fiókokat, illetve az Azure DocumentDB-t vagy az Azure Cosmos DB erőforrás-neveket használó eszközöket. Az Azure DocumentDB API-ra utalva a nevek ugyanazt a szolgáltatást takarják. 

## <a name="develop"></a> Hogyan lehet alkalmazásokat fejleszteni a DocumentDB API használatával?

Az Azure Cosmos DB az erőforrásokat a REST API-kon keresztül teszi elérhetővé, amelyeket bármilyen, HTTP/HTTPS-kérelem indítására képes nyelv meghívhat. Ezenfelül a DocumentDB API-hoz számos népszerű nyelvhez biztosítunk programozási könyvtárakat. Az ügyfélkönyvtárak sok szempontból leegyszerűsítik az API használatát, mivel számos alfolyamatot kezelnek (például címek gyorsítótárazása, kivételek kezelése, automatikus újrapróbálkozások stb.). Jelenleg a következő nyelvekhez és platformokhoz érhetők el könyvtárak:  

| Letöltés | Dokumentáció |
| --- | --- |
| [.NET SDK](http://go.microsoft.com/fwlink/?LinkID=402989) |[.NET-kódtár](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet) |
| [Node.js SDK](http://go.microsoft.com/fwlink/?LinkID=402990) |[Node.js-kódtár](http://azure.github.io/azure-documentdb-node/) |
| [Java SDK](http://go.microsoft.com/fwlink/?LinkID=402380) |[Java-kódtár](/java/api/com.microsoft.azure.documentdb) |
| [JavaScript SDK](http://go.microsoft.com/fwlink/?LinkID=402991) |[JavaScript-kódtár](http://azure.github.io/azure-documentdb-js/) |
| n/a |[Kiszolgálóoldali JavaScript SDK](http://azure.github.io/azure-documentdb-js-server/) |
| [Python SDK](https://pypi.python.org/pypi/pydocumentdb) |[Python-kódtár](http://azure.github.io/azure-documentdb-python/) |
| n/a | [API a MongoDB-hez](mongodb-introduction.md)


Az [Azure Cosmos DB Emulator](local-emulator.md) használatával helyben fejlesztheti és tesztelheti alkalmazását a DocumentDB API-val, anélkül, hogy ehhez regisztrálnia kellene egy Azure-előfizetést, vagy fizetnie kellene a szolgáltatásért. Amikor már elégedett az alkalmazás működésével az emulátorban, átválthat az Azure Cosmos DB-fiók használatára a felhőben.

Az alapvető létrehozási, olvasási, frissítési és törlési műveletek mellett a DocumentDB API egy részletes SQL-lekérdezési felületet is biztosít JSON-dokumentumok és kiszolgálóoldali támogatás lekéréséhez a JavaScript-alkalmazáslogika tranzakciós végrehajtásához. A lekérdezés és parancsfájl végrehajtására szolgáló felületek az összes platform könyvtárán, illetve a REST API-kon keresztül is elérhetők. 

### <a name="sql-query"></a>SQL-lekérdezés
A DocumentDB API a JavaScript típusrendszerből eredő SQL-nyelv, valamint a relációs, hierarchikus és térbeli lekérdezéseket támogató kifejezések használatával támogatja a dokumentumok lekérdezését. A DocumentDB lekérdezési nyelv a JSON-dokumentumok lekérdezésének egyszerű, mégis erőteljes felülete. A nyelv támogatja az ANSI SQL-szintaxis egy alkészletét, valamint biztosítja a JavaScript-objektumok, tömbök, objektumkonstrukciók és függvényhívások szoros integrációját. A DocumentDB API a fejlesztőtől származó explicit séma vagy indexelési mutatók nélkül biztosítja a lekérdezési modelljét.

A felhasználó által megadott függvények regisztrálhatók a DocumentDB API-ban, és az SQL-lekérdezés részeként hivatkozhatók, így a szintaxis kiterjeszthető az egyéni alkalmazáslogika támogatása érdekében. Ezen felhasználó által megadott függvények JavaScript programokként vannak megírva, futtatásuk pedig az adatbázisban történik. 

A .NET-fejlesztők számára a DocumentDB API [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx) egy LINQ-lekérdezést is kínál. 

### <a name="transactions-and-javascript-execution"></a>Tranzakciók és a JavaScript futtatása
A DocumentDB API lehetővé teszi az alkalmazáslogikák teljes mértékben JavaScriptben írt, elnevezett programokként történő megírását. Ezeket a programokat a rendszer regisztrálja egy gyűjteményben, és adatbázis-műveleteket adhatnak ki az adott gyűjteményben lévő dokumentumokon. A JavaScript regisztrálható eseményindítóként, tárolt eljárásként vagy felhasználó által megadott függvényként való futtatásra. Az eseményindítók és tárolt eljárások létrehozhatnak, olvashatnak, frissíthetnek és törölhetnek dokumentumokat, míg a felhasználó által megadott függvények futtatása lekérdezés végrehajtási logikájának részeként történik, a gyűjteményhez történő írási hozzáférés nélkül.

A JavaScript Cosmos DB-ben való futtatásának modellezése a relációs adatbázis-rendszerek által támogatott alapelvek alapján történik, ahol a JavaScript a Transact-SQL modern helyettesítője. Minden JavaScript-logika futtatása egy környezeti ACID-tranzakción belül történik pillanatkép-elkülönítéssel. Ha a futtatása során a JavaScript kivételt jelez, a teljes tranzakció megszakad.

## <a name="are-there-any-online-courses-on-azure-cosmos-db"></a>Találhatók online tanfolyamok az Azure Cosmos DB-ben?

Igen, az Azure DocumentDB-ben található egy [Microsoft Virtual Academy](https://mva.microsoft.com/en-US/training-courses/azure-documentdb-planetscale-nosql-16847) tanfolyam. 

>[!VIDEO https://mva.microsoft.com/en-US/training-courses-embed/azure-documentdb-planetscale-nosql-16847]
>
>

## <a name="next-steps"></a>Következő lépések
Már van Azure-fiókja? Akkor megkezdheti az Azure Cosmos DB használatát. Ehhez kövesse a [gyors üzembe helyezési útmutatót](../cosmos-db/create-documentdb-dotnet.md), amely végigvezeti a fióklétrehozás folyamatán, és bevezeti a Cosmos DB használatába.

[1]: ./media/documentdb-introduction/json-database-resources1.png


