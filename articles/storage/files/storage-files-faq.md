---
title: "Gyakori kérdések – Azure-fájlok |} Microsoft Docs"
description: "Azure-fájlokkal kapcsolatban gyakran feltett kérdésekre adott válaszokat megkeresése"
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/13/2017
ms.author: renash
ms.openlocfilehash: 91c46ea0897f83811cfa5c1a8b67677caff14569
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="frequently-asked-questions-about-azure-files"></a>Az Azure Files kapcsolatos gyakori kérdések
[Az Azure Files](storage-files-introduction.md) teljes körűen felügyelt fájlmegosztást kínáló a felhőben, amelyek elérhetők az iparági szabvány [Server Message Block (SMB) protokoll](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) (más néven a Common Internet File System vagy a CIFS). Az Azure-fájlmegosztások párhuzamosan több felhőalapú vagy helyszíni, Windows, Linux vagy macOS rendszerű üzemelő példány által is csatlakoztathatóak. Emellett az Azure fájlmegosztások gyorsítótárazható, a Windows Azure fájlszinkronizálás (előzetes verzió) rendszerű kiszolgálókon a gyors hozzáférés a közelében, ahol az adatokat használják.

Ez a cikk néhány gyakran feltett kérdésekre Azure fájlok szolgáltatásokat és funkciókat, beleértve az Azure fájlszinkronizálás foglalkozunk. Ha a választ a kérdésére itt nem látható, ne habozzon, kapcsolatba velünk a következő csatornákon (a növekvő sorrendben):

1. Ez a cikk megjegyzések szakaszában.
2. [Az Azure Storage-fórum](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredata)
3. [Az Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) 
4. Microsoft Support: Hozzon létre egy új támogatási esetet, lépjen a "Súgó és támogatás" fülre az Azure portálon, és kattintson a "New támogatja a kérelem".

## <a name="general"></a>Általános kérdések
* <a id="why-files-useful"></a>**Miért érdemes a Azure-fájlok?**  
   Az Azure Files lehetővé teszi a fájlmegosztások létrehozása a felhőben anélkül, hogy a fizikai kiszolgáló vagy eszköz/berendezés terhelés kezelésére. Ez azt jelenti, hogy az operációs rendszer frissítéseinek alkalmazása és cseréje a hibás lemezek kevesebb időt tölthet – ezen monoton munkák mindegyikét meg végezzük. Azure-fájlok segítségére lehetnek a forgatókönyvekkel kapcsolatos további tudnivalókért lásd: [miért Azure Files szolgáltatás hasznos](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>**Mik azok a fájlok eléréséhez Azure-fájlokban szereplő különböző módokon?**  
    Akkor is csatlakoztathatja a fájlmegosztást SMB 3.0 protokoll használatával a helyi számítógépen vagy eszközök, például [Tártallózó](http://storageexplorer.com/) a fájlmegosztás található fájlokat. Az alkalmazásból használhatja storage ügyfélkódtáraival, a REST API-k, a PowerShell vagy a CLI-t a fájlok az Azure-fájlmegosztás eléréséhez.

* <a id="what-is-afs"></a>**Mi az Azure fájlszinkronizálás?**  
    Azure fájlszinkronizálás fájlmegosztások a szervezet Azure-fájlokban szereplő központosíthatja a rugalmasságot, teljesítmény és egy helyszíni fájlkiszolgáló kompatibilitási kiadása nélkül teszi lehetővé. Ezt úgy éri el, hogy átalakítja a Windows-kiszolgálókat az Azure-fájlmegosztás gyors gyorsítótáraivá. A Windows Server rendszeren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl. SMB, NFS vagy FTPS), és annyi gyorsítótára lehet világszerte, amennyire csak szüksége van.

* <a id="files-versus-blobs"></a>**Miért használjak egy Azure fájlmegosztás Azure Blob storage és az adataimat?**  
    Az Azure Files és az Azure Blob storage mind nyújtanak olyan nagy mennyiségű adat tárolása a felhőben, de hasznos némileg eltérő célokra. Az Azure Blob storage akkor hasznos, strukturálatlan adatok tárolásához igénylő jelentős mértékű, natív felhőalapú alkalmazásokhoz. Maximalizálása a teljesítmény és méretezhetőség érdekében az Azure Blob Storage tárolóban egy egyszerűbb tárolási absztrakciós, mint egy igaz fájlrendszer. Emellett Azure Blob Storage tárolóban csak érhető REST-alapú ügyfél függvénytárainak (vagy közvetlenül a REST-alapú protokollon keresztül).

    Az Azure Files viszont kifejezetten célja, hogy a fájlt a rendszer, a fájl kivonatok, amelyeket ismer és a helyszíni operációs rendszerek évnyi kedvelt mindegyikével. Azure Blob Storage tárolóban, például Azure fájlok egy REST-felületen és a REST-alapú ügyfél függvénytárainak kínál, de eltérően Azure Blob storage Azure fájlok is biztosít az Azure fájlmegosztások SMB elérésére. Ez azt jelenti, hogy közvetlenül csatlakoztathatja egy Azure fájlmegosztás Windows, Linux vagy macOS, a helyszíni vagy felhőben virtuális gépeket, anélkül, hogy a kód írása, vagy különleges illesztőprogramok csatlakoztatása a fájlrendszerben. Emellett az Azure fájlmegosztások előfordulhat, hogy gyorsítótárazott Azure fájlszinkronizálás használatával gyorsan elérheti őket az adatok helyének közelébe helyszíni fájlkiszolgálókon. 
   
    Az Azure-fájlok és az Azure Blob Storage tárolóban különbségei részletesebb leírását lásd: [Azure Blob Storage tárolóban, a Azure fájlok vagy az Azure-lemezek használatára való](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Azure Blob Storage tárolóban kapcsolatos további információkért lásd: [Blob storage bemutatása](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**Mire használható az Azure fájlmegosztás vagy Azure lemezek?**  
    Egy Azure lemez most, hogy a lemez. Egy önálló lemez önmagában nem nagyon hasznos - érték kívül egy Azure lemez, Azure-ban futó virtuális géphez csatolni kell. Azure-lemezeket használható semmit, és mindent használhatja a lemezt egy helyszíni kiszolgálón: egy operációs rendszer lemezeként lapozófájl egy operációs rendszer, vagy a kijelölt tárhelycsomópont alkalmazásokra vonatkozóan. Az egyik érdekes Azure lemezek használata létrehozásához egy fájlkiszolgáló a felhőben helyezi, pontosan ugyanúgy használja, akkor előfordulhat, hogy használni egy Azure fájlmegosztás. Az Azure virtuális gépeken a fájlkiszolgáló telepítése módja a képek remekül felhívják magukra, nagy teljesítményű a file storage beolvasása az Azure-ban, ha szüksége van Azure-fájlok (például az NFS protokoll támogatása, vagy a prémium szintű storage) jelenleg nem támogatott központi telepítési beállítások. 

    Másrészt a fájlkiszolgálót futtató Azure lemezzel háttérrendszer, tárolási lesz sokkal drágább, mint egy Azure fájlmegosztás használata több okból. Első lépésként mellett a lemezes tárolás fizet, kell is kell fizetnie egy vagy több Azure virtuális gépeken futó járó költségek. Másodszor is kell kezelni a virtuális gépeket futtatnak a fájlkiszolgálón, például a felelősek az operációs rendszer frissítései, stb. Végezetül végső soron szükséges adatokat a helyi gyorsítótárba, esetén az Ön számára a beállítása és kezelése (például az elosztott fájlrendszer replikációs) technológiától fordulhat elő, hogy.

    A legjobb Azure fájlok és az Azure virtuális gépeken, a háttér-tárolóként Azure lemezek futtatott fájlkiszolgáló segítségével egy érdekes megoldás, a felhő virtuális gép által üzemeltetett fájlkiszolgáló telepítése Azure fájlszinkronizálás. Ha az Azure fájlmegosztás ugyanabban a régióban, mint a fájlkiszolgáló, engedélyezheti rétegezésével a felhő, és állítsa be a kötet szabad terület százalékos legnagyobb (99 %). Ez biztosítja, miközben lehetővé teszi, hogy használja a választott azt szeretné, szemben a fájlkiszolgálók ilyen semmit NFS-protokollt igénylő alkalmazások támogatják az adatok minimális ismétlődést.

    Az egyik módja egy nagy teljesítményű és magas rendelkezésre állású fájlkiszolgálóra telepítse az Azure-ban beállítása további információkért lásd: [telepítése IaaS VM Vendégfürttel rendelkezzen a Microsoft Azure-ban](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). Az Azure-fájlok és az Azure lemezek közötti különbségekről részletesebb leírását lásd: [Azure Blob Storage tárolóban, a Azure fájlok vagy az Azure-lemezek használatára való](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Azure-lemezeket kapcsolatos további információkért lásd: [Azure felügyelt lemezekhez – áttekintés](../../virtual-machines/windows/managed-disks-overview.md).

* <a id="get-started"></a>**Hogyan kezdjem el Azure-fájlokat használ?**  
    Ismerkedés az Azure Files könnyen: egyszerűen [fájlmegosztás létrehozása](storage-how-to-create-file-share.md) , és csatlakoztassa az előnyben részesített operációs rendszer: 

    - [A Windows csatlakoztatható](storage-how-to-use-files-windows.md)
    - [A Linux csatlakoztatható](storage-how-to-use-files-linux.md)
    - [MacOS csatlakoztatása](storage-how-to-use-files-mac.md)

    Részletesebb hogyan telepítheti egy Azure fájlmegosztás éles fájlmegosztások a szervezet cserélni, váltásról [Azure fájlok központi telepítésének tervezésében](storage-files-planning.md).

* <a id="redundancy-options"></a>**Milyen tárolási redundancia lehetőségeket támogat az Azure fájlok?**  
    Az Azure Files csak jelenleg helyileg redundáns tárolás (LRS) és a georedundáns tárolás (GRS) most. Rendszer zónaredundáns tárolás (ZRS) és az írásvédett georedundáns tárolás (RA-GRS) a jövőben támogatása, de nincs ütemtervek jelenleg megosztásához.

* <a id="tier-options"></a>**Milyen tárolási rétegek jelenleg támogat az Azure-fájlok?**  
    Az Azure Files jelenleg csak támogatja a szabványos rétege. Jelenleg nincs ütemtervek premium és ritkán használt adatok támogatás megosztásához. Vegye figyelembe, hogy nem hozható létre Azure fájlmegosztásokat csak a Blob storage-fiókok vagy a prémium szintű storage-fiókok.

* <a id="give-us-feedback"></a>**Hogy valóban szeretné *x* funkció hozzáadása az Azure Fileshoz is hozzáadja?**  
    Az Azure-fájlok csapat elengedhetetlen, iránt érdeklődik rendelkezik minden visszajelzés hallgatás szolgáltatásainkkal. Adja a funkciókra vonatkozó kérések szavazhatnak a [Azure fájlok UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)! Jelenleg folyamatban Reméljük, delighting, számos új szolgáltatást.

## <a name="azure-file-sync"></a>Az Azure File szinkronizálása
* <a id="afs-region-availability"></a>**Milyen régiók jelenleg támogatott az Azure-fájl szinkronizálás (előzetes verzió)?**  
    USA nyugati régiója, Nyugat-Európában, Kelet-Ausztrália és Délkelet-Ázsia Azure fájlszinkronizálás már érhető el. A Microsoft támogatni fogják a további régiók szerint felé általánosan rendelkezésre álló dolgozunk. További információkért lásd: [régiónkénti elérhetőség](storage-sync-files-planning.md#region-availability).

* <a id="cross-domain-sync"></a>**Is kell tartományhoz csatlakoztatott és a tartományhoz nem csatlakoztatott kiszolgálók ugyanazon szinkronizálási csoportba?**  
    Igen, a szinkronizálási csoport, amelyek különböző Active Directory-csoporttagság, beleértve a nem tartományhoz csatlakoztatott kiszolgáló végpontok is tartalmazhat. Konfigurációs technikailag működik, amíg nem ajánlott ennek a szokásos beállításokkal, fájlok és mappák több kiszolgálón megadott ACL-ek nem feltétlenül érvénybe lépteti a szinkronizálási csoport többi kiszolgálójára. A legjobb eredmények elérése érdekében javasoljuk, vagy kiszolgálók ugyanazon Active Directory erdőben, a megbízhatósági kapcsolatok eltérő Active Directory-erdőkben található kiszolgálók, vagy nem egy tartományhoz, de nem vegyesen a fentiek mindegyikét a kiszolgálók közötti szinkronizálása.

* <a id="afs-change-detection"></a>**A fájl létrehozott közvetlenül a saját Azure fájlmegosztás SMB protokollon keresztül vagy a portálon keresztül. Mennyi ideig, amíg a fájl szinkronizálva van a szinkronizálási csoportban lévő kiszolgálók?** 
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**Ha ugyanazon fájl két kiszolgáló körülbelül egy időben történtek, mi történik?**  
    Az Azure fájlszinkronizálás használ egy egyszerű ütközés feloldása stratégia: mindkét módosítás tartsa azt. A legtöbb legutóbb írt tartja az eredeti fájlnevet. A régebbi fájl nem tartalmaz, a "forrás" gép és a névhez, ez a besorolás ütközés száma: 
   
    `<FileNameWithoutExtension>-<MachineName>[-#].<ext>`  

    Például az első ütköző `CompanyReport.docx` válna `CompanyReport-CentralServer.docx` Ha `CentralServer` van, ahol a régebbi írása történt. A második ütközés volna azonosítottak `CompanyReport-CentralServer-1.docx`.

* <a id="afs-storage-redundancy"></a>**Az Azure-fájl szinkronizálás támogatott georedundáns tárolás (GRS)?**  
    Igen, helyileg redundáns tárolás (LRS) és a georedundáns tárolás (GRS) támogatottak az Azure fájlszinkronizálás. Georedundáns párosított régiók közötti feladatátvétel esetén javasoljuk az új régió kezelésére csak az adatok biztonsági mentéséhez. Azure fájl szinkronizálása nem automatikusan elindítja az új elsődleges régióban való szinkronizálás. 

* <a id="sizeondisk-versus-size"></a>**Miért nem a *lemezterület* tulajdonság fájl megfelelést a *mérete* tulajdonság használata Azure fájlszinkronizálás után?**  
    Windows Fájlkezelőben két tulajdonságok, közzététele **mérete** és **lemezterület** képviselő egy fájl méretét. Ezek a Tulajdonságok találhatókkal különböznek jelentése; **Mérete** a fájl teljes méretét jelenti közben **lemezterület** a ténylegesen a lemezen tárolt fájlok továbbítása méretét adja meg. Ezek is számos okból, például a tömörítési eltérnek, az Adatdeduplikáció, illetve abban az esetben, ha, felhőalapú Azure fájl szinkronizálással rétegezésével. Ha egy fájl többszintű Azure fájlmegosztásra, lemezen lesz az nulla, mert a fájlfolyam nem Azure fájlmegosztás tárolja a lemezen. Akkor is egy fájl részben rétegzett (vagy részben visszaírt), ami azt jelenti, hogy a fájl egy része van a lemezen. Ez akkor fordulhat elő, alkalmazásokhoz, mint a médialejátszó vagy például .zip fájlba segédprogramok fájlok olvasása részben. 

* <a id="is-my-file-tiered"></a>**Hogyan állapítható meg, ha egy fájl rétegzett lett-e?**  
    Számos módon ellenőrizze, hogy ha egy fájl rétegzett lett-e az Azure-fájl megosztása van:
    
    1. **Ellenőrizze a fájl a fájl attribútumait.** Ehhez kattintson a jobb gombbal a fájlra, navigáljon a **részletek** görgessen le a a **attribútumok** tulajdonság. Egy rétegzett fájl kell beállítani a következő attribútumokat:     
        
        | A levél attribútum | Attribútum | Meghatározás |
        |:----------------:|-----------|------------|
        | A | Archívum | Azt jelzi, hogy a fájl készüljön biztonsági másolat biztonsági mentési szoftverrel. Ez az attribútum értéke mindig függetlenül attól, hogy a fájl rétegzett vagy teljesen tárolása a lemezen. |
        | P | Ritka fájl | Azt jelzi, hogy a fájl, amely a hatékony NTFS kínál a fájl egy speciális típusa van, amikor a fájl a lemezen adatfolyamok alapvetően üres ritka fájlok. Azure fájlszinkronizálás ritka fájlokat használ, mert a fájl teljes rétegzett, azaz a fájlfolyam van csak a felhőben tárolt vagy részben visszaírt, ami azt jelenti, hogy a fájl egy része már a lemezen. Ha egy fájl teljesen előtt tartani, lemezre, Azure fájlszinkronizálás konvertálja a ritka fájlok rendszeres fájlt. |
        | L | Újraelemzési pont | Azt jelzi, hogy a fájl rendelkezik-e az újraelemzési pont, amely a fájlrendszer szűrőjének általi használatra különleges mutató. Az Azure fájlszinkronizálás újraelemzési pontokat használ az Azure fájlszinkronizálás fájlrendszerszűrő (StorageSync.sys) megadása a felhőben a fájl tárolására. Ez lehetővé teszi a zökkenőmentes hozzáférést, még akkor is ismeri az Azure fájlszinkronizálás használatban van a végfelhasználói és hogyan juthatnak az Azure fájlmegosztás fájlban nélkül. Ha egy fájl teljesen visszaírása, Azure fájlszinkronizálás az újraelemzési pont eltávolítása a fájlból. |
        | O | Kapcsolat nélküli módban | Azt jelzi, hogy vagy azok egy részét a fájl tartalma nem tárolása a lemezen. Ha egy fájl teljesen visszaírása, Azure fájlszinkronizálás eltávolítja ezt az attribútumot. |

        ![A részleteket tartalmazó lapot a kiválasztott fájl tulajdonságainak párbeszédpanelét](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Célszerű is megállapíthatja, hogy az összes mappában lévő fájlok attribútumok hozzáadásával a **attribútumok** mező fájl Exporer tábla megjelenítéséhez. Ehhez kattintson a jobb gombbal az oszlop (például méret), válasszon **további** válassza **attribútumok** a legördülő listából.
        
    2. **Használjon `fsutil` a fájl újraelemzési pontok kereséséhez.** Említetteknek megfelelően az előző beállítás, egy rétegzett fájlt mindig lesz újraelemzési pont vagy egy különös mutató az Azure fájlszinkronizálás fájlrendszerszűrő (StorageSync.sys), állítsa. Ellenőrizheti, ha a fájl rendelkezik-e egy újraelemzési pontok és a a `fsutil` eszközt egy rendszergazda jogú parancssort vagy a PowerShell-munkamenetben:
    
        ```PowerShell
        fsutil reparsepoint query <your-file-name>
        ```

        Amennyiben a fájl egy újraelemzési pont, megjelennie **újraelemzési címke: 0x8000001e**. A hexadecimális érték az újraelemzési pont érték Azure fájlszinkronizálás tulajdonában. A kimeneti is tartalmazza az újraelemzési adatokat az elérési út a fájlhoz az Azure-fájl megosztása.

        > [!Warning]  
        > A `fsutil reparsepoint` segédprogram parancs is tartalmaz egy újraelemzési pont törlésének lehetőségét. Nem hajtható végre: a parancs csak a fájlszinkronizálás Azure mérnöki csapathoz utasításainak – ezzel az adatvesztéssel járhat. 

* <a id="afs-recall-file"></a>**A használni kívánt fájl rendelkezik lett rétegzett... hogyan lehet visszahívása úgy, hogy helyi használatra lemez?**  
    A lemezre fájl visszahívásának legkönnyebben való megnyitásához. Az Azure fájlszinkronizálás fájlrendszerszűrő (StorageSync.sys) fog zökkenőmentesen töltse le a fájlt az Azure fájlmegosztás anélkül, hogy a munkájuk elvégzéséhez. Fájltípus esetében, és lehet részlegesen. számú, például a multimédia vagy zip-fájlok, a fájl megnyitása nem jár le a a teljes fájlt.

    Az is lehetséges kényszerített egy fájlt PowerShell használatával. Például ez akkor lehet hasznos, ha előhívja sok fájl egyszerre (például a mappában található összes fájl egy). A kiszolgáló-csomópontra, amelyen telepítve van-e az Azure fájlszinkronizálás PowerShell munkamenetet nyit meg, és futtassa a következő PowerShell-parancsokat:
    
    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
    ```

* <a id="afs-force-tiering"></a>**Hogyan kényszerítheti az egy fájlt vagy könyvtárat helyezhető el?**  
    Ha engedélyezve van, a felhő rétegezési funkció automatikusan réteg alapján az utolsó hozzáférés fájlok és módosítási időpont a kötet szabad terület százalékos aránya a felhőbeli végpont megadott elérése érdekében, azonban néha előfordulhat, hogy szeretné, hogy szolgáltatásiréteg manuálisan kívánja egy fájl. Például ez akkor lehet hasznos, ha nem kíván újra használni a hosszú ideig, és a szabad területet a köteten most szeretné, hogy más fájlok/mappák nagy méretű fájl mentése. Beállíthatja, hogy a következő PowerShell-parancsokkal rétegezéséhez:

    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
    ```

* <a id="afs-files-excluded"></a>**Azure fájlszinkronizálás automatikusan kizárja a mely fájlok vagy mappák?**
    Alapértelmezés szerint Azure fájlszinkronizálás nem tartalmazza a következő fájlokat:
    
    - Desktop.ini
    - thumbs.db
    - ehthumbs.db
    - ~$\*.\*
    - \*.laccdb
    - \*.tmp
    - 635D02A9D91C401B97884B82B3BCDAEA.\*

    A következő mappák is ki vannak zárva, alapértelmezés szerint:

    - \System volume Information
    - \$INDÍTSA ÚJRA. BIN
    - \SyncShareState

* <a id="afs-os-support"></a>**Azure-fájl szinkronizálást használ a Windows Server 2008 R2, Linux, és szeretnék, vagy az NAS eszközzel - Azure fájlszinkronizálás támogatja, amelyek?**
    Napjainkban Azure fájlszinkronizálás csak támogatja a Windows Server 2012 R2 és Windows Server 2016. Jelenleg nem tudunk bármely más terveket is osztjuk meg, de még a nyitott és érdekelt ügyfél igény szerint további platformok támogatására. Tudassa velünk, milyen platformok szeretné támogatni a számunkra [Azure fájlok UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).

## <a name="security-authentication-and-access-control"></a>Biztonsági, hitelesítési és hozzáférés-vezérlés
* <a id="ad-support"></a>**Az Active Directory-alapú hitelesítési és hozzáférés-vezérlés Azure fájlok által támogatott?**  
    Az Azure Files kétféleképpen teheti a hozzáférés-vezérlés kezelése:

    - Az SAS segítségével is létrehozhat, amelyek érvényesek a megadott időtartam különleges engedélyekkel rendelkező jogkivonatokat. Például a jogkivonatot az adott fájlba 10 perc lejárati csak olvasási hozzáféréssel is létrehozhat. Csak olvasási hozzáféréssel bárki, aki rendelkezik a token érvényességi ideje alatt azt rendelkezik, amely a fájl ezen 10 percig. SAS-kulcsok használata csak támogatott a REST API-n vagy könyvtárak keresztül ma, csatlakoztatnia kell az Azure fájlmegosztás SMB-n keresztül, a tárfiók kulcsait.

    - Azure fájlszinkronizálás megőrzi, és minden hozzáférés-vezérlési listák (AD-alapú vagy helyi) replikálja a szinkronizált összes server-végpontokat. Windows Server már képes hitelesíteni az Active Directoryban, mert Azure fájlszinkronizálás biztosíthat kiváló stop-résnek mérték csak teljes körű támogatása az AD-alapú hitelesítéshez, és hozzáférés-vezérlési lista támogatási érkezik.

    Az Azure Files közvetlenül jelenleg nem támogatja az Active Directory.

* <a id="encryption-at-rest"></a>**Hogyan biztosítható meg, hogy a saját Azure fájlmegosztás titkosított nyugalmi-e?**  
    Titkosítási nyugalmi éppen minden régióban alapértelmezés szerint engedélyezett. Ezek régiókhoz nincs teendője engedélyezheti a titkosítást. Más területen, tekintse meg [kiszolgálóoldali titkosítás](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>**Hogyan biztosíthat hozzáférést egy adott fájlt, egy webböngésző segítségével?**  
    Az SAS segítségével is létrehozhat, amelyek érvényesek a megadott időtartam különleges engedélyekkel rendelkező jogkivonatokat. Létrehozhat például egy jogkivonatot, amely csak olvasási hozzáféréssel rendelkezik egy bizonyos fájlhoz egy megadott ideig. Bárki, aki rendelkezik az URL-cím érhetik el a fájlt közvetlenül bármely webböngészővel érvényes állapotában. Az SAS-kulcsok egyszerűen létrehozhatóak például olyan felhasználói felületekkel, mint a Storage Explorer.

* <a id="file-level-permissions"></a>**Ennyi az egész lehetséges, adja meg a csak olvasható vagy csak írási engedélyeket a megosztáson belüli mappákhoz?**  
    Ha a fájlmegosztást SMB-protokollal csatlakoztatja, nem kezelheti ilyen szinten a fájlmegosztást. Az viszont megoldás lehet, ha létrehoz közös hozzáférésű jogosultságkódokat (SAS) a REST API-n vagy a klienskódtáron keresztül.

* <a id="ip-restrictions"></a>**Egy Azure fájlmegosztás IP-korlátozásokat is létrehozható?**  
    Igen, az Azure fájlmegosztás elérését korlátozható a tárolási fiók szintjén. További információkért lásd: lásd: [konfigurálása Azure Storage tűzfalak és a virtuális hálózatok](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>**Mik az Azure-fájlok lehet megfelelőségi szabályzatok?**  
   Az Azure Files az azonos tárolási architektúrával rendelkezik, mint más tárolási szolgáltatások az Azure Storage fut, és alkalmazza az azonos megfelelőségi szabályzatok. További információ az Azure Storage adatok megfelelőségi letöltheti, és tekintse meg [Microsoft Azure Data Protection dokumentum](http://go.microsoft.com/fwlink/?LinkID=398382&clcid=0x409) és a [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/default.aspx).

## <a name="on-premises-access"></a>Helyszíni hozzáférés
* <a id="expressroute-not-required"></a>**Konfigurálnom kell a Azure ExpressRoute segítségével csatlakozzon az Azure Fileshoz vagy Azure fájl Sync szolgáltatás használatával a helyszíni?**  
    ExpressRoute nem, nem szükséges egy Azure-fájlmegosztás eléréséhez. Egy Azure fájlmegosztás közvetlenül most csatlakoztatni a helyszíni, minden olyan szükség esetén, hogy rendelkezik-e 445-ös porton (TCP, kimenő) nyissa meg az Internet-hozzáférés (Ez az a port folytat SMB). Azure fájlszinkronizálás használata, minden olyan szükséges (TCP, kimenő) a 443-as portot a HTTPS-hozzáféréshez (nem szükséges SMB). Azonban ExpressRoute használható bármelyik hozzáférési beállítás, ha szükséges.

* <a id="mount-locally"></a>**Hogyan lehet egy Azure fájlmegosztás csatlakoztatása a helyi számítógépen?**  
    Akkor is csatlakoztathatja a fájlmegosztást SMB-protokollal, amíg a port 445 (TCP, kimenő) meg nyitva, és az ügyfél támogatja az SMB 3.0 protokollt (például használata a Windows 10 vagy Windows Server 2016-os). Ha a 445-ös porton le van tiltva, vagy az Internetszolgáltató által a szervezet házirendje, Azure fájlszinkronizálás használhatja az Azure-fájlmegosztás eléréséhez.

## <a name="backup"></a>Biztonsági mentés
* <a id="backup-share"></a>**Készítsen biztonsági másolatot az Azure-fájl megosztása?**  
    Rendszeres használható [megosztása (előzetes verzió) pillanatképek](storage-how-to-use-files-snapshots.md) véletlen törlés elleni védelem. A 3. fél biztonsági mentési eszköz, amely egy csatlakoztatott fájlmegosztást készíthet biztonsági másolatot, vagy használja az AzCopy, RoboCopy. 

## <a name="share-snapshots"></a>A pillanatképek megosztása
### <a name="share-snapshots---general"></a>Megosztás pillanatképek – általános
* <a id="what-are-snaphots"></a>**Mi az, hogy a megosztás fájlpillanatkép?**  
    Azure fájlmegosztás fájlpillanatképek csak olvasható verziója, a fájlmegosztások létrehozását teszi lehetővé. Lehetővé teszi a tartalom vissza egy régebbi verziója ugyanennek a megosztásnak vagy Azure vagy a helyszíni további módosítások a másik helyre másolhatja. Megosztás pillanatkép kapcsolatos további információkért lásd [pillanatkép megosztása áttekintése](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>**A megosztás pillanatképeket tároló?**  
    Megosztás pillanatképek ugyanazt a tárfiókot, a fájlmegosztást tárolja.

* <a id="snapshot-perf-impact"></a>**Vannak a teljesítményre gyakorolt hatása?**  
    Megosztás pillanatképek nem rendelkezik a teljesítményigény.

* <a id="snapshot-consistency"></a>**Alkalmazás megosztása pillanatképek megegyeznek?**  
    Nem. Megosztás pillanatképek nincsenek alkalmazás konzisztens. Ürítse ki az írási műveletek az alkalmazásból a megosztáshoz való megosztás pillanatképének készítése előtt felhasználónak kell.

* <a id="snapshot-limits"></a>**Vannak-e a megosztás a pillanatképek számára vonatkozó korlátozások?**  
    Az Azure fájlokat továbbra is a 200 megosztás pillanatképek korlátozva van. Megosztás pillanatképek nem számítanak bele a fájlmegosztási kvóta így nincs megosztás korlát a megosztás pillanatképek által használt összes hely nem. Tárfiókok korlátai továbbra is érvényesek lesznek. 200 megosztás pillanatképek után korábbi pillanatképek ahhoz, hogy az új megosztás pillanatképeket törölni kell.

### <a name="create-share-snapshots"></a>Megosztás pillanatképeket
* <a id="file-snaphsots"></a>**Az egyes fájlok megosztási pillanatkép hozhat létre?**  
    Megosztás pillanatképek fájl fájlmegosztási szinten jönnek létre. A megosztás fájlpillanatkép állíthatja vissza a fájlokat, de fájlszintű megosztás pillanatképek nem hozható létre. Azonban ha elvégezte a megosztási szint megosztás pillanatképet, és meg szeretné tekinteni a megosztás pillanatképeket, amelyekben egy adott fájl módosult, akkor megteheti "Korábbi verziók" tapasztalatunkat csatlakoztatott Windows-megosztáson található. Tudassa velünk, ha van szükség egy fájl pillanatkép szolgáltatás [Azure fájlok UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encypted-snapshots"></a>**Titkosított fájlmegosztási megosztás pillanatképeket hozhat létre?**  
    Is pillanatképet egy megosztás, az Azure fájlmegosztások titkosítási rendelkező engedélyezve aktívan. Egy megosztás pillanatképből fájlok visszaállíthatja egy titkosított fájlmegosztás. Ha a megosztás titkosítva van, a megosztás pillanatkép is titkosítva lesznek.

* <a id="geo-redundant-snaphsots"></a>**A megosztás pillanatképek georedundáns vannak?**
    Megosztás pillanatkép, amelynek felvétel Azure fájlmegosztás azonos redundancia fog rendelkezni. Ha a fiókhoz kijelölt georedundáns tárolás (GRS) a megosztás pillanatkép fog is tárolhatók redundantly párosított régióban.

### <a name="manage-share-snapshots"></a>Megosztás pillanatképek kezeléséhez
* <a id="browse-snapshots-linux"></a>**Tallózhatja a Linux megosztás pillanatképeinek?**  
    Azure CLI 2.0 segítségével lista létrehozása, keresse meg és Linux-visszaállítás.

* <a id="copy-snapshots-to-other-storage-account"></a>**Átmásolhatók-e a megosztás pillanatképek során eltérő tárfiók?**  
    Megosztás pillanatképek fájljait átmásolhatja egy másik helyre, de nem a megosztás-pillanatképek magukat.

### <a name="restore-data-from-share-snapshots"></a>Megosztás származó adatok visszaállítása
* <a id="promote-share-snapshot"></a>**A kiinduló megosztáshoz való megosztás pillanatkép is előléptetni?**  
    Bármely kívánt célra megosztás pillanatképből csak másolhatja adatokat. A kiinduló megosztáshoz való megosztás pillanatkép azonban nem léptethető elő.

* <a id="restore-snapshotted-file-to-other-share"></a>**Állíthatók vissza adatokat a megosztás pillanatképből egy másik tárfiókkal?**  
    Igen. Egy megosztás pillanatképből fájlok lehet másolni az eredeti vagy egy másik helyre, amely azonos vagy eltérő tárfiók az azonos vagy különböző régiókban. Fájlok másolása a helyszíni vagy bármely más felhő.    
  
### <a name="cleanup-share-snapshot"></a>Tisztítás megosztás pillanatkép
* <a id="delete-share-keep-snapshots"></a>**Törölje a megosztást de nem ugyanazt a pillanatképeket?**
    Pedig nem fogják tudni törli a megosztást, ha rendelkezik active megosztás pillanatképek a megosztáshoz. Az API-k áll rendelkezésre megosztás pillanatképek együtt megosztás törlése, és azonos Azure-portálon is érhet el.

* <a id="delete-share-with-snapshots"></a>**Mi történik a megosztás pillanatképet, ha a Tárfiókot?**
    Ha törli a tárfiókot, a megosztás pillanatképek is törlődik.

## <a name="billing-and-pricing"></a>Számlázási és az árképzés terén
* <a id="vm-file-share-network-traffic"></a>**Egy Azure virtuális gép és egy Azure fájlmegosztás közötti hálózati forgalmat, amelyet az előfizetés külső sávszélességnek számít?**  
    Ha a fájlmegosztás és a virtuális gép ugyanazon Azure-régióban, akkor kimenő forgalomról közöttük szabad. Ha különböző régiókban, adatforgalom külső sávszélességnek minősül, fogjuk számlázni.

* <a id="share-snapshot-price"></a>**Milyen mértékű ugyanazt a pillanatképek költség?**
     Előzetes megosztás során pillanatkép-kapacitás nem számítjuk fel. Standard tárolási kilépő és tranzakciós költségek könyöklő alkalmazni. Általános rendelkezésre állás érdekében után kapacitás és a tranzakciók megosztás pillanatképen fogjuk számlázni.
     
     Megosztás pillanatképei növekményes jellegűek. A kiinduló megosztás pillanatkép a megosztás saját magát. A további megosztási pillanatképek növekményes, és csak a korábbi megosztás pillanatképből különbözeti tárolja. Csak a módosított tartalmat a kell fizetni. Ha egy megosztást az adatok 100 GiB rendelkezik, de csak 5 GiB megváltozott az utolsó megosztás pillanatképet készít, a megosztás pillanatkép fog csak 5 további GiB használ fel, és így lehetetlenné válik a számlázott csak 105 GiB. Lásd: [árazás lap](https://azure.microsoft.com/pricing/details/storage/files/) tranzakció és a szabványos kilépő díjakat olvashat.

## <a name="scale-and-performance"></a>Méretezés és teljesítmény
* <a id="files-scale-limits"></a>**A skálázási korlátai Azure fájlok**  
    Az Azure-fájlok méretezhetőségi és Teljesítménycélok információkért lásd: [Azure fájlok méretezhetőségi és Teljesítménycélok](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>**Azure-fájlok jelenleg kínál... tudom növelni a saját Azure fájlmegosztás méretével, mint nagyobb fájlmegosztást kell?**  
    Nem, a maximális egy Azure fájlmegosztás mérete 5 TiB. Ez a funkció jelenleg egy rögzített korlátját, amely jelenleg nem lehet beállítani. Rendszer dolgozik a megoldáson 100 TiBs a fájlmegosztás méretét, de nincs ütemtervek jelenleg megosztásához.

* <a id="open-handles-quota"></a>**Hány ügyfél egyidejűleg hozzáfér ugyanazt a fájlt?**  
    Nincs megnyitott leíróinak 2000 kvóta akár egyetlen fájlt. Miután 2000 megnyitott leíróinak, akkor hiba lép fel, amely kvóta elérésekor.

* <a id="zip-slow-performance"></a>**A teljesítmény nem volt kielégítő, amikor fájlokat próbáltam kicsomagolni Azure fájlokba. Mit tegyek?**  
    Nagyszámú fájlok átvitele Azure-fájlok, azt javasoljuk, hogy AzCopy (Windows, Linux/UNIX előzetes verzió) vagy Azure Powershell, ezek az eszközök a hálózati átvitel optimalizált.

* <a id="slow-perf-windows-81-2012r2"></a>**A Windows Server 2012 R2 vagy Windows 8.1 saját Azure fájlmegosztás már csatlakoztatása után, és a teljesítmény lassú - miért?**  
    Egy ismert probléma van, amikor egy Azure fájlmegosztás csatlakoztatása a Windows Server 2012 R2 és Windows 8.1, amely a 2014. április összegző frissítés a Windows 8.1 és Windows Server 2012 R2 lett telepítve. Győződjön meg róla, hogy Windows Server 2012 R2 és Windows 8.1 összes példánya van-e a javítás az optimális teljesítmény alkalmazott (természetesen mindig ajánlott véve a Windows Update szolgáltatáson keresztül Windows javításokat). További információkért vegye ki a KB-cikkben talál [csökkentheti a teljesítményt, amikor Azure fájlok fér hozzá a Windows 8.1 vagy Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Szolgáltatások és egyéb szolgáltatások együttműködés
* <a id="cluster-witness"></a>**Használható az Azure fájlmegosztással egy *fájl megosztása tanúsító* a Windows Server feladatátvevő fürt?**  
    Ez nem támogatott egy Azure fájlmegosztás. Ez az Azure Blob Storage tárolóban szolgáltatáshoz beállításával kapcsolatos további információkért lásd: [felhő tanúsító feladatátvevő fürt telepítése](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>**Csatlakoztathatok egy Azure fájlmegosztás Azure tároló példányhoz?** ?  
    Igen, az Azure fájlmegosztások egyaránt megőrizni az adatokat tároló példány élettartama meghaladja a képek remekül felhívják magukra beállítást. További információkért lásd: [csatlakoztatása az Azure fájlmegosztások Azure tároló osztályt](../../container-instances/container-instances-mounting-azure-files-volume.md).

* <a id="rest-rename"></a>**Van-e a REST API átnevezési művelettel?**  
    Jelenleg nem.

* <a id="nested-shares"></a>**Létrehozható beágyazott megosztás, azaz megosztás egy megosztásban?**  
    Nem. A fájlmegosztás a virtuális meghajtó, amelyet csatlakoztathat, így nem támogatott a beágyazott megosztás.

* <a id="ibm-mq"></a>**Az Azure Files használata az IBM MQ-val**  
    Az IBM kiadott egy útmutató dokumentumot az IBM MQ ügyfelei az általuk használt szolgáltatással Azure-fájlok konfigurálásakor. További információk: [How to setup IBM MQ Multi instance queue manager with Microsoft Azure File Service](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service) (Az IBM MQ többpéldányos üzenetsor-kezelőjének beállítása a Microsoft Azure File szolgáltatással).

## <a name="see-also"></a>Lásd még:
* [A Windows Azure fájlok problémák megoldásához](storage-troubleshoot-windows-file-connection-problems.md)
* [Azure-fájlok kapcsolatos problémák elhárítása a Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Hibaelhárítás az Azure fájlszinkronizálás (előzetes verzió)](storage-sync-files-troubleshoot.md)