---
title: Konfiguráció – Azure Event Grid IoT Edge | Microsoft dokumentumok
description: Konfiguráció az IOt Edge Eseményrácsban.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 841b5092775353bbe3340dbbd55610026f998a15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76846468"
---
# <a name="event-grid-configuration"></a>Eseményrács konfigurációja

Az Event Grid számos olyan konfigurációt biztosít, amelyek környezetenként módosíthatók. A következő szakasz az összes rendelkezésre álló beállításra és azok alapértelmezett beállításaira vonatkozik.

## <a name="tls-configuration"></a>TLS-konfiguráció

Az ügyfélhitelesítésről általában a [Biztonság és hitelesítés](security-authentication.md)című témakörben olvashat. Használatára példák találhatók [ebben](configure-api-protocol.md)a cikkben.

| Tulajdonság neve | Leírás |
| ---------------- | ------------ |
|`inbound__serverAuth__tlsPolicy`| Az Eseményrács modul TLS-házirendje. Az alapértelmezett érték csak HTTPS.
|`inbound__serverAuth__serverCert__source`| Az Event Grid modul által a TLS-konfigurációhoz használt kiszolgálói tanúsítvány forrása. Az alapértelmezett érték az IoT Edge.

## <a name="incoming-client-authentication"></a>Bejövő ügyfélhitelesítés

Az ügyfélhitelesítésről általában a [Biztonság és hitelesítés](security-authentication.md)című témakörben olvashat. Példák találhatók ebben a [cikkben](configure-client-auth.md).

| Tulajdonság neve | Leírás |
| ---------------- | ------------ |
|`inbound__clientAuth__clientCert__enabled`| A tanúsítványalapú ügyfélhitelesítés be- és kikapcsolása. Az alapértelmezett érték igaz.
|`inbound__clientAuth__clientCert__source`| Az ügyféltanúsítványok érvényesítésének forrása. Az alapértelmezett érték az IoT Edge.
|`inbound__clientAuth__clientCert__allowUnknownCA`| Önaláírt ügyféltanúsítvány engedélyezésére vonatkozó házirend. Az alapértelmezett érték igaz.
|`inbound__clientAuth__sasKeys__enabled`| A SAS-kulcsalapú ügyfélhitelesítés be- és kikapcsolása. Az alapértelmezett érték ki van kapcsolva.
|`inbound__clientAuth__sasKeys__key1`| A bejövő kérelmek érvényesítéséhez a következő értékek egyike.
|`inbound__clientAuth__sasKeys__key2`| Választható második érték a bejövő kérelmek érvényesítéséhez.

## <a name="outgoing-client-authentication"></a>Kimenő ügyfél hitelesítése
Az ügyfélhitelesítésről általában a [Biztonság és hitelesítés](security-authentication.md)című témakörben olvashat. Példák találhatók ebben a [cikkben](configure-identity-auth.md).

| Tulajdonság neve | Leírás |
| ---------------- | ------------ |
|`outbound__clientAuth__clientCert__enabled`| A kimenő kérelmek identitástanúsítványának be- és kikapcsolása. Az alapértelmezett érték igaz.
|`outbound__clientAuth__clientCert__source`| Az Event Grid modul kimenő tanúsítványának beolvasásának forrása. Az alapértelmezett érték az IoT Edge.

## <a name="webhook-event-handlers"></a>Webhook eseménykezelők

Az ügyfélhitelesítésről általában a [Biztonság és hitelesítés](security-authentication.md)című témakörben olvashat. Példák találhatók ebben a [cikkben](configure-webhook-subscriber-auth.md).

| Tulajdonság neve | Leírás |
| ---------------- | ------------ |
|`outbound__webhook__httpsOnly`| Házirend, amely azt szabályozza, hogy csak a HTTPS-előfizetők engedélyezettek legyenek-e. Az alapértelmezett érték igaz (csak HTTPS).
|`outbound__webhook__skipServerCertValidation`| A jelölés sel szabályozhatja, hogy érvényesítse-e az előfizető tanúsítványát. Az alapértelmezett érték igaz.
|`outbound__webhook__allowUnknownCA`| Házirend, amely azt szabályozza, hogy az előfizető bemutathatja-e az önaláírt tanúsítványt. Az alapértelmezett érték igaz. 

## <a name="delivery-and-retry"></a>Teljesítés és újrapróbálkozás

A funkcióról általában a [Kézbesítés és az Újra](delivery-retry.md)című témakörben olvashat.

| Tulajdonság neve | Leírás |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Az esemény kézbesítésére tett kísérletek maximális száma. Az alapértelmezett érték 30.
| `broker__defaultEventTimeToLiveInSeconds` | Az élethez való idő (TTL) másodpercben, amely után egy esemény ellesz dobva, ha nem kézbesítik. Az alapértelmezett érték **7200** másodperc

## <a name="output-batching"></a>Kimenet kötegelése

A funkcióról általában a [Kézbesítés és kimenet kötegelés című](delivery-output-batching.md)témakörben olvashat.

| Tulajdonság neve | Leírás |
| ---------------- | ------------ |
| `api__deliveryPolicyLimits__maxBatchSizeInBytes` | A `ApproxBatchSizeInBytes` gomb maximális értéke megengedett. Az alapértelmezett `1_058_576`érték a .
| `api__deliveryPolicyLimits__maxEventsPerBatch` | A `MaxEventsPerBatch` gomb maximális értéke megengedett. Az alapértelmezett `50`érték a .
| `broker__defaultMaxBatchSizeInBytes` | Maximális kézbesítési `MaxEventsPerBatch` kérelem mérete, ha csak meg van adva. Az alapértelmezett `1_058_576`érték a .
| `broker__defaultMaxEventsPerBatch` | Köteghez hozzáadandó események maximális száma, ha csak `MaxBatchSizeInBytes` meg van adva. Az alapértelmezett `10`érték a .

## <a name="metrics"></a>Mérőszámok

Ha többet szeretne tudni ame-metrikák ioT edge-en történő használatával kapcsolatban, tekintse meg [a témakörök és az előfizetések figyelése című témakört.](monitor-topics-subscriptions.md)

| Tulajdonság neve | Leírás |
| ---------------- | ------------ |
| `metrics__reporterType` | A metrikák enpoint-jának riportertípusa. Az `none` alapértelmezett érték, és letiltja a mutatókat. A `prometheus` prometheus kiállítási formátumban lévő mérőszámok engedélyezése.