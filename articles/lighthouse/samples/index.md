---
title: Azure Lighthouse-minták és -sablonok
description: Ezek a minták és Azure Resource Manager-sablonok megmutatják, hogyan történik az ügyfelek előkészítése az Azure által delegált erőforrás-kezeléshez, valamint az Azure Lighthouse-forgatókönyvek támogatása.
author: JnHs
manager: carmonm
ms.service: lighthouse
ms.topic: sample
ms.date: 10/17/2019
ms.author: jenhayes
ms.openlocfilehash: 6d47534026b6fe815f9756a74ba3438dc67a8e02
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553681"
---
# <a name="azure-lighthouse-samples"></a>Azure Lighthouse-minták

A következő táblázat Azure Resource Manager-sablonokra mutató hivatkozásokat tartalmaz az Azure Lighthouse-hoz. Többek között ezek a fájlok is megtalálhatók az [Azure Lighthouse mintaadattárában](https://github.com/Azure/Azure-Lighthouse-samples/).

## <a name="onboarding-customers-to-azure-delegated-resource-management"></a>Ügyfelek előkészítése az Azure által delegált erőforrás-kezeléshez

Különböző sablonokat biztosítunk az adott előkészítési forgatókönyvekhez. Válassza ki a legmegfelelőbb lehetőséget, majd módosítsa úgy a paraméterfájlt, hogy az megfeleljen az Ön által használt környezetnek. További információ a fájlok használatáról az üzemelő példányban: [Ügyfél előkészítése az Azure által delegált erőforrás-kezeléshez](../how-to/onboard-customer.md).

| **Sablon** | **Leírás** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | Előkészíti egy ügyfél előfizetését az Azure által delegált erőforrás-kezeléshez. Az üzembe helyezést külön-külön kell elvégezni mindegyik előfizetés esetében. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | Előkészíti egy ügyfél egy vagy több erőforráscsoportját az Azure által delegált erőforrás-kezeléshez. Amennyiben az adott előfizetésben egyetlen erőforráscsoportot kíván előkészíteni, használja a **rgDelegatedResourceManagement** parancsot, ha pedig többet, akkor használja a **multipleRgDelegatedResourceManagement** parancsot. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | Ha [közzétett egy felügyelt szolgáltatási ajánlatot az Azure Marketplace-en](../how-to/publish-managed-services-offers.md), opcionálisan használhatja ezt a sablont az ajánlatot elfogadó ügyfelek erőforrásainak előkészítésére. A paraméterfájl Marketplace-en megadott értékeinek meg kell egyezniük az ajánlat közzétételekor megadott értékekkel. |

Az üzembe helyezést általában külön-külön kell elvégezni mindegyik előkészített előfizetés esetében, azonban egy adott sablont több előfizetés üzembe helyezéséhez is használhat.

| **Sablon** | **Leírás** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | Azure Resource Manager-sablonok telepítése több előfizetéshez. |

## <a name="azure-policy"></a>Azure Policy

Ezek a minták bemutatják, hogyan használható az Azure Policy az Azure által delegált erőforrás-kezeléshez előkészített előfizetések esetében.

| **Sablon** | **Leírás** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag) | Hozzárendel egy olyan szabályzatot, amely hozzáad vagy eltávolít egy címkét (a modify hatás használatával) egy delegált előfizetés esetében. További információt [a javítható szabályzatok delegált előfizetésben való telepítésével foglalkozó részben](../how-to/deploy-policy-remediation.md) talál. |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-audit-delegation) | Hozzárendel egy olyan szabályzatot, amely naplózza a delegálási hozzárendeléseket. |
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring) | Hozzárendel egy olyan szabályzatot, amely engedélyezi diagnosztika futtatását az Azure Key Vault-erőforrásokon egy delegált előfizetésben (a deployIfNotExists hatás használatával). További információt [a javítható szabályzatok delegált előfizetésben való telepítésével foglalkozó részben](../how-to/deploy-policy-remediation.md) talál. |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-sub-monitoring) | Hozzárendel több szabályzatot a diagnosztika futtatásának engedélyezéséhez a delegált előfizetésen, valamint csatlakoztatja az összes windowsos és linuxos virtuális gépet a szabályzat által létrehozott Log Analytics-munkaterülethez. További információt [a javítható szabályzatok delegált előfizetésben való telepítésével foglalkozó részben](../how-to/deploy-policy-remediation.md) talál. |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-initiative) | Kezdeményezést (több kapcsolódó szabályzatdefiníciót) alkalmaz egy delegált előfizetésen. |

## <a name="azure-monitor"></a>Azure Monitor

Ezek a minták bemutatják, hogyan hozhat létre riasztásokat az Azure Monitor használatával az Azure által delegált erőforrás-kezeléshez előkészített előfizetésekhez.

| **Sablon** | **Leírás** |
|---------|---------|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/alert-using-actiongroup) | Ez a sablon létrehoz egy Azure-riasztást, és csatlakoztatja azt egy meglévő műveletcsoporthoz.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/multiple-loganalytics-alerts) | Több naplóriasztást hoz létre Kusto-lekérdezések alapján.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegation-alert-for-customer) | Üzembe helyez egy riasztást a bérlőben, ha a felhasználó előfizetést delegál a kezelést végző bérlő számára.|

## <a name="additional-cross-tenant-scenarios"></a>További több-bérlős forgatókönyvek

Ezek a minták több-bérlős felügyeleti forgatókönyvek esetében végrehajtható feladatokat mutatnak be.

| **Sablon** | **Leírás** |
|---------|---------|
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-rg-deployment) | Tárfiókokat helyez üzembe két különböző erőforráscsoportban.|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-mgmt-services) | Azure-felügyeleti szolgáltatásokat hoz létre, összekapcsolja őket, és további megoldásokat telepít. A teljes körű üzembe helyezéshez használja az **rgWithAzureMgmt.json** sablont. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-security-center) | Engedélyezi és beállítja az Azure Security Centert a célzott Azure-előfizetésen belül. |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-sentinel) | Üzembe helyezi és engedélyezi az Azure Sentinelt egy meglévő Log Analytics-munkaterületen egy delegált előfizetésben. |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-log-analytics-vm-extensions) | Ezekkel a sablonokkal Log Analytics virtuálisgép-bővítményeket helyezhet üzembe windowsos és linuxos virtuális gépein, valamint csatlakoztathatja őket a kijelölt Log Analytics-munkaterülethez |

## <a name="next-steps"></a>További lépések

- Megismerheti az [Azure által delegált erőforrás-kezelés](../concepts/azure-delegated-resource-management.md) részleteit.
- Ismerje meg az [Azure Lighthouse-mintatárat](https://github.com/Azure/Azure-Lighthouse-samples/).
