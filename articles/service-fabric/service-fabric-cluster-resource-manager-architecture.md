---
title: Fürterőforrás-kezelő architektúrája |} A Microsoft Docs
description: Az architektúra áttekintése a Service Fabric a fürterőforrás-kezelő.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 6c4421f9-834b-450c-939f-1cb4ff456b9b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: bfbdb05e8d2764d2b878e22d236cae30519da176
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666792"
---
# <a name="cluster-resource-manager-architecture-overview"></a>A fürt resource manager-architektúra áttekintése
A Service Fabric fürterőforrás-kezelő középső szolgáltatást, amely a fürt. A szolgáltatások a fürtben, különös tekintettel az erőforrás-használat és elhelyezési szabályok kívánt állapotát kezeli azt. 

A fürt erőforrásainak kezeléséhez, a Service Fabric fürterőforrás-kezelő több adatra kell rendelkeznie:

- Mely szolgáltatások jelenleg található.
- Minden egyes szolgáltatás jelenlegi (vagy alapértelmezett) erőforrás-használat 
- A fennmaradó fürt kapacitása 
- A fürtben lévő csomópontok kapacitása 
- Minden egyes csomóponton használt erőforrások mennyisége

Az adott szolgáltatás erőforrás-használat idővel változhat, és a szolgáltatások általában érdeklik egynél több erőforrás típusát. Több különböző szolgáltatásokat a mért valós fizikai és fizikai erőforrások lehetnek. Szolgáltatások fizikai mérőszámokat, például a memória- és használati követheti nyomon. Leggyakrabban szolgáltatások is érdeklik a logikai mérőszámai – például a "WorkQueueDepth" vagy "TotalRequests". Logikai és fizikai metrikákat is használható ugyanazon a fürtön. Metrikák is számos szolgáltatás között lehetnek megosztva, vagy külön az adott szolgáltatáshoz.

## <a name="other-considerations"></a>Egyéb szempontok
A tulajdonosai és üzemeltetői a fürt eltérhet a szolgáltatások és alkalmazások szerzőkkel, vagy legalább a azonos személyek elhasználódó különböző hats. Az alkalmazás fejlesztése során ismernie néhány dolgot, a szükséges információk. Az erőforrások fog felhasználni becsült rendelkezik, és hogyan kell üzembe helyezni különböző szolgáltatásokat. Például a webes szint elérhetővé tett csatlakozik az internethez, miközben az adatbázis-szolgáltatásokat nem kell csomópontokon való futáshoz van szüksége. Másik példaként a webes szolgáltatások vannak valószínűleg által korlátozott Processzor és a hálózat további információ a memória- és használati adatok szintű szolgáltatások ellátás közben. Azonban az adott szolgáltatásnak a termelés vagy a frissítés a Service kezel a webhelyek működés közbeni incidensek kezelése személy rendelkezik-e egy másik feladat szeretné elvégezni, és a különböző eszközök igényel. 

A fürt és a szolgáltatások dinamikusak:

- A fürtben található csomópontok száma növelhető vagy csökkenthető
- A különböző méretű és típusú csomópontok származnak, és nyissa meg
- Szolgáltatások is létrehozhatók, eltávolítva, és módosítsa a kívánt erőforrás-hozzárendelések és elhelyezési szabályok
- Frissítések vagy az egyéb felügyeleti műveleteket lehet vonni az alkalmazások a fürtön keresztül infrastruktúra szinten
- Hiba fordulhat elő, tetszőleges időpontban.

## <a name="cluster-resource-manager-components-and-data-flow"></a>Fürt resource manager-összetevők és az adatfolyam
A fürterőforrás-kezelő nyomon az egyes szolgáltatások követelményei és az erőforrások felhasználását ezeket a szolgáltatásokat minden egyes szolgáltatás objektummal rendelkezik. A fürterőforrás-kezelő két részből fogalmi: arról, hogy egyes csomópontok és a egy hibatűrő szolgáltatás ügynökök. Az ügynökök minden egyes csomópont követése betöltése a szolgáltatások, az összesítő jelentések őket, és rendszeres időközönként jelenti azokat. A fürterőforrás-kezelő szolgáltatás összesíti a helyi ügynökök és az aktuális konfigurációja alapján reagál kapcsolatos összes információt.

Nézzük meg a következő ábra:

<center>

![Resource Balancer-architektúra][Image1]
</center>

Futásidőben a rendszer számos módosítást, amely akkor fordulhat elő. Például hozzunk tegyük az egyes szolgáltatások erőforrásokért mennyisége megváltozik, bizonyos szolgáltatások sikertelenek lesznek, és az egyes csomópontok és annak megszüntetéséről a fürt. A csomópont a módosítások összesítve, és rendszeres időközönként a fürterőforrás-kezelő szolgáltatásnak küldött (1,2) ahol újra összesítése, elemzése, és tárolt. Minden néhány másodpercben, amely a szolgáltatás megvizsgálja a módosításokat, és határozza meg, ha szükség-e azokat a műveleteket (3). Például ez sikerült figyelje meg, hogy a fürt egyes üres csomópontok bővült. Ennek eredményeképpen dönt, hogy az egyes szolgáltatások át azokat a csomópontokat. A fürterőforrás-kezelő sikerült is figyelje meg, hogy egy adott csomópont van-e túlterhelve, vagy azt, hogy egyes szolgáltatások nem sikerült-e vagy törölve lett, más erőforrások felszabadítása.

Nézzük tekintse meg az alábbi ábrán, és tekintse meg a következő lépésekről. Tegyük fel, hogy a fürterőforrás-kezelő határozza meg, hogy a módosítások szükségesek. Ez koordinálja más rendszer szolgáltatásokkal (különösen a Feladatátvevőfürt-kezelő), a szükséges módosításokat. Majd a szükséges parancsok küldve a megfelelő csomópontok (4). Például tegyük fel, az erőforrás-kezelő észrevette, hogy a Csomópont5 túl lett terhelve, és így úgy döntött, hogy a csomópont4 Csomópont5 service B áthelyezéséhez. A fürt végén az újrakonfigurálás (5), a következőhöz hasonló:

<center>

![Resource Balancer-architektúra][Image2]
</center>

## <a name="next-steps"></a>További lépések
- A fürterőforrás-kezelő leíró a fürt számos lehetőség áll. A velük kapcsolatos további információért tekintse meg a cikk a [leíró, Service Fabric-fürt](./service-fabric-cluster-resource-manager-cluster-description.md)
- A fürt Resource Manager elsődleges feladatkörök újraegyensúlyozása a fürt és elhelyezési szabályok kényszerítése. Ezen viselkedés konfigurálásával kapcsolatos további információkért lásd: [terheléselosztás a Service Fabric-fürt](./service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png
