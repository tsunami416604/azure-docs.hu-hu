---
title: fájl belefoglalása
description: fájl belefoglalása
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 55eee87ed1e07b61a7f8287e86ddc0a7a6aa4df9
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2020
ms.locfileid: "75456751"
---
Ezek a minták bemutatják, hogyan használható az Azure Policy az Azure által delegált erőforrás-kezeléshez előkészített előfizetések esetében.

| **Sablon** | **Leírás** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag) | Hozzárendel egy olyan szabályzatot, amely hozzáad vagy eltávolít egy címkét (a modify hatás használatával) egy delegált előfizetés esetében. További információt [a javítható szabályzatok delegált előfizetésben való telepítésével foglalkozó részben](../articles/lighthouse/how-to/deploy-policy-remediation.md) talál. |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-audit-delegation) | Hozzárendel egy olyan szabályzatot, amely naplózza a delegálási hozzárendeléseket. |
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring) | Hozzárendel egy olyan szabályzatot, amely engedélyezi diagnosztika futtatását az Azure Key Vault-erőforrásokon egy delegált előfizetésben (a deployIfNotExists hatás használatával). További információt [a javítható szabályzatok delegált előfizetésben való telepítésével foglalkozó részben](../articles/lighthouse/how-to/deploy-policy-remediation.md) talál. |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-sub-monitoring) | Hozzárendel több szabályzatot a diagnosztika futtatásának engedélyezéséhez a delegált előfizetésen, valamint csatlakoztatja az összes windowsos és linuxos virtuális gépet a szabályzat által létrehozott Log Analytics-munkaterülethez. További információt [a javítható szabályzatok delegált előfizetésben való telepítésével foglalkozó részben](../articles/lighthouse/how-to/deploy-policy-remediation.md) talál. |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-initiative) | Kezdeményezést (több kapcsolódó szabályzatdefiníciót) alkalmaz egy delegált előfizetésen. |

