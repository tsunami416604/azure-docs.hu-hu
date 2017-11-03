---
title: "Erőforrás-kezelő architektúra |} Microsoft Docs"
description: "Az architektúra áttekintése a Service Fabric fürt Resource Manager."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 6c4421f9-834b-450c-939f-1cb4ff456b9b
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: f0d2202c17bf4d378a625a61e941edf7f3f24636
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="cluster-resource-manager-architecture-overview"></a>Fürt resource manager architektúrájának áttekintése
A Service Fabric fürt erőforrás-kezelő központi szolgáltatás, amely a fürt. Kezeli a kívánt azon szolgáltatások állapotát, a fürtben, különös tekintettel az erőforrás-felhasználás és elhelyezési szabályokat. 

Az erőforrások a fürt felügyeletéhez, a Service Fabric fürt erőforrás-kezelő több adatra kell rendelkeznie:

- Mely szolgáltatások jelenleg található.
- Minden szolgáltatás aktuális (vagy alapértelmezett) erőforrás-felhasználás 
- Fürt kapacitása 
- A fürt a csomópontok kapacitása 
- Minden egyes csomóponton használt erőforrások mennyisége

Az erőforrás-felhasználás adott szolgáltatás idővel megváltozhat, és a szolgáltatások általában érdeklik egynél több erőforrás típusát. Különböző szolgáltatásban valószínűleg mért tényleges fizikai mind a fizikai erőforrásokat. Szolgáltatások például lemez- és memória-felhasználás fizikai metrikák követheti nyomon. Szolgáltatások gyakrabban, logikai metrikák – többek között a "WorkQueueDepth" vagy "TotalRequests" lehet, hogy érdeklik. Logikai és fizikai metrikák ugyanazon fürt használható. Metrikák meg lehet osztani számos szolgáltatás összes vagy egy adott szolgáltatással.

## <a name="other-considerations"></a>Egyéb szempontok
A tulajdonosok és a fürt operátorok különbözhet a szolgáltatások és alkalmazások szerzők, vagy legalább a azonos személyek elhasználódó különböző kalap. Amikor az alkalmazás fejlesztése ismernie néhány dolgot kapcsolatos akkor van szükség. Az erőforrások fog felhasználni becsült rendelkezik, és hogyan kell telepíteni, különböző szolgáltatások. Például a webes réteg kell csomópontok kommunikál az internettel, amíg az adatbázis-szolgáltatásokat nem kell futnia. Másik példaként a web services rendszer valószínűleg által korlátozott Processzor- és hálózati, miközben az adatok réteg szolgáltatások gondot memóriát és lemezterületet fogyasztás kapcsolatos további információkért. Azonban a live-hely, amelyhez a termelésre vagy a szolgáltatás frissítése kezel szolgáltatás kezelése személy rendelkezik-e egy másik feladat elvégzéséhez, és más eszközök. 

A fürt és a szolgáltatások a következők dinamikus:

- A fürtben lévő csomópontok száma növelhető vagy csökkenthető
- Különböző méretű és csomópontok származnak, és nyissa meg
- Szolgáltatások is létrehozható, eltávolítva, és módosítsa a kívánt erőforrás-hozzárendelések és elhelyezési szabályokat
- Frissítések vagy az egyéb felügyeleti műveleteket lehet vonni az alkalmazás a fürthöz keresztül infrastruktúra szinten
- Hiba fordulhat elő, tetszőleges időpontban.

## <a name="cluster-resource-manager-components-and-data-flow"></a>Fürt resource manager összetevőit és adatfolyama
A fürt erőforrás-kezelő egyes szolgáltatások követelményei és az erőforrások felhasználása nyomon ezekbe a szolgáltatásokba minden szolgáltatás objektummal rendelkezik. A fürt erőforrás-kezelő két részből áll fogalmi: minden csomópont- és hibatűrő szolgáltatásként futó ügynököket. Az ügynököt a csomópontra követése tartozó szolgáltatásokat, összesített jelentéseinek őket, és rendszeresen jelenti. A fürt erőforrás-kezelő szolgáltatás a helyi ügynökök és a jelenlegi konfiguráció alapján reagál lévő adatok összesíti.

A következő ábra vizsgáljuk meg:

<center>
![Erőforrás terheléselosztó architektúrája][Image1]
</center>

Futásidőben, sok módosítások vannak, amely akkor fordulhat elő. Például most mondja ki az egyes szolgáltatások felhasználásához erőforrások mennyisége változik, bizonyos szolgáltatások sikertelen lesz, és egyes csomópontok csatlakozásról és annak megszüntetéséről a fürt. A csomópont a módosítások összesített értéket, és rendszeres időközönként a fürt erőforrás-kezelő szolgáltatásnak küldött (1,2) ahol ezek újra összesítve, elemzése, és tárolja. Minden néhány másodpercben, amely a szolgáltatás ellenőrzi, hogy a módosításokat, és határozza meg, ha szükség-e azokat a műveleteket (3). Például azt sikerült figyelje meg, hogy néhány üres csomópontokat a fürthöz hozzáadott. Ennek eredményeképpen dönt, hogy az egyes szolgáltatások áthelyezése azokat a csomópontokat. A fürt erőforrás-kezelő sikerült is vegye figyelembe, hogy egy adott csomópont túl van terhelve, vagy az, hogy egyes szolgáltatások nem sikerült-e vagy törölve lett, szabadítson fel más erőforrásokat.

Most nézze meg az alábbi ábrán, és tekintse meg, mi a következő lépés. Tegyük fel, hogy a fürt erőforrás-kezelő határozza meg, hogy módosításokra szükség. Az koordinálja más rendszer szolgáltatásokkal (különösen a Feladatátvevőfürt-kezelő), a szükséges módosításokat. Majd a szükséges parancsokat kell küldeni a megfelelő csomópontok (4). Például tegyük fel, az erőforrás-kezelő első fellépése Csomópont5 volt terhelve, és ezért úgy döntött, hogy a szolgáltatás B áthelyezése Csomópont5 csomópont4. Az újrakonfigurálás (5) végén a fürt néz ki:

<center>
![Erőforrás terheléselosztó architektúrája][Image2]
</center>

## <a name="next-steps"></a>Következő lépések
- A fürt erőforrás-kezelő rendelkezik a fürt leíró számos lehetőséget. További információkért róluk, tekintse meg a cikk a [leíró a Service Fabric-fürt](./service-fabric-cluster-resource-manager-cluster-description.md)
- A fürt Resource Manager elsődleges feladatokat van a fürt újraelosztás, és a elhelyezési szabályokat. Ezek közül a viselkedésmódok konfigurálásával kapcsolatos további információkért lásd: [terheléselosztási a Service Fabric-fürt](./service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png
