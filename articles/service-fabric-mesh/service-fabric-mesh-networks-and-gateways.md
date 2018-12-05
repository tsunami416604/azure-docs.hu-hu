---
title: Azure Service Fabric-hálózatkezelés – bevezetés |} A Microsoft Docs
description: Ismerje meg a hálózatok, az átjárók és az intelligens forgalmának irányítását a Service Fabric-háló.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/26/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 8d9f5c71d05477094d733fd6b798831b3ff02ce1
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52892361"
---
# <a name="introduction-to-networking-in-service-fabric-mesh-applications"></a>Bevezetés a Service Fabric-háló alkalmazások a hálózatkezelés
Ez a cikk ismerteti a terheléselosztók különböző típusú, hogyan átjárók csatlakozni más hálózatokhoz az alkalmazásokkal a hálózati és adatforgalmat a szolgáltatásokat az alkalmazások között.

## <a name="layer-4-vs-layer-7-load-balancers"></a>A vs-7. rétegbeli terheléselosztók 4. réteg
Terheléselosztás a különböző rétegeket hajthatók végre a [OSI-modell](https://en.wikipedia.org/wiki/OSI_model) a hálózatkezelés, milyen gyakran a réteg 4 (4. rétegbeli) és (7. rétegbeli) 7. réteg.  Általában a terheléselosztók két típusa van:

- Egy 4. rétegbeli terheléselosztó működik a hálózati átviteli réteg, amely foglalkozik, nem a csomagok tartalmának tekintettel csomagok kézbesítését. Csak a csomagfejléceket kell vizsgálni a terheléselosztó által, így a terheléselosztási feltételek IP-címek és portok. Például egy ügyfél küld a TCP-kapcsolat a terheléselosztóhoz. A load balancer lezárja a kapcsolatot (által közvetlenül a szinkronizálás a mi válaszol), kiválaszt egy háttérbeli, és lehetővé teszi egy új TCP-kapcsolat, a háttérkiszolgáló (küld egy új külön). Egy 4. rétegbeli terheléselosztó általában csak a TCP/UDP/4. rétegbeli kapcsolatot vagy a munkamenet a szintjén működik. Így a terheléselosztóval átirányítja a bájtok körül, és gondoskodik arról, hogy az ugyanazon munkamenet-ből származó bájtok megszünteti, ugyanazzal a háttérrendszerrel. A 4. rétegbeli terheléselosztó nem észleli a mozgás bájtok bármely alkalmazás részletei. A bájtok lehet bármely alkalmazás protokoll.

- Egy 7. rétegbeli terheléselosztó működik az alkalmazási rétegben, amely az egyes csomagok tartalmát foglalkozik. Megvizsgálja a csomag tartalma, mert érti például a HTTP, HTTPS és WebSockets protokollt. Ez lehetővé teszi a terheléselosztó az útválasztást végrehajtásához. Például egy ügyfél küld a terheléselosztó egyetlen HTTP/2 TCP-kapcsolatot. A terheléselosztó ezután telepítése folytatódik, hogy a két háttér-kapcsolatot. Az ügyfél a terheléselosztó két HTTP/2 adatfolyamot küld, ha egy stream érkezik egy háttér, és két háttérbeli küldendő két adatfolyam. Így még az ügyfelekről, amelyek teljesen más kérelem terhelések multiplexálási fog egyenletesen eloszlik hatékonyan a háttérrendszerek közötti. 

## <a name="networks-and-gateways"></a>Hálózatok és átjárók
Az a [Service Fabric Erőforrásmodell](service-fabric-mesh-service-fabric-resources.md), hálózati erőforráshoz egy külön-külön üzembe helyezhető erőforrásra, független egy alkalmazás vagy szolgáltatás-erőforrás, amely előfordulhat, hogy azok függőségként hivatkozni rá. Hozzon létre egy hálózati alkalmazásai számára, amely meg van nyitva, az internethez szolgál. A különböző alkalmazások több szolgáltatás ugyanazon a hálózaton egy része lehet. A magánhálózati jön létre, és a Service Fabric által kezelt és nem egy Azure virtuális hálózaton (VNET). Alkalmazások dinamikusan hozzáadható és eltávolítja a hálózati erőforrás engedélyezheti és tilthatja le a virtuális hálózatok közötti kapcsolat. 

Az átjáró két hálózatot hídként szolgál. Az átjáró-erőforrást telepít egy [Envoy proxy](https://www.envoyproxy.io/) , amely minden protokollhoz 4. rétegbeli és 7. rétegbeli speciális HTTP (S) alkalmazás útválasztás útválasztást biztosít. Az átjáró irányítja a forgalmat egy külső hálózatról a hálózatban, és meghatározza, hogy melyik szolgáltatást a forgalom irányítására.  Az open hálózat (alapvetően a nyilvános interneten) vagy egy Azure virtuális hálózatra, így a többi Azure-alkalmazások és erőforrások összekapcsolása a külső hálózat lehet. 

![Hálózat és átjáró][Image1]

A hálózati erőforrás létrehozásakor az `ingressConfig`, nyilvános IP-cím hozzá van rendelve a hálózati erőforrás. A nyilvános IP-cím a hálózati erőforrás élettartama fogja kötni.

A háló alkalmazás létrehozásakor azt egy meglévő hálózati erőforrást kell hivatkoznia. Új nyilvános portokról adhatók hozzá, vagy meglévő portok távolíthatja el a bejövő forgalom konfigurációjából. A hálózati erőforrás törlése sikertelen lesz, ha egy alkalmazás-erőforrás hivatkozik. Az alkalmazás törlése esetén a rendszer eltávolítja a hálózati erőforrás.

## <a name="next-steps"></a>További lépések 
Service Fabric-háló kapcsolatos további információkért olvassa el a áttekintése:
- [Service Fabric-háló áttekintése](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-networks-and-gateways/NetworkAndGateway.png