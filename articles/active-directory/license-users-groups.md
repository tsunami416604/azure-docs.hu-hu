---
title: "Az Azure Active Directory felhasználók licenc |} Microsoft Docs"
description: "Megtudhatja, hogyan licencszerződést saját maga és a felhasználók az Azure Active Directoryban."
services: active-directory
documentationcenter: 
author: jeffgilb
manager: mtillman
editor: 
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: jeffgilb
custom: it-pro
ms.openlocfilehash: bc210b83a9eeb947a15b60548e43096bd9e11c45
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="quickstart-license-users-in-azure-active-directory"></a>Gyors üzembe helyezés: Licenccel rendelkező felhasználók az Azure Active Directoryban
Az Azure AD licencet-alapú szolgáltatások munkahelyi egy Azure Active Directory (Azure AD) az előfizetéshez az Azure-bérlő aktiválása. Miután az előfizetés aktív, szolgáltatási lehetőségeket az Azure AD-rendszergazdák által kezelt és licenccel rendelkező felhasználók által használt. Nagyvállalati mobilitási + biztonsági, prémium szintű Azure AD vagy Azure AD alapvető megvásárolt, a bérlő frissül az előfizetést, beleértve a érvényességi időtartam és a fizetett licenccel. Az előfizetési adatai, beleértve a hozzárendelt vagy elérhető licencek száma alatt az Azure portálon keresztül érhető el **Azure Active Directory** nyissa meg a **licencek** csempére. A **licencek** panel is a legjobb hely a licenc-hozzárendelések kezeléséhez.

Bár megszerezni egy előfizetés fizetett képességek konfigurálnia kell az összes, továbbra is kell osztania felhasználói licenceket szolgáltatások fizetős fizetős Azure ad. Minden olyan felhasználó, aki kell rendelkezik hozzáféréssel, vagy ki kezeli, az Azure AD a szolgáltatás fizetős hozzá kell rendelni a licencet. A licenc-hozzárendelést a felhasználó és a megvásárolt szolgáltatás, például az Azure AD Premium, a Basic, vagy a nagyvállalati mobilitási + biztonsági közötti társítás.

Használhat [Csoportalapú licenc-hozzárendelést](active-directory-licensing-whatis-azure-portal.md) például a következő szabályok beállítása:
* A címtárban szereplő összes felhasználó automatikusan licenc beszerzése
* Rendelkezik a megfelelő beosztás lekérdezi a licenc
* A szervezet más kezelői döntési delegálhat (használatával [az önkiszolgáló csoportok](active-directory-accessmanagement-self-service-group-management.md))

> [!TIP]
> Részletes leírását az csoportokhoz a licenc-hozzárendelést, beleértve a speciális forgatókönyvek és az Office 365 licencelési forgatókönyvek, lásd: [szükséges licencek kiosztása a felhasználók számára az Azure Active Directory csoport tagsága](active-directory-licensing-group-assignment-azure-portal.md).

## <a name="assign-licenses-to-users-and-groups"></a>Szükséges licencek kiosztása a felhasználók és csoportok
Aktív az előfizetése használja, érdemes először licenc hozzárendelése saját magának, és frissítse a böngészőt annak érdekében, hogy a várt funkciók az előfizetéshez mellékelt látni. A következő lépésre licenceket rendelhet a felhasználókat, akiknek hozzáférésre van szükségük a fizetős Azure AD-funkciókat. Egyszerűen licencek hozzárendelése a licenc hozzárendelése felhasználói csoportokat, nem pedig az egyéni felhasználók számára. Amikor licencet rendel egy csoportot, csoport minden tagjára kapott licencet. Ha a felhasználók hozzáadásakor vagy eltávolításakor a csoportból, a megfelelő licenc automatikusan hozzárendelt vagy eltávolítani. 

> [!NOTE]
> Egyes Microsoft-szolgáltatások nem érhetők el az összes helyen. Mielőtt licenc rendelhet egy felhasználói, a rendszergazdának meg kell adnia a **felhasználási hely** tulajdonság a felhasználó számára. Beállíthatja, hogy ez a tulajdonság a **felhasználói** &gt; **profil** &gt; **beállítások** az Azure portálon. Licenc-hozzárendelést használatakor bármely felhasználó, akinek felhasználási hely nincs megadva örökli a könyvtár helye.

Kell hozzárendelnie, a **Azure Active Directory** &gt; **licencek** &gt; **összes**, egy vagy több terméket, majd válassza ki és **hozzárendelése** a parancssávon.

![Válassza ki a licenc hozzárendelése](media/license-users-groups/select-license-to-assign.png)

Használhatja a **felhasználók és csoportok** panel több felhasználó vagy csoport kiválasztása, vagy tiltsa le a termékben a service-csomagokról. Felhasználó- és csoportnevek kereséséhez használja a keresőmezőt felül.

![Válasszon egy felhasználót vagy csoportot a licenc-hozzárendelést](media/license-users-groups/select-user-for-license-assignment.png)

Amikor licencet rendel egy csoport, egy ideig, amíg az összes felhasználó öröklik a csoport méretétől függően a licenc is igénybe vehet. Akkor is ellenőrizhesse a feldolgozási állapotát a **csoport** panel alatt a **licencek** csempére.

![Licenc-hozzárendelés állapota](media/license-users-groups/license-assignment-status.png)

Hozzárendelési hibák az Azure AD a licenc-hozzárendelést során fordulhatnak elő, de viszonylag ritkán kezelése az Azure AD és a nagyvállalati mobilitási + biztonsági termékeket. A lehetséges hozzárendelési hibák korlátozva:
- Hozzárendelés ütközés: amikor a felhasználó a korábban hozzárendelt a licencet, amely nem kompatibilis a jelenlegi licenc. Ebben az esetben a új licenc szükséges az aktuális példány eltávolítása.
- Túllépte a rendelkezésre álló licencek: hozzárendelt csoportokban lévő felhasználók száma meghaladja a rendelkezésre álló licencek, amikor a felhasználó-hozzárendelés állapotát tükrözi egy hiba miatt hiányzó licencek hozzárendelése.

### <a name="azure-ad-b2b-collaboration-licensing"></a>Az Azure AD B2B együttműködés licencelés

B2B együttműködés lehetővé teszi az Azure AD-szolgáltatásokhoz való hozzáférés biztosításához az Azure AD-bérlő vendégfelhasználók meghívása, és bármely Azure-erőforrások elérhetővé tenni kívánt.  

Nincs B2B felhasználókat fióknevet és az Azure AD-alkalmazáshoz való hozzárendelése nem kell fizetni. Legfeljebb 10 / Vendég felhasználó és a 3 alapvető jelentés alkalmazásokat is szabadon B2B együttműködés felhasználók számára. Ha a Vendég felhasználó az Azure AD-bérlő a partner bármely megfelelő licenccel rendelkezik, akkor lesz licenccel kell rendelkezniük a saját is.

Nem kötelező, de ha lehetővé szeretné tenni a hozzáférést a fizetős Azure AD-funkciókat, e B2B vendégfelhasználók licenccel kell rendelkezniük az Azure AD licencet megfelelő. Az hívja fel egy licencet a fizetős Azure ad-bérlő számára egy további öt Vendég meghívót, hogy a bérlő felhasználói jogosultságokat rendelhet B2B együttműködés. A forgatókönyvek és a vonatkozó adatokat, lásd: [útmutatást licencelési B2B együttműködés](active-directory-b2b-licensing.md).

## <a name="view-assigned-licenses"></a>Licencek hozzárendelése megtekintése

A hozzárendelt és rendelkezésre álló licencek összefoglaló áttekintést jelenik meg **Azure Active Directory** &gt; **licencek** &gt; **összes**.

![Licenc összefoglaló megtekintése](media/license-users-groups/view-license-summary.png)

Hozzárendelt felhasználók és csoportok részletes listáját a termék kiválasztása esetén érhető el. A **licenccel rendelkező felhasználók** lista mutatja azokat az összes felhasználó jelenleg fel a licenc és e licenc közvetlenül lett hozzárendelve a felhasználó, vagy ha öröklés útján egy csoporttól származik.

![Licenc részleteinek megtekintése](media/license-users-groups/view-license-detail.png)

Hasonlóképpen a **licenccel rendelkező csoportok** lista mutatja azokat az összes csoport, amelyhez licencek van rendelve. Válasszon egy felhasználót vagy csoportot nyissa meg a **licencek** panel, amelyen látható, hogy az objektum összes licenccel.

## <a name="remove-a-license"></a>Távolítsa el a licenc

Licenc eltávolításához nyissa meg a felhasználó vagy csoport, és nyissa meg a **licencek** csempére. Jelölje ki a licencet, és kattintson a **eltávolítása**.

![Távolítsa el a licenc](media/license-users-groups/remove-license.png)

A felhasználó egy csoportból örökölt licencek közvetlenül nem távolítható el. Ehelyett távolítsa el a felhasználót a csoporthoz, amelyből a licenc történő öröklés.


## <a name="next-steps"></a>Következő lépések
A gyors üzembe helyezés licencek hozzárendelése az Azure AD-címtárban lévő felhasználók és csoportok hogy megismerte. 

A következő hivatkozás segítségével előfizetési licenc-hozzárendelések konfigurálása az Azure-portálon az Azure AD-ben.

> [!div class="nextstepaction"]
> [Az Azure AD-licencek hozzárendelése](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Overview) 