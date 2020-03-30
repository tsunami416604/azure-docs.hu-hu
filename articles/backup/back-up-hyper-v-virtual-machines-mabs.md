---
title: Hyper-V virtuális gépek biztonsági és biztonsági másolatot kell fonni a MABS-szel
description: Ez a cikk a Microsoft Azure Backup Server (MABS) használatával a virtuális gépek biztonsági mentésére és helyreállítására vonatkozó eljárásokat tartalmazza.
ms.topic: conceptual
ms.date: 07/18/2019
ms.openlocfilehash: 00d1dd04522c51e4d68450a7b8f25d7159d63724
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255067"
---
# <a name="back-up-hyper-v-virtual-machines-with-azure-backup-server"></a>Hyper-V virtuális gépek biztonsági mentése az Azure Backup Server rel

Ez a cikk bemutatja, hogyan készíthet biztonsági másolatot a Hyper-V virtuális gépekről a Microsoft Azure Backup Server (MABS) használatával.

## <a name="supported-scenarios"></a>Támogatott esetek

A MABS a következő esetekben tud biztonsági másolatot olni a Hyper-V gazdakiszolgálókon futó virtuális gépekről:

- **Virtuális gépek helyi vagy közvetlen tárolással** – helyi vagy közvetlenül csatlakoztatott tárhellyel rendelkező önálló Hyper-V-gazdakiszolgálók által futtatott virtuális gépek biztonsági mentése. Például: merevlemez, tárolóhálózati (SAN) eszköz vagy hálózati csatlakoztatott tároló (NAS) eszköz. A MABS védelmi ügynököt minden állomásra telepíteni kell.

- **Fürtben található virtuális gépek CSV-tárolóval** – megosztott fürtkötetes (CSV) tárolót használó Hyper-V-fürt által futtatott virtuális gépek biztonsági mentése. A MABS védelmi ügynök minden fürtcsomópontra telepítve van.

## <a name="host-versus-guest-backup"></a>Host versus vendég biztonsági mentés

A MABS a Hyper-V virtuális gépek gazda- vagy vendégszintű biztonsági mentését is eltudja végezni. A gazdagép szinten a MABS védelmi ügynök telepítve van a Hyper-V gazdakiszolgálón vagy fürtben, és védi a teljes virtuális gépek et és adatfájlokat futó, hogy az állomáson.   A vendég szinten az ügynök minden virtuális gépen telepítve van, és védi a gépen lévő számítási feladatokat.

Mindkét módszernek vannak előnyei és hátrányai is:

- A gazdagépszintű biztonsági mentések rugalmasak, mert a vendéggépeken futó operációs rendszer típusától függetlenül működnek, és nem igénylik a MABS védelmi ügynök telepítését az egyes virtuális gépeken. Ha gazdagépszintű biztonsági mentést telepít, egy teljes virtuális gépet, illetve fájlokat és mappákat (elemszintű helyreállítást) képes helyreállítani.

- A vendégszintű biztonsági mentés akkor hasznos, ha egy virtuális gépen futó adott számítási feladatokat szeretne védeni. Állomásszintű egy teljes virtuális gép vagy adott fájlok helyreállítása, de nem nyújt helyreállítást egy adott alkalmazás környezetében. Ha például egy biztonsági másolatban szereplő virtuális gépből szeretne visszaállítani bizonyos SharePoint-elemeket, a virtuális gép vendégszintű biztonsági mentését kell végeznie. Használja a vendégszintű biztonsági mentést, ha meg szeretné védeni az áthaladási lemezeken tárolt adatokat. Az áthaladás lehetővé teszi, hogy a virtuális gép közvetlenül hozzáférjen a tárolóeszközhöz, és ne tárolja a virtuális kötet adatait egy VHD-fájlban.

## <a name="how-the-backup-process-works"></a>A biztonsági mentési folyamat működése

A MABS biztonsági mentést végez a VSS-szel az alábbiak szerint. A leírás lépéseit az átláthatóság érdekében számozással láttuk el.

1. A MABS blokkalapú szinkronizálási motor a védett virtuális gép kezdeti másolatát készíti el, és biztosítja, hogy a virtuális gép másolata teljes és konzisztens legyen.

2. A kezdeti másolat készítése és ellenőrzése után a MABS a Hyper-V VSS írót használja a biztonsági mentések rögzítésére. A VSS-író a MABS-kiszolgálóval szinkronizált lemezblokkok konzisztens készletét biztosítja. Ez a megközelítés biztosítja a "teljes biztonsági mentés" előnyeit a MABS-kiszolgálóval, miközben minimalizálja a hálózaton átvinni kívánt biztonsági mentési adatokat.

3. A Hyper-V rendszert futtató kiszolgálón a MABS védelmi ügynök a meglévő Hyper-V API-k segítségével határozza meg, hogy egy védett virtuális gép is támogatja-e a VSS-t.

   - Ha egy virtuális gép megfelel az online biztonsági mentés követelményeinek, és telepítve van rajta a Hyper-V integrációs szolgáltatások összetevő, akkor a Hyper-V VSS rekurzív módon továbbítja a VSS-kérést a virtuális gép minden VSS-t támogató folyamatához. Ez a művelet a MABS védelmi ügynök virtuális gépen való telepítése nélkül történik. A Hyper-V VSS-író a rekurzív VSS-kérések révén biztosítja a lemezírási műveletek szinkronizációját, így adatvesztés nélkül rögzíthető a VSS-pillanatkép.

     A Hyper-V integrációs szolgáltatások összetevő meghívja a Hyper-V VSS-írót a virtuális gépekre a Kötet árnyékmásolata szolgáltatásban annak érdekében, hogy a virtuális gépek alkalmazási adatai konzisztensek legyenek.

   - Ha a virtuális gép nem felel meg az online biztonsági mentési követelményeknek, az MABS automatikusan a Hyper-V API-kat használja a virtuális gép szüneteltetésére, mielőtt adatfájlokat rögzítene.

4. Miután a virtuális gép kezdeti alapkonfigurációs másolata szinkronizálja a MABS-kiszolgálót, a virtuális gép erőforrásain végrehajtott összes módosítás egy új helyreállítási ponton lesz rögzítve. A helyreállítási pont a virtuális gép adott időpontbeli konzisztens változatát képviseli. Naponta legalább egyszer sor kerülhet egy helyreállítási pont rögzítésére. Új helyreállítási pont létrehozásakor a MABS blokkszintű replikációt használ a Hyper-V VSS írójával együtt annak meghatározására, hogy mely blokkok módosultak a Hyper-V-t futtató kiszolgálón az utolsó helyreállítási pont létrehozása után. Ezek az adatblokkok ezután átkerülnek a MABS-kiszolgálóra, és a védett adatok replikájára lesznek alkalmazva.

5. A MABS-kiszolgáló vss-t használ a helyreállítási adatokat tartalmazó köteteken, így több árnyékmásolat is elérhető. Minden egyes árnyékmásolat külön helyreállítást biztosít. A VSS helyreállítási pontok a MABS-kiszolgálón tárolódnak. A Hyper-V-t futtató kiszolgálón készült ideiglenes másolat csak az MABS-szinkronizálás időtartama alatt kerül tárolásra.

>[!NOTE]
>
>A Windows Server 2016-tól kezdődően a Hyper-V virtuális merevlemezek beépített változáskövetést is beépített változáskövetésnek neveznek, amelyet rugalmas változáskövetésnek (RCT) neveznek. A MABS az RCT-t (a Hyper-V natív változáskövetését) használja, ami csökkenti az időigényes konzisztencia-ellenőrzések szükségességét olyan esetekben, mint például a virtuális gépek összeomlása. Az RCT a VSS pillanatkép-alapú biztonsági mentések által biztosított változáskövetésnél nagyobb rugalmasságot biztosít. A MABS V3 tovább optimalizálja a hálózati és tárolási felhasználást azáltal, hogy csak a módosított adatokat viszi át a konzisztencia-ellenőrzések során.

## <a name="backup-prerequisites"></a>A biztonsági mentésre vonatkozó előfeltételek

Ezek a Hyper-V virtuális gépek MABS-szel történő biztonsági mentésének előfeltételei:

|Előfeltétel|Részletek|
|------------|-------|
|MABS előfeltételek|- Ha elemszintű helyreállítást szeretne végrehajtani a virtuális gépekhez (fájlok, mappák, kötetek helyreállítása), akkor telepítenie kell a Hyper-V szerepkört a MABS kiszolgálóra.  Ha csak azt szeretné, hogy visszaszerezze a virtuális gép, és nem elemszintű, majd a szerepkör nem szükséges.<br />- Egy MABS-kiszolgálón akár 800, egyenként 100 GB-os virtuális gépet is megvédhet, és több Nagyobb fürtöt támogató MABS-kiszolgálót is engedélyezhet.<br />- A MABS kizárja a lapozófájlt a növekményes biztonsági mentésekből a virtuális gépek biztonsági mentési teljesítményének javítása érdekében.<br />- A MABS biztonsági másolatot tud fésülni egy Hyper-V kiszolgálóról vagy fürtről a MABS-kiszolgálóval azonos tartományban, illetve gyermek- vagy megbízható tartományban. Ha munkacsoportban vagy nem megbízható tartományban szeretne biztonsági másolatot adni a Hyper-V-ről, be kell állítania a hitelesítést. Egyetlen Hyper-V kiszolgáló esetén ntlm-et vagy tanúsítványhitelesítést használhat. Fürt esetén csak tanúsítványhitelesítést használhat.<br />–   A gazdaszintű biztonsági mentésnek a virtuális gép adatainak csatlakoztatott lemezre történő biztonsági mentéséhez való használata nem támogatott. Ebben az esetben azt javasoljuk, hogy a virtuális merevlemez-fájlok biztonsági mentéséhez és a vendégszintű biztonsági mentéshez állomásszintű biztonsági mentést használjon a gazdagépen nem látható egyéb adatok biztonsági mentéséhez.<br />   -A deduplikált köteteken tárolt virtuális gépekről biztonsági másolatot készíthet.|
|A Hyper-V virtuális gépek előfeltételei|- A virtuális gépen futó integrációs összetevők verziójának meg kell egyeznie a Hyper-V gazdagép verziójával. <br />–   Az egyes virtuális gépek biztonsági mentéséhez szabad területre lesz szükség azon a köteten, amelyen a virtuálismerevlemez-fájlok találhatók, hogy a Hyper-V a biztonsági mentés során elég hellyel rendelkezzen a különbséglemezek (AVHD) számára. A területnek legalább akkorának kell lennie, mint a **kezdeti lemezméret\*forgalom sebessége\*biztonsági mentési időablak** számítás eredménye. Ha egy fürtön több biztonsági mentést futtat, akkor az egyes virtuális gépek különbséglemezeinek elegendő tárolókapacitással kell rendelkezniük, hogy a fenti számítás szerint elférjenek rajtuk az egyes virtuális gépek.<br />- A Windows Server 2012 R2 rendszert futtató Hyper-V gazdakiszolgálókon található virtuális gépek biztonsági másolatához a virtuális gépnek meg kell adni egy SCSI-vezérlőt, még akkor is, ha az nem csatlakozik semmihez. (A Windows Server 2012 R2 online biztonsági mentés, a Hyper-V állomás csatlakoztatja az új virtuális merevlemez a virtuális gép, majd később leszereli azt. Ezt csak az SCSI-vezérlő támogatja, ezért a virtuális gép online biztonsági mentéséhez szükséges.  E beállítás nélkül az 10103-as azonosítójú esemény akkor kerül kiadásra, amikor megpróbál biztonsági másolatot tartani a virtuális gépről.)|
|A Linux előfeltételei|- A Linux virtuális gépekről a MABS használatával biztonsági másolatot lehet fésülni. Csak a fájlkonzisztens pillanatképek támogatottak.|
|Virtuális gépek biztonsági mentése CSV-tárolóval|–   A megosztott fürtkötetes tároláshoz telepítse a Kötet árnyékmásolata szolgáltatás (VSS) hardverszolgáltatót a Hyper-V-kiszolgálóra. A VSS hardverszolgáltatóról a tárolóhálózat (SAN) gyártójánál érdeklődhet.<br />- Ha egy CSV-fürtben egy csomópont váratlanul leáll, a MABS konzisztencia-ellenőrzést hajt végre az adott csomóponton futó virtuális gépeken.<br />–   Ha újra kell indítani a kérdéses fürt egyik olyan Hyper-V-kiszolgálóját, amelyen engedélyezve van a BitLocker meghajtótitkosítás, konzisztencia-ellenőrzést kell futtatni a Hyper-V rendszerű virtuális gépeken.|
|Virtuális gépek biztonsági mentése SMB-tárolóval|–   Kapcsolja be az automatikus csatlakoztatást a Hyper-V-t futtató kiszolgálón a virtuális gép védelmének engedélyezéséhez.<br />   –   Tiltsa le a TCP Chimney tehermentesítést.<br />–   Ügyeljen arra, hogy minden Hyper-V machine$-fiók teljes körű engedélyekkel rendelkezzen a megfelelő távoli SMB-fájlmegosztásokon.<br />- Győződjön meg arról, hogy a fájl elérési útja az összes virtuálisgép-összetevő nek a helyreállítás során az alternatív helyre kevesebb, mint 260 karakter. Ha nem, a helyreállítás sikeres lehet, de a Hyper-V nem tudja csatlakoztatni a virtuális gépet.<br />- A következő forgatókönyvek nem támogatottak:<br />     Olyan központi telepítések, ahol a virtuális gép egyes összetevői helyi köteteken, egyes összetevők pedig távoli köteteken találhatók; IPv4- vagy IPv6-cím a tárolóhely-fájlkiszolgálóhoz, valamint a virtuális gép helyreállítása távoli SMB-megosztásokat használó számítógépre.<br />- Minden SMB-kiszolgálón engedélyeznie kell a File Server VSS Agent szolgáltatást - Adja hozzá **a Szerepkörök és szolgáltatások** > hozzáadása**szolgáltatásban Válassza ki a kiszolgálói szerepköröket** > **Fájl- és tárolásiszolgáltatások** > **fájlszolgáltatásfájl-kiszolgáló** > **File Services** > **vss ügynökszolgáltatásában.**|

## <a name="back-up-virtual-machines"></a>Virtuális gépek biztonsági mentése

1. Állítsa be a [MABS-kiszolgálót](backup-azure-microsoft-azure-backup.md) és [a tárhelyet.](backup-mabs-add-storage.md) A tárhely beállításakor alkalmazza a tárolási kapacitásra vonatkozó irányelveket.
   - Virtuális gép átlagos mérete – 100 GB
   - Virtuális gépek száma MABS-kiszolgálónként - 800
   - A 800 virtuális gép teljes mérete – 80 TB
   - Biztonságimásolat-tároláshoz szükséges terület – 80 TB

2. Állítsa be a MABS védelmi ügynököt a Hyper-V kiszolgálón vagy a Hyper-V fürtcsomópontokon. Ha vendégszintű biztonsági mentést végez, telepíteni fogja az ügynököt a vendégszintű biztonsági mentéshez kívánt virtuális gépekre.

3. A MABS rendszergazda konzolon kattintson a **Védelmi** > **védelem létrehozása csoport** elemre az Új védelmi csoport **létrehozása** varázsló megnyitásához.

4. A **Csoporttagok kiválasztása** lapon válassza ki a védendő virtuális gépeket az ezeket magukban foglaló Hyper-V-gazdakiszolgálóról. Javasoljuk, hogy minden olyan virtuális gépet, amelyre azonos védelmi szabályzat vonatkozik, egy védelmi csoportba helyezzen. A tárterület hatékony felhasználása érdekében engedélyezze az együttes elhelyezést. Az együttes elhelyezés lehetővé teszi a különböző védelmi csoportokból származó adatok ugyanazon lemezen vagy szalagon történő tárolását, így több adatforrásnak egyetlen replika- és helyreállításipont-kötete keletkezik.

5. Az **Adatvédelmi módszer kiválasztása** oldalon adja meg a védelmi csoport nevét. Válassza ki a **Rövid távú védelmet szeretnék a következő használatával: Lemez** elemet, és válassza az **Online védelmet szeretnék** beállítást, ha az Azure Backup szolgáltatás segítségével szeretné az Azure-ba menteni az adatok biztonsági másolatát.

6. A Rövid távú célok > **megőrzési tartományának** **megadása**csoportban adja meg, hogy mennyi ideig szeretné megőrizni a lemezadatokat. A **Szinkronizálás gyakorisága**területen adja meg, hogy milyen gyakran kell az adatok növekményes biztonsági mentése. A növekményes biztonsági mentések időközeinek megadása helyett azt is megteheti, hogy engedélyezi a **Csak helyreállítási pont létrehozása előtt** beállítást. Ha ez a beállítás engedélyezve van, a MABS egy gyors teljes biztonsági mentést fog futtatni közvetlenül az egyes ütemezett helyreállítási pont előtt.

    > [!NOTE]
    >
    >Ha alkalmazások munkaterhelését védi, akkor a helyreállítási pontok a Szinkronizálás gyakorisága beállítás szerint jönnek létre, feltéve, hogy az alkalmazás támogatja a növekményes biztonsági mentéseket. Ha nem, akkor a MABS egy gyors teljes biztonsági mentést futtat a növekményes biztonsági mentés helyett, és helyreállítási pontokat hoz létre az expressz biztonsági mentésütemezésnek megfelelően.

7. A **Lemezfoglalás áttekintése** lapon tekintse át a védelmi csoport számára lefoglalt tárterület-területet.

   **A teljes adatméret** a biztonsági másolatot készíteni kívánt adatok mérete, és a **MABS-en kiépítendő lemezterület** az a terület, amelyet a MABS a védelmi csoport számára javasol. A MABS a beállítások alapján választja ki az ideális biztonsági mentési kötetet. Lehetőség van azonban arra, hogy a **Disk allocation details** (Lemezfoglalás részletei) panelen módosítsa a biztonsági mentési kötetek választási lehetőségeit. A munkaterhelésekhez válassza ki a legördülő menüből az előnyben részesített tárhelyet. A módosítások megváltoztatják az **Összes tárhely** és a **Szabad tárterület** értékeit az **Elérhető lemezterület** ablaktáblán. Alulkiépített terület az a tárterület, amelyet a MABS javasol hozzáadni a kötethez, hogy a jövőben zökkenőmentesen folytathassa a biztonsági mentéseket.

8. A **Replika-létrehozási módszer kiválasztása** oldalon adhatja meg, hogy a rendszer hogyan végezze el a védelmi csoport adatainak kezdeti replikálását. Ha az **Automatikus replikálás lehetőséget választja a hálózaton keresztüli automatikus replikáláshoz,** javasoljuk, hogy csúcsidőn kívül válasszon. Nagy mennyiségű adat vagy az optimálisnál kisebb hálózati feltételek esetén érdemes lehet a **Manuális**lehetőséget, amely az adatokat cserélhető adathordozóval offline módban replikálja.

9. A **Konzisztencia-ellenőrzési beállítások** oldalon válassza ki, hogyan szeretné automatizálni a konzisztencia-ellenőrzéseket. Beállíthatja hogy a rendszer ütemezés szerint futtasson ellenőrzést, vagy csak akkor, amikor a replikaadatok inkonzisztenssé válnak. Ha nem szeretne automatikus konzisztencia-ellenőrzést beállítani, bármikor lefuttathat egy manuális ellenőrzést, ha a jobb gombbal a védelmi csoportra kattint, és a **Konzisztencia-ellenőrzés végrehajtása** elemet választja.

    A védelmi csoport létrehozását követően megtörténik az adatok kezdeti replikálása a kiválasztott módszernek megfelelően. A kezdeti replikálás után a védelmi csoport beállításai szerint lezajlanak az egyes biztonsági mentések. Ha biztonsági másolatot kell szereznie, vegye figyelembe a következőket:

## <a name="back-up-virtual-machines-configured-for-live-migration"></a>Az élő áttelepítéshez konfigurált virtuális gépek biztonsági mentése

Ha a virtuális gépek aktív áttelepítésben vesznek részt, a MABS továbbra is védi a virtuális gépeket, amíg a MABS védelmi ügynök telepítve van a Hyper-V gazdagépen. A virtuális gépek MABS-védelmének módja az élő áttelepítés típusától függ.

**A fürtön belüli élő áttelepítés** – Ha egy virtuális gépet egy fürtön belül telepítát, az MABS észleli az áttelepítést, és biztonsági másolatot tesz a virtuális gépről az új fürtcsomópontról a felhasználói beavatkozás követelménye nélkül. Mivel a tárolási hely nem változott, a MABS expressz teljes biztonsági másolatokkal folytatódik.

**A fürtön kívüli élő áttelepítés** – Ha egy virtuális gépet önálló kiszolgálók, különböző fürtök vagy önálló kiszolgáló és fürt között telepít át, az MABS észleli az áttelepítést, és felhasználói beavatkozás nélkül biztonsági másolatot tud fésülni a virtuális gépről.

### <a name="requirements-for-maintaining-protection"></a>A védelem fenntartásának követelményei

Az élő áttelepítés közben a védelem fenntartásához az alábbi követelmények teljesülése szükséges:

- A virtuális gépek Hyper-V-állomásainak egy System Center VMM-felhőben, egy VMM-kiszolgálón kell elhelyezkedniük, és legalább a System Center 2012 SP1-et kell futtatniuk.

- A MABS védelmi ügynököt minden Hyper-V állomásra telepíteni kell.

- A MABS-kiszolgálóknak csatlakozniuk kell a VMM-kiszolgálóhoz. A VMM-felhőösszes Hyper-V gazdakiszolgálójának is csatlakoznia kell a MABS-kiszolgálókhoz. Ez lehetővé teszi a MABS számára, hogy kommunikáljon a VMM-kiszolgálóval, így a MABS megtudhatja, hogy a virtuális gép jelenleg melyik Hyper-V gazdakiszolgálón fut, és új biztonsági másolatot hozhat létre a Rról a Hyper-V kiszolgálóról. Ha a kapcsolat nem jön létre a Hyper-V kiszolgálóval, a biztonsági mentés sikertelen lesz egy üzenettel, hogy a MABS védelmi ügynök nem érhető el.

- Minden MABS-kiszolgálónak, VMM-kiszolgálónak és Hyper-V gazdakiszolgálónak ugyanabban a tartományban kell lennie.

### <a name="details-about-live-migration"></a>Az élő áttelepítés részletei

Az élő áttelepítés közbeni biztonsági mentés során vegye figyelembe a következőket:

- Ha egy élő áttelepítés izolátus átviszi a tárolót, a MABS teljes konzisztencia-ellenőrzést végez a virtuális gépen, majd folytatja az expressz teljes biztonsági mentéseket. A tárolás élő áttelepítésekor a Hyper-V átrendezi a virtuális merevlemezt (VHD) vagy a VHDX-et, ami egyszeri csúcsot eredményez a MABS biztonsági mentési adatainak méretében.

- A virtuális védelem engedélyezéséhez kapcsolja be az automatikus csatlakoztatást a virtuális gép gazdagépén, és tiltsa le a TCP Chimney tehermentesítést.

- A MABS a 6070-es portot használja alapértelmezett portként a DPM-VMM segítő szolgáltatás üzemeltetéséhez. A beállításjegyzék módosítása:

    1. Keresse meg a **HKLM\Software\Microsoft\Microsoft Data Protection Manager\Configuration mappát.**
    2. Hozza létre a következő 32 bites duplaszó értéket: DpmVmmHelperServicePort, és írja be az új portszámot a beállításkulcs részeként.
    3. Nyissa meg a következőt: ```<Install directory>\Azure Backup Server\DPM\DPM\VmmHelperService\VmmHelperServiceHost.exe.config```, és módosítsa a portszámot 6070-ről az új portszámra. Például:```<add baseAddress="net.tcp://localhost:6080/VmmHelperService/" />```
    4. Indítsa újra a DPM VMM segítő szolgáltatást és a DPM szolgáltatást.

### <a name="set-up-protection-for-live-migration"></a>Védelem beállítása élő áttelepítéshez

Védelem beállítása élő áttelepítéshez:

1. Állítsa be a MABS-kiszolgálót és annak tárolóját, és telepítse a MABS-védelmi ügynököt a VMM-felhő minden Hyper-V gazdakiszolgálójára vagy fürtcsomópontjára. Ha SMB-tárolót használ egy fürtben, telepítse a MABS-védelmi ügynököt az összes fürtcsomópontra.

2. Telepítse a VMM-konzolt ügyfélösszetevőként a MABS-kiszolgálóra, hogy a MABS kommunikálni tudjon a VMM-kiszolgálóval. A konzolnak a VMM-kiszolgálón futó verzióval azonos verziójúnak kell lennie.

3. Rendelje hozzá a MABSMachineName$ fiókot írásvédett rendszergazdai fiókként a VMM felügyeleti kiszolgálón.

4. Csatlakoztassa az összes Hyper-V gazdakiszolgálót `Set-DPMGlobalProperty` az összes MABS-kiszolgálóhoz a PowerShell-parancsmaggal. A parancsmag több MABS-kiszolgálónevet is elfogad. Használja a következő formátumot: `Set-DPMGlobalProperty -dpmservername <MABSservername> -knownvmmservers <vmmservername>`. További információ: [Set-DPMGlobalProperty](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmglobalproperty?view=systemcenter-ps-2019).

5. Miután a VMM-felhő Hyper-V-gazdagépein futó összes virtuális gép felderítése megtörtént a VMM-ben, állítson be egy védelmi csoportot, és abba vegye fel a védeni kívánt virtuális gépeket. Automatikus konzisztencia-ellenőrzések engedélyezni kell a védelmi csoport szintjén a virtuális gép mobilitási forgatókönyvek védelme.

6. A beállítások konfigurálása után, amikor egy virtuális gép áttelepíti az egyik fürtről a másikra, az összes biztonsági mentés a várt módon folytatódik. A következő módon ellenőrizheti, hogy az élő áttelepítés a várt módon van-e engedélyezve:

   1. Ellenőrizze, hogy fut-e a DPM VMM segítő szolgáltatás. Ha nem, indítsa el.

   2. Nyissa meg a Microsoft SQL Server Management Studio alkalmazást, és csatlakozzon a MABS-adatbázist (DPMDB) tároló példányhoz. Futtassa a DPMDB-n a következő lekérdezést: `SELECT TOP 1000 [PropertyName] ,[PropertyValue] FROM[DPMDB].[dbo].[tbl_DLS_GlobalSetting]`.

      Ez a lekérdezés `KnownVMMServer`egy . A tulajdonság értékének meg kell egyeznie a `Set-DPMGlobalProperty` parancsmaggal megadott értékkel.

   3. Futtassa a következő lekérdezést a *VMMIdentifier* paraméter egy adott virtuális gép `PhysicalPathXML` elemében történő ellenőrzéséhez. A `VMName` helyére a virtuális gép nevét írja.

      ```sql
      select cast(PhysicalPath as XML) from tbl_IM_ProtectedObject where DataSourceId in (select datasourceid from tbl_IM_DataSource   where DataSourceName like '%<VMName>%')
      ```

   4. Nyissa meg azt az .xml-fájlt, amelyet a lekérdezés visszaad, és ellenőrizze, hogy a *VMMIdentifier* mezőhöz tartozik-e érték.

### <a name="run-manual-migration"></a>Manuális áttelepítés futtatása

Miután elvégezte az előző szakaszokban leírt lépéseket, és befejeződött a MABS Summary Manager feladat, az áttelepítés engedélyezve van. Alapértelmezés szerint ez a feladat éjfélkor kezdődik, és reggelente fut. Ha manuális áttelepítést szeretne végezni annak ellenőrzéséhez, hogy minden a várt módon működik-e, tegye a következőket:

1. Nyissa meg az SQL Server Management Studio alkalmazást, és csatlakozzon a MABS-adatbázist tároló példányhoz.

2. Futtassa a következő lekérdezést: `SELECT SCH.ScheduleId FROM tbl_JM_JobDefinition JD JOIN tbl_SCH_ScheduleDefinition SCH ON JD.JobDefinitionId = SCH.JobDefinitionId WHERE JD.Type = '282faac6-e3cb-4015-8c6d-4276fcca11d4' AND JD.IsDeleted = 0 AND SCH.IsDeleted = 0`. A lekérdezés visszaadja a **ScheduleID** értékét. Jegyezze fel ezt az azonosítót, mert a következő lépésben szükség lesz rá.

3. Az SQL Server Management Studióban bontsa ki az **SQL Server Agent**, majd a **Jobs** (Feladatok) csomópontot. Jobb gombbal kattintson a feljegyzett **ScheduleID**-re, és válassza a **Start Job at Step** (Feladat indítása adott lépésnél) parancsot.

A feladat futtatásakor a biztonsági mentés teljesítménye hatással van. A feladat végrehajtásához szükséges idő a rendszer méretétől függ.

## <a name="back-up-replica-virtual-machines"></a>Replika virtuális gépek biztonsági mentése

Ha a MABS windows Server 2012 R2 vagy nagyobb rendszeren fut, akkor biztonsági másolatot lehet tenni a replika virtuális gépeiről. Ez több szempontból is hasznos:

**Csökkenti a biztonsági mentéseknek a futó munkaterhelésekre gyakorolt hatását** – A virtuális gép biztonsági mentése némi többletterhelést okoz, mivel egy pillanatkép jön létre. A biztonsági mentési folyamat másodlagos távoli helyre való kiszervezésével a futó munkaterhelést már nem érinti a biztonsági mentési művelet. Ez csak olyan telepítések esetén alkalmazható, ahol a biztonsági másolat tárolása egy távoli helyen történik. Például biztonsági másolatokat készíthet naponta, és a gyors helyreállítás érdekében helyileg tárolhatja az adatokat, de a replika virtuális gépekről havonta vagy negyedévente is készíthet biztonsági másolatokat, amelyeket a hosszú távú megőrzés érdekében egy távoli helyen tárolhat.

**Kíméli a sávszélességet** – Egy tipikus fiókirodai/központi irodai telepítés esetén megfelelő nagyságrendű kiosztott sávszélességre van szükség a biztonsági másolatok telephelyek közötti átviteléhez. Ha az adatok biztonsági mentési stratégiája mellett kialakít egy replikációs és feladatátvételi stratégiát, csökkentheti a hálózaton keresztül továbbított redundáns adatok mennyiségét. A replika virtuális gép adatainak biztonsági mentésével az elsődleges helyett mentse a biztonsági másolat adatainak a hálózaton keresztüli küldésének többletterhelését.

**Lehetővé teszi a szolgáltatói biztonsági mentést** – Replika telephelyként üzemeltetett adatközpontot is használhat, és nincs szükség másodlagos adatközpontra. Ebben az esetben a gazdagép SLA konzisztens biztonsági másolatot igényel a replika virtuális gépek.

A feladatátvétel indításáig a replika virtuális gép ki van kapcsolva, és a VSS nem garantálja a replika virtuális gép alkalmazáskonzisztens biztonsági mentését. Ezért a replika virtuális gép biztonsági mentése csak összeomlás-konzisztens lesz. Ha az összeomlás-konzisztens mentést nem lehet garantálni a biztonsági mentés sikertelen lesz, ami számos esetben előfordulhat:

- A replika virtuális gép működése nem kifogástalan, állapota kritikus.

- A replika virtuális gép újraszinkronizálást végez (az Újraszinkronizálás folyamatban vagy az Újraszinkronizálás szükséges állapotban van).

- A virtuális gépre vonatkozóan az elsődleges és másodlagos hely közötti kezdeti replikálás folyamatban vagy függőben van.

- A .hrl naplók a replika virtuális gépére vannak alkalmazva, vagy egy korábbi művelet a .hrl naplók virtuális lemezen történő alkalmazására sikertelen, vagy megszakadt vagy megszakadt.

- A replika virtuális gép áttelepítése vagy feladatátvétele folyamatban van.

## <a name="recover-backed-up-virtual-machines"></a>Biztonsági másolatba mentett virtuális gépek helyreállítása

Amikor biztonsági másolatba mentett virtuális gépeket állít helyre, a Helyreállítási varázsló segítségével választhatja ki a kívánt virtuális gépet és az adott helyreállítási pontot. A Helyreállítási varázsló megnyitásához és a virtuális gép helyreállításához tegye a következőt:

1. A MABS felügyeleti konzolon írja be a virtuális gép nevét, vagy bontsa ki a védett elemek listáját, és jelölje ki a helyreállítani kívánt virtuális gépet.

2. **A következő helyreállítási pontjai** ablaktáblán kattintson a naptárban bármelyik dátumra az elérhető helyreállítási pontok megjelenítéséhez. Az **Elérési út** ablaktáblán válassza ki a Helyreállítási varázslóban használni kívánt helyreállítási pontot.

3. A **Műveletek** panelen kattintson a **Helyreállítás** elemre a Helyreállítási varázsló megnyitásához.

    A kiválasztott virtuális gép és helyreállítási pont megjelenik a **Helyreállítási beállítások áttekintése** képernyőn. Kattintson a **Tovább** gombra.

4. A **Helyreállítási típus kiválasztása** képernyőn válassza ki, hová szeretné visszaállítani az adatokat, és kattintson a **Tovább** gombra.

    - **Visszaállítás az eredeti példányban**: Ha az eredeti példányba állít vissza, akkor az eredeti virtuális merevlemez törlődik. A MABS a Hyper-V VSS író segítségével helyreállítja a virtuális merevlemezt és más konfigurációs fájlokat az eredeti helyre. A helyreállítási folyamat végén a virtuális gépek továbbra is magas rendelkezésre állásúak lesznek.
        A helyreállításhoz szükség van az erőforráscsoportra. Ha az nem érhető el, akkor egy másik helyre végezze el a helyreállítást, és állítsa a virtuális gépet magas rendelkezésre állásúra.

    - **Helyreállítás virtuális gépként bármely állomásszámára**: A MABS támogatja az alternatív helyhelyreállítást (ALR), amely a védett Hyper-V virtuális gép zökkenőmentes helyreállítását biztosítja egy másik Hyper-V állomásra, a processzorarchitektúrától függetlenül. A fürtcsomópontra helyreállított Hyper-V virtuális gépek nem lesznek magas rendelkezésre állásúak. Ha ezt a beállítást választja, a Helyreállítási varázsló megjelenít egy további, a cél és a cél elérési útjának azonosítására szolgáló képernyőt.

    - **Másolás hálózati mappába**: A MABS támogatja az elemszintű helyreállítást (ILR), amely lehetővé teszi a fájlok, mappák, kötetek és virtuális merevlemezek (VHD-k) elemszintű helyreállítását a Hyper-V virtuális gépek gazdaszintű biztonsági mentéséről hálózati megosztásra vagy mabs-védelemmel ellátott kiszolgálón lévő kötetre. A MABS védelmi ügynök nem kell telepíteni a vendég en belül az elemszintű helyreállítás végrehajtásához. Ha ezt a beállítást választja, a Helyreállítási varázsló megjelenít egy további, a cél és a cél elérési útjának azonosítására szolgáló képernyőt.

5. A **Helyreállítási beállítások megadása** oldalon adja meg a helyreállítási beállításokat, majd kattintson a **Tovább** gombra:

    - Ha egy virtuális gépet alacsony sávszélesség mellett állít helyre, kattintson a **Módosítás** lehetőségre a **Sávszélesség használatának szabályozása** beállítás engedélyezéséhez. A sávszélesség-szabályozási beállítás bekapcsolása után megadhatja, hogy mekkora sávszélességet, illetve mikor kíván elérhetővé tenni.
    - Ha a hálózatot konfigurálta, válassza ki a **Hardveresen készített pillanatfelvételek segítségével történő SAN-alapú helyreállítás engedélyezése** beállítást.
    - Ha azt szeretné, hogy a rendszer e-mail-értesítést küldjön a helyreállítási folyamat befejezése után, jelölje be az **E-mail küldése a helyreállítás befejezéséről** lehetőséget, majd adja meg a kívánt e-mail-címeket.

6. Az Összefoglalás képernyőn ellenőrizze az adatok helyességét. Ha az adatok nem megfelelőek, vagy módosítani szeretné őket, kattintson a **Vissza** gombra. Ha elégedett a beállításokkal, kattintson a **Helyreállítás** gombra a helyreállítási folyamat megkezdéséhez.

7. A helyreállítási feladatra vonatkozó információk a **Helyreállítás állapota** képernyőn jelennek meg.

## <a name="next-steps"></a>További lépések

[Adatok helyreállítása az Azure Backup Serverről](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server)
