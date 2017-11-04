---
title: "Azure Service Fabric hálózatkezelés mintái |} Microsoft Docs"
description: "A Service Fabric és a fürt létrehozása az Azure hálózati szolgáltatások segítségével közös hálózati mintákat ismerteti."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/30/2017
ms.author: ryanwi
ms.openlocfilehash: 535ea21a2c08be5f676ee24269b323a415b92607
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-networking-patterns"></a>A Service Fabric hálózati minták
Az Azure Service Fabric-fürt integrálhatja más Azure hálózati szolgáltatásokkal. Ebben a cikkben azt mutatja be az alábbi szolgáltatásokat használó fürtök létrehozásához:

- [Meglévő virtuális hálózathoz vagy alhálózathoz](#existingvnet)
- [Statikus nyilvános IP-cím](#staticpublicip)
- [Csak belső terheléselosztó](#internallb)
- [Belső és külső terheléselosztó](#internalexternallb)

A Service Fabric-szabványos virtuálisgép-méretezési csoportban lévő fut. Olyan funkciót, melyekkel egy virtuálisgép-méretezési csoportban lévő, használhatja a Service Fabric-fürt. Az Azure Resource Manager-sablonok a virtuálisgép-méretezési csoportok és a Service Fabric hálózati szakasza esetén azonosak. Miután telepít egy meglévő virtuális hálózatot, is könnyen más hálózati funkciókat, például Azure ExpressRoute, Azure VPN Gateway, a hálózati biztonsági csoporthoz és virtuális hálózati társviszony-létesítés tartalmaznia.

A Service Fabric rendszer más hálózati szolgáltatások egyik tulajdonsága az egyedi. A [Azure-portálon](https://portal.azure.com) belső módon használja a Service Fabric erőforrás-szolgáltató hívása fürthöz csomópontokat és alkalmazások adatainak beolvasása. A Service Fabric erőforrás-szolgáltató nyilvánosan elérhető bejövő kell elérnie a felügyeleti végpont HTTP-átjáró port (19080, alapértelmezés szerint a port). [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) használ a felügyeleti végpont a fürt kezeléséhez. A Service Fabric erőforrás-szolgáltató ezen a porton lekérdezés adatait a fürt is használja az Azure portálon megjelenítéséhez. 

Ha port 19080 nem érhető el a Service Fabric erőforrás-szolgáltató, egy üzenet, például *csomópont nem található* megjelenik a portálon, és megjelenik a a csomópont- és alkalmazás listája üres. Ha azt szeretné, hogy a fürt az Azure portálon, a terheléselosztó fel kell fednie egy nyilvános IP-címet, és a hálózati biztonsági csoport engedélyeznie kell a bejövő portot 19080 adatforgalmat. A telepítő nem felel meg a követelménynek, ha az Azure-portál nem jelenik meg a fürt állapota.

## <a name="templates"></a>Sablonok

Az összes Service Fabric-sablonok vannak [egyik letöltendő fájl](https://msdnshared.blob.core.windows.net/media/2016/10/SF_Networking_Templates.zip). Meg kell telepíteni, a sablonok – a következő PowerShell-parancsok használatával. Ha telepíti a meglévő Azure virtuális hálózat vagy a statikus nyilvános IP-sablont, először olvassa el a [telepítő kezdeti](#initialsetup) című szakaszát.

<a id="initialsetup"></a>
## <a name="initial-setup"></a>Kezdeti telepítés

### <a name="existing-virtual-network"></a>Meglévő virtuális hálózat

A következő példában először nevű ExistingRG-hálózatok, a meglévő virtuális hálózat a **ExistingRG** erőforráscsoportot. Az alhálózat neve alapértelmezett. Ezek az alapértelmezett erőforrások jönnek létre, ha az Azure-portál használatával hozzon létre egy szabványos virtuális gépet (VM). Létrehozhatja a virtuális hálózati és alhálózati nélkül a virtuális gép létrehozása, de a fő céllal, a fürt hozzáadása egy meglévő virtuális hálózathoz hálózati kapcsolatot biztosít a többi virtuális gépekhez. A virtuális gép létrehozása biztosít a meglévő virtuális hálózat általában használatáról jó példa. Ha a Service Fabric-fürt csak egy belső elosztott terhelésű, a nyilvános IP-cím nélkül is használhatja a virtuális gép és a nyilvános IP-cím a biztonságos *mezőben jump*.

### <a name="static-public-ip-address"></a>Statikus nyilvános IP-cím

Egy statikus nyilvános IP-cím általában egy dedikált erőforrást, amely a virtuális gép vagy a virtuális gépek hozzá van rendelve külön kezelnek. Azt ki van építve a dedikált hálózati erőforráscsoportban (azaz a Service Fabric-fürt erőforrás csoportosítás maga nem). Hozzon létre egy statikus nyilvános IP-cím nevű staticIP1 azonos ExistingRG az erőforráscsoporthoz tartozik, az Azure portálon vagy a PowerShell használatával:

```powershell
PS C:\Users\user> New-AzureRmPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG -Location westus -AllocationMethod Static -DomainNameLabel sfnetworking

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

A cikkben szereplő példák használjuk a Service Fabric template.json. A szabványos portál varázsló segítségével a sablon letöltéséről a portálon, a fürt létrehozása előtt. Is használhatja a sablont a [sablon gyűjtemény](https://azure.microsoft.com/en-us/documentation/templates/?term=service+fabric), például a [öt csomópontból Service Fabric-fürt](https://azure.microsoft.com/en-us/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/).

<a id="existingvnet"></a>
## <a name="existing-virtual-network-or-subnet"></a>Meglévő virtuális hálózathoz vagy alhálózathoz

1. Módosítsa a alhálózati paramétert a meglévő alhálózat nevét, és adja hozzá a két új paramétert a meglévő virtuális hálózat hivatkozni:

    ```
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


2. Módosítsa a `vnetID` változó, mutasson a meglévő virtuális hálózathoz:

    ```
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

3. Távolítsa el `Microsoft.Network/virtualNetworks` az erőforrásokat, így Azure nem hozzon létre egy új virtuális hálózat:

    ```
    /*{
    "apiVersion": "[variables('vNetApiVersion')]",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('virtualNetworkName')]",
    "location": "[parameters('computeLocation')]",
    "properities": {
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

4. A virtuális hálózaton megjegyzésbe a `dependsOn` attribútumának `Microsoft.Compute/virtualMachineScaleSets`, így nem függ egy új virtuális hálózat létrehozása:

    ```
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Computer/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType0Name')]",
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        /*"[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
        */
        "[Concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray0')[0])]",

    ```

5. A sablon telepítéséhez:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    A központi telepítést követően a virtuális hálózat tartalmaznia kell az új virtuális gépek méretezési csoportjának virtuális gépeket. A virtuális gép méretezési készlet csomóponttípus meg kell jelennie a meglévő virtuális hálózat és alhálózat. Is használhatja Remote Desktop Protocol (RDP) a virtuális Gépet, amely már szerepel a virtuális hálózat eléréséhez, és Pingelje meg az új állítsa be a virtuális gépek:

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

Egy másik példa, lásd: [, amely nem a Service Fabric jellemző](https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet).


<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>Statikus nyilvános IP-cím

1. Adja hozzá a statikus IP meglévő erőforráscsoport neve, a neve és a teljesen minősített tartománynevét (FQDN) paramétereinek:

    ```
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

2. Távolítsa el a `dnsName` paraméter. (A statikus IP-cím már szerepel ilyen.)

    ```
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

3. Egy változó való hivatkozáshoz a meglévő statikus IP-cím hozzáadása:

    ```
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. Távolítsa el `Microsoft.Network/publicIPAddresses` az erőforrásokat, így Azure nem hozzon létre egy új IP-cím:

    ```
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

5. Az IP-cím a megjegyzésbe a `dependsOn` attribútumának `Microsoft.Network/loadBalancers`, így nem függ egy új IP-cím létrehozása:

    ```
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

6. Az a `Microsoft.Network/loadBalancers` erőforrás, módosítsa a `publicIPAddress` eleme `frontendIPConfigurations` való hivatkozáshoz helyett egy újonnan létrehozott egy létező statikus IP-cím:

    ```
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

7. Az a `Microsoft.ServiceFabric/clusters` erőforrás, a módosítás `managementEndpoint` a statikus IP-címet a DNS teljes Tartományneve számára. Ha egy biztonságos fürtöt használ, ellenőrizze, hogy megváltoztatja *http://* való *https://*. (Vegye figyelembe, hogy ez a lépés csak a Service Fabric-fürtök vonatkozik. Ha egy virtuálisgép-méretezési csoport használja, kihagyhatja ezt a lépést.)

    ```
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',parameters('existingStaticIPDnsFQDN'),':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

8. A sablon telepítéséhez:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkingstaticip -Location westus

    $staticip = Get-AzureRmPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG

    $staticip

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingstaticip -TemplateFile C:\SFSamples\Final\template\_staticip.json -existingStaticIPResourceGroup $staticip.ResourceGroupName -existingStaticIPName $staticip.Name -existingStaticIPDnsFQDN $staticip.DnsSettings.Fqdn
    ```

A központi telepítést követően láthatja, hogy a terheléselosztó van kötve a nyilvános statikus IP-cím az egyéb erőforráscsoportból. A Service Fabric ügyfél-csatlakozási végpont és [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) végpont pontot a DNS FQDN a statikus IP-cím.

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>Csak belső terheléselosztó

Ebben a forgatókönyvben a külső terheléselosztóhoz, a Service Fabric alapértelmezett sablon lecseréli egy csak belső terheléselosztót. Az Azure-portál és a Service Fabric erőforrás-szolgáltató megvalósítását lásd: az előző szakaszban.

1. Távolítsa el a `dnsName` paraméter. (Nincs rá szükség.)

    ```
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. Szükség esetén egy statikus kiosztási módszerrel használatakor is hozzáadhat egy statikus IP-cím paraméter. Ha egy dinamikus elosztási módszert használ, nem szükséges ehhez a lépéshez.

    ```
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. Távolítsa el `Microsoft.Network/publicIPAddresses` az erőforrásokat, így Azure nem hozzon létre egy új IP-cím:

    ```
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

4. Távolítsa el az IP-cím `dependsOn` attribútumának `Microsoft.Network/loadBalancers`, így nem függ egy új IP-cím létrehozása. Adja hozzá a virtuális hálózatot `dependsOn` attribútumon, mert a terheléselosztó most a virtuális hálózati alhálózat függ:

    ```
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'))]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /*"[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"*/
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
    ```

5. Módosítsa a terheléselosztó `frontendIPConfigurations` beállítása a használatával egy `publicIPAddress`, alhálózat használatát és `privateIPAddress`. `privateIPAddress`egy előre meghatározott statikus belső IP-címet használja. A dinamikus IP-cím használatára, távolítsa el a `privateIPAddress` elemet, és módosítsa `privateIPAllocationMethod` való **dinamikus**.

    ```
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

6. Az a `Microsoft.ServiceFabric/clusters` erőforrás, a módosítás `managementEndpoint` úgy, hogy a belső terheléselosztó-címén mutasson. Ha biztonságos-fürtöt használ, ellenőrizze, hogy megváltoztatja *http://* való *https://*. (Vegye figyelembe, hogy ez a lépés csak a Service Fabric-fürtök vonatkozik. Ha egy virtuálisgép-méretezési csoport használja, kihagyhatja ezt a lépést.)

    ```
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',reference(variables('lbID0')).frontEndIPConfigurations[0].properties.privateIPAddress,':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

7. A sablon telepítéséhez:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkinginternallb -Location westus

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternallb -TemplateFile C:\SFSamples\Final\template\_internalonlyLB.json
    ```

A központi telepítést követően a terheléselosztó a statikus 10.0.0.250 magánhálózati IP-címet használ. Ha egy másik gép ugyanazon virtuális hálózatban, lépjen a belső [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) végpont. Figyelje meg, hogy a terheléselosztó mögött a csomópontok egyikét csatlakozik.

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>Belső és külső terheléselosztó

Ebben a forgatókönyvben a meglévő egycsomópontos típus külső terheléselosztó kezdődnie, és vegye fel a csomópont ugyanolyan belső terheléselosztót. A háttér-port, egy háttér címkészletet csatolva csak egyetlen terheléselosztót is hozzárendelhető. Válassza ki, mely terheléselosztót kapnak az alkalmazás portok, valamint mely terheléselosztót fog rendelkezni a felügyeleti végpontok (portok 19000 és 19080). Ha a felügyeleti végpontok a belső terheléselosztón, tartsa szem előtt a Service Fabric-erőforrás a cikkben korábban tárgyalt szolgáltató korlátozások. A példában használjuk, a felügyeleti végpontok maradnak a külső terheléselosztóhoz. Emellett adjon hozzá egy port 80 alkalmazás portot, és helyezze el a belső terheléselosztó.

A két csomóponttípus fürtben egy csomópont típus a külső terheléselosztóhoz. A második csomópont típus esetén a belső terheléselosztón. A két csomóponttípus-fürtöt használ, a portál által létrehozott két csomóponttípus sablonban (Ez a két terheléselosztók), a második terheléselosztó átvált belső terheléselosztót. További információkért lásd: a [csak belső terheléselosztó](#internallb) szakasz.

1. Adja hozzá a statikus belső load balancer IP-cím paraméter. (Egy dinamikus IP-cím használatához kapcsolódó megjegyzések, tekintse meg a cikk korábbi szakaszaiban.)

    ```
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. Adja hozzá az alkalmazás 80-as port paramétert.

3. Belső a meglévő hálózati változók, másolja és illessze be a hozzáadni, majd adja meg a "-Int" nevét:

    ```
    /* Add internal load balancer networking variables */
            "lbID0-Int": "[resourceId('Microsoft.Network/loadBalancers', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal'))]",
            "lbIPConfig0-Int": "[concat(variables('lbID0-Int'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
            "lbPoolID0-Int": "[concat(variables('lbID0-Int'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
            "lbProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricGatewayProbe')]",
            "lbHttpProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricHttpGatewayProbe')]",
            "lbNatPoolID0-Int": "[concat(variables('lbID0-Int'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
            /* Internal load balancer networking variables end */
    ```

4. Ha először a portál által létrehozott sablon, amely alkalmazás 80-as portot használja, a portál alapértelmezett sablon hozzáadása AppPort1 (80-as port) a külső terheléselosztóhoz. Ebben az esetben távolítsa el a külső terheléselosztó AppPort1 `loadBalancingRules` és mintavételek menüpontban, így adhat hozzá a belső terheléselosztó:

    ```
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

5. Adja hozzá egy második `Microsoft.Network/loadBalancers` erőforrás. A belső terheléselosztó létrehozott hasonló a [csak belső terheléselosztó](#internallb) szakaszában, de használja a "-Int" terheléselosztó változók betölteni, és megvalósítja az csak az alkalmazás 80-as porton. Ez eltávolítja `inboundNatPools`, hogy továbbra is RDP-végpontot a nyilvános terheléselosztó. Az RDP a belső terheléselosztón, helyezze `inboundNatPools` a külső terheléselosztóhoz, a belső terheléselosztóhoz:

    ```
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

6. A `networkProfile` a a `Microsoft.Compute/virtualMachineScaleSets` erőforrás, a belső háttér-címkészlet hozzáadása:

    ```
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

7. A sablon telepítéséhez:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkinginternalexternallb -Location westus

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternalexternallb -TemplateFile C:\SFSamples\Final\template\_internalexternalLB.json
    ```

A központi telepítést követően az erőforráscsoportban két terheléselosztók tekintheti meg. Ha tallózással azokat a terheléselosztókat, megtekintheti a nyilvános IP cím és felügyeleti végpontok (19000 és 19080 portok) a nyilvános IP-címet hozzárendelni. A statikus belső IP-cím és az alkalmazás végponton (80-as port) a belső terheléselosztó rendelt is látható. Mindkét terheléselosztók használja a virtuális gép méretezési készlet háttér-készlethez.

## <a name="next-steps"></a>Következő lépések
[Fürt létrehozása](service-fabric-cluster-creation-via-arm.md)
