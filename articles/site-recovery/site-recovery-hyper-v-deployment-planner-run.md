---
title: "Azure Site Recovery Deployment Planner a Hyper-V – Azure-hoz| Microsoft Docs"
description: "Ez a cikk az Azure Site Recovery Deployment Planner futtatásának módjait ismerteti a Hyper-V – Azure forgatókönyvhöz."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/02/2017
ms.author: nisoneji
ms.openlocfilehash: 34f0b641abcf4231655d74da46f1bdcadc5642f6
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2018
---
# <a name="run-azure-site-recovery-deployment-planner-for-hyper-v-to-azure"></a>Az Azure Site Recovery Deployment Planner futtatása a Hyper-V – Azure-hoz

## <a name="modes-of-running-deployment-planner"></a>A Deployment Planner futtatásának módjai
A parancssori eszköz (ASRDeploymentPlanner.exe) a következő négy mód bármelyikében futtatható: 
1.  [Virtuálisgép-lista lekérése](#get-vm-list-for-profiling-hyper-v-vms)
2.  [Profilkészítés](#profile-hyper-v-vms)
3.  [Jelentéskészítés](#generate-report)
4.  [Átviteli sebesség lekérdezése](#get-throughput)

Először futtassa az eszközt, hogy megkapja a virtuális gépek listáját egy vagy több Hyper-V-gazdagépről.  Ezt követően futtassa profilkészítési módban az eszközt a virtuális gép adatváltozásának és IOPS-értékének összegyűjtéséhez. Ezután futtassa az eszközt a jelentés létrehozásához, hogy megtudhassa a hálózati sávszélességet és a tárolási követelményeket.

## <a name="get-vm-list-for-profiling-hyper-v-vms"></a>Virtuálisgép-lista lekérése Hyper-V virtuális gépek profiljának készítéséhez
Először létre kell hoznia a profillal ellátni kívánt virtuális gépek listáját. Használja a Deployment Planner GetVMList módját, hogy egyetlen paranccsal előállítsa a több Hyper-V-gazdagépen található virtuális gépek listáját. A teljes lista létrehozását követően eltávolíthatja a kimeneti fájlból azokat a virtuális gépeket, amelyekhez nem kíván profilt készíteni. Ezt követően a kimeneti fájlt a többi művelet végrehajtásához használhatja (profilkészítés, jelentéskészítés és az átviteli sebesség lekérdezése).

A virtuális gépek listáját az eszköz egy Hyper-V-fürtre vagy önálló Hyper-V-gazdagépre vagy a kettő kombinációjára irányításával hozhatja létre.

### <a name="command-line-parameters"></a>Parancssori paraméterek
A következő táblázat a GetVMList módban futtatandó kötelező és nem kötelező eszközparaméterek listáját tartalmazza. 
```
ASRDeploymentPlanner.exe -Operation GetVMList /?
```
| Paraméter neve | Leírás |
|---|---|
| -Művelet | GetVMList |
| -Felhasználó | A Hyper-V-kiszolgálóhoz vagy Hyper-V-fürthöz való csatlakozáshoz használt felhasználónév. A felhasználónak rendszergazdai hozzáféréssel kell rendelkeznie.|
|-ServerListFile | Azon kiszolgálók listáját tartalmazó fájl, amelyek a profillal ellátni kívánt virtuális gépeket tartalmazzák. A fájl elérési útja lehet abszolút vagy relatív. A fájl minden sorának tartalmazza a következők egyikét:<ul><li>Hyper-V-gazdagép neve vagy IP-címe</li><li>Hyper-V-fürt neve vagy IP-címe</li></ul><br>Például: A „ServerList.txt” fájl az alábbi kiszolgálókat tartalmazza:<ul><li>Host_1</li><li>10.8.59.27</li><li>Cluster_1</li><li>Host_2</li>|
| -Directory|(Nem kötelező) Az univerzális elnevezési konvenciónak (UNC) megfelelő elérési út vagy azon helyi könyvtár elérési útja, ahol a művelet során létrehozott adatokat tárolni kívánja. Ha nem adja meg, a rendszer az aktuális elérés úton található „ProfiledData” könyvtárat használja alapértelmezett könyvtárként.|
|-OutputFile| (Nem kötelező) A megadott Hyper-V-kiszolgálókról lekért virtuális gépek listáját tartalmazó fájl. Ha nincs megemlítve név, a részleteket a „VMList.txt” fájl tartalmazza.  A fájl használatával kezdje meg a profilkészítést, miután eltávolította azokat a virtuális gépeket, amelyekhez nem kíván profilt készíteni.|
|-Password|(Nem kötelező) A Hyper-V-kiszolgálóhoz való csatlakozáshoz használt jelszó.   Ha nem adja meg a jelszót paraméterként, a rendszer később, a parancs végrehajtásakor rákérdez.|

### <a name="how-does-getvmlist-discovery-work"></a>Hogyan működik a GetVMList felfedezése?
**Hyper-V-fürt**: A Hyper-V-fürt nevének a kiszolgálólista fájljában történő megadásakor az eszköz megkeresi a fürt összes Hyper-V-csomópontját, és megszerzi az egyes Hyper-V-gazdagépeken található virtuális gépeket.

**Hyper-V-gazdagép**:A Hyper-V-gazdagép nevének megadásakor az eszköz ellenőrzi, hogy az fürthöz tartozik-e. Ha igen, lekérdezi a fürthöz tartozó csomópontokat. Ezután lekéri a virtuális gépeket minden Hyper-V-gazdagéptől. 

Egy fájlba is listázhatja azon virtuális gépek rövid nevét vagy IP-címét, amelyeket manuálisan szeretne profillal ellátni.

Nyissa meg a kimeneti fájlt a Jegyzettömbben, és másolja egy másik fájlba (pl. ProfileVMList.txt) minden olyan virtuális gép nevét, amelyről profilt kíván készíteni. Minden virtuális gép nevét külön sorba írja. A rendszer ezt a fájlt használja majd bemenetként az eszköz -VMListFile paraméteréhez az összes műveletnél (profilkészítés, jelentéskészítés és az átviteli sebesség lekérdezése).

### <a name="example-1-to-store-the-list-of-vms-in-a-file"></a>1. példa: A virtuális gépek listájának tárolása egy fájlban
```
ASRDeploymentPlanner.exe -Operation GetVMlist -ServerListFile “E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1 -OutputFile "E:\Hyper-V_ProfiledData\VMListFile.txt"
```

### <a name="example-2-to-store-the-list-of-vms-at-the-default-location-ie--directory-path"></a>2. példa: A virtuális gépek listájának tárolása az alapértelmezett helyen (például: címtár elérési útja)
```
ASRDeploymentPlanner.exe -Operation GetVMList -Directory "E:\Hyper-V_ProfiledData" -ServerListFile "E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1
```

## <a name="profile-hyper-v-vms"></a>Profilkészítés Hyper-V rendszerű virtuális gépekhez
Profilkészítési módban a Deployment Planner eszköz mindegyik Hyper-V-gazdagéphez csatlakozik, hogy összegyűjtse a virtuális gépek teljesítményadatait. 

* A profilkészítés nincs hatással az éles virtuális gépek teljesítményére, mivel nem jön létre közvetlen kapcsolat az éles virtuális gépekkel. A rendszer minden teljesítményadatot összegyűjt a Hyper-V-kiszolgálóról.
* Az eszköz 15 másodpercenként lekérdezi a Hyper-V-kiszolgálót a pontos profilkészítés érdekében és minden percben tárolja a teljesítményszámláló adatainak átlagát.
* Az eszköz zökkenőmentesen kezeli a virtuális gépek csomópontok közötti migrációját a fürtben és a tárolómigrációt egy gazdagépen belül.

### <a name="get-vm-list-to-profile"></a>Profillal ellátni kívánt virtuális gépek listájának lekérése
Tekintse át a [GetVMList](#get-vm-list-for-profiling-hyper-v-vms) műveletet a profillal ellátni kívánt virtuális gépek listájának létrehozásához.

Ha megvan azon virtuális gépek listája, amelyekről profilt szeretne készíteni, futtathatja az eszközt profilkészítési módban. 

### <a name="command-line-parameters"></a>Parancssori paraméterek
A következő táblázat a profilkészítési módban futtatandó kötelező és nem kötelező eszközparaméterek listáját tartalmazza. Az eszköz ugyanaz VMware – Azure és Hyper-V – Azure forgatókönyvek esetén. A Hyper-V esetében az alábbi paraméterek alkalmazhatók. 
```
ASRDeploymentPlanner.exe -Operation StartProfiling /?
```
| Paraméter neve | Leírás |
|---|---|
| -Művelet | StartProfiling |
| -Felhasználó | A Hyper-V-kiszolgálóhoz vagy Hyper-V-fürthöz való csatlakozáshoz használt felhasználónév. A felhasználónak rendszergazdai hozzáféréssel kell rendelkeznie.|
| -VMListFile | Azon virtuális gépek listáját tartalmazó fájl, amelyekhez profilt szeretne készíteni. A fájl elérési útja lehet abszolút vagy relatív. A Hyper-V esetében ez a fájl a GetVMList művelet kimeneti fájlja. Ha az előkészületeket manuálisan végzi, a fájl minden sorában egy kiszolgáló nevének vagy IP-címének kell szerepelnie, amelyet egy virtuális gép neve követ \ perjellel elválasztva. A fájlban megadott virtuálisgép-névnek meg kell egyeznie a Hyper-V-gazdagépen szereplő névvel.<ul>Például: A „VMList.txt” fájl az alábbi virtuális gépeket tartalmazza:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-NoOfMinutesToProfile|A percek száma, ameddig a profilkészítést futtatni szeretné. A minimális érték 30 perc.|
|-NoOfHoursToProfile|Az órák száma, ameddig a profilkészítést futtatni szeretné.|
|-NoOfDaysToProfile |A napok száma, ameddig a profilkészítést futtatni szeretné. Javasoljuk, hogy legalább 7 napig futtassa a profilkészítést. Ez biztosítja számítási feladatok mintájának megfigyelését a környezetben a meghatározott időtartamon belül, amely alapján pontos javaslat adható.|
|-Virtualization|Itt adhatja meg a virtualizálás típusát (VMware vagy Hyper-V).|
|-Directory|(Nem kötelező) Az univerzális elnevezési konvenciónak (UNC) megfelelő elérési út vagy azon helyi könyvtár elérési útja, ahol a profilkészítés során létrehozott adatokat tárolni kívánja. Ha nem adja meg, a rendszer az aktuális elérés úton található „ProfiledData” könyvtárat használja alapértelmezett könyvtárként.|
|-Password|(Nem kötelező) A Hyper-V-gazdagéphez való csatlakozáshoz használt jelszó. Ha nem adja meg most, akkor a rendszer később, a parancs végrehajtásakor rákérdez.|
|-StorageAccountName|(Nem kötelező) A helyszínről az Azure-ba történő adatreplikáció során elérhető átviteli sebesség azonosításához szükséges tárfiók neve. Az eszköz erre a tárfiókra tölti fel a tesztadatokat az átviteli sebesség kiszámításához. A tárfióknak általános célú v1-nek vagy tároló v2-nek (általános célú v2) kell lennie|
|-StorageAccountKey|(Nem kötelező) A tárfiók eléréséhez használt tárfiókkulcs. Nyissa meg az Azure Portalt, és kattintson a Tárfiókok > <Storage account name> > Beállítások > Hozzáférési kulcsok > 1. kulcs (vagy klasszikus tárfiók esetén az Elsődleges elérési kulcs) elemre.|
|-Környezet|(Nem kötelező) Ez az Ön Azure Storage-fiókjának célkörnyezete. Ez a következő három érték egyike lehet: AzureCloud, AzureUSGovernment, AzureChinaCloud. Az alapértelmezett érték az AzureCloud. Akkor használja ezt a paramétert, ha Azure-célrégióként Azure US Government- vagy Azure China-beli felhők vannak megadva.|

Javasoljuk, hogy legalább 7 napig végezze a virtuális gépek profiljának készítését. Ha az adatváltozási minta egy hónapon belül változik, javasoljuk, hogy a héten akkor készítsen profilt, amikor a maximális változás tapasztalható. A legjobb megoldás, ha 31 nap alatt készít profilt, így jobb javaslatokat kaphat. A profilkészítés időtartama alatt az ASRDeploymentPlanner.exe alkalmazás folyamatosan fut. A profilkészítés időtartama napokban adható meg az eszközben. Az eszköz gyors teszteléséhez vagy megvalósíthatósági próbához néhány óra vagy perc alatt is készíthet profilt. A minimális profilkészítési idő 30 perc. 

A profilkészítés során lehetősége van átadni egy tárfióknevet és -kulcsot, amivel megtudhatja, hogy az Azure Site Recovery mekkora átviteli sebességet érhet el a Hyper-V-kiszolgálóról az Azure-ba történő replikáció során. Ha nem ad át tárfióknevet és -kulcsot a profilkészítés során, az eszköz nem számítja ki az elérhető átviteli sebességet.

Az eszköz több példánya is futtatható egyszerre különböző virtuálisgép-csoportokon. Győződjön meg arról, hogy a virtuális gépek neve nem ismétlődik egyik profilkészítési csoportban sem. Ha például profilt készített 10 virtuális gépről (VM1–VM10), és néhány nap elteltével további öt virtuális gépről szeretne profilt készíteni (VM11–VM15), akkor a második csoporton (VM11–VM15) egy másik parancssori konzolról futtathatja az eszközt. Győződjön meg arról, hogy a második virtuálisgép-csoport nem tartalmaz olyan virtuálisgép-nevet, amely szerepel az első profilkészítési példányban, vagy a második futtatás esetében használjon eltérő kimeneti könyvtárat. Ha az eszköz két példánya ugyanazokról a virtuális gépekről készít profilt, és ugyanazt a kimeneti könyvtárat is használják, a létrehozott jelentés helytelen lesz. 

Alapértelmezés szerint az eszköz a profilhoz van beállítva, és legfeljebb 1000 virtuális géphez generál jelentéseket. A korlát módosításához meg kell változtatni a MaxVMsSupported kulcs értékét az *ASRDeploymentPlanner.exe.config* fájlban.
```
<!-- Maximum number of vms supported-->
<add key="MaxVmsSupported" value="1000"/>
```
Az alapértelmezett beállítások mellett ahhoz, hogy a profil 1500 virtuális géppel dolgozzon, két VMList.txt fájlt kell létrehozni. Az egyiken 1000, a másikon pedig 500 virtuális gépnek kell szerepelnie. Futtassa az ASR Deployment Planner két példányát, az egyiket a VMList1.txt, a másikat pedig a VMList2.txt fájllal. Mindkét VMList virtuális gépeinek esetében ugyanazt a könyvtárútvonalat is használhatja a profilkészítési adatok tárolásához. 

Tapasztalatunk szerint a hardverkonfigurációtól, és különösen a jelentést generáló eszközt futtató kiszolgáló RAM-méretétől függően a művelet elegendő memória hiányában megszakadhat. Megfelelő hardveres feltételek mellett a MaxVMsSupported esetében bármilyen nagyobb értéket meg lehet adni.  

A rendszer egyszer, a profilkészítési művelet elején rögzíti virtuálisgép-konfigurációkat, és egy VMDetailList.xml nevű fájlban tárolja őket. Ezeket az adatokat azután a jelentés létrehozásakor használja. Ha a profilkészítés során a virtuális gépek konfigurációjának bármely része módosul (például nő a magok, a lemezek vagy a hálózati adapterek száma), azt a rendszer nem rögzíti. Ha a profilkészítés során módosul az egyik virtuális gép konfigurációja, amelyről profilt készít, akkor a következő megkerülő megoldással kérheti le a virtuális gép legfrissebb adatait a jelentés készítésekor:

* Készítsen biztonsági másolatot a VMdetailList.xml fájlról, és törölje a fájlt a jelenlegi helyéről.
* Adja át a -User és a -Password argumentumot a jelentés létrehozásakor.

A profilkészítési parancs számos fájlt létrehoz a profilkészítési könyvtárban. Ezeket ne törölje, mert az hatással lenne a jelentésre.

### <a name="examples"></a>Példák
#### <a name="example-1-profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>1. példa: Profilkészítés virtuális gépről 30 napon keresztül, valamint a helyszín és az Azure közötti átviteli sebesség meghatározása
```
ASRDeploymentPlanner.exe -Operation StartProfiling -virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile 30 -User Contoso\HyperVUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="example-2-profile-vms-for-15-days"></a>2. példa: Profilkészítés virtuális gépről 15 napon keresztül
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  15  -User contoso\HypreVUser1
```

#### <a name="example-3-profile-vms-for-60-minutes-for-a-quick-test-of-the-tool"></a>3. példa: Profilkészítés virtuális gépekről 60 perc alatt az eszköz gyors teszteléséhez
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -NoOfMinutesToProfile 60 -User Contoso\HyperVUser1
```

#### <a name="example-4-profile-vms-for-2-hours-for-a-proof-of-concept"></a>4. példa: Profilkészítés virtuális gépekről 2 óra alatt megvalósíthatósági próbához
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -NoOfHoursToProfile 2 -User Contoso\HyperVUser1
```

>[FIGYELEM!]
>
* Ha a kiszolgáló, amelyen az eszköz fut, újraindul vagy összeomlik, vagy ha bezárja az eszközt a Ctrl + C billentyűkombinációval, a rendszer megőrzi a profilkészítés során létrehozott adatokat. Előfordulhat azonban, hogy az utolsó 15 percnyi profilkészítés adatai elvesznek. Ezen esetekben a kiszolgáló újraindítása után ismét futtassa az eszközt profilkészítési módban.
* Ha megadja a tárfióknevet és -kulcsot, az eszköz a profilkészítés utolsó lépéseként megméri az átviteli sebességet. Ha bezárja az eszközt, mielőtt a profilkészítés befejeződött volna, a rendszer nem számítja ki az átviteli sebességet. Az átviteli sebesség a jelentés létrehozása előtti lekérdezéséhez futtassa a GetThroughput műveletet a parancssori konzolból. Ellenkező esetben a létrehozott jelentés nem tartalmazza majd az átviteli sebességgel kapcsolatos információkat.
* Az Azure Site Recovery nem támogatja az iSCSI- és csatlakoztatott lemezekkel rendelkező virtuális gépeket. Azonban az eszköz nem képes észlelni és profilt készíteni a virtuális gépekhez csatolt iSCSI- és csatlakoztatott lemezekről.

## <a name="generate-report"></a>Jelentés létrehozása
Az eszköz egy makróbarát Microsoft Excel-fájlt (XLSM-fájlt) hoz létre a jelentés kimeneteként, amely összefoglalja az üzembehelyezési javaslatokat. A jelentés neve DeploymentPlannerReport_<unique numeric identifier>.xlsm, és a megadott könyvtárban lesz elérhető.
A profilkészítés befejezése után futtathatja az eszközt jelentéskészítési módban. 

### <a name="command-line-parameters"></a>Parancssori paraméterek
A következő táblázat a jelentéskészítési módban futtatandó kötelező és nem kötelező eszközparaméterek listáját tartalmazza. Az eszköz ugyanaz VMware – Azure és Hyper-V – Azure forgatókönyvek esetén. A Hyper-V esetében az alábbi paraméterek alkalmazhatók.
```
ASRDeploymentPlanner.exe -Operation GenerateReport /?
```
| Paraméter neve | Leírás |
|---|---|
| -Művelet | Jelentés készítése |
|-VMListFile | Azon virtuális gépek listáját tartalmazó fájl, amelyekről profilt készített, és amelyekről jelentést szeretne készíteni. A fájl elérési útja lehet abszolút vagy relatív. A Hyper-V esetében ez a fájl a GetVMList művelet kimeneti fájlja. Ha az előkészületeket manuálisan végzi, a fájl minden sorában egy kiszolgáló nevének vagy IP-címének kell szerepelnie, amelyet egy virtuális gép neve követ \ perjellel elválasztva. A fájlban megadott virtuálisgép-névnek meg kell egyeznie a Hyper-V-gazdagépen szereplő névvel.<ul>Például: A „VMList.txt” fájl az alábbi virtuális gépeket tartalmazza:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Virtualization|Itt adhatja meg a virtualizálás típusát (VMware vagy Hyper-V).|
|-Directory|(Nem kötelező) A profilkészítés során létrehozott adatokat tároló mappa univerzális elnevezési konvenciónak (UNC) megfelelő vagy helyi elérési útja. Ezekre az adatokra szükség van a jelentés létrehozásához. Ha nincs megadva név, a rendszer az aktuális elérés úton található „ProfiledData” könyvtárat használja alapértelmezett könyvtárként.|
| -Felhasználó | (Nem kötelező) A Hyper-V-kiszolgálóhoz vagy Hyper-V-fürthöz való csatlakozáshoz használt felhasználónév. A felhasználónak rendszergazdai hozzáféréssel kell rendelkeznie.<br>A rendszer a felhasználó és a jelszó használatával kéri le a virtuális gépek legfrissebb konfigurációs adatait a jelentéshez (pl. lemezek, magok, hálózati adapterek száma stb.). Ha nincs megadva, a rendszer a profilkészítéskor összegyűjtött konfigurációs adatokat használja fel.|
|-Password|(Nem kötelező) A Hyper-V-gazdagéphez való csatlakozáshoz használt jelszó. Ha nem adja meg most, akkor a rendszer később, a parancs végrehajtásakor rákérdez.|
| -DesiredRPO | (Nem kötelező) A kívánt helyreállítási időkorlát, percben megadva. Az alapértelmezett beállítás 15 perc.|
| -Bandwidth | (Nem kötelező) Sávszélesség Mb/s-ban megadva. A rendszer ezen paraméter alapján számítja ki az elérhető helyreállítási időkorlátot a megadott sávszélességhez. |
| -StartDate | (Nem kötelező) Kezdő dátum és idő HH-NN-ÉÉÉÉ:ÓÓ:PP (24 órás) formátumban megadva. A *StartDate* és az *EndDate* paraméter megadása kötelező. Ha a StartDate meg van adva, a rendszer a StartDate és az EndDate paraméter közötti időszakban összegyűjtött, profilkészítéshez használt adatokról állít elő jelentést. |
| -EndDate | (Nem kötelező) Záró dátum és idő HH-NN-ÉÉÉÉ:ÓÓ:PP (24 órás) formátumban megadva. Az *EndDate* és a *StartDate* paraméter megadása kötelező. Ha az EndDate meg van adva, a rendszer a StartDate és az EndDate paraméter közötti időszakban összegyűjtött, profilkészítéshez használt adatokról állít elő jelentést. |
| -GrowthFactor | (Nem kötelező) A növekedési tényező százalékértékként megadva. Az alapértelmezett érték 30%. |
| -UseManagedDisks | (Nem kötelező) UseManagedDisks – Igen/Nem. Az alapértelmezett érték az Igen. Az egy tárfiókban elhelyezhető virtuális gépek számának meghatározáskor a rendszer figyelembe veszi, hogy a virtuális gépek feladatátvétele, illetve feladatátvételi tesztje nem felügyelt lemez helyett felügyelt lemezen történik. |
|-SubscriptionId |(Nem kötelező) Az előfizetés GUID azonosítója. A paraméter használatával létrehozhat egy költségbecslési jelentést a legújabb árakkal az előfizetése alapján, az előfizetéséhez tartozó ajánlat figyelembe vételével, és az adott cél Azure-régióhoz a megadott pénznemben.|
|-TargetRegion|(Nem kötelező) A replikáció által megcélzott Azure-régió. Mivel az Azure költségei régiónként eltérőek, adott cél Azure-régióra vonatkozó jelentés létrehozásához használja ezt a paramétert.<br>Az alapértelmezett régió az USA 2. nyugati régiója vagy a legutoljára használt célrégió.<br>Tekintse át a [támogatott célrégiók](site-recovery-hyper-v-deployment-planner-cost-estimation.md#supported-target-regions) listáját.|
|-OfferId|(Nem kötelező) Az adott előfizetéshez társított ajánlat. Az alapértelmezett az MS-AZR-0003P (használatalapú fizetés).|
|-Currency|(Nem kötelező) A pénznem, amelyben a költségek megjelennek a létrehozott jelentésben. Az alapértelmezett az amerikai dollár ($), vagy a legutoljára használt pénznem.<br>Tekintse át a [támogatott pénznemek](site-recovery-hyper-v-deployment-planner-cost-estimation.md#supported-currencies) listáját.|

Alapértelmezés szerint az eszköz a profilhoz van beállítva, és legfeljebb 1000 virtuális géphez generál jelentéseket. A korlát módosításához meg kell változtatni a MaxVMsSupported kulcs értékét az *ASRDeploymentPlanner.exe.config* fájlban.
```
<!-- Maximum number of vms supported-->
<add key="MaxVmsSupported" value="1000"/>
```

### <a name="examples"></a>Példák
#### <a name="example-1-generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>1. példa: Jelentés készítése az alapértelmezett értékekkel, ha a profilkészítés során létrehozott adatok a helyi meghajtón találhatóak
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-2-generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>2. példa: Jelentés készítése, ha a profilkészítés során létrehozott adatok távoli kiszolgálón találhatóak
A felhasználónak olvasási/írási hozzáféréssel kell rendelkeznie a távoli könyvtárhoz.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V - -Directory “\\PS1-W2K12R2\Hyper-V_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-3-generate-a-report-with-a-specific-bandwidth-that-you-will-be-provision-for-the-replication"></a>3. példa: Jelentés készítése a replikációhoz létrehozott adott sávszélességgel
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt” -Bandwidth 100
```

#### <a name="example-4-generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>4. példa: Jelentés készítése 5%-os növekedési tényezővel az alapértelmezett 30% helyett 
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt” -GrowthFactor 5
```

#### <a name="example-5-generate-a-report-with-a-subset-of-profiled-data"></a>5. példa: Jelentés létrehozása a profilkészítés során használt adatok egy részéből
Tegyük fel, hogy van 30 napnyi adata, amelyet a profilkészítés során hozott létre, de csak 20 napról szeretne jelentést készíteni.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt” -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="example-6-generate-a-report-for-5-minutes-rpo"></a>6. példa: Jelentés készítése 5 perces helyreállítási időkorláttal
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -DesiredRPO 5
```

#### <a name="example-7-generate-a-report-for-south-india-azure-region-with-indian-rupee-and-specific-offer-id"></a>7. példa: Jelentés létrehozása a Dél-India Azure-régióhoz indiai rúpia pénznemmel és adott ajánlatazonosítóval
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -SubscriptionID 4d19f16b-3e00-4b89-a2ba-8645edf42fe5 -OfferID MS-AZR-0148P -TargetRegion southindia -Currency INR
```


## <a name="percentile-value-used-for-the-calculation"></a>A számításhoz használt százalékérték
**Alapértelmezés szerint a profilkészítés során összegyűjtött teljesítménymetrikák milyen százalékos arányát használja az eszköz jelentések készítésekor?**

Az eszköz alapértelmezett értéke az írási/olvasási IOPS, az írási IOPS és az adatváltozás esetén a 95. százalékérték az összes virtuális gép profiljának elkészítése során. Ez a mérőszám biztosítja, hogy a virtuális gépek által az ideiglenes események miatt esetlegesen észlelt 100. százalékértékes kiugrást a rendszer nem használja fel a cél tárfiók és a forrássávszélesség követelményeinek meghatározásakor. Az ideiglenes esemény lehet például egy naponta egyszer futtatott biztonsági mentési feladat, rendszeres időközönként végzett adatbázis-indexelés, elemzésijelentés-készítési tevékenység vagy bármely hasonló, rövid ideig tartó, időpontalapú esemény.

A 95. százalékérték használata valós képet ad a számítási feladatok valódi jellemzőiről, és a lehető legjobb teljesítményt biztosítja, miközben a számítási feladatok futnak az Azure-on. Ezt az értéket valószínűleg nem kell módosítania. Ha úgy dönt, hogy módosítja (például a 90. százalékértékre), akkor az alapértelmezett mappában található ASRDeploymentPlanner.exe.config konfigurációs fájl frissítésével és mentésével létrehozhat egy új jelentést a meglévő, profilkészítés során létrehozott adatokról.
```
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

## <a name="growth-factor-considerations"></a>Növekedési tényezővel kapcsolatos szempontok
**Miért kell figyelembe vennem a növekedési tényezővel kapcsolatos szempontokat a környezetek megtervezésekor?**
Rendkívül fontos figyelembe venni a növekedést a számítási feladatok jellemzőiben, feltételezve a használat lehetséges növekedését. Ha beállítja a védelmet, és módosulnak a számítási feladatok jellemzői, nincs lehetőség arra, hogy egy másik védelmi tárfiókra váltson a védelem letiltása és ismételt engedélyezése nélkül.

Tegyük fel például, hogy a virtuális gép jelenleg megfelel egy standard szintű tárreplikációs fiókhoz. A következő néhány hónapban számos változás következhet be:

* Nő azoknak a felhasználóknak a száma, akik az alkalmazást a virtuális gépen futtatják.
* Az így létrejövő megnövekedett adatváltozás miatt a virtuális gépnek prémium szintű tárolóra van szüksége, hogy az Azure Site Recovery replikációja tartani tudja a lépést.
* Ennek következtében le kell tiltania, majd újra engedélyeznie kell a védelmet egy prémium szintű tárfiókon.

Erősen ajánlott, hogy az üzembe helyezés tervezése során számoljon a növekedéssel, amelynek az alapértelmezett értéke ugyan 30 százalék, azonban az alkalmazása használati mintáját és növekedési előrejelzéseit Ön ismeri a legjobban, és a jelentés elkészítése közben igény szerint módosíthatja ezt a számot. Továbbá több jelentést is készíthet különböző növekedési tényezőkkel ugyanazokról a profilkészítési adatokról. Így eldöntheti, hogy melyik, a céltárolóra, valamint a forrássávszélességre vonatkozó javaslatok felelnek meg leginkább Önnek. 

Az elkészített Microsoft Excel-jelentés a következő információkat tartalmazza:

* [Helyszíni összefoglalás](site-recovery-hyper-v-deployment-planner-analyze-report.md#on-premises-summary)
* [Javaslatok](site-recovery-hyper-v-deployment-planner-analyze-report.md#recommendations)
* [Virtuális gép<->tároló elhelyezése](site-recovery-hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation)
* [Compatible VMs](site-recovery-hyper-v-deployment-planner-analyze-report.md#compatible-vms) (Kompatibilis virtuális gépek)
* [Incompatible VMs](site-recovery-hyper-v-deployment-planner-analyze-report.md#incompatible-vms) (Nem kompatibilis virtuális gépek)
* [Helyszíni tárolásra vonatkozó követelmények](site-recovery-hyper-v-deployment-planner-analyze-report.md#on-premises-storage-requirement)
* [IR-kötegelés](site-recovery-hyper-v-deployment-planner-analyze-report.md#initial-replication-batching)
* [Költségbecslés](site-recovery-hyper-v-deployment-planner-cost-estimation.md)

![Deployment Planner-jelentés](media/site-recovery-hyper-v-deployment-planner-run/deployment-planner-report-h2a.png)


## <a name="get-throughput"></a>Átviteli sebesség lekérdezése
Ha meg szeretné becsülni az átviteli sebességet, amelyet az Azure Site Recovery el tud érni helyszínről az Azure-ba történő replikáció során, futtassa az eszközt GetThroughput módban. Az eszköz arról a kiszolgálóról számítja ki az átviteli sebességet, amelyen fut. Ideális esetben ez a Hyper-V-kiszolgáló, amelynek virtuális gépei számára védelmet kíván biztosítani. 

### <a name="command-line-parameters"></a>Parancssori paraméterek 
Nyisson meg egy parancssori konzolt, és lépjen az Azure Site Recovery Deployment Planner eszköz mappájába. Futtassa az ASRDeploymentPlanner.exe fájlt az alábbi paraméterekkel:
```
ASRDeploymentPlanner.exe -Operation GetThroughput /?
```
 Paraméter neve | Leírás |
|---|---|
| -Művelet | Átviteli sebesség lekérdezése |
|-Virtualization|Itt adhatja meg a virtualizálás típusát (VMware vagy Hyper-V).|
|-Directory|(Nem kötelező) A profilkészítés során létrehozott adatokat tároló mappa univerzális elnevezési konvenciónak (UNC) megfelelő vagy helyi elérési útja. Ezekre az adatokra szükség van a jelentés létrehozásához. Ha nincs megadva név, a rendszer az aktuális elérés úton található „ProfiledData” könyvtárat használja alapértelmezett könyvtárként.|
| -StorageAccountName | A helyszínről az Azure-ba történő adatreplikáció során felhasznált sávszélesség meghatározásához szükséges tárfiók neve. Az eszköz erre a tárfiókra tölti fel a tesztadatokat a felhasznált sávszélesség megállapításához. A tárfióknak általános célú v1-nek vagy tároló v2-nek (általános célú v2) kell lennie |
| -StorageAccountKey | A tárfiók eléréséhez használt tárfiókkulcs. Nyissa meg az Azure Portalt, és kattintson a Tárfiókok > <*Tárfiók neve*> > Beállítások > Hozzáférési kulcsok > 1. kulcs elemre.|
| -VMListFile | Azon virtuális gépek listáját tartalmazó fájl, amelyekről profilt szeretne készíteni a felhasznált sávszélesség kiszámításához. A fájl elérési útja lehet abszolút vagy relatív. A Hyper-V esetében ez a fájl a GetVMList művelet kimeneti fájlja. Ha az előkészületeket manuálisan végzi, a fájl minden sorában egy kiszolgáló nevének vagy IP-címének kell szerepelnie, amelyet egy virtuális gép neve követ \ perjellel elválasztva. A fájlban megadott virtuálisgép-névnek meg kell egyeznie a Hyper-V-gazdagépen szereplő névvel.<ul>Például: A „VMList.txt” fájl az alábbi virtuális gépeket tartalmazza:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Környezet|(Nem kötelező) Ez az Ön Azure Storage-fiókjának célkörnyezete. Ez a következő három érték egyike lehet: AzureCloud, AzureUSGovernment, AzureChinaCloud. Az alapértelmezett érték az AzureCloud. Akkor használja ezt a paramétert, ha cél Azure-régióként az Amerikai Egyesült Államok kormánya által használt vagy Azure China-beli felhők vannak megadva.|


Az eszköz több 64 MB-os asrvhdfile<#>.vhd fájlt (a „#” a fájlok számát jelöli) is létrehoz a megadott könyvtárban. Az eszköz feltölti a fájlokat a tárfiókba az átviteli sebesség megállapításához. Az átviteli sebesség mérése után az eszköz törli az összes fájlt a tárfiókból és a helyi kiszolgálóról. Ha az eszköz bármilyen oknál fogva leáll az átviteli sebesség kiszámítása közben, akkor nem törli a fájlokat a tárolóból és a helyi kiszolgálóról. Ezeket manuálisan kell törölnie.

Az eszköz egy meghatározott időpontban méri az átviteli sebességet, és a kapott érték az Azure Site Recovery által a replikáció során elérhető maximális átviteli sebesség, feltéve, hogy a többi tényező nem változik. Ha például valamelyik alkalmazás több sávszélességet kezd el felhasználni ugyanazon a hálózaton belül, a tényleges átviteli sebesség változhat a replikáció során. Az átviteli sebesség mérésének eredménye eltérő lesz, ha a védett virtuális gépek adatváltozása magas a GetThroughput művelet végrehajtásakor. Ajánlott az eszközt különböző időpontokban futtatni a profilkészítés során, hogy átláthassa, milyen átviteli sebességi szintek érhetők el a különféle időpontokban. Az eszköz a legutóbb mért átviteli sebességet jeleníti meg a jelentésben.
    
### <a name="example"></a>Példa
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Virtualization Hyper-V -Directory E:\Hyp-erV_ProfiledData -VMListFile E:\Hyper-V_ProfiledData\ProfileVMList1.txt  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

[FIGYELEM!]
>
> Olyan kiszolgálón futtassa az eszközt, amelynek a tárolási és processzorjellemzői megegyeznek a Hyper-V-kiszolgálóéval.
>
> A replikációhoz a helyreállítási időkorlát 100 százalékos eléréséhez javasolt sávszélességet állítsa be. Ha a megfelelő sávszélesség beállítása után nem nő az eszköz által jelentett elért átviteli sebesség, tegye a következőket:
>
>  1. Ellenőrizze, hogy van-e olyan hálózati szolgáltatásminőség (QoS), amely korlátozná az Azure Site Recovery átviteli sebességét.
>
>  2. Ellenőrizze a hálózati késés minimalizálása érdekében, hogy az Azure Site Recovery-tároló a legközelebbi fizikailag támogatott Microsoft Azure-régióban található-e.
>
>  3. Ellenőrizze a helyi tároló jellemzőit, és állapítsa meg, hogy fejleszthető-e a hardver (pl. váltás HDD-ről SSD-re).
>

## <a name="next-steps"></a>További lépések
* [A létrehozott jelentés elemzése](site-recovery-hyper-v-deployment-planner-analyze-report.md).