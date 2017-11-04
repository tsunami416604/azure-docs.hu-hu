---
title: "Szerepköralapú hozzáférés-vezérlés használata StorSimple |} Microsoft Docs"
description: "Azure szerepköralapú hozzáférés-vezérlés (RBAC) StorSimple környezetében használatát ismerteti."
services: storsimple
documentationcenter: 
author: alkohli
manager: jconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2017
ms.author: alkohli
ms.openlocfilehash: d040849360a47c611d44b3a5d7649c685dcc8068
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2017
---
# <a name="role-based-access-control-for-storsimple"></a>StorSimple szerepköralapú hozzáférés-vezérlés

A cikkben megtudhatja, hogyan átruházásához hozzáférés-vezérlés (RBAC) is használható a StorSimple eszköz rövid leírása. Az RBAC részletes hozzáféréskezelést az Azure biztosít. Használja a Szerepalapú helyett mindenki jogosultságot ad a munkája elvégzéséhez a StorSimple felhasználók hozzáférésének csak a megfelelő mennyiségű teljes hozzáférést. Hozzáférés-kezelés az Azure-ban az alapok további információk: [szerepköralapú hozzáférés-vezérlés az Azure-portálon az első lépései](../active-directory/role-based-access-control-what-is.md).

Ez a cikk vonatkozik a StorSimple 8000 sorozat eszközeire frissítés 3.0 fut, vagy később az Azure-portálon.

## <a name="rbac-roles-for-storsimple"></a>A StorSimple RBAC-szerepkörök

Az RBAC-alapú szerepkörök rendelhetők. A szerepkörök gondoskodjon arról, hogy bizonyos jogosultsági szintek a környezetben elérhető erőforrások alapján. StorSimple-felhasználók közül választhatnak szerepkörök két típusa van: beépített és egyéni.

* **Beépített szerepkörök** -tulajdonos, közreműködő, olvasó vagy felhasználói hozzáférés adminisztrátora a beépített szerepkörök lehet. További információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörök](../active-directory/role-based-access-control-what-is.md#built-in-roles).

* **Egyéni szerepkörök** -Ha a beépített szerepkörök nem a saját igényeinek megfelelően, a StorSimple hozhat létre egyéni RBAC-szerepkörök. Egyéni RBAC-szerepkör létrehozásához egy beépített szerepkör kezdődhet, szerkesztheti, majd importálja vissza a környezetben. A letöltés és a szerepkör feltöltése felügyelt Azure PowerShell vagy az Azure parancssori felület használatával. További információkért lásd: [egyedi szerepkörök létrehozását a szerepköralapú hozzáférés-vezérlési](../active-directory/role-based-access-control-custom-roles.md).

A StorSimple eszköz felhasználói különböző szerepkörök az Azure portálon megtekintéséhez nyissa meg a StorSimple eszköz Manager szolgáltatáshoz, és folytassa a **hozzáférés-vezérlés (IAM) > szerepkörök**.


## <a name="create-a-custom-role-for-storsimple-infrastructure-administrator"></a>Hozzon létre egy egyéni biztonsági szerepkört a StorSimple infrastruktúra-rendszergazda

A következő példában először a beépített szerepkör **olvasó** , amely lehetővé teszi a felhasználóknak az erőforrás-hatókörök megtekintése, de nem szerkeszthetők, vagy hozzon létre újakat. Majd tovább bővítjük ennek a szerepkörnek hozzon létre egy új egyéni biztonsági szerepkört a StorSimple-infrastruktúra-rendszergazda segítségét. Ez a szerepkör van hozzárendelve a felhasználók, akik kezelhetik az infrastruktúrát a StorSimple eszközökhöz.

1. A Windows PowerShell futtatása rendszergazdaként.

2. Jelentkezzen be az Azure-bA.

    `Login-AzureRMAccount`

3. Az olvasó szerepkört exportálni egy JSON-sablon a számítógépen.

    ```
    Get-AzureRMRoleDefinition -Name "Reader"

    Get-AzureRMRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
    ```

4. Nyissa meg a JSON-fájlt a Visual Studióban. Láthatja, hogy egy tipikus RBAC szerepkör áll-e három fő szakasz **műveletek**, **NotActions**, és **AssignableScopes**.

    Az a **művelet** részben, a szerepkörhöz tartozó engedélyezett műveletek felsorolt összes. Egy erőforrás-szolgáltató hozzá van rendelve minden művelet. A StorSimple infrastruktúra-rendszergazda, használja a `Microsoft.StorSimple` erőforrás-szolgáltató.

    A PowerShell szolgáltatás használatával tekintse meg az összes az erőforrás-szolgáltató elérhető és regisztrált, az előfizetésben.

    `Get-AzureRMResourceProvider`

    Ellenőrizheti az összes rendelkezésre álló PowerShell-parancsmagok az erőforrás-szolgáltatók kezelése.

    Az a **NotActions** szakaszokban: az összes adott RBAC szerepkör korlátozott műveletek találhatók. Ebben a példában a műveletek nem korlátozottak.
    
    Az a **AssignableScopes**, az előfizetési azonosítók. Győződjön meg arról, hogy a Szerepalapú szerepkör tartalmazza a felhasználási explicit előfizetés-azonosító. Ha nincs megadva a megfelelő előfizetés-azonosító, nem engedélyezettek az előfizetésében szerepkört importálni.

    Szerkessze a szempontokat vegye figyelembe az előző fájlt.

    ```
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

6. Az egyéni RBAC szerepkör importálja vissza a környezetbe.

    `New-AzureRMRoleDefinition -InputFile "C:\ssrbaccustom.json"`


Ez a szerepkör ekkor meg kell jelennie a szerepkörök listáját a **hozzáférés-vezérlés** panelen.

![Nézet RBAC-szerepkörök](./media/storsimple-8000-role-based-access-control/rbac-role-types.png)

További információkért látogasson el [hozzon létre egy egyéni RBAC szerepkört a PowerShell segítségével](../active-directory/role-based-access-control-create-custom-roles-for-internal-external-users.md#create-a-custom-rbac-role-to-open-support-requests-using-powershell).

### <a name="sample-output-for-custom-role-creation-via-the-powershell"></a>Minta kimenet az egyéni szerepkör létrehozása a PowerShell használatával

```
PS C:\WINDOWS\system32> Login-AzureRMAccount

Environment           : AzureCloud
Account               : john.doe@contoso.com
TenantId              : <tenant_ID>
SubscriptionId        : <subscription_ID>
SubscriptionName      : Internal Consumption
CurrentStorageAccount :

PS C:\WINDOWS\system32> Get-AzureRMRoleDefinition -Name "Reader"

Name             : Reader
Id               : <guid>
IsCustom         : False
Description      : Lets you view everything, but not make any changes.
Actions          : {*/read}
NotActions       : {}
AssignableScopes : {/}

PS C:\WINDOWS\system32> Get-AzureRMRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json

PS C:\WINDOWS\system32> New-AzureRMRoleDefinition -InputFile "C:\ssrbaccustom.json"

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

PS C:\WINDOWS\system32>
```

## <a name="add-users-to-the-custom-role"></a>Felhasználók hozzáadása az egyéni szerepkör

A hozzáférés a szerepkör-hozzárendelés hatókörébe tartozó erőforrásból, erőforráscsoportból vagy előfizetésből biztosítható. Ha hozzáférést biztosító, figyelembe kell vennie, hogy a gyermek örökli a szülő csomópontban biztosított hozzáférést. További információkért látogasson el [erőforrás hierarchia és hozzáférési öröklési](../active-directory/role-based-access-control-what-is.md#resource-hierarchy-and-access-inheritance).

1. Ugrás a **hozzáférés-vezérlés (IAM)**. Kattintson a **+ Hozzáadás** a hozzáférés-vezérlő panelen.

    ![Hozzáférés hozzáadása az RBAC-szerepkör](./media/storsimple-8000-role-based-access-control/rbac-add-role.png)

2. Válassza ki a szerepkör, szeretne hozzárendelni, ebben az esetben a **StorSimple infrastruktúra-rendszergazda**.

3. Válassza ki azt a felhasználót, csoportot vagy alkalmazást a címtárában, amely számára hozzáférést kíván biztosítani. A címtárban rákereshet megjelenítendő nevekre, e-mail-címekre és objektumazonosítókra.

4. Válassza ki **mentése** a hozzárendelés létrehozásához.

    ![Engedélyek hozzáadása az RBAC-szerepkör](./media/storsimple-8000-role-based-access-control/rbac-create-role-infra-admin.png)

Egy **felhasználó felvétele** értesítést nyomon követi a folyamatot. A felhasználó sikeres hozzáadása után a felhasználók a hozzáférés-vezérlés listája frissül.

## <a name="view-permissions-for-the-custom-role"></a>Az egyéni szerepkör engedélyeinek megtekintése

Ez a szerepkör létrehozása után megtekintheti az Azure-portálon a szerepkörhöz társított engedélyeket.

1. Az ehhez a szerepkörhöz társított engedélyeket megtekintéséhez keresse fel **hozzáférés-vezérlés (IAM) > szerepkörök > StorSimple infrastruktúra-rendszergazda**. Ebben a szerepkörben levő felhasználók listája jelenik meg.

2. Jelöljön ki egy felhasználót a StorSimple infrastruktúra-rendszergazda, majd **engedélyek**.

    ![StorSimple infrastruktúra rendszergazdai szerepkör engedélyeinek megtekintése](./media/storsimple-8000-role-based-access-control/rbac-roles-view-permissions.png)

3. Az ehhez a szerepkörhöz társított engedélyeket jelennek meg.

    ![StorSimple infrastruktúra rendszergazdai szerepkör felhasználóinak megtekintése](./media/storsimple-8000-role-based-access-control/rbac-infra-admin-permissions1.png)


## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [belső és külső felhasználók egyéni szerepkörök hozzárendelése](../active-directory/role-based-access-control-create-custom-roles-for-internal-external-users.md).

