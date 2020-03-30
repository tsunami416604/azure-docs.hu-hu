---
title: Az Azure AD tartományi szolgáltatások használata a fájladatokhoz való hozzáférés engedélyezése SMB-n keresztül
description: Megtudhatja, hogyan engedélyezheti az identitásalapú hitelesítést az Azure-fájlok kiszolgálói üzenetblokkon (SMB) az Azure Active Directory tartományi szolgáltatásokon keresztül. A tartományhoz csatlakozó Windows virtuális gépek (VM-ek) ezután az Azure-fájlmegosztások azure AD hitelesítő adatok használatával férhetnek hozzá.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e2e3c7763a13c8850554b079a426ed4172b74d28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77599274"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Az Azure Active Directory tartományi szolgáltatások hitelesítésének engedélyezése az Azure-fájlokban

[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Az Azure AD-hitelesítés SMB-n keresztüli azure-fájlmegosztások áttekintése az [Azure Active Directory-hitelesítés áttekintése Az Azure Files smb-n keresztül](storage-files-active-directory-overview.md)című témakörben olvashat. Ez a cikk arra összpontosít, hogyan engedélyezheti a hitelesítést az Azure Active Directory tartományi szolgáltatásokkal (Azure AD DS) az Azure Files-on.

> [!NOTE]
> Az Azure Files támogatja a Kerberos-hitelesítést az RC4-HMAC titkosítással rendelkező Azure AD DS szolgáltatással. Az AES Kerberos titkosítás még nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt engedélyezne az Azure AD-t az SMB-n keresztül az Azure-fájlmegosztások számára, győződjön meg arról, hogy teljesítette az alábbi előfeltételeket:

1.  **Válasszon ki vagy hozzon létre egy Azure AD-bérlőt.**

    Az Azure AD-hitelesítéshez használhat új vagy meglévő bérlőt SMB-n keresztül. A bérlőés a fájlmegosztás, amely hez hozzá szeretne érni, ugyanahhoz az előfizetéshez kell társítható.

    Új Azure AD-bérlő létrehozásához [hozzáadhat egy Azure AD-bérlőt és egy Azure AD-előfizetést.](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription) Ha rendelkezik egy meglévő Azure AD-bérlővel, de új bérlőt szeretne létrehozni az Azure-fájlmegosztásokhoz, olvassa el [az Azure Active Directory-bérlő létrehozása](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant)című témakört.

1.  **Engedélyezze az Azure AD tartományi szolgáltatásokat az Azure AD-bérlőn.**

    Az Azure AD-hitelesítő adatokkal való hitelesítés támogatásához engedélyeznie kell az Azure AD tartományi szolgáltatásokat az Azure AD-bérlőszámára. Ha nem ön az Azure AD-bérlő rendszergazdája, lépjen kapcsolatba a rendszergazdával, és kövesse a lépésről lépésre mutató útmutatást [az Azure Active Directory tartományi szolgáltatások engedélyezéséhez az Azure Portalon](../../active-directory-domain-services/tutorial-create-instance.md)keresztül.

    Általában körülbelül 15 percet vesz igénybe egy Azure AD DS-telepítés befejezéséhez. Ellenőrizze, hogy az Azure AD DS állapotának **állapota fut-e,** és engedélyezve van-e a jelszókivonat-szinkronizálás, mielőtt továbblépne a következő lépésre.

1.  **Tartomány-csatlakozzon egy Azure-virtuális gép az Azure AD DS.Domain-join an Azure VM with Azure AD DS.**

    A fájlmegosztás eléréséhez az Azure AD hitelesítő adatok használatával egy virtuális gép, a virtuális gép kell tartomány-csatlakozott az Azure AD DS-hez. A virtuális gépek hez való tartománycsatlakozásról további információt a [Csatlakozás Windows Server rendszerű virtuális géphez felügyelt tartományhoz](../../active-directory-domain-services/join-windows-vm.md)című témakörben talál.

    > [!NOTE]
    > Az Azure AD DS-hitelesítés Az Azure-fájlmegosztásokkal rendelkező SMB-n keresztül csak a Windows 7 vagy Windows Server 2008 R2 feletti operációs rendszeren futó Azure-beli virtuális gépeken támogatott.

1.  **Jelöljön ki vagy hozzon létre egy Azure-fájlmegosztást.**

    Válasszon ki egy új vagy meglévő fájlmegosztást, amely ugyanahhoz az előfizetéshez van társítva, mint az Azure AD-bérlő. Az új fájlmegosztás létrehozásáról a Fájlmegosztás létrehozása az Azure Files alkalmazásban című [témakörben talál.](storage-how-to-create-file-share.md)
    Az optimális teljesítmény érdekében azt javasoljuk, hogy a fájlmegosztás ugyanabban a régióban legyen, mint az a virtuális gép, amelyből a megosztáselérését tervezi.

1.  **Ellenőrizze az Azure Files-kapcsolatot az Azure-fájlmegosztások csatlakoztatásával a tárfiók kulcsával.**

    Annak ellenőrzéséhez, hogy a virtuális gép és a fájlmegosztás megfelelően van-e konfigurálva, próbálja meg a fájlmegosztást a tárfiók kulcsával. További információt az [Azure-fájlmegosztás csatlakoztatása és a Windows-megosztás elérése című témakörben talál.](storage-how-to-use-files-windows.md)

## <a name="overview-of-the-workflow"></a>A munkafolyamat áttekintése

Mielőtt engedélyezne Az Azure AD DS-hitelesítés t SMB-n keresztül az Azure-fájlmegosztások, ellenőrizze, hogy az Azure AD és az Azure Storage-környezetek megfelelően vannak konfigurálva. Azt javasoljuk, hogy kövesse el az [előfeltételeket,](#prerequisites) hogy megbizonyosodjon arról, hogy elvégezte az összes szükséges lépést.

Ezután tegye a következő műveleteket az Azure Files-erőforrásokhoz való hozzáférés engedélyezéséhez az Azure AD-hitelesítő adatokkal:

- Engedélyezze az Azure AD DS-hitelesítést Az SMB-n keresztül a tárfiók hoz a tárfiók regisztrálásához a társított Azure AD DS-telepítés.
- Hozzáférési engedélyek hozzárendelése egy megosztáshoz egy Azure AD-identitáshoz (felhasználóhoz, csoporthoz vagy egyszerű szolgáltatáshoz).
- NTFS-engedélyek konfigurálása SMB-en keresztül könyvtárakhoz és fájlokhoz.
- Azure-fájlmegosztás csatlakoztatása egy tartományhoz csatlakoztatott virtuális gépről.

Az alábbi ábra bemutatja a teljes körű munkafolyamat ot az Azure AD DS-hitelesítés engedélyezéséhez az Azure Files SMB-n keresztül.

![Az Azure AD-t az Azure Files munkafolyamathoz való smb-n keresztüli ábrája](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Az Azure AD DS-hitelesítés engedélyezése a fiókjához

Az Azure AD DS-hitelesítés engedélyezéséhez Az Azure Files SMB-n keresztül, az Azure Portalon, az Azure PowerShellen vagy az Azure CLI-n keresztül beállíthat egy tulajdonságot a tárfiókokon. A tulajdonság beállítása implicit módon "tartomány csatlakozik" a tárfiókot a társított Azure AD DS-telepítés. Az Azure AD DS-hitelesítés SMB-n keresztül ezután engedélyezve van a tárfiók összes új és meglévő fájlmegosztásához.

Ne feledje, hogy csak akkor engedélyezheti az Azure AD DS-hitelesítést SMB-n keresztül, ha sikeresen telepítette az Azure AD DS-t az Azure AD-bérlőre. További információt az [előfeltételek](#prerequisites)ben talál.

### <a name="azure-portal"></a>Azure portál

Az Azure AD DS-hitelesítés [smb-n](https://portal.azure.com)keresztüli engedélyezéséhez kövesse az alábbi lépéseket:

1. Az Azure Portalon nyissa meg a meglévő tárfiókot, vagy [hozzon létre egy tárfiókot.](../common/storage-account-create.md)
1. A **Beállítások csoportban** válassza a **Konfiguráció**lehetőséget.
1. A **fájlmegosztások identitásalapú hozzáférése** csoportban váltson az **Azure Active Directory tartományi szolgáltatás (AAD DS)** **kapcsolójára engedélyezve.**
1. Kattintson a **Mentés** gombra.

Az alábbi képen bemutatja, hogyan engedélyezheti az Azure AD DS-hitelesítést SMB-n keresztül a tárfiókhoz.

![Az Azure AD DS-hitelesítés engedélyezése SMB-n keresztül az Azure Portalon](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

### <a name="powershell"></a>PowerShell  

Az Azure AD DS-hitelesítés engedélyezéséhez SMB-n keresztül az Azure PowerShell segítségével telepítse a legújabb Az modult (2.4 vagy újabb) vagy az Az.Storage modult (1.5 vagy újabb). A PowerShell telepítéséről az [Azure PowerShell telepítése Windows rendszeren a PowerShellGet szolgáltatással](https://docs.microsoft.com/powershell/azure/install-Az-ps)című témakörben talál további információt.

Új tárfiók létrehozásához hívja meg a [New-AzStorageAccount metódust,](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0)majd állítsa az **EnableAzureActiveDirectoryDomainServicesForFile** paramétert **true**értékre. A következő példában ne felejtse el lecserélni a helyőrző értékeket a saját értékeire. (Ha az előző előzetes modult használta, a szolgáltatásengedélyezések paramétere **enableAzureFilesAadIntegrationForSMB.)**

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

Ha engedélyezni szeretné ezt a szolgáltatást a meglévő tárfiókokon, használja a következő parancsot:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>Azure CLI

Az Azure AD-hitelesítés smb-n keresztüli engedélyezéséhez telepítse a legújabb CLI-verziót (2.0.70-es vagy újabb verzió). Az Azure CLI telepítéséről az [Azure CLI telepítése című](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)témakörben talál további információt.

Új tárfiók létrehozásához hívja meg az az `--enable-files-aadds` storage fiók[létrehozását,](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)és állítsa a tulajdonságot **true**értékre. A következő példában ne felejtse el lecserélni a helyőrző értékeket a saját értékeire. (Ha az előző előnézeti modult használta, a szolgáltatásengedélyezése konstitúdóparamétere **a fájl-aad.)**

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

Ha engedélyezni szeretné ezt a szolgáltatást a meglévő tárfiókokon, használja a következő parancsot:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Most már sikeresen engedélyezte az Azure AD DS-hitelesítést az SMB-n keresztül, és olyan egyéni szerepkört rendelt, amely hozzáférést biztosít egy Azure-fájlmegosztáshoz egy Azure AD-identitással. Ha további felhasználóknak szeretne hozzáférést adni a fájlmegosztáshoz, kövesse a [Hozzáférési engedélyek hozzárendelése](#assign-access-permissions-to-an-identity) az identitás használatához és az [NTFS-engedélyek konfigurálásához az SMB szakaszokon](#configure-ntfs-permissions-over-smb)című útmutatóutasításait.

## <a name="next-steps"></a>További lépések

Az Azure Files szolgáltatásról és az Azure AD SMB-n keresztüli használatáról az alábbi forrásokban talál további információt:

- [Az Azure Files identitásalapú hitelesítési támogatásának áttekintése az SMB-hozzáféréshez](storage-files-active-directory-overview.md)
- [Gyik](storage-files-faq.md)
