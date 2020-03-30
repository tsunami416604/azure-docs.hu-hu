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
ms.openlocfilehash: 52084b065ef65a69a6691b6646d1e199f011910d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67178954"
---
### <a name="to-modify-the-local-network-gateway-ip-address---no-gateway-connection"></a><a name="gwipnoconnection"></a>A helyi hálózati átjáró IP-címének módosítása - nincs átjárókapcsolat

A példa alapján módosítsa a helyi hálózati átjárót, amelynek nincs átjárókapcsolata. Az érték módosításával egy időben a címelőtagokat is módosíthatja.

1. A Helyi hálózati átjáró erőforrás **Beállítások** csoportjában kattintson a **Konfiguráció gombra.**
2. Az **IP-cím** mezőben módosítsa az IP-címet.
3. Kattintson a **Mentés** gombra a beállítások mentéséhez.

### <a name="to-modify-the-local-network-gateway-ip-address---existing-gateway-connection"></a><a name="gwipwithconnection"></a>A helyi hálózati átjáró IP-címének módosítása – meglévő átjárókapcsolat

A kapcsolattal rendelkező helyi hálózati átjáró módosításához először el kell távolítania a kapcsolatot. Ha a kapcsolatot eltávolította, módosíthatja az átjáró IP-címét, és létrehozhat egy új kapcsolatot. Ugyanekkor módosíthatja a címelőtagokat is. Ez némi állásidőt jelent a VPN-kapcsolata számára. Mikor módosítja az átjáró IP-címét, nem kell törölnie a VPN-átjárót. Csak a kapcsolatot kell eltávolítania.
 
#### <a name="1-remove-the-connection"></a>1. Távolítsa el a csatlakozást.

1. A Helyi hálózati átjáró erőforrás **Beállítások** csoportjában kattintson a **Kapcsolatok gombra.**
2. Kattintson a kapcsolat sorában lévő **...** gombra, majd a **Törlés gombra.**
3. A beállítások mentéséhez kattintson a **Mentés** gombra.

#### <a name="2-modify-the-ip-address"></a>2. Módosítsa az IP-címet.

Ugyanekkor módosíthatja a címelőtagokat is.

1. Az **IP-cím** mezőben módosítsa az IP-címet.
2. Kattintson a **Mentés** gombra a beállítások mentéséhez.

#### <a name="3-recreate-the-connection"></a>3. Hozza létre újra a kapcsolatot.

1. Keresse meg a virtuális hálózati átjáró a virtuális hálózathoz. (Nem a helyi hálózati átjáró.)
2. A Virtuális hálózati átjáró **Beállítások** területén kattintson a **Kapcsolatok gombra.**
3. Kattintson a **+ Hozzáadás** gombra a **Kapcsolat hozzáadása** panel megnyitásához.
4. Hozza létre újra a kapcsolatot.
5. A kapcsolat létrehozásához kattintson az **OK** gombra.