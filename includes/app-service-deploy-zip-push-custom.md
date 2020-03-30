---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 79fb8517ec6880e8a3eae0e74275567a24644b87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67178922"
---
## <a name="deployment-customization"></a>A telepítés testreszabása

A központi telepítési folyamat feltételezi, hogy a leadott .zip fájl egy használatra kész alkalmazást tartalmaz. Alapértelmezés szerint nem fut testreszabás. Ha ugyanazokat a buildfolyamatokat szeretné engedélyezni, mint a folyamatos integráció, adja hozzá az alábbi beállításokat az alkalmazásbeállításokhoz:

    SCM_DO_BUILD_DURING_DEPLOYMENT=true 

A .zip push deployment használatakor ez a beállítás alapértelmezés szerint **hamis.** Az alapértelmezett érték a folyamatos integrációs telepítések esetén **igaz.** Ha a beállítás **igaz,** a központi telepítéssel kapcsolatos beállításokat a központi telepítés során használja. Ezeket a beállításokat alkalmazásbeállításokként vagy a .zip fájl gyökerében található .deployment konfigurációs fájlban is konfigurálhatja. További információ: [Repository and Deployment-related settings](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings) in the deployment reference.