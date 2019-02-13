---
title: Állapotmonitorozás a Service Fabricben |} A Microsoft Docs
description: Bevezetés az Azure Service Fabric-állapotfigyelési modell, amely a fürt és az alkalmazások és szolgáltatások figyelését teszi lehetővé.
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
ms.openlocfilehash: 87081398e844f1e2b085a7e12c2b7aafce330ec9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56193764"
---
# <a name="introduction-to-service-fabric-health-monitoring"></a>A Service Fabric állapotmonitorozásának bemutatása
Az Azure Service Fabric egy állapotmodell által biztosított a gazdag, rugalmas és bővíthető állapotának kiértékelését és a jelentéskészítés mutatja be. A modell lehetővé teszi, közel valós idejű figyelését a fürt és a benne lévő futó szolgáltatások állapotát. Egyszerűen egészségügyi információk beszerzéséhez, és kijavíthatja az esetleges problémák előtt alkalmazza, és a nagy leállások miatt. A tipikus modellben szolgáltatások küldeni a jelentéseket a saját helyi található nézetek alapján, és, hogy információt összesített értéket jelenít meg, adja meg a teljes fürt szintű megtekintése.

A Service Fabric összetevői a gazdag állapotmodell használja a jelenlegi állapotuk jelentéséhez. Használhatja ugyanazt a mechanizmust jelentés állapotába le az alkalmazásokból. Fektet a magas színvonalú állapotfigyelő jelentési, amely rögzíti az egyéni feltételek, ha észleli, és sokkal egyszerűbben futó alkalmazás megoldásának.

> [!NOTE]
> Az egészségügyi alrendszer megoldására figyelt frissítéseket kell azt elindult. A Service Fabric biztosít a figyelt alkalmazás és a fürt frissítések, amelyek a teljes rendelkezésre, leállás nélkül és minimális felhasználói beavatkozás nélküli. Ezen célok eléréséhez a frissítés állapota alapján a konfigurált frissítési szabályzatok ellenőrzi. Frissítés folytassa csak akkor, ha a health figyelembe veszi a kívánt küszöbértékeket. Ellenkező esetben a frissítés automatikusan vissza állítva vagy fel van függesztve, a segítségével a rendszergazdák és hárítsa el a problémákat. Alkalmazásfrissítések kapcsolatos további információkért lásd: [Ez a cikk](service-fabric-application-upgrade.md).
> 
> 

## <a name="health-store"></a>Health Store adatbázisban
A health Store adatbázisban tartja entitásokról health szolgáltatással kapcsolatos információkat a fürt könnyen lekérés és értékelés céljából használják. Megvalósított módon egy Service Fabric állapotalapú szolgáltatás magas rendelkezésre állás és méretezhetőség biztosítása érdekében megőrzi. A health Store adatbázisban része a **fabric: / System** alkalmazás, és elérhető, amikor a fürt működik-e, és fut.

## <a name="health-entities-and-hierarchy"></a>Egészségügyi entitások és a hierarchia
Az egészségügyi szervezetek logikai interakciók és a különböző entitások közötti függőségek rögzíti a hierarchiában lévő vannak rendszerezve. A health Store adatbázisban az egészségügyi szervezetek és a hierarchia a Service Fabric összetevői érkezett jelentés alapján automatikusan létrehozza.

Az egészségügyi szervezetek a Service Fabric-entitások tükrözéséhez. (Például **egészségügyi alkalmazás entitás** megegyezik az alkalmazáspéldány üzembe helyezve, a fürt, miközben **egészségügyi csomópont entitás** megegyezik egy Service Fabric-fürt csomópontja.) A health hierarchia rögzíti a rendszer entitások interakciókat, és a speciális állapotának kiértékelését alapját. Megismerheti a Service Fabric alapfogalmak [a Service Fabric technikai áttekintése](service-fabric-technical-overview.md). Az alkalmazás további információkért lásd: [Service Fabric-alkalmazásmodell](service-fabric-application-model.md).

Az egészségügyi entitásokat és a hierarchia lehetővé teszik a fürt és az alkalmazások hatékonyan jelentett, hibakereséséhez és figyelemmel kísérni. Az állapotközpontú modellről biztosít egy pontos *részletes* állapotát, a fürt számos mozgó darab ábrázolása.

![Egészségügyi szervezetek.][1]
Az egészségügyi entitásokat, a hierarchia szülő-gyermek típusú kapcsolatokat alapján vannak rendezve.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

Az egészségügyi szervezetek a következők:

* **Fürt**. Service Fabric-fürt állapotát jelképezi. Fürt rendszerállapot-jelentések a teljes fürtöt érintő feltételek leírása. Ezek a feltételek befolyásolja a fürt vagy a fürt, másrészt több entitás. A feltétel alapján, a jelentéskészítő nem Szűkítse le egy vagy több nem kifogástalan gyermeke a probléma. Ilyenek például az agy a fürt hálózati a particionálás és kommunikációs hibák miatt felosztásának eredménye.
* **Csomópont**. A Service Fabric-csomópont állapotát jelképezi. Csomópont rendszerállapot-jelentések feltételeket, amelyek hatással vannak a csomópont funkcióit írja le. Azok a rajta futó összes üzembe helyezett entitás általában hatással. Ilyenek például a csomópont nincs elég szabad lemezterület (vagy más gépre kiterjedő tulajdonságok, például a memória, a kapcsolatok) és amikor a csomópont nem működik. A csomópont entitást azonosítja a csomópont neve (karakterlánc).
* **Alkalmazás**. A fürtben futó alkalmazáspéldány állapotát jelképezi. Alkalmazás rendszerállapot-jelentések ismertetik a feltételeket, amelyek hatással vannak az alkalmazás általános állapotát. Ezek nem lehet leszűkül, hogy az egyes gyermekek (szolgáltatások vagy telepített alkalmazások). Például a végpontok közötti interakció, többek között a különböző szolgáltatások, az alkalmazás. Az alkalmazás entitást azonosítja az alkalmazás nevét (URI).
* **Szolgáltatás**. A fürtben futó szolgáltatás állapotát jelképezi. Rendszerállapot-jelentések ismertetik a feltételeket, amelyek hatással vannak a szolgáltatás általános állapotát. A jelentéskészítő nem leszűkíteni a probléma egy sérült partíció vagy a replikát. Ilyenek például a szolgáltatás konfigurációs (például port vagy külső fájlmegosztás) tartozó összes partíció problémákat okozó. A szolgáltatás entitást azonosítja a szolgáltatás nevét (URI).
* **Partíció**. Az állapotfigyelő szolgáltatás partíció jelöli. Partíció rendszerállapot-jelentések ismertetik a feltételeket, amelyek hatással vannak a teljes replikakészlethez. Például ha replikák száma nem éri a célként megadott száma, és a egy partíció kvórumveszteségben van. A partíció entitást azonosítja a partíció Azonosítóját (GUID).
* **Replika**. Egy állapotalapú szolgáltatás replika- vagy egy állapotmentes szolgáltatás állapotát jelképezi. A replika nem watchdogs és a rendszer összetevőinek jelentései az alkalmazáshoz, a legkisebb egység. Az állapotalapú szolgáltatások esetében például a műveletek nem tudja replikálni a másodlagos példány hozható létre, és a lassú replikálás elsődleges replika. Állapot nélküli példány is, jelentheti a lemezén kevés a szabad erőforrás vagy kapcsolódási problémák. A replika entitás azonosítja a partíció Azonosítóját (GUID) és a replika és példány Azonosítóját (hosszú).
* **DeployedApplication**. Állapotát jelöli egy *egy csomóponton futó alkalmazás*. Az üzembe helyezett alkalmazás rendszerállapot-jelentések ismertetik a feltételek az alkalmazáshoz nem lehet leszűkül, hogy ugyanazon a csomóponton üzembe helyezett szolgáltatáscsomagok a csomópontra. Ilyenek például a hibákat, ha a virtuálisalkalmazás-csomag nem tölthető le a csomóponton és az alkalmazás rendszerbiztonsági tagok a csomóponton beállításával kapcsolatos problémák. Az üzembe helyezett alkalmazás azonosít alkalmazás neve (URI) és a csomópont neve (karakterlánc).
* **DeployedServicePackage**. A fürt egyik csomópontján futó szolgáltatáscsomagot állapotát jelképezi. Leírja, feltételek adott szolgáltatási csomaghoz nincsenek hatással a többi szolgáltatás csomagot ugyanazon a csomóponton ugyanahhoz az alkalmazáshoz. A kódcsomag például a csomag, amely nem indítható el és a egy csomagot, amely nem olvasható. A telepített csomag alkalmazás neve (URI), a csomópont neve (karakterlánc), a service manifest neve (karakterlánc) és a szolgáltatási csomag aktiválási azonosító (karakterlánc) azonosítja.

Az állapotközpontú modellről részletességét megkönnyíti a észlelheti és javíthatja a problémákat. Például ha a szolgáltatás nem válaszol, is alkalmazható jelenti, hogy az alkalmazás-példány állapota nem megfelelő. Reporting telepítésének, hogy szint ne legyen ideális megoldás, viszont mivel a probléma előfordulhat, hogy nem kell érintő adott alkalmazásban lévő összes szolgáltatás. Ha további információkat az adott partíció mutat a jelentés a nem megfelelő állapotú szolgáltatás vagy egy adott gyermekpartíciót kell alkalmazni. Az adatok automatikusan felületeken keresztül a hierarchiában, és a egy sérült partíció láthatóvá válik szolgáltatások és alkalmazások szinten. Ez az összesítés építve, és gyorsabban oldhatók meg azok a probléma okának segítségével.

A health hierarchia szülő-gyermek típusú kapcsolatokat áll. A fürt csomópontok és alkalmazások tevődik össze. Az alkalmazások az integrációs szolgáltatások és telepített alkalmazások. A telepített alkalmazások service-csomagok üzembe. Szolgáltatások létrehozott egy partíciót, és mindegyik partíció rendelkezik egy vagy több replikát. Speciális csomópontok és a telepített entitások közötti kapcsolat áll fenn. Egy nem megfelelő állapotú csomóponti jelentése szerint a felügyeleti rendszer összetevőt, a Feladatátvevőfürt-kezelő szolgáltatás hatással van a központilag telepített alkalmazások, szolgáltatási csomagok és a replikát.

A rendszerállapot-hierarchiában a rendszer a legújabb rendszerállapot-jelentések alapján, vagyis majdnem valós idejű információkat legfrissebb állapotát jeleníti meg.
Belső és külső watchdogs jelentései ugyanazokat az entitásokat a logikai alkalmazás-specifikus vagy egyéni figyelt feltételek alapján. Felhasználói jelentések megtalálhatók a jelentések segítségével.

Tervezze meg, hogy hogyan nagy méretű felhőalapú szolgáltatások tervezése során egészségügyi válaszolni, és jelentse be. A kezdeti beruházást, hibakeresés, monitorozása és működtetése megkönnyíti a szolgáltatás.

## <a name="health-states"></a>Állapotokat
A Service Fabric állapota háromféle írja le, hogy egy entitás kifogástalan állapotban-e vagy nem használ: OK, figyelmeztetés és hiba. A health Store adatbázisban küldött összes jelentés kell adnia ezeket az állapotokat az egyik. Az egészségügyi kiértékelésének eredménye az ezeket az állapotokat az egyik.

A lehetséges [állapotokat](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate) vannak:

* **OK**. Az entitás állapota kifogástalan. Nem tartoznak ismert problémák, vagy (ha alkalmazható) gyermekre jelentett.
* **Figyelmeztetés**. Az entitás rendelkezik néhány problémát, de továbbra is képes megfelelően működni. Ha például vannak az késleltetések, de ne okozzanak működési problémákat még. Bizonyos esetekben a figyelmeztetési feltételének előfordulhat, hogy javítsa ki magát külső beavatkozás nélkül. Ezekben az esetekben a rendszerállapot-jelentések hangsúlyozásával segítse és nyújtanak betekintést, mi történik. Más esetekben a figyelmeztetési feltételének felhasználói beavatkozás nélkül súlyos hibába ütközik, csökkenhet a.
* **Error**. Az entitás állapota nem megfelelő. Művelet kell tenni, javítsa ki az entitás állapotát, mert nem képes megfelelően működni.
* **Ismeretlen**. Az entitás a health Store adatbázisban nem létezik. Ez az eredmény az elosztott lekérdezések, amelyek több összetevőből származó eredmények egyesítése szerezhető. Ha például a get-csomópont listalekérdezés kerül **FailoverManager**, **ClusterManager**, és **HealthManager**; alkalmazás listalekérdezés kerül  **ClusterManager** és **HealthManager**. Ezek a lekérdezések egyesítése több rendszerösszetevők eredményeinek. Ha egy másik rendszerösszetevő ad vissza egy entitás, amely nem található a health Store adatbázisban, az egyesített eredmény rendelkezik-e az ismeretlen állapot. Egy entitás nem áll tárolóban, mivel a rendszerállapot-jelentések rendelkezik nem lett feldolgozva, vagy az entitás a törlés után eltávolította.

## <a name="health-policies"></a>Házirendek
A health Store adatbázisban megállapításához, hogy egy entitás kifogástalan állapotú a jelentések és annak gyermekeihez alapján a házirendek vonatkozik.

> [!NOTE]
> A fürtjegyzékben (a fürt és a csomópont állapota kiértékelési) vagy az alkalmazásjegyzékben (az alkalmazások kiértékelését és bármely gyermeke) állapotházirendeket adható meg. Az egyéni értékelési állapotházirendeket, amelyek csak a értékelés is átadhat értékelési rendszerállapot-kérelmek.
> 
> 

Alapértelmezés szerint a Service Fabric alkalmazza a szülő-gyermek hierarchikus kapcsolat (mindent lehet kifogástalan) szigorú szabályok. Ha még a gyermekek egyik sérült állapotot jelző esemény egy, a szülő nem megfelelő állapotúnak számít.

### <a name="cluster-health-policy"></a>Fürt állapota házirend
A [állapotházirend fürt](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy) segítségével értékelje ki a fürt állapotának és a csomópont állapotokat. A szabályzat a fürtjegyzék lehet definiálni. Ha nem található, használja az alapértelmezett házirend (nulla eltűrt hibák).
A fürt állapotházirend tartalmazza:

* [ConsiderWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Itt adhatja meg, e kezelni a figyelmeztetési állapot kiderítheti hibák egészségügyi kiértékelés során. Alapértelmezett: False (hamis).
* [MaxPercentUnhealthyApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications). Meghatározza az alkalmazásokat, amelyek nem megfelelő állapotú is lehet, mielőtt a fürt hibás számít eltűrt maximális százalékát.
* [MaxPercentUnhealthyNodes](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes). Itt adhatja meg, amely előtt a fürt hibás lehet sérült csomópontok maximális eltűrt százalékát. Nagy fürtjein, az egyes csomópontok vannak mindig le- illetve felskálázni a javításához, így ezt a százalékos arányt, amely ugyan úgy kell konfigurálni.
* [ApplicationTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap). Speciális alkalmazástípusokat ismerteti típusát állapotfigyelő szabályzat alkalmazástérkép használható fürt állapotának kiértékelése közben. Alapértelmezés szerint minden alkalmazás készletbe helyezzen és a MaxPercentUnhealthyApplications értékeli ki. Néhány alkalmazástípus eltérően kell kezelni, ha ezek a globális készletből elvégezhet. Ehelyett rendszer értékeli ki őket a az alkalmazás típusát, a térkép társított százalékos ellen. Például egy fürtben vannak több ezer, különböző típusú alkalmazáshoz, és néhány speciális alkalmazás típusú vezérlőelem az alkalmazáspéldányok. Az alkalmazások soha ne legyen hiba. Globális MaxPercentUnhealthyApplications 20 %-a kezelni kívánt meghibásodások bizonyos hibák, de az alkalmazás típusának "ControlApplicationType" a MaxPercentUnhealthyApplications állítsa 0-ra is megadhat. Így, ha sok alkalmazások közül néhány nem megfelelő állapotú, de a globális nem megfelelő állapotú százalékos arány alatt a fürt akkor kiértékelendő figyelmeztetés. A figyelmeztetési állapot nem befolyásolja a fürt frissítése, vagy egyéb figyelési hibaállapot váltott. De akár egy alkalmazást a hibás tenné a fürt nem kifogástalan, amely visszaállítási elindítja vagy leállítja a fürt frissítésének frissítési konfigurációjától függően.
  Az alkalmazás-típusokkal a térképen, az összes alkalmazáspéldány kiveszik a globális készlet alkalmazások. Akkor értékeli ki az alkalmazás típusát, az adott MaxPercentUnhealthyApplications leképezés használatával az alkalmazások teljes száma alapján. Az alkalmazások az összes többi a globális készlet maradnak, és a MaxPercentUnhealthyApplications értékeli.

Az alábbi példa egy olyan fürt-jegyzékfájlból. Az adattípus-leképezésben bejegyzések definiálásához előtagot a paraméternév megadásához, az "ApplicationTypeMaxPercentUnhealthyApplications-", az alkalmazástípus neve követ.

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

### <a name="application-health-policy"></a>Az alkalmazás állapotszabályzata
A [az alkalmazás állapotszabályzata](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy) ismerteti, hogyan történik, a kiértékelés az események és a gyermek-állapotok összesítés alkalmazások és ellenőrizhessék a gyermekek számára. Az alkalmazásjegyzékben definiálható **ApplicationManifest.xml**, alkalmazáscsomagban. Ha nincsenek házirendek meg van adva, a Service Fabric feltételezi, hogy az entitás nem megfelelő állapotú-e azt egy jelentés vagy egy alárendelt, figyelmeztetés vagy hibaüzenet állapota.
A konfigurálható szabályzatok a következők:

* [ConsiderWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Itt adhatja meg, e kezelni a figyelmeztetési állapot kiderítheti hibák egészségügyi kiértékelés során. Alapértelmezett: False (hamis).
* [MaxPercentUnhealthyDeployedApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications). Itt adhatja meg a központilag telepített alkalmazások, amelyek nem megfelelő állapotú előtt az alkalmazás hibás eltűrt maximális százalékos aránya. Ez a százalékérték sérült telepített alkalmazások száma felosztásával keresztül telepített alkalmazások jelenleg a fürtben található csomópontok számát. Felfelé kerekít a számítási csomópontok kis számú egy hiba elviselni. Alapértelmezett százalékos aránya: nulla.
* [DefaultServiceTypeHealthPolicy](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy). Adja meg az alapértelmezett szolgáltatás típusa egészségügyi szabályzatot, amely felülírja az alapértelmezett házirend az összes szolgáltatás esetében az alkalmazás.
* [ServiceTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap). Service health szabályzatokat szolgáltatástípus térképet biztosít. Ezek a szabályzatok cserélje le a házirendek alapértelmezett szolgáltatás típusa az egyes megadott szolgáltatás. Például, ha egy alkalmazásnak egy állapotmentes szolgáltatás átjárótípus és egy állapotalapú motor szolgáltatás típusa az egészségügyi házirendeket konfigurálhat a tesztelési eltérően. Szabályzat beállítása szolgáltatás típusának megadásakor szerezhet pontosabban szabályozhatja a a szolgáltatás állapotát.

### <a name="service-type-health-policy"></a>Szolgáltatás típusa állapotházirend
A [szolgáltatás típus állapotházirend](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy) határozza meg, hogy kiértékeléséhez, és a szolgáltatások és szolgáltatások gyermekei összesíteni. A házirend tartalmazza:

* [MaxPercentUnhealthyPartitionsPerService](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice). Megadja a sérült partíciók megengedett maximális százalékos aránya, mielőtt a szolgáltatás nem megfelelő állapotúnak számít. Alapértelmezett százalékos aránya: nulla.
* [MaxPercentUnhealthyReplicasPerPartition](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition). Megadja a sérült replikák eltűrt maximális százalékos aránya, mielőtt a partíció nem megfelelő állapotúnak számít. Alapértelmezett százalékos aránya: nulla.
* [MaxPercentUnhealthyServices](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices). Adja meg a nem megfelelő állapotú szolgáltatások eltűrt maximális százalékos aránya, mielőtt az alkalmazás nem megfelelő állapotúnak számít. Alapértelmezett százalékos aránya: nulla.

Az alábbi példa egy olyan alkalmazás-jegyzékfájlból:

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

## <a name="health-evaluation"></a>Állapot értékelése
Felhasználók és az automatikus szolgáltatások bármikor képes kiértékelni minden entitás állapotát. Egy entitás állapotának értékeléséhez, a health store összesítések összes egészségügyi jelentés az entitásra, és kiértékeli az összes gyermekre (ha alkalmazható). Az egészségügyi összesítési algoritmust használ állapotházirendeket, amelyek kiértékelése a rendszerállapot-jelentések és hogyan lehet összesíteni a gyermek állapotokat (ha alkalmazható).

### <a name="health-report-aggregation"></a>Egészségügyi jelentés összesítés
Egy entitás különböző jelentéskészítők (rendszerösszetevők vagy watchdogs) a különböző tulajdonságok által küldött több állapotjelentések rendelkezhet. Ilyenkor az összesítést a tartozó Eszközállapot házirendeket használ, különösen az alkalmazás vagy a fürt állapotházirend ConsiderWarningAsError tag. ConsiderWarningAsError adja meg a figyelmeztetéseket kiértékelése.

Összesített állapotát váltja a *legrosszabb* rendszerállapot-jelentéseket az entitásra. Ha legalább egy hiba állapotjelentés, összesített állapota a hiba.

![Egészségügyi jelentés összesítés a hibákat tartalmazó jelentést.][2]

A health entitás, amely rendelkezik egy vagy több hiba állapotjelentések hiba minősül. Ugyanez érvényes lejárt állapotjelentés, függetlenül attól, állapotába.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Ha nincs hibajelentések és a egy vagy több figyelmeztetés, összesített állapota, figyelmeztetés vagy hiba történt a ConsiderWarningAsError házirend jelző függően.

![Egészségügyi jelentés összesítési figyelmeztetés a jelentés és ConsiderWarningAsError hamis.][3]

A figyelmeztetés a jelentés és ConsiderWarningAsError egészségügyi jelentés összesítési false (alapértelmezett) értékre.

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>Gyermek állapotának összesítése
Egy entitás összesített állapotát jeleníti meg a gyermek állapotokat (ha alkalmazható). Az algoritmus optimalizált gyermek állapotokat összesíti az állapotfigyelő házirendeket használ alkalmazható entitás típusa alapján.

![Gyermek entitások állapotának összesítése.][4]

Gyermek összesítési egészségügyi szabályzatok alapján.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Miután az összes gyermekre a health Store adatbázisban kiértékelése, gyűjti azok alapján a beállított maximális százalékos aránya a gyermekek nem kifogástalan állapotúak. Ezt a százalékos arányt átveszi a rendszer a házirend entitás- és típusa alapján.

* Ha az összes gyermekre OK állapota van, a gyermek összesített állapota rendben.
* Ha gyermek OK és a figyelmeztetési állapotok, a gyermek összesített állapota figyelmet igényel.
* Ha gyermek hibaállapotok, amelyek túllépik a megengedett maximális százalékos aránya a sérült gyermek-, összesített szülő állapota a hiba.
* Ha a gyermekek a hibaállapotok tiszteletben a sérült gyermek engedélyezett maximális százalékos aránya, a szülő összesített állapota figyelmet igényel.

## <a name="health-reporting"></a>Állapotfigyelő jelentési
Rendszer összetevőit, a rendszer Fabric-alkalmazások és a belső/külső watchdogs jelentheti a Service Fabric-entitások ellen. A jelentéskészítők győződjön meg arról, *helyi* meghatározás állapota, a figyelt entitásokat, a figyelt azok feltételek alapján. Nem szükséges minden olyan globális állapot vagy az összesített adatok megjelenítéséhez. A kívánt viselkedés, a jelentéskészítők egyszerű, és nem összetett szervezetekre, hogy milyen információkat küldhet célszámítógéppel sok-sok dolog meg kell hogy.

Egészségügyi adatokat küldeni a health Store adatbázisban, egy riporter kell azonosítja az érintett entitásokat, és a egy jelentés létrehozása. Szeretne küldeni a jelentést, használja a [FabricClient.HealthClient.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) API, API-k a közzétett jelentés állapotát a `Partition` vagy `CodePackageActivationContext` objektumok, a PowerShell-parancsmagok vagy a REST.

### <a name="health-reports"></a>Rendszerállapot-jelentések
A [rendszerállapot-jelentések](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthreport) minden, az entitások a fürt a következő információkat tartalmazza:

* **SourceId**. Egy karakterlánc, amely egyedileg azonosítja a riporter a health-esemény.
* **Entitásazonosító**. Az entitást, ahol van alkalmazva a jelentés azonosítja. Ez eltér a [entitástípus](service-fabric-health-introduction.md#health-entities-and-hierarchy):
  
  * A fürt. Nincs.
  * Csomópont. Csomópont neve (karakterlánc).
  * az alkalmazás. Alkalmazás neve (URI). A fürtben üzembe helyezett alkalmazás-példány nevét jelöli.
  * A szolgáltatás. Szolgáltatás neve (URI). A-szolgáltatáspéldány van telepítve, a fürt nevét jelöli.
  * A partíció. Partíció azonosítója (GUID). A partíció egyedi azonosítóját jelöli.
  * A replika. Az állapotalapú szolgáltatás másodpéldány-azonosító vagy a stateless service-példány Azonosítóját (INT64).
  * DeployedApplication. Alkalmazás neve (URI) és a csomópont neve (karakterlánc).
  * DeployedServicePackage. Alkalmazás neve (URI), a csomópont neve (karakterlánc) és a service manifest neve (karakterlánc).
* **Vlastnost**. A *karakterlánc* (nem fix enumerálási), amely lehetővé teszi, hogy a jelentéskészítő az entitás egy adott tulajdonságra vonatkozó állapottal kapcsolatos esemény csoportosítását. Például riporter A jelenthetik-e a Node01 állapotát "Tároló" tulajdonság és a B riporter jelenthetik a Node01 állapotát "Kapcsolat" tulajdonság. A health Store adatbázisban ezek a jelentések külön állapotesemények Node01 entitás kell kezelni.
* **Leírás**. Egy karakterlánc, amely lehetővé teszi, hogy a jelentéskészítő az állapottal kapcsolatos esemény részletes információkat biztosít. **SourceId**, **tulajdonság**, és **HealthState** a jelentés teljes kell ismertetnie. A leírás ad emberek számára olvasható jelentés adatai. A szöveg megkönnyíti a rendszergazdák és felhasználók az állapotjelentés megértéséhez.
* **HealthState**. Egy [enumerálás](service-fabric-health-introduction.md#health-states) , amely leírja, hogy a jelentés állapotát. Az elfogadott értékek a következők: OK, figyelmeztetés és hiba.
* **Az élettartam**. Egy időtartam, amely azt jelzi, hogy mennyi ideig az állapotjelentés érvényes. Párosított **RemoveWhenExpired**, ez lehetővé teszi, hogy tudja, hogyan értékelheti ki a lejárt események, a health Store adatbázisban. Alapértelmezés szerint az érték a végtelen, és a jelentés érvényes örökre.
* **RemoveWhenExpired**. Logikai érték beolvasása. Ha igaz értékű, az lejárt állapotjelentés automatikusan törlődik a health Store adatbázisban, és a jelentés nem érinti az entitás állapotának kiértékelését. Használható a jelentés egy megadott időtartam elteltéig csak érvényességét, és a jelentéskészítő nem kell explicit módon is kapcsolhatja ki. Jelentések törlése a health store-ból is használható (például egy figyelő módosul, és leállítja a jelentéseknek az előző forrás és tulajdonság). Egy jelentés bármely korábbi állapotba a a health store adatbázisból kiürítéséhez RemoveWhenExpired együtt egy rövid TimeToLive küldésére. Ha az értéke HAMIS, a lejárt jelentés a állapotának kiértékelését a hiba számít. A False (hamis) érték jelzi a health Store adatbázisban számára, hogy a forrás jelentést kell rendszeresen ezt a tulajdonságot. Ha nem, majd kell valamilyen hiba történt a figyelő. A figyelő állapota az esemény hibát a mérlegeli rögzített.
* **SequenceNumber**. Pozitív egésznek kell lennie a folyamatosan növekvő konkurenciával kell, a jelentések sorrendet jelöli. Akkor használják a health Store adatbázisban észleli a hálózati késések vagy egyéb problémák miatt későn érkező elavult jelentéseket. Egy jelentés nem fogadja el, ha a feladatütemezés szám kisebb vagy egyenlő a legtöbb nemrég alkalmazva az ugyanahhoz az entitáshoz, a forrás és a tulajdonság száma. Ha nincs megadva, automatikusan létrejön a sorozatszámot. Fontos a sorozatszám csak akkor, ha a jelentéskészítési állapotváltozáskor helyezni. Ebben a helyzetben a forrás van szüksége, ne feledje, akkor küldi el a jelentéseket, és biztosíthatja az adatok a feladatátvételkor a helyreállításhoz.

Négy megtalálhatja információk – SourceId, entitásának azonosítója, tulajdonság vagy HealthState--szükségesek minden jelentés. A SourceId karakterlánc nem engedélyezett előtaggal kezdődik "**rendszer.**", amely jelentéseket a rendszer számára van fenntartva. Ugyanahhoz az entitáshoz a ugyanolyan forráshoz és a tulajdonság csak egy jelentés van. Több jelentést is ugyanazt a forrás és tulajdonság fedjék át egymást, vagy az ügyféloldalon health (ha azok vannak kötegelni) vagy az egészségügyi ügyféloldali tárolja. A csere sorozatszámok; alapján (az újabb sorozatszámok) új jelentések lecserélik a régebbi jelentések.

### <a name="health-events"></a>A Szolgáltatásállapot-események
Belsőleg, a health Store adatbázisban tartja [hálózatállapot-események](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthevent), amelyek a jelentések, illetve további metaadatokat kapcsolatos összes információt tartalmaznak. A metaadatok lett megadva a jelentés az egészségügyi ügyfél időpontját és azt a kiszolgálói oldalon módosításának idejét tartalmazza. A Szolgáltatásállapot-események által visszaadott [állapotlekérdezések](service-fabric-view-entities-aggregated-health.md#health-queries).

A hozzáadott metaadatokat tartalmazza:

* **SourceUtcTimestamp**. Az idő a jelentés az egészségügyi ügyfél (egyezményes világidő) lett megadva.
* **LastModifiedUtcTimestamp**. A jelentés a kiszolgálói oldalon (egyezményes világidő) utolsó módosításának időpontja.
* **IsExpired**. Jelző-e a jelentés lejárt-e, ha a lekérdezést hajtott végre a health Store adatbázisban. Egy esemény is járhatott, csak akkor, ha RemoveWhenExpired false (hamis). Ellenkező esetben az esemény lekérdezés nem ad vissza, és a tároló törlődik.
* **LastOkTransitionAt**, **LastWarningTransitionAt**, **LastErrorTransitionAt**. OK és figyelmeztetés/hiba átmenetek utolsó időpontját. Ezek a mezők ad az egészségügyi előzményeit állapotváltozási adat áramlik az esemény.

Az állapot átmeneti mezők a megalapozottabb riasztásokat vagy a "korábbi" állapotinformációkat esemény használható. A használatukkal forgatókönyvek például:

* Riasztás, ha egy tulajdonság volt, figyelmeztetés vagy hiba a több mint X perc. Ellenőrzi a feltételt egy ideig elkerülheti a riasztások a ideiglenes feltételek. Ha például egy riasztást, ha több mint öt perc alatt az állapot rendelkezik lett figyelmeztetés lefordítható (HealthState figyelmeztetés, és most - LastWarningTransitionTime == > 5 perc).
* Csak a feltételeket, amelyek megváltoztak az utolsó riasztás X perc. Ha egy jelentés már volt, hiba történt a megadott időpont előtt, figyelmen kívül hagyható, mivel már volt korábban jelzést.
* Ha egy tulajdonságot a figyelmeztetési és váltása, határozza meg, mennyi ideig már nem megfelelő állapotú (azaz nem OK). Ha például egy riasztást, ha a tulajdonság még nem kifogástalan állapotú öt percnél hosszabb ideig lefordítható (HealthState! = OK gombra, és most - LastOkTransitionTime > 5 perc).

## <a name="example-report-and-evaluate-application-health"></a>Példa: Alkalmazás állapotának kiértékelése és jelentse be
Az alábbi példa egy állapotjelentése az alkalmazást a Powershellen keresztül küld **fabric: / WordCount** a forrásból **MyWatchdog**. Az állapotjelentés az egészségügyi létre hibaállapot, végtelen TimeToLive az "availability" tulajdonság információkat tartalmaz. Ezután lekérdezi az alkalmazás állapotáról, amely összesítve egészségügyi állapot hibák és a jelentett hálózatállapot-események hálózatállapot-események listájában.

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

## <a name="health-model-usage"></a>Egészségügyi modell használat
Az állapotközpontú modellről lehetővé teszi, hogy a cloud services és az alapul szolgáló Service Fabric platform, amely méretezhető, mert figyelési és meghatározás vannak bontva a fürtön belül a különböző figyelők.
Más rendszerek egyetlen központosított szolgáltatás rendelkezik a fürt szintjén, amely az összes elemzi a *potenciálisan* szolgáltatások által kibocsátott hasznos információkat. Ez a megközelítés akadályozza a méretezhetőséget. Még nem teszi lehetővé a őket gyűjtendő információk segítségével azonosíthatja a problémákat és a potenciális problémákról, közel a hiba okát, amennyire csak lehetséges.

A monitorozási és diagnosztikai, fürtök és alkalmazások állapotának értékeléséhez és a figyelt frissítésekre az állapotközpontú modellről az erősen szolgál. Más szolgáltatások automatikus javítások végrehajtásához, fürt állapotelőzmények létrehozását, és adja ki a riasztások bizonyos feltételek a rendszerállapot-adatok használatával.

## <a name="next-steps"></a>További lépések
[A Service Fabric-állapotjelentések megtekintése](service-fabric-view-entities-aggregated-health.md)

[Hibaelhárítás rendszerállapot-jelentések használata](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Hogyan lehet szolgáltatási állapot jelentése és ellenőrzése](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Egyéni Service Fabric-állapotjelentések hozzáadása](service-fabric-report-health.md)

[A szolgáltatások helyi monitorozása és diagnosztizálása](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric-alkalmazás frissítése](service-fabric-application-upgrade.md)

