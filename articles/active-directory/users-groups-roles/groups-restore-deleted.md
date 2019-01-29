---
title: Törölt Office 365-csoport visszaállítása az Azure AD-ben | Microsoft Docs
description: A cikkből megtudhatja, hogyan állíthat vissza egy törölt csoportot, tekintheti meg a visszaállítható csoportokat, valamint törölhet véglegesen egy csoportot az Azure Active Directoryban.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: quickstart
ms.date: 08/28/2017
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: ef18897d00e29a21dd602dc7213706bfc30a68bc
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55179378"
---
# <a name="restore-a-deleted-office-365-group-in-azure-active-directory"></a>Törölt Office 365-csoport visszaállítása az Azure Active Directoryban

Amikor töröl egy Office 365-csoportot az Azure Active Directoryban (Azure AD), a törlés dátumától számított 30 napig a törölt csoport megmarad, azonban nem látható. Ennek célja az, hogy a csoport és tartalma szükség esetén visszaállítható legyen. Ez a funkció kizárólag Azure AD-ben használt Office 365-csoportokra vonatkozik. Nem használható biztonsági csoportok és terjesztési csoportok esetén.

> [!NOTE]
> Ne használja a `Remove-MsolGroup` parancsot, mert azzal véglegesen törli a csoportot. O365-csoport törléséhez mindig a `Remove-AzureADMSGroup` parancsot használja.

A csoportok visszaállításához szükséges engedély az alábbiak bármelyike lehet:

Szerepkör | Engedélyek
--------- | ---------
Vállalati rendszergazda, 2. szintű partnerek támogatása és az InTune szolgáltatás-rendszergazdái | A törölt Office 365-csoportok bármelyikét visszaállíthatják.
Felhasználói fiókok rendszergazdája és 1. szintű partnerek támogatása | A törölt Office 365-csoportok bármelyikét visszaállíthatják, a vállalati rendszergazda szerepkörhöz rendelt csoportokat kivéve.
Felhasználó | A korábban a tulajdonában álló törölt Office 365-csoportokat állíthatja vissza.


## <a name="view-the-deleted-office-365-groups-that-are-available-to-restore"></a>A visszaállítható törölt Office 365-csoportok megtekintése

Az alábbi parancsmagokkal tekintheti meg a törölt csoportokat annak ellenőrzéséhez, hogy a kívánt csoport vagy csoportok végleges törlése nem történt-e még meg. A parancsmagok az [Azure AD PowerShell-modul](https://www.powershellgallery.com/packages/AzureAD/) részét képezik. A modullal kapcsolatban az [Azure Active Directory PowerShell 2-es verzióját](/powershell/azure/install-adv2?view=azureadps-2.0) ismertető cikkben tekinthet meg további információt.

1.  Az alábbi parancsmag futtatásával jelenítheti meg a bérlő összes törölt, még visszaállítható Office 365-csoportját.
   
  ```
  Get-AzureADMSDeletedGroup
  ```

2.  Ha ismeri egy adott csoport objektumazonosítóját (amelyet egyébként az 1. lépésben található parancsmag kimenetében láthat), az alábbi parancsmag futtatásával ellenőrizheti, hogy az adott törölt csoport végleges törlése megtörtént-e már.
  
  ```
  Get-AzureADMSDeletedGroup –Id <objectId>
  ```

## <a name="how-to-restore-your-deleted-office-365-group"></a>Törölt Office 365-csoport visszaállítása
Miután ellenőrizte, hogy a csoport visszaállítható-e, az alábbi lépések valamelyikét végrehajtva állíthatja vissza a törölt csoportot. Ha a csoport dokumentumokat, SP-webhelyeket vagy más állandó objektumokat tartalmaz, a csoport és a tartalmainak teljes visszaállítása akár 24 órát is igénybe vehet.

1.  A csoport és a tartalmainak visszaállításához futtassa az alábbi parancsmagot.
 
 ```
 Restore-AzureADMSDeletedDirectoryObject –Id <objectId>
 ``` 

A törölt csoport az alábbi parancsmag futtatásával távolítható el véglegesen.
 ```
 Remove-AzureADMSDeletedDirectoryObject –Id <objectId>
 ```

## <a name="how-do-you-know-this-worked"></a>Hogyan ellenőrizhető a visszaállítás sikeressége?

Az Office 365-csoport visszaállításának sikerességét a `Get-AzureADGroup –ObjectId <objectId>` parancsmag futtatásával ellenőrizheti, amely a csoporttal kapcsolatos információkat jeleníti meg. A visszaállítási kérés befejeződését követően:

- A csoport megjelenik az Exchange bal oldali navigációs sávján.
- A csoportra vonatkozó terv megjelenik a Planner alkalmazásban.
- Minden olyan SharePoint webhelyet, és a tartalom mindegyikét érhetők el
- A csoport elérhető lesz bármelyik Exchange-végpontról, illetve az Office 365-csoportokat támogató egyéb Office 365 számítási feladatokból.

## <a name="next-steps"></a>További lépések
E cikkekben további információk találhatók az Azure Active Directory-csoportokkal kapcsolatban.

* [Meglévő csoportok megtekintése](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Csoportbeállítások kezelése](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Csoporttagok kezelése](../fundamentals/active-directory-groups-members-azure-portal.md)
* [Csoporttagságok kezelése](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [A csoportban lévő felhasználók dinamikus szabályainak kezelése](groups-dynamic-membership.md)
