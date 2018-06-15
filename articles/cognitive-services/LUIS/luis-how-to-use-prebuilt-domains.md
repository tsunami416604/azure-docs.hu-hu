---
title: Előre elkészített tartományok LUIS alkalmazásokban az Azure-ban |} Microsoft Docs
description: Útmutató az előre elkészített tartományok nyelvi ismertetése intelligens szolgáltatás (LUIS) alkalmazásban.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 72ca0def8528adae5e46a02fa5bfccd14a3b6ab4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349802"
---
# <a name="use-prebuilt-domains-in-luis-apps"></a>Előre elkészített tartományok LUIS alkalmazásokban  

Nyelvi ismertetése (LUIS) biztosít *előre elkészített tartományok*, ezek előre elkészített készleteinek [leképezések](luis-how-to-add-intents.md) és [entitások](luis-concept-entity-types.md) adott együttműködik a tartományok vagy közös kategóriái ügyfélalkalmazások számára. Az előre elkészített tartományok előre betanítva, és készen áll a LUIS adhat meg. A leképezések és entitások egy előre elkészített tartományban is teljes mértékben testreszabható, miután felvett őket az alkalmazáshoz,-akkor is betanítása őket utterances rendelkező a rendszerről ahhoz, hogy a felhasználók működni. Használja a teljes előre elkészített tartományban kiindulási pontként a testreszabás, vagy csak kölcsön néhány leképezések vagy egy előre elkészített tartományból entitások. 

Keresse meg a **előre elkészített tartományok** lapon, láthatja is használhatja az alkalmazás előre elkészített tartományához. Kattintson a csempére veheti fel az alkalmazást egy tartományhoz, vagy kattintson a **további** a csempén a leképezések és entitások.

> [!TIP]
> Az előre elkészített tartományok teljes listáját megtalálhatja a [előre elkészített tartományok hivatkozás](./luis-reference-prebuilt-domains.md).

![Előre elkészített tartomány hozzáadása](./media/luis-how-to-prebuilt-domain-entities/add-prebuilt-domain.png)


## <a name="add-a-prebuilt-domain"></a>Egy előre elkészített tartomány hozzáadása
Az a **előre elkészített tartományok** lapon található a RestaurantReservation tartomány, kattintson **tartomány hozzáadása**. Ha az előre elkészített tartomány az LUIS alkalmazáshoz hozzáadott, nyissa meg a **leképezések** , majd kattintson a a RestaurantReservation.Reserve célt. Láthatja, hogy számos példát utterances már megadott és az entitások címkével.

![RestaurantReservation.Reserve leképezés](./media/luis-how-to-prebuilt-domain-entities/prebuilt-domain-restaurant-reservation.png)


## <a name="designing-luis-apps-from-prebuilt-domains"></a>Előre elkészített tartományokból LUIS alkalmazások megtervezése
Előre elkészített tartományok az LUIS alkalmazás használatakor testre szabhatja egy előre elkészített teljes tartományban, vagy a leképezések és entitásokat tartalmazó kezdjen el.

## <a name="customizing-an-entire-prebuilt-domain"></a>Teljes előre elkészített tartományban testreszabása
Előre elkészített tartományok általános lettek kialakítva. Sok leképezések és entitások, választhat egy alkalmazást az igényeinek megfelelően testre tartalmazzák. Ha az előre elkészített teljes tartományban indul el, törölje a leképezések és entitások, amelyhez az alkalmazás használatához nincs szükség. Néhány leképezések vagy entitások hozzáadhatja a készlethez, amely az előre elkészített tartomány már biztosít. Például, ha használja a **események** sport esemény alkalmazások előre elkészített tartomány is sport csoportjai entitások hozzáadni. Indításakor [utterances biztosító](luis-how-to-add-example-utterances.md) LUIS, az alkalmazás adott kifejezések tartalmaznia. LUIS megtanulja ismeri fel őket, és az előre elkészített tartomány leképezések és az alkalmazás igényeinek megfelelően entitások megfigyelt. 

> [!TIP]
> A leképezések és entitások egy előre elkészített tartományban működnek együtt a leghatékonyabban. Érdemes kombinálását leképezések és azonos tartományból származzon lehetőség entitások.
> * Az ajánlott eljárás, hogy használja a kapcsolódó leképezések ugyanahhoz a tartományhoz. Például, ha testreszabása a `MakeReservation` a leképezési a **helyek** tartományhoz, majd válassza ki a `Cancel` a leképezési a **helyek** helyett a Mégse célt a atartomány**Események** vagy **segédprogramok** tartományok.

## <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Egy előre elkészített tartomány leképezés viselkedésének módosítása
Előfordulhat, hogy egy előre elkészített tartományt szeretne használni az alkalmazás LUIS megjelölésű hasonló megjelölésű tartalmaz, de azt szeretné, hogy eltérően viselkednek. Például a **helyek** előre elkészített tartomány biztosít egy `MakeReservation` leképezésének egy étterem foglalás, de így szeretné, hogy az alkalmazás számára, hogy a cél, hogy a szállodák foglalások használja. Ebben az esetben a beállításait módosíthatja, hogy a leképezés utterances megadásával LUIS segítségével Szálloda helyfoglalás és címkézés őket a használatával a `MakeReservation` leképezési, így LUIS is lehet retrained ismeri fel a `MakeReservation` leképezési foglalható le a szállodák kérelemben .

> [!TIP]
> Tekintse meg az előre elkészített leképezések testre szabható legyen az bármilyen tartomány használható segédprogramok tartományt. Például hozzáadhat `Utilities.Repeat` az alkalmazásra és a vonat azt ismeri fel bármilyen műveletek felhasználó szeretné ismételje meg az alkalmazásban.


## <a name="next-step"></a>Következő lépés

Testre szabhatja egy előre elkészített tartomány további példa utterances hozzáadva.

> [!div class="nextstepaction"]
> [Példa utterances hozzáadása](./luis-how-to-add-example-utterances.md)

## <a name="additional-resources"></a>További források

Tekintse meg a [előre elkészített tartományok hivatkozás](./luis-reference-prebuilt-domains.md) további részleteket az előre elkészített tartományok.
