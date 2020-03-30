---
title: Vész-helyreállítási útmutató az Avere vFXT for Azure-hoz
description: Az Azure-hoz készült Avere vFXT adatainak védelme a véletlen törléssel vagy kimaradásokkal szemben
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rohogue
ms.openlocfilehash: 28278f76497d6e9d0fee221bb4ef32fe6d369db0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966647"
---
# <a name="disaster-recovery-guidance-for-avere-vfxt-for-azure"></a>Vész-helyreállítási útmutató az Avere vFXT for Azure-hoz

Ez a cikk az Avere vFXT azure-beli munkafolyamatvédelmére vonatkozó stratégiákat vázol fel, és útmutatást ad az adatok biztonsági mentéséhez, hogy a balesetek vagy kimaradások helyreállíthassa őket.

Az Avere vFXT for Azure ideiglenesen tárolja az adatokat a gyorsítótárában. Az adatok hosszú távú tárolórendszerekben tárolódnak – helyszíni hardverrendszerekben, Azure Blob tárolótárolókban vagy mindkettőben.

A kimaradások és az esetleges adatvesztés elleni védelem érdekében vegye figyelembe az alábbi négy területet:

* Az állásidő elleni védelem, ha az Avere vFXT for Azure rendszer elérhetetlenné válik
* Adatok védelme a fürt gyorsítótárában
* Adatok védelme a háttér-NAS hardvertárolóban
* Adatok védelme háttérszintű Azure Blob felhőalapú tárhelyen

Minden Avere vFXT Azure-ügyfélnek létre kell hoznia a saját átfogó vész-helyreállítási terv, amely tartalmazza az ilyen elemek hez szükséges terveket. A vFXT-fürttel használt alkalmazások rugalmasságát is létrehozhatja. A [súgóért](#next-steps) olvassa el a Következő lépések című témakörben található hivatkozásokat.

## <a name="protect-against-downtime"></a>Védelem az állásidő ellen

A redundancia be van építve az Avere vFXT for Azure termékbe:

* A fürt magas rendelkezésre állású, és az egyes fürtcsomópontok minimális megszakítással átvehetik a feladatát.
* A gyorsítótárban megváltozott adatok at rendszeresen írják a háttér-core filers (hardver Es vagy Azure Blob) a hosszú távú tároláshoz.

Minden Avere vFXT azure-fürthöz kell elhelyezni egy rendelkezésre állási zónában, de használhatja redundáns fürtök található különböző zónákban vagy különböző régiókban, hogy gyorsan hozzáférést regionális kimaradás esetén.

A tárolótárolókat több régióban is elhelyezheti, ha aggódik az adatokhoz való hozzáférés elvesztése miatt. Ne feledje azonban, hogy a régiók közötti tranzakciók nagyobb késéssel és magasabb költséggel rendelkeznek, mint a régión belül maradó tranzakciók.

## <a name="protect-data-in-the-cluster-cache"></a>Adatok védelme a fürt gyorsítótárában

A gyorsítótárazott adatok at a rendszer a normál leállítás előtt mindig a központi fájlkezelőkbe írja, de ellenőrizetlen leállítás esetén a gyorsítótárban lévő megváltozott adatok elveszhetnek.

Ha a fürt ötöt csak a fájlolvasás optimalizálására használ, nincsenek elveszni való módosítások. Ha a fürtöt fájlmódosítások (írások) gyorsítótárazására is használja, fontolja meg, hogy módosítja-e az alapvető fájlkezelők [gyorsítótár-házirendjeit.](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html)<!-- link to legacy doc --> az adatok hosszú távú tárolásba írásának módját.

A helyreállítási tervnek általában a háttértároló rendszerek biztonsági mentésére kell összpontosítania, amelyek több adatot tárolnak, és általában fontosabbak a munkafolyamat hiba utáni visszaállításához.

## <a name="protect-data-in-nas-core-filers"></a>Adatok védelme a NAS core filers-ben

A helyszíni NAS hardvermag-fájlkezelőben tárolt adatok védelméhez használjon elfogadott módszereket, beleértve a pillanatképeket és a nas-szolgáltató által ajánlott teljes biztonsági mentéseket is. Ezek a központi filers vész-helyreállítási hatókörön kívül esik a jelen cikk.

## <a name="protect-data-in-azure-blob-storage"></a>Adatok védelme az Azure Blob storage-ban

Az Azure-hoz az Avere vFXT helyileg redundáns tárolást (LRS) használ az Azure Blob alapvető fájlkezelőihez. Ez azt jelenti, hogy a Blob-tárolókban lévő adatok automatikusan másolásra kerülnek az adatközponton belüli átmeneti hardverhibák elleni védelem érdekében.

Ez a szakasz tippeket ad a blobstorage-ban tárolt adatok ritka régiószintű kimaradások vagy véletlen törlések elleni további védelméhez.

Az Azure Blob storage-beli adatok védelmével kapcsolatos gyakorlati tanácsok a következők:

* Másolja a kritikus adatokat egy másik régióban lévő másik tárfiókba gyakran (olyan gyakran, ahogy azt a vész-helyreállítási terv határozza meg).
* A véletlen törlés vagy sérülés elkerülése érdekében szabályozhatja az összes célrendszeren lévő adatokhoz való hozzáférést. Fontolja meg [az erőforrás-zárolások](../azure-resource-manager/management/lock-resources.md) használatát az adattárolásban.
* Engedélyezze az Avere vFXT [azure-beli felhőbeli pillanatkép-funkciót](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_snapshot_policies.html>) a Blob core filers számára.

### <a name="copy-avere-vfxt-core-filer-data-to-a-backup-account"></a>Avere vFXT core filer adatainak másolása biztonsági másolatba

Az alábbi lépésekkel készíthet adatbiztonsági másolatot egy másik fiókban.

1. Szükség esetén hozzon létre egy új titkosítási kulcsot, és tárolja biztonságosan az érintett rendszereken kívül.

   Ha az adatokat az Avere vFXT azure-fürt, létre kell hoznia egy új titkosítási kulcsot, mielőtt az adatok másolása egy másik tárfiókba. Tárolja a kulcsot és a jelszót biztonságosan egy biztonságos létesítményben, amely nem lesz hatással a regionális hiba.

   Ezt a kulcsot meg kell adnia, amikor hozzáadja a tárolót egy fürthöz – még akkor is, ha újra hozzáadja az eredeti fürthöz.

   [Felhőtitkosítási beállítások olvasása](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_encryption_settings.html>)<!-- link to legacy doc site --> részletes információkért.

   Ha a tároló csak az Azure beépített titkosítását használja, kihagyhatja ezt a lépést.

1. Távolítsa el a központi fájlkezelőt a rendszerből. Ez arra kényszeríti a fürtöt, hogy az összes módosított adatot a háttértárolóba írja.

   Bár a biztonsági mentés után újra hozzá kell adnia a központi fájlkezelőt, az eltávolítás a legjobb módja annak, hogy garantálja, hogy az összes adat teljesen a háttérrendszerbe kerül. (A "felfüggesztés" lehetőség néha a gyorsítótárban hagyhatja a módosított adatokat.) <!-- xxx true? or just metadata? -->

   Jegyezze fel az alapvető fájlkezelő nevét és csatlakozási adatait (a vezérlőpult **Névtér** lapján felsorolva), hogy replikálhassa azt, amikor újra hozzáadja a tárolót a biztonsági mentés után.

   A fürt vezérlőpultjával távolítsa el a központi fájlkezelőt. [Nyissa meg a fürt vezérlőpultját,](avere-vfxt-cluster-gui.md) és válassza a **Core filer** > Manage**core filers (Core filer Manage core filers**) lehetőséget. Keresse meg a biztonsági másolatot, és az **Eltávolítás** gombbal törölje azt a fürtből.

1. Hozzon létre egy új, üres Blob storage-tárolót egy másik régióban lévő másik tárolófiókban.

1. Bármilyen kényelmes másolási eszközzel másolja az adatokat a core filer az új tárolóba. A másolatnak módosítások nélkül kell replikálnia az adatokat, és meg kell szakítania az Avere vFXT for Azure-hoz használt saját felhőalapú fájlrendszer-formátumot. Az Azure-alapú eszközök közé tartozik [az AzCopy,](../storage/common/storage-use-azcopy-v10.md) [az Azure PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)és az [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

1. Miután átmásolta az adatokat a biztonsági másolat tárolóba, adja hozzá az eredeti tárolót a fürthöz a [Tároló konfigurálása](avere-vfxt-add-storage.md)című részben leírtak szerint.

   * Használja ugyanazt az alapvető fájlkezelő nevet és csomóponti információt, hogy az ügyfél-munkafolyamatokat ne kelljen módosítani.
   * Állítsa a **Gyűjtő tartalom** értékét a meglévő adatbeállításra.
   * Ha a tárolót a fürt titkosította, meg kell adnia az aktuális titkosítási kulcsot a tartalmához. (Ez az a kulcs, amelyet az első lépésben frissített.)

Biztonsági mentések után az első, nem kell létrehozni egy új tárolótárolót. Azonban fontolja meg egy új titkosítási kulcs létrehozása minden alkalommal, amikor csinál egy biztonsági másolatot, hogy megbizonyosodjon arról, hogy az aktuális kulcs tárolják egy helyen, amire emlékszik.

### <a name="access-a-backup-data-source-during-an-outage"></a>Biztonsági másolat adatforrásának elérése kimaradás közben

A biztonsági mentési tároló eléréséhez egy Avere vFXT azure-fürthöz, kövesse ezt a folyamatot:

1. Ha szükséges, hozzon létre egy új Avere vFXT Azure-fürt höz egy nem érintett régióban.

   > [!TIP]
   > Amikor létrehoz egy Avere vFXT azure-fürthöz, mentheti a létrehozási sablon és a paraméterek másolatát. Ha az elsődleges fürt létrehozásakor menti ezeket az adatokat, akkor ugyanazzal a tulajdonságokkal rendelkező cserefürt létrehozásához használhatja. Az [összesítő](avere-vfxt-deploy.md#validation-and-purchase) lapon kattintson a **Letöltés sablon és paraméterek** hivatkozásra. A fürt létrehozása előtt mentse az adatokat egy fájlba.

1. Adjon hozzá egy új felhőalapú core filer, amely rámutat a duplikált Blob tároló.

   Győződjön meg arról, hogy a céltároló már tartalmaz adatokat a Fő fájlkészítő készítő varázsló **Bucket contents** beállításában. (A rendszernek figyelmeztetnie kell, ha véletlenül **üresre**hagyja ezt a készletet.)  <!-- you can't add a populated volume at cluster creation time via template, only create a fresh one -->

1. Ha szükséges, frissítse az ügyfeleket úgy, hogy az eredeti helyett az új fürtöt vagy az új core filer-t csatlakoztassa. (Ha a helyettesítő magfájlkezelőt az eredeti tárolóval azonos nevű és csomóponti elérési úttal adja hozzá, nem kell frissítenie az ügyfélfolyamatokat, hacsak nem kell az új fürtöt új IP-címre csatlakoztatnia.)

## <a name="next-steps"></a>További lépések

* Az Avere vFXT azure-hoz beállításainak testreszabásáról a [Fürthangolás](avere-vfxt-tuning.md)című olvasni tudó további információt talál.
* További információ a vészhelyreállításról és a rugalmas alkalmazások azure-beli létrehozásáról:

  * [Műszaki útmutató az Azure rugalmasságáról](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)
  * [Helyreállítás egész régióra kiterjedő szolgáltatáskimaradás esetén](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)
  * [Vészhelyreállítás és magas szintű rendelkezésre állás az Azure-alkalmazásokhoz](<https://docs.microsoft.com/azure/resiliency/resiliency-disaster-recovery-high-availability-azure-applications>)
  <!-- can't find these in the source tree to use relative links -->
