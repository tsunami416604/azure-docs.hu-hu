---
title: Az Azure File Sync cloud rétegezése | Microsoft dokumentumok
description: További információ az Azure File Sync felhőalapú rétegezési funkciójáról
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 11f9097fc4875f0a4300ac56dafe7af9a0b00c97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454618"
---
# <a name="cloud-tiering-overview"></a>Felhőrétegezés – áttekintés
A felhőrétegezés az Azure File Sync opcionális szolgáltatása, amelyben a gyakran használt fájlok helyileg vannak gyorsítótárazva a kiszolgálón, míg az összes többi fájl rétegzett az Azure Files-ba a házirend-beállítások alapján. Ha egy fájl rétegzett, az Azure File Sync fájlrendszer szűrő (StorageSync.sys) lecseréli a fájlt helyileg egy mutatót, vagy újraelemzési pont. Az újraelemzési pont a fájl URL-címét jelöli az Azure Files-ban. A rétegzett fájlok rendelkeznek az "offline" attribútummal és az NTFS fájlrendszerben beállított FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS attribútummal, így a harmadik féltől származó alkalmazások biztonságosan azonosíthatják a rétegzett fájlokat.
 
Amikor egy felhasználó megnyit egy rétegzett fájlt, az Azure File Sync zökkenőmentesen visszahívja a fájladatokat az Azure Files-ból anélkül, hogy a felhasználónak tudnia kellene, hogy a fájl az Azure-ban van tárolva. 
 
 > [!Important]  
 > A felhőrétegezés nem támogatott a Windows rendszerkötetén.
    
 > [!Important]  
 > A rétegzett fájlok visszahívásához a hálózati sávszélességnek legalább 1 Mb/s-nak kell lennie. Ha a hálózati sávszélesség kisebb, mint 1 Mb/s, előfordulhat, hogy a fájlok időkihagyási hibával nem tudnak visszahívni.

## <a name="cloud-tiering-faq"></a>Gyakori kérdések a felhőrétegezésről

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>Hogyan működik a felhőrétegezés?
Az Azure File Sync rendszerszűrő minden kiszolgálóvégponton létrehoz egy "hőtérképet" a névtérből. Ez figyeli a hozzáférések (olvasási és írási műveletek) idővel, majd alapján mind a gyakorisága és recency hozzáférés, hozzárendeli a hőpontszám minden fájlt. A gyakran használt fájl, amely a közelmúltban nyitotta meg kell tekinteni forró, míg a fájl, amely alig érintette, és nem érhető el egy ideje lesz tekinthető hűvös. Ha a kiszolgálón lévő fájlkötet túllépi a beállított kötetszabad terület küszöbértéket, a leghűvösebb fájlokat rétegezi az Azure Files-ba, amíg a szabad terület százaléka el nem teljesül.

Az Azure File Sync ügynök 4.0-s és újabb verzióiban megadhatja továbbá a dátumházirendet minden kiszolgálóvégponton, amely meghatározott számú napon belül rétegezi a nem elért vagy módosított fájlokat.

<a id="tiering-minimum-file-size"></a>
### <a name="what-is-the-minimum-file-size-for-a-file-to-tier"></a>Mekkora a fájlréteg minimális fájlmérete?
A 9.x-es és újabb ügynökverziók esetében a fájl-réteg minimális fájlmérete a fájlrendszer fürtjének méretétől függ (a fájlrendszer fürtméretének kétszerese). Ha például az NTFS fájlrendszer fürtjének mérete 4 KB, akkor a fájl és a szint minimális fájlmérete 8 KB. A 8.x-es vagy régebbi ügynökverziók esetében a fájl és a szint minimális fájlmérete 64 KB.

<a id="afs-volume-free-space"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work"></a>Hogyan működik a köteten található szabad hely rétegzési szabályzata?
A kötetszabad terület az a szabad terület, amelyet azon a köteten szeretne lefoglalni, amelyen a kiszolgálóvégpont található. Ha például a kötet szabad területe 20%-ra van állítva egy olyan köteten, amely egy kiszolgálói végpontot tartalmaz, a kötetterület legfeljebb 80%-át a legutóbb elért fájlok foglalják el, és a fennmaradó fájlok, amelyek nem férnek bele ebbe a térbe az Azure-ig rétegezve. A kötetszabad terület a kötet szintjén érvényes, nem pedig az egyes könyvtárak vagy szinkronizálási csoportok szintjén. 

<a id="volume-free-space-fastdr"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work-with-regards-to-new-server-endpoints"></a>Hogyan működik a köteten található szabad hely rétegzési szabályzata az új kiszolgálóvégpontok szempontjából?
Amikor egy kiszolgálóvégpont újonnan ki van építve, és egy Azure-fájlmegosztáshoz csatlakozik, a kiszolgáló először lehúzza a névteret, majd lehúzza a tényleges fájlokat, amíg el nem éri a kötet szabad területküszöbértéket. Ezt a folyamatot gyors vészhelyreállításnak vagy gyors névtér-visszaállításnak is nevezik.

<a id="afs-effective-vfs"></a>
### <a name="how-is-volume-free-space-interpreted-when-i-have-multiple-server-endpoints-on-a-volume"></a>Hogyan értelmezi a rendszer a köteten található szabad hely mennyiségét, ha a kötetnek több kiszolgálói végpontja van?
Ha egy köteten egynél több kiszolgálóvégpont van, a kötet tényleges szabad területküszöbértéke a kötet bármely kiszolgálóvégpontján megadott legnagyobb kötetszabad terület. A fájlok rétegezése a használati minták szerint lesz, függetlenül attól, hogy melyik kiszolgálóvégponthoz tartoznak. Ha például két kiszolgálóvégpontvan egy köteten, az Endpoint1 és az Endpoint2, ahol az Endpoint1 kötetszabad terület küszöbértéke 25%, az Endpoint2 pedig 50%-os kötetszabad területküszöbértéktel rendelkezik, a kötetszabad terület küszöbértéke mindkét kiszolgálóvégpont esetében 50%. 

<a id="date-tiering-policy"></a>
### <a name="how-does-the-date-tiering-policy-work-in-conjunction-with-the-volume-free-space-tiering-policy"></a>Hogyan működik a dátum rétegzési szabályzata a köteten található szabad hely rétegzési szabályzatával együtt? 
Amikor engedélyezi a felhőrétegezést egy kiszolgálóvégponton, mennyiségi szabad területházirendet kell beállítania. Mindig elsőbbséget élvez minden más házirenddel szemben, beleértve a dátumházirendet is. Szükség esetén engedélyezheti a dátumházirendet az adott kötet en lévő minden kiszolgálóvégponthoz, ami azt jelenti, hogy csak a házirendben leírt napokon belül érhető el (azaz olvasott vagy írt) fájlok maradnak helyi szinten, a staler-fájlok rétegezve. Ne feledje, hogy a kötetszabad területházirend mindig elsőbbséget élvez, és ha nincs elég szabad hely a köteten ahhoz, hogy a dátumházirendben leírt számú fájl megőrződjön, az Azure File Sync folytatja a leghidegebb fájlok rétegezését, amíg a kötet ingyenes helyszázaléka teljesül.

Tegyük fel például, hogy 60 napos dátumalapú rétegezési házirenddel és 20%-os kötetszabad területházirenddel rendelkezik. Ha a dátumházirend alkalmazása után a köteten a szabad terület kevesebb mint 20%-a van, a kötetszabad terület házirend beindul, és felülbírálja a dátumházirendet. Ez azt eredményezi, hogy több fájl rétegzett lesz, így a kiszolgálón tárolt adatok mennyisége 60 napról 45 napra csökkenthető. Ezzel szemben ez a házirend akkor is kényszeríti az időtartományon kívül esik fájlok rétegezését, ha még akkor sem érte el a szabad terület küszöbértékét – így a 61 napos fájlok akkor is rétegzettek lesznek, ha a kötet üres.

<a id="volume-free-space-guidelines"></a>
### <a name="how-do-i-determine-the-appropriate-amount-of-volume-free-space"></a>Hogyan határozhatom meg a köteten található szabad hely megfelelő méretét?
A helyi szinten tartandó adatok mennyiségét néhány tényező határozza meg: a sávszélesség, az adatkészlet hozzáférési mintája és a költségkeret. Ha alacsony sávszélességű kapcsolattal rendelkezik, érdemes több adatot helyi szinten tartani, hogy a felhasználók számára minimális késés legyen. Ellenkező esetben egy adott időszakban a lemorzsolódási arányalapján alapjául számíthat. Ha például tudja, hogy az 1 TB-os adatkészlet körülbelül 10%-a megváltozik, vagy havonta aktívan hozzáfér, akkor érdemes lehet 100 GB-ot helyi szinten tartani, hogy ne hívja vissza gyakran a fájlokat. Ha a kötet 2 TB, akkor meg kell tartani 5% (vagy 100 GB) helyi, ami azt jelenti, a fennmaradó 95%, a kötet szabad terület százaléka. Azonban azt javasoljuk, hogy adjon hozzá egy puffert a magasabb lemorzsolódási időszakokhoz – más szóval, kezdve egy kisebb kötetszabad terület százalékával, majd szükség esetén később állítsa be. 

Több adat helyi szinten tartása azt jelenti, hogy alacsonyabb kimenő költségeket, mivel kevesebb fájlt kell visszahívni az Azure-ból, de azt is megköveteli, hogy nagyobb mennyiségű helyszíni tárhelyet tartson fenn, amely saját költségén érkezik. Miután telepítette az Azure File Sync egy példányát, megnézheti a tárfiók kimenő kijáratait, hogy nagyjából felmérhesse, hogy a kötetszabad terület beállításai megfelelnek-e a használatnak. Feltéve, hogy a tárfiók csak az Azure File Sync Cloud Endpoint (azaz a szinkronizálási megosztás), majd a magas kimenő forgalom azt jelenti, hogy sok fájl van visszahívva a felhőből, és érdemes növelni a helyi gyorsítótár.

<a id="how-long-until-my-files-tier"></a>
### <a name="ive-added-a-new-server-endpoint-how-long-until-my-files-on-this-server-tier"></a>Új kiszolgálóvégpontot adtam hozzá. Mennyi idő múlva a fájlokat ezen a kiszolgálón szinten?
Az Azure File Sync ügynök 4.0-s és újabb verzióiban, miután a fájlokat feltöltötte az Azure-fájlmegosztásba, a rendszer tiered a szabályzatok szerint, amint a következő rétegezési munkamenet fut, ami óránként egyszer történik. Régebbi ügynököknél a rétegezés akár 24 órát is igénybe vehet.

<a id="is-my-file-tiered"></a>
### <a name="how-can-i-tell-whether-a-file-has-been-tiered"></a>Hogyan állapítható meg, hogy egy fájl rétegzett-e?
Számos módja van annak ellenőrzésére, hogy egy fájl rétegzett-e az Azure-fájlmegosztáshoz:
    
   *  **Ellenőrizze a fájl attribútumait.**
     Kattintson a jobb gombbal egy fájlra, válassza a **Részletek menüt,** majd görgessen le az **Attribútumok** tulajdonsághoz. A rétegzett fájlok attribútumai a következőtulajdonságokkal rendelkeznek:     
        
        | Attribútumbetű | Attribútum | Meghatározás |
        |:----------------:|-----------|------------|
        | A | Archívum | Azt jelzi, hogy a fájlról biztonsági másolatkészítésre van kiegészül. Ez az attribútum mindig be van állítva, függetlenül attól, hogy a fájl rétegzett vagy teljes mértékben a lemezen van-e tárolva. |
        | P | Ritka fájl | Azt jelzi, hogy a fájl ritka fájl. A ritka fájl egy speciális típusú fájl, amelyet az NTFS kínál a hatékony használat érdekében, ha a lemezfolyamon lévő fájl többnyire üres. Az Azure File Sync ritka fájlokat használ, mert egy fájl teljesen rétegzett vagy részben visszahívott. Egy teljes rétegzett fájlban a fájladatfolyam a felhőben tárolódik. Egy részben visszahívott fájlban a fájl nak ez a része már a lemezen van. Ha egy fájl teljes mértékben visszahívott a lemezre, az Azure File Sync konvertálja azt egy ritka fájlból egy normál fájlba. Ez az attribútum csak Windows Server 2016-os és régebbi rendszeren van beállítva.|
        | M | Adathozzáférés visszahívása | Azt jelzi, hogy a fájl adatai nincsenek teljes mértékben jelen a helyi tárolón. A fájl olvasása esetén a fájl tartalom legalább egy részét lekell kérni egy Olyan Azure-fájlmegosztásból, amelyhez a kiszolgálóvégpont csatlakozik. Ez az attribútum csak Windows Server 2019 rendszeren van beállítva. |
        | L | Újraelemzési pont | Azt jelzi, hogy a fájlnak újraelemzési pontja van. Az újraelemzési pont egy speciális mutató, amelyet a fájlrendszer szűrője használhat. Az Azure File Sync újraelemzési pontokat használ az Azure File Sync fájlrendszerszűrő (StorageSync.sys) számára a fájl tárolási helyének meghatározásához. Ez támogatja a zökkenőmentes hozzáférést. A felhasználóknak nem kell tudniuk, hogy az Azure File Sync használatban van, vagy hogyan férhethozzá a fájlhoz az Azure-fájlmegosztásban. Amikor egy fájl teljes visszahívott, az Azure File Sync eltávolítja az újraelemzési pontot a fájlból. |
        | O | Offline | Azt jelzi, hogy a fájl tartalmának egy része vagy egésze nem található a lemezen. Amikor egy fájl teljes visszahívott, az Azure File Sync eltávolítja ezt az attribútumot. |

        ![A fájl Tulajdonságok párbeszédpanelje, a Kijelölt Részletek lap](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        A mappában lévő összes fájl attribútumait úgy jelenítheti meg, hogy hozzáadja az **Attribútumok** mezőt a Fájlkezelő táblakijelzőjéhez. Ehhez kattintson a jobb gombbal egy meglévő oszlopra (például **méret),** válassza az **Egyebek**parancsot, majd válassza az **Attribútumok** elemet a legördülő listából.
        
   * **A `fsutil` fájl újraelemzési pontjainak ellenőrzésére használható.**
       Az előző beállításban leírtak szerint a rétegzett fájlhoz mindig van egy újraelemzési pont készlet. Az újraelemzési mutató az Azure File Sync fájlrendszerszűrő (StorageSync.sys) speciális mutatója. Annak ellenőrzéséhez, hogy egy fájl rendelkezik-e újraelemzési ponttal, egy `fsutil` emelt szintű parancssorból vagy PowerShell-ablakban futtassa a segédprogramot:
    
        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```

        Ha a fájl újraelemzési ponttal rendelkezik, akkor a **Reparse címke értéke: 0x8000001e**. Ez a hexadecimális érték az Azure File Sync tulajdonában lévő újraelemzési pont érték. A kimenet is tartalmazza az újraelemzési adatokat, amelyek az Azure-fájlmegosztáson a fájl elérési útját jelölik.

        > [!WARNING]  
        > A `fsutil reparsepoint` segédprogram-parancs képes újraelemzési pont törlésére is. Ne hajtsa végre ezt a parancsot, kivéve, ha az Azure File Sync mérnöki csapata kéri. A parancs futtatása adatvesztést okozhat. 

<a id="afs-recall-file"></a>

### <a name="a-file-i-want-to-use-has-been-tiered-how-can-i-recall-the-file-to-disk-to-use-it-locally"></a>A használni kívánt fájl rétegzett. Hogyan idézhetem vissza a fájlt a lemezre, hogy helyileg használhassam?
A fájlok lemezre való visszahívásának legegyszerűbb módja a fájl megnyitása. Az Azure File Sync fájlrendszer szűrő (StorageSync.sys) zökkenőmentesen letölti a fájlt az Azure-fájlmegosztásnélkül az Ön részéről. A részben olvasható fájltípusok, például multimédiás vagy .zip fájlok esetén a fájl megnyitása nem tölti le a teljes fájlt.

A PowerShell segítségével is kényszerítheti a fájl visszahívható. Ez a beállítás akkor lehet hasznos, ha egyszerre több fájlt szeretne visszahívni, például egy mappában lévő összes fájlt. Nyisson meg egy PowerShell-munkamenetet az On-kiszolgáló csomóponton, ahol az Azure File Sync telepítve van, majd futtassa a következő PowerShell-parancsokat:
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
Választható paraméterek:
* `-Order CloudTieringPolicy`először a legutóbb módosított fájlokat hívja vissza.  
* `-ThreadCount`meghatározza, hogy hány fájl hívható vissza párhuzamosan.
* `-PerFileRetryCount`meghatározza, hogy milyen gyakran kísérelnek meg visszahívást egy jelenleg letiltott fájlra.
* `-PerFileRetryDelaySeconds`meghatározza a visszahívási kísérletek újrapróbálkozása közötti időt másodpercben, és mindig az előző paraméterrel együtt kell használni.

> [!Note]  
> Ha a kiszolgálót üzemeltető helyi köteten nincs elegendő szabad terület `Invoke-StorageSyncFileRecall` az összes rétegzett adat visszahívásához, a parancsmag sikertelen lesz.  

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>Miért nem felel meg a fájl *mérete a lemezen* tulajdonságnak a *Size* tulajdonsággal az Azure File Sync használata után? 
A Windows Fájlkezelő két tulajdonságot takar a fájl méretéről: **Méret** és **Méret a lemezen**. Ezek a tulajdonságok jelentésükben finoman különböznek. **A méret** a fájl teljes méretét jelöli. **A lemezen lévő méret** a lemezen tárolt fájladatfolyam méretét jelöli. Ezek a tulajdonságok értékei különbözőek lehetnek a különböző okok miatt, például a tömörítés, az adatdeduplikáció használata vagy a felhőrétegezés az Azure File Sync használatával. Ha egy fájl rétegzett egy Azure-fájlmegosztás, a lemez mérete nulla, mert a fájlstream az Azure-fájlmegosztásban van tárolva, és nem a lemezen. Az is lehetséges, hogy egy fájl részlegesen rétegzett (vagy részben visszahívott). Egy részben rétegzett fájlban a fájl egy része a lemezen található. Ez akkor fordulhat elő, ha a fájlokat részben olyan alkalmazások olvassák el, mint a multimédia lejátszók vagy a zip segédprogramok. 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>Hogyan kényszeríthetek egy fájl vagy könyvtár rétegzett?
Ha a felhőrétegezési funkció engedélyezve van, a felhőrétegezés automatikusan rétegezi a fájlokat az utolsó hozzáférés alapján, és módosítja az időpontokat, hogy elérje a felhővégponton megadott szabad terület százalékát. Néha azonban előfordulhat, hogy manuálisan szeretné kényszeríteni a fájlt a szintre. Ez akkor lehet hasznos, ha olyan nagyméretű fájlt ment, amelyet hosszú ideig nem kíván újra használni, és azt szeretné, hogy a köteten lévő szabad terület más fájlok és mappák számára is használható legyen. A rétegezést a következő PowerShell-parancsokkal kényszerítheti:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

<a id="afs-image-thumbnail"></a>
### <a name="why-are-my-tiered-files-not-showing-thumbnails-or-previews-in-windows-explorer"></a>Miért nem jelennek meg a többszintű fájlok miniatűrjei vagy előnézetei a Windows Intézőben?
Rétegzett fájlok esetén a miniatűrök és az előnézetek nem lesznek láthatók a kiszolgáló végpontján. Ez a viselkedés várható, mivel a Windows miniatűr gyorsítótár-szolgáltatása szándékosan kihagyja az offline attribútummal rendelkező fájlok olvasását. Ha a felhőrétegezés engedélyezve van, a rétegzett fájlok olvasása a rendszer letöltését (visszahívva) eredményezi.

Ez a viselkedés nem az Azure File Sync, a Windows Intéző megjeleníti a "szürke X" minden olyan fájlokat, amelyek az offline attribútum beállítva. Az X ikon jelenik meg, amikor sMB-n keresztül fér hozzá a fájlokhoz. Ennek a viselkedésnek a részletes magyarázatát lásd:[https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105](https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105)


## <a name="next-steps"></a>Következő lépések
* [Az Azure-fájlszinkronizálás telepítésének megtervezése](storage-sync-files-planning.md)
