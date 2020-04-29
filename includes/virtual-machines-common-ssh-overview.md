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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "71168646"
---
## <a name="overview-of-ssh-and-keys"></a>Az SSH és a kulcsok áttekintése

Az [SSH](https://www.ssh.com/ssh/) egy titkosított kapcsolati protokoll, amely biztonságos bejelentkezéseket tesz lehetővé a nem biztonságos kapcsolatokon keresztül. Az SSH az Azure-ban üzemeltetett Linux rendszerű virtuális gépek alapértelmezett kapcsolati protokollja. Bár az SSH titkosított kapcsolatot biztosít, az SSH-kapcsolatokkal rendelkező jelszavak használatával továbbra is sebezhetővé válik a virtuális gép a találgatásos támadásokkal vagy a jelszavak kitalálása ellen. A virtuális gépek SSH-val való összekapcsolásának biztonságosabb és előnyben részesített módja egy nyilvános titkos kulcspár, más néven *SSH*-kulcs használata. 

* A *nyilvános kulcs* a linuxos virtuális gépre, vagy bármely más olyan szolgáltatásra kerül, amelyet nyilvános kulcsú titkosítással kíván használni.

* A *titkos kulcs* a helyi rendszeren marad. Védje a titkos kulcsot. Ne ossza meg senkivel.

Amikor SSH-ügyféllel csatlakozik a Linux rendszerű virtuális géphez (amely a nyilvános kulccsal rendelkezik), a távoli virtuális gép teszteli az ügyfelet, hogy biztosítsa a titkos kulcs használatát. Ha az ügyfél rendelkezik titkos kulccsal, a rendszer hozzáférést kap a virtuális géphez. 

A szervezet biztonsági házirendjeitől függően a több Azure-beli virtuális gép és szolgáltatás eléréséhez egyetlen nyilvános titkos kulcspár is felhasználható. Az elérni kívánt virtuális gépekhez vagy szolgáltatásokhoz nem kell külön kulcspárt. 

A nyilvános kulcs bárkivel megosztható, de csak Ön (vagy a helyi biztonsági infrastruktúra) rendelkezhet a titkos kulccsal.