---
title: Az Azure parancssori felület vagy PowerShell-parancsok futtatásával alatt egy Azure AD identity eléréséhez az Azure Storage (előzetes verzió) |} A Microsoft Docs
description: Az Azure CLI és PowerShell támogatja az Azure AD identitás-parancsok futtatásához az Azure Storage-tárolók és a várólisták és az adataik bejelentkezik. Egy hozzáférési jogkivonatot a munkamenet számára megadott, és a hívó műveletek engedélyezése. Engedélyek az Azure AD identity rendelt szerepkör függenek.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/29/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 56e7467443cb91f73a6b48f318d576345000a0e0
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2018
ms.locfileid: "44023097"
---
# <a name="use-an-azure-ad-identity-to-access-azure-storage-with-cli-or-powershell-preview"></a>Az Azure AD identity használata a parancssori felület vagy a PowerShell (előzetes verzió) az Azure Storage eléréséhez

Az Azure Storage előzetes bővítményeket biztosít az Azure CLI és PowerShell, amelyek lehetővé teszik, hogy jelentkezzen be, és a egy Azure Active Directory (Azure AD) identitás alatt parancsfájl-kezelési parancsok futtatásához. Az Azure AD identity lehet egy felhasználó, csoport vagy alkalmazás egyszerű szolgáltatást, vagy lehet egy [felügyeltszolgáltatás-identitás](../../active-directory/managed-identities-azure-resources/overview.md). Hozzárendelhet engedélyeket az eléréséhez a tárolási erőforrások az Azure AD Identity szerepköralapú hozzáférés-vezérlés (RBAC) keresztül. Az Azure Storage-ban RBAC-szerepkörök kapcsolatos további információkért lásd: [kezelés hozzáférési jogosultsága ahhoz, hogy az RBAC (előzetes verzió) az Azure Storage-adatokkal](storage-auth-aad-rbac.md).

Amikor bejelentkezik az Azure CLI vagy a PowerShell egy Azure AD-identitással, egy hozzáférési jogkivonatot, hogy az identitás alatt az Azure Storage eléréséhez adja vissza. A jogkivonat ezután automatikusan használják parancssori felület vagy PowerShell történő hitelesítéséhez az Azure Storage kapcsolatos művelet-végrehajtási. A támogatott műveletek már nincs szüksége egy kulccsal vagy SAS-jogkivonat a paranccsal át.

> [!IMPORTANT]
> Ebben az előzetes verzióban csak a nem éles használatra szolgál. Éles szolgáltatásiszint-szerződések (SLA) nem lesz elérhető, amíg az Azure AD integrálása az Azure Storage általánosan elérhető lett deklarálva. Ha az Azure AD-integrációs még nem támogatott a forgatókönyvnek, továbbra is használhatja a megosztott kulcsos engedélyezési vagy SAS-tokeneket az alkalmazásokban. Az előzetes verzióra vonatkozó további információkért lásd: [hitelesíti a hozzáférést az Azure Storage, Azure Active Directory (előzetes verzió) használatával](storage-auth-aad.md).
>
> Az előzetes verzióban RBAC szerepkör-hozzárendelések eltarthat propagálása akár öt perc alatt.
>
> Az Azure AD-integráció az Azure Storage for Azure Storage-műveletek HTTPS használatát igényli.

## <a name="supported-operations"></a>Támogatott műveletek

Az előzetes verzió bővítmények tárolókat, valamint a kis-és üzenetsorok esetén támogatottak. Hívja előfordulhat, hogy mely műveletek az Azure AD Identity, amellyel kell bejelentkezni az Azure parancssori felület vagy PowerShell jogosultságaitól függ. Az Azure Storage-tárolók, vagy várólisták engedélyeket szerepköralapú hozzáférés-vezérlés (RBAC) keresztül. Például ha egy Adatolvasó szerepkör van rendelve az identitást, majd futtathatja parancsokat, amelyek az adatok olvasása a tárolóból vagy üzenetsor. Ha egy adatok közreműködői szerepkör van rendelve az identitást, majd futtathatja parancsokat, amelyek olvasási, írási, vagy egy tároló vagy üzenetsor vagy a rajtuk tárolt adatok törlése. 

Minden Azure Storage-művelet egy tároló vagy a várólista szükséges jogosultságokat kapcsolatos részletekért lásd: [REST-műveleteinek meghívására szolgáló engedélyek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).  

## <a name="call-cli-commands-with-an-azure-ad-identity"></a>CLI-parancsok az Azure AD identitás-hívás

Az Azure CLI előzetes verzió bővítményének telepítése:

1. Győződjön meg arról, hogy telepítette-e az Azure CLI-verzió 2.0.32 vagy újabb verziója. Futtatás `az --version` a telepített verzió ellenőrzéséhez.
2. Futtassa a következő parancsot a minta-bővítmény telepítéséhez: 

    ```azurecli
    az extension add -n storage-preview
    ```

Az előzetes verzió bővítmény hozzáadása egy új `--auth-mode` paramétert a támogatott parancsok:

- Állítsa be a `--auth-mode` paramétert `login` jelentkezzen be az Azure AD identitás.
- Állítsa be a `--auth-mode` paramétert az örökölt `key` értéket, hogy megpróbálja lekérdezni egy fiók fő Ha nincs a fiókhoz tartozó hitelesítési paraméterek vannak megadva. 

Például egy blobot az Azure CLI használatával az Azure AD identity letöltéséhez először futtassa `az login`, majd hívja meg a parancsot a `--auth-mode` beállítása `login`:

```azurecli
az login
az storage blob download --account-name storagesamples --container sample-container --name myblob.txt --file myfile.txt --auth-mode login 
```

A társított környezeti változót a `--auth-mode` paraméter `AZURE_STORAGE_AUTH_MODE`.

## <a name="call-powershell-commands-with-an-azure-ad-identity"></a>Hívja a PowerShell-parancsok az Azure AD-identitás használata

Azure PowerShell használatával jelentkezzen be az Azure AD identitás:

1. Győződjön meg arról, hogy telepítve van a PowerShellGet legújabb verzióját. Futtassa a következő parancsot a legújabb verziójának telepítéséhez:
 
    ```powershell
    Install-Module -Name Azure.Storage -AllowPrerelease –AllowClobber -RequiredVersion "4.4.1-preview"
    ```

2. Távolítsa el az összes az Azure PowerShell korábbi telepítés.
3. Telepítse az AzureRM:

    ```powershell
    Install-Module AzureRM –Repository PSGallery –AllowClobber
    ```

4. Az előzetes verzió modul telepítése:

    ```powershell
    Install-Module -Name Azure.Storage -AllowPrerelease –AllowClobber 
    ```

5. Hívja a [New-azurestoragecontext parancsmaggal kapcsolatos](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontext) parancsmag segítségével hozzon létre egy környezetet, és tartalmazza a `-UseConnectedAccount` paraméter. 
6. Meghívnak egy parancsmagot, az Azure AD-identitásnak, hogy a környezet átadható a parancsmagnak.

Az alábbi példa bemutatja az Azure PowerShell-lel a tárolóban lévő blobok listázása az Azure AD-identitás használatával: 

```powershell
$ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -UseConnectedAccount 
Get-AzureStorageBlob -Container $sample-container -Context $ctx 
```

## <a name="next-steps"></a>További lépések

- Az Azure storage szolgáltatáshoz az RBAC-szerepkörök kapcsolatos további információkért lásd: [kezelés hozzáférési jogosultsága ahhoz, hogy az RBAC (előzetes verzió) tárolási adatok](storage-auth-aad-rbac.md).
- Az Azure Storage Felügyeltszolgáltatás-identitás használatával kapcsolatos további információkért lásd: [hitelesítés az Azure Felügyeltszolgáltatás-identitás (előzetes verzió) az Azure AD-vel](storage-auth-aad-msi.md).
- A tárolókhoz és üzenetsorok, a storage-alkalmazásokban való elérésének hitelesítéséhez, lásd: [storage alkalmazásait az Azure AD segítségével](storage-auth-aad-app.md).
- Az Azure-Blobok és üzenetsorok az Azure AD-integrációval kapcsolatos további információkért lásd: az Azure Storage csapat blogja tenne fel, [bejelentése a megtekintése az Azure AD-hitelesítés az Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
