---
title: Azure VMware-megoldás a CloudSimple által – CloudSimple csomópontkvóta
description: A CloudSimple-csomópontok kvótakorlátainak és a kvótanövelés kérésének módja
author: sharaths-cs
ms.author: dikamath
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 83dc9e26e03eb955d88340d1ed21084d4e685ed8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019638"
---
# <a name="cloudsimple-node-quota-limits"></a>CloudSimple csomópontkvóta-korlátok

Négy csomópont az alapértelmezett megvásárolható mennyiség, ha az előfizetés engedélyezve van a CloudSimple szolgáltatáshoz.  Bármilyen [csomóponttípust](cloudsimple-node.md) megvásárolhat az Azure Portalon.  Egy privát felhő létrehozásához legalább három, ugyanannak a termékváltozatnak a csomópontja szükséges.  Ha megvásárolta a csomópontokat, hibaüzenet jelenhet meg, amikor további csomópontokat próbál megvásárolni.

## <a name="quota-increase"></a>Kvótanövelés

A csomópontkvótát támogatási kérelem elküldésével növelheti. A szolgáltatásüzemeltetési csapat kiértékeli a kérelmet, és együttműködik Önnel a csomópontkvóta növelésében.  Új jegy megnyitásakor válassza a következő beállításokat:

* Probléma típusa: **Műszaki**
* Előfizetés: **Az előfizetés azonosítója**
* Szolgáltatás típusa: **VMware-megoldás a CloudSimple által**
* Probléma típusa: **Dedikált csomópontok kvótája**
* Probléma altípusa: **A kijelölt csomópontok kvótájának növelése**
* Tárgy: **Kvótaemelés**

A támogatási jegy részleteiben adja meg a szükséges számú csomópontot és csomópont termékváltozatát.

* Csomópont termékváltozata
* Azon további csomópontok száma, amelyekkvóta-növelést kér

## <a name="next-steps"></a>További lépések

* [Csomópontok vásárlása](create-nodes.md)
* [CloudSimple-csomópontok – áttekintés](cloudsimple-node.md)
