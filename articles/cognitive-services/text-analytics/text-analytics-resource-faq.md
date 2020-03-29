---
title: Gyakori kérdések a Szövegelemzés API-ról
titleSuffix: Azure Cognitive Services
description: Válaszok az Azure Cognitive Services Text Analytics API-jával kapcsolatos fogalmakkal, kódokkal és forgatókönyvekkel kapcsolatos gyakori kérdésekre.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: aca3b0bea65923cee1bae25020058666c973922c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837126"
---
# <a name="frequently-asked-questions-faq-about-the-text-analytics-cognitive-service"></a>Gyakori kérdések a Text Analytics kognitív szolgáltatásról

 Válaszok a Microsoft Cognitive Services Text Analytics API-jához az Azure-ban kapcsolatos fogalmakkal, kódokkal és forgatókönyvekkel kapcsolatos gyakori kérdésekre.

## <a name="can-text-analytics-identify-sarcasm"></a>A Szövegelemzés azonosíthatja a szarkazmust?

Elemzés a pozitív-negatív hangulat helyett hangulatészlelése.

Mindig van némi pontatlanság a hangulatelemzésben, de a modell akkor a leghasznosabb, ha nincs rejtett jelentés vagy a tartalom subtextje. Irónia, szarkazmus, humor, és hasonlóan árnyalt tartalom támaszkodnak a kulturális háttér és a normák közvetíteni szándék. Ez a fajta tartalom az egyik legnagyobb kihívást jelentő elemezni. Jellemzően az analizátor által készített adott pontszám és az ember szubjektív értékelése közötti legnagyobb eltérés az árnyalt jelentésű tartalom.

## <a name="can-i-add-my-own-training-data-or-models"></a>Hozzáadhatok saját betanítási adatokat vagy modelleket?

Nem, a modellek előre be vannak képezve. A feltöltött adatokon elérhető egyetlen műveletek a pontozás, a kulcskifejezések kinyerése és a nyelvfelismerés. Nem üzemeltetünk egyéni modelleket. Ha egyéni gépi tanulási modelleket szeretne létrehozni és üzemeltetni, fontolja meg a [Microsoft R Server gépi tanulási képességeit.](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)

## <a name="can-i-request-additional-languages"></a>Kérhetek további nyelveket?

A hangulatelemzés és a kulcskifejezések kinyerése bizonyos számú nyelven érhető [el.](text-analytics-supported-languages.md) A természetes nyelvi feldolgozás összetett, és jelentős tesztelést igényel az új funkciók kiadása előtt. Emiatt elkerüljük a támogatás előzetes bejelentését, hogy senki ne igényeljen olyan funkcióktól való függőséget, amelynek több időre van szüksége az érettséghez. 

Annak érdekében, hogy rangsorolja, hogy mely nyelveken dolgozzunk a következő, szavazzon bizonyos nyelvekre a [User Voice -on.](https://cognitive.uservoice.com/forums/555922-text-analytics) 

## <a name="why-does-key-phrase-extraction-return-some-words-but-not-others"></a>Miért a kulcskifejezés-kivonás visszaad néhány szót, de másokat nem?

A kulcskifejezések kinyerése kiküszöböli a nem alapvető fontosságú szavakat és az önálló mellékneveket. A melléknév-főnév kombinációk, mint például a "látványos kilátás" vagy a "ködös időjárás" együtt kerülnek visszaadásra.

A kimenet általában a mondat nagyszámaiból és objektumaiból áll. A kimenet fontossági sorrendben van felsorolva, az első mondat a legfontosabb. A fontosságot az alapján kell mérni, hogy hányszor említenek meg egy adott fogalmat, vagy hogy az elem milyen kapcsolatban áll a szöveg más elemeivel.

## <a name="why-does-output-vary-given-identical-inputs"></a>Miért változik a kimenet, azonos bemenetek szerint?

A modellek és algoritmusok fejlesztéseit akkor jelentik be, ha a módosítás jelentős, vagy ha a frissítés kisebb, csendesen bekerül a szolgáltatásba. Idővel előfordulhat, hogy ugyanaz a szövegbevitel más hangulatpontszámot vagy kulcskifejezés-kimenetet eredményez. Ez egy normális és szándékos következménye a felügyelt gépi tanulási erőforrások a felhőben.

## <a name="next-steps"></a>További lépések

A kérdése hiányzik egy hiányzó funkcióról vagy funkcióról? Fontolja meg a [uservoice weboldalunkon](https://cognitive.uservoice.com/forums/555922-text-analytics)történő igénylést vagy szavazást.

## <a name="see-also"></a>Lásd még

 [StackOverflow: Szövegelemzési API](https://stackoverflow.com/questions/tagged/text-analytics-api)   
 [StackOverflow: Kognitív szolgáltatások](https://stackoverflow.com/questions/tagged/microsoft-cognitive)
