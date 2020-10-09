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
ms.openlocfilehash: e1a9256cc9a015a5d8286de5e5bd7b61ed915a3b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91812755"
---
1. A virtuális WAN lapján válassza a **felhasználói VPN-konfigurációk**lehetőséget.
1. A lap tetején válassza a **felhasználói VPN-konfiguráció letöltése**lehetőséget. A WAN szintű konfiguráció letöltésekor egy beépített Traffic Manager-alapú felhasználói VPN-profilt kap. A globális profilokkal vagy a hub-alapú profillal kapcsolatos további információkért lásd: [hub-profilok](https://docs.microsoft.com/azure/virtual-wan/global-hub-profile). A feladatátvételi forgatókönyvek egyszerűsített globális profillal.

   Ha valamilyen okból kifolyólag egy központ nem érhető el, a szolgáltatás által biztosított beépített forgalom biztosítja a kapcsolódást (egy másik központon keresztül) az Azure-erőforrások felé a pont – hely felhasználók számára. A hub-specifikus VPN-konfigurációt mindig letöltheti a központba. A **felhasználói VPN (pont – hely)** területen töltse le a virtuális központ **felhasználói VPN-** profilját.
1. Miután a fájl elkészült, kattintson a hivatkozásra a letöltéshez.
1. A profil fájl segítségével konfigurálja a VPN-ügyfeleket.
