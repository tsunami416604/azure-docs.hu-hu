---
title: Az Azure Enterprise-előfizetések létrehozása programozott módon |} A Microsoft Docs
description: Megtudhatja, hogyan további Azure Enterprise vagy a Enterprise Dev/Test-előfizetések létrehozása programozott módon.
services: azure-resource-manager
author: jureid
manager: jureid
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/10/2019
ms.author: jureid
ms.openlocfilehash: 7985451eb2bb5e9fd4fbcfb3d2fcf35149122c15
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65796067"
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Programozott módon létrehozhat az Azure Enterprise-előfizetések (előzetes verzió)

Az Azure ügyfeleként [nagyvállalati szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), és hozhat létre nagyvállalati szerződés (MS-AZR - 0017 P) nagyvállalati szerződés – fejlesztés és tesztelés (MS-AZR - 0148 P) előfizetések programozott módon. Ebből a cikkből megismerheti, hogyan hozhatnak létre előfizetéseket programozott módon az Azure Resource Manager használatával.

Az API-ból egy Azure-előfizetést hoz létre, ha ezt az előfizetést a szerződés, amely alatt a Microsofttól vagy egy meghatalmazott viszonteladói beszerzett Microsoft Azure-szolgáltatások szabályozzák. További tudnivalókért lásd: [a Microsoft Azure – jogi információk](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

A regisztrációs fiók-előfizetések létrehoz egy tulajdonosi szerepkör kell rendelkeznie. Ezek a szerepkörök beolvasása a két módja van:

* A regisztráció rendszergazdájához is [győződjön meg arról, hogy fióktulajdonos](https://ea.azure.com/helpdocs/addNewAccount) (bejelentkezés szükséges) ami lehetővé teszi, a regisztrációs fiók tulajdonosának. Kövesse az utasításokat a meghívó e-mailben kapott manuálisan hozzon létre egy kezdeti előfizetést. Erősítse meg a fiók tulajdonjogát, és manuálisan hozzon létre egy kezdeti, mielőtt továbblép a következő lépés a nagyvállalati szerződéses előfizetésében. Csak hozzáadása a regisztrációt a fiók nem elég.

* A regisztrációs fiók meglévő tulajdonosává is [hozzáférési](grant-access-to-create-subscription.md). Hasonlóképpen, ha azt szeretné, a nagyvállalati szerződéses előfizetésében létrehozni az egyszerű szolgáltatás használatával, meg kell [megadása egyszerű szolgáltatást az előfizetések létrehozása](grant-access-to-create-subscription.md).

## <a name="find-accounts-you-have-access-to"></a>Keresse meg a fiók rendelkezik hozzáféréssel

-Fiók tulajdonosai Azure EA-regisztrációhoz hozzáadják, miután az Azure a fiók regisztrációjának kapcsolat segítségével határozza meg, hol számlázási, előfizetési díjai. A fiók alatt létrehozott összes előfizetés számlázása a regisztrált Nagyvállalati szerződéshez, amely a fiók felé. Előfizetések létrehozása, akkor meg kell adnia értékeket a regisztrációs fiók és a felhasználó rendszerbiztonsági tagok, az előfizetés tulajdonosa. 

Az alábbi parancsok futtatásához, meg kell bejelentkeznie a fiók tulajdonosának *kezdőkönyvtár*, azaz a címtár előfizetések alapértelmezés szerint létrehozott.

## <a name="resttabrest"></a>[REST](#tab/rest)

A kérelem hozzáfér az összes regisztrációs fiókok listáját:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Azure válaszol az hozzáfér az összes regisztrációs fiókok listáját:

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

Használja a `principalName` azonosítani a fiókot, amelyet az előfizetések kiszámlázható tulajdonságot. Másolás a `name` -fiók. Például, ha szeretne az előfizetések létrehozására a SignUpEngineering@contoso.com eszközregisztráció-fiókot, akkor másolja ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Ez a regisztrációs fiók az objektum Azonosítóját. Illessze be ezt az értéket, valahol, hogy használhatja azt, a következő lépésben `enrollmentAccountObjectId`.

## <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Nyissa meg [Azure Cloud Shell](https://shell.azure.com/) , és válassza ki a PowerShell.

Használja a [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) parancsmag használatával listázhatja az összes regisztrációs fiókok hozzáfér.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure fűzi hozzá van hozzáférése a regisztrációs fiókok listáját:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
Használja a `principalName` azonosítani a fiókot, amelyet az előfizetések kiszámlázható tulajdonságot. Másolás a `ObjectId` -fiók. Például, ha szeretne az előfizetések létrehozására a SignUpEngineering@contoso.com eszközregisztráció-fiókot, akkor másolja ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Illessze be az Objektumazonosító valahol, hogy a következő lépésben, használhatja a `enrollmentAccountObjectId`.

## <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Használja a [az számlázási regisztrációs-fióklista](https://aka.ms/EASubCreationPublicPreviewCLI) paranccsal listát készíthet az összes regisztrációs fiókok hozzáfér.

```azurecli-interactive 
az billing enrollment-account list
```

Azure fűzi hozzá van hozzáférése a regisztrációs fiókok listáját:

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

Használja a `principalName` azonosítani a fiókot, amelyet az előfizetések kiszámlázható tulajdonságot. Másolás a `name` -fiók. Például, ha szeretne az előfizetések létrehozására a SignUpEngineering@contoso.com eszközregisztráció-fiókot, akkor másolja ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Ez a regisztrációs fiók az objektum Azonosítóját. Illessze be ezt az értéket, valahol, hogy használhatja azt, a következő lépésben `enrollmentAccountObjectId`.

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>A regisztrációs fiók-előfizetések létrehozására

Az alábbi példa egy nevű előfizetést hoz létre *fejlesztői csapat előfizetés* az előző lépésben kiválasztott regisztrációs-fiókban. Az előfizetési ajánlat *MS-AZR - 0017P* (rendszeres Microsoft nagyvállalati szerződés keretében). Azt is két felhasználót ad RBAC tulajdonosként az előfizetés.

# <a name="resttabrest"></a>[REST](#tab/rest)

Győződjön meg arról, a következő kérelem, és cserélje le `<enrollmentAccountObjectId>` együtt a `name` az első lépésben másolt (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Ha szeretné, tulajdonosok, ismerje meg, [beszerzése a felhasználói objektum azonosítók](grant-access-to-create-subscription.md#userObjectId).

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

| Elem neve  | Szükséges | Típus   | Leírás                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Nem      | String | Az előfizetés megjelenített neve. Ha nincs megadva, van-e állítva a név az ajánlat, mint például a "Microsoft Azure Enterprise."                                 |
| `offerType`   | Igen      | String | Az ajánlat az előfizetés. A nagyvállalati szerződéssel rendelkező két lehetőség áll rendelkezésre [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (éles környezetben való használathoz) és [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (fejlesztés és tesztelés, kell lennie [van kapcsolva a nagyvállalati szerződések portáljának használatával](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Nem       | String | Azok a felhasználók, amelyet szeretne az előfizetés tulajdonosaként RBAC hozzáadása létrehozáskor, objektum azonosítója.  |

A válaszban szerezheti vissza egy `subscriptionOperation` figyelés objektumot. Ha az előfizetés létrehozása befejeződött, a `subscriptionOperation` objektum esetén ad vissza egy `subscriptionLink` objektum, amely rendelkezik az előfizetés azonosítóját.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Először telepítse a modul előzetes verziójának futtatásával `Install-Module Az.Subscription -AllowPrerelease`. Győződjön meg arról, hogy `-AllowPrerelease` működik, a PowerShellGet legújabb verziójának telepítése [a PowerShellGet modul beolvasása](/powershell/gallery/installing-psget).

Futtassa a [New-AzSubscription](/powershell/module/az.subscription) parancs alatt, és cserélje le `<enrollmentAccountObjectId>` együtt a `ObjectId` gyűjti az első lépésben (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Ha szeretné, tulajdonosok, ismerje meg, [beszerzése a felhasználói objektum azonosítók](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Elem neve  | Szükséges | Típus   | Leírás                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Nem      | String | Az előfizetés megjelenített neve. Ha nincs megadva, van-e állítva a név az ajánlat, mint például a "Microsoft Azure Enterprise."                                 |
| `OfferType`   | Igen      | String | Az ajánlat az előfizetés. A nagyvállalati szerződéssel rendelkező két lehetőség áll rendelkezésre [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (éles környezetben való használathoz) és [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (fejlesztés és tesztelés, kell lennie [van kapcsolva a nagyvállalati szerződések portáljának használatával](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Igen       | String | Az eszközregisztráció-fiókot, hogy az előfizetés alatt létrehozott, és díjat pedig objektum azonosítója. Az értéket nem egy GUID Azonosítót a, a `Get-AzEnrollmentAccount`. |
| `OwnerObjectId`      | Nem       | String | Azok a felhasználók, amelyet szeretne az előfizetés tulajdonosaként RBAC hozzáadása létrehozáskor, objektum azonosítója.  |
| `OwnerSignInName`    | Nem       | String | Azok a felhasználók, amelyet szeretne az előfizetés tulajdonosaként RBAC hozzáadása létrehozáskor, e-mail-címét. Ez a paraméter helyett használható `OwnerObjectId`.|
| `OwnerApplicationId` | Nem       | String | Bármely, amelyet szeretne az előfizetés tulajdonosaként RBAC hozzáadása létrehozáskor egyszerű szolgáltatás Alkalmazásazonosítója. Ez a paraméter helyett használható `OwnerObjectId`. Ez a paraméter használatakor az rendelkeznie kell az egyszerű szolgáltatás [olvasási hozzáférés a címtárhoz](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Összes paraméter teljes listáját lásd: [New-AzSubscription](/powershell/module/az.subscription.preview).

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Először telepítse a előzetes bővítmény futtatásával `az extension add --name subscription`.

Futtassa a [az fiók létrehozása](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) parancs alatt, és cserélje le `<enrollmentAccountObjectId>` együtt a `name` az első lépésben kimásolt (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Ha szeretné, tulajdonosok, ismerje meg, [beszerzése a felhasználói objektum azonosítók](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Elem neve  | Szükséges | Típus   | Leírás                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Nem      | String | Az előfizetés megjelenített neve. Ha nincs megadva, van-e állítva a név az ajánlat, mint például a "Microsoft Azure Enterprise."                                 |
| `offer-type`   | Igen      | String | Az ajánlat az előfizetés. A nagyvállalati szerződéssel rendelkező két lehetőség áll rendelkezésre [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (éles környezetben való használathoz) és [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (fejlesztés és tesztelés, kell lennie [van kapcsolva a nagyvállalati szerződések portáljának használatával](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Igen       | String | Az eszközregisztráció-fiókot, hogy az előfizetés alatt létrehozott, és díjat pedig objektum azonosítója. Az értéket nem egy GUID Azonosítót a, a `az billing enrollment-account list`. |
| `owner-object-id`      | Nem       | String | Azok a felhasználók, amelyet szeretne az előfizetés tulajdonosaként RBAC hozzáadása létrehozáskor, objektum azonosítója.  |
| `owner-upn`    | Nem       | String | Azok a felhasználók, amelyet szeretne az előfizetés tulajdonosaként RBAC hozzáadása létrehozáskor, e-mail-címét. Ez a paraméter helyett használható `owner-object-id`.|
| `owner-spn` | Nem       | String | Bármely, amelyet szeretne az előfizetés tulajdonosaként RBAC hozzáadása létrehozáskor egyszerű szolgáltatás Alkalmazásazonosítója. Ez a paraméter helyett használható `owner-object-id`. Ez a paraméter használatakor az rendelkeznie kell az egyszerű szolgáltatás [olvasási hozzáférés a címtárhoz](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Összes paraméter teljes listáját lásd: [az fiók létrehozása](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

----

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Az Azure Enterprise-előfizetés létrehozása API korlátozásai

- Csak az Azure Enterprise-előfizetések az API használatával is létrehozható.
- Egy kezdeti legfeljebb 50 előfizetéssel regisztrációs fiókonként, de Ön is [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) 200 korlát növelését. Ezt követően előfizetéseket csak az Account Center webhelyen keresztül lehet létrehozni.
- Szükség van legalább egy nagyvállalati szerződés vagy nagyvállalati szerződéssel rendelkező fejlesztési és tesztelési előfizetések a fiókban, ami azt jelenti, hogy a fióktulajdonos legalább egyszer keresztülment manuális-előfizetés.
- Felhasználók, akik nem tulajdonosai, de egy, az RBAC-n keresztül regisztrációs fiókhoz hozzáadott előfizetések Account Center használatával nem hozható létre.
- Nem választhat ki a bérlő az előfizetés kell létrehozni. A fiók tulajdonosának saját bérlőjén mindig létrejön az előfizetés. Az előfizetés áthelyezése egy másik bérlőhöz: [előfizetés-bérlő módosítása](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="next-steps"></a>További lépések

* A .NET használatával előfizetések létrehozása egy példa: [mintát a githubon](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Most, hogy létrehozott egy előfizetést, meg lehet adni, hogy lehetősége más felhasználók és az egyszerű szolgáltatások. További információkért lásd: [hozzáférést (előzetes verzió) az Azure Enterprise-előfizetések létrehozása](grant-access-to-create-subscription.md).
* Nagyszámú felügyeleti csoportok használatával előfizetések kezelésével kapcsolatos további tudnivalókért lásd: [az erőforrások rendszerezéséhez az Azure felügyeleti csoportok](management-groups-overview.md)
