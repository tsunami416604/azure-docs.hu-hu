---
title: Az OpenFOAM futtatása a HPC Pack segítségével Linuxos virtuális gépeken |} A Microsoft Docs
description: Az Azure-ban a Microsoft HPC Pack-fürt üzembe helyezése és a egy OpenFOAM feladat futtatásához a több Linuxos számítási csomópontokon az RDMA-hálózaton.
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
ms.openlocfilehash: a8744afe3ec3e83e4a543942441118356730347c
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52957241"
---
# <a name="run-openfoam-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>Az OpenFoam futtatása a Microsoft HPC Packkal Azure-beli linuxos RDMA-fürtön
Ez a cikk bemutatja, hogy egyik módja az OpenFoam futtatása az Azure-beli virtuális gépeken. Itt, a Microsoft HPC Pack-fürt Linuxos számítási csomópontok az Azure-ra és a Futtatás telepít egy [OpenFoam](http://openfoam.com/) Intel MPI-feladatok. Használhatja RDMA-kompatibilis Azure virtuális gépek a számítási csomópontok, így a számítási csomópontok Azure RDMA hálózati kommunikációra. Egyéb lehetőségek az OpenFoam futtatása az Azure-ban rendelkezésre álló teljes konfigurációjú kereskedelmi képeket is tartalmaznak a Marketplace-en, például az UberCloud a [OpenFoam 2.3 CentOS 6-os](https://azuremarketplace.microsoft.com/marketplace/apps/cfd-direct.cfd-direct-from-the-cloud), és a kiszolgálón való futtatásával [Azure Batch](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/). 

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Az OpenFOAM (a mező nyissa meg a műveletet és adatmanipulációs) egy nyílt forráskódú numerikus áramlástani (CFD) szoftvercsomagot, mérnöki és tudományos számításokban kereskedelmi és oktatási szervezetek a széles körben használt. Nástroje Pro meshing, nevezetesen snappyHexMesh, egy párhuzamos működésű mesher összetett CAD geometriája, és előzetes és utólagos feldolgozási tartalmazza. Szinte az összes folyamat futhat párhuzamosan – felhasználók teljes mértékben kihasználhatja a számítógép a rendelkezésükre.  

A Microsoft HPC Pack biztosítja a funkciók futtathat nagy méretű HPC és a párhuzamos alkalmazások, például MPI-alkalmazások, Microsoft Azure virtuális gépek fürtjein. HPC Pack támogatja a Linux-alapú alkalmazásokat számítási csomópont virtuális gépek üzembe helyezett HPC Pack-fürtökben futó Linux-alapú HPC is. Lásd: [Linuxos számítási csomópontok HPC Pack-fürtökben, az Azure-ban – első lépések](hpcpack-cluster.md) a bevezetést nyújtanak a Linux számítási csomópontok HPC packkel.

> [!NOTE]
> Ez a cikk a Linux MPI számítási feladatok futtatása HPC packkel mutatja be. Azt feltételezi, hogy néhány ismerete Linux rendszer-felügyeleti és MPI számítási feladatok futtatása Linux-fürtökön. Ha MPI és OpenFOAM ebben a cikkben látható a különböző verzióit használja, előfordulhat, módosíthatja az egyes telepítési és konfigurációs lépéseket. 
> 
> 

## <a name="prerequisites"></a>Előfeltételek
* **HPC Pack-fürthöz az RDMA-kompatibilis Linuxos számítási csomópontok** – méretű A8, A9, H16r, HPC Pack-fürt üzembe helyezése vagy H16rm Linux számítási csomópontok segítségével egy [Azure Resource Manager-sablon](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) vagy egy [Azure PowerShell-parancsprogram](hpcpack-cluster-powershell-script.md). Lásd: [Linuxos számítási csomópontok HPC Pack-fürtökben, az Azure-ban – első lépések](hpcpack-cluster.md) az Előfeltételek és a lépések bármelyik beállítást is választja. Ha a PowerShell parancsfájl központi telepítési lehetőséget választja, tekintse meg a minta konfigurációs fájlt a mintafájlok, ez a cikk végén található. Ez a konfiguráció használatával helyezhet üzembe egy Azure-alapú HPC Pack-fürt mérete a8-as Windows Server 2012 R2 átjárócsomópont és a számítási csomópontok 2 mérete a8-as SUSE Linux Enterprise Server 12. Az előfizetés és a szolgáltatás nevének megfelelő értékekkel helyettesítse. 
  
  **További tudnivaló**
  
  * Az Azure-beli Linuxos RDMA hálózati Előfeltételek, lásd: [nagy teljesítményű számítási Virtuálisgép-méretek](../../windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
  * Ha a Powershell parancsfájl központi telepítési beállítást használja, üzembe helyezése az RDMA hálózati kapcsolat használata egy felhőszolgáltatáson belül az összes Linux számítási csomópontok.
  * A Linux-csomópontok üzembe helyezése után csatlakozzon az ssh-KAPCSOLATOT minden további felügyeleti feladatokat hajthat végre. Az SSH-kapcsolat adatai található minden egyes Linux virtuális gép az Azure Portalon.  
* **Az Intel MPI** – az OpenFOAM futtatása az Azure-ban, SLES 12 HPC számítási csomópontokon az Intel MPI könyvtár 5 futásidejű telepítenie kell a [Intel.com hely](https://software.intel.com/en-us/intel-mpi-library/). (Az Intel MPI 5 előre telepítve van a CentOS-alapú HPC-lemezképekre.)  Egy későbbi lépésben szükség esetén telepítse az Intel MPI a Linuxos számítási csomópontokon. Ebben a lépésben előkészítheti az Intel regisztrálása után, kövesse a visszaigazoló e-mailben szereplő hivatkozást kapcsolódó weblapon. Ezután másolja a letöltési hivatkozást az Intel MPI-t a megfelelő verzióját .tgz fájlt. Ez a cikk az Intel MPI verzió 5.0.3.048 alapul.
* **Az OpenFOAM forrás csomag** – az OpenFOAM forrás csomag szoftver letöltése a Linuxhoz készült a [OpenFOAM Foundation hely](http://openfoam.org/download/2-3-1-source/). Ez a cikk az OpenFOAM-2.3.1.tgz mint forrás csomag verziója letölthető 2.3.1 alapul. Kövesse a cikk későbbi részében kicsomagolása és fordítsa le az OpenFOAM a Linuxos számítási csomópontokon.
* **EnSight** (nem kötelező) – az OpenFOAM szimuláció, letöltése és telepítése, az eredmények megtekintéséhez a [EnSight](https://www.ansys.com/products/platform/ansys-ensight/data-interfaces) megjelenítési és elemzési program. Licencelés és letöltése a EnSight helyen vannak.

## <a name="set-up-mutual-trust-between-compute-nodes"></a>Számítási csomópontok közötti kölcsönös bizalmi kapcsolat beállítása
Csomópontok közötti feladat futtatása a Linux több csomóponton van szükség a csomópontok megbíznak egymásban (által **rsh** vagy **ssh**). A HPC Pack-fürt létrehozásakor a Microsoft HPC Pack IaaS telepítési szkripttel a parancsfájl automatikusan beállítja a megadott rendszergazdai fiók végleges kölcsönös megbízhatósági. A nem rendszergazdai felhasználók számára hoz létre a fürt a tartományban kell beállítania ideiglenes kölcsönös megbízhatósági mozgatja a csomópontok, amikor egy feladat lefoglalt őket, és szüntesse meg a kapcsolat, a feladat befejezése után. Az egyes felhasználók megbízhatósági kapcsolatot hoz létre, adja meg a fürthöz, a bizalmi kapcsolat használó HPC Pack-RSA-kulcspárt.

### <a name="generate-an-rsa-key-pair"></a>Egy RSA-kulcspár létrehozása
Egyszerűen hozzon létre egy RSA-kulcspárral, amely tartalmaz egy nyilvános kulcs és a egy titkos kulcsot a Linux rendszerű **az ssh-keygen** parancsot.

1. Jelentkezzen be egy Linux rendszerű számítógépre.
2. Futtassa az alábbi parancsot:
   
   ```
   ssh-keygen -t rsa
   ```
   
   > [!NOTE]
   > Nyomja meg **Enter** , amíg befejeződik a parancs az alapértelmezett beállításokat használja. Ne adjon meg egy jelszót. Amikor a rendszer kéri a jelszót, csak nyomja meg az **Enter**.
   > 
   > 
   
   ![Egy RSA-kulcspár létrehozása][keygen]
3. Módosítsa a könyvtárat a ~/.ssh címtárban. A titkos kulcs id_rsa és a nyilvános kulcsot a id_rsa.pub tárolja.
   
   ![Nyilvános és titkos kulcsai][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>A kulcspárt hozzáadása a HPC Pack-fürthöz
1. A távoli asztali kapcsolat az átjárócsomóponthoz, a HPC Pack-rendszergazdai fiókkal (a rendszergazdai fiók beállítása az üzembe helyezési parancsfájl futtatásakor).
2. Standard Windows Server-eljárások használatával egy tartományi felhasználói fiók létrehozása a fürt Active Directory-tartományban. Például használja az Active Directory – felhasználók és számítógépek eszközt a központi csomóponton. Ebben a cikkben szereplő példák feltételezik, hogy hpclab\hpcuser nevű tartományi felhasználót hoz létre.
3. Hozzon létre egy C:\cred.xml nevű fájlt, és az RSA-kulcs adatokat másolja a fájlba. Mintafájl cred.xml van ez a cikk végén található.
   
   ```
   <ExtendedData>
     <PrivateKey>Copy the contents of private key here</PrivateKey>
     <PublicKey>Copy the contents of public key here</PublicKey>
   </ExtendedData>
   ```
4. Nyisson meg egy parancssort, és adja meg a következő parancsban a hpclab\hpcuser fiók hitelesítő adatait. Használja a **extendeddata** C:\cred.xml fájl hozott létre a fő adatok paramétert.
   
   ```
   hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
   ```
   
   Ez a parancs sikeresen kimeneti nélkül befejeződött. Miután beállította a felhasználói fiókok feladatok futtatásához szükséges hitelesítő adatait, a cred.xml fájlt tárolja biztonságos helyen, vagy törölheti is.
5. Ha az RSA-kulcspárt a Linux-csomópontok egyik jön létre, ne felejtse el törölni a kulcsok őket használatának befejezése után. Ha a HPC Pack talál egy meglévő id_rsa vagy id_rsa.pub fájl, azt nem kölcsönös megbízhatóság kialakításához.

> [!IMPORTANT]
> Nem ajánlott Linux feladat futtatása egy fürt rendszergazdaként megosztott fürtben, mert a rendszergazda által beküldött feladatok futtatása root fiókjának a Linux-csomópontok. Nem rendszergazdai felhasználók által beküldött feladatok azonban egy Linux helyi felhasználói fiók alatt fut, a neve megegyezik a feladat felhasználó. Ebben az esetben a HPC Pack állít be a Linux-felhasználó kölcsönös megbízhatósági a feladathoz lefoglalt csomópontok között. Beállíthatja a Linuxos felhasználó manuálisan a Linux-csomópontokon a feladat futtatása előtt, vagy a HPC Pack a felhasználó automatikusan létrehozza a feladat elküldésekor. Ha a HPC Pack hoz létre a felhasználó, HPC Pack törli a feladat befejezése után. Biztonsági kockázatok csökkentése érdekében a HPC Pack törli a kulcsokat a feladat befejezése után.
> 
> 

## <a name="set-up-a-file-share-for-linux-nodes"></a>Linux-csomópontok fájlmegosztás beállítása
Most állítsa be a szabványos SMB-megosztáshoz a fő csomópontot az egyik mappájába. Ahhoz, hogy a Linux-csomópontok gyakori elérési úttal rendelkező alkalmazás fájlok eléréséhez, a Linux-csomópontok csatlakoztatása a megosztott mappához. Ha azt szeretné, használhatja a beállítást, például az Azure Files-megosztás – számos forgatókönyv - vagy az NFS-megosztások ajánlott egy másik fájlmegosztás. A fájlmegosztási információk és részletes lépéseket lásd: [Linux számítási csomópontok az Azure-beli HPC Pack-fürt használatának első lépései](hpcpack-cluster.md).

1. Hozzon létre egy mappát a fő csomópontot, és ossza meg az mindenki számára olvasási/írási jogosultsággal beállításával. Például megoszthatja az átjárócsomóponthoz, mint a C:\OpenFOAM \\ \\SUSE12RDMA-HN\OpenFOAM. Itt *SUSE12RDMA-HN* az átjárócsomóponthoz állomás neve.
2. Nyisson meg egy Windows PowerShell-ablakot, és futtassa a következő parancsokat:
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /openfoam
   
   clusrun /nodegroup:LinuxNodes mount -t cifs //SUSE12RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
   ```

Az első parancs a LinuxNodes csoport minden csomópontján /openfoam nevű mappát hoz létre. A második parancs csatlakoztatja a megosztott mappa //SUSE12RDMA-HN/OpenFOAM dir_mode a Linux-csomópontokon, és file_mode bits 777 beállítása. A *felhasználónév* és *jelszó* parancsban kell lennie a fő csomópontot a felhasználó hitelesítő adatait.

> [!NOTE]
> A "\`" szimbólumot a második parancs a PowerShell-escape szimbólum. "\`," azt jelenti, hogy a "," (vessző karakter) a parancs része.
> 
> 

## <a name="install-mpi-and-openfoam"></a>MPI és OpenFOAM telepítése
Az OpenFOAM futtatása az MPI-feladatok, az RDMA hálózati, fordítsa le az OpenFOAM az Intel MPI-kódtárakat kell. 

Először futtassa több **clusrun** parancsokat az Intel MPI-kódtárak telepítése (Ha még nem telepítette), és a Linux-csomópontokat az OpenFOAM. A telepítési fájlokat, a Linux-csomópontok között megosztani előzőleg konfigurált átjárócsomópont megosztás használatára.

> [!IMPORTANT]
> Ezeket a telepítési és fordítása lépések példái. Győződjön meg arról, hogy a függő fordítóprogramot és tárak megfelelően vannak telepítve a Linux rendszer-felügyeleti néhány ismerete van szüksége. Szüksége lehet a környezeti változókat vagy más beállításokat, az Intel MPI-t és az OpenFOAM verzióit. További információkért lásd: [Intel MPI Library for Linux telepítővarázslót](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html) és [OpenFOAM forrás csomag telepítése](http://openfoam.org/download/2-3-1-source/) környezete számára.
> 
> 

### <a name="install-intel-mpi"></a>Az Intel MPI telepítése
Mentse a letöltött telepítési csomag az Intel MPI (ebben a példában l_mpi_p_5.0.3.048.tgz) C:\OpenFoam a központi csomóponton, hogy a Linux-csomópontok a /openfoam keresztül elérhető ezt a fájlt. Ezután futtassa **clusrun** Intel MPI erőforrástár telepítése a Linux-csomópontokat.

1. A következő parancsok másolja a csomagot, és csomagolja a /opt/intel minden egyes csomóponton.
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /opt/intel
   
   clusrun /nodegroup:LinuxNodes cp /openfoam/l_mpi_p_5.0.3.048.tgz /opt/intel/
   
   clusrun /nodegroup:LinuxNodes tar -xzf /opt/intel/l_mpi_p_5.0.3.048.tgz -C /opt/intel/
   ```
2. Az Intel MPI könyvtár csendes telepítéséhez használjon silent.cfg fájlt. Ez a cikk végén található minta fájljaiban találhat egy példát. A megosztott mappa /openfoam helyezze a fájlt. A silent.cfg fájllal kapcsolatos részletekért lásd: [Intel MPI Library for Linux telepítővarázslót - beavatkozás nélküli telepítés](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html#silentinstall).
   
   > [!TIP]
   > Győződjön meg arról, hogy a Linux rendszerű szöveges fájlból silent.cfg fájl mentésekor sorvégeket (csak LF, nem a CR Soremelés). Ez a lépés biztosítja, hogy megfelelően az fut a Linux-csomópontokat.
   > 
   > 
3. Az Intel MPI erőforrástár telepítése csendes módban.
   
   ```
   clusrun /nodegroup:LinuxNodes bash /opt/intel/l_mpi_p_5.0.3.048/install.sh --silent /openfoam/silent.cfg
   ```

### <a name="configure-mpi"></a>MPI konfigurálása
Tesztelési, a következő sorokat kell hozzáadni a /etc/security/limits.conf a Linux-csomópontok egyes:

    clusrun /nodegroup:LinuxNodes echo "*               hard    memlock         unlimited" `>`> /etc/security/limits.conf
    clusrun /nodegroup:LinuxNodes echo "*               soft    memlock         unlimited" `>`> /etc/security/limits.conf


Indítsa újra a Linux-csomópontokat, a limits.conf fájl frissítése után. Ha például használja a következő **clusrun** parancsot:

```
clusrun /nodegroup:LinuxNodes systemctl reboot
```

Az újraindítás után győződjön meg arról, hogy a megosztott mappa /openfoam, csatlakoztatva van-e.

### <a name="compile-and-install-openfoam"></a>Fordítsa le és telepítse az OpenFOAM
Mentse a letöltött telepítési csomag OpenFOAM forrás csomagnak (OpenFOAM 2.3.1.tgz ebben a példában) a C:\OpenFoam a központi csomóponton, hogy a Linux-csomópontok a /openfoam keresztül elérhető ez a fájl. Ezután futtassa **clusrun** OpenFOAM fordítása a Linux-csomópontok a parancsokat.

1. Hozzon létre egy mappát /opt/OpenFOAM minden egyes Linux-csomóponton, a forrás-csomagot másolja ezt a mappát, és csomagolja ki, hogy.
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /opt/OpenFOAM
   
   clusrun /nodegroup:LinuxNodes cp /openfoam/OpenFOAM-2.3.1.tgz /opt/OpenFOAM/
   
   clusrun /nodegroup:LinuxNodes tar -xzf /opt/OpenFOAM/OpenFOAM-2.3.1.tgz -C /opt/OpenFOAM/
   ```
2. Fordítsa le és az Intel MPI OpenFOAM, először állítsa be néhány környezeti változókat az Intel MPI-t és az OpenFOAM is. Settings.sh nevű bash parancsfájl használatával állítsa be a változókat. Ez a cikk végén található minta fájljaiban találhat egy példát. Ezt a fájlt (a Linux sorvégződések mentett) jelölje be a megosztott mappa /openfoam. Ez a fájl is tartalmazza, amellyel később egy OpenFOAM feladat futtatásához MPI és OpenFOAM modulok.
3. Az OpenFOAM összeállításához függő csomagok telepítéséhez. A Linux-disztribúciótól függően előfordulhat, hogy először hozzá kell egy tárház. Futtatás **clusrun** az alábbihoz hasonló parancsokat:
   
    ```
    clusrun /nodegroup:LinuxNodes zypper ar http://download.opensuse.org/distribution/13.2/repo/oss/suse/ opensuse
   
    clusrun /nodegroup:LinuxNodes zypper -n --gpg-auto-import-keys install --repo opensuse --force-resolution -t pattern devel_C_C++
    ```
   
    Ha szükséges, minden egyes Linux csomópont ssh-KAPCSOLATOT annak ellenőrzéséhez, hogy azok megfelelően fusson a parancsok futtatásához.
4. Futtassa a következő parancsot az OpenFOAM összeállításához. A fordítási folyamat eltarthat egy ideig, és a normál a kimenetbe naplóadatok nagy mennyiségű állít elő, ezért a **/ közbeékeléses** megjelenítéséhez a kimenetben közbeékeléses lehetőséget.
   
   ```
   clusrun /nodegroup:LinuxNodes /interleaved source /openfoam/settings.sh `&`& /opt/OpenFOAM/OpenFOAM-2.3.1/Allwmake
   ```
   
   > [!NOTE]
   > A "\`" szimbólumot a parancsot a PowerShell-escape szimbólum. "\`&" azt jelenti, hogy a "&" a parancs része.
   > 
   > 

## <a name="prepare-to-run-an-openfoam-job"></a>Az OpenFOAM feladat futtatásához előkészítése
Most már készüljön fel egy nevű sloshingTank3D, amely egy OpenFoam mintát, a két Linux-csomópontok MPI-feladat futtatásához. 

### <a name="set-up-the-runtime-environment"></a>A futtatókörnyezet beállítása
Állítsa be a futtatókörnyezetet MPI és OpenFOAM, a Linux-csomópontokat, futtassa a következő parancsot egy Windows PowerShell-ablakban a fő csomópont. (Ez a parancs érvénytelen, a SUSE Linux csak.)

```
clusrun /nodegroup:LinuxNodes cp /openfoam/settings.sh /etc/profile.d/
```

### <a name="prepare-sample-data"></a>Mintaadatok létrehozása
Fájlmegosztás között a Linux-csomópontok (/openfoam meghajtóként csatlakoztatott) korábban konfigurált átjárócsomópont megosztás használatára.

1. SSH a Linux számítási csomópontok.
2. A következő parancsot az OpenFOAM futásidejű környezetet, ha Ön még nem tette meg.
   
   ```
   $ source /openfoam/settings.sh
   ```
3. A sloshingTank3D minta másolása a megosztott mappához, és keresse meg a fájlt.
   
   ```
   $ cp -r $FOAM_TUTORIALS/multiphase/interDyMFoam/ras/sloshingTank3D /openfoam/
   
   $ cd /openfoam/sloshingTank3D
   ```
4. Alapértelmezett paramétereihez tartozó Ez a minta használata esetén tíz percig szeretné futtatni, így előfordulhat, hogy szeretné, hogy gyorsabban futnak bizonyos paramétereit is eltarthat. Egy egyszerű lehetőséget, hogy az idő lépés változók deltaT és a rendszer/controlDict fájlban writeInterval módosítása. Ez a fájl tartalmazza az idő és az adatok írásakor vagy olvasásakor megoldás ellenőrzésére vonatkozó összes bemeneti adatot. Például sikerült módosítani a 0,05 deltaT 0,5 értékét és a 0,05 writeInterval 0,5 értékét.
   
   ![Módosítsa a változókat. lépés][step_variables]
5. Adja meg a kívánt a változók értékeit a rendszer/decomposeParDict fájlban. Ebben a példában két Linux-csomópontok egyes 8 maggal rendelkező, ezért numberOfSubdomains 16 és hierarchicalCoeffs, n (1 1 16), ami azt jelenti, az OpenFOAM futtatása 16 folyamatokkal párhuzamosan. További információkért lásd: [OpenFOAM felhasználói útmutatója: 3.4 futó alkalmazások párhuzamosan](http://cfd.direct/openfoam/user-guide/running-applications-parallel/#x12-820003.4).
   
   ![Folyamatok bontható fel][decompose]
6. Futtassa az alábbi parancsokat a mintaadatok létrehozása a sloshingTank3D könyvtárból.
   
   ```
   $ . $WM_PROJECT_DIR/bin/tools/RunFunctions
   
   $ m4 constant/polyMesh/blockMeshDict.m4 > constant/polyMesh/blockMeshDict
   
   $ runApplication blockMesh
   
   $ cp 0/alpha.water.org 0/alpha.water
   
   $ runApplication setFields  
   ```
7. A központi csomóponton megtekintheti a mintaadatfájlok C:\OpenFoam\sloshingTank3D másolódnak. (C:\OpenFoam a központi csomóponton a megosztott mappához.)
   
   ![Az átjárócsomópont adatfájlokat][data_files]

### <a name="host-file-for-mpirun"></a>Mpirun gazdagép-példafájlja
Ebben a lépésben, hozzon létre egy gazdagép fájlt (a számítási csomópontok listája) amely a **mpirun** parancsot használja.

1. A Linux-csomópontok egyikét hozzon létre egy fájlt hostfile alatt /openfoam, ezért ezt a fájlt az összes Linux csomóponton /openfoam/hostfile címen érhető el.
2. A Linux-csomópont nevét írja, ezt a fájlt. Ebben a példában a fájl tartalmazza a következő nevekkel:
   
   ```       
   SUSE12RDMA-LN1
   SUSE12RDMA-LN2
   ```
   
   > [!TIP]
   > Ez a fájl C:\OpenFoam\hostfile, a központi csomóponton is létrehozhat. Ha ezt a lehetőséget választja, mentse a fájt Linux szövegfájl sorvégeket (csak LF, nem a CR Soremelés). Ez biztosítja, hogy megfelelően az fut a Linux-csomópontokat.
   > 
   > 
   
   **Bash parancsfájl burkoló**
   
   Ha számos Linux-csomóponttal rendelkezik, és azt szeretné, hogy a feladat csak az egyes futtatni, azt, nem célszerű használni egy rögzített gazdagépet a fájlt, mert a feladathoz lefoglalt csomópontok nem tudja. Ebben az esetben írhat egy bash parancsfájl burkoló **mpirun** automatikusan létrehozhatja a gazdagép fájlt. Egy példa bash parancsfájl burkoló nevű hpcimpirun.sh Ez a cikk végén található, és mentse /openfoam/hpcimpirun.sh. Ez a példa a szkript a következőket teszi:
   
   1. Beállítja a környezeti változókat **mpirun**, és néhány hozzáadása parancs paraméterei a MPI-feladat futtatása az rdma-t a hálózaton keresztül. Ebben az esetben azt állítja be az alábbi változókat:
      
      * I_MPI_FABRICS=shm:dapl
      * I_MPI_DAPL_PROVIDER=ofa-v2-ib0
      * I_MPI_DYNAMIC_CONNECTION = 0
   2. Létrehoz egy gazdagép megfelelően a környezeti változó $CCP_NODES_CORES, amely a HPC fő csomópontja úgy van beállítva, ha a feladat aktiválódik.
      
      $CCP_NODES_CORES formátumát ezt a mintát követi:
      
      ```
      <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
      ```
      
      ahol
      
      * `<Number of nodes>` – Ez a feladat számára lefoglalt csomópontok számát.  
      * `<Name of node_n_...>` – Ez a feladat számára lefoglalt minden egyes csomópont nevét.
      * `<Cores of node_n_...>` – a csomóponton a feladat számára lefoglalt magok számát.
      
      Például ha a feladat futtatásához két csomópont van szüksége, $CCP_NODES_CORES hasonlít a
      
      ```
      2 SUSE12RDMA-LN1 8 SUSE12RDMA-LN2 8
      ```
   3. Hívások a **mpirun** parancsot, és hozzáfűzi a két paramétert a parancssorból.
      
      * `--hostfile <hostfilepath>: <hostfilepath>` -a parancsfájl létrehozza a gazdagép-fájl elérési útja
      * `-np ${CCP_NUMCPUS}: ${CCP_NUMCPUS}` -a HPC Pack fő csomópontot, amely tárolja a teljes, a feladat számára lefoglalt magok száma által meghatározott környezeti változóban. Ebben az esetben adja meg a folyamatok számát **mpirun**.

## <a name="submit-an-openfoam-job"></a>Az OpenFOAM feladat elküldése
Most már küldhet egy feladatot a HPC Cluster Manager. A feldolgozás feladatokat adja át a parancsfájl hpcimpirun.sh parancssorok kell.

1. A fürt átjárócsomópontjához csatlakozik, és indítsa el a HPC Cluster Manager.
2. **Az erőforrás-kezelés**, győződjön meg arról, hogy a Linuxos számítási csomópontok szerepelnek a **Online** állapota. Ha nem, válassza ki azokat, és kattintson a **online állapotba hozás**.
3. A **feladatkezelés**, kattintson a **új feladat**.
4. Adja meg például a feladat nevét *sloshingTank3D*.
   
   ![Feladat részletei][job_details]
5. A **feladat-erőforrások**, válassza ki a "Csomópont" erőforrás típusát, és a minimális értéke 2. Ez a konfiguráció a feladatot futtat Linux két csomópontot tartalmaz, ebben a példában nyolc processzormaggal, amelyek mindegyike rendelkezik.
   
   ![Feladat-erőforrások][job_resources]
6. Kattintson a **szerkesztése feladatok** a bal oldali navigációs, és kattintson a **Hozzáadás** feladat hozzáadása a feladathoz. Négy tevékenységek hozzáadása a feladathoz a következő parancssorok és a beállítások.
   
   > [!NOTE]
   > Futó `source /openfoam/settings.sh` állítja be az OpenFOAM és MPI futtatókörnyezetet, így az összes alábbi feladatot meghívja az OpenFOAM parancs előtt.
   > 
   > 
   
   * **1. feladat**. Futtatás **decomposePar** futtatásához adatfájlokat létrehozni **interDyMFoam** párhuzamosan.
     
     * A feladat egy csomópont hozzárendelése
     * **Parancssor** - `source /openfoam/settings.sh && decomposePar -force > /openfoam/decomposePar${CCP_JOBID}.log`
     * **Munkakönyvtár** -/ openfoam/sloshingTank3D
     
     Az alábbi ábra illusztrálja. Hasonlóképp konfigurálhatja a hátralévő műveletekkel.
     
     ![1. feladat részletei][task_details1]
   * **2. feladat**. Futtatás **interDyMFoam** a mintául szolgáló számítási párhuzamosan.
     
     * A feladat két csomópont hozzárendelése
     * **Parancssor** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh interDyMFoam -parallel > /openfoam/interDyMFoam${CCP_JOBID}.log`
     * **Munkakönyvtár** -/ openfoam/sloshingTank3D
   * **3. feladat**. Futtatás **reconstructPar** a készletek minden processor_N_ directory címtárat idő egyesíthet egyetlen.
     
     * A feladat egy csomópont hozzárendelése
     * **Parancssor** - `source /openfoam/settings.sh && reconstructPar > /openfoam/reconstructPar${CCP_JOBID}.log`
     * **Munkakönyvtár** -/ openfoam/sloshingTank3D
   * **4. feladat**. Futtatás **foamToEnsight** konvertálni az OpenFOAM eredmény párhuzamosan EnSight fájlok formázhatja és a EnSight fájlokat helyezze Ensight nevű megkülönbözteti a kis a címtárban.
     
     * A feladat két csomópont hozzárendelése
     * **Parancssor** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh foamToEnsight -parallel > /openfoam/foamToEnsight${CCP_JOBID}.log`
     * **Munkakönyvtár** -/ openfoam/sloshingTank3D
7. Adja hozzá ezeket a feladatokat, növekvő sorrendben feladat függőségek.
   
   ![Tevékenységfüggőségek][task_dependencies]
8. Kattintson a **küldés** Ez a feladat futtatásához.
   
   Alapértelmezés szerint a HPC Pack elküldi a feladatot, az aktuális bejelentkezett felhasználói fiókkal. Miután rákattintott **küldés**, megjelenhet egy párbeszédpanel, ahol megadhatja a felhasználónevet és jelszót.
   
   ![Feladat hitelesítő adatai][creds]
   
   Bizonyos körülmények között a HPC Pack megjegyzi a bemenő előtt, és ezen a párbeszédpanelen nem jelenik meg a felhasználói adatokat. HPC Pack jelenjen meg. Adja meg a következő parancsot a parancssorba, és majd a feladat elküldéséhez.
   
   ```
   hpccred delcreds
   ```
9. A feladat szerint a paraméterek meg van a minta több órát a tíz percet vesz igénybe. Az intenzitástérkép láthatja a feladat futtatásakor Linux. 
   
   ![Hőtérkép][heat_map]
   
   Minden egyes csomóponton nyolc folyamatok el lesz indítva.
   
   ![Linux-folyamatok][linux_processes]
10. A feladat befejeztével a feladat eredményeinek található C:\OpenFoam\sloshingTank3D, és a naplófájlok, C:\OpenFoam mappát.

## <a name="view-results-in-ensight"></a>EnSight az eredmények megtekintése
Lehetősége van [EnSight](http://www.ensight.com/) vizualizálását és elemzését az OpenFOAM feladat eredményét. 

1. Miután telepítette a fő csomópontot EnSight, indítsa el.
2. Nyissa meg a C:\OpenFoam\sloshingTank3D\EnSight\sloshingTank3D.case.
   
   Megjelenik a megjelenítőben tartály.
   
   ![A EnSight tartály][tank]
3. Hozzon létre egy **Isosurface** a **internalMesh**, majd válassza a változó **alpha_water**.
   
   ![Hozzon létre egy isosurface][isosurface]
4. Színét **Isosurface_part** az előző lépésben létrehozott. Például állítsa be azt a vízjelek kék.
   
   ![Szerkesztés isosurface színe][isosurface_color]
5. Hozzon létre egy **Iso-kötet** a **falak** kiválasztásával **falak** a a **részek** panelen, majd kattintson a **Isosurfaces** gomb az eszköztáron.
6. A párbeszédpanelen válassza ki a **típus** , **Isovolume** és állítsa be, a minimális **Isovolume tartomány** 0,5. A isovolume létrehozásához kattintson a **létrehozás a kiválasztott kijelzőkkel**.
7. Színét **Iso_volume_part** az előző lépésben létrehozott. Például állítsa be, a mély víz kék.
8. Színét **falak**. Például állítsa be, átlátható fehérre.
9. Most kattintson **lejátszása** szeretné megtekinteni a szimuláció eredményeit.
   
    ![Tartály eredménye][tank_result]

## <a name="sample-files"></a>Mintafájl
### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>Minta konfigurációs XML-fájl esetén a fürt PowerShell-parancsfájl által
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

### <a name="sample-settingssh-script"></a>A példaszkript settings.sh
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


### <a name="sample-hpcimpirunsh-script"></a>A példaszkript hpcimpirun.sh
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
