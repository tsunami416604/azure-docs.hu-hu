---
title: Az Azure Marketplace-en keresőmotor-Optimalizálást közzétételi útmutatóban |} A Microsoft Docs
description: Keresés keresőmotor-optimalizálás (SEO) jelentős nyújt útmutatást.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: cacc7c0b269e8006903961049caf3cd7e3bee449
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57834336"
---
<a name="azure-marketplace-seo-publisher-guide"></a>Az Azure Marketplace-en keresőmotor-Optimalizálást útmutató Közzétevőknek
=======================================

### <a name="general-explanation-of-algorithm"></a>Algoritmus általános magyarázata

A Marketplace-en az Azure Search szolgáltatást a webhely keresési képességekkel működtetésére használja. Az algoritmus alapul kifejezés gyakorisága – inverz dokumentum gyakorisága ([TF-IDF](https://en.wikipedia.org/wiki/Tf–idf)). A standard [Lucene-elemzőt](https://lucene.apache.org/core/) szolgál.

Általában a teljes szöveg mezők, kategóriák és iparágak és bekerült a weightage a relevancia. Speciális feltételeket, a ritkán használt alkalmazások azonban gyakran az alkalmazásban való kereséssel egyezés magasabb pontszámot hoz létre. Így többek között például a "Virtuális gép" használati kis benefit járna, mivel a "Az Azure search" speciális sokkal lenne.
Az alábbiakban a leginkább megfelelő mezőket kell figyelembe venni.

 
|  Mező                   | Fontosság | Útmutatás                                                                                            |
|  --------------------    | ----------                   | ---------------                                                                   |
| Csomag neve               |  Magas      | A pontos vagy közel teljes egyezését a keresési lekérdezés magas prioritás előállításához.                       |
| Közzétevő neve           |  Magas      | A pontos vagy közel teljes egyezését a keresési lekérdezés magas prioritás előállításához.                       |
| Rövid leírás        |  Közepes    | Az alkalmazások és a közzétevő elnevezési megadott nevek szinte garantálja a nagy rangsoroló, nem lehet a dolgokat. Ebben az esetben egy rövid leírást, kritikus fontosságú. Tartsa meg a szöveg, rövid és lényegre törő. Kulcsszavak és várt keresési kifejezéseket is tartalmaznia kell a legjobb eredmény.  Kevésbé hatékony, mint például "Ez az a legjobb kiskereskedelmi POS-ra épülő teljes Dynamics 365" a "kiskereskedelmi POS (értékesítési pont), a Dynamics 365".  | 
| Hosszú leírás         |  Alacsony       | Leírás egy megoldást találhat javaslatokat kínál. A leghatékonyabb leírások ésszerű származnak, és a kulcsszavak szolgálnak.  Egy-az-ponthoz leírások kulcsszavakat használó több mint hosszú hosszú szöveges előnyeit. Győződjön meg arról, hogy a legfontosabb kifejezések, például az "IoT", a leírásban találhatók.  |
| Termékkategória       | Közepes     |  Termékkategóriák közzétevő lehetőségeket és a Microsoft kombinációja határozza meg. Válassza ki ezen kategóriák megfelelően, hogy a felhasználók könnyen megtalálhatják az alkalmazások a megfelelő kategóriában. |
|  |  |  |


### <a name="other-tips"></a>További tippek

-   Keresés beolvasása (nagy erőforrásigényű) felhasználói tevékenység javasol. Alkalmazás név vagy közzétevő egyezések értékelésénél azokat. Rövid leírás esetében a Ha a keresett kifejezés nem egyezik pontosan azzal a közzétevő neve/kulcsmező válik.
-   Letölthető dokumentumok keresési weightage nem szerepelnek.
-   Az alkalmazások tényleges beszerzési és használatát keresési rangsorolás is befolyásolja. Például két azonos alkalmazásokat, ahol technológiáikkal nagymértékben több felhasználó rendelkezik-e egy magasabb prioritást kap.
