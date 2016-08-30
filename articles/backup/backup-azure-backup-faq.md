<properties
   pageTitle="Azure Backup – gyakori kérdések | Microsoft Azure"
   description="Ez a cikk megválaszolja a biztonsági mentési szolgáltatásra és ügynökre, a biztonsági mentésre és azok megtartására, helyreállításra, biztonságára és egyéb, a biztonsági mentéssel és a vészhelyreállítással kapcsolatos általános gyakori kérdéseket."
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""
   keywords="backup and disaster recovery; backup service"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.date="04/04/2016"
     ms.author="trinadhk; giridham; arunak; markgal; jimpark;"/>

# Azure Backup szolgáltatás – GYIK

> [AZURE.SELECTOR]
- [Backup GYIK klasszikus módhoz](backup-azure-backup-faq.md)
- [Backup GYIK ARM módhoz](backup-azure-backup-ibiza-faq.md)

Ez a cikk az Azure Backup szolgáltatással kapcsolatos gyakori kérdések (és a hozzájuk tartozó válaszok) listája. Közösségünk gyorsan válaszol, és ha egy kérdést gyakran feltesznek, hozzáadjuk ehhez a cikkhez. A kérdésekre adott válaszok általában hivatkozási vagy támogatási információkat tartalmaznak. Az Azure Backup szolgáltatással kapcsolatban ezen vagy egy kapcsolódó cikk Disqus szakaszában tehet fel kérdéseket. Emellett egy fórumbejegyzésben is feltehet kérdéseket az Azure Backup szolgáltatással kapcsolatban a [vitafórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## Telepítés és konfiguráció
**1. kérdés Melyek azok az operációs rendszerek, amelyekből biztonsági mentést tudok készíteni az Azure-ba az Azure Backup használatával?** <br/>
1. válasz Az Azure Backup a következő operációs rendszereket támogatja


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

**2. kérdés Honnan tölthetem le a legújabb Azure Backup ügynököt?** <br/>
2. válasz [Innen](http://aka.ms/azurebackup_agent) töltheti le a legújabb ügynököt a Windows Server, System Center DPM vagy Windows-ügyfél biztonsági mentése számára. Ha egy virtuális gépről szeretne biztonsági mentést készíteni, használja a virtuálisgép-ügynököt (amely automatikusan telepíti a megfelelő bővítményt). Az Azure katalógusból létrehozott virtuális gépeken a virtuálisgép-ügynök már jelen van.

**3. kérdés Az SCDPM kiszolgáló melyik verziója támogatott?** <br/>
3. válasz Javasolt a [legújabb](http://aka.ms/azurebackup_agent) Azure Backup ügynök telepítése az SCDPM legújabb kumulatív frissítésén (2015 júliusában ez az UR6)

**4. kérdés Az Azure Backup ügynök telepítése közben a rendszer a **tároló hitelesítő adatainak** megadását kéri. A tároló hitelesítő adatai lejárnak?
4. válasz Igen, a tároló hitelesítő adatai 48 óra után lejárnak. Ha a fájl lejár, jelentkezzen be az Azure portálra, és töltse le a tároló hitelesítő adatfájljait a Backup tárolójából.

**5. kérdés Az egyes Azure-előfizetésekben létrehozható biztonsági mentési tárolók száma korlátozott?** <br/>
5. válasz Igen. A 2015. júliusi állapot szerint előfizetésenként 25 tárolót hozhat létre. Ha több tárolóra van szüksége, hozzon létre egy új előfizetést.

**6. kérdés Egy tárolóra egy számlázási entitásként kell tekinteni?** <br/>
6. válasz Habár lehet részletes számlát kérni minden tárolóhoz, erősen javasolt, hogy egy Azure-előfizetést tekintsen egy számlázási entitásként. Ez minden szolgáltatásra vonatkozik, és könnyebben kezelhető.

**7. kérdés Az egyes tárolókhoz regisztrálható kiszolgálók/gépek száma korlátozott?** <br/>
7. válasz Igen, tárolónként 50 gépet regisztrálhat. Az Azure infrastruktúra-szolgáltatás virtuális gépei esetén a korlát 200 virtuális gép tárolónként. Ha több gépet szeretne regisztrálni, hozzon létre egy új tárolót.

**8. kérdés Egy Windows-kiszolgálóról vagy -ügyfélről, illetve SCDPM-kiszolgálóról biztonsági másolatként menthető adatok mennyisége korlátozott?** <br/>
8. válasz Nem.

**9. kérdés Hogyan regisztrálhatom a kiszolgálómat egy másik adatközpontra?**<br/>
9. válasz A biztonsági mentési adatokat a rendszer annak a Backup szolgáltatásnak az adatközpontjába küldi, amelyikre regisztrálva van. Az adatközpont módosításának legegyszerűbb módja az ügynök eltávolítása, újratelepítése, majd egy új adatközpontra regisztrálása.

**10. kérdés Mi történik, ha átnevezek egy Windows-kiszolgálót, amelyről biztonsági adatmentés történik az Azure rendszerbe?**<br/>
10. válasz Ha átnevez egy kiszolgálót, minden aktuálisan konfigurált biztonsági mentés leáll.
Regisztrálnia kell a kiszolgáló új nevét a Backup-tárolóban. Ha létrehozta az új regisztrációt, az első biztonsági mentési művelet egy teljes biztonsági mentés lesz, nem pedig növekményes biztonsági mentés. Ha helyre kell állítania a korábban, a régi kiszolgálónévvel a tárolóba mentett adatokat, ezt az **Adatok helyreállítása** varázsló [**Másik kiszolgáló**](backup-azure-restore-windows-server.md#recover-to-an-alternate-machine) beállításában teheti meg.


**11. kérdés Milyen típusú meghajtókon lévő fájlokról és mappákról tudok biztonsági másolatot készíteni?** <br/>
11. válasz A következő meghajtókról/kötetekről nem lehet biztonsági másolatot készíteni:

- Cserélhető adathordozó: A meghajtónak állandónak kell lennie ahhoz, hogy egy biztonsági mentés forráselemeként lehessen használni.
- Csak olvasható kötetek: A kötetnek a működéshez írhatónak kell lennie a kötet árnyékmásolat (VSS) számára.
- Offline kötetek: A kötetnek a működéshez online-nak kell lennie a VSS számára.
- Hálózati megosztás: A kötetnek helyinek kell lennie a kiszolgáló számára, hogy az online biztonsági mentés használatával biztonsági másolatot lehessen róla készíteni.
- BitLocker által védett kötetek: A kötetet fel kell oldani a biztonsági mentés megkezdése előtt.
- Fájlrendszer azonosítása: Az online biztonsági mentési szolgáltatás ezen verziója csak az NTFS fájlrendszert támogatja.

**12. kérdés A kiszolgálón lévő milyen fájlokról és mappákról készíthetek biztonsági másolatot?**<br/>
12. válasz A következő típusok támogatottak:

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

**13. kérdés Mi a gyorsítótármappa minimális méretkövetelménye?** <br/>
13. válasz A gyorsítótármappa mérete határozza meg azt az adatmennyiséget, amelyről biztonsági másolatot készít. A gyorsítótár mappájának az adatok tárolásához szükséges terület 5%-ának kell lennie.

**14. kérdés Ha a szervezetem egy biztonsági mentési tárolóval rendelkezik, hogyan tudom az egyik kiszolgáló adatait elszigetelni egy másik kiszolgálóétól az adatok visszaállításakor?**<br/>
14. válasz Minden kiszolgáló, amely ugyanahhoz a tárolóhoz van regisztrálva, képes helyreállítani más, *ugyanazt a hozzáférési kódot használó* kiszolgálók adatainak biztonsági másolatait. Ha olyan kiszolgálókkal rendelkezik, amelyek adatainak biztonsági másolatait szeretné elszigetelni a szervezetében található más kiszolgálóétól, használjon egy erre kijelölt hozzáférési kódot ezekhez a kiszolgálókhoz. Például a humánerőforrás-kiszolgálók használhatnának egy titkosító hozzáférési kódot, a könyvelési kiszolgálók egy másikat és a tároló kiszolgálók egy harmadikat.

**15. kérdés „Át tudom telepíteni” az adataimról készült biztonsági mentést az előfizetéseim között?** <br/>
15. válasz: Nem

**16.kérdés „Át tudom telepíteni” a biztonsági mentési tárolót az előfizetéseim között?** <br/>
16. válasz: Nem. A tároló előfizetési szinten jön létre, és a létrehozása után nem lehet másik előfizetéshez hozzárendelni.

**17. kérdés Az Azure Backup ügynök működik egy Windows Server 2012 deduplikációt használó kiszolgálón?** <br/>
17. válasz: Igen. Az ügynökszolgáltatás átalakítja a deduplikált adatokat normális adatokká, amikor előkészíti a biztonsági mentési műveletet. Ezután optimalizálja az adatokat a biztonsági mentéshez, titkosítja őket, majd elküldi a titkosított adagokat az online biztonsági mentési szolgáltatásba.

**18. kérdés Ha megszakítok egy már elindult biztonsági mentési feladatot, az átküldött adatok biztonsági másolata törlődik?** <br/>
18. válasz: Nem. A biztonsági mentési tároló eltárolja azon adatok biztonsági másolatát, amelyek a megszakítás pillanatában már át lettek küldve. Az Azure Backup egy ellenőrzőpont-alapú mechanizmust használ, így a biztonsági mentés közben esetenként ellenőrzőpontokat ad az adatok biztonsági másolatához. Mivel ellenőrzőpontok vannak az adatok biztonsági másolatában, a következő biztonsági mentési folyamat ellenőrizni tudja a fájlok épségét. A következő aktivált biztonsági mentés növekményes mentés lesz a korábban mentett adatok biztonsági másolata alapján. A növekményes biztonsági mentés jobban kihasználja a sávszélességet, így nem kell ugyanazokat az adatokat ismételten elküldeni.

**19. kérdés Miért jelenik meg „Az Azure Backup nem lett konfigurálva ezen a kiszolgálón” figyelmeztetés annak ellenére, hogy korábban ütemeztem a rendszeres biztonsági mentést?** <br/>
19. válasz: Ez a figyelmeztetés akkor jelenik meg, amikor a helyi kiszolgálón tárolt ütemezett biztonsági mentési beállítások nem egyeznek meg a biztonsági mentési tárolóban tárolt beállításokkal. Ha a kiszolgáló vagy a beállítások egy korábbi hibátlan állapotra lettek visszaállítva, a biztonsági mentés ütemezése elveszítheti a szinkronizálást. Ha ilyen figyelmeztetést kap, [konfigurálja újra a biztonsági mentési házirendet](backup-azure-manage-windows-server.md), majd kattintson a **Biztonsági mentés azonnali futtatása** parancsra a helyi kiszolgálónak az Azure szolgáltatással történő újraszinkronizálásához.

**20. kérdés Milyen tűzfalszabályokat kell konfigurálni az Azure Backup szolgáltatáshoz?** <br/>
20. válasz A helyi adatoknak és a számítási feladatoknak az Azure rendszerbe történő zökkenőmentes átviteléhez és védelméhez a tűzfalnak érdemes engedélyezi a következő URL-címekkel történő kommunikációt:

- www.msftncsi.com
- \*.Microsoft.com
- \*.WindowsAzure.com
- \*.microsoftonline.com
- \*.windows.net

**21. kérdés Telepíthetem az Azure Backup ügynököt egy olyan Azure virtuális gépre, amelyet az Azure Backup szolgáltatás már támogat a virtuális gép bővítmény használatával?** <br/>
21. válasz Abszolút. Az Azure Backup virtuálisgép-szintű biztonsági mentést nyújt az Azure virtuális gépeknek az erre szolgáló bővítmény használatával. Az Azure Backup ügynököt telepítheti egy vendég Windows operációs rendszerre, hogy megvédje a vendég operációs rendszeren található fájlokat és mappákat.

**22. kérdés Telepíthetem az Azure Backup ügynököt egy Azure virtuális gépre, hogy biztonsági mentést készítsek egy, az Azure virtuális gép által biztosított ideiglenes tárolón lévő fájlokról és mappákról?** <br/>
22. válasz Az Azure Backup ügynököt telepítheti a vendég Windows operációs rendszeren, és biztonsági mentést készíthet a fájlokról és mappákról egy ideiglenes tárolóra. Ugyanakkor vegye figyelembe, hogy a biztonsági mentések sikertelenek lesznek, ha az ideiglenes tároló adatait törli. Emellett ha az ideiglenes tároló adatait törölték, csak állandó tárolóba tud visszaállítani.

**23. kérdés Telepítettem az Azure Backup ügynököt a fájljaim és mappáim megvédéséhez. Telepíthetem az SCDPM szolgáltatást, hogy együttműködjön az Azure Backup ügynökkel a helyi alkalmazások vagy a virtuális gép számítási feladatainak Azure-ban történő megvédésében?** <br/>
23. válasz Az Azure Backup SCDPM szolgáltatással történő használatához javasolt először az SCDPM-et telepíteni, és csak aztán az Azure Backup ügynököt. Ez biztosítja az Azure Backup ügynök és az SCDPM problémamentes integrációját, és lehetővé teszi fájlok és mappák, alkalmazások számítási feladatai és virtuális gépek Azure-ban történő védelmét közvetlenül az SCDPM felügyeleti konzoljából. Nem javasolt és nem támogatott az SCDPM telepítése az Azure Backup ügynök után a fent említett célokból.

**24. kérdés Mi a fájl elérési útjának hossza, amely meghatározható az Azure Backup ügynök használatával az Azure Backup házirendjének részeként?** <br/>  
24. válasz Az Azure Backup ügynök az NTFS-re hagyatkozik. A [fájl elérési útvonalának hossz-specifikációját a Windows API korlátozza](https://msdn.microsoft.com/library/aa365247.aspx#fully_qualified_vs._relative_paths). Ha a rendszer olyan fájlokról készít biztonsági másolatot, amelyeknek elérési útja hosszabb, mint a Windows API által meghatározott korlát, az ügyfelek dönthetnek a mentendő fájlok szülőmappájának vagy lemezmeghajtójának biztonsági mentése mellett.  

**25. kérdés Mely karakterek engedélyezettek az Azure Backup ügynököt használó Azure Backup házirend elérési útjában?** <br>  
25. válasz Az Azure Backup ügynök az NTFS-re hagyatkozik. Ez engedélyezi az [NTFS által támogatott karakterek](https://msdn.microsoft.com/library/aa365247.aspx#naming_conventions) használatát a fájl meghatározásának részeként.  

**26. kérdés Használhatom az Azure Backup kiszolgálót egy operációs rendszer nélküli helyreállítás (BMR) biztonsági másolatának létrehozásához egy fizikai kiszolgálón?** <br/>
26. válasz Igen.

**27. kérdés Konfigurálhatom úgy a Backup szolgáltatást, hogy levelet küldjön, ha egy biztonsági mentési feladat sikertelen volt?** <br/>
27. válasz Igen, a Backup szolgáltatás számos eseményalapú értesítéssel rendelkezik, amelyek egy PowerShell-parancsprogrammal használhatók. A teljes leírást itt találja: [Figyelmeztető értesítések](backup-azure-manage-vms.md#alert-notifications)



## Biztonsági mentés és megtartás
**1. kérdés Létezik méretkorlátozás a biztonsági mentésre kijelölt adatforrásokra vonatkozóan?** <br/>
1. válasz A 2015. augusztusi állapot szerint a támogatott operációs rendszerek esetén az adatforrás maximális mérete a következő:

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

**2. kérdés Egy biztonsági mentési feladat napi ütemezési száma korlátozott?**<br/>
2. válasz Igen, Windows Server vagy Windows-ügyfél esetén a biztonsági mentési feladatok naponta legfeljebb háromszor futtathatók. System Center DPM esetén a biztonsági mentési feladatok naponta legfeljebb kétszer futtathatók. Az infrastruktúra-szolgáltatás virtuális gépei esetén a biztonsági mentési feladat naponta legfeljebb egyszer futtatható.

**3. kérdés Van különbség a DPM és a Windows Server (pl. Windows Server kiszolgálón DPM nélkül) esetén az ütemezési házirendben?** <br/>
3. válasz Igen. A DPM használatával megadhat napi, heti, havi és évi ütemterveket. A Windows Server (DPM nélküli) használata csak napi és heti ütemterv megadását teszi lehetővé.

**4. kérdés Van különbség a DPM és a Windows Server vagy Windows-ügyfél (pl. Windows Server kiszolgálón DPM nélkül) esetén a megtartási házirendben?**<br/>
4. válasz Nem, a DPM és a Windows Server vagy Windows-ügyfél is rendelkezik napi, heti, havi és évi megtartási házirendekkel.

**5. kérdés Beállíthatom a megtartási házirendeket szelektíven – pl. konfigurálom hetente és naponta, de nem évente és havonta?**<br/>
5. válasz Igen, az Azure Backup megtartási struktúrája az igényeihez igazodva teljes rugalmasságot tesz lehetővé a megtartási házirend meghatározásában.

**6. kérdés „Ütemezhetek egy biztonsági mentést” este 6 órára, és megadhatok „megtartási házirendeket” egy másik időpontra?**<br/>
6. válasz Nem. Megtartási házirendeket csak biztonsági mentési pontokon lehet alkalmazni. A következő képen a megtartási házirend 0 és 18 órakor készült biztonsági mentésekre van meghatározva. <br/>

![Biztonsági mentés és megtartás ütemezése](./media/backup-azure-backup-faq/Schedule.png)
<br/>

**7. kérdés Egy növekményes másolat át lesz küldve az ütemezett megtartási házirendekhez?** <br/>
7. válasz Nem, a növekményes másolat a biztonsági mentés ütemezési oldalán megjelölt időpont alapján lesz elküldve. A megtartható pontok a megtartási házirend alapján vannak meghatározva.

**8. kérdés Ha egy biztonsági másolat hosszú ideig van megtartva, több időbe telik egy régebbi adatpont helyreállítása?** <br/>
8. válasz Nem – a legrégebbi és a legújabb pont helyreállítási ideje azonos. Minden helyreállítási pont teljes pontként viselkedik.

**9. kérdés Ha minden helyreállítási pont olyan, mint egy teljes pont, ez befolyásolja a teljes számlázandó biztonsági mentési tárolást?**<br/>
9. válasz  A tipikus hosszú távú megtartási pontok az adatok biztonsági másolatát teljes pontokként tárolják. A teljes pontok tárolás szempontjából *nem hatékonyak*, azonban könnyebben és gyorsabban visszaállíthatók. A növekményes másolatok tárolás szempontjából *hatékonyak* de egy adatlánc visszaállítását teszik szükségessé, ami befolyásolja a helyreállítási időt. Az Azure Backup tárolási architektúrája ezen két megoldás előnyeit ötvözi az adatok optimális tárolásával a gyors visszaállítás érdekében és alacsony tárolási költségekkel. Ez az adattárolási módszer biztosítja, hogy a bemenő és kimenő sávszélesség is hatékonyan van felhasználva. Mind az adattárolási hely, mind az adatok helyreállításához szükséges idő mértéke a minimális szinten van tartva.

**10. kérdés A létrehozható helyreállítási pontok száma korlátozott?**<br/>
10. válasz Nem. Eltöröltük a helyreállítási pontok korlátozásait. Annyi helyreállítási pontot hozhat létre, amennyit csak szeretne.

**11. kérdés Miért nem egyenlő a biztonsági mentés közben átvitt adatok mennyisége azon adatokéval, amelyekről biztonsági mentés készül?**<br/>
11. válasz Minden adat, amelyről biztonsági mentés készül, tömörítésre és titkosításra kerül az átvitel előtt. A tömörítés és a titkosítás alkalmazása után a biztonsági mentési tárolóban lévő adatok 30–40 százalékkal kisebbek.

**12. kérdés Van mód a Backup szolgáltatás által használt sávszélesség nagyságának beállítására?**<br/>
12. válasz Igen, használhatja a Backup ügynök **Tulajdonságok módosítása** beállítását a sávszélesség módosításához. Beállíthatja a sávszélesség méretét, továbbá az időpontokat, amikor használja ezt a sávszélességet. További információt itt talál: [Hálózati szabályozás](../backup-configure-vault.md#enable-network-throttling)

**13. kérdés Az internetes sávszélességem korlátozott a biztonsági mentéshez szükséges adatok mennyiségéhez képest. Van mód arra, hogy egy gyorsabb internetkapcsolattal rendelkező helyre vigyem az adatokat, és onnan küldjem azokat az Azure-ba?** <br/>
13. válasz Készíthet biztonsági másolatot az adatairól az Azure számára a szabványos online biztonsági mentési folyamattal, vagy használhatja az Azure Import/Export szolgáltatást, hogy átvigye az adatokat az Azure-blobtárolóba. Más mód nem létezik az adatok biztonsági másolatának az Azure tárolóba juttatására. Az Azure Import/Export szolgáltatás Azure Backuppal történő használatáról további információt az [Offline Backup munkafolyamat](backup-azure-backup-import-export.md) című cikkben talál.


## Helyreállítás
**1. kérdés Hány helyreállítást végezhetek el az Azure-ban biztonsági másolattal rendelkező adatokból?**<br/>
1. válasz Az Azure Backupból történő helyreállítások száma korlátlan.

**2. kérdés Az Azure adatközpontból helyreállítás közben keletkező kimenő forgalomért fizetnem kell?**<br/>
2. válasz Nem. A helyreállítások ingyenesek, és nem kell fizetni a kimenő forgalomért.

## Biztonság
**1. kérdés Az Azure-ba küldött adatok titkosítottak?** <br/>
1. válasz Igen. Az adatokat a rendszer az AES256 használatával a helyszíni kiszolgálón/ügyfélen/SCDPM-gépen titkosítja, és egy biztonságos HTTPS-kapcsolaton keresztül küldi el.

**2. kérdés Az adatokról készült biztonsági másolat az Azure-on is titkosított?**<br/>
2. válasz Igen. Az Azure-ba küldött (aktívan nem használt) adatok titkosítva maradnak. A Microsoft soha nem fejt vissza adatokat.

**3. kérdés Mi az adatok biztonsági másolatának titkosításához használt titkosítási kulcs minimális hossza?** <br/>
3. válasz A titkosítási kulcsnak legalább 16 karakter hosszúnak kell lennie.

**4. kérdés Mi történik, ha elveszítem a titkosítási kulcsot? Helyre tudom állítani az adatokat (vagy) a Microsoft helyre tudja állítani az adatokat?** <br/>
4. válasz Az adatok biztonsági másolatának titkosításához használt kulcs csak az ügyfél telephelyén van jelen. A Microsoft nem tárol másolatot az Azure-ban, és nem rendelkezik hozzáféréssel a kulcshoz. Ha az ügyfél elveszíti a kulcsot, a Microsoft nem tudja helyreállítani az adatok biztonsági másolatát.
 

## Biztonsági mentés gyorsítótára

**1. kérdés Hogyan változtathatom meg az Azure Backup ügynök számára meghatározott gyorsítótár helyét?**<br/>
1. válasz Menjen végig lépésenként az alábbi felsoroláson a gyorsítótár helyének megváltoztatásához.
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

**2. kérdés Hova helyezhetem el az Azure Backup ügynök gyorsítótármappáját, hogy megfelelően működjön?**<br/>
2. válasz A gyorsítótár-mappa számára a következő helyek nem javasoltak:

- Hálózati megosztás vagy cserélhető adathordozó: A gyorsítótármappának helyinek kell lennie az online biztonsági mentés használatával mentést igénylő kiszolgáló számára. A hálózati helyek és a cserélhető adathordozók, például az USB-meghajtók nem támogatottak.
- Offline kötetek: A gyorsítótár-mappának online kell lennie az Azure Backup ügynök használatával készített várható biztonsági mentés számára.

**3. kérdés Vannak a gyorsítótármappának olyan attribútumai, amelyek nem támogatottak?**<br/>
3. válasz A következő attribútumok vagy kombinációik nem támogatottak a gyorsítótármappa számára:

- Titkosított
- Deduplikált
- Tömörített
- Ritka
- Újraelemzési pont

Sem a gyorsítótármappának, sem a metaadatok virtuális merevlemez-meghajtójának nem érdemes a fenti attribútumokkal rendelkeznie az Azure Backup ügynök hibátlan működése érdekében.



<!--HONumber=jun16_HO2-->


