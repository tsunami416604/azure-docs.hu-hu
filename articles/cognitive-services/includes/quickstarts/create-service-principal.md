---
title: Azure-szolgáltatásnév létrehozása
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: 5cff103d17138608f4932e36444847437f6da236
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89321659"
---
## <a name="create-an-azure-service-principal"></a>Azure egyszerű szolgáltatás létrehozása

Ahhoz, hogy az alkalmazás működjön az Azure-fiókkal, szüksége lesz egy Azure-szolgáltatásra az engedélyek kezeléséhez. Kövesse az [Azure egyszerű szolgáltatás létrehozása](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0)című témakör utasításait.

Egyszerű szolgáltatás létrehozásakor látni fogja, hogy rendelkezik egy titkos értékkel, egy AZONOSÍTÓval és egy alkalmazás-AZONOSÍTÓval. Mentse az alkalmazás AZONOSÍTÓját és a titkos kulcsot egy ideiglenes helyre a későbbi lépések végrehajtásához.