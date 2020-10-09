---
title: Munkafüzetek adatforrások Azure Monitorása | Microsoft docs
description: Egyszerűsítse az összetett jelentéskészítést az előre elkészített és az egyéni paraméterekkel, Azure Monitor több adatforrásból készített munkafüzetekből
services: azure-monitor
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: mbullwin
ms.openlocfilehash: bbd231ca527b4c01509230e839b97187de29febd
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91825738"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Azure Monitor munkafüzetek adatforrásai

A munkafüzetek nagy számú adatforrással kompatibilisek. Ez a cikk végigvezeti a Azure Monitor-munkafüzetek számára jelenleg elérhető adatforrásokon.

## <a name="logs"></a>Naplók

A munkafüzetek lehetővé teszik a naplók lekérdezését a következő forrásokból:

* Azure Monitor naplók (Application Insights erőforrások és Log Analytics munkaterületek.)
* Erőforrás-központú adatforrások (tevékenység-naplók)

A munkafüzet szerzője olyan KQL-lekérdezéseket használhat, amelyek átalakítják a mögöttes erőforrás-adatokat olyan eredményhalmaz kiválasztásához, amely szövegként, diagramként vagy rácsként is megjeleníthető.

![Képernyőfelvétel a munkafüzetekről a jelentés felületéről](./media/workbooks-overview/logs.png)

A munkafüzet szerzője könnyedén lekérdezheti a több erőforrást egy valóban egységes, gazdag jelentéskészítési felület létrehozásával.

## <a name="metrics"></a>Mérőszámok

Az Azure-erőforrások olyan [metrikákat](data-platform-metrics.md) bocsátanak ki, amelyek munkafüzetek használatával érhetők el. A metrikák egy speciális vezérlőn keresztül érhetők el a munkafüzetekben, így megadhatja a cél erőforrásokat, a kívánt mérőszámokat és azok összesítését. Ezeket az adatsorokat diagramokban vagy rácsokban lehet ábrázolni.

![Képernyőfelvétel a CPU-kihasználtság munkafüzet-metrikáinak diagramjairól](./media/workbooks-overview/metrics-graph.png)

![Képernyőfelvétel a munkafüzet metrikái felületéről](./media/workbooks-overview/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph

A munkafüzetek támogatják az erőforrások és a metaadatok lekérdezését az Azure Resource Graph (ARG) használatával. Ez a funkció elsősorban a jelentések egyéni lekérdezési hatókörének létrehozásához használatos. Az erőforrás hatóköre az ARG által támogatott KQL-részhalmazon keresztül van kifejezve – ami gyakran elegendő a gyakori használati esetekhez.

Ha szeretné, hogy egy lekérdezés vezérlőelem használja ezt az adatforrást, használja a lekérdezés típusa legördülő listát az Azure Resource Graph kiválasztásához, és válassza ki a célozni kívánt előfizetéseket. A lekérdezés vezérlőelem használatával adja hozzá az ARG KQL-részhalmazt, amely egy érdekes erőforrás-részhalmazt választ ki.

![Képernyőkép az Azure Resource Graph KQL-lekérdezésről](./media/workbooks-overview/azure-resource-graph.png)

## <a name="azure-resource-manager"></a>Azure Resource Manager

A munkafüzet támogatja a Azure Resource Manager REST-műveleteket. Ez lehetővé teszi a management.azure.com-végpont lekérdezését anélkül, hogy meg kellene adnia a saját hitelesítési fejlécének tokenjét.

Ha szeretné, hogy egy lekérdezés vezérlőelem használja ezt az adatforrást, az adatforrás legördülő menüben válassza a Azure Resource Manager lehetőséget. Adja meg a megfelelő paramétereket, például http-metódust, URL-útvonalat, fejléceket, URL-paramétereket és/vagy törzset.

> [!NOTE]
> Jelenleg csak a, `GET` `POST` , és `HEAD` műveletek támogatottak.

## <a name="azure-data-explorer"></a>Azure Data Explorer

A munkafüzetek mostantól támogatják az [Azure adatkezelő](/azure/data-explorer/) -fürtökről való lekérdezést a hatékony [Kusto](/azure/kusto/query/index) -lekérdezési nyelvvel.   

![Képernyőfelvétel a Kusto-lekérdezési ablakról](./media/workbooks-overview/data-explorer.png)

## <a name="workload-health"></a>Munkaterhelés állapota

Azure Monitor rendelkezik olyan funkciókkal, amelyek proaktív módon figyelik a Windows-vagy Linux-alapú vendég operációs rendszerek rendelkezésre állását és teljesítményét. Azure Monitor a modellek kulcs-összetevőit és azok kapcsolatait, az összetevők állapotának mérésének feltételeit, valamint azt, hogy mely összetevők riasztást küldenek, ha a rendszer nem kifogástalan állapotot észlel. A munkafüzetek lehetővé teszik, hogy a felhasználók részletes interaktív jelentéseket hozzanak létre az adatok használatával.

Ha szeretné, hogy a lekérdezés vezérlőelem ezt az adatforrást használja, a **lekérdezés típusa** legördülő menüből válassza a munkaterhelés állapota lehetőséget, majd válassza az előfizetés, az erőforráscsoport vagy a virtuálisgép-erőforrások célhelyre lehetőséget. Az állapot-szűrő legördülő lista használatával kiválaszthatja az elemzési igényeknek megfelelő egészségügyi incidensek érdekes részhalmazát.

![A riasztások lekérdezésének képernyőképe](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Azure-erőforrás állapota

A munkafüzetek lehetővé teszik az Azure Resource Health beszerzését és más adatforrásokkal való összekapcsolását, hogy gazdag, interaktív állapotjelentést hozzanak létre.

Ha szeretné, hogy egy lekérdezés vezérlőelem használja ezt az adatforrást, a **lekérdezés típusa** legördülő listából válassza ki az Azure Health elemet, és válassza ki a célként használni kívánt erőforrásokat. Az állapot-szűrő legördülő lista használatával kiválaszthatja az elemzési igényekhez tartozó erőforrás-problémák érdekes részét.

![A riasztások lekérdezésének képernyőképe](./media/workbooks-overview/resource-health.png)

## <a name="json"></a>JSON

A JSON-szolgáltató lehetővé teszi a statikus JSON-tartalmak lekérdezési eredményének létrehozását. A paraméterek leggyakrabban a statikus értékek legördülő paramétereinek létrehozásához használatosak. A rendszer automatikusan átalakítja az egyszerű JSON-tömböket vagy-objektumokat a rács soraiba és oszlopaiba.  A további specifikus viselkedés érdekében a Results (eredmények) és a JSONPath beállítások segítségével konfigurálhatja az oszlopokat.

## <a name="alerts-preview"></a>Riasztások (előzetes verzió)

> [!NOTE]
> Az Azure-riasztási adatok lekérdezésének javasolt módja az [Azure Resource Graph](#azure-resource-graph) -adatforrás használata a tábla lekérdezésével `AlertsManagementResources` .
>
> Tekintse meg az [Azure Resource Graph-táblázat referenciáját](../../governance/resource-graph/reference/supported-tables-resources.md), vagy példákat a [riasztások sablonra](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Azure%20Resources/Alerts/Alerts.workbook) .
>
> A riasztások adatforrás egy ideig elérhető marad, míg a szerzők az ARG használatára váltanak. Az adatforrás használata a sablonokban nem ajánlott. 

A munkafüzetek lehetővé teszik a felhasználók számára, hogy megjelenítsék az erőforrásaikkal kapcsolatos aktív riasztásokat. Korlátozások: a riasztások adatforrásának olvasási hozzáféréssel kell rendelkeznie az előfizetéshez az erőforrások lekérdezése érdekében, és előfordulhat, hogy nem jeleníti meg az újabb típusú riasztásokat. 

Ha szeretné, hogy a lekérdezés vezérlőelem használja ezt az adatforrást, az _adatforrás_ legördülő menüben válassza a _riasztások (előzetes verzió)_ lehetőséget, majd válassza ki a célként használandó előfizetéseket, erőforráscsoportokat vagy erőforrásokat. A riasztási szűrő legördülő lista használatával kiválaszthatja az elemzési igényekhez tartozó riasztások érdekes részhalmazát.

## <a name="custom-endpoint"></a>Egyéni végpont

A munkafüzetek bármilyen külső forrásból származó adatok beolvasását támogatják. Ha az adatai az Azure-on kívül vannak, akkor ez az adatforrás-típus használatával a munkafüzetekbe is helyezhető.

Ha szeretné, hogy egy lekérdezés vezérlőelem használja ezt az adatforrást, az _adatforrás_ legördülő menüjéből válassza az _Egyéni végpont_lehetőséget. Adja meg a megfelelő paramétereket, például,, `Http method` `url` `headers` `url parameters` és/vagy `body` . Győződjön meg arról, hogy az adatforrás támogatja a [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) , máskülönben a kérelem sikertelen lesz.

Ha nem szeretne automatikusan hívásokat kezdeményezni a nem megbízható gazdagépekhez sablonok használatakor, a felhasználónak megbízhatóként kell megjelölni a használt gazdagépeket. Ezt úgy teheti meg, hogy a _Hozzáadás megbízhatóként_ gombra kattint, vagy ha megbízható gazdagépként hozzáadja a munkafüzet-beállításokhoz. Ezek a beállítások a webes feldolgozók IndexDb támogató böngészőkben lesznek mentve. További információ [itt](https://caniuse.com/#feat=indexeddb)található.

> [!NOTE]
> Ne írjon semmilyen titkot a következő mezők egyikéhez sem (,, `headers` `parameters` `body` , `url` ), mert a munkafüzet összes felhasználója számára láthatóvá válik.

## <a name="next-steps"></a>Következő lépések

* [Ismerkedjen](workbooks-visualizations.md) meg a munkafüzetek számos gazdag vizualizációs lehetőségével.
* A munkafüzet erőforrásaihoz való hozzáférés [szabályozása](workbooks-access-control.md) és megosztása.
* [Log Analytics lekérdezés-optimalizálási tippek](../log-query/query-optimization.md)
