---
title: Az Azure Service Fabric önálló fürt üzembe helyezés előkészítése |} A Microsoft Docs
description: A környezet előkészítése és a egy éles számítási feladatok kezelésére szánt-fürt üzembe helyezése előtt figyelembe kell venni a fürtkonfiguráció létrehozása kapcsolódó dokumentációt.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/11/2018
ms.author: dekapur
ms.openlocfilehash: 0b6fae59fbe0fa86cb16b176eb1df47e031d04f1
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53317192"
---
# <a name="plan-and-prepare-your-service-fabric-standalone-cluster-deployment"></a>Megtervezheti és előkészítheti a Service Fabric önálló fürtök üzembe helyezése

<a id="preparemachines"></a>Az alábbi lépések végrehajtásával, a fürt létrehozása előtt.

## <a name="plan-your-cluster-infrastructure"></a>A fürt-infrastruktúra megtervezése
Ön egy Service Fabric-fürtöt létrehozni a gépeken "saját", így eldöntheti, milyen típusú hibák azt szeretné, a fürt stabilitást biztosít. Például tegye meg kell külön power vonalak vagy hálózati kapcsolatok használata esetén ezek a gépek megadott? Ezenkívül fontolja meg ezek a gépek fizikai biztonságát. Hol találhatók a gépek, és ki kell őket a hozzáférést? Ezek a döntések után logikailag leképezheti a gépek különböző tartalék tartományokra (lásd a következő lépés). Az infrastruktúra tervezésének éles fürtök esetén a bonyolultabb, mint a tesztfürtök esetében.

## <a name="determine-the-number-of-fault-domains-and-upgrade-domains"></a>Tartalék tartományok számának meghatározása és a frissítési tartományok
A [ *tartalék tartomány* (FD)](service-fabric-cluster-resource-manager-cluster-description.md) fizikai egységek hiba, és közvetlenül kapcsolódik az Adatközpont fizikai infrastruktúráját. Tartalék tartomány áll hardverösszetevők (számítógépek, kapcsolók, hálózatok és egyéb), amelyek egy meghibásodási pont. Bár a tartalék tartományok és állványok közötti nincs 1:1 leképezés, lazán beszélni, minden állványban lehessen venni egy tartalék tartományt.

Tartalék tartományok között ClusterConfig.json ad meg, ha minden egyes FD neve választhat. A Service Fabric támogatja a hierarchikus tartalék tartományok között, így az infrastruktúra-topológiát bennük tükrözhetők.  Ha például a következő tartalék érvényesek:

* "faultDomain": "fd: / Room1/Rack1/Machine1"
* "faultDomain": "fd: / FD1"
* "faultDomain": "fd: / Room1/Rack1/PDU1/M1"

Egy *frissítési tartomány* (UD) egy logikai egységet a csomópontok. A Service Fabric vezényelt rendszerre (egy alkalmazás frissítése vagy a fürt frissítése) egy UD minden csomópontján csomópontja más frissítési tartománnyal továbbra is elérhetők kiszolgálni a kérelmeket a frissítés végrehajtásához álló alkotásait. A belső vezérlőprogram-frissítéseket, a gépek hajt végre nem fogadja el a frissítési tartománnyal, ezért meg kell tennie őket egy gépi egyszerre.

A legegyszerűbben úgy gondolja, hogy ezekkel a fogalmakkal, hogy fontolja meg a tartalék tartományok között tervezett karbantartás egységet nem tervezett hibák és frissítési tartománnyal egységet.

Frissítési tartománnyal ClusterConfig.json ad meg, amikor kiválaszthatja az egyes UD nevét. Ha például a következő neveket érvényesek:

* "upgradeDomain": "UD0"
* "upgradeDomain": "UD1A"
* "upgradeDomain": "DomainRed"
* "upgradeDomain": "Blue"

További részletes információ a tartalék és frissítési tartománnyal,: [leíró, Service Fabric-fürt](service-fabric-cluster-resource-manager-cluster-description.md).

Egy éles fürtöt kell span legalább három tartalék tartományok között annak érdekében, hogy a támogatott az éles környezetben, ha a karbantartási és a felügyeleti csomópont feletti teljes hozzáféréssel rendelkezik, vagyis Ön felelős frissítése, és cserélje le a gépek. Környezetekben (azt jelenti, az Amazon Web Services Virtuálisgép-példányok) fut, ha nem rendelkezik teljes hozzáféréssel a gépek fürtök esetén a fürt kell rendelkeznie legalább öt tartalék tartományok között. Minden egyes FD rendelkezhet egy vagy több csomópontot. Ez a gép frissítések és verziófrissítések, amelyek attól függően, hogy időzítési megzavarhatja a futó alkalmazások és szolgáltatások a fürtök által okozott problémák elkerülése érdekében.

## <a name="determine-the-initial-cluster-size"></a>A kezdeti csomópont mérete határozza meg

Általában a fürtben található csomópontok számát határozza meg, amely üzleti igényeinek, hány szolgáltatások és a tárolók fog futni a fürtön, és hány erőforrások van szüksége a számítási feladatok fenntartására. Éles fürtök esetén célszerű megadnia, hogy legalább öt csomópont a fürt 5 tartalék átfedés. Azonban a fentiekben ismertetettek szerint, ha a csomópontok teljes körű irányítását, és három tartalék tartományok között is kiterjedhet majd három csomópont is érdemes megtenni a feladatot.

Állapotalapú alkalmazások és szolgáltatások futtatása tesztfürtök kell három csomópont van, mivel csak a futó állapot nélküli munkaterhelés csak tesztelési fürtökhöz szükséges egy csomópontot. Emellett Megjegyzendő, hogy fejlesztési célokra, rendelkezhet több csomópont az adott számítógépen. Éles környezetben azonban a Service Fabric támogatja a fizikai vagy virtuális gép csak egy csomópontonkénti.

## <a name="prepare-the-machines-that-will-serve-as-nodes"></a>Készítse elő a gépek csomópontok erre a célra

Az alábbiakban néhány javasolt adatait tartalmazza az egyes gépek, a fürthöz hozzáadni kívánt:

* Egy legalább 16 GB RAM
* Legalább 40 GB szabad lemezterület
* Egy 4 mag, vagy nagyobb CPU
* Kapcsolat egy biztonságos hálózati vagy a hálózatok minden gép
* A Windows Server operációs rendszer telepítve van (érvényes verzió: A 2012 R2, a 2016, a 1709-es vagy a 1803)
* [.NET-keretrendszer 4.5.1-es vagy újabb](https://www.microsoft.com/download/details.aspx?id=40773), teljes verzióként
* [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/setup/installing-windows-powershell)
* A [RemoteRegistry szolgáltatás](https://technet.microsoft.com/library/cc754820) minden gépen kell futnia

Rendelkeznie kell a fürt rendszergazdája központi telepítését és konfigurálását a fürt [rendszergazdai jogosultságokkal](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) az egyes gépek. A Service Fabric tartományvezérlőn nem telepíthető.

## <a name="download-the-service-fabric-standalone-package-for-windows-server"></a>Töltse le a Service Fabric különálló csomag a Windows Server
[Töltse le a Windows Server - Service Fabric önálló csomag - hivatkozás](https://go.microsoft.com/fwlink/?LinkId=730690) , és tömörítse ki a csomagot, vagy egy üzembe helyezési géphez, amely nem része a fürtnek, vagy egy, a gép, amely egy fürt tagja lesz.

## <a name="modify-cluster-configuration"></a>Fürt konfigurációjának módosítása
Önálló fürt létrehozása akkor hozzon létre egy önálló fürt konfigurációs ClusterConfig.json fájlt, amely a fürt meghatározását ismerteti. A sablonokkal címen található a konfigurációs fájl alapján is az alábbi hivatkozásra. <br>
[Önálló fürtkonfiguráció](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

A szakaszok ezt a fájlt a részletekért lásd: [különálló Windows-fürt konfigurációs beállításainak](service-fabric-cluster-manifest.md).

Nyissa meg a letöltött csomag egy ClusterConfig.json fájlt, és módosítsa az alábbi beállításokat:

| **Konfigurációs beállítás** | **Leírás** |
| --- | --- |
| **NodeType** |A csomóponttípusok lehetővé teszik különböző csoportokba rendezheti a fürtcsomópontokat. A fürt legalább egy NodeType csomóponttípust kell rendelkeznie. Egy csoport összes csomópontja a következő közös jellemzőkkel rendelkeznek: <br> **Név** – Ez az a csomóponttípus neve. <br>**Végpontportokat** – ezek a különböző nevesített végpontok (portot képes) ennek a csomóponttípusnak társított. Használhat bármilyen szeretne, mindaddig, amíg azok nem ütköznek a bármi más, a jegyzékfájlban és még nem más, a számítógép vagy virtuális gépen futó alkalmazás által használt port számát. <br> **Elhelyezési tulajdonságok** – ezek a helyrendszeri szolgáltatások vagy a szolgáltatások elhelyezési korlátozások használni csomópont típus tulajdonságait írják le. Ezek a tulajdonságok akkor a felhasználó által definiált kulcs-érték párok, amelyek további metaadatok biztosítanak egy adott csomópont. Példák a csomópont tulajdonságait lehet rendelkezik-e a csomópont a merevlemez-meghajtóra vagy grafikus kártyát, a merevlemez-meghajtóról, magok és más fizikai tulajdonságok forgórészek száma. <br> **Kapacitások** -fürt(ök) adható meg neve és a egy adott erőforrás mennyiségét, hogy egy adott csomópont felhasználható rendelkezik. Például egy csomópontot adhat meg, hogy rendelkezik-e "MemoryInMb" nevű metrika a kapacitása és arról, hogy vannak-e rendelkezésre álló 2048 MB-ot alapértelmezés szerint. Ezek a kapacitások használhatók futásidőben győződjön meg arról, hogy az adott mennyiségű erőforrást igénylő szolgáltatások kerüljenek-e a csomópontokon, hogy ezek az erőforrások a szükséges mennyiségben érhető el.<br>**IsPrimary** – Ha győződjön meg arról, hogy csak az egyik értéke az elsődleges, a következő értékkel definiált egynél több NodeType *igaz*, amely az cég, ahol a rendszer futtassa. Minden más csomóponttípusok értékre kell állítani *false (hamis)* |
| **Csomópontok** |Ezek azok a csomópontok (csomópont típusa, csomópont nevét, IP-cím, tartalék tartomány és frissítési tartomány a csomópont) a fürt részét képező részleteit. A gépek azt szeretné, a fürt IP-címeit az itt felsorolt kell létrehozni. <br> Ha minden csomóponton ugyanaz az IP-címet használ, majd egy beépített fürt létrehozása, amely tesztelési célokra használható. Ne használja az éles számítási feladatok üzembe helyezése beépített fürtök. |

Miután a fürt konfigurációját volt-e a környezet konfigurálva minden beállítás, megbízhatósága a fürt környezetre (7. lépés).

<a id="environmentsetup"></a>

## <a name="environment-setup"></a>A környezet beállítása

Amikor a fürt rendszergazdája konfigurálja a különálló Service Fabric-fürtön, a környezetet kell állítani a következő feltételek: <br>
1. A fürtöt létrehozó felhasználónak számítanak csomópontnak a fürt konfigurációs fájlban felsorolt összes gép biztonsági rendszergazdai jogosultsággal kell rendelkeznie.
2. A gép, amelyről a fürt létrehozása, valamint minden egyes fürt csomópontját működtető kell:
   * Service Fabric SDK eltávolítása után
   * Rendelkezik a Service Fabric-futtatókörnyezet eltávolítása 
   * A Windows tűzfalszolgáltatást (mpssvc) engedélyezve van
   * A távoli beállításjegyzék szolgáltatás (a távoli beállításjegyzék) engedélyezve van
   * Fájl megosztási (SMB) engedélyezve van
   * Rendelkezik a szükséges portokat lett megnyitva, a fürt konfiguráció portok alapján
   * Rendelkezik a szükséges portok megnyitása a Windows az SMB és a távoli beállításjegyzék szolgáltatás: a 135-ös, 137-es, 138, 139-es és a 445-ös
   * Egy másik hálózati kapcsolat
3. A fürt csomópont gépek egyike egy tartományvezérlő legyen.
4. Ha a fürt üzembe lesz helyezve egy biztonságos fürt, ellenőrizze a szükséges biztonsági előfeltételeket helyezze el, és megfelelően van konfigurálva a konfiguráció alapján.
5. Ha a fürt gépek nem internetről elérhető, a fürt konfigurációját a állítsa be a következőket:
   * Telemetria letiltása: A *tulajdonságok* beállítása *"enableTelemetry": hamis*
   * Tiltsa le az automatikus Fabric verzió letöltése és értesítések, hogy az aktuális fürt verziója van-e hamarosan megszűnik a támogatás: A *tulajdonságok* beállítása *"fabricClusterAutoupgradeEnabled": hamis*
   * Azt is megteheti, ha hálózati internet-hozzáférés korlátozott fehér felsorolt tartományokhoz, az alábbi tartományok szükségesek az automatikus frissítés: go.microsoft.com jövőben a Microsoft

6. Állítsa be a megfelelő Service Fabric víruskereső – kizárások:

| **A víruskereső kizárt könyvtárak** |
| --- |
| Program Files\Microsoft a Service Fabric |
| FabricDataRoot (a fürt konfiguráció) |
| FabricLogRoot (a fürt konfiguráció) |

| **A víruskereső kizárt folyamatok** |
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

## <a name="validate-environment-using-testconfiguration-script"></a>TestConfiguration parancsfájl-környezet ellenőrzése
A TestConfiguration.ps1 parancsfájlt a különálló csomag található. Mint egy ajánlott eljárásokat elemző eszköz ellenőrzésére szolgál a fenti feltételek közül néhányat, és megerősítést ellenőrzése ellenőrzése, hogy egy fürtöt is üzembe helyezhetők az adott környezetben használható. Bármilyen hiba esetén tekintse meg a lista [környezet beállítása](service-fabric-cluster-standalone-deployment-preparation.md) hibaelhárításhoz. 

Ezt a parancsfájlt minden olyan gép, amely számítanak csomópontnak a fürt konfigurációs fájlban felsorolt összes gép rendszergazdai hozzáféréssel rendelkezik. Ez a szkript futtatott a gép nem rendelkezik a fürt részeként.

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

Ez a konfiguráció tesztelési modul jelenleg nem érvényesíti a biztonsági beállítások tehát ez egymástól függetlenül hajtható végre.  

> [!NOTE]
> Folyamatosan igyekszünk fejlesztései, hogy ez a modul még robusztusabbá, így ha egy hibás vagy hiányzó eset úgy gondolja, amely nem jelenleg TestConfiguration által észlelt, tudassa velünk keresztül a [támogatási csatornákat](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).   
> 
> 

## <a name="next-steps"></a>További lépések
* [A Windows Server rendszert futtató önálló fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md)
