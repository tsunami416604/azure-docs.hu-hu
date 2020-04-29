---
title: Konfiguráció – Azure Event Grid IoT Edge | Microsoft Docs
description: Konfiguráció a IoT Edge Event Gridban.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 841b5092775353bbe3340dbbd55610026f998a15
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76846468"
---
# <a name="event-grid-configuration"></a>Event Grid konfiguráció

Event Grid számos olyan konfigurációt biztosít, amelyek régiónként módosíthatók. A következő szakasz az összes elérhető lehetőségre és az azok alapértelmezett értékeire mutató hivatkozást tartalmaz.

## <a name="tls-configuration"></a>TLS-konfiguráció

Az ügyfél-hitelesítés általános ismertetése: [Biztonság és hitelesítés](security-authentication.md). A használatáról [ebben a cikkben](configure-api-protocol.md)talál példákat.

| Tulajdonság neve | Leírás |
| ---------------- | ------------ |
|`inbound__serverAuth__tlsPolicy`| A Event Grid modul TLS-házirendje. Az alapértelmezett érték csak HTTPS.
|`inbound__serverAuth__serverCert__source`| A Event Grid modul által a TLS-konfigurációhoz használt kiszolgálói tanúsítvány forrása. Az alapértelmezett érték IoT Edge.

## <a name="incoming-client-authentication"></a>Bejövő ügyfél-hitelesítés

Az ügyfél-hitelesítés általános ismertetése: [Biztonság és hitelesítés](security-authentication.md). [Ebben a cikkben](configure-client-auth.md)a példákat is megtalálhatja.

| Tulajdonság neve | Leírás |
| ---------------- | ------------ |
|`inbound__clientAuth__clientCert__enabled`| A tanúsítványalapú ügyfél-hitelesítés be-és kikapcsolása. Az alapértelmezett érték TRUE (igaz).
|`inbound__clientAuth__clientCert__source`| Az ügyféltanúsítványok érvényesítésének forrása. Az alapértelmezett érték IoT Edge.
|`inbound__clientAuth__clientCert__allowUnknownCA`| Önaláírt ügyféltanúsítványt engedélyező házirend. Az alapértelmezett érték TRUE (igaz).
|`inbound__clientAuth__sasKeys__enabled`| Az SAS-kulcs alapú ügyfél-hitelesítés be-és kikapcsolása. Az alapértelmezett érték ki van kapcsolva.
|`inbound__clientAuth__sasKeys__key1`| Az egyik érték a beérkező kérések érvényesítéséhez.
|`inbound__clientAuth__sasKeys__key2`| Nem kötelező második érték a beérkező kérések érvényesítéséhez.

## <a name="outgoing-client-authentication"></a>Kimenő ügyfél-hitelesítés
Az ügyfél-hitelesítés általános ismertetése: [Biztonság és hitelesítés](security-authentication.md). [Ebben a cikkben](configure-identity-auth.md)a példákat is megtalálhatja.

| Tulajdonság neve | Leírás |
| ---------------- | ------------ |
|`outbound__clientAuth__clientCert__enabled`| A kimenő kérésekhez tartozó Identity tanúsítvány csatolásának be-és kikapcsolása. Az alapértelmezett érték TRUE (igaz).
|`outbound__clientAuth__clientCert__source`| Event Grid modul kimenő tanúsítványának beolvasására szolgáló forrás. Az alapértelmezett érték IoT Edge.

## <a name="webhook-event-handlers"></a>Webhook-eseménykezelők

Az ügyfél-hitelesítés általános ismertetése: [Biztonság és hitelesítés](security-authentication.md). [Ebben a cikkben](configure-webhook-subscriber-auth.md)a példákat is megtalálhatja.

| Tulajdonság neve | Leírás |
| ---------------- | ------------ |
|`outbound__webhook__httpsOnly`| Házirend annak szabályozására, hogy csak HTTPS-előfizetők engedélyezettek-e. Az alapértelmezett érték igaz (csak HTTPS).
|`outbound__webhook__skipServerCertValidation`| Annak a jelzője, amely meghatározza, hogy ellenőrizni kell-e az előfizető tanúsítványát. Az alapértelmezett érték TRUE (igaz).
|`outbound__webhook__allowUnknownCA`| Házirend annak szabályozására, hogy egy előfizető tud-e önaláírt tanúsítványt megjeleníteni. Az alapértelmezett érték TRUE (igaz). 

## <a name="delivery-and-retry"></a>Teljesítés és újrapróbálkozás

A szolgáltatás általános megismeréséhez lásd: [kézbesítés és újrapróbálkozás](delivery-retry.md).

| Tulajdonság neve | Leírás |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Egy eseményt kézbesítő kísérletek maximális száma. Az alapértelmezett érték 30.
| `broker__defaultEventTimeToLiveInSeconds` | Élettartam (TTL) másodpercben, amely után az esemény el lesz dobva, ha nem érkezik meg. Az alapértelmezett érték **7200** másodperc

## <a name="output-batching"></a>Kimenet kötegelése

Ha többet szeretne megtudni erről a szolgáltatásról, olvassa el a [továbbítás és a kimenetek kötegelt feldolgozását](delivery-output-batching.md)ismertető témakört.

| Tulajdonság neve | Leírás |
| ---------------- | ------------ |
| `api__deliveryPolicyLimits__maxBatchSizeInBytes` | A `ApproxBatchSizeInBytes` forgatógomb számára engedélyezett maximális érték Az `1_058_576`alapértelmezett érték:.
| `api__deliveryPolicyLimits__maxEventsPerBatch` | A `MaxEventsPerBatch` forgatógomb számára engedélyezett maximális érték Az `50`alapértelmezett érték:.
| `broker__defaultMaxBatchSizeInBytes` | A kézbesítési kérelmek maximális mérete `MaxEventsPerBatch` , ha csak meg van adva. Az `1_058_576`alapértelmezett érték:.
| `broker__defaultMaxEventsPerBatch` | A köteghez hozzáadandó események maximális száma, ha csak `MaxBatchSizeInBytes` meg van adva. Az `10`alapértelmezett érték:.

## <a name="metrics"></a>Mérőszámok

Ha szeretné megtudni, hogyan használhatja a metrikákat a IoT Edge Event Grid, tekintse meg a [témakörök és előfizetések figyelése](monitor-topics-subscriptions.md)

| Tulajdonság neve | Leírás |
| ---------------- | ------------ |
| `metrics__reporterType` | A végpontot metrikái a Reporter típusa. Az alapértelmezett `none` érték az, és letiltja a metrikákat. Ezzel a `prometheus` beállítással engedélyezheti a metrikákat a Prometheus kiállítási formátumában.