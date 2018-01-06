---
title: "Az Azure Active Directory csoportokat az Office 365 lejárati |} Microsoft Docs"
description: "Az Office 365-csoportok az Azure Active Directoryban (előzetes verzió) lejárati beállítása"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: 6b454ed7257e8d3f91e585cee2b559c54371fb15
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2018
---
# <a name="configure-expiration-for-office-365-groups-preview"></a>Az Office 365-csoportok (előzetes verzió) lejárati konfigurálása

Kezelheti az Office 365-csoportok életciklusát lejárati szolgáltatások beállítása most. Az Azure Active Directory (Azure AD) megadható lejárati idejét csak az Office 365-csoportokat. Ha egyszer már megadta egy csoport lejárati dátuma:
-   A csoport tulajdonosainak értesítést kap, hogy újítsa meg a csoport a lejárati megújítására
-   Bármely csoport, amely nem hosszabbítja meg törlődik.
-   Minden Office 365-csoportot, amely törlődik csoport tulajdonosai vagy a rendszergazda által visszaállítható számított 30 napon belül

> [!NOTE]
> Az Office 365-csoportok lejárati beállításához az Azure AD prémium licenccel, amelyhez a csoportok minden tagját a lejárati beállítások érvényesek.

Töltse le és telepítse a Azure AD PowerShell-parancsmagokkal kapcsolatos információkért lásd: [Azure Active Directory PowerShell diagramhoz – a nyilvános előzetes 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="set-group-expiration"></a>Set csoport lejárata

1. Nyissa meg a [az Azure AD felügyeleti központban](https://aad.portal.azure.com) egy olyan fiókkal, amely az Azure AD-bérlő globális rendszergazdája.

2. Nyissa meg az Azure AD, válassza ki **felhasználók és csoportok**.

3. Válassza ki **-beállítások** majd **lejárati** a lejárati beállításainak megnyitásához.
  
  ![Lejárati panel](./media/active-directory-groups-lifecycle-azure-portal/expiration-settings.png)

4. Az a **lejárati** panelen is:

  * A csoport élettartamának beállítása napban. Kiválaszthatja az egyik előre beállított értékek, vagy egy egyéni érték (kell 31 nap vagy több). 
  * Adjon meg egy e-mail címet, ahol a megújításhoz és a lejárati értesítést küldjön Ha egy csoport nincs tulajdonosa. 
  * Válassza ki, melyik Office 365-csoportok lejár. Engedélyezheti a lejárati **összes** Office 365-csoportokat, választhatók ki az Office 365-csoportokat, vagy választja **nincs** letiltása az összes csoport lejárati.
  * Mentse a beállításokat, amikor elkészült, kiválasztásával **mentése**.


Erre például értesítő e-mailek küldése az Office 365 csoport tulajdonosainak a 30 nap, 15 nap, és a csoport lejárta előtt 1 nap.

![Lejárati értesítést](./media/active-directory-groups-lifecycle-azure-portal/expiration-notification.png)

Választhatja ki a csoport tulajdonosa **megújítási csoport** megújítani az Office 365 csoportban, vagy választhatja **csoport Ugrás** , a tagok és egyéb adatait a csoport.

Amikor lejár egy csoportot, a csoport egy napot a lejárat után törlődik. Például az e-mailben értesítést kap az Office 365 csoportházirend-tulajdonosok értesítheti őket a lejárati és az Office 365 csoport későbbi törlését.

![Csoport törlése e-mail-értesítések](./media/active-directory-groups-lifecycle-azure-portal/deletion-notification.png)

A csoport kiválasztásával visszaállítható **visszaállítása** vagy a PowerShell-parancsmagok használatával [visszaállítása egy törölt Office 365 csoport az Azure Active Directoryban] leírtak (https://docs.microsoft.com/azure/active-directory/active-directory-groups-restore-azure-portal).
    
Ha most visszaállítása a csoport tartalmazza a dokumentumok, SharePoint-webhelyeken vagy más állandó objektumok, a csoportot és annak tartalmát teljesen visszaállítására akár 24 óráig is eltarthat.

> [!NOTE]
> * Amikor először állítsa be a lejárati, régebbi, mint a lejárati időköz csoportok lejáratig 30 nap vannak állítva. Az első megújítási értesítő e-mailt által kiküldött egy napon belül. 
>   Például csoport 400 nappal ezelőtt lett létrehozva, és a lejárati időköz értéke 180 nap. Lejárati beállítások alkalmazásakor csoport rendelkezik 30 nap elteltével törlődik, kivéve, ha a tulajdonos megújítja azt.
> * Ha egy dinamikus csoport törlése és visszaállítása, látható egy új csoportot, és újra fel az a szabály alapján. Ez a folyamat akár 24 óráig is eltarthat.

## <a name="next-steps"></a>További lépések
Ezek a cikkek kiegészítő információt nyújt az Azure Active Directory csoportokat.

* [Tekintse meg a meglévő csoportok](active-directory-groups-view-azure-portal.md)
* [Egy csoport beállításainak kezelése](active-directory-groups-settings-azure-portal.md)
* [A csoport tagjai kezelése](active-directory-groups-members-azure-portal.md)
* [A csoport tagságát kezelése](active-directory-groups-membership-azure-portal.md)
* [A csoport dinamikus szabályok kezelése](active-directory-groups-dynamic-membership-azure-portal.md)
