---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 79fb8517ec6880e8a3eae0e74275567a24644b87
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132557"
---
## <a name="deployment-customization"></a>Telepítés testreszabása

Az üzembe helyezési folyamat azt feltételezi, hogy a .zip-fájlt, hogy az tartalmaz egy futtatásra kész alkalmazást. Alapértelmezés szerint nem testre szabott futnak. Ahhoz, hogy az azonos összeállítási folyamatairól a folyamatos integráció, az alkalmazás beállításait adja hozzá a következőket:

    SCM_DO_BUILD_DURING_DEPLOYMENT=true 

.Zip ügyfélleküldéses telepítés használata esetén ez a beállítás akkor **hamis** alapértelmezés szerint. Az alapértelmezett érték **igaz** folyamatos integráció központi telepítésekhez. Ha a beállítása **igaz**, a telepítéssel kapcsolatos beállítások telepítése során van szükség. Konfigurálhatja ezeket a beállításokat alkalmazásbeállításokként vagy egy .deployment konfigurációs fájlban, amely a .zip fájl gyökerében található. További információkért lásd: [tárházat és a jelentéskészítéssel kapcsolatos beállítások](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings) a telepítési útmutatóban.