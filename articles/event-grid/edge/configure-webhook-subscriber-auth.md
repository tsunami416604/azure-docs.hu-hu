---
title: Webhook-előfizető hitelesítésének konfigurálása – Azure Event Grid IoT Edge | Microsoft Docs
description: Webhook-előfizető hitelesítésének konfigurálása
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 97ed1e2ad84d895e9da0d96cd070e14acb46385d
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992482"
---
# <a name="configure-webhook-subscriber-authentication"></a>Webhook-előfizető hitelesítésének konfigurálása

Ez az útmutató példákat mutat be egy Event Grid modul lehetséges webhook-előfizetői konfigurációjában. Alapértelmezés szerint csak HTTPS-végpontok fogadhatók el a webhook-előfizetők számára. A Event Grid modul elutasítja, ha az előfizető önaláírt tanúsítványt ad meg.

## <a name="allow-only-https-subscriber"></a>Csak HTTPS-előfizető engedélyezése

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>Saját aláírású tanúsítvánnyal rendelkező HTTPS-előfizető engedélyezése

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Állítsa be úgy a `outbound:webhook:allowUnknownCA` tulajdonságot, hogy csak tesztelési környezetben `true`, mivel általában önaláírt tanúsítványokat használ. Éles számítási feladatokhoz azt javasoljuk, hogy **hamis**értékre állítsa őket.

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>HTTPS-előfizető engedélyezése, de tanúsítvány-ellenőrzés kihagyása

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=true",
    "outbound:webhook:allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>Állítsa a `outbound:webhook:skipServerCertValidation` tulajdonságot úgy, hogy csak tesztelési környezetekben `true`, mivel előfordulhat, hogy nem fog hitelesíteni egy tanúsítványt. Éles számítási feladatokhoz ajánlott **Hamis értéket** beállítani

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>HTTP és HTTPS engedélyezése önaláírt tanúsítványokkal

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=false",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Állítsa be úgy a `outbound:webhook:httpsOnly` tulajdonságot, hogy csak tesztelési környezetekben `false`, mert előbb létre kell hoznia egy HTTP-előfizetőt. Éles számítási feladatokhoz ajánlott **igaz** értéket beállítani
