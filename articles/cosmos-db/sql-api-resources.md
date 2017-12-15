---
title: "Az Azure Cosmos DB erőforrás-modellje és fogalmai |} Microsoft Docs"
description: "További információk a hierarchikus modell Azure Cosmos DB adatbázisok, gyűjtemények, felhasználó által definiált függvény (UDF), dokumentumok, erőforrások és még sok más kezeléséhez szükséges jogokat kapjon."
keywords: Hierarchikus modell cosmosdb, az azure, a Microsoft azure
services: cosmos-db
documentationcenter: 
author: rafats
manager: jhubbard
ms.assetid: ef9d5c0c-0867-4317-bb1b-98e219799fd5
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: rafats
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a88f17a658987e1ff3ae0e0f38d6551c3acee1da
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="azure-cosmos-db-hierarchical-resource-model-and-core-concepts"></a>Az Azure Cosmos DB hierarchikus erőforrás-modellje és alapfogalmai

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Az Azure Cosmos DB kezelő adatbázis entitásokat nevezzük **erőforrások**. Az egyes erőforrások egyedileg azonosít egy logikai URI-t. Az erőforrások szabványos HTTP-műveletek, kérelem/válasz fejlécek és állapotkódokat alállapotkódok használatával kommunikálhat. 

Ez a cikk az alábbi kérdéseket:

* Mi az Azure Cosmos DB modell?
* Mik azok a rendszer meghatározott felhasználói erőforrások figyelésekor erőforrásokat?
* Hogyan kezelje a egy erőforrást?
* Hogyan működik a gyűjteményekkel?
* Hogyan működik a tárolt eljárások, eseményindítók és felhasználó által megadott funkciókat (UDF)?

## <a name="hierarchical-resource-model"></a>Hierarchikus erőforrás-modellje
Az az alábbi ábrán látható módon, hierarchikus Azure Cosmos-DB **erőforrás-modellje** áll egy adatbázis-fiók, logikai és állandó URI segítségével minden egyes megcímezhető alatt lévő erőforrások csoportja. Erőforráscsoport nevezzük egy **hírcsatorna** ebben a cikkben. 

> [!NOTE]
> Azure Cosmos DB kínál a rendkívül hatékony TCP protokoll, amely egyben RESTful a kommunikációs modellben, keresztül érhető el a [SQL .NET ügyfél API-ja](sql-api-sdk-dotnet.md).
> 
> 

![Az Azure Cosmos DB hierarchikus erőforrás-modellje][1]  
**Hierarchikus erőforrás-modellje**   

Erőforrások munkakezdéshez kell [adatbázisfiók létrehozása](create-sql-api-dotnet.md) használata az Azure-előfizetéshez. Az adatbázisfiók állhat egy **adatbázisok**, több tartalmazó **gyűjtemények**, minden egyes, viszont tartalmazó ** tárolt eljárások, eseményindítók, felhasználó által megadott függvények, dokumentumok, és a kapcsolódó  **mellékletek**. Egy adatbázis is vannak társítva **felhasználók**, egyes számú **engedélyek** gyűjtemények, tárolt eljárások, eseményindítók, felhasználó által megadott függvények, dokumentumok és mellékletek eléréséhez. Míg adatbázisok, felhasználók, engedélyek és gyűjtemények rendszer által meghatározott erőforrások jól ismert sémákkal rendelkező, a dokumentumok és mellékletek tetszőleges, felhasználó által megadott JSON-tartalmak tartalmaz.  

| Erőforrás | Leírás |
| --- | --- |
| Adatbázisfiók |Az adatbázisfiók adatbázisok és a rögzített méretű mellékletek blob-tároló állítja be hozzá rendelve. Létrehozhat egy vagy több adatbázis fiókot az Azure-előfizetését használja. További tudnivalókért keresse fel a [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/cosmos-db/). |
| Adatbázis |Egy adatbázis a dokumentumtároló gyűjtemények között particionált logikai tárolója. Akkor is a felhasználók tárolójába kerülnek. |
| Felhasználó |Az engedélyek hatókörének logikai névtere. |
| Engedély |Egy adott erőforráshoz való hozzáférés egy felhasználó társított engedélyezési jogkivonatot. |
| Gyűjtemény |A gyűjtemény egy JSON-dokumentumokat és a kapcsolódó JavaScript-alkalmazáslogikát tartalmazó tároló. Egy gyűjtemény egy számlázható entitás, ahol a [költség](performance-levels.md) a gyűjteményhez társított teljesítményszint határozza meg. A gyűjtemények egy vagy több partícióra/kiszolgálóra is kiterjedhetnek, valamint gyakorlatilag korlátlan mennyiségű tárterület vagy átviteli sebesség kezelésére méretezhetők. |
| Tárolt eljárás |A JavaScript, amely regisztrálva gyűjtemény és tranzakciós úton futtatásuk az adatbázismotor írt alkalmazás logikáját. |
| Eseményindító |Úgy az alkalmazáslogikát végrehajtása előtt vagy után vagy egy INSERT utasítás, JavaScript nyelven írt csere vagy törlési művelet. |
| AZ UDF |JavaScript nyelven írt alkalmazás logikáját. Felhasználó által megadott függvények lehetővé teszik egy egyéni lekérdezés operátor modell, és ezáltal kiterjesztése a core SQL API lekérdező nyelve. |
| Dokumentum |Felhasználó által definiált (tetszőleges) JSON-tartalmak. Alapértelmezés szerint nem tartozik séma kell definiálni, sem másodlagos indexek kell adni a gyűjteménybe felvett összes dokumentumot. |
| Melléklet |Egy mellékletet tartalmazó hivatkozásokat és a külső blob/médiához kapcsolódó metaadatok különleges dokumentumot. A fejlesztői választhat Cosmos DB kezeli a blob rendelkezik, vagy tárolja el azt egy külső blob-szolgáltatónál, például a onedrive-on, Dropbox, stb. |

## <a name="system-vs-user-defined-resources"></a>A rendszer és felhasználói erőforrások
Erőforrások, például adatbázis-fiókokat, adatbázisok, gyűjtemények, felhasználók, engedélyek, tárolt eljárások, eseményindítók és felhasználó által megadott függvények - rögzített sémájába rendelkeznek, és rendszererőforrásokat nevezzük. Ezzel szemben erőforrások, például a dokumentumok és mellékletek rendelkezik nincs korlátozás a séma, és felhasználói erőforrások példát. A Cosmos DB rendszer és felhasználói erőforrásokat is képviselt és felügyelni standard-kompatibilis JSON-ként. Minden erőforrások, a rendszer vagy a felhasználó által definiált, a következő közös jellemzőkkel rendelkezik:

> [!NOTE]
> Összes rendszer által létrehozott tulajdonság az erőforrás fűzve előtagként a saját JSON-megjelenítés aláhúzással (_).
> 
> 

<table>
    <tbody>
        <tr>
            <td valign="top"><p><strong>Tulajdonság</strong></p></td>
            <td valign="top"><p><strong>Állítható be a felhasználó vagy rendszer által létrehozott?</strong></p></td>
            <td valign="top"><p><strong>Cél</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>_rid</p></td>
            <td valign="top"><p>Rendszer által létrehozott</p></td>
            <td valign="top"><p>Rendszer által létrehozott, az erőforrás és a hierarchikus egyedi azonosítója</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_etag</p></td>
            <td valign="top"><p>Rendszer által létrehozott</p></td>
            <td valign="top"><p>az egyidejű hozzáférések optimista vezérlését a szükséges erőforrás ETag</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_ts</p></td>
            <td valign="top"><p>Rendszer által létrehozott</p></td>
            <td valign="top"><p>Az erőforrás utolsó frissített időbélyegzője</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_self</p></td>
            <td valign="top"><p>Rendszer által létrehozott</p></td>
            <td valign="top"><p>Az erőforrás egyedi címmel rendelkező URI-azonosító</p></td>
        </tr>
        <tr>
            <td valign="top"><p>id</p></td>
            <td valign="top"><p>Vagy</p></td>
            <td valign="top"><p>Az erőforrás (az egyazon partíciókulcs-értékkel), felhasználó által definiált egyedi neve. Ha a felhasználó nem adja meg az azonosítót, egy azonosító-e a rendszer által létrehozott</p></td>
        </tr>
    </tbody>
</table>

### <a name="wire-representation-of-resources"></a>Átviteli megjelenítésűre erőforrások
Cosmos DB határozza meg, hogy saját fejlesztésű kiterjesztések számára a JSON standard vagy különleges kódolások; standard megfelelő JSON-dokumentumok együttműködik.  

### <a name="addressing-a-resource"></a>Egy erőforrás címzés
Minden erőforrás URI-címmel rendelkező. Értékét a **_self** erőforrás tulajdonság adja meg az erőforrás relatív URI. Az URI formátuma áll a /\<hírcsatorna\>/ {_rid} szegmenst:  

| A _self értéke | Leírás |
| --- | --- |
| /dbs |Adatcsatorna adatbázisok egy adatbázis-fiókkal |
| /dbs/ {%{dbname/} |{%{Dbname/} értékével egyező azonosítójú adatbázis |
| {%{dbname/} /dbs/ /colls/ |A gyűjtemények az adatbázis adatcsatorna |
| {%{dbname/} /dbs/ /colls/ {collName} |{CollName} értékével egyező azonosítójú gyűjtemény |
| {%{dbname/} /dbs/ /colls/ {collName} / docs |A gyűjtemény dokumentumok adatcsatorna |
| {%{dbname/} /dbs/ /colls/ {collName} /docs/ {dokumentumazonosító} |{Doc} értékével egyező azonosítójú dokumentálása |
| {%{dbname/} /dbs/ /felhasználók/ |A felhasználók egy adatbázist a hírcsatorna |
| {%{dbname/} /dbs/ /felhasználók/ {userId} |{Felhasználó} értékével egyező azonosítójú felhasználó |
| {%{dbname/} /dbs/ /felhasználók/ {userId} / engedélyek |Adatcsatorna egy felhasználói engedélyek |
| {%{dbname/} /dbs/ /felhasználók/ {userId} /permissions/ {permissionId} |{Engedély} értékével egyező azonosítójú engedély |

Minden erőforrás azonosítója tulajdonságon keresztül elérhetővé tett egyedi felhasználói névvel rendelkezik. Megjegyzés: a dokumentumok, ha a felhasználó nem adja meg az azonosító, az SDK-k automatikusan egyedi azonosítót előállítani a dokumentumhoz. Az azonosító: felhasználói karakterlánc, legfeljebb 256 karakter, amely egy adott szülő erőforrás keretén belül egyedi. 

Az egyes erőforrások is rendelkeznek, a rendszer által létrehozott hierarchikus erőforrás-azonosítója (más néven a relatív AZONOSÍTÓK), _rid tulajdonságon keresztül elérhető. A relatív AZONOSÍTÓK kódolja a teljes hierarchia egy adott erőforrás és a hivatkozási integritás elosztott módon használt kényelmes belső megjelenítése. A relatív AZONOSÍTÓK egyedi belül egy adatbázis-fiókot, és belső használható Cosmos DB anélkül, hogy több partíció keresések hatékony útválasztást. A _self és _rid tulajdonságainak értékei erőforrás mind a másodlagos, és a kanonikus ábrázolásai. 

A REST API-k erőforrások címzést és útválasztást kérelem az azonosítója és a _rid tulajdonságok is támogatja.

## <a name="database-accounts"></a>Adatbázis-fiókok
Megadhat egy vagy több Cosmos DB adatbázis fiókot az Azure-előfizetését használja.

Hozhat létre és kezelheti a Cosmos DB adatbázis fiókokat, Azure-portálon [http://portal.azure.com/](https://portal.azure.com/). Létrehozását és kezelését egy adatbázis-fiók rendszergazdai hozzáférésre van szüksége, és csak az Azure-előfizetéshez tartozó hajtható végre. 

### <a name="database-account-properties"></a>Adatbázis-fiók tulajdonságai
Kiépítése és kezelése az adatbázisfiók részeként konfigurálja, és olvassa el a következő tulajdonságokkal:  

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Tulajdonság neve</strong></p></td>
            <td valign="top"><p><strong>Leírás</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Konzisztencia-házirend</p></td>
            <td valign="top"><p>Állítsa ezt a tulajdonságot, konfigurálhatja az adatbázis-fiókjában a gyűjtemények alapértelmezett konzisztencia szintjét. Felülírhatja a konzisztenciaszint használatával [x-ms-konzisztencia-szint] kérelemfejléc egy kérelem alapon. <p><p>Ez a tulajdonság csak vonatkozik a <i>felhasználói erőforrások</i>. Meghatározott források támogatására vannak konfigurálva az összes rendszer olvasás/lekérdezések erős konzisztencia.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Engedélyezési kulcsok</p></td>
            <td valign="top"><p>Az elsődleges és másodlagos fő- és a csak olvasható kulcsok, amelyek az erőforrásokat az adatbázis-fiókjában rendszergazdai hozzáférést biztosítanak.</p></td>
        </tr>
    </tbody>
</table>

Mellett kiépítés, konfigurálása és kezelése a adatbázisfiókot az Azure portálról programozott módon is létrehozhat és Cosmos DB adatbázis fiókok használatával kezelni a [Azure Cosmos DB REST API-k](/rest/api/documentdb/) valamint [ügyfél SDK-k](sql-api-sdk-dotnet.md).  

## <a name="databases"></a>Adatbázisok
Egy Cosmos DB adatbázisa egy logikai tároló egy vagy több gyűjtemények és a felhasználók, az alábbi ábrán látható módon. Tetszőleges számú adatbázishoz egy Cosmos DB adatbázis fiókkal ajánlat korlátai hozhat létre.  

![Fiók és a gyűjtemények hierarchikus adatbázismodell][2]  
**Egy adatbázis egy olyan logikai tároló, a felhasználók és gyűjtemények**

Egy adatbázis gyakorlatilag korlátlan dokumentumtároló gyűjtemények belül particionált tartalmazhat.

### <a name="elastic-scale-of-an-azure-cosmos-db-database"></a>Rugalmasan méretezhető Azure Cosmos DB adatbázis
A Cosmos DB adatbázisa alapértelmezés – petabájt dokumentumtároló biztonsági SSD és a létesített átviteli sebesség és a néhány GB közötti rugalmas. 

Ellentétben a hagyományos RDBMS adatbázis Cosmos-adatbázis egy adatbázis nem egyetlen gép hatókörét. A Cosmos DB az alkalmazás skálázási igények nő, létrehozhat több gyűjteményt, adatbázisok vagy mindkettőt. Valóban különböző első felek alkalmazásainak a Microsofton belül használt Azure Cosmos DB egy fogyasztói léptékű által a dokumentumtároló terabájt az egyes gyűjtemények tartalmazó ezer rendkívül nagy Azure Cosmos DB adatbázisok létrehozásához. Növekedhet, és egy adatbázis zsugorítása hozzáadásával vagy eltávolításával a gyűjtemények az alkalmazás méretezési követelmények teljesítéséhez. 

Az ajánlat függvényében adatbázisban lévő gyűjtemények tetszőleges számú hozhat létre. Minden gyűjtemény rendelkezik, a biztonsági SSD-tárolóba, a kiválasztott teljesítményszint függvényében meg kiosztott átviteli sebesség.

Egy Azure Cosmos DB adatbázis is egy olyan tároló, a felhasználók. Egy felhasználó, szolgálna, az engedélyek egy készletét, amely minden részletre kiterjedő engedélyezési és a gyűjtemények, dokumentumok és mellékletek eléréséhez logikai névterét.  

Egyéb erőforrásokat az Azure Cosmos DB erőforrás-modellje, adatbázisokat hozhatja létre, cserélni, törlése, olvasása, vagy segítségével könnyen számba a [REST API-k](/rest/api/documentdb/) vagy annak bármelyik a [ügyfél SDK-k](sql-api-sdk-dotnet.md). Azure Cosmos-adatbázis vagy a metaadatok adatbázis erőforrás lekérdezése az erős konzisztencia biztosítja. Egy adatbázis törlése automatikusan biztosítja, hogy nem tud hozzáférni a gyűjtemények vagy az abban szereplő felhasználók.   

## <a name="collections"></a>Gyűjtemények
A Cosmos DB gyűjtemény egy olyan tároló, a JSON-dokumentumok. 

### <a name="elastic-ssd-backed-document-storage"></a>Rugalmas biztonsági SSD-dokumentumtároló
Egy gyűjtemény belsőleg rugalmas – automatikusan növekszik, és vegye fel vagy távolítsa el a dokumentumok mértékben változik. Gyűjtemények logikai erőforrások, egy vagy több fizikai partíciók, sem kiszolgálók is kiterjedhetnek. A gyűjteményen belül partíciók száma Cosmos DB a tárhely méretét és a létesített átviteli sebesség a gyűjtemény alapján határozza meg. Minden partíció Cosmos DB SSD-biztonsági tárolási társítva a rögzített méretű rendelkezik, és a magas rendelkezésre állású replikálódik. Partíció felügyeleti teljes mértékben felügyelt Azure Cosmos DB, és komplex kódot írnia, vagy a partíciók kezelésére nem rendelkeznek. A cosmos DB gyűjtemények **gyakorlatilag korlátlan** tárolási és átviteli tekintetében. 

### <a name="automatic-indexing-of-collections"></a>Az automatikus indexeléshez gyűjtemények
Azure Cosmos-adatbázis egy valódi sémamentes adatbázisrendszer. Az feltételez vagy igényel semmilyen sémát a JSON-dokumentumok. Egy gyűjteményhez való hozzáadása során dokumentumok, Azure Cosmos DB automatikusan elvégzi a őket, és elérhetők lekérdezését. Automatikus indexelés dokumentumok anélkül, hogy a séma vagy a másodlagos indexek Azure Cosmos DB kulcs képesség és írási optimalizált, a zárolás ingyenes, és a naplószerkezetű karbantartási eljárások szerint engedélyezve van. Azure Cosmos-adatbázis konzisztens lekérdezések szolgálatban nagyon gyorsan írások tartós kötet támogatja. A dokumentum és a index tárolási rendszer kiszámítja a minden gyűjtemény által felhasznált tárterület. A tárterületi és teljesítménybeli kompromisszumot társított indexelő az indexelési házirendet egy gyűjtemény konfigurálásával szabályozhatja. 

### <a name="configuring-the-indexing-policy-of-a-collection"></a>Az indexelési házirendet gyűjtemény konfigurálása
Az indexelési házirendet az egyes gyűjtemények lehetővé teszi, hogy a teljesítmény- és tárolási kompromisszumot indexelő társított. Az alábbi beállítások érhetők el Önnek az indexelő konfiguráció részeként:  

* Válassza ki, hogy a gyűjtemény automatikusan elvégzi a valamennyi dokumentumot vagy sem. Alapértelmezés szerint automatikusan indexeli ugyan összes dokumentumot. Ha szeretné, kapcsolja ki az automatikus indexeléshez, és csak bizonyos dokumentumokhoz szelektív felvétele az index. Ezzel szemben dönthet úgy, szelektív módon kizárása csak bizonyos dokumentumokhoz. Ez érhet el, az automatikus tulajdonság igaz vagy hamis, a gyűjtemény indexingPolicy beállítása és használata [x-ms-indexingdirective] kérelemfejléc beszúrni, cseréje vagy dokumentum törlése közben.  
* Válasszon, hogy vagy egyedi elérési utak vagy dokumentumok minták kizárja az indexből. Érhet el ez a beállítás includedPaths és a gyűjtemény indexingPolicy excludedPaths rendre. A tárterületi és teljesítménybeli kompromisszumot ideális tartomány- és kivonatoló a megadott elérési út minták is konfigurálhatja. 
* Itt választhat szinkron (konzisztens) és aszinkron (lazy) index frissítéseket. Alapértelmezés szerint az index frissítése szinkron módon minden insert, replace vagy egy dokumentumot – így a gyűjtemény törlése. Ez lehetővé teszi, hogy a lekérdezések tiszteletben konzisztencia szintjét, a dokumentum olvasása. Míg Azure Cosmos DB írási optimalizált, és támogatja a szinkron index karbantartási és egységes lekérdezések szolgáltató együtt dokumentum írások tartós kötetek, egyes gyűjtemények indexét lazily frissítésére is konfigurálhat. A lusta indexelési a hanghatások írási további, és tömeges adatfeldolgozást forgatókönyvek elsősorban az olvasási műveleteket gyűjtemények ideális.

Az indexelési házirendet a gyűjteményen hajtja végre a PUT módosítható. Ez lehet érhető el, vagy a [ügyfél SDK](sql-api-sdk-dotnet.md), a [Azure-portálon](https://portal.azure.com) vagy a [REST API-k](/rest/api/documentdb/).

### <a name="querying-a-collection"></a>A gyűjtemény lekérdezése
A dokumentumok a gyűjteményen belül lehet tetszőleges sémák és lekérdezheti a dokumentumok egy gyűjteményen belül anélkül, hogy semmilyen sémát, illetve másodlagos indexek előzetes megfizetése esetén. A gyűjtemény segítségével lekérheti a [Azure Cosmos adatbázis SQL-szintaxis hivatkozás](https://msdn.microsoft.com/library/azure/dn782250.aspx), pedig a gazdag hierarchikus, relációs és térbeli operátorokat és bővíthetőséget JavaScript-alapú felhasználó által megadott függvények biztosítja. A JSON-szintaxis lehetővé teszi, hogy a fa csomópontjai címkék JSON-dokumentumok fákként modellezési. Ez az SQL API automatikus indexelési technikái, valamint az Azure Cosmos DB SQL dialektusa szerint kihasználni. Az SQL lekérdező nyelve három fő szempontjait foglalja magában:   

1. A lekérdezési műveletek, amelyek kapcsolódnak a faszerkezetben, beleértve a hierarchikus lekérdezések és leképezések természetes egy kis készletét. 
2. Egy relációs műveleteket, köztük a összeállításban, szűrő, leképezések, összesítések és automatikus illesztések részét. 
3. Tiszta JavaScript-alapú, amelyek használhatók a felhasználó által megadott függvények (1) és (2).  

Az Azure Cosmos DB lekérdezési modelljét megkísérli egyensúlyt biztosítanak a funkciót, hatékonyságát és egyszerűség között. Az Azure Cosmos DB adatbázismotor natív módon lefordítja a, és az SQL lekérdezési utasítás végrehajtása. A gyűjtemény segítségével lekérheti a [REST API-k](/rest/api/documentdb/) vagy annak bármelyik a [ügyfél SDK-k](sql-api-sdk-dotnet.md). A .NET SDK tartalmaz egy LINQ szolgáltatónál.

> [!TIP]
> Próbálja ki az SQL API-t, és az adatkészletet az SQL-lekérdezések futtatásához a [Tesztlekérdezéseket](https://www.documentdb.com/sql/demo).
> 
> 

## <a name="multi-document-transactions"></a>Többdokumentumos tranzakció
Adatbázis-tranzakció biztonságos és megbízható programozási modellt biztosít a való egyidejű adatokban bekövetkező változásokat. RDBMS, az üzleti logika írását a hagyományos módon, hogy írási **tárolt eljárások** és/vagy **eseményindítók** töltse az adatbázis-kiszolgáló tranzakciós végrehajtásához. A RDBMS az alkalmazás-programozó két különböző programnyelveken kezeléséhez szükséges: 

* A (nem tranzakciós) alkalmazás programozási nyelv (például JavaScript, Python, C#, Java, stb.)
* T-SQL, a tranzakciós programozási nyelv, amely natív módon futtatja az adatbázis

A részletes elkötelezettségének JavaScript és JSON közvetlenül az adatbázis motorján belül, alapján a Azure Cosmos DB egy intuitív programozási modellt biztosít, végrehajtás alatt álló JavaScript-alkalmazáslogikát alapú közvetlenül a gyűjtemények, tárolt eljárások szempontjából a és eseményindítók. Ez lehetővé teszi, hogy mind a következők:

* Párhuzamossági hatékony végrehajtásának szabályozásához helyreállítási, automatikus a JSON objektumgrafikonok közvetlenül a az adatbázismotor indexelése
* Természetesen kifejező folyamatábrán, változó hatókörének, hozzárendelése és kivételkezelő primitívek az adatbázis tranzakcióihoz közvetlenül tekintetében a JavaScript programozási nyelv integrációja

A JavaScript-logika regisztrálva, a gyűjtemény szintjén majd adhat ki az adott gyűjtemény a dokumentumok a Helyadatbázis-műveletek. Az Azure Cosmos DB implicit módon becsomagolja a JavaScript-alapú tárolt eljárások és eseményindítók belül egy környezeti ACID-tranzakciókat pillanatkép-elkülönítéssel egy gyűjteményen belül dokumentumok között. Ha a futtatása során a JavaScript kivételt jelez, a teljes tranzakció megszakad. Az eredményül kapott programozási modell nagyon egyszerű még hatékony. JavaScript fejlesztők "tartós" programozási modellt kap a szalagtár primitívek, valamint a megszokott nyelvi szerkezetek továbbra is használatakor.   

JavaScript végrehajtása közvetlenül az adatbázismotor ugyanazt a címtartományt, a pufferkészlet belül teszi lehetővé teszi, hogy performant és adatbázis-művelet a dokumentumokon végzett gyűjtemény tranzakciós végrehajtását. Cosmos-adatbázis adatbázis-kezelő lehetővé teszi a JSON mély kötelezettségvállalás továbbá JavaScript megszünteti az a típus rendszerek alkalmazás és az adatbázis bármely impedancia eltérő.   

Gyűjtemény létrehozása után is regisztrált tárolt eljárások, eseményindítók és felhasználó által megadott függvények egy gyűjtemény használja a [REST API-k](/rest/api/documentdb/) vagy annak bármelyik a [ügyfél SDK-k](sql-api-sdk-dotnet.md). A regisztrációt követően hivatkoznak, és azokat hajtható végre. Vegye figyelembe a következő tárolt eljárás teljesen JavaScript nyelven írt, az alábbi kódot két argumentummal (könyv nevét és Szerző neve) és új dokumentum létrehozása, lekérdezi egy dokumentumot, és frissíti azt – összes egy implicit ACID-tranzakción belül. A végrehajtás során bármikor Ha a JavaScript kivételt vált ki, a teljes tranzakció megszakítása.

    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();        
        var collectionLink = collectionManager.getSelfLink()

        // create a new document.
        collectionManager.createDocument(collectionLink,
            {id: name, author: author},
            function(err, documentCreated) {
                if(err) throw new Error(err.message);

                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function(err, matchingDocuments) {
                        if(err) throw new Error(err.message);

                        context.getResponse().setBody(matchingDocuments.length);

                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don’t need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);   
                        }
                    })
            })
    };

Az ügyfél "elküldhet a" via HTTP POST tranzakciós végrehajtásához az adatbázishoz a fenti JavaScript-logika. HTTP-metódus használatával kapcsolatos további információkért lásd: [Azure Cosmos DB erőforrások RESTful interakció](https://msdn.microsoft.com/library/azure/mt622086.aspx). 

    client.createStoredProcedureAsync(collection._self, {id: "CRUDProc", body: businessLogic})
       .then(function(createdStoredProcedure) {
            return client.executeStoredProcedureAsync(createdStoredProcedure.resource._self,
                "NoSQL Distilled",
                "Martin Fowler");
        })
        .then(function(result) {
            console.log(result);
        },
        function(error) {
            console.log(error);
        });


Figyelje meg, hogy az adatbázis natív módon együttműködik a JSON és a JavaScript, mert nincs nincs rendszer eltérés, "OR leképezés" vagy kód generálása magic szükséges.   

Tárolt eljárások és eseményindítók kommunikálni egy gyűjtemény és a dokumentumok egy gyűjtemény egy jól meghatározott hálózatiobjektum-modellje, amely az aktuális gyűjtemény környezetben keresztül.  

Az SQL API-gyűjteményére is létrehozható, törölt, olvassa el, vagy felsorolt segítségével egyszerűen a [REST API-k](/rest/api/documentdb/) vagy annak bármelyik a [ügyfél SDK-k](sql-api-sdk-dotnet.md). Az SQL API-t mindig vagy a metaadat-gyűjtemény lekérdezése az erős konzisztencia biztosítja. A gyűjtemény törlése automatikusan biztosítja, hogy a dokumentumok, a mellékleteket, a tárolt eljárások, eseményindítók sem tudja már használni, és az abban szereplő felhasználó által megadott függvények.   

## <a name="stored-procedures-triggers-and-user-defined-functions-udf"></a>Tárolt eljárások, eseményindítók és felhasználó definiált függvény (UDF)
Az előző szakaszban leírtak írhat az alkalmazáslogikát, hogy közvetlenül az adatbázis motorján belül tranzakción belül futtatni. Az alkalmazáslogika teljes egészében a JavaScript írhatók, és a tárolt eljárás, eseményindító vagy egy UDF modellezhető. A tárolt eljárás vagy eseményindító JavaScript-kód beszúrása, cserélje le, olvassa el vagy lekérdezni a dokumentumok a gyűjteményen belül. Másrészről, egy UDF belül a JavaScript nem lehet beszúrni, cserélje le, vagy törölhetnek dokumentumokat. Felhasználó által megadott függvények számba venni a lekérdezés eredményhalmazából dokumentumok, és előállít egy másik eredményhalmaz. A több-bérlős Azure Cosmos DB kényszeríti a szigorú foglalásalapú erőforrás cégirányítási. Minden egyes tárolt eljárás, eseményindító vagy egy UDF lekérdezi a rögzített quantum az operációs rendszer-erőforrás a munkájuk elvégzéséhez. Továbbá a tárolt eljárások, eseményindítók és felhasználó által megadott függvények külső JavaScript szalagtárak szemben nem lehet csatolni, és is feketelistára teszi a számukra kiosztott erőforrás költségvetések túllépése esetén. Regisztrálja, a tárolt eljárások, eseményindítók és felhasználó által megadott függvények gyűjtemény regisztrációját a REST API-k használatával.  Regisztráláskor tárolt eljárás, eseményindító vagy egy UDF, előre összeállított és a rendszer bájt kódú, amelyek később hajtsa végre. A következő ssection illustrateshow az Azure Cosmos DB JavaScript SDK segítségével regisztrálja, végrehajtási és regisztrációjának törlése a tárolt eljárás, eseményindító és egy UDF. A JavaScript SDK egy egyszerű burkoló felett. a [REST API-k](/rest/api/documentdb/). 

### <a name="registering-a-stored-procedure"></a>A tárolt eljárás regisztrálása
Regisztrációs tárolt eljárás egy új tárolt eljárás erőforrás egy gyűjtemény HTTP POST használatával hoz létre.  

    var storedProc = {
        id: "validateAndCreate",
        body: function (documentToCreate) {
            documentToCreate.id = documentToCreate.id.toUpperCase();

            var collectionManager = getContext().getCollection();
            collectionManager.createDocument(collectionManager.getSelfLink(),
                documentToCreate,
                function(err, documentCreated) {
                    if(err) throw new Error('Error while creating document: ' + err.message;
                    getContext().getResponse().setBody('success - created ' + 
                            documentCreated.name);
                });
        }
    };

    client.createStoredProcedureAsync(collection._self, storedProc)
        .then(function (createdStoredProcedure) {
            console.log("Successfully created stored procedure");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-stored-procedure"></a>A tárolt eljárás végrehajtása
A következő tárolt eljárás végrehajtása egy HTTP POST meglévő tárolt eljárás erőforrás ellen úgy, hogy a kérelem törzsében szereplő eljárás paraméterek kiállításával történik.

    var inputDocument = {id : "document1", author: "G. G. Marquez"};
    client.executeStoredProcedureAsync(createdStoredProcedure.resource._self, inputDocument)
        .then(function(executionResult) {
            assert.equal(executionResult, "success - created DOCUMENT1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-stored-procedure"></a>A regisztráció megszüntetését tárolt eljárás
Beállításjegyzékből való törlésekor a tárolt eljárás egy HTTP DELETE elleni meglévő tárolt eljárás erőforrás kiállításával egyszerűen történik.   

    client.deleteStoredProcedureAsync(createdStoredProcedure.resource._self)
        .then(function (response) {
            return;
        }, function(error) {
            console.log("Error");
        });


### <a name="registering-a-pre-trigger"></a>Regisztrálás előtti eseményindító
Új eseményindító erőforrás létrehozása egy gyűjteményen keresztül HTTP POST regisztrációs eseményindító végezhető el. Megadhatja, ha az eseményindító nem előre, vagy a feladás egy vagy több eseményindító és a műveletnek a típusát (például létrehozása, Replace, Delete vagy összes) társított lehet.   

    var preTrigger = {
        id: "upperCaseId",
        body: function() {
                var item = getContext().getRequest().getBody();
                item.id = item.id.toUpperCase();
                getContext().getRequest().setBody(item);
        },
        triggerType: TriggerType.Pre,
        triggerOperation: TriggerOperation.All
    }

    client.createTriggerAsync(collection._self, preTrigger)
        .then(function (createdPreTrigger) {
            console.log("Successfully created trigger");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-pre-trigger"></a>Egy előtti eseményindító végrehajtása
Egy eseményindító végrehajtása egy meglévő eseményindító nevét megadó a FELADÁS egy vagy több/PUT vagy DELETE kérelmet egy dokumentum erőforrás a kérelem fejléce időpontjában történik.  

    client.createDocumentAsync(collection._self, { id: "doc1", key: "Love in the Time of Cholera" }, { preTriggerInclude: "upperCaseId" })
        .then(function(createdDocument) {
            assert.equal(createdDocument.resource.id, "DOC1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-pre-trigger"></a>A regisztráció megszüntetését előtti eseményindító
Egy eseményindító regisztrációját kiadása egy HTTP DELETE eseményindító erőforrással ellen használatával egyszerűen történik.  

    client.deleteTriggerAsync(createdPreTrigger._self);
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

### <a name="registering-a-udf"></a>Egy UDF regisztrálása
Egy UDF-regisztráció UDF új erőforrás létrehozása egy gyűjteményen keresztül HTTP POST végezhető el.  

    var udf = { 
        id: "mathSqrt",
        body: function(number) {
                return Math.sqrt(number);
        },
    };
    client.createUserDefinedFunctionAsync(collection._self, udf)
        .then(function (createdUdf) {
            console.log("Successfully created stored procedure");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-udf-as-part-of-the-query"></a>A lekérdezés részeként egy UDF végrehajtása
Egy UDF adható meg az SQL-lekérdezés részeként pedig a rendszer a core kiterjeszteni úgy [SQL lekérdező nyelve](sql-api-sql-query-reference.md).

    var filterQuery = "SELECT udf.mathSqrt(r.Age) AS sqrtAge FROM root r WHERE r.FirstName='John'";
    client.queryDocuments(collection._self, filterQuery).toArrayAsync();
        .then(function(queryResponse) {
            var queryResponseDocuments = queryResponse.feed;
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-udf"></a>A regisztráció megszüntetését olyan UDF-ben
Beállításjegyzékből való törlésekor a UDF egy HTTP DELETE elleni meglévő UDF erőforrás kiállításával egyszerűen történik.  

    client.deleteUserDefinedFunctionAsync(createdUdf._self)
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

Bár a fenti kódtöredékek bemutatta a regisztrációs (POST), (PUT) regisztrációjának, Olvasás/lista (GET) és végrehajtási (POST) keresztül a [JavaScript SDK](https://github.com/Azure/azure-documentdb-js), használhatja a [REST API-k](/rest/api/documentdb/) vagy más [ügyfél SDK-k](sql-api-sdk-dotnet.md). 

## <a name="documents"></a>Dokumentumok
Akkor is beszúrása, cserélje le, törlése, olvasása, számbavétele és lekérdezni egy gyűjtemény tetszőleges JSON-dokumentumokat. Azure Cosmos-adatbázis nem határozza meg, semmilyen sémát, és nem igényel másodlagos indexek támogatása érdekében egy gyűjtemény dokumentumok lekérdezését. A dokumentum maximális mérete 2 MB.   

Folyamatban egy valóban megnyitott adatbázis-szolgáltatás, Azure Cosmos DB nem találjon ki semmilyen speciális adattípusok (például: dátum idő) vagy a JSON-dokumentumokat az adott kódolások. Azure Cosmos-adatbázis nem igényel semmilyen különleges JSON egyezmények kodifikálni a különböző dokumentumok; közötti kapcsolatok az Azure Cosmos-adatbázis SQL-szintaxis szolgáló lekérdezés és a projekt dokumentumok különleges jegyzeteket vagy dokumentumok közötti kapcsolatok kodifikálni kell nélkül megkülönböztető tulajdonságok nagyon hatékony hierarchikus és relációs lekérdezés biztosít.  

Mint minden más erőforrásnál dokumentumok hozhatók létre, cseréje esetén törlése, olvasása, számba, és könnyen használatával a REST API-k vagy annak bármelyik lekérdezése a [ügyfél SDK-k](sql-api-sdk-dotnet.md). Dokumentum törlése azonnal területet szabadít fel a megfelelő összes beágyazott melléklet kvótát. A dokumentumok olvasási konzisztenciát mértékét az adatbázis-fiókot a konzisztencia-házirend következik. Ez a házirend attól függően, hogy az adatok konzisztenciájának követelményeinek, az alkalmazás kérelem alapon felülbírálható. Dokumentumok lekérdezésekor az olvasási konzisztenciával követi a indexelési mód beállítása a gyűjteményben. A "konzisztens" Ez a következő a fiók konzisztencia házirend. 

## <a name="attachments-and-media"></a>Mellékletek és az adathordozó
Az Azure Cosmos DB tárolását teszi bináris blobok/media vagy Azure Cosmos DB (legfeljebb 2 GB fiókonként) vagy a saját távoli médiatárbeli. Lehetővé teszi egy adathordozó melléklet nevű különleges dokumentum tekintetében a metaadatokat képviseli. A melléklet Azure Cosmos DB egy különleges (JSON) dokumentumot, amely a media/blob máshol tárolt hivatkozik. Csatolmány egyszerűen egy különleges dokumentumot, amely a metaadatokat (például hely, Szerző stb.) egy olyan távoli media storage-ban tárolt adathordozó rögzíti. 

Fontolja meg egy közösségi olvasási alkalmazás, amely Azure Cosmos-adatbázis segítségével tárolja a szabadkézi széljegyzeteket, és megjegyzéseket, beleértve a metaadatok kiemeli, könyvjelzők, minősítések, stb. tartozó, egy adott felhasználó e-könyv kedveli/dislikes.   

* A könyv maga a tartalom vagy tárolódik a média-tároló vagy egy távoli médiatárbeli Azure Cosmos DB adatbázisfiók részeként érhető el. 
* Az alkalmazás minden felhasználó tárolhatjuk egy különálló dokumentumként – például /colls/joe/docs/book1 által hivatkozott dokumentum Füzet1 Joe metaadatait tárolja. 
* A mellékletek mutat egy felhasználó egy adott könyv lapjain például a megfelelő dokumentum tárolt tartalom, /colls/joe/docs/book1/chapter1, /colls/joe/docs/book1/chapter2 stb. 

A fenti példák rövid azonosítók segítségével átadja az erőforrás-hierarchiában. Erőforrások keresztül egyedi erőforrás-azonosítókat megtalálhatja a REST API-kon keresztül érhetők el. 

Az adathordozó Azure Cosmos DB által felügyelt a melléklet _media tulajdonságának hivatkozik az adathordozó által az URI. Az Azure Cosmos DB biztosíthatja, hogy az adathordozó a szemétgyűjtési gyűjtése, ha az összes fennmaradó hivatkozást a rendszer eldobja. Azure Cosmos DB automatikusan hoz létre a mellékletet, ha az új adathordozó feltöltött tölti fel a _media úgy, hogy az újonnan hozzáadott adathordozó mutasson. Ha az adathordozó tárolása egy távoli blob-tároló felügyeli azt (például a OneDrive, Azure Storage stb dropbox-bA), az adathordozó hivatkozni továbbra is használhatja a mellékleteket. Ebben az esetben fog létrehozni a mellékletet, és feltölti a _media tulajdonsága.   

Összes többi erőforrása is hozható létre mellékleteket, cserélni, törlése, olvasása vagy számba egyszerűen a REST API-k vagy bármely, az ügyfél SDK-k használatával. Csakúgy, mint a dokumentumok, az olvasási konzisztenciával szintű mellékletek következik a konzisztencia-házirend az adatbázis-fiókot. Ez a házirend attól függően, hogy az adatok konzisztenciájának követelményeinek, az alkalmazás kérelem alapon felülbírálható. Mellékletek lekérdezésekor az olvasási konzisztenciával követi az indexelési mód beállítása a gyűjtemény. A "konzisztens" Ez a következő a fiók konzisztencia házirend. 
 

## <a name="users"></a>Felhasználók
Egy Azure Cosmos DB felhasználói engedélyek csoportosítása logikai névterének jelöli. Egy Azure Cosmos DB felhasználói az identity management rendszer vagy egy előre megadott alkalmazás-szerepkör egy felhasználó lehet rendelve. Az Azure Cosmos DB a felhasználó egyszerűen engedélyekkel az adatbázis csoportba absztrakciós jelöli.   

A több-bérlős megvalósítása az alkalmazásban, az Azure Cosmos Adatbázisba, amely megfelel a tényleges felhasználók vagy a bérlők számára az alkalmazás felhasználók hozhat létre. Ezután létrehozhat egy adott felhasználó engedélyeit, hogy a hozzáférés-vezérlés keresztül különböző gyűjtemények, dokumentumok, a mellékletek, stb.   

Az alkalmazások a felhasználó növekedési méretezést van, az adatok különböző módokon shard is elfogadja. A felhasználók az alábbiak szerint is modell:   

* Minden felhasználói adatbázis van leképezve.
* Minden felhasználó egy gyűjtemény van leképezve. 
* Több felhasználó dokumentumokat nyissa meg a kijelölt gyűjteményhez. 
* Több felhasználó dokumentumokat olyan készletek Ugrás.   

A megadott horizontális skálázási stratégia függetlenül választja, a tényleges felhasználók minta Azure Cosmos DB adatbázisban felhasználóként, és az egyes felhasználók részletes engedélyeket társítani.  

![Felhasználói gyűjtemények][3]  
**Horizontális azokat a stratégiákat és modellezési felhasználók**

Más erőforrások, például felhasználói Azure Cosmos DB hozhatók létre, cserélni, törlése, olvassa el, vagy számba egyszerűen a REST API-k vagy bármelyik az ügyfél SDK-k használatával. Cosmos. Azure-adatbázis mindig vagy egy felhasználói erőforrás metaadatait lekérdezése az erős konzisztencia biztosítja. Válassza, hogy nem tud hozzáférni az abban szereplő engedélyeit biztosítja, hogy automatikusan törli a felhasználó érdemes. Annak ellenére, hogy az Azure Cosmos DB úgy szabadít fel a engedélyeit a kvóta a törölt felhasználói a háttérben részeként, a törölt engedélyek érhető el azonnal újra lesz szükség.  

## <a name="permissions"></a>Engedélyek
Access control szempontból, erőforrások, például adatbázis-fiókokat, adatbázisok, felhasználók és engedéllyel minősülnek *felügyeleti* erőforrásokat, mivel ezek a rendszergazdai engedélyek szükségesek. Másrészről, erőforrások, például a gyűjtemények, dokumentumok, a mellékleteket, tárolt eljárások, eseményindítók, és a felhasználó által megadott függvények alapján egy adott adatbázisnak hatókörű, és figyelembe veendő *alkalmazás-erőforrásokat*. A két típusú erőforrások és a szerepköröket, amelyek elérhet (azaz a rendszergazdai és felhasználói) megfelelő, a használt engedélyezési modellt meghatározása kétféle *hívóbetűk*: *főkulcs* és  *Az erőforráskulcs*. A főkulcs a következő adatbázisfiókot része, és a rendszer átadja a fejlesztői (vagy a rendszergazda) ki van kiépítés az adatbázis-fiókot. A főkulcs szemantikájú rendszergazda, abban, hogy a felügyeleti és a alkalmazás erőforrásokhoz való hozzáférés engedélyezésére használható. Ezzel szemben egy erőforrás kulcsa a részletes elérési kulcsot, amely lehetővé teszi a hozzáférést egy *adott* alkalmazás erőforrás. Ebből kifolyólag rögzíti a felhasználó az adatbázis és az engedélyek a felhasználó rendelkezik-e egy adott erőforrás (például gyűjteményt, a dokumentum, melléklet, tárolt eljárás, eseményindító vagy UDF) közötti kapcsolat.   

A csak egy erőforrás-kulcs beszerzése, egy adott felhasználói engedélyt erőforrás létrehozása. Vegye figyelembe, hogy létrehozásához vagy engedély beolvasni, főkulcs biztosítani kell a hitelesítési fejléc. Engedély erőforrás kötelékek az erőforrás, a hozzáférés és a felhasználó. A felhasználó engedélye erőforrás létrehozása után csak kell a társított erőforráskulcs ahhoz, hogy a megfelelő erőforrás eléréséhez jelenthet. Egy erőforrás-kulcsot, ezért a logikai és kompakt megjelenítése az engedély erőforrás tekinthetők.  

Mint minden más erőforrások, az Azure Cosmos Adatbázisba engedélyek hozhatók létre, cserélni, törlése, olvassa el vagy számba egyszerűen a REST API-k vagy bármely, az ügyfél SDK-k használatával. Azure Cosmos DB mindig nyújt erős konzisztenciát biztosít a olvasása vagy kérdez le a metaadatokat az engedély. 

## <a name="next-steps"></a>Következő lépések
További tudnivalókat a HTTP-parancsokat kell használnia, erőforrásokkal [Azure Cosmos DB erőforrások RESTful interakció](https://msdn.microsoft.com/library/azure/mt622086.aspx).

[1]: media/sql-api-resources/resources1.png
[2]: media/sql-api-resources/resources2.png
[3]: media/sql-api-resources/resources3.png

