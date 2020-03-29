---
title: Az Azure Data Lake Analytics diagnosztikai naplóinak engedélyezése és megtekintése
description: Ismerje meg, hogyan állíthatja be és érheti el az Azure Data Lake Analytics diagnosztikai naplóit
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: cf5633d4-bc43-444e-90fc-f90fbd0b7935
ms.topic: conceptual
ms.date: 02/12/2018
ms.openlocfilehash: 7fd88383e909ebd6be64c22721b813946e37179e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60616499"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Az Azure Data Lake Analytics diagnosztikai naplóinak elérése

A diagnosztikai naplózás lehetővé teszi az adatok elérésének naplózási nyomvonalainak gyűjtését. Ezek a naplók olyan információkat tartalmaznak, mint például:

* Az adatokhoz hozzáférő felhasználók listája.
* Az adatok elérésének gyakori adata.
* A fiókban tárolt adatok nagy része.

## <a name="enable-logging"></a>Naplózás engedélyezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Nyissa meg a Data Lake Analytics-fiókot, és válassza **a Diagnosztikai naplók lehetőséget** a __Figyelő__ szakaszban. Ezután válassza __a Diagnosztika bekapcsolása__lehetőséget.

    ![A diagnosztika bekapcsolása a naplózási és naplók kéréséhez](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. A __Diagnosztikai beállítások párbeszédpanelen__adja meg a naplózási konfiguráció __nevét,__ majd adja meg a naplózási beállításokat.

    ![A diagnosztika bekapcsolása a naplózási és naplók kéréséhez](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "Diagnosztikai naplók engedélyezése")

   * Az adatokat három különböző módon tárolhatja/dolgozhatja fel.

     * Válassza __az Archiválás tárfiókba__ lehetőséget a naplók Azure-tárfiókban való tárolásához. Akkor használja ezt a beállítást, ha archiválni szeretné az adatokat. Ha ezt a lehetőséget választja, meg kell adnia egy Azure-tárfiókot a naplók mentéséhez.

     * Válassza a **Stream egy eseményközpontba** a naplóadatok egy Azure Event Hub. Akkor használja ezt a beállítást, ha a bejövő naplókvalós idejű elemzésére irányuló folyamata lefelé irányuló feldolgozási folyamattal rendelkezik. Ha ezt a lehetőséget választja, meg kell adnia a használni kívánt Azure Event Hub adatait.

     * Válassza __a Küldés a Log Analytics szolgáltatásba__ lehetőséget az adatok nak az Azure Monitor szolgáltatásba való elküldéséhez. Akkor használja ezt a lehetőséget, ha az Azure Monitor naplók at szeretne használni a naplók összegyűjtéséhez és elemzéséhez.
   * Adja meg, hogy naplónaplókat, vagy kérésnaplókat szeretne-e kapni, vagy mindkettőt.  A kérelemnapló minden API-kérelmet rögzít. A napló rögzíti az API-kérelem által aktivált összes műveletet.

   * Az __Archiválás tárfiókba lehetőségesetén__adja meg az adatok megőrzéséhez eltelt napok számát.

   * Kattintson a __Mentés__ gombra.

        > [!NOTE]
        > A __Mentés__ gombra kattintás előtt ki kell választania az __Archiválás tárfiókba__, __az Adatfolyam-küldés eseményközpontba__ vagy a __Küldés a naplóelemzésbe__ lehetőséget.

### <a name="use-the-azure-storage-account-that-contains-log-data"></a>A naplóadatokat tartalmazó Azure Storage-fiók használata

1. A naplózási adatokat tároló blobtárolók megjelenítéséhez nyissa meg a Data Lake Analytics naplózáshoz használt Azure Storage-fiókot, majd kattintson a __Blobok__elemre.

   * A tároló **insights-logs-audit** tartalmazza a naplónaplókat.
   * A tároló **insights-logs-kérelmek** tartalmazzák a kérelem naplók.

2. A tárolókon belül a naplók a következő fájlszerkezetalatt tárolódnak:

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
   > A `##` görbén szereplő bejegyzések tartalmazzák azt az évet, hónapot, napot és órát, amelyben a naplót létrehozták. A Data Lake Analytics óránként `m=` egy fájlt hoz `00`létre, így mindig tartalmaz egy értéket.

    A napló teljes elérési útja például a következő lehet:

        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

    Hasonlóképpen, a kérelemnapló teljes elérési útja a következő lehet:

        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## <a name="log-structure"></a>Naplószerkezet

A naplózási és kérelemnaplók strukturált JSON formátumúak.

### <a name="request-logs"></a>Naplók kérése

Az alábbiakban egy mintabejegyzés található a JSON-formátumú kérelemnaplóban. Minden blob rendelkezik egy **gyökérobjektummal, amelyet rekordoknak** neveznek, és amely naplóobjektumok tömbjét tartalmazza.

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

#### <a name="request-log-schema"></a>Naplóséma kérése

| Név | Típus | Leírás |
| --- | --- | --- |
| time |Sztring |A napló időbélyegzője (UTC-ben) |
| resourceId |Sztring |Annak az erőforrásnak az azonosítója, amelyen a művelet et |
| category |Sztring |A naplókategória. Például **a Kérések**. |
| operationName |Sztring |A naplózott művelet neve. Például GetAggregatedJobHistory. |
| resultType |Sztring |A művelet állapota, például 200. |
| callerIpAddress |Sztring |A kérést küldő ügyfél IP-címe |
| correlationId |Sztring |A napló azonosítója. Ezzel az értékkel csoportosíthatja a kapcsolódó naplóbejegyzéseket. |
| identity |Objektum |A naplót létrehozó identitás |
| properties |JSON |A részleteket lásd a következő szakaszban (Naplótulajdonságok kérése schema) |

#### <a name="request-log-properties-schema"></a>Naplótulajdonságainak kérése sémája

| Név | Típus | Leírás |
| --- | --- | --- |
| HttpMódszer |Sztring |A művelethez használt HTTP-módszer. Például get. |
| Útvonal |Sztring |Az az útvonal, amelyen a műveletet végrehajtották |
| RequestContentLength (RequestContentLength) |int |A HTTP-kérelem tartalomhossza |
| Ügyfélkéréseazonosító |Sztring |A kérést egyedileg azonosító azonosító |
| StartTime |Sztring |Az az időpont, amikor a kiszolgáló megkapta a kérést |
| EndTime |Sztring |Az az időpont, amikor a szerver választ küldött |

### <a name="audit-logs"></a>Naplók

Az alábbiakban egy mintabejegyzés található a JSON-formátumú naplóban. Minden blob rendelkezik egy **gyökérobjektummal, amelyet rekordoknak** neveznek, és amely naplóobjektumok tömbjét tartalmazza.

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

| Név | Típus | Leírás |
| --- | --- | --- |
| time |Sztring |A napló időbélyegzője (UTC-ben) |
| resourceId |Sztring |Annak az erőforrásnak az azonosítója, amelyen a művelet et |
| category |Sztring |A naplókategória. **Például: Audit**. |
| operationName |Sztring |A naplózott művelet neve. Például: JobSubmitted. |
| resultType |Sztring |A feladat állapotának alállapota (operationName). |
| resultSignature |Sztring |További részletek a feladat állapotáról (operationName). |
| identity |Sztring |A műveletet kérő felhasználó. Például: susan@contoso.com. |
| properties |JSON |A részleteket lásd a következő szakaszban (Naplótulajdonságainak naplózási sémája). |

> [!NOTE]
> **resultType** és **resultSignature** információt ad meg egy művelet eredményéről, és csak akkor tartalmaz értéket, ha egy művelet befejeződött. Például csak akkor tartalmaznak értéket, ha **a OperationName** a **JobStarted** vagy a **JobEnded**értéket tartalmazza.
>
>

#### <a name="audit-log-properties-schema"></a>Naplótulajdonságainak sémája

| Név | Típus | Leírás |
| --- | --- | --- |
| JobId |Sztring |A feladathoz rendelt azonosító |
| Feladatneve |Sztring |A feladathoz megadott név |
| Feladatruntime |Sztring |A feladat feldolgozásához használt futásidő |
| SubmitTime |Sztring |A feladat elküldésének időpontja (UTC-ben) |
| StartTime |Sztring |A feladat beküldés utáni futásának időpontja (UTC-ben) |
| EndTime |Sztring |A feladat befejezésekor |
| Párhuzamosság |Sztring |A beküldés során ehhez a feladathoz kért Data Lake Analytics-egységek száma |

> [!NOTE]
> **A SubmitTime**, **a StartTime**, **az EndTime**és a **Parallelism** információt nyújt egy műveletről. Ezek a bejegyzések csak akkor tartalmaznak értéket, ha a művelet elkezdődött vagy befejeződött. A **SubmitTime** például csak egy értéket tartalmaz, miután a **OperationName** értéke **JobSubmitted**.

## <a name="process-the-log-data"></a>A naplóadatok feldolgozása

Az Azure Data Lake Analytics mintaként szolgál a naplóadatok feldolgozásához és elemzéséhez. A mintát a. [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample)

## <a name="next-steps"></a>További lépések
* [Az Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
