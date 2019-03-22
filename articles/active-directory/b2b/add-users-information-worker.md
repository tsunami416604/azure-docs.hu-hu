---
title: Az információkkal dolgozó – Azure Active Directory B2B együttműködési felhasználókat hozzáadni |} A Microsoft Docs
description: B2B-együttműködés lehetővé teszi, hogy az információkkal dolgozó szakemberek és az alkalmazástulajdonosok vendégfelhasználók hozzáadása az Azure AD-hez hozzáférés |} A Microsoft Docs
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 561d7f5b28775e5f431d35d956cff6f800cc11b2
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294511"
---
# <a name="how-users-in-your-organization-can-invite-guest-users-to-an-app"></a>Milyen a szervezetben a felhasználók egy alkalmazáshoz vendégfelhasználókat is meghívhat

Vendég után felhasználói van adva a címtárban az Azure AD-ben az alkalmazás tulajdonosa küldhet a vendégfelhasználó egy közvetlen hivatkozást a megosztani kívánt alkalmazáshoz. Az Azure AD-rendszergazdák is állíthat be a katalógus vagy a SAML-alapú alkalmazásokat az Azure AD-bérlő önkiszolgáló kezelése. Ezzel a módszerrel alkalmazástulajdonosok kezelheti saját vendég felhasználók akkor is, ha a vendég felhasználók még nem lett felvéve a címtárba még. Ha egy alkalmazást az önkiszolgáló működésre van konfigurálva, az alkalmazás tulajdonosa használatával alkalmazás Vendég felhasználó meghívása vagy vendégfelhasználó felvétele egy csoportba, amely hozzáfér az alkalmazás hozzáférési paneljén. Önkiszolgáló alkalmazás-felügyeleti katalógus és a SAML-alapú alkalmazások néhány kezdeti beállítás rendszergazda van szükség. A következő található egy összefoglaló az a telepítési lépéseket (a részletesebb útmutatásért tekintse meg a [Előfeltételek](#prerequisites) ezen az oldalon lentebb):

 - Önkiszolgáló csoportkezelés a bérlő engedélyezése
 - Hozzon létre egy csoportot, rendelje hozzá az alkalmazáshoz, és adja meg a felhasználó egy olyan tulajdonost
 - Az önkiszolgáló alkalmazás konfigurálása és a csoport hozzárendelése az alkalmazáshoz

> [!NOTE]
> Ez a cikk bemutatja, hogyan állítható be a katalógus és a SAML-alapú alkalmazások az Azure AD-bérlő hozzáadott önkiszolgáló felügyelete. Emellett [beállítása az Office 365-csoportok önkiszolgáló](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) így a felhasználók kezelhetik a saját Office 365-csoportok a hozzáférést. További módokon felhasználók megoszthatják az Office-fájlok és alkalmazások vendégfelhasználók, lásd: [vendég hozzáférés az Office 365-csoportok](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6) és [megosztani a SharePoint-fájlok vagy mappák](https://support.office.com/article/share-sharepoint-files-or-folders-1fe37332-0f9a-4719-970e-d2578da4941c).

## <a name="invite-a-guest-user-to-an-app-from-the-access-panel"></a>A Vendég felhasználó meghívása egy alkalmazáshoz a hozzáférési panelen

Miután egy alkalmazást az önkiszolgáló működésre van konfigurálva, alkalmazástulajdonosok használatával saját hozzáférési Panel a megosztani kívánt alkalmazás Vendég felhasználó meghívása. A Vendég felhasználó nem feltétlenül hozzá kell adni az Azure AD-előre. 

1. Nyissa meg a hozzáférési Panel a `https://myapps.microsoft.com`.
2. Az alkalmazás pontjára, kattintson a három pontra (**...** ), majd válassza ki **kezelés alkalmazás**.
 
   ![A kezelés alkalmazás almenü a Salesforce alkalmazás ábrázoló képernyőfelvétel](media/add-users-iw/access-panel-manage-app.png)
 
3. Válassza ki a felhasználók lista tetején **+**.
   
   ![Tagok hozzáadása az alkalmazáshoz tartozó a pluszjelre ábrázoló képernyőfelvétel](media/add-users-iw/access-panel-manage-app-add-user.png)
   
4. Az a **tagok hozzáadása** keresőmezőbe, a Vendég felhasználó e-mail címét. Ha szeretné, üdvözlő üzenetet is megadhat.
   
   ![Képernyőfelvétel: a bővítmény hozzáadásához a Vendég tagok ablak](media/add-users-iw/access-panel-invitation.png)
   
5. Válassza ki **Hozzáadás** , a vendégfelhasználó meghívó küldéséhez. Miután elküldte a meghívót, a felhasználói fiók automatikusan hozzáadódik a címtárhoz vendégként.

## <a name="invite-someone-to-join-a-group-that-has-access-to-the-app"></a>Meghívás a csatlakozás a csoporthoz, amely hozzáfér az alkalmazáshoz
Miután egy alkalmazást az önkiszolgáló működésre van konfigurálva, az alkalmazástulajdonosok a az általuk kezelt csoportokhoz, a megosztani kívánt alkalmazásokat elérő vendégfelhasználókat is meghívhat. A vendégfelhasználók nem kell a könyvtárban már létezik. Az alkalmazás tulajdonosa kövesse az alábbi lépéseket a csoportba vendégfelhasználó meghívni, hogy az alkalmazás eléréséhez.

1. Győződjön meg arról, hogy Ön a tulajdonosa férhet hozzá a megosztani kívánt alkalmazáshoz önkiszolgáló csoport.
2. Nyissa meg a hozzáférési Panel a `https://myapps.microsoft.com`.
3. Válassza ki a **csoportok** alkalmazást.
   
   ![Képernyőfelvétel: a csoportok alkalmazás a hozzáférési panelen](media/add-users-iw/access-panel-groups.png)
   
4. A **saját csoportok**, válassza ki a csoportot, amely hozzáfér a megosztani kívánt alkalmazáshoz.
   
   ![Képernyőfelvétel: hol kell válasszon ki egy csoportot a saját csoportok alapján](media/add-users-iw/access-panel-groups-i-own.png)
   
5. A tagok listájának tetején válassza **+**.
   
   ![Képernyőfelvétel a plusz a tagoknak a csoportba való felvételekor:](media/add-users-iw/access-panel-groups-add-member.png)
   
6. Az a **tagok hozzáadása** keresőmezőbe, a Vendég felhasználó e-mail címét. Ha szeretné, üdvözlő üzenetet is megadhat.
   
   ![Képernyőfelvétel: a bővítmény hozzáadásához a Vendég tagok ablak](media/add-users-iw/access-panel-invitation.png)
   
7. Válassza ki **Hozzáadás** meghívóban leírtakat, a vendégfelhasználó automatikus küldése. Miután elküldte a meghívót, a felhasználói fiók automatikusan hozzáadódik a címtárhoz vendégként.


## <a name="prerequisites"></a>Előfeltételek

Az önkiszolgáló alkalmazás kezeléséhez szükséges néhány kezdeti beállítás egy globális rendszergazda és az Azure AD-rendszergazda által. A telepítés részeként fog az önkiszolgáló alkalmazás konfigurálása és az alkalmazásnak, hogy az alkalmazás tulajdonosa felügyelheti is kiadhatjuk egy csoportnak. Beállíthatja, hogy a csoporthoz bárki csoporttagság, de a csoport tulajdonosa jóváhagyás szükséges. (További információ [önkiszolgáló csoportkezelési](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management).) 

> [!NOTE]
> Nem adhat meg vendégfelhasználókat, dinamikus csoportot vagy egy csoportot, amely a helyszíni Active Directoryval van szinkronizálva.

### <a name="enable-self-service-group-management-for-your-tenant"></a>Önkiszolgáló csoportkezelés a bérlő engedélyezése
1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) globális rendszergazdaként.
2. A navigációs panelen válassza ki a **Azure Active Directory**.
3. Válassza ki **csoportok**.
4. A **beállítások**válassza **általános**.
5. A **Self Service eszközcsoport-kezelés**mellett található **tulajdonosok kezelhetik a csoporttagsági kérelmeket a hozzáférési panelen**válassza **Igen**.
6. Kattintson a **Mentés** gombra.

### <a name="create-a-group-to-assign-to-the-app-and-make-the-user-an-owner"></a>Hozzon létre egy csoportot, rendelje hozzá az alkalmazáshoz, és adja meg a felhasználó egy olyan tulajdonost
1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy Azure AD-rendszergazda vagy globális rendszergazdaként.
2. A navigációs panelen válassza ki a **Azure Active Directory**.
3. Válassza ki **csoportok**.
4. Válassza ki **új csoport**.
5. A **csoporttípust**válassza **biztonsági**.
6. Gépelje be a **Csoport nevét** és a **Csoport leírását**.
7. A **tagságtípusának**válassza **hozzárendelt**.
8. Válassza ki **létrehozás**, és zárja be a **csoport** lapot.
9. Az a **csoportok – összes csoport** lapon, nyissa meg a csoportot. 
10. A **kezelés**válassza **tulajdonosok** > **tulajdonosok hozzáadása**. Keresse meg a felhasználót, aki kezelje az alkalmazáshoz való hozzáférést. Válassza ki a felhasználót, és kattintson a **kiválasztása**.

### <a name="configure-the-app-for-self-service-and-assign-the-group-to-the-app"></a>Az önkiszolgáló alkalmazás konfigurálása és a csoport hozzárendelése az alkalmazáshoz
1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy Azure AD-rendszergazda vagy globális rendszergazdaként.
2. A navigációs panelen válassza ki **Azure Active Directory**.
3. Alatt **kezelés**válassza **vállalati alkalmazások** > **minden alkalmazás**.
4. Az alkalmazás listában keresse meg és nyissa meg az alkalmazást.
5. A **kezelés**válassza **egyszeri bejelentkezési**, és az alkalmazás egyszeri bejelentkezés konfigurálása. (További információkért lásd: [egyszeri bejelentkezést a vállalati alkalmazások kezelése](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal).)
6. A **kezelés**válassza **önkiszolgáló**, és az önkiszolgáló alkalmazás-hozzáférés beállítása. (További információkért lásd: [önkiszolgáló alkalmazás-hozzáférés használata](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to).) 

    > [!NOTE]
    > A beállítás **melyik csoporthoz lesz hozzáadva a hozzárendelt felhasználók?** válassza ki az előző szakaszban létrehozott a csoportot.
7. Alatt **kezelés**, jelölje be **felhasználók és csoportok**, és győződjön meg arról, hogy az önkiszolgáló csoportkezelési létrehozott megjelenik a listában.
8. A csoport tulajdonosa hozzáférési panel az alkalmazás hozzáadásához válassza **felhasználó hozzáadása** > **felhasználók és csoportok**. Keresse meg a csoport tulajdonosa, és válassza ki a felhasználót, kattintson a **kiválasztása**, és kattintson a **hozzárendelése** a felhasználó hozzáadása az alkalmazáshoz.

## <a name="next-steps"></a>További lépések

Az Azure AD B2B együttműködés a következő cikkekben talál:

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [Hogyan hozzá az Azure Active Directory-rendszergazdák B2B együttműködési felhasználókat?](add-users-administrator.md)
- [B2B együttműködés vendégmeghívás beváltása](redemption-experience.md)
- [Az Azure AD B2B-együttműködés licencelése](licensing-guidance.md)
