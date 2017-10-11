---
title: "Sikertelen biztonsági VMware virtuális gépek az Azure-ból a klasszikus portálon |} Microsoft Docs"
description: "További tudnivalók a VMware virtuális gépek feladatátvétele után is a helyszíni webhelyre megfelelően működjenek és fizikai kiszolgálók Azure-bA."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: mkjain
editor: 
ms.assetid: 7ca86e21-7a5d-45ab-8f4b-e42da90f199a
ms.service: site-recovery
ms.devlang: na
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: ruturajd
ms.openlocfilehash: 82d5eb7fd13b1e9700a3e9bc2d30775e9c129749
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="fail-back-vmware-virtual-machines-and-physical-servers-to-the-on-premises-site-classic-portal"></a>Sikertelen a hátsó VMware virtuális gépek és fizikai kiszolgálók a helyszíni hely (klasszikus portál)
> [!div class="op_single_selector"]
> * [Azure Portal](site-recovery-failback-azure-to-vmware.md)
> * [Klasszikus Azure portál](site-recovery-failback-azure-to-vmware-classic.md)
> * [A klasszikus Azure portálon (örökölt)](site-recovery-failback-azure-to-vmware-classic-legacy.md)
>
>

Ez a cikk ismerteti a helyszíni hely Azure-ból vissza az Azure virtuális gépek sikertelen. Kövesse az utasításokat ebben a cikkben, amikor készen áll a sikertelen biztonsági a VMware virtuális gépeket vagy windowsos/Linuxos fizikai kiszolgálók után azok feladatátvételt is a helyszíni hely Azure-ban ez [oktatóanyag](site-recovery-vmware-to-azure-classic.md).

## <a name="overview"></a>Áttekintés
Ez az ábra az ebben a forgatókönyvben a feladat-visszavétel architektúráját mutatja be.

Ez az architektúra használja, ha a folyamatkiszolgáló a helyszíni és az ExpressRoute használ.

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)

Használja az ebbe az architektúrába, ha a VPN-en vagy ExpressRoute kapcsolat van, és a folyamatkiszolgáló az Azure-on.

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)

Portok és a feladat-visszavétel teljes listájának megtekintéséhez architechture diagram tekintse meg az alábbi képen

![](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

Hogyan működik a feladat-visszavétel itt található:

* Miután az Azure-bA már feladatátvételt, akkor visszaadják feladataikat a helyszíni hely néhány fázisból áll:
  * **1. szakasz**:, lássa el újból védelemmel az Azure virtuális gépeket, hogy azokat vissza a VMware virtuális gépeket futtathatnak a helyszíni hely replikálást indítani. Ismételt védelem engedélyezése a virtuális Gépet áthelyezi egy feladat-visszavétel védelmi csoportot, amely automatikusan létrejött, a feladatátvételi védelmi csoport létrehozásakor. Ha hozzáadott a feladatátvevő védelmi csoport a helyreállítási tervbe, majd a feladat-visszavétel védelmi csoport is automatikusan lett adva a tervet.  Védelem-újrabeállítási során adja meg a feladat-visszavételt tervezéséről.
  * **2. szakaszra**: után Azure virtuális gépeken, a helyszíni helyre replikál, futtassa a sikertelen meghiúsul az Azure-ból.
  * **3. szakaszra**: követően az adatok visszaállítása sikertelen volt, állítsa a helyszíni virtuális gépek vissza a megbukott, hogy elindítja replikálása Azure-bA.


  [!VIDEO https://channel9.msdn.com/Blogs/Azure/Enhanced-VMware-to-Azure-Failback/player]

### <a name="failback-to-the-original-or-alternate-location"></a>Feladat-visszavétel az eredeti vagy egy másik helyre
Ha a feladatátvételt a virtuális gép ugyanazon forrására közben meghiúsulhatnak, ha még létezik a helyszíni VMware virtuális gép. Ebben a forgatókönyvben csak a változási különbözeteket fog megtörténni vissza. Vegye figyelembe:

* Ha átadja a fizikai kiszolgálók feladat-visszavétel akkor mindig egy új VMware virtuális géphez.
  * Vissza a fizikai gép sikertelen előtt vegye figyelembe, hogy
  * Védett fizikai gépek fog visszatérhet, amikor át nem adja vissza az Azure-ból a VMware virtuális gépként
  * Győződjön meg arról, hogy a legalább egy fő célkiszolgáló és a szükséges ESX/ESXi-gazdagépek szüksége a feladat-visszavételi Server felderíteni.
* Ha vissza az eredeti virtuális gép nem az alábbi feltételek teljesülése esetén:

  * Ha a virtuális gép vCenter-kiszolgáló kezeli a fő célkiszolgáló ESX-gazdagépet a virtuális gépek hozzáférése kell rendelkeznie.
  * Ha a virtuális gép ESX-gazdagépen, de vCenter nem felügyeli majd a merevlemezt a virtuális gép a fő Célkiszolgáló gazdagép által elérhető adattárolót a kell lennie.
  * Ha a virtuális Gépet az ESX-gazdagépen, és nem használja a vCenter el kell végeznie az ESX-gazdagép, a fő Célkiszolgáló felderítése ahhoz, hogy lássa el újból védelemmel. Ez igaz, ha a feladat-visszavétele vissza fizikai kiszolgálók túl.
  * Egy másik lehetőség (ha létezik a helyszíni virtuális gép) törli-e a feladat-visszavétel előtt. Majd feladat-visszavétel majd létrehoz egy új virtuális Gépet a fő célkiszolgáló ESX-gazdagép ugyanazon a gazdagépen.
* Ha egy másik helyre az adatokat feladatátvételi fogja visszaállítani a ugyanazon adattár és a helyszíni fő célkiszolgáló által használt azonos ESX-gazdagép.

## <a name="prerequisites"></a>Előfeltételek
* VMware környezetben lesz szüksége ahhoz, hogy a sikertelen biztonsági VMware virtuális gépek és fizikai kiszolgálók. Hibás fizikai kiszolgálóra nem támogatott.
* Ahhoz, hogy a feladat-visszavételt kell létrehozott Azure-hálózat védelmi kezdeti beállításakor. Feladat-visszavétel az Azure-hálózatot, amelyben az Azure virtuális gépek találhatók, a helyszíni hely közötti VPN- vagy ExpressRoute kapcsolatra van szüksége.
* Ha a virtuális gépeket szeretné visszavenni szüksége lesz, győződjön meg arról, hogy a vCenter-kiszolgáló által kezelt virtuális gépek a vCenter-kiszolgáló a felderítéshez szükséges jogosultságokkal rendelkezik. [További](site-recovery-vmware-to-azure-classic.md).
* Ha a pillanatképek a virtuális gép ismételt védelem meghiúsul. A pillanatképek vagy a lemezek törlése.
* Ahhoz, hogy a feladat-visszavételt meg kell létrehoznia egy összetevők száma:
  * **A folyamat-kiszolgáló létrehozása az Azure-ban**. Ez az egy Azure virtuális gép létrehozása és a feladat-visszavétel során adatközpontnak futnia kell. Törölheti a gép feladat-visszavétel befejezése után.
  * **Hozzon létre egy fő célkiszolgáló**: A fő célkiszolgáló adatokat küld és fogad feladat-visszavételre. A felügyeleti kiszolgáló, a helyszíni létrehozta a fő célkiszolgálón, alapértelmezés szerint telepítve van. Azonban sikertelen hátsó forgalom mennyiségét függően szükség lehet egy külön fő célkiszolgáló feladat-visszavételi létrehozásához.
  * Ha szeretne létrehozni egy további fő célkiszolgáló Linux rendszeren fut, akkor kell beállítani a Linux virtuális gépet a fő célkiszolgáló telepítése előtt az alább ismertetett.
* Konfigurációs kiszolgáló szükségesek helyszíni, ha így tesz, a feladat-visszavételre. A feladat-visszavétel során a virtuális gép léteznie kell a konfigurációs adatbázishoz, sikertelenek lesznek, mely feladat-visszavétel nem lesz sikeres. Ezért győződjön meg arról, hogy szánjon a kiszolgáló rendszeres ütemezett biztonsági mentést. Egy katasztrófa esetén akkor állítsa vissza az azonos IP-cím így fog működni a feladat-visszavétel.

## <a name="set-up-the-process-server-in-azure"></a>Állítsa be a folyamatkiszolgáló az Azure-ban
A folyamatkiszolgáló telepítése az Azure-ban, hogy az Azure virtuális gépeken is küldheti az adatokat a helyszíni fő célkiszolgáló kiszolgálóra van szükség.

1. A Site Recovery portálon > **konfigurációs kiszolgálók** lehetőséget, és adja hozzá az új folyamatkiszolgáló.

   ![](./media/site-recovery-failback-azure-to-vmware-classic/ps1.png)
2. Adja meg a folyamat-kiszolgáló nevét, és adjon meg egy nevet és jelszót fogja használni az Azure virtuális gép rendszergazdaként csatlakozni. A **konfigurációs kiszolgáló** válassza ki a helyszíni felügyeleti kiszolgálót, és adja meg az Azure-hálózatot, amelyben a folyamatkiszolgáló kell telepíteni. Ez legyen a hálózaton, amelyben az Azure virtuális gépek találhatók. Adjon meg egy egyedi IP-címet, válassza ki az alhálózatról, és telepítésének megkezdéséhez.

   ![](./media/site-recovery-failback-azure-to-vmware-classic/ps2.png)

   Akkor is kiváltódik egy feladatot, amely a folyamatkiszolgáló telepítése

   ![](./media/site-recovery-failback-azure-to-vmware-classic/ps3.png)

   A folyamatkiszolgáló telepítése az Azure-ban után bejelentkezhet a megadott hitelesítő adatok használatával. Az első alkalommal jelentkezik be a folyamat server párbeszédpanel fog futni. Adja meg az IP-címét a helyi felügyeleti kiszolgáló és a jelszót. Ne módosítsa az alapértelmezett 443-as portot. Is hagyhatja az alapértelmezett adatreplikáció 9443 port kivéve, ha a helyi felügyeleti kiszolgáló beállításakor kifejezetten módosítani ezt a beállítást.

   > [!NOTE]
   > A kiszolgáló nem fog megjelenni a **virtuális gép tulajdonságok**. Értéke csak alatt megjelenik a **kiszolgálók** lapján a felügyeleti kiszolgáló, amelyhez már regisztrálva van. Arról is igénybe vehet a folyamatkiszolgálót a jelennek meg a 10-15 perc.
   >
   >

## <a name="set-up-the-master-target-server-on-premises"></a>A fő célkiszolgáló helyszíni server beállítása
A fő célkiszolgáló kap a feladat-visszavétel adatokat. A fő célkiszolgáló automatikusan telepítve van a helyi felügyeleti kiszolgálón, de visszavétele esetén vissza nagy mennyiségű adat szükség lehet további fő célkiszolgáló beállítása. Ehhez az alábbiak szerint:

> [!NOTE]
> Ha szeretné a fő célkiszolgáló telepítése Linux, kövesse a következő eljárásban.
>
>

1. A Windows telepítése a fő célkiszolgáló, nyissa meg a gyors kezdés lapon a virtuális gép, amelyen telepíti a fő célkiszolgálón, és töltse le a fájlt az Azure Site Recovery az egységes telepítő varázsló.
2. Futtassa a telepítőt, és a **megkezdése előtt** válasszon **adjon hozzá további folyamat kiszolgálókat telepítésének bővítése**.
3. A varázsló a megszokott módon mikor meg [állítsa be a felügyeleti kiszolgáló](site-recovery-vmware-to-azure-classic.md). Az a **konfigurációs kiszolgáló adatait** adja meg azokat a fő célkiszolgáló és egy hozzáférési kódot elérni a virtuális gép IP-címét.

### <a name="set-up-a-linux-vm-as-the-master-target-server"></a>A fő célkiszolgáló beállított Linux virtuális gép
A felügyeleti kiszolgáló, a fő célkiszolgálón fut, telepítenie kell a centes Linux virtuális gép beállítása) S 6.6 minimális operációs rendszer, egyes SCSI merevlemez SCSI-azonosítóinak beolvasása, néhány további csomagok telepítése, és néhány egyéni módosítások alkalmazásához.

#### <a name="install-centos-66"></a>CentOS 6.6 telepítése
1. A CentOS 6.6 minimális méretű operációs rendszer telepítése a virtuális gép felügyeleti kiszolgálón. Egy DVD-meghajtóban lévő ISO tartani, és indítsa el a rendszer. Hagyja ki az adathordozó tesztelése, válassza ki a angol nyelvet, jelölje be a **alapvető tárolóeszközök**, ellenőrizze, hogy a merevlemez-meghajtó nem fontos adatokat, kattintson a **Igen**, minden az adatok elvetése. Adja meg a felügyeleti kiszolgáló állomásnevét, és válassza ki a kiszolgáló hálózati adaptert.  Az a **szerkesztése rendszer** párbeszédpanelen válasszon ** automatikus csatlakozás ** adja hozzá egy statikus IP-címet, a hálózati és a DNS-beállítások. Adjon meg egy időzóna, és a legfelső szintű jelszó megadásával érheti el a felügyeleti kiszolgáló.
2. Ha kéri a telepítés típusát azt szeretné, hogy válasszon **hozzon létre egyéni elrendezés** a partíció. Miután rákattintott **következő** válasszon **szabad** , és kattintson a Létrehozás gombra. Hozzon létre  **/** , **/var/összeomlási** és **/home partíciók** rendelkező **FS típusa:** **ext4**. A lapozófájl-kapacitás partion, hozzon létre **FS típusa: swap**.
3. Ha már meglévő eszközök találhatók, ekkor megjelenik egy figyelmeztető üzenet. Kattintson a **formátum** a meghajtót, amelyen a partícióbeállítások formázásához. Kattintson a **módosítás írása a lemezre** a partíció módosítások alkalmazásához.
4. Válassza ki **telepítés rendszertöltőt** > **tovább** a rendszertöltő telepítéséhez a legfelső szintű partíción.
5. Kattintson a telepítés befejeztével **újraindítás**.

#### <a name="retrieve-the-scsi-ids"></a>A SCSI-azonosítókat beolvasása
1. A telepítés után minden SCSI merevlemezt a virtuális gépen a SCSI-azonosítóinak beolvasása. Ez a felügyeleti kiszolgáló virtuális gép leállítása, ehhez a VMware virtuális gép tulajdonságainak kattintson a jobb gombbal a virtuális gép bejegyzés > **beállításainak szerkesztése** > **beállítások**.
2. Válassza ki **speciális** > **általános elem** kattintson **konfigurációs paraméterek**. Ez a beállítás de-active el, a gép futása közben. Az aktív legyen a számítógép le kell állítani.
3. Ha a sor **lemez. EnableUUID** létezik győződjön meg arról, hogy a beállítás értéke **igaz** (kis-és nagybetűket). Ha már van szakítsa meg, és tesztelje a SCSI parancsot egy vendég operációs rendszerében után.
4. Ha a sor nem létező kattintson **Hozzáadás sor** –, és adja hozzá azt a **igaz** érték. Ne használjon dupla idézőjelet.

#### <a name="install-additional-packages"></a>További csomagok telepítése
Szüksége lesz, letöltése és telepítése, néhány további csomagokat.

1. Győződjön meg arról, hogy a fő célkiszolgáló kapcsolódik az internethez.
2. A parancs futtatásával töltse le és telepítse a 15 csomagok a CentOS tárházból: **# yum – y xfsprogs perl lsscsi rsync wget kexec-eszközök telepítése**.
3. Ha védi a forrásgépek Linux p Reiser fut, vagy XFS fájlrendszer a gyökér- vagy rendszerindító eszköz, akkor le kell töltenie és további csomagok telepítése az alábbiak szerint:

   * # <a name="cd-usrlocal"></a>CD /usr/local
   * # <a name="wget-httpelrepoorglinuxelrepoel6x8664rpmskmod-reiserfs-00-1el6elrepox8664rpmhttpelrepoorglinuxelrepoel6x8664rpmskmod-reiserfs-00-1el6elrepox8664rpm"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)
   * # <a name="wget-httpelrepoorglinuxelrepoel6x8664rpmsreiserfs-utils-3621-1el6elrepox8664rpmhttpelrepoorglinuxelrepoel6x8664rpmsreiserfs-utils-3621-1el6elrepox8664rpm"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)
   * # <a name="rpm-ivh-kmod-reiserfs-00-1el6elrepox8664rpm-reiserfs-utils-3621-1el6elrepox8664rpm"></a>rpm – ivh kmod-reiserfs-0,0-1.el6.elrepo.x86_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm
   * # <a name="wget-httpmirrorcentosorgcentos66osx8664packagesxfsprogs-311-16el6x8665rpmhttpmirrorcentosorgcentos66osx8664packagesxfsprogs-311-16el6x8665rpm"></a>wget [http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)
   * # <a name="rpm-ivh-xfsprogs-311-16el6x8664rpm"></a>rpm – ivh xfsprogs-3.1.1-16.el6.x86_64.rpm

#### <a name="apply-custom-changes"></a>Egyéni módosítások alkalmazásához
Hajtsa végre a következő egyéni módosítások alkalmazásához beállította végezze el a telepítés utáni lépéseket és a csomagok telepítése:

1. A 6-64 RHEL Unified Agent ügynököt bináris másolja a virtuális Gépet. Futtassa ezt a parancsot a bináris untar: **bont – zxvf<file name>**
2. Futtassa ezt a parancsot, amelyhez engedélyeket: **# chmod 755./ApplyCustomChanges.sh**
3. Futtassa a parancsfájlt: **#./ApplyCustomChanges.sh**. A parancsfájl egyszer csak futtasson. Miután a parancsfájl sikeresen fut, indítsa újra a kiszolgálót.

## <a name="run-the-failback"></a>A feladat-visszavétel futtatása
### <a name="reprotect-the-azure-vms"></a>Lássa el újból védelemmel az Azure virtuális gépek
1. A Site Recovery portálon > **gépek** lapját, válassza ki a virtuális Gépet, amely a feladatátvétel megtörtént, és kattintson a **védelmének újbóli beállításához**.
2. A **fő célkiszolgáló** és **Folyamatkiszolgáló** válassza ki a helyszíni fő célkiszolgáló, és az Azure virtuális gép folyamatkiszolgáló.
3. Válassza ki a beállított fiók megjelenik a virtuális Géphez való kapcsolódáshoz.
4. Válassza ki a védelmi csoport feladat-visszavétel verzióját. A példa, ha a virtuális gép védett, akkor PG1 kell választania a PG1_Failback.
5. Ha azt szeretné, egy másik helyre történő helyreállítást, válassza az adatmegőrzési meghajtó és a fő célkiszolgáló konfigurált adattároló. Ha Ön visszaadják feladataikat a helyszíni hely a VMware virtuális gépeket a feladat-visszavétel védelmi terv fog használni a ugyanazon adattár a fő célkiszolgálón. Ha végre kívánja hajtani a replika Azure virtuális gép ugyanahhoz a helyszíni virtuális gép és a helyszíni virtuális gép már kell lennie, a fő célkiszolgáló ugyanazon adattár. Ha nincs virtuális gép helyszíni ismételt védelem alatt jön létre egy újat.

   ![](./media/site-recovery-failback-azure-to-vmware-classic/failback1.png)
6. Miután rákattintott **OK** ismételt védelemmel ellátni azt egy feladat elkezdi replikálni a virtuális Gépet az Azure-ból a helyszíni hely megkezdéséhez. A előrehaladásának a a **feladatok** fülre.

   ![](./media/site-recovery-failback-azure-to-vmware-classic/failback2.png)

### <a name="run-a-failover-to-the-on-premises-site"></a>Feladatátvételt végez a helyszíni hely
Ismételt védelemmel ellátni azt követően a virtuális gép áthelyezése a feladat-visszavétel verzióra, a védelmi csoport, és automatikusan hozzáadódik a helyreállítási tervet, használja a az Azure, ha van ilyen.

1. Az a **helyreállítási tervek** lapon jelölje be a a feladat-visszavétel csoportot tartalmazó helyreállítási tervet, és kattintson a **feladatátvételi** > **nem tervezett feladatátvétel**.
2. A **megerősítéséhez feladatátvétel** ellenőrzése a feladatátvevő irányát (az Azure-bA), és válassza ki a a (legújabb) a feladatátvételre használni kívánt helyreállítási pontot. Ha engedélyezte a **virtuális Gépre kiterjedő** helyreállíthatja a legutóbbi alkalmazás vagy összeomlás-konzisztens helyreállítási pontot replikációs tulajdonságok konfigurálása során. Kattintson a pipa jelre elindítani a feladatátvételt.
3. A feladatátvétel során a Site Recovery az Azure virtuális gépeken le fog állni. Ellenőrzése meg a várt módon, hogy feladat-visszavétel befejezése után is ellenőrizheti, hogy az Azure virtuális gépek be lett zárva várt módon.

### <a name="reprotect-the--on-premises-site"></a>Állítsa a helyszíni hely
Feladat-visszavétel befejezése után az adatok vissza a helyszíni helyen, de nem lehet védetté tenni. Az Azure-bA replikálást indítani újra tegye a következőket:

1. A Site Recovery portálon > **gépek** lapon válassza ki a virtuális gépek, amelyek nem tudták biztonsági, és kattintson a **védelmének újbóli beállításához**.
2. Miután ellenőrizte, hogy a replikáció Azure a várt módon működik, az Azure törölheti az Azure virtuális gépeken (jelenleg nem fut) visszaállítása sikertelen volt.

### <a name="common-issues-in-failback"></a>A feladat-visszavétel kapcsolatos gyakori hibák
1. Csak olvasási jogosultsággal rendelkező felhasználói vCenter-kiszolgálók automatikus észlelését, és a virtuális gépek védelmére, ha ez sikeres, és feladatátvételi működik. A védelem-Újrabeállítási időpontjában meghiúsul, mert a datastores nem lesz felderítve. Egyik oka az, nem látják a datastores felsorolt közben ismételt védelmével. A probléma megoldásához, frissítse a vCenter hitelesítőadat megfelelő engedélyekkel rendelkező fiókot, és próbálja újból elvégezni a feladatot. [További információ](site-recovery-vmware-to-azure-classic.md)
2. Ha a Linux virtuális gép feladatátvételi, és futtassa a helyszínen, látni fogja, hogy a hálózatkezelő csomag eltávolítása a számítógépről. Ennek az az oka helyreállításakor a virtuális Gépet az Azure-ban, a hálózatkezelő csomag törlődik.
3. Ha egy virtuális gép statikus IP-címmel van konfigurálva, és át nem adja a Azure, az IP-cím szerezte be a DHCP. Amikor a rendszer átadja a Vissza gombra, és a helyszínen, a virtuális gép továbbra is az IP-cím beszerzése a DHCP Protokollt használják. A számítógép és az IP-cím statikus cím biztonsági másolatot, szükség esetén állítsa be kell manuálisan a bejelentkezéshez.
4. Vagy ESXi 5.5 ingyenes kiadásban, vagy vSphere 6 hipervizor ingyenes kiadás használatakor volna sikertelen volt, de a feladat-visszavétel nem fog sikerülni. Ned vagy próbalicencre ahhoz, hogy feladat-visszavétel frissíteni fogja.

## <a name="failing-back-with-expressroute"></a>Az ExpressRoute meghibásodott vissza
Átveheti vissza egy VPN-kapcsolat vagy Azure expressroute-ot. Ha szeretné használni az ExpressRoute vegye figyelembe a következőket:

* ExpressRoute mely forrás gépek sikertelen keresztül, és az Azure virtuális gépek mely találhatók a feladatátvételt követően Azure virtuális hálózaton kell beállítani.
* Egy Azure storage-fiók egy nyilvános végpontot az adatok replikálódnak. Állítson be nyilvános társviszony-létesítés az ExpressRoute a Site Recovery replikáció ExpressRoute használja a cél adatközpont.
