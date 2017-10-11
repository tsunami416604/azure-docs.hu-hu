---
title: "StorSimple fájlmegosztási vész-Helyreállítási az Azure Site Recovery szolgáltatással automatizálhatja |} Microsoft Docs"
description: "Ismerteti a lépéseket és ajánlott eljárások a vész-helyreállítási megoldást az üzemeltetett Microsoft Azure StorSimple tároló fájlmegosztások létrehozásához."
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 23049a2c-055e-4d0e-b8f5-af2a87ecf53f
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/09/2017
ms.author: vidarmsft
ms.openlocfilehash: b4d575587eec1bcf43c33c7faeb8360ec67b5214
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Azure Site Recovery segítségével StorSimple üzemeltetett fájlmegosztások automatizált vész-helyreállítási megoldás
## <a name="overview"></a>Áttekintés
A Microsoft Azure StorSimple hibrid felhőalapú tárolási megoldás, amely a strukturálatlan adatok gyakran társított fájlmegosztások bonyolultságára. StorSimple felhőbeli tárhelyét használja a helyszíni megoldás, és automatikusan rétegek adatok kiterjesztése a helyszíni és felhőalapú tárolására között. Az adatvédelem integrálva van a helyi és felhőalapú pillanatfelvételek, nem kell az sprawling tároló-infrastruktúra.

[Az Azure Site Recovery](../site-recovery/site-recovery-overview.md) egy Azure-alapú szolgáltatás, amely a vész-helyreállítási képességeket biztosít replikáció, feladatátvétel és helyreállítási virtuális gépek megvalósításában. Az Azure Site Recovery számos replikációs technológia következetesen replikálni, védheti meg és zökkenőmentesen átveheti a virtuális gépek és a privát vagy nyilvános vagy a szolgáltatott felhők alkalmazásokat támogatja.

Az Azure Site Recovery, a virtuálisgép-replikációt és a StorSimple felhő pillanatképkezelési funkciókat, védelmet biztosíthat a teljes fájl kiszolgálói környezet. Egy becsukódjon egyetlen kattintással segítségével a fájlmegosztások online állapotba az Azure-ban csak néhány perc múlva.

Ez a dokumentum részletesen ismerteti, hogyan hozhat létre egy vész-helyreállítási megoldást a StorSimple tárolási üzemeltetett fájlmegosztások és végrehajtani a tervezett, nem tervezett, és feladatátvételi tesztek használ egy kattintással helyreállítási tervet. Lényegében azt illusztrálja, hogyan módosíthatja a helyreállítási terv a StorSimple feladatátvételek során vészhelyreállítási forgatókönyvek engedélyezése az Azure Site Recovery tárolójából. Ismerteti továbbá támogatott konfigurációk és előfeltételek. Jelen dokumentum céljából feltételezzük, hogy jártas az Azure Site Recovery és a StorSimple architektúrák alapjait.

## <a name="supported-azure-site-recovery-deployment-options"></a>Támogatott Azure Site Recovery telepítési lehetőségek
Az ügyfelek fizikai kiszolgálóként vagy virtuális gépek (VM) futó Hyper-V vagy VMware telepíthetők, és majd faragottnak kívül StorSimple tároló kötetekről a fájlmegosztások létrehozását. Az Azure Site Recovery megvédheti a fizikai és virtuális központi telepítések egy másodlagos helyre vagy az Azure-bA. Ez a dokumentum ismerteti az Azure-bA Hyper-V virtuális gép üzemeltetett fájlkiszolgálók esetében a helyreállítási hely és a StorSimple tároló fájlmegosztások vész-Helyreállítási megoldás részletei. Más forgatókönyvekben, ahol a fájlkiszolgáló virtuális gép VMware virtuális gép vagy fizikai gépen hasonlóképpen kell végrehajtani.

## <a name="prerequisites"></a>Előfeltételek
Egy Azure Site Recovery által üzemeltetett StorSimple tároló fájlmegosztások a egy kattintással vész-helyreállítási megoldást Végrehajtási előfeltételei a következők:

* Helyszíni Hyper-V vagy VMware vagy fizikai gépen futó virtuális gép Windows Server 2012 R2 fájl kiszolgáló
* StorSimple tárolási helyszíni Eszközkezelési regisztrálva az Azure StorSimple manager
* StorSimple felhő készülék létrehozása az Azure StorSimple manager (ez is meg kell őrizni, leállítást állapot)
* A StorSimple tárolóeszközön konfigurált köteteken tárolt fájlmegosztások
* [Az Azure Site Recovery services-tároló](../site-recovery/site-recovery-vmm-to-vmm.md) a Microsoft Azure-előfizetés létrehozása

Emellett, ha Azure a helyreállítási hely, futtassa a [Azure virtuális gép Readiness Assessment eszközt](http://azure.microsoft.com/downloads/vm-readiness-assessment/) a virtuális gépeken, és győződjön meg arról, hogy azok kompatibilis Azure virtuális gépek és az Azure Site Recovery services.

(Ami a magasabb költségű) problémákat, győződjön meg arról, hogy hozzon létre a StorSimple felhő készülék, az automation-fiók és a tárolási késés elkerülése érdekében fiók(ok) ugyanabban a régióban.

## <a name="enable-dr-for-storsimple-file-shares"></a>StorSimple-fájlmegosztások vész-Helyreállítási engedélyezése
Minden összetevőnek a helyszíni környezet kell befejezni a replikációt és helyreállítási védeni. Ez a szakasz ismerteti, hogyan:

* (Opcionális) Active Directory és a DNS-replikáció beállítása
* Azure Site Recovery segítségével a fájlkiszolgáló virtuális gép védelmének engedélyezése
* A StorSimple-kötetek védelmének engedélyezése
* A hálózat konfigurálása

### <a name="set-up-active-directory-and-dns-replication-optional"></a>(Opcionális) Active Directory és a DNS-replikáció beállítása
Ha szeretné védeni a gépek futnak az Active Directory és a DNS, hogy elérhetők a vész-Helyreállítási helyen, akkor explicit módon a védelmüket (így a fájlkiszolgálók hitelesítéssel átkapcsolás után érhetők el). Két módon ajánlott az ügyfél helyszíni környezetben összetettsége alapján.

#### <a name="option-1"></a>1. lehetőséget
Ha az ügyfél egy kis számú alkalmazást, a teljes egyetlen tartományvezérlővel rendelkezik helyszíni hely, és lesz akkor javasoljuk, hogy a tartomány a tartományvezérlő gépet replikálni egy másodlagos Azure Site Recovery-replikációt használ a teljes helyre feladatátvétele a webhely (Ez az a pont-pont és a webhely-Azure alkalmazható).

#### <a name="option-2"></a>2. lehetőséget
Ha az ügyfél alkalmazások nagy számú, az Active Directory-erdőt fut, és néhány alkalmazások keresztül egyszerre lesz sikertelen, akkor azt javasoljuk, hogy egy további tartományvezérlőt a vész-Helyreállítási helyen beállítása (vagy egy másodlagos helyre vagy az Azure-ban).

Tekintse meg [automatikus vész-Helyreállítási megoldást nyújt az Active Directory és az Azure Site Recovery segítségével DNS](../site-recovery/site-recovery-active-directory.md) utasításokat, ha egy tartományvezérlő elérhetővé tétele a vész-Helyreállítási helyen. Ez a dokumentum további része, a indulunk ki a vész-Helyreállítási helyen rendelkezésre áll egy tartományvezérlő.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Azure Site Recovery segítségével a fájlkiszolgáló virtuális gép védelmének engedélyezése
Ebben a lépésben elő kell készíteni a helyi fájl kiszolgálói környezet létrehozása és előkészítése az Azure Site Recovery-tárolóban és engedélyezi a virtuális gép fájl védelmét.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>A helyi fájl kiszolgálói környezet előkészítése
1. Állítsa be a **felhasználói fiókok felügyelete** való **nincs értesítés**. Ez azért szükséges, hogy az Azure automation-parancsfájlok segítségével az iSCSI-tárolók csatlakozás után át az Azure Site Recovery sikertelen lesz.

   1. Nyomja meg a Windows billentyű + Q, és keresse meg a **UAC**.
   2. Válassza ki **módosítás felhasználói fiókok felügyelete beállításainak**.
   3. A sáv húzza felfelé **nincs értesítés**.
   4. Kattintson a **OK** majd **Igen** megjelenésekor.

      ![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image1.png)
2. A Virtuálisgép-ügynök telepítése minden egyes, a fájlkiszolgáló virtuális gépeket. Ez azért szükséges, hogy az Azure automation parancsfájlok futtathatók a feladatait át virtuális gépeket.

   1. [Töltse le az ügynököt](http://aka.ms/vmagentwin) való `C:\\Users\\<username>\\Downloads`.
   2. Nyissa meg a Windows PowerShell rendszergazdai módban (Futtatás rendszergazdaként), és adja meg a letöltési hely navigáljon a következő parancsot:

      `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`

      > [!NOTE]
      > A fájlnév verziójától függően változhat.
      >
      >
3. Kattintson a **Tovább** gombra.
4. Fogadja el a **feltételeket a szerződés** majd **következő**.
5. Kattintson a **Befejezés** gombra.
6. StorSimple tárolási kívül faragottnak köteteket használó fájlmegosztásokat létrehozni. További információkért lásd: [kötetek kezelése a StorSimple Manager szolgáltatás segítségével](storsimple-manage-volumes.md).

   1. A helyszíni virtuális gépeken, nyomja meg a Windows billentyű + Q, és keresse meg a **iSCSI**.
   2. Válassza ki **iSCSI-kezdeményező**.
   3. Válassza ki a **konfigurációs** lapra, és másolja a kezdeményező neve.
   4. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
   5. Válassza ki a **StorSimple** lapra, és válassza ki a StorSimple Manager szolgáltatás, amely tartalmazza a fizikai eszköz.
   6. Kötet tárolója létrehozása, és majd a kötetek létrehozása. (Ezek a kötetek szolgálnak a fájl megosztásainak a fájlkiszolgáló virtuális gépeken). Másolja a kezdeményező neve, és adjon nevet a hozzáférés-vezérlési rekordokat, a kötetek létrehozásakor.
   7. Válassza ki a **konfigurálása** lapra, és vegye figyelembe az IP-cím, az eszköz le.
   8. A helyszíni virtuális gépeken, navigáljon a **iSCSI-kezdeményező** újra és írja be az IP-cím a gyors csatlakozás szakaszban. Kattintson a **gyors csatlakozás** (az eszköz most kell csatlakoztatni).
   9. Nyissa meg az Azure portál, és válassza a **kötetek és eszközök** fülre. Kattintson a **automatikus konfigurálása**. Az újonnan létrehozott kötetre kell megjelennie.
   10. A portálon, válassza ki a **eszközök** fülre, majd jelölje **hozzon létre egy új virtuális eszközt.** (A virtuális eszköz alkalmazva lesznek, ha a feladatátvételt hajt végre). Az új virtuális eszköz további költségek elkerülése érdekében offline állapotban is megmarad. A virtuális eszköz kapcsolat nélküli üzemmódra állítása, keresse fel a **virtuális gépek** a portál szakaszt, és le.
   11. Lépjen vissza a helyszíni virtuális gépeket, és nyissa meg a Lemezkezelés (nyomja meg a Windows billentyű + X, és válassza ki **Lemezkezelés**).
   12. Megfigyelheti, hogy néhány további lemezek (attól függően, hogy a létrehozott kötetek száma). Kattintson a jobb gombbal a először egy select **lemez inicializálása**, és válassza ki **OK**. Kattintson a jobb gombbal a **Unallocated** szakaszban jelölje be **új egyszerű kötet**, a meghajtóbetűjelet rendelje hozzá, és a varázsló befejezéséhez.
   13. Ismételje meg a l. a lemezeket. Most már megtekintheti a lemezeket a **ez PC** a Windows Intézőben.
   14. Használja a fájl- és tárolási szolgáltatások szerepkör fájlmegosztásokat hozhat létre ezeken a köteteken.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Létrehozása és előkészítése az Azure Site Recovery-tároló
Tekintse meg a [Azure Site Recovery dokumentáció](../site-recovery/site-recovery-hyper-v-site-to-azure.md) a fájlkiszolgáló virtuális gép védelmének megkezdéséhez az Azure Site Recovery szolgáltatással.

#### <a name="to-enable-protection"></a>Engedélyezze a védelmet
1. Az iSCSI cél(ok) bontja a helyszíni virtuális gépek az Azure Site Recovery segítségével védeni kívánt:

   1. Nyomja le a Windows billentyű + Q, és keresse meg a **iSCSI**.
   2. Válassza ki **iSCSI-kezdeményező beállítása**.
   3. Válassza le a korábban csatlakoztatott StorSimple eszközt. Azt is megteheti, megváltoztathatja a fájlkiszolgáló ki néhány percig a védelem engedélyezésekor.

   > [!NOTE]
   > Ennek hatására a fájlmegosztások átmenetileg nem érhető el.
   >
   >
2. [A virtuális gép védelmének engedélyezése](../site-recovery/site-recovery-hyper-v-site-to-azure.md) a fájlkiszolgáló virtuális gép az Azure Site Recovery portálról.
3. A kezdeti szinkronizálás kezdődik, amikor újra csatlakozhat a cél. Nyissa meg az iSCSI-kezdeményezőt a StorSimple eszközt, és kattintson **Connect**.
4. A szinkronizálás befejeztével, miután a virtuális gép állapota **védett**, válassza ki a virtuális Gépet, jelölje ki a **konfigurálása** lapot, és ennek megfelelően frissíti a virtuális gép hálózati (Ez az a hálózat, amely a sikertelen keresztül Virtuális gép van egy része lesz). Ha a hálózat nem jelenik meg, az azt jelenti, hogy a szinkronizálási továbbra is zajlik.

### <a name="enable-protection-of-storsimple-volumes"></a>A StorSimple-kötetek védelmének engedélyezése
Ha nincs kiválasztva a **engedélyezése ehhez a kötethez alapértelmezett biztonsági mentés** választás, a StorSimple-köteteket, nyissa meg a **biztonsági mentési házirendek** a StorSimple Manager szolgáltatásra, és alkalmas biztonsági mentési házirend létrehozása a köteteket. Ajánlott biztonsági mentések beállítani a helyreállítási időkorlát (RPO), amelyet az alkalmazás megtekintéséhez.

### <a name="configure-the-network"></a>A hálózat konfigurálása
A fájlkiszolgáló virtuális gép, hálózati beállítások konfigurálása a Azure Site Recovery, hogy a Virtuálisgép-hálózatok a feladatátvételt követően a megfelelő vész-Helyreállítási hálózathoz van csatlakoztatva.

Kiválaszthatja a virtuális gép a **replikált elemek** lapján adja meg a hálózati beállításokat a következő ábrán látható módon.

![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image2.png)

## <a name="create-a-recovery-plan"></a>Helyreállítási terv létrehozása
Az ASR Szolgáltatásban a fájlmegosztások a feladatátvételi folyamat automatizálása helyreállítási tervet is létrehozhat. A szüneteltetése akkor fordul elő, ha van lehetősége a fájlmegosztások csupán egyetlen kattintással néhány perc múlva. Ahhoz, hogy ezt az automatizálást, szüksége lesz egy Azure automation-fiók.

#### <a name="to-create-an-automation-account"></a>Automation-fiók létrehozása
1. Az Azure-portálon lépjen &gt; **Automation** szakasz.
2. Kattintson a **+ Hozzáadás** gomb, megnyílik a panel alatt.

   ![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image11.png)

   * Name,-adja meg az új automation-fiók
   * Előfizetés - előfizetés kiválasztása
   * Resource group - új/válasszon meglévő erőforráscsoport létrehozása
   * Hely - hely kiválasztásához, legyen az ugyanazon földrajzi vagy régióban, amelyben a StorSimple felhő készüléket és a Storage-fiókok létrejöttek.
   * Hozzon létre válassza ki az Azure-beli futtató fiók - **Igen** lehetőséget.

3. Ugrás az Automation-fiókot, kattintson a **Runbookok** &gt; **Tallózás gyűjtemény** rendszerbe való importálás érdekében a szükséges forgatókönyvek az automation-fiók.
4. Adja hozzá a következő forgatókönyvek található **vész-helyreállítási** címke a gyűjteményben:

   * StorSimple-köteteket a teszt feladatátvételi (TFO) után tisztítása
   * Feladatátvételi StorSimple kötettárolók
   * Csatlakoztassa a köteteket a StorSimple eszközön a feladatátvételt követően
   * Távolítsa el az egyéni parancsprogramok futtatására szolgáló bővítmény Azure virtuális gépen
   * Indítsa el a StorSimple virtuális készülék

     ![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image3.png)

5. A parancsfájlok közzététele kiválasztja a runbook automation-fiók, és kattintson a **szerkesztése** &gt; **közzététel** , majd **Igen** ellenőrzési üzenethez. Ez a lépés után a **Runbookok** lapon fog megjelenni az alábbiak szerint:

    ![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image4.png)

6. Az automation-fiók, jelölje ki a **eszközök** lapon &gt; kattintson **változók** &gt; **változó hozzáadása** , és adja hozzá a következő változókat. Ha szeretné, ezek az eszközök titkosításához. Ezek a változók a helyreállítási terv-specifikus. Ha a helyreállítási terv (amely a következő lépésben létrehoz) neve TestPlan, akkor a változók csal, TestPlan-StorSimRegKey TestPlan-AzureSubscriptionName és így tovább.

   * *RecoveryPlanName***- StorSimRegKey**: a StorSimple Manager szolgáltatáshoz a regisztrációs kulccsal.
   * *RecoveryPlanName***- AzureSubscriptionName**: az Azure-előfizetés nevét.
   * *RecoveryPlanName***- ResourceName**: a StorSimple-erőforrás, amely rendelkezik a StorSimple-eszköz nevét.
   * *RecoveryPlanName***- DeviceName**: az eszköz, amelynek a feladatait át szeretné adni.
   * *RecoveryPlanName***- VolumeContainers**: kötettárolók vesszővel elválasztott karakterlánc volcon1, volcon2, volcon3 az eszközön, amely kell végrehajtani, több mint; például szükség van.
   * *RecoveryPlanName***- TargetDeviceName**: A StorSimple felhő készüléknek, amelyen a tárolók feladatátvételre van.
   * *RecoveryPlanName***- TargetDeviceDnsName**: a céleszközt szolgáltatás nevét (Ez megtalálhatók a **virtuális gép** szakasz: a szolgáltatás neve megegyezik a DNS-név).
   * *RecoveryPlanName***- StorageAccountName**: A tárfiók nevét, amelyben a parancsfájl (melynek futtatnia a sikertelen a virtuális gép) tárolódik. Ez lehet a storage-fiók, amely ideiglenesen tárolja a parancsfájl lemezterületet rendelkezik.
   * *RecoveryPlanName***- StorageAccountKey**: a fenti storage-fiók elérési kulcsának.
   * *RecoveryPlanName***- ScriptContainer**: A nevét, a tároló, amelyben a parancsfájl eltárolva a felhőben. Ha a tároló nem létezik, a rendszer létrehozza.
   * *RecoveryPlanName***- VMGUIDS**: után a virtuális gépek védelméhez, Azure Site Recovery minden virtuális gép egyedi-Azonosítót rendel hozzá, amely a sikertelen részleteit átadó virtuális gép. A VMGUID beszerzéséhez válassza ki a **Recovery Services** fülre, és kattintson **védett elem** &gt; **védelmi csoportok** &gt;  **Gépek** &gt; **tulajdonságok**. Ha több virtuális géphez, majd adja hozzá a GUID egy vesszővel elválasztott karakterlánc.
   * *RecoveryPlanName***- AutomationAccountName** – a neve, amelyben a runbookokat és az eszközök hozzáadta az automation-fiók.

  Például, ha a helyreállítási terv neve fileServerpredayRP akkor a **hitelesítő adatok** & **változók** lapon meg kell jelennie az alábbiak szerint az eszközök hozzáadása után.

   ![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image5.png)

7. Lépjen a **Recovery Services** válassza ki azt a korábban létrehozott Azure Site Recovery-tárolóban.
8. Válassza ki a **helyreállítási tervek (helyreállítás)** parancsát **kezelése** csoportban, és hozzon létre új helyreállítási terv az alábbiak szerint:

   a.  Kattintson a **+ a helyreállítás terv** gomb, megnyílik a panel alatt.

      ![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image6.png)

   b.  Adja meg a helyreállítási terv nevét, válassza ki a forrás, a cél és a központi telepítési modell értékeket.

   c.  Válassza ki a virtuális gépeket a védelmi csoportból, a helyreállítási tervben közé tartoznak, és kattintson a kívánt **OK** gombra.

   d.  Jelölje ki azt a korábban létrehozott helyreállítási terv **Testreszabás** gombra kattintva nyissa meg a helyreállítási terv testreszabási nézetet.

   e.  Kattintson a jobb gombbal **összes csoportok leállítási** kattintson **előtti művelet hozzáadása**.

   f.  Megnyitja Insert művelet panelen, adjon meg egy nevet, válassza ki **elsődleges ügyféloldali** kapcsoló jelölje ki az Automation-fiók (hozzá a runbookok), majd válassza ki, ha a beállítás a **feladatátvételi-StorSimple-kötet-tárolók**  runbook.

   g.  Kattintson a jobb gombbal **csoport 1: Start** kattintson **Hozzáadás védett elemek** lehetőséget, majd válassza ki a virtuális gépeket lehet védetté tenni a helyreállítási tervet, majd kattintson a kívánt **Ok** gombra. Nem kötelező, ha már van kiválasztva a virtuális gépek.

   h.  Kattintson a jobb gombbal **csoport 1: Start** kattintson **művelet utáni** lehetőséget, majd adja hozzá az alábbi parancsfájlok:

   * Runbook elindítása-StorSimple-virtuális-készülék
   * Over-StorSimple-kötet-tárolók runbook sikertelen
   * Runbook csatlakoztatási-kötetek-után-feladatátvétel
   * Távolítsa el – egyéni-parancsfájl-kiterjesztés runbook

   i.  Adja hozzá a manuális műveletet a fenti 4 parancsfájlokat ugyanazon **csoport 1: utáni lépéseket** szakasz. Ez a művelet az a pont, ahol ellenőrizheti, hogy minden helyesen működik. Ez a művelet hozzá kell adni a feladatátvételi teszt részeként csak (csak így jelölje be a **feladatátvételi teszt** jelölőnégyzet).

   j.  A manuális műveletet követően adja hozzá a **tisztítás** parancsfájl-ugyanazzal az eljárással, amelyet a más runbookokat használt. **Mentés** a helyreállítási terv.

    > [!NOTE]
    > Feladatátvételi teszt futtatásakor Ellenőrizze minden, a manuális műveletet lépésnél, mert a StorSimple-köteteket a céleszközön kellett lett klónozása törli a karbantartás részeként a manuális művelet befejeződése után.
    >

    ![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image7.png)

## <a name="perform-a-test-failover"></a>Feladatátvételi teszt végrehajtása
Tekintse meg a [Active Directory vész-Helyreállítási megoldás](../site-recovery/site-recovery-active-directory.md) szempontokról adott Active Directory a feladatátvételi teszt útmutatója. A helyszíni beállítások nem zavarják minden, a teszt feladatátvétel esetén. A StorSimple-köteteket a helyszíni virtuális gép csatlakozó Azure StorSimple felhő készülékre van klónozása. Tesztelési célból egy virtuális Gépet az Azure-ban nem válik, és a klónozott kötetek vannak csatolva a virtuális Gépet.

#### <a name="to-perform-the-test-failover"></a>A feladatátvételi teszt végrehajtásához.
1. Az Azure-portálon válassza ki a site recovery-tárolóban.
2. Kattintson a helyreállítási terv létrehozása a fájlkiszolgáló virtuális gép.
3. Kattintson a **feladatátvételi teszt**.
4. Válassza ki az Azure virtuális hálózat, amelyhez Azure virtuális gépek csatlakoznak feladatátvételt követően.

   ![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image8.png)
5. A feladatátvételi művelet elindításához kattintson az **OK** gombra. Folyamatban van a virtuális gép tulajdonságainak megnyitásához, vagy a kattintva követheti nyomon a **tesztfeladat feladatátvételt** a tároló neve &gt; **feladatok** &gt; **Site Recovery-feladatok**.
6. A feladatátvétel befejezése után meg kell tudni a replika Azure machine jelennek meg az Azure-portálon &gt; **virtuális gépek**. Végezheti el az érvényesítést.
7. Miután az ellenőrzés befejezése után kattintson **érvényesítést teljes**. Ez most a StorSimple-köteteket és a StorSimple felhő készülék leállítás.
8. Amikor elkészült, kattintson a **karbantartása a feladatátvételi teszt** a a helyreállítási terv. A megjegyzések és mentéséhez a feladatátvételi teszttel kapcsolatos megfigyelések feljegyzéséhez. Ezzel a lépéssel törli a virtuális gép teszt feladatátvétele során létrehozott.

## <a name="perform-a-planned-failover"></a>Végezzen el egy tervezett feladatátvételt
   Egy tervezett feladatátvétel során a helyszíni fájlkiszolgáló virtuális gép van leállítása és a StorSimple eszköz kötetnek a biztonsági mentési pillanatképet készít felhő. A StorSimple-köteteket feladatátvétel történt a virtuális eszközhöz, a replika virtuális Gépre az Azure-on nem válik, és a kötetek vannak csatolva a virtuális gép.

#### <a name="to-perform-a-planned-failover"></a>A tervezett feladatátvétel végrehajtása
1. Válassza ki az Azure-portálon **helyreállítási szolgáltatások** tároló &gt; **helyreállítási tervek (helyreállítás)** &gt; **recoveryplan_name** készült a a fájlkiszolgáló virtuális gép.
2. A helyreállítási terv paneljén kattintson **további** &gt; **tervezett feladatátvétel**.

   ![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image9.png)
3. Az a **tervezett feladatátvétel megerősítése** panelen válassza ki a forrás és a célhelyek és válassza ki a cél hálózati, és kattintson a pipa ikonra a feladatátvételi folyamat ✓.
4. A replika virtuális gépek létrehozása után is a Függőben állapotba. Kattintson a **véglegesítési** a feladatátvétel véglegesítésének.
5. Replikáció befejezése után a virtuális gépek indítása a másodlagos helyen.

## <a name="perform-a-failover"></a>Végezzen el egy feladatátvételt
Egy nem tervezett feladatátvétel során a StorSimple-köteteket feladatátvétel történt a virtuális eszközhöz, a replika virtuális gép a Azure kerül, és a kötetek vannak csatolva a virtuális gép.

#### <a name="to-perform-a-failover"></a>A feladatátvétel végrehajtásához
1. Válassza ki az Azure-portálon **helyreállítási szolgáltatások** tároló &gt; **helyreállítási tervek (helyreállítás)** &gt; **recoveryplan_name** készült a a fájlkiszolgáló virtuális gép.
2. A helyreállítási terv paneljén kattintson **további** &gt; **feladatátvételi**.
3. Az a **megerősítéséhez feladatátvétel** panelen válassza ki az adatforrást, és a cél helyét.
4. Válassza ki **virtuális gépek leállítása és a legfrissebb adatok szinkronizálása** adhatja meg, hogy a Site Recovery próbálkozzon az állítsa le a védett virtuális gépet, és szinkronizálja az adatokat, hogy a legújabb adatok átvétele fog megtörténni.
5. A feladatátvétel után a virtuális gép állapot függőben van. Kattintson a **véglegesítési** a feladatátvétel véglegesítésének.


## <a name="perform-a-failback"></a>A feladat-visszavételt végrehajtani
A feladat-visszavétel során StorSimple kötettárolók vannak feladatátvételt vissza a fizikai eszköz után biztonsági másolatból történik.

#### <a name="to-perform-a-failback"></a>A feladat-visszavétel végrehajtására.
1. Válassza ki az Azure-portálon **helyreállítási szolgáltatások** tároló &gt; **helyreállítási tervek (helyreállítás)** &gt; **recoveryplan_name** készült a a fájlkiszolgáló virtuális gép.
2. A helyreállítási terv paneljén kattintson **további** &gt; **tervezett feladatátvétel**.
3. Válassza ki a forrás és cél helyét, válassza ki a megfelelő adatszinkronizálási és a virtuális gép létrehozásának beállításai.
4. Kattintson a **OK** gombra kattintva indítsa el a feladatátvételi folyamat.

   ![](./media/storsimple-disaster-recovery-using-azure-site-recovery/image10.png)

## <a name="best-practices"></a>Ajánlott eljárások
### <a name="capacity-planning-and-readiness-assessment"></a>A kapacitás tervezésével és a készültségi értékelés
#### <a name="hyper-v-site"></a>Hyper-V-hely
Használja a [felhasználói kapacitás planner eszköz](http://www.microsoft.com/download/details.aspx?id=39057) a kiszolgáló, a tároló és a hálózati infrastruktúra, a Hyper-V replika környezetének megtervezéséhez.

#### <a name="azure"></a>Azure
Futtathatja a [Azure virtuális gép Readiness Assessment eszközt](http://azure.microsoft.com/downloads/vm-readiness-assessment/) annak érdekében, hogy azok kompatibilis Azure virtuális gépek és az Azure Site Recovery Services virtuális gépeken. A Készültségfelmérő eszköz ellenőrzi a Virtuálisgép-konfigurációk, és figyelmeztetést küld, ha a konfiguráció nem kompatibilisek az Azure-ral. Például kapcsolatos figyelmeztetés Ha a C: meghajtó 127 GB-nál nagyobb.

Kapacitástervezés alkotják, legalább két fontos folyamat:

* Leképezés a helyszíni Hyper-V virtuális gépek Azure virtuális gép méretét (például A6, A7, A8 és a9-es).
* Annak meghatározása, hogy a szükséges internetes sávszélességet.

## <a name="limitations"></a>Korlátozások
* Jelenleg csak az 1 StorSimple eszköz feladatátvételre (az egyetlen StorSimple felhő készülék). A forgatókönyv egy fájlkiszolgáló több StorSimple eszközt magában foglaló jelenleg nem támogatott.
* Ha a virtuális gépek védelmének engedélyezésekor hibaüzenetet kap, győződjön meg arról, hogy az iSCSI-tárolók kapcsolat bontása.
* Egy biztonsági mentési házirendek kötettárolók átnyúlva miatt csoportban minden kötettárolók átvétele fog megtörténni együtt.
* A kiválasztott kötet tárolókban lévő összes kötet átvétele fog megtörténni.
* Hozzáadott akár 64 TB-nál nagyobb kötetek nem lehet feladatokat átvenni, mert egy egyetlen StorSimple felhő készülék maximális kapacitása 64 TB-ot.
* Ha a tervezett/nem tervezett feladatátvétel sikertelen lesz, és a virtuális gépek jönnek létre az Azure-ban, majd üríti a virtuális gépeket. Ehelyett hajtsa végre egy feladat-visszavételre. Ha törli a virtuális gépek majd a helyszíni virtuális gépek nem kapcsolható be újra.
* Egy feladatátvétel után Ha nem láthatók a köteteket, nyissa meg a virtuális gépek, nyissa meg a Lemezkezelés, ellenőrizze újra a lemezeket, és majd kapcsolásuk.
* Bizonyos esetekben a vész-Helyreállítási hely meghajtóbetűjeleket eltérhet a betűk helyszíni. Ilyen esetben szüksége lesz a feladatátvétel befejezése után manuálisan hárítsa el a problémát.
* Többtényezős hitelesítés az Azure hitelesítő adatokat, amelyeket is meg kell adni az automation-fiók eszközként le kell tiltani. A hitelesítés nem le van tiltva, ha parancsfájlok nem használhatók automatikus futtatását, és a helyreállítási terv sikertelen lesz.
* Feladatátvételi feladat időtúllépése: A StorSimple parancsfájl időtúllépést okoz, ha kötettárolók feladatátvételének az Azure Site Recovery felső határ az egyes parancsfájl (jelenleg 120 perc) több időt vesz igénybe.
* Biztonsági mentési feladat időtúllépése: A StorSimple parancsfájl végrehajtásának időkorlátja, ha a kötetek biztonsági mentését az Azure Site Recovery felső határ az egyes parancsfájl (jelenleg 120 perc) több időt vesz igénybe.

  > [!IMPORTANT]
  > A biztonsági mentés futtassa manuálisan az Azure-portálon, és futtassa újra a helyreállítási terv.

* Klónozza a feladat időtúllépése: A StorSimple parancsfájlt időtúllépés történik, ha tovább tart a kötetek klónozása mint az Azure Site Recovery felső határ az egyes parancsfájl (jelenleg 120 perc).
* Szinkronizálási hiba ideje: A StorSimple parancsfájlok hibák meg arról, hogy a biztonsági mentése sikertelen volt-e annak ellenére, hogy a biztonsági mentés sikeres, a portálon. Ennek oka lehet, hogy a StorSimple készülék idő nincs szinkronban az aktuális idő időzónája lehet.

  > [!IMPORTANT]
  > A készülék idő az aktuális idő időzónája szinkronizálása.

* Készülék feladatátvételi hiba: A StorSimple parancsfájl meghiúsulhat, ha van egy készülék feladatátvételt, ha a fut-e a helyreállítási terv.

  > [!IMPORTANT]
  > Futtassa újra a helyreállítási terv, a készülék feladatátvételi befejeződése után.


## <a name="summary"></a>Összefoglalás
Azure Site Recovery segítségével, a fájlkiszolgáló virtuális gép teljes automatizált vészhelyreállítási tervet hozhat létre a StorSimple tárolón tárolt fájlmegosztások rendelkező. Bárhonnan másodpercen belül is kezdeményezhető a feladatátvétel esetén a megszakítás és az az alkalmazás lépéseivel néhány perc múlva.
