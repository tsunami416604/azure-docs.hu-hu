---
title: A mondatok és a tokeneket a nyelvi elemzés API |} Microsoft Docs
description: Ismerje meg a mondatok elkülönítés és a nyelvi elemzés API kognitív szolgáltatásokban lexikális elemekké alakításának.
services: cognitive-services
author: DavidLiCIG
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 03/21/2016
ms.author: davl
ms.openlocfilehash: 4681098a0e56640e95463272be44f7432be26839
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347046"
---
# <a name="sentence-separation-and-tokenization"></a>Mondat elkülönítését és létrehozása

## <a name="background-and-motivation"></a>Háttér és kifejlesztésének

Szövegtörzs megadott, az első lépés a nyelvi elemzési felosztása mondatokat és jogkivonatokat.

### <a name="sentence-separation"></a>Mondat elkülönítése

Az első áttekintő, úgy tűnik, hogy a szöveg ossza mondat felettébb egyszerű: csak a végfelhasználók a mondatok jelölőket található, és nincs mondat törés.
Azonban ezek közé a következők gyakran bonyolult és nem egyértelmű.

Vegye figyelembe a következő példa szöveget:

> mit mondtál?!? I nem érkezett válasz az igazgató "új javaslat." Fontos, és a John Smith Patricia.

Ez a szöveg három tartalmazza:

- mit mondtál?!?
- I nem érkezett válasz az igazgató "új javaslat."
- Fontos, és a John Smith Patricia.

Vegye figyelembe, hogyan mondat vége nagyon különböző módon megjelölve.
Az első fejeződik be egy kérdőjel és felkiáltójel (más néven egy interrobang) kombinációja.
A második végein ponttal vagy teljes stop, de a következő idézőjel kell kell húzni azokat a korábbi mondat helyett szerepel.
A harmadik mondat láthatja, hogyan, hogy adott idő alatt karakter való megjelöléséhez rövidítések is használható.
Csak az absztrakt megnézi biztosít, érdemes kijelölni, de további munkára igaz mondat határokon azonosításához szükséges.

### <a name="tokenization"></a>Jogkivonat-létrehozási

A következő feladata a mondatok felosztása jogkivonatokat.
Az esetek többségében angol nyelvű jogkivonatok szóköz határolja.
(Jogkivonatok vagy szavak keresése egy sokkal egyszerűbb, mint a kínai, angol nyelven Ha szóköz legtöbbször nem használnak szavakat közötti.
Az első mondat írható előfordulhat, hogy a "Whatdidyousay?")

Nincsenek néhány bonyolult eseteket.
Első lépésként írásjelek gyakran (de nem mindig) kell osztható el azt körülvevő környezetben.
Második, angol nyelven van *összevonásokat*, például a "nem" vagy "", ahol szavak tömörített és kisebb darabokra rövidítése. A jogkivonatokat létrehozó célja a karaktersorozat felosztása szavakat.

Most, térjen vissza a a fenti példa mondatokat.
Most azt helyeztük "center pont" (&middot;) a különböző tokenek között.

- Mi &middot; volt &middot; meg &middot; tegyük fel például &middot; ?!?
- I &middot; volt &middot; n't &middot; hall &middot; kapcsolatos &middot; a &middot; igazgató &middot; tartozó &middot; " &middot; új &middot; javaslat &middot; . &middot; "
- Az &middot; tartozó &middot; fontos &middot; való &middot; John &middot; és &middot; asszony. &middot; Smith &middot; .

Vegye figyelembe, hogyan legtöbb jogkivonatok szavak a szótárban találjuk (pl. *fontos*, *igazgató*).
Mások kizárólag írásjelek állnak.
Végezetül nincsenek képviselő összevonásokat például több szokatlan jogkivonatok *n't* a *nem*, possessives, például *tartozó*stb. A lexikális elemekké alakításának lehetővé teszi a word kezelni *nem* és a kódot *nem* egységesebb módon példányhoz.

## <a name="specification"></a>Meghatározása

Fontos konzisztens döntenie néhány mondatot, és egy jogkivonat foglalja magában.
Azt a meghatározás támaszkodjon a [Penn Treebank](https://www.cis.upenn.edu/~treebank/) (néhány további részletek itt érhetők el: [https://www.cis.upenn.edu/~treebank/tokenization.html]).
