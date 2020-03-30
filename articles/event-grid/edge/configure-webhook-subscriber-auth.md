---
title: Webhook-előfizetői hitelesítés konfigurálása – Azure Event Grid IoT Edge | Microsoft dokumentumok
description: Webhook-előfizetők hitelesítésének konfigurálása
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 101dcae5870322878cec48098f2efae32cc68c14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841730"
---
# <a name="configure-webhook-subscriber-authentication"></a>Webhook-előfizetők hitelesítésének konfigurálása

Ez az útmutató példákat ad egy Event Grid-modul lehetséges webhook-előfizetői konfigurációira. Alapértelmezés szerint csak HTTPS-végpontok fogadhatók el a webhook-előfizetők számára. Az Event Grid modul elutasítja, ha az előfizető önaláírt tanúsítványt mutat be.

## <a name="allow-only-https-subscriber"></a>Csak HTTPS-előfizető engedélyezése

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>HTTPS-előfizető engedélyezése önaláírt tanúsítvánnyal

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Állítsa a `outbound__webhook__allowUnknownCA` `true` tulajdonságot csak tesztkörnyezetekben, mert általában önaláírt tanúsítványokat használhat. Éles számítási feladatok esetén azt javasoljuk, hogy állítsa be **a hamis.**

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>HTTPS-előfizető engedélyezése, de a tanúsítványellenőrzés kihagyása

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=true",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>Állítsa a `outbound__webhook__skipServerCertValidation` `true` tulajdonságot csak tesztkörnyezetekben, mert lehet, hogy nem mutat be olyan tanúsítványt, amelyet hitelesíteni kell. Éles számítási feladatokhoz azt javasoljuk, hogy állítsa be **a hamis**

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>HTTP és HTTPS engedélyezése önaláírt tanúsítványokkal

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=false",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Állítsa be `outbound__webhook__httpsOnly` `false` a tulajdonságot csak tesztkörnyezetekben, mert érdemes először http-előfizetőt létrehoznia. Az éles számítási feladatokhoz azt javasoljuk, hogy állítsa be **a true**
