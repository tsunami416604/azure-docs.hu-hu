---
title: Felügyeleti megoldás létrehozása az Azure-ban | Microsoft Docs
description: A felügyeleti megoldások közé tartoznak az Azure-ban csomagolt felügyeleti forgatókönyvek, amelyeket az ügyfelek hozzáadhatnak Log Analytics munkaterülethez.  Ez a cikk részletesen ismerteti, hogyan hozhat létre a saját környezetében használt felügyeleti megoldásokat, illetve hogyan teheti elérhetővé az ügyfelek számára.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5465c177cf174ebf8d6b7d4f43c5387bce3adb70
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75969700"
---
# <a name="design-and-build-a-management-solution-in-azure-preview"></a>Felügyeleti megoldás kialakítása és létrehozása az Azure-ban (előzetes verzió)
> [!NOTE]
> Ez az előzetes dokumentáció az Azure-ban jelenleg előzetes verzióban elérhető felügyeleti megoldások létrehozásához. Az alább ismertetett sémák változhatnak.

A [felügyeleti megoldások]( solutions.md) olyan csomagolt felügyeleti forgatókönyveket biztosítanak, amelyeket az ügyfelek hozzáadhatnak log Analytics munkaterülethez.  Ez a cikk egy alapszintű folyamatot mutat be a leggyakoribb követelményekhez megfelelő felügyeleti megoldás kialakításához és kiépítéséhez.  Ha most ismerkedik a felügyeleti megoldások létrehozásával, akkor ezt a folyamatot kiindulási pontként használhatja, majd kihasználhatja a fogalmakat, amelyekkel összetettebb megoldásokat hozhat létre.

## <a name="what-is-a-management-solution"></a>Mi az a felügyeleti megoldás?

A felügyeleti megoldások olyan Azure-erőforrásokat tartalmaznak, amelyek együttműködve egy adott felügyeleti forgatókönyv megvalósítására használhatók.  Olyan [erőforrás-kezelési sablonokként](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) valósulnak meg, amelyek részletesen ismertetik, hogyan kell telepíteni és konfigurálni a tartalmazott erőforrásokat a megoldás telepítésekor.

Az alapszintű stratégia a felügyeleti megoldás elindítása az Azure-környezet egyes összetevőinek létrehozásával.  A funkciók megfelelő működésének megkezdése után megkezdheti a csomagolást egy [felügyeleti megoldás fájljába]( solutions-solution-file.md). 


## <a name="design-your-solution"></a>A megoldás tervezése
A felügyeleti megoldások leggyakoribb mintája az alábbi ábrán látható.  A minta különböző összetevőit az alábbiakban tárgyaljuk.

![Felügyeleti megoldás áttekintése](media/solutions-creating/solution-overview.png)


### <a name="data-sources"></a>Adatforrások
A megoldás kialakításának első lépése a Log Analytics adattárból szükséges adatok meghatározása.  Ezeket az adatokat egy [adatforrás](../../azure-monitor/platform/agent-data-sources.md) vagy [egy másik megoldás]( solutions.md)gyűjtheti, vagy előfordulhat, hogy a megoldásnak meg kell adnia a gyűjtés folyamatát.

Számos módon gyűjthet adatforrásokat a Log Analytics adattárban az [log Analytics adatforrásaiban](../../azure-monitor/platform/agent-data-sources.md)leírtak szerint.  Ez a Windows-eseménynaplóban vagy a syslog által generált eseményeket is magában foglalja a Windows-és Linux-ügyfelekhez tartozó teljesítményszámlálók mellett.  A Azure Monitor által összegyűjtött Azure-erőforrásokból is gyűjthet adatokat.  

Ha olyan adatokra van szüksége, amelyek nem érhetők el az elérhető adatforrások bármelyikén keresztül, akkor használhatja a [http-adatgyűjtő API](../../azure-monitor/platform/data-collector-api.md) -t, amely lehetővé teszi az adatok írását a log Analytics adattárba bármely olyan ügyféltől, amely képes REST API meghívására.  Egy felügyeleti megoldásban az egyéni adatgyűjtés leggyakoribb módja egy olyan runbook létrehozása, amely az Azure-ból vagy külső erőforrásokból származó szükséges adatokat gyűjti [Azure Automation](../../automation/automation-runbook-types.md) , és az adatgyűjtő API-t használja az adattárba való íráshoz.  

### <a name="log-searches"></a>Naplók keresése
A [naplók keresése](../../azure-monitor/log-query/log-query-overview.md) a log Analytics adattárban található adatok kinyerésére és elemzésére szolgál.  Ezeket a nézeteket és riasztásokat a felhasználók is használják, így a felhasználó az adattárban lévő adatok ad hoc elemzését is elvégezheti.  

Meg kell határoznia azokat a lekérdezéseket, amelyeket úgy gondol, hogy a felhasználó számára hasznos lesz, még akkor is, ha azokat nem használja egyetlen nézet vagy riasztás sem.  Ezek a portálon mentett keresésként lesznek elérhetők a portálon, és a [lekérdezések vizualizációs részét](../../azure-monitor/platform/view-designer-parts.md#list-of-queries-part) is felvehetik az egyéni nézetben.

### <a name="alerts"></a>Értesítések
A [log Analytics riasztásai](../../azure-monitor/platform/alerts-overview.md) azonosítják a [naplóbeli keresések](#log-searches) során felmerülő problémákat a tárházban lévő adattárakban.  Vagy értesítik a felhasználót, vagy egy válaszban automatikusan futtatják a műveletet. Meg kell határoznia az alkalmazás különböző riasztási feltételeit, és tartalmaznia kell a megfelelő riasztási szabályokat a megoldás fájljában.

Ha a probléma kijavítása egy automatizált folyamattal lehetséges, akkor általában egy runbook hoz létre Azure Automation a szervizelés végrehajtásához.  A legtöbb Azure-szolgáltatás olyan [parancsmagokkal](/powershell/azure/overview) kezelhető, amelyeket a runbook az ilyen funkciók elvégzésére használhat.

Ha a megoldás külső funkciókat igényel a riasztásokra adott válaszként, akkor használhat [webhook-választ](../../azure-monitor/platform/alerts-metric.md).  Ez lehetővé teszi, hogy meghívjon egy külső webszolgáltatás által küldött adatokat a riasztásból.

### <a name="views"></a>Nézetek
A Log Analytics nézetei a Log Analytics adattárból származó adatok megjelenítésére szolgálnak.  Minden megoldás általában egyetlen nézetet tartalmaz, amely a felhasználó fő irányítópultján megjelenő [csempével](../../azure-monitor/platform/view-designer-tiles.md) fog szerepelni.  A nézet tetszőleges számú [vizualizációs alkatrészt](../../azure-monitor/platform/view-designer-parts.md) tartalmazhat, amelyek különböző vizualizációkat biztosítanak az összegyűjtött adatoknak a felhasználó számára.

[Az egyéni nézeteket a View Designer használatával hozhatja létre](../../azure-monitor/platform/view-designer.md) , amelyet később exportálhat a megoldás fájljába való felvételhez.  


## <a name="create-solution-file"></a>Megoldás fájljának létrehozása
Miután konfigurálta és tesztelte a megoldás részét képező összetevőket, létrehozhat [egy megoldási fájlt]( solutions-solution-file.md).  A megoldás-összetevőket egy [Resource Manager-sablonban](../../azure-resource-manager/templates/template-syntax.md) kell megvalósítani, amely tartalmaz egy [megoldási erőforrást]( solutions-solution-file.md#solution-resource) , amely a fájlban található többi erőforrással való kapcsolattal rendelkezik.  


## <a name="test-your-solution"></a>A megoldás tesztelése
A megoldás fejlesztése során telepítenie és tesztelni kell a munkaterületen.  Ezt bármely elérhető módszer használatával végezheti el a [Resource Manager-sablonok teszteléséhez és telepítéséhez](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="publish-your-solution"></a>A megoldás közzététele
Miután elvégezte és tesztelte a megoldást, az alábbi forrásokon keresztül elérhetővé teheti az ügyfelek számára.

- **Azure Gyorsindítás sablonok**.  Az Azure gyors üzembe helyezési [sablonok](https://azure.microsoft.com/resources/templates/) a Közösség által a githubon keresztül hozzájáruló Resource Manager-sablonok.  A megoldás elérhetővé tételéhez a [hozzájárulási útmutatóban](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE)talál további információt.
- Az **Azure Marketplace**-en.  Az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) lehetővé teszi a megoldás terjesztését és értékesítését más fejlesztőknek, szoftvergyártóknak és informatikai szakembereknek.  Megtudhatja, hogyan teheti közzé megoldásait az Azure Marketplace-en [az ajánlat közzétételéhez és kezeléséhez az Azure piactéren](../../marketplace/marketplace-publishers-guide.md).



## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [hozhat létre megoldást]( solutions-solution-file.md) a felügyeleti megoldáshoz.
* A [Azure Resource Manager-sablonok létrehozási](../../azure-resource-manager/templates/template-syntax.md)részleteinek megismerése.
* Más Resource Manager-sablonokból származó mintákhoz is kereshet Azure-beli [Gyorsindítás sablonokat](https://azure.microsoft.com/documentation/templates) .
