---
title: Együttműködés másokkal – LUIS
titleSuffix: Azure Cognitive Services
description: Az alkalmazás tulajdonosa hozzáadhat közreműködőket a szerzői erőforráshoz. Ezek a közreműködők módosíthatják a modellt, betanítják és közzétehetik az alkalmazást.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 15c7c6664b0c17d02f1f4e9120c9509fda21e0f4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467529"
---
# <a name="add-contributors-to-your-app"></a>Közreműködők hozzáadása az alkalmazáshoz

Az alkalmazások tulajdonosai hozzáadhatnak közreműködőket az alkalmazásokhoz. Ezek a közreműködők módosíthatják a modellt, betanítják és közzétehetik az alkalmazást. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

[!INCLUDE [Manage collaborators and contributors based on migrated or not-migrated apps](./includes/manage-contributor-collaborator-migration.md)]

## <a name="add-contributor-to-azure-authoring-resource"></a>Közreműködő hozzáadása az Azure authoring resourcehez

Az alábbi eljárás az Azure authoring Resource használatára **áttelepített** összes felhasználóra vonatkozik.

Áttelepítette, ha a LUIS authoring Experience egy authoring-erőforráshoz van kötve a LUIS **-portál Manage-> Azure-erőforrások** lapján.

1. A Azure Portal keresse meg a Language Understanding (LUIS) authoring erőforrást. A típus `LUIS.Authoring`.
1. Az erőforrás **Access Control (iam)** lapon válassza a **+ Hozzáadás** lehetőséget, majd válassza a **szerepkör-hozzárendelés hozzáadása**elemet.

    ![A Azure Portalban vegyen fel szerepkör-hozzárendelést az erőforrás-létrehozáshoz.](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. A **szerepkör-hozzárendelés hozzáadása** ablakban válassza ki a közreműködő **szerepkört** . A **hozzáférés kiosztása** beállításnál válassza az **Azure ad-felhasználó,-csoport vagy egyszerű szolgáltatásnév**lehetőséget. A **Select (kiválasztás** ) lehetőségnél adja meg a felhasználó e-mail-címét. Ha a felhasználó neve több mint 1 e-mail-cím, akkor győződjön meg arról, hogy az _elsődleges_ e-mail-fiók.

    ![Felhasználó e-mail-címének hozzáadása az Azure AD közreműködői szerepköréhez](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    Ha a felhasználó e-mail-címe megtalálható, válassza ki a fiókot, és válassza a **Mentés**lehetőséget. 

    Ha problémája van a szerepkör-hozzárendeléssel, tekintse át az [Azure szerepkör-hozzárendeléseket](../../role-based-access-control/role-assignments-portal.md) és az [Azure hozzáférés-vezérléssel kapcsolatos hibaelhárítást](../../role-based-access-control/troubleshooting.md#problems-with-rbac-role-assignments).

## <a name="add-collaborator-to-luis-app"></a>Közreműködő hozzáadása a LUIS-alkalmazáshoz

Az alábbi eljárás az összes olyan felhasználóra vonatkozik, akik nem lettek **áttelepítve** az Azure authoring Resource használatára.

Nem telepítette át, ha a LUIS authoring Experience nem kötődik egy authoring-erőforráshoz a LUIS **-portál Manage-> Azure-erőforrások** lapján.

Egy alkalmazás egyetlen szerzővel, a tulajdonossal, de több közreműködővel is rendelkezhet. Ha lehetővé szeretné tenni a közreműködők számára a LUIS-alkalmazás szerkesztését, hozzá kell adnia az általuk használt e-mailt a LUIS-portálhoz a közreműködők listájához való hozzáféréshez. A Hozzáadás után az alkalmazás a LUIS-portálon jelenik meg.

1. A jobb felső menüben válassza a **kezelés** lehetőséget, majd a bal oldali menüben válassza a **közreműködők** lehetőséget.

1. Válassza a **munkatárs hozzáadása** lehetőséget az eszköztáron.

    [![Közreműködő hozzáadása](./media/luis-how-to-collaborate/add-collaborator.png "Közreműködő hozzáadása")](./media/luis-how-to-collaborate/add-collaborator.png#lightbox)

1. Adja meg azt az e-mail-címet, amelyet a közreműködő használ a LUIS portálra való bejelentkezéshez.

    ![Közreműködő e-mail-címének hozzáadása](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)


### <a name="users-with-multiple-emails"></a>Több e-mailekkel rendelkező felhasználók 

Ha a LUIS-alkalmazáshoz közreműködőket/közreműködőket ad hozzá, pontosan megadja az e-mail-címet. Míg Azure Active Directory (Azure AD) lehetővé teszi, hogy egyetlen felhasználó több e-mail fiókkal is felhasználható, a LUIS megköveteli a felhasználótól, hogy jelentkezzen be a közreműködő/közreműködő hozzáadásakor megadott e-mail-címmel.

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Erőforrások Azure Active Directory

Ha [Azure Active Directoryt](https://docs.microsoft.com/azure/active-directory/) (Azure ad) használ a szervezetben, Language UNDERSTANDING (Luis) engedélyre van szüksége a felhasználók hozzáférésére vonatkozó információkhoz, amikor a Luis-t szeretnék használni. A LUIS által igényelt erőforrások minimálisak. 

A részletes leírást akkor tekintheti meg, ha olyan fiókkal próbál regisztrálni, amely rendszergazdai jogosultságokkal rendelkezik, vagy nem igényel rendszergazdai jogosultságot, például a rendszergazdai beleegyezett:

* Lehetővé teszi, hogy bejelentkezzen az alkalmazásba a szervezeti fiókjával, és hagyja, hogy az alkalmazás beolvassa a profilját. Azt is lehetővé teszi, hogy az alkalmazás beolvassa az alapszintű vállalati adatokat. Ez LUIS engedélyt ad az alapszintű profilokra vonatkozó adatok olvasására, például felhasználói azonosító, e-mail, név
* Lehetővé teszi az alkalmazás számára, hogy megtekintse és frissítse az adatait, még akkor is, ha jelenleg nem használja az alkalmazást. A felhasználó hozzáférési jogkivonatának frissítéséhez engedély szükséges.


### <a name="azure-active-directory-tenant-user"></a>Bérlői felhasználó Azure Active Directory

A LUIS standard Azure Active Directory (Azure AD) engedélyezési folyamatot használ. 

A bérlői rendszergazdának közvetlenül kell működnie azzal a felhasználóval, akinek hozzáférést kell biztosítania a LUIS használatához az Azure AD-ben. 

* Először a felhasználó bejelentkezik a LUIS-be, és a rendszergazdai jóváhagyást igénylő előugró párbeszédpanelt látja. A folytatás előtt a felhasználó kapcsolatba lép a bérlői rendszergazdával. 
* Másodszor, a bérlői rendszergazda bejelentkezik a LUIS-ba, és megtekint egy beleegyező folyamat előugró párbeszédpanelt. Ez az a párbeszédpanel, amely számára a rendszergazdának engedélyt kell adnia a felhasználó számára. Ha a rendszergazda elfogadja az engedélyt, a felhasználó továbbra is elvégezheti a LUIS-t. Ha a bérlői rendszergazda nem tud bejelentkezni a LUIS-be, a [rendszergazda hozzáférhet a](https://account.activedirectory.windowsazure.com/r#/applications) luishoz, amely az alábbi képernyőfelvételen látható. Figyelje meg, hogy a lista a `LUIS`nevet tartalmazó elemekre van szűrve.

![Azure Active Directory-engedély az alkalmazás webhelyén](./media/luis-how-to-collaborate/tenant-permissions.png)

Ha a bérlői rendszergazda csak bizonyos felhasználók számára szeretne LUIS-t használni, néhány lehetséges megoldás létezik:
* Adja meg a "rendszergazdai beleegyezett" (beleegyezett az Azure AD összes felhasználójára), de az "igen" értékre állítsa a "felhasználói hozzárendelés szükséges" értéket a vállalati alkalmazás tulajdonságai területen, végül pedig csak a kívánt felhasználókat rendeli hozzá az alkalmazáshoz. Ezzel a módszerrel a rendszergazda továbbra is "rendszergazdai beleegyezett" az alkalmazáshoz, de megadhatja azokat a felhasználókat, akik hozzáférhetnek hozzá.
* Egy második megoldás az [Azure AD Graph API](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview) használatával biztosítja az egyes felhasználók beleegyezett. 

További információ az Azure Active Directory-felhasználókról és-engedélyekről: 
* [Alkalmazás korlátozása](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) felhasználói csoportra

## <a name="next-steps"></a>További lépések

* Megtudhatja [, hogyan használhatja a verzióit](luis-how-to-manage-versions.md) az alkalmazás életciklusának szabályozására.
* Ismerje meg az adott erőforrással kapcsolatos fogalmakat, például a [szerzői erőforrást](luis-concept-keys.md#authoring-key) és a [közreműködőket](luis-concept-keys.md#contributions-from-other-authors) .
* Útmutató szerzői és futtatókörnyezeti erőforrások [létrehozásához](luis-how-to-azure-subscription.md)
* Migrálás az új [szerzői erőforrásba](luis-migration-authoring.md) 
