---
title: A megfelelőség naplózása és kezelése Azure Policy
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a Azure Policy a beépített szabályzatok használatára a Azure Machine Learninghoz.
author: jhirono
ms.author: jhirono
ms.date: 09/15/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: e7eebb22efe32b290e078348337049c6c3e762db
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90995539"
---
# <a name="audit-and-manage-azure-machine-learning-using-azure-policy"></a>Azure Machine Learning naplózása és kezelése Azure Policy használatával

A [Azure Policy](/azure/governance/policy) egy irányítási eszköz, amely lehetővé teszi, hogy az Azure-erőforrások megfeleljenek a szabályzatoknak. A Azure Machine Learning használatával a következő házirendeket rendelheti hozzá:

* **Ügyfél által felügyelt kulcs**: naplózás vagy betartatás, hogy a munkaterületeknek ügyfél által felügyelt kulcsot kell-e használniuk.
* **Privát hivatkozás**: annak ellenőrzése, hogy a munkaterületek használnak-e privát végpontot a virtuális hálózattal való kommunikációhoz.

A házirendek különböző hatókörökön állíthatók be, például az előfizetés vagy az erőforráscsoport szintjén. További információ: [Azure Policy dokumentáció](/azure/governance/policy/overview).

## <a name="built-in-policies"></a>Beépített szabályzatok

A Azure Machine Learning olyan házirendeket biztosít, amelyek a Azure Machine Learningval kapcsolatos gyakori forgatókönyvekhez használhatók. Ezeket a házirend-definíciókat hozzárendelheti meglévő előfizetéséhez, vagy felhasználhatja azokat a saját egyéni definícióinak létrehozásához. A Azure Machine Learning beépített házirendjeinek teljes listáját a [Azure Machine learning beépített szabályzatai](/azure/governance/policy/samples/built-in-policies#machine-learning)című témakörben tekintheti meg.

A Azure Machine Learninghöz kapcsolódó beépített szabályzat-definíciók megtekintéséhez kövesse az alábbi lépéseket:

1. Lépjen __Azure Policy__ a [Azure Portal](https://portal.azure.com).
1. Válassza a __definíciók__lehetőséget.
1. A __Típus mezőben__válassza a _beépített_lehetőséget, és a __kategóriánál__válassza a __Machine learning__lehetőséget.

Itt kiválaszthatja a szabályzat-definíciókat a megtekintéshez. Egy definíció megtekintése közben a __hozzárendelés__ hivatkozásra kattintva hozzárendelheti a szabályzatot egy adott hatókörhöz, és konfigurálhatja a szabályzat paramétereit. További információ: [szabályzatok társítása – portál](/azure/governance/policy/assign-policy-portal).

A házirendeket [Azure PowerShell](/azure/governance/policy/assign-policy-powershell), az [Azure CLI](https://docs.microsoft.com/azure/governance/policy/assign-policy-azurecli)és a [sablonok](/azure/governance/policy/assign-policy-template)használatával is hozzárendelheti.

## <a name="workspaces-encryption-with-customer-managed-key"></a>Munkaterületek titkosítása az ügyfél által felügyelt kulccsal

Meghatározza, hogy a munkaterületeket egy ügyfél által felügyelt kulccsal (CMK) kell-e titkosítani, vagy Microsoft által felügyelt kulccsal kell titkosítani a metrikákat és a metaadatokat. A CMK használatával kapcsolatos további információkért tekintse meg a vállalati biztonsági cikk [Azure Cosmos db](concept-enterprise-security.md#azure-cosmos-db) című szakaszát.

A szabályzat konfigurálásához állítsa be a Effect paramétert a __naplózás__ vagy a __Megtagadás__értékre. Ha __naplózásra__van BEÁLLÍTVA, CMK nélküli munkaterületeket hozhat létre, és a tevékenység naplójában létrejön egy figyelmeztetési esemény.

Ha a szabályzatot a __Megtagadás__értékre állítja, akkor nem hozhat létre munkaterületet, hacsak nem ad meg CMK. A CMK nélküli munkaterületek létrehozására tett kísérlet a következőhöz hasonló hibaüzenetet eredményez, `Resource 'clustername' was disallowed by policy` és hibát hoz létre a tevékenység naplójában. Ennek a hibának a részeként a rendszer a szabályzat azonosítóját is visszaadja.

## <a name="workspaces-should-use-private-link"></a>A munkaterületeknek privát hivatkozást kell használniuk

Azt szabályozza, hogy a munkaterületeknek az Azure privát hivatkozását kell használniuk az Azure Virtual Network való kommunikációhoz. A privát hivatkozás használatával kapcsolatos további információkért lásd: [privát hivatkozás beállítása munkaterülethez](how-to-configure-private-link.md).

A szabályzat konfigurálásához állítsa be a Effect paramétert a __naplózás__értékre. Ha privát hivatkozás nélkül hoz létre munkaterületet, a rendszer figyelmeztető eseményt hoz létre a tevékenység naplójában.

## <a name="next-steps"></a>Következő lépések

* [Az Azure Policy dokumentációja](/azure/governance/policy/overview)
* [Beépített szabályzatok a Azure Machine Learninghoz](policy-reference.md)
* [A biztonsági szabályzatok használata a Azure Security Center](/azure/security-center/tutorial-security-policy)