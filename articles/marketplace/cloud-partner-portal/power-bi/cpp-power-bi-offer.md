---
title: Power BI alkalmazás ajánlata | Azure piactér
description: Power BI alkalmazás közzététele a Microsoft AppSource piactéren.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: pabutler
ms.openlocfilehash: d1974133562c0bb8b8006ea2ef323e6797643b67
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826075"
---
# <a name="power-bi-app-offer"></a>Power BI-alkalmazásajánlat

|              |                                |
|--------------|--------------------------------|
| Ez a cikk azt ismerteti, hogyan tehet közzé egy Power BI alkalmazást a Microsoft [AppSource piactéren](https://appsource.microsoft.com/).  A Power BIi alkalmazáscsomag testreszabható Power BI tartalmakat, például adatkészleteket, jelentéseket és irányítópultokat. Ezután központilag telepítheti az alkalmazást más Power BI bérlők számára a AppSource-on keresztül, végrehajthatja a fejlesztő által engedélyezett módosításokat és testreszabásokat, és összekapcsolhatók a saját adataival. | ![Power BI ikon](./media/powerbi-icon.png) |


Ez a cikk három fő részből áll:

-   [Előfeltételek](./cpp-prerequisites.md). A Power BI-alkalmazások létrehozásának és közzétételének technikai és üzleti követelményei.
-   [Hozzon létre egy Power bi alkalmazás-ajánlatot](./cpp-create-offer.md). Power BI app Offer-bejegyzés létrehozása a [Cloud Partner Portal](https://cloudpartner.azure.com)használatával.
-   [Power bi alkalmazás-ajánlat közzététele](./cpp-publish-offer.md). Új ajánlat beküldése a AppSource közzétételre, valamint egy meglévő ajánlat frissítése.


## <a name="publishing-steps"></a>Közzétételi lépések

A Power BI alkalmazás-ajánlat közzétételének magas szintű lépései:

![Power BI alkalmazás-ajánlat közzétételi lépései](media/publishing-steps.png)

Itt látható a Power BI alkalmazás-ajánlat közzétételi folyamata:

1. Sablonbeli alkalmazás létrehozása Power BIban. Ez a művelet létrehoz egy csomag-telepítési URL-címet, amely az ajánlat elsődleges technikai eszközét jelöli. Jelenleg is népszerűsíti a tesztelési csomagot az üzem előtti időszakban. További információ: [Mi a Power bi template apps?](https://docs.microsoft.com/power-bi/service-template-apps-overview). 
2. Gyűjtse össze vagy hozza létre az ajánlat marketing-anyagát, beleértve a következőket: hivatalos név, leírás, emblémák stb. 
3. Gyűjtse össze vagy hozza létre az ajánlat jogi és támogatási dokumentumait: használati *feltételek*, *adatvédelmi szabályzat*, *támogatási szabályzat*, felhasználói súgó stb.
4. Ajánlat létrehozása: a Cloud Partner Portal használatával konfigurálhatja az ajánlat részleteit, beleértve az ajánlat leírását, a marketing-anyagokat, a jogi információkat, a támogatási információkat és az eszközök specifikációit.  Az ajánlat teljes megadása után küldje el közzétételre.
5. Figyelje a közzétételi folyamatot a Cloud Partner Portalban.  Ebben a lépésben a AppSource bevezetési csapata teszteli, ellenőrzi és tanúsítja az alkalmazást. 
6. Az alkalmazás sikeres minősítése után ellenőrizze azt a tesztkörnyezetben, és szabadítsa fel. 
7. A Power BI alkalmazás szerepel a AppSource (IT "élő").
8. Power BI az éles üzem előtti csomagot az üzemi környezetbe előléptetheti. További információ: [a sablon alkalmazás kiadásának kezelése](https://docs.microsoft.com/power-bi/service-template-apps-create#manage-the-template-app-release).


## <a name="next-steps"></a>További lépések

Mielőtt létrehozza a Power BI-alkalmazás ajánlatát, és közzéteszi a AppSource-ben, meg kell felelnie a Power BI alkalmazás AppSource való közzétételének [követelményeinek](./cpp-prerequisites.md) .
