---
title: Az Azure Container instance receptje
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan helyezhet üzembe Cognitive Services tárolókat az Azure Container instance szolgáltatásban
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: f247465c7e2c0a212df2821ebc7165d3ee5b15f3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876657"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Tároló üzembe helyezése és futtatása az Azure Container Instancesben

A következő lépésekkel egyszerűen méretezheti az Azure Cognitive Services-alkalmazásait a felhőben az Azure [Container instances](https://docs.microsoft.com/azure/container-instances/). A tárolókra bontás segítségével az infrastruktúra kezelése helyett az alkalmazások kiépítésére koncentrálhat. További információ a tárolók használatáról: [szolgáltatások és előnyök](../cognitive-services-container-support.md#features-and-benefits).

## <a name="prerequisites"></a>Előfeltételek

A recept bármilyen Cognitive Services tárolóval működik. A recept használata előtt létre kell hozni a kognitív szolgáltatás erőforrását a Azure Portalban. Minden olyan kognitív szolgáltatás, amely támogatja a tárolókat, a "hogyan lehet telepíteni" dokumentumot kifejezetten egy tároló szolgáltatásának telepítéséhez és konfigurálásához. Egyes szolgáltatások a tárolóhoz tartozó fájl-vagy fájl-készletet igényelnek, fontos, hogy megértse és használta a tárolót a megoldás használata előtt.

* A Azure Portalban létrehozott kognitív szolgáltatási erőforrás.
* A kognitív szolgáltatás **végpontjának URL-címe** – tekintse át az adott szolgáltatás "a telepítésének" módját a tároló számára, hogy megtudja, hol található a végpont URL-címe a Azure Portalon, és hogy az URL-cím helyes példája a következőképpen néz ki:. A pontos formátum a szolgáltatásról a szolgáltatásra változhat.
* Kognitív szolgáltatás **kulcsa** – a kulcsok az Azure-erőforrás **kulcsok** oldalán találhatók. Csak a két kulcs egyikét kell megadnia. A kulcs 32 alfanumerikus karakterekből álló karakterlánc.
* Egyetlen Cognitive Services tároló a helyi gazdagépen (a számítógépen). Győződjön meg arról, hogy a következőket teheti:
  * Húzza le a rendszerképet egy `docker pull` paranccsal.
  * Futtassa a helyi tárolót sikeresen az összes szükséges konfigurációs beállítással `docker run` a paranccsal.
  * Hívja meg a tároló végpontját, válaszoljon a HTTP-2xx és egy JSON-válaszra.

A szögletes zárójelben `<>`lévő összes változót a saját értékeivel kell helyettesíteni. Ez a csere tartalmazza a szögletes zárójeleket.

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](includes/create-container-instances-resource.md)]

## <a name="use-the-container-instance"></a>A Container-példány használata

1. Válassza ki az **áttekintést** , és másolja ki az IP-címet. Ez egy numerikus IP-cím lesz, `55.55.55.55`például:.
1. Nyisson meg egy új böngésző fület, és használja az IP-címet `http://<IP-address>:5000 (http://55.55.55.55:5000`, például:). Ekkor megjelenik a tároló kezdőlapja, amely arról tájékoztatja, hogy a tároló fut.

1. Válassza ki a **szolgáltatás API leírása** elemet a tárolóhoz tartozó felvágási oldal megtekintéséhez.

1. Válassza ki a **post** API-k bármelyikét, és válassza a **kipróbálás**lehetőséget.  A paraméterek megjelennek a bemenettel együtt. Adja meg a paramétereket.

1. Válassza a **végrehajtás** lehetőséget, hogy elküldje a kérést a tároló példányának.

    Sikeresen létrehozta és használta Cognitive Services tárolókat az Azure Container Instanceban.
