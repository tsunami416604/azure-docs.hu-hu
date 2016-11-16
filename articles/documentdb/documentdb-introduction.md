---
title: "A DocumentDB, egy JSON-adatbázis bemutatása | Microsoft Docs"
description: "Ismerje meg a DocumentDB-t, amely egy NoSQL-alapú JSON-adatbázis. Ez a dokumentum-adatbázis a big data, a rugalmas méretezhetőség és a magas rendelkezésre állás jegyében készült."
keywords: "json-adatbázis, dokumentum-adatbázis"
services: documentdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 686cdd2b-704a-4488-921e-8eefb70d5c63
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/13/2016
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 23a6be60d7bf8fa47589fffb5132a09994d33d4c


---
# <a name="introduction-to-documentdb-a-nosql-json-database"></a>A DocumentDB, egy NoSQL-alapú JSON-adatbázis bemutatása
## <a name="what-is-documentdb"></a>Mi a DocumentDB?
A DocumentDB egy teljes körűen felügyelt NoSQL adatbázis-szolgáltatás, amely a gyors és kiszámítható teljesítmény, a magas rendelkezésre állás, a rugalmas méretezhetőség, a globális terjesztés és a könnyű fejlesztés jegyében készült. Sémamentes NoSQL-adatbázisként a DocumentDB gazdag és ismerős SQL lekérdezési képességeket kínál a JSON-adatok egységesen rövid késleltetésével – ezáltal biztosítja az olvasási feladatok 99%-ának 10 ezredmásodpercen belüli, az írási feladatok 99%-ának pedig 15 ezredmásodpercen belüli kiszolgálását.. Ezeknek az egyedi előnyöknek köszönhetően a DocumentDB nagyszerű választás olyan web-, mobil-, játék-, IoT- és egyéb alkalmazásokhoz, amelyek zökkenőmentes méretezést és globális replikációt igényelnek.

## <a name="how-can-i-learn-about-documentdb"></a>Hogyan tudhatok meg többet a DocumentDB-ről?
Az alábbi három lépést követve gyorsan megismerheti a DocumentDB-t működés közben: 

1. Tekintse meg a kétperces [Mi a DocumentDB?](https://azure.microsoft.com/documentation/videos/what-is-azure-documentdb/) című videót, amely a DocumentDB használatának előnyeit mutatja be.
2. Tekintse meg a háromperces [DocumentDB létrehozása az Azure-on](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) című videót, amely kiemeli a DocumentDB-vel kapcsolatos első lépéseket az Azure-portál használatával.
3. Tekintse meg a [Tesztlekérdezéseket](http://www.documentdb.com/sql/demo), amelyek segítségével különböző tevékenységeken keresztül ismerheti meg a DocumentDB gazdag lekérdezési funkcióit. Ezután lépjen a Próbakörnyezet lapra, ahol futtathatja saját, egyéni SQL-lekérdezéseit, valamint kísérletezhet a DocumentDB-vel.

Ezt követően térjen vissza a cikkhez, amelyben a dolgok mélyére áshat.  

## <a name="what-capabilities-and-key-features-does-documentdb-offer"></a>Milyen lehetőségeket és fő összetevőket kínál a DocumentDB?
Az Azure DocumentDB az alábbi főbb képességeket és előnyöket kínálja:

* **Rugalmasan méretezhető átviteli sebesség és tárterület:** Könnyedén növelheti vagy csökkentheti a DocumentDB JSON-adatbázisának méretét az alkalmazás igényeinek megfelelően. Az adatok tárolása tartós állapotú meghajtón (SSD) történik az alacsony, előre jelezhető késés érdekében. A DocumentDB támogatja a gyűjteményeknek nevezett, JSON-adatok tárolására szolgáló tárolókat, amelyek szinte korlátlanul méretezhető tárterületet és átviteli sebességet biztosítanak. Ahogy az alkalmazás növekszik, kiszámítható teljesítmény mellett, rugalmasan és zökkenőmentesen méretezheti a DocumentDB-t. 
* **Többrégiós replikáció:** A DocumentDB transzparensen replikálja az Ön adatait a DocumentDB-fiókjához társított összes régióba, lehetővé téve a globális adathozzáférést igénylő alkalmazások fejlesztését, és kompromisszumot kínál a konzisztencia, a rendelkezésre állás és a teljesítmény között, a megfelelő garanciákkal. A DocumentDB transzparens regionális feladatátvételt biztosít a többkiszolgálós API-k segítségével, valamint világszerte rugalmasan méretezhető teljesítményt és tárolókapacitást nyújt. További információ: [Distribute data globally with DocumentDB](documentdb-distribute-data-globally.md) (Globális adatterjesztés a DocumentDB-vel).
* **Eseti lekérdezések a megszokott SQL-szintaxissal:** Heterogén JSON-dokumentumokat tárolhat a DocumentDB-ben, és a már ismerős SQL-szintaxis használatával kérdezheti le ezeket. A DocumentDB az egyidejűséget támogató, zárolásmentes, naplószerkezetű indexelési technológiát alkalmaz a dokumentumok teljes tartalmának indexeléséhez. Ez részletes, valós idejű lekérdezéseket tesz lehetővé sémamutatók, másodlagos indexek vagy nézetek megadása nélkül. További információk: [A DocumentDB lekérdezése](documentdb-sql-query.md). 
* **JavaScript végrehajtása az adatbázison belül:** Az alkalmazáslogikát tárolt eljárások, eseményindítók és a felhasználó által megadott függvények formájában is kifejezheti a standard JavaScript használatával. Ez lehetővé teszi, hogy az alkalmazáslogika anélkül használja az adatokat, hogy az alkalmazás és az adatbázis-séma közötti eltérések miatt kellene aggódni. A DocumentDB a JavaScript-alkalmazáslogika teljes tranzakciós végrehajtását biztosítja közvetlenül az adatbázis motorjában. A JavaScript szoros integrációja lehetővé teszi az INSERT, REPLACE, DELETE és SELECT műveletek elkülönített tranzakcióként történő végrehajtását a JavaScript-programból. További információk: [DocumentDB kiszolgálóoldali programozása](documentdb-programming.md).
* **Aprólékosan beállítható konzisztenciaszintek:** Négy jól meghatározott konzisztenciaszint közül választhat a konzisztencia és a teljesítmény közötti optimális kompromisszum elérése érdekében. A lekérdezések és olvasási műveletek esetében a DocumentDB négy különböző konzisztenciaszintet kínál: erős, kötött elavulás, munkamenet és végleges. Ezek a részletes, jól meghatározott konzisztenciaszintek lehetővé teszik, hogy ésszerű kompromisszumot alakítson ki a konzisztencia, a rendelkezésre állás és a késleltetés között. További információk: [A rendelkezésre állás és a teljesítmény maximalizálása a DocumentDB-ben konzisztenciaszintek használatával](documentdb-consistency-levels.md).
* **Teljes körűen felügyelt:** Nincs szükség az adatbázis és a gép erőforrásainak kezelésére. Teljes körűen felügyelt Microsoft Azure szolgáltatásként nincs szükség virtuális gépek kezelésére, szoftverek telepítésére és konfigurálására, a méretezés kezelésére vagy az összetett adatrétegek frissítésére. Minden adatbázisról automatikus biztonsági mentés készül, és védelmet élveznek a regionális meghibásodásokkal szemben. Könnyedén elvégezheti egy DocumentDB-fiók hozzáadását és a kapacitás szükség szerinti kiosztását, ami lehetővé teszi, hogy az adatbázis üzemeltetése és kezelése helyett az alkalmazásra összpontosítson. 
* **Nyílt kialakítás:** Hamar munkához láthat a meglévő ismeretei és eszközei használatával. A DocumentDB programozása könnyű, elérhető és nem igényli új eszközök megismerését vagy egyéni kiterjesztések használatát a JSON és a JavaScript formátumhoz. Egy egyszerű RESTful HTTP-felületen keresztül érheti el az adatbázis összes funkcióját, mint a CRUD, a lekérdezés és a JavaScript-feldolgozás. A DocumentDB támogatja a meglévő formátumokat, nyelveket és standardokat, miközben értékes adatbázis-képességeket is kínál.
* **Automatikus indexelés:** Alapértelmezés szerint a DocumentDB [automatikusan indexeli](documentdb-indexing.md) az adatbázisban szereplő összes dokumentumot, nem vár vagy igényel semmilyen sémát, és nem szükséges másodlagos indexek létrehozása sem. Szeretne mindent indexelni? Ne aggódjon, [a JSON-fájlok elérési útjainak indexelését](documentdb-indexing-policies.md) is ki lehet kapcsolni.

## <a name="a-namedatamanagementahow-does-documentdb-manage-data"></a><a name="data-management"></a>Hogyan kezeli a DocumentDB az adatokat?
Az Azure DocumentDB jól meghatározott adatbázis-erőforrások használatával kezeli a JSON-adatokat. A magas rendelkezésre állás érdekében a rendszer replikálja ezeket az erőforrásokat, amelyek a logikai URI-juk alapján egyedi módon címezhetők. A DocumentDB egy egyszerű HTTP-alapú RESTful programozási modellt kínál minden erőforráshoz. 

A DocumentDB-adatbázis fiók egy egyedi névtér, amely hozzáférést biztosít az Azure DocumentDB-hez. Ahhoz, hogy létrehozhasson egy adatbázis-fiókot, Azure-előfizetéssel kell rendelkeznie, amely számos különböző Azure-szolgáltatáshoz biztosít hozzáférést. 

A DocumentDB összes erőforrásának modellezése és tárolása JSON-dokumentumként történik. Az erőforrások elemekként és csatornákként vannak kezelve. Az elemek metaadatokat tartalmazó JSON-dokumentumok, a csatornák pedig elemek gyűjteményei. Az elemkészletek a megfelelő csatornákban találhatók.

Az alábbi képen láthatók a DocumentDB erőforrásainak kapcsolatai:

![A DocumentDB, egy NoSQL-alapú JSON-adatbázis erőforrásainak hierarchikus kapcsolatai.][1] 

Egy adatbázis-fiók adatbázis-készletekből áll, amelyek mindegyike több gyűjteményt tartalmaz. A gyűjtemények tárolt eljárásokat, eseményindítókat, felhasználó által megadott függvényeket, dokumentumokat és kapcsolódó mellékleteket tartalmazhatnak. Egy adatbázisnak továbbá felhasználói is vannak, amelyek mindegyike rendelkezik egy engedélykészlettel a különféle egyéb gyűjtemények, tárolt eljárások, eseményindítók, felhasználó által megadott függvények, dokumentumok és mellékletek eléréséhez. Amíg az adatbázisok, felhasználók és gyűjtemények rendszer által meghatározott, jól ismert sémákkal rendelkező erőforrások, addig a dokumentumok, tárolt eljárások, eseményindítók, felhasználó által megadott függvények és mellékletek tetszőleges, felhasználó által megadott JSON-tartalmak lehetnek.  

## <a name="a-namedevelopa-how-can-i-develop-apps-with-documentdb"></a><a name="develop"></a> Hogyan lehet alkalmazásokat fejleszteni a DocumentDB használatával?
Az Azure DocumentDB egy REST API-n keresztül tesz elérhetővé erőforrásokat, amelyet bármilyen, HTTP/HTTPS-kérelem indítására képes nyelv meghívhat. Ezenfelül a DocumentDB számos népszerű nyelvhez biztosít programozási könyvtárakat. Ezek a könyvtárak sok szempontból leegyszerűsítik az Azure DocumentDB használatát, mivel számos alfolyamatot kezelnek (például címek gyorsítótárazása, kivételek kezelése, automatikus újrapróbálkozások stb.). Jelenleg a következő nyelvekhez és platformokhoz érhetők el könyvtárak:  

| Letöltés | Dokumentáció |
| --- | --- |
| [.NET SDK](http://go.microsoft.com/fwlink/?LinkID=402989) |[.NET-kódtár](https://msdn.microsoft.com/library/azure/dn948556.aspx) |
| [Node.js SDK](http://go.microsoft.com/fwlink/?LinkID=402990) |[Node.js-kódtár](http://azure.github.io/azure-documentdb-node/) |
| [Java SDK](http://go.microsoft.com/fwlink/?LinkID=402380) |[Java-kódtár](http://azure.github.io/azure-documentdb-java/) |
| [JavaScript SDK](http://go.microsoft.com/fwlink/?LinkID=402991) |[JavaScript-kódtár](http://azure.github.io/azure-documentdb-js/) |
| n/a |[Kiszolgálóoldali JavaScript SDK](http://azure.github.io/azure-documentdb-js-server/) |
| [Python SDK](https://pypi.python.org/pypi/pydocumentdb) |[Python-kódtár](http://azure.github.io/azure-documentdb-python/) |

Az alapvető létrehozási, olvasási, frissítési és törlési műveletek mellett a DocumentDB egy részletes SQL-lekérdezési felületet is biztosít JSON-dokumentumok és kiszolgálóoldali támogatás lekéréséhez a JavaScript-alkalmazáslogika tranzakciós végrehajtásához. A lekérdezés és parancsfájl végrehajtására szolgáló felületek az összes platform könyvtárán, illetve a REST API-kon keresztül is elérhetők. 

### <a name="sql-query"></a>SQL-lekérdezés
Az Azure DocumentDB a JavaScript típusrendszerből eredő SQL-nyelv, valamint a relációs, hierarchikus és térbeli lekérdezéseket támogató kifejezések használatával támogatja a dokumentumok lekérdezését. A DocumentDB lekérdezési nyelv a JSON-dokumentumok lekérdezésének egyszerű, mégis erőteljes felülete. A nyelv támogatja az ANSI SQL-szintaxis egy alkészletét, valamint biztosítja a JavaScript-objektumok, tömbök, objektumkonstrukciók és függvényhívások szoros integrációját. A DocumentDB a fejlesztőtől származó explicit séma vagy indexelési mutatók nélkül biztosítja a lekérdezési modelljét.

A felhasználó által megadott függvények regisztrálhatók a DocumentDB-ben, és az SQL-lekérdezés részeként hivatkozhatók, így a szintaxis kiterjeszthető az egyéni alkalmazáslogika támogatása érdekében. Ezen felhasználó által megadott függvények JavaScript programokként vannak megírva, futtatásuk pedig az adatbázisban történik. 

A .NET-fejlesztők számára a DocumentDB egy LINQ-lekérdezést is kínál a [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx) részeként. 

### <a name="transactions-and-javascript-execution"></a>Tranzakciók és a JavaScript futtatása
A DocumentDB lehetővé teszi az alkalmazáslogikák teljes mértékben JavaScriptben írt, elnevezett programokként történő megírását. Ezeket a programokat a rendszer regisztrálja egy gyűjteményben, és adatbázis-műveleteket adhatnak ki az adott gyűjteményben lévő dokumentumokon. A JavaScript regisztrálható eseményindítóként, tárolt eljárásként vagy felhasználó által megadott függvényként való futtatásra. Az eseményindítók és tárolt eljárások létrehozhatnak, olvashatnak, frissíthetnek és törölhetnek dokumentumokat, míg a felhasználó által megadott függvények futtatása lekérdezés végrehajtási logikájának részeként történik, a gyűjteményhez történő írási hozzáférés nélkül.

A JavaScript DocumentDB-ben való futtatásának modellezése a relációs adatbázis-rendszerek által támogatott alapelvek alapján történik, ahol a JavaScript a Transact-SQL modern helyettesítője. Minden JavaScript-logika futtatása egy környezeti ACID-tranzakción belül történik pillanatkép-elkülönítéssel. Ha a futtatása során a JavaScript kivételt jelez, a teljes tranzakció megszakad.

## <a name="next-steps"></a>Következő lépések
Már van Azure-fiókja? Egy [DocumentDB-adatbázisfiók létrehozásával](documentdb-create-account.md) megkezdheti a DocumentDB használatát az [Azure Portalon](https://portal.azure.com/#gallery/Microsoft.DocumentDB).

Még nincs Azure-fiókja? A következőket teheti:

* Regisztrálhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/), amely 30 napot és egy 200 dolláros keretet biztosít az összes Azure-szolgáltatás kipróbálására. 
* Ha MSDN-előfizetéssel rendelkezik, akkor [havi 150 dollár ingyenes Azure-kreditre](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) jogosult, amit bármelyik Azure-szolgáltatáshoz felhasználhat. 

Ezután, amikor készen áll a tanulásra, tekintse meg a [képzési tervünket](https://azure.microsoft.com/documentation/learning-paths/documentdb/), és azon belül az összes elérhető képzési erőforrást. 

[1]: ./media/documentdb-introduction/json-database-resources1.png




<!--HONumber=Nov16_HO2-->


