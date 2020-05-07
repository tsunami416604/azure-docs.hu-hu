---
title: Alkalmazás-módosítási elemzés használata a Azure Monitor webalkalmazásokkal kapcsolatos problémák kereséséhez | Microsoft Docs
description: Az alkalmazással kapcsolatos problémák elhárításához használja a Azure Monitor az alkalmazások változási elemzését Azure App Service.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/04/2020
ms.openlocfilehash: c287a2315f2b2319a6873ce84ee0e4e48bec8444
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836774"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Alkalmazás-módosítási elemzés (előzetes verzió) használata Azure Monitor

Ha élő hely probléma vagy leállás történik, az alapvető ok gyors meghatározása kritikus fontosságú. A standard szintű figyelési megoldások riasztást kérhetnek a problémára. Akár azt is jelezhetik, hogy melyik összetevő meghibásodik. Ez a riasztás azonban nem mindig azonnal ismerteti a hiba okát. Tudja, hogy a hely öt perccel ezelőtt dolgozott, és most megszakadt. Mi változott az elmúlt öt percben? Ez az a kérdés, hogy az alkalmazás változásának elemzése úgy van kialakítva, hogy Azure Monitor válaszoljanak.

Az [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)teljesítményének növelésével a Change Analysis betekintést nyújt az Azure-alkalmazás változásaiba, hogy növelje a megfigyelt és csökkentse a MTTR (a javítási időt).

> [!IMPORTANT]
> A Change Analysis szolgáltatás jelenleg előzetes verzióban érhető el. Ezt az előzetes verziót szolgáltatási szintű szerződés nélkül biztosítjuk. Ez a verzió éles munkaterhelések esetén nem ajánlott. Előfordulhat, hogy egyes funkciók nem támogatottak, vagy korlátozott képességekkel rendelkeznek. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Áttekintés

A Change Analysis különböző típusú módosításokat észlel, az infrastruktúra rétegből egészen az alkalmazás üzembe helyezéséhez. Ez egy előfizetési szintű Azure-erőforrás-szolgáltató, amely ellenőrzi az erőforrás-változásokat az előfizetésben. A Change Analysis számos diagnosztikai eszközt tartalmaz, amelyek segítségével a felhasználók megismerhetik, hogy milyen változások okozták a hibákat.

A következő ábra a változások elemzésének architektúráját szemlélteti:

![Architektúra-diagram, amely bemutatja, hogyan módosulnak az elemzések, és hogyan biztosítható az ügyféleszközök számára](./media/change-analysis/overview.png)

## <a name="data-sources"></a>Adatforrások

Az alkalmazás megváltoztatja az elemzési lekérdezéseket Azure Resource Manager követett tulajdonságok, a proxyn belüli konfigurációk és a webalkalmazás módosításaiban. Emellett a szolgáltatás nyomon követi az erőforrás-függőségi változásokat az alkalmazások végpontok közötti diagnosztizálásához és figyeléséhez.

### <a name="azure-resource-manager-tracked-properties-changes"></a>A nyomon követett tulajdonságok változásai Azure Resource Manager

Az [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)használatával a Change Analysis egy korábbi rekordot biztosít arról, hogy az alkalmazás által üzemeltetett Azure-erőforrások mennyi idő alatt változtak. A nyomon követett beállítások, például a felügyelt identitások, a platform operációsrendszer-frissítése és az állomásnevek is észlelhetők.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Azure Resource Manager proxyn végzett beállítások változásai

A beállítások, például az IP-konfigurációs szabály, a TLS-beállítások és a bővítmény-verziók még nem érhetők el az Azure Resource Graph-ban, ezért az elemzési lekérdezések módosítása és a módosítások biztonságos kiszámításával további részleteket tudhat meg az alkalmazásban megváltoztatott adatokról.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>A webalkalmazások telepítésének és konfigurációjának változásai (a vendég módosításaiban)

A Change Analysis egy alkalmazás központi telepítési és konfigurációs állapotát 4 óránként rögzíti. Képes azonosítani például az alkalmazás környezeti változóinak változásait. Az eszköz kiszámítja a különbségeket, és bemutatja, hogy mi változott. A Resource Manager változásaitól eltérően előfordulhat, hogy a programkód-telepítési változási információk nem lesznek azonnal elérhetők az eszközön. A Change Analysis legújabb változásainak megtekintéséhez válassza a **frissítés**lehetőséget.

![Képernyőkép a "változások ellenőrzése most" gombról](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Függőségi változások

Az erőforrás-függőségek változásai a webalkalmazások hibáit is okozhatják. Ha például egy webalkalmazás meghívja a Redis cache-t, a Redis cache SKU hatással lehet a webalkalmazás teljesítményére. A függőségek változásainak észleléséhez a Change Analysis ellenőrzi a webalkalmazás DNS-rekordját. Így minden olyan alkalmazás-összetevő változását azonosítja, amely problémákat okozhat.
Jelenleg a következő függőségek támogatottak:
- Web Apps
- Azure Storage
- Azure SQL

## <a name="application-change-analysis-service"></a>Application Change Analysis Service

Az alkalmazás megváltoztatja az Analysis Service-t, és összesíti az adatokat a fent említett adatforrásokból. Elemzési lehetőségeket biztosít a felhasználók számára, hogy könnyen navigáljon az összes erőforrás-változáson, és azonosítsa, hogy melyik módosítás vonatkozik a hibaelhárítási vagy figyelési kontextusban.
A (z) "Microsoft. ChangeAnalysis" erőforrás-szolgáltatót regisztrálni kell egy előfizetésben a Azure Resource Manager követett tulajdonságok és a proxyn lévő beállítások módosítására vonatkozó adatváltozások számára. A webalkalmazás diagnosztizálása és megoldása eszköz beírásakor vagy a Change Analysis standalone (önálló módosítás) lapon az erőforrás-szolgáltató automatikusan regisztrálva lesz. Az előfizetéshez nem tartozik teljesítmény vagy Cost implementáció. Ha engedélyezi a webes alkalmazások módosítási elemzését (vagy lehetővé teszi a problémák diagnosztizálását és megoldását), akkor az elhanyagolható teljesítménnyel kapcsolatos hatással lesz a webalkalmazásra, és nincs számlázási díj.
A webalkalmazások vendégen belüli változásaihoz külön engedélyezés szükséges a programkódok webalkalmazásban való vizsgálatához. További részletekért lásd a cikk későbbi, [a problémák diagnosztizálása és megoldása eszközének módosítása](https://docs.microsoft.com/azure/azure-monitor/app/change-analysis#application-change-analysis-in-the-diagnose-and-solve-problems-tool) című szakaszát.

## <a name="visualizations-for-application-change-analysis"></a>Az alkalmazás változási elemzésének vizualizációi

### <a name="standalone-ui"></a>Önálló felhasználói felület

Azure Monitor a változások elemzésére szolgáló önálló ablaktábla a változásoknak az alkalmazás függőségeibe és erőforrásaiba való betekintéssel történő megtekintésére szolgál.

A felhasználói élmény elindításához keresse meg a Change Analysis kifejezést a Azure Portal található keresési sávon.

![Képernyőkép a keresési változások elemzéséről Azure Portal](./media/change-analysis/search-change-analysis.png)

A kiválasztott előfizetéshez tartozó összes erőforrás az elmúlt 24 órában történt változásokkal jelenik meg. A lap terhelési teljesítményének optimalizálása érdekében a szolgáltatás egyszerre 10 erőforrást jelenít meg. További erőforrások megtekintéséhez kattintson a következő lapokra. Dolgozunk a korlátozás eltávolításán.

![Képernyőkép a Change Analysis panelről Azure Portal](./media/change-analysis/change-analysis-standalone-blade.png)

Egy erőforrásra kattintva megtekintheti az összes módosítást. Ha szükséges, tekintse meg a JSON formátumú változás részleteit és az elemzéseket tartalmazó változást.

![Képernyőkép a változás részleteiről](./media/change-analysis/change-details.png)

Bármilyen visszajelzést a panelen vagy e-mailben changeanalysisteam@microsoft.coma visszajelzés küldése gombra kattintva használhat.

![Képernyőkép – visszajelzés gomb a Change Analysis panelen](./media/change-analysis/change-analysis-feedback.png)

### <a name="web-app-diagnose-and-solve-problems"></a>Webalkalmazás diagnosztizálása és megoldása

Azure Monitor a Change Analysis az önkiszolgáló **diagnosztizálása és a problémák megoldása** terén is beépül. Ezt a felhasználói élményt a App Service alkalmazás **Áttekintés** lapjáról érheti el.

![Az "áttekintés" gomb és a "problémák diagnosztizálása és megoldása" gomb képernyőképe](./media/change-analysis/change-analysis.png)

### <a name="application-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Alkalmazás-változási elemzés a problémák diagnosztizálása és megoldása eszközben

Az Application Change Analysis egy önálló detektor a webalkalmazásban a problémák diagnosztizálásához és megoldásához. Az **alkalmazás összeomlik** és a **webalkalmazás-leállási érzékelők**is összesítve vannak. A problémák diagnosztizálása és megoldása eszköz beírása után a **Microsoft. ChangeAnalysis** erőforrás-szolgáltató automatikusan regisztrálva lesz. Kövesse az alábbi utasításokat a webalkalmazás vendégen belüli módosítás-követésének engedélyezéséhez.

1. Válassza ki **a rendelkezésre állást és a teljesítményt**.

    ![Képernyőkép a "rendelkezésre állás és teljesítmény" hibaelhárítási lehetőségeiről](./media/change-analysis/availability-and-performance.png)

2. Válassza az **alkalmazás módosítása**lehetőséget. A funkció az **alkalmazások összeomlása**során is elérhető.

   ![Képernyőkép az "alkalmazás-összeomlások" gombról](./media/change-analysis/application-changes.png)

3. A módosítási elemzés engedélyezéséhez válassza az **Engedélyezés most**lehetőséget.

   ![Képernyőkép az "alkalmazás-összeomlások" lehetőségeiről](./media/change-analysis/enable-changeanalysis.png)

4. Kapcsolja be az **elemzést** , és válassza a **Mentés**lehetőséget. Az eszköz az összes webalkalmazást megjeleníti App Service csomag alatt. A csomag szintje kapcsoló használatával bekapcsolhatja a tervben szereplő összes webalkalmazáshoz tartozó változások elemzését.

    ![Képernyőfelvétel: a Change Analysis felhasználói felületének engedélyezése](./media/change-analysis/change-analysis-on.png)

5. A Change Analysis szolgáltatás eléréséhez válassza a **diagnosztizálás és megoldás problémák** > **rendelkezésre állása és a teljesítménybeli** > **alkalmazások összeomlása**lehetőséget. Ekkor megjelenik egy gráf, amely összefoglalja a változások típusát a változások részleteivel együtt. Alapértelmezés szerint az elmúlt 24 órában történt változások segítenek az azonnali problémák megoldásában.

     ![Képernyőkép a Change diff nézetről](./media/change-analysis/change-view.png)

### <a name="enable-change-analysis-at-scale"></a>Módosítási elemzés engedélyezése a skálán

Ha az előfizetése számos webalkalmazást tartalmaz, a webalkalmazás szintjén a szolgáltatás engedélyezése nem hatékony. Futtassa az alábbi parancsfájlt az előfizetésben lévő összes webalkalmazás engedélyezéséhez.

Előfeltételek:

- PowerShell az Module. Kövesse [az Azure PowerShell modul telepítésének](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.6.0) utasításait

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

### <a name="virtual-machine-diagnose-and-solve-problems"></a>Virtuális gépek diagnosztizálása és megoldása

Nyissa meg a problémák diagnosztizálása és megoldása eszközt a virtuális gépen.  Nyissa meg a **hibaelhárítási eszközöket**, keresse meg az oldalt, és válassza a **legutóbbi módosítások elemzése** lehetőséget a módosítások megtekintéséhez a virtuális gépen.

![Képernyőfelvétel a virtuális gép diagnosztizálásáról és megoldásáról](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Képernyőfelvétel a virtuális gép diagnosztizálásáról és megoldásáról](./media/change-analysis/analyze-recent-changes.png)

## <a name="next-steps"></a>További lépések

- Az [Azure app Services-alkalmazások](azure-web-apps.md)Application Insights engedélyezése.
- Engedélyezze Application Insights az [Azure-beli virtuális gépek és az Azure-beli virtuálisgép-méretezési csoport IIS által üzemeltetett alkalmazásai](azure-vm-vmss-apps.md)számára.
- További információ az [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)-ról, amely segít a energiagazdálkodási változások elemzésében.
