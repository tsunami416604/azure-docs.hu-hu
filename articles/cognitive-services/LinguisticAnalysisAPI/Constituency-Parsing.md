---
title: A nyelvi elemzés API elemzése választókerülete |} Microsoft Docs
description: További információk a hogyan elemzése, más néven "kifejezés struktúra elemzése," választókerülete azonosítja szöveg kifejezéseket.
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 03/21/2016
ms.author: lesun
ms.openlocfilehash: bff5e587621e1278c260d555aec280a0f4c7c8a1
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082172"
---
# <a name="constituency-parsing"></a>Választókerülete elemzése

(Más néven "kifejezés struktúra elemzése") elemzése választókerülete célja az a szöveg kifejezések azonosításához.
Ez akkor lehet hasznos, ha az információk beolvasása szövegből.
Az ügyfelek érdemes neve vagy egy nagy szervezet szöveg legfontosabb kifejezések keresésére, és a módosítók és minden ilyen kifejezés körülvevő műveletek megjelenítéséhez.

## <a name="phrases"></a>Kifejezések

Egy linguist, hogy egy *kifejezés* csupán szavak sorozata.
Ahhoz, hogy egy kifejezés, szavak csoportja számára, hogy egy adott szerepkör a mondatban együtt lesz rendelkezik.
Szó csoport is át együtt vagy egészét helyett, és a mondatok gördülékeny és nyelvtani maradjon.

Vegye figyelembe a mondatok

> Bluetooth-egy új hibrid automobile szeretnék.

Ezt a mondatot főnév kifejezést tartalmazza: "egy új hibrid autó Bluetooth-".
Hogyan azt, hogy, hogy ez a kifejezés?
Azt írni a mondatok (némileg poetically) az első teljes mondatot áthelyezésével:

> Egy új hibrid autó Bluetooth-szeretnék.

Vagy a Microsoft sikerült cserélje le a mondatot hasonló függvény és annak jelentése "egy divatos új autó" kifejezést:

> Egy divatos új autó szeretnék.

Ehelyett azt kivételezett szavak különböző részhalmazát, ha a csere feladatok ismeretlen vagy nem olvasható a mondatok vezetne.
Vegye figyelembe, hogy mi történik, ha azt "található egy új" elejéhez:

> Új hibrid autó Bluetooth-kívánt megkeresése

Az eredmények nagyon nehéz Elolvastam és megértettem.

Egy elemző célja az összes ilyen kifejezések keresését.
Interestingly természetes nyelven, a mondatok általában ágyazható be egy másik.
Ezek a kifejezések természetes ábrázolását egy fa, például az alábbiakat:

![Fa](./Images/tree.png)

A fán a fiókirodák telephelyeire "NP" jelölésű főnév kifejezések.
Nincsenek az számos ilyen kifejezések: *I*, *egy új hibrid automobile*, *Bluetooth*, és *egy új hibrid autó Bluetooth-*.

## <a name="phrase-types"></a>Kifejezés típusa

| Címke | Leírás | Példa |
|-------|-------------|---------|
|ADJP   | Adjective kifejezés | "így goromba" |
|ADVP   | Módosítószót kifejezés | "Törlés keresztül" |
|CONJP  | Együtt kifejezés | ", valamint" |
|ILLETHETI   | Töredék, hiányos vagy fragmentary bemenetek használt | "Erősen ajánlott..." |
|INTJ   | interjection | "Hooray" |
|LST    | Lista jelölő, beleértve az absztrakt | "#4)" |
|NAC    | Nem A bennük foglalt, azt jelzi, hogy nem alkotó kifejezés hatókörére |  "és a helyes üzlet" a ", a folyamat, és jó kezelése" |
|NP | Főnév kifejezés | "tasty burgonya pancake" |
|NX | A head megjelölni bizonyos összetett hálózati házirend-kiszolgáló belül használt| |
|PP | Prepositional kifejezés| "a"a készletben |
|PRN    | Zárójeles| "(úgynevezett)" |
|PRT    | alkotóelem| "out" a "bemásolt kimenő" |
|QP | Mennyiség kifejezésre (pl., összetett mérték vagy összeg) főnév kifejezés| "körül $75" |
|REGISZTRÁCIÓS KÓDJA    | Csökkentett relatív záradék.| "továbbra is feloldva" a "sok problémák még feloldatlan." |
|S  | Mondat vagy záradék. | "Ez az néhány mondatot."
|SBAR   | Alárendelt záradék, gyakran bevezetett subordinating együtt | "szerint balra" a "I kikeresi, I balra."|
|SBARQ  | Mit jelent-szó vagy - kifejezés által bevezetett közvetlen kérdés | "Mi történt a pont?" |
|SZOFTVERLELTÁR   | Fordított deklaratív mondat | "Soha nem azok tudomása volt." (vegye figyelembe, hogyan a normál tulajdonos "azok" helyezte át után a művelet "is") |
|SQ | Igen/nem fordított kérdést, vagy mi-kérdés fő záradékában | "Nem azok beszerezni a kocsit?" |
|UCP    | Eltérően koordinált kifejezés| "rövid és a hibák" (vegye figyelembe, hogyan vannak a egy melléknév és egy preposition kódot conjoined a "és")|
|VP | Művelet kifejezés | "hibába ütközött a woods" |
|WHADJP | Mit jelent-melléknév kifejezés | "hogyan kényelmetlen" |
|WHADVP | Mit jelent-módosítószót kifejezés| "Ha" |
|WHNP   | Mit jelent-főnév kifejezés| "mely burgonya", "mennyi leves"|
|WHPP   | Mit jelent prepositional kifejezés| "a"melyik ország|
|X  | Ismeretlen, bizonytalan vagy unbracketable.| első "a" a "a... a leves" |


## <a name="specification"></a>Meghatározása

Itt fák használja az S-kifejezéseknek a [Penn Treebank](https://catalog.ldc.upenn.edu/ldc99t42).
