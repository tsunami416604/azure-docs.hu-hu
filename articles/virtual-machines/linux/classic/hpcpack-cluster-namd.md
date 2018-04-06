---
title: A Linux virtuális gépek Microsoft HPC Pack NAMD |} Microsoft Docs
description: Azure a Microsoft HPC Pack fürt központi telepítése, és futtassa a NAMD szimuláció charmrun több Linux számítási csomóponton
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: 76072c6b-ac35-4729-ba67-0d16f9443bd7
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 10/13/2016
ms.author: danlep
ms.openlocfilehash: 61dd49d4bd3183b6b9a78036d6d7d01798e4dc89
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="run-namd-with-microsoft-hpc-pack-on-linux-compute-nodes-in-azure"></a>Az NAMD futtatása a Microsoft HPC Packkal Azure-beli linuxos számítási csomópontokon
Ez a cikk az Azure virtuális gépeken a Linux nagy teljesítményű számítástechnikai rendszerek (HPC) munkaterhelések futtatásához egy módszert mutat. Itt, állítsa be a [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) -fürtöt az Azure-on Linux számítási csomópontokat, majd futtassa egy [NAMD](http://www.ks.uiuc.edu/Research/namd/) szimuláció alapján számítja ki és jelenítheti meg a nagy biomolekuláris rendszer szerkezetének.  

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

* **NAMD** (Nanoscale molekuláris Dynamics programhoz) csomag egy párhuzamos molekuláris dynamics nagy teljesítményű szimuláció legfeljebb atom millióit tartalmazó nagy biomolekuláris rendszerek tervezték. Ezek a rendszerek közé vírusok, cella és a nagy fehérjék. NAMD arányosan több száz tipikus szimulációja magos, és több mint 500 000 magos a legnagyobb szimulációja.
* **A Microsoft HPC Pack** fürtök helyszíni számítógépekre vagy Azure virtuális gépek nagy méretű HPC és párhuzamos alkalmazások futtatásához szolgáltatásokat biztosítja. Eredetileg fejlesztett munkaterheléseknél Windows HPC HPC Pack megoldásként mostantól támogatja a futó Linux HPC alkalmazások Linux rendszeren telepített HPC Pack-fürtben lévő csomópont virtuális gépek számítási. Lásd: [Ismerkedés az Azure-ban HPC Pack-fürtben lévő Linux számítási csomópontok](hpcpack-cluster.md) bevezető.

## <a name="prerequisites"></a>Előfeltételek
* **A számítási csomópontok HPC Pack fürt Linux** -fürt üzembe helyezése HPC Pack Linux számítási csomópontok az Azure-ban vagy egy [Azure Resource Manager sablon](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) vagy egy [Azure PowerShell-parancsfájl](hpcpack-cluster-powershell-script.md). Lásd: [Ismerkedés az Azure-ban HPC Pack-fürtben lévő Linux számítási csomópontok](hpcpack-cluster.md) az Előfeltételek és az egyik beállítási mód lépéseit. Ha a PowerShell parancsfájl központi telepítés lehetőséget választja, lásd: a minta konfigurációs fájlt a mintafájlok, ez a cikk végén. Ez a fájl az Azure-alapú HPC Pack fürtöt egy Windows Server 2012 R2 átjárócsomópont és a számítási csomópontok négy mérete nagy CentOS 6.6 konfigurálja. Testre szabhatja ezt a fájlt, a saját környezetéhez szükséges módon.
* **Szoftver- és oktatóanyag fájlok NAMD** -letöltése NAMD szoftverek a Linux a [NAMD](http://www.ks.uiuc.edu/Research/namd/) hely (regisztráció szükséges). Ez a cikk NAMD verzió 2.10 alapul, és használja a [Linux-x86_64 (64 bites Intel vagy AMD Ethernet)](http://www.ks.uiuc.edu/Development/Download/download.cgi?UserID=&AccessCode=&ArchiveID=1310) archív. Töltse le a is a [NAMD oktatóanyag fájlok](http://www.ks.uiuc.edu/Training/Tutorials/#namd). A letöltések .tar fájlokat, és egy Windows eszköz a fájlok a fürt átjárócsomópontjába a van szüksége. A fájlok kibontásához, kövesse a cikk későbbi részében. 
* **VMD** (nem kötelező) – a NAMD feladat eredményének megtekintéséhez, töltse le és telepítse a molekuláris képi megjelenítés program [VMD](http://www.ks.uiuc.edu/Research/vmd/) a kiválasztott számítógépen. A jelenlegi verzió: 1.9.2. Tekintse meg a VMD, töltse le a hely a kezdéshez.  

## <a name="set-up-mutual-trust-between-compute-nodes"></a>Kölcsönös, a számítási csomópontok közötti megbízhatósági kapcsolat beállítása
Megbízhatósági kapcsolat áll fenn a csomópontok egy kereszt-csomópont feladat futó Linux több csomópont szükséges (által **rsh** vagy **ssh**). Ha a Microsoft HPC Pack IaaS telepítési parancsfájl a HPC Pack fürt létrehozásához, a parancsfájl automatikusan beállítja a rendszergazdai fiókhoz megadott állandó kölcsönös megbízhatósági. A nem rendszergazda felhasználók számára hoz létre a fürt a tartományban akkor a csomópontok közötti ideiglenes kölcsönös megbízhatóság kialakításához, ha egy feladat el lett kiosztva. Majd semmisítse meg a kapcsolat, a feladat befejezése után. Ehhez minden olyan felhasználóhoz, adja meg a fürthöz, amely HPC Pack használja a megbízhatósági kapcsolat létrehozására egy RSA kulcspár. Kövesse az utasításokat.

### <a name="generate-an-rsa-key-pair"></a>Egy RSA kulcspár létrehozása
Könnyen kulcspárlétrehozás egy RSA, egy nyilvános kulcs és a titkos kulcsot tartalmazó által a Linux operációs rendszert futtató **ssh-keygen** parancsot.

1. Jelentkezzen be a Linux-számítógép.
2. Futtassa az alábbi parancsot:
   
   ```bash
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
1. [Csatlakozás távoli asztal](../../windows/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) az átjárócsomóponthoz VM tartományba megadott hitelesítő adatok, amikor központilag telepítette a fürt (például hpc\clusteradmin). A fürt kezelése a központi csomópontból.
2. Szabványos Windows Server-eljárások segítségével hozzon létre egy tartományfelhasználói fiókot a fürt Active Directory-tartományban. Az Active Directory – felhasználók és számítógépek eszközt például használatát az átjárócsomópont. Ebben a cikkben szereplő példák azt feltételezik, hogy hpcuser hpclab tartományban (hpclab\hpcuser) nevű tartományi felhasználót kell létrehozni.
3. A tartományi felhasználó hozzáadása a HPC Pack fürt fürt felhasználóként. Útmutatásért lásd: [hozzáadását vagy eltávolítását fürt felhasználók](https://technet.microsoft.com/library/ff919330.aspx).
4. Hozzon létre egy C:\cred.xml nevű fájlt, és az RSA-kulcs adatait átmásolja azt. A mintafájlok, ez a cikk végén található példa.
   
   ```
   <ExtendedData>
     <PrivateKey>Copy the contents of private key here</PrivateKey>
     <PublicKey>Copy the contents of public key here</PublicKey>
   </ExtendedData>
   ```
5. Nyisson meg egy parancssort, és írja be a következő parancsot a hpclab\hpcuser fiók a hitelesítő adatok beállításához. Használja a **extendeddata** paraméter felelt meg a létrehozott C:\cred.xml fájl a fő adatok nevét.
   
   ```command
   hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
   ```
   
   Ez a parancs sikeresen kimeneti nélkül befejeződött. Miután beállította a hitelesítő adatok a feladatok futtatásához szükséges felhasználói fiókok, a cred.xml fájlt tárolja biztonságos helyen, vagy törölje azt.
6. Ha az RSA kulcspár a Linux-csomópontok egyikén jön létre, ne felejtse el azok használatának befejezése után törölje. Ha úgy találja, hogy egy meglévő id_rsa vagy id_rsa.pub fájl nincs beállítva HPC Pack kölcsönös megbízhatósági.

> [!IMPORTANT]
> Nem ajánlott a Linux feladat futtatásával a fürt rendszergazdai megosztott fürtön, mivel a Linux-csomópontokon a legfelső szintű fiók alatt fut egy feladatot, a rendszergazda által küldött. Egy feladat, a nem rendszergazda felhasználó által a neve megegyezik a feladat felhasználó Linux helyi felhasználói fiók alatt fut. Ebben az esetben HPC Pack állít be a Linux-felhasználó kölcsönös megbízhatósági a feladathoz kiosztott összes csomópont. A feladat futtatása előtt manuálisan a Linux-csomópontokon a Linux felhasználói állíthat be, vagy a HPC Pack a felhasználó automatikusan létrehozza a feladat elküldésekor. HPC Pack a felhasználó hoz létre, ha HPC Pack törli őket a feladat befejezése után. Biztonsági veszély csökkentése érdekében a kulcsok el lesznek távolítva a feldolgozás után a csomóponton.
> 
> 

## <a name="set-up-a-file-share-for-linux-nodes"></a>Linux-csomópontok fájlmegosztás beállítása
Most már SMB-fájlmegosztás beállítása, és csatlakoztassa a megosztott mappa minden csomóponton Linux engedélyezése a Linux-csomópontok közös elérési úttal rendelkező NAMD fájlok eléréséhez. Az alábbiakban egy megosztott mappába a központi csomóponton csatlakoztatni lépéseket. A megosztás például a CentOS 6.6 terjesztéseket, amelyek jelenleg nem támogatják az Azure File service ajánlott. Ha a Linux-csomópontok támogatja egy Azure-fájlmegosztás, látható [Azure File storage használata Linux](../../../storage/files/storage-how-to-use-files-linux.md). További fájl megosztási beállítások HPC Pack, lásd: [Ismerkedés az Azure-ban HPC Pack fürtben lévő Linux számítási csomópontok](hpcpack-cluster.md).

1. Hozzon létre egy mappát az átjárócsomóponthoz, és úgy, hogy olvasási/írási engedélyekkel mindenki számára ossza meg. Ebben a példában \\ \\CentOS66HN\Namd a mappát, ahol CentOS66HN az átjárócsomópont állomásneve nevét.
2. Hozzon létre egy almappát namd2 a megosztott mappában. Namd2 hozzon létre egy másik almappát namdsample.
3. Bontsa ki a mappában lévő fájlok NAMD által a Windows verziójával **bont** vagy egy másik Windows-segédprogram, amely .tar kiterjesztett archívumokat. 
   
   * Bontsa ki a NAMD bont archív való \\ \\CentOS66HN\Namd\namd2.
   * Az oktatóanyag fájlok kibontása \\ \\CentOS66HN\Namd\namd2\namdsample.
4. Nyisson meg egy Windows PowerShell-ablakot, és futtassa az alábbi parancsokat a Linux-csomópontokon a megosztott mappa csatlakoztatni.
   
    ```command
    clusrun /nodegroup:LinuxNodes mkdir -p /namd2
   
    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS66HN/Namd/namd2 /namd2 -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

Az első parancs létrehoz egy /namd2 a LinuxNodes csoport minden csomópontján nevű mappát. A második parancs csatlakoztatja a megosztott mappa //CentOS66HN/Namd/namd2 alakzatot dir_mode mappát, majd file_mode bitet 777. A *felhasználónév* és *jelszó* parancsban kell lennie az átjárócsomópont a felhasználó hitelesítő adatait.

> [!NOTE]
> A "\`" szimbólumra a második parancs a értéke egy escape szimbólum PowerShell. "\`," a "," (vessző karakter) azt jelenti, hogy a parancs része.
> 
> 

## <a name="create-a-bash-script-to-run-a-namd-job"></a>Hozzon létre egy Bash parancsfájlt NAMD feladat futtatása
A NAMD feladatot kell egy *csomópontlista* fájlt **charmrun** NAMD folyamatok indításakor használandó csomópontok számának megállapításához. A Bash parancsfájlt, amely a csomópontlista fájlt hoz létre, és futtatja **charmrun** a csomópontlista fájllal. Majd elküldheti a HPC-Fürtkezelőben ezt a parancsfájlt meghívó NAMD feladat.

Az Ön által választott szövegszerkesztő használatával hozzon létre egy Bash parancsprogramot a NAMD programfájljait tartalmazó /namd2 mappában, és nevezze el hpccharmrun.sh. A koncepció igazolása kész, másolja át a cikk végén megadott példa hpccharmrun.sh parancsfájl, és navigáljon [NAMD feladat elküldése](#submit-a-namd-job).

> [!TIP]
> Mentse a parancsfájlt a Linux és szövegfájlként sorvégeket a (csak LF, nem a CR LF). Ez biztosítja, hogy fusson megfelelően a Linux-csomóponton.
> 
> 

Az alábbiakban a bash parancsfájlok funkciója részleteit. 

1. Adja meg a bizonyos változókat.
   
   ```bash
   #!/bin/bash
   
   # The path of this script
   SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
   # Charmrun command
   CHARMRUN=${SCRIPT_PATH}/charmrun
   # Argument of ++nodelist
   NODELIST_OPT="++nodelist"
   # Argument of ++p
   NUMPROCESS="+p"
   ```
2. Csomópont információ lekérése a környezeti változók. $NODESCORES $CCP_NODES_CORES vegyes szavak listáját tárolja. $COUNT $NODESCORES mérete.
   
   ```
   # Get node information from the environment variables
   NODESCORES=(${CCP_NODES_CORES})
   COUNT=${#NODESCORES[@]}
   ```    
   
   A $CCP_NODES_CORES változó formátuma a következő:
   
   ```
   <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>…
   ```
   
   Ez a változó sorolja fel a csomópontok csomópontnevek és minden csomóponton, a feladat lefoglalt magok száma teljes száma. Például ha a feladat futtatásához 10 mag, $CCP_NODES_CORES értéke hasonló:
   
   ```
   3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 2
   ```
3. Ha a $CCP_NODES_CORES változó nincs beállítva, indítsa el **charmrun** közvetlenül. (Ez csak történjen, ha a parancsfájl futtatását közvetlenül a Linux-csomóponton.)
   
   ```
   if [ ${COUNT} -eq 0 ]
   then
     # CCP_NODES is_CORES is not found or is empty, so just run charmrun without nodelist arg.
     #echo ${CHARMRUN} $*
     ${CHARMRUN} $*
   ```
4. Vagy hozzon létre egy csomópontlista fájlt **charmrun**.
   
   ```
   else
     # Create the nodelist file
     NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$
   
     # Write the head line
     echo "group main" > ${NODELIST_PATH}
   
     # Get every node name and number of cores and write into the nodelist file
     I=1
     while [ ${I} -lt ${COUNT} ]
     do
         echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
         let "I=${I}+2"
     done
   ```
5. Futtatás **charmrun** a csomópontlista fájl, a visszatérési állapota, és távolítsa el a csomópontlista fájlt végén.
   
   {CCP_NUMCPUS} $ egy másik, a HPC Pack átjárócsomópont által beállított környezeti változó. Az összes, a feladat számára lefoglalt magok száma tárol. Használjuk charmrun folyamatok száma.
   
   ```
   # Run charmrun with nodelist arg
   #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
   ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
   
   RTNSTS=$?
   rm -f ${NODELIST_PATH}
   fi
   
   ```
6. Kilép, és a **charmrun** visszatérési állapota.
   
   ```
   exit ${RTNSTS}
   ```

Az alábbiakban látható a csomópontlista fájl, amely állít elő, a parancsfájl:

```
group main
host <Name of node1> ++cpus <Cores of node1>
host <Name of node2> ++cpus <Cores of node2>
…
```

Példa:

```
group main
host CENTOS66LN-00 ++cpus 4
host CENTOS66LN-01 ++cpus 4
host CENTOS66LN-03 ++cpus 2
```

## <a name="submit-a-namd-job"></a>NAMD feladat elküldése
Most már készen áll a HPC Cluster Manager NAMD feladat elküldése.

1. A fürt átjárócsomópontjához csatlakozik, és indítsa el a HPC Cluster Manager.
2. A **erőforrás-kezelés**, akkor győződjön meg arról, hogy a Linux számítási csomópontok a **Online** állapotát. Ha nem, válassza ki azokat, és kattintson a **online állapotba hozás**.
3. A **feladatkezelés**, kattintson a **új feladat**.
4. Írjon be egy feladat nevét, például *hpccharmrun*.
   
   ![Új HPC-feladat][namd_job]
5. Az a **feladat részletei** lap **feladat erőforrások**, adja meg, mint erőforrás **csomópont** és állítsa be a **minimális** 3. , három Linux csomópontján futtassa azt a feladatot, és minden fürtcsomópont négy magok.
   
   ![Feladat-erőforrások][job_resources]
6. Kattintson a **feladatok szerkesztése** a bal oldali navigációs, majd **Hozzáadás** feladat hozzáadása a feladatot.    
7. Az a **feladat részleteinek és az i/o-átirányítás** lapján állítsa be a következő értékeket:
   
   * **Parancssor** -
     `/namd2/hpccharmrun.sh ++remote-shell ssh /namd2/namd2 /namd2/namdsample/1-2-sphere/ubq_ws_eq.conf > /namd2/namd2_hpccharmrun.log`
     
     > [!TIP]
     > A fenti parancssor az sortörés nélkül egyetlen paranccsal. Azt több sor alatt megjelenő becsomagolja **parancssori**.
     > 
     > 
   * **Munkakönyvtár** -/namd2
   * **Minimális** – 3
     
     ![Feladat részletei][task_details]
     
     > [!NOTE]
     > A munkakönyvtár mert itt beállított **charmrun** megpróbálja munkakönyvtára minden egyes csomóponton nyissa meg. A munkakönyvtár nem van beállítva, ha HPC Pack a parancs indítja el a Linux-csomópontok egyikén létrehozni egy véletlenszerűen előállított nevű mappát. Ez a következő hibát okoz a többi csomóponton: `/bin/bash: line 37: cd: /tmp/nodemanager_task_94_0.mFlQSN: No such file or directory.` a probléma elkerülése érdekében adjon meg egy mappa elérési útját, mint a munkakönyvtár összes csomópontja által elérhető.
     > 
     > 
8. Kattintson a **OK** majd **Submit** a feladat futtatásához.
   
   Alapértelmezés szerint a HPC Pack elküldi a feladatot, a jelenlegi bejelentkezett felhasználói fiókkal. A párbeszédpanel előfordulhat, hogy megadását kéri a felhasználónevet és jelszót kattintás után **Submit**.
   
   ![Feladat hitelesítő adatai][creds]
   
   Bizonyos körülmények között a HPC Pack emlékszik a felhasználói adatok előtt adjon meg, és nem jeleníti meg ezen a párbeszédpanelen. HPC Pack jelenjen meg többé ez a következő parancsot a parancssorba írja be, és ezután a feladat elküldéséhez.
   
   ```command
   hpccred delcreds
   ```
9. A feladat befejezéséhez néhány percet vesz igénybe.
10. A feladat naplójának található \\ <headnodeName>\Namd\namd2\namd2_hpccharmrun.log és a kimenő fájlok \\ <headnodeName>\Namd\namd2\namdsample\1-2-sphere\.
11. Szükség esetén indítsa el a feladat eredményeinek megtekintéséhez VMD. A lépéseket a NAMD megjelenítésére kimeneti fájlokat (a jelen esetben egy ubiquitin fehérje molekula a vízjel területén) túlmutatnak annak hatókörén, ez a cikk. Lásd: [NAMD oktatóanyag](http://www.life.illinois.edu/emad/biop590c/namd-tutorial-unix-590C.pdf) részleteiről.
    
    ![Feladat eredményeinek][vmd_view]

## <a name="sample-files"></a>Mintafájlok
### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>A minta XML konfigurációs fájl fürttelepítés PowerShell-parancsprogram
```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
      <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS66HN</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS66LN-%00%</VMNamePattern>
    <ServiceName>MyLnxCNService</ServiceName>
     <VMSize>Large</VMSize>
     <NodeCount>4</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-66-20150325</ImageName>
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

### <a name="sample-hpccharmrunsh-script"></a>Mintaparancsfájl hpccharmrun.sh
```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
# Charmrun command
CHARMRUN=${SCRIPT_PATH}/charmrun
# Argument of ++nodelist
NODELIST_OPT="++nodelist"
# Argument of ++p
NUMPROCESS="+p"

# Get node information from ENVs
# CCP_NODES_CORES=3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 4
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # If CCP_NODES_CORES is not found or is empty, just run the charmrun without nodelist arg.
    #echo ${CHARMRUN} $*
    ${CHARMRUN} $*
else
    # Create the nodelist file
    NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

    # Write the head line
    echo "group main" > ${NODELIST_PATH}

    # Get every node name & cores and write into the nodelist file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the charmrun with nodelist arg
    #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
    ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}
```

 

<!--Image references-->
[keygen]:media/hpcpack-cluster-namd/keygen.png
[keys]:media/hpcpack-cluster-namd/keys.png
[namd_job]:media/hpcpack-cluster-namd/namd_job.png
[job_resources]:media/hpcpack-cluster-namd/job_resources.png
[creds]:media/hpcpack-cluster-namd/creds.png
[task_details]:media/hpcpack-cluster-namd/task_details.png
[vmd_view]:media/hpcpack-cluster-namd/vmd_view.png
