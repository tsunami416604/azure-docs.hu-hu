---
title: Állítsa vissza egy törölt Office 365-csoport – Azure ad-ben |} A Microsoft Docs
description: Visszaállíthatók a törölt, visszaállítható csoportok megtekintése, és véglegesen törli a csoportot az Azure Active Directoryban
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
ms.openlocfilehash: 55d08ddef46c4c78452fcdbc839219b624d55c04
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666409"
---
# <a name="restore-a-deleted-office-365-group-in-azure-active-directory"></a>Törölt Office 365-csoport visszaállítása az Azure Active Directoryban

Amikor töröl egy Office 365-csoportot az Azure Active Directoryban (Azure AD), a törlés dátumától számított 30 napig a törölt csoport megmarad, azonban nem látható. Ennek célja az, hogy a csoport és tartalma szükség esetén visszaállítható legyen. Ez a funkció kizárólag Azure AD-ben használt Office 365-csoportokra vonatkozik. Nem használható biztonsági csoportok és terjesztési csoportok esetén. Ne feledje, hogy a 30 napos csoport visszaállítása időszak a nem testreszabható.

> [!NOTE]
> Ne használja a `Remove-MsolGroup` parancsot, mert azzal véglegesen törli a csoportot. Mindig használjon `Remove-AzureADMSGroup` törli az Office 365-csoportot.

A csoportok visszaállításához szükséges engedély az alábbiak bármelyike lehet:

Szerepkör | Engedélyek
--------- | ---------
Globális rendszergazda, a Partner 2. rétegbeli támogatása és az Intune-rendszergazda | A törölt Office 365-csoportok bármelyikét visszaállíthatják.
Felhasználói rendszergazda és a partnerek 1. rétegbeli támogatása | Minden törölt Office 365-csoportot, kivéve azokat, a vállalati rendszergazda szerepkörrel is helyreállíthatja.
Felhasználó | Minden törölt Office 365-csoport saját is helyreállíthatja.

## <a name="view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore"></a>Megtekintheti, és elérhető a visszaállítandó törölt Office 365-csoportok kezelése

1. Jelentkezzen be a [Azure AD felügyeleti központ](https://aad.portal.azure.com) rendszergazdai felhasználói fiókkal.

2. Válassza ki **csoportok**, majd **törölt csoportok** elérhető visszaállítani a törölt csoportok megtekintése.

    ![Állítsa vissza a rendelkezésre álló csoportok megtekintése](media/groups-lifecycle/deleted-groups3.png)

3. Az a **törölt csoportok** panelen is:

   - Állítsa vissza a törölt csoportban és annak tartalma kiválasztásával **csoport visszaállítása**.
   - Végleg eltávolítani a törölt csoportban kiválasztásával **véglegesen törli a**. Csoport végleges eltávolításához rendszergazdának kell lennie.

## <a name="view-the-deleted-office-365-groups-that-are-available-to-restore-using-powershell"></a>Elérhető a Powershell segítségével történő visszaállításhoz törölt Office 365-csoportok megtekintése
Az alábbi parancsmagokkal tekintheti meg a törölt csoportokat annak ellenőrzéséhez, hogy a kívánt csoport vagy csoportok végleges törlése nem történt-e még meg. A parancsmagok az [Azure AD PowerShell-modul](https://www.powershellgallery.com/packages/AzureAD/) részét képezik. A modullal kapcsolatban az [Azure Active Directory PowerShell 2-es verzióját](/powershell/azure/install-adv2?view=azureadps-2.0) ismertető cikkben tekinthet meg további információt.

1.  Az alábbi parancsmag futtatásával jelenítheti meg a bérlő összes törölt, még visszaállítható Office 365-csoportját.
   
    ```
    Get-AzureADMSDeletedGroup
    ```

2.  Ha ismeri egy adott csoport objektumazonosítóját (amelyet egyébként az 1. lépésben található parancsmag kimenetében láthat), az alábbi parancsmag futtatásával ellenőrizheti, hogy az adott törölt csoport végleges törlése megtörtént-e már.

    ```
    Get-AzureADMSDeletedGroup –Id <objectId>
    ```

## <a name="how-to-restore-your-deleted-office-365-group-using-powershell"></a>Hogyan lehet visszaállítani a törölt Office 365-csoport Powershell-lel
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
- Minden olyan SharePoint webhelyet, és a tartalom mindegyikét érhetők el
- A csoport elérhető lesz bármelyik Exchange-végpontról, illetve az Office 365-csoportokat támogató egyéb Office 365 számítási feladatokból.

## <a name="next-steps"></a>További lépések
E cikkekben további információk találhatók az Azure Active Directory-csoportokkal kapcsolatban.

* [Meglévő csoportok megtekintése](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Csoportbeállítások kezelése](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Csoporttagok kezelése](../fundamentals/active-directory-groups-members-azure-portal.md)
* [Csoporttagságok kezelése](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [A csoportban lévő felhasználók dinamikus szabályainak kezelése](groups-dynamic-membership.md)
