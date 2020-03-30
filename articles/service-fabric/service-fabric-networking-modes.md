---
title: A tárolószolgáltatások hálózati üzemmódjainak konfigurálása
description: Ismerje meg, hogyan állíthatja be az Azure Service Fabric által támogatott különböző hálózati módokat.
author: athinanthny
ms.topic: conceptual
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: ba1fa92559d39a481008d1dd18036e4232be1bfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639802"
---
# <a name="service-fabric-container-networking-modes"></a>Service Fabric tárolóhálózati módjai

Egy Azure Service Fabric-fürt a tárolószolgáltatások **nat** hálózati módot használ alapértelmezés szerint. Ha egynél több tárolószolgáltatás figyel ugyanazon a porton, és nat módban van használatban, telepítési hibák léphetnek fel. Több tárolószolgáltatás ugyanazon a porton történő figyeléséhez a Service Fabric **open** hálózati módot kínál (5.7-es és újabb verziók). Nyílt módban minden tárolószolgáltatás rendelkezik egy belső, dinamikusan hozzárendelt IP-címmel, amely támogatja az ugyanazon a porton több szolgáltatást.  

Ha egy tárolószolgáltatás egy statikus végpont a szolgáltatásjegyzékben, hozhat létre és törölhet új szolgáltatásokat a Megnyitás mód használatával üzembe helyezési hibák nélkül. Ugyanaz a docker-compose.yml fájl statikus portleképezésekkel is használható több szolgáltatás létrehozásához.

Amikor egy tárolószolgáltatás újraindul vagy a fürt egy másik csomópontjára költözik, az IP-cím megváltozik. Emiatt nem javasoljuk a dinamikusan hozzárendelt IP-cím használatát a tárolószolgáltatások felderítéséhez. Csak a Service Fabric naming service vagy a DNS-szolgáltatás kell használni a szolgáltatás felderítéséhez. 

>[!WARNING]
>Az Azure virtuális hálózatonként összesen 65 356 IP-t engedélyez. A csomópontok száma és a tárolószolgáltatás-példányok száma (amelyek open módot használnak) nem haladhatja meg a 65 356 IP-t egy virtuális hálózaton belül. Nagy sűrűségű esetekben a nat hálózati módot javasoljuk. Emellett más függőségek, például a terheléselosztó lesz más [korlátozásokat](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) kell figyelembe venni. Csomópontonként legfeljebb 50 IP-t teszteltek és bizonyítottan stabilak. 
>

## <a name="set-up-open-networking-mode"></a>Hálózati mód megnyitása

1. Állítsa be az Azure Resource Manager sablont. A fürterőforrás **fabricSettings** szakaszában engedélyezze a DNS-szolgáltatást és az IP-szolgáltatót: 

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
    
2. Állítsa be a virtuálisgép-méretezési készlet erőforrás hálózati profilszakaszát. Ez lehetővé teszi, hogy a fürt minden csomópontján több IP-cím legyen konfigurálva. A következő példa egy Windows/Linux Service Fabric-fürt csomópontonként öt IP-címet állít be. Minden csomópontportján öt szolgáltatáspéldány figyelhet. Ha azt szeretné, hogy az öt IP-cím elérhető legyen az Azure Load Balancer-ből, az öt IP-szolgáltatót az Azure Load Balancer backend address pool-ba kell beszerveznie az alábbiak szerint.  A változókat is hozzá kell adnia a sablon tetejéhez a változók szakaszban.

    Adja hozzá ezt a szakaszt a változókhoz:

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
    
    Adja hozzá ezt a szakaszt a Virtuálisgép-méretezési csoport erőforráshoz:

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
 
3. Csak Windows-fürtök esetén állítson be egy NSG-szabályt, amely megnyitja az UDP/53-as portot a virtuális hálózat számára a következő értékekkel:

   |Beállítás |Érték | |
   | --- | --- | --- |
   |Prioritás |2000 | |
   |Név |Custom_Dns  | |
   |Forrás |VirtualNetwork | |
   |Cél | VirtualNetwork | |
   |Szolgáltatás | DNS (UDP/53) | |
   |Műveletek | Engedélyezés  | |
   | | |

4. Adja meg a hálózati módot az alkalmazásjegyzékben az egyes szolgáltatásokhoz: `<NetworkConfig NetworkType="Open">`. **A nyílt** hálózati mód azt eredményezi, hogy a szolgáltatás dedikált IP-címet kap. Ha nincs megadva mód, a szolgáltatás alapértelmezés szerint **nat** módban van. A következő jegyzékfájlban `NodeContainerServicePackage1` `NodeContainerServicePackage2` a és a szolgáltatások figyelhetik ugyanazt `Endpoint1`a portot (mindkét szolgáltatás figyel). Ha meg van adva a `PortBinding` Nyílt hálózati mód, a konfigurációk nem adhatók meg.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ApplicationManifest ApplicationTypeName="NodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
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

    A Windows-fürthöz tartozó alkalmazásokon belül különböző hálózati módokat keverhet és párosíthat a szolgáltatások között. Egyes szolgáltatások az Open módot, míg mások nat módot használnak. Ha egy szolgáltatás nat mód használatára van konfigurálva, a portnak, amelyet a szolgáltatás figyel, egyedinek kell lennie.

    >[!NOTE]
    >Linux-fürtökesetén a különböző szolgáltatások hálózati módjainak keverése nem támogatott. 
    >

5. Ha az **Open** mód van kiválasztva, **a** végpont definíciója a szolgáltatás jegyzékfájljában explicit módon kell mutatnia a végpontnak megfelelő kódcsomagra, még akkor is, ha a szolgáltatáscsomagban csak egy kódcsomag van. 
   
   ```xml
   <Resources>
     <Endpoints>
       <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" CodePackageRef="Code"/>
     </Endpoints>
   </Resources>
   ```
   
6. A Windows esetében a virtuális gép újraindítása a nyílt hálózat újbóli létrehozását eredményezi. Ez a hálózati verem egyik mögöttes problémája. Az alapértelmezett viselkedés a hálózat újbóli létrehozása. Ha ezt a viselkedést ki kell kapcsolni, a következő konfigurációt használhatja, amelyet egy konfigurációs frissítés követ.

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
* [További információ a Service Fabric szolgáltatásjegyzék-erőforrásokról](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources)
* [Windows-tároló központi telepítése a Service Fabric szolgáltatásba Windows Server 2016 rendszeren](service-fabric-get-started-containers.md)
* [Docker-tároló üzembe helyezése linuxos Service Fabric-re](service-fabric-get-started-containers-linux.md)
