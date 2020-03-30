---
title: Az Azure RBAC hibáinak elhárítása
description: Az Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC) kapcsolatos problémák elhárítása.
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
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 8aedc78772858815a18425fb1e6cb36a4600f647
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385095"
---
# <a name="troubleshoot-azure-rbac"></a>Az Azure RBAC hibáinak elhárítása

Ez a cikk választ ad néhány gyakori kérdésre az Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC), hogy tudja, mire számíthat a szerepkörök használatakor, és elháríthatja a hozzáférési problémákat.

## <a name="azure-role-assignments-limit"></a>Az Azure szerepkör-hozzárendelések korlátja

Az Azure előfizetésenként legfeljebb **2000** szerepkör-hozzárendelést támogat. Ha a "Nincs több szerepkör-hozzárendelések létrehozása (kód: RoleAssignmentLimitExceeded)" hibaüzenet jelenik meg, amikor szerepkört próbál hozzárendelni, próbálja meg csökkenteni az előfizetésben lévő szerepkör-hozzárendelések számát.

> [!NOTE]
> A **2000-es** szerepkör-hozzárendelések korlát előfizetésenként rögzített, és nem növelhető.

Ha közeledik ehhez a korláthoz, az alábbiakban néhány módszerként csökkentheti a szerepkör-hozzárendelések számát:

- Felhasználók hozzáadása csoportokhoz, és szerepkörök hozzárendelése a csoportokhoz. 
- Több beépített szerepkör kombinálása egyéni szerepkörrel. 
- A közös szerepkör-hozzárendelések egy magasabb hatókörben, például az előfizetés vagy a felügyeleti csoport.
- Ha az Azure AD Premium P2, a szerepkör-hozzárendelések jogosult az [Azure AD kiemelt identitáskezelés](../active-directory/privileged-identity-management/pim-configure.md) helyett véglegesen hozzárendelt. 
- Adjon hozzá egy további előfizetést. 

A szerepkör-hozzárendelések számának lefelvételéhez tekintse meg a [diagramot a hozzáférés-vezérlés (IAM) lapján](role-assignments-list-portal.md#list-number-of-role-assignments) az Azure Portalon. A következő Azure PowerShell-parancsokat is használhatja:

```azurepowershell
$scope = "/subscriptions/<subscriptionId>"
$ras = Get-AzRoleAssignment -Scope $scope | Where-Object {$_.scope.StartsWith($scope)}
$ras.Count
```

## <a name="problems-with-azure-role-assignments"></a>Problémák az Azure szerepkör-hozzárendelésekkel

- Ha nem tud szerepkör-hozzárendelést hozzáadni az Azure Portalon a **hozzáférés-vezérlési (IAM)** szolgáltatásban, mert a**Szerepkör-hozzárendelés** **hozzáadása** > beállítás le van tiltva, vagy mert az "Az objektumazonosítóval rendelkező `Microsoft.Authorization/roleAssignments/write` ügyfél nem rendelkezik művelet végrehajtására vonatkozó engedéllyel" engedély jelenik meg, ellenőrizze, hogy jelenleg olyan felhasználóval van-e bejelentkezve, amely olyan jogosultsággal rendelkezik, mint például [tulajdonos](built-in-roles.md#owner) vagy [felhasználói hozzáférés-rendszergazda](built-in-roles.md#user-access-administrator) a szerepkör hozzárendeléséhez.

## <a name="problems-with-custom-roles"></a>Problémák az egyéni szerepkörökkel

- Ha az egyéni szerepkör létrehozásához lépéseket kell használnia, tekintse meg az egyéni szerepkör-oktatóanyagokat az [Azure Portal (jelenleg](custom-roles-portal.md) előzetes verzióban), az [Azure PowerShell](tutorial-custom-role-powershell.md)vagy az [Azure CLI](tutorial-custom-role-cli.md)használatával.
- Ha nem tud frissíteni egy meglévő egyéni szerepkört, ellenőrizze, hogy jelenleg olyan felhasználóval `Microsoft.Authorization/roleDefinition/write` van-e bejelentkezve, akinek olyan szerepköre van, amely rendelkezik a [Tulajdonos](built-in-roles.md#owner) vagy a [Felhasználói hozzáférés rendszergazdája engedéllyel.](built-in-roles.md#user-access-administrator)
- Ha nem tud törölni egy egyéni szerepkört, és a következő hibaüzenet jelenik meg: "Vannak olyan szerepkör-hozzárendelések, amelyek szerepkörre hivatkoznak (kód: RoleDefinitionHasAssignments)", akkor még mindig vannak szerepkör-hozzárendelések, amelyek továbbra is az egyéni szerepkört használják. Távolítsa el a szóban forgó szerepkör-hozzárendeléseket, majd próbálja meg ismét törölni az egyéni szerepkört.
- Ha egy új egyéni szerepkör létrehozásakor a „Role definition limit exceeded. Nem hozható létre több szerepkör-definíció (kód: RoleDefinitionLimitExceeded)" új egyéni szerepkör létrehozásakor a nem használt egyéni szerepkörök törlése. Az Azure legfeljebb **5000** egyéni szerepkört támogat egy címtárban. (Az Azure Germany és az Azure China 21Vianet esetében a korlát 2000 egyéni szerepkör.)
- Ha a következőhöz hasonló hibaüzenetet kap, mint például a "Microsoft.Authorization/roleDefinitions/write" művelet végrehajtása a hatókör "/subscriptions/{subscriptionid}" hatókörön, azonban a csatolt előfizetés nem található", amikor egyéni szerepkört próbál frissíteni, ellenőrizze, hogy egy vagy több [hozzárendelhető hatókör](role-definitions.md#assignablescopes) törölve lett-e a címtárban. Ha a hatókör törölve lett, hozzon létre egy támogatási jegyet, mivel jelenleg nem áll rendelkezésre önkiszolgáló megoldás.

## <a name="custom-roles-and-management-groups"></a>Egyéni szerepkörök és felügyeleti csoportok

- Egyéni szerepkörben `AssignableScopes` csak egy felügyeleti csoportot határozhat meg. Felügyeleti csoport hozzáadása `AssignableScopes` jelenleg előzetes verzióban érhető el.
- A felügyeleti `DataActions` csoport hatókörén nem rendelhető hozzá egyéni szerepkörök.
- Az Azure Resource Manager nem ellenőrzi a felügyeleti csoport létezését a szerepkör-definíció hozzárendelhető hatókörében.
- Az egyéni szerepkörökről és felügyeleti csoportokról az [Erőforrások rendszerezése az Azure felügyeleti csoportokkal](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment)című témakörben talál további információt.

## <a name="transferring-a-subscription-to-a-different-directory"></a>Előfizetés átvitele másik könyvtárba

- Ha lépéseket szeretne tenni egy előfizetés másik Azure AD-címtárba való átviteléhez, olvassa el az [Azure-előfizetés tulajdonjogának átadása egy másik fiókba témakört.](../cost-management-billing/manage/billing-subscription-transfer.md)
- Ha egy előfizetést átegy másik Azure AD-könyvtárba, az összes szerepkör-hozzárendelések **véglegesen** törlődnek a forrás Azure AD könyvtárból, és nem települ nek át a cél Azure AD könyvtárba. Újra létre kell hoznia a szerepkör-hozzárendeléseket a célkönyvtárban. Manuálisan is létre kell hoznia a felügyelt identitásokat az Azure-erőforrásokhoz. További információt a [gyakori kérdések és a felügyelt identitásokkal kapcsolatos ismert problémák című témakörben talál.](../active-directory/managed-identities-azure-resources/known-issues.md)
- Ha Ön Az Azure AD globális rendszergazdája, és nem rendelkezik előfizetéssel a könyvtárak közötti átvitel után, az **Azure-erőforrások hozzáférés-kezelése** kapcsolóval ideiglenesen [magasabb szintre emelheti a hozzáférést](elevate-access-global-admin.md) az előfizetéshez való hozzáféréshez.

## <a name="issues-with-service-admins-or-co-admins"></a>Problémák a szolgáltatásadminisztrátorokkal vagy társadminisztrátorokkal

- Ha problémákat szeretne a szolgáltatás rendszergazdájával vagy a társrendszergazdákkal, olvassa el az [Azure-előfizetés-rendszergazdák](../cost-management-billing/manage/add-change-subscription-administrator.md) és [a klasszikus előfizetési rendszergazdai szerepkörök, az Azure-szerepkörök és az Azure AD-rendszergazdai szerepkörök](rbac-and-directory-admin-roles.md)hozzáadása vagy módosítása című témakört.

## <a name="access-denied-or-permission-errors"></a>Hozzáférés megtagadva vagy engedélyhibák

- Ha az "Az objektumazonosítóval rendelkező ügyfél nem rendelkezik felhatalmazással a hatókörön (kód: AuthorizationFailed) keresztüli művelet végrehajtására" engedélyjelenik meg, amikor erőforrást próbál létrehozni, ellenőrizze, hogy jelenleg olyan felhasználóval van-e bejelentkezve, aki írási szerepkörrel rendelkezik. engedélyt a kijelölt hatókörben lévő erőforráshoz. Például az erőforráscsoportban található virtuális gépek kezeléséhez a [Virtuális gépek közreműködője](built-in-roles.md#virtual-machine-contributor) szerepkörrel kell rendelkeznie az erőforráscsoportban (vagy a szülő hatókörben). Az egyes beépített szerepkörökhöz tartozó engedélyek listáját a [Beépített szerepkörök Azure-erőforrásokhoz](built-in-roles.md) témakörben tekintheti meg.
- Ha a támogatási jegy létrehozásakor vagy frissítésekor a "Nincs engedélye támogatási kérelem létrehozására" engedélyekkel kapcsolatos hibaüzenet jelenik meg, ellenőrizze, hogy `Microsoft.Support/supportTickets/write` jelenleg olyan felhasználóval van-e bejelentkezve, aki rendelkezik engedéllyel rendelkező szerepkörrel, például [a Támogatási kérelem közreműködőjével.](built-in-roles.md#support-request-contributor)

## <a name="role-assignments-with-unknown-security-principal"></a>Szerepkör-hozzárendelések ismeretlen rendszertaggal

Ha szerepkört rendel egy rendszerbiztonsági taghoz (felhasználó, csoport, egyszerű szolgáltatás vagy felügyelt identitás), majd később törli a rendszerbiztonsági tagot a szerepkör-hozzárendelés eltávolítása nélkül, a szerepkör-hozzárendelés rendszerbiztonsági tag típusa **ismeretlen**lesz. Az alábbi képernyőkép egy példát mutat be az Azure Portalon. A rendszerbiztonsági tag neve az **identitás törléseként** jelenik meg, **és az identitás már nem létezik.** 

![Webalkalmazás-erőforráscsoport](./media/troubleshooting/unknown-security-principal.png)

Ha ezt a szerepkör-hozzárendelést az Azure PowerShell használatával listázza, egy üres `DisplayName` és egy `ObjectType` Ismeretlen készlet jelenik meg. A [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) például a következőhöz hasonló szerepkör-hozzárendelést ad vissza:

```
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

Hasonlóképpen, ha ezt a szerepkör-hozzárendelést az Azure `principalName`CLI használatával sorolja fel, egy üres jelenik meg. Az [szerepkör-hozzárendelési lista](/cli/azure/role/assignment#az-role-assignment-list) például a következőhöz hasonló szerepkör-hozzárendelést ad vissza:

```
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

Nem probléma, hogy elhagyja ezeket a szerepkör-hozzárendelések, de eltávolíthatja őket a más szerepkör-hozzárendelésekhez hasonló lépésekkel. A szerepkör-hozzárendelések eltávolításáról az [Azure Portal](role-assignments-portal.md#remove-a-role-assignment), az Azure [PowerShell](role-assignments-powershell.md#remove-a-role-assignment)vagy az [Azure CLI](role-assignments-cli.md#remove-a-role-assignment) című témakörben talál további információt.

A PowerShellben, ha megpróbálja eltávolítani a szerepkör-hozzárendelések az objektum-azonosító és a szerepkör-definíció neve, és több szerepkör-hozzárendelés megfelel a paramétereket, a következő hibaüzenet jelenik meg: "A megadott információ nem felel meg a szerepkör-hozzárendelés". Az alábbi példa a hibaüzenetre mutat be:

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Ha ez a hibaüzenet jelenik meg, `-Scope` `-ResourceGroupName` győződjön meg arról is, hogy megadja a vagy a paramétereket.

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="role-assignment-changes-are-not-being-detected"></a>A rendszer nem észleli a szerepkör-hozzárendelés módosításait

Az Azure Resource Manager néha gyorsítótárazza a konfigurációkat és az adatokat a teljesítmény javítása érdekében. Szerepkör-hozzárendelések hozzáadásakor vagy eltávolításakor a módosítások érvénybe léptetése akár 30 percet is igénybe vehet. Ha az Azure Portalon, az Azure PowerShellben vagy az Azure CLI-ben használja, kijelentkezésével és bejelentkezésével kényszerítheti a szerepkör-hozzárendelés-módosítások frissítését. Ha a REST API-hívásokkal szerepkör-hozzárendelési módosításokat hajt végre, kényszerítheti a frissítést a hozzáférési jogkivonat frissítésével.

Ha szerepkör-hozzárendelést ad hozzá vagy távolít `DataActions`el a felügyeleti csoport hatókörében, és a szerepkör rendelkezik, előfordulhat, hogy az adatsíkon lévő hozzáférés több órán keresztül nem frissül. Ez csak a felügyeleti csoport hatókörére és az adatsíkra vonatkozik.

## <a name="web-app-features-that-require-write-access"></a>Írási hozzáférést igénylő webalkalmazás-szolgáltatások

Ha egy felhasználónak csak olvasható hozzáférést biztosít egyetlen webalkalmazáshoz, egyes funkciók le vannak tiltva, amelyekre esetleg nem számít. A következő felügyeleti képességek **írási** hozzáférést igényelnek egy webalkalmazáshoz (közreműködővagy tulajdonos), és nem érhetők el írásvédett forgatókönyvben.

* Parancsok (például indítás, leállítás stb.)
* Az általános konfiguráció, a méretezési beállítások, a biztonsági mentési beállítások és a figyelési beállítások módosítása
* Közzétételi hitelesítő adatok és egyéb titkos kulcsok, például alkalmazásbeállítások és kapcsolati karakterláncok elérése
* Streamelési naplók
* Diagnosztikai naplók konfigurációja
* Konzol (parancssor)
* Aktív és legutóbbi telepítések (a helyi git folyamatos telepítéshez)
* Becsült költés
* Webes tesztek
* Virtuális hálózat (csak akkor látható az olvasó számára, ha a virtuális hálózatot korábban egy írási hozzáféréssel rendelkező felhasználó konfigurálta).

Ha ezekhez a csempékhez nem tud hozzáférni, kérje meg a rendszergazdát, hogy hozzáférjen a webalkalmazáshoz.

## <a name="web-app-resources-that-require-write-access"></a>Írási hozzáférést igénylő webalkalmazás-erőforrások

A webalkalmazásokat bonyolítja néhány különböző erőforrás jelenléte, amelyek egymással játszanak. Itt van egy tipikus erőforrás csoport egy pár honlapok:

![Webalkalmazás-erőforráscsoport](./media/troubleshooting/website-resource-model.png)

Ennek eredményeképpen, ha csak a webalkalmazáshoz biztosít hozzáférést, az Azure Portal on a webhely panelfunkcióinak nagy része le van tiltva.

Ezek az elemek **írási** hozzáférést igényelnek az **Alkalmazásszolgáltatás-csomaghoz,** amely megfelel a webhelynek:  

* A webalkalmazás tarifacsomagjának megtekintése (ingyenes vagy normál)  
* Méretezési konfiguráció (példányok száma, virtuális gép mérete, automatikus méretezési beállítások)  
* Kvóták (tárolás, sávszélesség, CPU)  

Ezek az elemek **írási** hozzáférést igényelnek a webhelyet tartalmazó teljes **erőforráscsoporthoz:**  

* SSL-tanúsítványok és -kötések (az SSL-tanúsítványok megoszthatók az azonos erőforráscsoportban és földrajzi helyen lévő helyek között)  
* Riasztási szabályok  
* Automatikus méretezési beállítások  
* Alkalmazáselemzési összetevők  
* Webes tesztek  

## <a name="virtual-machine-features-that-require-write-access"></a>Írási hozzáférést igénylő virtuálisgép-szolgáltatások

A webalkalmazásokhoz hasonlóan a virtuális gép paneljének egyes funkciói írási hozzáférést igényelnek a virtuális géphez vagy az erőforráscsoport más erőforrásaihoz.

A virtuális gépek tartománynevekhez, virtuális hálózatokhoz, tárfiókokhoz és riasztási szabályokhoz kapcsolódnak.

Ezek az elemek **írási** hozzáférést igényelnek a **virtuális géphez:**

* Végpontok  
* IP-címek  
* Lemezek  
* Bővítmények  

Ezek **írási** hozzáférést igényelnek mind a **virtuális géphez**, mind a tartománynévvel együtt lévő **erőforráscsoporthoz(a** tartománynévvel együtt), amelyben szerepel:  

* Rendelkezésre állási csoport  
* Kiegyensúlyozott terhelés készlet  
* Riasztási szabályok  

Ha ezek hez nem tud hozzáférni, kérje meg a rendszergazdát, hogy járulékalapú hozzáférést kérjen az Erőforrás csoporthoz.

## <a name="azure-functions-and-write-access"></a>Azure Functions és írási hozzáférés

Az [Azure Functions](../azure-functions/functions-overview.md) egyes funkciói írási hozzáférést igényelnek. Ha például egy felhasználóhoz [Reader](built-in-roles.md#reader) van rendelve olvasószerepkör, akkor nem fogja tudni megtekinteni a függvényalkalmazás funkcióit. A portálon megjelenik **a (Nincs hozzáférés)**.

![A függvényalkalmazások nem rendelkeznek hozzáféréssel](./media/troubleshooting/functionapps-noaccess.png)

Az olvasó a **Platform funkciói** fülre, majd a **Minden beállítás** elemre kattintva megtekintheti a függvényalkalmazáshoz kapcsolódó beállításokat (hasonlóan egy webalkalmazáshoz), de ezek a beállítások nem módosíthatók. Ezeknek a funkcióknak a eléréséhez szüksége lesz a [közreműködői](built-in-roles.md#contributor) szerepkörre.

## <a name="next-steps"></a>További lépések

- [Vendégfelhasználók – hibaelhárítás](role-assignments-external-users.md#troubleshoot)
- [Azure-erőforrásokhoz való hozzáférés kezelése az RBAC és az Azure Portal használatával](role-assignments-portal.md)
- [Az Azure-erőforrások RBAC-változásainak tevékenységnaplóinak megtekintése](change-history-report.md)
