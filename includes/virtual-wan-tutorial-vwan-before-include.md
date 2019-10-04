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
ms.openlocfilehash: 66e21aefa5648262ca2fcc61501905f725ac0e4b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179023"
---
A konfigurálás megkezdése előtt győződjön meg a következő feltételek teljesüléséről:

* Ha már rendelkezik virtuális hálózattal, amelyhez csatlakozni szeretne, győződjön meg arról, hogy a helyszíni hálózat egyik alhálózata sincs átfedésben azon virtuális alhálózatokkal, amelyekhez csatlakozni szeretne. A virtuális hálózathoz nem szükséges átjáró-alhálózat, és nem rendelkezhet virtuális hálózati átjárókkal. Ha nem rendelkezik virtuális hálózattal, létrehozhat egyet a cikkben ismertetett lépések végrehajtásával.
* Igényeljen egy IP-címtartományt az elosztó régiójában. Az elosztó egy virtuális hálózat, és az elosztó régiójához megadott címtartomány nem lehet átfedésben a meglévő azon virtuális hálózatokkal, amelyekhez csatlakozni szeretne. Emellett nem lehet átfedésben azokkal a címtartományokkal sem, amelyekhez a helyszínen csatlakozik. Ha nem ismeri a helyszíni hálózati konfigurációjában található IP-címtereket, egyeztessen valakivel, aki ezeket az adatokat megadhatja Önnek.
* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.