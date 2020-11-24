---
title: fájlbefoglalás
description: fájlbefoglalás
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 317d75dae83e8529bda25897b77824d8cd36e72e
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95563385"
---
1. A virtuális WAN lapján válassza a **felhasználói VPN-konfigurációk** lehetőséget.
1. A lap tetején válassza a **felhasználói VPN-konfiguráció letöltése** lehetőséget. A WAN szintű konfiguráció letöltésekor egy beépített Traffic Manager-alapú felhasználói VPN-profilt kap. A globális profilokkal vagy a hub-alapú profillal kapcsolatos további információkért lásd: [hub-profilok](../articles/virtual-wan/global-hub-profile.md). A feladatátvételi forgatókönyvek egyszerűsített globális profillal.

   Ha valamilyen okból kifolyólag egy központ nem érhető el, a szolgáltatás által biztosított beépített forgalom biztosítja a kapcsolódást (egy másik központon keresztül) az Azure-erőforrások felé a pont – hely felhasználók számára. A hub-specifikus VPN-konfigurációt mindig letöltheti a központba. A **felhasználói VPN (pont – hely)** területen töltse le a virtuális központ **felhasználói VPN-** profilját.
1. Miután a fájl elkészült, kattintson a hivatkozásra a letöltéshez.
1. A profil fájl segítségével konfigurálja a VPN-ügyfeleket.