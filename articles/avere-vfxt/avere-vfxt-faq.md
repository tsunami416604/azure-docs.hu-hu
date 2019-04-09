---
title: Gyakori kérdések – Avere vFXT az Azure-hoz
description: – Gyakran ismételt kérdések Avere vFXT az Azure-hoz
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: v-erkell
ms.openlocfilehash: 69921300163bd9a326f3baedd3182da887ad02c4
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59057149"
---
# <a name="avere-vfxt-for-azure-faq"></a>Gyakori kérdések az Azure-hoz készült Avere vFXT-ről

Ez a cikk kérdésekre ad választ, amelyek segítségével eldöntheti, hogy az igényeinek megfelelő Avere vFXT Azure-e. Ez a Avere vFXT alapvető információkat biztosít, és bemutatja, hogyan működik együtt más Azure-összetevőket és külső gyártóktól származó termékekkel. 

## <a name="general"></a>Általános kérdések 

### <a name="what-is-avere-vfxt-for-azure"></a>Mi az Azure-hoz készült Avere vFXT?

Az Azure-ban Avere vFXT nagy teljesítményű rendszer gyorsítótárazza a hatékony feldolgozás érdekében kritikus fontosságú számítási feladatok Azure-beli számítási az aktív adatok.

### <a name="is-avere-vfxt-a-storage-solution"></a>Az Avere vFXT olyan tárolási megoldást?

Nem. Avere vFXT egy olyan fájlrendszer *gyorsítótár* tárolási környezetekben, például az EMC vagy NetApp NAS vagy egy Azure blob-tároló, amely csatolja. Avere vFXT leegyszerűsíti az ügyfelektől érkező kérelmek, és azt gyorsítótárazza az adatokat, amelyek nagy mennyiségű- és idővel a teljesítmény javítása szolgál. Avere vFXT maga nem tárol adatokat. Ez még nem rendelkezik információval az alapjául szolgáló mögöttes tárolt adatok mennyisége.

### <a name="is-avere-vfxt-a-tiering-solution"></a>Az Avere vFXT rétegezési megoldást?

Avere vFXT nem automatikusan szintű adatforgalom a gyakori és ritka elérésű szint között.  

### <a name="how-do-i-know-if-an-environment-is-right-for-avere-vfxt"></a>Honnan tudhatom meg, ha a környezet ideális Avere vFXT?

A legjobb módszer gondolja át ezt a kérdést tehet fel, a "a gyorsítótárazható munkaterhelés?" Azt jelenti a munkaterhelés a magas olvasási és írási arány van? Ilyen például, a 80-as/20 vagy 70 – 30 olvasási/írási műveleteket.

Vegye figyelembe Avere vFXT az Azure-hoz, ha egy fájl-alapú elemzési folyamatot, amely fut egyszerre nagy számú Azure-beli virtuális gépek, és megfelel-e az alábbi feltételek közül:

* A teljes teljesítmény lassú vagy inkonzisztensként miatt hosszú fájl elérés időpontját (több tíz ezredmásodperc vagy másodperc, követelményeitől függően). Ez a késés nem fogadható el az ügyfélnek.

* Az adatok feldolgozásához WAN-környezethez távolságban végén található, majd véglegesen áthelyezi az adatokat nem kivitelezhető. Az adatok lehet egy másik Azure-régióban, vagy egy ügyfél az adatközpontban.

* Az ügyfelek jelentős számú kért az adatokat – például egy nagy teljesítményű feldolgozási (HPC-) fürt. A nagyszámú egyidejű kérelmet is növelheti a késést.

* Az ügyfél szeretné futtatni a jelenlegi adatcsatorna "adott állapotában" Azure-beli virtuális gépeken, és van szüksége a POSIX-alapú megosztott, storage (vagy gyorsítótárazás) megoldás, a méretezhetőség érdekében. Avere vFXT használatával az Azure-ban, nem kell a munkahelyi folyamat az Azure Blob storage-natív hívásokat újratervezése.

* A HPC-alkalmazás NFSv3 ügyfelek alapul. (Bizonyos esetekben a SMB 2.1 ügyfél képes használni, de teljesítmény korlátozva.)

A következő ábra leegyszerűsíti a választ a kérdésére. Minél közelebb a munkafolyamatot, hogy a jobb felső sarokban, annál valószínűbb, hogy, hogy a Avere gyorsítótárazási megoldás a legmegfelelőbb környezete számára.

![az olyan adatbázisoknál Avere vFXT, hogy ezer ügyfél olvasási betöltések jobbak bemutató ábra.](media/avere-vfxt-fit-assessment.png)

### <a name="at-what-scale-of-clients-does-the-avere-vfxt-solution-make-the-most-sense"></a>Az ügyfelek milyen mennyiségű does a Avere, vFXT megoldás jelentéssel bírnak a legtöbbet?

A Avere vFXT gyorsítótár megoldás több száz, több ezer vagy több ezer, a számítási magok fejlesztették. Ha van néhány gépeken könnyű munka, Avere vFXT, nem a megfelelő megoldás.

Tipikus Avere vFXT ügyfelek körülbelül 1000 Processzormagok díjtól erőforrás-igényű számítási feladatok futtatásához. Ezekben a környezetekben akkora, mint 50 000 magok vagy nagyobb lehet. Mivel Avere vFXT méretezhető, támogatják ezeket a feladatokat a okoznának igényelnek további átviteli sebesség vagy IOPS további csomópontokat adhat hozzá.

### <a name="how-much-data-can-an-avere-vfxt-environment-store"></a>Mennyi adatot tárolhat Avere vFXT környezet?

Avere vFXT gyorsítótár. Ez nem kifejezetten tárolhatja az adatokat. RAM-MAL és SSD-k együttes használatával tárolja a gyorsítótárazott adatok. Az adatok véglegesen tárolja a háttér-tároló rendszerben (például a NetApp NAS rendszer vagy egy blob-tárolóba). A Avere vFXT rendszer nincs információ a mögötte tárolt adatok mennyisége. Avere vFXT csak gyorsítótárazza az ügyfél által lekért adatok részhalmazát.  

### <a name="what-regions-are-supported"></a>Mely régiók támogatottak?

Szuverén régiók (Kína, Németország) kivételével minden régióban Avere vFXT az Azure-hoz támogatott. Győződjön meg arról, hogy a használni kívánt régiót is támogatja a nagy mennyiségű számítási magok és a Virtuálisgép-példányok a Avere vFXT fürt létrehozásához szükséges.

### <a name="how-do-i-get-help-with-avere-vfxt"></a>Hogyan szerezhetem be az Avere vFXT kapcsolatban?

Egy speciális támogatási csoport nyújt segítséget az Avere vFXT az Azure-hoz. Kövesse a [segítség a rendszer a](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) , nyisson egy támogatási jegyet az Azure Portalról. 

### <a name="is-avere-vfxt-highly-available"></a>A magas rendelkezésre állású Avere vFXT?

Igen, Avere vFXT fut kizárólag egy magas rendelkezésre ÁLLÁSÚ megoldás.

### <a name="does-avere-vfxt-for-azure-also-support-other-cloud-services"></a>Avere vFXT Azure is támogatja a felhőszolgáltatásokra?

Azt jelzi, Igen, ügyfelek használhatják-e a Avere vFXT fürttel egynél több felhőszolgáltató. Az AWS S3 standard gyűjtők, Google Cloud Services normál gyűjtők és az Azure blob-tárolók támogatja. 

> [!NOTE] 
> Egy szoftver díj vonatkozik az AWS vagy a Google Cloud, de nem az Azure-ral Avere vFXT használatára.

## <a name="technical-compute"></a>Technikai: Compute

### <a name="can-you-describe-what-an-avere-vfxt-environment-looks-like"></a>Leírhatja milyen Avere vFXT környezet "néz"?

Avere vFXT készült a több Azure-beli virtuális gépek egy fürtözött készülék. Egy Python-kódtár kezeli a fürt létrehozása, törlése és módosítása. Olvasási [Mi az az Azure-ban Avere vFXT?](avere-vfxt-overview.md) további. 

### <a name="what-kind-of-azure-virtual-machines-does-avere-vfxt-run-on"></a>Milyen típusú Azure-beli virtuális gépek futtatása a Avere vFXT?  

Egy Azure-fürt Avere vFXT E32s_v3 a Microsoft Azure virtuális gépeket használ. 

<!-- ### Can I mix and match virtual machine types for my cluster?

No, you must choose one virtual machine type or the other.
    
### Can I move between virtual machine types?

Yes, there is a migration path to move from one VM type to the other. [Open a support ticket](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) to learn how.

-->

### <a name="does-the-avere-vfxt-environment-scale"></a>A Avere vFXT környezet skálázása nem?

A Avere vFXT fürt legyen a három virtuálisgép-csomópontok és akkora, mint 24 csomópontok is lehetnek. Forduljon a technikai Azure-támogatás megtervezéséhez, ha úgy véli, hogy kilencnél több csomópontból álló fürtben van szüksége. A nagyobb mennyiségű csomópont egy nagyobb méretű üzembe helyezési architektúra szükséges.

### <a name="does-the-avere-vfxt-environment-autoscale"></a>Nem a Avere vFXT környezet "automatikus"?

Nem. A fürt méretének felfelé és lefelé skálázhatja, de hozzáadásával vagy eltávolításával a fürtcsomópontok egy manuális lépés.

### <a name="can-i-run-the-avere-vfxt-cluster-as-a-virtual-machine-scale-set"></a>Futtathatom a Avere vFXT fürt, egy virtuálisgép-méretezési csoportot?

Avere vFXT nem támogatja a központi telepítését egy virtuálisgép-méretezési csoportot. Számos beépített rendelkezésre állási támogatási mechanizmusok csak egy fürtben résztvevő atomi virtuális gépek lettek kialakítva.  

### <a name="can-i-run-the-avere-vfxt-cluster-on-low-priority-vms"></a>Futtathatom egyszerre a Avere vFXT fürt az alacsony prioritású virtuális gépek?

Nem, a rendszer egy virtuális gép mögöttes stabil készlete van szükség.

### <a name="can-i-run-the-avere-vfxt-cluster-in-containers"></a>Futtathatom egyszerre a Avere vFXT fürt tárolókban?

Nem, Avere vFXT kell telepíteni önálló alkalmazást.

### <a name="do-the-avere-vfxt-vms-count-against-my-compute-quota"></a>A következő lépés a Avere vFXT virtuális gépek száma a számítási kvótával ellen?

Igen. Ellenőrizze, hogy elegendő kvótával rendelkezik támogatásához a fürt a régióban.  

### <a name="can-i-run-the-avere-vfxt-cluster-machines-in-different-availability-zones"></a>Futtathatom egyszerre a Avere vFXT fürt gépek különböző rendelkezésre állási zónában?

Nem. A magas rendelkezésre állású modell Avere vFXT jelenleg nem támogatja az egyes Avere vFXT fürt tagjainak különböző rendelkezésre állási zónában található.

### <a name="can-i-clone-avere-vfxt-virtual-machines"></a>Is Avere vFXT virtuális gépek klónozása?

Nem, adja hozzá vagy távolíthat el csomópontokat a Avere vFXT fürtben, a támogatott Python-szkriptet kell használnia. További információkért olvassa el [Avere vFXT fürtfelügyeleti](avere-vfxt-manage-cluster.md).  

### <a name="is-there-a-vm-version-of-the-software-i-can-run-in-my-own-local-environment"></a>Van egy "VM" verzióját a saját helyi környezetben futtatható szoftvereket?

Nem, a rendszer szolgálhat egy fürtözött készülék és a konkrét virtuálisgép-típusok tesztelve. Ez a korlátozás segítségével az ügyfelek ne hozzon létre egy rendszer, amely nem támogatja a tipikus Avere vFXT munkafolyamatok nagy teljesítményű követelményeit. 

## <a name="technical-disks"></a>Technikai: Lemezek

### <a name="what-types-of-disks-are-supported-for-the-azure-vms"></a>Milyen típusú lemezek az Azure-beli virtuális gépek támogatottak?

Az Azure-ban Avere vFXT 1 TB vagy 4 TB-os prémium szintű SSD-konfigurációk használhatja. A prémium szintű SSD-konfiguráció több felügyelt lemez telepíthető.

### <a name="does-the-cluster-support-unmanaged-disks"></a>A fürt támogatja a nem felügyelt lemezek?

A fürt nem felügyelt lemezt igényel.

### <a name="does-the-system-support-local-attached-ssds"></a>A rendszer támogatja a helyi (csatlakoztatott) SSD-k?

Az Azure-ban Avere vFXT jelenleg nem támogatja helyi SSD-kkel. Avere vFXT használt lemezek leáll és újraindul képesnek kell lennie, de a helyi csatlakoztatott SSD-k ebben a konfigurációban csak állíthatók le.

### <a name="does-the-system-support-ultra-ssds"></a>A rendszer támogatja a ultranagy SSD-k?

Nem, a rendszer támogatja a prémium szintű SSD konfigurációkat.

### <a name="can-i-detach-my-premium-ssds-and-reattach-them-later-to-preserve-cache-contents-between-use"></a>A prémium szintű SSD-k leválasztása és csatlakoztassa újból őket később segítségével megőrizheti a gyorsítótár tartalmának használata között?

Leválasztása és SSD-k újracsatlakoztatása nem támogatott. Metaadatok vagy a fájl tartalmát a forrás között használ, amely az adatok épségével kapcsolatos problémákat okozhat esetleg megváltoztak.

### <a name="does-the-system-encrypt-the-cache"></a>A rendszer titkosítja a gyorsítótár?

Adatok szétteríti a lemezeket, de nem titkosított. Azonban a egymás lemezek titkosíthatók. További információkért lásd: [biztonságos és használható szabályzatokat az Azure-beli virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/linux/security-policy#encryption).

## <a name="technical-networking"></a>Technikai: Hálózat

### <a name="what-network-is-recommended"></a>Milyen hálózati ajánlott?

A helyszíni tárolók Avere vFXT együtt használja, ha rendelkezik egy 1 – GB/s vagy nagyobb hálózati kapcsolat. Ha kis mennyiségű adatot, és hajlandó másolja az adatokat a felhőben a feladat futtatása előtt, VPN-kapcsolat elég lehet. 

> [!TIP] 
> A lassabb a hálózati kapcsolat van, a lassabb lesz a kezdeti ritka elérésű olvasási. Lassú olvasási növelheti a késést, a munkahelyi folyamat. 

### <a name="can-i-run-avere-vfxt-in-a-different-virtual-network-than-my-compute-cluster"></a>Futtathatók Avere vFXT egy másik virtuális hálózatban, mint a számítási fürt?

Igen, hozhat létre a Avere vFXT rendszer egy másik virtuális hálózatot. Olvasási [megtervezése a Avere vFXT rendszer](avere-vfxt-deploy-plan.md) részleteiről.

### <a name="does-avere-vfxt-require-its-own-subnet"></a>Szükség van a saját alhálózatán Avere vFXT?

Igen. Avere vFXT szigorúan fürtöt hoz létre magas rendelkezésre ÁLLÁS és szükség van a több IP-cím a művelethez használandó. Ha a fürtöt a saját alhálózatán található, elkerülheti a telepítési és a normál működés problémát okozhat IP-címütközés kockázatát. A fürt alhálózatot a meglévő virtuális hálózaton belül lehet mindaddig, amíg nem IP-címek között átfedés van.

### <a name="can-i-run-avere-vfxt-on-infiniband"></a>Futtathatok Avere vFXT InfiniBand?

Nem, Avere vFXT használ Ethernet/IP-cím csak.

### <a name="how-do-i-access-my-on-premises-nas-environment-from-avere-vfxt"></a>Hogyan férhetek hozzá a saját helyszíni NAS-környezetben a Avere vFXT?

Olyan Avere vFXT környezetre, mint bármely más Azure virtuális Gépet, hogy az ügyfél datacenter (és a biztonsági) a hálózati átjáró vagy VPN irányított hozzáférésre van szüksége, azt. Fontolja meg az Azure ExpressRoute-kapcsolat, ha az adott környezetben érhető el.

### <a name="what-are-the-bandwidth-requirements-for-avere-vfxt"></a>Mik azok a Avere vFXT sávszélesség követelményei?

A teljes sávszélesség követelmény két tényezőktől függ: 

* A forrásból a kért adatok mennyisége 
* Az ügyfélrendszer szintű késést kezdeti adatok betöltése során  

Késésérzékeny környezetekben 1 GB/s sebességű minimális hivatkozás fiber megoldást kell használnia. Expressroute-ot használni, ha rendelkezésre áll.  

### <a name="can-i-run-avere-vfxt-with-public-ip-addresses"></a>Futtathatók Avere vFXT nyilvános IP-címmel?

Nem, Avere vFXT célja, hogy az működtetni – ajánlott eljárások biztonságos hálózati környezetben.  

### <a name="can-i-restrict-internet-access-from-my-clusters-virtual-network"></a>Internet-hozzáférés korlátozhatom a fürt virtuális hálózatból? 

Általában további biztonsági konfigurálhatja a vnet igény szerint, de bizonyos korlátozások megzavarhatja a fürt működését.

Például a virtuális hálózatról kimenő internet-hozzáférés korlátozása problémákat okoz a fürt számára, ha a szabályokat, amelyek hozzáférhetnek a AzureConnectors AzureCloud explicit módon engedélyezik is hozzáadhat. Ez a helyzet leírt [kiegészítő dokumentációjában, a Githubon](https://github.com/Azure/Avere/tree/master/src/vfxt/internet_access.md).

Testre szabott biztonsági segítségért forduljon az ügyfélszolgálathoz leírtak szerint [segítség a rendszer a](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt).

## <a name="technical-back-end-storage-core-filers"></a>Technikai: Háttér-tároló (core kiemelik)

### <a name="how-many-core-filers-does-a-single-avere-vfxt-environment-support"></a>Hány core kiemelik támogatja egyetlen Avere vFXT környezethez?

Egy Avere vFXT fürt legfeljebb 20 core kiemelik támogatja. 

### <a name="how-does-the-avere-vfxt-environment-store-data"></a>Hogyan a Avere vFXT környezet adatok tárolása?

Avere vFXT tároló nem áll. A gyorsítótár, amely adatokat olvas be ír a core kiemelik nevű több tárolási cél. A prémium szintű SSD-lemezeket a Avere vFXT tárolt adatok átmeneti, és végül kiürül, a háttér-core filer Storage.

### <a name="which-core-filers-does-avere-vfxt-support"></a>Mely alapvető kiemelik támogatja Avere vFXT?

Általánosságban Avere vFXT Azure core kiemelik, a következő rendszereket támogatja: 

* A Dell EMC Isilon (OneFS 7.1-es, 7.2, 8.0-s és 8.1-es) 
* NetApp ONTAP (9.3, 9.2, mód 9.4, fürtözött 9.1P1, 8.0-8.3) és (7 módú 7.*, 8.0-8.3) 

  > [!NOTE] 
  > NetApp Azure Files jelenleg nem támogatott. 

* Az Azure blob-tárolók (csak a helyileg redundáns tárolás) 
* Az AWS S3 gyűjtő 
* Google Cloud gyűjtők

### <a name="why-doesnt-avere-vfxt-support-all-nfs-filers"></a>Miért nem támogatja az összes NFS kiemelik Avere vFXT?

Bár az összes NFS-platformok IETF követelményeknek azonos, a gyakorlatban minden implementáció rendelkezik a saját régi stílusú. Ezek az adatok érinti, és a tárolórendszer Avere vFXT együttműködését. A támogatott rendszereket a leggyakrabban használt platformokat, a Marketplace-en.

### <a name="does-avere-vfxt-support-private-object-storage-such-as-swiftstack"></a>Támogatja a Avere vFXT privát objektumtár (például SwiftStack)?

Avere vFXT nem támogatja a privát objektumok tárolásához.

### <a name="how-can-i-get-a-specific-storage-product-under-support"></a>Hogyan kaphatok támogatást egy adott tárolási termék?

Támogatási igény szerint, a mezőben mennyisége alapján. Ha elegendő bevétel-alapú kérelmek támogatásához NAS-megoldás, hogy érdemes lesz. A kérést az Azure-támogatás révén.

### <a name="can-i-use-azure-blob-storage-as-a-core-filer"></a>Használhatom az Azure Blob storage, a core filer?

Igen, Avere vFXT az Azure-hoz, egy block blob-tárolóba, a felhő alapvető filer használhatja.  

### <a name="what-are-the-storage-account-requirements-for-a-blob-core-filer"></a>Mik azok a blob core filer storage-fiókra vonatkozó követelmények?

A tárfiókban kell lennie egy általános célú v2 (GPv2-) fiók, és csak a helyileg redundáns tárolás konfigurálva. Georedundáns tárolás és a zónaredundáns tárolás nem támogatottak.

### <a name="can-i-use-archive-blob-storage"></a>Az archive blob storage használata

Nem. Az archive storage szolgáltatásiszint-szerződés (SLA) nem kompatibilis a valós idejű címtár és az Avere vFXT rendszer fájl hozzáférés igényeit. 

### <a name="can-i-use-cool-blob-storage"></a>Használható a ritka elérésű blobtároló?

A lassú elérési szint használata, de vegye figyelembe, hogy sokkal magasabb lesz-e a műveletek sebessége. 

### <a name="how-do-i-encrypt-the-blob-container"></a>Hogyan titkosíthatom a blob-tároló?

Blobtitkosítás konfigurálhatja az Azure-ban (preferált) vagy a Avere vFXT core filer szintjén.  

### <a name="can-i-use-my-own-encryption-key-for-a-blob-core-filer"></a>Használható egy blob core filer a saját titkosítási kulcsot?

Alapértelmezés szerint az adatok titkosítása az Azure Blob, Table és Queue storage, valamint az Azure Files a Microsoft által felügyelt kulcsokkal. A Blob storage és az Azure Files a saját titkosítási kulcsot is tenné. Ha Avere vFXT titkosítás használatát választja, kell Avere által létrehozott kulcs használata, és helyileg tárolja. 

## <a name="purchasing"></a>Vásárlás

### <a name="how-do-i-get-avere-vfxt-for-azure-licensing"></a>Hogyan kaphatok Avere vFXT az Azure-Licenckezelés?

Egy Avere vFXT lekérdezése az Azure-licenc is egyszerűen az Azure piactéren. Iratkozzon fel az Azure-fiókot, és kövesse az utasításokat a [a Avere vFXT fürt üzembe helyezéséhez](avere-vfxt-deploy.md) hozhat létre Avere vFXT fürtöt. 

### <a name="how-much-does-avere-vfxt-cost"></a>Mennyibe Avere vFXT szolgáltatás?

Az Azure-ban nem Avere vFXT fürtök használatának nincs további licencelési díja. Ügyfelei felelősek a tárolási és egyéb Azure-használati díjak.

### <a name="can-avere-vfxt-vms-be-run-as-low-priority"></a>Futtatható Avere vFXT virtuális gépek alacsony prioritásúként?

Nem, a Avere vFXT fürtök kötelező "always on" szolgáltatás. A fürtök is kapcsolható ki, ha nincs rá szükség. 

## <a name="next-steps"></a>További lépések

Első lépések Avere vFXT az Azure-hoz, olvassa el ezeket a cikkeket megtudhatja, hogyan tervezheti meg és a saját rendszerek központi telepítéséhez:

* [Az Avere vFXT rendszer megtervezése](avere-vfxt-deploy-plan.md)
* [Üzembe helyezés – áttekintés](avere-vfxt-deploy-overview.md)
* [-Avere vFXT fürt előkészítése](avere-vfxt-prereqs.md)
* [A Avere vFXT fürt üzembe helyezése](avere-vfxt-deploy.md)

További információ és eseteinek Avere vFXT látogasson el [Avere vFXT Azure](https://azure.microsoft.com/services/storage/avere-vfxt/).
