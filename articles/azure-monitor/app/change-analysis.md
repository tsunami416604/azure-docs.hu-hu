---
title: Webalkalmazás-problémák keresése az Alkalmazásmódosítás-elemzés használatával az Azure Monitorban | Microsoft dokumentumok
description: Az Alkalmazásváltozás-elemzés az Azure Monitorban segítségével elháríthatja az Azure App Service élő webhelyein felmerülő alkalmazásproblémákat.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/07/2019
ms.openlocfilehash: 036b8c084bdfdc11c02274758c550c76bdc7b1e7
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348731"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Alkalmazásmódosítás-elemzés használata (előzetes verzió) az Azure Monitorban

Élő webhely-probléma vagy kimaradás esetén a kiváltó ok gyors meghatározása kritikus fontosságú. A szabványos figyelési megoldások figyelmeztethetik a problémára. Még azt is jelezhetik, hogy melyik összetevő nem működik. De ez a riasztás nem mindig magyarázza meg azonnal a hiba okát. Tudod, hogy a webhelyed öt perce működött, és most elromlott. Mi változott az elmúlt öt percben? Ez az a kérdés, hogy az alkalmazásváltozás-elemzés célja, hogy választ az Azure Monitor.

Az [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)erejére építve a Change Analysis betekintést nyújt az Azure-alkalmazás változásaiba a megfigyelhetőség növelése és az MTTR csökkentése (a javításhoz elegendő idő) csökkentése érdekében.

> [!IMPORTANT]
> A Módosításelemzés jelenleg előzetes verzióban érhető el. Ez az előzetes verzió szolgáltatásszintű szerződés nélkül érhető el. Ez a verzió éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy egyes szolgáltatások nem támogatottak, vagy korlátozott képességekkel rendelkeznek. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="overview"></a>Áttekintés

A Változáselemzés különböző típusú módosításokat észlel, az infrastruktúrarétegtől egészen az alkalmazás üzembe helyezéséig. Ez egy előfizetés-szintű Azure-erőforrás-szolgáltató, amely ellenőrzi az erőforrás-változásokat az előfizetésben. A Változáselemzés különböző diagnosztikai eszközök adatait biztosítja, hogy a felhasználók megértsék, milyen változások okozhattak problémákat.

Az alábbi ábra a változáselemzés architektúráját mutatja be:

![Architektúradiagram arról, hogy a változáselemzés hogyan jut el a módosítási adatokhoz, és biztosítja azokat az ügyféleszközöknek](./media/change-analysis/overview.png)

Jelenleg változáselemzés integrálva van a **problémák diagnosztizálása és megoldása** az App Service webapp, valamint a rendelkezésre álló önálló lapon az Azure Portalon.
Tekintse meg az Összes erőforrás módosításai megtekintése az *Azure-ban* szakaszban a Változáselemzés panel eléréséhez, és a *Változáselemzés a Web Apps szolgáltatás* hoz ez a cikk későbbi részében a Web App portálon.

### <a name="azure-resource-manager-tracked-properties-changes"></a>Az Azure Resource Manager nyomon követte a tulajdonságok változásait

Az [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)használatával a Változáselemzés egy előzményrekordot ad arról, hogy az alkalmazást üzemeltető Azure-erőforrások hogyan változtak az idő múlásával. A nyomon követett beállítások, például a felügyelt identitások, a platformoperációsrendszer frissítése és az állomásnevek észlelhetők.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Az Azure Resource Manager végrehajtott beállítások módosításai
Az olyan beállítások, mint az IP-konfigurációs szabály, a TLS-beállítások és a bővítményverziók még nem érhetők el az ARG-ban, ezért módosítsa az elemzéslekérdezéseket, és biztonságosan számítsa ki ezeket a módosításokat, hogy további részleteket adjon meg az alkalmazásban bekövetkezett változásokról. Ez az információ még nem érhető el az Azure Resource Graph, de hamarosan elérhető lesz.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Változások a webalkalmazás telepítésében és konfigurálásában (vendégmódosítások)

A Változáselemzés 4 óránként rögzíti az alkalmazás üzembe helyezésének és konfigurációs állapotát. Észleli például az alkalmazáskörnyezeti változók változásait. Az eszköz kiszámítja a különbségeket, és bemutatja, mi változott. Az Erőforrás-kezelő módosításokkal ellentétben előfordulhat, hogy a kódtelepítés módosítási információi nem érhetők el azonnal az eszközben. A Változáselemzés legújabb változásainak megtekintéséhez válassza **a Betekintési módosítások most**lehetőséget.

![Képernyőkép a "Bekés most" gombról](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Függőségi változások

Az erőforrás-függőségek módosításai problémákat is okozhatnak a webalkalmazásokban. Ha például egy webalkalmazás egy Redis-gyorsítótárba hív, a Redis gyorsítótár-termékváltozat hatással lehet a webalkalmazás teljesítményére. A függőségek változásainak észleléséhez a Változáselemzés ellenőrzi a webalkalmazás DNS-rekordját. Ily módon azonosítja az összes alkalmazás-összetevő változásait, amelyek problémákat okozhatnak.
Jelenleg a következő függőségek támogatottak:
- Web Apps
- Azure Storage
- Azure SQL

### <a name="enablement"></a>Engedélyezés
A "Microsoft.ChangeAnalysis" erőforrás-szolgáltatót regisztrálni kell az Azure Resource Manager által nyomon követett tulajdonságokra vonatkozó előfizetéssel, és a proxied beállítások módosítják az adatokat, hogy elérhetők legyenek. Amikor belép a Web App diagnosztizálására és megoldására, vagy hozza létre a Változáselemzés önálló lapot, ez az erőforrás-szolgáltató automatikusan regisztrálva lesz. Nem rendelkezik teljesítmény- és költségimplementációkkal az előfizetéshez. Ha engedélyezi a változáselemzést a webalkalmazásokhoz (vagy engedélyezi a problémák diagnosztizálása és megoldása eszközt), az elhanyagolható hatással lesz a webalkalmazásteljesítményére, és nincs számlázási költség.
A vendégalkalmazásokon belüli webalkalmazások módosításai hoz létre külön engedélyezésea kódfájlok webalkalmazáson belüli beolvasásához. További információt [a problémadiagnosztizálás és problémamegoldás című szakasz változáselemzésének engedélyezése](https://docs.microsoft.com/azure/azure-monitor/app/change-analysis#enable-change-analysis-in-the-diagnose-and-solve-problems-tool) című témakörben talál további részletekért.


## <a name="viewing-changes-for-all-resources-in-azure"></a>Az Azure összes erőforrásának változásainak megtekintése
Az Azure Monitorban egy önálló panel változáselemzés megtekintheti az összes változást az elemzési és alkalmazásfüggőségi erőforrások.

Keresse meg a változáselemzés a keresősáv az Azure Portalon, hogy indítsa el a panelt.

![Képernyőkép: változáselemzés keresése az Azure Portalon](./media/change-analysis/search-change-analysis.png)

Válassza az Erőforráscsoport és az erőforrások lehetőséget a módosítások megtekintésének megkezdéséhez.

![Képernyőkép: Változáselemzés panel az Azure Portalon](./media/change-analysis/change-analysis-standalone-blade.png)

Az elemzési adatok és a kapcsolódó függőségek erőforrások, amelyek az alkalmazást üzemeltetik. Ez a nézet úgy van kialakítva, hogy a fejlesztők alkalmazásközpontúak legyenek a problémák elhárításához.

A jelenleg támogatott erőforrások a következők:
- Virtuális gépek
- Virtuális gép méretezési készlete
- Azure hálózati erőforrások
- Webalkalmazás vendégfájl-követéssel és környezeti változókkal

Bármilyen visszajelzést, használja a küldés visszajelzést changeanalysisteam@microsoft.comgombot a panelen vagy e-mailben .

![Képernyőkép a Visszajelzés gombról a Változáselemzés panelen](./media/change-analysis/change-analysis-feedback.png)

## <a name="change-analysis-for-the-web-apps-feature"></a>A Web Apps szolgáltatás elemzésének módosítása

Az Azure Monitorban a Változáselemzés is be van építve az önkiszolgáló **diagnosztizálása és a problémák megoldása** élményét. Ezt az élményt az App Service-alkalmazás **Áttekintés lapján** érheti el.

![Képernyőkép az "Áttekintés" gombról és a "Problémák diagnosztizálása és megoldása" gombról](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>A problémaelemzés engedélyezése a Problémák diagnosztizálása és megoldása eszközben

1. Válassza **az Elérhetőség és teljesítmény**lehetőséget.

    ![Képernyőkép a "Rendelkezésre állás és teljesítmény" hibaelhárítási lehetőségekről](./media/change-analysis/availability-and-performance.png)

1. Válassza **az Alkalmazásmódosítások lehetőséget.** Nem mintha a funkció is elérhető **application összeomlik**.

   ![Képernyőkép az "Alkalmazás összeomlik" gombról](./media/change-analysis/application-changes.png)

1. A változáselemzés engedélyezéséhez válassza **az Engedélyezés most**lehetőséget.

   ![Képernyőkép az "Alkalmazás összeomlik" lehetőségekről](./media/change-analysis/enable-changeanalysis.png)

1. Kapcsolja be az **Elemzés módosítása lehetőséget,** és válassza a **Mentés lehetőséget.** Az eszköz megjeleníti az összes webalkalmazást egy App Service-csomag alatt. A csomagszintű kapcsolóval bekapcsolhatja a Változáselemzés lehetőséget a csomag alatt található összes webalkalmazásnál.

    ![Képernyőkép a "Változáselemzés engedélyezése" felhasználói felületről](./media/change-analysis/change-analysis-on.png)


1. A változáselemzés eléréséhez jelölje **be a Problémák diagnosztizálása és megoldása** > Az elérhetőségi és**teljesítményalkalmazás-összeomlások****Availability and Performance** > lehetőséget. Megjelenik egy grafikon, amely összefoglalja a változások típusát az idő múlásával, valamint a változások részleteit. Alapértelmezés szerint az elmúlt 24 óra módosításai jelennek meg, hogy segítsenek az azonnali problémák megoldásában.

     ![Képernyőkép a változásdiff nézetről](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>Változáselemzés engedélyezése nagy méretekben

Ha az előfizetés számos webalkalmazást tartalmaz, a szolgáltatás engedélyezése a webalkalmazás szintjén nem lenne hatékony. Futtassa a következő parancsfájlt az előfizetésben lévő összes webalkalmazás engedélyezéséhez.

Előfeltételek:
* PowerShell Az modul. Kövesse [az Utasításokat az Azure PowerShell-modul telepítése kor.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.6.0)

Futtassa a következő parancsfájlt:

```PowerShell
# Log in to your Azure subscription
Connect-AzAccount

# Get subscription Id
$SubscriptionId = Read-Host -Prompt 'Input your subscription Id'

# Make Feature Flag visible to the subscription
Set-AzContext -SubscriptionId $SubscriptionId

# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"


# Enable each web app
$webapp_list = Get-AzWebApp | Where-Object {$_.kind -eq 'app'}
foreach ($webapp in $webapp_list)
{
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId $webapp.Id -Tag $tags -Force
}

```



## <a name="next-steps"></a>További lépések

- Az Application Insights engedélyezése az [Azure App Services-alkalmazásokhoz.](azure-web-apps.md)
- Engedélyezze az Application Insights for [Azure Virtuálisgép- és Azure-szintű méretezési csoport IIS által üzemeltetett alkalmazásokat.](azure-vm-vmss-apps.md)
- További információ az [Azure Resource Graph,](https://docs.microsoft.com/azure/governance/resource-graph/overview)amely segít a változáselemzés működtetésében.
