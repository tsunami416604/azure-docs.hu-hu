---
title: Hálózatkezelés minták az Azure Service Fabrichez |} A Microsoft Docs
description: A Service Fabric és a egy fürt létrehozása az Azure hálózati szolgáltatásaival közös hálózati minta ismerteti.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: aljo
ms.openlocfilehash: d5aa09f3ff899766e6eb6d1784e4417f7b48eac0
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59049897"
---
# <a name="service-fabric-networking-patterns"></a>Hálózati Service Fabric-minták
Más Azure hálózati szolgáltatásokkal integrálható az Azure Service Fabric-fürt. Ebben a cikkben bemutatjuk, hogyan hozhat létre a következő szolgáltatásokat használó fürtök:

- [Meglévő virtuális hálózat vagy alhálózat](#existingvnet)
- [Statikus nyilvános IP-cím](#staticpublicip)
- [Csak belső terheléselosztó](#internallb)
- [Belső és külső terheléselosztó](#internalexternallb)

A Service Fabric fut egy standard szintű virtuálisgép-méretezési csoportot. Olyan funkciót, amely egy virtuálisgép-méretezési csoportot is használhat, használhatja a Service Fabric-fürtön. A virtuálisgép-méretezési csoportok és a Service Fabric Azure Resource Manager sablonjainak hálózati szakaszok megegyeznek. Miután telepít egy meglévő virtuális hálózatot, könnyebbé vált a más hálózati funkciókat, mint például az Azure ExpressRoute, az Azure VPN Gateway, egy hálózati biztonsági csoporthoz és virtuális hálózatok közötti társviszony révén.

A Service Fabric egy egyedülálló megoldás, a más hálózati funkciókat a egy aspektust. A [az Azure portal](https://portal.azure.com) belsőleg az a Service Fabric erőforrás-szolgáltató használatával meghívja a csomópontok és alkalmazásokkal kapcsolatos információkat lekérni egy fürtön. A Service Fabric erőforrás-szolgáltató a HTTP átjáró portjára (19080-as, alapértelmezés szerint port) a felügyeleti végponthoz nyilvánosan elérhető-e bejövő hozzáférésre van szüksége. [A Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) használ a felügyeleti végpont a fürt kezeléséhez. A Service Fabric erőforrás-szolgáltató is ezt a portot, a fürttel kapcsolatos adatlekérdezés használ az Azure Portalon jelennek meg. 

Ha 19080-as port nem érhető el a Service Fabric erőforrás-szolgáltatótól származó, egy üzenet, például *csomópont nem található* megjelenik a portálon, és a csomópont és az alkalmazások listája üres jelenik meg. Ha meg szeretné tekinteni a a fürt az Azure Portalon, a terheléselosztó nyilvános IP-címet kell közzétennie, és a hálózati biztonsági csoportot engedélyeznie kell a 19080-as porton bejövő forgalmat. A telepítő nem felel meg a követelménynek, ha az Azure Portalon nem jelennek meg a fürt állapotát.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="templates"></a>Sablonok

A rendszer az összes Service Fabric-sablonok [GitHub](https://github.com/Azure/service-fabric-scripts-and-templates/tree/master/templates/networking). Meg kell tudnia, a sablonok üzembe helyezése – a következő PowerShell-parancsok használatával. A meglévő Azure Virtual Network-sablon vagy a statikus nyilvános IP-sablon üzembe, először olvassa el a [a kezdeti telepítés](#initialsetup) című szakaszát.

<a id="initialsetup"></a>
## <a name="initial-setup"></a>Kezdeti beállítás

### <a name="existing-virtual-network"></a>Meglévő virtuális hálózat

A következő példában kezdődik meg a nevű ExistingRG virtuális hálózatok közötti, egy meglévő virtuális hálózattal a **ExistingRG** erőforráscsoportot. Az alhálózat neve alapértelmezett. Ezek alapértelmezett erőforrások jönnek létre, ha az Azure portal használatával hozzon létre egy standard virtuális gép (VM). Anélkül, hogy a virtuális gép létrehozása sikerült létrehozni a virtuális hálózatot és alhálózatot, de célja, a fürt hozzáadása egy meglévő virtuális hálózatot a hálózati kapcsolat biztosítható a más virtuális gépeket. A virtuális gép létrehozása meglévő virtuális hálózat általában használatáról jó példa lehetővé teszi. Ha a Service Fabric-fürtöt használ, csak egy belső terheléselosztót, egy nyilvános IP-cím nélkül is használhatja a virtuális gép és a nyilvános IP-cím egy biztonságos *jump-box*.

### <a name="static-public-ip-address"></a>Statikus nyilvános IP-cím

Statikus nyilvános IP-cím általában egy dedikált erőforrás a virtuális Gépet vagy virtuális gépek hozzá van rendelve a külön-külön kezelt. Annak kiépítése egy dedikált hálózati erőforráscsoportban (azaz a Service Fabric-fürt erőforrás csoportosítás maga nem). Hozzon létre egy statikus nyilvános IP-címet staticIP1 ugyanazon ExistingRG erőforráscsoportban, az Azure Portalon vagy a PowerShell használatával:

```powershell
PS C:\Users\user> New-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG -Location westus -AllocationMethod Static -DomainNameLabel sfnetworking

Name                     : staticIP1
ResourceGroupName        : ExistingRG
Location                 : westus
Id                       : /subscriptions/1237f4d2-3dce-1236-ad95-123f764e7123/resourceGroups/ExistingRG/providers/Microsoft.Network/publicIPAddresses/staticIP1
Etag                     : W/"fc8b0c77-1f84-455d-9930-0404ebba1b64"
ResourceGuid             : 77c26c06-c0ae-496c-9231-b1a114e08824
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Static
IpAddress                : 40.83.182.110
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : null
DnsSettings              : {
                             "DomainNameLabel": "sfnetworking",
                             "Fqdn": "sfnetworking.westus.cloudapp.azure.com"
                           }
```

### <a name="service-fabric-template"></a>A Service Fabric-sablon

A cikkben szereplő példák a Service Fabric template.json használjuk. A standard szintű portál varázsló segítségével letöltheti a sablont a portálról, a fürt létrehozása előtt. Is használhatja, a [mintasablon](https://github.com/Azure-Samples/service-fabric-cluster-templates), például a [biztonságos öt csomópontos Service Fabric-fürt](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure).

<a id="existingvnet"></a>
## <a name="existing-virtual-network-or-subnet"></a>Meglévő virtuális hálózat vagy alhálózat

1. Módosítsa a alhálózati paramétert a meglévő alhálózat neve, és adja hozzá a két új paramétert a meglévő virtuális hálózat hivatkozni:

    ```json
        "subnet0Name": {
                "type": "string",
                "defaultValue": "default"
            },
            "existingVNetRGName": {
                "type": "string",
                "defaultValue": "ExistingRG"
            },

            "existingVNetName": {
                "type": "string",
                "defaultValue": "ExistingRG-vnet"
            },
            /*
            "subnet0Name": {
                "type": "string",
                "defaultValue": "Subnet-0"
            },
            "subnet0Prefix": {
                "type": "string",
                "defaultValue": "10.0.0.0/24"
            },*/
    ```

2. Tegye megjegyzésbe `nicPrefixOverride` attribútuma `Microsoft.Compute/virtualMachineScaleSets`, mert a meglévő alhálózatot használja, és le van tiltva ez a változó az 1. lépésben.

    ```json
            /*"nicPrefixOverride": "[parameters('subnet0Prefix')]",*/
    ```

3. Módosítsa a `vnetID` változót a meglévő virtuális hálózathoz pont:

    ```json
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

4. Távolítsa el `Microsoft.Network/virtualNetworks` az erőforrások közül tehát az Azure nem hoz létre egy új virtuális hálózatot:

    ```json
    /*{
    "apiVersion": "[variables('vNetApiVersion')]",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('virtualNetworkName')]",
    "location": "[parameters('computeLocation')]",
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
            {
                "name": "[parameters('subnet0Name')]",
                "properties": {
                    "addressPrefix": "[parameters('subnet0Prefix')]"
                }
            }
        ]
    },
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    }
    },*/
    ```

5. Tegye megjegyzésbe a virtuális hálózattal a `dependsOn` attribútuma `Microsoft.Compute/virtualMachineScaleSets`, ezért nem függnek egy új virtuális hálózatot hoz létre:

    ```json
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Computer/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType0Name')]",
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        /*"[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
        */
        "[Concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray0')[0])]",

    ```

6. A sablon üzembe helyezéséhez:

    ```powershell
    New-AzResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    Az üzembe helyezést követően a virtuális hálózat tartalmaznia kell az új méretezési csoport virtuális gépeinek. A virtuális gép méretezési készlet csomóponttípus meg kell jelennie a meglévő virtuális hálózatot és alhálózatot. Is használhatja Remote Desktop Protocol (RDP) eléri a virtuális Gépet, amely már volt a virtuális hálózatban, és az új méretezési pingelni állítsa be a virtuális gépek:

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

Egy másik példa: [, amely nem adott meg a Service Fabric](https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet).


<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>Statikus nyilvános IP-cím

1. Adja hozzá a statikus IP létező erőforráscsoport neve, nevét és teljesen minősített tartománynevét (FQDN):

    ```json
    "existingStaticIPResourceGroup": {
                "type": "string"
            },
            "existingStaticIPName": {
                "type": "string"
            },
            "existingStaticIPDnsFQDN": {
                "type": "string"
    }
    ```

2. Távolítsa el a `dnsName` paraméter. (A statikus IP-cím már szerepel egy ilyen.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

3. Egy változó való hivatkozáshoz a meglévő statikus IP-cím hozzáadása:

    ```json
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. Távolítsa el `Microsoft.Network/publicIPAddresses` az erőforrásokat, így az Azure nem hozzon létre új IP-cím:

    ```json
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

5. Tegye megjegyzésbe az IP-címét a `dependsOn` attribútuma `Microsoft.Network/loadBalancers`, ezért nem függnek egy új IP-cím létrehozása:

    ```json
    "apiVersion": "[variables('lbIPApiVersion')]",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB', '-', parameters('clusterName'), '-', parameters('vmNodeType0Name'))]",
    "location": "[parameters('computeLocation')]",
    /*
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', concat(parameters('lbIPName'), '-', '0'))]"
    ], */
    "properties": {
    ```

6. Az a `Microsoft.Network/loadBalancers` erőforrás, módosítsa a `publicIPAddress` eleme `frontendIPConfigurations` való hivatkozáshoz helyett egy újonnan létrehozott egy meglévő statikus IP-címe:

    ```json
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                "publicIPAddress": {
                                    /*"id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"*/
                                    "id": "[variables('existingStaticIP')]"
                                }
                            }
                        }
                    ],
    ```

7. Az a `Microsoft.ServiceFabric/clusters` erőforrás, a módosítás `managementEndpoint` a statikus IP-cím DNS teljes Tartománynevét. Ha egy biztonságos fürtöt használ, ellenőrizze, hogy módosítja *http://* való *https://*. (Vegye figyelembe, hogy érvényes-e ez a lépés csak a Service Fabric-fürtöket. Ha egy virtuálisgép-méretezési csoportot használja, kihagyhatja ezt a lépést.)

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',parameters('existingStaticIPDnsFQDN'),':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

8. A sablon üzembe helyezéséhez:

    ```powershell
    New-AzResourceGroup -Name sfnetworkingstaticip -Location westus

    $staticip = Get-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG

    $staticip

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingstaticip -TemplateFile C:\SFSamples\Final\template\_staticip.json -existingStaticIPResourceGroup $staticip.ResourceGroupName -existingStaticIPName $staticip.Name -existingStaticIPDnsFQDN $staticip.DnsSettings.Fqdn
    ```

Az üzembe helyezést követően láthatja, hogy a terheléselosztó van kötve a statikus nyilvános IP-címet a többi erőforráscsoportból. A Service Fabric ügyfélkapcsolati végpont és [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) végpont pontot a DNS-FQDN statikus IP-cím.

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>Csak belső terheléselosztó

Ebben a forgatókönyvben egy belső terheléselosztót az alapértelmezett a Service Fabric-sablont a külső terheléselosztó lecseréli. Az Azure portal és a Service Fabric erőforrás-szolgáltató a következmények lásd: az előző szakaszban.

1. Távolítsa el a `dnsName` paraméter. (Nincs rá szükség.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. Igény szerint ha a statikus foglalási módszert használja, hozzáadhat egy statikus IP-cím paraméter. Ha a dinamikus kiosztási módszer használható, nem kell erre a lépésre szükség.

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. Távolítsa el `Microsoft.Network/publicIPAddresses` az erőforrásokat, így az Azure nem hozzon létre új IP-cím:

    ```json
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

4. Távolítsa el az IP-cím `dependsOn` attribútuma `Microsoft.Network/loadBalancers`, ezért nem függnek egy új IP-cím létrehozása. A virtuális hálózat hozzáadása `dependsOn` attribútumon, mert a terheléselosztó már a virtuális hálózati alhálózat függ:

    ```json
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'))]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /*"[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"*/
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
    ```

5. Módosítsa a terheléselosztó `frontendIPConfigurations` beállítást használják egy `publicIPAddress`, egy alhálózatot az és `privateIPAddress`. `privateIPAddress` előre meghatározott statikus belső IP-címet használ. Dinamikus IP-címet használjon, távolítsa el a `privateIPAddress` elemet, és módosítsa `privateIPAllocationMethod` való **dinamikus**.

    ```json
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /*
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                } */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
    ```

6. Az a `Microsoft.ServiceFabric/clusters` erőforrás, a módosítás `managementEndpoint` átirányítása a belső terheléselosztó címmel. Ha egy biztonságos fürtöt használ, ellenőrizze, hogy módosítja *http://* való *https://*. (Vegye figyelembe, hogy érvényes-e ez a lépés csak a Service Fabric-fürtöket. Ha egy virtuálisgép-méretezési csoportot használja, kihagyhatja ezt a lépést.)

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',reference(variables('lbID0')).frontEndIPConfigurations[0].properties.privateIPAddress,':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

7. A sablon üzembe helyezéséhez:

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternallb -TemplateFile C:\SFSamples\Final\template\_internalonlyLB.json
    ```

Az üzembe helyezést követően a terheléselosztó 10.0.0.250 statikus magánhálózati IP-címet használja. Ha egy másik gép ugyanazon a virtuális hálózaton található, megnyithatja a belső [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) végpont. Vegye figyelembe, hogy csatlakozik az egyik csomópontot a terheléselosztó mögött.

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>Belső és külső terheléselosztó

Ebben a forgatókönyvben a meglévő egycsomópontos típus külső terheléselosztó kezdődnie, és adja hozzá a belső terheléselosztó az azonos csomóponttípus. Egy háttér-porton csatlakozik a háttér címkészletet csak egyetlen terheléselosztó rendelhetők. Válassza ki, melyik load balancert fog rendelkezni az alkalmazás portok, és mely terheléselosztót fog rendelkezni a felügyeleti végpontok (19000 és a 19080-as portot). Ha a belső terheléselosztón helyezi a felügyeleti végpontok, tartsa szem előtt a Service Fabric resource provider korlátozásokat a cikk korábbi részében ismertetett. A példában használjuk, a felügyeleti végpontok továbbra is a külső terheléselosztó. Emellett adjon hozzá egy port 80 alkalmazásport, és helyezze el a belső terheléselosztó.

Egy két csomóponttípus fürtben egy csomópont típusa van a külső terheléselosztó. A második csomópont típus esetén a belső terheléselosztón. Két csomóponttípus-fürtöt, a portál létrehozott két csomóponttípus sablonban (amely tartalmaz két terheléselosztók) használ, a második terheléselosztó váltson egy belső terheléselosztót. További információkért lásd: a [csak belső terheléselosztó](#internallb) szakaszban.

1. Adja hozzá a statikus belső load balancer IP-cím paraméter. (A dinamikus IP-címet használó kapcsolódó megjegyzések, lásd a cikk korábbi szakaszaiban.)

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. Adja hozzá az alkalmazás 80-as porton paramétert.

3. Adja hozzá a meglévő belső verziói hálózatkezelés változók, másolja és illessze be őket, és adja hozzá a "– Int" nevet:

    ```json
    /* Add internal load balancer networking variables */
            "lbID0-Int": "[resourceId('Microsoft.Network/loadBalancers', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal'))]",
            "lbIPConfig0-Int": "[concat(variables('lbID0-Int'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
            "lbPoolID0-Int": "[concat(variables('lbID0-Int'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
            "lbProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricGatewayProbe')]",
            "lbHttpProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricHttpGatewayProbe')]",
            "lbNatPoolID0-Int": "[concat(variables('lbID0-Int'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
            /* Internal load balancer networking variables end */
    ```

4. Ha először a portál által létrehozott sablont, amely az alkalmazás 80-as portot használja, a portál alapértelmezett sablon hozzáadása AppPort1 (80-as port) a külső terheléselosztó. Ebben az esetben távolítsa el a külső terheléselosztó AppPort1 `loadBalancingRules` és mintavételek menüpontban, így adhat hozzá a belső terheléselosztó:

    ```json
    "loadBalancingRules": [
        {
            "name": "LBHttpRule",
            "properties":{
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[variables('lbHttpProbeID0')]"
                },
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortLBRule1",
            "properties": {
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('loadBalancedAppPort1')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[concate(variables('lbID0'), '/probes/AppPortProbe1')]"
                },
                "protocol": "tcp"
            }
        }*/

    ],
    "probes": [
        {
            "name": "FabricGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricTcpGatewayPort')]",
                "protocol": "tcp"
            }
        },
        {
            "name": "FabricHttpGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricHttpGatewayPort')]",
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortProbe1",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('loadBalancedAppPort1')]",
                "protocol": "tcp"
            }
        } */

    ],
    "inboundNatPools": [
    ```

5. Vegyen fel egy második `Microsoft.Network/loadBalancers` erőforrás. A belső terheléselosztó létrehozása a hasonlóan néz ki a [csak belső terheléselosztó](#internallb) szakaszt, de használja a "– Int" load balancer változók, és csak az alkalmazás 80-as porton implementálja. Ez eltávolítja `inboundNatPools`, hogy megtartsa az RDP-végpontokra nyilvános terheléselosztón. Ha azt szeretné, az RDP a belső terheléselosztón, helyezze át `inboundNatPools` a külső terheléselosztó a belső terheléselosztóhoz:

    ```json
            /* Add a second load balancer, configured with a static privateIPAddress and the "-Int" load balancer variables. */
            {
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                /* Add "-Internal" to the name. */
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal')]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /* Remove public IP dependsOn, add vnet dependsOn
                    "[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"
                    */
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /* Switch from Public to Private IP address
                                */
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                }
                                */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
                    "backendAddressPools": [
                        {
                            "name": "LoadBalancerBEAddressPool",
                            "properties": {}
                        }
                    ],
                    "loadBalancingRules": [
                        /* Add the AppPort rule. Be sure to reference the "-Int" versions of backendAddressPool, frontendIPConfiguration, and the probe variables. */
                        {
                            "name": "AppPortLBRule1",
                            "properties": {
                                "backendAddressPool": {
                                    "id": "[variables('lbPoolID0-Int')]"
                                },
                                "backendPort": "[parameters('loadBalancedAppPort1')]",
                                "enableFloatingIP": "false",
                                "frontendIPConfiguration": {
                                    "id": "[variables('lbIPConfig0-Int')]"
                                },
                                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                                "idleTimeoutInMinutes": "5",
                                "probe": {
                                    "id": "[concat(variables('lbID0-Int'),'/probes/AppPortProbe1')]"
                                },
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "probes": [
                    /* Add the probe for the app port. */
                    {
                            "name": "AppPortProbe1",
                            "properties": {
                                "intervalInSeconds": 5,
                                "numberOfProbes": 2,
                                "port": "[parameters('loadBalancedAppPort1')]",
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "inboundNatPools": [
                    ]
                },
                "tags": {
                    "resourceType": "Service Fabric",
                    "clusterName": "[parameters('clusterName')]"
                }
            },
    ```

6. A `networkProfile` számára a `Microsoft.Compute/virtualMachineScaleSets` erőforrás, a belső háttér-címkészlet hozzáadása:

    ```json
    "loadBalancerBackendAddressPools": [
                                                        {
                                                            "id": "[variables('lbPoolID0')]"
                                                        },
                                                        {
                                                            /* Add internal BE pool */
                                                            "id": "[variables('lbPoolID0-Int')]"
                                                        }
    ],
    ```

7. A sablon üzembe helyezéséhez:

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternalexternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternalexternallb -TemplateFile C:\SFSamples\Final\template\_internalexternalLB.json
    ```

Üzembe helyezés után megjelenik az erőforráscsoportban lévő két terheléselosztók. Miután felkereste a terheléselosztók, láthatja a nyilvános IP cím és felügyeleti végpont (19000 és a 19080-as portot) rendelt nyilvános IP-cím. A statikus belső IP cím és az alkalmazások végpont (80-as port) a belső terheléselosztó rendelt is láthatja. Mindkét terheléselosztók az azonos virtuális gép méretezési készlet háttérkészlet-használja.

## <a name="next-steps"></a>További lépések
[Hozzon létre egy fürtöt](service-fabric-cluster-creation-via-arm.md) ternalLB.json
    ```

Üzembe helyezés után megjelenik az erőforráscsoportban lévő két terheléselosztók. Miután felkereste a terheléselosztók, láthatja a nyilvános IP cím és felügyeleti végpont (19000 és a 19080-as portot) rendelt nyilvános IP-cím. A statikus belső IP cím és az alkalmazások végpont (80-as port) a belső terheléselosztó rendelt is láthatja. Mindkét terheléselosztók az azonos virtuális gép méretezési készlet háttérkészlet-használja.

## <a name="next-steps"></a>További lépések
[Fürt létrehozása](service-fabric-cluster-creation-via-arm.md)
