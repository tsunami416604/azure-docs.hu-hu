---
title: Az Azure-erőforrások RBAC kapcsolatos hibák megoldása | Microsoft Docs
description: A szerepköralapú hozzáférés-vezérléssel (RBAC) kapcsolatos hibák elhárítása Azure-erőforrásokhoz.
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: c3ca873cb4dd49d77ba818b3d05c3aa41e89276f
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982719"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>Azure-erőforrások RBAC kapcsolatos hibák

Ez a cikk az Azure-erőforrások szerepköralapú hozzáférés-vezérlésével (RBAC) kapcsolatos gyakori kérdésekre ad választ, hogy tudja, mire számíthat a Azure Portal szerepköreinek használatakor, és hogyan oldhatja meg a hozzáférési problémákat.

## <a name="problems-with-rbac-role-assignments"></a>Problémák az RBAC-beli szerepkör-hozzárendelésekkel

- Ha nem tud szerepkör-hozzárendelést hozzáadni a Azure Portal on hozzáférés- **vezérlés (iam)** használatával, mert > a**szerepkör-hozzárendelés** hozzáadása beállítás le van tiltva, vagy az engedélyekkel kapcsolatos hibaüzenet jelenik meg, mert a (z) azonosítójú ügyfél nem rendelkezik művelet végrehajtásának engedélyezése ", győződjön meg arról, hogy jelenleg be van jelentkezve egy olyan szerepkörrel `Microsoft.Authorization/roleAssignments/write` rendelkező felhasználóval, amely jogosult a szerepkörhöz hozzárendelni kívánt hatókörre, például [tulajdonos](built-in-roles.md#owner) vagy [felhasználói hozzáférés rendszergazdai](built-in-roles.md#user-access-administrator) jogosultsággal.
- Ha a következő hibaüzenet jelenik meg: "nem hozhatók létre több szerepkör-hozzárendelés (kód: RoleAssignmentLimitExceeded)” (Nem lehet több szerepkör-hozzárendelést létrehozni (kód: RoleAssignmentLimitExceeded)) hibaüzenetet jelenik meg, próbálja meg csökkenteni a szerepkör-hozzárendelések számát a szerepkörök csoportokhoz történő hozzárendelésével. Az Azure legfeljebb **2000** szerepkör-hozzárendelést támogat előfizetésenként.

## <a name="problems-with-custom-roles"></a>Problémák az egyéni szerepkörökkel

- Ha az egyéni szerepkörök létrehozásához szükséges lépéseket szeretné látni, tekintse meg az egyéni szerepkör-oktatóanyagokat [Azure PowerShell](tutorial-custom-role-powershell.md) vagy az [Azure CLI](tutorial-custom-role-cli.md)használatával.
- Ha nem tud frissíteni egy meglévő egyéni szerepkört, ellenőrizze, hogy jelenleg be van-e jelentkezve olyan felhasználóval, akinek van olyan szerepköre, `Microsoft.Authorization/roleDefinition/write` amely jogosultsággal rendelkezik, például a [tulajdonos](built-in-roles.md#owner) vagy a [felhasználó hozzáférés-rendszergazdája](built-in-roles.md#user-access-administrator).
- Ha nem sikerül törölni egy egyéni szerepkört, és a „There are existing role assignments referencing role (code: RoleDefinitionHasAssignments” (Szerepkör-hozzárendelések hivatkoznak a szerepkörre (kód: RoleDefinitionHasAssignments)) hibaüzenet jelenik meg, akkor vannak olyan szerepkör-hozzárendelések, amelyek továbbra is az adott egyéni szerepkört használják. Távolítsa el a szóban forgó szerepkör-hozzárendeléseket, majd próbálja meg ismét törölni az egyéni szerepkört.
- Ha egy új egyéni szerepkör létrehozásakor a „Role definition limit exceeded. Nem hozhatók létre több szerepkör-definíció (kód: RoleDefinitionLimitExceeded) "Ha új egyéni szerepkört próbál létrehozni, törölje a nem használt egyéni szerepköröket. Az Azure legfeljebb **5000** egyéni szerepkört támogat a bérlők számára. (A speciális felhőkben, mint az Azure Government, az Azure Germany és az Azure China 21Vianet, az egyéni szerepkörök száma 2000-re van korlátozva.)
- Ha a következőhöz hasonló hibaüzenet jelenik meg: "az ügyfél jogosult a (z)" Microsoft. Authorization/roleDefinitions/Write "művelet végrehajtására a (z)"/Subscriptions/{subscriptionid} "hatókörben, de a csatolt előfizetés nem található" Ha egyéni szerepkört próbál frissíteni, ellenőrizze a következőt: azt határozza meg, hogy egy vagy több [hozzárendelhető hatókör](role-definitions.md#assignablescopes) törölve lett-e a bérlőben. Ha a hatókör törölve lett, hozzon létre egy támogatási jegyet, mivel jelenleg nem áll rendelkezésre önkiszolgáló megoldás.

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>Az RBAC helyreállítása az előfizetések bérlők közötti áthelyezésekor

- Ha egy előfizetés egy másik Azure AD-bérlőre való átvitelének lépéseire van szüksége, tekintse meg [Az Azure-előfizetés tulajdonjogának átruházása másik fiókra](../billing/billing-subscription-transfer.md)című témakört.
- Amikor előfizetést helyez át egy másik Azure AD-bérlőre, az összes szerepkör-hozzárendelés véglegesen törlődik a forrásbérlőből, így nem lesz áttelepítve az Azure AD-célbérlőbe. A szerepkör-hozzárendeléseket újra létre kell hoznia a célbérlőben. Emellett manuálisan újra létre kell hoznia a felügyelt identitásokat az Azure-erőforrásokhoz. További információ: [Gyakori kérdések és ismert problémák a felügyelt identitásokkal](../active-directory/managed-identities-azure-resources/known-issues.md)kapcsolatban.
- Ha Ön az Azure AD globális rendszergazdája, és a bérlők közötti áthelyezés után nem fér hozzá egy előfizetéshez, az Azure- **erőforrások hozzáférés** -vezérlése lehetőséggel ideiglenesen [emelheti](elevate-access-global-admin.md) ki a hozzáférést az előfizetéshez.

## <a name="issues-with-service-admins-or-co-admins"></a>Problémák a szolgáltatásadminisztrátorokkal vagy társadminisztrátorokkal

- Ha a szolgáltatás-rendszergazdával vagy a társ-rendszergazdákkal kapcsolatos problémákat tapasztal, tekintse meg az Azure-előfizetések [rendszergazdáinak hozzáadása vagy módosítása](../billing/billing-add-change-azure-subscription-administrator.md) , valamint [a klasszikus előfizetés-rendszergazdai szerepkörök, az Azure RBAC szerepkörei és az Azure ad-rendszergazdai szerepkörök](rbac-and-directory-admin-roles.md)

## <a name="access-denied-or-permission-errors"></a>Hozzáférés megtagadva vagy engedélyekkel kapcsolatos hibák

- Ha egy erőforrás létrehozásakor a „The client with object id does not have authorization to perform action over scope (code: AuthorizationFailed)” (Az adott objektumazonosítójú ügyfél nem rendelkezik a művelet a kiválasztott hatókörben való végrehajtásához szükséges engedéllyel (kód: AuthorizationFailed)) engedélyekkel kapcsolatos hiba lép fel, ellenőrizze, hogy a felhasználó, amelyikkel bejelentkezett, olyan szerepkörhöz van-e hozzárendelve, amely rendelkezik írási engedéllyel a kiválasztott hatókörben található erőforráshoz. Például az erőforráscsoportban található virtuális gépek kezeléséhez a [Virtuális gépek közreműködője](built-in-roles.md#virtual-machine-contributor) szerepkörrel kell rendelkeznie az erőforráscsoportban (vagy a szülő hatókörben). Az egyes beépített szerepkörökhöz tartozó engedélyek listáját a [Beépített szerepkörök Azure-erőforrásokhoz](built-in-roles.md) témakörben tekintheti meg.
- Ha a támogatási jegy létrehozásakor vagy frissítésekor a "nincs engedélye a támogatási kérelem létrehozására" hibaüzenet jelenik meg, ellenőrizze, hogy jelenleg be van-e jelentkezve olyan felhasználóval, akinek van olyan szerepköre, amely rendelkezik `Microsoft.Support/supportTickets/write` engedéllyel, például [ Támogatási kérelem közreműködői](built-in-roles.md#support-request-contributor).

## <a name="role-assignments-without-a-security-principal"></a>Rendszerbiztonsági tag nélküli szerepkör-hozzárendelések

Ha Azure PowerShell használatával listázza a szerepkör-hozzárendeléseket, akkor előfordulhat, hogy a hozzárendelések üresek `DisplayName` , és az `ObjectType` ismeretlen értékre van állítva. A [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) például a következőhöz hasonló szerepkör-hozzárendelést ad vissza:

```azurepowershell
RoleAssignmentId   : /subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111
DisplayName        :
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 33333333-3333-3333-3333-333333333333
ObjectType         : Unknown
CanDelegate        : False
```

Hasonlóképpen, amikor az Azure CLI használatával listázza a szerepkör-hozzárendeléseket, a hozzárendelések üresen `principalName`jelenhetnek meg. Az az [szerepkör-hozzárendelési lista](/cli/azure/role/assignment#az-role-assignment-list) például a következőhöz hasonló szerepkör-hozzárendelést ad vissza:

```azurecli
{
    "canDelegate": null,
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222",
    "name": "22222222-2222-2222-2222-222222222222",
    "principalId": "33333333-3333-3333-3333-333333333333",
    "principalName": "",
    "roleDefinitionId": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
    "roleDefinitionName": "Storage Blob Data Contributor",
    "scope": "/subscriptions/11111111-1111-1111-1111-111111111111",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

Ezek a szerepkör-hozzárendelések akkor fordulnak elő, amikor szerepkört rendel egy rendszerbiztonsági tag (felhasználó, csoport, szolgáltatásnév vagy felügyelt identitás) számára, és később törli a rendszerbiztonsági tag nevét. Ezek a szerepkör-hozzárendelések nem jelennek meg a Azure Portalban, és nem probléma, hogy elhagyják őket. Azonban ha szeretné, távolítsa el ezeket a szerepkörök hozzárendeléseit.

A szerepkör-hozzárendelések eltávolításához használja a [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) vagy [az az szerepkör-hozzárendelés törlési](/cli/azure/role/assignment#az-role-assignment-delete) parancsait.

Ha a PowerShellben megpróbálja eltávolítani a szerepkör-hozzárendeléseket az objektumazonosító és a szerepkör-definíció neve alapján, és egynél több szerepkör-hozzárendelés felel meg a paramétereknek, a következő hibaüzenet jelenik meg: "A megadott információ nem képezhető le szerepkör-hozzárendelésre". A következő hibaüzenet egy példát mutat be:

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Ha ez a hibaüzenet jelenik meg, győződjön meg arról, hogy a `-Scope` vagy `-ResourceGroupName` a paramétereket is megadja.

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="rbac-changes-are-not-being-detected"></a>A RBAC módosításait a rendszer nem észleli

A Azure Resource Manager időnként a konfiguráció és az adat gyorsítótárazásával javítja a teljesítményt. Szerepkör-hozzárendelések létrehozásakor vagy törlésekor akár 30 percet is igénybe vehet, amíg a módosítások érvénybe lépnek. Ha a Azure Portal, a Azure PowerShell vagy az Azure CLI-t használja, akkor kijelentkezéssel és bejelentkezéssel kényszerítheti a szerepkör-hozzárendelés módosításának frissítését. Ha REST API-hívásokkal módosítja a szerepkör-hozzárendelési módosításokat, a hozzáférési token frissítésével kényszerítheti a frissítést.

## <a name="web-app-features-that-require-write-access"></a>Írási hozzáférést igénylő webalkalmazás-funkciók

Ha csak olvasási hozzáférést ad a felhasználónak egyetlen webalkalmazáshoz, néhány szolgáltatás le lesz tiltva, és előfordulhat, hogy nem várható. A következő felügyeleti funkciókhoz **írási** hozzáférés szükséges egy webalkalmazáshoz (közreműködő vagy tulajdonos), és nem érhetők el egyetlen írásvédett forgatókönyvben sem.

* Parancsok (például indítás, Leállítás stb.)
* Beállítások módosítása, például általános konfiguráció, méretezési beállítások, biztonsági mentési beállítások és figyelési beállítások
* A közzétételi hitelesítő adatok és egyéb titkos kulcsok, például az Alkalmazásbeállítások és a kapcsolatok karakterláncok elérése
* Folyamatos átviteli naplók
* Diagnosztikai naplók konfigurálása
* Konzol (parancssor)
* Aktív és közelmúltbeli üzemelő példányok (a helyi git folyamatos üzembe helyezéséhez)
* Becsült költség
* Webtesztek
* Virtuális hálózat (csak akkor látható az olvasó számára, ha a virtuális hálózatot korábban írási hozzáféréssel rendelkező felhasználó konfigurálta).

Ha nem fér hozzá ezekhez a csempéhöz, meg kell kérnie a rendszergazdát, hogy a webalkalmazáshoz közreműködői hozzáférést kérjen.

## <a name="web-app-resources-that-require-write-access"></a>Írási hozzáférést igénylő webalkalmazás-erőforrások

A webalkalmazások bonyolultak az egymással való interakciót okozó néhány különböző erőforrás jelenléte mellett. Íme egy tipikus erőforráscsoport néhány webhellyel:

![Webalkalmazás-erőforráscsoport](./media/troubleshooting/website-resource-model.png)

Ennek eredményeképpen, ha hozzáférést ad valakinek a webalkalmazáshoz, a Azure Portal webhely paneljének számos funkciója le van tiltva.

Ezeknek az elemeknek **írási** hozzáférésre van szükségük a webhelyének megfelelő **app Service** -csomaghoz:  

* A webalkalmazás díjszabási szintje (ingyenes vagy standard) megtekintése  
* Méretezési konfiguráció (példányok száma, virtuális gép mérete, automatikus skálázási beállítások)  
* Kvóták (tárolás, sávszélesség, processzor)  

Ezeknek az elemeknek **írási** hozzáférésre van szükségük a webhelyet tartalmazó teljes **erőforráscsoport** számára:  

* SSL-tanúsítványok és-kötések (az SSL-tanúsítványok megoszthatók az azonos erőforráscsoporthoz és a földrajzi helyen található helyek között)  
* Riasztási szabályok  
* Az autoskálázás beállításai  
* Application Insights-összetevők  
* Webtesztek  

## <a name="virtual-machine-features-that-require-write-access"></a>Írási hozzáférést igénylő virtuálisgép-funkciók

A webalkalmazásokhoz hasonlóan a virtuális gép panel egyes funkcióinak írási hozzáférésre van szükségük a virtuális géphez vagy az erőforráscsoport egyéb erőforrásaihoz.

A virtuális gépek a tartománynevek, a virtuális hálózatok, a tárolási fiókok és a riasztási szabályokhoz kapcsolódnak.

Ezeknek az elemeknek **írási** hozzáférésre van szükségük a **virtuális géphez**:

* Végpontok  
* IP-címek  
* Lemezek  
* Bővítmények  

Ezeknek **írási** hozzáférésre van szükségük a **virtuális géphez**és az **erőforráscsoporthoz** (a tartománynévvel együtt):  

* Rendelkezésre állási csoport  
* Elosztott terhelésű készlet  
* Riasztási szabályok  

Ha nem fér hozzá ezekhez a csempéhöz, kérje meg a rendszergazdát, hogy a közreműködői hozzáférjen az erőforráscsoporthoz.

## <a name="azure-functions-and-write-access"></a>Azure Functions és írási hozzáférés

A [Azure functions](../azure-functions/functions-overview.md) egyes funkcióinak írási hozzáférésre van szükségük. Ha például egy felhasználó hozzá van rendelve az [olvasó](built-in-roles.md#reader) szerepkörhöz, nem fogja tudni megtekinteni a functions alkalmazásban lévő függvényeket. Megjelenik a portál **(nincs hozzáférés)** .

![A Function alkalmazások nem férnek hozzá](./media/troubleshooting/functionapps-noaccess.png)

Egy olvasó rákattinthat a **platform szolgáltatásai** lapra, majd a **minden beállítás** elemre kattintva megtekintheti a Function alkalmazáshoz kapcsolódó beállításokat (a webalkalmazáshoz hasonlóan), de ezek a beállítások nem módosíthatók. A szolgáltatások eléréséhez szüksége lesz a [közreműködő](built-in-roles.md#contributor) szerepkörre.

## <a name="next-steps"></a>További lépések
* [Azure-erőforrásokhoz való hozzáférés kezelése az RBAC és az Azure Portal használatával](role-assignments-portal.md)
* [Az Azure-erőforrásokra vonatkozó RBAC-változásokkal kapcsolatos tevékenységek naplóinak megtekintése](change-history-report.md)

