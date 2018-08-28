---
title: Az Azure Data Catalog fejlesztői fogalmak
description: A fogalmi modellben az Azure Data Catalog, mint a katalógus REST API-n keresztül elérhetővé tett kapcsolatos főbb fogalmakat bemutatása.
services: data-catalog
author: spelluru
ms.author: spelluru
ms.assetid: 89de9137-a0a4-40d1-9f8d-625acad31619
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 753b4660c8ca47f12aace87a254b93a88db8aaa7
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43053476"
---
# <a name="azure-data-catalog-developer-concepts"></a>Az Azure Data Catalog fejlesztői fogalmak
A Microsoft **Azure Data Catalog** egy teljes körűen felügyelt felhőszolgáltatás, amely lehetőségeket kínál az adatforrások felderítését és a közösségi adatforrás-metaadatokat. A fejlesztők a a szolgáltatás a REST API-kon keresztül. A fejlesztők számára, hogy sikeresen integrálása a service-ben megvalósított fogalmak értelmezéséhez fontos **Azure Data Catalog**.

## <a name="key-concepts"></a>Fő fogalmak
A **Azure Data Catalog** fogalmi modell a négy fő fogalmak alapul: A **katalógus**, **felhasználók**, **eszközök**, és  **Jegyzetek**.

![Koncepció][1]

*1. ábra – az Azure Data Catalog egyszerűsített fogalmi modellhez*

### <a name="catalog"></a>Katalógus
A **katalógus** szervezet tárolja az összes metaadat legfelső szintű tárolója. Van egy **katalógus** egy Azure-fiók lehet. Katalógusok vannak kötve egy Azure-előfizetéshez, de csak az egyiket **katalógus** hozható létre bármelyik megadott Azure-fiókra, annak ellenére, hogy egy fiók több előfizetéssel is rendelkezhet.

A katalógus tartalmazza a **felhasználók** és **eszközök**.

### <a name="users"></a>Felhasználók
Felhasználók, amely rendelkezik jogosultságokkal műveletek végrehajtásához rendszerbiztonsági tagok (a katalógusban, hozzáadása, szerkesztése vagy eltávolítása a cikkeket, stb.) a katalógusban.

A felhasználók még több különböző szerepkörök tartoznak. Szerepkörökkel kapcsolatos további információkért lásd: a szakasz szerepköröket és engedélyezési.

Az egyes felhasználók és biztonsági csoportokat is hozzáadhatók.

Az Azure Data Catalog az identitás és hozzáférés-kezelés az Azure Active Directory. Minden egyes Alkalmazáskatalógus felhasználói az Active Directory, a fiók tagja kell lennie.

### <a name="assets"></a>Objektumok
A **katalógus** adategységek tartalmazza. **Eszközök** vannak a katalógus által kezelt granularitási egysége.

Egy eszköz részletességét eltérő adatforrás. Az SQL Server vagy az Oracle Database az eszköz lehet egy tábla vagy nézet. Az SQL Server Analysis Services esetén az eszköz lehet egy mérték, egy dimenzió vagy egy fő teljesítménymutató (KPI). Az SQL Server Reporting Services az eszköz egy olyan jelentés.

Egy **eszköz** a dolog, adja hozzá, vagy távolítsa el a katalógusból. Vissza az eredményt egysége **keresési**.

Egy **eszköz** a nevét, helyét, és írja be, és jegyzetek, további leíró épül fel.

### <a name="annotations"></a>jegyzetek
Jegyzetek olyan elemek, amelyek az eszközök metaadatait.

Jegyzetek példák leírása, címkék, séma, dokumentáció, stb. Az eszköz és jegyzet típusainak teljes listáját az eszköz objektum modell szakaszban találhatók.

## <a name="crowdsourcing-annotations-and-user-perspective-multiplicity-of-opinion"></a>Közösségi jegyzetek és a felhasználó szemszögéből (véleményével kapcsolatban)
A rendszer kulcsfontosságú az Azure Data Catalog hogyan támogatja a metaadatok közösségi hozzáadása a rendszerben. És ne a wiki megközelítést – ahol csak egy vélemény és a legutolsó író wins – az Azure Data Catalog modell lehetővé teszi, hogy az élő egymás mellett a rendszer több véleményeket.

Ez a megközelítés tükrözi a való világból a ahol különböző felhasználók rendelkezhetnek programban különböző szempontok szerint egy adott eszköz a vállalati adatok:

* Adatbázis-rendszergazda rendelkezhetnek szolgáltatásiszint-szerződések, vagy a rendelkezésre álló feldolgozási időszakának adatainak tömeges ETL-műveletek
* Egy az adatgazdai nyújthat az információk az üzleti folyamatok, amelyekre az eszköz vonatkozik, és a besorolásokat, amelyek az üzleti van alkalmazva
* A pénzügyi elemző nyújthat az időszak vége jelentéskészítési feladatok során az adatok felhasználásának információ

Ebben a példában támogatása érdekében minden felhasználónak – az adatbázis, az adatgazdai és az elemzői – egy leírást adhat hozzá a katalógusban regisztrált egy táblát. A rendszer minden leírás karbantartása, és az Azure Data Catalog portál összes leírása jelennek meg.

Így a JSON-adattartalom objektumtípusok gyakran tulajdonságok tömbök egypéldányos várt lehet, hogy ezt a mintát a hálózatiobjektum-modellje, az elemeinek többsége alkalmazható.

Az eszköz alatt például a legfelső szintű, leírás objektumokból álló tömb. A tömb tulajdonság neve "leírások". A leírás objektumnak egy tulajdonság - leírás. Az egyik, hogy minden felhasználónak, aki típusok leírását lekérdezi egy leírás létre a felhasználó által megadott érték.

A UX kiválaszthatja a kombináció megjelenítése. Nincsenek megjeleníthető három különböző minták.

* A legegyszerűbb minta a "Show All". Ebben a mintában lévő összes objektum lista nézetben jelennek meg. Az Azure Data Catalog portál UX leírás ezt a mintát használja.
* Egy másik egyik "Merge". Ebben a mintában a különböző felhasználók az összes értéket egyesülnek együtt, ismétlődő eltávolítva. Ezt a mintát az Azure Data Catalog-portálon UX példák a címkék és a szakértők tulajdonság.
* Egy harmadik egyik "legutolsó író wins". Ez a minta csak a legutóbbi beírt érték jelenik meg. rövid név, amelyek a mintáról.

## <a name="asset-object-model"></a>Az eszközintelligencia hálózatiobjektum-modellje
Megjelent az alapfogalmak szakasz a **Azure Data Catalog** hálózatiobjektum-modellt tartalmaz cikkek, amelyek eszközök vagy széljegyzeteket lehet. Elemek tulajdonság tartozik, ez nem kötelező vagy szükséges lehet. Egyes tulajdonságok minden elemre vonatkozik. Egyes tulajdonságok vonatkoznak az összes eszköz. Egyes tulajdonságok vonatkoznak, csak a konkrét eszköz.

### <a name="system-properties"></a>Rendszertulajdonságok
<table><tr><td><b>Tulajdonság neve</b></td><td><b>Adattípus</b></td><td><b>Megjegyzések</b></td></tr><tr><td>időbélyeg</td><td>DateTime</td><td>Az elem módosította utoljára. Ez a mező a kiszolgáló jön létre, ha egy elem szerepel, és minden alkalommal, amikor frissül egy elemet. Ez a tulajdonság értékét a rendszer figyelmen kívül hagyja bevitele a közzétételi műveletek.</td></tr><tr><td>id</td><td>URI</td><td>Az elem (írásvédett) abszolút URL-címe Az elem egyedi címezhető URI.  Ez a tulajdonság értékét a rendszer figyelmen kívül hagyja bevitele a közzétételi műveletek.</td></tr><tr><td>type</td><td>Sztring</td><td>(Írásvédett) az eszköz típusa.</td></tr><tr><td>Az ETag</td><td>Sztring</td><td>Az elem, amely a katalógus elemeinek frissítő műveletek végrehajtásakor az optimista egyidejűség-vezérlés használható verziójának megfelelő karakterlánc. "*" értékek egyeztetéséhez használható.</td></tr></table>

### <a name="common-properties"></a>Közös tulajdonságok
Minden legfelső szintű eszköz és az összes jegyzet típusainak alkalmazni ezeket a tulajdonságokat.

<table>
<tr><td><b>Tulajdonság neve</b></td><td><b>Adattípus</b></td><td><b>Megjegyzések</b></td></tr>
<tr><td>fromSourceSystem</td><td>Logikai</td><td>Azt jelzi, hogy elem adatait a forrásrendszerben (például Sql Server-adatbázis, Oracle-adatbázis) származó vagy felhasználó által készített.</td></tr>
</table>

### <a name="common-root-properties"></a>Gyakori legfelső szintű tulajdonságai
<p>
Ezek a tulajdonságok minden legfelső szintű eszköz esetében alkalmazni.

<table><tr><td><b>Tulajdonság neve</b></td><td><b>Adattípus</b></td><td><b>Megjegyzések</b></td></tr><tr><td>név</td><td>Sztring</td><td>Az adatforrás helye adatait származó nevét</td></tr><tr><td>DSL</td><td>DataSourceLocation</td><td>Egyedileg írja le az adatforrás és az eszköz az azonosítók közül. (Lásd a kettős identitás szakaszt).  A dsl szerkezete a protokoll és a forrás típusa szerint változó.</td></tr><tr><td>Adatforrás</td><td>DataSourceInfo</td><td>További részleteket az eszköz típusától.</td></tr><tr><td>lastRegisteredBy</td><td>SecurityPrincipal</td><td>Ez az eszköz legutóbb regisztráló felhasználó ismerteti.  Mindkét egyedi azonosítója. a felhasználó (egyszerű felhasználónév) és a egy megjelenített nevet (Vezetéknév és utónév) tartalmazza.</td></tr><tr><td>containerId</td><td>Sztring</td><td>A tároló eszköz az adatforrás-azonosítója. Ez a tulajdonság nem támogatott a tároló típusa.</td></tr></table>

### <a name="common-non-singleton-annotation-properties"></a>Gyakori egyetlen jegyzet tulajdonságai
Ezek a tulajdonságok vonatkoznak az összes nem egypéldányos jegyzet a (jegyzetek, amely több lehet eszközönként).

<table>
<tr><td><b>Tulajdonság neve</b></td><td><b>Adattípus</b></td><td><b>Megjegyzések</b></td></tr>
<tr><td>kulcs</td><td>Sztring</td><td>Egy felhasználó által megadott kulcs, amely egyedileg azonosítja az aktuális gyűjtemény a jegyzet. A kulcs hosszúsága nem lehet hosszabb 256 karakternél.</td></tr>
</table>

### <a name="root-asset-types"></a>Legfelső szintű eszköz típusa
Legfelső szintű eszközintelligencia ezeket a feldolgozástípusokat, amelyek különböző fájltípusok közül a katalógusban regisztrált adategységek. Minden legfelső szintű típus egy nézet, amely az eszközintelligencia és jegyzetek az nézetben van. Nézet neve kell használni a megfelelő {view_name} URL-szegmens az, amikor REST API-val adategység közzététele.

<table><tr><td><b>Eszköz típusa (nézet neve)</b></td><td><b>További tulajdonságok</b></td><td><b>Adattípus</b></td><td><b>Engedélyezett jegyzetek</b></td><td><b>Megjegyzések</b></td></tr><tr><td>Tábla ("táblák")</td><td></td><td></td><td>Leírás<p>Rövid név<p>Címke<p>Séma<p>ColumnDescription<p>ColumnTag<p> Szakértő<p>Előzetes verzió<p>AccessInstruction<p>TableDataProfile<p>ColumnDataProfile<p>ColumnDataClassification<p>Dokumentáció<p></td><td>Egy tábla táblázatos adatokat jelöli.  Példa: SQL-táblára, az SQL-nézet, Analysis Services-táblázatos tábla, Analysis Services többdimenziós dimenzió, Oracle-tábla, és így tovább.   </td></tr><tr><td>Mérték ("mértékek")</td><td></td><td></td><td>Leírás<p>Rövid név<p>Címke<p>Szakértő<p>AccessInstruction<p>Dokumentáció<p></td><td>Ez a típus jelképezi egy Analysis Services-mérték.</td></tr><tr><td></td><td>mérték</td><td>Oszlop</td><td></td><td>A mérték leíró metaadatok</td></tr><tr><td></td><td>isCalculated </td><td>Logikai</td><td></td><td>Itt adhatja meg, ha a mérték, vagy nem számítja ki.</td></tr><tr><td></td><td>measureGroup</td><td>Sztring</td><td></td><td>Mérték fizikai tároló</td></tr><td>KPI ("kpis")</td><td></td><td></td><td>Leírás<p>Rövid név<p>Címke<p>Szakértő<p>AccessInstruction<p>Dokumentáció</td><td></td></tr><tr><td></td><td>measureGroup</td><td>Sztring</td><td></td><td>Mérték fizikai tároló</td></tr><tr><td></td><td>goalExpression</td><td>Sztring</td><td></td><td>Numerikus MDX-kifejezés vagy egy számítás, amely a KPI cél értékét adja vissza.</td></tr><tr><td></td><td>valueExpression</td><td>Sztring</td><td></td><td>Egy MDX numerikus kifejezés, amely a KPI tényleges értékét adja vissza.</td></tr><tr><td></td><td>statusExpression</td><td>Sztring</td><td></td><td>MDX-kifejezés, amely egy meghatározott időpontban a KPI állapotát jeleníti meg időben.</td></tr><tr><td></td><td>trendExpression</td><td>Sztring</td><td></td><td>MDX-kifejezés, amely kiértékeli a KPI értékének idővel. A trend bármely időalapú feltételt, amely egy adott üzleti környezetben hasznos lehet.</td>
<tr><td>A jelentés ("jelentés")</td><td></td><td></td><td>Leírás<p>Rövid név<p>Címke<p>Szakértő<p>AccessInstruction<p>Dokumentáció<p></td><td>Ez a típus jelképezi egy SQL Server Reporting Services jelentés </td></tr><tr><td></td><td>assetCreatedDate</td><td>Sztring</td><td></td><td></td></tr><tr><td></td><td>assetCreatedBy</td><td>Sztring</td><td></td><td></td></tr><tr><td></td><td>assetModifiedDate</td><td>Sztring</td><td></td><td></td></tr><tr><td></td><td>assetModifiedBy</td><td>Sztring</td><td></td><td></td></tr><tr><td>Tároló ("tárolók")</td><td></td><td></td><td>Leírás<p>Rövid név<p>Címke<p>Szakértő<p>AccessInstruction<p>Dokumentáció<p></td><td>Ez a típus jelképezi egy tároló más tartalmaktól, például egy SQL database, egy Azure BLOB-tároló vagy egy Analysis Services-modellben.</td></tr></table>

### <a name="annotation-types"></a>Jegyzet típusok
Jegyzet típusú metaadatokat, amelyek a többi átveheti a katalógusban rendelhető típusú képviseli.

<table>
<tr><td><b>Jegyzet típusának (beágyazott nézet neve)</b></td><td><b>További tulajdonságok</b></td><td><b>Adattípus</b></td><td><b>Megjegyzések</b></td></tr>

<tr><td>Leírás ("leírások")</td><td></td><td></td><td>Ez a tulajdonság egy eszköz leírását tartalmazza. A rendszer minden felhasználó saját leírást adhat hozzá.  Csak az adott felhasználó módosíthatja a leírást.  (A rendszergazdák és az adatforrások tulajdonosai a leírás objektum törlése, de nem szerkeszthetik). A rendszer külön-külön kezeli a felhasználók leírása.  Így nincs minden eszköz (egy minden felhasználónak, aki hozzájárult az adategységhez, valószínűleg egy, az adatforrásból származó adatokat tartalmazó mellett kapcsolatos tudásukat) a leírások tömbjét.</td></tr>
<tr><td></td><td>leírás</td><td>sztring</td><td>Az eszköz rövid leírása (2-3 vonalak)</td></tr>

<tr><td>Tag ("címke")</td><td></td><td></td><td>Ez a tulajdonság határozza meg egy címkét, az adott eszköz számára. A rendszer minden felhasználó egy eszköz több címkét is hozzáadhat.  Csak a címkeobjektumok létrehozó felhasználó szerkesztheti őket.  (A rendszergazdák és az adatforrások tulajdonosai a címke-objektum törlése, de nem szerkeszthetik). A rendszer külön-külön kezeli a felhasználói címkék.  Így nincs minden egyes objektum címke objektumokból álló tömb.</td></tr>
<tr><td></td><td>címke</td><td>sztring</td><td>Egy címkét, amely leírja az eszköz.</td></tr>

<tr><td>Rövid név ("friendlyName")</td><td></td><td></td><td>Ez a tulajdonság az eszköz rövid nevét tartalmazza. Rövid név a singleton jegyzet – csak egy rövid név lehet hozzáadni adategység is.  Csak a FriendlyName objektumot létrehozó felhasználó szerkesztheti azt. (A rendszergazdák és az adatforrások tulajdonosai a FriendlyName objektum törlése, de nem szerkeszthetik). A rendszer külön-külön megtartja a felhasználó rövid neve.</td></tr>
<tr><td></td><td>rövid név</td><td>sztring</td><td>Az eszköz rövid neve.</td></tr>

<tr><td>Meghatározott sémához ("")</td><td></td><td></td><td>A séma ismerteti az adatok struktúráját.  Felsorolja a attribútum (oszlop, attribútum, a mező, stb.), a típusait, valamint más metaadatokat.  Ezeket az adatokat az adatforrásból származik.  Séma egy egypéldányos jegyzet – csak egy séma is hozzáadhatók az adott eszköz számára.</td></tr>
<tr><td></td><td>Oszlopok</td><td>[Oszlop]</td><td>Oszlop objektumok egy tömbjét. Az oszlop bemutatják az adatforrásból származó adatokkal.</td></tr>

<tr><td>ColumnDescription ("columnDescriptions")</td><td></td><td></td><td>Ez a tulajdonság oszlop leírását tartalmazza.  A rendszer minden egyes felhasználója hozzáadhatja saját leírása (legfeljebb egy oszloponként) több oszlop. Csak a ColumnDescription objektumot létrehozó felhasználó szerkesztheti őket.  (A rendszergazdák és az adatforrások tulajdonosai a ColumnDescription objektum törlése, de nem szerkeszthetik). A rendszer külön-külön megtartja ezeket felhasználói oszlop leírása.  Így nincs minden eszköz (egy mindegyik felhasználóhoz, aki hozzájárult az oszlop mellett valószínűleg egy, az adatforrásból származó adatokat tartalmazó kapcsolatos tudásukat oszloponként) ColumnDescription objektumokból álló tömb.  A ColumnDescription lazán van kötve a sémát, így azt szinkronban. A ColumnDescription ír egy oszlopot, amely a séma már nem létezik.  Leírás és séma szinkronban tartani az író esetén.  Az adatforrás is rendelkezhetnek oszlopok leírása információk és további ColumnDescription objektumokat, amely az eszköz futtatásakor létrejön.</td></tr>
<tr><td></td><td>Oszlopnév</td><td>Sztring</td><td>A leírás hivatkozik az oszlop neve.</td></tr>
<tr><td></td><td>leírás</td><td>Sztring</td><td>rövid leírása (2-3 sor) oszlop.</td></tr>

<tr><td>ColumnTag ("columnTags")</td><td></td><td></td><td>Ez a tulajdonság tartalmazza egy címkét egy oszlophoz. A rendszer minden egyes felhasználó adhat hozzá az adott oszlop több címkét, és több oszlop címkéket adhat hozzá. Csak a ColumnTag objektumot létrehozó felhasználó szerkesztheti őket. (A rendszergazdák és az adatforrások tulajdonosai a ColumnTag objektum törlése, de nem szerkeszthetik). A rendszer e felhasználók oszlop címkék külön-külön kezeli.  Így nincs minden egyes objektum ColumnTag objektumokból álló tömb.  A ColumnTag lazán van kötve a sémát, így azt szinkronban. A ColumnTag ír egy oszlopot, amely a séma már nem létezik.  Oszlop címke és a séma szinkronban tartani az író esetén.</td></tr>
<tr><td></td><td>Oszlopnév</td><td>Sztring</td><td>Ez a címke hivatkozik az oszlop neve.</td></tr>
<tr><td></td><td>címke</td><td>Sztring</td><td>Az oszlop leíró címkét.</td></tr>

<tr><td>Szakértő ("szakértői")</td><td></td><td></td><td>Ez a tulajdonság tartalmazza a felhasználó, aki az adatkészlet szakértőnek számít. A szakértői opinions(descriptions) buborék leírások listázásakor felhasználói elejéhez. Minden felhasználó a saját szakértőket adhat meg. Csak az adott felhasználó szerkesztheti a szakértők objektum. (A rendszergazdák és az adatforrások tulajdonosai a szakértői objektumok törlése, de nem szerkeszthetik).</td></tr>
<tr><td></td><td>szakértő</td><td>SecurityPrincipal</td><td></td></tr>

<tr><td>Előzetes verzió ("előzetes verziók")</td><td></td><td></td><td>Az előzetes verzió tartalmazza az adatok az eszköz az első 20 sorok pillanatképet. Előzetes verzió csak bizonyos típusú eszközök (logikus táblában, de nem a mérték) jelentéssel bírnak.</td></tr>
<tr><td></td><td>előzetes verzióban</td><td>[objektum]</td><td>Egy oszlopot jelölő objektumok tömbje.  Minden objektum rendelkezik egy tulajdonság megfeleltetése a sorhoz adott oszlop értékét tartalmazó oszlop.</td></tr>

<tr><td>AccessInstruction ("accessInstructions")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>mimeType</td><td>sztring</td><td>A tartalom mime-típusát.</td></tr>
<tr><td></td><td>content</td><td>sztring</td><td>Az utasításokat, hogy hogyan érheti el, ezt az adategységet. A tartalom lehet egy URL-CÍMÉT, e-mail-címmel vagy utasítások.</td></tr>

<tr><td>TableDataProfile ("tableDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>numberOfRows</td></td><td>int</td><td>Az adatkészlet sorainak száma</td></tr>
<tr><td></td><td>méret</td><td>hossz</td><td>A mérete (bájt) az adatkészlet.  </td></tr>
<tr><td></td><td>schemaModifiedTime</td><td>sztring</td><td>A séma módosításának utolsó idejét</td></tr>
<tr><td></td><td>dataModifiedTime</td><td>sztring</td><td>Az utolsó időpont, az adatkészlet módosítva lett (adatok lett hozzáadva, módosítása vagy törlése)</td></tr>

<tr><td>ColumnsDataProfile ("columnsDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Oszlopok</td></td><td>ColumnDataProfile]</td><td>Egy oszlop adatainak profilok tömbje.</td></tr>

<tr><td>ColumnDataClassification ("columnDataClassifications")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Oszlopnév</td><td>Sztring</td><td>Ez a besorolás hivatkozik az oszlop neve.</td></tr>
<tr><td></td><td>osztályozás</td><td>Sztring</td><td>Ebben az oszlopban levő adatok besorolását.</td></tr>

<tr><td>Dokumentáció ("dokumentáció")</td><td></td><td></td><td>Egy adott eszköz társítva, csak egy dokumentáció is rendelkezhet.</td></tr>
<tr><td></td><td>mimeType</td><td>sztring</td><td>A tartalom mime-típusát.</td></tr>
<tr><td></td><td>content</td><td>sztring</td><td>A dokumentáció tartalmát.</td></tr>

</table>

### <a name="common-types"></a>Általános típus
Általános típusú tulajdonságok esetében a típus használható, de nem elemek.

<table>
<tr><td><b>Általános típus</b></td><td><b>Tulajdonságok</b></td><td><b>Adattípus</b></td><td><b>Megjegyzések</b></td></tr>
<tr><td>DataSourceInfo</td><td></td><td></td><td></td></tr>
<tr><td></td><td>sourceType</td><td>sztring</td><td>Az adatforrás típusát írja le.  Példa: SQL Server, Oracle Database, stb.  </td></tr>
<tr><td></td><td>objectType</td><td>sztring</td><td>Az adatforrás-objektum típusának leírása. Például: Table, az SQL Server megtekintéséhez.</td></tr>

<tr><td>DataSourceLocation</td><td></td><td></td><td></td></tr>
<tr><td></td><td>protokoll</td><td>sztring</td><td>Kötelező. Azt ismerteti, hogy az adatforrás folytatott kommunikációhoz használt protokoll. Például: "tds" az SQl Server, az "oracle" Oracle stb. Tekintse meg [adatforrás-hivatkozás megadása - DSL struktúra](data-catalog-dsr.md) jelenleg támogatott protokollok listáját.</td></tr>
<tr><td></td><td>Cím</td><td>Szótár<string, object></td><td>Kötelező. Cím olyan készlete, a protokoll, amely azonosítja az adatforrás hivatkozik rá jellemző adatok. A cím egy adott protokollhoz kötődő, ami azt jelenti, hogy adata összegnek nincs valós jelentése a protokoll ismerete nélkül.</td></tr>
<tr><td></td><td>hitelesítés</td><td>sztring</td><td>Választható. A hitelesítési sémát az adatforrás folytatott kommunikáció során használt. Például: windows, oauth, stb.</td></tr>
<tr><td></td><td>connectionProperties</td><td>Szótár<string, object></td><td>Választható. További információk hogyan csatlakozhat egy adatforráshoz.</td></tr>

<tr><td>SecurityPrincipal</td><td></td><td></td><td>A háttérrendszer adatellenőrzése nem bármely elleni aad-ben megadott tulajdonságok közzététele során.</td></tr>
<tr><td></td><td>egyszerű felhasználónév</td><td>sztring</td><td>Felhasználó egyedi e-mail címe. Meg kell adni, ha nincs megadva az objectId, vagy a "lastregisteredby nem azonos" tulajdonságot, ellenkező esetben kötelező kontextusában.</td></tr>
<tr><td></td><td>objectId</td><td>GUID</td><td>Felhasználó vagy biztonsági csoport AAD-identitását. Választható. Kötelező megadni, ha egyszerű felhasználónév nem áll rendelkezésre, ellenkező esetben nem kötelező.</td></tr>
<tr><td></td><td>Keresztnév</td><td>sztring</td><td>(Megjelenítési célokból) a felhasználó utóneve. Választható. Csak érvényes "lastregisteredby nem azonos" tulajdonság kontextusában. Nem adható meg, a "szerepkör", "engedélyek" és "szakértők" rendszerbiztonsági tag megadásakor.</td></tr>
<tr><td></td><td>Vezetéknév</td><td>sztring</td><td>Utolsó felhasználó neve (megjelenítési célokra). Választható. Csak érvényes "lastregisteredby nem azonos" tulajdonság kontextusában. Nem adható meg, a "szerepkör", "engedélyek" és "szakértők" rendszerbiztonsági tag megadásakor.</td></tr>

<tr><td>Oszlop</td><td></td><td></td><td></td></tr>
<tr><td></td><td>név</td><td>sztring</td><td>Az oszlop vagy az attribútum neve.</td></tr>
<tr><td></td><td>type</td><td>sztring</td><td>az oszlop vagy az attribútum adattípusa. Az engedélyezett típusok adatok forrás típusa, az eszköz függ.  A támogatott típusok csak egy részhalmazát.</td></tr>
<tr><td></td><td>maxLength</td><td>int</td><td>Az az oszlop vagy attribútum engedélyezett maximális hosszt. Az adatforrás származik. Néhány adatforrás típusa csak érvényes.</td></tr>
<tr><td></td><td>pontosság</td><td>bájt</td><td>Az oszlop vagy az attribútum a pontosságot. Az adatforrás származik. Néhány adatforrás típusa csak érvényes.</td></tr>
<tr><td></td><td>IsNullable értéke</td><td>Logikai</td><td>Az oszlop engedélyezett-e, vagy nem null érték tartozik. Az adatforrás származik. Néhány adatforrás típusa csak érvényes.</td></tr>
<tr><td></td><td>kifejezés</td><td>sztring</td><td>Ha az érték egy számított oszlopot, ezt a mezőt tartalmazza a kifejezés, amely kifejezi az értéket. Az adatforrás származik. Néhány adatforrás típusa csak érvényes.</td></tr>

<tr><td>ColumnDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Oszlopnév </td><td>sztring</td><td>Az oszlop neve</td></tr>
<tr><td></td><td>type </td><td>sztring</td><td>Az oszlop típusa</td></tr>
<tr><td></td><td>perc </td><td>sztring</td><td>Az adatkészlet a minimális érték</td></tr>
<tr><td></td><td>max. </td><td>sztring</td><td>Az adatkészlet a maximális érték</td></tr>
<tr><td></td><td>átlag </td><td>double</td><td>Az adatkészlet az átlagos érték</td></tr>
<tr><td></td><td>szórás </td><td>double</td><td>Az adatkészlet értékét</td></tr>
<tr><td></td><td>nullCount </td><td>int</td><td>Az adatkészlet null értékek száma</td></tr>
<tr><td></td><td>distinctCount  </td><td>int</td><td>Az adatkészlet különböző értékek száma</td></tr>


</table>

## <a name="asset-identity"></a>Az eszközintelligencia-identitás
Az Azure Data Catalog DataSourceLocation "dsl" tulajdonság a "cím" tulajdonságcsomagban szereplő identitás és a "protocol" tulajdonság használatával az eszköz, amely az objektum a katalógus belső cím használható identitás létrehozása.
Például a "tds" protokoll identitás Tulajdonságok "kiszolgáló", "database", "schema" és "object" rendelkezik. A protokoll és a Tulajdonságok kombinációja az identitás, az SQL Server táblázat eszköz létrehozásához használt.
Az Azure Data Catalog számos beépített adatforrás-protokollokhoz, amelyek megtalálhatók itt [adatforrás-hivatkozás megadása - DSL struktúra](data-catalog-dsr.md).
Támogatott protokollok csoportját programozott módon is kiterjeszthető (lásd a Data Catalog REST API-referencia). A katalógus rendszergazdái regisztrálhatja egyéni adatforrás-protokollokhoz. A következő táblázat ismerteti a tulajdonságot, ami szükséges regisztrálni az egyéni protokollokat.

### <a name="custom-data-source-protocol-specification"></a>Egyéni adatforrás protokoll megadása
<table>
<tr><td><b>Típus</b></td><td><b>Tulajdonságok</b></td><td><b>Adattípus</b></td><td><b>Megjegyzések</b></td></tr>

<tr><td>DataSourceProtocol</td><td></td><td></td><td></td></tr>
<tr><td></td><td>névtér</td><td>sztring</td><td>A protocol névtere. Namespace 1 kell 255 karakter hosszú, legalább egy pontot (.) elválasztott nem üres részt tartalmaz. Egyes részek 1 – 255 karakter hosszú lennie, betűvel kezdődhet és csak betűket és számokat tartalmazhat.</td></tr>
<tr><td></td><td>név</td><td>sztring</td><td>A protokoll nevét. Neve 1 – 255 karakter hosszú lennie, betűvel kezdődhet, és tartalmazhat csak betűket, számokat és kötőjelet (-) tartalmazhat.</td></tr>
<tr><td></td><td>identityProperties</td><td>DataSourceProtocolIdentityProperty[]</td><td>Identitás-tulajdonságok listáját, tartalmaznia kell legalább egy, de nem több mint 20 tulajdonságai. Például: "kiszolgáló", "database", "séma", "object" a "tds" protokoll azonosítótulajdonságokat.</td></tr>
<tr><td></td><td>identitySets</td><td>DataSourceProtocolIdentitySet]</td><td>Identitás-csoportok listája. Határozza meg az érvényes eszköznév identitás képviselő azonosítótulajdonságokat részhalmazához. Tartalmaznia kell legalább egy, de nem több mint 20 csoportok. Például: {"kiszolgáló", "database", "schema" és "objektum"} "tds" protokoll, amely meghatározza az Sql Server-táblát eszköz identitását a beállított identitás.</td></tr>

<tr><td>DataSourceProtocolIdentityProperty</td><td></td><td></td><td></td></tr>
<tr><td></td><td>név</td><td>sztring</td><td>A tulajdonság nevét. Neve 1 – 100 karakter hosszúságú, betűvel kezdődhet kell lennie, és csak betűket és számokat tartalmazhat.</td></tr>
<tr><td></td><td>type</td><td>sztring</td><td>A tulajdonság típusa. Támogatott értékek: "logikai" logikai ","bájtos","guid","int","egész szám","hosszú","string","url"</td></tr>
<tr><td></td><td>ignoreCase</td><td>Logikai</td><td>Azt jelzi, hogy esetben figyelmen kívül lehet hagyni tulajdonság értékét használatakor. Csak akkor adható "string" típusú tulajdonságok esetében. Alapértelmezett értéke FALSE (hamis).</td></tr>
<tr><td></td><td>urlPathSegmentsIgnoreCase</td><td>logikai]</td><td>Azt jelzi, hogy esetben figyelmen kívül lehet hagyni minden egyes szegmens az URL-cím elérési út. Csak akkor adható "url" típusú tulajdonságok esetében. Alapértelmezett érték: [false].</td></tr>

<tr><td>DataSourceProtocolIdentitySet</td><td></td><td></td><td></td></tr>
<tr><td></td><td>név</td><td>sztring</td><td>Az identitáskészlet neve.</td></tr>
<tr><td></td><td>properties</td><td>String]</td><td>Állítsa be ezt az identitást programname identitás tulajdonságok listája. Nem tartalmazhat ismétlődéseket. Minden egyes tulajdonság identitáskészlet által hivatkozott definiálni kell a protokoll "identityProperties" listájában.</td></tr>

</table>

## <a name="roles-and-authorization"></a>Szerepkörök és -engedélyezés
A Microsoft Azure Data Catalog eszközök és jegyzetek CRUD-műveletek engedélyezési képességeket biztosít.

## <a name="key-concepts"></a>Fő fogalmak
Az Azure Data Catalog két hitelesítési mechanizmust használ:

* Szerepkör-alapú hitelesítést
* Engedély-alapú hitelesítést

### <a name="roles"></a>Szerepkörök
Három szerepkörök állnak rendelkezésre: **rendszergazda**, **tulajdonosa**, és **közreműködői**.  Minden egyes szerepkörhöz, a hatókör és a jogok, amely az alábbi táblázat foglalja össze.

<table><tr><td><b>Szerepkör</b></td><td><b>Hatókör</b></td><td><b>Jogok</b></td></tr><tr><td>Rendszergazda</td><td>Katalógus (az összes eszközök/jegyzet a katalógusban)</td><td>Olvasási Delete ViewRoles

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Tulajdonos</td><td>Minden eszköz (legfelső cikk)</td><td>Olvasási Delete ViewRoles

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Közreműködő</td><td>Minden egyes eszköz és a jegyzet</td><td>Olvasási frissítés törlési ViewRoles Megjegyzés: az összes rights visszavonódnak, ha az olvasási jogosultsággal a cikk a közreműködői van visszavonva</td></tr></table>

> [!NOTE]
> **Olvasási**, **frissítés**, **törlése**, **ViewRoles** jogok vonatkoznak minden olyan elem (eszköz vagy jegyzet) közben **TakeOwnership**, **ChangeOwnership**, **ChangeVisibility**, **ViewPermissions** csak a legfelső szintű eszköz vonatkoznak.
> 
> **Törlés** jobb és minden olyan Szoftverbeállítások vagy alatta egyetlen cikkre vonatkozik. Például egy objektumot is törlésekor bármely adott eszközhöz tartozó jegyzetek.
> 
> 

### <a name="permissions"></a>Engedélyek
Engedély van, mint a hozzáférés-vezérlő bejegyzések listája. Minden egyes hozzáférés-vezérlési bejegyzés egy rendszerbiztonsági jogosultságokkal rendeli hozzá. Engedélyek csak akkor adható meg az eszköz (vagyis a legfelső szintű elem), és az eszköz és bármely elem vonatkoznak.

Során a **Azure Data Catalog** előzetes verzió csak **olvasási** jobb támogatott az adategység láthatóságának korlátozása forgatókönyv engedélyezéséhez engedélyek listájában.

Alapértelmezés szerint a hitelesített felhasználók rendelkezik **olvasási** jobb gombbal a katalógusban szereplő bármely elem, kivéve, ha a láthatósági korlátozódik, az engedélyek a rendszerbiztonsági tagok közül.

## <a name="rest-api"></a>REST API
**PUT** és **POST** nézetelem kérelmek segítségével szabályozhatja a szerepkörök és engedélyek: mellett elem adattartalom a két rendszer tulajdonságok adhatók meg **szerepkörök** és  **engedélyek**.

> [!NOTE]
> **engedélyek** egy legfelső szintű elem csak érvényes.
> 
> **Tulajdonos** szerepkör egy legfelső szintű elem csak érvényes.
> 
> Amikor létrehoznak egy elemet a katalógusban alapértelmezés szerint a **közreműködői** az aktuális hitelesített felhasználó értékre van állítva. Elem lehet frissíthető mindenki számára, ha **közreműködői** értékre kell állítani &lt;mindenki&gt; egyszerű, a speciális biztonsági a **szerepkörök** közzétenni tulajdonság első elem esetén (lásd: az alábbi példához). **Közreműködői** nem módosítható, és ugyanaz marad egy elem élettartama során (még **rendszergazda** vagy **tulajdonosa** nem rendelkezik a jogot arra, hogy módosítsa a **közreműködő**). Az egyetlen érték az explicit beállítást, amely támogatja a **közreműködői** van &lt;mindenki&gt;: **közreműködői** csak egy felhasználó, aki létrehozta egy elemet lehet vagy &lt;mindenki számára &gt;.
> 
> 

### <a name="examples"></a>Példák
**Közreműködői beállítása &lt;mindenki&gt; egy cikk közzétételekor.**
A speciális biztonsági egyszerű &lt;mindenki&gt; objectId "00000000-0000-0000-0000-000000000201" rendelkezik.
  **POST** https://api.azuredatacatalog.com/catalogs/default/views/tables/?api-version=2016-03-30

> [!NOTE]
> Bizonyos ügyfélmegvalósítások esetén HTTP előfordulhat, hogy automatikusan adja ki újból a kiszolgáló válasza egy 302, a kérelmek, de általában sáv engedélyezési fejléceket a kérelemből. Mivel az engedélyezési fejléc szükséges, hogy a kéréseket az Azure Data Cataloghoz, biztosítania kell az engedélyezési fejléc továbbra is biztosított, ha kialakít egy Azure Data Catalog által megadott átirányítási helyet kérelmet. Az alábbi mintakód bemutatja a .NET HttpWebRequest objektum használatával.
> 
> 

**Törzs**

    {
        "roles": [
            {
                "role": "Contributor",
                "members": [
                    {
                        "objectId": "00000000-0000-0000-0000-000000000201"
                    }
                ]
            }
        ]
    }

  **Tulajdonosok hozzárendelése és a egy meglévő legfelső szintű elem láthatóságának korlátozása**: **PUT** https://api.azuredatacatalog.com/catalogs/default/views/tables/042297b0...1be45ecd462a?api-version=2016-03-30

    {
        "roles": [
            {
                "role": "Owner",
                "members": [
                    {
                        "objectId": "c4159539-846a-45af-bdfb-58efd3772b43",
                        "upn": "user1@contoso.com"
                    },
                    {
                        "objectId": "fdabd95b-7c56-47d6-a6ba-a7c5f264533f",
                        "upn": "user2@contoso.com"
                    }
                ]
            }
        ],
        "permissions": [
            {
                "principal": {
                    "objectId": "27b9a0eb-bb71-4297-9f1f-c462dab7192a",
                    "upn": "user3@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            },
            {
                "principal": {
                    "objectId": "4c8bc8ce-225c-4fcf-b09a-047030baab31",
                    "upn": "user4@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            }
        ]
    }

> [!NOTE]
> A PUT ez nem szükséges, adja meg egy elem hasznos a szervezetnek: PUT csak az olyan szerepkörök és/vagy engedélyeit frissítésére is használható.
> 
> 

<!--Image references-->
[1]: ./media/data-catalog-developer-concepts/concept2.png
