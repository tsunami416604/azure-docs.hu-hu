---
title: Kapacitás megtervezése és VMware-vészhelyreállításhoz az Azure-bA az Azure Site Recovery használatával skálázása |} A Microsoft Docs
description: Ez a cikk segíthet megtervezése a kapacitás és méretezés beállításakor VMware virtuális gépek vészhelyreállítása az Azure-bA az Azure Site Recovery használatával.
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.date: 4/9/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: c198e6cd9d5c5e0aca69491db9df5d0ab8e08c7a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59358011"
---
# <a name="plan-capacity-and-scaling-for-vmware-disaster-recovery-to-azure"></a>Kapacitás és méretezés az Azure-bA VMware vész-helyreállítási terv

Ez a cikk segítségével tervezze meg a kapacitás és a méretezés során, a helyszíni VMware virtuális gépek és fizikai kiszolgálók replikálása az Azure használatával [Azure Site Recovery](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Hogyan kezdhetem meg, hogy kapacitástervezés?

További információ az Azure Site Recovery-infrastruktúra követelményei, a replikálási környezetre vonatkozó információkat gyűjthet futtatásával [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner-doc) a VMware replikálásához. További információkért lásd: [kapcsolatos Site Recovery Deployment Planner VMware – Azure](site-recovery-deployment-planner.md). 

A Site Recovery Deployment Planner egy jelentést, amely kompatibilis és nem kompatibilis virtuális gépek, virtuális lemezek teljes körű információt biztosít, és az adatváltozás lemezenként. Az eszköz emellett összegzi a cél helyreállítási Időkorlát és az Azure-infrastruktúra, a sikeres replikálás és feladatátvételi teszthez szükséges sávszélességre van szükség.

## <a name="capacity-considerations"></a>A kapacitás szempontok

Összetevő | Részletek
--- | ---
**Replikáció** | **Maximális napi adatváltozási sebesség**: A védett gép csak egy folyamatkiszolgáló használhatja. A folyamat egyetlen kiszolgáló képes kezelni a napi módosítási gyakorisága 2 TB-ig. 2 TB-os így, a maximális napi adatváltozási gyakoriság, amely a védett gép támogatott.<br /><br /> **Maximális átviteli sebesség**: Egy storage-fiókot az Azure-ban a replikált gépek is tartozhatnak. Egy standard szintű Azure Storage-fiókot képes kezelni másodpercenként 20 000 kérelem legfeljebb. Azt javasoljuk, hogy a forrásgép 20 000 között bemeneti/kimeneti műveletek száma másodpercenként (IOPS) számának korlátozásához. Például ha a forrásgépen, amely öt lemezzel rendelkezik, és minden lemezhez a forrásgépen 120 IOPS (8 KB méretű) hoz létre, a forrásgép belül van az 500-as Azure lemezenkénti IOPS-korlátját. (A szükséges tárfiókok száma nem egyenlő a teljes forrásgép elosztja a 20 000 IOPS.)
**Konfigurációs kiszolgáló** | A konfigurációs kiszolgáló tudja kezelni a napi módosítási aránya kapacitást az összes védett gépeken futó számítási feladatok között kell lennie. A konfigurációs gép elegendő sávszélesség, és folyamatosan replikálja az adatokat az Azure Storage kell rendelkeznie.<br /><br /> Ajánlott eljárás, hogy a konfigurációs kiszolgáló az ugyanazon a hálózaton és a LAN-szegmens, a védeni kívánt gépeket. A konfigurációs kiszolgáló elhelyezhet egy másik hálózaton, de a védeni kívánt gépekre kell rendelkezniük a réteg 3 hálózati.<br /><br /> A tábla a következő szakaszban található a konfigurációs kiszolgáló méretezési javaslatokat foglalja össze.
**Folyamatkiszolgáló** | Az első folyamatkiszolgáló a konfigurációs kiszolgálón alapértelmezés szerint telepítve van. A környezet skálázása további folyamatkiszolgálók telepítheti. <br /><br /> A folyamatkiszolgáló replikációs adatokat fogad a védett gépek. A folyamatkiszolgáló gyorsítótárazás, tömörítés és titkosítás segítségével optimalizálja az adatokat. Ezt követően a folyamatkiszolgáló elküldi az adatokat az Azure. A folyamat kiszolgáló gép elegendő erőforrás a következő feladatok végrehajtására kell rendelkeznie.<br /><br /> A folyamatkiszolgáló a lemezalapú gyorsítótárban használja. 600 GB vagy több külön gyorsítótár lemez használata a hálózati szűk keresztmetszet, illetve leállás esetén tárolt adatok változásának kezelésére.

## <a name="size-recommendations-for-the-configuration-server-and-inbuilt-process-server"></a>A folyamatkiszolgáló a konfigurációs kiszolgáló és a beépített javaslatok

A konfigurációs kiszolgáló, amely a beépített folyamatkiszolgálót használja a számítási feladatok védelmét tudják kezelni akár 200 olyan virtuális gépet, a következő konfigurációk alapján:

CPU | Memory (Memória) | Gyorsítótár-lemez mérete | A módosult adatok aránya | Védett gépek
--- | --- | --- | --- | ---
8 Vcpu (2 sockets * 4 mag \@ 2,5 GHz-es) | 16 GB | 300 GB | 500 GB vagy kevesebb | Használatával a 100-nál kevesebb gépeket replikálni.
12 vcpu-k (2 sockets * 6 magok \@ 2,5 GHz-es) | 18 GB | 600 GB | 501 GB – 1 TB | Használatával a 100-150 gépeket replikálni.
16 vcpu-k (2 sockets * 8 magos \@ 2,5 GHz-es) | 32 GB | 1 TB | > 1 TB-os 2 TB-ig | Használatával 151 és 200 gépeket replikálni.
Egy másik konfigurációs kiszolgáló telepítése használatával egy [OVF-sablon](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template). | | | | Helyezzen üzembe egy új konfigurációs kiszolgálót, ha több mint 200 olyan gépet replikál.
Üzembe helyezése egy másik [folyamatkiszolgáló](vmware-azure-set-up-process-server-scale.md#download-installation-file). | | | >2 TB| Új horizontális felskálázási folyamatkiszolgáló üzembe helyezése, 2 TB-nál nagyobb összesített napi adatváltozási sebesség esetén.

Az alábbi konfigurációk:

* Minden egyes forrásgép egyenként 100 GB-os három lemezzel rendelkezik.
* A nyolc közös hozzáférésű jogosultságkód meghajtók 10 k RPM teljesítménymérési tárolási RAID 10-zel, gyorsítótár lemez mértékegysége használtuk.

## <a name="size-recommendations-for-the-process-server"></a>A folyamatkiszolgáló vonatkozó javaslatok a méretekkel kapcsolatban

A folyamatkiszolgáló az a komponens, amely kezeli az adatok replikálása az Azure Site Recovery. Ha a napi adatváltozási sebesség 2 TB-nál nagyobb, hozzá kell adnia a horizontális felskálázási folyamatkiszolgáló replikációs terhelés kezelésére. A horizontális felskálázáshoz a következőket teheti:

* Konfigurációs kiszolgálók számának növelésére használatával üzembe helyezésével egy [OVF-sablon](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template). Ha például akár 400 gépet is védhet két konfigurációs kiszolgálók használatával.
* Adjon hozzá [horizontális felskálázási folyamatkiszolgáló](vmware-azure-set-up-process-server-scale.md#download-installation-file). A horizontális felskálázási folyamatkiszolgáló használata helyett (vagy mellett) a replikációs forgalom kezelésére a konfigurációs kiszolgálón.

Az alábbi táblázat ismerteti ezt a forgatókönyvet:

* Horizontális felskálázási folyamatkiszolgáló beállítása.
* Védett virtuális gépek a horizontális felskálázási folyamatkiszolgáló használatára konfigurálta.
* Minden egyes védett forrásgép egyenként 100 GB-os három lemezzel rendelkezik.

További folyamatkiszolgáló | Gyorsítótár-lemez mérete | A módosult adatok aránya | Védett gépek
--- | --- | --- | ---
4 vcpu-k (2 sockets * 2 mag \@ 2,5 GHz-es), 8 GB memóriát | 300 GB | 250 GB vagy kevesebb | Használatával a 85 vagy kevesebb gépeket replikálni.
8 Vcpu (2 sockets * 4 mag \@ 2,5 GHz-es), 12 GB memória | 600 GB | 1 TB-os 251 GB | Használatával 86-150 gépeket replikálni.
12 vcpu-k (2 sockets * 6 magok \@ 2,5 GHz-es) 24 GB memória | 1 TB | > 1 TB-os 2 TB-ig | Használatával a 225 151 gépeket replikálni.

Hogyan méretezhető, hogy a kiszolgálók attól függ, hogy a vertikális vagy horizontális felskálázás modellhez tartozó beállításokat. Vertikális felskálázásához néhány csúcskategóriás konfigurációs kiszolgálók üzembe helyezésére, és a kiszolgálók feldolgozni. A horizontális felskálázáshoz helyezzen üzembe további kiszolgálókat, amelyek kevesebb erőforrást. Például ha 200 gépek védelméhez számára átfogó napi adatváltozási gyakoriság 1,5 TB-os, pillanatképet is az alábbi műveletek egyikét:

* Állítsa be a folyamat egyetlen kiszolgáló (16 vCPU, 24 GB RAM).
* Állítsa be két folyamatkiszolgálók (2 x 8 vCPU, 2 * 12 GB RAM).

## <a name="control-network-bandwidth"></a>Hálózati sávszélesség szabályozására

Használata után [Site Recovery Deployment Planner](site-recovery-deployment-planner.md) alapján számítja ki a sávszélességet (kezdeti replikációt, majd a különbözeti) replikációs van szüksége, van néhány használt sávszélesség szabályozásának lehetőségei replikáció:

* **A sávszélesség szabályozását**: Az Azure-bA a VMware-forgalom halad végig egy adott folyamatkiszolgáló. Beállíthatja a gépeken, futtatja a folyamatkiszolgálók, sávszélesség szabályozását.
* **Befolyásolhatja a sávszélesség**: A replikáció beállításkulcsok több által használt sávszélesség befolyásolhatja:
  * A **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** beállításazonosítót a lemezen adatátvitelre (kezdeti vagy változásreplikálásra) használt szálak számát adja meg. A magasabb értékek növeli a replikációhoz használt hálózati sávszélességet.
  * A **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** beállításjegyzékbeli érték határozza meg, az adatátviteli feladat-visszavétel során használt szálak számát.

### <a name="throttle-bandwidth"></a>Sávszélesség szabályozása

1. Nyissa meg az Azure Backup szolgáltatás MMC beépülő modul a gépen a folyamatkiszolgálót használja. Alapértelmezés szerint a helyi biztonsági másolat érhető el az asztalon vagy a következő mappában: C:\Program Files\Microsoft Azure Recovery Services Agent\bin.
2. A beépülő modulban, válassza ki a **tulajdonságainak módosítása**.

    ![Képernyőkép az Azure Backup szolgáltatás MMC beépülő modul lehetőséget tulajdonságainak módosítása](./media/site-recovery-vmware-to-azure/throttle1.png)
3. Az a **sávszélesség-szabályozási** lapon jelölje be **szabályozása a biztonsági mentési műveletek internetes sávszélességének**. Állítsa be a munkaórákra és a munkaórákon kívüli. 512 KB/s a 1,023 Mbps érvényes tartományok származnak.

    ![Az Azure Backup tulajdonságai párbeszédpanel képernyőképe](./media/site-recovery-vmware-to-azure/throttle2.png)

A szabályozáshoz a [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) parancsmag is használható. Például:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

A **Set-OBMachineSetting -NoThrottle** beállítás azt jelenti, hogy nincs szükség szabályozásra.

### <a name="alter-the-network-bandwidth-for-a-vm"></a>A hálózati sávszélesség ALTER egy virtuális géphez

1. Lépjen a virtuális gép beállításjegyzékben **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * A sávszélesség forgalmát egy replikáló lemez az megváltoztatására, módosítsa az értékét **UploadThreadsPerVM**. Hozzon létre a kulcsot, ha még nem létezik.
   * A feladat-visszavétel forgalmat az Azure-ból a sávszélesség módosítását, módosítsa az értékét **DownloadThreadsPerVM**.
2. Az alapértelmezett érték a kulcsok minden **4**. A szükségesnél több erőforrással ellátott hálózatban érdemes módosítani a beállításazonosítók alapértelmezett értékét. A maximális érték használható **32**. Az optimális érték kiválasztásához kövesse figyelemmel a forgalmat.

## <a name="set-up-the-site-recovery-infrastructure-to-protect-more-than-500-vms"></a>Több mint 500 virtuális gépek védelme érdekében a Site Recovery-infrastruktúra beállítása

A Site Recovery-infrastruktúra beállítása előtt férhet hozzá a környezethez, és mérhető, a következő tényezőket: kompatibilis virtuális gépek, a napi adatváltozási gyakoriság, a szükséges hálózati sávszélesség a rpo elérése érdekében a Site Recovery számát szeretné szükséges összetevők, és a kezdeti replikálás befejezéséhez szükséges időt. A következő lépéseket a szükséges adatokat:

1. Azoknak az ezeket a paramétereket, futtassa a Site Recovery Deployment Planner a környezetben. További praktikus útmutatók: [kapcsolatos Site Recovery Deployment Planner VMware – Azure](site-recovery-deployment-planner.md).
2. Konfigurációs kiszolgáló üzembe helyezése, amely megfelel a [a konfigurációs kiszolgáló méretével](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server). Ha az éles számítási feladatok nagyobb, mint 650 virtuális gépek, egy másik konfigurációs kiszolgáló telepítése.
3. Mért napi adatváltozási sebessége alapján, üzembe helyezése [horizontális felskálázási folyamatkiszolgáló](vmware-azure-set-up-process-server-scale.md#download-installation-file) segítségével [irányelvek méretezés](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).
4. Ha az adatváltozási sebessége meghaladja a 2 MB/s lemez virtuális géphez, győződjön meg arról, hogy prémium szintű felügyelt lemezek használata. Site Recovery Deployment Planner futtatása egy adott időszakban. Egyéb időszakokban adatváltozási sebesség csúcsok se zachytit előfordulhat, hogy a jelentésben.
5. [Állítsa be a hálózati sávszélesség](site-recovery-plan-capacity-vmware.md#control-network-bandwidth) alapján az elérni kívánt rpo-t.
6. Amikor az infrastruktúra beállításával, a számítási feladatok vészhelyreállítása engedélyezése. További információ [a forráskörnyezet beállítása VMware-ről az Azure-bA](vmware-azure-set-up-source.md).

## <a name="deploy-additional-process-servers"></a>További folyamatkiszolgálók üzembe helyezése

Ha a központi telepítés meghaladja a 200 forrásgépek horizontális felskálázása, vagy ha több mint 2 TB-os napi adatváltozásának összesen rendelkezik, hozzá kell adnia a folyamatkiszolgálók kezelése a forgalom mennyisége. Ismerje meg, hogyan állítható be a folyamatkiszolgáló, lásd: [feladat-visszavétel további folyamatkiszolgálók használatával nagy számú](vmware-azure-set-up-process-server-scale.md). Miután beállította a folyamatkiszolgáló, áttelepítheti forrásgépek használhatja azt.

### <a name="migrate-machines-to-use-the-new-process-server"></a>Az új folyamatkiszolgáló használandó gépek migrálása

1. Válassza ki **beállítások** > **Site Recovery kiszolgálók**. Válassza ki a konfigurációs kiszolgálót, majd bontsa ki a **kiszolgálók feldolgozni**.

    ![A Folyamatkiszolgáló párbeszédpanel képernyőképe](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Kattintson a jobb gombbal a folyamatkiszolgáló jelenleg használatban van, és válassza **kapcsoló**.

    ![A konfigurációs kiszolgáló párbeszédpanel képernyőképe](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. A **válassza folyamat-célkiszolgáló**, válassza ki a használni kívánt új folyamatkiszolgáló. Ezután válassza ki a virtuális gépek, amelyek a kiszolgáló fogja kezelni. A kiszolgáló adatainak beolvasása, válassza ki az információ ikonra. Hogy segítségükkel döntések betöltése, a szükséges minden egyes kiválasztott virtuális gép replikálása az új folyamatkiszolgáló átlagos lemezterület jelenik meg. Válassza ki a pipa jelre az új folyamatkiszolgáló való replikálásának megkezdéséhez.

## <a name="deploy-additional-master-target-servers"></a>Fő célkiszolgáló további kiszolgálók üzembe helyezése

A következő esetekben egynél több fő célkiszolgáló szükség:

*   Szeretne egy Linux-alapú virtuális gép védelme.
*   A fő célkiszolgáló elérhető-e a konfigurációs kiszolgálón nem rendelkezik hozzáféréssel a virtuális gép lemezei.
*   A fő célkiszolgáló (a kiszolgálón helyi lemezek száma), valamint a védeni kívánt lemezek számát a lemezek teljes száma nagyobb, mint 60 lemezek.

A fő célkiszolgáló egy Linux-alapú virtuális gép hozzáadása kapcsolatban lásd: [telepítése egy Linux rendszerű fő célkiszolgálót a feladat-visszavételhez](vmware-azure-install-linux-master-target.md).

A fő célkiszolgáló Windows-alapú virtuális gép hozzáadása:

1. Lépjen a **Recovery Services-tároló** > **Site Recovery-infrastruktúra** > **konfigurációs kiszolgálók**.
2. A szükséges konfigurációs kiszolgálót, majd válassza ki és **fő célkiszolgáló**.

    ![Képernyőkép a fő célkiszolgáló hozzáadása gomb](media/site-recovery-plan-capacity-vmware/add-master-target-server.png)
3. Töltse le az egyesített telepítő fájlját, és futtassa a fájlt a fő célkiszolgáló beállítása a virtuális gépen.
4. Válassza ki **telepítés fő célkiszolgáló** > **tovább**.

    ![A telepítés a fő célkiszolgáló beállítás képernyőképe](media/site-recovery-plan-capacity-vmware/choose-MT.PNG)
5. Az alapértelmezett telepítési hely, majd válassza ki és **telepítése**.

     ![Az alapértelmezett telepítési hely bemutató képernyőkép](media/site-recovery-plan-capacity-vmware/MT-installation.PNG)
6. Válassza ki a fő célkiszolgáló regisztrálni a konfigurációs kiszolgáló, **folytassa a konfigurációs**.

    ![Képernyőkép a továbbléphet a konfiguráció gomb](media/site-recovery-plan-capacity-vmware/MT-proceed-configuration.PNG)
7. Adja meg a konfigurációs kiszolgáló IP-címét, és adja meg a jelszót. Ismerje meg, hogyan hozhat létre egy hozzáférési kódot, lásd: [hozzon létre egy konfigurációs kiszolgáló jelszavát](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase). 

    ![Képernyőkép a helyét adja meg a konfigurációs kiszolgáló IP-cím és jelszó](media/site-recovery-plan-capacity-vmware/cs-ip-passphrase.PNG)
8. Kattintson a **Register** (Regisztrálás) elemre. Amikor befejeződött a regisztráció, válassza ki a **Befejezés**.

Ha a regisztráció sikeres befejezését követően a kiszolgáló megjelenik-e az Azure Portalon, **Recovery Services-tároló** > **Site Recovery-infrastruktúra**  >   **Konfigurációs kiszolgálók**, a fő célkiszolgálókat a konfigurációs kiszolgáló található.

 > [!NOTE]
 > Töltse le a legújabb verzióját a [fő fogadó kiszolgáló egyesített telepítő fájl a Windows](https://aka.ms/latestmobsvc).

## <a name="next-steps"></a>További lépések

Töltse le és futtassa [Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner).
