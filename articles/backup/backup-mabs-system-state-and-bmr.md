---
title: A rendszer állapota és a csupasz fém visszanyerési védelem
description: Az Azure Backup Server segítségével biztonsági másolatot készíthet a rendszerállapotról, és nem megfelelő fémes helyreállítási (BMR) védelmet biztosít.
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 358a1c96d598788170993fc48e60daae2b6b036c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505875"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-by-using-azure-backup-server"></a>Biztonsági mentés a rendszerállapotról, és visszaállítás a csupasz fémre az Azure Backup Server használatával

Az Azure Backup Server biztonsági másolatot készít a rendszerállapotról, és csupasz fémes helyreállítási (BMR) védelmet biztosít.

* **Rendszerállapot biztonsági mentése**: Biztonsági másolatot készít az operációs rendszer fájljairól. Ez a biztonsági másolat lehetővé teszi a helyreállítást, amikor a számítógép elindul, de a rendszerfájlok és a rendszerleíró adatbázis elvesznek. A rendszerállapot biztonsági mentése a következő elemeket tartalmazza:
  * Tartományi tag: rendszerindító fájlok, COM+-osztályok regisztrációs adatbázisa, beállításjegyzék
  * Tartományvezérlő: Windows Server Active Directory (NTDS), rendszerindító fájlok, COM+ osztályregisztrációs adatbázis, rendszerleíró adatbázis, rendszerkötet (SYSVOL)
  * Fürtszolgáltatásokat futtató számítógép: Fürtkiszolgáló metaadatai
  * Tanúsítványszolgáltatásokat futtató számítógép: Tanúsítványadatok
* **Bare-metal backup**: Biztonsági másolatot készít az operációs rendszer fájljairól és a kritikus köteteken lévő összes adatról, kivéve a felhasználói adatokat. Definíció szerint a BMR biztonsági mentés rendszerállapot-biztonsági mentést tartalmaz. Védelmet nyújt, ha a számítógép nem indul el, és mindent vissza kell állítania.

Az alábbi táblázat összefoglalja, hogy mit lehet biztonsági másolatot és helyreállítást. A rendszerállapotés a BMR által védett alkalmazásverziókról a [Miről készít biztonsági másolatot](backup-mabs-protection-matrix.md)az Azure Backup Server?

|Backup|Probléma|Helyreállítás az Azure Backup Server biztonsági másolatából|Rendszerállapot biztonsági mentése esetén helyreállítható|BMR|
|----------|---------|---------------------------|------------------------------------|-------|
|**Fájladatok**<br /><br />Az adatok rendszeres biztonsági mentése<br /><br />Teljes biztonsági mentés (BMR)/rendszerállapot biztonsági mentése|Elveszett fájladatok|I|N|N|
|**Fájladatok**<br /><br />Az Azure Backup Server biztonsági másolata a fájladatokról<br /><br />Teljes biztonsági mentés (BMR)/rendszerállapot biztonsági mentése|Elveszett vagy sérült operációs rendszer|N|I|I|
|**Fájladatok**<br /><br />Az Azure Backup Server biztonsági másolata a fájladatokról<br /><br />Teljes biztonsági mentés (BMR)/rendszerállapot biztonsági mentése|Elveszett kiszolgáló (az adatkötetek sértetlenek)|N|N|I|
|**Fájladatok**<br /><br />Az Azure Backup Server biztonsági másolata a fájladatokról<br /><br />Teljes biztonsági mentés (BMR)/rendszerállapot biztonsági mentése|Elveszett kiszolgáló (az adatkötetek elvesztek)|I|N|I<br /><br />BMR, majd a biztonsági másolatok adatainak rendszeres helyreállítása|
|**SharePoint-adatok**<br /><br />Az Azure Backup Server biztonsági másolata a farmadatokról<br /><br />Teljes biztonsági mentés (BMR)/rendszerállapot biztonsági mentése|Elveszett webhely, listák, listaelemek, dokumentumok|I|N|N|
|**SharePoint-adatok**<br /><br />Az Azure Backup Server biztonsági másolata a farmadatokról<br /><br />Teljes biztonsági mentés (BMR)/rendszerállapot biztonsági mentése|Elveszett vagy sérült operációs rendszer|N|I|I|
|**SharePoint-adatok**<br /><br />Az Azure Backup Server biztonsági másolata a farmadatokról<br /><br />Teljes biztonsági mentés (BMR)/rendszerállapot biztonsági mentése|Vészhelyreállítás|N|N|N|
|Windows Server 2012 R2 Hyper-V<br /><br />Az Azure Backup Server biztonsági másolata a Hyper-V állomásról vagy vendégről<br /><br />A gazdagép teljes biztonsági mentése (BMR)/rendszerállapotának biztonsági mentése|Elveszett virtuális gép|I|N|N|
|Hyper-V<br /><br />Az Azure Backup Server biztonsági másolata a Hyper-V állomásról vagy vendégről<br /><br />A gazdagép teljes biztonsági mentése (BMR)/rendszerállapotának biztonsági mentése|Elveszett vagy sérült operációs rendszer|N|I|I|
|Hyper-V<br /><br />Az Azure Backup Server biztonsági másolata a Hyper-V állomásról vagy vendégről<br /><br />A gazdagép teljes biztonsági mentése (BMR)/rendszerállapotának biztonsági mentése|Elvesztett Hyper-V-gazdagép (a virtuális gépek nem sérültek)|N|N|I|
|Hyper-V<br /><br />Az Azure Backup Server biztonsági másolata a Hyper-V állomásról vagy vendégről<br /><br />A gazdagép teljes biztonsági mentése (BMR)/rendszerállapotának biztonsági mentése|Elvesztett Hyper-V-gazdagép (a virtuális gépek elvesztek)|N|N|I<br /><br />BMR, majd rendszeres Azure Backup Server helyreállítás|
|SQL Server/Exchange<br /><br />Az Azure Backup Server alkalmazás biztonsági másolata<br /><br />Teljes biztonsági mentés (BMR)/rendszerállapot biztonsági mentése|Elveszett alkalmazásadatok|I|N|N|
|SQL Server/Exchange<br /><br />Az Azure Backup Server alkalmazás biztonsági másolata<br /><br />Teljes biztonsági mentés (BMR)/rendszerállapot biztonsági mentése|Elveszett vagy sérült operációs rendszer|N|I|I|
|SQL Server/Exchange<br /><br />Az Azure Backup Server alkalmazás biztonsági másolata<br /><br />Teljes biztonsági mentés (BMR)/rendszerállapot biztonsági mentése|Elveszett kiszolgáló (az adatbázis és a tranzakciós naplófájlok nem sérültek)|N|N|I|
|SQL Server/Exchange<br /><br />Az Azure Backup Server alkalmazás biztonsági másolata<br /><br />Teljes biztonsági mentés (BMR)/rendszerállapot biztonsági mentése|Elveszett kiszolgáló (az adatbázis és a tranzakciós naplófájlok elvesztek)|N|N|I<br /><br />BMR-helyreállítás, majd rendszeres Azure Backup Server-helyreállítás|

## <a name="how-system-state-backup-works"></a>A rendszerállapot biztonsági mentésének működése

Amikor a rendszerállapot biztonsági másolata fut, a biztonsági másolat kiszolgálója kommunikál a Windows Server biztonsági másolat segédprogrammal, és biztonsági másolatot kér a kiszolgáló rendszerállapotáról. A Biztonságimásolat-kiszolgáló és a Windows Server biztonsági másolat segédprogram alapértelmezés szerint azt a meghajtót használja, amelyen a legtöbb szabad terület van. A meghajtóval kapcsolatos információk a *PSDataSourceConfig.xml* fájlba kerülnek. 

Testreszabhatja a Biztonsági másolat kiszolgáló által a rendszerállapot biztonsági mentéséhez használt meghajtót: 

1. A védett kiszolgálón nyissa meg a *C:\Program Files\Microsoft Data Protection Manager\MABS\Datasources mappát.* 
1. Nyissa meg a *PSDataSourceConfig.xml* fájlt szerkesztésre. 
1. Módosítsa \<a meghajtó\> betűjelének FilesToProtect értékét. 
1. Mentse és zárja be a fájlt. 

Ha egy védelmi csoport a számítógép rendszerállapotának védelmére van beállítva, futtasson konzisztencia-ellenőrzést. Ha riasztás jön létre, válassza a **Védelmi csoport módosítása lehetőséget** a riasztásban, majd töltse ki a varázsló lapjait. Ezután futtasson egy újabb konzisztencia-ellenőrzést.

Ha a védelmi kiszolgáló fürtben található, a legtöbb szabad hellyel rendelkező meghajtóként fürtmeghajtót lehet kiválasztani. Ha a meghajtó tulajdonjogát átvált egy másik csomópontra, és a rendszerállapot biztonsági mentése fut, akkor a meghajtó nem érhető el, és a biztonsági mentés sikertelen lesz. Ebben az esetben módosítsa a *PSDataSourceConfig.xml fájlt* úgy, hogy az egy helyi meghajtóra mutasson.

Ezután a Windows Server biztonsági másolat létrehoz egy *WindowsImageBackup* nevű mappát a visszaállítási mappa gyökerében. Mivel a Windows Server biztonsági másolat létrehozza a biztonsági másolatot, az összes adat ebbe a mappába kerül. Amikor a biztonsági másolat befejeződik, a fájl átkerül a biztonsági másolat kiszolgálójára. Tekintse meg az alábbi információkat:

* A mappa és tartalma nem törlődik, amikor a biztonsági mentés vagy az átvitel befejeződik. A legjobb módja annak, hogy úgy gondolja, ez az, hogy a hely van fenntartva a következő alkalommal, amikor egy biztonsági mentés befejeződik.
* A mappa minden biztonsági mentéshez létrejön. Az idő- és dátumbélyegző az utolsó rendszerállapot-biztonsági mentés időpontját tükrözi.

## <a name="how-bmr-backup-works"></a>A BMR biztonsági mentés működése

BMR (beleértve a rendszerállapot biztonsági mentését is) esetén a biztonsági mentési feladat közvetlenül a Biztonsági másolat kiszolgáló számítógépének egy megosztására kerül mentésre. Nem menti a védett kiszolgáló egyik mappájába.

A Biztonsági másolat kiszolgáló meghívja a Windows Server biztonsági mentést, és megosztja a BMR biztonsági másolat replikákötetét. Ebben az esetben nincs szükség a Windows Server biztonsági másolat szolgáltatásra a legtöbb szabad területtel rendelkező meghajtó használatához. Ehelyett a feladathoz létrehozott megosztást használja.

Amikor a biztonsági másolat befejeződik, a fájl átkerül a biztonsági másolat kiszolgálójára. A naplók a *C:\Windows\Logs\WindowsServerBackup mappában*tárolódnak.

## <a name="prerequisites-and-limitations"></a>Előfeltételek és korlátozások

* A BMR nem támogatott Windows Server 2003 rendszert vagy ügyféloperációs rendszert futtató számítógépeken.

* A BMR és a rendszerállapot védelme ugyanazon számítógép esetében nem lehetséges különböző védelmi csoportokban.

* A biztonsági másolat készítő kiszolgáló számítógépe nem tudja megvédeni magát a BMR-vel szemben.

* A szalag rövid távú védelme (lemezről szalagra vagy D2T) nem támogatott a BMR esetében. A hosszú távú szalagra történő tárolás (lemezről lemezre vagy D2D2T) támogatott.

* A BMR védelem hez a Windows Server biztonsági másolat segédprogramot telepíteni kell a védett számítógépre.

* A BMR-védelem esetében a rendszerállapot-védelemmel ellentétben a biztonsági másolat kiszolgálónak nincs helyigénye a védett számítógépen. A Windows Server biztonsági másolat közvetlenül továbbítja a biztonsági másolatokat a biztonsági másolat készítő kiszolgáló számítógépére. A biztonsági mentési átviteli feladat nem jelenik meg a Biztonsági másolat **kiszolgálói feladatok** nézetben.

* A biztonsági másolat kiszolgálója 30 GB helyet foglal a replikaköteten a BMR számára. Ezt a területleosztást a Védelem csoport módosítása varázsló **Lemezfoglalás** lapján módosíthatja. Vagy használhatja a Get-DatasourceDiskAllocation és set-DatasourceDiskAllocation PowerShell parancsmagokat. A helyreállítási pont kötetén a BMR-védelem körülbelül 6 GB-ot igényel az öt napos megőrzéshez.
  * A replikakötet mérete nem csökkenthető 15 GB-nál kisebbre.
  * A Biztonsági másolat kiszolgáló nem számítja ki a BMR adatforrás méretét. Azt feltételezi, 30 GB minden szerver. Módosítsa az értéket a környezetben várt BMR-biztonsági mentések mérete alapján. Nagyjából kiszámíthatja a BMR biztonsági mentés méretét az összes kritikus köteten használt terület összegeként. Kritikus kötetek = rendszerindító kötet + rendszerkötet + rendszerüzemeltetési rendszerállapot-adatok, például az Active Directory.

* Ha a rendszerállapot-védelemről BMR-védelemre vált, akkor a BMR-védelem kevesebb helyet igényel a *helyreállítási pont kötetén.* A köteten lévő extra terület azonban nem nyered vissza. A kötetméretét manuálisan is csökkentheti a Védelemcsoport módosítása varázsló **Lemezfoglalás módosítása** lapján. Vagy használhatja a Get-DatasourceDiskAllocation és set-DatasourceDiskAllocation PowerShell parancsmagokat.

    Ha a rendszerállapot-védelemről BMR-védelemre vált, akkor a BMR-védelem több helyet igényel a *replikaköteten.* A kötet automatikusan kibővül. Ha módosítani szeretné az alapértelmezett helyfoglalásokat, használja a Modify-DiskAllocation PowerShell parancsmamot.

* Ha a BMR-védelemről rendszerállapot-védelemre vált, akkor több helyre van szüksége a helyreállítási pont kötetén. Előfordulhat, hogy a Biztonsági másolat kiszolgálója megpróbálja automatikusan növelni a kötetet. Ha a tárolókészlet nem rendelkezik elegendő hellyel, hiba történik.

    Ha a BMR-védelemről rendszerállapot-védelemre vált, akkor a védett számítógépen kell helyet foglalnunk. Szüksége van a térre, mert a rendszerállapot-védelem először a helyi számítógépre írja a kópiát, majd átviszi a replikát a biztonsági másolat kiszolgálószámítógépére.

## <a name="before-you-begin"></a>Előkészületek

1. **Az Azure Backup Server telepítése**. Ellenőrizze, hogy a Biztonsági másolat kiszolgáló telepítése megfelelően van-e telepítve. További információkért lásd:
    * [Az Azure Backup Server rendszerkövetelményei](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites)
    * [Biztonsági másolat kiszolgálóvédelmi mátrixa](backup-mabs-protection-matrix.md)

1. **A tároló beállítása**. A biztonsági mentési adatokat lemezen, szalagon és a felhőben tárolhatja az Azure-ral. További információ: [Az adattárolás előkészítése](https://docs.microsoft.com/system-center/dpm/plan-long-and-short-term-data-storage).

1. **Állítsa be a védelmi ügynököt**. Telepítse a védelmi ügynököt arra a számítógépre, amelyről biztonsági másolatot szeretne tenni. További információ: [Deploy the DPM protection agent](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Rendszerállapot biztonsági mentése és teljes biztonsági mentés

A rendszerállapot és a csupasz fém biztonsági másolatot kell adni:

1. Az Új védelmi csoport létrehozása varázsló megnyitásához a Biztonságimásolat-kiszolgáló felügyeleti konzolján válassza a **Védelmi** > **műveletek** > **védelmi csoport létrehozása lehetőséget.**

1. A **Védelem csoporttípus kiválasztása** lapon válassza a **Kiszolgálók (Kiszolgálók)** lehetőséget, majd a **Tovább**gombot.

1. A **Csoporttagok kiválasztása** lapon bontsa ki a számítógépet, majd válassza a **BMR** vagy a **rendszerállapot lehetőséget.**

    Ne feledje, hogy nem védheti a BMR-t és a rendszerállapotot ugyanazon számítógép különböző csoportokban. A BMR kiválasztásakor a rendszerállapot automatikusan engedélyezve lesz. További információ: [Deploy protection groups](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups).

1. Az **Adatvédelmi módszer kiválasztása** lapon válassza ki, hogyan kezelje a rövid távú biztonsági mentést és a hosszú távú biztonsági mentést. 

    A rövid távú biztonsági mentés mindig a lemez az első, azzal a lehetőséggel, biztonsági mentésa a lemezről az Azure Biztonsági mentés használatával (rövid távú vagy hosszú távú). A felhőbe való hosszú távú biztonsági mentés alternatívája ként hosszú távú biztonsági mentést állíthat be egy biztonsági másolatkiszolgálóhoz csatlakoztatott önálló szalagos eszközre vagy szalagos tárba.

1. A **Rövid távú célok kiválasztása** lapon válassza ki, hogyan készüljön biztonsági másolatot a lemezen való rövid távú tárolásra:
    * Az **Adatmegőrzési tartomány csoportban**adja meg, hogy mennyi ideig tartsa meg az adatokat a lemezen.
    * A **szinkronizálás gyakorisága,** adja meg, hogy milyen gyakran futtasson növekményes biztonsági mentés t lemezre. Ha nem szeretne biztonsági mentési időközt beállítani, válassza **a Közvetlenül a helyreállítási pont előtt**lehetőséget. A Biztonsági másolat kiszolgáló az egyes helyreállítási végpontok ütemezése előtt egy expressz teljes biztonsági mentést fog futtatni.

1. Ha hosszú távú tárolás céljából szalagon szeretné tárolni az adatokat, akkor a **Hosszú távú célok megadása** lapon adja meg, hogy mennyi ideig őrizze meg a szalagadatokat (1–99 év).
    1. A **Biztonsági mentés gyakorisága**területen adja meg, hogy milyen gyakran futtassa a biztonsági másolatot szalagra. A gyakoriság a kiválasztott megőrzési tartományon alapul:
        * Ha a megőrzési tartomány 1-99 év, akkor biztonsági másolatot lehet tenni naponta, hetente, kéthetente, havonta, negyedévente, félévente vagy évente.
        * Ha a megőrzési tartomány 1–11 hónap, akkor naponta, hetente, kéthetente vagy havonta biztonsági másolatot kaphat.
        * Ha a megőrzési tartomány 1-4 hét, akkor biztonsági másolatot naponta vagy hetente.

    1. A **Szalag és tár részleteinek kiválasztása** lapon jelölje ki a használni kívánt szalagot és tárat. Azt is megadhatja, hogy az adatokat tömörítse és titkosítsa-e.

1. A **Lemezfoglalás áttekintése** lapon tekintse át a védelmi csoport számára rendelkezésre álló tárterület-lemezterületet.

    * **A teljes adatméret** a biztonsági másolatot létrehozni kívánt adatok mérete.
    * **Az Azure Backup Server kiszolgálón kiépítendő lemezterület** az a terület, amelyet a Biztonsági másolat kiszolgáló a védelmi csoport számára javasol. A Biztonsági másolat kiszolgálója ezeket a beállításokat használja az ideális biztonsági mentési kötet kiválasztásához. A biztonsági másolat kötetének választási lehetőségeit a **Lemezfoglalás részletei ben**szerkesztheti.
    * A számítási feladatok hoz a legördülő menüben válassza ki az előnyben részesített tároló. A módosítások megváltoztatják az **Összes tárhely** és a **Szabad tárterület** értékeit az **Elérhető lemezterület** ablaktáblán. Alulkiépített terület az a tárterület, amelyet a Biztonsági másolat kiszolgáló javasol a kötethez a zökkenőmentes biztonsági mentés biztosítása érdekében.

1. A **Replika-létrehozási mód kiválasztása lapon** válassza ki, hogyan kezelje a kezdeti teljes adatreplikációt. 

    Ha úgy dönt, hogy replikálja a hálózaton keresztül, azt javasoljuk, hogy válasszon egy csúcsidőn kívüli idő. Nagy mennyiségű adat vagy az optimálisnál kisebb hálózati feltételek esetén fontolja meg az adatok kapcsolat nélküli replikálását cserélhető adathordozó használatával.

1. A **Konzisztencia-ellenőrzési beállítások megadása** lapon adja meg, hogyan automatizálhatja a konzisztencia-ellenőrzéseket. 

    Csak akkor futtathat ellenőrzést, ha a replikaadatok inkonzisztenssé válnak, vagy ütemezés szerint. Ha nem szeretné konfigurálni az automatikus konzisztencia-ellenőrzést, akkor bármikor futtathat manuális ellenőrzést. A manuális ellenőrzés futtatásához a Biztonságimásolat-kiszolgáló felügyeleti konzoljának **Védelmi** területén kattintson a jobb gombbal a védelmi csoportra, majd válassza a **Konzisztencia-ellenőrzés végrehajtása parancsot.**

1. Ha úgy döntött, hogy biztonsági másolatot készít a felhőbe az Azure Backup használatával, az **Online védelem adatai megadása** lapon válassza ki azokat a számítási feladatokat, amelyekről biztonsági másolatot szeretne készíteni az Azure-ba.

1. Az **Online biztonsági mentés ütemezésének megadása** lapon adja meg, hogy milyen gyakran készítsen növekményes biztonsági mentést az Azure-ba. 

    A biztonsági mentések minden nap, hét, hónap és év futtatását ütemezheti. Azt is megadhatja, hogy a biztonsági mentések mikor és mikor fussanak. Naponta legfeljebb kétszer történhet biztonsági mentés. Minden alkalommal, amikor egy biztonsági mentés fut, egy adat-helyreállítási pont jön létre az Azure-ban a biztonsági mentési adatok másolata, amely a Backup Server lemezen tárolt.

1. Az **Online adatmegőrzési szabályzat megadása** lapon adja meg, hogyan tárolják a napi, heti, havi és éves biztonsági mentéseket az Azure-ban létrehozott helyreállítási pontokat.

1. Az **Online replikáció kiválasztása** lapon adja meg, hogyan történik az adatok kezdeti teljes replikációja. 

    Replikálható a hálózaton keresztül, vagy biztonsági másolatot offline (offline vetés). Egy offline biztonsági mentés az Azure Import funkciót használja. További információt az [Azure Backup offline biztonsági mentési munkafolyamata című témakörben talál.](offline-backup-azure-data-box.md)

1. Az **Összegzés** lapon tekintse át a beállításokat. A **Csoport létrehozása**lehetőséget választva az adatok kezdeti replikációja történik. Amikor az adatreplikáció befejeződik, az **Állapot** lapon a védelmi csoport állapota **rendben**van. A biztonsági mentések ezután a védelmi csoport beállításainak megfelelően történnek.

## <a name="recover-system-state-or-bmr"></a>Rendszerállapot vagy BMR helyreállítása

A BMR, illetve a rendszerállapot mentése helyreállítható egy hálózati helyre. Ha biztonsági másolatot tett a BMR-ről, akkor a Windows helyreállítási környezet (WinRE) segítségével indítsa el a rendszert, és csatlakoztassa a hálózathoz. Ezután a Windows Server biztonsági másolat (WSB) eszközével végezzen helyreállítást a hálózati helyről. Ha biztonsági másolatot tett a rendszerállapotról, akkor a Windows Server biztonsági másolat segédprogrammal helyreállíthatja a hálózati helyet.

### <a name="restore-bmr"></a>BMR-visszaállítás

A helyreállítás futtatása a biztonsági másolat kiszolgálója számítógépen:

1. A **Helyreállítás** ablaktáblán keresse meg a helyreállítani kívánt számítógépet. Ezután válassza **a Bare Metal Recovery**lehetőséget.

1. Az elérhető helyreállítási pontok félkövérrel szedve jelennek meg a naptárban. Válassza ki a használni kívánt helyreállítási pont dátumát és időpontját.

1. A **Helyreállítási típus kiválasztása** lapon válassza **a Másolás hálózati mappába**lehetőséget.

1. A **Cél megadása** lapon válassza ki a másolt adatok célját. 

    Ne feledje, hogy az úti célnak elegendő helynek kell lennie az adatok számára. Javasoljuk, hogy hozzon létre egy új mappát a cél.

1. A **Helyreállítási beállítások megadása** lapon adja meg a biztonsági beállításokat. Ezután válassza ki, hogy a tárolóhálózat (SAN) alapú hardverpillanatképeket használja-e a gyorsabb helyreállítás érdekében. Ez a beállítás csak akkor érhető el, ha:
    * Van egy SAN, amely ezt a funkciót. 
    * Létrehozhat és feloszthat egy klónt, hogy írható legyen.
    * A védett számítógép és a biztonságimásolat-kiszolgáló számítógép ugyanahhoz a hálózathoz csatlakozik.

1. Értesítési beállítások megadása. 
1. A **Megerősítés** lapon válassza a **Helyreállítás**lehetőséget.

A megosztási hely beállítása:

1. A visszaállítási helyen nyissa meg a biztonsági másolatot tartalmazó mappát.

1. Ossza meg azt a mappát, amely egy szinttel a *WindowsImageBackup* felett van, hogy a megosztott mappa gyökere a *WindowsImageBackup* mappa. 

    Ha nem osztja meg ezt a mappát, a visszaállítás nem találja a biztonsági másolatot. A WinRE használatával való csatlakozáshoz olyan megosztásra van szüksége, amelyet a WinRE-ben a megfelelő IP-címmel és hitelesítő adatokkal érhet el.

A rendszer visszaállítása:

1. Indítsa el azt a számítógépet, amelyen vissza szeretné állítani a lemezképet a visszaállítani kívánt rendszer Windows DVD-jével.

1. Az első oldalon ellenőrizze a nyelvi és területi beállításokat. A **Telepítés** lapon válassza **a Számítógép javítása**lehetőséget.

1. A **Rendszer-helyreállítási beállítások** lapon válassza a Számítógép visszaállítása a **korábban létrehozott rendszerkép pelenka alapján**lehetőséget.

1. A **Rendszerkép biztonsági másolatának kiválasztása** lapon válassza **a Rendszerkép** > kiválasztása**irányított** > keresés lehetőséget**a hálózaton lévő rendszerképhez**. Ha figyelmeztetés jelenik meg, válassza az **Igen**lehetőséget. Lépjen a megosztási útvonalra, adja meg a hitelesítő adatokat, majd válassza ki a helyreállítási pontot. A rendszer az adott helyreállítási ponton rendelkezésre álló konkrét biztonsági másolatokat keres. Jelölje ki a használni kívánt helyreállítási pontot.

1. A **Válassza ki a Biztonsági másolat lap visszaállításának módját,** válassza a **Lemezek formázása és újraparticionálása**lehetőséget. A következő oldalon ellenőrizze a beállításokat.

1. A visszaállítás megkezdéséhez válassza a **Befejezés gombot.** Újraindítás szükséges.

### <a name="restore-system-state"></a>Rendszerállapot visszaállítása

A helyreállítás futtatása a biztonsági másolat kiszolgálón:

1. A **Helyreállítás** ablaktáblán keresse meg a helyreállítani kívánt számítógépet, és válassza a **Bare Metal Recovery (Csupasz fém helyreállítása) lehetőséget.**

1. Az elérhető helyreállítási pontok félkövérrel szedve jelennek meg a naptárban. Válassza ki a használni kívánt helyreállítási pont dátumát és időpontját.

1. A **Helyreállítási típus kiválasztása** lapon válassza **a Másolás hálózati mappába**lehetőséget.

1. A **Cél megadása** lapon adja meg, hogy hová szeretné másolni az adatokat. 

    Ne feledje, hogy a kiválasztott úti célnak elegendő helynek kell lennie az adatok számára. Javasoljuk, hogy hozzon létre egy új mappát a cél.

1. A **Helyreállítási beállítások megadása** lapon adja meg a biztonsági beállításokat. Ezután válassza ki, hogy a SAN-alapú hardver pillanatképek, a gyorsabb helyreállítás érdekében. Ez a beállítás csak akkor érhető el, ha: 
    * Van egy SAN, amely ezt a funkciót.
    * Létrehozhat és feloszthat egy klónt, hogy írható legyen. 
    * A védett számítógép és a biztonságimásolat-kiszolgáló kiszolgáló ugyanahhoz a hálózathoz csatlakozik.

1. Értesítési beállítások megadása. 
1. A **Megerősítés** lapon válassza a **Helyreállítás**lehetőséget.

A Windows Server biztonsági másolat futtatása:

1. Válassza a**Kiszolgáló** > **helyreállítása** > **a következő műveletet.** **Actions** > 

1. Válassza **az Másik kiszolgáló**lehetőséget, válassza a **Helytípus megadása** lapot, majd a Távoli megosztott **mappa**lehetőséget . Adja meg a helyreállítási pontot tartalmazó mappa elérési útját.

1. A **Helyreállítási típus kiválasztása** lapon válassza a **Rendszerállapot**lehetőséget.

1. A **Hely kiválasztása a Rendszerállapot-helyreállításhoz** lapon válassza az **Eredeti hely lehetőséget.**

1. A **Megerősítés** lapon válassza a **Helyreállítás**lehetőséget. 

1. A visszaállítás után indítsa újra a kiszolgálót.

A rendszerállapot-visszaállítás t is futtathatja a parancssorból: 

1. Indítsa el a Windows Server biztonsági másolat segédprogramot a helyreállítani kívánt számítógépen. 

1. A verzióazonosító lekérése a parancssorba írja be a következőt:

   ```wbadmin get versions -backuptarget \<servername\sharename\>```

1. A rendszerállapot-visszaállítás elindításához használja a verzióazonosítót. A parancssorba írja be a következőt: 

    ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

1. Erősítse meg, hogy el szeretné indítani a helyreállítást. A folyamat a parancssorablakban látható. Létrejön egy visszaállítási napló. 

1. A visszaállítás után indítsa újra a kiszolgálót.
