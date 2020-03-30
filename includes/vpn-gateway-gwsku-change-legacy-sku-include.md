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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67178999"
---
Ha az Erőforrás-kezelő központi telepítési modelljével dolgozik, átválthat az új átjáró-ska-kra. Ha örökölt átjáró termékváltozatról új termékváltozatra vált, törli a meglévő VPN-átjárót, és új VPN-átjárót hoz létre.

Munkafolyamat:

1. Távolítson el minden, a virtuális hálózat átjárójához tartozó kapcsolatot.
2. Törölje a régi VPN-átjárót.
3. Hozza létre az új VPN-átjárót.
4. Frissítse a helyszíni VPN-eszközt az új VPN-átjáró IP-címével (a helyek közötti kapcsolatokhoz).
5. Frissítse minden olyan helyi virtuális hálózatok közötti kapcsolat átjárója IP-címének értékét, amely ehhez az átjáróhoz kapcsolódik.
6. Töltse le az új VPN-konfigurációs csomagokat a virtuális hálózathoz ezen a VPN-átjárón keresztül kapcsolódó P2S-ügyfelekhez.
7. Hozza létre újra a virtuális hálózat átjárójához tartozó kapcsolatokat.

Szempontok:

* Az új termékkiterjesztési környezetbe való áthelyezéshez a VPN-átjárónak az Erőforrás-kezelő telepítési modelljében kell lennie.
* Ha klasszikus VPN-átjáróval rendelkezik, továbbra is a régebbi örökölt termékkészleteket kell használnia az adott átjáróhoz, azonban átméretezheti az örökölt termékalkalmazások között. Az új sk-k nem módosíthatók.
* Lesz kapcsolat állásidő, amikor átvált egy örökölt termékváltozat egy új termékváltozat.
* Amikor új átjáró termékváltozatra vált, a VPN-átjáró nyilvános IP-címe megváltozik. Ez akkor is megtörténik, ha ugyanazt a nyilvános IP-címobjektumot adja meg, amelyet korábban használt.