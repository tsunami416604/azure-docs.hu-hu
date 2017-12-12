---
title: "Állítsa vissza az Azure Active Directory törölt Office 365 csoport |} Microsoft Docs"
description: "Hogyan lehet visszaállítani a törölt csoportban, visszaállítható csoportok megtekintése és az Azure Active Directory csoport permamnently törlése"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: cc5f232a-1e77-45c2-b28b-1fcb4621725c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: 66b76b9bfd63f270c1bb86454908e2bae0897d04
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="restore-a-deleted-office-365-group-in-azure-active-directory"></a>Állítsa vissza egy törölt Office 365-csoport az Azure Active Directoryban

Ha töröl egy Office 365-csoport az Azure Active Directory (Azure AD), a törölt csoportban megtartott, de nem látható 30 napig a törlés dátumát. Ez az, hogy a csoport és annak tartalmát is állítható vissza, ha szükséges. Ez a funkció korlátozott kizárólag Office 365-csoportok az Azure ad-ben. Nincs elérhető biztonsági és terjesztési csoportok.

> [!NOTE] 
> Ne használjon `Remove-MsolGroup` mert véglegesen üríti azt a csoportot. Mindig `Remove-AzureADMSGroup` az Office 365-csoport törléséhez. 

Egy csoport visszaállításához szükséges engedélyek a következő lehet:

Szerepkör  | Engedélyek 
--------- | ---------
Vállalati rendszergazda, a Partner Tier2 támogatása és az InTune szolgáltatás-rendszergazdák | Visszaállíthatja a törölt Office 365-csoport 
Felhasználói fiók rendszergazdájához, és a Partner Tier1 támogatása | A vállalati rendszergazda szerepkörhöz hozzárendelt kivételével minden törölt Office 365 csoport állíthatja vissza. 
Felhasználó | Bármely törölt Office 365-csoport, amelynek azok tulajdonosa állíthatja vissza. 


## <a name="view-the-deleted-office-365-groups-that-are-available-to-restore"></a>A törölt Office 365-csoportokat, amelyek segítségével megtekintése
Az alábbi parancsmagok segítségével ellenőrizheti, hogy az egy vagy érdekli a meglévők közül nem még véglegesen eltávolításuk törölt csoportok megtekintése. Ezek a parancsmagok részét képezik a [Azure AD PowerShell modult](https://www.powershellgallery.com/packages/AzureAD/). Ez a modul kapcsolatos további információk találhatók a [Azure Active Directory PowerShell 2-es verzió](/powershell/azure/install-adv2?view=azureadps-2.0) cikk.

1.  Futtassa az alábbi parancsmagot, az összes törölt Office 365-csoportok megjelennek a bérlő visszaállítása továbbra is elérhető.
  ```
  Get-AzureADMSDeletedGroup
  ```

2.  A másik lehetőség Ha tudja, egy adott csoport objektumazonosító (és lekérheti a parancsmag az 1. lépésben), futtassa az alábbi parancsmagot, ellenőrizheti, hogy az adott törölt csoportban nem még véglegesen törölve lettek.
  ```
  Get-AzureADMSDeletedGroup –Id <objectId>
  ```



## <a name="how-to-restore-your-deleted-office-365-group"></a>Hogyan lehet visszaállítani a törölt Office 365-csoport
Miután ellenőrizte, hogy a csoport visszaállítása továbbra is elérhető, állítsa vissza a törölt csoportban található, az alábbi lépések egyikét. A csoport tartalmazza a dokumentumok, SP helyek vagy más állandó objektumok, ha egy csoportot és annak tartalmát teljesen visszaállítására akár 24 óráig is eltarthat.

1.  Futtassa az alábbi parancsmagot visszaállítása a csoportot és annak tartalmát.
  
  ```
  Restore-AzureADMSDeletedDirectoryObject –Id <objectId>
  ``` 

Alternatív megoldásként a következő parancsmag futtatásával végleg eltávolítani a törölt csoportban.
  ```
  Remove-AzureADMSDeletedDirectoryObject –Id <objectId>
  ```

## <a name="how-do-you-know-this-worked"></a>Honnan tudhatja ez dolgozott?
Győződjön meg arról, hogy sikeresen már visszaállította az Office 365-csoportok, futtassa a `Get-AzureADGroup –ObjectId <objectId>` parancsmag használatával jelenítse meg a-csoport információit. A visszaállítás után kérelem teljesítése:
- A csoport megjelenik a bal oldali navigációs sávban a Exchange
- Planner jelennek meg a csoport tervezése
- Minden olyan Sharepoint webhelyet, és minden, a tartalom elérhető lesz
- A csoport érhetők el az Exchange-végpontok és más Office 365 számítási feladattal, amely támogatja az Office 365-csoportokat


## <a name="next-steps"></a>Következő lépések
Ezek a cikkek további információkkal az Azure Active Directory csoportokat.

* [Tekintse meg a meglévő csoportok](active-directory-groups-view-azure-portal.md)
* [Egy csoport beállításainak kezelése](active-directory-groups-settings-azure-portal.md)
* [A csoport tagjai kezelése](active-directory-groups-members-azure-portal.md)
* [A csoport tagságát kezelése](active-directory-groups-membership-azure-portal.md)
* [A csoport dinamikus szabályok kezelése](active-directory-groups-dynamic-membership-azure-portal.md)
