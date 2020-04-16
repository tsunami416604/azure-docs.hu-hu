---
title: Mi az a Face szolgáltatás?
titleSuffix: Azure Cognitive Services
description: Az Azure Cognitive Services Face szolgáltatás algoritmusokat biztosít, amelyek a képeken lévő emberi arcok észlelésére, felismerésére és elemzésére szolgálnak.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 6714a0c4b967d80ad683ef023b5811423bdcb022
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81403470"
---
# <a name="what-is-the-azure-face-service"></a>Mi az Azure Face szolgáltatás?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Az Azure Cognitive Services Face szolgáltatás olyan algoritmusokat biztosít, amelyek észlelik, felismerik és elemzik a képekemberi arcát. Az emberi arc információk feldolgozásának képessége számos különböző szoftveres forgatókönyvben fontos. Példa forgatókönyvek közé tartozik a biztonság, a természetes felhasználói felület, a képtartalom-elemzés és -kezelés, a mobilalkalmazások és a robotika.

A Face szolgáltatás számos különböző funkciót biztosít, amelyek mindegyike a következő szakaszokban található.

## <a name="face-detection"></a>Arcfelismerés

A Face szolgáltatás észleli az emberi arcokat egy képen, és visszaadja a helyük téglalapkoordinátáit. Az arcfelismerés szükség esetén számos arccal kapcsolatos attribútumot nyerhet ki. Ilyenpéldául a fejpóz, a nem, az életkor, az érzelem, az arcszőrzet és a szemüveg.

> [!NOTE]
> Az arcfelismerő funkció a [Computer Vision API-n](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)keresztül is elérhető. Ha további műveleteket szeretne végezni arcadatokkal, használja a Face szolgáltatást, amely a cikkben tárgyalt szolgáltatás.

![Egy nő és egy férfi képe, téglalapokkal az arcuk körül, koruk és nemük.](./Images/Face.detection.jpg)

Az arcfelismeréssel kapcsolatos további információkért tekintse meg az [Arcfelismerési](concepts/face-detection.md) fogalmakról szóló cikket. Lásd még az [API-referenciadokumentáció észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) című témakört.

## <a name="face-verification"></a>Arcellenőrzés

Az Ellenőrzés API hitelesítést végez két észlelt arcon vagy egy észlelt arcról egy személy objektumra. Lényegében azt értékeli, hogy a két arc ugyanazon személyhez tartozik-e. Ez a képesség biztonsági helyzetekben is hasznos lehet. További információt az [Arcfelismerési](concepts/face-recognition.md) útmutatóban vagy az [API-kézikönyvben](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) talál.

## <a name="find-similar-faces"></a>Hasonló arcok keresése

A Hasonló keresése API összehasonlítja a célarcot a jelölt arcok készletével, hogy a céllaphoz hasonló arcok kisebb készletét találja. Két munkamód, matchPerson és matchFace, támogatottak. A matchPerson mód hasonló arcokat ad vissza, miután ugyanannak a személynek szűr az [Ellenőrzés API használatával.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) A matchFace mód figyelmen kívül hagyja az azonos személyű szűrőt. Olyan hasonló jelöltarcok listáját adja vissza, amelyek ugyanahhoz a személyhez tartozhatnak, vagy nem.

A következő példa a célarcot mutatja be:

![Mosolygó nő](./Images/FaceFindSimilar.QueryFace.jpg)

És ezek a képek a jelölt arcok:

![Öt mosolygó személy. Az a és b képeken ugyanaz a személy látható.](./Images/FaceFindSimilar.Candidates.jpg)

Négy hasonló arc megkereséséhez a matchPerson mód a és b értéket ad vissza, amelyek ugyanazt a személyt mutatják, mint a céllap. A matchFace mód&mdash;pontosan négy jelöltet ad vissza, még akkor is, ha néhány nem ugyanaz a személy, mint a cél, vagy alacsony a hasonlóság. További információt az [Arcfelismerési](concepts/face-recognition.md) útmutatóban vagy a [Hasonló API-k](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) keresése referenciadokumentációban talál.

## <a name="face-grouping"></a>Arccsoportosítás

A Group API több csoportra osztja az ismeretlen arcokat a hasonlóság alapján. Mindegyik csoport az eredeti arcok halmazának különálló valódi részhalmaza. A csoport összes arca valószínűleg ugyanahhoz a személyhez tartozik. Egyetlen személy számára több különböző csoport is létezhet. A csoportokat egy másik tényező különbözteti meg, például a kifejezés. További információt az [Arcfelismerési](concepts/face-recognition.md) útmutatóban vagy a [Csoport API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) referenciadokumentációjában talál.

## <a name="person-identification"></a>Személyazonosítás

A Identify API-t egy észlelt arc azonosítására használják egy személyek adatbázisával szemben. Ez a funkció hasznos lehet a fényképkezelő szoftver automatikus képcímkézéséhez. Az adatbázist előre létrehozza, és idővel szerkesztheti.

Az alábbi képen egy példa `"myfriends"`látható a . Minden csoport legfeljebb 1 millió különböző személyobjektumot tartalmazhat. Mindegyik személyobjektumhoz legfeljebb 248 arc lehet regisztrálva.

![A rács három oszlop különböző emberek, mindegyik három sor arcképek](./Images/person.group.clare.jpg)

Az adatbázis létrehozása és betanítása után egy új észlelt arccal azonosíthatja a csoportot. Ha a rendszer a csoport egyik tagjaként azonosítja az arcot, visszaadja a személyobjektumot.

A személyek azonosításáról további információt az [Arcfelismerési](concepts/face-recognition.md) útmutatóban vagy az [API azonosítása hivatkozási](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) dokumentációban talál.

## <a name="containers"></a>Containers

[A Face-tároló segítségével](face-how-to-install-containers.md) észlelheti, felismerheti és azonosíthatja az arcokat egy szabványosított Docker-tároló, amely közelebb van az adatokhoz.

## <a name="sample-apps"></a>Mintaalkalmazások

A következő mintaalkalmazások a Face szolgáltatás használatának néhány módját mutatják be:

- [Face API: A Windows ügyfélkódtár és minta](https://github.com/Microsoft/Cognitive-Face-Windows) egy WPF-alkalmazás, amely az arcfelismerés, -elemzés és -azonosítás számos forgatókönyvét mutatja be.
- [A FamilyNotes UWP alkalmazás](https://github.com/Microsoft/Windows-appsample-familynotes) egy univerzális Windows-platform (UWP) alkalmazás, amely arcazonosítást, valamint beszédfelismerést, Cortanát, tintát és kamerát használ egy családi jegyzetmegosztási forgatókönyvben.

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

A Cognitive Services összes erőforrásához, a Face szolgáltatást használó fejlesztőknek is tisztában kell lenniük a Microsoft ügyféladatokra vonatkozó irányelveivel. További információt a [Cognitive Services (Microsoft Adatvédelmi](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) központ) című témakörben talál.

## <a name="next-steps"></a>További lépések

Kövesse a rövid útmutatót egy arcészlelési forgatókönyv kódban történő megvalósításához:

- [Gyorsútmutató: A lemezkép lapjait a .NET SDK c# használatával észleli.](quickstarts/csharp.md) Más nyelvek is rendelkezésre állnak.
