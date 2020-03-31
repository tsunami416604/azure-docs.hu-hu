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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179089"
---
Megnyit egy portot, vagy hozzon létre egy végpontot egy virtuális gép (VM) az Azure-ban egy hálózati szűrő létrehozása egy alhálózaton vagy egy virtuális gép hálózati adapter. Ezeket a szűrőket, amelyek a bejövő és a kimenő forgalmat egyaránt vezérelik, a forgalmat fogadó erőforráshoz kapcsolódó hálózati biztonsági csoportra helyezi.

Ebben a cikkben bemutatja, hogyan hozhat létre egy hálózati szűrőt, amely a szabványos TCP-port 80 (feltételezhető, hogy már elindította a megfelelő szolgáltatásokat, és megnyitotta az operációs rendszer tűzfalszabályait a virtuális gép).

Miután létrehozott egy virtuális gép, amely úgy van beállítva, hogy a webes kérelmek et a szabványos TCP-port 80, a következőket teheti:

1. Hozzon létre egy hálózati biztonsági csoportot.

2. Hozzon létre egy bejövő biztonsági szabályt, amely engedélyezi a forgalmat, és értékeket rendel a következő beállításokhoz:

   - **Célport-tartományok**: 80

   - **Forrásport tartományok:*** (bármilyen forrásportot engedélyez)

   - **Prioritás :** Adjon meg egy 65 500-nál kisebb és magasabb prioritású értéket, mint az alapértelmezett befogószabály.

3. Társítsa a hálózati biztonsági csoportot a virtuális gép hálózati adapteréhez vagy alhálózatához.

Bár ez a példa egy egyszerű szabályt használ a HTTP-forgalom engedélyezéséhez, hálózati biztonsági csoportok és szabályok használatával összetettebb hálózati konfigurációkat is létrehozhat. 




