---
title: Service Fabric-fürt skálázása az Azure-ban
description: Ebben az oktatóanyagban megtudhatja, hogyan skálázhatja a Service Fabric-fürt az Azure-ban és be, és hogyan kell megtisztítani a maradék erőforrásokat.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: f1b813576a94541cdc2ab0a67fea71b6f49696c5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78251797"
---
# <a name="tutorial-scale-a-service-fabric-cluster-in-azure"></a>Oktatóanyag: Service Fabric-fürt skálázása az Azure-ban

Ez az oktatóanyag egy sorozat harmadik része, és bemutatja, hogyan méretezheti ki és be a meglévő fürtöt. Az oktatóanyag végére elsajátíthatja a fürtök skálázásának és a hátramaradt erőforrások eltávolításának módját.  Az Azure-ban futó fürt méretezése című további információért olvassa el [a Méretezési szolgáltatás fabric-fürtjeit.](service-fabric-cluster-scaling.md)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Csomópontok hozzáadása és eltávolítása (kicsinyítés és méretezés)
> * Csomóponttípusok hozzáadása és eltávolítása (horizontális felskálázás és méretezés)
> * Csomópont-erőforrások növelése (felskálázás)

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Biztonságos [Windows-fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) létrehozása az Azure-ban sablon használatával
> * [Fürt figyelése](service-fabric-tutorial-monitor-cluster.md)
> * Fürt horizontális fel- és leskálázása
> * [Fürt futtatókörnyezetének frissítése](service-fabric-tutorial-upgrade-cluster.md)
> * [Fürt törlése](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Telepítse [az Azure PowerShellt](https://docs.microsoft.com/powershell/azure/install-Az-ps) vagy [az Azure CLI-t.](/cli/azure/install-azure-cli)
* Biztonságos [Windows-fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) létrehozása az Azure-ban

## <a name="important-considerations-and-guidelines"></a>Fontos szempontok és iránymutatások

Az alkalmazás-munkaterhelések idővel változnak, a meglévő szolgáltatásoknak több (vagy kevesebb) erőforrásra van szükségük?  [Csomópontok hozzáadása vagy eltávolítása](#add-nodes-to-or-remove-nodes-from-a-node-type) csomóponttípusból a fürterőforrások növeléséhez vagy csökkentéséhez.

Több mint 100 csomópontot kell hozzáadnia a fürthöz?  Egyetlen Service Fabric-csomópont típusa/méretezési csoportja nem tartalmazhat 100-nál több csomópontot/virtuális gépet.  Ha egy fürtöt 100 csomóponton túl szeretne méretezni, [adjon hozzá további csomóponttípusokat.](#add-nodes-to-or-remove-nodes-from-a-node-type)

Az alkalmazás több szolgáltatással rendelkezik, és bármelyiknek nyilvánosnak vagy internettel szemben kell lennie?  A tipikus alkalmazások egy előtér-átjárószolgáltatást tartalmaznak, amely bemenetet fogad egy ügyféltől és egy vagy több háttérszolgáltatástól, amelyek az előtér-szolgáltatásokkal kommunikálnak. Ebben az esetben azt javasoljuk, hogy [legalább két csomóponttípust adjon hozzá](#add-nodes-to-or-remove-nodes-from-a-node-type) a fürthöz.  

A szolgáltatások különböző infrastrukturális igényeket, például nagyobb RAM vagy nagyobb CPU-ciklusok? Például az alkalmazás tartalmaz egy előtér-szolgáltatás és egy háttér-szolgáltatás. Az előtér-szolgáltatás kisebb virtuális gépeken (például D2-hez hasonló virtuális gépeken) futtatható, amelyek portjai nyitva állnak az interneten. A háttérszolgáltatás azonban számításigényes, és nagyobb virtuális gépeken kell futnia (olyan virtuális gépméretekkel, mint a D4, D6, D15), amelyek nem az internetfelé néznek. Ebben az esetben azt javasoljuk, hogy [adjon hozzá két vagy több csomóponttípust](#add-nodes-to-or-remove-nodes-from-a-node-type) a fürthöz. Ez lehetővé teszi, hogy minden csomóponttípus különböző tulajdonságokkal rendelkezik, például az internetkapcsolattal vagy a virtuális gép méretével. A virtuális gépek száma egymástól függetlenül is méretezhető.

Egy Azure-fürt méretezésekénél tartsa szem előtt az alábbi irányelveket:

* Egyetlen Service Fabric-csomópont típusa/méretezési csoportja nem tartalmazhat 100-nál több csomópontot/virtuális gépet.  Ha egy fürtöt 100 csomóponton túl szeretne méretezni, adjon hozzá további csomóponttípusokat.
* Az éles számítási feladatokat futtató elsődleges csomóponttípusok [tartóssági szintje][durability] Arany vagy Ezüst, és mindig öt vagy több csomót.
* Az állapotalapú éles számítási feladatokat futtató nem elsődleges csomóponttípusoknak mindig öt vagy több csomót kell rendelkezniük.
* Állapot nélküli éles számítási feladatokat futtató nem elsődleges csomóponttípusokmindig két vagy több csomót.
* Az Arany vagy ezüst [bármilyen csomóponttípusának][durability] mindig öt vagy több csomóval kell rendelkeznie.
* Ha egy elsődleges csomóponttípus méretezése (csomópontok eltávolítása) esetén soha nem szabad a példányok számát a [megbízhatósági szint nél][reliability] kisebbre csökkenteni.

További információkért olvassa el a [fürt kapacitásra vonatkozó útmutatóját.](service-fabric-cluster-capacity.md)

## <a name="export-the-template-for-the-resource-group"></a>Erőforráscsoport sablonjának exportálása

Miután létrehozott egy biztonságos [Windows-fürtöt,](service-fabric-tutorial-create-vnet-and-windows-cluster.md) és sikeresen bebiztosította az erőforráscsoportot, exportálja az erőforráscsoport Erőforrás-kezelő sablonját. A sablon exportálása lehetővé teszi a fürt és erőforrásai jövőbeli üzembe helyezésének automatizálását, mivel a sablon tartalmazza az összes teljes infrastruktúrát.  A sablonok exportálásával kapcsolatos további információkért olvassa el [az Azure Resource Manager erőforráscsoportok kezelése az Azure Portal használatával](/azure/azure-resource-manager/manage-resource-groups-portal)című olvasni.

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a fürtöt (**sfclustertutorialgroup**, ha az oktatóanyagot) tartalmazó erőforráscsoport. 

2. A bal oldali ablaktáblában válassza a **Központi telepítések**lehetőséget, vagy válassza a hivatkozást a **Telepítések csoportban.** 

3. Válassza ki a legutóbbi sikeres telepítést a listából.

4. A bal oldali ablaktáblában válassza a **Sablon** lehetőséget, majd a **Letöltés** lehetőséget a sablon ZIP-fájlként történő exportálásához.  Mentse a sablont és a paramétereket a helyi számítógépre.

## <a name="add-nodes-to-or-remove-nodes-from-a-node-type"></a>Csomópontok hozzáadása vagy csomóponttípus eltávolítása

A be- és kiskálázás, illetve a horizontális méretezés módosítja a csomópontok számát a fürtben. A méretezéskor vagy a kiskálázáskor további virtuálisgép-példányokat ad hozzá a méretezési csoporthoz. Ezek a példányok lesznek a Service Fabric által használt csomópontok. A Service Fabric tudja, ha a méretezési csoport új példányokkal bővül (felskálázással), és automatikusan ennek megfelelően jár el. A fürt bármikor méretezhető, még akkor is, ha a számítási feladatok futnak a fürtön.

### <a name="update-the-template"></a>A sablon frissítése

[Sablon- és paraméterfájl exportálása](#export-the-template-for-the-resource-group) az erőforráscsoportból a legutóbbi telepítéshez.  Nyissa meg a *parameters.json* fájlt.  Ha a fürtöt az oktatóanyag [mintasablonjával][template] telepítette, a fürtben három csomóponttípus és három paraméter határozza meg az egyes csomóponttípusok csomópontjainak számát: *nt0InstanceCount*, *nt1InstanceCount*és *nt2InstanceCount*.  Az *nt1InstanceCount* paraméter például beállítja a második csomóponttípus példányszámát, és beállítja a virtuális gépek számát a társított virtuálisgép-méretezési csoportban.

Így az *nt1InstanceCount* értékének frissítésével módosítja a csomópontok számát a második csomóponttípusban.  Ne feledje, hogy egy csomóponttípus nem méretezhető 100-nál több csomópontra.  Az állapotalapú éles számítási feladatokat futtató nem elsődleges csomóponttípusoknak mindig öt vagy több csomót kell rendelkezniük. Állapot nélküli éles számítási feladatokat futtató nem elsődleges csomóponttípusokmindig két vagy több csomót.

Ha méretezés, csomópontok eltávolítása, a csomópont típusa Bronz [tartóssági szint][durability] manuálisan el kell [távolítani a csomópontok állapotát.](service-fabric-cluster-scale-up-down.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set)  Az Ezüst és arany tartóssági szint esetében ezeket a lépéseket a platform automatikusan elvégezheti.

### <a name="deploy-the-updated-template"></a>A frissített sablon telepítése
Mentse a *template.json* és a *parameters.json fájlok módosításait.*  A frissített sablon telepítéséhez futtassa a következő parancsot:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ChangingInstanceCount"
```
Vagy a következő Azure CLI parancs:
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="add-a-node-type-to-the-cluster"></a>Csomóponttípus hozzáadása a fürthöz

Az Azure-ban futó Service Fabric-fürtben definiált minden csomóponttípus [külön virtuálisgép-méretezési csoportként van beállítva.](service-fabric-cluster-nodetypes.md) Ezután minden csomóponttípus külön-külön kezelhető. Az egyes csomóponttípusokat egymástól függetlenül skálázhatja fel- vagy le, különböző portkészleteket nyithat meg, és különböző kapacitásmutatókat használhat. Az egyes fürtcsomópontokon futó operációsrendszer-termékváltozatot is egymástól függetlenül módosíthatja, de vegye figyelembe, hogy a mintafürtben nem futtatható windowsos és Linux-csoport. Egyetlen csomóponttípus/méretezési csoport nem tartalmazhat 100-nál több csomópontot.  A fürt vízszintesen több mint 100 csomópontra méretezhető további csomóponttípusok/méretezési készletek hozzáadásával. A fürt bármikor méretezhető, még akkor is, ha a számítási feladatok futnak a fürtön.

### <a name="update-the-template"></a>A sablon frissítése

[Sablon- és paraméterfájl exportálása](#export-the-template-for-the-resource-group) az erőforráscsoportból a legutóbbi telepítéshez.  Nyissa meg a *parameters.json* fájlt.  Ha a fürtöt az [oktatóanyagmintasablonhasználatával][template] telepítette, három csomóponttípus van a fürtben.  Ebben a szakaszban egy negyedik csomóponttípust ad hozzá egy Erőforrás-kezelő sablon frissítésével és telepítésével. 

Az új csomóponttípus mellett hozzá adja a társított virtuálisgép-méretezési csoportot (amely a virtuális hálózat külön alhálózatában fut) és a hálózati biztonsági csoportot is.  Választhat, hogy új vagy meglévő nyilvános IP-cím és az Azure terheléselosztó erőforrásokat az új méretezési csoport.  Az új csomóponttípus [tartóssági szintje][durability] Ezüst, mérete "Standard_D2_V2".

A *template.json* fájlban adja hozzá a következő új paramétereket:
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

A *template.json* fájlban adja hozzá a következő új változókat:
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

A *template.json* fájlban adjon hozzá egy új alhálózatot a virtuális hálózati erőforráshoz:
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

A *template.json* fájlban adjon hozzá új nyilvános IP-cím- és terheléselosztó-erőforrásokat:
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

A *template.json* fájlban adjon hozzá új hálózati biztonsági csoportot és virtuálisgép-méretezési készleterőforrásokat.  A virtuálisgép-méretezési csoport Service Fabric bővítménytulajdonságaiban található NodeTypeRef tulajdonság leképezi a megadott csomóponttípust a méretezési készlethez.

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

A *template.json* fájlban frissítse a fürterőforrást, és adjon hozzá egy új csomóponttípust:
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

A *parameters.json* fájlban adja hozzá a következő új paramétereket és értékeket:
```json
"nt3InstanceCount": {
    "Value": 5    
},
"vmNodeType3Size": {
    "Value": "Standard_D2_V2"
},
```

### <a name="deploy-the-updated-template"></a>A frissített sablon telepítése
Mentse a *template.json* és a *parameters.json fájlok módosításait.*  A frissített sablon telepítéséhez futtassa a következő parancsot:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "AddingNodeType"
```
Vagy a következő Azure CLI parancs:
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="remove-a-node-type-from-the-cluster"></a>Csomóponttípus eltávolítása a fürtből
A Service Fabric-fürt létrehozása után vízszintesen skálázhatja a fürtöt egy csomóponttípus (virtuálisgép-méretezési csoport) és annak összes csomópontjának eltávolításával. A fürt bármikor méretezhető, még akkor is, ha a számítási feladatok futnak a fürtön. A fürt méretezése, az alkalmazások automatikusan skálázható is.

> [!WARNING]
> Az Remove-AzServiceFabricNodeType használatával csomóponttípus t nem ajánlott gyakran használni. Ez egy veszélyes parancs, mivel törli a virtuális gép méretezési készlet erőforrás mögött a csomópont típusát. 

A csomóponttípus eltávolításához futtassa az [Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) parancsmavet.  A csomóponttípusnak ezüst vagy arany [tartóssági szintnek][durability] kell lennie: A parancsmag törli a csomóponttípushoz társított méretezési készletet, és némi időt vesz igénybe.  Ezután futtassa az [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) parancsmast az eltávolítandó csomópontok mindegyikén, amely törli a csomópont állapotát, és eltávolítja a csomópontokat a fürtből. Ha vannak szolgáltatások a csomópontokon, akkor a szolgáltatások először egy másik csomópontra kerülnek. Ha a fürtkezelő nem talál csomópontot a replika/szolgáltatás számára, akkor a művelet késik/blokkolva van.

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
A Service Fabric-fürt létrehozása után függőlegesen skálázhatja a fürtcsomópont típusát (módosíthatja a csomópontok erőforrásait), vagy frissítheti a csomópont típusú virtuális gépek operációs rendszerét.  

> [!WARNING]
> Azt javasoljuk, hogy ne módosítsa a virtuális gép termékváltozata egy méretezési csoport/csomópont típusú, kivéve, ha silver tartóssági vagy nagyobb tartóssági fut. A virtuális gép termékváltozatának mérete egy adat-destruktív helyszíni infrastruktúra-művelet módosítása. Anélkül, hogy a változás késleltethető vagy figyelhető lenne, lehetséges, hogy a művelet adatvesztést okozhat az állapotalapú szolgáltatások számára, vagy más előre nem látható működési problémákat okozhat, még állapotnélküli munkaterhelések esetén is.

> [!WARNING]
> Azt javasoljuk, hogy ne módosítsa a virtuális gép termékváltozatának az elsődleges csomópont típus, amely egy veszélyes művelet, és nem támogatott.  Ha több fürtkapacitásra van szüksége, további virtuálisgép-példányokat vagy további csomóponttípusokat adhat hozzá.  Ha ez nem lehetséges, létrehozhat egy új fürtöt, és [visszaállíthatja](service-fabric-reliable-services-backup-restore.md) az alkalmazás állapotát (ha van ilyen) a régi fürtből.  Ha ez nem lehetséges, [módosíthatja az elsődleges csomóponttípus virtuálisgép-termékváltozatát.](service-fabric-scale-up-node-type.md)

### <a name="update-the-template"></a>A sablon frissítése

[Sablon- és paraméterfájl exportálása](#export-the-template-for-the-resource-group) az erőforráscsoportból a legutóbbi telepítéshez.  Nyissa meg a *parameters.json* fájlt.  Ha a fürtöt az [oktatóanyagmintasablonhasználatával][template] telepítette, három csomóponttípus van a fürtben.  

A második csomóponttípusban lévő virtuális gépek mérete a *vmNodeType1Size* paraméterben van beállítva.  Módosítsa a *vmNodeType1Size* paraméter értékét Standard_D2_V2-ről [Standard_D3_V2,](../virtual-machines/dv2-dsv2-series.md)amely megduplázza az egyes virtuálisgép-példányok erőforrásait.

A virtuális gép termékváltozat mindhárom csomóponttípushoz a *vmImageSku* paraméterben van beállítva.  Ismét a csomóponttípusú virtuálisgép-termékváltozat módosítását óvatosan kell megközelíteni, és az elsődleges csomóponttípushoz nem ajánlott.

### <a name="deploy-the-updated-template"></a>A frissített sablon telepítése
Mentse a *template.json* és a *parameters.json fájlok módosításait.*  A frissített sablon telepítéséhez futtassa a következő parancsot:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ScaleUpNodeType"
```
Vagy a következő Azure CLI parancs:
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Csomópontok hozzáadása és eltávolítása (kicsinyítés és méretezés)
> * Csomóponttípusok hozzáadása és eltávolítása (horizontális felskálázás és méretezés)
> * Csomópont-erőforrások növelése (felskálázás)

Folytassa a következő oktatóanyaggal, amelyben megismerheti, hogyan frissíthetők a fürtök futtatókörnyezetei.
> [!div class="nextstepaction"]
> [Fürt futtatókörnyezetének frissítése](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json

> * Csomóponttípusok hozzáadása és eltávolítása (horizontális felskálázás és méretezés)
> * Csomópont-erőforrások növelése (felskálázás)

Folytassa a következő oktatóanyaggal, amelyben megismerheti, hogyan frissíthetők a fürtök futtatókörnyezetei.
> [!div class="nextstepaction"]
> [Fürt futtatókörnyezetének frissítése](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
