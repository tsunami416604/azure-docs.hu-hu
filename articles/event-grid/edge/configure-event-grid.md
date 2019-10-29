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
ms.openlocfilehash: a9bac9a84e1ba034b011691ae82a1cb67eb71af0
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992430"
---
# <a name="event-grid-configuration"></a>Event Grid konfiguráció

Event Grid számos olyan konfigurációt biztosít, amelyek régiónként módosíthatók. A következő szakasz az összes elérhető lehetőségre és az azok alapértelmezett értékeire mutató hivatkozást tartalmaz.

## <a name="tls-configuration"></a>TLS-konfiguráció

Az ügyfél-hitelesítés általános ismertetése: [Biztonság és hitelesítés](security-authentication.md). A használatáról [ebben a cikkben](configure-api-protocol.md)talál példákat.

| Tulajdonság neve | Leírás |
| ---------------- | ------------ |
|`inbound:serverAuth:tlsPolicy`| A Event Grid modul TLS-házirendje. Az alapértelmezett érték csak HTTPS.
|`inbound:serverAuth:serverCert:source`| A Event Grid modul által a TLS-konfigurációhoz használt kiszolgálói tanúsítvány forrása. Az alapértelmezett érték IoT Edge.

## <a name="incoming-client-authentication"></a>Bejövő ügyfél-hitelesítés

Az ügyfél-hitelesítés általános ismertetése: [Biztonság és hitelesítés](security-authentication.md). [Ebben a cikkben](configure-client-auth.md)a példákat is megtalálhatja.

| Tulajdonság neve | Leírás |
| ---------------- | ------------ |
|`inbound:clientAuth:clientCert:enabled`| A tanúsítványalapú ügyfél-hitelesítés be-és kikapcsolása. Az alapértelmezett érték TRUE (igaz).
|`inbound:clientAuth:clientCert:source`| Az ügyféltanúsítványok érvényesítésének forrása. Az alapértelmezett érték IoT Edge.
|`inbound:clientAuth:clientCert:allowUnknownCA`| Önaláírt ügyféltanúsítványt engedélyező házirend. Az alapértelmezett érték TRUE (igaz).
|`inbound:clientAuth:sasKeys:enabled`| Az SAS-kulcs alapú ügyfél-hitelesítés be-és kikapcsolása. Az alapértelmezett érték ki van kapcsolva.
|`inbound:clientAuth:sasKeys:key1`| Az egyik érték a beérkező kérések érvényesítéséhez.
|`inbound:clientAuth:sasKeys:key2`| Nem kötelező második érték a beérkező kérések érvényesítéséhez.

## <a name="outgoing-client-authentication"></a>Kimenő ügyfél-hitelesítés
Az ügyfél-hitelesítés általános ismertetése: [Biztonság és hitelesítés](security-authentication.md). [Ebben a cikkben](configure-identity-auth.md)a példákat is megtalálhatja.

| Tulajdonság neve | Leírás |
| ---------------- | ------------ |
|`outbound:clientAuth:clientCert:enabled`| A kimenő kérésekhez tartozó Identity tanúsítvány csatolásának be-és kikapcsolása. Az alapértelmezett érték TRUE (igaz).
|`outbound:clientAuth:clientCert:source`| Event Grid modul kimenő tanúsítványának beolvasására szolgáló forrás. Az alapértelmezett érték IoT Edge.

## <a name="webhook-event-handlers"></a>Webhook-eseménykezelők

Az ügyfél-hitelesítés általános ismertetése: [Biztonság és hitelesítés](security-authentication.md). [Ebben a cikkben](configure-webhook-subscriber-auth.md)a példákat is megtalálhatja.

| Tulajdonság neve | Leírás |
| ---------------- | ------------ |
|`outbound:webhook:httpsOnly`| Házirend annak szabályozására, hogy csak HTTPS-előfizetők engedélyezettek-e. Az alapértelmezett érték igaz (csak HTTPS).
|`outbound:webhook:skipServerCertValidation`| Annak a jelzője, amely meghatározza, hogy ellenőrizni kell-e az előfizető tanúsítványát. Az alapértelmezett érték TRUE (igaz).
|`outbound:webhook:allowUnknownCA`| Házirend annak szabályozására, hogy egy előfizető tud-e önaláírt tanúsítványt megjeleníteni. Az alapértelmezett érték TRUE (igaz). 

## <a name="delivery-and-retry"></a>Teljesítés és újrapróbálkozás

A szolgáltatás általános megismeréséhez lásd: [kézbesítés és újrapróbálkozás](delivery-retry.md).

| Tulajdonság neve | Leírás |
| ---------------- | ------------ |
| `broker:defaultMaxDeliveryAttempts` | Egy eseményt kézbesítő kísérletek maximális száma. Az alapértelmezett érték 30.
| `broker:defaultEventTimeToLiveInSeconds` | Élettartam (TTL) másodpercben, amely után az esemény el lesz dobva, ha nem érkezik meg. Az alapértelmezett érték **7200** másodperc

## <a name="output-batching"></a>Kimeneti köteg

Ha többet szeretne megtudni erről a szolgáltatásról, olvassa el a [továbbítás és a kimenetek kötegelt feldolgozását](delivery-output-batching.md)ismertető témakört.

| Tulajdonság neve | Leírás |
| ---------------- | ------------ |
| `api:deliveryPolicyLimits:maxBatchSizeInBytes` | Az `ApproxBatchSizeInBytes` Knob számára engedélyezett maximális érték. Az alapértelmezett érték `1_058_576`.
| `api:deliveryPolicyLimits:maxEventsPerBatch` | Az `MaxEventsPerBatch` Knob számára engedélyezett maximális érték. Az alapértelmezett érték `50`.
| `broker:defaultMaxBatchSizeInBytes` | A kézbesítési kérelmek maximális mérete, ha csak `MaxEventsPerBatch` van megadva. Az alapértelmezett érték `1_058_576`.
| `broker:defaultMaxEventsPerBatch` | A köteghez hozzáadandó események maximális száma, ha csak `MaxBatchSizeInBytes` van megadva. Az alapértelmezett érték `10`.
