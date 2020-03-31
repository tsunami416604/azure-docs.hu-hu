---
title: VMware virtuális gépek/fizikai kiszolgálók vészhelyreállítása egy másodlagos helyre az Azure Site Recovery szolgáltatással
description: Ismerje meg, hogyan állíthatja be a VMware virtuális gépek, illetve a Windows és linuxos fizikai kiszolgálók vészutáni helyreállítását egy másodlagos helyre az Azure Site Recovery szolgáltatással.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: raynew
ms.openlocfilehash: 71d230c9fea25edfbf0ca4ea40f15b69779ad060
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256808"
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Helyszíni VMware-alapú virtuális gépek vagy fizikai kiszolgálók vészhelyreállításának beállítása másodlagos helyre

Az InMage Scout az [Azure Site Recovery](site-recovery-overview.md) szolgáltatásban valós idejű replikációt biztosít a helyszíni VMware-helyek között. Az InMage Scout az Azure Site Recovery szolgáltatás-előfizetések részét képezi.

## <a name="end-of-support-announcement"></a>Támogatás megszűnésének bejelentése

Az Azure Site Recovery forgatókönyv a helyszíni VMware vagy fizikai adatközpontok közötti replikációhoz eléri a támogatás végét.

-   2018 augusztusától a forgatókönyv nem konfigurálható a Recovery Services tárolójában, és az InMage Scout szoftver nem tölthető le a tárolóból. A meglévő telepítések támogatottak lesznek. 
-   2020. december 31-től a forgatókönyv nem támogatott.
- A meglévő partnerek új ügyfeleket vehetnek fel a forgatókönyvbe, amíg a támogatás véget nem ér.

2018-ban és 2019-ben két frissítés jelenik meg: 

-   7. frissítés: Javítja a hálózati konfigurációval és megfelelőségi problémákkal kapcsolatos problémákat, és támogatja a TLS 1.2-t.
-   8. frissítés: Támogatja az RHEL/CentOS 7.3/7.4/7.5 linuxos operációs rendszereket és a SUSE 12-t

A 8- as frissítés után nem jelenik meg további frissítés. A 8.

Az Azure Site Recovery továbbra is újít azáltal, hogy a VMware és a Hyper-V ügyfelei számára zökkenőmentes és kategóriájában legjobb DRaaS-megoldást biztosít az Azure-ral, mint vész-helyreállítási helyszínvel. A Microsoft azt javasolja, hogy a meglévő InMage / ASR Scout ügyfelek fontolja meg az Azure Site Recovery VMware az Azure-forgatókönyv az üzletmenet folytonossági igényeinek. Az Azure Site Recovery VMware az Azure-ba forgatókönyv egy nagyvállalati szintű DR-megoldás a VMware-alkalmazások számára, amely rpo-t és RTO-t kínál a percekhez, támogatja a többvirtuális gépes alkalmazások replikációját és helyreállítását, a zökkenőmentes bevezetést, az átfogó figyelést és jelentős TCO-előny.

### <a name="scenario-migration"></a>Forgatókönyv – Migrálás
Alternatív megoldásként azt javasoljuk, hogy a vész-helyreállítási helyszíni VMware virtuális gépek és a fizikai gépek replikálásával az Azure-ba. Ezt a következőképpen teheti meg:

1.  Tekintse át az alábbi gyors összehasonlítást. A helyszíni gépek replikálása előtt ellenőriznie kell, hogy azok megfelelnek-e az Azure-ba való replikáció [követelményeinek.](./vmware-physical-azure-support-matrix.md#replicated-machines) Ha VMware virtuális gépeket replikál, javasoljuk, hogy tekintse át a [kapacitástervezési irányelveket,](./site-recovery-plan-capacity-vmware.md)és futtassa a [Deployment Planner eszközt](./site-recovery-deployment-planner.md) az identitáskapacitás-követelményeknek, és ellenőrizze a megfelelőséget.
2.  A Deployment Planner futtatása után beállíthatja a replikációt: o VMware virtuális gépekhez kövesse ezeket az oktatóanyagokat az [Azure előkészítéséhez,](./tutorial-prepare-azure.md) [a helyszíni VMware-környezet előkészítéséhez](./vmware-azure-tutorial-prepare-on-premises.md)és a [vészhelyreállítás beállításához.](./vmware-azure-tutorial-prepare-on-premises.md)
o A fizikai gépek, kövesse ezt a [bemutató](./physical-azure-disaster-recovery.md).
3.  Miután a gépek replikálják az Azure-ba, futtathat egy [vész-helyreállítási gyakorlatot,](./site-recovery-test-failover-to-azure.md) hogy megbizonyosodjon arról, hogy minden a várt módon működik.

### <a name="quick-comparison"></a>Gyors összehasonlítás

**Szolgáltatás** | **Replikálás az Azure-ba** |**A VMware adatközpontjai közötti replikáció**
--|--|--
**Szükséges összetevők** |Mobilitási szolgáltatás replikált gépeken. Helyszíni konfigurációs kiszolgáló, folyamatkiszolgáló, fő célkiszolgáló. Ideiglenes folyamatkiszolgáló az Azure-ban a feladat-visszavételhez.|Mobilitási szolgáltatás, folyamatkiszolgáló, konfigurációs kiszolgáló és főcél
**Konfiguráció és vezénylés** |Recovery Services-tároló az Azure Portalon | A vContinuum használata 
**Replikált** |Lemez (Windows és Linux) |Kötet-Windows<br> Lemez-Linux
**Megosztott lemezfürt** |Nem támogatott|Támogatott
**Adatforgalom-határértékek (átlag)** |10 MB/s adat lemezenként<br> 25 MB/s adat virtuális gépenként<br> [További információ](./site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) | > 10 MB/s adat lemezenként  <br> > 25 MB/s adat virtuális gépenként
**Megfigyelő** |Az Azure Portalról|CX-ből (konfigurációs kiszolgáló)
**Támogatási mátrix** | [Kattintson ide a részletekért](./vmware-physical-azure-support-matrix.md)|[Az ASR Scout-kompatibilis mátrix letöltése](https://aka.ms/asr-scout-cm)


## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez:

- [Tekintse át](vmware-physical-secondary-support-matrix.md) az összes összetevő támogatási követelményeit.
- Győződjön meg arról, hogy a replikálni kívánt gépek megfelelnek a [replikált géptámogatásnak.](vmware-physical-secondary-support-matrix.md#replicated-vm-support)


## <a name="download-and-install-component-updates"></a>Összetevő-frissítések letöltése és telepítése

 Tekintse át és telepítse a legújabb [frissítéseket.](#updates) A frissítéseket a következő sorrendben kell telepíteni a kiszolgálókra:

1. RX-kiszolgáló (ha van ilyen)
2. Konfigurációs kiszolgálók
3. Kiszolgálók feldolgozása
4. Fő célkiszolgálók
5. vContinuum szerverek
6. Forráskiszolgáló (Windows és Linux kiszolgálók is)

Telepítse a frissítéseket az alábbiak szerint:

> [!NOTE]
>Előfordulhat, hogy a Scout összes összetevőfájl-frissítési verziója nem egyezik meg a frissítés .zip fájljában. A régebbi verzió azt jelzi, hogy a frissítés előző frissítése óta nincs változás az összetevőben.

Töltse le a [frissítés](https://aka.ms/asr-scout-update7) .zip fájlt, valamint a [MySQL és php frissítés konfigurációs](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade) fájlokat. A frissítés .zip fájl tartalmazza a következő összetevők összes alap bináris és halmozott frissítési bináris fájlját: 
- InMage_ScoutCloud_RX_8.0.1.0_RHEL6-64_GA_02Mar2015.tar.gz
- RX_8.0.7.0_GA_Update_7_2965621_28Dec18.tar.gz
- InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe
- InMage_CX_TP_8.0.1.0_Windows_GA_26Feb2015_release.exe
- CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe
- InMage_PI_8.0.1.0_Windows_GA_26Feb2015_release.exe
- InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe
- InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe
- InMage_UA_8.0.7.0_OL5-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_OL5-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_OL6-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_OL6.-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL5-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL5-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL6-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL7-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP1-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP1-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP2-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP2-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP3-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP3-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP4-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP4-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-64_GA_04Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP1-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP1-64_GA_04Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP2-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP2-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP3-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP3-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP4-64_GA_03Dec2018_release.tar.gz
  1. Bontsa ki a .zip fájlokat.
  2. **RX-kiszolgáló**: Másolja **RX_8.0.7.0_GA_Update_7_2965621_28Dec18.tar.gz-t** az RX kiszolgálóra, és bontsa ki. A kibontott mappában futtassa a **/Install**parancsot.
  3. **Konfigurációs kiszolgáló és folyamatkiszolgáló**: Másolja **a CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe** programot a konfigurációs kiszolgálóra és a folyamatkiszolgálóra. Dupla kattintással futtassa.<br>
  4. **Windows fő célkiszolgáló**: Az egyesített ügynök frissítéséhez másolja a **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** programot a kiszolgálóra. Kattintson rá duplán a futtatásához. Ugyanez a fájl is használható friss telepítés. Ugyanez az egyesített ügynökfrissítés a forráskiszolgálóra is alkalmazható.
  A frissítésnek nem kell alkalmaznia a **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe** programmal készített fő célra, mivel ez az új GA telepítő a legújabb módosításokkal.
  5. **vContinuum kiszolgáló**: Másolja **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe-t** a kiszolgálóra.  Győződjön meg arról, hogy bezárta a vContinuum varázslót. Dupla kattintással a fájlt futtatni.
  6. **Linux fő célkiszolgáló**: Az egyesített ügynök frissítéséhez másolja a **InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz-t** a Linux Master Target kiszolgálóra, és bontsa ki. A kibontott mappában futtassa a **/Install**parancsot.
  7. **Windows forráskiszolgáló**: Az egyesített ügynök frissítéséhez másolja a **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** programot a forráskiszolgálóra. Dupla kattintással a fájlt futtatni. 
  8. **Linux forráskiszolgáló**: Az egyesített ügynök frissítéséhez másolja az egyesített ügynökfájl megfelelő verzióját a Linux-kiszolgálóra, és bontsa ki. A kibontott mappában futtassa a **/Install**parancsot.  Példa: RHEL 6,7 64 bites kiszolgáló esetén másolja **a InMage_UA_8.0.7.0_RHEL6.64_GA_03Dec2018_release.tar.gz-t** a kiszolgálóra, és bontsa ki. A kibontott mappában futtassa a **/Install**parancsot.
  9. A Configuration Server, Process Server és RX kiszolgáló fent említett telepítőkkel való frissítése után a PHP és a MySQL könyvtárakat manuálisan kell frissíteni a [gyors telepítési útmutató](https://aka.ms/asr-scout-quick-install-guide)7.4 szakaszában említett lépésekkel.

## <a name="enable-replication"></a>A replikáció engedélyezése

1. Állítsa be a replikációt a forrás- és a cél VMware-helyek között.
2. A telepítésről, védelemről és helyreállításról a következő dokumentumokban olvashat bővebben:

   * [Kibocsátási megjegyzések](https://aka.ms/asr-scout-release-notes)
   * [Kompatibilitási mátrix](https://aka.ms/asr-scout-cm)
   * [Használati útmutató](https://aka.ms/asr-scout-user-guide)
   * [RX felhasználói útmutató](https://aka.ms/asr-scout-rx-user-guide)
   * [Gyors telepítési útmutató](https://aka.ms/asr-scout-quick-install-guide)
   * [MYSQL és PHP könyvtárak frissítése](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade)

## <a name="updates"></a>Frissítések

### <a name="site-recovery-scout-801-update-7"></a>Site Recovery Scout 8.0.1 Frissítés 7 
Frissítve: December 31, 2018 Letöltés [Scout frissítés 7](https://aka.ms/asr-scout-update7).
Scout Update 7 egy teljes telepítő, amely használható a friss telepítés, valamint a frissítés a meglévő ügynökök / MT, amelyek a korábbi frissítések (a Frissítés 1 Update 6). Tartalmazza az 1-es frissítéstől a 6-ig történő összes javítást, valamint az alábbiakban ismertetett új javításokat és fejlesztéseket.
 
#### <a name="new-features"></a>Új funkciók
* PCI-megfelelőség
* TLS 1.2-es és a 2-es es

#### <a name="bug-and-security-fixes"></a>Hiba- és biztonsági javítások
* Javítva: A Windows fürt/önálló gépek helytelen IP-konfigurációval rendelkeznek a helyreállítás/DR-drill után.
* Javítva: Előfordulhat, hogy a Lemez hozzáadása sikertelen a V2V-fürtesetében.
* Javítva: a vContinuum varázsló elakad a helyreállítási fázisban, ha a fő cél Windows Server 2016
* Javítva: A MySQL biztonsági problémáit a MySQL 5.7.23-as verzióra való frissítésével enyhítik

#### <a name="manual-upgrade-for-php-and-mysql-on-csps-and-rx"></a>Kézi frissítés a PHP és a MySQL a CS, PS, és RX
A PHP parancsfájl-platformot a Configuration Server, a Process Server és az RX Server 7.2.10-es verziójára kell frissíteni.
A MySQL adatbázis-kezelő rendszert frissíteni kell az 5.7.23-as verzióra a Configuration Server, a Process Server és az RX Server rendszeren.
Kérjük, kövesse a Php és MySQL verziók frissítéséhez a [Gyors telepítési útmutatóban](https://aka.ms/asr-scout-quick-install-guide) megadott manuális lépéseket.

### <a name="site-recovery-scout-801-update-6"></a>Site Recovery Scout 8.0.1 frissítés 6 
Frissítve: October 12, 2017

Letöltés [Scout frissítés 6](https://aka.ms/asr-scout-update6).

A Scout Update 6 egy összesített frissítés. Tartalmazza az 1-es frissítéstől az 5-ös frissítésig minden javítást, valamint az alábbiakban ismertetett új javításokat és fejlesztéseket. 

#### <a name="new-platform-support"></a>Új platformtámogatás
* A Windows Server 2016 forrástámogatása hozzáadva
* A következő Linux operációs rendszerek támogatása lett hozzáadva:
    - Red Hat Enterprise Linux (RHEL) 6,9
    - CentOS 6,9
    - Oracle Linux 5.11
    - Oracle Linux 6.8
* A VMware Center 6.5 támogatása hozzáadva

Telepítse a frissítéseket az alábbiak szerint:

> [!NOTE]
>Előfordulhat, hogy a Scout összes összetevőfájl-frissítési verziója nem egyezik meg a frissítés .zip fájljában. A régebbi verzió azt jelzi, hogy a frissítés előző frissítése óta nincs változás az összetevőben.

Töltse le a [frissítés](https://aka.ms/asr-scout-update6) .zip fájlt. A fájl a következő összetevőket tartalmazza: 
- RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
- CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe
- UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
- UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
- vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe
- UA update4 bit rhel5, OL5, OL6, SUSE 10,\<SUSE 11: UA_ Linux operációs rendszer>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
  1. Bontsa ki a .zip fájlokat.
  2. **RX-kiszolgáló**: Másolja **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz-t** az RX kiszolgálóra, és bontsa ki. A kibontott mappában futtassa a **/Install**parancsot.
  3. **Konfigurációs kiszolgáló és folyamatkiszolgáló**: Másolja **CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe-t** a konfigurációs kiszolgálóra és a folyamatkiszolgálóra. Dupla kattintással futtassa.<br>
  4. **Windows fő célkiszolgáló**: Az egyesített ügynök frissítéséhez másolja a **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** programot a kiszolgálóra. Kattintson rá duplán a futtatásához. Ugyanez az egyesített ügynökfrissítés a forráskiszolgálóra is alkalmazható. Ha a forrás nem frissült a 4-es frissítésre, frissítse az egyesített ügynököt.
  A frissítésnek nem kell alkalmaznia a **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** programmal készített fő célra, mivel ez az új GA telepítő a legújabb módosításokkal.
  5. **vContinuum kiszolgáló**: Másolja **vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe-t** a kiszolgálóra.  Győződjön meg arról, hogy bezárta a vContinuum varázslót. Dupla kattintással a fájlt futtatni.
  A frissítésnek nem kell alkalmaznia a **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe-vel** készített fő célra, mivel ez az új GA telepítő a legújabb módosításokkal.
  6. **Linux fő célkiszolgáló**: Az egyesített ügynök frissítéséhez másolja **a UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz-t** a fő célkiszolgálóra, és bontsa ki. A kibontott mappában futtassa a **/Install**parancsot.
  7. **Windows forráskiszolgáló**: Az egyesített ügynök frissítéséhez másolja **a UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** programot a forráskiszolgálóra. Dupla kattintással a fájlt futtatni. 
  Nem kell telepítenie az **0_Windows_GA_28Sep2017_release InMage_UA_8**5.
  8. **Linux forráskiszolgáló**: Az egyesített ügynök frissítéséhez másolja az egyesített ügynökfájl megfelelő verzióját a Linux-kiszolgálóra, és bontsa ki. A kibontott mappában futtassa a **/Install**parancsot.  Példa: RHEL 6,7 64 bites kiszolgáló esetén másolja **a UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz-t** a kiszolgálóra, és bontsa ki. A kibontott mappában futtassa a **/Install**parancsot.


> [!NOTE]
> * A Windows Server 2016 alapegységes ügynöktelepítője frissítve lett. Az új telepítő **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe** van csomagolva az alap Scout GA csomag (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). Ugyanaz a telepítő lesz használva az összes támogatott Windows-verzióhoz. 
> * A Windows Server 2016-ot támogató Windows vContinuum & Master Target telepítő frissítése meglett. Az új telepítő **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** van csomagolva az alap Scout GA csomag (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). Ugyanez a telepítő lesz használva a Windows 2016 Master Target és a Windows 2012R2 Master Target központi telepítéséhez.
> * A fizikai kiszolgálón futó Windows Server 2016 rendszert az ASR Scout nem támogatja. Csak a Windows Server 2016 VMware VM-et támogatja. 
>

#### <a name="bug-fixes-and-enhancements"></a>Hibajavítások és fejlesztések
- A feladat-visszavételi védelem sikertelen a Linux virtuális gép számára, és a replikálandó lemezek listája üres a konfiguráció végén.

### <a name="site-recovery-scout-801-update-5"></a>Site Recovery Scout 8.0.1 Frissítés 5
A Scout Update 5 egy összesített frissítés. Tartalmazza az összes javítást az 1-es frissítéstől a 4-es frissítésig, valamint az alábbiakban ismertetett új javításokat.
- A Site Recovery Scout 4-es frissítésének 5-ös frissítéséhez tartozó javítások kifejezetten a fő cél- és vContinuum-összetevőkre vonatkoznak.
- Ha a forráskiszolgálók, a fő cél-, konfigurációs, folyamat- és RX-kiszolgálók már futnak a 4. 

#### <a name="new-platform-support"></a>Új platformtámogatás
* SUSE Linux Enterprise Server 11 Service Pack 4(SP4)
* Az SLES 11 SP4 64 bites **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** alap Scout GA csomaggal **(InMage_Scout_Standard_8.0.1 GA.zip)** van csomagolva. Töltse le a GA-csomagot a portálról, a hogy a vault létrehozása.


#### <a name="bug-fixes-and-enhancements"></a>Hibajavítások és fejlesztések

* A Windows fürttámogatás megbízhatóságának növelésére szolgáló javítások:
    * Rögzített- Néhány P2V MSCS fürtlemezek lesz RAW helyreállítása után.
    * Javítva – A P2V MSCS-fürt helyreállítása lemezrendelés-eltérés miatt sikertelen.
    * Javítva – A lemezek hozzáadására irányuló MSCS-fürtművelet lemezméret-eltéréssel nem sikerül.
    * Javítva – Az RDM LUNs leképezéssel rendelkező forrás MSCS-fürt készültségi ellenőrzése nem felel meg a méretellenőrzésnek.
    * Javítva – Az egycsomópontos fürtvédelem SCSI-eltérés miatt sikertelen. 
    * Javítva – A P2V Windows fürtkiszolgáló újbóli védelme sikertelen, ha célfürtlemezek vannak jelen. 
    
* Javítva: A feladat-visszavételi védelem során, ha a kiválasztott fő célkiszolgáló nem ugyanazon az ESXi-kiszolgálón van, mint a védett forrásgép (továbbítási védelem során), akkor a vContinuum rossz fő célkiszolgálót vesz fel a feladat-visszavétel és a helyreállítás során. működés sikertelen.

> [!NOTE]
> * A P2V-fürtjavításai csak az okra a fizikai MSCS-fürtökre vonatkoznak, amelyek újonnan védettek a Site Recovery Scout 5-ös frissítéssel. A fürtjavítások régebbi frissítésekkel rendelkező védett P2V MSCS-fürtökre való telepítéséhez kövesse a [Site Recovery Scout Release Notes](https://aka.ms/asr-scout-release-notes)12 szakaszában említett frissítési lépéseket.
> * ha az újravédelem idején ugyanazok a lemezcsoportok aktívak minden fürtcsomóponton, mint az eredeti védelem során, akkor a fizikai MSCS-fürt újbóli védelme csak a meglévő céllemezeket használhatja fel újra. Ha nem, akkor használja a Site [Recovery Scout Release Notes](https://aka.ms/asr-scout-release-notes)12 szakaszának manuális lépéseit, hogy a céloldali lemezeket a megfelelő adattárelérési útra helyezze át, az újravédelem során. Ha a frissítési lépések benem követése nélkül védi újra az MSCS-fürtöt P2V módban, új lemezt hoz létre a cél ESXi-kiszolgálón. A régi lemezeket manuálisan kell törölnie az adattárból.
> * Ha egy sles11 vagy SLES11 (bármely szervizcsomaggal rendelkező) kiszolgálót szabályosan újraindít, manuálisan jelölje meg a **gyökérlemez** replikációs párjait az újraszinkronizáláshoz. Nincs értesítés a CX felületen.Ha nem jelöli meg a gyökérlemezt az újraszinkronizáláshoz, adatintegritási problémákat észlelhet.


### <a name="azure-site-recovery-scout-801-update-4"></a>Az Azure Site Recovery Scout 8.0.1-es frissítése 4
Scout Update 4 egy kumulatív frissítés. Ez magában foglalja az összes javítást az 1-es frissítéstől a 3-as frissítésig, valamint az alábbiakban ismertetett új javításokat.

#### <a name="new-platform-support"></a>Új platformtámogatás

* A vCenter/vSphere 6.0, 6.1 és 6.2 támogatással bővült
* A linuxos operációs rendszerek támogatása hozzáadva:
  * Red Hat Enterprise Linux (RHEL) 7.0, 7.1 és 7.2
  * CentOS 7.0, 7.1 és 7.2
  * Red Hat Enterprise Linux (RHEL) 6,8
  * CentOS 6,8

> [!NOTE]
> RHEL/CentOS 7 64 bites **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** van csomagolva az alap Scout GA csomag **InMage_Scout_Standard_8.0.1 GA.zip**. Töltse le a Scout GA csomagot a portálról a vault létrehozása című részben leírtak szerint.

#### <a name="bug-fixes-and-enhancements"></a>Hibajavítások és fejlesztések

* Továbbfejlesztett leállításkezelés a következő Linux operációs rendszerek és klónok esetében a nem kívánt újraszinkronizálási problémák megelőzése érdekében:
    * Red Hat Enterprise Linux (RHEL) 6.x
    * Oracle Linux (OL) 6.x
* Linux esetén az egyesített ügynök telepítési könyvtárában lévő összes mappahozzáférési engedély csak a helyi felhasználóra korlátozódik.
* Windows rendszeren a gyakori elosztott konzisztencia-könyvjelzők kiadásakor az erősen betöltött elosztott alkalmazásokon, például az SQL Server- és a Share Point-fürtökön előforduló időtúllépési probléma javítása.
* A naplóval kapcsolatos javítás a konfigurációs kiszolgáló alaptelepítőjében.
* A Windows fő célbázistelepítője hozzálett adva a VMware vCLI 6.0 letöltési hivatkozásához.
* További ellenőrzések és naplók kerültek hozzáadásra, a hálózati konfiguráció s a feladatátvételi és vész-helyreállítási gyakorlatok során.
* Egy olyan probléma kijavítása, amely miatt az adatmegőrzési adatok nem jelentendők a konfigurációs kiszolgálónak.  
* Fizikai fürtök esetén a kötetátméretezés sikertelensítését okozó probléma javítása a vContinuum varázslóban a forráskötet zsugorításakor.
* A fürtvédelmi probléma javítása, amely a következő hibával sikerült: "Nem sikerült megtalálni a lemezaláírást", ha a fürtlemez PRDM-lemez.
* A cxps átviteli kiszolgáló összeomlásának javítása, amelyet egy tartományon kívüli kivétel okoz.
* A vContinuum varázsló **Leküldéses telepítése** lapján a kiszolgálónév és az IP-cím oszlopai mostantól átméretezhetők.
* RX API-fejlesztések:
  * Az öt legújabb elérhető közös konzisztenciapontok már elérhető (csak garantált címkék).
  * A kapacitás és a szabad terület részletei minden védett eszközön megjelennek.
  * A felderítő illesztőprogram állapota a forráskiszolgálón érhető el.

> [!NOTE]
> * **InMage_Scout_Standard_8.0.1_GA.zip** alapcsomag:
>     * Frissített konfigurációs kiszolgálóalap-telepítő (**InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
>     * Windows fő célbázistelepítő (**InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**).
>     * Minden új telepítéshez használja az új konfigurációs kiszolgálót és a Windows fő célGA-bitjeit.
> * A 4-es frissítés közvetlenül a 8.0.1 GA-n alkalmazható.
> * A konfigurációs kiszolgáló és az RX-frissítések nem állíthatók vissza a rendszer alkalmazása után.


### <a name="azure-site-recovery-scout-801-update-3"></a>Az Azure Site Recovery Scout 8.0.1 3.

A hely-helyreállítási frissítések összesítő jellegűek. A 3- as frissítés tartalmazza az 1. A 3-as frissítés közvetlenül alkalmazható a 8.0.1 GA-n. A konfigurációs kiszolgáló és az RX-frissítések nem állíthatók vissza a rendszer alkalmazása után.

#### <a name="bug-fixes-and-enhancements"></a>Hibajavítások és fejlesztések
A 3.

* A konfigurációs kiszolgáló és az RX nincs regisztrálva a tárolóban, amikor a proxy mögött vannak.
* Az állapotjelentésben nem frissül nek azok az órák száma, amelyekben a helyreállításipont-célkitűzés (RPO) nem lett elérve.
* A konfigurációs kiszolgáló nem szinkronizál rx-szel, ha az ESX hardveradatai vagy a hálózati adatok UTF-8 karaktert tartalmaznak.
* A Windows Server 2008 R2 tartományvezérlők nem indulnak el a helyreállítás után.
* Az offline szinkronizálás nem a várt módon működik.
* Virtuális gép feladatátvétele után a replikációpár törlése hosszú ideig nem történik meg a konfigurációs kiszolgálókonzolon. A felhasználók nem tudják végrehajtani a feladat-visszavételi vagy folytatási műveleteket.
* A konzisztenciafeladat által végzett összes pillanatkép-művelet optimalizálva van, így csökkenthető az alkalmazáskapcsolatbontások, például az SQL Server-ügyfelek.
* A konzisztenciaeszköz (VACP.exe) teljesítménye javult. A Windows pillanatképeinek létrehozásához szükséges memóriahasználat csökkent.
* A leküldéses telepítési szolgáltatás összeomlik, ha a jelszó 16 karakternél nagyobb.
* a vContinuum nem ellenőrzi és kéri az új vCenter-hitelesítő adatokat, amikor a hitelesítő adatok módosulnak.
* Linux on a fő cél cache manager (cachemgr) nem tölt le fájlokat a folyamat kiszolgálóról. Ez replikációs pár szabályozását eredményezi.
* Ha a fizikai feladatátvevő fürt (MSCS) lemezsorrendje nem azonos minden csomóponton, a replikáció nincs beállítva a fürtkötetek egy részéhez. A javítás kihasználásához a fürtöt újra védeni kell.  
* Az SMTP funkció nem a várt módon működik, miután az RX scout 7.1-ről Scout 8.0.1-re vált.
* További statisztikák lettek hozzáadva a visszaállítási művelet naplójában a befejezéshez szükséges idő nyomon követéséhez.
* A forráskiszolgálón lévő Linux operációs rendszerek támogatása:
  * Red Hat Enterprise Linux (RHEL) 6 frissítés 7
  * CentOS 6 frissítés 7
* A konfigurációs kiszolgáló és az RX konzolok mostantól a pár értesítéseit jelenítik meg, amelyek bittérképes módba lépnek.
* Az RX-ben a következő biztonsági javítások kerültek hozzáadásra:
    * Az engedélyezés megkerülése a paraméterek manipulálásával: Korlátozott hozzáférés a nem alkalmazható felhasználók számára.
    * Helyek közötti kérelem hamisítás: Az oldal-token koncepció valósult meg, és véletlenszerűen generál minden oldalon. Ez azt jelenti, hogy csak egy bejelentkezési példány van ugyanahhoz a felhasználóhoz, és az oldalfrissítés nem működik. Ehelyett átirányítja az irányítópultra.
    * Rosszindulatú fájlfeltöltés: A fájlok meghatározott kiterjesztésekre korlátozódnak: z, aiff, asf, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, log, mid, mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb, rtf , sdc, sitd, swf, sxc, sxw, kátrány, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml, és zip.
    * Állandó helyek közötti parancsfájlok: Bemeneti érvényesítések kerültek hozzáadásra.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Az Azure Site Recovery Scout 8.0.1 2.

A 2.

* **Konfigurációs kiszolgáló:** Problémák, amelyek megakadályozták, hogy a 31 napos szabad mérési funkció a várt módon működjön, amikor a konfigurációs kiszolgáló regisztrálva van az Azure Site Recovery tárolóban.
* **Egyesített ügynök**: Javítsa ki az 1.

### <a name="azure-site-recovery-scout-801-update-1"></a>Az Azure Site Recovery Scout 8.0.1-es frissítése
Az 1.

* 31 nap ingyenes védelem kiszolgálópéldányonként. Ez lehetővé teszi a funkciók tesztelését vagy a koncepció igazolásának beállítását.
* A kiszolgálón minden művelet, beleértve a feladatátvételt és a feladat-visszavételt is, az első 31 napban ingyenes. Az idő akkor kezdődik, amikor a kiszolgáló először védett Site Recovery Scout. A 32.
* A jelenleg felszámított védett kiszolgálók száma bármikor elérhető a tároló **irányítópultján.**
* A vSphere parancssori felület (vCLI) 5.5 2.-es frissítése támogatással bővült.
* A forráskiszolgálón található Linux operációs rendszerek támogatása:
    * RHEL 6 frissítés 6
    * RHEL 5 frissítés 11
    * CentOS 6 frissítés 6
    * CentOS 5 frissítés 11
* Hibajavítások a következő problémák megoldására:
  * A tároló regisztrációja sikertelen a konfigurációs kiszolgálón vagy az RX-kiszolgálón.
  * A fürtkötetek nem a várt módon jelennek meg, amikor a fürtözött virtuális gépek újra védettek, ahogy azok folytatódnak.
  * A feladat-visszavétel sikertelen lesz, ha a fő célkiszolgáló a helyszíni éles virtuális gépektől eltérő ESXi-kiszolgálón található.
  * A konfigurációs fájl engedélyei a 8.0.1-es frissítéskor módosulnak. Ez a módosítás hatással van a védelemre és a műveletekre.
  * Az újraszinkronizálási küszöbérték nem a várt módon van kikényszerítve, ami inkonzisztens replikációs viselkedést okoz.
  * Az RPO-beállítások nem jelennek meg megfelelően a konfigurációs kiszolgáló konzolján. A tömörítetlen adatérték helytelenül mutatja a tömörített értéket.
  * Az Eltávolítás művelet nem a vContinuum varázsló várt módon törlődik, és a replikáció nem törlődik a konfigurációs kiszolgáló konzoljáról.
  * A vContinuum varázslóban a lemez automatikusan ki lesz jelölve, amikor az MSCS-virtuális gépek védelme során a Lemeznézetben a **Részletek** gombra kattint.
  * A fizikai-virtuális (P2V) forgatókönyv, a szükséges HP-szolgáltatások (például a CIMnotify és a CqMgHost) nem kerülnek át manuális virtuálisgép-helyreállítás. Ez a probléma további rendszerindítási időt eredményez.
  * A Linux virtuálisgép-védelem sikertelen, ha a fő célkiszolgálón 26-nál több lemez található.

