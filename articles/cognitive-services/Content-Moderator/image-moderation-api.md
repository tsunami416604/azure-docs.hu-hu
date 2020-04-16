---
title: Képmoderálás - Tartalommoderátor
titleSuffix: Azure Cognitive Services
description: A Tartalommoderátor gépi képmoderálásával és a hurokban lévő emberi ellenőrzés eszközzel moderálja a felnőtteknek szánt és pikáns tartalmakat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 36777208dc8ac179f1aaf345c374a33001e3f8bd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404270"
---
# <a name="learn-image-moderation-concepts"></a>Képmoderálási fogalmak megismerése

A Tartalommoderátor gépi képmoderálásával és a hurokban lévő [emberi ellenőrzés eszközzel](Review-Tool-User-Guide/human-in-the-loop.md) moderálja a felnőtteknek szánt és pikáns tartalmakat. Képbe késelést a szövegtartalomból, és kibonthatja a szöveget, és észlelheti az arcokat. A képeket összepárosíthatja az egyéni listákkal, és további műveleteket tehet.

## <a name="evaluating-for-adult-and-racy-content"></a>Felnőtt és pikáns tartalom értékelése

A **Kiértékelés** művelet 0 és 1 közötti megbízhatósági pontszámot ad vissza. Azt is visszaadja a logikai adatok egyenlő igaz vagy hamis. Ezek az értékek előre jelzik, hogy a kép tartalmaz-e potenciális felnőtt vagy pikáns tartalmat. Amikor az API-t a képpel (fájllal vagy URL-lel) hívja meg, a visszaadott válasz a következő információkat tartalmazza:

    "ImageModeration": {
      .............
      "adultClassificationScore": 0.019196987152099609,
      "isImageAdultClassified": false,
      "racyClassificationScore": 0.032390203326940536,
      "isImageRacyClassified": false,
      ............
      ],

> [!NOTE]
> 
> - Az `isImageAdultClassified` az olyan képek esetleges előfordulását jelzi, amelyek bizonyos esetekben szexuálisan explicit vagy felnőtt tartalomnak minősülhetnek.
> - Az `isImageRacyClassified` az olyan képek esetleges előfordulását jelzi, amelyek bizonyos esetekben szexuális utalásokat tartalmazó vagy felnőtt tartalomnak minősülhetnek.
> - A pontszámok 0 és 1 között vannak. Minél magasabb a pontszám, annál magasabb a modell azt jósolja, hogy a kategória alkalmazható lehet. Ez az előzetes verzió a manuálisan kódolt eredmények helyett egy statisztikai modellen alapul. Javasoljuk, hogy a saját tartalommal tesztelje annak meghatározásához, hogy az egyes kategóriák hogyan igazodjanak az Ön igényeihez.
> - A logikai értékek a belső pontszám küszöbértékek től függően igazak vagy hamisak. Az ügyfeleknek fel kell mérniük, hogy ezt az értéket használják-e, vagy a tartalmi irányelveik alapján döntenek-e az egyéni küszöbértékek ről.

## <a name="detecting-text-with-optical-character-recognition-ocr"></a>Szöveg észlelése optikai karakterfelismeréssel (OCR)

Az **Optikai karakterfelismerés (OCR)** művelet előre jelzi a szövegtartalom jelenlétét a képen, és kinyeri azt a szöveg moderálása érdekében, többek között. Megadhatja a nyelvet. Ha nem ad meg nyelvet, az észlelés alapértelmezés szerint angol lesz.

A válasz a következő információkat tartalmazza:
- Az eredeti szöveg.
- Az észlelt szövegelemek a megbízhatósági pontszámaikkal.

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


## <a name="detecting-faces"></a>Arcfelismerés

Az arcok észlelése segít felismerni a személyes adatokat, például a képeken lévő arcokat. Észleli a potenciális arcokat és az egyes képekpotenciális lapjainak számát.

A válasz tartalmazza ezt az információt:

- Arcok száma
- Az észlelt arcok helyének listája

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

## <a name="creating-and-managing-custom-lists"></a>Egyéni listák létrehozása és kezelése

Sok online közösségben, miután a felhasználók képeket vagy más típusú tartalmat töltöttek fel, a sértő elemeket a következő napokban, hetekben és hónapokban többször is megoszthatják. A költségek ismételt szkennelés és kiszűrése ugyanazt a képet, vagy akár kissé módosított változataa kép több helyen is drága és hiba-hajlamos.

Ahelyett, hogy ugyanazt a képet többször moderaníta, a sértő képeket hozzáadja a letiltott tartalmak egyéni listájához. Így a tartalommoderálási rendszer összehasonlítja a bejövő képeket az egyéni listákkal, és leállítja a további feldolgozást.

> [!NOTE]
> A maximális korlát **5 képlista**, amelyek egyenként **nem haladhatják meg a 10 000 képet**.
>

A tartalommoderátor teljes [képlista-kezelési API-t](try-image-list-api.md) biztosít az egyéni lemezképek listájának kezeléséhez szükséges műveletekkel. Kezdje a [képlisták API-konzollal,](try-image-list-api.md) és használja a REST API-kódmintákat. Tekintse meg a [.NET lemezképlistát](image-lists-quickstart-dotnet.md) is, ha ismeri a Visual Studio és a C#.

## <a name="matching-against-your-custom-lists"></a>Egyeztetés az egyéni listákhoz

Az egyezési művelet lehetővé teszi a bejövő képek intelligens egyeztetését az egyéni listák bármelyikével, a Lista műveletek használatával létrehozva és kezelve.

Ha egyezést talál, a művelet visszaadja az azonosítót és az egyező kép moderálási címkéit. A válasz tartalmazza ezt az információt:

- Meccspontszám (0 és 1 között)
- Egyező kép
- Képcímkék (az előző moderálás során hozzárendelve)
- Képcímkék

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

## <a name="review-tool"></a>Felülvizsgálati eszköz

További árnyaltabb esetekben használja a [Tartalommoderátor-ellenőrző eszközt](Review-Tool-User-Guide/human-in-the-loop.md) és annak API-ját, hogy felszínre hozhassa a moderálás eredményeit és tartalmát az emberi moderátorok véleményezésében. Áttekintik a géppel hozzárendelt címkéket, és megerősítik a végső döntésüket.

![Képek felülvizsgálata emberi moderátorok által](images/moderation-reviews-quickstart-dotnet.PNG)

## <a name="next-steps"></a>További lépések

Tesztelje a [képmoderálás API-konzolt,](try-image-api.md) és használja a REST API-kódmintákat. Tekintse meg a [.NET SDK rövidútmutató](dotnet-sdk-quickstart.md) Képmoderálás szakaszát is, ha ismeri a Visual Studio és a C# programot.
