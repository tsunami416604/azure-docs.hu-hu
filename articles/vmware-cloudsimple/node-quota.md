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
ms.openlocfilehash: 5da91f37d197e9d427343a09d0376076c356e92d
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877819"
---
# <a name="cloudsimple-node-quota-limits"></a>CloudSimple-kvóta korlátai

Négy csomópont a vásárláshoz elérhető alapértelmezett mennyiség, ha az előfizetés engedélyezve van a CloudSimple szolgáltatáshoz.  A Azure Portal bármely [csomópont-típusát](cloudsimple-node.md) megvásárolhatja.  Egy privát felhő létrehozásához legalább három csomópontnak kell futnia ugyanazon SKU-ban.  Ha megvásárolta a csomópontokat, a további csomópontok megvásárlásakor hibaüzenet jelenhet meg.

## <a name="quota-increase"></a>Kvóta növekedése

Egy támogatási kérelem elküldésével növelheti a csomópont kvótáját. A szolgáltatási operatív csapat értékeli a kérést, és együttműködik Önnel a csomópont-kvóta növeléséhez.  Új jegy megnyitásakor válassza ki a következő beállításokat:

* Probléma típusa: **Technikai**
* Előfizetés: **Az előfizetés azonosítója**
* Szolgáltatás típusa: **VMware-megoldás CloudSimple szerint**
* Probléma típusa: **Dedikált csomópontok kvótája**
* Probléma altípusa: **A dedikált csomópontok kvótájának bővítése**
* Tulajdonos **Kvóta növekedése**

A támogatási jegy részleteiben adja meg a szükséges számú csomópontot és a csomópont-SKU-t.

Az előfizetéshez tartozó csomóponti kvóta [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) növeléséhez a Microsoft-fiók képviselőjével is kapcsolatba léphet.  A következőket kell megadnia:

* Előfizetés azonosítója
* Csomópont SKU
* Azon további csomópontok száma, amelyekhez a kvóta növelését kéri

## <a name="next-steps"></a>További lépések

* [Megvásárlási csomópontok](create-nodes.md)
* [A CloudSimple-csomópontok áttekintése](cloudsimple-node.md)