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
ms.openlocfilehash: 0aedeea2a6ad08e1627c2d1a6ebde6c91a4d02d9
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841765"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Identitás konfigurálása a Event Grid modulhoz

Ebből a cikkből megtudhatja, hogyan konfigurálhatja a rácshoz tartozó identitást az Edge-ben. Alapértelmezés szerint a Event Grid modul a IoT biztonsági démon által konfigurált identitás-tanúsítványt jeleníti meg. Az Edge Event Grid a kimenő hívásával mutatja be az identitási tanúsítványát, amikor eseményeket küld. Az előfizető ezt követően ellenőrizheti, hogy az a Event Grid modul, amely elküldje az eseményt az elfogadás előtt.

Tekintse meg a [biztonsági és hitelesítési](security-authentication.md) útmutatót az összes lehetséges konfigurációhoz.

## <a name="always-present-identity-certificate"></a>Az identitás tanúsítványa mindig szerepel
A következő példa egy olyan konfigurációt mutat be, amely a kimenő hívásokon lévő Identity tanúsítványokat mutatja be. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>Nem jelennek meg az identitás tanúsítványa
Az alábbi példa egy olyan konfigurációt mutat be, amely nem mutatja be a kimenő hívásokra vonatkozó identitás-tanúsítványt. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=false"
  ]
}
 ```
