---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 12/21/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: bca78e2963f19b60071b1b27c8dc65c76818e10e
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54029301"
---
## <a name="overview-of-ssh-and-keys"></a>Az SSH és a kulcsok – áttekintés

[SSH](https://www.ssh.com/ssh/) titkosított kapcsolatot protokoll, amely lehetővé teszi, hogy a biztonságos bejelentkezés nem biztonságos kapcsolatokon keresztül. SSH olyan Azure-ban üzemeltetett Linuxos virtuális gépek alapértelmezett kapcsolat protokollja. Habár maga SSH titkosított kapcsolatot biztosít, jelszavak használata SSH-kapcsolatok továbbra is védtelen a virtuális gép a találgatásos támadásokkal vagy a jelszavak találgatást. Biztonságosabb és előnyben részesített módszer egy virtuális Gépre SSH használatával csatlakozhat egy nyilvános-titkos kulcspárt, más néven használatával van *SSH-kulcsok*. 

* A *nyilvános kulcs* helyezkedik el, a Linux virtuális Gépre, vagy bármely más szolgáltatást, amelyet használata a nyilvános kulcsú titkosítással kíván.

* A *titkos kulcs* marad, a helyi számítógépen. Védje a titkos kulcsot. Ne ossza meg senkivel.

Az ügyfél számára, ellenőrizze, hogy rendelkezik a titkos kulcs egy SSH-ügyfél használhatja a Linux rendszerű virtuális Géphez való csatlakozáshoz (amely a nyilvános kulccsal rendelkezik), a távoli virtuális teszteli. Ha az ügyfél a titkos kulccsal rendelkezik, akkor kap a virtuális Géphez való hozzáférés. 

Attól függően, a szervezet biztonsági szabályzatokat újra felhasználhatja egy egyetlen nyilvános-titkos kulcspárt több Azure virtuális gépek és szolgáltatások eléréséhez. Nem kell külön kulcspár minden virtuális gép vagy szolgáltatás el szeretné érni. 

A nyilvános kulcs bárkivel megosztható, azonban csak Ön (vagy a helyi biztonsági infrastruktúra) rendelkezik a titkos kulccsal kell.