---
title: fájlbefoglalás
description: fájlbefoglalás
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 52084b065ef65a69a6691b6646d1e199f011910d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "67178954"
---
### <a name="to-modify-the-local-network-gateway-ip-address---no-gateway-connection"></a><a name="gwipnoconnection"></a>A helyi hálózati átjáró IP-címének módosítása – nincs átjáró-kapcsolódás

A példa alapján módosítsa a helyi hálózati átjárót, amelynek nincs átjárókapcsolata. Az érték módosításával egy időben a címelőtagokat is módosíthatja.

1. A helyi hálózati átjáró erőforrás **Beállítások** szakaszában kattintson a **konfiguráció**elemre.
2. Az **IP-cím** mezőben módosítsa az IP-címet.
3. Kattintson a **Mentés** gombra a beállítások mentéséhez.

### <a name="to-modify-the-local-network-gateway-ip-address---existing-gateway-connection"></a><a name="gwipwithconnection"></a>A helyi hálózati átjáró IP-címének módosítása – meglévő átjáró-kapcsolatok

Ha olyan helyi hálózati átjárót szeretne módosítani, amely rendelkezik kapcsolatban, először el kell távolítania a kapcsolódást. Ha a kapcsolatot eltávolította, módosíthatja az átjáró IP-címét, és létrehozhat egy új kapcsolatot. Ugyanekkor módosíthatja a címelőtagokat is. Ez némi állásidőt jelent a VPN-kapcsolata számára. Mikor módosítja az átjáró IP-címét, nem kell törölnie a VPN-átjárót. Csak a kapcsolatot kell eltávolítania.
 
#### <a name="1-remove-the-connection"></a>1. szüntesse meg a kapcsolatok eltávolítását.

1. A helyi hálózati átjáró erőforrás **Beállítások** szakaszában kattintson a **kapcsolatok**elemre.
2. Kattintson a **...** elemre a kapcsolatok sorában, majd kattintson a **Törlés**gombra.
3. A beállítások mentéséhez kattintson a **Save (Mentés** ) gombra.

#### <a name="2-modify-the-ip-address"></a>2. módosítsa az IP-címet.

Ugyanekkor módosíthatja a címelőtagokat is.

1. Az **IP-cím** mezőben módosítsa az IP-címet.
2. Kattintson a **Mentés** gombra a beállítások mentéséhez.

#### <a name="3-recreate-the-connection"></a>3. hozza létre újból a kapcsolatokat.

1. Navigáljon a VNet Virtual Network átjáróra. (Nem a helyi hálózati átjáró.)
2. A Virtual Network átjáró **Beállítások** szakaszában kattintson a **kapcsolatok**elemre.
3. Kattintson a **+ Hozzáadás** gombra a **Kapcsolódás hozzáadása** panel megnyitásához.
4. Hozza létre újból a kapcsolatokat.
5. A kapcsolódás létrehozásához kattintson **az OK** gombra.