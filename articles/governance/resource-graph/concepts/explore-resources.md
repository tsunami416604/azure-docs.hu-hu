---
title: Az Azure-erőforrások megtekintése
description: Ismerje meg az Erőforrás-grafikon lekérdezési nyelv segítségével az erőforrások vizsgálata, és Fedezze fel, hogyan vannak csatlakoztatva.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/23/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 0b4a75558f5e82b707ae5d012acef4d2c5c4b7a0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64723808"
---
# <a name="explore-your-azure-resources-with-resource-graph"></a>Az Azure-erőforrások felfedezése a Resource Graph használatával

Az Azure Erőforrás-grafikon lehetővé teszi a vizsgálata, és Fedezze fel az Azure-erőforrások gyors és méretezhető. Fejthetők vissza a gyors válaszokat, egy remek lehetőséget nyújt a környezet és az Azure-erőforrások alkotó tulajdonságaival kapcsolatos tudnivalók.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="explore-virtual-machines"></a>A virtual machines felfedezése

Az Azure-ban egy közös erőforráshoz egy virtuális gépet. Virtuális gépek egy erőforrás típusa van, hogy lekérdezhetők legyenek sok tulajdonságot. Minden egyes tulajdonság a szűrés vagy pontosan a keresett erőforrás keresése lehetőséget biztosít.

### <a name="virtual-machine-discovery"></a>Virtuális gép felderítése

Kezdjük az egyszerű lekérdezés egyetlen virtuális Gépet a környezetre, és tekintse meg a visszaadott tulajdonságait.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1" | ConvertTo-Json -Depth 100
```

> [!NOTE]
> Az Azure PowerShell `Search-AzGraph` parancsmag adja vissza egy **PSCustomObject** alapértelmezés szerint. A kimenet néznek ki, mi az Azure CLI-t, adja vissza, hogy a `ConvertTo-Json` parancsmag használható. Az alapértelmezett érték a **mélysége** van _2_. Értékre állítaná _100_ minden visszaadott szintek kell konvertálni.

A JSON-eredményeket struktúrája a következő példához hasonló:

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1",
    "plan": {},
    "properties": {
      "hardwareProfile": {
        "vmSize": "Standard_B2s"
      },
      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "/subscriptions/<subscriptionId>/MyResourceGroup/providers/Microsoft.Network/networkInterfaces/contosovm1535",
            "resourceGroup": "MyResourceGroup"
          }
        ]
      },
      "osProfile": {
        "adminUsername": "localAdmin",
        "computerName": "ContosoVM1",
        "secrets": [],
        "windowsConfiguration": {
          "enableAutomaticUpdates": true,
          "provisionVMAgent": true
        }
      },
      "provisioningState": "Succeeded",
      "storageProfile": {
        "dataDisks": [],
        "imageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2016-Datacenter",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": 127,
          "managedDisk": {
            "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
            "resourceGroup": "MyResourceGroup",
            "storageAccountType": "Premium_LRS"
          },
          "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
          "osType": "Windows"
        }
      },
      "vmId": "bbb9b451-6dc7-4117-bec5-c971eb1118c6"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {},
    "subscriptionId": "<subscriptionId>",
    "tags": {},
    "type": "microsoft.compute/virtualmachines"
  }
]
```

A Tulajdonságok mondja el, további információ a virtuális gép típusú erőforrást, a Termékváltozat, az operációs rendszer, lemezek, címkék, és az erőforráscsoportban és előfizetésben tagja.

### <a name="virtual-machines-by-location"></a>Virtuális gépek hely szerint

Tart a virtuális gépek erőforrásra vonatkozó tanultakat, használja a **hely** tulajdonságot az összes virtuális gép hely alapján. Frissítse a lekérdezést, azt fogja távolítsa el a korlátot, és összefoglalója hely értékek száma.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by location
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

A JSON-eredményeket struktúrája a következő példához hasonló:

```json
[
  {
    "count_": 386,
    "location": "eastus"
  },
  {
    "count_": 215,
    "location": "southcentralus"
  },
  {
    "count_": 59,
    "location": "westus"
  }
]
```

Ekkor láthatja, hogy hány virtuális gépet minden egyes Azure-régióban van.

### <a name="virtual-machines-by-sku"></a>Termékváltozat által a virtuális gépek

Az eredeti virtuális gép tulajdonságainak visszatérve próbáljon található összes virtuális gépet, amely a Termékváltozat mérete **Standard_B2s**. A visszaadott JSON-t keresi, láthatjuk, hogy a tárolt **properties.hardwareprofile.vmsize**. Frissítjük a lekérdezésben található összes virtuális gépet, amely a méretét, és csak a virtuális gép és a régió nevét adja vissza.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| project name, resourceGroup"
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

### <a name="virtual-machines-connected-to-premium-managed-disks"></a>Virtuális gépek csatlakozni a prémium szintű managed Disks szolgáltatásba

Ha szerettünk volna a részletek a prémium szintű felügyelt lemezek ezek csatolt **Standard_B2s** virtuális gépeket, hogy kibontásával, a lekérdezést, hogy ossza meg velünk a felügyelt lemez az erőforrás-azonosítója.

```kusto
where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| extend disk = properties.storageProfile.osDisk.managedDisk
| where disk.storageAccountType == 'Premium_LRS'
| project disk.id
```

> [!NOTE]
> Egy másik módja a Termékváltozat használatával lett volna a **aliasok** tulajdonság **Microsoft.Compute/virtualMachines/sku.name**. Tekintse meg a [aliasok megjelenítése](../samples/starter.md#show-aliases) és [eltérő alias értékek megjelenítése](../samples/starter.md#distinct-alias-values) példákat.

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

```azurepowershell-interactive
  Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

Ez a lemez azonosítók listáját.

### <a name="managed-disk-discovery"></a>Felügyelt lemez felderítése

Az első rekordja az előző lekérdezést azt vizsgáljuk meg a Tulajdonságok léteznek, a felügyelt lemez, amely az első virtuális gépen lett csatolva. A frissített lekérdezés a lemez Azonosítóját használja, és módosítja a típusát.

Például, például az előző lekérdezés kimenete:

```json
[
  {
    "disk_id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166"
  }
]
```

```kusto
where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'
```

A lekérdezés futtatása előtt hogyan tette azt, hogy a **típus** kell **Microsoft.Compute/disks**?
Ha a teljes azonosító tekinti meg, látni fogja **/providers/Microsoft.Compute/disks/** karakterlánc részeként. Ez a karakterlánc töredék feltárhatja, hogy milyen kereséséhez írjon be egy mutatót biztosítja. Egy alternatív módszer lenne, távolítsa el a korlát típusa szerint, és inkább csak keresési azonosító mező szerint. Mivel az azonosító egyedi, csak egy olyan rekorddal kellene visszaadnia és a **típus** tulajdonság rá, hogy részletesen.

> [!NOTE]
> Ebben a példában működéséhez le kell cserélnie az azonosító mező az eredmény a saját környezetének.

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

A JSON-eredményeket struktúrája a következő példához hasonló:

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "plan": {},
    "properties": {
      "creationData": {
        "createOption": "Empty"
      },
      "diskSizeGB": 127,
      "diskState": "ActiveSAS",
      "provisioningState": "Succeeded",
      "timeCreated": "2018-09-14T12:17:32.2570000Z"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {
      "name": "Premium_LRS",
      "tier": "Premium"
    },
    "subscriptionId": "<subscriptionId>",
    "tags": {
      "environment": "prod"
    },
    "type": "microsoft.compute/disks"
  }
]
```

## <a name="explore-virtual-machines-to-find-public-ip-addresses"></a>Ismerje meg a virtuális gépek nyilvános IP-címek keresése

Az Azure CLI-vel lekérdezések először megkeresi és tárolja a hálózati adapterek (NIC) erőforrások virtuális gépekhez csatlakoztatott összes. Ezután minden IP-cím erőforrás, amely egy nyilvános IP-cím megállapításához használja a hálózati adapterek listáját, és tárolja ezeket az értékeket. Végül biztosít a nyilvános IP-címek listáját.

```azurecli-interactive
# Use Resource Graph to get all NICs and store in the 'nic' variable
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20" --output table | tail -n +3 > nics.txt

# Review the output of the query stored in 'nics.txt'
cat nics.txt
```

Használja a `nics.txt` fájlt a következő lekérdezésben kérheti le a kapcsolódó hálózati adapter erőforrások adatait, ha egy nyilvános IP-cím csatolt hálózati adaptereken.

```azurecli-interactive
# Use Resource Graph with the 'nics.txt' file to get all related public IP addresses and store in 'publicIp.txt' file
az graph query -q="where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$(awk -vORS="','" '{print $0}' nics.txt | sed 's/,$//')') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp" --output table | tail -n +3 > ips.txt

# Review the output of the query stored in 'ips.txt'
cat ips.txt
```

Utolsó, használja a nyilvános IP-cím erőforrások listáját tárolja `ips.txt` tényleges nyilvános IP-cím lekérése és megjelenítésére.

```azurecli-interactive
# Use Resource Graph with the 'ips.txt' file to get the IP address of the public IP address resources
az graph query -q="where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$(awk -vORS="','" '{print $0}' ips.txt | sed 's/,$//')') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip" --output table
```

## <a name="next-steps"></a>További lépések

- További információk a [lekérdezés nyelvéről](query-language.md)
- Tekintse meg a használt nyelv [alapszintű lekérdezések](../samples/starter.md)
- Tekintse meg a speciális használ [összetettebb lekérdezésekhez](../samples/advanced.md)