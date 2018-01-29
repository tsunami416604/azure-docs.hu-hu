---
title: "Átmeneti környezet az Azure App Service web Apps beállítása |} Microsoft Docs"
description: "Ismerje meg, előkészített közzététele az Azure App Service web Apps használatával."
services: app-service
documentationcenter: 
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
ms.openlocfilehash: 55c023e8f6b41c17e85ba441f862a7682b2f2599
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Átmeneti környezet az Azure App Service beállítása
<a name="Overview"></a>

A webalkalmazás, webes alkalmazás a Linux, mobil háttér és API-alkalmazás telepítésekor [App Service](http://go.microsoft.com/fwlink/?LinkId=529714), telepíthet egy külön üzembe helyezési pont helyett az alapértelmezett éles tárolóhelyre futtatáskor a **szabványos** vagy **prémium** App Service-csomag mód. Üzembe helyezési saját állomásnevek ténylegesen élő alkalmazások. Alkalmazás tartalmát és konfigurációk elemek lecserélhető között két üzembe helyezési, beleértve az éles webalkalmazásra. A telepített környezet tárolóhelye az alkalmazás telepítése az alábbi előnyökkel jár:

* Mielőtt az éles webalkalmazásra a csere, ellenőrizheti egy átmeneti üzembe helyezési tárhelyet az alkalmazások változásairól.
* Központilag telepíthetők az alkalmazások a tárhely először és csere az éles környezetben biztosítja, hogy a összes példányát a tárolóhely Mielőtt éles környezetben felcserélés folyamatban vannak tárolóhelyspecifikus. Ez megszünteti állásidő, az alkalmazás központi telepítésekor. A forgalom átirányítása zökkenőmentes-kérelmek nem dobja swap műveletek miatt. A teljes munkafolyamat konfigurálásával automatizálható [automatikus felcserélés](#Auto-Swap) , ha nincs szükség a előtti swap érvényesítése.
* Egy felcserélés után a tárolóhely korábban előkészített alkalmazással most már rendelkezik az előző éles alkalmazások. Ha a módosításokat, azokat az éles tárolóhelyre felcserélve nem a várt módon, közvetlenül a "utolsó ismert helyes"nevű hely eléréséhez a azonos virtuális végezhet vissza.

Minden App Service-csomag mód támogatja az üzembe helyezési pontok különböző számú. Tárolóhely száma megállapítása: az alkalmazás mód támogatja, lásd: [App Service szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/app-service/).

* Ha az alkalmazás több tárolóhelye van, a mód nem módosítható.
* Skálázás nem érhető el a nem végleges pont felcserélése.
* Csatolt erőforrás-kezelés nem végleges pont felcserélése nem támogatott. Az a [Azure Portal](http://go.microsoft.com/fwlink/?LinkId=529715) csak, úgy kerülheti el a lehetséges hatásairól az éles tárhely ideiglenesen a nem éles tárolóhelyre áthelyezése egy másik App Service csomag módot. Vegye figyelembe, hogy az nem éles tárhely kell ismét közös azonos módot az éles tárolóhelyre ahhoz, hogy a két felcserélése is.

<a name="Add"></a>

## <a name="add-a-deployment-slot"></a>Adja hozzá egy üzembe helyezési tárhelyet
Futnia kell az alkalmazást a **szabványos** vagy **prémium** ahhoz, hogy engedélyezheti a több üzembe helyezési mód.

1. Az a [Azure Portal](https://portal.azure.com/), nyissa meg az alkalmazás [erőforráspanelen](../azure-resource-manager/resource-group-portal.md#manage-resources).
2. Válassza ki a **üzembe helyezési** lehetőséget, majd kattintson az **tárhely felvétele**.
   
    ![Adja hozzá egy új üzembe helyezési tárhelyet][QGAddNewDeploymentSlot]
   
   > [!NOTE]
   > Ha az alkalmazás már nem része a **szabványos** vagy **prémium** mód, kapni fog egy üzenetet, a támogatott módok előkészített közzététel engedélyezéséhez. Ezen a ponton rendelkezik választhatja **frissítése** , és keresse meg a **méretezési** fülre az alkalmazás a folytatás előtt.
   > 
   > 
3. Az a **tárhely hozzáadása** panelen nevezze el a tárhely, és válassza ki, hogy egy másik meglévő üzembe helyezési pont az alkalmazáskonfiguráció klónozását. Kattintson a pipa jelre a folytatáshoz.
   
    ![Beállítások forrása][ConfigurationSource1]
   
    Először ad hozzá egy tárolóhely, csak két lehetősége van: az alapértelmezett tárolóhelyről, éles környezetben, vagy egyáltalán nem klónozott konfigurációs.
    A létrehozást követően több üzembe helyezési ponti, lesz klónozza a nem az éles tárhely beállításait:
   
    ![Konfigurációs forrás][MultipleConfigurationSources]
4. Az alkalmazás erőforrás paneljén kattintson **üzembe helyezési**, majd kattintson az erőforráspanelről, hogy a tárhely, metrikákat és csakúgy, mint bármely más alkalmazás-konfigurációs vannak beállítva egy üzembe helyezési tárhelyet. A tárhely neve jelezve, az üzembe helyezési pont megjelenítő a panel tetején látható.
   
    ![Központi telepítés tárolóhely cím][StagingTitle]
5. Kattintson az alkalmazás URL-CÍMÉT a tárolóhely panelen. Figyelje meg a telepített környezet tárolóhelye saját állomásnév és is egy élő app. Az üzembe helyezési pont nyilvános hozzáférés korlátozására, lásd: [App Service Web App – webes hozzáférés letiltása nem éles üzembe helyezési](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

Nincs tartalom központi telepítési tárolóhely létrehozása után. A tárolóhely telepítene egy másik tárház ágat, vagy egy teljesen más tárházba. A tárolóhely konfigurációs is módosíthatja. A közzétételi profil vagy a központi telepítési társított hitelesítő adatokat a telepített környezet tárolóhelye tartalom frissítések használja.  Például végezheti [ezt a tárolóhelyet a git közzététele](app-service-deploy-local-git.md).

<a name="AboutConfiguration"></a>

## <a name="configuration-for-deployment-slots"></a>Az üzembe helyezési konfiguráció
Klónozza a másik üzembe helyezési pont konfigurációja, a klónozott konfigurációs esetén szerkeszthető. Továbbá bizonyos konfigurációs elemek követi a tartalmat a lapozófájl-kapacitás (nem tárolóhely adott) keresztül közben más konfigurációs elemek ugyanaz a tárolóhely marad a lapozófájl-kapacitás (tárolóhely adott) után. A következő listákban konfiguráció szerint változnak, ha Ön felcserélése megjelenítése.

**Beállítások, amelyek van cserélve**:

* Általános beállítások - keretrendszer verziója, 32 vagy 64 bites, például webes szoftvercsatornák
* Alkalmazásbeállítások (beállítható úgy, hogy a tárhely anyagot)
* Kapcsolati karakterláncok (beállítható úgy, hogy a tárhely anyagot)
* Leírók leképezése
* Megfigyelési és diagnosztikai beállítások
* Webjobs-feladatok tartalom

**Beállítások, amelyek nem van cserélve**:

* Közzétételi végpontok
* Egyéni tartománynevek
* SSL-tanúsítványok és kötések
* Skálázási beállításokat
* Webjobs-feladatok bejegyzéstípusait

Egy alkalmazás beállítás vagy a kapcsolati karakterlánc (nem cserélhető fel) tárhely anyagot konfigurálása, hozzáférés-a **Alkalmazásbeállítások** paneljén adott tárhely, majd válassza ki a **tárolóhely beállítás** mezőben a konfiguráció a tárolóhely kell odatapadjon elemei. Vegye figyelembe, hogy jelölés egy konfigurációs elem, ha adott tárolóhely létrehozásáról szóló az elem nem cserélhető között az alkalmazáshoz kapcsolódó összes üzembe helyezési hatását.

![Tárolóhely-beállítások][SlotSettings]

<a name="Swap"></a>

## <a name="swap-deployment-slots"></a>Központi telepítés felcserélése 
Az üzembe helyezési kicserélheti a **áttekintése** vagy **üzembe helyezési** a app erőforráspanelen ábrázolása.

> [!IMPORTANT]
> Mielőtt egy üzembe helyezési pont alkalmazás felcserélni a éles környezetben, győződjön meg arról, hogy minden adott nem tárolóhely-beállítások kívánt felcserélés célja, hogy vannak-e konfigurálva.
> 
> 

1. Központi telepítés felcserélése, kattintson a **Swap** gombra a parancssávon az alkalmazás vagy a telepített környezet tárolóhelye parancsra a parancssávon.
   
    ![Lapozófájl-kapacitás gomb][SwapButtonBar]

2. Győződjön meg arról, hogy a lapozófájl-kapacitás forrás- és a lapozófájl-kapacitás cél megfelelően vannak beállítva. A felcserélés célja általában az éles webalkalmazásra. Kattintson a **OK** elvégezni a műveletet. A művelet befejezése után az üzembe helyezési rendelkezik lett cserélve.

    ![Felcserélés befejezése](./media/web-sites-staged-publishing/SwapImmediately.png)

    Az a **felcserélés előnézettel** típus felcserélése, lásd: [felcserélés előnézettel (több fázisban swap)](#Multi-Phase).  

<a name="Multi-Phase"></a>

## <a name="swap-with-preview-multi-phase-swap"></a>A felcserélés előnézettel (több fázisban swap)

A felcserélés előnézettel, vagy több fázisban swap leegyszerűsíti a tárolóhely konfigurációs elemeket, például kapcsolati karakterláncok érvényesítése.
A kritikus fontosságú munkaterhelésekhez, érvényesíteni szeretné, hogy az alkalmazás az éles tárolóhelyre konfiguráció alkalmazása várt viselkedik, és végezze el az ilyen érvényesítési *előtt* az alkalmazást éles környezetben van cserélve. Felcserélés előnézettel az alábbiakra lesz szüksége.

> [!NOTE]
> A felcserélés előnézettel a webalkalmazásokban Linux rendszeren nem támogatott.

Használatakor a **a felcserélés előnézettel** beállítás (lásd: [telepítési felcserélése](#Swap)), az App Service a következőket teszi:

- Megtartja a céltárolóhelyre változatlan marad, így nem érinti a meglévő terhelése, hogy a bővítőhely (pl. éles környezet).
- A forrás tárolási helyre, beleértve a tárolóhely-specifikus kapcsolati karakterláncok és Alkalmazásbeállítások céltárolóhelyen konfigurációs elemeinek vonatkozik.
- A forrás tárolási helyre, a fent említett konfigurációs elemeket a munkavégző folyamatok újraindul.
- Amikor befejezte a lapozófájl-kapacitás: a warmed létrehozása előtti forrás tárolási helyre helyezi a hangsúlyt a céltárolóhelyre. A rendeltetési tárolási helyre helyezi át a forrás tárolási helyre, mint egy manuális felcserélés.
- Ha megszakítja a lapozófájl-kapacitás: újra alkalmazza a forrás tárolási helyre konfigurációs elemet a forrás tárolási helyre.

Megtekintheti, pontosan hogyan az alkalmazás működik a céltárolóhelyre konfigurációjával kapcsolatban. Ellenőrzés befejezése után végezze el a lapozófájl-kapacitás egy külön lépésben. Ez a lépés előnye, hogy a forrás tárolási helyre már tárolóhelyspecifikus a kívánt konfigurációval rendelkezik, és az ügyfelek nem tapasztalnak állásidőt fog tapasztalni.  

Több fázisban swap érhető el az Azure PowerShell-parancsmagok-példák az Azure PowerShell-parancsmagok telepítési üzembe helyezési ponti szakaszban szerepelnek.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Az automatikus felcserélés konfigurálása
Az automatikus felcserélés leegyszerűsíti a DevOps helyzetekben, ahol szeretné folyamatosan alkalmazás telepítése az állásidő és nulla hidegindítás a végfelhasználók az alkalmazás. Ha egy üzembe helyezési pont van konfigurálva az automatikus felcserélés éles környezetben, minden alkalommal, amikor a kód frissítése leküldése a tárolóhely, App Service lesz automatikusan felcserélése az alkalmazás éles környezetben után azt rendelkezik már tárolóhelyspecifikus tárolóhelye.

> [!IMPORTANT]
> Ha engedélyezi az automatikus felcserélés a tárhely, ellenőrizze, hogy a tárhely konfigurációs pontosan a cél tárolóhely (általában az éles tárolóhelyre) készült konfigurációs.
> 
> 

> [!NOTE]
> Az automatikus felcserélés a webalkalmazásokban Linux rendszeren nem támogatott.

Az automatikus felcserélés konfigurálása tárhely is könnyen. Kövesse az alábbi lépéseket:

1. A **üzembe helyezési**, válassza ki, nem éles tárhely és kiválasztható **Alkalmazásbeállítások** , hogy a tárhely erőforrás panelén.  
   
    ![][Autoswap1]
2. Válassza ki **a** a **automatikus felcserélés**, válassza ki a kívánt cél bővítőhely a **automatikus felcserélés tárolóhely**, és kattintson a **mentése** parancsra a parancssávon. Ellenőrizze, hogy a tárhely pontosan a cél tárolóhely szánt konfigurációját.
   
    A **értesítések** lapon villogjon, egy zöld **sikeres** a művelet végrehajtása után.
   
    ![][Autoswap2]
   
   > [!NOTE]
   > Tesztelje az automatikus felcserélés az alkalmazásra vonatkozóan, először jelölje ki a cél nem éles tárhely **automatikus felcserélés tárolóhely** Ismerkedjen meg a szolgáltatás számára.  
   > 
   > 
3. Hajtsa végre egy adott üzembe helyezési pont kódot küldeni. Az automatikus felcserélés rövid idő múlva történjen, és a frissítés jelenik meg a cél a tárhely URL-címen.

<a name="Rollback"></a>

## <a name="to-rollback-a-production-app-after-swap"></a>A visszaállítás után swap éles alkalmazások
Ki a hibákat a tárolóhelycsere élesben azonosít, ha visszaállíthatja az üzembe helyezési ponti azok előtti swap állapota az azonos két üzembe helyezési ponti azonnal csere által.

<a name="Warm-up"></a>

## <a name="custom-warm-up-before-swap"></a>Egyéni bemelegítési swap előtt
Néhány alkalmazás egyéni bemelegítési műveletek lehet szükség. A `applicationInitialization` konfigurációs elem a Web.config fájlban lehetővé teszi egyéni inicializálási műveletek végrehajtása előtt a kérelem érkezik. A csereművelet végrehajtásához az egyéni bemelegítési várakozik. Íme egy minta web.config töredéket.

    <applicationInitialization>
        <add initializationPage="/" hostName="[app hostname]" />
        <add initializationPage="/Home/About" hostname="[app hostname]" />
    </applicationInitialization>

<a name="Delete"></a>

## <a name="to-delete-a-deployment-slot"></a>Egy üzembe helyezési pont törlése
Egy üzembe helyezési pont paneljén nyissa meg a telepített környezet tárolóhelye panelen, kattintson a **áttekintése** (az alapértelmezett oldal), és kattintson a **törlése** parancsra a parancssávon.  

![Egy üzembe helyezési pont törlése][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="azure-powershell-cmdlets-for-deployment-slots"></a>Az üzembe helyezési tárhely az Azure PowerShell-parancsmagjai
Az Azure PowerShell modul, amely kezelése a Windows PowerShell, hiszen támogatják az kezelése az Azure App Service üzembe helyezési segítségével Azure-parancsmagokat kínál.

* A telepítése és konfigurálása az Azure PowerShell és az Azure PowerShell hitelesítéséhez az Azure előfizetéssel rendelkező információkért lásd: [telepítése és konfigurálása a Microsoft Azure PowerShell](/powershell/azure/overview).  

- - -
### <a name="create-a-web-app"></a>Webalkalmazás létrehozása
```
New-AzureRmWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

- - -
### <a name="create-a-deployment-slot"></a>Egy üzembe helyezési tárhely létrehozása
```
New-AzureRmWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

- - -
### <a name="initiate-a-swap-with-review-multi-phase-swap-and-apply-destination-slot-configuration-to-source-slot"></a>Indítson el egy felcserélés felülvizsgálati (több fázisban swap) és a cél tárolóhely konfiguráció alkalmazása a forrás tárolási helyre
```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-source-slot-configuration"></a>Megszakítja a függőben lévő felcserélés (felülvizsgálati felcserélés) és a forrás helyezési pont konfigurációjának visszaállítása
```
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

- - -
### <a name="swap-deployment-slots"></a>Központi telepítés felcserélése
```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="delete-deployment-slot"></a>Üzembe helyezési pont törlése
```
Remove-AzureRmResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

- - -
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="azure-command-line-interface-azure-cli-commands-for-deployment-slots"></a>Az üzembe helyezési tárhely az Azure parancssori felület (CLI) parancsok
Az Azure parancssori felület platformfüggetlen parancsokat biztosít működik-e az Azure-ral, hiszen támogatják az App Service üzembe helyezési pontok kezelése.

* Telepítése és konfigurálása az Azure parancssori felület, beleértve az Azure parancssori felület csatlakoztatása az Azure-előfizetéshez, további információkért lásd: [telepítése és konfigurálása az Azure parancssori felület](../cli-install-nodejs.md).
* Az Azure App Service az Azure parancssori felületen használható parancsok listáját, hívja meg a `azure site -h`.

> [!NOTE] 
> A [Azure CLI 2.0](https://github.com/Azure/azure-cli) parancsok a üzembe helyezési, lásd: [az webalkalmazás üzembe helyezési pont](/cli/azure/webapp/deployment/slot).

- - -
### <a name="azure-site-list"></a>az Azure webhely-lista
Az aktuális előfizetésben az alkalmazásokkal kapcsolatos információk hívás **azure webhelylista**, az alábbi példa.

`azure site list webappslotstest`

- - -
### <a name="azure-site-create"></a>az Azure-webhely létrehozása
Egy üzembe helyezési tárhely létrehozása, hívja meg a **azure-webhely létrehozása** és adja meg a meglévő alkalmazás és a tárolóhely létrehozásához, az alábbi példában látható módon nevét.

`azure site create webappslotstest --slot staging`

Az új tárhely verziókövetésének engedélyezéséhez használja a **– git** beállítás, az alábbi példában látható módon.

`azure site create --git webappslotstest --slot staging`

- - -
### <a name="azure-site-swap"></a>az Azure site lapozófájl-kapacitás
Ahhoz, hogy a frissített telepítési az éles alkalmazások tárolóhely, használja a **azure hely swap** parancs a Csere műveletet, az alábbi példában látható módon. Az éles alkalmazás nem fog tapasztalni bármely állásidő, és nem fog változni hidegindítás.

`azure site swap webappslotstest`

- - -
### <a name="azure-site-delete"></a>az Azure webhely törlése
Az üzembe helyezési pont, amely már nem szükséges törölheti a **azure webhely törlése** parancs, az alábbi példában látható módon.

`azure site delete webappslotstest --slot staging`

- - -
> [!NOTE]
> Tekintsen meg működés közben egy webalkalmazást. [App Service kipróbálása](https://azure.microsoft.com/try/app-service/) azonnal, és hozzon létre egy rövid élettartamú alapszintű alkalmazást – nincs szükség bankkártyára, nem jár kötelezettségekkel.
> 
> 

## <a name="next-steps"></a>További lépések
[Az Azure App Service Web App – webes hozzáférés letiltása nem éles üzembe helyezési](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)
[Bevezetés az App Service Linux](../app-service/containers/app-service-linux-intro.md)
[Microsoft Azure ingyenes próbaverzió](https://azure.microsoft.com/pricing/free-trial/)

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

