---
title: Vevőkör-elemzés – nyelvi elemzési API
titlesuffix: Azure Cognitive Services
description: Ismerje meg hogyan vevőkör-elemzés, más néven "kifejezést struktúra elemzése," azonosítja a mondatok szövegben.
services: cognitive-services
author: RichardSunMS
manager: cgronlun
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: conceptual
ms.date: 03/21/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 89832f2d936a08df8b6f9e846c3dd4a5665c06a4
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238624"
---
# <a name="constituency-parsing"></a>Vevőkör-elemzés

> [!IMPORTANT]
> A nyelvi elemzés előzetes verziója 2018. augusztus 9 volt leszerelése. Azt javasoljuk, [Azure Machine Learning szövegelemzési moduljait](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) szöveg feldolgozásra és elemzésre.

Vevőkör-elemzés (más néven "kifejezés helyett szerepel struktúra elemzése") az a célja, hogy az kifejezések a szövegben azonosíthatja.
Ez akkor hasznos, ha az adatok kivonása szövegből.
Az ügyfeleknek érdemes a szolgáltatás nevét vagy a kulcsfontosságú kifejezéseket, így a big Data típusú szövegtörzs, és meg szeretné megtekinteni a dostupnosti és műveletek körüli minden ilyen kifejezés helyett szerepel.

## <a name="phrases"></a>Kifejezések

Az egy linguist egy *kifejezés* szavak több, mint egy sorozata.
Ahhoz, hogy egy kifejezés, a szavakat csoport rendelkezik, az adott szerepet játszanak a mondat jár együtt.
Adott szósorozat csoport együtt áthelyezni, vagy teljes cseréje, és a mondat kell maradnia, gördülékeny és nyelvtani.

Vegye figyelembe a mondat

> Szeretnék egy új hibrid automobile Bluetooth találja.

Ezt a mondatot főnév kifejezést tartalmazza: "egy új hibrid autó Bluetooth".
Hogyan értesíti, hogy ez a kifejezés?
Azt is újraírási a mondat (némileg poetically) helyezi át az előtérben egész a mondatot:

> Egy új hibrid automobile szeretnék kevesebbet Bluetooth.

Vagy hasonló funkciót, és ez azt jelenti, például a "egy divatos új autó" kifejezés azt sikerült lecserélheti erre a kifejezésre:

> Szeretnék egy divatos új autó talál.

Ha ehelyett azt kivételezett szó különböző részhalmazát, csere feladatok ismeretlen vagy nem olvasható mondatokat vezetne.
Vegye figyelembe, hogy mi történik, ha a "található új" az előtérben mozgatjuk:

> Keresse meg egy új hibrid automobile Bluetooth szeretnék.

Az eredmények nagyon nehezen olvashatók és érthetők.

Egy elemző célja az összes ilyen kifejezések keresésére.
Interestingly természetes nyelven a mondatok általában egy másik ágyazhatók egymásba.
A következő kifejezésekkel ellátva természetes ábrázolása egy fa, például a következő:

![Fa](./Images/tree.png)

Ehhez a fához megjelölt "NP" az ágak főnév kifejezések.
Nincsenek az számos ilyen kifejezések: *I*, *egy új hibrid automobile*, *Bluetooth*, és *egy új hibrid automobile Bluetooth*.

## <a name="phrase-types"></a>A kifejezés típusa

| Címke | Leírás | Példa |
|-------|-------------|---------|
|ADJP   | Adjective kifejezés | "ezért goromba" |
|ADVP   | Módosítószót kifejezés | "Törlés keresztül" |
|CONJP  | Együtt kifejezés | ", valamint" |
|ILLETHETI   | Fragment, hiányos vagy fragmentary bemenet használt | "Erősen ajánlott..." |
|INTJ   | interjection | "Hurrá" |
|LST    | Lista jelölő, beleértve az absztrakt | "#4)" |
|NAC    | Nem A megvalósítani a bennük foglalt, azt jelzi, hogy nem alkotóelem kifejezés hatókörkezelése |  "és a egy jó üzlet" a "olvashatók be dolgokat és a egy jó kezelése" |
|NP | Főnév kifejezés | "tasty potato pancake" |
|NX | A fő meg bizonyos összetett hálózati házirend-kiszolgáló belül használt| |
|PORTJÁNAK PROFILTULAJDONSÁGAI | Prepositional kifejezés| "az a készlet" |
|PRN    | Zárójeles| "(úgynevezett)" |
|PRT    | particle| "lejárt" a "bemásolt ki" |
|QP | Quantity kifejezés (azaz a összetett mérték vagy összeg) belül főnév kifejezés| "körül 75 $" |
|REGISZTRÁCIÓS KÓDJA    | Csökkentett relatív záradék.| "még feloldatlan" a "számos probléma még feloldatlan." |
|S  | Mondat vagy záradékban. | "Ez a mondatok."
|SBAR   | Alárendelt záradék, gyakran bevezetett egy subordinating együttes használata | "az I balra" az "I kikeresi, e left."|
|SBARQ  | Mit jelent-szó vagy - kifejezés által bevezetett közvetlen kérdés | "Mi volt a pont?" |
|SZOFTVERLELTÁR   | Fordított deklaratív mondat | "Nem akcióról azokat." (vegye figyelembe, hogyan normál tárgyát "," került után a művelet "is") |
|SQ | Igen/nem fordított kérdést, vagy a main záradékában kérdőszavak-kérdés | "Fejeződött kapnak az autó?" |
|UCP    | Ellentétben koordinált kifejezés| "rövid és a hibák" (vegye figyelembe, hogyan vannak a egy melléknév és a egy preposition kifejezés conjoined együtt "és")|
|ALELNÖK | Művelet kifejezés | "hibába ütközött az erdők" |
|WHADJP | Mit jelent-melléknév kifejezés | "hogyan kényelmetlen" |
|WHADVP | Mit jelent-módosítószót kifejezés| "when" |
|WHNP   | Mit jelent-főnév kifejezés| "melyik burgonya", "mennyi leves"|
|WHPP   | Mit jelent prepositional kifejezés| "a melyik ország"|
|X  | Ismeretlen, bizonytalan vagy unbracketable.| első "a" a "a... a leves" |


## <a name="specification"></a>Specifikáció

Itt fák az S-kifejezések használata a [Penn Treebank](https://catalog.ldc.upenn.edu/ldc99t42).
