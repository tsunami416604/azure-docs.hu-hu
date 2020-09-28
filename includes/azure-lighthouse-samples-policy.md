---
title: fájl belefoglalása
description: fájl belefoglalása
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 09/24/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: a9c9b5081232988cb4ab80bfcbb9f2d92d1d025f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336572"
---
Ezek a minták bemutatják, hogyan használható az Azure Policy az Azure Lighthouse-hoz előkészített előfizetésekkel.

| **Sablon** | **Leírás** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) | Hozzárendel egy olyan szabályzatot, amely hozzáad vagy eltávolít egy címkét (a modify hatás használatával) egy delegált előfizetés esetében. További információt [a javítható szabályzatok delegált előfizetésben való telepítésével foglalkozó részben](../articles/lighthouse/how-to/deploy-policy-remediation.md) talál. |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-audit-delegation) | Hozzárendel egy olyan szabályzatot, amely naplózza a delegálási hozzárendeléseket. |
| [policy-delegate-management-groups](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups) | Hozzárendel egy szabályzatot annak ellenőrzéséhez, hogy a felügyeleti csoporton belüli előfizetések delegálása egy felügyelt bérlőhöz megtörtént-e, és ha nem, akkor létrehozza a hozzárendelést.
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring) | Hozzárendel egy olyan szabályzatot, amely engedélyezi diagnosztika futtatását az Azure Key Vault-erőforrásokon egy delegált előfizetésben (a deployIfNotExists hatás használatával). További információt [a javítható szabályzatok delegált előfizetésben való telepítésével foglalkozó részben](../articles/lighthouse/how-to/deploy-policy-remediation.md) talál. |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-sub-monitoring) | Hozzárendel több szabályzatot a diagnosztika futtatásának engedélyezéséhez a delegált előfizetésen, valamint csatlakoztatja az összes windowsos és linuxos virtuális gépet a szabályzat által létrehozott Log Analytics-munkaterülethez. További információt [a javítható szabályzatok delegált előfizetésben való telepítésével foglalkozó részben](../articles/lighthouse/how-to/deploy-policy-remediation.md) talál. |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-initiative) | Kezdeményezést (több kapcsolódó szabályzatdefiníciót) alkalmaz egy delegált előfizetésen. |

