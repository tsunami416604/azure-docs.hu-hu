---
title: Restore a deleted Office 365 group - Azure AD | Microsoft Docs
description: How to restore a deleted group, view restorable groups, and permanently delete a group in Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96d212df51a58125e3b959a18f5cf2ac9d391d30
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422375"
---
# <a name="restore-a-deleted-office-365-group-in-azure-active-directory"></a>Törölt Office 365-csoport visszaállítása az Azure Active Directoryban

Amikor töröl egy Office 365-csoportot az Azure Active Directoryban (Azure AD), a törlés dátumától számított 30 napig a törölt csoport megmarad, azonban nem látható. Ennek célja az, hogy a csoport és tartalma szükség esetén visszaállítható legyen. Ez a funkció kizárólag Azure AD-ben használt Office 365-csoportokra vonatkozik. Nem használható biztonsági csoportok és terjesztési csoportok esetén. Please note that the 30-day group restoration period is not customizable.

> [!NOTE]
> Ne használja a `Remove-MsolGroup` parancsot, mert azzal véglegesen törli a csoportot. Always use `Remove-AzureADMSGroup` to delete an Office 365 group.

A csoportok visszaállításához szükséges engedély az alábbiak bármelyike lehet:

Szerepkör | Engedélyek
--------- | ---------
Global administrator, Group administrator, Partner Tier2 support, and Intune administrator | A törölt Office 365-csoportok bármelyikét visszaállíthatják.
User administrator and Partner Tier1 support | Can restore any deleted Office 365 group except those groups assigned to the Company Administrator role
Felhasználó | Can restore any deleted Office 365 group that they own

## <a name="view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore"></a>View and manage the deleted Office 365 groups that are available to restore

1. Sign in to the [Azure AD admin center](https://aad.portal.azure.com) with a User administrator account.

2. Select **Groups**, then select **Deleted groups** to view the deleted groups that are available to restore.

    ![view groups that are available to restore](media/groups-lifecycle/deleted-groups3.png)

3. On the **Deleted groups** blade, you can:

   - Restore the deleted group and its contents by selecting **Restore group**.
   - Permanently remove the deleted group by selecting **Delete permanently**. To permanently remove a group, you must be an administrator.

## <a name="view-the-deleted-office-365-groups-that-are-available-to-restore-using-powershell"></a>View the deleted Office 365 groups that are available to restore using Powershell

Az alábbi parancsmagokkal tekintheti meg a törölt csoportokat annak ellenőrzéséhez, hogy a kívánt csoport vagy csoportok végleges törlése nem történt-e még meg. A parancsmagok az [Azure AD PowerShell-modul](https://www.powershellgallery.com/packages/AzureAD/) részét képezik. A modullal kapcsolatban az [Azure Active Directory PowerShell 2-es verzióját](/powershell/azure/install-adv2?view=azureadps-2.0) ismertető cikkben tekinthet meg további információt.

1.  Az alábbi parancsmag futtatásával jelenítheti meg a bérlő összes törölt, még visszaállítható Office 365-csoportját.
   

    ```powershell
    Get-AzureADMSDeletedGroup
    ```

2.  Ha ismeri egy adott csoport objektumazonosítóját (amelyet egyébként az 1. lépésben található parancsmag kimenetében láthat), az alábbi parancsmag futtatásával ellenőrizheti, hogy az adott törölt csoport végleges törlése megtörtént-e már.

    ```
    Get-AzureADMSDeletedGroup –Id <objectId>
    ```

## <a name="how-to-restore-your-deleted-office-365-group-using-powershell"></a>How to restore your deleted Office 365 group using Powershell

Miután ellenőrizte, hogy a csoport visszaállítható-e, az alábbi lépések valamelyikét végrehajtva állíthatja vissza a törölt csoportot. Ha a csoport dokumentumokat, SP-webhelyeket vagy más állandó objektumokat tartalmaz, a csoport és a tartalmainak teljes visszaállítása akár 24 órát is igénybe vehet.

1. A csoport és a tartalmainak visszaállításához futtassa az alábbi parancsmagot.
 

   ```
    Restore-AzureADMSDeletedDirectoryObject –Id <objectId>
    ``` 

2. A törölt csoport az alábbi parancsmag futtatásával távolítható el véglegesen.
    

    ```
    Remove-AzureADMSDeletedDirectoryObject –Id <objectId>
    ```

## <a name="how-do-you-know-this-worked"></a>Hogyan ellenőrizhető a visszaállítás sikeressége?

Az Office 365-csoport visszaállításának sikerességét a `Get-AzureADGroup –ObjectId <objectId>` parancsmag futtatásával ellenőrizheti, amely a csoporttal kapcsolatos információkat jeleníti meg. A visszaállítási kérés befejeződését követően:

- A csoport megjelenik az Exchange bal oldali navigációs sávján.
- A csoportra vonatkozó terv megjelenik a Planner alkalmazásban.
- Any SharePoint sites and all of their contents will be available
- A csoport elérhető lesz bármelyik Exchange-végpontról, illetve az Office 365-csoportokat támogató egyéb Office 365 számítási feladatokból.

## <a name="next-steps"></a>Következő lépések

E cikkekben további információk találhatók az Azure Active Directory-csoportokkal kapcsolatban.

* [Meglévő csoportok megtekintése](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Csoportbeállítások kezelése](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Csoporttagok kezelése](../fundamentals/active-directory-groups-members-azure-portal.md)
* [Csoporttagságok kezelése](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [A csoportban lévő felhasználók dinamikus szabályainak kezelése](groups-dynamic-membership.md)
