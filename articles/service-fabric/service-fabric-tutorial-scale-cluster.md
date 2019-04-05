---
title: Service Fabric-fürt skálázása az Azure-ban | Microsoft Docs
description: Ebben az oktatóanyagban elsajátíthatja a Service Fabric-fürt méretezése az Azure-ban.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2019
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 05a30bee8e6eb0db2e06d6d5a3a7af0d0759fb4c
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59049404"
---
# <a name="tutorial-scale-a-service-fabric-cluster-in-azure"></a>Oktatóanyag: Service Fabric-fürt méretezése az Azure-ban

Ez az oktatóanyag egy sorozat harmadik része, és bemutatja, hogyan lehet a meglévő fürtök horizontális fel- és. Az oktatóanyag végére elsajátíthatja a fürtök skálázásának és a hátramaradt erőforrások eltávolításának módját.  Az Azure-ban futó fürt méretezéssel kapcsolatos további információkért olvassa el [méretezése Service Fabric-fürtök](service-fabric-cluster-scaling.md).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Adjon hozzá és távolíthat el csomópontokat (horizontális felskálázás és horizontális leskálázás)
> * Hozzáadhat és eltávolíthat csomóponttípusok (horizontális felskálázás és horizontális leskálázás)
> * Növelje a csomópont erőforrásokat (vertikális felskálázási)

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Hozzon létre egy biztonságos [Windows-fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) az Azure-ban sablon használatával
> * [-Fürt monitorozása](service-fabric-tutorial-monitor-cluster.md)
> * Fürt horizontális fel- és leskálázása
> * [Fürt futtatókörnyezetének frissítése](service-fabric-tutorial-upgrade-cluster.md)
> * [Fürt törlése](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Telepítés [az Azure Powershell](https://docs.microsoft.com/powershell/azure/install-Az-ps) vagy [az Azure CLI](/cli/azure/install-azure-cli).
* Hozzon létre egy biztonságos [Windows-fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) az Azure-ban

## <a name="important-considerations-and-guidelines"></a>Fontos szempontokat és irányelveket

Alkalmazás számítási feladatainak az idő előrehaladtával változik, szükséges a meglévő szolgáltatások további (vagy kevesebb) erőforrások?  [Adjon hozzá vagy távolíthat el csomópontokat](#add-nodes-to-or-remove-nodes-from-a-node-type) egy csomópont írja be a növelése vagy csökkentése érdekében a fürt erőforrásait.

Több mint 100 csomópont hozzáadása a fürthöz kell?  Egy egyetlen Service Fabric-csomópont típusa/méretezési csoportot nem tartalmazhat több mint 100 csomópontok vagy virtuális gépek.  100 csomópont túli fürtök skálázásának [adjon hozzá további csomóponttípusok](#add-nodes-to-or-remove-nodes-from-a-node-type).

Az alkalmazás nem rendelkezik több szolgáltatást, és ezek közül bármelyik szükséges nyilvános vagy az internetre?  Tipikus alkalmazások tartalmazzák egy előtér-átjáró szolgáltatás, amely fogad az ügyféltől érkező bemeneti és a egy vagy több háttér-szolgáltatás, amely kommunikálni a előtér-szolgáltatásokat. Ebben az esetben javasoljuk, hogy [legalább két csomópont-típus hozzáadása](#add-nodes-to-or-remove-nodes-from-a-node-type) a fürthöz.  

Rendelkezik a szolgáltatások különböző infrastruktúrához, például a nagyobb RAM vagy nagyobb CPU-ciklusok? Például az alkalmazás tartalmaz egy előtér-szolgáltatás és a egy háttér-szolgáltatás. Az előtér-szolgáltatás futtatható kisebb rendelkező virtuális gépeken (VM-méretek D2 hasonlóan) a portokat nyissa meg az internethez. A háttérszolgáltatás, azonban nagy számítási igényű és kell futtatni használó virtuális gépeken nagyobb (például: D4, D6, D15 Virtuálisgép-méretek), amelyek nem az internet felé néző. Ebben az esetben javasoljuk, hogy Ön [adjon hozzá két vagy több csomóponttípusok](#add-nodes-to-or-remove-nodes-from-a-node-type) a fürtön. Ez lehetővé teszi, hogy mindegyik csomóponttípus, hogy a különböző tulajdonságai például internetkapcsolat vagy Virtuálisgép-méretet. A virtuális gépek száma skálázhatók egymástól függetlenül, illetve.

Ha méretezése egy Azure-fürtön, vegye figyelembe a következő irányelveket:

* Egy egyetlen Service Fabric-csomópont típusa/méretezési csoportot nem tartalmazhat több mint 100 csomópontok vagy virtuális gépek.  Meghaladja a 100 csomópont virtuálisgép-fürtök skálázásának, adjon hozzá további csomóponttípusok.
* Elsődleges csomóponttípusok éles számítási feladatok futtatásához rendelkeznie kell egy [tartóssági szint] [ durability] , arany és ezüst és mindig legalább öt csomóponttal.
* állapot-nyilvántartó éles számítási feladatok nem elsődleges csomóponttípusok mindig rendelkeznie kell legalább öt csomóponttal.
* állapot nélküli számítási feladatok futtatása nem elsődleges csomóponttípusok mindig rendelkeznie kell legalább két csomóponttal.
* Minden csomópont típusú [tartóssági szint] [ durability] , arany és ezüst mindig rendelkeznie kell legalább öt csomóponttal.
* Ha skálázás (a csomópontok eltávolítására) az elsődleges csomóponttípushoz, kevesebb, mint a példányok száma soha nem érdemes kisebb a [megbízhatósági szint] [ reliability] igényel.

További információkért olvassa el [a fürt kapacitásának útmutatást](service-fabric-cluster-capacity.md).

## <a name="export-the-template-for-the-resource-group"></a>Erőforráscsoport sablonjának exportálása

Miután létrehozott egy biztonságos [Windows-fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) és beállítása sikeresen megtörtént, az erőforráscsoport az erőforráscsoport a Resource Manager-sablon exportálása. A sablon exportálása lehetővé teszi, hogy automatizálja a később üzembe helyezések, a fürt és a hozzá tartozó erőforrás, mert a sablon tartalmazza a teljes infrastruktúra.  További információ a sablonok exportálása, [kezelése az Azure Resource Manager-erőforráscsoportok az Azure portal használatával](/azure/azure-resource-manager/manage-resource-groups-portal).

1. Az a [az Azure portal](https://portal.azure.com), nyissa meg a fürtöt tartalmazó erőforráscsoportot (**sfclustertutorialgroup**, ha az ebben az oktatóanyagban). 

2. A bal oldali panelen válassza ki a **központi telepítések**, vagy jelölje ki a hivatkozást **központi telepítések**. 

3. Válassza ki a legutóbbi sikeres üzembe helyezés a listából.

4. A bal oldali panelen válassza ki a **sablon** majd **letöltése** exportálhatja a sablont egy ZIP-fájlba.  A sablon és paraméterek mentése a helyi számítógépen.

## <a name="add-nodes-to-or-remove-nodes-from-a-node-type"></a>Csomópontok hozzáadása vagy távolít el csomópontokat a csomópont típusa

Skálázás be és ki, vagy a horizontális skálázás módosítja a fürtben található csomópontok számát. Horizontális vagy, a méretezési csoportot további virtuálisgép-példányok hozzá. Ezek a példányok lesznek a Service Fabric által használt csomópontok. A Service Fabric tudja, ha a méretezési csoport új példányokkal bővül (felskálázással), és automatikusan ennek megfelelően jár el. Méretezheti a fürt bármikor, még akkor is, ha a számítási feladatok a fürtön futnak.

### <a name="update-the-template"></a>A sablon frissítéséhez

[Egy sablon és paraméterek fájl exportálása](#export-the-template-for-the-resource-group) a legutóbbi telepítés erőforráscsoportból.  Nyissa meg a *parameters.json* fájlt.  Ha üzembe helyezte a fürtöt használ a [mintasablon] [ template] ebben az oktatóanyagban a fürt és a három paraméter által beállított a csomópontok számát az egyes csomóponttípusok három csomópont típusa van:  *nt0InstanceCount*, *nt1InstanceCount*, és *nt2InstanceCount*.  A *nt1InstanceCount* paramétert, például a példányszám beállítja a második csomópont típusa, és beállítja a virtuális gépek száma a hozzárendelt virtuális gép méretezési.

Igen, az értékét frissítésével a *nt1InstanceCount* írja be a második csomópont csomópontok számának módosításához.  Ne feledje, hogy egy csomópont ki több mint 100 csomópont típusa nem skálázhatja.  állapot-nyilvántartó éles számítási feladatok nem elsődleges csomóponttípusok mindig rendelkeznie kell legalább öt csomóponttal. állapot nélküli számítási feladatok futtatása nem elsődleges csomóponttípusok mindig rendelkeznie kell legalább két csomóponttal.

Ha a méretezéssel csomópontot távolíthat egy, a csomópont típusa bronz [tartóssági szint] [ durability] kell [manuálisan távolítsa el ezeket a csomópontok állapotát](service-fabric-cluster-scale-up-down.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set).  A Silver és Gold tartóssági szint ezeket a lépéseket kell elvégezni automatikusan a platform.

### <a name="deploy-the-updated-template"></a>A frissített sablon üzembe helyezése
Mentse a módosításokat a *template.json* és *parameters.json* fájlokat.  A frissített sablon üzembe helyezéséhez futtassa a következő parancsot:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ChangingInstanceCount"
```
Vagy az Azure CLI-parancsot:
```azure-cli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="add-a-node-type-to-the-cluster"></a>Adja hozzá a fürthöz a csomópont típusa

Meghatározott Azure-ban futó Service Fabric-fürtök minden csomóponttípusa be van állítva, mint egy [külön virtuálisgép-méretezési csoport](service-fabric-cluster-nodetypes.md). Mindegyik csomóponttípus kezelhetők külön-külön. Egymástól függetlenül mindegyik csomóponttípus kisebbre vagy nagyobbra méretezhetők, amelyekre eltérő nyitott portokkal és használni a különböző kapacitási mérőszámot. Módosítsa az operációs rendszer Termékváltozata a fürt minden csomópontján fut, de vegye figyelembe, hogy nem a Windows és Linux rendszerű, a minta-fürtön futó függetlenül is. Egyetlen csomópont típusa és méretezési csoportot nem tartalmazhat több mint 100 csomópont.  További csomópont típusa vagy méretezési csoportjaihoz hozzáadásával horizontálisan egy fürtöt több mint 100 csomópont virtuálisgép skálázhatja. Méretezheti a fürt bármikor, még akkor is, ha a számítási feladatok a fürtön futnak.

### <a name="update-the-template"></a>A sablon frissítéséhez

[Egy sablon és paraméterek fájl exportálása](#export-the-template-for-the-resource-group) a legutóbbi telepítés erőforráscsoportból.  Nyissa meg a *parameters.json* fájlt.  Ha üzembe helyezte a fürtöt használ a [mintasablon] [ template] ebben az oktatóanyagban három csomópont típusa van a fürtben.  Ebben a szakaszban egy negyedik csomóponttípus frissítése és üzembe helyezése Resource Manager-sablon hozzáadása. 

Az új csomópont típusa mellett is adja hozzá a hozzárendelt virtuális gép méretezési (amely futtat egy külön alhálózatot a virtuális hálózat), majd a hálózati biztonsági csoport.  Ha szeretné, adja hozzá az új vagy meglévő nyilvános IP-cím és az Azure load balancer-erőforrások az új méretezési csoportot.  Az új csomópont típus rendelkezik egy [tartóssági szint] [ durability] ezüst és "Standard D2 v2" méretét.

Az a *template.json* fájlt, adja hozzá a következő új paramétereket:
```json
"nt3InstanceCount": {
    "defaultValue": 5,
    "type": "Int",
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType3Size": {
    "defaultValue": "Standard_D2_V2",
    "type": "String"
},
```

Az a *template.json* fájlt, adja hozzá a következő új változókkal:
```json
"lbID3": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
"lbIPConfig3": "[concat(variables('lbID3'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
"lbPoolID3": "[concat(variables('lbID3'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
"lbProbeID3": "[concat(variables('lbID3'),'/probes/FabricGatewayProbe')]",
"lbHttpProbeID3": "[concat(variables('lbID3'),'/probes/FabricHttpGatewayProbe')]",
"lbNatPoolID3": "[concat(variables('lbID3'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
"vmNodeType3Name": "[toLower(concat('NT4', variables('vmName')))]",
"vmStorageAccountName3": "[toLower(concat(uniqueString(resourceGroup().id), '1', '3' ))]",
"nt3applicationStartPort": "20000",
"nt3applicationEndPort": "30000",
"nt3ephemeralStartPort": "49152",
"nt3ephemeralEndPort": "65534",
"nt3fabricTcpGatewayPort": "19000",
"nt3fabricHttpGatewayPort": "19080",
"nt3reverseProxyEndpointPort": "19081",
"subnet3Name": "Subnet-3",
"subnet3Prefix": "10.0.3.0/24",
"subnet3Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet3Name'))]",
```

Az a *template.json* fájlt, adjon hozzá egy új alhálózatot a virtuális hálózati erőforrás:
```json
{
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[variables('addressPrefix')]"
            ]
        },
        "subnets": [
            ...
            {
                "name": "[variables('subnet3Name')]",
                "properties": {
                    "addressPrefix": "[variables('subnet3Prefix')]",
                    "networkSecurityGroup": {
                        "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', variables('subnet3Name')))]"
                    }
                }
            }
        ]
    },
    "dependsOn": [
        ...
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', variables('subnet3Name')))]"
    ]
},
```

Az a *template.json* fájlt, adja hozzá az új nyilvános IP-cím és a load balancer erőforrások:
```json
{
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "dnsSettings": {
            "domainNameLabel": "[concat(variables('dnsName'),'-','nt4')]"
        },
        "publicIPAllocationMethod": "Dynamic"
    }
},
        {
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "frontendIPConfigurations": [
            {
                "name": "LoadBalancerIPConfig",
                "properties": {
                    "publicIPAddress": {
                        "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
                    }
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
            {
                "name": "LBRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "LBHttpRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbHttpProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule1",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort1')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort1')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe1')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule2",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort2')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort2')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe2')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            {
                "name": "FabricGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricTcpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "FabricHttpGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricHttpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe1",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort1')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe2",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort2')]",
                    "protocol": "tcp"
                }
            }
        ],
        "inboundNatPools": [
            {
                "name": "LoadBalancerBEAddressNatPool",
                "properties": {
                    "backendPort": "3389",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPortRangeEnd": "4500",
                    "frontendPortRangeStart": "3389",
                    "protocol": "tcp"
                }
            }
        ]
    },
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
    ]
},
```

Az a *template.json* fájlt, adja hozzá az új hálózati biztonsági csoport és a virtuális gép méretezési csoport erőforrásainak.  A Service Fabric-bővítmény tulajdonságai a virtuális gép méretezési belüli nodetyperef hivatkozással tulajdonság a megadott csomóponttípus a méretezési csoporthoz rendeli hozzá.

```json
{
    "type": "Microsoft.Network/networkSecurityGroups",
    "name": "[concat('nsg', variables('subnet3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[resourceGroup().location]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "securityRules": [
            {
                "name": "allowSvcFabSMB",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "445",
                    "direction": "Inbound",
                    "priority": 3950,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow SMB traffic within the net, used by fabric to move packages around"
                }
            },
            {
                "name": "allowSvcFabCluser",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "1025-1027",
                    "direction": "Inbound",
                    "priority": 3920,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow ports within vnet that are used by the fabric to talk between nodes"
                }
            },
            {
                "name": "allowSvcFabEphemeral",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3ephemeralStartPort'), '-', variables('nt3ephemeralEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3930,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow fabric ephemeral ports within the vnet"
                }
            },
            {
                "name": "allowSvcFabPortal",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricHttpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3900,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster web portal"
                }
            },
            {
                "name": "allowSvcFabClient",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricTcpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3910,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used by the fabric client (includes powershell)"
                }
            },
            {
                "name": "allowSvcFabApplication",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3applicationStartPort'), '-', variables('nt3applicationEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3940,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow fabric application ports within the vnet"
                }
            },
            {
                "name": "blockAll",
                "properties": {
                    "access": "Deny",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "*",
                    "direction": "Inbound",
                    "priority": 4095,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "block all traffic except what we've explicitly allowed"
                }
            },
            {
                "name": "allowVNetRDP",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "3389",
                    "direction": "Inbound",
                    "priority": 3960,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow RDP within the net"
                }
            },
            {
                "name": "allowSvcFabReverseProxy",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3reverseProxyEndpointPort')]",
                    "direction": "Inbound",
                    "priority": 3980,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster using reverse proxy"
                }
            },
            {
                "name": "allowAppPort1",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort1')]",
                    "direction": "Inbound",
                    "priority": 2001,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 1"
                }
            },
            {
                "name": "allowAppPort2",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort2')]",
                    "direction": "Inbound",
                    "priority": 2002,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 2"
                }
            }
        ]
    }
},
{
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "sku": {
        "name": "[parameters('vmNodeType3Size')]",
        "capacity": "[parameters('nt3InstanceCount')]",
        "tier": "Standard"
    },
    "name": "[variables('vmNodeType3Name')]",
    "apiVersion": "2018-10-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
            "mode": "Automatic"
        },
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                    {
                        "name": "[concat(variables('vmNodeType3Name'),'OMS')]",
                        "properties": {
                            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                            "type": "MicrosoftMonitoringAgent",
                            "typeHandlerVersion": "1.0",
                            "autoUpgradeMinorVersion": true,
                            "settings": {
                                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
                            },
                            "protectedSettings": {
                                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
                            }
                        }
                    },
                    {
                        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType3Name')]",
                        "properties": {
                            "type": "ServiceFabricNode",
                            "autoUpgradeMinorVersion": true,
                            "protectedSettings": {
                                "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                                "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                            },
                            "publisher": "Microsoft.Azure.ServiceFabric",
                            "settings": {
                                "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                                "nodeTypeRef": "[variables('vmNodeType3Name')]",
                                "dataPath": "D:\\SvcFab",
                                "durabilityLevel": "Silver",
                                "enableParallelJobs": true,
                                "nicPrefixOverride": "[variables('subnet3Prefix')]",
                                "certificate": {
                                    "thumbprint": "[parameters('certificateThumbprint')]",
                                    "x509StoreName": "[parameters('certificateStoreValue')]"
                                }
                            },
                            "typeHandlerVersion": "1.0"
                        }
                    },
                    {
                        "name": "[concat('VMDiagnosticsVmExt','_vmNodeType3Name')]",
                        "properties": {
                            "type": "IaaSDiagnostics",
                            "autoUpgradeMinorVersion": true,
                            "protectedSettings": {
                                "storageAccountName": "[variables('applicationDiagnosticsStorageAccountName')]",
                                "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
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
                                "StorageAccount": "[variables('applicationDiagnosticsStorageAccountName')]"
                            },
                            "typeHandlerVersion": "1.5"
                        }
                    },
                    {
                        "name": "[concat('VMIaaSAntimalware','_vmNodeType3Name')]",
                        "properties": {
                            "publisher": "Microsoft.Azure.Security",
                            "type": "IaaSAntimalware",
                            "typeHandlerVersion": "1.5",
                            "settings": {
                                "AntimalwareEnabled": "true",
                                "Exclusions": {
                                    "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                                    "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
                                },
                                "RealtimeProtectionEnabled": "true",
                                "ScheduledScanSettings": {
                                    "isEnabled": "true",
                                    "scanType": "Quick",
                                    "day": "7",
                                    "time": "120"
                                }
                            },
                            "protectedSettings": null
                        }
                    }
                ]
            },
            "networkProfile": {
                "networkInterfaceConfigurations": [
                    {
                        "name": "[concat(variables('nicName'), '-2')]",
                        "properties": {
                            "ipConfigurations": [
                                {
                                    "name": "[concat(variables('nicName'),'-',2)]",
                                    "properties": {
                                        "loadBalancerBackendAddressPools": [
                                            {
                                                "id": "[variables('lbPoolID3')]"
                                            }
                                        ],
                                        "loadBalancerInboundNatPools": [
                                            {
                                                "id": "[variables('lbNatPoolID3')]"
                                            }
                                        ],
                                        "subnet": {
                                            "id": "[variables('subnet3Ref')]"
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
                "computernamePrefix": "[variables('vmNodeType3Name')]",
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
    "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
    ]
},
```

Az a *template.json* fájlt, frissítse a fürt erőforrásai, és adjon hozzá egy új csomópont típusa:
```json
{
    "type": "Microsoft.ServiceFabric/clusters",
    "name": "[parameters('clusterName')]",
    "apiVersion": "2018-02-01",
    "location": "[parameters('clusterLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "nodeTypes": [
            ...
            {
                "name": "[variables('vmNodeType3Name')]",
                "applicationPorts": {
                    "endPort": "[variables('nt3applicationEndPort')]",
                    "startPort": "[variables('nt3applicationStartPort')]"
                },
                "clientConnectionEndpointPort": "[variables('nt3fabricTcpGatewayPort')]",
                "durabilityLevel": "Silver",
                "ephemeralPorts": {
                    "endPort": "[variables('nt3ephemeralEndPort')]",
                    "startPort": "[variables('nt3ephemeralStartPort')]"
                },
                "httpGatewayEndpointPort": "[variables('nt3fabricHttpGatewayPort')]",
                "isPrimary": false,
                "reverseProxyEndpointPort": "[variables('nt3reverseProxyEndpointPort')]",
                "vmInstanceCount": "[parameters('nt3InstanceCount')]"
            }
        ],    
    }
}                
```

Az a *parameters.json* fájlt, adja hozzá a következő új paramétereket és értékeket:
```json
"nt3InstanceCount": {
    "Value": 5    
},
"vmNodeType3Size": {
    "Value": "Standard_D2_V2"
},
```

### <a name="deploy-the-updated-template"></a>A frissített sablon üzembe helyezése
Mentse a módosításokat a *template.json* és *parameters.json* fájlokat.  A frissített sablon üzembe helyezéséhez futtassa a következő parancsot:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "AddingNodeType"
```
Vagy az Azure CLI-parancsot:
```azure-cli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="remove-a-node-type-from-the-cluster"></a>A csomóponttípus eltávolítása a fürtből
Egy Service Fabric-fürt létrehozását követően méretezheti a fürt vízszintesen csomópont típusa (virtuálisgép-méretezési) és az összes hozzá tartozó csomópont eltávolításával. Méretezheti a fürt bármikor, még akkor is, ha a számítási feladatok a fürtön futnak. A fürt skálázható, mivel az alkalmazások automatikus méretezése is.

> [!WARNING]
> Typ uzlu eltávolítása egy éles fürtöt Remove-AzServiceFabricNodeType használatával nem ajánlott a gyakran használható. Ez megegyezik a veszélyes parancs törli a virtuális gép méretezési csoport erőforrás mögött typ uzlu. 

A csomóponttípus eltávolításához futtassa a [Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) parancsmagot.  A csomópont típusúnak kell lennie Silver vagy Gold [tartóssági szint] [ durability] a parancsmag törli a méretezési csoporthoz társított a csomópont típusa és eltarthat egy ideig.  Ezután futtassa a [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) parancsmagot a csomópontok szeretne eltávolítani, amely törli a csomópont állapota és a csomópontok eltávolítása a fürtből. Ha a csomópontok szolgáltatásokat, majd a szolgáltatások először kerülnek egy másik csomópontra. A kezelőt nem található a csomópont a replika vagy szolgáltatások, a művelet, késleltetett/blokkolja-e.

```powershell
$groupname = "sfclustertutorialgroup"
$nodetype = "nt4vm"
$clustername = "mysfcluster123"

Remove-AzServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My

$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="increase-node-resources"></a>Csomópont-erőforrások növelése 
Egy Service Fabric-fürt létrehozását követően méretezhetők egy fürtcsomóponttípus függőlegesen (az erőforrásokat a csomópontok módosítása), vagy frissítse az operációs rendszer a csomópont típusú virtuális gépeket.  

> [!WARNING]
> Azt javasoljuk, hogy nem módosítja a virtuális gép Termékváltozata méretezési készlet vagy csomópont típusa csak a Silver szintű tartósságot futó vagy nagyobb. A virtuális gép SKU-méret módosítása egy olyan adatok felülíró helyszíni infrastruktúra művelet. Késleltetés, vagy figyelheti a módosítás néhány képessége nélkül is lehet, hogy a művelet az állapotalapú szolgáltatások esetében adatvesztést is okozhat, vagy akár állapot nélküli számítási feladatok esetében más előre nem látható operatív problémákat okozhat.

> [!WARNING]
> Azt javasoljuk, hogy az elsődleges csomóponttípushoz, amely veszélyes művelet, és nem támogatott Virtuálisgép-Termékváltozat nem módosítja.  Ha a fürtöt nagyobb kapacitásra van szüksége, további Virtuálisgép-példányt, vagy további csomóponttípusok is hozzáadhat.  Ha ez nem lehetséges, egy új fürtöt hozhat létre és [alkalmazásállapot visszaállítása](service-fabric-reliable-services-backup-restore.md) (ha van ilyen) a régi fürtről.  Ha ez nem lehetséges, [módosítsa a virtuális gép Termékváltozata az elsődleges csomóponttípushoz](service-fabric-scale-up-node-type.md).

### <a name="update-the-template"></a>A sablon frissítéséhez

[Egy sablon és paraméterek fájl exportálása](#export-the-template-for-the-resource-group) a legutóbbi telepítés erőforráscsoportból.  Nyissa meg a *parameters.json* fájlt.  Ha üzembe helyezte a fürtöt használ a [mintasablon] [ template] ebben az oktatóanyagban három csomópont típusa van a fürtben.  

A második csomópont írja be a virtuális gépek méretének állítja be a *vmNodeType1Size* paraméter.  Módosítsa a *vmNodeType1Size* paraméter értékét a standard D2 v2 [standard D3 v2](/azure/virtual-machines/windows/sizes-general#dv2-series), amely megduplázza az erőforrásokat az egyes Virtuálisgép-példányok.

A VM-Termékváltozat minden három csomópont esetében be van állítva a *vmImageSku* paraméter.  Ismét typ uzlu VM-Termékváltozat módosítása körültekintően kell megközelíthető és az elsődleges csomóponttípushoz nem ajánlott.

### <a name="deploy-the-updated-template"></a>A frissített sablon üzembe helyezése
Mentse a módosításokat a *template.json* és *parameters.json* fájlokat.  A frissített sablon üzembe helyezéséhez futtassa a következő parancsot:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ScaleUpNodeType"
```
Vagy az Azure CLI-parancsot:
```azure-cli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Adjon hozzá és távolíthat el csomópontokat (horizontális felskálázás és horizontális leskálázás)
> * Hozzáadhat és eltávolíthat csomóponttípusok (horizontális felskálázás és horizontális leskálázás)
> * Növelje a csomópont erőforrásokat (vertikális felskálázási)

Folytassa a következő oktatóanyaggal, amelyben megismerheti, hogyan frissíthetők a fürtök futtatókörnyezetei.
> [!div class="nextstepaction"]
> [Fürt futtatókörnyezetének frissítése](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
ND horizontális leskálázás))
> * Hozzáadhat és eltávolíthat csomóponttípusok (horizontális felskálázás és horizontális leskálázás)
> * Növelje a csomópont erőforrásokat (vertikális felskálázási)

Folytassa a következő oktatóanyaggal, amelyben megismerheti, hogyan frissíthetők a fürtök futtatókörnyezetei.
> [!div class="nextstepaction"]
> [Fürt futtatókörnyezetének frissítése](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
