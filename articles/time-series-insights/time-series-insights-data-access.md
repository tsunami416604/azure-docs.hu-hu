---
title: Az Azure Time Series Insights előzetes verziója biztonsági - Configure security eléréséhez és kezeléséhez az Azure Time Series Insights – előzetes |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan konfigurálhat egy felügyeleti hozzáférés és az engedélyek szabályzatok és az adat-hozzáférési szabályzatokkal is gondoskodhat az Azure Time Series Insights előzetes verziója.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/26/2018
ms.custom: seodec18
ms.openlocfilehash: 7f8eb66feecd58b766b3414b1bbc6bd4e27bf4f7
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53275465"
---
# <a name="grant-data-access-to-an-environment"></a>Adathozzáférés-környezetben

Ez a cikk ismerteti az Azure Time Series Insights – előzetes hozzáférési házirendek két típusú.

## <a name="grant-data-access"></a>Adathozzáférés biztosítása

Adathozzáférés egy felhasználó rendszerbiztonsági tag az alábbi lépéseket követve.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Keresse meg a Time Series Insights-környezet. Adja meg `Time Series` a a **keresési** mezőbe. Válassza ki **Time Series Environment** a keresési eredmények között.
1. Válassza ki az Azure Time Series Insights környezetet a listából.
1. Válassza ki **az adathozzáférési házirendek**, majd válassza ki **+ Hozzáadás**.

    ![adat-hozzáférési-egy][1]

1. Válasszon **felhasználó kiválasztása**. Keresse meg a felhasználói név vagy e-mail cím a hozzáadni kívánt felhasználó található. Kattintson a **kiválasztása** a kijelölés megerősítéséhez.

    ![a második hozzáférési adatok][2]

1. Válasszon **szerepkör kiválasztása**. Válassza ki a felhasználó a megfelelő hozzáférés-szerepkör:

    * Válassza ki **közreműködői** szeretné-e, hogy a felhasználó módosíthatja a referenciaadatok és megosztás a mentett lekérdezéseket és perspektívákat a környezet más felhasználóival.

    * Ellenkező esetben válassza **olvasó** használatával adatokat lekérdezni a felhasználó engedélyezi a környezetben, és a személyes, nem megosztott lekérdezések mentéséhez a környezetben.

   Válassza ki **OK** szerepkör kiválasztásának megerősítéséhez.

    ![adat-hozzáférési-három][3]

1. Válassza ki **OK** a a **felhasználói szerepkör kiválasztása** lapot.

    ![adat-hozzáférési-négy][4]

1. Ellenőrizze, hogy a **az adathozzáférési házirendek** lap felsorolja a felhasználók és a szerepkörök minden felhasználó számára.

    ![Adat-hozzáférési-öt][5]

## <a name="provide-guest-access-to-a-user-from-another-azure-active-directory-tenant"></a>Vendég-elérést biztosíthat a felhasználók egy másik Azure Active Directory-bérlő

`Guest` felügyeleti szerepkör nem. Egy kifejezés, amely egyetlen bérlő felkérik, hogy egy másik. Miután a Vendég fiók a bérlő címtárba felkérik, ugyanazt a hozzáférés-vezérlést alkalmazni, mint bármely más rendelkezhet. Felügyeleti hozzáférés a hozzáférés-vezérlés (IAM) paneljén a Time Series Insights-környezetet biztosíthat. Vagy az adathozzáférési házirendek panelen keresztül biztosíthat hozzáférést az adatokhoz a környezetben. További információk az Azure Active Directory (Azure AD) bérlő vendéghozzáférés, [hozzáadása az Azure Active Directory B2B együttműködési felhasználókat az Azure Portalon](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Vendég hozzáférést biztosítani más bérlők egy Azure AD-felhasználót a Time Series Insights-környezet az alábbi lépéseket követve.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Keresse meg a Time Series Insights-környezet. Adja meg **Time Series** a a **keresési** mezőbe. Válassza ki **Time Series Environment** a keresési eredmények között.
1. Válassza ki az Azure Time Series Insights környezetet a listából.
1. Válassza ki **az adathozzáférési házirendek**, majd válassza ki **+ meghívása**.

    ![adat-hozzáférési-hat][6]

1. Adja meg az e-mail-cím a meghívni kívánt felhasználó. Ez az e-mail cím társítva az Azure ad-vel kell lennie. Opcionálisan megadhat egy személyes üzenetet a meghíváshoz.

    ![adat-hozzáférési-hét][7]

1. Keresse meg a megerősítő buborékra a képernyőn megjelenő.

    ![adat-hozzáférési-nyolc][8]

1. Válasszon **felhasználó kiválasztása**. Keresse meg a meghívót, keresse meg a hozzáadni kívánt felhasználó vendégfelhasználó e-mail-címét. Kattintson a **kiválasztása** a kijelölés megerősítéséhez.

    ![adat-hozzáférési-kilenc][9]

1. Válasszon **szerepkör kiválasztása**. Válassza ki a megfelelő hozzáférési szerepkört, a Vendég felhasználó:

    * Válassza ki **közreműködői** szeretné-e, hogy a felhasználó módosíthatja a referenciaadatok és megosztás a mentett lekérdezéseket és perspektívákat a környezet más felhasználóival.

    * Ellenkező esetben válassza **olvasó** használatával adatokat lekérdezni a felhasználó engedélyezi a környezetben, és a személyes, nem megosztott lekérdezések mentéséhez a környezetben.

   Válassza ki **OK** szerepkör kiválasztásának megerősítéséhez.

    ![adat-hozzáférési-tíz][10]

1. Válassza ki **OK** a a **felhasználói szerepkör kiválasztása** lapot.

1. Ellenőrizze, hogy a **az adathozzáférési házirendek** lap felsorolja a Vendég felhasználó és minden vendégfelhasználó szerepköreit.

    ![adat-hozzáférési tizenegy][11]

1. Most a Vendég felhasználó férhet hozzá a környezethez, az Azure-bérlő, amelyhez meghívót őket található lépéseket kell követnie. Először is, fogadja el a meghívást küldött őket. Ezt a meghívót az 5. lépésben használt e-mail-címre küldött e-mailen keresztül. Kiválasztják **Ismerkedés** fogadására.

    ![adat-hozzáférési-tizenkét][12]

1. Ezután a vendégfelhasználó fogadja el a rendszergazda szervezeti tartozó engedélyeket.

    ![adat-hozzáférési tizenhárom][13]

1. Miután bejelentkezett a Vendég felhasználó, hogy felkínáljuk számukra, használt e-mail címre, és azok fogadja el a meghívást, insights.azure.com való. Egyszer, akkor jelölje ki a profilképet, az e-mail-cím mellett a képernyő jobb felső sarkában.

    ![adat-hozzáférési-tizennégy][14]

1. A következő, a Vendég felhasználó által az Azure-bérlőhöz a directory legördülő menüből. Ezt a bérlőt, amelyhez meghívót őket lesz.

    ![adat-hozzáférési-tizenöt][15]

Után a vendégfelhasználót a bérlőhöz választja, a Time Series Insights-környezet, amelyhez a megadott őket hozzáférés meg. Most már a 8. lépésben megadott azokat a szerepkörhöz társított összes funkciót.

## <a name="next-steps"></a>További lépések

* Ismerje meg, [hozzáadása az Azure Event Hubs eseményforrás](./time-series-insights-how-to-add-an-event-source-eventhub.md) a Time Series Insights-környezetbe.
* Küldés [az eseményforrás események](./time-series-insights-send-events.md).
* Nézet [a Time Series Insights előzetes verziója Explorer környezetében](./time-series-insights-update-explorer.md).

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