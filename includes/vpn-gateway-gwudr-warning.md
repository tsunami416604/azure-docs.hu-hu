---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 97fde67c3ac7649418ed0239a2c7aa4f1a4b3f96
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81274901"
---
A 0.0.0.0/0 céllal és a GatewaySubnet-en nsg-vel rendelkező, felhasználó által definiált útvonalak **nem támogatottak.** Az ezzel a konfigurációval létrehozott átjárók létrehozása le lesz tiltva. Az átjárók megfelelő működéséhez hozzáférést kell biztosítani a felügyeleti vezérlőkhöz. Az átjáró rendelkezésre állásának biztosítása érdekében a [BGP-útvonal propagálása](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol) értékre kell állítani a GatewaySubnet-en. Ha ez le van tiltva, az átjáró nem fog működni.
