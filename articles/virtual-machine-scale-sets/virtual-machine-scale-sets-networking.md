---
title: Azure-beli virtuálisgép-méretezési csoportok hálózatkezelése
description: Az Azure virtuálisgép-méretezési készletek fejlettebb hálózati tulajdonságainak konfigurálása.
author: mimckitt
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 07/17/2017
ms.author: mimckitt
ms.openlocfilehash: efe3a39008361fdf76d80a0c8e7e2e30b061117d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461351"
---
# <a name="networking-for-azure-virtual-machine-scale-sets"></a>Azure-beli virtuálisgép-méretezési csoportok hálózatkezelése

Ha a Portalon keresztül helyez üzembe virtuálisgép-méretezési csoportot, bizonyos hálózati tulajdonságok esetében alapértelmezett értékeket használ a rendszer (például Azure Load Balancer bejövő NAT-szabályokkal). Ez a cikk azt ismerteti, hogyan használhatja a méretezési csoportokkal konfigurálható speciális hálózatkezelési szolgáltatásokat.

Az ebben a cikkben ismertetett összes szolgáltatás konfigurálható az Azure Resource Manager-sablonok használatával. Egyes szolgáltatások esetében az Azure CLI-hez és PowerShellhez is találhat példákat.

## <a name="accelerated-networking"></a>Gyorsított hálózatkezelés
Az Azure Gyorsított hálózatkezelés javítja a hálózati teljesítményt azáltal, hogy engedélyezi az egygyökerű I/O-virtualizálást (SR-IOV) a virtuális gépekre. A Gyorsított hálózatkezelésről további információt a [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md) vagy [Linux](../virtual-network/create-vm-accelerated-networking-cli.md) rendszerű virtuális gépek Gyorsított hálózatkezelésével foglalkozó cikkben talál. Ha a gyorsított hálózatkezelést méretezési csoportokkal szeretné használni, állítsa az enableAcceleratedNetworking tulajdonságot **true** értékre a méretezési csoport networkInterfaceConfigurations beállításaiban. Például:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
    {
      "name": "niconfig1",
      "properties": {
        "primary": true,
        "enableAcceleratedNetworking" : true,
        "ipConfigurations": [
          ...
        ]
      }
    }
   ]
}
```

## <a name="azure-virtual-machine-scale-sets-with-azure-load-balancer"></a>Az Azure virtuálisgép-méretezési készletek az Azure Load Balancer segítségével

A virtuálisgép-méretezési készletekkel és a terheléselosztóval végzett munka során a következőket kell figyelembe venni:

* **Több virtuálisgép-méretezési csoport nem használhatja ugyanazt a terheléselosztót.**
* **Porttovábbítási és bejövő NAT-szabályok:**
  * Minden virtuálisgép-méretezési készletnek rendelkeznie kell egy bejövő NAT-szabállyal.
  * A méretezési készlet létrehozása után a háttérport nem módosítható a terheléselosztó állapotminta által használt terheléselosztási szabályhoz. A port módosításához távolítsa el az állapotminta az Azure virtuálisgép-méretezési készlet frissítésével, frissítse a portot, majd konfigurálja újra az állapotminta.
  * Ha a terheléselosztó háttérkészletében használja a virtuálisgép-méretezési készletet, az alapértelmezett bejövő NAT-szabályok automatikusan létrejönnek.
* **Terheléselosztási szabályok:**
  * Ha a terheléselosztó háttérkészletében használja a virtuálisgép-méretezési készletet, az alapértelmezett terheléselosztási szabály automatikusan létrejön.
* **Kimenő szabályok**:
  *  Ha egy olyan háttérkészlethez szeretne kimenő szabályt létrehozni, amelyre már hivatkozik egy terheléselosztási szabály, először meg kell jelölnie a **"Implicit kimenő szabályok létrehozása" szót** a portálon, amikor a bejövő terheléselosztási szabály létrejön. **No**

  :::image type="content" source="./media/vmsslb.png" alt-text="Terheléselosztási szabály létrehozása" border="true":::

A következő módszerek kelthetegy virtuálisgép-méretezési készlet üzembe helyezéséhez egy meglévő Azure-terheléselosztóval.

* [Konfiguráljon egy virtuálisgép-méretezési készletet egy meglévő Azure Load Balancer-rel az Azure Portal használatával.](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-portal)
* [Konfiguráljon egy virtuálisgép-méretezési készletet egy meglévő Azure Load Balancer-rel az Azure PowerShell használatával.](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-powershell)
* [Konfiguráljon egy virtuálisgép-méretezési készletet egy meglévő Azure Load Balancer-rel az Azure CLI használatával.](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-cli)

## <a name="create-a-scale-set-that-references-an-application-gateway"></a>Application Gateway-re hivatkozó méretezési csoport létrehozása
Ha alkalmazásátjárót használó méretezési csoportot szeretne létrehozni, akkor hivatkozzon az alkalmazásátjáró háttércímkészletére a méretezési csoport ipConfigurations szakaszában, mint ebben az ARM-sablonkonfigurációban:

```json
"ipConfigurations": [{
  "name": "{config-name}",
  "properties": {
  "subnet": {
    "id": "{subnet-id}"
  },
  "ApplicationGatewayBackendAddressPools": [{
    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/applicationGateways/{gateway-name}/backendAddressPools/{pool-name}"
  }]
}]
```

>[!NOTE]
> Vegye figyelembe, hogy az alkalmazásátjárónak ugyanabban a virtuális hálózatban kell lennie, mint a méretezési csoportnak, de attól eltérő alhálózatban.


## <a name="configurable-dns-settings"></a>Konfigurálható DNS-beállítások
Alapértelmezés szerint a méretezési csoportok azon virtuális hálózat és alhálózat DNS-beállításait használják, ahol létrehozták őket. A méretezési csoportok DNS-beállításait azonban közvetlenül is konfigurálhatja.

### <a name="creating-a-scale-set-with-configurable-dns-servers"></a>Konfigurálható DNS-kiszolgálókkal rendelkező méretezési csoport létrehozása
Ha egyéni DNS-konfigurációval rendelkező méretezési csoportot szeretne létrehozni az Azure CLI használatával, adja hozzá a **--dns-servers** argumentumot a **vmss create** parancshoz, majd adja meg a kiszolgálók IP-címeit szóközökkel elválasztva. Például:

```bash
--dns-servers 10.0.0.6 10.0.0.5
```

Ha egyéni DNS-kiszolgálókat szeretne konfigurálni egy Azure-sablonban, adja hozzá a dnsSettings tulajdonságot a méretezési csoport networkInterfaceConfigurations szakaszához. Például:

```json
"dnsSettings":{
    "dnsServers":["10.0.0.6", "10.0.0.5"]
}
```

### <a name="creating-a-scale-set-with-configurable-virtual-machine-domain-names"></a>Konfigurálható virtuálisgép-tartománynevekkel rendelkező méretezési csoport létrehozása
Ha olyan méretezési csoportot szeretne létrehozni a CLI használatával, amelyben a virtuális gépek egyéni DNS-névvel rendelkeznek, adja hozzá a **--vm-domain-name** argumentumot a **virtual machine scale set create** parancshoz, majd ezek után adja meg a tartománynév sztringjét.

Ha be szeretné állítani a tartománynevet egy Azure-sablonban, adjon hozzá egy **dnsSettings** tulajdonságot a scale set **networkInterfaceConfigurations** szakaszhoz. Például:

```json
"networkProfile": {
  "networkInterfaceConfigurations": [
    {
    "name": "nic1",
    "properties": {
      "primary": true,
      "ipConfigurations": [
      {
        "name": "ip1",
        "properties": {
          "subnet": {
            "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
          },
          "publicIPAddressconfiguration": {
            "name": "publicip",
            "properties": {
            "idleTimeoutInMinutes": 10,
              "dnsSettings": {
                "domainNameLabel": "[parameters('vmssDnsName')]"
              }
            }
          }
        }
      }
    ]
    }
}
```

A virtuális gépek egyéni DNS-nevének kimenete az alábbi módon kell, hogy kinézzen:

```output
<vm><vmindex>.<specifiedVmssDomainNameLabel>
```

## <a name="public-ipv4-per-virtual-machine"></a>Nyilvános IPv4-cím virtuális gépenként
Az Azure méretezési csoportok virtuális gépeinek általában nincs szükségük saját nyilvános IP-címre. A legtöbb esetben gazdaságosabb és biztonságosabb megoldás, ha egy terheléselosztóhoz vagy egy egyéni virtuális géphez (azaz jumpboxhoz) társít nyilvános IP-címet, amely ezután szükség szerint átirányítja a beérkező kapcsolatokat a méretezési csoportok virtuális gépeihez (például bejövő NAT-szabályok segítségével).

Egyes helyzetek azonban megkövetelik, hogy a méretezési csoport virtuális gépei saját nyilvános IP-címmel rendelkezzenek. Egy ilyen példa a játékok, ahol a konzolnak közvetlen kapcsolatot kell létesítenie egy felhőalapú virtuális géppel, amely elvégzi a játék fizikai világának feldolgozását. Egy másik példa, ha a különböző régiókban található virtuális gépeknek külső kapcsolatokat kell létesítenie egymással egy elosztott adatbázisban.

### <a name="creating-a-scale-set-with-public-ip-per-virtual-machine"></a>Méretezési csoport létrehozása úgy, hogy minden virtuális gép saját IP-címmel rendelkezzen
Ha olyan méretezési csoportot szeretne létrehozni a CLI használatával, amely minden egyes virtuális géphez hozzárendel egy nyilvános IP-címet, adja hozzá a **--public-ip-per-vm** paramétert a **vmss create** parancshoz. 

Ha egy Azure-sablon használatával szeretne méretezési csoportot létrehozni, győződjön meg arról, hogy a Microsoft.Compute/virtualMachineScaleSets erőforrás API-verziója legalább **2017-03-30,** és adjon hozzá egy **publicIpAddressConfiguration** JSON tulajdonságot a méretezési csoport ipConfigurations szakaszhoz. Például:

```json
"publicIpAddressConfiguration": {
    "name": "pub1",
    "properties": {
      "idleTimeoutInMinutes": 15
    }
}
```

Példasablon: [201-vmss-public-ip-linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-public-ip-linux)

### <a name="querying-the-public-ip-addresses-of-the-virtual-machines-in-a-scale-set"></a>A méretezési csoportban található virtuális gépek nyilvános IP-címének lekérdezése
A méretezési csoportok virtuális gépeihez hozzárendelt nyilvános IP-címek listáját az **az vmss list-instance-public-ips** paranccsal kérheti le a CLI használatával.

A powershell használatával a méretezési csoport ban szereplő nyilvános IP-címek listázásához használja a _Get-AzPublicIpAddress_ parancsot. Például:

```powershell
Get-AzPublicIpAddress -ResourceGroupName myrg -VirtualMachineScaleSetName myvmss
```

A nyilvános IP-címeket úgy is lekérdezheti, ha közvetlenül a nyilvános IP-cím konfigurációjának erőforrás-azonosítójára hivatkozik. Például:

```powershell
Get-AzPublicIpAddress -ResourceGroupName myrg -Name myvmsspip
```

A méretezési csoportok virtuális gépeihez hozzárendelt nyilvános IP-címeket az [Azure Resource Explorer](https://resources.azure.com), illetve az Azure REST API **2017-03-30-as** vagy újabb verzióját lekérdezve is megjelenítheti.

Az [Azure Resource Explorer](https://resources.azure.com) lekérdezése:

1. Nyissa meg az [Azure Resource Explorert](https://resources.azure.com) egy böngészőben.
1. Bontsa ki a bal oldalon található *előfizetéseket* a mellettük lévő *+* elemre kattintva. Ha az *előfizetések* alatt csak egy elem található, lehet, hogy az előfizetés már ki lett bontva.
1. Bontsa ki az előfizetést.
1. Bontsa ki az erőforráscsoportot.
1. Bontsa ki a *Szolgáltatók* csomópontot.
1. Bontsa ki a *Microsoft.Compute* csomópontot.
1. Bontsa ki a *virtualMachineScaleSets* csomópontot.
1. Bontsa ki a méretezési csoportot.
1. Kattintson a *publicipaddresses* elemre.

Az Azure REST API lekérdezése:

```bash
GET https://management.azure.com/subscriptions/{your sub ID}/resourceGroups/{RG name}/providers/Microsoft.Compute/virtualMachineScaleSets/{scale set name}/publicipaddresses?api-version=2017-03-30
```

Az [Azure Resource Explorer](https://resources.azure.com) és az Azure REST API példakimenete:

```json
{
  "value": [
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/pipvmss/virtualMachines/0/networkInterfaces/pipvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"a64060d5-4dea-4379-a11d-b23cd49a3c8d\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "ee8cb20f-af8e-4cd6-892f-441ae2bf701f",
        "ipAddress": "13.84.190.11",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/0/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    },
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"5f6ff30c-a24c-4818-883c-61ebd5f9eee8\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "036ce266-403f-41bd-8578-d446d7397c2f",
        "ipAddress": "13.84.159.176",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    }
```

## <a name="multiple-ip-addresses-per-nic"></a>Több IP-cím hálózati adapterenként
A méretezési csoportok virtuális gépeihez csatolt minden hálózati adapter egy vagy több hozzárendelt IP-konfigurációval rendelkezhet. Az egyes konfigurációkhoz egy magánhálózati IP-cím van hozzárendelve. Az egyes konfigurációkhoz egy nyilvános IP-cím erőforrás is hozzárendelhető. A hálózati adapterekhez hozzárendelhető IP-címek, valamint az Azure-előfizetésekben használható nyilvános IP-címek számával kapcsolatos további információkért tekintse meg az [Azure korlátairól](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) szóló cikket.

## <a name="multiple-nics-per-virtual-machine"></a>Több hálózati adapter virtuális gépenként
A gép méretétől függően virtuális gépenként legfeljebb 8 hálózati adapterrel rendelkezhet. A gépenkénti hálózati adapterek maximális számával kapcsolatos további információért tekintse meg a [virtuális gépek méretéről](../virtual-machines/windows/sizes.md) szóló cikket. Az egy virtuálisgép-példányhoz csatlakoztatott összes hálózati adapternek ugyanahhoz a virtuális hálózathoz kell kapcsolódnia. A hálózati adapterek csatlakozhatnak különböző alhálózatokhoz, de mindegyik alhálózatnak ugyanabba a virtuális hálózatba kell tartoznia.

A következő példa egy olyan méretezési csoport hálózati profilja, amely több hálózatiadapter-bejegyzéssel, és virtuális gépenként több nyilvános IP-címmel rendelkezik:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
        "name": "nic1",
        "properties": {
            "primary": true,
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        },
        {
        "name": "nic2",
        "properties": {
            "primary": false,
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        }
    ]
}
```

## <a name="nsg--asgs-per-scale-set"></a>Hálózati biztonsági csoport és alkalmazásbiztonsági csoportok méretezési csoportonként
A [hálózati biztonsági csoportokkal](../virtual-network/security-overview.md) az Azure virtuális hálózatokban lévő Azure-erőforrások bejövő és kimenő forgalmát szűrheti biztonsági szabályok használatával. Az [alkalmazásbiztonsági csoportokkal](../virtual-network/security-overview.md#application-security-groups) az Azure-erőforrások hálózati biztonságát kezelheti, és az alkalmazás struktúrájának kiterjesztéseként csoportosíthatja őket.

A hálózati biztonsági csoportok közvetlenül alkalmazhatók a méretezési csoportokra, ha hozzáadja a hivatkozást a méretezési csoport virtuálisgép-tulajdonságainak hálózatiadapter-konfiguráció szakaszához.

Az alkalmazásbiztonsági csoportok közvetlenül is megadhatók a méretezési csoportoknál, ha hozzáad egy hivatkozást a méretezési csoport virtuálisgép-tulajdonságain belül a hálózati adapter IP-konfigurációját tartalmazó szakaszhoz.

Például:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": true,
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            },
                            "applicationSecurityGroups": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/applicationSecurityGroups/', variables('asgName'))]"
                                }
                            ],
                "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

Annak ellenőrzéséhez, hogy a hálózati biztonsági csoport társítva van-e a méretezési csoporttal, használja az `az vmss show` parancsot. Az alábbi példában a `--query` attribútummal történik az eredmények szűrése, és csak a kimenet releváns része látható.

```azurecli
az vmss show \
    -g myResourceGroup \
    -n myScaleSet \
    --query virtualMachineProfile.networkProfile.networkInterfaceConfigurations[].networkSecurityGroup

[
  {
    "id": "/subscriptions/.../resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/nsgName",
    "resourceGroup": "myResourceGroup"
  }
]
```

Annak ellenőrzéséhez, hogy az alkalmazásbiztonsági csoport társítva van-e a méretezési csoporttal, használja az `az vmss show` parancsot. Az alábbi példában a `--query` attribútummal történik az eredmények szűrése, és csak a kimenet releváns része látható.

```azurecli
az vmss show \
    -g myResourceGroup \
    -n myScaleSet \
    --query virtualMachineProfile.networkProfile.networkInterfaceConfigurations[].ipConfigurations[].applicationSecurityGroups

[
  [
    {
      "id": "/subscriptions/.../resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationSecurityGroups/asgName",
      "resourceGroup": "myResourceGroup"
    }
  ]
]
```



## <a name="next-steps"></a>További lépések
Az Azure-beli virtuális hálózatokról az [Azure-beli virtuális hálózatok áttekintését](../virtual-network/virtual-networks-overview.md) ismertető dokumentumban talál további információt.
