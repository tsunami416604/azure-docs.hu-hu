---
title: Telepítse át az Azure-végpontok egyéni beszéd szolgáltatásból tarifacsomagok |} Microsoft Docs
description: Tudnivalók a központi telepítések áttelepíthetők rétegek S0 és S1 S2 az egyéni beszéd Szolgáltatásvégpontok kognitív szolgáltatásban.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/05/2017
ms.author: panosper
ms.openlocfilehash: 6d92459deb3464cd97c215cbf9a8320628b6da80
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347570"
---
# <a name="migrate-deployments-to-the-new-pricing-model"></a>Telepítse át az új árképzési modellt központi telepítések
Től július 2017 egyéni beszéd szolgáltatás nyújt egy [új árképzési modellt](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/). Az új modell *könnyebben érthetőek legyenek*, *egyszerűbb költségek kiszámításához*, és *rugalmasabb* skálázás tekintetében. A méretezés, a Microsoft a méretezési egység fogalma vezetett be. Minden egyes méretezési egység öt egyidejű kérelmek képes kezelni. A régi modell egyidejű kérelmek méretezésének: 5 egyidejű kérelmek szinthez S0 lett beállítva, és lett állítva, akkor a szinthez S1 12 egyidejű kérelmek. Ezek a korlátozások, nagyobb rugalmasság a használati eset követelményeinek, és biztosítani megnyitott azt.

Ha régi S0 vagy S1 réteg futtatja, azt javasoljuk, hogy telepítse át a meglévő telepítések az új S2 réteget. Az új S2 réteg S0 mind az S1 rétegek ismerteti. Az elérhető lehetőségek az alábbi ábrán látható:

![A "Válasszon tarifacsomagot" lap](../../../media/cognitive-services/custom-speech-service/custom-speech-pricing-tier.png)

A Microsoft az áttelepítés félig automatizált módon kezeli. Első lépésként indít el az áttelepítést az új árképzési szint kiválasztásával. Ezután azt telepítse át a telepítés automatikusan.

A méretezési egységek régi rétegek-leképezés a következő táblázatban látható:

| Szint | Egyidejű kérelmek (régi modell) | Migrálás | Egyidejű kérelmek |
|----- | ----- | ---- | ---- |
| S0 |  5   |   => **S2** 1 skálázási egység |   5 |
| S1 |  12  |   => **S2** 3 méretezési egységek |  15 |

Telepítse át az új réteghez, tegye a következőket:

## <a name="step-1-check-your-existing-deployment"></a>1. lépés: A jelenlegi telepítés ellenőrzése
Lépjen a [egyéni beszéd portálon](http://cris.ai), és ellenőrizze a meglévő telepítések. A jelen példában két üzemelő példány van. Egy központi telepítési fut egy S0 réteget, és a más központi telepítés futtatása egy S1 rétegen. A központi telepítések megjelennek-e a **központi telepítési beállítások** a következő táblázat:

![A központi telepítések lap](../../../media/cognitive-services/custom-speech-service/custom-speech-deployments.png)

## <a name="step-2-select-your-new-pricing-tier-in-the-azure-portal"></a>2. lépés: Új tarifacsomag kiválasztása az Azure-portálon
1. Nyisson meg egy új böngészőlapon, és jelentkezzen be a [Azure-portálon](http://ms.portal.azure.com/). 

2. Az a **kognitív szolgáltatások** ablaktáblán, a a **előfizetések** listára, válassza ki az egyéni beszéd előfizetés. 

3. Az előfizetéshez tartozó ablaktáblában jelölje ki **tarifacsomag**.

    ![A "Tarifacsomag" hivatkozásra](../../../media/cognitive-services/custom-speech-service/custom-speech-update-tier.png)

4. Az a **válasszon tarifacsomagot** lapon jelölje be **S2 szabványos**. Ez a tarifacsomag az új, egyszerűbb és rugalmasabb árképzési szint.

5. Válassza ki a **válasszon** gombra.

    ![A "Válasszon tarifacsomagot" lap](../../../media/cognitive-services/custom-speech-service/custom-speech-update-pricing.png)

## <a name="step-3-check-the-migration-status-in-the-custom-speech-service-portal"></a>3. lépés: Az áttelepítési állapotának ellenőrzése az az egyéni beszéd portál
Az egyéni beszéd portál adja vissza, és a központi telepítések ellenőrzése. (Ha a böngészőablakot folyamatban, frissítse azt.) 

A kapcsolódó központi telepítés állapotát kell váltott át *feldolgozása*. Az áttelepítés úgy is ellenőrizheti a **központi telepítési beállítások** oszlop. Nincs információ a méretezési egységek és naplózási most található. A méretezési egységek tükröznie kell azt az előző tarifacsomagot. A naplózás is be kell kapcsolni, a táblázatban látható módon:

![A központi telepítési beállítások oszlop](../../../media/cognitive-services/custom-speech-service/custom-speech-deployments-new.png)


> [!NOTE]
> Ha problémákat még az áttelepítés során, lépjen kapcsolatba velünk.
>

## <a name="next-steps"></a>További lépések
További oktatóanyagokat tekintse meg:
* [Egyéni akusztikus modell létrehozása](cognitive-services-custom-speech-create-acoustic-model.md)
* [Egyéni nyelvi modell létrehozása](cognitive-services-custom-speech-create-language-model.md)
* [Egyéni beszéd-szöveg-végpont létrehozása](cognitive-services-custom-speech-create-endpoint.md)
