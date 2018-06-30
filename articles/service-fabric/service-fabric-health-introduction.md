---
title: A Service Fabric állapotfigyelésének |} Microsoft Docs
description: Bevezetés az Azure Service Fabric állapotfigyelési modell, amely a fürt és az alkalmazások és szolgáltatások felügyelete.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: ''
ms.assetid: 1d979210-b1eb-4022-be24-799fd9d8e003
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: fc0bb56e85c2a9cf7a458b0f6d97887d392ee65f
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114316"
---
# <a name="introduction-to-service-fabric-health-monitoring"></a>A Service Fabric állapotmonitorozásának bemutatása
Az Azure Service Fabric egy állapotmodell sokoldalú, rugalmasan és bővíthető állapotának kiértékelését és a jelentéskészítés biztosító vezet be. A modell lehetővé teszi, hogy a fürt és a benne a szolgáltatás állapotának közel valós idejű figyelését. Egyszerűen állapottal kapcsolatos adatok beszerzéséhez, és kijavíthatja az esetleges problémák ahhoz, hogy kaszkádolt és okozhat nagy kimaradások esetén. A tipikus modell szolgáltatások küldjön jelentést a helyi nézetek alapján, és, hogy arra, hogy általános információkat összesíti fürt szintű nézet.

A Service Fabric összetevői a gazdag állapotmodell segítségével jelentést az aktuális állapot. Ugyanazt a mechanizmust segítségével jelentés állapotát, az alkalmazások. Fektet kiváló minőségű állapotfigyelő reporting, amely rögzíti az egyéni feltételek, ha észleli, és a futó alkalmazás sokkal könnyebben kapcsolatos problémák megoldása.

A következő Microsoft Virtual Academy videó is ismerteti a Service Fabric állapotmodellt és használatának módját: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tevZw56yC_1906218965">
<img src="./media/service-fabric-health-introduction/HealthIntroVid.png" WIDTH="360" HEIGHT="244">
</a></center>

> [!NOTE]
> Azt elindítva, az egészségügyi alrendszer a figyelt frissítésekre valamilyen igény kielégítéséhez. A Service Fabric biztosít a figyelt alkalmazás és a fürt megfelelő frissítések rendelkezésre állásának teljes, állásidő nélkül és minimális felhasználói beavatkozás nélküli számára. Ezen célok eléréséhez a frissítés frissítési konfigurált házirendek alapján állapotát ellenőrzi. Egy frissítést folytatni lehessen, csak akkor, ha a rendszerállapot tiszteletben tartja a kívánt küszöbértékeket. Ellenkező esetben a frissítés automatikusan visszaállítása vagy szünetel segítségével a rendszergazdák egy alkalommal a problémák megoldásával kapcsolatban. Alkalmazásfrissítések kapcsolatos további információkért lásd: [Ez a cikk](service-fabric-application-upgrade.md).
> 
> 

## <a name="health-store"></a>A Health Store adatbázisban
A health Store adatbázisban entitások állapotfigyelő kapcsolatos adatokat a fürt könnyen szabályzatokat beolvasó és kiértékelő tartja. Mivel a Service Fabric megőrzött állapot-nyilvántartó szolgáltatás magas rendelkezésre állás és méretezhetőség megvalósítása. A health Store adatbázisban része a **fabric: / System** alkalmazás, és elérhető, amikor a fürt működik-e, és fut.

## <a name="health-entities-and-hierarchy"></a>Állapotfigyelő entitásokat és a hierarchia
A health entitások rögzíti a kapcsolati és a függőségek között különféle entitásokat logikai hierarchiában lévő vannak rendezve. A health Store adatbázisban a health entitásokat és a hierarchiában a Service Fabric összetevői érkezett jelentés alapján automatikusan létrehozza.

Az állapotfigyelő entitásokat a Service Fabric entitások tükrözik. (Például **állapotfigyelő alkalmazás entitás** megegyezik egy alkalmazáspéldány telepítése a fürt, miközben **állapotfigyelő csomópont entitás** megegyezik egy Service Fabric-fürt csomópontja.) A rendszerállapot-hierarchia rögzíti a kapcsolati, a rendszer entitások, és speciális állapotának kiértékelését alapját. Megismerheti a Service Fabric alapfogalmak [Service Fabric a műszaki áttekintés](service-fabric-technical-overview.md). Az alkalmazás kapcsolatban bővebben lásd: [Service Fabric-alkalmazás modell](service-fabric-application-model.md).

Az állapotfigyelő entitásokat és a hierarchia lehetővé teszi a fürt és az alkalmazások hatékony jelentett, indítja, és figyeli. Az állapotmodell biztosít egy pontos *részletes* a fürt számos áthelyezése adatot állapotának megjelenítése.

![Állapotfigyelő entitásokat.][1]
Az állapotfigyelő entitásokat, a hierarchia szülő-gyermek kapcsolatba alapján vannak rendezve.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

Az állapotfigyelő entitásokat a következők:

* **Fürt**. A Service Fabric-fürt állapotát jelképezi. Fürt állapotjelentések ismertetik a feltételeket, amelyek hatással vannak a teljes fürtöt. Ezek a feltételek befolyásolja a fürt vagy egyrészt a fürt több entitás. A feltétel alapján, a jelentéskészítő nem szűkíthető le egy vagy több nem kifogástalan gyermeke a problémát. Például a fürt hálózati particionálás vagy kommunikációs problémák miatt a felosztás agy.
* **Csomópont**. A Service Fabric-csomópont állapotát jelképezi. Csomópont állapotjelentések feltételeket, amelyek hatással vannak a csomópont funkcióit ismerteti. Azok a általában hatással az összes telepített entitás fut rajta. Példák a csomópont nincs elég szabad lemezterület (vagy egyéb gépre kiterjedő tulajdonságai, például a memória, a kapcsolatok), és ha egy csomópont le. A csomópont entitás azonosítja a csomópont neve (karakterlánc).
* **Alkalmazás**. A fürtben futó alkalmazás példány állapotát jelképezi. Alkalmazás állapotjelentések ismertetik a feltételeket, amelyek hatással vannak az alkalmazás általános állapotát. Ezeket nem kell leszűkítheti egyedi gyermekekre (szolgáltatások vagy telepített alkalmazások). Például a végpontok közötti interakció között különböző szolgáltatásokat az alkalmazásban. Az alkalmazás entitás azonosítja az alkalmazás neve (URI).
* **Szolgáltatás**. A fürtben futó szolgáltatás állapotát jelképezi. Rendszerállapot-jelentések általános állapotát, a szolgáltatást érintő feltételek ismertetik. A jelentéskészítő nem szűkíthető a problémát egy sérült partíció vagy a replikát. A szolgáltatáskonfiguráció (például a port vagy a külső fájlmegosztás) minden olyan partíciónak kapcsolatos problémát okozó például. A szolgáltatás entitás azonosítja a szolgáltatás nevét (URI).
* **Partíció**. A szolgáltatás partíció állapotát jelképezi. Partíció állapotjelentések száma, amelyek hatással vannak a teljes replikakészlethez feltételek ismertetik. Például ha a replikák száma kevesebb cél, és a partíció kvórumveszteségben van. A partíció entitás azonosítja a partíció Azonosítót (GUID).
* **A replika**. Egy állapotalapú szolgáltatási replika- vagy egy állapotmentes szolgáltatások állapotát jelképezi. A nem a legkisebb egység watchdogs és rendszerösszetevők jeleníthet meg az alkalmazás a replikát. Állapotalapú szolgáltatások például egy elsődleges másodpéldány, amelyek másodlagos adatbázis és a lassú replikációs műveletek nem replikálódnak. Állapot nélküli példány is, ha nincs elegendő szabad erőforrások vagy kapcsolódási problémák léptek fel is tud jelentéseket. A replika entitás azonosítja a partíció Azonosítót (GUID) és a replika vagy a példány azonosítója (hosszú).
* **DeployedApplication**. Állapotát jelképezi egy *csomóponton futó alkalmazás*. Telepített alkalmazás állapotjelentések feltételek az alkalmazáshoz a csomóponton, amelyek ugyanazon a csomóponton telepített service-csomagok nem lehet leszűkítheti írja le. Ilyen például a hibák, amikor alkalmazáscsomag nem lehet letölteni a csomóponton és alkalmazás rendszerbiztonsági tagok a csomóponton beállításával kapcsolatos problémák. A telepített alkalmazás azonosítja az alkalmazásnév (URI) és a csomópont neve (karakterlánc).
* **DeployedServicePackage**. A fürt egy csomópontján fut a szolgáltatáscsomagot állapotát jelképezi. Azt ismerteti, hogy a feltételek adott szolgáltatás csomaghoz nincsenek hatással a többi szolgáltatás csomagot ugyanahhoz az alkalmazáshoz ugyanazon a csomóponton. A kódcsomag például a service-csomagot, amely nem indítható el, és a konfigurációs csomag nem olvasható. A telepített szervizcsomag azonosíthatók alkalmazásnév (URI), a csomópont neve (karakterlánc), a jegyzékfájl neve (karakterlánc) és a szolgáltatás csomag aktiválási azonosító (karakterlánc).

A lépésköz legyen az állapotmodell megkönnyíti, hogy ismeri fel és javítsa ki a problémákat. Például a szolgáltatás nem válaszol, esetén megvalósítható jelentheti, hogy az alkalmazáspéldány nem kifogástalan. Jelentéskészítés, hogy szintje nem ideális, azonban mivel a probléma lehet, hogy nem kell érintő, hogy az alkalmazásban levő összes szolgáltatását. A jelentés alkalmazni kell a nem megfelelő állapotú szolgáltatás, vagy egy adott gyermekpartíciót, ha további információt adott partíció mutat. Az adatok automatikusan felületeken keresztül a hierarchiában, és nem kifogástalan partíción láthatóvá válnak szolgáltatások és alkalmazások szinten. Ez az összesítés segít a rögzítési ponthoz, és gyorsabban hárítsa el a problémát az okozza.

A rendszerállapot-hierarchia szülő-gyermek kapcsolatba áll. Egy fürt csomópontja és az alkalmazások tevődik össze. Alkalmazások vannak olyan szolgáltatások és alkalmazások telepítését. A telepített alkalmazások telepített szervizcsomagok. Szolgáltatások partíciókkal rendelkezik, és mindegyik partíció rendelkezik egy vagy több replikákat. Különleges csomópontok és a telepített entitások közötti kapcsolat áll fenn. Egy nem megfelelő állapotú csomóponti alapján a szolgáltató rendszer összetevője, a Feladatátvevőfürt-kezelő szolgáltatás hatással van a központilag telepített alkalmazások, szervizcsomagok és replikák telepítve rajta.

A rendszerállapot-hierarchia a rendszert a legújabb állapotjelentések, vagyis csaknem valós idejű információkat a legújabb állapotát jeleníti meg.
Belső és külső watchdogs jelenthetik-e az alkalmazás-specifikus logika vagy egyéni figyelt feltételek alapján azonos entitásokat. Felhasználói jelentések megtalálhatók a rendszer jelentéseket.

Tervezze meg a jelentést, és egy nagy felhőszolgáltatás tervezése során állapotfigyelő válaszolni fektetnek. A kezdeti befektetési hibakeresési, felügyelete és működtetéséhez megkönnyíti a szolgáltatás.

## <a name="health-states"></a>Állapotok
A Service Fabric három állapotokat használatával írják le, hogy egy entitás kifogástalan-e vagy sem: OK, figyelmeztetés és hiba. A health Store adatbázisban küldött jelentései ezeket az állapotokat egyikét kell adnia. Az állapotfigyelő kiértékelésének eredménye az egyik ezeket az állapotokat.

A lehetséges [állapotokat](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate) vannak:

* **OK**. Entitás állapota kifogástalan. Nincsenek küldött, vagy saját gyermekéhez. (ha alkalmazható) ismert problémák.
* **Figyelmeztetés**. Az entitásnak van néhány problémát, de továbbra is működhet megfelelően. Például késések fordulnak elő, de azok nem indítják el működési probléma merül fel még. Bizonyos esetekben a figyelmeztetési feltételének megoldhatja maga külső beavatkozás nélkül. Ezekben az esetekben állapotjelentések felhívják a figyelmet, és adja meg, mi láthatósága van folyamatban. Más esetekben a figyelmeztetési feltételének ronthatja egy súlyos hiba, felhasználói beavatkozás nélkül.
* **Error**. Az entitás nem kifogástalan. A művelet kell végezni az entitás, az állapot javításához, mert nem tud megfelelően működni.
* **Ismeretlen**. Az entitás a health Store adatbázisban nem létezik. Ennek az elosztott lekérdezések, amelyek több összetevőből származó eredmények egyesítése lehet lekérni. Például a get-csomópont listalekérdezés ugrik **FailoverManager**, **ClusterManager**, és **HealthManager**; alkalmazás listalekérdezés ugrik  **ClusterManager** és **HealthManager**. A lekérdezések eredményeit a rendszer több összetevő egyesíteni. Ha egy másik rendszerösszetevő adja vissza, amely nem található a health Store adatbázisban entitás, az egyesített eredmény tartalmaz-e az ismeretlen állapot. Egy entitás oka nem a tárolóban állapotjelentések még nem dolgozott, vagy az entitás törölve lett törlése után.

## <a name="health-policies"></a>Házirendek
A health Store adatbázisban állapotházirendeket meghatározásához entitás kifogástalan kapcsolódó jelentések és a gyermekek alapján alkalmazza.

> [!NOTE]
> A fürtjegyzékben (a fürt és a csomópont állapotának kiértékelését) vagy az alkalmazásjegyzékben (az alkalmazás kiértékelése és bármely gyermeke) állapotházirendeket adható meg. Rendszerállapot-értékelés kérelmek az egyéni értékelési állapotházirendeket, amelyek csak a értékelés is átadhatja.
> 
> 

Alapértelmezés szerint a Service Fabric a szülő-gyermek hierarchikus kapcsolat (mindent lehet kifogástalan) szigorú szabályok vonatkoznak. Ha még a gyermekek egyike sérült állapotot jelző esemény egy, a szülő nem megfelelő állapotúnak számít.

### <a name="cluster-health-policy"></a>Fürt állapotházirend
A [állapotházirend fürt](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy) a fürt állapota, így a csomópont állapotának kiértékelésére szolgál. A házirend a fürtjegyzékben definiálhatók. Ha nincs jelen, az alapértelmezett házirend (nulla megengedett hibák) használja.
A fürt állapotházirend tartalmazza:

* [A ConsiderWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Meghatározza, hogy kezelni a figyelmeztetési állapot jelentenek hibaként állapot kiértékelésekor. Alapértelmezett: false.
* [MaxPercentUnhealthyApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications). A maximális megengedett százalékos értékét határozza meg, hogy a nem megfelelő lehet, mielőtt a fürt tekinthető hiba történt az alkalmazások.
* [MaxPercentUnhealthyNodes](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes). A maximális megengedett százalékos értékét határozza meg, amely a nem megfelelő lehet, mielőtt a fürt tekinthető hiba a csomópontok. Nagy fürtökben egyes csomópontok: mindig le vagy ki a javításához, így ezen százalékos arány kell konfigurálni, amely elviselni.
* [Applicationtypehealthpolicymap paraméter hiányzó értékei](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap). Az alkalmazás típusa állapotfigyelő házirend-hozzárendelés segítségével fürt állapot kiértékelésekor különleges alkalmazástípusokat ismerteti. Alapértelmezés szerint minden alkalmazás kerüljenek a készlet és a MaxPercentUnhealthyApplications értékeli ki. Néhány alkalmazástípus eltérően kell kezelni, ha ezek a globális készlet kívül lehessen állítani. Ehelyett az alkalmazás neve a térkép társított százalékos elleni kiértékelésük. Például a fürt számos különböző típusú alkalmazások ezer, és néhány speciális alkalmazás típusú vezérlő az alkalmazáspéldányok. A vezérlő alkalmazások soha nem lehet a hibás. Néhány hiba tűrését 20 %, de az alkalmazás típusa "ControlApplicationType" a MaxPercentUnhealthyApplications 0 globális MaxPercentUnhealthyApplications adhatja meg. Így ha az alkalmazások egy részének a sok sérült állapotban, de a globális sérült százalékos arány alatt, a fürt becsülhető figyelmeztetési. Figyelmeztetési állapot nem befolyásolja a fürt frissítése, és más figyelési aktivált hiba állapota. De még egy vezérlőt alkalmazás hibás tenné a fürt nem kifogástalan, amely visszaállítása elindítja vagy leállítja a fürtök frissítése, a frissítési konfigurációjától függően.
  A térkép definiált alkalmazás típusok minden alkalmazáspéldányok kiveszik a globális készlet alkalmazások. Kiértékelésük alkalmazás típusú, az adott MaxPercentUnhealthyApplications leképezés használatával kérelmek teljes száma alapján. Az alkalmazások az összes többi a globális készlet maradnak, és a kiértékelésük a MaxPercentUnhealthyApplications.

A következő példa egy fürtjegyzékben fájlból. Az alkalmazás-típusú hozzárendelés bejegyzések definiálásához előtag a paraméter neve "ApplicationTypeMaxPercentUnhealthyApplications-", az alkalmazás neve követ.

```xml
<FabricSettings>
  <Section Name="HealthManager/ClusterHealthPolicy">
    <Parameter Name="ConsiderWarningAsError" Value="False" />
    <Parameter Name="MaxPercentUnhealthyApplications" Value="20" />
    <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
    <Parameter Name="ApplicationTypeMaxPercentUnhealthyApplications-ControlApplicationType" Value="0" />
  </Section>
</FabricSettings>
```

### <a name="application-health-policy"></a>Az alkalmazás állapotházirendje
A [az alkalmazás állapotházirendje](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy) ismerteti, hogyan az alkalmazások és azok alárendelt események és a gyermek-állapotok összesítési kiértékelése történik. Az alkalmazásjegyzékben definiálható **ApplicationManifest.xml**, alkalmazáscsomagban. Ha nincsenek házirendek meg van adva, a Service Fabric feltételezi, hogy az entitás sérült állapotba, ha egy jelentés vagy egy alárendelt rendelkezik, a figyelmeztetés vagy hibaüzenet állapotát.
A konfigurálható házirendek a következők:

* [A ConsiderWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Meghatározza, hogy kezelni a figyelmeztetési állapot jelentenek hibaként állapot kiértékelésekor. Alapértelmezett: false.
* [MaxPercentUnhealthyDeployedApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications). A maximális megengedett százalékos értékét határozza meg, hogy a nem megfelelő lehet, mielőtt az alkalmazás tekinthető hiba a központilag telepített alkalmazások. Ez a százalékérték keresztül az alkalmazások jelenleg telepítve vannak a a fürtben lévő csomópontok száma nem megfelelő a telepített alkalmazások számát elosztjuk. A számítási csomópontok kis számú hiba tűrését kerekít. Alapértelmezett százalékos aránya: nulla.
* [DefaultServiceTypeHealthPolicy](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy). Adja meg az alapértelmezett service type állapotházirend minden szolgáltatás esetében az alkalmazás alapértelmezett állapotházirend váltja fel.
* [Servicetypehealthpolicymap paraméterek hiányzó értékei](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap). Szolgáltatás állapotházirendeket szolgáltatás típusonkénti térképére biztosít. Ezek a házirendek cserélje le az alapértelmezett szolgáltatás típus állapotházirendeket, ha az egyes megadott szolgáltatás. Például ha egy alkalmazás egy állapot nélküli átjáró szolgáltatás típusa, egy állapot-nyilvántartó motor szolgáltatás, beállíthatja a health azok tesztelési másképp. Szolgáltatástípus száma szabályzat megadása esetén is révén a részletesebben vezérelhető, a szolgáltatás állapotáról.

### <a name="service-type-health-policy"></a>Service type állapotházirend
A [service type állapotházirend](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy) értékelje ki, és a szolgáltatások és szolgáltatások gyermekei összesítése módját adja meg. A házirend tartalmazza:

* [MaxPercentUnhealthyPartitionsPerService](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice). A maximális megengedett százalékos értékét határozza meg a nem megfelelő partíciók előtt a szolgáltatás nem megfelelő állapotúnak számít. Alapértelmezett százalékos aránya: nulla.
* [MaxPercentUnhealthyReplicasPerPartition](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition). A maximális megengedett százalékos értékét határozza meg a nem megfelelő replikák előtt a partíció nem megfelelő állapotúnak számít. Alapértelmezett százalékos aránya: nulla.
* [MaxPercentUnhealthyServices](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices). A maximális megengedett százalékos értékét határozza meg a nem megfelelő szolgáltatások előtt az alkalmazás nem megfelelő állapotúnak számít. Alapértelmezett százalékos aránya: nulla.

A következő példa egy olyan alkalmazás-jegyzékfájlból:

```xml
    <Policies>
        <HealthPolicy ConsiderWarningAsError="true" MaxPercentUnhealthyDeployedApplications="20">
            <DefaultServiceTypeHealthPolicy
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="10"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="FrontEndServiceType"
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="20"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="BackEndServiceType"
                   MaxPercentUnhealthyServices="20"
                   MaxPercentUnhealthyPartitionsPerService="0"
                   MaxPercentUnhealthyReplicasPerPartition="0">
            </ServiceTypeHealthPolicy>
        </HealthPolicy>
    </Policies>
```

## <a name="health-evaluation"></a>Állapot kiértékelésekor
Felhasználók és az automatikus szolgáltatások meg tudja határozni, egyetlen entitás állapotát bármikor. Kiértékelése egy entitás állapotát, a health store összesítések összes rendszerállapot jelentés az entitáson, és minden gyermekét (ha alkalmazható) kiértékeli. Az állapotfigyelő összesítési algoritmus által használt állapotjelentések kiértékelheti és az összesített gyermek állapotokat (ha alkalmazható) meghatározó házirendek.

### <a name="health-report-aggregation"></a>Állapotfigyelő jelentés aggregáció
Egy entitás különböző jelentéskészítők (rendszerösszetevők vagy watchdogs) a tulajdonságai eltérők által küldött több állapotjelentések rendelkezhet. Az összesítés a hozzá tartozó, állapotra házirendeket használ, különösen az alkalmazás vagy a fürt állapotházirend ConsiderWarningAsError tagja. A ConsiderWarningAsError figyelmeztetések kiértékelése módját adja meg.

Összesített állapotát váltja ki, a *legrosszabb* rendszerállapot-jelentéseket az entitás. Ha legalább egy hiba állapotjelentése, összesített állapotát hiba.

![Állapotfigyelő jelentés összesítési az esetleges hibajelentésben való megjelenítéshez.][2]

A health entitás, amely rendelkezik egy vagy több hiba állapotjelentések hiba történik. Ugyanez érvényes a lejárt állapotjelentése, függetlenül annak állapotát.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Ha nem a hibajelentések és egy vagy több figyelmeztetés, összesített állapotát figyelmeztetés vagy a hiba, attól függően, hogy a ConsiderWarningAsError házirend jelző.

![Állapotfigyelő jelentés összesítési figyelmeztetés jelentés és a ConsiderWarningAsError hamis.][3]

Állapotfigyelő jelentés összesítési figyelmeztetés jelentés és a ConsiderWarningAsError értéke hamis (alapértelmezett).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>Gyermek állapotának összesítése
Entitás összesített állapotát jeleníti meg a gyermek állapotokat (ha alkalmazható). A gyermek állapotának összesítése az algoritmus a állapotfigyelő házirendeket használ alkalmazható entitás típusa alapján.

![Gyermek entitások állapotának összesítése.][4]

Gyermek-összesítési házirendek alapján.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Miután a health Store adatbázisban szereplő összes gyermek kiértékelése, összesíti az azok állapotokat a sérült gyermek maximális beállított százalékos arány alapján. Ezen százalékos arány a szabályzatot az entitáshoz és alárendelt alapján lesz végrehajtva.

* Ha minden gyermeknek OK állapotok, gyermek összesített állapota rendben.
* Ha gyermeke OK és a figyelmeztetési állapotok, a gyermek összesített állapota figyelmet igényel.
* Ha a hiba állapotnak, amely nem veszik figyelembe a megengedett maximális százalékos sérült gyermekek gyermek, összesített állapota nem megfelelő.
* Ha a hiba államok gyermekek tiszteletben sérült gyermekek engedélyezett maximális százalékát, a összesített állapota figyelmet igényel.

## <a name="health-reporting"></a>Rendszerállapot-jelentés
A Service Fabric entitások rendszer összetevőit, a rendszer Fabric-alkalmazások és a belső/külső watchdogs jelenthetnek. Ellenőrizze a jelentéskészítők *helyi* meghatározás mutatja be a figyelt entitásokat, várják a feltételek alapján. Nem kell nézze meg a globális állapota vagy összesített adatokat. A kívánt viselkedés, hogy egyszerű jelentéskészítők, és nem összetett szervezetek, amelyek közül sok következtethető ki, milyen információkat küldeni a következőkre van szükség.

Rendszerállapot-adatok küldését a health Store adatbázisban, egy jelentéskészítő kell azonosítja az érintett entitást, és hozzon létre egy jelentés. A jelentés küldése, használja a [FabricClient.HealthClient.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) API, API-k kikerültek a jelentés állapotának a `Partition` vagy `CodePackageActivationContext` objektumok, a PowerShell-parancsmagokkal vagy a REST.

### <a name="health-reports"></a>Állapotjelentések száma
A [állapotjelentések](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthreport) entitások a fürt minden egyes a következő információkat tartalmazza:

* **SourceId**. Egy karakterlánc, amely egyedileg azonosítja az egészségügyi esemény jelentéskészítői.
* **Entitás azonosítója**. Azonosítja az entitást, amikor a jelentésben van érvényben. Ez eltér a [entitástípus](service-fabric-health-introduction.md#health-entities-and-hierarchy):
  
  * A fürt. Nincs.
  * Csomópont. Csomópont neve (karakterlánc).
  * Az alkalmazás. Alkalmazás neve (URI). Az alkalmazáspéldány telepítése a fürt nevét jelöli.
  * A szolgáltatás. Szolgáltatás neve (URI). A fürtben telepített service-példány nevét jelöli.
  * A partíció. Partíció azonosítója (GUID). A partíció egyedi azonosítóját jelöli.
  * A replika. Az állapotalapú szolgáltatási másodpéldány-azonosító, vagy az állapot nélküli szolgáltatáspéldány-Azonosítóval (INT64).
  * DeployedApplication. Az alkalmazásnév (URI) és a csomópont neve (karakterlánc).
  * DeployedServicePackage. Alkalmazás neve (URI), a csomópont neve (karakterlánc) és a service manifest neve (karakterlánc).
* **Tulajdonság**. A *karakterlánc* (nem fix enumerálási), amely lehetővé teszi, hogy a jelentési a állapotesemény az entitás egy adott tulajdonságra vonatkozó kategorizálását. Például A jelentési jelenthetik-e a Node01 állapotának "Tároló" tulajdonság, és a B jelentéskészítői jelenthetik-e a Node01 állapotának "Kapcsolat" tulajdonság. A health Store adatbázisban ezek a jelentések külön állapotával kapcsolatos események Node01 entitás tekintendők.
* **Leírás**. Egy karakterlánc, amely lehetővé teszi, hogy a jelentési a állapotesemény kapcsolatos részletes információk. **SourceId**, **tulajdonság**, és **HealthState** teljesen le kell írnia a jelentést. A leírás ad emberek számára olvasható jelentés adatai. A szöveg egyszerűbbé teszi a rendszergazdák és felhasználók számára az állapotjelentést ismertetése.
* **HealthState**. Egy [számbavételi](service-fabric-health-introduction.md#health-states) , amely leírja, hogy a jelentés állapotának. Az elfogadott értékei OK, figyelmeztetés és hiba.
* **A TimeToLive tulajdonság**. Timespan érték, amely azt jelzi, hogy mennyi ideig az állapotjelentést érvényes. Alapján kialakulhat **RemoveWhenExpired**, ez lehetővé teszi, hogy a health Store adatbázisban ismernie a lejárt események kiértékelni. Alapértelmezés szerint az érték a végtelen, és a jelentés érvényes tartja.
* **RemoveWhenExpired**. Olyan logikai érték. Ha true értéke esetén a lejárt állapotjelentése automatikusan eltávolítja a health Store adatbázisban, és a jelentés nem befolyásolja a entitás állapotának kiértékelését. A jelentés egy megadott időszak alatt csak érvényes, és a jelentéskészítő nem kell explicit módon ürítse használt. Jelentések törlése a health Store adatbázisban is használható (például egy figyelő módosul, és leállítja az előző és tulajdonság jelentéseinek). Egy jelentés ezzel a rövid TimeToLive együtt RemoveWhenExpired törli az összes előző állapotát, a health store-ból is küldheti. Ha a beállítás értéke FALSE, az állapot kiértékelésekor a hiba a lejárt jelentés kell kezelni. A hamis értéket jelzi a health Store adatbázisban számára, hogy a forrás jelentést kell rendszeresen ezt a tulajdonságot. Ha nem, majd kell van a figyelő kódjával. A figyelő állapotát annak eldöntéséhez, hogy az eseményt, amely során rögzített.
* **SequenceNumber**. Pozitív egésznek kell lennie a egyre növekvő, a jelentések sorrendet jelöli. Elavult jelentések hálózati késések vagy más olyan problémák miatt késői kapott észlelésére szolgál által a health Store adatbázisban. Kisebb vagy egyenlő, mint a legtöbb legutóbb alkalmazott számát a ugyanaz az entitás, a forrás és a tulajdonság a sorszám esetén jelentést elutasítva. Ha nincs megadva, a sorszám automatikusan létrejön. Fontos a sorszám helyezése csak állapotváltozáskor jelentésekor. Ebben az esetben a forrás megjegyezhető akkor küldi el a jelentéseket, és biztosíthatja az adatok helyreállítás feladatátvétel van szüksége.

Négy darabot információk – SourceId, entitás azonosítója, Property és HealthState--szükségesek a minden jelentés. A SourceId karakterlánc nem engedélyezett a előtaggal kezdődik "**System.**", amely rendszer jelentések számára van fenntartva. Ugyanaz az entitás nincs az azonos forrásból és a tulajdonság csak egy jelentés. A forrás és a tulajdonsághoz több jelentések fedjék át egymást, egészségügyi ügyféloldali (ha ezek vannak kötegelni) vagy az egészségügyi ügyféloldali tárolja. A csere alapul sorszámok; újabb jelentéskészítés (a magasabb sorszámok) cserélje le a régebbi jelentéseket.

### <a name="health-events"></a>Állapotával kapcsolatos események
Belső, a health Store adatbázisban tartja [állapotával kapcsolatos események](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthevent), a jelentések és a metaadatokat az összes adatot tartalmazó. A metaadatok közé tartozik a lett megadva a jelentés a rendszerállapot-ügyfél és az idő a kiszolgáló oldalán módosítva lett. A állapotával kapcsolatos események által visszaadott [állapotlekérdezések](service-fabric-view-entities-aggregated-health.md#health-queries).

A hozzáadott metaadatokat tartalmazza:

* **SourceUtcTimestamp**. Az idő a jelentés a rendszerállapot-ügyfél (egyezményes világidő) lett megadva.
* **LastModifiedUtcTimestamp**. A jelentés a kiszolgáló oldalán (egyezményes világidő) legutóbbi módosításának időpontja.
* **IsExpired**. A jelző annak jelzésére, hogy a jelentés által a health Store adatbázisban végrehajtáskor a rendszer a lekérdezés lejárt. Egy esemény is lehet lejárt, csak akkor, ha RemoveWhenExpired értéke "false". Ellenkező esetben az esemény lekérdezés nem ad vissza, és eltávolítják az áruházból.
* **LastOkTransitionAt**, **LastWarningTransitionAt**, **LastErrorTransitionAt**. Az OK vagy figyelmeztetés/hiba átmenetek legutóbbi időpontját. Ezeket a mezőket ad állapotának előzménye Állapotváltások eseménynél.

Az állapot átmenet mezők intelligensebb riasztásokat vagy a "korábbi" állapottal kapcsolatos adatok esemény használható. Forgatókönyvek például ezek engedélyezése:

* Riasztás, ha egy tulajdonság már figyelmeztetés/hiba a több, mint X perc. Egy adott időn belül feltételét ellenőrzése Ezzel elkerülheti a riasztásokat az ideiglenes feltételek alapján. Például egy riasztást, ha az állapot rendelkezik lett figyelmeztetés öt percnél hosszabb lefordítható (HealthState figyelmeztetés, és most - LastWarningTransitionTime == > 5 perc).
* Csak a feltételek, amelyek megváltoztak a legutóbbi riasztás X perc. A jelentés a megadott időpont előtt már, hiba, ha azt figyelmen kívül hagyhatja, mert már volt korábban jelzést.
* Egy tulajdonság váltása van figyelmeztetés és hiba, ha határozza meg, mennyi ideig lett sérült (Ez azt jelenti, hogy nem OK). Például egy riasztást, ha a tulajdonság nem kifogástalan öt percnél hosszabb ideig lefordítható (HealthState! = az OK gombra, és most - LastOkTransitionTime > 5 perc).

## <a name="example-report-and-evaluate-application-health"></a>Példa: Jelentés és a alkalmazás állapotának kiértékelése
A következő példa a PowerShell segítségével az alkalmazás állapotát jelentést küld a **fabric: / WordCount** a forrás **MyWatchdog**. A jelentés a health tulajdonság hibaállapot, végtelen TimeToLive az "availability" információkat tartalmaz. Ezután lekérdezi az alkalmazás állapotának, mely értéket ad vissza összesített állapotát állapot hibák és a jelentett állapotfigyelő események állapotával kapcsolatos események közül.

```powershell
PS C:\> Send-ServiceFabricApplicationHealthReport –ApplicationName fabric:/WordCount –SourceId "MyWatchdog" –HealthProperty "Availability" –HealthState Error

PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Error event: SourceId='MyWatchdog', Property='Availability'.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Error
                                  SequenceNumber        : 131032204762818013
                                  SentAt                : 3/23/2016 3:27:56 PM
                                  ReceivedAt            : 3/23/2016 3:27:56 PM
                                  TTL                   : Infinite
                                  Description           :
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Ok->Error = 3/23/2016 3:27:56 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="health-model-usage"></a>Állapotfigyelő model felhasználás
Az állapotmodell lehetővé teszi, hogy felhőszolgáltatások és a mögöttes Service Fabric-platformról méretezési, mert a figyelés és a rendszerállapot-meghatározás meg vannak osztva a fürtön belül a különböző figyelők.
Egyéb rendszerek rendelkezik egyetlen központi szolgáltatás összes elemzi a fürt szintjén a *potenciálisan* szolgáltatások által kibocsátott hasznos információkat. Ez a megközelítés hátráltatja a méretezhetőséget. Még nem teszi lehetővé a őket segít azonosítani a problémákat és a potenciális problémákról, az alapvető ok lehető megközelíti a meghatározott kapcsolatos információk összegyűjtéséhez.

Az állapotmodell fokozottan szolgál a figyelése és diagnosztizálása, a fürt és az alkalmazás állapotának kiértékelésekor és a figyelt frissítésekre. Egyéb szolgáltatások egészségügyi adatok segítségével automatikus javításához, a fürt állapotelőzményeinek elkészítéséhez és a riasztások bizonyos feltételek esetén a probléma.

## <a name="next-steps"></a>További lépések
[A Service Fabric rendszerállapot-jelentések megtekintése](service-fabric-view-entities-aggregated-health.md)

[Rendszerállapot-jelentések használata a hibaelhárításhoz](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Jelentés és a szolgáltatás állapotának ellenőrzése](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Adja hozzá az egyéni Service Fabric állapotjelentések száma](service-fabric-report-health.md)

[Figyelése és diagnosztizálása helyileg szolgáltatások](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[A Service Fabric-alkalmazás frissítése](service-fabric-application-upgrade.md)

