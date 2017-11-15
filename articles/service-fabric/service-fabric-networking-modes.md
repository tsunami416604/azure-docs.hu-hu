---
title: "Az Azure Service Fabric tárolószolgáltatások hálózati módok konfigurálása |} Microsoft Docs"
description: "Útmutató a hálózati mód, amely támogatja az Azure Service Fabric beállítása."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 855e315f66858210875039f91f7f05055ff7d9b9
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/11/2017
---
# <a name="service-fabric-container-networking-modes"></a>A Service Fabric tároló hálózati módok

A hálózati módban érhető el a Service Fabric-fürt tárolószolgáltatások az alapértelmezett érték a `nat` hálózati mód. Az a `nat` hálózati mód, hogy az azonos port eredményeket a központi telepítési hibái figyel egynél több tároló szolgáltatás. Rendszert futtató több szolgáltatási adott figyelési ugyanazt a portot, a Service Fabric támogatja a `Open` hálózati mód (5.7-es vagy újabb verzió). Az a `Open` hálózati mód, minden tárolószolgáltatás beolvasása dinamikusan hozzárendelt IP-címnek belső így több szolgáltatás ugyanazt a portot figyeli.   

Így egyetlen szolgáltatástípus egy statikus végponttal, a szolgáltatás jegyzékben meghatározott, az új szolgáltatások előfordulhat, hogy hozható létre és használatával, a telepítési hibák nélkül törli a `Open` hálózati mód. Hasonlóképpen, egy használhatja ugyanazt `docker-compose.yml` több szolgáltatások létrehozásának statikus fájlt.

A dinamikusan kiosztott IP-használatával felderítéséhez a szolgáltatások esetén nem javasolt óta az IP-címet érintő módosításait a szolgáltatás újraindul, vagy egy másik csomópontjára helyezi át. Csak a **Service Fabric-szolgáltatás** vagy a **DNS-szolgáltatás** a szolgáltatás felderítése. 


> [!WARNING]
> Az Azure virtuális hálózat csak 4096 IP-címek összesen használható. Ebből kifolyólag a csomópontok száma és a tároló szolgáltatás példányainak száma összege (a `Open` hálózati) legfeljebb 4096 történik egy Vneten belül. Ilyen nagy sűrűségű forgatókönyvek esetén a `nat` hálózati mód használata ajánlott.
>

## <a name="setting-up-open-networking-mode"></a>Nyissa meg a hálózati mód beállítása

1. DNS-szolgáltatás és az IP-szolgáltató a engedélyezésével az Azure Resource Manager-sablon beállítása `fabricSettings`. 

    ```json
    "fabricSettings": [
                {
                    "name": "DnsService",
                    "parameters": [
                       {
                            "name": "IsEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Hosting",
                    "parameters": [
                      { 
                            "name": "IPProviderEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name":  "Trace/Etw", 
                    "parameters": [
                    {
                            "name": "Level",
                            "value": "5"
                    }
                    ]
                },
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "ContainerNetworkSetup",
                            "value": "true"
                    }
                    ]
                }
            ],
    ```

2. A hálózati profil szakasz beállítása lehetővé teszi több IP-címmel kell konfigurálni a fürt mindegyik csomópontján. Az alábbi példa beállítja az öt IP-címek csomópontonként (így a minden egyes csomóponton portot öt szolgáltatáspéldány rendelkezhet) Windows/Linux Service Fabric-fürt.

    ```json
    "variables": {
        "nicName": "NIC",
        "vmName": "vm",
        "virtualNetworkName": "VNet",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "vmNodeType0Name": "[toLower(concat('NT1', variables('vmName')))]",
        "subnet0Name": "Subnet-0",
        "subnet0Prefix": "10.0.0.0/24",
        "subnet0Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet0Name'))]",
        "lbID0": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType0Name')))]",
        "lbIPConfig0": "[concat(variables('lbID0'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
        "lbPoolID0": "[concat(variables('lbID0'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
        "lbProbeID0": "[concat(variables('lbID0'),'/probes/FabricGatewayProbe')]",
        "lbHttpProbeID0": "[concat(variables('lbID0'),'/probes/FabricHttpGatewayProbe')]",
        "lbNatPoolID0": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]"
    }
    "networkProfile": {
                "networkInterfaceConfigurations": [
                  {
                    "name": "[concat(parameters('nicName'), '-0')]",
                    "properties": {
                      "ipConfigurations": [
                        {
                          "name": "[concat(parameters('nicName'),'-',0)]",
                          "properties": {
                            "primary": "true",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "loadBalancerInboundNatPools": [
                              {
                                "id": "[variables('lbNatPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 1)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 2)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 3)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 4)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 5)]",
                          "properties": {
                            "primary": "false",
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
              }
   ```
 

3. Csak Windows-fürtök esetén beállítása egy NSG-szabály az alábbi értékeket a vnet UDP/53-as port megnyitására:

   | Prioritás |    Név    |    Forrás      |  Cél   |   Szolgáltatás    | Műveletek |
   |:--------:|:----------:|:--------------:|:--------------:|:------------:|:------:|
   |     2000 | Custom_Dns | VirtualNetwork | VirtualNetwork | DNS (UDP/53) | Engedélyezés  |


4. Adja meg a hálózati mód az alkalmazásjegyzéket az egyes szolgáltatásokhoz `<NetworkConfig NetworkType="Open">`.  A mód `Open` eredményezi, a szolgáltatás egy dedikált IP-cím beolvasása. Ha egy mód nincs megadva, a basic alapértelmezett `nat` mód. Így az alábbi jegyzék `NodeContainerServicePackage1` és `NodeContainerServicePackage2` is ugyanazt a portot minden figyelése (mindkét szolgáltatás által figyelt `Endpoint1`). Ha a `Open` hálózati mód Sorozatszámcsoport, `PortBinding` configs nem adható meg.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ApplicationManifest ApplicationTypeName="NodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
      <Description>Calculator Application</Description>
      <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
        <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      </Parameters>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage1" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService1.Code" Isolation="hyperv">
           <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage2" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService2.Code" Isolation="default">
            <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
    </ApplicationManifest>
    ```
Ön szabadon kombinálhatók hálózati mód szolgáltatásban egy Windows-fürt számára az alkalmazáson belül. Ebből kifolyólag lehet néhány szolgáltatás `Open` mód és az egyes `nat` hálózati mód. Ha a szolgáltatás beállítása a `nat`, a port a figyeléshez egyedinek kell lennie. Különböző szolgáltatások hálózati módjainak keverése nem támogatott Linux fürtökön. 


## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megtanulta, a Service Fabric által kínált módok hálózati.  

* [A Service Fabric alkalmazásmodellt.](service-fabric-application-model.md)
* [A Service Fabric service manifest erőforrások](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-service-manifest-resources)
* [A Service Fabric Windows Server 2016 egy Windows-tároló telepítése](service-fabric-get-started-containers.md)
* [Telepítse a Service Fabric Linux egy Docker-tároló](service-fabric-get-started-containers-linux.md)
