---
title: Gyakori kérdések az Azure-fájlok |} Microsoft Docs
description: Azure-fájlokkal kapcsolatban gyakran feltett kérdésekre adott válaszokat megkeresése
services: storage
documentationcenter: ''
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 12/04/2017
ms.author: renash
ms.openlocfilehash: cb44f1d456ec12b7fd21e397b749117942560f05
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="frequently-asked-questions-about-azure-files"></a>Azure-fájlok kapcsolatos gyakori kérdések
[Az Azure Files](storage-files-introduction.md) teljes körűen felügyelt fájlmegosztást kínáló a felhőben, amelyek elérhetők a szabványos [Server Message Block (SMB) protokoll](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) (más néven Common Internet File System vagy CIFS). Akkor is csatlakoztathatja az Azure fájlmegosztások egyidejűleg felhőalapú vagy helyszíni üzemelő példányok esetében a Windows, Linux és macOS. Azure fájlmegosztásokat Windows kiszolgáló gépen a gyors hozzáférés megközelíti az adatok helyének Azure fájlszinkronizálás (előzetes verzió) segítségével képes gyorsítótárazni.

Ebben a cikkben megválaszolunk Azure fájlok szolgáltatásokat és funkciókat, beleértve az Azure fájlszinkronizálás Azure fájlokkal kapcsolatos gyakori kérdésekre. Ha nem látja a választ a kérdésére, lépjen kapcsolatba velünk (a növekvő sorrendben) a következő csatornákon keresztül:

1. Ez a cikk megjegyzéseket tartalmazó részét.
2. [Az Azure Storage fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
3. [Azure-fájlokat UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Microsoft támogatási szolgálatához. Az Azure portálon, egy új támogatási kérelem létrehozásához a **súgó** lapon jelölje be a **súgó + támogatás** gombra, és válassza **új támogatja a kérelem**.

## <a name="general"></a>Általános kérdések
* <a id="why-files-useful"></a>
**Hogyan van hasznos az Azure-fájlok?**  
   Használhatja az Azure fájlokat fájlmegosztásokat hozhat létre a felhőben anélkül, hogy a terhelést a fizikai kiszolgáló, az eszköz vagy a készülék kezeléséért. Azt a munkához monoton, beleértve az operációs rendszer frissítéseinek alkalmazása és cseréje a hibás lemezeket. Azure-fájlok segítségével a forgatókönyvekkel kapcsolatos további tudnivalókért lásd: [miért Azure Files szolgáltatás hasznos](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>
**Mik azok a fájlok eléréséhez Azure-fájlokban szereplő különböző módokon?**  
    Akkor is csatlakoztathatja a fájlmegosztást a helyi számítógépen az SMB 3.0 protokoll használatával, vagy használhatja a hasonló eszközök [Tártallózó](http://storageexplorer.com/) a fájlmegosztás található fájlokat. Az alkalmazásból használhatja storage ügyfélkódtáraival, a REST API-k, a PowerShell vagy az Azure CLI-t a fájlok az Azure fájlmegosztás eléréséhez.

* <a id="what-is-afs"></a>
**Mi az Azure fájlszinkronizálás?**  
    Azure fájlszinkronizálás segítségével központosíthatja a fájlmegosztások a szervezet Azure fájlokban, ugyanakkor változatlanul megőrizze a rugalmasság, a teljesítmény és a kompatibilitási egy helyszíni fájlkiszolgáló. Azure fájl szinkronizálása a Windows Server-gépek átalakítja a Azure fájlmegosztás gyors gyorsítótárába. Minden protokoll, amely a Windows Server helyileg, az adatok eléréséhez használhatja, például SMB, a hálózati fájlrendszer (NFS) és a fájl átvitele protokoll Service (ftps-t). Akkor is annyi gyorsítótárak világszerte szükség szerint.

* <a id="files-versus-blobs"></a>
**Mire használható az Azure fájlmegosztások Azure Blob storage és az adataimat?**  
    Az Azure Files és az Azure Blob storage nyújtanak módjai nagy mennyiségű adat tárolása a felhőben, ők azonban hasznos némileg eltérő célokra. 
    
    Az Azure Blob storage akkor hasznos, strukturálatlan adatok tárolásához igénylő jelentős mértékű, natív felhőalapú alkalmazásokhoz. Maximalizálása a teljesítmény és méretezhetőség érdekében az Azure Blob Storage tárolóban egy egyszerűbb tárolási absztrakciós, mint egy igaz fájlrendszer. Azure Blob Storage tárolóban végezheti el, csak a REST-alapú ügyfél függvénytárainak (vagy közvetlenül a REST-alapú protokollon keresztül).

    Az Azure Files szolgáltatás kifejezetten egy fájlrendszer. Az Azure Files ismert, és a helyszíni operációs rendszerek használata évnyi kedvelt minden fájl kivonatok rendelkezik. Azure Blob Storage tárolóban, például a Azure fájlok egy REST-felületen és a REST-alapú ügyfél függvénytárainak kínál. Azure Blob Storage tárolóban, ellentétben a Azure fájlok az Azure fájlmegosztások SMB hozzáférést biztosít. SMB használatával akkor is csatlakoztathatja az Azure fájlmegosztások közvetlenül Windows, Linux vagy macOS, vagy a helyszíni vagy felhőalapú virtuális gépek, a programozás vagy különleges illesztőprogramok csatolása a fájlrendszer nélkül. A helyszíni fájlkiszolgálókon lévő Azure fájlmegosztásokat Azure fájlszinkronizálás használatával gyorsan elérheti őket, az adatok helyének hamarosan képes gyorsítótárazni. 
   
    Az Azure-fájlok és az Azure Blob Storage tárolóban különbségei részletesebb leírásáért lásd: [Azure Blob Storage tárolóban, a Azure fájlok vagy az Azure-lemezek használatára való](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Azure Blob Storage tárolóban kapcsolatos további információkért lásd: [Blob storage bemutatása](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**Mire használható az Azure fájlmegosztások Azure lemezek helyett?**  
    Egy Azure lemezeken található egyszerűen egy lemezt. Egy önálló önmagában lemez nem túl hasznos. Lehet értéket kiolvasni a Azure-lemezeket, egy lemezt kell csatlakoztatni az Azure-ban futó virtuális gép. Azure-lemezeket használható minden olyan lemezt egy helyszíni kiszolgálón szeretné használni. Az operációs rendszer tárolására, egy operációs rendszer a lapozófájl vagy az alkalmazás dedikált tárolóként használható. Egy Azure lemezek érdekes használja, ha egy fájlkiszolgáló ugyanazon a helyen, ahol az az Azure fájlmegosztások segítségével lehet használni a felhőben. Egy fájlkiszolgáló Azure virtuális gépek telepítése módja a nagy teljesítményű a file storage beolvasása az Azure-ban, ha jelenleg nem támogatottak az Azure-fájlok (például az NFS protokoll támogatása, vagy a prémium szintű storage) telepítési lehetőségeket. 

    A fájlkiszolgálót futtató Azure lemezzel háttér-tárolóként általában azonban sokkal drágább, mint az Azure fájlmegosztások használata néhány okokból. Először mellett a lemezes tárolás fizet, is kell fizetnie egy vagy több Azure virtuális gépeken futó járó költségek. Másodszor is kell kezelni a virtuális gépeket, a fájlkiszolgáló futtatásához használt. Például való telepítésért felelős az operációs rendszer frissítései. Végezetül végső soron van szüksége a gyorsítótárazott helyszíni az adatokat, esetén az Ön számára a állíthat be és felügyelhet replikációs technológiák, például elosztott fájl rendszer replikációs (DFSR), hogy fordulhat elő.

    Egyik módszer az első a legjobb Azure fájlok és a fájlkiszolgáló futó Azure virtuális gépek (mellett háttér-tárolóként Azure lemez felhasználásával) egy fájlkiszolgálón, amely a virtuális gép felhőben található Azure fájlszinkronizálás telepíteni fogja. Ha Azure fájlmegosztás ugyanabban a régióban, mint a fájlkiszolgáló, engedélyezheti a felhő rétegezéséhez, és állítsa be a szabad terület százalékos mennyisége maximális (99 %). Ez biztosítja, hogy a minimális duplikálva lettek-e az adatok. Bármely kívánt alkalmazásokat a fájl-kiszolgálókkal, például támogatja az NFS-protokollt igénylő alkalmazások is használhatja.

    A beállítás a egy nagy teljesítményű és magas rendelkezésre állású fájlkiszolgálót az Azure-ban való beállításának kapcsolatos információkért lásd: [telepítése infrastruktúra-szolgáltatási virtuális gép vendég fürtök a Microsoft Azure-ban](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). Azure-fájlok és az Azure lemezek közötti különbségekről részletesebb leírását lásd: [Azure Blob Storage tárolóban, a Azure fájlok vagy az Azure-lemezek használatára való](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Azure-lemezeket kapcsolatos további információkért lásd: [Azure felügyelt lemezekhez – áttekintés](../../virtual-machines/windows/managed-disks-overview.md).

* <a id="get-started"></a>
**Hogyan kezdjem el Azure-fájlokat használ?**  
   Ismerkedés az Azure-fájlok is könnyen. Első, [fájlmegosztás létrehozása](storage-how-to-create-file-share.md), és csatlakoztassa az előnyben részesített operációs rendszer: 

    * [A Windows csatlakoztatása](storage-how-to-use-files-windows.md)
    * [A Linux csatlakoztatása](storage-how-to-use-files-linux.md)
    * [A macOS csatlakoztatása](storage-how-to-use-files-mac.md)

   Részletesebb az Azure fájlmegosztások telepítésével kapcsolatos éles fájlmegosztások a szervezet cserélni, váltásról [Azure fájlok központi telepítésének tervezésében](storage-files-planning.md).

* <a id="redundancy-options"></a>
**Milyen tárolási redundancia lehetőségeket támogat az Azure fájlok?**  
    Azure-fájlok jelenleg helyileg redundáns tárolás (LRS), a zóna redundáns tárolás (ZRS) és a georedundáns tárolás (GRS). Írásvédett georedundáns (RA-GRS) tárolás támogatása a jövőben, de ütemtervek megosztásához jelenleg nem tudunk.

* <a id="tier-options"></a>
**Milyen tárolási rétegek Azure fájlok támogatottak?**  
    Azure-fájlok jelenleg csak a standard rétege. Nem tudunk ütemtervek most támogatja prémium szintű storage, és ritkán használt adatok megosztásához. 
    
    > [!NOTE]
    > Nem hozható létre Azure fájlmegosztásokat, csak a blob storage-fiókok vagy a prémium szintű storage-fiókok.

* <a id="give-us-feedback"></a>
**Valóban szeretném tekinteni az Azure Fileshoz hozzá egy adott funkcióhoz. Felveheti azt?**  
    Az Azure-fájlok csapat iránt érdeklődik rendelkezik minden visszajelzés hallgatás szolgáltatásainkkal. Adjon szavazhatnak szolgáltatás kérelmek [Azure fájlok UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)! Jelenleg folyamatban Reméljük, delighting, számos új szolgáltatást.

## <a name="azure-file-sync"></a>Azure File Sync

* <a id="afs-region-availability"></a>
**Milyen régiókban támogatott Azure fájlszinkronizálás (előzetes verzió)?**  
    Azure fájlszinkronizálás jelenleg Kelet-Ausztrália Kanada központi, USA keleti régiója, Délkelet-Ázsiában, Egyesült Királyság déli régiója, Nyugat-Európa, USA nyugati régiója érhető el. További régiókhoz fogja támogatni, általánosan rendelkezésre álló felé dolgozunk. További információkért lásd: [régiónkénti elérhetőség](storage-sync-files-planning.md#region-availability).

* <a id="cross-domain-sync"></a>
**Szinkronizálási ugyanabban a csoportban is rendelkezem tartományhoz csatlakoztatott és a nem tartományhoz csatlakoztatott?**  
    Igen. A szinkronizálási csoport tartalmazhat rendelkeznek eltérő Active Directory tagsági server-végpontokat, akkor is, ha nem a tartományhoz. Bár technikailag működik, ebben a konfigurációban, nem ajánlott ennek a szokásos beállításokkal, mert a hozzáférés-vezérlési listák (ACL) a fájlok és mappák több kiszolgálón definiált nem feltétlenül érvénybe lépteti a szinkronizálási csoport többi kiszolgálójára. A legjobb eredmények elérése érdekében ajánlott szinkronizálása az Active Directory-erdőben lévő kiszolgálók között, kiszolgálók, amelyek más Active Directory-erdőben található, de amelyek bizalmi kapcsolatok létesítése vagy kiszolgálók, amelyek nincsenek a tartomány között. Ajánlott elkerülni, álló ezeket a beállításokat használja.

* <a id="afs-change-detection"></a>
**A fájl létrehozott közvetlenül a saját Azure fájlmegosztások SMB használatával, vagy a portálon. Mennyi időt vesz igénybe a fájl szinkronizálása a szinkronizálási csoport kiszolgálójára?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**Ha ugyanazon fájl két kiszolgáló körülbelül egy időben történtek, mi történik?**  
    Az Azure fájlszinkronizálás használ egy egyszerű ütközésfeloldás stratégia: jelenleg mindkét módosításokat, a két kiszolgáló egyidejűleg módosított fájlok nyomon. A legutóbb írt módosítás megtartja az eredeti fájlnevet. A régebbi fájl rendelkezik a "forrás" gép és az ütközést a névhez. Ez azt jelenti, hogy ez a besorolás: 
   
    \<FileNameWithoutExtension\>-\<MachineName\>\[-#\].\<ext\>  

    Például az első CompanyReport.docx irányadók válna CompanyReport-CentralServer.docx Ha CentralServer, ahol a régebbi írása történt. A második ütközés CompanyReport-CentralServer-1.docx neve.

* <a id="afs-storage-redundancy"></a>
**Georedundáns tárolás az Azure-fájl szinkronizálás támogatva van?**  
    Igen, Azure fájlokat helyileg redundáns tárolás (LRS) és a georedundáns tárolás (GRS) is támogatja. Georedundáns párosított régiók közötti feladatátvétel, azt javasoljuk, hogy kezeli-e az új régió csak az adatok biztonsági mentéséhez. Azure fájl szinkronizálása nem automatikusan megkezdődik az új elsődleges régióban való szinkronizálás. 

* <a id="sizeondisk-versus-size"></a>
**Miért nem a *lemezterület* tulajdonság fájl megfelelést a *mérete* tulajdonság használata Azure fájlszinkronizálás után?**  
    A Windows Fájlkezelőben egy fájl mérete képviselő két tulajdonságok közzététele: **mérete** és **lemezterület**. Ezek a Tulajdonságok találhatókkal jelentése különböznek. **Méret** a fájl teljes méretét jelenti. **Lemezterület** a fájlfolyam a lemezen tárolt méretét adja meg. Ezek a tulajdonságok értékeit számos okból, például a tömörítési eltérnek, az Adatdeduplikáció használata, vagy a felhőalapú Azure fájl szinkronizálással rétegezésével. Ha egy fájl Azure-fájlmegosztásra többszintű, a a lemez mérete nulla, mivel a fájlok továbbítása az Azure-fájlmegosztáshoz, és nem a lemezen. Lehetőség arra is részben rétegzett (vagy részben visszaírt) fájl. Részlegesen rétegzett fájlban a fájl a lemezen van. Ez akkor fordulhat elő, amikor fájlok alkalmazások, például médialejátszó részben olvasható, vagy a zip-segédprogramok. 

* <a id="is-my-file-tiered"></a>
**Hogyan állapítható meg, hogy a fájl rétegzett lett-e?**  
    Ellenőrizze, hogy egy fájl rétegzett lett-e az Azure fájlmegosztások számos módja van:
    
   *  **Ellenőrizze a fájl a fájl attribútumait.**
     Ehhez kattintson a jobb gombbal a fájlra, ugorjon a **részletek**, majd görgessen le a a **attribútumok** tulajdonság. A rétegzett fájl a következő attribútummal rendelkezik:     
        
        | A levél attribútum | Attribútum | Meghatározás |
        |:----------------:|-----------|------------|
        | A | Archívum | Azt jelzi, hogy a fájl készüljön biztonsági másolat biztonsági mentési szoftverrel. Ez az attribútum értéke mindig, függetlenül attól, hogy a fájl rétegzett vagy teljesen tárolása a lemezen. |
        | P | Ritka fájl | Azt jelzi, hogy a fájl ritka fájlok. Ritka fájl egy speciális típusa a hatékony NTFS kínál a fájl esetén a lemez-adatfolyamban lévő fájl többnyire üres. Azure fájlszinkronizálás használja a ritka fájlokat, mivel a fájl rétegzett teljesen vagy részben előtt tartani. Egy teljesen rétegzett fájlban a fájlfolyam tárolja a felhőben. A részlegesen visszaírt fájl a fájl már a lemezen. Ha egy fájl teljesen előtt tartani, lemezre, Azure fájlszinkronizálás átalakítja a ritka fájlok rendszeres fájlba. |
        | L | Újraelemzési pont | Azt jelzi, hogy a fájl rendelkezik-e az újraelemzési pont. Újraelemzési pont egy fájlrendszerszűrő általi használatra különleges mutató. Az Azure fájlszinkronizálás újraelemzési pontokat használ, a Azure fájlszinkronizálás fájlrendszerszűrő (StorageSync.sys) a felhő a fájl tárolási helyének meghatározásához. Ez a funkció támogatja zökkenőmentes hozzáférést. Felhasználóknak nem kell tudnia, hogy Azure fájlszinkronizálás használatban van-e, illetve a fájl az Azure fájlmegosztás eléréséhez. Ha egy fájl teljesen visszaírása, Azure fájlszinkronizálás az újraelemzési pont eltávolítása a fájlból. |
        | O | Offline | Azt jelzi, hogy vagy azok egy részét a fájl tartalma nem tárolása a lemezen. Ha egy fájl teljesen visszaírása, Azure fájlszinkronizálás eltávolítja ezt az attribútumot. |

        ![A fájl esetén a kiválasztott Részletek lap tulajdonságai párbeszédpanel](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Megjelenik a mappában lévő összes fájl attribútumai hozzáadásával a **attribútumok** mezőjét a Fájlkezelőben tábla megjelenítéséhez. Ehhez kattintson a jobb gombbal az oszlop (például **mérete**) elemre, jelölje be **további**, majd válassza ki **attribútumok** a legördülő listából.
        
   * **Használjon `fsutil` a fájl újraelemzési pontok kereséséhez.**
       Az előző beállítás leírtak a rétegzett fájl mindig rendelkezik egy újraelemzési pont beállítása. Újraelemzési mutató egy különleges mutatót a Azure fájlszinkronizálás fájlrendszerszűrő (StorageSync.sys). Ellenőrizze, hogy van-e a fájl egy újraelemzési pont egy emelt szintű parancssorból vagy a PowerShell-ablakban, futtassa a `fsutil` segédprogram:
    
        ```PowerShell
        fsutil reparsepoint query <your-file-name>
        ```

        Ha a fájl egy újraelemzési pont, is a várt **újraelemzési címke: 0x8000001e**. A hexadecimális érték az újraelemzési pont érték Azure fájlszinkronizálás tulajdonában. A kimeneti elérési képviseli a fájlmegosztáson az Azure újraelemzési adatokat is tartalmazza.

        > [!WARNING]  
        > A `fsutil reparsepoint` segédprogram parancsot is van lehetősége, újraelemzési pont törlése. Kivéve, ha a fájlszinkronizálás Azure mérnöki csapat arra kéri, hogy ne hajtsa végre ezt a parancsot. A parancs futtatása adatvesztést eredményezhet. 

* <a id="afs-recall-file"></a>**A használni kívánt fájl rendelkezik lett rétegzett. Hogyan lehet visszahívása lemezt kell használni, helyileg a fájlt?**  
    A lemezre fájl visszahívásának legkönnyebben megnyitni a fájlt. Az Azure fájlszinkronizálás fájlrendszerszűrő (StorageSync.sys) zökkenőmentesen letölti a fájlt az Azure fájlmegosztásról beavatkozást munka nélkül. Fájltípus esetében, és lehet részlegesen. számú, például a multimédia vagy .zip fájlt, a fájl megnyitása nem sikerül letölteni a teljes fájlt.

    Is használhatja PowerShell kényszerítheti a fájlt. A beállítás akkor lehet hasznos, ha előhívja több fájl egyszerre, például egy mappában lévő összes fájlt. A kiszolgáló-csomópontra, amelyen telepítve van-e az Azure fájlszinkronizálás PowerShell munkamenetet nyit meg, és futtassa a következő PowerShell-parancsokat:
    
    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
    ```

* <a id="afs-force-tiering"></a>
**Hogyan kényszeríti a fájlt vagy könyvtárat helyezhető el?**  
    Ha a felhő rétegezési szolgáltatás engedélyezve van, a felhő rétegezéséhez automatikusan rétegek fájlok utolsó hozzáférés alapul, és módosítási eléréséhez a kötet szabad terület százalékos aránya a felhőbeli végpont a megadott időpont. Egyes esetekben azonban előfordulhat, hogy manuálisan kényszeríteni kívánja a réteg egy fájlt. Ez akkor lehet hasznos, ha menti, amelyet nem kíván újra használni a hosszú ideig nagy fájlok, és a szabad lemezterület a köteten a más fájlok és mappák használni kívánt. Beállíthatja, hogy a következő PowerShell-parancsok segítségével rétegezéséhez:

    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
    ```

* <a id="afs-effective-vfs"></a>
**Hogyan van *kötet hely* értelmezi, ha egy köteten több kiszolgáló-végpontot?**  
    Ha egy köteten több kiszolgáló végpont, hatékony kötet szabad terület küszöbértéke bármely kiszolgáló végpont a köteten lévő között megadott legnagyobb kötet szabad terület. Függetlenül attól, hogy melyik kiszolgáló végpont, amelyekhez tartoznak a használati mintáknak megfelelően fog helyezhető el a fájlokat. Például, ha két kiszolgáló végpontok egy köteten, Endpoint1 és Endpoint2, ahol Endpoint1 egy kötet szabad terület küszöbértéke 25 %-os és Endpoint2 rendelkezik egy mennyiségi szabad terület küszöbértéke 50 %-os mindkét kiszolgáló végpont kötet hely küszöbértéke 50 % lesz.

* <a id="afs-files-excluded"></a>
**Azure fájlszinkronizálás automatikusan kizárja a mely fájlok vagy mappák?**  
    Alapértelmezés szerint Azure fájlszinkronizálás nem tartalmazza a következő fájlokat:
    * desktop.ini
    * thumbs.db
    * ehthumbs.db
    * ~$\*.\*
    * \*.laccdb
    * \*.tmp
    * 635D02A9D91C401B97884B82B3BCDAEA.\*

    A következő mappák is ki vannak zárva, alapértelmezés szerint:

    * \System volume Information
    * \$RECYCLE.BIN
    * \SyncShareState

* <a id="afs-os-support"></a>
**Használható a Windows Server 2008 R2, a Linux vagy a hálózati csatolású (NAS) tárolóeszköz Azure fájlszinkronizálás?**  
    Azure fájlszinkronizálás jelenleg csak a Windows Server 2016 és a Windows Server 2012 R2. Jelenleg nem tudunk bármely más terveket is osztjuk meg, de meg van nyitva támogató ügyfelek igény szerint további platformok még. Ossza meg velünk [Azure fájlok UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) milyen platformok szeretné támogatásához.

## <a name="security-authentication-and-access-control"></a>Biztonsági, hitelesítési és hozzáférés-vezérlés
* <a id="ad-support"></a>
**Az Active Directory-alapú hitelesítési és hozzáférés-vezérlés Azure fájlok által támogatott?**  
    Az Azure Files hozzáférés vezérléséhez két lehetőséget kínál:

    - Közös hozzáférésű jogosultságkód (SAS) segítségével a rendszer, hogy meghatározott engedélyekkel rendelkezik, és amelyek érvényesek a megadott időtartam alatt jogkivonatokat hoz létre. Például a jogkivonatot az olvasási hozzáférést egy adott fájlt, amelynek a 10 perces lejárati is létrehozhat. Csak olvasási hozzáféréssel bárki, aki a token érvényességi ideje alatt birtokában van, amely a fájl ezen 10 percig. Jelenleg megosztott elérési aláírási kulcsokat csak a REST API-n keresztül, vagy a klienskódtárak segítségével használható. Csatlakoztatnia kell az Azure fájlmegosztások SMB-n keresztül a tárfiókkulcsokat használatával.

    - Az Azure fájlszinkronizálás megőrzi, és minden tulajdonosi hozzáférés-vezérlési listákat vagy DACL, replikálja az (akár az Active Directory-alapú helyi) számára a szinkronizált server-végpontokat. Windows Server már képes hitelesíteni az Active Directoryban, mert Azure fájlszinkronizálás hatékony stop-résnek lehetséges csak teljes körű támogatása az Active Directory-alapú hitelesítéshez, de ACL-támogatás érkezik.

    Jelenleg Azure-fájlok nem támogatja az Active Directory közvetlenül.

* <a id="encryption-at-rest"></a>
**Hogyan tudom biztosítható, hogy az Azure fájlmegosztások titkosítása?**  
    Az Azure Storage szolgáltatás titkosítási éppen minden régióban alapértelmezés szerint engedélyezett. Ezek a területek nem kell bármilyen műveletet engedélyezheti a titkosítást. Más régiókból, lásd: [kiszolgálóoldali titkosítás](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>
**Hogyan tud nyújtani azzal, hogy egy webböngészővel egy adott fájlt a hozzáférést?**  
    Közös hozzáférésű jogosultságkód segítségével hoz létre jogkivonatokat, amelyek adott engedélyekkel rendelkezik, és amelyek érvényesek a megadott időtartam alatt. Például egy jogkivonatot, amely egy adott fájlt, csak olvasható hozzáférést biztosít a beállított időn is létrehozhat. Bárki, aki rendelkezik az URL-cím érhetik el a fájlt közvetlenül bármely webböngészőben a token érvényességi ideje alatt. Könnyen létrehozhat egy közös hozzáférésű aláírási kulcs a Tártallózó például egy felhasználói Felületéről.

* <a id="file-level-permissions"></a>
**Ennyi az egész lehetséges, adja meg a csak olvasható vagy csak írási engedélyeket a megosztáson belüli mappákhoz?**  
    Csatlakoztatja a fájlmegosztást SMB használatával, ha nem rendelkezik engedélyekkel mappaszintű szabályozhatják. Azonban ha egy közös hozzáférésű jogosultságkódot a REST API vagy klienskódtárak használatával hoz létre, megadhatja csak olvasható, vagy csak írási engedélyekkel kell rendelkeznie a megosztáson belüli mappákhoz.

* <a id="ip-restrictions"></a>
**IP-korlátozások, az Azure fájlmegosztások is létrehozható?**  
    Igen. Az Azure fájlmegosztás elérését a tárolási fiók szintjén korlátozható. További információkért lásd: [konfigurálása Azure Storage tűzfalak és a virtuális hálózatok](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>
**Milyen adatok megfelelőségi házirendek támogatja az Azure-fájlok?**  
   Az Azure Files egyéb tárolási szolgáltatások az Azure Storage használt azonos tároló-architektúra futtatható. Az Azure Files az azonos megfelelőségi szabályzatok az egyéb Azure storage szolgáltatások használt vonatkozik. Adatok Azure Storage-ellenőrzésre vonatkozó további információkért töltse le és tekintse meg a [Microsoft Azure Data Protection dokumentum](http://go.microsoft.com/fwlink/?LinkID=398382&clcid=0x409), és navigáljon a [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/default.aspx).

## <a name="on-premises-access"></a>Helyszíni hozzáférés
* <a id="expressroute-not-required"></a>
**Konfigurálnom kell a Azure ExpressRoute segítségével csatlakozzon az Azure Fileshoz vagy Azure fájl Sync szolgáltatás használatával a helyszíni?**  
    Nem. ExpressRoute az Azure fájlmegosztások eléréséhez nem szükséges. Ha csatlakoztat egy Azure fájlmegosztás közvetlen a helyszínen, az összes, amely szükséges, hogy a 445-ös porton (TCP, kimenő) nyissa meg az internet-hozzáférés (azt a portot, amelyet való kommunikációhoz az SMB protokollt használja). Azure fájlszinkronizálás használata szükséges csak a 443-as (TCP, kimenő) portot a HTTPS-hozzáféréshez (nem szükséges SMB). Azonban Ön *is* ExpressRoute használja az egyik hozzáférési lehetőséget.

* <a id="mount-locally"></a>
**Hogyan is csatlakoztathatja a helyi számítógépen az Azure fájlmegosztások?**  
    A fájlmegosztást csatlakoztathatja az SMB protokoll segítségével, ha a port 445 (TCP, kimenő) meg nyitva, és az ügyfél támogatja az SMB 3.0 protokollt (például, ha a Windows 10 vagy Windows Server 2016 használata). Ha a 445-ös porton le van tiltva, vagy az Internetszolgáltató által a szervezet házirendje, Azure fájlszinkronizálás használhatja az Azure fájlmegosztás eléréséhez.

## <a name="backup"></a>Biztonsági mentés
* <a id="backup-share"></a>
**Készítsen biztonsági másolatot az Azure-fájl megosztása?**  
    Rendszeres használható [pillanatképek megosztása](storage-how-to-use-files-snapshots.md) véletlen törlés elleni védelem. Is használhatja AzCopy, Robocopy, vagy egy külső biztonsági mentési eszköz képes biztonsági mentést egy csatlakoztatott fájlmegosztást. 

## <a name="share-snapshots"></a>A pillanatképek megosztása
### <a name="share-snapshots-general"></a>Pillanatképek megosztása: általános
* <a id="what-are-snaphots"></a>
**Mik azok a fájlpillanatképek megosztást?**  
    Az Azure file megosztás pillanatképeket hozhat létre egy csak olvasható verziója a fájlmegosztásokat. Is használhatja Azure fájlokat másolni a tartalom vissza egy korábbi ugyanennek a megosztásnak, az Azure rendszerben, vagy további módosításokat helyszíni másik helyre. Megosztás pillanatképeket vonatkozó további információkért tekintse meg a [áttekintése pillanatkép megosztása](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>
**A megosztás pillanatképeket tároló?**  
    Megosztás pillanatképek ugyanazt a tárfiókot, a fájlmegosztást tárolja.

* <a id="snapshot-perf-impact"></a>
**Vannak a teljesítményre gyakorolt hatása megosztás pillanatképek használata?**  
    Megosztás pillanatképek nem rendelkezik a teljesítményigény.

* <a id="snapshot-consistency"></a>
**Azok megosztási pillanatképek alkalmazáskonzisztens?**  
    Nem, nincsenek alkalmazáskonzisztens pillanatképek megosztás. A felhasználó a írások ürítse ki az alkalmazásból a megosztáshoz a megosztást pillanatképének készítése előtt kell.

* <a id="snapshot-limits"></a>
**Vannak-e használható megosztás pillanatképek számára vonatkozó korlátozások?**  
    Igen. Az Azure Files legfeljebb 200 megosztás pillanatképek továbbra is. Megosztás pillanatképek nem száma a fájlmegosztási kvóta felé így a teljes lemezterület, melynek használatával a megosztás pillanatképek megosztásonkénti korlátozva. Tárfiókok korlátai továbbra is érvényesek lesznek. Után 200 megosztás pillanatképeket törölnie kell a korábbi pillanatképek új megosztás-pillanatképek létrehozásához.
* <a id="snapshot-cost"></a>
**Mennyi nem megosztás pillanatkép költség?**  
    Szabványos és standard tárolási költségű pillanatkép vonatkoznak. A pillanatképeket növekményes jellegűek. A kiinduló pillanatkép a megosztás saját magát. A későbbi pillanatképek növekményes, és csak a korábbi pillanatképből különbözeti tárolja. Ez azt jelenti, hogy az új módosításokkal, amely a számlázási lesz látható minimális, ha a munkaterhelés forgalom minimális lesz. Lásd: [árazás lap](https://azure.microsoft.com/en-us/pricing/details/storage/files/) Standard Azure-fájlok díjszabási információkat. A megosztás pillanatkép által felhasznált mérete meg módja a számlázott kapacitás összevetésével ma használható kapacitás. Jelenleg is dolgozunk eszközt használunk erre a jelentéskészítési javítása érdekében.


### <a name="create-share-snapshots"></a>Megosztás pillanatképeket
* <a id="file-snaphsots"></a>
**Az egyes fájlok megosztási pillanatkép hozhat létre?**  
    Megosztás pillanatképek a fájl fájlmegosztási szinten jönnek létre. A megosztás fájlpillanatkép állíthatja vissza a fájlokat, de fájlszintű megosztás pillanatképek nem hozható létre. Azonban ha elvégezte a megosztási szintű megosztás pillanatképet, és meg szeretné tekinteni a megosztás pillanatképeket, amelyekben egy adott fájl módosult, ehhez a **korábbi verziók** csatlakoztatott Windows-megosztáson található. 
    
    Ha egy fájl pillanatkép funkció, ossza meg velünk [Azure fájlok UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encypted-snapshots"></a>
**Egy titkosított fájlmegosztás megosztás pillanatképeket hozhat létre?**  
    Titkosítás engedélyezve aktívan rendelkező Azure fájlmegosztásokat megosztás pillanatképet is igénybe vehet. Egy megosztás pillanatképből fájlok visszaállíthatja egy titkosított fájlmegosztás. Ha a megosztás titkosítva van, a megosztás pillanatkép is titkosítva van.

* <a id="geo-redundant-snaphsots"></a>
**A megosztás pillanatképek georedundáns vannak?**  
    Megosztás pillanatképek rendelkezik, amelynek felvétel Azure fájlmegosztás azonos redundanciát. Georedundáns tárolás jelölt ki a fiókot, ha a pillanatkép-is megosztásba tárolódik redundantly párosított régióban.

### <a name="manage-share-snapshots"></a>Megosztás pillanatképek kezeléséhez
* <a id="browse-snapshots-linux"></a>
**Tallózhatja a Linux megosztás pillanatképeinek?**  
    Azure CLI 2.0 segítségével létrehozása, listázása, keresse meg és állítson vissza egy megosztás pillanatképet a Linux.

* <a id="copy-snapshots-to-other-storage-account"></a>
**Átmásolhatók-e a megosztás pillanatképek során eltérő tárfiók?**  
    Másolhat fájlokat származó megosztás egy másik helyre, de nem lehet másolni a megosztás pillanatképek magukat.

### <a name="restore-data-from-share-snapshots"></a>Megosztás származó adatok visszaállítása
* <a id="promote-share-snapshot"></a>
**A kiinduló megosztáshoz való megosztás pillanatkép is előléptetni?**  
    Megosztás pillanatkép adatokat semmilyen más célra másolhatja. A kiinduló megosztáshoz való megosztás pillanatkép nem léptethető elő.

* <a id="restore-snapshotted-file-to-other-share"></a>
**Állíthatók vissza adatokat a megosztás pillanatképből egy másik tárfiókkal?**  
    Igen. Egy megosztás pillanatképből fájlokat az eredeti helyre vagy másik helyre, amely tartalmazza a tárfiókon vagy egy másik tárolási fiókot, vagy ugyanabban a régióban vagy különböző régiókban lehet másolni. Akkor is tud fájlokat másolni a helyszíni helyre vagy bármely más felhőbe.    
  
### <a name="clean-up-share-snapshots"></a>Megosztás pillanatképek tisztítása
* <a id="delete-share-keep-snapshots"></a>
**Törölje a megosztást azonban nem törli a megosztást pillanatképek?**  
    Ha a megosztás aktív megosztás pillanatképekkel rendelkezik, a megosztás nem törölhető. Az API-k segítségével törölje a megosztást pillanatképeket, és a megosztáshoz. Is törölheti a megosztás pillanatképek mind a megosztás, az Azure portálon.

* <a id="delete-share-with-snapshots"></a>
**Mi történik a megosztás pillanatképet, ha a tárfiókot?**  
    Ha törli a tárfiókot, a megosztás pillanatképek is törlődnek.

## <a name="billing-and-pricing"></a>Számlázási és az árképzés terén
* <a id="vm-file-share-network-traffic"></a>
**Nem közötti hálózati forgalom az Azure virtuális gép és egy Azure fájlmegosztás fájlszám külső sávszélesség, amelyet az előfizetést?**  
    Ha a fájlmegosztás és a virtuális gép ugyanazon Azure-régióban van, akkor nem kell külön fizetni a fájlmegosztás és a virtuális gép közötti forgalom. Ha a fájlmegosztás és a virtuális gép különböző régiókban, adatforgalom külső sávszélességnek minősül, van szó.

* <a id="share-snapshot-price"></a>
**Milyen mértékű ugyanazt a pillanatképek költség?**  
     Előzetes nincs megosztás pillanatképet jelent a kapacitástervezés nem kell fizetni. Standard szintű tárolást kilépő és tranzakciós költségek vonatkoznak. Általános rendelkezésre állás érdekében után előfizetések a kapacitás és a tranzakciókért a megosztás pillanatképek fogjuk számlázni.
     
     Megosztás pillanatképei növekményes jellegűek. A kiinduló megosztás pillanatkép a megosztás saját magát. Minden ezt követő megosztás pillanatképet növekményes, és csak az előző megosztás pillanatképből különbség tárolja. Csak a módosított tartalmat a kell fizetni. Ha egy megosztást az adatok 100 GiB rendelkezik, de csak 5 GiB megváltozott az utolsó megosztás pillanatfelvétel óta, a megosztás pillanatkép csak 5 további GiB használ fel, és a 105 GiB kell fizetni. Tranzakció és a szabványos kilépő díjakat kapcsolatos további információkért tekintse meg a [árazás lap](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="scale-and-performance"></a>Méretezés és teljesítmény
* <a id="files-scale-limits"></a>
**A skálázási korlátai Azure fájlok**  
    Azure-fájlok méretezhetőségi és Teljesítménycélok kapcsolatos információkért lásd: [Azure fájlok méretezhetőségi és Teljesítménycélok](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>
**Van szükségem az Azure-fájlok jelenleg nyújt, mint nagyobb fájlmegosztást. A saját Azure fájlmegosztás méretének növelése?**  
    Nem. Az Azure fájlmegosztások maximális mérete 5 TiB. Ez jelenleg rögzített korlátját, amely jelenleg nem lehet beállítani. A fájlmegosztás méretét 100 TiB megoldás jelenleg is dolgozunk, de ütemtervek megosztásához jelenleg nem tudunk.

* <a id="open-handles-quota"></a>
**Hány ügyfél egyidejűleg hozzáfér ugyanazt a fájlt?**   
    Egyetlen fájl 2000 megnyitott kezelőkkel tartozó kvóta van. Ha 2000 megnyitott leíróinak, egy hibaüzenet jelenik meg, amely szerint a kvóta elérésekor.

* <a id="zip-slow-performance"></a>
**A teljesítmény akkor lassú, ha szeretnék csomagolja ki a fájlokat az Azure Fileshoz. Mit tegyek?**  
    Nagyszámú fájlok átvitele az Azure Fileshoz, javasoljuk, AzCopy (a Windows; a Linux és UNIX rendszerű előzetes verzió) vagy az Azure PowerShell használata. Ezek az eszközök a hálózati átvitel optimalizált.

* <a id="slow-perf-windows-81-2012r2"></a>
**Miért van a lassú teljesítmény, miután szeretnék saját Azure fájlmegosztás csatlakoztatása a Windows Server 2012 R2 vagy Windows 8.1?**  
    Egy ismert probléma van, amikor az Azure fájlmegosztások csatlakoztatása a Windows Server 2012 R2 és Windows 8.1. A probléma a 2014. április összegző frissítés a Windows 8.1 és Windows Server 2012 R2 lett telepítve. Az optimális teljesítmény érdekében győződjön meg arról, hogy Windows Server 2012 R2 és Windows 8.1 alkalmazza a javítás. (Mindig kap a Windows Update Windows javítások.) További információkért lásd: a Microsoft Tudásbázis kapcsolódó cikkében tanulmányozható [csökkentheti a teljesítményt, amikor Azure fájlok fér hozzá a Windows 8.1 vagy Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Szolgáltatások és egyéb szolgáltatások együttműködés
* <a id="cluster-witness"></a>
**Használható az Azure-fájlmegosztáshoz, egy *fájl megosztása tanúsító* a Windows Server feladatátvevő fürt?**  
    Ez a konfiguráció egy Azure fájlmegosztás nem támogatott. Ez az Azure Blob Storage tárolóban szolgáltatáshoz beállításával kapcsolatos további információkért lásd: [felhő tanúsító feladatátvevő fürt telepítése](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>
**Csatlakoztathatok egy Azure-fájlmegosztáshoz egy Azure-tárolóba példány?**  
    Igen, az Azure fájlmegosztások is jó választás, ha azt szeretné megőrizni az adatokat tároló példány élettartamát túl. További információkért lásd: [csatlakoztatása az Azure fájlmegosztások Azure-tárolóba osztályt](../../container-instances/container-instances-mounting-azure-files-volume.md).

* <a id="rest-rename"></a>
**Van-e a REST API átnevezési művelettel?**  
    Jelenleg nem.

* <a id="nested-shares"></a>
**Adható meg a beágyazott megosztás? Más szóval a megosztás egy megosztásban?**  
    Nem. A fájlmegosztás *van* a virtuális meghajtó, amelyet csatlakoztathat, így nem támogatottak a beágyazott megosztás.

* <a id="ibm-mq"></a>
**Miként használható az Azure-fájlok IBM MQ-val?**  
    Az IBM kiadott egy dokumentumot, amely segítségével az IBM MQ-ügyfelek konfigurálása az Azure-fájlok az IBM szolgáltatással. További információkért lásd: [beállítása az IBM MQ többpéldányos várólista-kezelő Microsoft Azure fájlok szolgáltatással](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).

## <a name="see-also"></a>Lásd még
* [A Windows Azure fájlok hibaelhárítása](storage-troubleshoot-windows-file-connection-problems.md)
* [Az Azure Files lévő Linux hibaelhárítása](storage-troubleshoot-linux-file-connection-problems.md)
* [Hibaelhárítás az Azure fájlszinkronizálás (előzetes verzió)](storage-sync-files-troubleshoot.md)
