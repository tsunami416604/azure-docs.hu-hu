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
ms.openlocfilehash: a929149f115d716bf7f9d850abe5ba97bd5a8189
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30198094"
---
### <a name="gwipnoconnection"></a> A helyi hálózati átjáró IP-cím - átjáró kapcsolat módosítása

A példa alapján módosítsa a helyi hálózati átjárót, amelynek nincs átjárókapcsolata. Az érték módosításával egy időben a címelőtagokat is módosíthatja.

1. A helyi hálózati átjáró erőforráson az a **beállítások** területén kattintson **konfigurációs**.
2. Az a **IP-cím** módosítsa az IP-címet.
3. Kattintson a **Mentés** gombra a beállítások mentéséhez.

### <a name="gwipwithconnection"></a>A helyi hálózati átjáró átjáró IP-cím - átjárókapcsolat meglévő módosítása

Módosítsa a helyi hálózati átjáró, amely egy kapcsolattal rendelkezik, először távolítsa el a kapcsolatot kell. Ha a kapcsolatot eltávolította, módosíthatja az átjáró IP-címét, és létrehozhat egy új kapcsolatot. Ugyanekkor módosíthatja a címelőtagokat is. Ez némi állásidőt jelent a VPN-kapcsolata számára. Mikor módosítja az átjáró IP-címét, nem kell törölnie a VPN-átjárót. Csak a kapcsolatot kell eltávolítania.
 
#### <a name="1-remove-the-connection"></a>1. Távolítsa el a kapcsolatot.

1. A helyi hálózati átjáró erőforráson az a **beállítások** területén kattintson **kapcsolatok**.
2. Kattintson a **...**  a kapcsolat sorában, majd kattintson **törlése**.
3. Kattintson a **mentése** a beállítások mentéséhez.

#### <a name="2-modify-the-ip-address"></a>2. Az IP-cím módosításához.

Ugyanekkor módosíthatja a címelőtagokat is.

1. Az a **IP-cím** módosítsa az IP-címet.
2. Kattintson a **Mentés** gombra a beállítások mentéséhez.

#### <a name="3-recreate-the-connection"></a>3. Hozza létre újra a kapcsolatot.

1. Nyissa meg a virtuális hálózati átjáró a Vnethez tartozó. (Nem a helyi hálózati átjáró.)
2. A virtuális hálózati átjárón az a **beállítások** kattintson **kapcsolatok**.
3. Kattintson a **+ Hozzáadás** megnyitásához a **kapcsolat hozzáadása a** panelen.
4. Hozza létre újra a kapcsolatot.
5. Kattintson a **OK** a VPN-kapcsolat létrehozásához.