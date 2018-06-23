---
title: Elnevezési rendszer a nyelvi elemzés API Analyzer |} Microsoft Docs
description: Ismerje meg, hogyan a nyelvi elemzés API használja ahhoz, hogy mind a rugalmasságot, és a pontosság elemzőkkel az elnevezési struktúrája.
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 03/23/2016
ms.author: lesun
ms.openlocfilehash: 2729b7126e82862660fc8e1a995cc87ae996ea03
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346998"
---
# <a name="analyzer-names"></a>Elemző nevét

Egy némileg bonyolult lekérdezések elnevezési szerkezet segítségével mindkét rugalmasságot elemzőkkel és a pontosság megválaszolásával nevét jelenti.
Analyzer nevek négy részből áll: egy Azonosítót, jellegű, egy specifikációja és megvalósítását.
Minden egyes összetevő szerepe van megadva.

## <a name="id"></a>ID (Azonosító)
Először egy analyzer rendelkezik-e az egyedi Azonosítót; egy GUID Azonosítót.
A GUID viszonylag ritkán kell módosítani, de a segítségükkel egyedileg az adott analyzer leírására.

## <a name="kind"></a>Altípus
Ezt követően mindegyik elemző van egy **jellegű**.
Ez határozza meg, a vizsgálati típus adott vissza, és egyedileg kell definiálhat, jelenítik meg, hogy elemzés adatszerkezet tág feltételeknek.
Jelenleg nincsenek három különböző típusú:
 - [Jogkivonatok](Sentences-and-Tokens.md)
 - [POS címkék](Pos-Tagging.md)
 - [Választókerülete fa](constituency-parsing.md)

## <a name="specification"></a>Meghatározása
Egy adott típusú belül azonban különböző szakértők előfordulhat, hogy nem ért meg, hogyan kell elemezni egy adott jelenség.
Eltérően programozási nyelven hogyan ezt a nem egyértelmű és pontos definíciója van.

Például képzelhető el, azt próbál található a jogkivonatok az angol mondat "Ő nem nyissa meg."
Különösen érdemes lehet a karakterlánc "nem".
Egy lehetséges értelmezése, hogy ezt el kell osztani két jogkivonatokba: "adta" és "nincs".
Ezután az alternatív mondat "ő nem haladt" kellene ugyanazokat a jogkivonatokat.
Egy másik lehetőség, azaz, hogy azt kell bontani a tokenek "adta" és "n't".
Az utóbbi jogkivonat nem általában tekint szót, de ez a megközelítés őrzi meg további információt a felület karakterlánc, egyes esetekben lehet hasznos.
Vagy előfordulhat, hogy csökkenésére érdemes figyelembe venni egyetlen szó.

Attól függetlenül történik választott történik, hogy tetszőleges következetesen kell tenni.
Pontosan a szerepkör egy **specification**: döntse el, hogy mi a helyes megjelenítése kell lennie.

Analyzer kimenetek csak viszonylag hasonlítható ugyanazon előírásoknak megfelelő adatokat.

## <a name="implementation"></a>Megvalósítás

Gyakran nincsenek több modellek, amelyek az azonos eredmények elérése érdekében, de más-más teljesítménybeli jellemzőit.
Lehet, hogy egy modell gyorsabb azonban kevésbé pontos; egy másik kompromisszum tehetik egy másikra.

A **megvalósítási** része egy elemző nevét alapján határozza meg az ilyen információkat, úgy, hogy a felhasználók ki tudja választani a igényeiknek leginkább megfelelő elemzőt.
