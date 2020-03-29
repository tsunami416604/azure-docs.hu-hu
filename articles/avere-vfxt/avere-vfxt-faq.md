---
title: Gyakori kérdések – Avere vFXT for Azure
description: Gyakori kérdések az Azure-hoz való Avere vFXT-ről
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 186b2c048a9de42318e4af287393d731a4eb16f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153462"
---
# <a name="avere-vfxt-for-azure-faq"></a>Gyakori kérdések az Azure-hoz készült Avere vFXT-ről

Ez a cikk megválaszolja azon kérdéseket, amelyek segíthetnek eldönteni, hogy az Avere vFXT for Azure megfelelő-e az Igényeinek. Alapvető információkat ad az Avere vFXT-ről, és elmagyarázza, hogyan működik más Azure-összetevőkkel és külső gyártóktermékeivel.

## <a name="general"></a>Általános kérdések

### <a name="what-is-avere-vfxt-for-azure"></a>Mi az Azure-hoz készült Avere vFXT?

Az Avere vFXT for Azure egy nagy teljesítményű fájlrendszer, amely gyorsítótárazza az aktív adatokat az Azure-ban a kritikus fontosságú számítási feladatok hatékony feldolgozása érdekében.

### <a name="is-avere-vfxt-a-storage-solution"></a>Az Avere vFXT egy tárolási megoldás?

Nem. Az Avere vFXT for Azure egy *fájlrendszer-gyorsítótár,* amely tárolási környezetekhez, például az EMC-hez vagy a NetApp NAS-hoz vagy egy Azure blobtárolóhoz kapcsolódik. Az Avere vFXT egyszerűsíti az ügyfelektől érkező adatkéréseket, és gyorsítótárazza azokat az adatokat, amelyeket a teljesítmény nagymértékű és időbeli javítása érdekében szolgál. Az Avere vFXT maga nem tárol adatokat. Nincs információja a mögötte tárolt adatok mennyiségéről.

### <a name="is-avere-vfxt-a-tiering-solution"></a>Az Avere vFXT rétegezési megoldás?

Az Avere vFXT for Azure nem ad automatikusan adatokat a gyakori és ritka elérésű szintek között.  

### <a name="how-do-i-know-if-an-environment-is-right-for-avere-vfxt"></a>Honnan tudhatom, hogy egy környezet megfelelő-e az Avere vFXT számára?

A legjobb módja annak, hogy gondolja át ezt a kérdést, hogy kérje, "A számítási feladatok cacheable?" Ez azt, hogy a számítási feladatok magas írási arányt eredményeznek? Egy példa a 80/20 vagy 70/30 olvasási/írási.

Fontolja meg az Avere vFXT for Azure-t, ha olyan fájlalapú analitikus folyamattal rendelkezik, amely nagyszámú Azure-beli virtuális gépen fut, és az alábbi feltételek közül egynek vagy többnek felel meg:

* Az általános teljesítmény lassú vagy inkonzisztens a hosszú fájlhozzáférési idők miatt (a követelményektől függően tíz ezredmásodperc vagy másodperc). Ez a késés elfogadhatatlan az ügyfél számára.

* A feldolgozáshoz szükséges adatok a WAN-környezet túlsó végén találhatók, és az adatok végleges áthelyezése nem praktikus. Előfordulhat, hogy az adatok egy másik Azure-régióban vagy egy ügyfél-adatközpontban.

* Jelentős számú ügyfél kéri az adatokat – például egy nagy teljesítményű számítástechnikai (HPC) fürtben. Az egyidejű kérelmek nagy száma növelheti a késést.

* Az ügyfél szeretné futtatni a jelenlegi folyamat "ahogy van" az Azure virtuális gépek, és szüksége van egy POSIX-alapú megosztott tárolási (vagy gyorsítótárazási) megoldás a méretezhetőség. Az Avere vFXT azure-hoz való használatával nem kell újramegterveznie a munkafolyamatot az Azure Blob storage natív hívásainak kezdeményezéséhez.

* A HPC alkalmazás NFSv3 ügyfeleken alapul. (Bizonyos körülmények között használhatja az SMB 2.1 ügyfeleket, de a teljesítmény korlátozott.)

Az alábbi ábra segíthet megválaszolni ezt a kérdést. Minél közelebb van a munkafolyamat a jobb felső sarokhoz, annál valószínűbb, hogy az Avere vFXT for Azure gyorsítótárazási megoldás megfelelő a környezetéhez.

![Grafikon diagram mutatja, hogy a több ezer ügyféllel rendelkező, olvasási nehéz terhelések jobban megfelelnek az Avere vFXT-nek](media/avere-vfxt-fit-assessment.png)

### <a name="at-what-scale-of-clients-does-the-avere-vfxt-solution-make-the-most-sense"></a>Milyen mértékben az ügyfelek nem az Avere vFXT megoldás, hogy a legtöbb értelme?

Az Avere vFXT gyorsítótár-megoldás több száz, több ezer vagy több tízezer számítási mag kezelésére készült. Ha van néhány gép futó könnyű munka, Avere vFXT nem a megfelelő megoldás.

A tipikus Avere vFXT-ügyfelek körülbelül 1000 PROCESSZORmagtól kezdve nagy terhelést futtatnak. Ezek a környezetek akár 50 000 vagy több magot is megkaphatnak. Mivel az Avere vFXT méretezhető, hozzáadhat csomópontokat, amelyek támogatják ezeket a számítási feladatokat, ahogy nőnek, hogy nagyobb átviteli vagy több IOPS-t igényelnek.

### <a name="how-much-data-can-an-avere-vfxt-environment-store"></a>Mennyi adatot tárolhat egy Avere vFXT környezet?

Az Avere vFXT for Azure egy gyorsítótár. Nem kifejezetten tárolja az adatokat. A ram és az SSD kombinációját használja a gyorsítótárazott adatok tárolására. Az adatok véglegesen egy háttértároló rendszeren (például netapp NAS-rendszeren vagy blobtárolón) tárolódnak. Az Avere vFXT rendszer nem rendelkezik információval a mögötte tárolt adatok mennyiségéről. Az Avere vFXT csak az ügyfelek által kért adatok részhalmazát gyorsítótárazza.  

### <a name="what-regions-are-supported"></a>Mely régiók támogatottak?

Az Avere vFXT for Azure minden régióban támogatott, kivéve a szuverén régiókat (Kína, Németország). Győződjön meg arról, hogy a használni kívánt régió támogatja a nagy mennyiségű számítási magok és az Avere vFXT-fürt létrehozásához szükséges virtuálisgép-példányok.

### <a name="how-do-i-get-help-with-avere-vfxt"></a>Hogyan kaphatok segítséget az Avere vFXT-vel kapcsolatban?

A kisegítő személyzet egy speciális csoportja segítséget nyújt az Avere vFXT for Azure-hoz. Kövesse a [Segítség a rendszerrel kapcsolatban](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) a támogatási jegy megnyitásához az Azure Portalon található támogatási jegy megnyitásához című témakör utasításait.

### <a name="is-avere-vfxt-highly-available"></a>Az Avere vFXT magas rendelkezésre állású?

Igen, az Avere vFXT kizárólag HA megoldásként fut.

### <a name="does-avere-vfxt-for-azure-also-support-other-cloud-services"></a>Az Avere vFXT for Azure más felhőszolgáltatásokat is támogat?

Igen, az ügyfelek egynél több felhőszolgáltatót is használhatnak az Avere vFXT-fürttel. Támogatja az AWS S3 szabványos gyűjtők, a Google Cloud Services szabványos gyűjtők, és az Azure blob tárolók.

> [!NOTE]
> Az Avere vFXT AWS vagy Google Cloud tárolóval való használatáért szoftverdíj vonatkozik. Az Azure blob storage használatáért nincs további szoftverdíj.

## <a name="technical-compute"></a>Műszaki: Számítás

### <a name="can-you-describe-what-an-avere-vfxt-environment-looks-like"></a>Le tudná írni, hogy néz ki egy Avere vFXT környezet?

Az Avere vFXT egy fürtözött berendezés, amely több Azure virtuális gépből áll. A Python-függvénytár kezeli a fürt létrehozását, törlését és módosítását. További információért olvassa el [a What is Avere vFXT for Azure?című](avere-vfxt-overview.md) cikket további információért.

### <a name="what-kind-of-azure-virtual-machines-does-avere-vfxt-run-on"></a>Milyen Azure virtuális gépeken fut az Avere vFXT?  

Az Avere vFXT for Azure cluster microsoft Azure E32s_v3 virtuális gépeket használ.

<!-- ### Can I mix and match virtual machine types for my cluster?

No, you must choose one virtual machine type or the other.
    
### Can I move between virtual machine types?

Yes, there is a migration path to move from one VM type to the other. [Open a support ticket](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) to learn how.
-->

### <a name="does-the-avere-vfxt-environment-scale"></a>Az Avere vFXT környezet méreted?

Az Avere vFXT-fürt lehet akár három virtuálisgép-csomópont, vagy akár 24 csomópont is lehet. Ha úgy gondolja, hogy kilenc csomópontnál több csomópontból álló fürtre van szüksége, forduljon az Azure technikai támogatási szolgálatához, ha úgy véli, hogy kilenc csomópontnál nagyobb fürtre van szüksége. A csomópontok nagyobb száma nagyobb üzembe helyezési architektúrát igényel.

### <a name="does-the-avere-vfxt-environment-autoscale"></a>Az Avere vFXT környezet "automatikus skálázás"?

Nem. A fürtméretet felfelé és lefelé skálázhatja, de a fürtcsomópontok hozzáadása vagy eltávolítása manuális lépés.

### <a name="can-i-run-the-avere-vfxt-cluster-as-a-virtual-machine-scale-set"></a>Futtathatom az Avere vFXT-fürtöt virtuálisgép-méretezési csoportként?

Az Avere vFXT nem támogatja a virtuálisgép-méretezési csoport telepítését. Számos beépített rendelkezésre állási támogatási mechanizmus csak a fürtben részt vevő atomi virtuális gépekhez készült.  

### <a name="can-i-run-the-avere-vfxt-cluster-on-low-priority-vms"></a>Futtathatom az Avere vFXT-fürtöt alacsony prioritású virtuális gépeken?

Nem, a rendszer nek szüksége van egy mögöttes stabil virtuális gépek.

### <a name="can-i-run-the-avere-vfxt-cluster-in-containers"></a>Futtathatom az Avere vFXT-fürtöt tárolókban?

Nem, az Avere vFXT-t független alkalmazásként kell telepíteni.

### <a name="do-the-avere-vfxt-vms-count-against-my-compute-quota"></a>Az Avere vFXT virtuális gépek beleszámítanak a számítási kvótámba?

Igen. Győződjön meg arról, hogy rendelkezik a fürt támogatásához szükséges kvótával a régióban.  

### <a name="can-i-run-the-avere-vfxt-cluster-machines-in-different-availability-zones"></a>Futtathatom az Avere vFXT fürtgépeket különböző rendelkezésre állási zónákban?

Nem. Az Avere vFXT magas rendelkezésre állású modellje jelenleg nem támogatja a különböző rendelkezésre állási zónákban található egyes Avere vFXT-fürttagokat.

### <a name="can-i-clone-avere-vfxt-virtual-machines"></a>Klónozhatom az Avere vFXT virtuális gépeket?

Nem, a támogatott Python-parancsfájlt kell használnia az Avere vFXT-fürt csomópontjainak hozzáadásához vagy eltávolításához. További információ: [Az Avere vFXT-fürt kezelése.](avere-vfxt-manage-cluster.md)

### <a name="is-there-a-vm-version-of-the-software-i-can-run-in-my-own-local-environment"></a>Van egy "VM" változata a szoftver tudok futtatni a saját helyi környezetben?

Nem, a rendszer fürtözött készülékként érhető el, és adott virtuálisgép-típusokon tesztelve. Ez a korlátozás segít az ügyfeleknek elkerülni egy olyan rendszer létrehozását, amely nem támogatja egy tipikus Avere vFXT-munkafolyamat nagy teljesítményű követelményeit.

## <a name="technical-disks"></a>Műszaki: Lemezek

### <a name="what-types-of-disks-are-supported-for-the-azure-vms"></a>Milyen típusú lemezek támogatottak az Azure virtuális gépek?

Az Avere vFXT for Azure 1 TB-os vagy 4 TB-os prémium szintű SSD-konfigurációkat is használhat. A prémium szintű SSD-konfiguráció több felügyelt lemezként telepíthető.

### <a name="does-the-cluster-support-unmanaged-disks"></a>Támogatja a fürt a nem felügyelt lemezeket?

Nem, a fürthöz felügyelt lemezek szükségesek.

### <a name="does-the-system-support-local-attached-ssds"></a>Támogatja a rendszer a helyi (csatlakoztatott) SSD-ket?

Az Avere vFXT for Azure jelenleg nem támogatja a helyi SSD-ket. Az Avere vFXT-hez használt lemezeknek le kell állítaniuk és újra kell indítaniuk, de a helyi csatlakoztatott SSD-k ebben a konfigurációban csak megállíthatók.

### <a name="does-the-system-support-ultra-ssds"></a>Támogatja a rendszer az ultra SSD-ket?

Nem, a rendszer csak a prémium szintű SSD-konfigurációkat támogatja.

### <a name="can-i-detach-my-premium-ssds-and-reattach-them-later-to-preserve-cache-contents-between-use"></a>Leválaszthatom a prémium szintű SSD-ket, és később újra csatlakoztathatom őket, hogy megőrizzem a gyorsítótár tartalmát a használat között?

Az SSD-k leválasztása és újbóli csatlakoztatása nem támogatott. Előfordulhat, hogy a forrás metaadatai vagy fájltartalma megváltozott a felhasználások között, ami adatintegritási problémákat okozhat.

### <a name="does-the-system-encrypt-the-cache"></a>Titkosítja a rendszer a gyorsítótárat?

Az adatok csíkozottak a lemezeken, de nincsenek titkosítva. A lemezek azonban titkosíthatók. További információt az [Azure-beli virtuális gépek házirendjeinek védelme és használata című témakörben talál.](https://docs.microsoft.com/azure/virtual-machines/linux/security-policy#encryption)

## <a name="technical-networking"></a>Műszaki: Hálózatépítés

### <a name="what-network-is-recommended"></a>Milyen hálózat ajánlott?

Ha helyszíni tárolót használ az Avere vFXT-vel, 1 Gb/s-os vagy jobb hálózati kapcsolattal kell rendelkeznie a tároló és a fürt között. Ha kis mennyiségű adattal rendelkezik, és a feladatok futtatása előtt hajlandó adatokat másolni a felhőbe, a VPN-kapcsolat elegendő lehet.

> [!TIP]
> Minél lassabb a hálózati kapcsolat, annál lassabb lesz a kezdeti "hideg" olvasás. Lassú olvasás növeli a munkafolyamat késését.

### <a name="can-i-run-avere-vfxt-in-a-different-virtual-network-than-my-compute-cluster"></a>Futtathatom az Avere vFXT-t a számítási fürtömtől eltérő virtuális hálózatban?

Igen, az Avere vFXT rendszert egy másik virtuális hálózatban hozhatja létre. Olvassa el a terv [a Avere vFXT rendszer](avere-vfxt-deploy-plan.md) a részletekért.

### <a name="does-avere-vfxt-require-its-own-subnet"></a>Az Avere vFXT-nek saját alhálózatra van szüksége?

Igen. Az Avere vFXT szigorúan magas rendelkezésre állású (HA) fürtként fut, és működéséhez több IP-cím szükséges. Ha a fürt saját alhálózatában van, elkerülheti az IP-címütközések kockázatát, ami problémákat okozhat a telepítéshez és a normál működéshez. A fürt alhálózata lehet egy más erőforrások által használt virtuális hálózaton belül, feltéve, hogy az IP-címek nem fedik át egymást.

### <a name="can-i-run-avere-vfxt-on-infiniband"></a>Futtathatom az Avere vFXT-t az InfiniBand-en?

Nem, az Avere vFXT csak Ethernet/IP protokollt használ.

### <a name="how-do-i-access-my-on-premises-nas-environment-from-avere-vfxt"></a>Hogyan érhetem el a helyszíni NAS-környezetemet az Avere vFXT-től?

Az Avere vFXT-környezet, mint bármely más Azure virtuális gép, hogy hálózati átjárón vagy VPN-en keresztül az ügyfél adatközpont (és vissza) irányított hozzáférést igényel. Fontolja meg az Azure ExpressRoute-kapcsolat használatát, ha az elérhető a környezetében.

### <a name="what-are-the-bandwidth-requirements-for-avere-vfxt"></a>Mik az Avere vFXT sávszélesség-követelményei?

A teljes sávszélesség-igény két tényezőtől függ:

* A forrástól kért adatmennyiség
* Az ügyfélrendszer késést űrtűre a kezdeti adatok betöltése során  

Késésre érzékeny környezetek, használjon szálas megoldás minimális kapcsolatsebessége 1 Gbps. Használja az ExpressRoute-ot, ha az elérhető.  

### <a name="can-i-run-avere-vfxt-with-public-ip-addresses"></a>Futtathatom az Avere vFXT-t nyilvános IP-címekkel?

Nem, az Avere vFXT-t ajánlott eljárások révén biztonságos hálózati környezetben kell üzemeltetni.

### <a name="can-i-restrict-internet-access-from-my-clusters-virtual-network"></a>Korlátozhatom az internet-hozzáférést a fürt virtuális hálózatáról?

A virtuális hálózat általában szükség szerint további biztonságot konfigurálhat, de bizonyos korlátozások zavarhatják a fürt működését.

Például a kimenő internet-hozzáférés korlátozása a virtuális hálózatról problémákat okoz a fürtszámára, kivéve, ha olyan szabályt is hozzáad, amely kifejezetten engedélyezi az AzureCloud elérését. Ezt a helyzetet a [GitHub kiegészítő dokumentációja](https://github.com/Azure/Avere/tree/master/src/vfxt/internet_access.md)ismerteti.

A személyre szabott biztonsággal kapcsolatban forduljon az ügyfélszolgálathoz a Segítség a [rendszerrel kapcsolatban](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt)című témakörben.

## <a name="technical-back-end-storage-core-filers"></a>Műszaki: Háttértároló (core filers)

### <a name="how-many-core-filers-does-a-single-avere-vfxt-environment-support"></a>Hány core filers nem egy Avere vFXT környezet támogatása?

Az Avere vFXT-fürt legfeljebb 20 magos fájlkezelőt támogat.

### <a name="how-does-the-avere-vfxt-environment-store-data"></a>Hogyan tárolja az Avere vFXT környezet az adatokat?

Az Avere vFXT nem tároló. Ez egy gyorsítótár, amely beolvassa és írja az adatokat több tárolási célok úgynevezett core filers. Az Avere vFXT prémium szintű SSD-lemezein tárolt adatok átmenetiek, és végül a háttérmagos fájlkezelő tárolóba ürülnek.

### <a name="which-core-filers-does-avere-vfxt-support"></a>Melyik alapvető filers nem Avere vFXT támogatja?

Általánosságban elmondható, hogy az Avere vFXT for Azure a következő rendszereket támogatja alapvető fájlkezelőként:

* Dell EMC Isilon (OneFS 7.1, 7.2, 8.0 és 8.1) 
* NetApp ONTAP (Fürtözött mód 9.4, 9.3, 9.2, 9.1P1, 8.0-8.3) és (7-Mode 7.*, 8.0-8.3)

* Azure blobtárolók (csak helyileg redundáns tárolásesetén)
* AWS S3 gyűjtők
* Google Cloud gyűjtők

### <a name="why-doesnt-avere-vfxt-support-all-nfs-filers"></a>Miért nem támogatja az Avere vFXT az összes NFS fájlkezelőt?

Bár minden NFS platform megfelel az IETF szabványoknak, a gyakorlatban minden megvalósításnak megvan a maga furcsasága. Ezek a részletek befolyásolják, hogy az Avere vFXT hogyan lép kölcsönhatásba a tárolórendszerrel. A támogatott rendszerek a legszélesebb körben használt platformok a piacon.

### <a name="does-avere-vfxt-support-private-object-storage-such-as-swiftstack"></a>Támogatja az Avere vFXT a magánobjektum-tárolást (például a SwiftStacket)?

Az Avere vFXT nem támogatja a magánobjektumok tárolását.

### <a name="how-can-i-get-a-specific-storage-product-under-support"></a>Hogyan kaphatok egy adott tárolási terméket támogatás alatt?

A támogatás a helyszíni kereslet összegén alapul. Ha elegendő bevételalapú kérelem áll a NAS-megoldás támogatásához, akkor megfontoljuk. Az Azure-támogatáson keresztül is lehet kéréseket intézni.

### <a name="can-i-use-azure-blob-storage-as-a-core-filer"></a>Használhatom az Azure Blob storage-ot alapvető fájlkezelőként?

Igen, az Avere vFXT for Azure használhatja a blokk blob tároló, mint egy felhő alapú core filer.

### <a name="what-are-the-storage-account-requirements-for-a-blob-core-filer"></a>Mik a tárolási fiók követelményei egy blob core filer?

A tárfióknak általános célú v2-es (GPv2) fióknak kell lennie, és csak helyileg redundáns tárolásra konfigurálva kell lennie. Georedundáns tárolás és zónaredundáns tárolás nem támogatottak.

Olvassa el [az Azure Blob Storage felhőalapú központi fájlkezelőjét](avere-vfxt-add-storage.md#azure-blob-storage-cloud-core-filer) a tárfiók követelményeivel kapcsolatos további részletekért.

### <a name="can-i-use-archive-blob-storage"></a>Használhatom az archív blobtár-tárolót?

Nem. Az archív tárolásszolgáltatási szint (SLA) nem kompatibilis az Avere vFXT rendszer valós idejű könyvtár- és fájlhozzáférési igényeivel.

### <a name="can-i-use-cool-blob-storage"></a>Használhatom a hűvös blob storage-ot?

Ritka elérésű blobstorage általában nem ajánlott egy Avere vFXT azure-beli core filer. A hűvös szint alacsonyabb tárolási költségeket, de magasabb üzemeltetési költségeket kínál. (További részleteka [Blobok tiltása című](<https://azure.microsoft.com/pricing/details/storage/blobs/>) témakörben látható.) Ha az adatokat gyakran érik el és módosítják vagy törlik, fontolja meg a gyakori elérésű szint használatát.

[A hozzáférési szintek](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers#cool-access-tier) további információt adnak arról, hogy mikor érdemes a ritka elérésű rétegű tárolót vFXT-magfájlkezelőként használni.

### <a name="how-do-i-encrypt-the-blob-container"></a>Hogyan titkosíthatom a blobtárolót?

A blob titkosítása konfigurálható az Azure-ban (előnyben részesített) vagy az Avere vFXT core filer szinten.  

### <a name="can-i-use-my-own-encryption-key-for-a-blob-core-filer"></a>Használhatom a saját titkosítási kulcsomat egy blob core filer-hoz?

Alapértelmezés szerint az adatok titkosítása a Microsoft által felügyelt kulcsok az Azure Blob, table és queue storage, valamint az Azure Files. Saját kulcsot hozhat a Blob storage és az Azure Files titkosításához. Ha az Avere vFXT titkosítást választja, az Avere által létrehozott kulcsot kell használnia, és helyileg kell tárolnia.

## <a name="purchasing"></a>Beszerzés

### <a name="how-do-i-get-avere-vfxt-for-azure-licensing"></a>Hogyan szerezhetem be az Avere vFXT azure-licencelést?

Az Azure Piactéren keresztül egyszerű az Avere vFXT for Azure-licenc beszerzése. Regisztráljon egy Azure-fiókot, majd kövesse [az Avere vFXT-fürt üzembe helyezésére](avere-vfxt-deploy.md) vonatkozó utasításokat egy Avere vFXT-fürt létrehozásához.

### <a name="how-much-does-avere-vfxt-cost"></a>Mennyibe kerül az Avere vFXT?

Az Azure-ban nincs további licencelési díj az Avere vFXT-fürtök használatáért. Az ügyfelek felelősek a tárolási és egyéb Azure-fogyasztási díjakért.

### <a name="can-avere-vfxt-vms-be-run-as-low-priority"></a>Az Avere vFXT virtuális gépek alacsony prioritásúként futtathatók?

Nem, az Avere vFXT-fürtök "mindig bekapcsolt" szolgáltatást igényelnek. A fürtök kikapcsolhatók, ha nincs rá szükség.

## <a name="next-steps"></a>További lépések

Az Azure-hoz készült Avere vFXT első lépéseiből megtudhatja, hogyan tervezheti meg és telepítheti saját rendszerét:

* [Az Avere vFXT rendszer megtervezése](avere-vfxt-deploy-plan.md)
* [Az üzembe helyezés áttekintése](avere-vfxt-deploy-overview.md)
* [Felkészülés az Avere vFXT-fürt létrehozására](avere-vfxt-prereqs.md)
* [Az Avere vFXT-fürt üzembe helyezése](avere-vfxt-deploy.md)

Ha többet szeretne megtudni az Avere vFXT képességeiről és használati eseteiről, látogasson el [az Avere vFXT for Azure oldalra.](https://azure.microsoft.com/services/storage/avere-vfxt/)
