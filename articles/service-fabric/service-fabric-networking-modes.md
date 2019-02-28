---
title: Konfigurálja az Azure Service Fabric-tároló szolgáltatások hálózatkezelés módjai |} A Microsoft Docs
description: Ismerje meg, hogyan állítható be az Azure Service Fabric által támogatott különböző hálózatkezelés módjai.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: aljo, subramar
ms.openlocfilehash: cb918516a9180b4bffdc72c688df5d830e83833a
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56959663"
---
# <a name="service-fabric-container-networking-modes"></a>A Service Fabric tárolóalapú hálózatkezelés módjai

Azure Service Fabric-fürt, a container services használ **nat** hálózati mód alapértelmezés szerint. Üzembe helyezési hibák akkor fordulhatnak elő, amikor egynél több tárolószolgáltatás ugyanazt a portot figyeli, és a nat-módot használja. Támogatja a több tárolószolgáltatások ugyanazt a portot figyeli, hogy a Service Fabric kínál **nyílt** hálózati mód (5.7 és újabb verziók). Nyissa meg a módban minden tároló szolgáltatásnak van egy belső dinamikusan kiosztott IP-címet, amely támogatja a több szolgáltatás ugyanazt a portot figyeli.  

Ha egy container service-ben egy statikus végpont a szolgáltatásjegyzékben, hozzon létre, és nyissa meg az üzembe helyezési hibák nélkül módot az új szolgáltatások törlése. Ugyanazt a docker-compose.yml fájlt is használható a statikus portleképezéseit több szolgáltatást létrehozni.

Ha egy tárolószolgáltatás újraindítja vagy áthelyezi egy másik csomópontra a fürtben, az IP-cím megváltozik. Ezen okból kifolyólag nem ajánlott a dinamikusan kiosztott IP-cím használatával próbálják felderíteni, container services. Csak a Service Fabric elnevezési szolgáltatásban, vagy a DNS-szolgáltatás szolgáltatásfelderítési használandó. 

>[!WARNING]
>Az Azure lehetővé teszi, hogy 65356 IP-címek száma virtuális hálózatonként összesen. A csomópontok számát és a container service-példányok (, nyissa meg az módot használ) nem lehet hosszabb 65356 IP-címek egy virtuális hálózaton belül. Nagy sűrűségű forgatókönyvek esetén ajánlott nat hálózati mód. Emellett egyéb függőségek, például a terheléselosztó lesz más [korlátozások](https://docs.microsoft.com/en-us/azure/azure-subscription-service-limits) kell figyelembe venni. A jelenleg legfeljebb 50 IPs csomópontonként teszteltük és bevált stabil. 
>

## <a name="set-up-open-networking-mode"></a>Nyissa meg a hálózati mód beállítása

1. Az Azure Resource Manager-sablon beállítása. Az a **fabricSettings** szakasz a fürterőforrás engedélyezi a DNS-szolgáltatás és az IP-szolgáltató: 

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
    
2. Állítsa be a hálózati profil szakaszt a Virtual Machine Scale Set erőforrás. Ez lehetővé teszi a több IP-címmel kell konfigurálni a fürt mindegyik csomópontján. Az alábbi példában öt IP-cím / Windows/Linux Service Fabric-fürt csomópontja beállítja. Minden egyes csomóponton porton öt szolgáltatáspéldányok rendelkezhet. Ahhoz, hogy érhető el az Azure Load Balancerhez öt IP-cím, regisztrálni az Azure Load Balancer Háttércímkészlethez öt IP-cím alább látható módon.  Emellett a sablon a változók szakaszban elejéhez adja hozzá a változókat kell.

    Ez a szakasz hozzá változókat:

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
    ```
    
    Ez a szakasz a Virtual Machine Scale Set erőforrás adja hozzá:

    ```json   
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
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 2)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 3)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 4)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 5)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
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
              }
   ```
 
3. Csak Windows-fürtök esetén a következő értékeket a virtuális hálózat UDP/53-as port a megnyíló Azure hálózati biztonsági csoport (NSG) szabály beállítása:

   |Beállítás |Érték | |
   | --- | --- | --- |
   |Prioritás |2000 | |
   |Name (Név) |Custom_Dns  | |
   |Forrás |VirtualNetwork | |
   |Cél | VirtualNetwork | |
   |Szolgáltatás | DNS (UDP/53) | |
   |Műveletek | Engedélyezés  | |
   | | |

4. Adja meg a hálózati módját az egyes szolgáltatások az alkalmazásjegyzékben: `<NetworkConfig NetworkType="Open">`. **Nyissa meg** hálózatkezelés mód eredményez a szolgáltatás egy dedikált IP-cím beolvasása. Ha egy mód nincs megadva, a szolgáltatás alapértelmezett **nat** mód. A következő jegyzékfájl példában a `NodeContainerServicePackage1` és `NodeContainerServicePackage2` szolgáltatások is minden egyes figyelő ugyanazt a portot (mindkét szolgáltatás által figyelt `Endpoint1`). Ha hálózati módban nyissa meg van adva, `PortBinding` konfigurációk nem adható meg.

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

    Ön szabadon kombinálhatók eltérő hálózatkezelés módjai el a Windows-fürt az alkalmazáson belül. Egyes szolgáltatások nyílt mód használható, míg mások a nat-módot használja. Ha egy szolgáltatás nat mód használatára van konfigurálva, a port, amelyet a szolgáltatás figyel a következőn: egyedinek kell lennie.

    >[!NOTE]
    >Linux-fürtökön keverése hálózatkezelés módjai különböző szolgáltatások esetében nem támogatott. 
    >

5. Ha a **nyílt** mód be van jelölve, a **végpont** meghatározása a szolgáltatásjegyzékben mutasson a kívánt végpontnak megfelelő a kódcsomag explicit módon kell, akkor is, ha a csomag csak egy kódot tartalmaz Ez a csomag. 
   
   ```xml
   <Resources>
     <Endpoints>
       <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" CodePackageRef="Code"/>
     </Endpoints>
   </Resources>
   ```
   
6. A Windows a virtuális gép újraindítását miatt nyissa meg a hálózat, újra létre kell hozni. Ez az a hálózati verem az alapul szolgáló elhárításukra. Az alapértelmezett viselkedést, hogy hozza létre újra a hálózaton. Ha ezt a viselkedést ki van kapcsolva, a következő konfiguráció használható a konfiguráció frissítése követi.

```json
"fabricSettings": [
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "SkipContainerNetworkResetOnReboot",
                            "value": "true"
                    }
                    ]
                }
            ],          
 ``` 
 
## <a name="next-steps"></a>További lépések
* [A Service Fabric-alkalmazásmodell megismerése](service-fabric-application-model.md)
* [További információ a Service Fabric szolgáltatásjegyzéki erőforrások](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources)
* [Egy Windows-tároló üzembe helyezése a Service Fabric Windows Server 2016-on](service-fabric-get-started-containers.md)
* [Egy Docker-tároló üzembe helyezése a Service fabric Linux rendszeren](service-fabric-get-started-containers-linux.md)
