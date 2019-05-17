---
title: Konfigurálja a biztonsági eléréséhez és kezeléséhez az Azure Time Series Insights – előzetes |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan konfigurálhat egy felügyeleti hozzáférés és az engedélyek szabályzatok és az adat-hozzáférési szabályzatokkal is gondoskodhat az Azure Time Series Insights előzetes verziója.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/01/2019
ms.custom: seodec18
ms.openlocfilehash: 6853637ba23e17f3a7ca5420bdd84425c81a67be
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65791022"
---
# <a name="grant-data-access-to-an-environment"></a>Adathozzáférés-környezetben

Ez a cikk ismerteti az Azure Time Series Insights – előzetes hozzáférési házirendek két típusú.

## <a name="sign-in-to-time-series-insights"></a>Jelentkezzen be a Time Series Insights

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Keresse meg a Time Series Insights-környezet. Adja meg `Time Series` a a **keresési** mezőbe. Válassza ki **Time Series Environment** a keresési eredmények között.
1. Válassza ki az Azure Time Series Insights környezetet a listából.

## <a name="grant-data-access"></a>Adathozzáférés biztosítása

Adathozzáférés egy felhasználó rendszerbiztonsági tag az alábbi lépéseket követve.

1. Válassza ki **az adathozzáférési házirendek**, majd válassza ki **+ Hozzáadás**.

    [![Data-access-one](media/data-access/data-access-one.png)](media/data-access/data-access-one.png#lightbox)

1. Válasszon **felhasználó kiválasztása**. Keresse meg a felhasználói név vagy e-mail cím a hozzáadni kívánt felhasználó található. Kattintson a **kiválasztása** a kijelölés megerősítéséhez.

    [![Data-access-two](media/data-access/data-access-two.png)](media/data-access/data-access-two.png#lightbox)

1. Válasszon **szerepkör kiválasztása**. Válassza ki a felhasználó a megfelelő hozzáférés-szerepkör:

    * Válassza ki **közreműködői** szeretné-e, hogy a felhasználó módosíthatja a referenciaadatok és megosztás a mentett lekérdezéseket és perspektívákat a környezet más felhasználóival.

    * Ellenkező esetben válassza **olvasó** használatával adatokat lekérdezni a felhasználó engedélyezi a környezetben, és a személyes, nem megosztott lekérdezések mentéséhez a környezetben.

   Válassza ki **OK** szerepkör kiválasztásának megerősítéséhez.

    [![adat-hozzáférési-három](media/data-access/data-access-three.png)](media/data-access/data-access-three.png#lightbox)

1. Válassza ki **OK** a a **felhasználói szerepkör kiválasztása** lapot.

    [![adat-hozzáférési-négy](media/data-access/data-access-four.png)](media/data-access/data-access-four.png#lightbox)

1. Ellenőrizze, hogy a **az adathozzáférési házirendek** lap felsorolja a felhasználók és a szerepkörök minden felhasználó számára.

    [![Data-access-five](media/data-access/data-access-five.png)](media/data-access/data-access-five.png#lightbox)

## <a name="provide-guest-access-from-another-aad-tenant"></a>Adja meg a vendéghozzáférés az AAD-bérlő

`Guest` felügyeleti szerepkör nem. Egy kifejezés, amely egyetlen bérlő felkérik, hogy egy másik. Miután a Vendég fiók a bérlő címtárba felkérik, ugyanazt a hozzáférés-vezérlést alkalmazni, mint bármely más rendelkezhet. Felügyeleti hozzáférés a hozzáférés-vezérlés (IAM) paneljén a Time Series Insights-környezetet biztosíthat. Vagy az adathozzáférési házirendek panelen keresztül biztosíthat hozzáférést az adatokhoz a környezetben. További információk az Azure Active Directory (Azure AD) bérlő vendéghozzáférés, [hozzáadása az Azure Active Directory B2B együttműködési felhasználókat az Azure Portalon](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Vendég hozzáférést biztosítani más bérlők egy Azure AD-felhasználót a Time Series Insights-környezet az alábbi lépéseket követve.

1. Válassza ki **az adathozzáférési házirendek**, majd válassza ki **+ meghívása**.

    [![Data-access-six](media/data-access/data-access-six.png)](media/data-access/data-access-six.png#lightbox)

1. Adja meg az e-mail-cím a meghívni kívánt felhasználó. Ez az e-mail cím társítva az Azure ad-vel kell lennie. Opcionálisan megadhat egy személyes üzenetet a meghíváshoz.

    [![Data-access-seven](media/data-access/data-access-seven.png)](media/data-access/data-access-seven.png#lightbox)

1. Keresse meg a megerősítő buborékra a képernyőn megjelenő.

    [![Data-access-eight](media/data-access/data-access-eight.png)](media/data-access/data-access-eight.png#lightbox)

1. Válasszon **felhasználó kiválasztása**. Keresse meg a meghívót, keresse meg a hozzáadni kívánt felhasználó vendégfelhasználó e-mail-címét. Kattintson a **kiválasztása** a kijelölés megerősítéséhez.

    [![Data-access-nine](media/data-access/data-access-nine.png)](media/data-access/data-access-nine.png#lightbox)

1. Válasszon **szerepkör kiválasztása**. Válassza ki a megfelelő hozzáférési szerepkört, a Vendég felhasználó:

    * Válassza ki **közreműködői** szeretné-e, hogy a felhasználó módosíthatja a referenciaadatok és megosztás a mentett lekérdezéseket és perspektívákat a környezet más felhasználóival.

    * Ellenkező esetben válassza **olvasó** használatával adatokat lekérdezni a felhasználó engedélyezi a környezetben, és a személyes, nem megosztott lekérdezések mentéséhez a környezetben.

   Válassza ki **OK** szerepkör kiválasztásának megerősítéséhez.

    [![Data-access-ten](media/data-access/data-access-ten.png)](media/data-access/data-access-ten.png#lightbox)

1. Válassza ki **OK** a a **felhasználói szerepkör kiválasztása** lapot.

1. Ellenőrizze, hogy a **az adathozzáférési házirendek** lap felsorolja a Vendég felhasználó és minden vendégfelhasználó szerepköreit.

    [![Data-access-eleven](media/data-access/data-access-eleven.png)](media/data-access/data-access-eleven.png#lightbox)

1. Most a Vendég felhasználó férhet hozzá a környezethez, az Azure-bérlő, amelyhez meghívót őket található lépéseket kell követnie. Először is, fogadja el a meghívást küldött őket. Ezt a meghívót az 5. lépésben használt e-mail-címre küldött e-mailen keresztül. Kiválasztják **Ismerkedés** fogadására.

    [![Data-access-twelve](media/data-access/data-access-twelve.png)](media/data-access/data-access-twelve.png#lightbox)

1. Ezután a vendégfelhasználó fogadja el a rendszergazda szervezeti tartozó engedélyeket.

    [![Data-access-thirteen](media/data-access/data-access-thirteen.png)](media/data-access/data-access-thirteen.png#lightbox)

1. Miután bejelentkezett a Vendég felhasználó, hogy felkínáljuk számukra, használt e-mail címre, és azok fogadja el a meghívást, insights.azure.com való. Egyszer, akkor jelölje ki a profilképet, az e-mail-cím mellett a képernyő jobb felső sarkában.

    [![Data-access-fourteen](media/data-access/data-access-fourteen.png)](media/data-access/data-access-fourteen.png#lightbox)

1. A következő, a Vendég felhasználó által az Azure-bérlőhöz a directory legördülő menüből. Ezt a bérlőt, amelyhez meghívót őket lesz.

    [![Data-access-fifteen](media/data-access/data-access-fifteen.png)](media/data-access/data-access-fifteen.png#lightbox)

Után a vendégfelhasználót a bérlőhöz választja, a Time Series Insights-környezet, amelyhez a megadott őket hozzáférés meg. Most már minden olyan funkciót, a megadott őket a szerepkörhöz társított **5. lépés**.

## <a name="next-steps"></a>További lépések

* Ismerje meg, [hozzáadása az Azure Event Hubs eseményforrás](./time-series-insights-how-to-add-an-event-source-eventhub.md) a Time Series Insights-környezetbe.

* Küldés [az eseményforrás események](./time-series-insights-send-events.md).

* Nézet [a Time Series Insights előzetes verziója Explorer környezetében](./time-series-insights-update-explorer.md).
