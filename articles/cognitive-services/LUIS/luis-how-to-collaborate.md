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
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: 004da6c84e6226c4de3114d32cde826cd167e1ab
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844837"
---
# <a name="add-contributors-to-your-app"></a>Közreműködők hozzáadása az alkalmazáshoz

Az alkalmazások tulajdonosai hozzáadhatnak közreműködőket az alkalmazásokhoz. A közreműködők módosíthatja a modell betanítását és tegye közzé az alkalmazást. 

[!INCLUDE [Manage collaborators and contributors based on migrated or not-migrated apps](./includes/manage-contributor-collaborator-migration.md)]

## <a name="add-contributor-to-azure-authoring-resource"></a>Közreműködő hozzáadása az Azure authoring resourcehez

Az alábbi eljárás az Azure authoring Resource használatára **áttelepített** összes felhasználóra vonatkozik.

Áttelepítette, ha a LUIS authoring Experience egy authoring-erőforráshoz van kötve a LUIS **-portál Manage-> Azure-erőforrások** lapján.

1. A Azure Portal keresse meg a Language Understanding (LUIS) authoring erőforrást. A típusa a következő `LUIS.Authoring`:.
1. Az erőforrás **Access Control (iam)** lapon válassza a **+ Hozzáadás** lehetőséget, majd válassza a **szerepkör-hozzárendelés hozzáadása**elemet.

    ![A Azure Portalban vegyen fel szerepkör-hozzárendelést az erőforrás-létrehozáshoz.](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. A **szerepkör-hozzárendelés hozzáadása** ablakban válassza ki a közreműködő **szerepkört** . A **hozzáférés kiosztása** beállításnál válassza az **Azure ad-felhasználó,-csoport vagy egyszerű szolgáltatásnév**lehetőséget. A **Select (kiválasztás** ) lehetőségnél adja meg a felhasználó e-mail-címét. Ha a felhasználó neve több mint 1 e-mail-cím, akkor győződjön meg arról, hogy az _elsődleges_ e-mail-fiók.

    ![Felhasználó e-mail-címének hozzáadása az Azure AD közreműködői szerepköréhez](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    Ha a felhasználó e-mail-címe megtalálható, válassza ki a fiókot, és válassza a **Mentés**lehetőséget. 

    Ha problémája van a szerepkör-hozzárendeléssel, tekintse át az [Azure szerepkör-hozzárendeléseket](../../role-based-access-control/role-assignments-portal.md) és az [Azure hozzáférés-vezérléssel kapcsolatos hibaelhárítást](../../role-based-access-control/troubleshooting.md#problems-with-rbac-role-assignments).

## <a name="add-collaborator-to-luis-app"></a>Közreműködő hozzáadása a LUIS-alkalmazáshoz

Az alábbi eljárás az összes olyan felhasználóra vonatkozik, akik nem lettek **áttelepítve** az Azure authoring Resource használatára.

Nem telepítette át, ha a LUIS authoring Experience nem kötődik egy authoring-erőforráshoz a LUIS **-portál Manage-> Azure-erőforrások** lapján.

Egy alkalmazás egyetlen Szerző, a tulajdonos, rendelkezik, de sok közreműködők is. Ahhoz, hogy a LUIS-alkalmazás szerkesztéséhez közreműködő, hozzá kell adnia az e-mailt a közreműködők listája a LUIS-portál eléréséhez használnak. Hozzáadás után az alkalmazás pedig a LUIS-portálon jeleníti meg.

1. Válassza ki **kezelés** jobb felső menüben, majd válassza **közreműködők** a bal oldali menüben.

1. Válassza ki **adja hozzá a közreműködő** az eszköztáron.

    [![Hozzáadás közreműködő](./media/luis-how-to-collaborate/add-collaborator.png "Hozzáadás közreműködő")](./media/luis-how-to-collaborate/add-collaborator.png#lightbox)

1. Adja meg az e-mail-cím, a közreműködő segítségével jelentkezzen be a LUIS-portálon.

    ![Adja meg a közreműködő e-mail-címet](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)


### <a name="users-with-multiple-emails"></a>Több e-mailekkel rendelkező felhasználók 

Ha a LUIS-alkalmazáshoz közreműködőket/közreműködőket ad hozzá, pontosan megadja az e-mail-címet. Míg Azure Active Directory (Azure AD) lehetővé teszi, hogy egyetlen felhasználó több e-mail fiókkal is felhasználható, a LUIS megköveteli a felhasználótól, hogy jelentkezzen be a közreműködő/közreműködő hozzáadásakor megadott e-mail-címmel.

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Az Azure Active Directory-erőforrások

Ha [Azure Active Directoryt](https://docs.microsoft.com/azure/active-directory/) (Azure ad) használ a szervezetben, Language UNDERSTANDING (Luis) engedélyre van szüksége a felhasználók hozzáférésére vonatkozó információkhoz, amikor a Luis-t szeretnék használni. A LUIS igénylő erőforrások minimálisak. 

Amikor egy olyan fiókkal, amely rendelkezik rendszergazdai jóváhagyás, vagy nincs szükség rendszergazdai jóváhagyás, például a rendszergazdai jóváhagyást bejelentkezés jelenik meg a részletes leírása:

* Lehetővé teszi, hogy jelentkezzen be az alkalmazásba a szervezeti fiókjával, és hogy az alkalmazás olvassa a profilját. Azt is lehetővé teszi, hogy az alkalmazásnak a főbb munkahelyi adatai olvasását. Ez LUIS engedélyt ad az alapszintű profilokra vonatkozó adatok olvasására, például felhasználói azonosító, e-mail, név
* Itt engedélyezheti az alkalmazásnak, és frissítheti az adatokat, akkor is, ha jelenleg nem használja az alkalmazást. A felhasználó hozzáférési jogkivonatának frissítéséhez engedély szükséges.


### <a name="azure-active-directory-tenant-user"></a>Az Azure Active Directory-bérlői felhasználó

A LUIS a standard szintű Azure Active Directory (Azure AD) jóváhagyási folyamatot használ. 

A bérlői rendszergazda közvetlenül a felhasználó, aki a LUIS használatát az Azure AD-ben biztosított hozzáférést kell működnie. 

* A felhasználó először jelentkezik be a LUIS, és megtekinti a rendszergazdai jóváhagyást igénylő felugró párbeszédpanel. A felhasználó felveszi a kapcsolatot a bérlői rendszergazda a folytatás előtt. 
* A bérlői rendszergazda második, LUIS bejelentkezik, és egy hozzájárulási folyamat felugró párbeszédpanel látja. Ez az a párbeszédpanelen, a rendszergazda engedélyt kell adnia a felhasználó számára. A rendszergazda fogad az engedélyt, ha a felhasználó nem tudja folytatni az intelligens hangfelismerési szolgáltatással. Ha a bérlői rendszergazda nem tud bejelentkezni a LUIS-be, a [rendszergazda hozzáférhet a](https://account.activedirectory.windowsazure.com/r#/applications) luishoz, amely az alábbi képernyőfelvételen látható. Figyelje meg, hogy a lista a nevet `LUIS`tartalmazó elemekre van szűrve.

![Alkalmazás webhelye által az Azure active directory-engedély](./media/luis-how-to-collaborate/tenant-permissions.png)

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
