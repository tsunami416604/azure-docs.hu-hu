---
title: A Szövegelemzések API - kognitív Azure-szolgáltatásokkal kapcsolatos gyakori kérdések |} Microsoft Docs
description: Válaszok Microsoft kognitív szolgáltatások szöveg Analytics API kapcsolatos gyakori kérdéseket az Azure-on.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 3/07/2018
ms.author: heidist
ms.openlocfilehash: bf82899b4317f0f5ce0f6ca5096dccef7cddd931
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349399"
---
# <a name="frequently-asked-questions-faq-about-the-text-analytics-api"></a>A Szövegelemzések API kapcsolatos gyakori kérdések (GYIK)

 Válaszok a fogalmakat, kód és a szöveg Analytics API a Microsoft Azure kognitív Services kapcsolatos forgatókönyvek gyakran feltett kérdésekre.

## <a name="can-text-analytics-identify-sarcasm"></a>Szövegelemzések azonosíthatja sarcasm?

Elemzésre van kedve észlelési helyett negatív pozitív véleményeket.

Mindig a céggel kapcsolatos véleményeket elemzési hiba bizonyos fokú, de a modell akkor hasznos, ha nincs rejtett jelentéssel vagy alatta szöveggel a tartalomhoz. Irony, sarcasm, humor és hasonló módon nuanced tartalom kulturális környezet és a leképezés közvetíteni normák támaszkodnak. Ez a tartalom típus közül a legnagyobb kihívást jelentő elemzéséhez. A legnagyobb eltérés van a analyzer és a szubjektív értékelése a HR-részleg által megadott pontszámot között általában a tartalom nuanced jelentéssel.

## <a name="can-i-add-my-own-training-data-or-models"></a>Saját betanítási adatok, vagy a modellek adhat hozzá?

Nem, a modellek pretrained vannak. Az egyetlen elérhető műveleteinek feltöltött adatok pontozása vannak, kulcs kifejezés kinyerésére, és a nyelvi észlelése. Nem kezdeményezünk egyéni modellek. Ha szeretné-e létrehozni és egyéni machine learning modellek tárolására, vegye figyelembe a [gépi tanulási Microsoft R Server képességei](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package).

## <a name="can-i-request-additional-languages"></a>Kérjen további nyelveket is?

Véleményeket elemzést, a kulcs kifejezés kibontási érhetők el egy [válassza ki azon nyelvek számának](text-analytics-supported-languages.md). Természetes nyelvű feldolgozási bonyolult, és jelentős tesztelése, mielőtt az új funkció is megjelent igényel. Ezért azt ne előzetes bejelentése támogatása, így senki időt vesz igénybe egy függőséget az funkció, amely számos több időre van szüksége. 

Működéséhez a következő nyelveket sorrendjének meghatározásához szavazni bizonyos nyelveken [User Voice](https://cognitive.uservoice.com/forums/555922-text-analytics). 

## <a name="why-does-key-phrase-extraction-return-some-words-but-not-others"></a>Miért nem kulcs kifejezés kibontási térjen vissza néhány szót, de nem mások?

Kulcs kifejezés kibontási megszünteti az nélkülözhető szavak és a különálló melléknevek. Melléknév-főnév kombinációit, például "látványosan nézetek" vagy "foggy időjárási" együtt is megjelennek.

Általában kimeneti parancsokat és a mondat objektumok áll. Kimeneti fontosság, a legfontosabbak az első kifejezés szerepel. Fontos a szám, ahányszor egy adott fogalom említett, vagy az, hogy a szöveg egyéb elemeinek elem kapcsolathoz mérése történik.

## <a name="why-does-output-vary-given-identical-inputs"></a>Miért kimeneti változik, azonos bemeneti adatokat a megadott?

Modellek és algoritmusok fejlesztések történik bejelentés, ha a módosítás fő, és csendes integrált a szolgáltatásba, ha a frissítés kisebb. Idővel előfordulhat, hogy ugyanazokat a bemeneti szöveg az eredményeket egy másik véleményeket pontszám vagy kulcs kifejezés kimeneti. Ez a normál és szándékos következtében a felhőben felügyelt machine learning erőforrások használatával.

## <a name="next-steps"></a>További lépések

Egy hiányzó szolgáltatást és funkciót a kérdése van? A kért, vagy szavazott a [UserVoice webhelyén](https://cognitive.uservoice.com/forums/555922-text-analytics).

## <a name="see-also"></a>Lásd még

 [StackOverflow: Szövegelemzések API](https://stackoverflow.com/questions/tagged/text-analytics-api)   
 [StackOverflow: Kognitív szolgáltatások](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
