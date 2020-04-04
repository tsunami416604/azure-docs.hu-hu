---
title: Engedély megadása az Azure-kulcstartóelérésére vonatkozó alkalmazásoknak – Azure Key Vault | Microsoft dokumentumok
description: Megtudhatja, hogy miként adhat engedélyt számos alkalmazásnak a kulcstartó eléréséhez
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/27/2019
ms.author: mbaldwin
ms.openlocfilehash: a3be46f2b50afbe2347445a8b3220c74d1bfc52c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656987"
---
# <a name="provide-key-vault-authentication-with-an-access-control-policy"></a>A Kulcstároló hitelesítésének biztosítása hozzáférés-vezérlési házirenddel

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A felhőalapú alkalmazások Key Vaultba történő hitelesítésének legegyszerűbb módja felügyelt identitással történik; A részleteket az Azure Key Vault eléréséhez az [App Service felügyelt identitásának használata.](managed-identity.md)  Ha egy helyszíni alkalmazás, helyi fejlesztés, vagy más módon nem tudja használni a felügyelt identitást, ehelyett manuálisan regisztrálhat egy egyszerű szolgáltatás, és hozzáférést biztosít a key vault egy hozzáférés-vezérlési szabályzat használatával.  

A key vault legfeljebb 1024 hozzáférési szabályzat-bejegyzéseket támogat, és minden egyes bejegyzés külön engedélyeket ad egy "egyszerű" számára: Például az [Azure Key Vault-ügyfélrendszerében a .NET gyorsindításhoz](quick-create-net.md) való hozzáférés e módszerrel.

A Key Vault hozzáférés-vezérlésével kapcsolatos részletekért tekintse meg [az Azure Key Vault biztonsága: Identitás- és hozzáférés-kezelés.](overview-security.md#identity-and-access-management) A [kulcsok, a titkos kulcsok és a tanúsítványok](about-keys-secrets-and-certificates.md) hozzáférés-vezérléssel kapcsolatos részletek a következő témakörökben érhetők el: 

- [Kulcsok hozzáférés-vezérlése](about-keys-secrets-and-certificates.md#key-access-control)
- [Titkos kulcsok hozzáférés-vezérlése](about-keys-secrets-and-certificates.md#secret-access-control)
- [Tanúsítvány-hozzáférés-vezérlés](about-keys-secrets-and-certificates.md#certificate-access-control)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

- Egy kulcstartó. Használhatja a meglévő kulcstartót, vagy hozzon létre egy újat az alábbi rövid útmutatók egyikének lépéseit követve:
   - [Hozzon létre egy kulcstartót az Azure CLI-vel](quick-create-cli.md)
   - [Kulcstartó létrehozása az Azure PowerShell használatával](quick-create-powershell.md)
   - [Hozzon létre egy kulcstartót az Azure Portalon.](quick-create-portal.md)
- Az [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) vagy [az Azure PowerShell.](/powershell/azure/overview) Másik lehetőségként használhatja az [Azure Portalt.](https://portal.azure.com)

## <a name="grant-access-to-your-key-vault"></a>Hozzáférés a kulcstartóhoz

Minden kulcstartó hozzáférési házirend-bejegyzés külön engedélyeket ad egy egyszerű felhasználónak:

- **Egy alkalmazás** Ha az alkalmazás felhőalapú, ehelyett felügyelt identitás használatával fér [hozzá az Azure Key Vaulthoz,](managed-identity.md)ha lehetséges.
- **Egy Azure AD-csoport** Bár a key vault csak támogatja a 1024-es hozzáférési szabályzat bejegyzéseket, több alkalmazást és felhasználókat adhat hozzá egyetlen Azure AD-csoporthoz, majd adja hozzá, hogy a csoport egyetlen bejegyzésként a hozzáférés-vezérlési szabályzat.
- **Felhasználó** A kulcstartóhoz való közvetlen hozzáférés nem **ajánlott**a felhasználóknak. Ideális esetben a felhasználók hozzá kell adni egy Azure AD-csoporthoz, amely viszont hozzáférést biztosít a key vaulthoz. Lásd: [Az Azure Key Vault biztonsága: Identitás- és hozzáférés-kezelés.](overview-security.md#identity-and-access-management)


### <a name="get-the-objectid"></a>Az objektumazonosító beszereznie

Ahhoz, hogy egy alkalmazás, az Azure AD-csoport vagy a felhasználó hozzáférést a key vault, először be kell szereznie az objectId.

#### <a name="applications"></a>Alkalmazások

Egy alkalmazás objectId egyalkalmazás megfelel a kapcsolódó egyszerű szolgáltatás. A szolgáltatástagokra vonatkozó részletekért. lásd: [Alkalmazás- és szolgáltatásegyszerű objektumok az Azure Active Directoryban.](../active-directory/develop/app-objects-and-service-principals.md) 

Egy alkalmazás objectId-jának beszerzése kétféleképpen szerezhető be.  Az első az, hogy regisztrálja az alkalmazást az Azure Active Directoryval. Ehhez kövesse az [alkalmazás regisztrálása a Microsoft identity platformmal](../active-directory/develop/quickstart-register-app.md)című rövid útmutató lépéseit. A regisztráció befejeztével az objektumazonosító "Alkalmazás (ügyfél) azonosítóként" jelenik meg.

A második az egyszerű szolgáltatás létrehozása egy terminálablakban. Az Azure CLI használatával használja az [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) parancsot.

```azurecli-interactive
az ad sp create-for-rbac -n "http://mySP"
```

Az objectId a kimenetben a `clientID`.

Az Azure PowerShell használatával használja a [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal?view=azps-2.7.0) parancsmag.


```azurepowershell-interactive
New-AzADServicePrincipal -DisplayName mySP
```

Az objectId a kimenetben `Id` (nem `ApplicationId`) jelenik meg.

#### <a name="azure-ad-groups"></a>Azure AD-csoportok

Több alkalmazást és felhasználót adhat hozzá egy Azure AD-csoporthoz, majd hozzáférést adhat a csoportnak a kulcstartóhoz.  További részletekért tekintse meg a [tagok létrehozása és hozzáadása egy Azure AD-csoporthoz](#creating-and-adding-members-to-an-azure-ad-group) szakasz, alább.

Az Azure AD-csoport objektumazonosítójának megkereséséhez az Azure CLI használatával használja az [az ad csoportlista](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-list) parancs. A szervezetben lévő csoportok nagy száma miatt meg kell adnia egy `--display-name` keresési karakterláncot is a paraméterhez.

```azurecli-interactive
az ad group list --display-name <search-string>
```
Az objectId a JSON-ban lesz visszaadva:

```json
    "objectId": "48b21bfb-74d6-48d2-868f-ff9eeaf38a64",
    "objectType": "Group",
    "odata.type": "Microsoft.DirectoryServices.Group",
```

Az Azure PowerShell használatával egy Azure AD-csoport objektumazonosítójának megkereséséhez használja a [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup?view=azps-2.7.0) parancsmag. A szervezetben lévő csoportok nagy száma miatt valószínűleg keresési karakterláncot is szeretne `-SearchString` megadni a paraméterhez.

```azurepowershell-interactive
Get-AzADGroup -SearchString <search-string>
```

A kimenetben az objectId a `Id`következőként jelenik meg:

```console
...
Id                    : 1cef38c4-388c-45a9-b5ae-3d88375e166a
...
```

#### <a name="users"></a>Felhasználók

Egyéni felhasználót is hozzáadhat a key vault hozzáférés-vezérlési szabályzatához. **Ezt nem javasoljuk.** Ehelyett azt javasoljuk, hogy felhasználókat adjon hozzá egy Azure AD-csoporthoz, és adja hozzá a csoportot a szabályzatok.

Ha ennek ellenére meg szeretné találni az Azure CLI-vel rendelkező felhasználót, használja az [az ad user show](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-show) parancsot, és adja át a felhasználók e-mail címét a `--id` paraméternek.


```azurecli-interactive
az ad user show --id <email-address-of-user>
```

A felhasználó objectId-ja a következő értéket adja vissza a kimenetben:

```console
  ...
  "objectId": "f76a2a6f-3b6d-4735-9abd-14dccbf70fd9",
  "objectType": "User",
  ...
```

Az Azure PowerShell-felhasználó megkereséséhez használja a [Get-AzADUser](/powershell/module/az.resources/get-azaduser?view=azps-2.7.0) parancsmaggal, `-UserPrincipalName` és adja át a felhasználók e-mail-címét a paraméternek.

```azurepowershell-interactive
 Get-AzAdUser -UserPrincipalName <email-address-of-user>
```

A felhasználó objectId-ja a kimenetben `Id`a .

```console
...
Id                : f76a2a6f-3b6d-4735-9abd-14dccbf70fd9
Type              :
```

### <a name="give-the-principal-access-to-your-key-vault"></a>A kulcstartó hoz való hozzáférés alapvető adatainak megírása

Most, hogy rendelkezik a fő objektumazonosítójával, létrehozhat egy hozzáférési szabályzatot a kulcstartóhoz, amely lekéri, listázni, beállítani és törölni a kulcsok és titkos kulcsok engedélyeit, valamint a kívánt további engedélyeket.

Az Azure CLI ezzel az objectId átadásával az [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) parancsot.

```azurecli-interactive
az keyvault set-policy -n <your-unique-keyvault-name> --spn <ApplicationID-of-your-service-principal> --secret-permissions get list set delete --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

Az Azure PowerShell, ez úgy történik, hogy átadja az objectId a [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) parancsmag. 

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy –VaultName <your-key-vault-name> -PermissionsToKeys create,decrypt,delete,encrypt,get,list,unwrapKey,wrapKey -PermissionsToSecrets get,list,set,delete -ObjectId <Id>

```

## <a name="creating-and-adding-members-to-an-azure-ad-group"></a>Tagok létrehozása és hozzáadása egy Azure AD-csoporthoz

Létrehozhat egy Azure AD-csoportot, hozzáadhat alkalmazásokat és felhasználókat a csoporthoz, és hozzáférést adhat a csoportnak a kulcstartóhoz.  Ez lehetővé teszi, hogy egyetlen hozzáférési házirend-bejegyzésként több alkalmazást adjon hozzá egy kulcstartóhoz, és szükségtelenné teszi, hogy a felhasználók nak közvetlen hozzáférést kell biztosítania a kulcstartóhoz (amit nem teszünk). További információt az [Alkalmazás- és erőforrás-hozzáférés kezelése az Azure Active Directory-csoportok használatával (App and Resource Access kezelése) témakörben](../active-directory/fundamentals/active-directory-manage-groups.md)talál.

### <a name="additional-prerequisites"></a>További előfeltételek

A fenti [előfeltételeken](#prerequisites)kívül engedélyeket is szüksége lesz a csoportok létrehozásához/szerkesztéséhez az Azure Active Directory-bérlőben. Ha nem rendelkezik engedélyekkel, előfordulhat, hogy kapcsolatba kell lépnie az Azure Active Directory rendszergazdájával.

Ha a PowerShellt kívánja használni, szüksége lesz az [Azure AD PowerShell-modulra](https://www.powershellgallery.com/packages/AzureAD/2.0.2.50) is

### <a name="create-an-azure-active-directory-group"></a>Azure Active Directory-csoport létrehozása

Hozzon létre egy új Azure Active Directory-csoportot az Azure CLI [az ad group create](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-create) paranccsal vagy az Azure PowerShell [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup?view=azureadps-2.0) parancsmaggal.


```azurecli-interactive
az ad group create --display-name <your-group-display-name> --mail-nickname <your-group-mail-nickname>
```

```powershell
New-AzADGroup -DisplayName <your-group-display-name> -MailNickName <your-group-mail-nickname>
```

Mindkét esetben jegyezze fel az újonnan létrehozott csoportok GroupId, ahogy szüksége lesz rá az alábbi lépéseket.

### <a name="find-the-objectids-of-your-applications-and-users"></a>Az alkalmazások és felhasználók tárgyazonosítóinak megkeresése

Az alkalmazások objektumazonosítóit az Azure CLI használatával az [az ad sp](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) list `--show-mine` parancs, a paraméter segítségével találhatja meg.

```azurecli-interactive
az ad sp list --show-mine
```

Keresse meg az alkalmazások objektumazonosítóit az Azure PowerShell használatával a [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal?view=azps-2.7.0) parancsmaggal, és egy keresési karakterláncot ad át a `-SearchString` paraméternek.

```azurepowershell-interactive
Get-AzADServicePrincipal -SearchString <search-string>
```

A Felhasználók objektumazonosítóinak megkereséséhez kövesse a fenti [Felhasználók](#users) szakasz lépéseit.

### <a name="add-your-applications-and-users-to-the-group"></a>Alkalmazások és felhasználók hozzáadása a csoporthoz

Most adja hozzá az objektumazonosítókat az újonnan létrehozott Azure AD-csoporthoz.

Az Azure CLI használatával használja az [az ad csoport tag hozzáadása,](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add)átadása az objectId a `--member-id` paraméter.


```azurecli-interactive
az ad group member add -g <groupId> --member-id <objectId>
```

Az Azure PowerShell használatával használja az [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember?view=azps-2.7.0) parancsmagját, `-MemberObjectId` és adja át az objectId-t a paraméternek.

```azurepowershell-interactive
Add-AzADGroupMember -TargetGroupObjectId <groupId> -MemberObjectId <objectId> 
```

### <a name="give-the-ad-group-access-to-your-key-vault"></a>Hozzáférés az AD-csoportnak a kulcstartóhoz

Végül adja meg az AD-csoport engedélyeit a key vault az Azure CLI [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) parancs, vagy az Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) parancsmag használatával. Példák: [Az alkalmazás, az Azure AD-csoport vagy a felhasználói hozzáférés a key vault](#give-the-principal-access-to-your-key-vault) szakasz, a fenti.

Az alkalmazásnak legalább egy identitás- és hozzáférés-kezelési (IAM) szerepkört is hozzá kell rendelnie a key vaulthoz. Ellenkező esetben nem lesz képes bejelentkezni, és nem lesz megfelelő jogosultsággal az előfizetés eléréséhez.

## <a name="next-steps"></a>További lépések

- [Az Azure Key Vault biztonsága: Identitás- és hozzáférés-kezelés](overview-security.md#identity-and-access-management)
- [Key Vault-hitelesítés biztosítása egy App Service által felügyelt identitással](managed-identity.md)
- [A kulcsok, titkos kódok és tanúsítványok ismertetése](about-keys-secrets-and-certificates.md)
- [Biztosítsa a kulcstartót.](key-vault-secure-your-key-vault.md)
- [Az Azure Key Vault fejlesztői útmutatója](key-vault-developers-guide.md)
- Tekintse át az [Azure Key Vault gyakorlati tanácsait](key-vault-best-practices.md)
