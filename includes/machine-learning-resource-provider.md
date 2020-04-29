---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/09/2020
ms.author: larryfr
ms.openlocfilehash: 8fd774f8a3a73ceaffa7902b35e1b1dff12ef5af
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75893971"
---
Azure Machine Learning munkaterület vagy a munkaterület által használt erőforrás létrehozásakor a következő üzenetekhez hasonló hibaüzenet jelenhet meg:

* `No registered resource provider found for location {location}`
* `The subscription is not registered to use namespace {resource-provider-namespace}`

A legtöbb erőforrás-szolgáltató automatikusan regisztrálva van, de nem az összes. Ha ezt az üzenetet kapja, regisztrálnia kell a megemlített szolgáltatót.

Az erőforrás-szolgáltatók regisztrálásával kapcsolatos információkért lásd: [hibák elhárítása az erőforrás-szolgáltató regisztrálásához](../articles/azure-resource-manager/templates/error-register-resource-provider.md).