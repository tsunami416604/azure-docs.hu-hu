---
title: Az Azure Backup Server rendszerállapot védi, és visszaállítja az operációs rendszer nélküli
description: Azure Backup Server használatával a rendszerállapot biztonsági mentését, és az operációs rendszer nélküli helyreállítás (BMR) védelmet nyújt.
services: backup
author: markgalioto
manager: carmonm
keywords: ''
ms.service: backup
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: markgal
ms.openlocfilehash: d35f8667cb1ca9a0b3abd08450ebc647d6d12276
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34607208"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-with-azure-backup-server"></a>Rendszerállapot biztonsági mentését, és állítsa vissza az Azure Backup Server operációs rendszer nélküli

Az Azure Backup Server készít biztonsági másolatot a rendszer állapotát, és operációs rendszer nélküli helyreállítás (BMR) védelmet nyújt.

*   **Rendszerállapot biztonsági mentését**: operációs rendszer fájljait, készít biztonsági másolatot, akkor helyreállíthatja a számítógép indításakor, de a rendszerfájlok és a beállításjegyzék elvesznek. A rendszerállapot biztonsági mentése tartalmazza:
    * Tartományi tag: rendszerindítási fájlokat, a COM + osztályregisztrációs adatbázis, a beállításjegyzék
    * Tartományvezérlő: Windows Server Active Directory (NTDS), a rendszerindító fájlok, a COM + osztályregisztrációs adatbázis, a beállításjegyzék, a system volume (SYSVOL)
    * Fürtözött szolgáltatásokat futtató számítógép: Fürtkiszolgáló metaadatai
    * Tanúsítvány-szolgáltatásokat futtató számítógép: tanúsítvány-adatok
* **Operációs rendszer nélküli biztonsági mentés**: biztonsági mentést készít operációs rendszer fájljait és minden adat a kritikus köteteken (kivéve a felhasználói adatok). BMR biztonsági mentés definícióját, foglalja magában a rendszerállapot biztonsági mentését. Ha a számítógép nem indul el, és Ön mindent helyre kell védelmet nyújt.

A következő táblázat összefoglalja, mit akkor is biztonsági mentését és helyreállítását. A BMR és rendszerállapot védhető app verzióival kapcsolatos részletes információkért lásd: [biztonsági mentése Azure Backup Server funkciója?](backup-mabs-protection-matrix.md).

|Backup|Probléma|Az Azure Backup Server biztonsági másolat helyreállítása|Rendszerállapot helyreállítása|OPERÁCIÓS RENDSZER NÉLKÜLI HELYREÁLLÍTÁS|
|----------|---------|---------------------------|------------------------------------|-------|
|**Fájladatok**<br /><br />Rendszeres biztonsági mentését<br /><br />BMR vagy rendszerállapot biztonsági mentése|Elveszett adatok|I|N|N|
|**Fájladatok**<br /><br />Az Azure Backup Server az adatok biztonsági mentése fájlba<br /><br />BMR vagy rendszerállapot biztonsági mentése|Elveszett vagy sérült operációs rendszer|N|I|I|
|**Fájladatok**<br /><br />Az Azure Backup Server az adatok biztonsági mentése fájlba<br /><br />BMR vagy rendszerállapot biztonsági mentése|Elveszett kiszolgáló (az adatkötetek nem sérültek)|N|N|I|
|**Fájladatok**<br /><br />Az Azure Backup Server az adatok biztonsági mentése fájlba<br /><br />BMR vagy rendszerállapot biztonsági mentése|Elveszett kiszolgáló (az adatkötetek elvesztek)|I|Nem|Igen (operációs rendszer nélküli Helyreállítás, biztonsági másolat adatainak normál helyreállítása követi)|
|**A SharePoint-adatok**:<br /><br />Az Azure Backup Server az adatok biztonsági mentése farm<br /><br />BMR vagy rendszerállapot biztonsági mentése|Elveszett hely, listák, listaelemek és dokumentumok|I|N|N|
|**A SharePoint-adatok**:<br /><br />Az Azure Backup Server az adatok biztonsági mentése farm<br /><br />BMR vagy rendszerállapot biztonsági mentése|Elveszett vagy sérült operációs rendszer|N|I|I|
|**A SharePoint-adatok**:<br /><br />Az Azure Backup Server az adatok biztonsági mentése farm<br /><br />BMR vagy rendszerállapot biztonsági mentése|Vészhelyreállítás|N|N|N|
|Windows Server 2012 R2 Hyper-V<br /><br />Azure Backup Server biztonsági mentés Hyper-V gazdagép vagy Vendég<br /><br />BMR vagy rendszerállapot biztonsági mentését gazdagép|Elveszett VM|I|N|N|
|Hyper-V<br /><br />Azure Backup Server biztonsági mentés Hyper-V gazdagép vagy Vendég<br /><br />BMR vagy rendszerállapot biztonsági mentését gazdagép|Elveszett vagy sérült operációs rendszer|N|I|I|
|Hyper-V<br /><br />Azure Backup Server biztonsági mentés Hyper-V gazdagép vagy Vendég<br /><br />BMR vagy rendszerállapot biztonsági mentését gazdagép|Elveszett Hyper-V állomás (a VM nem sérültek)|N|N|I|
|Hyper-V<br /><br />Azure Backup Server biztonsági mentés Hyper-V gazdagép vagy Vendég<br /><br />BMR vagy rendszerállapot biztonsági mentését gazdagép|Elveszett Hyper-V állomás (virtuális gépek elveszett)|N|N|I<br /><br />Operációs rendszer nélküli Helyreállítás, Azure Backup Server normál helyreállítása követi|
|SQL Server és az Exchange<br /><br />Az Azure Backup Server alkalmazás biztonsági mentése<br /><br />BMR vagy rendszerállapot biztonsági mentése|Az alkalmazásadatok elveszett|I|N|N|
|SQL Server és az Exchange<br /><br />Az Azure Backup Server alkalmazás biztonsági mentése<br /><br />BMR vagy rendszerállapot biztonsági mentése|Elveszett vagy sérült operációs rendszer|N|Y|I|
|SQL Server és az Exchange<br /><br />Az Azure Backup Server alkalmazás biztonsági mentése<br /><br />BMR vagy rendszerállapot biztonsági mentése|Elveszett kiszolgáló (adatbázis/tranzakciós naplók nem sérültek)|N|N|I|
|SQL Server és az Exchange<br /><br />Az Azure Backup Server alkalmazás biztonsági mentése<br /><br />BMR vagy rendszerállapot biztonsági mentése|Elveszett kiszolgáló (adatbázis-tranzakciókhoz naplók elveszett)|N|N|I<br /><br />Operációs rendszer nélküli helyreállítás Azure Backup Server normál helyreállítása követi|

## <a name="how-system-state-backup-works"></a>Hogyan működik a rendszerállapot biztonsági mentése

Amikor a rendszerállapot biztonsági mentése fut, tartalék kiszolgáló kommunikál a Windows Server biztonsági másolat kérje a kiszolgáló rendszerállapotának biztonsági másolata. Alapértelmezés szerint biztonsági mentés kiszolgáló és a Windows Server biztonsági másolat használja a legtöbb szabad lemezterülettel rendelkező meghajtóra. A meghajtó kapcsolatos információkat a PSDataSourceConfig.xml fájl kerül. Ez az a meghajtó által a biztonsági mentéshez használt Windows Server biztonsági másolat.

Testre szabhatja a helykiszolgáló biztonsági mentése által a rendszerállapot biztonsági mentését a meghajtót. A védett kiszolgálón navigáljon a C:\Program Files\Microsoft Data Protection Manager\MABS\Datasources. Nyissa meg szerkesztésre a PSDataSourceConfig.xml fájlt. Módosítsa a \<FilesToProtect\> a meghajtó betűjeléhez tartozó értéket. Mentse és zárja be a fájlt. Ha a számítógép rendszerállapotát védelméhez védelmicsoport-készlet, akkor futtasson konzisztencia-ellenőrzést. Ha riasztás jön létre, jelölje be **a védelmi csoport módosítása** kattintson a riasztásra, majd fejezze be a varázslót. Ezután futtassa a konzisztencia-ellenőrzést.

Ne feledje, hogy a védelmi kiszolgáló egy fürt része, ha lehetséges, hogy a fürt meghajtó lesz kiválasztva a legtöbb szabad területtel rendelkező meghajtóként. Ha a meghajtó tulajdonosa egy másik csomópontra, és a rendszer állapota biztonsági mentés futtatása lett átadva, a meghajtó nem érhető el, és a biztonsági mentés sikertelen. Ebben az esetben módosítsa a psdatasourceconfig.xml fájlt egy helyi meghajtóra.

Ezt követően a Windows Server biztonsági másolat egy gyökérkönyvtárában található a visszaállítási mappát a WindowsImageBackup nevű mappát hoz létre. Mivel a Windows Server biztonsági másolat hozza létre a biztonsági mentés, a rendszer az adatok kerülnek, ebben a mappában. Ha a biztonsági mentés befejeződött, a fájl átkerül a biztonsági mentési számítógépén. Tekintse meg az alábbi információkat:

* Ez a mappa és annak tartalma nem törlődnek a biztonsági mentési vagy átviteli befejezésekor. Ez a legjobb módszer az, hogy a hely a rendszer fenntartja a biztonsági mentés befejeződött a következő alkalommal.
* A mappa minden alkalommal létrejön egy biztonsági másolat legyen. A dátumával és időpontjával stamp tükrözze az utolsó rendszerállapot biztonsági mentését idején.

## <a name="bmr-backup"></a>BMR biztonsági mentéssel

Az operációs rendszer nélküli Helyreállítás (beleértve a rendszerállapot biztonsági mentése) a biztonsági mentési feladat mentett közvetlenül egy megosztást a biztonsági mentési kiszolgálón. Mentése nem történik egy mappába a védett kiszolgálón.

Tartalék kiszolgáló meghívja a Windows Server biztonsági másolat, és osztja meg a replikakötetet az adott BMR biztonsági mentés. Ebben az esetben azt nem adja meg a Windows Server biztonsági másolat a legtöbb szabad területtel rendelkező meghajtó használatára. Ehelyett a létrehozott megosztás használ a feladatot.

Ha a biztonsági mentés befejeződött, a fájl átkerül a biztonsági mentési számítógépén. Naplók C:\Windows\Logs\WindowsServerBackup vannak tárolva.

## <a name="prerequisites-and-limitations"></a>Előfeltételek és korlátozások

-   A BMR nem támogatott a Windows Server 2003 rendszerű számítógépeken, vagy egy ügyfél operációs rendszert futtató számítógépekre.

-   Védelme nem lehetséges a BMR és a rendszer a különböző védelmi csoportokban ugyanazon a számítógépen.

-   A biztonsági mentés számítógépet nem tudja védeni magát az operációs rendszer nélküli Helyreállítás.

-   Rövid távú védelem szalagra (lemezről szalagra vagy D2T) a BMR esetében nem támogatott. Hosszú távú tárolás szalagon (lemez lemez-az-szalagra vagy D2D2T) támogatott.

-   A BMR-védelemre vált a Windows Server biztonsági másolat a védett számítógépen telepítenie kell.

-   A BMR-védelemre vált, eltérően rendszerállapot-védelemről, a biztonsági mentés kiszolgáló nem rendelkezik semmilyen lemezterületet a védett számítógépen. Windows Server biztonsági másolat közvetlenül küld biztonsági mentések a biztonsági mentés számítógépén. A biztonsági mentési átviteli feladat nem jelenik meg a helykiszolgáló biztonsági mentése **feladatok** nézet.

-   Tartalék kiszolgáló 30 GB helyet a replika kötetén a BMR foglalja le. Ezt a módosíthatja a **lemezfoglalás** lapon a védelmi csoport módosítása varázslót vagy a Get-DatasourceDiskAllocation és a Set-DatasourceDiskAllocation PowerShell-parancsmagok használatával. A helyreállítási pont kötetén a BMR-védelem az egy 5 napos megőrzési szükséges körülbelül 6 GB.
    * Vegye figyelembe, hogy nem csökkenti a replikakötet mérete kisebb, mint 15 GB.
    * Helykiszolgáló biztonsági mentése nem számítja ki a BMR-adatforrás méretét. Az összes feltételezi 30 GB. Módosítsa az értéket, a BMR biztonsági mentések adott környezetben várható mérete alapján. A BMR biztonsági mentés mérete körülbelül kiszámítható felhasznált területek összegeként a kritikus köteteken. Kritikus kötetek = rendszerindító kötet + rendszerkötet + a rendszerállapot-adatok, például az Active Directory tartalmazó kötet.

-   Ha módosítja a rendszerállapot-védelemről BMR-védelemre, ha BMR-védelem kevesebb területre van szükség a a *helyreállításipont-kötet*. Azonban további lemezterületet a köteten nem felszabadul. A manuális zsugorítását a kötet mérete a **lemezfoglalás módosítása** oldalán a védelmi csoport módosítása varázslót vagy a Get-DatasourceDiskAllocation és a Set-DatasourceDiskAllocation PowerShell-parancsmagok használatával.

    Ha módosítja a rendszerállapot-védelemről BMR-védelemre, a BMR-védelem több helyet igényel a a *replikakötet*. A kötet automatikusan ki van bővítve. Ha szeretné módosítani az alapértelmezett helylefoglalási, használja a Modify-DiskAllocation PowerShell-parancsmagot.

-   Rendszerállapot-védelemre vált BMR-védelemre vált, ha szüksége több helyet a helyreállítási pont kötetén. Helykiszolgáló biztonsági mentése megpróbálja a kötet automatikus növelésére. Nincs elegendő hely a tárolókészletben, ha hiba történik.

    Rendszerállapot-védelemre vált BMR-védelemre vált, ha a védett számítógépen területre van szükség. Ennek az az oka a rendszerállapot-védelemről először írja a replikát a helyi számítógépen, és átadja a biztonsági mentés számítógépén.

## <a name="before-you-begin"></a>Előkészületek

1.  **Az Azure Backup Server telepítése**. Győződjön meg arról, hogy a biztonsági mentés kiszolgáló megfelelően van-e telepítve. További információkért lásd:
    * [Azure Backup Server rendszerkövetelményei](http://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites)
    * [Tartalék kiszolgáló védelmi mátrix](backup-mabs-protection-matrix.md)

2.  **Tárolás beállítása**. A lemezen, szalagon, és az Azure felhőben tárolhatja a biztonsági mentési adatokat. További információkért lásd: [adatok a tárterület előkészítése](https://docs.microsoft.com/system-center/dpm/plan-long-and-short-term-data-storage).

3.  **A védelmi ügynök beállítása**. Telepítse a védelmi ügynököt a számítógépre, amelyet szeretne biztonsági másolatot készíteni. További információkért lásd: [a DPM védelmi ügynök telepítése](http://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Készítsen biztonsági másolatot a rendszerállapot és az operációs rendszer nélküli
A védelmi csoport beállítása [telepíteni a védelmi csoportok](http://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups). Vegye figyelembe, hogy Ön védelme esetében nem lehetséges a BMR és a rendszer ugyanazon a számítógépen, a különböző csoporthoz. Emellett operációs rendszer nélküli Helyreállítás kiválasztásakor rendszerállapot automatikusan engedélyezve van.


1.  A biztonsági mentés Server felügyeleti konzol az új védelmi csoport létrehozása varázsló megnyitásához válassza **védelmi** > **műveletek** > **védelmi csoport létrehozása** .

2.  Az a **védelmi csoport típusának kiválasztása** lapon, hogy melyik **kiszolgálók**, majd válassza ki **következő**.

3.  Az a **csoporttagok kiválasztása** lapon bontsa ki a számítógépet, és válassza ki vagy **BMR** vagy **rendszerállapot**.

    Ne feledje, hogy nem láthatók el védelemmel ugyanazon a számítógépen, a különböző csoporthoz BMR és a rendszer állapotát. Emellett operációs rendszer nélküli Helyreállítás kiválasztásakor rendszerállapot automatikusan engedélyezve van. További információkért lásd: [telepíteni a védelmi csoportok](http://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups).

4.  Az a **adatvédelmi módszer kiválasztása** lapon, válassza ki, hogyan szeretné kezelni a rövid távú és hosszú távú biztonsági mentés. Rövid távú biztonsági mentés mindig először lemezre, emellett a biztonsági másolatot a lemezről az Azure felhőalapú Azure biztonsági mentési (rövid távú vagy hosszú távú) használatával. A felhőalapú hosszú távú biztonsági mentés helyett is, hogy a hosszú távú kiszolgálók olyan önálló eszköz vagy a szalaggal szalagtár csatlakozik a helykiszolgáló biztonsági mentése.

5.  Az a **rövid távú célok kiválasztása** lapon, válassza ki, hogy biztonsági másolat készítése rövid távú tárolás lemezen:
    1. A **megőrzési időtartam**, válassza ki, mennyi ideig szeretné az adatok megőrzése lemezen. 
    2. A **szinkronizálási gyakoriság**, válassza ki, milyen gyakran szeretné futtatni a növekményes biztonsági mentés lemezre. Ha nem szeretné, biztonsági mentés gyakoriságát, ellenőrizheti a **csak helyreállítási pont létrehozása előtt** lehetőséget. Helykiszolgáló biztonsági mentése elindul egy kifejezett, teljes biztonsági mentést, egyes helyreállítási pontok ütemezése előtt.

6.  Ha a hosszú távú tárolás szalagon tárolt adatokat tárolni szeretné a **rövid távú célok megadása** lapon, válassza ki, hogy mennyi ideig szeretné megőrizni a szalagon tárolt adatokat (1 – 99 év). 
    1. A **a biztonsági mentés gyakorisága**, milyen gyakran szalagra történő biztonsági mentés válasszon kell futtatnia. A gyakoriság a megőrzési tartomány választott alapul:
        * Ha a megőrzési tartomány 1 – 99 év, biztonsági mentések a fordulhat elő, naponta, hetente, kéthetente, havonta, negyedévente, félévente vagy évente hajthatók végre.
        * Ha a megőrzési tartomány 1 – 11 hónap, kiválaszthatja a biztonsági mentések naponta, hetente, kéthetente vagy havonta.
        * Ha a megőrzési tartomány 1 – 4 hét, biztonsági mentések naponta vagy hetente hajthatók végre.

    2. Az a **szalag és szalagtár részleteinek kiválasztása** lapon válassza ki a szalagot, és a használni kívánt szalagtárat, és hogy adatokat kell tömöríteni és titkosított.

7.  Az a **tekintse át a lemezfoglalás** lapján tekintse át a tárolókészlet szabad kapacitását a védelmi csoport számára lefoglalt.

    1. **Teljes méret adatok** készítsen biztonsági másolatot szeretne az adatok mérete.
    2. **Azure Backup Server létesítendő lemezterület** a kiszolgáló biztonsági mentése azt javasolja, hogy a védelmi csoporthoz tartozó területre. Helykiszolgáló biztonsági mentése úgy dönt, hogy a megadott beállítások alapján épp ezért tökéletes választás a biztonsági mentési köteten. Azonban módosíthatja a biztonsági mentési kötet választási **foglalás részletei lemez**. 
    3. Olyan munkaterhelések esetén a legördülő menüben válassza ki a kívánt tároló. A módosítások megváltoztatni az **tárhelyet** és **szabad tárhely** a a **rendelkezésre álló lemezterület** ablaktáblán. Underprovisioned-e hely, amely a biztonsági mentés Server javasol ad hozzá a köteten, zökkenőmentes biztonsági mentések biztosításához tárolókapacitást.

8.  Az a **replika-létrehozási módszer kiválasztása** lapon, válassza ki, hogyan szeretné kezelni a teljes kezdeti adatreplikálás. Ha hálózati replikálást, ajánlott csúcsidőn kívüli időpontot választani. Nagy mennyiségű adat vagy kisebb, mint optimális hálózati állapotok esetén érdemes lehet adatreplikálás a kapcsolat nélküli cserélhető adathordozó használatával.

9. Az a **válasszon konzisztencia-ellenőrzési beállítások** lapon, válassza ki, hogyan szeretné automatizálni a konzisztencia-ellenőrzést. Ha szeretné, futtasson ellenőrzést csak akkor, ha a replika adatait válik inkonzisztenssé, vagy ütemezés szerint. Ha nem szeretné konfigurálni az automatikus konzisztencia-ellenőrzését, bármikor futtathatja egy manuális ellenőrzést. Egy manuális ellenőrzést, a futtatásához az **védelmi** terület a biztonsági mentés Server felügyeleti konzol, kattintson a jobb gombbal a védelmi csoportot, és válassza **konzisztencia-ellenőrzés**.

10. Ha az Azure Backup használatával biztonsági másolatot a felhő kijelölt a **Online védelem adatainak megadása** lapon, győződjön meg arról, hogy kiválassza a munkaterhelések biztonsági mentése az Azure-bA szeretne.

11. Az a **Online biztonsági mentési ütemezés megadása** lap, az Azure-bA válassza ki, milyen gyakran növekményes biztonsági mentések történik. Biztonsági mentések minden nap, hét, hónap és év futtatni, és válassza ki a dátumát és időpontját, amelyen futtatni kell is ütemezheti. Biztonsági mentés akkor fordulhat elő, legfeljebb naponta kétszer. Minden alkalommal, amikor fut egy biztonsági mentési, helyreállítási pont létrehozása készült Azure-ban a helykiszolgáló biztonsági mentése lemezen tárolt biztonsági mentési adatok másolása.

12. Az a **Online adatmegőrzési szabály megadása** lapon, válassza ki, hogyan megőrzi a helyreállítási pontok napi, heti, havi vagy éves biztonsági mentést készített az Azure-ban.

13. Az a **Online replikációs válasszon** lapon, válassza ki az adatok teljes kezdeti replikációjához módját. A hálózaton keresztül replikálja, vagy hajtsa végre a kapcsolat nélküli (offline összehangolása) biztonsági mentés. Offline biztonsági másolat használja az Azure Import szolgáltatással. További információkért lásd: [az Azure Backup Offline biztonsági másolat munkafolyamat](backup-azure-backup-import-export.md).

14. Az a **összegzés** lapján tekintse át a beállításokat. Miután kiválasztotta a **csoport létrehozása**, akkor fordul elő, az adatok kezdeti replikálása. Ha replikálása befejeződik, a a **állapot** lapja, a védelmi csoport állapota esetén **OK**. Biztonsági mentés majd történik / a védelmi csoport beállításait.

## <a name="recover-system-state-or-bmr"></a>Rendszerállapotra vagy operációs rendszer nélküli helyreállítás
BMR vagy rendszerállapot egy hálózati helyre helyreállíthatják. Ha készített biztonsági másolatot az operációs rendszer nélküli Helyreállítás, használja a Windows helyreállítási környezet (WinRE) indítása a rendszer, és csatlakoztassa a hálózathoz. Ezt követően a Windows Server biztonsági másolat használatával állítsa helyre a hálózati helyről. Ha készített biztonsági másolatot a rendszer állapotát, használja a Windows Server biztonsági másolat használatával állítsa helyre a hálózati helyről.

### <a name="restore-bmr"></a>BMR-visszaállítás
A biztonsági mentés kiszolgáló számítógépen futtassa a helyreállítási:

1.  Az a **helyreállítási** ablaktáblán, a Keresés a számítógép, amelyen helyre kell, és válassza **operációs rendszer nélküli helyreállítás**.

2.  Rendelkezésre álló helyreállítási pontok félkövérrel szedve jelennek meg a naptárban. Válassza ki a dátum és idő, amely a használni kívánt helyreállítási pont.

3.  Az a **helyreállítási típus kiválasztása** lapon, hogy melyik **másolás hálózati mappába.**

4.  Az a **célhely megadása** oldalon, jelölje be az adatokat másolni szeretné. Ne feledje, hogy a kijelölt helyre kell-e elég hely van. Azt javasoljuk, hogy hozzon létre egy új mappát.

5.  Az a **helyreállítási beállítások megadása** lapon, válassza ki a biztonsági beállításokat kell alkalmazni. Ezt követően adja meg, hogy a tárolóhálózat (SAN) használni kívánt-alapú hardver-pillanatfelvételeket a gyorsabb helyreállítás érdekében. (Ez a beállítás csak akkor, ha egy SAN, ez a funkció érhető el, és lehetővé teszi hozhat létre, és a klón felosztására, annak írhatóvá rendelkezik. Emellett a védett számítógép és a biztonsági mentés kiszolgáló számítógép kapcsolódnia kell az ugyanazon a hálózaton.)

6.  Értesítési beállítások megadása. Az a **megerősítő** lapon jelölje be **helyreállítása**.

Állítsa be a megosztás helyét:

1.  A visszaállítás helyének keresse meg a mappa, amely a biztonsági mentés.

2.  Ossza meg a mappát, amely WindowsImageBackup egy szinttel, hogy a megosztott mappa gyökeréhez gyökerénél a WindowsImageBackup mappa. Ha nem így tesz, a visszaállítás nem találja a biztonsági mentés. A Windows helyreállítási környezet (WinRE) segítségével csatlakozzon, egy, a megfelelő IP-címét és hitelesítő adatait a WinRE keresztül elérhető megosztott kell.

A rendszer visszaállítása:

1.  Indítsa el a számítógépet, amelyen szeretné visszaállítani a rendszerképet, a Windows DVD használatával állítja vissza a rendszer.

2.  Az első lapon ellenőrizze a nyelvi és területi beállítások. Az a **telepítése** lapon jelölje be **javítsa ki a számítógép**.

3.  Az a **rendszer-helyreállítási beállítások** lapon jelölje be **visszaállíthatja a számítógép korábban létrehozott rendszerkép használatával**.

4.  Az a **válassza ki a rendszerkép biztonsági mentése** lapon, hogy melyik **lemezkép kiválasztása** > **speciális** > **rendszerkép keresése a hálózat**. Ha megjelenik egy figyelmeztetés, válassza ki a **Igen**. Nyissa meg a megosztás elérési útját adja meg a hitelesítő adatokat, majd válassza ki a helyreállítási pontot. Ez a művelet megkeresi az adott biztonsági mentések helyreállítási pontban elérhető. Válassza ki a használni kívánt helyreállítási pontot.

5.  Az a **válassza ki a biztonsági másolat visszaállítása** lapon jelölje be **lemezek formázása és újraparticionálása**. A következő lapon ellenőrizze a beállításokat. 

6.  A visszaállítás megkezdéséhez válassza **Befejezés**. Újraindításra szükség.

### <a name="restore-system-state"></a>Rendszerállapot visszaállítása

Helyreállítás futtatása a biztonsági kiszolgálón:

1.  Az a **helyreállítási** ablaktáblán, a Keresés a számítógépen, amely a helyreállítani kívánt, és válassza **operációs rendszer nélküli helyreállítás**.

2.  Rendelkezésre álló helyreállítási pontok félkövérrel szedve jelennek meg a naptárban. Válassza ki a dátum és idő, amely a használni kívánt helyreállítási pont.

3.  Az a **helyreállítási típus kiválasztása** lapon, hogy melyik **másolás hálózati mappába**.

4.  Az a **célhely megadása** oldalon, jelölje be az adatokat másolni szeretné. Ne feledje, hogy a kijelölt helyre kell-e elegendő hely. Azt javasoljuk, hogy hozzon létre egy új mappát.

5.  Az a **helyreállítási beállítások megadása** lapon, válassza ki a biztonsági beállításokat kell alkalmazni. Ezután válassza ki, hogy SAN-alapú hardveresen készített pillanatfelvételek használatához a gyorsabb helyreállítás érdekében. (Ez a beállítás csak akkor, ha egy ezt a funkciót, és képes a SAN hozhat létre, és annak írhatóvá klón felosztására, hogy. Emellett a védett számítógép és a biztonsági mentés Server kiszolgáló kapcsolódnia kell az ugyanazon a hálózaton.)

6.  Értesítési beállítások megadása. Az a **megerősítő** lapon jelölje be **helyreállítása**.

Futtassa a Windows Server biztonsági másolat:

1.  Válassza ki **műveletek** > **helyreállítása** > **ehhez a kiszolgálóhoz** > **következő**.

2.  Válassza ki **egy másik kiszolgáló**, jelölje be a **tárhely típusának megadása** lapon, majd válassza ki **távoli megosztott mappa**. Adja meg a helyreállítási pontot tartalmazó mappát.

3.  Az a **helyreállítási típus kiválasztása** lapon, hogy melyik **rendszerállapot**. 

4. Az a **helyének megadása a rendszerállapot-helyreállítás** lapon, hogy melyik **eredeti helyére**.

5.  Az a **megerősítő** lapon jelölje be **helyreállítása**. A visszaállítás után indítsa újra a kiszolgálót.

6.  Is futtathatja a rendszerállapot-visszaállítást a parancssorba. Ehhez indítsa el a Windows Server biztonsági másolat a helyreállítani kívánt számítógépen. Ahhoz, hogy a verzió azonosítóját meg egy parancssori ablakot, írja be: ```wbadmin get versions -backuptarget \<servername\sharename\>```

    A verzióazonosító segítségével indítsa el a rendszerállapot-visszaállítást. A parancssorba írja be: ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

    Győződjön meg arról, hogy szeretné-e a helyreállítás indítása. A folyamat a parancssori ablakban látható. Egy visszaállítási napló jön létre. A visszaállítás után indítsa újra a kiszolgálót.

