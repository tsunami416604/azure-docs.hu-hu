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
ms.date: 06/05/2018
ms.author: jlian
ms.openlocfilehash: df66ba1ec2c855a24731387210b0127892f5796f
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234784"
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Programozott módon létrehozása az Azure vállalati előfizetések (előzetes verzió)

Egy Azure ügyfélként a [nagyvállalati szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), és hozhat létre (MS-AZR - 0017 P) EA EA fejlesztési és tesztelési célú (MS-AZR - 0148 P) előfizetések programozott módon. Ebből a cikkből megismerheti, hogyan programozott módon, Azure Resource Manager előfizetést létrehozni.

Amikor ez az API hoz létre Azure-előfizetéssel, adott előfizetéshez szabályozza a szerződést, amely alatt a Microsoft és egy hivatalos viszonteladóitól beszerezte a Microsoft Azure-szolgáltatások. További tudnivalókért lásd: [Microsoft Azure jogi információk](https://azure.microsoft.com/support/legal/).

## <a name="prerequisites"></a>Előfeltételek

* A fióknak kell lennie az Azure EA tagság a fiók tulajdonosa. Ha nem, kérje meg a beléptetési rendszergazdáját, hogy [hozzáadnia a EA portálon fiók tulajdonos](https://ea.azure.com/helpdocs/addNewAccount) (napló-a szükséges). Kövesse az utasításokat a meghívó e-mailben történő kezdeti előfizetéssel, manuálisan hozzon létre. Fiók tulajdonjogát, és manuálisan hozzon létre egy kezdeti EA előfizetéshez, mielőtt továbblép a következő lépéssel. Ehhez mindössze a fiókot, a regisztráció nem elegendőek.

* Ha szeretné a Nagyvállalati előfizetéssel, hozzon létre egy egyszerű szolgáltatás segítségével, meg kell [adja meg, hogy a szolgáltatás egyszerű képes létrehozni az előfizetések](grant-access-to-create-subscription.md).

## <a name="find-accounts-you-have-access-to"></a>Található fiók rendelkezik hozzáféréssel

Az Azure EA tagság fiók tulajdonos van hozzá, Azure fiókregisztrálást kapcsolat használatával az előfizetés költségek számlázási helyének meghatározása. A fiók alatt létrehozott összes előfizetés számlázása a Nagyvállalati beléptetés, amely a fiók felé. Előfizetések létrehozása, át kell az eszközregisztráció-fiók és a felhasználó rendszerbiztonsági tagoknak az előfizetés tulajdonosa értékeket. 

A következő parancsok futtatásához meg kell bejelentkeznie a fiók tulajdonosára *kezdőkönyvtár*, amely az, hogy a könyvtár, előfizetések alapértelmezés szerint létrehozott.

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

Az alábbi példakód létrehozza a nevű előfizetést létrehozására vonatkozó kérelem *fejlesztői csapat előfizetés* és előfizetés ajánlat *MS-AZR - 0017P* (rendszeres EA). A beléptetési fiók `747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (helyőrző, ez az érték értéke GUID), amely az, hogy a beléptetési fiók a SignUpEngineering@contoso.com. Azt is hozzáadja a két felhasználókat RBAC tulajdonosként az előfizetéshez.

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
| `displayName` | Nem      | Sztring | Az előfizetés megjelenítendő nevét. Ha nincs megadva, a nevét, például a "Microsoft Azure Enterprise". az ajánlat beállítása                                 |
| `offerType`   | Igen      | Sztring | Az ajánlat az előfizetés. A két beállítás megadása a EA [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (éles környezetben való használathoz) és [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (fejlesztési és tesztelési célú, kell lennie [-e kapcsolva a EA portálon](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Nem       | Sztring | Minden olyan felhasználó, amelyeket meg szeretne létrehozásakor adja hozzá az előfizetés RBAC tulajdonost objektum azonosítója.  |

A válaszban vissza egy `subscriptionOperation` objektum figyelésre. Ha az előfizetés létrehozása befejeződött, a `subscriptionOperation` objektum meghaladná a `subscriptionLink` objektum, amely rendelkezik az előfizetés-azonosító.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Ez a minta modul használatához telepítse futtatásával `Install-Module AzureRM.Subscription -AllowPrerelease` első. Győződjön meg arról, hogy `-AllowPrerelease` működik, a PowerShellGet legújabb verziójának telepítése [PowerShellGet lekérési modulja](/powershell/gallery/installing-psget).

Használja a [New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview) együtt `enrollmentAccount` objektumazonosító, a `EnrollmentAccountObjectId` paraméter segítségével hozzon létre egy új előfizetést. 

```azurepowershell-interactive
New-AzureRmSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId 747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx -OwnerObjectId <userObjectId>,<servicePrincipalObjectId>
```

| Elem neve  | Szükséges | Típus   | Leírás                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Nem      | Sztring | Az előfizetés megjelenítendő nevét. Ha nincs megadva, a nevét, például a "Microsoft Azure Enterprise". az ajánlat beállítása                                 |
| `OfferType`   | Igen      | Sztring | Az ajánlat az előfizetés. A két beállítás megadása a EA [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (éles környezetben való használathoz) és [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (fejlesztési és tesztelési célú, kell lennie [-e kapcsolva a EA portálon](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Igen       | Sztring | Az eszközregisztráció-fiók, hogy az előfizetés alapján létre és terhelve objektum azonosítója. Az értéket nem kapott GUID `Get-AzureRmEnrollmentAccount`. |
| `OwnerObjectId`      | Nem       | Sztring | Minden olyan felhasználó, amelyeket meg szeretne létrehozásakor adja hozzá az előfizetés RBAC tulajdonost objektum azonosítója.  |
| `OwnerSignInName`    | Nem       | Sztring | Minden olyan felhasználó, amelyeket meg szeretne létrehozásakor adja hozzá az előfizetés RBAC tulajdonost e-mail címe. Ez a paraméter nem használható `OwnerObjectId`.|
| `OwnerApplicationId` | Nem       | Sztring | Az alkalmazás Azonosítóját a bármely szolgáltatás egyszerű, amelyeket meg szeretne létrehozásakor adja hozzá az előfizetés RBAC tulajdonost. Ez a paraméter nem használható `OwnerObjectId`.| 

Minden paraméterek teljes listájának megtekintéséhez lásd: [New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview).

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

A kép bővítmény használatával telepítse futtatásával `az extension add --name subscription` első.

Használja a [az fiók létrehozása](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) együtt `enrollmentAccount` objektumazonosító, a `enrollment-account-object-id` paraméter segítségével hozzon létre egy új előfizetést.

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Elem neve  | Szükséges | Típus   | Leírás                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Nem      | Sztring | Az előfizetés megjelenítendő nevét. Ha nincs megadva, a nevét, például a "Microsoft Azure Enterprise". az ajánlat beállítása                                 |
| `offer-type`   | Igen      | Sztring | Az ajánlat az előfizetés. A két beállítás megadása a EA [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (éles környezetben való használathoz) és [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (fejlesztési és tesztelési célú, kell lennie [-e kapcsolva a EA portálon](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Igen       | Sztring | Az eszközregisztráció-fiók, hogy az előfizetés alapján létre és terhelve objektum azonosítója. Az értéket nem kapott GUID `az billing enrollment-account list`. |
| `owner-object-id`      | Nem       | Sztring | Minden olyan felhasználó, amelyeket meg szeretne létrehozásakor adja hozzá az előfizetés RBAC tulajdonost objektum azonosítója.  |
| `owner-upn`    | Nem       | Sztring | Minden olyan felhasználó, amelyeket meg szeretne létrehozásakor adja hozzá az előfizetés RBAC tulajdonost e-mail címe. Ez a paraméter nem használható `owner-object-id`.|
| `owner-spn` | Nem       | Sztring | Az alkalmazás Azonosítóját a bármely szolgáltatás egyszerű, amelyeket meg szeretne létrehozásakor adja hozzá az előfizetés RBAC tulajdonost. Ez a paraméter nem használható `owner-object-id`.| 

Minden paraméterek teljes listájának megtekintéséhez lásd: [az fiók létrehozása](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

----

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Vállalati Azure-előfizetés létrehozása API korlátozásai

- Csak a vállalati Azure-előfizetések az API segítségével hozhatók létre.
- 50 előfizetések fiókonként korlátozva van. Ezt követően előfizetések csak Account Center használatával hozhatók létre.
- Szükség van legalább egy EA vagy EA fejlesztési és tesztelési célú előfizetések alatt a fiók, amely azt jelenti, hogy a fiók tulajdonosának keresztülment legalább egyszer előfizetési manuális.
- Felhasználók, akik nem fiókhoz tulajdonosainak, de bekerült az RBAC, keresztül beléptetési fiók nem hozható létre előfizetéseket fióknak központ használatával.
- A bérlő létre kell hozni az előfizetés nem választhat. Az előfizetés mindig a fiók tulajdonosának otthoni bérlő jön létre. Az előfizetés áthelyezése egy másik bérlői, lásd: [előfizetés-bérlő módosítása](..\active-directory\active-directory-how-subscriptions-associated-directory.md).

## <a name="next-steps"></a>További lépések

* Például a .NET használatával előfizetések létrehozása, [mintát a Githubon code](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Most, hogy létrehozta az előfizetést, más felhasználók és szolgáltatásnevekről meg lehet adni, hogy képes. További információkért lásd: [hozzáférést (előzetes verzió) Azure vállalati előfizetést létrehozni](grant-access-to-create-subscription.md).
* A felügyeleti csoportok előfizetések nagy számú kezelésével kapcsolatos további tudnivalókért lásd: [az Azure felügyeleti csoportok-erőforrások rendszerezése](management-groups-overview.md)
