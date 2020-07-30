---
title: Azure CLI – a felügyelt lemezekre vonatkozó importálási/exportálási hozzáférés korlátozása privát hivatkozásokkal (előzetes verzió)
description: Privát hivatkozások (előzetes verzió) engedélyezése a felügyelt lemezekhez az Azure CLI-vel. Lehetővé teszi, hogy biztonságosan exportálja és importálja a lemezeket csak a virtuális hálózaton belül.
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 07/15/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 5df11e704987098d61ced7afbff5e6234d4d5f04
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420284"
---
# <a name="azure-cli---restrict-importexport-access-for-managed-disks-with-private-links-preview"></a>Azure CLI – a felügyelt lemezek importálási/exportálási hozzáférésének korlátozása privát hivatkozásokkal (előzetes verzió)

A [privát végpontok](../../private-link/private-endpoint-overview.md) (előzetes verzió) használatával korlátozhatja a felügyelt lemezek exportálását és importálását, valamint az adatok biztonságos elérését az Azure-beli virtuális hálózatban lévő ügyfelektől származó [privát kapcsolaton](../../private-link/private-link-overview.md) keresztül. A magánhálózati végpont IP-címet használ a Managed Disks szolgáltatáshoz tartozó virtuális hálózati címtartomány alapján. A virtuális hálózat és a felügyelt lemezek ügyfelei közötti hálózati forgalom áthalad a virtuális hálózaton és a Microsoft gerinc hálózatán lévő privát kapcsolaton, ami kiküszöböli a nyilvános internetről való kitettséget. 

Ha privát hivatkozásokat szeretne használni a felügyelt lemezek exportálásához/importálásához, először hozzon létre egy lemez-hozzáférési erőforrást, és csatolja azt egy adott előfizetésben található virtuális hálózathoz egy privát végpont létrehozásával. Ezután rendeljen hozzá egy lemezt vagy egy pillanatképet a lemezes hozzáférés egy példányához. Végül állítsa be a lemez NetworkAccessPolicy tulajdonságát vagy a pillanatképet a következőre: `AllowPrivate` . Ez korlátozni fogja a virtuális hálózat elérését. 

A NetworkAccessPolicy tulajdonságot beállíthatja úgy `DenyAll` , hogy megakadályozza, hogy bárki exportálja egy lemez vagy pillanatkép adatait. A NetworkAccessPolicy tulajdonság alapértelmezett értéke: `AllowAll` .

## <a name="limitations"></a>Korlátozások

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../../includes/virtual-machines-disks-private-links-limitations.md)]

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

[!INCLUDE [virtual-machines-disks-private-links-regions](../../../includes/virtual-machines-disks-private-links-regions.md)]

## <a name="log-in-into-your-subscription-and-set-your-variables"></a>Jelentkezzen be az előfizetésbe, és állítsa be a változókat

```azurecli-interactive

subscriptionId=yourSubscriptionId
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessForPrivateLinks
vnetName=yourVNETForPrivateLinks
subnetName=yourSubnetForPrivateLinks
privateEndPointName=yourPrivateLinkForSecureMDExportImport
privateEndPointConnectionName=yourPrivateLinkConnection

#The name of an existing disk which is the source of the snapshot
sourceDiskName=yourSourceDiskForSnapshot

#The name of the new snapshot which will be secured via Private Links
snapshotNameSecuredWithPL=yourSnapshotNameSecuredWithPL

az login

az account set --subscription $subscriptionId

```

## <a name="create-a-disk-access-using-azure-cli"></a>Lemezes hozzáférés létrehozása az Azure CLI-vel
```azurecli-interactive
az group deployment create -g $resourceGroupName \
--template-uri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/privatelinks/CreateDiskAccess.json" \
--parameters "region=$region" "diskAccessName=$diskAccessName"

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)
```

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

A hálózati házirendek, például a hálózati biztonsági csoportok (NSG) nem támogatottak privát végpontok esetén. Ahhoz, hogy egy privát végpontot helyezzen üzembe egy adott alhálózaton, az adott alhálózaton explicit letiltási beállításra van szükség. 

```azurecli-interactive
az network vnet create --resource-group $resourceGroupName \
    --name $vnetName \
    --subnet-name $subnetName
```
## <a name="disable-subnet-private-endpoint-policies"></a>Alhálózat magánhálózati végponti házirendjeinek letiltása

Az Azure egy virtuális hálózaton belüli alhálózatra helyezi az erőforrásokat, ezért a magánhálózati végpontok hálózati házirendjeinek letiltásához frissítenie kell az alhálózatot. 

```azurecli-interactive
az network vnet subnet update --resource-group $resourceGroupName \
    --name $subnetName  \
    --vnet-name $vnetName \
    --disable-private-endpoint-network-policies true
```
## <a name="create-a-private-endpoint-for-the-disk-access-object"></a>Magánhálózati végpont létrehozása a lemez-hozzáférési objektumhoz

```azurecli-interactive
az network private-endpoint create --resource-group $resourceGroupName \
    --name $privateEndPointName \
    --vnet-name $vnetName  \
    --subnet $subnetName \
    --private-connection-resource-id $diskAccessId \
    --group-ids disks \
    --connection-name $privateEndPointConnectionName
```

## <a name="configure-the-private-dns-zone"></a>A saját DNS zóna konfigurálása

Hozzon létre egy saját DNS zónát a Storage blob tartományhoz, hozzon létre egy társítási hivatkozást a Virtual Network, és hozzon létre egy DNS-zónát, amely a privát végpontot a saját DNS zónához társítja. 

```azurecli-interactive
az network private-dns zone create --resource-group $resourceGroupName \ 
    --name "privatelink.blob.core.windows.net"

az network private-dns link vnet create --resource-group $resourceGroupName \
    --zone-name "privatelink.blob.core.windows.net" \
    --name yourDNSLink \
    --virtual-network $vnetName \
    --registration-enabled false 

az network private-endpoint dns-zone-group create \
   --resource-group $resourceGroupName \
   --endpoint-name $privateEndPointName \
   --name yourZoneGroup \
   --private-dns-zone "privatelink.blob.core.windows.net" \
   --zone-name disks
```

## <a name="create-a-disk-protected-with-private-links"></a>Privát hivatkozásokkal védett lemez létrehozása
  ```cli
    resourceGroupName=yourResourceGroupName
    region=northcentralus
    diskAccessName=yourDiskAccessName
    diskName=yourDiskName
    diskSkuName=Standard_LRS
    diskSizeGB=128

    diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)

    az group deployment create -g $resourceGroupName \
       --template-uri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/privatelinks/CreateDisksWithExportViaPrivateLink.json" \
       --parameters "diskName=$diskName" \
       "diskSkuName=$diskSkuName" \
       "diskSizeGB=$diskSizeGB" \
       "diskAccessId=$diskAccessId" \
       "region=$region" \
       "networkAccessPolicy=AllowPrivate"
```

## <a name="create-a-snapshot-of-a-disk-protected-with-private-links"></a>Privát hivatkozásokkal védett lemez pillanatképének létrehozása
   ```cli
    resourceGroupName=yourResourceGroupName
    region=northcentralus
    diskAccessName=yourDiskAccessName
    sourceDiskName=yourSourceDiskForSnapshot
    snapshotNameSecuredWithPL=yourSnapshotName

    diskId=$(az disk show -n $sourceDiskName -g $resourceGroupName --query [id] -o tsv)
   
    diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)
   
    az group deployment create -g $resourceGroupName \
      --template-uri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/privatelinks/CreateSnapshotWithExportViaPrivateLink.json" \
      --parameters "snapshotName=$snapshotNameSecuredWithPL" \
      "sourceResourceId=$diskId" \
      "diskAccessId=$diskAccessId" \
      "region=$region" \
      "networkAccessPolicy=AllowPrivate" 
```

## <a name="next-steps"></a>Következő lépések

- [GYIK a privát hivatkozásokról](faq-for-disks.md#private-links-for-securely-exporting-and-importing-managed-disks)
- [Felügyelt pillanatképek a CLI használatával történő exportálása/másolása virtuális merevlemezként egy másik régióban lévő tárfiókba](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd.md)
