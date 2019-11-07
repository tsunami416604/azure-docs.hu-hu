---
title: Alkalmazás-módosítási elemzés használata a Azure Monitor webalkalmazásokkal kapcsolatos problémák kereséséhez | Microsoft Docs
description: Az alkalmazással kapcsolatos problémák elhárításához használja a Azure Monitor az alkalmazások változási elemzését Azure App Service.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/07/2019
ms.openlocfilehash: dc572d29b4e6d95525959becad0ed8069735e33c
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605991"
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

A változások elemzése a App Service webalkalmazásban a **problémák diagnosztizálásához és megoldásához** is integrálva van. A módosítás észlelésének engedélyezéséhez és a webalkalmazás változásainak megtekintéséhez tekintse meg a cikk későbbi, a *Web Apps funkciójának változási elemzését* ismertető szakaszát.

### <a name="azure-resource-manager-deployment-changes"></a>Azure Resource Manager telepítési változások

Az [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)használatával a Change Analysis egy korábbi rekordot biztosít arról, hogy az alkalmazás által üzemeltetett Azure-erőforrások mennyi idő alatt változtak. A Change Analysis képes azonosítani például az IP-konfigurációs szabályok, a felügyelt identitások és az SSL-beállítások változásait. Tehát ha egy címkét egy webalkalmazáshoz ad hozzá, az elemzés változása a változást tükrözi. Ez az információ mindaddig elérhető, amíg a `Microsoft.ChangeAnalysis` erőforrás-szolgáltató engedélyezve van az Azure-előfizetésben.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>A webalkalmazások telepítésének és konfigurációjának változásai (a vendég módosításaiban)

A Change Analysis egy alkalmazás központi telepítési és konfigurációs állapotát 4 óránként rögzíti. Képes azonosítani például az alkalmazás környezeti változóinak változásait. Az eszköz kiszámítja a különbségeket, és bemutatja, hogy mi változott. A Resource Manager változásaitól eltérően előfordulhat, hogy a programkód-telepítési változási információk nem lesznek azonnal elérhetők az eszközön. A Change Analysis legújabb változásainak megtekintéséhez válassza a **változások ellenőrzése most**lehetőséget.

![Képernyőkép a "változások ellenőrzése most" gombról](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Függőségi változások

Az erőforrás-függőségek változásai a webalkalmazások hibáit is okozhatják. Ha például egy webalkalmazás meghívja a Redis cache-t, a Redis cache SKU hatással lehet a webalkalmazás teljesítményére. A függőségek változásainak észleléséhez a Change Analysis ellenőrzi a webalkalmazás DNS-rekordját. Így minden olyan alkalmazás-összetevő változását azonosítja, amely problémákat okozhat.
Jelenleg a következő függőségek támogatottak:
- Web Apps
- Azure Storage
- Azure SQL

## <a name="viewing-changes-for-all-resources-in-azure"></a>Az Azure összes erőforrásának változásainak megtekintése
Azure Monitor az elemzések és az alkalmazás-függőségek erőforrásaival végzett módosítások megtekintéséhez különálló panel áll rendelkezésre a Change Analysis szolgáltatáshoz.

Keresse meg a Change Analysis kifejezést a Azure Portal található keresési sávban a panel elindításához.

![Képernyőkép a keresési változások elemzéséről Azure Portal](./media/change-analysis/search-change-analysis.png)

Válassza ki az erőforráscsoportot és az erőforrásokat a módosítások megtekintéséhez.

![Képernyőkép a Change Analysis panelről Azure Portal](./media/change-analysis/change-analysis-standalone-blade.png)

Megtekintheti az alkalmazást üzemeltető információk és a kapcsolódó függőségek erőforrásait. Ez a nézet a fejlesztők számára készült alkalmazás-központú megoldás, amellyel elháríthatja a problémákat.

A jelenleg támogatott erőforrások a következők:
- Virtuális gépek
- Virtuálisgép-méretezési csoport
- Azure hálózati erőforrások
- Webalkalmazás és a vendégen belüli fájlok követése és környezeti változók változásai

Ha bármilyen visszajelzést szeretne küldeni, használja a (visszajelzés küldése) gombot a panelen vagy az e-mail-changeanalysisteam@microsoft.com. 

![Képernyőkép – visszajelzés gomb a Change Analysis panelen](./media/change-analysis/change-analysis-feedback.png)

## <a name="change-analysis-for-the-web-apps-feature"></a>Web Apps szolgáltatás elemzésének módosítása

Azure Monitor a Change Analysis az önkiszolgáló **diagnosztizálása és a problémák megoldása** terén is beépül. Ezt a felhasználói élményt a App Service alkalmazás **Áttekintés** lapjáról érheti el.

![Az "áttekintés" gomb és a "problémák diagnosztizálása és megoldása" gomb képernyőképe](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>A változások elemzésének engedélyezése a problémák diagnosztizálása és megoldása eszközben

1. Válassza ki **a rendelkezésre állást és a teljesítményt**.

    ![Képernyőkép a "rendelkezésre állás és teljesítmény" hibaelhárítási lehetőségeiről](./media/change-analysis/availability-and-performance.png)

1. Válassza az **alkalmazás módosítása**lehetőséget. A szolgáltatás nem érhető el az **alkalmazás összeomlása**során is.

   ![Képernyőkép az "alkalmazás-összeomlások" gombról](./media/change-analysis/application-changes.png)

1. A módosítási elemzés engedélyezéséhez válassza az **Engedélyezés most**lehetőséget.

   ![Képernyőkép az "alkalmazás-összeomlások" lehetőségeiről](./media/change-analysis/enable-changeanalysis.png)

1. Kapcsolja be az **elemzést** , és válassza a **Mentés**lehetőséget.

    ![Képernyőfelvétel: a Change Analysis felhasználói felületének engedélyezése](./media/change-analysis/change-analysis-on.png)


1. A Change Analysis szolgáltatás eléréséhez válassza a **problémák diagnosztizálása és megoldása** > a **rendelkezésre állás és a teljesítmény** > az **alkalmazás összeomlik**lehetőséget. Ekkor megjelenik egy gráf, amely összegzi a változások típusát az idő múlásával együtt a változások részleteivel együtt:

     ![Képernyőkép a Change diff nézetről](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>Módosítási elemzés engedélyezése a skálán

Ha az előfizetése számos webalkalmazást tartalmaz, a webalkalmazás szintjén a szolgáltatás engedélyezése nem hatékony. Futtassa az alábbi parancsfájlt az előfizetésben lévő összes webalkalmazás engedélyezéséhez.

Előfeltételek:
* PowerShell az Module. Kövesse [az Azure PowerShell modul telepítésének](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.6.0) utasításait

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

- Az [Azure app Services-alkalmazások](azure-web-apps.md)Application Insights engedélyezése.
- Engedélyezze Application Insights az [Azure-beli virtuális gépek és az Azure-beli virtuálisgép-méretezési csoport IIS által üzemeltetett alkalmazásai](azure-vm-vmss-apps.md)számára.
- További információ az [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)-ról, amely segít a energiagazdálkodási változások elemzésében.
