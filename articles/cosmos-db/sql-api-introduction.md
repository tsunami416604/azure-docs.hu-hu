---
title: 'Azure Cosmos DB: Bevezetés az SQL API használatába | Microsoft Docs'
description: Megtudhatja, hogyan tárolhat és kérdezhet le alacsony késleltetésű, nagyméretű JSON-dokumentumköteteket az Azure Cosmos DB-ben SQL-lel és JavaScripttel.
keywords: json-adatbázis, dokumentum-adatbázis
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: na
ms.topic: overview
ms.date: 05/22/2017
ms.author: rafats
ms.openlocfilehash: fdeb58d72e15f563fd70ae94804de0773dd603c7
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49387347"
---
# <a name="introduction-to-azure-cosmos-db-sql-api"></a>Alapvető ismeretek az Azure Cosmos DB: SQL API felületéről

Az [Azure Cosmos DB](introduction.md) a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása az alapvető fontosságú alkalmazásokhoz. Az Azure Cosmos DB az [iparág legjobb szolgáltatásiszint-szerződései](https://azure.microsoft.com/support/legal/sla/cosmos-db/) által biztosított [teljes körű, globális terjesztést](distribute-data-globally.md) kínál, valamint [a teljesítmény és a tárterület rugalmas méretezését](partition-data.md) világszerte, az esetek 99%-ában egyszámjegyű ezredmásodperces késéseket, [öt jól meghatározott konzisztenciaszintet](consistency-levels.md) és garantált magas rendelkezésre állást. Az Azure Cosmos DB [automatikusan indexeli az adatokat](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) anélkül, hogy a felhasználónak sémákat és indexeket kellene kezelnie. Többmodelles szolgáltatás, amely támogatja a dokumentumokat, a kulcs-értékeket, a diagramokat és az oszlopos adatmodelleket.

![Azure SQL API](./media/sql-api-introduction/cosmosdb-sql-api.png) 

Az SQL API-val az Azure Cosmos DB az [SQL-lekérdezési képességek](sql-api-sql-query.md) gazdag és ismerős skáláját nyújtja, és egységesen rövid késleltetéssel kezeli a séma nélküli JSON-adatokat. A cikkben áttekintést kap az Azure Cosmos DB SQL API-járól, és megtudhatja, hogyan tárolhat vele nagyméretű JSON-adatköteteket, kérdezheti le őket ezredmásodperces nagyságrendű késleltetéssel, és hogyan fejlesztheti tovább könnyedén a sémát. 

## <a name="what-capabilities-and-key-features-does-azure-cosmos-db-offer"></a>Milyen lehetőségeket és fő összetevőket kínál a Azure Cosmos DB?
Az Azure Cosmos DB az SQL API-n keresztül az alábbi főbb képességeket és előnyöket kínálja:

* **Rugalmasan méretezhető átviteli sebesség és tárterület:** Könnyedén növelheti vagy csökkentheti JSON-adatbázisának méretét az alkalmazás igényeinek megfelelően. Az adatok tárolása SSD-meghajtón történik az alacsony, előre jelezhető késés érdekében. Az Azure Cosmos DB támogatja a gyűjteményeknek nevezett, JSON-adatok tárolására szolgáló tárolókat, amelyek szinte korlátlanul méretezhető tárterületet és átviteli sebességet biztosítanak. Ahogy az alkalmazás növekszik, kiszámítható teljesítmény mellett, rugalmasan és zökkenőmentesen méretezheti az Azure Cosmos DB-t. 


* **Többrégiós replikáció:** Az Azure Cosmos DB transzparensen replikálja az adatait az Azure Cosmos DB-fiókjához társított összes régióba, lehetővé téve a globális adathozzáférést igénylő alkalmazások fejlesztését, és kompromisszumot kínál a konzisztencia, a rendelkezésre állás és a teljesítmény között, a megfelelő garanciákkal. Az Azure Cosmos DB transzparens regionális feladatátvételt biztosít a többkiszolgálós API-k segítségével, valamint világszerte rugalmasan méretezhető teljesítményt és tárolókapacitást nyújt. További információ: [Globális adatterjesztés az Azure Cosmos DB-vel](distribute-data-globally.md).

* **Eseti lekérdezések a megszokott SQL-szintaxissal:** Heterogén JSON-dokumentumokat tárolhat, és a már ismerős SQL-szintaxis használatával kérdezheti le ezeket. Az Azure Cosmos DB az egyidejűséget támogató, zárolásmentes, naplószerkezetű indexelési technológiát alkalmaz a dokumentumok teljes tartalmának indexeléséhez. Ez részletes, valós idejű lekérdezéseket tesz lehetővé sémamutatók, másodlagos indexek vagy nézetek megadása nélkül. További információk: [Az Azure Cosmos DB lekérdezése](sql-api-sql-query.md). 
* **JavaScript végrehajtása az adatbázison belül:** Az alkalmazáslogikát tárolt eljárások, eseményindítók és a felhasználó által megadott függvények formájában is leírhatja standard JavaScript használatával. Ez lehetővé teszi, hogy az alkalmazáslogika anélkül használja az adatokat, hogy az alkalmazás és az adatbázis-séma közötti eltérések miatt kellene aggódni. Az SQL API a JavaScript-alkalmazáslogika teljes tranzakciós végrehajtását biztosítja közvetlenül az adatbázis motorjában. A JavaScript szoros integrációja lehetővé teszi az INSERT, REPLACE, DELETE és SELECT műveletek elkülönített tranzakcióként történő végrehajtását a JavaScript-programból. További információk: [SQL kiszolgálóoldali programozása](programming.md).

* **Aprólékosan beállítható konzisztenciaszintek:** Öt jól meghatározott konzisztenciaszint közül választhat a konzisztencia és a teljesítmény közötti optimális kompromisszum elérése érdekében. A lekérdezések és olvasási műveletek esetében az Azure Cosmos DB öt különböző konzisztenciaszintet kínál: erős, kötött elavulás, munkamenet, konzisztens előtag és végleges. Ezek a részletes, jól meghatározott konzisztenciaszintek lehetővé teszik, hogy ésszerű kompromisszumot alakítson ki a konzisztencia, a rendelkezésre állás és a késleltetés között. További információk: [A rendelkezésre állás és a teljesítmény maximalizálása a konzisztenciaszintek használatával](consistency-levels.md).

* **Teljes körűen felügyelt:** Nincs szükség az adatbázis és a gép erőforrásainak kezelésére. Teljes körűen felügyelt Microsoft Azure szolgáltatásként nincs szükség virtuális gépek kezelésére, szoftverek telepítésére és konfigurálására, a méretezés kezelésére vagy az összetett adatrétegek frissítésére. Minden adatbázisról automatikus biztonsági mentés készül, és védelmet élveznek a regionális meghibásodásokkal szemben. Könnyedén elvégezheti egy Azure Cosmos DB-fiók hozzáadását és a kapacitás szükség szerinti kiosztását, ami lehetővé teszi, hogy az adatbázis üzemeltetése és kezelése helyett az alkalmazásra összpontosítson. 

* **Nyílt kialakítás:** Hamar munkához láthat a meglévő ismeretei és eszközei használatával. Az SQL API programozása könnyű, elérhető és nem igényli új eszközök megismerését vagy egyéni bővítmények használatát a JSON és a JavaScript formátumhoz. Egy egyszerű RESTful HTTP-felületen keresztül érheti el az adatbázis összes funkcióját, mint a CRUD, a lekérdezés és a JavaScript-feldolgozás. Az SQL API támogatja a meglévő formátumokat, nyelveket és standardokat, miközben értékes adatbázis-képességeket is kínál.

* **Automatikus indexelés:** Alapértelmezés szerint az Azure Cosmos DB automatikusan indexeli az adatbázisban szereplő összes dokumentumot, nem vár vagy igényel semmilyen sémát, és nem szükséges másodlagos indexek létrehozása sem. Szeretne mindent indexelni? Ne aggódjon, [a JSON-fájlok elérési útjainak indexelését](indexing-policies.md) is ki lehet kapcsolni.

* **Módosítási hírcsatorna támogatása:** A módosítási hírcsatorna a módosításuk ideje szerint sorba rendezve sorolja fel az Azure Cosmos DB-gyűjteményben található dokumentumokat. Ezzel a hírcsatornával nyomon követhetők az adatok módosításai az adatok replikálása, API-hívások aktiválása vagy a frissítések streamfeldolgozása céljából. A módosítási hírcsatorna automatikusan engedélyezve van, és egyszerűen használható: [további információk a módosítási hírcsatornáról](https://docs.microsoft.com/azure/cosmos-db/change-feed). 

## <a name="data-management"></a>Hogyan kezelhetők az adatok az SQL API-val?
Az SQL API jól meghatározott adatbázis-erőforrások használatával kezeli a JSON-adatokat. A magas rendelkezésre állás érdekében a rendszer replikálja ezeket az erőforrásokat, amelyek a logikai URI-juk alapján egyedi módon címezhetők. Az SQL API egy egyszerű, HTTP-alapú RESTful programozási modellt kínál minden erőforráshoz. 


Az Azure Cosmos DB-adatbázisfiók egy egyedi névtér, amely hozzáférést biztosít az Azure Cosmos DB-hez. Ahhoz, hogy létrehozhasson egy adatbázis-fiókot, Azure-előfizetéssel kell rendelkeznie, amely számos különböző Azure-szolgáltatáshoz biztosít hozzáférést. 

Az Azure Cosmos DB összes erőforrásának modellezése és tárolása JSON-dokumentumként történik. Az erőforrások elemekként és csatornákként vannak kezelve. Az elemek metaadatokat tartalmazó JSON-dokumentumok, a csatornák pedig elemek gyűjteményei. Az elemkészletek a megfelelő csatornákban találhatók.

Az alábbi képen láthatók az Azure Cosmos DB erőforrásainak kapcsolatai:

![Az Azure Cosmos DB erőforrásainak hierarchikus kapcsolatai][1] 

Egy adatbázis-fiók adatbázis-készletekből áll, amelyek mindegyike több gyűjteményt tartalmaz. A gyűjtemények tárolt eljárásokat, eseményindítókat, felhasználó által megadott függvényeket, dokumentumokat és kapcsolódó mellékleteket tartalmazhatnak. Egy adatbázisnak továbbá felhasználói is vannak, amelyek mindegyike rendelkezik egy engedélykészlettel a különféle egyéb gyűjtemények, tárolt eljárások, eseményindítók, felhasználó által megadott függvények, dokumentumok és mellékletek eléréséhez. Amíg az adatbázisok, felhasználók és gyűjtemények rendszer által meghatározott, jól ismert sémákkal rendelkező erőforrások, addig a dokumentumok, tárolt eljárások, eseményindítók, felhasználó által megadott függvények és mellékletek tetszőleges, felhasználó által megadott JSON-tartalmak lehetnek.  

## <a name="develop"></a> Hogyan lehet alkalmazásokat fejleszteni az SQL API használatával?

Az Azure Cosmos DB az erőforrásokat a REST API-kon keresztül teszi elérhetővé, amelyeket bármilyen, HTTP/HTTPS-kérelem indítására képes nyelv meghívhat. Ezenfelül az SQL API-hoz számos népszerű nyelvhez biztosítunk programozási kódtárakat. Az ügyfélkódtárak sok szempontból leegyszerűsítik az API használatát, mivel számos alfolyamatot kezelnek (például címek gyorsítótárazása, kivételek kezelése, automatikus újrapróbálkozások stb.). Jelenleg a következő nyelvekhez és platformokhoz érhetők el könyvtárak:  

| Letöltés | Dokumentáció |
| --- | --- |
| [.NET SDK](http://go.microsoft.com/fwlink/?LinkID=402989) |[.NET-referenciadokumentumok](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet) |
| [Java SDK](http://go.microsoft.com/fwlink/?LinkID=402380) |[Java-referenciadokumentumok](/java/api/com.microsoft.azure.documentdb) |
| [JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos) |[JavaScript-referenciadokumentumok](https://docs.microsoft.com/javascript/api/@azure/cosmos/?view=azure-node-latest) |
| [Python SDK](https://pypi.python.org/pypi/pydocumentdb) |[Python-referenciadokumentumok](https://github.com/Azure/azure-cosmos-python) |

Az [Azure Cosmos DB Emulator](local-emulator.md) használatával helyben fejlesztheti és tesztelheti alkalmazását az SQL API-val, anélkül, hogy ehhez regisztrálnia kellene egy Azure-előfizetést, vagy fizetnie kellene a szolgáltatásért. Amikor már elégedett az alkalmazás működésével az emulátorban, átválthat az Azure Cosmos DB-fiók használatára a felhőben.

Az alapvető létrehozási, olvasási, frissítési és törlési műveletek mellett az SQL API egy részletes SQL-lekérdezési felületet is biztosít JSON-dokumentumok lekéréséhez, és kiszolgálóoldali támogatást kínál a JavaScript-alkalmazáslogika tranzakciós végrehajtásához. A lekérdezés és parancsfájl végrehajtására szolgáló felületek az összes platform könyvtárán, illetve a REST API-kon keresztül is elérhetők. 

### <a name="sql-query"></a>SQL-lekérdezés
Az Azure Cosmos DB a JavaScript típusrendszerből eredő SQL-nyelv, valamint a relációs, hierarchikus és térbeli lekérdezéseket támogató kifejezések használatával támogatja a dokumentumok lekérdezését. Az Azure Cosmos DB lekérdezési nyelv a JSON-dokumentumok lekérdezésének egyszerű, mégis erőteljes felülete. A nyelv támogatja az ANSI SQL-szintaxis egy alkészletét, valamint biztosítja a JavaScript-objektumok, tömbök, objektumkonstrukciók és függvényhívások szoros integrációját. Az SQL API a fejlesztőtől származó explicit séma vagy indexelési mutatók nélkül biztosítja a lekérdezésmodellt.

A felhasználó által megadott függvények regisztrálhatók az SQL API-ban, és az SQL-lekérdezés részeként hivatkozhatók, így a szintaxis kiterjeszthető az egyéni alkalmazáslogika támogatása érdekében. Ezen felhasználó által megadott függvények JavaScript programokként vannak megírva, futtatásuk pedig az adatbázisban történik. 

A .NET-fejlesztők számára az SQL API [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx) egy LINQ-lekérdezésszolgáltatót is kínál. 

### <a name="transactions-and-javascript-execution"></a>Tranzakciók és a JavaScript futtatása
Az SQL API lehetővé teszi az alkalmazáslogikák teljes mértékben JavaScriptben írt, elnevezett programokként történő megírását. Ezeket a programokat a rendszer regisztrálja egy gyűjteményben, és adatbázis-műveleteket adhatnak ki az adott gyűjteményben lévő dokumentumokon. A JavaScript regisztrálható eseményindítóként, tárolt eljárásként vagy felhasználó által megadott függvényként való futtatásra. Az eseményindítók és tárolt eljárások létrehozhatnak, olvashatnak, frissíthetnek és törölhetnek dokumentumokat, míg a felhasználó által megadott függvények futtatása lekérdezés végrehajtási logikájának részeként történik, a gyűjteményhez történő írási hozzáférés nélkül.

A JavaScript Cosmos DB-ben való futtatásának modellezése a relációs adatbázis-rendszerek által támogatott alapelvek alapján történik, ahol a JavaScript a Transact-SQL modern helyettesítője. Minden JavaScript-logika futtatása egy környezeti ACID-tranzakción belül történik pillanatkép-elkülönítéssel. Ha a futtatása során a JavaScript kivételt jelez, a teljes tranzakció megszakad.

## <a name="next-steps"></a>További lépések
Már van Azure-fiókja? Akkor megkezdheti az Azure Cosmos DB használatát. Ehhez kövesse a [gyors üzembe helyezési útmutatót](../cosmos-db/create-sql-api-dotnet.md), amely végigvezeti a fióklétrehozás folyamatán, és bevezeti a Cosmos DB használatába.

[1]: ./media/sql-api-introduction/json-database-resources1.png

