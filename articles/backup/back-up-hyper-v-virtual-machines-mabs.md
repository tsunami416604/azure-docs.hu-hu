---
title: Hyper-V rendszerű virtuális gépek biztonsági mentése Azure Backup Server
description: Ez a cikk a virtuális gépek Microsoft Azure Backup kiszolgáló használatával történő biztonsági mentésének és helyreállításának eljárásait tartalmazza.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: dacurwin
ms.openlocfilehash: f15606c83c221e4591a2a1f6a71fc7141bdf3daf
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074970"
---
# <a name="back-up-hyper-v-virtual-machines-with-azure-backup-server"></a>Hyper-V rendszerű virtuális gépek biztonsági mentése Azure Backup Server

Ez a cikk azt ismerteti, hogyan lehet biztonsági másolatot készíteni a Hyper-V rendszerű virtuális gépekről Microsoft Azure Backup kiszolgáló (MABS) használatával.

## <a name="supported-scenarios"></a>Támogatott esetek

A MABS a Hyper-V-gazdagépeken futó virtuális gépek biztonsági mentését a következő esetekben végezheti el:

- **Virtuális gépek helyi vagy közvetlen tárolással** – a Hyper-V gazdagépen üzemeltetett virtuális gépek biztonsági mentése helyi vagy közvetlenül csatlakoztatott tárolóval. Például: merevlemez, Tárolóhálózati (SAN) eszköz vagy hálózati csatlakoztatott tároló (NAS) eszköz. A MABS védelmi ügynököt minden gazdagépre telepíteni kell.

- **Fürtben lévő virtuális gépek CSV-tárolóval** – fürt megosztott KÖTETE (CSV) tárolóval rendelkező Hyper-V fürtön futó virtuális gépek biztonsági mentése. A MABS védelmi ügynök minden fürtcsomóponton telepítve van.

## <a name="host-versus-guest-backup"></a>Gazdagép és vendég biztonsági mentése

A MABS a Hyper-V virtuális gépek gazdagépét vagy vendég szintű biztonsági mentését is elvégezheti. A gazdagép szintjén a MABS védelmi ügynök telepítve van a Hyper-V-gazdagépen vagy-fürtön, és megvédi a gazdagépen futó összes virtuális gépet és adatfájlt.   A vendég szinten az ügynököt minden virtuális gépre telepíti, és megvédi a gépen lévő munkaterhelést.

Mindkét módszernek vannak előnyei és hátrányai:

- A gazdagép szintű biztonsági mentések rugalmasak, mert a vendég gépen futó operációs rendszer típusától függetlenül működnek, és nem szükséges a MABS védelmi ügynök telepítése minden egyes virtuális gépen. Ha a gazdagép szintű biztonsági mentést telepíti, a teljes virtuális gépet, illetve a fájlokat és mappákat (elemszintű helyreállítás) is helyreállíthatja.

- A vendég szintű biztonsági mentés akkor lehet hasznos, ha egy virtuális gépen futó adott munkaterheléseket kíván védelemmel ellátni. A gazdagép szintjén helyreállíthatja a teljes virtuális gépet vagy az adott fájlokat, de nem biztosít a helyreállítást egy adott alkalmazás kontextusában. Ha például adott SharePoint-elemeket szeretne helyreállítani egy biztonsági másolatból álló virtuális gépről, akkor a virtuális gép vendég szintű biztonsági másolatát kell használnia. Ha az áteresztő lemezeken tárolt adatvédelmet szeretné biztosítani, használja a vendég szintű biztonsági mentést. A továbbító lehetővé teszi, hogy a virtuális gép közvetlenül hozzáférhessen a tárolóeszközhöz, és nem tárolja a virtuális köteteket a VHD-fájlokban.

## <a name="how-the-backup-process-works"></a>A biztonsági mentési folyamat működése

A MABS a következőképpen végzi a biztonsági mentést a VSS-vel. A leírásban szereplő lépések számozása az érthetőség érdekében megtörténik.

1. A MABS blokk-alapú szinkronizációs motor a védett virtuális gép kezdeti másolatát készíti el, és biztosítja, hogy a virtuális gép másolata elkészült és konzisztens legyen.

2. A kezdeti másolat létrejötte és ellenőrzése után a MABS a Hyper-V VSS-író használatával rögzíti a biztonsági mentéseket. A VSS-író adathalmazt biztosít a MABS-kiszolgálóval szinkronizált lemezes blokkokhoz. Ez a megközelítés biztosítja a teljes biztonsági mentés előnyeit a MABS-kiszolgálóval, miközben minimalizálja a hálózaton keresztül továbbított biztonsági mentési adatmennyiséget.

3. A Hyper-V-t futtató kiszolgálókon futó MABS védelmi ügynök a meglévő Hyper-V API-k használatával határozza meg, hogy a védett virtuális gép is támogatja-e a VSS-t.

   - Ha egy virtuális gép megfelel az online biztonsági mentés követelményeinek, és telepítette a Hyper-V Integration Services összetevőt, akkor a Hyper-V VSS-író rekurzív módon továbbítja a VSS-kérést a virtuális gép összes VSS-t támogató folyamatához. Ez a művelet a MABS védelmi ügynöknek a virtuális gépre való telepítése nélkül történik. A rekurzív VSS-kérelem lehetővé teszi a Hyper-V VSS-író számára a lemezes írási műveletek szinkronizálását, hogy a rendszer adatvesztés nélkül rögzítse a VSS-pillanatképet.

     A Hyper-V Integration Services összetevő meghívja a Hyper-V VSS-írót a virtuális gépeken a Kötet árnyékmásolata szolgáltatásban (VSS), így biztosítva, hogy az alkalmazásadatok konzisztens állapotban legyenek.

   - Ha a virtuális gép nem felel meg az online biztonsági mentés követelményeinek, a MABS automatikusan a Hyper-V API-k használatával szünetelteti a virtuális gépet az adatfájlok rögzítése előtt.

4. Miután a virtuális gép kezdeti példánya szinkronizálva lett a MABS-kiszolgálóval, a rendszer a virtuális gép erőforrásain végrehajtott összes módosítást egy új helyreállítási pontba rögzíti. A helyreállítási pont a virtuális gép adott időpontban történő konzisztens állapotát jelöli. A helyreállítási pontok rögzítése naponta legalább egyszer megtörténhet. Új helyreállítási pont létrehozásakor a MABS a Hyper-V VSS-író mellett blokkolja a blokk szintű replikálást annak meghatározásához, hogy a legutóbbi helyreállítási pont létrehozása után a Hyper-V-t futtató kiszolgálón milyen blokkok lettek módosítva. Ezeket az adatblokkokat a rendszer átviszi a MABS-kiszolgálóra, és alkalmazza őket a védett adatreplikára.

5. A MABS-kiszolgáló a helyreállítási adattároló köteteken található VSS-t használja, hogy több árnyékmásolat is elérhető legyen. Mindegyik árnyékmásolat külön helyreállítást biztosít. A VSS helyreállítási pontjait a MABS-kiszolgálón tárolja a rendszer. A Hyper-V-t futtató kiszolgálón végrehajtott ideiglenes másolat csak a MABS-szinkronizálás időtartamára van tárolva.

>[!NOTE]
>
>A Windows Server 2016-es verziótól kezdődően a Hyper-V virtuális merevlemezek beépített változások követését jelentik a rugalmas változások követése (RCT) néven. A MABS a RCT (a Hyper-V natív változások nyomon követését) használja, ami csökkenti az időigényes konzisztencia-ellenőrzés szükségességét olyan helyzetekben, mint például a virtuális gépek összeomlása. A RCT nagyobb rugalmasságot biztosít, mint a VSS pillanatkép-alapú biztonsági mentések által biztosított változások nyomon követése. A MABS v3 a konzisztencia-ellenőrzések során csak a módosult adatmennyiségek átvitelével optimalizálja a hálózat és a tárterület felhasználását.

## <a name="backup-prerequisites"></a>Biztonsági mentési előfeltételek

A Hyper-V virtuális gépek MABS történő biztonsági mentésének előfeltételei a következők:

|Előfeltétel|Részletek|
|------------|-------|
|A MABS előfeltételei|– Ha elemszintű helyreállítást szeretne végrehajtani a virtuális gépeken (fájlok, mappák, kötetek helyreállítása), akkor telepítenie kell a Hyper-V szerepkört a MABS-kiszolgálóra.  Ha csak a virtuális gépet kívánja helyreállítani, és nem elemszintű, akkor a szerepkör nem szükséges.<br />– Akár 800 virtuális gépet is véd 100 GB-onként egy MABS-kiszolgálón, és engedélyezheti a nagyobb fürtöket támogató több MABS-kiszolgálót.<br />– A MABS kizárja a lapozófájlt a növekményes biztonsági mentésből a virtuális gép biztonsági mentési teljesítményének növelése érdekében.<br />– A MABS képes biztonsági másolatot készíteni egy Hyper-V-kiszolgálóról vagy-fürtről, amely ugyanabban a tartományban van, mint a MABS-kiszolgáló, vagy egy gyermek vagy egy megbízható tartomány. Ha a Hyper-V biztonsági mentését munkacsoportban vagy nem megbízható tartományban szeretné végrehajtani, akkor be kell állítania a hitelesítést. Egyetlen Hyper-V kiszolgáló esetén NTLM-vagy tanúsítványalapú hitelesítést használhat. Fürt esetén csak tanúsítványalapú hitelesítést használhat.<br />– A gazdagép szintű biztonsági mentés használatával a virtuális gép adatai nem támogatottak az áteresztő lemezeken. Ebben az esetben azt javasoljuk, hogy a gazdagép szintű biztonsági mentéssel készítsen biztonsági másolatot a VHD-fájlokról, és a vendég szintű biztonsági mentéssel készítsen biztonsági másolatot a gazdagépen nem látható egyéb adatfájlokról.<br />   – Elvégezheti a deduplikált köteteken tárolt virtuális gépek biztonsági mentését.|
|Hyper-V virtuális gépek előfeltételei|– A virtuális gépen futó integrációs összetevők verziójának meg kell egyeznie a Hyper-V-gazdagép verziójával. <br />– Minden virtuális gép biztonsági mentése esetén szabad területnek kell lennie a virtuális merevlemez-fájlokat futtató köteten, hogy a biztonsági mentés során a Hyper-V elég helyet különbséglemezek-lemezek (AVHD) számára. A területnek legalább akkora kell lennie, mint a **kezdeti lemez méretének\*a forgalom\*a biztonsági mentés** időpontjában. Ha egy fürtön több biztonsági mentést futtat, elegendő tárolókapacitással kell rendelkeznie ahhoz, hogy az egyes virtuális gépek Különbséglemezeinek megfeleljenek a számításnak.<br />-A Windows Server 2012 R2 rendszerű Hyper-V-gazdagépeken található virtuális gépek biztonsági mentéséhez a virtuális gépnek rendelkeznie kell egy SCSI-vezérlővel, még akkor is, ha az nem csatlakozik semmihöz. (A Windows Server 2012 R2 online biztonsági mentésében a Hyper-V-gazdagép egy új virtuális merevlemezt csatlakoztat a virtuális géphez, majd később leválasztja azt. Ezt csak az SCSI-vezérlő tudja támogatni, ezért szükséges a virtuális gép online biztonsági mentéséhez.  A beállítás nélkül a 10103-es AZONOSÍTÓJÚ esemény lesz kiadva, amikor megkísérli a virtuális gép biztonsági mentését.)|
|A Linux előfeltételei|– A Linux rendszerű virtuális gépek biztonsági mentését a MABS 2012 R2 használatával végezheti el. Csak a fájl-konzisztens Pillanatképek támogatottak.|
|Virtuális gépek biztonsági mentése CSV-tárolóval|– A CSV-tároláshoz telepítse a Kötet árnyékmásolata szolgáltatás (VSS) hardver-szolgáltatót a Hyper-V-kiszolgálón. Vegye fel a kapcsolatot a Tárolóhálózati (SAN) gyártójával a VSS-hardver szolgáltatónál.<br />– Ha egy csomópont váratlanul leáll egy CSV-fürtben, a MABS konzisztencia-ellenőrzést hajt végre az adott csomóponton futó virtuális gépeken.<br />– Ha újra kell indítania egy olyan Hyper-V-kiszolgálót, amelynek BitLocker meghajtótitkosítás engedélyezve van a CSV-fürtön, konzisztencia-ellenőrzést kell futtatnia a Hyper-V virtuális gépeken.|
|Virtuális gépek biztonsági mentése SMB-tárolóval|– Kapcsolja be az automatikus csatlakoztatást a Hyper-V-t futtató kiszolgálón a virtuális gép védelmének engedélyezéséhez.<br />   – Tiltsa le a TCP-kémény kiszervezését.<br />– Győződjön meg arról, hogy az összes Hyper-V Machine $-fiók teljes körű engedélyekkel rendelkezik az adott távoli SMB-fájlmegosztás számára.<br />– Győződjön meg arról, hogy az összes virtuálisgép-összetevő fájljának elérési útja a másik helyre történő helyreállítás során kevesebb, mint 260 karakter. Ha nem, a helyreállítás sikeres lehet, de a Hyper-V nem fogja tudni csatlakoztatni a virtuális gépet.<br />– A következő forgatókönyvek nem támogatottak:<br />     Olyan központi telepítések, ahol a virtuális gép egyes összetevői helyi köteteken találhatók, és egyes összetevők távoli köteteken találhatók; egy IPv4-vagy IPv6-cím a tárolási hely fájlkiszolgáló számára, valamint egy virtuális gép helyreállítása olyan számítógépre, amely távoli SMB-megosztásokat használ.<br />– Engedélyeznie kell a Fájlkiszolgálói VSS-ügynök szolgáltatást minden SMB-kiszolgálón – adja hozzá a **szerepkörök és szolgáltatások hozzáadása** > **kiszolgálói szerepkörök kiválasztása** > **fájl-és tárolási szolgáltatások** > **Fájlszolgáltatások** > **file Service** > **fájlkiszolgáló VSS-ügynök szolgáltatás**.|

## <a name="back-up-virtual-machines"></a>Virtuális gépek biztonsági mentése

1. Állítsa be a [MABS-kiszolgálót](backup-azure-microsoft-azure-backup.md) és [a tárolót](backup-mabs-add-storage.md). A tárterület beállításakor használja a tárolási kapacitásra vonatkozó irányelveket.
   - Virtuálisgép-méret átlagos mérete – 100 GB
   - Virtuális gépek száma MABS-kiszolgálónként – 800
   - 800 virtuális gép teljes mérete – 80 TB
   - A biztonsági mentési tár szükséges területe – 80 TB

2. Állítsa be a MABS védelmi ügynököt a Hyper-V-kiszolgálón vagy a Hyper-V-fürt csomópontjain. Ha vendég szintű biztonsági mentést végez, az ügynököt azon virtuális gépekre kell telepítenie, amelyekről biztonsági másolatot szeretne készíteni a vendég szintjén.

3. A MABS felügyeleti konzolon kattintson a **védelem** > **védelmi csoport létrehozása** elemre az **új védelmi csoport létrehozása** varázsló megnyitásához.

4. A **csoporttagok kiválasztása** lapon válassza ki azokat a virtuális gépeket, amelyeket el szeretne látni a Hyper-V-gazdagépek azon kiszolgálóiról, amelyeken találhatóak. Javasoljuk, hogy minden olyan virtuális gépet, amely azonos védelmi szabályzattal fog rendelkezni, egyetlen védelmi csoportba helyezze. A tárterület hatékony kihasználásához engedélyezze az elhelyezést. Az együttes elhelyezés lehetővé teszi a különböző védelmi csoportokból származó adatok ugyanazon lemezen vagy szalagon történő tárolását, így több adatforrásnak egyetlen replika és egy helyreállítási pont kötete van.

5. Az **adatvédelmi módszer kiválasztása** lapon adja meg a védelmi csoport nevét. Válassza a **rövid távú védelmet szeretnék a lemez használatával** lehetőséget, és válassza az **online védelmet szeretnék** lehetőséget, ha az Azure Backup szolgáltatással szeretne biztonsági mentést készíteni az Azure-ba.

6. A **rövid távú célok megadása** > **megőrzési**időtartam mezőben határozza meg, hogy mennyi ideig kívánja megőrizni a lemezes adattárolási tartományt. A **szinkronizálás gyakorisága**mezőben adja meg, hogy milyen gyakran fusson a növekményes biztonsági másolatok. Másik lehetőségként a növekményes biztonsági másolatok intervallumának kiválasztása helyett **közvetlenül egy helyreállítási pont előtt**is engedélyezheti a beállítást. Ha ez a beállítás engedélyezve van, a MABS expressz teljes biztonsági mentést futtat az egyes ütemezett helyreállítási pontok előtt.

    > [!NOTE]
    >
    >Ha az alkalmazások munkaterheléseit védi, a helyreállítási pontok a szinkronizálás gyakorisága szerint jönnek létre, ha az alkalmazás támogatja a növekményes biztonsági mentéseket. Ha nem, akkor a MABS expressz teljes biztonsági mentést futtat növekményes biztonsági mentés helyett, és a helyreállítási pontokat az expressz biztonsági mentési ütemtervnek megfelelően hozza létre.

7. A **lemez lefoglalásának áttekintése** lapon tekintse át a védelmi csoport számára lefoglalt tárterület lemezterületét.

   A **teljes adatméret** a biztonsági mentéshez használni kívánt adatmennyiség, a **MABS** pedig a lemezterület, amelyet a MABS a védelmi csoport számára ajánl. A MABS a beállítások alapján kiválasztja az ideális biztonsági mentési kötetet. A **lemez-foglalás részleteiben**azonban szerkesztheti a kötetek biztonsági mentésének lehetőségeit. A munkaterhelések esetében válassza ki az előnyben részesített tárolót a legördülő menüben. A módosítások a **rendelkezésre álló Disk Storage** ablaktáblán a **teljes tárterület** és a **szabad tárterület** értékeit módosítják. A kiépített terület az a tárolási MABS, amely a kötethez való hozzáadást javasolja, hogy zökkenőmentesen folytassa a biztonsági mentéseket a jövőben.

8. A **replika-létrehozási módszer kiválasztása** lapon adja meg, hogy a rendszer hogyan végezze el a védelmi csoportba tartozó adatvédelem kezdeti replikálását. Ha úgy dönt, hogy **automatikusan replikálja a hálózaton keresztül**, javasoljuk, hogy válasszon ki egy off-Peak időpontot. Nagy mennyiségű vagy az optimális hálózati feltételeknél kisebb méretű adatforgalom esetén érdemes **manuálisan**kiválasztani a lehetőséget, amelyhez a cserélhető adathordozón keresztül az adatreplikálás szükséges.

9. A **konzisztencia-ellenőrzési beállítások** lapon válassza ki, hogyan szeretné automatizálni a konzisztencia-ellenőrzéseket. Engedélyezheti az ellenőrzések futtatását, ha a replika adatai inkonzisztensek, vagy egy ütemterv szerint. Ha nem szeretne automatikus konzisztencia-ellenőrzést beállítani, akkor bármikor lefuttathat egy manuális ellenőrzést, ha a jobb gombbal a védelmi csoportra kattint, és kiválasztja a **konzisztencia-ellenőrzés végrehajtása**lehetőséget.

    A védelmi csoport létrehozása után az adatműveletek kezdeti replikálása a kiválasztott módszernek megfelelően történik. A kezdeti replikálás után minden biztonsági mentés a védelmi csoport beállításaival összhangban történik. Ha helyre kell állítania az adatok biztonsági másolatát, vegye figyelembe a következőket:

## <a name="back-up-virtual-machines-configured-for-live-migration"></a>Az élő áttelepítéshez konfigurált virtuális gépek biztonsági mentése

Ha a virtuális gépek élő áttelepítésben vesznek részt, a MABS továbbra is védelmet biztosít a virtuális gépek számára, amíg a MABS védelmi ügynök telepítve van a Hyper-V-gazdagépen. Az élő áttelepítés típusától függ, hogy a MABS miként védi a virtuális gépeket.

**Élő áttelepítés fürtön belül** – ha egy virtuális gépet egy fürtön belül telepít át, a MABS észleli az áttelepítést, és a felhasználó beavatkozása nélkül biztonsági másolatot készít a virtuális gépről az új fürtcsomóponton. Mivel a tárolási hely nem változott, a MABS expressz teljes biztonsági mentéssel folytatódik.

**Élő áttelepítés a fürtön kívül** – ha egy virtuális gépet önálló kiszolgálók, különböző fürtök vagy egy önálló kiszolgáló és egy fürt között telepít át, a MABS észleli az áttelepítést, és felhasználói beavatkozás nélkül képes biztonsági másolatot készíteni a virtuális gépről.

### <a name="requirements-for-maintaining-protection"></a>A védelem fenntartásának követelményei

Az élő áttelepítés során az alábbi követelmények vonatkoznak a védelem fenntartására:

- A virtuális gépek Hyper-V-gazdagépeit egy System Center VMM-felhőben kell elhelyezni egy VMM-kiszolgálón, amely legalább a System Center 2012 SP1 verziót futtatja.

- A MABS védelmi ügynököt minden Hyper-V-gazdagépen telepíteni kell.

- A MABS-kiszolgálóknak csatlakozniuk kell a VMM-kiszolgálóhoz. A VMM-felhőben található összes Hyper-V-gazdagépnek is csatlakoznia kell a MABS-kiszolgálókhoz. Ez lehetővé teszi, hogy a MABS kommunikáljon a VMM-kiszolgálóval, így a MABS megtudhatja, hogy melyik Hyper-V-gazdagépen fut a virtuális gép, és hogyan hozhat létre új biztonsági másolatot a Hyper-V-kiszolgálóról. Ha nem létesíthető kapcsolat a Hyper-V-kiszolgálóval, a biztonsági mentés egy üzenettel meghiúsul, miszerint a MABS védelmi ügynök nem érhető el.

- Az összes MABS-kiszolgálónak, VMM-kiszolgálónak és Hyper-V-gazdagépnek ugyanabban a tartományban kell lennie.

### <a name="details-about-live-migration"></a>Az élő áttelepítés részletei

Az élő áttelepítés során a következő biztonsági mentést kell figyelembe venni:

- Ha az élő áttelepítés tárolót használ, a MABS teljes konzisztencia-ellenőrzést hajt végre a virtuális gépen, majd expressz teljes biztonsági mentéssel folytatja a műveletet. A tároló élő áttelepítése esetén a Hyper-V újrarendezi a virtuális merevlemezt (VHD) vagy a VHDX, ami egy egyszeri csúcsot okoz a MABS biztonsági mentési adatmennyiségének.

- A virtuális gép gazdagépén kapcsolja be az automatikus csatlakoztatást a virtuális védelem engedélyezéséhez, és tiltsa le a TCP-kémény kiszervezését.

- A MABS a 6070-as portot használja alapértelmezett portként a DPM-VMM segítő szolgáltatás üzemeltetéséhez. A beállításjegyzék módosítása:

    1. Navigáljon a **HKLM\Software\Microsoft\Microsoft adatvédelmi manager\configuration útvonalon**.
    2. Hozzon létre egy 32 bites DWORD-értéket: DpmVmmHelperServicePort, és írja be a frissített portszámot a beállításkulcs részeként.
    3. Nyissa meg ```<Install directory>\Azure Backup Server\DPM\DPM\VmmHelperService\VmmHelperServiceHost.exe.config```, és módosítsa a portszámot 6070-ről az új portra. Például:```<add baseAddress="net.tcp://localhost:6080/VmmHelperService/" />```
    4. Indítsa újra a DPM-VMM Helper szolgáltatást, és indítsa újra a DPM szolgáltatást.

### <a name="set-up-protection-for-live-migration"></a>Védelem beállítása élő áttelepítéshez

Az élő áttelepítés védelmének beállítása:

1. Állítsa be a MABS-kiszolgálót és annak tárolóját, és telepítse a MABS védelmi ügynököt minden Hyper-V-gazdagépre vagy fürtcsomópontra a VMM-felhőben. Ha egy fürtben SMB-tárolót használ, telepítse a MABS védelmi ügynököt a fürt összes csomópontjára.

2. Telepítse a VMM-konzolt a MABS-kiszolgálón lévő ügyfél-összetevőként, hogy a MABS képes legyen kommunikálni a VMM-kiszolgálóval. A konzolnak meg kell egyeznie a VMM-kiszolgálón futó verzióval.

3. Rendelje hozzá a MABSMachineName $ fiókot írásvédett rendszergazdai fiókként a VMM felügyeleti kiszolgálón.

4. A `Set-DPMGlobalProperty` PowerShell-parancsmaggal kapcsolja össze az összes Hyper-V gazdagép-kiszolgálót az összes MABS-kiszolgálóval. A parancsmag több MABS-kiszolgáló nevét is elfogadja. Használja a formátumot: `Set-DPMGlobalProperty -dpmservername <MABSservername> -knownvmmservers <vmmservername>`. További információ: [set-DPMGlobalProperty](https://technet.microsoft.com/library/hh881752.aspx).

5. Miután a VMM-felhőkben futó Hyper-V-gazdagépeken futó összes virtuális gép fel van derítve a VMM-ben, állítson be egy védelmi csoportot, és adja hozzá a védelemmel ellátni kívánt virtuális gépeket. Az automatikus konzisztencia-ellenőrzést a védelmi csoport szintjén kell engedélyezni a virtuális gépek mobilitási forgatókönyvei alatt.

6. A beállítások konfigurálása után a virtuális gép áttelepítése az egyik fürtről a másikra, az összes biztonsági mentés a várt módon folytatódik. Az élő áttelepítést a következő módon engedélyezheti a várt módon:

   1. Győződjön meg arról, hogy a DPM-VMM Helper szolgáltatás fut. Ha nem, indítsa el.

   2. Nyissa meg Microsoft SQL Server Management Studio, és kapcsolódjon a MABS-adatbázist (DPMDB) futtató példányhoz. A DPMDB futtassa a következő lekérdezést: `SELECT TOP 1000 [PropertyName] ,[PropertyValue] FROM[DPMDB].[dbo].[tbl_DLS_GlobalSetting]`.

      Ez a lekérdezés egy `KnownVMMServer`nevű tulajdonságot tartalmaz. Ennek az értéknek meg kell egyeznie a `Set-DPMGlobalProperty` parancsmaggal megadott értékkel.

   3. Futtassa a következő lekérdezést a *VMMIdentifier* paraméter egy adott virtuális gép `PhysicalPathXML` való ellenőrzéséhez. Cserélje le a `VMName`t a virtuális gép nevére.

      ```sql
      select cast(PhysicalPath as XML) from tbl_IM_ProtectedObject where DataSourceId in (select datasourceid from tbl_IM_DataSource   where DataSourceName like '%<VMName>%')
      ```

   4. Nyissa meg azt az. xml-fájlt, amelyet a lekérdezés visszaad, és ellenőrizze, hogy a *VMMIdentifier* mezőben van-e érték.

### <a name="run-manual-migration"></a>Manuális áttelepítés futtatása

Miután elvégezte az előző szakaszban leírt lépéseket, és az MABS Summary Manager-feladatok befejeződik, a Migrálás engedélyezve van. Alapértelmezés szerint ez a művelet éjfélkor kezdődik, és minden reggel fut. Ha manuális áttelepítést szeretne végezni annak vizsgálatához, hogy minden a várt módon működik-e, tegye a következőket:

1. Nyissa meg SQL Server Management Studiot, és kapcsolódjon a MABS-adatbázist futtató példányhoz.

2. Futtassa a következő lekérdezést: `select * from tbl_SCH_ScheduleDefinition where JobDefinitionID='9B30D213-B836-4B9E-97C2-DB03C3EB39D7'`. Ez a lekérdezés a **ScheduleID**adja vissza. Jegyezze fel ezt az azonosítót, mert a következő lépésben használni fogja.

3. A SQL Server Management Studio bontsa ki **SQL Server Agent**, majd a **feladatok**csomópontot. Kattintson a jobb gombbal a megjegyzett **ScheduleID** , és válassza **a feladatok indítása a következő lépésben**lehetőséget.

A biztonsági mentési teljesítmény a feladatok futtatásakor érintett. A telepítés mérete és skálázása határozza meg, hogy a rendszer mennyi időt vesz igénybe a feladatok befejezéséhez.

## <a name="back-up-replica-virtual-machines"></a>Replika virtuális gépek biztonsági mentése

Ha a MABS Windows Server 2012 R2 vagy újabb rendszeren fut, akkor biztonsági mentést készíthet a replika virtuális gépekről. Ez több okból is hasznos:

**Csökkenti a biztonsági mentéseknek a futó munkaterhelésekre gyakorolt hatását** – a virtuális gép biztonsági mentése némi terhelést jelent a pillanatkép létrehozásakor. A biztonsági mentési folyamat másodlagos távoli helyre történő kiszervezésével a biztonsági mentési művelet már nem érinti a futó munkaterhelést. Ez csak olyan központi telepítések esetén alkalmazható, ahol a biztonsági másolatot egy távoli helyen tárolják. Előfordulhat például, hogy napi biztonsági mentést készít, és az adatokat helyileg tárolja, hogy biztosítsa a gyors visszaállítási időt, de a replika virtuális gépekről havonta vagy negyedévente készítsen biztonsági másolatokat a hosszú távú megőrzés érdekében.

**Sávszélességet takarít** meg – egy tipikus távoli fiókirodában/központi telepítésben elegendő mennyiségű kiosztott sávszélesség szükséges a biztonsági mentési adatok helyek közötti átviteléhez. Ha létrehoz egy replikációs és feladatátvételi stratégiát az adatok biztonsági mentési stratégiáján kívül, csökkentheti a hálózaton keresztül továbbított redundáns adatok mennyiségét. Ha nem az elsődleges, hanem a replika virtuális gép adatokról készít biztonsági mentést, a biztonsági másolatban szereplő adatokat a hálózaton keresztül küldi el.

A **gazdagép biztonsági mentésének engedélyezése** – egy üzemeltetett adatközpontot is használhat replikaként, és nincs szükség másodlagos adatközpontra. Ebben az esetben a szolgáltatói SLA-nak konzisztens biztonsági másolatot kell készítenie a replika virtuális gépekről.

A replika virtuális gép ki van kapcsolva a feladatátvétel kezdeményezése előtt, és a VSS nem tudja garantálni a replika virtuális gép alkalmazás-konzisztens biztonsági mentését. Így a replika virtuális gépek biztonsági mentése csak összeomlás-konzisztens lesz. Ha az összeomlás-konzisztencia nem garantálható, a biztonsági mentés sikertelen lesz, és ez számos feltételben fordulhat elő:

- A replika virtuális gép nem kifogástalan állapotú, és kritikus állapotban van.

- A replika virtuális gép újraszinkronizálást végez (az újraszinkronizálás folyamatban vagy Újraszinkronizálás szükséges állapotában).

- Az elsődleges és a másodlagos hely közötti kezdeti replikálás folyamatban vagy függőben van a virtuális gépen.

- . HRL naplók vannak alkalmazva a replika virtuális gépre, vagy a. HRL naplók virtuális gépen történő alkalmazására irányuló korábbi művelet meghiúsult, vagy megszakították vagy megszakították.

- A replika virtuális gép áttelepítése vagy feladatátvétele folyamatban van

## <a name="recover-backed-up-virtual-machines"></a>Virtuális gépek biztonsági másolatának helyreállítása

Ha egy biztonsági másolattal rendelkező virtuális gépet állít helyre, a helyreállítási varázslóval kiválaszthatja a virtuális gépet és az adott helyreállítási pontot. A helyreállítási varázsló megnyitásához és a virtuális gép helyreállításához:

1. A MABS felügyeleti konzolon írja be a virtuális gép nevét, vagy bontsa ki a védett elemek listáját, és válassza ki a helyreállítani kívánt virtuális gépet.

2. A **helyreállítási pontok** ablaktáblán a naptárban kattintson bármelyik dátumra az elérhető helyreállítási pontok megtekintéséhez. Ezután az **elérési út** ablaktáblán válassza ki azt a helyreállítási pontot, amelyet használni szeretne a helyreállítási varázslóban.

3. A **műveletek** menüben kattintson a **helyreállítás** elemre a helyreállítási varázsló megnyitásához.

    A kiválasztott virtuális gép és helyreállítási pont megjelenik a **helyreállítási beállítások áttekintése** képernyőn. Kattintson a **Tovább**gombra.

4. A **helyreállítási típus kiválasztása** képernyőn válassza ki, hogy hová szeretné visszaállítani az adatkészletet, majd kattintson a **tovább**gombra.

    - **Visszaállítás az eredeti példányra**: Ha az eredeti példányt állítja helyre, a rendszer törli az eredeti VHD-t. A MABS a Hyper-V VSS-író használatával helyreállítja a VHD-t és az egyéb konfigurációs fájlokat az eredeti helyükre. A helyreállítási folyamat végén a virtuális gépek továbbra is elérhetők.
        Az erőforráscsoportot a helyreállításhoz kell megjelenni. Ha nem érhető el, állítsa helyre a helyreállítást egy másik helyre, és tegye elérhetővé a virtuális gépet.

    - **Helyreállítás virtuális gépnek bármely gazdagépre**: a MABS támogatja a másik helyre történő helyreállítást (ALR), amely a védett Hyper-v virtuális gépek zökkenőmentes helyreállítását teszi lehetővé egy másik Hyper-v-gazdagépen, a processzor-architektúrától függetlenül. A fürtcsomóponton helyreállított Hyper-V virtuális gépek nem lesznek magasan elérhetők. Ha ezt a lehetőséget választja, a helyreállítási varázsló további képernyőt jelenít meg a cél és a célhely elérési útjának azonosításához.

    - **Másolás hálózati mappába**: a MABS támogatja az elemszintű helyreállítást (ILR), amely lehetővé teszi a fájlok, mappák, kötetek és virtuális merevlemezek (VHD-k) elemszintű helyreállítását a Hyper-V virtuális gépek gazdagép szintű biztonsági másolatából egy hálózati megosztásra vagy egy MABS által védett kiszolgáló kötetére. Az elemszintű helyreállítás végrehajtásához a MABS védelmi ügynököt nem kell a vendégen belül telepíteni. Ha ezt a lehetőséget választja, a helyreállítási varázsló további képernyőt jelenít meg a cél és a célhely elérési útjának azonosításához.

5. A **helyreállítási beállítások megadása beállításnál adja meg** a helyreállítási beállításokat, majd kattintson a **tovább**gombra:

    - Ha alacsony sávszélességen keresztül helyreállít egy virtuális gépet, kattintson a **módosítás** gombra a **hálózati sávszélesség-használat szabályozásának**engedélyezéséhez. A szabályozási beállítás bekapcsolása után megadhatja az elérhetővé tenni kívánt sávszélesség mennyiségét és a sávszélesség rendelkezésre állásának időpontját.
    - Ha konfigurálta a hálózatot, válassza a **hardveres Pillanatképek használatával történő San-alapú helyreállítás engedélyezése** lehetőséget.
    - Válassza az **E-mail küldése a helyreállítás befejezésekor** lehetőséget, majd adja meg az e-mail-címeket, ha azt szeretné, hogy a helyreállítási folyamat befejezése után a rendszer elküldje az e-mail-értesítéseket.

6. Az összefoglalás képernyőn győződjön meg arról, hogy az összes adat helyes. Ha a részletek nem megfelelőek, vagy ha módosítani kívánja a módosításokat, kattintson a **vissza**gombra. Ha elégedett a beállításokkal **, kattintson a helyreállítás gombra** a helyreállítási folyamat elindításához.

7. A **helyreállítás állapota** képernyő a helyreállítási feladatról nyújt információkat.

## <a name="next-steps"></a>További lépések

[Adatok helyreállítása az Azure Backup Serverről](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server)
