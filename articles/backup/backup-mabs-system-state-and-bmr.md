---
title: Az Azure Backup Server rendszer rendszerállapotának védelmét látja el, és visszaállítja az operációs rendszer nélküli
description: Az Azure Backup Server használatával a rendszerállapotának biztonsági mentése és az operációs rendszer nélküli helyreállítás (BMR) védelmének biztosítására.
services: backup
author: markgalioto
manager: carmonm
keywords: ''
ms.service: backup
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: markgal
ms.openlocfilehash: 7cb87847d6a1e191fb20dfa9cdf263066704eb6d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238811"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-with-azure-backup-server"></a>Rendszerállapotának biztonsági mentése és visszaállítása az Azure Backup Server operációs rendszer nélküli

Az Azure Backup Server rendszerállapotának biztonsági mentését, és operációs rendszer nélküli helyreállítás (BMR) védelmet nyújt.

*   **Rendszerállapot biztonsági mentését**: készít biztonsági másolatot operációs rendszer fájljait, így helyreállíthatja a számítógép elindul, de a rendszerfájlok és a beállításjegyzék elvesznek. A rendszerállapot biztonsági mentése tartalmazza:
    * Tartományi tag: rendszerindító fájlok, COM + osztályok regisztrációs adatbázisa, beállításjegyzék
    * Tartományvezérlő: Windows Server Active Directory (NTDS), rendszerindító fájlok, COM + osztályok regisztrációs adatbázisa, beállításjegyzék, rendszerkötet (SYSVOL)
    * Fürt-szolgáltatásokat futtató számítógép: Fürtkiszolgáló metaadatai
    * Tanúsítvány-szolgáltatásokat futtató számítógép: Tanúsítványadatok
* **Operációs rendszer nélküli biztonsági mentés**: biztonsági mentést készít operációsrendszer-fájlok és az összes adat a kritikus köteteken (kivéve a felhasználói adatok). A BMR biztonsági mentés definíció szerint magában foglalja a rendszerállapot biztonsági. Ha egy számítógép nem indul el, és mindent helyre kell védelmet nyújt.

A következő táblázat összefoglalja, mit, biztonsági mentése és helyreállítása. A BMR és rendszerállapot védhető app-verziókkal kapcsolatos részletes információkért lásd: [célja az Azure Backup Server biztonsági mentése?](backup-mabs-protection-matrix.md).

|Backup|Probléma|Azure Backup Server biztonsági mentés helyreállítása|Rendszerállapot helyreállítása|OPERÁCIÓS RENDSZER NÉLKÜLI HELYREÁLLÍTÁS|
|----------|---------|---------------------------|------------------------------------|-------|
|**Fájladatok**<br /><br />Adatok rendszeres biztonsági mentése<br /><br />BMR/rendszerállapot biztonsági mentése|Elveszett fájladatok|I|N|N|
|**Fájladatok**<br /><br />Az Azure Backup Server biztonsági másolat a fájladatok<br /><br />BMR/rendszerállapot biztonsági mentése|Elveszett vagy sérült operációs rendszer|N|I|I|
|**Fájladatok**<br /><br />Az Azure Backup Server biztonsági másolat a fájladatok<br /><br />BMR/rendszerállapot biztonsági mentése|Elveszett kiszolgáló (az adatkötetek nem sérültek)|N|N|I|
|**Fájladatok**<br /><br />Az Azure Backup Server biztonsági másolat a fájladatok<br /><br />BMR/rendszerállapot biztonsági mentése|Elveszett kiszolgáló (az adatkötetek elvesztek)|I|Nem|Igen (BMR, a biztonsági másolat fájladatok normál helyreállítása követi)|
|**SharePoint-adatok**:<br /><br />Az Azure Backup Server backup farmadatok<br /><br />BMR/rendszerállapot biztonsági mentése|Elveszett webhely, listák, listaelemek és dokumentumok|I|N|N|
|**SharePoint-adatok**:<br /><br />Az Azure Backup Server backup farmadatok<br /><br />BMR/rendszerállapot biztonsági mentése|Elveszett vagy sérült operációs rendszer|N|I|I|
|**SharePoint-adatok**:<br /><br />Az Azure Backup Server backup farmadatok<br /><br />BMR/rendszerállapot biztonsági mentése|Vészhelyreállítás|N|N|N|
|A Windows Server 2012 R2 Hyper-V<br /><br />Az Azure Backup Server biztonsági másolat, a Hyper-V-gazdagép vagy Vendég<br /><br />BMR/rendszerállapot biztonsági mentését gazdagép|Elveszett virtuális gép|I|N|N|
|Hyper-V<br /><br />Az Azure Backup Server biztonsági másolat, a Hyper-V-gazdagép vagy Vendég<br /><br />BMR/rendszerállapot biztonsági mentését gazdagép|Elveszett vagy sérült operációs rendszer|N|I|I|
|Hyper-V<br /><br />Az Azure Backup Server biztonsági másolat, a Hyper-V-gazdagép vagy Vendég<br /><br />BMR/rendszerállapot biztonsági mentését gazdagép|Elvesztett Hyper-V-gazdagép (VM-EK nem sérültek)|N|N|I|
|Hyper-V<br /><br />Az Azure Backup Server biztonsági másolat, a Hyper-V-gazdagép vagy Vendég<br /><br />BMR/rendszerállapot biztonsági mentését gazdagép|Elvesztett Hyper-V-gazdagép (a virtuális gépek elvesztek)|N|N|I<br /><br />Operációs rendszer nélküli Helyreállítás, az Azure Backup Server normál helyreállítása követi|
|Az SQL Server és az Exchange<br /><br />Az Azure Backup Server alkalmazás biztonsági mentése<br /><br />BMR/rendszerállapot biztonsági mentése|Elveszett alkalmazásadatok|I|N|N|
|Az SQL Server és az Exchange<br /><br />Az Azure Backup Server alkalmazás biztonsági mentése<br /><br />BMR/rendszerállapot biztonsági mentése|Elveszett vagy sérült operációs rendszer|N|Y|I|
|Az SQL Server és az Exchange<br /><br />Az Azure Backup Server alkalmazás biztonsági mentése<br /><br />BMR/rendszerállapot biztonsági mentése|Elveszett kiszolgáló (az adatbázis és a tranzakciós naplófájlok nem sérültek)|N|N|I|
|Az SQL Server és az Exchange<br /><br />Az Azure Backup Server alkalmazás biztonsági mentése<br /><br />BMR/rendszerállapot biztonsági mentése|Elveszett kiszolgáló (adatbázis és a tranzakciós naplófájlok elvesztek)|N|N|I<br /><br />BMR helyreállítás, az Azure Backup Server normál helyreállítása követi|

## <a name="how-system-state-backup-works"></a>Hogyan működik a rendszerállapot biztonsági mentése

Amikor a rendszerállapot biztonsági mentése fut, biztonsági mentési kiszolgálóra kommunikál a Windows Server biztonsági másolat igénylése a kiszolgáló rendszerállapotának biztonsági másolata. Alapértelmezés szerint a Backup Server és a Windows Server biztonsági másolat a legnagyobb elérhető szabad területtel rendelkező meghajtó használja. A meghajtóval kapcsolatos információkat a rendszer a PSDataSourceConfig.xml fájlba menti. Ez az a meghajtó, amely a Windows Server biztonsági másolat használja biztonsági mentésekhez.

Testre szabhatja a meghajtó, amely a rendszerállapot biztonsági mentése Backup Server használ. A védett kiszolgálón nyissa meg a C:\Program Files\Microsoft Data Protection Manager\MABS\Datasources. Nyissa meg szerkesztésre a PSDataSourceConfig.xml fájlt. Módosítsa a \<FilesToProtect\> a meghajtó betűjeléhez tartozó értéket. Mentse és zárja be a fájlt. Ha a számítógép rendszerállapotának védelme érdekében a védelmicsoport-készlethez, futtasson konzisztencia-ellenőrzést. Ha a rendszer riasztást állít elő, válassza ki a **védelmi csoport módosítása** kattintson a riasztásra, majd fejezze be a varázslót. Ezután futtasson konzisztencia-ellenőrzést.

Fontos megjegyezni, hogy ha a védelmi kiszolgáló része egy fürtnek, lehetséges, hogy egy fürt program fürtmeghajtót választja legnagyobb szabad területtel rendelkező meghajtóként. Ha a meghajtó tulajdonosa egy másik csomópontra, és a egy rendszer állapota biztonsági mentés futtatása lett átadva, a meghajtó nem érhető el, és a biztonsági mentés sikertelen. Ebben a forgatókönyvben a psdatasourceconfig.xml fájlt egy helyi meghajtóra, módosítani.

Ezután a Windows Server biztonsági másolat a visszaállítási mappa gyökere a WindowsImageBackup nevű mappát hoz létre. Windows Server biztonsági másolat hoz létre a biztonsági mentés, mivel minden adat ebbe a mappába kerül. Ha a biztonsági mentés befejeződött, a fájl átkerül a biztonsági mentési számítógépén. Tekintse meg az alábbi információkat:

* Ez a mappa és annak tartalma nem törlődnek a biztonsági másolat és az átvitel befejezésekor. Gondoljon erre úgy, hogy a legjobb módszer a következő alkalommal, amikor egy biztonsági mentés befejeződik a lefoglalt terület.
* A mappában jön létre minden alkalommal, amikor a biztonsági másolat készül. A dátum és idő bélyeg tükrözik az idő az utolsó rendszerállapot biztonsági mentését.

## <a name="bmr-backup"></a>Az ONH biztonsági mentés

A BMR-nek (beleértve a rendszerállapot biztonsági mentése) a biztonsági mentési feladat mentett közvetlenül egy megosztást a biztonsági mentési számítógépén. Ez a nem menthető egy mappába a védett kiszolgálón.

Biztonsági mentési kiszolgálóra meghívja a Windows Server biztonsági másolat, és kiosztja a replikakötetet az adott BMR biztonsági mentés. Ebben az esetben nem a Windows Server biztonsági másolat a legnagyobb szabad területtel rendelkező meghajtó használatára utasítja. Ehelyett a létrehozott megosztást használ a feladat.

Ha a biztonsági mentés befejeződött, a fájl átkerül a biztonsági mentési számítógépén. Naplók C:\Windows\Logs\WindowsServerBackup helyen tárolódnak.

## <a name="prerequisites-and-limitations"></a>Előfeltételek és korlátozások

-   A BMR nem támogatott a Windows Server 2003 rendszerű számítógépeken, vagy egy ügyfél operációs rendszert futtató számítógépekre.

-   A BMR és rendszerállapot különböző védelmi csoportokban ugyanazon a számítógépen nem nyújt védelmet.

-   Backup Server számítógép nem tudja védeni magát a BMR-nek.

-   Szalag (disk-to-tape vagy D2T) rövid távú védelem a BMR esetében nem támogatott. Hosszú távú tárolás szalagon (lemez-az-disk-to-tape vagy D2D2T) támogatott.

-   A BMR-védelem Windows Server biztonsági másolat a védett számítógépen telepítve van.

-   A BMR-védelem esetében eltérően a rendszerállapot-védelemre, Backup Server nem rendelkezik minden szükséges lemezterület a védett számítógépen. A Windows Server biztonsági másolat biztonsági másolatokat közvetlenül küld a Backup Server számítógéphez. A biztonsági mentési átviteli feladat nem jelenik meg a biztonsági mentési kiszolgálóra **feladatok** megtekintése.

-   Biztonsági mentési kiszolgálóra fenntartja 30 GB helyet a replikaköteten a BMR-nek. Ezt a módosíthatja a **lemezfoglalás** lapon a védelmi csoport módosítása varázslót vagy a Get-DatasourceDiskAllocation és a Set-DatasourceDiskAllocation PowerShell-parancsmagok használatával. A helyreállítási pont kötetén a BMR-védelem szükséges egy öt napnyi adatmegőrzéshez körülbelül 6 GB.
    * Vegye figyelembe, hogy a replikakötet mérete nem csökkenthető 15 GB alá.
    * Biztonsági mentési kiszolgálóra nem számítja ki a BMR-adatforrás méretét. Minden kiszolgáló esetében 30 GB feltételezi. Módosítsa az értéket, a BMR biztonsági mentések a környezetében várható mérete alapján. A BMR biztonsági mentés mérete körülbelül módon számítható felhasznált terület összege az összes kritikus kötet. Kritikus kötetek = rendszerindító kötet + rendszerkötet + a rendszerállapot-adatok, például az Active Directory tartalmazó kötet.

-   Ha módosítja a rendszerállapot BMR-védelemre, ha a BMR-védelem kevesebb területre van szükség a a *helyreállításipont-kötet*. Azonban nem felszabadul a további területet a köteten. A kötet méretét a manuális zsugorítását a **lemezfoglalás módosítása** oldalán a védelmi csoport módosítása varázslót vagy a Get-DatasourceDiskAllocation és a Set-DatasourceDiskAllocation PowerShell-parancsmagok használatával.

    Ha módosítja a rendszerállapot BMR-védelemre, a BMR-védelem több helyet igényel a a *replikakötet*. A kötet automatikusan ki van bővítve. Ha azt szeretné, módosíthatja az alapértelmezett lemezfoglalásokat a Modify-DiskAllocation PowerShell-parancsmagot használhatja.

-   Ha a rendszerállapot-védelemre módosítja a BMR-védelem, kell több helyet a helyreállítási pont kötetén. Biztonsági mentési kiszolgálóra próbálja meg a kötet automatikus növelésére. Nincs elegendő hely a tárolókészletben, ha hiba történik.

    Ha módosítja a BMR-védelem a rendszerállapot-védelemre, helyre van szüksége a védett számítógépen. Ennek az az oka a rendszerállapot-védelem először írja a replikát a helyi számítógépen, és majd átviszi a Backup Server számítógéphez.

## <a name="before-you-begin"></a>Előkészületek

1.  **Az Azure Backup Server telepítése**. Győződjön meg arról, hogy a Backup-kiszolgáló megfelelően van-e telepítve. További információkért lásd:
    * [Azure Backup Server rendszerre vonatkozó követelmények](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites)
    * [Backup Server védelmi mátrix](backup-mabs-protection-matrix.md)

2.  **Tárolás beállítása**. Biztonsági mentési adatokat lemezen, szalagon vagy az Azure-ral a felhőben tárolhatja. További információkért lásd: [az adattárolás előkészítése](https://docs.microsoft.com/system-center/dpm/plan-long-and-short-term-data-storage).

3.  **A védelmi ügynök beállítása**. Telepítse a védelmi ügynököt a számítógépen, amelyet szeretne készíteni. További információkért lásd: [a DPM védelmi ügynök telepítése](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Rendszerállapot és az operációs rendszer nélküli biztonsági mentése
Védelmi csoport beállítása leírtak szerint [védelmi csoportok telepítése](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups). Vegye figyelembe, hogy különféle csoportokba ugyanazon a számítógépen a BMR és rendszerállapot védelme nem lehetséges. Ezenkívül amikor kiválasztja a BMR, rendszerállapot automatikusan engedélyezve lesz.


1.  A Backup Server felügyeleti konzol az új védelmi csoport létrehozása varázsló megnyitásához válassza **védelmi** > **műveletek** > **védelmi csoport létrehozása** .

2.  Az a **védelmi csoport típusának kiválasztása** lapra, jelölje be **kiszolgálók**, majd válassza ki **tovább**.

3.  Az a **csoporttagok kiválasztása** lapon bontsa ki a számítógépet, és válassza ki vagy **BMR** vagy **rendszerállapot**.

    Ne feledje, hogy különféle csoportokba ugyanazon a számítógépen is a BMR és rendszerállapot védelme nem lehetséges. Ezenkívül amikor kiválasztja a BMR, rendszerállapot automatikusan engedélyezve lesz. További információkért lásd: [védelmi csoportok telepítése](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups).

4.  Az a **adatvédelmi módszer kiválasztása** lapon, válassza ki, hogyan szeretné kezelni a rövid és hosszú távú biztonsági mentést. Rövid távú biztonsági mentést, mindig először lemez, a készíthető biztonsági másolat a lemezről az Azure-ban a felhőbeli Azure Backup szolgáltatással (rövid távú vagy hosszú távú) használatával. A felhőbe történő hosszú távú biztonsági mentés helyett, hogy állítsa be a hosszú távú biztonsági mentés egy különálló eszköz, vagy szalagok szalagtárhoz, amely kapcsolódik a biztonsági mentési kiszolgálóra.

5.  Az a **rövid távú célok kiválasztása** lapon, válassza ki, hogyan szeretné a rövid távú tárolás lemezen biztonsági mentése:
    1. A **megőrzési**, válassza ki, hogy mennyi ideig szeretné megőrizni az adatokat lemezen. 
    2. A **szinkronizálási gyakoriság**, válassza ki, hogy milyen gyakran szeretne lemezre történő növekményes biztonsági mentést futtatni. Ha nem szeretné a biztonsági mentési időközt, ellenőrizheti a **közvetlenül egy helyreállítási pont előtt** lehetőséget. Biztonsági mentési kiszolgálóra fog futni egy expressz, teljes biztonsági mentés, minden egyes helyreállítási pontok ütemezése előtt.

6.  Ha szeretné-e a hosszú távú tárolás szalagon lévő adatok tárolásához a **rövid távú célok megadása** lapon, válassza ki, hogy mennyi ideig szeretné megőrizni a szalagos adatokat (1 – 99 év). 
    1. A **biztonsági mentés gyakorisága**, milyen gyakran szalagra történő biztonsági mentés válassza kell futnia. A gyakoriság a kiválasztott megőrzési alapul:
        * Ha a megőrzési időtartam 1 – 99 év, biztonsági mentések a fordulhat elő, naponta, hetente, kéthetente, havonta, negyedévente, félévente vagy évente hajthatók végre.
        * Ha a megőrzési időtartam 1 – 11 hónap, kiválaszthatja a biztonsági mentések naponta, hetente, kéthetente vagy havonta.
        * Ha a megőrzési időtartam 1 – 4 hét, biztonsági mentések naponta vagy hetente hajthatók végre.

    2. Az a **szalag és szalagtár részleteinek kiválasztása** lapon válassza ki a szalagot és szalagtárat, és hogy az adatok tömörítve és titkosítva.

7.  Az a **tekintse át a lemezfoglalás** lapon, tekintse át a készlet lemezterületet a védelmi csoport számára lefoglalt.

    1. **Adatok mérete összesen** a készíteni kívánt adatok mérete.
    2. **Az Azure Backup Server-en létesítendő lemezterület** a területe, amely a Backup Server javasolja a védelmi csoport számára. Biztonsági mentési kiszolgálóra megadott beállítások alapján az ideális biztonsági mentési kötetet választja ki. Azonban módosíthatja a biztonsági mentési kötetek választási lehetőségeit **foglalás részletei lemez**. 
    3. A számítási feladatok, a legördülő menüben válassza az előnyben részesített tárhelyet. A módosítások megváltoztatják a tartozó értékeket **tárterületének** és **szabad tárterület** a a **elérhető lemezterület** ablaktáblán. Underprovisioned hely, amely a Backup Server annak biztosítása érdekében a biztonsági mentések zökkenőmentes lefutását a kötethez hozzáadni javasol a tárhely.

8.  Az a **replika-létrehozási módszer kiválasztása** lapon, válassza ki, hogyan szeretné kezelni a kezdeti teljes adatreplikációt. Ha hálózati replikálást, javasoljuk a csúcsidőn kívüli időpontot választani. Nagy mennyiségű adatot vagy kevesebb, mint optimális hálózati körülmények esetén érdemes lehet, az adatok offline replikálni a cserélhető adathordozó használatával.

9. Az a **válassza a konzisztencia-ellenőrzési beállítások** lapon, válassza ki, hogyan szeretné automatizálni a konzisztencia-ellenőrzéseket. Ha szeretné, futtasson ellenőrzést csak akkor, amikor a replikaadatok válik inkonzisztenssé, vagy ütemezés szerint. Ha nem szeretne automatikus konzisztencia-ellenőrzést beállítani, bármikor futtathatja egy manuális ellenőrzést. A futtatásához egy manuális ellenőrzést, a **védelmi** területén a Backup Server felügyeleti konzolon kattintson a jobb gombbal a védelmi csoport, és válassza **konzisztencia-ellenőrzés végrehajtása**.

10. Ha azt választotta, a felhőbe az Azure Backup használatával biztonsági mentése a **Online védelem adatainak megadása** lapon, győződjön meg arról, hogy a számítási feladatok biztonsági mentése az Azure-bA szeretne választja.

11. Az a **Online biztonsági mentési ütemezés megadása** lapon, az Azure-ban válassza ki, milyen gyakran növekményes biztonsági mentések történik. Futtatás minden nap, hét, hónap és év, és válassza ki a dátumát és időpontját, amelyen futtatni kell a biztonsági mentéseket ütemezhet. Naponta legfeljebb kétszer történhet biztonsági mentés. Minden alkalommal, amikor egy biztonsági mentése fut, egy data helyreállítási pont létrehozása az Azure-ban a biztonsági mentési kiszolgálóra lemezen tárolt biztonsági mentési adatok másolatából.

12. Az a **Online adatmegőrzési szabály megadása** lapon, válassza ki, hogy a a napi, heti, havi és évi biztonsági mentésekből létrehozott helyreállítási pontok meddig őrizze meg az Azure-ban.

13. Az a **Online replikáció kiválasztása** lapon, válassza ki, hogyan történik a adatok teljes kezdeti replikálását. A hálózaton keresztül replikálja, vagy hajtsa végre az offline biztonsági mentést (offline áttöltést). Az offline biztonsági mentés az Azure Import szolgáltatással működik. További információkért lásd: [Offline biztonsági mentési munkafolyamat az Azure Backup](backup-azure-backup-import-export.md).

14. Az a **összefoglalás** lapon, tekintse át a beállításokat. Miután kiválasztotta **csoport létrehozása**, megtörténik az adatok kezdeti replikálása. Amikor adatok replikáció befejezését követően a a **állapot** oldal, a védelmi csoport állapota az **OK**. Biztonsági mentési beállítások ezután kerül sor / a védelem.

## <a name="recover-system-state-or-bmr"></a>Rendszerállapot vagy BMR helyreállítása
BMR vagy rendszerállapot egy hálózati helyre helyreállíthatják. Ha BMR biztonsági, használja a Windows helyreállítási környezet (WinRE) indítása a rendszer, és csatlakoztassa a hálózathoz. Ezt követően a Windows Server biztonsági másolat használatával állítsa helyre a hálózati helyről. Ha a rendszerállapot biztonsági, használja a Windows Server biztonsági másolat használatával állítsa helyre a hálózati helyről.

### <a name="restore-bmr"></a>A BMR-visszaállítás
Helyreállítás futtatása a biztonsági kiszolgálón:

1.  Az a **helyreállítási** ablaktáblán, keresse meg a számítógépen szeretné helyreállítani, és válassza ki **operációs rendszer nélküli helyreállítás**.

2.  Rendelkezésre álló helyreállítási pontok félkövérrel szedve jelennek meg a naptárban. Válassza ki a dátumot és időpontot a használni kívánt helyreállítási pontot.

3.  Az a **helyreállítási típus kiválasztása** lapra, jelölje be **másolás hálózati mappába.**

4.  Az a **célhely megadása** lapon jelölje be kívánja az adatokat másolni. Ne feledje, hogy a kiválasztott célon elegendő hely rendelkeznie kell. Azt javasoljuk, hogy hozzon létre egy új mappát.

5.  Az a **helyreállítási beállítások megadása** lapra, jelölje be a alkalmazni a biztonsági beállításokat. Ezután válassza ki, hogy a tárolóhálózat (SAN) használni kívánt-alapú hardveres pillanatképeket, a gyorsabb helyreállítás érdekében. (Ez a lehetőség csak akkor, ha rendelkezik egy TÁROLÓHÁLÓZATTAL, ez a funkció érhető el, és hogy létrehozni és megosztani egy klónt annak írhatóvá. Emellett a védett számítógép és a Backup Server számítógépet kapcsolódnia kell az ugyanazon a hálózaton.)

6.  Állítsa be az értesítési beállításokat. Az a **megerősítő** lapon jelölje be **helyreállítása**.

Állítsa be a megosztás helye:

1.  A visszaállítási helyen nyissa meg a mappát, amely rendelkezik a biztonsági mentés.

2.  Ossza meg a mappát, amely egy szinttel WindowsImageBackup, hogy a megosztott mappa gyökere a WindowsImageBackup mappa legyen. Ha nem így tesz, a visszaállítás nem találja meg a biztonsági mentés. Windows helyreállítási környezet (WinRE) segítségével csatlakozzon, egy megosztás keresztül elérhető WinRE környezetben elérhető megosztást helyes IP-címet és hitelesítő adatokat kell.

A rendszer visszaállítása:

1.  Indítsa el a számítógépet, amelyen szeretné visszaállítani a rendszerképet a Windows DVD-t használja a visszaállítani kívánt rendszernek.

2.  Az első oldalon ellenőrizze a nyelvi és területi beállításokat. Az a **telepítése** lapon jelölje be **számítógép javítása**.

3.  Az a **rendszer-helyreállítási beállítások** lapon jelölje be **a számítógép visszaállítása egy korábban létrehozott rendszerkép használatával**.

4.  Az a **egy rendszerképes biztonsági másolat kiválasztása** lapra, jelölje be **lemezkép kiválasztása** > **speciális** > **rendszerkép keresése a hálózat**. Ha figyelmeztetés jelenik meg, válassza ki a **Igen**. Nyissa meg a megosztás elérési útjára, adja meg a hitelesítő adatait, és válassza ki a helyreállítási pontot. Ez a művelet megkeresi az adott helyreállítási pontban elérhető biztonsági másolatok. Válassza ki a használni kívánt helyreállítási pontot.

5.  Az a **válassza ki a biztonsági másolat visszaállítási módját** lapon jelölje be **lemezek formázása és újraparticionálása**. A következő oldalon ellenőrizze a beállításokat. 

6.  A visszaállítás megkezdéséhez válassza **Befejezés**. Újraindításra szükség.

### <a name="restore-system-state"></a>Rendszerállapot visszaállítása

Helyreállítás futtatása a Backup Server:

1.  Az a **helyreállítási** ablaktáblán, keresse meg a helyreállítani kívánt számítógépet, és válassza ki **operációs rendszer nélküli helyreállítás**.

2.  Rendelkezésre álló helyreállítási pontok félkövérrel szedve jelennek meg a naptárban. Válassza ki a dátumot és időpontot a használni kívánt helyreállítási pontot.

3.  Az a **helyreállítási típus kiválasztása** lapra, jelölje be **másolás hálózati mappába**.

4.  Az a **célhely megadása** lapon jelölje be, ahol az adatokat másolni kívánja. Ne feledje, hogy a kijelölt célhelyen kell-e elegendő hely. Azt javasoljuk, hogy hozzon létre egy új mappát.

5.  Az a **helyreállítási beállítások megadása** lapra, jelölje be a alkalmazni a biztonsági beállításokat. Ezután válassza ki, hogy a SAN-alapú hardveres pillanatfelvételeket kíván használni a gyorsabb helyreállítás érdekében. (Ez a lehetőség csak akkor, ha ezt a funkciót, és lehetővé teszi a SAN hozhat létre, és annak írhatóvá klón rendelkezik. Emellett a védett számítógép és a Backup Server kiszolgáló kapcsolódnia kell az ugyanazon a hálózaton.)

6.  Állítsa be az értesítési beállításokat. Az a **megerősítő** lapon jelölje be **helyreállítása**.

Futtassa a Windows Server biztonsági másolat:

1.  Válassza ki **műveletek** > **helyreállítása** > **ezen a kiszolgálón** > **tovább**.

2.  Válassza ki **egy másik kiszolgálóra**, jelölje be a **tárhely típusának megadása** oldalra, és kattintson **távoli megosztott mappa**. Adja meg a helyreállítási pontot tartalmazó mappa elérési útja.

3.  Az a **helyreállítási típus kiválasztása** lapra, jelölje be **rendszerállapot**. 

4. Az a **helyének megadása a rendszerállapot-helyreállítás** lapra, jelölje be **eredeti**.

5.  Az a **megerősítő** lapon jelölje be **helyreállítása**. A visszaállítás után indítsa újra a kiszolgálót.

6.  Is futtathatja a rendszerállapot-visszaállítást parancsot a parancssorba. Ehhez indítsa el a Windows Server biztonsági másolat a helyreállítani kívánt a számítógépet. Verzió azonosítójának lekéréséhez parancsot a parancssorba írja be: ```wbadmin get versions -backuptarget \<servername\sharename\>```

    A verzióazonosító segítségével indítsa el a rendszerállapot-visszaállítást. A parancssorba írja be: ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

    Győződjön meg arról, hogy szeretné-e indítani a helyreállítást. A folyamat a parancssori ablakban látható. Egy visszaállítási napló jön létre. A visszaállítás után indítsa újra a kiszolgálót.

