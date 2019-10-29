---
title: Identitás konfigurálása – Azure Event Grid IoT Edge | Microsoft Docs
description: Event Grid modul identitásának konfigurálása
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/05/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 336b6157128468169264d6ffa9564da4d9338aae
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992443"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Identitás konfigurálása a Event Grid modulhoz

Ez a cikk példákat mutat be egy Event Grid modul lehetséges identitási konfigurációjáról. Alapértelmezés szerint a Event Grid modul a IoT biztonsági démon által konfigurált identitás-tanúsítványt fogja bemutatni. Az Identity Certificate a Event Grid modulban jelenik meg, amely a kimenő hívásokat mutatja be, amikor eseményeket kézbesít. Egy Event Grid esemény előfizetője ellenőrizheti, hogy valóban az a Event Grid modul, amely elküldje az eseményt az esemény elfogadása előtt.

Tekintse meg a [biztonsági és hitelesítési](security-authentication.md) útmutatót az összes lehetséges konfigurációhoz.

## <a name="always-present-identity-certificate"></a>Az identitás tanúsítványa mindig szerepel
A következő példa egy olyan konfigurációt mutat be, amely a kimenő hívásokon lévő Identity tanúsítványokat mutatja be. 

```json
 {
  "Env": [
    "outbound:clientAuth:clientCert:enabled=true",
    "outbound:clientAuth:clientCert:source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>Nem jelennek meg az identitás tanúsítványa
Az alábbi példa egy olyan konfigurációt mutat be, amely nem mutatja be a kimenő hívásokra vonatkozó identitás-tanúsítványt. 

```json
 {
  "Env": [
    "outbound:clientAuth:clientCert:enabled=false"
  ]
}
 ```
