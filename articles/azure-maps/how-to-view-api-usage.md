---
title: Az Azure Maps API-használati mutatóinak megtekintése | Microsoft Azure Maps
description: Ebből a cikkből megtudhatja, hogyan tekintheti meg a Microsoft Azure Maps API-hívások metrikákat az Azure Portalon.
author: philmea
ms.author: philmea
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 0eb117af712b3b1f63a3f99c96cba9775f8e3996
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335155"
---
# <a name="view-azure-maps-api-usage-metrics"></a>Az Azure Maps API használati metrikáinak megtekintése

Ez a cikk bemutatja, hogyan tekintheti meg az API-használati metrikákat az Azure Maps-fiókjához az [Azure Portalon.](https://portal.azure.com) A mérőszámok kényelmes grafikonformátumban jelennek meg egy testreszabható időtartam mentén.

## <a name="view-metric-snapshot"></a>Mérőszám-pillanatkép megtekintése

Néhány gyakori mutatót a Térkép-fiók **Áttekintés** oldalán tekinthet meg. Jelenleg *az Összes kérelem,* *az Összes hiba*és *a Rendelkezésre állás* látható egy választható időtartamon keresztül.

![Az Azure Maps használati mutatói – áttekintés](media/how-to-view-api-usage/portal-overview.png)

Folytassa a következő szakaszra, ha testre kell szabnia ezeket a diagramokat az adott elemzéshez.

## <a name="view-detailed-metrics"></a>Részletes mutatók megtekintése

1. Jelentkezzen be Azure-előfizetése bea [portálon.](https://portal.azure.com)

2. Kattintson a bal oldalon a **Minden erőforrás** menüelemre, és keresse meg az *Azure Maps-fiókot.*

3. Miután megnyitotta mapsfiókját, kattintson a bal oldali **Mérőszámok** menüre.

4. A **Metrikák** ablaktáblán válasszon az alábbi lehetőségek közül:

   1. **Elérhetőség** – amely az API-rendelkezésre állás *egy* adott időszakban átlagosan jeleníti meg.
   2. **Használat** – amely megmutatja, hogy a fiók használata hogyan *számít.*

      ![Az Azure Maps használati mérőszámok ablaktáblája](media/how-to-view-api-usage/portal-metrics.png)

5. Ezután az *Időtartományt* az **Elmúlt 24 óra (Automatikus)** gombra kattintva választhatja ki. Alapértelmezés szerint az időtartomány 24 órára van állítva. Kattintás után az összes választható időtartomány megjelenik. Kiválaszthatja az *Idő részletességét, és kiválaszthatja,* hogy az időt *helyiként* vagy *GMT-ként* jelenítse meg ugyanabban a legördülő menüben. Kattintson az **Alkalmaz** gombra.

    ![Az Azure Maps metrikák időtartománya](media/how-to-view-api-usage/time-range.png)

6. Miután hozzáadja a metrikát, **szűrőt adhat hozzá** az adott mutatóhoz tartozó tulajdonságokból. Ezután válassza ki a diagramon tükröződő tulajdonság értékét.

    ![Azure Maps használati metrikák szűrő](media/how-to-view-api-usage/filter.png)

7. A **metrika felosztását** is alkalmazhatja a kiválasztott metrikatulajdonság alapján. Lehetővé teszi, hogy a diagram több grafikonra legyen felosztva, a tulajdonság minden egyes értékéhez. A következő képen az egyes grafikonok színe megegyezik a diagram alján látható tulajdonságértékkel.

    ![Az Azure Maps használati metrikáinak felosztása](media/how-to-view-api-usage/splitting.png)

8. Ugyanazon a grafikonon több mutatót is megfigyelhet, egyszerűen kattintson a képtetején a **Metrika hozzáadása** gombra.

## <a name="next-steps"></a>További lépések

További információ az Azure Maps API-król, amelyek használatát nyomon szeretné követni:
> [!div class="nextstepaction"] 
> [Az Azure Maps Webes SDK útmutatója](how-to-use-map-control.md)

> [!div class="nextstepaction"] 
> [Azure Maps Android SDK útmutató](how-to-use-android-map-control-library.md)

> [!div class="nextstepaction"]
> [Az Azure Maps REST API dokumentációja](https://docs.microsoft.com/rest/api/maps)
