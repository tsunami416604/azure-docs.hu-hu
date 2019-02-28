---
title: Engedélyezze, és az Azure Data Lake Analytics diagnosztikai naplóinak megtekintése
description: Megtudhatja, hogyan állíthatja be, és az Azure Data Lake Analytics diagnosztikai naplók elérése
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: cf5633d4-bc43-444e-90fc-f90fbd0b7935
ms.topic: conceptual
ms.date: 02/12/2018
ms.openlocfilehash: 7fd88383e909ebd6be64c22721b813946e37179e
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56959127"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Az Azure Data Lake Analytics diagnosztikai naplóinak elérése

Diagnosztikai naplózás gyűjthet adatokat a fájlhozzáférés napló ellenőrzését teszi lehetővé. Ezek a naplók például nyújtanak információt:

* Az adatokat elérő felhasználók listáját.
* Milyen gyakran az adatokhoz.
* A fiókban tárolt adatok mennyiségét.

## <a name="enable-logging"></a>Naplózás engedélyezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Nyissa meg a Data Lake Analytics-fiókot, és válassza ki **diagnosztikai naplók** származó a __figyelő__ szakaszban. Majd __diagnosztika bekapcsolása__.

    ![Engedélyezze a diagnosztikát gyűjtése a naplózási és a naplók kérése](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. A __diagnosztikai beállítások__, adjon meg egy __neve__ ehhez a naplózási konfiguráció és majd naplózási beállításokat.

    ![Engedélyezze a diagnosztikát gyűjtése a naplózási és a naplók kérése](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "diagnosztikai naplók engedélyezése")

   * Tárolási/folyamat az adatok három különböző módon lehet váltani.

     * Válassza ki __archiválás tárfiókba__ naplóinak tárolásához egy Azure storage-fiókban. Használja ezt a beállítást, ha az adatok archiválása szeretné. Ha ezt a lehetőséget választja, meg kell adnia egy az Azure storage-fiók segítségével menti a naplókat.

     * Válassza ki **Stream egy eseményközpontba** a naplózási adatok streamelése az Azure-Eseményközpontba. Használja ezt a beállítást, ha egy alárendelt feldolgozási folyamatot, amely a bejövő naplók valós időben elemzi. Ha ezt a lehetőséget választja, meg kell adnia a részletek az Azure Event Hubs is használni szeretné a.

     * Válassza ki __Küldés a Log Analyticsnek__ az adatok küldése az Azure Monitor szolgáltatásba. Használja ezt a beállítást, ha azt szeretné, az Azure Monitor-naplók segítségével gyűjtse össze és -naplók elemzése.
   * Adja meg, hogy szeretné-e a vizsgálati naplók kérelmekről készült naplók és/vagy beolvasása.  Egy kérelem napló rögzíti minden API-kérelem. Az auditnapló rögzíti, hogy API-kérelem által aktivált összes művelet.

   * A __archiválás tárfiókba__, adja meg, hány napig megőrizni az adatokat.

   * Kattintson a __Save__ (Mentés) gombra.

        > [!NOTE]
        > Ki kell választania, vagy __archiválás tárfiókba__, __egy eseményközpontba Stream__ vagy __Küldés a Log Analyticsnek__ gombra kattintás előtt a __mentése__ gomb.

### <a name="use-the-azure-storage-account-that-contains-log-data"></a>Az Azure Storage-fiókot, amely tartalmazza a Teljesítménynapló-adatok használata

1. A blob-tárolók, amelyek rendelkeznek a naplózási adatok megjelenítéséhez nyissa meg a Data Lake Analytics, a naplózáshoz használt Azure Storage-fiókot, és kattintson __Blobok__.

   * A tároló **insights-logs-naplózási** tartalmazza a naplófájlok.
   * A tároló **insights-logs-kérelmek** tartalmazza a kérés naplókat.

2. A tárolók belül a naplók alapján a következő fájlstruktúra tárolja:

        resourceId=/
          SUBSCRIPTIONS/
            <<SUBSCRIPTION_ID>>/
              RESOURCEGROUPS/
                <<RESOURCE_GRP_NAME>>/
                  PROVIDERS/
                    MICROSOFT.DATALAKEANALYTICS/
                      ACCOUNTS/
                        <DATA_LAKE_ANALYTICS_NAME>>/
                          y=####/
                            m=##/
                              d=##/
                                h=##/
                                  m=00/
                                    PT1H.json

   > [!NOTE]
   > A `##` bejegyzések az elérési út az év, hónap, nap és óra jött létre a napló tartalmazza. A Data Lake Analytics létrehoz egy fájlt óránként, ezért `m=` mindig tartalmazza a egy értéke `00`.

    Tegyük fel az auditnapló teljes elérési útja lehet:

        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

    Hasonlóképpen egy kérelem naplófájl teljes elérési útja lehet:

        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## <a name="log-structure"></a>Napló struktúra

A naplózási és a kérés naplók strukturált JSON formátumban vannak.

### <a name="request-logs"></a>Kérelmekről készült naplók

Íme egy minta-bejegyzést a JSON-formátumú kérelem naplóban. Minden egyes blob van egy gyökérobjektum nevű **rekordok** log objektumok egy tömbjét tartalmazza.

    {
    "records":
      [        
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_analytics_account_name>",
             "category": "Requests",
             "operationName": "GetAggregatedJobHistory",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {
                 "HttpMethod":"POST",
                 "Path":"/JobAggregatedHistory",
                 "RequestContentLength":122,
                 "ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8",
                 "StartTime":"2016-07-07T21:02:52.472Z",
                 "EndTime":"2016-07-07T21:02:53.456Z"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="request-log-schema"></a>Kérelem séma

| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| time |String |A napló időbélyegzője (az UTC) |
| resourceId |String |Helyezze a művelet igénybe vett erőforrás azonosítója |
| category |String |A naplózási kategóriát. Ha például **kérelmek**. |
| operationName |String |A művelet, amelyet a rendszer neve. Ha például GetAggregatedJobHistory. |
| resultType |String |A művelet, például 200-as állapotát. |
| callerIpAddress |String |Az ügyfél a kérés IP-címe |
| correlationId |String |A napló azonosítója. Ez az érték kapcsolódó bejegyzései készletét csoportosítására használható. |
| identity |Objektum |Az identitás által generált napló |
| properties |JSON |A következő szakaszban (kérelem tulajdonságai séma) kapcsolatos részletek |

#### <a name="request-log-properties-schema"></a>Kérelem tulajdonságai séma

| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| HttpMethod |String |A művelethez használt HTTP-metódust. Például KAPHAT. |
| Útvonal |String |Az elérési utat a művelet végrehajtásának ideje |
| RequestContentLength |int |A HTTP-kérelem tartalmának hossza |
| ClientRequestId |String |Az azonosító, amely egyedileg azonosítja ezt a kérelmet |
| Kezdés időpontja |String |Az idő, amelyen a kiszolgáló a kérelem érkezett |
| Befejezés időpontja: |String |Az idő, amikor a kiszolgáló által küldött választ |

### <a name="audit-logs"></a>Naplók

Íme egy minta-bejegyzést a JSON-formátumú naplóban. Minden egyes blob van egy gyökérobjektum nevű **rekordok** log objektumok egy tömbjét tartalmazza.

    {
    "records":
      [        
        . . . .
        ,
        {
             "time": "2016-07-28T19:15:16.245Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_ANALYTICS_account_name>",
             "category": "Audit",
             "operationName": "JobSubmitted",
             "identity": "user@somewhere.com",
             "properties": {
                 "JobId":"D74B928F-5194-4E6C-971F-C27026C290E6",
                 "JobName": "New Job",
                 "JobRuntimeName": "default",
                 "SubmitTime": "7/28/2016 7:14:57 PM"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>Auditnapló sémája

| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| time |String |A napló időbélyegzője (az UTC) |
| resourceId |String |Helyezze a művelet igénybe vett erőforrás azonosítója |
| category |String |A naplózási kategóriát. Ha például **naplózási**. |
| operationName |String |A művelet, amelyet a rendszer neve. Ha például JobSubmitted. |
| resultType |String |A részállapot esetében a feladat állapota (operationName). |
| resultSignature |String |További részleteket az a feladat állapota (operationName). |
| identity |String |A felhasználó által kért a műveletet. Például: susan@contoso.com. |
| properties |JSON |A következő szakaszban (naplózási tulajdonságok séma) részletekért lásd: |

> [!NOTE]
> **resultType** és **resultSignature** adatokat egy művelet eredményét, és csak tartalmaz értéket, ha egy művelet befejeződött. Például egy érték csak tartalmaznak amikor **operationName** szereplő érték **JobStarted** vagy **JobEnded**.
>
>

#### <a name="audit-log-properties-schema"></a>Naplózási tulajdonságok séma

| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| JobId |String |A feladathoz hozzárendelt azonosító |
| JobName |String |A feladat megadott neve |
| JobRunTime |String |A futtatókörnyezet, a feladat elvégzéséhez használt |
| SubmitTime |String |Az idő (UTC), a feladat el lett küldve. |
| Kezdés időpontja |String |Az idő a feladat elindításának (UTC) elküldése után |
| Befejezés időpontja: |String |Az idő a feladat befejeződött |
| Párhuzamosság |String |A kért a feladat elküldése közben. a Data Lake Analytics-egységek számát |

> [!NOTE]
> **SubmitTime**, **StartTime**, **EndTime**, és **párhuzamosság** művelet információkat tartalmaz. Ezek a bejegyzések csak tartalmazhatnak értéket, amely a művelet elindult vagy befejeződött. Ha például **SubmitTime** csak az után értéket tartalmaz **operationName** értéke **JobSubmitted**.

## <a name="process-the-log-data"></a>A naplózási adatok feldolgozása

Az Azure Data Lake Analytics nyújt egy minta a naplózási adatok feldolgozásához és elemzéséhez. Találja, a minta [ https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample ](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample).

## <a name="next-steps"></a>További lépések
* [Az Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
