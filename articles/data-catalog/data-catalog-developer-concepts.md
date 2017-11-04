---
title: "Data Catalog fejlesztői fogalmak |} Microsoft Docs"
description: "Bevezetés az Azure Data Catalog fogalmi modellben, mint a katalógus REST API-n keresztül elérhetővé az alapvető fogalmakat."
services: data-catalog
documentationcenter: 
author: spelluru
manager: jhubbard
editor: 
tags: 
ms.assetid: 89de9137-a0a4-40d1-9f8d-625acad31619
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 10/15/2017
ms.author: spelluru
ms.openlocfilehash: e3c26c2358c15d18c71b82fe1f389c039ecbd97b
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="azure-data-catalog-developer-concepts"></a>Az Azure Data Catalog fejlesztői fogalmak
Microsoft **Azure Data Catalog** egy teljes körűen felügyelt felhőszolgáltatás, amely az adatforrás-felderítés és a közösségi adatforrások metaadatainak képességeket biztosít. A fejlesztők a a szolgáltatás a REST API-kon keresztül. A fejlesztők számára sikeresen integrálható a fontos modul a szolgáltatás ismertetése **Azure Data Catalog**.

## <a name="key-concepts"></a>Fő fogalmak
A **Azure Data Catalog** fogalmi modell négy alapvető fogalmakat alapul: A **katalógus**, **felhasználók**, **eszközök**, és **jegyzetek**.

![A koncepció][1]

*1. ábra – az Azure Data Catalog egyszerűsített fogalmi modellt*

### <a name="catalog"></a>Alkalmazáskatalógus
A **katalógus** szervezet tárolja az összes metaadat a legfelső szintű tárolója. Van egy **katalógus** egy Azure-fiók lehet. Azure-előfizetéssel, de csak egy katalógusok vannak társítva **katalógus** hozható létre a megadott Azure-fiókra, annak ellenére, hogy a fiók több előfizetéssel is rendelkezhet.

A katalógus tartalmaz **felhasználók** és **eszközök**.

### <a name="users"></a>Felhasználók
Felhasználók, amely rendelkezik jogosultságokkal műveletek végrehajtásához rendszerbiztonsági (a katalógusban, hozzáadásához, szerkesztéséhez vagy távolítsa el az elemeket, stb.) a katalógusban.

Egy felhasználó rendelkezhet több különböző szerepkör is létezik. A szerepkörök további információkért lásd: a szakasz szerepköröket és engedélyezési.

Az egyes felhasználók és biztonsági csoportok is hozzáadhatók.

Az Azure Data Catalog az Azure Active Directory az identitások és hozzáférések felügyeletéhez. Minden egyes Alkalmazáskatalógus felhasználói az Active Directory, a fiók tagjának kell lennie.

### <a name="assets"></a>Objektumok
A **katalógus** adategységeket tartalmazza. **Eszközök** kezeli a katalógus granularitási egység vannak.

Egy eszköz granularitása adatforrás függ. Az SQL Server vagy Oracle-adatbázishoz az eszköz lehet egy táblát vagy nézetet. Az SQL Server Analysis Services az eszköz lehet egy mérték, a dimenzió vagy egy fő teljesítménymutató (KPI). SQL Server Reporting Services egy jelentést.

Egy **eszköz** hozzáadása vagy eltávolítása a katalógust a jelenti. Vissza az eredmény egység **keresési**.

Egy **eszköz** a nevét, helyét, és típusa, és további leíró jegyzetek épül fel.

### <a name="annotations"></a>Jegyzetek
Jegyzetek olyan elemek, amelyek megfelelnek az eszközökre vonatkozó metaadatok.

Jegyzetek többek között a leírás, a címkék, a séma, a dokumentáció, a stb. Az eszköz és a jegyzet típusainak teljes listáját az eszköz objektum modell szakaszban szerepelnek.

## <a name="crowdsourcing-annotations-and-user-perspective-multiplicity-of-opinion"></a>Közösségi jegyzeteket és a felhasználó szempontból (véleményével multiplicitása)
A rendszer kulcsfontosságú az Azure Data Catalog hogyan támogatja a metaadatok közösségi hozzáadása a rendszerben. Egy wiki megközelítésre – ahol csak egy véleményével és a legutolsó író wins – az Azure Data Catalog modell lehetővé teszi, hogy az élő egymás mellett, a rendszer több vélemények adatkötetekkel.

Ez a megközelítés által adott jelentéseket tükrözik a valós életben a ahol más felhasználók is rendelkezik különböző szempontok szerint egy adott eszköz a vállalati adatok:

* Adatbázis-rendszergazda rendelkezhetnek információk a szolgáltatásszint-szerződések, és a rendelkezésre álló feldolgozási időszakában tömeges ETL műveletek
* Egy adatok steward által biztosított információk az üzleti folyamatok, amely az eszköz vonatkozik, és a besorolásokat, az üzleti telepített rá
* Egy pénzügyi elemző is ismertetik, hogyan használja fel az adatok időszak vége jelentéskészítési műveletek során

Ebben a példában támogatása érdekében minden felhasználó – a DBA, az adatok steward és az elemző – olyan leírást adhat hozzá a katalógusban regisztrált egyetlen tábla. A rendszer minden leírás karbantartása, és az Azure Data Catalog portálon jelennek meg minden leírás.

Ezt a mintát úgy, hogy a JSON-adattartalmat az Objektumtípusok gyakran azokhoz a tulajdonságokhoz tömbök egypéldányos várt előfordulhat, hogy érvényes hálózatiobjektum-modellje elemeinek többsége.

Például az eszköz a legfelső szintű leírás objektumokból álló tömb. A tömb tulajdonság neve "leírások". A leírás objektumnak egy tulajdonság - leírás. A minta nem minden felhasználónak, aki típusok leírást a leírás objektum lekérdezi a felhasználó által megadott érték létrehozott.

A UX kiválaszthatja a kombinációja megjelenítése. Nincsenek megjelenítendő három különböző kombinációját.

* A legegyszerűbb minta nem "Minden látszik". Ebben a mintában az összes objektum lista nézetben láthatók. Az Azure Data Catalog-portál UX leírás ezt a mintát használ.
* Egy másik a mintája, "Merge". Ebben a mintában a különböző felhasználók számára az összes érték egyesítve lesznek együtt, az ismétlődő eltávolítva. Ebben a mintában az Azure Data Catalog portálon UX többek között a címkéket és szakértők tulajdonságait.
* A harmadik minta "utolsó író wins". Ebben a mintában csak a legutóbbi értéke adta-e a megjelenik-e. rövid név ebben a mintában példája.

## <a name="asset-object-model"></a>Eszköz hálózatiobjektum-modellje
Alapfogalmak területen jelent a **Azure Data Catalog** hálózatiobjektum-modellt tartalmaz elemeket, amely eszközök vagy széljegyzeteket. Elemek tulajdonság tartozik, amely választható vagy szükséges lehet. Minden elemre érvényes néhány tulajdonságát. Egyes tulajdonságok alkalmazása az összes eszköz. Néhány tulajdonság csak az adott eszköz típusok vonatkoznak.

### <a name="system-properties"></a>Rendszertulajdonságok
<table><tr><td><b>Tulajdonság neve</b></td><td><b>Adattípus</b></td><td><b>Megjegyzések</b></td></tr><tr><td>időbélyeg</td><td>Dátum és idő</td><td>A legutóbbi elem módosítva lett. Ez a mező a kiszolgáló által létrehozott, ha egy elem szerepel, és minden alkalommal, amikor frissül egy elemet. Ez a tulajdonság értékét a rendszer figyelmen kívül hagyja a bemeneti műveletek közzététele.</td></tr><tr><td>id</td><td>URI</td><td>Az elem (csak olvasható) abszolút URL-címe Az elem egyedi címmel rendelkező URI-JÁNAK.  Ez a tulajdonság értékét a rendszer figyelmen kívül hagyja a bemeneti műveletek közzététele.</td></tr><tr><td>type</td><td>Karakterlánc</td><td>(Csak olvasható) az eszköz típusa.</td></tr><tr><td>ETag</td><td>Karakterlánc</td><td>Egy olyan karakterlánc, amely a katalógus elemeinek frissítő műveletek végrehajtása során az egyidejű hozzáférések optimista vezérlését használt verzió. "*" értékek egyeztetéséhez használható.</td></tr></table>

### <a name="common-properties"></a>Általános tulajdonságai
Minden legfelső szintű eszköz és az összes jegyzetet típusainak alkalmazni ezeket a tulajdonságokat.

<table>
<tr><td><b>Tulajdonság neve</b></td><td><b>Adattípus</b></td><td><b>Megjegyzések</b></td></tr>
<tr><td>fromSourceSystem</td><td>Logikai érték</td><td>Azt jelzi, hogy elem adatait (például az Sql Server-adatbázis, Oracle-adatbázishoz) a forrásrendszerben származó vagy felhasználó által létrehozott.</td></tr>
</table>

### <a name="common-root-properties"></a>A gyökérszintű általános tulajdonságai
<p>
Minden legfelső szintű eszköz esetében alkalmazni ezeket a tulajdonságokat.

<table><tr><td><b>Tulajdonság neve</b></td><td><b>Adattípus</b></td><td><b>Megjegyzések</b></td></tr><tr><td>név</td><td>Karakterlánc</td><td>Az adatforrás hely adatait származó nevét</td></tr><tr><td>DSL</td><td>DataSourceLocation</td><td>Egyedi írja le az adatforrás, és az eszköz azonosítók egyike. (Lásd a kettős identitási szakaszban).  A dsl szerkezete a protokoll és a forrás típusa platformonként változó.</td></tr><tr><td>Adatforrás</td><td>Érhető el</td><td>További információkhoz juthat az eszköz típusától.</td></tr><tr><td>lastRegisteredBy</td><td>SecurityPrincipal</td><td>Ez az eszköz legutóbb regisztrált felhasználó ismerteti.  Mindkét az egyedi azonosítót (Keresztnév és Vezetéknév) egy megjelenítési nevet és a felhasználót (upn) tartalmazza.</td></tr><tr><td>Tárolóazonosító</td><td>Karakterlánc</td><td>A tároló eszköz az adatforrás-azonosítója. Ez a tulajdonság nem támogatott a tároló típushoz.</td></tr></table>

### <a name="common-non-singleton-annotation-properties"></a>Közös nem egypéldányos jegyzet tulajdonságai
Ezeket a tulajdonságokat mindenfajta nem egypéldányos megjegyzés vonatkozik (jegyzeteket, amely lehet több eszközönként).

<table>
<tr><td><b>Tulajdonság neve</b></td><td><b>Adattípus</b></td><td><b>Megjegyzések</b></td></tr>
<tr><td>kulcs</td><td>Karakterlánc</td><td>A felhasználó által megadott kulcs, amely egyedileg azonosítja a Megjegyzés a jelenlegi gyűjteményben. A kulcs hossza nem lehet hosszabb 256 karakternél.</td></tr>
</table>

### <a name="root-asset-types"></a>Legfelső szintű eszköz típusa
Legfelső szintű eszköz azokat a különféle is a katalógusban regisztrált adategységeket képviselő típusok. Minden legfelső szintű típushoz nincs nézet, amely ismerteti az eszköz és a jegyzetek a nézetben. Nézet neve a megfelelő {view_name} URL-szegmenseket REST API-n keresztül közzétételekor használandó.

<table><tr><td><b>Eszköz típusa (nézet neve)</b></td><td><b>További tulajdonságok</b></td><td><b>Adattípus</b></td><td><b>Engedélyezett jegyzetek</b></td><td><b>Megjegyzések</b></td></tr><tr><td>A tábla ("tábla")</td><td></td><td></td><td>Leírás<p>Rövid név<p>Címke<p>Séma<p>ColumnDescription<p>ColumnTag<p> szakértői<p>Előzetes verzió<p>AccessInstruction<p>TableDataProfile<p>ColumnDataProfile<p>ColumnDataClassification<p>Dokumentáció<p></td><td>Egy tábla táblázatos adatokat jelöli.  Például: SQL-tábla, az SQL-nézet, Analysis Services táblázatos tábla, Analysis Services többdimenziós dimenzió, Oracle-tábla stb.   </td></tr><tr><td>A mérték ("intézkedések")</td><td></td><td></td><td>Leírás<p>Rövid név<p>Címke<p>szakértői<p>AccessInstruction<p>Dokumentáció<p></td><td>Ez a típus jelképezi egy Analysis Services-mértéket.</td></tr><tr><td></td><td>mértékcsoport</td><td>Oszlop</td><td></td><td>A mérték leíró metaadatok</td></tr><tr><td></td><td>isCalculated </td><td>Logikai érték</td><td></td><td>Itt adhatja meg, ha a mérték kiszámítása vagy sem.</td></tr><tr><td></td><td>MeasureGroup</td><td>Karakterlánc</td><td></td><td>A mérték fizikai tároló</td></tr><td>KPI-t ("KPI-k")</td><td></td><td></td><td>Leírás<p>Rövid név<p>Címke<p>szakértői<p>AccessInstruction<p>Dokumentáció</td><td></td></tr><tr><td></td><td>MeasureGroup</td><td>Karakterlánc</td><td></td><td>A mérték fizikai tároló</td></tr><tr><td></td><td>goalExpression</td><td>Karakterlánc</td><td></td><td>Numerikus MDX-kifejezés vagy a számítás, amely a KPI cél értékét adja vissza.</td></tr><tr><td></td><td>valueExpression</td><td>Karakterlánc</td><td></td><td>MDX numerikus kifejezés a KPI tényleges értékét adja vissza.</td></tr><tr><td></td><td>statusExpression</td><td>Karakterlánc</td><td></td><td>MDX-kifejezés, amely egy megadott időpontban KPI állapotát jeleníti meg időben.</td></tr><tr><td></td><td>trendExpression</td><td>Karakterlánc</td><td></td><td>MDX-kifejezés, amely kiértékeli a KPI értékének adott idő alatt. A trend minden időalapú nézeteket, amelyek egy adott üzleti környezetben hasznos lehet.</td>
<tr><td>A jelentés ("jelentés")</td><td></td><td></td><td>Leírás<p>Rövid név<p>Címke<p>szakértői<p>AccessInstruction<p>Dokumentáció<p></td><td>Ez a típus jelképezi egy SQL Server Reporting Services jelentés </td></tr><tr><td></td><td>assetCreatedDate</td><td>Karakterlánc</td><td></td><td></td></tr><tr><td></td><td>assetCreatedBy</td><td>Karakterlánc</td><td></td><td></td></tr><tr><td></td><td>assetModifiedDate</td><td>Karakterlánc</td><td></td><td></td></tr><tr><td></td><td>assetModifiedBy</td><td>Karakterlánc</td><td></td><td></td></tr><tr><td>A tároló ("tárolók")</td><td></td><td></td><td>Leírás<p>Rövid név<p>Címke<p>szakértői<p>AccessInstruction<p>Dokumentáció<p></td><td>Ez a típus jelképezi más eszközök, például az SQL-adatbázis, az Azure BLOB-tároló vagy egy Analysis Services-modell tárolója.</td></tr></table>

### <a name="annotation-types"></a>Jegyzet típusok
Jegyzet típusok típusú belül a katalógus más típusúra rendelt metaadatok jelölik.

<table>
<tr><td><b>Jegyzet típusát (beágyazott nézet neve)</b></td><td><b>További tulajdonságok</b></td><td><b>Adattípus</b></td><td><b>Megjegyzések</b></td></tr>

<tr><td>Leírás ("leírások")</td><td></td><td></td><td>Ez a tulajdonság az adott eszköz számára leírását tartalmazza. A rendszer minden felhasználó saját leírást adhat hozzá.  Csak a felhasználó módosíthatja a leírást.  (A rendszergazdák és az eszköz tulajdonosait a leírás objektum törlését, azonban nem szerkeszthető). A rendszer külön-külön fenntartja a felhasználói leírása.  Így nincs minden eszköz (egy mindegyik felhasználó hozzájárult, valószínűleg egy, az adatforrásból származó adatokat tartalmazó mellett az eszköz a ismerete) leírások tömbjét.</td></tr>
<tr><td></td><td>leírás</td><td>Karakterlánc</td><td>Az eszköz rövid leírása (sorok 2-3)</td></tr>

<tr><td>Címke ("címkék")</td><td></td><td></td><td>Ez a tulajdonság határozza meg egy címkét az adott eszköz számára. A rendszer minden felhasználóhoz az adott eszköz számára több címke adhat hozzá.  Csak a címke objektumok létrehozó felhasználó szerkesztheti azokat.  (A rendszergazdák és az eszköz tulajdonosait a címke objektum törlését, azonban nem szerkeszthető). A rendszer fenntartja a felhasználói címkék külön-külön.  Így nincs minden eszköz a kód objektumokból álló tömb.</td></tr>
<tr><td></td><td>Címke</td><td>Karakterlánc</td><td>Az eszköz leíró címkét.</td></tr>

<tr><td>Rövid név ("friendlyName")</td><td></td><td></td><td>Ez a tulajdonság egy eszköz rövid nevét tartalmazza. FriendlyName egy egypéldányos Megjegyzés – az eszköz csak egy rövid név is hozzáadhatók.  Csak a rövid név objektumot létrehozó felhasználó szerkeszthető. (A rendszergazdák és az eszköz tulajdonosait a rövid név objektum törlését, azonban nem szerkeszthető). A rendszer külön-külön kezeli a felhasználó rövid neve.</td></tr>
<tr><td></td><td>Rövid név</td><td>Karakterlánc</td><td>Az eszköz rövid neve.</td></tr>

<tr><td>A séma ("schema")</td><td></td><td></td><td>A séma ismerteti az adatok szerkezete.  Az attribútum (oszlop, attribútum, mező, stb.) neve, meg kell adnia, valamint egyéb metaadatokat.  Ezeket az adatokat az adatforrásból származik.  Séma egy egypéldányos megjegyzés – csak egy séma felveheti az adott eszköz számára.</td></tr>
<tr><td></td><td>oszlopok</td><td>[Oszlop]</td><td>Egy oszlop objektumokból álló tömb. Az oszlop leírják az adatforrásból származó információkkal.</td></tr>

<tr><td>ColumnDescription ("columnDescriptions")</td><td></td><td></td><td>Ez a tulajdonság egy oszlop leírását tartalmazza.  A rendszer minden felhasználó saját leírása több oszlop (legfeljebb egyetlen oszloponként) adhat hozzá. Csak a ColumnDescription objektumok létrehozó felhasználó szerkesztheti azokat.  (A rendszergazdák és az eszköz tulajdonosait a ColumnDescription objektum törlését, azonban nem szerkeszthető). A rendszer külön kezeli a felhasználó az oszlopok leírása.  Így nincs minden eszköz (szövegmezőnként egy oszlop összes felhasználója számára azok mellett valószínűleg egy, az adatforrásból származó adatokat tartalmazó oszlop ismerete hozzájárult) ColumnDescription objektumokból álló tömb.  A ColumnDescription lazán van kötve a séma, így szinkronban kérheti le. Egy oszlop, amely már nem létezik a sémában a ColumnDescription ír le.  Az író leírását és séma szinkronban tartásához esetén.  Az adatforrás is oszlopok leírása információk és további ColumnDescription objektumokat, amely az eszköz futtatásakor létrejön.</td></tr>
<tr><td></td><td>Oszlopnév</td><td>Karakterlánc</td><td>A leírást hivatkozik az oszlop neve.</td></tr>
<tr><td></td><td>leírás</td><td>Karakterlánc</td><td>egy rövid (sorok 2-3) az oszlop leírása.</td></tr>

<tr><td>ColumnTag ("columnTags")</td><td></td><td></td><td>Ez a tulajdonság egy oszlop címkét tartalmaz. A rendszer minden felhasználó adhat hozzá az adott oszlop több címke, és több oszlop címkéket adhat hozzá. Csak a ColumnTag objektumok létrehozó felhasználó szerkesztheti azokat. (A rendszergazdák és az eszköz tulajdonosait a ColumnTag objektum törlését, azonban nem szerkeszthető). A rendszer fenntartja a felhasználók oszlop címkék külön-külön.  Így nincs minden eszköz ColumnTag objektumokból álló tömb.  A ColumnTag lazán van kötve a séma, így szinkronban kérheti le. Egy oszlop, amely már nem létezik a sémában a ColumnTag ír le.  Az író szinkronban tartsa az oszlop címke és séma esetén.</td></tr>
<tr><td></td><td>Oszlopnév</td><td>Karakterlánc</td><td>A címke hivatkozik az oszlop neve.</td></tr>
<tr><td></td><td>Címke</td><td>Karakterlánc</td><td>Az oszlop leíró címkét.</td></tr>

<tr><td>A szakértő ("szakértők")</td><td></td><td></td><td>Ez a tulajdonság egy felhasználót, aki a következő adatkészletben szakértő tekinthető tartalmazza. A szakértői opinions(descriptions) buborék a UX leírások listázásakor tetején. Minden felhasználó megadhatja a saját szakértői. Csak a felhasználó módosíthatja a szakértői. (A rendszergazdák és az eszköz tulajdonosait a szakértői objektumok törlése, de nem szerkeszthető).</td></tr>
<tr><td></td><td>szakértői</td><td>SecurityPrincipal</td><td></td></tr>

<tr><td>Előzetes verzió ("előzetes verziójú funkciók")</td><td></td><td></td><td>Az előzetes kiadásban az adatok az eszköz az első 20 sorok pillanatkép tartalmazza. Előzetes verzió csak a célszerű bizonyos típusú eszközöket (érdemes táblához, de a mérték nem).</td></tr>
<tr><td></td><td>Előzetes verzió</td><td>[objektum]</td><td>Egy oszlop képviselő objektumok tömbje.  Minden objektum rendelkezik egy tulajdonság a leképezés csak a sor az adott oszlop értékét tartalmazó oszlop.</td></tr>

<tr><td>AccessInstruction ("accessInstructions")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>mimeType</td><td>Karakterlánc</td><td>A mime-típusát a tartalmat.</td></tr>
<tr><td></td><td>Tartalom</td><td>Karakterlánc</td><td>Hogyan férhetnek hozzá az adatok eszközre vonatkozó utasításokat. A tartalom URL-címet, az e-mail címet vagy utasítások lehet.</td></tr>

<tr><td>TableDataProfile ("tableDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>numberOfRows</td></td><td>int</td><td>A következő adatkészletben sorok száma</td></tr>
<tr><td></td><td>Méret</td><td>hosszú</td><td>Az adatkészlet bájtban mérete.  </td></tr>
<tr><td></td><td>schemaModifiedTime</td><td>Karakterlánc</td><td>A séma módosításának időpontja</td></tr>
<tr><td></td><td>dataModifiedTime</td><td>Karakterlánc</td><td>Az adatkészlet módosításának időpontja (adatok lett hozzáadva, módosított vagy törlése)</td></tr>

<tr><td>ColumnsDataProfile ("columnsDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>oszlopok</td></td><td>ColumnDataProfile]</td><td>Egy oszlop adatainak profilok tömbjét.</td></tr>

<tr><td>ColumnDataClassification ("columnDataClassifications")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Oszlopnév</td><td>Karakterlánc</td><td>Ez a besorolás hivatkozik az oszlop neve.</td></tr>
<tr><td></td><td>Besorolás</td><td>Karakterlánc</td><td>Az ebben az oszlopban az adatok besorolását.</td></tr>

<tr><td>A dokumentáció ("dokumentáció")</td><td></td><td></td><td>Egy adott eszköz lehet társítva csak egy dokumentációját.</td></tr>
<tr><td></td><td>mimeType</td><td>Karakterlánc</td><td>A mime-típusát a tartalmat.</td></tr>
<tr><td></td><td>Tartalom</td><td>Karakterlánc</td><td>A dokumentáció tartalmat.</td></tr>

</table>

### <a name="common-types"></a>Általános típusok
Általános tulajdonságok a típusokkal használható, de nincsenek elemek.

<table>
<tr><td><b>Közös típusa</b></td><td><b>Tulajdonságok</b></td><td><b>Adattípus</b></td><td><b>Megjegyzések</b></td></tr>
<tr><td>Érhető el</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Forrástípus</td><td>Karakterlánc</td><td>Az adatforrás típusát.  Például: SQL Server, az Oracle-adatbázishoz, stb.  </td></tr>
<tr><td></td><td>Objektumtípus</td><td>Karakterlánc</td><td>Az adatforrás-objektum típusának leírása. Például: Table, és tekintse meg az SQL Server.</td></tr>

<tr><td>DataSourceLocation</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Protokoll</td><td>Karakterlánc</td><td>Kötelező. Ismerteti az adatforráshoz való kommunikációhoz használt protokoll. Például: "tds" SQl-kiszolgáló, "oracle" Oracle, stb. Tekintse meg [adatforrás-hivatkozás megadását - DSL struktúra](data-catalog-dsr.md) a jelenleg támogatott protokollok listáját.</td></tr>
<tr><td></td><td>Cím</td><td>Könyvtár<string, object></td><td>Kötelező. A protokoll, amely azonosítja a hivatkozott adatforrás jellemző adatok-cím tartozik. A cím hatókörét egy adott protokoll, ami azt jelenti, hogy adata értelmetlen a protokoll ismerete nélkül.</td></tr>
<tr><td></td><td>Hitelesítés</td><td>Karakterlánc</td><td>Választható. Az adatforráshoz való kommunikációhoz használt hitelesítési séma. Például: windows, az oauth, stb.</td></tr>
<tr><td></td><td>connectionProperties</td><td>Könyvtár<string, object></td><td>Választható. További tájékoztatást az adatforráshoz való kapcsolódás.</td></tr>

<tr><td>SecurityPrincipal</td><td></td><td></td><td>A háttérrendszer adatellenőrzése nem bármely elleni aad-ben megadott tulajdonságok közzététele során.</td></tr>
<tr><td></td><td>egyszerű felhasználónév</td><td>Karakterlánc</td><td>Felhasználó egyedi e-mail címét. Meg kell adni, ha objectId nem áll rendelkezésre, vagy a "lastRegisteredBy" tulajdonság, ellenkező esetben nem kötelező a környezetében.</td></tr>
<tr><td></td><td>Objektumazonosító</td><td>GUID</td><td>Felhasználó vagy biztonsági csoport AAD-identitása. Választható. Kötelező megadni, ha egyszerű felhasználónév nincs megadva, ellenkező esetben nem kötelező.</td></tr>
<tr><td></td><td>Utónév</td><td>Karakterlánc</td><td>Utónév felhasználó (megjelenítési célokra). Választható. Csak a "lastRegisteredBy" tulajdonság a környezetben érvényes. Nem lehet megadni, ha a rendszerbiztonsági tag megadása "szerepkörök", "engedélyek" és "szakértők".</td></tr>
<tr><td></td><td>Vezetéknév</td><td>Karakterlánc</td><td>Utolsó felhasználó neve (megjelenítési célokra). Választható. Csak a "lastRegisteredBy" tulajdonság a környezetben érvényes. Nem lehet megadni, ha a rendszerbiztonsági tag megadása "szerepkörök", "engedélyek" és "szakértők".</td></tr>

<tr><td>Oszlop</td><td></td><td></td><td></td></tr>
<tr><td></td><td>név</td><td>Karakterlánc</td><td>Az oszlop vagy az attribútum nevét.</td></tr>
<tr><td></td><td>type</td><td>Karakterlánc</td><td>az oszlop vagy az attribútum adattípusa. Az engedélyezett típusok adatok forrástípus az eszköz függ.  A támogatott típusok csak egy részét.</td></tr>
<tr><td></td><td>MaxLength</td><td>int</td><td>Az oszlop vagy attribútum megengedett maximális hosszúságot. Az adatforrás származik. Néhány adatforrás típusok csak érvényes.</td></tr>
<tr><td></td><td>Pontosság</td><td>Bájt</td><td>Az oszlop vagy az attribútum a pontosság. Az adatforrás származik. Néhány adatforrás típusok csak érvényes.</td></tr>
<tr><td></td><td>isNullable</td><td>Logikai érték</td><td>Az oszlopban engedélyezett-e null értékűnek lennie, vagy nem. Az adatforrás származik. Néhány adatforrás típusok csak érvényes.</td></tr>
<tr><td></td><td>kifejezés</td><td>Karakterlánc</td><td>Ha az érték egy számított oszlop, ebben a mezőben a kifejezést tartalmaz, amely kifejezze az értéket. Az adatforrás származik. Néhány adatforrás típusok csak érvényes.</td></tr>

<tr><td>ColumnDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Oszlopnév </td><td>Karakterlánc</td><td>Az oszlop neve</td></tr>
<tr><td></td><td>type </td><td>Karakterlánc</td><td>Az oszlop típusa</td></tr>
<tr><td></td><td>perc </td><td>Karakterlánc</td><td>A minimális érték a következő adatkészletben</td></tr>
<tr><td></td><td>maximális </td><td>Karakterlánc</td><td>A maximális érték a következő adatkészletben</td></tr>
<tr><td></td><td>átlagos </td><td>Dupla</td><td>A következő adatkészletben átlagos érték</td></tr>
<tr><td></td><td>szórás </td><td>Dupla</td><td>A következő adatkészletnél a szórás</td></tr>
<tr><td></td><td>nullCount </td><td>int</td><td>A következő adatkészletben null értékek száma</td></tr>
<tr><td></td><td>distinctCount  </td><td>int</td><td>A különböző értékeket a következő adatkészletben száma</td></tr>


</table>

## <a name="asset-identity"></a>Eszköz identitása
Az Azure Data Catalog "protokoll" és azonosító tulajdonságot használják az "address" tulajdonságcsomag DataSourceLocation "dsl" tulajdonságának az identitás, az eszköz, amely az eszköz a katalógus belül használja létrehozásához.
Például a "tds" protokoll identitása Tulajdonságok "kiszolgáló", "adatbázis", "schema" és "objektum" rendelkezik. A protokoll és a Tulajdonságok kombinációi az identitás, az SQL Server tábla eszköz létrehozásához használt.
Az Azure Data Catalog biztosít több beépített forrás protokollok, amelyek leírása megtalálható a [adatforrás-hivatkozás megadását - DSL struktúra](data-catalog-dsr.md).
A támogatott protokollok készletét programozott módon is terjeszthető (tekintse meg a Data Catalog REST API-referencia). A katalógus rendszergazdák regisztrálhatja az egyéni forrás protokollokat. A következő táblázat ismerteti az egyéni protokoll regisztrációjához szükséges tulajdonságait.

### <a name="custom-data-source-protocol-specification"></a>Egyéni adatok forrás protokoll specifikációja
<table>
<tr><td><b>Típus</b></td><td><b>Tulajdonságok</b></td><td><b>Adattípus</b></td><td><b>Megjegyzések</b></td></tr>

<tr><td>DataSourceProtocol</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Namespace</td><td>Karakterlánc</td><td>A protokoll névtere. Namespace lehet 1-255 karakter hosszú, tartalmaz legalább egy nem üres pontot (.) elválasztva. Minden egyes részben 1-255 karakter hosszú lehet, betűvel kezdődhet és csak betűket és számokat tartalmazhat.</td></tr>
<tr><td></td><td>név</td><td>Karakterlánc</td><td>A protokoll neve. Neve 1-255 karakter hosszú lehet, betűvel kezdődhet és csak betűket, számokat és kötőjel (-) karaktert tartalmazhat.</td></tr>
<tr><td></td><td>identityProperties</td><td>DataSourceProtocolIdentityProperty]</td><td>Azonosító tulajdonságainak listája, tartalmaznia kell legalább egy, de nem több mint 20 tulajdonságait. Például: "kiszolgáló", "database", "schema", "object" a "tds" protokoll identitása tulajdonságainak.</td></tr>
<tr><td></td><td>identitySets</td><td>DataSourceProtocolIdentitySet]</td><td>Beállítja a identitás listája. Meghatározza a határoz meg érvényes eszköznév identitás identitás tulajdonságainak beállítása. Tartalmaznia kell legalább egy, de nem több mint 20 beállítása. Például: {"kiszolgáló", "adatbázis", "schema" és "objektum"} "tds" protokoll, amely határozza meg az Sql Server tábla eszköz identitásának beállítása identitást.</td></tr>

<tr><td>DataSourceProtocolIdentityProperty</td><td></td><td></td><td></td></tr>
<tr><td></td><td>név</td><td>Karakterlánc</td><td>A tulajdonság nevét. Neve 1 és 100 karakter hosszú, betűvel kezdődik kell, és csak betűket és számokat tartalmazhat.</td></tr>
<tr><td></td><td>type</td><td>Karakterlánc</td><td>A tulajdonság típusát. Támogatott értékek: "logikai" boolean ","bájt","guid","int","integer","hosszú","string","url"</td></tr>
<tr><td></td><td>az ignoreCase</td><td>logikai érték</td><td>Azt jelzi, hogy esetben figyelmen kívül kell tulajdonság értékét használja. Csak adható meg a "string" típusú tulajdonságokhoz. Alapértelmezett értéke hamis.</td></tr>
<tr><td></td><td>urlPathSegmentsIgnoreCase</td><td>logikai]</td><td>Azt jelzi, hogy esetben figyelmen kívül hagyja az egyes szegmens az URL-cím elérési út. Csak adható meg "url" típusú tulajdonságokhoz. Alapértelmezett érték: [false].</td></tr>

<tr><td>DataSourceProtocolIdentitySet</td><td></td><td></td><td></td></tr>
<tr><td></td><td>név</td><td>Karakterlánc</td><td>Az identitás-készlet nevét.</td></tr>
<tr><td></td><td>properties</td><td>String]</td><td>Ezzel az identitással bekerült azonosító tulajdonságainak listája meg. Nem tartalmazhat ismétlődéseket. Minden egyes tulajdonsága identitás készlet által hivatkozott "identityProperties" protokoll listájának definiálni kell.</td></tr>

</table>

## <a name="roles-and-authorization"></a>Szerepkörök és engedélyezés
Microsoft Azure Data Catalog eszközökre és a jegyzetek CRUD műveleteihez engedélyezési képességeket biztosít.

## <a name="key-concepts"></a>Fő fogalmak
Az Azure Data Catalog kétféle hitelesítési módszer használ:

* Szerepköralapú hitelesítés
* Engedély-alapú engedélyezési

### <a name="roles"></a>Szerepkörök
Három szerepkör: **rendszergazda**, **tulajdonos**, és **közreműködő**.  Minden szerepkörhöz, a hatókör és a jogok, amely a következő táblázat tartalmazza.

<table><tr><td><b>Szerepkör</b></td><td><b>Hatókör</b></td><td><b>Jogok</b></td></tr><tr><td>Rendszergazda</td><td>Katalógus (eszközök/szereplő összes megjegyzést a katalógus)</td><td>Olvasási Delete ViewRoles

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Tulajdonos</td><td>Minden eszköz (legfelső szintű elem)</td><td>Olvasási Delete ViewRoles

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Közreműködő</td><td>Minden egyes eszköz és a jegyzet</td><td>Olvasási frissítés törlési ViewRoles Megjegyzés: a jogok vissza lenne vonva, ha a olvasási jogosultsággal a cikk a visszavonja a közreműködői a</td></tr></table>

> [!NOTE]
> **Olvasási**, **frissítés**, **törlése**, **ViewRoles** jogok a következők tetszőleges elemre (eszköz vagy jegyzet) során alkalmazandó **TakeOwnership**, **ChangeOwnership**, **ChangeVisibility**, **ViewPermissions** esetében csak a legfelső szintű eszköz alkalmazhatók.
> 
> **Törlés** jobb és bármely alelemeket vagy alatta egyetlen cikkre vonatkozik. Például egy eszköz is törlésekor a jegyzeteket az adott eszköz számára.
> 
> 

### <a name="permissions"></a>Engedélyek
Engedély van, mint a hozzáférés-vezérlő bejegyzések listája. Minden egyes hozzáférés-vezérlési bejegyzés jogosultsága ahhoz, hogy a rendszerbiztonsági tagok készletét rendeli hozzá. Engedélyeket csak az eszköz (Ez azt jelenti, hogy a gyökérelem) adható meg, és az eszköz és bármely alelemeket vonatkoznak.

Során a **Azure Data Catalog** csak előzetes **olvasási** jobbra látható-e az eszköz korlátozása terület engedélyezéséhez az engedélyek listájában támogatott.

Alapértelmezés szerint minden hitelesített felhasználó rendelkezik **olvasási** a katalógus valamely elemére a jobb gombbal, kivéve, ha látható a rendszer az engedélyek rendszerbiztonsági tagok készletét.

## <a name="rest-api"></a>REST API
**PUT** és **POST** eleme kérelmek segítségével szabályozhatja a szerepkörökről és engedélyekről: mellett elem adattartalom a két rendszer tulajdonságok is megadhatók **szerepkörök** és **engedélyek**.

> [!NOTE]
> **engedélyek** csak a legfelső szintű elem érvényes.
> 
> **Tulajdonos** szerepkör csak a legfelső szintű elem érvényes.
> 
> Egy elem a katalógus létrehozásakor alapértelmezés szerint a **közreműködő** jelenleg hitelesített felhasználó értékre van állítva. Ha kell, hogy a cikk frissíthető mindenki számára, **közreműködő** meg &lt;mindenki&gt; különleges rendszerbiztonsági tag a a **szerepkörök** tulajdonságot, ha az első elem közzétéve (lásd az alábbi példában). **Közreműködő** nem módosítható, és az elem élettartama során változatlan marad (még akkor is **rendszergazda** vagy **tulajdonos** módosításához nem rendelkezik a **közreműködő**). Csak a következő érték támogatott explicit beállításának a **közreműködő** van &lt;mindenki&gt;: **közreműködő** csak egy elemet létrehozó felhasználó lehet vagy &lt;mindenki&gt;.
> 
> 

### <a name="examples"></a>Példák
**Közreműködő beállítása &lt;mindenki&gt; cikk közzétételekor.**
Különleges rendszerbiztonsági tag &lt;mindenki&gt; objectId "00000000-0000-0000-0000-000000000201" rendelkezik.
  **POST** https://api.azuredatacatalog.com/catalogs/default/views/tables/?api-version=2016-03-30

> [!NOTE]
> Bizonyos ügyfélmegvalósítások esetén HTTP előfordulhat, hogy automatikusan újból kiadja azokat a kérelmeket a kiszolgálóról a 302 válaszként, de általában sáv engedélyezési fejléceket a kérelemből. Mivel hitelesítési fejlécéhez szükséges a kérelmeket az Azure Data Cataloghoz, úgy kell beállítania, hitelesítési fejlécéhez újra kibocsátani egy Azure Data Catalog átirányítási helyére vonatkozó kérelmet továbbra is biztosított. Az alábbi példakód mutatja be, a .NET HttpWebRequest objektum használatával.
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

  **Rendelje hozzá a tulajdonosok és korlátozható a láthatóságuk egy meglévő legfelső szintű elem**: **PUT** https://api.azuredatacatalog.com/catalogs/default/views/tables/042297b0...1be45ecd462a?api-version=2016-03-30

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
> A PUT nem kötelező elem payloadot meg a törzsben szereplő: PUT csak szerepkörök és/vagy frissítésére is használható.
> 
> 

<!--Image references-->
[1]: ./media/data-catalog-developer-concepts/concept2.png
