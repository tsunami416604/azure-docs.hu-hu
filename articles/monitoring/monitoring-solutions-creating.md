---
title: A felügyeleti megoldás az Azure-ban |} Microsoft Docs
description: Kezelési megoldásai a következők csomagolt felügyeleti lehetőségeket, amelyek az ügyfelek a Naplóelemzési munkaterület adhat hozzá az Azure-ban.  Ez a cikk részletesen hogyan hozhat létre a saját környezetében használható felügyeleti megoldás, vagy szeretné elérhetővé tenni az ügyfelek számára.
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
ms.openlocfilehash: 92089904941ae913f1992a4407083bfcae010f2d
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="design-and-build-a-management-solution-in-azure-preview"></a>Tervezése és készítése olyan felügyeleti megoldást az Azure (előzetes verzió)
> [!NOTE]
> Ez az előzetes dokumentum megoldások létrehozásához Azure, amely jelenleg előzetes verziójúak. Az alábbiakban a séma van változhat.

[Megoldások]( monitoring-solutions.md) adja meg, hogy az ügyfelek adhat hozzá a Naplóelemzési munkaterület csomagolt felügyeleti lehetőségeket.  Ez a cikk egy alapvető folyamat tervezése és a megoldás létrehozása kezelési leggyakoribb követelményeknek megfelelő mutatja be.  Ha most ismerkedik létrehozása kezelési megoldásai, akkor használja ezt a folyamatot a kiindulási pontként, és majd a fogalmakat, a követelmények fejlődnek bonyolultabb megoldások.

## <a name="what-is-a-management-solution"></a>Mi az a felügyeleti megoldás?

Megoldások Azure-erőforrások, amelyek együttműködése egy adott felügyeleti forgatókönyv eléréséhez tartalmaz.  Akkor használják, mint [erőforrás-kezelés sablonok](../azure-resource-manager/resource-manager-template-walkthrough.md) tartalmaznak, telepítése és konfigurálása a benne lévő erőforrások, a megoldás telepítésekor részleteit.

Az alapvető stratégia a felügyeleti megoldás az Azure környezetben az egyes összetevők épület elindításához.  Miután a funkciót, megfelelően működik, majd elindíthatja csomagolására őket egy [felügyeleti megoldást fájl]( monitoring-solutions-solution-file.md). 


## <a name="design-your-solution"></a>A megoldás tervezése
A leggyakrabban használt minta felügyeleti megoldás a következő ábrán látható.  Ebben a mintában a különböző összetevőket ismerteti a alatt.

![Felügyeleti megoldás áttekintése](media/monitoring-solutions-creating/solution-overview.png)


### <a name="data-sources"></a>Adatforrások
A megoldás tervezésének első lépése az adatokat, amelyekre szüksége van a Naplóelemzési tárházból meghatározása.  Ezek az adatok is lehet összegyűjteni a [adatforrás](../log-analytics/log-analytics-data-sources.md) vagy [egy másik megoldás]( monitoring-solutions.md), vagy a megoldás lehet, hogy kell adnia azt gyűjti.

Adatforrások gyűjtendő a Log Analyticshez tárházban leírtak szerint számos módon vannak [Naplóelemzési adatforrások](../log-analytics/log-analytics-data-sources.md).  Az eseményeket is tartalmazza a Windows Eseménynapló, vagy a Syslog által generált teljesítményszámlálók mellett a Windows és Linux-ügyfelek.  Az Azure-erőforrások Azure figyelő által gyűjtött adatokat is gyűjthet.  

Ha van szüksége, amely nem érhető el keresztül a rendelkezésre álló adatforrások bármelyikét adatokat, akkor is használhatja a [HTTP adatait gyűjtője API](../log-analytics/log-analytics-data-collector-api.md) lehetővé teszi a Naplóelemzési tárház minden ügyfélről, amely a REST API meghívása adatokat írni.  A leggyakoribb azt jelenti, hogy egyéni adatgyűjtés olyan felügyeleti megoldást, ha a egy [az Azure Automationben runbook](../automation/automation-runbook-types.md) , amely a szükséges adatokat gyűjti össze az Azure-bA vagy külső erőforrások és a Data Collector API segítségével írni a tárház.  

### <a name="log-searches"></a>Napló-keresések
[A keresések jelentkezzen](../log-analytics/log-analytics-log-searches.md) kibontása és elemezhetik a Log Analyticshez tárházban szolgálnak.  Használják nézeteket és riasztásokat mellett, így a felhasználónak az alkalmi adatelemzési a tárházban.  

Meg kell határozni, hogy akkor is, ha azok nem használt nézeteket és riasztásokat is hasznos lehet a felhasználó lekérdezéseket.  Ezek számukra mentett a portálon elérhető lesz, és is hozzáadhat azokat egy [lista a lekérdezések a képi megjelenítés része](../log-analytics/log-analytics-view-designer-parts.md#list-of-queries-part) az egyéni nézetben.

### <a name="alerts"></a>Riasztások
[Log Analytics riasztások](../log-analytics/log-analytics-alerts.md) keresztül problémák azonosításához [keresések jelentkezzen](#log-searches) a tárházban található adatok alapján.  Vagy a felhasználó értesítése, vagy automatikusan futtasson egy műveletet válaszként. Azonosítsa az alkalmazás különböző riasztási feltételeket kell, és megfelelő riasztási szabályok belefoglalása a megoldásfájl.

Ha a probléma esetleg az egy automatikus folyamat javítani kell, majd lesz általában létrehozta a forgatókönyvet az Azure Automationben a szervizelés.  Az Azure szolgáltatások felügyelhetők a [parancsmagok](/powershell/azure/overview) amely runbook volna mellyel elvégezheti a ilyen funkciót.

Ha a megoldás riasztást válaszul külső funkciókat igényel, akkor is használhatja a [webhook válasz](../log-analytics/log-analytics-alerts-actions.md).  Ez lehetővé teszi, hogy egy üzenetet küld információkat a riasztás külső webszolgáltatás hívására.

### <a name="views"></a>Nézetek
Log Analytics-nézetek segítségével a Naplóelemzési tárházból adatainak megjelenítése.  Egyes megoldások általában fogja tartalmazni az egyetlen nézetben egy [csempe](../log-analytics/log-analytics-view-designer-tiles.md) , amely a felhasználó fő irányítópult jelenik meg.  A nézet tartalmazhat tetszőleges számú [képi megjelenítés részek](../log-analytics/log-analytics-view-designer-parts.md) az összegyűjtött adatok különböző képi biztosításához a felhasználó számára.

Ön [a nézet Designer segítségével egyéni nézeteket hozhat létre](../log-analytics/log-analytics-view-designer.md) , amelyek később exportálhatja, hogy a megoldás fájlban.  


## <a name="create-solution-file"></a>Megoldás fájl létrehozása
Miután konfigurálását és tesztelt lesz a megoldás részét képező összetevőket, [hozzon létre egy megoldást fájlt]( monitoring-solutions-solution-file.md).  A megoldás-összetevő megvalósítandó egy [Resource Manager-sablon](../azure-resource-manager/resource-group-authoring-templates.md) , amely tartalmazza egy [megoldás erőforrás]( monitoring-solutions-solution-file.md#solution-resource) kapcsolatok az egyéb erőforrások a fájlban.  


## <a name="test-your-solution"></a>A megoldás tesztelése
A megoldás fejlesztése, során szüksége lesz telepítéséhez és tesztelheti a munkaterületen.  Ehhez a rendelkezésre álló módszereket [tesztelése és telepítése a Resource Manager-sablonok](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="publish-your-solution"></a>A megoldás közzététele
Miután befejeződött, és a megoldás tesztelése, akkor is tegye elérhetővé az ügyfelek az alábbi forrásokon keresztül.

- **Azure gyors üzembe helyezési sablonokat**.  [Azure gyors üzembe helyezési sablonokat](https://azure.microsoft.com/resources/templates/) Githubon keresztül a Közösség által közzétett erőforrás-kezelő sablonok készlete.  Elérhetővé teheti a megoldás által a következő információkat a [hozzájárulás útmutató](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE).
- **Az Azure piactér**.  A [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/) lehetővé teszi terjesztése és a megoldás eladásra más fejlesztők számára, a független szoftverszállítók, és az informatikai szakemberek számára.  Megismerheti a megoldás közzététele az Azure piactéren: [közzététele és kezelése az Azure piactéren ajánlatot](../marketplace-publishing/marketplace-publishing-getting-started.md).



## <a name="next-steps"></a>További lépések
* Megtudhatja, hogyan [hozzon létre egy megoldást fájlt]( monitoring-solutions-solution-file.md) -kezelési megoldást.
* További részleteit [Azure Resource Manager-sablonok készítése](../azure-resource-manager/resource-group-authoring-templates.md).
* Keresési [Azure gyors üzembe helyezési sablonokat](https://azure.microsoft.com/documentation/templates) példákért különböző Resource Manager-sablonok.