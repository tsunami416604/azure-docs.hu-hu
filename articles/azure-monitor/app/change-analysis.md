---
title: Az Azure Monitor módosítás Alkalmazáselemzés használatával keresse meg a webalkalmazás-problémák |} A Microsoft Docs
description: Elemzések használata teszi alkalmazás módosítása az Azure monitorban élő webhelyeken az Azure App Service-alkalmazások hibáinak elhárítása.
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: 45df8f9e57223ea60a11c6af2187d362184cae2b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443350"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Alkalmazáselemzés módosítása (előzetes verzió) használata az Azure monitorban

Amikor egy élő probléma, illetve leállás történik, való megfelelés gyors megállapítása az alapvető ok, kritikus fontosságú. Standard figyelési megoldások előfordulhat, hogy riasztást küld, a problémát. Előfordulhat, hogy még jelzik összetevő nem működik. Azonban ez a riasztás nem mindig azonnal ismertetik a hiba okát. Biztos, hogy a hely működött öt perccel ezelőtt történt, és most már rendelkezik a megszakadt. Mi változott, az utolsó öt perc alatt? Ez az a kérdés, amely Alkalmazáselemzés módosítása az Azure monitorban választ.

Kialakításához hatékonyságát a [Azure Erőforrás-grafikon](https://docs.microsoft.com/azure/governance/resource-graph/overview), módosítás elemzés révén betekintést kaphat az Azure-alkalmazás módosítások observability növelheti és csökkentheti az MTTR (kijavításához középidős).

> [!IMPORTANT]
> Változás elemzési jelenleg előzetes verzióban érhető el. Ez az előnézeti verzió nélkül egy szolgáltatásiszint-szerződést biztosítunk. Ez a verzió nem ajánlott éles számítási feladatok esetében. Egyes funkciói esetleg nem támogatott, vagy előfordulhat, hogy korlátozott képességekkel. További információkért lásd: [a Microsoft Azure előzetesekre vonatkozó kiegészítő feltételek](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Áttekintés

Változás Analysis észleli a módosításokat, egészen az alkalmazás központi telepítése az infrastruktúra-rétegből különböző típusú. Előfizetés-szintű Azure erőforrás-szolgáltató, amely ellenőrzi az előfizetés erőforrás-módosítások. Változás Analysis biztosít különböző diagnosztikai eszközök segítségnyújtás a felhasználóknak megérteni, milyen módosítások adatait okozott problémákat.

A következő ábra szemlélteti a módosítás analitikai architektúra:

![Hogyan módosítása Analysis lekérdezi az adatok módosítása, és átadja ügyféleszközök architektúra ábrája](./media/change-analysis/overview.png)

Jelenleg módosítása elemzési integrálva van a **diagnosztizálása és a problémák megoldásához** élmény az App Service web app alkalmazásban. Módosítás engedélyezése és a webalkalmazást a módosítások megtekintéséhez lásd: a *elemzési módosítsa a Web Apps szolgáltatásának* Ez a cikk későbbi szakaszában talál.

### <a name="azure-resource-manager-deployment-changes"></a>Az Azure Resource Manager-környezet módosítására

Használatával [Azure Erőforrás-grafikon](https://docs.microsoft.com/azure/governance/resource-graph/overview), módosítás elemzési biztosít egy korábbi rekordot az Azure-erőforrások, amelyek az alkalmazás hogy hogyan változnak idővel. Például az IP-konfigurációs szabályok, a felügyelt identitásokból és SSL-beállítások módosítása elemzés képes észlelni, módosítja. Tehát egy webalkalmazást egy címkét ad hozzá, ha elemzési módosítása megjelenik a módosítás. Ez az információ érhető el, amíg a `Microsoft.ChangeAnalysis` erőforrás-szolgáltató engedélyezve van az Azure-előfizetésében.

### <a name="changes-in-web-app-deployment-and-configuration"></a>Webes alkalmazás üzembe helyezése és konfigurációs változásai

Változás elemzési 4 óránként alkalmazás telepítési és konfigurációs állapotát rögzíti. Azt észleli, például az alkalmazás környezeti változókat a módosításokat. Az eszköz kiszámítja a különbségeket, és megadja, hogy mi változott. Ellentétben a Resource Manager-módosítások kód telepítési adatokat nem feltétlenül azonnal elérhető az eszköz. Változás-elemzés a legutóbbi módosítások megtekintéséhez jelölje ki **vizsgálat most módosítja**.

![Képernyőkép az "Ellenőrzés változik most" gomb](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Függőségi módosítások

Erőforrás-függőségek módosításai problémákat webalkalmazásban okozhat. Ha a webalkalmazás, Redis Cache-gyorsítótárhoz, például a Redis cache Termékváltozat hatással lehetnek a webes alkalmazás teljesítményét. Módosítás elemzése a függőségek észleli a módosításokat, ellenőrzi, a webalkalmazás DNS-rekord. Így a módosítások az alkalmazás összes összetevőjét, amelyek problémákat okozhatnak azonosítja.

## <a name="change-analysis-for-the-web-apps-feature"></a>A Web Apps szolgáltatásának elemzési módosítása

Az Azure monitorban módosítása elemzési jelenleg be van építve az önkiszolgáló **diagnosztizálása és a problémák megoldásához** tapasztalható. Ez a tapasztalat, a hozzáférés a **áttekintése** App Service-alkalmazás lapján.

![Képernyőkép az "Áttekintés" gomb és az "diagnosztizálása és a problémák megoldása" gombra](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Változás elemzéseket végezhet diagnosztizálása engedélyezése és eszközzel kapcsolatos problémák megoldásához

1. Válassza ki **rendelkezésre állás és teljesítmény**.

    ![A "rendelkezésre állás és teljesítmény" hibaelhárítási lehetőségek képernyőképe](./media/change-analysis/availability-and-performance.png)

1. Válassza ki **alkalmazások módosítására**. Nem, amely a szolgáltatás is elérhető **alkalmazás összeomlik**.

   ![Képernyőkép az "Alkalmazás összeomlik" gomb](./media/change-analysis/application-changes.png)

1. Ahhoz, hogy a módosítás elemzés, válassza ki **engedélyezés**.

   ![Képernyőkép a beállítások "Alkalmazás összeomlik"](./media/change-analysis/enable-changeanalysis.png)

1. Kapcsolja be a **módosítása elemzési** válassza **mentése**.

    ![A "Change elemzése engedélyezése" felhasználói felület képernyőképe](./media/change-analysis/change-analysis-on.png)


1. Változás elemzési eléréséhez, válassza **diagnosztizálása és a problémák megoldásához** > **rendelkezésre állás és teljesítmény** > **alkalmazás összeomlik**. Ekkor megjelenik egy grafikont is tartalmaz, ezeket a módosításokat az idő múlásával módosításainak összegzi:

     ![A módosítás diff nézet képernyőképe](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>Nagy mennyiségű változás elemzés engedélyezése

Ha az előfizetés számos webes alkalmazásokat tartalmazza, nem elég hatékony engedélyezni a szolgáltatást, a web app szintjén lenne. Ebben az esetben kövesse ezeket az alternatív utasításokat.

### <a name="register-the-change-analysis-resource-provider-for-your-subscription"></a>Az előfizetéshez tartozó módosítási elemzési erőforrás-szolgáltató regisztrálása

1. Regisztrálni a módosítási elemzési szolgáltatás jelző (előzetes verzió). Mivel a funkció jelölője előzetes verzióban érhető el, akkor láthatóvá az előfizetéshez regisztrálnia kell:

   1. Nyissa meg az [Azure Cloud Shellt](https://azure.microsoft.com/features/cloud-shell/).

      ![Képernyőfelvétel a Cloud Shell módosítása](./media/change-analysis/cloud-shell.png)

   1. Módosítsa a rendszerhéj típusát **PowerShell**.

      ![Képernyőfelvétel a Cloud Shell módosítása](./media/change-analysis/choose-powershell.png)

   1. Futtassa az alábbi PowerShell-parancsot:

        ``` PowerShell
        Set-AzContext -Subscription <your_subscription_id> #set script execution context to the subscription you are trying to enable
        Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.ChangeAnalysis" -ListAvailable #Check for feature flag availability
        Register-AzureRmProviderFeature -FeatureName PreviewAccess -ProviderNamespace Microsoft.ChangeAnalysis #Register feature flag
        ```

1. A módosítás elemzési az előfizetéshez tartozó erőforrás-szolgáltató regisztrálása.

   - Lépjen a **előfizetések**, és válassza ki az előfizetést, a módosítás szolgáltatásban engedélyezni kívánja. Ezután válassza ki az erőforrás-szolgáltatók:

        ![Képernyőfelvétel: hogyan kell a változás elemzési erőforrás-szolgáltató regisztrálása](./media/change-analysis/register-rp.png)

       - Válassza ki **Microsoft.ChangeAnalysis**. Ezután a lap tetején válassza **regisztrálása**.

       - Az erőforrás-szolgáltató engedélyezése után beállíthatja a rejtett címke észleli a módosítást az üzembe helyezés a webalkalmazásban. Beállíthat egy rejtett címkét, kövesse az utasításokat a **nem sikerült beolvasni a módosítás elemzési adatokat**.

   - Egy PowerShell-parancsprogram segítségével azt is megteheti, az erőforrás-szolgáltató regisztrálásához:

        ```PowerShell
        Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState #Check if RP is ready for registration

        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis" #Register the Change Analysis RP
        ```

        A PowerShell használatával beállíthat egy rejtett címkét egy webalkalmazás, futtassa a következő parancsot:

        ```powershell
        $webapp=Get-AzWebApp -Name <name_of_your_webapp>
        $tags = $webapp.Tags
        $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
        Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
        ```

     > [!NOTE]
     > Miután hozzáadta a rejtett címke, előfordulhat, hogy továbbra is szeretné várjon legfeljebb 4 óra, mielőtt elkezdené a változások jelent meg. Eredmények késnek, mivel változás elemzési csak 4 óránként ellenőrzi a webes alkalmazás. A 4 óra ütemezés korlátozza a vizsgálat teljesítményre gyakorolt hatás.

## <a name="next-steps"></a>További lépések

- App Service-ben figyelése, hatékonyabban [Application Insights-szolgáltatások engedélyezését](azure-web-apps.md) az Azure monitorban.
- Tudjon meg többet [Azure Erőforrás-grafikon](https://docs.microsoft.com/azure/governance/resource-graph/overview), amely segítséget nyújt változás elemzés power.
