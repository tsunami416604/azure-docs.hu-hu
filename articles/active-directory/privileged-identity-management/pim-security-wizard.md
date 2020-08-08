---
title: Azure AD-szerepkörök felderítése és észlelése (előzetes verzió) Privileged Identity Management korábbi biztonsági varázslóban – Azure Active Directory
description: A felderítés és az adatok (korábban biztonsági varázsló) segítségével az állandó Azure AD szerepkör-hozzárendeléseket az Privileged Identity Management használatával igény szerinti hozzárendelésekre alakíthatja át.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 08/07/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef1de7e98a9cb57f83b87589ceddedc3cdd80927
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2020
ms.locfileid: "88005932"
---
# <a name="discovery-and-insights-preview-for-azure-ad-roles-formerly-security-wizard"></a>Az Azure AD szerepköreinek felderítése és bepillantást nyerhet (korábban biztonsági varázsló)

Ha a Azure Active Directory (Azure AD) szervezeten belül Privileged Identity Management (PIM) szolgáltatással indul, a **felderítés és az észlelés (előzetes verzió)** lapon megtekintheti az első lépéseket. Ez a szolgáltatás azt mutatja be, hogy kik vannak hozzárendelve a szervezet Kiemelt szerepköreihez, és hogyan használhatja a PIM-t az állandó szerepkör-hozzárendelések gyors módosításához az igény szerinti hozzárendelésekben. Az állandó jogosultsági szintű szerepkör-hozzárendeléseket megtekintheti vagy módosíthatja a **felderítés és az adatok (előzetes verzió)** szolgáltatásban. Ez egy elemzési eszköz és egy műveleti eszköz.

## <a name="discovery-and-insights-preview"></a>Felderítés és észlelés (előzetes verzió)

A szervezet Privileged Identity Management használatának megkezdése előtt az összes szerepkör-hozzárendelés állandó. A felhasználók mindig a hozzárendelt szerepkörökhöz tartoznak, még akkor is, ha nincs rájuk szükségük. A korábbi biztonsági varázslót felváltó felderítési és észlelési funkciók (előzetes verzió) megjelenítik a Kiemelt szerepkörök listáját, valamint azt, hogy hány felhasználó van jelenleg ezekben a szerepkörökben. Kilistázhatja a szerepkörök hozzárendeléseit, ha többet szeretne megtudni a hozzárendelt felhasználókkal kapcsolatban, ha egy vagy több ismeretlen.

: heavy_check_mark: a **Microsoft azt javasolja** , hogy tartsa meg a két, a bejelentkezéskor a többtényezős hitelesítést nem igénylő globális rendszergazdai szerepkörhöz véglegesen hozzárendelt break Glass-fiókot. Ezeket bármilyen megszakítást okozó megoldásban használhatja, vagy abban az esetben, ha senki sem tudja a globális rendszergazdai szerepkört megemelni.

Emellett a szerepkör-hozzárendelések állandók maradnak, ha a felhasználó Microsoft-fiók (azaz egy olyan fiók, amelyet a Microsoft-szolgáltatásokba, például a Skype-ba vagy a Outlook.com-be való bejelentkezéshez használnak). Ha a többtényezős hitelesítés aktiválása szükséges egy ilyen szerepkörben, a rendszer kizárja a felhasználót.

## <a name="open-discovery-and-insights-preview"></a>A felderítés és az észlelések megnyitása (előzetes verzió)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza az **Azure ad-szerepkörök** lehetőséget, majd válassza a **felderítés és az észlelés (előzetes verzió)** lehetőséget. A lap megnyitása megkezdi a felderítési folyamatot a megfelelő szerepkör-hozzárendelések kereséséhez.

    ![Azure AD-szerepkörök – felderítési és észlelési oldal, amely a 3 lehetőséget mutatja](./media/pim-security-wizard/new-preview-link.png)

1. Válassza a **globális rendszergazdák csökkentése**lehetőséget.

    ![Globális rendszergazdák csökkentése – az összes tagot megjelenítő szerepkör panel](./media/pim-security-wizard/new-preview-page.png)

1. Tekintse át a globális rendszergazdai szerepkör-hozzárendelések listáját.

    ![Globális rendszergazdák csökkentése – az összes tagot megjelenítő szerepkör panel](./media/pim-security-wizard/new-global-administrator-list.png)

1. A **tovább** gombra kattintva válassza ki azokat a felhasználókat vagy csoportokat, akik számára jogosult szeretne lenni, majd válassza a **jogosult** vagy a **hozzárendelés eltávolítása**lehetőséget.

    ![Tagok átalakítása jogosult lapra lehetőséggel kiválaszthatja azokat a tagokat, akik számára jogosult lehet a szerepkörökre.](./media/pim-security-wizard/new-global-administrator-buttons.png)

1. Azt is megkövetelheti, hogy az összes globális rendszergazda áttekintse a saját hozzáférését.

    ![A globális rendszergazdák lap hozzáférési felülvizsgálatok szakasza](./media/pim-security-wizard/new-global-administrator-access-review.png)

1. Miután kiválasztotta ezeket a módosításokat, megjelenik egy Azure-értesítés.

1. Ezután kiválaszthatja az **állandó hozzáférés megszüntetése** vagy az **egyszerű szolgáltatások áttekintése** lehetőséget, hogy megismételje a fenti lépéseket más kiemelt szerepkörökön és a szolgáltatás egyszerű szerepkör-hozzárendelésein. Az egyszerű szolgáltatás szerepkör-hozzárendelései csak a szerepkör-hozzárendelések eltávolítására használhatók.

    ![További információk az állandó hozzáférés és az egyszerű szolgáltatások áttekintése érdekében ](./media/pim-security-wizard/new-preview-page-service-principals.png)

## <a name="next-steps"></a>További lépések

- [Azure AD-szerepkörök kiosztása Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Hozzáférés biztosítása más rendszergazdáknak a Privileged Identity Management kezeléséhez](pim-how-to-give-access-to-pim.md)
