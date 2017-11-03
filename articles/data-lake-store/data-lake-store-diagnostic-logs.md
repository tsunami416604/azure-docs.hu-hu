---
title: "Az Azure Data Lake Store diagnosztikai naplók megtekintése |} Microsoft Docs"
description: "Ismerje meg, hogyan kell beállítania, és hozzáférés az Azure Data Lake Store diagnosztikai naplók "
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: f6e75eb1-d0ae-47cf-bdb8-06684b7c0a94
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 73d0dabe5b8b179cbc0847c2819947febd6ef4d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-store"></a>Diagnosztikai naplók az Azure Data Lake Store elérése
További diagnosztikai a Data Lake Store-fiók és a fiókja gyűjtött naplók megtekintéséhez a naplózás engedélyezéséhez.

A szervezetek diagnosztikai naplózását is az Azure Data Lake Store fiók gyűjthet adatokat a fájlhozzáférés napló ellenőrzését, amely bemutatja, például a listát a felhasználók fér hozzá az adatokhoz, hogy milyen gyakran az adatokhoz, mennyi adatot a fiók tárolva van stb.

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Azure Data Lake Store-fiók**. Kövesse [Az Azure Data Lake Store használatának első lépései az Azure Portal használatával](data-lake-store-get-started-portal.md) című témakör utasításait.

## <a name="enable-diagnostic-logging-for-your-data-lake-store-account"></a>A Data Lake Store-fiók diagnosztikai naplózás engedélyezése
1. Jelentkezzen be az új [Azure Portalra](https://portal.azure.com).
2. Nyissa meg a Data Lake Store-fiókot, és a Data Lake Store-fiók panelen kattintson **beállítások**, és kattintson a **diagnosztikai naplók**.
3. Az a **diagnosztikai naplók** panelen kattintson a **a diagnosztika bekapcsolásához**.

    ![Diagnosztikai naplózás engedélyezése](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "diagnosztikai naplók engedélyezése")

3. Az a **diagnosztikai** panelen diagnosztikai naplózás konfigurálása a következő módosításokat.
   
    ![Diagnosztikai naplózás engedélyezése](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "diagnosztikai naplók engedélyezése")
   
   * A **neve**, adjon meg egy értéket a diagnosztikai naplófájl-konfiguráció.
   * Ha szeretné, az adatok tárolási/folyamat más módon.
     
        * Jelölje be a **tárfiókba archív** bejegyzéseit, amelyek egy Azure Storage-fiók tárolásához. Ha az adatokat, amelyek lesznek kötegelt feldolgozásra egy későbbi időpontban archiválni szeretné ezt a beállítást használja. Ha ezt a beállítást meg kell adnia egy Azure Storage-fiók mentése a naplókat.
        
        * Jelölje be a **adatfolyam egy eseményközpontba** adatfolyam napló adatokat az Azure-Eseményközpontok felé. Valószínűleg ezt a beállítást fogja használni, ha egy alárendelt feldolgozási folyamat bejövő naplók valós időben elemezni. Ha ezt a lehetőséget választja, meg kell adnia a használni kívánt Azure Event Hubs részleteit.

        * Jelölje be a **küldeni a Naplóelemzési** használhatja az Azure Naplóelemzés szolgáltatást a előállított naplózási adatok elemzésére. Ha ezt a lehetőséget választja, meg kell adnia a részletek az Operations Management Suite-munkaterülettel a végezze el a webhelynapló elemzése használható.
     
   * Adja meg, hogy megkapják a naplók vagy kérelmek naplóit vagy mindkettőt.
   * Adja meg, hány nap, amelynek meg kell őrizni az adatokat. Megőrzési csak akkor alkalmazható, ha az Azure storage-fiók segítségével archiválja naplóadatokat.
   * Kattintson a **Save** (Mentés) gombra.

Miután engedélyezte a diagnosztikai beállítások, a naplófájlok az figyelemmel követheti a **diagnosztikai naplók** fülre.

## <a name="view-diagnostic-logs-for-your-data-lake-store-account"></a>A Data Lake Store-fiók diagnosztikai naplók megtekintése
A Data Lake Store-fiók a naplóadatok megtekintéséhez két módja van.

* A Data Lake Store-fiókból beállítások megtekintése
* Az adatokat tároló Azure Storage-fiókból

### <a name="using-the-data-lake-store-settings-view"></a>Használatával a Data Lake Store beállítások megtekintése
1. A Data Lake Store-fiókból **beállítások** panelen kattintson a **diagnosztikai naplók**.
   
    ![Diagnosztikai naplózás nézet](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "diagnosztikai naplók megtekintése") 
2. Az a **diagnosztikai naplók** panelen megjelenik a naplók kategorizálta **naplók** és **kérelem naplók**.
   
   * Kérelem naplók rögzítése a Data Lake Store-fiók minden API kérelmet.
   * Naplók hasonlóak naplók kérése, de a műveletek végrehajtása a Data Lake Store-fiók sokkal részletesebb információkat biztosít. Például egy egyetlen feltöltés API-hívás a kérelem naplókban több "Append" műveletet a naplófájlban eredményezheti.
3. A naplók letöltéséhez kattintson a **letöltése** hivatkozás minden naplóbejegyzés ellen.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>Az Azure Storage-fiókhoz, amely tartalmazza adatainak naplózása
1. Nyissa meg a naplózás a Data Lake Store társított Azure Storage-fiók panelen, és kattintson a Blobok. A **Blob szolgáltatás** panel két tárolók listája.
   
    ![Diagnosztikai naplózás nézet](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "diagnosztikai naplók megtekintése")
   
   * A tároló **insights-logs-naplózási** tartalmazza a naplókat.
   * A tároló **insights-logs-kérelmek** tartalmaz a kérelmek naplóit.
2. Ezek a tárolók belül a naplók tárolt az alábbi szerkezettel.
   
    ![Diagnosztikai naplózás nézet](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "diagnosztikai naplók megtekintése")
   
    Tegyük fel a teljes elérési útját, és egy naplófájlba lehet`https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    Hasonlóképpen a teljes elérési útját a napló lehet`https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>A naplózási adatok szerkezete ismertetése
A naplózási és kérelem naplók JSON formátumban vannak. Ez a szakasz azt nézze meg a kérelem JSON szerkezete és a naplók.

### <a name="request-logs"></a>Naplók kérése
Íme egy minta-bejegyzést a JSON-formátumú kérelem naplóban. Minden egyes blob tartozik egy legfelső szintű objektum nevű **rekordok** , amely tartalmazza a napló objektumokból álló tömb.

    {
    "records": 
      [        
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
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
| Név | Típus | Leírás |
| --- | --- | --- |
| time |Karakterlánc |Az időbélyeg (UTC szerint) a napló |
| resourceId |Karakterlánc |Helyezze a művelet erőforrás azonosítója |
| category |Karakterlánc |A napló kategóriát. Például **kérelmek**. |
| operationName |Karakterlánc |A művelet naplózott neve. Például getfilestatus. |
| resultType |Karakterlánc |A művelet, például 200 állapotát. |
| callerIpAddress |Karakterlánc |A kérést küldő ügyfél IP-címe |
| correlationId |Karakterlánc |A napló, amelyek azonosítója használt csoportba a kapcsolódó naplóbejegyzések készlete |
| identity |Objektum |Az identitás, amely a napló jön létre |
| properties |JSON |További információ alább olvasható |

#### <a name="request-log-properties-schema"></a>Kérelem tulajdonságok séma
| Név | Típus | Leírás |
| --- | --- | --- |
| HttpMethod |Karakterlánc |A művelethez használt HTTP-metódust. Például beolvasása. |
| Útvonal |Karakterlánc |Az elérési út a művelet végrehajtásának ideje |
| RequestContentLength |int |A HTTP-kérelem a tartalom hossza |
| clientRequestId |Karakterlánc |Az azonosító, amely egyedileg azonosítja az ehhez a kérelemhez |
| Kezdő időpont |Karakterlánc |Az a kiszolgáló fogadja a kérelem ideje |
| Befejezés időpontja |Karakterlánc |Az idő, ahol a kiszolgáló által küldött választ |

### <a name="audit-logs"></a>Naplók
Íme egy minta-bejegyzést a JSON-formátumú naplóban. Minden egyes blob tartozik egy legfelső szintű objektum nevű **rekordok** napló objektumok tömbjét tartalmazza, amelyek

    {
    "records": 
      [        
        . . . .
        ,
        {
             "time": "2016-07-08T19:08:59.359Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Audit",
             "operationName": "SeOpenStream",
             "resultType": "0",
             "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
             "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
             "properties": {"StreamName":"adl://<data_lake_store_account_name>.azuredatalakestore.net/logs.csv"}
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>Naplózási séma
| Név | Típus | Leírás |
| --- | --- | --- |
| time |Karakterlánc |Az időbélyeg (UTC szerint) a napló |
| resourceId |Karakterlánc |Helyezze a művelet erőforrás azonosítója |
| category |Karakterlánc |A napló kategóriát. Például **naplózási**. |
| operationName |Karakterlánc |A művelet naplózott neve. Például getfilestatus. |
| resultType |Karakterlánc |A művelet, például 200 állapotát. |
| correlationId |Karakterlánc |A napló, amelyek azonosítója használt csoportba a kapcsolódó naplóbejegyzések készlete |
| identity |Objektum |Az identitás, amely a napló jön létre |
| properties |JSON |További információ alább olvasható |

#### <a name="audit-log-properties-schema"></a>Naplózási tulajdonságai séma
| Név | Típus | Leírás |
| --- | --- | --- |
| StreamName |Karakterlánc |Az elérési út a művelet végrehajtásának ideje |

## <a name="samples-to-process-the-log-data"></a>A naplózási adatok feldolgozása a minták
Azure Data Lake Store minta hogyan feldolgozhatja és elemezheti a naplózási adatokat biztosít. A minta a található [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample). 

## <a name="see-also"></a>Lásd még:
* [Az Azure Data Lake Store áttekintése](data-lake-store-overview.md)
* [Biztonságos adattárolás a Data Lake Store-ban](data-lake-store-secure-data.md)

