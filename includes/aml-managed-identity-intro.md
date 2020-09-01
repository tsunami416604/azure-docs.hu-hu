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
ms.openlocfilehash: d8ee7b61897a9718d7078fc16eddc177fefbff43
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147478"
---
 Azure Machine Learning a számítási fürtök támogatják az Azure-erőforrásokhoz való hozzáférés hitelesítéséhez szükséges [felügyelt identitásokat](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) is, a kódban szereplő hitelesítő adatok nélkül. A felügyelt identitásoknak két típusa létezik:

* Egy **rendszerhez rendelt felügyelt identitás** közvetlenül a Azure Machine learning számítási fürtön van engedélyezve. A rendszerhez rendelt identitás életciklusa közvetlenül a számítási fürthöz van kötve. Ha a számítási fürt törölve lett, az Azure automatikusan törli a hitelesítő adatokat és az identitást az Azure AD-ben.
* A **felhasználó által hozzárendelt felügyelt identitás** egy önálló Azure-erőforrás, amelyet az Azure felügyelt identitás szolgáltatás biztosít. A felhasználókhoz rendelt felügyelt identitásokat több erőforráshoz is hozzárendelheti, és a kívánt ideig továbbra is fennáll.