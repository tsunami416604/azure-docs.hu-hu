---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 5e0cff7bde6e80a776d694820ca7b69dafa7c0d9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83648814"
---
A privát helyhez való hozzáférés arra utal, hogy az alkalmazás csak magánhálózat, például egy Azure virtuális hálózat számára legyen elérhető.

* A privát helyhez való hozzáférés a szolgáltatási végpontok konfigurálásakor a [prémium](../articles/azure-functions/functions-premium-plan.md), a [fogyasztási](../articles/azure-functions/functions-scale.md#consumption-plan)és a [app Servicei](../articles/azure-functions/functions-scale.md#app-service-plan) csomagban érhető el.
    * A szolgáltatási végpontok a **platform szolgáltatásai**  >  **hálózatkezelés**  >  **konfigurálása hozzáférési korlátozások megadása**  >  **szabály hozzáadása**alkalmazás alapján konfigurálhatók. A virtuális hálózatok mostantól szabálytípusként is kiválaszthatók.
    * További információ: [Virtual Network szolgáltatás-végpontok](../articles/virtual-network/virtual-network-service-endpoints-overview.md).
    * Ne feledje, hogy a szolgáltatási végpontok esetében a függvény továbbra is teljes kimenő hozzáférést biztosít az internethez, még a konfigurált Virtual Network Integration is.
* A privát hely elérését egy belső terheléselosztó (ILB) használatára konfigurált App Service Environmenton belül is elérhető. További információ: [belső terheléselosztó létrehozása és használata app Service Environmentsal](../articles/app-service/environment/create-ilb-ase.md).

A privát helyek hozzáférésének beállításával kapcsolatos további információkért lásd: [Azure functions Private site Access létrehozása](../articles/azure-functions/functions-create-private-site-access.md).