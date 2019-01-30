---
title: Hozzon létre egy egyéni beszédfelismerési végpontot a Custom Speech Service az Azure-ban |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy egyéni hang-szöveg transzformációs végpontot a Cognitive Services Custom Speech Service.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 07/08/2017
ms.author: panosper
ms.openlocfilehash: f9aca8529bf0407022ff3683c1cd73b87045f2f2
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55216666"
---
# <a name="create-a-custom-speech-to-text-endpoint"></a>Egyéni beszédfelismerési végpont létrehozása

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

Miután létrehozta a testreszabott akusztikai modell vagy nyelvi modell, egy egyéni hang-szöveg transzformációs végpontját telepíthetné őket. 

## <a name="create-an-endpoint"></a>Végpont létrehozása
Új egyéni végpont létrehozásához válassza **központi telepítések** a a **Custom Speech** az oldal tetején lévő menüben. Ez a művelet eltarthat, hogy a **központi telepítések** oldal, amely tartalmazza a jelenlegi egyéni végpontok. Ha még nem hozott olyan végpontok, a tábla je prázdná. A tábla címe az aktuális területi beállítást mutatja. 

Hozzon létre egy másik nyelven üzemelő példánya, válassza a **módosítása területi beállítás**. További információ a támogatott nyelvek: [területi beállításokat támogatja a Custom Speech Service](cognitive-services-custom-speech-change-locale.md).

Új végpont létrehozásához válassza **hozzon létre új**. Az a **központi telepítés létrehozása** panelen adja meg az adatokat a **neve** és **leírás** mezőkbe az egyéni üzembe helyezés.

Az a **előfizetés** kombinált mezőben válassza ki a használni kívánt előfizetést. Ha egy S2-előfizetést, kiválaszthatja a skálázási egység és tartalmának naplózását. További információ a skálázási egység és a naplózás: [Custom Speech Service-mérőszámok és kvóták](../cognitive-services-custom-speech-meters.md).

Az alábbi táblázat bemutatja, hogyan skálázási egységek leképezése az egyidejű kérelmek érhető el:

| Skálázási egység | Az egyidejű kérelmek száma |
| ------ | ----- |
| 0 | 1 |
| 1 | 5 |
| 2 | 10 |
| 3 | 15 |
| n | 5 * n |

Akkor is kiválasztható, hogy tartalomnaplózás át lett váltva a be- és kikapcsolása. Választjuk, a végpont forgalom a Microsoft belső használatra tárolja-e. Ha nincs bejelölve, a forgalom tárolja a rendszer letiltja. Tartalomnaplózás eredményez további költségek újrainduljanak. Tekintse át a [díjszabási információkat tartalmazó oldal](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/) részleteiről.

> [!NOTE]
> Tartalomnaplózás "No nyomkövetési" nevezzük az árképzést ismertető oldalra.
>


Emellett a Microsoft biztosít durva becslést a költségek, így mindig tisztában a skálázási egység és tartalomnaplózás költségeinek gyakorolt hatást. Ez a becslés durva becslést, és a tényleges költségek eltérhetnek.

> [!NOTE]
> Ezek a beállítások F0 (ingyenes csomag) előfizetés nem érhetők el.
>

Az a **akusztikai modell** listájához, válassza ki az akusztikai modell, amelyet szeretne, majd a a **nyelvi modell** listájához, válassza ki a használni kívánt nyelvi modell. A lehetőségek a akusztikai és nyelvi modelleket mindig foglalja bele az alapszintű Microsoft modellek. Az alapszintű modell kiválasztása a kombinációk korlátozza. Nem kombinálhatók az alapvető természetes nyelvi modellek való kereséssel, majd szabja alapvető modellek.

![A központi telepítés létrehozása lap](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-create2.png)

> [!NOTE]
> Ügyeljen arra, hogy fogadja el a használati feltételeit és a díjszabásról a jelölőnégyzet bejelölésével.
>

Miután kiválasztotta a akusztikai és nyelvi modelljeit, válassza ki a **létrehozás**. Ez a művelet visszaadja, hogy a **központi telepítések** lapot. A táblázat most már tartalmaz egy bejegyzést, amely megfelel az új végpont. A végpont állapota a jelenlegi állapotában mutatja, miközben létrehozása folyamatban van. Egyéni modellek az új végpont vezérlőként akár 30 percet is igénybe vehet. Ha a központi telepítés állapota *Complete*, a végpont készen áll a használatra.

![A központi telepítések lap](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-ready.png)

Amikor készen áll az üzembe helyezés, az üzemelő példány neve lesz a hivatkozást. A hivatkozásra kattintva megjelenik a **központi telepítési információk** oldal, amely az egyéni végpont használata vagy egy HTTP-kérelem URL-címek vagy a Microsoft Cognitive Services beszédfelismerő Ügyfélkódtárának, amely használja a websockets jeleníti meg.

![A központi telepítési információkat tartalmazó oldal](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-info2.png)

## <a name="next-steps"></a>További lépések

További oktatóanyagokban talál:
* [Egy egyéni hang-szöveg transzformációs végpont használata](cognitive-services-custom-speech-use-endpoint.md)
* [Egyéni akusztikai modell létrehozása](cognitive-services-custom-speech-create-acoustic-model.md)
* [Egyéni nyelvi modell létrehozása](cognitive-services-custom-speech-create-language-model.md)
