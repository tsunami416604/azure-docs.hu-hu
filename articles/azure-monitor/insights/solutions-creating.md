---
title: Felügyeleti megoldás létrehozása az Azure-ban |} A Microsoft Docs
description: Felügyeleti megoldások közé tartoznak a csomagolt felügyeleti forgatókönyvek az Azure-ügyfeleket adhat hozzá a Log Analytics-munkaterületet.  Ez a cikk részletesen hogyan használható a saját környezetben felügyeleti megoldásokat hozhat létre, vagy szeretné elérhetővé tenni az ügyfelek számára.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 24ffe9eded908c82750ce5a6d6fbe87991bdae72
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52872848"
---
# <a name="design-and-build-a-management-solution-in-azure-preview"></a>Megtervezik és megvalósítják a felügyeleti megoldás az Azure-ban (előzetes verzió)
> [!NOTE]
> Ez a felügyeleti megoldások létrehozásához az Azure-ban, jelenleg előzetes verzióban érhető el előzetes dokumentációjában talál. Semmilyen sémát, az alábbiakban a változhat.

[Felügyeleti megoldások]( solutions.md) adja meg a csomagolt felügyeleti forgatókönyvek, amelyek az ügyfelek adhat hozzá a Log Analytics-munkaterületet.  Ez a cikk egy alapszintű folyamatot megtervezni és létrehozni egy felügyeleti megoldás, amely lehetővé teszi a leggyakoribb követelményekre mutat be.  Ha most ismerkedik a felügyeleti megoldások fejlesztése, akkor használja ezt a folyamatot a kiindulási pontként, és akkor támaszkodjon az összetett megoldások, a követelményeinek változását követve fogalmakat.

## <a name="what-is-a-management-solution"></a>Mi az felügyeleti megoldás?

Felügyeleti megoldások Azure-erőforrások, amelyek együttműködve érhet el egy adott felügyeleti forgatókönyvet tartalmaz.  Ezek vannak implementálva [Resource Management-sablonokat](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md) bemutatja, hogyan telepítheti és konfigurálhatja a benne foglalt erőforrásokat a megoldás telepítésekor, amely tartalmazza.

Az alapszintű stratégia, hogy indítsa el a felügyeleti megoldás az Azure-környezetben az egyes összetevők létrehozásával.  Ha már rendelkezik az a funkciók működését, megkezdheti a csomagolás őket egy [felügyeleti megoldásfájlt]( solutions-solution-file.md). 


## <a name="design-your-solution"></a>A megoldás tervezése
A leggyakrabban használt minta egy felügyeleti megoldás az alábbi ábrán látható.  Ebben a mintában a különböző összetevők ismertetik az alább.

![Megoldás áttekintése](media/solutions-creating/solution-overview.png)


### <a name="data-sources"></a>Adatforrások
A megoldás tervezésének első lépése annak meghatározása, az adatokat, amelyekre szüksége van a Log Analytics-adattárból.  Előfordulhat, hogy ezeket az adatokat gyűjti össze a [adatforrás](../../azure-monitor/platform/agent-data-sources.md) vagy [egy másik megoldás]( solutions.md), vagy a megoldás lehet, hogy kell adnia azt gyűjti.

Adatforrások leírtak szerint a Log Analytics-adattárban gyűjtött számos módon vannak [adatforrásokat a Log Analytics](../../azure-monitor/platform/agent-data-sources.md).  Ez magában foglalja a Windows eseménynaplóban az események vagy Syslog által generált teljesítményszámlálók mellett a Windows- és Linux-ügyfelek.  Az Azure-erőforrások Azure Monitor által gyűjtött is gyűjthet adatokat.  

Nem minden elérhető adatforrások-n keresztül elérhető adatok van szüksége, akkor használhatja a [HTTP-adatgyűjtő API](../../azure-monitor/platform/data-collector-api.md) így a használatával írhat adatokat a Log Analytics-tárházba bármely ügyfélnek, amely segítségével meghívhatja a REST API-t.  A leggyakoribb azt jelenti, hogy egyéni adatgyűjtés felügyeleti megoldás az, hogy hozzon létre egy [az Azure Automation runbook](../../automation/automation-runbook-types.md) , amely a szükséges adatokat gyűjti össze az Azure- vagy külső erőforrásokat, és az adatgyűjtő API segítségével írni a adattár.  

### <a name="log-searches"></a>Naplókeresések
[Naplókeresések](../../azure-monitor/log-query/log-query-overview.md) kibontása és elemzése a Log Analytics-adattárban lévő adatok használhatók.  Ezek a nézetek és riasztások mellett lehetővé teszi a felhasználónak az ad hoc elemzést az adatok a tárházban szolgálnak.  

Meg kell határozni, hogy úgy gondolja, hogy akkor is hasznos lehet a felhasználónak, akkor is, ha nem használta azokat bármilyen nézeteket és riasztásokat lekérdezéseket.  Ezek őket a mentett keresések, a portálon elérhető lesz, és is hozzáadhatja őket egy [lista a lekérdezés vizualizációs rész](../../azure-monitor/platform/view-designer-parts.md#list-of-queries-part) az egyéni nézetben.

### <a name="alerts"></a>Riasztások
[Riasztások a Log Analyticsben](../../monitoring-and-diagnostics/monitoring-overview-alerts.md) azonosíthatja a problémákat keresztül [naplókereséseket](#log-searches) az adatokban a tárházban.  Vagy a felhasználó értesítése, vagy automatikusan válaszként futtatni a műveletet. Azonosítsa az alkalmazás különböző riasztási feltételeket kell és adathordozófájlba felvenni a megoldás megfelelő riasztási szabályok.

Ha a probléma esetleg egy automatizált folyamattal javítani kell, majd általában létrehozhat egy runbook az Azure Automationben a szervizelés végrehajtásához.  A legtöbb Azure-szolgáltatások kezelhetők [parancsmagok](/powershell/azure/overview) , amely a runbook szeretné kihasználni a funkció végrehajtásához.

Ha a megoldáshoz szükséges külső funkciók egy riasztásra adott válaszként, akkor használhat egy [webhook válasza](../../monitoring-and-diagnostics/alert-metric.md).  Ez lehetővé teszi, hogy egy külső webes szolgáltatás adatokat küld a riasztásokból meghívható.

### <a name="views"></a>Nézetek
A nézetek a Log Analytics segítségével a Log Analytics-adattárban adatainak megjelenítése.  Egyes megoldások általában fogja tartalmazni a szolgáltatással egyetlen nézetben egy [csempe](../../azure-monitor/platform/view-designer-tiles.md) , amely a felhasználó fő irányítópultján jelenik meg.  A nézet tartalmazhat tetszőleges számú [Vizualizáció részek](../../azure-monitor/platform/view-designer-parts.md) különböző megjelenítését tartalmazza az összegyűjtött adatokat biztosít a felhasználó számára.

Ön [Az adatforrásnézet-tervezőből segítségével egyéni nézeteket hozhat létre](../../azure-monitor/platform/view-designer.md) , amelyek később exportálhatja, hogy a megoldás fájlban.  


## <a name="create-solution-file"></a>Megoldás-fájl létrehozása
Miután konfigurált és tesztelt az összetevőket, amelyek a megoldás része lesz, akkor is [hozzon létre egy megoldást fájlt]( solutions-solution-file.md).  A megoldás-összetevőket a megvalósítandó egy [Resource Manager-sablon](../../azure-resource-manager/resource-group-authoring-templates.md) , amely tartalmaz egy [megoldás erőforrás]( solutions-solution-file.md#solution-resource) kapcsolatok egyéb erőforrások a fájlban.  


## <a name="test-your-solution"></a>A megoldás tesztelése
Bár a megoldás fejleszt, telepítse és tesztelje a munkaterületen kell.  Ezt megteheti a rendelkezésre álló módszerek bármelyikével [tesztelése, és telepítse a Resource Manager-sablonok](../../azure-resource-manager/resource-group-template-deploy.md).

## <a name="publish-your-solution"></a>Tegye közzé a megoldását
Befejeződött, és a tesztelt megoldását, akkor is tegye elérhetővé a következő forrásokból ügyfeleket.

- **Az Azure gyorsindítási sablonok**.  [Az Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/) Resource Manager-sablonok a Githubon keresztül Közösség által biztosított készlete.  Elérhetővé teheti a megoldás a következő információkat a [közreműködői útmutató](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE).
- **Az Azure Marketplace**.  A [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/) lehetővé teszi, hogy terjesztése, és értékesítse a megoldását azt más fejlesztők, független szoftverszállítók, és informatikai szakemberek számára.  Megismerheti a megoldás közzététele az Azure piactéren, hogy [hogyan tehet közzé és kezelése az Azure Marketplace-ajánlat](../../marketplace/marketplace-publishers-guide.md).



## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [létrehozza a megoldásfájlt]( solutions-solution-file.md) a felügyeleti megoldás.
* Ismerje meg az adatait [Azure Resource Manager-sablonok készítése](../../azure-resource-manager/resource-group-authoring-templates.md).
* Keresés [Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates) minták a különböző Resource Manager-sablonok.
