---
title: Programozott módon a vállalati Azure-előfizetések létrehozása |} Microsoft Docs
description: Megtudhatja, hogyan programozott módon további Azure Enterprise vagy a vállalati fejlesztési és tesztelési célú előfizetést létrehozni.
services: azure-resource-manager
author: jlian
manager: jlian
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/30/2018
ms.author: jlian
ms.openlocfilehash: 8d1eb3229f22b2da3a356562250fedb3c35c4816
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Programozott módon létrehozása az Azure vállalati előfizetések (előzetes verzió)

Egy Azure ügyfélként a [nagyvállalati szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), és hozhat létre (MS-AZR - 0017 P) EA EA fejlesztési és tesztelési célú (MS-AZR - 0148 P) előfizetések programozott módon. Egy másik felhasználó vagy egyszerű szolgáltatás fiókja terhelve előfizetések létrehozása engedéllyel, biztosítva számukra [szerepköralapú hozzáférés-vezérlést (RBAC)](../active-directory/role-based-access-control-configure.md) hozzáfér a beléptetési fiókjához. 

> [!IMPORTANT]
> Ez az API-n keresztül létrehozott Azure szóló a szerződést, amely alatt szerzett be a Microsoft Azure-szolgáltatások a Microsoft és egy hivatalos viszonteladóitól vonatkozik. További tudnivalókért lásd: [Microsoft Azure jogi információk](https://azure.microsoft.com/support/legal/).

Ez a cikk a tartalma:

> [!div class="checklist"]
> * Megtudhatja, hogyan programozott módon, Azure Resource Manager-előfizetések létrehozása
> * Ismerje meg, hogyan számára az RBAC használatát képes létrehozni az előfizetések EA fiókjába számlázva megosztása

További tájékoztatás a [.NET mintakód a Githubon](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).

## <a name="ask-your-ea-enrollment-admin-to-add-you-as-account-owner"></a>Kérje meg a EA beléptetési rendszergazdát hozzáadása, a fiók tulajdonosa

Első lépésként kérje meg a beléptetési rendszergazdáját, hogy [adja hozzá azt a EA portál használatával a fiók tulajdonosa](https://ea.azure.com/helpdocs/addNewAccount) (napló-a szükséges). Kövesse az utasításokat a meghívó e-mailben történő kezdeti előfizetéssel, manuálisan hozzon létre.

> [!IMPORTANT]
> Fiók tulajdonjogát kell, és manuálisan hozzon létre egy kezdeti EA előfizetéshez, mielőtt továbblép a következő lépéssel. Csak a fiók hozzáadása a tagság nincs elegendő.

## <a name="find-accounts-you-have-access-to"></a>Található fiók rendelkezik hozzáféréssel

Az Azure EA tagság fiók tulajdonos van hozzá, Azure fiókregisztrálást kapcsolat használatával az előfizetés költségek számlázási helyének meghatározása. Előfizetések létrehozásához először megállapítása milyen beléptetési fiók rendelkezik hozzáféréssel. Ha Ön jelenleg egy EA fiók tulajdonosa, és próbálja használni az API, az Azure a következő feltételek teljesülését ellenőrzi:

- A fiók hozzá lett adva az EA tagság
- Rendelkezik egy vagy több EA vagy EA fejlesztési és tesztelési célú előfizetés, ami azt jelenti, hogy Ön már állapotba keresztül előfizetési manuális legalább egyszer
- Jelentkezett be a fiók tulajdonosának *kezdőkönyvtár*, amely az, hogy a könyvtár, előfizetések alapértelmezés szerint létrehozott

A fenti két feltétel teljesülése esetén egy `enrollmentAccount` erőforrás ad vissza, és az adott fiók előfizetések létrehozásához. A fiók alatt létrehozott összes előfizetés számlázása a Nagyvállalati beléptetés, amely a fiók felé.

# <a name="resttabrest"></a>[REST](#tab/rest)

A kérelem fiókokhoz beléptetési listázásához:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Azure válaszol az összes regisztrációs fiók rendelkezik hozzáféréssel listáját:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Használja a [Get-AzureRmEnrollmentAccount parancs](/powershell/module/azurerm.billing/get-azurermenrollmentaccount) listát az összes regisztrációs fiók rendelkezik hozzáféréssel.

```azurepowershell-interactive
Get-AzureRmEnrollmentAccount
```

Azure válaszol, a fiókok objektumazonosítók és az e-mail címek listáját.

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Használja a [az számlázási beléptetési-fióklista](https://aka.ms/EASubCreationPublicPreviewCLI) paranccsal listát készíthet összes beléptetési fiók rendelkezik hozzáféréssel.

```azurecli-interactive 
az billing enrollment-account list
```
Azure válaszol, a fiókok objektumazonosítók és az e-mail címek listáját.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

---

Használja a `principalName` tulajdonság segítségével azonosítja a fiókot, amelyet az előfizetések terhelve. Használja a `id` , a `enrollmentAccount` érték, amely a következő lépésben az előfizetés létrehozásához használt.

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Egy adott beléptetési fiókhoz tartozó előfizetések létrehozása 

Az alábbi példakód létrehozza a nevű előfizetést létrehozására vonatkozó kérelem *fejlesztői csapat előfizetés* és előfizetés ajánlat *MS-AZR - 0017P* (rendszeres EA). A beléptetési fiók `747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (helyőrző értékét, ez az egy GUID), amely beléptetési fiókjának van SignUpEngineering@contoso.com. Azt is hozzáadja a két felhasználókat RBAC tulajdonosként az előfizetéshez.

# <a name="resttabrest"></a>[REST](#tab/rest)

Használja a `id` , a `enrollmentAccount` az előfizetés létrehozására vonatkozó kérelem elérési útját.

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

{
  "displayName": "Dev Team Subscription",
  "offerType": "MS-AZR-0017P",
  "owners": [
    {
      "objectId": "<userObjectId>"
    },
    {
      "objectId": "<servicePrincipalObjectId>"
    }
  ]
}
```

| Elem neve  | Szükséges | Típus   | Leírás                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Nem      | Karakterlánc | Az előfizetés megjelenítendő nevét. Ha nincs megadva, a nevét, például a "Microsoft Azure Enterprise". az ajánlat beállítása                                 |
| `offerType`   | Igen      | Karakterlánc | Az ajánlat az előfizetés. A két beállítás megadása a EA [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (éles környezetben való használathoz) és [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (fejlesztési és tesztelési célú, kell lennie [-e kapcsolva a EA portálon](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Nem       | Karakterlánc | Minden olyan felhasználó, amelyeket meg szeretne létrehozásakor adja hozzá az előfizetés RBAC tulajdonost objektum azonosítója.  |

A válaszban vissza egy `subscriptionOperation` objektum figyelésre. Ha az előfizetés létrehozása befejeződött, a `subscriptionOperation` objektum meghaladná a `subscriptionLink` objektum, amely rendelkezik az előfizetés-azonosító.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Ez a minta modul használatához telepítse futtatásával `Install-Module AzureRM.Subscription -AllowPrerelease` első. Győződjön meg arról, hogy `-AllowPrerelease` működik, a PowerShellGet legújabb verziójának telepítése [PowerShellGet lekérési modulja](/powershell/gallery/psget/get_psget_module).

Használja a [New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview) együtt `enrollmentAccount` objektumazonosító, a `EnrollmentAccountObjectId` paraméter segítségével hozzon létre egy új előfizetést. 

```azurepowershell-interactive
New-AzureRmSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId 747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx -OwnerObjectId <userObjectId>,<servicePrincipalObjectId>
```

| Elem neve  | Szükséges | Típus   | Leírás                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Nem      | Karakterlánc | Az előfizetés megjelenítendő nevét. Ha nincs megadva, a nevét, például a "Microsoft Azure Enterprise". az ajánlat beállítása                                 |
| `OfferType`   | Igen      | Karakterlánc | Az ajánlat az előfizetés. A két beállítás megadása a EA [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (éles környezetben való használathoz) és [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (fejlesztési és tesztelési célú, kell lennie [-e kapcsolva a EA portálon](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Igen       | Karakterlánc | Az eszközregisztráció-fiók, hogy az előfizetés alapján létre és terhelve objektum azonosítója. A kapott GUID-érték `Get-AzureRmEnrollmentAccount`. |
| `OwnerObjectId`      | Nem       | Karakterlánc | Minden olyan felhasználó, amelyeket meg szeretne létrehozásakor adja hozzá az előfizetés RBAC tulajdonost objektum azonosítója.  |
| `OwnerSignInName`    | Nem       | Karakterlánc | Minden olyan felhasználó, amelyeket meg szeretne létrehozásakor adja hozzá az előfizetés RBAC tulajdonost e-mail címe. Ez a paraméter nem használható `OwnerObjectId`.|
| `OwnerApplicationId` | Nem       | Karakterlánc | Az alkalmazás Azonosítóját a bármely szolgáltatás egyszerű, amelyeket meg szeretne létrehozásakor adja hozzá az előfizetés RBAC tulajdonost. Ez a paraméter nem használható `OwnerObjectId`.| 

Minden paraméterek teljes listájának megtekintéséhez lásd: [New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview).

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

A kép bővítmény használatával telepítse futtatásával `az extension add --name subscription` első.

Használja a [az fiók létrehozása](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) együtt `enrollmentAccount` objektumazonosító, a `enrollment-account-object-id` paraméter segítségével hozzon létre egy új előfizetést.

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Elem neve  | Szükséges | Típus   | Leírás                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Nem      | Karakterlánc | Az előfizetés megjelenítendő nevét. Ha nincs megadva, a nevét, például a "Microsoft Azure Enterprise". az ajánlat beállítása                                 |
| `offer-type`   | Igen      | Karakterlánc | Az ajánlat az előfizetés. A két beállítás megadása a EA [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (éles környezetben való használathoz) és [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (fejlesztési és tesztelési célú, kell lennie [-e kapcsolva a EA portálon](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Igen       | Karakterlánc | Az eszközregisztráció-fiók, hogy az előfizetés alapján létre és terhelve objektum azonosítója. A kapott GUID-érték `az billing enrollment-account list`. |
| `owner-object-id`      | Nem       | Karakterlánc | Minden olyan felhasználó, amelyeket meg szeretne létrehozásakor adja hozzá az előfizetés RBAC tulajdonost objektum azonosítója.  |
| `owner-upn`    | Nem       | Karakterlánc | Minden olyan felhasználó, amelyeket meg szeretne létrehozásakor adja hozzá az előfizetés RBAC tulajdonost e-mail címe. Ez a paraméter nem használható `owner-object-id`.|
| `owner-spn` | Nem       | Karakterlánc | Az alkalmazás Azonosítóját a bármely szolgáltatás egyszerű, amelyeket meg szeretne létrehozásakor adja hozzá az előfizetés RBAC tulajdonost. Ez a paraméter nem használható `owner-object-id`.| 

Minden paraméterek teljes listájának megtekintéséhez lásd: [az fiók létrehozása](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

----

## <a name="delegate-access-to-an-enrollment-account-using-rbac"></a>Egy olyan beléptetési fiókot az RBAC használata delegálása

Egy másik felhasználó vagy szolgáltatás egyszerű adhat egy adott fiók-előfizetések létrehozása olyan [számukra az RBAC tulajdonosi szerepkört, a beléptetési fiók a hatókörben](../active-directory/role-based-access-control-manage-access-rest.md). Az alábbi példában a felhasználó adja meg a bérlői `principalId` a `<userObjectId>` (a SignUpEngineering@contoso.com) egy tulajdonosi szerepkört, a beléptetési fiók. 

# <a name="resttabrest"></a>[REST](#tab/rest)

```json
PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>"
  }
}
```
Tulajdonosi szerepkör sikeresen hozzá van rendelve, a beléptetési fiók hatókörben, amikor a szerepkör-hozzárendelés információkkal válaszol, Azure:

```json
{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>",
    "scope": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "createdOn": "2018-03-05T08:36:26.4014813Z",
    "updatedOn": "2018-03-05T08:36:26.4014813Z",
    "createdBy": "<assignerObjectId>",
    "updatedBy": "<assignerObjectId>"
  },
  "id": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "<roleAssignmentGuid>"
}
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Használja a [New-AzureRmRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) adhat egy másik felhasználónak tulajdonosi hozzáférés igénylés fiókjára.

```azurepowershell-interactive
New-AzureRmRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Használja a [az szerepkör-hozzárendelés létrehozása](../active-directory/role-based-access-control-manage-access-azure-cli.md) adhat egy másik felhasználónak tulajdonosi hozzáférés igénylés fiókjára.

```azurecli-interactive 
az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

----

Miután egy felhasználó beléptetési fiókja RBAC tulajdonosát válik, annak alapján előfizetések programozott módon hozhatnak létre. Delegált felhasználó által létrehozott előfizetés még az eredeti tulajdonosa szolgáltatásadminisztrátoréval, de is rendelkezik a delegált felhasználó tulajdonos alapértelmezés szerint. 

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Tevékenység-naplók segítségével előfizetések létrehozó naplózási

Ez az API-n keresztül létrehozott előfizetések követheti nyomon, használja a [bérlői tevékenység napló API](/rest/api/monitor/tenantactivitylogs). Jelenleg nem lehetséges a PowerShell, a CLI vagy az Azure-portálon nyomon követéséhez előfizetés létrehozása.

1. Bérlői rendszergazdaként az Azure AD-bérlő [jogosultságszintjének emelése](../active-directory/role-based-access-control-tenant-admin-access.md) majd olvasó szerepkör hozzárendelése a naplózási felhasználói a hatókörben `/providers/microsoft.insights/eventtypes/management`.
1. A naplózási felhasználóként hívja a [bérlői tevékenység napló API](/rest/api/monitor/tenantactivitylogs) előfizetés létrehozása tevékenységek. Példa:

```
GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'" 
```

> [!NOTE]
> Kényelmesen hívja az API a parancssorból, próbálja ki a [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Vállalati Azure-előfizetés létrehozása API korlátozásai

- Csak a vállalati Azure-előfizetések az API segítségével hozhatók létre.
- 50 előfizetések fiókonként korlátozva van. Ezt követően előfizetések csak Account Center használatával hozhatók létre.
- Szükség van legalább egy EA vagy EA fejlesztési és tesztelési célú előfizetések alatt a fiók, amely azt jelenti, hogy a fiók tulajdonosának keresztülment legalább egyszer előfizetési manuális.
- Felhasználók, akik nem fiókhoz tulajdonosainak, de bekerült az RBAC, keresztül beléptetési fiók nem hozható létre előfizetéseket fióknak központ használatával.
- A bérlő létre kell hozni az előfizetés nem választhat. Az előfizetés mindig a fiók tulajdonosának otthoni bérlő jön létre. Az előfizetés áthelyezése egy másik bérlői, lásd: [előfizetés-bérlő módosítása](..\active-directory\active-directory-how-subscriptions-associated-directory.md).

## <a name="next-steps"></a>További lépések

* Például a .NET használatával előfizetések létrehozása, [mintát a Githubon code](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Azure Resource Manager és az API-k kapcsolatos további információkért lásd: [Azure Resource Manager áttekintése](resource-group-overview.md).
* A felügyeleti csoportok előfizetések nagy számú kezelésével kapcsolatos további tudnivalókért lásd: [az Azure felügyeleti csoportok-erőforrások rendszerezése](management-groups-overview.md)
* Egy átfogó bevált gyakorlatokat tartalmazó útmutatóval nagy méretű szervezeteknek előfizetés cégirányításra fókuszálnak, olvassa el [Azure enterprise scaffold - részletes utasításokkal megadott előfizetés-irányítás](resource-manager-subscription-governance.md)
