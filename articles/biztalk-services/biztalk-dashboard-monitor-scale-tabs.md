---
title: Irányítópult, figyelő, skála, konfigurálásához és a BizTalk Services hibrid kapcsolatok |} A Microsoft Docs
description: Ismerje meg a vezérlőelemek és a BizTalk Services teljesítményének figyelése
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 7a1815db-0de2-4274-8be0-198c1b077324
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 3f4763b5e15d4b9b84e868262a9e8538b8a407a2
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51228827"
---
# <a name="review-the-dashboard-monitor-scale-configure-and-hybrid-connection-tabs"></a>Tekintse át az Irányítópult, Figyelő, Skála, Konfigurálás és Hibridkapcsolat lapokat

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Miután létrehozta a BizTalk-szolgáltatás, és az alkalmazás üzembe helyezéséhez, módosíthatja egyes BizTalk-szolgáltatás beállításai és az alkalmazás teljesítményének monitorozásához. 

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

Ekkor megnyílik egy új ablakban a következő lappal. Ez a témakör ismerteti az ezeken a lapokon.

## <a name="quickstart-quickstartquickstart"></a>A rövid útmutató)![Első lépések][Quickstart])
A BizTalk Services kiadásától függően az összes felsorolt lehetőségek előfordulhat, hogy nem érhető el. 

<table border="1">
    <tr>
        <td><strong>Eszközök beszerzése</strong></td>
        <td>Töltse le a BizTalk Services SDK telepítése a Visual Studio-projektsablonok a helyi fejlesztői számítógépen való. Ezek a sablonok létrehozása a <strong>BizTalk Services</strong> (híd) és a <strong>BizTalk-szolgáltatás összetevőinek</strong> (átalakító) Visual Studio-projektek, amelyek a BizTalk-szolgáltatás vannak telepítve.
        <br/><br/>
        <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=302335"> Hogyan kezdhetem meg az Azure BizTalk Services SDK használatával </a> és <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=241589">telepítése az Azure BizTalk Services SDK</a> első lépések lépéseit ismerteti.
        </td>
    </tr>
    <tr>
        <td><strong>Partneri szerződés létrehozása</strong></td>
        <td>Megnyílik az Azure BizTalk Services Portáljára, ahol hozzá partnerek és X12, AS2, hozzon létre Azure-ban üzemeltetett és EDIFACT EDI-szerződést.
        <br/><br/>
        <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=303653">A BizTalk Services portáljának üzenetkezelés EDI-összetevők konfigurálása</a> első lépések lépéseit ismerteti.
        </td>
    </tr>

<tr>
        <td><strong>További információ a BizTalk Services</strong></td>
        <td>Nyissa meg a <a HREF="https://azure.microsoft.com/documentation/services/biztalk-services/">tanulási központ</a> tudhat meg többet az Azure BizTalk Services.</td>
</tr>
</table>


A tálcán a lap alján, a következőket teheti:

<table border="1">

<tr>
<td><strong>Kezelése</strong> az alkalmazás központi telepítésének</td>
<td>Ekkor megnyílik az Azure BizTalk Services portálján. A BizTalk Services portáljának bejárata EDI konfigurálására, beleértve az partnerek hozzáadása és létrehozása X12, AS2, EDIFACT-egyezmények és.
<br/><br/>
Ez ugyanaz, mint a <strong>egyezmények létrehozása</strong> a a <strong>gyors üzembe helyezés</strong> fülre.
<br/><br/>
<a HREF="https://go.microsoft.com/fwlink/p/?LinkID=303653">A BizTalk Services portáljának üzenetkezelés EDI-összetevők konfigurálása</a> további információkkal szolgál a BizTalk Services Portáljára.</td>
</tr>

<tr>
<td><strong>Kapcsolatadatok</strong> , a hozzáférés-vezérlési Namespace</td>
<td>Amikor kiválasztja a kapcsolati adatokat, majd a hozzáférés-vezérlési Namespace, az alapértelmezett kiállító és a kulcs alapértelmezett jelennek meg. Ezeket az értékeket is másolhatja.
<br/><br/>
Az Access Control Portal is megnyithatja. <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=285670">Hozzon létre egy hozzáférés-vezérlés Namespace</a> további információkat biztosít az Access Control portál.</td>
</tr>

<tr>
<td><strong>Kulcsok szinkronizálása</strong> a Storage-fiókban</td>
<td>Storage-fiók létrehozásakor automatikusan létrejön egy elsődleges kulcs és egy másodlagos kulcs. A titkosítási kulcsokat a Tárfiókhoz való hozzáférés szabályozása. A BizTalk-szolgáltatás automatikusan az elsődleges kulcsot használja. <strong>Kulcsok szinkronizálása</strong> engedélyezése a felhasználók az elsődleges és a másodlagos kulcs között a BizTalk-szolgáltatás megszakítása nélkül.
<br/><br/>
Ha például azt szeretné, a BizTalk-szolgáltatás használata egy új elsődleges kulcsot a tárfiók. Ehhez tegye a következőket:
<br/><br/>
<ol>
<li>Válassza ki a BizTalk-szolgáltatás, és válassza ki <strong>kulcsok szinkronizálása</strong>. Válassza ki a másodlagos kulcsot. Ha így tesz, a BizTalk szolgáltatás elindul, a másodlagos kulcs használatával.</li>
<li>Válassza ki a tárfiókját, és az elsődleges kulcs újragenerálása. Ne feledje, hogy a BizTalk-szolgáltatás használ a másodlagos kulcsot.</li>
<li>Válassza ki a BizTalk-szolgáltatás, és válassza ki <strong>kulcsok szinkronizálása</strong>. Most válassza ki az elsődleges kulcsot. Ez az az új elsődleges kulcs újragenerálása meg.</li>
<li>Válassza ki a tárfiókját, és a másodlagos kulcs újragenerálása.</li>
</ol>
<br/>
Ez a folyamat "átváltási kulcsok" nevezzük. A célja annak engedélyezése a felhasználók a BizTalk-szolgáltatás megszakítása nélkül az elsődleges és a másodlagos kulcs közötti váltáshoz.</td>
</tr>

<tr>
<td><strong>Törlés</strong> az alkalmazás</td>
<td>Kiválasztásakor törléséhez a BizTalk-szolgáltatás, és abba telepített összes elemet el lesznek távolítva.</td>
</tr>
</table>


## <a name="dashboard"></a>Irányítópult
A BizTalk Services kiadásától függően az összes felsorolt lehetőségek előfordulhat, hogy nem érhető el. 

Amikor kiválasztja a BizTalk-szolgáltatás nevét, az irányítópult lap jelenik meg. Irányítópult, a következőket teheti:

##### <a name="usage-overview-shows-the-number-of-used-hybrid-connections"></a>Felhasználás: Használt hibrid kapcsolatok számát jeleníti meg.
Az adathasználat is megjeleníti a GB-ban. 

##### <a name="metric-graph-shows-a-fixed-list-of-performance-metrics"></a>: Metrika grafikonja rögzített listájából választható teljesítmény-mérőszámok
Ezen adatok segítségével valós időben értékelhető a BizTalk-szolgáltatás állapotát. Azt is beállíthatja a **relatív** vagy **abszolút** értékek és az időtartományt **időköz** a diagramon megjelenő metrikák. 

Ezeket a teljesítmény-mérőszámok leírását, Ugrás [rendelkezésre álló metrikák](#Metrics) ebben a témakörben.

##### <a name="quick-glance-lists-your-biztalk-service-properties"></a>Gyors áttekintés: Felsorolja a BizTalk-szolgáltatás tulajdonságai
<table border="1">

<tr>
<td><strong>Követési adatbázis hitelesítő adatainak frissítése</strong></td>
<td>A felhasználónév és a nyomkövetési adatbázis szolgáltatásba való bejelentkezéshez használt jelszó megváltozik.</td>
</tr>
<tr>
<td><strong>SSL-tanúsítvány frissítése</strong></td>
<td>A BizTalk-szolgáltatás egy másik SSL-tanúsítvány használatára is frissítheti. Egy önaláírt SSL-tanúsítvány automatikusan jön létre mikor, <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=302280">a BizTalk-szolgáltatás létrehozása</a>.</td>
</tr>
<tr>
<td><strong>Tanúsítvány letöltése</strong></td>
<td>Letöltheti a helyi számítógépre a BizTalk-szolgáltatás által használt SSL-tanúsítványt.</td>
</tr>
<tr>
<td><strong>Állapot</strong></td>
<td>A BizTalk-szolgáltatás aktuális állapotát jeleníti meg. Lásd: <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=329870">BizTalk Services: szolgáltatás állapottáblázata</a>. </td>
</tr>
<tr>
<td><strong>Szolgáltatás URL-címe</strong></td>
<td>A BizTalk-szolgáltatás URL-CÍMÉT. Ez a ugyanaz, mint a <strong>tartomány URL-címe</strong> a BizTalk-szolgáltatás létrehozásakor megadott.</td>
</tr>
<tr>
<td><strong>Nyilvános virtuális IP-cím (VIP) címe</strong></td>
<td>A BizTalk-szolgáltatáshoz rendelt IP-címe. Összes bemeneti végponthoz használt és a kimenő adatforgalmat a forrás-címe. Az IP-cím tartozik a BizTalk-szolgáltatás, mindaddig, amíg a létrehozást. Ha törli a BizTalk-szolgáltatás, az IP-cím van rendelve egy másik BizTalk-szolgáltatás.</td>
</tr>
<tr>
<td><strong>Az ACS-Namespace</strong></td>
<td>A BizTalk-szolgáltatás végzi a hitelesítést.</td>
</tr>
<tr>
<td><strong>Kiadás</strong></td>
<td>Listák a kiadás a BizTalk szolgáltatás létrehozásakor adott meg.</td>
</tr>
<tr>
<td><strong>Hely</strong></td>
<td>Megjeleníti a földrajzi régiót, amelyen a BizTalk-szolgáltatás.</td>
</tr>
<tr>
<td><strong>Létrehozva</strong></td>
<td>Megjeleníti a dátum és a BizTalk-szolgáltatás létrehozásának ideje.</td>
</tr>
<tr>
<td><strong>Nyomkövető adatbázis</strong></td>
<td>Az Azure SQL Database tárolja a nyomkövetési táblát, a BizTalk-szolgáltatás által használt név. 
<br/><br/>
<a HREF="https://go.microsoft.com/fwlink/p/?LinkID=302280">Követelmények Explained</a> részletesen nyomon követése az adatbázison.</td>
</tr>
<tr>
<td><strong>Tárolás figyelés és archiválás</strong></td>
<td>Az Azure-Tárfiók nevét, amely tárolja a BizTalk-szolgáltatás figyelési kimenetét.
<br/><br/>
<a HREF="https://go.microsoft.com/fwlink/p/?LinkID=302280">Követelmények Explained</a> részletesen a Storage-fiókot.</td>
</tr>
<tr>
<td><strong>Előfizetés neve</strong></td>
<td>Felsorolja az előfizetést, amelyen a BizTalk-szolgáltatás. Az előfizetés hozzáférés szabályozza.</td>
</tr>
<tr>
<td><strong>előfizetés-azonosító</strong></td>
<td>Ha egy előfizetés jön létre, egy előfizetés-azonosító automatikusan jön létre. REST API-k használata esetén szükség lehet adja meg az előfizetés-azonosító.</td>
</tr>
</table>

[A BizTalk Services: Kiépítés](https://go.microsoft.com/fwlink/p/?LinkID=302280) BizTalk-szolgáltatás létrehozása lépéseit ismerteti.

##### <a name="manage-connection-information-sync-keys-and-delete-in-the-task-bar"></a>Kezelheti a kapcsolatadatok, a kulcsok szinkronizálása, és törölje a tálcán:
<table border="1">

<tr>
<td><strong>Kezelése</strong> az alkalmazás központi telepítésének</td>
<td>Ekkor megnyílik az Azure BizTalk Services Portáljára. A BizTalk Services portáljának bejárata EDI konfigurálására, beleértve az partnerek hozzáadása és létrehozása X12, AS2, EDIFACT-egyezmények és.
<br/><br/>
Ez ugyanaz, mint a <strong>egyezmények létrehozása</strong> a a <strong>gyors üzembe helyezés</strong> fülre.
<br/><br/>
<a HREF="https://go.microsoft.com/fwlink/p/?LinkID=303653">A BizTalk Services portáljának üzenetkezelés EDI-összetevők konfigurálása</a> további információkkal szolgál a BizTalk Services Portáljára.</td>
</tr>
<tr>
<td><strong>Kapcsolatadatok</strong> , a hozzáférés-vezérlési Namespace</td>
<td>Megjeleníti a hozzáférés-vezérlési Namespace, az alapértelmezett kibocsátó és a kulcs alapértelmezett értékeket; amely lehet másolni.
<br/><br/>
Az Access Control Portal is megnyithatja. Ez hozzáférést vezérlő portál egy megegyezik az Active Directory lehetőséget a bal oldali navigációs ablaktáblán.
<br/><br/>
<a HREF="https://go.microsoft.com/fwlink/p/?LinkID=285670">Az ACS-Namespace kezelése</a> további információkat biztosít az Access Control portál.</td>
</tr>
<tr>
<td><strong>Kulcsok szinkronizálása</strong> a Storage-fiókban</td>
<td>Storage-fiók létrehozásakor automatikusan létrejön egy elsődleges kulcs és egy másodlagos kulcs. A titkosítási kulcsokat a Tárfiókhoz való hozzáférés szabályozása. A BizTalk-szolgáltatás automatikusan az elsődleges kulcsot használja. <strong>Kulcsok szinkronizálása</strong> engedélyezése a felhasználók az elsődleges és a másodlagos kulcs között a BizTalk-szolgáltatás megszakítása nélkül.
<br/><br/>
Ha például azt szeretné, a BizTalk-szolgáltatás használata egy új elsődleges kulcsot a tárfiók. Ehhez tegye a következőket:
<br/><br/>
<ol>
<li>Válassza ki a BizTalk-szolgáltatás, és válassza ki <strong>kulcsok szinkronizálása</strong>. Válassza ki a másodlagos kulcsot. Ha így tesz, a BizTalk szolgáltatás elindul, a másodlagos kulcs használatával.</li>
<li>Válassza ki a tárfiókját, és az elsődleges kulcs újragenerálása. Ne feledje, hogy a BizTalk-szolgáltatás használ a másodlagos kulcsot.</li>
<li>Válassza ki a BizTalk-szolgáltatás, és válassza ki <strong>kulcsok szinkronizálása</strong>. Most válassza ki az elsődleges kulcsot. Ez az az új elsődleges kulcs újragenerálása meg.</li>
<li>Válassza ki a tárfiókját, és a másodlagos kulcs újragenerálása.</li>
</ol>
<br/>
Ez a folyamat "átváltási kulcsok" nevezzük. A célja annak engedélyezése a felhasználók a BizTalk-szolgáltatás megszakítása nélkül az elsődleges és a másodlagos kulcs közötti váltáshoz.</td>
</tr>

<tr>
<td><strong>Törlés</strong> az alkalmazás</td>
<td>A BizTalk-szolgáltatás és a központi telepítésű minden elem el lesznek távolítva.</td>
</tr>
</table>


## <a name="monitor"></a>Figyelés
Nem vonatkozik az ingyenes kiadásra.

Amikor kiválasztja a BizTalk-szolgáltatás nevét, a figyelés lap érhető el, és a következőket jeleníti meg:

##### <a name="metric-graph-displays-the-selected-performance-metrics"></a>Metrika Graph: Kiválasztott teljesítmény-mérőszámait jeleníti meg.
Ezen adatok segítségével valós időben értékelhető a BizTalk-szolgáltatás állapotát. Kiválaszthatja, melyik teljesítmény-mérőszámok jelennek meg. Egyszerre legfeljebb hat teljesítmény-mérőszámok lehet megjeleníteni. 

Azt is beállíthatja a **relatív** vagy **abszolút** értékek és az időtartományt **időköz** a megjelenített mérőszámok. 

##### <a name="to-remove-or-display-metrics-in-the-graph"></a>Metrikák megjelenítése a gráf vagy eltávolítása:
1. Válassza ki a **figyelő** fülre.
2. Válassza ki **metrikák hozzáadása** a tálcán:  
   ![Válassza a metrikák hozzáadása][AddMetrics]
3. Ellenőrizze a teljesítmény-mérőszámok meg szeretné jeleníteni.
4. Kattintson a pipa, térjen vissza a **figyelő** fülre.
5. Válassza ki a kört a adott mérőszám értéke a diagramon megjelenítendő metrikát mellett.  
   
    Ha például a **CPU-használat** metrika szürkén jelenik meg; a kimenetét a gráf nem jelenik meg:  
   ![CPU-használati metrikája szürkén jelenik meg][GrayedMetric]  
   
    Válassza a szürke kör ahhoz, hogy ki a **CPU-használat** kimenetét a diagramon megjelenítendő metrikát:  
   ![CPU-használat a mérőszám engedélyezve van][EnabledMetric]
6. A megjelenített graph és a lista egy metrika eltávolításához jelölje ki **metrika törlése** a tálcán. Vissza a metrika hozzáadása a listához, válassza ki a **metrikák hozzáadása** a tálcán, ellenőrizze a metrika, és kattintson a pipa, térjen vissza a **figyelő** fülre. Válassza ki a a szürke ahhoz, hogy a metrika kör ki.

## <a name="Metrics"></a>Rendelkezésre álló metrikák
A következő számlálókat/teljesítménymetrikák érhetők el:

<table border="1">

<tr>
<td><strong>RountdTrip késés</strong></td>
<td>Az átlagos idő ezredmásodpercben (ms) feldolgozni egy üzenetet az időpont, az üzenet érkezik, amíg az üzenetet feldolgoz a BizTalk-szolgáltatás által az összes hidak jeleníti meg. Csak a sikeresen feldolgozott üzenetek számítanak.<br/><br/>
Az alábbi események történnek, ha egy időbélyeg jön létre:
<ul>
<li>Üzenet kerül, az átjáró</li>
<li>Üzenet továbbítása történik a célhelyre</li>
<li>Cél választ</li>
<li>Cél nyugtázási válasz elküldése az átjáróhoz</li>
</ul>
<br/>
Ez a metrika az alábbi számítás eredménye látható:
<br/><br/>
[Cél nyugtázási küldött válasz az átjárónak] - [üzenet kerül, az átjáró]</td>
</tr>
<tr>
<td><strong>Hibák a forrásnál</strong></td>
<td>A sikertelen üzenetek teljes számát jeleníti meg a BizTalk-szolgáltatás, ha a forrás-végpontokra érkező üzenetek beolvasás által.</td>
</tr>
<tr>
<td><strong>CPU-használat</strong></td>
<td>Az összes szerepkörpéldány átlagos processzoridő % sorolja fel.</td>
</tr>
<tr>
<td><strong>Feldolgozás késéssel</strong></td>
<td>Az átlagos idő ezredmásodpercben (ms) feldolgozni egy üzenetet a BizTalk szolgáltatás összes hidak, kivéve a destinations töltött idő jeleníti meg. Csak a sikeresen feldolgozott üzenetek számítanak.<br/><br/>
Minden, az alábbi események történnek, ha egy időbélyeg jön létre:

<ul>
<li>Üzenet kerül, az átjáró</li>
<li>Üzenet továbbítása történik a célhelyre</li>
<li>Cél választ</li>
<li>Cél nyugtázási válasz elküldése az átjáróhoz</li>
</ul>
<br/>Ez a metrika az alábbi számítás eredménye látható:<br/><br/>
[Cél nyugtázási küldött válasz az átjárónak] - [üzenet kerül, az átjáró] - [cél választ] + [üzenet a cél van átirányítva]</td>
</tr>
<tr>
<td><strong>A folyamat sikertelen</strong></td>
<td>Adott időintervallumon belül minden hidak között a BizTalk-szolgáltatás által a feldolgozás során sikertelen üzenetek teljes számát jeleníti meg.</td>
</tr>
<tr>
<td><strong>Elküldött üzenetek</strong></td>
<td>Adott időintervallumon belül minden hidak között a BizTalk-szolgáltatás által küldött üzenetek teljes számát jeleníti meg. Ez a mérőszám értéke akkor növekszik, amikor a folyamat által küldött üzenet eléri az útválasztási cél. Ez a metrika nem jelzi, hogy egy üzenet feldolgozása sikeresen megtörtént.<br/><br/>
A kérés-válasz forgatókönyvekben a metrika van növekszik, ha az útvonal cél a nyugta nyugtázása küld vissza a folyamatot.</td>
</tr>
<tr>
<td><strong>Beérkezett üzenetek</strong></td>
<td>Adott időintervallumon belül minden hidak között a BizTalk-szolgáltatás által fogadott üzenetek teljes számát jeleníti meg. Ez a mérőszám értéke akkor növekszik, amikor egy új üzenet érkezik, a folyamat.</td>
</tr>
<tr>
<td><strong>A folyamat üzeneteket</strong></td>
<td>Megjeleníti az adott időintervallumon belül a BizTalk-szolgáltatás által jelenleg feldolgozott üzenetek teljes száma.</td>
</tr>
<tr>
<td><strong>Feldolgozott üzenetek</strong></td>
<td>Sikeresen feldolgozni a BizTalk-szolgáltatás által az összes hidak adott időintervallumon belül üzenetek teljes számát jeleníti meg. Ez a mérőszám értéke akkor növekszik, amikor egy üzenet a folyamat sikeresen fogadta, és sikeresen irányítja a cél.</td>
</tr>
</table>


## <a name="scale"></a>Méretezés
A méretezés lapon adhatók hozzá vagy kivonása a BizTalk-szolgáltatás által használt egységek számát. Alapértelmezés szerint nincs ilyen egység konfigurálva. A BizTalk-szolgáltatás méretezése további egységeket is hozzáadhatók. Ha növeli a méretezési csoport, növeli a teljesítményt. Erőforrások mennyisége is nő, beleértve a telepített hidak, szerződések, LOB-kapcsolatokat, és a feldolgozási teljesítményt. Például megnöveli a méretezési csoport 1 egységből 2 egységre. Ebben a helyzetben üzembe helyezése a double a hidak száma, a szerződések duplán, a LOB-kapcsolatokat duplán és duplán a feldolgozási teljesítményt.

BizTalk kiadásaiban nem kínál a skálázási beállítást. Ebben az esetben egy egység számára engedélyezett. Annak meghatározásához, hány egység a kiadás skálázhatók, tekintse meg [BizTalk Services: kiadások diagramja](biztalk-editions-feature-chart.md).

Egységek számának növelése hatással lehet díjszabása. Ha növeli az egységeket, kiválasztása **mentése** megjelenít egy üzenetet, amely arra kéri, ha a számlázási kihatással van. Ezután válassza ki, a folytatáshoz. Ha növeli a egység, a BizTalk-szolgáltatás állapota módosítások aktív frissítési számát. A frissítés állapota a BizTalk-szolgáltatás továbbra is futtassa.

[A BizTalk Services: Kiadások diagramja](biztalk-editions-feature-chart.md) határozza meg az "Egység".

## <a name="configure"></a>Konfigurálás
A hibrid kapcsolatok nem vonatkozik.

A biztonsági mentés állapota beállítása None vagy automatikus. Ha a beállítás a nincs, nem készült biztonsági másolat automatikusan jönnek létre. Ha a beállítás automatikus, konfigurálja a biztonsági mentési helyre, a gyakoriságot, hogy a biztonságimásolat-fájlokat a biztonsági mentés, és mennyi ideig. 

[BizTalk Services: Biztonsági mentés és visszaállítás](biztalk-backup-restore.md) részleteit. 

## <a name="HybridConnections"></a>Hibrid kapcsolatok
Hibrid kapcsolatok a helyszíni erőforrásokhoz statikus TCP-portot, például az SQL Server, MySQL, HTTP Web API-k és a legtöbb egyéni webszolgáltatáshoz használó Azure-alkalmazások, például a Web Apps vagy az Azure App Service Mobile Apps csatlakoztatása. A BizTalk Services hibrid kapcsolatok kezeli.

Hozzon létre, vagy az Azure BizTalk Services hibrid kapcsolatok kezelése, tekintse meg [hibrid kapcsolatok](integration-hybrid-connection-overview.md).

## <a name="next"></a>Következő lépés
Most, hogy már ismeri a különböző lappal, további Azure BizTalk Services szolgáltatásokkal kapcsolatban:

* [BizTalk Services: Szabályozás](biztalk-throttling-thresholds.md)  
* [BizTalk Services: Kiállító neve és kiállító kulcsa](biztalk-issuer-name-issuer-key.md)  
* [BizTalk Services: Biztonsági mentés és visszaállítás](biztalk-backup-restore.md)

## <a name="see-also"></a>Lásd még:
* [Hibrid kapcsolatok](integration-hybrid-connection-overview.md)  
* [A BizTalk Services: Fejlesztői, alapszintű, Standard és prémium kiadások diagramja](biztalk-editions-feature-chart.md)  
* [A BizTalk Services: kiépítés](biztalk-provision-services.md)  
* [BizTalk Services: A BizTalk szolgáltatás Állapottáblázata](biztalk-service-state-chart.md)  
* [Hogyan kezdhetem el az Azure BizTalk Services SDK használatát](https://go.microsoft.com/fwlink/p/?LinkID=302335)

[Quickstart]: ./media/biztalk-dashboard-monitor-scale-tabs/QuickStartIcon.png
[AddMetrics]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
[GrayedMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
[EnabledMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png

