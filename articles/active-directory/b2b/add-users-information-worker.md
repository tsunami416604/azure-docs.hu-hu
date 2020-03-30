---
title: B2B együttműködési felhasználók hozzáadása adatfeldolgozóként - Azure AD
description: A B2B-együttműködés lehetővé teszi az informatikai dolgozók és az alkalmazástulajdonosok számára, hogy vendégfelhasználókat adjanak hozzá az Azure AD-hez a hozzáféréshez | Microsoft dokumentumok
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: abb5c6939d8c88db35a776aa8f2c075a4bdcc609
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77565417"
---
# <a name="how-users-in-your-organization-can-invite-guest-users-to-an-app"></a>Hogyan hívhatják meg a szervezet felhasználói vendégfelhasználókat egy alkalmazásba?

Miután egy vendégfelhasználó hozzá lett adva a címtárhoz az Azure AD-ben, az alkalmazás tulajdonosa közvetlen hivatkozást küldhet a vendégfelhasználónak a megosztani kívánt alkalmazáshoz. Az Azure AD-rendszergazdák is beállíthatja az önkiszolgáló felügyelet a galéria vagy SAML-alapú alkalmazások az Azure AD-bérlő. Így az alkalmazástulajdonosok kezelhetik saját vendégfelhasználóikat, még akkor is, ha a vendégfelhasználók még nem lettek hozzáadva a címtárhoz. Ha egy alkalmazás önkiszolgálóra van konfigurálva, az alkalmazás tulajdonosa a Hozzáférési panel segítségével hív meg egy vendégfelhasználót egy alkalmazásba, vagy vendégfelhasználót ad hozzá egy olyan csoporthoz, amely hozzáfér az alkalmazáshoz. A galéria és az SAML-alapú alkalmazások önkiszolgáló alkalmazásfelügyeletéhez a rendszergazda kezdeti beállítására van szükség. Az alábbiakban összefoglaljuk a beállítási lépéseket (részletesebb útmutatásért lásd: [Előfeltételek](#prerequisites) később ezen az oldalon):

 - Engedélyezze az önkiszolgáló csoportkezelést a bérlő számára
 - Az alkalmazáshoz rendelt csoport létrehozása és a felhasználó tulajdonossá téváje
 - Az alkalmazás konfigurálása önkiszolgálóhasználatra és a csoport hozzárendelése az alkalmazáshoz

> [!NOTE]
> Ez a cikk ismerteti, hogyan állíthatja be az önkiszolgáló felügyelet a katalógus és az SAML-alapú alkalmazások, amelyek az Azure AD-bérlőhöz hozzáadott. [Önkiszolgáló Office 365-csoportokat is beállíthat,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) így a felhasználók kezelhetik a saját Office 365-csoportjukhoz való hozzáférést. Az Office-fájlok és -alkalmazások vendégfelhasználókkal való megosztásának további módjairól a [Vendéghozzáférés az Office 365-csoportokban](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6) és [a SharePoint-fájlok vagy -mappák megosztása](https://support.office.com/article/share-sharepoint-files-or-folders-1fe37332-0f9a-4719-970e-d2578da4941c)című témakörben található.

## <a name="invite-a-guest-user-to-an-app-from-the-access-panel"></a>Vendégfelhasználó meghívása egy alkalmazásba a Hozzáférési panelről

Miután egy alkalmazás önkiszolgálóra van konfigurálva, az alkalmazástulajdonosok saját hozzáférési paneljükön meghívhatnak egy vendégfelhasználót a megosztani kívánt alkalmazásba. A vendégfelhasználó nem feltétlenül kell előre hozzáadni az Azure AD-hez. 

1. Nyissa meg a hozzáférési `https://myapps.microsoft.com`panelt a programból.
2. Mutasson az alkalmazásra, jelölje ki a három pontot (**...**), majd válassza **az Alkalmazás kezelése**lehetőséget.
 
   ![Képernyőkép a Salesforce alkalmazás kezelése almenüjről](media/add-users-iw/access-panel-manage-app.png)
 
3. A felhasználók listájának tetején **+** válassza a lehetőséget.
   
   ![Képernyőkép a tagok alkalmazáshoz való felvételéhez használt plusz szimbólumról](media/add-users-iw/access-panel-manage-app-add-user.png)
   
4. A **Tagok hozzáadása** mezőbe írja be a vendégfelhasználó e-mail címét. Ha szeretné, üdvözlő üzenetet is megadhat.
   
   ![A Tagok hozzáadása ablakvendég felvételére szolgáló képernyőkép](media/add-users-iw/access-panel-invitation.png)
   
5. Válassza a **Hozzáadás** lehetőséget, ha meghívót szeretne küldeni a vendégfelhasználónak. Miután elküldte a meghívót, a felhasználói fiók automatikusan hozzáadódik a címtárhoz vendégként.

## <a name="invite-someone-to-join-a-group-that-has-access-to-the-app"></a>Az alkalmazáshoz hozzáféréssel rendelkező csoporthoz való csatlakozásra meghívott személy meghívása
Miután egy alkalmazás önkiszolgálóra van konfigurálva, az alkalmazástulajdonosok meghívhatják a vendégfelhasználókat az általuk kezelt csoportokba, amelyek hozzáférnek a megosztani kívánt alkalmazásokhoz. A vendégfelhasználóknak már nem kell létezniük a címtárban. Az alkalmazás tulajdonosa az alábbi lépéseket követve meghívhat egy vendégfelhasználót a csoportba, hogy hozzáférhessen az alkalmazáshoz.

1. Győződjön meg arról, hogy annak az önkiszolgáló csoportnak a tulajdonosa, amely hozzáfér a megosztani kívánt alkalmazáshoz.
2. Nyissa meg a hozzáférési `https://myapps.microsoft.com`panelt a programból.
3. Válassza a **Csoportok** alkalmazást.
   
   ![Képernyőkép a Csoportok alkalmazásról a Hozzáférési panelen](media/add-users-iw/access-panel-groups.png)
   
4. Az **Általam birtokolt csoportok**csoportban válassza ki azt a csoportot, amely hozzáfér a megosztani kívánt alkalmazáshoz.
   
   ![Képernyőkép acsoport kijelölésének helyéről az általam birtokolt csoportok alatt](media/add-users-iw/access-panel-groups-i-own.png)
   
5. A csoporttagok listájának tetején **+** válassza a lehetőséget.
   
   ![Képernyőkép a tagok csoporthoz való felvételéhez használt plusz szimbólumról](media/add-users-iw/access-panel-groups-add-member.png)
   
6. A **Tagok hozzáadása** mezőbe írja be a vendégfelhasználó e-mail címét. Ha szeretné, üdvözlő üzenetet is megadhat.
   
   ![A Tagok hozzáadása ablakvendég felvételére szolgáló képernyőkép](media/add-users-iw/access-panel-invitation.png)
   
7. Válassza a **Hozzáadás** lehetőséget, ha a meghívót automatikusan el szeretné küldeni a vendégfelhasználónak. Miután elküldte a meghívót, a felhasználói fiók automatikusan hozzáadódik a címtárhoz vendégként.


## <a name="prerequisites"></a>Előfeltételek

Az önkiszolgáló alkalmazások kezeléséhez egy globális rendszergazda és egy Azure AD-rendszergazda kezdeti beállítására van szükség. A telepítés részeként konfigurálja az alkalmazást az önkiszolgáló, és rendeljen hozzá egy csoportot az alkalmazáshoz, amelyet az alkalmazás tulajdonosa kezelhet. A csoportot úgy is beállíthatja, hogy bárki tagságot kérjen, de a csoport tulajdonosának jóváhagyása szükséges. (További információ az [önkiszolgáló csoportkezelésről.)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) 

> [!NOTE]
> Vendégfelhasználók at nem adhat hozzá dinamikus vagy a helyszíni Active Directoryval szinkronizált csoporthoz.

### <a name="enable-self-service-group-management-for-your-tenant"></a>Engedélyezze az önkiszolgáló csoportkezelést a bérlő számára
1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) globális rendszergazdaként.
2. A navigációs panelen válassza az **Azure Active Directory**lehetőséget.
3. Válassza a **Csoportok lehetőséget.**
4. A **Beállítások csoportban**válassza az **Általános**lehetőséget.
5. Az **Önkiszolgáló csoportkezelés csoportcsoportcsoport-kezelés**csoportcsoport-kezelés csoportcsoport-kérelmek kezelése a **Hozzáférési panelen**válassza az **Igen**lehetőséget.
6. Kattintson a **Mentés** gombra.

### <a name="create-a-group-to-assign-to-the-app-and-make-the-user-an-owner"></a>Az alkalmazáshoz rendelt csoport létrehozása és a felhasználó tulajdonossá téváje
1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) Azure AD-rendszergazdaként vagy globális rendszergazdaként.
2. A navigációs panelen válassza az **Azure Active Directory**lehetőséget.
3. Válassza a **Csoportok lehetőséget.**
4. Válassza az **Új csoport lehetőséget.**
5. A **Csoporttípus csoportban**válassza a **Biztonság**lehetőséget.
6. Gépelje be a **Csoport nevét** és a **Csoport leírását**.
7. A **Tagság típusa csoportban**válassza **a Hozzárendelt lehetőséget.**
8. Válassza **a Létrehozás**lehetőséget, és zárja be a **Csoport** lapot.
9. A **Csoportok – Minden csoport** lapon nyissa meg a csoportot. 
10. A **Kezelés csoportban**válassza **a Tulajdonosok** > **hozzáadása tulajdonosok lehetőséget.** Keresse meg azt a felhasználót, akinek kezelnie kell az alkalmazáshoz való hozzáférést. Jelölje ki a felhasználót, majd kattintson a **Kijelölés gombra.**

### <a name="configure-the-app-for-self-service-and-assign-the-group-to-the-app"></a>Az alkalmazás konfigurálása önkiszolgálóhasználatra és a csoport hozzárendelése az alkalmazáshoz
1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) Azure AD-rendszergazdaként vagy globális rendszergazdaként.
2. A navigációs ablakban válassza az **Azure Active Directory**lehetőséget.
3. A **Kezelés csoportban**válassza **a Vállalati alkalmazások** > **minden alkalmazás lehetőséget.**
4. Az alkalmazáslistában keresse meg és nyissa meg az alkalmazást.
5. A **Kezelés csoportban**válassza **az Egyszeri bejelentkezés**lehetőséget, és konfigurálja az alkalmazást egyszeri bejelentkezésre. (További részletek a [vállalati alkalmazások egyszeri bejelentkezésének kezeléséről.](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications)
6. A **Kezelés csoportban**válassza **az Önkiszolgáló**lehetőséget, és állítsa be az önkiszolgáló alkalmazás-hozzáférést. (További részletek az [önkiszolgáló alkalmazás-hozzáférés használatáról.](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to)) 

    > [!NOTE]
    > A beállítás **hoz melyik csoporthoz kell hozzárendelni a felhasználókat?** jelölje ki az előző szakaszban létrehozott csoportot.
7. A **Kezelés csoportban**válassza a **Felhasználók és csoportok**lehetőséget, és ellenőrizze, hogy a létrehozott önkiszolgáló csoport megjelenik-e a listában.
8. Ha hozzá szeretné adni az alkalmazást a csoporttulajdonos hozzáférési paneljéhez, válassza **a Felhasználói** > **felhasználók és csoportok**hozzáadása lehetőséget. Keresse meg a csoport tulajdonosát, és jelölje ki a felhasználót, kattintson a **Kijelölés gombra,** majd a **Hozzárendelés** parancsra, hogy hozzáadja a felhasználót az alkalmazáshoz.

## <a name="next-steps"></a>További lépések

Tekintse meg az alábbi cikkeket az Azure AD B2B együttműködés:

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [Hogyan adhatnak hozzá Az Azure Active Directory rendszergazdái B2B együttműködési felhasználókat?](add-users-administrator.md)
- [B2B együttműködési meghívás visszaváltás](redemption-experience.md)
- [Az Azure AD B2B-együttműködésének licencelése](licensing-guidance.md)
