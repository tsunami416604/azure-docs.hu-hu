---
title: A LUIS-alkalmazások az Azure-ban előre összeállított tartományok használata |} A Microsoft Docs
description: Ismerje meg, a Language Understanding Intelligent Service (LUIS) alkalmazások előre összeállított tartományok használata.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: diberry
ms.openlocfilehash: df57f9adf5bf7f5f652a77ddeafe950463c6a9fc
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224177"
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
