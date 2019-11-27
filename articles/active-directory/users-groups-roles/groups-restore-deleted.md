---
title: Törölt Office 365-csoport visszaállítása – Azure AD | Microsoft Docs
description: Törölt csoport visszaállítása, visszaállítható csoportok megtekintése és csoport végleges törlése Azure Active Directory
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

Amikor töröl egy Office 365-csoportot az Azure Active Directoryban (Azure AD), a törlés dátumától számított 30 napig a törölt csoport megmarad, azonban nem látható. Ennek célja az, hogy a csoport és tartalma szükség esetén visszaállítható legyen. Ez a funkció kizárólag Azure AD-ben használt Office 365-csoportokra vonatkozik. Nem használható biztonsági csoportok és terjesztési csoportok esetén. Vegye figyelembe, hogy a 30 napos csoportos visszaállítási időszak nem szabható testre.

> [!NOTE]
> Ne használja a `Remove-MsolGroup` parancsot, mert azzal véglegesen törli a csoportot. Az Office 365-csoportok törléséhez mindig használjon `Remove-AzureADMSGroup`.

A csoportok visszaállításához szükséges engedély az alábbiak bármelyike lehet:

Szerepkör | Engedélyek
--------- | ---------
Globális rendszergazda, csoport rendszergazdája, partner szint-támogatás és Intune-rendszergazda | A törölt Office 365-csoportok bármelyikét visszaállíthatják.
Felhasználói rendszergazda és partner Tier1-támogatás | Visszaállíthatja a törölt Office 365-csoportot, kivéve azokat a csoportokat, amelyek a vállalati rendszergazda szerepkörhöz vannak rendelve
Felhasználó | Visszaállíthatja az összes olyan törölt Office 365-csoportot, amely a saját tulajdonában van

## <a name="view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore"></a>A törölt Office 365-csoportok megtekintése és kezelése, amelyek elérhetők a visszaállításhoz

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com) egy felhasználói rendszergazdai fiókkal.

2. Válassza a **csoportok**lehetőséget, majd válassza a **törölt csoportok** lehetőséget a visszaállítani kívánt törölt csoportok megtekintéséhez.

    ![a visszaállításhoz elérhető csoportok megtekintése](media/groups-lifecycle/deleted-groups3.png)

3. A **törölt csoportok** panelen a következőket teheti:

   - Állítsa vissza a törölt csoportot és annak tartalmát a **Restore Group (visszaállítási csoport**) lehetőség kiválasztásával.
   - Véglegesen távolítsa el a törölt csoportot a **Törlés végleges**lehetőség kiválasztásával. A csoportok végleges eltávolításához rendszergazdának kell lennie.

## <a name="view-the-deleted-office-365-groups-that-are-available-to-restore-using-powershell"></a>A PowerShell használatával visszaállítható, törölt Office 365-csoportok megtekintése

Az alábbi parancsmagokkal tekintheti meg a törölt csoportokat annak ellenőrzéséhez, hogy a kívánt csoport vagy csoportok végleges törlése nem történt-e még meg. A parancsmagok az [Azure AD PowerShell-modul](https://www.powershellgallery.com/packages/AzureAD/) részét képezik. A modullal kapcsolatban az [Azure Active Directory PowerShell 2-es verzióját](/powershell/azure/install-adv2?view=azureadps-2.0) ismertető cikkben tekinthet meg további információt.

1.  Az alábbi parancsmag futtatásával jelenítheti meg a bérlő összes törölt, még visszaállítható Office 365-csoportját.
   

    ```powershell
    Get-AzureADMSDeletedGroup
    ```

2.  Ha ismeri egy adott csoport objektumazonosítóját (amelyet egyébként az 1. lépésben található parancsmag kimenetében láthat), az alábbi parancsmag futtatásával ellenőrizheti, hogy az adott törölt csoport végleges törlése megtörtént-e már.

    ```
    Get-AzureADMSDeletedGroup –Id <objectId>
    ```

## <a name="how-to-restore-your-deleted-office-365-group-using-powershell"></a>Törölt Office 365-csoport visszaállítása a PowerShell használatával

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
- Minden SharePoint-webhely és annak tartalma elérhető lesz
- A csoport elérhető lesz bármelyik Exchange-végpontról, illetve az Office 365-csoportokat támogató egyéb Office 365 számítási feladatokból.

## <a name="next-steps"></a>További lépések

E cikkekben további információk találhatók az Azure Active Directory-csoportokkal kapcsolatban.

* [Meglévő csoportok megtekintése](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Csoportbeállítások kezelése](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Csoporttagok kezelése](../fundamentals/active-directory-groups-members-azure-portal.md)
* [Csoporttagságok kezelése](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [A csoportban lévő felhasználók dinamikus szabályainak kezelése](groups-dynamic-membership.md)
