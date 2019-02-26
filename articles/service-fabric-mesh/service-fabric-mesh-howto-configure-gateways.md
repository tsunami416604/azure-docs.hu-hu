---
title: Kérelmek-átjáró konfigurálása |} A Microsoft Docs
description: Megtudhatja, hogyan kezeli a bejövő forgalom a Service Fabric-háló futó alkalmazás az átjáró konfigurálásához.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/28/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 47709b84fe67c55c84bea59123d5a46a6e3940fb
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806602"
---
# <a name="configure-a-gateway-resource-to-route-requests"></a>A kérelmeket a Gateway-erőforrás beállítása

Egy átjáró-erőforrást a bejövő forgalom irányítása a hálózathoz, az alkalmazás-környezetet tároló szolgál. A beállítást adhat meg szabályokat, amelyek kérelmek adott szolgáltatások vagy a végpontok a kérelem szerkezete alapján irányítja. Lásd: [Bevezetés a Service Fabric-háló hálózatkezelésének](service-fabric-mesh-networks-and-gateways.md) hálózatokat és átjárókat a háló további tájékoztatást. 

Átjáró-erőforrásokat kell deklarálni, a központi telepítési sablont (JSON- vagy yam) részeként, és hálózati erőforrástól függ-e. Ez a dokumentum ismerteti a különböző tulajdonságait, amely az átjáró is beállítható, és a egy minta-átjáró konfigurációs ismerteti.

## <a name="options-for-configuring-your-gateway-resource"></a>Beállítások konfigurálása az átjáró-erőforrás

Mivel az alkalmazás hálózat és az alapul szolgáló infrastruktúra közötti hídként szolgál az átjáró-erőforrást (a `open` hálózati). Csak akkor kell egyet konfigurálásához (a háló előzetes verzióban van alkalmazásonként egy átjárót a korlát). A nyilatkozat az erőforrás két fő részből áll: az erőforrás metaadatának és tulajdonságait. 

### <a name="gateway-resource-metadata"></a>Átjáró erőforrás-metaadatok

Az átjáró a következő metaadatokkal deklarálva van:
* `apiVersion` -be kell állítani az "2018-09-01-preview" (vagy újabb, a jövőben)
* `name` – Ez az átjáró karakterlánc nevét
* `type` – "Microsoft.ServiceFabricMesh/gateways"
* `location` – Állítsa be a helyre, az alkalmazás / hálózati kell lennie; általában lesz egy hivatkozást a hely paraméter a központi telepítésben
* `dependsOn` – a hálózatra, amelyhez ezt az átjárót erre a célra egy belépési pont

Itt látható, hogy néz ki egy Azure Resource Manager-(JSON-) központi telepítési sablon: 

```json
{
  "apiVersion": "2018-09-01-preview",
  "name": "myGateway",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [  
    "Microsoft.ServiceFabricMesh/networks/myNetwork"
  ],
  "properties": {
    [...]
  }
}
```

### <a name="gateway-properties"></a>Átjáró tulajdonságai

A Tulajdonságok szakaszának segítségével definiálása a hálózatokat, amelyekhez az átjáró működik, és az útválasztási kérelmek szabályait. 

#### <a name="source-and-destination-network"></a>Forrás és cél hálózati 

Minden átjáró által igényelt egy `sourceNetwork` és `destinationNetwork`. A forrás hálózati számít, ha a hálózaton, amelyről az alkalmazás megkapja a bejövő kéréseket. A name tulajdonság mindig "Nyitott" értékre kell állítani. A célhálózat a hálózathoz, amely a kérések céloz meg. A név-érték ehhez meg kell az erőforrás nevét, az alkalmazás helyi hálózat (tartalmaznia kell az erőforrás teljes referencia). Egy mintául szolgáló konfigurációs megjelenése "myNetwork" nevű hálózat-KözpontiTelepítés-lentebb.

```json 
"properties": {
  "description": "Service Fabric Mesh Gateway",
  "sourceNetwork": {
    "name": "Open"
  },
  "destinationNetwork": {
    "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'myNetwork')]"
  },
  [...]
}
```

#### <a name="rules"></a>Szabályok 

Az átjáró rendelkezhet több útválasztási szabályokat, amelyek a bejövő forgalom miképpen adja meg a rendszer úgy kezeli. Útválasztási szabály határozza meg, hogy a figyelő portját és a cél-végpontot egy adott alkalmazás közötti kapcsolat. A TCP-útválasztási szabályok esetében nincs egy 1:1 megfeleltetését, Port: végpontja között. HTTP-útválasztási szabályok vizsgálja meg az elérési útját a kérelmet, és szükség esetén a fejlécek, döntse el, hogy a kérelmet a rendszer átirányítja az összetettebb útválasztási szabályokat állíthatók be. 

Útválasztási szabályok vannak megadva a port száma alapján. Minden bejövő port a saját szabályok tömbje rendelkezik az átjáró konfigurációs tulajdonságok szakaszában található. 

#### <a name="tcp-routing-rules"></a>TCP-útválasztási szabályok 

A TCP-útválasztási szabály áll a következő tulajdonságokkal: 
* `name` -a szabály, amely a választott karakterlánc lehet mutató hivatkozás 
* `port` -portot a bejövő kérelmeket 
* `destination` -Végpont meghatározása, amely tartalmazza az `applicationName`, `serviceName`, és `endpointName`, ahol a kérelmek átirányíthatók kell a

Íme egy példa a TCP-útválasztási szabály:

```json
"properties": {
  [...]
  "tcp": [
    {
      "name": "web",
      "port": 80,
      "destination": {
        "applicationName": "myApp",
        "serviceName": "myService",
        "endpointName": "myListener"
      }
    }
  ]
}
```


#### <a name="http-routing-rules"></a>HTTP-útválasztási szabályok 

Egy HTTP-útválasztási szabályt tartalmaz a következő tulajdonságokkal: 
* `name` -a szabály, amely a választott karakterlánc lehet mutató hivatkozás 
* `port` -portot a bejövő kérelmeket 
* `hosts` -házirendeket, amelyek a alkalmazni a különböző "hosts", a fent megadott porton érkező kéréseket tömbjét. Gazdagépei olyan alkalmazások és szolgáltatások, előfordulhat, hogy a hálózaton kell futniuk, és is képes kiszolgálni a bejövő kérelmeket, azaz a web app készletét. Gazdagép házirendek értelmez ahhoz, úgy kell hozza létre a következő csökkenő sajátlagossága figyelembe szintjei
    * `name` – a gazdagép, amelyhez az alábbi útválasztási szabályokat megadott DNS-nevét. Használatával "*" Itt hozna létre útválasztási szabályokat minden gazdagép esetén.
    * `routes` -házirendeket az adott állomás tömbje
        * `match` -a alkalmazni, ez a szabály a bejövő kérelem struktúra specifikáció alapján egy `path`
            * `path` -tartalmaz egy `value` (bejövő URI), `rewrite` (módját a kérelem továbbítását), és a egy `type` (is jelenleg csak a "Előtag" lehet)
            * `header` – nem kötelező tömb fejlécek értékek, ha a kérelem fejlécében egyeznie a kérelem megfelel (feljebb) elérési útja.
              * mindegyik bejegyzés tartalmazza a `name` (a fejléc megfelelő karakterlánc neve), `value` (string a kérelemben szereplő tartományfejléc értéke), és a egy `type` (is jelenleg csak a "Exact" lehet)
        * `destination` -Ha a kérelem megfelel, lesz átirányítva, erre a célra a használatával van megadva egy `applicationName`, `serviceName`, és `endpointName`

Íme egy példa HTTP útválasztási szabályt, amely ezt a hálózatot az alkalmazások által kiszolgált gazdagépeket 80,-as porton érkező kéréseket a alkalmazni. Ha a kérelem URL-címnek elérési útja, azaz a megfelelő szerkezettel `<IPAddress>:80/pickme/<requestContent>`, akkor azt a rendszer átirányítja a `myListener` végpont.  

```json
"properties": {
  [...]
  "http": [
    {
      "name": "web",
      "port": 80,
      "hosts": [
        {
          "name": "*",
          "routes": [
            {
              "match": {
                "path": {
                  "value": "/pickme",
                  "rewrite": "/",
                  "type": "Prefix"
                }
              },
              "destination": {
                "applicationName": "meshApp",
                "serviceName": "myService",
                "endpointName": "myListener"
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="sample-config-for-a-gateway-resource"></a>Minta konfigurációs egy átjáró-erőforrás 

Itt látható a teljes átjáró erőforrás-konfiguráció néz ki (Ez az a bejövő forgalom elérhető mintából igazítani a [háló minták tárház](https://github.com/Azure-Samples/service-fabric-mesh/blob/2018-09-01-preview/templates/ingress/meshingress.linux.json)):

```json
{  
  "apiVersion": "2018-09-01-preview",
  "name": "ingressGatewayLinux",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [  
    "Microsoft.ServiceFabricMesh/networks/meshNetworkLinux"
  ],
  "properties": {  
    "description": "Service Fabric Mesh Gateway for Linux mesh samples.",
    "sourceNetwork": {  
      "name": "Open"
    },
    "destinationNetwork": {  
      "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'meshNetworkLinux')]"
    },
    "http": [  
      {  
        "name": "web",
        "port": 80,
        "hosts": [  
          {  
            "name": "*",
            "routes": [  
              {  
                "match": {  
                  "path": {  
                    "value": "/hello",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {  
                  "applicationName": "meshAppLinux",
                  "serviceName": "helloWorldService",
                  "endpointName": "helloWorldListener"
                }
              },
              {  
                "match": {  
                  "path": {  
                    "value": "/counter",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {  
                  "applicationName": "meshAppLinux",
                  "serviceName": "counterService",
                  "endpointName": "counterServiceListener"
                }
              }
            ]
          }
        ]
      }
    ]
  }
}
```

Az átjáró konfigurálva van egy Linux-alkalmazás, "meshAppLinux", amely legalább két szolgáltatást, a "helloWorldService" és "counterService", amely a 80-as portot figyeli. A bejövő kérelem URL-cím szerkezete, attól függően, irányítja át a kérés egyik szolgáltatásba. 
* "<IPAddress>: 80/helloWorld/\<kérelem\>" irányítja a helloWorldService a "helloWorldListener" kérelem eredményezne. 
* "<IPAddress>: 80/számláló/\<kérelem\>" irányítja a counterService a "counterListener" kérelem eredményezne. 

## <a name="next-steps"></a>További lépések
* Üzembe helyezése a [bejövő minta](https://github.com/Azure-Samples/service-fabric-mesh/tree/2018-09-01-preview/templates/ingress) átjárók működés megtekintéséhez
