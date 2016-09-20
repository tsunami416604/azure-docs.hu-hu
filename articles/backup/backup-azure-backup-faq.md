<properties
   pageTitle="Azure Backup – gyakori kérdések | Microsoft Azure"
   description="Ez a cikk megválaszolja a biztonsági mentési szolgáltatásra és ügynökre, a biztonsági mentésre és azok megtartására, helyreállításra, biztonságára és egyéb, a biztonsági mentéssel és a vészhelyreállítással kapcsolatos általános gyakori kérdéseket."
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""
   keywords="biztonsági mentés és vészhelyreállítás; biztonsági mentési szolgáltatás"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.date="08/29/2016"
     ms.author="trinadhk; giridham; arunak; markgal; jimpark;"/>

# Azure Backup szolgáltatás – GYIK

> [AZURE.SELECTOR]
- [Backup GYIK klasszikus módhoz](backup-azure-backup-faq.md)
- [Backup GYIK Resource Manager módhoz](backup-azure-backup-ibiza-faq.md)

Ez a cikk az Azure Backup szolgáltatással kapcsolatos gyakori kérdések (és a hozzájuk tartozó válaszok) listája. Közösségünk gyorsan válaszol, és ha egy kérdést gyakran feltesznek, hozzáadjuk ehhez a cikkhez. A kérdésekre adott válaszok általában hivatkozási vagy támogatási információkat tartalmaznak. Az Azure Backup szolgáltatással kapcsolatban ezen vagy egy kapcsolódó cikk Disqus szakaszában tehet fel kérdéseket. Emellett egy fórumbejegyzésben is feltehet kérdéseket az Azure Backup szolgáltatással kapcsolatban a [vitafórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).


## Melyek azok az operációs rendszerek, amelyekből biztonsági mentést tudok készíteni az Azure-ba az Azure Backup használatával? <br/>
Az Azure Backup a következő operációs rendszereket támogatja a fájlok, mappák és alkalmazások Azure Backup Server és SCDPM segítségével végzett biztonsági mentéséhez. 

| Operációs rendszer        | Platform           | SKU  |
| :------------- |-------------| :-----|
| Windows 8 és a legújabb szervizcsomagok      | 64 bit | Enterprise, Pro |
| Windows 7 és a legújabb szervizcsomagok      | 64 bit | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| Windows 8.1 és a legújabb szervizcsomagok | 64 bit      |    Enterprise, Pro |
| Windows 10      | 64 bit | Enterprise, Pro, Home |
|Windows Server 2012 R2 és a legújabb szervizcsomagok| 64 bit| Standard, Datacenter, Foundation|
|Windows Server 2012 és a legújabb szervizcsomagok|    64 bit| Datacenter, Foundation, Standard|
|Windows Storage Server 2012 R2 és a legújabb szervizcsomagok  |64 bit|    Standard, Workgroup|
|Windows Storage Server 2012 és a legújabb szervizcsomagok |64 bit |Standard, Workgroup
|Windows Server 2012 R2 és a legújabb szervizcsomagok  |64 bit|    Essential|
|Windows Server 2008 R2 SP1 |64 bit|    Standard, Enterprise, Datacenter, Foundation|
|Windows Server 2008 SP2    |64 bit|    Standard, Enterprise, Datacenter, Foundation|

Azure VM Backup esetében:

- **Linux**: Az Azure Backup az [Azure által támogatott disztribúciók listáját](../virtual-machines/virtual-machines-linux-endorsed-distros.md) támogatja a Core OS Linux kivételével.  Egyéb saját Linux-disztribúciók is működtethetők, ha a virtuális gép ügynöke elérhető a virtuális gépen, és a rendszer támogatja a Pythont.
- **Windows Server**: A Windows Server 2008 R2-nél régebbi verziók nem támogatottak.

## Honnan tölthetem le a legújabb Azure Backup ügynököt? <br/>
[Innen](http://aka.ms/azurebackup_agent) töltheti le a legújabb ügynököt a Windows Server, System Center DPM vagy Windows-ügyfél biztonsági mentése számára. Ha egy virtuális gépről szeretne biztonsági mentést készíteni, használja a virtuálisgép-ügynököt (amely automatikusan telepíti a megfelelő bővítményt). Az Azure katalógusból létrehozott virtuális gépeken a virtuálisgép-ügynök már jelen van.

## Az SCDPM kiszolgáló melyik verziója támogatott? <br/>
Javasolt a [legújabb](http://aka.ms/azurebackup_agent) Azure Backup ügynök telepítése az SCDPM legújabb kumulatív frissítésén (2016 augusztusában ez az UR11).

## Az Azure Backup ügynök telepítése közben a rendszer a tároló hitelesítő adatainak megadását kéri. A tároló hitelesítő adatai lejárnak?
Igen, a tároló hitelesítő adatai 48 óra után lejárnak. Ha a fájl lejár, jelentkezzen be az Azure Portalra, és töltse le a tároló hitelesítő adatfájljait a tárolójából. 

## Az egyes Azure-előfizetésekben létrehozható tárolók száma korlátozott? <br/>
Igen. A 2016. augusztusi állapot szerint előfizetésenként 25 tárolót hozhat létre (a biztonsági mentési és a Recovery Services-tárolóhoz is). Ha több tárolóra van szüksége, hozzon létre egy új előfizetést.

## Az egyes tárolókhoz regisztrálható kiszolgálók/gépek száma korlátozott? <br/>
Igen, tárolónként 50 gépet regisztrálhat. Az Azure infrastruktúra-szolgáltatás virtuális gépei esetén a korlát 200 virtuális gép tárolónként. Ha több gépet szeretne regisztrálni, hozzon létre egy új tárolót.

## Hogyan regisztrálhatom a kiszolgálómat egy másik adatközpontra?<br/>
A biztonsági mentési adatokat a rendszer annak a tárolónak az adatközpontjába küldi, amelyikre regisztrálva van. Az adatközpont módosításának legegyszerűbb módja az ügynök eltávolítása, újratelepítése, majd a kívánt adatközponthoz tartozó új tárolóra regisztrálása.

## Mi történik, ha átnevezek egy Windows-kiszolgálót, amelyről biztonsági adatmentés történik az Azure rendszerbe?<br/>
Ha átnevez egy kiszolgálót, minden aktuálisan konfigurált biztonsági mentés leáll.
Regisztrálnia kell a kiszolgáló új nevét a Backup-tárolóban. Ha létrehozta az új regisztrációt, az első biztonsági mentési művelet egy teljes biztonsági mentés lesz, nem pedig növekményes biztonsági mentés. Ha helyre kell állítania a korábban, a régi kiszolgálónévvel a tárolóba mentett adatokat, ezt az **Adatok helyreállítása** varázsló [**Másik kiszolgáló**](backup-azure-restore-windows-server.md#recover-to-an-alternate-machine) beállításában teheti meg.

## Milyen típusú meghajtókon lévő fájlokról és mappákról tudok biztonsági másolatot készíteni? <br/>
A következő meghajtókról/kötetekről nem lehet biztonsági másolatot készíteni:

- Cserélhető adathordozó: A meghajtónak állandónak kell lennie ahhoz, hogy egy biztonsági mentés forráselemeként lehessen használni.
- Csak olvasható kötetek: A kötetnek a működéshez írhatónak kell lennie a kötet árnyékmásolat (VSS) számára.
- Offline kötetek: A kötetnek a működéshez online-nak kell lennie a VSS számára.
- Hálózati megosztás: A kötetnek helyinek kell lennie a kiszolgáló számára, hogy az online biztonsági mentés használatával biztonsági másolatot lehessen róla készíteni.
- BitLocker által védett kötetek: A kötetet fel kell oldani a biztonsági mentés megkezdése előtt.
- Fájlrendszer azonosítása: Az online biztonsági mentési szolgáltatás ezen verziója csak az NTFS fájlrendszert támogatja.

## A kiszolgálón lévő milyen fájlokról és mappákról készíthetek biztonsági másolatot?<br/>
A következő típusok támogatottak:

- Titkosított
- Tömörített
- Ritka
- Tömörített + ritka
- Rögzített hivatkozások: Nem támogatott, átugorva
- Újraelemzési pont: Nem támogatott, átugorva
- Titkosított + tömörített: Nem támogatott, átugorva
- Titkosított + ritka: Nem támogatott, átugorva
- Tömörített stream: Nem támogatott, átugorva
- Ritka stream: Nem támogatott, átugorva

## Mi a gyorsítótármappa minimális méretkövetelménye? <br/>
A gyorsítótármappa mérete határozza meg azt az adatmennyiséget, amelyről biztonsági másolatot készít. A gyorsítótár mappájának az adatok tárolásához szükséges terület 5%-ának kell lennie.

## Ha a szervezetem egy tárolóval rendelkezik, hogyan tudom az egyik kiszolgáló adatait elszigetelni egy másik kiszolgálóétól az adatok visszaállításakor?<br/>
Minden kiszolgáló, amely ugyanahhoz a tárolóhoz van regisztrálva, képes helyreállítani más, *ugyanazt a hozzáférési kódot használó* kiszolgálók adatainak biztonsági másolatait. Ha olyan kiszolgálókkal rendelkezik, amelyek adatainak biztonsági másolatait szeretné elszigetelni a szervezetében található más kiszolgálóétól, használjon egy erre kijelölt hozzáférési kódot ezekhez a kiszolgálókhoz. Például a humánerőforrás-kiszolgálók használhatnának egy titkosító hozzáférési kódot, a könyvelési kiszolgálók egy másikat és a tároló kiszolgálók egy harmadikat.

## Át lehet telepíteni a biztonsági mentési adatokat vagy tárolót az előfizetéseim között? <br/>
Nem. A tároló előfizetési szinten jön létre, és a létrehozása után nem lehet másik előfizetéshez hozzárendelni.

## Az Azure Backup ügynök működik egy Windows Server 2012 deduplikációt használó kiszolgálón? <br/>
Igen. Az ügynökszolgáltatás átalakítja a deduplikált adatokat normális adatokká, amikor előkészíti a biztonsági mentési műveletet. Ezután optimalizálja az adatokat a biztonsági mentéshez, titkosítja őket, majd elküldi a titkosított adagokat az online biztonsági mentési szolgáltatásba.

## Ha megszakítok egy már elindult biztonsági mentési feladatot, az átküldött adatok biztonsági másolata törlődik? <br/>
Nem. A biztonsági mentési tároló eltárolja azon adatok biztonsági másolatát, amelyek a megszakítás pillanatában már át lettek küldve. Az Azure Backup egy ellenőrzőpont-alapú mechanizmust használ, így a biztonsági mentés közben esetenként ellenőrzőpontokat ad az adatok biztonsági másolatához. Mivel ellenőrzőpontok vannak az adatok biztonsági másolatában, a következő biztonsági mentési folyamat ellenőrizni tudja a fájlok épségét. A következő aktivált biztonsági mentés növekményes mentés lesz a korábban mentett adatok biztonsági másolata alapján. A növekményes biztonsági mentés jobban kihasználja a sávszélességet, így nem kell ugyanazokat az adatokat ismételten elküldeni.

Azure virtuális gép biztonsági mentése esetén a feladat megszakítása után a rendszer figyelmen kívül hagyja az átvitt adatokat, és egy friss biztonsági mentés növekményes adatokat visz át egy korábban sikeresen végrehajtott biztonsági mentési feladatból. 

## Miért jelenik meg „Az Azure Backup nem lett konfigurálva ezen a kiszolgálón” figyelmeztetés annak ellenére, hogy korábban ütemeztem a rendszeres biztonsági mentést? <br/>
Ez a figyelmeztetés akkor jelenik meg, amikor a helyi kiszolgálón tárolt ütemezett biztonsági mentési beállítások nem egyeznek meg a biztonsági mentési tárolóban tárolt beállításokkal. Ha a kiszolgáló vagy a beállítások egy korábbi hibátlan állapotra lettek visszaállítva, a biztonsági mentés ütemezése elveszítheti a szinkronizálást. Ha ilyen figyelmeztetést kap, [konfigurálja újra a biztonsági mentési házirendet](backup-azure-manage-windows-server.md), majd kattintson a **Biztonsági mentés azonnali futtatása** parancsra a helyi kiszolgálónak az Azure szolgáltatással történő újraszinkronizálásához.

## Milyen tűzfalszabályokat kell konfigurálni az Azure Backup szolgáltatáshoz? <br/>
A helyi adatoknak és a számítási feladatoknak az Azure rendszerbe történő zökkenőmentes átviteléhez és védelméhez a tűzfalnak érdemes engedélyezi a következő URL-címekkel történő kommunikációt:

- www.msftncsi.com
- \*.Microsoft.com
- \*.WindowsAzure.com
- \*.microsoftonline.com
- \*.windows.net

##Telepíthetem az Azure Backup ügynököt egy olyan Azure virtuális gépre, amelyet az Azure Backup szolgáltatás már támogat a virtuális gép bővítmény használatával? <br/>
Abszolút. Az Azure Backup virtuálisgép-szintű biztonsági mentést nyújt az Azure virtuális gépeknek az erre szolgáló bővítmény használatával. Az Azure Backup ügynököt telepítheti egy vendég Windows operációs rendszerre, hogy megvédje a vendég operációs rendszeren található fájlokat és mappákat.

## Telepíthetem az Azure Backup ügynököt egy Azure virtuális gépre, hogy biztonsági mentést készítsek egy, az Azure virtuális gép által biztosított ideiglenes tárolón lévő fájlokról és mappákról? <br/>
Az Azure Backup ügynököt telepítheti a vendég Windows operációs rendszeren, és biztonsági mentést készíthet a fájlokról és mappákról egy ideiglenes tárolóra. Ugyanakkor vegye figyelembe, hogy a biztonsági mentések sikertelenek lesznek, ha az ideiglenes tároló adatait törli. Emellett ha az ideiglenes tároló adatait törölték, csak állandó tárolóba tud visszaállítani.

## Telepítettem az Azure Backup ügynököt a fájljaim és mappáim megvédéséhez. Telepíthetem az SCDPM szolgáltatást, hogy együttműködjön az Azure Backup ügynökkel a helyi alkalmazások vagy a virtuális gép számítási feladatainak Azure-ban történő megvédésében? <br/>
Az Azure Backup SCDPM szolgáltatással történő használatához javasolt először az SCDPM-et telepíteni, és csak aztán az Azure Backup ügynököt. Ez biztosítja az Azure Backup ügynök és az SCDPM problémamentes integrációját, és lehetővé teszi fájlok és mappák, alkalmazások számítási feladatai és virtuális gépek Azure-ban történő védelmét közvetlenül az SCDPM felügyeleti konzoljából. A fent említett okokból nem javasolt és nem támogatott az SCDPM telepítése az Azure Backup ügynök telepítése után.

## Mi a fájl elérési útjának hossza, amely meghatározható az Azure Backup ügynök használatával az Azure Backup házirendjének részeként? <br/>  
Az Azure Backup ügynök az NTFS-re hagyatkozik. A [fájl elérési útvonalának hossz-specifikációját a Windows API korlátozza](https://msdn.microsoft.com/library/aa365247.aspx#fully_qualified_vs._relative_paths). Ha a rendszer olyan fájlokról készít biztonsági másolatot, amelyeknek elérési útja hosszabb, mint a Windows API által meghatározott korlát, az ügyfelek dönthetnek a mentendő fájlok szülőmappájának vagy lemezmeghajtójának biztonsági mentése mellett.  

## Mely karakterek engedélyezettek az Azure Backup ügynököt használó Azure Backup házirend elérési útjában? <br>  
 Az Azure Backup ügynök az NTFS-re hagyatkozik. Ez engedélyezi az [NTFS által támogatott karakterek](https://msdn.microsoft.com/library/aa365247.aspx#naming_conventions) használatát a fájl meghatározásának részeként.  

## Használhatom az Azure Backup kiszolgálót egy operációs rendszer nélküli helyreállítás (BMR) biztonsági másolatának létrehozásához egy fizikai kiszolgálón? <br/>
Igen.

## Konfigurálhatom úgy a Backup szolgáltatást, hogy levelet küldjön, ha egy biztonsági mentési feladat sikertelen volt? <br/>
Igen, a Backup szolgáltatás számos eseményalapú értesítéssel rendelkezik, amelyek egy PowerShell-parancsprogrammal használhatók. A teljes leírást itt találja: [Figyelmeztető értesítések](backup-azure-manage-vms.md#alert-notifications)

## Létezik méretkorlátozás a biztonsági mentésre kijelölt adatforrásokra vonatkozóan? <br/>
Noha tárolószinten nincsen korlátozva, hogy mennyi adatról készíthet biztonsági másolatot, az Azure Backup korlátozza az adatforrások maximális méretét (gyakorlati okokból azonban ezek a korlátok nagyon magasak). A 2015. augusztusi állapot szerint a támogatott operációs rendszerek esetén az adatforrás maximális mérete a következő:

|Sorszám | Operációs rendszer |  Adatforrás maximális mérete |
| :-------------: |:-------------| :-----|
|1| Windows Server 2012 vagy újabb| 54 400 GB|
|2| Windows 8 vagy újabb| 54 400 GB|
|3| Windows Server 2008, Windows Server 2008 R2 | 1700 GB|
|4| Windows 7 | 1700 GB|

Az alábbi táblázat megmagyarázza, hogy az egyes adatforrásméretek hogyan lettek meghatározva.

|   Adatforrás  |   Részletek |
| :-------------: |:-------------|
|Kötet |Egy kiszolgáló vagy ügyfélgép egyetlen kötetéről biztonsági mentés alatt álló adatok mennyisége|
|Hyper-V virtuális gép | A biztonsági mentés alatt álló virtuális gép összes virtuális merevlemezének összes adata|
|Microsoft SQL Server-adatbázis | A biztonsági mentés alatt álló egyetlen SQL-adatbázis mérete |
|Microsoft SharePoint |Egy biztonsági mentés alatt álló SharePoint-farm tartalmának és konfigurációs adatbázisainak összessége|
|Microsoft Exchange |Egy biztonsági mentés alatt álló Exchange-kiszolgáló összes Exchange-adatbázisa|
|BMR/Rendszerállapot |A biztonsági mentés alatt álló gép BMR-ének vagy rendszerállapotának minden egyes másolata|

## Egy biztonsági mentési feladat napi ütemezési száma korlátozott?<br/>
Igen, Windows Server vagy Windows-ügyfél esetén a biztonsági mentési feladatok naponta legfeljebb háromszor futtathatók. System Center DPM esetén a biztonsági mentési feladatok naponta legfeljebb kétszer futtathatók. Az infrastruktúra-szolgáltatás virtuális gépei esetén a biztonsági mentési feladat naponta legfeljebb egyszer futtatható.

## Van különbség a DPM és a Windows Server (pl. Windows Server kiszolgálón DPM nélkül) esetén az ütemezési házirendben? <br/>
Igen. A DPM használatával megadhat napi, heti, havi és évi ütemterveket. A Windows Server (DPM nélküli) használata csak napi és heti ütemterv megadását teszi lehetővé.

## Van különbség a DPM és a Windows Server vagy Windows-ügyfél (pl. Windows Server kiszolgálón DPM nélkül) esetén a megtartási házirendben?<br/>
Nem, a DPM és a Windows Server vagy Windows-ügyfél is rendelkezik napi, heti, havi és évi megtartási házirendekkel.

## Beállíthatom a megtartási házirendeket szelektíven – pl. konfigurálom hetente és naponta, de nem évente és havonta?<br/>
Igen, az Azure Backup megtartási struktúrája az igényeihez igazodva teljes rugalmasságot tesz lehetővé a megtartási házirend meghatározásában.

## „Ütemezhetek egy biztonsági mentést” este 6 órára, és megadhatok „megtartási házirendeket” egy másik időpontra?<br/>
Nem. Megtartási házirendeket csak biztonsági mentési pontokon lehet alkalmazni. A következő képen a megtartási házirend 0 és 18 órakor készült biztonsági mentésekre van meghatározva. <br/>

![Biztonsági mentés és megtartás ütemezése](./media/backup-azure-backup-faq/Schedule.png)
<br/>

## Egy növekményes másolat át lesz küldve az ütemezett megtartási házirendekhez? <br/>
Nem, a növekményes másolat a biztonsági mentés ütemezési oldalán megjelölt időpont alapján lesz elküldve. A megtartható pontok a megtartási házirend alapján vannak meghatározva.

## Ha egy biztonsági másolat hosszú ideig van megtartva, több időbe telik egy régebbi adatpont helyreállítása? <br/>
 Nem – a legrégebbi és a legújabb pont helyreállítási ideje azonos. Minden helyreállítási pont teljes pontként viselkedik.

## Ha minden helyreállítási pont olyan, mint egy teljes pont, ez befolyásolja a teljes számlázandó biztonsági mentési tárolást?<br/>
A tipikus hosszú távú megtartási pontok az adatok biztonsági másolatát teljes pontokként tárolják. A teljes pontok tárolás szempontjából *nem hatékonyak*, azonban könnyebben és gyorsabban visszaállíthatók. A növekményes másolatok tárolás szempontjából *hatékonyak* de egy adatlánc visszaállítását teszik szükségessé, ami befolyásolja a helyreállítási időt. Az Azure Backup tárolási architektúrája ezen két megoldás előnyeit ötvözi az adatok optimális tárolásával a gyors visszaállítás érdekében és alacsony tárolási költségekkel. Ez az adattárolási módszer biztosítja, hogy a bemenő és kimenő sávszélesség is hatékonyan van felhasználva. Mind az adattárolási hely, mind az adatok helyreállításához szükséges idő mértéke a minimális szinten van tartva. Megtudhatja, hogy miért hatékonyak a [növekményes biztonsági mentések](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/). 

## A létrehozható helyreállítási pontok száma korlátozott?<br/>
Nem. Eltöröltük a helyreállítási pontok korlátozásait. Annyi helyreállítási pontot hozhat létre, amennyit csak szeretne.

## Miért nem egyenlő a biztonsági mentés közben átvitt adatok mennyisége azon adatokéval, amelyekről biztonsági mentés készül?<br/>
 Minden, az Azure Backup ügynökétől, az SCDPM-ből vagy az Azure Backup Serverről származó adatot, amelyről biztonsági mentés készül, a rendszer tömörít és titkosít az átvitel előtt. A tömörítés és a titkosítás alkalmazása után a biztonsági mentési tárolóban lévő adatok 30–40 százalékkal kisebbek.

## Van mód a Backup szolgáltatás által használt sávszélesség nagyságának beállítására?<br/>
 Igen, használhatja a Backup ügynök **Tulajdonságok módosítása** beállítását a sávszélesség módosításához. Beállíthatja a sávszélesség méretét, továbbá az időpontokat, amikor használja ezt a sávszélességet. További információt itt talál: [Hálózati szabályozás](../backup-configure-vault.md#enable-network-throttling)

## Az internetes sávszélességem korlátozott a biztonsági mentéshez szükséges adatok mennyiségéhez képest. Van mód arra, hogy egy gyorsabb internetkapcsolattal rendelkező helyre vigyem az adatokat, és onnan küldjem azokat az Azure-ba? <br/>
Készíthet biztonsági másolatot az adatairól az Azure számára a szabványos online biztonsági mentési folyamattal, vagy használhatja az Azure Import/Export szolgáltatást, hogy átvigye az adatokat az Azure-blobtárolóba. Más mód nem létezik az adatok biztonsági másolatának az Azure tárolóba juttatására. Az Azure Import/Export szolgáltatás Azure Backuppal történő használatáról további információt az [Offline Backup munkafolyamat](backup-azure-backup-import-export.md) című cikkben talál.

## Hány helyreállítást végezhetek el az Azure-ban biztonsági másolattal rendelkező adatokból?<br/>
Az Azure Backupból történő helyreállítások száma korlátlan.

## Az Azure adatközpontból helyreállítás közben keletkező kimenő forgalomért fizetnem kell?<br/>
 Nem. A helyreállítások ingyenesek, és nem kell fizetni a kimenő forgalomért.

## Az Azure-ba küldött adatok titkosítottak? <br/>
Igen. Az adatokat a rendszer az AES256 használatával a helyszíni kiszolgálón/ügyfélen/SCDPM-gépen titkosítja, és egy biztonságos HTTPS-kapcsolaton keresztül küldi el.

## Az adatokról készült biztonsági másolat az Azure-on is titkosított?<br/>
 Igen. Az Azure-ba küldött (aktívan nem használt) adatok titkosítva maradnak. A Microsoft soha nem fejt vissza adatokat. Azure virtuális gép biztonsági mentéséhez az Azure Backup a virtuális gép titkosítására támaszkodik, vagyis ha a virtuális gépe az Azure Disk Encryption vagy más titkosítási technológiával van titkosítva, az Azure Backup ezzel védi az adatait. 

## Mi az adatok biztonsági másolatának titkosításához használt titkosítási kulcs minimális hossza? <br/>
 A titkosítási kulcsnak legalább 16 karakter hosszúnak kell lennie.

## Mi történik, ha elveszítem a titkosítási kulcsot? Helyre tudom állítani az adatokat (vagy) a Microsoft helyre tudja állítani az adatokat? <br/>
Az adatok biztonsági másolatának titkosításához használt kulcs csak az ügyfél telephelyén van jelen. A Microsoft nem tárol másolatot az Azure-ban, és nem rendelkezik hozzáféréssel a kulcshoz. Ha az ügyfél elveszíti a kulcsot, a Microsoft nem tudja helyreállítani az adatok biztonsági másolatát.

## Hogyan változtathatom meg az Azure Backup ügynök számára meghatározott gyorsítótár helyét?<br/>
 Menjen végig lépésenként az alábbi felsoroláson a gyorsítótár helyének megváltoztatásához.
- Állítsa le a Backup motort úgy, hogy végrehajtja a következő parancsot egy rendszergazdai jogú parancssorban:

  ```PS C:\> Net stop obengine```

- Ne helyezze át a fájlokat. Ehelyett másolja a gyorsítótár mappáját egy másik, elegendő tárhellyel rendelkező meghajtóra. Miután ellenőrizte, hogy a biztonsági másolatok működnek az új gyorsítótárhelyen, az eredeti gyorsítótárhelyet eltávolíthatja.

- Frissítse a következő beállításjegyzékbeli bejegyzéseket az új gyorsítótárhely mappájának elérési útjával.<br/>

|Beállításjegyzékbeli elérési út | Beállításjegyzék kulcsa | Érték |
| ------ | ------- | ------|
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` | ScratchLocation | *Új gyorsítótár-mappa helye* |
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` | ScratchLocation | *Új gyorsítótár-mappa helye* |

- Indítsa újra a Backup motort úgy, hogy végrehajtja a következő parancsot egy rendszergazdai jogú parancssorban:

  ```PS C:\> Net start obengine```

  Miután a biztonsági mentés létrehozása sikeresen befejeződött az új gyorsítótárhelyen, eltávolíthatja az eredeti gyorsítótármappát.

## Hova helyezhetem el az Azure Backup ügynök gyorsítótármappáját, hogy megfelelően működjön?<br/>
A gyorsítótár-mappa számára a következő helyek nem javasoltak:

- Hálózati megosztás vagy cserélhető adathordozó: A gyorsítótármappának helyinek kell lennie az online biztonsági mentés használatával mentést igénylő kiszolgáló számára. A hálózati helyek és a cserélhető adathordozók, például az USB-meghajtók nem támogatottak.
- Offline kötetek: A gyorsítótár-mappának online kell lennie az Azure Backup ügynök használatával készített várható biztonsági mentés számára.

## Vannak a gyorsítótármappának olyan attribútumai, amelyek nem támogatottak?<br/>
 A következő attribútumok vagy kombinációik nem támogatottak a gyorsítótármappa számára:

- Titkosított
- Deduplikált
- Tömörített
- Ritka
- Újraelemzési pont

Sem a gyorsítótármappának, sem a metaadatok virtuális merevlemez-meghajtójának nem érdemes a fenti attribútumokkal rendelkeznie az Azure Backup ügynök hibátlan működése érdekében.



<!--HONumber=sep16_HO1-->


