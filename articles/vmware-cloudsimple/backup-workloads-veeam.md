---
title: Azure VMware-megoldás a CloudSimple-től – A számítási feladatok virtuális gépeinek biztonsági, a Veeam használatával történő biztonsági,
description: A cikk azt ismerteti, hogy miként biztonsági másolatot kaphat az Azure-alapú CloudSimple magánfelhőben futó virtuális gépekről a Veeam B&R 9.5 használatával
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3262841efb9109b1de24fe501ea0a7bea0dd612d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025129"
---
# <a name="back-up-workload-vms-on-cloudsimple-private-cloud-using-veeam-br"></a>A Számítási feladatok virtuális gépeinek biztonsági és biztonsági, a Veeam B&R használatával

Ez az útmutató bemutatja, hogyan biztonsági másolatot készít az Azure-alapú CloudSimple private cloud ban futó virtuális gépekről a Veeam B&R 9.5 használatával.

## <a name="about-the-veeam-back-up-and-recovery-solution"></a>A Veeam biztonsági mentéséről és a helyreállítási megoldásról

A Veeam megoldás a következő összetevőket tartalmazza.

**Biztonsági másolat kiszolgálója**

A biztonsági másolat kiszolgálója egy Windows-kiszolgáló (VM), amely a Veeam vezérlőközpontjaként szolgál, és a következő funkciókat hajtja végre: 

* A biztonsági mentés, a replikáció, a helyreállítás ellenőrzése és a feladatok visszaállítása koordinálása
* A feladatütemezés és az erőforrás-elosztás szabályozása
* Lehetővé teszi a biztonsági mentési infrastruktúra összetevőinek beállítását és kezelését, valamint a biztonsági mentési infrastruktúra globális beállításainak megadását

**Proxykiszolgálók**

A proxykiszolgálók a biztonsági másolat kiszolgálója és a biztonsági mentési infrastruktúra más összetevői között vannak telepítve. A következő funkciókat kezelik:

* Virtuálisgép-adatok lekérése az éles tárból
* Tömörítés
* Deduplikáció
* Titkosítás
* Adatok továbbítása a biztonsági másolat tárházához

**Biztonsági másolat tárháza**

A biztonsági másolat tárháza az a tárolóhely, ahol a Veeam biztonsági másolatokat, virtuális gépmásolatokat és metaadatokat tárol a replikált virtuális gépekhez.  A tárház lehet helyi lemezekkel (vagy csatlakoztatott NFS/SMB) rendelkező Windows vagy Linux-kiszolgáló, vagy hardveres tárolódeduplikációs készülék.

### <a name="veeam-deployment-scenarios"></a>Veeam telepítési forgatókönyvek
Az Azure-t kihasználva biztonsági mentési tárházat és tárolási célt biztosíthat a hosszú távú biztonsági mentéshez és archiváláshoz. A magánfelhőben lévő virtuális gépek és az Azure biztonsági másolattára közötti biztonsági mentési hálózati forgalom nagy sávszélességű, alacsony késleltetésű kapcsolaton keresztül történik. A régiók közötti replikációs forgalom a belső Azure hátlapi hálózaton keresztül halad, ami csökkenti a felhasználók sávszélességének költségeit.

**Egyszerű telepítés**

A 30 TB-nál kisebb biztonsági másolatot készítő környezetekben a CloudSimple a következő konfigurációt ajánlja:

* Veeam biztonsági mentési kiszolgáló és proxy kiszolgáló telepítve ugyanazon a virtuális gépa a magánfelhőben.
* Egy Linux-alapú elsődleges biztonsági mentési tárház az Azure-ban a biztonsági mentési feladatok célként konfigurálva.
* `azcopy`az adatok másolása az elsődleges biztonsági mentési tárházból egy Azure blob tárolóba, amely replikálása egy másik régióba.

![Alapvető telepítési forgatókönyvek](media/veeam-basicdeployment.png)

**Speciális telepítés**

A 30 TB-nál nagyobb biztonsági másolatot készítő környezetekben a CloudSimple a következő konfigurációt ajánlja:

* A vSAN-fürt csomópontonkénti egy proxykiszolgálója, a Veeam ajánlásának szerint.
* Windows alapú elsődleges biztonsági mentési tárház a magánfelhőben, hogy gyorsítótárazzanak öt nap nyi adatot a gyors visszaállításhoz.
* Linux biztonsági mentési tárház az Azure-ban, mint a cél a biztonsági mentési másolási feladatok hosszabb időtartamú megőrzése. Ezt a tárházat kibővített biztonsági mentési tárházként kell konfigurálni.
* `azcopy`az adatok másolása az elsődleges biztonsági mentési tárházból egy Azure blob tárolóba, amely replikálása egy másik régióba.

![Alapvető telepítési forgatókönyvek](media/veeam-advanceddeployment.png)

Az előző ábrán figyelje meg, hogy a biztonsági másolat proxy egy virtuális gép, amelynek gyors hozzáférés hozzáadása a vSAN-adattárban számítási feladatok virtuálisgép-lemezeihez. Veeam a virtuális berendezés biztonsági másolatproxy átviteli módját használja a vSAN-hoz.

## <a name="requirements-for-veeam-solution-on-cloudsimple"></a>A Veeam-megoldás követelményei a CloudSimple-en

A Veeam megoldás megköveteli, hogy tegye a következőket:

* Adja meg saját Veeam licenceit.
* Telepítse és kezelje a Veeam-et a CloudSimple private cloudban futó számítási feladatok biztonsági mentéséhez.

Ez a megoldás teljes körű vezérlést biztosít a Veeam biztonsági mentési eszköz felett, és a virtuális gép biztonsági mentési feladatainak kezeléséhez a natív Veeam felület vagy a Veeam vCenter beépülő modul használatát kínálja.

Ha Ön már veeam felhasználó, kihagyhatja a Veeam solution components szakaszát, és közvetlenül folytathatja a [Veeam telepítési forgatókönyveket.](#veeam-deployment-scenarios)

## <a name="install-and-configure-veeam-backups-in-your-cloudsimple-private-cloud"></a>Veeam biztonsági mentések telepítése és konfigurálása a CloudSimple private cloud szolgáltatásban

Az alábbi szakaszok ismertetik, hogyan telepítheti és konfigurálhatja a Veeam biztonsági mentési megoldás a CloudSimple private cloud.

A telepítési folyamat a következő lépésekből áll:

1. [vCenter felhasználói felület: Infrastruktúra-szolgáltatások beállítása a magánfelhőben](#vcenter-ui-set-up-infrastructure-services-in-your-private-cloud)
2. [CloudSimple portál: Privát felhőalapú hálózat beállítása veeamhoz](#cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam)
3. [CloudSimple portál: Jogosultságok eszkalálódása](#cloudsimple-private-cloud-escalate-privileges-for-cloudowner)
4. [Azure portal: A virtuális hálózat csatlakoztatása a magánfelhőhöz](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
5. [Azure Portal: Biztonsági tárház létrehozása az Azure-ban](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
6. [Azure portal: Konfigurálja az Azure blob storage-t a hosszú távú adatmegőrzéshez](#configure-azure-blob-storage-for-long-term-data-retention)
7. [vCenter felhasználói felület e magánfelhő: Telepítse veeam B&R](#vcenter-console-of-private-cloud-install-veeam-br)
8. [Veeam konzol: Konfigurálja a Veeam Backup & Recovery szoftvert](#veeam-console-install-veeam-backup-and-recovery-software)
9. [CloudSimple portál: Veeam-hozzáférés beállítása és a jogosultságok eszkalációjának mentesítése](#cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges)

### <a name="before-you-begin"></a>Előkészületek

A Veeam telepítésének megkezdése előtt a következőkre van szükség:

* Az Ön tulajdonában lévő Azure-előfizetés
* Előre létrehozott Azure-erőforráscsoport
* Egy Azure virtuális hálózat az előfizetésben
* Azure-tárfiók
* A CloudSimple portál használatával létrehozott [magánfelhő.](create-private-cloud.md)  

A végrehajtási szakaszban a következő elemekre van szükség:

* VMware-sablonok a Veeam telepítéséhez (például Windows Server 2012 R2 – 64 bites lemezkép)
* Egy rendelkezésre álló VLAN azonosított a biztonsági mentési hálózat
* A biztonsági másolat hálózatához rendelendő alhálózat CIDR-je
* Veeam 9.5 u3 telepíthető adathordozó (ISO) feltöltve a private cloud vSAN adattárába

### <a name="vcenter-ui-set-up-infrastructure-services-in-your-private-cloud"></a>vCenter felhasználói felület: Infrastruktúra-szolgáltatások beállítása a magánfelhőben

Konfigurálja az infrastruktúra-szolgáltatásokat a magánfelhőben, hogy megkönnyítse a számítási feladatok és eszközök kezelését.

* A [vCenter-identitásforrások beállítása](set-vcenter-identity.md) az Active Directory használatához az alábbi módon hozzáadhat külső identitásszolgáltatót, ha az alábbiak bármelyike vonatkozik:

  * Szeretné azonosítani a felhasználókat a helyszíni Active Directory (AD) a magánfelhőben.
  * Szeretne beállítani egy AD a privát felhőben az összes felhasználó számára.
  * Az Azure AD-t szeretné használni.
* Ha IP-címkeresési, IP-címkezelési és névfeloldási szolgáltatásokat szeretne biztosítani a magánfelhőben lévő számítási feladatokhoz, állítson be egy DHCP- és DNS-kiszolgálót a [DNS- és DHCP-alkalmazások és -számítási feladatok beállítása a CloudSimple magánfelhőben](dns-dhcp-setup.md)című részen leírtak szerint.

### <a name="cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam"></a>CloudSimple privát felhő: A Veeam privát felhőalapú hálózatának beállítása

A CloudSimple portálon beállítható a Magánfelhő-hálózat a Veeam megoldáshoz.

Hozzon létre egy VLAN-t a biztonsági másolat hálózatához, és rendeljen hozzá egy alhálózat CIDR-t. További információt a [VLAN-ok/alhálózatok létrehozása és kezelése című témakörben talál.](create-vlan-subnet.md)

Hozzon létre tűzfalszabályokat a felügyeleti alhálózat és a biztonsági másolat hálózata között, hogy lehetővé tegye a hálózati forgalmat a Veeam által használt portokon. Lásd a Veeam témakör [használt portok](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95). A tűzfalszabályok létrehozásáról a [Tűzfaltáblák és -szabályok beállítása (Tűzfaltáblák és szabályok) témakörben](firewall.md)talál.

Az alábbi táblázat portlistát tartalmaz.

| Ikon | Leírás | Ikon | Leírás |
| ------------ | ------------- | ------------ | ------------- |
| Biztonsági másolat kiszolgálója  | vCenter  | HTTPS / TCP  | 443 |
| Biztonsági másolat kiszolgálója <br> *A Veeam biztonsági mentés & replikációs összetevőinek telepítéséhez szükséges* | Biztonsági másolat proxyja  | TCP/UDP  | 135, 137-139 és 445 |
    | Biztonsági másolat kiszolgálója   | DNS  | UDP  | 53  | 
    | Biztonsági másolat kiszolgálója   | Veeam frissítési értesítési kiszolgáló  | TCP  | 80  | 
    | Biztonsági másolat kiszolgálója   | Veeam licencfrissítési kiszolgáló  | TCP  | 443  | 
    | Biztonsági másolat proxyja   | vCenter |   |   | 
    | Biztonsági másolat proxyja  | Linux biztonsági másolat tárháza   | TCP  | 22  | 
    | Biztonsági másolat proxyja  | Windows biztonsági másolat tárháza  | TCP  | 49152 - 65535   | 
    | Biztonsági másolat tárháza  | Biztonsági másolat proxyja  | TCP  | 2500 -5000  | 
    | Forrásbiztonsági adattár<br> *Biztonsági másolati feladatokhoz használatos*  | Célbiztonsági másolat tárháza  | TCP  | 2500 - 5000  | 

Hozzon létre tűzfalszabályokat a számítási feladatok alhálózata és a biztonsági másolat hálózata között a [Tűzfaltáblák és -szabályok beállítása](firewall.md)című részben leírtak szerint.  Az alkalmazás biztonsági mentéséhez és visszaállításához [további portokat](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95) kell megnyitni az adott alkalmazásokat tároló számítási feladatokon.

Alapértelmezés szerint a CloudSimple 1 Gbps-os ExpressRoute-kapcsolatot biztosít. Nagyobb környezetméretek esetén nagyobb sávszélességű kapcsolatra lehet szükség. A nagyobb sávszélességű kapcsolatokról további információkért forduljon az Azure ügyfélszolgálatához.

A telepítés folytatásához szüksége van az engedélyezési kulcsra és a társáramkör URI-jára, valamint az Azure-előfizetéshez való hozzáférésre.  Ez az információ a CloudSimple portál virtuális hálózati kapcsolatlapján érhető el. További információt az [Azure virtuális hálózat és a CloudSimple-kapcsolat társviszony-létesítési adatainak beszerzése](virtual-network-connection.md)című témakörben talál. Ha bármilyen problémája van az információ megszerzésével, forduljon az [ügyfélszolgálathoz.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

### <a name="cloudsimple-private-cloud-escalate-privileges-for-cloudowner"></a>CloudSimple privát felhő: A felhőtulajdonos jogosultságai nak eszkalálódása

Az alapértelmezett "felhőtulajdonos" felhasználó nem rendelkezik megfelelő jogosultságokkal a Private Cloud vCenter ben a VEEAM telepítéséhez, ezért a felhasználó vCenter-jogosultságait fokozni kell. További információ: [Jogosultságok elmélyítése](escalate-private-cloud-privileges.md).

### <a name="azure-portal-connect-your-virtual-network-to-the-private-cloud"></a>Azure portal: A virtuális hálózat csatlakoztatása a magánfelhőhöz

Csatlakoztassa a virtuális hálózatot a magánfelhőhöz az ExpressRoute használatával az [Azure virtuális hálózati kapcsolat utasításainak követésével.](azure-expressroute-connection.md)

### <a name="azure-portal-create-a-backup-repository-vm"></a>Azure Portal: Hozzon létre egy biztonsági tárház virtuális gép

1. Hozzon létre egy szabványos D2 v3 virtuális gép (2 vCPU és 8 GB memória).
2. Válassza ki a CentOS 7.4 alapú képet.
3. Konfiguráljon egy hálózati biztonsági csoportot (NSG) a virtuális géphez. Ellenőrizze, hogy a virtuális gép nem rendelkezik-e nyilvános IP-címmel, és nem érhető el a nyilvános internetről.
4. Hozzon létre egy felhasználónevet és jelszót alapú felhasználói fiókot az új virtuális géphez. További információt a [Linux-alapú virtuális gép létrehozása az Azure Portalon című témakörben talál.](../virtual-machines/linux/quick-create-portal.md)
5. Hozzon létre 1x512 GiB-szabványú HDD-t, és csatolja a virtuális tárházhoz.  További információt a [Felügyelt adatlemez csatlakoztatása Windows virtuális géphez az Azure Portalon című témakörben talál.](../virtual-machines/windows/attach-managed-disk-portal.md)
6. [XFS-kötet létrehozása a felügyelt lemezen](https://www.digitalocean.com/docs/volumes/how-to/). Jelentkezzen be a virtuális gépa korábban említett hitelesítő adatok használatával. Logikai kötet létrehozásához hajtsa végre a következő parancsfájlt, adja hozzá a lemezt, hozzon létre egy XFS [fájlrendszer-partíciót,](https://www.digitalocean.com/docs/volumes/how-to/partition/) és [csatlakoztassa](https://www.digitalocean.com/docs/volumes/how-to/mount/) a partíciót a /backup1 elérési út alá.

    Példa parancsfájl:

    ```
    sudo pvcreate /dev/sdc
    sudo vgcreate backup1 /dev/sdc
    sudo lvcreate -n backup1 -l 100%FREE backup1
    sudo mkdir -p /backup1
    sudo chown veeamadmin /backup1
    sudo chmod 775 /backup1
    sudo mkfs.xfs -d su=64k -d sw=1 -f /dev/mapper/backup1-backup1
    sudo mount -t xfs /dev/mapper/backup1-backup1 /backup1
    ```

7. Tegye elérhetővé a /backup1 kapcsolót NFS-csatlakoztatási pontként a magánfelhőben futó Veeam biztonsági mentési kiszolgálóhoz. További információt a Digital Ocean "Hogyan kell [beállítani egy NFS-tartó a CentOS 6 rendszeren"](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-centos-6)című cikkében talál. Ezt az NFS-megosztásnevet akkor használja, ha konfigurálja a biztonsági másolat tárházát a Veeam biztonsági másolat kiszolgálón.

8. Konfigurálja a szűrési szabályokat az NSG-ben a biztonsági másolat tárházvirtuális gép explicit módon lehetővé teszi az összes hálózati forgalmat a virtuális gép és a virtuális gép.

> [!NOTE]
> A Veeam Backup & Replication az SSH protokollt használja a Linux biztonsági mentési adattárakkal való kommunikációhoz, és megköveteli az SCP segédprogramot a Linux-adattárakon. Ellenőrizze, hogy az SSH démon megfelelően van-e konfigurálva, és hogy az SCP elérhető-e a Linux-gazdagépen.

### <a name="configure-azure-blob-storage-for-long-term-data-retention"></a>Az Azure blobstorage konfigurálása a hosszú távú adatmegőrzéshez

1. Hozzon létre egy általános célú tárfiókot (GPv2) szabványos típusú és egy blob tárolót a Microsoft első lépések az [Azure Storage szolgáltatással](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)című videójában leírtak szerint.
2. Hozzon létre egy Azure storage-tárolót, a tároló létrehozása hivatkozásban [leírtak](https://docs.microsoft.com/rest/api/storageservices/create-container) szerint.
2. Töltse `azcopy` le a Microsoft tól a Linux parancssori segédprogramját. A Következő parancsokat használhatja a CentOS 7.5 bash rendszerhéjában.

    ```
    wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
    tar -xf azcopy.tar.gz
    sudo ./install.sh
    sudo yum -y install libunwind.x86_64
    sudo yum -y install icu
    ```

3. A `azcopy` paranccsal biztonsági másolat fájlokat másolhat a blobtárolóba és onnan.  Részletes parancsokért [lásd: Adatátvitel az AzCopy-on Linuxon](../storage/common/storage-use-azcopy-linux.md) című témakörben.

### <a name="vcenter-console-of-private-cloud-install-veeam-br"></a>vCenter konzol private cloud: Telepítse Veeam B&R

A vCenter takaros felhőből elérheti a Veeam szolgáltatásfiókot, telepítheti a Veeam B&R 9.5-ös verziójával, és konfigurálhatja a Veeam szolgáltatást a szolgáltatásfiók használatával.

1. Hozzon létre egy új szerepkört nevű "Veeam backup szerepkör", és hozzá kell rendelni a szükséges engedélyeket által ajánlott Veeam. További részletek értelésért lásd a Veeam témakör [szükséges engedélyeket](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95).
2. Hozzon létre egy új "Veeam felhasználói csoport" csoportot a vCenterben, és rendelje hozzá a "Veeam biztonsági mentési szerepkör".Create a new 'Veeam User Group' group in vCenter and assign it the 'Veeam Backup Role'.
3. Hozzon létre egy új "Veeam Service Account" felhasználót, és adja hozzá a "Veeam felhasználói csoporthoz".

    ![Veeam szolgáltatásfiók létrehozása](media/veeam-vcenter01.png)

4. Hozzon létre egy elosztott portcsoportot a vCenterben a VLAN biztonsági mentési hálózat használatával. A részletekért tekintse meg a VMware [videót, amely elosztott portcsoportot hoz létre a vSphere webügyfélben.](https://www.youtube.com/watch?v=wpCd5ZbPOpA)
5. Hozza létre a virtuális gépeket a Veeam biztonsági mentési és proxykiszolgálóihoz a vCenterben a [Veeam rendszerkövetelményeinek megfelelően.](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95) Használhatja a Windows 2012 R2 vagy Linux. További információ: [Követelmények a Linux biztonsági mentési tárolók](https://www.veeam.com/kb2216).
6. Csatlakoztassa a telepíthető Veeam ISO-t CD-rom-eszközként a Veeam biztonsági mentési kiszolgáló virtuális gépében.
7. RdP-munkamenet használatával a Windows 2012 R2 gépre (a Veeam telepítésének céljára) telepítse a [Veeam B&R 9.5u3](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95) rendszert windows 2012 R2 virtuális gépbe.
8. Keresse meg a Veeam biztonsági másolat kiszolgálójának belső IP-címét, és állítsa be, hogy az IP-cím statikus legyen a DHCP-kiszolgálón. Az ehhez szükséges pontos lépések a DHCP-kiszolgálótól függenek. Például a Netgate cikk <a href="https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html" target="_blank">statikus DHCP-hozzárendelések</a> elmagyarázza, hogyan kell beállítani a DHCP-kiszolgáló t pfSense útválasztó használatával.

### <a name="veeam-console-install-veeam-backup-and-recovery-software"></a>Veeam konzol: Telepítse veeam biztonsági és helyreállítási szoftver

A Veeam konzol használatával konfigurálja a Veeam biztonsági és helyreállítási szoftvert. További információt a [Veeam backup & Replication v9 – Telepítés és telepítés című témakörben talál.](https://www.youtube.com/watch?v=b4BqC_WXARk)

1. VMware vSphere hozzáadása felügyelt kiszolgálói környezetként. Amikor a rendszer kéri, adja meg a [vCenter konzol magánfelhő: Veeam B telepítése&R](#vcenter-console-of-private-cloud-install-veeam-br).

    * A betöltés-vezérléshez és az alapértelmezett speciális beállításokhoz használja az alapértelmezett beállításokat.
    * Állítsa be a csatlakoztatási kiszolgáló helyét tartalék kiszolgálónak.
    * Módosítsa a Veeam-kiszolgáló konfigurációs biztonsági mentési helyét a távoli tárházra.

2. Adja hozzá a Linux-kiszolgálót az Azure-ban a biztonsági másolat tárházaként.

    * A betöltésvezérléshez és a speciális beállításokhoz használja az alapértelmezett beállításokat. 
    * Állítsa be a csatlakoztatási kiszolgáló helyét tartalék kiszolgálónak.
    * Módosítsa a Veeam-kiszolgáló konfigurációs biztonsági mentési helyét a távoli tárházra.

3. A konfigurációs biztonsági másolat titkosításának engedélyezése **a Home> Konfigurációs biztonsági mentés beállításai párbeszédpanelen.**

4. Windows server virtuális gép hozzáadása a VMware-környezet proxykiszolgálójaként. A "Traffic Rules" használatával titkosítsa a biztonsági mentési adatokat a hálózaton keresztül.

5. Biztonsági mentési feladatok konfigurálása.
    * A biztonsági mentési feladatok konfigurálásához kövesse a [Biztonságimásolat-készítő feladat létrehozása](https://www.youtube.com/watch?v=YHxcUFEss4M)című útmutató utasításait.
    * Engedélyezze a biztonsági másolat fájljainak titkosítását **a Speciális beállítások > a Storage területen.**

6. Biztonsági másolati feladatok konfigurálása.

    * A biztonságimásolat-másolási feladatok konfigurálásához kövesse a [Biztonságimásolat-másolási feladat létrehozása című](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)videó utasításait.
    * Engedélyezze a biztonsági másolat fájljainak titkosítását **a Speciális beállítások > a Storage területen.**

### <a name="cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges"></a>CloudSimple portál: Veeam-hozzáférés beállítása és a jogosultságok eszkalációjának mentesítése
Hozzon létre egy nyilvános IP-címet a Veeam biztonsági mentési és helyreállítási kiszolgálóhoz. További információt a [Nyilvános IP-címek lefoglalása](public-ips.md)című témakörben talál.

Hozzon létre egy tűzfalszabályt, amely lehetővé teszi, hogy a Veeam biztonsági mentési kiszolgáló kimenő kapcsolatot hozzon létre a Veeam webhelyhez a frissítések/javítások letöltéséhez a 80-as TCP-porton. További információt a [Tűzfaltáblák és -szabályok beállítása (Tűzfaltáblák és -szabályok) beállítása témakörben talál.](firewall.md)

A jogosultságok eszkalálódásának visszaeséséhez olvassa el a [Jogosultságok eltörlése](escalate-private-cloud-privileges.md#de-escalate-privileges).

## <a name="references"></a>Referencia

### <a name="cloudsimple-references"></a>CloudSimple hivatkozások

* [Magánfelhő létrehozása](create-private-cloud.md)
* [VLAN-ok/alhálózatok létrehozása és kezelése](create-vlan-subnet.md)
* [vCenter-identitásforrások](set-vcenter-identity.md)
* [Számítási feladatok DNS- és DHCP-telepítése](dns-dhcp-setup.md)
* [Jogosultságok eszkalációja](escalate-privileges.md)
* [Tűzfaltáblák és -szabályok beállítása](firewall.md)
* [Privát felhő engedélyek](learn-private-cloud-permissions.md)
* [Nyilvános IP-címek lefoglalása](public-ips.md)

### <a name="veeam-references"></a>Veeam referenciák

* [Használt portok](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)
* [Szükséges engedélyek](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95)
* [Rendszerkövetelmények](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)
* [A Veeam biztonsági mentés telepítése & replikációhoz](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95)
* [Szükséges modulok és engedélyek a Multi-OS FLR és repository támogatás Linux](https://www.veeam.com/kb2216)
* [Veeam Backup & replikáció v9 - Telepítés és telepítés - Videó](https://www.youtube.com/watch?v=b4BqC_WXARk)
* [Veeam v9 létrehozása Backup Job - Videó](https://www.youtube.com/watch?v=YHxcUFEss4M)
* [Veeam v9 létrehozása Backup Copy Job - Videó](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)

### <a name="azure-references"></a>Azure-hivatkozások

* [Virtuális hálózati átjáró konfigurálása az ExpressRoute számára az Azure Portal használatával](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
* [Virtuális hálózat csatlakoztatása áramkörhöz – más előfizetés](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)
* [Linuxos virtuális gép létrehozása az Azure Portalon](../virtual-machines/linux/quick-create-portal.md)
* [Felügyelt adatlemez csatolása Windows virtuális géphez az Azure Portalon](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Azure Storage – videó – első lépések](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)
* [Tároló létrehozása](https://docs.microsoft.com/rest/api/storageservices/create-container)
* [Adatok áthelyezése az AzCopyval Linux rendszeren](../storage/common/storage-use-azcopy-linux.md)

### <a name="vmware-references"></a>VMware referenciák

* [Elosztott portcsoport létrehozása a vSphere webügyfélben - videó](https://www.youtube.com/watch?v=wpCd5ZbPOpA)

### <a name="other-references"></a>Egyéb hivatkozások

* [XFS-kötet létrehozása a felügyelt lemezen - RedHat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/ch-xfs)
* [Hogyan állítsunk be egy NFS mount-ot a CentOS 7 rendszeren - HowToForge](https://www.howtoforge.com/nfs-server-and-client-on-centos-7)
* [A DHCP-kiszolgáló konfigurálása - Netgate](https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html)
