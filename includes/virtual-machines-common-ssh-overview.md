---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 12/21/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 3f9b86dd3716a25ab95265cf46b616144f57163b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "71168646"
---
## <a name="overview-of-ssh-and-keys"></a>Az SSH és a billentyűk áttekintése

[Az SSH](https://www.ssh.com/ssh/) egy titkosított kapcsolatprotokoll, amely biztonságos bejelentkezést tesz lehetővé a nem biztonságos kapcsolatokon keresztül. Az SSH az Azure-ban üzemeltetett Linux-virtuális gépek alapértelmezett kapcsolatprotokollja. Bár maga az SSH is titkosított kapcsolatot biztosít, az SSH-kapcsolatokkal rendelkező jelszavak használata továbbra is sebezhetővé teszi a virtuális gépet a találgatásos támadásokkal vagy a jelszavak kitalálásával szemben. Az SSH használatával a virtuális géphez való csatlakozás biztonságosabb és előnyben részesített módja egy nyilvános-titkos kulcspár, más néven *SSH-kulcsok*használata. 

* A *nyilvános kulcs* a Linux virtuális gépre vagy bármely más szolgáltatásra kerül, amelyet nyilvános kulcsú titkosítással kíván használni.

* A *személyes kulcs* a helyi rendszeren marad. Védje a titkos kulcsot. Ne ossza meg senkivel.

Ha egy SSH-ügyfél segítségével csatlakozik a Linux virtuális gép (amely rendelkezik a nyilvános kulcs), a távoli virtuális gép teszteli az ügyfél, hogy megbizonyosodjon arról, hogy rendelkezik a személyes kulcs. Ha az ügyfél rendelkezik a személyes kulcs, hozzáférést kap a virtuális géphez. 

A szervezet biztonsági szabályzataitól függően újra felhasználhatja egyetlen nyilvános és titkos kulcspárt több Azure-beli virtuális gép és szolgáltatás eléréséhez. Nem kell külön kulcspár minden virtuális gép hez vagy szolgáltatáshoz, amelyet el szeretne érni. 

A nyilvános kulcs bárkivel megosztható, de csak Ön (vagy a helyi biztonsági infrastruktúra) rendelkezhet a személyes kulccsal.