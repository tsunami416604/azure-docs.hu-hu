---
title: Office 365-csoportok lejáratának beállítása – Azure Active Directory | Microsoft dokumentumok
description: Az Office 365-csoportok lejáratának beállítása az Azure Active Directoryban
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/13/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 562b551bc8a46a45135bf6a9a8e328b4b0e74f98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80048246"
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Az Office 365-csoportok lejárati házirendjének konfigurálása

Ez a cikk bemutatja, hogyan kezelheti az Office 365-csoportok életciklusát egy elévülési szabályzat beállításával. Csak az Azure Active Directoryban (Azure AD) csak office 365-csoportokhoz állíthat be elévülési szabályzatot.

Miután beállítottegy csoportot, hogy lejárjon:

- A felhasználói tevékenységekkel rendelkező csoportok automatikusan megújulnak a lejárat közeledtével.
- A csoport tulajdonosai értesítést kapnak a csoport megújításáról, ha a csoport nem újul meg automatikusan.
- A nem megújított csoportok törlődnek.
- A törölt Office 365-csoportokat a csoporttulajdonosok vagy a rendszergazda 30 napon belül visszaállíthatja.

Jelenleg csak egy elévülési szabályzat konfigurálható az Azure AD-szervezet összes Office 365-csoportjához.

> [!NOTE]
> Az Office 365-csoportok lejárati szabályzatának konfigurálásához és használatához rendelkeznie kell, de nem feltétlenül kell Azure AD Premium-licenceket hozzárendelnie az összes olyan csoport tagjaihoz, amelyekre az elévülési szabályzat vonatkozik.

Az Azure AD PowerShell-parancsmagok letöltésével és telepítésével kapcsolatos további tudnivalókért tekintse meg az [Azure Active Directory PowerShell for Graph 2.0.0.137 című témakört.](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137)

## <a name="activity-based-automatic-renewal"></a>Tevékenységalapú automatikus megújítás

Az Azure AD-intelligencia, csoportok most automatikusan megújul attól függően, hogy a közelmúltban használt. Ez a szolgáltatás szükségtelenné teszi a csoporttulajdonosok manuális beavatkozását, mivel az Office 365-szolgáltatások , például az Outlook, a SharePoint vagy a Teams csoportban végzett felhasználói tevékenységén alapul. Ha például egy tulajdonos vagy egy csoporttag például például egy sharePoint-dokumentum feltöltése, egy Teams-csatorna felkeresése vagy e-mail küldése a csoportnak az Outlook programban, a csoport automatikusan megújul, és a tulajdonos nem kap megújítási értesítéseket.

### <a name="activities-that-automatically-renew-group-expiration"></a>A csoport lejáratát automatikusan megújuló tevékenységek

A következő felhasználói műveletek automatikus csoportmegújítást eredményeznek:

- SharePoint: Fájlok megtekintése, szerkesztése, letöltése, áthelyezése, megosztása és feltöltése
- Outlook: Csatlakozás csoportba, olvasás/írás csoportüzenet a csoportterületről, Üzenet kedvelése (az Outlook Web Access programban)
- Csapatok: Felkeresni egy Teams-csatornát

### <a name="auditing-and-reporting"></a>Könyvvizsgálat és jelentéstétel

A rendszergazdák az Azure AD-ben a tevékenységnaplózási naplókból lekaphatják az automatikusan megújított csoportok listáját.

![Csoportok automatikus megújítása tevékenység alapján](./media/groups-lifecycle/audit-logs-autorenew-group.png)

## <a name="roles-and-permissions"></a>Szerepkörök és engedélyek

Az alábbi szerepkörök konfigurálhatják és használhatják az Office 365-csoportok lejáratát az Azure AD-ben.

Szerepkör | Engedélyek
-------- | --------
Globális rendszergazda, csoportrendszergazda vagy felhasználó rendszergazda | Az Office 365 csoportok lejárati házirend-beállításainak létrehozása, olvasása, frissítése és törlése<br>Bármely Office 365-csoport megújítása
Felhasználó | Megújíthat egy saját Office 365-csoportot<br>Visszaállíthatja az általuk birtokolt Office 365-csoportot<br>Az elévülési házirend beállításainak olvasása

A törölt csoportok visszaállítására vonatkozó engedélyekről a [Törölt Office 365-csoport visszaállítása az Azure Active Directoryban](groups-restore-deleted.md)című témakörben talál további információt.

## <a name="set-group-expiration"></a>Csoportok elévülésének beállítása

1. Nyissa meg az [Azure AD felügyeleti központot](https://aad.portal.azure.com) egy olyan fiókkal, amely az Azure AD-szervezet globális rendszergazdája.

2. Válassza **a Csoportok**lehetőséget, majd a **Lejárat** lehetőséget választva nyissa meg a lejárati beállításokat.
  
   ![Csoportok elévülési beállításai](./media/groups-lifecycle/expiration-settings.png)

3. A **Lejárat** lapon a következőket teheti:

    - Állítsa be a csoport élettartamát napokban. Kiválaszthat egy előre beállított értéket vagy egy egyéni értéket (31 nap vagy több).
    - Adja meg azt az e-mail címet, amelyen a megújítási és lejárati értesítéseket el kell küldeni, ha egy csoportnak nincs tulajdonosa.
    - Válassza ki, hogy mely Office 365-csoportok járnak le. A következők lejáratát a következőkre állíthatja be:
      - **Az összes** Office 365-csoportok
      - **A kijelölt** Office 365-csoportok listája
      - **Nincs,** ha minden csoportesetében korlátozná a lejárati adatokat
    - Mentse a beállításokat, ha végzett, válassza a **Mentés**lehetőséget.

> [!NOTE]
> - A lejárat első beállításakor a lejárati időköznél régebbi csoportok 35 napra vannak állítva a lejáratig, kivéve, ha a csoport automatikusan megújul, vagy a tulajdonos megújítja azt.
> - Dinamikus csoport törlésekor és visszaállításakor új csoportnak tekintik, és a szabály nak megfelelően újra feltöltik. Ez a folyamat akár 24 órát is igénybe vehet.
> - A Csapatokban használt csoportok lejárati értesítései megjelennek a Teams-tulajdonosok hírcsatornában.

## <a name="email-notifications"></a>E-mail-értesítések

Ha a csoportok at nem újítja meg automatikusan, az ilyen e-mail értesítéseket a rendszer 30 nappal, 15 nappal és 1 nappal a csoport lejárta előtt elküldi az Office 365-ös csoporttulajdonosoknak. Az e-mail nyelvét a csoportok tulajdonosának előnyben részesített nyelve vagy az Azure AD nyelvi beállítása határozza meg. Ha a csoport tulajdonosa megadott egy előnyben részesített nyelvet, vagy több tulajdonos nak ugyanaz az előnyben részesített nyelve, akkor ezt a nyelvet használja a program. Minden más esetben az Azure AD nyelvi beállítás használatos.

![Lejárati e-mail értesítések](./media/groups-lifecycle/expiration-notification.png)

A **Csoportértesítés megújítása** e-mailben a csoporttulajdonosok közvetlenül elérhetik a Csoport részleteit tartalmazó lapot a [Hozzáférési panelen.](https://account.activedirectory.windowsazure.com/r#/applications) Ott a felhasználók további információkat kaphatnak a csoportról, például a leírását, az utolsó megújítását, a lejárati idő lejártát, valamint a csoport megújításának lehetőségét. A csoport részletei lap most már az Office 365-csoport erőforrásaira mutató hivatkozásokat is tartalmaz, így a csoport tulajdonosa kényelmesen megtekintheti a csoport tartalmát és tevékenységét.

Amikor egy csoport lejár, a csoport törlődik egy nappal a lejárati dátum után. A rendszer e-mailben értesíti az Office 365-csoport tulajdonosokat az Office 365-csoport lejáratáról és későbbi törléséről.

![Csoporttörlési e-mail értesítések](./media/groups-lifecycle/deletion-notification.png)

A csoport a törléstől számított 30 napon belül visszaállítható a **Visszaállítás csoport** kiválasztásával vagy a PowerShell-parancsmagok használatával, az Azure Active Directory törölt [Office 365-csoportjának visszaállítása](groups-restore-deleted.md)című részében leírtak szerint. Felhívjuk figyelmét, hogy a 30 napos csoportrestaurálási időszak nem szabható testre.

Ha a visszaállítani kívánt csoport dokumentumokat, SharePoint-webhelyeket vagy más állandó objektumokat tartalmaz, a csoport és annak tartalma akár 24 órát is igénybe vehet.

## <a name="how-to-retrieve-office-365-group-expiration-date"></a>Az Office 365-csoport lejárati dátumának beolvasása

A Hozzáférési panelen kívül, ahol a felhasználók megtekinthetik a csoport adatait, beleértve a lejárati dátumot és az utolsó megújított dátumot, az Office 365-csoport lejárati dátuma leolvasható a Microsoft Graph REST API Beta programból. a microsoft graph béta verziójában engedélyezve van a csoporttulajdonságként lejárati dátumidő. Get kéréssel visszakereshető. További részletekért kérjük, olvassa el [ezt a példát](https://docs.microsoft.com/graph/api/group-get?view=graph-rest-beta#example).

> [!NOTE]
> A hozzáférési panelcsoport-tagságok kezeléséhez a "Hozzáférés panelen lévő csoportok hozadékához" beállítást "Nem" beállítással kell beállítani az Azure Active Directory csoportok általános beállításában.

## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Az Office 365-csoport lejárati működése a jogi visszatartásban lévő postaládával

Ha egy csoport lejár és törlődik, akkor 30 nappal a törlés után a csoport adatai véglegesen törlődnek az olyan alkalmazásokból, mint a Planner, a Webhelyek vagy a Teams, de a jogi visszatartásban lévő csoportpostaláda megmarad, és nem törlődik véglegesen. A rendszergazda exchange-parancsmagokkal állíthatja vissza a postaládát az adatok beolvasásához.

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Az Office 365-csoport lejárati módja az adatmegőrzési házirenddel

Az adatmegőrzési házirend a Biztonsági és megfelelőségi központ on konfigurálva van konfigurálva. Ha adatmegőrzési házirendet állított be az Office 365-csoportokhoz, amikor egy csoport lejár és törlődik, a csoportfiókban lévő csoportos beszélgetések és a csoportwebhelyen lévő fájlok az adatmegőrzési tárolóban maradnak az adatmegőrzési tárolóban meghatározott számú napig Politika. A felhasználók a lejárat után nem látják a csoportot vagy annak tartalmát, de a webhely- és postaláda-adatokat az e-felderítés segítségével helyreállíthatják.

## <a name="powershell-examples"></a>Példák powershell-példák

Íme néhány példa arra, hogyan konfigurálhatja az Office 365-csoportok lejárati beállításait az Azure AD-szervezetben:

1. Telepítse a PowerShell 2.0-s modulját, és jelentkezzen be a PowerShell-üzenetben:

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. Az elévülési beállítások konfigurálása: Az Új-AzureADMSGroupLifecyclePolicy parancsmag használatával az Azure AD-szervezet összes Office 365-csoportjának élettartamát 365 napra állíthatja. A tulajdonosok nélküli Office 365-csoportok megújítási értesítéseit a 'emailaddress@contoso.com
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. A get-AzureADMSGroupLifecyclePolicy meglévő házirend beolvasása: Ez a parancsmag lekéri az Office 365-csoport aktuális, konfigurált lejárati beállításait. Ebben a példában a következőket láthatja:

   - A házirend azonosítója
   - Az Azure AD-szervezet összes Office 365-csoportjának élettartama 365 napra van állítva
   - A tulajdonosok nélküli Office 365-csoportok megújítási értesítéseit a rendszer a 'emailaddress@contoso.com.
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. Frissítse a meglévő szabályzat Set-AzureADMSGroupLifecyclePolicy: Ez a parancsmag egy meglévő szabályzat frissítésére szolgál. Az alábbi példában a csoport élettartama a meglévő szabályzatban 365 napról 180 napra változik.
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. Adott csoportok hozzáadása a házirend-Add-AzureADMSLifecyclePolicyGroup: Ez a parancsmag hozzáad egy csoportot az életciklus-szabályzathoz. Lássunk erre egy példát:
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. Távolítsa el a meglévő házirend-eltávolítás-AzureADMSGroupLifecyclePolicy: Ez a parancsmag törli az Office 365-csoport lejárati beállításait, de megköveteli a házirend-azonosítót. Ez a parancsmag letiltja az Office 365-csoportok lejáratát.
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
A következő parancsmagok segítségével konfigurálhatja a házirend et részletesebben. További információt a [PowerShell dokumentációjában](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups)talál.

- Get-AzureADMSGroupLifecyclePolicy
- Új-AzureADMSGroupLifecyclePolicy
- Get-AzureADMSGroupLifecyclePolicy
- Set-AzureADMSGroupLifecyclePolicy
- Eltávolítás-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSÉletciklus-alapúszakpolitikai csoport
- Eltávolítás-AzureADMSÉletciklus-alapú házirendcsoport
- Alaphelyzetbe állítás-AzureADMSéletciklus-csoport
- Get-AzureADMSÉletciklus-alapú házirendcsoport

## <a name="next-steps"></a>További lépések

Ezek a cikkek további információkat nyújtanak az Azure AD-csoportokról.

- [Meglévő csoportok megtekintése](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Csoportbeállítások kezelése](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Csoporttagok kezelése](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Csoporttagságok kezelése](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [A csoportban lévő felhasználók dinamikus szabályainak kezelése](groups-dynamic-membership.md)
