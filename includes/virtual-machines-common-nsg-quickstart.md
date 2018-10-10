---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 09/12/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: ec6cbcbc93fe87634c87caeb0041b75ec916a22f
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2018
ms.locfileid: "48888565"
---
Nyisson meg egy portot, vagy hozzon létre egy végpontot a virtuális géphez (VM) az Azure-ban egy alhálózatot vagy egy virtuális hálózati adapter hálózati szűrő létrehozásával. Ezeket a szűrőket, amely a bejövő és kimenő adatforgalom vezérlésére, helyezze el az erőforrás a forgalmat fogadó csatolt hálózati biztonsági csoport.

Ebben a cikkben a példa bemutatja, hogyan hozhat létre egy hálózati szűrő, amely a szabványos 80-as porton (feltételezhető már sikeresen elindult a megfelelő szolgáltatásokat, és bármely operációs rendszer tűzfalszabályokat a virtuális gép megnyitása) használja.

Miután létrehozott egy virtuális Gépet, amely a szabványos 80-as TCP-portot a webes kérések kiszolgálására van konfigurálva, akkor a következőket teheti:

1. Hozzon létre egy hálózati biztonsági csoportot.

2. Engedélyezi a forgalmat bejövő biztonsági szabály létrehozása, és rendelje hozzá az értékeket az alábbi beállításokat:

   - **Célporttartományok**: 80-as

   - **Porttartományok forrás**: * (lehetővé teszi, hogy bármely forrásportból)

   - **Prioritás értéke**: Adjon meg egy értéket, amely kisebb, mint a 65,500 és magasabb a prioritás kevésbé alapértelmezett bejövő szabály megtagadja.

3. A hálózati biztonsági csoport társítása a virtuális hálózati adapter vagy alhálózatot.

Bár ebben a példában egy egyszerű szabályt, hogy a HTTP-forgalmat használ, használhatja a hálózati biztonsági csoportok és a szabályok összetettebb hálózati konfigurációk létrehozásához. 




