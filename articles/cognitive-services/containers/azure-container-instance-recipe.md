---
title: Az Azure Container Instance receptje
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan telepítheti a Cognitive Services-tárolókat az Azure Container Instance szolgáltatásban
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: f247465c7e2c0a212df2821ebc7165d3ee5b15f3
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876657"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Tároló üzembe helyezése és futtatása az Azure Container Instancesben

A következő lépésekkel egyszerűen skálázhatja az Azure Cognitive Services-alkalmazásokat a felhőben az Azure [Container Instances használatával.](https://docs.microsoft.com/azure/container-instances/) A tárolók segítségével az alkalmazások kiépítésére összpontosíthat az infrastruktúra kezelése helyett. A tárolók használatáról további információt a szolgáltatások és előnyök című [témakörben talál.](../cognitive-services-container-support.md#features-and-benefits)

## <a name="prerequisites"></a>Előfeltételek

A recept bármely Cognitive Services-tárolóval működik. A Cognitive Service-erőforrást létre kell hozni az Azure Portalon a recept használata előtt. Minden egyes Cognitive Service, amely támogatja a tárolók egy "Hogyan kell telepíteni" dokumentum kifejezetten a szolgáltatás telepítése és konfigurálása egy tárolóhoz. Egyes szolgáltatások a tároló bemeneteként fájlra vagy fájlkészletre van szükség, fontos, hogy a megoldás használata előtt megértse és sikeresen használja a tárolót.

* Az Azure Portalon létrehozott Cognitive Service-erőforrás.
* Cognitive **Service-végpont URL-cím** – tekintse át az adott szolgáltatás "Hogyan kell telepíteni" a tároló, hogy megtudja, hol a végpont URL-cím az Azure Portalon belül, és milyen a helyes példa az URL-t néz ki. A pontos formátum szolgáltatásról szolgáltatásra változhat.
* Cognitive **Service-kulcs** – a kulcsok az Azure-erőforrás **Keys** oldalán találhatók. Csak egy a két kulcs. A kulcs egy 32 alfanumerikus karakterből álló karakterlánc.
* Egyetlen Cognitive Services-tároló a helyi gazdagépen (a számítógépen). Győződjön meg róla, hogy:
  * Húzza le a `docker pull` képet egy paranccsal.
  * Futtassa a helyi tárolót sikeresen az összes szükséges konfigurációs beállítással egy `docker run` paranccsal.
  * Hívja fel a tároló végpontját, és kapja vissza a HTTP 2xx és a JSON-válasz válaszát.

A szögletes zárójelben `<>`lévő összes változót a saját értékeire kell cserélni. Ez a csere magában foglalja a szögletes zárójeleket.

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](includes/create-container-instances-resource.md)]

## <a name="use-the-container-instance"></a>A tárolópéldány használata

1. Válassza ki az **Áttekintés lehetőséget,** és másolja az IP-címet. Ez egy numerikus IP-cím lesz, például `55.55.55.55`.
1. Nyisson meg egy új böngészőlapot, és `http://<IP-address>:5000 (http://55.55.55.55:5000`használja például az IP-címet). Látni fogja a tároló kezdőlapját, amely tudatja, hogy a tároló fut.

1. Válassza **a Service API leírása a** tároló swagger lapjának megtekintéséhez válassza a szolgáltatás leírását.

1. Válassza ki bármelyik **POST** API-t, és válassza **a Kipróbálás**lehetőséget .  A paraméterek a bemenettel együtt jelennek meg. Töltse ki a paramétereket.

1. Válassza **a Végrehajtás** lehetőséget a kérelem nek a tárolópéldánynak való elküldéséhez.

    Sikeresen létrehozott és használt Kognitív szolgáltatások tárolók az Azure Container Instance.You have successfully created and used Cognitive Services containers in Azure Container Instance.
