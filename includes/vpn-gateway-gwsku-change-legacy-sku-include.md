---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4c232e1ce183c6935d625b5bc9987a4981865ae4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57908688"
---
Ha a Resource Manager üzemi modellel dolgozik, módosíthatja az új átjáró SKU-k. Amikor a régi átjárók Termékváltozatainak módosítja az új Termékváltozatra, törölje a meglévő VPN-átjárót, és hozzon létre egy új VPN-átjárót.

Munkafolyamat:

1. Távolítson el minden, a virtuális hálózat átjárójához tartozó kapcsolatot.
2. Törölje a régi VPN-átjárót.
3. Hozza létre az új VPN-átjárót.
4. Frissítse a helyszíni VPN-eszközt az új VPN-átjáró IP-címével (a helyek közötti kapcsolatokhoz).
5. Frissítse minden olyan helyi virtuális hálózatok közötti kapcsolat átjárója IP-címének értékét, amely ehhez az átjáróhoz kapcsolódik.
6. Töltse le az új VPN-konfigurációs csomagokat a virtuális hálózathoz ezen a VPN-átjárón keresztül kapcsolódó P2S-ügyfelekhez.
7. Hozza létre újra a virtuális hálózat átjárójához tartozó kapcsolatokat.

Szempontok:

* Szeretné áthelyezni az új termékváltozatokra, a Resource Manager-alapú üzemi modellben a VPN-átjárót kell lennie.
* Ha a klasszikus VPN-átjáró, továbbra is kell a régebbi örökölt termékváltozatok használata az adott átjáróra vonatkozó, átméretezheti, azonban az örökölt termékváltozatok között. Nem módosítható az új termékváltozatokra.
* Kapcsolat állásidő lesz, amikor az új Termékváltozatra módosítja egy örökölt Termékváltozat.
* Amikor új átjárók Termékváltozatainak módosítása, a VPN-átjáró nyilvános IP-cím változik. Ez akkor történik, még akkor is, ha az azonos nyilvános IP-címobjektumot, amelyet korábban használt adja meg.