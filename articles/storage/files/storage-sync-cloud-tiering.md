---
title: A Azure File Sync Cloud-rétegek ismertetése | Microsoft Docs
description: Tudnivalók a Azure File Sync funkcióinak felhős szintjéről
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: fea9cebc5199fc7c1fc5c081aa45f08044c21e44
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268092"
---
# <a name="cloud-tiering-overview"></a>A felhőalapú rétegek áttekintése
A felhőalapú rétegek a Azure File Sync választható funkciója, amelyekben a gyakran használt fájlok a kiszolgálón helyileg vannak gyorsítótárazva, míg az összes többi fájl a házirend-beállítások alapján Azure Files. Egy fájl többszintű kiválasztásakor a Azure File Sync fájlrendszer-szűrő (StorageSync. sys) a fájlt helyileg váltja fel egy mutatóval vagy újraelemzési ponttal. Az újraelemzési pont a fájl URL-címét jelöli Azure Files. A többrétegű fájlok "offline" attribútummal és az NTFS fájlrendszerrel beállított FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS attribútummal is rendelkeznek, így a harmadik féltől származó alkalmazások biztonságosan azonosíthatják a többrétegű fájlokat.
 
Amikor egy felhasználó megnyit egy rétegű fájlt, Azure File Sync zökkenőmentesen visszahívja a fájl adatait a Azure Files anélkül, hogy a felhasználónak tudniuk kell róla, hogy a fájlt az Azure tárolja. 
 
 > [!Important]  
 > A felhő-rétegek nem támogatottak a Windows rendszerköteteken található kiszolgálói végpontok esetében, és csak a 64 KiB-nál nagyobb méretű fájlok állíthatók be Azure Files.
    
A Azure File Sync nem támogatja a 64 KiB-nál kisebb méretű fájlok használatát, mivel a rétegek és az ilyen kisméretű fájlok visszahívásának teljesítménye meghaladja a lemezterület megtakarítását.

 > [!Important]  
 > A lépcsőzetesen beállított fájlok felidézéséhez a hálózati sávszélességnek legalább 1 MB/s-nak kell lennie. Ha a hálózati sávszélesség kevesebb, mint 1 Mbps, előfordulhat, hogy a fájlok időtúllépési hiba miatt nem tudnak felidézni.

## <a name="cloud-tiering-faq"></a>Felhőalapú rétegek – gyakori kérdések

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>Hogyan működik a felhőalapú rétegek működése?
A Azure File Sync rendszerszűrő minden kiszolgálói végponton létrehoz egy "hő"-t a névtérből. A szolgáltatás az idő múlásával figyeli a hozzáféréseket (olvasási és írási műveleteket), majd a hozzáférés gyakorisága és recency alapján minden fájlhoz hozzárendel egy hő pontszámot. A legutóbb megnyitott fájl gyakran megtekinthető állapotba kerül, míg egy kis ideig nem fér hozzá a fájlhoz. Ha a kiszolgálón lévő fájl mennyisége meghaladja a szabad területhez beállított mennyiség küszöbértékét, akkor a rendszer a leghidegebb fájlokat Azure Files, amíg a szabad terület százalékos értéke nem teljesül.

A Azure File Sync ügynök 4,0-es és újabb verzióiban megadhatja a dátumra vonatkozó házirendet minden olyan kiszolgálói végponton, amely a megadott számú napon belül nem hozzáférő vagy módosított fájlokat tartalmaz.

<a id="afs-volume-free-space"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work"></a>Hogyan működik a kötet szabad területének rétegű házirendje?
A kötet szabad területe az a szabad terület, amelyet le szeretne foglalni azon a köteten, amelyen a kiszolgálói végpont található. Ha például a kötet szabad területe 20%-ra van állítva egy olyan köteten, amely egy kiszolgáló-végponttal rendelkezik, a legutóbb elért fájlok a lemezterület 80%-ában lesznek elfoglalva, az összes többi olyan fájllal, amely nem fér el az Azure-ba. A kötet szabad területe a kötet szintjén érvényes, nem pedig az egyes címtárak vagy szinkronizálási csoportok szintjére. 

<a id="volume-free-space-fastdr"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work-with-regards-to-new-server-endpoints"></a>Hogyan működik a kötet szabad területének rétegeinek szabályzata az új kiszolgálói végpontok tekintetében?
Ha egy kiszolgálói végpontot újonnan létesítettek és csatlakoztatnak egy Azure-fájlmegosztást, akkor a kiszolgáló először lekéri a névteret, majd lekéri a tényleges fájlokat, amíg eléri a kötet szabad területének küszöbértékét. Ezt a folyamatot a gyors katasztrófa-helyreállítás vagy a névtér gyors visszaállítása is nevezik.

<a id="afs-effective-vfs"></a>
### <a name="how-is-volume-free-space-interpreted-when-i-have-multiple-server-endpoints-on-a-volume"></a>Hogyan történik a kötetek szabad területének értelmezése, ha több kiszolgálói végpontom van egy köteten?
Ha egy köteten egynél több kiszolgálói végpont található, akkor a köteten lévő összes kiszolgálói végponton megadott legnagyobb szabad terület nagysága az adott köteten lévő összes kiszolgáló-végponton. A fájlok a használati minták alapján lesznek feldolgozva, függetlenül attól, hogy melyik kiszolgálói végponthoz tartoznak. Ha például két kiszolgáló-végponttal rendelkezik egy köteten, a Endpoint1 és a Endpoint2, ahol a Endpoint1 a kötet szabad területének küszöbértéke 25%, a Endpoint2 pedig 50%-os szabad területtel rendelkezik, akkor a kiszolgálói végpontok esetében a kötet szabad területének küszöbértéke 50% lesz. 

<a id="date-tiering-policy"></a>
### <a name="how-does-the-date-tiering-policy-work-in-conjunction-with-the-volume-free-space-tiering-policy"></a>Hogyan működik a dátum-előtételi szabályzat a kötet szabad területére vonatkozó adatmennyiségi szabályzattal együtt? 
Ha egy kiszolgálói végponton engedélyezi a felhő-rétegek bekapcsolását, beállíthatja a kötet szabad területére vonatkozó házirendet. Mindig elsőbbséget élvez minden más szabályzattal szemben, beleértve a dátumra vonatkozó házirendet is. Lehetőség van arra is, hogy minden egyes kiszolgálói végponthoz engedélyezheti a dátumra vonatkozó szabályzatot, ami azt jelenti, hogy csak a megadott (azaz a (z) és a (z), a jelen szabályzatban ismertetett fájlok jelennek meg a házirendben, és az elavult fájlokkal együtt, az összes elavult fájllal. Ne feledje, hogy a kötet szabad területének házirendje mindig elsőbbséget élvez, és ha nincs elég szabad terület a köteten, hogy annyi napig érdemes megőrizni a fájlokat, ahogy azt a dátum házirend írja le, a Azure File Sync továbbra is a leghidegebb fájlokat fogja használni, amíg a kötet nem szabad a terület százalékos aránya teljesül.

Tegyük fel például, hogy egy 60 napos dátum-alapú rétegbeli szabályzattal és 20%-os mennyiségű szabad területtel rendelkezik. Ha a dátum házirend alkalmazása után a szabad terület kevesebb, mint 20%-a a köteten van, akkor a kötet szabad területére vonatkozó házirend bekerül és felülbírálja a dátum házirendet. Ennek eredményeképpen a rendszer több fájlt is felhasznál, így a kiszolgálón tárolt adatok mennyisége 60 nap és 45 nap között csökkenthető. Ezzel a szabályzattal ellentétben az időtartományon kívül eső fájlok rétegeire is kényszeríthető, még akkor is, ha még nem találta meg a szabad terület küszöbértékét – tehát a 61 napos fájlok akkor is lesznek feldolgozva, ha a kötet üres.

<a id="volume-free-space-guidelines"></a>
### <a name="how-do-i-determine-the-appropriate-amount-of-volume-free-space"></a>Hogyan határozza meg a kötet szabad területének megfelelő mennyiségét?
A helyi adatok mennyiségét néhány tényező határozza meg: a sávszélesség, az adatkészlet hozzáférési mintája és a költségkeret. Ha alacsony sávszélességű kapcsolattal rendelkezik, érdemes megtartania a helyi adatait, hogy a felhasználók minimális késéssel rendelkezzenek. Ellenkező esetben egy adott időszakon belül elvégezheti azt a kiindulási arányban. Ha például tudja, hogy az 1 TB-os adatkészlet 10%-a megváltozik, vagy minden hónapban aktívan hozzáfér, akkor érdemes megtartania a 100 GB-ot a helyi számítógépen, hogy ne kelljen gyakran újrahívni a fájlokat. Ha a kötet 2TB, akkor 5%-ot (vagy 100 GB-ot) kell megtartania, ami azt jelenti, hogy a fennmaradó 95% a kötet szabad területének százalékos aránya. Azt javasoljuk azonban, hogy adjon hozzá egy puffert a nagyobb adatváltozási időszakok esetében, azaz a mennyiségtől kezdve a szabad lemezterület százalékos arányát, majd később szükség esetén módosítsa. 

A további adatok helyi megtartása csökkenti a kimenő forgalom költségeit, mivel az Azure-ból kevesebb fájl lesz meghívva, de emellett nagyobb mennyiségű helyszíni tárterület fenntartására is van szükség, amely a saját költségén alapul. Ha Azure File Sync központilag telepítette a példányát, megtekintheti a Storage-fiók kimenő adatait, hogy nagyjából felmérje, hogy a kötet szabad területének beállításai megfelelőek-e a használathoz. Feltételezve, hogy a Storage-fiók csak a Azure File Sync Felhőbeli végpontját (azaz a szinkronizálási megosztást) tartalmazza, a magas kimenő forgalom pedig azt jelenti, hogy a felhőből sok fájl visszahívásra kerül, és érdemes megfontolni a helyi gyorsítótár növelését.

<a id="how-long-until-my-files-tier"></a>
### <a name="ive-added-a-new-server-endpoint-how-long-until-my-files-on-this-server-tier"></a>Új kiszolgálói végpontot adott hozzá. Mennyi ideig tartanak a fájlok a kiszolgálói szinten?
A Azure File Sync ügynök 4,0-es és újabb verzióiban a fájlok Azure-fájlmegosztásba való feltöltése után a rendszer a szabályzatok alapján a következő, az óránként egyszer megjelenő munkamenet-futtatási időpontokat fogja felosztani. A régebbi ügynökök esetében a rétegek akár 24 órát is igénybe vehetnek.

<a id="is-my-file-tiered"></a>
### <a name="how-can-i-tell-whether-a-file-has-been-tiered"></a>Honnan tudhatom meg, hogy van-e lépcsőzetesen egy fájl?
Több módon is ellenőrizhető, hogy a fájl az Azure-fájlmegosztás szintjére lett-e osztva:
    
   *  **Keresse meg a fájl attribútumait a fájlban.**
     Kattintson a jobb gombbal egy fájlra, lépjen a **részletek**menüpontra, majd görgessen le az **attribútumok** tulajdonsághoz. A rétegű fájlok a következő attribútumokkal vannak beállítva:     
        
        | Attribútum betűjele | Attribútum | Meghatározás |
        |:----------------:|-----------|------------|
        | Az | Archívum | Azt jelzi, hogy a fájlt biztonsági mentési szoftverrel kell biztonsági másolatot készíteni. Ez az attribútum mindig be van állítva, függetlenül attól, hogy a fájl többszintes vagy teljes mértékben a lemezen van-e tárolva. |
        | P | Ritka fájl | Azt jelzi, hogy a fájl ritka fájl. A ritka fájlok olyan speciális fájltípusok, amelyeket az NTFS biztosít a hatékony használatra, ha a lemezen lévő fájl többnyire üres. A Azure File Sync ritka fájlokat használ, mivel a fájlok teljes mértékben, vagy részben visszahívásra kerülnek. A teljes mértékben többrétegű fájlokban a fájl stream a felhőben tárolódik. Egy részben visszanevezett fájlban a fájl egy része már lemezen van. Ha egy fájl teljesen visszahívásra kerül a lemezre, Azure File Sync átalakítja egy ritka fájlból egy normál fájlba. Ez az attribútum csak a Windows Server 2016-es és régebbi verzióra van beállítva.|
        | M | Visszahívás az adateléréssel kapcsolatban | Azt jelzi, hogy a fájl nem teljes mértékben jelen van a helyi tárolóban. A fájl olvasása esetén a fájl legalább egy olyan Azure-fájlmegosztás lekérését eredményezi, amelyhez a kiszolgálói végpont csatlakozik. Ez az attribútum csak a Windows Server 2019 rendszerre van beállítva. |
        | L | Újraelemzési pont | Azt jelzi, hogy a fájl újraelemzési ponttal rendelkezik. Az újraelemzési pont egy speciális mutató, amely egy fájlrendszer-szűrő általi használatra szolgál. A Azure File Sync újraelemzési pontokat használ a Azure File Sync fájlrendszer-szűrő (StorageSync. sys) megadásához a fájl tárolására szolgáló Felhőbeli helyen. Ez támogatja a zökkenőmentes hozzáférést. A felhasználóknak nem kell tudniuk, hogy a Azure File Sync használatban van, vagy hogyan érhetik el az Azure-fájlmegosztás fájlját. Ha egy fájl teljesen visszahívásra kerül, Azure File Sync eltávolítja az újraelemzési pontot a fájlból. |
        | O | Kapcsolat nélküli | Azt jelzi, hogy a fájl tartalma nem tárolódik a lemezen. Ha egy fájl teljesen visszahívásra kerül, Azure File Sync eltávolítja ezt az attribútumot. |

        ![A fájlhoz tartozó Tulajdonságok párbeszédpanel, ahol a Részletek lap van kijelölve](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        A mappában található összes fájl attribútumait úgy tekintheti meg, hogy hozzáadja az **attribútumok** mezőt a fájlkezelő tábla megjelenítéséhez. Ehhez kattintson a jobb gombbal egy meglévő oszlopra (például **méret**), válassza a **továbbiak**lehetőséget, majd válassza az **attribútumok** elemet a legördülő listából.
        
   * **Az újraelemzési pontok egy fájlon való ellenőrzéséhez használja a `fsutil`.**
       Az előző beállításban leírtaknak megfelelően a többplatformos fájlok esetében mindig van egy újraelemzési pont beállítása. Az újraelemzési mutató egy speciális mutató a Azure File Sync fájlrendszer-szűrőhöz (StorageSync. sys). Annak ellenőrzéséhez, hogy egy fájl rendelkezik-e újraelemzési ponttal, rendszergazda jogú parancssorban vagy PowerShell-ablakban futtassa a `fsutil` segédprogramot:
    
        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```

        Ha a fájl újraelemzési ponttal rendelkezik, várhatóan megtekintheti az **újraelemzési címke értékét: 0x8000001e**. Ez a hexadecimális érték a Azure File Sync tulajdonában lévő újraelemzési pont értéke. A kimenet tartalmazza azokat az újraelemzési adatokat is, amelyek az Azure-fájlmegosztás fájljának elérési útját jelölik.

        > [!WARNING]  
        > Az `fsutil reparsepoint` segédprogram-parancs is képes törölni egy újraelemzési pontot. Ne hajtsa végre ezt a parancsot, kivéve, ha a Azure File Sync mérnöki csapat kéri. A parancs futtatása adatvesztést okozhat. 

<a id="afs-recall-file"></a>

### <a name="a-file-i-want-to-use-has-been-tiered-how-can-i-recall-the-file-to-disk-to-use-it-locally"></a>A használni kívánt fájlt a rendszer lépcsőzetesen felhasználta. Hogyan hívhatom fel a fájlt a lemezre helyileg való használatra?
A fájlok lemezre való felidézésének legegyszerűbb módja a fájl megnyitása. A Azure File Sync fájlrendszer-szűrő (StorageSync. sys) zökkenőmentesen letölti az Azure-fájlmegosztás fájlját anélkül, hogy az Ön részéről munkát kellene elosztania. A részben beolvasható fájltípusok, például a multimédia vagy a. zip fájlok esetében a fájl megnyitása nem tölti le a teljes fájlt.

A PowerShell használatával is kényszerítheti a fájlok visszahívását. Ez a beállítás akkor lehet hasznos, ha egyszerre több fájlt szeretne felidézni, például egy mappa összes fájlját. Nyisson meg egy PowerShell-munkamenetet azon a kiszolgáló-csomóponton, ahol a Azure File Sync telepítve van, majd futtassa a következő PowerShell-parancsokat:
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
Választható paraméterek:
* a `-Order CloudTieringPolicy` először a legutóbb módosított fájlokat fogja felidézni.  
* `-ThreadCount` meghatározza, hogy hány fájlt lehet visszahívni párhuzamosan.
* `-PerFileRetryCount`meghatározza, hogy a rendszer milyen gyakran próbálkozzon a visszahívással egy jelenleg blokkolt fájlon.
* `-PerFileRetryDelaySeconds`meghatározza azt az időt másodpercben, ameddig a rendszer újrahívja az újrapróbálkozási kísérleteket, és mindig az előző paraméterrel együtt kell használni őket.

> [!Note]  
> Ha a kiszolgálót futtató helyi köteten nincs elég szabad hely az összes rétegű adat felidézéséhez, akkor a `Invoke-StorageSyncFileRecall` parancsmag sikertelen lesz.  

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>Miért nem egyezik *meg* a fájl mérete a *méret tulajdonsággal* a Azure file Sync használata után? 
A Windows fájlkezelő két tulajdonságot tesz elérhetővé a fájl méretének, a **méretnek és** **a méretnek a lemezen**való megjelenítéséhez. Ezek a tulajdonságok finoman különböznek a jelentésekben. A **méret** a fájl teljes méretét jelöli. A **lemez mérete** a lemezen tárolt fájl adatfolyamának méretét jelöli. Ezeknek a tulajdonságoknak az értékei különböző okoktól különbözőek lehetnek, például a tömörítés, az adatok deduplikálása vagy a Felhőbeli rétegek Azure File Sync használatával. Ha egy fájl egy Azure-fájlmegosztás keretén belül van, a lemez mérete nulla, mivel a fájl streamet az Azure-fájlmegosztás tárolja, és nem a lemezen. A fájlok részlegesen is elhelyezhetők (vagy részben visszahívható). Egy részben rétegű fájlban a fájl egy része a lemezen található. Ez akkor fordulhat elő, ha a fájlok részben olvashatók az olyan alkalmazások, mint például a multimédia lejátszók vagy a zip-segédprogramok. 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>Hogyan egy fájl vagy könyvtár lépcsőzetes kikényszerítését?
Ha engedélyezve van a felhő-előállítási funkció, a Felhőbeli rétegek automatikusan a legutóbbi hozzáférés és a módosítási idő alapján, a Felhőbeli végponton megadott mennyiségű szabad terület százalékos arányának eléréséhez. Időnként előfordulhat azonban, hogy manuálisan szeretné kényszeríteni a fájl a szintet. Ez akkor lehet hasznos, ha olyan nagyméretű fájlt ment, amelyet hosszú ideje nem kíván újra használni, és azt szeretné, hogy a köteten lévő szabad terület más fájlokhoz és mappákhoz is használható legyen. Az alábbi PowerShell-parancsokkal kényszerítheti a rétegek használatát:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

<a id="afs-image-thumbnail"></a>
### <a name="why-are-my-tiered-files-not-showing-thumbnails-or-previews-in-windows-explorer"></a>A Windows Intézőben miért nem láthatók miniatűr vagy előzetes verziók a többrétegű fájlok számára?
A többhelyes fájlok esetében a miniatűrök és az előzetes verziók nem láthatók a kiszolgálói végponton. Ez a viselkedés várható, mivel a Windows miniatűr gyorsítótár funkciója szándékosan kihagyja a fájlok olvasását az offline attribútummal. Ha engedélyezve van a felhőalapú rétegek beolvasása, a többoldalas fájlok olvasásával le lehet tölteni őket (visszahívásra).

Ez a viselkedés nem jellemző a Azure File Syncre, a Windows Intéző megjeleníti a "szürke X" értéket minden olyan fájlnál, amelynél az offline attribútum be van állítva. Az X ikon jelenik meg, amikor SMB-kapcsolaton keresztül fér hozzá a fájlokhoz. A viselkedés részletes ismertetését itt találja: [https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105](https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105)


## <a name="next-steps"></a>További lépések
* [Azure File Sync központi telepítésének tervezése](storage-sync-files-planning.md)
