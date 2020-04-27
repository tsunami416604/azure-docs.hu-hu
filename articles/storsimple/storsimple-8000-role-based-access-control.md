---
title: Szerepköralapú Access Control használata a StorSimple-hez | Microsoft Docs
description: Ismerteti, hogyan használható az Azure szerepköralapú Access Control (RBAC) a StorSimple környezetében.
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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "66159136"
---
# <a name="role-based-access-control-for-storsimple"></a>Szerepköralapú Access Control a StorSimple

Ez a cikk egy rövid leírást tartalmaz arról, hogyan használható az Azure szerepköralapú Access Control (RBAC) a StorSimple-eszközhöz. A RBAC részletes hozzáférés-vezérlést biztosít az Azure-hoz. A RBAC használatával csak a megfelelő mennyiségű hozzáférést biztosíthatja a StorSimple-felhasználók számára a feladatok elvégzéséhez ahelyett, hogy mindenki számára korlátlan hozzáférést adna. Az Azure-beli hozzáférés-kezelés alapjaival kapcsolatos további információkért lásd: [a Azure Portal a szerepköralapú Access Control használatának első lépései](../role-based-access-control/overview.md).

Ez a cikk a 3,0-es vagy újabb verzióját futtató StorSimple 8000 sorozatú eszközökre vonatkozik a Azure Portal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-storsimple"></a>A StorSimple RBAC szerepkörei

A RBAC a szerepkörök alapján rendelhetők hozzá. A szerepkörök bizonyos jogosultsági szinteket biztosítanak a környezetben elérhető erőforrások alapján. A StorSimple-felhasználók két típusú szerepkört választhatnak: beépített vagy egyéni.

* **Beépített szerepkörök** – a beépített szerepkörök tulajdonosa, közreműködője, olvasója vagy felhasználói hozzáférési rendszergazdája lehet. További információ: [beépített szerepkörök az Azure szerepköralapú Access Controlhoz](../role-based-access-control/built-in-roles.md).

* **Egyéni szerepkörök** – ha a beépített szerepkörök nem felelnek meg az igényeinek, LÉTREHOZHAT egyéni RBAC-szerepköröket a StorSimple. Egyéni RBAC-szerepkör létrehozásához hozzon létre egy beépített szerepkört, szerkessze, majd importálja újra a környezetbe. A szerepkör letöltése és feltöltése Azure PowerShell vagy az Azure CLI használatával történik. További információ: [Egyéni szerepkörök létrehozása szerepköralapú Access Controlhoz](../role-based-access-control/custom-roles.md).

Ha szeretné megtekinteni a StorSimple-eszköz felhasználóinak számára elérhető különböző szerepköröket a Azure Portalban, lépjen a StorSimple Eszközkezelő szolgáltatáshoz, és lépjen a **hozzáférés-vezérlés (iam) > szerepkörök**elemre.


## <a name="create-a-custom-role-for-storsimple-infrastructure-administrator"></a>Egyéni szerepkör létrehozása a StorSimple-infrastruktúra rendszergazdájához

Az alábbi példában a beépített szerepkör- **olvasóval** kezdjük, amellyel a felhasználók megtekinthetik az összes erőforrás-hatókört, de nem szerkeszthetik őket, és újakat hozhatnak létre. Ezt követően kiterjesztjük ezt a szerepkört egy új egyéni szerepkör-StorSimple infrastruktúra-rendszergazda létrehozásához. Ez a szerepkör olyan felhasználókhoz van hozzárendelve, akik felügyelhetik a StorSimple-eszközök infrastruktúráját.

1. Futtassa a Windows PowerShellt rendszergazdaként.

2. Bejelentkezik az Azure-ba.

    `Connect-AzAccount`

3. Exportálja az olvasó szerepkört JSON-sablonként a számítógépén.

    ```powershell
    Get-AzRoleDefinition -Name "Reader"

    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
    ```

4. Nyissa meg a JSON-fájlt a Visual Studióban. Láthatja, hogy egy tipikus RBAC-szerepkör három fő szakaszból, **műveletből**, nem **tapintatokból**és **AssignableScopes**áll.

    A **művelet** szakaszban megjelenik a szerepkörre vonatkozó összes engedélyezett művelet. Minden művelet hozzá van rendelve egy erőforrás-szolgáltatóhoz. StorSimple-infrastruktúra rendszergazdája használja az `Microsoft.StorSimple` erőforrás-szolgáltatót.

    A PowerShell használatával megtekintheti az előfizetésében elérhető és regisztrált összes erőforrás-szolgáltatót.

    `Get-AzResourceProvider`

    Az összes elérhető PowerShell-parancsmagot is megtekintheti az erőforrás-szolgáltatók kezeléséhez.

    A **tapintatok** szakaszban az adott RBAC-szerepkörre vonatkozó összes korlátozott művelet szerepel a felsorolásban. Ebben a példában egyetlen művelet sincs korlátozva.
    
    A **AssignableScopes**alatt az előfizetési azonosítók szerepelnek a felsorolásban. Győződjön meg arról, hogy a RBAC szerepkör tartalmazza a explicit előfizetés-azonosítót, ahol a használatban van. Ha nincs megadva a megfelelő előfizetés-azonosító, nem importálhatja a szerepkört az előfizetésében.

    Szerkessze a fájlt, és vegye figyelembe az előző szempontokat.

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

6. Importálja újra az egyéni RBAC-szerepkört a környezetbe.

    `New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"`


A szerepkörnek ekkor a **hozzáférés-vezérlés** panelen kell megjelennie a szerepkörök listájában.

![RBAC-szerepkörök megtekintése](./media/storsimple-8000-role-based-access-control/rbac-role-types.png)

További információért lépjen az [Egyéni szerepkörök](../role-based-access-control/custom-roles.md)elemre.

### <a name="sample-output-for-custom-role-creation-via-the-powershell"></a>Minta kimenete egyéni szerepkörök létrehozásához a PowerShell használatával

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

A hozzáférés a szerepkör-hozzárendelés hatókörébe tartozó erőforrásból, erőforráscsoportból vagy előfizetésből biztosítható. A hozzáférés megadásakor vegye figyelembe, hogy a szülő csomóponton megadott hozzáférést a gyermek örökli. További információért lépjen a [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md)elemre.

1. Nyissa meg a **hozzáférés-vezérlés (iam)** lehetőséget. Kattintson a **+ Hozzáadás** elemre a hozzáférés-vezérlés panelen.

    ![Hozzáférés hozzáadása a RBAC szerepkörhöz](./media/storsimple-8000-role-based-access-control/rbac-add-role.png)

2. Válassza ki a hozzárendelni kívánt szerepkört, ebben az esetben ez a **StorSimple-infrastruktúra rendszergazdája**.

3. Válassza ki azt a felhasználót, csoportot vagy alkalmazást a címtárában, amely számára hozzáférést kíván biztosítani. A címtárban rákereshet megjelenítendő nevekre, e-mail-címekre és objektumazonosítókra.

4. A hozzárendelés létrehozásához válassza a **Mentés** lehetőséget.

    ![Engedélyek hozzáadása a RBAC szerepkörhöz](./media/storsimple-8000-role-based-access-control/rbac-create-role-infra-admin.png)

A **felhasználói értesítések hozzáadásával** nyomon követheti a folyamat előrehaladását. A felhasználó sikeres hozzáadása után a rendszer frissíti a hozzáférés-vezérlésben lévő felhasználók listáját.

## <a name="view-permissions-for-the-custom-role"></a>Az egyéni szerepkör engedélyeinek megtekintése

A szerepkör létrehozása után megtekintheti a szerepkörhöz társított engedélyeket a Azure Portal.

1. A szerepkörhöz társított engedélyek megtekintéséhez lépjen a **hozzáférés-vezérlés (iam) > szerepkörök > StorSimple-infrastruktúra rendszergazdája**elemre. Megjelenik a szerepkörben lévő felhasználók listája.

2. Válasszon ki egy StorSimple-infrastruktúra rendszergazdai felhasználót, és kattintson az **engedélyek**elemre.

    ![Az StorSimple infra rendszergazdai szerepkör engedélyeinek megtekintése](./media/storsimple-8000-role-based-access-control/rbac-roles-view-permissions.png)

3. Megjelenik az ehhez a szerepkörhöz társított engedélyek.

    ![Felhasználók megtekintése a StorSimple infra rendszergazdai szerepkörben](./media/storsimple-8000-role-based-access-control/rbac-infra-admin-permissions1.png)


## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [rendelhet hozzá egyéni szerepköröket belső és külső felhasználók](../role-based-access-control/role-assignments-external-users.md)számára.
