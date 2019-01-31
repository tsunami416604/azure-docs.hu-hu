---
title: Gyakori kérdések (GYIK) az Azure Files |} A Microsoft Docs
description: Azure Files szolgáltatással kapcsolatos gyakori kérdésekre adott válaszokat megkeresése
services: storage
author: RenaShahMSFT
ms.service: storage
ms.date: 01/02/2019
ms.author: renash
ms.subservice: files
ms.openlocfilehash: b3329f591d8478499b8270eb8a211d311465b020
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55457020"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Az Azure Files kapcsolatos gyakori kérdések (GYIK)
[Az Azure Files](storage-files-introduction.md) teljes körűen felügyelt fájlmegosztást kínáló, amely az iparági szabványnak megfelelő keresztül érhető el a felhőben [Server Message Block (SMB) protokoll](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx). Azure-fájlmegosztások párhuzamosan csatlakoztathatja felhőbeli vagy helyszíni üzemelő példányok esetében a Windows, Linux és macOS. A Windows Server-gépek Azure-fájlmegosztások közel, ahol az adatok felhasználásának gyors hozzáférés az Azure File Sync használatával képes gyorsítótárazni.

Ez a cikk az Azure Files szolgáltatást és funkciót, beleértve az Azure File Sync az Azure Files kapcsolatos gyakori kérdésekre ad választ. Ha nem látja a választ a kérdésére, lépjen kapcsolatba velünk (a eszkalálásáról rendelésben) a következő csatornákon keresztül:

1. Ez a cikk a megjegyzéseket.
2. [Az Azure Storage-fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
3. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. A Microsoft ügyfélszolgálata. Hozhat létre egy új támogatási kérelmet az Azure Portalon, az a **súgó** lapon jelölje be a **súgó + támogatás** gombra, és válassza ki **új támogatási kérelem**.

## <a name="general"></a>Általános kérdések
* <a id="why-files-useful"></a>
**Hogyan érdemes az Azure Files?**  
   Az Azure Files segítségével fájlmegosztásokat hozhat létre a felhőben anélkül, hogy kezeléséért egy fizikai kiszolgálón, az eszköz vagy a készülék járó többletterhelést. Monoton munkát végzünk, beleértve az operációs rendszer frissítéseinek alkalmazása és a hibás lemezek. A forgatókönyvek, amelyek az Azure Files segítségével az kapcsolatos további információkért lásd: [akkor hasznos, miért az Azure Files](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>
**Mik azok a fájlok eléréséhez az Azure Files különböző módokon?**  
    Akkor is csatlakoztathatja a fájlmegosztást a helyi gépen az SMB 3.0 protokoll használatával, vagy használhatja a hasonló eszközök [Tártallózó](http://storageexplorer.com/) fájlmegosztásban lévő fájlok eléréséhez. Az alkalmazásból használhatja tároló ügyfélkódtárai, a REST API-k, a PowerShell vagy az Azure CLI a fájlok az Azure-fájlmegosztás eléréséhez.

* <a id="what-is-afs"></a>
**Mi az Azure File Sync?**  
    Az Azure File Sync használatával fájlmegosztásainak a szervezet az Azure Files között, miközben gondoskodik a rugalmasságát, teljesítményét és kompatibilitását a helyszíni fájlkiszolgálók. Az Azure File Sync a Windows Server rendszerű gépek az Azure-fájlmegosztás gyors gyorsítótáraivá alakítja át. Helyileg, az adatok eléréséhez a Windows Serveren elérhető bármely protokollt használhatja, beleértve az SMB, a hálózati fájlrendszer (NFS) és a File Transfer Protocol szolgáltatás (FTPS). Tetszőleges számú gyorsítótárak világszerte igény szerint is rendelkezhet.

* <a id="files-versus-blobs"></a>
**Miért célszerű használni az Azure-fájlmegosztások az Azure Blob storage és az adataim?**  
    Az Azure Files és az Azure Blob storage módjai mindkettő lehetővé teszi nagy mennyiségű adat tárolása a felhőben, de hasznosak lehetnek némileg különböző célokra. 
    
    Az Azure Blob storage nagy léptékben méretezhető, felhőbeli működésre, a strukturálatlan adatok tárolásához igénylő alkalmazások esetében hasznos. A maximális teljesítmény és méretezhetőség érdekében az Azure Blob storage egy egyszerűbb tárolás absztrakciós, mint egy valós fájlrendszer. Elérheti az Azure Blob storage csak REST-alapú klienskódtárak révén (vagy közvetlenül a REST-alapú protokollon keresztül).

    Az Azure Files egy kifejezetten egy fájlrendszer. Az Azure Files ismert és kedvelt a munka a helyi operációs rendszer összes fájl kivonatok rendelkezik. Azure Blob storage, például a az Azure Files egy REST-felület és a REST-alapú klienskódtárak kínál. Ellentétben az Azure Blob storage az Azure Files Azure-fájlmegosztások SMB hozzáférést biztosít. Az SMB használatával csatlakoztathatja Azure-fájlmegosztások közvetlenül Windows, Linux vagy MacOS rendszerű, helyszíni vagy felhőbeli VM-eken, kód írása és speciális illesztőprogramok csatolása a fájlrendszer nélkül. A helyszíni fájlkiszolgálókon lévő Azure-fájlmegosztások a gyors hozzáférés érdekében közel, ahol az adatokat használja az Azure File Sync használatával képes gyorsítótárazni. 
   
    Az Azure Files és az Azure Blob storage közötti különbségekről további részletes ismertetését lásd: [való használata az Azure Blob storage, az Azure Files és az Azure Disks](../common/storage-decide-blobs-files-disks.md). Azure Blob storage szolgáltatással kapcsolatos további tudnivalókért lásd: [Blob storage bemutatása](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**Miért célszerű használni az Azure-fájlmegosztások Azure-lemezek helyett?**  
    Az Azure Disks egy lemez egyszerűen egy lemez. Az Azure Disks érték lekéréséhez a lemez csatolása kell az Azure-ban futó virtuális géphez. Azure-lemezek minden, egy helyszíni kiszolgálón szeretné használni a lemez is használható. Egy operációs rendszer lemezként, lapozóterület-os vagy egy alkalmazás dedikált tárolására használható. Egy Azure-lemezek érdekes használja, ha egy fájlkiszolgáló ugyanazon a helyen, ahol az Azure-fájlmegosztás segítségével lehet használni a felhőben. Egy Azure Virtual Machines fájlkiszolgáló üzembe helyezésének módja a nagy teljesítményű kaphat a file storage az Azure-ban, ha a központi telepítési beállítások, amelyek jelenleg nem támogatottak az Azure Files (például az NFS protokoll támogatása vagy a prémium szintű storage) van szüksége. 

    Azonban a fájlkiszolgálót futtató Azure-lemezek háttér-tárolóként általában nem sokkal drágábbak, mint az Azure-fájlmegosztások használatával több okból is. Először a lemezes tárhely gyorsítótárpéldányok is külön kell fizetni egy vagy több Azure virtuális gépeken futó járó költségek. Másodszor is kell kezelni a virtuális gépek, amelyek a fájlkiszolgálón futtassa. Ha például Ön felelős az operációs rendszer frissítései. Végül végső soron az adatokat helyszíni gyorsítótárazott van szüksége, hogy, állítsa be, és kezelheti a replikációs technológiák, például az elosztott fájl replikációs (DFSR), győződjön meg, hogy történjen.

    Bevezetés az Azure Files és a egy fájlkiszolgálón, amely az Azure Virtual machines szolgáltatásban üzemeltetett (mellett az Azure-lemezek használatával háttér-tároló) a legjobb egyik módja, hogy egy fájlkiszolgálón, amely egy felhőalapú virtuális gép az Azure File Sync telepítése. Ha az Azure-fájlmegosztást a fájlkiszolgáló ugyanabban a régióban, felhőbeli rétegezés, és állítsa be a kötetet a szabad terület százalékos aránya (99 %) legfeljebb engedélyezheti. Ez biztosítja, hogy a minimális adatredundancia. Is használhatja az alkalmazásokat, a fájlkiszolgálók, az azt szeretné, például az NFS-protokollt igénylő alkalmazások támogatásához.

    A beállítás a egy nagy teljesítményű és magas rendelkezésre állású fájlkiszolgáló az Azure-ban való beállításának kapcsolatos információkért lásd: [telepítése IaaS virtuális gépek vendégfürtjének a Microsoft Azure-ban](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). Az Azure Files és az Azure-lemezek közötti különbségekről további részletes ismertetését lásd: [való használata az Azure Blob storage, az Azure Files és az Azure Disks](../common/storage-decide-blobs-files-disks.md). Az Azure Disks kapcsolatos további információkért lásd: [Azure Managed Disks – áttekintés](../../virtual-machines/windows/managed-disks-overview.md).

* <a id="get-started"></a>
**Hogyan kezdhetem Azure Files használatával?**  
   Az Azure Files Sajátítsa el könnyedén. Először [fájlmegosztás létrehozása](storage-how-to-create-file-share.md), és csatlakoztathatja az előnyben részesített operációs rendszerben: 

    * [Csatlakoztatás Windows az](storage-how-to-use-files-windows.md)
    * [Csatlakoztatás Linux az](storage-how-to-use-files-linux.md)
    * [Csatlakoztatás macOS az](storage-how-to-use-files-mac.md)

   Azure-fájlmegosztások telepítéséről részletesebb útmutató cserélje le a termelési fájlmegosztások a szervezetben, lásd: [Azure Files üzembe helyezésének megtervezése](storage-files-planning.md).

* <a id="redundancy-options"></a>
**Milyen tárolási adatredundanciával kapcsolatos lehetőségek támogat az Azure Files?**  
    Az Azure Files jelenleg támogatja a helyileg redundáns tárolás (LRS), a zónaredundáns tárolás (ZRS) és a georedundáns tárolás (GRS). Írásvédett georedundáns tárolást (RA-GRS) tárolás támogatása a jövőben azonban ütemtervek megosztásához jelenleg nem áll rendelkezésre.

* <a id="tier-options"></a>
**Milyen tárolási rétegek az Azure Files támogatottak?**  
    Az Azure Files jelenleg csak a standard szintű tárolási réteg támogatja. Nem kell, hogy az jelenleg támogatja a premium storage és a ritkán használt adatok megosztásához ütemtervek. 
    
    > [!NOTE]
    > Azure-fájlmegosztások nem hozható létre, csak blob storage-fiókok vagy a prémium szintű storage-fiókok.

* <a id="give-us-feedback"></a>
**Szeretném tekintse meg az Azure Files hozzáadott egy adott szolgáltatáshoz. Hozzáadhatja azt?**  
    Az Azure Files csapat érdekli minden visszajelzését, várjuk a szolgáltatással kapcsolatban. Adjon a szolgáltatással kapcsolatos kéréseit szavazhat [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)! Mi vagyunk Reméljük, delighting, számos új szolgáltatást.

## <a name="azure-file-sync"></a>Azure File Sync

* <a id="afs-region-availability"></a>
**Mely régiókban érhető el az Azure File Sync?**  
    A rendelkezésre álló régiók listájának találhatók a [régiók rendelkezésre állása](storage-sync-files-planning.md#region-availability) szakaszban az Azure File Sync tervezési útmutató. Folyamatosan hozzáadjuk a további régiók, beleértve a nem nyilvános régió támogatása.

* <a id="cross-domain-sync"></a>
**Tartalmazhat tartományhoz csatlakoztatott és a nem tartományhoz csatlakoztatott kiszolgálók ugyanazt a szinkronizálási csoportot?**  
    Igen. Szinkronizálási csoport tartalmazhat kiszolgálói végpontot, amely rendelkezik a különböző Active Directory-csoporttagságok, még akkor is, ha azok nem a tartományhoz. Bár technikailag működik ez a konfiguráció, nem ajánlott ennek egy tipikus konfigurációja, mivel hozzáférés-vezérlési listák (ACL) a fájlok és mappák több kiszolgálón definiált előfordulhat, hogy nem tudja majd érvénybe lépteti a szinkronizálási csoport többi kiszolgálóján. A legjobb eredmények érdekében javasoljuk, hogy ugyanazon Active Directory-erdőben lévő kiszolgálók között, kiszolgálók, amelyek eltérő Active Directory-erdőben találhatók, de amelyek létesített megbízhatósági kapcsolatokkal vagy kiszolgálón, amely nem egy tartomány között szinkronizálása. Ajánlott elkerülni, illetve ezeket a konfigurációkat.

* <a id="afs-change-detection"></a>
**Hoztam létre egy fájlt közvetlenül a saját Azure-fájlmegosztást az SMB használatával, vagy a portálon. Mennyi ideig tart a fájlt a kiszolgálóra a szinkronizálási csoport szinkronizálása?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**Ha ugyanazt a fájlt módosul a két kiszolgáló körülbelül egy időben, mi történik?**  
    Az Azure File Sync egyszerű ütközésfeloldás stratégiát alkalmaz: mindkét módosítások nálunk a fájlokra, amelyek egy időben két kiszolgálóra változnak. A legutóbb írt módosítás megtartja az eredeti fájlnevet. A régebbi fájl rendelkezik a "forrás" gép és a ütközés számot a névhez. Ez a besorolás a következő: 
   
    \<FileNameWithoutExtension\>-\<MachineName\>\[-#\].\< ext\>  

    Például az első ütközése CompanyReport.docx válnak CompanyReport-CentralServer.docx hol lépett fel a régebbi írási CentralServer esetén. A második ütközés CompanyReport-CentralServer-1.docx neve.

* <a id="afs-storage-redundancy"></a>
**Georedundáns tárolás az Azure File Sync támogatott?**  
    Igen, az Azure Files támogatja mind a helyileg redundáns tárolás (LRS), és a georedundáns tárolás (GRS). Ha GRS párosított régiók közötti feladatátvétel esetén javasoljuk az új régióban gyökérkönyvtárral csak az adatok biztonsági másolata. Az Azure File Sync nem kezdődik automatikusan az új elsődleges régióba való szinkronizálását. 

* <a id="sizeondisk-versus-size"></a>
**Miért nem a *lemezterület* fájl egyezést tulajdonsága a *mérete* tulajdonság használata az Azure File Sync után?**  
 Lásd: [ismertetése, Felhőbeli Rétegezés](storage-sync-cloud-tiering.md#sizeondisk-versus-size).

* <a id="is-my-file-tiered"></a>
**Hogyan állapítható meg, hogy egy fájl tartozik lett a rétegzett?**  
 Lásd: [ismertetése, Felhőbeli Rétegezés](storage-sync-cloud-tiering.md#is-my-file-tiered).

* <a id="afs-recall-file"></a>**A használni kívánt fájl tartozik lett rétegzett. Hogyan lehet visszahívása vele helyi lemezre a fájlt?**  
 Lásd: [ismertetése, Felhőbeli Rétegezés](storage-sync-cloud-tiering.md#afs-recall-file).


* <a id="afs-force-tiering"></a>
**Hogyan kényszeríthetem egy fájl vagy címtár helyezhető el?**  
 Lásd: [ismertetése, Felhőbeli Rétegezés](storage-sync-cloud-tiering.md#afs-force-tiering).

* <a id="afs-effective-vfs"></a>
**Hogyan van *szabad terület a köteten* értelmezni, ha egy köteten van több kiszolgálói végpontot?**  
 Lásd: [ismertetése, Felhőbeli Rétegezés](storage-sync-cloud-tiering.md#afs-effective-vfs).

* <a id="afs-files-excluded"></a>
**Mely fájlok vagy mappák automatikusan kizáródnak az Azure File Sync?**  
    Alapértelmezés szerint az Azure File Sync kizárja a következő fájlokat:
    * Desktop.ini
    * thumbs.db
    * ehthumbs.db
    * ~$\*.\*
    * \*.laccdb
    * \*.tmp
    * 635D02A9D91C401B97884B82B3BCDAEA.\*

    Alapértelmezés szerint is kizárja a következő mappák:

    * \System volume Information
    * \$RECYCLE.BIN
    * \SyncShareState

* <a id="afs-os-support"></a>
**Használhatom az Azure File Sync Windows Server 2008 R2, a Linux vagy a hálózati tárolóeszközök (NAS) eszközömet?**  
    Az Azure File Sync jelenleg csak a Windows Server 2016 és a Windows Server 2012 R2 támogatja. Jelenleg nem áll rendelkezésre más terveket osztjuk meg is, de vagyunk az ügyfelek igényei szerint további platformokat támogatja. Tudassa velünk [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) milyen platformok szeretné támogatásához.

* <a id="afs-tiered-files-out-of-endpoint"></a>
**Miért léteznek a rétegzett fájlok kívül a kiszolgálói végpont névtér?**  
    Azure File Sync ügynök 3-as verziójú, mielőtt az Azure File Sync blokkolja a rétegzett fájlok a kiszolgálói végpont kívüli, de a kiszolgálói végpont ugyanazon a köteten az áthelyezés. Másolási műveletek – a rétegzett fájlok áthelyezése és áthelyezi a rétegzett más köteteket is nem érinti. Ez a jelenség oka implicit feltételezve, hogy a Fájlkezelőben és a többi Windows API-k, hogy ugyanazon a köteten műveletekre (majdnem) instanenous műveletek átnevezése volt. Ez azt jelenti, a kurzor elvégzi a Fájlkezelőben, vagy más áthelyezési módszerek (mint például a parancssor vagy PowerShell) jelennek meg nem válaszol, amíg az Azure File Sync visszahívja az adatok a felhőből. Kezdve [Azure File Sync ügynök verziója 3.0.12.0](storage-files-release-notes.md#supported-versions), az Azure File Sync lehetővé teszi a áthelyezése egy rétegzett fájlt a kiszolgálói végpont kívül. A korábban említett engedélyezi a rétegzett fájl létezik és a egy rétegzett fájlt a kiszolgálói végpont kívül, és ezután – a háttérben a fájl lehívása negatív hatások elkerülése azt. Ez azt jelenti, hogy áthelyezi az ugyanazon kötet instaneous, és visszaírni a lemezre a fájlt, az Áthelyezés befejezése után a munkát végzünk. 

* <a id="afs-do-not-delete-server-endpoint"></a>
**Problémát tapasztalok egy probléma az Azure File Sync használatával a my server (szinkronizálási szolgáltatás cloud rétegezési, stb). E távolítsa el, majd hozza létre újra a kiszolgálói végpontot?**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
**Áthelyezhető a társzinkronizálási szolgáltatás és/vagy a storage-fiók egy másik erőforráscsoportba vagy előfizetésbe?**  
   Igen, a társzinkronizálási szolgáltatás és/vagy a storage-fiókot is áthelyezhető másik erőforráscsoportba vagy előfizetésbe meglévő Azure AD-bérlőn belül. Ha a tárfiókot, a hibrid File Sync szolgáltatásbeli hozzáférést a tárfiókhoz kell (lásd: [biztosítása érdekében az Azure File Sync hozzáfér a tárfiókhoz](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Az Azure File Sync nem támogatja az előfizetés áthelyezése egy másik Azure AD-bérlővel.
    
* <a id="afs-ntfs-acls"></a>
**Azure File Sync megőrzése nem könyvtár szinten NTFS hozzáférés-vezérlési listák Azure Files szolgáltatásban tárolt adatok együtt?**

    Az NTFS hozzáférés-vezérlési listák végzett a kiszolgálók az Azure File Sync metaadatokként megmaradnak a helyi fájlból. Az Azure Files nem támogatja a hitelesítés az Azure AD hitelesítő adatait az Azure File Sync szolgáltatás által felügyelt fájlmegosztások eléréséhez.
    
## <a name="security-authentication-and-access-control"></a>Biztonság, hitelesítés és hozzáférés-vezérlés
* <a id="ad-support"></a>
**Az Active Directory-alapú hitelesítés és hozzáférés-vezérlés az Azure Files által támogatott?**  
    
    Igen, támogatja az Azure Files a azonosító-alapú hitelesítés és hozzáférés-vezérlés az Azure Active Directoryval (Azure AD) (előzetes verzió). Az Azure Files SMB-n keresztül az Azure AD authentication használ az Azure Active Directory Domain Services engedélyezése a tartományhoz csatlakoztatott virtuális gépek eléréséhez, megosztások, könyvtárak és fájlok használatával az Azure AD hitelesítő adatait. További részletekért lásd: [áttekintése az Azure Active Directory hitelesítési SMB-n keresztül az Azure Files (előzetes verzió)](storage-files-active-directory-overview.md). 

    Az Azure Files kezelése a hozzáférés-vezérlés két további lehetőséget kínál:

    - Közös hozzáférésű jogosultságkódok (SAS) használatával, amely van konkrét engedélyük, és amely érvényes a megadott időtartam alatt jogkivonatokat hoz létre. Például egy jogkivonatot a csak olvasási hozzáférést egy adott fájlt, amely rendelkezik egy 10 perc elteltével is létrehozhat. Csak olvasási hozzáféréssel bárki, aki ismeri a jogkivonatot, amíg a jogkivonat érvényes rendelkezik, amely a fájl 10 percnek. Jelenleg a közös hozzáférési aláírási kulcsokat csak a REST API-val vagy a klienskódtárak támogatottak. Csatlakoztatnia kell az Azure-fájlmegosztás SMB-n keresztül a storage-fiók kulcsainak használatával.

    - Az Azure File Sync megőrzi, és minden tulajdonosi hozzáférés-vezérlési listák vagy DACL-EK, replikálja az (akár Active Directory-alapú vagy helyi) az összes kiszolgálói végpontot, amely a szinkronizált. A Windows Server Active Directoryval már hitelesíthetők, mert az Azure File Sync teljes körűen támogatja az Active Directory-alapú hitelesítésre amíg hatékony stop-gap lehetőség, és ACL-támogatás érkezik.

* <a id="ad-support-regions"></a>
**Érhető el az előzetes verzió, az Azure AD SMB-n keresztül az Azure Files számára az összes Azure-régióban?**

    Az előzetes verzióra az összes nyilvános régióban érhető el.

* <a id="ad-support-on-premises"></a>
**Támogatja az Azure AD-hitelesítés SMB-n keresztül az Azure Files (előzetes verzió) hitelesítés a helyszíni gépeket az Azure AD-vel?**

    Nem, az Azure Files nem támogatja a helyszíni gépeket az Azure AD-hitelesítés az előzetes kiadásban.

* <a id="ad-support-devices"></a>
**Biztosítja az Azure AD hitelesítési SMB-n keresztül az Azure Files (előzetes verzió) támogatás SMB hozzáférés eszközök Azure AD hitelesítő adataival csatlakozik, vagy regisztrálva az Azure AD?**

    Nem, ez a forgatókönyv nem támogatott.

* <a id="ad-support-rest-apis"></a>
**REST API-kat támogató Get/Set/másolás/könyvtárfájl NTFS hozzáférés-vezérlési listák vannak?**

    Az előzetes kiadás nem támogatja a REST API-kkal kapni, állítsa be, vagy másolja be az NTFS hozzáférés-vezérlési listák könyvtárak és fájlok.

* <a id="ad-vm-subscription"></a>
**Az Azure AD hitelesítő adatait egy virtuális gépről egy másik előfizetésben elérhető számomra az Azure Files?**

    Ha az előfizetés, amely alatt a fájlmegosztás üzembe lesz helyezve az Azure AD-bérlőhöz, a virtuális gép tartomány részeként csatlakoztatva, majd az Azure Files Azure AD hitelesítő adatokkal érheti el az Azure AD tartományi szolgáltatások deploymnet társítva. A korlátozás nem az előfizetés a várakozásnak, de a társított Azure ad-bérlőben.    
    
* <a id="ad-support-subscription"></a>
**Lehet engedélyezni az Azure AD-hitelesítés SMB-n keresztül az Azure Files számára az Azure AD-bérlő, amely különbözik az elsődleges bérlőt, amelyhez társítva a fájlmegosztás?**

    Az Azure Files nem, csak az Azure AD-integráció az Azure AD-bérlő, amely ugyanahhoz az előfizetéshez, mivel a fájlmegosztás található támogatja. Lehet, hogy csak egy előfizetéssel társított Azure AD-bérlővel.

* <a id="ad-linux-vms"></a>
**Azure AD-hitelesítés SMB-n keresztül az Azure Files (előzetes verzió) támogatja a Linux rendszerű virtuális gépek?**

    Hitelesítés a Linux rendszerű virtuális gépek nem, nem támogatott az előzetes kiadásban.

* <a id="ad-aad-smb-afs"></a>
**Az Azure AD-hitelesítés hasznosíthatom SMB képességek kezeli az Azure File Sync-fájlmegosztásokon keresztül?**

    Nem, az Azure Files nem támogatja az Azure File Sync által felügyelt fájlmegosztások megőrzi az NTFS hozzáférés-vezérlési listák. A fájl hozzáférés-vezérlési listák végezni a helyszíni fájlkiszolgálók megmaradnak az Azure File Sync. Bármely NTFS hozzáférés-vezérlési listák ellen az Azure Files natív módon konfigurált az Azure File Sync szolgáltatás felülíródik. Emellett az Azure Files nem támogatja a fájlmegosztások az Azure File Sync szolgáltatás kezeli a hozzáférést a hitelesítés az Azure AD hitelesítő adatait.

* <a id="encryption-at-rest"></a>
**Hogyan tudok biztosíthatja, hogy az Azure-fájlmegosztás titkosítása?**  

    Az Azure Storage Service Encryption az összes régióban alapértelmezés szerint engedélyezése folyamatban van. Ezekben a régiókban nem kell minden olyan műveleteket, engedélyezze a titkosítást. Más régiókban, lásd: [kiszolgálóoldali titkosítás](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>
**Hogyan is biztosít egy webes böngésző használatával egy adott fájlhoz történő hozzáférés?**  

    Közös hozzáférésű jogosultságkódok használatával, amely van konkrét engedélyük, és amely érvényes a megadott időtartam alatt jogkivonatokat hoz létre. Ha például egy jogkivonatot, amely egy adott fájl csak olvasható hozzáférést biztosít egy megadott ideig is létrehozhat. Bárki, aki ismeri az URL-címet is elérheti a fájl bármely webböngészőben amíg a token érvényes. Egyszerűen létrehozhat egy közös hozzáférésű jogosultságkód kulcsot egy felhasználói felületen, mint a Storage Explorer.

* <a id="file-level-permissions"></a>
**Ennyi az egész lehetséges adja meg a csak olvasási vagy csak írási engedélyeket a megosztáson belüli mappákhoz?**  

    Csatlakoztatja a fájlmegosztást SMB használatával, ha nem rendelkezik engedélyekkel mappaszintű felett. Azonban a REST API vagy klienskódtárak használatával hoz létre egy közös hozzáférésű jogosultságkódot, csak olvasható, vagy csak írási engedélyeket a a megosztáson belüli mappákhoz is megadhat.

* <a id="ip-restrictions"></a>
**Megvalósíthat egy Azure-fájlmegosztást az IP-korlátozások?**  

    Igen. A hozzáférést az Azure-fájlmegosztást a storage-fiók szintjén korlátozható. További információkért lásd: [konfigurálása az Azure Storage-tűzfalak és virtuális hálózatok](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>
**Milyen adatok megfelelőségi szabályzatok támogatja az Azure Files?**  

   Az Azure Files fut. a más tárolási szolgáltatásokra az Azure Storage-ban használt azonos tárolási architektúrával. Az Azure Files az azonos megfelelőségi szabályzatok az egyéb Azure storage-szolgáltatások által használt vonatkozik. Az Azure Storage-adatok megfelelőségi kapcsolatos további információkért olvassa el [Azure Storage-megfelelőségi ajánlatok](https://docs.microsoft.com/azure/storage/common/storage-compliance-offerings), és nyissa meg a [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx).

## <a name="on-premises-access"></a>Helyszíni hozzáférés
* <a id="expressroute-not-required"></a>
**Kell az Azure Files csatlakozás az Azure ExpressRoute használatával vagy az Azure File Sync használata a helyszínen?**  

    Nem. Az ExpressRoute nem szükséges Azure-fájlmegosztás eléréséhez. Ha a rendszer csatlakoztatása egy Azure-fájlmegosztás közvetlenül a helyszínen, az összes, amely szükséges az, hogy a 445-ös (TCP, kimenő) nyissa meg az internet-hozzáférés (Ez az SMB által való kommunikációra használt port). Ha az Azure File Sync használata esetén szükséges csak HTTPS-hozzáféréshez (nem szükséges SMB) a 443-as (TCP, kimenő) porton. Azonban Ön *is* bármelyik hozzáférést az expressroute-ot használni.

* <a id="mount-locally"></a>
**Hogyan csatlakoztathatom Azure-fájlmegosztás a helyi gépemen?**  

    A fájlmegosztás akkor is csatlakoztathatja az SMB protokoll használatával, ha a 445-ös (TCP, kimenő) meg nyitva, és az ügyfél támogatja az SMB 3.0 protokollt (például, ha a Windows 10-es vagy Windows Server 2016 használ). Ha a 445-ös port le van tiltva, a szervezeti házirend vagy az Internetszolgáltató által, az Azure File Sync használatával az Azure-fájlmegosztás eléréséhez.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**Hogyan oszthatom saját Azure-fájlmegosztások biztonsági mentése meg?**  
    Rendszeres használható [megosztási pillanatképek](storage-snapshots-files.md) véletlen törlés elleni védelem. Is használhatja az AzCopy, Robocopy vagy egy külső biztonsági mentési eszköz, amely egy csatlakoztatott fájlmegosztás készíthető. Az Azure Backup kínál az Azure Files biztonsági mentése. Tudjon meg többet [biztonsági mentése az Azure-fájlmegosztások az Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-files).

## <a name="share-snapshots"></a>Megosztási pillanatképek

### <a name="share-snapshots-general"></a>Megosztási pillanatképek: Általános kérdések
* <a id="what-are-snaphots"></a>
**Mik azok a fájlmegosztások pillanatképei?**  
    Az Azure fájlmegosztási pillanatképeket segítségével hozza létre a fájlmegosztások csak olvasható verzióját. Is használhatja az Azure Files a tartalom vissza egy korábbi másolása máshová az Azure-ban vagy a helyszíni további módosításokat a megosztáshoz. Megosztási pillanatképek kapcsolatos további információkért tekintse meg a [pillanatkép áttekintése megosztása](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>
**A megosztási pillanatképek tároló?**  
    Megosztási pillanatképek az adott tárfiók, mivel a fájlmegosztás vannak tárolva.

* <a id="snapshot-perf-impact"></a>
**Vannak-e bármilyen teljesítményre gyakorolt hatása a megosztási pillanatképek?**  
    Megosztási pillanatképek nem kell semmilyen teljesítménybeli terhelést okoz.

* <a id="snapshot-consistency"></a>
**Megosztási pillanatképek alkalmazáskonzisztens vannak?**  
    Nem, megosztási pillanatképek használata nem alkalmazás-konzisztens. A felhasználó az alkalmazás a megosztáshoz a megosztási pillanatkép kiléptetése előtt kell kiüríti az írási műveletek.

* <a id="snapshot-limits"></a>
**Használhatom a megosztási pillanatképek száma korlátozva van?**  
    Igen. Az Azure Files őrizheti meg egy legfeljebb 200 megosztási pillanatképek. Megosztási pillanatképek nem számítanak felé a fájlmegosztás kvótájának, így a teljes terület, a fájlmegosztási pillanatképekből által használt fájlmegosztásonként korlátlan. Tárfiókok korlátai továbbra is érvényesek. Után 200 megosztási pillanatképek törölnie kell a korábbi pillanatképek új megosztási pillanatképek létrehozása.

* <a id="snapshot-cost"></a>
**IP-címek fenntartási tegye meg a pillanatképek költség?**  
    Standard szintű tranzakció, és standard tárterület költségeit pillanatkép vonatkoznak. A pillanatképeket növekményes jellegűek. A kiinduló pillanatkép maga a megosztást. Az ezt követő pillanatképek növekményes, és csak fogja tárolni a korábbi pillanatképből a különbözeti. Ez azt jelenti, hogy az új módosításokkal, amely látható lesz a számlán minimális, ha a munkaterhelés forgalma minimális lesz. Lásd: [díjszabási oldalunkon](https://azure.microsoft.com/pricing/details/storage/files/) szabványos Azure Files a díjszabásról. A megosztási pillanatképek által felhasznált méret nézze módja a számlázott kapacitás összevetésével ma használt kapacitás. Dolgozunk a javítása, a jelentés azokat az eszközöket.

* <a id="ntfs-acls-snaphsots"></a>
**Az NTFS hozzáférés-vezérlési listák a könyvtárak és fájlok a megosztási pillanatképek őrzött?**
    Az NTFS hozzáférés-vezérlési listák könyvtárak és fájlok megmaradnak a megosztási pillanatképek.

### <a name="create-share-snapshots"></a>Megosztási pillanatképek létrehozása
* <a id="file-snaphsots"></a>
**Hozhat létre az egyes fájlok megosztási pillanatkép?**  
    Megosztási pillanatképek hozhatók létre, a fájl megosztási szintje. Visszaállíthatja az egyes fájlok megosztási pillanatképből, de a fájlszintű megosztási pillanatképek nem hozható létre. Azonban, ha elvégezte a megosztásszintű megosztási pillanatkép, és meg szeretné tekinteni egy adott fájl módosított rendelkező megosztási pillanatképek, ezt megteheti a **korábbi verziók** csatlakoztatott Windows-megosztáson található. 
    
    Ha egy fájl pillanatkép funkció, ossza meg velünk [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encrypted-snapshots"></a>
**Egy titkosított fájlmegosztás megosztási pillanatképeket hozhat létre?**  
    Azure-fájlmegosztások titkosítás inaktív állapotban engedélyezett rendelkező megosztási pillanatképek is igénybe vehet. Fájlok megosztási pillanatképből egy titkosított megosztás visszaállítása. Ha a megosztás titkosítva van, a megosztási pillanatképek is titkosítva.

* <a id="geo-redundant-snaphsots"></a>
**A megosztási pillanatképek georedundáns vannak?**  
    Megosztási pillanatképek Azure-fájlmegosztás, amelynek adatpontjára, az azonos redundanciával rendelkezik. Ha kiválasztotta a georedundáns tárolás a fiók, a megosztási pillanatkép is redundantly tárolódik a párosított régióra.

### <a name="manage-share-snapshots"></a>Megosztási pillanatképek kezelése
* <a id="browse-snapshots-linux"></a>
**Megnyithatja a megosztási pillanatképek Linux rendszerben?**  
    Azure CLI segítségével létrehozása, listázása, keresse meg és állítsa vissza a megosztási pillanatképek Linux rendszeren.

* <a id="copy-snapshots-to-other-storage-account"></a>
**Képes a megosztási pillanatképek is másolja egy másik tárfiókba?**  
    Másolhat fájlokat a megosztási pillanatképek egy másik helyre, de a megosztási pillanatképek maguk nem lehet másolni.

### <a name="restore-data-from-share-snapshots"></a>Adatok visszaállítása megosztási pillanatképekből
* <a id="promote-share-snapshot"></a>
**Előléptethet egy megosztási pillanatképre annak a kiinduló megosztást?**  
    Adatokat egy megosztás pillanatképéből bármely más célhelyre másolja. Egy megosztási pillanatképre annak az alapszintű megosztás nem léptethető elő.

* <a id="restore-snapshotted-file-to-other-share"></a>
**Vissza tudok állítani biztonsági adatok a megosztás pillanatképéből, amely eltérő tárfiókot használ?**  
    Igen. Egy megosztás pillanatképéből fájlokat az eredeti helyre vagy egy másik helyre, amely tartalmazza ugyanazt a tárfiókot, vagy egy másik tárfiókot, vagy ugyanabban a régióban, vagy különböző régiókban lehet másolni. Akkor is tud fájlokat másolni a helyszíni helyre, vagy bármely más felhőbe.    
  
### <a name="clean-up-share-snapshots"></a>Megosztási pillanatképek törlése
* <a id="delete-share-keep-snapshots"></a>
**Törli a megosztást azonban nem a saját megosztási pillanatképek törlése?**  
    Ha aktív megosztási pillanatképek a megosztáson található, a megosztás nem törölhető. API-k segítségével, és a megosztás megosztási pillanatképek törlése. Is törölheti a megosztási pillanatképek és a megosztás, az Azure Portalon is.

* <a id="delete-share-with-snapshots"></a>
**Mi történik a megosztási pillanatképek, ha a storage-fiókomat?**  
    Ha törli a tárfiókot, a megosztási pillanatképek is törlődnek.

## <a name="billing-and-pricing"></a>Számlázási és díjszabás
* <a id="vm-file-share-network-traffic"></a>
**Nem a hálózati forgalom egy Azure virtuális gép és egy Azure-beli fájlmegosztás közötti külső sávszélesség, amely az előfizetés díját?**  
    Ha a fájlmegosztás és a virtuális gép ugyanazon Azure-régióban, nincs a fájlmegosztás és a virtuális gép közötti adatforgalom díjmentes. Ha a fájlmegosztás és a virtuális gép eltérő régióban van, közöttük a forgalom külső sávszélességnek számítjuk fel.

* <a id="share-snapshot-price"></a>
**IP-címek fenntartási tegye meg a pillanatképek költség?**  
     Előzetes verzióban nem számítunk fel díjat megosztási pillanatkép-kapacitás. Standard szintű storage kimenő forgalom és tranzakciós költségek a alkalmazni. Általános elérhetőségét követően kapacitás és a tranzakciókért a megosztási pillanatképek előfizetések számítunk ki.
     
     Megosztási pillanatképek olyan növekményes jellegűek. A kiinduló megosztási pillanatkép maga a megosztást. Az összes későbbi megosztási pillanatképek növekményes, és csak a különbség az előző megosztás pillanatképéből tárolásához. Fizetnie csak a módosított tartalmat. Ha egy megosztást a 100 GB adat van, de csak 5 GiB az utolsó megosztási pillanatkép óta megváltozott, a megosztási pillanatképek csak 5 további GiB használ fel, és 105 GiB számlázzuk ki. Tranzakciók és a standard szintű kimenő forgalom költségeit kapcsolatos további információkért lásd: a [díjszabási oldalunkon](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="scale-and-performance"></a>Méretezés és teljesítmény
* <a id="files-scale-limits"></a>
**Mik azok a skálázási korlátokat az Azure Files?**  
    Az Azure Files méretezhetőségi és teljesítménycéljai kapcsolatos információkért lásd: [Azure Files méretezhetőségi és teljesítménycéljai](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>
**Az Azure Files jelenleg érhetők el, mint nagyobb fájlmegosztás van szükségem. Növelheti a saját Azure-fájlmegosztás méretét?**  
    Nem. Az Azure-fájlmegosztás maximális mérete 5 Tib-ra. Ez jelenleg rögzített korlátja, amely azt nem lehet beállítani. Folyamatban van egy megoldás 100 Tib-ra a megosztás méretének növeléséhez, de ütemtervek megosztásához jelenleg nem áll rendelkezésre.

* <a id="open-handles-quota"></a>
**Hány ügyfelek hozzáférhetnek a egyszerre ugyanazt a fájlt?**   
    Nincs egyetlen fájl 2000 megnyitott kezelőkkel-kvótát. Ha 2000 megnyitott leíróinak, egy hibaüzenet jelenik meg, amely szerint a kvóta elérése.

* <a id="zip-slow-performance"></a>
**A teljesítmény akkor lassú, ha az Azure Files fájlokat kicsomagolni a lemezen. Mit tegyek?**  
    Nagy mennyiségű fájlt át az Azure Files, azt javasoljuk, hogy használja-e AzCopy (Windows, Linux és UNIX rendszerekhez készült előzetes verzióban érhető el) vagy az Azure PowerShell. Ezek az eszközök hálózati átvitelt lettek optimalizálva.

* <a id="slow-perf-windows-81-2012r2"></a>
**Miért van a teljesítmény lassú, miután I saját Azure-fájlmegosztás csatlakoztatása a Windows Server 2012 R2 vagy Windows 8.1?**  
    Egy ismert probléma van, amikor az Azure-fájlmegosztás csatlakoztatása a Windows Server 2012 R2 és Windows 8.1. A probléma volt javítani a 2014. április összegző frissítés a Windows 8.1 és Windows Server 2012 R2. Az optimális teljesítmény érdekében győződjön meg arról, hogy a Windows Server 2012 R2 és Windows 8.1 összes példánya rendelkezik-e a alkalmazni a javítást. (Mindig küld Windows javításai a Windows Update szolgáltatáson keresztül.) További információkért lásd: a Microsoft Tudásbázis társított [csökkentheti a teljesítményt, amikor az Azure Files fér hozzá a Windows 8.1 vagy Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Funkciók és más szolgáltatásokkal való együttműködés
* <a id="cluster-witness"></a>
**Használható a saját Azure-fájlmegosztást, egy *tanúsító fájlmegosztást* saját Windows Server feladatátvevő fürt?**  
    Ez a konfiguráció jelenleg nem támogatott az Azure-fájlmegosztások. Állítsa be ezt az Azure Blob storage szolgáltatásra kapcsolatos további információkért lásd: [Felhőbeli tanúsító telepítése feladatátvevő fürt](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>
**Csatlakoztathatok egy Azure-fájlmegosztást az egy Azure-tárolópéldányon?**  
    Azure-fájlmegosztások Igen, akkor érdemes választani, ha meg szeretné megőrizni az adatokat tárolópéldány időtartama túl. További információkért lásd: [csatlakoztatása egy Azure-fájlmegosztás az Azure Container instances szolgáltatással](../../container-instances/container-instances-mounting-azure-files-volume.md).

* <a id="rest-rename"></a>
**Van-e a REST API-ban átnevezési művelettel?**  
    Jelenleg nem.

* <a id="nested-shares"></a>
**Állítható be a beágyazott megosztás? Más szóval olyan megosztás egy megosztásban?**  
    Nem. A fájlmegosztás *van* a virtuális meghajtó, amelyet csatlakoztathat, így a beágyazott megosztás nem támogatottak.

* <a id="ibm-mq"></a>
**Hogyan használhatom az Azure Files az IBM MQ-val?**  
    Az IBM kiadott egy dokumentumot, amelyek segítségével az IBM MQ-ügyfelek konfigurálása az Azure Files az IBM-szolgáltatással. További információkért lásd: [beállítása az IBM MQ többpéldányos üzenetsor-kezelő a Microsoft Azure Files szolgáltatással](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).

## <a name="see-also"></a>Lásd még
* [A Windows Azure-fájlok hibaelhárítása](storage-troubleshoot-windows-file-connection-problems.md)
* [A Linux Azure-fájlok hibaelhárítása](storage-troubleshoot-linux-file-connection-problems.md)
* [Az Azure File Sync hibaelhárítása](storage-sync-files-troubleshoot.md)
