---
title: "Figyelő, a méretezés, az irányítópult konfigurálásához és a BizTalk szolgáltatások hibrid kapcsolatok |} Microsoft Docs"
description: "További tudnivalók a vezérlők és figyelemmel kísérni a teljesítményét BizTalk szolgáltatások"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 7a1815db-0de2-4274-8be0-198c1b077324
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 351809cd5f165a863dc02bfadf78fa59cbaabfd7
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2017
---
# <a name="review-the-dashboard-monitor-scale-configure-and-hybrid-connection-tabs"></a>Tekintse át az Irányítópult, Figyelő, Skála, Konfigurálás és Hibridkapcsolat lapokat

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Miután a BizTalk szolgáltatás létrehozása és az alkalmazás központi telepítése, néhány a BizTalk szolgáltatás beállításainak módosítása, és az alkalmazás teljesítményének figyelése. 

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

Az alábbi fülekkel ekkor megnyílik egy új ablakban. Ez a témakör ismerteti az ezeken a lapokon.

## <a name="quickstart-quickstartquickstart"></a>Gyors üzembe helyezés)![Első lépések][Quickstart])
BizTalk szolgáltatások kiadásoknál felsorolt összes beállítások nem érhetők el. 

<table border="1">
    <tr>
        <td><strong>Eszközök</strong></td>
        <td>Töltse le a BizTalk szolgáltatások SDK telepítése a Visual Studio projektsablonjai a helyi fejlesztési számítógépen. Ezek a sablonok létrehozása a <strong>BizTalk szolgáltatások</strong> (híd) és a <strong>BizTalk szolgáltatás-összetevők</strong> (átalakítási) Visual Studio-projektek a BizTalk szolgáltatás telepített.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302335">Hogyan kezdhetem meg az Azure BizTalk Services SDK használatával </a> és <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=241589">telepítése az Azure BizTalk szolgáltatások SDK</a> első lépések lépéseit sorolja fel.
        </td>
    </tr>
    <tr>
        <td><strong>Partner megállapodások létrehozása</strong></td>
        <td>Megnyitja a Azure BizTalk Services portálra, ahol adja hozzá a partnerek, és hozzon létre X12, AS2, Azure-platformon futó és EDIFACT EDI-szerződések.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">BizTalk szolgáltatások Portal üzenetküldéshez EDI összetevők konfigurálása</a> első lépések lépéseit sorolja fel.
        </td>
    </tr>

<tr>
        <td><strong>További információ a BizTalk szolgáltatások</strong></td>
        <td>Lépjen a <a HREF="http://azure.microsoft.com/documentation/services/biztalk-services/">center tanulási</a> Azure BizTalk szolgáltatások tájékozódhat.</td>
</tr>
</table>


A tálcán a lap alján, a következőket teheti:

<table border="1">

<tr>
<td><strong>Kezelése</strong> az alkalmazások központi telepítésének</td>
<td>Megnyitja a Azure BizTalk Services portálra. A BizTalk szolgáltatások portál nem EDI-konfiguráció hozzáadása a partnerek és létrehozása X12, AS2, beleértve a Megjelenés és EDIFACT-egyezmények.
<br/><br/>
Ez az azonos <strong>egyezmények létrehozása</strong> a a <strong>gyors üzembe helyezés</strong> lapon.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">BizTalk szolgáltatások Portal üzenetküldéshez EDI összetevők konfigurálása</a> nyújt részletesebb információt a BizTalk Services portálra.</td>
</tr>

<tr>
<td><strong>Kapcsolat információi</strong> , a hozzáférés-vezérlési Namespace</td>
<td>Ha bejelöli a kapcsolati adatokat, majd a hozzáférés-vezérlési Namespace, az alapértelmezett kibocsátó és a kulcs alapértelmezett jelennek meg. Ezek az értékek másolhatja.
<br/><br/>
A hozzáférés-vezérlő portál is megnyithatja. <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Hozzon létre egy hozzáférés-vezérlés Namespace</a> a hozzáférés-vezérlő Portal nyújt részletesebb információt.</td>
</tr>

<tr>
<td><strong>Kulcsok szinkronizálása</strong> tárfiókban</td>
<td>Storage-fiók létrehozásakor automatikusan létrejön egy elsődleges kulcs és egy másodlagos kulcs. A titkosítási kulcsokat a Tárfiók való hozzáférés szabályozása. A BizTalk szolgáltatás automatikusan az elsődleges kulcsot használja. <strong>Kulcsok szinkronizálása</strong> engedélyezése a felhasználóknak a BizTalk szolgáltatás megszakítása nélkül az elsődleges és a másodlagos kulcs közötti váltáshoz.
<br/><br/>
Például azt szeretné a BizTalk szolgáltatás egy új elsődleges kulcsot a tárfiók. Ehhez tegye a következőket:
<br/><br/>
<ol>
<li>Válassza ki a BizTalk szolgáltatás, majd <strong>szinkronizálási kulcsok</strong>. Válassza ki a másodlagos kulcsot. Ha így tesz, a BizTalk szolgáltatás elindul, a másodlagos kulcs használatával.</li>
<li>Válassza ki a tárfiók, és az elsődleges kulcs újragenerálása. Ne feledje, hogy a BizTalk szolgáltatás használ a másodlagos kulcsot.</li>
<li>Válassza ki a BizTalk szolgáltatás, majd <strong>szinkronizálási kulcsok</strong>. Jelölje ki, az elsődleges kulcs. Az új elsődleges kulcs, akkor a rendszer újra létrehozza azt.</li>
<li>Válassza ki a tárfiók, és a másodlagos kulcs újragenerálása.</li>
</ol>
<br/>
A folyamat elnevezése "helyettesítő kulcsok". A célja engedélyezése a felhasználóknak a BizTalk szolgáltatás megszakítása nélkül az elsődleges és a másodlagos kulcs közötti váltáshoz.</td>
</tr>

<tr>
<td><strong>Törlés</strong> az alkalmazás</td>
<td>A BizTalk szolgáltatás törléséhez és telepített összes elemek eltávolítása.</td>
</tr>
</table>


## <a name="dashboard"></a>Irányítópult
BizTalk szolgáltatások kiadásoknál felsorolt összes beállítások nem érhetők el. 

Akkor válassza ki a BizTalk szolgáltatás nevét, az irányítópult fület jelenik meg. Az irányítópult a következőket teheti:

##### <a name="usage-overview-shows-the-number-of-used-hybrid-connections"></a>Használati áttekintése: Használt hibrid kapcsolatok számát jeleníti meg.
Megjeleníti a használati adatok is GB-ban. 

##### <a name="metric-graph-shows-a-fixed-list-of-performance-metrics"></a>Metrika Graph: Metrikák rögzített listáját tartalmazza.
Ezeket a mérési adja meg a BizTalk szolgáltatás valós idejű értékeit. Is beállíthatja a **relatív** vagy **abszolút** értékek és időtartomány **időköz** számos a metrika a grafikonon megjelenített. 

A metrikák leírását itt [elérhető](#Metrics) ebben a témakörben.

##### <a name="quick-glance-lists-your-biztalk-service-properties"></a>Gyors áttekintő: Felsorolja a BizTalk szolgáltatás tulajdonságai
<table border="1">

<tr>
<td><strong>Követés adatbázis-hitelesítő adatok frissítése</strong></td>
<td>Módosítja a felhasználónevet és a nyomkövetési adatbázis szolgáltatásba való bejelentkezéshez használt jelszót.</td>
</tr>
<tr>
<td><strong>SSL-tanúsítvány frissítése</strong></td>
<td>Frissítheti a BizTalk szolgáltatás különböző SSL-tanúsítvány használatára. Egy önaláírt SSL-tanúsítvány mikor automatikusan létrejön, <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">a BizTalk szolgáltatás létrehozása</a>.</td>
</tr>
<tr>
<td><strong>Tanúsítvány letöltése</strong></td>
<td>Letöltheti a BizTalk szolgáltatás a helyi számítógép által használt SSL-tanúsítványt.</td>
</tr>
<tr>
<td><strong>Állapot</strong></td>
<td>A BizTalk szolgáltatás aktuális állapotát jeleníti meg. Lásd: <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=329870">BizTalk szolgáltatások: szolgáltatás állapota diagram</a>. </td>
</tr>
<tr>
<td><strong>URL-címe</strong></td>
<td>A BizTalk szolgáltatás URL-CÍMÉT. Ez megegyezik a <strong>tartomány URL-cím</strong> a BizTalk szolgáltatás létrehozásakor megadott.</td>
</tr>
<tr>
<td><strong>Nyilvános virtuális IP-cím (VIP) címe</strong></td>
<td>A BizTalk szolgáltatás hozzárendelt IP-címet. Az összes bemeneti végpont szolgál, és a kimenő adatforgalmat a forrás-címe. Az IP-cím, amíg létrejön a BizTalk szolgáltatás tartozik. Ha törli a BizTalk szolgáltatás, az IP-cím van hozzárendelve egy másik BizTalk szolgáltatás.</td>
</tr>
<tr>
<td><strong>Az ACS-Namespace</strong></td>
<td>A BizTalk szolgáltatás végzi a hitelesítést.</td>
</tr>
<tr>
<td><strong>Kiadás</strong></td>
<td>Listák kiadásának kitölti a a BizTalk szolgáltatás létrehozása.</td>
</tr>
<tr>
<td><strong>Hely</strong></td>
<td>A földrajzi régiót, amelyen a BizTalk szolgáltatás jeleníti meg.</td>
</tr>
<tr>
<td><strong>Létrehozva</strong></td>
<td>A dátum és a BizTalk szolgáltatás létrehozásakor jeleníti meg.</td>
</tr>
<tr>
<td><strong>Adatbázis nyomon követése</strong></td>
<td>Az Azure SQL-adatbázis neve, amely tárolja a nyomkövetési táblát, a BizTalk szolgáltatás által használt. 
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Követelmények Explained</a> a követési adatbázis részleteit.</td>
</tr>
<tr>
<td><strong>Tárolási figyelés/archiválása</strong></td>
<td>A figyelési kimenetet a BizTalk szolgáltatás Azure Storage fióknevet.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Követelmények Explained</a> részleteit a tárfiók.</td>
</tr>
<tr>
<td><strong>Előfizetés neve</strong></td>
<td>Az előfizetés, amelyen a BizTalk szolgáltatás sorolja fel. Az előfizetés szabályozza a hozzáférést.</td>
</tr>
<tr>
<td><strong>Előfizetés-azonosító</strong></td>
<td>Előfizetés létrehozásakor a rendszer automatikusan előállítja az előfizetés-azonosító. REST API-k használatakor esetleg adja meg az előfizetés-azonosító.</td>
</tr>
</table>

[BizTalk szolgáltatások: Kiépítés](http://go.microsoft.com/fwlink/p/?LinkID=302280) BizTalk szolgáltatás létrehozása lépéseit sorolja fel.

##### <a name="manage-connection-information-sync-keys-and-delete-in-the-task-bar"></a>Kezelése, a kapcsolatadatok, a szinkronizálási kulcsokat, és a tálcán törlése:
<table border="1">

<tr>
<td><strong>Kezelése</strong> az alkalmazások központi telepítésének</td>
<td>Megnyitja a Azure BizTalk Services portálra. A BizTalk szolgáltatások portál nem EDI-konfiguráció hozzáadása a partnerek és létrehozása X12, AS2, beleértve a Megjelenés és EDIFACT-egyezmények.
<br/><br/>
Ez az azonos <strong>egyezmények létrehozása</strong> a a <strong>gyors üzembe helyezés</strong> lapon.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">BizTalk szolgáltatások Portal üzenetküldéshez EDI összetevők konfigurálása</a> nyújt részletesebb információt a BizTalk Services portálra.</td>
</tr>
<tr>
<td><strong>Kapcsolat információi</strong> , a hozzáférés-vezérlési Namespace</td>
<td>Megjeleníti a hozzáférés-vezérlési Namespace, alapértelmezett kibocsátó és a kulcs alapértelmezett értékek; amely másolhatók.
<br/><br/>
A hozzáférés-vezérlő portál is megnyithatja. A hozzáférés-vezérlő portál megegyezik az Active Directory beállításával a bal oldali navigációs ablaktáblán.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Az ACS-Namespace kezelése</a> a hozzáférés-vezérlő Portal nyújt részletesebb információt.</td>
</tr>
<tr>
<td><strong>Kulcsok szinkronizálása</strong> tárfiókban</td>
<td>Storage-fiók létrehozásakor automatikusan létrejön egy elsődleges kulcs és egy másodlagos kulcs. A titkosítási kulcsokat a Tárfiók való hozzáférés szabályozása. A BizTalk szolgáltatás automatikusan az elsődleges kulcsot használja. <strong>Kulcsok szinkronizálása</strong> engedélyezése a felhasználóknak a BizTalk szolgáltatás megszakítása nélkül az elsődleges és a másodlagos kulcs közötti váltáshoz.
<br/><br/>
Például azt szeretné a BizTalk szolgáltatás egy új elsődleges kulcsot a tárfiók. Ehhez tegye a következőket:
<br/><br/>
<ol>
<li>Válassza ki a BizTalk szolgáltatás, majd <strong>szinkronizálási kulcsok</strong>. Válassza ki a másodlagos kulcsot. Ha így tesz, a BizTalk szolgáltatás elindul, a másodlagos kulcs használatával.</li>
<li>Válassza ki a tárfiók, és az elsődleges kulcs újragenerálása. Ne feledje, hogy a BizTalk szolgáltatás használ a másodlagos kulcsot.</li>
<li>Válassza ki a BizTalk szolgáltatás, majd <strong>szinkronizálási kulcsok</strong>. Jelölje ki, az elsődleges kulcs. Az új elsődleges kulcs, akkor a rendszer újra létrehozza azt.</li>
<li>Válassza ki a tárfiók, és a másodlagos kulcs újragenerálása.</li>
</ol>
<br/>
A folyamat elnevezése "helyettesítő kulcsok". A célja engedélyezése a felhasználóknak a BizTalk szolgáltatás megszakítása nélkül az elsődleges és a másodlagos kulcs közötti váltáshoz.</td>
</tr>

<tr>
<td><strong>Törlés</strong> az alkalmazás</td>
<td>A BizTalk szolgáltatás és a telepített összes elemet a rendszer eltávolítja.</td>
</tr>
</table>


## <a name="monitor"></a>Figyelés
Nem vonatkozik az ingyenes kiadásra.

A BizTalk szolgáltatás neve kiválasztásakor a figyelés lapján érhető el, és megjeleníti a következő:

##### <a name="metric-graph-displays-the-selected-performance-metrics"></a>Metrika Graph: Megjeleníti a kijelölt metrikák
Ezeket a mérési adja meg a BizTalk szolgáltatás valós idejű értékeit. Úgy dönt, hogy mely metrikák jelennek meg. Egyszerre legfeljebb hat teljesítménymutatók megjelenítése. 

Is beállíthatja a **relatív** vagy **abszolút** értékek és időtartomány **időköz** a megjelenített mérőszámokat. 

##### <a name="to-remove-or-display-metrics-in-the-graph"></a>Távolítsa el, vagy a grafikonon metrikák megjelenítése:
1. Válassza ki a **figyelő** fülre.
2. Válassza ki **metrikák hozzáadása** a tálcán:  
   ![Válassza ki a metrikák hozzáadása][AddMetrics]
3. Ellenőrizze a teljesítményi értékeket, akkor jelenik meg.
4. Válassza ki a pipára térjen vissza a **figyelő** fülre.
5. Válassza ki a kör mellett a metrika az adott metrika értéket a grafikonon jeleníti meg.  
   
    Például a **CPU-használat** metrika szürkén jelenik meg; kimenetét a diagramhoz nem jelenik meg:  
   ![CPU-használat metrika szürkén jelenik meg][GrayedMetric]  
   
    Kimenő kör engedélyezéséhez válassza a szürke a **CPU-használat** metrika kimenetét a diagramon megjelenítendő:  
   ![CPU-használat metrika engedélyezve van][EnabledMetric]
6. A megjelenített diagram és a lista a metrika eltávolításához jelölje ki **törölje metrikát** a tálcán. A metrika vissza felvenni a listára, válassza ki **metrikák hozzáadása** a tálcán, ellenőrizze a mérték, és válassza ki a pipa való visszatéréshez a **figyelő** fülre. Válassza ki a a szürke ahhoz, hogy a mérték kör ki.

## <a name="Metrics"></a>Elérhető
A következő számlálók/metrikák érhetők el:

<table border="1">

<tr>
<td><strong>RountdTrip késés</strong></td>
<td>Az átlagos ideje ezredmásodpercben (ms) feldolgozni egy üzenetet abból az időből, amikor az üzenetet kapja, amíg a teljes dolgozza fel a BizTalk szolgáltatás összes hidak jeleníti meg. Csak a sikeresen feldolgozott üzeneteinek bájtjai számítanak.<br/><br/>
A következő események következnek be, amikor egy Timestamp típusú jön létre:
<ul>
<li>Üzenet kerül, az átjáró</li>
<li>Üzenet annak biztosítására, hogy a cél</li>
<li>Cél választ</li>
<li>Cél nyugtázási választ küldött az átjáró</li>
</ul>
<br/>
Ez a mérőszám a következő számítás eredményét mutatja:
<br/><br/>
[Cél nyugtázási küldött választ az átjáró] - [üzenet kerül, az átjáró]</td>
</tr>
<tr>
<td><strong>Hibákat forrásnál</strong></td>
<td>Nem sikerült üzenetek teljes számát jeleníti meg a BizTalk szolgáltatás, ha a forrás-végpontok üzeneteit húzza.</td>
</tr>
<tr>
<td><strong>CPU-használat</strong></td>
<td>Az átlagos processzoridő % található összes szerepkörpéldány sorolja fel.</td>
</tr>
<tr>
<td><strong>Feldolgozási késleltetés</strong></td>
<td>Az átlagos ideje ezredmásodpercben (ms) feldolgozni egy üzenetet a BizTalk szolgáltatás összes hidak, kivéve az idő a célok jeleníti meg. Csak a sikeresen feldolgozott üzeneteinek bájtjai számítanak.<br/><br/>
A következő események bekövetkezésekor időbélyeg jön létre:

<ul>
<li>Üzenet kerül, az átjáró</li>
<li>Üzenet annak biztosítására, hogy a cél</li>
<li>Cél választ</li>
<li>Cél nyugtázási választ küldött az átjáró</li>
</ul>
<br/>Ez a mérőszám a következő számítás eredményét mutatja:<br/><br/>
[Cél nyugtázási küldött választ az átjáró] - [üzenet kerül, az átjáró] - [cél választ] + [üzenet annak biztosítására, hogy a cél]</td>
</tr>
<tr>
<td><strong>A folyamat sikertelen</strong></td>
<td>Nem sikerült feldolgozni a BizTalk szolgáltatás között a hidak időintervallumon belül-kezelési üzenetek teljes számát jeleníti meg.</td>
</tr>
<tr>
<td><strong>Küldött üzenetek</strong></td>
<td>Adott időintervallumon belül minden hidak között a BizTalk szolgáltatás által küldött üzenetek teljes számát jeleníti meg. Ez a metrika értéke akkor növekszik, ha a folyamat által küldött üzenet eléri az útvonal cél. Ez a metrika nem jelzi, hogy egy üzenet feldolgozása sikeresen.<br/><br/>
A kérelem-válasz-forgatókönyvek a metrika értéke növekszik, ha az útvonal cél egy fogadását nyugtázási küld vissza a feldolgozási sor.</td>
</tr>
<tr>
<td><strong>A Beérkezett üzenetek</strong></td>
<td>Időintervallumon belül minden hidak között a BizTalk szolgáltatás által fogadott üzenetek teljes számát jeleníti meg. Ez a metrika értéke akkor nő, amikor egy új üzenet jelenik meg a folyamat.</td>
</tr>
<tr>
<td><strong>A folyamat üzenetek</strong></td>
<td>Megjeleníti az adott időintervallumon belül a BizTalk szolgáltatás által jelenleg feldolgozott üzenetek teljes száma.</td>
</tr>
<tr>
<td><strong>Az üzenetek feldolgozásának</strong></td>
<td>Sikeresen feldolgozott üzenetek a BizTalk szolgáltatás közötti összes hidak időintervallumon belül teljes számát jeleníti meg. Ez a metrika értéke akkor nő, amikor egy üzenetet a folyamat sikeresen fogad és sikeresen irányítja át a cél.</td>
</tr>
</table>


## <a name="scale"></a>Méretezés
A skála lapon adhat hozzá vagy kivonása a BizTalk szolgáltatás által használt egységek száma. Alapértelmezés szerint van egy egység konfigurálva. További egységek méretezése a BizTalk szolgáltatás lehet hozzáadni. Ha növeli a skála, amelyek növelve az adatátviteli sebességet. Erőforrások mennyisége is növeli, beleértve a telepített hidak, a megállapodások, a LOB-kapcsolatok, és feldolgozási kapacitása révén. Például növelheti a méret 1 egységből 2 egység. Ebben a helyzetben kétszer annyi hidak telepíteni, a megállapodások duplán, duplán a LOB-kapcsolatok és a feldolgozási teljesítmény duplán.

BizTalk kiadásaiban nem képes a skálázási beállítást. Ebben a helyzetben egy egység engedélyezett. Annak meghatározásához, hány egység a edition méretezhetők, tekintse meg [BizTalk szolgáltatások: kiadások diagram](biztalk-editions-feature-chart.md).

Egységek számának növelésével hatással lehet a díjszabás. Ha növeli az egységeket, kiválasztásával **mentése** egy üzenetben arra kéri, ha a számlázási kihatással van. Ezután válasszon ki, a folytatáshoz. Ha növeli a BizTalk szolgáltatás állapota módosítások aktív Updating egységek száma. A frissítés állapota a BizTalk szolgáltatás fut tovább.

[BizTalk szolgáltatások: Kiadások diagram](biztalk-editions-feature-chart.md) "egység".

## <a name="configure"></a>Konfigurálás
A hibrid kapcsolatok nem vonatkozik.

A biztonsági mentés állapotának beállítása None vagy automatikus. Ha nincs beállítva, nem készült biztonsági másolat automatikusan létrejönnek. Ha a beállítás automatikus, konfigurálja a biztonsági mentési helyre, a biztonsági mentéshez, és mennyi ideig megőrzi a biztonsági mentési fájlokat gyakoriságát. 

[BizTalk szolgáltatások: Biztonsági mentése és visszaállítása](biztalk-backup-restore.md) részleteit. 

## <a name="HybridConnections"></a>Hibrid kapcsolatok
Hibrid kapcsolatok csatlakozzon az Azure-alkalmazások, például a Web Apps vagy az Azure App Service Mobile Apps egy helyszíni erőforrást, amely a statikus TCP-port, például az SQL Server, a MySQL, a HTTP webes API-k és a legtöbb egyéni webszolgáltatások használja. BizTalk szolgáltatások felügyelete hibrid kapcsolatok.

Hozzon létre, vagy hibrid kapcsolatok a Azure BizTalk szolgáltatások kezeléséhez, tekintse meg a [hibrid kapcsolatok](integration-hybrid-connection-overview.md).

## <a name="next"></a>Következő lépés
Most, hogy ismeri a különböző lappal, további Azure BizTalk szolgáltatások szolgáltatásait:

* [BizTalk Services: Szabályozás](biztalk-throttling-thresholds.md)  
* [BizTalk Services: Kiállító neve és kiállító kulcsa](biztalk-issuer-name-issuer-key.md)  
* [BizTalk Services: Biztonsági mentés és visszaállítás](biztalk-backup-restore.md)

## <a name="see-also"></a>Lásd még:
* [Hibrid kapcsolatok](integration-hybrid-connection-overview.md)  
* [BizTalk szolgáltatások: Fejlesztői, Basic, Standard és prémium kiadás diagram](biztalk-editions-feature-chart.md)  
* [BizTalk szolgáltatások: kiépítés](biztalk-provision-services.md)  
* [BizTalk szolgáltatások: BizTalk szolgáltatás állapotának diagram](biztalk-service-state-chart.md)  
* [Hogyan kezdhetem el az Azure BizTalk Services SDK használatát](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[Quickstart]: ./media/biztalk-dashboard-monitor-scale-tabs/QuickStartIcon.png
[AddMetrics]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
[GrayedMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
[EnabledMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png

