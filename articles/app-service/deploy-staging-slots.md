---
title: Állítsa be átmeneti környezeteket az Azure App Service web Apps-alkalmazások |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja a szakaszos közzétételt a web apps az Azure App Service-ben.
services: app-service
documentationcenter: ''
author: cephalin
writer: cephalin
manager: jpconnoc
editor: mollybos
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2019
ms.author: cephalin
ms.openlocfilehash: fd488d475e24bc1aeebfa49b9d81b04ebae449ff
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445600"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Állítsa be átmeneti környezeteket az Azure App Service-ben
<a name="Overview"></a>

A web app, webalkalmazás Linuxon, a mobil háttérrendszer vagy az API-alkalmazás telepítésekor [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714), használhat egy külön üzembe helyezési pont helyett az alapértelmezett üzemelési amikor futtat az a **Standard**, **Prémium**, vagy **elkülönített** App Service-csomag szint. Üzembe helyezési tárhelyek élő alkalmazások saját állomásnevek. Alkalmazás tartalmát és a konfigurációs elemek felcserélhetők két üzembe helyezési tárhely, többek között az üzemelési között. 

Egy nem éles tárhely, az alkalmazás üzembe helyezése a következő előnyökkel jár:

* Egy átmeneti üzembe helyezési pont az alkalmazások változásairól sikeressége az üzemelési előtt ellenőrizheti.
* Alkalmazások üzembe helyezése egy tárhely először és sikeressége az éles környezetbe biztosítja, hogy, hogy a összes példányát a tárolóhely, mielőtt éles környezetben a felcserélés folyamatban vannak bemelegíteni. Ez nem jár állásidővel, az alkalmazás központi telepítésekor. A forgalom átirányítása zökkenőmentesen, és nincsenek kérelmek eldobásakor felcserélési művelet miatt. A teljes munkafolyamat konfigurálásával automatizálható [automatikus felcserélés](#Auto-Swap) amikor előtti felcserélés érvényesítési nincs szükség.
* A korábban előkészített alkalmazással tárolóhely egy éles környezetbe való áttérés után most már rendelkezik a korábbi éles alkalmazások. Ha a módosításokat az üzemelési a felcserélés nem a várt módon, az ugyanazon a felcserélési funkció azonnal az "utolsó ismert jó webhely" első végezheti vissza.

Minden App Service-csomag szint támogatja az üzembe helyezési pontok eltérő számú. Nem jár további költségekkel az üzembe helyezési pontok használatakor. Ismerje meg, hány tárhelyek a az alkalmazás réteg által támogatott, lásd: [az App Service korlátai](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits). 

Skálázhatók az alkalmazások a másik tarifacsomagra, győződjön meg róla, hogy a célként megadott szint támogatja-e az alkalmazás már használja tárolóhelyeinek száma. Például, ha az alkalmazás több mint öt tárolóhelyei, nem skálázhatja azt le a a **Standard** tier, mert a **Standard** réteg csak öt üzembe helyezési pontok támogatja. 

<a name="Add"></a>

## <a name="add-a-slot"></a>Tárhely felvétele
Az alkalmazásnak futnia kell a **Standard**, **prémium szintű**, vagy **elkülönített** szint ahhoz, hogy több üzembe helyezési pontok engedélyezése.

1. Az a [az Azure portal](https://portal.azure.com/), nyissa meg az alkalmazás [erőforráslapján](../azure-resource-manager/manage-resources-portal.md#manage-resources).

2. A bal oldali panelen válassza ki a **üzembe helyezési pontok** > **tárhely felvétele**.
   
    ![Adjon hozzá egy új üzembe helyezési pont](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Ha az alkalmazás már nem szerepel a **Standard**, **prémium**, vagy **elkülönített** szint, kap egy üzenetet, amely azt jelzi, hogy a kétlépcsős közzététel engedélyezése a támogatott réteget. Ezen a ponton rendelkezik-e a kívánt **frissítése** és nyissa meg a **méretezési** lapon az alkalmazás a folytatás előtt.
   > 

3. Az a **tárhely felvétele** párbeszédpanelt, nevezze el a tárolóhely, és válassza ki, hogy egy alkalmazás-konfiguráció egy másik üzembe helyezési pont klónozásához. Válassza ki **Hozzáadás** folytatásához.
   
    ![Konfiguráció forrása](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    Egy bármely meglévő tárolóhelyről származó konfiguráció klónozhat. Beállítások, amely képes lehet klónozni Alkalmazásbeállítások, kapcsolati karakterláncok, nyelvi keretrendszer-verziókat, web sockets, HTTP-verzió és platform bitszámértékének tartalmazza.

4. A tárolóhely hozzáadása után válassza ki a **bezárásához** a párbeszédpanel bezárásához. Most már megjelenik az új tárhely a **üzembe helyezési pontok** lapot. Alapértelmezés szerint **forgalom %** az üzemelési minden ügyfél forgalom az új üzembe helyezési pont 0 értékre van állítva.

5. Válassza ki az új üzembe helyezési pont, hogy a tárolóhely-erőforrások oldalának megnyitásához.
   
    ![Üzembe helyezési pont címe](./media/web-sites-staged-publishing/StagingTitle.png)

    Az előkészítési pont is rendelkezik felügyeleti csakúgy, mint bármely más App Service-alkalmazás. A helyezési pont konfigurációját módosíthatja. A tárolóhely neve látható az oldal tetején lévő emlékezteti, hogy az üzembe helyezési pont látja.

6. Válassza ki a tárolóhely erőforrás oldalon az alkalmazás URL-CÍMÉT. Üzembe helyezési pont a saját gazdagép nevével és élő alkalmazások is. Az üzembe helyezési pont való nyilvános hozzáférés korlátozása, lásd: [Azure App Service az IP-korlátozások](app-service-ip-restrictions.md).

Az új üzembe helyezési pont nem tartalmaz, akkor is, ha a beállításokat egy másik tárolóhelyről klónozását. Például végezheti [közzététele a gittel tárolóhely](app-service-deploy-local-git.md). A tárolóhely egy másik adattárban ágat vagy egy másik adattárban telepítheti. 

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>Mi történik a során a lapozófájl-kapacitás

### <a name="swap-operation-steps"></a>Lapozófájl-kapacitás műveleti lépés

(Általában az előkészítési ponton történő üzemelési) két pontok cseréje, App Service-ben győződjön meg arról, hogy a céltárolóhelyként nem szoftverkarbantartás a következőket hajtja végre:

1. A következő beállításokat alkalmazni (például az üzemelési) céltárolóhelyen megadott összes példányát, a forrás tárolóhely: 
    - [Tárolóhely-specifikus](#which-settings-are-swapped) Alkalmazásbeállítások és a kapcsolati karakterláncok, ha van ilyen.
    - [Folyamatos üzembe helyezés](deploy-continuous-deployment.md) beállításait, ha engedélyezve van.
    - [App Service-hitelesítés](overview-authentication-authorization.md) beállításait, ha engedélyezve van.
    
    Mindkét esetben indítja el a forrás tárolóhely újraindításához szereplő összes példányt. Során [felcserélés előnézettel](#Multi-Phase), ez az első fázis végét jelöli. A felcserélési művelet szüneteltetve van, és ellenőrizheti, hogy a forrás-tárolóhely a céltárolóhelyen megadott beállításokkal megfelelően működik.

1. Várjon, amíg minden példány a forrás adatszalagot az újraindítás végrehajtásához. Minden olyan példány nem indul újra, ha a felcserélési műveletet átirányítja a forrás minden módosítás visszaáll, és leállítja a műveletet.

1. Ha [helyi gyorsítótár](overview-local-cache.md) van engedélyezve, aktiválja a helyi gyorsítótár inicializálása azáltal, hogy egy HTTP-kérelem a kérelem legfelső szintű ("/") a forrás tárolóhely minden példányán. Várjon, amíg az egyes példányok bármilyen HTTP-választ adja vissza. Helyi gyorsítótár inicializálása egy másik újraindítási hatására az összes példányt.

1. Ha [automatikus felcserélés](#Auto-Swap) engedélyezve van a [egyéni bemelegítési](#Warm-up), eseményindító [alkalmazás kezdeményezés](https://docs.microsoft.com/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) azáltal, hogy egy HTTP-kérelem a kérelem legfelső szintű ("/") a forrás minden példányán tárolóhely.

    Ha `applicationInitialization` nincs megadva, aktiválja a HTTP-kérést, az egyes példányok a forrás tárolóhely alkalmazás gyökérkönyvtárába. 
    
    Ha egy példány bármilyen HTTP-választ ad vissza, a rendelkezik tekinthető kell bemelegíteni.

1. Ha az adatforrás tárolóhelyen lévő összes példány sikeresen vannak bemelegíteni, a két pontok cseréje az útválasztási szabályokat a két pontok között. Elvégezte a lépést céltárolóhelyen megadott (például az üzemelési) rendelkezik az alkalmazást, amelyet korábban bemelegíteni a forrás-tárolóhely.

1. Most, hogy a forrás tárolóhely az üzem előtti felcserélés alkalmazás korábban már rendelkezik a céltárolóhelyen megadott, az összes beállítás alkalmazása és a példányok újraindítása a azonos műveletet végrehajtani.

A felcserélési művelet bármely ponton inicializálása a átváltva alkalmazások összes feladatát a forrás tárolóhely történik. A céltárolóhelyen megadott online állapotban marad, amíg folyamatban van, a forrás tárolóhely előkészített és bemelegíteni, függetlenül attól, ahol a felcserélés sikeres vagy sikertelen lesz. Üzemelési és előkészítési pont cseréje, győződjön meg arról, hogy az éles tárolóhelyre mindig a céltárolóhelyen megadott. Ezzel a módszerrel a felcserélési művelet nincs hatással az éles alkalmazások.

### <a name="which-settings-are-swapped"></a>Mely beállításai vannak a felcserélés?
Az egy másik üzembe helyezési pont konfigurációs klónozásakor a klónozott konfigurációs szerkeszthető. Bizonyos konfigurációs elemek között a lapozófájl-kapacitás (nem tárolóhely adott), hajtsa végre a tartalom mivel ugyanaz a tárolóhely más konfigurációs elemek maradni a lapozófájl-kapacitás (tárolóhely adott) után. A következő listákban megjelenítése a beállításokat, ha a pontok cseréje.

**Beállításokat, amelyeket a rendszer elküldje**:

* Általános beállítások; például keretrendszer verziója, 32 vagy 64-bites, a web sockets
* Alkalmazásbeállítások (beállítható úgy, hogy a tárhely-en)
* Kapcsolati karakterláncok (beállítható úgy, hogy a tárhely-en)
* Leírók leképezése
* Monitorozási és diagnosztikai beállítások
* A nyilvános tanúsítványok
* WebJobs-tartalom
* Hibrid kapcsolatok *
* Virtuális hálózati integráció *
* Szolgáltatásvégpontokat *
* Azure Content Delivery Network *

A funkciók egy csillag (*) kell tenni a bővítőhelyre kiemelt bevezetését tervezzük. 

**Beállítások, amely nem cserélhető**:

* Közzétételi végpontjai
* Egyéni tartománynevek
* Privát tanúsítványok és az SSL-kötések
* Skála beállításai
* Webjobs-feladatok ütemezők
* Az IP-korlátozások
* Folyamatos üzem
* Beállítások protokollt (HTTPS, TLS-verziót, ügyfél-tanúsítványok)
* Diagnosztikai beállítások
* Eltérő eredetű erőforrások megosztása (CORS)

<!-- VNET and hybrid connections not yet sticky to slot -->

Egy alkalmazás beállítás vagy kapcsolati karakterlánc (nem a felcserélés) egy adott tárhely anyagot konfigurálásához nyissa meg a **konfigurációs** lap, tárolóhely. Hozzáadása vagy szerkesztése egy beállítást, és válassza **üzembe helyezési tárhelybeállítás**. A jelölőnégyzet bejelölésével utasítja az App Service, hogy a beállítás nem cserélhető. 

![Tárhelybeállítás](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Két pontok cseréje 
Az alkalmazás a kicserélheti az üzembe helyezési pontok **üzembe helyezési pontok** oldal és a **áttekintése** lap. A tárolóhelycsere a technikai részletekért lásd: [mi történik a lapozófájl-kapacitás során](#AboutConfiguration).

> [!IMPORTANT]
> Mielőtt egy alkalmazást, egy üzembe helyezési pont akkor váltás éles üzemre, ellenőrizze, hogy éles üzemi-e a céltárolóhelyen megadott, és, hogy az összes forrás nyílásba beállításai pontosan, ahogy az éles környezetben legyen.
> 
> 

Az üzembehelyezési pontok cseréje:

1. Nyissa meg az alkalmazás **üzembe helyezési pontok** lapon, és válassza **felcserélése**.
   
    ![Lapozófájl-kapacitás gomb](./media/web-sites-staged-publishing/SwapButtonBar.png)

    A **felcserélése** párbeszédpanelen a kijelölt forrás- és a céltárolóhelyen módosítani a-beállításokat ismerteti.

2. Válassza ki a kívánt **forrás** és **cél** tárolóhelyek. A cél általában az éles webalkalmazásra. Jelölje be a **az adatforrás-módosításokkal** és **cél módosítások** követését, és győződjön meg arról, hogy a konfigurációs módosítások várható. Ha elkészült, kicserélheti tárolóhelyben azonnal kiválasztásával **lapozófájl-kapacitás**.

    ![Felcserélés befejezése](./media/web-sites-staged-publishing/SwapImmediately.png)

    A céltárolóhelyen megadott lenne futtatásának módját az új beállításokkal előtt a felcserélés ténylegesen történik, ne jelölje ki a **lapozófájl-kapacitás**, de kövesse a [felcserélés előnézettel](#Multi-Phase).

3. Amikor végzett, zárja be a párbeszédpanelt kiválasztásával **bezárásához**.

Ha bármilyen problémába ütközik, tekintse meg [cseréje hibaelhárítása](#troubleshoot-swaps).

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Felcserélés előnézettel (többfázisú lapozó)

> [!NOTE]
> Felcserélés előnézettel a Linuxos web apps nem támogatott.

Előtt, váltás éles üzemre, mert a céltárolóhelyként, ellenőrizze, hogy az alkalmazás fut-e a átváltva beállításokkal. Az adatforrás-tárolóhelyen is bemelegíteni, amely üzleti szempontból alapvető fontosságú alkalmazások esetében fontos felcserélés befejezése előtt.

A felcserélés előnézettel hajt végre, amikor az App Service végez azonos [felcserélési művelet](#AboutConfiguration) azonban megszakítja az első lépés után. Az előkészítési ponton található az eredmény ezután ellenőrizheti a felcserélés befejezése előtt. 

Ha megszakítja a felcserélés, az App Service konfigurációs elemek átirányítja a forrás újra alkalmazza.

A felcserélés előnézettel:

1. Kövesse a [üzembehelyezési pontok cseréje](#Swap) de válassza **végrehajtása felcserélés előnézettel**.

    ![Felcserélés előnézettel](./media/web-sites-staged-publishing/SwapWithPreview.png)

    A párbeszédpanel bemutatja, hogyan 1. fázis a forrás-tárolóhely a konfiguráció módosításait, és hogyan 2. fázis: a forrás- és tárolóhely módosul.

2. Ha készen áll a felcserélés indítása, válassza ki a **felcserélés indítása**.

    1\. fázis befejezése, értesítést kap a párbeszédpanelen. Előzetes verzió a lapozófájl-kapacitás, a forrás-tárolóhely a `https://<app_name>-<source-slot-name>.azurewebsites.net`. 

3. Ha készen áll a függőben lévő felcserélés befejezéséhez, válassza ki a **felcserélés befejezése** a **felcserélési művelet** válassza **felcserélés befejezése**.

    Egy függőben lévő felcserélés érvénytelenítése, jelölje be **Mégse felcserélés** helyette.

4. Amikor végzett, zárja be a párbeszédpanelt kiválasztásával **bezárásához**.

Ha bármilyen problémába ütközik, tekintse meg [cseréje hibaelhárítása](#troubleshoot-swaps).

A többfázisú felcserélés automatizálása, tekintse meg a [automatizálása a PowerShell-lel](#automate-with-powershell).

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>Állítsa vissza a lapozófájl-kapacitás
Bármilyen hiba után egy tárolóhelycsere céltárolóhelyen megadott (például az üzemelési) történik, ha vissza a tárolóhelyek felcserélés előtti állapotába pontjai az azonos két azonnal.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Automatikus felcserélés konfigurálása

> [!NOTE]
> Automatikus felcserélés nem támogatja a web apps on Linuxhoz.

Automatikus felcserélés egyszerűsítheti az Azure DevOps-forgatókönyvekre, ahol szeretné helyezze üzembe az alkalmazást folyamatosan nulla kiküszöbölik és állásidő nélkül az alkalmazás ügyfelek számára. Ha automatikus felcserélés engedélyezve van egy tárolóhelyen éles környezetben, minden alkalommal, továbbítsa a kód módosításait az, hogy a tárolóhely, az App Service automatikusan [felcserélése az alkalmazás éles környezetbe](#swap-operation-steps) után folyékonnyá a forrás-tárolóhely.

   > [!NOTE]
   > Mielőtt állít be automatikus felcserélés az éles üzembe helyezési pont, fontolja meg automatikus felcserélés célja nem éles tárhely a tesztelés.
   > 

Automatikus felcserélés konfigurálása:

1. Nyissa meg az alkalmazás erőforrás-lapon. Válassza ki **üzembe helyezési pontok** >  *\<kívánt forrás >*  > **konfigurációs**  >  **Általános beállítások**.
   
2. A **engedélyezve van az automatikus felcserélés**válassza **a**. Válassza ki a kívánt cél tárhely **automatikus felcserélés üzembe helyezési pont**, és válassza ki **mentése** a parancssávon. 
   
    ![Automatikus felcserélés konfigurálása szolgáló kiválasztások](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Hajtsa végre egy kódot leküldéses átirányítja a forrás. Automatikus felcserélés rövid idő múlva történik, és a frissítés is megjelenik a cél a tárhely URL-címen.

Ha bármilyen problémába ütközik, tekintse meg [cseréje hibaelhárítása](#troubleshoot-swaps).

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>Adja meg az egyéni bemelegítési
Ha a [automatikus felcserélés](#Auto-Swap), előfordulhat, hogy egyes alkalmazások egyéni bemelegítési műveletek a felcserélés előtt. A `applicationInitialization` konfigurációs elem a Web.config fájlban lehetővé teszi, hogy egyéni inicializálási műveletek adható meg. A [felcserélési művelet](#AboutConfiguration) megvárja, amíg az egyéni bemelegítési befejezését, mielőtt a célként megadott tárhellyel való felcserélése. Íme egy minta web.config kódrészletet.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

Testreszabásáról további információkat a `applicationInitialization` elem, lásd: [leggyakoribb slot swap sikertelen, és hogyan javíthatók](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/).

Testre szabhatja a bemelegítési viselkedését az egyik vagy mindkét alábbi [Alkalmazásbeállítások](configure-common.md):

- `WEBSITE_SWAP_WARMUP_PING_PATH`: Pingelje webhelyét ízelítőt elérési útja. Adja hozzá ennek az alkalmazásbeállításnak értékeként perjellel adnak meg egyéni elérési úttal, amely a kezdődik. Például: `/statuscheck`. Az alapértelmezett érték `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Érvényes HTTP-válaszkódot a bemelegítési művelethez. Adja hozzá ennek az alkalmazásbeállításnak a HTTP-kódok vesszővel tagolt listája. Például `200,202` . A visszaadott állapotkód nem szerepel a listán, ha a melegítési és a Váltás műveletek le vannak állítva. Alapértelmezés szerint minden válaszkódot érvényesek.

Ha bármilyen problémába ütközik, tekintse meg [cseréje hibaelhárítása](#troubleshoot-swaps).

## <a name="monitor-a-swap"></a>A figyelő a lapozófájl-kapacitás

Ha a [felcserélési művelet](#AboutConfiguration) végrehajtásához, hosszú ideig tart a felcserélési művelet az információkat szerezhet a [tevékenységnapló](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

A portálon a bal oldali ablaktáblán, az alkalmazás erőforrás oldalán válassza **tevékenységnapló**.

Csereművelet jelenik meg a napló-lekérdezéshez, mivel `Swap Web App Slots`. Bontsa ki azt, és válassza ki az egyik suboperations vagy hibákat a részletek megtekintéséhez.

## <a name="route-traffic"></a>Forgalom irányítása

Alapértelmezésben a kéréseket az alkalmazás éles URL-címe (`http://<app_name>.azurewebsites.net`) legyenek átirányítva az éles webalkalmazásra. A forgalom egy része azt egy másik tárolóhelyre irányíthatja. Ez a funkció akkor hasznos, ha a felhasználói visszajelzések szüksége lesz egy új frissítést, de Ön nem készen áll az éles környezetbe feloldásához.

### <a name="route-production-traffic-automatically"></a>Automatikusan éles forgalom irányítása

Éles forgalom irányítására automatikusan:

1. Nyissa meg az alkalmazás erőforrás-oldalon, és válassza ki **üzembe helyezési pontok**.

2. Az a **forgalom %** oszlopa a tárolóhely kívánt átirányítása, adja meg (0 és 100) közötti százalékos képviselő szeretné átirányítani teljes forgalom mennyisége. Kattintson a **Mentés** gombra.

    ![A forgalom százalékos arány](./media/web-sites-staged-publishing/RouteTraffic.png)

A beállítás mentése után a megadott ügyfeleinek százalékos aránya véletlenszerűen irányítja a rendszer a nem éles tárhely. 

Miután az ügyfél automatikusan annak biztosítására, hogy egy adott tárhely annak "rögzített", hogy a tárolóhely esetében az élettartam adott ügyfél-munkamenet. Az ügyfélböngészőnek, mely a munkamenet rögzítve van naplófájlbejegyzéseket átnézve tárolóhely láthatja a `x-ms-routing-name` cookie-t a HTTP-fejléceket. Egy kérelmet, amely a "átmeneti" tárolóhely irányítja a rendszer rendelkezik a cookie-k `x-ms-routing-name=staging`. Egy kérelmet, amely az éles tárolóhelyre lesz irányítva a cookie-k rendelkezik `x-ms-routing-name=self`.

### <a name="route-production-traffic-manually"></a>Manuálisan éles forgalom irányítása

Mellett automatikus forgalom-útválasztást, App Service-ben is irányíthatja a kérelmeket egy adott tárhely. Ez akkor hasznos, ha azt szeretné, hogy a felhasználókat, hogy engedélyezve van a hibajelentések vagy tilthatják le a béta-alkalmazás. Éles forgalom irányítására manuálisan, használja a `x-ms-routing-name` lekérdezési paraméter.

Ahhoz, hogy a felhasználók tilthatják le a béta-alkalmazás, például helyezheti ezt a hivatkozást a weblapon:

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

A karakterlánc `x-ms-routing-name=self` adja meg az éles webalkalmazásra. Miután az ügyfél böngészője fér hozzá a hivatkozás, a rendszer átirányítja az éles webalkalmazásra. Minden későbbi kérés a `x-ms-routing-name=self` cookie-t, amely rögzíti a munkamenet az éles webalkalmazásra.

Ahhoz, hogy a felhasználók engedélyezve a béta-alkalmazását, az azonos lekérdezési paraméter értéke a nem éles tárolóhely neve. Például:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

Alapértelmezés szerint új pontok vannak megadva az útválasztási szabály `0%`, szürke színnel megjelenített. Ha explicit módon beállította ezt az értéket `0%` (fekete szöveg látható), a felhasználók férhetnek hozzá az előkészítési pont manuálisan használatával a `x-ms-routing-name` lekérdezési paraméter. De azok nem lesznek irányítva a tárolóhely automatikusan, mivel az útválasztási százalékos értéke 0. Ez az, ahol "elrejtheti" az előkészítési pont nyilvános során tesztelheti a módosításokat a tárolóhely a belső csapatok így speciális forgatókönyv.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>A pont törlése

Nyissa meg az alkalmazás erőforrás-lapon. Válassza ki **üzembe helyezési pontok** >  *\<törlése tárolóhely >*  > **áttekintése**. Válassza ki **törlése** a parancssávon.  

![Üzembehelyezési pont törlése](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatizálás a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az Azure PowerShell modul, amely Azure-t a Windows PowerShell-lel, beleértve az Azure App Service-ben üzembe helyezési pontok felügyeletének támogatását, így kezelheti-parancsmagokat kínál.

Információk az telepítése és konfigurálása az Azure PowerShell-lel és az Azure PowerShell-lel hitelesítése az Azure-előfizetésében: [telepítése és konfigurálása a Microsoft Azure PowerShell](/powershell/azure/overview).  

---
### <a name="create-a-web-app"></a>Webalkalmazás létrehozása
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-a-slot"></a>Tárhely létrehozása
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>A lapozófájl-kapacitás (többfázisú lapozó) szolgáltatás előzetes kezdeményezzen, és átirányítja a forrás célhelyre tárolóhely konfigurációjának alkalmazása
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>Megszakítja a függőben lévő felcserélés (tekintse át a lapozó) és a forrás tárolóhely-konfiguráció visszaállítása
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>Üzembehelyezési pontok cseréje
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Lapozófájl-kapacitás-események figyelése a tevékenységnaplóban
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>A pont törlése
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

---
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>Automatizálás a parancssori felület használatával

A [Azure CLI-vel](https://github.com/Azure/azure-cli) parancsok a üzembe helyezési pontok, lásd: [az webapp deployment slot](/cli/azure/webapp/deployment/slot).

## <a name="troubleshoot-swaps"></a>Hibaelhárítás cseréje

Során bármilyen hiba esetén egy [tárolóhelycsere](#AboutConfiguration), hogy be van jelentkezve *D:\home\LogFiles\eventlog.xml*. Azt is rögzíti az alkalmazás-specifikus hibanaplójában.

Az alábbiakban néhány gyakori lapozófájl-kapacitás hibák:

- Egy HTTP-kérelem az alkalmazás gyökérkönyvtárában van túllépte az időkorlátot. A csereművelet a 90 másodperc alatt minden HTTP-kérés és az újrapróbálkozások vár legfeljebb 5 alkalommal. Ha az összes újrapróbálkozás az időkorlátot, a címeket cserélő művelet le van állítva.

- Helyi gyorsítótár inicializálása sikertelen lehet, ha az alkalmazás tartalmának meghaladja a helyi lemez kvóta megadva a helyi gyorsítótárat. További információkért lásd: [helyi gyorsítótár – áttekintés](overview-local-cache.md).

- Során [egyéni bemelegítési](#Warm-up), a HTTP-kérések, belső (áthaladás nélkül a külső URL-cím) történik. Az egyes URL-újraírási szabályok a meghiúsulhatnak *Web.config*. Például szabályok tartománynevek átirányítása vagy HTTPS kényszerítése megakadályozhatja bemelegítési kérelem eléri az alkalmazáskód. A probléma megoldásához módosítsa úgy a újraírási szabályok adja hozzá az alábbi feltételek:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Egy egyéni bemelegítési nélkül az URL-újraírási szabályok továbbra is letilthatja a HTTP-kérések. A probléma megoldásához módosítsa úgy a újraírási szabályok adja hozzá a következő feltételt:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Néhány [IP-korlátozási szabályok](app-service-ip-restrictions.md) megakadályozhatja, hogy az alkalmazás HTTP-kérelmeket küldjön a címeket cserélő művelet. IPv4-címtartományok kezdődő `10.` és `100.` az üzemelő példány belső. Meg kell hogy azok tudjanak csatlakozni az alkalmazáshoz.

## <a name="next-steps"></a>További lépések
[Nem éles pontok való hozzáférés letiltása](app-service-ip-restrictions.md)
