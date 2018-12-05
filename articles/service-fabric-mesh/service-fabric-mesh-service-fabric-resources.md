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
ms.date: 10/23/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 3eeabd4c3bf099d7a0c7007bdf0c8c7e85f3381e
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52889667"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Bevezetés a Service Fabric Erőforrásmodell

A Service Fabric Erőforrásmodell ismerteti egy egyszerű módja egy Service Fabric-háló alkalmazást alkotó erőforrásokat határoz meg. Az egyéni erőforrások bármely Service Fabric-környezetbe is telepíthető.  A Service Fabric Erőforrásmodell egyben kompatibilis az Azure Resource Manager-modellben. A következő típusú erőforrásokat jelenleg ebben a modellben támogatottak:

- Alkalmazások és szolgáltatások
- Hálózatok
- Átjárók
- Titkos kulcsok és titkos kulcsok/értékek
- Kötetek

Minden erőforrás erőforrás-fájlt, amely egy egyszerű YAML vagy JSON-dokumentum, amely ismerteti a háló alkalmazás, és ki van építve a Service Fabric platform deklaratív leírása.

## <a name="applications-and-services"></a>Alkalmazások és szolgáltatások

Egy alkalmazás-erőforrást az üzembe helyezés, a verziókezelés és a egy háló alkalmazás teljes élettartama egysége. Mikroszolgáltatások képviselő egy vagy több, szolgáltatás-erőforrások állnak. Minden egyes szolgáltatás erőforrás, áll, amelyek egy vagy több, írja le, minden a tároló rendszerképét a kódcsomag társított futtatásához szükséges kódot csomagokat.

![Alkalmazások és szolgáltatások][Image1]

Service erőforrás deklarálja a következőket:

- Tároló nevét, verzióját és a beállításjegyzék
- Az egyes tárolók szükséges CPU és memória-erőforrások
- Hálózati végpont
- Más erőforrások, például a hálózatok, kötetek és titkos kulcsok mutató hivatkozások 

Service erőforrás részeként megadott kódcsomagok üzembe és aktiválása együtt, csoportként. A szolgáltatás-erőforrás azt is megtudhatja, hány példányt a szolgáltatás futtatásához, és is hivatkozik az egyéb erőforrások (például hálózati erőforrás) függ.

A háló alkalmazás több szolgáltatás épül fel, ha azok nem biztos, hogy együtt ugyanazon a csomóponton futnak. Ezenkívül az alkalmazás a frissítés során egy szolgáltatás frissítése sikertelen eredményez a korábbi verzióra visszaállításra minden szolgáltatás.

Korábban alluded, mivel mindegyik alkalmazáspéldány életciklusának egymástól függetlenül is felügyelhetők. Ha például egy alkalmazáspéldány egymástól függetlenül is frissíthető az alkalmazáspéldányok a. Általában, tartsa szolgáltatások számának az alkalmazások viszonylag kicsi, mivel a további szolgáltatások mindössze egy alkalmazás, annál nehezebb kezelésére válik minden egyes szolgáltatás egymástól függetlenül.

## <a name="networks"></a>Hálózatok

Hálózati erőforrás külön-külön üzembe helyezhető erőforrásra, független egy alkalmazás vagy szolgáltatás-erőforrás, amely előfordulhat, hogy azok függőségként hivatkozni rá. Hozzon létre egy hálózati alkalmazások szolgál. A különböző alkalmazások több szolgáltatás ugyanazon a hálózaton része lehet.  További információkért olvassa el [hálózatkezelés a Service Fabric-háló alkalmazások](service-fabric-mesh-networks-and-gateways.md).

> [!NOTE]
> Az aktuális előzetes verzió csak az alkalmazások és hálózatok közötti egy az egyhez leképezést támogatja

![Hálózat és átjáró][Image2]

## <a name="gateways"></a>Átjárók
Egy átjáró-erőforrást két hálózat csatlakozik, és átirányítja a forgalmat.  Az átjáró lehetővé teszi, hogy a szolgáltatások, külső ügyfelekkel való kommunikációhoz és a bejövő forgalmi biztosít az az oka.  Az átjáró használatával is lehet a háló-alkalmazás csatlakoztatása a saját, meglévő virtuális hálózattal. További információkért olvassa el [hálózatkezelés a Service Fabric-háló alkalmazások](service-fabric-mesh-networks-and-gateways.md).

![Hálózat és átjáró][Image2]

## <a name="secrets"></a>Titkos kulcsok

Titkos kódok erőforrások függetlenül üzembe helyezhető egy alkalmazás vagy szolgáltatás-erőforrás, amely előfordulhat, hogy azok függőségként hivatkozni rá. Titkos kulcsok biztonságos továbbítására az alkalmazások szolgál. A különböző alkalmazások több szolgáltatást is lehet hivatkozni értékeit a titkos kulcsot.

## <a name="volumes"></a>Kötetek

Tárolók gyakran elérhetővé ideiglenes lemezek. Ideiglenes lemezek olyan rövid élettartamú, azonban, hogy egy új ideiglenes lemez lekérése, és elveszíti az adatokat, ha összeomlik, egy tárolót. Emellett akkor is nehéz ideiglenes lemezek az információk megosztása más tárolóktól. Kötetek, amely csatlakozik a tárolópéldányok állapotban maradnak használó belső könyvtárak. Kötetek általános célú a file storage biztosítanak, és lehetővé teszi olvasási/írási fájlt normál lemez i/o-fájl API-k használatával. A kötet erőforrás egy deklaratív módszert ismertetik, hogyan van csatlakoztatva egy könyvtárat és a biztonsági tárolási, (Azure Files kötet vagy a Service Fabric Reliable kötet).  További információkért olvassa el [állapot tárolására](service-fabric-mesh-storing-state.md#volumes).

![Kötetek][Image3]

## <a name="programming-models"></a>Programozási modellek
Szolgáltatás-erőforrás csak egy tárolórendszerképet szeretne futtatni, amelyre a társított erőforrás kód csomag(ok) a szükséges. Tárolón belül bármely keretrendszer használatával anélkül, hogy tudni, hogy bármilyen nyelven, bármely kódot futtathatja, vagy a Service Fabric-háló adott API-k használata. 

Az alkalmazás kódjában hordozható még a Service Fabric-háló kívül marad, és az alkalmazások központi telepítéseit a nyelv és keretrendszer, a szolgáltatások megvalósításához használt függetlenül konzisztens marad. Az alkalmazás az ASP.NET Core, Go, vagy csak a folyamatok és parancsprogramok halmaza, hogy a Service Fabric-háló erőforrás-alapú üzemi modell változatlan marad. 

## <a name="packaging-and-deployment"></a>Formátumokat támogató csomagolási és üzembe helyezés

Az erőforrás-modellje alapján Service Fabric-háló alkalmazásokat a Docker-tárolókként vannak csomagolva.  Service Fabric-háló egy megosztott, több-bérlős környezet, amely tárolókat biztosít magas szintű elkülönítés.  Ezeket az alkalmazásokat a JSON-formátumú, vagy egy YAML formátumú (amely majd alakul át, JSON) ismerteti. Egy háló-alkalmazást az Azure Service Fabric-háló üzembe helyezésekor, a JSON az alkalmazás leírására szolgáló egy Azure Resource Manager-sablon. Erőforrások az Azure-erőforrások vannak rendelve.  A háló alkalmazás Service Fabric-fürt üzembe helyezésekor (önálló vagy az Azure-ban üzemeltetett), a az alkalmazás leírására szolgáló JSON-ja formátuma a következőhöz hasonló az Azure Resource Manager-sablonnal.  Üzembe helyezését követően háló alkalmazások HTTP-kapcsolatok vagy az Azure CLI segítségével is felügyelhetők. 


## <a name="next-steps"></a>További lépések 
Service Fabric-háló kapcsolatos további információkért olvassa el a áttekintése:
- [Service Fabric-háló áttekintése](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-service-fabric-resources/AppsAndServices.png
[Image2]: media/service-fabric-mesh-service-fabric-resources/NetworkAndGateway.png
[Image3]: media/service-fabric-mesh-service-fabric-resources/volumes.png
