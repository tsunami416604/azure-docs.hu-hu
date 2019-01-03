---
title: Az Azure Storage-ban biztonságos átvitel megkövetelése |} A Microsoft Docs
description: További információ az Azure Storage és engedélyezését a "Biztonságos átvitelre van szükség" funkciót.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 06/20/2017
ms.author: fryu
ms.component: common
ms.openlocfilehash: 6f17cb4540feea6400c60c8032d071d6a922c871
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53633061"
---
# <a name="require-secure-transfer-in-azure-storage"></a>Az Azure Storage-ban biztonságos átvitel megkövetelése

A "Biztonságos átvitelre van szükség" beállítás csak a fiók teszi kérelmek biztonságos kapcsolatok növeli a storage-fiók biztonságát. Például meghívásakor REST API-k, a tárfiók eléréséhez, akkor csatlakoznia kell HTTPS-en keresztül. "A biztonságos átvitelre van szükség" elutasítja a HTTP-kérelmeket.

Az Azure Files szolgáltatást használja, minden olyan kapcsolat, titkosítás nélkül sikertelen lesz, ha a "Biztonságos átvitelre van szükség" engedélyezve van. Ez magában foglalja a forgatókönyvek, amelyek használják az egyes verziói a Linuxos SMB-ügyfél, SMB 2.1 és az SMB 3.0 titkosítás nélkül. 

Alapértelmezés szerint a "Biztonságos átvitelre van szükség" lehetőség le van tiltva az SDK-val egy storage-fiók létrehozásakor. És ez alapértelmezés szerint engedélyezve van az Azure Portal tárfiók létrehozásakor.

> [!NOTE]
> Azure Storage nem támogatja a HTTPS az egyéni tartománynevek, mert a rendszer nem alkalmazza ezt a beállítást, ha egy egyéni tartománynevet használ. És a klasszikus tárfiókok nem támogatottak.

## <a name="enable-secure-transfer-required-in-the-azure-portal"></a>Engedélyezze a "Biztonságos átvitelre van szükség" az Azure Portalon

A "biztonságos átvitelre van szükség" beállítás a tárfiók létrehozásakor bekapcsolhatja a [az Azure portal](https://portal.azure.com). Ön is engedélyezheti azt a meglévő tárfiókok.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Egy új storage-fiók biztonságos átvitel megkövetelése

1. Nyissa meg a **storage-fiók létrehozása** panel az Azure Portalon.
1. A **biztonságos átvitelre van szükség**válassza **engedélyezve**.

  ![Tárfiók panel létrehozása](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Egy meglévő tárfiókot a biztonságos átvitel megkövetelése

1. Válasszon egy meglévő tárfiókot az Azure Portalon.
1. A Storage-fiók menü ablaktáblán alatt **beállítások**válassza **konfigurációs**.
1. A **biztonságos átvitelre van szükség**válassza **engedélyezve**.

  ![Tárolási fiók menü panel](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="enable-secure-transfer-required-programmatically"></a>Engedélyezze a "Biztonságos átvitelre van szükség" programozott módon

Biztonságos átvitel megkövetelése programozott módon, használja a beállítást _supportsHttpsTrafficOnly_ a REST API, eszközök és kódtárak a tárfiók tulajdonságai:

* [REST API-val](https://docs.microsoft.com/rest/api/storagerp/storageaccounts) (verzió: 2016-12-01)
* [PowerShell](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) (verzió: 0,7)
* [Parancssori felület](https://pypi.python.org/pypi/azure-cli-storage/2.0.11) (verzió: 2.0.11)
* [NodeJS](https://www.npmjs.com/package/azure-arm-storage/) (verzió: 1.1.0-s)
* [.NET SDK-val](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/6.3.0-preview) (verzió: 6.3.0-s verzióra frissült)
* [Python SDK](https://pypi.python.org/pypi/azure-mgmt-storage/1.1.0) (verzió: 1.1.0-s)
* [Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage) (verzió: 0.11.0)

### <a name="enable-secure-transfer-required-setting-with-powershell"></a>Engedélyezze a "Biztonságos átvitelre van szükség" beállítása a PowerShell-lel

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ez a példa az Azure PowerShell-modul Az 0,7 vagy újabb verziója szükséges. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket.

Futtassa a `Connect-AzAccount` parancsot, hogy kapcsolatot hozzon létre az Azure-ral.

 Beállítás ellenőrzéséhez a következő parancsot használja:

```powershell
> Get-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

A beállításnak az engedélyezéséhez a következő parancsot használja:

```powershell
> Set-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

### <a name="enable-secure-transfer-required-setting-with-cli"></a>Engedélyezze a "Biztonságos átvitel szükséges" beállítást a CLI használatával

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 Beállítás ellenőrzéséhez a következő parancsot használja:

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

## <a name="next-steps"></a>További lépések
Az Azure Storage biztonsági képességeket, amelyek együtt lehetővé teszik a fejlesztők számára biztonságos alkalmazások széles választékát nyújtja. További részletekért látogasson el a [Storage biztonsági útmutatóját](storage-security-guide.md).
