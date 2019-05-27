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
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121020"
---
### <a name="gwipnoconnection"></a> A helyi hálózati átjáró IP-címének módosítása – átjárókapcsolat nélkül

A példa alapján módosítsa a helyi hálózati átjárót, amelynek nincs átjárókapcsolata. Az érték módosításával egy időben a címelőtagokat is módosíthatja.

1. A helyi hálózati átjáró erőforrás található a **beállítások** területén kattintson **konfigurációs**.
2. Az a **IP-cím** módosítsa az IP-címet.
3. Kattintson a **Mentés** gombra a beállítások mentéséhez.

### <a name="gwipwithconnection"></a>Módosíthatja a helyi hálózati átjáró IP-cím – meglévő átjárókapcsolat

Módosítja egy helyi hálózati átjárót, kapcsolattal rendelkezik, akkor kell először távolítsa el a kapcsolatot. Ha a kapcsolatot eltávolította, módosíthatja az átjáró IP-címét, és létrehozhat egy új kapcsolatot. Ugyanekkor módosíthatja a címelőtagokat is. Ez némi állásidőt jelent a VPN-kapcsolata számára. Mikor módosítja az átjáró IP-címét, nem kell törölnie a VPN-átjárót. Csak a kapcsolatot kell eltávolítania.
 
#### <a name="1-remove-the-connection"></a>1. Távolítsa el a kapcsolatot.

1. A helyi hálózati átjáró erőforrás található a **beállítások** területén kattintson **kapcsolatok**.
2. Kattintson a **...**  a kapcsolat sorában, majd kattintson **törlése**.
3. Kattintson a **mentése** a beállítások mentéséhez.

#### <a name="2-modify-the-ip-address"></a>2. IP-címének módosítása.

Ugyanekkor módosíthatja a címelőtagokat is.

1. Az a **IP-cím** módosítsa az IP-címet.
2. Kattintson a **Mentés** gombra a beállítások mentéséhez.

#### <a name="3-recreate-the-connection"></a>3. Hozza létre újból a kapcsolatot.

1. Keresse meg a virtuális hálózati átjáró a virtuális hálózat számára. (Nem a helyi hálózati átjárót.)
2. A virtuális hálózati átjáró az a **beállítások** területén kattintson **kapcsolatok**.
3. Kattintson a **+ Hozzáadás** megnyitásához a **kapcsolat hozzáadása** panelen.
4. Hozza létre újból a kapcsolatot.
5. Kattintson a **OK** a kapcsolat létrehozásához.