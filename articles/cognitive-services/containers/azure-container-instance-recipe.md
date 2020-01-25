---
title: Az Azure Container instance receptje
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan helyezhet üzembe Cognitive Services tárolókat az Azure Container instance szolgáltatásban
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 78f35042678aa7c30cebf73796df3e5d564b4502
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717001"
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
  * Futtassa a helyi tárolót sikeresen az összes szükséges konfigurációs beállítással `docker run` paranccsal.
  * Hívja meg a tároló végpontját, válaszoljon a HTTP-2xx és egy JSON-válaszra.

A szögletes zárójelben lévő összes változót a saját értékeire kell cserélni, `<>`. Ez a csere tartalmazza a szögletes zárójeleket.

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](includes/create-container-instances-resource.md)]

## <a name="use-the-container-instance"></a>A Container-példány használata

1. Válassza ki az **áttekintést** , és másolja ki az IP-címet. Ez egy numerikus IP-cím lesz, például `55.55.55.55`.
1. Nyisson meg egy új böngésző fület, és használja az IP-címet, például `http://<IP-address>:5000 (http://55.55.55.55:5000`). Ekkor megjelenik a tároló kezdőlapja, amely arról tájékoztatja, hogy a tároló fut.

1. Válassza ki a **szolgáltatás API leírása** elemet a tárolóhoz tartozó felvágási oldal megtekintéséhez.

1. Válassza ki a **post** API-k bármelyikét, és válassza a **kipróbálás**lehetőséget.  A paraméterek megjelennek a bemenettel együtt. Adja meg a paramétereket.

1. Válassza a **végrehajtás** lehetőséget, hogy elküldje a kérést a tároló példányának.

    Sikeresen létrehozta és használta Cognitive Services tárolókat az Azure Container Instanceban.
