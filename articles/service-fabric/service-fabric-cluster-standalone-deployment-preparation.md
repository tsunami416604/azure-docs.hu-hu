---
title: "Azure Service Fabric önálló fürt telepítési előkészítése |} Microsoft Docs"
description: "A környezet előkészítése és a fürt konfigurációjának, figyelembe kell venni a termelési alkalmazások és szolgáltatások kezelése szánt fürt üzembe helyezése előtt létrehozása kapcsolatos dokumentációját."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/12/2017
ms.author: dekapur;maburlik;chackdan
ms.openlocfilehash: b1190ec5a3ff70a368b29465699f9082d2b989bf
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
<a id="preparemachines"></a>

# <a name="plan-and-prepare-your-service-fabric-standalone-cluster-deployment"></a>Tervezze meg és készítse elő a Service Fabric önálló fürttelepítés
A fürt létrehozása előtt, hajtsa végre a következő lépéseket.

## <a name="plan-your-cluster-infrastructure"></a>A fürt-infrastruktúra megtervezése
Ön éppen készül a Service Fabric-fürt létrehozása a "saját" gépeken, eldöntheti, milyen típusú hibákat azt szeretné, a fürt a túlélést. Például tegye meg kell külön power vonalak vagy a hálózati kapcsolatok használata esetén az érintett számítógépekre megadott? Emellett vegye figyelembe ezeknek a gépeknek a fizikai biztonságot. A gépek helyét, és hozzáférést kell? Ezek a döntések után logikailag leképezheti a gépek különböző tartalék tartományok (lásd a következő lépés). Az infrastruktúra megtervezésének éles fürtök bonyolultabb, mint a tesztfürtökön.

## <a name="determine-the-number-of-fault-domains-and-upgrade-domains"></a>Tartalék tartományok számának meghatározásához és frissítési tartományt
A [ *tartalék tartomány* (FD)](service-fabric-cluster-resource-manager-cluster-description.md) egy fizikai egység a hiba, és közvetlenül kapcsolódik a fizikai infrastruktúra az adatközpontokban. A tartalék tartomány hardver összetevőből áll (számítógépek, kapcsolók, hálózatok és több), amelyek a hibaérzékeny pontok kialakulását. Bár a tartalék tartományok és állványok között nincs 1:1 leképezés, lazán beszéd, minden állványban tekinthető tartalék tartomány.

FDs művelet ad meg, beállíthatja az egyes FD nevét. A Service Fabric hierarchikus FDs támogatja, így a tükrözhetők az infrastruktúra-topológiát a bennük foglalt.  Például a következő FDs érvényesek:

* "faultDomain": "fd: / Room1/Rack1/gép1"
* "faultDomain": "fd: / FD1"
* "faultDomain": "fd: / Room1/Rack1/PDU1/M1"

Egy *frissítési tartomány* (UD) a csomópont egy logikai egységet. Frissítéskor a Service Fabric vezénylését (egy alkalmazás frissítése vagy a fürt frissítése) egy UD összes csomópontjának kerül a verziófrissítés közben egyéb UDs csomópontjának kérelem kiszolgálására elérhetők maradnak. A belső vezérlőprogram frissítési hajt végre a gépek nem fogadják el UDs, ezért tegye őket egy számítógép egyszerre.

A legegyszerűbben úgy gondolja, hogy ezek a fogalmak kapcsolatos FDs tekinti egység nem tervezett hiba UDs tervezett karbantartás egységként.

UDs művelet ad meg, beállíthatja az egyes UD nevét. Például az alábbi neveket érvényesek:

* "upgradeDomain": "UD0"
* "upgradeDomain": "UD1A"
* "upgradeDomain": "DomainRed"
* "upgradeDomain": "Blue"

Részletesebb információ a FDs és UDs, lásd: [a Service Fabric-fürt leíró](service-fabric-cluster-resource-manager-cluster-description.md).

A fürt éles környezetben kell span legalább három FDs ahhoz, hogy az éles környezet esetén nem támogatott, ha a karbantartási és kezelési csomópont teljes hozzáféréssel rendelkezik, azaz való telepítésért felelős frissítés és a gépek cseréje. A fürtök (azaz Amazon Web Services Virtuálisgép-példányok) környezetben fut, amelyhez nem rendelkezik teljes hozzáférés a gépeket a fürt legalább öt FDs kell rendelkeznie. Minden egyes FD egy vagy több csomópont is rendelkezhetnek. Ez a gép frissítések és verziófrissítések, amelyek attól függően, hogy azok időzítése is az alkalmazások és szolgáltatások fürtök futtató intefere által okozott problémák megelőzése érdekében.

## <a name="determine-the-initial-cluster-size"></a>A fürtcsomópontok kezdeti mérete határozza meg

Általában a fürtben található csomópontok számát határozza meg a üzleti igények alapján, azaz, hogy hány szolgáltatások és a tárolók fog futni a fürtön, és hány erőforrások van szüksége a terhelés fenntartása érdekében. Az éles fürtök esetén azt ajánljuk legalább 5 csomópontok a fürt 5 FDs átfedés. Azonban a fent leírtaknak megfelelően, ha a csomópontok teljes hozzáféréssel rendelkeznek, és három FDs is kiterjedhet három csomópontot kell is tegye a feladatot.

Állapotalapú alkalmazások és szolgáltatások futtatása tesztfürtökön kell három csomópontot, mivel csak a csak az állapot nélküli munkaterheléseket futó tesztfürtökön egy csomópont. Azt is megjegyezni, hogy fejlesztési célra lehet egynél több csomópont az adott számítógépen. Éles környezetben azonban a Service Fabric támogatja az egyes fizikai vagy virtuális gép csak egy csomópont.

## <a name="prepare-the-machines-that-will-serve-as-nodes"></a>Készítse elő a gépek csomópontok erre a célra

Az alábbiakban néhány javasolt specifikációk a fürthöz hozzáadni kívánt minden egyes géphez:

* Egy legalább 16 GB RAM
* Legalább 40 GB szabad lemezterület
* A 4 mag, vagy nagyobb Processzor
* A biztonságos hálózati és az összes gép hálózati kapcsolat
* Windows Server 2012 R2 vagy Windows Server 2016
* [.NET-keretrendszer 4.5.1-es vagy újabb](https://www.microsoft.com/download/details.aspx?id=40773), teljes telepítése
* [A Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/setup/installing-windows-powershell)
* A [RemoteRegistry szolgáltatás](https://technet.microsoft.com/library/cc754820) a gépen kell futnia

Rendelkeznie kell a fürt rendszergazdája telepítése és konfigurálása a fürt [rendszergazdai jogosultságokkal](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) minden gépen. A Service Fabric tartományvezérlőn nem telepíthető.

## <a name="download-the-service-fabric-standalone-package-for-windows-server"></a>A Windows Server a Service Fabric önálló csomag letöltése
[Töltse le a Windows Server - szolgáltatás háló önálló csomag - hivatkozás](http://go.microsoft.com/fwlink/?LinkId=730690) és bontsa ki a csomagot, vagy a központi telepítés gépekhez, amely nem része a fürtnek, vagy egy, a gép, amely a fürt része lesz.

## <a name="modify-cluster-configuration"></a>Fürt konfigurációjának módosítása
Önálló fürt létrehozása kell létrehoznia egy önálló fürtön konfigurációs művelet fájlt, amely a fürt megadását ismerteti. A sablonok a következő címen található a konfigurációs fájlt létrehozhatja a hivatkozáson. <br>
[Önálló fürtkonfigurációk](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

Ez a fájl szakaszaiban a részletekért lásd: [önálló Windows-fürt konfigurációs beállításainak](service-fabric-cluster-manifest.md).

Nyisson meg egy művelet fájlt a letöltött csomagban található, és módosítsa a következő beállításokat:
| **Konfigurációs beállítás** | **Leírás** |
| --- | --- |
| **A NodeType tulajdonságok értéke** |Csomóponttípusok csoportokba a fürtcsomópontok különböző teszik lehetővé. A fürt legalább egy NodeType kell rendelkeznie. A csoport összes csomópontja a következő közös jellemzőkkel rendelkezik: <br> **Név** -típusú csomópont neve. <br>**Végpontportokat** – ezek különböző megnevezett végpontok (portok) Ez a csomóponttípus társított. A portszám, amely kívánja, mindaddig, amíg azok nem ütköznek a bármi más, a jegyzékfájlban, és nem már használatban van a számítógép vagy virtuális gépen más alkalmazás is használhatja. <br> **Elhelyezési tulajdonságok** -ezek tulajdonságai csomópont, amely korlátozza, a rendszer szolgáltatások vagy a szolgáltatások használatát ismerteti. Ezek a Tulajdonságok felhasználó által definiált kulcs/érték párok, adja meg egy adott csomópont extra metaadatai. Csomópont tulajdonságai példái lenne, rendelkezik-e a csomópont a merevlemez-meghajtóra vagy grafikus kártya, a merevlemez-meghajtóról, maggal és más fizikai tulajdonságok forgórészek száma. <br> **Kapacitások** -csomópont-kapacitás határozza meg, a név és egy adott erőforrás méretét, hogy egy adott csomópont felhasználható rendelkezik-e. Meghatározhatja például, egy csomópont lehet, hogy rendelkezik-e olyan metrikajelentés "MemoryInMb" nevű kapacitás, és arról, hogy rendelkezik-e elérhető 2048 MB-ot alapértelmezés szerint. A kapacitás segítségével futásidőben győződjön meg arról, hogy az adott mennyiségű erőforrást igénylő szolgáltatások kerülnek-e a csomópontokon, amely rendelkezik a szükséges mennyiségű elérhető erőforrások.<br>**IsPrimary** – Ha győződjön meg arról, hogy csak az egyik van állítva az elsődleges értékű definiált egynél több NodeType *igaz*, vagyis ha a rendszer szolgáltatások futtatása. Minden más csomóponttípusok értékre kell állítani *hamis* |
| **Csomópontok** |Ezek a részletes adatainak megadása (típusú csomópont, a csomópont neve, IP-cím, tartalék tartomány és a csomópont frissítési tartomány) a fürt részét képező csomópontjain. A gépek azt szeretné, ebben a listában szerepel az IP-címét kell létrehozni a fürtöt. <br> Összes csomópontjának azonos IP-címét használja, ha majd egy egy beépített fürt létrehozása, amely tesztelési célokra használható. Ne használjon egy beépített fürtök üzembe helyezése a termelési számítási feladatokhoz. |

Miután a fürt konfigurálásának volt a környezet összes beállítást, az tesztelhető szemben a fürtözött környezet (7. lépés).

<a id="environmentsetup"></a>

## <a name="environment-setup"></a>A környezet beállítása

Amikor a fürt rendszergazdája konfigurálja a különálló Service Fabric-fürt, a következő feltételeket kell megadnia beállítása kell azt a környezetet: <br>
1. A fürtöt létrehozó felhasználónak rendszergazdai biztonsági jogosultságokkal a fürt konfigurációs fájlban lévő csomópontok felsorolt összes gépen kell rendelkeznie.
2. Gépek, amelyből létrehozza a fürtöt, valamint minden fürt csomópont gép kell:
* El kell távolítani a Service Fabric SDK
* Rendelkezik a Service Fabric-futtatókörnyezet eltávolítása 
* A Windows tűzfalszolgáltatást (mpssvc) engedélyezve van
* A távoli beállításjegyzék szolgáltatás (remoteregistry) engedélyezve van
* Fájl megosztási (SMB) engedélyezve van
* Rendelkezik a szükséges portok nyitva, a fürt konfigurációs portok alapján
* Rendelkezik a Windows az SMB és a távoli beállításjegyzék szolgáltatás számára szükséges portok: 135-ös, 137, 138, 139 és 445-ös
* Egy másik hálózati kapcsolattal rendelkezik
3. A fürt csomópont gépek egyike kell tartományvezérlőnek lennie.
4. Ha telepíteni a fürt egy biztonságos fürt, ellenőrzi a szükséges biztonsági előfeltételeket helyezze el, és megfelelően van konfigurálva a konfiguráció alapján.
5. Ha a fürtbeli gépeken nincsenek internetről elérhető, állítsa be a következőket a fürtkonfiguráció:
* Telemetria letiltása: az *tulajdonságok* beállítása *"enableTelemetry": hamis*
* Tiltsa le az automatikus háló verzió letöltése & értesítéseket, hogy a fürt verziószámának közelít végéhez: alatt *tulajdonságok* beállítása *"fabricClusterAutoupgradeEnabled": hamis*
* Azt is megteheti, ha hálózati internet-hozzáférés fehér felsorolt tartományok korlátozódik, az alábbi tartományok szükség az automatikus frissítés: go.microsoft.com jövőben a Microsoft

6. Állítsa be a megfelelő Service Fabric víruskereső kizárásokat:

| **Víruskereső kizárt könyvtárak** |
| --- |
| Program Files\Microsoft a Service Fabric |
| A FabricDataRoot (a fürtkonfiguráció) |
| (A fürtkonfiguráció) fabriclogroot mappában |

| **Víruskereső kizárt folyamatok** |
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
| FileStoreService.exe |

## <a name="validate-environment-using-testconfiguration-script"></a>Ellenőrizze a környezet TestConfiguration parancsfájl használatával
A TestConfiguration.ps1 parancsfájl az önálló csomagban található. Ellenőrzése a fenti feltételek közül egy ajánlott eljárásokat elemző eszköz szolgál, és megerősítést ellenőrzése ellenőrzése, hogy a fürt telepíthető egy adott környezetben kell használni. Ha bármilyen hiba, tekintse meg a lista [környezetben való telepítés](service-fabric-cluster-standalone-deployment-preparation.md) hibaelhárításhoz. 

Ezt a parancsfájlt, amely csomópontként a fürt konfigurációs fájlban felsorolt minden gép rendszergazdai hozzáféréssel rendelkezik gépi. Az ezt a parancsfájlt futtató számítógép nem rendelkezik a fürt részeként.

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

Jelenleg ez a konfiguráció tesztelési modul nem felel meg a biztonsági beállítások, egymástól függetlenül végezhető azt.  

> [!NOTE]
> Folyamatosan fejlesztjük Ez a modul robusztusabb, létrehozásához, ha egy hibás vagy hiányzó esetben úgy gondolja, amely nem TestConfiguration által jelenleg észlelt, kérjük, tájékoztasson keresztül a [támogatnak](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).   
> 
> 

## <a name="next-steps"></a>Következő lépések
* [A Windows Server rendszert futtató önálló fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md)
