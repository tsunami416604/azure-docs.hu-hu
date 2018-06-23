---
title: Az Azure Content moderátor - lemezkép moderálás |} Microsoft Docs
description: Nem megfelelő képek mérsékelt kép moderálás használata
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/20/2018
ms.author: sajagtap
ms.openlocfilehash: c7cbc343c6e9113642d0ac79f4a4d60a404e8171
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349335"
---
# <a name="image-moderation"></a>Képmoderálás

Használja a tartalom moderátor gép támogatású kép moderálás és [emberi felülvizsgálati eszköz](Review-Tool-User-Guide/human-in-the-loop.md) képek ellopható és felnőtt tartalom mérsékelt. Szöveges tartalom képek vizsgálata, hogy a szöveg és felületei észleléséhez. Felel meg a lemezképek egyéni listájával, és további műveleteket végrehajtania.

## <a name="evaluating-for-adult-and-racy-content"></a>Felnőtt és ellopható tartalom értékelése

A **Evaluate** művelet 0 és 1 közötti megbízhatósági pontszámot adja vissza. Azt is vissza logikai adatok igaz vagy hamis. Ezekkel az értékekkel előre jelezni, hogy a rendszerkép tartalmazza-e a potenciális felnőtt vagy ellopható tartalmat. Az API-t hívja a lemezképpel (a fájl vagy URL-cím), a visszaadott válasz tartalmazza a következő információkat:

    "ImageModeration": {
      .............
      "adultClassificationScore": 0.019196987152099609,
      "isImageAdultClassified": false,
      "racyClassificationScore": 0.032390203326940536,
      "isImageRacyClassified": false,
      ............
      ],

> [!NOTE]

> - `isImageAdultClassified` olyan lemezképkészlet, amellyel ivarilag explicit vagy bizonyos esetekben felnőtt tekinthetők lehetséges meglétének jelöli.
> - `isImageRacyClassified` olyan lemezképkészlet, amellyel ivarilag kétértelmű vagy bizonyos esetekben érett tekinthetők lehetséges meglétének jelöli.
> - A pontszámok 0 és 1 között. Minél nagyobb a pontszám, annál magasabb a modell becslése, hogy a kategória alkalmazhatók. Ez az előnézet manuálisan kódolt eredményekkel helyett statisztikai modell támaszkodik. Azt javasoljuk, hogy a saját tartalom határozza meg, hogyan legyen a különböző kategóriájú az igényeinek megfelelően tesztelték.
> - A logikai értékek a következők: IGAZ vagy hamis függően a belső pontszám küszöbértékeket. Az ügyfelek fel kell mérnie hogy ezt az értéket, vagy adja meg a tartalom házirendek alapján egyéni küszöbértékeket.
>

## <a name="detecting-text-with-optical-character-recognition-ocr"></a>Szöveg rendelkező optikai (OCR) észlelése

A **optikai (OCR)** művelet előrejelzi a szöveges tartalom a kép jelenlétét, és kibontja az a szöveg moderálás, egyebek mellett. Megadhatja a nyelvet. Ha nem adja meg a nyelvet, az észlelési alapértelmezés szerint angol.

A válasz a következő információkat tartalmazza:
- Az eredeti szöveg.
- Az észlelt szövegelemek az vetett bizalmat eredményeiket.

Példa kivonat:

    "TextDetection": {
      "status": {
        "code": 3000.0,
        "description": "OK",
        "exception": null
      },
      .........
      "language": "eng",
      "text": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
      "candidates": []
    },


## <a name="detecting-faces"></a>Lapok észlelése

Lapok észlelése segítségével azonosíthatók a lapok például személyes azonosításra alkalmas adatokat (PII) a lemezképeket. Észlelés lehetséges oldalak és a potenciális lapok száma minden kép.

A válasz ezeket az információkat tartalmazza:

- Lapok száma
- Az észlelt lapok helyek listája

Példa kivonat:


    "FaceDetection": {
       ......
      "result": true,
      "count": 2,
      "advancedInfo": [
      .....
      ],
      "faces": [
        {
          "bottom": 598,
          "left": 44,
          "right": 268,
          "top": 374
        },
        {
          "bottom": 620,
          "left": 308,
          "right": 532,
          "top": 396
        }
      ]
    }

## <a name="creating-and-managing-custom-lists"></a>Egyéni listák elkészítése és kezelése

Számos online Közösség Miután a felhasználók képeket vagy egyéb típusú tartalmak feltöltéséhez sértő elemek előfordulhat, hogy beolvasása többször keresztül megosztott a következő nap, hét és hónap. Ismételten vizsgálatát, és a megegyező vagy a kép több helyen akár csak kis mértékben módosított verziói kiszűrése járó költségek lehet költséges és hibákhoz vezethet.

Helyett moderálás ugyanazt a képet többször, akkor vegye fel a sértő képek a egyéni letiltott tartalom. Ily módon a tartalom moderálás rendszer összehasonlítja a egyéni listájával bejövő lemezképeket, és leállítja a további feldolgozás.

> [!NOTE]
> Nincs a jelenlegi maximális műveletszámot **5 kép listák** minden listájával, hogy **legfeljebb 10 000 képek**.
>

A tartalom moderátor biztosít teljes [kép lista felügyeleti API](try-image-list-api.md) műveletek listájának egyéni lemezképek kezeléséhez. Indítsa el a [kép listája API konzol](try-image-list-api.md) és a REST API-Kódminták használja. Emellett olvassa el a [kép lista .NET gyors üzembe helyezés](image-lists-quickstart-dotnet.md) Ha ismeri a Visual Studio és a C#.

## <a name="matching-against-your-custom-lists"></a>Az egyéni listájával megfelelő

Az egyeztetés művelet lehetővé teszi, hogy az egyéni listák, létrehozása és kezelése, a lista műveletekkel ellen bejövő képek intelligens megfelelő.

Ha a program egyezést talál, a művelet azonosítója és a megfelelő kép moderálás címkék adja vissza. A válasz ezeket az információkat tartalmazza:

- Egyezés pontszám (0 és 1) között
- Egyező kép
- Kép címkék (előző moderálás hozzárendelt)
- Kép címkék

Példa kivonat:

    {
    ..............,
    "IsMatch": true,
    "Matches": [
        {
            "Score": 1.0,
            "MatchId": 169490,
            "Source": "169642",
            "Tags": [],
            "Label": "Sports"
        }
    ],
    ....
    }

## <a name="human-review-tool"></a>Emberi vizsgálóeszköz

További nuanced esetben használja a tartalom moderátor [tekintse át az eszköz](Review-Tool-User-Guide/human-in-the-loop.md) és annak API illesztésének moderálás eredményeit és az emberi moderátorok áttekintését a tartalmat. Tekintse át a számítógéphez hozzárendelt címkék azokat, és erősítse meg a végső döntések.

![Az emberi moderátorok kép áttekintése](images/moderation-reviews-quickstart-dotnet.PNG)

## <a name="next-steps"></a>További lépések

Tesztelése a [kép moderálás API konzol](try-image-api.md) és a REST API-Kódminták használja. Emellett olvassa el a [kép moderálás .NET gyors üzembe helyezés](image-moderation-quickstart-dotnet.md) Ha ismeri a Visual Studio és a C#.
