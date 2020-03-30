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
ms.openlocfilehash: 1199819d274590cc81d0234680f8765f9cc36c0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67178955"
---
### <a name="to-modify-local-network-gateway-ip-address-prefixes---no-gateway-connection"></a><a name="noconnection"></a>Helyi hálózati átjáró IP-címelőtagjainak módosítása – nincs átjárókapcsolat

#### <a name="to-add-additional-address-prefixes"></a>További címelőtagok felvétele:

1. A Helyi hálózati átjáró erőforrás **Beállítások** csoportjában kattintson a **Konfiguráció gombra.**
2. Adja hozzá az IP-címterületet a *További címtartomány hozzáadása* mezőben.
3. A beállítások mentéséhez kattintson a **Mentés** gombra.

#### <a name="to-remove-address-prefixes"></a>Címelőtagok eltávolítása:

1. A Helyi hálózati átjáró erőforrás **Beállítások** csoportjában kattintson a **Konfiguráció gombra.**
2. Kattintson az eltávolítani kívánt előtagot tartalmazó sorban a **"..."** gombra.
3. Kattintson az **Eltávolítás** lehetőségre.
4. A beállítások mentéséhez kattintson a **Mentés** gombra.

### <a name="to-modify-local-network-gateway-ip-address-prefixes---existing-gateway-connection"></a><a name="withconnection"></a>Helyi hálózati átjáró IP-címelőtagjainak módosítása – létező átjárókapcsolat

Ha már rendelkezik átjárókapcsolattal, és szeretné felvenni vagy eltávolítani a helyi hálózati átjáróban tárolt IP-címelőtagokat, akkor az alábbi lépéseket kell sorban végrehajtania. Ez némi állásidőt jelent a VPN-kapcsolata számára. Mikor módosítja az IP-címelőtagokat, nem kell törölnie a VPN-átjárót. Csak a kapcsolatot kell eltávolítania.

#### <a name="1-remove-the-connection"></a>1. Távolítsa el a csatlakozást.

1. A Helyi hálózati átjáró erőforrás **Beállítások** csoportjában kattintson a **Kapcsolatok gombra.**
2. Kattintson a **...** a sorban az egyes kapcsolatok, majd kattintson **a Törlés gombra**.
3. A beállítások mentéséhez kattintson a **Mentés** gombra.

#### <a name="2-modify-the-address-prefixes"></a>2. Módosítsa a címelőtagokat.

További címelőtagok felvétele:

1. A Helyi hálózati átjáró erőforrás **Beállítások** csoportjában kattintson a **Konfiguráció gombra.**
2. Adja hozzá az IP-címterületet.
3. A beállítások mentéséhez kattintson a **Mentés** gombra.

Címelőtagok eltávolítása:

1. A Helyi hálózati átjáró erőforrás **Beállítások** csoportjában kattintson a **Konfiguráció gombra.**
2. Kattintson az eltávolítani kívánt előtagot tartalmazó sorban a **...** gombra.
3. Kattintson az **Eltávolítás** lehetőségre.
4. A beállítások mentéséhez kattintson a **Mentés** gombra.

#### <a name="3-recreate-the-connection"></a>3. Hozza létre újra a kapcsolatot.

1. Keresse meg a virtuális hálózati átjáró a virtuális hálózathoz. (Nem a helyi hálózati átjáró.)
2. A Virtuális hálózati átjáró **Beállítások** területén kattintson a **Kapcsolatok gombra.**
3. Kattintson a **+ Hozzáadás** gombra a **Kapcsolat hozzáadása** panel megnyitásához.
4. Hozza létre újra a kapcsolatot.
5. A kapcsolat létrehozásához kattintson az **OK** gombra.