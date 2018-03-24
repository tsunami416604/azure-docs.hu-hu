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
ms.openlocfilehash: 3b8049515f753cbcf8ca068c1790f716f02d30b6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
### <a name="noconnection"></a>Helyi hálózati átjáró IP-címelőtagjainak módosítása – nincs átjárókapcsolat

#### <a name="to-add-additional-address-prefixes"></a>További címelőtagok felvétele:

1. A helyi hálózati átjáró erőforráson az a **beállítások** területén kattintson **konfigurációs**.
2. Adja hozzá az IP-címterének a *újabb címtartomány felvétele* mezőbe.
3. Kattintson a **mentése** a beállítások mentéséhez.

#### <a name="to-remove-address-prefixes"></a>Címelőtagok eltávolítása:

1. A helyi hálózati átjáró erőforráson az a **beállítások** területén kattintson **konfigurációs**.
2. Kattintson a **"..."** a sort, amelyben az eltávolítani kívánt előtagot.
3. Kattintson a **eltávolítása**.
4. Kattintson a **mentése** a beállítások mentéséhez.

### <a name="withconnection"></a>Helyi hálózati átjáró IP-címelőtagjainak módosítása – létező átjárókapcsolat

Ha már rendelkezik átjárókapcsolattal, és szeretné felvenni vagy eltávolítani a helyi hálózati átjáróban tárolt IP-címelőtagokat, akkor az alábbi lépéseket kell sorban végrehajtania. Ez némi állásidőt jelent a VPN-kapcsolata számára. Mikor módosítja az IP-címelőtagokat, nem kell törölnie a VPN-átjárót. Csak a kapcsolatot kell eltávolítania.

#### <a name="1-remove-the-connection"></a>1. Távolítsa el a kapcsolatot.

1. A helyi hálózati átjáró erőforráson az a **beállítások** területén kattintson **kapcsolatok**.
2. Kattintson a **...**  minden kapcsolat sorában, majd kattintson **törlése**.
3. Kattintson a **mentése** a beállítások mentéséhez.

#### <a name="2-modify-the-address-prefixes"></a>2. Módosítsa a címelőtagokat.

További címelőtagok felvétele:

1. A helyi hálózati átjáró erőforráson az a **beállítások** területén kattintson **konfigurációs**.
2. Az IP-címterület hozzáadása.
3. Kattintson a **mentése** a beállítások mentéséhez.

Címelőtagok eltávolítása:

1. A helyi hálózati átjáró erőforráson az a **beállítások** területén kattintson **konfigurációs**.
2. Kattintson a **...**  a sort, amelyben az eltávolítani kívánt előtagot.
3. Kattintson a **eltávolítása**.
4. Kattintson a **mentése** a beállítások mentéséhez.

#### <a name="3-recreate-the-connection"></a>3. Hozza létre újra a kapcsolatot.

1. Nyissa meg a virtuális hálózati átjáró a Vnethez tartozó. (Nem a helyi hálózati átjáró.)
2. A virtuális hálózati átjárón az a **beállítások** kattintson **kapcsolatok**.
3. Kattintson a **+ Hozzáadás** megnyitásához a **kapcsolat hozzáadása a** panelen.
4. Hozza létre újra a kapcsolatot.
5. Kattintson a **OK** a VPN-kapcsolat létrehozásához.