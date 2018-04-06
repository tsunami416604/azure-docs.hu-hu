---
title: HPC Pack OpenFOAM futó Linux virtuális gépek |} Microsoft Docs
description: Az Azure a Microsoft HPC Pack fürt központi telepítése, és egy OpenFOAM feladat futtatása több Linux számítási csomóponton az RDMA-hálózaton.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: c0bb1637-bb19-48f1-adaa-491808d3441f
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 07/22/2016
ms.author: danlep
ms.openlocfilehash: f43790d3495e1c09730e90b5077ec840731a7d83
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="run-openfoam-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>Az OpenFoam futtatása a Microsoft HPC Packkal Azure-beli linuxos RDMA-fürtön
Ez a cikk az Azure virtuális gépeken OpenFoam futtatásához egy módszert mutat. Itt, a Microsoft HPC Pack-fürt Linux számítási csomópontok az Azure és a Futtatás telepít egy [OpenFoam](http://openfoam.com/) Intel MPI feladatot. Az RDMA-kompatibilis Azure virtuális gépeken használhatja a számítási csomópontok, hogy a számítási csomópontok Azure RDMA hálózati kommunikációra. Más OpenFoam Azure rendszerben való futtatásra a választható lehetőségek teljesen konfigurált kereskedelmi képek elérhető a piactéren, például a UberCloud tartozó [OpenFoam 2.3 a CentOS 6](https://azure.microsoft.com/marketplace/partners/ubercloud/openfoam-v2dot3-centos-v6/), és futtatja a [Azure Batch](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/). 

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

(A nyissa meg a mező művelet és adatkezelési) OpenFOAM egy nyílt forráskódú számítási folyadékból dynamics (CFD) csomagot, amely széles körben használt mérnöki és tudományos, kereskedelmi és a academic szervezetekben. Ez magában foglalja az eszközök meshing, nevezetesen snappyHexMesh, egy parallelized mesher összetett CAD-geometriája, és előtti és utáni feldolgozásra. Szinte minden folyamatok párhuzamosan, így a felhasználók teljes körű kihasználása érdekében a számítógép a rendelkezésükre futnak.  

A Microsoft HPC Pack felügyeleti teendők központjaként HPC és párhuzamos alkalmazások, beleértve a MPI alkalmazásokat, a fürtökön a Microsoft Azure virtuális gépek futtatásához szolgáltatásokat biztosítja. HPC Pack támogatja a virtuális gépeken telepített HPC Pack-fürtben lévő Linux alkalmazások csomópontjain futó Linux HPC is. Lásd: [Ismerkedés az Azure-ban HPC Pack-fürtben lévő Linux számítási csomópontok](hpcpack-cluster.md) használata Linux bemutatása a számítási csomópontok HPC Pack.

> [!NOTE]
> Ez a cikk bemutatja, hogyan HPC Pack a Linux MPI munkaterhelések futtatásához. Azt feltételezi, hogy rendelkezik-e bizonyos fokú ismeretét, Linux rendszer felügyeleti és a Linux fürtökön MPI munkaterheket futtatnak. Ha MPI és különbözik az ebben a cikkben szereplő OpenFOAM-verziót használ, lehetséges, hogy módosítani az egyes telepítési és konfigurációs lépéseket. 
> 
> 

## <a name="prerequisites"></a>Előfeltételek
* **HPC Pack fürt RDMA-kompatibilisek-e a Linux és a számítási csomópontok** - fürt üzembe helyezése HPC Pack méretű A8, A9, H16r, vagy H16rm Linux számítási csomópontok használatával egy [Azure Resource Manager sablon](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) vagy egy [Azure PowerShell parancsfájl](hpcpack-cluster-powershell-script.md). Lásd: [Ismerkedés az Azure-ban HPC Pack-fürtben lévő Linux számítási csomópontok](hpcpack-cluster.md) az Előfeltételek és az egyik beállítási mód lépéseit. Ha a PowerShell parancsfájl központi telepítés lehetőséget választja, lásd: a minta konfigurációs fájlt a mintafájlok, ez a cikk végén. Használja ezt a konfigurációt a fürt üzembe helyezése Azure-alapú HPC Pack mérete A8 Windows Server 2012 R2 átjárócsomópont és a számítási csomópontok 2 mérete A8 SUSE Linux Enterprise Server 12. Az előfizetés és a szolgáltatás nevének megfelelő értékeit helyettesítse. 
  
  **További tudnivaló**
  
  * Linux RDMA hálózati Előfeltételek az Azure-ban, lásd: [nagy teljesítményű számítási Virtuálisgép-méretek](../../windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
  * A Powershell parancsfájl központi telepítési beállítás használatakor telepítse az RDMA hálózati kapcsolat használata egy felhőalapú szolgáltatás belül az összes Linux számítási csomópontokat.
  * Miután a Linux-csomópontok üzembe, csatlakozzon az SSH további felügyeleti feladatok végrehajtásához. Az SSH-kapcsolat adatai található az egyes Linux virtuális gépek Azure-portálon.  
* **Intel MPI** - OpenFOAM SLES 12 HPC számítási csomópontok az Azure, a futtatásához telepítenie kell az Intel MPI könyvtár 5 futásidejű a [Intel.com hely](https://software.intel.com/en-us/intel-mpi-library/). (Intel MPI 5 előre telepítve van a CentOS-alapú HPC képek.)  Egy későbbi lépésben szükség esetén telepítse az Intel MPI a Linux számítási csomóponton. Ebben a lépésben előkészítése Intel regisztrálása után, hivatkozásra a megerősítési e-mailben a kapcsolódó weblapra. Ezután másolja az Intel MPI megfelelő verzióját a .tgz fájl letöltési hivatkozását. Ez a cikk Intel MPI verzió 5.0.3.048 alapul.
* **OpenFOAM forrás csomag** -a Linux, a OpenFOAM forrás Pack szoftver töltse le a [OpenFOAM Foundation webhely](http://openfoam.org/download/2-3-1-source/). Ez a cikk a forrás csomag verziója letölthető 2.3.1 OpenFOAM-2.3.1.tgz van alapján. Kövesse az utasításokat, ezzel csomagolja ki és OpenFOAM lefordítani a Linux számítási csomóponton a cikk későbbi részében.
* **EnSight** (nem kötelező) – a OpenFOAM szimuláció, töltse le és telepítse az eredmények megtekintéséhez a [EnSight](https://www.ceisoftware.com/download/) képi megjelenítés és elemzések program. Licencelési és letöltési információkat a EnSight helyen vannak.

## <a name="set-up-mutual-trust-between-compute-nodes"></a>Kölcsönös, a számítási csomópontok közötti megbízhatósági kapcsolat beállítása
Megbízhatósági kapcsolat áll fenn a csomópontok egy kereszt-csomópont feladat futó Linux több csomópont szükséges (által **rsh** vagy **ssh**). Ha a Microsoft HPC Pack IaaS telepítési parancsfájl a HPC Pack fürt létrehozásához, a parancsfájl automatikusan beállítja a rendszergazdai fiókhoz megadott állandó kölcsönös megbízhatósági. A nem rendszergazda felhasználók számára hoz létre a fürt a tartományban a csomópontok közötti ideiglenes kölcsönös megbízhatósági beállítani, ha egy feladat őket rendelt kell, és szüntesse meg a kapcsolat, a feladat befejezése után. Megbízhatósági kapcsolat létrehozása az egyes felhasználók, adja meg a fürthöz, a megbízhatósági kapcsolathoz használó HPC Pack egy RSA kulcspár.

### <a name="generate-an-rsa-key-pair"></a>Egy RSA kulcspár létrehozása
Könnyen kulcspárlétrehozás egy RSA, egy nyilvános kulcs és a titkos kulcsot tartalmazó által a Linux operációs rendszert futtató **ssh-keygen** parancsot.

1. Jelentkezzen be a Linux-számítógép.
2. Futtassa az alábbi parancsot:
   
   ```
   ssh-keygen -t rsa
   ```
   
   > [!NOTE]
   > Nyomja le az **Enter** az alapértelmezett beállításokkal, a parancs befejeződéséig. Ne adjon meg egy jelszót. Amikor a rendszer kéri a jelszót, csak Entert **Enter**.
   > 
   > 
   
   ![Egy RSA kulcspár létrehozása][keygen]
3. Módosítsa a könyvtárat a ~/.ssh könyvtárba. A titkos kulcs id_rsa és a nyilvános kulcsot a id_rsa.pub tárolódik.
   
   ![Nyilvános és titkos kulcsai][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>Adja hozzá a kulcspárt a HPC Pack fürthöz
1. A távoli asztali kapcsolat az átjárócsomópont a HPC Pack rendszergazdai fiókkal (a rendszergazdai fiók beállítása az üzembe helyezési parancsfájl futtatása).
2. Szabványos Windows Server-eljárások segítségével hozzon létre egy tartományfelhasználói fiókot a fürt Active Directory-tartományban. Az Active Directory – felhasználók és számítógépek eszközt például használatát az átjárócsomópont. Ebben a cikkben szereplő példák azt feltételezik, hogy hpclab\hpcuser nevű tartományi felhasználót kell létrehozni.
3. Hozzon létre egy C:\cred.xml nevű fájlt, és az RSA-kulcs adatait átmásolja azt. A mintafájl cred.xml van ez a cikk végén.
   
   ```
   <ExtendedData>
     <PrivateKey>Copy the contents of private key here</PrivateKey>
     <PublicKey>Copy the contents of public key here</PublicKey>
   </ExtendedData>
   ```
4. Nyisson meg egy parancssort, és írja be a következő parancsot a hpclab\hpcuser fiók a hitelesítő adatok beállításához. Használja a **extendeddata** paraméter felelt meg a C:\cred.xml fájl hozott létre a fő adatok nevét.
   
   ```
   hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
   ```
   
   Ez a parancs sikeresen kimeneti nélkül befejeződött. Miután beállította a hitelesítő adatok a feladatok futtatásához szükséges felhasználói fiókok, a cred.xml fájlt tárolja biztonságos helyen, vagy törölje azt.
5. Ha az RSA kulcspár a Linux-csomópontok egyikén jön létre, ne felejtse el azok használatának befejezése után törölje. Ha HPC Pack talál egy meglévő id_rsa vagy id_rsa.pub fájl, akkor nem kölcsönös megbízhatóság kialakításához.

> [!IMPORTANT]
> Nem ajánlott a Linux feladat futtatásával a fürt rendszergazdai megosztott fürtön, mivel a Linux-csomópontokon a legfelső szintű fiók alatt fut egy feladatot, a rendszergazda által küldött. A feladat a nem rendszergazda felhasználók által benyújtott azonban Linux helyi felhasználói fiók alatt fut, a neve megegyezik a feladat felhasználó. Ebben az esetben HPC Pack állít be a Linux-felhasználó kölcsönös megbízhatósági a a feladathoz kiosztott csomópontjai között. A feladat futtatása előtt manuálisan a Linux-csomópontokon a Linux felhasználói állíthat be, vagy a HPC Pack a felhasználó automatikusan létrehozza a feladat elküldésekor. HPC Pack a felhasználó hoz létre, ha HPC Pack törli őket a feladat befejezése után. A biztonsági kockázatok csökkentése a HPC Pack törli a kulcsokat a feladat befejeződése után.
> 
> 

## <a name="set-up-a-file-share-for-linux-nodes"></a>Linux-csomópontok fájlmegosztás beállítása
Most már készen egy szabványos SMB-megosztáson az átjárócsomópont az egyik mappájába. Ahhoz, hogy a Linux-csomópontok közös elérési úttal rendelkező alkalmazás fájlok eléréséhez, csatlakoztassa a Linux-csomópontokon a megosztott mappához. Ha azt szeretné, használhatja a beállítást, például egy Azure fájlok fájlmegosztás - számos forgatókönyv - vagy az NFS-megosztások ajánlott egy másik fájlmegosztás. A fájlmegosztási információk és részletes lépéseit lásd: [Ismerkedés az Azure-ban HPC Pack fürtben lévő Linux számítási csomópontok](hpcpack-cluster.md).

1. Hozzon létre egy mappát az átjárócsomóponthoz, és úgy, hogy olvasási/írási engedélyekkel mindenki számára ossza meg. Például, megosztás, az átjárócsomópont C:\OpenFOAM \\ \\SUSE12RDMA-HN\OpenFOAM. Itt *SUSE12RDMA-HN* az átjárócsomópont állomás neve.
2. Nyisson meg egy Windows PowerShell-ablakot, és futtassa a következő parancsokat:
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /openfoam
   
   clusrun /nodegroup:LinuxNodes mount -t cifs //SUSE12RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
   ```

Az első parancs létrehoz egy /openfoam a LinuxNodes csoport minden csomópontján nevű mappát. A második parancs a megosztott mappa //SUSE12RDMA-HN/OpenFOAM csatlakoztatja a dir_mode a Linux-csomópont, és file_mode bitet 777. A *felhasználónév* és *jelszó* parancsban kell lennie az átjárócsomópont a felhasználó hitelesítő adatait.

> [!NOTE]
> A "\`" szimbólumra a második parancs a értéke egy escape szimbólum PowerShell. "\`," a "," (vessző karakter) azt jelenti, hogy a parancs része.
> 
> 

## <a name="install-mpi-and-openfoam"></a>MPI és OpenFOAM telepítése
OpenFOAM MPI feladatként futtatja az RDMA hálózati, szüksége az Intel MPI könyvtárakkal OpenFOAM összeállításához. 

Először futtassa több **clusrun** parancsok futtatásával telepítse Intel MPI függvénytárak (Ha még nincs telepítve) és a Linux csomópontján OpenFOAM. A telepítési fájlokat, a Linux-csomópontok közötti megosztásához korábban konfigurált átjárócsomópont megosztás használatára.

> [!IMPORTANT]
> Ezeket a telepítési és fordítása lépések példái. Meg kell néhány a Linux rendszer felügyeleti annak érdekében, hogy a függő compilers – és szalagtárak megfelelően van telepítve. Előfordulhat, hogy módosítania bizonyos környezeti változókat és más beállításait az Intel MPI és OpenFOAM verzióit. További információkért lásd: [Intel MPI Library for Linux – telepítési útmutató](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html) és [OpenFOAM forrás csomag telepítése](http://openfoam.org/download/2-3-1-source/) alkalmaz környezetében.
> 
> 

### <a name="install-intel-mpi"></a>Intel MPI telepítése
Mentse a letöltött telepítési csomag az Intel MPI (ebben a példában l_mpi_p_5.0.3.048.tgz) C:\OpenFoam a head csomóponton, hogy a Linux-csomópontok /openfoam hozzáfér ehhez a fájlt. Ezután futtassa **clusrun** Intel MPI könyvtár telepítése Linux-csomópontjaihoz.

1. A következő parancsok másolja a csomagot, és bontsa ki a /opt/intel minden egyes csomóponton.
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /opt/intel
   
   clusrun /nodegroup:LinuxNodes cp /openfoam/l_mpi_p_5.0.3.048.tgz /opt/intel/
   
   clusrun /nodegroup:LinuxNodes tar -xzf /opt/intel/l_mpi_p_5.0.3.048.tgz -C /opt/intel/
   ```
2. Intel MPI könyvtár csendes telepítéséhez használja a silent.cfg fájl. A mintafájlok, ez a cikk végén található példa. A megosztott mappa /openfoam helyezze a fájlt. A silent.cfg fájllal kapcsolatos részletekért lásd: [Intel MPI Library for Linux – telepítési útmutató - csendes telepítést](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html#silentinstall).
   
   > [!TIP]
   > Győződjön meg arról, hogy Ön a silent.cfg fájl szövegfájl, amelynek Linux sorvégeket (csak LF, nem a CR LF). Ez a lépés biztosítja, hogy ez megfelelően a Linux-csomóponton.
   > 
   > 
3. Intel MPI könyvtár telepítése csendes módban.
   
   ```
   clusrun /nodegroup:LinuxNodes bash /opt/intel/l_mpi_p_5.0.3.048/install.sh --silent /openfoam/silent.cfg
   ```

### <a name="configure-mpi"></a>MPI konfigurálása
A vizsgálat, a következő sorokat kell hozzá minden egyes Linux csomópont /etc/security/limits.conf:

    clusrun /nodegroup:LinuxNodes echo "*               hard    memlock         unlimited" `>`> /etc/security/limits.conf
    clusrun /nodegroup:LinuxNodes echo "*               soft    memlock         unlimited" `>`> /etc/security/limits.conf


Indítsa újra a Linux-csomópontot, a limits.conf fájl frissítése után. Például használja a következő **clusrun** parancs:

```
clusrun /nodegroup:LinuxNodes systemctl reboot
```

Az újraindítás után győződjön meg arról, hogy a megosztott mappa, /openfoam csatlakoztatva van.

### <a name="compile-and-install-openfoam"></a>Fordítsa le és OpenFOAM telepítése
Mentse a letöltött telepítési csomagot a OpenFOAM forrás csomagnak (OpenFOAM 2.3.1.tgz ebben a példában) C:\OpenFoam a az átjárócsomóponthoz, hogy a Linux-csomópontok /openfoam hozzáfér ehhez a fájlt. Ezután futtassa **clusrun** parancsokat minden csomóponton a Linux OpenFOAM összeállításához.

1. Hozzon létre egy mappát /opt/OpenFOAM minden egyes Linux csomóponton, forrás másolja ezt a mappát, és bontsa ki, hogy.
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /opt/OpenFOAM
   
   clusrun /nodegroup:LinuxNodes cp /openfoam/OpenFOAM-2.3.1.tgz /opt/OpenFOAM/
   
   clusrun /nodegroup:LinuxNodes tar -xzf /opt/OpenFOAM/OpenFOAM-2.3.1.tgz -C /opt/OpenFOAM/
   ```
2. Az Intel MPI könyvtárhoz, először állítsa be néhány környezetiblokk-változót Intel MPI és OpenFOAM OpenFOAM összeállításához. Nevű settings.sh bash parancsfájl segítségével a változókat. A mintafájlok, ez a cikk végén található példa. Helyezze a fájlt (a Linux sorvégződések mentett) a megosztott mappa /openfoam. Ez a fájl is a MPI és OpenFOAM futtatókörnyezetek később egy OpenFOAM feladat futtatásához használt beállításait tartalmazza.
3. Telepítse a függő csomagok OpenFOAM összeállításához. Attól függően, hogy a Linux-disztribúció előfordulhat, hogy először vegyen fel egy tárházat. Futtatás **clusrun** a következőhöz hasonló parancsot:
   
    ```
    clusrun /nodegroup:LinuxNodes zypper ar http://download.opensuse.org/distribution/13.2/repo/oss/suse/ opensuse
   
    clusrun /nodegroup:LinuxNodes zypper -n --gpg-auto-import-keys install --repo opensuse --force-resolution -t pattern devel_C_C++
    ```
   
    Ha szükséges, minden egyes Linux csomópontra SSH annak ellenőrzéséhez, hogy futnak megfelelően parancsok futtatásához.
4. A következő parancsot OpenFOAM összeállításához. Az összeállítási folyamat végrehajtásához némi időt vesz igénybe, és naplózási adatok normál a kimenetbe nagy mennyiségű állít elő, ezért a **/ időosztásos** lehetőséget a kimeneti időosztásos megjelenítéséhez.
   
   ```
   clusrun /nodegroup:LinuxNodes /interleaved source /openfoam/settings.sh `&`& /opt/OpenFOAM/OpenFOAM-2.3.1/Allwmake
   ```
   
   > [!NOTE]
   > A "\`" szimbólum a parancsban értéke egy escape szimbólum PowerShell. "\`&" azt jelenti, hogy a "&" parancs része.
   > 
   > 

## <a name="prepare-to-run-an-openfoam-job"></a>Egy OpenFOAM feladat futtatásához előkészítése
Most Felkészülés egy hívása sloshingTank3D, amely a OpenFoam minta egyike, két Linux csomópont MPI feladat futtatásához. 

### <a name="set-up-the-runtime-environment"></a>A futásidejű környezet beállítása
Beállítása a futásidejű környezetek MPI és OpenFOAM a Linux-csomóponton, futtassa a következő parancsot egy Windows PowerShell-ablakban a head csomóponton. (Ez a parancs érvénytelen a SUSE Linux csak.)

```
clusrun /nodegroup:LinuxNodes cp /openfoam/settings.sh /etc/profile.d/
```

### <a name="prepare-sample-data"></a>Mintaadatok előkészítése
Fájlmegosztás között a Linux-csomópontok (mint /openfoam csatlakoztatott) korábban konfigurált átjárócsomópont megosztás használatára.

1. A számítási csomópontok SSH a Linux egyikére.
2. A következő parancsot a OpenFOAM futtatási környezetet, ha még nem tette meg ezt.
   
   ```
   $ source /openfoam/settings.sh
   ```
3. Másolja a sloshingTank3D minta a megosztott mappa, és keresse meg a fájlt.
   
   ```
   $ cp -r $FOAM_TUTORIALS/multiphase/interDyMFoam/ras/sloshingTank3D /openfoam/
   
   $ cd /openfoam/sloshingTank3D
   ```
4. Ez a minta alapértelmezett paraméterértékeivel használatakor is igénybe vehet több tíz perc alatt szeretné futtatni, ezért érdemes néhány abba, hogy gyorsabbá paraméterek módosításához. Egy egyszerű rendszer idő lépés változók deltaT és a rendszer/controlDict fájlban writeInterval módosíthatja. Ez a fájl tartalmazza az idő és adatok írásakor vagy olvasásakor megoldás ellenőrzésére vonatkozó összes bemeneti adatot. Például megváltoztathatja 0,5 a 0,05 deltaT értékének és a 0,05 writeInterval 0,5 értékét.
   
   ![Lépés változók módosítása][step_variables]
5. A rendszer/decomposeParDict fájlban adja meg a változót a kívánt értékeket. Ebben a példában két Linux csomópont minden használ 8 magos, úgy állítsa be a numberOfSubdomains 16 és a hierarchicalCoeffs n (1 1 16), ami azt jelenti, 16 folyamatokkal párhuzamosan futnak a OpenFOAM. További információkért lásd: [OpenFOAM felhasználói útmutatója: 3.4 futó alkalmazások párhuzamosan](http://cfd.direct/openfoam/user-guide/running-applications-parallel/#x12-820003.4).
   
   ![Folyamatok felbontani][decompose]
6. A következő parancsokat a mintaadatok létrehozása a sloshingTank3D könyvtárból.
   
   ```
   $ . $WM_PROJECT_DIR/bin/tools/RunFunctions
   
   $ m4 constant/polyMesh/blockMeshDict.m4 > constant/polyMesh/blockMeshDict
   
   $ runApplication blockMesh
   
   $ cp 0/alpha.water.org 0/alpha.water
   
   $ runApplication setFields  
   ```
7. A head csomóponton meg kell jelennie a mintaadatfájlok C:\OpenFoam\sloshingTank3D másolódnak. (A megosztott mappát a átjárócsomópontjához C:\OpenFoam érték).)
   
   ![Az átjárócsomópont adatfájlokat][data_files]

### <a name="host-file-for-mpirun"></a>Állomás fájlt mpirun
Ebben a lépésben létrehozott állomás-fájlokat (számítási csomópontok listáját) amely a **mpirun** parancsot használja.

1. A Linux-csomópontok egyikét hozzon létre egy fájlt hostfile alatt /openfoam, ezért ezt a fájlt az összes Linux csomóponton /openfoam/hostfile címen érhető el.
2. A Linux csomópontnevek írni az ezt a fájlt. Ebben a példában a fájl tartalmazza a következő szerepel:
   
   ```       
   SUSE12RDMA-LN1
   SUSE12RDMA-LN2
   ```
   
   > [!TIP]
   > Ez a fájl C:\OpenFoam\hostfile, a központi csomóponton is létrehozhat. Ha ezt a lehetőséget választja, a Linux és szövegfájlként menteni sorvégeket (csak LF, nem a CR LF). Ez biztosítja, hogy fusson megfelelően a Linux-csomóponton.
   > 
   > 
   
   **Bash parancsfájlok burkoló**
   
   Sok Linux-csomópont van, és azt szeretné, hogy a feladat futásának csak az egyes, esetén nem érdemes egy rögzített gazdagépet fájl, mert nem tudja, melyik csomópontok rendszer rendeli hozzá a feladatot. Ebben az esetben írni a bash parancsfájlok burkolót **mpirun** automatikusan a gazdagép-fájl létrehozásához. Egy példa bash parancsfájl burkoló nevű hpcimpirun.sh Ez a cikk végén található, és mentse /openfoam/hpcimpirun.sh. Ebben a példaként megadott parancsfájlt a következőket teszi:
   
   1. Beállítja a környezeti változók a **mpirun**, és néhány hozzáadása parancs paraméterei a MPI feladat futtatásához az RDMA a hálózaton keresztül. Ebben az esetben azt állítja be a következő változókat:
      
      * I_MPI_FABRICS=shm:dapl
      * I_MPI_DAPL_PROVIDER=ofa-v2-ib0
      * I_MPI_DYNAMIC_CONNECTION=0
   2. Létrehoz egy gazdagép megfelelően a környezeti változó $CCP_NODES_CORES, amely szerint a HPC átjárócsomópont van beállítva, a feladat aktiválásakor.
      
      $CCP_NODES_CORES formátuma ezt a mintát követi:
      
      ```
      <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
      ```
      
      Ha
      
      * `<Number of nodes>` – a feladat számára lefoglalt csomópontok száma.  
      * `<Name of node_n_...>` – a feladat rendelt minden csomópont neve.
      * `<Cores of node_n_...>` -a csomóponton a feladat számára lefoglalt magok száma.
      
      Például ha a feladat futtatásához két csomópont van szüksége, $CCP_NODES_CORES hasonlít
      
      ```
      2 SUSE12RDMA-LN1 8 SUSE12RDMA-LN2 8
      ```
   3. Hívások a **mpirun** parancsot, és hozzáfűzi a két paraméter a parancssorban.
      
      * `--hostfile <hostfilepath>: <hostfilepath>` -a parancsfájl létrehozza a gazdagép-fájl elérési útja
      * `-np ${CCP_NUMCPUS}: ${CCP_NUMCPUS}` -olyan környezeti változó beállítása a HPC Pack központi csomópont, amely tárolja az összes, a feladat számára lefoglalt magok száma. Ebben az esetben adja meg a folyamatok száma **mpirun**.

## <a name="submit-an-openfoam-job"></a>Egy OpenFOAM feladat elküldése
Most elküldheti a HPC Cluster Manager feladat. A parancsfájl hpcimpirun.sh át a parancsokat a projekt feladatokat kell.

1. A fürt átjárócsomópontjához csatlakozik, és indítsa el a HPC Cluster Manager.
2. **Az erőforrás-kezelés**, akkor győződjön meg arról, hogy a Linux számítási csomópontok a **Online** állapotát. Ha nem, válassza ki azokat, és kattintson a **online állapotba hozás**.
3. A **feladatkezelés**, kattintson a **új feladat**.
4. Írjon be egy feladat nevét, például *sloshingTank3D*.
   
   ![Feladat részletei][job_details]
5. A **feladat-erőforrások**, válassza ki az erőforrás "Csomópont" típusú, és legalább a 2 értékűre állított. Ez a konfiguráció futó Linux két csomópont, amelyek mindegyikének nyolc processzormaggal ebben a példában a feladat.
   
   ![Feladat-erőforrások][job_resources]
6. Kattintson a **feladatok szerkesztése** a bal oldali navigációs, majd **Hozzáadás** feladat hozzáadása a feladatot. Négy feladatok hozzá a feladatot a következő parancssorok és beállítások.
   
   > [!NOTE]
   > Futó `source /openfoam/settings.sh` beállítja a OpenFOAM és MPI futtatási környezet, ezért a következő feladatokat mindegyikének meghívja a OpenFOAM parancs előtt.
   > 
   > 
   
   * **1. feladat**. Futtatás **decomposePar** futtatásához adatfájlokat létrehozni **interDyMFoam** párhuzamosan.
     
     * A feladat egy csomópont hozzárendelése
     * **Parancssor** - `source /openfoam/settings.sh && decomposePar -force > /openfoam/decomposePar${CCP_JOBID}.log`
     * **Munkakönyvtár** -/ openfoam/sloshingTank3D
     
     Lásd az alábbi ábrát. Hasonló módon konfigurálhatja a hátralévő műveletekkel.
     
     ![1. feladat részletei][task_details1]
   * **2. feladat**. Futtatás **interDyMFoam** párhuzamosan a minta kiszámításához.
     
     * A feladat két csomópont hozzárendelése
     * **Parancssor** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh interDyMFoam -parallel > /openfoam/interDyMFoam${CCP_JOBID}.log`
     * **Munkakönyvtár** -/ openfoam/sloshingTank3D
   * **3. feladat**. Futtatás **reconstructPar** idő könyvtárak minden processor_N_ könyvtárból készleteinek egyesíthet egyetlen.
     
     * A feladat egy csomópont hozzárendelése
     * **Parancssor** - `source /openfoam/settings.sh && reconstructPar > /openfoam/reconstructPar${CCP_JOBID}.log`
     * **Munkakönyvtár** -/ openfoam/sloshingTank3D
   * **4. feladat**. Futtatás **foamToEnsight** OpenFOAM eredménye konvertálható párhuzamosan EnSight fájlok formázása, és helyezze el a EnSight fájlok egy kis könyvtárban Ensight nevű könyvtár.
     
     * A feladat két csomópont hozzárendelése
     * **Parancssor** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh foamToEnsight -parallel > /openfoam/foamToEnsight${CCP_JOBID}.log`
     * **Munkakönyvtár** -/ openfoam/sloshingTank3D
7. Adja hozzá ezeket a feladatokat, növekvő sorrendben feladat függőségek.
   
   ![Tevékenységfüggőségek][task_dependencies]
8. Kattintson a **Submit** a feladat futtatásához.
   
   Alapértelmezés szerint a HPC Pack elküldi a feladatot, a jelenlegi bejelentkezett felhasználói fiókkal. Miután rákattintott **Submit**, megjelenhet egy párbeszédpanel, ahol megadhatja a felhasználónevet és jelszót.
   
   ![Feladat hitelesítő adatai][creds]
   
   Bizonyos körülmények között a HPC Pack emlékszik a felhasználói adatok előtt adjon meg, és nem jeleníti meg ezen a párbeszédpanelen. HPC Pack jelenjen meg többé ez a következő parancsot a parancssorba írja be, és ezután a feladat elküldéséhez.
   
   ```
   hpccred delcreds
   ```
9. A feladat több tíz perc alatt az a paraméterek, a minta beállítása alapján több órát vesz igénybe. Hőtérkép a Linux csomópontokon futó feladat látható. 
   
   ![Hőtérkép][heat_map]
   
   Minden egyes csomóponton nyolc folyamatok indulnak el.
   
   ![Linux-folyamat][linux_processes]
10. A feladat befejezése után található a feladat eredményeinek C:\OpenFoam\sloshingTank3D, és a naplófájlok a C:\OpenFoam mappát.

## <a name="view-results-in-ensight"></a>EnSight az eredmények megtekintése
Ezenkívül szükség [EnSight](https://www.ceisoftware.com/) megtekintheti és elemezheti a OpenFOAM feladat eredményeinek számára. További információt a képi megjelenítés és EnSight animációt megjelenik ez [videó útmutató](http://www.ceisoftware.com/wp-content/uploads/screencasts/vof_visualization/vof_visualization.html).

1. Miután telepítette az átjárócsomópont EnSight, indítsa el.
2. Open C:\OpenFoam\sloshingTank3D\EnSight\sloshingTank3D.case.
   
   Megjelenik egy tartály a megjelenítőben.
   
   ![A EnSight tartály][tank]
3. Hozzon létre egy **Isosurface** a **internalMesh**, majd válassza a változó **alpha_water**.
   
   ![Hozzon létre egy isosurface][isosurface]
4. Színét **Isosurface_part** az előző lépésben létrehozott. Például állítsa vízjel kék.
   
   ![Isosurface szín módosítása][isosurface_color]
5. Hozzon létre egy **Iso-kötet** a **falvastagságát** kiválasztásával **falvastagságát** a a **részek** panelen, majd kattintson a **Isosurfaces** gomb az eszköztáron.
6. A párbeszédpanelen válassza ki a **típus** , **Isovolume** és állítsa be a minimális **Isovolume tartomány** 0,5. A isovolume létrehozásához kattintson a **létrehozása a kijelölt alkotórészek**.
7. Színét **Iso_volume_part** az előző lépésben létrehozott. Például állítsa a részletes vízjel kék.
8. Színét **falvastagságát**. Például állítsa átlátszó üres.
9. Most kattintson **lejátszása** a szimuláció az eredmények megtekintéséhez.
   
    ![Tartály eredménye][tank_result]

## <a name="sample-files"></a>Mintafájlok
### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>A minta XML konfigurációs fájl fürttelepítés PowerShell-parancsprogram
 ```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>suse12rdmavnet</VNetName>
    <SubnetName>SUSE12RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>SUSE12RDMA-HN</VMName>
    <ServiceName>suse12rdma-je</ServiceName>
    <VMSize>A8</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>SUSE12RDMA-LN%1%</VMNamePattern>
    <ServiceName>suse12rdma-je</ServiceName>
    <VMSize>A8</VMSize>
    <NodeCount>2</NodeCount>
      <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```

### <a name="sample-credxml-file"></a>Mintafájl cred.xml
```
<ExtendedData>
  <PrivateKey>-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAxJKBABhnOsE9eneGHvsjdoXKooHUxpTHI1JVunAJkVmFy8JC
qFt1pV98QCtKEHTC6kQ7tj1UT2N6nx1EY9BBHpZacnXmknpKdX4Nu0cNlSphLpru
lscKPR3XVzkTwEF00OMiNJVknq8qXJF1T3lYx3rW5EnItn6C3nQm3gQPXP0ckYCF
Jdtu/6SSgzV9kaapctLGPNp1Vjf9KeDQMrJXsQNHxnQcfiICp21NiUCiXosDqJrR
AfzePdl0XwsNngouy8t0fPlNSngZvsx+kPGh/AKakKIYS0cO9W3FmdYNW8Xehzkc
VzrtJhU8x21hXGfSC7V0ZeD7dMeTL3tQCVxCmwIDAQABAoIBAQCve8Jh3Wc6koxZ
qh43xicwhdwSGyliZisoozYZDC/ebDb/Ydq0BYIPMiDwADVMX5AqJuPPmwyLGtm6
9hu5p46aycrQ5+QA299g6DlF+PZtNbowKuvX+rRvPxagrTmupkCswjglDUEYUHPW
05wQaNoSqtzwS9Y85M/b24FfLeyxK0n8zjKFErJaHdhVxI6cxw7RdVlSmM9UHmah
wTkW8HkblbOArilAHi6SlRTNZG4gTGeDzPb7fYZo3hzJyLbcaNfJscUuqnAJ+6pT
iY6NNp1E8PQgjvHe21yv3DRoVRM4egqQvNZgUbYAMUgr30T1UoxnUXwk2vqJMfg2
Nzw0ESGRAoGBAPkfXjjGfc4HryqPkdx0kjXs0bXC3js2g4IXItK9YUFeZzf+476y
OTMQg/8DUbqd5rLv7PITIAqpGs39pkfnyohPjOe2zZzeoyaXurYIPV98hhH880uH
ZUhOxJYnlqHGxGT7p2PmmnAlmY4TSJrp12VnuiQVVVsXWOGPqHx4S4f9AoGBAMn/
vuea7hsCgwIE25MJJ55FYCJodLkioQy6aGP4NgB89Azzg527WsQ6H5xhgVMKHWyu
Q1snp+q8LyzD0i1veEvWb8EYifsMyTIPXOUTwZgzaTTCeJNHdc4gw1U22vd7OBYy
nZCU7Tn8Pe6eIMNztnVduiv+2QHuiNPgN7M73/x3AoGBAOL0IcmFgy0EsR8MBq0Z
ge4gnniBXCYDptEINNBaeVStJUnNKzwab6PGwwm6w2VI3thbXbi3lbRAlMve7fKK
B2ghWNPsJOtppKbPCek2Hnt0HUwb7qX7Zlj2cX/99uvRAjChVsDbYA0VJAxcIwQG
TxXx5pFi4g0HexCa6LrkeKMdAoGAcvRIACX7OwPC6nM5QgQDt95jRzGKu5EpdcTf
g4TNtplliblLPYhRrzokoyoaHteyxxak3ktDFCLj9eW6xoCZRQ9Tqd/9JhGwrfxw
MS19DtCzHoNNewM/135tqyD8m7pTwM4tPQqDtmwGErWKj7BaNZARUlhFxwOoemsv
R6DbZyECgYEAhjL2N3Pc+WW+8x2bbIBN3rJcMjBBIivB62AwgYZnA2D5wk5o0DKD
eesGSKS5l22ZMXJNShgzPKmv3HpH22CSVpO0sNZ6R+iG8a3oq4QkU61MT1CfGoMI
a8lxTKnZCsRXU1HexqZs+DSc+30tz50bNqLdido/l5B4EJnQP03ciO0=
-----END RSA PRIVATE KEY-----</PrivateKey>
  <PublicKey>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEkoEAGGc6wT16d4Ye+yN2hcqigdTGlMcjUlW6cAmRWYXLwkKoW3WlX3xAK0oQdMLqRDu2PVRPY3qfHURj0EEellpydeaSekp1fg27Rw2VKmEumu6Wxwo9HddXORPAQXTQ4yI0lWSerypckXVPeVjHetbkSci2foLedCbeBA9c/RyRgIUl227/pJKDNX2Rpqly0sY82nVWN/0p4NAyslexA0fGdBx+IgKnbU2JQKJeiwOomtEB/N492XRfCw2eCi7Ly3R8+U1KeBm+zH6Q8aH8ApqQohhLRw71bcWZ1g1bxd6HORxXOu0mFTzHbWFcZ9ILtXRl4Pt0x5Mve1AJXEKb username@servername;</PublicKey>
</ExtendedData>
```
### <a name="sample-silentcfg-file-to-install-mpi"></a>Mintafájl silent.cfg MPI telepítése
```
# Patterns used to check silent configuration file
#
# anythingpat - any string
# filepat     - the file location pattern (/file/location/to/license.lic)
# lspat       - the license server address pattern (0123@hostname)
# snpat       - the serial number pattern (ABCD-01234567)

# accept EULA, valid values are: {accept, decline}
ACCEPT_EULA=accept

# optional error behavior, valid values are: {yes, no}
CONTINUE_WITH_OPTIONAL_ERROR=yes

# install location, valid values are: {/opt/intel, filepat}
PSET_INSTALL_DIR=/opt/intel

# continue with overwrite of existing installation directory, valid values are: {yes, no}
CONTINUE_WITH_INSTALLDIR_OVERWRITE=yes

# list of components to install, valid values are: {ALL, DEFAULTS, anythingpat}
COMPONENTS=DEFAULTS

# installation mode, valid values are: {install, modify, repair, uninstall}
PSET_MODE=install

# directory for non-RPM database, valid values are: {filepat}
#NONRPM_DB_DIR=filepat

# Serial number, valid values are: {snpat}
#ACTIVATION_SERIAL_NUMBER=snpat

# License file or license server, valid values are: {lspat, filepat}
#ACTIVATION_LICENSE_FILE=

# Activation type, valid values are: {exist_lic, license_server, license_file, trial_lic, serial_number}
ACTIVATION_TYPE=trial_lic

# Path to the cluster description file, valid values are: {filepat}
#CLUSTER_INSTALL_MACHINES_FILE=filepat

# Intel(R) Software Improvement Program opt-in, valid values are: {yes, no}
PHONEHOME_SEND_USAGE_DATA=no

# Perform validation of digital signatures of RPM files, valid values are: {yes, no}
SIGNING_ENABLED=yes

# Select yes to enable mpi-selector integration, valid values are: {yes, no}
ENVIRONMENT_REG_MPI_ENV=no

# Select yes to update ld.so.conf, valid values are: {yes, no}
ENVIRONMENT_LD_SO_CONF=no


```

### <a name="sample-settingssh-script"></a>Sample settings.sh script
```
#!/bin/bash

# impi
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# openfoam
export FOAM_INST_DIR=/opt/OpenFOAM
source /opt/OpenFOAM/OpenFOAM-2.3.1/etc/bashrc
export WM_MPLIB=INTELMPI
```


### <a name="sample-hpcimpirunsh-script"></a>Mintaparancsfájl hpcimpirun.sh
```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"

# Set mpirun runtime evironment
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# mpirun command
MPIRUN=mpirun
# Argument of "--hostfile"
NODELIST_OPT="--hostfile"
# Argument of "-np"
NUMPROCESS_OPT="-np"

# Get node information from ENVs
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # CCP_NODES_CORES is not found or is empty, just run the mpirun without hostfile arg.
    ${MPIRUN} $*
else
    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$

    # Get every node name and write into the hostfile file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the mpirun with hostfile arg
    ${MPIRUN} ${NUMPROCESS_OPT} ${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}

```





<!--Image references-->
[keygen]:media/hpcpack-cluster-openfoam/keygen.png
[keys]:media/hpcpack-cluster-openfoam/keys.png
[step_variables]:media/hpcpack-cluster-openfoam/step_variables.png
[data_files]:media/hpcpack-cluster-openfoam/data_files.png
[decompose]:media/hpcpack-cluster-openfoam/decompose.png
[job_details]:media/hpcpack-cluster-openfoam/job_details.png
[job_resources]:media/hpcpack-cluster-openfoam/job_resources.png
[task_details1]:media/hpcpack-cluster-openfoam/task_details1.png
[task_dependencies]:media/hpcpack-cluster-openfoam/task_dependencies.png
[creds]:media/hpcpack-cluster-openfoam/creds.png
[heat_map]:media/hpcpack-cluster-openfoam/heat_map.png
[tank]:media/hpcpack-cluster-openfoam/tank.png
[tank_result]:media/hpcpack-cluster-openfoam/tank_result.png
[isosurface]:media/hpcpack-cluster-openfoam/isosurface.png
[isosurface_color]:media/hpcpack-cluster-openfoam/isosurface_color.png
[linux_processes]:media/hpcpack-cluster-openfoam/linux_processes.png
