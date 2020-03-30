---
title: Programozott módon hozzon létre Azure-előfizetéseket
description: Ismerje meg, hogyan hozhat létre további Azure-előfizetéseket programozott módon.
author: amberbhargava
ms.topic: conceptual
ms.date: 03/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.openlocfilehash: 33f1d154f438b917b79cd299e81c9078e2f2e81d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460398"
---
# <a name="programmatically-create-azure-subscriptions-preview"></a>Programozott módon hozzon létre Azure-előfizetéseket (előzetes verzió)

A [nagyvállalati szerződéssel (EA),](https://azure.microsoft.com/pricing/enterprise-agreement/) [a Microsoft ügyfélszerződéssel (MCA)](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) vagy [a Microsoft Partner Szerződés (MPA)](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) számlázási fiókkal rendelkező Azure-ügyfelek programozott módon hozhatnak létre előfizetéseket. Ebben a cikkben megtudhatja, hogyan hozhat létre előfizetéseket programozott módon az Azure Resource Manager használatával.

Amikor programozott módon hoz létre egy Azure-előfizetést, az adott előfizetést az a szerződés szabályozza, amely alapján a Microsofttól vagy egy hivatalos viszonteladótól szerezte be az Azure-szolgáltatásokat. További információt a [Microsoft Azure jogi adatai című témakörben talál.](https://azure.microsoft.com/support/legal/)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


## <a name="create-subscriptions-for-an-ea-billing-account"></a>Előfizetések létrehozása Nagy- és Nagy- és Nagy- és Nagy-A számlázási fiókhoz

Az alábbi szakaszokban található információk alapján eA-előfizetéseket hozhat létre.

### <a name="prerequisites"></a>Előfeltételek

Előfizetés létrehozásához tulajdonosi szerepkörrel kell rendelkeznie egy regisztrációs fiókhoz. A szerepkör kétféleképpen szerezhető be:

* A regisztráció vállalati rendszergazdája [fióktulajdonossá teheti Önt](https://ea.azure.com/helpdocs/addNewAccount) (kötelező bejelentkezés), ami a regisztrációs fiók tulajdonosává teszi.

* A beléptetési fiók meglévő tulajdonosa [hozzáférést adhat Önnek.](grant-access-to-create-subscription.md) Hasonlóképpen, ha egy egyszerű szolgáltatás használatával szeretne létrehozni egy Nagy- és Szolgáltatás-előfizetést, [biztosítania kell, hogy az egyszerű szolgáltatás képes legyen előfizetések létrehozására.](grant-access-to-create-subscription.md)

### <a name="find-accounts-you-have-access-to"></a>Olyan fiókok keresése, amelyekhez hozzáférése van

Miután hozzáadta a fióktulajdonoshoz társított regisztrációs fiókhoz, az Azure a fiók-beléptetési kapcsolat segítségével határozza meg, hogy hol kell kiszámlázniaz előfizetési díjakat. A fiók alatt létrehozott összes előfizetésszámlázás a fiók által meglátogatott nagyvállalati szerződési szolgáltatásnak kerül kiszámlázására. Előfizetések létrehozásához meg kell adnia a regisztrációs fiók és a felhasználói egyszerű az előfizetés hez megadott értékeket.

A következő parancsok futtatásához be kell jelentkeznie a fióktulajdonos *kezdőkönyvtárába*, amely az előfizetések alapértelmezés szerint létrehozott könyvtára.

### <a name="rest"></a>[Többi](#tab/rest)

Az összes olyan regisztrációs fiók listázásának kérése, amelyhez hozzáférése van:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Az API-válasz felsorolja az összes olyan regisztrációs fiókot, amelyhez hozzáférése van:

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

A `principalName` tulajdonság segítségével azonosíthatja azt a fiókot, amelynek az előfizetések számlázását el szeretné számolni. Másolja `name` a fiók. Ha például előfizetéseket szeretne létrehozni SignUpEngineering@contoso.com a regisztrációs fiók alatt, másolja a programot. ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` Ez az azonosító a regisztrációs fiók objektumazonosítója. Illessze be ezt az értéket valahova, `enrollmentAccountObjectId`hogy a következő lépésben használhassa .

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Nyissa meg [az Azure Cloud Shellt,](https://shell.azure.com/) és válassza a PowerShell lehetőséget.

A [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) parancsmag segítségével sorolja fel az összes olyan regisztrációs fiókot, amelyhez hozzáférése van.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Az Azure a regisztrációs fiókok listájával válaszol, amelyekhez hozzáférése van:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
A `principalName` tulajdonság segítségével azonosíthatja azt a fiókot, amelynek az előfizetések számlázását el szeretné számolni. Másolja `ObjectId` a fiók. Ha például előfizetéseket szeretne létrehozni SignUpEngineering@contoso.com a regisztrációs fiók alatt, másolja a programot. ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` Illessze be az objektumazonosítót valahova, hogy a `enrollmentAccountObjectId`következő lépésben a .

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az [az számlázási regisztrációs fiók lista](https://aka.ms/EASubCreationPublicPreviewCLI) paranccsal listázhatja az összes olyan regisztrációs fiókot, amelyhez hozzáférése van.

```azurecli-interactive
az billing enrollment-account list
```

Az Azure a regisztrációs fiókok listájával válaszol, amelyekhez hozzáférése van:

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

A `principalName` tulajdonság segítségével azonosíthatja azt a fiókot, amelynek az előfizetések számlázását el szeretné számolni. Másolja `name` a fiók. Ha például előfizetéseket szeretne létrehozni SignUpEngineering@contoso.com a regisztrációs fiók alatt, másolja a programot. ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` Ez az azonosító a regisztrációs fiók objektumazonosítója. Illessze be ezt az értéket valahova, `enrollmentAccountObjectId`hogy a következő lépésben használhassa .

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Előfizetések létrehozása egy adott regisztrációs fiók alatt

A következő példa létrehoz egy *fejlesztői csapatelőfizetés* nevű előfizetést az előző lépésben kiválasztott regisztrációs fiókban. Az előfizetési ajánlat AZ *MS-AZR-0017P* (rendszeres Microsoft Nagyvállalati Szerződés). Azt is opcionálisan két felhasználót ad hozzá RBAC tulajdonosok az előfizetéshez.

### <a name="rest"></a>[Többi](#tab/rest)

Hajtsa végre a következő kérést, amelyben cserélje le a `<enrollmentAccountObjectId>` értéket az előző lépésben kimásolt `name` értékével (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Ha tulajdonosokat szeretne megadni, olvassa el, [hogyan szerezheti be a felhasználói objektumazonosítókat.](grant-access-to-create-subscription.md#userObjectId)

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
| `displayName` | Nem      | Sztring | Az előfizetés megjelenítendő neve. Ha nincs megadva, akkor az ajánlat neve, például a "Microsoft Azure Enterprise" lesz beállítva.                                 |
| `offerType`   | Igen      | Sztring | Az ajánlat az előfizetés. Az EA két lehetősége az [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (termelési felhasználás) és [az MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (fejlesztési/tesztelési, az EA portál használatával kell [bekapcsolni).](https://ea.azure.com/helpdocs/DevOrTestOffer)                |
| `owners`      | Nem       | Sztring | Bármely felhasználó objektumazonosítója, amelyet RBAC-tulajdonosként szeretne hozzáadni az előfizetéshez, amikor létrehozza.  |

A válaszban visszakap `subscriptionOperation` egy objektumot a figyeléshez. Amikor az előfizetés létrehozása `subscriptionOperation` befejeződött, `subscriptionLink` az objektum egy objektumot ad vissza, amely rendelkezik az előfizetés-azonosítóval.

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Először telepítse ezt az `Install-Module Az.Subscription -AllowPrerelease`előnézeti modult a futtatásával. A biztos `-AllowPrerelease` működés érdekében telepítse a PowerShellGet a [Get PowerShellGet modulból](/powershell/scripting/gallery/installing-psget)legújabb verzióját.

Futtassa az alábbi [New-AzSubscription](/powershell/module/az.subscription) parancsot, és cserélje le `<enrollmentAccountObjectId>` az `ObjectId` első lépésben (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Ha tulajdonosokat szeretne megadni, olvassa el, [hogyan szerezheti be a felhasználói objektumazonosítókat.](grant-access-to-create-subscription.md#userObjectId)

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Elem neve  | Kötelező | Típus   | Leírás                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Nem      | Sztring | Az előfizetés megjelenítendő neve. Ha nincs megadva, akkor az ajánlat neve, például a "Microsoft Azure Enterprise" lesz beállítva.                                 |
| `OfferType`   | Igen      | Sztring | Az ajánlat az előfizetés. Az EA két lehetősége az [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (termelési felhasználás) és [az MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (fejlesztési/tesztelési, az EA portál használatával kell [bekapcsolni).](https://ea.azure.com/helpdocs/DevOrTestOffer)                |
| `EnrollmentAccountObjectId`      | Igen       | Sztring | Annak a regisztrációs fióknak az objektumazonosítója, amely alatt az előfizetés létrejön, és amelynek számlázása történik. Ez az érték egy GUID, hogy `Get-AzEnrollmentAccount`kapsz. |
| `OwnerObjectId`      | Nem       | Sztring | Bármely felhasználó objektumazonosítója, amelyet RBAC-tulajdonosként szeretne hozzáadni az előfizetéshez, amikor létrehozza.  |
| `OwnerSignInName`    | Nem       | Sztring | Bármely felhasználó e-mail címe, amelyet rBAC-tulajdonosként szeretne hozzáadni az előfizetéshez, amikor létrehozza. Ezt a paramétert `OwnerObjectId`használhatja a helyett.|
| `OwnerApplicationId` | Nem       | Sztring | Az alkalmazás azonosítója minden olyan egyszerű szolgáltatás, amely szeretné hozzáadni, mint egy RBAC-tulajdonos az előfizetésben, amikor létrehozása. Ezt a paramétert `OwnerObjectId`használhatja a helyett. A paraméter használatakor a szolgáltatásnévnek [olvasási hozzáféréssel kell rendelkeznie a címtárhoz.](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole)|

Az összes paraméter teljes listájának megtekintéséhez olvassa el a [New-AzSubscription](/powershell/module/az.subscription).

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Először telepítse ezt az `az extension add --name subscription`előnézeti bővítményt a futtatásával.

Futtassa az [az fiók létrehozása](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) parancsot az alábbi, helyett `<enrollmentAccountObjectId>` a `name` másolt az első lépésben (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Ha tulajdonosokat szeretne megadni, olvassa el, [hogyan szerezheti be a felhasználói objektumazonosítókat.](grant-access-to-create-subscription.md#userObjectId)

```azurecli-interactive
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Elem neve  | Kötelező | Típus   | Leírás                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Nem      | Sztring | Az előfizetés megjelenítendő neve. Ha nincs megadva, akkor az ajánlat neve, például a "Microsoft Azure Enterprise" lesz beállítva.                                 |
| `offer-type`   | Igen      | Sztring | Az ajánlat az előfizetés. Az EA két lehetősége az [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (termelési felhasználás) és [az MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (fejlesztési/tesztelési, az EA portál használatával kell [bekapcsolni).](https://ea.azure.com/helpdocs/DevOrTestOffer)                |
| `enrollment-account-object-id`      | Igen       | Sztring | Annak a regisztrációs fióknak az objektumazonosítója, amely alatt az előfizetés létrejön, és amelynek számlázása történik. Ez az érték egy GUID, hogy `az billing enrollment-account list`kapsz. |
| `owner-object-id`      | Nem       | Sztring | Bármely felhasználó objektumazonosítója, amelyet RBAC-tulajdonosként szeretne hozzáadni az előfizetéshez, amikor létrehozza.  |
| `owner-upn`    | Nem       | Sztring | Bármely felhasználó e-mail címe, amelyet rBAC-tulajdonosként szeretne hozzáadni az előfizetéshez, amikor létrehozza. Ezt a paramétert `owner-object-id`használhatja a helyett.|
| `owner-spn` | Nem       | Sztring | Az alkalmazás azonosítója minden olyan egyszerű szolgáltatás, amely szeretné hozzáadni, mint egy RBAC-tulajdonos az előfizetésben, amikor létrehozása. Ezt a paramétert `owner-object-id`használhatja a helyett. A paraméter használatakor a szolgáltatásnévnek [olvasási hozzáféréssel kell rendelkeznie a címtárhoz.](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole)|

Az összes paraméter teljes listájának megtekintéséhez olvassa el az az fiók létrehozása című [témakört.](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create)

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Az Azure Enterprise előfizetés-létrehozási API korlátai

- Ezzel az API-val csak Azure Enterprise-előfizetések hozhatók létre.
- Regisztrációs fiókonként legfeljebb 500 előfizetés lehet. Ezt követően a fióktovábbi előfizetések csak az Azure Portalon hozhatók létre. Ha további előfizetéseket szeretne létrehozni az API-n keresztül, hozzon létre egy másik regisztrációs fiókot.
- Azok a felhasználók, akik nem fióktulajdonosok, de rBAC-on keresztül adtak hozzá egy regisztrációs fiókhoz, nem hozhatnak létre előfizetéseket az Azure Portalon.
- Nem választhatja ki a bérlőt az előfizetés létrehozásához. Az előfizetés mindig a fióktulajdonos otthoni bérlőjében jön létre. Ha át szeretné helyezni az előfizetést egy másik bérlőre, olvassa el az [előfizetés bérlőjének módosítása](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)című témakört.


## <a name="create-subscriptions-for-an-mca-account"></a>McA-fiók előfizetésének létrehozása

### <a name="prerequisites"></a>Előfeltételek

Az előfizetések létrehozásához tulajdonosi, közreműködői vagy Azure-előfizetés-létrehozószerepkörrel kell rendelkeznie egy számlaszakaszon, illetve tulajdonosi vagy közreműködői szerepkörrel egy számlázási profilon vagy számlázási fiókban. További információkért lásd [az előfizetés számlázási szerepköreit és azok feladatait](../../cost-management-billing/manage/understand-mca-roles.md#subscription-billing-roles-and-tasks).

Az alábbi példa REST API-kat használ. A PowerShell és az Azure CLI jelenleg nem támogatottak.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Olyan számlázási fiókok megkeresése, amelyekhez hozzáférése van

Az alábbi kérelem az összes számlázási fiók listázására.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
Az API-válasz felsorolja azokat a számlázási fiókokat, amelyekhez hozzáférése van.

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
A `displayName` tulajdonság segítségével azonosíthatja azt a számlázási fiókot, amelyhez előfizetéseket szeretne létrehozni. Győződjön meg arról, hogy a fiók agreeementType típusa *MicrosoftCustomerAgreement*. Másolja `name` a fiók másolatát.  Ha például előfizetést szeretne létrehozni `Contoso` a számlázási fiókhoz, másolja a programot. `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` Illessze be valahova ezt az értéket, hogy a következő lépésben használni tudja.

### <a name="find-invoice-sections-to-create-subscriptions"></a>Számlaszakaszok keresése előfizetések létrehozásához

Az előfizetés díjai a számlázási profil számlájának egy szakaszában jelennek meg. A következő API-val lekaphatja a számlaszakaszok és számlázási profilok listáját, amelyeken azure-előfizetések létrehozására jogosult.

Hajtsa végre a következő kérést, amelyben cserélje le a `<billingAccountName>` értéket az előző lépésben kimásolt `name` értékével (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```).

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```
Az API-válasz felsorolja az összes számlaszakaszt és azok számlázási profiljait, amelyekhez előfizetéseket hozhat létre:

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

A `invoiceSectionDisplayName` tulajdonság segítségével azonosíthatja azt a számlaszakaszt, amelyhez előfizetéseket szeretne létrehozni. Másolja `invoiceSectionId`a `billingProfileId` t és `skuId` a számlaszakasz egyikét. Ha `Microsoft Azure plan` például számlaszakasz típusú `Development` előfizetést szeretne létrehozni, másolja `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX`a `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` , `0001`és a programot. Illessze be ezeket az értékeket valahova, hogy a következő lépésben használhassa őket.

### <a name="create-a-subscription-for-an-invoice-section"></a>Előfizetés létrehozása számlaszakaszhoz

A következő példa létrehoz egy *Fejlesztői csapat nevű előfizetést,* amely *microsoft Azure-csomag* típusú a *fejlesztési* számla szakaszhoz. Az előfizetés számlázása a *Contoso-finanszírozás* számlázási profiljának lesz számlázva, és megjelenik a számla *Fejlesztési* szakaszában.

Tegye meg a `<invoiceSectionId>` következő `invoiceSectionId` kérelmet, helyettesítve```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```a másolt a második lépés ( ). Át kell adnia `billingProfileId` a `skuId` és az API kérési paramétereinek második lépéséből másolnia kell. Ha tulajdonosokat szeretne megadni, olvassa el, [hogyan szerezheti be a felhasználói objektumazonosítókat.](grant-access-to-create-subscription.md#userObjectId)

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
| `displayName` | Igen      | Sztring | Az előfizetés megjelenítendő neve.|
| `billingProfileId`   | Igen      | Sztring | A számlázási profil azonosítója, amelyet az előfizetés díjaiért számlázunk.  |
| `skuId` | Igen      | Sztring | A termékváltozat-azonosító, amely meghatározza az Azure-csomag típusát. |
| `owners`      | Nem       | Sztring | Az objektumazonosító bármely felhasználó vagy szolgáltatásnév, amely szeretné hozzáadni, mint egy RBAC-tulajdonos az előfizetés, amikor létrehozása kor.  |
| `costCenter` | Nem      | Sztring | Az előfizetéshez társított költséghely. Megjelenik a használat csv fájlban. |
| `managementGroupId` | Nem      | Sztring | Annak a felügyeleti csoportnak az azonosítója, amelyhez az előfizetés hozzá lesz adva. A felügyeleti csoportok listájának beolvassa a [Felügyeleti csoportok – LISTA API című témakört.](/rest/api/resources/managementgroups/list) Használja az API-ból származó felügyeleti csoport azonosítóját. |

A válaszban visszakap `subscriptionCreationResult` egy objektumot a figyeléshez. Amikor az előfizetés létrehozása `subscriptionCreationResult` befejeződött, `subscriptionLink` az objektum egy objektumot ad vissza, amely rendelkezik az előfizetés-azonosítóval.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>Előfizetések létrehozása mpa-számlázási fiókhoz

### <a name="prerequisites"></a>Előfeltételek

A számlázási fiókhoz előfizetés létrehozásához globális rendszergazdai vagy rendszergazdai szerepkörrel kell rendelkeznie a szervezet felhőalapú szolgáltatófiókjában. További információt a [Partnerközpont – Felhasználói szerepkörök és engedélyek hozzárendelése](https://docs.microsoft.com/partner-center/permissions-overview)című témakörben talál.

Az alábbi példa REST API-kat használ. A PowerShell és az Azure CLI jelenleg nem támogatottak.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>Keresse meg azokkal a számlázási fiókokkal, amelyekhez hozzáférése van

Az alábbi kérelem vel sorolja fel az összes olyan számlázási fiókot, amelyhez hozzáférése van.

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
A `displayName` tulajdonság segítségével azonosíthatja azt a számlázási fiókot, amelyhez előfizetéseket szeretne létrehozni. Győződjön meg arról, hogy a fiók agreeementType típusa *MicrosoftPartnerAgreement*. Másolja `name` a fiókhoz. Ha például előfizetést szeretne létrehozni `Contoso` a számlázási fiókhoz, másolja a programot. `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` Illessze be valahova ezt az értéket, hogy a következő lépésben használni tudja.

### <a name="find-customers-that-have-azure-plans"></a>Azure-csomagokkal rendelkező ügyfelek keresése

Tegye meg a `<billingAccountName>` következő `name` kérést, és cserélje```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```le a másolt az első lépésből ( ) a számlázási fiókban lévő összes olyan ügyfél listázásához, akikszámára Azure-előfizetéseket hozhat létre.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
Az API-válasz felsorolja az ügyfelek a számlázási fiók ban az Azure-csomagok. Ezeket az ügyfeleket előfizetések hozhatják létre.

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

A `displayName` tulajdonság segítségével azonosíthatja azt a vevőt, amelyikhez előfizetéseket szeretne létrehozni. Másolja `id` a vevőnek. Ha például előfizetést szeretne létrehozni `Fabrikam toys`a számára, `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`másolja a programot. Illessze be ezt az értéket valahol használni a következő lépésekben.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>Közvetett szolgáltatók számára nem kötelező: Az ügyfél viszonteladóinak beszerezni

Ha Ön közvetett szolgáltató a csp kétrétegű modellben, megadhat egy viszonteladót, miközben előfizetéseket hoz létre az ügyfelek számára.

Tegye meg a `<customerId>` következő `id` kérést, és cserélje```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```le a második lépésből ( ) másoltra, hogy felsorolja az összes olyan viszonteladót, amely elérhető az ügyfél számára.

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
A `description` tulajdonság segítségével azonosíthatja az előfizetéshez társított viszonteladót. Másolja `resellerId` a viszonteladónak. Ha például társítani `Wingtip`szeretné, másolja `3xxxxx`a programot. Illessze be valahova ezt az értéket, hogy a következő lépésben használni tudja.

### <a name="create-a-subscription-for-a-customer"></a>Előfizetés létrehozása vevő számára

A következő példa létrehoz egy *Dev Team-előfizetés* nevű előfizetést a *Fabrikam-játékokhoz,* és *wingtip* viszonteladót társít az előfizetéshez. T

Tegye meg a `<customerId>` következő `id` kérelmet, helyettesítve```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```a másolt a második lépés ( ). Adja át az API kérelemparamétereinek második lépéséből másolt opcionális *viszonteladóId* át.

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
| `displayName` | Igen      | Sztring | Az előfizetés megjelenítendő neve.|
| `skuId` | Igen      | Sztring | Az Azure-csomag termékazonosítója. *A 0001* használata Microsoft Azure Plan típusú előfizetésekhez |
| `resellerId`      | Nem       | Sztring | Az előfizetéshez társított viszonteladó MPN-azonosítója.  |

A válaszban visszakap `subscriptionCreationResult` egy objektumot a figyeléshez. Amikor az előfizetés létrehozása `subscriptionCreationResult` befejeződött, `subscriptionLink` az objektum egy objektumot ad vissza, amely rendelkezik az előfizetés-azonosítóval.

## <a name="next-steps"></a>További lépések

* A nagyvállalati szerződés (EA) előfizetésének .NET használatával történő létrehozásáról például [a GitHub mintakódját](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core)című témakörben található.
* Most, hogy létrehozott egy előfizetést, ezt a lehetőséget más felhasználóknak és egyszerű szolgáltatástagoknak is megadhatja. További információ: [Hozzáférés megadása Azure Enterprise-előfizetések létrehozásához (előzetes verzió)](grant-access-to-create-subscription.md).
* Ha többet szeretne tudni arról, hogy hogyan kezelheti a nagyszámú előfizetést felügyeleti csoportok használatával, olvassa [el az Erőforrások rendszerezése az Azure felügyeleti csoportokkal](../../governance/management-groups/overview.md)
