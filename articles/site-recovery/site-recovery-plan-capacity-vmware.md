---
title: Tervezze meg a VMware vész-helyreállítási kapacitását az Azure Site Recovery segítségével
description: Ez a cikk segítséget nyújt a kapacitás és a méretezés megtervezésében, amikor az Azure Site Recovery használatával beállítja a VMware virtuális gépek vészhelyreállítását az Azure-ba.
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.date: 4/9/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 467c70a722b8a243be6ac2826188a4ba3459aa06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257614"
---
# <a name="plan-capacity-and-scaling-for-vmware-disaster-recovery-to-azure"></a>Kapacitás és méretezés tervezése a VMware vész-helyreállítási azure-ba

Ebben a cikkben megtervezheti a kapacitást és a méretezést, amikor az Azure Site Recovery használatával replikálja a helyszíni VMware virtuális gépeket és fizikai kiszolgálókat az [Azure-ba.](site-recovery-overview.md)

## <a name="how-do-i-start-capacity-planning"></a>Hogyan kezdhetem el a kapacitástervezést?

Ha többet szeretne megtudni az Azure Site Recovery infrastruktúra követelményeiről, gyűjtsön információkat a replikációs környezetről az [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner-doc) vmware-replikációs futtatásával. További információ: [A Site Recovery Deployment Planner for VMware for Azure](site-recovery-deployment-planner.md)című témakörben talál további információt. 

A Site Recovery Deployment Planner egy jelentést tartalmaz, amely teljes körű információt tartalmaz a kompatibilis és nem kompatibilis virtuális gépekről, a virtuális gépenkénti lemezekről és a lemezenkénti adatforgalomról. Az eszköz a hálózati sávszélesség-követelményeket is összegzi a cél RPO és az Azure-infrastruktúra teljesítéséhez, amely a sikeres replikációhoz és a tesztelési feladatátvételhez szükséges.

## <a name="capacity-considerations"></a>Kapacitással kapcsolatos szempontok

Összetevő | Részletek
--- | ---
**Replikációs** | **Maximális napi változási sebesség**: A védett gépek csak egy folyamatkiszolgálót használhatnak. Egy folyamatkiszolgáló legfeljebb 2 TB napi változást tud kezelni. Tehát a 2 TB a maximális napi adatváltozási sebesség, amely támogatott egy védett gép esetében.<br /><br /> **Maximális átviteli teljesítmény:** A replikált gép egy tárfiókhoz is tartozhat az Azure-ban. Egy szabványos Azure Storage-fiók másodpercenként legfeljebb 20 000 kérést képes kezelni. Azt javasoljuk, hogy korlátozza a bemeneti/kimeneti műveletek másodpercenkénti száma (IOPS) a forrásgép 20 000.Tjavasoljuk, hogy a bemeneti/kimeneti műveletek száma másodpercenként a forrásgépen 20 000. Például ha egy forrásgép, amely öt lemezt, és minden lemez generál 120 IOPS (8 K méretű) a forrásgépen, a forrásgép az Azure lemezenkénti IOPS-korlát 500. (A szükséges tárfiókok száma megegyezik a teljes forrásgép IOPS osztva 20 000.)
**Konfigurációs kiszolgáló** | A konfigurációs kiszolgálónak képesnek kell lennie a napi változási sebesség kapacitásának kezelésére a védett gépeken futó összes számítási feladatban. A konfigurációs gépnek elegendő sávszélességgel kell rendelkeznie az adatok Azure Storage-ba történő folyamatos replikálásához.<br /><br /> Ajánlott eljárás, hogy a konfigurációs kiszolgálót ugyanarra a hálózatra és LAN-szegmensre helyezi, mint a védeni kívánt gépeket. A konfigurációs kiszolgálót másik hálózatra helyezheti, de a védeni kívánt gépeknek 3.<br /><br /> A konfigurációs kiszolgáló méretre vonatkozó javaslatait a következő szakaszban található táblázat foglalja össze.
**Folyamatkiszolgáló** | Az első folyamatkiszolgáló alapértelmezés szerint telepítve van a konfigurációs kiszolgálón. A környezet méretezéséhez további folyamatkiszolgálókat is telepíthet. <br /><br /> A folyamatkiszolgáló replikációs adatokat fogad a védett gépekről. A folyamatkiszolgáló gyorsítótárazás, tömörítés és titkosítás használatával optimalizálja az adatokat. Ezután a folyamatkiszolgáló elküldi az adatokat az Azure-ba. A folyamatkiszolgáló gépének elegendő erőforrással kell rendelkeznie a feladatok végrehajtásához.<br /><br /> A folyamatkiszolgáló lemezalapú gyorsítótárat használ. A hálózati szűk keresztmetszet vagy kimaradás esetén tárolt adatváltozások kezeléséhez használjon legalább 600 GB-os vagy annál nagyobb különálló gyorsítótárlemezt.

## <a name="size-recommendations-for-the-configuration-server-and-inbuilt-process-server"></a>Méretezési javaslatok a konfigurációs kiszolgálóhoz és a beépített folyamatkiszolgálóhoz

A számítási feladatok védelmére beépített folyamatkiszolgálót használó konfigurációs kiszolgáló legfeljebb 200 virtuális gépet képes kezelni a következő konfigurációk alapján:

CPU | Memory (Memória) | A lemez méretének gyorsítótára | Adatváltozási sebesség | Védett gépek
--- | --- | --- | --- | ---
8 vCPU (2 foglalat * \@ 4 mag 2,5 GHz) | 16 GB | 300 GB | 500 GB vagy kevesebb | 100-nál kevesebb gépet replikál.
12 vCPU (2 foglalat * \@ 6 mag 2,5 GHz) | 18 GB | 600 GB | 501 GB–1 TB | 100 és 150 között 100 gépet replikál.
16 vCPU (2 foglalat * \@ 8 mag 2,5 GHz) | 32 GB | 1 TB | >1 TB és 2 TB között | 151-200 gépet replikál.
Telepítsen egy másik konfigurációs kiszolgálót [OVF sablon](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template)használatával. | | | | Telepítsen egy új konfigurációs kiszolgálót, ha több mint 200 gépet replikál.
Másik [folyamatkiszolgáló](vmware-azure-set-up-process-server-scale.md#download-installation-file)telepítése . | | | >2 TB| Ha a teljes napi adatváltozási sebesség meghaladja a 2 TB-ot, telepítsen egy új horizontális felskálázási folyamatkiszolgálót.

Ezekben a konfigurációkban:

* Minden forrásgép három, egyenként 100 GB-os lemezzel rendelkezik.
* Nyolc 10 K RPM és RAID 10-es megosztott hozzáférésű aláírás-meghajtó teljesítményértékelésének összehasonlító tárolóját használtuk a gyorsítótár lemezének mérésére.

## <a name="size-recommendations-for-the-process-server"></a>A folyamatkiszolgáló méretre vonatkozó javaslatai

A folyamatkiszolgáló az az összetevő, amely kezeli az adatok replikációját az Azure Site Recovery szolgáltatásban. Ha a napi változási sebesség nagyobb, mint 2 TB, a replikációs terhelés kezeléséhez kibővített folyamatkiszolgálókat kell hozzáadnia. A horizontális felskálázáshoz a következőket teheti:

* Növelje a konfigurációs kiszolgálók számát [ovf sablon](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template)használatával történő telepítéssel. Például két konfigurációs kiszolgáló használatával legfeljebb 400 gépet védhet meg.
* [Kibővített folyamatkiszolgálók](vmware-azure-set-up-process-server-scale.md#download-installation-file)hozzáadása . A kibővített folyamatkiszolgálók segítségével a konfigurációs kiszolgáló helyett (vagy mellett) a replikációs forgalmat is kezelni tudja.

Az alábbi táblázat ezt a forgatókönyvet ismerteti:

* Kibővített folyamatkiszolgálót állít be.
* A védett virtuális gépeket a kibővített folyamatkiszolgáló használatára konfigurálta.
* Minden védett forrásgép három, egyenként 100 GB-os lemezzel rendelkezik.

További folyamatkiszolgáló | A lemez méretének gyorsítótára | Adatváltozási sebesség | Védett gépek
--- | --- | --- | ---
4 vCPU (2 foglalat * \@ 2 mag 2,5 GHz), 8 GB memória | 300 GB | 250 GB vagy kevesebb | 85 vagy kevesebb gép replikálására használható.
8 vCPU (2 foglalat * \@ 4 mag 2,5 GHz), 12 GB memória | 600 GB | 251 GB–1 TB | 86-150 gépet replikálhat.
12 vCPU (2 foglalat * \@ 6 mag 2,5 GHz) 24 GB memória | 1 TB | >1 TB és 2 TB között | 151-225 gép replikálására használható.

A kiszolgálók méretezése attól függ, hogy előnyben részesíti-e a horizontális felskálázási vagy horizontális felskálázási modellt. A méretezéshez telepítsen néhány csúcskategóriás konfigurációs kiszolgálót és dolgozza fel a kiszolgálókat. A horizontális felskálázáshoz telepítsen több kevesebb erőforrással rendelkező kiszolgálót. Ha például 200 gépet szeretne védeni 1,5 TB-os összesített napi adatváltozási sebességgel, az alábbi műveletek egyikét teheti meg:

* Egyetlen folyamatkiszolgáló beállítása (16 vCPU, 24 GB RAM).
* Két folyamatkiszolgáló beállítása (2 x 8 vCPU, 2* 12 GB RAM).

## <a name="control-network-bandwidth"></a>A hálózati sávszélesség szabályozása

Miután a [Site Recovery Deployment Planner](site-recovery-deployment-planner.md) segítségével kiszámította a replikációhoz szükséges sávszélességet (kezdeti replikációt, majd a különbözetet), néhány lehetősége van a replikációhoz használt sávszélesség szabályozására:

* **Sávszélesség szabályozása:** Az Azure-ba replikáló VMware-forgalom egy adott folyamatkiszolgálón keresztül történik. A folyamatkiszolgálóként futó gépeksávszélessége szabályozható.
* **Sávszélesség befolyásolása**: Néhány beállításkulcs használatával befolyásolhatja a replikációhoz használt sávszélességet:
  * A **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** beállításjegyzék-érték a lemez adatátviteléhez (kezdeti vagy különbözeti replikációhoz) használt szálak számát adja meg. A nagyobb érték növeli a replikációhoz használt hálózati sávszélességet.
  * A **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** beállításjegyzék-érték a feladat-visszavétel során az adatátvitelhez használt szálak számát adja meg.

### <a name="throttle-bandwidth"></a>Sávszélesség szabályozása

1. Nyissa meg az Azure Backup MMC beépülő modult a folyamatkiszolgálóként használt gépen. Alapértelmezés szerint a biztonsági mentés parancsikonja elérhető az asztalon vagy a következő mappában: C:\Program Files\Microsoft Azure Recovery Services Agent\bin.
2. A beépülő modulban válassza a **Tulajdonságok módosítása**lehetőséget.

    ![Képernyőkép az Azure Backup MMC beépülő modul beállításáról a tulajdonságok módosításához](./media/site-recovery-vmware-to-azure/throttle1.png)
3. A **Szabályozás** lapon válassza az **Internet sávszélesség-használat szabályozásának engedélyezése a biztonsági mentési műveletekhez**lehetőséget. Állítsa be a munkaidő és a nem munkaidő korlátait. Az érvényes tartományok 512 Kb/s és 1023 Mb/s között mozognak.

    ![Képernyőkép az Azure Biztonsági másolat tulajdonságai párbeszédpanelről](./media/site-recovery-vmware-to-azure/throttle2.png)

A szabályozáshoz a [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) parancsmag is használható. Például:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

A **Set-OBMachineSetting -NoThrottle** beállítás azt jelenti, hogy nincs szükség szabályozásra.

### <a name="alter-the-network-bandwidth-for-a-vm"></a>A virtuális gép hálózati sávszélességének módosítása

1. A virtuális gép beállításjegyzékében nyissa meg **a HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication mappát.**
   * A replikálólemez sávszélesség-forgalmának módosításához módosítsa az **UploadThreadsPerVM**értékét. Hozza létre a kulcsot, ha nem létezik.
   * Az Azure-ból érkező feladat-visszavételi forgalom sávszélességének módosításához módosítsa a **DownloadThreadsPerVM**értékét.
2. Az alapértelmezett érték az egyes **kulcsok**4 . A szükségesnél több erőforrással ellátott hálózatban érdemes módosítani a beállításazonosítók alapértelmezett értékét. A maximális érték **32**. Az optimális érték kiválasztásához kövesse figyelemmel a forgalmat.

## <a name="set-up-the-site-recovery-infrastructure-to-protect-more-than-500-vms"></a>A Site Recovery infrastruktúra beállítása több mint 500 virtuális gép védelmére

A Site Recovery infrastruktúra beállítása előtt a következő tényezők mérésére használja a környezetet: kompatibilis virtuális gépek, a napi adatváltozási sebesség, az elérni kívánt RPO szükséges hálózati sávszélessége, a Site Recovery száma szükséges összetevőket, valamint a kezdeti replikáció befejezéséhez szükséges időt. Hajtsa végre a következő lépéseket a szükséges információk összegyűjtéséhez:

1. A paraméterek méréséhez futtassa a Site Recovery Deployment Planner programot a környezetén. Hasznos irányelveket a [VMware webhely-helyreállítási telepítéstervezője az Azure-ba című témakörben talál.](site-recovery-deployment-planner.md)
2. Olyan konfigurációs kiszolgáló telepítése, amely megfelel [a konfigurációs kiszolgáló méretre vonatkozó ajánlásainak.](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) Ha az éles munkaterhelés meghaladja a 650 virtuális gépet, telepítsen egy másik konfigurációs kiszolgálót.
3. A mért napi adatváltozási sebesség alapján telepítsen [kibővített folyamatkiszolgálókat](vmware-azure-set-up-process-server-scale.md#download-installation-file) a [méretre vonatkozó irányelvek](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server)segítségével.
4. Ha a lemez esési sebessége várhatóan meghaladja a 2 Mb/s-ot, győződjön meg arról, hogy prémium szintű felügyelt lemezeket használ. A Webhely-helyreállítási központi telepítés tervezője egy adott időszakra fut. Előfordulhat, hogy máskor az adatváltozási sebesség csúcsai nem lesznek rögzítve a jelentésben.
5. [Állítsa be a hálózati sávszélességet](site-recovery-plan-capacity-vmware.md#control-network-bandwidth) az elérni kívánt RPO alapján.
6. Az infrastruktúra beállítása után engedélyezze a vészhelyreállítást a számítási feladatokhoz. További információ: [A VMware forráskörnyezetének beállítása az Azure replikációhoz](vmware-azure-set-up-source.md)című témakörben olvashat.

## <a name="deploy-additional-process-servers"></a>További folyamatkiszolgálók telepítése

Ha a központi telepítést 200-nál több forrásgépnél bővíti, vagy ha a napi lemorzsolódási arány meghaladja a 2 TB-ot, folyamatkiszolgálókat kell hozzáadnia a forgalom mennyiségének kezeléséhez. A terméket 9.24-es verzióban bővítettük, hogy [a folyamatkiszolgáló figyelmeztetéseket](vmware-physical-azure-monitor-process-server.md#process-server-alerts) biztosítson a kibővített folyamatkiszolgáló beállításáról. [Állítsa be a folyamatkiszolgálót az](vmware-azure-set-up-process-server-scale.md) új forrásgépek védelmére vagy [a terhelés kiegyenlítésére](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load).

### <a name="migrate-machines-to-use-the-new-process-server"></a>Gépek áttelepítése az új folyamatkiszolgáló használatához

1. Válassza a **Beállítások** > **hely-helyreállítási kiszolgálók lehetőséget.** Jelölje ki a konfigurációs kiszolgálót, majd bontsa ki **a Folyamatkiszolgálók csomópontot.**

    ![Képernyőkép a Folyamatkiszolgáló párbeszédpanelről](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Kattintson a jobb gombbal a jelenleg használt folyamatkiszolgálóra, majd válassza a **Váltás parancsot.**

    ![Képernyőkép a Konfigurációs kiszolgáló párbeszédpanelről](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. A **Célfolyamat-kiszolgáló kiválasztása**csoportban válassza ki a használni kívánt új folyamatkiszolgálót. Ezután válassza ki a kiszolgáló által kezelt virtuális gépeket. A kiszolgálóval kapcsolatos információk kijelölését válassza az információs ikonra. A betöltési döntések meghozatalához megjelenik az egyes kiválasztott virtuális gépek nek az új folyamatkiszolgálóra replikálásához szükséges átlagos terület. Jelölje be a pipát az új folyamatkiszolgálóra való replikálás megkezdéséhez.

## <a name="deploy-additional-master-target-servers"></a>További főcélkiszolgálók telepítése

A következő esetekben egynél több fő célkiszolgálóra van szükség:

*   Egy Linux-alapú virtuális gépet szeretne védeni.
*   A konfigurációs kiszolgálón elérhető fő célkiszolgáló nem fér hozzá a virtuális gép adattárához.
*   A fő célkiszolgálón lévő lemezek száma (a kiszolgálón lévő helyi lemezek száma és a védendő lemezek száma) meghaladja a 60 lemezt.

Ha meg szeretné tudni, hogyan adhat hozzá fő célkiszolgálót egy Linux-alapú virtuális géphez, olvassa el [A Linux fő célkiszolgáló telepítése feladat-visszavételhez](vmware-azure-install-linux-master-target.md)című témakört.

Fő célkiszolgáló hozzáadása Windows-alapú virtuális géphez:

1. Nyissa meg a **Recovery Services Vault** > **helyhelyreállítási infrastruktúra** > **konfigurációs kiszolgálóinak webhelykonfigurációs kiszolgálóit.**
2. Válassza ki a szükséges konfigurációs kiszolgálót, majd válassza **a Fő célkiszolgáló**lehetőséget.

    ![Képernyőkép a Fő célkiszolgáló hozzáadása gombról](media/site-recovery-plan-capacity-vmware/add-master-target-server.png)
3. Töltse le az egyesített telepítőfájlt, majd futtassa a fájlt a virtuális gépen a fő célkiszolgáló beállításához.
4. Válassza **a Főcél** > telepítése**Tovább lehetőséget.**

    ![Képernyőkép, amelyen a Fő cél telepítése beállítás látható](media/site-recovery-plan-capacity-vmware/choose-MT.PNG)
5. Válassza ki az alapértelmezett telepítési helyet, majd kattintson a **Telepítés gombra.**

     ![Képernyőkép az alapértelmezett telepítési helyet megjelenítő képernyőképről](media/site-recovery-plan-capacity-vmware/MT-installation.PNG)
6. Ha regisztrálni szeretné a fő célt a konfigurációs kiszolgálóval, válassza a **Folytatás a konfigurációba**lehetőséget.

    ![Képernyőkép, amelyen a Folytatás a konfigurációhoz gomb látható](media/site-recovery-plan-capacity-vmware/MT-proceed-configuration.PNG)
7. Adja meg a konfigurációs kiszolgáló IP-címét, majd írja be a jelszót. A jelszó létrehozásáról a [Konfigurációs kiszolgáló jelmondatának létrehozása .Ok a konfigurációs kiszolgáló jelmondatának létrehozása](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase). 

    ![Képernyőkép, amely megmutatja, hogy hol adja meg a konfigurációs kiszolgáló IP-címét és jelmondatát](media/site-recovery-plan-capacity-vmware/cs-ip-passphrase.PNG)
8. Kattintson a **Register** (Regisztrálás) elemre. A regisztráció befejezése után válassza a **Befejezés**gombot.

Ha a regisztráció sikeresen befejeződik, a kiszolgáló megjelenik az Azure Portalon a **Recovery Services Vault** > **site recovery infrastruktúra** > **konfigurációs kiszolgálói ,** a fő célkiszolgálók a konfigurációs kiszolgáló.

 > [!NOTE]
 > Töltse le a [fő célkiszolgáló windowsos egyesített telepítőfájljának](https://aka.ms/latestmobsvc)legújabb verzióját.

## <a name="next-steps"></a>További lépések

A [Webhely-helyreállítási telepítéstervező](https://aka.ms/asr-deployment-planner)letöltése és futtatása.
