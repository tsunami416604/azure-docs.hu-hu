---
title: Együttműködés másokkal - LUIS
titleSuffix: Azure Cognitive Services
description: Az alkalmazás tulajdonosa közreműködőket vehet fel a szerzői erőforrásba. Ezek a közreműködők módosíthatják a modellt, betaníthatják és közzétehetik az alkalmazást.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 913a2b26f67773d9fafbc0a8430d121fbabb97cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053459"
---
# <a name="add-contributors-to-your-app"></a>Közreműködők hozzáadása az alkalmazáshoz

Az alkalmazás tulajdonosa közreműködőket adhat hozzá az alkalmazásokhoz. Ezek a közreműködők módosíthatják a modellt, betaníthatják és közzétehetik az alkalmazást. 

[!INCLUDE [Manage collaborators and contributors based on migrated or not-migrated apps](./includes/manage-contributor-collaborator-migration.md)]

## <a name="add-contributor-to-azure-authoring-resource"></a>Közreműködő hozzáadása az Azure szerzői erőforrásához

Az alábbi eljárás az összes **áttelepített** felhasználók számára az Azure szerzői erőforrás használata.

Áttelepítette, ha a LUIS szerzői élmény e-kezelési erőforrás hoz van kötve a **Kezelése -> Azure-erőforrások** lapon a LUIS-portálon.

1. Az Azure Portalon keresse meg a language understanding (LUIS) szerzői erőforrás. Ez a `LUIS.Authoring`típus .
1. Az erőforrás **hozzáférés-vezérlési (IAM)** lapján válassza a **+Hozzáadás,** majd a **Szerepkör-hozzárendelés hozzáadása**lehetőséget.

    ![Az Azure Portalon adja hozzá a szerepkör-hozzárendelést a szerzői erőforráshoz.](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. A **Szerepkör-hozzárendelés hozzáadása** ablakban válassza ki a Közreműködő **szerepkörét.** A **Hozzáférés hozzárendelése beállításban** válassza az **Azure AD-felhasználó, -csoport vagy egyszerű szolgáltatás lehetőséget.** A **Kijelölés** beállításban adja meg a felhasználó e-mail címét. Ha a felhasználót több mint 1 e-mail-cím ismeri ugyanahhoz a tartományhoz, győződjön meg arról, hogy adja meg az _elsődleges_ e-mail fiókot.

    ![Felhasználói e-mail hozzáadása az Azure AD közreműködői szerepköréhez](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    Amikor a felhasználó e-mail címe megtalálható, jelölje ki a fiókot, és válassza a **Mentés**lehetőséget. 

    Ha problémája van ezzel a szerepkör-hozzárendeléssel, tekintse át [az Azure szerepkör-hozzárendeléseket](../../role-based-access-control/role-assignments-portal.md) és [az Azure hozzáférés-vezérléssel kapcsolatos hibaelhárítást.](../../role-based-access-control/troubleshooting.md#problems-with-azure-role-assignments)

## <a name="add-collaborator-to-luis-app"></a>Közreműködő hozzáadása a LUIS alkalmazáshoz

Az alábbi eljárás minden olyan felhasználó számára, akik **nem telepítettek** az Azure szerzői erőforrás használatára.

Nem telepítette át, ha a LUIS szerzői élménye nincs kötve egy szerzői erőforráshoz a **Luis-> Azure-erőforrások kezelése** lapján.

Egy alkalmazás egyetlen szerzővel, a tulajdonos, de sok együttműködő. Ahhoz, hogy a közreműködők szerkeszthesd a LUIS-alkalmazást, hozzá kell adnia az általuk használt e-mailt a LUIS-portál eléréséhez a közreműködők listájához. Hozzáadása után az alkalmazás megjelenik a LUIS-portálon.

1. Válassza a jobb felső menü **Kezelés parancsát,** majd a bal oldali menüben válassza a **Közreműködők** lehetőséget.

1. Válassza az eszköztár **Közreműködő hozzáadása** parancsát.

1. Adja meg azt az e-mail címet, amelyet a közreműködő a LUIS-portálra való bejelentkezéshez használ.

    ![Munkatárs e-mail címének hozzáadása](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)


### <a name="users-with-multiple-emails"></a>Több e-maillel rendelkező felhasználók 

Ha közreműködők/közreműködők hozzáadása egy LUIS-alkalmazáshoz, megadja a pontos e-mail-címet. Míg az Azure Active Directory (Azure AD) lehetővé teszi, hogy egy felhasználó egynél több e-mail fiók szinonimaként használt, LUIS megköveteli, hogy a felhasználó jelentkezzen be a megadott e-mail-címet a közreműködő/közreműködő hozzáadásakor.

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Az Azure Active Directory erőforrásai

Ha [az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (Azure AD) a szervezetben, nyelvi megértés (LUIS) engedélyre van szüksége a felhasználók hozzáférésére vonatkozó információkat, ha a LUIS-t szeretné használni. A LUIS által igényelt erőforrások minimálisak. 

A részletes leírás akkor jelenik meg, amikor rendszergazdai jóváhagyással rendelkező vagy rendszergazdai jóváhagyással nem rendelkező fiókkal próbál regisztrálni, például rendszergazdai jóváhagyással:

* Lehetővé teszi, hogy a szervezeti fiókkal jelentkezzen be az alkalmazásba, és lehetővé tegye az alkalmazás számára, hogy elolvassa a profilját. Azt is lehetővé teszi az alkalmazás számára, hogy olvassa el az alapvető vállalati információkat. Ez lehetővé teszi a LUIS számára az alapvető profiladatok olvasását, például a felhasználói azonosítót, az e-mailt, a
* Lehetővé teszi, hogy az alkalmazás akkor is megtekintse és frissítse az adatokat, ha éppen nem használja az alkalmazást. A felhasználó hozzáférési jogkivonatának frissítéséhez engedély szükséges.


### <a name="azure-active-directory-tenant-user"></a>Az Azure Active Directory bérlői felhasználója

A LUIS szabványos Azure Active Directory (Azure AD) hozzájárulási folyamatot használ. 

A bérlői rendszergazdának közvetlenül együtt kell működnie a felhasználóval, akinek hozzáférésre van szüksége a LUIS használatához az Azure AD-ben. 

* Először a felhasználó bejelentkezik a LUIS-ba, és látja az előugró párbeszédpanelt, amely rendszergazdai jóváhagyást igényel. A felhasználó a folytatás előtt kapcsolatba lép a bérlői rendszergazdával. 
* Másodszor, a bérlői rendszergazda bejelentkezik a LUIS-ba, és megjelenik egy hozzájárulási folyamat előugró párbeszédpanel. Ez az a párbeszéd, amelyre a rendszergazdának engedélyt kell adnia a felhasználónak. Miután a rendszergazda elfogadja az engedélyt, a felhasználó folytathatja a LUIS. Ha a bérlői rendszergazda nem jelentkezik be a LUIS-ba, a rendszergazda hozzáférhet a LUIS [hozzájárulásához,](https://account.activedirectory.windowsazure.com/r#/applications) amely a következő képernyőképen látható. Figyelje meg, hogy a lista `LUIS`a nevet tartalmazó elemekre van szűrve.

![Az Azure active directory-engedélye alkalmazáswebhelyszerint](./media/luis-how-to-collaborate/tenant-permissions.png)

Ha a bérlői rendszergazda csak azt szeretné, hogy bizonyos felhasználók a LUIS-t használják, vannak néhány lehetséges megoldás:
* A "rendszergazdai hozzájárulás" megadása (hozzájárulás az Azure AD összes felhasználójának), de majd állítsa "Igen" a "Felhasználói hozzárendelés szükséges" a Vállalati alkalmazás tulajdonságai, és végül hozzá, /hozzá csak a kívánt felhasználók az alkalmazáshoz. Ezzel a módszerrel a rendszergazda továbbra is "rendszergazdai jóváhagyást" ad az alkalmazásnak, azonban lehetséges az elérhető felhasználók vezérlése.
* A második megoldás, az [Azure AD identitás- és hozzáférés-kezelési API](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview) használatával a Microsoft Graph, hogy minden egyes felhasználó beleegyezését adja. 

További információ az Azure active directory-felhasználóiról és hozzájárulásáról: 
* [Az alkalmazás korlátozása](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) felhasználók egy számára

## <a name="next-steps"></a>További lépések

* Ismerje [meg, hogyan vezérelheti](luis-how-to-manage-versions.md) a verziók az alkalmazás életciklusát.
* Ismerje meg a fogalmakat, beleértve a [szerzői erőforrást](luis-concept-keys.md#authoring-key) és az adott erőforrás [közreműködőit.](luis-concept-keys.md#contributions-from-other-authors)
* Útmutató szerzői és futásidejű erőforrások [létrehozásához](luis-how-to-azure-subscription.md)
* Áttelepítés az új [szerzői erőforrásra](luis-migration-authoring.md) 
