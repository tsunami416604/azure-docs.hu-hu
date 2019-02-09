---
title: Önálló Service Fabric-fürtök – áttekintés |} A Microsoft Docs
description: Service Fabric-fürtök a Windows Server és Linux rendszerű, mely azt jelenti, hogy lesz üzembe helyezéséhez és gazdagép Service Fabric-alkalmazások bárhol futtatható Windows Server vagy Linux.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: 5d3421c4d48a6bf8416a774c4b4e5e7852a83f06
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967339"
---
# <a name="overview-of-service-fabric-standalone-clusters"></a>A Service Fabric önálló fürtök – áttekintés

Service Fabric-fürt, amelybe mikroszolgáltatásokat helyezhet üzembe és felügyelhet virtuális vagy fizikai gépek hálózaton keresztül csatlakozó készlete áll. Egy számítógép vagy virtuális Gépet, amely egy fürt része egy fürt csomópontja nevezzük. Fürtök méretezheti több ezer csomóponton. Ha új csomópontot ad hozzá a fürtöt, akkor a Service Fabric csomópontok megnövekedett számú rebalances a szolgáltatás partíció replikák és példányok. A teljes javítja az alkalmazások teljesítményéről, és csökkenti a versengés memória való hozzáféréshez. Ha a fürt csomópontjainak hatékonyan nincsenek használatban, csökkentheti a fürtben található csomópontok számát. A Service Fabric rebalances a partíciók replikáit és példányainak újra a csomópontokat, hogy a hardver jobban kihasználja a csomópontokon csökkent számú.

A csomópont típusa meghatározza a méretét, számát és tulajdonságait a beállított a csomópontok a fürtben. Ezután mindegyik csomóponttípus egymástól függetlenül skálázható vertikálisan le vagy fel, eltérő nyitott portokkal rendelkezhet, és eltérő kapacitásmetrikái lehetnek. A csomóponttípusok a fürtcsomópontcsoportok szerepkörének (például „előtér” vagy „háttér”) meghatározására szolgálnak. A fürt több csomóponttípussal is rendelkezhet, de éles fürtök esetében az elsődleges csomóponttípusnak legalább öt (vagy tesztfürtök esetében legalább három) virtuális géppel kell rendelkeznie. [A Service Fabric-rendszerszolgáltatások](service-fabric-technical-overview.md#system-services) elhelyezése az elsődleges csomóponttípusra történik.

A folyamat a Service Fabric-fürt létrehozása a helyszíni hasonlít a virtuális gépek olyan tetszőleges a felhőben egy fürt létrehozásának folyamatán. A kezdeti lépéseket annak a virtuális gépek kiépítése a felhőszolgáltató vagy a helyszíni környezetben használt vonatkoznak rájuk. Ha engedélyezve van azok között hálózati kapcsolattal rendelkező virtuális gépek csoportját, majd állítsa be a Service Fabric-csomag, a lépéseket a fürt beállítások szerkesztése, és futtassa a fürt létrehozása és felügyeleti parancsfájlokat azonosak. Ez biztosítja, hogy tudását és üzemeltetése és kezelése a Service Fabric-fürtök élménye átruházható Ha új üzemeltetési környezetek lehetőséget választja.

## <a name="cluster-security"></a>Fürtbiztonság
Service Fabric-fürt saját erőforrás.  A feladata biztonságossá tétele a-fürtök segítségével megakadályozhatja a jogosulatlan felhasználókat csatlakozik hozzájuk. Biztonságos fürt különösen fontos, ha a fürtön futtatnak éles számítási feladatokat.

### <a name="node-to-node-security"></a>Csomópontok közötti biztonsági
Csomópontok közötti biztonsági védi a virtuális gépek és a egy fürtben számítógépek közötti kommunikációt. A biztonsági forgatókönyv biztosítja, hogy csak a fürthöz való csatlakoztatáshoz jogosult számítógépek részt vehetnek-üzemeltető, alkalmazások és szolgáltatások a fürtben. Service Fabric-fürt biztonságossá tétele és adja meg az alkalmazás biztonsági funkciók X.509-tanúsítványokat használ.  Egy fürt tanúsítványra szükség a fürt forgalmának biztonságossá tétele és a fürt és a kiszolgáló hitelesíthető.  Önkiszolgáló aláírt-tanúsítványok a tesztfürtök esetében használhatók, de egy megbízható hitelesítésszolgáltatótól származó tanúsítványt használjon biztonságos éles fürtökben.

Windows biztonsági Windows önálló fürt is engedélyezhető. Ha a Windows Server 2012 R2 és Windows Active Directory, azt javasoljuk, hogy a csoportosan felügyelt szolgáltatásfiókok használatához Windows biztonsági. Ellenkező esetben használja a Windows biztonsági Windows-fiókokkal.

További információkért olvassa el [csomópontok biztonság](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Ügyfél-csomópont biztonsági
Ügyfél-csomópont biztonsági hitelesíti az ügyfeleket, és segít az ügyfél és a fürt egyes csomópontjain közötti biztonságos kommunikációhoz. Az ilyen típusú biztonsági segítségével biztosíthatja, hogy csak a jogosult felhasználók hozzáférését a fürt és a fürtön üzembe helyezett alkalmazások. Az ügyfelek kapcsolódnak egyedileg azonosított keresztül vagy X.509 tanúsítvány biztonsági hitelesítő adataik. Nem kötelező ügyféltanúsítványok tetszőleges számú használható a fürt rendszergazdai vagy felhasználói ügyfelek hitelesítéséhez.

Ügyfél-tanúsítványok mellett az Azure Active Directory is konfigurálhatja a fürt az ügyfelek hitelesítéséhez.

További információkért olvassa el [ügyfél a csomópont közötti biztonsághoz](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control-rbac"></a>Szerepköralapú hozzáférés-vezérlés (RBAC)
A Service Fabric támogatja a hozzáférés-vezérlés bizonyos fürt műveletek a különböző felhasználói csoportokhoz való hozzáférés korlátozására is. Ez segít a fürt biztonságát. Hozzáférés-vezérlési kétféle fürthöz csatlakozó ügyfelek támogatottak: Rendszergazdai szerepkör és a felhasználói szerepkör.  

További információkért olvassa el [szerepköralapú hozzáférés-vezérlés (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac).

## <a name="scaling"></a>Méretezés

Alkalmazások számára az idő előrehaladtával változik. Szükség lehet növelni a fürterőforrások felel meg az alkalmazás nagyobb számítási feladatok vagy a hálózati forgalom, vagy csökkentse a fürterőforrások, ha az igény csökken. Egy Service Fabric-fürt létrehozását követően, horizontálisan a fürt (módosíthatja a csomópontok számát), vagy függőlegesen (módosíthatja a csomópontok az erőforrások). Méretezheti a fürt bármikor, még akkor is, ha a számítási feladatok a fürtön futnak. A fürt skálázható, mivel az alkalmazások automatikus méretezése is.

További információkért olvassa el [önálló fürtök méretezése](service-fabric-cluster-scaling-standalone.md).

## <a name="upgrading"></a>Frissítés

Önálló fürt egy erőforrást, amikor teljesen saját. Ön felelős az alapul szolgáló operációs rendszer javításait, és hálófrissítések indítása. Állítsa be a fürt automatikus futtatókörnyezet frissítését, kapni a Microsoft által kiadott új verzióra, vagy kiválaszthat egy támogatott futtatókörnyezet-verzióra. Hálófrissítések, mellett is javítani az operációs rendszer, és frissítse a fürt konfigurációját, például a tanúsítványokat és alkalmazásportok. 

További információkért olvassa el [önálló fürtök frissítése](service-fabric-cluster-upgrade-standalone.md).

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek
Ön létre tudja hozni a fürtök virtuális gépek vagy ezek (Linux még nem támogatott) operációs rendszert futtató számítógépeken:

* Windows Server 2012 R2
* Windows Server 2016 

## <a name="next-steps"></a>További lépések
Tudjon meg többet [biztonságossá tétele](service-fabric-cluster-security.md), [skálázás](service-fabric-cluster-scaling-standalone.md), és [frissítése](service-fabric-cluster-upgrade-standalone.md) önálló fürtök.

Ismerje meg [Service Fabric támogatási lehetőségeinek](service-fabric-support.md).