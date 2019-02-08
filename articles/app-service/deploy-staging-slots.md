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
ms.date: 01/03/2019
ms.author: cephalin
ms.openlocfilehash: 2a5ff771064c860447427c9e3cc1345a03a69634
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55893754"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Állítsa be átmeneti környezeteket az Azure App Service-ben
<a name="Overview"></a>

> [!NOTE]
> Ez az útmutató bemutatja, hogyan kezelheti a bővítőhelyek új előzetes kezelése lap használatával. A meglévő felügyeleti lap segítségével ügyfeleink továbbra is a meglévő tárhely kezelése lap, mielőtt használja. 
>

A web app, webalkalmazás, Linux, a mobil háttérrendszer és az API-alkalmazás telepítésekor [App Service-ben](https://go.microsoft.com/fwlink/?LinkId=529714), helyezhet üzembe egy külön üzembe helyezési pont helyett az alapértelmezett éles tárolóhelyre való futtatáskor a **Standard**, **Prémium**, vagy **elkülönített** App Service-csomag szint. Üzembe helyezési pontok valójában élő alkalmazások saját állomásnevekkel. Alkalmazás tartalmát és a konfigurációs elemek felcserélhetők két üzembe helyezési tárhely, többek között az üzemelési között. Egy nem éles tárhely, az alkalmazás üzembe helyezése a következő előnyökkel jár:

* Egy átmeneti üzembe helyezési pont az alkalmazások változásairól sikeressége az üzemelési előtt ellenőrizheti.
* Alkalmazások üzembe helyezése egy tárhely először és sikeressége az éles környezetbe biztosítja, hogy, hogy a összes példányát a tárolóhely, mielőtt éles környezetben a felcserélés folyamatban vannak bemelegíteni. Ez nem jár állásidővel, az alkalmazás központi telepítésekor. A forgalom átirányítása zökkenőmentesen, és nincsenek kérelmek eldobásakor felcserélési művelet miatt. A teljes munkafolyamat konfigurálásával automatizálható [automatikus felcserélés](#Auto-Swap) amikor előtti felcserélés érvényesítési nincs szükség.
* A korábban előkészített alkalmazással tárolóhely egy éles környezetbe való áttérés után most már rendelkezik a korábbi éles alkalmazások. Ha a módosításokat az üzemelési a felcserélés nem a várt módon, az ugyanazon a felcserélési funkció azonnal az "utolsó ismert jó webhely" első végezheti vissza.

Minden App Service-csomag szint támogatja az üzembe helyezési pontok eltérő számú. Ismerje meg, hány tárhelyek a az alkalmazás réteg által támogatott, lásd: [App Service korlátai](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits). A célként megadott szint skálázhatja a másik tarifacsomagra, támogatnia kell az alkalmazás már használja tárolóhelyeinek száma. Például, ha az alkalmazás több mint öt tárolóhelyei, nem skálázhatja azt le a **Standard** tier, mert **Standard** szint csak az öt üzembe helyezési pontok támogatja.

<a name="Add"></a>

## <a name="add-slot"></a>Tárhely felvétele
Az alkalmazásnak futnia kell a **Standard**, **prémium szintű**, vagy **elkülönített** szint ahhoz, hogy több üzembe helyezési pontok engedélyezése.

1. Az a [az Azure portal](https://portal.azure.com/), nyissa meg az alkalmazás [erőforráslapján](../azure-resource-manager/resource-group-portal.md#manage-resources).

2. A bal oldali navigációs sávján válassza ki a **üzembe helyezési pontok (előzetes verzió)** lehetőséget, majd kattintson a **tárhely felvétele**.
   
    ![Adjon hozzá egy új üzembe helyezési pont](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Ha az alkalmazás már nem az a **Standard**, **prémium szintű**, vagy **elkülönített** szint, megjelenik a támogatott szintek engedélyezésének a szakaszos közzétételt jelző üzenet. Ezen a ponton rendelkezik-e a kívánt **frissítése** , és keresse meg a **méretezési** lapon az alkalmazás a folytatás előtt.
   > 

3. Az a **tárhely felvétele** párbeszédpanel, nevezze el a tárolóhely, és válassza ki, hogy egy másik meglévő üzembe helyezési pont az alkalmazáskonfigurációt klónozásához. Kattintson a **Hozzáadás** folytatásához.
   
    ![Beállítások forrása](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    Minden olyan meglévő tárolóhelyről származó konfiguráció klónozhat. Beállítások, amely képes lehet klónozni Alkalmazásbeállítások, kapcsolati karakterláncok, nyelvi keretrendszer-verziókat, web sockets, HTTP-verzió és platform bitszámértékének tartalmazza.

4. A tárolóhely hozzáadása után kattintson a **bezárásához** a párbeszédpanel bezárásához. Most már megjelenik az új tárhely a **üzembe helyezési pontok (előzetes verzió)** lapot. Alapértelmezés szerint a **forgalom %** az üzemelési minden ügyfél forgalom az új üzembe helyezési pont 0 értékre van állítva.

5. Kattintson az új üzembe helyezési pont, hogy a tárolóhely-erőforrások oldalának megnyitásához.
   
    ![Üzembe helyezési pont címe](./media/web-sites-staged-publishing/StagingTitle.png)

    Az előkészítési pont is rendelkezik felügyeleti csakúgy, mint bármely más App Service-alkalmazás. A helyezési pont konfigurációját módosíthatja. A tárolóhely neve látható az oldal tetején lévő emlékezteti, hogy az üzembe helyezési pont látja.

6. Kattintson az alkalmazás a tárhely-erőforrások oldalának URL-címet. Az üzembe helyezési pont rendelkezik a saját állomásnév és is élő alkalmazások. Az üzembe helyezési pont való nyilvános hozzáférés korlátozása, lásd: [Azure App Service IP-korlátozások](app-service-ip-restrictions.md).

Az új üzembe helyezési pont nem tartalmaz, akkor is, ha a beállításokat egy másik tárolóhelyről klónozását. Például végezheti [közzététele a gittel tárolóhely](app-service-deploy-local-git.md). A tárolóhely egy másik adattárban ágat vagy egy másik adattárban telepítheti. 

<a name="AboutConfiguration"></a>

## <a name="which-settings-are-swapped"></a>Mely beállításai vannak a felcserélés?
Az egy másik üzembe helyezési pont konfigurációs klónozásakor a klónozott konfigurációs szerkeszthető. Továbbá bizonyos konfigurációs elemek kövesse a tartalom a lapozófájl-kapacitás (nem tárolóhely adott) között, míg más konfigurációs elemek ugyanaz a tárolóhely maradni a lapozófájl-kapacitás (tárolóhely adott) után. A következő listákban megjelenítése a beállításokat, ha a pontok cseréje.

**Beállításokat, amelyeket a rendszer elküldje**:

* Általános beállítások - keretrendszer verziója, 32 vagy 64 bites, például a Web sockets
* Alkalmazásbeállítások (beállítható úgy, hogy a tárhely-en)
* Kapcsolati karakterláncok (beállítható úgy, hogy a tárhely-en)
* Leírók leképezése
* Monitorozási és diagnosztikai beállítások
* A nyilvános tanúsítványok
* WebJobs-tartalom
* Hibrid kapcsolatok

**Beállítások, amely nem cserélhető**:

* Közzétételi végpontjai
* Egyéni tartománynevek
* Privát tanúsítványok és az SSL-kötések
* Lépték beállításai
* Webjobs-feladatok ütemezők

<!-- VNET, IP restrictions, CORS, hybrid connections? -->

Adja meg egy alkalmazás beállítás vagy kapcsolati karakterlánc (nem a felcserélés) egy adott tárhely anyagot, lépjen a **nastavení Aplikace** , üzembe helyezési pont lapon, majd válassza ki a **Tárhelybeállítás** jelölőnégyzetet a érdemes elgondolkodni a bővítőhelyre konfigurációs elemeket. Megjelölése a konfigurációs elem tárolóhely adott utasítja az App Service, hogy azt ne legyen cserélhető.

![Tárhelybeállítás](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Két pontok cseréje 
Az alkalmazás kicserélheti az üzembe helyezési pontok **üzembe helyezési pontok (előzetes verzió)** lapot. 

Válthat is a tárolóhelyek a **áttekintése** és **üzembe helyezési pontok** lapokat, de jelenleg biztosít a régi felületet. Ez az útmutató megmutatja az új felhasználói felületének használata a **üzembe helyezési pontok (előzetes verzió)** lapot.

> [!IMPORTANT]
> Előtt, egy alkalmazást, egy üzembe helyezési pont váltás éles üzemre, ellenőrizze, hogy minden beállítás, hogy a lapozófájl-kapacitás célzott kívánt vannak-e konfigurálva.
> 
> 

Üzembehelyezési pontok cseréje, kövesse az alábbi lépéseket:

1. Keresse meg az alkalmazás **üzembe helyezési pontok (előzetes verzió)** lapot, és kattintson **felcserélése**.
   
    ![Lapozófájl-kapacitás gomb](./media/web-sites-staged-publishing/SwapButtonBar.png)

    A **felcserélése** párbeszédpanelen a kijelölt forrás- és a céltárolóhelyen módosítani a beállításokat ismerteti.

2. Válassza ki a kívánt **forrás** és **cél** tárolóhelyek. A cél általában az éles webalkalmazásra. Is, kattintson a **az adatforrás-módosításokkal** és **cél módosítások** követését, és győződjön meg arról, hogy a konfigurációs módosítások várható. Amikor végzett, kicserélheti tárolóhelyben azonnal kattintva **lapozófájl-kapacitás**.

    ![Felcserélés befejezése](./media/web-sites-staged-publishing/SwapImmediately.png)

    A céltárolóhelyen megadott lenne futtatásának módját az új beállításokkal előtt a felcserélés ténylegesen történik, hogy ne kattintson **lapozófájl-kapacitás**, de kövesse a [felcserélés előnézettel](#Multi-Phase).

3. Amikor végzett, zárja be a párbeszédpanelt kattintva **bezárásához**.

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Felcserélés előnézettel (többfázisú lapozó)

> [!NOTE]
> Felcserélés előnézettel a Linuxos web apps nem támogatott.

Előtt érvényesítheti a céltárolóhelyen megadott helyezheti, ellenőrizze az alkalmazás fut, a átváltva beállításokkal előtt történik, a lapozófájl-kapacitás. Az adatforrás-tárolóhelyen is bemelegíteni, amely üzleti szempontból alapvető fontosságú alkalmazások esetében is kívánatos felcserélés befejezése előtt.

A felcserélés előnézettel hajt végre, amikor az App Service a következőket teszi a a felcserélés indítása:

- Változatlan marad, így nem vonatkozik a meglévő számítási feladatot, hogy a tárolóhely (például az éles környezet) a céltárolóhelyen megadott megőrzi.
- A forrás tárolóhely, beleértve a tárolóhely-specifikus kapcsolati karakterláncok és a beállítások a céltárolóhelyen megadott konfigurációs elemeinek vonatkozik.
- Az ilyen konfigurációs elemek segítségével forrás tárolóhelyen munkavégző folyamatok újraindul. Keresse meg a forrás tárolóhely, és futtassa a konfigurációs módosítások az alkalmazás megtekintéséhez.

Ha végrehajtani a felcserélés egy külön lépésben, az App Service helyezi át a felmelegedett forrás bővítőhely a céltárolóhelyen megadott be, és a céltárolóhelyen megadott a forrás-tárolóhely. Ha megszakítja a felcserélés, App Service-ben átirányítja a forrás újra alkalmazza a forrás bővítőhely a konfigurációs elemeket.

Felcserélés előnézettel, kövesse az alábbi lépéseket.

1. Kövesse a [üzembehelyezési pontok cseréje](#Swap) de válassza **végrehajtása felcserélés előnézettel**.

    ![Felcserélés előnézettel](./media/web-sites-staged-publishing/SwapWithPreview.png)

    A párbeszédpanel bemutatja, hogyan 1. fázis a forrás-tárolóhely a konfiguráció módosításait, és hogyan a forrás- és tárolóhely módosítsa a 2. fázis.

2. Ha készen áll a felcserélés indítása, kattintson a **felcserélés indítása**.

    1. fázis befejezése után értesítést kap arról a párbeszédpanelen. Előzetes verzió a lapozófájl-kapacitás, a forrás adatszalagot az `https://<app_name>-<source-slot-name>.azurewebsites.net`. 

3. Amikor készen áll a függőben lévő felcserélés befejezése, válassza ki a **felcserélés befejezése** a **felcserélési művelet** kattintson **felcserélés befejezése**.

    Egy függőben lévő felcserélés érvénytelenítése, jelölje be **Mégse lapozófájl-kapacitás** Ehelyett kattintson **Mégse lapozófájl-kapacitás**.

4. Amikor végzett, zárja be a párbeszédpanelt kattintva **bezárásához**.

A többfázisú felcserélés automatizálása, tekintse meg automatizálása a PowerShell-lel.

<a name="Rollback"></a>

## <a name="roll-back-swap"></a>Állítsa vissza a lapozófájl-kapacitás
Bármilyen hiba után egy tárolóhelycsere céltárolóhelyen megadott (például az üzemelési) történik, ha vissza a tárolóhelyek felcserélés előtti állapotába pontjai az azonos két azonnal.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Automatikus felcserélés konfigurálása

> [!NOTE]
> Automatikus felcserélés nem támogatja a web apps on Linuxhoz.

Automatikus felcserélés leegyszerűsíti a DevOps-forgatókönyvekre, ahol szeretné a végfelhasználókat az alkalmazás folyamatosan, a nulla hidegindítási, üzemkimaradás alkalmazás üzembe helyezése. Ha éles környezetben, minden egyes leküldésekor a kód egy tárolóhely autoswaps változnak, hogy a tárolóhely, az App Service automatikusan felcserélése az alkalmazás éles környezetbe után folyékonnyá a forrás-tárolóhely.

   > [!NOTE]
   > Mielőtt konfigurálná a automatikus felcserélés az éles üzembe helyezési pont, fontolja meg tesztelési automatikus felcserélés célja nem éles tárhely a először.
   > 

Automatikus felcserélés konfigurálásához kövesse az alábbi lépéseket:

1. Keresse meg az alkalmazás erőforrás-lapon. Válassza ki **üzembe helyezési pontok (előzetes verzió)** > *\<kívánt forrás >* > **Alkalmazásbeállítások**.
   
2. A **automatikus felcserélés**, jelölje be **a**, válassza ki a kívánt cél bővítőhely a **automatikus felcserélés tárolóhely**, és kattintson a **mentése** a parancssávon. 
   
    ![](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Hajtsa végre egy kódot leküldéses átirányítja a forrás. Automatikus felcserélés rövid idő múlva történik, és a frissítés is megjelenik a cél a tárhely URL-címen.

<a name="Warm-up"></a>

## <a name="custom-warm-up"></a>Egyéni bemelegítési
Használata esetén [Auto-Swap](#Auto-Swap), bizonyos alkalmazásokhoz szükség lehet egyéni bemelegítési műveletek a felcserélés előtt. A `applicationInitialization` konfigurációs elem a Web.config fájlban lehetővé teszi, hogy adhat meg egyéni inicializálási műveletek kell elvégezni. A felcserélési művelet befejeződését, mielőtt a célként megadott tárhellyel való felcserélése ezen egyéni bemelegítési várakozik. Íme egy minta web.config kódrészletet.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

## <a name="monitor-swap"></a>A figyelő swap

Ha a címeket cserélő művelet egy hosszú ideig tart, a a felcserélési művelet információkat szerezhet a [tevékenységnapló](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

Az erőforrás-oldalon az alkalmazás a portálon a bal oldali navigációs sávján válassza **tevékenységnapló**.

Csereművelet jelenik meg a napló-lekérdezéshez, mivel `Swap Web App Slots`. Bontsa ki azt, és válassza ki az egyik suboperations vagy hibákat a részletek megtekintéséhez.

## <a name="route-traffic"></a>Forgalom irányítása

Alapértelmezésben a kéréseket az alkalmazás éles URL-címe (`http://<app_name>.azurewebsites.net`) legyenek átirányítva az éles webalkalmazásra. A forgalom egy része azt egy másik tárolóhelyre irányíthatja. Ez a funkció akkor hasznos, ha a felhasználói visszajelzések szüksége lesz egy új frissítést, de Ön nem készen áll az éles környezetbe feloldásához.

### <a name="route-production-traffic-automatically"></a>Automatikusan éles forgalom irányítása

Éles forgalom irányítására automatikusan, kövesse az alábbi lépéseket:

1. Az alkalmazás erőforrás-oldalon keresse meg és válassza **üzembe helyezési pontok (előzetes verzió)**.

2. Az a **forgalom %** oszlopa a tárolóhely kívánt átirányítása, adja meg (0 és 100) közötti százalékos képviselő szeretné átirányítani teljes forgalom mennyisége. Kattintson a **Save** (Mentés) gombra.

    ![](./media/web-sites-staged-publishing/RouteTraffic.png)

A beállítás a mentés után a megadott százalék, az ügyfelek véletlenszerűen irányítja a rendszer a nem éles tárhely. 

Miután az ügyfél automatikusan annak biztosítására, hogy egy adott tárhely annak "rögzített", hogy a tárolóhely esetében az élettartam adott ügyfél-munkamenet. Az ügyfélböngészőnek, mely a munkamenet rögzítve van naplófájlbejegyzéseket átnézve tárolóhely láthatja a `x-ms-routing-name` cookie-t a HTTP-fejléceket. Egy kérelmet, amely a "átmeneti" tárolóhely irányítja a rendszer rendelkezik a cookie-k `x-ms-routing-name=staging`. Egy kérelmet, amely az éles tárolóhelyre lesz irányítva a cookie-k rendelkezik `x-ms-routing-name=self`.

### <a name="route-production-traffic-manually"></a>Manuálisan éles forgalom irányítása

Mellett automatikus forgalom-útválasztást, App Service-ben is irányíthatja a kérelmeket egy adott tárhely. Ez akkor hasznos, ha azt szeretné, hogy a felhasználók tudni opt be vagy lemondja a bétaverzió alkalmazását. Éles forgalom irányítására manuálisan, használja a `x-ms-routing-name` lekérdezési paraméter.

Ahhoz, hogy a felhasználók tilthatják le a béta-alkalmazás, például helyezheti erre a hivatkozásra a webalkalmazás lapjának:

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

A karakterlánc `x-ms-routing-name=self` adja meg az éles webalkalmazásra. Miután az ügyfél böngészője fér hozzá a hivatkozás, nem csak azt átirányítódik az éles tárolóhelyre, de minden későbbi kérés a `x-ms-routing-name=self` cookie-t, amely rögzíti a munkamenet az éles webalkalmazásra.

Ahhoz, hogy a felhasználók engedélyezve a béta-alkalmazását, állítsa be ugyanazon lekérdezési paraméter a nem éles tárolóhely neve:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

<a name="Delete"></a>

## <a name="delete-slot"></a>Pont törlése

Keresse meg az alkalmazás erőforrás-lapon. Válassza ki **üzembe helyezési pontok (előzetes verzió)** > *\<törlése tárolóhely >* > **áttekintése**. Kattintson a **törlése** a parancssávon.  

![Üzembehelyezési pont törlése](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatizálás a PowerShell használatával

Az Azure PowerShell modul, amely Azure-t a Windows PowerShell-lel, beleértve az Azure App Service-ben üzembe helyezési pontok felügyeletének támogatását, így kezelheti-parancsmagokat kínál.

Információk az telepítése és konfigurálása az Azure PowerShell-lel és az Azure PowerShell-lel hitelesítése az Azure-előfizetésében: [telepítése és konfigurálása a Microsoft Azure PowerShell](/powershell/azure/overview).  

- - -
### <a name="create-web-app"></a>Webalkalmazás létrehozása
```PowerShell
New-AzureRmWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

- - -
### <a name="create-slot"></a>Tárhely létrehozása
```PowerShell
New-AzureRmWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

- - -
### <a name="initiate-swap-with-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-source-slot"></a>Felcserélés előnézettel (többfázisú lapozó) kezdeményezi, és a forrás célhelyre tárolóhely konfigurációjának alkalmazása
```PowerShell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="cancel-pending-swap-swap-with-review-and-restore-source-slot-configuration"></a>Folyamatban lévő felcserélés, (tekintse át a lapozó) és a forrás tárolóhely konfiguráció visszaállítása
```PowerShell
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

- - -
### <a name="swap-deployment-slots"></a>Üzembehelyezési pontok cseréje
```PowerShell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>A tevékenység Log lapozófájl-kapacitás-események figyelése
```PowerShell
Get-AzureRmLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

- - -
### <a name="delete-slot"></a>Pont törlése
```PowerShell
Remove-AzureRmResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

- - -
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-cli"></a>Automatizálás a parancssori felület

A [Azure CLI-vel](https://github.com/Azure/azure-cli) parancsok a üzembe helyezési pontok, lásd: [az webapp deployment slot](/cli/azure/webapp/deployment/slot).

## <a name="next-steps"></a>További lépések
[Nem éles pontok való hozzáférés letiltása](app-service-ip-restrictions.md)
