---
title: Identitás konfigurálása – Azure Event Grid IoT Edge | Microsoft dokumentumok
description: Az Event Grid modul identitásának konfigurálása
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/05/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 0aedeea2a6ad08e1627c2d1a6ebde6c91a4d02d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841765"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Az Event Grid modul identitásának konfigurálása

Ez a cikk bemutatja, hogyan konfigurálhatja az identitást a Grid on Edge számára. Alapértelmezés szerint az Event Grid modul az IoT biztonsági démon által konfigurált identitástanúsítványt mutatja be. Event Grid on Edge bemutatja az identitás-tanúsítvány a kimenő hívások, amikor eseményeket kézbesít. Az előfizető ezután ellenőrizheti, hogy az Event Grid modul, amely elküldte az eseményt, mielőtt elfogadja.

Az összes lehetséges konfigurációt lásd: [Biztonsági és hitelesítési](security-authentication.md) útmutató.

## <a name="always-present-identity-certificate"></a>Mindig jelen identitásigazolás
Íme egy példa konfigurációmindig bemutatja az identitástanúsítványt a kimenő hívások. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>Nincs identitásigazolás
Íme egy példa konfiguráció, amely nem mutatja be az identitástanúsítványt a kimenő hívások. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=false"
  ]
}
 ```
