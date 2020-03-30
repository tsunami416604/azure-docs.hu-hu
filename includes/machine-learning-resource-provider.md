---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/09/2020
ms.author: larryfr
ms.openlocfilehash: 8fd774f8a3a73ceaffa7902b35e1b1dff12ef5af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75893971"
---
Az Azure Machine Learning-munkaterület vagy a munkaterület által használt erőforrás létrehozásakor a következő üzenetekhez hasonló hibaüzenet jelenhet meg:

* `No registered resource provider found for location {location}`
* `The subscription is not registered to use namespace {resource-provider-namespace}`

A legtöbb erőforrás-szolgáltató automatikusan regisztrálva van, de nem az összes. Ha ezt az üzenetet kapja, regisztrálnia kell az említett szolgáltatót.

Az erőforrás-szolgáltatók regisztrálásáról az [Erőforrás-szolgáltató regisztrációjának hibáinak megoldása című](../articles/azure-resource-manager/templates/error-register-resource-provider.md)témakörben talál további információt.