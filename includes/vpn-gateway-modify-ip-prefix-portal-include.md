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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "67178955"
---
### <a name="to-modify-local-network-gateway-ip-address-prefixes---no-gateway-connection"></a><a name="noconnection"></a>Helyi hálózati átjáró IP-címelőtagjainak módosítása – nincs átjárókapcsolat

#### <a name="to-add-additional-address-prefixes"></a>További címelőtagok felvétele:

1. A helyi hálózati átjáró erőforrás **Beállítások** szakaszában kattintson a **konfiguráció**elemre.
2. Adja hozzá az IP-címtartományt a *további címtartomány hozzáadása* mezőben.
3. A beállítások mentéséhez kattintson a **Save (Mentés** ) gombra.

#### <a name="to-remove-address-prefixes"></a>Címelőtagok eltávolítása:

1. A helyi hálózati átjáró erőforrás **Beállítások** szakaszában kattintson a **konfiguráció**elemre.
2. Kattintson a **"..."** elemre az eltávolítani kívánt előtagot tartalmazó sorban.
3. Kattintson az **Eltávolítás** lehetőségre.
4. A beállítások mentéséhez kattintson a **Save (Mentés** ) gombra.

### <a name="to-modify-local-network-gateway-ip-address-prefixes---existing-gateway-connection"></a><a name="withconnection"></a>Helyi hálózati átjáró IP-címelőtagjainak módosítása – létező átjárókapcsolat

Ha már rendelkezik átjárókapcsolattal, és szeretné felvenni vagy eltávolítani a helyi hálózati átjáróban tárolt IP-címelőtagokat, akkor az alábbi lépéseket kell sorban végrehajtania. Ez némi állásidőt jelent a VPN-kapcsolata számára. Mikor módosítja az IP-címelőtagokat, nem kell törölnie a VPN-átjárót. Csak a kapcsolatot kell eltávolítania.

#### <a name="1-remove-the-connection"></a>1. szüntesse meg a kapcsolatok eltávolítását.

1. A helyi hálózati átjáró erőforrás **Beállítások** szakaszában kattintson a **kapcsolatok**elemre.
2. Kattintson a **...** gombra az egyes kapcsolatok sorában, majd kattintson a **Törlés**gombra.
3. A beállítások mentéséhez kattintson a **Save (Mentés** ) gombra.

#### <a name="2-modify-the-address-prefixes"></a>2. módosítsa a címek előtagjait.

További címelőtagok felvétele:

1. A helyi hálózati átjáró erőforrás **Beállítások** szakaszában kattintson a **konfiguráció**elemre.
2. Adja hozzá az IP-címtartomány méretét.
3. A beállítások mentéséhez kattintson a **Save (Mentés** ) gombra.

Címelőtagok eltávolítása:

1. A helyi hálózati átjáró erőforrás **Beállítások** szakaszában kattintson a **konfiguráció**elemre.
2. Kattintson a **...** elemre az eltávolítani kívánt előtagot tartalmazó sorban.
3. Kattintson az **Eltávolítás** lehetőségre.
4. A beállítások mentéséhez kattintson a **Save (Mentés** ) gombra.

#### <a name="3-recreate-the-connection"></a>3. hozza létre újból a kapcsolatokat.

1. Navigáljon a VNet Virtual Network átjáróra. (Nem a helyi hálózati átjáró.)
2. A Virtual Network átjáró **Beállítások** szakaszában kattintson a **kapcsolatok**elemre.
3. Kattintson a **+ Hozzáadás** gombra a **Kapcsolódás hozzáadása** panel megnyitásához.
4. Hozza létre újból a kapcsolatokat.
5. A kapcsolódás létrehozásához kattintson **az OK** gombra.