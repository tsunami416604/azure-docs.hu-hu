---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 2c1a4a1931bc2e38b0bee5f90518b01fdf4767a1
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2018
ms.locfileid: "30196783"
---
Dolgozunk a Resource Manager üzembe helyezési modellel, ha az új átjáró termékváltozatok módosítható. Amikor egy örökölt gateway SKU vált egy új másikra, akkor törölje a meglévő VPN-átjáró, majd hozzon létre egy új VPN-átjáró.

Munkafolyamat:

1. Távolítson el minden, a virtuális hálózat átjárójához tartozó kapcsolatot.
2. Törölje a régi VPN-átjárót.
3. Hozza létre az új VPN-átjárót.
4. Frissítse a helyszíni VPN-eszközt az új VPN-átjáró IP-címével (a helyek közötti kapcsolatokhoz).
5. Frissítse minden olyan helyi virtuális hálózatok közötti kapcsolat átjárója IP-címének értékét, amely ehhez az átjáróhoz kapcsolódik.
6. Töltse le az új VPN-konfigurációs csomagokat a virtuális hálózathoz ezen a VPN-átjárón keresztül kapcsolódó P2S-ügyfelekhez.
7. Hozza létre újra a virtuális hálózat átjárójához tartozó kapcsolatokat.

Szempontok:

* Helyezze át az új SKU, VPN-átjárót kell lennie a Resource Manager üzembe helyezési modellben.
* Ha egy hagyományos VPN-átjáró, továbbra is kell a régebbi örökölt termékváltozatok használatát, hogy az átjáró, átméretezheti, azonban a régebbi termékváltozatok között. Az új SKU nem lehet átváltani.
* Kapcsolat állásidő lesz, amikor egy új másikra vált egy örökölt Termékváltozat.
* Új átjáró SKU módosításakor a VPN-átjáró nyilvános IP-címét fogja módosítani. Ez akkor fordul elő, még akkor is, ha az azonos nyilvános IP-cím objektum, amely korábban használt adja meg.