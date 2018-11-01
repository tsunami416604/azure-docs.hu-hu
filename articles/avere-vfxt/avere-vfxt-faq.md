---
title: Gyakori kérdések – Avere vFXT az Azure-hoz
description: – Gyakran ismételt kérdések Avere vFXT az Azure-hoz
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 334b4c912c40949cbecab2173425927d46350d07
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634192"
---
# <a name="avere-vfxt-for-azure-faq"></a>Avere vFXT Azure – GYIK

Ez a cikk, amely segítségével eldöntheti, hogy a Avere vFXT megoldás-e az igényeinek megfelelő kérdésekre ad választ. Ez a Avere vFXT képességekkel kapcsolatos alapvető információkat biztosít, és bemutatja, hogyan működik együtt más Azure-összetevőket és külső gyártóktól származó termékekkel. 

## <a name="general"></a>Általános kérdések 

### <a name="what-is-avere-vfxt-for-azure"></a>Mi az az Azure-ban Avere vFXT?

Avere vFXT az Azure nagy teljesítményű rendszer gyorsítótárazza a hatékony feldolgozás érdekében a kritikus fontosságú számítási feladatokat az Azure Compute aktív adatokat.

### <a name="is-the-avere-vfxt-a-storage-solution"></a>Az a Avere vFXT olyan tárolási megoldást?

Nem. Avere vFXT egy fájlrendszer **gyorsítótár** tárolási környezetekben, például az EMC vagy NetApp NAS- vagy Blob-tároló, amely csatolja. A vFXT leegyszerűsíti az ügyfelektől érkező kérelmek, és az adatokat, ipari méretekben- és idővel a teljesítmény javítása szolgál. A maga vFXT nem tárol adatokat. Ez még nem rendelkezik információval az alapjául szolgáló mögöttes tárolt adatok mennyisége.

### <a name="is-the-avere-vfxt-a-tiering-solution"></a>Az a Avere vFXT rétegezési megoldást?

A Avere vFXT nem automatikusan szintű adatforgalom a gyakori és ritka elérésű szint között.  

### <a name="how-do-i-know-if-an-environment-is-right-for-the-avere-vfxt"></a>Honnan tudhatom meg, ha a környezet ideális a Avere vFXT?

A legjobb módszer gondolja át ezt a kérdést tehet fel, a "a gyorsítótárazható munkaterhelés?" Azt jelenti nem rendelkezik a számítási feladatok magas olvasási arány – például 80-as/20 vagy 70 – 30 olvasási/írási műveleteket írni.

Vegye figyelembe Avere vFXT az Azure-hoz, ha egy fájl-alapú elemzési folyamatot, amely fut egyszerre nagy számú Azure-beli virtuális gépek, és megfelel-e az alábbi feltételek közül:

* A teljes teljesítmény lassú vagy inkonzisztensként miatt hosszú fájl elérés időpontját (több tíz ezredmásodperc vagy másodperc, követelményeitől függően). Ez a késés nem fogadható el, a végfelhasználók számára.

* Az adatok feldolgozásához WAN-környezethez távolságban végén található, majd véglegesen áthelyezi az adatokat nem kivitelezhető. Az adatok lehet egy másik Azure-régióban, vagy egy ügyfél az adatközpontban.

* Az ügyfelek jelentős számú kért az adatokat – például egy nagy teljesítményű feldolgozási (HPC-) fürt. A nagyszámú egyidejű kérelmet is növelheti a késést.

* Az ügyfél szeretné futtatni a jelenlegi adatcsatorna ",-van" az Azure-beli virtuális gépek, és igényeinek megfelelően a POSIX-alapú megosztott tároló (vagy gyorsítótárazás) megoldás, a méretezhetőség érdekében. Avere vFXT használatával az Azure-ban, nem kell a munkahelyi folyamat az Azure Blob storage-natív hívásokat újratervezése.

* A HPC-alkalmazás NFSv3 ügyfelek alapul. (Bizonyos esetekben a SMB 2.1 ügyfelek is használható, de teljesítmény korlátozva.)

   Az alábbi ábra próbál egyszerűsítése a választ a kérdésére. Minél közelebb a munkafolyamatot, hogy a jobb felső sarokban, annál valószínűbb, hogy, hogy a Avere gyorsítótárazási megoldás a legmegfelelőbb környezete számára.

   ![az olyan adatbázisoknál Avere vFXT, hogy ezer ügyfél olvasási betöltések jobbak bemutató ábra.](media/avere-vfxt-fit-assessment.png)

### <a name="at-what-scale-of-clients-does-the-avere-vfxt-solution-make-the-most-sense"></a>Az ügyfelek milyen mennyiségű does a Avere, vFXT megoldás jelentéssel bírnak a legtöbbet?

A vFXT gyorsítótár megoldás több száz, több ezer vagy több ezer, a számítási magok fejlesztették. Ha van néhány gépeken könnyű munka, Avere vFXT, nem a megfelelő megoldás.

Tipikus Avere vFXT ügyfelek körülbelül 1000 Processzormagok díjtól erőforrás-igényű számítási feladatok futtatásához. Ezekben a környezetekben akkora, mint 50 000 magok vagy nagyobb lehet. Mivel a vFXT méretezhető, támogatják ezeket a feladatokat a okoznának igényelnek további átviteli sebesség vagy IOPS további csomópontokat adhat hozzá.

### <a name="how-much-data-can-an-avere-vfxt-environment-store"></a>Mennyi adatot tárolhat Avere vFXT környezet?

Avere vFXT gyorsítótár, a nem az adatokat külön tárolni. RAM-MAL és SSD-k együttes használatával tárolja a gyorsítótárazott adatok. Az adatok véglegesen tárolja a háttér-tároló rendszerben (például a NetApp NAS rendszer vagy egy Blob-tárolóba). A Avere vFXT rendszer nincs információ a mögötte; tárolt adatok mennyisége a vFXT csak gyorsítótárazza az ügyfél által lekért adatok részhalmazát.  

### <a name="what-regions-are-supported"></a>Mely régiók támogatottak?

2018. November 1. minden régióban szuverén régiók (Kína, Németország) kivételével, és a government-régiók Avere vFXT az Azure-hoz támogatott. Győződjön meg arról, hogy a használni kívánt régiót is támogatja a nagy mennyiségű számítási magok, valamint a Virtuálisgép-példányok a Avere vFXT fürt létrehozásához szükséges. Szuverén régiók és a kormányzati felhők még nem támogatottak.

### <a name="how-do-i-get-help-with-the-avere-vfxt"></a>Hogyan szerezhetem be a Avere vFXT segítséget?

Egy speciális támogatási csoport nyújt segítséget a Avere vFXT az Azure-hoz. Kövesse a [segítség a rendszer a](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) , nyisson egy támogatási jegyet az Azure Portalról. 

### <a name="is-the-avere-vfxt-highly-available"></a>Az a Avere vFXT magas rendelkezésre állású?

Igen, a Avere vFXT fut kizárólag egy magas rendelkezésre ÁLLÁSÚ megoldás.

### <a name="does-avere-vfxt-for-azure-also-support-other-cloud-services"></a>Avere vFXT Azure is támogatja a felhőszolgáltatásokra?

Azt jelzi, Igen, ügyfelek használhatják-e a Avere vFXT fürttel egynél több felhőszolgáltató. Az AWS S3 normál gyűjtők és Google Cloud Services normál gyűjtők, valamint az Azure Blob-tárolók támogatja. 

> [!NOTE] 
> Egy szoftver díj vonatkozik az AWS vagy a Google Cloud, de nem az Azure-ral Avere vFXT használatára.

## <a name="technical---compute"></a>Technical Preview – számítási

### <a name="can-you-describe-what-an-avere-vfxt-environment-looks-like"></a>Leírhatja milyen Avere vFXT környezet "néz"?

A Avere vFXT készült a több Azure-beli virtuális gépek egy fürtözött készülék. Egy Python-kódtár kezeli a fürt létrehozása, törlése és módosítása. Olvasási [Mi az az Azure-ban Avere vFXT?](avere-vfxt-overview.md) további. 

### <a name="what-kind-of-azure-virtual-machines-does-the-avere-vfxt-run-on"></a>Milyen típusú Azure-beli virtuális gépek nem a Avere vFXT futtatását?  

Az Azure-fürtben Avere vFXT vagy a Microsoft Azure E32s_v3, vagy a D16s_v3 virtuális gépek használ. 

### <a name="can-i-mix-and-match-virtual-machine-types-for-my-cluster"></a>E szabadon kombinálhatók a virtuális gépek típusainak a fürtöm számára?

Nem, ki kell választania egy virtuális gép típusát és a más.
    
### <a name="can-i-move-between-virtual-machine-types"></a>Áthelyezhető virtuálisgép-típusok között?

Igen, nincs típusú virtuális gép áthelyezése a másik áttelepítési elérési útját. [Hozzon létre egy támogatási jegyet](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) megtudhatja, hogyan.

### <a name="does-the-avere-vfxt-environment-scale"></a>A Avere vFXT környezet skálázása nem?

A Avere vFXT fürt legyen a három virtuálisgép-csomópontok és akkora, mint 24 csomópontok is lehetnek. Ha úgy véli, hogy kilencnél több csomópontból álló fürtben kell tervezési segítségért forduljon a technikai Azure-támogatást. A nagyobb mennyiségű csomópont nagyobb üzembe helyezési architektúra szükséges.

### <a name="does-the-avere-vfxt-environment-autoscale"></a>Nem a Avere vFXT környezet "automatikus"?

Nem. A fürt méretének felfelé és lefelé skálázhatja, de hozzáadásával vagy eltávolításával a fürtcsomópontok egy manuális lépés.

### <a name="can-i-run-the-vfxt-cluster-as-a-virtual-machine-scale-set"></a>Futtathatom a vFXT fürt, egy virtuálisgép-méretezési csoportot?

Avere vFXT nem támogatja a virtuális gépek méretezési csoportot (VMSS) üzembe helyezése. Számos beépített rendelkezésre állási támogatási mechanizmusok csak egy fürtben résztvevő atomi virtuális gépek lettek kialakítva.  

### <a name="can-i-run-the-vfxt-cluster-on-low-priority-vms"></a>Futtathatom egyszerre a vFXT fürt az alacsony prioritású virtuális gépek?

Nem, a rendszer egy virtuális gép mögöttes stabil készlete van szükség.

### <a name="can-i-run-the-vfxt-cluster-in-containers"></a>Futtathatom egyszerre a vFXT fürt tárolókban?

Nem, a Avere vFXT kell telepíteni önálló alkalmazást.

### <a name="do-the-avere-vfxt-vms-count-against-my-compute-quota"></a>A következő lépés a Avere vFXT virtuális gépek száma a számítási kvótával ellen?

Igen. Ellenőrizze, hogy rendelkezik elegendő kvótával a fürt támogatásához a régióban.  

### <a name="can-i-run-the-avere-vfxt-cluster-machines-in-different-availability-zones"></a>Futtathatom egyszerre a Avere vFXT fürt gépek különböző rendelkezésre állási zónában?

Nem. A jelenleg használt Avere vFXT magas rendelkezésre állású modell nem támogatja a különböző rendelkezésre állási zónában található egyes vFXT fürt tagjainak.

### <a name="can-i-clone-avere-vfxt-virtual-machines"></a>Is Avere vFXT virtuális gépek klónozása?

Nem, adja hozzá vagy távolíthat el csomópontokat a Avere vFXT fürtben, a támogatott Python-szkriptet kell használnia. További információkért olvassa el [Avere vFXT fürtfelügyeleti](avere-vfxt-manage-cluster.md).  

### <a name="is-there-a-vm-version-of-the-software-i-can-run-in-my-own-local-environment"></a>Van egy "VM" verzióját a saját helyi környezetben futtatható szoftvereket?

Nem, a rendszer szolgálhat egy fürtözött készülék és a konkrét virtuálisgép-típusok tesztelve. Ez a korlátozás segítségével az ügyfelek ne hozzon létre egy rendszer, amely nem támogatja a magas teljesítmény-követelmények a tipikus Avere vFXT munkafolyamatok. 

## <a name="technical---disks"></a>Technical Preview – lemezek

### <a name="what-type-of-disks-are-supported-for-the-azure-vms"></a>Milyen típusú lemezek az Azure-beli virtuális gépek támogatottak?

Az Azure-ban Avere vFXT 1 TB vagy 4 TB-os prémium szintű SSD-konfigurációk használhatja. A prémium szintű SSD-konfiguráció több felügyelt lemez telepíthető.

### <a name="does-the-cluster-support-unmanaged-disks"></a>A fürt támogatja a nem felügyelt lemezek?

A fürt nem felügyelt lemezt igényel.

### <a name="does-the-system-support-local-attached-ssds"></a>A rendszer támogatja a helyi (csatlakoztatott) SSD-k?

Az Azure-ban Avere vFXT jelenleg nem támogatja helyi SSD-kkel. A Avere vFXT használt lemezek leáll és újraindul képesnek kell lennie, de a helyi csatlakoztatott SSD-k ebben a konfigurációban csak állíthatók le.

### <a name="does-the-system-support-ultra-ssds"></a>A rendszer támogatja a ultranagy SSD-k?

Nem, a rendszer támogatja a prémium szintű SSD konfigurációkat.

### <a name="can-i-detach-my-premium-ssds-and-reattach-them-later-to-preserve-cache-contents-between-use"></a>A prémium szintű SSD-k leválasztása és csatlakoztassa újból őket később segítségével megőrizheti a gyorsítótár tartalmának használata között?

Leválasztása és SSD-k újracsatlakoztatása nem támogatott. Metaadatok vagy a fájl tartalmát a forrás között használ, amely adatok épségével kapcsolatos problémákat okozhat esetleg megváltoztak.

### <a name="does-the-system-encrypt-the-cache"></a>A rendszer titkosítja a gyorsítótár?

Adatok szétteríti a lemezeket, de nem titkosított. Azonban a egymás lemezek titkosíthatók. További információ található [Itt](https://docs.microsoft.com/azure/virtual-machines/linux/security-policy#encryption).

## <a name="technical---networking"></a>Technikai - hálózatkezelés

### <a name="what-network-is-recommended"></a>Milyen hálózati ajánlott?

Ha a használatával a helyszínen a Avere vFXT tárolóval, egy 1 – GB/s vagy nagyobb hálózati kapcsolat kell rendelkeznie. Ha kis mennyiségű adatot, és hajlandó másolja az adatokat a felhőben a feladat futtatása előtt, VPN-kapcsolat is elegendő lehet. 

> [!TIP] 
> A lassabb a hálózati kapcsolat van, a lassabb lesz a kezdeti ritka elérésű olvasási. Lassú olvasási növelheti a késést, a munkahelyi folyamat. 

### <a name="can-i-run-the-avere-vfxt-in-a-different-virtual-network-than-my-compute-cluster"></a>Futtathatom egyszerre a Avere vFXT egy másik virtuális hálózatban, mint a számítási fürt?

Igen, hozhat létre a Avere vFXT rendszer egy másik virtuális hálózatot. Olvasási [megtervezése a Avere vFXT rendszer](avere-vfxt-deploy-plan.md) részleteiről.

### <a name="does-the-avere-vfxt-require-its-own-subnet"></a>A Avere vFXT igényel a saját alhálózatában?

Igen. A Avere vFXT szigorúan csak egy magas rendelkezésre ÁLLÁSÚ fürt, és szükség van a több IP-cím a művelethez használandó. Ha a fürtöt a saját alhálózatán található, elkerülheti a telepítési és a normál működés problémát okozhat IP-címütközés kockázatát. A fürt alhálózatot a meglévő virtuális hálózaton belül lehet mindaddig, amíg nem IP-címek között átfedés van.

### <a name="can-i-run-the-avere-vfxt-on-infiniband"></a>Futtathatom egyszerre a Avere vFXT az Infiniband?

Nem, a Avere vFXT használ Ethernet/IP-cím csak.

### <a name="how-do-i-access-my-on-premises-nas-environment-from-the-avere-vfxt"></a>Hogyan férhetek hozzá a saját helyszíni NAS-környezetben a Avere vFXT származó?

A Avere vFXT környezet semmiben nem különbözik bármely más Azure virtuális Gépet, annak, hogy az ügyfél adatközpont (és a biztonsági) a hálózati átjáró vagy VPN irányított hozzáférésre van szüksége, azt. Fontolja meg az Azure ExpressRoute-kapcsolat, ha az adott környezetben érhető el.

### <a name="what-are-the-bandwidth-requirements-for-the-avere-vfxt"></a>Mik azok a Avere vFXT sávszélesség követelményei?

A teljes sávszélesség követelmény két tényezőktől függ: 

* A forrásból a kért adatok mennyisége 
* Az ügyfélrendszer szintű késést kezdeti adatok betöltése során  

Késésérzékeny környezetekben 1 GB/s sebességű minimális hivatkozás fiber megoldást kell használnia. Expressroute-ot használni, ha rendelkezésre áll.  

### <a name="can-i-run-the-vfxt-with-public-ip-addresses"></a>Futtathatom egyszerre a vFXT nyilvános IP-címmel?

Nem, a vFXT célja, hogy az működtetni, ajánlott eljárások használatával biztonságos hálózati környezetben.  

## <a name="technical---backend-storage-core-filers"></a>Technical Preview – háttérrendszer storage (core kiemelik)

### <a name="how-many-core-filers-does-a-single-avere-vfxt-environment-support"></a>Hány core kiemelik támogatja egyetlen Avere vFXT környezethez?

Egy Avere vFXT fürt legfeljebb 20 core kiemelik támogatja. 

### <a name="how-does-the-avere-environment-store-data"></a>Hogyan a Avere környezet adatok tárolása?

A Avere vFXT tároló nem áll. A gyorsítótár, amely adatokat olvas be ír a core kiemelik nevű több tárolási cél. A prémium szintű SSD-lemezeket a Avere vFXT tárolt adatok átmeneti, és végül kiürül, a háttérbeli core filer Storage.

### <a name="which-core-filers-does-avere-vfxt-support"></a>Mely alapvető kiemelik támogatja Avere vFXT?

Általánosságban Avere vFXT Azure core kiemelik, a következő rendszereket támogatja: 

* A Dell EMC Isilon (OneFS 7.1-es, 7.2, 8.0-s és 8.1 esetén) 
* NetApp ONTAP (9.3, 9.2, mód 9.4, fürtözött 9.1P1, 8.0-8.3) és (7 módú 7.*, 8.0-8.3) 
* Az Azure Blob-tárolók (csak az LRS esetén) 
* Az AWS S3 gyűjtő 
* Google Cloud gyűjtők

### <a name="why-doesnt-the-avere-vfxt-support-all-nfs-filers"></a>Miért nem támogatja az összes NFS kiemelik a Avere vFXT?

Bár az összes NFS-platformok IETF követelményeknek azonos, a gyakorlatban minden implementáció rendelkezik a saját régi stílusú. Ezek a részletek érinti, és a tárolórendszer a Avere vFXT együttműködését. A támogatott rendszereket a leggyakrabban használt platformokat, a Marketplace-en.

### <a name="does-avere-vfxt-support-private-object-storage-such-as-swiftstack"></a>Támogatja a Avere vFXT privát objektumtár (például Swiftstack)?

Avere vFXT nem támogatja a privát objektumok tárolásához.

### <a name="how-can-i-get-a-specific-storage-product-under-support"></a>Hogyan kaphatok támogatást egy adott tárolási termék?

Támogatási igény szerint, a mezőben mennyisége alapján. Ha elegendő bevétel-alapú kérelmek egy adott NAS-megoldás támogatja, akkor minősül. A kérést az Azure-támogatás révén.

### <a name="can-i-use-azure-blob-storage-as-a-core-filer"></a>Használhatom az Azure Blob storage, a core filer?

Igen, Azure Avere vFXT használhatja egy block Blob-tároló a felhő alapvető filer.  

### <a name="what-are-the-storage-account-requirements-for-a-blob-core-filer"></a>Mik azok a Blob core filer storage-fiókra vonatkozó követelmények?

A tárfiókban kell lennie egy általános célú v2 (GPv2-) fiók, és a helyileg redundáns tárolás (LRS) csak konfigurálva. Grs-t és a ZRS nem támogatottak.

### <a name="can-i-use-archive-blob-storage"></a>Használhatom az Archive Blob storage?

Nem. Az Archive storage a szolgáltatói szerződés nem kompatibilis a valós idejű címtár és vFXT rendszer kell hozzá a fájlhoz. 

### <a name="can-i-use-cool-blob-storage"></a>Használható a ritka elérésű blobtároló?

A lassú elérési szint használata, de vegye figyelembe, hogy sokkal magasabb lesz-e a műveletek sebessége. 

### <a name="how-do-i-encrypt-the-blob-container"></a>Hogyan titkosíthatom a Blob-tároló?

Vagy beállíthatja a Blob titkosítását, az Azure-ban (preferált) vagy a vFXT core filer szintjén.  

### <a name="can-i-use-my-own-encryption-key-for-a-blob-core-filer"></a>Használható egy Blob core filer a saját titkosítási kulcsot?

Alapértelmezés szerint az adatok titkosítása az Azure-Blobok, táblák, fájlok és üzenetsorok használatával a Microsoft által kezelt kulcsokkal. A saját Azure-Blobok és fájlok titkosítási kulcsot is tenné. Ha vFXT titkosítás használatát választja, kell Avere által létrehozott kulcs használata, és helyileg tárolja. 

## <a name="purchasing"></a>Vásárlás

### <a name="how-do-i-get-avere-vfxt-for-azure-licensing"></a>Hogyan kaphatok Avere vFXT az Azure-Licenckezelés?

Egy Avere vFXT lekérdezése az Azure-licenc is egyszerűen az Azure piactéren. Azure-fiókot regisztráljon, és kövesse az utasításokat a [vFXT-fürt üzembe helyezése](avere-vfxt-deploy.md) hozhat létre Avere vFXT fürtöt. 

### <a name="how-much-does-the-avere-vfxt-cost"></a>A Avere vFXT mennyibe?

Az Azure-ban nem Avere vFXT fürtök használatának nincs további licencelési díja. Ügyfelei felelősek a tárolási és egyéb Azure-használati díjak.

### <a name="can-avere-vfxt-vms-be-run-as-low-priority"></a>Futtatható Avere vFXT virtuális gépek alacsony prioritásúként?

Nem, a Avere vFXT fürtök kötelező "always on" szolgáltatás. A fürtök is kapcsolható ki, ha nincs rá szükség. 

## <a name="next-steps"></a>További lépések

Első lépések Avere vFXT az Azure-hoz, olvassa el az alábbi hivatkozások megtudhatja, hogyan tervezheti meg és a saját rendszerek központi telepítéséhez:

* [A Avere vFXT rendszer tervezése](avere-vfxt-deploy-plan.md)
* [Az üzembe helyezés áttekintése](avere-vfxt-deploy-overview.md)
* [Készítse elő a Avere vFXT létrehozása](avere-vfxt-prereqs.md)
* [A vFXT-fürt üzembe helyezése](avere-vfxt-deploy.md)

További Avere vFXT lehetőségekről, és a használati esetek, látogasson el a [Avere vFXT az Azure-ban (előzetes verzió)](https://azure.microsoft.com/services/storage/avere-vfxt/).
