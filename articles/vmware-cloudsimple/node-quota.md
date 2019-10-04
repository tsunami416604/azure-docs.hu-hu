---
title: Azure VMware-megoldás CloudSimple szerint – CloudSimple Node kvóta
description: Ismerteti a CloudSimple-csomópontok kvótáit, valamint a kvóta növelésének kérését
author: sharaths-cs
ms.author: dikamath
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 83dc9e26e03eb955d88340d1ed21084d4e685ed8
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70913931"
---
# <a name="cloudsimple-node-quota-limits"></a>CloudSimple-kvóta korlátai

Négy csomópont a vásárláshoz elérhető alapértelmezett mennyiség, ha az előfizetés engedélyezve van a CloudSimple szolgáltatáshoz.  A Azure Portal bármely [csomópont-típusát](cloudsimple-node.md) megvásárolhatja.  Egy privát felhő létrehozásához legalább három csomópontnak kell futnia ugyanazon SKU-ban.  Ha megvásárolta a csomópontokat, a további csomópontok megvásárlásakor hibaüzenet jelenhet meg.

## <a name="quota-increase"></a>Kvótanövelés

Egy támogatási kérelem elküldésével növelheti a csomópont kvótáját. A szolgáltatási operatív csapat értékeli a kérést, és együttműködik Önnel a csomópont-kvóta növeléséhez.  Új jegy megnyitásakor válassza ki a következő beállításokat:

* Probléma típusa: **Technikai**
* Előfizetés: **Az előfizetés azonosítója**
* Szolgáltatás típusa: **VMware-megoldás CloudSimple szerint**
* Probléma típusa: **Dedikált csomópontok kvótája**
* Probléma altípusa: **A dedikált csomópontok kvótájának bővítése**
* Tulajdonos **Kvóta növekedése**

A támogatási jegy részleteiben adja meg a szükséges számú csomópontot és a csomópont-SKU-t.

* Csomópont SKU
* Azon további csomópontok száma, amelyekhez a kvóta növelését kéri

## <a name="next-steps"></a>További lépések

* [Megvásárlási csomópontok](create-nodes.md)
* [A CloudSimple-csomópontok áttekintése](cloudsimple-node.md)
