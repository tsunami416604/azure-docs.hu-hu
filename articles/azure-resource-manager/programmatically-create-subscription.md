---
title: Azure Enterprise-előfizetések programozott létrehozása | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre programozott módon további Azure Enterprise-vagy Enterprise Dev/Test-előfizetéseket.
services: azure-resource-manager
author: jureid
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/10/2019
ms.author: jureid
ms.openlocfilehash: 755eabe97508b403205ff04a8d2d35feee314eb9
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258928"
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Azure Enterprise-előfizetések programozott létrehozása (előzetes verzió)

[Nagyvállalati szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)Azure-ügyfélként az EA (MS-AZR-0017P) és az EA dev/test (MS-AZR-0148P) előfizetések programozott módon hozhatók létre. Ebből a cikkből megtudhatja, hogyan hozhat létre programozott módon előfizetéseket Azure Resource Manager használatával.

Amikor létrehoz egy Azure-előfizetést ebből az API-ból, az előfizetést a szerződés szabályozza, amely alatt a Microsofttól vagy egy erre a szolgáltatástól kapott Microsoft Azure szolgáltatásokat. További információ: [Microsoft Azure jogi információk](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Rendelkeznie kell tulajdonosi szerepkörrel azon a beléptetési fiókon, amelyhez előfizetéseket kíván létrehozni. Ezeket a szerepköröket kétféleképpen érheti el:

* A beléptetési rendszergazda elvégezheti a [fiók tulajdonosát](https://ea.azure.com/helpdocs/addNewAccount) (bejelentkezés szükséges), amely a beléptetési fiók tulajdonosát teszi lehetővé. Kövesse a meghívói e-mailben kapott utasításokat a kezdeti előfizetés manuális létrehozásához. Erősítse meg a fiók tulajdonjogát, és hozzon létre manuálisan egy kezdeti EA-előfizetést, mielőtt továbblép a következő lépésre. Ha csak a fiókot adja hozzá a regisztrációhoz, nem elég.

* A beléptetési fiók meglévő tulajdonosa [engedélyezheti a hozzáférést](grant-access-to-create-subscription.md). Hasonlóképpen, ha egyszerű szolgáltatásnév használatával kívánja létrehozni az EA-előfizetést, meg kell adnia, [hogy az egyszerű szolgáltatás előfizetéseket hozzon létre](grant-access-to-create-subscription.md).

## <a name="find-accounts-you-have-access-to"></a>Azon fiókok keresése, amelyekhez hozzáférése van

Miután hozzáadta egy Azure EA-regisztrációhoz a fiók tulajdonosaként, az Azure a fiók és a regisztrációs kapcsolat alapján határozza meg, hogy hol kell számlázni az előfizetési díjakat. A fiókban létrehozott összes előfizetés számlázása a fiókhoz tartozó EA-regisztráció felé történik. Az előfizetések létrehozásához át kell adni a beléptetési fiókra vonatkozó értékeket, valamint az előfizetéshez tartozó felhasználói rendszerbiztonsági tagokat. 

A következő parancsok futtatásához be kell jelentkeznie a fiók tulajdonosa *kezdőkönyvtárának könyvtárába*, amely az a könyvtár, amelyet az előfizetések alapértelmezés szerint hoznak létre.

## <a name="resttabrest"></a>[REST](#tab/rest)

Az összes olyan regisztrációs fiók listázására vonatkozó kérelem, amelyhez hozzáféréssel rendelkezik:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Az Azure válaszol az összes beléptetési fiók listájára:

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

`principalName` A tulajdonsággal azonosíthatja azt a fiókot, amelyre az előfizetéseket számlázni kívánja. Másolja a `name` fiókját. Ha például a beléptetési fiókkal szeretne előfizetéseket létrehozni, másolja SignUpEngineering@contoso.com ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```a következőt:. Ez az azonosító a beléptetési fiók objektumazonosító. Illessze be ezt az értéket valahova, hogy a következő lépésben `enrollmentAccountObjectId`használhatja azt.

## <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Nyissa meg [Azure Cloud Shell](https://shell.azure.com/) és válassza a PowerShell lehetőséget.

A [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) parancsmag használatával listázhatja az összes olyan regisztrációs fiókot, amelyhez hozzáféréssel rendelkezik.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Az Azure az Ön számára elérhető regisztrációs fiókok listájával válaszol:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
`principalName` A tulajdonsággal azonosíthatja azt a fiókot, amelyre az előfizetéseket számlázni kívánja. Másolja a `ObjectId` fiókját. Ha például a beléptetési fiókkal szeretne előfizetéseket létrehozni, másolja SignUpEngineering@contoso.com ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```a következőt:. Illessze be ezt az objektumazonosítót valahova, hogy a következő lépésben `enrollmentAccountObjectId`használhassa azt.

## <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Az az [Billing beléptetés-Account List](https://aka.ms/EASubCreationPublicPreviewCLI) paranccsal listázhatja az összes olyan regisztrációs fiókot, amelyhez hozzáférése van.

```azurecli-interactive 
az billing enrollment-account list
```

Az Azure az Ön számára elérhető regisztrációs fiókok listájával válaszol:

```json
[
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "SignUpEngineering@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  },
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "BillingPlatformTeam@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  }
]
```

`principalName` A tulajdonsággal azonosíthatja azt a fiókot, amelyre az előfizetéseket számlázni kívánja. Másolja a `name` fiókját. Ha például a beléptetési fiókkal szeretne előfizetéseket létrehozni, másolja SignUpEngineering@contoso.com ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```a következőt:. Ez az azonosító a beléptetési fiók objektumazonosító. Illessze be ezt az értéket valahova, hogy a következő lépésben `enrollmentAccountObjectId`használhatja azt.

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Előfizetések létrehozása egy adott beléptetési fiók alapján

Az alábbi példa egy *Dev Team-előfizetés* nevű előfizetést hoz létre az előző lépésben kiválasztott beléptetési fiókban. Az előfizetési ajánlat az *MS-AZR-0017P* (normál Microsoft nagyvállalati szerződés). Opcionálisan két felhasználót is hozzáadhat az előfizetéshez RBAC-tulajdonosként.

# <a name="resttabrest"></a>[REST](#tab/rest)

Végezze el a következő kérelmet `<enrollmentAccountObjectId>` , `name` és cserélje le az elemet az első```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```lépésből (). Ha meg szeretné adni a tulajdonosokat, ismerkedjen meg [a felhasználói objektumok azonosítóinak beolvasásával](grant-access-to-create-subscription.md#userObjectId).

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

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

| Elem neve  | Kötelező | Típus   | Leírás                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Nem      | Sztring | Az előfizetés megjelenített neve. Ha nincs megadva, az ajánlat neve (például "Microsoft Azure Enterprise") van beállítva.                                 |
| `offerType`   | Igen      | Sztring | Az előfizetés ajánlata. Az EA két lehetőségét az [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (éles használat) és az [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/test) használatára kell [bekapcsolni az EA Portal használatával](https://ea.azure.com/helpdocs/DevOrTestOffer).                |
| `owners`      | Nem       | Sztring | Az előfizetésben a létrehozáskor RBAC-tulajdonosként hozzáadni kívánt felhasználó objektumazonosító.  |

A válaszban egy `subscriptionOperation` objektumot kell visszakapnia a figyeléshez. Az előfizetés létrehozása után az `subscriptionOperation` objektum egy olyan `subscriptionLink` objektumot ad vissza, amely az előfizetés-azonosítóval rendelkezik.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Először telepítse ezt az előzetes verziójú modult `Install-Module Az.Subscription -AllowPrerelease`a futtatásával. A működésének `-AllowPrerelease` biztosításához telepítse a PowerShellGet legújabb verzióját a [Get PowerShellGet modulból](/powershell/gallery/installing-psget).

Futtassa a [New-AzSubscription](/powershell/module/az.subscription) parancsot az alábbi parancsban, `ObjectId` és cserélje `<enrollmentAccountObjectId>` le az adatokat az```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```első lépésben (). Ha meg szeretné adni a tulajdonosokat, ismerkedjen meg [a felhasználói objektumok azonosítóinak beolvasásával](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Elem neve  | Kötelező | Típus   | Leírás                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Nem      | Sztring | Az előfizetés megjelenített neve. Ha nincs megadva, az ajánlat neve (például "Microsoft Azure Enterprise") van beállítva.                                 |
| `OfferType`   | Igen      | Sztring | Az előfizetés ajánlata. Az EA két lehetőségét az [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (éles használat) és az [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/test) használatára kell [bekapcsolni az EA Portal használatával](https://ea.azure.com/helpdocs/DevOrTestOffer).                |
| `EnrollmentAccountObjectId`      | Igen       | Sztring | Annak a beléptetési fióknak az azonosítója, amelyre az előfizetést létrehozták, majd a számlázásra kerül. Ez az érték egy GUID azonosító, amelyből `Get-AzEnrollmentAccount`származik. |
| `OwnerObjectId`      | Nem       | Sztring | Az előfizetésben a létrehozáskor RBAC-tulajdonosként hozzáadni kívánt felhasználó objektumazonosító.  |
| `OwnerSignInName`    | Nem       | Sztring | Annak a felhasználónak az e-mail-címe, amelyet RBAC-tulajdonosként szeretne hozzáadni az előfizetéshez a létrehozásakor. Ezt a paramétert használhatja a `OwnerObjectId`helyett.|
| `OwnerApplicationId` | Nem       | Sztring | Az előfizetésben a létrehozáskor RBAC-tulajdonosként hozzáadni kívánt egyszerű szolgáltatásnév alkalmazás-azonosítója. Ezt a paramétert használhatja a `OwnerObjectId`helyett. A paraméter használatakor az egyszerű szolgáltatásnak [olvasási hozzáféréssel kell rendelkeznie a címtárhoz](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Az összes paraméter teljes listájának megtekintéséhez lásd: [New-AzSubscription](/powershell/module/az.subscription).

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Először telepítse ezt az előzetes verziójú bővítményt `az extension add --name subscription`a futtatásával.

Futtassa az az [Account Create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) parancsot az alábbi paranccsal `<enrollmentAccountObjectId>` , és `name` cserélje le az első lépésben lemásolt elemre (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Ha meg szeretné adni a tulajdonosokat, ismerkedjen meg [a felhasználói objektumok azonosítóinak beolvasásával](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Elem neve  | Kötelező | Típus   | Leírás                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Nem      | Sztring | Az előfizetés megjelenített neve. Ha nincs megadva, az ajánlat neve (például "Microsoft Azure Enterprise") van beállítva.                                 |
| `offer-type`   | Igen      | Sztring | Az előfizetés ajánlata. Az EA két lehetőségét az [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (éles használat) és az [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/test) használatára kell [bekapcsolni az EA Portal használatával](https://ea.azure.com/helpdocs/DevOrTestOffer).                |
| `enrollment-account-object-id`      | Igen       | Sztring | Annak a beléptetési fióknak az azonosítója, amelyre az előfizetést létrehozták, majd a számlázásra kerül. Ez az érték egy GUID azonosító, amelyből `az billing enrollment-account list`származik. |
| `owner-object-id`      | Nem       | Sztring | Az előfizetésben a létrehozáskor RBAC-tulajdonosként hozzáadni kívánt felhasználó objektumazonosító.  |
| `owner-upn`    | Nem       | Sztring | Annak a felhasználónak az e-mail-címe, amelyet RBAC-tulajdonosként szeretne hozzáadni az előfizetéshez a létrehozásakor. Ezt a paramétert használhatja a `owner-object-id`helyett.|
| `owner-spn` | Nem       | Sztring | Az előfizetésben a létrehozáskor RBAC-tulajdonosként hozzáadni kívánt egyszerű szolgáltatásnév alkalmazás-azonosítója. Ezt a paramétert használhatja a `owner-object-id`helyett. A paraméter használatakor az egyszerű szolgáltatásnak [olvasási hozzáféréssel kell rendelkeznie a címtárhoz](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Az összes paraméter teljes listájának megjelenítéséhez tekintse meg az [az Account Create (fiók létrehozása](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create)) című témakört.

---

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Az Azure Enterprise előfizetés-létrehozási API korlátai

- Ezzel az API-val csak az Azure nagyvállalati előfizetéseket lehet létrehozni.
- A regisztrációs fiókhoz legfeljebb 200 előfizetés adható meg. Ezt követően a fiókhoz tartozó több előfizetés csak a Account Center használatával hozható létre. Ha több előfizetést szeretne létrehozni az API-n keresztül, hozzon létre egy másik beléptetési fiókot.
- Azok a felhasználók, akik nem rendelkeznek a tulajdonosokkal, de a RBAC keresztül regisztráltak egy regisztrációs fiókba, nem hozhatnak létre előfizetéseket a Account Center használatával.
- Nem választhatja ki azt a bérlőt, amelyhez az előfizetést létre kívánja hozni. Az előfizetést a rendszer mindig a fiók tulajdonosának Kezdőlap bérlője hozza létre. Az előfizetés másik bérlőre való áthelyezésével kapcsolatban lásd az [előfizetés-bérlő módosítása](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)című témakört.

## <a name="next-steps"></a>További lépések

* Az előfizetések .NET-tel történő létrehozásával kapcsolatos példa: [mintakód a githubon](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Most, hogy létrehozott egy előfizetést, megadhatja ezt a lehetőséget más felhasználóknak és egyszerű szolgáltatásoknak. További információ: [hozzáférés biztosítása Azure Enterprise-előfizetések létrehozásához (előzetes verzió)](grant-access-to-create-subscription.md).
* Ha többet szeretne megtudni a nagy számú előfizetés felügyeleti csoportokkal való kezelésével kapcsolatban, tekintse meg [az erőforrások rendszerezése az Azure felügyeleti csoportok](management-groups-overview.md) segítségével című témakört.
