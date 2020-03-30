---
title: Az Azure Service Fabric fordított proxybeállítása
description: Ismerje meg, hogyan állíthatja be és konfigurálhatja az Azure Service Fabric-alkalmazások fordított proxyszolgáltatását.
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: pepogors
ms.openlocfilehash: 131440036896d323cbf821d7a220328456e1db36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645446"
---
# <a name="set-up-and-configure-reverse-proxy-in-azure-service-fabric"></a>Fordított proxy beállítása és konfigurálása az Azure Service Fabricben
A fordított proxy egy opcionális Azure Service Fabric-szolgáltatás, amely segít a Service Fabric-fürtben futó mikroszolgáltatásoknak a http-végpontokkal rendelkező más szolgáltatások felderítésében és kommunikálásában. További információ: [Reverse proxy in Azure Service Fabric](service-fabric-reverseproxy.md). Ez a cikk bemutatja, hogyan állíthatja be és konfigurálhatja a fordított proxyt a fürtben. 

## <a name="enable-reverse-proxy-using-azure-portal"></a>Fordított proxy engedélyezése az Azure Portalhasználatával

Az Azure Portal egy új Service Fabric-fürt létrehozásakor lehetővé teszi a fordított proxy engedélyezését. Meglévő fürt nem frissíthető fordított proxy használatára a portálon keresztül. 

Ha [fürtöt hoz létre az Azure Portal használatával,](./service-fabric-cluster-creation-via-portal.md)konfigurálni szeretné a fordított proxyt, győződjön meg arról, hogy a következőket teszi:

1. 2. **lépés: Fürtkonfiguráció** **csoportjában**válassza a **Fordított proxy engedélyezése**lehetőséget.

   ![Fordított proxy engedélyezése a portálon](./media/service-fabric-reverseproxy-setup/enable-rp-portal.png)
2. (Nem kötelező) A biztonságos fordított proxy konfigurálásához SSL-tanúsítványt kell konfigurálnia. A **3.** **Configure cluster security settings** **Configuration type** **Custom** Ezután a **Fordított proxy SSL-tanúsítvány**csoportban jelölje **be az SSL-tanúsítvány felvétele fordított proxyhoz** jelölőnégyzetet, és adja meg a tanúsítvány adatait.

   ![Biztonságos fordított proxy konfigurálása a portálon](./media/service-fabric-reverseproxy-setup/configure-rp-certificate-portal.png)

   Ha úgy dönt, hogy a fürt létrehozásakor nem konfigurálja a fordított proxyt egy tanúsítvánnyal, ezt később megteheti a fürt erőforráscsoportjának Erőforrás-kezelő sablonján keresztül. További információ: [Fordított proxy engedélyezése az Azure Resource Manager-sablonokon keresztül](#enable-reverse-proxy-via-azure-resource-manager-templates).

## <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>Fordított proxy engedélyezése az Azure Resource Manager-sablonokon keresztül

Fürtök az Azure-ban, használhatja az Azure Resource Manager sablon, hogy a fordított proxy service fabric engedélyezheti. A fordított proxyt engedélyezheti a fürt létrehozásakor, vagy a fürt későbbi frissítésével. 

Új fürt esetén [létrehozhat egyéni Erőforrás-kezelő sablont,](service-fabric-cluster-creation-via-arm.md) vagy használhat mintasablont. 

A GitHub biztonságos fordított proxymintasablonjaiban megtalálhatja az Erőforrás-kezelő mintasablonjait, amelyek segítségével konfigurálhatja az Azure-fürt biztonságos [fordított proxymintasablonjait.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample) A [HTTPS-proxy konfigurálása biztonságos fürtben](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample/README.md#configure-https-reverse-proxy-in-a-secure-cluster) a README-fájlban című útmutatóban és a tanúsítványváltás biztonságos proxyjának konfigurálásához és a tanúsítványváltás kezeléséhez használt sablonokat című részben találja.

Meglévő fürt esetén exportálhatja a fürt erőforráscsoportjához készült Erőforrás-kezelő sablont az [Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template), a [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell)vagy az [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli)használatával.

Az Erőforrás-kezelő sablon használata után a következő lépésekkel engedélyezheti a fordított proxyt:

1. Adjon meg egy portot a fordított proxyhoz a sablon [Paraméterek szakaszában.](../azure-resource-manager/templates/template-syntax.md)

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19081,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Adja meg a Portot a [**Microsoft.ServiceFabric/clusters**](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) Resource type (Nodetype objektumok) minden egyes [csomóponttípus-objektumához](../azure-resource-manager/templates/template-syntax.md).

    A portot a reverseProxyEndpointPort paraméternév azonosítja.

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
3. Ha az SSL-tanúsítványokat a porton a fordított proxyhoz szeretné konfigurálni, adja hozzá a tanúsítványt a **Microsoft.ServiceFabric/clusters** [Resource type szakasz](../resource-group-authoring-templates.md) ***reverseProxyCertificate*** tulajdonságához.

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

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>A fürttanúsítványtól eltérő proxytanúsítvány támogatása
 Ha a fordított proxytanúsítvány eltér a fürtet biztonságossá tevő tanúsítványtól, akkor a korábban megadott tanúsítványt telepíteni kell a virtuális gépre, és hozzá kell adni a hozzáférés-vezérlési listához (ACL), hogy a Service Fabric hozzáférhessen. Ezt a [**Microsoft.Compute/virtualMachineScaleSets Resource type (Microsoft.Compute/virtualMachineScaleSets**](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachinescalesets) [Resource type ) szakaszban](../resource-group-authoring-templates.md)teheti meg. A telepítéshez adja hozzá a tanúsítványt az osProfile-hoz. A sablon bővítményszakasza frissítheti az ACL tanúsítványát.

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
> Ha a fürttanúsítványtól eltérő tanúsítványokat használ egy meglévő fürt fordított proxyjának engedélyezéséhez, telepítse a fordított proxytanúsítványt, és frissítse az ACL-t a fürtön, mielőtt engedélyezne a fordított proxyt. Töltse ki az [Azure Resource Manager-sablon](service-fabric-cluster-creation-via-arm.md) üzembe helyezését a korábban a központi telepítés megkezdése előtt említett beállítások kal, hogy engedélyezze a fordított proxyt az 1-3 lépésekben.

## <a name="enable-reverse-proxy-on-standalone-clusters"></a>Fordított proxy engedélyezése önálló fürtökön

Önálló fürtök esetén a ClusterConfig.json fájlban engedélyezheti a fordított proxyt. Engedélyezheti a fordított proxyt a fürt létrehozásakor vagy egy meglévő fürt konfigurációjának frissítésével. A ClusterConfig.json fájlokban elérhető beállításokról az [Önálló fürtbeállítások című](./service-fabric-cluster-manifest.md)témakörben olvashat bővebben.

A következő lépések bemutatják a fordított proxy engedélyezéséhez, és adott esetben a fordított proxy X.509 tanúsítvánnyal történő védelméhez használt beállításokat. 

1. A fordított proxy engedélyezéséhez állítsa be a **reverseProxyEndpointPort** értékét a csomóponttípushoz a fürtkonfiguráció **tulajdonságai** alatt. A következő JSON a fordított proxyvégpont-port 19081-re állítását mutatja a "NodeType0" típusú csomópontok esetében:

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
2. (Nem kötelező) Biztonságos fordított proxy esetén konfiguráljon egy tanúsítványt a **biztonsági** szakaszban a **tulajdonságok**alatt. 
   - Fejlesztési vagy tesztelési környezet esetén használhatja a **ReverseProxyCertificate** beállítást:

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
   - Éles környezetben a **ReverseProxyCertificateCommonNames** beállítás ajánlott:

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

   Ha többet szeretne tudni az önálló fürt tanúsítványainak konfigurálásáról és kezeléséről, valamint a fordított proxy védelmére használt tanúsítványok konfigurálásáról, olvassa el az [X509 tanúsítványalapú biztonság című témakört.](./service-fabric-windows-cluster-x509-security.md)

Miután módosította a ClusterConfig.json fájlt a fordított proxy engedélyezésére, kövesse [a Fürtkonfiguráció frissítése](service-fabric-cluster-config-upgrade-windows-server.md) a fürtkonfiguráció frissítése című útmutató utasításait a módosítások fürtre való leküldéses hez.


## <a name="expose-reverse-proxy-on-a-public-port-through-azure-load-balancer"></a>Fordított proxy kitevétése nyilvános porton az Azure Load Balancer-en keresztül

A fordított proxy egy Azure-fürtön kívülről, azure load balancer szabályok és egy Azure Health Probe a fordított proxyport. Ezek a lépések az Azure Portalon vagy az Erőforrás-kezelő sablonhasználatával bármikor elvégezhetők a fürt létrehozása után. 

> [!WARNING]
> Amikor konfigurálja a fordított proxy portját a terheléselosztóban, a fürt összes olyan mikroszolgáltatása, amely HTTP-végpontot fed le, a fürtön kívülről címezhető. Ez azt jelenti, hogy a belsőnek szánt mikroszolgáltatások felderíthetők lehetnek egy meghatározott rosszindulatú felhasználó által. Ez potenciálisan kihasználható súlyos sebezhetőségeket jelent; például:
>
> * A rosszindulatú felhasználók szolgáltatásmegtagadási támadást indíthatnak, ha ismételten olyan belső szolgáltatást hívnak meg, amely nem rendelkezik megfelelően megerősített támadási felülettel.
> * A rosszindulatú felhasználók hibásan formázott csomagokat juttathatnak el egy belső szolgáltatásba, ami nem kívánt viselkedést eredményez.
> * A belső nek szánt szolgáltatások olyan személyes vagy bizalmas adatokat adhatnak vissza, amelyek nem a fürtön kívüli szolgáltatásoknak vannak kitéve, így ezeket a bizalmas információkat rosszindulatú felhasználók számára teszik ki. 
>
> Mielőtt a fordított proxyportot nyilvánossá tenné, győződjön meg arról, hogy teljes mértékben tisztában van a fürt és a rajta futó alkalmazások lehetséges biztonsági következményeivel. 
>

Ha egy önálló fürt höz nyilvánosan szeretné elérhetővé tenni a fordított proxyt, az ennek módja a fürtöt üzemeltető rendszertől függ, és a jelen cikk hatókörén kívül esik. Az előző figyelmeztetés a fordított proxy nyilvános felfedéséről azonban továbbra is érvényes.

### <a name="expose-the-reverse-proxy-using-azure-portal"></a>A fordított proxy felfedése az Azure Portal használatával 

1. Az Azure Portalon kattintson a fürt erőforráscsoportjára, majd kattintson a fürt terheléselosztójára.
2. Ha a fordított proxyporthoz szeretne állapotmintát hozzáadni, a terheléselosztó ablak bal oldali ablaktáblájában a **BEÁLLÍTÁSOK csoportban**kattintson az **Állapotminta parancsra.** Ezután kattintson a **Hozzáadás** gombra az Állapotminta ablak tetején, és adja meg a fordított proxyport adatait, majd kattintson az **OK**gombra. Alapértelmezés szerint a fordított proxyport 19081, kivéve, ha a fürt létrehozásakor módosította.

   ![Fordított proxy állapotminta konfigurálása](./media/service-fabric-reverseproxy-setup/lb-rp-probe.png)
3. Ha terheléselosztó szabályt szeretne hozzáadni a fordított proxyport felfedéséhez, a terheléselosztó ablakbal oldali ablaktáblájában a **BEÁLLÍTÁSOK csoportban**kattintson a **Terheléselosztási szabályok elemre.** Ezután kattintson a **Hozzáadás** gombra a Terheléselosztási szabályok ablak tetején, és adja meg a fordított proxyport részleteit. Győződjön meg arról, hogy a **port** értékét arra a portra állította be, amelyen a fordított proxyt elérhetővé szeretné tenni, a **háttér-port** értékét a fordított proxy engedélyezésekor beállított porthoz, és az Előző lépésben konfigurált állapotminta **állapotmintavételértékét.** Szükség szerint állítson be más mezőket, és kattintson **az OK**gombra.

   ![Terheléselosztó szabály konfigurálása fordított proxyhoz](./media/service-fabric-reverseproxy-setup/lb-rp-rule.png)

### <a name="expose-the-reverse-proxy-via-resource-manager-templates"></a>A fordított proxy felfedése az Erőforrás-kezelő sablonjain keresztül

A következő JSON ugyanarra a sablonra hivatkozik, amelyet az [Azure Resource Manager-sablonokon keresztül fordított proxy engedélyezése](#enable-reverse-proxy-via-azure-resource-manager-templates)használ. A dokumentum nak a dokumentum nak az adott szakaszában tájékozódhat arról, hogyan hozhat létre Erőforrás-kezelő sablont, illetve hogyan exportálhat sablont egy meglévő fürthöz.  A módosítások a [**Microsoft.Network/loadBalancers**](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers) [Erőforrás típus szakaszában történnek.](../resource-group-authoring-templates.md)

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


## <a name="customize-reverse-proxy-behavior-using-fabric-settings"></a>A fordított proxy viselkedésének testreszabása a háló beállításaival

Testre szabhatja a fordított proxy viselkedését a háló beállításokat az Erőforrás-kezelő sablonban az Azure-ban üzemeltetett fürtök vagy a ClusterConfig.json fájl önálló fürtök. A fordított proxy viselkedését szabályozó beállítások a **fabricSettings** szakasz [**FabricSettings szakaszának ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) szakaszában találhatók a fürt **tulajdonságai** szakaszban. 

Beállíthatja például a **DefaultHttpRequestTimeout** értékét, hogy a kérelmek időtúlszáma a fordított proxyra 180 másodpercre állítsa a következő JSON-értéket:

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

Az Azure-fürtök hálóbeállításainak frissítéséről a [Fürtbeállítások testreszabása erőforrás-kezelősablonok használatával](service-fabric-cluster-config-upgrade-azure.md)című témakörben olvashat bővebben. Önálló fürtök esetén olvassa el [a Fürtbeállítások testreszabása önálló fürtökhöz című témakört.](service-fabric-cluster-config-upgrade-windows-server.md) 

Számos hálóbeállítások segítségével biztonságos kommunikáció takar a fordított proxy és a szolgáltatások között. Ezekről a beállításokról a [Csatlakozás biztonságos szolgáltatáshoz fordított proxyval](service-fabric-reverseproxy-configure-secure-communication.md)című témakörben olvashat részletesen.

## <a name="next-steps"></a>További lépések
* [Továbbítás beállítása a HTTP-szolgáltatás védelmére a fordított proxyval](service-fabric-reverseproxy-configure-secure-communication.md)
* A fordított proxy konfigurációs beállításait az [ApplicationGateway/Http című témaköra A Szolgáltatásháló fürtbeállításainak testreszabása című szakaszban találja.](service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)
