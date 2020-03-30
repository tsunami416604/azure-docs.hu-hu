---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 945d701a2a7dffc259c601b4dab9fa1333ccc066
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74896614"
---
A konfiguráció megkezdése előtt ellenőrizze, hogy megfelel-e az alábbi feltételeknek:

* Ha már rendelkezik olyan virtuális hálózattal, amelyhez csatlakozni szeretne, ellenőrizze, hogy a helyszíni hálózat egyik alhálózata sem fedi-e át azt. A virtuális hálózat nem igényel átjáró alhálózatot, és nem rendelkezhet virtuális hálózati átjárókkal. Ha nem rendelkezik virtuális hálózattal, a cikkben ismertetett lépésekkel hozhat létre egyet.
* Igényeljen egy IP-címtartományt az elosztó régiójában. A hub egy virtuális hálózat, és a hub régióhoz megadott címtartomány nem fedheti át a meglévő virtuális hálózatot, amelyhez csatlakozik. Nem lehet átfedésben a helyszíni kapcsolattartományokat. Ha nem ismeri a helyszíni hálózati konfigurációban található IP-címtartományokat, egyeztessen valakivel, aki meg tudja adni ezeket az adatokat.
* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.