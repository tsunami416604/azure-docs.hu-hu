---
title: Frissítés/áttelepítendő a Termékváltozat PrimaryNodeType a magasabb termékváltozatok eljárás |} Microsoft Docs
description: 'Útmutató: a frissítés/áttelepítendő a Termékváltozat PrimaryNodeType a PowerShell-parancsokkal és parancssori felület parancsait magasabb SKU.'
services: service-fabric
documentationcenter: .net
author: v-rachiw
manager: navya
editor: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/08/2018
ms.author: v-rachiw
ms.openlocfilehash: 96956543a44b6d5d967e3bae3fd833b08baf3d6f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34642733"
---
# <a name="upgrademigrate-the-sku-for-primary-node-type-to-higher-sku"></a>Frissítés/telepítse át a Termékváltozat elsődleges csomóponttípus magasabb másikra

Ez a cikk ismerteti a Termékváltozat az elsődleges csomópont típusú magasabb SKU Azure PowerShell használatával a service fabric-fürt frissítése/áttelepítése

## <a name="add-a-new-virtual-machine-scale-set"></a>Egy új virtuálisgép-méretezési csoport hozzáadása

Telepítsen egy új virtuálisgép-méretezési csoport és a terheléselosztó hasonló adataival. A Service Fabric bővítmény konfiguráció (különösen a csomóponttípus) új virtuálisgép-méretezési csoport nem lehet ugyanaz, mint a régi méretezési meg próbált frissíteni. A Service Fabric explorer ellenőrizheti, hogy rendelkezésre állnak-e az új csomópontok

#### <a name="azure-powershell"></a>Azure PowerShell

Az alábbi példában szereplő Azure PowerShell telepítése a frissített Resource Manager-sablon *template.json* nevű erőforráscsoport használatával *myResourceGroup*:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName myResourceGroup -TemplateFile template.json -TemplateParameterFile parameters.json
```

#### <a name="azure-cli"></a>Azure CLI

A következő parancsot az Azure Service Fabric parancssori felület használatával frissített Resource Manager-sablon üzembe helyezése *template.json* nevű erőforráscsoport használatával *myResourceGroup*:

```CLI
az group deployment create --resource-group myResourceGroup --template-file template.json --parameters parameters.json
```

Tekintse meg a következő példa az új virtuálisgép-méretezési készlet erőforrás hozzáadása a meglévő fürt elsődleges csomóponttípusok json-sablon módosítása

```json
        {
            "apiVersion": "[variables('vmssApiVersion')]",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[parameters('vmNodeType2Name')]",
            "location": "[parameters('computeLocation')]",
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
                "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType2Name')))]",
                "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]",
                "[concat('Microsoft.Storage/storageAccounts/', parameters('applicationDiagnosticsStorageAccountName'))]"
            ],
            "properties": {
                "overprovision": "[parameters('overProvision')]",
                "upgradePolicy": {
                    "mode": "Automatic"
                },
                "virtualMachineProfile": {
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "[concat(parameters('vmNodeType2Name'),'_ServiceFabricNode')]",
                                "properties": {
                                    "type": "ServiceFabricNode",
                                    "autoUpgradeMinorVersion": true,
                                    "protectedSettings": {
                                        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                                        "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                                    },
                                    "publisher": "Microsoft.Azure.ServiceFabric",
                                    "settings": {
                                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                                        "dataPath": "D:\\\\SvcFab",
                                        "durabilityLevel": "Bronze",
                                        "enableParallelJobs": true,
                                        "nicPrefixOverride": "[parameters('subnet0Prefix')]",
                                        "certificate": {
                                            "thumbprint": "[parameters('certificateThumbprint')]",
                                            "x509StoreName": "[parameters('certificateStoreValue')]"
                                        }
                                    },
                                    "typeHandlerVersion": "1.0"
                                }
                            },
                            {
                                "name": "[concat('VMDiagnosticsVmExt','_vmNodeType2Name')]",
                                "properties": {
                                    "type": "IaaSDiagnostics",
                                    "autoUpgradeMinorVersion": true,
                                    "protectedSettings": {
                                        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
                                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                                        "storageAccountEndPoint": "https://core.windows.net/"
                                    },
                                    "publisher": "Microsoft.Azure.Diagnostics",
                                    "settings": {
                                        "WadCfg": {
                                            "DiagnosticMonitorConfiguration": {
                                                "overallQuotaInMB": "50000",
                                                "EtwProviders": {
                                                    "EtwEventSourceProviderConfiguration": [
                                                        {
                                                            "provider": "Microsoft-ServiceFabric-Actors",
                                                            "scheduledTransferKeywordFilter": "1",
                                                            "scheduledTransferPeriod": "PT5M",
                                                            "DefaultEvents": {
                                                                "eventDestination": "ServiceFabricReliableActorEventTable"
                                                            }
                                                        },
                                                        {
                                                            "provider": "Microsoft-ServiceFabric-Services",
                                                            "scheduledTransferPeriod": "PT5M",
                                                            "DefaultEvents": {
                                                                "eventDestination": "ServiceFabricReliableServiceEventTable"
                                                            }
                                                        }
                                                    ],
                                                    "EtwManifestProviderConfiguration": [
                                                        {
                                                            "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                                            "scheduledTransferLogLevelFilter": "Information",
                                                            "scheduledTransferKeywordFilter": "4611686018427387904",
                                                            "scheduledTransferPeriod": "PT5M",
                                                            "DefaultEvents": {
                                                                "eventDestination": "ServiceFabricSystemEventTable"
                                                            }
                                                        }
                                                    ]
                                                }
                                            }
                                        },
                                        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
                                    },
                                    "typeHandlerVersion": "1.5"
                                }
                            }
                        ]
                    },
                    "networkProfile": {
                        "networkInterfaceConfigurations": [
                            {
                                "name": "[concat(parameters('nicName'), '-2')]",
                                "properties": {
                                    "ipConfigurations": [
                                        {
                                            "name": "[concat(parameters('nicName'),'-',2)]",
                                            "properties": {
                                                "loadBalancerBackendAddressPools": [
                                                    {
                                                        "id": "[variables('lbPoolID2')]"
                                                    }
                                                ],
                                                "loadBalancerInboundNatPools": [
                                                    {
                                                        "id": "[variables('lbNatPoolID2')]"
                                                    }
                                                ],
                                                "subnet": {
                                                    "id": "[variables('subnet0Ref')]"
                                                }
                                            }
                                        }
                                    ],
                                    "primary": true
                                }
                            }
                        ]
                    },
                    "osProfile": {
                        "adminPassword": "[parameters('adminPassword')]",
                        "adminUsername": "[parameters('adminUsername')]",
                        "computernamePrefix": "[parameters('vmNodeType2Name')]",
                        "secrets": [
                            {
                                "sourceVault": {
                                    "id": "[parameters('sourceVaultValue')]"
                                },
                                "vaultCertificates": [
                                    {
                                        "certificateStore": "[parameters('certificateStoreValue')]",
                                        "certificateUrl": "[parameters('certificateUrlValue')]"
                                    }
                                ]
                            }
                        ]
                    },
                    "storageProfile": {
                        "imageReference": {
                            "publisher": "[parameters('vmImagePublisher')]",
                            "offer": "[parameters('vmImageOffer')]",
                            "sku": "[parameters('vmImageSku')]",
                            "version": "[parameters('vmImageVersion')]"
                        },
                        "osDisk": {
                            "caching": "ReadOnly",
                            "createOption": "FromImage",
                            "managedDisk": {
                                "storageAccountType": "[parameters('storageAccountType')]"
                            }
                        }
                    }
                }
            },
            "sku": {
                "name": "[parameters('vmNodeType2Size')]",
                "capacity": "[parameters('nt2InstanceCount')]",
                "tier": "Standard"
            },
            "tags": {
                "resourceType": "Service Fabric",
                "clusterName": "[parameters('clusterName')]"
            }
        },
```

## <a name="remove-old-virtual-machine-scale-set"></a>Távolítsa el a régi virtuálisgép-méretezési csoport

1. Tiltsa le a régi virtuális gép méretezési készletben csomópont eltávolítása biztonsági mentés Virtuálisgép-példányok. Ez a művelet egy hosszú ideig is eltarthat. Egyszerre letilthatja, és kell sorba. Várjon, amíg az összes csomópont le vannak tiltva. 
#### <a name="azure-powershell"></a>Azure PowerShell
Az alábbi példában Azure Service Fabric PowerShell nevű csomópont példány letiltása *NTvm1_0* a régi virtuális gép méretezési készletben elnevezett *NTvm1*:
```powershell
Disable-ServiceFabricNode -NodeName NTvm1_0 -Intent RemoveNode
```
#### <a name="azure-cli"></a>Azure CLI
A következő parancsot használja az Azure Service Fabric parancssori felület nevű csomópont példány letiltása *NTvm1_0* a régi virtuális gép méretezési készletben elnevezett *NTvm1*:
```CLI
sfctl node disable --node-name "_NTvm1_0" --deactivation-intent RemoveNode
```
2. Távolítsa el a teljes méretezési készlet. Várjon, amíg a méretezési készletben üzembe helyezési állapota sikeres volt.
#### <a name="azure-powershell"></a>Azure PowerShell
Az alábbi példában az Azure PowerShell távolítsa el a teljes méretezési készletben elnevezett *NTvm1* nevű erőforráscsoportból *myResourceGroup*:
```powershell
Remove-AzureRmVmss -ResourceGroupName myResourceGroup -VMScaleSetName NTvm1
```
#### <a name="azure-cli"></a>Azure CLI
A következő parancsot az Azure Service Fabric parancssori felület használatával távolítsa el a teljes méretezési készletben elnevezett *NTvm1* nevű erőforráscsoportból *myResourceGroup*:

```CLI
az vmss delete --name NTvm1 --resource-group myResourceGroup
```

## <a name="remove-load-balancer-related-to-old-scale-set"></a>Távolítsa el a régi méretezési kapcsolódó terheléselosztó

Távolítsa el a régi méretezési kapcsolódó terheléselosztót. Ezt a lépést, akkor a fürt állásidő rövid idő alatt

#### <a name="azure-powershell"></a>Azure PowerShell

Az alábbi példában szereplő Azure PowerShell eltávolítása a terheléselosztó nevű *LB-myCluster-NTvm1* régi méretezési készletben nevű erőforráscsoportból kapcsolódó *myResourceGroup*:

```powershell
Remove-AzureRmLoadBalancer -Name LB-myCluster-NTvm1 -ResourceGroupName myResourceGroup
```

#### <a name="azure-cli"></a>Azure CLI

A következő parancsot az Azure Service Fabric parancssori felület használatával távolítsa el a terheléselosztó nevű *LB-myCluster-NTvm1* régi méretezési készletben nevű erőforráscsoportból kapcsolódó *myResourceGroup*:

```CLI
az network lb delete --name LB-myCluster-NTvm1 --resource-group myResourceGroup
```

## <a name="remove-public-ip-related-to-old-scale-set"></a>Távolítsa el a régi méretezési kapcsolódó nyilvános IP-cím

Tároló DNS-beállítások nyilvános IP-cím változó beállítani régi méretezési kapcsolódik, majd távolítsa el a nyilvános IP-cím

#### <a name="azure-powershell"></a>Azure PowerShell

Az alábbi példában szereplő Azure PowerShell nevű nyilvános IP-cím DNS-beállítások tárolására *LBIP-myCluster-NTvm1* változó és az IP-cím eltávolítása:

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm1  -ResourceGroupName myResourceGroup
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn
Remove-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm1 -ResourceGroupName myResourceGroup
```

#### <a name="azure-cli"></a>Azure CLI

A következő parancsot az Azure Service Fabric parancssori felület használatával nevű nyilvános IP-cím DNS-beállítások beolvasása *LBIP-myCluster-NTvm1* , és távolítsa el az IP-cím:

```CLI
az network public-ip show --name LBIP-myCluster-NTvm1 --resource-group myResourceGroup
az network public-ip delete --name LBIP-myCluster-NTvm1 --resource-group myResourceGroup
```

## <a name="update-public-ip-address-related-to-new-scale-set"></a>Frissítés kapcsolatos új méretezési nyilvános IP-cím

Frissítés DNS-beállítások új méretezési készletben régi méretezési kapcsolódó nyilvános IP-cím DNS-beállításokkal kapcsolatos nyilvános IP-cím

#### <a name="azure-powershell"></a>Azure PowerShell
Az alábbi példában szereplő Azure PowerShell nevű nyilvános IP-cím DNS-beállítások frissítése *LBIP-myCluster-NTvm3* a DNS-beállítások a korábbi lépésben változók tárolva:

```powershell
$PublicIP = Get-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm3  -ResourceGroupName myResourceGroup
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP
```

#### <a name="azure-cli"></a>Azure CLI

A következő parancsot használja az Azure Service Fabric parancssori felület nevű nyilvános IP-cím DNS-beállítások frissítése *LBIP-myCluster-NTvm3* a DNS-beállítások a régi a korábbi lépésben összegyűjtött nyilvános IP-cím:

```CLI
az network public-ip update --name LBIP-myCluster-NTvm3 --resource-group myResourceGroup --dns-name myCluster
```

## <a name="remove-knowledge-of-service-fabric-node-from-fm"></a>Távolítsa el a service fabric-csomópont Tudásbázis FM

Értesítse a Service Fabric, hogy a csomópontok, amelyek nem működnek, hogy eltávolították a fürtből. (Ez a parancs futtatása az összes virtuális gép példányok régi virtuálisgép-méretezési csoport) (Ha a tartóssági szint a régi virtuálisgép-méretezési csoport ezüst vagy arany, ebben a lépésben nincs feltétlenül szükség. Mivel a lépés a rendszer automatikusan történik.)

#### <a name="azure-powershell"></a>Azure PowerShell
Az alábbi példában Azure Service Fabric PowerShell értesíti, hogy a csomópont neve a service fabric *NTvm1_0* eltávolításra került:

```powershell
Remove-ServiceFabricNodeState -NodeName NTvm1_0
```

#### <a name="azure-cli"></a>Azure CLI

A következő parancsot az Azure Service Fabric parancssori felület használatával értesíti, hogy a csomópont neve a service fabric *NTvm1_0* eltávolításra került:

```CLI
sfctl node remove-state --node-name _NTvm1_0
```
