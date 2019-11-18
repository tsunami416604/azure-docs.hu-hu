---
title: Azure-előfizetések programozott létrehozása
description: Ismerje meg, hogyan hozhat létre programozott módon további Azure-előfizetéseket.
author: amberb
ms.topic: conceptual
ms.date: 04/10/2019
ms.author: banders
ms.openlocfilehash: 7eb698fa22aee2cddb67a7eed44d48acddaa8905
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151002"
---
# <a name="programmatically-create-azure-subscriptions-preview"></a>Azure-előfizetések programozott létrehozása (előzetes verzió)

A [nagyvállalati szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), a [Microsoft Customer Agreement (MCA)](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) vagy a [Microsoft Partner Agreement (MPa)](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) számlázási fiókkal rendelkező Azure-ügyfelek programozott módon hozhatnak létre előfizetéseket. Ebből a cikkből megtudhatja, hogyan hozhat létre programozott módon előfizetéseket Azure Resource Manager használatával.

Ha programozott módon hozza létre az Azure-előfizetést, az előfizetést arra a szerződés szabályozza, amelynek keretében az Azure-szolgáltatásokat beszerezte a Microsofttól vagy egy erre vonatkozó viszonteladótól. További információ: [Microsoft Azure jogi információk](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-subscriptions-for-an-ea-billing-account"></a>Előfizetés létrehozása egy EA számlázási fiókhoz

### <a name="prerequisites"></a>Előfeltételek

Előfizetés létrehozásához tulajdonosi szerepkörrel kell rendelkeznie a beléptetési fiókban. A szerepkört kétféleképpen szerezheti be:

* A regisztráció vállalati rendszergazdája elvégezheti a [fiók tulajdonosát](https://ea.azure.com/helpdocs/addNewAccount) (bejelentkezés szükséges), amely a beléptetési fiók tulajdonosát teszi lehetővé.

* A beléptetési fiók meglévő tulajdonosa [engedélyezheti a hozzáférést](grant-access-to-create-subscription.md). Hasonlóképpen, ha egyszerű szolgáltatásnevet szeretne létrehozni egy EA-előfizetéshez, meg kell adnia, [hogy az egyszerű szolgáltatás előfizetéseket hozzon létre](grant-access-to-create-subscription.md).

### <a name="find-accounts-you-have-access-to"></a>Azon fiókok keresése, amelyekhez hozzáférése van

Miután hozzáadta a fiók tulajdonosához társított beléptetési fiókot, az Azure a fiók és a regisztráció közötti kapcsolat alapján határozza meg, hogy hol kell számlázni az előfizetési díjakat. A fiókban létrehozott összes előfizetés számlázása a fiókhoz tartozó EA-regisztráció alapján történik. Az előfizetések létrehozásához át kell adni a beléptetési fiókra vonatkozó értékeket, valamint az előfizetéshez tartozó felhasználói rendszerbiztonsági tagokat. 

A következő parancsok futtatásához be kell jelentkeznie a fiók tulajdonosa *kezdőkönyvtárának könyvtárába*, amely az a könyvtár, amelyet az előfizetések alapértelmezés szerint hoznak létre.

### <a name="resttabrest"></a>[REST](#tab/rest)

Az összes olyan regisztrációs fiók listázására vonatkozó kérelem, amelyhez hozzáféréssel rendelkezik:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Az API-válasz felsorolja az összes olyan regisztrációs fiókot, amelyhez hozzáféréssel rendelkezik:

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

A `principalName` tulajdonsággal azonosíthatja azt a fiókot, amelyre az előfizetéseket számlázni kívánja. Másolja a fiók `name`ét. Ha például előfizetéseket szeretne létrehozni a SignUpEngineering@contoso.com beléptetési fiókban, akkor másolja ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Ez az azonosító a beléptetési fiók objektumazonosító. Illessze be ezt az értéket valahova, hogy a következő lépésben használja `enrollmentAccountObjectId`ként.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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
A `principalName` tulajdonsággal azonosíthatja azt a fiókot, amelyre az előfizetéseket számlázni kívánja. Másolja a fiók `ObjectId`ét. Ha például előfizetéseket szeretne létrehozni a SignUpEngineering@contoso.com beléptetési fiókban, akkor másolja ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Illessze be ezt az objektumazonosítót valahova, hogy a következő lépésben használja a `enrollmentAccountObjectId`.

### <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

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

A `principalName` tulajdonsággal azonosíthatja azt a fiókot, amelyre az előfizetéseket számlázni kívánja. Másolja a fiók `name`ét. Ha például előfizetéseket szeretne létrehozni a SignUpEngineering@contoso.com beléptetési fiókban, akkor másolja ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Ez az azonosító a beléptetési fiók objektumazonosító. Illessze be ezt az értéket valahova, hogy a következő lépésben használja `enrollmentAccountObjectId`ként.

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Előfizetések létrehozása egy adott beléptetési fiók alapján

Az alábbi példa egy *Dev Team-előfizetés* nevű előfizetést hoz létre az előző lépésben kiválasztott beléptetési fiókban. Az előfizetési ajánlat az *MS-AZR-0017P* (normál Microsoft nagyvállalati szerződés). Opcionálisan két felhasználót is hozzáadhat az előfizetéshez RBAC-tulajdonosként.

### <a name="resttabrest"></a>[REST](#tab/rest)

Hajtsa végre a következő kérést, amelyben cserélje le a `<enrollmentAccountObjectId>` értéket az előző lépésben kimásolt `name` értékével (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Ha meg szeretné adni a tulajdonosokat, ismerkedjen meg [a felhasználói objektumok azonosítóinak beolvasásával](grant-access-to-create-subscription.md#userObjectId).

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

A válaszban egy `subscriptionOperation` objektumot kap a figyeléshez. Az előfizetés létrehozása után a `subscriptionOperation` objektum egy `subscriptionLink` objektumot ad vissza, amely az előfizetés-AZONOSÍTÓval rendelkezik.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Először telepítse ezt az előzetes verziójú modult `Install-Module Az.Subscription -AllowPrerelease`futtatásával. A `-AllowPrerelease` működésének biztosításához telepítse a PowerShellGet legújabb verzióját a [Get PowerShellGet modulból](/powershell/gallery/installing-psget).

Futtassa az alábbi [New-AzSubscription](/powershell/module/az.subscription) parancsot, és cserélje le a `<enrollmentAccountObjectId>`t az első lépésben összegyűjtött `ObjectId`ra (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Ha meg szeretné adni a tulajdonosokat, ismerkedjen meg [a felhasználói objektumok azonosítóinak beolvasásával](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Elem neve  | Kötelező | Típus   | Leírás                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Nem      | Sztring | Az előfizetés megjelenített neve. Ha nincs megadva, az ajánlat neve (például "Microsoft Azure Enterprise") van beállítva.                                 |
| `OfferType`   | Igen      | Sztring | Az előfizetés ajánlata. Az EA két lehetőségét az [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (éles használat) és az [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/test) használatára kell [bekapcsolni az EA Portal használatával](https://ea.azure.com/helpdocs/DevOrTestOffer).                |
| `EnrollmentAccountObjectId`      | Igen       | Sztring | Annak a beléptetési fióknak az azonosítója, amelyre az előfizetést létrehozták, majd a számlázásra kerül. Ez az érték egy GUID azonosító, amelyet a `Get-AzEnrollmentAccount`tól kap. |
| `OwnerObjectId`      | Nem       | Sztring | Az előfizetésben a létrehozáskor RBAC-tulajdonosként hozzáadni kívánt felhasználó objektumazonosító.  |
| `OwnerSignInName`    | Nem       | Sztring | Annak a felhasználónak az e-mail-címe, amelyet RBAC-tulajdonosként szeretne hozzáadni az előfizetéshez a létrehozásakor. `OwnerObjectId`helyett ezt a paramétert használhatja.|
| `OwnerApplicationId` | Nem       | Sztring | Az előfizetésben a létrehozáskor RBAC-tulajdonosként hozzáadni kívánt egyszerű szolgáltatásnév alkalmazás-azonosítója. `OwnerObjectId`helyett ezt a paramétert használhatja. A paraméter használatakor az egyszerű szolgáltatásnak [olvasási hozzáféréssel kell rendelkeznie a címtárhoz](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Az összes paraméter teljes listájának megtekintéséhez lásd: [New-AzSubscription](/powershell/module/az.subscription).

### <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Először telepítse ezt az előzetes verziójú bővítményt `az extension add --name subscription`futtatásával.

Futtassa az az [Account Create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) parancsot az alábbi paranccsal, és cserélje le a `<enrollmentAccountObjectId>`t az első lépésben másolt `name`re (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Ha meg szeretné adni a tulajdonosokat, ismerkedjen meg [a felhasználói objektumok azonosítóinak beolvasásával](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Elem neve  | Kötelező | Típus   | Leírás                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Nem      | Sztring | Az előfizetés megjelenített neve. Ha nincs megadva, az ajánlat neve (például "Microsoft Azure Enterprise") van beállítva.                                 |
| `offer-type`   | Igen      | Sztring | Az előfizetés ajánlata. Az EA két lehetőségét az [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (éles használat) és az [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (dev/test) használatára kell [bekapcsolni az EA Portal használatával](https://ea.azure.com/helpdocs/DevOrTestOffer).                |
| `enrollment-account-object-id`      | Igen       | Sztring | Annak a beléptetési fióknak az azonosítója, amelyre az előfizetést létrehozták, majd a számlázásra kerül. Ez az érték egy GUID azonosító, amelyet a `az billing enrollment-account list`tól kap. |
| `owner-object-id`      | Nem       | Sztring | Az előfizetésben a létrehozáskor RBAC-tulajdonosként hozzáadni kívánt felhasználó objektumazonosító.  |
| `owner-upn`    | Nem       | Sztring | Annak a felhasználónak az e-mail-címe, amelyet RBAC-tulajdonosként szeretne hozzáadni az előfizetéshez a létrehozásakor. `owner-object-id`helyett ezt a paramétert használhatja.|
| `owner-spn` | Nem       | Sztring | Az előfizetésben a létrehozáskor RBAC-tulajdonosként hozzáadni kívánt egyszerű szolgáltatásnév alkalmazás-azonosítója. `owner-object-id`helyett ezt a paramétert használhatja. A paraméter használatakor az egyszerű szolgáltatásnak [olvasási hozzáféréssel kell rendelkeznie a címtárhoz](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Az összes paraméter teljes listájának megjelenítéséhez tekintse meg az [az Account Create (fiók létrehozása](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create)) című témakört.

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Az Azure Enterprise előfizetés-létrehozási API korlátai

- Ezzel az API-val csak az Azure nagyvállalati előfizetéseket lehet létrehozni.
- A regisztrációs fiókhoz legfeljebb 200 előfizetés adható meg. Ezt követően a fiókhoz több előfizetés is létrehozható a Azure Portalban. Ha több előfizetést szeretne létrehozni az API-n keresztül, hozzon létre egy másik beléptetési fiókot.
- Azok a felhasználók, akik nem rendelkeznek a tulajdonosokkal, de az RBAC-on keresztül regisztráltak egy regisztrációs fiókba, nem hozhatnak létre előfizetéseket a Azure Portal.
- Nem választhatja ki azt a bérlőt, amelyhez az előfizetést létre kívánja hozni. Az előfizetést a rendszer mindig a fiók tulajdonosának Kezdőlap bérlője hozza létre. Az előfizetés másik bérlőre való áthelyezésével kapcsolatban lásd az [előfizetés-bérlő módosítása](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)című témakört.


## <a name="create-subscriptions-for-an-mca-account"></a>Előfizetések létrehozása MCA-fiókhoz

### <a name="prerequisites"></a>Előfeltételek

Az előfizetések létrehozásához tulajdonosi, közreműködő vagy Azure-előfizetési szerepkörrel kell rendelkeznie egy számlázási vagy számlázási fiókban, illetve a tulajdonos vagy közreműködő szerepkörben. További információkért lásd [az előfizetés számlázási szerepköreit és azok feladatait](../billing/billing-understand-mca-roles.md#subscription-billing-roles-and-tasks).

Az alábbi példa a REST API-k használatát mutatja be. A PowerShell és az Azure CLI jelenleg nem támogatottak.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Azon számlázási fiókok keresése, amelyekhez hozzáférése van 

Az alábbi kérelem elvégzésével sorolja fel az összes számlázási fiókot.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
Az API-válasz felsorolja azokat a számlázási fiókokat, amelyekhez hozzáfér.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
A `displayName` tulajdonsággal azonosíthatja azt a számlázási fiókot, amelyhez előfizetéseket kíván létrehozni. Győződjön meg arról, hogy a fiók agreeementType *MicrosoftCustomerAgreement*. Másolja a fiók `name`ét.  Ha például előfizetést szeretne létrehozni az `Contoso` számlázási fiókhoz, akkor másolja `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Illessze be valahova ezt az értéket, hogy a következő lépésben használni tudja.

### <a name="find-invoice-sections-to-create-subscriptions"></a>Számlázási csoportok keresése előfizetések létrehozásához

Az előfizetéshez tartozó díjak a számlázási profil számlájának egy szakaszában jelennek meg. A következő API-val lekérheti az Azure-előfizetések létrehozására jogosult számla-és számlázási profilok listáját.

Hajtsa végre a következő kérést, amelyben cserélje le a `<billingAccountName>` értéket az előző lépésben kimásolt `name` értékével (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```).

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```
Az API-válasz felsorolja az összes számlázási szakaszt és azok számlázási profilját, amelyekhez hozzáféréssel rendelkezik az előfizetések létrehozásához:

```json
{
    "value": [{
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Development",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/invoiceSections/GJ77-xxxx-xxx-xxx"
    }, {
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Testing",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX"
  }]
}
    
```

A `invoiceSectionDisplayName` tulajdonsággal azonosíthatja azt a számla szakaszt, amelyhez előfizetéseket kíván létrehozni. Másolja a `invoiceSectionId`t, `billingProfileId` és az egyik `skuId` a számla szakaszra. Ha például egy `Microsoft Azure plan` típusú előfizetést szeretne létrehozni `Development` számla szakaszhoz, akkor másolja `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX`, `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` és `0001`. Illessze be ezeket az értékeket valahova, hogy azok a következő lépésben használhatók legyenek.

### <a name="create-a-subscription-for-an-invoice-section"></a>Előfizetés létrehozása a számla szakaszhoz

A következő példában létrehozunk egy, a *fejlesztési* számla szakaszhoz *Microsoft Azure terv* típusú *Dev Team-előfizetés* nevű előfizetést. Az előfizetés számlázása a *contoso Pénzügy* számlázási profiljába történik, és a számla *fejlesztési* szakaszában jelenik meg. 

Végezze el a következő kérést, és cserélje le a `<invoiceSectionId>`t a második lépésből (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```) másolt `invoiceSectionId`. Át kell adnia a `billingProfileId`, és `skuId` az API kérés paramétereinek második lépése alapján. Ha meg szeretné adni a tulajdonosokat, ismerkedjen meg [a felhasználói objektumok azonosítóinak beolvasásával](grant-access-to-create-subscription.md#userObjectId).

```json
POST https://management.azure.com<invoiceSectionId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "billingProfileId": "<billingProfileId>",
  "skuId": "<skuId>",
  "owners": [
      {
        "objectId": "<userObjectId>"
      },
      {
        "objectId": "<servicePrincipalObjectId>"
      }
    ],
  "costCenter": "35683",
  "managementGroupId": "/providers/Microsoft.Management/managementGroups/xxxxxxx",",
}'

```

| Elem neve  | Kötelező | Típus   | Leírás                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Igen      | Sztring | Az előfizetés megjelenített neve.|
| `billingProfileId`   | Igen      | Sztring | Az előfizetés díjainak számlázására szolgáló számlázási profil azonosítója.  |
| `skuId` | Igen      | Sztring | Az Azure-csomag típusát meghatározó SKU-azonosító. |
| `owners`      | Nem       | Sztring | Bármely olyan felhasználói vagy egyszerű szolgáltatásnév objektumazonosító, amelyet a létrehozáskor RBAC-tulajdonosként szeretne hozzáadni az előfizetéshez.  |
| `costCenter` | Nem      | Sztring | Az előfizetéshez társított költséghely. Megjelenik a használati CSV-fájlban. |
| `managementGroupId` | Nem      | Sztring | Annak a felügyeleti csoportnak az azonosítója, amelyhez az előfizetés hozzá lesz adva. A felügyeleti csoportok listájának lekéréséhez lásd: [Management groups-List API](https://docs.microsoft.com/rest/api/resources/managementgroups/list). Használja a felügyeleti csoport AZONOSÍTÓját az API-ból. |

A válaszban egy `subscriptionCreationResult` objektumot kap a figyeléshez. Az előfizetés létrehozása után a `subscriptionCreationResult` objektum egy `subscriptionLink` objektumot ad vissza, amely az előfizetés-AZONOSÍTÓval rendelkezik.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>Előfizetés létrehozása MPA számlázási fiókhoz

### <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy előfizetést hozzon létre a számlázási fiókjához, globális rendszergazdai vagy rendszergazdai ügynök szerepkörrel kell rendelkeznie a szervezet felhőalapú megoldás-szolgáltatói fiókjában. További információ: [partner-központ – felhasználói szerepkörök és engedélyek kiosztása](https://docs.microsoft.com/partner-center/permissions-overview).

Az alábbi példa a REST API-k használatát mutatja be. A PowerShell és az Azure CLI jelenleg nem támogatottak.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>Azon számlázási fiókok megkeresése, amelyekhez hozzáférése van 

Az alábbi kérelem elvégzésével sorolja fel az összes olyan számlázási fiókot, amelyhez hozzáféréssel rendelkezik.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
Az API-válasz felsorolja a számlázási fiókokat.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftPartnerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
A `displayName` tulajdonsággal azonosíthatja azt a számlázási fiókot, amelyhez előfizetéseket kíván létrehozni. Győződjön meg arról, hogy a fiók agreeementType *MicrosoftPartnerAgreement*. Másolja a fiókhoz tartozó `name`. Ha például előfizetést szeretne létrehozni az `Contoso` számlázási fiókhoz, akkor másolja `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Illessze be valahova ezt az értéket, hogy a következő lépésben használni tudja.

### <a name="find-customers-that-have-azure-plans"></a>Azure-csomagokkal rendelkező ügyfelek keresése

Végezze el a következő kérelmet, és cserélje le a `<billingAccountName>`t az első lépésből (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```) másolt `name` a számlázási fiókban lévő összes ügyfél listázásához, amelyhez Azure-előfizetéseket hozhat létre. 

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
Az API-válasz felsorolja a számlázási fiókban lévő ügyfeleket az Azure-csomagokkal. Ezekhez az ügyfelekhez előfizetéseket is létrehozhat.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso USD",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam sports",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam bakery"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }]
}
    
```

A `displayName` tulajdonsággal azonosíthatja azt az ügyfelet, amelyhez előfizetéseket kíván létrehozni. Másolja az ügyfél `id`. Ha például `Fabrikam toys`-előfizetést szeretne létrehozni, akkor másolja `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. Illessze be ezt az értéket valahova a következő lépésekben való használathoz.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>Nem kötelező a közvetett szolgáltatók esetében: ügyfelek viszonteladóinak beszerzése

Ha Ön közvetett szolgáltató a CSP kétrétegű modellben, megadhat egy viszonteladót, miközben előfizetéseket hoz létre az ügyfelek számára. 

Végezze el a következő kérést, és cserélje le a `<customerId>`t a második lépésből (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) másolt `id` az ügyfelek számára elérhető összes viszonteladó listázásához.

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
Az API-válasz felsorolja az ügyfél viszonteladóit:

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
},
{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "5xxxxx",
        "description": "Tailspin"
      }
    ]
  }
}]
}
```
Az `description` tulajdonsággal azonosíthatja a viszonteladót, aki társítva lesz az előfizetéshez. Másolja a viszonteladó `resellerId`. Ha például `Wingtip`szeretne hozzárendelni, akkor másolja `3xxxxx`. Illessze be valahova ezt az értéket, hogy a következő lépésben használni tudja.

### <a name="create-a-subscription-for-a-customer"></a>Előfizetés létrehozása az ügyfél számára

A következő példában létrehozunk egy *fejlesztői csapat előfizetése* *Fabrikam-játékok* számára nevű előfizetést, és *Wingtip* -viszonteladót rendel hozzá az előfizetéshez. T

Hajtsa végre a következő kérést, amelyben cserélje le a `<customerId>` értéket az előző lépésben kimásolt `id` értékével (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Adja át a választható *resellerId* az API kérés paramétereinek második lépése alapján. 

```json
POST https://management.azure.com<customerId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "skuId": "0001",
  "resellerId": "<resellerId>",
}'
```

| Elem neve  | Kötelező | Típus   | Leírás                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Igen      | Sztring | Az előfizetés megjelenített neve.|
| `skuId` | Igen      | Sztring | Az Azure-csomag SKU-azonosítója. *0,001* használata Microsoft Azure csomag típusú előfizetésekhez |
| `resellerId`      | Nem       | Sztring | Az előfizetéshez hozzárendelni kívánt viszonteladó MPN-azonosítója.  |

A válaszban egy `subscriptionCreationResult` objektumot kap a figyeléshez. Az előfizetés létrehozása után a `subscriptionCreationResult` objektum egy `subscriptionLink` objektumot ad vissza, amely az előfizetés-AZONOSÍTÓval rendelkezik.

## <a name="next-steps"></a>További lépések

* Ha például egy Nagyvállalati Szerződés (EA) előfizetést .NET használatával kíván létrehozni, tekintse [meg a mintakód a githubon](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core)című témakört.
* Most, hogy létrehozott egy előfizetést, megadhatja ezt a lehetőséget más felhasználóknak és egyszerű szolgáltatásoknak. További információ: [hozzáférés biztosítása Azure Enterprise-előfizetések létrehozásához (előzetes verzió)](grant-access-to-create-subscription.md).
* Ha többet szeretne megtudni a nagy számú előfizetés felügyeleti csoportokkal való kezelésével kapcsolatban, tekintse meg [az erőforrások rendszerezése az Azure felügyeleti csoportok](management-groups-overview.md) segítségével című témakört.
