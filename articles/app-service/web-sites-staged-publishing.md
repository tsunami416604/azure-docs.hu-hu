---
title: Állítsa be átmeneti környezeteket az Azure App Service web Apps-alkalmazások |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja a szakaszos közzétételt a web apps az Azure App Service-ben.
services: app-service
documentationcenter: ''
author: cephalin
writer: cephalin
manager: erikre
editor: mollybos
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: cephalin
ms.openlocfilehash: 35027b4eadba72776fd27826a389763cc0832eee
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48044314"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Állítsa be átmeneti környezeteket az Azure App Service-ben
<a name="Overview"></a>

A web app, webalkalmazás, Linux, a mobil háttérrendszer és az API-alkalmazás telepítésekor [App Service-ben](http://go.microsoft.com/fwlink/?LinkId=529714), helyezhet üzembe egy külön üzembe helyezési pont helyett az alapértelmezett éles tárolóhelyre való futtatáskor a **Standard** vagy **prémium** App Service-csomag szint. Üzembe helyezési pontok valójában élő alkalmazások saját állomásnevekkel. Alkalmazás tartalmát és a konfigurációs elemek felcserélhetők két üzembe helyezési tárhely, többek között az üzemelési között. Üzembehelyezési pont, az alkalmazás üzembe helyezése a következő előnyökkel jár:

* Egy átmeneti üzembe helyezési pont az alkalmazások változásairól sikeressége az üzemelési előtt ellenőrizheti.
* Alkalmazások üzembe helyezése egy tárhely először és sikeressége az éles környezetbe biztosítja, hogy a összes példányát a tárolóhely, mielőtt éles környezetben a felcserélés folyamatban vannak bemelegíteni. Ez nem jár állásidővel, az alkalmazás központi telepítésekor. A forgalom átirányítása zökkenőmentesen, és nincsenek kérelmek eldobásakor felcserélési művelet eredményeként. A teljes munkafolyamat konfigurálásával automatizálható [automatikus felcserélés](#Auto-Swap) , ha nincs szükség a felcserélés előtti ellenőrzés.
* A korábban előkészített alkalmazással tárolóhely egy éles környezetbe való áttérés után most már rendelkezik a korábbi éles alkalmazások. Ha a módosításokat az üzemelési a felcserélés nem várt módon működik, az ugyanazon a felcserélési funkció azonnal az "utolsó ismert jó webhely" első végezhet vissza.

Minden App Service-csomag szint támogatja az üzembe helyezési pontok eltérő számú. Ismerje meg, hány tárhelyek a az alkalmazás réteg által támogatott, lásd: [App Service korlátai](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits). A célként megadott szint skálázhatja a másik tarifacsomagra, támogatnia kell az alkalmazás már használja tárolóhelyeinek száma. Például, ha az alkalmazás több mint 5 tárolóhelyei, nem skálázhatja azt le a **Standard** tier, mert **Standard** réteg csak 5 üzembe helyezési pontok támogatja.

<a name="Add"></a>

## <a name="add-a-deployment-slot"></a>Üzembehelyezési pont hozzáadása
Az alkalmazásnak futnia kell a **Standard** vagy **prémium** szint ahhoz, hogy több üzembe helyezési pontok engedélyezése.

1. Az a [az Azure Portal](https://portal.azure.com/), nyissa meg az alkalmazás [erőforráspanel](../azure-resource-manager/resource-group-portal.md#manage-resources).
2. Válassza ki a **üzembe helyezési pontok** lehetőséget, majd kattintson a **tárhely felvétele**.
   
    ![Adjon hozzá egy új üzembe helyezési pont][QGAddNewDeploymentSlot]
   
   > [!NOTE]
   > Ha az alkalmazás még nem szerepel a **Standard** vagy **prémium** szint, kapni fog egy üzenet jelzi, a támogatott szintek engedélyezésének a szakaszos közzétételt. Ezen a ponton rendelkezik-e a kívánt **frissítése** , és keresse meg a **méretezési** lapon az alkalmazás a folytatás előtt.
   > 
   > 
3. Az a **tárhely felvétele** panelen nevezze el a tárolóhely, és válassza ki, hogy egy másik meglévő üzembe helyezési pont az alkalmazáskonfigurációt klónozásához. Kattintson a pipa jelre a folytatáshoz.
   
    ![Beállítások forrása][ConfigurationSource1]
   
    Az első alkalommal tárhely felvétele, csak két lehetőség közül választhat: klónozási konfigurációs éles környezetben, vagy egyáltalán nem az alapértelmezett tárolóhelyen.
    Több tárolóhely létrehozása után lesz, nem az éles környezetben a tárolóhelyről származó konfiguráció klónozásához:
   
    ![Konfigurációs forrás][MultipleConfigurationSources]
4. Az alkalmazás erőforrás panelen kattintson **üzembe helyezési pontok**, majd kattintson az üzembehelyezési pont megnyithatja, hogy a tárhely-erőforrás paneljének, metrikákat és konfigurációs csakúgy, mint bármely más alkalmazást. A tárolóhely neve emlékezteti, hogy az üzembe helyezési pont jelenik meg a panel tetején látható.
   
    ![Üzembe helyezési pont címe][StagingTitle]
5. Kattintson az alkalmazás URL-CÍMÉT a tárolóhely panelen. Figyelje meg, hogy az üzembe helyezési pont rendelkezik a saját állomásnév, és ezzel egyúttal élő alkalmazások. Az üzembe helyezési pont való nyilvános hozzáférés korlátozása, lásd: [App Service Web App – webes elérésének letiltása nem éles rendszerek üzembe helyezési pontok](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

Nincs tartalom van üzembe helyezési tárhely létrehozása után. A tárolóhely egy másik adattárban ágat, vagy egy teljesen más tárházat telepítheti. A tárolóhely-konfiguráció is módosíthatja. A közzétételi profil vagy a központi telepítési hitelesítő adatok használata társított tartalom frissítések üzembe helyezési pont.  Például végezheti [közzététele a gittel tárolóhely](app-service-deploy-local-git.md).

<a name="AboutConfiguration"></a>

## <a name="which-settings-are-swapped"></a>Mely beállításai vannak a felcserélés?
Az egy másik üzembe helyezési pont konfigurációs klónozásakor a klónozott konfigurációs szerkeszthető. Ezenkívül bizonyos konfigurációs elemek követi a tartalmat a lapozófájl-kapacitás (nem tárolóhely adott) között, míg más konfigurációs elemek ugyanaz a tárolóhely marad a lapozófájl-kapacitás (tárolóhely adott) után. A következő listákban megjelenítése a beállításokat, ha a pontok cseréje.

**Beállításokat, amelyeket a rendszer elküldje**:

* Általános beállítások - keretrendszer verziója, 32 vagy 64 bites, például a Web sockets
* Alkalmazásbeállítások (beállítható úgy, hogy a tárhely-en)
* Kapcsolati karakterláncok (beállítható úgy, hogy a tárhely-en)
* Leírók leképezése
* Monitorozási és diagnosztikai beállítások
* WebJobs-tartalom

**Beállítások, amely nem cserélhető vannak**:

* Közzétételi végpontjai
* Egyéni tartománynevek
* SSL-tanúsítványok és -kötések
* Lépték beállításai
* Webjobs-feladatok ütemezők

Egy alkalmazás beállítás vagy kapcsolati karakterlánc elgondolkodni a tárhely (nem a felcserélés) konfigurálásához nyissa meg a **Alkalmazásbeállítások** panelen egy adott üzembe helyezési pont, majd válassza ki a **Tárhelybeállítás** be a konfiguráció érdemes elgondolkodni a bővítőhelyre elemeket. Megjelölése a konfigurációs elem tárolóhely adott hatása a megadása, hogy az elem nem cserélhető, az alkalmazáshoz társított üzembehelyezési pontok közötti.

![Tárolóhely-beállítások][SlotSettings]

<a name="Swap"></a>

## <a name="swap-deployment-slots"></a>Üzembehelyezési pontok cseréje 
Kicserélheti az üzembe helyezési pontok a **áttekintése** vagy **üzembe helyezési pontok** az app erőforráspanelen nézete.

> [!IMPORTANT]
> Mielőtt, egy alkalmazást, egy üzembe helyezési pont váltás éles üzemre, ellenőrizze, hogy az összes nem tárolóhely adott beállításai pontosan úgy, ahogyan azt szeretné, hogy a lapozófájl-kapacitás célzott.
> 
> 

1. Üzembehelyezési pontok cseréje, kattintson a **lapozófájl-kapacitás** gombra a parancssávon az alkalmazás vagy a parancssorban az üzembehelyezési pont.
   
    ![Lapozófájl-kapacitás gomb][SwapButtonBar]

2. Győződjön meg arról, hogy a lapozófájl-kapacitás forrás és cél lapozófájl-kapacitás megfelelően vannak beállítva. A felcserélés célja általában az éles webalkalmazásra. Kattintson a **OK** befejezni a műveletet. A művelet végeztével az üzembe helyezési pontok rendelkezik lett fel lett cserélve.

    ![Felcserélés befejezése](./media/web-sites-staged-publishing/SwapImmediately.png)

    Az a **felcserélés előnézettel** felcserélés típusa, lásd: [felcserélés előnézettel (többfázisú lapozó)](#Multi-Phase).  

<a name="Multi-Phase"></a>

## <a name="swap-with-preview-multi-phase-swap"></a>Felcserélés előnézettel (többfázisú lapozó)

Felcserélés előnézettel vagy többfázisú éles környezetbe való áttérés egyszerűbbé teszik a tárolóhely-specifikus konfigurációs elemeket, például kapcsolati karakterláncok érvényesítése.
Alapvető fontosságú számítási feladatokhoz, érvényesíteni szeretné, hogy az alkalmazás működését, amikor az üzemelési konfiguráció alkalmazása a vártnak, és végezze el az ilyen érvényesítési *előtt* az alkalmazás éles környezetben van felcserélni. Felcserélés előnézettel a következőkre lesz szüksége.

> [!NOTE]
> Felcserélés előnézettel nem támogatott a web apps on Linuxhoz.

Használatakor a **felcserélés előnézettel** beállítás (lásd: [üzembehelyezési pontok cseréje](#Swap)), az App Service a következőket:

- Biztosítja, hogy a céltárhely változatlan marad, hogy a tárolóhely (például az éles környezet) a meglévő számítási feladatok nem változik.
- A forrás tárolóhely, beleértve a tárolóhely-specifikus kapcsolati karakterláncok és a beállítások a céltárhely konfigurációs elemeinek vonatkozik.
- A forrás-tárolóhely a fent említett konfigurációs elemek használatával munkavégző folyamatok újraindul.
- Amikor befejezte a lapozófájl-kapacitás: a forrás előtti warmed felfelé tárolóhely helyezi át a céltárhely. A céltárhely átkerül a forrás tárolóhely, mint egy manuális lapozófájl-kapacitás.
- Ha megszakítja a lapozófájl-kapacitás: újra alkalmazza a konfigurációs elemek a forrás bővítőhely átirányítja a forrás.

Megtekintheti, hogy pontosan hogyan az alkalmazást fog viselkedni a céltárhely konfigurációval. Miután elvégezte az ellenőrzés, hogy egy külön lépésben felcserélés befejezése. Ebben a lépésben az előnye, hogy a forrás tárolóhely már bemelegíteni megfelelő kívánt beállításokkal rendelkezik, és az ügyfelek nem működik az állásidő.  

Többfázisú felcserélésre elérhető Azure PowerShell-parancsmagokkal-minták az Azure PowerShell-parancsmagok üzembe helyezési pontok szakaszban szerepelnek.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Automatikus felcserélés konfigurálása
Automatikus felcserélés leegyszerűsíti a DevOps-forgatókönyvekre, ahol szeretné folyamatosan, nulla hidegindítási, üzemkimaradás alkalmazás üzembe helyezése a végfelhasználók az alkalmazás. Ha egy üzembe helyezési pont konfigurálva van az automatikus felcserélés éles környezetben, minden egyes leküldésekor a kód frissítése, hogy a tárolóhely, App Service-ben lesz automatikusan felcserélni a az alkalmazás éles környezetbe után azt rendelkezik már bemelegíteni a céltárolóhelyen.

> [!IMPORTANT]
> Ha engedélyezi az automatikus felcserélés egy üzembe helyezési pont, győződjön meg arról, a tárhely konfigurációját pontosan szól, céltárolóhelyen megadott (általában az üzemelési) konfigurációját.
> 
> 

> [!NOTE]
> Automatikus felcserélés nem támogatott a web apps on Linuxhoz.

Automatikus felcserélés konfigurálása egy üzembe helyezési pont sem ördöngösség. Kövesse az alábbi lépéseket:

1. A **üzembe helyezési pontok**, egy nem éles rendszerek üzembe helyezési pont kiválasztása, és válassza a **Alkalmazásbeállítások** , hogy a tárhely erőforrás panelen.  
   
    ![][Autoswap1]
2. Válassza ki **a** a **automatikus felcserélés**, válassza ki a kívánt cél bővítőhely a **automatikus felcserélés tárolóhely**, és kattintson a **mentése** a parancssávon. Ellenőrizze, hogy az üzembe helyezési pont konfigurációs pontosan szánt a céloldali üzembe helyezési pont konfigurációját.
   
    A **értesítések** lapon tokenkódot egy zöld **sikeres** a művelet befejeződése után.
   
    ![][Autoswap2]
   
   > [!NOTE]
   > Automatikus felcserélés teszteléséhez az alkalmazáshoz, először válassza ki a nem éles cél bővítőhely a **automatikus felcserélés tárolóhely** áttekinteni a szolgáltatást.  
   > 
   > 
3. Hajtsa végre az adott üzembe helyezési pont kód leküldéses. Automatikus felcserélés rövid idő múlva történik, és a frissítés is megjelenik a cél a tárhely URL-címen.

<a name="Rollback"></a>

## <a name="roll-back-a-production-app-after-swap"></a>Lapozófájl-kapacitás után visszaállíthatja a éles alkalmazások
Ha hibákat azonosítják a tárolóhelycsere után éles környezetben, végezheti el a visszaállítást tárolóhelyben azok előtti felcserélés állapota azonos két tárolóhely azonnal felcserélésével.

<a name="Warm-up"></a>

## <a name="custom-warm-up-before-swap"></a>Egyéni bemelegítési előtt lapozófájl-kapacitás
Néhány alkalmazás egyéni bemelegítési műveleteket lehet szükség. A `applicationInitialization` konfigurációs elem a Web.config fájlban lehetővé teszi, hogy adja meg az egyéni inicializálási műveletek egy fogadása előtt kell elvégezni. A csereművelet végrehajtásához az egyéni bemelegítési várakozik. Íme egy minta web.config kódrészletet.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostname="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

## <a name="monitor-swap-progress"></a>A figyelő felcserélés folyamatban van

Egyes esetekben a felcserélési művelet eltarthat egy ideig tart, például ha az alkalmazást, amelyet a rendszer elküldje egy hosszú bemelegítési idő tartozik-e. További információt a lapozófájl-kapacitás műveletének a [tevékenységnapló](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) a a [az Azure portal](https://portal.azure.com).

Az alkalmazás oldalán a portál bal oldali navigációs sávján válassza **tevékenységnapló**.

Csereművelet jelenik meg a napló-lekérdezéshez, mivel `Slotsswap`. Bontsa ki azt, és válassza ki az egyik suboperations vagy hibákat a részletek megtekintéséhez.

![A tárolóhelycsere tevékenységnapló](media/web-sites-staged-publishing/activity-log.png)

<a name="Delete"></a>

## <a name="delete-a-deployment-slot"></a>Üzembehelyezési pont törlése
Üzembehelyezési pont panelen nyissa meg az üzembe helyezési pont panelen, kattintson **áttekintése** (az alapértelmezett oldal), és kattintson a **törlése** a parancssávon.  

![Üzembehelyezési pont törlése][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-azure-powershell"></a>Automatizálhatja az Azure PowerShell használatával

Az Azure PowerShell modul, amely Azure-t a Windows PowerShell-lel, beleértve az Azure App Service-ben üzembe helyezési pontok felügyeletének támogatását, így kezelheti-parancsmagokat kínál.

* Információk az telepítése és konfigurálása az Azure PowerShell-lel és az Azure PowerShell-lel hitelesítése az Azure-előfizetésében: [telepítése és konfigurálása a Microsoft Azure PowerShell](/powershell/azure/overview).  

- - -
### <a name="create-a-web-app"></a>Webalkalmazás létrehozása
```PowerShell
New-AzureRmWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

- - -
### <a name="create-a-deployment-slot"></a>Üzembe helyezési tárhely létrehozása
```PowerShell
New-AzureRmWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

- - -
### <a name="initiate-a-swap-with-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-source-slot"></a>Kezdeményezzen egy felcserélés előnézettel (többfázisú lapozó), és a alkalmazni cél tárolóhely konfigurációs forrás
```PowerShell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-source-slot-configuration"></a>Megszakítja a függőben lévő felcserélés (tekintse át a lapozó) és a forrás tárolóhely konfiguráció visszaállítása
```PowerShell
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

- - -
### <a name="swap-deployment-slots"></a>Üzembehelyezési pontok cseréje
```PowerShell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Lapozófájl-kapacitás-események figyelése a tevékenységnaplóban
```PowerShell
Get-AzureRmLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

- - -
### <a name="delete-deployment-slot"></a>Üzembe helyezési pont törlése
```
Remove-AzureRmResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

- - -
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-azure-cli"></a>Automatizálhatja az Azure CLI-vel

A [Azure CLI-vel](https://github.com/Azure/azure-cli) parancsok a üzembe helyezési pontok, lásd: [az webapp deployment slot](/cli/azure/webapp/deployment/slot).

## <a name="next-steps"></a>További lépések
[Az Azure App Service Web App – webes elérésének letiltása nem éles rendszerek üzembe helyezési pontok](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)  
[A linuxon futó App Service bemutatása](../app-service/containers/app-service-linux-intro.md)  
[A Microsoft Azure ingyenes próbaverziója](https://azure.microsoft.com/pricing/free-trial/)

<!-- IMAGES -->
[QGAddNewDeploymentSlot]:  ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]:  ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png
[Autoswap1]: ./media/web-sites-staged-publishing/AutoSwap01.png
[Autoswap2]: ./media/web-sites-staged-publishing/AutoSwap02.png
[SlotSettings]: ./media/web-sites-staged-publishing/SlotSetting.png

