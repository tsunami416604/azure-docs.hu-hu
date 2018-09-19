---
title: Elemző elnevezési rendszer – nyelvi elemzési API
titlesuffix: Azure Cognitive Services
description: Ismerje meg, miként teszi lehetővé a nyelvi elemzési API Analyzer elnevezési struktúráját a rugalmasság és a pontosság.
services: cognitive-services
author: RichardSunMS
manager: cgronlun
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: conceptual
ms.date: 03/23/2016
ms.author: lesun
ms.openlocfilehash: a998bdf32be948448131ea12db1b7d4204e6722d
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127465"
---
# <a name="analyzer-names"></a>Elemző nevek

Egy némileg összetettebb elnevezési struktúráját elemzők, hogy mindkét elemzők és a pontosság ismertetése, mi a név azt jelenti, hogy a nagyobb rugalmasság érdekében használjuk.
Elemző nevek négy részből áll: egy Azonosítót, egy objektumtípust, specifikáció és implementációja.
A szerepkör minden egyes összetevő van megadva.

## <a name="id"></a>ID (Azonosító)
Először egy analyzer rendelkezik-e; egyedi azonosító egy GUID Azonosítót.
Ezek GUID viszonylag ritkán kell módosítani, de az egyetlen módszer egy adott elemző eszköz egyedi ismertetik.

## <a name="kind"></a>Altípus
Ezután minden egyes analyzer van egy **kind**.
Ez rendkívül széleskörű használati elemzés típusát adja vissza, és egyedileg kell definiálhat, az adatok struktúrája képviseli az elemzés az határozza meg.
Jelenleg három különböző típusú:
 - [Jogkivonatok](Sentences-and-Tokens.md)
 - [POS-címkék](Pos-Tagging.md)
 - [Vevőkör-fa](constituency-parsing.md)

## <a name="specification"></a>Specifikáció
Belül egy adott típusú azonban különböző szakértők előfordulhat, hogy nem ért egyet a hogyan kell elemezni egy adott energiatermelés módjával.
Programozási nyelvek, ellentétben nem egyértelmű és pontos definíció, hogy ezt nem.

Például tegyük fel, hogy próbál keresse meg a jogkivonatok az angol nyelvű mondatban "Ő nem Ugrás."
Különösen érdemes "nem" karakterláncot.
Egy lehetséges értelmezése, hogy ezt el kell osztani két jogkivonatokba: "adott" és "not".
Ezután az alternatív mondat "ő nem tudta" kellene ugyanazokat a jogkivonatokat.
Egy másik lehetőség is, hogy azt kell bontani a jogkivonatok "adott" és "n't".
Az utóbbi jogkivonat lenne nem általában tekinthető szót, de ez a megközelítés megőrzi a surface karakterláncot, amely időnként hasznos lehet további információt.
Vagy esetleg a csökkenésére kell alkalmazni az egyetlen szó.

Függetlenül választott történik, hogy tetszőleges konzisztens módon kell tenni.
Ez pontosan az a szerepkör az olyan **specifikáció**: döntse el, mi a megfelelő ábrázolás kell lennie.

Elemző kimenetek csak viszonylag hasonlítható ugyanazon előírásoknak megfelelő adatokat.

## <a name="implementation"></a>Megvalósítás

Vannak gyakran több modellek, amelyek az azonos eredmények elérése érdekében, de eltérő teljesítményjellemzővel.
Egy modell gyorsabb lehet, ha kevésbé pontos; egy másik kompromisszum egy másik teszi.

A **megvalósítási** egy elemző neve része azonosítására szolgál az ilyen információkat, úgy, hogy a felhasználók a saját igényeiknek leginkább megfelelő analyzer folytathatja a munkát.
