---
title: Az adathozzáférést biztosító biztonság konfigurálása – Azure Time Series Insights előzetes verzió | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat biztonsági, engedélyezési és kezelési házirendeket az Azure Time Series Insights előzetes verziójának környezetében.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: f49567b8060be2bf2a9ca2b8a1bdee23f58fdd6b
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012688"
---
# <a name="grant-data-access-to-an-environment"></a>Az adathozzáférés biztosítása egy környezethez

Ez a cikk a Azure Time Series Insights előzetes verziójának hozzáférési házirendjeinek két típusát tárgyalja.

## <a name="sign-in-to-time-series-insights"></a>Bejelentkezés Time Series Insights

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com/).
1. Keresse meg Time Series Insights-környezetét. Írja be a `Time Series` **kifejezést a keresőmezőbe** . Válassza az **idősorozat-környezet** lehetőséget a keresési eredmények között.
1. Válassza ki az Azure Time Series Insights környezetet a listából.

## <a name="grant-data-access"></a>Adathozzáférés biztosítása

Az alábbi lépések végrehajtásával biztosíthatja az adathozzáférést egy felhasználói tag számára.

1. Válassza **az adatelérési házirendek**, majd a **+ Hozzáadás**lehetőséget.

    [![Data-access-one](media/data-access/data-access-one.png)](media/data-access/data-access-one.png#lightbox)

1. Válassza a **felhasználó kiválasztása**lehetőséget. Keresse meg a felhasználónevet vagy az e-mail-címet, hogy megkeresse a hozzáadni kívánt felhasználót. Válassza **a kijelölés lehetőséget a** kijelölés megerősítéséhez.

    [![adatelérés – két](media/data-access/data-access-two.png)](media/data-access/data-access-two.png#lightbox)

1. Válassza a **szerepkör kiválasztása**lehetőséget. Válassza ki a megfelelő hozzáférési szerepkört a felhasználó számára:

    * Válassza a **közreműködő** lehetőséget, ha engedélyezni szeretné a felhasználó számára, hogy módosítsa a hivatkozásokat, és megossza a mentett lekérdezéseket és perspektívákat a környezet más felhasználóival.

    * Ellenkező esetben válassza az **olvasó** lehetőséget, ha lehetővé szeretné tenni a felhasználó számára az adatlekérdezést a környezetben, és személyes, nem megosztott, lekérdezéseket menteni a környezetben.

   A szerepkör választásának megerősítéséhez kattintson **az OK gombra** .

    [adat![-hozzáférés – három](media/data-access/data-access-three.png)](media/data-access/data-access-three.png#lightbox)

1. A **felhasználói szerepkör kiválasztása** lapon kattintson az **OK gombra** .

    [![adatelérés – négy](media/data-access/data-access-four.png)](media/data-access/data-access-four.png#lightbox)

1. Győződjön meg arról, hogy az **adatelérési házirendek** lap felsorolja a felhasználókat és az egyes felhasználók szerepköreit.

    [![Data-access-five](media/data-access/data-access-five.png)](media/data-access/data-access-five.png#lightbox)

## <a name="provide-guest-access-from-another-aad-tenant"></a>Vendég hozzáférés biztosítása másik HRE-bérlőtől

`Guest` nem felügyeleti szerepkör. Ez egy olyan fiók, amelyet az egyik bérlőtől a másikba hívnak meg. A vendég fióknak a bérlő címtárba való meghívása után ugyanazzal a hozzáférés-vezérléssel is rendelkezhet, mint bármely más fiók esetében. Time Series Insights-környezethez a Access Control (IAM) panelen adhat felügyeleti hozzáférést. Emellett az adatelérési szabályzatok panelen is megadhat hozzáférést a környezetben található adathoz. A Azure Active Directory (Azure AD) bérlői vendég hozzáférésével kapcsolatos további információkért olvassa el [a Azure Active Directory B2B együttműködési felhasználók hozzáadása a Azure Portal](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Kövesse az alábbi lépéseket, ha vendég hozzáférést szeretne biztosítani egy Time Series Insights-környezethez egy másik bérlő Azure AD-felhasználója számára.

1. Válassza **az adatelérési házirendek**, majd a **+ meghívás**lehetőséget.

    [![Data-access-six](media/data-access/data-access-six.png)](media/data-access/data-access-six.png#lightbox)

1. Adja meg a meghívni kívánt felhasználó e-mail-címét. Ezt az e-mail-címet hozzá kell rendelni az Azure AD-hez. Igény szerint személyes üzenetet is hozzáadhat a meghívóhoz.

    [![Data-access-seven](media/data-access/data-access-seven.png)](media/data-access/data-access-seven.png#lightbox)

1. Keresse meg a képernyőn megjelenő megerősítő buborékot.

    [![Data-access-eight](media/data-access/data-access-eight.png)](media/data-access/data-access-eight.png#lightbox)

1. Válassza a **felhasználó kiválasztása**lehetőséget. Keresse meg a vendég felhasználó e-mail-címét, amelyet a felvenni kívánt felhasználó megtalálására meghívott. Ezután **válassza a lehetőséget** a kijelölés megerősítéséhez.

    [![Data-access-nine](media/data-access/data-access-nine.png)](media/data-access/data-access-nine.png#lightbox)

1. Válassza a **szerepkör kiválasztása**lehetőséget. Válassza ki a megfelelő hozzáférési szerepkört a vendég felhasználó számára:

    * Válassza a **közreműködő** lehetőséget, ha engedélyezni szeretné a felhasználó számára, hogy módosítsa a hivatkozásokat, és megossza a mentett lekérdezéseket és perspektívákat a környezet más felhasználóival.

    * Ellenkező esetben válassza az **olvasó** lehetőséget, ha lehetővé szeretné tenni a felhasználó számára az adatlekérdezést a környezetben, és személyes, nem megosztott, lekérdezéseket menteni a környezetben.

   A szerepkör választásának megerősítéséhez kattintson **az OK gombra** .

    [![Data-access-ten](media/data-access/data-access-ten.png)](media/data-access/data-access-ten.png#lightbox)

1. A **felhasználói szerepkör kiválasztása** lapon kattintson az **OK gombra** .

1. Győződjön meg arról, hogy az **adatelérési házirendek** lap felsorolja a vendég felhasználót és az egyes vendég felhasználók szerepköreit.

    [![Data-access-eleven](media/data-access/data-access-eleven.png)](media/data-access/data-access-eleven.png#lightbox)

1. Most a vendég felhasználónak el kell végeznie az Azure-bérlőben található környezet eléréséhez szükséges lépéseket. Először elfogadják az elküldött meghívót. Ezt a meghívót e-mailben küldjük el az 5. lépésben használt e-mail-címre. Ezek közül választhatnak az első **lépések** elfogadásához.

    [![Data-access-twelve](media/data-access/data-access-twelve.png)](media/data-access/data-access-twelve.png#lightbox)

1. Ezután a vendég felhasználó elfogadja a rendszergazda szervezetéhez társított engedélyeket.

    [adat![-hozzáférés – tizenhárom](media/data-access/data-access-thirteen.png)](media/data-access/data-access-thirteen.png#lightbox)

1. Miután bejelentkezett a vendég felhasználó a meghívni kívánt e-mail-címre, és elfogadják a meghívót, a insights.azure.com. A képernyő jobb felső sarkában az e-mail-cím melletti avatart is kiválaszthatja.

    [![adatelérés – tizennégy](media/data-access/data-access-fourteen.png)](media/data-access/data-access-fourteen.png#lightbox)

1. Ezután a vendég felhasználó kiválasztja az Azure-bérlőt a címtár legördülő menüből. Ezt a bérlőt kell meghívnia.

    [![Data-access-fifteen](media/data-access/data-access-fifteen.png)](media/data-access/data-access-fifteen.png#lightbox)

Miután a vendég felhasználó kiválasztja a bérlőt, láthatják azt a Time Series Insights környezetet, amelyhez hozzáférést biztosított számukra. Most már az **5. lépésben**megadott szerepkörhöz tartozó összes képességgel rendelkeznek.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg [, hogyan adhat hozzá Azure Event Hubs eseményforrás](./time-series-insights-how-to-add-an-event-source-eventhub.md) a Time Series Insights-környezethez.

* [Események küldése az esemény forrásának](./time-series-insights-send-events.md).

* Tekintse [meg a környezetet a Time Series Insights Preview Explorerben](./time-series-insights-update-explorer.md).
