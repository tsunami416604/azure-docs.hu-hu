---
title: Az Azure parancssori felület vagy a PowerShell-parancsok futtatásával alatt az Azure AD identity elérni az Azure Storage (előzetes verzió) |} Microsoft Docs
description: Az Azure CLI és PowerShell támogatja a parancsok futtatásához Azure Storage tárolók és a várólisták és az adatokat az Azure AD identitás bejelentkezik. Olyan hozzáférési jogkivonatot a munkamenet és annak hitelesíthetők hívó műveletek. Engedélyek az Azure AD identity rendelt szerepkör függ.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/30/2018
ms.author: tamram
ms.openlocfilehash: 27397d490902625d10e54ea17a9e534fbfbca8cd
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757502"
---
# <a name="use-an-azure-ad-identity-to-access-azure-storage-with-cli-or-powershell-preview"></a>Az Azure AD identity használatával férjenek hozzá a Azure Storage CLI vagy a PowerShell használatával (előzetes verzió)

Az Azure Storage Azure CLI és a PowerShell, amelyek lehetővé teszik, hogy jelentkezzen be és futtassa a parancsokat egy Azure Active Directory (Azure AD) identitás preview bővítményeket biztosít. Az Azure AD identity lehet egy felhasználó, csoport vagy alkalmazás egyszerű, vagy azok egy [identitás](../../active-directory/managed-service-identity/overview.md). A szerepköralapú hozzáférés-vezérlést (RBAC) keresztül az Azure AD Identity tárolási erőforrások eléréséhez engedélyeket rendelhet. További információ a RBAC-szerepkörök az Azure Storage: [kezelése hozzáférési jogosultsága ahhoz, hogy az RBAC (előzetes verzió) Azure Storage-adatokkal](storage-auth-aad-rbac.md).

Amikor bejelentkezik Azure parancssori felületen vagy a PowerShell egy Azure AD-identitás, Azure Storage eléréséhez az identitásukat egy hozzáférési jogkivonatot adja vissza. A token majd automatikusan történik CLI vagy a PowerShell engedélyezése az Azure Storage műveleteket. A támogatott műveleteket már nincs szüksége egy fiókkulcs vagy SAS-jogkivonat a parancs átadni.

> [!IMPORTANT]
> Ez az előzetes kiadás csak nem üzemi használatra készült. Éles szolgáltatásiszint-szerződések (SLA) nem lesz elérhető, amíg az Azure AD integrálása az Azure Storage általánosan elérhető van deklarálva. Ha az Azure AD-integrációs jelenleg nem támogatott a forgatókönyvnek, továbbra is használhatja a megosztott kulcsos engedélyezési vagy SAS-tokenje az alkalmazásokban. Az előzetes kapcsolatos további információkért lásd: [hitelesíti a hozzáférést az Azure Active Directoryval (előzetes verzió) Azure Storage](storage-auth-aad.md).
>
> Az előzetes RBAC szerepkör-hozzárendelések terjesztése akár öt percet is eltarthat.
>
> Az Azure AD integrálása az Azure Storage Azure Storage műveletekhez HTTPS használatát igényli.

## <a name="supported-operations"></a>Támogatott műveletek

A minta-bővítmények a tárolók és a várólisták műveleteihez támogatottak. Milyen műveleteket, lehet, hogy a hívás a Azure AD identitása, amelynek bejelentkezés az Azure parancssori felület vagy a PowerShell jogosultságaitól függ. Azure Storage tárolók vagy várólisták engedélyeket szerepköralapú hozzáférés-vezérlést (RBAC) keresztül. Például ha egy Adatolvasó szerepkör van rendelve az identitás, majd futtathatja parancsokat, amelyek a tárolóból vagy várólista-adatok olvasása. Az azonosító adatok közreműködői szerepkör van hozzárendelve, majd futtathatja parancsokat, amelyek az olvasási, írási vagy törlése egy tárolót vagy várólista, vagy hogy milyen adatokat tartalmaznak. 

Egy tároló vagy egy sor minden Azure Storage művelethez szükséges engedélyekkel kapcsolatos részletekért lásd: [REST műveleteinek hívása engedélyeinek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).  

## <a name="call-cli-commands-with-an-azure-ad-identity"></a>Hívás parancssori felület parancsait egy Azure AD-identitás

Az előnézet-bővítmény telepítése Azure parancssori felület esetén:

1. Győződjön meg arról, hogy telepítette-e az Azure CLI verzió 2.0.32 vagy újabb. Futtatás `az --version` a telepített verzió ellenőrzéséhez.
2. A következő parancsot a preview bővítmény telepítéséhez: 

    ```azurecli
    az extension add -n storage-preview
    ```

A kép bővítmény hozzáadja egy új `--auth-mode` paraméter támogatott parancsok:

- Állítsa be a `--auth-mode` paramétert `login` is bejelentkezhet egy Azure AD identity.
- Állítsa be a `--auth-mode` paramétert az örökölt `key` megpróbálja lekérdezni a valamelyik kulcs fiókot, ha a fiók hitelesítési paraméter nélküli értéket kapnak. 

Például az Azure CLI használata az Azure AD identity blob letöltéséhez először futtassa `az login`, a parancsot, majd hívja `--auth-mode` beállítása `login`:

```azurecli
az login
az storage blob download --account-name storagesamples --container sample-container --name myblob.txt --file myfile.txt --auth-mode login 
```

A környezeti változó társított a `--auth-mode` paraméter `AZURE_STORAGE_AUTH_MODE`.

## <a name="call-powershell-commands-with-an-azure-ad-identity"></a>Az Azure AD identity PowerShell-parancsokat hívása

Azure PowerShell használatával jelentkezzen be az Azure AD identity:

1. Győződjön meg arról, hogy rendelkezik-e telepítve PowerShellGet legújabb verzióját. A következő paranccsal telepítse a legújabb:
 
    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

2. Távolítsa el az Azure PowerShell minden korábbi telepítéshez.
3. Telepítse az Azure RM-ben:

    ```powershell
    Install-Module AzureRM –Repository PSGallery –AllowClobber
    ```

4. A minta-modul telepítése:

    ```powershell
    Install-Module-Name Azure.Storage-RequiredVersion 4.4.0-AllowPrerelease –AllowClobber -Repository PSGallery -Force 
    ```

5. Hívja a [New-AzureStorageContext](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontext) parancsmag segítségével hozzon létre olyan környezetben, és tartalmazzák a `-UseConnectedAccount` paraméter. 
6. Az Azure AD identitással parancsmag hívására, a környezet átadása a parancsmagnak.

A következő példa bemutatja, hogyan az Azure PowerShell a tárolóban lévő blobok listázásához használja az Azure AD identity: 

```powershell
$ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -UseConnectedAccount 
Get-AzureStorageBlob -Container $sample-container -Context $ctx 
```

## <a name="next-steps"></a>További lépések

- Az Azure-tárolás RBAC-szerepkörök kapcsolatos további információkért lásd: [kezelése hozzáférési jogosultsága ahhoz, hogy az RBAC (előzetes verzió) storage-adatokkal](storage-auth-aad-rbac.md).
- Az Azure Storage felügyelt szolgáltatás identitás használatával kapcsolatos további tudnivalókért lásd: [hitelesítés az Azure Managed Service identitás (előzetes verzió) Azure AD-val](storage-auth-aad-msi.md).
- A tárolók és a tárolási alkalmazásokon belül a várólista elérésének hitelesítéséhez, lásd: [tárolási alkalmazások az Azure AD használatára](storage-auth-aad-app.md).
- Az Azure AD integrálása az Azure-blobokat és üzenetsorokat kapcsolatos további információkért lásd a következő Azure Storage csapat blogbejegyzésben, [az Azure betekintő AD hitelesítés az Azure Storage bejelentése](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
