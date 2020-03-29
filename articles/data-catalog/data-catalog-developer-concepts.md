---
title: Az Azure Data Catalog fejlesztői nek fogalmai
description: Az Azure Data Catalog fogalmi modelljének legfontosabb fogalmai, a katalógus REST API-n keresztül elérhetővé.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 80adc98255cfc9145d583ac775bbc490d599234e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68976831"
---
# <a name="azure-data-catalog-developer-concepts"></a>Az Azure Data Catalog fejlesztői nek fogalmai
A Microsoft **Azure Data Catalog** egy teljes körűen felügyelt felhőszolgáltatás, amely az adatforrások felderítésére és a crowdsourcing adatforrás-metaadatokra is képes. A fejlesztők a REST API-kon keresztül használhatják a szolgáltatást. A szolgáltatásban megvalósított fogalmak megértése fontos a fejlesztők számára, hogy sikeresen integrálódjanak az **Azure Data Catalog-ba.**

## <a name="key-concepts"></a>Fő fogalmak
Az **Azure Data Catalog** fogalmi modellje négy fő koncepción alapul: a **katalóguson**, **a felhasználókon**, **az eszközökön**és a **jegyzeteken.**

![Az Azure Data Catalog koncepcionális modellének illusztrációja](./media/data-catalog-developer-concepts/concept2.png)

*1. ábra – Az Azure Data Catalog egyszerűsített fogalmi modellje*

### <a name="catalog"></a>Katalógus
A **katalógus** a szervezet által tárolandó összes metaadat legfelső szintű tárolója. Egy **katalógus** engedélyezett Azure-fiókonként. A katalógusok egy Azure-előfizetéshez vannak kötve, de csak egy **katalógus** hozható létre egy adott Azure-fiókhoz, még akkor is, ha egy fiók több előfizetéssel is rendelkezhet.

A katalógus **a Felhasználók** és **eszközök**elemet tartalmazza.

### <a name="users"></a>Felhasználók
A felhasználók olyan rendszerbiztonsági tagok, akik jogosultak műveletek végrehajtására (keresés a katalógusban, elemek hozzáadása, szerkesztése vagy eltávolítása stb.) a katalógusban.

A felhasználó nak több különböző szerepköre lehet. A szerepkörökkel kapcsolatos további információt a Szerepkörök és engedélyezés című szakaszban talál.

Egyéni felhasználók és biztonsági csoportok is hozzáadhatók.

Az Azure Data Catalog az Azure Active Directoryt használja az identitás- és hozzáférés-kezeléshez. Minden katalógusfelhasználónak a fiók Active Directoryjának tagjának kell lennie.

### <a name="assets"></a>Objektumok
A **katalógus** adateszközöket tartalmaz. **Az eszközök** a katalógus által kezelt részletességi egység.

Az eszköz részletessége adatforrásonként változik. SQL Server vagy Oracle Database esetén az eszköz lehet tábla vagy nézet. Az SQL Server Analysis Services esetében az eszköz lehet mérték, dimenzió vagy fő teljesítménymutató (KPI). Az SQL Server Reporting Services esetében az eszköz jelentés.

Az **eszköz** az a dolog, amelyet hozzáad vagy eltávolít a katalógusból. Ez az egység az eredmény kapsz vissza **keresés**.

**Az eszköz** neve, helye és típusa, valamint további leírására alkalmas jegyzetek alkotják az eszközt.

### <a name="annotations"></a>Széljegyzetek
A jegyzetek olyan elemek, amelyek az eszközök metaadatait jelölik.

A jegyzetek közé tartoznak például a leírás, a címkék, a séma, a dokumentáció stb. Az eszköztípusok és a jegyzettípusok teljes listája az Eszközobjektum-modell szakaszban található.

## <a name="crowdsourcing-annotations-and-user-perspective-multiplicity-of-opinion"></a>Crowdsourcing-jegyzetek és felhasználói perspektíva (a vélemények sokfélesége)
Az Azure Data Catalog egyik legfontosabb szempontja, hogy hogyan támogatja a metaadatok crowdsourcing a rendszerben. Ellentétben a wiki megközelítés – ahol csak egy vélemény, és az utolsó író nyer – az Azure Data Catalog modell lehetővé teszi, hogy több vélemény él egymás mellett a rendszerben.

Ez a megközelítés a vállalati adatok valós világát tükrözi, ahol a különböző felhasználók eltérő perspektívával rendelkezhetnek egy adott eszközre vonatkozóan:

* Az adatbázis-kezelő tájékoztatást adhat a szolgáltatásiszint-szerződésekről vagy a tömeges ETL-műveletekhez rendelkezésre álló feldolgozási ablakról
* Az adatgondnok információkat adhat meg azokról az üzleti folyamatokról, amelyekre az eszköz vonatkozik, vagy a vállalkozás által alkalmazott besorolásokról.
* A pénzügyi elemző tájékoztatást adhat arról, hogyan használják fel az adatokat az időszak végi jelentési feladatok során

Ebben a példában minden felhasználó – a DBA, az adatgondnok és az elemző – leírást adhat a katalógusban regisztrált egyetlen táblához. Az összes leírás megmarad a rendszerben, és az Azure Data Catalog portálon minden leírás jelenik meg.

Ez a minta az objektummodell legtöbb elemére vonatkozik, így a JSON-hasznos adatban lévő objektumtípusok gyakran tömbök olyan tulajdonságokhoz, ahol egyalakot várhat.

Az eszközgyökér alatt például leíró objektumok tömbje található. A tömbtulajdonság neve "leírások". A leíró objektumnak egy tulajdonsága van - leírás. A minta az, hogy minden felhasználó, aki beírja a leírást kap egy leírás objektumot a felhasználó által megadott értékhez létrehozva.

A felhasználói felület ezután kiválaszthatja, hogyan jelenítse meg a kombinációt. Három különböző minta jelenik meg.

* A legegyszerűbb minta a "Show All". Ebben a mintázatban az összes objektum listanézetben jelenik meg. Az Azure Data Catalog portál felhasználói felülete ezt a mintát használja a leíráshoz.
* Egy másik minta az "Egyesítés". Ebben a mintában a különböző felhasználók összes értéke egyesül, és az ismétlődés eltávolításra kerül. Ilyen minta az Azure Data Catalog portál felhasználói felületén például a címkék és a szakértők tulajdonságai.
* A harmadik minta az "utolsó író nyer". Ebben a mintában csak a beírt legutóbbi érték jelenik meg. a friendlyName egy példa erre a mintára.

## <a name="asset-object-model"></a>Eszközobjektum-modell
Ahogy azt a Kulcsfogalmak szakaszban, az **Azure Data Catalog** objektummodell elemeket tartalmaz, amelyek lehetnek eszközök vagy jegyzetek. Az elemek tulajdonságai lehetnek, amelyek nem kötelezőek vagy kötelezőek. Egyes tulajdonságok az összes elemre vonatkoznak. Egyes tulajdonságok az összes eszközre vonatkoznak. Egyes tulajdonságok csak bizonyos eszköztípusokra vonatkoznak.

### <a name="system-properties"></a>Rendszertulajdonságok
<table><tr><td><b>Tulajdonság neve</b></td><td><b>Adattípus</b></td><td><b>Megjegyzések</b></td></tr><tr><td>időbélyeg</td><td>DateTime</td><td>Az elem legutóbbi módosításának időpontja. Ezt a mezőt a kiszolgáló akkor hozza létre, amikor egy cikket beszúr, és minden alkalommal, amikor egy cikket frissít. A közzétételi műveletek bemeneténa a rendszer figyelmen kívül hagyja ennek a tulajdonságnak az értékét.</td></tr><tr><td>id</td><td>Uri</td><td>Az elem abszolút url-je (csak olvasható). Ez az elem egyedi címezhető URI-ja.  A közzétételi műveletek bemeneténa a rendszer figyelmen kívül hagyja ennek a tulajdonságnak az értékét.</td></tr><tr><td>type</td><td>Sztring</td><td>Az eszköz típusa (csak olvasható).</td></tr><tr><td>Etag</td><td>Sztring</td><td>A cikk azon verziójának megfelelő karakterlánc, amely optimista egyidejűség-vezérlésre használható a katalógusban lévő cikkeket frissítő műveletek végrehajtásakor. A "*" bármely érték egyeztetésére használható.</td></tr></table>

### <a name="common-properties"></a>Közös tulajdonságok
Ezek a tulajdonságok minden gyökéreszköz-típusra és az összes jegyzettípusra vonatkoznak.

<table>
<tr><td><b>Tulajdonság neve</b></td><td><b>Adattípus</b></td><td><b>Megjegyzések</b></td></tr>
<tr><td>fromSourceSystem</td><td>Logikai</td><td>Azt jelzi, hogy az elem adatai forrásrendszerből származnak-e (például Sql Server Database, Oracle Database), vagy egy felhasználó készítette őket.</td></tr>
</table>

### <a name="common-root-properties"></a>Gyakori gyökértulajdonságok
<p>
Ezek a tulajdonságok minden gyökéreszköztípusra vonatkoznak.

<table><tr><td><b>Tulajdonság neve</b></td><td><b>Adattípus</b></td><td><b>Megjegyzések</b></td></tr><tr><td>név</td><td>Sztring</td><td>Az adatforrás helyadataiból származtatott név</td></tr><tr><td>Dsl</td><td>DataSourceLocation</td><td>Egyedileg írja le az adatforrást, és az eszköz egyik azonosítója. (Lásd a kettős identitás szakaszt).  A dsl szerkezete a protokolltól és a forrástípustól függően változik.</td></tr><tr><td>Datasource</td><td>DataSourceInfo</td><td>További részletek az eszköz típusáról.</td></tr><tr><td>lastRegisteredBy</td><td>SecurityPrincipal (Felhasználó)</td><td>Azt a felhasználót írja le, aki legutóbb regisztrálta ezt az eszközt.  A felhasználó egyedi azonosítóját (upn) és megjelenítendő nevet (vezetéknév és keresztnév) is tartalmazza.</td></tr><tr><td>konténerazonosító</td><td>Sztring</td><td>Az adatforrás tárolóeszközének azonosítója. Ez a tulajdonság nem támogatott a tároló típus.</td></tr></table>

### <a name="common-non-singleton-annotation-properties"></a>Gyakori nem egyedi jegyzet eljegyzet tulajdonságai
Ezek a tulajdonságok minden nem egyedi jegyzetre vonatkoznak (a jegyzetek, amelyek eszközenként többek lehetnek).

<table>
<tr><td><b>Tulajdonság neve</b></td><td><b>Adattípus</b></td><td><b>Megjegyzések</b></td></tr>
<tr><td>kulcs</td><td>Sztring</td><td>A felhasználó által megadott kulcs, amely egyedileg azonosítja a jegyzet az aktuális gyűjteményben. A kulcs hossza nem haladhatja meg a 256 karaktert.</td></tr>
</table>

### <a name="root-asset-types"></a>Gyökéreszköz-típusok
A gyökéreszköz-típusok azok a típusok, amelyek a katalógusban regisztrálható különböző típusú adateszközöket képviselik. Minden gyökértípushoz tartozik egy nézet, amely leírja a nézetben szereplő eszköz- és jegyzetjegyzeteket. A(z) {view_name} URL-szegmensben a nézetnevet kell használni, amikor egy eszközt REST API-val tesz közzé.

<table><tr><td><b>Eszköz típusa (Nézet neve)</b></td><td><b>További tulajdonságok</b></td><td><b>Adattípus</b></td><td><b>Engedélyezett jegyzetek</b></td><td><b>Megjegyzések</b></td></tr><tr><td>Táblázat ("táblák")</td><td></td><td></td><td>Leírás<p>Rövid név<p>Címke<p>Séma<p>Oszlopleírás<p>Oszlopcímke<p> Szakértő<p>Előzetes verzió<p>AccessInstruction (Hozzáférési utasítások)<p>TableDataProfile<p>ColumnDataProfile (Oszlopdataprofil)<p>ColumnDataClassification<p>Dokumentáció<p></td><td>A táblázat minden táblázatos adatot jelöl.  Például: SQL Table, SQL View, Analysis Services táblázattáblázat, Analysis Services többdimenziós dimenzió, Oracle Table stb.   </td></tr><tr><td>Intézkedés ("intézkedések")</td><td></td><td></td><td>Leírás<p>Rövid név<p>Címke<p>Szakértő<p>AccessInstruction (Hozzáférési utasítások)<p>Dokumentáció<p></td><td>Ez a típus egy Analysis Services-mérték.</td></tr><tr><td></td><td>Intézkedés</td><td>Oszlop</td><td></td><td>Az intézkedést leíró metaadatok</td></tr><tr><td></td><td>iscalculated </td><td>Logikai</td><td></td><td>Itt adható meg, hogy a mérték ki van-e számítva vagy sem.</td></tr><tr><td></td><td>measureGroup (csoport)</td><td>Sztring</td><td></td><td>A mérték fizikai tárolója</td></tr><td>KPI ("kpiis")</td><td></td><td></td><td>Leírás<p>Rövid név<p>Címke<p>Szakértő<p>AccessInstruction (Hozzáférési utasítások)<p>Dokumentáció</td><td></td></tr><tr><td></td><td>measureGroup (csoport)</td><td>Sztring</td><td></td><td>A mérték fizikai tárolója</td></tr><tr><td></td><td>goalExpression kifejezés</td><td>Sztring</td><td></td><td>MDX numerikus kifejezés vagy a Fő teljesítménymutató célértékét visszaadó számítás.</td></tr><tr><td></td><td>valueExpression kifejezés</td><td>Sztring</td><td></td><td>MDX numerikus kifejezés, amely a Fő teljesítménymutató tényleges értékét adja eredményül.</td></tr><tr><td></td><td>statusExpression kifejezés</td><td>Sztring</td><td></td><td>A Fő teljesítménymutató állapotát egy megadott időpontban reprezentált MDX-kifejezés.</td></tr><tr><td></td><td>trendexpressz</td><td>Sztring</td><td></td><td>MDX-kifejezés, amely a KPI időbeli értékét értékeli ki. A trend bármely időalapú feltétel lehet, amely egy adott üzleti környezetben hasznos.</td>
<tr><td>Jelentés ("jelentések")</td><td></td><td></td><td>Leírás<p>Rövid név<p>Címke<p>Szakértő<p>AccessInstruction (Hozzáférési utasítások)<p>Dokumentáció<p></td><td>Ez a típus az SQL Server Reporting Services jelentését jelöli </td></tr><tr><td></td><td>assetCreatedDate</td><td>Sztring</td><td></td><td></td></tr><tr><td></td><td>eszközCreatedby</td><td>Sztring</td><td></td><td></td></tr><tr><td></td><td>eszközMódosításdátuma</td><td>Sztring</td><td></td><td></td></tr><tr><td></td><td>eszközModifiedBy</td><td>Sztring</td><td></td><td></td></tr><tr><td>Konténer ("konténerek")</td><td></td><td></td><td>Leírás<p>Rövid név<p>Címke<p>Szakértő<p>AccessInstruction (Hozzáférési utasítások)<p>Dokumentáció<p></td><td>Ez a típus más eszközök, például egy SQL-adatbázis, egy Azure Blobs-tároló vagy egy Analysis Services-modell tárolóját jelöli.</td></tr></table>

### <a name="annotation-types"></a>Jegyzettípusok
A jegyzettípusok olyan metaadattípusokat jelölnek, amelyek a katalóguson belül más típusokhoz rendelhetők.

<table>
<tr><td><b>Jegyzet típusa (beágyazott nézet neve)</b></td><td><b>További tulajdonságok</b></td><td><b>Adattípus</b></td><td><b>Megjegyzések</b></td></tr>

<tr><td>Leírás ("leírások")</td><td></td><td></td><td>Ez a tulajdonság egy eszköz leírását tartalmazza. A rendszer minden felhasználója hozzáadhatja saját leírását.  Csak az a felhasználó szerkesztheti a Description objektumot.  (A rendszergazdák és az eszköztulajdonosok törölhetik a Description objektumot, de nem szerkeszthetik). A rendszer külön tartja karban a felhasználók leírását.  Így minden eszközön egy sor leírás található (minden olyan felhasználó számára, aki hozzájárult az eszközről való ismereteihez, az adatforrásból származó információkat tartalmazó mellett).</td></tr>
<tr><td></td><td>leírás</td><td>sztring</td><td>Az eszköz rövid leírása (2-3 sor)</td></tr>

<tr><td>Címke ("címkék")</td><td></td><td></td><td>Ez a tulajdonság egy eszköz címkéjét határozza meg. A rendszer minden felhasználója több címkét is hozzáadhat egy eszközhöz.  Csak a Címkeobjektumokat létrehozó felhasználó szerkesztheti őket.  (A rendszergazdák és az eszköztulajdonosok törölhetik a Címke objektumot, de nem szerkeszthetik). A rendszer külön tartja karban a felhasználók címkéit.  Így minden egyes eszközön van egy címkeobjektumok tömbje.</td></tr>
<tr><td></td><td>címke</td><td>sztring</td><td>Az eszközt leíró címke.</td></tr>

<tr><td>FriendlyName ("friendlyName")</td><td></td><td></td><td>Ez a tulajdonság egy eszköz rövid nevét tartalmazza. A FriendlyName egy singleton jegyzet - csak egy FriendlyName adható hozzá egy eszközhöz.  Csak a FriendlyName objektumot létrehozó felhasználó szerkesztheti azt. (A rendszergazdák és az eszköztulajdonosok törölhetik a FriendlyName objektumot, de nem szerkeszthetik). A rendszer külön tartja karban a felhasználók rövid nevét.</td></tr>
<tr><td></td><td>rövidnevű név</td><td>sztring</td><td>Az eszköz rövid neve.</td></tr>

<tr><td>Séma ("séma")</td><td></td><td></td><td>A séma az adatok szerkezetét írja le.  Felsorolja az attribútum (oszlop, attribútum, mező, stb) nevét, típusát, valamint egyéb metaadatokat.  Ez az információ az adatforrásból származik.  A séma egy egyedi jegyzet – csak egy séma adható hozzá egy eszközhöz.</td></tr>
<tr><td></td><td>oszlopok</td><td>Oszlop[]</td><td>Oszlopobjektumok tömbje. Az oszlopot az adatforrásból származó információkkal írják le.</td></tr>

<tr><td>ColumnDescription ("columnDescriptions")</td><td></td><td></td><td>Ez a tulajdonság egy oszlop leírását tartalmazza.  A rendszer minden felhasználója hozzáadhatja a saját leírását több oszlophoz (oszloponként legbőlegenként). Csak a ColumnDescription objektumokat létrehozó felhasználó szerkesztheti őket.  (A rendszergazdák és az eszköztulajdonosok törölhetik a ColumnDescription objektumot, de nem szerkeszthetik). A rendszer külön tartja karban a felhasználó oszlopleírását.  Így minden egyes eszközön van egy columndescription objektumtömb (oszloponként egy oszloponként minden olyan felhasználó számára, aki az adatforrásból származó információkat tartalmazó oszloptudással járult hozzá).  A ColumnDescription lazán kötődik a sémához, így nem szinkronizálható. A ColumnDescription olyan oszlopot írhat le, amely már nem létezik a sémában.  Az író feladata, hogy szinkronban tartsa a leírást és a sémát.  Az adatforrás oszlopleírási adatokkal is rendelkezhet, és további ColumnDescription objektumok, amelyek az eszköz futtatásakor jönnek létre.</td></tr>
<tr><td></td><td>columnName (oszlopnév)</td><td>Sztring</td><td>A leírás által hivatkozott oszlop neve.</td></tr>
<tr><td></td><td>leírás</td><td>Sztring</td><td>az oszlop rövid leírása (2-3 sor).</td></tr>

<tr><td>Oszloptag ("columnTags")</td><td></td><td></td><td>Ez a tulajdonság egy oszlop címkéjét tartalmazza. A rendszer minden felhasználója több címkét adhat hozzá egy adott oszlophoz, és több oszlophoz is hozzáadhat címkéket. Csak a ColumnTag objektumokat létrehozó felhasználó szerkesztheti őket. (A rendszergazdák és az eszköztulajdonosok törölhetik a ColumnTag objektumot, de nem szerkeszthetik). A rendszer külön tartja karban a felhasználók oszlopcímkéit.  Így minden eszközön van egy columntag objektumtömb.  A ColumnTag lazán kötődik a sémához, így nem lesz szinkronban. A ColumnTag leírhat egy olyan oszlopot, amely már nem található meg a sémában.  Az író feladata, hogy az oszlopcímkét és a sémát szinkronban tartsa.</td></tr>
<tr><td></td><td>columnName (oszlopnév)</td><td>Sztring</td><td>Annak az oszlopnak a neve, amelyre a címke hivatkozik.</td></tr>
<tr><td></td><td>címke</td><td>Sztring</td><td>Az oszlopot leíró címke.</td></tr>

<tr><td>Szakértő ("szakértők")</td><td></td><td></td><td>Ez a tulajdonság egy olyan felhasználót tartalmaz, aki az adatkészlet szakértőjének minősül. A szakértők véleménye (leírások) buborék a tetején a UX, amikor felsorolja leírások. Minden felhasználó megadhatja saját szakértőit. Csak az a felhasználó szerkesztheti a szakértői objektumot. (A rendszergazdák és az eszköztulajdonosok törölhetik a Szakértői objektumokat, de nem szerkeszthetik őket).</td></tr>
<tr><td></td><td>Szakértő</td><td>SecurityPrincipal (Felhasználó)</td><td></td></tr>

<tr><td>Előnézet ("előnézetek")</td><td></td><td></td><td>Az előzetes verzió az eszköz első 20 sorának pillanatképét tartalmazza. Az előnézet csak bizonyos típusú eszközök esetében van értelme (a Table esetében van értelme, de a Mérték esetében nem).</td></tr>
<tr><td></td><td>Előnézet</td><td>objektum[]</td><td>Oszlopot képviselő objektumok tömbje.  Minden objektumnak van egy tulajdonsága, amely egy oszlophoz van hozzá, és a sorhoz az adott oszlop értéke szerepel.</td></tr>

<tr><td>AccessInstruction ("accessInstructions")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>mimeTípus</td><td>sztring</td><td>A tartalom pantomimtípusa.</td></tr>
<tr><td></td><td>content</td><td>sztring</td><td>Az adategység elérésére vonatkozó utasítások. A tartalom lehet URL- cím, e-mail cím vagy utasításkészlet.</td></tr>

<tr><td>TableDataProfile ("tableDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>numberOfRows</td></td><td>int</td><td>Az adatkészlet sorainak száma</td></tr>
<tr><td></td><td>size</td><td>long</td><td>Az adatkészlet mérete bájtban.  </td></tr>
<tr><td></td><td>sémaModifiedTime</td><td>sztring</td><td>A séma legutóbbi módosítása</td></tr>
<tr><td></td><td>dataModifiedTime</td><td>sztring</td><td>Az adatkészlet legutóbbi módosításának időpontja (az adatok hozzáadása, módosítása vagy törlése)</td></tr>

<tr><td>ColumnsDataProfile ("columnsDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>oszlopok</td></td><td>ColumnDataProfile[]</td><td>Oszlopadatprofilok tömbje.</td></tr>

<tr><td>ColumnDataClassification ("columnDataClassifications")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName (oszlopnév)</td><td>Sztring</td><td>A besorolás által hivatkozott oszlop neve.</td></tr>
<tr><td></td><td>besorolás</td><td>Sztring</td><td>Az ebben az oszlopban szereplő adatok besorolása.</td></tr>

<tr><td>Dokumentáció ("dokumentáció")</td><td></td><td></td><td>Egy adott eszközhöz csak egy dokumentáció tartozhat.</td></tr>
<tr><td></td><td>mimeTípus</td><td>sztring</td><td>A tartalom pantomimtípusa.</td></tr>
<tr><td></td><td>content</td><td>sztring</td><td>A dokumentáció tartalma.</td></tr>

</table>

### <a name="common-types"></a>Gyakori típusok
A gyakori típusok használhatók a tulajdonságok típusaiként, de nem elemek.

<table>
<tr><td><b>Gyakori típus</b></td><td><b>Tulajdonságok</b></td><td><b>Adattípus</b></td><td><b>Megjegyzések</b></td></tr>
<tr><td>DataSourceInfo</td><td></td><td></td><td></td></tr>
<tr><td></td><td>forrástípus</td><td>sztring</td><td>Az adatforrás típusát írja le.  Például: SQL Server, Oracle Database, stb.  </td></tr>
<tr><td></td><td>objectType</td><td>sztring</td><td>Az adatforrásobjektum típusát írja le. Például: Tábla, Nézet az SQL Server hez.</td></tr>

<tr><td>DataSourceLocation</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Protokoll</td><td>sztring</td><td>Kötelező. Az adatforrással való kommunikációhoz használt protokoll ismertetése. Például: "tds" az SQl Server, "oracle" az Oracle, stb A jelenleg támogatott protokollok listáját lásd: <a href="https://docs.microsoft.com/azure/data-catalog/data-catalog-dsr">Adatforrás-referencia specifikáció – DSL-struktúra.</a></td></tr>
<tr><td></td><td>address</td><td>Szótári&lt;karakterlánc, objektum&gt;</td><td>Kötelező. A cím a hivatkozott adatforrás azonosítására használt protokollra jellemző adathalmaz. A címadatok egy adott protokollra terjednek ki, ami azt jelenti, hogy értelmetlenek a protokoll ismerete nélkül.</td></tr>
<tr><td></td><td>hitelesítés</td><td>sztring</td><td>Választható. Az adatforrással való kommunikációhoz használt hitelesítési séma. Például: ablakok, oauth, stb.</td></tr>
<tr><td></td><td>connectionProperties (kapcsolattulajdonságai)</td><td>Szótári&lt;karakterlánc, objektum&gt;</td><td>Választható. További információ az adatforráshoz való csatlakozásról.</td></tr>

<tr><td>SecurityPrincipal (Felhasználó)</td><td></td><td></td><td>A háttérprogram nem hajt végre a megadott tulajdonságok a közzététel során az AAD-vel szembeni ellenőrzést.</td></tr>
<tr><td></td><td>Upn</td><td>sztring</td><td>A felhasználó egyedi e-mail címe. Meg kell adni, ha objectId nincs megadva, vagy a környezetben a "lastRegisteredBy" tulajdonság, egyébként nem kötelező.</td></tr>
<tr><td></td><td>objectId</td><td>Guid</td><td>Felhasználó vagy biztonsági csoport AAD identitás. Választható. Meg kell adni, ha upn nincs megadva, egyébként nem kötelező.</td></tr>
<tr><td></td><td>firstName</td><td>sztring</td><td>A felhasználó utóneve (megjelenítési célokra). Választható. Csak a "lastRegisteredBy" tulajdonság környezetében érvényes. Nem adható meg a "szerepkörök", "engedélyek" és "szakértők" rendszerbiztonsági tag megadásakor.</td></tr>
<tr><td></td><td>lastName</td><td>sztring</td><td>A felhasználó vezetékneve (megjelenítési célokra). Választható. Csak a "lastRegisteredBy" tulajdonság környezetében érvényes. Nem adható meg a "szerepkörök", "engedélyek" és "szakértők" rendszerbiztonsági tag megadásakor.</td></tr>

<tr><td>Oszlop</td><td></td><td></td><td></td></tr>
<tr><td></td><td>név</td><td>sztring</td><td>Az oszlop vagy attribútum neve.</td></tr>
<tr><td></td><td>type</td><td>sztring</td><td>az oszlop vagy attribútum adattípusa. A megengedett típusok az eszköz adatforrástípusától függenek.  A típusoknak csak egy részhalmaza támogatott.</td></tr>
<tr><td></td><td>Maxlength</td><td>int</td><td>Az oszlophoz vagy attribútumhoz engedélyezett maximális hossz. Adatforrásból származtatva. Csak bizonyos forrástípusokra vonatkozik.</td></tr>
<tr><td></td><td>Precíziós</td><td>bájt</td><td>Az oszlop vagy attribútum pontossága. Adatforrásból származtatva. Csak bizonyos forrástípusokra vonatkozik.</td></tr>
<tr><td></td><td>isNullable</td><td>Logikai</td><td>Azt jelzi, hogy az oszlopnak engedélyezett-e null érték. Adatforrásból származtatva. Csak bizonyos forrástípusokra vonatkozik.</td></tr>
<tr><td></td><td>kifejezés</td><td>sztring</td><td>Ha az érték számított oszlop, akkor ez a mező az értéket kifejező kifejezést tartalmazza. Adatforrásból származtatva. Csak bizonyos forrástípusokra vonatkozik.</td></tr>

<tr><td>ColumnDataProfile (Oszlopdataprofil)</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName (oszlopnév) </td><td>sztring</td><td>Az oszlop neve</td></tr>
<tr><td></td><td>type </td><td>sztring</td><td>Az oszlop típusa</td></tr>
<tr><td></td><td>p </td><td>sztring</td><td>Az adatkészlet minimális értéke</td></tr>
<tr><td></td><td>Max </td><td>sztring</td><td>Az adatkészlet maximális értéke</td></tr>
<tr><td></td><td>Avg </td><td>double</td><td>Az adathalmaz átlagos értéke</td></tr>
<tr><td></td><td>Stdev </td><td>double</td><td>Az adathalmaz szórása</td></tr>
<tr><td></td><td>nullCount (nullcount) </td><td>int</td><td>Az adatkészlet null értékeinek száma</td></tr>
<tr><td></td><td>distinctCount (késem:  </td><td>int</td><td>Az adatkészlet különböző értékeinek száma</td></tr>


</table>

## <a name="asset-identity"></a>Eszköz identitása
Az Azure Data Catalog a DataSourceLocation "dsl" tulajdonság "address" tulajdonságának "protokoll" és identitástulajdonságait használja az eszköz identitásának létrehozásához, amely a katalóguson belüli eszköz címzésére szolgál.
A "tds" protokoll például "kiszolgáló", "adatbázis", "séma" és "objektum" identitástulajdonságokkal rendelkezik. A protokoll és az identitástulajdonságok kombinációi az SQL Server Table Asset identitásának létrehozásához használatosak.
Az Azure Data Catalog számos beépített adatforrás-protokollt biztosít, amelyek az [adatforrás-referenciaspecifikáció – DSL-struktúra](data-catalog-dsr.md).
A támogatott protokollok készlete programozott módon bővíthető (lásd a Data Catalog REST API hivatkozását). A katalógus rendszergazdái egyéni adatforrásprotokollokat regisztrálhatnak. Az alábbi táblázat az egyéni protokoll regisztrálásához szükséges tulajdonságokat ismerteti.

### <a name="custom-data-source-protocol-specification"></a>Egyéni adatforrásprotokoll-specifikáció
<table>
<tr><td><b>Típus</b></td><td><b>Tulajdonságok</b></td><td><b>Adattípus</b></td><td><b>Megjegyzések</b></td></tr>

<tr><td>DataSourceProtocol</td><td></td><td></td><td></td></tr>
<tr><td></td><td>névtér</td><td>sztring</td><td>A protokoll névtere. A névtérnek 1 és 255 karakter között kell lennie, egy vagy több nem üres részt kell tartalmaznia, amelyeket pont (.) választ el egymástól. Minden résznek 1 és 255 karakter között kell lennie, betűvel kell kezdenie, és csak betűket és számokat tartalmazhat.</td></tr>
<tr><td></td><td>név</td><td>sztring</td><td>A protokoll neve. A név nek 1 és 255 karakter között kell lennie, betűvel kell kezdenie, és csak betűket, számokat és a kötőjel (-) karaktert kell tartalmaznia.</td></tr>
<tr><td></td><td>identityProperties (identitástulajdonságai)</td><td>DataSourceProtocolIdentityProperty[]</td><td>Az identitástulajdonságok listájának legalább egy, de legfeljebb 20 tulajdonságot kell tartalmaznia. Például: "kiszolgáló", "adatbázis", "séma", "objektum" a "tds" protokoll identitástulajdonságai.</td></tr>
<tr><td></td><td>identitySets</td><td>DataSourceProtocolIdentitySet[]</td><td>Identitáskészletek listája. Identitástulajdonságok készleteit határozza meg, amelyek az érvényes eszköz identitását jelölik. Legalább egy, de legfeljebb 20 készletet kell tartalmaznia. Például: {"server", "database", "séma" és "object"} a "tds" protokoll identitáskészlete, amely az Sql Server Table eszköz identitását határozza meg.</td></tr>

<tr><td>DataSourceProtocolIdentityProperty tulajdonság</td><td></td><td></td><td></td></tr>
<tr><td></td><td>név</td><td>sztring</td><td>A tulajdonság neve. A név nek 1 és 100 karakter között kell lennie, betűvel kell kezdenie, és csak betűket és számokat tartalmazhat.</td></tr>
<tr><td></td><td>type</td><td>sztring</td><td>Az ingatlan típusa. Támogatott értékek: "bool", boolean", "byte", "guid", "int", "egész", "hosszú", "karakterlánc", "url"</td></tr>
<tr><td></td><td>ignoreCase</td><td>Bool</td><td>Azt jelzi, hogy a tulajdonság értékének használatakor figyelmen kívül kell-e hagyni az esetet. Csak "karakterlánc" típusú tulajdonságokhoz adható meg. Az alapértelmezett érték hamis.</td></tr>
<tr><td></td><td>urlPathSegmentsIgnoreCase</td><td>bool[]</td><td>Azt jelzi, hogy az url elérési útjának minden egyes szegmensében figyelmen kívül kell-e hagyni az esetet. Csak az "URL" típusú tulajdonságokhoz adható meg. Az alapértelmezett érték [hamis].</td></tr>

<tr><td>DataSourceProtocolIdentitySet</td><td></td><td></td><td></td></tr>
<tr><td></td><td>név</td><td>sztring</td><td>Az identitáskészlet neve.</td></tr>
<tr><td></td><td>properties</td><td>karakterlánc[]</td><td>Az identitáskészletbe tartozó identitástulajdonságok listája. Nem tartalmazhat ismétlődéseket. Az identitáskészlet által hivatkozott minden tulajdonságot meg kell határozni a protokoll "identityProperties" listájában.</td></tr>

</table>

## <a name="roles-and-authorization"></a>Szerepkörök és engedélyezés
A Microsoft Azure Data Catalog engedélyezési lehetőségeket biztosít az eszközökön és a jegyzeteken végzett CRUD-műveletekhez.

## <a name="key-concepts"></a>Fő fogalmak
Az Azure Data Catalog két engedélyezési mechanizmust használ:

* Szerepköralapú engedélyezés
* Engedélyalapú engedélyezés

### <a name="roles"></a>Szerepkörök
Három szerepkör van: **Rendszergazda**, **Tulajdonos**és **Közreműködő.**  Minden szerepkörnek megvan a maga hatóköre és jogai, amelyeket a következő táblázat foglal össze.

<table><tr><td><b>Szerepkör</b></td><td><b>Hatókör</b></td><td><b>Jogok</b></td></tr><tr><td>Rendszergazda</td><td>Katalógus (az összes eszköz/jegyzet a katalógusban)</td><td>Nézetadatok törlése

ChangeOwnership ChangeVisibility NézetEngedélyek</td></tr><tr><td>Tulajdonos</td><td>Minden egyes eszköz (gyökérelem)</td><td>Nézetadatok törlése

ChangeOwnership ChangeVisibility NézetEngedélyek</td></tr><tr><td>Közreműködő</td><td>Minden egyes eszköz és jegyzet</td><td>Olvasási frissítés : ViewRoles törlése Megjegyzés: az összes jogosultság visszavonásra kerül, ha az elem olvasási jogát visszavonják a közreműködőtől</td></tr></table>

> [!NOTE]
> **Az Olvasás**, **frissítés,** **törlés**, **ViewRoles** jogok bármely elemre (eszköz vagy jegyzet) vonatkoznak, míg **a TakeOwnership**, **ChangeOwnership**, **ChangeVisibility**, **ViewPermissions csak** a gyökérelemre vonatkozik.
> 
> **A törlési** jog egy elemre és az alatta lévő alelemekre vagy elemekre vonatkozik. Például egy eszköz törlése is törli az adott eszközhöz vonatkozó jegyzeteket.
> 
> 

### <a name="permissions"></a>Engedélyek
Az engedély a hozzáférés-vezérlési bejegyzések listája. Minden hozzáférés-vezérlési bejegyzés hez jogosultságkészlet et rendel egy rendszerbiztonsági taghoz. Az engedélyek csak egy eszközön (azaz gyökérelemen) adhatók meg, és az eszközre és az alelemekre vonatkozhatnak.

Az **Azure Data Catalog** előzetes verzió során csak **olvasási** jog támogatott az engedélyek listájában, hogy lehetővé tegye a forgatókönyv láthatóságának korlátozását egy eszköz.

Alapértelmezés szerint minden hitelesített felhasználó **olvasási** joggal rendelkezik a katalógus bármely eleméhez, kivéve, ha a láthatóság az engedélyekben szereplő rendszerbiztonsági tagok készletére korlátozódik.

## <a name="rest-api"></a>REST API
**A PUT** és **a POST** nézetelem-kérelmek a szerepkörök és engedélyek vezérlésére használhatók: a cikk hasznos terhelése mellett két rendszertulajdonság is megadható **szerepkörök** és **engedélyek.**

> [!NOTE]
> csak a gyökérelemre vonatkozó **engedélyek.**
> 
> **Tulajdonosi** szerepkör csak egy gyökérelemre vonatkozik.
> 
> Alapértelmezés szerint, ha egy elem jön létre a katalógusban a **közreműködő** je a jelenleg hitelesített felhasználó. Ha az elemet mindenkinek fel kell adnia, &lt;&gt; akkor a **közreműködőnek** az elem első közzétételekor a **szerepkörök** tulajdonságában a Mindenki speciális rendszerbiztonsági tag értékre kell állítania (lásd a következő példát). **A közreműködő** nem módosítható, és az elem élettartama alatt változatlan marad (még a **rendszergazda** vagy a **tulajdonos** sem jogosult a **közreműködő módosítására).** A **közreműködő** explicit beállításának csak &lt;a&gt;Mindenki: **Közreműködő** támogatott értéke csak &lt;&gt;olyan felhasználó lehet, aki létrehozott egy elemet, vagy a Mindenki .
> 
> 

### <a name="examples"></a>Példák
**Elem közzétételekor állítsa be a Közreműködő t &lt;mindenki&gt; hez.**
Különleges rendszerbiztonsági tag &lt;Mindenkinek&gt; van "00000000-0000-0000-0000-0000000000201" objektumazonosítója.
  **POST** https:\//api.azuredatacatalog.com/catalogs/default/views/tables/?api-version=2016-03-30

> [!NOTE]
> Egyes HTTP-ügyfélimplementációk automatikusan újra kibocsáthatják a kérelmeket a kiszolgálóról érkező 302-es válaszként, de általában az engedélyezési fejléceket kivonják a kérelemből. Mivel az engedélyezési fejléc az Azure Data Catalog kéréseinek teljesítéséhez szükséges, biztosítania kell, hogy az engedélyezési fejléc továbbra is meg legyen adva, amikor újra kiad egy kérelmet az Azure Data Catalog által megadott átirányítási helyre. A következő mintakód a .NET HttpWebRequest objektum használatával mutatja be.
> 
> 

**Törzs**
```json
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
```

  **Tulajdonosok hozzárendelése és láthatóságának korlátozása meglévő gyökérelemhez**: **PUT** https:\//api.azuredatacatalog.com/catalogs/default/views/tables/042297b0...1be45ecd462a?api-version=2016-03-30

```json
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
```

> [!NOTE]
> A PUT-ban nem szükséges cikk-hasznos testet megadni a törzsben: A PUT csak szerepkörök és/vagy engedélyek frissítésére használható.
> 
