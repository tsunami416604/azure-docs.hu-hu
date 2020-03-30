---
title: API-protokollok konfigurálása – Azure Event Grid IoT Edge | Microsoft dokumentumok
description: Konfigurálja az Event Grid által az IoT Edge-en elérhetővé tett API-protokollokat.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: ''
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 908bc941ee7379de067621e10adf5fd6ee6df559
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841810"
---
# <a name="configure-event-grid-api-protocols"></a>Event Grid API protokollok konfigurálása

Ez az útmutató példákat ad egy Event Grid modul lehetséges protokollkonfigurációira. Az Event Grid modul elérhetővé teszi az API-t a felügyeleti és futásidejű műveletekhez. Az alábbi táblázat a protokollokat és a portokat rögzíti.

| Protocol (Protokoll) | Port | Leírás |
| ---------------- | ------------ | ------------ |
| HTTP | 5888 | Alapértelmezés szerint ki van kapcsolva. Csak a tesztelés során hasznos. Nem alkalmas éles számítási feladatokhoz.
| HTTPS | 4438 | Alapértelmezett

Az összes lehetséges konfigurációt lásd: [Biztonsági és hitelesítési](security-authentication.md) útmutató.

## <a name="expose-https-to-iot-modules-on-the-same-edge-network"></a>HTTPS-elemek kitevéése ugyanazon a peremhálózati IoT-modulokon

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-https-to-other-iot-modules-and-non-iot-workloads"></a>HTTPS engedélyezése más IoT-modulokhoz és nem IoT-számítási feladatokhoz

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
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
> A **PortBindings** szakasz lehetővé teszi a belső portok hozzárendelését a tárolóállomás portjaihoz. Ez a funkció lehetővé teszi az Event Grid modul elérését az IoT Edge tárolóhálózaton kívülről, ha az IoT peremhálózati eszköz nyilvánosan elérhető.

## <a name="expose-http-and-https-to-iot-modules-on-the-same-edge-network"></a>Http és HTTPS kitevé és HTTPS-kapcsolattal azonos peremhálózati IoT-modulok számára

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-http-and-https-to-other-iot-modules-and-non-iot-workloads"></a>HTTP és HTTPS engedélyezése más IoT-modulokhoz és nem IoT-számítási feladatokhoz

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
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
> Alapértelmezés szerint minden IoT-modul része a hídhálózat által létrehozott IoT Edge futásidő. Lehetővé teszi, hogy ugyanazon a hálózaton lévő különböző IoT-modulok kommunikáljanak egymással. **A PortBindings** lehetővé teszi, hogy egy belső tárolóportot rendeljen a gazdagéphez, így bárki hozzáférhet az Event Grid modul portjához kívülről.

>[!IMPORTANT]
> Míg a portok elérhetővé az IoT Edge-hálózaton kívül, az ügyfél-hitelesítés kényszeríti, aki ténylegesen lehetővé tette, hogy hívásokat a modulba.
