---
title: Szerepköralapú hozzáférés-vezérlés használata a storsimple-höz készült |} A Microsoft Docs
description: Ismerteti a StorSimple-környezetben való használata az Azure szerepköralapú hozzáférés-vezérlés (RBAC).
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
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59049472"
---
# <a name="role-based-access-control-for-storsimple"></a>Storsimple-höz készült szerepköralapú hozzáférés-vezérlés

Ez a cikk az Azure szerepköralapú hozzáférés-vezérlés (RBAC) segítségével hogyan biztosítható a StorSimple eszköz rövid leírását. Az RBAC hozzáféréskezelést az Azure biztosít. Az RBAC használatával adja meg a StorSimple-felhasználók helyett mindenki számára a feladataik elvégzéséhez a hozzáférést csak a megfelelő mennyiségű ugyanolyan korlátlan hozzáféréssel. Hozzáférés-kezelés az Azure-ban az alapokat további információ: [szerepköralapú hozzáférés-vezérlés az Azure Portalon – első lépések](../role-based-access-control/overview.md).

Ez a cikk vonatkozik, a StorSimple 8000 sorozatú eszközök Update 3.0 vagy később az Azure Portalon.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-storsimple"></a>Storsimple-höz készült RBAC-szerepkörök

Az RBAC a szerepkörök alapján is hozzárendelhető. A szerepkörök győződjön meg arról, bizonyos jogosultsági szintek, a környezetben elérhető erőforrások alapján. A StorSimple-felhasználók választhatnak az szerepkörök két típusa van: beépített vagy egyéni.

* **Beépített szerepkörök** – lehet, hogy a beépített szerepkörök tulajdonos, közreműködő, olvasó vagy a felhasználói hozzáférés rendszergazdája. További információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörei](../role-based-access-control/built-in-roles.md).

* **Egyéni szerepkörök** – Ha a beépített szerepkörök nem felelnek meg az igényeinek, létrehozhat storsimple-höz készült egyéni RBAC-szerepkörökhöz. Hozzon létre egy egyéni RBAC-szerepkört, indítsa el a beépített szerepkörrel, szerkeszthetik és majd importálja vissza a környezetben. A letöltés és a szerepkör feltöltése az Azure PowerShell vagy az Azure CLI használatával történik. További információkért lásd: [egyéni szerepkörök létrehozása a szerepköralapú hozzáférés-vezérlés](../role-based-access-control/custom-roles.md).

Az Azure Portalon a StorSimple eszköz felhasználói különböző szerepkörök megtekintéséhez nyissa meg a StorSimple-Eszközkezelő szolgáltatás, és folytassa a **hozzáférés-vezérlés (IAM) > szerepkörök**.


## <a name="create-a-custom-role-for-storsimple-infrastructure-administrator"></a>Egyéni szerepkör létrehozása a StorSimple infrastruktúra-rendszergazda

A következő példában a beépített szerepkörrel rendelkező módszertan **olvasó** , amely lehetővé teszi a felhasználóknak az erőforrás minden hatókör megtekintéséhez, de nem szerkesztheti őket, vagy újakat hoz létre. Azt, majd kiterjeszthetik ezt a szerepkört, hozzon létre egy új egyéni biztonsági szerepkört a StorSimple-infrastruktúra-rendszergazda. Ez a szerepkör van rendelve az infrastruktúra, a StorSimple-eszközök kezelésére jogosult felhasználók.

1. Windows PowerShell futtatása rendszergazdaként.

2. Bejelentkezik az Azure-ba.

    `Connect-AzAccount`

3. Az Olvasó szerepkör exportálása egy JSON-sablon a számítógépen.

    ```powershell
    Get-AzRoleDefinition -Name "Reader"

    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
    ```

4. Nyissa meg a JSON-fájlt a Visual Studióban. Láthatja, hogy egy tipikus RBAC szerepkör áll három fő szakaszra, **műveletek**, **NotActions**, és **AssignableScopes**.

    Az a **művelet** részben, az összes felsorolt az engedélyezett műveletek ehhez a szerepkörhöz. Minden művelet egy erőforrás-szolgáltató van hozzárendelve. A StorSimple infrastruktúra-rendszergazda, használja a `Microsoft.StorSimple` erőforrás-szolgáltató.

    Használja a Powershellt, tekintse meg az összes erőforrás-szolgáltató elérhető és a regisztrált az előfizetésében.

    `Get-AzResourceProvider`

    Az összes elérhető PowerShell-parancsmagok az erőforrás-szolgáltatók kezelése is kereshet.

    Az a **NotActions** részei, az összes felsorolt a korlátozott műveleteket egy adott RBAC-szerepkör. Ebben a példában a műveletek nem korlátozottak.
    
    Alatt a **AssignableScopes**, az előfizetésben azonosítók. Győződjön meg arról, hogy az RBAC szerepkör tartalmazza az explicit előfizetés-azonosító, hol használják. Ha a helyes előfizetési azonosító nincs megadva, nem engedélyezettek a szerepkört az előfizetés importálásához.

    Szerkessze a fájlt az előző szempontokat szem.

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

6. Az egyéni RBAC szerepkör importálja vissza a környezetben.

    `New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"`


Ez a szerepkör meg kell jelennie a listában, a szerepkörök a **hozzáférés-vezérlés** panelen.

![RBAC-szerepkörök megtekintése](./media/storsimple-8000-role-based-access-control/rbac-role-types.png)

További információért ugorjon [egyéni szerepkörök](../role-based-access-control/custom-roles.md).

### <a name="sample-output-for-custom-role-creation-via-the-powershell"></a>Az egyéni szerepkör létrehozása a PowerShell segítségével a kimeneti példa

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

## <a name="add-users-to-the-custom-role"></a>Felhasználók hozzáadása az egyéni szerepkör

A hozzáférés a szerepkör-hozzárendelés hatókörébe tartozó erőforrásból, erőforráscsoportból vagy előfizetésből biztosítható. Ha hozzáférést biztosít, hogy a gyermek öröklik a szülő csomópontban biztosított hozzáférést figyelembe kell vennie. További információért ugorjon [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md).

1. Lépjen a **hozzáférés-vezérlés (IAM)**. Kattintson a **+ Hozzáadás** az Access control panelen.

    ![Hozzáférés RBAC-szerepkör hozzáadása](./media/storsimple-8000-role-based-access-control/rbac-add-role.png)

2. Válassza ki a hozzárendelni kívánt szerepkört, ebben az esetben a **StorSimple infrastruktúra-rendszergazda**.

3. Válassza ki azt a felhasználót, csoportot vagy alkalmazást a címtárában, amely számára hozzáférést kíván biztosítani. A címtárban rákereshet megjelenítendő nevekre, e-mail-címekre és objektumazonosítókra.

4. Válassza ki **mentése** hozni a hozzárendelést.

    ![Engedélyek az RBAC-szerepkör hozzáadása](./media/storsimple-8000-role-based-access-control/rbac-create-role-infra-admin.png)

Egy **felhasználó felvétele** értesítési nyomon követi a folyamatot. A felhasználó sikeres hozzáadása után a felhasználók a hozzáférés-vezérlés listája frissül.

## <a name="view-permissions-for-the-custom-role"></a>Az egyéni szerepkör engedélyeinek megtekintése

Ez a szerepkör létrehozása után megtekintheti a az Azure Portalon a szerepkörhöz társított engedélyeket.

1. Az ehhez a szerepkörhöz tartozó engedélyek megtekintéséhez lépjen a **hozzáférés-vezérlés (IAM) > szerepkörök > a StorSimple infrastruktúra-rendszergazda**. A szerepkör-felhasználók listája jelenik meg.

2. Válasszon ki egy StorSimple infrastruktúra-rendszergazda felhasználót, és kattintson a **engedélyek**.

    ![A StorSimple Infra rendszergazdai szerepkörhöz tartozó engedélyek megtekintése](./media/storsimple-8000-role-based-access-control/rbac-roles-view-permissions.png)

3. Az ehhez a szerepkörhöz társított engedélyeket jelennek meg.

    ![A StorSimple Infra rendszergazdai szerepkör felhasználóinak megtekintése](./media/storsimple-8000-role-based-access-control/rbac-infra-admin-permissions1.png)


## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [egyéni szerepkörök hozzárendelése belső és külső felhasználókhoz](../role-based-access-control/role-assignments-external-users.md).
