---
title: Kapacitás megtervezése és skálázás az Azure-bA az Azure Site Recovery VMware-replikációhoz |} A Microsoft Docs
description: Ez a cikk a kapacitás megtervezése és a méretezési csoport használja, ha a VMware virtuális gépek replikálása az Azure Site Recoveryvel Azure-bA
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.date: 07/06/2018
ms.topic: conceptual
ms.author: rayne
ms.openlocfilehash: 905798acd5836c31953714d7984cfb19f16cecab
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37920797"
---
# <a name="plan-capacity-and-scaling-for-vmware-replication-with-azure-site-recovery"></a>Kapacitás megtervezése és skálázás az Azure Site Recovery VMware-replikáláshoz

Ez a cikk segítségével azonosítani a kapacitástervezés és skálázás, ha a helyszíni VMware virtuális gépek és fizikai kiszolgálók replikálása az Azure-bA [Azure Site Recovery](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Hogyan kezdhetem meg, hogy kapacitástervezés?

A replikálási környezetre vonatkozó információk összegyűjtésére futtatásával a [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner-doc) a VMware replikálásához. [További információ](site-recovery-deployment-planner.md) az eszközről. Kompatibilis és nem kompatibilis virtuális gépek, lemezek, virtuális gépenként információt gyűjthet a lesz, és az adatváltozás lemezenként. Emellett az eszköz teljesíti a hálózati sávszélességre vonatkozó követelményeket, és lefedi a sikeres replikációhoz és feladatátvételi teszthez szükséges Azure-infrastruktúrát.

## <a name="capacity-considerations"></a>A kapacitás szempontok

**Összetevő** | **Részletek** |
--- | --- | ---
**Replikáció** | **Maximális napi adatváltozási sebesség:** egyetlen folyamatkiszolgáló képes kezelni a napi és a egy védett számítógép csak használhatja egy folyamatkiszolgáló módosítása minősítése 2 TB-ig. 2 TB-os így, a maximális napi adatváltozási gyakoriság, amely a védett gép támogatott.<br/><br/> **Maximális átviteli sebesség:** egy storage-fiókot az Azure-ban A replikált gépek is tartozhatnak. A standard szintű tárfiók képes kezelni másodpercenként 20 000 kérelem legfeljebb, és azt javasoljuk, hogy őrizze meg a bemeneti/kimeneti műveletek száma másodpercenként (IOPS) számát a forrásgép között 20 000. Például ha a forrásgép 5 lemezekkel rendelkezik, és az egyes lemezek állít elő, 120 IOPS (8 KB a mérete) a forrásgépen, majd lesz a lemez IOPS-korlát az 500-as Azure-ban. (A szükséges tárfiókok száma osztva a 20 000 teljes forrásgépen IOPS, megegyezik.)
**Konfigurációs kiszolgáló** | A konfigurációs kiszolgáló tudja kezelni a napi módosítási aránya kapacitást az összes védett gépeken futó számítási feladatok között kell lennie, és ha folyamatosan szeretné replikálni adatokat az Azure Storage elegendő sávszélességet kell.<br/><br/> Ajánlott eljárásként keresse meg a konfigurációs kiszolgáló az ugyanahhoz a hálózathoz és a LAN-szegmens, a védeni kívánt gépeket. Egy másik hálózaton található, de a védeni kívánt gépek kell rendelkezniük a réteg 3 hálózati rá.<br/><br/> A tábla a következő szakaszban található a konfigurációs kiszolgáló méretezési javaslatokat foglalja össze.
**Folyamatkiszolgáló** | Az első folyamatkiszolgáló a konfigurációs kiszolgálón alapértelmezés szerint telepítve van. A környezet skálázása további folyamatkiszolgálók telepítheti. <br/><br/> A folyamatkiszolgáló replikációs adatokat fogad a védett gépekre, és a gyorsítótárazás, tömörítés és titkosítás segítségével optimalizálja őket. Ezután, elküldi az adatokat az Azure-bA. A folyamat kiszolgáló gépnek rendelkeznie kell a következő feladatok végrehajtására elegendő erőforrással.<br/><br/> A folyamatkiszolgáló a lemezalapú gyorsítótárban használja. 600 GB vagy több külön gyorsítótár lemez használata esetén a hálózati szűk keresztmetszet, illetve leállás tárolt adatok változásának kezelésére.

## <a name="size-recommendations-for-the-configuration-server"></a>A konfigurációs kiszolgáló méretezési javaslatokat

**CPU** | **Memória** | **Gyorsítótár-lemez mérete** | **A módosult adatok aránya** | **Védett gépek**
--- | --- | --- | --- | ---
8 Vcpu (2 sockets * @ 2,5 gigahertz [GHz-es] 4 mag) | 16 GB | 300 GB | 500 GB vagy kevesebb | 100-nál kevesebb gépeket replikálni.
12 vcpu-k (2 sockets * @ 2,5 GHz-es 6 mag) | 18 GB | 600 GB | 500 GB – 1 TB | 100 – 150 gépek között replikálja.
16 vcpu-k (2 sockets * 8 mag, 2,5 GHz-es @) | 32 GB | 1 TB | 1 TB-os 2 TB-ig | 150-200 gépek között replikálja.
Egy másik folyamatkiszolgáló üzembe helyezése | | | > 2 TB | További folyamatkiszolgálók üzembe helyezése, ha több mint 200 olyan gépet replikál, vagy ha a napi adatváltozási sebessége meghaladja a 2 TB.

Az elemek magyarázata:

* Minden egyes forrásgép egyenként 100 GB-os 3 lemezzel van konfigurálva.
* Teljesítménymérési tárolási, 8, 10 k RPM, SAS-meghajtót a RAID 10-ben, gyorsítótár lemez mértékegysége használtuk.

## <a name="size-recommendations-for-the-process-server"></a>A folyamatkiszolgáló vonatkozó javaslatok a méretekkel kapcsolatban

Ha több mint 200 olyan gépet védeni kell, vagy a napi adatváltozási sebesség 2 TB-nál nagyobb, a folyamatkiszolgáló replikációs terhelés kezelésére is hozzáadhat. A horizontális felskálázáshoz a következőket teheti:

* Növelje a konfigurációs kiszolgálók számát. Ha például védheti akár 400 gépet két konfigurációs kiszolgálóval.
* Adjon hozzá további folyamatkiszolgálók, és ezek helyett (vagy mellett) forgalom kezelésére használja a konfigurációs kiszolgálón.

A következő táblázat ismerteti a forgatókönyv, amelyben:

* Ön nem szeretne élni a folyamatkiszolgáló a konfigurációs kiszolgálót használja.
* A további folyamatkiszolgálón beállítását.
* Védett virtuális gépek, a további folyamatkiszolgálón használatára konfigurálta.
* Minden egyes védett forrásgép egyenként 100 GB-os három lemez van konfigurálva.

**Konfigurációs kiszolgáló** | **További folyamatkiszolgáló** | **Gyorsítótár-lemez mérete** | **A módosult adatok aránya** | **Védett gépek**
--- | --- | --- | --- | ---
8 Vcpu (2 sockets * 4 mag, 2,5 GHz-es @), 16 GB memória | 4 vcpu-k (2 sockets * 2 mag, 2,5 GHz-es @), 8 GB memória | 300 GB | 250 GB vagy kevesebb | 85 vagy kevesebb gépeket replikálni.
8 Vcpu (2 sockets * 4 mag, 2,5 GHz-es @), 16 GB memória | 8 Vcpu (2 sockets * 4 mag, 2,5 GHz-es @), 12 GB memória | 600 GB | 250 GB – 1 TB | Replikálja a 85-150 gépek között.
12 vcpu-k (2 sockets * 6 mag, 2,5 GHz-es @), 18 GB memória | 12 vcpu-k (2 sockets * 6 mag, 2,5 GHz-es @) 24 GB memória | 1 TB | 1 TB-os 2 TB-ig | 150-225 gépek között replikálja.

A kiszolgálók, skálázza módja függ a vertikális vagy horizontális felskálázás modellhez tartozó beállításokat.  Néhány csúcskategóriás konfigurációs és folyamatkiszolgálók üzembe helyezésével vertikális vagy horizontális felskálázás kevesebb erőforrást a további kiszolgálók üzembe helyezésével. Például ha 220 gépek van szüksége, sikerült tegye a következők egyikét:

* 12 vCPU, 18 GB memória, a konfigurációs kiszolgáló és a egy további folyamatkiszolgáló 12 vCPU, 24 GB memóriával rendelkező beállítása. Védett gépek csak a további folyamatkiszolgálón használatára konfigurálja.
* (2 x 8 vCPU, 16 GB RAM) két konfigurációs kiszolgáló és két további folyamatkiszolgálók (1 x 8 vCPU, és 4 vcpu-t x 1 leíró 135-ös + 85 [220] gépek) beállítása. Védett gépek csak a további folyamatkiszolgálók használatára konfigurálja.


## <a name="control-network-bandwidth"></a>Hálózati sávszélesség szabályozására

Miután már használta a [a Deployment Planner eszköz](site-recovery-deployment-planner.md) kiszámításához szükséges (a kezdeti replikáció és a majd különbözeti) replikációs sávszélesség, szabályozhatja a használatával több lehetőség közül választhat replikálásához használt sávszélesség mennyiségét:

* **A sávszélesség szabályozását**: az Azure-bA replikált VMware-forgalom halad keresztül egy adott folyamatkiszolgáló. Beállíthatja a sávszélesség a folyamatkiszolgáló futtató gépek szabályozását.
* **Befolyásolhatja a sávszélesség**: befolyásolhatja a sávszélesség-replikációhoz használt néhány beállításkulcsok használatával:
  * A **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** beállításazonosítót a lemezen adatátvitelre (kezdeti vagy változásreplikálásra) használt szálak számát adja meg. Minél magasabb a beállítás értéke, annál nagyobb hálózati sávszélességet használ a rendszer a replikáláshoz.
  * A **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** számát határozza meg az adatátviteli feladat-visszavétel során használt szálak számát.

### <a name="throttle-bandwidth"></a>Sávszélesség szabályozása

1. Nyissa meg az Azure Backup szolgáltatás MMC beépülő modul a gépen, és a folyamatkiszolgáló. Alapértelmezés szerint a helyi biztonsági másolat érhető el az asztalon vagy a következő mappában: C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Kattintson a beépülő modul **Tulajdonságok módosítása** elemére.

    ![Képernyőkép az Azure Backup szolgáltatás MMC beépülő modul lehetőséget tulajdonságainak módosítása](./media/site-recovery-vmware-to-azure/throttle1.png)
3. Az a **sávszélesség-szabályozási** lapon jelölje be **szabályozása a biztonsági mentési műveletek internetes sávszélességének**. Állítsa be a munkaórákra és a munkaórákon kívüli. Érvényes tartományok 1023 MB / s a rendszer 512 KB/s.

    ![Képernyőkép az Azure Backup tulajdonságai párbeszédpanel](./media/site-recovery-vmware-to-azure/throttle2.png)

A szabályozáshoz a [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) parancsmag is használható. Íme egy példa:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

A **Set-OBMachineSetting -NoThrottle** beállítás azt jelenti, hogy nincs szükség szabályozásra.

### <a name="influence-network-bandwidth-for-a-vm"></a>A hálózati sávszélesség szabályozása a virtuális gép

1. Lépjen a beállításjegyzékben a virtuális gép, a **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Befolyásolhatja a sávszélesség forgalmát a replikáló lemez, módosítsa az értékét **UploadThreadsPerVM**, vagy hozzon létre a kulcsot, ha még nem létezik.
   * Befolyásolhatja a sávszélesség a feladat-visszavétel forgalmat az Azure-ból, módosítsa az értékét **DownloadThreadsPerVM**.
2. Az alapértelmezett érték a 4. A szükségesnél több erőforrással ellátott hálózatban érdemes módosítani a beállításazonosítók alapértelmezett értékét. A maximális érték a 32. Az optimális érték kiválasztásához kövesse figyelemmel a forgalmat.


## <a name="deploy-additional-process-servers"></a>További folyamatkiszolgálók üzembe helyezése

Ha horizontális vette az a központi telepítés meghaladja a 200 forrásgépek vagy naponta több mint 2 TB-os adatváltozásának-összesen rendelkezik, további folyamatkiszolgálók a forgalom mértéke kezelésére van szüksége. Kövesse az utasításokat az adott [Ez a cikk](vmware-azure-set-up-process-server-scale.md) állíthatja be a folyamatkiszolgáló. Miután beállította a kiszolgáló, áttelepítheti forrásgépek használhatja azt.

### <a name="migrate-machines-to-use-the-new-process-server"></a>Az új folyamatkiszolgáló használandó gépek migrálása

1. A **beállítások** > **Site Recovery kiszolgálók**, és kattintson a konfigurációs kiszolgáló, majd bontsa ki a **kiszolgálók feldolgozni**.

    ![Képernyőkép a Folyamatkiszolgáló párbeszédpanel](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Kattintson a jobb gombbal a folyamatkiszolgáló jelenleg használatban van, és kattintson a **kapcsoló**.

    ![Képernyőfelvétel: a konfigurációs kiszolgálóhoz párbeszédpanel](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. A **válassza folyamat-célkiszolgáló**, válassza ki a használni kívánt új folyamatkiszolgáló, és válassza ki a virtuális gépek, amelyek a kiszolgáló fogja kezelni. Kattintson az információ ikonra a kiszolgáló adatainak beolvasása. Hogy a döntések betöltése érdekében van szükség minden egyes kiválasztott virtuális gép replikálása az új folyamatkiszolgáló átlagos terület jelenik meg. Kattintson a pipa jelre az új folyamatkiszolgáló való replikálásának megkezdéséhez.

## <a name="deploy-additional-master-target-servers"></a>Fő célkiszolgáló további kiszolgálók üzembe helyezése

További fő célkiszolgáló során a következő esetekben szüksége lesz

1. Ha egy Linux-alapú virtuális gép védelmét.
2. Ha a fő célkiszolgáló elérhető-e a konfigurációs kiszolgálón nem fér hozzá a virtuális gép lemezei.
3. Ha a fő lemezek teljes száma a célkiszolgáló (nem. a kiszolgálón helyi lemezek) és a védeni kívánt lemezek száma meghaladja a 60 lemezeket.

Adjon hozzá egy új fő célkiszolgálót a **Linux-alapú virtuális gép**, [ide](vmware-azure-install-linux-master-target.md).

A **Windows-alapú virtuális gép**, hajtsa végre az alább megadott utasításokat.

1. Navigáljon a **Recovery Services-tároló** > **Site Recovery-infrastruktúra** > **konfigurációs kiszolgálók**.
2. Kattintson a szükséges konfigurációs kiszolgáló > **+ a fő célkiszolgáló**.![ Adjon hozzá-master-cél-server.png](media/site-recovery-plan-capacity-vmware/add-master-target-server.png)
3. Töltse le az egységes beállítás, és futtassa a fő célkiszolgáló beállítása a virtuális gépen.
4. Válasszon **telepítés fő célkiszolgáló** > **tovább**. ![Válasszon MT.PNG](media/site-recovery-plan-capacity-vmware/choose-MT.PNG)
5. Válassza ki az alapértelmezett telepítési hely > kattintson **telepítése**. ![Fő Célkiszolgáló telepítése](media/site-recovery-plan-capacity-vmware/MT-installation.PNG)
6. Kattintson a **lépjen tovább a konfigurációs** fő célkiszolgáló regisztrálni a konfigurációs kiszolgáló. ![Fő Célkiszolgáló folytatható configuration.PNG](media/site-recovery-plan-capacity-vmware/MT-proceed-configuration.PNG)
7. Adja meg a konfigurációs kiszolgáló és a hozzáférési kódot IP-címét. [Kattintson ide a](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) megtudhatja, hogyan hozhat létre hozzáférési kódot.![ cs-ip-jelszó](media/site-recovery-plan-capacity-vmware/cs-ip-passphrase.PNG)
8. Kattintson a **regisztrálása** , és kattintson a hozzászólás regisztrációs **Befejezés**.
9. Sikeres regisztráció esetén a kiszolgáló megjelenik-e a portál **Recovery Services-tároló** > **Site Recovery-infrastruktúra** > **konfiguráció kiszolgálók** > fő vonatkozó konfigurációs kiszolgáló.

 >[!NOTE]
 >Is letöltheti a legújabb verzióra a fő cél kiszolgáló egységes beállítás a Windows [Itt](https://aka.ms/latestmobsvc).

## <a name="next-steps"></a>További lépések

Töltse le és futtassa a [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner)
