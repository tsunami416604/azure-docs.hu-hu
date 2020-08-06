---
title: Figyelési megoldások a Azure Monitorban | Microsoft Docs
description: A Azure Monitor figyelési megoldásai olyan logikai, vizualizációs és adatgyűjtési szabályok gyűjteményei, amelyek egy adott problémás terület körüli metrikákat biztosítanak.  Ez a cikk a figyelési megoldások telepítésével és használatával kapcsolatos információkat tartalmaz.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/12/2020
ms.openlocfilehash: 406991de2441836565c911ef8a8e1402f6398eb0
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87824783"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>A Azure Monitor figyelési megoldásai

A figyelési megoldások kihasználják az Azure szolgáltatásait, hogy további elemzéseket nyújtsanak egy adott alkalmazás vagy szolgáltatás működéséről. Ez a cikk rövid áttekintést nyújt az Azure-beli monitorozási megoldásokról, valamint a használatáról és telepítéséről. A Azure Monitor a használt alkalmazásokhoz és szolgáltatásokhoz is hozzáadhat figyelési megoldásokat. Ezek általában díjmentesen érhetők el, de a használati díjakat meghívó adatokat gyűjtenek.

## <a name="use-monitoring-solutions"></a>Monitorozási megoldások használata

Nyissa meg Azure Monitor **Áttekintés** lapját, és jelenítse meg a munkaterületen telepített összes megoldás csempéjét.

1. Nyissa meg az [Azure Portalt](https://ms.portal.azure.com). Keresse meg és válassza a **figyelő**elemet.
1. Az **áttekintések** menüben válassza a **továbbiak**lehetőséget.
1. A képernyő felső részén található legördülő listák használatával módosíthatja a munkaterületet vagy a csempék időtartományát.
1. Kattintson a csempére egy megoldás megnyitásához, amely részletesebb elemzést tartalmaz az összegyűjtött adatokról.

![Áttekintés](media/solutions/overview.png)

A figyelési megoldások több típusú Azure-erőforrást is tartalmazhatnak, és a megoldáshoz tartozó összes erőforrást ugyanúgy megtekintheti, mint bármely más erőforrást. Például a megoldásban szereplő összes naplózási lekérdezés megjelenik a [lekérdezési](../log-query/get-started-portal.md#load-queries) lekérdezésekben a **megoldás-lekérdezések** területen, ha ad hoc elemzést végez a [naplók](../log-query/log-query-overview.md)lekérdezésével.

## <a name="list-installed-monitoring-solutions"></a>Telepített figyelési megoldások listázása

### <a name="portal"></a>[Portál](#tab/portal)

Az alábbi eljárás segítségével listázhatja az előfizetésében telepített figyelési megoldásokat.

1. Nyissa meg az [Azure Portalt](https://ms.portal.azure.com). Keresse meg és válassza ki a **megoldásokat**.
1. Az összes munkaterületre telepített megoldások listája látható. A megoldás nevét a-ben telepített munkaterület neve követi.
1. Az előfizetés vagy az erőforráscsoport alapján történő szűréshez használja a képernyő felső részén található legördülő listákat.

![Az összes megoldás listázása](media/solutions/list-solutions-all.png)

A megoldás nevére kattintva megnyithatja az összefoglalás lapját. Ezen a lapon láthatók a megoldásban szereplő összes nézet, és különböző lehetőségeket biztosít a megoldáshoz és a munkaterületéhez. Tekintse meg a megoldás Összegzés lapját a megoldások listázásához a fenti eljárások egyikével, majd kattintson a megoldás nevére.

![Megoldás tulajdonságai](media/solutions/solution-properties.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Használja az az [monitor log-Analytics megoldás listázása](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution#ext-log-analytics-solution-az-monitor-log-analytics-solution-list) parancsot az előfizetésében telepített figyelési megoldások listázásához.   A parancs futtatása előtt `list` kövesse a [figyelési megoldás telepítése](#install-a-monitoring-solution)című témakörben található előfeltételeket.

```azurecli
# List all log-analytics solutions in the current subscription.
az monitor log-analytics solution list

# List all log-analytics solutions for a specific subscription
az monitor log-analytics solution list --subscription MySubscription

# List all log-analytics solutions in a resource group
az monitor log-analytics solution list --resource-group MyResourceGroup
```

* * *

## <a name="install-a-monitoring-solution"></a>Figyelési megoldás telepítése

### <a name="portal"></a>[Portál](#tab/portal)

A Microsoft és partnerei által kínált figyelési megoldások az [Azure piactéren](https://azuremarketplace.microsoft.com)érhetők el. A következő eljárással kereshet az elérhető megoldásokban, és telepítheti őket. Megoldás telepítésekor ki kell választania egy [log Analytics munkaterületet](../platform/manage-access.md) , ahol a megoldás telepítve lesz, és az adatok gyűjtése történik.

1. Az [előfizetéshez tartozó megoldások listájában](#list-installed-monitoring-solutions)kattintson a **Hozzáadás**gombra.
1. Válasszon ki egy megoldást, vagy keressen rá. [A keresési hivatkozáson](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions)keresztül is böngészhet a megoldásokban.
1. Keresse meg a kívánt figyelési megoldást, és olvassa el a leírását.
1. A telepítési folyamat elindításához kattintson a **Létrehozás** gombra.
1. A telepítési folyamat indításakor a rendszer felszólítja, hogy adja meg a Log Analytics munkaterületet, és adja meg a megoldás szükséges konfigurációját.

![Megoldás telepítése](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Megoldás telepítése a Közösségből

A Közösség tagjai felügyeleti megoldásokat küldhetnek az Azure Gyorsindítás sablonjaiba. Ezeket a megoldásokat közvetlenül is telepítheti, vagy később is letöltheti a sablonokat.

1. A munkaterület és a fiók összekapcsolásához kövesse az [log Analytics munkaterület és az Automation-fiók](#log-analytics-workspace-and-automation-account) című témakörben leírt eljárást.
2. Ugrás az [Azure Gyorsindítás sablonjaira](https://azure.microsoft.com/documentation/templates/).
3. Keressen egy olyan megoldást, amely érdekli.
4. A részletek megtekintéséhez válassza ki a megoldást az eredmények közül.
5. Kattintson az **üzembe helyezés az Azure-** ban gombra.
6. A rendszer arra kéri, hogy adjon meg olyan információkat, mint például az erőforráscsoport és a hely a megoldás paramétereinek értékei mellett.
7. A megoldás telepítéséhez kattintson a **vásárlás** gombra.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prepare-your-environment"></a>A környezet előkészítése

1. Az Azure CLI összetevő telepítése

   A CLI-hivatkozási parancsok futtatása előtt [telepítenie kell az Azure CLI](/cli/azure/install-azure-cli) -t.  Ha szeretné, a Azure Cloud Shell használatával is elvégezheti a jelen cikkben ismertetett lépéseket.  A Azure Cloud Shell egy interaktív rendszerhéj-környezet, amelyet a böngészőben használhat.  Cloud Shell elindítása a következő módszerek egyikével:

   - Nyissa meg Cloud Shell[https://shell.azure.com](https://shell.azure.com)

   - A [Azure Portal](https://portal.azure.com) jobb felső sarkában lévő menüsorban kattintson a **Cloud Shell** gombra

1. Bejelentkezés lehetőséget.

   Ha a parancssori felület helyi telepítését használja, jelentkezzen be az az [login](/cli/azure/reference-index#az-login) parancs használatával.  A hitelesítési folyamat befejezéséhez kövesse a terminálban megjelenő lépéseket.

    ```azurecli
    az login
    ```

1. A `log-analytics` bővítmény telepítése

   A `log-analytics` parancs az alapszintű Azure CLI kísérleti bővítménye. További információ a bővítmények [használatáról az Azure CLI használatával](/cli/azure/azure-cli-extensions-overview?).

   ```azurecli
   az extension add --name log-analytics
   ```

   A következő figyelmeztetés várható.

   ```output
   The installed extension `log-analytics` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="install-a-solution-with-the-azure-cli"></a>Megoldás telepítése az Azure CLI-vel

Megoldás telepítésekor ki kell választania egy [log Analytics munkaterületet](../platform/manage-access.md) , ahol a megoldás telepítve lesz, és az adatok gyűjtése történik.  Az Azure CLI-vel a munkaterületek kezelése az az [monitor log-Analytics munkaterület-](/cli/azure/monitor/log-analytics/workspace) referenciák használatával végezhető el.  A munkaterület és a fiók összekapcsolásához kövesse az [log Analytics munkaterület és az Automation-fiók](#log-analytics-workspace-and-automation-account) című témakörben leírt eljárást.

A figyelési megoldás telepítéséhez használja az az [monitor log-Analytics megoldás létrehozása](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution) lehetőséget.  Szögletes zárójelben lévő paraméterek megadása nem kötelező.

```azurecli
az monitor log-analytics solution create --name
                                         --plan-product
                                         --plan-publisher
                                         --resource-group
                                         --workspace
                                         [--no-wait]
                                         [--tags]
```

Az alábbi mintakód egy log-Analytics megoldást hoz létre a Omsgaléria/tárolók megtervezéséhez.

```azurecli
az monitor log-analytics solution create --resource-group MyResourceGroup \
                                         --name Containers({SolutionName}) \
                                         --tags key=value \
                                         --plan-publisher Microsoft  \
                                         --plan-product "OMSGallery/Containers" \
                                         --workspace "/subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/ \
                                           Microsoft.OperationalInsights/workspaces/{WorkspaceName}"
```

* * *

## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics munkaterület és Automation-fiók

Minden figyelési megoldáshoz [log Analytics munkaterület](../platform/manage-access.md) szükséges a megoldás által gyűjtött adatok tárolásához, valamint a naplóbeli keresések és nézetek üzemeltetéséhez. Néhány megoldáshoz szükség van egy [Automation-fiókra](../../automation/automation-security-overview.md) is, amely runbookok és kapcsolódó erőforrásokat is tartalmaz. A munkaterületnek és a fióknak meg kell felelnie az alábbi követelményeknek.

* Egy megoldás telepítése csak egyetlen Log Analytics munkaterületet és egy Automation-fiókot használhat. A megoldást külön is telepítheti több munkaterületre.
* Ha egy megoldáshoz Automation-fiókra van szükség, akkor a Log Analytics munkaterület és az Automation-fióknak egymáshoz kell kapcsolódnia. Log Analytics munkaterület csak egy Automation-fiókhoz kapcsolható, és egy Automation-fiók csak egy Log Analytics munkaterülethez kapcsolható.
* Ahhoz, hogy csatolni lehessen, az Log Analytics munkaterület és az Automation-fióknak ugyanahhoz az előfizetéshez kell tartoznia, de az adott régióban üzembe helyezett különböző erőforráscsoportok is lehetnek. A kivétel az USA keleti régiójában és az USA 2. keleti régiójában található Automation-fiókhoz tartozó munkaterület.

Ha az Azure Marketplace-en keresztül telepít megoldást, a rendszer a munkaterület és az Automation-fiók megadását kéri. A közöttük létrejött kapcsolat akkor jön létre, ha még nincsenek összekapcsolva.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Log Analytics munkaterület és az Automation-fiók közötti kapcsolat ellenőrzése

A következő eljárással ellenőrizheti, hogy Log Analytics munkaterület és egy Automation-fiók közötti kapcsolat látható-e.

1. Válassza ki az Automation-fiókot a Azure Portal.
1. Görgessen a menüben a **kapcsolódó erőforrások** szakaszhoz, és válassza a **csatolt munkaterület**lehetőséget.
1. Ha a **munkaterület** egy Automation-fiókhoz van csatolva, akkor ez a lap felsorolja a munkaterületet, amelyhez csatolva van. Ha kijelöli a felsorolt munkaterület nevét, a rendszer átirányítja az adott munkaterület áttekintés lapjára.

## <a name="remove-a-monitoring-solution"></a>Figyelési megoldás eltávolítása

### <a name="portal"></a>[Portál](#tab/portal)

Ha a portálon szeretné eltávolítani a telepített megoldásokat, keresse meg a [telepített megoldások listájában](#list-installed-monitoring-solutions). Kattintson a megoldás nevére az összefoglalás oldal megnyitásához, majd kattintson a **Törlés**gombra.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Egy telepített megoldás Azure CLI használatával történő eltávolításához használja az az [monitor log-Analytics Solution delete](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution#ext-log-analytics-solution-az-monitor-log-analytics-solution-delete) parancsot.

```azurecli
az monitor log-analytics solution delete --name
                                         --resource-group
                                         [--no-wait]
                                         [--yes]
```

* * *

## <a name="next-steps"></a>További lépések

* Szerezze be a [Microsoft figyelési megoldásainak listáját](../monitor-reference.md).
* Megtudhatja, hogyan [hozhat létre lekérdezéseket](../log-query/log-query-overview.md) a figyelési megoldások által gyűjtött adatok elemzéséhez.
* Tekintse [meg Azure monitor összes Azure CLI-parancsát](/cli/azure/azure-cli-reference-for-monitor).