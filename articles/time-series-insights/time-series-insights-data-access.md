---
title: A biztonság konfigurálása az adathozzáférés biztosításához - Azure Time Series Insights Preview | Microsoft dokumentumok
description: Ismerje meg, hogyan konfigurálhatja a biztonságot, az engedélyeket és kezelheti az adatelérési szabályzatokat az Azure Time Series Insights előzetes környezetében.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/10/2020
ms.custom: seodec18
ms.openlocfilehash: 1c8f14bb1bca082a9d887e5d6d88aec213448c3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254351"
---
# <a name="grant-data-access-to-an-environment"></a>Adathozzáférés megadása környezethez

Ez a cikk ismerteti a kétféle Azure Time Series Insights előzetes hozzáférési szabályzatok.

> [!TIP]
> Olvassa el az Azure Active Directory alkalmazás regisztrációs lépéseinek [hitelesítési és engedélyezési](time-series-insights-authentication-and-authorization.md) lépéseit.

## <a name="sign-in-to-time-series-insights"></a>Bejelentkezés a Time Series Insights-ba

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
1. Keresse meg a Time Series Insights környezetben. Írja `Time Series` be a **Keresőmezőbe.** A keresési eredmények között válassza a **Time Series Environment lehetőséget.**
1. Válassza ki az Azure Time Series Insights környezetet a listából.

## <a name="grant-data-access"></a>Adathozzáférés biztosítása

Az alábbi lépésekkel biztosíthat adathozzáférést egy egyszerű felhasználó számára.

1. Válassza **az Adatelérési házirendek**lehetőséget, majd a **+ Add**lehetőséget.

    [![Adatelérési házirend kijelölése és hozzáadása](media/data-access/data-access-select-add-button.png)](media/data-access/data-access-select-add-button.png#lightbox)

1. Válassza **a Felhasználó kiválasztása**lehetőséget. Keresse meg a felhasználónevet vagy az e-mail címet a hozzáadni kívánt felhasználó megkereséséhez. A kijelölés megerősítéséhez válassza a **Kijelölés** lehetőséget.

    [![A hozzáadni kívánt felhasználó kiválasztása](media/data-access/data-access-select-user-to-confirm.png)](media/data-access/data-access-select-user-to-confirm.png#lightbox)

1. Válassza **a Szerepkör kiválasztása lehetőséget.** Válassza ki a felhasználó számára megfelelő hozzáférési szerepkört:

    * Válassza **a Közreműködő** lehetőséget, ha engedélyezni szeretné, hogy a felhasználó módosítsa a referenciaadatokat, és megossza a mentett lekérdezéseket és perspektívákat a környezet többi felhasználójával.

    * Ellenkező esetben válassza a **Reader** lehetőséget, hogy a felhasználó lekérdezhesse az adatokat a környezetben, és személyes, nem megosztott lekérdezéseket menthesen a környezetben.

   A szerepkör választásának megerősítéséhez válassza az **OK gombot.**

    [![A kijelölt szerepkör megerősítése](media/data-access/data-access-select-a-role.png)](media/data-access/data-access-select-a-role.png#lightbox)

1. A Felhasználói **szerepkör kiválasztása** lapon válassza az **OK** gombot.

    [![A Felhasználói szerepkör kiválasztása lapon válassza az OK lehetőséget.](media/data-access/data-access-confirm-user-and-role.png)](media/data-access/data-access-confirm-user-and-role.png#lightbox)

1. Ellenőrizze, hogy az **Adatelérési házirendek** lap felsorolja-e az egyes felhasználók felhasználóit és szerepköreit.

    [![A megfelelő felhasználók és szerepkörök ellenőrzése](media/data-access/data-access-verify-and-confirm-assignments.png)](media/data-access/data-access-verify-and-confirm-assignments.png#lightbox)

## <a name="provide-guest-access-from-another-azure-ad-tenant"></a>Vendéghozzáférés biztosítása egy másik Azure AD-bérlőről

A `Guest` szerepkör nem felügyeleti szerepkör. Ez egy olyan kifejezés, amelyet egy olyan fiókra használnak, amelyet egyik bérlőtől a másikig hívnak meg. Miután a vendégfiók meghívást kapott a bérlő könyvtárába, ugyanazt a hozzáférés-vezérlést alkalmazhatja rá, mint bármely más fiókot. Felügyeleti hozzáférést biztosíthat a Time Series Insights-környezethez a hozzáférés-vezérlés (IAM) panel használatával. Vagy hozzáférést biztosíthat a környezetben lévő adatokhoz az Adatelérési házirendek panelen keresztül. Az Azure Active Directory (Azure AD) bérlői vendéghozzáférésével kapcsolatos további információkért olvassa el [az Azure Active Directory B2B együttműködési felhasználóinak hozzáadása az Azure Portalon](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)című olvasnivalót.

Kövesse az alábbi lépéseket, hogy a time series insights-környezethez egy Time Series Insights-környezethez egy másik bérlőből származó Azure AD-felhasználó számára hozzáférést biztosítson.

1. Válassza **az Adatelérési házirendek**lehetőséget, majd a **+ Meghívás**lehetőséget.

    [![Válassza az Adatelérési rendőrség, majd a + Meghívás lehetőséget](media/data-access/data-access-invite-another-aad-tenant.png)](media/data-access/data-access-invite-another-aad-tenant.png#lightbox)

1. Adja meg a meghívni kívánt felhasználó e-mail címét. Ezt az e-mail címet az Azure AD-hez kell társtársosan kezelni. A meghívóhoz személyes üzenetet is felvehet.

    [![Adja meg az e-mail címet a kijelölt felhasználó megkereséséhez](media/data-access/data-access-invite-guest-by-email.png)](media/data-access/data-access-invite-guest-by-email.png#lightbox)

1. Keresse meg a képernyőn megjelenő megerősítő buborékot.

    [![Keresse meg a megerősítő buborékot](media/data-access/data-access-confirmation-bubble.png)](media/data-access/data-access-confirmation-bubble.png#lightbox)

1. Válassza **a Felhasználó kiválasztása**lehetőséget. Keresse meg a meghívott vendégfelhasználó e-mail címét, hogy megkeresse a hozzáadni kívánt felhasználót. Ezután **jelölje be** a kijelölés megerősítéséhez.

    [![Jelölje ki a felhasználót, és erősítse meg a kijelölést](media/data-access/data-access-select-invited-person-confirmation.png)](media/data-access/data-access-select-invited-person-confirmation.png#lightbox)

1. Válassza **a Szerepkör kiválasztása lehetőséget.** Válassza ki a vendégfelhasználó számára a megfelelő hozzáférési szerepkört:

    * Válassza **a Közreműködő** lehetőséget, ha engedélyezni szeretné, hogy a felhasználó módosítsa a referenciaadatokat, és megossza a mentett lekérdezéseket és perspektívákat a környezet többi felhasználójával.

    * Ellenkező esetben válassza a **Reader** lehetőséget, hogy a felhasználó lekérdezhesse az adatokat a környezetben, és személyes, nem megosztott lekérdezéseket menthesen a környezetben.

   A szerepkör választásának megerősítéséhez válassza az **OK gombot.**

    [![A szerepkör választásának megerősítése](media/data-access/data-access-select-ok-and-confirm.png)](media/data-access/data-access-select-ok-and-confirm.png#lightbox)

1. A Felhasználói **szerepkör kiválasztása** lapon válassza az **OK** gombot.

1. Ellenőrizze, hogy az **Adatelérési házirendek** lap felsorolja-e a vendégfelhasználót és az egyes vendégfelhasználók szerepköreit.

    [![A felhasználók és szerepkörök megfelelő hozzárendelésének ellenőrzése](media/data-access/data-access-confirm-invited-users-and-roles.png)](media/data-access/data-access-confirm-invited-users-and-roles.png#lightbox)

1. Most a vendég felhasználó kap egy meghívó e-mailt a fent megadott e-mail címre. A vendégfelhasználó az **Első lépések** lehetőséget választja az elfogadás megerősítéséhez és az Azure Cloudhoz való csatlakozáshoz.

    [![A vendég az Első lépések lehetőséget választja az elfogadáshoz](media/data-access/data-access-email-invitation.png)](media/data-access/data-access-email-invitation.png#lightbox)

1. Az **Első lépések**kiválasztása után a vendégfelhasználó megjelenik a rendszergazda szervezetéhez társított engedélymezővel. Az **Elfogadás**lehetőség kiválasztásával az engedély megadásával be lesznek jelentkezve.

    [![A vendég áttekinti az engedélyeket, és elfogadja](media/data-access/data-access-grant-permission-sign-in.png)](media/data-access/data-access-grant-permission-sign-in.png#lightbox)

1. A rendszergazda [megosztja a környezet URL-címét](time-series-insights-parameterized-urls.md) a vendégével.

1. Miután a vendégfelhasználó be van jelentkezve a meghíváshoz használt e-mail címre, és elfogadják a meghívást, a rendszer az Azure Portalra irányítja őket. 

1. A vendég most már hozzáférhet a megosztott környezethez a rendszergazda által biztosított környezeti URL-cím használatával. Az azonnali hozzáférés hez beírhatják ezt az URL-t a webböngészőjükbe.

1. A rendszergazda bérlője megjelenik a vendégfelhasználó számára, miután kiválasztotta a profil ikonját a Time Series explorer jobb felső sarkában.

    [![Avatar kiválasztása insights.azure.com](media/data-access/data-access-select-tenant-and-instance.png)](media/data-access/data-access-select-tenant-and-instance.png#lightbox)


    Miután a vendégfelhasználó kiválasztja a rendszergazda bérlőjét, kiválaszthatja a megosztott Time Series Insights környezetet. 
    
    Most már rendelkeznek az **5.**

    [![A vendégfelhasználó legördülő menüből választja ki az Azure-bérlőt](media/data-access/data-access-all-capabilities.png)](media/data-access/data-access-all-capabilities.png#lightbox)

## <a name="next-steps"></a>További lépések

* Ismerje [meg, hogyan adhat hozzá egy Azure Event Hubs-eseményforrást](./time-series-insights-how-to-add-an-event-source-eventhub.md) a Time Series Insights-környezethez.

* Események küldése [az eseményforrásra.](./time-series-insights-send-events.md)

* A [Time Series Insights előzetes verziójának kezelője a környezetét tekintheti meg.](./time-series-insights-update-explorer.md)
