---
title: Office 365-csoportok elévülésének beállítása – Azure Active Directory | Microsoft Docs
description: Office 365-csoportok elévülésének beállítása Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/06/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56bfe92de24b9386252ee8719af66cc658948565
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844313"
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Az Office 365-csoportok elévülési szabályzatának konfigurálása

Ebből a cikkből megtudhatja, hogyan kezelheti az Office 365-csoportok életciklusát egy lejárati szabályzat beállításával. Lejárati szabályzatot csak az Office 365-csoportokhoz adhat meg Azure Active Directory (Azure AD).

Miután beállított egy csoportot a lejárat után:

- A csoport tulajdonosai értesítést kapnak a csoport megújításáról a közeljövőben
- A nem megújított csoportok törlődnek
- A törölt Office 365-csoportok 30 napon belül visszaállíthatók a csoport tulajdonosai vagy a rendszergazda

Jelenleg csak egy lejárati szabályzat konfigurálható egy bérlő Office 365-csoportjaihoz.

> [!NOTE]
> Az Office 365-csoportok elévülési szabályzatának konfigurálása és használata megköveteli, hogy az összes olyan csoport tagjaihoz, amelyekhez a lejárati szabályzat érvényes, de ne rendeljen hozzá prémium szintű Azure AD licenceket.

További információ az Azure AD PowerShell-parancsmagok letöltéséről és telepítéséről: [Azure Active Directory PowerShell a Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="roles-and-permissions"></a>Szerepkörök és engedélyek

Az alábbi szerepkörökkel konfigurálható és használható az Office 365-csoportok elévülése az Azure AD-ben.

Role | Engedélyek
-------- | --------
Globális rendszergazda vagy felhasználói rendszergazda | Az Office 365-csoportok lejárati szabályzatának beállításainak létrehozása, olvasása, frissítése vagy törlése<br>Bármely Office 365-csoport megújítható
Felhasználó | Megújíthat olyan Office 365-csoportot, amely a saját tulajdonában van<br>Visszaállíthat egy olyan Office 365-csoportot, amely a saját tulajdonában van<br>A lejárati szabályzat beállításainak olvasása

A törölt csoportok visszaállítására vonatkozó engedélyekkel kapcsolatos további információkért lásd: [törölt Office 365-csoport visszaállítása Azure Active Directoryban](groups-restore-deleted.md).

## <a name="set-group-expiration"></a>Csoportok elévülésének beállítása

1. Nyissa meg az [Azure ad felügyeleti központot](https://aad.portal.azure.com) egy olyan fiókkal, amely globális rendszergazda az Azure ad-szervezetben.

2. Válassza a **csoportok**lehetőséget, majd válassza a **lejárat** lehetőséget a lejárat beállításainak megnyitásához.
  
   ![Csoportok lejárati beállításai](./media/groups-lifecycle/expiration-settings.png)

3. A **lejárat** lapon a következőket végezheti el:

  - Állítsa be a csoport élettartamát napokban. Kiválaszthatja az egyik előre definiált értéket vagy egy egyéni értéket (31 napos vagy ennél több értéknek kell lennie).
  - Itt adhatja meg azt az e-mail címet, ahol a megújítási és lejárati értesítéseket el kell juttatni, ha egy csoportnak nincs tulajdonosa.
  - Válassza ki az Office 365-csoportok érvényességét. A következőhöz adhatja meg a lejárat időtartamát:
    - **Az összes** Office 365-csoportok
    - A **kiválasztott** Office 365-csoportok listája
    - **Nincs** az összes csoport lejáratának korlátozása
  - Ha elkészült, mentse a beállításokat a **Mentés gombra**kattintva.

> [!NOTE]
> Amikor először állítja be a lejáratot, a lejárati időintervallumnál régebbi csoportok a lejárat után 30 napig vannak beállítva, kivéve, ha a tulajdonos megújítja. Az első megújítási értesítő e-mailt egy napon belül küldi el a rendszer.
>
> Dinamikus csoport törlésekor és visszaállításakor a rendszer új csoportként tekinti meg, és a szabálynak megfelelően újra feltölti őket. Ez a folyamat akár 24 órát is igénybe vehet.
>
> A csapatokban használt csoportok lejárati megjegyzései megjelennek a csapatok tulajdonosai hírcsatornában.

## <a name="email-notifications"></a>E-mail értesítés

Az e-mailes értesítéseket, például az Office 365 csoport tulajdonosainak 30 napot, 15 napot és 1 nappal a csoport lejáratát megelőzően küldi el a rendszer. Az e-mailek nyelvét a csoportok tulajdonosának előnyben részesített nyelve vagy az Azure AD nyelvi beállítása határozza meg. Ha a csoport tulajdonosa definiált egy előnyben részesített nyelvet, vagy több tulajdonosnak azonos az előnyben részesített nyelve, akkor a rendszer ezt a nyelvet használja. Minden más esetben az Azure AD nyelvi beállítását használja a rendszer.

![Lejárati e-mail értesítések](./media/groups-lifecycle/expiration-notification.png)

A csoport **tulajdonosai értesítési** e-mailben a csoport tulajdonosainak közvetlenül érhetik el a csoport részletei lapot a hozzáférési panelen. Ebben az esetben a felhasználók további információkat kaphatnak a csoportról, például a leírásáról, a legutóbbi megújítás időpontját, a lejárat idejét, valamint a csoport megújításának lehetőségét is. A csoport részletei lap az Office 365-csoport erőforrásaira mutató hivatkozásokat is tartalmaz, így a csoport tulajdonosa kényelmesen megtekintheti a csoport tartalmát és tevékenységeit.

Ha egy csoport lejár, a csoport egy nappal a lejárati dátum után törlődik. Egy e-mailes értesítést küld az Office 365 csoport tulajdonosainak, amely tájékoztatja őket az Office 365-csoport lejáratáról és későbbi törléséről.

![E-mail értesítések csoport törlése](./media/groups-lifecycle/deletion-notification.png)

A csoport a törléstől számított 30 napon belül visszaállítható, ha a **visszaállítási csoportot** vagy a PowerShell-parancsmagokat használja a [törölt Office 365-csoport visszaállítása Azure Active Directoryban](groups-restore-deleted.md)című témakörben leírtak szerint. Vegye figyelembe, hogy a 30 napos csoportos visszaállítási időszak nem szabható testre.

Ha a visszaállítani kívánt csoport dokumentumokat, SharePoint-webhelyeket vagy más állandó objektumokat tartalmaz, akár 24 órát is igénybe vehet, hogy teljesen visszaállítsa a csoportot és annak tartalmát.

## <a name="how-to-retrieve-office-365-group-expiration-date"></a>Az Office 365-csoport lejárati dátumának lekérése
A hozzáférés panelen kívül, ahol a felhasználók megtekinthetik a csoport részleteit, beleértve a lejárati dátumot és az utolsó megújított dátumot, az Office 365-csoport lejárati dátuma beolvasható Microsoft Graph REST API Beta-ból. a expirationDateTime tulajdonság engedélyezve van Microsoft Graph Beta-ban. Lekérhető GET kéréssel. További részletekért tekintse meg [ezt a példát](https://docs.microsoft.com/graph/api/group-get?view=graph-rest-beta#example).

> [!NOTE]
> A csoporttagságok a hozzáférési panelen való kezeléséhez a "nem" értékre kell állítani a "hozzáférés a csoportok számára a hozzáférési panelen" beállítást a Azure Active Directory csoportok általános beállításában.

## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Hogyan működik az Office 365-csoport lejárata a jogi úton lévő postafiókkal
Ha egy csoport lejár, és törlődik, akkor 30 nappal azután, hogy törli a csoport adatait olyan alkalmazásokból, mint a Planner, a helyek vagy a csapatok véglegesen törölve lettek, de a jogi úton tartott csoportos postaláda megmarad, és nem véglegesen törlődik. A rendszergazda az Exchange-parancsmagok használatával visszaállíthatja a postaládát az adatlekérdezéshez. 

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Hogyan működik az Office 365 Group lejárata az adatmegőrzési szabályzattal
Az adatmegőrzési szabályt a biztonsági és megfelelőségi központ konfigurálja. Ha adatmegőrzési szabályzatot állított be az Office 365-csoportokhoz, a csoport lejárata és törlése után a csoport postaládájában és a csoportba tartozó fájlokban lévő csoportok beszélgetéseit a rendszer megőrzi az adatmegőrzési tárolóban az adatmegőrzéshez megadott számú napon belül. politika. A felhasználók a lejárat után nem látják a csoportot vagy annak tartalmát, de az e-Discovery segítségével helyreállíthatja a helyet és a postaláda-adatokat.

## <a name="powershell-examples"></a>PowerShell-példák
Íme néhány példa arra, hogyan használhatók a PowerShell-parancsmagok az Office 365-csoportok lejárati beállításainak konfigurálásához az Azure AD-szervezetben:

1. Telepítse a PowerShell 2.0 modult, és jelentkezzen be a PowerShell parancssorába:

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. A lejárati beállítások konfigurálása a New-AzureADMSGroupLifecyclePolicy parancsmaggal állíthatja be az Azure AD-szervezet összes Office 365-csoportjának élettartamát 365 napra. A tulajdonosokkal nem rendelkező Office 365-emailaddress@contoso.comcsoportok megújítási értesítései a következőre lesznek küldve:
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. A Get-AzureADMSGroupLifecyclePolicy meglévő házirend beolvasása: Ez a parancsmag lekérdezi az Office 365-csoport jelenleg konfigurált lejárati beállításait. Ebben a példában a következőket láthatja:

   - A szabályzat azonosítója
   - Az Azure AD-szervezet összes Office 365-csoportjának élettartama 365 nap
   - A tulajdonosokkal nem rendelkező Office 365-csoportok megújítási értesítéseiemailaddress@contoso.coma következőre lesznek küldve: "."
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. A meglévő házirend-AzureADMSGroupLifecyclePolicy frissítése: Ez a parancsmag egy meglévő szabályzat frissítésére szolgál. Az alábbi példában a csoport élettartama a meglévő szabályzatban 365 nap és 180 nap között változik.
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. Adjon hozzá meghatározott csoportokat a szabályzat Add-AzureADMSLifecyclePolicyGroup: Ez a parancsmag egy csoportot hoz létre az életciklus-szabályzathoz. Példa:
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. Távolítsa el a meglévő Policy Remove-AzureADMSGroupLifecyclePolicy: Ez a parancsmag törli az Office 365 csoport lejárati beállításait, de megköveteli a szabályzat AZONOSÍTÓját. Ez letiltja az Office 365-csoportok elévülését.
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
A következő parancsmagok segítségével konfigurálhatja a szabályzatot részletesebben. További információ: PowerShell- [dokumentáció](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups).

- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Get-AzureADMSGroupLifecyclePolicy
- Set-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>További lépések

Ezek a cikkek további információkat nyújtanak az Azure AD-csoportokról.

- [Meglévő csoportok megtekintése](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Csoportbeállítások kezelése](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Csoporttagok kezelése](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Csoporttagságok kezelése](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [A csoportban lévő felhasználók dinamikus szabályainak kezelése](groups-dynamic-membership.md)
