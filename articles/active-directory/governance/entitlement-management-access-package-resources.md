---
title: Hozzáférési csomag erőforrásszerepköreinek módosítása az Azure AD jogosultságkezelésében – Azure Active Directory
description: Megtudhatja, hogyan módosíthatja egy meglévő hozzáférési csomag erőforrásszerepköreit az Azure Active Directory jogosultságkezelésben.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 102bbfbd1c02c93830f5c7fce89fe95d7fde54c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261891"
---
# <a name="change-resource-roles-for-an-access-package-in-azure-ad-entitlement-management"></a>Hozzáférési csomag erőforrásszerepköreinek módosítása az Azure AD-jogosultságkezelésben

Access package managerként bármikor módosíthatja a hozzáférési csomag erőforrásait anélkül, hogy aggódnia kellene a felhasználó új erőforrásokhoz való hozzáférésének kiépítése vagy a korábbi erőforrásokhoz való hozzáférésük eltávolítása miatt. Ez a cikk azt ismerteti, hogyan módosíthatja egy meglévő hozzáférési csomag erőforrásszerepköreit.

Ez a videó áttekintést nyújt a hozzáférési csomagok módosításáról.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3LD4Z]

## <a name="check-catalog-for-resources"></a>Erőforrások katalógusának ellenőrzése

Ha erőforrásokat kell hozzáadnia egy hozzáférési csomaghoz, ellenőrizze, hogy a szükséges erőforrások elérhetők-e a katalógusban. Ha Ön hozzáférés-csomagkezelő, nem adhat hozzá erőforrásokat a katalógushoz, még akkor sem, ha rendelkezik azokkal. A katalógusban elérhető erőforrások használatára korlátozódik.

**Előfeltételi szerepkör:** Globális rendszergazda, Felhasználói rendszergazda, Katalógustulajdonosa vagy Access-csomagkezelő

1. Az Azure Portalon kattintson az **Azure Active Directory,** majd **az identitás-cégirányítási**.

1. A bal oldali menüben kattintson a **Katalógus** parancsra, majd nyissa meg a katalógust.

1. A bal oldali menüben kattintson az **Erőforrások** parancsra a katalógusban lévő erőforrások listájának megtekintéséhez.

    ![A katalógusban lévő erőforrások listája](./media/entitlement-management-access-package-resources/catalog-resources.png)

1. Ha ön hozzáférés-csomagkezelő, és erőforrásokat kell hozzáadnia a katalógushoz, megkérheti a katalógus tulajdonosát, hogy vegye fel őket.

## <a name="add-resource-roles"></a>Erőforrás-szerepkörök hozzáadása

Az erőforrásszerepkör az erőforráshoz társított engedélyek gyűjteménye. Az erőforrásokat a felhasználók számára elérhetővé teheti, ha erőforrásszerepköröket ad hozzá a hozzáférési csomaghoz. Csoportokhoz, csoportokhoz, alkalmazásokhoz és SharePoint-webhelyekhez adhat hozzá erőforrásszerepköröket.

**Előfeltételi szerepkör:** Globális rendszergazda, Felhasználói rendszergazda, Katalógustulajdonosa vagy Access-csomagkezelő

1. Az Azure Portalon kattintson az **Azure Active Directory,** majd **az identitás-cégirányítási**.

1. A bal oldali menüben kattintson az **Access-csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. A bal oldali menüben kattintson az **Erőforrás-szerepkörök parancsra.**

1. Kattintson **az Erőforrás-szerepkörök hozzáadása** gombra az Erőforrás-szerepkörök hozzáadása a csomaghoz lap megnyitásához.

    ![Access csomag – Erőforrás-szerepkörök hozzáadása](./media/entitlement-management-access-package-resources/resource-roles-add.png)

1. Attól függően, hogy csoportot, csoportot, alkalmazást vagy SharePoint-webhelyet szeretne hozzáadni, hajtsa végre az alábbi erőforrásszerepkör-szakaszok egyikében ismertetett lépéseket.

## <a name="add-a-group-or-team-resource-role"></a>Csoport- vagy csoporterőforrás-szerepkör hozzáadása

A jogosultságkezelés automatikusan hozzáadhat felhasználókat egy csoporthoz vagy egy csoporthoz a Microsoft Teamsben, amikor hozzáférési csomagot kapnak. 

- Ha egy csoport vagy csoport egy hozzáférési csomag része, és egy felhasználó van hozzárendelve az adott hozzáférési csomaghoz, a felhasználó hozzáadódik az adott csoporthoz vagy csoporthoz, ha még nincs jelen.
- Amikor egy felhasználó hozzáférési csomag-hozzárendelése lejár, a rendszer eltávolítja őket a csoportból vagy a csapatból, kivéve, ha jelenleg egy másik hozzáférési csomaghoz van hozzárendelt, amely ugyanazt a csoportot vagy csoportot tartalmazza.

Bármelyik Azure [AD biztonsági csoportot vagy office 365-csoportot kiválaszthatja.](../fundamentals/active-directory-groups-create-azure-portal.md) A rendszergazdák bármely csoportot hozzáadhatnak a katalógushoz; a katalógustulajdonosok bármilyen csoportot hozzáadhatnak a katalógushoz, ha ők a csoport tulajdonosai. A csoport kiválasztásakor tartsa szem előtt az alábbi Azure AD-korlátozásokat:

- Ha egy felhasználót, beleértve egy vendéget is, egy csoport vagy csoport tagjaként ad hozzá, láthatja a csoport vagy csoport összes többi tagját.
- Az Azure AD nem módosíthatja a Windows Server Active Directoryból az Azure AD Connect használatával szinkronizált vagy az Exchange Online-ban terjesztési csoportként létrehozott csoport tagságát.  
- A dinamikus csoportok tagsága nem frissíthető tag hozzáadásával vagy eltávolításával, így a dinamikus csoporttagságok nem használhatók jogosultságkezeléssel.

További információt a Csoportok és az [Office 365-csoportok és a Microsoft Teams](https://docs.microsoft.com/microsoftteams/office-365-groups) [összehasonlítása](https://docs.microsoft.com/office365/admin/create-groups/compare-groups) című témakörben talál.

1. Az **Erőforrás-szerepkörök hozzáadása** a csomaghoz lapon kattintson a **Csoportok és csapatok** elemre a Csoportok kiválasztása ablaktábla megnyitásához.

1. Válassza ki a hozzáférési csomagba felvenni kívánt csoportokat és csoportokat.

    ![Access csomag – Erőforrás-szerepkörök hozzáadása – Csoportok kijelölése](./media/entitlement-management-access-package-resources/group-select.png)

1. Kattintson a **Kiválasztás** gombra.

    A csoport vagy csoport kiválasztása után az **Altípus** oszlop a következő altípusok egyikét fogja felsorolni:

    |  |  |
    | --- | --- |
    | Biztonság | Erőforrásokhoz való hozzáférés megadására szolgál. |
    | Disztribúció | Értesítések küldésére szolgál egy csoportnak. |
    | O365 | Olyan Office 365-csoport, amely nem teams-kompatibilis. A felhasználók közötti együttműködésre használható, a vállalaton belül és kívül egyaránt. |
    | Csoport | A Teams-kompatibilis Office 365-csoport. A felhasználók közötti együttműködésre használható, a vállalaton belül és kívül egyaránt. |

1. A **Szerepkör** listában válassza a **Tulajdonos** vagy **a Tag**lehetőséget.

    Általában a tagszerepkört választja ki. Ha a Tulajdonos szerepkört választja, amely lehetővé teszi a felhasználók számára, hogy más tagokat vagy tulajdonosokat vegyenek fel vagy távolítsanak el.

    ![Access csomag – Erőforrásszerepkör hozzáadása csoporthoz vagy csoporthoz](./media/entitlement-management-access-package-resources/group-role.png)

1. Kattintson a **Hozzáadás** gombra.

    A hozzáférési csomaghoz meglévő hozzárendelésekkel rendelkező felhasználók a hozzáhozzáadásakor automatikusan a csoport vagy a csoport tagjaivá válnak.

## <a name="add-an-application-resource-role"></a>Alkalmazáserőforrás-szerepkör hozzáadása

Beállíthatja, hogy az Azure AD automatikusan hozzárendelje a felhasználók hozzáférését egy Azure AD vállalati alkalmazáshoz, beleértve az SaaS-alkalmazásokat és a szervezet Azure AD-vel összevont alkalmazásait is, amikor egy felhasználóhoz hozzáférési csomag van hozzárendelve. Az Azure AD-vel összevont egyszeri bejelentkezésen keresztül integrálható alkalmazások esetében az Azure AD összevonási jogkivonatokat ad ki az alkalmazáshoz rendelt felhasználók számára.

Az alkalmazások több szerepkörrel is rendelkezhetnek. Amikor egy alkalmazást egy hozzáférési csomaghoz ad hozzá, ha az alkalmazás egynél több szerepkört is rendelkezik, meg kell adnia a megfelelő szerepkört a felhasználók számára. Ha alkalmazásokat fejleszt, a rról, hogy ezek a szerepkörök hogyan kerülnek hozzáadásra az [alkalmazásokhoz: Konfigurálja a vállalati alkalmazások SAML-jogkivonatában kiadott szerepkörjogkivonatot.](../develop/active-directory-enterprise-app-role-management.md)

Ha egy alkalmazásszerepkör egy hozzáférési csomag része:

- Amikor egy felhasználó hoz hozzá, hogy a hozzáférési csomag, a felhasználó hozzáadódik az adott alkalmazásszerepkör, ha még nincs jelen.
- Amikor egy felhasználó hozzáférési csomag-hozzárendelése lejár, a hozzáférésük törlődik az alkalmazásból, kivéve, ha egy másik hozzáférési csomaghoz van hozzárendelt, amely tartalmazza az adott alkalmazásszerepkört.

Az alkalmazás kiválasztásakor az alábbiakat kell figyelembe venni:

- Az alkalmazások szerepköreikhez csoportokat is rendelhetnek.  Választhat, hogy egy csoport helyett egy alkalmazás szerepkör egy hozzáférési csomag, de akkor az alkalmazás nem lesz látható a felhasználó számára a hozzáférési csomag részeként a Saját Hozzáférési portálon.

1. Az **Erőforrás-szerepkörök hozzáadása a csomaghoz** lapon kattintson az **Alkalmazások** elemre az Alkalmazások kiválasztása ablaktábla megnyitásához.

1. Jelölje ki a hozzáférési csomagba felvenni kívánt alkalmazásokat.

    ![Access csomag – Erőforrás-szerepkörök hozzáadása – Alkalmazások kiválasztása](./media/entitlement-management-access-package-resources/application-select.png)

1. Kattintson a **Kiválasztás** gombra.

1. A **Szerepkör** listában jelöljön ki egy alkalmazásszerepkört.

    ![Access csomag – Erőforrásszerepkör hozzáadása egy alkalmazáshoz](./media/entitlement-management-access-package-resources/application-role.png)

1. Kattintson a **Hozzáadás** gombra.

    A hozzáférési csomaghoz meglévő hozzárendelésekkel rendelkező felhasználók automatikusan hozzáférést kapnak ehhez az alkalmazáshoz, amikor hozzáadja.

## <a name="add-a-sharepoint-site-resource-role"></a>SharePoint-webhely erőforrásszerepkör hozzáadása

Az Azure AD automatikusan hozzárendelheti a felhasználóknak a sharepoint online-webhelyhez vagy a SharePoint Online-webhelycsoporthoz való hozzáférést, ha hozzáférési csomagot kapnak.

1. Az **Erőforrás-szerepkörök hozzáadása** a csomaghoz lapon kattintson a **SharePoint-webhelyek** elemre a SharePoint Online-webhelyek kiválasztása ablaktábla megnyitásához.

1. Jelölje ki a hozzáférési csomagba felvenni kívánt SharePoint Online-webhelyeket.

    ![Access csomag – Erőforrás-szerepkörök hozzáadása – SharePoint Online-webhelyek kiválasztása](./media/entitlement-management-access-package-resources/sharepoint-site-select.png)

1. Kattintson a **Kiválasztás** gombra.

1. A **Szerepkör** listában jelöljön ki egy SharePoint Online-webhelyszerepkört.

    ![Access csomag – Erőforrásszerepkör hozzáadása SharePoint Online-webhelyhez](./media/entitlement-management-access-package-resources/sharepoint-site-role.png)

1. Kattintson a **Hozzáadás** gombra.

    A hozzáférési csomaghoz már meglévő hozzárendelésekkel rendelkező felhasználók a hozzáadáskor automatikusan hozzáférést kapnak ehhez a SharePoint Online-webhelyhez.

## <a name="remove-resource-roles"></a>Erőforrás-szerepkörök eltávolítása

**Előfeltételi szerepkör:** Globális rendszergazda, Felhasználói rendszergazda, Katalógustulajdonosa vagy Access-csomagkezelő

1. Az Azure Portalon kattintson az **Azure Active Directory,** majd **az identitás-cégirányítási**.

1. A bal oldali menüben kattintson az **Access-csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. A bal oldali menüben kattintson az **Erőforrás-szerepkörök parancsra.**

1. Az erőforrás-szerepkörök listájában keresse meg az eltávolítani kívánt erőforrásszerepkört.

1. Kattintson a három pontra (**...**), majd az **Erőforrás-szerepkör eltávolítása parancsra.**

    A hozzáférési csomaghoz meglévő hozzárendelésekkel rendelkező felhasználók eltávolításakor automatikusan visszavonják a hozzáférésüket ehhez az erőforrásszerepkörhöz.

## <a name="when-changes-are-applied"></a>Változások alkalmazásakor

A jogosultságkezelés során az Azure AD naponta többször dolgozza fel a hozzárendelés és az erőforrások tömeges módosításait a hozzáférési csomagokban. Így ha feladatot hajt végre, vagy módosítja a hozzáférési csomag erőforrásszerepköreit, az akár 24 órát is igénybe vehet, amíg a módosítás az Azure AD-ben történik, valamint a módosítások más Microsoft Online Services-szolgáltatásokba vagy csatlakoztatott SaaS-be való terjesztéséhez szükséges idő Alkalmazások. Ha a módosítás csak néhány objektumot érint, a módosítás valószínűleg csak néhány percet vesz igénybe az Azure AD-ben való alkalmazáshoz, majd más Azure AD-összetevők észlelik ezt a módosítást, és frissítik a SaaS-alkalmazásokat. Ha a módosítás több ezer objektumot érint, a módosítás hosszabb időt vesz igénybe. Ha például rendelkezik egy 2 alkalmazással és 100 felhasználói hozzárendeléssel rendelkező hozzáférési csomaggal, és úgy dönt, hogy SharePoint-webhelyszerepkört ad hozzá a hozzáférési csomaghoz, akkor késleltethető lehet, amíg az összes felhasználó a SharePoint-webhelyszerepkör része nem lesz. Az Azure AD-napló, az Azure AD-kiépítési napló és a SharePoint-webhely naplózási naplói segítségével figyelheti az előrehaladást.

Az eltávolított csapattagok az Office 365-csoportból is törlődnek. Előfordulhat, hogy a csapat csevegési funkciójából való eltávolítás késleltetve történik meg. További információ: [Csoporttagság](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership).

## <a name="next-steps"></a>További lépések

- [Hozzon létre egy alapszintű csoportot, és vegyen fel tagokat az Azure Active Directory használatával](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Útmutató: Konfigurálja a vállalati alkalmazások SAML-jogkivonatában kiadott szerepkör-jogcímet](../develop/active-directory-enterprise-app-role-management.md)
- [A SharePoint Online bemutatása](/sharepoint/introduction)