---
title: Eléréséhez és kezeléséhez az Azure Time Series Insights biztonságának konfigurálása |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan konfigurálhat egy felügyeleti hozzáférés és az engedélyek szabályzatok és az adat-hozzáférési szabályzatok az Azure Time Series Insights secure.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 3694ff0db63d685cb63f586062158b4f8acac5cf
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52847609"
---
# <a name="grant-data-access-to-an-environment"></a>Adathozzáférés-környezetben

Ez a cikk ismerteti az Azure Time Series Insights (előzetes verzió) hozzáférési házirendek két típusú.

## <a name="grant-data-access"></a>Adathozzáférés biztosítása

Adathozzáférés egy felhasználó rendszerbiztonsági tag az alábbi lépéseket követve:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/).
1. Keresse meg a Time Series Insights (TSI) környezetben. Típus `Time Series` a a **keresési** mezőbe. Válassza ki **Time Series Environment** a keresési eredmények között.
1. Válassza ki a TSI-környezetet a listából.
1. Válassza ki **az adathozzáférési házirendek**, majd **+ Hozzáadás**.

    ![adat-hozzáférési-egy][1]

1. Válassza ki **felhasználó kiválasztása**. Keresse meg a felhasználói név vagy e-mail cím a hozzáadni kívánt felhasználó található. Kattintson a **kiválasztása** a kijelölés megerősítéséhez.

    ![a második hozzáférési adatok][2]

1. Válassza ki **szerepkör kiválasztása**. Válassza ki a felhasználó a megfelelő hozzáférés-szerepkör:

    * Válassza ki **közreműködői** szeretné-e, hogy a felhasználó módosíthatja a referenciaadatok és megosztás a mentett lekérdezéseket és perspektívákat a környezet más felhasználóival.

    * Ellenkező esetben válassza **olvasó** lehetővé teszik a felhasználói adatokat lekérdezni a környezetben, és személyes (nem megosztott) lekérdezéseket mentéséhez a környezetben.

    Válassza ki **Ok** szerepkör kiválasztásának megerősítéséhez.

    ![adat-hozzáférési-három][3]

1. Válassza ki **Ok** a a **felhasználói szerepkör kiválasztása** lapot.

    ![adat-hozzáférési-négy][4]

1. A **az adathozzáférési házirendek** lap felsorolja a felhasználók és a szerepkör minden felhasználóhoz.

    ! [adatok-access-öt[5]

## <a name="provide-guest-access-to-a-user-from-another-azure-active-directory-tenant"></a>Vendég-elérést biztosíthat a felhasználók egy másik Azure Active Directory-bérlő

`Guest` felügyeleti szerepkör; nem egy kifejezés, amely van meghívót kapott a következőként egyik bérlőből a másikba. Miután a Vendég fiók a bérlő címtárba kapott meghívót, veheti fel a azonos hozzáférés-vezérlést, mint bármely más, vagy felügyeleti hozzáférési jogot a TSI-környezet a hozzáférés-vezérlés (IAM) paneljén, vagy hozzáférést biztosítani az adatokhoz a alkalmazni a környezet az adathozzáférési házirendek panelen keresztül. További információk az Azure Active Directory (AAD) bérlői vendéghozzáférés, [hozzáadása az Azure Active Directory B2B együttműködési felhasználókat az Azure Portalon](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Vendég hozzáférést biztosítani más bérlők egy AAD-felhasználót a TSI-környezet az alábbi lépéseket követve:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Keresse meg a TSI-környezetben. Típus **Time Series** kifejezést a keresőmezőbe. Válassza ki **Time Series Environment** a keresési eredmények között.
1. Válassza ki a TSI-környezetet a listából.
1. Válassza ki **az adathozzáférési házirendek**, majd **+ meghívása**.

    ![adat-hozzáférési-hat][6]

1. Adja meg a felhasználó e-mail címe, akinél szeretné meghívni. Vegye figyelembe, hogy ez legyen az aad-vel kapcsolatos e-mailt. Opcionálisan megadhat egy személyes üzenetet a meghíváshoz.

    ![adat-hozzáférési-hét][7]

1. Megjelenik egy megerősítő buborékra a képernyőn jelennek meg.

    ![adat-hozzáférési-nyolc][8]

1. Válassza ki **felhasználó kiválasztása**. Keresse meg a vendégfelhasználót csak meghívót, keresse meg a hozzáadni kívánt felhasználó e-mail-címét. Kattintson a **kiválasztása** a kijelölés megerősítéséhez.

    ![adat-hozzáférési-kilenc][9]

1. Válassza ki **kiválasztása** szerepkör. Válassza ki a megfelelő hozzáférési szerepkört, a Vendég felhasználó:

    1. Válassza ki **közreműködői** szeretné-e, hogy a felhasználó módosíthatja a referenciaadatok és megosztás a mentett lekérdezéseket és perspektívákat a környezet más felhasználóival.
    1. Ellenkező esetben válassza **olvasó** lehetővé teszik a felhasználói adatokat lekérdezni a környezetben, és személyes (nem megosztott) lekérdezéseket mentéséhez a környezetben.
    1. Válassza ki **Ok** szerepkör kiválasztásának megerősítéséhez.

    ![adat-hozzáférési-tíz][10]

1. Válassza ki **Ok** a a **felhasználói szerepkör kiválasztása** lapot.

1. A **az adathozzáférési házirendek** most már a lap felsorolja a vendégfelhasználót és az egyes vendég felhasználók a szerepkört.

    ![adat-hozzáférési tizenegy][11]

1. Most a vendégfelhasználó elvégzendő lépéseket eléréséhez az Azure-ban található a környezetben csak bérlői meg kell hívni őket. Először is szükségük fogadja el a meghívást, az imént elküldött. Ezt a meghívót az 5. lépésében meghívót címre küldött e-mailen keresztül. Érdemes kattintanak **Ismerkedés**, hogy fogadja el.

    ![adat-hozzáférési-tizenkét][12]

1. Ezután a vendégfelhasználó kell fogadja el a rendszergazda szervezeti tartozó engedélyeket.

    ![adat-hozzáférési tizenhárom][13]

1. Ha a Vendég felhasználó jelentkezik be az e-mail-cím meghívót, és, fogadja el a meghívást, azok insights.azure.com címének lesz. Egyszer, fog gombra kell kattintani a profilképet, az a képernyő jobb felső sarkában lévő e-mailek mellett az.

    ![adat-hozzáférési-tizennégy][14]

1. Ezután válassza ki a meghívott felhasználónak az Azure bérlő a directory legördülő menüből. Ez az a bérlőt, amelyhez a meghívót őket.

    ![adat-hozzáférési-tizenöt][15]

1. Végül amikor a vendégfelhasználót a bérlőhöz választja, akkor fogja látni a TSI-környezet, amely csak a megadott őket a hozzáférést. Most kell rendelkezniük a 8. lépésében megadott szerepkörhöz társított összes funkciót.

## <a name="next-steps"></a>További lépések

* Ismerje meg, [Event Hub-eseményforrás hozzáadása](./time-series-insights-how-to-add-an-event-source-eventhub.md) az Azure TSI-környezethez.
* Küldés [az eseményforrás események](./time-series-insights-send-events.md).
* Nézet [Time Series Insights Explorer környezetében](./time-series-insights-update-explorer.md).

<!-- Images -->
[1]: media/data-access/data-access-one.png
[2]: media/data-access/data-access-two.png
[3]: media/data-access/data-access-three.png
[4]: media/data-access/data-access-four.png
[5]: media/data-access/data-access-five.png
[6]: media/data-access/data-access-six.png
[7]: media/data-access/data-access-seven.png
[8]: media/data-access/data-access-eight.png
[9]: media/data-access/data-access-nine.png
[10]: media/data-access/data-access-ten.png
[11]: media/data-access/data-access-eleven.png
[12]: media/data-access/data-access-twelve.png
[13]: media/data-access/data-access-thirteen.png
[14]: media/data-access/data-access-fourteen.png
[15]: media/data-access/data-access-fifteen.png