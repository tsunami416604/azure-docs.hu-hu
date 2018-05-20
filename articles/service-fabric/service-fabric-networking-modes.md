---
title: Az Azure Service Fabric tárolószolgáltatások hálózati módok konfigurálása |} Microsoft Docs
description: Ismerje meg, hogyan állíthat be a különböző hálózati módok Azure Service Fabric által támogatott.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: f831c046bcf8f633841f9dc4a0fce6d1e419e6c2
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-container-networking-modes"></a>A Service Fabric tároló hálózati módok

Egy tároló Azure Service Fabric-fürt által használt szolgáltatások **nat** hálózati mód alapértelmezés szerint. Ha egynél több tárolószolgáltatás ugyanazt a portot figyeli, és a nat-mód használatára, központi telepítési hibák adódhatnak. Ugyanazon a porton figyel több tároló szolgáltatásokat támogatja, a Service Fabric kínál **nyitott** hálózati mód (5.7 és újabb verziók). Megnyitási módot minden egyes tárolószolgáltatás van egy belső dinamikusan hozzárendelt IP-címet, amely támogatja a több szolgáltatás ugyanazon a porton figyel.  

Ha a szolgáltatás jegyzékfájlban egy tárolószolgáltatás egy statikus végponttal rendelkezik, hozzon létre, és törölje a új szolgáltatások telepítési hiba nélkül megnyitási módot használatával. Ugyanazt a docker-compose.yml fájlt is használható a statikus több szolgáltatások létrehozására.

A tárolószolgáltatás újraindítja vagy a fürt egy másik csomópontjára helyezi át, ha megváltozik az IP-cím. Emiatt nem ajánlott a dinamikusan kiosztott IP-cím használatával tárolószolgáltatások felderítéséhez. Csak a Service Fabric-szolgáltatás vagy a DNS-szolgáltatás felderítése használható. 

>[!WARNING]
>Azure lehetővé teszi, hogy a virtuális hálózatonként 4096 IP-címek összesen. A csomópontok száma és (amely megnyitási módot használ) tároló szolgáltatás példányainak száma nem haladhatja meg 4096 IP-címek a virtuális hálózaton belül. Nagy sűrűségű forgatókönyvek esetén ajánlott nat hálózati mód.
>

## <a name="set-up-open-networking-mode"></a>Nyissa meg a hálózati mód beállítása

1. Az Azure Resource Manager-sablon beállítása. Az a **fabricSettings** szakaszban, a DNS-szolgáltatás és az IP-szolgáltató engedélyezése: 

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

2. A hálózati profil szakasz beállítása lehetővé teszi több IP-címmel kell konfigurálni a fürt mindegyik csomópontján. Az alábbi példa beállítja az öt IP-címek / Windows/Linux Service Fabric-fürt csomópontja. Minden egyes csomóponton porton öt szolgáltatáspéldány lehet.

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
 
3. Csak Windows-fürtök esetén beállítása egy Azure hálózati biztonsági csoport (NSG) szabály, amely megnyílik az UDP/53-as port a virtuális hálózat, a következő értékekkel:

   |Beállítás |Érték | |
   | --- | --- | --- |
   |Prioritás |2000 | |
   |Name (Név) |Custom_Dns  | |
   |Forrás |VirtualNetwork | |
   |Cél | VirtualNetwork | |
   |Szolgáltatás | DNS (UDP/53) | |
   |Műveletek | Engedélyezés  | |
   | | |

4. Adja meg a hálózati módban az alkalmazásjegyzékben, az egyes szolgáltatásokhoz: `<NetworkConfig NetworkType="Open">`. **Nyissa meg** hálózati mód eredményezi, hogy a szolgáltatás egy dedikált IP-cím beolvasása. Ha egy mód nincs megadva, a szolgáltatás az alapértelmezett **nat** mód. A manifest ügynökök a `NodeContainerServicePackage1` és `NodeContainerServicePackage2` szolgáltatások minden figyelési ugyanazt a portot is (mindkét szolgáltatás által figyelt `Endpoint1`). Ha meg van határozva a hálózati megnyitási módot, `PortBinding` konfigurációk nem adható meg.

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

    Ön szabadon kombinálhatók hálózati mód szolgáltatásban egy Windows-fürt számára az alkalmazáson belül. Egyes szolgáltatások használhatnak megnyitási módot, míg mások nat-módot használja. Ha egy szolgáltatás nat-mód használatára van konfigurálva, a portot, amelyet figyeli a szolgáltatás egyedinek kell lennie.

    >[!NOTE]
    >Linux-fürtök esetén a különböző szolgáltatásokhoz hálózati módok keverése nem támogatott. 
    >

5. Amikor a **nyitott** módot választja, a **végpont** meghatározása a szolgáltatás jegyzékben kell explicit módon mutasson a végpontnak megfelelő kódcsomag akkor is, ha a szolgáltatáscsomag csak egy kódot Ez a csomag. 
   
   ```xml
   <Resources>
     <Endpoints>
       <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" CodePackageRef="Code"/>
     </Endpoints>
   </Resources>
   ```

## <a name="next-steps"></a>További lépések
* [A Service Fabric-alkalmazásmodell megismerése](service-fabric-application-model.md)
* [További tudnivalók a Service Fabric service manifest erőforrások](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources)
* [A Service Fabric Windows Server 2016 egy Windows-tároló telepítése](service-fabric-get-started-containers.md)
* [Telepítse a Service Fabric Linux egy Docker-tároló](service-fabric-get-started-containers-linux.md)
