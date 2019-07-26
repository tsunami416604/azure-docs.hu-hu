---
title: Azure Backup Server védi a rendszerállapotot és visszaállítja az operációs rendszer nélküli számítógépekre
description: A Azure Backup Server használatával biztonsági mentést készíthet a rendszerállapotról, és operációs rendszer nélküli helyreállítást (BMR) biztosíthat.
author: rayne-wiselman
manager: carmonm
keywords: ''
ms.service: backup
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: raynew
ms.openlocfilehash: a21169a5d9da7c9f1baf8a7d1e7365348860fca1
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465027"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-with-azure-backup-server"></a>Rendszerállapot biztonsági mentése és visszaállítása operációs rendszer nélküli számítógépre Azure Backup Server

Azure Backup Server biztonsági mentést készít a rendszerállapotról, és operációs rendszer nélküli helyreállítást (BMR) biztosít.

*   **Rendszerállapot biztonsági mentése**: Biztonsági másolatot készít az operációs rendszer fájljairól, így a számítógép indításakor helyreállítható, de a rendszerfájlok és a beállításjegyzék elvesznek. A rendszerállapot biztonsági mentése A következőket tartalmazza:
    * Tartományi tag: Rendszerindító fájlok, COM+-osztályok regisztrációs adatbázisa, beállításjegyzék
    * Tartományvezérlő: Windows Server Active Directory (NTDS), rendszerindító fájlok, COM+-osztályok regisztrációs adatbázisa, beállításjegyzék, rendszerkötet (SYSVOL)
    * Fürtszolgáltatást futtató számítógép: Fürt kiszolgálói metaadatainak
    * A Bizonyítványszolgáltatások szolgáltatást futtató számítógép: Tanúsítványadatok
* **Operációs rendszer nélküli biztonsági mentés**: Biztonsági másolatot készít az operációs rendszer fájljairól és a kritikus köteteken lévő összes információról (kivéve a felhasználói adatmennyiségeket). Definíció szerint a BMR biztonsági mentése a rendszerállapot biztonsági mentését is tartalmazza. Védelmet biztosít, ha a számítógép nem indul el, és mindent helyre kell állítani.

Az alábbi táblázat összefoglalja, hogy a biztonsági mentést és helyreállítást hogyan végezheti el. A rendszerállapot-és BMR védelemmel ellátott alkalmazás-verziókkal kapcsolatos részletes információkért lásd: [Mi a Azure Backup Server biztonsági másolat?](backup-mabs-protection-matrix.md).

|Tartalék|Probléma|Helyreállítás Azure Backup Server biztonsági másolatból|Helyreállítás rendszerállapotról biztonsági másolatból|BMR|
|----------|---------|---------------------------|------------------------------------|-------|
|**Fájlinformációk**<br /><br />Rendszeres adatbiztonsági mentés<br /><br />BMR/rendszerállapot biztonsági mentése|Elveszett fájlok|I|N|N|
|**Fájlinformációk**<br /><br />A fájlinformációk Azure Backup Server biztonsági mentése<br /><br />BMR/rendszerállapot biztonsági mentése|Elveszett vagy sérült operációs rendszer|N|I|I|
|**Fájlinformációk**<br /><br />A fájlinformációk Azure Backup Server biztonsági mentése<br /><br />BMR/rendszerállapot biztonsági mentése|Elveszett kiszolgáló (az adatkötetek érintetlenek)|N|N|I|
|**Fájlinformációk**<br /><br />A fájlinformációk Azure Backup Server biztonsági mentése<br /><br />BMR/rendszerállapot biztonsági mentése|Elveszett kiszolgáló (az adatkötetek elvesztek)|I|Nem|Igen (BMR, majd a biztonsági másolatban tárolt fájlok rendszeres helyreállítása)|
|**SharePoint**-adatértékek:<br /><br />Azure Backup Server a farmon tárolt adatbiztonsági másolatok<br /><br />BMR/rendszerállapot biztonsági mentése|Elveszett hely, lista, listaelem, dokumentum|I|N|N|
|**SharePoint**-adatértékek:<br /><br />Azure Backup Server a farmon tárolt adatbiztonsági másolatok<br /><br />BMR/rendszerállapot biztonsági mentése|Elveszett vagy sérült operációs rendszer|N|I|I|
|**SharePoint**-adatértékek:<br /><br />Azure Backup Server a farmon tárolt adatbiztonsági másolatok<br /><br />BMR/rendszerállapot biztonsági mentése|Vészhelyreállítás|N|N|N|
|Windows Server 2012 R2 Hyper-V<br /><br />A Hyper-V-gazdagép vagy-vendég Azure Backup Server biztonsági mentése<br /><br />A gazdagép BMR/rendszerállapotának biztonsági mentése|Elveszett virtuális gép|I|N|N|
|Hyper-V<br /><br />A Hyper-V-gazdagép vagy-vendég Azure Backup Server biztonsági mentése<br /><br />A gazdagép BMR/rendszerállapotának biztonsági mentése|Elveszett vagy sérült operációs rendszer|N|I|I|
|Hyper-V<br /><br />A Hyper-V-gazdagép vagy-vendég Azure Backup Server biztonsági mentése<br /><br />A gazdagép BMR/rendszerállapotának biztonsági mentése|Elveszett Hyper-V-gazdagép (a virtuális gépek érintetlenek)|N|N|I|
|Hyper-V<br /><br />A Hyper-V-gazdagép vagy-vendég Azure Backup Server biztonsági mentése<br /><br />A gazdagép BMR/rendszerállapotának biztonsági mentése|Elveszett Hyper-V-gazdagép (a virtuális gépek elvesztek)|N|N|I<br /><br />BMR, amelyet a rendszeres Azure Backup Server helyreállítás követ|
|SQL Server/Exchange<br /><br />Azure Backup Server alkalmazás biztonsági mentése<br /><br />BMR/rendszerállapot biztonsági mentése|Elveszett alkalmazásadatok|I|N|N|
|SQL Server/Exchange<br /><br />Azure Backup Server alkalmazás biztonsági mentése<br /><br />BMR/rendszerállapot biztonsági mentése|Elveszett vagy sérült operációs rendszer|N|y|I|
|SQL Server/Exchange<br /><br />Azure Backup Server alkalmazás biztonsági mentése<br /><br />BMR/rendszerállapot biztonsági mentése|Elveszett kiszolgáló (az adatbázis/tranzakciós naplók érintetlenek)|N|N|I|
|SQL Server/Exchange<br /><br />Azure Backup Server alkalmazás biztonsági mentése<br /><br />BMR/rendszerállapot biztonsági mentése|Elveszett kiszolgáló (adatbázis/tranzakciós naplók elvesztek)|N|N|I<br /><br />BMR helyreállítás, amelyet a rendszeres Azure Backup Server helyreállítás követ|

## <a name="how-system-state-backup-works"></a>A rendszerállapot biztonsági mentésének működése

Rendszerállapot biztonsági mentésének futtatásakor a Backup kiszolgáló a Windows Server biztonsági másolatával kommunikál a kiszolgáló rendszerállapotának biztonsági másolatának kéréséhez. Alapértelmezés szerint a Backup Server és a Windows Server biztonsági másolat a legnagyobb rendelkezésre álló szabad területtel rendelkező meghajtót használja. A meghajtóval kapcsolatos információkat a rendszer a PSDataSourceConfig. XML fájlba menti. Ez az a meghajtó, amelyet a Windows Server biztonsági másolat használ a biztonsági mentésekhez.

Testreszabhatja azt a meghajtót, amelyet a Backup Server használ a rendszerállapot biztonsági mentéséhez. A védett kiszolgálón lépjen a C:\Program Files\Microsoft adatvédelem Manager\MABS\Datasources. Nyissa meg szerkesztésre a PSDataSourceConfig. xml fájlt. Módosítsa a \<meghajtóbetűjel\> FilesToProtect értékét. Mentse és zárja be a fájlt. Ha van olyan védelmi csoport, amely a számítógép rendszerállapotának védelmére van beállítva, futtasson egy konzisztencia-ellenőrzést. Ha riasztás jön létre, válassza a **védelmi csoport módosítása** lehetőséget a riasztásban, majd fejezze be a varázslót. Ezután futtasson egy újabb konzisztencia-ellenőrzést.

Vegye figyelembe, hogy ha a védelmi kiszolgáló fürtben található, lehetséges, hogy a legnagyobb szabad területtel rendelkező meghajtóként a rendszer kijelöli a meghajtót. Ha a meghajtó tulajdonosa egy másik csomópontra váltott át, és a rendszerállapot biztonsági mentése fut, a meghajtó nem érhető el, és a biztonsági mentés sikertelen lesz. Ebben az esetben módosítsa a PSDataSourceConfig. xml fájlt úgy, hogy egy helyi meghajtóra mutasson.

Ezután Windows Server biztonsági másolat létrehoz egy WindowsImageBackup nevű mappát a visszaállítási mappa gyökerében. Ahogy Windows Server biztonsági másolat létrehozza a biztonsági másolatot, a rendszer az összes adatterületet ebbe a mappába helyezi. A biztonsági mentés befejezésekor a rendszer átviszi a fájlt a biztonsági mentési kiszolgáló számítógépére. Tekintse meg az alábbi információkat:

* Ez a mappa és annak tartalma nem törlődik a biztonsági mentés vagy az átvitel befejezésekor. Ennek a legjobb módja az, hogy a rendszer a tárterületet a következő biztonsági mentés befejezése után foglalja le.
* A mappát a rendszer minden alkalommal létrehozza, amikor biztonsági mentés készül. Az idő és a dátum bélyegzője a rendszerállapot utolsó biztonsági mentésének időpontját tükrözi.

## <a name="bmr-backup"></a>BMR biztonsági mentés

A BMR (beleértve a rendszerállapot biztonsági mentését is) a biztonsági mentési feladatot a rendszer közvetlenül a biztonságimásolat-kiszolgáló számítógép egyik megosztására menti. A rendszer nem menti a védett kiszolgáló egyik mappájába.

A Backup Server meghívja a Windows Server biztonsági másolat, és megosztja az adott BMR biztonsági másolatának replikájának kötetét. Ebben az esetben nem azt Windows Server biztonsági másolat, hogy a legnagyobb szabad területtel rendelkező meghajtót használja. Ehelyett a feladatokhoz létrehozott megosztást használja.

A biztonsági mentés befejezésekor a rendszer átviszi a fájlt a biztonsági mentési kiszolgáló számítógépére. A naplók tárolása a C:\windows\logs\windowsserverbackup

## <a name="prerequisites-and-limitations"></a>Előfeltételek és korlátozások

-   A BMR nem támogatott a Windows Server 2003 rendszerű számítógépeken, illetve az ügyfél operációs rendszerét futtató számítógépeken.

-   A BMR és a rendszerállapot védelme nem biztosítható a különböző védelmi csoportokban található számítógépekhez.

-   A biztonsági mentési kiszolgáló számítógépe nem tudja megóvni magát a BMR.

-   A szalag rövid távú védelme (lemezről szalagra vagy D2T) nem támogatott a BMR. A szalagos hosszú távú tárolás (lemezről lemezre vagy szalagra vagy D2D2T) támogatott.

-   A BMR-védelem esetében Windows Server biztonsági másolat a védett számítógépre kell telepíteni.

-   A BMR-védelem esetében a rendszerállapot-védelemtől eltérően a Backup Server nem rendelkezik a védett számítógépen a szükséges lemezterülettel. Windows Server biztonsági másolat közvetlenül továbbítja a biztonsági mentéseket a biztonságimásolat-kiszolgáló számítógépére. A biztonságimásolat-áthelyezési feladat nem jelenik meg a biztonsági mentési kiszolgáló **feladatok** nézetében.

-   A Backup Server 30 GB helyet foglal le a BMR lévő replika köteten. Ezt a **lemez** kiosztása lapon módosíthatja a védelmi csoport módosítása varázsló vagy a Get-DatasourceDiskAllocation és a set-DatasourceDiskAllocation PowerShell-parancsmagok használatával. A helyreállítási pont kötetén a BMR-védelemnek körülbelül 6 GB-nak kell lennie öt nap megőrzéséhez.
    * Vegye figyelembe, hogy a replika kötetének mérete nem csökkenthető 15 GB-nál kevesebbre.
    * A Backup Server nem számítja ki a BMR-adatforrás méretét. Minden kiszolgáló esetében 30 GB-ot feltételez. Módosítsa az értéket a környezetében várható BMR biztonsági mentések méretétől függően. A BMR biztonsági mentésének mérete nagyjából a kritikus kötetek felhasznált területének összege alapján számítható ki. Kritikus kötetek = rendszerindító kötet + rendszerkötet + kötet, amely a rendszerállapot-adatmennyiséget (például Active Directory) üzemelteti.

-   Ha a rendszerállapot-védelemről BMR-védelemre vált, a BMR-védelem kevesebb helyet igényel a *helyreállítási pont kötetén*. A köteten található felesleges terület azonban nem lett visszaállítva. A kötet méretét manuálisan csökkentheti a védelmi csoport módosítása varázsló **lemezterület módosítása** lapján vagy a Get-DatasourceDiskAllocation és a set-DatasourceDiskAllocation PowerShell-parancsmagok használatával.

    Ha a rendszerállapot-védelemről BMR-védelemre vált, a BMR-védelem több helyet igényel a *replika kötetén*. A kötet automatikusan ki van bővítve. Ha módosítani szeretné az alapértelmezett terület foglalásait, használja a Modify-Diskallocation parancsmaggal módosíthatja PowerShell-parancsmagot.

-   Ha a BMR-védelemről rendszerállapot-védelemre vált, több helyre van szüksége a helyreállítási pont kötetén. Előfordulhat, hogy a biztonsági mentési kiszolgáló megpróbálja automatikusan megnövelni a kötetet. Ha nincs elegendő hely a tárolóban, hiba történik.

    Ha a BMR-védelemről rendszerállapot-védelemre vált, a védett számítógépen helyre kell állítania a helyet. Ennek az az oka, hogy a rendszerállapot-védelem először a helyi számítógépre írja a replikát, majd átviszi a biztonsági mentési kiszolgáló számítógépére.

## <a name="before-you-begin"></a>Előkészületek

1.  **Azure Backup Server üzembe helyezése**. Ellenőrizze, hogy a biztonsági mentési kiszolgáló megfelelően van-e telepítve. További információkért lásd:
    * [A Azure Backup Server rendszerkövetelményei](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites)
    * [A Backup Server védelmi mátrixa](backup-mabs-protection-matrix.md)

2.  **Állítsa be**a tárolót. A biztonsági mentési adatok lemezen, szalagon és a felhőben is tárolhatók az Azure-ban. További információ: az [adattároló előkészítése](https://docs.microsoft.com/system-center/dpm/plan-long-and-short-term-data-storage).

3.  **Állítsa be a védelmi ügynököt**. Telepítse a védelmi ügynököt arra a számítógépre, amelyre biztonsági másolatot szeretne készíteni. További információ: [a DPM védelmi ügynök telepítése](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Rendszerállapot biztonsági mentése és operációs rendszer nélküli számítógép
Hozzon létre egy védelmi csoportot a [védelmi csoportok telepítése](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups)című témakörben leírtak szerint. Vegye figyelembe, hogy a BMR és a rendszerállapot nem védhető meg ugyanazon számítógép esetében különböző csoportokban. Emellett a BMR kijelölésekor a rendszer automatikusan engedélyezi a rendszerállapotot.


1.  Az új védelmi csoport létrehozása varázsló megnyitásához a biztonsági mentési kiszolgáló felügyeleti konzol válassza a **védelmi** > **műveletek** > **védelmi csoport létrehozása**lehetőséget.

2.  A **védelmi csoport típusának kiválasztása** lapon válassza a **kiszolgálók**lehetőséget, majd kattintson a **tovább**gombra.

3.  A **csoporttagok kiválasztása** lapon bontsa ki a számítógépet, majd válassza a **BMR** vagy a rendszerállapot lehetőséget.

    Ne feledje, hogy a BMR és a rendszerállapotot nem lehet a különböző csoportokban lévő ugyanazon a számítógépen is védelemmel ellátni. Emellett a BMR kijelölésekor a rendszer automatikusan engedélyezi a rendszerállapotot. További információ: [védelmi csoportok telepítése](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups).

4.  Az **adatvédelmi módszer kiválasztása** lapon válassza ki, hogyan szeretné kezelni a rövid távú és a hosszú távú biztonsági mentést. A rövid távú biztonsági mentés mindig lemezre történik, a lemezről az Azure-felhőbe történő biztonsági mentés lehetőségével Azure Backup (rövid távú vagy hosszú távú) használatával. A hosszú távú biztonsági mentés alternatívájaként a hosszú távú biztonsági mentést egy különálló szalagos eszközre vagy a Backup Serverhez csatlakoztatott szalagos tárba kell beállítani.

5.  A **rövid távú célok kiválasztása** lapon válassza ki, hogyan szeretné biztonsági mentést készíteni a lemez rövid távú tárolására:
    1. A **megőrzési**időtartam beállításnál válassza ki, hogy mennyi ideig szeretné megőrizni az adatok lemezen történő tárolását. 
    2. A **szinkronizálás gyakorisága**beállításnál válassza ki, hogy milyen gyakran szeretne növekményes biztonsági mentést készíteni lemezre. Ha nem szeretné beállítani a biztonsági mentés időközét, a közvetlenül a **helyreállítási pont előtt** lehetőségre kattintva is megtekintheti. A biztonsági mentési kiszolgáló expressz, teljes biztonsági mentést futtat az egyes helyreállítási pontok ütemezése előtt.

6.  Ha a hosszú távú tároláshoz szalagos adattárolást szeretne tárolni, a **hosszú távú célok megadása** oldalon válassza ki, hogy mennyi ideig szeretné megőrizni a szalagos adatfeldolgozást (1-99 év). 
    1. A **biztonsági mentés gyakorisága**beállításnál válassza ki, hogy milyen gyakran fusson a szalagos biztonsági mentés. A gyakoriság a kiválasztott megőrzési tartományon alapul:
        * Ha a megőrzési időtartam 1-99 év, a biztonsági mentések naponta, hetente, kéthetente, havonta, negyedévente, félévente vagy évente hajthatók végre.
        * Ha a megőrzési időtartam 1-11 hónap, akkor a biztonsági mentések naponta, hetente, kéthetente vagy havonta hajthatók végre.
        * Ha a megőrzési időtartam 1-4 hét, a biztonsági mentések naponta vagy hetente hajthatók végre.

    2. A **szalag és könyvtár részleteinek kiválasztása** lapon válassza ki a használni kívánt szalagot és tárat, valamint azt, hogy az adatok tömörítve és titkosítva vannak-e.

7.  A **lemez kiosztásának áttekintése** lapon tekintse át a védelmi csoport számára lefoglalt tárterület lemezterületét.

    1. Az adatmennyiség **teljes mérete** a biztonsági mentésre használni kívánt adatmennyiség.
    2. **Azure Backup Server** kiépíthető lemezterület az a terület, amelyet a biztonsági mentési kiszolgáló a védelmi csoport számára javasol. A Backup Server a beállítások alapján kiválasztja az ideális biztonsági mentési kötetet. A **lemezes foglalás részleteiben**azonban szerkesztheti a kötetek biztonsági mentésének lehetőségeit. 
    3. A munkaterhelések esetében a legördülő menüben válassza ki az előnyben részesített tárolót. A módosítások a **rendelkezésre álló Disk Storage** ablaktáblán a **teljes tárterület** és a **szabad tárterület** értékeit módosítják. A kiépített terület azt a tárterületet jelenti, amelyet a Backup Server javasol a kötethez való hozzáadáshoz, így biztosítva a zökkenőmentes biztonsági mentést.

8.  A **replika-létrehozási módszer kiválasztása** lapon válassza ki, hogyan szeretné kezelni a kezdeti teljes adatreplikációt. Ha úgy dönt, hogy replikálja a hálózaton keresztül, javasoljuk, hogy válasszon ki egy off-Peak időpontot. Nagy mennyiségű vagy az optimálisnál kisebb hálózati feltételek esetén érdemes lehet a cserélhető adathordozón keresztül replikálni az adatkapcsolatot.

9. A **konzisztencia-ellenőrzési beállítások kiválasztása** lapon válassza ki, hogyan szeretné automatizálni a konzisztencia-ellenőrzéseket. Dönthet úgy, hogy csak akkor futtatja az ellenőrzését, ha a replika adatai inkonzisztensek, vagy egy adott időpontban. Ha nem szeretne automatikus konzisztencia-ellenőrzést beállítani, akkor bármikor lefuttathatja a manuális ellenőrzést. Manuális ellenőrzés futtatásához a biztonsági mentési felügyeleti konzol kiszolgáló **védelme** területén kattintson a jobb gombbal a védelmi csoportra, majd válassza a **konzisztencia-ellenőrzés végrehajtása**lehetőséget.

10. Ha a Azure Backup használatával végez biztonsági mentést a felhőre, az **online védelem adatok megadása** lapon válassza ki azokat a munkaterheléseket, amelyekről biztonsági másolatot szeretne készíteni az Azure-ba.

11. Az **online biztonsági mentési ütemezés megadása** lapon válassza ki, hogy milyen gyakran történjenek növekményes biztonsági másolatok az Azure-ba. A biztonsági mentéseket ütemezheti minden nap, hét, hónap és év futtatására, és kiválaszthatja azt az időpontot és dátumot, amelyen a futtatást el szeretné indítani. A biztonsági mentések naponta akár kétszer is előfordulhatnak. Minden alkalommal, amikor egy biztonsági mentés fut, egy adathelyreállítási pont jön létre az Azure-ban a biztonsági mentési kiszolgáló lemezén tárolt biztonsági mentési adatok másolatával.

12. Az **online adatmegőrzési szabály megadása** lapon válassza ki, hogy a napi, heti, havi és éves biztonsági másolatok alapján létrehozott helyreállítási pontok megmaradjanak az Azure-ban.

13. Az **online replikáció kiválasztása** lapon válassza ki, hogyan történjen az adatkezdeti teljes replikálás. Replikálhatja a hálózaton keresztül, vagy elvégezheti az offline biztonsági mentést (kapcsolat nélküli előkészítés). Az offline biztonsági mentés az Azure importálási szolgáltatását használja. További információ: [Offline biztonsági mentési munkafolyamat Azure Backup-ben](backup-azure-backup-import-export.md).

14. Az **Összefoglalás** lapon tekintse át a beállításokat. Miután kiválasztotta a **csoport létrehozása**lehetőséget, az adatműveletek kezdeti replikálása történik. Az adatreplikálás befejeződése után az **állapot** lapon a védelmi csoport állapota **OK**. A biztonsági mentés a védelmi csoport beállításai szerint történik.

## <a name="recover-system-state-or-bmr"></a>Rendszerállapot vagy BMR helyreállítása
A BMR vagy a rendszerállapotot egy hálózati helyre állíthatja helyre. Ha biztonsági mentést készített a BMR, a Windows helyreállítási környezet (WinRE) segítségével indítsa el a rendszert, és kapcsolódjon a hálózathoz. Ezután a Windows Server biztonsági másolat használatával állítsa helyre a hálózati helyről. Ha biztonsági mentést készít a rendszerállapotról, használja a Windows Server biztonsági másolat a hálózati helyről való helyreállításhoz.

### <a name="restore-bmr"></a>BMR visszaállítása
Futtassa a helyreállítást a biztonsági mentési kiszolgáló számítógépén:

1.  A **helyreállítás** ablaktáblán keresse meg a helyreállítani kívánt számítógépet, majd válassza az operációs rendszer nélküli **helyreállítás**lehetőséget.

2.  A rendelkezésre álló helyreállítási pontok félkövérrel vannak megadva a naptárban. Válassza ki a használni kívánt helyreállítási pont dátumát és időpontját.

3.  A **helyreállítási típus kiválasztása** lapon válassza a **Másolás hálózati mappába lehetőséget.**

4.  A **célhely megadása** lapon válassza ki, hogy hová szeretné másolni az adatfájlokat. Ne feledje, hogy a kiválasztott célhelynek elegendő helyet kell tartalmaznia. Javasoljuk, hogy hozzon létre egy új mappát.

5.  A **helyreállítási beállítások megadása** lapon válassza ki az alkalmazni kívánt biztonsági beállításokat. Ezután válassza ki, hogy szeretné-e használni a Tárolóhálózati (SAN) alapú hardveres pillanatképeket a gyorsabb helyreállítás érdekében. (Ez a lehetőség csak akkor érhető el, ha SAN-val rendelkezik ezzel a funkcióval, és lehetővé teszi a klónok létrehozását és felosztását, hogy írható legyen. Emellett a védett számítógépnek és a biztonságimásolat-kiszolgáló számítógépnek ugyanahhoz a hálózathoz kell csatlakoznia.)

6.  Értesítési beállítások beállítása. A **jóváhagyás** lapon válassza a **helyreállítás**lehetőséget.

A megosztás helyének beállítása:

1.  A visszaállítási helyen lépjen a biztonsági mentést tartalmazó mappára.

2.  Ossza meg a WindowsImageBackup felett egy szinttel rendelkező mappát, hogy a megosztott mappa gyökerében a WindowsImageBackup mappa legyen. Ha ezt nem teszi meg, a visszaállítás nem találja a biztonsági mentést. A Windows helyreállítási környezet (WinRE) használatával történő kapcsolódáshoz olyan megosztásra van szükség, amelyet a megfelelő IP-címmel és hitelesítő adatokkal a WinRE-ben érhet el.

A System visszaállítása:

1.  Indítsa el azt a számítógépet, amelyen vissza szeretné állítani a rendszerképet a visszaállítani kívánt rendszerhez tartozó Windows DVD használatával.

2.  Az első lapon ellenőrizze a nyelvi és területi beállítások beállítást. A **telepítés** lapon válassza a **számítógép javítása**lehetőséget.

3.  A **rendszer-helyreállítási beállítások** lapon válassza a **számítógép visszaállítása a korábban létrehozott rendszerkép használatával**lehetőséget.

4.  A rendszerkép **biztonsági mentésének kiválasztása** **lapon válassza** > a rendszerkép**speciális** > **keresése a hálózaton**rendszerképekhez lehetőséget. Ha megjelenik egy figyelmeztetés, válassza az **Igen**lehetőséget. Lépjen a megosztás elérési útjára, adja meg a hitelesítő adatokat, majd válassza ki a helyreállítási pontot. Ez megkeresi az adott helyreállítási pontban elérhető biztonsági másolatokat. Válassza ki a használni kívánt helyreállítási pontot.

5.  A **biztonsági másolat visszaállítási módjának kiválasztása** lapon válassza a **lemezek formázása és**újraparticionálása elemet. A következő lapon ellenőrizze a beállításokat. 

6.  A visszaállítás megkezdéséhez válassza a **Befejezés**lehetőséget. Újraindítás szükséges.

### <a name="restore-system-state"></a>Rendszerállapot visszaállítása

Helyreállítás futtatása a biztonsági mentési kiszolgálón:

1.  A **helyreállítás** ablaktáblán keresse meg a helyreállítani kívánt számítógépet, majd válassza az operációs rendszer nélküli **helyreállítás**lehetőséget.

2.  A rendelkezésre álló helyreállítási pontok félkövérrel vannak megadva a naptárban. Válassza ki a használni kívánt helyreállítási pont dátumát és időpontját.

3.  A **helyreállítási típus kiválasztása** lapon válassza a **Másolás hálózati mappába**lehetőséget.

4.  A **célhely megadása** lapon válassza ki, hová szeretné másolni az adatfájlokat. Ne feledje, hogy a kiválasztott célhelynek elegendő helyet kell biztosítania. Javasoljuk, hogy hozzon létre egy új mappát.

5.  A **helyreállítási beállítások megadása** lapon válassza ki az alkalmazni kívánt biztonsági beállításokat. Ezután adja meg, hogy SAN-alapú hardveres pillanatképeket kíván-e használni a gyorsabb helyreállítás érdekében. (Ez csak akkor lehetséges, ha SAN-val rendelkezik ezzel a funkcióval, és lehetővé teszi a klónok létrehozását és felosztását, hogy az írható legyen. Emellett a védett számítógép és a biztonságimásolat-kiszolgáló kiszolgálójának ugyanahhoz a hálózathoz kell csatlakoznia.)

6.  Értesítési beállítások beállítása. A **jóváhagyás** lapon válassza a **helyreállítás**lehetőséget.

Windows Server biztonsági másolat futtatása:

1.  Válassza  > ki > a**következőt** **a kiszolgáló** > **helyreállítása**műveletekkel.

2.  Válasszon **másik kiszolgálót**, válassza a **hely típusának megadása** lapot, majd kattintson a **távoli megosztott mappa**lehetőségre. Adja meg a helyreállítási pontot tartalmazó mappa elérési útját.

3.  A **helyreállítási típus kiválasztása** lapon válassza a rendszerállapot lehetőséget. 

4. A rendszerállapot- **helyreállítás helyének kiválasztása** lapon válassza az **eredeti hely**lehetőséget.

5.  A **jóváhagyás** lapon válassza a **helyreállítás**lehetőséget. A visszaállítás után indítsa újra a kiszolgálót.

6.  A rendszerállapot-visszaállítást a parancssorból is futtathatja. Ehhez indítsa el Windows Server biztonsági másolat a helyreállítani kívánt számítógépen. A termékazonosító verziójának lekéréséhez írja be a következőt a parancssorba:```wbadmin get versions -backuptarget \<servername\sharename\>```

    A verzió azonosítójának használatával indítsa el a rendszerállapot-visszaállítást. A parancssorba írja be a következőt:```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

    Erősítse meg, hogy el szeretné indítani a helyreállítást. A folyamatot a parancssori ablakban tekintheti meg. Létrejön egy visszaállítási napló. A visszaállítás után indítsa újra a kiszolgálót.

