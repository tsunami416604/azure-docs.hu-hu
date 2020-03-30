---
title: Önálló fürttelepítés előkészítése
description: A környezet előkészítésével és a fürtkonfiguráció létrehozásával kapcsolatos dokumentációt figyelembe kell venni az éles számítási feladatok kezelésére szánt fürt üzembe helyezése előtt.
author: dkkapur
ms.topic: conceptual
ms.date: 9/11/2018
ms.author: dekapur
ms.openlocfilehash: 6a00b7d1b72d594c08021982b2448de6275414c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610063"
---
# <a name="plan-and-prepare-your-service-fabric-standalone-cluster-deployment"></a>A Service Fabric önálló fürt telepítésének megtervezése és előkészítése

<a id="preparemachines"></a>A fürt létrehozása előtt hajtsa végre az alábbi lépéseket.

## <a name="plan-your-cluster-infrastructure"></a>A fürtinfrastruktúra megtervezése
A "saját" gépeken létre kell hoznia egy Service Fabric-fürtöt, így eldöntheti, hogy milyen típusú hibákat szeretne a fürt fennmaradni. Például külön elektromos vezetékekre vagy internetkapcsolatra van szüksége ezekhez a gépekhez? Ezen túlmenően, fontolja meg a fizikai biztonságát ezek a gépek. Hol találhatók a gépek, és kinek van szüksége hozzáférésre? Miután ezeket a döntéseket, logikailag leképezheti a gépeket a különböző tartalék tartományok (lásd a következő lépés). Az éles fürtök infrastruktúra-tervezése nagyobb szerepet vesz fel, mint a tesztfürtök.

## <a name="determine-the-number-of-fault-domains-and-upgrade-domains"></a>A tartalék tartományok és a frissítési tartományok számának meghatározása
A [ *tartalék tartomány* (FD)](service-fabric-cluster-resource-manager-cluster-description.md) a hiba fizikai egysége, és közvetlenül kapcsolódik az adatközpontok fizikai infrastruktúrájához. A tartalék tartomány olyan hardverösszetevőkből (számítógépekből, kapcsolókból, hálózatokból stb.) áll, amelyek egyetlen meghibásodási ponttal rendelkeznek. Bár a tartalék tartományok és állványok között nincs 1:1-es hozzárendelés, lazán szólva, minden állvány tartalék tartománynak tekinthető.

Ha fd-ket ad meg a ClusterConfig.json fájlban, kiválaszthatja az egyes FD-k nevét. A Service Fabric támogatja a hierarchikus FD-ket, így tükrözheti az infrastruktúra topológiáját.  Például a következő FD-k érvényesek:

* "faultDomain": "fd:/Room1/Rack1/Machine1"
* "faultDomain": "fd:/FD1"
* "faultDomain": "fd:/Room1/Rack1/PDU1/M1"

A *frissítési tartomány* (UD) a csomópontok logikai egysége. A Service Fabric által rendezett frissítések (vagy egy alkalmazás-frissítés vagy egy fürt frissítése) során az UD összes csomópontja lelesz választva a frissítés végrehajtásához, míg más UD-k csomópontjai továbbra is elérhetők maradnak a kérelmek kiszolgálásához. A gépeken végrehajtott firmware-frissítések nem tartják tiszteletben az UD-kat, ezért egyszerre csak egy géppel kell elvégeznie őket.

A legegyszerűbb módja annak, hogy úgy gondolja, ezek a fogalmak, hogy fontolja meg FDs, mint az egység a nem tervezett hiba és az UD-k, mint az egység a tervezett karbantartás.

Ha ud-kat ad meg a ClusterConfig.json fájlban, kiválaszthatja az egyes UD-k nevét. Például a következő nevek érvényesek:

* "upgradeDomain": "UD0"
* "upgradeDomain": "UD1A"
* "upgradeDomain": "DomainRed"
* "upgradeDomain": "Kék"

Az FD-kről és az UD-król a [Service Fabric-fürt leírása](service-fabric-cluster-resource-manager-cluster-description.md)című témakörben talál további információt.

Az éles környezetben lévő fürtnek legalább három FD-re kell kiterjednie ahhoz, hogy éles környezetben is támogatott legyen, ha teljes mértékben ellenőrzése alatt tart a csomópontok karbantartása és kezelése, azaz Ön felelős a gépek frissítéséért és cseréjéért. Olyan környezetekben (azaz Amazon Web Services virtuálisgép-példányokban) futó fürtök esetén, ahol nem rendelkezik teljes hozzáféréssel a gépek felett, legalább öt FD-vel kell rendelkeznie a fürtben. Minden FD-nek lehet egy vagy több csomópontja. Ez megakadályozza a számítógép-frissítések és -frissítések által okozott problémákat, amelyek időzítésüktől függően zavarhatják az alkalmazások és szolgáltatások fürtökben való futtatását.

## <a name="determine-the-initial-cluster-size"></a>A kezdeti fürtméret meghatározása

Általában a csomópontok száma a fürtben határozza meg az üzleti igények, azaz hány szolgáltatások és tárolók fog futni a fürtön, és hány erőforrásra van szüksége a számítási feladatok fenntartásához. Éles fürtök esetén azt javasoljuk, hogy legalább öt csomópont ot a fürtben, amely 5 FDs.For production clusters, javasoljuk, hogy legalább öt csomópont a fürtben, amely 5 FDs.For production clusters, javasoljuk, hogy legalább öt csomópont a fürtben, amely 5 FDs.For production clusters, javasoljuk, hogy legalább öt csomópont a fürtben, amely 5 Azonban a fent leírtak szerint, ha teljes mértékben ellenőrzése alatt a csomópontok, és három FD-k, majd három csomópont is ezt a munkát.

Az állapotalapú számítási feladatokat futtató tesztfürtöknek három csomóponttal kell rendelkezniük, míg a csak állapot nélküli számítási feladatokat futtató tesztfürtöknek csak egy csomópontra van szükségük. Azt is meg kell jegyezni, hogy fejlesztési célokra egy adott gépen egynél több csomópont is lehet. Éles környezetben azonban a Service Fabric fizikai vagy virtuális gépenként csak egy csomópontot támogat.

## <a name="prepare-the-machines-that-will-serve-as-nodes"></a>Készítse elő a csomópontokként szolgáló gépeket

Az alábbiakban néhány ajánlott specifikációt olvashat a fürthöz hozzáadni kívánt gépekhez:

* Legalább 16 GB RAM
* Legalább 40 GB szabad lemezterület
* 4 magos vagy nagyobb CPU
* Biztonságos hálózathoz vagy hálózatokhoz való csatlakozás minden gép számára
* A Windows Server operációs rendszer telepítve van (érvényes verzió: 2012 R2, 2016, 1709 vagy 1803). A Service Fabric 6.4.654.9590-es és újabb verziója támogatja a Server 2019 és 1809 kiszolgálót is.
* [.NET Framework 4.5.1 vagy újabb,](https://www.microsoft.com/download/details.aspx?id=40773)teljes telepítés
* [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/install/installing-windows-powershell)
* A [RemoteRegistry szolgáltatásnak](https://technet.microsoft.com/library/cc754820) az összes gépen futnia kell
* A Service Fabric telepítőmeghajtójának NTFS fájlrendszernek kell lennie

A fürtöt üzembe helyező és konfiguráló fürtrendszergazdának [rendszergazdai jogosultságokkal](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) kell rendelkeznie az egyes gépeken. A Service Fabric tartományvezérlőn nem telepíthető.

## <a name="download-the-service-fabric-standalone-package-for-windows-server"></a>A Service Fabric önálló csomagjának letöltése Windows Server rendszerhez
[Letöltési hivatkozás – Service Fabric önálló csomag – Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690) és csomagolja ki a csomagot, vagy egy központi gép, amely nem része a fürtnek, vagy az egyik gép, amely része lesz a fürtnek.

## <a name="modify-cluster-configuration"></a>Fürtkonfiguráció módosítása
Önálló fürt létrehozásához létre kell hoznia egy önálló fürtkonfigurációfürtfürtConfig.json fájlt, amely leírja a fürt specifikációját. A konfigurációs fájlt az alábbi hivatkozáson található sablonokra alapozhatja. <br>
[Önálló fürtkonfigurációk](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

A fájl szakaszairól az önálló [Windows-fürt konfigurációs beállításai](service-fabric-cluster-manifest.md)című témakörben olvashat részletesen.

Nyissa meg a letöltött csomag fürtconfig.json fájljainak egyikét, és módosítsa a következő beállításokat:

| **Konfigurációs beállítás** | **Leírás** |
| --- | --- |
| **NodeTypes (Csomóponttípusok)** |A csomóponttípusok lehetővé teszik a fürtcsomópontok különböző csoportokra való elkülönítését. A fürtnek legalább egy NodeType típussal kell rendelkeznie. A csoport összes csomópontja a következő közös jellemzőkkel rendelkezik: <br> **Name** - Ez a csomópont típusneve. <br>**Végponti portok** – Ezek különböző elnevezett végpontok (portok), amelyek ehhez a csomóponttípushoz vannak társítva. Bármilyen portszámot használhat, feltéve, hogy azok nem ütköznek semmi mással ebben a jegyzékfájlban, és még nem használják a gépen/virtuális gépen futó más alkalmazások. <br> **Elhelyezési tulajdonságok** – Ezek a csomóponttípus tulajdonságait írják le, amelyeket a rendszerszolgáltatások vagy a szolgáltatások elhelyezési kényszereként használ. Ezek a tulajdonságok a felhasználó által definiált kulcs/érték párok, amelyek további metaadatokat biztosítanak egy adott csomóponthoz. A csomópont tulajdonságai közé lehet például az, hogy a csomópont rendelkezik-e merevlemez-meghajtóval vagy grafikus kártyával, a merevlemezben, a magokban és más fizikai tulajdonságokban lévő orsók száma. <br> **Kapacitások** – A csomópontkapacitások határozzák meg egy adott erőforrás nevét és mennyiségét, amelyet egy adott csomópont rendelkezésre bocsát fogyasztásra. Egy csomópont például meghatározhatja, hogy kapacitása van-e a "MemoryInMb" nevű metrikához, és alapértelmezés szerint 2048 MB-tal rendelkezik.For example, a node may define that it has capacity for a metric called "MemoryInMb", and that it has it has 2048 MB available by default. Ezeket a kapacitásokat futásidőben használják annak biztosítására, hogy a meghatározott erőforrásokat igénylő szolgáltatások azokon a csomópontokon legyenek elhelyezve, amelyek rendelkeznek a szükséges erőforrásokkal.<br>**IsPrimary** - Ha egynél több NodeType definiálva van, győződjön meg arról, hogy csak egy van beállítva elsődleges értékkel az *igaz*értékkel, amely a rendszerszolgáltatások futtatásának helye. Az összes többi csomóponttípust hamis értékre kell *állítani.* |
| **Csomópontok** |Ezek a fürt részét szolgáló csomópontok (csomóponttípus, csomópontnév, IP-cím, tartaléktartomány és a csomópont frissítési tartománya) részletei. A fürt létrehozásához szükséges gépeket itt kell felsorolni az IP-címükkel együtt. <br> Ha ugyanazt az IP-címet használja az összes csomóponthoz, akkor létrejön egy egydobozos fürt, amelyet tesztelési célokra használhat. Ne használjon egydobozos fürtöket éles számítási feladatok üzembe helyezéséhez. |

Miután a fürtkonfiguráció minden beállítását beállította a környezethez, tesztelhető a fürtkörnyezettel (7. lépés).

<a id="environmentsetup"></a>

## <a name="environment-setup"></a>A környezet beállítása

Amikor egy fürtrendszergazda konfigurál egy service fabric önálló fürtöt, a környezetet a következő feltételekkel kell beállítani: <br>
1. A fürtöt létrehozó felhasználónak rendszergazdai szintű biztonsági jogosultságokkal kell rendelkeznie a fürt konfigurációs fájljában csomópontként felsorolt összes géphez.
2. Az a gép, amelyből a fürt létrejön, valamint minden fürtcsomópont-gépnek:
   * A Service Fabric SDK eltávolítása
   * A Service Fabric futásidejű eltávolítása
   * A Windows tűzfal szolgáltatás (mpssvc) engedélyezése
   * A távoli beállításjegyzék-szolgáltatás (távoli beállításjegyzék) engedélyezése
   * Engedélyezve van a fájlmegosztás (SMB)
   * A szükséges portok megnyitása a fürtkonfigurációs portok alapján
   * A Windows SMB és a Távoli beállításjegyzék szolgáltatáshoz szükséges portok megnyitása: 135, 137, 138, 139 és 445
   * Hálózati kapcsolat egymással
3. A fürtcsomópont-gépek egyike sem lehet tartományvezérlő.
4. Ha az üzembe helyezendő fürt biztonságos fürt, ellenőrizze a szükséges biztonsági előfeltételeket, és megfelelően vannak konfigurálva a konfigurációval szemben.
5. Ha a fürtgépek nem érhetők el az interneten, állítsa be a következőket a fürtkonfigurációban:
   * Telemetriai adatok letiltása: Az *"enableTelemetry"* *tulajdonságkészletalatt:* false
   * A Fabric automatikus verziójának letiltása & értesítések letöltése, hogy az aktuális fürtverzió a támogatás végéhez közeledik: A *"fabricClusterAutoupgradeEnabled"* *tulajdonságkészlet* alatt: false
   * Ha a hálózati internet-hozzáférés csak a fehérlistán szereplő tartományokra korlátozódik, az alábbi tartományok szükségesek az automatikus frissítéshez: go.microsoft.com download.microsoft.com

6. Állítsa be a megfelelő Service Fabric-víruskizárásokat:

| **Víruskereső kizárt könyvtárak** |
| --- |
| Programfájlok\Microsoft Service Fabric |
| FabricDataRoot (fürtkonfigurációból) |
| FabricLogRoot (fürtkonfigurációból) |

| **Vírusvédelem kizárt folyamatai** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe fájl |

## <a name="validate-environment-using-testconfiguration-script"></a>Környezet ellenőrzése testkonfigurációs parancsfájllal
A TestConfiguration.ps1 parancsfájl megtalálható az önálló csomagban. Ajánlott eljárások atalizátorként használják a fenti feltételek egy részének érvényesítéséhez, és józansági ellenőrzésként kell használni annak ellenőrzéséhez, hogy egy fürt telepíthető-e egy adott környezetben. Ha hiba történt, olvassa el a [Környezeti telepítés](service-fabric-cluster-standalone-deployment-preparation.md) című témakört hibaelhárításért.

Ez a parancsfájl bármely olyan számítógépen futtatható, amely rendszergazdai hozzáféréssel rendelkezik a fürt konfigurációs fájljában csomópontként felsorolt összes géphez. A parancsfájl futtatásához szükséges gépnek nem kell a fürt részét vennie.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 : True
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
Passed                     : True
```

Jelenleg ez a konfigurációs tesztelési modul nem ellenőrzi a biztonsági konfigurációt, ezért ezt egymástól függetlenül kell elvégezni.

> [!NOTE]
> Folyamatosan fejlesztéseket hajtunk végre, hogy ez a modul robusztusabb legyen, így ha van egy hibás vagy hiányzó eset, amely ről úgy gondolja, hogy jelenleg nem fogott a TestConfiguration, kérjük, tudassa velünk [támogatási csatornáinkon](https://docs.microsoft.com/azure/service-fabric/service-fabric-support)keresztül .
>
>

## <a name="next-steps"></a>További lépések
* [Windows Serveren futó különálló fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md)
