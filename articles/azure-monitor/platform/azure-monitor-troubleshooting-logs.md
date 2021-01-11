---
title: Azure Monitor hibaelhárítási naplók (előzetes verzió)
description: A Azure Monitor használatával gyorsan, vagy rendszeres időközönként megvizsgálhatja a problémákat, elháríthatja a kód-vagy konfigurációs problémákat vagy a támogatási eseteket, amelyek gyakran a nagy mennyiségű adat keresésére támaszkodnak az egyes elemzésekhez.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 12/29/2020
ms.openlocfilehash: 816cdddc1f3d0a9bc9ebc3f277bc223a688cba31
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2021
ms.locfileid: "98067363"
---
# <a name="azure-monitor-troubleshooting-logs-preview"></a>Azure Monitor hibaelhárítási naplók (előzetes verzió)
A Azure Monitor használatával gyorsan és/vagy rendszeres időközönként vizsgálhatja a problémákat, elháríthatja a kód-vagy konfigurációs problémákat vagy a támogatási eseteket, amelyek gyakran a nagy mennyiségű adat keresésére támaszkodnak bizonyos elemzések esetén.

>[!NOTE]
> * A hibaelhárítási naplók előnézeti módban vannak.
>* Forduljon a [log Analytics csapatához](mailto:orens@microsoft.com) bármilyen kérdéssel, vagy alkalmazza a szolgáltatást.
## <a name="troubleshoot-and-query-your-code-or-configuration-issues"></a>A kód-vagy konfigurációs problémák elhárítása és lekérdezése
Azure Monitor hibaelhárítási naplók használatával beolvashatja a rekordokat, és egyszerűbb és olcsóbb módon vizsgálhatja meg a KQL.
A hibaelhárítási naplók az alapszintű hibaelhárítási lehetőségeket biztosítják.

> [!NOTE]
>* A hibaelhárítási mód döntése konfigurálható.
>* A hibaelhárítási naplók alkalmazhatók meghatározott táblákra, amelyek jelenleg a "Container logs" és az "alkalmazás-Nyomkövetések" tábláiban találhatók.
>* A 4 napos ingyenes megőrzési idő további költségeket is kiterjesztheti.
>* Alapértelmezés szerint a táblák öröklik a munkaterület megőrzését. A további költségek elkerülése érdekében ajánlott módosítani a táblák megőrzését. Ide [kattintva megtudhatja, hogyan módosíthatja a táblák megőrzését](https://docs.microsoft.com//azure/azure-monitor/platform/manage-cost-storage).

## <a name="turn-on-troubleshooting-logs-on-your-tables"></a>Hibaelhárítási naplók bekapcsolása a táblákon

A munkaterületen található hibaelhárítási naplók bekapcsolásához a következő API-hívást kell használnia.
```http
PUT https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

(With body in the form of a GET single table request response)

Response:

{
        "properties": {
          "retentionInDays": 40,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": true
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": "{tableName}"
      }
```
## <a name="check-if-the-troubleshooting-logs-feature-is-enabled-for-a-given-table"></a>Ellenőrizze, hogy a hibaelhárítási naplók funkció engedélyezve van-e egy adott táblánál
A következő API-hívással ellenőrizhető, hogy engedélyezve van-e a hibaelhárítási napló egy adott táblához.

```http
GET https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

Response: 
"properties": {
          "retentionInDays": 30,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": true,
          "lastTroubleshootDate": "Thu, 19 Nov 2020 07:40:51 GMT"
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": " {tableName}"

```
## <a name="check-if-the-troubleshooting-logs-feature-is-enabled-for-all-of-the-tables-in-a-workspace"></a>Ellenőrizze, hogy a hibaelhárítási naplók funkció engedélyezve van-e a munkaterület összes táblájához
A következő API-hívással ellenőrizhető, hogy mely táblákon van engedélyezve a hibaelhárítási napló.

```http
GET "https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables"

Response: 
{
          "properties": {
            "retentionInDays": 30,
            "isTroubleshootingAllowed": true,
            "isTroubleshootEnabled": true,
            "lastTroubleshootDate": "Thu, 19 Nov 2020 07:40:51 GMT"
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table1",
          "name": "table1"
        },
        {
          "properties": {
            "retentionInDays": 7,
            "isTroubleshootingAllowed": true
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table2",
          "name": "table2"
        },
        {
          "properties": {
            "retentionInDays": 7,
            "isTroubleshootingAllowed": false
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table3",
          "name": "table3"
        }
```
## <a name="turn-off-troubleshooting-logs-on-your-tables"></a>A táblák hibaelhárítási naplóinak kikapcsolása

Ha ki szeretné kapcsolni a munkaterülethez tartozó hibakeresési naplókat, a következő API-hívást kell használnia.
```http
PUT https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

(With body in the form of a GET single table request response)

Response:

{
        "properties": {
          "retentionInDays": 40,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": false
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": "{tableName}"
      }
```
>[!TIP]
>* A parancsok futtatásához bármilyen REST API eszközt használhat. [További információk](https://docs.microsoft.com/rest/api/azure/)
>* A hitelesítéshez a tulajdonosi jogkivonatot kell használnia. [További információk](https://social.technet.microsoft.com/wiki/contents/articles/51140.azure-rest-management-api-the-quickest-way-to-get-your-bearer-token.aspx)

>[!NOTE]
>* A "isTroubleshootingAllowed" jelző – azt ismerteti, hogy a tábla engedélyezett-e a szolgáltatásban
>* A "isTroubleshootEnabled" érték azt jelzi, hogy a funkció engedélyezve van-e a táblához – be-és kikapcsolható (igaz vagy hamis)
>* Ha egy adott tábla "isTroubleshootEnabled" jelzőjét letiltja, az újbóli engedélyezése csak egy héttel az előző engedélyezési dátum után lehetséges.
>* Ez jelenleg csak a következő táblák esetén támogatott: (egyes más SKU-ket a jövőben is támogatni fogjuk) – [További információ a díjszabásról](https://docs.microsoft.com/services-hub/health/azure_pricing).

## <a name="query-limitations-for-troubleshooting"></a>Hibaelhárítási korlátozások a lekérdezésekhez
A "hibaelhárítási naplók" jelöléssel rendelkező táblákra néhány korlátozás vonatkozik:
*   Kevesebb feldolgozási erőforrást kap, ezért nem lesz megfelelő a nagyméretű irányítópultok, az összetett Analitika vagy számos egyidejű API-hívás esetében.
*   A lekérdezések két napos időtartományra korlátozódnak.
* az öblítés nem fog működni – [További információ a kiürítésről](https://docs.microsoft.com/rest/api/loganalytics/workspacepurge/purge).
* A riasztások nem támogatottak ezen a szolgáltatáson keresztül.
## <a name="next-steps"></a>Következő lépések
* [Lekérdezések írása](https://docs.microsoft.com/azure/data-explorer/write-queries)


