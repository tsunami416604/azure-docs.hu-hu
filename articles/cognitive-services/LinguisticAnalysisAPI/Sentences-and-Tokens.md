---
title: Mondatok és lexikális – nyelvi elemzési API
titlesuffix: Azure Cognitive Services
description: Ismerje meg a mondatok szétválasztása és lexikális elemzése, a nyelvi elemzési API-ban.
services: cognitive-services
author: DavidLiCIG
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 03/21/2016
ms.author: davl
ROBOTS: NOINDEX
ms.openlocfilehash: 435513023cf74bbc259cb922220d5f9940452d79
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55879394"
---
# <a name="sentence-separation-and-tokenization"></a>Mondatok szétválasztása és lexikális elemzése

> [!IMPORTANT]
> A Linguistic Analysis előzetes verzióját 2018. augusztus 9-én visszavontuk. A szövegek feldolgozásához és elemzéséhet az [Azure Machine Learning szövegelemzési moduljait](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) javasoljuk használni.

## <a name="background-and-motivation"></a>Háttér-információkért és motiváció

Adott szövegtörzs, nyelvi elemzés első lépése az felosztása mondatok és lexikális jelek.

### <a name="sentence-separation"></a>Mondatok szétválasztása

Az első pillantásra, úgy tűnik, hogy a szöveg ossza mondatokat használata egyszerű: mindössze keresse meg a végfelhasználók a mondat, jelölők és az ott mondatokat.
Azonban ezek a jelek olyan gyakran bonyolult, és nem egyértelmű.

Vegye figyelembe a következő példa szöveget:

> mit mondtál?!? Nem hallható információ a igazgatója "új javaslat." Mr. és Patricia Smith fontos.

Ez a szöveg három mondatokat tartalmazza:

- mit mondtál?!?
- Nem hallható információ a igazgatója "új javaslat."
- Mr. és Patricia Smith fontos.

Vegye figyelembe, hogy hogyan mondatokat ends különböző módokon vannak megjelölve.
Az első kérdőjelek és felkiáltójel (más néven egy interrobang) karakterrel végződik.
Az előzetes mondat, le kell kérnie a második véget ér, ponttal vagy teljes leállítása, de a következő idézőjelet.
A harmadik mondat láthatja, hogyan, hogy adott időszak karakter való megjelöléséhez rövidítések is használható.
Csak az absztrakt Hibaoldal kínál a jó jelöltnek számít, de további munka szükséges igaz mondat határainak azonosítása.

### <a name="tokenization"></a>A jogkivonatok

A következő feladata a mondatok felosztása jogkivonatokat.
Angol nyelvű jogkivonatok többségében, térközt vannak elválasztva.
(Jogkivonatok vagy szavak keresése az sokkal egyszerűbb, mint a kínai, angol nyelvű Ha tárolóhelyek többnyire nem használnak szavak között.
Az első mondat írható előfordulhat, hogy a "Whatdidyousay?")

Nincsenek néhány bonyolult esetek.
Első lépésként írásjelek gyakran (de nem mindig) kell környezet körülvevő dobozból osztható.
A második, angol nyelven van *összevonásokat*, mint például a "nem" vagy "", ahol szavak tömörített és kisebb darabokra rövidítése.
A jogkivonatokat létrehozó az a célja, hogy a karaktersorozat felosztása szavakat.

Nézzük térjen vissza a ismét a fenti példa mondatokat.
Most hogy elhelyezte a "center pont" (&middot;) minden egyes elkülönült jogkivonat között.

- Mi &middot; fejeződött &middot; , &middot; tegyük fel, hogy &middot; ?!?
- E &middot; fejeződött &middot; n't &middot; hallani &middot; kapcsolatos &middot; a &middot; igazgató &middot; a &middot; " &middot; új &middot; javaslat &middot; . &middot; "
- Ez &middot; a &middot; fontos &middot; való &middot; Mr. &middot; és &middot; asszony. &middot; Smith &middot; .

Vegye figyelembe, hogyan legtöbb jogkivonatok olyan szavakat a szótár találjuk (például *fontos*, *igazgató*).
Mások kizárólag írásjelek állnak.
Végezetül vannak további szokatlan jogkivonatok, amelyek például összevonásokat *n't* a *nem*, és possessives *a*.
Ez a jogkivonatok lehetővé teszi számunkra az kezelni a word *nem* és a kifejezést *nem* több konzisztens módon.

## <a name="specification"></a>Specifikáció

Fontos konzisztens döntéseket mondatok és a egy token foglalja magában.
Hogy támaszkodnak az specifikációt a a [Penn Treebank](https://catalog.ldc.upenn.edu/LDC99T42) (További részletekért ftp://ftp.cis.upenn.edu/pub/treebank/public_html/tokenization.html érhető el).
