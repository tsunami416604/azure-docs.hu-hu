---
title: Kapacitás megtervezése és skálázása az Azure-bA az Azure Site Recovery VMware-vészhelyreállítás |} A Microsoft Docs
description: Ez a cikk a kapacitás megtervezése és a méretezési csoport használja, ha beállítása VMware virtuális gépek vészhelyreállítása az Azure-bA az Azure Site Recoveryvel
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.date: 12/12/2018
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 6f644416a9e56009aadd0f8e1b217402d625af84
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53788735"
---
# <a name="plan-capacity-and-scaling-for-vmware-disaster-recovery-to-azure"></a>Kapacitás és méretezés az Azure-bA VMware vész-helyreállítási terv

Ez a cikk segítségével azonosítani a kapacitástervezés és skálázás, ha a helyszíni VMware virtuális gépek és fizikai kiszolgálók replikálása az Azure-bA [Azure Site Recovery](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Hogyan kezdhetem meg, hogy kapacitástervezés?

Tudnivalók az Azure Site Recovery-infrastruktúra követelményei, a replikálási környezetre vonatkozó információk futtatásával gyűjtsön a [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner-doc) a VMware replikálásához. [További információ](site-recovery-deployment-planner.md) az eszközről. Ez az eszköz kompatibilis és nem kompatibilis virtuális gépek, lemezek, virtuális gépenként Ez a jelentés teljes körű információkat, és az adatváltozás lemezenként. Az eszköz emellett összegzi a cél helyreállítási Időkorlát és a sikeres replikálás és feladatátvételi teszthez szükséges Azure-infrastruktúra sávszélességre van szükség.

## <a name="capacity-considerations"></a>A kapacitás szempontok

**Összetevő** | **Részletek** |
--- | --- | ---
**Replikáció** | **Maximális napi változási:** A védett gép csak egy folyamatkiszolgálót, és a egy egyetlen folyamatkiszolgáló képes kezelni a napi módosítási gyakorisága 2 TB-ig. 2 TB-os így, a maximális napi adatváltozási gyakoriság, amely a védett gép támogatott.<br/><br/> **Maximális átviteli sebesség:** Egy storage-fiókot az Azure-ban a replikált gépek is tartozhatnak. A standard szintű tárfiók képes kezelni másodpercenként 20 000 kérelem legfeljebb, és azt javasoljuk, hogy őrizze meg a bemeneti/kimeneti műveletek száma másodpercenként (IOPS) számát a forrásgép között 20 000. Például ha a forrásgép 5 lemezekkel rendelkezik, és az egyes lemezek állít elő, 120 IOPS (8 KB a mérete) a forrásgépen, majd lesz a lemez IOPS-korlát az 500-as Azure-ban. (A szükséges tárfiókok száma osztva a 20 000 teljes forrásgépen IOPS, megegyezik.)
**Konfigurációs kiszolgáló** | A konfigurációs kiszolgáló tudja kezelni a napi módosítási aránya kapacitást az összes védett gépeken futó számítási feladatok között kell lennie, és ha folyamatosan szeretné replikálni adatokat az Azure Storage elegendő sávszélességet kell.<br/><br/> Ajánlott eljárásként keresse meg a konfigurációs kiszolgáló az ugyanahhoz a hálózathoz és a LAN-szegmens, a védeni kívánt gépeket. Egy másik hálózaton található, de a védeni kívánt gépek kell rendelkezniük a réteg 3 hálózati rá.<br/><br/> A tábla a következő szakaszban található a konfigurációs kiszolgáló méretezési javaslatokat foglalja össze.
**Folyamatkiszolgáló** | Az első folyamatkiszolgáló a konfigurációs kiszolgálón alapértelmezés szerint telepítve van. A környezet skálázása további folyamatkiszolgálók telepítheti. <br/><br/> A folyamatkiszolgáló replikációs adatokat fogad a védett gépekre, és a gyorsítótárazás, tömörítés és titkosítás segítségével optimalizálja őket. Ezután, elküldi az adatokat az Azure-bA. A folyamat kiszolgáló gépnek rendelkeznie kell a következő feladatok végrehajtására elegendő erőforrással.<br/><br/> A folyamatkiszolgáló a lemezalapú gyorsítótárban használja. 600 GB vagy több külön gyorsítótár lemez használata esetén a hálózati szűk keresztmetszet, illetve leállás tárolt adatok változásának kezelésére.

## <a name="size-recommendations-for-the-configuration-serverin-built-process-server"></a>A konfigurációs kiszolgáló és a beépített folyamatkiszolgáló vonatkozó javaslatok a méretekkel kapcsolatban

Minden egyes konfigurációs kiszolgáló helyezzük [OVF-sablon](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template) beépített folyamatkiszolgálóval rendelkező. Erőforrások, például a Processzor, a memória, a szabad terület a konfigurációs kiszolgáló különböző ütemben használhatók, ha a virtuális gépek védelmét a beépített folyamatkiszolgáló tételnél jelennek. Ezért a követelmények eltérőek lehetnek, ha a beépített folyamatkiszolgáló fel van.
Ha a beépített folyamatkiszolgáló számítási feladatok védelmére használt konfigurációs kiszolgáló legfeljebb 200 olyan virtuális gépet, a következő konfigurációk alapján képes kezelni.

**CPU** | **Memória** | **Gyorsítótár-lemez mérete** | **A módosult adatok aránya** | **Védett gépek**
--- | --- | --- | --- | ---
8 Vcpu (2 sockets * 4 mag \@ 2,5 gigahertz [GHz-es]) | 16 GB | 300 GB | 500 GB vagy kevesebb | 100-nál kevesebb gépeket replikálni.
12 vcpu-k (2 sockets * 6 magok \@ 2,5 GHz-es) | 18 GB | 600 GB | 500 GB – 1 TB | 100 – 150 gépek között replikálja.
16 vcpu-k (2 sockets * 8 magos \@ 2,5 GHz-es) | 32 GB | 1 TB | 1 TB-os 2 TB-ig | 150-200 gépek között replikálja.
Keresztül egy másik konfigurációs kiszolgáló telepítése [OVF-sablon](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template) | | | | Helyezzen üzembe új konfigurációs kiszolgálót, ha több mint 200 olyan gépet replikál.
Üzembe helyezése egy másik [folyamatkiszolgáló](vmware-azure-set-up-process-server-scale.md#download-installation-file) | | | &GT; 2 TB-OS| Új horizontális felskálázási folyamatkiszolgáló üzembe helyezése, ha a teljes napi adatváltozási sebesség nagyobb, mint 2 TB-os.

Az elemek magyarázata:

* Minden egyes forrásgép egyenként 100 GB-os 3 lemezzel van konfigurálva.
* Teljesítménymérési tárolási, 8, 10 k RPM, SAS-meghajtót a RAID 10-ben, gyorsítótár lemez mértékegysége használtuk.

## <a name="size-recommendations-for-the-configuration-server"></a>A konfigurációs kiszolgáló méretezési javaslatokat

Ha nem tervezi egy folyamat kiszolgálóként a konfigurációs kiszolgálót használni, kövesse az alábbi konfigurációban legfeljebb 650 virtuális gépek kezeléséhez.

**CPU** | **RAM-MAL** | **Operációsrendszer-lemez mérete** | **A módosult adatok aránya** | **Védett gépek**
--- | --- | --- | --- | ---
24 vcpu-k (2 sockets * 12 magok \@ 2,5 gigahertz [GHz-es])| 32GB | 80 GB | Nem alkalmazható | Legfeljebb 650 virtuális gépet

Minden egyes forrásgép, ahol egyenként 100 GB-os 3 lemezzel van konfigurálva.

Óta folyamat kiszolgáló funkció nem használható, adatváltozási sebesség nem alkalmazható. A fenti kapacitás fenntartása válthat a számítási feladatok beépített folyamatkiszolgálóról horizontális felskálázás egy másik folyamat az alábbi az irányelveket [Itt](vmware-azure-manage-process-server.md#balance-the-load-on-process-server).

## <a name="size-recommendations-for-the-process-server"></a>A folyamatkiszolgáló vonatkozó javaslatok a méretekkel kapcsolatban

Folyamat kiszolgáló az a komponens, amely az Azure Site Recovery a replikációs folyamat kezeli. Ha a napi adatváltozási sebesség 2 TB-nál nagyobb, hozzá kell egy horizontális felskálázási folyamatkiszolgáló replikációs terhelés kezelésére. A horizontális felskálázáshoz a következőket teheti:

* Az üzembe helyezést a konfigurációs kiszolgálók számának növelésére [OVF-sablon](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template). Ha például védheti akár 400 gépet két konfigurációs kiszolgálóval.
* Adjon hozzá [horizontális felskálázási folyamatkiszolgáló](vmware-azure-set-up-process-server-scale.md#download-installation-file), és ezek helyett (vagy mellett) a replikációs forgalom kezelésére használja a konfigurációs kiszolgáló.

A következő táblázat ismerteti a forgatókönyv, amelyben:

* Horizontális felskálázási folyamatkiszolgáló beállítását.
* Védett virtuális gépek a horizontális felskálázási folyamatkiszolgáló használatára konfigurálta.
* Minden egyes védett forrásgép egyenként 100 GB-os három lemez van konfigurálva.

**További folyamatkiszolgáló** | **Gyorsítótár-lemez mérete** | **A módosult adatok aránya** | **Védett gépek**
--- | --- | --- | ---
4 vcpu-k (2 sockets * 2 mag \@ 2,5 GHz-es), 8 GB memória | 300 GB | 250 GB vagy kevesebb | 85 vagy kevesebb gépeket replikálni.
8 Vcpu (2 sockets * 4 mag \@ 2,5 GHz-es), 12 GB memória | 600 GB | 250 GB – 1 TB | Replikálja a 85-150 gépek között.
12 vcpu-k (2 sockets * 6 magok \@ 2,5 GHz-es) 24 GB memória | 1 TB | 1 TB-os 2 TB-ig | 150-225 gépek között replikálja.

A kiszolgálók, skálázza módja függ a vertikális vagy horizontális felskálázás modellhez tartozó beállításokat.  Néhány csúcskategóriás konfigurációs és folyamatkiszolgálók üzembe helyezésével vertikális vagy horizontális felskálázás kevesebb erőforrást a további kiszolgálók üzembe helyezésével. Például ha szüksége a napi 1,5 TB-os, átfogó adatváltozási sebesség 200 gépet véd, sikerült tegye a következők egyikét:

* Állítsa be a 16 vCPU, 24 GB RAM-MAL folyamat egyetlen kiszolgálót.
* Állítsa be két folyamatkiszolgálók (2 x 8 vCPU, 2 * 12 GB RAM).

## <a name="control-network-bandwidth"></a>Hálózati sávszélesség szabályozására

Miután már használta a [a Deployment Planner eszköz](site-recovery-deployment-planner.md) kiszámításához szükséges (a kezdeti replikáció és a majd különbözeti) replikációs sávszélesség, szabályozhatja a használatával több lehetőség közül választhat replikálásához használt sávszélesség mennyiségét:

* **A sávszélesség szabályozását**: Az Azure-bA a VMware-forgalom halad végig egy adott folyamatkiszolgáló. Beállíthatja a sávszélesség a folyamatkiszolgáló futtató gépek szabályozását.
* **Befolyásolhatja a sávszélesség**: A sávszélesség-replikációhoz használt néhány beállításkulcsok használatával is szabályozhatja:
  * A **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** beállításazonosítót a lemezen adatátvitelre (kezdeti vagy változásreplikálásra) használt szálak számát adja meg. Minél magasabb a beállítás értéke, annál nagyobb hálózati sávszélességet használ a rendszer a replikáláshoz.
  * A **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** számát határozza meg az adatátviteli feladat-visszavétel során használt szálak számát.

### <a name="throttle-bandwidth"></a>Sávszélesség szabályozása

1. Nyissa meg az Azure Backup szolgáltatás MMC beépülő modul a gépen, és a folyamatkiszolgáló. Alapértelmezés szerint a helyi biztonsági másolat érhető el az asztalon vagy a következő mappában: C:\Program Files\Microsoft Azure Recovery Services Agent\bin.
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

## <a name="setup-azure-site-recovery-infrastructure-to-protect-more-than-500-virtual-machines"></a>500-nál több virtuális gép védelméhez az Azure Site Recovery-infrastruktúra beállítása

Az Azure Site Recovery-infrastruktúra beállítása előtt hozzá kell férnie a környezet az alábbi tényezők mérésére: kompatibilis virtuális gépek, a napi adatok megváltoztatása arány, a szükséges hálózati sávszélesség a kívánt rpo-t, az Azure site recovery száma szükséges összetevők, ideje befejezni a kezdeti replikáció stb.,

1. Azoknak az ezeket a paramétereket, ügyeljen arra, hogy az adott környezet megosztott irányelvek segítségével a deployment planner futtatása [Itt](site-recovery-deployment-planner.md).
2. Konfigurációs kiszolgáló üzembe helyezése az említett követelményeinek [Itt](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server). Ha az éles számítási feladatok nagyobb, mint 650 virtuális gépek, egy másik konfigurációs kiszolgáló telepítése.
3. Mért napi adatváltozási sebessége alapján, üzembe helyezése [horizontális felskálázási folyamatkiszolgáló](vmware-azure-set-up-process-server-scale.md#download-installation-file) conditions stated above mérete irányelvek segítségével [Itt](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).
4. Ha az adatváltozási sebessége a lemez virtuális gépek túllépné a 2 MB/s, ügyeljen arra, hogy [premium storage-fiók beállítása](tutorial-prepare-azure.md#create-a-storage-account). Deployment planner futtatása egy adott időtartamra, mivel az adatok maximális szám más időszakban, előfordulhat, hogy a jelentés nem rögzített időszakok akkor változik.
5. Az RPO megfelelően [állítsa be a hálózati sávszélesség](site-recovery-plan-capacity-vmware.md#control-network-bandwidth).
6. Az infrastruktúra beállítása után kövesse a közzétett [útmutató témakörei](vmware-azure-set-up-source.md) vész-helyreállítási a számítási feladatainak engedélyezéséhez.

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
