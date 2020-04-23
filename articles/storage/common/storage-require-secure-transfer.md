---
title: Biztonságos átvitel megkövetelése a biztonságos kapcsolatok biztosításához
titleSuffix: Azure Storage
description: Ismerje meg, hogyan követelheti meg a biztonságos átvitelt az Azure Storage-ba irányuló kérések hez. Ha egy tárfiók biztonságos átvitelét igényli, a rendszer elutasítja a nem biztonságos kapcsolatból származó kérelmeket.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 125f4188ed3f12f366c619af9efe3aa203987c19
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870517"
---
# <a name="require-secure-transfer-to-ensure-secure-connections"></a>Biztonságos átvitel megkövetelése a biztonságos kapcsolatok biztosításához

Beállíthatja, hogy a tárfiók csak a tárfiók **biztonságos átvitelhez szükséges** tulajdonságának beállításával fogadja a biztonságos kapcsolatokból érkező kérelmeket. Ha biztonságos átvitelre van szüksége, a rendszer elutasítja a nem biztonságos kapcsolatból származó kérelmeket. A Microsoft azt javasolja, hogy mindig szükség van a biztonságos átvitel az összes tárfiókok.

Ha biztonságos átvitelre van szükség, az Azure Storage REST API-műveletet https-en keresztül kell meghívni. A HTTP-n keresztül benyújtott kérelmeket a rendszer elutasítja.

Az Azure File share-hez titkosítás nélkül iMb-n keresztüli csatlakozás sikertelen, ha a tárfiókhoz biztonságos átvitelszükséges. A nem biztonságos kapcsolatok közé tartoznak például az SMB 2.1, az SMB 3.0 titkosítás nélküli verziójai vagy a Linux SMB-ügyfél egyes verziói.

Alapértelmezés szerint a **Biztonságos átvitel szükséges** tulajdonság engedélyezve van, amikor létrehoz egy tárfiókot.

> [!NOTE]
> Mivel az Azure Storage nem támogatja a HTTPS egyéni tartománynevek, ez a beállítás nem alkalmazható, ha egyéni tartománynevet használ. És a klasszikus tárfiókok nem támogatottak.

## <a name="require-secure-transfer-in-the-azure-portal"></a>Biztonságos átvitel megkövetelése az Azure Portalon

A **biztonságos átvitelszükséges** tulajdonságot bekapcsolhatja, amikor az [Azure Portalon](https://portal.azure.com)hoz létre tárfiókot. Azt is engedélyezheti a meglévő tárfiókok.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Biztonságos átvitel megkövetelése új tárfiókhoz

1. Nyissa meg a **Tárfiók létrehozása** ablaktáblát az Azure Portalon.
1. A **Biztonságos átvitel szükséges területen**válassza az **Engedélyezve**lehetőséget.

   ![Tárfiók létrehozása panel](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Biztonságos átvitel megkövetelése meglévő tárfiókhoz

1. Válasszon ki egy meglévő tárfiókot az Azure Portalon.
1. A Tárfiók menüablaktábláján a **BEÁLLÍTÁSOK csoportban**válassza a **Konfiguráció lehetőséget.**
1. A **Biztonságos átvitel szükséges területen**válassza az **Engedélyezve**lehetőséget.

   ![A Tárfiók menüablaka](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="require-secure-transfer-from-code"></a>Biztonságos átvitel megkövetelése a kódból

A biztonságos átvitel megköveteléséhez programozott módon állítsa be a _supportsHttpsTrafficOnly_ tulajdonságot a tárfiókban. Ezt a tulajdonságot a Storage Resource Provider REST API, az ügyféltárak és az eszközök használatával állíthatja be:

* [REST API](/rest/api/storagerp/storageaccounts)
* [PowerShell](/powershell/module/az.storage/set-azstorageaccount)
* [parancssori felület](/cli/azure/storage/account)
* [Nodejs](https://www.npmjs.com/package/azure-arm-storage/)
* [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage)
* [Python SDK](https://pypi.org/project/azure-mgmt-storage)
* [Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage)

## <a name="require-secure-transfer-with-powershell"></a>Biztonságos átvitel megkövetelése a PowerShellhasználatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ez a minta az Azure PowerShell-modul Az 0.7-es vagy újabb verzióját igényli. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket.

Futtassa a `Connect-AzAccount` parancsot, hogy kapcsolatot hozzon létre az Azure-ral.

 A beállítás ellenőrzéséhez használja a következő parancssort:

```powershell
Get-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

A beállítás engedélyezéséhez használja a következő parancssort:

```powershell
Set-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

## <a name="require-secure-transfer-with-azure-cli"></a>Biztonságos átvitel megkövetelése az Azure CLI-vel

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 A beállítás ellenőrzéséhez használja a következő parancsot:

```azurecli-interactive
az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

A beállítás engedélyezéséhez használja a következő parancsot:

```azurecli-interactive
az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>További lépések

[Biztonsági javaslatok a Blob storage-hoz](../blobs/security-recommendations.md)
