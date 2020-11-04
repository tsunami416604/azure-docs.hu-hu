---
title: A szabályzatok megfelelőségének naplózása és kezelése
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a Azure Policy a beépített szabályzatok használatára Azure Machine Learning annak biztosítására, hogy a munkaterületei megfeleljenek a követelményeknek.
author: jhirono
ms.author: jhirono
ms.date: 09/15/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: 47a97bb8fbe52d3dde84afd77997b179a5c9248d
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325500"
---
# <a name="audit-and-manage-azure-machine-learning-using-azure-policy"></a>Azure Machine Learning naplózása és kezelése Azure Policy használatával

A [Azure Policy](../governance/policy/index.yml) egy irányítási eszköz, amely lehetővé teszi, hogy az Azure-erőforrások megfeleljenek a szabályzatoknak. A Azure Machine Learning használatával a következő házirendeket rendelheti hozzá:

* **Ügyfél által felügyelt kulcs** : naplózás vagy betartatás, hogy a munkaterületeknek ügyfél által felügyelt kulcsot kell-e használniuk.
* **Privát hivatkozás** : annak ellenőrzése, hogy a munkaterületek használnak-e privát végpontot a virtuális hálózattal való kommunikációhoz.

A házirendek különböző hatókörökön állíthatók be, például az előfizetés vagy az erőforráscsoport szintjén. További információ: [Azure Policy dokumentáció](../governance/policy/overview.md).

## <a name="built-in-policies"></a>Beépített szabályzatok

A Azure Machine Learning olyan házirendeket biztosít, amelyek a Azure Machine Learningval kapcsolatos gyakori forgatókönyvekhez használhatók. Ezeket a házirend-definíciókat hozzárendelheti meglévő előfizetéséhez, vagy felhasználhatja azokat a saját egyéni definícióinak létrehozásához. A Azure Machine Learning beépített házirendjeinek teljes listáját a [Azure Machine learning beépített szabályzatai](../governance/policy/samples/built-in-policies.md#machine-learning)című témakörben tekintheti meg.

A Azure Machine Learninghöz kapcsolódó beépített szabályzat-definíciók megtekintéséhez kövesse az alábbi lépéseket:

1. Lépjen __Azure Policy__ a [Azure Portal](https://portal.azure.com).
1. Válassza a __definíciók__ lehetőséget.
1. A __Típus mezőben__ válassza a _beépített_ lehetőséget, és a __kategóriánál__ válassza a __Machine learning__ lehetőséget.

Itt kiválaszthatja a szabályzat-definíciókat a megtekintéshez. Egy definíció megtekintése közben a __hozzárendelés__ hivatkozásra kattintva hozzárendelheti a szabályzatot egy adott hatókörhöz, és konfigurálhatja a szabályzat paramétereit. További információ: [szabályzatok társítása – portál](../governance/policy/assign-policy-portal.md).

A házirendeket [Azure PowerShell](../governance/policy/assign-policy-powershell.md), az [Azure CLI](../governance/policy/assign-policy-azurecli.md)és a [sablonok](../governance/policy/assign-policy-template.md)használatával is hozzárendelheti.

## <a name="workspaces-encryption-with-customer-managed-key"></a>Munkaterületek titkosítása az ügyfél által felügyelt kulccsal

Meghatározza, hogy a munkaterületeket egy ügyfél által felügyelt kulccsal (CMK) kell-e titkosítani, vagy Microsoft által felügyelt kulccsal kell titkosítani a metrikákat és a metaadatokat. A CMK használatával kapcsolatos további információkért tekintse meg a vállalati biztonsági cikk [Azure Cosmos db](concept-enterprise-security.md#azure-cosmos-db) című szakaszát.

A szabályzat konfigurálásához állítsa be a Effect paramétert a __naplózás__ vagy a __Megtagadás__ értékre. Ha __naplózásra__ van BEÁLLÍTVA, CMK nélküli munkaterületeket hozhat létre, és a tevékenység naplójában létrejön egy figyelmeztetési esemény.

Ha a szabályzatot a __Megtagadás__ értékre állítja, akkor nem hozhat létre munkaterületet, hacsak nem ad meg CMK. A CMK nélküli munkaterületek létrehozására tett kísérlet a következőhöz hasonló hibaüzenetet eredményez, `Resource 'clustername' was disallowed by policy` és hibát hoz létre a tevékenység naplójában. Ennek a hibának a részeként a rendszer a szabályzat azonosítóját is visszaadja.

## <a name="workspaces-should-use-private-link"></a>A munkaterületeknek privát hivatkozást kell használniuk

Azt szabályozza, hogy a munkaterületeknek az Azure privát hivatkozását kell használniuk az Azure Virtual Network való kommunikációhoz. A privát hivatkozás használatával kapcsolatos további információkért lásd: [privát hivatkozás beállítása munkaterülethez](how-to-configure-private-link.md).

A szabályzat konfigurálásához állítsa be a Effect paramétert a __naplózás__ értékre. Ha privát hivatkozás nélkül hoz létre munkaterületet, a rendszer figyelmeztető eseményt hoz létre a tevékenység naplójában.

## <a name="next-steps"></a>Következő lépések

* [Az Azure Policy dokumentációja](../governance/policy/overview.md)
* [Beépített szabályzatok a Azure Machine Learninghoz](policy-reference.md)
* [A biztonsági szabályzatok használata a Azure Security Center](../security-center/tutorial-security-policy.md)