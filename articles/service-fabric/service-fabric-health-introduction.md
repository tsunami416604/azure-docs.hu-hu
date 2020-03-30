---
title: Állapotfigyelés a Service Fabricben
description: Bevezetés az Azure Service Fabric állapotfigyelési modelljébe, amely a fürt, valamint az alkalmazások és szolgáltatások figyelését biztosítja.
author: oanapl
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: 473aa2b9a74193a857390cd3e29b2b559b6084d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282418"
---
# <a name="introduction-to-service-fabric-health-monitoring"></a>A Service Fabric állapotmonitorozásának bemutatása
Az Azure Service Fabric egy állapotmodellt vezet be, amely gazdag, rugalmas és bővíthető állapotértékelést és -jelentést nyújt. A modell lehetővé teszi a fürt és a benne futó szolgáltatások állapotának közel valós idejű figyelését. Könnyedén szerezhet egészségügyi információkat, és kijavíthatja a lehetséges problémákat, mielőtt azok kaszkádolnak, és hatalmas leállásokat okozhatnak. A tipikus modellben a szolgáltatások a helyi nézetek alapján küldenek jelentéseket, és az adatok összesítése átfogó fürtszintű nézetet biztosít.

A Service Fabric-összetevők ezt a gazdag állapotmodellt használják a jelenlegi állapotuk jelentéséhez. Használhatja ugyanazt a mechanizmust az alkalmazások állapotának jelentéséhez. Ha olyan jó minőségű állapotjelentésbe fektet be, amely rögzíti az egyéni feltételeket, sokkal könnyebben észlelheti és javíthatja a futó alkalmazás problémáit.

> [!NOTE]
> Elindítottuk az állapoti alrendszert, hogy kielégítsük a felügyelt frissítések szükségességét. A Service Fabric figyelt alkalmazás- és fürtfrissítéseket biztosít, amelyek biztosítják a teljes rendelkezésre állást, az állásidőt és a minimális felhasználói beavatkozást. Ezeknek a céloknak a elérése érdekében a frissítés a konfigurált frissítési házirendek alapján ellenőrzi az állapotot. A frissítés csak akkor folytatható, ha az állapot tiszteletben tartja a kívánt küszöbértékeket. Ellenkező esetben a frissítés automatikusan visszalesz állítva, vagy szünetel, hogy a rendszergazdák nak lehetőségük legyen a problémák megoldására. Az alkalmazásfrissítésekről ebben a cikkben olvashat [bővebben.](service-fabric-application-upgrade.md)
> 
> 

## <a name="health-store"></a>Egészségáruház
Az állapottároló a fürtben lévő entitásokra vonatkozó, állapottal kapcsolatos információkat tárol a egyszerű lekérés és kiértékelés érdekében. A Service Fabric tartósan állapotalapú szolgáltatás, amely biztosítja a magas rendelkezésre állást és a méretezhetőséget. A health store része a **háló:/Rendszer** alkalmazás, és akkor érhető el, ha a fürt működik.

## <a name="health-entities-and-hierarchy"></a>Egészségügyi entitások és hierarchia
Az állapotentitások logikai hierarchiába vannak rendezve, amely rögzíti a különböző entitások közötti interakciókat és függőségeket. Az állapottároló automatikusan létrehozza az állapotentitásokat és a hierarchiát a Service Fabric-összetevőktől kapott jelentések alapján.

Az állapotentitások tükrözik a Service Fabric entitások. (Például **az állapotalkalmazás-entitás** megfelel a fürtben telepített alkalmazáspéldánynak, míg az **állapotcsomópont-entitás** megfelel egy Service Fabric-fürtcsomópontnak.) Az állapothierarchia rögzíti a rendszerentitások interakcióit, és ez az alapja a speciális állapotkiértékelésnek. A Service Fabric legfontosabb fogalmairól a [Service Fabric technikai áttekintésében olvashat.](service-fabric-technical-overview.md) Az alkalmazásról a [Service Fabric alkalmazásmodellben.](service-fabric-application-model.md)

Az állapotentitások és a hierarchia lehetővé teszi a fürt és az alkalmazások hatékony annekt, és figyelése. Az állapotmodell pontos, *részletes* reprezentációt biztosít a fürt számos mozgó darabjának állapotáról.

![Állapotentitások.][1]
A szülő-gyermek kapcsolatokon alapuló hierarchiába rendezett állapotentitások.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

Az egészségügyi entitások a következők:

* **Fürt .** Egy Service Fabric-fürt állapotát jelöli. A fürt állapotjelentései a teljes fürtöt érintő feltételeket írják le. Ezek a feltételek a fürt több entitását vagy magát a fürtöt érintik. Az állapot alapján a riporter nem tudja leszűkíteni a problémát egy vagy több egészségtelen gyerekre. Ilyenpéldául a fürt felosztásának agya hálózati particionálási vagy kommunikációs problémák miatt.
* **Csomópont**. Egy Service Fabric-csomópont állapotát jelöli. A csomópont állapotjelentései a csomópont működését befolyásoló feltételeket írnak le. Általában az összes üzembe helyezett entitást érintik. Ilyenek például a csomópont on-of lemezterület (vagy más gép-szintű tulajdonságok, például a memória, kapcsolatok), és ha egy csomópont nem. A csomópont entitást a csomópont neve (karakterlánc) azonosítja.
* **Alkalmazás**. A fürtben futó alkalmazáspéldány állapotát jelöli. Az alkalmazás állapotjelentései olyan feltételeket írnak le, amelyek befolyásolják az alkalmazás általános állapotát. Nem lehet leszűkíteni az egyes gyermekekre (szolgáltatásokra vagy telepített alkalmazásokra). Példák közé tartozik a végpontok közötti interakció az alkalmazás különböző szolgáltatások között. Az alkalmazásentitást az alkalmazás neve (URI) azonosítja.
* **Szolgáltatás**. A fürtben futó szolgáltatás állapotát jelöli. A szolgáltatás állapotjelentéseinek leírása olyan feltételeket, amelyek befolyásolják a szolgáltatás általános állapotát. Az oktatónem szűkítheti le a problémát egy nem megfelelő állapotú partícióra vagy replikára. Ilyen szolgáltatáskonfiguráció (például port vagy külső fájlmegosztás), amely minden partíciónál problémát okoz. A szolgáltatás entitást a szolgáltatás neve (URI) azonosítja.
* **Partíció**. Egy szolgáltatáspartíció állapotát jelöli. A partíció állapotjelentései a teljes replikakészletet érintő feltételeket írják le. Ilyenek például, ha a replikák száma nem éri el a célszámát, és ha egy partíció kvórumveszteség. A partícióentitást a partícióazonosító (GUID) azonosítja.
* **Replika**. Egy állapotalapú szolgáltatásreplika vagy egy állapotmentes szolgáltatáspéldány állapotát jelöli. A replika a legkisebb egység, amely watchdogs és a rendszer-összetevők jelentést egy alkalmazás. Állapotalapú szolgáltatások esetében példák közé tartozik egy elsődleges replika, amely nem replikálhatja a műveleteket a másodlagos és lassú replikáció. Emellett egy állapotmentes példány jelentheti, ha elfogy az erőforrások, vagy kapcsolódási problémák. A replika entitás t azonosítja a partíció azonosítója (GUID) és a replika vagy példány azonosítója (hosszú).
* **Telepített alkalmazás**. A *csomóponton futó alkalmazás*állapotát jelöli. Az üzembe helyezett alkalmazás állapotjelentések az alkalmazásra jellemző feltételeket írják le a csomóponton, amelyek nem szűkülhetnek le az ugyanazon a csomóponton telepített szolgáltatáscsomagokra. Ilyenek például azok a hibák, amikor az alkalmazáscsomag nem tölthető le az adott csomópontra, és problémák vannak az alkalmazásrendszeri rendszerbiztonsági tagok csomóponton történő beállításával. A telepített alkalmazást az alkalmazás neve (URI) és a csomópont neve (karakterlánc) azonosítja.
* **DeployedServicePackage**. A fürt csomópontján futó szolgáltatáscsomag állapotát jelöli. Ismerteti a szolgáltatási csomagra vonatkozó azon feltételeket, amelyek nem érintik az ugyanazon az alkalmazás ugyanazon csomópontján lévő többi szolgáltatáscsomagot. Ilyenek például egy kódcsomag a szolgáltatáscsomagban, amely nem indítható el, és egy konfigurációs csomag, amely nem olvasható. A telepített szolgáltatáscsomagot az alkalmazás neve (URI), a csomópont neve (karakterlánc), a szolgáltatásjegyzék neve (karakterlánc) és a szolgáltatáscsomag aktiválási azonosítója (karakterlánc) azonosítja.

Az állapotmodell részletessége megkönnyíti a problémák észlelését és kijavítását. Ha például egy szolgáltatás nem válaszol, akkor az alkalmazáspéldány nem megfelelő állapotú. Az ezen a szinten történő jelentéskészítés azonban nem ideális, mert a probléma nem feltétlenül érinti az alkalmazáson belüli összes szolgáltatást. A jelentést a nem megfelelő állapotú szolgáltatásra vagy egy adott gyermekpartícióra kell alkalmazni, ha további információk mutatnak az adott partícióra. Az adatok automatikusan megjelennek a hierarchián keresztül, és egy nem megfelelő állapotú partíció láthatóvá válik a szolgáltatás és az alkalmazás szintjén. Ez az összesítés segít a probléma kiváltó okának gyorsabb azonosításában és megoldásában.

Az állapothierarchia szülő-gyermek kapcsolatokból áll. A fürt csomópontokból és alkalmazásokból áll. Az alkalmazások szolgáltatásokkal és telepített alkalmazásokkal rendelkeznek. Az üzembe helyezett alkalmazások szolgáltatáscsomagokat telepítettek. A szolgáltatások partíciókkal rendelkeznek, és minden partíció egy vagy több replikával rendelkezik. A csomópontok és az üzembe helyezett entitások között különleges kapcsolat áll fenn. A felügyeleti rendszer összetevője, a Feladatátvevő kezelő szolgáltatás által jelentett, nem megfelelő állapotú csomópont hatással van az üzembe helyezett alkalmazásokra, szolgáltatáscsomagokra és replikákra.

Az állapothierarchia a rendszer legújabb állapotát képviseli a legfrissebb állapotjelentések alapján, amely szinte valós idejű információ.
Belső és külső figyelők jelentheti az azonos entitások alkalmazásspecifikus logika vagy egyéni figyelt feltételek alapján. A felhasználói jelentések a rendszerjelentésekkel együtt léteznek.

Tervezze meg, hogyan jelentheti az állapotot és hogyan reagálhat az egészségre egy nagy felhőszolgáltatás tervezése során. Ez az előzetes befektetés megkönnyíti a szolgáltatás hibakeresését, figyelését és üzemeltetését.

## <a name="health-states"></a>Egészségügyi állapotok
A Service Fabric három állapotot használ annak leírására, hogy egy entitás kifogástalan állapotban van-e vagy sem: OK, figyelmeztetés és hiba. A health store-ba küldött jelentéseknek meg kell adniuk ezen állapotok egyikét. Az egészségügyi értékelés eredménye az egyik ilyen állam.

A lehetséges [állapotok](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate) a következők:

* **Oké, ez az.** Az entitás kifogástalan állapotú. Nincsenek ismert problémák róluk vagy gyermekeiről (adott esetben).
* **Figyelmeztetés**. Az entitásnak vannak problémái, de továbbra is megfelelően működhet. Például vannak késések, de még nem okoznak funkcionális problémákat. Bizonyos esetekben a figyelmeztetési feltétel külső beavatkozás nélkül is megoldhatja magát. Ezekben az esetekben az egészségügyi jelentések felhívják a figyelmet, és betekintést nyújtanak a folyamatban lévő dolgokba. Más esetekben a figyelmeztető állapot felhasználói beavatkozás nélkül súlyos problémává bomlik le.
* **Error**. Az entitás nem kifogástalan. Lépéseket kell tenni az entitás állapotának javításához, mert nem működik megfelelően.
* **Ismeretlen**. Az entitás nem létezik a health Store.The entity doesn't exist in the health store. Ez az eredmény az elosztott lekérdezésekből szerezhető be, amelyek több összetevő eredményeit egyesítik. A begetszolgáltatási csomópontlista lekérdezés például a **FailoverManager**, **a ClusterManager**és a **HealthManager**lesz. az alkalmazáslista-lekérdezés a **ClusterManager** és a **HealthManager**lesz. Ezek a lekérdezések több rendszerösszetevő eredményeit egyesítik. Ha egy másik rendszerösszetevő olyan entitást ad vissza, amely nincs jelen a health Store-ban, az egyesített eredmény ismeretlen állapotú. Egy entitás nincs raktáron, mert az állapotjelentések még nincsenek feldolgozva, vagy az entitás törlés után lett törölve.

## <a name="health-policies"></a>Egészségügyi politikák
Az állapottároló állapotházirendeket alkalmaz annak megállapítására, hogy egy entitás kifogástalan állapotban van-e a jelentései és gyermekei alapján.

> [!NOTE]
> Az állapotházirendek megadhatók a fürtjegyzékben (fürt- és csomópontállapot-kiértékeléshez) vagy az alkalmazásjegyzékben (az alkalmazás kiértékeléséhez és annak bármely gyermekéhez). Az állapotértékelési kérelmek egyéni állapotértékelési szabályzatokban is átmehetnek, amelyek csak az adott értékeléshez használatosak.
> 
> 

Alapértelmezés szerint a Service Fabric szigorú szabályokat alkalmaz (mindennek kifogástalannak kell lennie) a szülő-gyermek hierarchikus kapcsolatra. Ha még az egyik gyermek egy nem megfelelő állapotú esemény, a szülő tekinthető nem kifogástalan.

### <a name="cluster-health-policy"></a>Fürt állapotházirendje
A [fürt állapotházirendje](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy) a fürt állapotának és a csomópont állapotának kiértékelésére szolgál. A házirend definiálható a fürtjegyzékben. Ha nincs jelen, az alapértelmezett házirend (nulla tolerálható hibák) használatos.
A fürt állapotházirendje a következőket tartalmazza:

* [ConsiderWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Itt adható meg, hogy a figyelmeztetési állapotjelentéseket hibaként kezelje-e az állapotkiértékelés során. Alapértelmezett érték: false (hamis).
* [MaxPercentUnhealthyApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications). Megadja az alkalmazások maximális tolerálható százalékát, amely nem kifogástalan lehet, mielőtt a fürt hibásnak minősülne.
* [MaxPercentUnhealthyNodes](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes). Megadja a csomópontok maximális tolerálható százalékát, amely nem megfelelő állapotú lehet, mielőtt a fürt hibásnak minősülne. Nagy fürtök, egyes csomópontok mindig le vagy ki a javítást, ezért ezt a százalékot úgy kell beállítani, hogy tolerálja ezt.
* [ApplicationTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap). Az alkalmazástípus állapotházirend-leképezése a fürt állapotának kiértékelése során speciális alkalmazástípusok leírására használható. Alapértelmezés szerint az összes alkalmazás egy készletbe kerül, és a MaxPercentUnhealthyApplications alkalmazással kerül kiértékelésre. Ha bizonyos alkalmazástípusokat eltérően kell kezelni, akkor kivehetők a globális készletből. Ehelyett a rendszer az alkalmazástípus nevéhez tartozó százalékok alapján értékeli ki őket a térképen. Egy fürtben például több ezer különböző típusú alkalmazás és egy speciális alkalmazástípus néhány vezérlőalkalmazás-példánya van. A vezérlőalkalmazások soha nem lehetnek hibásak. Megadhatja a globális MaxPercentUnhealthyApplications 20%, hogy tolerálja bizonyos hibákat, de az alkalmazás típusa "ControlApplicationType" állítsa a MaxPercentUnhealthyApplications 0. Így ha a sok alkalmazás közül néhány nem kifogástalan, de a globális nem megfelelő állapotú százalékos érték alatt van, a fürt figyelmeztetéslesz. A figyelmeztetési állapot nem befolyásolja a fürt frissítését vagy a hibaállapot által kiváltott egyéb figyelést. De még egy vezérlőalkalmazás hiba tenné fürt nem kifogástalan, amely elindítja a visszaállítást, vagy szünetelteti a fürt frissítése, attól függően, hogy a frissítési konfiguráció.
  A térképen definiált alkalmazástípusok esetében az összes alkalmazáspéldány kikerül az alkalmazások globális készletéből. Ezek kiértékelése az alkalmazástípus alkalmazásainak teljes száma alapján történik, a térképről származó adott MaxPercentUnhealthyApplications használatával. Az összes többi alkalmazás a globális készletben marad, és a MaxPercentUnhealthyApplications alkalmazással lesz kiértékelve.

A következő példa egy részlet egy fürtjegyzékből. Az alkalmazástípus-leképezés bejegyzéseinek definiálásához előtagítsa meg a paraméter nevét az "ApplicationTypeMaxPercentUnhealthyApplications-"-mal, majd az alkalmazástípus nevét.

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

### <a name="application-health-policy"></a>Alkalmazás állapotházirendje
Az [alkalmazás állapotházirendje](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy) leírja, hogy az események és a gyermekállapotok összesítésének értékelése hogyan történik az alkalmazások és gyermekeik esetében. Az alkalmazáscsomagban definiálható az **ApplicationManifest.xml**alkalmazásjegyzékben. Ha nincs megadva házirendek, a Service Fabric feltételezi, hogy az entitás nem kifogástalan állapotú, ha rendelkezik egy állapotjelentés vagy egy gyermek a figyelmeztetés vagy hiba állapota.
A konfigurálható házirendek a következők:

* [ConsiderWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Itt adható meg, hogy a figyelmeztetési állapotjelentéseket hibaként kezelje-e az állapotkiértékelés során. Alapértelmezett érték: false (hamis).
* [MaxPercentUnhealthyDeployedApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications). Megadja az üzembe helyezett alkalmazások maximális tolerálható százalékát, amely az alkalmazás hibás nak minősül. Ezt a százalékot úgy számítja ki, hogy elosztja a nem megfelelő állapotú telepített alkalmazások számát azon csomópontok számával, amelyeken az alkalmazások jelenleg telepítve vannak a fürtben. A számítás kerekít, hogy tolerálja a kis számú csomópontok egy hiba. Alapértelmezett százalék: nulla.
* [DefaultServiceTypeHealthPolicy](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy). Megadja az alapértelmezett szolgáltatástípus állapotházirendet, amely az alkalmazás összes szolgáltatástípusának alapértelmezett állapotházirendje helyébe lép.
* [ServiceTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap). Szolgáltatástípusonkénti szolgáltatásállapot-házirendek leképezése. Ezek a házirendek minden megadott szolgáltatástípus alapértelmezett szolgáltatástípus-állapotházirendje helyébe lépnek. Ha például egy alkalmazás állapotnélküli átjárószolgáltatás-típussal és állapotalapú motorszolgáltatás-típussal rendelkezik, az állapotházirendeket eltérőmódon konfigurálhatja. Ha szolgáltatástípusonként adja meg a házirendet, részletesebb enciklést kaphat a szolgáltatás állapotának.

### <a name="service-type-health-policy"></a>Szolgáltatástípus állapotházirendje
A [szolgáltatástípus állapotházirendje](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy) meghatározza, hogyan kell kiértékelni és összesíteni a szolgáltatásokat és a szolgáltatások gyermekeit. A házirend a következőket tartalmazza:

* [MaxPercentUnhealthyPartitionsPerService](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice). Megadja a nem megfelelő állapotú partíciók maximális tolerálható százalékát, mielőtt egy szolgáltatás nem megfelelő állapotúnak minősülne. Alapértelmezett százalék: nulla.
* [MaxPercentUnhealthyReplicasPerPartition](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition). Megadja a nem megfelelő állapotú replikák maximális tolerálható százalékát, mielőtt egy partíció nem megfelelő állapotúnak minősülne. Alapértelmezett százalék: nulla.
* [MaxPercentUnhealthyServices](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices). Megadja a nem megfelelő állapotú szolgáltatások maximális tolerálható százalékát, mielőtt az alkalmazás nem megfelelő állapotúnak minősülne. Alapértelmezett százalék: nulla.

A következő példa egy részlet egy alkalmazásjegyzékből:

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

## <a name="health-evaluation"></a>Egészségügyi értékelés
A felhasználók és az automatizált szolgáltatások bármikor kiértékelhetik bármely entitás állapotát. Egy entitás állapotának kiértékeléséhez az állapottároló összesíti az entitásösszes állapotjelentését, és kiértékeli az összes gyermekét (adott esetben). Az állapotösszesítési algoritmus állapotházirendeket használ, amelyek meghatározzák, hogyan kell kiértékelni az állapotjelentéseket, és hogyan kell összesíteni a gyermek állapotait (adott esetben).

### <a name="health-report-aggregation"></a>Egészségügyi jelentés összesítése
Egy entitás rendelkezhet több állapotjelentés különböző riporterek (rendszer-összetevők vagy figyelők) különböző tulajdonságokon küldött. Az összesítés a kapcsolódó állapotházirendeket használja, különösen a ConsiderWarningAsError alkalmazás- vagy fürtállapot-házirendet. A ConsiderWarningAsError a figyelmeztetések kiértékelésének módját határozza meg.

Az összesített állapot az entitás *legrosszabb* állapotjelentései által aktiválódik. Ha legalább egy hibaállapot-jelentés, az összesített állapot egy hiba.

![Az állapotjelentés összesítése hibajelentéssel.][2]

Egy állapotentitás, amely egy vagy több hibaállapot-jelentést rendelkezik, hibaként lesz kiértékelve. Ugyanez igaz a lejárt állapotjelentés, függetlenül annak állapotát.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Ha nincsenek hibajelentések és egy vagy több figyelmeztetés, az összesített állapot figyelmeztetés vagy hiba, a ConsiderWarningAsError házirend-jelzőtől függően.

![Állapotjelentés összesítése figyelmeztető jelentéssel és ConsiderWarningAsError hamis.][3]

Állapotjelentés összesítése figyelmeztető jelentéssel és ConsiderWarningAsError beállítással hamis (alapértelmezett).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>A gyermekek egészségének összesítése
Az entitás összesített állapota tükrözi a gyermek állapotát (adott esetben). A gyermekállapot-állapotok összesítésének algoritmusa az entitástípus alapján alkalmazható állapotházirendeket használja.

![Gyermek entitások állapotának összesítése.][4]

Gyermekösszesítés az egészségügyi irányelvek alapján.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Miután az állapottároló értékelte az összes gyermeket, összesíti az állapotaikat a sérült gyermekek konfigurált maximális százaléka alapján. Ez a százalék az entitás és a gyermektípus alapján a házirendből származik.

* Ha minden gyermek rendben van, a gyermek összesített állapota rendben van.
* Ha a gyermekek is ok és figyelmeztetés i, a gyermek összesített állapot figyelmeztetés.
* Ha vannak olyan gyermekek, akiknek a hibaüzenete idotartamú állapotok nem tartják tiszteletben a nem megfelelő állapotú gyermekek maximális anamforát, az összesített szülőállapot hiba.
* Ha a hibás állapotú gyermekek tiszteletben tartják a nem megfelelő állapotú gyermekek maximálisan engedélyezett százalékát, az összesített szülőállapot figyelmeztetés.

## <a name="health-reporting"></a>Egészségügyi jelentések
A rendszerösszetevők, a System Fabric-alkalmazások és a belső/külső figyelők jelenthetik a Service Fabric-entitások. A riporterek *helyi* szinten állapítják meg a megfigyelt entitások állapotát, az általuk figyelemmel kísért körülmények alapján. Nem kell megvizsgálniuk semmilyen globális állapotot vagy összesített adatot. A kívánt viselkedés az, hogy egyszerű riporterek, és nem összetett szervezetek, hogy meg kell nézni sok mindent következtetni, hogy milyen információkat küldeni.

Az állapotadatok nak a health store-ba való elküldéséhez a jelentéskészítőnek azonosítania kell az érintett entitást, és létre kell hoznia egy állapotjelentést. A jelentés elküldéséhez használja a [FabricClient.Health.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) API-t, `Partition` jelentse `CodePackageActivationContext` az adott vagy objektumokon elérhető állapoti API-kat, a PowerShell-parancsmagokat vagy a REST-et.

### <a name="health-reports"></a>Egészségügyi jelentések
A fürt minden egyes entitásának [állapotjelentései](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthreport) a következő információkat tartalmazzák:

* **SourceId**. Olyan karakterlánc, amely egyedileg azonosítja az állapotesemény riporterét.
* **Entitásazonosító**. Azt az entitást azonosítja, amelyben a jelentést alkalmazzák. Az [entitás típusától](service-fabric-health-introduction.md#health-entities-and-hierarchy)függően különbözik:
  
  * Fürt. Nincs.
  * Csomópont. Csomópont neve (karakterlánc).
  * Alkalmazás. Alkalmazás neve (URI). A fürtben telepített alkalmazáspéldány nevét jelöli.
  * Szolgáltatás. Szolgáltatás neve (URI). A fürtben telepített szolgáltatáspéldány nevét jelöli.
  * Partíció. Partíció azonosítója (GUID). A partíció egyedi azonosítóját jelöli.
  * Replika. Az állapotalapú szolgáltatásreplikájának azonosítója vagy az állapotmentes szolgáltatáspéldány azonosítója (INT64).
  * Telepített alkalmazás. Alkalmazás neve (URI) és csomópontneve (karakterlánc).
  * DeployedServicePackage. Alkalmazásnév (URI), csomópontnév (karakterlánc) és szolgáltatásjegyzék-név (karakterlánc).
* **Ingatlan**. Olyan *karakterlánc* (nem rögzített felsorolás), amely lehetővé teszi, hogy az adatriporter kategorizálja az állapoteseményt az entitás egy adott tulajdonságához. Például a "A" jelentés készítője jelentheti a Node01 "Storage" tulajdonság állapotát, a "B" riporter pedig jelentheti a Node01 "Connectivity" tulajdonság állapotát. A health Store-ban ezeket a jelentéseket a Node01 entitás külön állapoteseményekként kezeli a program.
* **Leírás**. Olyan karakterlánc, amely lehetővé teszi, hogy egy adat-riporter részletes információkat szolgáltasson az állapoteseményről. **SourceId**, **Property**, és **HealthState** teljes mértékben le kell írnia a jelentést. A leírás a jelentéssel kapcsolatban ember által olvasható információkat ad hozzá. A szöveg megkönnyíti a rendszergazdák és a felhasználók számára az állapotjelentés megértését.
* **HealthState**. A jelentés állapotát leíró [felsorolás.](service-fabric-health-introduction.md#health-states) Az elfogadott értékek rendben vannak, figyelmeztetés és hiba.
* **TimeToLive**. Az állapotjelentés érvényességi idejét jelzi. Az **RemoveWhenExpired**funkcióval párosítva lehetővé teszi, hogy a health Store tudja, hogyan kell kiértékelni a lejárt eseményeket. Alapértelmezés szerint az érték végtelen, és a jelentés örökre érvényes.
* **RemoveWhenExpired**. Egy logikai. Ha értéke igaz, a lejárt állapotjelentés automatikusan törlődik a health Store, és a jelentés nem befolyásolja az entitás állapotának kiértékelését. Akkor használatos, ha a jelentés csak meghatározott ideig érvényes, és a jelentéskészítőnek nem kell explicit módon törölnie. Arra is használható, hogy törölje a jelentéseket az állapottárolóból (például egy figyelő módosul, és leállítja a korábbi forrással és tulajdonsággal rendelkező jelentések küldését). Küldhet egy jelentést egy rövid TimeToLive együtt RemoveWhenExpired törölje a korábbi állapotok a health Store. Ha az érték értéke hamis, a lejárt jelentés az állapotkiértékelés hibaként lesz kezelve. A hamis érték jelzi a health Store, hogy a forrás rendszeresen jelentést kell jelentenie a tulajdonság. Ha nem, akkor valami baj van a házőrzővel. A figyelő állapotát az esemény hibaként való figyelembevételével rögzíti.
* **SequenceNumber ( SequenceNumber**) ( Sorozatszám ) A pozitív egész szám, hogy kell egyre növekvő, ez jelenti a sorrendben a jelentések. A health store a hálózati késések vagy más problémák miatt késedelmesen fogadott elavult jelentések észlelésére szolgál. A jelentés elutasításra kerül, ha a sorszám kisebb vagy egyenlő, mint az ugyanazon entitás, forrás és tulajdonság legutóbb alkalmazott száma. Ha nincs megadva, a sorszám automatikusan jön létre. A sorszámot csak akkor kell beadni, ha az állapotátmenetekről számol be. Ebben a helyzetben a forrásnak emlékeznie kell arra, hogy mely jelentéseket küldte el, és meg kell őriznie a feladatátvételre vonatkozó adatokat.

Ez a négy információdarab – SourceId, entitásazonosító, tulajdonság és HealthState – minden állapotjelentéshez szükséges. A SourceId karakterlánc nem kezdheti el a "**System.**" előtagot, amely a rendszerjelentések számára van fenntartva. Ugyanahhoz az entitáshoz csak egy jelentés tartozik ugyanarra a forrásra és tulajdonságra. Ugyanaza forrás és tulajdonság több jelentése felülírja egymást, vagy az állapotügyfél-oldalon (ha kötegelt), vagy az állapottároló oldalán. A csere sorozatszámokon alapul; az újabb jelentések (nagyobb sorszámokkal) helyettesítik a régebbi jelentéseket.

### <a name="health-events"></a>Egészségügyi események
Belsőleg az állapottároló ban tárolja az [állapoteseményeket,](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthevent)amelyek tartalmazzák a jelentésekből származó összes információt és további metaadatokat. A metaadatok tartalmazzák a jelentés állapotügyfélnek való átadásának időpontját és a kiszolgálóoldali módosítás időpontját. Az állapoteseményeket [az állapotlekérdezések](service-fabric-view-entities-aggregated-health.md#health-queries)adják vissza.

A hozzáadott metaadatok a következőket tartalmazzák:

* **SourceUtcTimestamp**. A jelentés állapotügyfélnek (koordinált világidő) való eltöltésének időpontja.
* **LastModifiedUtcTimestamp**. A jelentés kiszolgálóoldali legutóbbi módosításának időpontja (koordinált világidő).
* **Lejárt**. Egy jelző, amely jelzi, hogy a jelentés lejárt-e, amikor a lekérdezést a health store végrehajtotta. Egy esemény csak akkor járt le, ha az RemoveWhenExpired értéke hamis. Ellenkező esetben az eseményt nem adja vissza a lekérdezés, és eltávolítja a tárolóból.
* **Lastoktransitionat**, **lastWarningTransitionat**, **Lasterrortransitionat**. Az OK/figyelmeztetés/hiba átmenetek utolsó időpontja. Ezek a mezők az esemény állapotátmeneteinek előzményeit adják meg.

Az állapotátmenet-mezők intelligensebb riasztásokhoz vagy "történelmi" állapotesemény-információkhoz használhatók. Olyan forgatókönyveket engedélyeznek, mint például:

* Riasztás, ha egy tulajdonság x percnél tovább volt figyelmeztetésben/hibaben. A feltétel egy ideig tartó ellenőrzése elkerüli az ideiglenes feltételekre vonatkozó riasztásokat. Például egy riasztást, ha az állapot már több mint öt percig volt figyelmeztetés lehet lefordítani (HealthState == Figyelmeztetés és most - LastWarningTransitionTime > 5 perc).
* Figyelmeztetés csak az elmúlt X percben megváltozott körülmények között. Ha egy jelentés a megadott időpont előtt már hiba volt, figyelmen kívül hagyható, mert korábban már jelzett.
* Ha egy tulajdonság a figyelmeztetés és a hiba között vált, határozza meg, hogy mennyi ideig volt kifogástalan (azaz nem OK). Például egy riasztást, ha a tulajdonság nem kifogástalan több mint öt percig lehet lefordítani (HealthState != Ok és Most - LastOkTransitionTime > 5 perc).

## <a name="example-report-and-evaluate-application-health"></a>Példa: Az alkalmazás állapotának jelentése és értékelése
A következő példa egy állapotjelentést küld a PowerShell-en keresztül az alkalmazás **háló:/WordCount** a forrásból **MyWatchdog.** Az állapotjelentés információkat tartalmaz a "rendelkezésre állás" állapotállapotú állapotról, végtelen TimeToLive állapotban. Ezután lekérdezi az alkalmazás állapotát, amely összesített állapothibákat és az állapotesemények listájában jelentett állapoteseményeket ad vissza.

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

## <a name="health-model-usage"></a>Állapotmodell használata
Az állapotmodell lehetővé teszi a felhőszolgáltatások és az alapul szolgáló Service Fabric platform méretezését, mivel a figyelési és állapot-meghatározások a fürt különböző figyelői között oszlanak meg.
Más rendszerek egyetlen, központi szolgáltatással rendelkeznek a fürt szintjén, amely elemzi a szolgáltatások által kibocsátott *összes potenciálisan* hasznos információt. Ez a megközelítés akadályozza a méretezhetőséget. Emellett nem teszi lehetővé számukra, hogy konkrét információkat gyűjtsenek a problémák és a lehetséges problémák lehető legközelebb imázsának azonosításához.

Az állapotmodell erősen használható a figyelés és a diagnózis, a fürt és az alkalmazás állapotának kiértékelésére, valamint a felügyelt frissítésekre. Más szolgáltatások az állapotadatok at automatikus javítások elvégzéséhez, a fürt állapotelőzményeinek létrehozásához és bizonyos feltételek esetén riasztások kiadásához használják.

## <a name="next-steps"></a>További lépések
[A Service Fabric állapotjelentésének megtekintése](service-fabric-view-entities-aggregated-health.md)

[Rendszerállapot-jelentések használata hibaelhárításhoz](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[A szolgáltatás állapotának jelentése és ellenőrzése](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Egyéni Service Fabric-állapotjelentések hozzáadása](service-fabric-report-health.md)

[A szolgáltatások helyi figyelése és diagnosztikája](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[A Service Fabric alkalmazásfrissítése](service-fabric-application-upgrade.md)

