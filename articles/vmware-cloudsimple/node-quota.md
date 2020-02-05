---
title: Azure VMware-megoldások (AVS) – AVS Node kvóta
description: Ismerteti az AVS-csomópontok kvótájának korlátait, valamint a kvóta növelésének kérését
author: sharaths-cs
ms.author: dikamath
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fa1b056c8c96fb09def63ca1cd696fc2da5e9bed
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019638"
---
# <a name="avs-node-quota-limits"></a>Az AVS-csomópont kvótájának korlátai

Négy csomópont a vásárláshoz elérhető alapértelmezett mennyiség, ha az előfizetés engedélyezve van az AVS szolgáltatáshoz. A Azure Portal bármely [csomópont-típusát](cloudsimple-node.md) megvásárolhatja. Egy AVS Private Cloud létrehozásához legalább három csomópontnak kell futnia ugyanazon SKU-ban. Ha megvásárolta a csomópontokat, a további csomópontok megvásárlásakor hibaüzenet jelenhet meg.

## <a name="quota-increase"></a>Kvótanövelés

Egy támogatási kérelem elküldésével növelheti a csomópont kvótáját. A szolgáltatási operatív csapat értékeli a kérést, és együttműködik Önnel a csomópont-kvóta növeléséhez. Új jegy megnyitásakor válassza ki a következő beállításokat:

* Probléma típusa: **technikai**
* Előfizetés: **az előfizetés azonosítója**
* Szolgáltatás típusa: **VMware-megoldás AVS** -vel
* Probléma típusa: **dedikált csomópontok kvótája**
* Probléma altípusa: **a dedikált csomópontok kvótájának növelésére**
* Subject: **kvóta növekedése**

A támogatási jegy részleteiben adja meg a szükséges számú csomópontot és a csomópont-SKU-t.

* Csomópont SKU
* Azon további csomópontok száma, amelyekhez a kvóta növelését kéri

## <a name="next-steps"></a>Következő lépések

* [Megvásárlási csomópontok](create-nodes.md)
* [Az AVS-csomópontok áttekintése](cloudsimple-node.md)
