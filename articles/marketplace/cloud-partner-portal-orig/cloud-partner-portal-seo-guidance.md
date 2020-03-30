---
title: Az Azure Piactér SEO-útmutatója
description: Útmutatást nyújt a keresőoptimalizálás (SEO) maximalizálásához.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: dsindona
ms.openlocfilehash: 761cdc2233bce3619d4c2c9ce1d7d7177d3bc407
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280150"
---
# <a name="azure-marketplace-seo-guidance"></a>Az Azure Piactér SEO-útmutatója

Ebből a cikkből megtudhatja, hogyan maximalizálhatja az ajánlat felderíthetőségét az [Azure Piactéren](https://azuremarketplace.microsoft.com) és az [AppSource-ban](https://appsource.microsoft.com)található keresési funkciókon keresztül. 


## <a name="general-explanation-of-algorithm"></a>Az algoritmus általános magyarázata

A Microsoft piacterei az Azure Cognitive Search segítségével működtetik a webhely keresési lehetőségeit. Az algoritmus a kifejezés gyakorisága-inverz dokumentum gyakoriságán ([TF-IDF](https://en.wikipedia.org/wiki/Tf–idf)) alapul. A standard [Lucene Analyzer-t](https://lucene.apache.org/core/) használják.

Általánosságban elmondható, hogy az összes szövegmező, kategória és iparág szerepel a relevancia súlyozásában. Azok a speciális kifejezések, amelyeket az alkalmazások ritkán használnak, de az alkalmazásban gyakran, magasabb egyezési pontszámot eredményeznek a kereséssel. Így a kifejezések, mint a "VM" kínálna kevés előnnyel, mivel az "Azure search" lenne sokkal speciális.
Az alábbiakban a legfontosabb területeket kell figyelembe venni.

 
|  Mező                   | Fontosság | Útmutatás                                                                                            |
|  --------------------    | ----------                   | ---------------                                                                   |
| Offer Name               |  Magasság      | A keresési lekérdezéssel való teljes egyezés pontos vagy ahhoz közeli találat magas helyezést eredményez.                       |
| Publisher Name           |  Magasság      | A keresési lekérdezéssel való teljes egyezés pontos vagy ahhoz közeli találat magas helyezést eredményez.                       |
| Rövid leírás        |  Közepes    | Az alkalmazások és a közzétevők nevének elnevezése szinte garantálja a magas rangú, nem feltétlenül a leginkább releváns. Ebben az esetben a rövid leírás kritikus fontosságú. Tartsa a szöveget tömörés a lényegre. A legjobb eredmény érdekében kulcsszavakat és a várt keresési kifejezéseket kell megadni.  Például a "Ez a Dynamics 365 tetejére teljesen épített legjobb kiskereskedelmi POS" kevésbé hatékony, mint a "Retail POS (értékesítési pont) a Dynamics 365-nek".  | 
| Hosszú leírás         |  Alacsony       | Leírás kínál egy módja annak, hogy bemegy mélyebbre. A leghatékonyabb leírások ésszerű hosszúságúak és kulcsszavakat használnak.  A kulcsszavakat használó, pontig történő leírások több mint hosszú, hosszadalmas szöveget eredményeznek. Győződjön meg arról, hogy a kulcskifejezések, például az "IoT", szerepelnek a leírásban.  |
| Termékkategóriák       | Közepes     |  A termékkategóriákat a közzétevői lehetőségek és a Microsoft kombinációja határozza meg. Válassza ki ezeket a kategóriákat megfelelően, hogy a felhasználók könnyen megtalálják az alkalmazásokat a megfelelő kategóriában. |
|  |  |  |


## <a name="other-tips"></a>Egyéb tippek

-   Keresés azt sugallja, hogy kap nehéz felhasználói tevékenység. Rangsorolja az alkalmazások nevével/közzétevőjrel szembeni egyezéseket. A rövid leírás lesz a kulcsmező, ha a keresési kifejezés nem pontosan egyezik a közzétevő/alkalmazás nevével.
-   A letölthető dokumentumok nem szerepelnek a keresési súlyozásban.
-   Az alkalmazások tényleges megszerzése és használata hatással lesz a keresési rangsorra is. Például két egyenértékű alkalmazás, ahol az egyik mérhetetlenül több felhasználó kap egy magasabb rangú.
