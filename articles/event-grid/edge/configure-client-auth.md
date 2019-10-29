---
title: A bejövő hívások ügyfél-hitelesítésének konfigurálása – Azure Event Grid IoT Edge | Microsoft Docs
description: A Event Grid által elérhetővé tett API-protokollok konfigurálása IoT Edgeon.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: b5456130e89bf77e2c2ba41880323e38f6b27f4c
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992508"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>A bejövő hívások ügyfél-hitelesítésének konfigurálása

Ez az útmutató példákat mutat be a Event Grid modul lehetséges ügyfél-hitelesítési konfigurációjáról. A Event Grid modul két típusú ügyfél-hitelesítést támogat: –

* Közös hozzáférésű aláírás (SAS) kulcs alapú
* tanúsítvány alapú

Tekintse meg a [biztonsági és hitelesítési](security-authentication.md) útmutatót az összes lehetséges konfigurációhoz.

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>Tanúsítvány alapú ügyfél-hitelesítés engedélyezése, önaláírt tanúsítványok nélkül

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=false"
  ]
}
 ```

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>Tanúsítvány alapú ügyfél-hitelesítés engedélyezése, önaláírt tanúsítványok engedélyezése

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>Állítsa be a **beérkező tulajdonságot: clientAuth: clientCert: allowUnknownCA** **csak tesztelési környezetben, mivel** általában önaláírt tanúsítványokat használ. Éles számítási feladatokhoz azt javasoljuk, hogy ezt a tulajdonságot **hamis** értékre állítsa be, és egy hitelesítésszolgáltatótól (CA) származó tanúsítványokat.

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>Tanúsítvány-alapú és Sas-kulcs alapú ügyfél-hitelesítés engedélyezése

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=true",
    "inbound:clientAuth:sasKeys:key1=<some-secret1-here>",
    "inbound:clientAuth:sasKeys:key2=<some-secret2-here>",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Az SAS-kulcs alapú ügyfél-hitelesítés lehetővé teszi a nem IoT Edge-modul számára a felügyeleti és futtatókörnyezeti műveletek elvégzését, feltételezve, hogy az API-portok a IoT Edge hálózaton kívül is elérhetők.
