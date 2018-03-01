---
title: "Műveleteket, eseményeket és számlálókat figyelje a Load Balancer |} Microsoft Docs"
description: "Megtudhatja, hogyan engedélyezze a figyelmeztetési események, és a mintavételi Azure Load Balancer egészségügyi állapot naplózása"
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
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 4388a1e933a0ebf211b5a7621c74b0622be41a4c
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2018
---
# <a name="log-analytics-for-azure-load-balancer"></a>Naplóelemzés az Azure Load Balancerhez

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Segítségével különböző típusú naplók az Azure-ban kezelésére és hibaelhárítására terheléselosztók. Néhányat ezek a naplók a portálon keresztül is elérhetők. Minden naplók az Azure blob storage kicsomagolja, és különböző eszközök, például az Excel és a Power bi megtekintett. Többet tudhat meg az alábbi listában naplók különböző típusú.

* **A naplók:** használható [Azure-beli Auditnaplók](../monitoring-and-diagnostics/insights-debugging-with-events.md) (korábbi nevén műveleti naplókat) összes művelet nem továbbíthatók a társított Azure-előfizetéseit, és azok állapotának megtekintése. Naplók alapértelmezés szerint engedélyezve vannak, és az Azure portálon is megtekinthetők.
* **Eseménynaplók riasztási:** használhatja ezt a naplót a terheléselosztó által kiváltott riasztások megtekintése. A load balancer állapota 5 percenként összegyűjtött. Ez a napló csak íródott, ha egy terhelés terheléselosztó figyelmeztetési esemény jelenik meg.
* **Állapot-mintavételi naplók:** Ez a napló segítségével megtekintheti a állapotmintáihoz, például a háttér-készletben szereplő példányok, amelyek nem fogadnak kérések a terheléselosztó miatt állapota sikertelen mintavételek száma által észlelt problémákkal. Ez a napló írni, amikor a mintavételi állapota megváltozik.

> [!IMPORTANT]
> Naplófájl analytics jelenleg csak az internetre irányuló terheléselosztók működik. A Resource Manager üzembe helyezési modellben telepített erőforrásokhoz csak érhetők el naplók. A klasszikus üzembe helyezési modellel erőforrások naplók nem használható. Az üzembe helyezési modellel kapcsolatos további információkért lásd: [Understanding Resource Manager üzembe helyezési és a klasszikus üzembe helyezési](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="enable-logging"></a>Naplózás engedélyezése

Minden erőforrás-kezelő erőforrás naplózásra automatikusan engedélyezve van. Esemény és állapotfigyelő mintavételi naplózás elindításához lesznek a naplók keresztül elérhető adatok gyűjtésének engedélyezése szükséges. Az alábbi lépések segítségével naplózását.

Jelentkezzen be a [Azure-portálon](http://portal.azure.com). Ha még nem rendelkezik egy terhelés-kiegyenlítő [hozzon létre egy terhelés-kiegyenlítő](load-balancer-get-started-internet-arm-ps.md) a folytatás előtt.

1. Kattintson a portál **Tallózás**.
2. Válassza ki **Terheléselosztók**.

    ![portál - terheléselosztó](./media/load-balancer-monitor-log/load-balancer-browse.png)

3. Válassza ki a meglévő terheléselosztó >> **összes beállítás**.
4. A jobb oldalon a terheléselosztó neve alatt a párbeszédpanelen görgessen **figyelés**, kattintson a **diagnosztika**.

    ![portál - terheléselosztási--beállításokat](./media/load-balancer-monitor-log/load-balancer-settings.png)

5. Az a **diagnosztika** ablaktáblán, a **állapot**, jelölje be **a**.
6. Kattintson a **Tárfiók**.
7. A **naplók**, válasszon egy meglévő tárfiókot, vagy hozzon létre egy újat. A csúszka segítségével meghatározhatja, hány napos az eseményadatok fogja tárolni az eseménynaplókat. 
8. Kattintson a **Save** (Mentés) gombra.

    ![portál – diagnosztikai naplók](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

> [!NOTE]
> Naplók nem igényelnek külön tárfiókot. A használható tárolási esemény és állapota Hálózatfigyelő naplózási szolgáltatási díjak gyakorisága.

## <a name="audit-log"></a>Napló

A napló alapértelmezés szerint létrejön. A naplók Azure eseménynaplók tárolójában 90 napig megőrződnek. További információ a naplók elolvasni a [események megtekintése és a naplók](../monitoring-and-diagnostics/insights-debugging-with-events.md) cikk.

## <a name="alert-event-log"></a>Riasztási eseménynaplót

Ez a napló csak akkor keletkezik, ha engedélyezte a egy / load balancer alapján. Az események naplózása JSON formátumban, és ha engedélyezte a naplózást a megadott tárfiók tárolja. Az esemény például a következő:

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

A JSON kimeneti jelennek meg a *eventname* tulajdonság, amely leírja, a terheléselosztó okát létrehozott egy riasztást. Ebben az esetben az előállított riasztások TCP port Erőforrásfogyás forrás IP-NAT által okozott (SNAT) korlátok miatt volt.

## <a name="health-probe-log"></a>Állapot-mintavételi napló

Ez a napló csak akkor keletkezik, ha engedélyezte a egy betöltési terheléselosztó módon részletes fenti száma. Ha engedélyezte a naplózást a megadott tárfiók tárolja az adatok. Egy "insights-logs-loadbalancerprobehealthstatus" nevű tárolót jön létre, és a rendszer a következő adatokat naplózza:

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

A JSON-kimenetét a Tulajdonságok mező az alapvető információkat jeleníti meg a mintavételi állapotadatok. A *dipDownCount* tulajdonság mutatja a háttérkiszolgálók, amely nem fordulnak elő a hálózati forgalom miatt sikertelen mintavételi válaszok példányok száma összesen.

## <a name="view-and-analyze-the-audit-log"></a>Megtekintése és elemzése a napló

Megtekintheti és elemezheti a napló adatairól a következő módszereket:

* **Azure-eszközök:** információt lekérni a felügyeleti naplók az Azure PowerShell, az Azure parancssori felület (CLI), az Azure REST API vagy az Azure betekintő portálon keresztül. Részletes útmutatás az egyes módszerek részletes leírást talál az [naplózási műveletek a Resource Manager](../azure-resource-manager/resource-group-audit.md) cikk.
* **A Power BI:** Ha még nem rendelkezik egy [Power BI](https://powerbi.microsoft.com/pricing) fiók, próbálkozzon az ingyenes. Használja a [Azure-beli Auditnaplók content pack a Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs), elemezheti az adatokat, előre konfigurált irányítópultok, vagy testre szabhatja a nézeteket, melyek illeszkednek igényeihez.

## <a name="view-and-analyze-the-health-probe-and-event-log"></a>Megtekintése és elemzése a állapotmintáihoz és az Eseménynapló

Szeretne csatlakozni a tárfiókhoz és a JSON naplóbejegyzéseket esemény és állapota Hálózatfigyelő naplók beolvasása. Amikor a JSON-fájlok letöltéséhez alakíthatja őket CSV, tekintse meg az Excel-, Power BI, vagy bármely más adatok képi megjelenítés eszközt.

> [!TIP]
> Ha ismeri a Visual Studio és állandók és a C# változók értékeinek módosítása alapvető fogalmait, használhatja a [konverter eszközök jelentkezzen](https://github.com/Azure-Samples/networking-dotnet-log-converter) elérhető a Githubon.

## <a name="additional-resources"></a>További források

* [Megjelenítheti a Power BI az Azure-beli Auditnaplók](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) blogbejegyzést.
* [Megtekintése és elemzése a Power bi-ban és több Azure-beli Auditnaplók](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogbejegyzést.

## <a name="next-steps"></a>További lépések

[A Load Balancer vizsgálatok ismertetése](load-balancer-custom-probe-overview.md)
