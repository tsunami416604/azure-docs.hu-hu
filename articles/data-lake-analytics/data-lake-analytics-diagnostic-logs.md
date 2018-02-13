---
title: "Diagnosztikai naplók megtekintése az Azure Data Lake Analytics |} Microsoft Docs"
description: "Ismerje meg, hogyan kell beállítania, és hozzáférés az Azure Data Lake analytics diagnosztikai naplók "
services: data-lake-analytics
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: cf5633d4-bc43-444e-90fc-f90fbd0b7935
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/12/2018
ms.author: larryfr
ms.openlocfilehash: e6cc5fd3d45691dbdc004f346c10d7b4568ae9aa
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2018
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Diagnosztikai naplók elérése az Azure Data Lake Analytics

Diagnosztikai naplózás gyűjthet adatokat a fájlhozzáférés napló ellenőrzését teszi lehetővé. Ezek a naplók meg információkat, mint:

* Azoknak a felhasználóknak, az adatok érhetők el.
* Milyen gyakran az adatokhoz.
* A fiók mennyi adatot tárolja.

## <a name="enable-logging"></a>Naplózás engedélyezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Nyissa meg a Data Lake Analytics-fiókja, és válassza ki **diagnosztikai naplók** a a __figyelő__ szakasz. Válassza ki, __a diagnosztika bekapcsolásához__.

    ![Engedélyezze a diagnosztikát gyűjtése a naplózási és naplók kérése](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. A __diagnosztikai beállítások__, adjon meg egy __neve__ a naplózási, mind jelölje ki a naplózási beállításokat.

    ![Engedélyezze a diagnosztikát gyűjtése a naplózási és naplók kérése](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "diagnosztikai naplók engedélyezése")

   * Ha szeretné, az adatok tárolási/folyamat három különböző módon.

     * Válassza ki __tárfiókba archív__ naplók tárolására az Azure storage-fiók. Használja ezt a beállítást, ha azt szeretné, hogy az adatok archiválása. Ha ezt a lehetőséget választja, meg kell adnia egy Azure storage-fiók mentése a naplókat.

     * Válassza ki **adatfolyam egy eseményközpontba** adatfolyam napló adatokat az Azure-Eseményközpontok felé. Használja ezt a beállítást, ha egy alárendelt feldolgozási folyamatot, amely elemzi a bejövő naplók valós időben. Ha ezt a lehetőséget választja, meg kell adnia a használni kívánt Azure Event Hubs részleteit.

     * Válassza ki __küldeni a Naplóelemzési__ adatokat küldeni a Naplóelemzés szolgáltatás. Használja ezt a beállítást, ha azt szeretné, Log Analytics segítségével gyűjtse össze és naplóinak elemzése.
   * Adja meg, hogy megkapják a naplók vagy kérelmek naplóit vagy mindkettőt.  A napló minden API-kérelem rögzíti. Egy naplórekordok az adott API-kérelem által kezdeményezett összes műveletet.

   * A __tárfiókba archív__, adja meg az adatok megőrzéséhez a napok számát.

   * Kattintson a __Mentés__gombra.

        > [!NOTE]
        > Ki kell választania vagy __tárfiókba archív__, __adatfolyam egy eseményközpontba__ vagy __küldeni a Naplóelemzési__ való kattintás előtt a __mentése__ jelölt gombra.

### <a name="use-the-azure-storage-account-that-contains-log-data"></a>Használatát naplóadatokat tartalmazó Azure Storage-fiókban

1. A blobtárolók, amely a naplózási adatok tárolásához megjelenítéséhez nyissa meg a naplózásra használható a Data Lake Analytics az Azure Storage-fiók, és kattintson __Blobok__.

   * A tároló **insights-logs-naplózási** tartalmazza a naplókat.
   * A tároló **insights-logs-kérelmek** tartalmaz a kérelmek naplóit.

2. A tárolók területen belül a naplók a következő fájlstruktúra tárolt:

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
   > A `##` bejegyzések az elérési út év, hónap, nap, és óránként jött létre a napló tartalmaz. A Data Lake Analytics egy fájl minden órában létrehoz, így `m=` mindig szereplő érték `00`.

    Tegyük fel a teljes elérési útját, és egy naplófájlba lehet:

        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

    Hasonlóképpen a teljes elérési útját a napló lehet:

        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## <a name="log-structure"></a>Napló struktúra

A naplózási és kérelem naplók strukturált JSON formátumban vannak.

### <a name="request-logs"></a>Naplók kérése

Íme egy minta-bejegyzést a JSON-formátumú kérelem naplóban. Minden egyes blob tartozik egy legfelső szintű objektum nevű **rekordok** , amely tartalmazza a napló objektumokból álló tömb.

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
| time |Karakterlánc |Az időbélyeg (UTC szerint) a napló |
| resourceId |Karakterlánc |Helyezze a művelet erőforrás azonosítója |
| category |Karakterlánc |A napló kategóriát. Például **kérelmek**. |
| operationName |Karakterlánc |A művelet naplózott neve. Például GetAggregatedJobHistory. |
| resultType |Karakterlánc |A művelet, például 200 állapotát. |
| callerIpAddress |Karakterlánc |A kérést küldő ügyfél IP-címe |
| correlationId |Karakterlánc |A napló azonosítója. Ez az érték a kapcsolódó naplóbejegyzések készlete csoportosításához használható. |
| identity |Objektum |Az identitás, amely a napló jön létre |
| properties |JSON |Lásd a következő szakaszát (kérelem tulajdonságok séma) |

#### <a name="request-log-properties-schema"></a>Kérelem tulajdonságok séma

| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| HttpMethod |Karakterlánc |A művelethez használt HTTP-metódust. Például beolvasása. |
| Útvonal |Karakterlánc |Az elérési út a művelet végrehajtásának ideje |
| RequestContentLength |int |A HTTP-kérelem a tartalom hossza |
| ClientRequestId |Karakterlánc |Az azonosító, amely egyedileg azonosítja az ehhez a kérelemhez |
| StartTime |Karakterlánc |Az a kiszolgáló fogadja a kérelem ideje |
| Befejezés időpontja |Karakterlánc |Az idő, ahol a kiszolgáló által küldött választ |

### <a name="audit-logs"></a>Naplók

Íme egy minta-bejegyzést a JSON-formátumú naplóban. Minden egyes blob tartozik egy legfelső szintű objektum nevű **rekordok** , amely tartalmazza a napló objektumokból álló tömb.

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

#### <a name="audit-log-schema"></a>Naplózási séma

| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| time |Karakterlánc |Az időbélyeg (UTC szerint) a napló |
| resourceId |Karakterlánc |Helyezze a művelet erőforrás azonosítója |
| category |Karakterlánc |A napló kategóriát. Például **naplózási**. |
| operationName |Karakterlánc |A művelet naplózott neve. Például JobSubmitted. |
| resultType |Karakterlánc |A részállapot a feladat állapota (operationName). |
| resultSignature |Karakterlánc |További részletek a feladat állapota (operationName). |
| identity |Karakterlánc |A felhasználót, hogy a kért a műveletet. Például: susan@contoso.com. |
| properties |JSON |Lásd a következő szakaszát (naplózási tulajdonságai séma) |

> [!NOTE]
> **resultType** és **resultSignature** adatokat a művelet eredményét, és csak tartalmaz értéket, ha egy művelet befejeződött. Például csak tartalmaznak egy érték amikor **operationName** szereplő érték **JobStarted** vagy **JobEnded**.
>
>

#### <a name="audit-log-properties-schema"></a>Naplózási tulajdonságai séma

| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| JobId |Karakterlánc |A feladathoz rendelt azonosító |
| Feladat neve |Karakterlánc |A feladat a megadott neve |
| JobRunTime |Karakterlánc |A futtatókörnyezet segítségével dolgozza fel a feladatot |
| SubmitTime |Karakterlánc |Az idő (UTC), a feladat el lett küldve |
| StartTime |Karakterlánc |A indításakor a feladat futtatása után küldése (az UTC) |
| Befejezés időpontja |Karakterlánc |A feladat befejezésének időpontja |
| Párhuzamos végrehajtás |Karakterlánc |Ez a feladat kért végzett leadásakor Data Lake Analytics egységek száma |

> [!NOTE]
> **SubmitTime**, **StartTime**, **EndTime**, és **párhuzamossági** művelet kapcsolatban nyújtanak információkat. Ezek a bejegyzések csak tartalmaz értéket ha művelet indítása vagy befejeződött. Például **SubmitTime** csak értéke után **operationName** értéke **JobSubmitted**.

## <a name="process-the-log-data"></a>A naplózási adatok feldolgozása

Az Azure Data Lake Analytics egy minta feldolgozhatja és elemezheti a naplóadatok hogyan biztosít. A minta a található [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample).

## <a name="next-steps"></a>További lépések
* [Az Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
