---
title: Késési problémák elhárítása Storage Analytics-naplók használatával
description: Azonosítsa és hárítsa el a késési problémákat az Azure Storage analitikus naplóival, és optimalizálja az ügyfélalkalmazást.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196506"
---
# <a name="troubleshoot-latency-using-storage-analytics-logs"></a>Késési problémák elhárítása Storage Analytics-naplók használatával

Diagnosztizálása és hibaelhárítás a kulcsfontosságú készség az azure storage-beli ügyfélalkalmazások létrehozásához és támogatásához.

Az Azure-alkalmazások elosztott jellege miatt a hibák és a teljesítményproblémák diagnosztizálása és hibaelhárítása összetettebb lehet, mint a hagyományos környezetekben.

Az alábbi lépések bemutatják, hogyan azonosíthatja és elháríthatja a késési problémákat az Azure Storage analitikus naplóival, és hogyan optimalizálhatja az ügyfélalkalmazást.

## <a name="recommended-steps"></a>Javasolt lépések

1. Töltse le a [Storage Analytics naplókat.](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#download-storage-logging-log-data)

2. A következő PowerShell-parancsfájl segítségével konvertálja a nyers formátumú naplókat táblázatos formátumba:

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

3. A szkript indít egy GUI ablak, ahol szűrheti az információkat oszlopok, az alábbiak szerint.

   ![Tároló elemző eszköz tárolóablak](media/troubleshoot-latency-storage-analytics-logs/storage-analytic-log-parser-window.png)
 
4. Szűkítse le a naplóbejegyzéseket a "művelettípus" alapján, és keresse meg a probléma időkerete alatt létrehozott naplóbejegyzést.

   ![Művelettípusú naplóbejegyzések](media/troubleshoot-latency-storage-analytics-logs/operation-type.png)

5. A probléma bekövetkezésének ideje alatt a következő értékek fontosak:

   * Művelet típusa = GetBlob
   * request-status = SASNetworkError
   * Vége-to-End-Latency-In-Ms = 8453
   * Kiszolgáló-késleltetés-Múlva = 391

   A végpontok késését a következő egyenlet telíti ki:

   * Végpontok utáni késés = Kiszolgáló-késés + Ügyfélkésés

   Számítsa ki az ügyfél késését a naplóbejegyzés segítségével:

   * Ügyfélkésés = végpontok közötti késés – kiszolgáló-késés

          * Example: 8453 – 391 = 8062ms

   Az alábbi táblázat az OperationType és a RequestStatus eredmények nagy késleltetésű eredményeiről tartalmaz tájékoztatást:

   |   |RequestStatus=<br>Sikeres|RequestStatus=<br>(SAS) Hálózati hiba|Ajánlás|
   |---|---|---|---|
   |GetBlob (GetBlob)|Igen|Nem|[**GetBlob művelet:** RequestStatus = Sikeres](#getblob-operation-requeststatus--success)|
   |GetBlob (GetBlob)|Nem|Igen|[**GetBlob művelet:** RequestStatus = (SAS)Hálózati hiba](#getblob-operation-requeststatus--sasnetworkerror)|
   |PutBlob (PutBlob)|Igen|Nem|[**Put Operation:** RequestStatus = Sikeres](#put-operation-requeststatus--success)|
   |PutBlob (PutBlob)|Nem|Igen|[**Put Operation:** RequestStatus = (SAS)Hálózati hiba](#put-operation-requeststatus--sasnetworkerror)|

## <a name="status-results"></a>Állapoteredmények

### <a name="getblob-operation-requeststatus--success"></a>GetBlob művelet: RequestStatus = Sikeres

Ellenőrizze az "Ajánlott lépések" szakasz 5.

* Végpontok utáni késés
* Kiszolgáló-késés
* Ügyfél-késés

A **GetBlob-művelet** **RequestStatus = Sikeres,** ha **a maximális idő** az **ügyfél-késés,** ez azt jelzi, hogy az Azure Storage tölt nagy mennyiségű időt adatok írása az ügyfélnek. Ez a késleltetés ügyféloldali problémát jelez.

**Ajánlás:**

* Vizsgálja meg a kódot az ügyfél.
* A Wireshark, a Microsoft Message Analyzer vagy a Tcping segítségével vizsgálja meg az ügyfél hálózati kapcsolati problémáit. 

### <a name="getblob-operation-requeststatus--sasnetworkerror"></a>GetBlob művelet: RequestStatus = (SAS)Hálózati hiba

Ellenőrizze az "Ajánlott lépések" szakasz 5.

* Végpontok utáni késés
* Kiszolgáló-késés
* Ügyfél-késés

A **GetBlob operation** with **RequestStatus = (SAS)NetworkError**( GetBlob operation , ha **a maximális idő** az **ügyfél-késésben**van töltve , a leggyakoribb probléma az, hogy az ügyfél bontja a kapcsolatot, mielőtt az időtúltöltés lejár a storage szolgáltatásban.

**Ajánlás:**

* Vizsgálja meg a kódot az ügyfél, hogy tudja, miért és mikor az ügyfél bontja a kapcsolatot a tárolási szolgáltatás.
* A Wireshark, a Microsoft Message Analyzer vagy a Tcping segítségével vizsgálja meg az ügyfél hálózati kapcsolati problémáit. 

### <a name="put-operation-requeststatus--success"></a>Put Operation: RequestStatus = Sikeres

Ellenőrizze az "Ajánlott lépések" szakasz 5.

* Végpontok utáni késés
* Kiszolgáló-késés
* Ügyfél-késés

A **Put Operation** with **RequestStatus = Success**, ha a maximális **idő** **az ügyfél-késésben**van töltve, ez azt jelzi, hogy az ügyfél több időt vesz igénybe az adatok küldése az Azure Storage-ba. Ez a késleltetés ügyféloldali problémát jelez.

**Ajánlás:**

* Vizsgálja meg a kódot az ügyfél.
* A Wireshark, a Microsoft Message Analyzer vagy a Tcping segítségével vizsgálja meg az ügyfél hálózati kapcsolati problémáit. 

### <a name="put-operation-requeststatus--sasnetworkerror"></a>Put Operation: RequestStatus = (SAS)NetworkError

Ellenőrizze az "Ajánlott lépések" szakasz 5.

* Végpontok utáni késés
* Kiszolgáló-késés
* Ügyfél-késés

A **PutBlob-művelet** **RequestStatus = (SAS)NetworkError,** ha **a maximális idő** töltött **ügyfél-késés,** a leggyakoribb probléma az, hogy az ügyfél leválasztja, mielőtt egy időtúltöltés lejár a storage szolgáltatásban.

**Ajánlás:**

* Vizsgálja meg a kódot az ügyfél, hogy tudja, miért és mikor az ügyfél bontja a kapcsolatot a tárolási szolgáltatás.
* A Wireshark, a Microsoft Message Analyzer vagy a Tcping segítségével vizsgálja meg az ügyfél hálózati kapcsolati problémáit.

