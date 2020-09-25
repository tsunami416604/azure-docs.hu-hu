---
title: A Azure File Sync Cloud-rétegek ismertetése | Microsoft Docs
description: További információ a felhőalapú rétegek kiválasztásáról, a választható Azure File Sync funkcióról. A gyakran használt fájlokat a rendszer helyileg gyorsítótárazza a kiszolgálón; mások a Azure Filesra vannak bontva.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9df06a9d81ef3c9fbe3380bab88325a586981db9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91329312"
---
# <a name="cloud-tiering-overview"></a>A felhőalapú rétegek áttekintése
A felhőalapú rétegek a Azure File Sync választható funkciója, amelyekben a gyakran használt fájlok a kiszolgálón helyileg vannak gyorsítótárazva, míg az összes többi fájl a házirend-beállítások alapján Azure Files. Egy fájl többszintű kiválasztásakor a Azure File Sync fájlrendszer-szűrő (StorageSync.sys) a fájlt helyileg váltja fel egy mutatóval vagy újraelemzési ponttal. Az újraelemzési pont a fájl URL-címét jelöli Azure Files. A többrétegű fájlok "offline" attribútummal és az NTFS fájlrendszerrel beállított FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS attribútummal is rendelkeznek, így a harmadik féltől származó alkalmazások biztonságosan azonosíthatják a többrétegű fájlokat.
 
Amikor egy felhasználó megnyit egy rétegű fájlt, Azure File Sync zökkenőmentesen visszahívja a fájl adatait a Azure Files anélkül, hogy a felhasználónak tudniuk kell róla, hogy a fájlt az Azure tárolja. 
 
 > [!Important]  
 > A felhő-rétegek nem támogatottak a Windows rendszerköteten.
    
 > [!Important]  
 > A lépcsőzetesen beállított fájlok felidézéséhez a hálózati sávszélességnek legalább 1 MB/s-nak kell lennie. Ha a hálózati sávszélesség kevesebb, mint 1 Mbps, előfordulhat, hogy a fájlok időtúllépési hiba miatt nem tudnak felidézni.

## <a name="cloud-tiering-faq"></a>Felhőalapú rétegek – gyakori kérdések

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>Hogyan működik a felhőalapú rétegek működése?
A Azure File Sync rendszerszűrő minden kiszolgálói végponton létrehoz egy "hő"-t a névtérből. A szolgáltatás az idő múlásával figyeli a hozzáféréseket (olvasási és írási műveleteket), majd a hozzáférés gyakorisága és recency alapján minden fájlhoz hozzárendel egy hő pontszámot. A legutóbb megnyitott fájl gyakran megtekinthető állapotba kerül, míg egy kis ideig nem fér hozzá a fájlhoz. Ha a kiszolgálón lévő fájl mennyisége meghaladja a szabad területhez beállított mennyiség küszöbértékét, akkor a rendszer a leghidegebb fájlokat Azure Files, amíg a szabad terület százalékos értéke nem teljesül.

Emellett megadhat egy dátum-házirendet minden olyan kiszolgálói végponton, amely a megadott számú napon belül nem elérhető fájlokat fogja felvenni, a rendelkezésre álló helyi tárterülettől függetlenül. Ez egy jó választás, hogy proaktívan szabadítson fel helyi lemezterületet, ha tudja, hogy az adott kiszolgálói végponton lévő fájlokat nem kell egy bizonyos életkoron túl helyileg megőrizni. Ezzel az adott köteten lévő más végpontok számára értékes helyi lemez-kapacitást szabadít fel, így több fájl is gyorsítótárazható.

A Cloud rétegű hő lényegében az összes szinkronizált fájl rendezett listája, és olyan helyen található, amelyen engedélyezve van a felhőalapú rétegek használata. Egy adott hő található egyedi fájl relatív helyzetének meghatározásához a rendszer a következő időbélyegek közül a maximumot használja, az adott sorrendben: MAX (utolsó hozzáférés időpontja, utolsó módosítás időpontja, létrehozás időpontja). Általában a legutóbbi hozzáférési idő nyomon követhető és elérhető. Ha azonban új kiszolgálói végpont jön létre, a felhőalapú rétegek engedélyezve vannak, akkor kezdetben nem volt elég idő a fájl elérésének megfigyeléséhez. A legutóbbi hozzáférési idő hiányában az utolsó módosítás ideje a hő relatív pozíciójának kiértékelésére szolgál. Ugyanez a tartalék érvényes a dátum házirendre. A legutóbbi hozzáférési idő nélkül a dátum-házirend az utolsó módosítás időpontjában fog működni. Ha ez nem érhető el, az egy fájl létrehozási idejére fog visszatérni. Az idő múlásával a rendszer több és több fájlhoz való hozzáférési kérelmet is betart, és a kimutatást a saját maga által követett utolsó hozzáférési idő előállításához használja fel.

A felhő-rétegek nem függnek az NTFS-szolgáltatástól a legutóbbi hozzáférési idő nyomon követéséhez. Ez az NTFS-szolgáltatás alapértelmezés szerint ki van kapcsolva, és a teljesítménnyel kapcsolatos megfontolások miatt nem ajánlott manuálisan engedélyezni ezt a szolgáltatást. A felhő-rétegek a legutóbbi hozzáférési időt külön és nagyon hatékonyan nyomon követik.

<a id="tiering-minimum-file-size"></a>
### <a name="what-is-the-minimum-file-size-for-a-file-to-tier"></a>Mennyibe kerül a fájl minimális mérete a szinthez?

Az ügynök 12-es és újabb verzióiban a fájlokra vonatkozó minimális fájlméret a fájlrendszer fürtjének méretétől függ. A Felhőbeli rétegek minimális mérete a fürt méretének és a minimális 8 KB-os értéknek a kiszámítására alkalmas. A következő táblázat a mennyiségi fürt méretétől függően a minimálisan felhasználható fájlméretet mutatja be:

|Kötet fürtjének mérete (bájt) |Ennek a méretnek vagy nagyobb méretű fájloknak lépcsőzetesen kell lenniük  |
|----------------------------|---------|
|4 KB vagy kisebb (4096)      | 8 KB    |
|8 KB (8192)                 | 16 KB   |
|16 KB (16384)               | 32 KB   |
|32 KB (32768)               | 64 KB   |
|64 KB (65536) és nagyobb    | 128 KB  |

A Windows Server 2019 és a Azure File Sync Agent 12-ös és újabb verziója esetén a fürtök mérete akár 2 MB is lehet, és a nagyobb szektorcsoportok méretének megegyező módon működik. A régebbi operációs rendszer vagy ügynök verziója legfeljebb 64 KB-ig támogatja a fürtök méretét, azonban a felhő-rétegek nem működnek.

A Windows által használt összes fájlrendszer, a fürt méretétől függően rendezi a merevlemezt (más néven a foglalási egység mérete). A fürt mérete a fájl tárolására használható legkisebb lemezterületet jelöli. Ha a fájlméretek nem jönnek létre a fürt méretének még többszörösére, a fájlnak a fürt következő többszörösére való tárolásához további helyet kell használni.

A Azure File Sync a Windows Server 2012 R2 és újabb operációs rendszert futtató NTFS-kötetek esetében támogatott. Az alábbi táblázat az új NTFS-kötet létrehozásakor az alapértelmezett szektorcsoport-méreteket ismerteti. 

|Kötet mérete    |Windows Server 2012R2 és újabb verziók |
|---------------|---------------|
|7 MB – 16 TB   | 4 KB          |
|16TB – 32 TB   | 8 KB          |
|32 TB TÁRTERÜLETET – 64 TB   | 16 KB         |
|64TB – 128 TB  | 32 KB         |
|128TB – 256 TB | 64 KB         |
|> 256 TB       | Nem támogatott |

Lehetséges, hogy a kötet létrehozásakor manuálisan formázta a kötetet egy másik szektorcsoport-mérettel. Ha a kötet a Windows egy korábbi verziójából ered, akkor az alapértelmezett szektorcsoportok mérete is eltérő lehet. [Ez a cikk további részleteket tartalmaz az alapértelmezett fürtök méretéről.](https://support.microsoft.com/help/140365/default-cluster-size-for-ntfs-fat-and-exfat) Még akkor is, ha 4 KB-nál kisebb méretű fürtöt választ, egy 8 KB-os korlátot, amely a minimálisan felhasználható fájlméretet eredményezi, továbbra is érvényes. (Akkor is, ha a műszakilag 2x fürt mérete nem éri el a 8 KB-ot.)

Az abszolút minimum oka az, hogy az NTFS tárolja a rendkívül kis méretű fájlokat – 1 KB 4 KB méretű fájlokat. A kötet más paramétereinek függvényében előfordulhat, hogy a kis fájlok nem tárolódnak a lemezen lévő fürtben. Előfordulhat, hogy az ilyen fájlokat közvetlenül a kötet főfájl-táblájában vagy az "MFT-rekord" tárolja. A felhő-réteg újraelemzési pontja mindig lemezen van tárolva, és pontosan egy fürtöt vesz igénybe. Az ilyen kis méretű fájlok megtakarítása nem lehetséges. A szélsőséges esetek akár több helyet is használhatnak a felhőalapú rétegek engedélyezésével. Ennek elleni védelem érdekében a Felhőbeli többméretű fájl legkisebb mérete 8 KB, 4 KB-os vagy kisebb méretű fürt esetén.

<a id="afs-volume-free-space"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work"></a>Hogyan működik a köteten található szabad hely rétegzési szabályzata?
A kötet szabad területe az a szabad terület, amelyet le szeretne foglalni azon a köteten, amelyen a kiszolgálói végpont található. Ha például a kötet szabad területe 20%-ra van állítva egy olyan köteten, amely egy kiszolgáló-végponttal rendelkezik, a legutóbb elért fájlok a lemezterület 80%-ában lesznek elfoglalva, az összes többi olyan fájllal, amely nem fér el az Azure-ba. A kötet szabad területe a kötet szintjén érvényes, nem pedig az egyes címtárak vagy szinkronizálási csoportok szintjére. 

<a id="volume-free-space-fastdr"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work-with-regards-to-new-server-endpoints"></a>Hogyan működik a köteten található szabad hely rétegzési szabályzata az új kiszolgálóvégpontok szempontjából?
Ha egy kiszolgálói végpontot újonnan létesítettek és csatlakoztatnak egy Azure-fájlmegosztást, akkor a kiszolgáló először lekéri a névteret, majd lekéri a tényleges fájlokat, amíg eléri a kötet szabad területének küszöbértékét. Ezt a folyamatot a gyors katasztrófa-helyreállítás vagy a névtér gyors visszaállítása is nevezik.

<a id="afs-effective-vfs"></a>
### <a name="how-is-volume-free-space-interpreted-when-i-have-multiple-server-endpoints-on-a-volume"></a>Hogyan értelmezi a rendszer a köteten található szabad hely mennyiségét, ha a kötetnek több kiszolgálói végpontja van?
Ha egy köteten egynél több kiszolgálói végpont található, akkor a köteten lévő összes kiszolgálói végponton megadott legnagyobb szabad terület nagysága az adott köteten lévő összes kiszolgáló-végponton. A fájlok a használati minták alapján lesznek feldolgozva, függetlenül attól, hogy melyik kiszolgálói végponthoz tartoznak. Ha például két kiszolgáló-végponttal rendelkezik egy köteten, a Endpoint1 és a Endpoint2, ahol a Endpoint1 a kötet szabad területének küszöbértéke 25%, a Endpoint2 pedig 50%-os szabad területtel rendelkezik, akkor a kiszolgálói végpontok esetében a kötet szabad területének küszöbértéke 50% lesz. 

<a id="date-tiering-policy"></a>
### <a name="how-does-the-date-tiering-policy-work-in-conjunction-with-the-volume-free-space-tiering-policy"></a>Hogyan működik a dátum rétegzési szabályzata a köteten található szabad hely rétegzési szabályzatával együtt? 
Ha egy kiszolgálói végponton engedélyezi a felhő-rétegek bekapcsolását, beállíthatja a kötet szabad területére vonatkozó házirendet. Mindig elsőbbséget élvez minden más szabályzattal szemben, beleértve a dátumra vonatkozó házirendet is. Lehetőség van arra is, hogy a köteten minden egyes kiszolgálói végponthoz engedélyezheti a dátum házirendjét. Ez a szabályzat úgy kezeli, hogy csak a megadott fájlok (azaz a () olvasása vagy írása) a szabályzat által ismertetett időtartományon belül maradjon helyi. A rendszer a megadott számú napon belül nem fér hozzá a fájlokhoz. 

A Felhőbeli rétegek az utolsó hozzáférési időt használják annak meghatározására, hogy mely fájlokat kell a rétegekbe állítani. A felhő-előállítók szűrő-illesztőprogramja (storagesync.sys) nyomon követi a legutóbbi hozzáférési időt, és naplózza a Felhőbeli adattárolóban tárolt adatokat. Lekérheti a Heat Store-t, és mentheti egy CSV-fájlba egy helyi PowerShell-parancsmag használatával.

```powershell
# There is a single heat store for files on a volume / server endpoint / individual file.
# The heat store can get very large. If you only need to retrieve the "coolest" number of items, use -Limit and a number

# Import the PS module:
Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'

# VOLUME FREE SPACE: To get the order in which files will be tiered using the volume free space policy:
Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName LastAccessTimeWithSyncAndTieringOrder

# DATE POLICY: To get the order in which files will be tiered using the date policy:
Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName LastAccessTimeWithSyncAndTieringOrderV2

# Find the heat store information for a particular file:
Get-StorageSyncHeatStoreInformation -FilePath '<PathToSpecificFile>'
```

> [!IMPORTANT]
> Az utolsó hozzáférésű időbélyegző nem az NTFS által követett tulajdonság, ezért a Fájlkezelőben alapértelmezés szerint nem látható. Ne használja a fájl utolsó módosításának időbélyegét annak vizsgálatához, hogy a szabályzat a várt módon működik-e. Ez az időbélyegző csak az írásokat követi nyomon, az olvasásokat nem. Használja a következő parancsmagot a kiértékeléshez legutóbb használt időbélyeg beszerzéséhez.

> [!WARNING]
> Ne kapcsolja be az NTFS-funkciót a fájlok és mappák utolsó hozzáférésének nyomon követéséhez. Ez a funkció alapértelmezés szerint ki van kapcsolva, mert nagy hatással van a teljesítményre. A Azure File Sync automatikusan és nagyon hatékonyan nyomon követheti a legutóbbi hozzáférési időt, és nem használja ezt az NTFS-szolgáltatást.

Ne feledje, hogy a kötet szabad területének házirendje mindig elsőbbséget élvez, és ha nincs elég szabad terület a köteten, hogy annyi napig érdemes megőrizni a fájlokat, ahogy azt a dátum házirend írja le, Azure File Sync továbbra is a leghidegebb fájlokat fogja megtartani, amíg a kötet szabad területének százalékos értéke nem teljesül.

Tegyük fel például, hogy egy 60 napos dátum-alapú rétegbeli szabályzattal és 20%-os mennyiségű szabad területtel rendelkezik. Ha a dátum házirend alkalmazása után a szabad terület kevesebb, mint 20%-a a köteten van, akkor a kötet szabad területére vonatkozó házirend bekerül és felülbírálja a dátum házirendet. Ennek eredményeképpen a rendszer több fájlt is felhasznál, így a kiszolgálón tárolt adatok mennyisége 60 nap és 45 nap között csökkenthető. Ezzel a szabályzattal ellentétben az időtartományon kívül eső fájlok rétegeire is kényszeríthető, még akkor is, ha még nem találta meg a szabad terület küszöbértékét – tehát a 61 napos fájlok akkor is lesznek feldolgozva, ha a kötet üres.

<a id="volume-free-space-guidelines"></a>
### <a name="how-do-i-determine-the-appropriate-amount-of-volume-free-space"></a>Hogyan határozhatom meg a köteten található szabad hely megfelelő méretét?
A helyi adatok mennyiségét néhány tényező határozza meg: a sávszélesség, az adatkészlet hozzáférési mintája és a költségkeret. Ha alacsony sávszélességű kapcsolattal rendelkezik, érdemes megtartania a helyi adatait, hogy a felhasználók minimális késéssel rendelkezzenek. Ellenkező esetben egy adott időszakon belül elvégezheti azt a kiindulási arányban. Ha például tudja, hogy az 1 TB-os adatkészlet 10%-a megváltozik, vagy minden hónapban aktívan hozzáfér, akkor érdemes megtartania a 100 GB-ot a helyi számítógépen, hogy ne kelljen gyakran újrahívni a fájlokat. Ha a kötet 2TB, akkor 5%-ot (vagy 100 GB-ot) kell megtartania, ami azt jelenti, hogy a fennmaradó 95% a kötet szabad területének százalékos aránya. Azt javasoljuk azonban, hogy adjon hozzá egy puffert a nagyobb adatváltozási időszakok esetében, azaz a mennyiségtől kezdve a szabad lemezterület százalékos arányát, majd később szükség esetén módosítsa. 

A további adatok helyi megtartása csökkenti a kimenő forgalom költségeit, mivel az Azure-ból kevesebb fájl lesz meghívva, de emellett nagyobb mennyiségű helyszíni tárterület fenntartására is van szükség, amely a saját költségén alapul. Ha Azure File Sync központilag telepítette a példányát, megtekintheti a Storage-fiók kimenő adatait, hogy nagyjából felmérje, hogy a kötet szabad területének beállításai megfelelőek-e a használathoz. Feltételezve, hogy a Storage-fiók csak a Azure File Sync Felhőbeli végpontját (azaz a szinkronizálási megosztást) tartalmazza, a magas kimenő forgalom pedig azt jelenti, hogy a felhőből sok fájl visszahívásra kerül, és érdemes megfontolni a helyi gyorsítótár növelését.

<a id="how-long-until-my-files-tier"></a>
### <a name="ive-added-a-new-server-endpoint-how-long-until-my-files-on-this-server-tier"></a>Új kiszolgálói végpontot adott hozzá. Mennyi ideig tartanak a fájlok a kiszolgálói szinten?

Azt határozza meg, hogy a fájlokat a beállított szabályzatok alapján kell-e kiértékelni, óránként egyszer. Új kiszolgálói végpont létrehozásakor két helyzet fordulhat elő:

1. Amikor új kiszolgálói végpontot ad hozzá, az adott kiszolgáló helyén gyakran előfordulnak fájlok. Először fel kell tölteni őket, mielőtt megkezdené a Felhőbeli rétegek megkezdését. A kötet szabad területének házirendje nem kezdi meg a munkáját, amíg az összes fájl kezdeti feltöltését befejezte. A választható dátumra vonatkozó házirend azonban egy adott fájl alapján fog működni, amint a fájl feltöltése megtörtént. Az egyórás időköz itt is érvényes. 
2. Új kiszolgálói végpont hozzáadásakor előfordulhat, hogy egy üres kiszolgáló helyét egy Azure-fájlmegosztás számára az adataival kapcsolja össze. Akár egy második kiszolgáló, akár vész-helyreállítási helyzetben van. Ha úgy dönt, hogy letölti a névteret, és felidézi a tartalmat a kiszolgáló kezdeti letöltése során, akkor a névtér leállása után a rendszer visszahívja a fájlokat az utolsó módosítás időbélyegzője alapján. Csak annyi fájl lesz meghívva, amely a kötet szabad területére vonatkozó házirendben és a választható dátum-házirendben is elfér.

<a id="is-my-file-tiered"></a>
### <a name="how-can-i-tell-whether-a-file-has-been-tiered"></a>Honnan tudhatom meg, hogy van-e lépcsőzetesen egy fájl?
Több módon is ellenőrizhető, hogy a fájl az Azure-fájlmegosztás szintjére lett-e osztva:
    
   *  **Keresse meg a fájl attribútumait a fájlban.**
     Kattintson a jobb gombbal egy fájlra, lépjen a **részletek**menüpontra, majd görgessen le az **attribútumok** tulajdonsághoz. A rétegű fájlok a következő attribútumokkal vannak beállítva:     
        
        | Attribútum betűjele | Attribútum | Definíció |
        |:----------------:|-----------|------------|
        | A | Archívum | Azt jelzi, hogy a fájlt biztonsági mentési szoftverrel kell biztonsági másolatot készíteni. Ez az attribútum mindig be van állítva, függetlenül attól, hogy a fájl többszintes vagy teljes mértékben a lemezen van-e tárolva. |
        | P | Ritka fájl | Azt jelzi, hogy a fájl ritka fájl. A ritka fájlok olyan speciális fájltípusok, amelyeket az NTFS biztosít a hatékony használatra, ha a lemezen lévő fájl többnyire üres. A Azure File Sync ritka fájlokat használ, mivel a fájlok teljes mértékben, vagy részben visszahívásra kerülnek. A teljes mértékben többrétegű fájlokban a fájl stream a felhőben tárolódik. Egy részben visszanevezett fájlban a fájl egy része már lemezen van. Ha egy fájl teljesen visszahívásra kerül a lemezre, Azure File Sync átalakítja egy ritka fájlból egy normál fájlba. Ez az attribútum csak a Windows Server 2016-es és régebbi verzióra van beállítva.|
        | M | Visszahívás az adateléréssel kapcsolatban | Azt jelzi, hogy a fájl nem teljes mértékben jelen van a helyi tárolóban. A fájl olvasása esetén a fájl legalább egy olyan Azure-fájlmegosztás lekérését eredményezi, amelyhez a kiszolgálói végpont csatlakozik. Ez az attribútum csak a Windows Server 2019 rendszerre van beállítva. |
        | L | Újraelemzési pont | Azt jelzi, hogy a fájl újraelemzési ponttal rendelkezik. Az újraelemzési pont egy speciális mutató, amely egy fájlrendszer-szűrő általi használatra szolgál. A Azure File Sync újraelemzési pontokat használ a Azure File Sync fájlrendszer-szűrő (StorageSync.sys) számára a fájl tárolására szolgáló Felhőbeli hely meghatározásához. Ez támogatja a zökkenőmentes hozzáférést. A felhasználóknak nem kell tudniuk, hogy a Azure File Sync használatban van, vagy hogyan érhetik el az Azure-fájlmegosztás fájlját. Ha egy fájl teljesen visszahívásra kerül, Azure File Sync eltávolítja az újraelemzési pontot a fájlból. |
        | O | Offline | Azt jelzi, hogy a fájl tartalma nem tárolódik a lemezen. Ha egy fájl teljesen visszahívásra kerül, Azure File Sync eltávolítja ezt az attribútumot. |

        ![A fájlhoz tartozó Tulajdonságok párbeszédpanel, ahol a Részletek lap van kijelölve](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        A mappában található összes fájl attribútumait úgy tekintheti meg, hogy hozzáadja az **attribútumok** mezőt a fájlkezelő tábla megjelenítéséhez. Ehhez kattintson a jobb gombbal egy meglévő oszlopra (például **méret**), válassza a **továbbiak**lehetőséget, majd válassza az **attribútumok** elemet a legördülő listából.
        
   * **`fsutil`Egy fájl újraelemzési pontjainak keresésére használható.**
       Az előző beállításban leírtaknak megfelelően a többplatformos fájlok esetében mindig van egy újraelemzési pont beállítása. Az újraelemzési mutató egy speciális mutató a Azure File Sync fájlrendszer-szűrőhöz (StorageSync.sys). Annak ellenőrzéséhez, hogy egy fájl újraelemzési ponttal rendelkezik-e, futtassa a következő parancsot egy rendszergazda jogú parancssorban vagy PowerShell-ablakban `fsutil` :
    
        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```

        Ha a fájl újraelemzési ponttal rendelkezik, várhatóan megtekintheti az **újraelemzési címke értékét: 0x8000001e**. Ez a hexadecimális érték a Azure File Sync tulajdonában lévő újraelemzési pont értéke. A kimenet tartalmazza azokat az újraelemzési adatokat is, amelyek az Azure-fájlmegosztás fájljának elérési útját jelölik.

        > [!WARNING]  
        > A `fsutil reparsepoint` segédprogram-parancs szintén képes törölni egy újraelemzési pontot. Ne hajtsa végre ezt a parancsot, kivéve, ha a Azure File Sync mérnöki csapat kéri. A parancs futtatása adatvesztést okozhat. 

<a id="afs-recall-file"></a>
### <a name="a-file-i-want-to-use-has-been-tiered-how-can-i-recall-the-file-to-disk-to-use-it-locally"></a>A használni kívánt fájlt a rendszer lépcsőzetesen felhasználta. Hogyan hívhatom fel a fájlt a lemezre helyileg való használatra?
A fájlok lemezre való felidézésének legegyszerűbb módja a fájl megnyitása. A Azure File Sync fájlrendszer-szűrő (StorageSync.sys) zökkenőmentesen letölti a fájlt az Azure-fájlmegosztás alapján, anélkül, hogy az Ön részéről munkát kellene elosztania. A részben beolvasható fájltípusok, például a multimédia vagy a. zip fájlok esetében a fájl megnyitása nem tölti le a teljes fájlt.

A PowerShell használatával is kényszerítheti a fájlok visszahívását. Ez a beállítás akkor lehet hasznos, ha egyszerre több fájlt szeretne felidézni, például egy mappa összes fájlját. Nyisson meg egy PowerShell-munkamenetet azon a kiszolgáló-csomóponton, ahol a Azure File Sync telepítve van, majd futtassa a következő PowerShell-parancsokat:
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
Választható paraméterek:
* `-Order CloudTieringPolicy` először a legutóbb módosított vagy elért fájlokat fogja felidézni, és az aktuális rétegű szabályzat engedélyezi. 
    * Ha a kötet szabad területére vonatkozó házirend be van állítva, a rendszer visszahívja a fájlokat, amíg el nem éri a kötet szabad területére vonatkozó házirend-beállítást. Ha például a kötet szabad házirend-beállítása 20%, akkor a visszahívás leáll, ha a kötet szabad területe eléri a 20%-ot.  
    * Ha a kötet szabad területe és a dátum házirendje konfigurálva van, a rendszer visszahívja a fájlokat, amíg el nem éri a kötet szabad területét vagy a dátum házirend-beállítását. Ha például a kötet szabad házirend-beállítása 20%, a dátum pedig 7 nap, a visszahívás leáll, ha a kötet szabad területe eléri a 20%-ot, vagy a 7 napon belül elért vagy módosított összes fájl helyi.
* `-ThreadCount` meghatározza, hogy hány fájlt lehet visszahívni párhuzamosan.
* `-PerFileRetryCount`meghatározza, hogy a rendszer milyen gyakran próbálkozzon a visszahívással egy jelenleg blokkolt fájlon.
* `-PerFileRetryDelaySeconds`meghatározza azt az időtartamot másodpercben, ameddig a rendszer újrahívja az újrapróbálkozási kísérleteket, és mindig az előző paraméterrel együtt kell használni őket.

Példa:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -ThreadCount 8 -Order CloudTieringPolicy -PerFileRetryCount 3 -PerFileRetryDelaySeconds 10
``` 

> [!Note]  
> - A meghívó-StorageSyncFileRecall parancsmag a fájlok letöltési teljesítményének javítására is használható, amikor új kiszolgálói végpontot ad hozzá egy meglévő szinkronizálási csoporthoz.  
>- Ha a kiszolgálót üzemeltető helyi köteten nincs elég szabad hely az összes rétegű adat felidézéséhez, a `Invoke-StorageSyncFileRecall` parancsmag meghiúsul.  

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>Miért nem egyezik *meg* a fájl mérete a *méret tulajdonsággal* a Azure file Sync használata után? 
A Windows fájlkezelő két tulajdonságot tesz elérhetővé a fájl méretének, a **méretnek és** **a méretnek a lemezen**való megjelenítéséhez. Ezek a tulajdonságok finoman különböznek a jelentésekben. A **méret** a fájl teljes méretét jelöli. A **lemez mérete** a lemezen tárolt fájl adatfolyamának méretét jelöli. Ezeknek a tulajdonságoknak az értékei különböző okoktól különbözőek lehetnek, például a tömörítés, az adatok deduplikálása vagy a Felhőbeli rétegek Azure File Sync használatával. Ha egy fájl egy Azure-fájlmegosztás keretén belül van, a lemez mérete nulla, mivel a fájl streamet az Azure-fájlmegosztás tárolja, és nem a lemezen. A fájlok részlegesen is elhelyezhetők (vagy részben visszahívható). Egy részben rétegű fájlban a fájl egy része a lemezen található. Ez akkor fordulhat elő, ha a fájlok részben olvashatók az olyan alkalmazások, mint például a multimédia lejátszók vagy a zip-segédprogramok. 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>Hogyan egy fájl vagy könyvtár lépcsőzetes kikényszerítését?

> [!NOTE]
> Ha kijelöl egy lépcsőzetesen megjelenő könyvtárat, csak a címtárban jelenleg található fájlok vannak kiválasztva. Az ezen időpont után létrehozott fájlok nem kerülnek automatikusan lépcsőzetesen.

Ha engedélyezve van a felhő-előállítási funkció, a Felhőbeli rétegek automatikusan a legutóbbi hozzáférés és a módosítási idő alapján, a Felhőbeli végponton megadott mennyiségű szabad terület százalékos arányának eléréséhez. Időnként előfordulhat azonban, hogy manuálisan szeretné kényszeríteni a fájl a szintet. Ez akkor lehet hasznos, ha olyan nagyméretű fájlt ment, amelyet hosszú ideje nem kíván újra használni, és azt szeretné, hogy a köteten lévő szabad terület más fájlokhoz és mappákhoz is használható legyen. Az alábbi PowerShell-parancsokkal kényszerítheti a rétegek használatát:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

<a id="afs-image-thumbnail"></a>
### <a name="why-are-my-tiered-files-not-showing-thumbnails-or-previews-in-windows-explorer"></a>A Windows Intézőben miért nem láthatók miniatűr vagy előzetes verziók a többrétegű fájlok számára?
A többhelyes fájlok esetében a miniatűrök és az előzetes verziók nem láthatók a kiszolgálói végponton. Ez a viselkedés várható, mivel a Windows miniatűr gyorsítótár funkciója szándékosan kihagyja a fájlok olvasását az offline attribútummal. Ha engedélyezve van a felhőalapú rétegek beolvasása, a többoldalas fájlok olvasásával le lehet tölteni őket (visszahívásra).

Ez a viselkedés nem jellemző a Azure File Syncre, a Windows Intéző megjeleníti a "szürke X" értéket minden olyan fájlnál, amelynél az offline attribútum be van állítva. Az X ikon jelenik meg, amikor SMB-kapcsolaton keresztül fér hozzá a fájlokhoz. A viselkedés részletes ismertetését a következő témakörben találja: [https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105](https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105)

<a id="afs-tiering-disabled"></a>
### <a name="i-have-cloud-tiering-disabled-why-are-there-tiered-files-in-the-server-endpoint-location"></a>Letiltottam a Felhőbeli letiltást, miért vannak a kiszolgálói végpontok helyen található, lépcsőzetes fájlok?

A kiszolgálói végpont helyének két oka lehet a többrétegű fájlok:

- Amikor új kiszolgálói végpontot ad hozzá egy meglévő szinkronizálási csoporthoz, a rendszer először szinkronizálja a metaadatokat a-kiszolgálóval, majd a háttérben letölti a fájlokat a kiszolgálóra. A fájlok a rétegek szerint lesznek feldolgozva, amíg helyileg nem töltik le őket. Ha egy új kiszolgáló szinkronizálási csoportba való felvételekor szeretné javítani a fájl letöltésének teljesítményét, használja a [Meghívási-StorageSyncFileRecall](storage-sync-cloud-tiering.md#afs-recall-file) parancsmagot.

- Ha a felhő-rétegek engedélyezve lettek a kiszolgálói végponton, majd letiltották, akkor a fájlok addig lesznek feldolgozva, amíg el nem éri őket.


## <a name="next-steps"></a>Következő lépések
* [Azure File Sync központi telepítésének tervezése](storage-sync-files-planning.md)
