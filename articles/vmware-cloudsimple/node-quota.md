---
title: Azure VMware-megoldás CloudSimple szerint – CloudSimple Node kvóta
description: Ismerteti a CloudSimple-csomópontok kvótáit, valamint a kvóta növelésének kérését
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e765d7c87f2f941a5e2d558b71c4e5a71d2df9b1
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816671"
---
# <a name="cloudsimple-node-quota-limits"></a>CloudSimple-kvóta korlátai

Négy csomópont a kiépítés számára elérhető alapértelmezett mennyiség, ha előfizetése engedélyezve van a CloudSimple szolgáltatáshoz.  Bármely [csomópont-típus](cloudsimple-node.md) kiépíthető Azure Portalból.  Egy privát felhő létrehozásához legalább három csomópontnak kell futnia ugyanazon SKU-ban.  Ha kiépítte a csomópontokat, hibaüzenet jelenhet meg, ha további csomópontokat próbál kiépíteni.

## <a name="quota-increase"></a>Kvóta növekedése

Egy támogatási kérelem elküldésével növelheti a csomópont kvótáját. A szolgáltatási operatív csapat értékeli a kérést, és együttműködik Önnel a csomópont-kvóta növeléséhez.  Új jegy megnyitásakor válassza ki a következő beállításokat:

* Probléma típusa: **Technikai**
* Előfizetés: **Az előfizetés azonosítója**
* Szolgáltatás típusa: **VMware-megoldás CloudSimple szerint**
* Probléma típusa: **Dedikált csomópontok kvótája**
* Probléma altípusa: **A dedikált csomópontok kvótájának bővítése**
* Tárgy: **Kvóta növekedése**

A támogatási jegy részleteiben adja meg a szükséges számú csomópontot és a csomópont-SKU-t.

Az előfizetéshez tartozó csomóponti kvóta [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) növeléséhez a Microsoft-fiók képviselőjével is kapcsolatba léphet.  A következőket kell megadnia:

* Előfizetés azonosítója
* Csomópont SKU
* Azon további csomópontok száma, amelyekhez a kvóta növelését kéri

## <a name="next-steps"></a>További lépések

* [Csomópontok kiépítése](create-nodes.md)
* [A CloudSimple-csomópontok áttekintése](cloudsimple-node.md)