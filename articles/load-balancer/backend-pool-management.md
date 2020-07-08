---
title: Háttérbeli készlet kezelése
description: Útmutató egy Load Balancer háttér-készletének konfigurálásához
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: overview
ms.date: 07/06/2020
ms.author: errobin
ms.openlocfilehash: 6d9700e134a9e3d6c53524d15c8b3503cf5773c7
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050186"
---
# <a name="backend-pool-management"></a>Háttérbeli készlet kezelése
A háttér-készlet a Load Balancer alapvető összetevője, amely meghatározza a számítási erőforrások azon csoportját, amely egy adott terheléselosztási szabály forgalmát fogja szolgálni. A háttér-készletek megfelelő konfigurálásával a rendszer a forgalom kiszolgálására jogosult gépek egy csoportját határozza meg. A háttér-készleteket kétféleképpen lehet konfigurálni: hálózati kártya (NIC), valamint egy kombinált IP-cím és Virtual Network (VNET) erőforrás-azonosító. 

Virtual Machines és Virtual Machine Scale Setst érintő legtöbb esetben ajánlott a háttér-készletet a hálózati adapterrel konfigurálni, mivel ez a módszer az erőforrás és a háttér-készlet közötti legközvetlenebb kapcsolatot hozza létre. A tárolók és Kubernetes hüvelyek, amelyek nem rendelkeznek hálózati adapterrel vagy a háttérbeli erőforrások IP-címeinek előfoglalásával kapcsolatos forgatókönyvekhez, a háttér-készletet IP-cím és VNET-azonosító kombináció szerint konfigurálhatja.

Ha NIC-vagy IP-cím-és VNET-AZONOSÍTÓval konfigurálja a portálon keresztül, a felhasználói felület végigvezeti az egyes lépéseken, és az összes konfigurációs frissítést a háttérbe kerül. A cikk konfigurációs fejezetei a Azure PowerShell, a CLI, a REST API és az ARM-sablonokra összpontosítanak, hogy betekintést kapjanak a háttérbeli készletek struktúrája az egyes konfigurációs beállításokhoz.

## <a name="configuring-backend-pool-by-nic"></a>Háttérbeli készlet konfigurálása hálózati adapter alapján
Amikor hálózati adapteren konfigurálja a háttérrendszer-készletet, fontos szem előtt tartani, hogy a rendszer létrehozza a háttér-készletet a Load Balancer művelet részeként, és a tagokat a hálózati adapter IP-konfigurációs tulajdonságának részeként hozzáadja a háttér-készlethez. Az alábbi példák a háttér-készlet létrehozási és feltöltési műveleteire összpontosítanak a munkafolyamat és a kapcsolat kiemeléséhez.

  >[!NOTE] 
  >Fontos megjegyezni, hogy a hálózati adapteren keresztül konfigurált háttér-készletek nem frissíthetők egy művelet részeként a háttér-készleten. A háttérbeli erőforrások hozzáadásának vagy törlésének az erőforrás hálózati adapterén kell történnie.

### <a name="powershell"></a>PowerShell
Új háttér-készlet létrehozása: 
```powershell
$backendPool = New-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup   -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName  
```

Hozzon létre egy új hálózati adaptert, és adja hozzá a háttér-készlethez:
```powershell
$nic = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic' -LoadBalancerBackendAddressPool $bepool -Subnet $vnet.Subnets[0]
```

A Load Balancer háttér-készlet adatainak beolvasása annak megerősítéséhez, hogy a hálózati adapter hozzá lett adva a háttér-készlethez:
```powershell
Get-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup  -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName -BackendAddressPool  $bePool  
```

Hozzon létre egy új virtuális gépet, és csatolja a hálózati adaptert a háttér-készletbe való helyezéshez:
```powershell
# Create a username and password for the virtual machine
$cred = Get-Credential

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName 'myVM1' -VMSize Standard_DS1_v2 `
 | Set-AzVMOperatingSystem -Windows -ComputerName 'myVM1' -Credential $cred `
 | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2019-Datacenter -Version latest `
 | Add-AzVMNetworkInterface -Id $nicVM1.Id
 
# Create a virtual machine using the configuration
$vm1 = New-AzVM -ResourceGroupName $rgName -Zone 1 -Location $location -VM $vmConfig
```


  
### <a name="cli"></a>parancssori felület
A háttér-készlet létrehozása:
```bash
az network lb address-pool create --resourceGroup myResourceGroup --lb-name myLB --name myBackendPool 
```

Hozzon létre egy új hálózati adaptert, és adja hozzá a háttér-készlethez:
```bash
az network nic create --resource-group myResourceGroup --name myNic --vnet-name myVnet --subnet mySubnet --network-security-group myNetworkSecurityGroup --lb-name myLB --lb-address-pools myBackEndPool
```

A háttér-készlet beolvasásával erősítse meg, hogy az IP-cím helyesen lett hozzáadva:
```bash
az network lb address-pool show -g MyResourceGroup --lb-name MyLb -n MyBackendPool
```

Hozzon létre egy új virtuális gépet, és csatolja a hálózati adaptert a háttér-készletbe való helyezéshez:
```bash
az vm create --resource-group myResourceGroup --name myVM --nics myNic --image UbuntuLTS --admin-username azureuser --generate-ssh-keys
```

### <a name="rest-api"></a>REST API
A háttér-készlet létrehozása:
```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/loadBalancers/{load-balancer-name}/backendAddressPools/{backend-pool-name}?api-version=2020-05-01
```

Hozzon létre egy hálózati adaptert, és adja hozzá a hálózati adapter IP-konfigurációk tulajdonságán keresztül létrehozott háttér-készlethez:

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/networkInterfaces/{nic-name}?api-version=2020-05-01
```

JSON-kérelem törzse:
```json
{
  "properties": {
    "enableAcceleratedNetworking": true,
    "ipConfigurations": [
      {
        "name": "ipconfig1",
        "properties": {
          "subnet": {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}/subnets/{subnet-name}"
          },
          "loadBalancerBackendAddressPools": {
                                    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/loadBalancers/{load-balancer-name}/backendAddressPools/{backend-pool-name}"
          }
        }
      }
    ]
  },
  "location": "eastus"
}
```

A Load Balancer háttér-készlet adatainak beolvasása annak megerősítéséhez, hogy a hálózati adapter hozzá lett adva a háttér-készlethez:

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name/providers/Microsoft.Network/loadBalancers/{load-balancer-name/backendAddressPools/{backend-pool-name}?api-version=2020-05-01
```

Hozzon létre egy virtuális gépet, és csatolja a háttér-készletre hivatkozó hálózati adaptert:

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Compute/virtualMachines/{vm-name}?api-version=2019-12-01
```

JSON-kérelem törzse:
```JSON
{
  "location": "easttus",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_D1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "2016-Datacenter",
        "publisher": "MicrosoftWindowsServer",
        "version": "latest",
        "offer": "WindowsServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Standard_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "myVM",
      "adminPassword": "{your-password}"
    }
  }
}
```

### <a name="arm-template"></a>ARM-sablon
Ezt a [GYORSKONFIGURÁLÁS ARM-sablont](https://github.com/Azure/azure-quickstart-templates/tree/master/101-load-balancer-standard-create/) követve helyezzen üzembe egy Load Balancer és Virtual Machines, és adja hozzá a Virtual Machinest a háttér-készlethez a hálózati adapteren keresztül.

## <a name="configuring-backend-pool-by-ip-address-and-virtual-network"></a>A háttérbeli készlet konfigurálása IP-cím és Virtual Network szerint
Ha terheléselosztást használ a tároló erőforrásaihoz, vagy a háttérbeli készletet egy IP-címtartomány használatával előre kitölti, az IP-cím és a Virtual Network használatával bármely érvényes erőforráshoz átirányíthat, függetlenül attól, hogy van-e hálózati adaptere. Az IP-cím és a VNET konfigurálásakor az összes háttér-készlet kezelése közvetlenül a háttérbeli készlet objektumon történik, az alábbi példákban kiemelten.

  >[!IMPORTANT] 
  >Ez a funkció jelenleg előzetes verzióban érhető el, és a következő korlátozásokkal rendelkezik:
  >* A hozzáadott 100 IP-címek korlátozása
  >* A háttérbeli erőforrásoknak ugyanabban a Virtual Networkban kell lenniük, mint a Load Balancer
  >* Ez a funkció jelenleg nem támogatott a Portal UX-ben
  >* Ez csak a standard szintű terheléselosztó esetén érhető el
  
### <a name="powershell"></a>PowerShell
Új háttér-készlet létrehozása: 
```powershell
$backendPool = New-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup   -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPooName  
```

A háttérbeli készlet frissítése új IP-címmel a meglévő VNET:  
```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroup 
 
$ip1 = New-AzLoadBalancerBackendAddressConfig -IpAddress "10.0.0.5" -Name "TestVNetRef" -VirtualNetwork $virtualNetwork  
 
$backendPool.LoadBalancerBackendAddresses.Add($ip1) 

Set-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup  -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName -BackendAddressPool  $backendPool  
```

A Load Balancer háttér-készlet adatainak beolvasása annak megerősítéséhez, hogy a háttérbeli címek hozzá legyenek adva a háttér-készlethez:
```powershell
Get-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup  -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName -BackendAddressPool  $backendPool  
```
Hozzon létre egy hálózati adaptert, és vegye fel a háttér-készletbe úgy, hogy az IP-címet a háttérbeli címek egyikére állítja be:
```
$nic = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic' -PrivateIpAddress 10.0.0.4 -Subnet $vnet.Subnets[0]
```

Hozzon létre egy virtuális gépet, és csatlakoztassa a hálózati adaptert egy IP-címmel a háttér-készletben:
```powershell
# Create a username and password for the virtual machine
$cred = Get-Credential

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName 'myVM' -VMSize Standard_DS1_v2 `
 | Set-AzVMOperatingSystem -Windows -ComputerName 'myVM' -Credential $cred `
 | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2019-Datacenter -Version latest `
 | Add-AzVMNetworkInterface -Id $nic.Id
 
# Create a virtual machine using the configuration
$vm = New-AzVM -ResourceGroupName $rgName -Zone 1 -Location $location -VM $vmConfig
```

### <a name="cli"></a>parancssori felület
A CLI használatával a háttér-készletet parancssori paraméterekkel vagy JSON-konfigurációs fájlon keresztül töltheti fel. 

Hozza létre és töltse fel a háttér-készletet a parancssori paraméterek használatával:
```bash
az network lb address-pool create --lb-name myLB --name myBackendPool --vnet {VNET resource ID} --backend-address name=addr1 ip-address=10.0.0.4 --backend-address name=addr2 ip-address=10.0.0.5
```

A háttér-készlet létrehozása és feltöltése JSON-konfigurációs fájl használatával:
```bash
az network lb address-pool create --lb-name myLB --name myBackendPool --vnet {VNET resource ID} --backend-address-config-file @config_file.json
```

JSON-konfigurációs fájl:
```JSON
        [
          {
            "name": "address1",
            "virtualNetwork": "/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}",
            "ipAddress": "10.0.0.4"
          },
          {
            "name": "address2",
            "virtualNetwork": "/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}",
            "ipAddress": "10.0.0.5"
          }
        ]
```

A Load Balancer háttér-készlet adatainak beolvasása annak megerősítéséhez, hogy a háttérbeli címek hozzá legyenek adva a háttér-készlethez:
```bash
az network lb address-pool show -g MyResourceGroup --lb-name MyLb -n MyBackendPool
```

Hozzon létre egy hálózati adaptert, és vegye fel a háttér-készletbe úgy, hogy az IP-címet a háttérbeli címek egyikére állítja be:
```bash
az network nic create \
  --resource-group myResourceGroup \
  --name myNic \
  --vnet-name myVnet \
  --subnet mySubnet \
  --network-security-group myNetworkSecurityGroup \
  --lb-name myLB \
  --private-ip-address 10.0.0.4
```

Hozzon létre egy virtuális gépet, és csatlakoztassa a hálózati adaptert egy IP-címmel a háttér-készletben:
```bash
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --nics myNic \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

### <a name="rest-api"></a>REST API


Hozza létre a háttér-készletet, és adja meg a háttérbeli címeket egy PUT háttérbeli készletre vonatkozó kérelem használatával. Adja meg a felvenni kívánt háttérbeli címeket a cím neve, az IP-cím és a Virtual Network azonosító használatával a PUT kérelem JSON-törzsében:

```
PUT https://management.azure.com/subscriptions/subid/resourceGroups/testrg/providers/Microsoft.Network/loadBalancers/lb/backendAddressPools/backend?api-version=2020-05-01
```

JSON-kérelem törzse:
```JSON
{
  "properties": {
    "loadBalancerBackendAddresses": [
      {
        "name": "address1",
        "properties": {
          "virtualNetwork": {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}"
          },
          "ipAddress": "10.0.0.4"
        }
      },
      {
        "name": "address2",
        "properties": {
          "virtualNetwork": {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}"
          },
          "ipAddress": "10.0.0.5"
        }
      }
    ]
  }
}
```

A Load Balancer háttér-készlet adatainak beolvasása annak megerősítéséhez, hogy a háttérbeli címek hozzá legyenek adva a háttér-készlethez:
```
GET https://management.azure.com/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/loadBalancers/{load-balancer-name}/backendAddressPools/{backend-pool-name}?api-version=2020-05-01
```

Hozzon létre egy hálózati adaptert, és vegye fel a háttér-készletbe úgy, hogy az IP-címet a háttérbeli címek egyikére állítja be:
```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/networkInterfaces/{nic-name}?api-version=2020-05-01
```

JSON-kérelem törzse:
```JSON
{
  "properties": {
    "enableAcceleratedNetworking": true,
    "ipConfigurations": [
      {
        "name": "ipconfig1",
        "properties": {
          "privateIPAddress": "10.0.0.4",
          "subnet": {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}/subnets/{subnet-name}"
          }
        }
      }
    ]
  },
  "location": "eastus"
}
```

Hozzon létre egy virtuális gépet, és csatlakoztassa a hálózati adaptert egy IP-címmel a háttér-készletben:

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Compute/virtualMachines/{vm-name}?api-version=2019-12-01
```

JSON-kérelem törzse:
```JSON
{
  "location": "eastus",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_D1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "2016-Datacenter",
        "publisher": "MicrosoftWindowsServer",
        "version": "latest",
        "offer": "WindowsServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Standard_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "myVM",
      "adminPassword": "{your-password}"
    }
  }
}
```

### <a name="arm-template"></a>ARM-sablon
Hozza létre a Load Balancer, a háttér-készletet, és töltse fel a háttérbeli készletet a háttérbeli címekkel:
```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "loadBalancers_myLB_location": {
            "type": "SecureString"
        },
        "loadBalancers_myLB_location_1": {
            "type": "SecureString"
        },
        "backendAddressPools_myBackendPool_location": {
            "type": "SecureString"
        },
        "backendAddressPools_myBackendPool_location_1": {
            "type": "SecureString"
        },
        "loadBalancers_myLB_name": {
            "defaultValue": "myLB",
            "type": "String"
        },
        "virtualNetworks_myVNET_externalid": {
            "defaultValue": "/subscriptions/6bb4a28a-db84-4e8a-b1dc-fabf7bd9f0b2/resourceGroups/ErRobin4/providers/Microsoft.Network/virtualNetworks/myVNET",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2020-04-01",
            "name": "[parameters('loadBalancers_myLB_name')]",
            "location": "eastus",
            "sku": {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "LoadBalancerFrontEnd",
                        "properties": {
                            "privateIPAddress": "10.0.0.7",
                            "privateIPAllocationMethod": "Dynamic",
                            "subnet": {
                                "id": "[concat(parameters('virtualNetworks_myVNET_externalid'), '/subnets/Subnet-1')]"
                            },
                            "privateIPAddressVersion": "IPv4"
                        }
                    }
                ],
                "backendAddressPools": [
                    {
                        "name": "myBackendPool",
                        "properties": {
                            "loadBalancerBackendAddresses": [
                                {
                                    "name": "addr1",
                                    "properties": {
                                        "ipAddress": "10.0.0.4",
                                        "virtualNetwork": {
                                            "location": "[parameters('loadBalancers_myLB_location')]"
                                        }
                                    }
                                },
                                {
                                    "name": "addr2",
                                    "properties": {
                                        "ipAddress": "10.0.0.5",
                                        "virtualNetwork": {
                                            "location": "[parameters('loadBalancers_myLB_location_1')]"
                                        }
                                    }
                                }
                            ]
                        }
                    }
                ],
                "loadBalancingRules": [],
                "probes": [],
                "inboundNatRules": [],
                "outboundRules": [],
                "inboundNatPools": []
            }
        },
        {
            "type": "Microsoft.Network/loadBalancers/backendAddressPools",
            "apiVersion": "2020-04-01",
            "name": "[concat(parameters('loadBalancers_myLB_name'), '/myBackendPool')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLB_name'))]"
            ],
            "properties": {
                "loadBalancerBackendAddresses": [
                    {
                        "name": "addr1",
                        "properties": {
                            "ipAddress": "10.0.0.4",
                            "virtualNetwork": {
                                "location": "[parameters('backendAddressPools_myBackendPool_location')]"
                            }
                        }
                    },
                    {
                        "name": "addr2",
                        "properties": {
                            "ipAddress": "10.0.0.5",
                            "virtualNetwork": {
                                "location": "[parameters('backendAddressPools_myBackendPool_location_1')]"
                            }
                        }
                    }
                ]
            }
        }
    ]
}
```

Hozzon létre egy virtuális gépet és egy csatlakoztatott hálózati adaptert. Állítsa be a hálózati adapter IP-címét a háttérbeli címek egyikére:
```
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "String",
      "metadata": {
        "description": "Name of storage account"
      }
    },
    "storageAccountDomain": {
      "type": "String",
      "metadata": {
        "description": "The domain of the storage account to be created."
      }
    },
    "adminUsername": {
      "type": "String",
      "metadata": {
        "description": "Admin username"
      }
    },
    "adminPassword": {
      "type": "SecureString",
      "metadata": {
        "description": "Admin password"
      }
    },
    "vmName": {
      "defaultValue": "myVM",
      "type": "String",
      "metadata": {
        "description": "Prefix to use for VM names"
      }
    },
    "imagePublisher": {
      "type": "String",
      "metadata": {
        "description": "Image Publisher"
      }
    },
    "imageOffer": {
      "defaultValue": "WindowsServer",
      "type": "String",
      "metadata": {
        "description": "Image Offer"
      }
    },
    "imageSKU": {
      "defaultValue": "2012-R2-Datacenter",
      "type": "String",
      "metadata": {
        "description": "Image SKU"
      }
    },
    "lbName": {
      "defaultValue": "myLB",
      "type": "String",
      "metadata": {
        "description": "Load Balancer name"
      }
    },
    "nicName": {
      "defaultValue": "nic",
      "type": "String",
      "metadata": {
        "description": "Network Interface name prefix"
      }
    },
    "privateIpAddress": {
      "defaultValue": "10.0.0.5",
      "type": "String",
      "metadata": {
        "description": "Private IP Address of the VM"
      }
    },
    "vnetName": {
      "defaultValue": "myVNET",
      "type": "String",
      "metadata": {
        "description": "VNET name"
      }
    },
    "vmSize": {
      "defaultValue": "Standard_A1",
      "type": "String",
      "metadata": {
        "description": "Size of the VM"
      }
    },
    "storageLocation": {
      "type": "String",
      "metadata": {
        "description": "Location of the Storage Account."
      }
    },
    "location": {
      "type": "String",
      "metadata": {
        "description": "Location to deploy all the resources in."
      }
    }
  },
  "variables": {
    "networkSecurityGroupName": "networkSecurityGroup1",
    "storageAccountType": "Standard_LRS",
    "subnetName": "Subnet-1",
    "publicIPAddressType": "Static",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('storageLocation')]",
      "properties": {
        "accountType": "[variables('storageAccountType')]"
      }
    },
    {
      "type": "Microsoft.Network/networkSecurityGroups",
      "apiVersion": "2016-03-30",
      "name": "[variables('networkSecurityGroupName')]",
      "location": "[parameters('location')]",
      "properties": {
        "securityRules": []
      }
    },
    {
      "type": "Microsoft.Network/networkInterfaces",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Static",
              "privateIpAddress": "[parameters('privateIpAddress')]",
              "subnet": {
                "id": "[variables('subnetRef')]"
              }
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('vmName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
        "[parameters('nicName')]"
      ],
      "properties": {
        "hardwareProfile": {
          "vmSize": "[parameters('vmSize')]"
        },
        "osProfile": {
          "computername": "[parameters('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "[parameters('imagePublisher')]",
            "offer": "[parameters('imageOffer')]",
            "sku": "[parameters('imageSKU')]",
            "version": "latest"
          },
          "osDisk": {
            "name": "osdisk",
            "vhd": {
              "uri": "[concat('http://',parameters('storageAccountName'),'.blob.',parameters('storageAccountDomain'),'/vhds/','osdisk', '.vhd')]"
            },
            "caching": "ReadWrite",
            "createOption": "FromImage"
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
            }
          ]
        }
      }
    }
  ]
}
```
