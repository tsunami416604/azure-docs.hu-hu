---
title: fájlbefoglalás
description: fájlbefoglalás
services: cognitive-services
author: roy-har
manager: diberry
ms.service: cognitive-services
ms.date: 06/03/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: roy-har
ms.openlocfilehash: cffc9af2ef93ec44e2af9363c0040d1f22d945f7
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2020
ms.locfileid: "84416385"
---
Hozza létre a pizza alkalmazást.

1. Válassza a [pizza-app-for-Luis-v6. JSON](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-app-for-luis-v6.json) lehetőséget a fájl GitHub-oldalának megadásához `pizza-app-for-luis.json` .
1. Kattintson a jobb gombbal a **RAW** gombra, és válassza a **hivatkozás mentése másként** lehetőséget a számítógépre való mentéshez `pizza-app-for-luis.json` .
1. Jelentkezzen be a [Luis portálra](https://www.luis.ai).
1. Válassza [a saját alkalmazások](https://www.luis.ai/applications)lehetőséget.
1. A **saját alkalmazások** lapon válassza az **+ új alkalmazás lehetőséget a beszélgetéshez**.
1. Válassza **az IMPORTÁLÁS JSON-ként**lehetőséget.
1. Az **új alkalmazás importálása** párbeszédpanelen kattintson a **fájl kiválasztása** gombra.
1. Válassza ki a `pizza-app-for-luis.json` letöltött fájlt, majd kattintson a **Megnyitás**gombra.
1. Az **új alkalmazás importálása** párbeszédpanel **név** mezőjébe írja be a pizza-alkalmazás nevét, majd kattintson a **kész** gombra.

Az alkalmazás importálva lesz.

Ha megjelenik egy párbeszédpanel, amely **bemutatja, hogyan hozhat létre egy hatékony Luis-alkalmazást**, zárjuk be a párbeszédpanelt.

## <a name="train-and-publish-the-pizza-app"></a>A pizza-alkalmazás betanítása és közzététele

Meg kell jelennie a **szándékok** oldalnak a pizza-alkalmazásban található leképezések listájával.

[!INCLUDE [How to train](howto-train.md)]

[!INCLUDE [How to publish](howto-publish.md)]

## <a name="add-an-authoring-resource-to-the-pizza-app"></a>Szerzői erőforrás hozzáadása a pizza-alkalmazáshoz

1. Válassza a **kezelés**lehetőséget.
1. Válassza ki az **Azure-erőforrásokat**.
1. Válassza az **erőforrás létrehozása**lehetőséget.
1. Válassza a **szerzői erőforrás módosítása**lehetőséget.

Ha szerzői erőforrással rendelkezik, adja meg a **bérlő nevét**, az **előfizetés nevét**és a szerzői erőforrás **Luis-erőforrásának nevét** .

Ha nem rendelkezik szerzői erőforrással:

1. Válassza az **új erőforrás létrehozása**lehetőséget.
1. Adja meg a **bérlő nevét**, az **erőforrás nevét**, az **előfizetés nevét**és az **Azure-erőforráscsoport nevét**.

A pizza-alkalmazás most már készen áll a használatra.

## <a name="record-the-app-id-prediction-key-and-prediction-endpoint-of-your-pizza-app"></a>A pizza-alkalmazás alkalmazás-AZONOSÍTÓjának, előrejelzési kulcsának és előrejelzési végpontjának rögzítése

Az új pizza-alkalmazás használatához szüksége lesz az alkalmazás-AZONOSÍTÓra, a szerzői kulcsra és a pizza-alkalmazás szerzői végpontra.

A következő értékek megkeresése:

1. A **leképezések** lapon válassza a **kezelés**lehetőséget.
1. Az **Alkalmazásbeállítások** lapon jegyezze fel az **alkalmazás azonosítóját**.
1. Válassza ki az **Azure-erőforrásokat**.
1. Válassza az **erőforrás létrehozása**lehetőséget.
1. A **szerzői erőforrás** lapon jegyezze fel az **elsődleges kulcsot**. Ez az érték a szerzői kulcs.
1. Jegyezze fel a **végpont URL-címét**. Ez az érték a szerzői végpont.
