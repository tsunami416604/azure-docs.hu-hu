---
title: "VMware virtuális gépek vagy fizikai kiszolgálók egy másodlagos helyre vész-helyreállítási beállítása |} Microsoft Docs"
description: "Ez a cikk ismerteti a helyszíni VMware virtuális gépek vagy windowsos/Linuxos fizikai kiszolgálók replikálása egy másodlagos helyre, az Azure Site Recovery szolgáltatással."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: b2cba944-d3b4-473c-8d97-9945c7eabf63
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2017
ms.author: raynew
ms.openlocfilehash: 8cfaa56735c1f4e2e01b58fdde2ad0e77b388762
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="set-up-disaster-recovery-of-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>VMware virtuális gépek vagy fizikai kiszolgálók egy másodlagos helyre vész-helyreállítási beállítása


Az Azure Site Recovery InMage Scout biztosít a valós idejű, a helyszíni VMware helyek közötti replikáció. Azure Site Recovery szolgáltatás előfizetések InMage Scout tartalmazza.

Ha nem rendelkezik Azure-előfizetéssel, [ingyenes fiók létrehozását](https://azure.microsoft.com/pricing/free-trial/) megkezdése előtt.


## <a name="create-a-vault"></a>Tároló létrehozása
1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/) > **Recovery Services** szolgáltatásba.
2. Új kattintson > felügyeleti > biztonsági mentés és helyreállítás (OMS). Másik lehetőségként kattinthat a Tallózás > Recovery Services-tároló > Hozzáadás.
3. A **Név** mezőben adjon meg egy, a tárolót azonosító rövid nevet. Ha egynél több előfizetéssel rendelkezik, válasszon egyet ezek közül.
4. A **erőforráscsoport** hozzon létre egy új erőforráscsoportot, vagy válasszon egy meglévőt. Adjon meg egy Azure-régiót végezze el a kötelező mezőket.
5. A **hely**, válassza ki a tároló földrajzi területét. Annak ellenőrzéséhez, régiók, lásd: [Azure Site Recovery Díjszabásáról](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Ha szeretne gyors hozzáférést a tárolóhoz az irányítópultról kattintson a rögzítés az irányítópulton, és majd kattintson a Létrehozás gombra.
7. Az új tároló megjelenik az irányítópulton > összes erőforrást, és a fő Recovery Services-tárolók lap.

## <a name="configure-the-vault-and-download-inmage-scout-components"></a>A tároló konfigurálása és InMage Scout összetevők letöltése
1. A helyreállítási szolgáltatások tárolók lapon válassza ki a tároló, kattintson a **beállítások**.
2. A **beállítások** > **bevezetés** kattintson **Site Recovery** > 1. lépés: **infrastruktúra előkészítése** > **védelmi cél**.
3. A **védelmi cél** válassza a helyreállítási helyen, és válassza az Igen, amelyen a VMware vSphere Hipervizorra. Ezután kattintson az OK gombra.
4. A **Scout telepítő**, kattintson a letöltés a letöltési InMage Scout 8.0.1 GA szoftver és a regisztrációs kulcsot. A telepítő a szükséges összetevőket fájljai a letöltött .zip-fájlban.

## <a name="step-3-install-component-updates"></a>3. lépés: Az összetevő-frissítések telepítése
Olvassa el a legújabb [frissítések](#updates). A kiszolgálók frissítési fájlok lesz telepítése a következő sorrendben:

1. Ha szükséges a RX kiszolgáló
2. Konfigurációs kiszolgálók
3. Folyamat kiszolgálók
4. Fő célkiszolgálóra
5. vContinuum-kiszolgáló
6. A forráskiszolgálón (a Windows és Linux-kiszolgáló)

A frissítések telepítése az alábbiak szerint:

1. Töltse le a [frissítése](https://aka.ms/asr-scout-update5) .zip fájl. A .zip fájl a következő fájlokat tartalmazza:

   * RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.GZ
   * CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe
   * UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
   * UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
   * vCon_Windows_8.0.5.0_GA_Update_5_11525767_20Apr17.exe
   * EE update4 bits RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
2. Bontsa ki a .zip fájlt.<br>
3. **A RX kiszolgáló**: másolás **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** a RX kiszolgálóra, és bontsa ki. A kibontott mappát, futtassa **/telepítése**.<br>
4. **A konfigurációs kiszolgáló/folyamat kiszolgáló**: másolás **CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe** a konfigurációs kiszolgáló és a folyamatkiszolgáló. Kattintson duplán a futtatáshoz.<br>
5. **A Windows fő célkiszolgáló**: a unified agent frissítéséhez másolása **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** a fő célkiszolgálóhoz. Kattintson duplán a való futtatáshoz. Vegye figyelembe, hogy a unified agent is alkalmazható a forráskiszolgáló Ha forrás Update4 vége nem frissül. Akkor kell telepítenie a forráskiszolgálón és, ahogy azt korábban említettük, a listában szereplő később.<br>
6. **A vContinuum-kiszolgáló**: másolás **vCon_Windows_8.0.5.0_GA_Update_5_11525767_20Apr17.exe** a vContinuum-kiszolgáló számára.  Győződjön meg arról, hogy a vContinuum varázsló bezárt. Kattintson duplán a futtatható fájl.<br>
7. **A Linux fő célkiszolgáló**: a unified agent frissítéséhez másolása **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** a fő célkiszolgáló, és bontsa ki. A kibontott mappát, futtassa **/telepítése**.<br>
8. **A Windows forráskiszolgáló**: frissítés 5-ügynök telepítése Update 4 már fut. Ha a forrás nem igényelnek. Ha kevesebb, mint Update 4 fut, alkalmazza a frissítés 5-ügynök.
Másolja a unified agent frissítéséhez **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** a forráskiszolgálóra. Kattintson duplán a való futtatáshoz. <br>
9. **A Linux rendszerű forráskiszolgálóhoz**: a unified agent frissítéséhez EE-fájl megfelelő verziója a Linux-kiszolgálóra másolja, majd bontsa ki. A kibontott mappát, futtassa **/telepítése**.  Példa: RHEL 6,7 64 bites kiszolgálót, másolja át **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** a kiszolgálóra, és bontsa ki. A kibontott mappát, futtassa **/telepítése**.

## <a name="step-4-set-up-replication"></a>4. lépés: A replikáció beállítása
1. Állítsa be a forrás közötti replikációt, és a cél a VMware-hely.
2. Útmutatásért használja az InMage Scout dokumentáció, és letöltődik a termékkel. Másik lehetőségként a dokumentáció az alábbiak szerint végezheti el:

   * [Kibocsátási megjegyzések](https://aka.ms/asr-scout-release-notes)
   * [Kompatibilitási mátrix](https://aka.ms/asr-scout-cm)
   * [Felhasználói útmutató](https://aka.ms/asr-scout-user-guide)
   * [Útmutató a RX felhasználó](https://aka.ms/asr-scout-rx-user-guide)
   * [A gyors telepítési útmutató](https://aka.ms/asr-scout-quick-install-guide)

## <a name="updates"></a>Frissítések
### <a name="azure-site-recovery-scout-801-update-5"></a>Az Azure Site Recovery Scout 8.0.1 5. frissítése
5. Scout frissítése az összesítő frissítés. Az 1. frissítés vége: update4 és a következő új hibajavításokat és fejlesztések a javításokat tartalmaz.
A Site Recovery Scout update4 update5 hozzáadott javítások fő célkiszolgáló és a vContinuum-összetevők vonatkoznak. Ha a forrás kiszolgálóinak, a fő célkiszolgálón, a konfigurációs kiszolgáló, a Folyamatkiszolgáló és a RX már a Site Recovery Scout update4 majd szüksége frissítés 5 csak a fő célkiszolgálón. 

**Új eszközplatform-támogatás**
* SUSE Linux Enterprise Server 11 szervizcsomag 4(SP4)

> [!NOTE]
> SLES 11 SP4 64 bites **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** ALAPCSOMAG Scout GA együtt van csomagolva **InMage_Scout_Standard_8.0.1 GA.zip**. Töltse le a Scout GA csomag portálról a [1. lépés](#step-1-create-a-vault).
>

**Hibajavításokat és fejlesztések**

* Windows-fürt támogatása megbízhatóságának növelése
    * Rögzített-bizonyos néhány a P2V MSCS fürtlemezekhez válik a helyreállítás után RAW
    * Fixed-P2V MSCS helyreállítása sikertelen lesz, lemez rendelés eltérése miatt.
    * A művelet sikertelen, és a lemez méretének eltérése lemezek hozzáadása a Fixed-MSCS-fürtökben
    * Fixed-forrás MSCS fürt RDM LUN-ok leképezése készültség-ellenőrzés sikertelen, a méret ellenőrzése
    * Fixed-egyetlen csomópont fürt védelme SCSI eltérés probléma miatt nem működik 
    * A P2V Windows fürtkiszolgáló ismételt fixed-védelemmel sikertelen lesz, ha a cél fürtlemezek szerepelnek. 
    
* Során feladat-visszavétel védelem, ha a kijelölt fő Célkiszolgáló nem ugyanazon a kiszolgálón ESXi legyen, mint a védett forrásgép (során előre védelemmel), majd vContinuum szerzi be a megfelelő MT helyreállítási feladat-visszavétel során, és ezt követően a helyreállítási művelet sikertelen lesz.

> [!NOTE]
> 
> * P2V fürt fent javítások alkalmazhatók csak adott fizikai MSCS-fürtök és hely helyreállítási Scout update5 frissen védett. További bejelentkezések nélkül hozzáférhet a fürt kijavítja az már védett P2V MSCS fürtön régebbi frissítésekkel, a frissítési lépések az 12 szakaszban ismertetett szüksége, frissítés védett Scout frissítés 5 P2V MSCS fürtöket a [kibocsátási megjegyzéseket](https://aka.ms/asr-scout-release-notes) .
> 
> * Állítsa be a fizikai MSCS-fürt védelmét is felhasználhatja meglévő céllemezek, csak ha időpontjában az újbóli védelem, ugyanazokat a lemezek aktív a fürt csomópontjain mint az eredetileg védett. Ha nem, majd manuális lépések amit 12 részben a [kibocsátási megjegyzéseket](https://aka.ms/asr-scout-release-notes) helyezhető át a céllemezek ügyféloldali újból használni őket az újbóli védelem alatt az megfelelő datastore elérési útra. Ha Ön lássa el újból védelemmel az MSCS-fürtökben P2V módban frissítési lépések nélkül majd hoz létre az új lemez a célként megadott ESXi-kiszolgálón. Manuálisan törölje a régi lemezt az adattárral kell.
> 
> * Amikor forrás SLES11 vagy bármely service pack kiszolgálóval SLES11 szabályosan újraindítása után, majd egy manuálisan kell megjelölni a **legfelső szintű** lemez replikációs pár szinkronizálja újra, akkor nem jelenik meg értesítés CX felhasználói felületén. Ha nem jelöli be a legfelső szintű lemezt újraszinkronizálási, megjelenik az adatok integritását (DI) kapcsolatos problémákat.
> 

### <a name="azure-site-recovery-scout-801-update-4"></a>Az Azure Site Recovery Scout 8.0.1 Update 4
Scout Update 4 az összesítő frissítés. Az 1. frissítés vége: update3 és a következő új hibajavításokat és fejlesztések a javításokat tartalmaz.

**Új eszközplatform-támogatás**

* Már támogatja a vCenter vagy vSphere 6.0, 6.1 és 6.2
* Már támogatja a következő Linux operációs rendszerek
  * Red Hat Enterprise Linux (RHEL) 7.0, 7.1-es és 7.2
  * 7.0, 7.1-es és 7.2 centOS
  * Red Hat Enterprise Linux (RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> RHEL/CentOS 7 64 bites **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** ALAPCSOMAG Scout GA együtt van csomagolva **InMage_Scout_Standard_8.0.1 GA.zip**. Töltse le a Scout GA csomag portálról a [1. lépés](#step-1-create-a-vault).
>
>

**Hibajavításokat és fejlesztések**

* Továbbfejlesztett leállítási kezelése a következő Linux operációs rendszer és a klónok nemkívánatos nteljes előforduló problémák megelőzéséhez.
  * Red Hat Enterprise Linux (RHEL) 6.x
  * Oracle Linux (OL) 6.x
* Linux végezze el a mappahozzáférési engedélyek szükségesek a unified agent telepítési könyvtára most korlátozódik csak a helyi felhasználói.
* A Windows közös elosztott konzisztencia könyvjelző elküldése a fokozottan során probléma csatornainicializálásnak elosztott alkalmazások, például az SQL és megosztási pont fürtök betöltve.
* A hozzáadott napló kapcsolódó javítás CX alap telepítő.
* VMware vCLI 6.0 letöltési hivatkozás Windows fő célkiszolgáló alap telepítő kerül.
* További ellenőrzéseket és naplókat a további hálózati konfigurációkat módosítások hozzá feladatátvételi és vész-Helyreállítási csukja során.
* A CX nem bejelentések bizonyos megőrzési információkat.  
* Fizikai fürt kötet vContinuum varázsló lépéseinek újra méretezés művelet sikertelen lesz, amikor a forrás kötet zsugorítás következik be.
* Fürt védelem sikertelen volt, hiba: "Nem található a lemezaláírását" Ha a fürt lemez PRDM lemez.
* cxps server összeomlása átviteli out tartományon kívüli kivétel miatt.
* Kiszolgáló neve és IP-oszlopok is méretezhető vContinuum varázsló leküldéses telepítés lapján.
* Fejlesztések a RX API
  * Öt legújabb elérhető közös konzisztencia pontokat biztosít (csak a garantált címkék).
  * Kapacitás és szabad terület részleteket biztosít a védett eszközök.
  * Ez a Scout illesztőprogram állapot forráskiszolgáló.

> [!NOTE]
> * **InMage_Scout_Standard_8.0.1_GA.zip** ALAPCSOMAG most már frissítve van CX alap telepítő **InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe** és a Windows a fő célkiszolgálón alap installer **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**. Minden új telepítésre használja új CX és a Windows a fő célkiszolgálón GA bits.
> * 4. frissítés közvetlenül alkalmazható 8.0.1 GA
> * A konfigurációs kiszolgáló és a RX frissítések után a rendszer alkalmazza azokat a még nem állítható.
>
>

### <a name="azure-site-recovery-scout-801-update-3"></a>Az Azure Site Recovery Scout 8.0.1 Update 3
3 frissítés a következő hibajavításokat és fejlesztéseket tartalmazza:

* A konfigurációs kiszolgáló és a RX regisztrációja elmarad való a Site Recovery-tárolóban, ha azok a proxykiszolgáló mögött található.
* Az órát, hogy a helyreállítási időkorlát (RPO) még nem teljesült nem frissíti a az állapotjelentést.
* A konfigurációs kiszolgáló nem szinkronizálja a RX, amikor az ESX hardveres jellemzőit vagy hálózati adatok bármely UTF-8 karaktereket tartalmaz.
* Windows Server 2008 R2 rendszerű tartományvezérlők nem tudnak indítsa el a helyreállítás után.
* Kapcsolat nélküli szinkronizálás nem a várt módon működik.
* Virtuális gép (VM) feladatátvétel után replikációs érpáras törlés lekérdezi elakadt a CX felhasználói felületén hosszú ideig, és felhasználók nem tudják a feladat-visszavétel befejezése, és folytassa a műveletet.
* Pillanatkép a konzisztencia-feladat által végrehajtott műveletek optimalizált alkalmazás csökkentése érdekében a teljes SQL-ügyfelek például leválasztja.
* A teljesítmény, a konzisztencia eszköz (VACP.exe) csökkenti a memóriahasználatot is szükséges a Windows pillanatképeinek javult.
* A leküldéses telepítés szolgáltatás összeomlik, több mint 16 karakter jelszó esetén.
* vContinuum nincs ellenőrzése és új vCenter hitelesítő adatokat kér, ha a hitelesítő adatok módosításakor.
* Linux a fő célkiszolgáló gyorsítótár-kezelő (cachemgr) van nem fájlok letöltését a folyamatkiszolgáló, amely replikációs pár sávszélesség-szabályozás eredményez.
* Ha a fizikai feladatátvevő fürtöt (MSCS) lemez sorrendje nem azonos a csomópontokon, replikációs nincs beállítva a fürt kötetek közül.
  <br/>Vegye figyelembe, hogy a fürt kell látható el újra védelemmel javítás kihasználásához.  
* SMTP nem működőképes, miután a RX Scout 8.0.1 Scout 7.1 legyen frissítve.
* További statisztikák hozzá lett adva a naplóban a visszaállítási művelet befejeződését, tett idejének nyomon követésére.
* Már támogatja a Linux operációs rendszereken a forráskiszolgálón:
  * Red Hat Enterprise Linux (RHEL) 6 frissítés 7
  * CentOS 6 7 frissítése
* A CX és a RX felhasználói felülete most megjelenítése az értesítés a pár, amelyek bitkép üzemmódba.
* A következő biztonsági javításokat a RX bővült:

| **A probléma leírása** | **Eljárások végrehajtása** |
| --- | --- |
| Engedélyezési megkerülése paraméter illetéktelen keresztül |Korlátozott hozzáférés a nem megfelelő felhasználók számára. |
| Webhelyközi kérések hamisításának megakadályozása |A lap-token koncepció, amely minden oldalon véletlenszerűen generálja megvalósítva. <br/>Ez jelenik meg: <li> Csak egyetlen bejelentkezési példánya a felhasználónak van.</li><li>Oldal frissítési nem működik – ez az irányítópult fogja átirányítani Önt.</li> |
| Rosszindulatú fájl feltöltése |Korlátozott fájlok az egyes bővítmények. A bővítmények engedélyezett: 7z, aiff, ASP avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, napló mid, mov, mp3, mp4, Microsoft termékkód, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, erőforrás-kezelő, rmi, rmvb, rtf, sdc, sitd, swf, sxc, sxw, bont, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml, és zip. |
| Állandó többhelyes parancsfájlok |Hozzáadja a bemeneti érvényesítést. |

> [!NOTE]
> * Összes helyreállítás esetben összegző frissítésről. Frissítés 3 rendelkezik Update 1 és 2. frissítés a javításokat. Frissítés 3 közvetlenül alkalmazható 8.0.1 GA
> * A konfigurációs kiszolgáló és a RX frissítések után a rendszer alkalmazza azokat a még nem állítható.
>
>

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Az Azure Site Recovery Scout 8.0.1 frissítés 2 (a 03 frissítés Dec 15)
2. frissítés javításairól a következők:

* **Konfigurációs kiszolgáló**: Javítsa ki a hibát, amely miatt nem sikerült a 31 napos ingyenes mérési szolgáltatás működését a konfigurációs kiszolgáló sikeresen regisztrálva a Site Recovery várt.
* **Az Unified agent**: Javítsa ki a Update 1, a frissítés nincs telepítve a fő célkiszolgáló frissítésekor az volt a 8.0.1 8.0-s verziójáról eredményező kapcsolatos problémát.

### <a name="azure-site-recovery-scout-801-update-1"></a>Az Azure Site Recovery Scout 8.0.1 Update 1
1. frissítés a következő hibajavításokat tartalmaz, és új funkcióit tartalmazza:

* 31 napos ingyenes védelmet egy kiszolgáló-példány. Ez lehetővé teszi a funkció tesztelése, vagy egy-a-koncepció igazolása beállításához.
  * Minden műveletet a kiszolgálón, beleértve a feladatátvételi és a feladat-visszavétel szabadon első 31 napra vonatkozó időpontjától egy kiszolgálót a Site Recovery Scout először védett.
  * Az újabb verziók esetében a 32 nap egyes védett kiszolgálókon a szabványos példány arány Azure Site Recovery által védett felhasználói tulajdonú helyhez kell fizetnie.
  * Bármikor jelenleg felszámított védett kiszolgálók számát érhető el az irányítópult-oldalon, az Azure Site Recovery-tárolóban.
* Támogatja a vSphere parancssori felület (vCLI) 5.5 Update 2.
* Megjelent a Linux operációs rendszerek a forráskiszolgálón támogatása:
  * RHEL 6 6 frissítése
  * RHEL 5 11 frissítése
  * A centOS 6 frissítés 6
  * CentOS 5 frissítés 11
* A következő problémák hibajavítások:
  * Tároló regisztrálása meghiúsul, a konfigurációs kiszolgáló vagy a RX kiszolgáló.
  * Fürtkötetek nem megfelelően jelenik meg a fürtözött virtuális gépek vannak látható el újra védelemmel visszatértekor azokat.
  * Feladat-visszavétel sikertelen lesz, amikor a fő célkiszolgáló üzemelteti a helyszíni üzemi virtuális gépekről származó eltérő ESXi-kiszolgálón.
  * Konfigurációs fájl engedélyei módosulnak 8.0.1, amely hatással van a védelem és a műveletek történő frissítésekor.
  * Az újraszinkronizálás küszöbérték nem lépnek érvénybe, vártnak, amely inkonzisztens replikációs viselkedést eredményezi.
  * A helyreállítási Időkorlátra vonatkozó beállításait nem jelennek meg helyesen a konfigurációs kiszolgáló felületén. A kibontott adatok értékeinek helytelenül jeleníti meg a tömörített értékét.
  * Az eltávolítási művelet nem a várt módon a vContinuum varázsló törli, és a replikáció nem törlődik a konfigurációs kiszolgáló felhasználói felületén.
  * A vContinuum varázsló lemez automatikusan ki nem jelölt kattintva **részletek** során MSCS virtuális gép védelmét, a lemez nézetben.
  * Manuális a virtuális gép helyreállítása során a fizikai-virtuális (P2V) forgatókönyv HP szükséges szolgáltatások, például CIMnotify és CqMgHost, nem áthelyezni. Az eredmény további rendszerindítás.
  * Linux virtuális gépek védelmének sikertelen lesz, ha több mint 26 lemezeket a fő célkiszolgálón.

## <a name="next-steps"></a>Következő lépések
Bármely kérdéseit, amely rendelkezik a [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).
