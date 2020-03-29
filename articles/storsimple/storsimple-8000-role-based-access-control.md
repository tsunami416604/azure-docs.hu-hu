---
title: Szerepköralapú hozzáférés-vezérlés használata a StorSimple alkalmazáshoz | Microsoft dokumentumok
description: Az Azure szerepköralapú hozzáférés-vezérlés (RBAC) használata a StorSimple környezetében.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2017
ms.author: alkohli
ms.openlocfilehash: a79753a897a62e194a759c23a9c0acc45c5f36c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66159136"
---
# <a name="role-based-access-control-for-storsimple"></a>Szerepköralapú hozzáférés-vezérlés a StorSimple-hez

Ez a cikk röviden ismerteti, hogy az Azure szerepköralapú hozzáférés-vezérlés (RBAC) hogyan használható a StorSimple-eszközhöz. Az RBAC részletes hozzáférés-kezelést kínál az Azure-hoz. Az RBAC használatával csak a megfelelő mennyiségű hozzáférést biztosíthat a StorSimple-felhasználóknak a munkájukhoz, ahelyett, hogy mindenkiszámára korlátlan hozzáférést biztosítana. Az Azure-hozzáférés-kezelés alapjairól az [Azure-portálon a Szerepköralapú hozzáférés-vezérlés első lépései](../role-based-access-control/overview.md)című témakörben talál további információt.

Ez a cikk az Azure Portalon 3.0-s vagy újabb frissítést futtató StorSimple 8000 sorozatú eszközökre vonatkozik.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-storsimple"></a>RBAC szerepkörök a StorSimple-hez

Az RBAC a szerepkörök alapján rendelhető hozzá. A szerepkörök bizonyos jogosultsági szinteket biztosítanak a környezetben rendelkezésre álló erőforrások alapján. A StorSimple-felhasználók kétféle szerepkör közül választhatnak: beépített vagy egyéni.

* **Beépített szerepkörök** – A beépített szerepkörök lehetnek tulajdonos, közreműködő, olvasó vagy felhasználói hozzáférés rendszergazdája. További információ: [Beépített szerepkörök az Azure szerepköralapú hozzáférés-vezérléshez.](../role-based-access-control/built-in-roles.md)

* **Egyéni szerepkörök** – Ha a beépített szerepkörök nem felelnek meg az igényeinek, egyéni RBAC-szerepköröket hozhat létre a StorSimple számára. Egyéni RBAC-szerepkör létrehozásához kezdje egy beépített szerepkörrel, szerkesztse, majd importálja vissza a környezetbe. A szerepkör letöltése és feltöltése az Azure PowerShell vagy az Azure CLI használatával lesz kezelve. További információt a [Szerepköralapú hozzáférés-vezérléshez egyéni szerepkörök létrehozása című témakörben talál.](../role-based-access-control/custom-roles.md)

A StorSimple-eszköz felhasználója számára az Azure Portalon elérhető különböző szerepkörök megtekintéséhez nyissa meg a StorSimple Eszközkezelő szolgáltatást, majd nyissa meg a **Hozzáférés-vezérlés (IAM) > szerepkörök.**


## <a name="create-a-custom-role-for-storsimple-infrastructure-administrator"></a>Egyéni szerepkör létrehozása a StorSimple infrastruktúra-felügyelő számára

A következő példában a beépített **szerepkör-olvasóval** kezdjük, amely lehetővé teszi a felhasználók számára az összes erőforráshatókör megtekintését, de nem szerkeszti őket, vagy újakat hoz létre. Ezután kibővítjük ezt a szerepkört egy új egyéni szerepkör létrehozásához StorSimple Infrastructure admin. Ez a szerepkör olyan felhasználókhoz van rendelve, akik kezelhetik a StorSimple eszközök infrastruktúráját.

1. Futtassa a Windows PowerShellt rendszergazdaként.

2. Bejelentkezik az Azure-ba.

    `Connect-AzAccount`

3. Exportálja az Olvasó szerepkört JSON-sablonként a számítógépen.

    ```powershell
    Get-AzRoleDefinition -Name "Reader"

    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
    ```

4. Nyissa meg a JSON-fájlt a Visual Studióban. Láthatja, hogy egy tipikus RBAC szerepkör három fő szakaszból áll: **Műveletek**, **NotActions**és **AssignableScopes**.

    A **Művelet** szakaszban a szerepkör összes engedélyezett művelete szerepel a listában. Minden művelet egy erőforrás-szolgáltatótól van hozzárendelve. A StorSimple infrastruktúra rendszergazdája, használja az erőforrás-szolgáltató. `Microsoft.StorSimple`

    A PowerShell segítségével megtekintheti az előfizetésben elérhető és regisztrált összes erőforrás-szolgáltatót.

    `Get-AzResourceProvider`

    Az összes elérhető PowerShell-parancsmag az erőforrás-szolgáltatók kezeléséhez is ellenőrizheti.

    A **NotActions** szakaszokban egy adott RBAC szerepkör összes korlátozott művelete szerepel a listában. Ebben a példában nincs művelet korlátozva.
    
    A **AssignableScopes**csoportban az előfizetési azonosítók fel vannak sorolva. Győződjön meg arról, hogy az RBAC szerepkör tartalmazza az explicit előfizetés-azonosítót, ahol használja. Ha nincs megadva a megfelelő előfizetési azonosító, nem importálhatja a szerepkört az előfizetésben.

    A fájl szerkesztése az előző szempontokat szem előtt tartva.

    ```json
    {
        "Name":  "StorSimple Infrastructure Admin",
        "Id":  "<guid>",
        "IsCustom":  true,
        "Description":  "Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install, download etc.",
        "Actions":  [
                        "Microsoft.StorSimple/managers/alerts/read",
                        "Microsoft.StorSimple/managers/devices/volumeContainers/read",
                        "Microsoft.StorSimple/managers/devices/jobs/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/write",
                        "Microsoft.StorSimple/managers/clearAlerts/action",
                        "Microsoft.StorSimple/managers/devices/networkSettings/read",
                        "Microsoft.StorSimple/managers/devices/publishSupportPackage/action",
                        "Microsoft.StorSimple/managers/devices/scanForUpdates/action",
                        "Microsoft.StorSimple/managers/devices/metrics/read"

                    ],
        "NotActions":  [

                    ],
        "AssignableScopes":  [
                                "/subscriptions/<subscription_ID>/"
                            ]
    }
    ```

6. Importálja az egyéni RBAC szerepkört vissza a környezetbe.

    `New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"`


Ennek a szerepkörnek most meg kell jelennie a **hozzáférés-vezérlési** panel szerepköreinek listájában.

![RBAC-szerepkörök megtekintése](./media/storsimple-8000-role-based-access-control/rbac-role-types.png)

További információ: [Egyéni szerepkörök](../role-based-access-control/custom-roles.md).

### <a name="sample-output-for-custom-role-creation-via-the-powershell"></a>Példakimenet egyéni szerepkör-létrehozáshoz a PowerShellen keresztül

```powershell
Connect-AzAccount
```

```Output
Environment           : AzureCloud
Account               : john.doe@contoso.com
TenantId              : <tenant_ID>
SubscriptionId        : <subscription_ID>
SubscriptionName      : Internal Consumption
CurrentStorageAccount :
```

```powershell
Get-AzRoleDefinition -Name "Reader"
```

```Output
Name             : Reader
Id               : <guid>
IsCustom         : False
Description      : Lets you view everything, but not make any changes.
Actions          : {*/read}
NotActions       : {}
AssignableScopes : {/}
```

```powershell
Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"
```

```Output
Name             : StorSimple Infrastructure Admin
Id               : <tenant_ID>
IsCustom         : True
Description      : Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install,
                   download etc.
Actions          : {Microsoft.StorSimple/managers/alerts/read,
                   Microsoft.StorSimple/managers/devices/volumeContainers/read,
                   Microsoft.StorSimple/managers/devices/jobs/read,
                   Microsoft.StorSimple/managers/devices/alertSettings/read...}
NotActions       : {}
AssignableScopes : {/subscriptions/<subscription_ID>/}
```

## <a name="add-users-to-the-custom-role"></a>Felhasználók hozzáadása az egyéni szerepkörhöz

A hozzáférés a szerepkör-hozzárendelés hatókörébe tartozó erőforrásból, erőforráscsoportból vagy előfizetésből biztosítható. A hozzáférés biztosításakor ne feledje, hogy a szülőcsomóponton biztosított hozzáférést a gyermek örökölte. További információért nyissa meg a [szerepköralapú hozzáférés-vezérlést.](../role-based-access-control/overview.md)

1. Nyissa meg a **hozzáférés-vezérlés (IAM) című**területet. Kattintson a **+ Hozzáadás** gombra a hozzáférés-vezérléspanelen.

    ![Hozzáférés hozzáadása az RBAC szerepkörhöz](./media/storsimple-8000-role-based-access-control/rbac-add-role.png)

2. Válassza ki a hozzárendelni kívánt szerepkört, ebben az esetben a **StorSimple infrastruktúra rendszergazdája.**

3. Válassza ki azt a felhasználót, csoportot vagy alkalmazást a címtárában, amely számára hozzáférést kíván biztosítani. A címtárban rákereshet megjelenítendő nevekre, e-mail-címekre és objektumazonosítókra.

4. A hozzárendelés létrehozásához válassza a **Mentés** lehetőséget.

    ![Engedélyek hozzáadása az RBAC szerepkörhöz](./media/storsimple-8000-role-based-access-control/rbac-create-role-infra-admin.png)

A **felhasználó hozzáadása** értesítés nyomon követi a folyamatot. A felhasználó sikeres hozzáadása után a hozzáférés-vezérlésfelhasználóinak listája frissül.

## <a name="view-permissions-for-the-custom-role"></a>Az egyéni szerepkör engedélyeinek megtekintése

A szerepkör létrehozása után megtekintheti a szerepkörhöz társított engedélyeket az Azure Portalon.

1. A szerepkörhöz társított engedélyek megtekintéséhez nyissa meg a **hozzáférés-vezérlés (IAM) > szerepkörök > a StorSimple infrastruktúra rendszergazdája című**részt. Megjelenik a szerepkörben szereplő felhasználók listája.

2. Jelöljön ki egy StorSimple Infrastructure Admin felhasználót, és kattintson **az Engedélyek gombra.**

    ![A StorSimple Infra Admin szerepkör engedélyeinek megtekintése](./media/storsimple-8000-role-based-access-control/rbac-roles-view-permissions.png)

3. A szerepkörhöz társított engedélyek megjelennek.

    ![Felhasználók megtekintése a StorSimple Infra Admin szerepkörben](./media/storsimple-8000-role-based-access-control/rbac-infra-admin-permissions1.png)


## <a name="next-steps"></a>További lépések

Megtudhatja, hogy [miként rendelhet egyéni szerepköröket belső és külső felhasználókhoz.](../role-based-access-control/role-assignments-external-users.md)
