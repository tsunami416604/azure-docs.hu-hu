---
title: Az Azure Active Directory csoportokat az Office 365 lejárati |} Microsoft Docs
description: Az Office 365-csoportok az Azure Active Directoryban lejárati beállítása
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: 1756ef0a1614c69d9490899969fc78c67b9e086f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Az Office 365-csoportok-elévülési szabályzatának konfigurálása

Most kezelheti az Office 365-csoportok életciklusát számukra egy lejárati házirend beállításával. Csak Office 365-csoportok az Azure Active Directory (Azure AD) a lejárati házirendet állíthat be. 

Ha egyszer már megadta egy csoport lejárati dátuma:
-   A csoport tulajdonosainak értesítést kap, hogy újítsa meg a csoport a lejárati megújítására
-   Bármely csoport, amely nem hosszabbítja meg törlődik.
-   Minden Office 365-csoportot, amely törlődik csoport tulajdonosai vagy a rendszergazda által visszaállítható számított 30 napon belül

> [!NOTE]
> Konfigurálása és a lejárati házirendet az Office 365-csoportok használata szükséges, hogy az aktuális Azure AD Premium licenccel rendelkezik, amely a lejárati házirend érvényben van a csoportok összes tagja.

Töltse le és telepítse a Azure AD PowerShell-parancsmagokkal kapcsolatos információkért lásd: [Azure Active Directory PowerShell diagramhoz 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="roles-and-permissions"></a>Szerepkörök és engedélyek
Az alábbiakban a szerepköröket, amelyek konfigurálhatja és használhatja a lejárati az Office 365-csoportok az Azure ad-ben.

Szerepkör | Engedélyek
-------- | --------
Globális vagy felhasználói fiók rendszergazdája | Létrehozása, olvasása, frissítése, és az Office 365 csoportok lejárati házirend-beállítások törlése<br>Minden Office 365 csoport megújíthatják
Felhasználó | Az Office 365 csoport saját megújíthatják<br>Az Office 365 csoport saját állíthatja vissza.<br>Elolvashatják a lejárati házirend-beállítások

Engedélyek visszaállítani a törölt csoportban lévő további információkért lásd: [visszaállítása egy törölt Office 365-csoport az Azure Active Directoryban](active-directory-groups-restore-azure-portal.md).

## <a name="set-group-expiration"></a>Set csoport lejárata

1. Nyissa meg a [az Azure AD felügyeleti központban](https://aad.portal.azure.com) egy olyan fiókkal, amely az Azure AD-bérlő globális rendszergazdája.

2. Válassza ki **csoportok**, majd jelölje be **lejárati** a lejárati beállításainak megnyitásához.
  
  ![Lejárati panel](./media/active-directory-groups-lifecycle-azure-portal/expiration-settings.png)

4. Az a **lejárati** panelen is:

  * A csoport élettartamának beállítása napban. Kiválaszthatja az egyik előre beállított értékek, vagy egy egyéni érték (kell 31 nap vagy több). 
  * Adjon meg egy e-mail címet, ahol a megújításhoz és a lejárati értesítést küldjön Ha egy csoport nincs tulajdonosa. 
  * Válassza ki, melyik Office 365-csoportok lejár. Engedélyezheti a lejárati **összes** Office 365-csoportokat, ha szeretné, csak engedélyezése **kijelölt** Office 365-csoportokat, vagy válassza ki a **nincs** letiltása az összes csoport lejárata .
  * Mentse a beállításokat, amikor elkészült, kiválasztásával **mentése**.


Erre például értesítő e-mailek küldése az Office 365 csoport tulajdonosainak a 30 nap, 15 nap, és a csoport lejárta előtt 1 nap.

![Lejárati értesítést](./media/active-directory-groups-lifecycle-azure-portal/expiration-notification.png)

Az a **megújítási csoport** értesítő e-mailt, csoport tulajdonosainak közvetlenül is elérni a csoport részleteit megjelenítő oldalon a hozzáférési panelen. Van a felhasználók további információkat a csoport például annak leírását, ha az utolsó megújított, a lejárat idejét, valamint is újítsa meg a csoport olyan szeretne kapni. A csoport részleteit megjelenítő oldalon most is az Office 365 csoport forrásokra mutató hivatkozásokat, hogy a csoport tulajdonosa kényelmesen megtekintheti a tartalom és a tevékenység a csoportban.

Amikor lejár egy csoportot, a csoport egy napot a lejárat után törlődik. Például az e-mailben értesítést kap az Office 365 csoportházirend-tulajdonosok értesítheti őket a lejárati és az Office 365 csoport későbbi törlését.

![Csoport törlése e-mail-értesítések](./media/active-directory-groups-lifecycle-azure-portal/deletion-notification.png)

A csoport kiválasztásával visszaállítható a törlés számított 30 napon belül **visszaállítása** vagy a PowerShell-parancsmagok használatával [visszaállítása egy törölt Office 365-csoport az Azure Active Directoryban](active-directory-groups-restore-azure-portal.md).
    
Ha most visszaállítása a csoport tartalmazza a dokumentumok, SharePoint-webhelyeken vagy más állandó objektumok, a csoportot és annak tartalmát teljesen visszaállítására akár 24 óráig is eltarthat.

> [!NOTE]
> * Amikor először állítsa be a lejárati, régebbi, mint a lejárati időköz csoportok lejáratig 30 nap vannak állítva. Az első megújítási értesítő e-mailt által kiküldött egy napon belül. 
>   Például csoport 400 nappal ezelőtt lett létrehozva, és a lejárati időköz értéke 180 nap. Lejárati beállítások alkalmazásakor csoport rendelkezik 30 nap elteltével törlődik, kivéve, ha a tulajdonos megújítja azt.
> * Jelenleg csak egy elévülési szabályzatának konfigurálhatók a bérlő az Office 365-csoportokat.
> * Ha egy dinamikus csoport törlése és visszaállítása, látható egy új csoportot, és újra fel az a szabály alapján. Ez a folyamat akár 24 óráig is eltarthat.

## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Office 365 csoport lejárati működése postafiók jogilag előírt megőrzést rendel
Amikor egy csoport lejár, és el kell hagyni, majd 30 nappal a törlés után a csoport adatokat az alkalmazásokból, például Planner, helyek, csapatok véglegesen törlődik, de a csoport-postaládát jogilag előírt megőrzést rendel marad, és nem véglegesen törlődik. A rendszergazda az Exchange-parancsmagok használatával állítsa helyre a postaládát az adatok beolvasása. 

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Office 365 csoport lejárati adatmegőrzési működése
Az adatmegőrzési a biztonsági és megfelelőségi központ van konfigurálva. Állított be egy megőrzési házirend, az Office 365-csoportokat, amikor egy csoport lejár, és törlődik, ha a csoport témák a csoport postaládában és a csoport hely fájlok kerülnek be a megőrzési tároló az adott számú napig az adatmegőrzési meghatározott házirend. A felhasználók elteltével láthatják a csoport vagy a benne lévő tartalom lejárata után, de a hely és a postaláda adatok elektronikus felderítés keresztül állíthatja helyre.

## <a name="powershell-examples"></a>PowerShell-példák
Az alábbiakban használatát PowerShell-parancsmagok lejárati beállításait az Office 365-csoportok az Ön bérlőjében példái:

1. Telepítse a PowerShell 2.0 előzetes modult (2.0.0.137), és jelentkezzen be a PowerShell-parancssorba:
  ````
  Install-Module -Name AzureADPreview
  connect-azuread 
  ````
2. A lejárati beállítások New-AzureADMSGroupLifecyclePolicy: Ez a parancsmag élettartama beállítja az összes Office 365 csoport – 365 nap a bérlőben. Megújítási értesítések az Office 365 csoportok nélkül tulajdonosok kapnak "emailaddress@contoso.com"
  
  ````
  New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
  ````
3. A Get-AzureADMSGroupLifecyclePolicy meglévő házirend beolvasása: Ez a parancsmag lekéri az aktuális Office 365 csoport lejárati beállítások vannak konfigurálva. Ebben a példában látható:
  * A házirend-azonosító 
  * Az összes Office 365-csoportok a bérlő élettartama – 365 nap van beállítva.
  * Megújítási értesítések az Office 365 csoportok nélkül tulajdonosok kapnak "emailaddress@contoso.com."
  
  ````
  Get-AzureADMSGroupLifecyclePolicy
  
  ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
  --                                    ------------------- ----------------- ---------------------------
  26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
  ```` 
   
4. A Set-AzureADMSGroupLifecyclePolicy meglévő házirend frissítése: Ez a parancsmag használatával frissítse a meglévő házirend. Az alábbi példában a meglévő házirendben lévő csoport élettartama 180 nap módosul 365 nap. 
  
  ````
  Set-AzureADMSGroupLifecyclePolicy -Id “26fcc232-d1c3-4375-b68d-15c296f1f077”   -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
  ````
  
5. Adjon hozzá adott csoportok az Add-AzureADMSLifecyclePolicyGroup csoportházirend: Ez a parancsmag egy csoport hozzáadása a életciklusra vonatkozó szabályzata. Példa: 
  
  ````
  Add-AzureADMSLifecyclePolicyGroup -Id “26fcc232-d1c3-4375-b68d-15c296f1f077” -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
  ````
  
6. Távolítsa el a meglévő házirend eltávolítása AzureADMSGroupLifecyclePolicy: Ez a parancsmag törli az Office 365 lejárati-beállításokat, de van szükség a házirend-azonosítóhoz. Ezzel a lépéssel letiltja az Office 365-csoportok lejárati idejét. 
  
  ````
  Remove-AzureADMSGroupLifecyclePolicy -Id “26fcc232-d1c3-4375-b68d-15c296f1f077”
  ````
  
A következő parancsmag segítségével további információkhoz juthat a házirendek konfigurálásához. Vonatkozó tudnivalókat, lásd: [PowerShell dokumentációs](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups).

* Get-AzureADMSGroupLifecyclePolicy
* New-AzureADMSGroupLifecyclePolicy
* Get-AzureADMSGroupLifecyclePolicy
* Set-AzureADMSGroupLifecyclePolicy
* Remove-AzureADMSGroupLifecyclePolicy
* Add-AzureADMSLifecyclePolicyGroup
* Remove-AzureADMSLifecyclePolicyGroup
* Reset-AzureADMSLifeCycleGroup   
* Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>További lépések
Ezek a cikkek kiegészítő információt nyújt az Azure Active Directory csoportokat.

* [Tekintse meg a meglévő csoportok](active-directory-groups-view-azure-portal.md)
* [Egy csoport beállításainak kezelése](active-directory-groups-settings-azure-portal.md)
* [A csoport tagjai kezelése](active-directory-groups-members-azure-portal.md)
* [A csoport tagságát kezelése](active-directory-groups-membership-azure-portal.md)
* [A csoport dinamikus szabályok kezelése](active-directory-groups-dynamic-membership-azure-portal.md)
