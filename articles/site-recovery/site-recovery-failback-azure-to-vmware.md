---
title: "Feladat-visszavétel VMware virtuális gépek az Azure-ból a helyszíni |} Microsoft Docs"
description: "További tudnivalók a VMware virtuális gépek feladatátvétele után is a helyszíni webhelyre megfelelően működjenek és fizikai kiszolgálók Azure-bA."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: mkjain
editor: 
ms.assetid: 5a47337f-89a9-43e8-8fdc-7da373c0fb0f
ms.service: site-recovery
ms.devlang: na
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 03/27/2017
ms.author: ruturajd
ms.openlocfilehash: dde0bb6b4f6bc10afdd7d40adc6689d42b37de81
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="fail-back-vmware-virtual-machines-and-physical-servers-to-the-on-premises-site"></a>Sikertelen a hátsó VMware virtuális gépek és fizikai kiszolgálók a helyszíni hely


Ez a cikk ismerteti, hogyan feladat-visszavételi Azure virtuális gépek az Azure-ból a helyszíni helyre. Kövesse a megjelenő utasításokat itt készen feladat-visszavételt a VMware virtuális gépeket vagy windowsos vagy Linuxos fizikai kiszolgálók követően újra védelemmel ellátott ez használata a gépek [hivatkozás](site-recovery-how-to-reprotect.md).

>[!NOTE]
>Ha a klasszikus Azure portált használ, olvassa el említett utasításokat [Itt](site-recovery-failback-azure-to-vmware-classic.md) az Azure-architektúra továbbfejlesztett VMware és [Itt](site-recovery-failback-azure-to-vmware-classic-legacy.md) örökölt architektúrájának

## <a name="overview"></a>Áttekintés
Ez a szakasz a diagramokon a feladat-visszavétel architektúra ehhez a forgatókönyvhöz.

Ha a Folyamatkiszolgáló a helyszíni és az Azure ExpressRoute-kapcsolatot használ, ez architektúrák használatára:

![ExpressRoute architektúra diagramja](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)

Ha a Folyamatkiszolgáló az Azure-on, és egy VPN vagy ExpressRoute kapcsolat van, használja az ebbe az architektúrába:

![VPN-architektúra ábrája](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)

Portok és a feladat-visszavétel architektúra diagramja teljes listáját lásd az alábbi képen:

![Feladatátvétel-feladat-visszavétel összes portok listája](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

Miután az Azure-bA már feladatátvételt, akkor visszaadják feladataikat a helyszíni hely három fázisból áll:

* **1. szakasz**:, lássa el újból védelemmel az Azure virtuális gépeket, hogy azokat a VMware virtuális gépek, a helyszíni hely futó vissza a replikálást indítani.
* **2. szakaszra**: után az Azure virtuális gépek replikálva vannak a helyszíni hely, a feladatátvétel sikertelen lesz az Azure-ból futtassa.
* **3. szakaszra**: követően az adatok visszaállítása sikertelen volt, állítsa a helyszíni virtuális gépek vissza a megbukott, hogy elindítja replikálása Azure-bA.

### <a name="fail-back-to-the-original-location-or-an-alternate-location"></a>Visszaadják feladataikat az eredeti helyre vagy másik helyre
Után a rendszer átadja a VMware virtuális gép, akkor sikertelen lehet VM ugyanazon forrására Ha még létezik a helyszíni. Ebben a forgatókönyvben csak az eltérések sikertelen vissza.

Fizikai kiszolgálók feladatátvételt, ha a feladat-visszavétel új VMware virtuális gép mindig is. Mielőtt sikertelen vissza a fizikai gépen, vegye figyelembe, hogy:
* A védett fizikai gépek amikor azt át nem adja vissza az Azure-ból a VMware virtuális gépként vissza származnak. Windows Server 2008 R2 SP1 fizikai gépen, ha a védett, és az Azure-ba, a feladatátvételt nem sikerült vissza. A Windows Server 2008 R2 SP1 gépek, fut-e, a helyszíni virtuális gép fog tudni a feladat-visszavételt.
* Győződjön meg arról, hogy észlelni-e a szükséges ESX/ESXi-gazdagépek szükséges visszaadják feladataikat együtt legalább egy fő célkiszolgálót.

Ha nem vissza az eredeti virtuális gép, a következőkre szükség:

* Ha a virtuális gép vCenter-kiszolgáló felügyeli, a fő célkiszolgáló ESX-gazdagépet a virtuális gépek hozzáférése kell rendelkeznie.
* Ha a virtuális Gépet az ESX-gazdagépen, de nem felügyeli a vCenter, a merevlemezt a virtuális gép kell a fő Célkiszolgáló gazdagép által elérhető adattárolót.
* Ha a virtuális Gépet az ESX-gazdagépen, és nem használja a vCenter, el kell végeznie az ESX-gazdagép, a fő Célkiszolgáló felderítése ahhoz, hogy lássa el újból védelemmel. Ez igaz, ha a feladat-visszavétele vissza fizikai kiszolgálók túl.
* Egy másik lehetőség (ha létezik a helyszíni virtuális gép) törli-e a feladat-visszavétel előtt. Feladat-visszavétel majd létrehoz egy új virtuális Gépet a fő célkiszolgáló ESX-gazdagép ugyanazon a gazdagépen.

Ha nem vissza egy másik helyre, az adatok helyreállítása ugyanazon adattár és a helyszíni fő célkiszolgáló által használt azonos ESX-gazdagépet.

## <a name="prerequisites"></a>Előfeltételek
* Sikertelen biztonsági VMware virtuális gépek és fizikai kiszolgálók VMware környezetben van szüksége. Hibás fizikai kiszolgálóra nem támogatott.
* A feladat-visszavételt, kell létrehozott Azure-hálózat védelmi kezdeti beállításakor. Feladat-visszavétel az Azure-hálózatot, amelyben az Azure virtuális gépek találhatók, a helyszíni hely közötti VPN- vagy ExpressRoute kapcsolatra van szüksége.
* Ha a virtuális gép között szeretné visszavenni a vCenter-kiszolgáló által kezelt ellenőrizze, hogy rendelkezik a szükséges engedélyekkel a vCenter-kiszolgáló a virtuális gépek felderítése. További információkért lásd: [replikálása VMware virtuális gépek és fizikai kiszolgálók Azure-bA az Azure Site Recovery](site-recovery-vmware-to-azure-classic.md).
* Ha a pillanatképek a virtuális gép jelen, ismételt védelem sikertelen lesz. A pillanatképek vagy a lemezek törlése.
* Ahhoz, hogy feladat-visszavételt, hozza létre ezeket az összetevőket:
  * **A folyamat-kiszolgáló létrehozása az Azure-ban**. Ez az összetevő egy Azure virtuális gép létrehozása és feladat-visszavétel során tovább futnak. Törölheti a virtuális gép feladat-visszavétel befejezése után.
  * **Hozzon létre egy fő célkiszolgáló**: A fő célkiszolgáló adatokat küld és fogad feladat-visszavételre. A felügyeleti kiszolgáló, a helyszíni létrehozott rendelkezzen a fő célkiszolgálón, amely alapértelmezés szerint telepítve van. Azonban nem sikerült visszaírt forgalom mennyiségét, attól függően szükség lehet egy külön fő célkiszolgáló feladat-visszavételi létrehozásához.
  * Hozzon létre további fő célkiszolgálón, amely Linux rendszeren fut, állítsa be a Linux virtuális Gépet a fő célkiszolgáló telepítése előtt, a későbbiekben olvashat.
* A konfigurációs kiszolgálón szükséges a helyszíni, ha így tesz, a feladat-visszavétel. A feladat-visszavétel során a virtuális gép léteznie kell a konfigurációs kiszolgáló adatbázisában. Ha a konfigurációs adatbázishoz nincs a virtuális Gépet tartalmaz, a feladat-visszavétel nem lehet sikeres. Ezért győződjön meg arról, hogy elvégezte-e a kiszolgáló rendszeres ütemezett biztonsági mentések. Egy olyan vészhelyzet esetén szükség állítsa vissza az azonos IP-címmel, hogy a feladat-visszavétel működik.
* Beállításánál a disk.enableUUID=true **konfigurációs paraméterek** a fő cél a virtuális gép VMware-ben. Ha a sor nem létezik, adja hozzá. Ez a beállítás szükséges, hogy megfelelően csatlakoztatva van, adjon meg egy egységes univerzálisan egyedi azonosító (UUID) a virtuálisgép-lemez (VMDK) fájlt.
* Vegye figyelembe a "fő célkiszolgáló tárolási vMotioned nem lehet" feltétel, ami a sikertelen feladat-visszavétel okozhat. A virtuális gép nem kapja meg, mert a lemez nem rendelkezésére álló.
* Adjon hozzá egy meghajtót, egy adatmegőrzési meghajtó, a fő célkiszolgáló alakzatot nevezik. Adjon hozzá egy lemezt, és formázza a meghajtót.

## <a name="failback-policy"></a>Feladat-visszavételi házirend
Egy feladatátvételi házirendhez kell replikálni a helyszíni. A házirend automatikusan létrejön, amikor előre házirendet hoz létre, amely automatikusan hozzárendeli a konfigurációs kiszolgáló. A rendszer nem szerkeszthető. A házirendben engedélyezve van a következő replikációs beállítások:

* Helyreállítási Időkorlát küszöbértéke = 15 perc
* Helyreállítási pontok megőrzésének ideje 24 óra =
* Alkalmazáskonzisztens pillanatkép gyakorisága = 60 perc

 ![A feladat-visszavétel házirend replikációs beállítások](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

## <a name="set-up-a-process-server-in-azure"></a>Állítsa be a Folyamatkiszolgáló az Azure-ban
A Folyamatkiszolgáló telepítése az Azure-ban, hogy az Azure virtuális gépeken is küldheti az adatokat a helyszíni fő célkiszolgáló kiszolgálóra.

Ha szeretne védetté tenni a virtuális gépeket, mint a hagyományos erőforrások (Ez azt jelenti, a virtuális Gépet az Azure-ban helyre egy virtuális Gépet, amely a klasszikus telepítési modellel készült), a Folyamatkiszolgáló az Azure-ban van szüksége. Ha helyreállította a virtuális gépeket az Azure Resource Manager központi telepítési típust, a Folyamatkiszolgáló erőforrás-kezelő kell rendelkeznie a központi telepítési típusként. A központi telepítési típus szerint a Folyamatkiszolgálót a központilag telepített Azure virtuális hálózat van kiválasztva.

1. A **tároló** > **beállítások** > **Site Recovery-infrastruktúra** (alatt **kezelése**) > **Konfigurációs kiszolgálók** (alatt **a VMware és fizikai gépek**), válassza ki a konfigurációs kiszolgálót.
2. Kattintson a **Server feldolgozni**.

  ![Folyamat Server gomb](./media/site-recovery-failback-azure-to-vmware-classic/add-processserver.png)
3. Válassza ki a Folyamatkiszolgálót, mint a telepítendő **központi telepítése egy feladat-visszavételi Folyamatkiszolgáló az Azure-ban**.
4. Válassza ki az előfizetést, amely a virtuális gépeket, a helyreállított.
5. Válassza ki a virtuális gép helyreállított Azure-hálózatot. A Folyamatkiszolgáló kell lennie ugyanazon a hálózaton, hogy kommunikálhassanak a helyreállított virtuális gépek és a Folyamatkiszolgáló.
6. Ha kiválasztott egy *klasszikus üzembe helyezési modellel* hálózati, hozzon létre egy virtuális Gépet az Azure piactéren keresztül, és telepítse azt a Folyamatkiszolgáló.

 ![Az "A folyamat kiszolgáló hozzáadása" ablak](./media/site-recovery-failback-azure-to-vmware-classic/add-classic.png)

 A Folyamatkiszolgáló hoz létre, mert figyelmet fordítani az alábbiak:
 * A lemezkép neve *Microsoft Azure Site helyreállítási folyamat kiszolgáló V2*. Válassza ki **klasszikus** lehetőséget üzemi modellként.

       ![Select "Classic" as the Process Server deployment model](./media/site-recovery-failback-azure-to-vmware-classic/templatename.png)
 * A utasításainak megfelelően a Folyamatkiszolgáló telepítése [replikálása VMware virtuális gépek és fizikai kiszolgálók Azure-bA az Azure Site Recovery](site-recovery-vmware-to-azure-classic.md).
7. Ha bejelöli a *erőforrás-kezelő* Azure hálózati, a Folyamatkiszolgáló telepítése az alábbi információk megadásával:

  * Az erőforráscsoport, amelyet szeretne telepíteni szeretné a kiszolgálót neve
  * A kiszolgáló nevét
  * Felhasználónév és jelszó a bejelentkezés a kiszolgálóra
  * A storage-fiók, amelyet szeretne telepíteni szeretné a kiszolgálót
  * Az alhálózat és a csatlakozáshoz használni kívánt hálózati illesztőt
   >[!NOTE]
   >Létre kell hoznia a saját [hálózati illesztő](../virtual-network/virtual-networks-multiple-nics.md) (NIC), és válassza ki azt a Folyamatkiszolgáló központi telepítésekor.

    ![Adja meg az adatokat a "Folyamat kiszolgáló hozzáadása" párbeszédpanel](./media/site-recovery-failback-azure-to-vmware-classic/psinputsadd.png)

8. Kattintson az **OK** gombra. Ez a művelet elindítja egy feladatot, amely létrehoz egy erőforrás-kezelő központi telepítési típus virtuális gépet a Folyamatkiszolgáló telepítése során. A konfigurációs kiszolgálón a kiszolgáló regisztrálásához futtassa a telepítőt, a virtuális Gépen belül található útmutatást követve [replikálása VMware virtuális gépek és fizikai kiszolgálók Azure-bA az Azure Site Recovery](site-recovery-vmware-to-azure-classic.md). Egy feladatot, amely a Folyamatkiszolgáló telepítése is elindul.

  A Folyamatkiszolgáló szerepel a **konfigurációs kiszolgálók** > **tartozó kiszolgálók** > **folyamat kiszolgálók** fülre.

    ![](./media/site-recovery-failback-azure-to-vmware-new/pslistingincs.png)

    > [!NOTE]
    > A Folyamatkiszolgáló nem látható a **virtuális gép tulajdonságok**. Csak a látható a **kiszolgálók** lapján a felügyeleti kiszolgálót, hogy regisztrálva van. A folyamat kiszolgáló megjelenését 10 – 15 percet is igénybe vehet.


## <a name="set-up-the-master-target-server-on-premises"></a>A fő célkiszolgáló helyszíni server beállítása
A fő célkiszolgáló kap a feladat-visszavétel adatokat. A kiszolgáló automatikusan települ a helyi felügyeleti kiszolgálón, de visszavétele esetén vissza túl sok adatot, szükség lehet további fő célkiszolgáló beállítása. Állítsa be a fő célkiszolgáló helyi, tegye a következőket:

> [!NOTE]
> Linux fő célkiszolgáló beállítása, ugorjon a következő eljárást. Csak CentOS 6.6 minimális méretű operációs rendszer használata a fő célkiszolgáló az operációs rendszer.

1. Hoz létre a fő célkiszolgáló Windows rendszeren, ha a gyors üzembe helyezési oldal megnyitása a fő célkiszolgáló telepítése a virtuális gép.
2. Töltse le a fájlt az Azure Site Recovery az egységes telepítő varázsló.
3. Futtassa a telepítőt és a **megkezdése előtt**, jelölje be **adjon hozzá további folyamat kiszolgálókat telepítésének bővítése**.
4. A varázsló a megszokott módon mikor meg [állítsa be a felügyeleti kiszolgáló](site-recovery-vmware-to-azure-classic.md). Az a **konfigurációs kiszolgáló adatait** lapon adja meg a fő célkiszolgáló IP-címét, és adja meg a virtuális gép eléréséhez.

### <a name="set-up-a-linux-vm-as-the-master-target-server"></a>A fő célkiszolgáló beállított Linux virtuális gép
A felügyeleti kiszolgálón fut a fő célkiszolgáló Linux virtuális gép beállításához telepítse a CentOS 6.6 minimális méretű operációs rendszer. Ezután minden SCSI merevlemez SCSI-azonosítóinak beolvasása, néhány további csomagok telepítése, és néhány egyéni módosítások alkalmazásához.

#### <a name="install-centos-66"></a>CentOS 6.6 telepítése

1. A CentOS 6.6 minimális méretű operációs rendszer telepítése a virtuális gép felügyeleti kiszolgálón. Egy DVD-meghajtó ISO tartani, és indítsa el a rendszer. Hagyja ki az adathordozó tesztelése. Válassza ki **angol** a nyelv kiválasztása **alapvető tárolóeszközök**, ellenőrizze, hogy a merevlemez-meghajtó nem rendelkezik a fontos adatokat, kattintson a **Igen**, és elveti az adatok. Adja meg a felügyeleti kiszolgáló állomásnevét, majd válassza ki a kiszolgáló hálózati adaptert.  Az a **szerkesztése rendszer** párbeszédpanelen jelölje ki **automatikus csatlakozás**, majd adja hozzá egy statikus IP-címet, a hálózati és a DNS-beállítások. Adjon meg egy időzónát. A felügyeleti kiszolgáló eléréséhez adja meg a gyökér szintű jelszavát.
2. Amikor a rendszer megkérdezi, hogy milyen típusú telepítést szeretne, válassza ki a **hozzon létre egyéni elrendezés** a partíció. Kattintson a **Tovább** gombra. Válassza ki **szabad**, és kattintson a **létrehozása**. Hozzon létre  **/** , **/var/összeomlási**, és **/home partíciók** rendelkező **FS típusa:** **ext4**. Hozzon létre a lapozófájl-kapacitás partíciót **FS típusa: swap**.
3. Ha már meglévő eszközökön található, megjelenik egy figyelmeztető üzenet. Kattintson a **formátum** a meghajtót, amelyen a partícióbeállítások formázásához. Kattintson a **módosítás írása a lemezre** a partíció módosítások alkalmazásához.
4. Válassza ki **telepítés rendszertöltőt** > **tovább** a rendszertöltő telepítéséhez a legfelső szintű partíción.
5. Ha a telepítés befejeződött, kattintson a **újraindítás**.

#### <a name="retrieve-the-scsi-ids"></a>A SCSI-azonosítókat beolvasása

1. A telepítés után minden SCSI merevlemezt a virtuális gépen a SCSI-azonosítóinak beolvasása. Ehhez állítsa le a felügyeleti kiszolgáló virtuális gép. A VMware virtuális gép tulajdonságai, kattintson a jobb gombbal a virtuális gép bejegyzés > **beállításainak szerkesztése** > **beállítások**.
2. Válassza ki **speciális** > **általános elem**, és kattintson a **konfigurációs paraméterek**. Ez a beállítás nem érhető el, a gép futása közben. A lehetőség csak akkor használható a gép le kell állítani.
3. A következő lehetőségek közül választhat:
 * Ha a sor **lemez. EnableUUID** létezik, győződjön meg arról, hogy a értéke **igaz** (kis-és nagybetűket). Ha már értéke TRUE, szakítsa meg, és az SCSI parancsot egy vendég operációs rendszerében tesztelése után.
 * Ha a sor **lemez. EnableUUID** nem létezik, kattintson a **Hozzáadás sor**, majd adja hozzá azt a **igaz** érték. Ne használjon dupla idézőjelek közé.

#### <a name="install-additional-packages"></a>További csomagok telepítése
Töltse le, és további csomagok telepítése.

1. Győződjön meg arról, hogy a fő célkiszolgáló kapcsolódik az internethez.
2. Töltse le, és 15 csomagok telepítése a CentOS tárházból, futtassa ezt a parancsot: `# yum install –y xfsprogs perl lsscsi rsync wget kexec-tools`.
3. Ha a forrás gépeket lát el védelemmel egy Reiser Linux fut, vagy XFS a gyökér- vagy rendszerindító eszköz fájlrendszer, töltse le és további csomagok telepítése az alábbiak szerint:

   * \#CD /usr/local
   * \#wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)
   * \#wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)
   * \#rpm – ivh kmod-reiserfs-0,0-1.el6.elrepo.x86_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm
   * \#wget [http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)
   * \#rpm – ivh xfsprogs-3.1.1-16.el6.x86_64.rpm
   * \#yum eszköz-leképező-többutas (szükséges ahhoz, hogy a fő célkiszolgáló többutas csomagok) telepítése

#### <a name="apply-custom-changes"></a>Egyéni módosítások alkalmazásához
Miután a telepítés utáni lépések végrehajtása és a csomagok telepítése, egyéni módosítások alkalmazásához az alábbi lépésekkel:

1. A 6-64 RHEL Unified Agent ügynököt bináris másolja a virtuális Gépet. A bináris untar, futtassa a parancsot: `tar –zxvf <file name>`.
2. Engedélyek biztosítják, hogy a parancs futtatásához: `# chmod 755 ./ApplyCustomChanges.sh`.
3. Futtassa a következő parancsfájlt: `# ./ApplyCustomChanges.sh`. Csak egyszer futtatnia. Miután sikeresen lefutott, indítsa újra a kiszolgálót.

## <a name="run-the-failback"></a>A feladat-visszavétel futtatása
### <a name="reprotect-the-azure-vms"></a>Lássa el újból védelemmel az Azure virtuális gépek
1. A **tároló**, a **replikált elemek**, kattintson a jobb gombbal a virtuális Gépet, amely rendelkezik a rendszer feladatátvételt, és válassza **védelmének újbóli beállításához**.
2. A panelen láthatja, hogy védelmi irányának **Azure a helyszíni** már be van jelölve.
3. A **fő célkiszolgáló** és **Folyamatkiszolgáló**, válassza ki a helyszíni fő célkiszolgáló és az Azure virtuális gép Folyamatkiszolgáló.
4. Válassza ki a lemezek helyi a helyreállítani kívánt adattárolót. Használja ezt a beállítást, ha a helyszíni virtuális gép törlődik, és új lemezek létrehozásához szükséges. Figyelmen kívül hagyja a beállítást, ha a lemez már létezik, de továbbra is meg kell adnia egy értéket.
5. Egy adatmegőrzési meghajtó használatával állítsa le a pontok időpont, amikor a virtuális gép replikálódik vissza a helyszíni. Az itt felsorolt adatmegőrzési meghajtó bizonyos feltételeknek. Ezek a feltételek nélkül a meghajtó nem szerepel a fő célkiszolgálón.

  * Kötet használja (cél replikációs, és így tovább) más célra nem lehet.
  * A kötet nem lehet zárolási üzemmódban.
  * A kötet nem lehet gyorsítótárkötet. (A fő célkiszolgáló telepítése nem létezik a köteten található. A Folyamatkiszolgáló és fő cél egyéni telepítési lemez nem jogosult az adatmegőrzési kötet. Itt, a telepített Folyamatkiszolgáló és fő cél kötet van, a gyorsítótár kötetén a fő célkiszolgáló.)
  * A kötet fájlrendszerének nem lehet FAT és FAT32.
  * A kötet kapacitása nem lehet nulla.
  * Az alapértelmezett adatmegőrzési kötet Windows R köteten.
  * Az alapértelmezett adatmegőrzési kötet Linux /mnt/retention.

6. A feladat-visszavétel házirend automatikusan ki van jelölve.
7. Miután rákattintott **OK** ismételt védelem megkezdéséhez egy feladat elkezdi replikálni a virtuális Gépet az Azure-ból a helyszíni hely. A előrehaladásának a a **feladatok** fülre.

Ha azt szeretné, egy másik helyre történő helyreállítást, válassza az adatmegőrzési meghajtó és a fő célkiszolgáló konfigurált adattároló. Nem vissza a helyszíni hely, a VMware virtuális gépeket a feladat-visszavétel védelmi terv a fő célkiszolgáló ugyanazon adattár használják. Ha azt szeretné, a replika Azure virtuális gép a ugyanazt a helyszíni virtuális gép helyreállításához, a helyszíni virtuális gép már kell lennie, a fő célkiszolgáló ugyanazon adattár. Ha nincs virtuális gép a helyszíni, egy új ismételt védelem alatt jön létre.

![Válassza ki az "Azure a helyszíni" a legördülő menü](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

A helyreállítási terv szinten is állítsa. Ha a replikációs csoport, csak a helyreállítási terv használatával is állítsa. Akkor állítsa a helyreállítási tervet, használja az előző értéket minden védett gép.

> [!NOTE]
> Replikációs csoportok vissza magát a azonos fő célkiszolgáló kell védeni. A védett másik fő célkiszolgáló kiszolgálókkal vissza, ha egy közös időpontig visszamenőleg nem lehet megállapítani, a számukra.

### <a name="run-a-failover-to-the-on-premises-site"></a>Feladatátvételt végez a helyszíni hely
Állítsa a virtuális gép, miután a feladatátvételt az Azure-ból a helyszíni is kezdeményezhető.

1. A replikált elemek lapon kattintson a jobb gombbal a virtuális gépet, majd válassza ki **nem tervezett feladatátvétel**.
2. A **megerősítéséhez feladatátvétel**, ellenőrizze a feladatátvételi irányát (az Azure-ból), majd válassza ki a feladatátvétel (a legújabb, vagy a legújabb alkalmazáskonzisztens helyreállítási pont) használni kívánt helyreállítási pontok. Időben a legutóbbi pont előtt történik az alkalmazáskonzisztens helyreállítási pontot, és az adatvesztést okoz.
3. A feladatátvétel során a Site Recovery az Azure virtuális gépek leáll. Után ellenőrizheti, hogy feladat-visszavétel befejezése után várt módon, győződjön meg arról, hogy az Azure virtuális gépek be lett zárva várt módon ellenőrizheti.

### <a name="reprotect-the-on-premises-site"></a>Állítsa a helyszíni hely
Feladat-visszavétel befejezése után, a rendszer törli véglegesítse a virtuális gép biztosítja, hogy a virtuális gépeket az Azure-ban. Ehhez kattintson a jobb gombbal a védett elem, és kattintson **véglegesítése**. Ez a művelet egy feladatot, amely eltávolítja a korábbi helyreállított virtuális gépek Azure-ban váltja ki.

A rendszer a véglegesítés után az adatokat a helyszíni hely újra be kell lennie, de azt nem lehet védetté tenni. Indítsa el újra az Azure-bA replikál, tegye a következőket:

1. A **tároló**, a **beállítás** > **replikált elemek**, válassza ki a virtuális gépek biztonsági sikertelen, és kattintson a **védelmének újbóli beállításához**.
2. Az érték a folyamatkiszolgáló, amelyet a adatokat küldhet vissza az Azure ad.
3. Kattintson az **OK** gombra.

A feladatátvételen befejezése után a virtuális gép vissza az Azure-bA replikál, és elvégezhető a feladatátvétel.

### <a name="resolve-common-failback-issues"></a>Feladat-visszavétel kapcsolatos gyakori problémák megoldása
* Ha egy csak olvasható felhasználói vCenter-kiszolgálók automatikus észlelését, és a virtuális gépek védelmére, ez sikeres, és feladatátvételi működik. Ismételt védelem, során feladatátvétel sikertelen lesz, mivel a datastores nem lesz felderítve. A jelenség, mint nem látják az ismételt védelem alatt felsorolt datastores. A probléma megoldásához frissítse a vCenter hitelesítőadat egy megfelelő jogosultsággal rendelkező fiók, és próbálja újból elvégezni a feladatot. További információkért lásd: [replikálása VMware virtuális gépek és fizikai kiszolgálók Azure-bA az Azure Site Recovery szolgáltatással](site-recovery-vmware-to-azure-classic.md)
* Ha a feladat-visszavételt Linux virtuális gép, és futtassa a helyszíni, láthatja, hogy a hálózatkezelő csomag el lett távolítva a gép. Az Eltávolítás azért van, mert a hálózatkezelő csomagot el kell távolítani, ha a virtuális Gépet helyre lett állítva, az Azure-ban.
* Ha egy virtuális gép statikus IP-címmel van konfigurálva, és át nem adja a Azure, az IP-cím szerezte be a DHCP. Amikor a rendszer átadja a Vissza gombra, és a helyszínen, a virtuális gép továbbra is az IP-cím beszerzése a DHCP Protokollt használják. Manuálisan jelentkezzen be a gépet, majd állítsa vissza az IP-címet a statikus cím, ha szükséges.
* Vagy ESXi 5.5 ingyenes kiadásban, vagy vSphere 6 hipervizor ingyenes kiadás használatakor volna sikertelen volt, de nem sikerült a feladat-visszavétel lenne. Ahhoz, hogy a feladat-visszavétel, frissíteni vagy program próbalicencre.
* Ha a kiszolgáló nem érhető el a következő adatok a Folyamatkiszolgálótól, ellenőrizze a kapcsolatot és a konfigurációs kiszolgáló által a Telnet és a konfigurációs kiszolgáló a 443-as porton. Pingelje meg a konfigurációs kiszolgáló, a Folyamatkiszolgáló gépről is próbálkozhat. A Folyamatkiszolgáló lehet szívverést a konfigurációs kiszolgálóhoz való csatlakozáskor.
* Próbálja vissza egy másik vCenter az sikertelen lesz, ha győződjön meg arról, hogy az új vCenter fel van derítve és, hogy a fő célkiszolgáló is fel van derítve. Egy tipikus tünete, hogy a datastores nem elérhetők és látható a **lássa el újból védelemmel** párbeszédpanel megnyitásához.
* Egy védett, mint egy fizikai a helyi számítógépen, nem lehet nem sikerült az Azure-ból a helyszíni WS2008R2SP1 machine.

## <a name="fail-back-with-expressroute"></a>Az ExpressRoute-es vissza
Vissza a VPN-kapcsolaton keresztül, vagy egy ExpressRoute-kapcsolat használatával sikertelen lehet. Ha egy ExpressRoute-kapcsolat használni kívánt, vegye figyelembe a következőket:

* Az ExpressRoute-kapcsolatot az Azure virtuális hálózat, amely átveszi a forrásgépek, és ahol az Azure virtuális gépek találhatók a feladatátvételt követően a kell beállítani.
* Egy Azure storage-fiók egy nyilvános végpontot az adatok replikálódnak. Egy ExpressRoute-kapcsolatot használ, állítsa be a nyilvános társviszony-létesítés az ExpressRoute a Site Recovery replikációs cél adatközpontot.
