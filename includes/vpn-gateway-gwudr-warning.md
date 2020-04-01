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
ms.openlocfilehash: 0e12ad66c635632b29b70000b6e227ddcbb5357b
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501476"
---
A 0.0.0.0/0 céllal és a GatewaySubnet-en nsg-vel rendelkező, felhasználó által definiált útvonalak **nem támogatottak.** Az ezzel a konfigurációval létrehozott átjárók létrehozása le lesz tiltva. Az átjárók megfelelő működéséhez hozzáférést kell biztosítani a felügyeleti vezérlőkhöz. Az átjáró rendelkezésre állásának biztosítása érdekében a [BGP-útvonal propagálása](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol]) értékre kell állítani a GatewaySubnet-en. Ha ez le van tiltva, az átjáró nem fog működni.
