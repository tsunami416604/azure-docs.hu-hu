---
title: Áttelepítése tarifacsomagok végpontok a Custom Speech Service az Azure-ban |} A Microsoft Docs
description: Megtudhatja, hogyan központi telepítések áttelepíthetők S0 és S1 szint a Cognitive Services S2 a Custom Speech Service-végpont.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/05/2017
ms.author: panosper
ms.openlocfilehash: a9bdb257137db0063d39f028a69e2164eccbdc31
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49340460"
---
# <a name="migrate-deployments-to-the-new-pricing-model"></a>Az új díjszabási modellre központi telepítései áttelepítésének

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

2017. július Custom Speech Service kínál egy [új díjszabási modell](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/). Az új modell *megérteni*, *egyszerűbb költségek kiszámítása,*, és *rugalmasabb* méretezése szempontjából. A méretezés, a Microsoft a méretezési egység fogalma vezetett be. Minden egyes méretezési egység öt kérés párhuzamos képes kezelni. 5 egyidejű kérelmek S0 szint, az egyidejű kérelmek a régi konstrukcióban skálázást lett beállítva, és azt lett beállítva, az S1 szint 12 egyidejű kérelmek. Ezeket a korlátokat kínáló, nagyobb rugalmasságot biztosít a használatieset-követelményeinek megnyitotta azt.

Ha régi S0 vagy az S1 szintet futtatja, javasoljuk, hogy a meglévő telepítések telepíti át az új S2 szintű. Az új S2 szintű az S0 és a S1 szint kiterjed. Az elérhető lehetőségek az alábbi ábrán látható:

![A "Tarifacsomag kiválasztása" lap](../../../media/cognitive-services/custom-speech-service/custom-speech-pricing-tier.png)

A Microsoft kezeli az áttelepítés félig automatikus módon. Először indít el az áttelepítést az új tarifacsomag kiválasztásával. Majd hogy telepítse át a központi telepítés automatikusan.

A régi szintek egységig leképezése az alábbi táblázatban látható:

| Szint | Egyidejű kérelmek (a régi konstrukcióban) | Migrálás | Egyidejű kérelmek |
|----- | ----- | ---- | ---- |
| S0 |  5   |   => **S2** az 1 skálázási egység |   5 |
| S1 |  12  |   => **S2** 3 skálázási egységekkel |  15 |

Az új csomag át, tegye a következőket:

## <a name="step-1-check-your-existing-deployment"></a>1. lépés: A meglévő telepítés ellenőrzése
Nyissa meg a [Custom Speech Service portal](http://cris.ai), és a meglévő telepítések ellenőrzése. Ebben a példában a rendszer két üzembe helyezés. Egy központi telepítési az S0 csomag fut, és a központi telepítés egy S1 szintű futtat. Jelennek meg a központi telepítések a **központi telepítési beállítások** oszlop a következő tábla:

![A központi telepítések lap](../../../media/cognitive-services/custom-speech-service/custom-speech-deployments.png)

## <a name="step-2-select-your-new-pricing-tier-in-the-azure-portal"></a>2. lépés: Új tarifacsomag kiválasztása az Azure Portalon
1. Nyisson meg egy új böngészőlapot, és jelentkezzen be a [az Azure portal](http://ms.portal.azure.com/). 

2. Az a **Cognitive Services** ablaktáblán, a a **előfizetések** listájához, válassza ki az egyéni beszédfelismerési előfizetés. 

3. Az előfizetéshez tartozó panelén válassza **tarifacsomag**.

    ![A "Tarifacsomag" hivatkozásra](../../../media/cognitive-services/custom-speech-service/custom-speech-update-tier.png)

4. Az a **válassza ki a tarifacsomagot** lapon jelölje be **S2 Standard**. Ez a tarifacsomag az új, egyszerűbb és rugalmasabb tarifacsomagot.

5. Válassza ki a **kiválasztása** gombra.

    ![A "Tarifacsomag kiválasztása" lap](../../../media/cognitive-services/custom-speech-service/custom-speech-update-pricing.png)

## <a name="step-3-check-the-migration-status-in-the-custom-speech-service-portal"></a>3. lépés: Ellenőrizze a Custom Speech Service portálon a migrálás állapota
A Custom Speech Service portálhoz adja vissza, és ellenőrizze az üzemelő példányok. (Ha a böngészőablak folyamatban, frissítse azt.) 

A kapcsolódó központi telepítés állapotát kell vált *feldolgozása*. Az áttelepítés úgy is ellenőrizheti a **központi telepítési beállítások** oszlop. Most már megtalálhatja van skálázási egységek és a naplózási információkat. A skálázási egység tükröznie kell azt az előző tarifacsomagot. A naplózás is be kell kapcsolni, a táblázatban látható módon:

![A központi telepítési beállítások oszlop](../../../media/cognitive-services/custom-speech-service/custom-speech-deployments-new.png)


> [!NOTE]
> Ha problémába ütközik az áttelepítés során, lépjen kapcsolatba velünk.
>

## <a name="next-steps"></a>További lépések
További oktatóanyagokban talál:
* [Egyéni akusztikai modell létrehozása](cognitive-services-custom-speech-create-acoustic-model.md)
* [Testreszabott nyelvi modell létrehozása](cognitive-services-custom-speech-create-language-model.md)
* [Hozzon létre egy egyéni hang-szöveg transzformációs végpontot](cognitive-services-custom-speech-create-endpoint.md)
