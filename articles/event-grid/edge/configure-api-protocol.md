---
title: API-protokollok konfigurálása – Azure Event Grid IoT Edge | Microsoft Docs
description: A Event Grid által elérhetővé tett API-protokollok konfigurálása IoT Edgeon.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: ''
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: f39968476f2fecf655e6c69bea2ff19304d2b465
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992521"
---
# <a name="configure-event-grid-api-protocols"></a>Event Grid API-protokollok konfigurálása

Ez az útmutató példákat mutat be egy Event Grid modul lehetséges protokoll-konfigurációjáról. A Event Grid modul az API-t teszi elérhetővé felügyeleti és futtatókörnyezeti műveleteihez. A következő táblázat rögzíti a protokollokat és a portokat.

| Protocol (Protokoll) | Port | Leírás |
| ---------------- | ------------ | ------------ |
| HTTP | 5888 | Alapértelmezés szerint ki van kapcsolva. Csak tesztelés során hasznos. Éles számítási feladatokhoz nem alkalmas.
| HTTPS | 4438 | Alapértelmezett

Tekintse meg a [biztonsági és hitelesítési](security-authentication.md) útmutatót az összes lehetséges konfigurációhoz.

## <a name="expose-https-to-iot-modules-on-the-same-edge-network"></a>HTTPS-alapú IoT-modulok közzététele ugyanazon a peremhálózati hálózaton

```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=strict",
    "inbound:serverAuth:serverCert:source=IoTEdge"
  ]
}
 ```

## <a name="enable-https-to-other-iot-modules-and-non-iot-workloads"></a>HTTPS engedélyezése más IoT-modulok és nem IoT munkaterhelések számára

```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=strict",
    "inbound:serverAuth:serverCert:source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> A **PortBindings** szakasz lehetővé teszi belső portok leképezését a tároló gazdagépének portjaira. Ez a funkció lehetővé teszi a Event Grid modul elérését az IoT Edge Container networken kívülről, ha az IoT Edge-eszköz nyilvánosan elérhető.

## <a name="expose-http-and-https-to-iot-modules-on-the-same-edge-network"></a>HTTP-és HTTPS-alapú IoT-modulok közzététele ugyanazon a peremhálózati hálózaton

```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=enabled",
    "inbound:serverAuth:serverCert:source=IoTEdge"
  ]
}
 ```

## <a name="enable-http-and-https-to-other-iot-modules-and-non-iot-workloads"></a>HTTP és HTTPS engedélyezése más IoT-modulok és nem IoT-munkaterhelések számára

```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=enabled",
    "inbound:serverAuth:serverCert:source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ],
      "5888/tcp": [
        {
          "HostPort": "5888"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> Alapértelmezés szerint minden IoT modul a híd hálózat által létrehozott IoT Edge futtatókörnyezet részét képezi. Lehetővé teszi, hogy az ugyanazon a hálózaton lévő különböző IoT-modulok kommunikáljanak egymással. A **PortBindings** lehetővé teszi egy tároló belső portjának leképezését a gazdagépre, így bárki számára elérhetővé teheti Event Grid modul portját kívülről.

>[!IMPORTANT]
> Amíg a portok elérhetővé tehetők a IoT Edge hálózaton kívül, az ügyfél-hitelesítés azt kényszeríti ki, hogy a modulban valóban engedélyezve legyenek a hívások.
