---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 11/08/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: cff3d7bfb89d5b03f986da32edc148efcfb7e7bd
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2018
ms.locfileid: "51506334"
---
## <a name="overview-of-ssh-and-keys"></a>Az SSH és a kulcsok – áttekintés

Az SSH nem titkosított kapcsolatot protokoll, amely lehetővé teszi a biztonságos bejelentkezés nem biztonságos kapcsolatokon keresztül. SSH olyan Azure-ban üzemeltetett Linuxos virtuális gépek alapértelmezett kapcsolat protokollja. Habár maga SSH titkosított kapcsolatot biztosít, jelszavak használata SSH-kapcsolatok továbbra is védtelen a virtuális gép a találgatásos támadásokkal vagy a jelszavak találgatást. Biztonságosabb és előnyben részesített módszer egy virtuális Gépre SSH használatával csatlakozhat egy nyilvános-titkos kulcspárt, más néven használatával van *SSH-kulcsok*. 

* A *nyilvános kulcs* helyezkedik el, a Linux virtuális Gépre, vagy bármely más szolgáltatást, amelyet használata a nyilvános kulcsú titkosítással kíván.

* A *titkos kulcs* , a helyi rendszer használják egy SSH-ügyfél a személyazonosságát, ha a Linux rendszerű virtuális Géphez való csatlakozáskor. Védje a titkos kulcsot. Ne ossza meg senkivel.

Attól függően, a szervezet biztonsági szabályzatokat újra felhasználhatja egy egyetlen nyilvános-titkos kulcspárt több Azure virtuális gépek és szolgáltatások eléréséhez. Nem kell külön kulcspár minden virtuális gép vagy szolgáltatás el szeretné érni. 

A nyilvános kulcs bárkivel megosztható, azonban csak Ön (vagy a helyi biztonsági infrastruktúra) rendelkezik a titkos kulccsal kell.