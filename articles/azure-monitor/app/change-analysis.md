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
ms.openlocfilehash: b04bd57c66b52e9a2c14d43c9e89d7c54fc48ae2
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415835"
---
# <a name="azure-monitor-application-change-analysis-public-preview"></a>Az Azure Monitor módosítás alkalmazáselemzés (nyilvános előzetes verzió)

Amikor egy élő webhely probléma/leállás esetén, kritikus fontosságú alapvető okának gyors meghatározása. Figyelési megoldások standard segítenek gyorsan azonosítani, hogy probléma van, és gyakran még összetevő nem működik. De ez nem mindig vezethet az, hogy miért jelentkezik a hiba azonnali magyarázatot. A hely öt perccel ezelőtt történt, dolgoztam, most már rendelkezik a megszakadt. Mi változott, az utolsó öt perc alatt? Ez az a kérdés, hogy az új funkció módosítás alkalmazáselemzés úgy tervezték, hogy válaszoljon az Azure Monitor. Hatékonyságát építve a [Azure Erőforrás-grafikon](https://docs.microsoft.com/azure/governance/resource-graph/overview) alkalmazáselemzés módosítása az Azure App Service alkalmazás módosítások observability növelheti és csökkentheti az MTTR (átlagos idő a javítási) betekintést nyújt.

> [!IMPORTANT]
> Az Azure Monitor módosítás alkalmazáselemzés jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-do-i-use-application-change-analysis"></a>Hogyan használhatom a módosítás alkalmazáselemzés?

Az Azure Monitor módosítás alkalmazáselemzés jelenleg beépített az önkiszolgáló **diagnosztizálása és a problémák megoldásához** észlel, amely elérhető a **áttekintése** szakaszban az Azure App Service alkalmazás:

![Képernyőkép az Azure App Service áttekintése piros mezők körül Áttekintés gombra a lap és diagnosztizálása és megoldani a problémákat gomb](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis"></a>Változás elemzés engedélyezése

1. Válassza ki **rendelkezésre állás és teljesítmény**

    ![Képernyőkép a rendelkezésre állási és hibaelhárítási lehetőségek](./media/change-analysis/availability-and-performance.png)

2. Kattintson a **alkalmazás összeomlik** csempére.

   ![Az alkalmazás összeomlik csempe képernyőképe](./media/change-analysis/application-crashes-tile.png)

3. Ahhoz, hogy **módosítása Analysis** válassza **engedélyezés**.

   ![Képernyőkép a rendelkezésre állási és hibaelhárítási lehetőségek](./media/change-analysis/application-crashes.png)

4. Érvénybe a teljes mértékben kihasználhatja elemzési funkciókat csoport módosítása **módosítása elemzési**, **kód módosításait keressen**, és **mindig a** való **a** Válassza ki **mentése**.

    ![Képernyőkép az Azure App Service engedélyezése a felhasználói felületen módosítása](./media/change-analysis/change-analysis-on.png)

    Ha **módosítása elemzési** van engedélyezve, akkor lesz képes észlelni az erőforrás-szolgáltatói módosítások. Ha **kód módosításait keressen** van engedélyezve, is meg üzembe helyezési fájlokat és Helykonfigurációk változásait. Engedélyezés **mindig** optimalizálja a módosítás teljesítményének vizsgálata, de további költségekkel járhat számlázási szempontból.

5.  Minden engedélyezését követően kiválasztásával **diagnosztizálása és a problémák megoldásában** > **rendelkezésre állás és teljesítmény** > **alkalmazás összeomlik** lehetővé teszi a módosítás elemzési élmény érhető el. A gráf fog summerize részleteivel együtt idővel ezek a módosítások a történt változások típusa:

     ![Képernyőkép a módosítása diff megtekintése](./media/change-analysis/change-view.png)

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="unable-to-fetch-change-analysis-information"></a>Nem sikerült beolvasni a módosítás elemzési adatait.

Ez az az aktuális előzetes előkészítési folyamatot egy ideiglenes probléma. A megoldás a webalkalmazás egy rejtett címke beállítását, és majd frissíteni az oldalt tartalmaz:

   ![Képernyőkép a rejtett módosítása címke](./media/change-analysis/hidden-tag.png)

A PowerShell-lel rejtett címke beállítása:

1. Nyissa meg az Azure Cloud Shell:

    ![Képernyőkép az Azure Cloud Shell módosítása](./media/change-analysis/cloud-shell.png)

2. A rendszerhéj típusának módosítása a Powershellbe:

   ![Képernyőkép az Azure Cloud Shell módosítása](./media/change-analysis/choose-powershell.png)

3. Futtassa a következő parancsot:

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
