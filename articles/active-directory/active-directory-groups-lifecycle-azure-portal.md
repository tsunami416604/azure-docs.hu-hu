---
title: "Tekintse meg az Azure Active Directoryban Office 365-csoportok lejárati |} Microsoft Docs"
description: "Az Office 365-csoportok az Azure Active Directoryban (előzetes verzió) lejárati beállítása"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: 8a43df84fd050d7b4bd8d937b8c55e744cb805d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-office-365-groups-expiration-preview"></a>Office 365 csoportok lejárati (előzetes verzió) konfigurálása

Most kezelheti az Office 365-csoportok életciklusát lejárati kiválasztott az Office 365-csoportokra vonatkozó beállításával. Miután a lejárati be van állítva, a újítsa meg a csoporthoz, ha még mindig van szükségük a csoportok a rendszer felkéri a tulajdonosok csoport. Minden Office 365-csoportot, amely nem hosszabbítja meg törlődni fog. Minden Office 365-csoportot, amely törölve lett a csoportházirend-tulajdonosok vagy a rendszergazda által számított 30 napon belül állítható vissza.  


> [!NOTE]
> Beállíthatja, hogy csak az Office 365-csoportok lejárati idejét.
>
> Az Office 365-csoportok lejárati beállításához, hogy be van-e rendelve egy Azure AD Premium-licenc
>   - A rendszergazda, aki a bérlő a lejárati beállítások konfigurálása
>   - Ez a beállítás a kijelölt csoportok összes tagja

## <a name="set-office-365-groups-expiration"></a>Állítsa be az Office 365 csoportok lejárata

1. Nyissa meg a [az Azure AD felügyeleti központban](https://aad.portal.azure.com) egy olyan fiókkal, amely az Azure AD-bérlő globális rendszergazdája.

2. Nyissa meg az Azure AD, válassza ki **felhasználók és csoportok**.

3. Válassza ki **-beállítások** majd **lejárati** a lejárati beállításainak megnyitásához.
  
  ![Lejárati panel](./media/active-directory-groups-lifecycle-azure-portal/expiration-settings.png)

4. Az a **lejárati** panelen is:

  * A csoport élettartamának beállítása napban. Kiválaszthatja az egyik előre beállított értékek, vagy egy egyéni érték (kell 31 nap vagy több). 
  * Adjon meg egy e-mail címet, ahol a megújításhoz és a lejárati értesítést küldjön Ha egy csoport nincs tulajdonosa. 
  * Válassza ki, melyik Office 365-csoportok lejár. Engedélyezheti a lejárati **összes** Office 365-csoportokat, választhatók ki az Office 365-csoportokat, vagy választja **nincs** letiltása az összes csoport lejárati.
  * Mentse a beállításokat, amikor elkészült, kiválasztásával **mentése**.

Hogyan töltse le és telepítse a Microsoft PowerShell-modult az Office 365-csoportok PowerShell lejárati konfigurálása, lásd: [Azure Active Directory V2 PowerShell-modul - nyilvános előzetes 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

Erre például értesítő e-mailek küldése az Office 365 csoport tulajdonosainak a 30 nap, 15 nap, és a csoport lejárta előtt 1 nap.

![Lejárati értesítést](./media/active-directory-groups-lifecycle-azure-portal/expiration-notification.png)

Választhatja ki a csoport tulajdonosa **megújítási csoport** megújítani az Office 365 csoportban, vagy választhatja **csoport Ugrás** , a tagok és egyéb adatait a csoport.

Amikor lejár egy csoportot, a csoport egy napot a lejárat után törlődik. Például az e-mailben értesítést kap az Office 365 csoportházirend-tulajdonosok értesítheti őket a lejárati és az Office 365 csoport későbbi törlését.

![Csoport törlése e-mail-értesítések](./media/active-directory-groups-lifecycle-azure-portal/deletion-notification.png)

A csoport kiválasztásával visszaállítható **visszaállítása** vagy a PowerShell-parancsmagok használatával [visszaállítása egy törölt Office 365 csoport az Azure Active Directoryban] leírtak (https://docs.microsoft.com/azure/active-directory/active-directory-groups-restore-azure-portal).
    
Ha most visszaállítása a csoport tartalmazza a dokumentumok, SharePoint-webhelyeken vagy más állandó objektumok, a csoportot és annak tartalmát teljesen visszaállítására akár 24 óráig is eltarthat.

> [!NOTE]
> * A lejárati beállítások telepítésekor előfordulhat néhány olyan csoportot, a régebbi, mint a lejárati időszak. Ezek a csoportok nem lehet azonnal törlődnek, de a lejáratig 30 nap értékre van beállítva. Az első megújítási értesítő e-mailt által kiküldött egy napon belül. Például csoport 400 nappal ezelőtt lett létrehozva, és a lejárati időköz értéke 180 nap. Lejárati beállítások alkalmazásakor csoport rendelkezik 30 nap elteltével törlődik, kivéve, ha a tulajdonos megújítja azt.
> * Ha egy dinamikus csoport törlése és visszaállítása, látható egy új csoportot, és újra fel az a szabály alapján. Ez a folyamat akár 24 óráig is eltarthat.

## <a name="next-steps"></a>Következő lépések
Ezek a cikkek kiegészítő információt nyújt az Azure Active Directory csoportokat.

* [Tekintse meg a meglévő csoportok](active-directory-groups-view-azure-portal.md)
* [Egy csoport beállításainak kezelése](active-directory-groups-settings-azure-portal.md)
* [A csoport tagjai kezelése](active-directory-groups-members-azure-portal.md)
* [A csoport tagságát kezelése](active-directory-groups-membership-azure-portal.md)
* [A csoport dinamikus szabályok kezelése](active-directory-groups-dynamic-membership-azure-portal.md)
