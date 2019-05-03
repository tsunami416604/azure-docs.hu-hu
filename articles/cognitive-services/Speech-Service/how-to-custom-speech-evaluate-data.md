---
title: Egyéni beszédfelismerés – beszédszolgáltatások pontossága kiértékeléséhez
titlesuffix: Azure Cognitive Services
description: Ez a dokumentum lesz mutatja kvantitatív mérhető a Microsoft hang-szöveg transzformációs modellt vagy az egyéni modell minőségét. Hang + emberi címkével beszédátírási adatok pontossága teszteléséhez szükséges, és 30 percen át 5 óra reprezentatív hanganyagra meg kell adni.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 7c1b3602b09c1a129923180c4b1d4a5f8293de2c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025909"
---
# <a name="evaluate-custom-speech-accuracy"></a>Egyéni beszédfelismerés pontossága kiértékelése

Ebben a dokumentumban, fog megtudhatja, hogyan kvantitatív mérhető a Microsoft hang-szöveg transzformációs modellt vagy az egyéni modell minőségét. Hang + emberi címkével beszédátírási adatok pontossága teszteléséhez szükséges, és 30 percen át 5 óra reprezentatív hanganyagra meg kell adni.

## <a name="what-is-word-error-rate-wer"></a>Mi az a Word hiba sebessége (WER)?

Az iparági szabvány modellpontosságból mérésére *Word Hibaarány* (WER). A WER megszámolja a helytelen szavak felismerése során meghatározott, majd szó az emberi címkével a szövegben ismertetett teljes száma alapján osztja fel. Végül, hogy a rendszer megszorozza 100 %-os kiszámításához a WER programban.

![A WER képlet](./media/custom-speech/custom-speech-wer-formula.png)

Helytelenül azonosított szavak fall három kategóriába sorolhatók:

* Az beszúrási (I): Az elmélet átiratot az helytelenül hozzáadott szavakat
* Törlés (D): A rendszer nem észleli az elmélet átiratot a szavakat
* Helyettesítés (S): Referencia- és elmélet közötti helyettesített szavakat

Például:

![Helytelenül azonosított szavak – példa](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>Ki a hibákat, és javíthatja a WER használatára

Az a gép felismerési eredményeket a WER segítségével kiértékelheti a modellt használ az alkalmazás, az eszköz vagy a termék minőségének. A WER 5 – 10 %-os jó minőségű tekinthető, és készen áll a használatra. A WER 20 %-os elfogadható,, de érdemes figyelembe venni a további. A WER legalább 30 %-os gyenge minőség a jeleket, és testreszabási és képzési igényel.

Fontos, hogyan oszlanak meg a hibákat. Számos törlési hiba történik, esetén általában a gyenge hang jel erőssége miatt. A probléma megoldásához kell közelebb hangadatokat gyűjthet a forrás. Beszúrási hiba jelenti azt, hogy a hanganyag lett rögzítve zajos környezetben, és belevág jelen lehet okozó problémák felismerése. Helyettesítés gyakran hiba történik egy megfelelő minta a tartomány-specifikus feltételek szerint vagy emberi címkével beszédátírás megadott vagy kapcsolatos szöveget.

Egyes fájlok adatainak elemzésével, megadhatja, hogy milyen típusú hibákat létezik, és mely hibák egyediek-e egy bizonyos fájlhoz. A fájlok szintjén ismertetése problémákat segít javítását célozza.

## <a name="create-a-test"></a>Hozzon létre egy tesztet

Ha szeretné a Microsoft baseline hang-szöveg transzformációs modell vagy egy egyéni modell számára betanított minőségének ellenőrzéséhez két modell pontossága kiértékelheti, hogy össze lehessen hasonlítani. Az összehasonlítás WER és felismerési eredményeket is tartalmaz. Általában egy egyéni modell a rendszer összehasonlítja a Microsoft baseline modellel.

Modelleket szeretne értékelni egymás mellett:

1. Navigáljon a **hang-szöveg transzformációs > Custom Speech > tesztelés**.
2. Kattintson a **teszt hozzáadása**.
3. Válassza ki **pontossága kiértékelése**. Adja meg a teszt nevét, leírását, és válassza ki az audio- és emberi címkével beszédátírási adatkészlet.
4. Válassza ki a tesztelni kívánt legfeljebb két modellek.
5. Kattintson a **Create** (Létrehozás) gombra.

A teszt sikeres létrehozása után összehasonlíthatja az eredményeket egymás mellett.

## <a name="side-by-side-comparison"></a>Egymás melletti összehasonlítása

A teszt befejeződése után az állapot módosítása által jelzett *sikeres*, megtalálhatja a WER szám mind a két modellben szereplő a tesztet. Kattintson a teszt neve, a tesztelési részletei lap megtekintéséhez. A Részletek lap felsorolja az adatkészlet, a két modell mellett az elküldött adatkészletből a beszédátírási felismerés eredményét jelző kimondott szöveg. Az egymás melletti összehasonlítás vizsgálata érdekében különböző alkalmazáshiba-típusok, beleértve a beszúrási, törlési és helyettesítési válthat. A hanganyag figyeli, és az egyes oszlopokban, amely megjeleníti az emberi címkével beszédátírási felismerési eredményeket és az eredményeket a hang-szöveg transzformációs két modell összehasonlítása, eldöntheti, melyik modellben az igényeinek, és amelyeknél további és fejlesztései szükséges.

## <a name="next-steps"></a>További lépések

* [A modell tanítása](how-to-custom-speech-train-model.md)
* [A modell üzembe helyezése](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>További források

* [Készítse elő és az adatok tesztelése](how-to-custom-speech-test-data.md)
* [Az adatok vizsgálata](how-to-custom-speech-inspect-data.md)
