---
title: "VMware virtuális gépek vagy fizikai kiszolgálók egy másodlagos helyre, az Azure Site Recovery vész-helyreállítási beállítása |} Microsoft Docs"
description: "Megtudhatja, hogyan állíthatja be a VMware virtuális gépeket, vagy a Windows és Linux fizikai kiszolgálókon, egy másodlagos helyre, az Azure Site Recovery vész-helyreállítási."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: gauarvd
editor: 
ms.assetid: 68616d15-398c-4f40-8323-17b6ae1e65c0
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: raynew
ms.openlocfilehash: 503d7060437d08ed35681fca7f1b9306746b7f44
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2017
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Helyszíni VMware virtuális gépek vagy fizikai kiszolgálók egy másodlagos helyre vész-helyreállítási beállítása

Az InMage Scout [Azure Site Recovery](site-recovery-overview.md) biztosít a valós idejű replikációs között a helyszíni VMware helyek. Azure Site Recovery szolgáltatás előfizetések InMage Scout tartalmazza. 


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- [Felülvizsgálati](site-recovery-support-matrix-to-sec-site.md) a támogatási követelmények lévő valamennyi összetevőnél.
- Győződjön meg arról, hogy a replikálni kívánt gépek ahhoz [replikált gép támogatási](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions).


## <a name="create-a-vault"></a>Tároló létrehozása

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="choose-a-protection-goal"></a>Válassza ki a védelmi cél

Kiválasztása replikálja, valamint replikálni úgy, hogy a helyét.

1. Kattintson a **Site Recovery** > **infrastruktúra előkészítése** > **védelmi cél**.
2. Válassza ki **helyreállítási hely** > **Igen, amelyen a VMware vSphere Hipervizorra**. Ezután kattintson az **OK** gombra.
3. A **Scout telepítő**, InMage Scout 8.0.1 GA szoftver, és a regisztrációs kulcs letöltése. A telepítési fájlok lévő valamennyi összetevőnél szerepelnek a letöltött zip-fájlt.

## <a name="download-and-install-component-updates"></a>Összetevő-frissítések letöltése és telepítése

 Tekintse át és telepítse a legújabb [frissítések](#updates). Frissítéseket kell telepíteni a kiszolgálókon a következő sorrendben:

1. (Ha van ilyen) a RX kiszolgáló
2. Konfigurációs kiszolgálók
3. Folyamat kiszolgálók
4. A fő célkiszolgálón kiszolgálók
5. vContinuum-kiszolgáló
6. (A Windows és Linux-kiszolgálókon) a forráskiszolgálón

A frissítések telepítése az alábbiak szerint:

> [!NOTE]
>Minden Scout összetevők frissítés verzióját nem lehet ugyanaz a frissítés .zip-fájlban. A régebbi jelzi, hogy az összetevő nem módosult a frissítés az előző frissítés óta.

Töltse le a [frissítése](https://aka.ms/asr-scout-update6) .zip fájl. A fájl a következő összetevőkből áll: 
  - RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.GZ
  - CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe
  - UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
  - UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
  - vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe
  - EE update4 bits RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
1. Bontsa ki a .zip fájlt.
2. **Kiszolgáló a RX**: másolás **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** a RX kiszolgálóra, és bontsa ki. A kibontott mappát, futtassa **/telepítése**.
3. **Konfigurációs kiszolgáló és a folyamatkiszolgáló**: másolás **CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe** a konfigurációs kiszolgáló és a folyamatkiszolgáló. Kattintson duplán a futtatáshoz.<br>
4. **Windows fő célkiszolgáló**: a unified agent frissítéséhez másolása **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** a kiszolgálóra. Kattintson duplán a való futtatáshoz. Egyesített ügynök frissítése esetén is alkalmazható a forráskiszolgálón. Ha a forrás frissítése 4 még nem frissítették, frissítenie kell a unified agent.
  A frissítés nem kell alkalmazni a fő cél a előkészített **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** , ez ugyanis, hogy a legújabb módosításokkal új GA telepítő.
5. **vContinuum-kiszolgáló**: másolás **vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe** a kiszolgálóra.  Győződjön meg arról, hogy a vContinuum varázsló bezárt. Kattintson duplán a futtatható fájl.
    A frissítés nem kell alkalmazni a fő célkiszolgálón a előkészített **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** , ez ugyanis, hogy a legújabb módosításokkal új GA telepítő.
6. **Linux fő célkiszolgáló**: a unified agent frissítéséhez másolása **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** a fő célkiszolgáló, és bontsa ki. A kibontott mappát, futtassa **/telepítése**.
7. **A forráskiszolgáló Windows**: a unified agent frissítéséhez másolása **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** a forráskiszolgálóra. Kattintson duplán a futtatható fájl. 
    Nem kell a frissítés 5-ügynök telepítése a forráskiszolgálón, ha már frissítették Update 4 vagy a forrás-ügynök telepítve van a legújabb kiinduló installer **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe**.
8. **Linux-forráskiszolgáló**: a unified agent frissítéséhez a unified agent fájl megfelelő verziója a Linux-kiszolgálóra másolja, majd bontsa ki. A kibontott mappát, futtassa **/telepítése**.  Példa: RHEL 6,7 64 bites kiszolgálót, másolja át **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** a kiszolgálóra, és bontsa ki. A kibontott mappát, futtassa **/telepítése**.

## <a name="enable-replication"></a>A replikáció engedélyezése

1. Állítsa be a forrás közötti replikációt, és a cél a VMware-hely.
2. Tekintse meg a következő dokumentumok további információt a telepítési, védelmi és helyreállítási:

   * [Kibocsátási megjegyzések](https://aka.ms/asr-scout-release-notes)
   * [Kompatibilitási mátrix](https://aka.ms/asr-scout-cm)
   * [Felhasználói útmutató](https://aka.ms/asr-scout-user-guide)
   * [Útmutató a RX felhasználó](https://aka.ms/asr-scout-rx-user-guide)
   * [A gyors telepítési útmutató](https://aka.ms/asr-scout-quick-install-guide)

## <a name="updates"></a>Frissítések

### <a name="site-recovery-scout-801-update-6"></a>Site Recovery Scout 8.0.1 frissítés 6 
Frissített: Október 12, 2017

Töltse le [Scout frissítés 6](https://aka.ms/asr-scout-update6).

6 Scout frissítés az összesítő frissítés. Frissítés 5 és az új javítások és továbbfejlesztett funkcióit az alábbiakban az 1. frissítést minden javításokat tartalmaz. 

#### <a name="new-platform-support"></a>Új eszközplatform-támogatás
* Már támogatja az adatforrás a Windows Server 2016
* Már támogatja a következő Linux operációs rendszereken:
    - Red Hat Enterprise Linux (RHEL) 6.9.
    - CentOS 6.9.
    - Oracle Linux 5.11
    - Oracle Linux 6.8
* Már támogatja a VMware Center 6.5

> [!NOTE]
> * Alap Agent(UA) az egységes telepítő Windows lett támogatásához a Windows Server 2016 frissíteni. Az új telepítő **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe** Scout GA ALAPCSOMAG együtt van csomagolva (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). Az azonos telepítőn minden támogatott Windows-verzióhoz használható. 
> * Kiinduló Windows vContinuum & fő célkiszolgáló lett, hogy a telepítő frissíteni a Windows Server 2016 támogatásához. Az új telepítő **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** Scout GA ALAPCSOMAG együtt van csomagolva (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). Az azonos telepítőn Windows 2016 fő célkiszolgáló és a Windows 2012R2 fő célkiszolgáló telepítésére használható.
> * A GA csomag letöltéséről a portálon, a [hozzon létre egy tárolót](#create-a-vault).
>

#### <a name="bug-fixes-and-enhancements"></a>Hibajavításokat és fejlesztések
- Feladat-visszavétel védelem meghiúsul a Linux virtuális gép replikálása lemezek listáját nincs megadva konfigurációs végén.

### <a name="site-recovery-scout-801-update-5"></a>Hely helyreállítási Scout 8.0.1 5. frissítése
5. Scout frissítése az összesítő frissítés. Update 4-es frissítés 1-től minden javítások, valamint az alább ismertetett új javítások tartalmazza.
- A Site Recovery Scout Update 4 javítások történtek, a frissítés 5 kifejezetten a fő célkiszolgáló és a vContinuum összetevők vonatkoznak.
- Ha az adatforrás-kiszolgálók, a fő célkiszolgáló, konfigurációs, folyamat, és kiszolgálók a RX már fut az Update 4, majd alkalmazhatja azt csak a fő célkiszolgálón. 

#### <a name="new-platform-support"></a>Új eszközplatform-támogatás
* SUSE Linux Enterprise Server 11 szervizcsomag 4(SP4)
* SLES 11 SP4 64 bites **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** Scout GA ALAPCSOMAG együtt van csomagolva (**InMage_Scout_Standard_8.0.1 GA.zip**). A GA csomag letöltéséről a portálon, a [hozzon létre egy tárolót](#create-a-vault).


#### <a name="bug-fixes-and-enhancements"></a>Hibajavításokat és fejlesztések

* Megnövelt Windows-fürtszolgáltatás javításokat megbízhatóság támogatja:
    * Rögzített – a P2V MSCS némelyike fürtlemezekhez válik a helyreállítás után RAW.
    * A lemez rendelés eltérése miatt nem sikerül fixed-P2V MSCS fürt helyreállítani.
    * Rögzített - az MSCS a fürt hozzáadása a lemezek meghiúsul a lemez mérete eltérés műveletet.
    * Rögzített a készültségi ellenőrizze, hogy a forrás MSCS-fürtökben RDM LUN-leképezéssel mérete ellenőrzése sikertelen.
    * Fixed-egyetlen csomópont fürt védelme egy SCSI-eltérés probléma miatt sikertelen. 
    * A P2V Windows fürtkiszolgáló fixed-az újbóli védelem sikertelen lesz, ha a cél fürtlemezek szerepelnek. 
    
* Rögzített: Során feladat-visszavétel védelem, ha a kijelölt fő célkiszolgáló nem szerepel a védett forrásgép megegyező ESXi-kiszolgálóhoz (során előre védelemmel), majd vContinuum szerzi be a megfelelő fő célkiszolgáló és a helyreállítási feladat-visszavétel, a helyreállítási a művelet sikertelen lesz.

> [!NOTE]
> * A P2V-fürt javítások csak a hely helyreállítási Scout frissítés 5 újonnan védett fizikai MSCS-fürtök vonatkoznak. A fürt javítások régebbi frissítésekkel védett P2V MSCS fürtökön telepítéséhez hajtsa végre a frissítési lépéseket 12 részben a [hely helyreállítási Scout kibocsátási megjegyzések](https://aka.ms/asr-scout-release-notes).
> * Ha az újbóli védelem idején ugyanazokat a lemezek nem aktív mindegyik fürtcsomópont mint az eredetileg védett, majd egy fizikai MSCS-fürtökben az újbóli védelem csak meglévő céllemezek is felhasználhatja. Ha nem, használja a manuális módszerrel 12 szakaszában [hely helyreállítási Scout kibocsátási megjegyzések](https://aka.ms/asr-scout-release-notes), a céllemezek ügyféloldali áthelyezése a megfelelő datastore elérési útját, során az újbóli védelem újbóli. Ha Ön lássa el újból védelemmel az MSCS-fürtökben P2V módban a frissítési lépések nélkül, a célkiszolgáló ESXi létrehoz egy új lemezt. Szüksége lesz az adattároló manuálisan törölje a régi lemezt.
> * Ha a forrás SLES11 vagy SLES11 (és bármilyen szervizcsomag) kiszolgáló szabályosan újraindul, majd manuális megjelöléséhez a **legfelső szintű** lemez replikációs párok újbóli szinkronizáláshoz. A felhasználó nem kap értesítést a CX felületen. Ha nem jelöli be a legfelső szintű lemez az újraszinkronizálás, megfigyelheti az adatok épségével kapcsolatos problémákat.


### <a name="azure-site-recovery-scout-801-update-4"></a>Az Azure Site Recovery Scout 8.0.1 Update 4
Scout Update 4 az összesítő frissítés. Minden javítások történtek az 1. frissítést a Update 3 és az alábbiakban ismertetett új javításokat tartalmaz.

#### <a name="new-platform-support"></a>Új eszközplatform-támogatás

* Már támogatja a vCenter vagy vSphere 6.0, 6.1 és 6.2
* Már támogatja a fenti Linux operációs rendszerek:
  * Red Hat Enterprise Linux (RHEL) 7.0, 7.1-es és 7.2
  * 7.0, 7.1-es és 7.2 centOS
  * Red Hat Enterprise Linux (RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> RHEL/CentOS 7 64 bites **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** Scout GA ALAPCSOMAG együtt van csomagolva **InMage_Scout_Standard_8.0.1 GA.zip**. A Scout GA csomag letöltéséről a portálon, a [hozzon létre egy tárolót](#create-a-vault).

#### <a name="bug-fixes-and-enhancements"></a>Hibajavításokat és fejlesztések

* A következő Linux operációs rendszerek és klónok nemkívánatos újbóli szinkronizálási előforduló problémák megelőzéséhez kezelésének továbbfejlesztett Leállítás:
    * Red Hat Enterprise Linux (RHEL) 6.x
    * Oracle Linux (OL) 6.x
* A Linux minden mappahozzáférési engedélyek a unified agent telepítési könyvtárban most csak a helyi felhasználói korlátozódik.
* A Windows, egy javítást alkalmaztunk a problémát, amely közös elosztott konzisztencia könyvjelzők kiállításához történt időtúllépés a terhelve elosztott alkalmazások, például az SQL Server és a megosztási pont fürtök.
* A napló kapcsolódó javítsa ki a konfigurációs kiszolgáló alap telepítőben.
* A letöltési hivatkozás a VMware vCLI 6.0 a fő célkiszolgáló alap Windows installer lett adva.
* További ellenőrzéseket és a naplókat a rendszer adott, a hálózati konfigurációs módosításokat feladatátvétel és vész-helyreállítási csukja során.
* Egy javítást alkalmaztunk megőrzési nem jelentendő információkat és a konfigurációs kiszolgáló okozó problémát.  
* Fizikai fürtök esetén egy javítást alkalmaztunk a kötet átméretezése, sikertelen lehet a vContinuum varázslóban, ha a forráskötet zsugorítását okozó problémát.
* Egy javítást alkalmaztunk a fürt védelmi hiba, amely a hiba miatt sikertelen volt: "Nem található a lemezaláírását", amikor a fürt lemez egy PRDM lemez.
* Egy javítást alkalmaztunk a cxps átviteli kiszolgáló összeomlik vagy out tartományon kívüli kivétel okozta.
* Kiszolgáló neve és IP-cím oszlopot is méretezhető a a **leküldéses ügyféltelepítés** a vContinuum varázsló.
* Fejlesztések a RX API:
  * Az öt legújabb elérhető közös konzisztencia most rendelkezésre álló pont (csak a címkék garantált).
  * Kapacitás és szabad terület részletei jelennek meg az összes védett eszköz.
  * A forráskiszolgálón Scout illesztőprogram állapotba áll rendelkezésre.

> [!NOTE]
> * **InMage_Scout_Standard_8.0.1_GA.zip** ALAPCSOMAG rendelkezik:
    * Egy frissített konfigurációs alap telepítője (**InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
    * A fő célkiszolgáló alap Windows installer (**InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**).
    * Minden új telepítések esetén használja az új konfigurációs kiszolgáló és a Windows a fő célkiszolgáló GA bit.
> * 4. frissítés közvetlenül 8.0.1 alkalmazható GA
> * A konfigurációs kiszolgáló és a RX frissítések után is érvényben nem állítható.


### <a name="azure-site-recovery-scout-801-update-3"></a>Az Azure Site Recovery Scout 8.0.1 Update 3

Összes helyreállítás esetben összegző frissítésről. Frissítés 3 Update 1 és 2. frissítést minden javításait tartalmazzák. Frissítés 3 közvetlenül alkalmazható 8.0.1 GA A konfigurációs kiszolgáló és a RX frissítések után is érvényben nem állítható.

#### <a name="bug-fixes-and-enhancements"></a>Hibajavításokat és fejlesztések
Frissítés 3 javítja a következő problémákat:

* A konfigurációs kiszolgáló és a RX nem a tárolóban regisztrált ha azok a proxykiszolgáló mögött található.
* A jelentés nem frissül az órát, amelyben a helyreállítási időkorlát (RPO) nem érhető el.
* A konfigurációs kiszolgáló nincs szinkronizálás a RX amikor az ESX hardveres jellemzőit, vagy hálózati adatok bármely UTF-8 karaktereket tartalmaz.
* Windows Server 2008 R2 rendszerű tartományvezérlők ne indítsa el a helyreállítás után.
* Kapcsolat nélküli szinkronizálás nem a várt módon működik.
* Virtuális gép feladatátvétele után replikációs érpáras törlése nem előrehaladás a konfigurációs kiszolgáló konzolon hosszú ideig. Felhasználók nem a feladat-visszavétel befejezése, és folytathatja műveleteket.
* A teljes pillanatkép műveleteket a konzisztencia-feladatot a optimalizált, például az SQL Server-ügyfeleken leválasztja alkalmazás csökkentése érdekében.
* Konzisztencia eszköz (VACP.exe) teljesítménye javult. A Windows pillanatképek létrehozásához szükséges memória használata csökkent.
* A leküldéses telepítés szolgáltatás összeomlik, ha a jelszó nem nagyobb, mint 16 karakter hosszúságú lehet.
* vContinuum nem ellenőrizze, és új vCenter hitelesítőadat, bekéri a hitelesítő adatok módosításakor.
* Linux a fő célkiszolgáló gyorsítótár-kezelő (cachemgr) nem fájlok letöltése adatok a folyamatkiszolgálótól. Az eredmény replikációs pár sávszélesség-szabályozás.
* Ha a fizikai feladatátvevő fürtöt (MSCS) lemez sorrendje nem ugyanaz az összes olyan csomóponton, replikációs a fürtkötetek részénél nincs beállítva. A fürt kell látható el újra védelemmel javítás kihasználásához.  
* SMTP-funkciókat az elvárt módon, nem működik, után a RX Scout 8.0.1 Scout 7.1 legyen frissítve.
* További statisztika a naplóban a visszaállítási művelet, nyomon követheti az befejezéséhez szükséges idő hozzáadva.
* Már támogatja a Linux operációs rendszereken a forráskiszolgálón:
  * Red Hat Enterprise Linux (RHEL) 6 frissítés 7
  * CentOS 6 7 frissítése
* A konfigurációs kiszolgáló és a RX konzolok most értesítési megjelenítése a pár, amelyek bitkép üzemmódba.
* A következő biztonsági javításokat a RX bővült:
    * Engedélyezési megkerülése keresztül illetéktelen paraméter: nem megfelelő felhasználók korlátozott hozzáféréssel.
    * Webhelyközi kérések hamisítására: A lap-jogkivonat koncepció lett megvalósítva, és minden oldalon véletlenszerűen létrehozott. Ez azt jelenti, hogy csak egy bejelentkezési példányban a felhasználónak, és a lap frissítése nem működik. Ehelyett az átirányítja az irányítópulton.
    * Rosszindulatú fájlfeltöltés: fájlok csak bizonyos bővítmények: z, aiff, az ASP, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, napló mid, mov, mp3, mp4, Microsoft termékkód, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, erőforrás-kezelő, rmi, rmvb, rtf , sdc, sitd, swf, sxc, sxw, bont, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml és zip.
    * Állandó többhelyes scripting: bemeneti érvényesítést lettek hozzáadva.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Az Azure Site Recovery Scout 8.0.1 frissítés 2 (a 03 frissítés Dec 15)

2. frissítés javításairól a következők:

* **Konfigurációs kiszolgáló**: problémákat, amelyek miatt sikertelen volt, a 31 napos ingyenes mérési várt, a konfigurációs kiszolgáló sikeresen regisztrálva a Site Recovery szolgáltatás működését.
* **Az Unified agent**: Javítsa ki a Update 1, amelyek a frissítés nincs telepítve a fő célkiszolgálón, hogy 8.0.1 8.0-s verziójáról a frissítés során hibát.

### <a name="azure-site-recovery-scout-801-update-1"></a>Az Azure Site Recovery Scout 8.0.1 Update 1
1. frissítés a következő hibajavításokat tartalmaz, és új funkcióit tartalmazza:

* 31 napos ingyenes védelmet egy kiszolgáló-példány. Ez lehetővé teszi a funkció tesztelése, vagy egy-a-koncepció igazolása beállításához.
* Az első 31 napra vonatkozó összes műveleteket végez a kiszolgálón, beleértve a feladatátvételi és a feladat-visszavétel szabadon. Az idő kezdődik, amikor egy kiszolgáló hely helyreállítási Scout először védi. 32 napjától egyes védett kiszolgálókon a Site Recovery által védett felhasználói tulajdonú helyhez szabványos példány díj számítjuk fel.
* Bármikor, jelenleg felszámított védett kiszolgálók számát úgy érhető el a **irányítópult** a tárolóban lévő állapottal.
* Támogatást kaptak a vSphere parancssori felület (vCLI) 5.5 Update 2.
* Támogatást kaptak a Linux operációs rendszerek a forráskiszolgálón:
    * RHEL 6 6 frissítése
    * RHEL 5 11 frissítése
    * A centOS 6 frissítés 6
    * CentOS 5 frissítés 11
* A következő problémák hibajavítások:
  * Tároló regisztrálása meghiúsul, a konfigurációs kiszolgáló, vagy a RX kiszolgáló.
  * Fürtkötetek nem jelenik meg a várt fürtözött virtuális gépek vannak látható el újra védelemmel, akkor folytassa.
  * Feladat-visszavétel sikertelen lesz, amikor a fő célkiszolgáló üzemelteti a helyszíni virtuális gépek gyártási eltérő ESXi-kiszolgálón.
  * Konfigurációs fájl engedélyei módosulnak 8.0.1 történő frissítésekor. Ez a módosítás hatással van a védelem és a műveletek.
  * Az újraszinkronizálás küszöbérték nem lépnek érvénybe, elvárás, amely inkonzisztens replikáció működését.
  * A helyreállítási Időkorlátra vonatkozó beállításait nem jelennek meg helyesen a konfigurációs kiszolgáló konzol. A kibontott adatok értékeinek helytelenül jeleníti meg a tömörített értékét.
  * Az eltávolítási művelet nem a várt módon a vContinuum varázsló törli, és a replikáció nem törlődik a konfigurációs kiszolgáló konzolján.
  * A vContinuum varázsló lemez automatikusan ki nem jelölt kattintva **részletek** a lemez nézetben MSCS virtuális gépek védelme során.
  * A fizikai-virtuális (P2V) forgatókönyv esetén szükséges HP szolgáltatások (például CIMnotify és CqMgHost) a virtuális gép helyreállítási kézi áthelyezése nem. A probléma további rendszerindítás eredményez.
  * Linux virtuális gép védelme nem működik, ha több mint 26 lemezeket a fő célkiszolgálón.

