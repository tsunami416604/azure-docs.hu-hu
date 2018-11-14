---
title: Tudnivalók az Azure File Sync Felhőrétegzési |} A Microsoft Docs
description: További tudnivalók az Azure File Sync szolgáltatás, Felhőbeli Rétegezés
services: storage
author: sikoo
ms.service: storage
ms.topic: article
ms.date: 09/21/2018
ms.author: sikoo
ms.component: files
ms.openlocfilehash: a0f427ef84a6540522f521cd365e2422a70eb0cd
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51623651"
---
# <a name="cloud-tiering-overview"></a>A cloud rétegezési áttekintése
Felhőbeli rétegezés egy olyan opcionális szolgáltatás, az Azure File Sync, amelyben gyakran használt gyorsítótárba helyileg a kiszolgálón, míg minden más fájlnál számítógépen rétegzett az Azure Files házirend-beállításai alapján. A rétegzett egy fájlt, az Azure File Sync fájlrendszerszűrő (StorageSync.sys) helyettesíti a fájlt helyileg egy mutatót, vagy az újraelemzési pont. Az újraelemzési pontot az Azure Files-fájlra mutató URL-címet jelöli. Egy rétegzett fájlt a "offline" attribútum és a beállítása az NTFS, harmadik féltől származó alkalmazások biztonságosan azonosíthassa a rétegzett fájlok FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS attribútum is rendelkezik.
 
Amikor egy felhasználó megnyit egy rétegzett fájlt, az Azure File Sync zökkenőmentesen visszaírja a fájl adatait a az Azure Files a felhasználót, hogy a fájl valójában az Azure-ban tárolt ismerete nélkül. 
 
 > [!Important]  
    > Fontos: A Felhőbeli rétegezés esetén nem támogatott a Windows rendszer köteteken kiszolgálói végpontot, és csak 64 KiB-nál nagyobb fájlok helyezhető el az Azure Files.
    
Az Azure File Sync nem támogatja a rétegzési fájlok kisebb, mint 64 KiB, a teljesítménybeli terhelést rétegezést és az ilyen kisméretű fájlok visszahívása felülmúlják a lemezterület-megtakarítás.

## <a name="cloud-tiering-faq"></a>A felhő rétegezési – gyakori kérdések

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>Hogyan a rétegzési munkahelyi felhő?
Az Azure File Sync rendszer szűrő "intenzitástérkép" a névtér épülő összes kiszolgálói végpontot. Idővel figyeli hozzáfér (olvasási és írási műveletek), és ezt követően a gyakoriságát és a hozzáférési recency alapján rendel egy megadott hőtérképrészlet pontozása minden fájl. Egy gyakran használt legutóbb megnyitott fájlt akkor minősül forró, mivel a ritkán használt adatok, amelyek alig megszáradásukig, és a egy ideje nem használtak egy fájl akkor minősül. A fájl egy kiszolgálón meghaladja a beállított kötet szabad terület küszöbértékei, amikor azt fogja a leglátványosabb fájlokat az Azure Files adatszint amíg a szabad terület százalékos aránya nem teljesül.

A 4.0-s verziója és a fent az Azure File Sync ügynök emellett megadhat dátum házirend kiszolgálói végpontot fog réteg nem érhető el, vagy a megadott számú napon belül módosított fájlok.

<a id="afs-volume-free-space"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work"></a>Hogyan működik a mennyiségi szabad terület rétegzési szabályzat?
Szabad terület a köteten a szabad terület a köteten, amelyen a kiszolgálói végpont is található lefoglalni kívánt érték. Például ha a szabad terület a köteten egy köteten, amely rendelkezik egy kiszolgálói végpont 20 %-os értékre van állítva, másolatot 80 %-a terület a köteten a rendszer helyéhez a legutóbb használt fájlokat, minden további fájl, amely nem illik a hely rétegzett Azure-ban. Szabad terület a köteten a kötet szintjén, nem pedig az egyes könyvtárak és szinkronizálási csoportok szintjén vonatkozik. 

<a id="volume-free-space-fastdr"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work-with-regards-to-new-server-endpoints"></a>Hogyan működik a mennyiségi szabad terület rétegzési szabályzat tartományállapot új kiszolgálói végpontot?
Kiszolgálói végpont újonnan kiosztott, és csatlakozik az Azure-fájlmegosztás, ha a kiszolgáló először kéri le a névtér, és ezután kéri le a tényleges fájlokat a kötet szabad terület küszöbértékei beolvasásig. Ez a folyamat gyors vész-helyreállítási vagy gyors névtér visszaállítási is nevezik.

<a id="afs-effective-vfs"></a>
### <a name="how-is-volume-free-space-interpreted-when-i-have-multiple-server-endpoints-on-a-volume"></a>Szabad terület a köteten, hogyan a rendszer értelmezi, ha egy köteten van több kiszolgálói végpontot?
Ha egynél több kiszolgálói végpontot egy köteten, a kötet tényleges szabad terület küszöbértékei a legnagyobb szabad terület a köteten a köteten lévő összes kiszolgálói végpontot között megadott. Fájlok rétegezettek lesznek függetlenül attól, hogy mely kiszolgálói végpontot, amelyhez tartoznak azok használati mintáknak megfelelően. Például ha a két kiszolgáló végpontokat egy köteten, Endpoint1 és típusú Endpoint2, ahol Endpoint1 egy kötet szabad terület küszöbértékei 25 %-os és típusú Endpoint2 rendelkezik egy mennyiségi szabad terület küszöbértékei 50 %-os a kötet szabad terület küszöbértékei mindkét kiszolgáló végpont lesz 50 %-a. 

<a id="date-tiering-policy"></a>
### <a name="how-does-the-date-tiering-policy-work-in-conjunction-with-the-volume-free-space-tiering-policy"></a>A dátum rétegzési szabályzat működése együtt a szabályzat rétegezést kötet szabad területtel rendelkező 
Ha engedélyezve van a felhőbeli rétegezést kiszolgálói végpont, a kötet szabad terület házirend beállításával. Azt mindig elsőbbséget élvez bármely más szabályzatok, például a dátum a házirend. Igény szerint engedélyezheti a szabályzat minden egyes kiszolgáló végponton, hogy kötet, ami azt jelenti, csak a fájlok elérni (vagyis, olvasása vagy írása) Ez a szabályzat írja le nap tartományán belül folyamatosan helyi, bármely staler fájlokkal rétegzett dátumot. Ne feledje, hogy a kötet szabad terület mindig élvez elsőbbséget, és nincs elég szabad terület a köteten, tetszőleges számú nap alatt érkezett fájlok leírtak szerint a dátum a házirend megőrzése, ha az Azure File Sync továbbra is a leghidegebb fájlok rétegezés csak az ingyenes kötet területszázalék teljesül.

Tegyük fel például, van egy dátum-alapú rétegzési szabályzat 60 nap és a egy kötet a szabad terület házirend 20 %-os. Esetén, a dátum a házirend alkalmazása, után kevesebb mint 20 % szabad terület a köteten a kötet szabad terület házirend jelentkezik, és felülbírálhatja a dátum-házirendet. Folyamatban van a rétegzett, további fájlok Ez azt eredményezi, úgy, hogy a kiszolgálón tárolt adatok mennyisége csökkenthető az adatok 60 napig 45 nap. Ezzel szemben, ezzel a szabályzattal megkövetelhető értéke kívül esik az időtartományt, még akkor is, ha nem elérte a szabad terület küszöbértékei – így 61 napnál régebbi egy fájl akkor is, ha a kötet üres rétegezettek lesznek fájlok rétegezést.

<a id="volume-free-space-guidelines"></a>
### <a name="how-do-i-determine-the-appropriate-amount-of-volume-free-space"></a>Hogyan állapítható meg a megfelelő mennyiségű szabad terület a köteten?
Adatmennyiség helyi kell tartania néhány tényező határozza meg: a sávszélesség, az adatkészlet hozzáférési minta és a költségvetést. Ha alacsony sávszélességű kapcsolattal rendelkezik, előfordulhat, hogy szeretné megőrizni az adatokat több helyi biztosítása érdekében a felhasználók minimális késéssel. Ellenkező esetben akkor is alapul a lemorzsolódási rátához egy adott időszakban. Például ha tudja, hogy 1 TB-os adatkészlet módosítást körülbelül 10 % vagy aktívan érhető el minden hónapban, akkor előfordulhat, hogy szeretné megőrizni a 100 GB helyi tehát akkor vannak nem gyakran visszahívott fájlokat. Ha a kötet 2 TB-os, akkor érdemes megtartani 5 %-os (vagy 100 GB-os) helyi, ami azt jelenti, a fennmaradó 95 %-os a mennyiségi szabad terület százalékos aránya. Azonban javasoljuk, hogy a fiók e-mail-címen magasabb szintű adatforgalom – puffert hozzáadása más szóval kezdve egy alacsonyabb kötet szabad terület százalékos aránya, és majd módosítani, ha a későbbiekben is szükség. 

Helyi további adatok megőrzése mellett azt jelenti, hogy kevesebb fájlt fog hívhatók vissza az Azure-ból alacsonyabb kimenő forgalom költségeit, de is szükséges, hogy egy helyszíni tárat, amelyben a saját költségek nagyobb mennyiségű karbantartása. Miután az Azure File Sync üzembe helyezett egy példányát, tekintse meg a tárfiók kimenő forgalom nagyjából mérőműszer-e a kötet szabad terület beállítások megfelelőek a használatra vonatkozóan. Feltéve, hogy a storage-fiókot tartalmazza, csak az Azure File Sync Felhőbeli végpont (azaz a szinkronizálási megosztás), majd a nagy forgalom azt jelenti, hogy sok fájl van visszaírandó a felhőből, és fontolja meg a helyi gyorsítótárban.

<a id="how-long-until-my-files-tier"></a>
### <a name="ive-added-a-new-server-endpoint-how-long-until-my-files-on-this-server-tier"></a>Új kiszolgálói végpontok hozzá. Mennyi ideig amíg a kiszolgáló szintjén a fájlokat?
A 4.0-s verziója és a fent az Azure File Sync ügynök után a fájlok feltöltése az Azure-fájlmegosztást, hogy azok rétegezettek lesznek a házirendek alapján, amint a következő rétegezési munkamenet fut, amely óránként egyszer történik. A régebbi ügynökök rétegezést akár 24 órának is el megtörténjen.

<a id="is-my-file-tiered"></a>
### <a name="how-can-i-tell-whether-a-file-has-been-tiered"></a>Hogyan állapítható meg, hogy egy fájl tartozik lett a rétegzett?
Ellenőrizze, hogy a fájl tartozik rétegzett az Azure-fájlmegosztás számos módja van:
    
   *  **Ellenőrizze a fájl a fájl attribútumait.**
     Kattintson a jobb gombbal a fájlra, ugorjon a **részletek**, majd görgessen le a a **attribútumok** tulajdonság. Egy rétegzett fájlt állítsa be a következő attribútumokkal rendelkezik:     
        
        | Levél attribútum | Attribútum | Meghatározás |
        |:----------------:|-----------|------------|
        | A | Archívum | Azt jelzi, hogy a fájl másolatot kell készíteni a biztonsági mentési szoftverrel. Ez az attribútum értéke mindig, függetlenül attól, hogy a fájl rétegzett vagy teljes mértékben a lemezen tárolt. |
        | P | Ritka fájl | Azt jelzi, hogy a fájl egy ritka fájlok. Ritka fájl a hatékony NTFS kínál a fájl egy speciális típusa esetén a fájl lemezre Stream nagyrészt üres. Az Azure File Sync ritka fájlokat használ, mert a fájl teljes rétegzett vagy részlegesen idézni. A fájlfolyamot egy teljes körűen rétegzett fájlt, a felhőben van tárolva. A részlegesen visszaírt fájl a fájl már a lemezen. Ha a fájl teljes idézni lemezre, az Azure File Sync átalakítja a ritka fájlok regulárním souborem. |
        | L | Újraelemzési pontok | Azt jelzi, hogy a fájl egy újraelemzési pont. Újraelemzési pont egy speciális mutató egy fájlrendszerszűrő általi használatra. Az Azure File Sync újraelemzési pontokat használ, az Azure File Sync fájlrendszerszűrőnek (StorageSync.sys) a felhő a fájl tárolási helyének meghatározásához. Ez támogatja a közvetlen hozzáférést. Felhasználóknak nem kell tudnia, hogy az Azure File Sync használatban van-e, illetve a fájlt az Azure-fájlmegosztás eléréséhez. A fájl teljes ismeretes, az Azure File Sync a fájlt az újraelemzési pont eltávolítása. |
        | O | Offline | Azt jelzi, hogy néhány vagy összes a fájl tartalma nem tárolja a lemezen. A fájl teljes ismeretes, az Azure File Sync eltávolítja ezt az attribútumot. |

        ![A fájlhoz, a részletek lapon kiválasztott a tulajdonságai párbeszédpanel](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Megjelenik egy mappában található összes fájl attribútumait hozzáadásával a **attribútumok** a tábla megjelenített a fájlkezelő mezőt. Ehhez kattintson a jobb gombbal egy meglévő oszlopára vonatkozóan (például **mérete**), jelölje be **további**, majd válassza ki **attribútumok** a legördülő listából.
        
   * **Használat `fsutil` egy fájlt az újraelemzési pontok kereséséhez.**
       Az előző beállítás leírtak egy rétegzett fájlt mindig van egy újraelemzési pont beállítása. Egy újraelemzési mutató az Azure File Sync fájlrendszerszűrő (StorageSync.sys) a speciális mutató. Ellenőrizze, hogy rendelkezik-e a fájl egy újraelemzési pont egy emelt szintű parancssort vagy a PowerShell-ablakban, futtassa a `fsutil` segédprogrammal:
    
        ```PowerShell
        fsutil reparsepoint query <your-file-name>
        ```

        Ha a fájl egy újraelemzési ponttal rendelkezik, tekintse meg a várható **címkeértékkel újraelemzési: 0x8000001e**. A hexadecimális érték az újraelemzési pont érték, amely az Azure File Sync tulajdonában van. A kimenet a fájlt a az Azure-fájlmegosztást az elérési utat jelölő újraelemzési adatokat is tartalmazza.

        > [!WARNING]  
        > A `fsutil reparsepoint` segédprogram parancsot is van lehetősége, újraelemzési pont törlése. Ez a parancs nem végrehajtani, kivéve, ha az Azure File Sync mérnöki csapata arra kéri, hogy. A parancs futtatása az adatvesztést eredményezhet. 

<a id="afs-recall-file"></a>

### <a name="a-file-i-want-to-use-has-been-tiered-how-can-i-recall-the-file-to-disk-to-use-it-locally"></a>A használni kívánt fájl tartozik lett rétegzett. Hogyan lehet visszahívása vele helyi lemezre a fájlt?
A fájl lemezre visszahívásának legegyszerűbb módja, hogy nyissa meg a fájlt. Az Azure File Sync fájlrendszerszűrő (StorageSync.sys) zökkenőmentesen fájlt tölt le a az Azure-fájlmegosztást az Ön részéről munka nélkül. Minden fájltípus esetében, amelyek részben olvasható, például multimédiás vagy .zip fájlokat, a fájl megnyitása nem sikerül letölteni a teljes fájlt.

Is használhatja PowerShell kényszerítése egy fájlt. Ez a beállítás akkor hasznos, ha egyszerre, majd előkeresnie több fájl egy mappában található összes fájl például lehet. Nyissa meg a kiszolgáló-csomóponton, amelyen telepítve van-e az Azure File Sync egy PowerShell-munkamenetet, és futtassa a következő PowerShell-parancsokat:
    
    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
    ```

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>Miért nem a *lemezterület* fájl egyezést tulajdonsága a *mérete* tulajdonság használata az Azure File Sync után? 
Windows Fájlkezelőben tesz elérhetővé, amelyek egy fájl mérete két tulajdonság: **mérete** és **lemezterület**. Ezek a Tulajdonságok találhatókkal jelentése különböznek. **Méret** a fájl teljes méretét jelenti. **A lemez mérete** a fájlfolyamot, hogy a lemez méretét adja meg. Ezek a tulajdonságok értékeit számos okból, például a tömörítés esetében eltérőek, az Adatdeduplikáció használatát, vagy az Azure File Sync felhőrétegzési. Egy fájlt az Azure-fájlmegosztások többszintű, ha a lemez mérete nulla, mert a fájlfolyamot van tárolva, az Azure-fájlmegosztást, és nem a lemezen. Lehetőség arra is a fájlok részben rétegzett (vagy részlegesen visszahívott szoftvertermék) lehet. A részlegesen rétegezett fájl a fájl a lemezen van megvalósítva. Ez akkor fordulhat elő, amikor fájlt például a multimédia-lejátszó alkalmazások által részben olvasható vagy segédprogramok zip. 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>Hogyan kényszeríthetem egy fájl vagy címtár helyezhető el?
Ha a felhő rétegezési szolgáltatás engedélyezve van, felhőbeli rétegezés automatikusan rétegek fájlok alapján az utolsó hozzáférés, és elérése érdekében a kötet szabad terület százalékos aránya a felhőbeli végpont a megadott idő módosítása. Egyes esetekben azonban érdemes manuálisan a Tier fájl kényszerített. Ez akkor lehet hasznos, ha menti a nagy méretű fájlt, amely nem szeretne hosszú ideig újra használni, és azt szeretné, a szabad lemezterület a köteten a más fájlok és mappák használata. Kényszerítheti, hogy a következő PowerShell-parancsok használatával rétegezést:

    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
    ```

## <a name="next-steps"></a>További lépések
* [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
