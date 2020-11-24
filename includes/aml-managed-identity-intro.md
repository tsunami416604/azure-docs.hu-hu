---
title: fájlbefoglalás
description: fájlbefoglalás
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: 70b636b7bb508b71475a7464983b091d5d10e0e1
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95556237"
---
 Azure Machine Learning a számítási fürtök támogatják az Azure-erőforrásokhoz való hozzáférés hitelesítéséhez szükséges [felügyelt identitásokat](../articles/active-directory/managed-identities-azure-resources/overview.md) is, a kódban szereplő hitelesítő adatok nélkül. A felügyelt identitásoknak két típusa létezik:

* Egy **rendszerhez rendelt felügyelt identitás** közvetlenül a Azure Machine learning számítási fürtön van engedélyezve. A rendszerhez rendelt identitás életciklusa közvetlenül a számítási fürthöz van kötve. Ha a számítási fürt törölve lett, az Azure automatikusan törli a hitelesítő adatokat és az identitást az Azure AD-ben.
* A **felhasználó által hozzárendelt felügyelt identitás** egy önálló Azure-erőforrás, amelyet az Azure felügyelt identitás szolgáltatás biztosít. A felhasználókhoz rendelt felügyelt identitásokat több erőforráshoz is hozzárendelheti, és a kívánt ideig továbbra is fennáll.