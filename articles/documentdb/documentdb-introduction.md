<properties 
    pageTitle="A DocumentDB, egy JSON-adatbázis bemutatása | Microsoft Azure" 
    description="Ismerje meg a DocumentDB-t, amely egy NoSQL-alapú JSON-adatbázis. Ez a dokumentum-adatbázis a big data, a rugalmas méretezhetőség és a magas rendelkezésre állás jegyében készült." 
    keywords="json database, document database"
    services="documentdb" 
    authors="mimig1" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="03/30/2016" 
    ms.author="mimig"/>

# A DocumentDB, egy NoSQL-alapú JSON-adatbázis bemutatása

Az Azure DocumentDB egy teljes körűen felügyelt NoSql adatbázis-szolgáltatás, amely a gyors és kiszámítható teljesítmény, a magas rendelkezésre állás, az automatikus méretezhetőség és a könnyű fejlesztés jegyében készült. A rugalmas adatmodell, az egységes sebesség és rövid késleltetés, valamint a gazdag lekérdezési képességek nagyszerű megoldássá teszik a webes, játék-, IoT- és számos egyéb alkalmazás esetében, amelyek zökkenőmentes méretezést igényelnek.

Az alábbi három lépést követve gyorsan megismerheti ezt a JSON-adatbázist működés közben: 

1. Tekintse meg a kétperces [Mi a DocumentDB?](https://azure.microsoft.com/documentation/videos/what-is-azure-documentdb/) című videót, amely a DocumentDB használatának előnyeit mutatja be.
2. Tekintse meg a háromperces [DocumentDB létrehozása az Azure-on](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) című videót, amely kiemeli a DocumentDB-vel kapcsolatos első lépéseket az Azure-portál használatával. 
3. Tekintse meg a [Tesztlekérdezéseket](http://www.documentdb.com/sql/demo), amelyek segítségével különböző tevékenységeken keresztül ismerheti meg a DocumentDB gazdag lekérdezési funkcióit. Ezután lépjen a Próbakörnyezet lapra, ahol futtathatja saját, egyéni SQL-lekérdezéseit, valamint kísérletezhet a DocumentDB-vel. 

Ezt követően térjen vissza a cikkhez, amelyben a dolgok mélyére ásva az alábbi kérdésekre kaphat választ:  

-   [Mi a DocumentDB, és milyen értéket nyújt a modern alkalmazások esetében?](#what-is-azure-documentdb)
-   [Hogyan történik az adatok kezelése a DocumentDB-ben, és hogyan férhetek hozzájuk?](#data-management)
-   [Hogyan fejleszthetek alkalmazásokat a DocumentDB használatával?](#develop)
-   [Mik a DocumentDB-alkalmazás összeállításának következő lépései?](#next-steps)  

## Mi az Azure DocumentDB?  

A modern alkalmazások rendkívül nagy mennyiségű adatot állítanak elő és hasznának fel, továbbá gyorsan is reagálnak ezekre. Ezek az alkalmazások nagyon gyorsan fejlődnek, és ezáltal a mögöttes adatséma is. Erre reagálva a fejlesztők egyre gyakrabban választanak sémamentes NoSQL dokumentum-adatbázisokat, mivel ezek egyszerű, gyors és méretezhető megoldást biztosítanak az adatok tárolásához és feldolgozásához, miközben megőrzik az alkalmazás-adatmodellek és a strukturálatlan adatcsatornák gyors ismétlésének képességét. A sémamentes adatbázisok közül sok azonban nem teszi lehetővé az összetett lekérdezéseket és a tranzakciós feldolgozást, ami megnehezíti a speciális adatkezelést. Itt jön a képbe a DocumentDB. A Microsoft arra tervezte a DocumentDB-t, hogy megfeleljen a napjaink alkalmazásai által támasztott adatkezelési követelményeknek.

A DocumentDB egy valódi sémamentes NoSQL-adatbázis, amelyet modern mobil-, webes, játék- és IoT-alkalmazásokhoz terveztek. A DocumentDB egységesen biztosítja a gyors olvasást és írást, a séma rugalmasságát, valamint az adatbázis méretének igény szerinti növelését, illetve csökkentését. Nem feltételez vagy igényel semmilyen sémát a JSON-dokumentumok esetében, amelyeket indexel. Alapértelmezés szerint automatikusan indexeli az adatbázisban szereplő összes dokumentumot, és nem vár vagy igényel semmilyen sémát, illetve másodlagos indexek létrehozását. A DocumentDB összetett, eseti lekérdezéseket tesz lehetővé az SQL-nyelv használatával, támogatja a jól meghatározott konzisztenciaszinteket, továbbá a JavaScript nyelvvel integrált, többdokumentumos tranzakció-feldolgozást kínál a tárolt eljárások, eseményindítók és felhasználói függvények megszokott programozási modelljének használatával. 

JSON-adatbázisként a DocumentDB natív módon támogatja a JSON-dokumentumokat, lehetővé téve az alkalmazássémák egyszerű ismétlését, valamint a kulcsérték, dokumentum és táblázatos adatmodelleket igénylő alkalmazások támogatását. A DocumentDB támogatja a JSON és a JavaScript mindenre kiterjedő jelenlétét, kiküszöbölve ezáltal az alkalmazás által meghatározott objektumok és az adatbázis-séma közötti eltéréseket. A JavaScript szoros integrációja továbbá lehetővé teszi a fejlesztők számára az alkalmazáslogika hatékony és közvetlen végrehajtását – mindezt az adatbázis motorján belül az adatbázis-tranzakció során. 

Az Azure DocumentDB az alábbi főbb képességeket és előnyöket kínálja:

-   **Rugalmasan méretezhető átviteli sebesség és tárterület:** Könnyedén növelheti vagy csökkentheti a DocumentDB JSON-adatbázisának méretét az alkalmazás igényeinek megfelelően. Az adatok tárolása tartós állapotú meghajtón (SSD) történik az alacsony, előre jelezhető késés érdekében. A DocumentDB támogatja a gyűjteménynek nevezett JSON-adatok tárolására szolgáló tárolókat, amelyek szinte korlátlanul méretezhető tárterületet és átviteli sebességet biztosítanak. Ahogy az alkalmazás növekszik, kiszámítható teljesítmény mellett, rugalmasan és zökkenőmentesen méretezheti a DocumentDB-t. 

-   **Eseti lekérdezések a megszokott SQL-szintaxissal:** Heterogén JSON-dokumentumokat tárolhat a DocumentDB-ben, és a már ismerős SQL-szintaxis használatával kérdezheti le ezeket. A DocumentDB az egyidejűséget támogató, zárolásmentes, naplószerkezetű indexelési technológiát alkalmaz a dokumentumok teljes tartalmának indexeléséhez. Ez részletes, valós idejű lekérdezéseket tesz lehetővé sémamutatók, másodlagos indexek vagy nézetek megadása nélkül. További információk: [A DocumentDB lekérdezése](documentdb-sql-query.md). 

-   **JavaScript végrehajtása az adatbázison belül:** Az alkalmazáslogikát tárolt eljárások, eseményindítók és a felhasználó által megadott függvények formájában is kifejezheti a standard JavaScript használatával. Ez lehetővé teszi, hogy az alkalmazáslogika anélkül használja az adatokat, hogy az alkalmazás és az adatbázis-séma közötti eltérések miatt kellene aggódni. A DocumentDB a JavaScript-alkalmazáslogika teljes tranzakciós végrehajtását biztosítja közvetlenül az adatbázis motorjában. A JavaScript szoros integrációja lehetővé teszi az INSERT, REPLACE, DELETE és SELECT műveletek elkülönített tranzakcióként történő végrehajtását a JavaScript-programból. További információk: [DocumentDB kiszolgálóoldali programozása](documentdb-programming.md).

-   **Aprólékosan beállítható konzisztenciaszintek:** Négy jól meghatározott konzisztenciaszint közül választhat a konzisztencia és a teljesítmény közötti optimális kompromisszum elérése érdekében. A lekérdezések és olvasási műveletek esetében a DocumentDB négy különböző konzisztenciaszintet kínál: erős, kötött elavulás, munkamenet és végleges. Ezek a részletes, jól meghatározott konzisztenciaszintek lehetővé teszik, hogy ésszerű kompromisszumot alakítson ki a konzisztencia, a rendelkezésre állás és a késleltetés között. További információk: [A rendelkezésre állás és a teljesítmény maximalizálása a DocumentDB-ben konzisztenciaszintek használatával](documentdb-consistency-levels.md).

-   **Teljes körűen felügyelt:** Nincs szükség az adatbázis és a gép erőforrásainak kezelésére. Teljes körűen felügyelt Microsoft Azure szolgáltatásként nincs szükség virtuális gépek kezelésére, szoftverek telepítésére és konfigurálására, a méretezés kezelésére vagy az összetett adatrétegek frissítésére. Minden adatbázisról automatikus biztonsági mentés készül, és védelmet élveznek a regionális meghibásodásokkal szemben. Könnyedén elvégezheti egy DocumentDB-fiók hozzáadását és a kapacitás szükség szerinti kiosztását, ami lehetővé teszi, hogy az adatbázis üzemeltetése és kezelése helyett az alkalmazásra összpontosítson. 

-   **Nyílt kialakítás:** Hamar munkához láthat a meglévő ismeretei és eszközei használatával. A DocumentDB programozása könnyű, elérhető és nem igényli új eszközök megismerését vagy egyéni kiterjesztések használatát a JSON és a JavaScript formátumhoz. Egy egyszerű RESTful HTTP-felületen keresztül érheti el az adatbázis összes funkcióját, mint a CRUD, a lekérdezés és a JavaScript-feldolgozás. A DocumentDB támogatja a meglévő formátumokat, nyelveket és standardokat, miközben értékes adatbázis-képességeket is kínál.

A DocumentDB használatával rugalmas adatkészleteket tárolhat, amelyek lekérdezési és tranzakciós feldolgozást igényelnek. Az alkalmazás-forgatókönyvek közé tartozhatnak az interaktív webes, mobil- és játékalkalmazások felhasználói adatai, továbbá az IoT-eszközök által létrehozott JSON-adatok tárolása, lekérése és feldolgozása. Egy adatbázis bármennyi JSON-dokumentumot képes tárolni, mivel a DocumentDB kiválóan alkalmazható az interneten hatalmas méretekben futtatható alkalmazások esetében.

##<a name="data-management"></a>Az Azure DocumentDB erőforrásai
Az Azure DocumentDB jól meghatározott adatbázis-erőforrások használatával kezeli az adatokat. A magas rendelkezésre állás érdekében a rendszer replikálja ezeket az erőforrásokat, amelyek a logikai URI-juk alapján egyedi módon címezhetők. A DocumentDB egy egyszerű HTTP-alapú RESTful programozási modellt kínál minden erőforráshoz. 

A DocumentDB-adatbázis fiók egy egyedi névtér, amely hozzáférést biztosít az Azure DocumentDB-hez. Ahhoz, hogy létrehozhasson egy adatbázis-fiókot, Azure-előfizetéssel kell rendelkeznie, amely számos különböző Azure-szolgáltatáshoz biztosít hozzáférést. 

A DocumentDB összes erőforrásának modellezése és tárolása JSON-dokumentumként történik. Az erőforrások elemekként és csatornákként vannak kezelve. Az elemek metaadatokat tartalmazó JSON-dokumentumok, a csatornák pedig elemek gyűjteményei. Az elemkészletek a megfelelő csatornákban találhatók.

Az alábbi képen láthatók a DocumentDB erőforrásainak kapcsolatai:

![A DocumentDB, egy NoSQL-alapú JSON-adatbázis erőforrásainak hierarchikus kapcsolatai.][1] 

Egy adatbázis-fiók adatbázis-készletekből áll, amelyek mindegyike több gyűjteményt tartalmaz. A gyűjtemények tárolt eljárásokat, eseményindítókat, felhasználó által megadott függvényeket, dokumentumokat és kapcsolódó mellékleteket tartalmazhatnak. Egy adatbázisnak továbbá felhasználói is vannak, amelyek mindegyike rendelkezik egy engedélykészlettel a különféle egyéb gyűjtemények, tárolt eljárások, eseményindítók, felhasználó által megadott függvények, dokumentumok és mellékletek eléréséhez. Amíg az adatbázisok, felhasználók és gyűjtemények rendszer által meghatározott, jól ismert sémákkal rendelkező erőforrások, addig a dokumentumok, tárolt eljárások, eseményindítók, felhasználó által megadott függvények és mellékletek tetszőleges, felhasználó által megadott JSON-tartalmak lehetnek.  

##<a name="develop"></a> Fejlesztés az Azure DocumentDB használatával
Az Azure DocumentDB egy REST API-n keresztül tesz elérhetővé erőforrásokat, amelyet bármilyen, HTTP/HTTPS-kérelem indítására képes nyelv meghívhat. Ezenfelül a DocumentDB számos népszerű nyelvhez biztosít programozási könyvtárakat. Ezek a könyvtárak sok szempontból leegyszerűsítik az Azure DocumentDB használatát, mivel számos alfolyamatot kezelnek (például címek gyorsítótárazása, kivételek kezelése, automatikus újrapróbálkozások stb.). Jelenleg a következő nyelvekhez és platformokhoz érhetők el könyvtárak:  

Letöltés | Dokumentáció
--- | ---
[.NET SDK](http://go.microsoft.com/fwlink/?LinkID=402989) | [.NET kódtár](https://msdn.microsoft.com/library/azure/dn948556.aspx)
[Node.js SDK](http://go.microsoft.com/fwlink/?LinkID=402990) | [Node.js kódtár](http://azure.github.io/azure-documentdb-node/)
[Java SDK](http://go.microsoft.com/fwlink/?LinkID=402380) | [Java kódtár](http://azure.github.io/azure-documentdb-java/)
[JavaScript SDK](http://go.microsoft.com/fwlink/?LinkID=402991) | [JavaScript kódtár](http://azure.github.io/azure-documentdb-js/)
n/a | [Kiszolgálóoldali JavaScript SDK](http://azure.github.io/azure-documentdb-js-server/)
[Python SDK](https://pypi.python.org/pypi/pydocumentdb) | [Python kódtár](http://azure.github.io/azure-documentdb-python/)

Az alapvető létrehozási, olvasási, frissítési és törlési műveletek mellett a DocumentDB egy részletes SQL-lekérdezési felületet is biztosít JSON-dokumentumok és kiszolgálóoldali támogatás lekéréséhez a JavaScript-alkalmazáslogika tranzakciós végrehajtásához. A lekérdezés és parancsfájl végrehajtására szolgáló felületek az összes platform könyvtárán, illetve a REST API-kon keresztül is elérhetők. 

### SQL-lekérdezés
Az Azure DocumentDB a JavaScript típusrendszerből eredő SQL-nyelv, valamint a relációs, hierarchikus és térbeli lekérdezéseket támogató kifejezések használatával támogatja a dokumentumok lekérdezését. A DocumentDB lekérdezési nyelv a JSON-dokumentumok lekérdezésének egyszerű, mégis erőteljes felülete. A nyelv támogatja az ANSI SQL-szintaxis egy alkészletét, valamint biztosítja a JavaScript-objektumok, tömbök, objektumkonstrukciók és függvényhívások szoros integrációját. A DocumentDB a fejlesztőtől származó explicit séma vagy indexelési mutatók nélkül biztosítja a lekérdezési modelljét.

A felhasználó által megadott függvények regisztrálhatók a DocumentDB-ben, és az SQL-lekérdezés részeként hivatkozhatók, így a szintaxis kiterjeszthető az egyéni alkalmazáslogika támogatása érdekében. Ezen felhasználó által megadott függvények JavaScript programokként vannak megírva, futtatásuk pedig az adatbázisban történik. 

A .NET-fejlesztők számára a DocumentDB egy LINQ-lekérdezést is kínál a [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx) részeként. 

### Tranzakciók és a JavaScript futtatása
A DocumentDB lehetővé teszi az alkalmazáslogikák teljes mértékben JavaScriptben írt, elnevezett programokként történő megírását. Ezeket a programokat a rendszer regisztrálja egy gyűjteményben, és adatbázis-műveleteket adhatnak ki az adott gyűjteményben lévő dokumentumokon. A JavaScript regisztrálható eseményindítóként, tárolt eljárásként vagy felhasználó által megadott függvényként való futtatásra. Az eseményindítók és tárolt eljárások létrehozhatnak, olvashatnak, frissíthetnek és törölhetnek dokumentumokat, míg a felhasználó által megadott függvények futtatása lekérdezés végrehajtási logikájának részeként történik, a gyűjteményhez történő írási hozzáférés nélkül.

A JavaScript DocumentDB-ben való futtatásának modellezése a relációs adatbázis-rendszerek által támogatott alapelvek alapján történik, ahol a JavaScript a Transact-SQL modern helyettesítője. Minden JavaScript-logika futtatása egy környezeti ACID-tranzakción belül történik pillanatkép-elkülönítéssel. Ha a futtatása során a JavaScript kivételt jelez, a teljes tranzakció megszakad.

## Következő lépések
Amennyiben már rendelkezik Azure-fiókkal, egy [DocumentDB adatbázis-fiók létrehozásával](https://portal.azure.com/#gallery/Microsoft.DocumentDB) elkezdheti a DocumentDB használatát az [Azure-portálon](documentdb-create-account.md).

Ha nem rendelkezik Azure-fiókkal, a következőket teheti:

- Regisztrálhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/pricing/free-trial/), amely 30 napot és egy 200 dolláros keretet biztosít az összes Azure-szolgáltatás kipróbálására. 
- Ha MSDN-előfizetéssel rendelkezik, akkor [havi 150 dollár ingyenes Azure-kreditre](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) jogosult, amit bármelyik Azure-szolgáltatáshoz felhasználhat. 

Ezután, amikor készen áll a tanulásra, tekintse meg a [képzési tervünket](https://azure.microsoft.com/documentation/learning-paths/documentdb/), és azon belül az összes elérhető képzési erőforrást. 


[1]: ./media/documentdb-introduction/json-database-resources1.png
 



<!--HONumber=Jun16_HO2-->


