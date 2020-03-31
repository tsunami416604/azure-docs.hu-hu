---
title: Bejövő hívások ügyfélhitelesítésének konfigurálása - Azure Event Grid IoT Edge | Microsoft dokumentumok
description: Konfigurálja az Event Grid által az IoT Edge-en elérhetővé tett API-protokollokat.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3363db4557dd19e8d72747ccd62bb535abb7b1e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841791"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>Bejövő hívások ügyfélhitelesítésének konfigurálása

Ez az útmutató példákat ad az Event Grid modul lehetséges ügyfélhitelesítési konfigurációira. Az Event Grid modul kétféle ügyfélhitelesítést támogat:

* Megosztott hozzáférésű aláírás (SAS) kulcsalapú
* Tanúsítványalapú

Az összes lehetséges konfigurációt lásd: [Biztonsági és hitelesítési](security-authentication.md) útmutató.

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>Tanúsítványalapú ügyfélhitelesítés engedélyezése, önaláírt tanúsítványok nélkül

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=false"
  ]
}
 ```

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>Tanúsítványalapú ügyfélhitelesítés engedélyezése, önaláírt tanúsítványok engedélyezése

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>Állítsa be a tulajdonság **inbound__clientAuth__clientCert__allowUnknownCA** **csak** tesztkörnyezetekben, mert általában önaláírt tanúsítványokat használhat. Éles számítási feladatok esetén azt javasoljuk, hogy állítsa be ezt a tulajdonságot **hamis** és tanúsítványok egy hitelesítésszolgáltatótól.

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>Tanúsítványalapú és sas-kulcsalapú ügyfélhitelesítés engedélyezése

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=true",
    "inbound__clientAuth__sasKeys__key1=<some-secret1-here>",
    "inbound__clientAuth__sasKeys__key2=<some-secret2-here>",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>A SAS-kulcsalapú ügyfélhitelesítés lehetővé teszi, hogy egy nem IoT peremhálózati modul felügyeleti és futásidejű műveleteket végez, feltételezve természetesen, hogy az API-portok az IoT Edge-hálózaton kívül érhetők el.
