---
title: Gyakori kérdések (GYIK) a Azure Files | Microsoft Docs
description: Válaszokat talál a Azure Filesekkel kapcsolatos gyakori kérdésekre.
author: roygara
ms.service: storage
ms.date: 02/19/2020
ms.author: rogarana
ms.subservice: files
ms.topic: conceptual
ms.openlocfilehash: c6503f2782832b7155c0c081aab9769296e08a8e
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565060"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Gyakran ismételt kérdések (GYIK) a Azure Filesról
A [Azure Files](storage-files-introduction.md) teljes körűen felügyelt fájlmegosztást biztosít a felhőben, amely az iparági szabványnak megfelelő [SMB protokollon](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)keresztül érhető el. Az Azure-fájlmegosztás párhuzamosan csatlakoztatható a Felhőbeli vagy a Windows, Linux és macOS rendszerű helyszíni környezetekhez. Az Azure-fájlmegosztás a Windows Server rendszerű gépeken is gyorsítótárazható a Azure File Sync használatával a gyors eléréshez, ahol az adott adatforgalomhoz közeledik.

Ez a cikk a Azure Files szolgáltatásokkal és funkciókkal kapcsolatos gyakori kérdésekre ad választ, beleértve a Azure File Sync használatát Azure Files használatával. Ha nem látja a választ a kérdésére, felveheti velünk a kapcsolatot a következő csatornákon keresztül (növekvő sorrendben):

1. A cikk megjegyzések szakasza.
2. [Azure Storage-fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
3. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Microsoft ügyfélszolgálata. Új támogatási kérelem létrehozásához a Azure Portal **Súgó** lapján kattintson a **Súgó + támogatás** gombra, majd válassza az **új támogatási kérelem**lehetőséget.

## <a name="general"></a>Általános kérdések
* <a id="why-files-useful"></a>
  **hogyan Azure Files hasznos?**  
   A Azure Files használatával hozhat létre fájlmegosztást a felhőben anélkül, hogy a felelős a fizikai kiszolgáló, eszköz vagy berendezés terhelésének kezeléséért. Az Ön számára monoton munkát végezünk, beleértve az operációs rendszer frissítéseinek alkalmazását és a hibás lemezek cseréjét. Ha többet szeretne megtudni azokról a forgatókönyvekről, amelyeket a Azure Files segíthetnek, tekintse meg a [miért Azure Files hasznos](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>
  , **hogy milyen különböző módokon férhet hozzá a Azure Files fájljaihoz?**  
    Az SMB 3,0 protokoll használatával csatlakoztathatja a fájlmegosztást a helyi számítógépen, vagy használhat olyan eszközöket, mint a [Storage Explorer](https://storageexplorer.com/) a fájlmegosztás fájljainak eléréséhez. Az alkalmazásból a Storage ügyféloldali kódtárait, a REST API-kat, a PowerShellt vagy az Azure CLI-t használhatja az Azure-fájlmegosztás fájljainak eléréséhez.

* <a id="what-is-afs"></a>
  **mi Azure file Sync?**  
    A Azure File Sync segítségével központilag kezelheti a szervezete fájlmegosztást Azure Filesban, miközben megőrizheti a helyszíni fájlkiszolgáló rugalmasságát, teljesítményét és kompatibilitását. Azure File Sync átalakítja a Windows Server-gépeket az Azure-fájlmegosztás gyors gyorsítótárba. A Windows Serveren elérhető bármely protokoll használatával helyileg férhet hozzá az adataihoz, beleértve az SMB-t, a hálózati fájlrendszert (NFS) és a File Transfer Protocol szolgáltatást (FTPS). Tetszőleges számú gyorsítótárral rendelkezhet a világ minden tájáról.

* <a id="files-versus-blobs"></a>
  **Miért érdemes egy Azure-fájlmegosztást és az Azure Blob Storage-ot használni az adataim számára?**  
    A Azure Files és az Azure Blob Storage szolgáltatással a Felhőbeli nagy mennyiségű adattárolási módszer is használható, de ez némileg eltérő célokra is hasznos lehet. 
    
    Az Azure Blob Storage olyan nagy méretű, Felhőbeli natív alkalmazások esetében hasznos, amelyeknek strukturálatlan adatmennyiséget kell tárolniuk. A teljesítmény és a méretezés maximalizálása érdekében az Azure Blob Storage egy egyszerű tárolási absztrakció, mint a valódi fájlrendszer. Az Azure Blob Storage-t csak REST-alapú ügyféloldali kódtárak (vagy közvetlenül a REST-alapú protokollon keresztül) érheti el.

    Azure Files kifejezetten fájlrendszer. Azure Files rendelkezik a fájl összes olyan absztrakt nevével, amelyet a helyszíni operációs rendszerekkel kapcsolatos évek óta ismer és szeret. Az Azure Blob Storage-hoz hasonlóan a Azure Files REST-felületet és REST-alapú ügyféloldali kódtárakat is biztosít. Az Azure Blob Storage szolgáltatástól eltérően a Azure Files SMB-hozzáférést biztosít az Azure-fájlmegosztás számára. Az SMB használatával közvetlenül csatlakoztathat egy Azure-fájlmegosztást a Windows, Linux vagy macOS rendszeren, akár helyszíni, akár Felhőbeli virtuális gépeken, anélkül, hogy kódot kellene írnia, vagy bármilyen speciális illesztőprogramot csatolhat a fájlrendszerhez. Az Azure-fájlmegosztás a helyszíni fájlkiszolgálók esetében is gyorsítótárazható Azure File Sync használatával a gyors elérés érdekében, az adatforgalom helyétől függően. 
   
    Az Azure Files és az Azure Blob Storage közötti különbségek részletesebb leírását lásd: az [Azure Blob Storage, a Azure Files vagy az Azure-lemezek használatának meghatározása](../common/storage-decide-blobs-files-disks.md). További információ az Azure Blob Storage-ról: [Bevezetés a blob Storage](../blobs/storage-blobs-introduction.md)-ba.

* <a id="files-versus-disks"></a>**Miért érdemes Azure-fájlmegosztást használni az Azure-lemezek helyett?**  
    Az Azure-lemezeken található lemezek egyszerűen egy lemez. Az Azure-lemezek értékének lekéréséhez csatolni kell egy lemezt az Azure-ban futó virtuális géphez. Az Azure-lemezek minden olyan eszközhöz használhatók, amely a helyszíni kiszolgálók lemezét fogja használni. Használhatja operációs rendszer lemezként, mint egy operációs rendszer vagy egy alkalmazás dedikált tárterülete. Az Azure-lemezek érdekes használata egy olyan fájlkiszolgáló létrehozása a felhőben, amely ugyanazon a helyen használható, ahol Azure-fájlmegosztást is használhat. Egy fájlkiszolgáló Virtual Machines Azure-beli üzembe helyezése nagy teljesítményű megoldás az Azure-ban, ha olyan központi telepítési beállításokat igényel, amelyeket a Azure Files jelenleg nem támogat (például NFS protokoll-támogatás vagy Premium Storage). 

    Ha azonban az Azure-lemezeket futtató fájlkiszolgáló a háttérbeli tárolóként jellemzően sokkal drágább, mint az Azure-fájlmegosztás használata, néhány ok miatt. Először is, a lemezes tárolás kifizetése mellett fizetnie kell egy vagy több Azure-beli virtuális gép futtatásának költségeiért. Másodszor, a fájlkiszolgáló futtatásához használt virtuális gépeket is kezelnie kell. Tegyük fel, hogy az operációs rendszer frissítéseiért felelős. Végül, ha végül a helyszíni gyorsítótárba helyezi az adattárolást, a replikációs technológiák (például a elosztott fájlrendszer replikáció (DFSR)) beállítása és kezelése a következő módon történik.

    Az Azure Files és az Virtual Machines Azure-ban üzemeltetett fájlkiszolgáló (az Azure-lemezek háttér-tárolóként való használata mellett) egyik megközelítése, hogy a Azure File Synct egy felhőalapú virtuális gépen üzemeltetett fájlkiszolgálón telepítse. Ha az Azure-fájlmegosztás abban a régióban van, ahol a fájlkiszolgáló található, engedélyezheti a Felhőbeli adatmennyiséget, és beállíthatja a szabad terület százalékos arányát a maximális értékre (99%). Ez minimálisan duplikált adatmennyiséget biztosít. Használhatja a fájlkiszolgálók által kívánt alkalmazásokat, például az NFS protokoll támogatását igénylő alkalmazásokat is.

    A nagy teljesítményű és magas rendelkezésre állású fájlkiszolgáló Azure-beli beállításával kapcsolatos további információkért lásd: [IAAS VM-vendég fürtök üzembe helyezése Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). A Azure Files és az Azure-lemezek közötti különbségek részletesebb ismertetését lásd: [Az Azure Blob Storage, a Azure Files vagy az Azure-lemezek használatának meghatározása](../common/storage-decide-blobs-files-disks.md). További információ az Azure-lemezekről: [azure Managed Disks – áttekintés](../../virtual-machines/windows/managed-disks-overview.md).

* <a id="get-started"></a>
  **hogyan a Azure Files használatának megkezdéséhez?**  
   A Azure Files első lépései egyszerűek. Először [hozzon létre egy fájlmegosztást](storage-how-to-create-file-share.md), majd csatlakoztassa az előnyben részesített operációs rendszerhez: 

  * [Csatlakoztatás Windows rendszeren](storage-how-to-use-files-windows.md)
  * [Csatlakoztatás Linuxon](storage-how-to-use-files-linux.md)
  * [Csatlakoztatás macOS-ben](storage-how-to-use-files-mac.md)

    Az Azure-fájlmegosztás a szervezeten belüli éles fájlmegosztás helyett történő üzembe helyezésével kapcsolatos részletes útmutatót a [Azure Files központi telepítésének tervezése](storage-files-planning.md)című témakörben talál.

* <a id="redundancy-options"></a>a **Azure Files által támogatott tárolási redundancia-beállításokat 
  ?**  
    Jelenleg a Azure Files támogatja a helyileg redundáns tárolást (LRS), a Zone redundáns tárolást (ZRS), a Geo-redundáns tárolást (GRS) és a Geo-zóna-redundáns tárolást (GZRS) (előzetes verzió). Azt tervezzük, hogy a jövőben támogatni kell az olvasási hozzáférésű geo-redundáns (RA-GRS) tárhelyet, de nem rendelkezünk a megosztás időkeretével.

* <a id="tier-options"></a>
  **milyen tárolási rétegek támogatottak Azure Files?**  
    Azure Files támogatja a két tárolási szintet: prémium és standard. A standard fájlmegosztást az általános célú (GPv1 vagy GPv2) Storage-fiókok, a prémium fájlmegosztás pedig a FileStorage Storage-fiókokban hozza létre. További információ a [szabványos fájlmegosztás](storage-how-to-create-file-share.md) és a [prémium szintű fájlmegosztás](storage-how-to-create-premium-fileshare.md)létrehozásáról. 
    
    > [!NOTE]
    > Azure-fájlmegosztás nem hozható létre blob Storage-fiókokból vagy *prémium* általános célú (GPv1 vagy GPv2) Storage-fiókból. A standard szintű Azure-fájlmegosztás csak *szabványos* általános célú fiókokban hozható létre, és a prémium szintű Azure-fájlmegosztás csak FileStorage Storage-fiókokban hozható létre. A *prémium* szintű általános célú (GPv1 és GPv2) Storage-fiókok csak a prémium szintű lapokat tartalmazó Blobok esetén érhetők el. 

* <a id="give-us-feedback"></a>
  szeretném **megtekinteni a Azure Fileshoz hozzáadott speciális funkciót. Felveheti?**  
    A Azure Files csapata érdekli a szolgáltatással kapcsolatos összes visszajelzés meghallgatása. Kérjük, szavazzon a szolgáltatásra vonatkozó kérésekre [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)! Örömmel várjuk, hogy számos új funkciót kínálunk.

## <a name="azure-file-sync"></a>Azure File Sync

* <a id="afs-region-availability"></a>
  a **Azure file Sync támogatott régiókat?**  
    Az elérhető régiók listája a Azure File Sync tervezési útmutató [régió rendelkezésre állása](storage-sync-files-planning.md#region-availability) szakaszában található. Folyamatosan bővítjük a további régiókat, köztük a nem nyilvános régiókat is.

* <a id="cross-domain-sync"></a>
  **tartományhoz csatlakoztatott és tartományhoz nem csatlakoztatott kiszolgálók is vannak ugyanabban a szinkronizálási csoportban?**  
    Igen. A szinkronizálási csoport olyan kiszolgálói végpontokat tartalmazhat, amelyek különböző Active Directory tagsággal rendelkeznek, még akkor is, ha nincsenek tartományhoz csatlakoztatva. Bár ez a konfiguráció technikailag működik, ezt a konfigurációt nem javasoljuk, mert az egyik kiszolgálón lévő fájlokhoz és mappákhoz definiált hozzáférés-vezérlési listák (ACL-ek) nem kényszeríthető ki a szinkronizálási csoport többi kiszolgálója. A legjobb eredmény érdekében javasoljuk, hogy szinkronizálja az azonos Active Directory erdőben található kiszolgálók között, a különböző Active Directory erdőkben lévő kiszolgálók között, amelyek megbízhatósági kapcsolatot létesítettek, vagy olyan kiszolgálók között, amelyek nem egy tartományban találhatók. Javasoljuk, hogy ne használja a konfigurációk kombinációját.

* <a id="afs-change-detection"></a>
  **Létrehoztam egy fájlt közvetlenül az Azure-fájlmegosztás használatával SMB vagy a portálon. Mennyi ideig tart a fájl szinkronizálása a szinkronizálási csoportban lévő kiszolgálókhoz?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**Ha ugyanezt a fájlt két kiszolgálón is megváltoztatják egy időben, mi történik?**  
    A Azure File Sync egy egyszerű ütközés-feloldási stratégiát használ: egyszerre két kiszolgálón módosítjuk a fájlok változásait. A legutóbb írt módosítás megtartja az eredeti fájlnevet. A régi fájl a "forrás" gépet és az ütköző számot fűzi a névhez. Ezt a besorolást követi: 
   
    \<FileNameWithoutExtension\>-\<számítógépnév\>\[-#\].\<ext\>  

    A CompanyReport. docx első ütközése például CompanyReport-CentralServer. docx lesz, ha a CentralServer a régebbi írás történt. A második ütközés neve CompanyReport-CentralServer-1. docx lesz. A Azure File Sync fájlon keresztül támogatja az 100-es ütközési fájlokat. Ha elérte az ütköző fájlok maximális számát, a fájl szinkronizálása sikertelen lesz, amíg az ütköző fájlok száma nem haladja meg a 100-ot.

* <a id="afs-storage-redundancy"></a>
  **a Geo-redundáns tárolás Azure file Sync támogatott?**  
    Igen, Azure Files támogatja a helyileg redundáns tárolást (LRS) és a Geo-redundáns tárolást (GRS). Ha a GRS konfigurált fiókból kezdeményezi a tárolási fiók feladatátvételét a párosított régiók között, a Microsoft azt javasolja, hogy az új régiót csak az adatok biztonsági másolatának megfelelően kezelje. A Azure File Sync nem kezdi meg automatikusan a szinkronizálást az új elsődleges régióval. 

* <a id="sizeondisk-versus-size"></a>
   **, hogy miért nem egyezik meg a fájl *mérete* *a méret* tulajdonsággal a Azure file Sync használata után?**  
  Lásd: a [felhőalapú rétegek ismertetése](storage-sync-cloud-tiering.md#sizeondisk-versus-size).

* <a id="is-my-file-tiered"></a>
  Honnan tudhatom meg, hogy van- **e lépcsőzetesen egy fájl?**  
  Lásd: a [felhőalapú rétegek ismertetése](storage-sync-cloud-tiering.md#is-my-file-tiered).

* <a id="afs-recall-file"></a>**A használni kívánt fájlt a rendszer lépcsőzetesen felhasználta. Hogyan hívhatom fel a fájlt a lemezre helyileg való használatra?**  
  Lásd: a [felhőalapú rétegek ismertetése](storage-sync-cloud-tiering.md#afs-recall-file).

* <a id="afs-force-tiering"></a>
  **Hogyan kikényszeríteni egy fájl vagy könyvtár lépcsőzetes kikényszerítését?**  
  Lásd: a [felhőalapú rétegek ismertetése](storage-sync-cloud-tiering.md#afs-force-tiering).

* <a id="afs-effective-vfs"></a>
  a ** *kötet szabad területének* értelmezése, ha több kiszolgálói végpontom van egy köteten?**  
  Lásd: a [felhőalapú rétegek ismertetése](storage-sync-cloud-tiering.md#afs-effective-vfs).

* <a id="afs-files-excluded"></a>
  , **hogy mely fájlokat és mappákat Azure file Sync automatikusan kizárja a rendszer?**  
  Lásd: [fájlok kihagyva](storage-sync-files-planning.md#files-skipped).

* <a id="afs-os-support"></a>
  használhatok **Azure file Sync a Windows Server 2008 R2, a Linux vagy a My Network-Attached Storage (NAS) eszközzel?**  
    Jelenleg a Azure File Sync csak a Windows Server 2019, a Windows Server 2016 és a Windows Server 2012 R2 rendszert támogatja. Jelenleg nem rendelkezünk más csomagokkal, amelyeket megoszthatunk, de az ügyfelek igénye alapján további platformokat is támogatunk. Tudassa velünk [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) azokat a platformokat, amelyeket támogatni kíván.

* <a id="afs-tiered-files-out-of-endpoint"></a>
  **miért léteznek a többplatformos fájlok a kiszolgálói végpont névterén kívül?**  
    A Azure File Sync ügynök 3-as verziója előtt a Azure File Sync letiltotta a többplatformos fájlok áthelyezését a kiszolgálói végponton kívül, de a kiszolgálói végponttal megegyező köteten. A másolási műveletek, a nem többrétegű fájlok áthelyezése és a többrétegű kötetek áthelyezése nem érintett. Ennek a viselkedésnek az az oka, hogy a fájlkezelő és más Windows API-k a műveleteknek ugyanazon a köteten való mozgatásával (közel) a pillanatnyi átnevezési műveletek. Ez azt jelenti, hogy az áthelyezéssel a fájlkezelő vagy más áthelyezési módszer (például a parancssor vagy a PowerShell) nem válaszol, miközben Azure File Sync visszahívja az adatok felhőből való hívását. A [Azure file Sync ügynök verziójának 3.0.12.0](storage-files-release-notes.md#supported-versions)kezdődően az Azure file Sync lehetővé teszi, hogy egy többplatformos fájlt helyezzen át a kiszolgálói végponton kívül. Elkerüljük a korábban említett negatív hatásokat, mivel lehetővé teszik, hogy a többplatformos fájl a kiszolgálói végponton kívüli többplatformos fájlként is létezve legyen, majd a háttérben meghívja a fájlt. Ez azt jelenti, hogy az ugyanarra a kötetre irányuló mozdulatok azonnaliek, és az áthelyezés befejezése után az összes művelet visszahívja a fájlt a lemezre. 

* <a id="afs-do-not-delete-server-endpoint"></a>
  **probléma merült fel a Azure file Sync a saját kiszolgálón (szinkronizálás, felhő-rétegek stb.). El kell távolítani, majd újra létre kell hozni a kiszolgálói végpontot?**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
  **áthelyezhetem a Storage Sync szolgáltatást és/vagy a Storage-fiókot egy másik erőforrás-csoportba vagy előfizetésbe?**  
   Igen, a Storage Sync szolgáltatás és/vagy a Storage-fiók áthelyezhető egy másik erőforráscsoporthoz vagy előfizetésbe a meglévő Azure AD-bérlőn belül. Ha áthelyezi a Storage-fiókot, meg kell adnia a hibrid File Sync szolgáltatás elérését a Storage-fiókhoz (lásd: [Azure file Sync hozzáférése a Storage-fiókhoz](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > A Azure File Sync nem támogatja az előfizetés másik Azure AD-bérlőre való áthelyezését.
    
* <a id="afs-ntfs-acls"></a>a 
  **Azure file Sync megőrizni a könyvtár/fájl szintű NTFS ACL-eket, valamint a Azure Files tárolt** adatfájlokat?

    Február 24-én a 2020-es verzióban az Azure file Sync által létrehozott új és meglévő ACL-ek NTFS-formátumban lesznek tárolva, és az Azure-fájlmegosztás felé irányuló ACL-módosítások szinkronizálása a szinkronizálási csoport összes kiszolgálójára történik. A Azure Files hozzáférés-vezérlési listáin végrehajtott módosítások az Azure file Sync használatával lesznek szinkronizálva. Az adatok Azure Filesba való másolása során az SMB használatával férhet hozzá a megosztáshoz, és megőrizheti a hozzáférés-vezérlési listákat. A meglévő REST-alapú eszközök, például a AzCopy vagy a Storage Explorer nem őrzik meg az ACL-eket.

    Ha engedélyezte a Azure Backup a file Sync Managed file shares szolgáltatásban, a fájl ACL-ek továbbra is visszaállíthatók a biztonsági mentés visszaállítási munkafolyamatának részeként. Ez a teljes megosztásra vagy egyedi fájlokra/könyvtárakra is használható.

    Ha pillanatképeket használ a fájl-szinkronizálás által felügyelt fájlmegosztás saját felügyeletű biztonsági mentési megoldásának részeként, akkor előfordulhat, hogy az ACL-ek nem állíthatók be megfelelően az NTFS ACL-re, ha a pillanatképek a 2020. február 24. előtt történtek. Ha ez történik, vegye fel a kapcsolatot az Azure ügyfélszolgálatával.
    
## <a name="security-authentication-and-access-control"></a>Biztonság, hitelesítés és hozzáférés-vezérlés
* <a id="ad-support"></a>
**a Azure Files által támogatott identitás-alapú hitelesítés és hozzáférés-vezérlés?**  
    
    Igen, Azure Files támogatja az identitás-alapú hitelesítést és a hozzáférés-vezérlést. Az identitás-alapú hozzáférés-vezérlést kétféleképpen is használhatja: Azure Active Directory Domain Services (Azure AD DS) (GA) vagy Active Directory (AD) (előzetes verzió). Azure Files az Azure AD DS az SMB protokollon keresztüli hitelesítés lehetővé teszi, hogy az Azure AD DS tartományhoz csatlakoztatott Windows virtuális gépek hozzáférjenek a megosztásokhoz, könyvtárakhoz és fájlokhoz az Azure AD hitelesítő adataival. Az AD támogatja a hitelesítést az AD-tartományhoz csatlakoztatott, helyszíni vagy Azure-beli számítógépeken az Azure-fájlmegosztás SMB-kapcsolaton keresztüli eléréséhez. További részletekért lásd: [az SMB-hozzáférésre vonatkozó Azure Files identitás-alapú hitelesítés támogatásának áttekintése](storage-files-active-directory-overview.md). 

    A Azure Files két további módszert kínál a hozzáférés-vezérlés kezelésére:

    - A közös hozzáférésű aláírások (SAS) használatával olyan jogkivonatokat állíthat elő, amelyek meghatározott engedélyekkel rendelkeznek, és amelyek egy adott időintervallumra érvényesek. Létrehozhat például egy jogkivonatot csak olvasási hozzáféréssel egy adott fájlhoz, amely 10 perces lejáratú. Bárki, aki rendelkezik a jogkivonattal, míg a jogkivonat érvényes, csak olvasási hozzáféréssel rendelkezik a fájlhoz ezen a 10 percen belül. A közös hozzáférésű aláírási kulcsokat csak a REST API vagy az ügyféloldali kódtárak támogatják. A Storage-fiók kulcsainak használatával csatlakoztatnia kell az Azure-fájlmegosztást az SMB-kapcsolaton keresztül.

    - Azure File Sync megőrzi és replikálja az összes tulajdonosi ACL-t vagy DACL-t (akár Active Directory-alapú, akár helyi) az összes olyan kiszolgálói végpontra, amelyet szinkronizál. Mivel a Windows Server már hitelesíthető Active Directorysal, Azure File Sync egy hatályos megszakítási lehetőség, amíg teljes mértékben támogatja a Active Directory-alapú hitelesítés és az ACL-támogatás megérkezését.
    
    Az Azure Storage szolgáltatásban támogatott összes protokoll átfogó megjelenítéséhez tekintse meg az [Azure Storage-hozzáférés engedélyezését](https://docs.microsoft.com/azure/storage/common/storage-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ismertető témakört. 

* <a id="ad-support-devices"></a>a 
**Azure Files Azure Active Directory Domain Services (azure AD DS) hitelesítés támogatja az Azure ad-beli hitelesítő adatokkal való SMB-hozzáférést az Azure ad-hez csatlakoztatott vagy regisztrált eszközökön?**

    Nem, ez a forgatókönyv nem támogatott.

* <a id="ad-support-rest-apis"></a>
van olyan **REST API-k, amelyek támogatják a beolvasási/beállítási/másolási/fájl NTFS ACL-eket?**

    Igen, támogatjuk a REST API-kat, amelyek a könyvtárakra vagy fájlokra vonatkozó NTFS ACL-ek beszerzését, beállítását vagy másolását használják az [2019-02-02](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#version-2019-02-02) (vagy újabb) REST API használatakor.

* <a id="ad-vm-subscription"></a>
**hozzáférhetek az Azure ad-beli hitelesítő adatokkal rendelkező Azure Files egy másik előfizetéshez tartozó virtuális gépről?**

    Ha az előfizetés, amely alatt a fájlmegosztás telepítve van, ugyanahhoz az Azure AD-bérlőhöz van társítva, mint az Azure AD Domain Services-példány, amelyhez a virtuális gép tartományhoz csatlakozik, akkor a Azure Files ugyanazzal az Azure AD-beli hitelesítő adatokkal érheti el. A korlátozás nem az előfizetésre, hanem a kapcsolódó Azure AD-bérlőre is érvényes.    
    
* <a id="ad-support-subscription"></a>
**engedélyezhető Azure Files azure AD DS vagy ad-hitelesítés olyan Azure ad-Bérlővel, amely eltér az elsődleges bérlőtől, amelyhez a fájlmegosztás társítva van?**

    Nem, Azure Files csak az Azure AD DS vagy az AD-integrációt támogatja egy olyan Azure AD-Bérlővel, amely ugyanabban az előfizetésben található, mint a fájlmegosztás. Egy Azure AD-bérlőhöz csak egy előfizetés társítható. Ez a korlátozás az Azure-AD DS és az AD-hitelesítési módszerekre is vonatkozik. Az AD hitelesítéshez való használatakor az AD-hitelesítő adatokat szinkronizálni kell az Azure AD-vel, amelyhez a Storage-fiók társítva van.

* <a id="ad-linux-vms"></a>
**Azure Files Azure AD DS vagy ad-hitelesítés támogatja a Linux rendszerű virtuális gépeket?**

    Nem, a Linux rendszerű virtuális gépekről történő hitelesítés nem támogatott.

* <a id="ad-multiple-forest"></a>
**Azure Files ad-hitelesítés támogatja a több erdőt használó ad-környezettel való integrációt?**    

    Azure Files AD-hitelesítés csak az AD tartományi szolgáltatás azon erdőjét integrálja, amelyhez a Storage-fiók regisztrálva van. Egy másik AD-erdő hitelesítésének támogatásához a környezetnek megfelelően kell konfigurálnia az erdőszintű megbízhatóságot. Azure Files a regisztráció egy AD tartományi szolgáltatásba, amely általában azonos a normál fájlkiszolgálón, ahol az AD-fiókot hoz létre a hitelesítéshez. Az egyetlen különbség, hogy a Storage-fiók regisztrált SPN-je "file.core.windows.net" értékkel végződik, amely nem felel meg a tartomány utótagjának.

    Tekintse meg a tartományi rendszergazdát, és ellenőrizze, hogy szükséges-e a DNS-útválasztási házirend frissítése a több erdős hitelesítés engedélyezéséhez.

* <a id=""></a>
**milyen régiók érhetők el Azure Files ad-hitelesítéshez (előzetes verzió)?**

    A részletekért tekintse meg az [ad regionális elérhetőségét](storage-files-active-directory-domain-services-enable.md#regional-availability) ismertető témakört.

* <a id="ad-aad-smb-afs"></a>
**kihasználható Azure Files Azure AD DS Authentication vagy Active Directory (ad) hitelesítés (előzetes verzió) a Azure file Sync által felügyelt fájlmegosztás esetén?**

    Igen, engedélyezheti az Azure-AD DS vagy az AD-hitelesítést az Azure file Sync által kezelt fájlmegosztás esetén. A könyvtár/fájl NTFS ACL-ek helyi fájlkiszolgálón való módosítása a Azure Files és fordítva történik.

* <a id="encryption-at-rest"></a>
Hogyan biztosíthatom az Azure-fájlmegosztás inaktív állapotban való **titkosítását?**  

    Igen. További információ: [Azure Storage Service encryption](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>
**Hogyan biztosíthatok hozzáférést egy adott fájlhoz egy webböngésző használatával?**  

    A közös hozzáférési aláírások használatával olyan jogkivonatokat állíthat elő, amelyek meghatározott engedélyekkel rendelkeznek, és amelyek egy adott időintervallumra érvényesek. Létrehozhat például egy jogkivonatot, amely csak olvasási hozzáférést biztosít egy adott fájlhoz egy meghatározott ideig. Bárki, aki rendelkezik az URL-címmel, közvetlenül hozzáférhet a fájlhoz bármely webböngészőből, amíg a jogkivonat érvényes. Egyszerűen létrehozhat egy közös hozzáférési aláírási kulcsot egy olyan felhasználói felületen, mint például a Storage Explorer.

* <a id="file-level-permissions"></a>
**csak olvasási vagy csak írási engedélyeket adhat meg a megosztáson belüli mappákhoz?**  

    Ha az SMB használatával csatlakoztatja a fájlmegosztást, nincs jogosultsága a mappa szintű vezérléssel. Ha azonban a REST API vagy az ügyféloldali kódtárak használatával hoz létre közös hozzáférési aláírást, megadhat csak olvasási vagy csak írási engedélyeket a megosztáson belüli mappákhoz.

* <a id="ip-restrictions"></a>
**megvalósítható az Azure-fájlmegosztás IP-korlátozásai?**  

    Igen. Az Azure-fájlmegosztás elérését korlátozhatja a Storage-fiók szintjén. További információ: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>
**milyen adatmegfelelőségi szabályzatokat Azure Files támogatni?**  

   Azure Files ugyanazon a tárolási architektúrán fut, amelyet az Azure Storage más tárolási szolgáltatásaiban használ. Azure Files ugyanazokat az adatmegfelelőségi szabályzatokat alkalmazza, amelyek más Azure Storage-szolgáltatásokban is használhatók. Az Azure Storage-beli adatok megfelelőségével kapcsolatos további információkért tekintse meg az [Azure Storage megfelelőségi ajánlatait](https://docs.microsoft.com/azure/storage/common/storage-compliance-offerings), és nyissa meg a [Microsoft adatvédelmi központját](https://microsoft.com/trustcenter/default.aspx).

## <a name="on-premises-access"></a>Helyszíni hozzáférés

* <a id="port-445-blocked"></a>
**az internetszolgáltatót, vagy blokkolja a 445-es portot, amely nem Azure Files csatlakoztatást. Mit tegyek?**

    Az [445-es blokkolt port megkerülő megoldásának különböző módjairól itt](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems#cause-1-port-445-is-blocked)olvashat. Azure Files csak a régión vagy adatközponton kívülről engedélyezi a kapcsolódást az SMB 3,0 (titkosítási támogatással) használatával. Az SMB 3,0 protokoll számos olyan biztonsági funkciót vezetett be, mint például a csatornák titkosítása, ami nagyon biztonságos az interneten keresztül. Lehetséges azonban, hogy a 445-es portot letiltották az alacsonyabb SMB-verziókban található sebezhetőségek korábbi okai miatt. Ideális esetben a portot csak az SMB 1,0-forgalomhoz kell letiltani, és az SMB 1,0-et minden ügyfélen ki kell kapcsolni.

* <a id="expressroute-not-required"></a>
az **Azure ExpressRoute-t kell használnia a Azure Fileshoz való kapcsolódáshoz vagy a helyszíni Azure file Sync használatához?**  

    Nem. Az Azure-fájlmegosztás eléréséhez nem szükséges a ExpressRoute. Ha közvetlenül a helyszínen csatlakoztat egy Azure-fájlmegosztást, az összes szükséges az internet-hozzáféréshez nyitva lévő 445-as (TCP kimenő) portra (ez az a port, amelyet az SMB a kommunikációhoz használ). Ha Azure File Sync használ, az összes szükséges az 443-as port (TCP kimenő) HTTPS-hozzáféréshez (nincs szükség SMB-re). A ExpressRoute azonban a következő hozzáférési lehetőségek egyikével *is* használhatja.

* <a id="mount-locally"></a>
**Hogyan lehet csatlakoztatni egy Azure-fájlmegosztást a helyi gépen?**  

    A fájlmegosztást az SMB protokoll használatával csatlakoztathatja, ha a 445-as (TCP kimenő) port meg van nyitva, és az ügyfél támogatja az SMB 3,0 protokollt (például ha Windows 10 vagy Windows Server 2016 rendszert használ). Ha a szervezet házirendje vagy az INTERNETSZOLGÁLTATÓja blokkolja a 445-as portot, a Azure File Sync használatával férhet hozzá az Azure-fájlmegosztás eléréséhez.

## <a name="backup"></a>Biztonsági mentés
* <a id="backup-share"></a>
**hogyan az Azure-fájlmegosztás biztonsági mentését?**  
    A véletlen törlések elleni védelemhez használhat rendszeres [megosztási pillanatképeket](storage-snapshots-files.md) . Használhatja a AzCopy, a Robocopy vagy egy külső gyártótól származó biztonsági mentési eszközt is, amely képes biztonsági másolatot készíteni egy csatlakoztatott fájlmegosztásról. A Azure Backup Azure Files biztonsági mentését kínálja. További információ az [Azure-fájlmegosztás Azure Backup általi biztonsági mentéséről](https://docs.microsoft.com/azure/backup/backup-azure-files).

## <a name="share-snapshots"></a>Pillanatképek megosztása

### <a name="share-snapshots-general"></a>Pillanatképek megosztása: általános
* <a id="what-are-snaphots"></a>
**a fájlmegosztás pillanatképei?**  
    Az Azure-fájlmegosztás pillanatképeit használhatja a fájlmegosztás csak olvasható verziójának létrehozásához. A Azure Files használatával a tartalom egy korábbi verzióját visszamásolhatja ugyanarra a megosztásra, az Azure egy másik helyére, vagy a helyszínen további módosításokat is végezhet. A pillanatképek megosztásával kapcsolatos további tudnivalókért tekintse meg a [megosztás pillanatképének áttekintése](storage-snapshots-files.md)című témakört.

* <a id="where-are-snapshots-stored"></a>
**hol tárolódnak a megosztási Pillanatképek?**  
    A megosztási Pillanatképek tárolása ugyanabban a Storage-fiókban történik, mint a fájlmegosztás.

* <a id="snapshot-consistency"></a>
**a pillanatképek alkalmazásának konzisztens megosztása?**  
    Nem, a megosztási Pillanatképek nem az alkalmazások konzisztensek. A megosztási pillanatkép megkezdése előtt a felhasználónak ki kell ürítenie az alkalmazásból a megosztásba írt írásokat.

* <a id="snapshot-limits"></a>
a **használható megosztási Pillanatképek száma**  
    Igen. A Azure Files legfeljebb 200 megosztási pillanatképet tud megőrizni. A megosztási Pillanatképek nem számítanak bele a megosztási kvótába, így a megosztási Pillanatképek által használt teljes területhez nem tartozik megosztási korlát. A Storage-fiókra vonatkozó korlátozások továbbra is érvényesek. A 200-es megosztási pillanatképek létrehozása után törölni kell a régebbi pillanatképeket új megosztási Pillanatképek létrehozásához.

* <a id="snapshot-cost"></a>
Mennyibe **kerül a pillanatképek megosztása?**  
    A standard szintű tranzakció és a standard szintű tárolási díj a pillanatképre lesz érvényes. A pillanatképek növekményes jellegűek. Az alap pillanatkép maga a megosztás. Az összes további pillanatkép növekményes, és csak az előző pillanatképből származó különbözetet fogja tárolni. Ez azt jelenti, hogy a számlán látható különbözeti változások minimálisak lesznek, ha a számítási feladatok elváltozása minimális. A standard Azure Files díjszabási információit a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/storage/files/) tekintheti meg. Napjainkban a megosztási pillanatkép által felhasznált méretet a felhasznált kapacitással összehasonlítva tekintheti meg. Dolgozunk az eszközökön a jelentéskészítés javítására.

* <a id="ntfs-acls-snaphsots"></a>a 
a **megosztási pillanatképekben tárolt könyvtárak és fájlok NTFS ACL-listái?**  
    A könyvtárak és fájlok NTFS ACL-listái megmaradnak a megosztási pillanatképekben.

### <a name="create-share-snapshots"></a>Megosztási pillanatképek létrehozása
* <a id="file-snaphsots"></a>
**Létrehozhatok egyéni fájlok megosztási pillanatképét?**  
    A megosztási Pillanatképek a fájlmegosztás szintjén jönnek létre. Az egyes fájlokat visszaállíthatja a fájlmegosztás pillanatképből, de a fájl szintű megosztási Pillanatképek nem hozhatók létre. Ha azonban egy megosztási szintű megosztási pillanatképet használ, és szeretné listázni a megosztási pillanatképeket, amelyekben egy adott fájl módosult, akkor ezt a Windows-csatlakoztatott megosztás **előző verzióiban** teheti meg. 
    
    Ha fájl-pillanatkép-szolgáltatásra van szüksége, tudassa velünk [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encrypted-snapshots"></a>
**Létrehozhatok egy titkosított fájlmegosztás megosztási pillanatképeit?**  
    Olyan Azure-fájlmegosztás megosztási pillanatképét is elvégezheti, amelyeken engedélyezve van a titkosítás. A megosztási Pillanatképek fájljait egy titkosított fájlmegosztás számára állíthatja vissza. Ha a megosztás titkosított, a megosztási pillanatkép is titkosítva lesz.

* <a id="geo-redundant-snaphsots"></a>
**a megosztási Pillanatképek?**  
    A megosztási Pillanatképek ugyanazzal a redundanciával rendelkeznek, mint az általuk készített Azure-fájlmegosztás. Ha a fiókhoz a Geo-redundáns tárolást választotta, a megosztási pillanatképet a párosított régióban is redundánsan tárolja a rendszer.

### <a name="manage-share-snapshots"></a>Megosztási Pillanatképek kezelése
* <a id="browse-snapshots-linux"></a>
**tallózhatok a Linuxon futó megosztási Pillanatképek között?**  
    Használhatja az Azure CLI-t a megosztási Pillanatképek létrehozásához, listázásához, tallózásához és visszaállításához Linuxon.

* <a id="copy-snapshots-to-other-storage-account"></a>
**másolhatom a megosztási pillanatképeket egy másik Storage-fiókba?**  
    Fájlokat másolhat a megosztási pillanatképekről egy másik helyre, a megosztási Pillanatképek azonban nem másolhatók.

### <a name="restore-data-from-share-snapshots"></a>Adatok visszaállítása a megosztási pillanatképekről
* <a id="promote-share-snapshot"></a>
**előléptethető megosztási pillanatkép az alapmegosztásra?**  
    A megosztási Pillanatképek adatait bármely más célhelyre másolhatja. Megosztási pillanatkép nem előléptethető az alapmegosztásra.

* <a id="restore-snapshotted-file-to-other-share"></a>
Visszaállíthatom a **megosztási Pillanatképek adatait egy másik Storage-fiókba?**  
    Igen. A megosztási pillanatképből származó fájlok átmásolhatók az eredeti helyre vagy egy másik helyre, amely tartalmazza ugyanazt a Storage-fiókot vagy más Storage-fiókot, akár ugyanabban a régióban, akár különböző régiókban. A fájlokat egy helyszíni helyre vagy más felhőbe is másolhatja.    
  
### <a name="clean-up-share-snapshots"></a>Megosztási Pillanatképek tisztítása
* <a id="delete-share-keep-snapshots"></a>
**törölhetem a megosztást, de nem törölhetem a megosztási pillanatképeket?**  
    Ha a megosztáson aktív megosztási Pillanatképek vannak, akkor nem törölheti a megosztást. A megosztási Pillanatképek törléséhez használhat API-t is. A megosztási pillanatképeket és a megosztást is törölheti a Azure Portalban.

* <a id="delete-share-with-snapshots"></a>
**Mi történik a megosztási pillanatképekkel, ha törölem a Storage-fiókomat?**  
    Ha törli a Storage-fiókját, a rendszer törli a megosztási pillanatképeket is.

## <a name="billing-and-pricing"></a>Számlázás és díjszabás
* <a id="vm-file-share-network-traffic"></a>
**Az Azure-beli virtuális gép és az Azure-fájlmegosztás közötti hálózati forgalom az előfizetésre felszámított külső sávszélességnek számít?**  
    Ha a fájlmegosztás és a virtuális gép ugyanabban az Azure-régióban található, akkor a fájlmegosztás és a virtuális gép közötti forgalomért nem számítunk fel további díjat. Ha a fájlmegosztás és a virtuális gép különböző régiókban található, a köztük lévő forgalom külső sávszélességként lesz felszámítva.

* <a id="share-snapshot-price"></a>
Mennyibe **kerül a pillanatképek megosztása?**  
     Az előzetes verzió ideje alatt a megosztási Pillanatképek kapacitása díjmentes. A standard szintű tárolás és a tranzakciós költségek is érvényesek. Az általános elérhetőség után a rendszer előfizetéseket számít fel a megosztási Pillanatképek kapacitása és tranzakciói után.
     
     A megosztási Pillanatképek növekményes jellegűek. Az alap megosztási pillanatkép maga a megosztás. Az összes későbbi megosztási pillanatkép növekményes, és csak az előző megosztási pillanatképből származó különbséget tárolja. Csak a módosított tartalomért számítunk fel díjat. Ha a megosztás 100-es adatokkal van ellátva, de csak 5 GiB módosult az utolsó megosztási pillanatkép óta, a megosztási pillanatkép csak 5 további GiB-t használ fel, és a 105 GiB díját számítjuk fel. A tranzakciós és standard kimenő forgalommal kapcsolatos további információkért tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="scale-and-performance"></a>Méretezés és teljesítmény
* <a id="files-scale-limits"></a>
**melyek a Azure Files skálázási korlátai?**  
    A Azure Files méretezhetőségével és teljesítményével kapcsolatos információkért lásd: [Azure Files skálázhatósági és teljesítményi célok](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>
**milyen méretek érhetők el az Azure file shares szolgáltatásban?**  
    Az Azure fájlmegosztás mérete (prémium és standard) akár 100 TiB-ra is méretezhető. A standard szinthez tartozó nagyobb fájlmegosztás előkészítéséhez tekintse meg a bevezetési útmutató a további [fájlmegosztás (standard szint)](storage-files-planning.md#onboard-to-larger-file-shares-standard-tier) című szakaszát.

* <a id="lfs-performance-impact"></a>
**kibővíti a fájlmegosztás kvótáját a számítási feladatokra vagy a Azure file Syncra?**
    
    Nem. A kvóta kibővítése nem befolyásolja a számítási feladatokat vagy Azure File Sync.

* <a id="open-handles-quota"></a>
hány **ügyfél fér hozzá egyszerre ugyanahhoz a fájlhoz?**    
    A 2 000-as számú nyitott leíró egyetlen fájlon van. Ha a 2 000-es megnyitott kezelővel rendelkezik, hibaüzenet jelenik meg, amely szerint a kvóta elérte a kvótát.

* <a id="zip-slow-performance"></a>a **Azure Filesban lévő fájlok kicsomagolásakor a teljesítmény lassú 
. Mit tegyek?**  
    Ha nagy számú fájlt szeretne átvinni Azure Filesre, javasoljuk, hogy használja a AzCopy (Windows rendszeren, a Linux és UNIX rendszerhez készült előzetes verzióban) vagy Azure PowerShell. Ezek az eszközök hálózati átvitelre vannak optimalizálva.

* <a id="slow-perf-windows-81-2012r2"></a>
**Miért lassú a teljesítményem az Azure-fájlmegosztás Windows Server 2012 R2 vagy windows 8,1 rendszeren való csatlakoztatása után?**  
    Ismert hiba történt egy Azure-fájlmegosztás Windows Server 2012 R2 és Windows 8,1 rendszeren való csatlakoztatásakor. A problémát a Windows 8,1 és a Windows Server 2012 R2 összesített frissítésében 2014 javítottuk. Az optimális teljesítmény érdekében gondoskodjon arról, hogy a Windows Server 2012 R2 és a Windows 8,1 összes példánya alkalmazza ezt a javítást. (A Windows-javítások mindig Windows Updateon keresztül érkeznek.) További információ: a Microsoft tudásbázishoz tartozó cikk [lassú teljesítményű, ha a Windows 8,1 vagy a Server 2012 R2 rendszerhez fér hozzá Azure Files](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Funkciók és együttműködés más szolgáltatásokkal
* <a id="cluster-witness"></a>
használhatom **Az Azure-fájlmegosztást *tanúsító fájlmegosztásként* a Windows Server feladatátvevő fürthöz?**  
    Az Azure-fájlmegosztás jelenleg nem támogatja ezt a konfigurációt. További információ az Azure Blob Storage-hoz való beállításáról: [Felhőbeli tanúsító üzembe helyezése feladatátvevő fürtön](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>
**csatlakoztathat egy Azure-fájlmegosztást egy Azure Container-példányon?**  
    Igen, az Azure-fájlmegosztás jó lehetőség, ha a Container-példányok élettartama után szeretné megőrizni az adatokat. További információ: Azure- [fájlmegosztás csatlakoztatása az Azure Container instances szolgáltatással](../../container-instances/container-instances-mounting-azure-files-volume.md).

* <a id="rest-rename"></a>
**van átnevezési művelet a REST APIban?**  
    Jelenleg nem.

* <a id="nested-shares"></a>
beállíthat **beágyazott megosztásokat? Más szóval egy megosztás alatt található megosztás?**  
    Nem. *A fájlmegosztás az a virtuális* illesztőprogram, amelyet csatlakoztathat, így a beágyazott megosztások nem támogatottak.

* <a id="ibm-mq"></a>
**hogyan az Azure Files az IBM MQ-val?**  
    Az IBM kiadott egy olyan dokumentumot, amely segítséget nyújt az IBM MQ-ügyfeleknek az IBM szolgáltatással való Azure Files konfigurálásában. További információ: az [IBM MQ többpéldányos üzenetsor-kezelő beállítása Microsoft Azure Files szolgáltatással](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).

## <a name="see-also"></a>Lásd még
* [A Windows Azure Files hibáinak megoldása](storage-troubleshoot-windows-file-connection-problems.md)
* [A Linux Azure Files hibáinak megoldása](storage-troubleshoot-linux-file-connection-problems.md)
* [Az Azure File Sync hibaelhárítása](storage-sync-files-troubleshoot.md)
