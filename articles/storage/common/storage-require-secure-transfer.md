---
title: "Az Azure Storage biztonságos átvitelét igénylő |} Microsoft Docs"
description: "Ismerje meg a \"Biztonságos átviteli szükséges\" funkció Azure Storage és az engedélyezéshez."
services: storage
documentationcenter: na
author: fhryo-msft
manager: Jason.Hogg
editor: fhryo-msft
ms.assetid: 
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 06/20/2017
ms.author: fryu
ms.openlocfilehash: 1bb87cf3e37e486f9a03da43df652442c19fd218
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="require-secure-transfer-in-azure-storage"></a>Biztonságos átvitele az Azure Storage megkövetelése

A "Biztonságos szükséges átviteli" beállítás növeli a a storage-fiókjának biztonságát azzal, hogy csak kérésének engedélyezése a fiókhoz a biztonságos kapcsolatokat. Például ha a tárfiók eléréséhez REST API-hívás, csatlakoznia kell HTTPS protokoll használatával. "Biztonságos szükséges átviteli" HTTP használata kérelmeket visszautasítja.

Az Azure-fájlok szolgáltatását használja, bármilyen titkosítás nélküli kapcsolatot sikertelen lesz, ha engedélyezve van a "Biztonságos szükséges átviteli". Ez magában foglalja az SMB 2.1, SMB 3.0-titkosítás nélkül és a Linux SMB-ügyfél az egyes verziói használó forgatókönyvek. 

Alapértelmezés szerint a "Biztonságos szükséges átviteli" beállítás le van tiltva.

> [!NOTE]
> Azure Storage az egyéni tartománynevek nem támogatja a HTTPS PROTOKOLLT, mert ez a beállítás nem lesz alkalmazva, amikor egy egyéni tartománynevet használja. És a klasszikus tárfiókokat nem támogatottak.

## <a name="enable-secure-transfer-required-in-the-azure-portal"></a>Engedélyezze a "Biztonságos átviteli szükséges" az Azure-portálon

A "biztonságos továbbítás szükséges" beállítást, ha a tárfiók létrehozása bekapcsolása a [Azure-portálon](https://portal.azure.com). Engedélyezheti azt a meglévő tárfiókok.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Új tárfiók biztonságos átvitele kérése

1. Nyissa meg a **storage-fiók létrehozása** ablaktáblán az Azure portálon.
1. A **szükséges átviteli biztonságos**, jelölje be **engedélyezve**.

  ![Storage-fiók panelen létrehozása](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Meglévő tárfiók biztonságos átvitele kérése

1. Válassza ki a meglévő tárfiókot az Azure portálon.
1. A tárolóban lévő fiók menü ablaktáblán, a **beállítások**, jelölje be **konfigurációs**.
1. A **szükséges átviteli biztonságos**, jelölje be **engedélyezve**.

  ![Tárolási fiók menü ablaktábla](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="enable-secure-transfer-required-programmatically"></a>Engedélyezze a "Biztonságos szükséges átviteli" programozott módon

Kötelező a biztonságos átvitele programozott módon, a beállítást használják _supportsHttpsTrafficOnly_ a REST API-t, az eszközök vagy a könyvtárak a tárfiók tulajdonságai:

* [REST API](https://docs.microsoft.com/en-us/rest/api/storagerp/storageaccounts) (verzió: 2016-12-01)
* [PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.storage/set-azurermstorageaccount?view=azurermps-4.1.0) (verzió: 4.1.0)
* [Parancssori felület](https://pypi.python.org/pypi/azure-cli-storage/2.0.11) (verzió: 2.0.11)
* [NodeJS](https://www.npmjs.com/package/azure-arm-storage/) (verzió: 1.1.0-ás)
* [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/6.3.0-preview) (verzió: 6.3.0)
* [Python SDK](https://pypi.python.org/pypi/azure-mgmt-storage/1.1.0) (verzió: 1.1.0-ás)
* [Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage) (verzió: 0.11.0)

### <a name="enable-secure-transfer-required-setting-with-powershell"></a>Engedélyezze a "Biztonságos továbbítás szükséges" beállítást a PowerShell használatával

Ez a minta az Azure PowerShell 4.1-es vagy újabb verziója szükséges. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

Futtatás `Login-AzureRmAccount` kapcsolat létrehozása az Azure-ral.

 A beállítás ellenőrzéséhez a következő parancsot használja:

```powershell
> Get-AzureRmStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

A beállításnak az engedélyezéséhez a következő parancsot használja:

```powershell
> Set-AzureRmStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

### <a name="enable-secure-transfer-required-setting-with-cli"></a>Engedélyezze a "Biztonságos továbbítás szükséges" beállítás a parancssori felület

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 A beállítás ellenőrzéséhez a következő parancsot használja:

```azurecli-interactive
> az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

A beállításnak az engedélyezéséhez a következő parancsot használja:

```azurecli-interactive
> az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>Következő lépések
Az Azure Storage biztonsági képességeket, amelyek együtt lehetővé teszik a fejlesztők számára a biztonságos alkalmazások széles választékát nyújtja. További részletekért látogasson el a [tárolási biztonsági útmutatója](storage-security-guide.md).
