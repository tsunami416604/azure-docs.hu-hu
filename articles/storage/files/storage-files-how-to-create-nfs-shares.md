---
title: NFS-megosztás létrehozása – Azure Files
description: Megtudhatja, hogyan hozhat létre olyan Azure-fájlmegosztást, amely a hálózati fájlrendszer protokollal csatlakoztatható.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: c265f404b2e2e2796baf0febb93997ef099f0844
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90564877"
---
# <a name="how-to-create-an-nfs-share"></a>NFS-megosztás létrehozása

Az Azure-fájlmegosztás teljes körűen felügyelt fájlmegosztás, amely a felhőben él. A kiszolgálói üzenetblokk protokoll vagy a hálózati fájlrendszer (NFS) protokoll használatával érhetők el. Ez a cikk az NFS protokollt használó fájlmegosztás létrehozását ismerteti. További információ mindkét protokollról: [Azure fájlmegosztás protokollok](storage-files-compare-protocols.md).

## <a name="limitations"></a>Korlátozások

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Előfeltételek

- Hozzon létre egy [FileStorage-fiókot](storage-how-to-create-premium-fileshare.md).

    > [!IMPORTANT]
    > Az NFS-megosztások csak megbízható hálózatokból érhetők el. Az NFS-megosztással létesített kapcsolatoknak az alábbi források egyikéből kell származnia:

    - [Hozzon létre egy privát végpontot](storage-files-networking-endpoints.md#create-a-private-endpoint) (ajánlott), vagy [korlátozza a hozzáférést a nyilvános végponthoz](storage-files-networking-endpoints.md#restrict-public-endpoint-access).
    - [Pont – hely (P2S) VPN konfigurálása Linux rendszeren a Azure Fileshoz való használathoz](storage-files-configure-p2s-vpn-linux.md).
    - [Helyek közötti VPN konfigurálása Azure Fileshoz való használatra](storage-files-configure-s2s-vpn.md).
    - Konfigurálja a [ExpressRoute](../../expressroute/expressroute-introduction.md).
- Ha az Azure CLI-t szeretné használni, [telepítse a legújabb verziót](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="register-the-nfs-41-protocol"></a>Az NFS 4,1 protokoll regisztrálása

Ha a Azure PowerShell modult vagy az Azure CLI-t használja, regisztrálja a szolgáltatást a következő parancsokkal:

### <a name="powershell"></a>PowerShell

```azurepowershell
Connect-AzAccount
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context
Register-AzProviderFeature -FeatureName AllowNfsFileShares -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az login
az feature register --name AllowNfsFileShares
                    --namespace Microsoft.Storage
                    --subscription <yourSubscriptionIDHere>
az provider register --namespace Microsoft.Storage
```

## <a name="verify-that-the-feature-is-registered"></a>Annak ellenőrzése, hogy a szolgáltatás regisztrálva van-e

A regisztráció jóváhagyása akár egy órát is igénybe vehet. A regisztráció befejezésének ellenőrzéséhez használja a következő parancsokat:

### <a name="powershell"></a>PowerShell

```azurepowershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNfsFileShares
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az feature show --name AllowNfsFileShares --namespace Microsoft.Storage --subscription <yourSubscriptionIDHere>
```

## <a name="create-an-nfs-share"></a>NFS-megosztás létrehozása

# <a name="portal"></a>[Portál](#tab/azure-portal)

Most, hogy létrehozott egy FileStorage-fiókot, és konfigurálta a hálózatot, létrehozhat egy NFS-fájlmegosztást. A folyamat hasonló az SMB-megosztások létrehozásához, ha a megosztás létrehozásakor az **NFS** -t választja az **SMB** helyett.

1. Navigáljon a Storage-fiókjához, és válassza a **fájlmegosztás**lehetőséget.
1. Válassza a **+ fájlmegosztás** lehetőséget egy új fájlmegosztás létrehozásához.
1. Nevezze el a fájlmegosztást, válasszon ki egy kiosztott kapacitást.
1. A **protokollnál** válassza az **NFS (előzetes verzió)** lehetőséget.
1. A **legfelső szintű squash** kiválasztását teszi elérhetővé.

    - Root squash (alapértelmezett) – a távoli adminisztrátor (root) hozzáférése az UID (65534) és a GID (65534) számára van leképezve.
    - Nincs root deverés – a távoli adminisztrátor (root) a root-ként kapja meg a hozzáférést.
    - Minden squash – minden felhasználói hozzáférés az UID (65534) és a GID (65534) számára van leképezve.
    
1. Kattintson a **Létrehozás** gombra.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/create-nfs-file-share.png" alt-text="Képernyőfelvétel a fájlmegosztás létrehozási paneljéről":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Győződjön meg arról, hogy a .NET-keretrendszer telepítve van. Lásd: [.NET-keretrendszer letöltése](https://dotnet.microsoft.com/download/dotnet-framework).
 
1. A következő parancs használatával ellenőrizze, hogy a telepített PowerShell `5.1` -verzió vagy magasabb-e.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   A PowerShell verziójának frissítéséhez lásd: a [meglévő Windows PowerShell frissítése](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
1. Telepítse a PowershellGet modul legújabb verzióját.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

1. Kattintson a Bezárás gombra, majd nyissa meg újra a PowerShell-konzolt.

1. Telepítse az az **. Storage** Preview modul **2.5.2-** es verziójának előzetes verzióját.

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   A PowerShell-modulok telepítésével kapcsolatos további információkért lásd: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)
   
1. Ha prémium szintű fájlmegosztást szeretne létrehozni a Azure PowerShell modullal, használja a [New-AzRmStorageShare](/powershell/module/az.storage/new-azrmstorageshare) parancsmagot.

> [!NOTE]
> A kiosztott megosztási méretek a megosztási kvóta szerint vannak megadva, a fájlmegosztás számlázása a kiosztott méret alapján történik. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/storage/files/) olvasható.

  ```powershell
  New-AzRmStorageShare `
   -ResourceGroupName $resourceGroupName `
   -StorageAccountName $storageAccountName `
   -Name myshare `
   -EnabledProtocol NFS `
   -RootSquash RootSquash `
   -Context $storageAcct.Context
  ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Prémium fájlmegosztás Azure CLI-vel való létrehozásához használja az az [Storage Share Create](/cli/azure/storage/share-rm) parancsot.

> [!NOTE]
> A kiosztott megosztási méretek a megosztási kvóta szerint vannak megadva, a fájlmegosztás számlázása a kiosztott méret alapján történik. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/storage/files/) olvasható.

```azurecli-interactive
az storage share-rm create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --enabled-protocol NFS \
    --root-access RootSquash \
    --name "myshare" 
```
---

## <a name="next-steps"></a>További lépések

Most, hogy létrehozott egy NFS-megosztást, a használatához csatlakoztatnia kell azt a Linux-ügyfélen. Részletekért lásd: [NFS-megosztás csatlakoztatása](storage-files-how-to-mount-nfs-shares.md).
