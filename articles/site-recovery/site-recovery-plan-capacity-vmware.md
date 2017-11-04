---
title: "Kapacitástervezés és a VMware-replikáció az Azure szolgáltatásban az Azure Site Recovery skálázás |} Microsoft Docs"
description: "Ez a cikk terv kapacitás és a skála használja, ha VMware virtuális gépek replikálása Azure-bA az Azure Site Recovery szolgáltatással"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 0a1cd8eb-a8f7-4228-ab84-9449e0b2887b
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/30/2017
ms.author: rayne
ms.openlocfilehash: 0f4d82d450a6ca2e73c68452a409f300841dbf32
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2017
---
# <a name="plan-capacity-and-scaling-for-vmware-replication-with-azure-site-recovery"></a>Kapacitás és a VMware-replikáció az Azure Site Recovery méretezés tervezése

Ez a cikk segítségével mérje fel, jelent a kapacitástervezés és a méretezésről, a helyszíni VMware virtuális gépek és fizikai kiszolgálók replikálása Azure-bA esetén [Azure Site Recovery](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Hogyan kezdhetem meg, hogy a kapacitástervezés?

A replikálási környezetre vonatkozó információt parancsprogram futtatásával gyűjtsön a [Azure Site Recovery telepítési Planner](https://aka.ms/asr-deployment-planner-doc) a VMware-replikáció. [További](site-recovery-deployment-planner.md) erről az eszközről. Lesz gyűjtse össze a kompatibilis és nem kompatibilis virtuális gépekhez, virtuális gépenként lemezek kapcsolatos információkat, és lemezenként kavarog adatokat. Az eszköz is magában foglalja az sávszélességre van szükség, és az Azure-infrastruktúra szükséges sikeres replikáció és a teszt feladatátvételhez.

## <a name="capacity-considerations"></a>A kapacitás kapcsolatos szempontok

**Összetevő** | **Részletek** |
--- | --- | ---
**Replikáció** | **Legfeljebb napi adatváltozási sebesség:** A védett gép csak egy folyamat-kiszolgálót is használhat, és a folyamat egyetlen kiszolgáló kezelni tud a napi módosítás legfeljebb 2 TB-os aránya. Így 2 TB az adatok maximális napi adatváltozási sebessége, a védett gépek által támogatott.<br/><br/> **Maximális átviteli sebesség:** egy tárfiókot az Azure-ban A replikált gép is tartozhatnak. Standard szintű tárfiók kezelni tud a legfeljebb 20 000 kérelmek / másodperc, és azt javasoljuk, hogy ne bemeneti/kimeneti műveletek (IOPS) másodpercenkénti száma a forrásgép között 20 000. Például ha a forrásgép 5 lemezzel rendelkezik, és egyes lemezek 120 IOPS (8 KB-os méret) a forrásgépen állít elő, majd lesz lemez IOPS-korlát az 500-as Azure-ban. (A storage-fiókok szükséges száma nem 20 000 végzett teljes forrásgép iops-érték, annál.)
**Konfigurációs kiszolgáló** | A konfigurációs kiszolgáló tudja kezelni a napi módosítási gyakorisága kapacitást az összes védett gépeken futó munkaterhelések között kell lennie, és folyamatosan replikálják az adatokat az Azure Storage elegendő sávszélesség szükséges.<br/><br/> Ajánlott eljárásként keresse meg a konfigurációs kiszolgáló, az ugyanazon a hálózaton és a LAN-szegmens, a védeni kívánt gépek. Egy másik hálózaton található, de a védeni kívánt gépek réteg 3 hálózati láthatóságának rá kell rendelkeznie.<br/><br/> Méret javaslatok a konfigurációs kiszolgáló a következő szakaszban található táblázat foglalja össze.
**Folyamatkiszolgáló** | Az első folyamatkiszolgáló a konfigurációs kiszolgálón alapértelmezés szerint telepítve van. Telepíthet további folyamat kiszolgálók méretezése a környezetben. <br/><br/> A folyamatkiszolgáló védett gépek kap replikációs adatokat, és gyorsítótárazás, tömörítés és titkosítás segítségével optimalizálja őket. Ezt követően az adatok az Azure-bA küldené. A folyamat server gépnek rendelkeznie kell a feladatok végrehajtásához szükséges erőforrásokat.<br/><br/> A folyamatkiszolgáló a lemezalapú gyorsítótárban használja. 600 GB vagy több különálló gyorsítótár lemez használatával kezeli a hálózati szűk vagy szolgáltatáskimaradás esetén tárolt adatok változásait.

## <a name="size-recommendations-for-the-configuration-server"></a>A konfigurációs kiszolgáló méretével kapcsolatos megfontolások

**PROCESSZOR** | **Memória** | **Gyorsítótár-lemez mérete** | **Adatváltozási sebesség** | **Védett gépek**
--- | --- | --- | --- | ---
8 Vcpu (2 sockets * @ 2,5 gigahertz [GHz] 4 mag) | 16 GB | 300 GB | 500 GB vagy kevesebb | 100-nál kevesebb gépek replikálása.
12 Vcpu (2 sockets * @ 2,5 GHz-es 6 mag) | 18 GB | 600 GB | 1 TB 500 GB | 100-150 gépek közti replikálásához.
16 Vcpu (2 sockets * @ 2,5 GHz-es 8 mag) | 32 GB | 1 TB | 1 TB-os és 2 TB | 150-200 gépek közti replikálásához.
Egy másik folyamat-kiszolgáló központi telepítése | | | > 2 TB | Ha több mint 200 gépeket replikál, vagy ha napi módosítása aránya meghaladja a 2 TB további folyamat kiszolgálók telepítése

Az elemek magyarázata:

* Minden forrásgép 100 GB, 3 lemezzel van konfigurálva.
* Összehasonlítási tárolása 8 SAS-meghajtókkal 10 k fordulat/PERCES, RAID 10-es, gyorsítótár lemez mérések használtuk.

## <a name="size-recommendations-for-the-process-server"></a>A folyamatkiszolgáló mérete javaslatok

Ha több mint 200 gépek védeni kíván, vagy a napi változási sebessége nagyobb, mint 2 TB, a replikációs terhelés kezelése érdekében folyamat-kiszolgálót is hozzáadhat. Horizontális, a következőket teheti:

* A konfigurációs kiszolgálók számának növeléséhez. Például megvédheti legfeljebb 400 gépek két konfigurációs kiszolgálóval.
* Folyamat további kiszolgálók hozzáadásához, és használja e helyett (vagy kívül) forgalmat fog kezelni a konfigurációs kiszolgáló.

A következő táblázat egy olyan forgatókönyvet, amelyben:

* Még nem tervezi folyamat-kiszolgálóként a konfigurációs kiszolgálót használja.
* Egy további folyamatkiszolgáló beállítása.
* A további folyamatkiszolgálót szeretne használni a védett virtuális gépek konfigurálását.
* Minden védett forrásgép 100 GB három lemez van konfigurálva.

**Konfigurációs kiszolgáló** | **További folyamatkiszolgáló** | **Gyorsítótár-lemez mérete** | **Adatváltozási sebesség** | **Védett gépek**
--- | --- | --- | --- | ---
8 Vcpu (2 sockets * @ 2,5 GHz, 4 mag), 16 GB memória | 4 Vcpu (2 sockets * @ 2,5 GHz-es 2 mag), 8 GB memória | 300 GB | 250 GB vagy kevesebb | 85 vagy kevesebb gépek replikálása.
8 Vcpu (2 sockets * @ 2,5 GHz, 4 mag), 16 GB memória | 8 Vcpu (2 sockets * @ 2,5 GHz, 4 mag), 12 GB memória | 600 GB | 250 GB és 1 TB | Replikálja a 85-150 gépek között.
12 Vcpu (2 sockets * @ 2,5 GHz-es 6 mag), 18 GB memória | 12 Vcpu (2 sockets * @ 2,5 GHz-es 6 mag) 24 GB memória | 1 TB | 1 TB-os és 2 TB | 150-225 gépek közti replikálásához.

Amelyben a kiszolgálók méretezése módja attól függ, hogy egy méretezett és kibővített modell igény szerint.  Vertikális felskálázás néhány csúcskategóriás konfigurációs és folyamat-kiszolgálók üzembe helyezésével, vagy kevesebb erőforrást további kiszolgálók üzembe helyezésével kiterjesztése. Például ha 220 gépek védelmére van szüksége, módszerekkel a következők:

* A konfigurációs kiszolgáló 12 vCPU, 18 GB memória, és egy további folyamatkiszolgáló 12 vCPU, 24 GB memóriát a beállítása. Konfigurálja a védett gépek csak a további folyamatkiszolgálót szeretne használni.
* (2 darab 8 vCPU, 16 GB RAM) két konfigurációs kiszolgáló és két további folyamat-kiszolgáló (1 x 8 vCPU és 4 vCPU x 1 leíróhoz 135-ös + 85 [220] gépek) beállítása. Védett gépek csak a folyamat további kiszolgálók használatára konfigurálja.


## <a name="control-network-bandwidth"></a>Hálózati sávszélesség szabályozására

Használata után a [a központi telepítés Planner eszköz](site-recovery-deployment-planner.md) replikációs (a kezdeti replikáció és majd különbözeti) szükséges sávszélességet is kiszámíthatja, hogy a beállítások több replikáláshoz használt sávszélességet szabályozhatja:

* **A sávszélesség szabályozását**: az Azure-bA replikált VMware-forgalom halad át egy adott folyamat kiszolgáló. Beállíthatja a folyamat kiszolgálókat futtató gépeken sávszélesség szabályozását.
* **Sávszélesség befolyásolja**: a sávszélesség-replikációhoz használt néhány beállításkulcsok használatával is szabályozhatja:
  * A **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** egy lemezen adatátvitelre (kezdeti vagy változásreplikálásra replikációs) használt szálak számát adja meg a beállításazonosítót. Minél magasabb a beállítás értéke, annál nagyobb hálózati sávszélességet használ a rendszer a replikáláshoz.
  * A **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** adatátvitel feladat-visszavétel során használt szálak számát adja meg.

### <a name="throttle-bandwidth"></a>Sávszélesség szabályozása

1. Nyissa meg az Azure Backup szolgáltatás MMC beépülő modul a folyamat-kiszolgálóként működő számítógép. Alapértelmezés szerint a helyi biztonsági mentés érhető el az asztalon, vagy a következő mappában: C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Kattintson a beépülő modul **Tulajdonságok módosítása** elemére.

    ![Képernyőfelvétel az Azure Backup szolgáltatás MMC beépülő modul lehetőséget tulajdonságainak módosítása](./media/site-recovery-vmware-to-azure/throttle1.png)
3. Az a **sávszélesség-szabályozási** lapon jelölje be **engedélyezi az internetes sávszélesség szabályozásának a biztonsági mentési műveleteknél**. Állítsa be a munkaórákra és a munkaórákon kívüli időre. 512 kB/s és 102 MB/s közötti értéket adhat meg.

    ![Képernyőfelvétel az Azure biztonsági mentés tulajdonságai párbeszédpanel](./media/site-recovery-vmware-to-azure/throttle2.png)

A szabályozáshoz a [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) parancsmag is használható. Íme egy példa:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

A **Set-OBMachineSetting -NoThrottle** beállítás azt jelenti, hogy nincs szükség szabályozásra.

### <a name="influence-network-bandwidth-for-a-vm"></a>A hálózati sávszélesség szabályozása a virtuális gépek

1. A virtuális beállításjegyzék, lépjen a **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Befolyásolják a sávszélesség-forgalom replikációs lemezen, módosítsa a értékét **UploadThreadsPerVM**, vagy hozzon létre a kulcsot, ha még nem létezik.
   * Az Azure-ból feladatátvételi forgalomhoz sávszélesség befolyásolja, módosítsa a értékét **DownloadThreadsPerVM**.
2. Az alapértelmezett érték a 4. A szükségesnél több erőforrással ellátott hálózatban érdemes módosítani a beállításazonosítók alapértelmezett értékét. A maximális érték a 32. Az optimális érték kiválasztásához kövesse figyelemmel a forgalmat.


## <a name="deploy-additional-process-servers"></a>További folyamat-kiszolgálók központi telepítése

Ha rendelkezik méretezési ki a központi telepítés meghaladja a 200 forrásgépek vagy naponta kavarog egyidejűleg több, mint 2 TB-os aránya teljes, szükséges további folyamat kiszolgálók kezeléséhez a forgalom mennyisége. Kövesse az alábbi utasításokat a folyamatkiszolgáló beállítása. A kiszolgáló beállítása után telepít át forrásgépek rá.

1. A **Site Recovery kiszolgálók**, kattintson a konfigurációs kiszolgáló, majd **Folyamatkiszolgáló**.

    ![Képernyőfelvétel a Site Recovery kiszolgálók beállítást folyamat kiszolgáló hozzáadása](./media/site-recovery-vmware-to-azure/migrate-ps1.png)
2. A **kiszolgálótípus**, kattintson a **folyamat server (helyszíni)**.

    ![Képernyőfelvétel a Folyamatkiszolgáló párbeszédpanel](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
3. Töltse le a Site Recovery az egységes telepítő fájlját, és futtassa a folyamatkiszolgáló telepítése. Ez is regisztrálja azt a tárolóban lévő állapottal.
4. Az **Előkészületek** területen válassza a **További folyamatkiszolgálók hozzáadása az üzembe helyezés horizontális felskálázásához** lehetőséget.
5. A varázsló a megszokott módon mikor meg [beállítása](#step-2-set-up-the-source-environment) a konfigurációs kiszolgáló.

    ![Képernyőfelvétel az Azure Site Recovery az egységes telepítő varázsló](./media/site-recovery-vmware-to-azure/add-ps1.png)
6. A **konfigurációs kiszolgáló adatait**, adja meg az IP-címe a konfigurációs kiszolgáló és a jelszót. Szerezze be a jelszót, futtassa a **[SiteRecoveryInstallationFolder]\home\sysystems\bin\genpassphrase.exe – n** a konfigurációs kiszolgálón.

    ![Képernyőfelvétel a konfigurációs kiszolgáló Részletek lap](./media/site-recovery-vmware-to-azure/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>Az új folyamatkiszolgáló használandó gépek áttelepítése
1. A **beállítások** > **Site Recovery kiszolgálók**, kattintson a konfigurációs kiszolgáló, és végül **kiszolgálók feldolgozni**.

    ![Képernyőfelvétel a Folyamatkiszolgáló párbeszédpanel](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Kattintson a jobb gombbal a folyamatkiszolgáló jelenleg használatban van, és kattintson a **kapcsoló**.

    ![Képernyőfelvétel a konfigurációs kiszolgáló párbeszédpanel](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. A **válassza folyamat-célkiszolgálót**, válassza ki a használni kívánt új folyamat kiszolgálót, majd válassza ki a virtuális gépek, amelyek a kiszolgáló fogja kezelni. Kattintson az ikonra az a kiszolgáló adatainak beolvasása. Annak érdekében, hogy a döntések tölthető be, szükség van az összes kiválasztott virtuális gépet replikálni az új folyamatkiszolgáló átlagos terület jelenik meg. Kattintson a pipa jelre az új folyamatkiszolgáló a replikáló elindításához.


## <a name="next-steps"></a>Következő lépések

Töltse le és futtassa a [Azure Site Recovery telepítési Planner](https://aka.ms/asr-deployment-planner)
