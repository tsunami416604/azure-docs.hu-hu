---
title: Az Azure-erőforrások áttekintése
description: Ismerje meg, hogyan használhatja az Erőforrásgráf lekérdezési nyelvet az erőforrások feltárásához és a kapcsolódásuk megismeréséhez.
ms.date: 10/18/2019
ms.topic: conceptual
ms.openlocfilehash: 0c191915b8c558d80ffef554ef758a35157e035c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76156981"
---
# <a name="explore-your-azure-resources-with-resource-graph"></a>Az Azure-erőforrások felfedezése a Resource Graph használatával

Az Azure Resource Graph lehetővé teszi az Azure-erőforrások gyors és nagy méretű felfedezését és felderítését. A gyors válaszokra tervezett megoldással nagyszerű enmegismerheti a környezetét és az Azure-erőforrásokat kikészítő tulajdonságokat is.

## <a name="explore-virtual-machines"></a>Virtuális gépek felfedezése

Az Azure-ban egy közös erőforrás egy virtuális gép. Erőforrástípusként a virtuális gépek számos lekérdezhető tulajdonsággal rendelkeznek. Minden tulajdonság lehetőséget biztosít a keresett erőforrás pontos szűrésére vagy megkeresésére.

### <a name="virtual-machine-discovery"></a>Virtuális gép felderítése

Kezdjük egy egyszerű lekérdezést egyetlen virtuális gép bekéseléséhez a környezetünkből, és tekintse meg a visszaadott tulajdonságokat.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1" | ConvertTo-Json -Depth 100
```

> [!NOTE]
> Az Azure `Search-AzGraph` PowerShell-parancsmag alapértelmezés szerint **pscustomobject objektumot** ad vissza. Ahhoz, hogy a kimenet ugyanúgy néz ki, `ConvertTo-Json` mint amit az Azure CLI visszaad, a parancsmag használatos. A **Mélység** alapértelmezett értéke _2_. Ha _100-ra_ állítja be, az összes visszaadott szintet konvertálnia kell.

A JSON-eredmények a következő példához hasonló szerkezetűek:

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

A tulajdonságok további információkat a virtuálisgép-erőforrás magáról, mindent Termékváltozat, operációs rendszer, lemezek, címkék és az erőforráscsoport és az előfizetés, amelynek tagja.

### <a name="virtual-machines-by-location"></a>Virtuális gépek hely szerint

Figyelembe véve, amit megtudtunk a virtuális gépek erőforrás, használjuk a **hely** tulajdonság ot az összes virtuális gép hely szerint történő megszámlálásához. A lekérdezés frissítéséhez eltávolítjuk a korlátot, és összegezzük a helyértékek számát.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by location
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

A JSON-eredmények a következő példához hasonló szerkezetűek:

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

Most már láthatja, hogy az egyes Azure-régiókban hány virtuális gép található.

### <a name="virtual-machines-by-sku"></a>Virtuális gépek a Termékváltozattól

Visszatérve az eredeti virtuálisgép-tulajdonságokra, próbáljuk meg megtalálni az összes olyan virtuális gépet, amelynek termékváltozatának **Standard_B2s.** A visszaadott JSON-t tekintve azt látjuk, hogy a **properties.hardwareprofile.vmsize helyen tárolódik.** Frissítjük a lekérdezést, hogy megtalálja az összes virtuális gépet, amely megfelel ennek a méretnek, és csak a virtuális gép és a régió nevét adja vissza.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| project name, resourceGroup
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

### <a name="virtual-machines-connected-to-premium-managed-disks"></a>Prémium szintű felügyelt lemezekhez csatlakoztatott virtuális gépek

Ha meg akartuk kapni a Standard_B2s **virtuális** gépekhez csatlakoztatott prémium szintű felügyelt lemezek adatait, kibonthatjuk a lekérdezést, hogy megadjuk a felügyelt lemezek erőforrás-azonosítóját.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| extend disk = properties.storageProfile.osDisk.managedDisk
| where disk.storageAccountType == 'Premium_LRS'
| project disk.id
```

> [!NOTE]
> Egy másik módja annak, hogy a Termékváltozat lett volna az **aliasok** tulajdonság **Microsoft.Compute/virtualMachines/sku.name**. Tekintse meg az [Aliasok megjelenítése](../samples/starter.md#show-aliases) és [a Különböző aliasértékek megjelenítése](../samples/starter.md#distinct-alias-values) példákat.

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

Az eredmény a lemezazonosítók listája.

### <a name="managed-disk-discovery"></a>Felügyelt lemezfelderítés

Az előző lekérdezés első rekordjával megismerjük az első virtuális géphez csatlakoztatott felügyelt lemezen található tulajdonságokat. A frissített lekérdezés a lemezazonosítót használja, és módosítja a típust.

Példa kimenetre az előző lekérdezésből, például:

```json
[
  {
    "disk_id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166"
  }
]
```

```kusto
Resources
| where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'
```

A lekérdezés futtatása előtt honnan tudtuk, hogy a **típusnak** most **Microsoft.Compute/disks-nek**kell lennie?
Ha megnézi a teljes azonosítót, a **/providers/Microsoft.Compute/disks/** a karakterlánc részeként jelenik meg. Ez a karakterlánc-töredék ad egy tippet, hogy milyen típusú keresni. Egy másik módszer az lenne, hogy távolítsa el a határértéket típus szerint, és ehelyett csak a keresés az azonosító mezőben. Mivel az azonosító egyedi, csak egy rekordot ad **vissza,** és a típus tulajdonság a rajta biztosítja ezt a részletet.

> [!NOTE]
> Ahhoz, hogy ez a példa működjön, le kell cserélnie az azonosító mezőt a saját környezetéből származó eredményre.

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

A JSON-eredmények a következő példához hasonló szerkezetűek:

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

## <a name="explore-virtual-machines-to-find-public-ip-addresses"></a>Fedezze fel a virtuális gépeket a nyilvános IP-címek kereséséhez

Ez a lekérdezéskészlet először megkeresi és tárolja a virtuális gépekhez csatlakoztatott összes hálózati adapter (NIC) erőforrást. Ezután a lekérdezések a hálózati adapterek listáját használják az egyes IP-címerőforrások megkereséséhez, amelyek nyilvános IP-cím, és tárolják ezeket az értékeket. Végül a lekérdezések a nyilvános IP-címek listáját adják meg.

```azurecli-interactive
# Use Resource Graph to get all NICs and store in the 'nics.txt' file
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20" --output table | tail -n +3 > nics.txt

# Review the output of the query stored in 'nics.txt'
cat nics.txt
```

```azurepowershell-interactive
# Use Resource Graph to get all NICs and store in the $nics variable
$nics = Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20"

# Review the output of the query stored in the variable
$nics.nic
```

Használja a fájlt (Azure CLI) vagy a változó (Azure PowerShell) a következő lekérdezésben a kapcsolódó hálózati adapter erőforrásainak részleteit, ahol van egy nyilvános IP-cím a hálózati adapterhez csatolva.

```azurecli-interactive
# Use Resource Graph with the 'nics.txt' file to get all related public IP addresses and store in 'publicIp.txt' file
az graph query -q="Resources | where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$(awk -vORS="','" '{print $0}' nics.txt | sed 's/,$//')') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp" --output table | tail -n +3 > ips.txt

# Review the output of the query stored in 'ips.txt'
cat ips.txt
```

```azurepowershell-interactive
# Use Resource Graph  with the $nics variable to get all related public IP addresses and store in $ips variable
$ips = Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$($nics.nic -join "','")') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp"

# Review the output of the query stored in the variable
$ips.publicIp
```

Végül használja a fájlban (Azure CLI) vagy változóban (Azure PowerShell) tárolt nyilvános IP-cím erőforrások listáját a kapcsolódó objektumból és megjelenítésből.

```azurecli-interactive
# Use Resource Graph with the 'ips.txt' file to get the IP address of the public IP address resources
az graph query -q="Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$(awk -vORS="','" '{print $0}' ips.txt | sed 's/,$//')') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip" --output table
```

```azurepowershell-interactive
# Use Resource Graph with the $ips variable to get the IP address of the public IP address resources
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$($ips.publicIp -join "','")') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip"
```

Ha meg szeretné tekinteni, hogyan hajtsa végre ezeket a lépéseket egyetlen lekérdezésben az `join` üzemeltetővel, tekintse meg a Virtuális gépek listázása a hálózati [adapterrel és a nyilvános IP-minta.](../samples/advanced.md#join-vmpip)

## <a name="next-steps"></a>További lépések

- További információ a [lekérdezés nyelvéről](query-language.md).
- Tekintse meg a használt nyelvet az [Indítólekérdezésekben.](../samples/starter.md)
- Lásd: Speciális használat a [Speciális lekérdezésekben.](../samples/advanced.md)
