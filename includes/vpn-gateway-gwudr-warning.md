---
title: fájlbefoglalás
description: fájlbefoglalás
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 97fde67c3ac7649418ed0239a2c7aa4f1a4b3f96
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81274901"
---
A GatewaySubnet 0.0.0.0/0 célhelykel és NSG rendelkező felhasználó által megadott útvonalak **nem támogatottak**. Az ezzel a konfigurációval létrehozott átjárókat a rendszer letiltja a létrehozásból. Az átjárók a megfelelő működés érdekében a felügyeleti vezérlőkhöz való hozzáférést igényelnek. A [BGP-útvonal propagálását](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol) "enabled" értékre kell állítani a GatewaySubnet az átjáró elérhetőségének biztosítása érdekében. Ha ez a beállítás letiltva értékre van állítva, az átjáró nem fog működni.
