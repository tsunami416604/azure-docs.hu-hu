---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 04/16/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 5e199771c39ba2fbdeabbd04ed4081a9cd3ea117
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2018
ms.locfileid: "36964518"
---
## <a name="overview-of-ssh-and-keys"></a>SSH és a kulcsok – áttekintés

Az SSH egy olyan titkosított kapcsolati protokoll, amely lehetővé teszi, hogy a biztonságos bejelentkezések nem biztonságos kapcsolaton keresztül. Az Azure szolgáltatásban üzemeltetett Linux virtuális gépek alapértelmezett csatlakozási protokoll. Habár maga SSH titkosított kapcsolatot biztosít, jelszavak használata SSH-kapcsolatok továbbra is védtelen a virtuális Gépet a találgatásos támadásokkal vagy a jelszavak találgatás. A biztonságosabb és előnyben részesített csatlakoztatása egy virtuális gép SSH módszer egy nyilvános-titkos kulcsból álló kulcspárt, más néven az SSH-kulcsok használatával. 

* A *nyilvános kulcs* helyezkedik el a Linux virtuális Gépet, vagy bármely olyan szolgáltatás, amely a nyilvános kulcsú titkosítás használna.

* A *titkos kulcs* van Mi mutassa be a Linux virtuális gép kapcsolat létrehozásakor egy SSH, a személyazonosságát. Védje a titkos kulcsot. Ne ossza meg senkivel.

Attól függően, hogy a szervezet biztonsági házirendek felhasználhatja egy egyetlen nyilvános-titkos kulcsból álló kulcspárt több Azure virtuális gépek és szolgáltatások eléréséhez. Nem kell külön kulcspárra van szüksége az egyes virtuális gép vagy szolgáltatás el szeretné érni. 

A nyilvános kulcs bárkivel megosztható, azonban csak Ön (vagy a helyi biztonsági infrastruktúra) rendelkezik a titkos kulccsal.