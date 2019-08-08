---
title: Alkalmazás-módosítási elemzés használata a Azure Monitor webalkalmazásokkal kapcsolatos problémák kereséséhez | Microsoft Docs
description: Az alkalmazással kapcsolatos problémák elhárításához használja a Azure Monitor az alkalmazások változási elemzését Azure App Service.
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: a08fc7d7822b4aeddafb588fdb73e86559ce2b12
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68849173"
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

Az [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)használatával a Change Analysis egy korábbi rekordot biztosít arról, hogy az alkalmazás által üzemeltetett Azure-erőforrások mennyi idő alatt változtak. A Change Analysis képes azonosítani például az IP-konfigurációs szabályok, a felügyelt identitások és az SSL-beállítások változásait. Tehát ha egy címkét egy webalkalmazáshoz ad hozzá, az elemzés változása a változást tükrözi. Ez az információ mindaddig elérhető, amíg az `Microsoft.ChangeAnalysis` erőforrás-szolgáltató engedélyezve van az Azure-előfizetésben.

### <a name="changes-in-web-app-deployment-and-configuration"></a>A webalkalmazások telepítésének és konfigurálásának változásai

A Change Analysis egy alkalmazás központi telepítési és konfigurációs állapotát 4 óránként rögzíti. Képes azonosítani például az alkalmazás környezeti változóinak változásait. Az eszköz kiszámítja a különbségeket, és bemutatja, hogy mi változott. A Resource Manager változásaitól eltérően előfordulhat, hogy a programkód-telepítési változási információk nem lesznek azonnal elérhetők az eszközön. A Change Analysis legújabb változásainak megtekintéséhez válassza a **változások ellenőrzése most**lehetőséget.

![Képernyőkép a "változások ellenőrzése most" gombról](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Függőségi változások

Az erőforrás-függőségek változásai a webalkalmazások hibáit is okozhatják. Ha például egy webalkalmazás meghívja a Redis cache-t, a Redis cache SKU hatással lehet a webalkalmazás teljesítményére. A függőségek változásainak észleléséhez a Change Analysis ellenőrzi a webalkalmazás DNS-rekordját. Így minden olyan alkalmazás-összetevő változását azonosítja, amely problémákat okozhat.
Jelenleg a következő függőségek támogatottak:
- Web Apps
- Azure Storage
- Azure SQL


## <a name="change-analysis-for-the-web-apps-feature"></a>Web Apps szolgáltatás elemzésének módosítása

Azure Monitor a Change Analysis jelenleg az önkiszolgáló diagnosztizálása és a **problémák megoldása** terén van beépítve. Ezt a felhasználói élményt a App Service alkalmazás **Áttekintés** lapjáról érheti el.

![Az "áttekintés" gomb és a "problémák diagnosztizálása és megoldása" gomb képernyőképe](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>A változások elemzésének engedélyezése a problémák diagnosztizálása és megoldása eszközben

1. Válassza ki **a rendelkezésre állást és a teljesítményt**.

    ![Képernyőkép a "rendelkezésre állás és teljesítmény" hibaelhárítási lehetőségeiről](./media/change-analysis/availability-and-performance.png)

1. Válassza az **alkalmazás módosítása**lehetőséget. A szolgáltatás nem érhető el az **alkalmazás összeomlása**során is.

   ![Képernyőkép az "alkalmazás-összeomlások" gombról](./media/change-analysis/application-changes.png)

1. A módosítási elemzés engedélyezéséhez válassza az **Engedélyezés most**lehetőséget.

   ![Képernyőkép az "alkalmazás-összeomlások" lehetőségeiről](./media/change-analysis/enable-changeanalysis.png)

1. Kapcsolja be az elemzést, és válassza a **Mentés**lehetőséget.

    ![Képernyőfelvétel: a Change Analysis felhasználói felületének engedélyezése](./media/change-analysis/change-analysis-on.png)


1. A Change Analysis szolgáltatás eléréséhez válassza a **diagnosztizálás és megoldás problémák** > **rendelkezésre állása és** > a teljesítménybeli**alkalmazások összeomlása**lehetőséget. Ekkor megjelenik egy gráf, amely összegzi a változások típusát az idő múlásával együtt a változások részleteivel együtt:

     ![Képernyőkép a Change diff nézetről](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>Módosítási elemzés engedélyezése a skálán

Ha az előfizetése számos webalkalmazást tartalmaz, a webalkalmazás szintjén a szolgáltatás engedélyezése nem hatékony. Ebben az esetben kövesse az alábbi alternatív utasításokat.

### <a name="register-the-change-analysis-resource-provider-for-your-subscription"></a>Regisztrálja a Change Analysis erőforrás-szolgáltatót az előfizetéséhez

1. Regisztrálja a Change Analysis szolgáltatás jelzőjét (előzetes verzió). Mivel a funkció jelzője előzetes verzióban érhető el, regisztrálnia kell, hogy láthatóvá tegye az előfizetését:

   1. Nyissa meg az [Azure Cloud Shellt](https://azure.microsoft.com/features/cloud-shell/).

      ![Képernyőkép a változásról Cloud Shell](./media/change-analysis/cloud-shell.png)

   1. Módosítsa a rendszerhéj típusát a **powershellre**.

      ![Képernyőkép a változásról Cloud Shell](./media/change-analysis/choose-powershell.png)

   1. Futtassa az alábbi PowerShell-parancsot:

        ``` PowerShell
        Set-AzContext -Subscription <your_subscription_id> #set script execution context to the subscription you are trying to enable
        Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.ChangeAnalysis" -ListAvailable #Check for feature flag availability
        Register-AzureRmProviderFeature -FeatureName PreviewAccess -ProviderNamespace Microsoft.ChangeAnalysis #Register feature flag
        ```

1. Regisztrálja a Change Analysis erőforrás-szolgáltatót az előfizetéshez.

   - Lépjen azelőfizetések elemre, és válassza ki azt az előfizetést, amelyet engedélyezni szeretne a Change Service-ben. Ezután válassza az erőforrás-szolgáltatók elemet:

        ![A Change Analysis erőforrás-szolgáltató regisztrálását bemutató képernyőkép](./media/change-analysis/register-rp.png)

       - Válassza a **Microsoft. ChangeAnalysis**elemet. Ezután a lap tetején válassza a **regisztráció**lehetőséget.

       - Az erőforrás-szolgáltató engedélyezése után a webalkalmazásban beállíthat egy rejtett címkét a módosítások észleléséhez a telepítés szintjén. Rejtett címke beállításához kövesse a **nem sikerült beolvasni az elemzési adatok módosítása**című szakasz utasításait.

   - Azt is megteheti, hogy egy PowerShell-parancsfájl használatával regisztrálja az erőforrás-szolgáltatót:

        ```PowerShell
        Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState #Check if RP is ready for registration

        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis" #Register the Change Analysis RP
        ```

        A következő parancs futtatásával a PowerShell használatával egy webalkalmazás rejtett címkéjét állíthatja be:

        ```powershell
        $webapp=Get-AzWebApp -Name <name_of_your_webapp>
        $tags = $webapp.Tags
        $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
        Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
        ```

     > [!NOTE]
     > A rejtett címke hozzáadása után előfordulhat, hogy a módosítások megkezdése előtt akár 4 órát is várnia kell. Az eredmények késleltetve lettek, mert a Change Analysis csak 4 óránként vizsgálja a webalkalmazást. A 4 órás ütemterv korlátozza a vizsgálat teljesítményére gyakorolt hatást.

## <a name="next-steps"></a>További lépések

- Az [Azure app Services-alkalmazások](azure-web-apps.md)Application Insights engedélyezése.
- Engedélyezze Application Insights az [Azure-beli virtuális gépek és az Azure-beli virtuálisgép-méretezési csoport IIS által üzemeltetett alkalmazásai](azure-vm-vmss-apps.md)számára.
- További információ az [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)-ról, amely segít a energiagazdálkodási változások elemzésében.
