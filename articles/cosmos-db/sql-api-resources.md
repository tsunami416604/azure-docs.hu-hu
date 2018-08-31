---
title: Az Azure Cosmos DB erőforrás-modellje és fogalmai |} A Microsoft Docs
description: További információ az Azure Cosmos DB hierarchikus modell adatbázisok, gyűjtemények, felhasználó által definiált függvény (UDF), dokumentumok, kezelheti az erőforrásokat, és további engedélyekkel.
keywords: Hierarchikus modell, cosmosdb, azure, Microsoft azure
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: rafats
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3ba02e7760d7400b5168a902415f16c4b276b3a7
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43287953"
---
# <a name="azure-cosmos-db-hierarchical-resource-model-and-core-concepts"></a>Az Azure Cosmos DB hierarchikus erőforrás-modellje és alapfogalmai

Az Azure Cosmos DB-kezelő adatbázis-entitások nevezzük **erőforrások**. A logikai URI egyedileg azonosítja az egyes erőforrások. A szabványos HTTP-műveletek, a kérelem/válasz fejlécei és állapotkódok erőforrásaival is interakcióba. 

Ez a cikk az alábbi kérdéseket:

* Mi az Azure Cosmos DB erőforrás-modellje?
* Mik a rendszer meghatározott erőforrások figyelésekor felhasználó által definiált erőforrásokat?
* Hogyan oldja meg egy erőforrást?
* Hogyan működnek a gyűjtemények?
* Hogyan működik a tárolt eljárások, eseményindítók és felhasználó által megadott funkciókat (UDF)?

## <a name="hierarchical-resource-model"></a>Hierarchikus erőforrásmodell
Ahogy a következő diagram azt ábrázolja, az Azure Cosmos DB hierarchikus **erőforrásmodell** beállítja az adatbázis-fiókja alatt, logikai és stabil URI-n keresztül minden egyes címmel rendelkező erőforrások áll. Erőforráscsoport neve egy **hírcsatorna** ebben a cikkben. 

> [!NOTE]
> Az Azure Cosmos DB egy nagy hatékonyságú TCP protokoll, amely egyben RESTful a kommunikációt a modellben, kínál keresztül érhető el a [SQL .NET ügyfél API-JÁNAK](sql-api-sdk-dotnet.md).
> 
> 

![Az Azure Cosmos DB hierarchikus erőforrásmodell][1]  
**Hierarchikus erőforrásmodell**   

Az erőforrásokhoz való használatának megkezdéséhez, kell [adatbázisfiók létrehozása](create-sql-api-dotnet.md) Azure-előfizetése. Az adatbázisfiók állhat egy **adatbázisok**, mindegyik több **gyűjtemények**, minden egyes, amelyek ezután tartalmaznak **tárolt eljárások, elindítja, felhasználói függvényeket, dokumentumokat és kapcsolódó a mellékletek**. Egy adatbázis is vannak társítva **felhasználók**, minden egyes vannak beállítva **engedélyek** gyűjtemények, tárolt eljárások, eseményindítók, felhasználói függvények, dokumentumok és mellékletek eléréséhez. Adatbázisok, felhasználók, engedélyek és gyűjtemények rendszer által meghatározott, jól ismert sémákkal rendelkező erőforrások, amelyek a dokumentumok és mellékletek tetszőleges, felhasználó által definiált JSON-tartalmak tartalmaz.  

| Erőforrás | Leírás |
| --- | --- |
| Adatbázisfiók |Egy adatbázis-fiók társítva az adatbázisok és a egy meghatározott méretű blob storage között a mellékleteket. Létrehozhat egy vagy több adatbázis fiók Azure-előfizetése. További információért látogasson el a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/cosmos-db/). |
| Adatbázis |Egy adatbázis a dokumentumtároló gyűjtemények között particionált logikai tárolói. Emellett akkor is a felhasználók tárolójába kerülnek. |
| Felhasználó |A logikai névtér sorolására alkalmazott engedélyeket. |
| Engedély |Egy adott erőforráshoz való hozzáférés a felhasználóhoz társított engedélyezési jogkivonatot. |
| Gyűjtemény |A gyűjtemény egy JSON-dokumentumokat és a kapcsolódó JavaScript-alkalmazáslogikát tartalmazó tároló. A gyűjtemények egy vagy több partícióra/kiszolgálóra is kiterjedhetnek, valamint gyakorlatilag korlátlan mennyiségű tárterület vagy átviteli sebesség kezelésére méretezhetők. |
| Tárolt eljárás |Az alkalmazáslogika regisztrálva a gyűjtemény és tranzakciós szempontból végrehajtott belül az adatbázismotor, JavaScript nyelven. |
| Eseményindító |Az alkalmazáslogika végrehajtása előtt vagy után vagy egy insert javascriptben írt cserélje le, vagy törlési művelet. |
| AZ UDF |Az alkalmazáslogika JavaScript nyelven. UDF-EK engedélyezése egy egyéni lekérdezés operátor modellezheti, és ezáltal kiterjesztése az alapvető SQL API lekérdezési nyelv. |
| Dokumentum |Felhasználó által definiált (tetszőleges) JSON-tartalmak. Alapértelmezés szerint nincs séma kell definiálni, sem a másodlagos indexekkel kellene foglalkoznia kell adni a gyűjteményhez hozzáadott összes dokumentumot. |
| Melléklet |A melléklet hivatkozások és a külső blob/media metaadatokat tartalmazó speciális dokumentumban találhatók. A fejlesztői választhat a blob, Cosmos DB által kezelt vagy tárolja egy külső blob-szolgáltatónál, például a OneDrive, Dropbox stb. |

## <a name="system-vs-user-defined-resources"></a>A rendszer és a felhasználó által definiált erőforrásokat
Erőforrások, például adatbázisfiókhoz, adatbázisok, gyűjtemények, felhasználók, engedélyek, tárolt eljárások, eseményindítók és felhasználói függvények – minden rendelkezik rögzített sémát, és rendszer-erőforrásokat az úgynevezett. Ezzel szemben az erőforrások, például dokumentumok és mellékletek nincs korlátozás a séma rendelkezik, és példák felhasználó által definiált erőforrásokat. Cosmos DB, a rendszer és a felhasználó által definiált erőforrásokat is jelölt és standard-kompatibilis JSON-ként felügyelt. Az összes erőforrást, a rendszer vagy a felhasználó által definiált, a következő közös tulajdonságokkal kell rendelkeznie:

> [!NOTE]
> Az összes rendszer által létrehozott tulajdonság az erőforrás van fűzve előtagként a saját JSON-reprezentációval aláhúzásjelet (_).
> 
> 

<table>
    <tbody>
        <tr>
            <td valign="top"><p><strong>Tulajdonság</strong></p></td>
            <td valign="top"><p><strong>Állítható be felhasználó vagy rendszer által létrehozott?</strong></p></td>
            <td valign="top"><p><strong>Cél</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>_rid</p></td>
            <td valign="top"><p>A rendszer által létrehozott</p></td>
            <td valign="top"><p>A rendszer által létrehozott, az erőforrás egyedi, és a hierarchikus azonosítója</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_etag</p></td>
            <td valign="top"><p>A rendszer által létrehozott</p></td>
            <td valign="top"><p>Az optimista egyidejűség-vezérlés szükséges erőforrás ETag</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_ts</p></td>
            <td valign="top"><p>A rendszer által létrehozott</p></td>
            <td valign="top"><p>Az erőforrás utolsó frissítés időbélyege</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_self</p></td>
            <td valign="top"><p>A rendszer által létrehozott</p></td>
            <td valign="top"><p>Az erőforrás egyedi címezhető URI</p></td>
        </tr>
        <tr>
            <td valign="top"><p>id</p></td>
            <td valign="top"><p>Vagy</p></td>
            <td valign="top"><p>Az erőforrás (együtt az egyazon partíciókulcs-értékkel), felhasználó által megadott egyedi név. Ha a felhasználó nem ad meg azonosítót, egy azonosító-e a rendszer által létrehozott</p></td>
        </tr>
    </tbody>
</table>

### <a name="wire-representation-of-resources"></a>Átviteli megjelenítésűre erőforrások
A cosmos DB nem írja elő minden olyan szellemi tulajdont képező bővítmények a JSON standard vagy speciális kódolásokat; standard megfelelő JSON-dokumentumok is működik.  

### <a name="addressing-a-resource"></a>Erőforrás-címzés
Minden erőforrás URI-címezhető. Értékét a **_self** erőforrás a tulajdonság adja meg az erőforrás relatív URI. Az URI formátumát áll a /\<hírcsatorna\>/ {_rid} elérési út szegmensek:  

| A _self értékét | Leírás |
| --- | --- |
| /dbs |Az adatbázisok adatbázis-fiókja alatt hírcsatorna |
| /dbs/ {%{dbname/} |Az érték a(z) %{dbname/} egyező azonosítójú adatbázis |
| {%{dbname/} /dbs/ /colls/ |Hírcsatorna csoportban egy adatbázis-gyűjtemények |
| {%{dbname/} /dbs/ /colls/ {collName} |Adatgyűjtés és a egy {collName} értékével egyező azonosító |
| {%{dbname/} /dbs/ /colls/ {collName} / docs |A gyűjtemény alatt dokumentumok hírcsatorna |
| /dbs/ {%{dbname/} {collName} /colls/ /docs/ {dokumentumazonosító} |A következő érték: {doc} egyező azonosítójú dokumentálása |
| {%{dbname/} /dbs/ /users/ |Hírcsatorna a felhasználói csoportban egy adatbázis |
| /dbs/{dbName}/users/{userId} |A következő érték: {user} egyező azonosítójú felhasználó |
| /dbs/{dbName}/users/{userId}/permissions |Hírcsatorna engedélyekkel a felhasználó alapján |
| /dbs/{dbName}/users/{userId}/permissions/{permissionId} |Egy megfelelő az érték {engedély} azonosítóval rendelkező engedély |

Az egyes erőforrások elérhetővé az id tulajdonság egyedi felhasználói névvel rendelkezik. Megjegyzés: a dokumentumok, ha a felhasználó nem ad meg egy azonosító és az SDK-k automatikusan létrehozhat egy egyedi azonosítót a dokumentumot. Az azonosító egy olyan felhasználó által definiált karakterlánc, legfeljebb 256 karakter lehet, amely egy adott szülőerőforrás keretén belül egyedi. 

Minden erőforrás rendelkezik is egy rendszer által létrehozott hierarchikus erőforrás-azonosítója (más néven az RID-verem), amely _rid tulajdonságon keresztül érhető el. A relatív AZONOSÍTÓK kódolja a teljes hierarchia, hogy egy adott erőforrás és egy kényelmes belső ábrázolás hivatkozási integritás elosztott módon használt. A relatív AZONOSÍTÓK belül az adatbázisfiók egyedi, és belsőleg használatos Cosmos DB által anélkül, hogy a partíció közötti keresések hatékony útválasztást. A _self és _rid tulajdonságok értékei alternatív és a canonical-alapú ábrázolását egy erőforrást. 

A REST API-k támogatják az erőforrások és -azonosítóját, valamint a _rid tulajdonságok kérelmek útválasztást.

## <a name="database-accounts"></a>Adatbázis-fiókoknál
Telepíthet egy vagy több Cosmos DB adatbázis-fiók Azure-előfizetése.

Létrehozhat és kezelése az Azure Portalon, a Cosmos DB-adatbázis fiókok [ http://portal.azure.com/ ](https://portal.azure.com/). Létrehozásába és kezelésébe az adatbázisfiók rendszergazdai hozzáférésre van szüksége, és csak végezhető el az Azure-előfizetéshez. 

### <a name="database-account-properties"></a>Adatbázis-fiók tulajdonságai
Üzembe helyezése és kezelése az adatbázisfiók részeként konfigurálja, és olvassa el a következő tulajdonságokkal:  

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Tulajdonság neve</strong></p></td>
            <td valign="top"><p><strong>Leírás</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Konzisztencia-szabályzat</p></td>
            <td valign="top"><p>Ezzel a tulajdonsággal beállíthatja az adatbázis-fiókban a gyűjtemények alapértelmezett konzisztencia szintjét. A konzisztencia szintjét, a [x-ms-konzisztencia-szintű] kérelem fejléce használatával egy kérelem alapon felül lehet bírálni. <p><p>Ez a tulajdonság csak vonatkozik a <i>erőforrások felhasználó által definiált</i>. Meghatározott erőforrások támogatására vannak konfigurálva az összes rendszer olvasások és lekérdezésekkel erős konzisztencia.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Hitelesítési kulcsok</p></td>
            <td valign="top"><p>Az elsődleges és másodlagos fő- és a csak olvasható kulcsok, amelyek az adatbázis-fiók összes rendszergazdai hozzáférést biztosítanak.</p></td>
        </tr>
    </tbody>
</table>

Azon kívül, konfigurálása és kezelése a fiókot az Azure Portalról, szoftveresen is létrehozhat és Cosmos DB-adatbázisfiókhoz használatával kezelni a [Azure Cosmos DB – REST API-k](/rest/api/cosmos-db/) valamint[ügyféloldali SDK-k](sql-api-sdk-dotnet.md).  

## <a name="databases"></a>Adatbázisok
Cosmos DB-hez egy vagy több gyűjtemények és felhasználó, olyan logikai tároló, a következő ábrán látható módon. Tetszőleges számú adatbázishoz egy Cosmos DB-adatbázisfiók ajánlat korlátai alapján hozhat létre.  

![Fiók és a gyűjtemények hierarchikus adatbázismodell][2]  
**Egy adatbázis olyan logikai tároló, a felhasználók és gyűjtemények**

Egy adatbázis korlátlan dokumentumtároló gyűjtemények belül particionált is tartalmazhat.

### <a name="elastic-scale-of-an-azure-cosmos-db-database"></a>Rugalmasan méretezhető, egy Azure Cosmos DB-adatbázis
Cosmos DB-hez alapértelmezés – és a néhány GB-os SSD-alapú dokumentumtárolási és a létesített átviteli sebesség petabájtnyi rugalmas. 

Ellentétben a hagyományos RDBMS-adatbázis a Cosmos DB adatbázis hatóköre nem egyetlen gépen. A Cosmos dB-ben, az alkalmazás növekszik, alkalmazásigények hozhat létre további gyűjteményeket, adatbázisok vagy mindkettőt. Sőt különböző első alkalmazásait Microsoft már használja az Azure Cosmos DB fogyasztói méretű dokumentumtárolási terabájtnyi való minden egyes tartalmazó ezer gyűjtemény létrehozása a rendkívül nagy méretű Azure Cosmos DB-adatbázisok által. Ön méretezhetősége révén akár egy adatbázis hozzáadásával vagy eltávolításával gyűjteményeket az alkalmazás méretezési követelményeinek. 

Tetszőleges számú vonatkozik az ajánlat adatbázisban lévő gyűjtemények is létrehozhat. Minden gyűjtemény, vagy (egy adatbázisban) gyűjteményt készletét, rendelkezik SSD biztonsági tárolási és átviteli sebesség kiosztott részéért, attól függően, a kiválasztott ajánlat.

Egy Azure Cosmos DB-adatbázis egy tárolót a felhasználók akkor is. Egy felhasználó, a kapcsolja, egy logikai névteret engedélyek egy készletét, amely minden részletre kiterjedő engedélyezési és a gyűjtemények, dokumentumok és mellékletek hozzáférést biztosít.  

Egyéb erőforrásokat az Azure Cosmos DB erőforrás-modellje, az adatbázisok létrehozhatók, cseréje esetén, törlése, olvasása, vagy számba használatával könnyedén vagy a [REST API-k](/rest/api/cosmos-db/) , illetve a [ügyféloldali SDK-k](sql-api-sdk-dotnet.md). Az Azure Cosmos DB garantálja a konzisztenciát az adatbázis-erőforrás a metaadatok lekérdezése vagy olvasása. Adatbázis törlése automatikusan biztosítja, hogy nem tud hozzáférni a gyűjtemények vagy az abban szereplő felhasználók.   

## <a name="collections"></a>Gyűjtemények
A Cosmos DB-gyűjtemények a JSON-dokumentumok tárolója. 

### <a name="elastic-ssd-backed-document-storage"></a>Rugalmas SSD-alapú dokumentum-tároló
Egy gyűjtemény belsőleg rugalmas – automatikusan növekszik és csökken, adja hozzá, vagy távolítsa el a dokumentumokat. Gyűjtemények logikai erőforrások, és a egy vagy több fizikai partíciók vagy a kiszolgálók is kiterjedhetnek. A gyűjteményhez társított partíciók számát, a tároló mérete alapján, a Cosmos DB és a a gyűjtemény vagy egy gyűjtemények a létesített átviteli sebesség határozza meg. Cosmos DB-ben minden partíció van társítva az SSD-alapú tárolási rögzített méretű, és magas rendelkezésre állás érdekében replikálva van. Partíció felügyeleti teljes körűen felügyelt Azure Cosmos DB által, és nem kell komplex kódot írnia, vagy a partíciók kezeléséhez. A cosmos DB-gyűjtemények **korlátlan** tárolási és átviteli sebesség tekintetében. 

### <a name="automatic-indexing-of-collections"></a>A gyűjtemények automatikus indexelése
Az Azure Cosmos DB egy valódi sémamentes adatbázis rendszer. Ez nem feltételez vagy igényel semmilyen sémát a JSON-dokumentumok esetében. Dokumentumok hozzáadása egy gyűjteményhez, az Azure Cosmos DB automatikusan indexeli őket és érhetők el, hogy a lekérdezés. Automatikus indexelést a dokumentumok anélkül, hogy a séma vagy másodlagos indexek az Azure Cosmos DB képesség kulcsfontosságú, és engedélyezve van, írásra optimalizált, a zárolás ingyenes és a napló strukturált index karbantartási technikákkal. Az Azure Cosmos DB támogatja a rendkívül gyors írások konzisztens lekérdezések szolgálatban mennyiségű. Dokumentum és az index tárolási rendszer kiszámítja az egyes gyűjtemények által felhasznált tárterület. Szabályozhatja, hogy a tárolás és teljesítmény kompromisszummal indexelő konfigurálásával az indexelési házirendet egy gyűjtemény társítva. 

### <a name="configuring-the-indexing-policy-of-a-collection"></a>Gyűjtemény indexelési házirend konfigurálása
Minden gyűjtemény indexelési házirend lehetővé teszi a teljesítmény- és tárolási kompromisszummal indexelő társított. A következő beállítások lesznek elérhetők az indexelő konfiguráció részeként:  

* Válassza ki, hogy a gyűjtemény automatikusan indexeli az összes dokumentumot, vagy nem. Alapértelmezés szerint automatikusan indexelt összes dokumentumot. Ha szeretné, kapcsolja ki az Automatikus indexelés és a szelektív ad hozzá az index csak bizonyos dokumentumokhoz. Ezzel szemben is külön-külön ki szeretne zárni csak bizonyos dokumentumokhoz. Ez az automatikus tulajdonság igaz vagy hamis értéket, az a gyűjtemény indexingPolicy lennie, és a [x-ms-indexingdirective] kérelem fejléce használatával beszúrásához, cseréje vagy dokumentum törlése közben érheti el.  
* Döntse el, vagy egyedi elérési utak vagy a dokumentumok mintákat kizárja az indexből. Érheti el a beállítás includedPaths és a gyűjtemény a indexingPolicy excludedPaths jelölik. Beállíthatja, hogy a tárolás és teljesítmény kompromisszummal adott elérésiút-minták a tartomány és a kivonatot lekérdezések. 
* Válasszon közötti szinkron (konzisztens), és aszinkron (lazy) index frissítéseit. Alapértelmezés szerint az index frissítése szinkron módon minden olyan insert, replace vagy a dokumentum a gyűjtemény törlése. Ez lehetővé teszi a konzisztencia szintjét, amely dokumentumot olvasások tartsa tiszteletben a lekérdezéseket. Noha az Azure Cosmos DB írási lett optimalizálva, és támogatja a szinkron indexkarbantartás és konzisztens-lekérdezések kiszolgálása dokumentum írások tartós mennyiségű, konfigurálhatja az egyes gyűjtemények ráérősen frissítéséhez az index. Lusta indexelő serkenti az írási teljesítményének tovább, és ideális tömeges betöltési alkalmazási helyzetekben elsősorban a olvasási gyűjtemények esetében.

Az indexelési házirendet is módosítható az a gyűjtemény egy PUT végrehajtásával. Ez lehet keresztül érhető el a [ügyféloldali SDK-val](sql-api-sdk-dotnet.md), a [az Azure portal](https://portal.azure.com), vagy a [REST API-k](/rest/api/cosmos-db/).

### <a name="querying-a-collection"></a>Gyűjtemény lekérdezése
A dokumentumok a gyűjteményen belül is tetszőleges sémákkal rendelkeznek, és kérhet le dokumentumokat egy gyűjteményen belül anélkül, hogy bármiféle séma vagy másodlagos indexek előre. A gyűjtemény használatával lekérdezheti a [Azure Cosmos DB SQL-szintaxis referenciája](https://msdn.microsoft.com/library/azure/dn782250.aspx), amely biztosítja a részletes hierarchikus, relációs és térbeli operátorokat és bővíthetőséget a JavaScript-alapú UDF-EK. A JSON-szintaxis lehetővé teszi, hogy a JSON-dokumentumok fákat, a címkéket, ahogy a fa csomópontjai modellezési. Ez az SQL API-k automatikus indexelési technikái Azure Cosmos DB SQL dialect úgy kihasználni. Az SQL-lekérdezési nyelv áll három fő szempontjait:   

1. Egy kis készletét a lekérdezési műveletek, amelyek leképezik a faszerkezetben, beleértve a hierarchikus lekérdezéseket és leképezések természetes módon. 
2. Többek között az összeállítás, szűrő, leképezések, összesítések és önkiszolgáló illesztések relációs műveletek egy részét. 
3. Tiszta JavaScript-alapú felhasználói függvények, amelyek együttműködnek (1) és (2).  

Az Azure Cosmos DB lekérdezési modelljét próbál funkciót, a hatékonyság és az egyszerűség kedvéért közötti egyensúlyt. Az Azure Cosmos DB-adatbázismotor natív módon lefordítja, és végrehajtja az SQL-lekérdezés utasításokat. Egy gyűjtemény használatával lekérdezheti a [REST API-k](/rest/api/cosmos-db/) , illetve a [ügyféloldali SDK-k](sql-api-sdk-dotnet.md). A .NET SDK tartalmaz egy LINQ.

> [!TIP]
> Próbálja ki az SQL API-t, és az adatkészletet az SQL-lekérdezések futtatásához a [Query Playground](https://www.documentdb.com/sql/demo).
> 
> 

## <a name="multi-document-transactions"></a>Többdokumentumos tranzakciókat
Adatbázis-tranzakciók többé vesződnie a sérült egyidejű módosításai az adatok egy biztonságos és kiszámítható programozási modellt biztosítanak. Relációsadatbázis-kezelő rendszer, az üzleti logika írását a hagyományos módon, hogy írási **tárolt eljárások** és/vagy **eseményindítók** , és küldje el azt az adatbázis-kiszolgálóhoz tranzakciós végrehajtásához. Relációsadatbázis-kezelő rendszer az alkalmazás-programozó szükséges a két, különböző programnyelveken foglalkozik: 

* A (nem tranzakciós) alkalmazás programozási nyelvet (például JavaScript, Python, C#, Java stb.)
* T-SQL, a tranzakciós programozási nyelv, az adatbázis által natív módon végrehajtott

Alapján a mélyen elkötelezettek a JavaScript és a JSON közvetlenül az adatbázismotor belül, Azure Cosmos DB kínál egy intuitív programozási modellhez végrehajtása JavaScript alkalmazáslogikát közvetlenül a gyűjtemények alapján tekintetében tárolt eljárások és eseményindítók. Ez lehetővé teszi a következő két:

* Egyidejűségi hatékony megvalósítását szabályozhatja, helyreállítás, az automatikus indexelést, a JSON-objektum grafikonok közvetlenül az adatbázismotor a
* Átvitelvezérlés természetes módon kifejezése, változó hatókörkezeléséhez kapcsolódó, hozzárendelése és integrációs kivételkezelési primitívek adatbázis-tranzakciók közvetlenül a JavaScript programnyelvet tekintetében

A JavaScript-logika regisztrálva, a gyűjtemény szintjén is hogyan adhat ki az adatbázis-műveleteket a dokumentumokat az adott gyűjtemény. Az Azure Cosmos DB implicit módon becsomagolja a JavaScript-alapú tárolt eljárásokkal és eseményindítókkal belül egy környezeti ACID tranzakciót a pillanatkép-elkülönítés dokumentumok egy gyűjteményen belül. Ha a futtatása során a JavaScript kivételt jelez, a teljes tranzakció megszakad. Az eredményül kapott programozási modell használata egyszerű mégis hatékony. JavaScript-fejlesztőinek "tartós" programozási modell beolvasása közben továbbra is a jól ismert nyelvi szerkezeteket és a könyvtár primitívek használja.   

JavaScript végrehajtása az adatbázismotor a pufferkészletben azonos címterében belül közvetlenül lehetővé teszi lehetővé teszi a nagy teljesítményű és az adatbázis-műveletek dokumentumokon gyűjtemény tranzakciós végrehajtásához. Továbbá a Cosmos DB adatbázis-kezelő lehetővé teszi a JSON mélyen elkötelezettek, és a JavaScript használatával kiküszöbölhető a bármely impedanica-eltérés a típus rendszereket, alkalmazás és az adatbázis között.   

Miután létrehozott egy gyűjteményt, regisztrálhat a tárolt eljárások, eseményindítók és felhasználói függvények az egy gyűjtemény használatával a [REST API-k](/rest/api/cosmos-db/) , illetve a [ügyféloldali SDK-k](sql-api-sdk-dotnet.md). A regisztrációt követően is hivatkozhat, és végre őket. Vegye figyelembe a következő tárolt eljárást, teljes mértékben javascriptben írt, a kódot az alábbi két argumentumot (a könyv nevét és a szerző neve) és a egy új dokumentumot hoz létre, lekérdezi a dokumentumok és frissíti azt – az összes implicit ACID-tranzakción belül. A végrehajtás során bármikor Javascrip-kivétel lépett fel, ha a teljes tranzakció megszakítása.

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

Az ügyfél "szállíthat" a fenti JavaScript-logika tranzakciós végrehajtásához HTTP POST-n keresztül az adatbázishoz. HTTP-metódusok használatával kapcsolatos további információkért lásd: [RESTful interakció az Azure Cosmos DB-erőforrások](https://msdn.microsoft.com/library/azure/mt622086.aspx). 

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


Figyelje meg, hogy az adatbázis natív módon megért JSON és a JavaScript, mert nincs system adattípus-eltérés, ne legyen "Vagy leképezés" vagy code generation Magic Quadrant szükséges.   

Tárolt eljárások és eseményindítók interakcióba egy gyűjtemény és a egy jól definiált hálózatiobjektum-modellje, amely az aktuális gyűjtemény környezetben keresztül gyűjteményben található dokumentumokat.  

Az SQL API-ban lehet gyűjteményeket létrehozni, törlése, olvasási vagy sorszámozott használatával könnyedén vagy a [REST API-k](/rest/api/cosmos-db/) , illetve a [ügyféloldali SDK-k](sql-api-sdk-dotnet.md). Az SQL API mindig biztosítja a metaadat-gyűjtemény lekérdezése vagy olvasása a konzisztenciát. A gyűjtemény törlése automatikusan biztosítja, hogy nem fér hozzá egyik a dokumentumok, a mellékleteket, a tárolt eljárások, eseményindítók és felhasználói függvények az abban szereplő.   

## <a name="stored-procedures-triggers-and-user-defined-functions-udf"></a>Tárolt eljárások, eseményindítók és felhasználói definiált függvények (UDF)
Az előző szakaszban leírtak írhat az alkalmazáslogikát, hogy közvetlenül az adatbázismotor belül tranzakción belül futtatni. Az alkalmazáslogika teljes mértékben javascriptben írható, és a egy tárolt eljárást, eseményindító vagy egy UDF modellezhető. A JavaScript-kódot egy tárolt eljárás vagy eseményindító Beszúrás, csere, olvassa el vagy kérdezheti le egy gyűjteményen belül. Másrészről, a JavaScript egy UDF belül nem lehet beszúrni, cserélje le, vagy törölhetnek dokumentumokat. UDF-EK számba venni az eredményhalmaz egy lekérdezést a dokumentumok és a egy másik eredményhalmaz. A több-bérlős Azure Cosmos DB kényszeríti a szigorú foglalásalapú erőforrás-szabályozása. Minden egyes tárolt eljárás, eseményindító vagy egy UDF lekérdezi a rögzített kvantum az operációs rendszer erőforrásait, ezt a munkát. Továbbá a tárolt eljárások, eseményindítók és felhasználói függvények nem sikerült összekapcsolni az külső JavaScript-kódtárak szemben, és tiltólistán van, ha túllépi a számukra kiosztott erőforrás költségvetések. Lehet regisztrálni, a tárolt eljárások, eseményindítók és felhasználói függvények regisztrációját a gyűjteményt a REST API-k használatával.  Regisztráláskor egy tárolt eljárást, eseményindító vagy egy UDF előre lefordított és tárolt bájt kóddal, amely később hajtsa végre. A következő szakasz azt mutatja be, hogyan használhatja az Azure Cosmos DB a JavaScript SDK regisztrálásához, hajtsa végre, és egy tárolt eljárást, a trigger és a egy UDF-ben. A JavaScript SDK-t egy egyszerű burkolót felett. a [REST API-k](/rest/api/cosmos-db/). 

### <a name="registering-a-stored-procedure"></a>Tárolt eljárás regisztrálása
Tárolt eljárás regisztrálása tárolt eljárás új erőforrás egy gyűjtemény HTTP POST használatával hoz létre.  

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

### <a name="executing-a-stored-procedure"></a>Tárolt eljárás végrehajtása
Tárolt eljárás végrehajtása egy HTTP POST egy meglévő tárolt eljárás erőforrás ellen kiállításával paraméterek átadása a kérelem törzsében szereplő eljárás szerint történik.

    var inputDocument = {id : "document1", author: "G. G. Marquez"};
    client.executeStoredProcedureAsync(createdStoredProcedure.resource._self, inputDocument)
        .then(function(executionResult) {
            assert.equal(executionResult, "success - created DOCUMENT1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-stored-procedure"></a>Tárolt eljárás regisztrációjának törlése
Regisztrációjának törlése a tárolt eljárás alapján egy meglévő tárolt eljárás erőforrás ellen egy HTTP DELETE történik.   

    client.deleteStoredProcedureAsync(createdStoredProcedure.resource._self)
        .then(function (response) {
            return;
        }, function(error) {
            console.log("Error");
        });


### <a name="registering-a-pre-trigger"></a>Regisztrálás előtti eseményindító
A trigger regisztrációs hozzon létre egy új eseményindító erőforrást egy gyűjteményen HTTP POST-n keresztül történik. Ha az eseményindító egy előre be állítva, vagy a post eseményindító és művelet azt is társítható (például létrehozása, Replace, Delete vagy az összes) is megadhat.   

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

### <a name="executing-a-pre-trigger"></a>Üzem előtti eseményindító végrehajtása
Eseményindító végrehajtása egy meglévő trigger nevének megadásával a POST és PUT és DELETE kérelem a kérelem fejlécében keresztül dokumentum erőforrás kiállító időpontjában történik meg.  

    client.createDocumentAsync(collection._self, { id: "doc1", key: "Love in the Time of Cholera" }, { preTriggerInclude: "upperCaseId" })
        .then(function(createdDocument) {
            assert.equal(createdDocument.resource.id, "DOC1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-pre-trigger"></a>Üzem előtti eseményindító regisztrációjának törlése
Regisztrációjának törlésekor az eseményindító használatával egy HTTP DELETE eseményindító meglévő erőforrás ellen kiállítása történik.  

    client.deleteTriggerAsync(createdPreTrigger._self);
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

### <a name="registering-a-udf"></a>Regisztrálás egy UDF
Regisztráció egy UDF hozzon létre egy új UDF-erőforrást egy gyűjteményen HTTP POST-n keresztül történik.  

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
Egy UDF-ben az SQL-lekérdezés részeként adható meg, és arra, hogy az alapvető kiterjesztése használatos [SQL lekérdező nyelve](sql-api-sql-query-reference.md).

    var filterQuery = "SELECT udf.mathSqrt(r.Age) AS sqrtAge FROM root r WHERE r.FirstName='John'";
    client.queryDocuments(collection._self, filterQuery).toArrayAsync();
        .then(function(queryResponse) {
            var queryResponseDocuments = queryResponse.feed;
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-udf"></a>Egy UDF regisztrációjának törlése
Egy UDF regisztrációjának egyszerűen végzi el kiadása egy HTTP DELETE egy meglévő UDF erőforrás ellen.  

    client.deleteUserDefinedFunctionAsync(createdUdf._self)
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

Noha a fenti kódrészletek bemutatta a regisztrációs (POST), a regisztráció megszüntetésének (PUT), a olvasás/listázási (GET) és a végrehajtási (POST) keresztül a [JavaScript SDK](https://github.com/Azure/azure-documentdb-js), használhatja a [REST API-k](/rest/api/cosmos-db/) vagy más [ügyféloldali SDK-k](sql-api-sdk-dotnet.md). 

## <a name="documents"></a>Dokumentumok
Akkor is beszúrása, cserélje le, törlése, olvasása, számbavétele és egy gyűjtemény tetszőleges JSON-dokumentumok lekérdezése. Az Azure Cosmos DB nem írja elő semmilyen sémát, és nem szükséges másodlagos indexek támogatásához a lekérdezés a gyűjteményben található dokumentumokat. A dokumentum maximális mérete 2 MB.   

Folyamatban van egy valóban megnyitott adatbázis-szolgáltatás, az Azure Cosmos DB nem találjon ki minden olyan speciális adattípusok (például dátum) vagy adott kódolásai JSON-dokumentumok. Az Azure Cosmos DB nincs szükség semmilyen különleges JSON egyezmények kodifikálni a különböző dokumentumok; közötti kapcsolatokat az Azure Cosmos DB SQL-szintaxis a lekérdezés és a projekt dokumentumok speciális jegyzetek és kodifikálni dokumentumok közötti kapcsolatokat kell nélkül operátorok megkülönböztető tulajdonságok hatékony hierarchikus és relációs lekérdezési biztosít.  

Más erőforrások, dokumentumok hozhatók létre, cseréje esetén, törlése, olvasása, enumerált, és lekérdezése a REST API-k, illetve bármely használatával könnyedén a [ügyféloldali SDK-k](sql-api-sdk-dotnet.md). Dokumentum törlése azonnal szabadít fel az összes beágyazott melléklet megfelelő kvóta. Dokumentumok olvasási konzisztencia szintjét az adatbázis-fiókot a konzisztencia-szabályzat következik. Ez a szabályzat az alkalmazás adatkonzisztencia-követelményeitől függően kérelem alapon felülbírálható. Dokumentumok lekérdezésekor az olvasás következetes követi az indexelési mód beállítása a gyűjtemény. A "konzisztens" Ez a fiók konzisztencia-szabályzat követi. 

## <a name="attachments-and-media"></a>Mellékletek és az adathordozó
Az Azure Cosmos DB lehetővé teszi a bináris media/blobok tárolására, vagy az Azure Cosmos DB (legfeljebb 2 GB-fiókonként) vagy a saját távoli médiatárbeli. Lehetővé teszi egy adathordozó melléklet nevű speciális dokumentumban találhatók tekintetében a metaadatokat képviseli. Az Azure Cosmos DB a melléklet, amely hivatkozik a máshol tárolt media/blob speciális (JSON) dokumentumban. A melléklet egyszerűen csak egy speciális dokumentumban találhatók, amely a metaadatokat (például a tartózkodási hely, a szerző stb.) egy olyan távoli media storage-ban tárolt adathordozó rögzíti. 

Érdemes lehet egy Azure Cosmos DB széljegyzetek tárolására használó közösségi olvasó alkalmazás, és a metaadatokat, például a megjegyzéseit, emeli ki, a könyvjelzők, értékelések, stb. egy adott felhasználó egy e-könyv a társított kedvelések/dislikes.   

* Tárolja a tartalmát a könyv magát, az adathordozó-storage-ban vagy egy távoli media store vagy Azure Cosmos DB-adatbázisfiók részeként érhető el. 
* Egy alkalmazás minden felhasználói metaadatok tárolhat egy különálló dokumentumként – például /colls/joe/docs/book1 hivatkozik a dokumentum Munka1 Joe metaadatait tárolja. 
* Egy felhasználó egy adott könyv tartalom lapok mutató mellékletek tárolt a megfelelő dokumentumot, például /colls/joe/docs/book1/chapter1, /colls/joe/docs/book1/chapter2 stb. 

A fent felsorolt példák rövid azonosítók használata az erőforrás-hierarchia közlésére. Erőforrások keresztül egyedi erőforrás-azonosítókat megtalálhatja a REST API-kon keresztül érhetők el. 

A média-, Azure Cosmos DB által felügyelt a melléklet _adathordozó tulajdonságát hivatkozik az URI a médiaelemet. Az Azure Cosmos DB biztosítja az adathordozót a szemétgyűjtési gyűjtünk, amikor az összes a szálankénti függőben lévő hivatkozást a rendszer elveti. Az Azure Cosmos DB automatikusan hoz létre a mellékletet, amikor feltölti az új adathordozó tölti fel a _adathordozó, hogy az újonnan hozzáadott adathordozó mutasson. Ha az adathordozó tárolása egy távoli blobtár Ön felügyeli azt (például OneDrive, az Azure Storage, DropBox stb.), hivatkoznia a media továbbra is használhatja a mellékleteket. Ebben az esetben fog létrehozni a mellékletet, és töltse fel a _adathordozó tulajdonsága.   

Ahogy minden más erőforrások, a mellékletek hozhatók létre, cserélni, törlése, olvasása vagy enumerálni a REST API-k vagy bármely, az ügyfél SDK-k használatával könnyedén. Dokumentumok, a mellékletek olvasási konzisztencia szintjét következik a konzisztencia-szabályzat az adatbázisfiókot. Ez a szabályzat az alkalmazás adatkonzisztencia-követelményeitől függően kérelem alapon felülbírálható. Mellékletek lekérdezésekor az olvasás következetes követi az indexelési mód beállítása a gyűjtemény. A "konzisztens" Ez a fiók konzisztencia-szabályzat követi. 
 

## <a name="users"></a>Felhasználók
Egy Azure Cosmos DB felhasználói engedélyek csoportosítása egy logikai névteret jelöl. Egy Azure Cosmos DB-felhasználó is hozzá lehet rendelve egy felhasználó számára az identitáskezelési rendszerekkel vagy egy előre definiált alkalmazás-szerepkör. Az Azure Cosmos DB a felhasználó egyszerűen csoportban egy adatbázis engedélyek egy készletét csoporthoz absztrakciós jelöli.   

A több-bérlős implementálása az alkalmazásban, az Azure Cosmos DB, amely megfelel a tényleges felhasználók vagy a bérlők számára az alkalmazás felhasználók hozhat létre. Ezután létrehozhat egy adott felhasználói engedélyeket, amelyek megfelelnek a hozzáférés-vezérlés keresztül különböző gyűjtemények, dokumentumok, a mellékleteket, stb.   

Az alkalmazások méretezése a felhasználó növekedést is szükséges, az adatok különböző módjait a szegmensek is elfogadja. Minden egyes felhasználónak a következőképpen modellezheti:   

* Minden felhasználó képez le egy adatbázist.
* Minden felhasználó képez le egy gyűjteményt. 
* Több felhasználó megfelelő dokumentumok lépjen egy dedikált gyűjteményt. 
* Több felhasználó megfelelő dokumentumok nyissa meg a set-gyűjtemények.   

A megadott horizontális skálázási stratégia függetlenül választja, az aktuális felhasználói modell felhasználóként az Azure Cosmos DB-adatbázisban, és részletes engedélyeket, az egyes felhasználók társítása.  

![Felhasználógyűjtemények][3]  
**Horizontális skálázási stratégia és a modellezés felhasználók**

Más erőforrások, például felhasználók az Azure Cosmos DB hozhatók létre, cserélni, törlése, olvassa el vagy REST API-k vagy bármely, az ügyfél SDK-k használatával könnyedén számba. Az Azure Cosmos DB mindig biztosít a metaadatok felhasználói erőforrás lekérdezése vagy olvasása a konzisztenciát. Érdemes megjegyeznünk, hogy nem tud hozzáférni az ebben lévő engedélyek biztosítja, hogy automatikusan egy felhasználói fiók törlése. Annak ellenére, hogy az Azure Cosmos DB a háttérben a törölt felhasználó részeként szabadít fel az engedélyeket a beállított kvótát, a törölt engedélyek érhető el azonnal újra kell használni.  

## <a name="permissions"></a>Engedélyek
Access control szempontból, erőforrások, például adatbázis-fiókok, adatbázisok, felhasználók és engedély számítanak *felügyeleti* erőforrásokat, mivel ezek a rendszergazdai engedély szükséges. Másrészről, erőforrások, például a gyűjtemények, dokumentumok, a mellékleteket, tárolt eljárások, eseményindítók, és az UDF-EK alapján egy adott adatbázishoz kötődő és tekinthető *alkalmazás-erőforrások*. A két típusú erőforrások és a szerepköröket, amelyek érheti el őket (azaz a rendszergazdai és felhasználói) megfelelő, a használt engedélyezési modellt határozza meg két típusú *hozzáférési kulcsok*: *főkulcs* és  *Az erőforráskulcs*. A főkulcs egy része az adatbázis-fiókot, és a fejlesztői (vagy a rendszergazda) megadott ki van folyamatban az adatbázis-fiókot. A főkulcsnak rendszergazda szemantikát, abban, hogy a felügyeleti és az alkalmazás-erőforrásokhoz való hozzáférés engedélyezésére használható. Ezzel szemben az erőforrás kulcs-e egy részletes hozzáférés-kulcsot, amely lehetővé teszi a hozzáférést egy *adott* alkalmazás-erőforrás. Ebből kifolyólag rögzíti a felhasználó az adatbázis és az engedélyek a felhasználónak van egy adott erőforrás (például gyűjtemény, dokumentum, melléklet, a tárolt eljárás, eseményindító vagy UDF) közötti kapcsolat.   

Egy erőforrás-kulcs beszerzése egyetlen módja, hozzon létre egy adott felhasználó engedélyt erőforrást. Annak érdekében, hogy létrehozni vagy beolvasni az engedélyt, egy főkulcsot az engedélyezési fejléc kell bemutatni. Engedély erőforrás összeköt az erőforrást, a hozzáférés és a felhasználó. Engedély erőforrás létrehozása után a felhasználónak csak szüksége van a társított erőforrás kulcs található a megfelelő erőforrás történő hozzáféréshez. Ezért egy erőforrás-kulcsot, az engedély erőforrásokat szimbolizáló logikai és kompakt is megtekinthetők.  

Ahogy minden más erőforrásokkal, engedélyekkel az Azure Cosmos DB hozható létre, cserélni, törlése, olvassa el vagy REST API-k vagy bármely, az ügyfél SDK-k használatával könnyedén számba. Az Azure Cosmos DB mindig biztosít egy engedély a metaadatok lekérdezése vagy olvasása a konzisztenciát. 

## <a name="next-steps"></a>További lépések
További információ a HTTP-parancsok segítségével az erőforrások használatára [RESTful interakció az Azure Cosmos DB-erőforrások](https://msdn.microsoft.com/library/azure/mt622086.aspx).

[1]: media/sql-api-resources/resources1.png
[2]: media/sql-api-resources/resources2.png
[3]: media/sql-api-resources/resources3.png

