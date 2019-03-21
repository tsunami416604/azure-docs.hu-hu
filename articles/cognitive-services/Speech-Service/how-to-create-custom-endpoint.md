---
title: Hozzon létre egy egyéni beszédfelismerési végpontot a Speech szolgáltatással az Azure-ban |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy egyéni hang-szöveg transzformációs végpont Azure Speech Services használatával.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 03/12/2018
ms.author: panosper
ms.openlocfilehash: 1f7a84d187ba6279caad4926d54bfc56254152af
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57862998"
---
# <a name="create-a-custom-speech-to-text-endpoint"></a>Egyéni beszédfelismerési végpont létrehozása

Miután létrehozta a testreszabott akusztikai modell vagy nyelvi modell, egy egyéni hang-szöveg transzformációs végpontját telepíthetné őket.

## <a name="create-an-endpoint"></a>Végpont létrehozása
Új egyéni végpont létrehozásához válassza **végpontok** a a **Custom Speech** az oldal tetején lévő menüben. Ez a művelet eltarthat, hogy a **végpontok** oldal, amely tartalmazza a jelenlegi egyéni végpontok. Ha még nem hozott olyan végpontok, a tábla je prázdná. A tábla címe az aktuális területi beállítást mutatja.

Hozzon létre egy másik nyelven üzemelő példánya, válassza a **módosítása területi beállítás**. További információ a támogatott nyelveket.

Új végpont létrehozásához válassza **hozzon létre új**. Az a **Create Endpoint** panelen adja meg az adatokat a **neve** és **leírás** mezőkbe az egyéni üzembe helyezés.

Az a **előfizetés** kombinált mezőben válassza ki a használni kívánt előfizetést. Ha az S0 (figyelembe véve) előfizetéssel, választhatja az egyidejű kérelmek.

Akkor is kiválasztható, hogy tartalomnaplózás át lett váltva a be- és kikapcsolása. Választjuk, a végpont forgalom tárolja-e. Ha nincs bejelölve, a forgalom tárolja a rendszer letiltja. Az összes naplózott tartalom található letöltési hivatkozások a végpont -> Részletek nézet

Az a **akusztikai modell** listájához, válassza ki az akusztikai modell, amelyet szeretne, majd a a **nyelvi modell** listájához, válassza ki a használni kívánt nyelvi modell. A lehetőségek a akusztikai és nyelvi modelleket mindig foglalja bele az alapszintű Microsoft modellek. Az alapszintű modell kiválasztása a kombinációk korlátozza. Nem kombinálhatók az alapvető természetes nyelvi modellek való kereséssel, majd szabja alapvető modellek.

> [!NOTE]
> Ügyeljen arra, hogy fogadja el a használati feltételeit és a díjszabásról a jelölőnégyzet bejelölésével.
>

Miután kiválasztotta a akusztikai és nyelvi modelljeit, válassza ki a **létrehozás**. Ez a művelet visszaadja, hogy a **központi telepítések** lapot. A táblázat most már tartalmaz egy bejegyzést, amely megfelel az új végpont. A végpont állapota a jelenlegi állapotában mutatja, miközben létrehozása folyamatban van. Egyéni modellek az új végpont vezérlőként akár 30 percet is igénybe vehet. Ha a központi telepítés állapota *Complete*, a végpont készen áll a használatra.

Amikor készen áll az üzembe helyezés, a végpont neve lesz a hivatkozást. A hivatkozásra kattintva megjelenik a **Végpontinformációit** oldal, amely az egyéni végpont használata vagy egy HTTP-kérelem URL-címek vagy a Microsoft Cognitive Services beszédfelismerő Ügyfélkódtárának, amely használja a websockets jeleníti meg.

## <a name="next-steps"></a>További lépések

További oktatóanyagokban talál:
- [Próbaverziós Speech Services-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [Importálni akusztikai modell létrehozása](how-to-customize-acoustic-models.md)
- [Testreszabott nyelvi modell létrehozása](how-to-customize-language-model.md)
