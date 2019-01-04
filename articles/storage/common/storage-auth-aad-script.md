---
title: Az Azure parancssori felület vagy PowerShell-parancsok futtatásával alatt egy Azure AD identity eléréséhez az Azure Storage (előzetes verzió) |} A Microsoft Docs
description: Az Azure CLI és PowerShell támogatja az Azure AD identitás-parancsok futtatásához az Azure Storage-tárolók és a várólisták és az adataik bejelentkezik. Egy hozzáférési jogkivonatot a munkamenet számára megadott, és a hívó műveletek engedélyezése. Engedélyek az Azure AD identity rendelt szerepkör függenek.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/15/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: b5a129c2a92c18b979a3b0c2eeea7fa19791551c
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53633765"
---
# <a name="use-an-azure-ad-identity-to-access-azure-storage-with-cli-or-powershell-preview"></a>Az Azure AD identity használata a parancssori felület vagy a PowerShell (előzetes verzió) az Azure Storage eléréséhez

Az Azure Storage előzetes bővítményeket biztosít az Azure CLI és PowerShell, amelyek lehetővé teszik, hogy jelentkezzen be, és a egy Azure Active Directory (Azure AD) identitás alatt parancsfájl-kezelési parancsok futtatásához. Az Azure AD identity lehet egy felhasználó, csoport vagy alkalmazás egyszerű szolgáltatást, vagy lehet egy [-identitás az Azure-erőforrások](../../active-directory/managed-identities-azure-resources/overview.md). Hozzárendelhet engedélyeket az eléréséhez a tárolási erőforrások az Azure AD Identity szerepköralapú hozzáférés-vezérlés (RBAC) keresztül. Az Azure Storage-ban RBAC-szerepkörök kapcsolatos további információkért lásd: [kezelés hozzáférési jogosultsága ahhoz, hogy az RBAC (előzetes verzió) az Azure Storage-adatokkal](storage-auth-aad-rbac.md).

Amikor bejelentkezik az Azure CLI vagy a PowerShell egy Azure AD-identitással, egy hozzáférési jogkivonatot, hogy az identitás alatt az Azure Storage eléréséhez adja vissza. A jogkivonat ezután automatikusan használják parancssori felület vagy PowerShell történő hitelesítéséhez az Azure Storage kapcsolatos művelet-végrehajtási. A támogatott műveletek már nincs szüksége egy kulccsal vagy SAS-jogkivonat a paranccsal át.

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

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

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure PowerShell használatával jelentkezzen be az Azure AD identitás:

1. Távolítsa el az összes az Azure PowerShell előző telepítés:

    - Távolítsa el az összes korábbi telepítés az Azure PowerShell használatával Windows a **alkalmazások és szolgáltatások** menüpont **beállítások**.
    - Távolítsa el az összes **Azure*** a modulok `%Program Files%\WindowsPowerShell\Modules`.

1. Győződjön meg arról, hogy telepítve van a PowerShellGet legújabb verzióját. Nyisson meg egy Windows PowerShell-ablakot, és a legújabb verzió telepítéséhez a következő parancsot:
 
    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```
1. Zárja be, majd nyissa meg a PowerShell-ablakban a PowerShellGet telepítése után. 

1. Telepítse az Azure PowerShell legújabb verzióját:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Telepítse az Azure Storage előzetes modul, amely támogatja az Azure ad-ben:
   
   ```powershell
   Install-Module Az.Storage -Repository PSGallery -AllowPrerelease -AllowClobber -Force
   ```
1. Zárja be és nyissa meg ismét a PowerShell-ablakban.
1. Hívja a [New-AzStorageContext](https://docs.microsoft.com/powershell/module/azure.storage/new-AzStoragecontext) parancsmag segítségével hozzon létre egy környezetet, és tartalmazza a `-UseConnectedAccount` paraméter. 
1. Meghívnak egy parancsmagot, az Azure AD-identitásnak, hogy az újonnan létrehozott környezet átadása a parancsmaghoz.

Az alábbi példa bemutatja, hogyan az Azure PowerShell tárolóban lévő blobok listázásához az Azure AD-identitás használatával. Ügyeljen arra, hogy cserélje le a helyőrző fiók és a tároló nevét a saját értékeire: 

```powershell
$ctx = New-AzStorageContext -StorageAccountName storagesamples -UseConnectedAccount 
Get-AzStorageBlob -Container sample-container -Context $ctx 
```

## <a name="next-steps"></a>További lépések

- Az Azure storage szolgáltatáshoz az RBAC-szerepkörök kapcsolatos további információkért lásd: [kezelés hozzáférési jogosultsága ahhoz, hogy az RBAC (előzetes verzió) tárolási adatok](storage-auth-aad-rbac.md).
- Felügyelt identitások az Azure-erőforrások az Azure Storage használatával kapcsolatos további információkért lásd: [hitelesítés blobok és üzenetsorok az Azure-ral a hozzáférést az Azure-erőforrások (előzetes verzió) által felügyelt identitások](storage-auth-aad-msi.md).
- A tárolókhoz és üzenetsorok, a storage-alkalmazásokban való elérésének hitelesítéséhez, lásd: [storage alkalmazásait az Azure AD segítségével](storage-auth-aad-app.md).
- Az Azure-Blobok és üzenetsorok az Azure AD-integrációval kapcsolatos további információkért lásd: az Azure Storage csapat blogja tenne fel, [bejelentése a megtekintése az Azure AD-hitelesítés az Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
