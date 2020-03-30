---
title: Átjáró konfigurálása útvonalkérésekhez
description: Ismerje meg, hogyan konfigurálhatja az átjárót, amely kezeli a Service Fabric Mesh-en futó alkalmazás(ok) bejövő forgalmát.
author: dkkapur
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: ec408403d4baa0f211c6bfe867a15c96513693cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75461950"
---
# <a name="configure-a-gateway-resource-to-route-requests"></a>Átjáró-erőforrás konfigurálása útvonalkérelmekhez

Az átjáró-erőforrás a bejövő forgalom az alkalmazásnak otthont ad otthont tartalmazó hálózatra történő irányítására szolgál. Konfigurálja úgy, hogy olyan szabályokat adjon meg, amelyeken keresztül a kérelmek adott szolgáltatásokhoz vagy végpontokhoz irányulnak a kérelem szerkezete alapján. [A hálózatokról](service-fabric-mesh-networks-and-gateways.md) és átjárókról a Mesh hálózatában című témakörben talál további információt a hálózat- és átjárókról. 

Az átjáró-erőforrásokat a központi telepítési sablon (JSON vagy yaml) részeként kell deklarálni, és egy hálózati erőforrástól függenek. Ez a dokumentum ismerteti a különböző tulajdonságokat, amelyek beállíthatók az átjáró, és kiterjed a minta átjáró konfiguráció.

## <a name="options-for-configuring-your-gateway-resource"></a>Az átjáró erőforrás konfigurálásának lehetőségei

Mivel az átjáró erőforrás hídként szolgál az alkalmazás hálózata és az `open` alapul szolgáló infrastruktúra hálózata (a hálózat) között. Csak egy beállítást kell konfigurálnia (a Mesh előzetes verzióban alkalmazásonként legfeljebb egy átjáró van). Az erőforrás deklarációja két fő részből áll: az erőforrás metaadataiból és a tulajdonságokból. 

### <a name="gateway-resource-metadata"></a>Átjáróerőforrás metaadatai

Az átjáró deklarálva a következő metaadatokkal történik:
* `apiVersion`- kell állítani a "2018-09-01-preview" (vagy később, a jövőben)
* `name`- az átjáró karakterláncának neve
* `type`- "Microsoft.ServiceFabricMesh/átjárók"
* `location`- be kell állítani, hogy a helyét az alkalmazás / hálózat; általában a telepítési környezetben lévő helyparaméterre való hivatkozás
* `dependsOn`- az a hálózat, amelynek ez az átjáró a

Az Azure Resource Manager (JSON) telepítési sablonban a következőképpen jelenik meg: 

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

A tulajdonságok szakasz azátjáró t anamandórendszerének hálózatait és az útválasztási kérelmekre vonatkozó szabályokat határozza meg. 

#### <a name="source-and-destination-network"></a>Forrás- és célhálózat 

Minden átjáróhoz `sourceNetwork` `destinationNetwork`szükség van egy és . A forráshálózat az a hálózat, amelyről az alkalmazás bejövő kéréseket fog fogadni. A name tulajdonságának mindig "Open" (Nyitott) beállításra kell állítva. A célhálózat az a hálózat, amelyet a kérelmek céloznak. Ennek a névértékét az alkalmazás helyi hálózatának erőforrásnevére kell beállítani (teljes hivatkozást kell tartalmaznia az erőforrásra). Lásd alább a minta konfiguráció, hogy ez hogyan néz ki a központi telepítés a hálózat úgynevezett "myNetwork".

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

Egy átjáró több útválasztási szabóval is rendelkezhet, amelyek meghatározzák a bejövő forgalom kezelésének módját. Az útválasztási szabály határozza meg a figyelőport és egy adott alkalmazás célvégpontja közötti kapcsolatot. A TCP útválasztási szabályok esetében 1:1 hozzárendelés van a Port:Endpoint között. Http útválasztási szabályok esetén összetettebb útválasztási szabályokat állíthat be, amelyek a kérelem elérési útját és adott esetben fejléceket vizsgálnak a kérelem átirányításának eldöntéséhez. 

Az útválasztási szabályok portonként vannak megadva. Minden egyes be- éselődési port saját szabálytömböt biztosít az átjáró konfigurációs tulajdonságszakaszában. 

#### <a name="tcp-routing-rules"></a>TCP útválasztási szabályok 

A TCP útválasztási szabály a következő tulajdonságokból áll: 
* `name`- hivatkozás a szabályra, amely tetszőleges karakterlánc lehet 
* `port`- port figyelni a bejövő kérelmek 
* `destination`- végpontspecifikáció, `applicationName`amely `serviceName`tartalmazza, és `endpointName`ahol a kérelmeket a

Íme egy példa a TCP útválasztási szabályra:

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


#### <a name="http-routing-rules"></a>HTTP útválasztási szabályok 

A HTTP útválasztási szabály a következő tulajdonságokból áll: 
* `name`- hivatkozás a szabályra, amely tetszőleges karakterlánc lehet 
* `port`- port figyelni a bejövő kérelmek 
* `hosts`- a fent megadott porton található különböző "állomásokra" érkező kérésekre vonatkozó szabályzatok sora. A gazdagépek olyan alkalmazások és szolgáltatások, amelyek a hálózaton futnak, és bejövő kéréseket, azaz egy webalkalmazást is kiszolgálhatnak. A gazdaházirendek értelmezése sorrendben van értelmezve, ezért a következőket kell létrehoznia csökkenő specificitási szinteken
    * `name`- annak az állomásnak a DNS-neve, amelyre a következő útválasztási szabályok vannak megadva. A "*" használata itt útválasztási szabályokat hozna létre az összes állomás számára.
    * `routes`- egy sor politika erre a konkrét fogadó
        * `match`- a szabály alkalmazásához beérkező kérelemstruktúra meghatározása, amely a`path`
            * `path`- tartalmaz `value` egy (bejövő `rewrite` URI), (hogyan szeretné a kérelmet `type` továbbítani), és egy (jelenleg csak "Előtag")
            * `header`- egy választható tömb fejlécek értékek egyeznek a kérelem fejlécében, hogy ha a kérelem megfelel az elérési út specifikációja (fent).
              * minden bejegyzés `name` tartalmazza (a megegyezendő fejléc karakterláncneve), `value` (a fejléc `type` karakterláncértéke a kérelemben), és egy (jelenleg csak "Pontos" lehet)
        * `destination`- ha a kérelem megegyezik, akkor a kérés erre a `applicationName` `serviceName`célállomásra lesz irányítva, amelyet egy , , és`endpointName`

Íme egy példa http-útválasztási szabály, amely a 80-as porton érkező kérelmekre vonatkozna a hálózat ban lévő alkalmazások által kiszolgált összes állomásra. Ha a kérelem URL-címe olyan struktúrával rendelkezik, `<IPAddress>:80/pickme/<requestContent>`amely megfelel az elérési `myListener` út specifikációjának, azaz , akkor a végpontra lesz irányítva.  

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

## <a name="sample-config-for-a-gateway-resource"></a>Példa konfigurációs átjáró-erőforráshoz 

Itt van, amit a teljes Gateway erőforrás konfigurációs néz ki (ez igazodik a bejövő adatok minta elérhető a [háló minták tártára):](https://github.com/Azure-Samples/service-fabric-mesh/blob/2018-09-01-preview/templates/ingress/meshingress.linux.json)

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

Ez az átjáró egy Linux-alkalmazáshoz, a "meshAppLinux"-hoz van konfigurálva, amely legalább két szolgáltatásból áll, a "helloWorldService" és a "counterService", amelyek a 80-as porton figyelnek. A bejövő kérelem URL-struktúrájától függően a kérést a szolgáltatások egyikéhez irányítja. 
* "\<IPAddress>:80/helloWorld/\<request\>" azt eredményezné, hogy a kérés a helloWorldListener "helloWorldListener" címére irányulna a helloWorldService szolgáltatásban. 
* Az\<" IPAddress>:80/counter/\<request\>" azt eredményezné, hogy a kérés a counterService "counterListener" címére irányulna. 

## <a name="next-steps"></a>További lépések
* A [bejövő támadások minta](https://github.com/Azure-Samples/service-fabric-mesh/tree/2018-09-01-preview/templates/ingress) telepítése az átjárók működés közbeni megtekintéséhez
