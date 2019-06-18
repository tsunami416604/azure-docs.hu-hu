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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66121004"
---
### <a name="noconnection"></a>Helyi hálózati átjáró IP-címelőtagjainak módosítása – nincs átjárókapcsolat

#### <a name="to-add-additional-address-prefixes"></a>További címelőtagok felvétele:

1. A helyi hálózati átjáró erőforrás található a **beállítások** területén kattintson **konfigurációs**.
2. Adja hozzá az IP-címtér az *újabb címtartomány felvétele* mezőbe.
3. Kattintson a **mentése** a beállítások mentéséhez.

#### <a name="to-remove-address-prefixes"></a>Címelőtagok eltávolítása:

1. A helyi hálózati átjáró erőforrás található a **beállítások** területén kattintson **konfigurációs**.
2. Kattintson a **"..."** a el kívánja távolítani az előtagot tartalmazó sor.
3. Kattintson a **eltávolítása**.
4. Kattintson a **mentése** a beállítások mentéséhez.

### <a name="withconnection"></a>Helyi hálózati átjáró IP-címelőtagjainak módosítása – létező átjárókapcsolat

Ha már rendelkezik átjárókapcsolattal, és szeretné felvenni vagy eltávolítani a helyi hálózati átjáróban tárolt IP-címelőtagokat, akkor az alábbi lépéseket kell sorban végrehajtania. Ez némi állásidőt jelent a VPN-kapcsolata számára. Mikor módosítja az IP-címelőtagokat, nem kell törölnie a VPN-átjárót. Csak a kapcsolatot kell eltávolítania.

#### <a name="1-remove-the-connection"></a>1. Távolítsa el a kapcsolatot.

1. A helyi hálózati átjáró erőforrás található a **beállítások** területén kattintson **kapcsolatok**.
2. Kattintson a **...**  minden kapcsolat sorában, majd kattintson **törlése**.
3. Kattintson a **mentése** a beállítások mentéséhez.

#### <a name="2-modify-the-address-prefixes"></a>2. Módosítsa a címelőtagokat.

További címelőtagok felvétele:

1. A helyi hálózati átjáró erőforrás található a **beállítások** területén kattintson **konfigurációs**.
2. Adja hozzá az IP-címtér.
3. Kattintson a **mentése** a beállítások mentéséhez.

Címelőtagok eltávolítása:

1. A helyi hálózati átjáró erőforrás található a **beállítások** területén kattintson **konfigurációs**.
2. Kattintson a **...**  a el kívánja távolítani az előtagot tartalmazó sor.
3. Kattintson a **eltávolítása**.
4. Kattintson a **mentése** a beállítások mentéséhez.

#### <a name="3-recreate-the-connection"></a>3. Hozza létre újból a kapcsolatot.

1. Keresse meg a virtuális hálózati átjáró a virtuális hálózat számára. (Nem a helyi hálózati átjárót.)
2. A virtuális hálózati átjáró az a **beállítások** területén kattintson **kapcsolatok**.
3. Kattintson a **+ Hozzáadás** megnyitásához a **kapcsolat hozzáadása** panelen.
4. Hozza létre újból a kapcsolatot.
5. Kattintson a **OK** a kapcsolat létrehozásához.