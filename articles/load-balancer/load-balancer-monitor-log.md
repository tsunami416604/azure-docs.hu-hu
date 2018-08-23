---
title: Az alapszintű nyilvános Load Balancer operations, események és teljesítményszámlálók figyelése |} A Microsoft Docs
description: Ismerje meg, hogyan engedélyezze a riasztási eseményekre, és egészségügyi állapotának naplózása alapszintű nyilvános Load Balancer mintavételi
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
ms.assetid: 56656d74-0241-4096-88c8-aa88515d676d
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/10/2018
ms.author: kumud
ms.openlocfilehash: 5c196a24d5cc9e4343762e55b5885c1264c2fcad
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2018
ms.locfileid: "42061056"
---
# <a name="log-analytics-for-public-basic-load-balancer"></a>Naplóelemzés az alapszintű nyilvános Load Balancer

>[!IMPORTANT] 
>Az Azure Load Balancer két különböző típust támogat: Alapszintű és Standard. Ez a cikk az Alapszintű Load Balancert ismerteti. A Standard Load Balancer kapcsolatos további információkért lásd: [Standard Load Balancer áttekintése](load-balancer-standard-overview.md) amely közzéteszi a többdimenziós metrikák az Azure monitorban telemetriai adatokat.

Segítségével különböző típusú naplók az Azure-ban felügyelet és hibaelhárítás az alapszintű Load Balancer Terheléselosztók. Néhányat ezek a naplók a portálon keresztül is elérhetők. Az összes napló ki kell olvasni az Azure blob storage-ból, és a különböző eszközök, például az Excel és a Power bi tekinthetők meg. További információ a különböző típusú naplók az alábbi listából.

* **Auditnaplók:** használható [Azure-Auditnaplók](../monitoring-and-diagnostics/insights-debugging-with-events.md) (korábbi nevén műveleti naplók) minden művelet nem továbbíthatók a társított Azure-előfizetéseit, és azok állapotának megtekintése. Auditnaplók alapértelmezés szerint engedélyezve vannak, és az Azure Portalon lehet megtekinteni.
* **Riasztás az eseménynaplókat:** használhatja ezt a naplót a terheléselosztó által kiváltott riasztások megtekintéséhez. A terheléselosztó állapotát öt percenként gyűjtött. Ha egy load balancer figyelmeztetési esemény jelenik meg, ez a napló csak írása.
* **Állapotminta-naplókat:** Ez a napló segítségével megtekintheti az állapotmintát, például a példányok a háttérkiszolgáló-készlet, amely nem kap kéréseket a terheléselosztóból miatt sikertelen állapotadat-mintavételi által észlelt problémákkal. Ebbe a naplófájlba írja be az állapot-mintavételi változása esetén.

> [!IMPORTANT]
> Log analytics jelenleg csak nyilvános alapszintű működik terheléselosztók. A Resource Manager-alapú üzemi modellben üzembe helyezett erőforrások csak érhetők el naplók. Naplók az erőforrások a klasszikus üzemi modellben nem használhat. Az üzembe helyezési modellel kapcsolatos további információkért lásd: [Understanding Resource Manager-alapú és klasszikus üzembe helyezési](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="enable-logging"></a>Naplózás engedélyezése

Naplóvizsgálat automatikusan engedélyezve van minden Resource Manager-erőforrással. Esemény- és egészségügyi ezeket a naplókat keresztül elérhető adatok gyűjtésének megkezdéséhez naplózását mintavételi engedélyeznie kell. Használja az alábbi lépéseket a naplózás engedélyezése.

Jelentkezzen be a [az Azure portal](http://portal.azure.com). Ha még nem rendelkezik egy terheléselosztó [load balancer létrehozása](load-balancer-get-started-internet-arm-ps.md) a folytatás előtt.

1. Kattintson a portál **Tallózás**.
2. Válassza ki **Terheléselosztók**.

    ![portál – load balancer](./media/load-balancer-monitor-log/load-balancer-browse.png)

3. Válasszon egy meglévő terheléselosztóhoz >> **minden beállítás**.
4. A terheléselosztó neve alatt található a párbeszédpanel jobb oldalán, görgessen a **figyelés**, kattintson a **diagnosztikai**.

    ![portál – load-balancer-beállítások](./media/load-balancer-monitor-log/load-balancer-settings.png)

5. Az a **diagnosztikai** panel alatt **állapot**, jelölje be **a**.
6. Kattintson a **Tárfiók**.
7. A **naplók**, válasszon ki egy meglévő tárfiókot vagy hozzon létre egy újat. A csúszka segítségével meghatározhatja, hány napig érdemes eseményadatok lesz tárolva az eseménynaplók. 
8. Kattintson a **Save** (Mentés) gombra.

A megadott tárfiók diagnosztikai menti a Table Storage. Ha a naplókat a rendszer nem menti, mert nincs megfelelő napló előállítása.

![Portál – diagnosztikai naplók](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

> [!NOTE]
> Auditnaplók nem igényel külön tárfiókot. A használati esemény- és egészségügyi tárhelyet mintavételi naplózási szolgáltatás díjat számolunk.

## <a name="audit-log"></a>Napló

Alapértelmezés szerint a napló jön létre. A naplók az Azure-eseménynaplók áruházbeli 90 napig megőrződnek. További információ a naplók olvassa el a [és naplók megtekintése, események](../monitoring-and-diagnostics/insights-debugging-with-events.md) cikk.

## <a name="alert-event-log"></a>Riasztási eseménynaplót

Ez a napló csak akkor keletkezik, ha engedélyezte az a load balancer alapon történik. Az események naplózása JSON formátumban, és a naplózás engedélyezésekor a megadott tárfiókban tárolja. Az alábbiakban látható egy példa egy eseményt.

```json
{
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }
}
```

JSON kimeneti mutat be a *eventname* tulajdonság, amely azt ismerteti, a terheléselosztó az az oka létre riasztást. Ebben az esetben a generált riasztások TCP portfogyás forrás IP NAT által okozott (SNAT) korlátok miatt volt.

## <a name="health-probe-log"></a>Állapot-mintavételi napló

Ez a napló csak akkor keletkezik, ha engedélyezte az a load balancer alapon történik leírtaknak megfelelően a fenti. Az adatok a storage-fiókot, ha engedélyezte a naplózást a megadott tárolódik. Egy "insights-logs-loadbalancerprobehealthstatus" nevű tároló jön létre, és a rendszer a következő adatokat naplózza:

```json
{
    "records":[
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 1,
            "healthPercentage": 50.000000
        }
    },
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 0,
            "healthPercentage": 100.000000
        }
    }]
}
```

A JSON-kimenet megjeleníti a Tulajdonságok mező mintavételi állapotát az alapvető információkat. A *dipDownCount* tulajdonság a háttéralkalmazás, amely a rendszer nem fogad hálózati forgalmat a meghiúsult mintavételi válaszok miatt a példányok teljes számát jeleníti meg.

## <a name="view-and-analyze-the-audit-log"></a>Megtekintheti és elemezheti az auditnapló

Megtekintheti és elemezheti a naplózási adatokat az alábbi módszerek bármelyikével:

* **Azure-eszközök:** információt lekérni a felügyeleti naplók az Azure PowerShell-lel, az Azure parancssori felület (CLI), az Azure REST API vagy az Azure betekintő portálon keresztül. Az egyes módszerek részletes útmutató a részletes leírást talál a [auditálási műveletek a Resource Manager](../azure-resource-manager/resource-group-audit.md) cikk.
* **A Power bi-ban:** Ha Ön még nem rendelkezik egy [Power BI](https://powerbi.microsoft.com/pricing) fiók próbálhatja ki ingyenesen. Használatával a [Azure-Auditnaplók tartalomcsomaghoz a Power bi](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs), elemezheti az adatokat az irányítópultok előre konfigurált, vagy testre szabhatja a nézeteket a saját követelményeinek megfelelően.

## <a name="view-and-analyze-the-health-probe-and-event-log"></a>Megtekintheti és elemezheti az állapotadat-mintavétel és az Eseménynapló

A tárfiókhoz való csatlakozáshoz, és esemény- és egészségügyi állapotminta-naplókat a JSON-bejegyzései lekérése kell. Miután letöltötte a JSON-fájlok, alakíthatja őket CSV, tekintse meg az Excel, a Power bi vagy bármely más adatvizualizációs eszközt.

> [!TIP]
> Ha ismeri a Visual Studiót, illetve C#-állandók és -változók módosításának alapfogalmait, használja a GitHubról elérhető [naplókonvertáló eszközöket](https://github.com/Azure-Samples/networking-dotnet-log-converter).

## <a name="additional-resources"></a>További források

* [Az Azure-Auditnaplók a Power BI segítségével ábrázolhatja](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) blogbejegyzést.
* [Megtekintheti és elemezheti a Power BI és egyéb Azure-Auditnaplók](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogbejegyzést.

## <a name="next-steps"></a>További lépések

[A Load Balancer vizsgálatok ismertetése](load-balancer-custom-probe-overview.md)
