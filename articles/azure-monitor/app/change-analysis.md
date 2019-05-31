---
title: Alkalmazás figyelése az Azure analysis - módosítása felderítése, amely befolyásolhatja az élő webhelyet kapcsolatos problémák/valamilyen okból kimaradás lép az Azure Monitor alkalmazással megváltoztatása elemzés |} A Microsoft Docs
description: Alkalmazás élő webhely hibáinak elhárítása az Azure App Services szolgáltatásban az Azure Monitor alkalmazáselemzés módosítása
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: 5bd3816e65398283de85b4551a137b3f97db4cc7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226336"
---
# <a name="application-change-analysis-public-preview"></a>Alkalmazásváltozási elemzés (előzetes verzió)

Amikor egy élő webhely probléma/leállás esetén, kritikus fontosságú alapvető okának gyors meghatározása. Figyelési megoldások standard segítenek gyorsan azonosítani, hogy probléma van, és gyakran még összetevő nem működik. De ez nem mindig vezethet az, hogy miért jelentkezik a hiba azonnali magyarázatot. A hely öt perccel ezelőtt történt, dolgoztam, most már rendelkezik a megszakadt. Mi változott, az utolsó öt perc alatt? Ez az a kérdés, hogy az új funkció módosítás alkalmazáselemzés úgy tervezték, hogy válaszoljon az Azure Monitor. Hatékonyságát építve a [Azure Erőforrás-grafikon](https://docs.microsoft.com/azure/governance/resource-graph/overview) alkalmazáselemzés módosítása az Azure-alkalmazás módosítások observability növelheti és csökkentheti az MTTR (átlagos idő a javítási) betekintést nyújt.

> [!IMPORTANT]
> Az Azure Monitor módosítás alkalmazáselemzés jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview-of-change-analysis-service"></a>Változás elemzési szolgáltatás áttekintése
A módosítás elemzési szolgáltatás észleli a különböző típusú módosításokat infrastruktúra-rétegből egészen az alkalmazás központi telepítését. Egy előfizetési szintű Azure erőforrás-szolgáltató be az előfizetés erőforrás-módosítások megjelenését és nyújt a különböző diagnosztikai eszközöket segítségnyújtás a felhasználóknak megérteni, milyen módosítások adatait okozott problémákat.

A következő ábra szemlélteti a módosítás elemzési szolgáltatás architektúrája: ![Hogyan szerzi be a módosítás analysis service architektúra diagramja módosítani az adatokat, és adja meg az ügyféleszközök elől az adatokat](./media/change-analysis/overview.png)

Jelenleg az eszköz integrálva van az az App Services, web app diagnosztizálhatja és élmény problémák megoldásában. Lásd: *App Services-webalkalmazás módosítása elemzési szolgáltatás* engedélyezése és megtekintése a webalkalmazás módosításait szakaszán.

### <a name="azure-resource-manager-deployment-changes"></a>Az Azure Resource Manager-környezet módosítására
Kihasználva [Azure Erőforrás-grafikon](https://docs.microsoft.com/azure/governance/resource-graph/overview) a módosítás elemző eszközt biztosít egy korábbi rekordot az Azure-erőforrások, amelyek az alkalmazás hogy hogyan változnak idővel. Például ha egy webalkalmazás hozzáadja egy címkét, a módosítás fog szerepelni a módosítás elemző eszközt.
Ezt az információt, mindig kéznél lesznek, amíg a `Microsoft.ChangeAnalysis` erőforrás-szolgáltató előkészítve az Azure-előfizetést.

### <a name="web-application-deployment-and-configuration-changes"></a>Webes alkalmazás központi telepítési és konfigurációs módosítások
Változás-elemző eszköz számítási különbségeket, és jelenleg, mi változott a 4 óránként alkalmazás telepítési és konfigurációs állapotát rögzíti. Az ilyen változások közé Alkalmazásmódosítások környezeti változó, IP-konfiguráció szabály módosításainak, Felügyeltszolgáltatás-identitás módosításokat, SSL-beállítások módosításait, és így tovább.
Resource Manager-módosítások, eltérően az ilyen típusú adatokat nem lehet az eszköz azonnal elérhető. A legutóbbi módosítások megtekintéséhez használja a "Vizsgálat változik most" gomb az eszközben.

![Képernyőkép a módosítások vizsgálat most diagnosztizálása gombra, és megoldani a problémákat eszköz módosítása elemzés integrációjával az app service web appsban](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Függőségi módosítások
Függőségek erőforrás is lehet a problémák okát. Például ha egy webalkalmazás, Redis Cache-gyorsítótárhoz, a webes alkalmazás teljesítményét is negatív hatással lehet a Redis cache Termékváltozat. A web app DNS-rekord vajon módosítást analysis service is megjeleníti a függőségek az adatokat, hogy a problémákat okozott összes összetevőjét bekövetkezett változásokat.


## <a name="change-analysis-service-for-app-services-web-app"></a>Elemzési szolgáltatás az App Services-webalkalmazás módosítása

Az Azure Monitor módosítás alkalmazáselemzés jelenleg beépített az önkiszolgáló **diagnosztizálása és a problémák megoldásához** észlel, amely elérhető a **áttekintése** szakaszban az Azure App Service alkalmazás:

![Képernyőkép az Azure App Service áttekintése piros mezők körül Áttekintés gombra a lap és diagnosztizálása és megoldani a problémákat gomb](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-diagnose-and-solve-problems-tool"></a>Módosítsa a diagnosztikai elemzéseket és eszközzel kapcsolatos problémák megoldásához engedélyezése

1. Válassza ki **rendelkezésre állás és teljesítmény**

    ![Képernyőkép a rendelkezésre állási és hibaelhárítási lehetőségek](./media/change-analysis/availability-and-performance.png)

2. Kattintson a **alkalmazás összeomlik** csempére.

   ![Az alkalmazás összeomlik csempe képernyőképe](./media/change-analysis/application-crashes-tile.png)

3. Ahhoz, hogy **módosítása Analysis** válassza **engedélyezés**.

   ![Képernyőkép a rendelkezésre állási és hibaelhárítási lehetőségek](./media/change-analysis/application-crashes.png)

4. Érvénybe a teljes mértékben kihasználhatja elemzési funkciókat csoport módosítása **módosítása elemzési**, **kód módosításait keressen**, és **mindig a** való **a** Válassza ki **mentése**.

    ![Képernyőkép az Azure App Service engedélyezése a felhasználói felületen módosítása](./media/change-analysis/change-analysis-on.png)

    Ha **módosítása elemzési** van engedélyezve, akkor lesz képes észlelni az erőforrás-szolgáltatói módosítások. Ha **kód módosításait keressen** van engedélyezve, is meg üzembe helyezési fájlokat és Helykonfigurációk változásait. Engedélyezés **mindig** optimalizálja a módosítás teljesítményének vizsgálata, de további költségekkel járhat számlázási szempontból.

5.  Minden engedélyezését követően kiválasztásával **diagnosztizálása és a problémák megoldásában** > **rendelkezésre állás és teljesítmény** > **alkalmazás összeomlik** lehetővé teszi a módosítás elemzési élmény érhető el. A gráf tartalmazza ezeket a módosításokat a részleteivel együtt idővel történt változások típusa:

     ![Képernyőkép a módosítása diff megtekintése](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-service-at-scale"></a>Nagy mennyiségű változás elemzési szolgáltatás engedélyezése
Ha webalkalmazások sok az előfizetésében, engedélyezze a szolgáltatást, webes alkalmazás szint nem elég hatékony lesz. Az alábbiakban néhány alternatív előkészítéséről szóló útmutatást.

#### <a name="registering-change-analysis-resource-provider-for-your-subscription"></a>Az előfizetéshez tartozó módosítási elemzési erőforrás-szolgáltató regisztrálása

1. Változás elemzés előzetes funkció jelölője regisztrálása

    Mivel ez a funkció előzetes verzióban érhető el, a szolgáltatás jelző ahhoz, hogy az előfizetés számára megjelenített először regisztrálnia kell.
    - Nyissa meg az [Azure Cloud Shellt](https://azure.microsoft.com/features/cloud-shell/).

    ![Képernyőkép az Azure Cloud Shell módosítása](./media/change-analysis/cloud-shell.png)

    - A rendszerhéj típusának módosítása a Powershellbe:

    ![Képernyőkép az Azure Cloud Shell módosítása](./media/change-analysis/choose-powershell.png)

    - Futtassa az alábbi PowerShell-parancsot:

    ``` PowerShell

    Set-AzContext -Subscription <your_subscription_id> #set script execution context to the subscription you are trying to onboard
    Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.ChangeAnalysis" -ListAvailable #Check for feature flag availability
    Register-AzureRmProviderFeature -FeatureName PreviewAccess -ProviderNamespace Microsoft.ChangeAnalysis #Register feature flag

    ```

2. Az előfizetés módosítása elemzési erőforrás-szolgáltató regisztrálása

    - Keresse meg az előfizetések, válassza ki a kívánt előfizetést, készítse elő a módosítás szolgáltatást, majd kattintson az erőforrás-szolgáltatók:

        ![Előfizetések panelen módosítsa elemzési RP regisztrálásához képernyőképe](./media/change-analysis/register-rp.png)

    - Válassza ki *Microsoft.ChangeAnalysis* kattintson *regisztrálása* elemre a lap tetején.

    - Ha az erőforrás-szolgáltató már előkészítve, kövesse az utasításokat a *nem sikerült beolvasni a módosítása elemzési adatokat* alábbi rejtett címke beállítása a webalkalmazást a központi telepítés engedélyezése a szint észlelés módosításához a webalkalmazásban.

3. Alternatív megoldásként a fenti. 2. lépés, regisztrálhat a PowerShell-szkripttel nevezhetnek az erőforrás-szolgáltató:

    ```PowerShell
    Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState #Check if RP is ready for registration

    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis" #Register the Change Analysis RP
    ```

4. Rejtett címke beállítása a PowerShell használatával egy webalkalmazás, futtassa a következő parancsot:

    ```powershell
    $webapp=Get-AzWebApp -Name <name_of_your_webapp>
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
    ```

> [!NOTE]
> Miután hozzáadta a rejtett címke, előfordulhat, hogy továbbra is szeretné kezdetben várjon legfeljebb 4 óra első megtekintheti a módosításokat. A 4 óra freqeuncy, hogy a módosítás analysis service használatával ellenőrizheti a webalkalmazás teljesítményére gyakorolt hatásának a vizsgálat eközben okozza.

## <a name="next-steps"></a>További lépések

- Javíthatja az Azure App Services monitoringja [az Application Insights-funkciók engedélyezésével](azure-web-apps.md) Azure monitor.
- Elmélyítse a a [Azure Erőforrás-grafikon](https://docs.microsoft.com/azure/governance/resource-graph/overview) elemzési módosítása power Azure Monitor alkalmazás segítségével.
