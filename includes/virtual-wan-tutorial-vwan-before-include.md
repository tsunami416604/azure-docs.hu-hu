---
title: fájlbefoglalás
description: fájlbefoglalás
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 945d701a2a7dffc259c601b4dab9fa1333ccc066
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023523"
---
A konfiguráció elindítása előtt ellenőrizze, hogy teljesülnek-e az alábbi feltételek:

* Ha már rendelkezik olyan virtuális hálózattal, amelyhez csatlakozni szeretne, ellenőrizze, hogy a helyszíni hálózat egyik alhálózata sem fedi-e egymást. A virtuális hálózat nem igényel átjáró-alhálózatot, és nem rendelkezhet virtuális hálózati átjárókkal. Ha nem rendelkezik virtuális hálózattal, létrehozhat egyet a cikkben ismertetett lépések segítségével.
* Igényeljen egy IP-címtartományt az elosztó régiójában. A hub egy virtuális hálózat, és a hub-régióhoz megadott címtartomány nem fedi át a meglévő virtuális hálózatot, amelyhez csatlakozik. Nem fedi átfedésben a helyszíni kapcsolatokhoz kapcsolódó címtartományt is. Ha nem ismeri a helyszíni hálózati konfigurációjában található IP-címtartományok körét, akkor egyeztessen valakivel, aki ezeket az adatokat megadhatja Önnek.
* Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).