---
title: Azure Site Recovery Deployment Planner VMware Azure-hoz| Microsoft Docs
description: "Ez az Azure Site Recovery Deployment Planner felhasználói útmutatója"
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 2/21/2017
ms.author: nisoneji
translationtype: Human Translation
ms.sourcegitcommit: f438db1d0129dfb0e2eaa00146147084cd8c11b6
ms.openlocfilehash: 179e8cf928d83a3a66ed3489173e4c28a2bc9d4f
ms.lasthandoff: 02/28/2017


---
#<a name="azure-site-recovery-deployment-planner"></a>Azure Site Recovery Deployment Planner
Ez az Azure Site Recovery Deployment Planner felhasználói útmutatója a VMware–Azure éles környezetben való üzembe helyezéséhez.


##<a name="overview"></a>Áttekintés

A VMware virtuális gépek Azure Site Recovery-vel történő védelmének megkezdése előtt elegendő sávszélességet kell kiosztania a napi szintű adatváltozások alapján, hogy elérje a kívánt RPO-t. Üzembe kell helyeznie a megfelelő számú helyszíni konfigurációs és folyamatkiszolgálót. Létre kell hoznia a megfelelő típusú és számú standard vagy prémium szintű cél Azure Storage-tárfiókot is, a fokozatosan növekvő használat során megnövekedett éles kiszolgálók miatt. A tároló típusa virtuális gépenként dönthető el a munkaterhelés jellemzői (R/W IOPS, adatváltozás) és az Azure Site Recovery korlátai alapján.  

Az Azure Site Recovery Deployment Planner nyilvános előzetes verziója egy parancssori eszköz, amely jelenleg csak a VMware–Azure esetében érhető el. Ennek az eszköznek a használatával (termelési hatás nélkül) távolról készítheti el Vmware virtuális gépeit, hogy megismerhesse a sávszélességet és az Azure tárolási követelményeit a sikeres replikálás és feladatátvételi teszt érdekében.  Bármely helyszíni Azure Site Recovery-összetevő telepítése nélkül futtathatja az eszközt, habár a pontos elért átvitelisebesség-eredmény érdekében ajánlott a Planner futtatása egy olyan Windows Serveren, amely teljesíti annak az Azure Site Recovery Configuration Servernek a minimális követelményeit, amelyet végül telepítenie kell az éles környezetben való üzembe helyezés egyik első lépéseként.

Az eszköz a következő részleteket biztosítja:

**Kompatibilitási felmérés**<br>
* A virtuális gép jogosultságfelmérése a lemezek, a lemezméretek, az IOPS és a forgalom alapján

**A hálózatisávszélesség-igény és az RPO-elemzés**<br>
* Becsült hálózati sávszélesség a változásreplikáláshoz<br>
* Az az átviteli sebesség, amelyet az Azure Site Recovery szolgáltatás a helyszínekről érhet el az Azure számára<br>
* A kötegelt virtuális gépek száma a becsült sávszélesség alapján, a kezdeti replikálás adott időn belüli végrehajtásához<br>

**A Microsoft Azure infrastruktúrakövetelményei**<br>
* A tárolótípus (standard vagy prémium szintű tároló) követelménye az egyes virtuális gépekhez<br>
* A replikáláshoz kiosztandó standard és prémium szintű tárfiókok száma<br>
* Tárfiókok elnevezési javaslatai az Azure Storage útmutatója alapján<br>
* Az összes virtuális gép tárfiókjának elhelyezése<br>
* A feladatátvételi teszt/feladatátvételi előfizetés előtt kiosztandó Microsoft Azure magok száma<br>
* A Microsoft Azure-alapú virtuális gépek ajánlott mérete az egyes helyszíni virtuális gépek számára<br>

**Helyszíni infrastruktúra-követelmények**<br>
* A helyszínen üzembe helyezendő konfigurációs és folyamatkiszolgálók szükséges számú<br>

>[!IMPORTANT]
>
>Az eszközben minden számítás a munkaterhelési jellemzők 30%-os növekedési tényezőjével történik a folyamatosan növekedő használat lehetősége miatt, illetve a profilkészítési mérőszámok 95%-át alapul véve (R/W IOPS, adatforgalom stb.) Mindkét paraméter – a növekedési tényező és a százalékérték is – konfigurálható. További információ a [növekedési tényezőről](site-recovery-deployment-planner.md#growth-factor) és a [számításhoz használt százalékértékről](site-recovery-deployment-planner.md#percentile-value-used-for-the-calculation).
>


## <a name="requirements"></a>Követelmények
Az eszköz két fő fázisból áll – a profil- és jelentéskészítésből. Van egy harmadik lehetőség csak az átviteli sebesség kiszámítására. Az alábbiakban találhatók a kiszolgáló követelményei, ahol a profilkészítés/átviteli sebesség mérése készül.

| Követelmény | Leírás|
|---|---|
|Profilkészítés és az átviteli sebesség mérése| <br>Operációs rendszer: Microsoft Windows Server 2012 R2 <br>Ideális esetben legalább a következő konfigurációs kiszolgáló [méretével](https://aka.ms/asr-v2a-on-prem-components) egyezik<br>Gépkonfiguráció . 8 vCPU, 16 GB RAM, 300 GB HDD<br [Microsoft .NET-keretrendszer 4.5](https://aka.ms/dotnet-framework-45)<br>[VMware vSphere PowerCLI 6.0 R3](https://developercenter.vmware.com/tool/vsphere_powercli/6.0)<br>[A Visual Studio 2012 szoftverhez készült Microsoft Visual C++ terjeszthető változata](https://aka.ms/vcplusplus-redistributable)<br> Internet-hozzáférés a Microsoft Azure szolgáltatáshoz erről a kiszolgálóról<br> Microsoft Azure Storage-fiók<Br>Rendszergazdai hozzáférés a kiszolgálón<br>Minimális szabad lemezterület 100 GB (feltéve, hogy 1000 virtuális gép átlagosan 3 lemezzel 30 napig készít profilokat)|
| Jelentéskészítés| Bármely 2013-as vagy újabb Microsoft Excellel rendelkező Windows PC/Windows Server |
| Felhasználói engedélyek | Csak olvasási jogosultság a felhasználói fióknak a VMware vCenter/vSphere kiszolgáló profilkészítés közben való hozzáféréséhez|


> [!NOTE]
>
> Az eszköz csak a VMDK- és RDM-lemezzel rendelkező virtuális gépeknek tud profilt készíteni. Nem képes az iSCSI-vagy NFS-lemezzel rendelkező virtuális gépek profilkészítésére. Bár az Azure Site Recovery támogatja az iSCSI- és NFS-lemezeket a VMware-kiszolgálókkal, az üzembe helyezési tervező nincs a vendéggépben, és csak a vCenter teljesítményszámlálók használatával készít profilokat, így az eszköz nem lát ezekben a lemeztípusokban.
>


##<a name="download"></a>Letöltés
[Töltse le](https://aka.ms/asr-deployment-planner) az Azure Site Recovery Deployment Planner legújabb nyilvános előzetes verzióját.  Az eszköz zip formátumba van csomagolva.  Az eszköz jelenlegi verziója csak a VMware–Azure forgatókönyvet támogatja.

Másolja a zip-fájlt a Windows Serveren belül oda, ahonnan futtatni kívánja az eszközt. Habár a profillal ellátandó virtuális gépeket tartalmazó VMware vCenter-kiszolgálóhoz vagy a VMware vSphere-gazdagéphez való csatlakozáshoz futtathatja az eszközt bármely hálózati hozzáféréssel rendelkező Windows Server 2012 R2 operációs rendszerről, ajánlott egy olyan kiszolgálón futtatni az eszközt, amelynek a hardverkonfigurációja a [konfigurációs kiszolgáló méretezési útmutatója](https://aka.ms/asr-v2a-on-prem-components) alapján készült.  Ha már üzembe helyezte az Azure Site Recovery helyszíni összetevőit, futtassa az eszközt a konfigurációs kiszolgálóról. A konfigurációs kiszolgálóval megegyező (beépített folyamatkiszolgálóval rendelkező) hardverkonfiguráció ajánlott azon a kiszolgálón, ahol futtatja az eszközt, így az eszköz által jelentett elért átviteli sebesség meg fog egyezni az Azure Site Recovery replikálás során elérhető tényleges átviteli sebességgel – az átviteli sebesség számítása a kiszolgálón és a kiszolgáló hardverkonfigurációján (CPU, tárterület stb.) elérhető hálózati sávszélességtől függ. Ha az eszközt bármely másik kiszolgálóról futtatja, arról a kiszolgálóról történik a átviteli sebesség kiszámolása a Microsoft Azure-ba, és a kiszolgáló hardverkonfigurációja eltérhet a konfigurációs kiszolgálóétól, így az eszköz által jelentett elért átviteli sebesség nem lesz pontos.

Csomagolja ki a tömörített mappát. Több fájlt és almappát láthat. Az ASRDeploymentPlanner.exe futtatható a szülőmappában.

Példa: másolja a zip-fájlt az E:\ meghajtóra, és csomagolja ki.
E:\ASR Deployment Planner-Preview_v1.0.zip

E:\ASR Deployment Planner-Preview_v1.0\ ASR Deployment Planner-Preview_v1.0\ ASRDeploymentPlanner.exe

##<a name="capabilities"></a>Funkciók
A parancssori eszköz (ASRDeploymentPlanner.exe) a következő három mód bármelyikében futtatható:

1.    Profilkészítés  
2.    Jelentéskészítés
3.    Átviteli sebesség lekérdezése

Először futtassa profilkészítés-módban az eszközt, hogy összegyűjtse a virtuális gép adatforgalmát és IOPS-értékét.  Ezután futtassa az eszközt a jelentés létrehozásához, hogy megtudhassa a hálózati sávszélességet és a tárolási követelményeket.

##<a name="profiling"></a>Profilkészítés
A profilkészítési módban a Deployment Planner eszköz a vCenter-kiszolgálóhoz vagy vSphere ESXi-gazdagéphez csatlakozik, hogy összegyűjtse a virtuális gép teljesítményadatait.

* A pofilkészítés nincs hatással a virtuális gépek teljesítményére, mivel nem jön létre közvetlen kapcsolat az éles virtuális géppel. A rendszer minden teljesítményadatot összegyűjt a vCenter-kiszolgálóról / vSphere ESXi-gazdagépről.
* A rendszer 15 percenként kérdezi le a vCenter-kiszolgálót / vSphere ESXi-gazdagépet, hogy biztosítani tudja, hogy a profilkészítés elhanyagolható hatással van a kiszolgálóra. Ez azonban nem veszélyezteti a profilkészítés pontosságát, mivel az eszköz minden percben tárolja a teljesítményszámláló adatait.

####<a name="create-a-list-of-virtual-machines-to-profile"></a>A profillal ellátni kívánt virtuális gépek listájának létrehozása
Először is szükség van a virtuális gépek listájára, amelyekhez profilt szeretne készíteni. A következő VMware vSphere PowerCLI-parancsok használatával a VMware vCenteren vagy a VMware vSphere ESXi-gazdagépen található összes virtuális gép nevét elérheti. Másik lehetőségként listázhatja azon virtuális gépek rövid nevét / IP-címét, amelyeket manuálisan szeretne a profilba helyezni.

1.    Jelentkezzen be a virtuális gépbe, ahol a VMware vSphere PowerCLI telepítve van
2.    A VMware vSphere PowerCLI konzol megnyitása
3.    Győződjön meg arról, hogy a végrehajtási szabályzat nincs letiltva a szkript esetében. Ha a szabályzat le van tiltva, indítsa el a VMware vSphere PowerCLI-konzolt rendszergazdai módban, és futtassa a következő parancsot az engedélyezéséhez:

            Set-ExecutionPolicy –ExecutionPolicy AllSigned

4.    Futtassa az alábbi két parancsot a VMware vCenteren vagy a VMware vSphere ESXi-n található virtuális gépek nevének lekéréséhez, és mentse őket egy szöveges dokumentumban (.txt).
Cserélje le a &lsaquo;server name&rsaquo; (kiszolgáló neve), a &lsaquo;user name&rsaquo; (felhasználónév), a &lsaquo;password&rsaquo; (jelszó), az &lsaquo;outputfile.txt&rsaquo; (kimenetifájl.txt) paramétereket saját értékeire.

            Connect-VIServer -Server <server name> -User <user name> -Password <password>

            Get-VM |  Select Name | Sort-Object -Property Name >  <outputfile.txt>


5.    Nyissa meg a kimeneti fájlt a Jegyzettömbben. Másolja egy másik fájlba (pl. ProfileVMList.txt) minden olyan virtuális gép nevét, amelyről profilt kíván készíteni. Minden virtuális gép nevét külön sorba írja. A rendszer ezt a fájlt használja majd bemenetként a parancssori eszköz -VMListFile paraméteréhez

    ![Üzembehelyezés-tervező](./media/site-recovery-deployment-planner/profile-vm-list.png)


####<a name="start-profiling"></a>Profilkészítés indítása
Ha megvan azon virtuális gépek listája, amelyekről profilt szeretne készíteni, futtathatja az eszközt profilkészítési módban. Az alábbi lista az eszköz profilkészítési módjában használható kötelező és választható paramétereket sorolja fel. A szögletes zárójelek ([]) közé írt paraméterek nem kötelezők.

ASRDeploymentPlanner.exe -Operation StartProfiling /?

| Paraméter neve | Leírás |
|---|---|
| -Művelet |      StartProfiling |
| -Kiszolgáló | Azon vCenter-kiszolgáló/ESXi-gazdagép teljes tartományneve vagy IP-címe, amelynek virtuális gépeiről profilt szeretne készíteni.|
| -Felhasználó | A vCenter-kiszolgálóhoz/ESXi-gazdagéphez való csatlakozáshoz használt felhasználónév. A felhasználónak legalább olvasási hozzáféréssel kell rendelkeznie.|
| -VMListFile |    Azon virtuális gépek listáját tartalmazó fájl, amelyekről profilt szeretne készíteni. A fájl elérési útja lehet abszolút vagy relatív. A fájl minden sorában egy virtuális gép nevének vagy IP-címének kell állnia. A fájlban megadott virtuálisgép-neveknek meg kell egyezniük a vCenter-kiszolgálón vagy az ESXi-gazdagépen szereplő nevekkel. <br> Például: A „VMList.txt” fájl az alábbi virtuális gépeket tartalmazza:<br>virtual_machine_A <br>10.150.29.110<br>virtual_machine_B |
| -NoOfDaysToProfile | A napok száma, ameddig a profilkészítést futtatni szeretné. Javasoljuk, hogy legalább 15 napig futtassa a profilkészítést. Ez biztosítja számítási feladatok mintájának megfigyelését a környezetben a meghatározott időtartamon belül, amely alapján pontos javaslat adható |
| [-Címtár] |    Az UNC elérési út vagy azon helyi könyvtár elérési útja, ahol a profilkészítés során létrehozott adatokat tárolni kívánja. Ha nem adja meg, a rendszer az aktuális elérés úton található „ProfiledData” könyvtárat használja alapértelmezett könyvtárként. |
| [-Password ] | A vCenter-kiszolgálóhoz/ESXi-gazdagéphez való csatlakozáshoz használt jelszó. Ha nem adja meg most, a rendszer a parancs végrehajtásakor rákérdez.|
|  [-StorageAccountName]  | A helyszínről az Azure-ba történő adatreplikáció során elérhető átviteli sebesség azonosításához szükséges Azure Storage-fióknév. Az eszköz erre a tárfiókra tölti fel a tesztadatokat az átviteli sebesség kiszámításához.|
| [-StorageAccountKey] | A tárfiók eléréséhez használt Azure Storage-fiókkulcs. Nyissa meg az Azure Portalt, és kattintson a Tárfiókok > [Tárfiók neve] > Beállítások > Hozzáférési kulcsok >&1;. kulcs (vagy klasszikus tárfiók esetén az Elsődleges elérési kulcs) lehetőségre. |

Javasoljuk, hogy legalább 15–30 napig folytassa a virtuális gépek profiljának készítését. A profilkészítés időtartama alatt az ASRDeploymentPlanner.exe alkalmazás folyamatosan fut. A profilkészítés időtartama napokban adható meg az eszközben. Ha csak gyorsan tesztelni kívánja az eszközt, és néhány óráig vagy percig szeretné futtatni a profilkészítést, akkor a nyilvános előzetes verzióban napban kell megadnia az időtartamot.  Ha például 30 percig szeretné futtatni a profilkészítést, a bemenet 30 / (60*24), azaz 0,021 nap.  A minimális profilkészítési idő 30 perc.

A profilkészítés során lehetősége átadhat Azure Storage-fióknév és -kulcsot, amivel megtudhatja, hogy az Azure Site Recovery mekkora átviteli sebességet érhet el a konfigurációs kiszolgálóról vagy a folyamatkiszolgálóról az Azure-ba történő replikáció során. Ha nem ad át Azure Storage-fióknevet és -kulcsot a profilkészítés során, az eszköz nem számítja ki az elérhető átviteli sebességet.


Az eszköz több példánya is futtatható egyszerre különböző virtuálisgép-készleteken. Győződjön meg arról, hogy a virtuális gépek neve nem ismétlődik egyik profilkészítési készletben sem. Ha például profilt készített tíz virtuális gépről (VM1–VM10), és néhány nap elteltével további öt virtuális gépről szeretne profilt készíteni (VM11–VM15), akkor futtathatja az eszközt egy másik parancssori konzolból a második készleten (VM11–VM15). Arról azonban meg kell győződnie, hogy a második virtuálisgép-készlet nem tartalmaz olyan virtuálisgép-nevet, amely szerepel az első profilkészítési példányban, és hogy a második futtatás esetében eltérő kimeneti könyvtárat használ. Ha az eszköz két példánya ugyanazokról a virtuális gépekről készít profilt, és ugyanazt a kimeneti könyvtárat is használják, a létrehozott jelentés helytelen lesz.

A rendszer egyszer, a profilkészítési művelet elején rögzíti virtuálisgép-konfigurációkat, és egy VMDetailList.xml nevű fájlban tárolja őket. A rendszer ezt az információt használja fel a jelentés létrehozásakor. A virtuális gép konfigurációjában, a profilkészítés elindítása és leállása közötti időszakban végzett módosításokat (pl. magok, lemezek, hálózati adapterek stb. számának növelése) nem rögzíti a rendszer. Ha a profilkészítés közben az egyik virtuális gép konfigurációja megváltozik, akkor a nyilvános előzetes verzióban az alábbi megkerülő megoldással kérheti le a virtuális gép legújabb adatait a jelentés készítésekor.   

* Készítsen biztonsági másolatot a „VMdetailList.xml” fájlról, és törölje a fájlt a jelenlegi helyéről.
* Adja meg a -User és -Password argumentumokat a jelentés létrehozásakor.

A profilkészítési parancs számos fájlt létrehoz a profilkészítési könyvtárban. Ezeket ne törölje, mert az hatással lenne a jelentésre.

#####<a name="example-1-to-profile-virtual-machines-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>1. példa: Profilkészítés virtuális gépről 30 napon keresztül, és a helyszínről az Azure-ba történő átvitel sebességének meghatározása
ASRDeploymentPlanner.exe **-Operation** StartProfiling -Directory “E:\vCenter1_ProfiledData” **-Server** vCenter1.contoso.com **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  **-NoOfDaysToProfile**  30  **-User** vCenterUser1 **-StorageAccountName**  asrspfarm1 **-StorageAccountKey** Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==

#####<a name="example-2-to-profile-virtual-machines-for-15-days"></a>2. példa: Profilkészítés virtuális gépről 15 napon keresztül
ASRDeploymentPlanner.exe **-Operation** StartProfiling **-Directory** “E:\vCenter1_ProfiledData” **-Server** vCenter1.contoso.com **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  **-NoOfDaysToProfile**  15  -User vCenterUser1

#####<a name="example-3-to-profile-virtual-machines-for-1-hour-for-a-quick-test-of-the-tool"></a>3. példa: Profilkészítés virtuális gépről 1 órán keresztül az eszköz gyors teszteléséhez
ASRDeploymentPlanner.exe **-Operation** StartProfiling **-Directory** “E:\vCenter1_ProfiledData” **-Server** vCenter1.contoso.com **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  **-NoOfDaysToProfile**  0.04  **-User** vCenterUser1


>[!NOTE]
>
> * Ha a kiszolgáló, amelyen az eszköz fut, újraindul vagy összeomlik, vagy ha bezárja az eszközt a Ctrl + C billentyűkombinációval, a rendszer megőrzi azokat az adatokat, amelyekről profil készült. Előfordulhat, hogy az utolsó 15 percnyi profilkészítés adatai emiatt elvesznek. A kiszolgáló újraindítása után ismét futtatnia kell az eszközt profilkészítési módban.
>
> * Ha megadja az Azure Storage-fióknevet és -kulcsot, az eszköz a profilkészítés utolsó lépéseként megméri az átviteli sebességet. Ha bezárja az eszközt, mielőtt a profilkészítés teljesen befejeződött volna, a rendszer nem számítja ki az átviteli sebességet. Bármikor lekérdezheti az átviteli sebességet a jelentés létrehozása előtt, ha futtatja a GetThroughput műveletet a parancssori konzolból. Ellenkező esetben a létrehozott jelentés nem tartalmazza majd az elért átviteli sebességről szóló információt.
>

##<a name="generate-report"></a>Jelentés létrehozása
Az eszköz egy XLSM-fájlt (makróbarát Microsoft Excel-fájl) hoz létre a jelentés kimeneteként, amely összefoglalja az üzembehelyezési javaslatokat. A jelentés neve DeploymentPlannerReport_<Unique Numeric Identifier>.xlsm, és a megadott könyvtárban lesz elérhető.

A profilkészítés befejezése után futtathatja az eszközt jelentéskészítési módban. Az alábbi lista az eszköz jelentéskészítési módjában használható kötelező és választható paramétereket sorolja fel. A szögletes zárójelek ([]) közé írt paraméterek nem kötelezők.

ASRDeploymentPlanner.exe -Operation GenerateReport /?

|Paraméter neve | Leírás |
|-|-|
| -Művelet | Jelentés készítése |
| -Kiszolgáló |  Annak a vCenter- vagy vSphere-kiszolgálónak a teljes tartományneve vagy IP-címe, ahol azon virtuális gépek találhatók, amelyekről profilt készített, és amelyekről most jelentést szeretne készíteni. Ügyeljen arra, hogy ha vCenter-kiszolgálót használt a profilkészítés során, akkor nem használhat vSphere-kiszolgálót a jelentéskészítéshez, és fordítva.|
| -VMListFile | Azon virtuális gépek listáját tartalmazó fájl, amelyekről profilt készített, és amelyekről most jelentést szeretne készíteni. A fájl elérési útja lehet abszolút vagy relatív. A fájl minden sorában egy virtuális gép nevének vagy IP-címének kell állnia. A fájlban megadott virtuálisgép-neveknek meg kell egyezniük a vCenter-kiszolgálón vagy az ESXi-gazdagépen szereplő, valamint a profilkészítéskor használt nevekkel.|
| [-Címtár] | A profilkészítés során létrehozott adatokat tároló mappa UNC vagy helyi elérési útja. Ezekre az adatokra szükség van a jelentés létrehozásához. Ha nincs megadva, a rendszer a „ProfiledData” könyvtárat használja. |
| [-GoalToCompleteIR] |    Ennyi óra alatt kell befejeződnie azoknak a virtuális gépeknek a kezdeti replikációja, amelyekről profilt kíván készíteni. Az elkészített jelentés tartalmazza azon virtuális gépek számát, amelyeknek a kezdeti replikációja befejezhető a megadott időtartam alatt. Az alapértelmezett érték 72 óra. |
| [-Felhasználó] | A vCenter- vagy vSphere-kiszolgálóhoz való csatlakozáshoz használt felhasználónév. A rendszer ezzel kéri le a virtuális gépek legutóbbi konfigurációs adatait a jelentéshez (pl. lemezek, magok, hálózati adapterek száma stb.). Ha nincs megadva, a rendszer a profilkészítés indításakor összegyűjtött konfigurációs adatokat használja fel. |
| [-Jelszó] | A vCenter-kiszolgálóhoz/ESXi-gazdagéphez való csatlakozáshoz használt jelszó. Ha nem adja meg paraméterként, a rendszer a parancs végrehajtásakor rákérdez. |
| [-DesiredRPO] | A kívánt helyreállítási időkorlát (RPO) percekben megadva. Az alapértelmezett érték 15 perc.|
| [-Sávszélesség] | Sávszélesség (Mbps). A rendszer ez alapján számítja ki a helyreállítási időkorlátot a megadott sávszélességhez. |
| [-StartDate]  | Kezdő dátum és idő HH-NN-ÉÉÉÉ:ÓÓ:PP (24 órás) formátumban megadva. A „StartDate” és „EndDate” paraméterek megadása kötelező. Ha ezek meg vannak adva, a rendszer a StartDate és EndDate paraméterek közötti időszakban összegyűjtött, profilkészítéshez használt adatokról állít elő jelentést. |
| [-EndDate] | Záró dátum és idő HH-NN-ÉÉÉÉ:ÓÓ:PP (24 órás) formátumban megadva. Az „EndDate” és „StartDate” paraméterek megadása kötelező. Ha ezek meg vannak adva, a rendszer a StartDate és EndDate paraméterek közötti időszakban összegyűjtött, profilkészítéshez használt adatokról állít elő jelentést. |
| [-GrowthFactor] |Növekedési tényező százalékos értéke. Az alapértelmezett érték 30%.  |


##### <a name="example-1-to-generate-report-with-default-values-when-profiled-data-is-on-the-local-drive"></a>1. példa: Jelentés készítése alapértelmezett értékekkel, ha a profilkészítés során létrehozott adatok a helyi meghajtón vannak
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** “E:\vCenter1_ProfiledData” **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt”


##### <a name="example-2-to-generate-report-when-profiled-data-is-on-a-remote-server-user-should-have-readwrite-access-on-the-remote-directory"></a>2. példa: Jelentés készítése, ha a profilkészítés során létrehozott adatok távoli kiszolgálón vannak. A felhasználónak olvasási/írási hozzáféréssel kell rendelkeznie a távoli könyvtárhoz.
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** “\\PS1-W2K12R2\vCenter1_ProfiledData” **-VMListFile** “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”

##### <a name="example-3-generate-report-with-specific-bandwidth-and-goal-to-complete-ir-within-specified-time"></a>3. példa: Jelentés készítése megadott sávszélességgel és a kezdeti replikáció adott határidejű befejezésére vonatkozó céllal
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** “E:\vCenter1_ProfiledData” **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt” **-Bandwidth** 100 **-GoalToCompleteIR** 24

##### <a name="example-4-generate-report-with-5-growth-factor-instead-of-the-default-30"></a>4. példa: Jelentés készítése 5%-os növekedési tényezővel az alapértelmezett 30% helyett
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** “E:\vCenter1_ProfiledData” **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt” **-GrowthFactor** 5

##### <a name="example-5-generate-report-with-a-subset-of-profiled-data-say-you-have-30-days-of-profiled-data-and-want-to-generate-the-report-for-only-20-days"></a>5. példa: Jelentés létrehozása a profilkészítés során használt adatok részhalmazával. Tegyük fel, hogy van 30 napnyi adata, amelyet a profilkészítés során hozott létre, de csak 20 napról szeretne jelentést készíteni.
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** “E:\vCenter1_ProfiledData” **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt” **-StartDate**  01-10-2017:12:30 -**EndDate** 01-19-2017:12:30

##### <a name="example-6-generate-report-for-5-minutes-rpo"></a>6. példa: Jelentés készítése 5 perces helyreállítási időkorláttal.
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** “E:\vCenter1_ProfiledData” **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  **-DesiredRPO** 5

### <a name="percentile-value-used-for-the-calculation"></a>A számításhoz használt százalékérték
**Alapértelmezés szerint a profilkészítés során összegyűjtött teljesítménymetrikák milyen százalékos arányát használja a rendszer a jelentés elkészítésekor?**

Az eszköz alapértelmezett értéke az írási/olvasási IOPS, az írási IOPS és az adatváltozás esetén a 95. százalékérték az összes virtuális gép profiljának elkészítése során. Ez biztosítja, hogy a rendszer nem használja fel a virtuális gépeken tapasztalt átmeneti események (pl. naponta egyszer futtatott biztonsági mentési feladat, időszakos adatbázis-indexelés, elemzési jelentés készítése vagy bármely más, a profilkészítés ideje alatt történő, rövid ideig tartó esemény) által kiváltott 100%-os kiugrásokat a céloldali Azure Storage-re és a forrásoldali sávszélességre vonatkozó követelmények megállapításakor. A 95. százalékérték használata valós képet ad a számítási feladatok valódi jellemzőiről, és a lehető legjobb teljesítményt biztosítja, miközben ezek a számítási feladatok futnak a Microsoft Azure-on. Ezt az értéket valószínűleg nem kell gyakran módosítania, de ha úgy dönt, hogy tovább csökkenti (például 90%-ra), akkor az alapértelmezett mappában található „ASRDeploymentPlanner.exe.config” konfigurációs fájl frissítésével és mentésével létrehozhat egy új jelentést a már létező, a profilkészítés során létrehozott adatokról.

        &lsaquo;add key="WriteIOPSPercentile" value="95" /&rsaquo;>      
        &lsaquo;add key="ReadWriteIOPSPercentile" value="95" /&rsaquo;>      
        &lsaquo;add key="DataChurnPercentile" value="95" /&rsaquo;

### <a name="growth-factor"></a>Növekedési tényező
**Miért érdemes figyelembe venni a növekedési tényezőt az üzembe helyezés megtervezésénél?**

Rendkívül fontos figyelembe venni a növekedést a számítási feladatok jellemzőiben, feltételezve a használat lehetséges növekedését. Erre azért van szükség, mert ha beállítja a védelmet, és módosulnak a számítási feladatok jellemzői, jelenleg nincs lehetőség arra, hogy egy másik védett Azure Storage-fiókra váltson a védelem letiltása és ismételt engedélyezése nélkül. Például ha egy virtuális gép jelenleg megfelel egy standard szintű tárreplikációs fiókhoz, de például három hónap elteltével a virtuális gépen futó alkalmazás felhasználószámának növekedése miatt az adatforgalom megnő a virtuális gépen, és prémium szintű tárolóra van szükség. Ebben ez esetben le kell tiltania a védelmet, majd ismét engedélyeznie kell egy prémium szintű tárfiókon, hogy az Azure Site Recovery replikációja tartani tudja a lépést a nagyobb forgalommal. Emiatt erősen ajánlott, hogy az üzembe helyezés tervezése során számoljon a növekedéssel, amelynek alapértelmezett értéke 30%. Az alkalmazása használati mintáját és növekedési előrejelzéseit Ön ismeri a legjobban, és a jelentés elkészítése közben igény szerint módosíthatja ezt a számot. Azt is megteheti, hogy több jelentést készít különböző növekedési tényezőkkel, de ugyanazokról az adatokról. Így eldöntheti, hogy melyik, a céloldali Azure Storage-ra, valamint a forrásoldali sávszélességre vonatkozó javaslatok felelnek meg leginkább Önnek.

Az elkészített Microsoft Excel-jelentés a következő munkalapokat tartalmazza

* [Input](site-recovery-deployment-planner.md#input) (Bemenet)
* [Recommedations](site-recovery-deployment-planner.md#recommendations-with-desired-rpo-as-input) (Javaslatok)
* [Recommedations-Bandwidth Input](site-recovery-deployment-planner.md#recommendations-with-available-bandwidth-as-input) (Javaslatok – Bemeneti sávszélesség)
* [VM<->Storage Placement](site-recovery-deployment-planner.md#vm-storage-placement) (Virtuálisgép<->tároló elhelyezése)
* [Compatible VMs](site-recovery-deployment-planner.md#compatible-vms) (Kompatibilis virtuális gépek)
* [Incompatible VMs](site-recovery-deployment-planner.md#incompatible-vms) (Nem kompatibilis virtuális gépek)

![Üzembehelyezés-tervező](./media/site-recovery-deployment-planner/dp-report.png)


##<a name="get-throughput"></a>Átviteli sebesség lekérdezése
Ha meg szeretné becsülni az átviteli sebességet, amelyet az Azure Site Recovery el tud érni helyszínről az Azure-ba történő replikáció során, futtassa az eszközt GetThroughput módban. Az eszköz arról a kiszolgálóról számítja ki az átviteli sebességet, amelyen fut (ez a kiszolgáló optimális esetben a konfigurációs kiszolgálók méretezési útmutatóján alapul).  Ha már üzembe helyezte az Azure Site Recovery infrastruktúra-összetevőit a helyszínen, futtassa az eszközt a konfigurációs kiszolgálón.

Nyisson meg egy parancssori konzolt, és keresse meg az ASR üzembehelyezés-tervező eszköz mappáját.  Futtassa az ASRDeploymentPlanner.exe fájlt az alábbi paraméterekkel. A szögletes zárójelek ([]) közé írt paraméterek nem kötelezők.

ASRDeploymentPlanner.exe -Operation GetThroughput /?

|Paraméter neve | Leírás |
|-|-|
| -művelet | Átviteli sebesség lekérdezése |
| [-Címtár] | A profilkészítés során létrehozott adatokat tároló mappa UNC vagy helyi elérési útja. Ezekre az adatokra szükség van a jelentés létrehozásához. Ha nincs megadva, a rendszer a „ProfiledData” könyvtárat használja.  |
| -StorageAccountName | A helyszínről az Azure-ba történő adatreplikáció során felhasznált sávszélesség meghatározásához szükséges Azure Storage-fióknév. Az eszköz erre a tárfiókra tölti fel a tesztadatokat a felhasznált sávszélesség megállapításához. |
| -StorageAccountKey | A tárfiók eléréséhez használt Azure Storage-fiókkulcs. Nyissa meg az Azure Portalt, és kattintson a Tárfiókok > [Tárfiók neve] > Beállítások > Hozzáférési kulcsok >&1;. kulcs (vagy klasszikus tárfiók esetén az Elsődleges elérési kulcs) lehetőségre. |
| -VMListFile | Azon virtuális gépek listáját tartalmazó fájl, amelyekről profilt szeretne készíteni a felhasznált sávszélesség kiszámításához. A fájl elérési útja lehet abszolút vagy relatív. A fájl minden sorában egy virtuális gép nevének vagy IP-címének kell állnia. A fájlban megadott virtuálisgép-neveknek meg kell egyezniük a vCenter-kiszolgálón vagy az ESXi-gazdagépen szereplő nevekkel.<br>Például A „VMList.txt” fájl az alábbi virtuális gépeket tartalmazza:<br>virtual machine_A <br>10.150.29.110<br>virtual machine_B|

Az eszköz több 64 MB-os „asrchdfile<#>.vhd” nevű fájlt (a # a virtuális merevlemez számát jelöli) is létrehoz a megadott könyvtárban.  Az eszköz ezeket a fájlokat tölti fel az Azure Storage-fiókba az átviteli sebesség megállapításához. Az átviteli sebesség mérése után törli ezeket a fájlokat az Azure Storage-fiókból és a helyi kiszolgálóról. Ha az eszköz bármilyen oknál fogva leáll az átviteli sebesség kiszámítása közben, akkor nem törli a fájlokat az Azure Storage-ból és a helyi kiszolgálóról. Ebben az esetben manuálisan kell törölnie a fájlokat.

Az eszköz egy adott időpontban méri az átviteli sebességet, és a kapott érték az Azure Site Recovery által a replikáció során elérhető maximális átviteli sebesség, feltéve, hogy a többi tényező sem változik. Ha például valamelyik alkalmazás több sávszélességet kezd el felhasználni ugyanazon a hálózaton belül, a tényleges átviteli sebesség változhat a replikáció során. Ha a GetThroughput parancsot egy konfigurációs kiszolgálóról futtatja, az eszköz nem veszi figyelembe a védett virtuális gépeket és a folyamatban lévő replikációkat. Az átviteli sebesség mérésének eredménye eltérő lesz attól függően, hogy a védett virtuális gépek adatváltozása magas vagy alacsony a GetThroughput művelet végrehajtásakor.  Ajánlott az eszközt különböző időpontokban futtatni a profilkészítés során, hogy átláthassa, milyen átviteli sebességek érhetők el a különféle időpontokban. Az eszköz a legutóbb mért átviteli sebességet jeleníti meg a jelentésben.


##### <a name="example"></a>Példa
ASRDeploymentPlanner.exe **-Operation** GetThroughput **-Directory**  E:\vCenter1_ProfiledData **-VMListFile** E:\vCenter1_ProfiledData\ProfileVMList1.txt  **-StorageAccountName**  asrspfarm1 **-StorageAccountKey** by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==

>[!NOTE]
>
> * Olyan kiszolgálón futtassa az eszközt, amelynek a tároló- és processzorjellemzői megegyeznek a konfigurációs kiszolgálóéval
>
> * A replikációhoz a helyreállítási időkorlát 100%-os eléréséhez javasolt sávszélességet ossza ki. Ha a megfelelő sávszélesség kiosztása után sem nő az eszköz által jelentett elért átviteli sebesség, ellenőrizze a következőket:
>
> a. Ellenőrizze, hogy van-e olyan hálózati szolgáltatásminőség (QoS), amely korlátozná az Azure Site Recovery átviteli sebességét
>
> b. Ellenőrizze, hogy a hálózati késés minimalizálása érdekében az Azure Site Recovery-tároló a legközelebbi fizikai támogatott Microsoft Azure-régióban található-e
>
> c. Ellenőrizze a helyi tároló jellemzőit, és próbálja meg fejleszteni a hardvert (pl. váltás HDD-ről SSD-re stb.)
>
> d. Módosítsa az Azure Site Recovery beállításait a folyamatkiszolgálón [a replikációhoz használt hálózati sávszélesség növelésével](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).
>

##<a name="recommendations-with-desired-rpo-as-input"></a>Javaslatok a kívánt helyreállítási időkorlát (RPO) bemenetként való megadásával

###<a name="profiled-data"></a>Profilkészítés során létrehozott adatok

![Üzembehelyezés-tervező](./media/site-recovery-deployment-planner/profiled-data-period.png)

**Profiled data period** (Profilkészítési adatok létrehozásának időtartama) – A profilkészítés futtatásának időtartama. Az eszköz alapértelmezés szerint a profilkészítés során létrehozott minden adatot számításba vesz, kivéve, ha megadja a StartDate és EndDate beállításokat a jelentéskészítés során, és egy adott időszakról készít jelentést.

**Server Name** (Kiszolgáló neve) – Azon VMware vCenter vagy ESXi-gazdagép neve vagy IP-címe, amelynek a virtuális gépeiről a jelentés készül.

**Desired RPO** (Kívánt RPO) – Az üzembe helyezés kívánt helyreállítási időkorlátja. Alapértelmezés szerint a szükséges hálózati sávszélesség kiszámítása 15, 30 és 60 perces RPO értékkel történik. A kiválasztástól függően az eszköz frissíti az érintett értékeket a munkalapon. Ha használta a DesiredRPOinMin paramétert a jelentés elkészítése közben, akkor az az érték jelenik meg a Kívánt helyreállítási időkorlát legördülő listában.

###<a name="profiling-overview"></a>Profilkészítés áttekintése

![Üzembehelyezés-tervező](./media/site-recovery-deployment-planner/profiling-overview.png)

**Total Profiled Virtual Machines** (Minden virtuális gép, amelyről profil készült) – Azon virtuális gépek teljes száma, amelyekről profil készült. Ha a VMListFile olyan virtuális gépek nevét is tartalmazza, amelyekről nem készült profil, a rendszer ezeket figyelmen kívül hagyja a jelentéskészítés során, és nem számítja bele őket a profilkészítésben részt vevő virtuális gépek teljes számába.

**Compatible Virtual Machines** (Kompatibilis virtuális gépek) – Azon virtuális gépek száma, amelyek az Azure-ban védhetők az Azure Site Recovery használatával. Ez azon kompatibilis virtuális gépek teljes számát jelöli, amelyekhez a rendszer kiszámolta szükséges hálózati sávszélességet, az Azure Storage-fiókok, a Microsoft Azure-magok, valamint a konfigurációs és további folyamatkiszolgálók számát. Az egyes kompatibilis virtuális gépek részletei elérhetők a jelentés Compatible VMs (Kompatibilis virtuális gépek) munkalapján.

**Incompatible Virtual Machines** (Nem kompatibilis virtuális gépek) – Azon virtuális gépek száma, amelyekről profil készült, és amelyek nem védhetők Azure Site Recoveryvel. Az inkompatibilitás okait az alábbi, Nem kompatibilis virtuális gépek című szakaszban olvashatja. Ha a VMListFile olyan virtuális gépek nevét is tartalmazza, amelyekről nem készült profil, a rendszer nem számítja bele őket a nem kompatibilis virtuális gépek számába. Az ilyen virtuális gépek az Incompatible VMs (Nem kompatibilis virtuális gépek) munkalap végén, „Data not found” (Nem található adat) megjelöléssel láthatók.

**Desired RPO** (Kívánt RPO) – A kívánt helyreállítási időkorlát percben megadva. Az eszköz három helyreállítási időkorlát értékről készít jelentést: 15, 30 és 60 perc. Az alapértelmezett érték 15 perc. A jelentésben szereplő ajánlott sávszélesség attól függően változik, hogy melyik lehetőséget választja a munkalap jobb felső sarkában található Desired RPO (Kívánt RPO) legördülő listából. Ha egyéni értékű „-DesiredRPO” paraméterrel készítette el a jelentést, ez az egyéni érték jelenik meg alapértelmezett értékként a Kívánt helyreállítási időkorlát legördülő listában.

###<a name="required-network-bandwidth-mbps"></a>Szükséges hálózati sávszélesség (Mbps)

![Üzembehelyezés-tervező](./media/site-recovery-deployment-planner/required-network-bandwidth.png)

**To meet RPO 100% of the time:** (Az RPO betartása az idő 100%-ában): Ez az ajánlott sávszélesség (Mbps), amelyet ki kell osztania, ha mindig garantáltan el kívánja érni a kívánt helyreállítási időkorlátot. Ekkora sávszélességet kell kijelölnie az összes kompatibilis virtuális gép stabil állapotú változásreplikációjához, ha biztosan ki kívánja zárni a helyreállítási időkorlát megszegését.

**To meet RPO 90% of the time** (Az RPO betartása az idő 90%-ában) Ha a szélessávú kapcsolat díjszabása miatt vagy egyéb okból kifolyólag nincs lehetősége arra, hogy akkora sávszélességet osszon ki, amellyel a kívánt helyreállítási időkorlát mindig betartható, akkor kioszthat egy alacsonyabb sávszélesség-értéket, amellyel az idő 90%-ában betarthatja a kívánt RPO-t. Az alacsonyabb sávszélesség-kiosztás következményeinek áttekintéséhez a jelentés lehetőségelemzést biztosít elemzést a helyreállítási időkorlát várható megszegéseinek számáról és időtartamáról.

**Achieved Throughput:** (Elért átviteli sebesség): Az átviteli sebesség a GetThroughput parancs futtatásához használt kiszolgáló és az Azure Storage-fióknak helyet adó Microsoft Azure-régió között. Hozzávetőleges átviteli sebességet jelöl, amely akkor érhető el, ha a kompatibilis virtuális gépeket az Azure Site Recoveryvel védi, feltéve, hogy a konfigurációs kiszolgáló vagy folyamatkiszolgáló tárolója és hálózati jellemzői megegyeznek azon kiszolgálóéval, ahol az eszközt futtatta. Az elért átviteli sebesség az átviteli sebesség a GetThroughput parancs futtatásához használt kiszolgáló és az Azure Storage-fióknak helyet adó Microsoft Azure-régió között. Hozzávetőleges átviteli sebességet jelöl, amely akkor érhető el, ha a kompatibilis virtuális gépeket az Azure Site Recoveryvel védi, feltéve, hogy a konfigurációs kiszolgáló vagy folyamatkiszolgáló tárolója és hálózati jellemzői megegyeznek azon kiszolgálóéval, ahol az eszközt futtatta.

A replikációhoz a helyreállítási időkorlát 100%-os eléréséhez javasolt sávszélességet ossza ki. Ha a megfelelő sávszélesség kiosztása után sem nő az eszköz által jelentett elért átviteli sebesség, ellenőrizze a következőket:

a.    Ellenőrizze, hogy van-e olyan hálózati szolgáltatásminőség (QoS), amely korlátozná az Azure Site Recovery átviteli sebességét

b.    Ellenőrizze, hogy a hálózati késés minimalizálása érdekében az Azure Site Recovery-tároló a legközelebbi fizikai támogatott Microsoft Azure-régióban található-e

c.    Ellenőrizze a helyi tároló jellemzőit, és próbálja meg fejleszteni a hardvert (pl. váltás HDD-ről SSD-re stb.)

d. Módosítsa az Azure Site Recovery beállításait a folyamatkiszolgálón [a replikációhoz használt hálózati sávszélesség növelésével](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

Ha az eszközt olyan konfigurációs kiszolgálón vagy folyamatkiszolgálón futtatja, amely már rendelkezik védett virtuális gépekkel, futtassa az eszközt többször is, mert az elért átviteli sebesség eltérő lesz az aktuális adatforgalomtól függően.

Az Azure Site Recovery minden vállalati üzemelő példánya esetében javasoljuk az [ExpressRoute](https://aka.ms/expressroute) használatát.

###<a name="required-azure-storage-accounts"></a>Szükséges Azure Storage-fiókok
Ez a diagram az összes kompatibilis virtuális gép védelméhez szükséges (standard és prémium szintű) Azure Storage-fiókok teljes számát mutatja.  A [Recommended VM placement plan](site-recovery-deployment-planner.md#vm-storage-placement) (Ajánlott virtuálisgép-elhelyezési terv) elemre kattintva megtudhatja, melyik tárfiókot érdemes használni a különféle virtuális gépekhez.  

![Üzembehelyezés-tervező](./media/site-recovery-deployment-planner/required-azure-storage-accounts.png)

###<a name="required-number-of-azure-cores"></a>Szükséges Azure-magok száma
Ez az összes kompatibilis virtuális gép feladatátvétele vagy feladatátvételi tesztje előtt kiosztandó magok teljes száma. Ha az előfizetés nem rendelkezik a megfelelő számú maggal, az Azure Site Recovery nem tudja létrehozni a virtuális gép(ek)et a feladatátvételi teszt vagy a tényleges feladatátvétel alatt.

![Üzembehelyezés-tervező](./media/site-recovery-deployment-planner/required-number-of-azure-cores.png)

###<a name="required-on-premises-infrastructure"></a>Szükséges helyszíni infrastruktúra
Ez az összes virtuális gép védelméhez konfigurálandó konfigurációs kiszolgálók és további folyamatkiszolgálók teljes száma. Ez a szám a legnagyobb konfiguráció támogatott [korlátain](https://aka.ms/asr-v2a-on-prem-components) alapul. Ez a korlát lehet a napi adatforgalom vagy a védett virtuális gépek maximális száma (virtuális gépenként átlagosan három lemezt feltételezve). Ha eléri valamelyik korlátot a konfigurációs kiszolgálón vagy a további folyamatkiszolgálón, az eszköz további kiszolgálókat javasol. A napi összes adatforgalom és a védett lemezek teljes számának részletei az [Input](site-recovery-deployment-planner.md#input) (Bemenet) munkalapon találhatók.

![Üzembehelyezés-tervező](./media/site-recovery-deployment-planner/required-on-premises-infrastructure.png)

###<a name="what-if-analysis"></a>Lehetőségelemzés
Ez az elemzés kiemeli, hogy hányszor szegné meg a helyreállítási időkorlátot a profilkészítés időtartama alatt, ha alacsonyabb sávszélességet állít be, amely az idő 90%-ában biztosítaná a kívánt RPO betartását. A helyreállítási időkorlát túllépése bármelyik nap egy vagy több alkalommal is előfordulhat – az ábra csak a napi RPO-csúcsértéket mutatja.
Az elemzés alapján eldöntheti, hogy az RPO-túllépések teljes száma az összes nap alatt, valamint a napi RPO-csúcsérték elfogadható-e a megadott alacsonyabb sávszélesség esetén. Ha elfogadható, akkor lefoglalhatja az alacsonyabb sávszélességet a replikációhoz. Ha nem, akkor foglalja le a javasolt magasabb sávszélességet ahhoz, hogy az idő 100%-ában betarthassa az RPO-t.

![Üzembehelyezés-tervező](./media/site-recovery-deployment-planner/what-if-analysis.png)

###<a name="recommended-vm-batch-size-for-initial-replication"></a>A virtuálisgép-köteg ajánlott mérete a kezdeti replikációhoz
A szakasz arra tesz javaslatot, hogy hány virtuális gép védhető meg egyszerre úgy, hogy a kezdeti replikáció 72 órán belül befejeződjön (ez az érték konfigurálható: a jelentés létrehozásakor használja a GoalToCompleteIR paramétert a módosításához) a javasolt sávszélesség és a kívánt RPO 100%-os betartása mellett az üzembe helyezés során.  A diagram egy sávszélesség-tartományt és azt a virtuálisgép-köteg méretet mutatja, amellyel a kezdeti replikáció 72 órán belüli befejezhető. Ez a szám az összes kompatibilis virtuális gépen és a virtuális gépek átlagos észlelt méretén alapul.  

A nyilvános előzetes verzióban a jelentés nem határozza meg, hogy melyik virtuális gépeket foglalja bele egy kötegbe. A Compatible VMs (Kompatibilis virtuális gépek) munkalapon látható lemezméretek alapján kikeresheti az egyes virtuális gépek méretét, és kiválaszthatja az egyes kötegekhez tartozó virtuális gépeket, vagy választhat a számítási feladatok ismert jellemzői alapján is.  A kezdeti replikáció befejezési ideje a virtuális gépek lemezeinek tényleges méretével, a felhasznált lemezterülettel és az elérhető hálózati átviteli sebességgel arányosan változik.

![Üzembehelyezés-tervező](./media/site-recovery-deployment-planner/recommended-vm-batch-size.png)

###<a name="growth-factor-and-percentile-values-used"></a>A használt növekedési tényező és százalékértékek
A munkalap alján található szakasz a profilkészítésben részt vevő virtuális gépek összes teljesítményszámlálójához használt százalékértéket (az alapértelmezett érték 95%), valamint az összes számításban használt növekedési tényező százalékértékét (az alapértelmezett érték 30%) mutatja.

![Üzembehelyezés-tervező](./media/site-recovery-deployment-planner/max-iops-and-data-churn-setting.png)

##<a name="recommendations-with-available-bandwidth-as-input"></a>Javaslatok az elérhető sávszélesség bemenetként való megadásával

![Üzembehelyezés-tervező](./media/site-recovery-deployment-planner/profiling-overview-bandwidth-input.png)

Előfordulhat olyan helyzet, hogy legfeljebb x Mbps sávszélességet tud kiosztani az Azure Site Recovery replikációjára. Az eszközzel megadhatja a rendelkezésre álló sávszélességet (a jelentéskészítés közben, a -Bandwidth paraméterrel), és megkapja az elérhető RPO-t percben megadva. Az elérhető RPO-értéknek ismeretében eldöntheti, hogy további sávszélességet kell kiosztania, vagy elfogadja a vészhelyreállítási megoldást ezzel az RPO-val.

![Üzembehelyezés-tervező](./media/site-recovery-deployment-planner/achievable-rpos.png)

##<a name="input"></a>Input (Bemenet)
Az Input (Bemenet) oldal áttekintést nyújt arról a VMware-környezetről, amelyről profilt készített.

![Üzembehelyezés-tervező](./media/site-recovery-deployment-planner/Input.png)

**Start Date and End Date** (Kezdő és záró dátum) – Azon profilkészítési adatok kezdő és záró dátuma, amelyekről jelentést kíván készíteni. Alapértelmezés szerint a kezdő dátum az a dátum, amikor a profilkészítés megkezdődött, a záró dátum pedig az a dátum, amikor a profilkészítés leáll.  Ezek lehetnek a StartDate és EndDate értékek, ha a jelentést ezekkel a paraméterekkel hozza létre. Kezdő és záró dátum: azon profilkészítési adatok kezdő és záró dátuma, amelyekről jelentést kíván készíteni. Alapértelmezés szerint a kezdő dátum az a dátum, amikor a profilkészítés megkezdődött, a záró dátum pedig az a dátum, amikor a profilkészítés leáll.  Ezek lehetnek a StartDate és EndDate értékek, ha a jelentést ezekkel a paraméterekkel hozza létre.

**Total number of profiling days** (Profilkészítés napjainak teljes száma) – A jelentéskészítés kezdő és záró dátuma között eltelt profilkészítési napok teljes száma. A profilkészítés napjainak teljes száma a jelentéskészítés kezdő és záró dátuma között eltelt profilkészítési napok teljes száma.

**Number of compatible virtual machines** (Kompatibilis virtuális gépek száma) – Ez azon kompatibilis virtuális gépek teljes számát jelöli, amelyekhez a rendszer kiszámolta szükséges hálózati sávszélességet, valamint a szükséges Azure Storage-fiókok, Microsoft Azure-magok, illetve konfigurációs és további folyamatkiszolgálók számát.
Total number of disks across all compatible virtual machines (Lemezek teljes száma az összes kompatibilis virtuális gépen) – Az összes kompatibilis virtuális gépen található összes lemez. Ez a szám az egyik olyan bemenet, amely alapján a rendszer meghatározza az üzemelő példányban használandó konfigurációs kiszolgálók és további folyamatkiszolgálók számát.

**Average number of disks per compatible virtual machine** (Lemezek átlagos száma kompatibilis virtuális gépenként) – Az összes kompatibilis virtuális gép alapján számított átlagos lemezszám.

**Average disk size (GB)** (Átlagos lemezméret (GB)) – Az összes kompatibilis virtuális gép alapján számított átlagos lemezméret.

**Desired RPO (minutes)** (Kívánt RPO (perc)) – Az RPO alapértelmezett értéke vagy a jelentéskészítéskor a DesiredRPO paraméterhez szükséges sávszélesség becslése érdekében megadott érték.

**Desired bandwidth (Mbps)** (Kívánt sávszélesség (Mbps)) – A jelentéskészítéskor az elérhető RPO becslése érdekében a „Bandwidth” paraméterhez megadott érték.

**Observed typical data churn per day (GB)** (Megfigyelt átlagos napi adatváltozás (GB)) – Az összes profilkészítési napon megfigyelt átlagos adatváltozás. Ez a szám az egyik olyan bemenet, amely alapján a rendszer meghatározza az üzemelő példányban használandó konfigurációs kiszolgálók és további folyamatkiszolgálók számát.


##<a name="vm-storage-placement"></a>Virtuálisgép-tároló elhelyezése

![Üzembehelyezés-tervező](./media/site-recovery-deployment-planner/vm-storage-placement.png)

**Disk Storage Type** (Lemezes tárolás típusa) – Az Elhelyezendő virtuális gépek oszlopban említett összes virtuális gép replikációjához használt Standard vagy Premium szintű Azure Storage-fiók.

**Suggested Prefix** (Javasolt előtag) – Az Azure Storage-fiók elnevezéséhez javasolt háromkarakteres előtag. Saját előtagot is használhat, de az eszköz által javasolt nevek követik az [Azure Storage-fiókok partíció-elnevezési szabályait](https://aka.ms/storage-performance-checklist).

**Suggested Account Name** (Javasolt fióknév) – Azt jelzi, hogy kell kinéznie az Azure Storage-fiók nevének a javasolt előtag használata után. Cserélje le a < > szimbólumok közötti nevet a kívánt bemenetre.

**Log Storage Account:** (Naplótárolási fiók) – Minden replikációs naplót egy standard szintű Azure Storage-fiók tárol. A prémium szintű Azure Storage-fiókokba replikált virtuális gépek esetében üzembe kell helyeznie egy további standard szintű Azure Storage-fiókot a naplók tárolására. Egyetlen standard szintű naplótárolási fiókot több prémium szintű replikációs tárfiók is használhat. A standard szintű tárfiókokba replikált virtuális gépek ugyanazt a tárfiókot használják a naplókhoz is.

**Suggested Log Account Name** (Javasolt naplótárolási fióknév) – Azt jelzi, hogy kell kinéznie a naplókat tároló Azure Storage-fiók nevének a javasolt előtag használata után. Cserélje le a < > szimbólumok közötti nevet a kívánt bemenetre.

**Placement Summary** (Elhelyezés összegzése) – Az Azure Storage-fiókon található virtuális gépeknek a replikáció és feladatátvételi teszt vagy tényleges feladatátvétel során mért teljes terhelését összegzését biztosítja. Tartalmazza az Azure Storage-fiókhoz rendelt virtuális gépek teljes számát, az Azure Storage-fiókba elhelyezni kívánt összes virtuális gép összesített olvasási/írási IOPS-értékét, az összesített írási (replikációs) IOPS-értéket, az összes lemez teljes kiosztott méretét, valamint a lemezek teljes számát.

**Virtual Machines to Place** (Elhelyezendő virtuális gépek) – Felsorolja az összes virtuális gépet, amelyet az optimális teljesítmény és kihasználtság érdekében az adott Azure Storage-fiókon ajánlott elhelyezni.

##<a name="compatible-vms"></a>Kompatibilis virtuális gépek
![Üzembehelyezés-tervező](./media/site-recovery-deployment-planner/compatible-vms.png)

**VM Name** (Virtuális gép neve) – A virtuális gép neve vagy IP-címe, a jelentés elkészítésekor a VMListFile fájlban használt formában. Ez az oszlop a virtuális gépekhez csatolt lemezek (VMDK-k) listáját is megjeleníti.

**VM Compatibility** (Virtuális gép kompatibilitása) – Két értéke van: Yes (Igen)/ Yes (Igen)*. A Yes* (Igen) azokra az esetekre vonatkozik, amikor a virtuális gép megfelel egy [prémium szintű Azure Storage-hoz](https://aka.ms/premium-storage-workload), és a profilkészítés során megállapított magas adatváltozású vagy IOPS-értékű lemez megfelel a P20-as vagy P30-as kategóriának, de a lemez mérete miatt a rendszer P10-es vagy P20-as kategóriába sorolja be. Az Azure Storage a lemez mérete alapján dönti el, hogy melyik prémium szintű lemeztípushoz rendelje hozzá a lemezt: 128 GB alatt P10, 128 és 512 GB között P20, illetve 512 és 1023 GB között P30. Tehát ha a számítási feladatok jellemzői alapján egy lemez a P20-as vagy P30-as kategóriába tartozik, de a mérete alapján egy alacsonyabb prémium szintű tárolólemez-típusba kerül, az eszköz Yes* (Igen*) megjelöléssel látja el ezeket a virtuális gépeket. Emellett az eszköz azt is javasolja, hogy módosítsa a forráslemez méretét, hogy a lemez megfeleljen a megfelelő ajánlott prémium szintű tárolólemez-típusnak, vagy hogy módosítsa a céllemez típusát a feladatátvétel után.
A tároló típusa Standard vagy Premium.

**Suggested Prefix** (Javasolt előtag) – Az Azure Storage-fiók három karakterből álló előtagja

**Storage Account** (Storage-fiók) – A fiók neve a javasolt előtaggal

**R/W IOPS (with Growth Factor)** (Írási/olvasási IOPS (növekedési tényezővel)) – A lemez IOPS-csúcsértéke (alapértelmezés szerint a 95. százalékos érték), beleértve a későbbi növekedési faktort is (az alapértelmezett érték 30%). Vegye figyelembe, hogy a virtuális gép teljes írási/olvasási IOPS-értéke nem mindig egyezik meg az egyes lemezek írási/olvasási IOPS-értékeinek összegével. Ez azért van, mert a virtuális gép írási/olvasási IOPS-csúcsértéke az egyes lemezeken a profilkészítési időszak alatt percenként mért írási/olvasási IOPS-értékek legmagasabb összege.

**Data Churn in Mbps (with Growth Factor)** (Adatváltozás (Mbps) (növekedési tényezővel)) – A lemez adatváltozásának csúcsértéke (alapértelmezés szerint a 95. százalékérték), beleértve a későbbi növekedési faktort is (alapértelmezés szerint 30%). Vegye figyelembe, hogy a virtuális gép teljes adatváltozása nem mindig egyezik meg az egyes lemezek adatváltozásának összegével. Ez azért van, mert a virtuális gép adatváltozásának csúcsértéke az egyes lemezeken a profilkészítési időszak alatt percenként mért adatváltozás-értékek legmagasabb összege.

**Azure VM Size** (Azure-beli virtuális gép mérete) – Az Azure Compute-beli hozzárendelt virtuális gép ideális mérete az adott helyszíni virtuális gép esetén. Ez a hozzárendelés a helyszíni virtuális gép memóriája, a lemezek/magok/hálózati adapterek száma és az írási/olvasási IOPS-érték alapján történik. A javaslat mindig az Azure-beli virtuális gép legkisebb mérete, amely megfelel a helyszíni virtuális gép összes jellemzőjének.

**Number of Disks** (Lemezek száma) – A virtuális gép lemezeinek (VMDK-inak) teljes száma

**Disk size (GB)** (Lemez mérete (GB)) – A virtuális gép összes lemeze számára kiosztott teljes méret. Az eszköz a virtuális gép egyes lemezeinek méretét is megjeleníti.

**Cores** (Magok) – A virtuális gép processzormagjainak száma.

**Memory (MB)** (Memória (MB)) – A virtuális gép fizikai memóriája (RAM).

**NICs** (Hálózati adapterek) – A virtuális gép hálózati adaptereinek száma.

##<a name="incompatible-vms"></a>Nem kompatibilis virtuális gépek

![Üzembehelyezés-tervező](./media/site-recovery-deployment-planner/incompatible-vms.png)

**VM Name** (Virtuális gép neve) – A virtuális gép neve vagy IP-címe, a jelentés elkészítésekor a VMListFile fájlban használt formában. Ez az oszlop a virtuális gépekhez csatolt lemezek (VMDK-k) listáját is megjeleníti.

**VM Compatibility** (Virtuális gép kompatibilitása) – Azt jelzi, hogy miért nem kompatibilis az adott virtuális gép az Azure Site Recoveryvel való használattal. Az indokok a virtuális gép minden nem kompatibilis lemezénél meg vannak adva, és a következők lehetnek a közzétett Azure Storage-[korlátok](https://aka.ms/azure-storage-scalbility-performance) alapján.

* Disk size > 1023 GB – Azure Storage currently does not support > 1 TB disk sizes (Lemezméret > 1023 GB – Az Azure Storage jelenleg nem támogatja az 1 TB-nál kisebb lemezméretet)
* Total VM size (replication + TFO) exceeds supported Azure Storage account size limit (35 TB) (A virtuális gép teljes mérete (replikáció + feladatátvételi teszt) meghaladja az Azure Storage-fiók támogatott méretkorlátját (35 TB)) – Ez általában akkor fordul el, ha a virtuális gép egyetlen lemezzel rendelkezik, amelynek bizonyos teljesítményjellemzői meghaladják a Microsoft Azure / Azure Site Recovery standard tárolókra vonatkozó támogatott korlátait. Ennek eredményeképp a virtuális gép átkerül a prémium szintű tárolózónába. A prémium szintű Azure Storage-fiókok maximális támogatott mérete azonban 35 TB, és egyetlen védett virtuális gép nem védhető több tárfiókon keresztül. Ügyeljen arra is, hogy amikor a feladatátvételi tesztet védett virtuális gépen hajtja végre, akkor az ugyanazon a tárfiókon fut, ahol a replikáció folyik. Ez azt jelenti, hogy a lemez méretének kétszeresét kell kiosztani, hogy a replikáció is folytatódhasson, és ezzel párhuzamosan a feladatátvételi teszt is sikeres legyen.
* Source IOPS exceeds supported Azure Storage IOPS limit of 5000 per disk (A forrás IOPS meghaladja az Azure Storage lemezenkénti 5000-es IOPS-korlátját)
* Source IOPS exceeds supported Azure Storage IOPS limit of 80,000 per VM (A forrás IOPS meghaladja az Azure Storage lemezenkénti 80 000-es IOPS-korlátját)
* Average data churn exceeds supported Azure Site Recovery data churn limit of 10 MBps for average IO size for disk (Az átlagos adatváltozás meghaladja az Azure Site Recovery lemezenkénti átlagos IO-mérethez megadott 10 MBps értékű adatváltozási korlátját)
* Total data churn across all disks on the VM exceeds the maximum supported Azure Site Recovery data churn limit of 54 MBps per VM (Az összes virtuális gépre vonatkozó teljes adatváltozás meghaladja az Azure Site Recovery virtuális gépenkénti 54 Mbps-os támogatott adatváltozási korlátját)
* Average effective write IOPS exceeds supported Azure Site Recovery IOPS limit of 840 for disk (Az átlagos tényleges írási IOPS-érték meghaladja az Azure Site Recovery lemezenkénti 840-es támogatott IOPS-korlátját)
* Calculated snapshot storage exceeding the supported snapshot storage limit of 10 TB (A kiszámított pillanatkép-tároló mérete meghaladja a 10 TB-os támogatott méretet)

**R/W IOPS (with Growth Factor)** (Írási/olvasási IOPS (növekedési tényezővel)) – A lemez IOPS-csúcsértéke (alapértelmezés szerint a 95. százalékos érték), beleértve a későbbi növekedési faktort is (az alapértelmezett érték 30%). Vegye figyelembe, hogy a virtuális gép teljes írási/olvasási IOPS-értéke nem mindig egyezik meg az egyes lemezek írási/olvasási IOPS-értékeinek összegével. Ez azért van, mert a virtuális gép írási/olvasási IOPS-csúcsértéke az egyes lemezeken a profilkészítési időszak alatt percenként mért írási/olvasási IOPS-értékek legmagasabb összege.

**Data Churn in Mbps (with Growth Factor)** (Adatváltozás (Mbps) (növekedési tényezővel)) – A lemez adatváltozásának csúcsértéke (alapértelmezés szerint a 95. százalékérték), beleértve a későbbi növekedési faktort is (alapértelmezés szerint 30%). Vegye figyelembe, hogy a virtuális gép teljes adatváltozása nem mindig egyezik meg az egyes lemezek adatváltozásának összegével. Ez azért van, mert a virtuális gép adatváltozásának csúcsértéke az egyes lemezeken a profilkészítési időszak alatt percenként mért adatváltozás-értékek legmagasabb összege.

**Number of Disks** (Lemezek száma) – A virtuális gép lemezeinek (VMDK-inak) teljes száma

**Disk size (GB)** (Lemez mérete (GB)) – A virtuális gép összes lemeze számára kiosztott teljes méret. Az eszköz a virtuális gép egyes lemezeinek méretét is megjeleníti.

**Cores** (Magok) – A virtuális gép processzormagjainak száma.

**Memory (MB)** (Memória (MB)) – A virtuális gép fizikai memóriája (RAM).

**NICs** (Hálózati adapterek) – A virtuális gép hálózati adaptereinek száma.


##<a name="azure-site-recovery-limits"></a>Az Azure Site Recovery korlátai

**Replikáció tárolási célja** | **Forráslemez átlagos I/O-mérete** |**Forráslemez átlagos adatváltozása** | **Forráslemez teljes napi adatváltozása**
---|---|---|---
Standard szintű Storage | 8 KB    | 2 MB/s | Lemezenként&168; GB
Prémium szintű P10 lemez | 8 KB    | 2 MB/s | Lemezenként&168; GB
Prémium szintű P10 lemez | 16 KB | 4 MB/s |    Lemezenként&336; GB
Prémium szintű P10 lemez | 32 KB vagy több | 8 MB/s | Lemezenként&672; GB
Prémium szintű P20/P30 lemez | 8 KB    | 5 MB/s | Lemezenként&421; GB
Prémium szintű P20/P30 lemez | 16 KB vagy több |10 MB/s    | Lemezenként&842; GB


Ezek átlagos értékek, amelyek 30%-os IO-átfedést feltételeznek. Az Azure Site Recovery képes magasabb átviteli sebesség kezelésére az átfedési arány, a nagyobb írási méretek és a számítási feladatok tényleges I/O-viselkedése alapján. A fenti számok körülbelül 5 perces átlagos hátralékot feltételeznek. Ez azt jelenti, hogy a rendszer 5 percen belül feldolgozza a feltöltött adatokat, és létrehoz egy helyreállítási pontot.

A fent közzétett korlátok saját tesztjeinken alapulnak, de nem fedhetik le az alkalmazások minden lehetséges I/O-kombinációját. A tényleges eredmények a saját alkalmazásának I/O-műveletei alapján változnak. A legjobb eredmények érdekében még az üzembe helyezés megtervezése után is ajánlott az alkalmazás alapos tesztelése feladatátvételi tesztek használatával, így valós képet kaphat a teljesítményről.

##<a name="release-notes"></a>Kibocsátási megjegyzések
Az Azure Site Recovery Deployment Planner 1.0-s nyilvános előzetes verziója az alábbi ismert hibákat tartalmazza, amelyeket az elkövetkező frissítések fognak elhárítani.

* Az eszköz csak a VMware – Azure forgatókönyvek esetén működik, Hyper-V – Azure irányú üzembe helyezések esetében nem. A Hyper-V – Azure forgatókönyvekhez használja a [Hyper-V Capacity Planner eszközt](./site-recovery-capacity-planning-for-hyper-v-replication.md).
* A GetThroughput művelet nem támogatott az US Government és kínai Microsoft Azure-régiókban.
* Az eszköz nem képes profilt készíteni a virtuális gépekről, ha a vCenter-kiszolgáló különböző ESXi-gazdagépein több azonos nevű vagy IP-című virtuális gép található. Ebben a verzióban az eszköz kihagyja a profilkészítést abban az esetben, ha a VMListFile fájlban ismétlődik egy virtuális gép neve vagy IP-címe. Ez áthidalható úgy, hogy a vCenter-kiszolgáló helyett az ESXi-gazdagéppel készíti el a virtuális gépek profilját. Minden ESXi-gazdagép esetében futtatnia kell egy példányt.

