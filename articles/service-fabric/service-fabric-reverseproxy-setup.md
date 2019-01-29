---
title: Az Azure Service Fabric fordított proxy beállítása |} A Microsoft Docs
description: Megtudhatja, hogyan állíthatja be, és a Service Fabric fordított proxy konfigurálása.
services: service-fabric
documentationcenter: na
author: jimacoMS2
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 07/27/201
ms.author: v-jamebr
ms.openlocfilehash: 522e9209da5d2df796700dea764270382b1170f5
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55102765"
---
# <a name="set-up-and-configure-reverse-proxy-in-azure-service-fabric"></a>Állítsa be, és az Azure Service Fabric fordított proxy konfigurálása
Fordított proxy egy nem kötelező az Azure Service Fabric-szolgáltatás, amely segít a Service Fabric-fürtön futó mikroszolgáltatásokat felderítése és kommunikálni más szolgáltatásokkal, amelyek http-végpontokat. További tudnivalókért lásd: [fordított proxy az Azure Service Fabric](service-fabric-reverseproxy.md). Ez a cikk bemutatja, hogyan állíthatja be, és a fürtben a fordított proxy konfigurálása. 

## <a name="enable-reverse-proxy-using-azure-portal"></a>Az Azure portal használatával fordított proxy engedélyezése

Az Azure portal az új Service Fabric-fürt létrehozásakor a fordított proxy engedélyezése lehetőséget biztosít. Meglévő fürt a portálon keresztül fordított proxy használatára nem frissíthető. 

Fordított proxy konfigurálása során meg [hozzon létre egy fürtöt az Azure portal használatával](./service-fabric-cluster-creation-via-portal.md), győződjön meg arról, tegye a következőket:

1. A **2. lépés: A fürt konfiguráció**alatt **csomóponttípus konfigurációja**válassza **fordított proxy engedélyezése**.

   ![A portálon a fordított proxy engedélyezése](./media/service-fabric-reverseproxy-setup/enable-rp-portal.png)
2. (Nem kötelező) Biztonságos fordított proxy konfigurálása, SSL-tanúsítvány konfigurálása kell. A **3. lépés: Biztonsági**, a **fürtbiztonsági beállítások konfigurálása**alatt **konfigurációtípus**válassza **egyéni**. Ezután a **fordított Proxy SSL-tanúsítvány**válassza **fordított proxyhoz tartozó SSL-tanúsítvány** , és adja meg a tanúsítvány adatait.

   ![Biztonságos fordított proxy konfigurálása a portálon](./media/service-fabric-reverseproxy-setup/configure-rp-certificate-portal.png)

   Ha nem kíván a fürt létrehozásakor a fordított proxy konfigurálása egy tanúsítvánnyal, érdemes tehát később a fürt erőforráscsoport a Resource Manager-sablon használatával. További tudnivalókért lásd: [fordított proxy engedélyezése az Azure Resource Manager-sablonok segítségével](#enable-reverse-proxy-via-azure-resource-manager-templates).

## <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonok segítségével fordított proxy engedélyezése

A fürtök az Azure-ban az Azure Resource Manager-sablon használatával a Service Fabric fordított proxy engedélyezése. Fordított proxy engedélyezheti a fürt létrehozásakor vagy a fürt frissítésével később is engedélyezheti. 

Új fürt esetében is [hozzon létre egy egyéni Resource Manager-sablon](service-fabric-cluster-creation-via-arm.md) vagy egy mintául szolgáló sablont is használhat. 

Mintául szolgáló Resource Manager-sablonok, amelyek segítségével egy Azure-fürtön a biztonságos fordított proxy konfigurálása annak a [biztonságos fordított Proxy Mintasablonokat](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample) a Githubon. Tekintse meg [konfigurálása HTTPS fordított Proxy egy biztonságos fürt](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample/README.md#configure-https-reverse-proxy-in-a-secure-cluster) útmutatásért és a sablonok használatával biztonságos fordított proxy konfigurálása egy tanúsítvánnyal, illetve tanúsítványváltás kezelésére, az információs fájlban.

Egy meglévő fürthöz, exportálhatja a Resource Manager-sablon a fürterőforrás használó csoport a [az Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template#export-the-template-from-resource-group), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell#export-resource-group-as-template), vagy a [Azure CLI-vel](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli#export-resource-group-as-template).

Miután egy Resource Manager-sablon, engedélyezheti a fordított proxy, az alábbi lépéseket követve:

1. A fordított proxy port definiálása a [paraméterek szakaszban](../azure-resource-manager/resource-group-authoring-templates.md) a sablon.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19081,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Adja meg a portot a nodetype objektumok mindegyike a [ **Microsoft.ServiceFabric/clusters** ](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) [erőforrás típushoz című](../azure-resource-manager/resource-group-authoring-templates.md).

    A port a paraméternév megadásához, reverseProxyEndpointPort azonosítja.

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```
3. A port a fordított proxyhoz tartozó SSL-tanúsítványok konfigurálásához adja hozzá a tanúsítványt a ***reverseProxyCertificate*** tulajdonságot a **Microsoft.ServiceFabric/clusters** [erőforrás Írja be a szakasz](../resource-group-authoring-templates.md).

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>A fordított proxy tanúsítvány, amely eltér a fürttanúsítvány támogatása
 Ha a fordított proxy tanúsítvány eltér a tanúsítványt, amely biztonságossá teszi a fürt, majd a korábban megadott tanúsítvány telepíteni a virtuális gépen és kell hozzáadni a hozzáférés-vezérlési lista (ACL), hogy a Service Fabric hozzá tud férni. Ehhez a [ **Microsoft.Compute/virtualMachineScaleSets** ](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachinescalesets) [erőforrás típushoz című](../resource-group-authoring-templates.md). A telepítéshez adja hozzá a tanúsítványt a osProfile. A sablon a bővítmény szakasz frissítheti a hozzáférés-vezérlési tanúsítványt.

  ```json
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    ....
      "osProfile": {
          "adminPassword": "[parameters('adminPassword')]",
          "adminUsername": "[parameters('adminUsername')]",
          "computernamePrefix": "[parameters('vmNodeType0Name')]",
          "secrets": [
            {
              "sourceVault": {
                "id": "[parameters('sfReverseProxySourceVaultValue')]"
              },
              "vaultCertificates": [
                {
                  "certificateStore": "[parameters('sfReverseProxyCertificateStoreValue')]",
                  "certificateUrl": "[parameters('sfReverseProxyCertificateUrlValue')]"
                }
              ]
            }
          ]
        }
   ....
   "extensions": [
          {
              "name": "[concat(parameters('vmNodeType0Name'),'_ServiceFabricNode')]",
              "properties": {
                      "type": "ServiceFabricNode",
                      "autoUpgradeMinorVersion": false,
                      ...
                      "publisher": "Microsoft.Azure.ServiceFabric",
                      "settings": {
                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                        "dataPath": "D:\\\\SvcFab",
                        "durabilityLevel": "Bronze",
                        "testExtension": true,
                        "reverseProxyCertificate": {
                          "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                          "x509StoreName": "[parameters('sfReverseProxyCertificateStoreValue')]"
                        },
                  },
                  "typeHandlerVersion": "1.0"
              }
          },
      ]
    }
  ```
> [!NOTE]
> Tanúsítványokat, amelyek eltérnek a fürttanúsítvány egy meglévő fürtben a fordított proxy engedélyezése a használatakor a fordított proxy tanúsítvány telepítéséhez, és frissítse az ACL-t a fürtön, a fordított proxy engedélyezése előtt. Végezze el a [Azure Resource Manager-sablon](service-fabric-cluster-creation-via-arm.md) az említett beállításokat használó központi telepítési korábban a fordított proxy engedélyezése a telepítés megkezdése előtt a lépések 1 – 3.

## <a name="enable-reverse-proxy-on-standalone-clusters"></a>Az önálló fürtök fordított proxy engedélyezése

Az önálló fürtök esetén a ClusterConfig.json fájlban fordított proxy engedélyezése. Engedélyezheti a fordított proxy a fürt létrehozása vagy frissítése egy meglévő fürt konfigurációját. ClusterConfig.json fájlok rendelkezésre álló beállításokkal kapcsolatos további tudnivalókért lásd: [önálló fürtbeállítások](./service-fabric-cluster-manifest.md).

A következő lépések bemutatják, hogy fordított proxy használatával a beállítások és szükség esetén a fordított proxy X.509 tanúsítvánnyal biztonságossá tételéhez. 

1. Fordított proxy engedélyezéséhez állítsa be a **reverseProxyEndpointPort** az érintett csomópont alatt **tulajdonságok** a fürt Config. A következő JSON látható "NodeType0" típusú csomópontok 19081 a fordított proxy végponti port beállítást:

   ```json
       "properties": {
          ... 
           "nodeTypes": [
               {
                   "name": "NodeType0",
                   ...
                   "reverseProxyEndpointPort": "19081",
                   ...
               }
           ],
          ...
       }
   ```
2. (Nem kötelező) A tanúsítvány konfigurálása egy biztonságos fordított proxy a **biztonsági** szakaszba **tulajdonságok**. 
   - Fejlesztési-tesztelési környezet is használhatja a **ReverseProxyCertificate** beállítást:

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificate": {
                          "Thumbprint": "[Thumbprint]",
                          "ThumbprintSecondary": "[Thumbprint]",
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```
   - Az éles környezetben a **ReverseProxyCertificateCommonNames** beállítás ajánlott:

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificateCommonNames": {
                        "CommonNames": [
                            {
                              "CertificateCommonName": "[CertificateCommonName]"
                            }
                          ],
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```

   További információk konfigurálása és kezelése egy önálló fürtöt, valamint a fordított proxy védelmére szolgáló tanúsítványok konfigurálásával kapcsolatos további részleteket a tanúsítványok kapcsolatban lásd: [X509 ügyféltanúsítvány-alapú biztonsági](./service-fabric-windows-cluster-x509-security.md).

Miután módosította a fordított proxy engedélyezése ClusterConfig.json fájl, kövesse a [a fürt konfigurációjának frissítése](service-fabric-cluster-config-upgrade-windows-server.md) paranccsal küldje le a módosításokat a fürthöz.


## <a name="expose-reverse-proxy-on-a-public-port-through-azure-load-balancer"></a>Tegye elérhetővé az Azure Load Balanceren keresztül nyilvános port fordított proxy

Oldja meg a fordított proxy egy Azure-fürtön kívül, állítsa be az Azure Load Balancer-szabályok és a egy Azure-Állapotminta esetében a fordított proxy portjával. Ezeket a lépéseket az Azure portal vagy a Resource Manager-sablon használatával a fürt létrehozása után bármikor elvégezhető. 

> [!WARNING]
> A fordított proxy portjával Load balancerben konfigurálásakor, amely közzétenni egy HTTP-végpontot a fürt összes mikroszolgáltatások a fürtön kívülről címezhető. Ez azt jelenti, hogy rosszindulatú felhasználók által felderíthető lehet-e a mikroszolgáltatás-alapú szinkronban kell lennie a belső. Ez potenciálisan megadja súlyos biztonsági réseket, hogy azokat kihasználnák; Példa:
>
> * Egy rosszindulatú felhasználó egy szolgáltatásmegtagadási támadást indíthatnak ismételten meghívásával egy belső szolgáltatás, amely nem rendelkezik egy eléggé támadási felületét.
> * Egy rosszindulatú felhasználó előfordulhat, hogy helytelenül formázott csomagok továbbítására az egy belső szolgáltatás nem kívánt viselkedést eredményez.
> * Szinkronban kell lennie a belső szolgáltatás nem célja, hogy ki vannak téve a szolgáltatások a fürtön, így is közzéteheti a bizalmas adatokat egy rosszindulatú felhasználó kívül titkos vagy bizalmas információkat adhatnak vissza. 
>
> Ellenőrizze, hogy teljes mértékben tudomásul veszi és csökkentheti a potenciális biztonsági vonatkozásai annak a fürthöz, és azt, mielőtt a fordított proxy portjával nyilvános futó alkalmazások. 
>

Ha azt szeretné, hogy fordított proxy nyilvánosan önálló fürt, amelyben ekkor módon függ, a rendszer a fürtöt, és ez a cikk nem terjed. A fenti figyelmeztetést is közzéteheti a fordított proxy nyilvánosan, azonban továbbra is érvényben marad.

### <a name="expose-the-reverse-proxy-using-azure-portal"></a>Tegye elérhetővé a fordított proxy, az Azure portal használatával 

1. Az Azure Portalon kattintson az erőforráscsoport, a fürt számára, majd a terheléselosztó a fürt számára.
2. Egy állapot-mintavételi modul a fordított proxy portjával, a hozzáadása a bal oldali panelen, a load balancer ablak alatt **beállítások**, kattintson a **állapotadat-mintavételek**. Kattintson a **Hozzáadás** tetején található a Health-mintavételek ablakban és adja meg a fordított proxy portjával részleteit, majd kattintson **OK**. Alapértelmezés szerint a fordított proxy portjával 19081.,, kivéve, ha a módosítás a fürt létrehozásakor.

   ![Fordított proxy állapotadat-mintavétel konfigurálása](./media/service-fabric-reverseproxy-setup/lb-rp-probe.png)
3. A fordított proxy portjával, a bal oldali panelen, a load balancer ablak elérhetővé a terheléselosztó szabály hozzáadása **beállítások**, kattintson a **terheléselosztási szabályok**. Kattintson a **Hozzáadás** felső részén a terhelés terheléselosztási szabályok ablakban és adja meg annak részleteit a fordított proxy portjával. Mindenképpen állítsa be a **Port** érték, a portot, amelyet a fordított proxy közzétéve, az **háttérport** értéket a fordított proxy engedélyezésekor beállított port és a **állapotadat-mintavétel** az állapotfigyelő mintavételező az előző lépésben beállított értéket. Állítsa be a megfelelő, és kattintson a többi mező **OK**.

   ![Load balancer-szabályt a fordított proxy konfigurálása](./media/service-fabric-reverseproxy-setup/lb-rp-rule.png)

### <a name="expose-the-reverse-proxy-via-resource-manager-templates"></a>Tegye elérhetővé a fordított proxy használatával a Resource Manager-sablonok

A következő JSON-ban használt ugyanazt a sablont hivatkozik [fordított proxy engedélyezése az Azure Resource Manager-sablonok segítségével](#enable-reverse-proxy-via-azure-resource-manager-templates). Tekintse meg a szakasz a dokumentum létrehozása a Resource Manager-sablonnal, vagy egy meglévő fürt sablon exportálása kapcsolatos információkat.  A módosítások a [ **Microsoft.Network/loadBalancers** ](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers) [erőforrás típushoz című](../resource-group-authoring-templates.md).

    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```


## <a name="customize-reverse-proxy-behavior-using-fabric-settings"></a>Fordított proxy viselkedések hálóbeállítások testreszabása

Testre szabhatja az önálló fürtök ClusterConfig.json fájljában vagy az Azure-ban futó fürtök esetén a Resource Manager-sablon beállításaival fabric fordított proxyja viselkedését. Fordított proxy viselkedését szabályozó beállítások találhatók a [ **ApplicationGateway/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) című rész a **fabricSettings** szakasz alatt a fürt **tulajdonságok** szakaszban. 

Az értékét állítsa például **DefaultHttpRequestTimeout** , a kérelem időkorlátja a 180 másodperc, ahogy az alábbi JSON-ra mutató fordított proxyként szeretné:

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "DefaultHttpRequestTimeout",
                   "value": "180"
                 }
               ]
             }
           ],
           ...
   }
   ``` 

Azure fürtök hálóbeállítások frissítésével kapcsolatos további információkért lásd: [testre szabhatja a Resource Manager-sablonok használatával fürtök beállításait](service-fabric-cluster-config-upgrade-azure.md). Az önálló fürtök esetén lásd: [fürtbeállításokhoz az önálló fürtök testreszabása](service-fabric-cluster-config-upgrade-windows-server.md). 

Több hálóbeállítások szolgálnak a fordított proxy és a szolgáltatások közötti biztonságos kommunikáció érdekében. Ezek a beállítások kapcsolatos részletes információkért lásd: [Csatlakozás biztonságos szolgáltatás, amely a fordított proxy](service-fabric-reverseproxy-configure-secure-communication.md).

## <a name="next-steps"></a>További lépések
* [A fordított proxy-továbbítást a biztonságos HTTP-szolgáltatás beállítása](service-fabric-reverseproxy-configure-secure-communication.md)
* A fordított proxy konfigurációs lehetőségekről [testreszabása a Service Fabric-fürt beállítások szakaszában ApplicationGateway/Http](service-fabric-cluster-fabric-settings.md#applicationgatewayhttp).
