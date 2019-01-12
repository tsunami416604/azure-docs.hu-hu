---
title: Mi az a Face API?
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használható az arcfelismerési szolgáltatás képeken szereplő arcok észlelésére és elemzésére.
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: overview
ms.date: 10/29/2018
ms.author: sbowles
ms.openlocfilehash: 1916aa94dcf7528ce898ef37eac29c1017f03f15
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2019
ms.locfileid: "54231886"
---
# <a name="what-is-the-azure-face-api"></a>Mi az Azure Face API?

Az Azure Face API egy kognitív szolgáltatás, amely algoritmusokat biztosít a képeken szereplő emberi arcok észleléséhez, felismeréséhez és elemzéséhez. Az emberi arcokkal kapcsolatos információk feldolgozásának képessége számos különböző szoftveres forgatókönyvben lehet fontos, ilyenek például a biztonsági szolgáltatások, a természetes felhasználói felületek, a képtartalmak elemzése és kezelése, a mobilalkalmazások és a robotika.

A Face API több különféle funkciót biztosít, amelyeket az alábbi szakaszok ismertetnek. A továbbiakban többet is megtudhat az egyes funkciókról, és eldöntheti, hogy megfelelnek-e az igényeinek.

## <a name="face-detection"></a>Arcfelismerés

A Face API képes felismerni az emberi arcokat egy képen, és visszaadja a helyüket jelző téglalap koordinátáit. Az arcfelismerés igény szerint kinyerhet még egy sor, archoz kapcsolódó attribútumot, ilyen például a testtartás, nem, életkor, fejtartás, arcszőrzet és szemüveg.

![Egy nő és egy férfi képe, téglalappal a személyek arca körül, amely alatt az életkoruk és a nemük látható](./Images/Face.detection.jpg)

Az arcfelismerési szolgáltatás a [Computer Vision API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) használatával is elérhető, ha azonban további műveleteket szeretne végezni az arcokhoz kapcsolódó adatokkal, a Face API-t (ezt a szolgáltatást) kell használnia. További információ az arcfelismeréssel kapcsolatban: [Detect API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="face-verification"></a>Arcellenőrzés

A Verify API hitelesítést végez két észlelt arc között vagy egy észlelt arc és egy személyobjektum között. Lényegében azt értékeli, hogy a két arc ugyanazon személyhez tartozik-e. Ez leginkább a biztonsággal kapcsolatos forgatókönyvekben bizonyulhat hasznosnak. További információ: [Verify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="find-similar-faces"></a>Hasonló arcok keresése

A Find Similar API a célarcot és a jelöltek arcát használva megkeresi azon arcok egy kisebb részhalmazát, amelyek a leginkább hasonlítanak a célarcra. Két működési mód támogatott, a **matchPerson** és a **matchFace**. A **matchPerson** mód hasonló arcokat ad vissza az ugyanazon személyre irányuló szűrést követően (a [Verify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) használatával). A **matchFace** mód nem foglalkozik az azonos személyekre vonatkozó szűrővel, és a leginkább hasonló jelöltek arcát adja vissza, amelyek nem feltétlenül tartoznak ugyanazon személyhez.

A következő példában ez a célarc:

![Mosolygó nő](./Images/FaceFindSimilar.QueryFace.jpg)

Ezek pedig a jelölt személyek arcai:

![Öt mosolygó személy. Az „a” és a „b” kép ugyanazt a személyt ábrázolja](./Images/FaceFindSimilar.Candidates.jpg)

Négy hasonló arc megkereséséhez a **matchPerson** mód az (a) és a (b) képet adja vissza, amely a célarccal egyező személyt ábrázolja. A **matchFace** mód az (a), (b), (c) és (d)&mdash;képet adja vissza, pontosan négy jelöltet, még akkor is, ha közülük egyesek nem ugyanazok a személyek, mint a célszemély, vagy a hasonlóság kicsi. További információ: [Find Similar API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).

## <a name="face-grouping"></a>Arccsoportosítás

A Group API több csoportra osztja az ismeretlen arcokat a hasonlóság alapján. Mindegyik csoport az eredeti arcok halmazának különálló valódi részhalmaza. A csoportokban található arcok mindegyike vélhetően ugyanahhoz a személyhez tartozik, azonban több különböző csoport is lehet egyetlen személyhez kapcsolódóan (mely csoportok egy másik tényező, például az arckifejezés alapján jönnek létre). További információ: [Group API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="person-identification"></a>Személyazonosítás

Az Identify API-val az észlelt arcok azonosíthatók a személyek adatbázisa alapján. Ez a fényképkezelési szoftverekben végzett automatikus képcímkézéskor bizonyulhat hasznosnak. Az adatbázis előre létrehozható, és menet közben szerkeszthető.

Az alábbi kép a „myfriends” nevű példaadatbázist ábrázolja. Mindegyik csoport legfeljebb 1 000 000 különböző személyobjektumot tartalmazhat, és mindegyik személyobjektum legfeljebb 248 regisztrált arccal rendelkezhet.

![Táblázat, amely a különböző személyeket 3 oszlopban, az arcképeket pedig 3 sorban tartalmazza](./Images/person.group.clare.jpg)

Egy adatbázis létrehozása és betanítása után azonosítást hajthat végre a csoport és egy új észlelt arc között. Ha a rendszer a csoport egyik tagjaként azonosítja az arcot, visszaadja a személyobjektumot.

További információ a személyazonosítással kapcsolatban: [Identify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="use-containers"></a>Tárolók használata

[Arcfelismerés a tárolót használja](face-how-to-install-containers.md) észleléséhez ismeri fel, és arcazonosítás szabványos Docker-tároló közelebb telepítse az adatokhoz.

## <a name="sample-apps"></a>Mintaalkalmazások

Az alábbi mintaalkalmazások a Face API használatának néhány módját mutatják be.

- [A Microsoft Face API: Windows-Klienskódtár és a minta](https://github.com/Microsoft/Cognitive-Face-Windows) – a WPF-alkalmazás azt mutatja be, számos forgatókönyv Face észlelési, elemzési és azonosítása.
- [FamilyNotes UWP-alkalmazás](https://github.com/Microsoft/Windows-appsample-familynotes) – Univerzális Windows Platform-alkalmazás (UWP-alkalmazás), amely a beszédfelismerés, Cortana, a szabadkézi bevitel és a kamera használata mellett arcazonosítást alkalmaz egy családi üzenetmegosztási forgatókönyvben.

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Csakúgy, mint a Cognitive Services összes az Arcfelismerés szolgáltatással a fejlesztők célszerű tisztában lennie a vásárlói adatokat a Microsoft házirendeket. Tekintse meg a [Cognitive Services-lapra](https://www.microsoft.com/en-us/trustcenter/cloudservices/cognitiveservices) a további Microsoft Trust Center.

## <a name="next-steps"></a>További lépések

Kövesse egy egyszerű arcfelismerési forgatókönyv kóddal történő implementálásának rövid útmutatóját.
- [Rövid útmutató: Arcfelismerés a képet a .NET SDK használatával C# ](quickstarts/csharp.md) (más nyelvek esetén érhető el)
