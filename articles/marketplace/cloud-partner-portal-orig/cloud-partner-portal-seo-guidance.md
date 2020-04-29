---
title: Az Azure Marketplace SEO-útmutatója
description: Útmutatást nyújt a keresőmotor-optimalizálás maximalizálásához (SEO).
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: dsindona
ms.openlocfilehash: 761cdc2233bce3619d4c2c9ce1d7d7177d3bc407
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280150"
---
# <a name="azure-marketplace-seo-guidance"></a>Az Azure Marketplace SEO-útmutatója

Ez a cikk azt ismerteti, hogyan maximalizálható az ajánlata az [Azure Marketplace](https://azuremarketplace.microsoft.com) -en és a [AppSource](https://appsource.microsoft.com)található keresési funkciókon keresztül. 


## <a name="general-explanation-of-algorithm"></a>Az algoritmus általános magyarázata

A Microsoft piactérek az Azure Cognitive Searcht használják a hely keresési képességeinek bekapcsolására. Az algoritmus a kifejezés gyakorisága – inverz dokumentum gyakorisága ([TF-IDF](https://en.wikipedia.org/wiki/Tf–idf)) alapján történik. A standard [Lucene Analyzer](https://lucene.apache.org/core/) használatos.

Általánosságban elmondható, hogy minden szövegmezőt, kategóriát és iparágat tartalmaz, és tartalmazza a relevancia súlyozását. Azok a speciális kifejezések, amelyeket ritkán használnak az alkalmazások, de gyakran az alkalmazásban magasabb pontszámot eredményeznek a kereséssel. A "virtuális gép" kifejezéseket is beleértve, például az "Azure Search" sokkal fejlettebb lenne.
Az alábbiakban a legfontosabb mezőket érdemes figyelembe venni.

 
|  Mező                   | Fontosság | Útmutatás                                                                                            |
|  --------------------    | ----------                   | ---------------                                                                   |
| Offer Name               |  Magasság      | A keresési lekérdezés teljes egyezésének pontos vagy záró értéke magas prioritást eredményez.                       |
| Publisher Name           |  Magasság      | A keresési lekérdezés teljes egyezésének pontos vagy záró értéke magas prioritást eredményez.                       |
| Rövid leírás        |  Közepes    | Az alkalmazások és a közzétevők neveinek adott elnevezése szinte magas rangot garantál, nem lehet a legfontosabb. Ebben az esetben a rövid leírás kritikus fontosságú. A szöveg tömör és lényegretörő maradjon. A legjobb eredménynek a kulcsszavakat és a várt keresési kifejezéseket kell tartalmaznia.  Például: "Ez a Dynamics 365-re épülő legjobb kiskereskedelmi POS, amely kevésbé hatékony, mint a Dynamics 365-hez készült" kiskereskedelmi POS (értékesítési pont) ".  | 
| Hosszú leírás         |  Alacsony       | A leírással részletesebben is eljuthat. A leghatékonyabb leírások ésszerű hosszúságú, és a kulcsszavak is érvényesek.  A kulcsszavakat használó a to-the-Point leírások hosszabb és hosszadalmas szöveg esetén is hasznosak lehetnek. Győződjön meg arról, hogy a feltételek (például "IoT") szerepelnek a leírásban.  |
| Termékkategóriák       | Közepes     |  A termékkategóriák a kiadói döntések és a Microsoft kombinációja alapján vannak meghatározva. Válassza ki ezeket a kategóriákat, hogy a felhasználók könnyedén megtalálják a megfelelő kategóriába tartozó alkalmazásokat. |
|  |  |  |


## <a name="other-tips"></a>További tippek

-   A keresés nagy mennyiségű felhasználói tevékenységet javasol. Rangsorolja az alkalmazás neve/közzétevője egyezéseit. A rövid leírás lesz a kulcsmező, ha a keresési kifejezés nem pontosan egyezik a közzétevő/alkalmazás nevével.
-   A letölthető dokumentumok nem szerepelnek a keresési súlyozásban.
-   Az alkalmazások tényleges beszerzése és használata hatással lesz a keresési rangsorolásra is. Például két egyenértékű alkalmazás, amelyben az egyik jelentősen több felhasználóval rendelkezik, magasabb rangot kap.
