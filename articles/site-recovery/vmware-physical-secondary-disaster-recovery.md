---
title: VMware virtuális gépek vagy fizikai kiszolgálók másodlagos helyre az Azure Site Recoveryvel történő vészhelyreállításának beállítása |} A Microsoft Docs
description: Ismerje meg, hogyan állítható be, valamint VMware virtuális gépeket, vagy a Windows és Linux rendszerű fizikai kiszolgálókat, az Azure Site Recovery egy másodlagos helyre.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 12/31/2018
ms.author: raynew
ms.openlocfilehash: 2467da8d5a87a3a9325b807aec48c584ab0197cb
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54079102"
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>A helyszíni VMware virtuális gépek vagy fizikai kiszolgálók másodlagos helyre történő vészhelyreállításának beállítása

Az InMage Scout [Azure Site Recovery](site-recovery-overview.md) védekezés valós idejű között a helyszíni VMware-webhelyek. Az InMage Scout segédprogramot az Azure Site Recovery szolgáltatás előfizetések tartalmazza.

## <a name="end-of-support-announcement"></a>Teljes támogatása bejelentés

Az Azure Site Recovery a forgatókönyvben a helyszíni VMware vagy fizikai adatközpontok közötti replikáció eléri-e célból támogatása.

-   A 2018 augusztus a forgatókönyv nem lehet konfigurálni a helyreállítási tárban, és nem tölthető le az InMage Scout szoftver a tárolóból. Meglévő üzemelő példányok is támogatott lesz. 
-   A December 31-ig 2020-ra a forgatókönyv nem lesznek támogatva.
- Meglévő partnerek új ügyfelek bevonásának forgatókönyvet is, amíg támogatása véget ér.

2018-as és a 2019 során két frissítések elérhető lesz: 

-   7. frissítés: Hálózati konfigurációs és megfelelőségi problémák, és a TLS 1.2 támogatást biztosít.
-   8. frissítés: A Linux operációs rendszerek RHEL/CentOS 7.3/7.4/7.5 és a SUSE-12 támogatása

8. frissítés, miután további frissítések elérhető lesz. Korlátozott gyorsjavítási támogatás hozzáadva a frissítés 8 operációs rendszerek és hibajavításokat tartalmaz, a lehető legjobb alapján lesz.

Az Azure Site Recovery továbbra is lehetővé teszi az ügyfelek VMware és Hyper-V zökkenőmentes és a legjobb minőségű DRaaS az Azure-t egy vész-helyreállítási webhelyként villámgyors innovációt hajthat végre. A Microsoft azt javasolja, hogy a már InMage / ASR Scout ügyfelek fontolja meg az Azure Site Recovery VMware – Azure forgatókönyvek esetében az üzletmenet-folytonossági kell. Az Azure Site Recovery VMware – Azure forgatókönyvére egy nagyvállalati szintű Vészhelyreállítási megoldás az alkalmazások VMware RPO és RTO perc kínáló támogatása több virtuális gépes alkalmazás replikálását és helyreállítását, zökkenőmentes bevezetése átfogó figyelést, és jelentős előnye a teljes bekerülési Költséget.

### <a name="scenario-migration"></a>Áttelepítési forgatókönyv
Alternatív megoldásként a vészhelyreállítás beállítása helyszíni VMware virtuális gépek és fizikai gépek az Azure-bA replikálásával javasoljuk. Ehhez a következő:

1.  Tekintse át az alábbi gyors összehasonlítást. Lehetőség van a helyszíni gépeket replikálni, mielőtt szüksége van-e, ellenőrizze, hogy megfelelnek-e [követelmények](./vmware-physical-azure-support-matrix.md#replicated-machines) számára az Azure-bA. Ha VMware virtuális gépeket replikál, azt javasoljuk, hogy tekintse át [kapacitástervezési útmutató](./site-recovery-plan-capacity-vmware.md), és futtassa a [Deployment Planner eszköz](./site-recovery-deployment-planner.md) az identitás kapacitásigények, és a megfelelőség ellenőrzése.
2.  A Deployment Planner futtatásának után állíthatja be a replikációs: o VMware virtuális gépeket, hajtsa végre az ezekben az oktatóanyagokban a [készítheti elő az Azure](./tutorial-prepare-azure.md), [a helyszíni VMware-környezet előkészítése a](./vmware-azure-tutorial-prepare-on-premises.md), és [beállítása vész-helyreállítási](./vmware-azure-tutorial-prepare-on-premises.md).
fizikai gépek o kövesse ezt [oktatóanyag](./physical-azure-disaster-recovery.md).
3.  Gépek az Azure-bA replikál, után futtathatja egy [vészhelyreállítási próba végrehajtása](./site-recovery-test-failover-to-azure.md) , győződjön meg arról, hogy minden a várt módon működik.

### <a name="quick-comparison"></a>Gyors összehasonlítása

**Funkció** | **Az Azure-bA** |**VMware-adatközpontok közötti replikáció**
--|--|--
**Szükséges összetevők** |Mobilitási szolgáltatás a replikált gépek. A helyszíni konfigurációs kiszolgáló, folyamatkiszolgáló, fő célkiszolgáló. Ideiglenes folyamatkiszolgáló az Azure-ban feladat-visszavételhez.|Mobilitási szolgáltatás, a Folyamatkiszolgáló, a konfigurációs kiszolgáló és a fő célkiszolgáló
**Konfigurációs és vezénylési** |Recovery Services-tárolót az Azure Portalon | VContinuum használatával 
**Replikált**|Lemez (Windows és Linux) |Kötet – Windows<br> Lemez – Linux
**Megosztott lemezfürt**|Nem támogatott|Támogatott
**Data churn korlátait (átlag)** |Lemezenként 10 MB/s-adatok<br> Virtuális gépenként 25MB/s-adatok<br> [További információ](./site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) | > 10 MB/s adatok lemezenként  <br> Virtuális gépenként > 25 MB/s-adatok
**Monitorozás** |Az Azure Portalról|A CX (konfigurációs kiszolgáló)
**Támogatási mátrix**| [Kattintson ide a részletekért](./vmware-physical-azure-support-matrix.md)|[Töltse le az ASR Scout kompatibilis mátrix](https://aka.ms/asr-scout-cm)


## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez:

- [Felülvizsgálat](vmware-physical-secondary-support-matrix.md) a támogatási követelmények valamennyi összetevője számára.
- Győződjön meg arról, hogy a replikálni kívánt gépek megfelelnek-e a [replikált gépek támogatása](vmware-physical-secondary-support-matrix.md#replicated-vm-support).


## <a name="download-and-install-component-updates"></a>Töltse le és telepítse az összetevő-frissítései

 Tekintse át, és telepítse a legújabb [frissítések](#updates). A következő sorrendben kiszolgálók frissítéseket kell telepíteni:

1. RX kiszolgálón (ha van)
2. Konfigurációs kiszolgálók
3. Folyamatkiszolgálók
4. A fő célkiszolgáló kiszolgálók
5. vContinuum-kiszolgáló
6. Forráskiszolgáló (Windows és Linux-kiszolgálók)

A frissítések a következőképpen telepítheti:

> [!NOTE]
>Minden a Scout összetevőinek fájl frissítési verziója nem lehet ugyanaz a frissítés .zip-fájlban. A régebbi verziót azt jelzik, hogy nem történik változás az összetevő a frissítés az előző frissítés óta.

Töltse le a [frissítése](https://aka.ms/asr-scout-update7) .zip-fájlt. A fájl tartalmazza az összes, az alap bináris fájljait és összegző frissítés bináris fájljait a következő összetevők: 
  - InMage_ScoutCloud_RX_8.0.1.0_RHEL6-64_GA_02Mar2015.tar.gz
  - RX_8.0.7.0_GA_Update_7_2965621_28Dec18.tar.GZ
  - InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe
  - InMage_CX_TP_8.0.1.0_Windows_GA_26Feb2015_release.exe
  - CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe
  - InMage_PI_8.0.1.0_Windows_GA_26Feb2015_release.exe
  - InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe
  - InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe
  - InMage_UA_8.0.7.0_OL5-32_GA_03Dec2018_release.tar.gz
  - InMage_UA_8.0.7.0_OL5-64_GA_03Dec2018_release.tar.gz
  - InMage_UA_8.0.7.0_OL6-32_GA_03Dec2018_release.tar.gz
  - InMage_UA_8.0.7.0_OL6-64_GA_03Dec2018_release.tar.gz
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
2. **RX kiszolgáló**: Másolás **RX_8.0.7.0_GA_Update_7_2965621_28Dec18.tar.gz** a RX kiszolgálóra, és csomagolja ki. A kibontott mappát, futtassa a **/Install**.
3. **Konfigurációs kiszolgáló és a folyamatkiszolgáló**: Másolás **CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe** a konfigurációs kiszolgáló és a folyamatkiszolgáló. Kattintson duplán a futtatáshoz.<br>
4. **Windows fő célkiszolgáló**: Az egyesített ügynök frissítése, másolása **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** a kiszolgálóhoz. Kattintson duplán a futtatáshoz. Ugyanebben a fájlban a friss telepítés is használható. Az azonos egységes ügynök frissítése akkor is alkalmazható a forráskiszolgálón.
  A frissítést nem szükséges a alkalmazni a fő cél az előkészített **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe** , ez az új GA telepítő a legújabb módosításokkal.
5. **vContinuum-kiszolgáló**:  Másolás **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe** a kiszolgálóhoz.  Győződjön meg arról, hogy bezárt a vContinuum varázslóját. Kattintson duplán a fájlra a futtatáshoz.
6. **Linux rendszerű fő célkiszolgáló**: Az egyesített ügynök frissítése, másolása **InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz** a Linuxos fő célkiszolgáló, és csomagolja ki. A kibontott mappát, futtassa a **/Install**.
7. **A forráskiszolgáló Windows**: Az egyesített ügynök frissítése, másolása **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** a forráskiszolgálón. Kattintson duplán a fájlra a futtatáshoz. 
8. **Linux-forráskiszolgálón**: Az egyesített ügynök frissítése, másolja az egyesített ügynök fájl megfelelő verziója a Linux-kiszolgálón, és csomagolja. A kibontott mappát, futtassa a **/Install**.  Példa: Az RHEL 6.7 server 64 bites, a másolási **InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz** a kiszolgálóra, és csomagolja ki. A kibontott mappát, futtassa a **/Install**.
9. A fent említett telepítők a konfigurációs kiszolgáló, Folyamatkiszolgáló és RX kiszolgáló frissítés után a PHP és MySQL-kódtárakat a lépéseket manuálisan kell frissíteni kell a [szakasz](#manual-upgrade-for-php-and-mysql-on-cs-ps-and-rx).

## <a name="enable-replication"></a>A replikáció engedélyezése

1. Beállítja a replikációt a forrás között, és célozhat meg VMware-webhelyek.
2. További információ a telepítés, védelem és helyreállítás a következő dokumentumokban talál:

   * [Kibocsátási megjegyzések](https://aka.ms/asr-scout-release-notes)
   * [Kompatibilitási mátrix](https://aka.ms/asr-scout-cm)
   * [Felhasználói útmutató](https://aka.ms/asr-scout-user-guide)
   * [RX felhasználói útmutató](https://aka.ms/asr-scout-rx-user-guide)
   * [Gyors telepítési útmutató](https://aka.ms/asr-scout-quick-install-guide)
   * [MYSQL és PHP-kódtárak frissítése](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade)

## <a name="updates"></a>Frissítések

### <a name="site-recovery-scout-801-update-7"></a>Site Recovery Scout 8.0.1 7. frissítés 
Frissítve: 2018. december 31-ig töltse le [Scout frissítés 7](https://aka.ms/asr-scout-update7).
Scout frissítés 7 el egy teljes telepítő, amely feltárhatja, hogy frissítse a meglévő ügynökök/MT korábbi frissítéseket (frissítési 1 – 6. frissítés) tartoznak, valamint friss telepítéséhez használható. 6. frissítés és az új javítások és fejlesztések az alábbiakban az 1. frissítés minden javításokat tartalmaz.
 
#### <a name="new-features"></a>Új funkciók
* PCI-megfelelőség
* A TLS 1.2-es verzió támogatása

#### <a name="bug-and-security-fixes"></a>És a biztonsági javítások
* Kijavítva: Windows-fürt/önálló gépek helytelen IP-konfiguráció esetén a helyreállítási/működéseinek rendelkezik.
* Kijavítva: Néha hozzáadás lemez művelet sikertelen lesz, V2V fürt számára.
* Kijavítva: vContinuum varázsló elakad helyreállítási fázis során a fő célkiszolgálón a Windows Server 2016 esetén
* Kijavítva: Biztonsági kérdések MySQL hivatottak MySQL 5.7.23 verziójára való frissítését

#### <a name="manual-upgrade-for-php-and-mysql-on-csps-and-rx"></a>A PHP és MySQL CS, PS és RX manuális frissítése
A konfigurációs kiszolgáló, Folyamatkiszolgáló és RX kiszolgáló 7.2.10 verzió a PHP-parancsfájl-kezelési platform kell frissíteni.
Konfigurációs kiszolgáló, Folyamatkiszolgáló és RX Server verziója 5.7.23 kell frissíteni a MySQL-adatbázis-kezelő rendszer.
Megadott manaual lépések végrehajtásával a [rövid telepítési útmutatót](https://aka.ms/asr-scout-quick-install-guide) a PHP és MySQL-verziók frissítésére.

### <a name="site-recovery-scout-801-update-6"></a>Site Recovery Scout 8.0.1 frissítése 6 
Frissítve: 2017. október 12.

Töltse le [Scout 6. összesített frissítéssel](https://aka.ms/asr-scout-update6).

6. összesített frissítéssel Scout az összesítő frissítés. Az 1. frissítés Update 5 és az új javítások és fejlesztések az alább ismertetett összes javításokat tartalmaz. 

#### <a name="new-platform-support"></a>Új eszközplatform-támogatás
* Támogatási hozzá van adva forrás a Windows Server 2016-hoz
* A következő Linux operációs rendszerek már támogatja:
    - Red Hat Enterprise Linux (RHEL) 6.9.
    - CentOS 6.9.
    - Oracle Linux 5.11.
    - Oracle Linux 6.8
* Már támogatja a VMware Center 6.5

A frissítések a következőképpen telepítheti:

> [!NOTE]
>Minden a Scout összetevőinek fájl frissítési verziója nem lehet ugyanaz a frissítés .zip-fájlban. A régebbi verziót azt jelzik, hogy nem történik változás az összetevő a frissítés az előző frissítés óta.

Töltse le a [frissítése](https://aka.ms/asr-scout-update6) .zip-fájlt. A fájl a következő összetevőket tartalmazza: 
  - RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
  - CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe
  - UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
  - UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
  - vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe
  - UA update4 bits RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
1. Bontsa ki a .zip fájlokat.
2. **RX kiszolgáló**: Másolás **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** a RX kiszolgálóra, és csomagolja ki. A kibontott mappát, futtassa a **/Install**.
3. **Konfigurációs kiszolgáló és a folyamatkiszolgáló**: Másolás **CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe** a konfigurációs kiszolgáló és a folyamatkiszolgáló. Kattintson duplán a futtatáshoz.<br>
4. **Windows fő célkiszolgáló**: Az egyesített ügynök frissítése, másolása **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** a kiszolgálóhoz. Kattintson duplán a futtatáshoz. Az azonos egységes ügynök frissítése akkor is alkalmazható a forráskiszolgálón. Ha a forrás még nem lett frissítve, a 4-es frissítést, frissítenie kell az egyesített ügynök.
  A frissítést nem szükséges a alkalmazni a fő cél az előkészített **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** , ez az új GA telepítő a legújabb módosításokkal.
5. **vContinuum-kiszolgáló**:  Másolás **vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe** a kiszolgálóhoz.  Győződjön meg arról, hogy bezárt a vContinuum varázslóját. Kattintson duplán a fájlra a futtatáshoz.
    A frissítést nem szükséges a fő célkiszolgálón előkészítve a alkalmazni **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** mivel ez új a végleges verzió telepítőjét a érintő legújabb változásokat.
6. **Linux rendszerű fő célkiszolgáló**: Az egyesített ügynök frissítése, másolása **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** a fő célkiszolgáló és csomagolja ki. A kibontott mappát, futtassa a **/Install**.
7. **A forráskiszolgáló Windows**: Az egyesített ügynök frissítése, másolása **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** a forráskiszolgálón. Kattintson duplán a fájlra a futtatáshoz. 
    Nem kell a frissítés 5-ügynök telepítése a forráskiszolgálón, ha már frissítették Update 4 vagy a forrás-ügynök telepítve van a legújabb kiinduló installer **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe**.
8. **Linux-forráskiszolgálón**: Az egyesített ügynök frissítése, másolja az egyesített ügynök fájl megfelelő verziója a Linux-kiszolgálón, és csomagolja. A kibontott mappát, futtassa a **/Install**.  Példa: Az RHEL 6.7 server 64 bites, a másolási **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** a kiszolgálóra, és csomagolja ki. A kibontott mappát, futtassa a **/Install**.


> [!NOTE]
> * Alap Agent(UA) egyesített telepítő Windows lett frissítve a támogatási szolgálatának a Windows Server 2016-ban. Az új telepítő **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe** a Scout általánosan elérhető alapszintű csomag együtt van csomagolva (**InMage_Scout_Standard_8.0.1 a GA-Oct17.zip**). Az azonos telepítő minden támogatott Windows-verzió lesz használható. 
> * Alap Windows vContinuum és a fő célkiszolgáló lett, hogy a telepítő támogatási szolgálatának a Windows Server 2016-ra frissíteni. Az új telepítő **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** a Scout általánosan elérhető alapszintű csomag együtt van csomagolva (**InMage_Scout_Standard_8.0.1 a GA-Oct17.zip**). Az azonos telepítő Windows 2016 a fő célkiszolgáló és a Windows 2012 R2 a fő célkiszolgáló telepítéséhez használható.
> * ASR Scout által nem támogatott a Windows server 2016-ban, a fizikai kiszolgálón. Csak a Windows Server 2016 VMware virtuális gép támogatja. 
>

#### <a name="bug-fixes-and-enhancements"></a>Hibajavítások és fejlesztések
- Replikálandó lemezek listájával Linux rendszerű virtuális gép feladat-visszavétel védelem sikertelen je prázdná config végén.

### <a name="site-recovery-scout-801-update-5"></a>Site Recovery Scout 8.0.1 5. frissítése
Scout Update 5 a kumulatív frissítés. Update 4-es frissítés 1-től az összes javításokat, és az alább ismertetett új javítások tartalmazza.
- Hibajavítások. a Site Recovery Scout Update 4 Update 5 kifejezetten a fő cél- és a vContinuum-összetevőket a rendszer.
- Ha az adatforrás-kiszolgálók, a fő célkiszolgáló, konfigurációs, folyamat és RX kiszolgálók Update 4 már fut, akkor csak a fő célkiszolgáló a alkalmazni azt. 

#### <a name="new-platform-support"></a>Új eszközplatform-támogatás
* SUSE Linux Enterprise Server 11. szervizcsomag 4(SP4)
* SLES 11 SP4 64 bites **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** a Scout általánosan elérhető alapszintű csomag együtt van csomagolva (**InMage_Scout_Standard_8.0.1 GA.zip**). Töltse le a GA-csomagot a portálról, leírtak szerint [hozzon létre egy tárolót](#create-a-vault).


#### <a name="bug-fixes-and-enhancements"></a>Hibajavítások és fejlesztések

* Megnövelt Windows-fürtszolgáltatás javításait megbízhatóság támogatják:
    * Kijavítva – a P2V MSCS némelyike fürt válnak lemezek RAW helyreállítás után.
    * A lemez rendelés eltérése miatt nem sikerül fixed-P2V MSCS fürt helyreállítani.
    * A fürt való hozzáadása lemezek sikertelen lesz, és a egy lemez méretű verzióeltérési hiba kijavítva – az MSCS.
    * Kijavítva a készültségi ellenőrizze, hogy a forrás RDM LUN-ok leképezése MSCS-fürt méretének ellenőrzése sikertelen.
    * Fixed-egyetlen csomópont fürt védelme egy SCSI eltérése miatt nem működik. 
    * A P2V Windows cluster server fixed-ismételt védelme sikertelen lesz, ha a cél fürtlemezek szerepelnek. 
    
* Kijavítva: Feladat-visszavétel védelem, során, ha a kijelölt fő célkiszolgáló nem található a védett forrásgép megegyező ESXi kiszolgálóra (során előre védelem), majd vContinuum szerzi be a megfelelő fő célkiszolgáló és a helyreállítási feladat-visszavételt, a helyreállítás a művelet sikertelen lesz.

> [!NOTE]
> * A P2V-fürt javítások csak az újonnan védett a Site Recovery Scout Update 5 fizikai MSCS-fürtök vonatkoznak. A fürt javítások a védett P2V MSCS-fürtökben régebbi frissítések telepítéséhez hajtsa végre a frissítési lépéseket szakaszában 12 a [Site Recovery Scout kibocsátási megjegyzések](https://aka.ms/asr-scout-release-notes).
> * Ha ismételt védelem idején ugyanazokat a lemezek aktív mindegyik fürtcsomópont, amikor az eredetileg védett, majd egy fizikai MSCS-fürt ismételt védelem csak felhasználhatja meglévő lemezeit. Ha nem, majd kövesse a manuális szakaszában 12 [Site Recovery Scout kibocsátási megjegyzések](https://aka.ms/asr-scout-release-notes), a cél egymás lemezek áthelyezése a megfelelő adattároló elérési útját, újból ismételt védelem során. A P2V módban MSCS-fürt frissítési lépések nélkül ismételt védelme, ha a céloldali ESXi-kiszolgálón létrehoz egy új lemezt. Szüksége lesz a régi lemezt manuálisan törölje az adattárhoz.
> * Ha a forrás SLES11 vagy SLES11 (az összes szervizcsomaggal) kiszolgáló szabályosan újraindul, majd manuális megjelöléséhez a **legfelső szintű** lemez replikációs párok újbóli szinkronizáláshoz. Nincs a CX felületen nem kap értesítést. Ha a legfelső szintű lemez újraszinkronizálásra nem jelöli be, megfigyelheti az adatok épségével kapcsolatos problémákat.


### <a name="azure-site-recovery-scout-801-update-4"></a>Az Azure Site Recovery Scout 8.0.1 Update 4
4. frissítéssel Scout az összesítő frissítés. Ez magában foglalja az összes javítások történtek az 1. frissítés Update 3 és az alább ismertetett új javítások.

#### <a name="new-platform-support"></a>Új eszközplatform-támogatás

* Már támogatja a vCenter/vsphere-6.0, 6.1-es és 6.2
* A Linux operációs rendszer már támogatja:
  * Red Hat Enterprise Linux (RHEL) 7.0, 7.1-es és 7.2
  * 7.0, 7.1-es és 7.2 centOS
  * Red Hat Enterprise Linux (RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> RHEL/CentOS 7 64 bites **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** a Scout általánosan elérhető alapszintű csomag együtt van csomagolva **InMage_Scout_Standard_8.0.1 GA.zip**. A Scout a GA-csomag letöltése a portálról, leírtak szerint [hozzon létre egy tárolót](#create-a-vault).

#### <a name="bug-fixes-and-enhancements"></a>Hibajavítások és fejlesztések

* Továbbfejlesztett leállítás kezelése a következő Linux operációs rendszerek és klónozásával jár, nemkívánatos újraszinkronizálás problémák elkerülése érdekében:
    * Red Hat Enterprise Linux (RHEL) 6.x
    * Oracle Linux (OL) 6.x
* Linux esetén az egyesített ügynöktelepítési könyvtár összes mappa hozzáférési engedélyeket csak a helyi felhasználói most már korlátozódik.
* A Windows, egy közös, elosztott konzisztencia könyvjelzők, kiadásakor fellépő probléma időtúllépése javítja a terhelve elosztott alkalmazások, például az SQL Server és SharePoint-fürtöket.
* A napló a konfigurációs kiszolgáló alap telepítőjében javítás kapcsolatos.
* A VMware vCLI 6.0 egy letöltési hivatkozást a fő célkiszolgáló alap Windows installer lett hozzáadva.
* További ellenőrzési és naplók lettek hozzáadva, a feladatátvétel és a vészhelyreállítási próbák során a hálózati konfigurációs módosításokat.
* Egy javít egy problémát okozó megőrzési információk nem jelenthető legyen a konfigurációs kiszolgálóhoz.  
* Fizikai fürtök esetén a kötet átméretezése a vContinuum varázslóját a forráskötet összezsugorítása során meghibásodását okozó probléma megoldását.
* A hiba miatt sikertelen volt egy fürt védelmi problémák megoldását: "Nem található a lemez(ek) aláírása", ha a fürtözött lemez-e a PRDM lemez.
* Egy cxps átviteli kiszolgáló összeomlik, egy kibővített tartományon kívüli kivétel okozza a megoldását.
* Kiszolgáló neve és IP-cím oszlopok immár a méretezhető a **Ügyfélleküldéses telepítés** vContinuum lapján.
* RX API fejlesztései:
  * Az öt legújabb elérhető közös konzisztencia most már rendelkezésre álló pont (csak garantált címkék).
  * Kapacitás és szabad terület részletei jelennek meg az összes védett eszköz.
  * A forráskiszolgálón a Scout illesztőprogram állapot érhető el.

> [!NOTE]
> * **InMage_Scout_Standard_8.0.1_GA.zip** alapszintű csomag:
    * Egy frissített konfigurációs kiszolgáló alap telepítője (**InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
    * Egy fő célkiszolgáló alap Windows installer (**InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**).
    * Minden új telepítések esetén használja az új konfigurációs kiszolgáló és a fő célkiszolgáló általánosan elérhető a bits Windows.
> * 4. frissítés akkor alkalmazható, közvetlenül a 8.0.1 általánosan elérhető
> * A konfigurációs kiszolgáló és a frissítések RX majd, miután a alkalmazni már nem állítható.


### <a name="azure-site-recovery-scout-801-update-3"></a>Az Azure Site Recovery Scout 8.0.1 3-as frissítés

Site Recovery-hírek összeadódnak. 3. frissítés Update 1 és 2. frissítés minden javításokat tartalmaz. 3. frissítés közvetlenül alkalmazható 8.0.1 általánosan elérhető A konfigurációs kiszolgáló és a frissítések RX majd, miután a alkalmazni már nem állítható.

#### <a name="bug-fixes-and-enhancements"></a>Hibajavítások és fejlesztések
3. frissítés az alábbi problémákat javítja:

* A konfigurációs kiszolgáló és a RX aspektusnevek regisztrálva a tároló Ha a proxy mögött.
* Az állapotjelentés, amelyben a helyreállításipont-célkitűzés (RPO) elérte a nem órák száma nem frissül.
* A konfigurációs kiszolgáló nincs szinkronizálva RX ezzel esetén az ESX-alapú hardverek részletei vagy szerepelnek hálózati adatok, tartalmazhat UTF-8 karaktereket.
* A Windows Server 2008 R2 rendszerű tartományvezérlők ne indítsa el a helyreállítás után.
* Kapcsolat nélküli szinkronizálás nem működik megfelelően.
* Virtuális gép a feladatátvételt követően replikációs-pár törlése nem halad a konfigurációs kiszolgáló konzol a hosszú ideig. A felhasználók nem a feladat-visszavétel befejezése vagy folytatni működését.
* A teljes pillanatkép-készítési műveletek a konzisztencia feladat által optimalizált forráskötetekben, csökkentheti az alkalmazás időnként például az SQL Server-ügyfeleken.
* Konzisztencia (VACP.exe) eszköz teljesítménye javult. A memóriahasználat pillanatképeket létrehozni a Windows csökkentett szükséges.
* Leküldéses telepítési szolgáltatás összeomlik, ha a jelszó, 16 karakternél.
* vContinuum nem ellenőrizze, és kérheti a új vCenter hitelesítő adatokat, hitelesítő adatok módosításakor.
* A Linux a fő célkiszolgáló gyorsítótár-kezelő (cachemgr) nem fájlok letöltése a folyamatkiszolgálóról. Az eredmény replikációs pár szabályozás.
* Ha a fizikai feladatátvevő fürt: (MSCS) lemezrendelését nem ugyanaz az összes csomóponton, replikációs néhány a fürt kötetén nincs beállítva. A fürt kell újra javítás előnyeinek kihasználása érdekében.  
* SMTP-funkciók nem várt módon működik, RX Scout 7.1 Scout 8.0.1 frissítése után.
* További statisztika lettek hozzáadva a visszaállítási művelethez, nyomon követéséhez, végrehajtásához szükséges idő a naplóban.
* A forráskiszolgálón a Linux operációs rendszerekhez már támogatja:
  * Red Hat Enterprise Linux (RHEL) 6 7. frissítés
  * CentOS 6 7 frissítése
* A konfigurációs kiszolgáló és a RX-konzolok most az értesítések megjelenítése a párhoz, amely bitkép üzemmódba.
* A következő biztonsági javításokat a RX lettek hozzáadva:
    * Engedélyezési megkerülése keresztül paraméter illetéktelen módosítása: Korlátozott hozzáférés a nem megfelelő felhasználók számára.
    * Webhelyközi kérések hamisításának megakadályozása: A lap-token fogalma lett megvalósítva, és minden oldalon véletlenszerűen létrehozott. Ez azt jelenti, hogy csak egy egyszeri bejelentkezési példánya adott felhasználó számára, és a lap frissítése nem működik. Ehelyett átirányítja a az irányítópulthoz.
    * Kártevő fájl feltöltése: Fájlok korlátozva, az egyes bővítményekről: z, aiff, az ASP, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, log, mid mov, mp3, mp4, mpc, mpeg, mpg, ods odt, pdf, png, ppt, pptx, pxd, qt, ram, a rar, erőforrás-kezelő, rmi, rmvb, rtf, sdc, sitd, swf , sxc, sxw, tar, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml és zip.
    * Állandó többhelyes scripting: A bemeneti ellenőrzések bővült.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Az Azure Site Recovery Scout 8.0.1 (a 03 frissítést december 15) 2-es frissítés

A 2. frissítésben javításokat tartalmazza:

* **Konfigurációs kiszolgáló**: Problémák, amelyek ebben az esetben a 31 napos ingyenes mérési funkciója megfelelően működik, amikor a konfigurációs kiszolgáló regisztrálva lett az Azure Site Recovery-tároló használata.
* **Egyesített ügynök**: Javítsa ki a Update 1, melyek kiváltották a frissítés nincs telepítve a fő célkiszolgálón a 8.0.1 8.0-s verziójáról a frissítés során egy adott probléma megoldásához.

### <a name="azure-site-recovery-scout-801-update-1"></a>Az Azure Site Recovery Scout 8.0.1 1. frissítés
1. frissítés a következő hibajavítások és új funkciókat tartalmazza:

* 31 napig ingyenes védelem különbséglemezekhez. Ez lehetővé teszi, hogy a funkció tesztelése, vagy hozzon létre egy proof-of-concept.
* Az első 31 napra vonatkozó összes műveletet a kiszolgálón, beleértve a feladatátvétel és feladat-visszavétel használata ingyenes. Az idő a kiszolgáló első védett a Site Recovery Scout indul el. A 32 nap, a védett kiszolgáló díja felhasználói tulajdonú helyre a Site Recovery általi védelem standard szintű példány díjának.
* Bármikor, jelenleg ezekért a védett kiszolgálók száma érhető el a **irányítópult** a tárolóban.
* Támogatási 5.5-ös frissítés 2 vsphere parancssori felület (vCLI) lett hozzáadva.
* Támogatás hozzáadva ezen Linux operációs rendszerek a forráskiszolgálón:
    * RHEL 6 6 frissítése
    * RHEL 5 11 frissítése
    * CentOS 6 6. összesített frissítéssel
    * CentOS 5 frissítés 11
* Hibajavítások a következő problémák:
  * Tár regisztrálása sikertelen lesz, a konfigurációs kiszolgáló vagy a RX kiszolgáló.
  * Fürt kötetek nem jelennek meg a várt Ha fürtözött virtuális gépek ismételt védelme, azok folytatása.
  * Feladat-visszavétel sikertelen lesz, amikor a fő célkiszolgáló ESXi-kiszolgálón egy másik, a helyszíni éles üzemű virtuális gépekre üzemel.
  * Konfigurációs fájl engedélyei módosulnak 8.0.1 történő frissítéskor. Ez a módosítás érinti, védelmi és műveletek.
  * Az újraszinkronizálás küszöbérték megfelelően működik, konzisztens replikációs viselkedést okozó nincs kényszerítve.
  * A helyreállítási Időkorlát-beállítások nem jelennek meg helyesen a konfigurációs kiszolgáló konzol. A tömörítetlen adat értéke helytelenül a tömörített értéket mutatja.
  * Az eltávolítási művelet nem törli a vContinuum varázslóját a várt módon, és a replikáció nem törlődik a konfigurációs kiszolgáló konzolról.
  * A vContinuum varázslóját a lemez automatikusan ki nem jelölt kattintva **részletek** a lemez nézetben MSCS virtuális gépek védelme során.
  * A fizikai-virtuális (P2V) forgatókönyvben szükséges HP szolgáltatások (például CIMnotify és CqMgHost) a virtuális gép helyreállítási manuális nem helyezi. A probléma további rendszerindító időt eredményez.
  * Linux rendszerű virtuális gép védelme nem működik, ha több mint 26 lemezeket a fő célkiszolgálón.

