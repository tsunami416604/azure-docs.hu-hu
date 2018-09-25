---
title: Előre összeállított tartományok használata az alkalmazások gyorsabb a LUIS-alkalmazások készítése
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) biztosít az előre összeállított tartományok, az ügyfélalkalmazások általános kategóriáit vagy előre létrehozott adatkészletek szándékok és entitások, amelyek együtt, a tartományok számára. Az előre összeállított tartományok előre betanított, és készen áll, hogy a LUIS-alkalmazás.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/06/2018
ms.author: diberry
ms.openlocfilehash: 1aa7e1bf9c1a584803a60a5061b4ae4cc8664ff4
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037371"
---
# <a name="use-prebuilt-domains-in-luis-apps"></a>A LUIS-alkalmazások előre összeállított tartományok használata  

Language Understanding (LUIS) biztosít *előre összeállított tartományok*, olyan előre összeállított készletei, amelyek [leképezések](luis-how-to-add-intents.md) és [entitások](luis-concept-entity-types.md) , hogy működnek együtt, a tartományok vagy közös kategóriái ügyfélalkalmazások számára. Az előre összeállított tartományok előre betanított, és készen áll, hogy a LUIS-alkalmazás. A leképezések és a egy előre elkészített tartományban entitások olyan teljes mértékben testre szabható, miután hozzáadta azokat az alkalmazásba – betaníthatja őket a kimondott szöveg a rendszer így a felhasználók számára. Előre összeállított teljes tartományban használja kiindulási pontként a testreszabás, vagy csak kölcsönzött néhány leképezések vagy előre összeállított tartományok entitásokat. 

Keresse meg a **előre összeállított tartományok** lapján megtekintheti a többi előre összeállított tartományok használhatja az alkalmazásban. Egy tartomány hozzáadása az alkalmazáshoz, a csempére, vagy kattintson a **további** a csempén további információ a szándékokat és entitásokat.

> [!TIP]
> Az előre összeállított tartományok teljes listáját megtalálhatja az [előre összeállított tartományok referencia](./luis-reference-prebuilt-domains.md).

![Előre összeállított tartomány hozzáadása](./media/luis-how-to-prebuilt-domain-entities/add-prebuilt-domain.png)


## <a name="add-a-prebuilt-domain"></a>Előre összeállított tartomány hozzáadása
Az a **előre összeállított tartományok** lapon található a RestaurantReservation tartomány, kattintson **tartomány hozzáadása**. Miután az előre összeállított tartomány bővült a LUIS-alkalmazás, nyissa meg **leképezések** , majd kattintson a RestaurantReservation.Reserve célja. Láthatja, hogy sok példa utterances rendelkezik már megadott és címkével rendelkező entitásokat.

![RestaurantReservation.Reserve leképezés](./media/luis-how-to-prebuilt-domain-entities/prebuilt-domain-restaurant-reservation.png)


## <a name="designing-luis-apps-from-prebuilt-domains"></a>Előre összeállított tartományok a LUIS-alkalmazások tervezése
Ha előre összeállított tartományok a LUIS-alkalmazás használ, testre szabhatja egy előre elkészített teljes tartomány, vagy csak néhány példa a szándékok és entitások, kezdje.

## <a name="customizing-an-entire-prebuilt-domain"></a>Előre összeállított teljes tartományban testreszabása
Előre összeállított tartományok tervezték általános. Számos szándékok és entitások, amelyek közül választhat egy alkalmazást az igényeinek megfelelően testre tartalmazzák. Ha a testreszabás, előre összeállított teljes tartományban indul el, törölje a szándékok és entitások, amelyhez az alkalmazás használatához nincs szükség. A készlethez, amely az előre összeállított tartomány már biztosít néhány leképezések vagy entitások is hozzáadhat. Például, ha használja a **események** sport esemény alkalmazások előre összeállított tartományban is sport csapatok entitások hozzáadása. Amikor elkezd [beszédmódok nyújtó](luis-how-to-add-example-utterances.md) , LUIS, olyan feltételek, amelyek az alkalmazás adott. A LUIS megtanulja felismerni őket, és az előre összeállított tartományban szándékok és entitások az alkalmazás igényeinek megfelelően szokásait. 

> [!TIP]
> A leképezések és a egy előre elkészített tartományban entitások működnek együtt a leghatékonyabban. Érdemes úgy, hogy szándékokat és entitásokat, amikor csak lehetséges ugyanahhoz a tartományhoz.
> * Ajánlott eljárás, hogy kapcsolódó leképezések ugyanahhoz a tartományhoz. Például, ha testreszabása a `MakeReservation` a szándék a **helyek** tartományhoz, majd válassza ki a `Cancel` szándék a a **helyek** helyett a Mégse gombra célt a atartomány**Események** vagy **segédprogramok** tartományok.

## <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Egy előre elkészített tartomány szándékot viselkedésének módosítása
Előfordulhat, hogy egy előre elkészített tartományt tartalmaz megjelölésű megjelölésű szeretné engedélyezni a LUIS-alkalmazás a hasonló, de azt szeretné, hogy eltérően viselkednek. Például a **helyek** előre összeállított tartományt biztosít egy `MakeReservation` célja, hogy egy étterem foglalást, de szeretné az alkalmazást, hogy a szállodai foglalások adott szándékot használatához. Ebben az esetben módosíthatja, hogy a leképezés viselkedését utterances azáltal, hogy a LUIS szállodai foglalások tétele és a címkézés őket használatával a `MakeReservation` szándék, így a LUIS újrataníthatók felismerni a `MakeReservation` küldött kérelemben egy szállodai Vendég repülőjáratra szándék .

> [!TIP]
> Tekintse meg az előre összeállított testre szabható használható minden olyan tartományban szándékok segédprogramok tartományt. Például hozzáadhat `Utilities.Repeat` az alkalmazás és a vonat, felismerni bármilyen műveletek felhasználó szeretné ismételje meg az alkalmazásban.


## <a name="next-step"></a>Következő lépés

További példa beszédmódok hozzáadása egy előre elkészített tartomány testreszabásához.

> [!div class="nextstepaction"]
> [Példa beszédmódok hozzáadása](./luis-how-to-add-example-utterances.md)

## <a name="additional-resources"></a>További források

Tekintse meg a [előre összeállított tartományok referencia](./luis-reference-prebuilt-domains.md) tartalmaz további információt az előre összeállított tartományok.
