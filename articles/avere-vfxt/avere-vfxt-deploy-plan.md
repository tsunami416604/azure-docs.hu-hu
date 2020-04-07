---
title: Tervezze meg Avere vFXT rendszerét - Azure
description: A cikk ismerteti az Avere vFXT azure-hoz való üzembe helyezése előtti tervezést
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: 6acc1ffd197ddba4290ff7c0751b259d98a70927
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754394"
---
# <a name="plan-your-avere-vfxt-system"></a>Az Avere vFXT rendszer megtervezése

Ez a cikk bemutatja, hogyan tervezheti meg az új Avere vFXT azure-fürthöz, amely az igényeinek megfelelően van elhelyezve és méretezve.

Mielőtt az Azure Marketplace-re kerülne, vagy bármilyen virtuális gépet hozna létre, vegye figyelembe az alábbi adatokat:

* Hogyan lép kapcsolatba a fürt más Azure-erőforrásokkal?
* Hol kell a fürtelemeket magánhálózatokban és alhálózatokban elhelyezni?
* Milyen típusú háttértárolót fog használni, és hogyan fogja a fürt hozzáférni?
* Milyen hatékonynak kell lennie a fürtcsomópontoknak a munkafolyamat támogatásához?

Olvasson tovább, hogy többet.

## <a name="learn-the-components-of-the-system"></a>Ismerje meg a rendszer összetevőit

Hasznos lehet az Avere vFXT for Azure rendszer összetevőinek megismerése a tervezés megkezdésekor.

* Fürtcsomópontok – A fürt három vagy több fürtcsomópontként konfigurált virtuális gépből áll. További csomópontok nagyobb átviteli és nagyobb gyorsítótárat adnak a rendszernek.

* Gyorsítótár – A gyorsítótár kapacitása egyenlően oszlik meg a fürtcsomópontok között. A fürt létrehozásakor állítsa be a csomópontonkénti gyorsítótár méretét; a csomópontméretek hozzáadódnak, hogy a gyorsítótár teljes mérete legyen.

* Fürtvezérlő – A fürtvezérlő egy további virtuális gép, amely ugyanazon az alhálózaton belül található, mint a fürtcsomópontok. A vezérlő a fürt létrehozásához és a folyamatban lévő felügyeleti feladatokhoz szükséges.

* Háttér-tároló – A gyorsítótárazandó adatok hosszú távon egy hardvertároló rendszerben vagy egy Azure Blob-tárolóban tárolódnak. Az Avere vFXT azure-fürthöz való létrehozása után hozzáadhat tárhelyet, vagy ha blob tárolót használ, hozzáadhatja és konfigurálhatja a tárolót a fürt létrehozása közben.

* Ügyfelek – A gyorsítótárazott fájlokat használó ügyfélgépek virtuális fájlelérési úttal csatlakoznak a fürthöz a tárolórendszerek közvetlen elérése helyett. (Tovább a [Mount a Avere vFXT klaszter](avere-vfxt-mount-clients.md).)

## <a name="subscription-resource-group-and-network-infrastructure"></a>Előfizetés, erőforráscsoport és hálózati infrastruktúra

Fontolja meg, hogy az Avere vFXT azure-beli üzembe helyezési elemei hol lesznek. Az alábbi ábra az Avere vFXT Azure-összetevőkre vonatkozó lehetséges elrendezését mutatja be:

![A fürtvezérlőt és a fürt virtuális gépeit egy alhálózaton belül mutatja. Az alhálózati határ körül egy vnet-határ. A virtuális hálózaton belül egy hatszög képviseli a tárolási szolgáltatás végpontját; egy blob tárolóhoz a virtuális hálózaton kívül egy szaggatott nyílhoz kapcsolódik.](media/avere-vfxt-components-option.png)

Az Avere vFXT-fürt hálózati infrastruktúrájának tervezésekor kövesse az alábbi irányelveket:

* Hozzon létre egy új előfizetést minden Avere vFXT azure-telepítéshez. Az előfizetés összes összetevőjének kezelése.

  Az új előfizetések használatának előnyei az egyes telepítésekhez a következők:
  * Egyszerűbb költségkövetés – Egyetlen előfizetésben tekintheti meg és naplózta az erőforrásokból, infrastruktúrából és számítási ciklusokból származó összes költséget.
  * Egyszerűbb karbantartás – A projekt befejezéseután eltávolíthatja a teljes előfizetést.
  * Az erőforráskvóták kényelmes particionálása – Az Avere vFXT-ügyfelek és a fürt egyetlen előfizetésben elkülönítése más kritikus fontosságú számítási feladatok védelme érdekében a lehetséges erőforrás-szabályozással szemben. Ez a szétválasztás megakadályozza az ütközést, amikor nagyszámú ügyfelet hoz létre egy nagy teljesítményű számítástechnikai munkafolyamathoz.

* Keresse meg az ügyfél számítási rendszerek közel a vFXT-fürthöz. A háttértárolók távolibbak is lehetnek.  

* Keresse meg a vFXT-fürt és a fürtvezérlő virtuális gép együtt - különösen, hogy kell:

  * Ugyanabban a virtuális hálózatban
  * Ugyanabban az erőforráscsoportban
  * Ugyanannak a tárfióknak a használata
  
  A fürtlétrehozási sablon a legtöbb esetben kezeli ezt a konfigurációt.

* A fürtnek a saját alhálózatában kell lennie, hogy elkerülje az IP-cím ütközését az ügyfelekkel vagy más számítási erőforrásokkal.

* A fürtlétrehozási sablon nal hozd létre a fürt höz szükséges infrastruktúra-erőforrások többségét, beleértve az erőforráscsoportokat, a virtuális hálózatokat, az alhálózatokat és a tárfiókokat.

  Ha már meglévő erőforrásokat szeretne használni, győződjön meg arról, hogy azok megfelelnek az ebben a táblában szereplő követelményeknek.

  | Erőforrás | Használja a meglévőt? | Követelmények |
  |----------|-----------|----------|
  | Erőforráscsoport | Igen, ha üres | Üresnek kell lennie.|
  | Tárfiók | **Igen,** ha egy meglévő Blob-tároló csatlakoztatása a fürt létrehozása után <br/>  **Nem,** ha új Blob-tároló létrehozása a fürt létrehozása során | A meglévő Blob-tárolónak üresnek kell lennie <br/> &nbsp; |
  | Virtuális hálózat | Igen | Új Azure Blob-tároló létrehozása esetén tartalmaznia kell egy tárolási szolgáltatás végpontját |
  | Alhálózat | Igen | Más erőforrások nem tartalmazhatnak |

## <a name="ip-address-requirements"></a>IP-címre vonatkozó követelmények

Győződjön meg arról, hogy a fürt alhálózata elég nagy IP-címtartományt biztosít a fürt támogatásához.

Az Avere vFXT-fürt a következő IP-címeket használja:

* Egy fürtkezelési IP-cím. Ez a cím szükség szerint áthelyezhető csomópontról csomópontra a fürtben, hogy mindig elérhető legyen. Ezen a címen csatlakozhat az Avere Vezérlőpult konfigurációs eszközéhez.
* Minden fürtcsomópontesetében:
  * Legalább egy ügyfélfelé néző IP-cím. (Az összes ügyfélfelé néző címet a fürt *v-kiszolgálója*kezeli, amely szükség szerint áthelyezheti az IP-címeket a csomópontok között.)
  * Egyetlen IP-cím a fürtkommunikációhoz
  * Egy példány IP-címe (a virtuális géphez rendelve)

Ha Azure Blob storage-t használ, a fürt virtuális hálózatáról is szükség lehet IP-címekre:  

* Egy Azure Blob storage-fiók legalább öt IP-címet igényel. Tartsa ezt a követelményt, ha a Blob storage-ot ugyanabban a virtuális hálózatban találja, mint a fürt.
* Ha a fürt virtuális hálózatán kívül található Azure Blob-tárolót használja, hozzon létre egy tárolási szolgáltatás végpontját a virtuális hálózaton belül. A végpont nem használ IP-címet.

Lehetősége van a hálózati erőforrások és a Blob storage (ha használják) a fürt különböző erőforráscsoportokban.

## <a name="vfxt-node-size"></a>vFXT csomópont mérete

A fürtcsomópontokként szolgáló virtuális gépek határozzák meg a gyorsítótár kérelemátviteli és tárolási kapacitását. <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

Minden vFXT-csomópont azonos lesz. Ez azt, hogy ha háromcsomópontos fürtöt hoz létre, három azonos típusú és méretű virtuális géplesz.

| Példány típusa | vCPU-k | Memory (Memória)  | Helyi SSD-tároló  | Adatlemezek max. száma | Nem gyorsítótárazott lemezátviteli-rendszer | NIC (szám) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51 200 IOPS <br/> 768 Mb/s | 16 000 Mb/s (8)  |

A lemez gyorsítótára csomópontonként konfigurálható, és 1000 GB-ról 8000 GB-ra tombolhat. Csomópontonként 4 TB az ajánlott gyorsítótárméret Standard_E32s_v3 csomópontokhoz.

A virtuális gépekről további információt a Microsoft Azure dokumentációjában talál: [Memóriaoptimalizált virtuálisgép-méretek](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>Fiókkvóta

Győződjön meg arról, hogy az előfizetés képes az Avere vFXT-fürt, valamint a használt számítástechnikai vagy ügyfélrendszerek futtatásához. A részletekért olvassa el [a vFXT-fürt kvótáját.](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)

## <a name="back-end-data-storage"></a>Háttéradat-tárolás

A háttértároló rendszerek fájlokat szolgáltatnak a fürt gyorsítótárába, és módosított adatokat is fogadnak a gyorsítótárból. Döntse el, hogy a munkakészlet hosszú távon egy új Blob-tárolóban vagy egy meglévő tárolórendszerben (felhőben vagy hardverben) lesz-e tárolva. Ezeket a háttértároló rendszereket *mag-filers-nek nevezzük.*

### <a name="hardware-core-filers"></a>Hardveres magfilerek

A fürt létrehozása után adja hozzá a hardvertároló rendszereket a vFXT-fürthöz. Számos népszerű hardverrendszert használhat, beleértve a helyszíni rendszereket is, feltéve, hogy a tárolórendszer elérhető a fürt alhálózatáról.

A [Tároló konfigurálása](avere-vfxt-add-storage.md) című, meglévő tárolórendszer Avere vFXT-fürthöz való hozzáadásáról szóló részletes útmutatást olvassa el.

### <a name="cloud-core-filers"></a>Felhőalapú core filerek

Az Avere vFXT azure-rendszer üres Blob-tárolókat használhat a háttér-tároláshoz. A tárolóknak üresnek kell lenniük, amikor hozzáadódnak a fürthöz – a vFXT-rendszernek képesnek kell lennie az objektumtároló kezelésére anélkül, hogy meg kellene őriznie a meglévő adatokat.

> [!TIP]
> Ha az Azure Blob storage-ot a háttérrendszerhez szeretné használni, hozzon létre egy új tárolót a virtuális gépfürt létrehozásának részeként. A fürt létrehozási sablon hozhat létre és konfigurálhat egy új Blob-tárolót, hogy készen álljon a használatra, amint a fürt elérhetővé válik. A tároló későbbi hozzáadása bonyolultabb.
>
> A részletekért olvassa [el Az Avere vFXT létrehozása az Azure-hoz](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure) című olvasni.

Miután hozzáadja az üres Blob storage tárolót core filerként, adatokat másolhat a fürtön keresztül. Használjon párhuzamos, többszálas másolási mechanizmust. Olvassa el [az adatok áthelyezése a vFXT-fürtbe](avere-vfxt-data-ingest.md) című olvasnivalót, amelyből megtudhatja, hogyan másolhat adatokat hatékonyan a fürt új tárolójára az ügyfélgépek és az Avere vFXT-gyorsítótár használatával.

## <a name="cluster-access"></a>Fürthozzáférés

Az Avere vFXT azure-fürt höz található egy privát alhálózat, és a fürt nem rendelkezik nyilvános IP-címmel. A fürtfelügyelet és az ügyfélkapcsolatok hoz létre valamilyen módon a magánalhálózatot.

A hozzáférési lehetőségek a következők:

* Jump host – Nyilvános IP-cím hozzárendelése egy külön virtuális gép hez a magánhálózaton belül, és ezzel tls-alagutat hozhat létre a fürtcsomópontokhoz.

  > [!TIP]
  > Ha nyilvános IP-címet állít be a fürtvezérlőn, használhatja ugrási állomásként. További információért olvassa el [a fürtvezérlőt ugróállomásként.](#cluster-controller-as-jump-host)

* Virtuális magánhálózat (VPN) – Konfiguráljon egy pont-hely vagy helyek közötti VPN-t az Azure-beli magánhálózat és a vállalati hálózatok között.

* Azure ExpressRoute – Privát kapcsolat konfigurálása ExpressRoute-partneren keresztül.

Ezekről a lehetőségekről az [Azure Virtual Network internetes kommunikációval kapcsolatos dokumentációjában olvashat részletesen.](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet)

### <a name="cluster-controller-as-jump-host"></a>Fürtvezérlő ugrásfogadóként

Ha nyilvános IP-címet állít be a fürtvezérlőn, ugróállomásként használhatja az Avere vFXT-fürthöz való kapcsolatfelvételhez a magánalhálózaton kívülről. Mivel azonban a vezérlő hozzáférési jogosultságokkal rendelkezik a fürtcsomópontok módosításához, ez kis biztonsági kockázatot jelent.

A nyilvános IP-címmel rendelkező vezérlő biztonságának növelése érdekében a központi telepítési parancsfájl automatikusan létrehoz egy hálózati biztonsági csoportot, amely csak a 22-es porthoz való bejövő hozzáférést korlátozza. Tovább védheti a rendszert az IP-forráscímek tartományához való hozzáférés zárolásával , azaz csak a fürthozzáféréshez használni kívánt gépekkapcsolatait.

A fürt létrehozásakor eldöntheti, hogy létrehoz-e nyilvános IP-címet a fürtvezérlőn.

* Ha új **virtuális hálózatot** vagy **új alhálózatot**hoz létre, a fürtvezérlő **nyilvános** IP-címet kap.
* Ha meglévő virtuális hálózatot és alhálózatot választ ki, a fürtvezérlő csak **magánhálózati** IP-címekkel fog rendelkezni.

## <a name="vm-access-roles"></a>Virtuálisgép-hozzáférési szerepkörök

Az Azure [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/index.yml) (RBAC) használatával engedélyezi a fürt virtuális gépeit bizonyos feladatok végrehajtására. A fürtvezérlőnek például engedélyre van szüksége a fürtcsomópont virtuális gépei létrehozásához és konfigurálásához. A fürtcsomópontoknak képesnek kell lenniük IP-címek hozzárendelésére vagy más fürtcsomópontokhoz való hozzárendelésére.

Az Avere vFXT virtuális gépekhez két beépített Azure-szerepkör használatos:

* A fürtvezérlő az [Avere Contributor](../role-based-access-control/built-in-roles.md#avere-contributor)beépített szerepkört használja.
* A fürtcsomópontok az [Avere Operator](../role-based-access-control/built-in-roles.md#avere-operator)beépített szerepkört használják.

Ha testre kell szabnia az Avere vFXT-összetevők hozzáférési szerepköreit, meg kell határoznia a saját szerepkörét, majd hozzá kell rendelnie a virtuális gépekhez a létrehozásukidőpontjában. A központi telepítési sablon nem használható az Azure Marketplace-en. Forduljon a Microsoft ügyfélszolgálatához és ügyfélszolgálatához, ha megnyit egy jegyet az Azure Portalon a Segítség a [rendszerrel kapcsolatban](avere-vfxt-open-ticket.md)című témakörben leírtak szerint.

## <a name="next-steps"></a>További lépések

[A központi telepítés áttekintése](avere-vfxt-deploy-overview.md) az Avere vFXT Azure-rendszerhez való létrehozásához és az adatok kiszolgálására való felkészüléshez szükséges lépések teljes képet ad.
