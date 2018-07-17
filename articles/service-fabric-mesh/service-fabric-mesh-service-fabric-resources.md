---
title: Az Azure Service Fabric Erőforrásmodell bemutatása |} A Microsoft Docs
description: További információ a Service Fabric erőforrás modell, egy egyszerűsített megközelítése alkalmazások Service Fabric-háló.
services: service-fabric-mesh
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 6a96995a46cd2ad0a1afe4f4afe49d62409f2b68
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39076096"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Bevezetés a Service Fabric Erőforrásmodell

Service Fabric Erőforrásmodell ismerteti egy egyszerű módja egy Service Fabric-háló alkalmazást alkotó erőforrásokat határoz meg. A következő típusú erőforrásokat jelenleg ebben a modellben támogatottak:

- Alkalmazások
- Szolgáltatások
- Kötetek
- Hálózatok

Minden erőforrás erőforrás-fájlt, amely egy egyszerű YAML vagy JSON-dokumentum, amely ismerteti a háló alkalmazás, és ki van építve a Service Fabric platform deklaratív leírása.

## <a name="resource-overview"></a>Erőforrás-áttekintés

### <a name="applications-and-services"></a>Alkalmazások és szolgáltatások

Egy alkalmazás-erőforrást az üzembe helyezés, a verziókezelés és a egy háló alkalmazás teljes élettartama egysége. Egy vagy több, szolgáltatás-erőforrások, amelyek a mikroszolgáltatások áll. Minden egyes szolgáltatás erőforrás, áll, amelyek egy vagy több, írja le, minden a tároló rendszerképét a kódcsomag, többek között az alábbi társított futtatásához szükséges kódot csomagokat:

- Tároló nevét, verzióját és a beállításjegyzék
- Az egyes tárolók szükséges CPU és memória-erőforrások
- Hálózati végpont
- Kötetek a tárolóban, egy önálló kötetre erőforrásra hivatkozik.

Service erőforrás részeként megadott kódcsomagok üzembe és aktiválása együtt, csoportként. A szolgáltatás-erőforrás azt is megtudhatja, hány példányt a szolgáltatás futtatásához, és is hivatkozik az egyéb erőforrások (például hálózati erőforrás) függ.

A háló alkalmazás több szolgáltatás épül fel, ha azok nem biztos, hogy együtt ugyanazon a csomóponton futnak. Ezenkívül az alkalmazás a frissítés során egy szolgáltatás frissítése sikertelen eredményez a korábbi verzióra visszaállításra minden szolgáltatás.



```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

Korábban alluded, mivel mindegyik alkalmazáspéldány életciklusának egymástól függetlenül is felügyelhetők. Ha például egy alkalmazáspéldány egymástól függetlenül is frissíthető az alkalmazáspéldányok a. Általában, tartsa szolgáltatások számának az alkalmazások viszonylag kicsi, mivel a további szolgáltatások mindössze egy alkalmazás, annál nehezebb kezelésére válik minden egyes szolgáltatás egymástól függetlenül.

### <a name="networks"></a>Hálózatok

Hálózati erőforrás külön-külön üzembe helyezhető erőforrásra, független egy alkalmazás vagy szolgáltatás-erőforrás, amely előfordulhat, hogy azok függőségként hivatkozni rá. Magánhálózatot hozhat létre az alkalmazások szolgál. A különböző alkalmazások több szolgáltatás ugyanazon a hálózaton része lehet.

> [!NOTE]
> Az aktuális előzetes verzió csak az alkalmazások és hálózatok közötti egy az egyhez leképezést támogatja

### <a name="volumes"></a>Kötetek

Kötetek, amely csatlakozik a tárolópéldányok állapotban maradnak használó belső könyvtárak. A köteterőforrás deklaratív lehetőséget ismertetik, hogyan van csatlakoztatva egy könyvtárat és a hozzá tartozó biztonsági tároló.

## <a name="programming-models"></a>Programozási modellek
Szolgáltatás-erőforrás csak egy tárolórendszerképet szeretne futtatni, amelyre a társított erőforrás kód csomag(ok) a szükséges. Tárolón belül bármely keretrendszer használatával anélkül, hogy tudni, hogy bármilyen nyelven, bármely kódot futtathatja, vagy a Service Fabric-háló adott API-k használata. 

Az alkalmazás kódjában hordozható még a Service Fabric-háló kívül marad, és az alkalmazások központi telepítéseit a nyelv és keretrendszer, a szolgáltatások megvalósításához használt függetlenül konzisztens marad. Az alkalmazás az ASP.NET Core, Go, vagy csak a folyamatok és parancsprogramok halmaza, hogy a Service Fabric-háló erőforrás-alapú üzemi modell változatlan marad. 

## <a name="deployment"></a>Környezet

Amikor a Service Fabric-háló üzembe helyezése erőforrások üzemelnek Azure HTTP Protokollon keresztül vagy az Azure CLI Azure Resource Manager-sablonok. 


## <a name="next-steps"></a>További lépések 
Service Fabric-háló kapcsolatos további információkért olvassa el a áttekintése:
- [Service Fabric-háló áttekintése](service-fabric-mesh-overview.md)
