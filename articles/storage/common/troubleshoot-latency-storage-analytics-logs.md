---
title: Késési problémák elhárítása Storage Analytics-naplók használatával
description: Azonosíthatja és elháríthatja az Azure Storage analitikai naplóival kapcsolatos késési problémákat, és optimalizálhatja az ügyfélalkalmazás használatát.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/21/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: 2197a149235c0dca98a24a57549538b2a4cbb1c8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "74196506"
---
# <a name="troubleshoot-latency-using-storage-analytics-logs"></a>Késési problémák elhárítása Storage Analytics-naplók használatával

A diagnosztizálás és a hibaelhárítás kulcsfontosságú képesség az ügyfélalkalmazások Azure Storage szolgáltatással történő létrehozásához és támogatásához.

Az Azure-alkalmazások elosztott jellegéből adódóan a hibák és a teljesítménnyel kapcsolatos problémák diagnosztizálása és hibaelhárítása összetettebb lehet, mint a hagyományos környezetekben.

Az alábbi lépések bemutatják, hogyan azonosíthatja és elháríthatja a késéssel kapcsolatos problémákat az Azure Storage analitikus naplóival, és hogyan optimalizálhatja az ügyfélalkalmazás használatát.

## <a name="recommended-steps"></a>Javasolt lépések

1. Töltse le a [Storage Analytics naplókat](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#download-storage-logging-log-data).

2. A következő PowerShell-parancsfájl használatával alakítsa át a nyers formátumú naplókat táblázatos formátumba:

   ```Powershell
   $Columns = 
        (   "version-number",
            "request-start-time",
            "operation-type",
            "request-status",
            "http-status-code",
            "end-to-end-latency-in-ms",
            "server-latency-in-ms",
            "authentication-type",
            "requester-account-name",
            "owner-account-name",
            "service-type",
            "request-url",
            "requested-object-key",
            "request-id-header",
            "operation-count",
            "requester-ip-address",
            "request-version-header",
            "request-header-size",
            "request-packet-size",
            "response-header-size",
            "response-packet-size",
            "request-content-length",
            "request-md5",
            "server-md5",
            "etag-identifier",
            "last-modified-time",
            "conditions-used",
            "user-agent-header",
            "referrer-header",
            "client-request-id"
        )

   $logs = Import-Csv “REPLACE THIS WITH FILE PATH” -Delimiter ";" -Header $Columns

   $logs | Out-GridView -Title "Storage Analytic Log Parser"
   ```

3. A szkript elindít egy grafikus felhasználói felületet tartalmazó ablakot, amelyen az alábbi ábrán látható módon szűrheti az adatokat oszlopok alapján.

   ![Storage analitikus napló elemző ablaka](media/troubleshoot-latency-storage-analytics-logs/storage-analytic-log-parser-window.png)
 
4. Szűkítse le a naplóbejegyzéseket a "művelet típusa" alapján, és keresse meg a probléma időbeli keretében létrehozott naplóbejegyzést.

   ![Művelet típusú naplóbejegyzések](media/troubleshoot-latency-storage-analytics-logs/operation-type.png)

5. A probléma bekövetkezésének ideje alatt a következő értékek fontosak:

   * Művelet – típus = GetBlob
   * kérelem – állapot = SASNetworkError
   * Végpontok közötti késleltetés – ms = 8453
   * Kiszolgáló – késés – MS = 391

   A végpontok közötti késleltetés kiszámítása a következő egyenlet használatával történik:

   * Végpontok közötti késés = kiszolgáló-késés + ügyfél késése

   Az ügyfél késésének kiszámítása a naplóbejegyzés használatával:

   * Ügyfél késése = végpontok közötti késés – kiszolgáló – késés

          * Example: 8453 – 391 = 8062ms

   A következő táblázat a nagy késleltetésű OperationType és a RequestStatus eredményekkel kapcsolatos információkat tartalmaz:

   |   |RequestStatus =<br>Sikeres|RequestStatus =<br>Sas NetworkError|Ajánlás|
   |---|---|---|---|
   |GetBlob|Igen|Nem|[**GetBlob művelet:** RequestStatus = sikeres](#getblob-operation-requeststatus--success)|
   |GetBlob|Nem|Igen|[**GetBlob művelet:** RequestStatus = (SAS) NetworkError](#getblob-operation-requeststatus--sasnetworkerror)|
   |PutBlob|Igen|Nem|[**Put művelet:** RequestStatus = sikeres](#put-operation-requeststatus--success)|
   |PutBlob|Nem|Igen|[**Put művelet:** RequestStatus = (SAS) NetworkError](#put-operation-requeststatus--sasnetworkerror)|

## <a name="status-results"></a>Állapot eredményei

### <a name="getblob-operation-requeststatus--success"></a>GetBlob művelet: RequestStatus = sikeres

A "javasolt lépések" szakasz 5. lépésében említettek szerint tekintse meg az alábbi értékeket:

* Végpontok közötti késés
* Kiszolgáló – késés
* Ügyfél – késés

A **RequestStatus = sikerrel**rendelkező **GetBlob-műveletben** , ha a **maximális idő** az **ügyfél-késésben**van elköltve, ez azt jelzi, hogy az Azure Storage nagy mennyiségű időt tölt ki az ügyfélnek az adatírás során. Ez a késleltetés ügyféloldali problémát jelez.

**Ajánlás**

* Vizsgálja meg a kódot az ügyfélen.
* A Wireshark, a Microsoft Message Analyzer vagy a Tcping használatával vizsgálja meg az ügyfél hálózati kapcsolati problémáit. 

### <a name="getblob-operation-requeststatus--sasnetworkerror"></a>GetBlob művelet: RequestStatus = (SAS) NetworkError

A "javasolt lépések" szakasz 5. lépésében említettek szerint tekintse meg az alábbi értékeket:

* Végpontok közötti késés
* Kiszolgáló – késés
* Ügyfél – késés

A **RequestStatus = (SAS) NetworkError**rendelkező **GetBlob-műveletekben** , ha a **maximális idő** az **ügyfél-késésben**van elköltve, a leggyakoribb probléma az, hogy az ügyfél le van választva, mielőtt lejár az időkorlát a tárolási szolgáltatásban.

**Ajánlás**

* Vizsgálja meg az ügyfél kódját, hogy megtudja, miért és mikor szakad meg az ügyfél a Storage szolgáltatással.
* A Wireshark, a Microsoft Message Analyzer vagy a Tcping használatával vizsgálja meg az ügyfél hálózati kapcsolati problémáit. 

### <a name="put-operation-requeststatus--success"></a>Put művelet: RequestStatus = sikeres

A "javasolt lépések" szakasz 5. lépésében említettek szerint tekintse meg az alábbi értékeket:

* Végpontok közötti késés
* Kiszolgáló – késés
* Ügyfél – késés

Ha a **RequestStatus = sikerrel**rendelkező **put művelet** során a **maximális idő** az **ügyfél-késésben**van, akkor ez azt jelzi, hogy az ügyfél több időt vesz igénybe az Azure Storage-ba való adatküldéshez. Ez a késleltetés ügyféloldali problémát jelez.

**Ajánlás**

* Vizsgálja meg a kódot az ügyfélen.
* A Wireshark, a Microsoft Message Analyzer vagy a Tcping használatával vizsgálja meg az ügyfél hálózati kapcsolati problémáit. 

### <a name="put-operation-requeststatus--sasnetworkerror"></a>Put művelet: RequestStatus = (SAS) NetworkError

A "javasolt lépések" szakasz 5. lépésében említettek szerint tekintse meg az alábbi értékeket:

* Végpontok közötti késés
* Kiszolgáló – késés
* Ügyfél – késés

A **RequestStatus = (SAS) NetworkError**rendelkező **PutBlob-műveletekben** , ha a **maximális idő** az **ügyfél-késésben**van elköltve, a leggyakoribb probléma az, hogy az ügyfél le van választva, mielőtt lejár az időkorlát a tárolási szolgáltatásban.

**Ajánlás**

* Vizsgálja meg az ügyfél kódját, hogy megtudja, miért és mikor szakad meg az ügyfél a Storage szolgáltatással.
* A Wireshark, a Microsoft Message Analyzer vagy a Tcping használatával vizsgálja meg az ügyfél hálózati kapcsolati problémáit.

