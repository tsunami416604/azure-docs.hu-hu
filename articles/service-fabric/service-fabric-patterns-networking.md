---
title: Hálózati minták az Azure Service Fabric számára
description: Ismerteti a Service Fabric gyakori hálózati mintáit, és hogyan hozhat létre fürtöt az Azure hálózati szolgáltatásaival.
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 065c311fffe409b20e02a3fddf1e9e7e6a82a2a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75466290"
---
# <a name="service-fabric-networking-patterns"></a>Service Fabric hálózati minták
Integrálhatja az Azure Service Fabric-fürtöt más Azure hálózati funkciókkal. Ebben a cikkben bemutatjuk, hogyan hozhat létre fürtöket, amelyek a következő szolgáltatásokat használják:

- [Meglévő virtuális hálózat vagy alhálózat](#existingvnet)
- [Statikus nyilvános IP-cím](#staticpublicip)
- [Csak belső terheléselosztó](#internallb)
- [Belső és külső terheléselosztó](#internalexternallb)

A Service Fabric egy szabványos virtuálisgép-méretezési csoportban fut. Bármilyen funkció, amely használhatja a virtuális gép méretezési készlet, a Service Fabric-fürt. Az Azure Resource Manager-sablonok hálózati szakaszai a virtuálisgép-méretezési készletek és a Service Fabric azonosak. Miután telepítette egy meglévő virtuális hálózatra, egyszerűen beépíthet más hálózati funkciókat, például az Azure ExpressRoute-ot, az Azure VPN-átjárót, egy hálózati biztonsági csoportot és a virtuális hálózati társviszony-létesítést.

### <a name="allowing-the-service-fabric-resource-provider-to-query-your-cluster"></a>Annak engedélyezése, hogy a Service Fabric erőforrás-szolgáltató lekérdezze a fürtöt

A Service Fabric egy szempontból egyedülálló a többi hálózati szolgáltatásból. Az [Azure Portal](https://portal.azure.com) belsőleg a Service Fabric erőforrás-szolgáltató segítségével hívja meg a fürtcsomópontok és alkalmazások információkat. A Service Fabric erőforrás-szolgáltató nyilvánosan elérhető bejövő hozzáférést igényel a HTTP átjáróporthoz (alapértelmezés szerint 19080-as port) a felügyeleti végponton. [A Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) a felügyeleti végpontot használja a fürt kezeléséhez. A Service Fabric erőforrás-szolgáltató is használja ezt a portot a fürt adatait, az Azure Portalon való megjelenítéshez. 

Ha az 19080-as port nem érhető el a Service Fabric erőforrás-szolgáltatótól, a portálon megjelenik egy üzenet, például a *nem található csomópontok,* és a csomópont és az alkalmazáslista üresen jelenik meg. Ha szeretné látni a fürtaz Azure Portalon, a terheléselosztó nak közzé kell tennie egy nyilvános IP-címet, és a hálózati biztonsági csoport engedélyeznie kell a bejövő port 19080 forgalmat. Ha a beállítás nem felel meg ezeknek a követelményeknek, az Azure Portal nem jeleníti meg a fürt állapotát.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="templates"></a>Sablonok

Minden Service Fabric-sablon a [GitHubban](https://github.com/Azure/service-fabric-scripts-and-templates/tree/master/templates/networking)található. A sablonokat a következő PowerShell-parancsokkal kell telepítenie. Ha a meglévő Azure virtuális hálózati sablon vagy a statikus nyilvános IP-sablon telepítése, először olvassa el a jelen cikk [kezdeti beállítási](#initialsetup) szakaszát.

<a id="initialsetup"></a>
## <a name="initial-setup"></a>Kezdeti beállítás

### <a name="existing-virtual-network"></a>Meglévő virtuális hálózat

A következő példában egy meglévő virtuális hálózat nevű LétezőRG-vnet, a **ExistingRG** erőforráscsoportban. Az alhálózat neve alapértelmezett. Ezek az alapértelmezett erőforrások akkor jönnek létre, amikor az Azure Portalon egy szabványos virtuális gép (VM) létrehozásához. A virtuális hálózatot és az alhálózatot a virtuális gép létrehozása nélkül is létrehozhatja, de a fürt meglévő virtuális hálózathoz való hozzáadásának fő célja, hogy hálózati kapcsolatot biztosítson más virtuális gépeknek. A virtuális gép létrehozása jó példát ad arra, hogyan használják a meglévő virtuális hálózatot. Ha a Service Fabric-fürt csak egy belső terheléselosztót használ, nyilvános IP-cím nélkül, használhatja a virtuális gépet és a nyilvános IP-címet biztonságos *ugródobozként.*

### <a name="static-public-ip-address"></a>Statikus nyilvános IP-cím

A statikus nyilvános IP-cím általában egy dedikált erőforrás, amely a virtuális géptől vagy a virtuális gépektől elkülönítve van kezelve. Egy dedikált hálózati erőforráscsoportban van kiépítve (szemben a Service Fabric fürterőforrás-csoport maga). Hozzon létre egy statikus nyilvános IP-címet nevű staticIP1 ugyanabban a ExistingRG erőforráscsoportban, akár az Azure Portalon, vagy a PowerShell használatával:

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

### <a name="service-fabric-template"></a>Service Fabric sablon

Ebben a cikkben példákban a Service Fabric template.json. A szabványos portál varázslóval letöltheti a sablont a portálról, mielőtt fürtöt hozna létre. Használhatja a [mintasablonok](https://github.com/Azure-Samples/service-fabric-cluster-templates)egyikét is, például a [biztonságos ötcsomópontos Service Fabric-fürtöt.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure)

<a id="existingvnet"></a>
## <a name="existing-virtual-network-or-subnet"></a>Meglévő virtuális hálózat vagy alhálózat

1. Módosítsa az alhálózati paramétert a meglévő alhálózat nevére, majd adjon hozzá két új paramétert a meglévő virtuális hálózatra való hivatkozáshoz:

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

2. Megjegyzés `nicPrefixOverride` ki attribútuma `Microsoft.Compute/virtualMachineScaleSets`, mert meglévő alhálózatot használ, és letiltotta ezt a változót az 1.

    ```json
            /*"nicPrefixOverride": "[parameters('subnet0Prefix')]",*/
    ```

3. Módosítsa `vnetID` a változót úgy, hogy a meglévő virtuális hálózatra mutasson:

    ```json
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

4. Eltávolítás `Microsoft.Network/virtualNetworks` az erőforrásokból, így az Azure nem hoz létre új virtuális hálózatot:

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

5. A virtuális hálózatot `dependsOn` a ból `Microsoft.Compute/virtualMachineScaleSets`fűzheti hozzá, hogy ne függjön új virtuális hálózat létrehozásától:

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

6. A sablon telepítése:

    ```powershell
    New-AzResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    A telepítés után a virtuális hálózat tartalmaznia kell az új méretezési készlet virtuális gépek. A virtuálisgép méretezési csoport csomóponttípusának meg kell jelennie a meglévő virtuális hálózat és alhálózat. A Távoli asztali protokoll (RDP) használatával is elérheti a virtuális hálózatban már meglévő virtuális gépet, és pingelheti az új méretezési készlet virtuális gépeket:

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

Egy másik példa, lásd [az egyik, amely nem kifejezetten a Service Fabric.](https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet)


<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>Statikus nyilvános IP-cím

1. Adja meg a meglévő statikus IP-erőforráscsoport nevének paramétereit, nevét és teljesen minősített tartománynevét (FQDN):

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

2. Távolítsa `dnsName` el a paramétert. (A statikus IP-címnek már van ilyen.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

3. Adjon hozzá egy változót a meglévő statikus IP-címre való hivatkozáshoz:

    ```json
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. Eltávolítás `Microsoft.Network/publicIPAddresses` az erőforrásokból, így az Azure nem hoz létre új IP-címet:

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

5. Fűzzön hozzá megjegyzést `dependsOn` az IP-címhez a tulajdonságából, `Microsoft.Network/loadBalancers`hogy ne függjön új IP-cím létrehozásától:

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

6. Az `Microsoft.Network/loadBalancers` erőforrásban módosítsa `publicIPAddress` azt `frontendIPConfigurations` az elemet, amely a meglévő statikus IP-címre hivatkozik az újonnan létrehozott helyett:

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

7. Az `Microsoft.ServiceFabric/clusters` erőforrásban `managementEndpoint` módosítsa a statikus IP-cím DNS-fqdn-jára. Ha biztonságos fürtöt használ, módosítsa *http://* *https://.* (Vegye figyelembe, hogy ez a lépés csak a Service Fabric-fürtök. Ha virtuálisgép-méretezési készletet használ, hagyja ki ezt a lépést.)

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',parameters('existingStaticIPDnsFQDN'),':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

8. A sablon telepítése:

    ```powershell
    New-AzResourceGroup -Name sfnetworkingstaticip -Location westus

    $staticip = Get-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG

    $staticip

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingstaticip -TemplateFile C:\SFSamples\Final\template\_staticip.json -existingStaticIPResourceGroup $staticip.ResourceGroupName -existingStaticIPName $staticip.Name -existingStaticIPDnsFQDN $staticip.DnsSettings.Fqdn
    ```

A telepítés után láthatja, hogy a terheléselosztó a másik erőforráscsoport nyilvános statikus IP-címéhez van kötve. A Service Fabric ügyfélkapcsolati végpontja és a [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) végpontja a statikus IP-cím DNS-fqdn-je.

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>Csak belső terheléselosztó

Ebben a forgatókönyvben lecseréli a külső terheléselosztó az alapértelmezett Service Fabric-sablon egy csak belső terheléselosztó. Tekintse meg [a cikk korábbi, az](#allowing-the-service-fabric-resource-provider-to-query-your-cluster) Azure Portalra és a Service Fabric-erőforrás-szolgáltatóra gyakorolt hatásokat.

1. Távolítsa `dnsName` el a paramétert. (Ez nem szükséges.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. Statikus foglalási módszer használata esetén statikus IP-címparamétert is hozzáadhat. Ha dinamikus foglalási módszert használ, ezt a lépést nem kell megtennie.

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. Eltávolítás `Microsoft.Network/publicIPAddresses` az erőforrásokból, így az Azure nem hoz létre új IP-címet:

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

4. Távolítsa el `dependsOn` a cím `Microsoft.Network/loadBalancers`IP-címattribútumát, hogy ne függjön új IP-cím létrehozásától. Adja hozzá `dependsOn` a virtuális hálózati attribútumot, mert a terheléselosztó mostantól a virtuális hálózat alhálózatától függ:

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

5. Módosítsa a terheléselosztó `frontendIPConfigurations` beállítását `publicIPAddress`a használatával, hogy alhálózatot és `privateIPAddress`. `privateIPAddress`előre definiált statikus belső IP-címet használ. Dinamikus IP-cím használatához távolítsa `privateIPAddress` el az `privateIPAllocationMethod` elemet, majd váltson **Dinamikus**elemre.

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

6. Az `Microsoft.ServiceFabric/clusters` erőforrásban `managementEndpoint` módosítsa a belső terheléselosztó címre való pontra való váltást. Ha biztonságos fürtöt használ, győződjön meg arról, hogy *http://* *https://.* (Vegye figyelembe, hogy ez a lépés csak a Service Fabric-fürtök. Ha virtuálisgép-méretezési készletet használ, hagyja ki ezt a lépést.)

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',reference(variables('lbID0')).frontEndIPConfigurations[0].properties.privateIPAddress,':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

7. A sablon telepítése:

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternallb -TemplateFile C:\SFSamples\Final\template\_internalonlyLB.json
    ```

Üzembe helyezés után a terheléselosztó a privát statikus 10.0.0.250 IP-címet használja. Ha egy másik gép ugyanabban a virtuális hálózatban, akkor a belső [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) végpont. Vegye figyelembe, hogy a terheléselosztó mögötti csomópontok egyikéhez csatlakozik.

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>Belső és külső terheléselosztó

Ebben a forgatókönyvben a meglévő egycsomópontos típusú külső terheléselosztóval kezdi, és hozzáad egy belső terheléselosztót ugyanahhoz a csomóponttípushoz. A háttér-címkészlethez csatlakoztatott háttér-port csak egyetlen terheléselosztóhoz rendelhető hozzá. Válassza ki, hogy melyik terheléselosztó rendelkezik az alkalmazásportokkal, és melyik terheléselosztó rendelkezik a felügyeleti végpontokkal (19000-es és 19080-as portok). Ha a felügyeleti végpontokat a belső terheléselosztóra helyezi, tartsa szem előtt a service fabric erőforrás-szolgáltató [korábban](#allowing-the-service-fabric-resource-provider-to-query-your-cluster)tárgyalt korlátozásait. Az általunk használt példában a felügyeleti végpontok a külső terheléselosztón maradnak. Hozzáad egy 80-as port alkalmazásportot is, és helyezze a belső terheléselosztóra.

Egy kétcsomópontos típusú fürtben egy csomóponttípus van a külső terheléselosztón. A másik csomóponttípus a belső terheléselosztón található. Kétcsomópontos típusú fürt használatához a portál által létrehozott kétcsomópontos típusú sablonban (amely két terheléselosztóval rendelkezik), a második terheléselosztót belső terheléselosztóra kell váltania. További információt a [Csak belső terheléselosztó](#internallb) című szakaszban talál.

1. Adja hozzá a statikus belső terheléselosztó IP-cím paraméterét. (A dinamikus IP-cím használatával kapcsolatos megjegyzéseket a cikk korábbi szakaszaiban olvashat.)

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. Adjon hozzá egy 80-as alkalmazásport paramétert.

3. A meglévő hálózati változók belső verzióinak hozzáadásához másolja és illessze be őket, és adja hozzá a "-Int" nevet:

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

4. Ha a portál által létrehozott sablonnal kezdi, amely a 80-as alkalmazásportot használja, az alapértelmezett portálsablon hozzáadja az AppPort1-et (80-as portot) a külső terheléselosztóhoz. Ebben az esetben távolítsa el az AppPort1-et a külső terheléselosztóból `loadBalancingRules` és a mintavételekből, így hozzáadhatja a belső terheléselosztóhoz:

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

5. Adjon hozzá `Microsoft.Network/loadBalancers` egy második erőforrást. Úgy néz ki, mint a belső terheléselosztó létre a [belső csak terheléselosztó](#internallb) szakaszban létrehozott, de használja a "-Int" terheléselosztó változók, és csak az alkalmazás port 80. Ez is `inboundNatPools`eltávolítja, az RDP-végpontok megtartása a nyilvános terheléselosztó. Ha azt szeretné, hogy az RDP `inboundNatPools` a belső terheléselosztón legyen, lépjen a külső terheléselosztóról erre a belső terheléselosztóra:

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

6. Az `networkProfile` `Microsoft.Compute/virtualMachineScaleSets` erőforráshoz adja hozzá a belső háttércímkészletet:

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

7. A sablon telepítése:

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternalexternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternalexternallb -TemplateFile C:\SFSamples\Final\template\_internalexternalLB.json
    ```

A telepítés után két terheléselosztó látható az erőforráscsoportban. Ha a terheléselosztók között böngészik, láthatja a nyilvános IP-címhez és felügyeleti végpontokat (19000 és 19080 portok). A belső terheléselosztóhoz rendelt statikus belső IP-cím és alkalmazásvégpont (80-as port). Mindkét terheléselosztó ugyanazt a virtuálisgép-méretezési készlet háttérkészletét használja.

## <a name="notes-for-production-workloads"></a>Megjegyzések az éles számítási feladatokhoz

A fenti GitHub-sablonok úgy vannak kialakítva, hogy működjenek együtt az alapértelmezett Termékváltozat az Azure Standard Load Balancer (SLB), az alapszintű termékváltozat. Ez az SLB nem rendelkezik SLA-val, így az éles számítási feladatokhoz a standard termékváltozatot kell használni. Erről az Azure [Standard Load Balancer áttekintése című témakörben olvashat bővebben.](/azure/load-balancer/load-balancer-standard-overview) Bármely Service Fabric-fürt, amely a szabványos termékváltozat slb-hez használatával biztosítja, hogy minden csomóponttípus rendelkezik egy szabály, amely lehetővé teszi a kimenő forgalmat a 443-as porton. Ez a fürt beállításának befejezéséhez szükséges, és az ilyen szabály nélküli központi telepítés sikertelen lesz. A fenti példában egy "csak belső" terheléselosztó, egy további külső terheléselosztó hozzá kell adni a sablonhoz egy szabályt, amely lehetővé teszi a kimenő forgalmat a 443-as port.

## <a name="next-steps"></a>További lépések
[Fürt létrehozása](service-fabric-cluster-creation-via-arm.md)

A telepítés után két terheléselosztó látható az erőforráscsoportban. Ha a terheléselosztók között böngészik, láthatja a nyilvános IP-címhez és felügyeleti végpontokat (19000 és 19080 portok). A belső terheléselosztóhoz rendelt statikus belső IP-cím és alkalmazásvégpont (80-as port). Mindkét terheléselosztó ugyanazt a virtuálisgép-méretezési készlet háttérkészletét használja.

