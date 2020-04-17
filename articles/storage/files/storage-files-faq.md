---
title: Gyakori kérdések az Azure Files-ról | Microsoft dokumentumok
description: Válaszok az Azure Files szolgáltatással kapcsolatos gyakori kérdésekre.
author: roygara
ms.service: storage
ms.date: 02/23/2020
ms.author: rogarana
ms.subservice: files
ms.topic: conceptual
ms.openlocfilehash: 01b5f87c2557e2195573b90766ee45e001798cca
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537695"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Az Azure Filesszal kapcsolatos gyakori kérdések (GYIK)
[Az Azure Files](storage-files-introduction.md) teljes körűen felügyelt fájlmegosztásokat kínál a felhőben, amelyek az iparági szabványnak megfelelő [SMB protokollon](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)keresztül érhetők el. Az Azure-fájlmegosztásokat egyidejűleg csatlakoztathatja a Windows, Linux és macOS felhőbeli vagy helyszíni központi telepítésein. Az Azure-fájlmegosztások windows Server-gépeken is gyorsítótárazhatók az Azure File Sync használatával az adatok felhasználási helyéhez közeli gyors hozzáférés érdekében.

Ez a cikk az Azure Files funkcióival és funkcióival kapcsolatos gyakori kérdésekre ad választ, beleértve az Azure File Sync és az Azure Files használatát. Ha nem látja a választ a kérdésére, felveheti velünk a kapcsolatot a következő csatornákon keresztül (növekvő sorrendben):

1. A hozzászólások részben ezt a cikket.
2. [Azure Storage Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
3. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Microsoft-támogatás. Új támogatási kérelem létrehozásához az Azure Portal **súgólapján** kattintson a **Súgó + támogatás** gombra, majd az Új támogatási **kérelem**elemre.

## <a name="general"></a>Általános kérdések
* <a id="why-files-useful"></a>
  **Hogyan hasznos az Azure Files?**  
   Az Azure Files segítségével fájlmegosztásokat hozhat létre a felhőben anélkül, hogy felelős lenne a fizikai kiszolgáló, eszköz vagy berendezés terhelésének kezeléséért. Mi a monoton munkát az Ön számára, beleértve az operációs rendszer frissítéseit és a rossz lemezek cseréjét. Ha többet szeretne megtudni arról, hogy az Azure Files hogyan segíthet Önnek, olvassa el [a Miért hasznos az Azure Files.](storage-files-introduction.md#why-azure-files-is-useful)

* <a id="file-access-options"></a>
  **Milyen különböző módokon érheti el a fájlokat az Azure Files-ban?**  
    A fájlmegosztást csatlakoztathatja a helyi számítógépen az SMB 3.0 protokoll használatával, vagy olyan eszközökkel, mint a [Storage Explorer,](https://storageexplorer.com/) hogy hozzáférjen a fájlmegosztásban lévő fájlokhoz. Az alkalmazásból használhatja a storage-ügyfélkönyvtárak, REST API-k, PowerShell vagy az Azure CLI az Azure-fájlmegosztásban lévő fájlok eléréséhez.

* <a id="what-is-afs"></a>
  **Mi az Azure File Sync?**  
    Az Azure File Sync használatával központosíthatja a szervezet fájlmegosztásait az Azure Files-ban, miközben megőrizheti a helyszíni fájlkiszolgáló rugalmasságát, teljesítményét és kompatibilitását. Az Azure File Sync átalakítja a Windows Server-gépek egy gyors gyorsítótár az Azure-fájlmegosztás. A Windows Server kiszolgálón elérhető bármely protokoll thasználhat az adatok helyi eléréséhez, beleértve az SMB-t, a Hálózati fájlrendszert (NFS) és az FTPS-szolgáltatást. Annyi gyorsítótára lehet, amennyire szüksége van szerte a világon.

* <a id="files-versus-blobs"></a>
  **Miért érdemes Azure-fájlmegosztást és Azure Blob-tárolót használni az adataimhoz?**  
    Az Azure Files és az Azure Blob storage egyaránt kínál nak lehetőséget nagy mennyiségű adat tárolására a felhőben, de ezek hasznosak némileg eltérő célokra. 
    
    Az Azure Blob storage olyan tömeges méretű, natív, natív alkalmazások esetén hasznos, amelyeknek strukturálatlan adatokat kell tárolniuk. A teljesítmény és a méretezés maximalizálása érdekében az Azure Blob storage egyszerűbb tárolási absztrakció, mint egy valódi fájlrendszer. Az Azure Blob storage csak REST-alapú ügyfélkódtárakon keresztül (vagy közvetlenül a REST-alapú protokollon keresztül érhető el).

    Az Azure Files kifejezetten egy fájlrendszer. Az Azure Files rendelkezik az összes olyan fájlkivonattal, amelyet a helyszíni operációs rendszerekkel való évek óta ismer és szeret. Az Azure Blob storage-hoz hasonlóan az Azure Files is rest-felületet és REST-alapú ügyfélkódtárakat kínál. Az Azure Blob storage-tól eltérően az Azure Files SMB-hozzáférést biztosít az Azure-fájlmegosztásokhoz. Az SMB használatával közvetlenül windowsos, Linuxos vagy macOS-alapú Azure-fájlmegosztást csatlakoztathat, akár a helyszínen, akár a felhőbeli virtuális gépeken, anélkül, hogy bármilyen kódot írna, vagy speciális illesztőprogramokat csatolna a fájlrendszerhez. Az Azure-fájlmegosztások a helyszíni fájlkiszolgálókon is gyorsítótárazhatók az Azure File Sync használatával a gyors hozzáférés érdekében, közel az adatok felhasználásának helyéhez. 
   
    Az Azure Files és az Azure Blob storage közötti különbségek részletesebb leírását [az Azure Storage alapvető szolgáltatásainak bemutatása](../common/storage-introduction.md)című témakörben található. Ha többet szeretne megtudni az Azure Blob storage-ról, [olvassa el a Bevezetés a Blob-tárolóba című témakört.](../blobs/storage-blobs-introduction.md)

* <a id="files-versus-disks"></a>**Miért használnék Azure-fájlmegosztást az Azure Disks helyett?**  
    A lemez az Azure Disks egyszerűen egy lemez. Ahhoz, hogy értéket kapjon az Azure Disks-ből, csatolnia kell egy lemezt egy azure-ban futó virtuális géphez. Az Azure Disks használható mindent, amit egy lemezt használna egy helyszíni kiszolgálón. Használhatja operációs rendszer lemezként, az operációs rendszer felcserélési területeként vagy egy alkalmazás dedikált tárolójaként. Az Azure Disks érdekes használata, hogy hozzon létre egy fájlkiszolgálót a felhőben, hogy ugyanazokon a helyeken, ahol lehet használni egy Azure-fájlmegosztást. A fájlkiszolgáló üzembe helyezése az Azure virtuális gépeken nagy teljesítményű módja annak, hogy az Azure-ban beszerezzen fájltárolást, ha olyan telepítési beállításokra van szüksége, amelyeket jelenleg nem támogat az Azure Files (például az NFS protokoll támogatása vagy a prémium szintű tárhely). 

    Azonban egy fájlkiszolgáló azure-lemezes háttér-tárolóként általában sokkal drágább, mint egy Azure-fájlmegosztás használata, több okból is. Először is, amellett, hogy fizet a lemezes tárolás, akkor is meg kell fizetnie a költségek futtatásáért egy vagy több Azure-beli virtuális gépek. Másodszor, a fájlkiszolgáló futtatásához használt virtuális gépeket is kezelnie kell. Például Ön felelős az operációs rendszer frissítéseiért. Végül, ha végső soron az adatok helyszíni gyorsítótárazására van szükség, a replikációs technológiák, például az elosztott fájlrendszer replikációja (DFSR) beállításának és kezelésétől kell ezt megvalósítania.

    Az Azure Files és az Azure virtuális gépeken üzemeltetett fájlkiszolgáló (az Azure Disks háttérrendszer-alapú tárolóként való használata mellett) legjobb megoldásának egyik módja az Azure File Sync telepítése egy felhőalapú virtuális gépen üzemeltetett fájlkiszolgálóra. Ha az Azure-fájlmegosztás ugyanabban a régióban van, mint a fájlkiszolgáló, engedélyezheti a felhőrétegezést, és beállíthatja a szabad terület százalékos százalékát a maximálisra (99%). Ez biztosítja az adatok minimális megkettőzését. A fájlkiszolgálókkal bármilyen alkalmazást használhat, például az NFS protokollt támogatást igénylő alkalmazásokat.

    A nagy teljesítményű és magas rendelkezésre állású fájlkiszolgáló Azure-beli beállításának beállításáról az [IaaS virtuálisgép-kiszolgálófürtök központi telepítése a Microsoft Azure-ban](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/)című témakörben talál további információt. Az Azure Files és az Azure Disks közötti különbségek részletesebb leírását [az Azure Storage-szolgáltatások bemutatása](../common/storage-introduction.md)című témakörben található. Ha többet szeretne megtudni az Azure Disks lemezekről, olvassa el az [Azure felügyelt lemezek – áttekintés című témakört.](../../virtual-machines/windows/managed-disks-overview.md)

* <a id="get-started"></a>
  **Hogyan kezdhetem el használni az Azure Files-t?**  
   Az Azure Files első lépései egyszerűek. Először [hozzon létre egy fájlmegosztást](storage-how-to-create-file-share.md), majd csatlakoztassa a kívánt operációs rendszerhez: 

  * [Csatlakoztatás a Windows rendszerben](storage-how-to-use-files-windows.md)
  * [Csatlakoztatás Linux alatt](storage-how-to-use-files-linux.md)
  * [Csatlakoztatás macOS rendszerben](storage-how-to-use-files-mac.md)

    Az Azure-fájlmegosztások szervezeten belüli éles fájlmegosztások lecserélésére történő üzembe helyezéséről az [Azure Files telepítésének megtervezése című](storage-files-planning.md)témakörben talál részletesebb útmutatót.

* <a id="redundancy-options"></a>
  **Milyen tárolási redundancia-beállításokat támogat az Azure Files?**  
    Jelenleg az Azure Files támogatja a helyileg redundáns tárolás (LRS), zóna redundáns tárolás (ZRS), georedundáns tárolás (GRS) és a geozóna redundáns tárolás (GZRS) (előzetes verzió). Azt tervezzük, hogy támogatja az olvasási hozzáférésű georedundáns (RA-GRS) tárolás a jövőben, de nem rendelkezik határidőket megosztani ebben az időben.

* <a id="tier-options"></a>
  **Milyen tárolási szintek támogatottak az Azure Files?**  
    Az Azure Files két tárolási szintet támogat: prémium és standard. Általános célú szabványos fájlmegosztások (GPv1 vagy GPv2) tárolófiókok, prémium szintű fájlmegosztások pedig a FileStorage tárfiókokban jönnek létre. További információ a [szabványos fájlmegosztások](storage-how-to-create-file-share.md) és [a prémium fájlmegosztások](storage-how-to-create-premium-fileshare.md)létrehozásáról. 
    
    > [!NOTE]
    > Nem hozhat létre Azure-fájlmegosztásokat a Blob storage-fiókokból vagy *a prémium szintű* általános célú (GPv1 vagy GPv2) tárfiókokból. Standard Azure-fájlmegosztások csak *a standard* általános célú fiókok ban kell létrehozni, és prémium szintű Azure-fájlmegosztások csak A FileStorage tárfiókok csak létre kell hozni. *Prémium szintű* általános célú (GPv1 és GPv2) tárfiókok csak prémium szintű lapblobok. 

* <a id="give-us-feedback"></a>
  **Nagyon szeretném látni az Azure Files adott funkcióját. Hozzá tudja adni?**  
    Az Azure Files csapata érdeklődik a szolgáltatásunkkal kapcsolatos visszajelzések iránt. Kérjük, szavazzon az [Azure Files UserVoice funkciókéréseiről!](https://feedback.azure.com/forums/217298-storage/category/180670-files) Bízunk benne, hogy örömet önnek sok új funkciót.

  **Támogatja az Azure Files a fájlzárolást?**  
    Igen, az Azure Files teljes mértékben támogatja az SMB/Windows-stílusú fájlzárolást, [lásd a részleteket.](https://docs.microsoft.com/rest/api/storageservices/managing-file-locks) 
    
## <a name="azure-file-sync"></a>Azure File Sync

* <a id="afs-region-availability"></a>
  **Mely régiók támogatottak az Azure File Sync?**  
    Az elérhető régiók listája megtalálható az Azure File Sync tervezési útmutató [régió elérhetősége](storage-sync-files-planning.md#azure-file-sync-region-availability) szakaszában. Folyamatosan támogatást nyújtunk további régióknak, beleértve a nem nyilvános régiókat is.

* <a id="cross-domain-sync"></a>
  **Lehetnek tartományhoz csatlakozó és nem tartományhoz csatlakozó kiszolgálók ugyanabban a szinkronizálási csoportban?**  
    Igen. A szinkronizálási csoportok különböző Active Directory-tagsággal rendelkező kiszolgálóvégpontokat tartalmazhatnak, még akkor is, ha nem tartományhoz vannak csatlakoztatva. Bár ez a konfiguráció technikailag működik, nem javasoljuk, hogy ez egy tipikus konfiguráció, mert a hozzáférés-vezérlési listák (ACLs), amelyek az egyik kiszolgálón lévő fájlokhoz és mappákhoz vannak definiálva, előfordulhat, hogy a szinkronizálási csoport más kiszolgálói nem kényszeríthetők. A legjobb eredmény érdekében azt javasoljuk, hogy szinkronizálja azokat a kiszolgálókat, amelyek ugyanabban az Active Directory erdőben találhatók, különböző Active Directory-erdőkben lévő, de bizalmi kapcsolatokat létesítő kiszolgálók között, vagy olyan kiszolgálók között, amelyek nem egy tartományban találhatók. Azt javasoljuk, hogy ne használja a konfigurációk vegyesen.

* <a id="afs-change-detection"></a>
  **Az SMB használatával vagy a portálon közvetlenül az Azure-fájlmegosztásban hoztam létre egy fájlt. Mennyi ideig tart, amíg a fájl szinkronizálódik a szinkronizálási csoport kiszolgálóival?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**Ha ugyanazt a fájlt két kiszolgálón körülbelül egy időben módosítják, mi történik?**  
    Az Azure File Sync egy egyszerű ütközés-megoldási stratégiát használ: a két kiszolgálón egyszerre módosított fájlok mindkét módosítását megtartjuk. A legutóbb írott módosítás megtartja az eredeti fájlnevet. A régebbi fájl rendelkezik a "forrás" géppel, és az ütközési szám hozzávan fűzve a névhez. Ebből következik ez a rendszerezés: 
   
    \<FájlnévnélkülExtension\>-\<\>\[-#\]MachineName . \<ext\>  

    A CompanyReport.docx első ütközése például CompanyReport-CentralServer.docx lesz, ha a CentralServer az a hely, ahol a régebbi írás történt. A második ütközés neve CompanyReport-CentralServer-1.docx. Az Azure File Sync fájlonként 100 ütközési fájlt támogat. Az ütköző fájlok maximális számának elérése után a fájl szinkronizálása sikertelen lesz, amíg az ütköző fájlok száma 100-nál kevesebb nem lesz.

* <a id="afs-storage-redundancy"></a>
  **A georedundáns tárolás támogatott az Azure File Sync?**  
    Igen, az Azure Files támogatja mind a helyileg redundáns tárolás (LRS) és a georedundáns tárolás (GRS). Ha egy GRS-hez konfigurált fiókból kezdeményez egy tárfiók-feladatátvételt a párosított régiók között, a Microsoft azt javasolja, hogy az új régiót csak az adatok biztonsági másolataként kezelje. Az Azure File Sync nem kezdi meg automatikusan a szinkronizálást az új elsődleges régióval. 

* <a id="sizeondisk-versus-size"></a>
  **Miért nem felel meg a fájl *mérete a lemezen* tulajdonságnak a *Size* tulajdonsággal az Azure File Sync használata után?**  
  Lásd: [A felhőrétegezés ismertetése.](storage-sync-cloud-tiering.md#sizeondisk-versus-size)

* <a id="is-my-file-tiered"></a>
  **Hogyan állapítható meg, hogy egy fájl rétegzett-e?**  
  Lásd: [A felhőrétegezés ismertetése.](storage-sync-cloud-tiering.md#is-my-file-tiered)

* <a id="afs-recall-file"></a>**A használni kívánt fájl rétegzett. Hogyan idézhetem vissza a fájlt a lemezre, hogy helyileg használhassam?**  
  Lásd: [A felhőrétegezés ismertetése.](storage-sync-cloud-tiering.md#afs-recall-file)

* <a id="afs-force-tiering"></a>
  **Hogyan kényszeríthetek egy fájl vagy könyvtár rétegzett?**  
  Lásd: [A felhőrétegezés ismertetése.](storage-sync-cloud-tiering.md#afs-force-tiering)

* <a id="afs-effective-vfs"></a>
  **Hogyan értelmezi a *kötetszabad területet,* ha több kiszolgálóvégpont van egy köteten?**  
  Lásd: [A felhőrétegezés ismertetése.](storage-sync-cloud-tiering.md#afs-effective-vfs)

* <a id="afs-files-excluded"></a>
  **Mely fájlokat vagy mappákat zárja ki automatikusan az Azure File Sync?**  
  Lásd: [Kihagyott fájlok](storage-sync-files-planning.md#files-skipped).

* <a id="afs-os-support"></a>
  **Használhatom az Azure File Sync szolgáltatást Windows Server 2008 R2, Linux vagy a hálózathoz csatlakoztatott tároló (NAS) eszközzel?**  
    Az Azure File Sync jelenleg csak a Windows Server 2019, a Windows Server 2016 és a Windows Server 2012 R2 rendszert támogatja. Jelenleg nincs más tervünk, amelyet megoszthatnánk, de nyitottak vagyunk arra, hogy további platformokat támogassunk az ügyfelek igényei alapján. Tudassa velünk az [Azure Files UserVoice-nál,](https://feedback.azure.com/forums/217298-storage/category/180670-files) hogy milyen platformokat szeretne támogatni.

* <a id="afs-tiered-files-out-of-endpoint"></a>
  **Miért léteznek rétegzett fájlok a kiszolgálóvégpont-névtéren kívül?**  
    Az Azure File Sync agent 3-as verziójáelőtt az Azure File Sync megakadályozta a rétegzett fájlok áthelyezését a kiszolgáló végpontján kívülre, de ugyanazon a köteten, mint a kiszolgáló végpontja. A másolási műveleteket, a nem rétegzett fájlok áthelyezéseit és a rétegzett fájlok más kötetekre való áthelyezését ez nem érintette. Ennek oka az volt, hogy a Fájlkezelő és más Windows API-k azt feltételezték, hogy az áthelyezési műveletek ugyanazon a köteten (majdnem) azonnali átnevezési műveletek. Ez azt jelenti, hogy a lépésekkel a Fájlkezelő vagy más áthelyezési módszerek (például a parancssor i vagy a PowerShell) nem válaszolnak, miközben az Azure File Sync visszahívja az adatokat a felhőből. Az [Azure File Sync agent 3.0.12.0-s verziójával](storage-files-release-notes.md#supported-versions)kezdve az Azure File Sync lehetővé teszi, hogy egy rétegzett fájlt helyezzen át a kiszolgáló végpontja intőjén kívülre. Elkerüljük a korábban említett negatív hatásokat azáltal, hogy lehetővé tesszük, hogy a rétegzett fájl rétegzett fájlként létezzen a kiszolgáló végpontjain kívül, majd visszahívja a fájlt a háttérben. Ez azt jelenti, hogy ugyanazon a köteten történő áthelyezések azonnaliak, és mindent megteszünk annak érdekében, hogy a fájl visszahívása a lemezre az áthelyezés befejezése után befejeződjön. 

* <a id="afs-do-not-delete-server-endpoint"></a>
  **Problémám van az Azure File Sync szolgáltatással a kiszolgálómon (szinkronizálás, felhőrétegezés stb.). Eltávolítsam és hozzam létre újra a kiszolgálóvégpontot?**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
  **Áthelyezhetem a storage sync szolgáltatást és/vagy a tárfiókot egy másik erőforráscsoportba vagy előfizetésbe?**  
   Igen, a storage sync szolgáltatás és/vagy a tárfiók áthelyezhető egy másik erőforráscsoportba vagy előfizetésbe a meglévő Azure AD-bérlőn belül. Ha a tárfiók át lett helyezve, meg kell adnia a hibrid fájlszinkronizálási szolgáltatás nak a tárfiókhoz való hozzáférését [(lásd: Győződjön meg arról, hogy az Azure File Sync hozzáfér a tárfiókhoz).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > Az Azure File Sync nem támogatja az előfizetés áthelyezését egy másik Azure AD-bérlőre.
    
* <a id="afs-ntfs-acls"></a>
  **Az Azure File Sync megőrzi a könyvtár-fájl szintű NTFS ACL-eket az Azure Files-ban tárolt adatokkal együtt?**

    2020. február 24-én az Azure-fájlszinkronizálás által rétegzett új és meglévő ACL-ek NTFS formátumban megmaradnak, és a közvetlenül az Azure-fájlmegosztáson végrehajtott ACL-módosítások szinkronizálódnak a szinkronizálási csoport összes kiszolgálójával. Az Azure Files-on végrehajtott ACL-ek módosításai az Azure-fájlszinkronizáláson keresztül szinkronizálódnak. Amikor adatokat másol az Azure Files ba, győződjön meg arról, hogy az SMB használatával érheti el a megosztást, és megőrzi az ACL-eket. A meglévő REST-alapú eszközök, például az AzCopy vagy a Storage Explorer nem maradnak meg az ACL-ok.

    Ha engedélyezte az Azure Backup a fájlszinkronizálás felügyelt fájlmegosztások, fájl ACLs továbbra is vissza állítható részeként a biztonsági mentési visszaállítási munkafolyamat. Ez szerkezet egyik részére a teljes osztozik vagy egyén fájlokat/ címtésok.

    Ha a fájlszinkronizálás által kezelt fájlmegosztások saját felügyelt biztonsági mentési megoldásának részeként használja a pillanatképeket, előfordulhat, hogy az ACL-ek nem lesznek megfelelően visszaállítva az NTFS ACL-ok számára, ha a pillanatképek 2020. Ebben az esetben vegye fel a kapcsolatot az Azure-támogatással.
    
## <a name="security-authentication-and-access-control"></a>Biztonság, hitelesítés és hozzáférés-vezérlés
* <a id="ad-support"></a>
**Az Azure Files támogatja az identitásalapú hitelesítést és hozzáférés-vezérlést?**  
    
    Igen, az Azure Files támogatja az identitásalapú hitelesítést és hozzáférés-vezérlést. Az identitásalapú hozzáférés-vezérlés kétféle képpen használható: helyszíni Active Directory tartományi szolgáltatások (előzetes verzió) vagy Azure Active Directory tartományi szolgáltatások (Azure AD DS). A helyszíni Active Directory tartományi szolgáltatások (AD DS) támogatja a hitelesítést az Active Directory tartományi szolgáltatásokhoz csatlakozott gépek használatával, akár a helyszínen, akár az Azure-ban, az Azure fájlmegosztások SMB-n keresztüli eléréséhez. Az Azure AD DS-hitelesítés Az Azure Files SMB-n keresztül lehetővé teszi az Azure AD DS-tartományhoz csatlakozó Windows-virtuális gépek számára a megosztások, könyvtárak és fájlok azure AD-hitelesítő adatok használatával való eléréséhez. További információt az [Azure Files identitásalapú hitelesítési támogatásának áttekintése az SMB-hozzáféréshez](storage-files-active-directory-overview.md)című témakörben talál. 

    Az Azure Files két további lehetőséget kínál a hozzáférés-vezérlés kezelésére:

    - A megosztott hozzáférésű aláírások (SAS) segítségével adott engedélyekkel rendelkező és meghatározott időtartamig érvényes jogkivonatokat hozhat létre. Létrehozhat például egy jogkivonatot, amely csak olvasható hozzáférést biztosít egy 10 perces lejárati sebességű fájlhoz. Bárki, aki rendelkezik a jogkivonat, amíg a jogkivonat érvényes, írásvédett hozzáféréssel rendelkezik a fájlhoz a 10 percig. A megosztott hozzáférésű aláírási kulcsok csak a REST API-n vagy az ügyfélkódtárakon keresztül támogatottak. Az Azure-fájlmegosztást SMB-re kell csatlakoztatnia a tárfiók kulcsai használatával.

    - Az Azure File Sync megőrzi és replikálja az összes tetszetős ACL-eket vagy DAFI-kat (akár Active Directory-alapú, akár helyi) az összes kiszolgálóvégpontra, amelyhez szinkronizál. Mivel a Windows Server már hitelesíthető az Active Directoryval, az Azure File Sync hatékony stop-gap lehetőség, amíg az Active Directory-alapú hitelesítés és az ACL-támogatás teljes körű támogatása meg nem érkezik.
    
    Az Azure [Storage-hozzáférés engedélyezése](https://docs.microsoft.com/azure/storage/common/storage-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) az Azure Storage-szolgáltatásokban támogatott összes protokoll átfogó megjelenítését. 

* <a id="ad-support-devices"></a>
**Az Azure Files Active Directory tartományi szolgáltatások (Azure AD DS) hitelesítése támogatja az SMB-hozzáférést az Azure AD-hez csatlakozott vagy az Azure AD-hez regisztrált eszközökről származó SMB-hitelesítő adatok használatával?**

    Nem, ez a forgatókönyv nem támogatott.

* <a id="ad-support-rest-apis"></a>
**Vannak olyan REST API-k, amelyek támogatják a könyvtár/fájl beolvasását/beállítását/másolását?**

    Igen, támogatjuk azokat a REST API-kat, amelyek a [2019-07-es](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#version-2019-07-07) (vagy újabb) REST API használatakor ntfs-acl-okat hoznak létre, illetve nem másolnak könyvtárakhoz vagy fájlokhoz.

* <a id="ad-vm-subscription"></a>
**Hozzáférhetek az Azure-fájlmegosztásokhoz az Azure AD hitelesítő adataival egy másik előfizetésben lévő virtuális gépről?**

    Ha az előfizetés, amely alatt a fájlmegosztás telepítve van társítva ugyanazzal az Azure AD-bérlővel, mint az Azure AD DS központi telepítése, amelyhez a virtuális gép tartomány-csatlakozott, majd hozzáférhet az Azure-fájlmegosztások ugyanazt az Azure AD hitelesítő adatok használatával. A korlátozás nem az előfizetésre, hanem a társított Azure AD-bérlőre van kiszabva.
    
* <a id="ad-support-subscription"></a>
**Engedélyezhetem az Azure AD DS vagy a helyszíni AD DS-hitelesítést az Azure-fájlmegosztásokhoz egy Azure AD-bérlő használatával, amely eltér az Azure-fájlmegosztás elsődleges bérlőjétől?**

    Nem, az Azure Files csak az Azure AD DS vagy a helyszíni AD DS-integrációt támogatja egy Olyan Azure AD-bérlővel, amely ugyanabban az előfizetésben található, mint a fájlmegosztás. Csak egy előfizetés társítható egy Azure AD-bérlővel. Ez a korlátozás az Azure AD DS és a helyszíni AD DS hitelesítési módszerekre egyaránt vonatkozik. A helyszíni AD DS hitelesítéshez való használataesetén [az AD DS hitelesítő adatait szinkronizálni kell az Azure AD-vel,](../../active-directory/hybrid/how-to-connect-install-roadmap.md) amelyhez a tárfiók társítva van.

* <a id="ad-linux-vms"></a>
**Az Azure AD DS vagy a helyszíni AD DS-hitelesítés az Azure fájlmegosztások linuxos virtuális gépeket támogatja?**

    Nem, a Linux virtuális gépekhitelesítés nem támogatott.

* <a id="ad-aad-smb-afs"></a>
**Az Azure File Sync által kezelt fájlmegosztások támogatják az Azure AD DS vagy a helyszíni AD DS (előzetes verzió) hitelesítést?**

    Igen, engedélyezheti az Azure AD DS vagy a helyszíni AD DS-hitelesítést az Azure File Sync által kezelt fájlmegosztáson. A helyi fájlkiszolgálókon lévő címtár-/fájl NTFS-akon lévő kontifikált módosításokat az Azure Files rétegezi, és fordítva.

* <a id="ad-aad-smb-files"></a>
**Hogyan ellenőrizhetem, hogy engedélyeztem-e az AD DS-hitelesítést a tárfiókomban, és lekérhetem-e a tartományadatait?**

    Az itt található utasításokat [itt](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-enable#1-enable-ad-authentication-for-your-account)találja.
    
* <a id="encryption-at-rest"></a>
**Hogyan győződhetek meg arról, hogy az Azure-fájlmegosztás titkosítva van in-t?**  

    Igen. További információ: [Azure Storage Service Encryption](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>
**Hogyan tudok hozzáférést biztosítani egy adott fájlhoz webböngészővel?**  

    A megosztott hozzáférésű jogosultságok segítségével adott engedélyekkel rendelkező és meghatározott időtartamig érvényes jogkivonatokat hozhat létre. Létrehozhat például egy jogkivonatot, amely csak olvasható hozzáférést biztosít egy adott fájlhoz egy meghatározott ideig. Bárki, aki rendelkezik az URL-t érheti el a fájlt közvetlenül bármely webböngészőben, amíg a token érvényes. A felhasználói felülethez hasonló felhasználói felületről, például a Storage Explorer programból egyszerűen létrehozhat egy megosztott hozzáférésű aláíráskulcsot.

* <a id="file-level-permissions"></a>
**Meg adható írásvédett vagy csak írható engedélyek a megosztáson belüli mappákhoz?**  

    Ha a fájlmegosztást Az SMB használatával csatlakoztatja, nem rendelkezik mappaszintű vezérléssel az engedélyek felett. Ha azonban megosztott hozzáférésű aláírást hoz létre a REST API vagy az ügyféltárak használatával, írásvédett vagy csak írási engedélyeket adhat meg a megosztáson belüli mappákhoz.

* <a id="ip-restrictions"></a>
**Megvalósíthatok IP-korlátozásokat egy Azure-fájlmegosztáshoz?**  

    Igen. Az Azure-fájlmegosztáshoz való hozzáférés a tárfiók szintjén korlátozható. További információt az [Azure Storage tűzfalak és virtuális hálózatok konfigurálása című témakörben talál.](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

* <a id="data-compliance-policies"></a>
**Milyen adatmegfelelőségi szabályzatokat támogat az Azure Files?**  

   Az Azure Files ugyanazon a tárolási architektúrán fut, amelyet az Azure Storage más tárolási szolgáltatásaiban is használnak. Az Azure Files ugyanazokat az adatmegfelelőségi szabályzatokat alkalmazza, mint amelyeket más Azure-tárolási szolgáltatásokban használnak. Az Azure Storage-adatok megfelelőségéről az [Azure Storage megfelelőségi ajánlatai](https://docs.microsoft.com/azure/storage/common/storage-compliance-offerings)című dokumentumban tájékozódhat, majd a [Microsoft Adatvédelmi központban](https://microsoft.com/trustcenter/default.aspx)talál.
   
### <a name="ad-authentication"></a>AD-hitelesítés
* <a id=""></a>
**Támogatja az Azure Files Azure AD-hitelesítése a Linux virtuális gépeket?**

    Nem, a Linux virtuális gépekhitelesítés nem támogatott.

* <a id="ad-multiple-forest"></a>
**Az Azure-fájlmegosztások helyszíni AD DS-hitelesítése támogatja az integrációt egy Több erdőt használó AD DS-környezettel?**    

    A helyszíni Azure-fájlok AD DS-hitelesítés csak integrálható a tartományi szolgáltatás erdőjével, amelyhez a tárfiók regisztrálva van. Egy másik erdőhitelesítés támogatásához a környezetnek megfelelően konfigurált erdőszintű megbízhatósági kapcsolattal kell rendelkeznie. Az, ahogyan az Azure Files regisztráljon az AD DS-ben, majdnem megegyezik a normál fájlkiszolgálóval, ahol létrehoz egy identitást (számítógép- vagy szolgáltatásbejelentkezési fiókot) az AD DS-ben a hitelesítéshez. Az egyetlen különbség az, hogy a tárfiók regisztrált SPN-je "file.core.windows.net" végződik, amely nem egyezik meg a tartományutótaggal. Érdeklődjön a tartományi rendszergazdával, hogy szükség van-e a DNS-útválasztási házirend frissítésére ahhoz, hogy a különböző tartományutótag miatt több erdőhitelesítést engedélyezhet.

* <a id=""></a>
**Milyen régiók érhetők el az Azure Files AD DS-hitelesítéshez (előzetes verzió)?**

    A részleteket az [AD DS regionális elérhetőségében](storage-files-identity-auth-active-directory-enable.md#regional-availability) találja.
    
* <a id="ad-aad-smb-afs"></a>
**Használható az Azure Files Active Directory (AD) hitelesítése (előzetes verzió) az Azure File Sync által kezelt fájlmegosztásokon?**

    Igen, engedélyezheti az AD-hitelesítést az Azure-fájlszinkronizálás által kezelt fájlmegosztáson. A helyi fájlkiszolgálókon lévő címtár-/fájl NTFS-akon lévő kontifikált módosításokat az Azure Files rétegezi, és fordítva.

* <a id="ad-aad-smb-files"></a>
**Hogyan ellenőrizhetem, hogy engedélyeztem-e az AD-hitelesítést a tárfiókomon és az AD tartományadatain?**

    Az [itt](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-enable#enable-ad-authentication-for-your-account) megadott utasításokból megtudhatja, hogy az Azure Files AD-hitelesítés engedélyezve van-e a tárfiókban, és lekérheti-e az AD-tartomány adatait.

* <a id="ad-aad-smb-files"></a>
**Van-e különbség a számítógépfiók vagy szolgáltatásbejelentkezési fiók létrehozása során, amely a tárfiókot képviseli az AD-ben?**

    Hozzon létre egy [számítógép-fiókot](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (alapértelmezett) vagy [egy szolgáltatás bejelentkezési fiók](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) nincs különbség, hogy a hitelesítés hogyan működne az Azure Files. Saját maga dönthet arról, hogyan képviselhet egy tárfiókot identitásként az AD-környezetben. A Join-AzStorageAccountForAuth parancsmagban beállított alapértelmezett DomainAccountType készlet a számítógépfiók. Az AD-környezetben beállított jelszó lejárati életkora azonban eltérő lehet a számítógép- vagy szolgáltatásbejelentkezési fiókesetében, és ezt figyelembe kell vennie [a tárfiók identitásának a d-ben való frissítése](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-enable#5-update-ad-account-password)során.

## <a name="on-premises-access"></a>Helyszíni hozzáférés

* <a id="port-445-blocked"></a>
**Saját isp vagy it blokkolja port 445, amely nem azure-fájlok csatlakoztatása. Mit kell tennem?**

    A [445-ös blokkolt port különböző megoldási módjairól itt olvashat.](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems#cause-1-port-445-is-blocked) Az Azure Files csak a régión vagy adatközponton kívülről származó SMB 3.0-s (titkosítási támogatással rendelkező) kapcsolatokat engedélyezi. Az SMB 3.0 protokoll számos biztonsági funkciót vezetett be, beleértve a csatornatitkosítást, amely nagyon biztonságos az interneten keresztül. Azonban lehetséges, hogy a 445-ös portot az alacsonyabb SMB-verziókban található biztonsági rések korábbi okai miatt blokkolták. Ideális esetben a portot csak az 1.0-s SMB forgalom esetén kell blokkolni, és az SMB 1.0-t minden ügyfélnél ki kell kapcsolni.

* <a id="expressroute-not-required"></a>
**Az Azure ExpressRoute segítségével kell csatlakoznom az Azure Files-hoz, vagy az Azure File Sync helyszíni használatához?**  

    Nem. Az ExpressRoute nem szükséges az Azure-fájlmegosztás eléréséhez. Ha közvetlenül a helyszínen csatlakoztat egy Azure-fájlmegosztást, csak annyit kell tenni, hogy a 445-ös port (TCP kimenő) meg legyen nyitva az internet-hozzáféréshez (ez az a port, amelyet az SMB a kommunikációhoz használ). Ha az Azure File Sync, minden, ami szükséges a 443-as port (TCP kimenő) a HTTPS-hozzáférés (nem SMB szükséges). Az ExpressRoute azonban a hozzáférési beállítások egyikével *is* használható.

* <a id="mount-locally"></a>
**Hogyan csatlakoztathatok egy Azure-fájlmegosztást a helyi gépemen?**  

    A fájlmegosztást az SMB protokoll használatával csatlakoztathatja, ha a 445-ös (TCP kimenő) port meg van nyitva, és az ügyfél támogatja az SMB 3.0 protokollt (például Windows 10 vagy Windows Server 2016 használata esetén). Ha a 445-ös portot a szervezet szabályzata vagy az isp blokkolja, az Azure File Sync használatával elérheti az Azure-fájlmegosztást.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**Hogyan tudok biztonsági másolatot tenni az Azure-fájlmegosztásról?**  
    A véletlen törlésekkel szembeni védelemhez rendszeres [megosztási pillanatképeket](storage-snapshots-files.md) használhat. Használhatja az AzCopy, Robocopy vagy egy külső biztonsági mentési eszközt is, amely biztonsági másolatot készíthet a csatlakoztatott fájlmegosztásról. Az Azure Backup biztonsági másolatot készít az Azure Files ról. További információ az [Azure Backup által isznak arról, hogy miként készíthet biztonsági másolatot az Azure-fájlmegosztásokról.](https://docs.microsoft.com/azure/backup/backup-azure-files)

## <a name="share-snapshots"></a>Pillanatképek megosztása

### <a name="share-snapshots-general"></a>Pillanatképek megosztása: Általános
* <a id="what-are-snaphots"></a>
**Mik azok a fájlmegosztási pillanatképek?**  
    Az Azure-fájlmegosztási pillanatképek segítségével létrehozhatja a fájlmegosztások csak olvasható verzióját. Az Azure Files segítségével is átmásolhatja a tartalom egy korábbi verzióját ugyanarra a megosztásra, egy másik helyre az Azure-ban, vagy a helyszíni további módosításokhoz. Ha többet szeretne tudni a megosztási pillanatképekről, olvassa el a [Megosztáspillanatkép áttekintését.](storage-snapshots-files.md)

* <a id="where-are-snapshots-stored"></a>
**Hol tárolják a megosztási pillanatképeket?**  
    A megosztási pillanatképek ugyanabban a tárfiókban tárolódnak, mint a fájlmegosztás.

* <a id="snapshot-consistency"></a>
**A megosztási pillanatképek alkalmazáskonzisztensek?**  
    Nem, a megosztási pillanatképek nem alkalmazáskonzisztensek. A felhasználónak ki kell ürölnie az írási műveleteket az alkalmazásból a megosztásra a megosztás pillanatképének elvégzése előtt.

* <a id="snapshot-limits"></a>
**Korlátozva vannak a használható megosztási pillanatképek száma?**  
    Igen. Az Azure Files legfeljebb 200 megosztási pillanatképet tarthat meg. A megosztási pillanatképek nem számítanak bele a megosztási kvótába, így nincs megosztásonkénti korlát az összes megosztási pillanatkép által használt teljes területre vonatkozóan. A tárfiók-korlátok továbbra is érvényesek. 200 megosztási pillanatkép után törölnie kell a régebbi pillanatképeket az új megosztási pillanatképek létrehozásához.

* <a id="snapshot-cost"></a>
**Mennyibe kerülnek a megosztási pillanatképek?**  
    A pillanatképre a szokásos tranzakciós és elszámolóárú tárolási költség vonatkozik. A pillanatképek növekményes jellegűek. Az alap pillanatkép maga a megosztás. Az összes ezt követő pillanatképek növekményes, és csak tárolja a diff az előző pillanatkép. Ez azt jelenti, hogy a különbözeti változások, amelyek a számlán látható lesz minimális, ha a számítási feladatok lemorzsolódása minimális. Tekintse meg [a Díjszabás oldalon](https://azure.microsoft.com/pricing/details/storage/files/) a standard Azure-fájlok díjszabási információkat. Ma a megosztási pillanatkép által felhasznált méret a számlázott kapacitás és a használt kapacitás összehasonlításával. Dolgozunk a jelentéstétel javítását célzó eszközökkel.

* <a id="ntfs-acls-snaphsots"></a>
**Vannak NTFS ACL-ek a könyvtárak és fájlok megmaradnak a megosztás pillanatképek?**  
    A könyvtárakon és fájlokon lévő NTFS-acl-ok a megosztási pillanatképekben maradnak meg.

### <a name="create-share-snapshots"></a>Megosztási pillanatképek létrehozása
* <a id="file-snaphsots"></a>
**Létrehozhatok megosztási pillanatképet az egyes fájlokról?**  
    A megosztási pillanatképek a fájlmegosztás szintjén jönnek létre. Az egyes fájlok visszaállíthatók a fájlmegosztás pillanatképéből, de nem hozhat létre fájlszintű megosztási pillanatképeket. Ha azonban megosztásszintű megosztási pillanatképet készített, és fel szeretné sorolni azokat a megosztási pillanatképeket, amelyeken egy adott fájl megváltozott, ezt a Windowshoz csatlakoztatott megosztás **korábbi verziói** területen teheti meg. 
    
    Ha fájlpillanatkép-funkcióra van szüksége, tudassa velünk az [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)webhelyen.

* <a id="encrypted-snapshots"></a>
**Létrehozhatok megosztási pillanatképeket egy titkosított fájlmegosztásról?**  
    Az Azure-fájlmegosztások megosztási pillanatképét, amelyek inaktív titkosításengedélyezve van. A fájlokat a megosztáspillanatképből titkosított fájlmegosztásra állíthatja vissza. Ha a megosztás titkosítva van, a megosztási pillanatkép is titkosítva van.

* <a id="geo-redundant-snaphsots"></a>
**A megosztási pillanatképek georedundánsak?**  
    A megosztási pillanatképek redundanciája megegyezik azzal az Azure-fájlmegosztási szolgáltatásokkal, amelyekhez készültek. Ha a fiókhoz georedundáns tárolást választott, a megosztási pillanatkép is redundánsan tárolódik a párosított régióban.

### <a name="manage-share-snapshots"></a>Megosztási pillanatképek kezelése
* <a id="browse-snapshots-linux"></a>
**Böngészhetek a megosztási pillanatképek között Linuxról?**  
    Az Azure CLI segítségével hozhat létre, listázhatja, tallózhatja és visszaállíthatja a megosztási pillanatképeket linuxos.

* <a id="copy-snapshots-to-other-storage-account"></a>
**Átmásolhatom a megosztáspillanatképeket egy másik tárfiókba?**  
    A megosztási pillanatképekből fájlokat másolhat egy másik helyre, de a megosztási pillanatképeket nem másolhatja.

### <a name="restore-data-from-share-snapshots"></a>Adatok visszaállítása megosztási pillanatképekből
* <a id="promote-share-snapshot"></a>
**Előléptethetek egy megosztási pillanatképet az alapmegosztásra?**  
    A megosztáspillanatképből bármely más célhelyre másolhatja az adatokat. Megosztási pillanatképet nem léptethet elő az alapmegosztásra.

* <a id="restore-snapshotted-file-to-other-share"></a>
**Visszaállíthatok adatokat a megosztás pillanatképéből egy másik tárfiókba?**  
    Igen. Fájlokat egy megosztás pillanatkép lehet másolni az eredeti helyre, vagy egy másik helyre, amely tartalmazza vagy ugyanazt a tárfiókot, vagy egy másik tárfiók, akár ugyanabban a régióban, vagy különböző régiókban. A fájlokat egy helyszíni helyre vagy bármely más felhőbe is másolhatja.    
  
### <a name="clean-up-share-snapshots"></a>Megosztási pillanatképek karbantartása
* <a id="delete-share-keep-snapshots"></a>
**Törölhetem a megosztásomat, de nem törölhetem a megosztási pillanatképeket?**  
    Ha aktív megosztási pillanatképek vannak a megosztáson, nem törölheti a megosztást. Api-val törölheti a megosztási pillanatképeket, valamint a megosztást. A megosztási pillanatképeket és az Azure Portalon is törölheti a megosztási pillanatképeket.

* <a id="delete-share-with-snapshots"></a>
**Mi történik a megosztási pillanatképekkel, ha törlöm a tárfiókot?**  
    Ha törli a tárfiókot, a megosztási pillanatképek is törlődnek.

## <a name="billing-and-pricing"></a>Számlázás és árképzés
* <a id="vm-file-share-network-traffic"></a>
**Az Azure virtuális gép és egy Azure-fájlmegosztás közötti hálózati forgalom az előfizetésre terhelt külső sávszélességnek számít?**  
    Ha a fájlmegosztás és a virtuális gép ugyanabban az Azure-régióban, nincs további díj a fájlmegosztás és a virtuális gép közötti forgalom. Ha a fájlmegosztás és a virtuális gép különböző régiókban van, a köztük lévő forgalom külső sávszélességként kerül felszámításra.

* <a id="share-snapshot-price"></a>
**Mennyibe kerülnek a megosztási pillanatképek?**  
     Az előzetes verzió során a megosztási pillanatkép kapacitása nem számít fel díjat. A normál tárolási kimenő forgalom és a tranzakciós költségek érvényesek. Az általános rendelkezésre állás után az előfizetések díja a kapacitásért és a megosztási pillanatképektranzakcióiért kerül felszámításra.
     
     A megosztási pillanatképek növekményes jellegűek. Az alapmegosztáspillanatképe maga a megosztás. Minden további megosztási pillanatkép növekményes, és csak az előző megosztási pillanatképtől való különbséget tárolja. Csak a módosított tartalomért kell fizetnie. Ha 100 GiB adattal rendelkezik, de csak 5 GiB változott az utolsó megosztási pillanatkép óta, a megosztási pillanatkép csak 5 további GiB-t használ fel, és 105 GiB-t számlázunk. A tranzakciós és a normál kimenő forgalom díjairól az [Árképzés oldalon](https://azure.microsoft.com/pricing/details/storage/files/)talál további információt.

## <a name="scale-and-performance"></a>Méretezés és teljesítmény
* <a id="files-scale-limits"></a>
**Milyen méretezési korlátok vannak az Azure Files-ban?**  
    Az Azure Files méretezhetőségéről és teljesítménycéljairól az [Azure Files méretezhetőségi és teljesítménycélok című témakörben](storage-files-scale-targets.md)talál.

* <a id="need-larger-share"></a>
**Milyen méretű ek érhetők el az Azure-fájlmegosztásokhoz?**  
    Az Azure-fájlmegosztási méretek (prémium és normál) akár 100 TiB-ig is skálázhatók. Tekintse meg a tervezési útmutató [alaplapi nagyobb fájlmegosztások (standard réteg)](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) szakaszát a standard szint nagyobb fájlmegosztási utasításainak bevezetési utasításához.

* <a id="lfs-performance-impact"></a>
**A fájlmegosztási kvóta bővítése hatással van a számítási feladataimra vagy az Azure File Sync-re?**
    
    Nem. A kvóta bővítése nem befolyásolja a számítási feladatok vagy az Azure File Sync.

* <a id="open-handles-quota"></a>
**Hány ügyfél férhet hozzá egyszerre ugyanahhoz a fájlhoz?**   
    Egy fájlban 2000 megnyitott leíróból álló kvóta található. Ha 2000 megnyitott leírója van, egy hibaüzenet jelenik meg, amely szerint a kvóta elérésekor.

* <a id="zip-slow-performance"></a>
**A teljesítményem lassú, amikor kicsomagolom a fájlokat az Azure Files szolgáltatásban. Mit kell tennem?**  
    Nagy számú fájl átviteléhez az Azure Files, azt javasoljuk, hogy az AzCopy (Windows; előzetes verzióban Linux és UNIX) vagy az Azure PowerShell. Ezeket az eszközöket hálózati átvitelre optimalizálták.

* <a id="slow-perf-windows-81-2012r2"></a>
**Miért lassú a teljesítményem, miután csatlakoztattam az Azure-fájlmegosztást Windows Server 2012 R2 vagy Windows 8.1 rendszerre?**  
    Ismert probléma merül fel, ha Azure-fájlmegosztást csatlakoztat Windows Server 2012 R2 és Windows 8.1 rendszerre. A problémát a Windows 8.1 és a Windows Server 2012 R2 2 áprilisi összesített frissítése javította. Az optimális teljesítmény érdekében győződjön meg arról, hogy a Windows Server 2012 R2 és a Windows 8.1 összes példányán alkalmazza ezt a javítást. (A Windows-javításokat mindig a Windows Update szolgáltatáson keresztül kell kapnia.) További információt a Microsoft Tudásbázis hoz [tartozó, Az Azure Files Windows 8.1-ről vagy Server 2012 R2 rendszerből való elérésekor nyújtott lassú teljesítmény című cikkben talál.](https://support.microsoft.com/kb/3114025)

## <a name="features-and-interoperability-with-other-services"></a>Jellemzők és interoperabilitás más szolgáltatásokkal
* <a id="cluster-witness"></a>
**Használhatom az Azure-fájlmegosztást a Windows Server feladatátvételi fürtöm *hez való fájlmegosztástanúsítóként?***  
    Jelenleg ez a konfiguráció nem támogatott egy Azure-fájlmegosztás. Ha többet szeretne tudni arról, hogy miként állíthatja be ezt az Azure Blob storage-hoz, [olvassa el a Felhőbeli tanúsító telepítése feladatátvevő fürthöz című témakört.](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)

* <a id="containers"></a>
**Csatlakoztathatok egy Azure-fájlmegosztást egy Azure Container-példányra?**  
    Igen, az Azure-fájlmegosztások jó lehetőség, ha egy tárolópéldány élettartama után is meg szeretné tartani az információkat. További információ: [Azure-fájlmegosztás csatlakoztatása az Azure Container-példányokkal.](../../container-instances/container-instances-mounting-azure-files-volume.md)

* <a id="rest-rename"></a>
**Van átnevezési művelet a REST API-ban?**  
    Jelenleg nem.

* <a id="nested-shares"></a>
**Beállíthatok beágyazott megosztásokat? Más szóval, egy részvény alatt?**  
    Nem. A fájlmegosztás *a* csatlakoztatható virtuális illesztőprogram, így a beágyazott megosztások nem támogatottak.

* <a id="ibm-mq"></a>
**Hogyan használhatom az Azure Files-t az IBM MQ-val?**  
    Az IBM kiadott egy dokumentumot, amely segít az IBM MQ ügyfeleinek az Azure Files konfigurálásában az IBM szolgáltatással. További információt az [IBM MQ többpéldányos várólista-kezelő beállítása a Microsoft Azure Files szolgáltatással című témakörben](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service)talál.

## <a name="see-also"></a>Lásd még
* [Azure-fájlok – problémamegoldás a Windows rendszerben](storage-troubleshoot-windows-file-connection-problems.md)
* [Azure-fájlok – problémamegoldás Linux alatt](storage-troubleshoot-linux-file-connection-problems.md)
* [Azure-fájlok szinkronizálásának hibaelhárítása](storage-sync-files-troubleshoot.md)
