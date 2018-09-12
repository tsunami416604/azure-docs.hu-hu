---
title: Diagnosztikai naplók megtekintése az Azure Data Lake Storage Gen1 |} A Microsoft Docs
description: 'Megtudhatja, hogyan beállítása és az Azure Data Lake Storage Gen1 diagnosztikai naplók elérése '
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: f6e75eb1-d0ae-47cf-bdb8-06684b7c0a94
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: 91cbebecc227d24337b2d1b421dd1611bf0fac46
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44390796"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-storage-gen1"></a>Az Azure Data Lake Storage Gen1 diagnosztikai naplóinak elérése
Ismerje meg, diagnosztikai naplózás az Azure Data Lake Storage Gen1 fiókját és a fiókja gyűjtött naplók megtekintése a engedélyezéséhez.

Szervezetek számára engedélyezheti a diagnosztikai naplózás az Azure Data Lake Storage Gen1 fiókjuk gyűjthet adatokat a fájlhozzáférés napló ellenőrzését, hogy a felhasználók az adatokat, hogy milyen gyakran az adatokhoz, például a lista információkat nyújt a tárolt adatok mennyiségét a fiók stb. Ha engedélyezve van, a diagnosztikai és/vagy a kérések jelentkezett be egy képességeinkhez mérten biztosítjuk. Kérelmek és a diagnosztika naplóbejegyzések jönnek létre, csak akkor, ha nincsenek a szolgáltatásvégpont kérelmekre.

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Az Azure Data Lake Storage Gen1 fiók**. Kövesse az utasításokat, [Azure Data Lake Storage Gen1 használatának első lépései az Azure Portal használatával](data-lake-store-get-started-portal.md).

## <a name="enable-diagnostic-logging-for-your-data-lake-storage-gen1-account"></a>A Data Lake Storage Gen1 fiók diagnosztikai naplózás engedélyezése
1. Jelentkezzen be az új [Azure Portalra](https://portal.azure.com).
2. Nyissa meg a Data Lake Storage Gen1 fiókját, és a Data Lake Storage Gen1 fiók panelen kattintson az **diagnosztikai beállítások**.
3. Az a **diagnosztikai beállítások** panelen kattintson a **diagnosztika bekapcsolása**.

    ![Diagnosztikai naplózás engedélyezése](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "diagnosztikai naplók engedélyezése")

3. Az a **diagnosztikai beállítások** panelen a következő módosításokat a diagnosztikai naplózás beállítása.
   
    ![Diagnosztikai naplózás engedélyezése](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "diagnosztikai naplók engedélyezése")
   
   * A **neve**, adjon meg egy értéket a diagnosztikai naplót a konfigurációhoz.
   * Az adatok tárolási/folyamat különböző módon lehet váltani.
     
        * Válassza a **archiválás tárfiókba** egy Azure Storage-fiókot a naplók tárolásához. Ha az adatokat, amelyek lesznek a batch-feldolgozása egy későbbi időpontban archiválni szeretné ezt a beállítást használja. Ha ezt a beállítást meg kell adnia egy Azure Storage-fiók mentése a naplókat.
        
        * Válassza a **Stream egy eseményközpontba** a naplózási adatok streamelése az Azure-Eseményközpontba. Valószínűleg ez a beállítás fogja használni, ha rendelkezik egy alárendelt feldolgozási beérkező naplók valós időben elemezheti. Ha ezt a lehetőséget választja, meg kell adnia a részletek az Azure Event Hubs is használni szeretné a.

        * Válassza a **Küldés a Log Analyticsnek** az Azure Log Analytics szolgáltatás használata a létrehozott naplóadatok elemzéséhez. Ezt a beállítást, ha meg kell adnia a részletek a Log Analytics-munkaterületen, melyeket használhat a végrehajtása elemzése. Lásd: [megtekintése vagy elemzése a Log Analytics naplóbeli keresés gyűjtött adatok](../log-analytics/log-analytics-tutorial-viewdata.md) Log Analytics használatával.
     
   * Adja meg, hogy szeretné-e a vizsgálati naplók kérelmekről készült naplók és/vagy beolvasása.
   * Adja meg a napok, amelynek az adatokat meg kell őrizni. Adatmegőrzési csak akkor alkalmazható, ha a Teljesítménynapló-adatok archiválása az Azure storage-fiókot használ.
   * Kattintson a **Save** (Mentés) gombra.

Miután engedélyezte a diagnosztikai beállítások, a naplókat is tekintse meg a **diagnosztikai naplók** fülre.

## <a name="view-diagnostic-logs-for-your-data-lake-storage-gen1-account"></a>A Data Lake Storage Gen1 fiók diagnosztikai naplók megtekintése
Kétféleképpen a naplóadatok a Data Lake Storage Gen1 fiók megtekintéséhez.

* A Data Lake Storage Gen1 a Fiókbeállítások megtekintéséhez
* Az adatokat tároló Azure Storage-fiók

### <a name="using-the-data-lake-storage-gen1-settings-view"></a>A Data Lake Storage Gen1 beállítások nézettel
1. A Data Lake Storage Gen1 fiókból **beállítások** panelen kattintson a **diagnosztikai naplók**.
   
    ![Diagnosztikai naplók megtekintése](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "diagnosztikai naplók megtekintése") 
2. Az a **diagnosztikai naplók** panelen megjelenik a naplókban az alapján kategorizáljuk **Auditnaplók** és **kérelem naplók**.
   
   * Kérelmekről készült naplók rögzítése a Data Lake Storage Gen1 fiók végrehajtott minden egyes API-kérelem.
   * Auditnaplók hasonlóak kérelem naplók, de a Data Lake Storage Gen1 fiók végrehajtott műveletek sokkal részletes áttekintést nyújt. Ha például egy egyetlen feltöltéssel kérelmekről készült naplók az API-hívással több "Hozzáfűzés" műveletek naplóiban eredményezhet.
3. A naplók letöltéséhez kattintson a **letöltése** hivatkozás minden naplóbejegyzés ellen.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>Az Azure Storage-fiókból naplóadatokat tartalmazó
1. Nyissa meg a naplózási Data Lake Storage Gen1 társított Azure Storage-fiók panelen, és kattintson a Blobok. A **Blob service** panel felsorolja a két tárolót.
   
    ![Diagnosztikai naplózás nézet](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "diagnosztikai naplók megtekintése")
   
   * A tároló **insights-logs-naplózási** tartalmazza a naplófájlok.
   * A tároló **insights-logs-kérelmek** tartalmazza a kérés naplókat.
2. Ezek a tárolók belül a naplók az alábbi struktúrával alatt tárolja.
   
    ![Diagnosztikai naplózás nézet](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "diagnosztikai naplók megtekintése")
   
    Tegyük fel az auditnapló teljes elérési útja lehet `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    Hasonlóképpen egy kérelem naplófájl teljes elérési útja lehet `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>A naplóadatok szerkezete ismertetése
A naplózási és a kérés naplók JSON formátumban vannak. Ebben a szakaszban azt nézzük kérelem JSON szerkezetének és naplók.

### <a name="request-logs"></a>Kérelmekről készült naplók
Íme egy minta-bejegyzést a JSON-formátumú kérelem naplóban. Minden egyes blob van egy gyökérobjektum nevű **rekordok** log objektumok egy tömbjét tartalmazza.

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

#### <a name="request-log-schema"></a>Kérelem séma
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| time |Sztring |A napló időbélyegzője (az UTC) |
| resourceId |Sztring |Helyezze a művelet igénybe vett erőforrás azonosítója |
| category |Sztring |A naplózási kategóriát. Ha például **kérelmek**. |
| operationName |Sztring |A művelet, amelyet a rendszer neve. Ha például getfilestatus. |
| resultType |Sztring |A művelet, például 200-as állapotát. |
| callerIpAddress |Sztring |Az ügyfél a kérés IP-címe |
| correlationId |Sztring |Csoportosíthatja a kapcsolódó bejegyzései készletét használja, amely a napló azonosítója |
| identity |Objektum |Az identitás által generált napló |
| properties |JSON |Részletes információkat lejjebb talál |

#### <a name="request-log-properties-schema"></a>Kérelem tulajdonságai séma
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| HttpMethod |Sztring |A művelethez használt HTTP-metódust. Például KAPHAT. |
| Útvonal |Sztring |Az elérési utat a művelet végrehajtásának ideje |
| RequestContentLength |int |A HTTP-kérelem tartalmának hossza |
| ClientRequestId |Sztring |Az azonosítója, amely egyedileg azonosítja ezt a kérelmet |
| Kezdés időpontja |Sztring |Az idő, amelyen a kiszolgáló a kérelem érkezett |
| Befejezés időpontja: |Sztring |Az idő, amikor a kiszolgáló által küldött választ |

### <a name="audit-logs"></a>Naplók
Íme egy minta-bejegyzést a JSON-formátumú naplóban. Minden egyes blob van egy gyökérobjektum nevű **rekordok** , amely tartalmazza a napló objektumokból álló tömb

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

#### <a name="audit-log-schema"></a>Naplózási séma
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| time |Sztring |A napló időbélyegzője (az UTC) |
| resourceId |Sztring |Helyezze a művelet igénybe vett erőforrás azonosítója |
| category |Sztring |A naplózási kategóriát. Ha például **naplózási**. |
| operationName |Sztring |A művelet, amelyet a rendszer neve. Ha például getfilestatus. |
| resultType |Sztring |A művelet, például 200-as állapotát. |
| resultSignature |Sztring |További információt a műveletet. |
| correlationId |Sztring |Csoportosíthatja a kapcsolódó bejegyzései készletét használja, amely a napló azonosítója |
| identity |Objektum |Az identitás által generált napló |
| properties |JSON |Részletes információkat lejjebb talál |

#### <a name="audit-log-properties-schema"></a>Naplózási tulajdonságok séma
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| StreamName |Sztring |Az elérési utat a művelet végrehajtásának ideje |

## <a name="samples-to-process-the-log-data"></a>A minták a naplózási adatok feldolgozása
Amikor naplókat küld az Azure Data Lake Storage Gen1 Azure Log Analytics (lásd: [megtekintése vagy elemzése a Log Analytics naplóbeli keresés gyűjtött adatok](../log-analytics/log-analytics-tutorial-viewdata.md) Log Analytics használatával), a következő lekérdezés listáját tartalmazó táblát adja vissza felhasználó megjelenített neveket, az idő az eseményeket, és az események száma egy vizuális diagramban együtt az esemény alkalommal. Könnyedén módosítható megjelenítéséhez a felhasználó GUID-azonosítója vagy más jellemzők:

```
search *
| where ( Type == "AzureDiagnostics" )
| summarize count(TimeGenerated) by identity_s, TimeGenerated
```


Az Azure Data Lake Storage Gen1 nyújt egy minta a naplózási adatok feldolgozásához és elemzéséhez. Találja, a minta [ https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample ](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample). 

## <a name="see-also"></a>Lásd még
* [Az Azure Data Lake Storage Gen1 áttekintése](data-lake-store-overview.md)
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)

