---
title: Az Azure Data Lake Storage Gen1 diagnosztikai naplóinak megtekintése | Microsoft dokumentumok
description: 'Ismerje meg, hogyan állíthatók be és érhetnek el diagnosztikai naplókat az Azure Data Lake Storage Gen1 szolgáltatáshoz '
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: f6e75eb1-d0ae-47cf-bdb8-06684b7c0a94
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: d200f72b3c0e5634c3dca8f60a4754a14351110a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60878716"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-storage-gen1"></a>Az Azure Data Lake Storage Gen1 diagnosztikai naplóinak elérése
Ismerje meg, hogyan engedélyezheti az Azure Data Lake Storage Gen1-fiókjához tartozó diagnosztikai naplózást, és hogyan tekintheti meg a fiókjához gyűjtött naplókat.

A szervezetek engedélyezhetik az Azure Data Lake Storage Gen1 fiókdiagnosztikai naplózását, hogy adatokat gyűjtsenek a naplózási naplókból, amelyek olyan információkat szolgáltatnak, mint például az adatokhoz hozzáférő felhasználók listája, az adatok elérésének gyakori, a fiókban tárolt adatok száma stb. Ha engedélyezve van, a diagnosztika és/vagy a kérelmek a legjobb erőfeszítés alapján kerülnek naplózásra. Mind a kérelmek, mind a diagnosztikai naplóbejegyzések csak akkor jönnek létre, ha a szolgáltatás végpontja ellen kérelmek vannak.

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Azure Data Lake Storage Gen1 fiók.** Kövesse az [Azure Data Lake Storage Gen1 használatának első](data-lake-store-get-started-portal.md)lépéseit az Azure Portal használatával című útmutatóban.

## <a name="enable-diagnostic-logging-for-your-data-lake-storage-gen1-account"></a>Diagnosztikai naplózás engedélyezése a Data Lake Storage Gen1 fiókhoz
1. Jelentkezzen be az új [Azure Portalra](https://portal.azure.com).
2. Nyissa meg a Data Lake Storage Gen1 fiókját, és a Data Lake Storage Gen1 fiók panelen kattintson a **Diagnosztikai beállítások gombra.**
3. A **Diagnosztika beállításai** panelen kattintson **a Diagnosztika bekapcsolása gombra.**

    ![Diagnosztikai naplózás engedélyezése](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "Diagnosztikai naplók engedélyezése")

3. A Diagnosztikai beállítások panelen **hajtsa** végre a következő módosításokat a diagnosztikai naplózás konfigurálásához.
   
    ![Diagnosztikai naplózás engedélyezése](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Diagnosztikai naplók engedélyezése")
   
   * A **Név mezőbe**írja be a diagnosztikai napló konfigurációjának értékét.
   * Az adatokat különböző módokon tárolhatja/dolgozhatja fel.
     
        * Válassza ki a **lehetőséget, hogy archiválja a tárfiókban** a naplók tárolására egy Azure Storage-fiók. Ezt a beállítást akkor használja, ha archiválni szeretné a kötegelt feldolgozásra kerülő adatokat egy későbbi időpontban. Ha ezt a lehetőséget választja, meg kell adnia egy Azure Storage-fiókot a naplók mentéséhez.
        
        * Válassza ki azt a lehetőséget, **hogy streameljen egy eseményközpontba** a naplóadatok egy Azure Event Hubba való streameléséhez. Valószínűleg akkor fogja használni ezt a lehetőséget, ha rendelkezik egy alsóbb rétegbeli feldolgozási folyamattal a bejövő naplók valós időben történő elemzéséhez. Ha ezt a lehetőséget választja, meg kell adnia a használni kívánt Azure Event Hub adatait.

        * Válassza ki a **küldés a Log Analytics** szolgáltatásba lehetőséget az Azure Monitor szolgáltatás használatához a létrehozott naplóadatok elemzéséhez. Ha ezt a lehetőséget választja, meg kell adnia a Log Analytics munkaterület részleteit, amelyet a naplóelemzés végrehajtásával használna. Lásd: [Az Azure Monitor naplóival gyűjtött adatok megtekintése vagy elemzése az](../azure-monitor/learn/tutorial-viewdata.md) Azure Monitor-naplók használatával kapcsolatos részletekért.
     
   * Adja meg, hogy naplónaplókat, vagy kérésnaplókat szeretne-e kapni, vagy mindkettőt.
   * Adja meg, hogy hány napig őrizze meg az adatokat. Adatmegőrzés csak akkor alkalmazható, ha az Azure storage-fiók segítségével archiválja a naplóadatokat.
   * Kattintson a **Mentés** gombra.

Miután engedélyezte a diagnosztikai beállításokat, megtekintheti a naplókat a **Diagnosztikai naplók** lapon.

## <a name="view-diagnostic-logs-for-your-data-lake-storage-gen1-account"></a>A Data Lake Storage Gen1 fiók diagnosztikai naplóinak megtekintése
A Data Lake Storage Gen1 fiók naplóadatainak megtekintésére kétféleképpen tekintheti meg.

* A Data Lake Storage Gen1 fiókbeállításnézetből
* Az Azure Storage-fiókból, ahol az adatok at tárolják

### <a name="using-the-data-lake-storage-gen1-settings-view"></a>A Data Lake Storage Gen1 beállítások nézetének használata
1. A Data Lake Storage Gen1 fiók **beállítások** paneljén kattintson **a Diagnosztikai naplók**elemre.
   
    ![Diagnosztikai naplók megtekintése](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "Diagnosztikai naplók megtekintése") 
2. A **Diagnosztikai naplók** panelen a **naplók** és a naplók kérések szerint kategorizált naplókat kell **látnia.**
   
   * A kérelemnaplók rögzítik a Data Lake Storage Gen1 fiókon elvégzett összes API-kérelmet.
   * A naplónaplók hasonlóak a naplók kérése, de a Data Lake Storage Gen1 fiókon végrehajtott műveletek sokkal részletesebb bontását biztosítják. Például egy feltöltési API-hívás kérelem naplók eredményezhet több "hozzáfűzése" műveletek a naplónaplókban.
3. A naplók letöltéséhez kattintson a **Letöltés** hivatkozásra az egyes naplóbejegyzésekhez.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>A naplóadatokat tartalmazó Azure Storage-fiókból
1. Nyissa meg az Azure Storage-fiók panel társított Data Lake Storage Gen1 naplózáshoz, és kattintson a Blobs. A **Blob szolgáltatás** panel két tárolót sorol fel.
   
    ![Diagnosztikai naplózás megtekintése](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Diagnosztikai naplók megtekintése")
   
   * A tároló **insights-logs-audit** tartalmazza a naplónaplókat.
   * A tároló **insights-logs-kérelmek** tartalmazzák a kérelem naplók.
2. Ezeken a tárolókon belül a naplók a következő struktúrában tárolódnak.
   
    ![Diagnosztikai naplózás megtekintése](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Diagnosztikai naplók megtekintése")
   
    Például a napló teljes elérési útja`https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    Hasonlóképpen, a kérelemnapló teljes elérési útja`https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>A naplóadatok szerkezetének megismerése
A naplózási és kérésnaplók JSON formátumúak. Ebben a szakaszban a JSON struktúráját tekintjük meg a kérelmek és a naplózási naplók.

### <a name="request-logs"></a>Naplók kérése
Az alábbiakban egy mintabejegyzés található a JSON-formátumú kérelemnaplóban. Minden blob rendelkezik egy **gyökérobjektummal, amelyet rekordoknak** neveznek, és amely naplóobjektumok tömbjét tartalmazza.

    {
    "records": 
      [        
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
             "category": "Requests",
             "operationName": "GETCustomerIngressEgress",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z"}
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
| operationName |Sztring |A naplózott művelet neve. Például getfilestatus. |
| resultType |Sztring |A művelet állapota, például 200. |
| callerIpAddress |Sztring |A kérést küldő ügyfél IP-címe |
| correlationId |Sztring |A kapcsolódó naplóbejegyzések csoportjának csoportosítására használható napló azonosítója |
| identity |Objektum |A naplót létrehozó identitás |
| properties |JSON |Lásd alább a részleteket |

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
Az alábbiakban egy mintabejegyzés található a JSON-formátumú naplóban. Minden blobhoz tartozik egy rekord **nevű** gyökérobjektum, amely naplóobjektumok tömbjét tartalmazza

    {
    "records": 
      [        
        . . . .
        ,
        {
             "time": "2016-07-08T19:08:59.359Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
             "category": "Audit",
             "operationName": "SeOpenStream",
             "resultType": "0",
             "resultSignature": "0",
             "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
             "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
             "properties": {"StreamName":"adl://<data_lake_storage_gen1_account_name>.azuredatalakestore.net/logs.csv"}
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
| operationName |Sztring |A naplózott művelet neve. Például getfilestatus. |
| resultType |Sztring |A művelet állapota, például 200. |
| resultSignature |Sztring |További részletek a műveletről. |
| correlationId |Sztring |A kapcsolódó naplóbejegyzések csoportjának csoportosítására használható napló azonosítója |
| identity |Objektum |A naplót létrehozó identitás |
| properties |JSON |Lásd alább a részleteket |

#### <a name="audit-log-properties-schema"></a>Naplótulajdonságainak sémája
| Név | Típus | Leírás |
| --- | --- | --- |
| StreamName (Adatfolyamneve) |Sztring |Az az útvonal, amelyen a műveletet végrehajtották |

## <a name="samples-to-process-the-log-data"></a>Minták a naplóadatok feldolgozásához
Amikor naplókat küld az Azure Data Lake Storage Gen1-től az Azure Monitor-naplókba (lásd: [Az Azure Monitor naplóival gyűjtött adatok megtekintése vagy elemzése](../azure-monitor/learn/tutorial-viewdata.md) az Azure Monitor naplóinak részleteire vonatkozóan), a következő lekérdezés egy olyan táblázatot ad vissza, amely tartalmazza a felhasználói megjelenítendő nevek listáját, az események idejét és az események számát az esemény időpontjában egy vizuális diagrammal együtt. Könnyen módosítható a felhasználói guid vagy más attribútumok megjelenítéséhez:

```
search *
| where ( Type == "AzureDiagnostics" )
| summarize count(TimeGenerated) by identity_s, TimeGenerated
```


Az Azure Data Lake Storage Gen1 mintaként szolgál a naplóadatok feldolgozásához és elemzéséhez. A mintát a. [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample) 

## <a name="see-also"></a>Lásd még
* [Az Azure Data Lake storage gen1 áttekintése](data-lake-store-overview.md)
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)

