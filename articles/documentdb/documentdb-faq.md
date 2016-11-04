---
title: Kérdések a DocumentDB adatbázissal kapcsolatban – Gyakori kérdések | Microsoft Docs
description: Válaszokat kaphat az Azure DocumentDB, egy JSON-hoz készült NoSQL-alapú dokumentumadatbázis-szolgáltatással kapcsolatos gyakori kérdésekre. Az adatbázis kapacitásával, teljesítményszintjeivel és méretezhetőségével kapcsolatos kérdések megválaszolása.
keywords: Database questions, frequently asked questions, documentdb, azure, Microsoft azure
services: documentdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: ''

ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/30/2016
ms.author: mimig

---
# Gyakori kérdések a DocumentDB-ről
## Az adatbázisra vonatkozó kérdések a Microsoft Azure DocumentDB alapjairól
### Mi a Microsoft Azure DocumentDB?
A Microsoft Azure DocumentDB szolgáltatás egy kiválóan méretezhető, NoSQL-alapú dokumentumkezelő adatbázis-szolgáltatás, amely sémamentes adatok széleskörű lekérdezését biztosítja, konfigurálható és megbízható teljesítményt nyújt, valamint gyors fejlesztést tesz lehetővé – mindezt egy felügyelt platformon keresztül, amelynek alapjául az Azure sokoldalúsága és széles körű alkalmazhatósága szolgál. A DocumentDB minden olyan webes, mobil-, játék- és IoT-alkalmazáshoz megfelelő megoldás, amelynél a kiszámítható teljesítmény, a közel valós idejű hozzáférés és a sémamentesadat-modell jelenti a legfőbb szempontokat. A DocumentDB a natív JSON-adatmodell révén biztosítja a sémák rugalmasságát és a gazdag indexelési lehetőségeket, továbbá JavaScript-integrációval ellátott, több dokumentumos tranzakciótámogatást is tartalmaz.  

Az adatbázissal kapcsolatos további kérdésekért, válaszokért, valamint a szolgáltatás telepítésére és használatára vonatkozó utasításokért lásd: [DocumentDB dokumentációs oldal](https://azure.microsoft.com/documentation/services/documentdb/).

### Milyen adatbázis a DocumentDB?
A DocumentDB egy NoSQL-alapú, dokumentumközpontú adatbázis, amely JSON formátumban tárolja az adatokat.  A DocumentDB támogatja a beágyazott, önálló adatstruktúrákat, amelyek egy gazdag DocumentDB [SQL-lekérdezési szintaxis](documentdb-sql-query.md) használatával kérhetők le. A DocumentDB [tárolt eljárások, eseményindítók és felhasználó által megadott függvények](documentdb-programming.md) révén biztosítja a kiszolgálóoldali JavaScript nagy teljesítményű tranzakciós feldolgozását. Az adatbázis továbbá támogatja a fejlesztők által beállítható konzisztenciaszinteket a [társított teljesítményszintekkel](documentdb-performance-levels.md).

### Rendelkeznek-e a DocumentDB-adatbázisok olyan táblákkal, mint egy relációs adatbázis (RDBMS)?
Nem, a DocumentDB JSON-dokumentumok gyűjteményeiben tárolja az adatokat.  A DocumentDB erőforrásaival kapcsolatos információkért lásd: [A DocumentDB erőforrás-modellje és fogalmai](documentdb-resources.md). 

### Támogatják-e a DocumentDB-adatbázisok a sémamentes adatokat?
Igen, a DocumentDB engedélyezi az alkalmazások számára, hogy sémadefiníciók vagy mutatók nélkül tároljanak tetszőleges JSON-dokumentumokat. Az adatok azonnal lekérdezhetők a DocumentDB SQL-lekérdezési felületén keresztül.   

### Támogatja a DocumentDB az ACID-tranzakciókat?
Igen, a DocumentDB támogatja a JavaScriptben tárolt eljárásokként és eseményindítókként kifejezett dokumentumok közötti tranzakciókat. A tranzakciók az egyes gyűjteményeken belül egyetlen partícióra vannak korlátozva, végrehajtásuk pedig az ACID-szemantikákkal, mindent vagy semmit alapon történik a többi párhuzamosan végrehajtott kódtól vagy felhasználói kérelmektől elkülönítve.  Ha a JavaScript alkalmazáskód kiszolgálóoldali végrehajtásakor kivételek jelentkeznek, a teljes tranzakció vissza lesz állítva. 

### Melyek a DocumentDB jellemző használati esetei?
A DocumentDB jó választás az új webes, mobil-, játék- és IoT-alkalmazásokhoz, amelyek esetében fontos az automatikus méretezés, a kiszámítható teljesítmény, az ezredmásodperces válaszidők gyorsasága és a sémamentes adatokra épülő lekérdezés képessége. A DocumentDB rendkívül gyors fejlesztést tesz lehetővé, így támogatja az alkalmazás adatmodelljeinek folyamatos ismétlését. A felhasználók által létrehozott tartalmakat és adatokat kezelő alkalmazások a [DocumentDB gyakori alkalmazási esetei](documentdb-use-cases.md).  

### Hogyan kínál a DocumentDB kiszámítható teljesítményt?
A DocumentDB esetében az átviteli sebesség mértéke a Kérelemegység (RU – Request unit). 1 RU a GET átviteli sebességének felel meg egy 1 KB-os dokumentum esetében. A DocumentDB minden művelete, köztük az olvasások, írások, SQL-lekérdezések és a tárolt eljárások végrehajtása rendelkezik egy meghatározó Kérelemegység értékkel, amely a művelet végrehajtásához szükséges átviteli sebességen alapul.  Ahelyett, hogy a processzorhasználaton, az I/O-komponenseken, a memórián és ezek az alkalmazás átviteli sebességére gyakorolt hatásán gondolkodna, csupán egyetlen mértéket, a kérelemegységet kell figyelembe vennie.

Az egyes DocumentDB-gyűjtemények a létesített átviteli sebesség révén lefoglalhatók a másodpercenkénti átvitelek kérelemegysége alapján. Ha szeretne valamilyen skálát alkalmazni, felmérheti az egyes kérelmeket a kérelemegység-értékük megállapításához, és gyűjteményeket létesíthet az összes kérelem összes kérelemegységének kezeléséhez. Az alkalmazás igényeinek változásával növelheti vagy csökkentheti a gyűjtemény átviteli sebességét. A kérelemegységekkel kapcsolatos további információkért, valamint a gyűjtemény igényeinek meghatározására vonatkozó utasításokért olvassa el a következőt: [Teljesítmény és kapacitás kezelése](documentdb-manage.md).

### A DocumentDB megfelel a HIPAA-szabványnak?
Igen, a DocumentDB megfelel a HIPAA-szabványnak. A HIPAA az egyéni és beazonosítható egészségügyi adatok használatára, nyilvánosságra hozatalára és védelmére vonatkozó követelményeket állapítja meg. További információkért lásd: [Microsoft Adatvédelmi központ](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### Mik a DocumentDB tárolási korlátai?
Egy gyűjtemény által a DocumentDB-adatbázisban tárolható adatok teljes mennyiségének nincs elméleti korlátja. Ha egy gyűjteményben több mint 250 GB adatot szeretne tárolni, a fiók kvótájának növeléséért [lépjen kapcsolatba az ügyfélszolgálattal](documentdb-increase-limits.md). 

### Mik a DocumentDB átviteli sebességének korlátai?
Nincs elméleti korlátja annak, hogy egy gyűjtemény összesen mekkora átviteli sebességet támogat a DocumentDB-adatbázisban, amennyiben a munkaterhelés nagyjából egyenlően osztható el megfelelően nagy számú partíciós kulcsok között. Ha szeretné meghaladni a gyűjteményenkénti vagy fiókonkénti 250 000 kérelemegység/másodperces értéket, a fiók kvótájának növeléséért [lépjen kapcsolatba az ügyfélszolgálattal](documentdb-increase-limits.md). 

### Mennyibe kerül a Microsoft Azure DocumentDB?
A részletekért tekintse meg a [DocumentDB díjszabása](https://azure.microsoft.com/pricing/details/documentdb/) című oldalt. A DocumentDB használati díját a használatban lévő gyűjtemények száma, a gyűjtemények online állapotban töltött óráinak száma, valamint az egyes gyűjtemények esetében felhasznált tárterület mérete és a létesített átviteli sebesség határozza meg. 

### Elérhető-e ingyenes fiók?
Ha korábban még nem használta az Azure-t, regisztrálhat egy [Ingyenes Azure-fiókot](https://azure.microsoft.com/pricing/free-trial/), ami 30 napot és egy 200 dolláros keretet biztosít az összes Azure-szolgáltatás kipróbálására. Illetve ha Visual Studio-előfizetéssel rendelkezik, akkor [havi 150 dollár ingyenes Azure-kreditre](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) jogosult, amit bármelyik Azure-szolgáltatáshoz felhasználhat.  

### Hogyan kaphatok további segítséget a DocumentDB-vel kapcsolatban?
Ha segítségre van szüksége, lépjen kapcsolatba velünk a [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-documentdb) és az [Azure DocumentDB MSDN fejlesztői fórumok](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureDocumentDB) segítségével, vagy foglaljon időpontot egy [személyes csevegésre a DocumentDB mérnöki csapatával](http://www.askdocdb.com/). Ha értesülni szeretne a DocumentDB-vel kapcsolatos legújabb hírekről és szolgáltatásokról, kövessen minket a [Twitteren](https://twitter.com/DocumentDB).

## A Microsoft Azure DocumentDB beállítása
### Hogyan regisztrálhatok a Microsoft Azure DocumentDB szolgáltatásra?
A DocumentDB az [Azure portálról][azure-portal] érhető el.  Először regisztrálnia kell egy Microsoft Azure-előfizetésre.  A Microsoft Azure-előfizetési regisztrációt követően a DocumentDB-fiókot hozzáadhatja Azure-előfizetéséhez. A DocumentDB-fiók hozzáadására vonatkozó utasításokért lásd: [DocumentDB adatbázis-fiók létrehozása](documentdb-create-account.md).   

### Mi a főkulcs?
A főkulcs egy biztonsági jogkivonat, amellyel egy fiók összes erőforrása elérhető. A főkulccsal rendelkező egyének olvasási és írási hozzáféréssel rendelkeznek az adatbázis-fiók összes erőforrásához. Legyen körültekintő a főkulcsok kiosztásakor. Az elsődleges és másodlagos főkulcsok az [Azure Portal][azure-portal] **Kulcsok** panelén érhetők el. A kulcsokkal kapcsolatos további információkért lásd: [Hozzáférési kulcsok megtekintése, másolása és újragenerálása](documentdb-manage-account.md#keys).

### Hogyan hozható létre adatbázis?
Adatbázisokat a [DocumentDB-adatbázisok létrehozása](documentdb-create-database.md) című leírás alapján az [Azure-portál](), a [DocumentDB SDK-k](documentdb-sdk-dotnet.md) egyike vagy a [REST API-k](https://msdn.microsoft.com/library/azure/dn781481.aspx) használatával hozhat létre.  

### Mi a gyűjtemény?
A gyűjtemény egy JSON-dokumentumokat és a kapcsolódó JavaScript-alkalmazáslogikát tartalmazó tároló. A gyűjtemény egy számlázható entitás, amelyek esetében a [költséget](documentdb-performance-levels.md) a gyűjteményhez társított teljesítményszint határozza meg. A gyűjtemények egy vagy több partícióra/kiszolgálóra is kiterjedhetnek, valamint gyakorlatilag korlátlan mennyiségű tárterület vagy átviteli sebesség kezelésére méretezhetők.

A gyűjtemények továbbá a DocumentDB számlázási egységei. A gyűjtemények számlázása óránként történik a létesített átviteli sebesség és a felhasznált tárterület alapján. További információkért lásd: [A DocumentDB díjszabása](https://azure.microsoft.com/pricing/details/documentdb/).  

### Hogyan állíthatom be a felhasználókat és engedélyeket?
Felhasználókat és engedélyeket a [DocumentDB SDK-k](documentdb-sdk-dotnet.md) egyike vagy a [REST API-k](https://msdn.microsoft.com/library/azure/dn781481.aspx) használatával hozhat létre.   

## Adatbázissal kapcsolatos kérdések a Microsoft Azure DocumentDB használatával történő fejlesztésről
### Hogyan kezdhetem el a fejlesztését a DocumentDB használatával?
[SDK-k](documentdb-sdk-dotnet.md) a .NET, Python, Node.js, JavaScript és Java esetében érhetők el.  A fejlesztők továbbá a [RESTful HTTP API-k](https://msdn.microsoft.com/library/azure/dn781481.aspx) segítségével is használhatják a DocumentDB-erőforrásokat számos különböző platformon és programnyelven. 

A DocumentDB-hez tartozó [.NET](documentdb-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](documentdb-nodejs-samples.md) és [Python](documentdb-python-samples.md) SDK-minták a GitHubon érhetők el.

### Támogatja-e a DocumentDB az SQL-t?
A DocumentDB SQL lekérdező nyelve az SQL által támogatott lekérdezési funkcionalitás továbbfejlesztett alkészlete. A DocumentDB SQL lekérdező nyelve a JavaScript-alapú, felhasználó által megadott funkciók révén széles körű hierarchikus és relációs operátorokat és bővíthetőséget biztosít.   A JSON-szintaxis lehetővé teszi a JSON-dokumentumok fákként való modellezését, ahol a fa csomópontjai olyan címkék, amelyeket a DocumentDB automatikus indexelési technikái és a DocumentDB SQL-lekérdezési dialektusa egyaránt használ.  Az SQL-szintaxis használatával kapcsolatos részletekért lásd: [A DocumentDB lekérdezése][lekérdezés] című cikk.

### Mely adattípusokat támogatja a DocumentDB?
A DocumentDB ugyanazokat a primitív adattípusokat támogatja, mint a JSON. A JSON egyszerű típusrendszerrel rendelkezik, amely karakterláncokból, számokból (IEEE754 kétszeres pontosságú) és logikai értékekből áll – igaz, hamis és null.  Az összetettebb adattípusok (pl. dátum/idő, GUID, Int64 és geometria) a JSON-fájlban és a DocumentDB-ben egyaránt ábrázolhatók a beágyazott objektumok { } operátorral való létrehozásával, illetve a tömbök [ ] operátorral történő létrehozásával. 

### Hogyan biztosítja a DocumentDB az egyidejűséget?
A DocumentDB HTTP entitáscímkék vagy ETag-ek használatával támogatja az egyidejű hozzáférések optimista vezérlését (OCC). Minden DocumentDB erőforrás rendelkezik egy ETag-gel, és a DocumentDB-ügyfelek írási kéréseiben szerepel a legutóbb beolvasott változatuk. Ha az ETag naprakész, a módosítás véglegesítve lesz. Ha az értéket kívülről megváltoztatták, a kiszolgáló elutasítja az írást egy „HTTP 412 Sikertelen előfeltétel” válaszkóddal. Az ügyfeleknek be kell olvasniuk az erőforrás legfrissebb verzióját, majd meg kell ismételniük a kérelmet. 

### Hogyan végezhetők tranzakciók a DocumentDB-ben?
A DocumentDB JavaScriptben tárolt eljárások és eseményindítók révén támogatja a nyelvintegrált tranzakciókat. A parancsfájlban az összes művelet pillanatkép-elkülönítés használatával lesz végrehajtva, egypartíciós gyűjtemény esetében a gyűjtemény hatókörében, particionált gyűjtemény esetében pedig a gyűjteményen belül azonos partíciós kulccsal rendelkező dokumentumok hatókörében. A tranzakció kezdetekor pillanatkép készül a dokumentumverziókról (ETag-ek), és csak akkor lesznek véglegesítve, ha a parancsfájl sikeres. Ha a JavaScript hibát jelez, a tranzakció vissza lesz állítva. További részletekért lásd: [DocumentDB kiszolgálóoldali programozása](documentdb-programming.md).

### Hogyan szúrhatók be kötegelve dokumentumok a DocumentDB-be?
A dokumentumok háromféleképpen szúrhatók be kötegelve a DocumentDB-be:

* Az adatáttelepítés eszközzel az [Adatok importálása a DocumentDB-be](documentdb-import-data.md) című leírásban foglaltak szerint.
* Az Azure-portálon található Dokumentumtallózó használatával a [Dokumentumok kötegelt hozzáadása a Dokumentumtallózóval](documentdb-view-json-document-explorer.md#BulkAdd) című leírásban foglaltak szerint.
* Tárolt eljárások használatával a [DocumentDB kiszolgálóoldali programozása](documentdb-programming.md) című leírásban foglaltak szerint.

### Támogatja a DocumentDB az erőforrás-hivatkozások gyorsítótárazását?
Igen. Mivel a DocumentDB egy RESTful szolgáltatás, az erőforrás-hivatkozások nem módosíthatók, és ezáltal gyorsítótárazhatók. A DocumentDB-ügyfelek megadhatnak egy „If-None-Match” fejlécet bármilyen erőforrás, például dokumentum vagy gyűjtemény olvasása esetén, és csak akkor frissítik a helyi másolataikat, ha a kiszolgálóverzió módosult. 

[azure-portal]: https://portal.azure.com
[lekérdezés]: documentdb-sql-query.md



<!--HONumber=Jun16_HO2-->


