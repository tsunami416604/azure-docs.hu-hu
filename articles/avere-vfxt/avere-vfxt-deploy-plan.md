---
title: A Avere vFXT rendszer – Azure megtervezése
description: Ismerteti a tervezése előtt Avere vFXT telepítése az Azure-hoz
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: c0304e290d18e6569e3fcc2efbab8af15727b80c
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55508157"
---
# <a name="plan-your-avere-vfxt-system"></a>Az Avere vFXT rendszer megtervezése

Ez a cikk az Azure-fürttel, ellenőrizze, hogy a fürt létrehozása elhelyezni, és az igényeinek megfelelő méretű egy új Avere vFXT tervezését ismerteti. 

Ugrás az Azure piactéren, illetve olyan virtuális gépek létrehozása előtt fontolja meg, hogyan működjön együtt az Azure-ban más elemek a fürtben. Tervezze meg, ahol fürterőforrások a magánhálózat és alhálózatokat kell elhelyezni, és döntse el, ahol a háttérbeli storage lesz. Ügyeljen arra, hogy a fürtcsomópontokon, hozzon létre megfelelő teljesítmény, a munkafolyamat támogatásához. 

Látogasson el lapunkra további információért.

## <a name="resource-group-and-network-infrastructure"></a>Erőforrás-csoport és a hálózati infrastruktúra

Fontolja meg, ahol az Azure-telepítéshez Avere vFXT elemeinek lesz. Az alábbi ábrán egy lehetséges elrendezés az Azure-összetevők a Avere vFXT:

![Az ábrán látható a fürt vezérlő és a fürt virtuális gépek egy alhálózaton belül. Az alhálózat körül határ egy virtuális hálózati határ. A virtuális hálózaton belül van egy hatszög jelölő a storage-szolgáltatásvégpontot; csatlakoztatva van egy szaggatott nyíl egy Blob Storage a virtuális hálózaton kívülről.](media/avere-vfxt-components-option.png)

A Avere vFXT rendszer hálózati infrastruktúra tervezése során kövesse az alábbi irányelveket:

* Összes elem a Avere vFXT üzembe helyezéshez létrehozott új előfizetéssel kell kezelni. Az előnyök: 
  * Egyszerűbb költség követési - nézet és a naplózási összes költséget, az erőforrásokat, az infrastruktúra és a számítási ciklusok egy adott előfizetéshez.
  * Könnyebb karbantartás – távolíthatja el a teljes előfizetés, amikor befejeződött a projekthez.
  * Erőforrás kényelmes particionálás kvóták – más kritikus fontosságú számítási feladatok megvédheti a lehetséges erőforrás-szabályozás állításakor a nagy mennyiségű ügyfelet a Avere vFXT ügyfelek elkülönítésével a nagy teljesítményű számítástechnikai munkafolyamathoz használt fel, és a fürt egy egyetlen előfizetéshez.

* Keresse meg a számítási ügyfélrendszer megközelíti a vFXT fürt. Háttér-tároló több távoli lehet.  

* Az egyszerűség kedvéért keresse meg a vFXT fürt és a fürt vezérlő virtuális Géphez, ugyanabban az erőforráscsoportban és ugyanazon a virtuális hálózaton (vnet). Ezek a kell is használhatja ugyanazt a tárfiókot. (A fürt vezérlő létrehozza a fürtöt, és a is használható parancssori fürtkezelés.)  

* A fürt IP-címütközés elkerülése az ügyfelekkel, vagy a számítási erőforrásokat a saját alhálózatán található kell működnie. 

## <a name="ip-address-requirements"></a>IP-követelményei 

Győződjön meg arról, hogy a fürt alhálózat egy elég nagy IP-címtartomány a fürt támogatásához. 

A Avere vFXT fürt használja a következő IP-címek:

* Egy fürt felügyeleti IP-címét. Ezt a címet áthelyezheti a csomópontok a fürtben, azonban mindig érhető el, hogy a Vezérlőpult Avere konfigurálása eszköz csatlakozhat.
* A fürt minden csomópontján:
  * Legalább egy ügyfél által használt IP-cím. (A fürt által kezelt összes ügyfél irányultságú címet *vserver*, amely áthelyezheti őket csomópontok közötti igény szerint.)
  * A fürtkommunikációhoz egy IP-cím
  * Egy példány IP-címe (a virtuális géphez hozzárendelt)

Ha Azure Blob storage szolgáltatást használja, azt is szükség lehet a fürt virtuális IP-címek:  

* Egy Azure Blob storage-fiók legalább öt IP-címet igényel. Ha a Blob storage keresse meg a fürt ugyanazon a vneten vegye figyelembe ennek a követelménynek.
* Ha használja az Azure Blob storage, amely kívül esik a virtuális hálózat a fürt számára, létre kell hoznia egy tárolási végpontot a virtuális hálózaton belül. Ez a végpont nem IP-címet használ.

Lehetősége van, keresse meg a hálózati erőforrásokhoz, és a Blob storage (ha van) eltérő erőforráscsoportokban a fürtből.

## <a name="vfxt-node-sizes"></a>vFXT csomópontméretűek 

A virtuális gépek, amelyek szolgálhat a fürtcsomópontok határozza meg a kérelem adatátviteli és tárolási kapacitás a gyorsítótár. A példányok típusai két, különböző memória, a processzor és a helyi tároló jellemzőit közül választhat. 

Minden egyes vFXT csomópont azonos lesz. Azt jelenti Ha létrehoz egy három csomópontos fürtben három virtuális gépet azonos típusú és méretű kap. 

| Példány típusa | Virtuális magok | Memory (Memória)  | Helyi SSD-tárolóval  | Adatlemezek max. száma | Nem gyorsítótárazott lemezteljesítmény | Hálózati adapter (darabszám) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D16s_v3 | 16  | 64 GiB  | 128 GiB  | 32 | 25,600 IOPS <br/> 384 MBps | 8000 MB/s (8) |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51,200 IOPS <br/> 768 MBps | 16000 MB/s (8)  |

Csomópontonként lemezgyorsítótár konfigurálható, 1000 GB-tól a 8000-es GB rage is. 1 TB-os csomópontonként Standard_D16s_v3 csomópontok javasolt gyorsítótár méretét, és 4 TB-os csomópontonként Standard_E32s_v3 csomópont használata ajánlott.

Ezek a virtuális gépek kapcsolatos további információkért olvassa el a következő Microsoft Azure-dokumentumokat:

* [Általános célú virtuális gépek méretei](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general)
* [Memóriahasználatra optimalizált virtuális gépek méretei](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>Fiókkvóta

Győződjön meg arról, hogy előfizetése rendelkezik-e futtatni a Avere vFXT fürt, valamint azok a számítástechnikai vagy ügyfél rendszerek használja a kapacitást. Olvasási [a vFXT fürt kvóta](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) részleteiről.

## <a name="back-end-data-storage"></a>Háttér-adattárolás

Ahol a Avere vFXT fürt tárolja az adatokat Ha az nem a gyorsítótárban? Döntse el, hogy a munkakészletének lesz hosszú távon tárolt, egy új Blob-tárolóba vagy egy meglévő felhő vagy hardver tárolórendszer. 

Ha szeretné az Azure Blob storage használata a háttér, létre kell hoznia egy új tárolót a vFXT fürt létrehozásának részeként. Ez a beállítás hoz létre, és konfigurálja az új tárolót, hogy, amint a fürt készen áll használatra kész legyen. 

Olvasási [a Avere vFXT létrehozni az Azure](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure) részleteiről.

> [!NOTE]
> Csak üres Blob storage-tárolók Avere vFXT rendszer core kiemelik is használható. A vFXT az objektumtároló kezelheti anélkül, hogy a meglévő adatok megőrzése érdekében képesnek kell lennie. 
>
> Olvasási [adatok áthelyezése a vFXT fürthöz](avere-vfxt-data-ingest.md) megtudhatja, hogyan másolhat adatokat a fürtöt az új tároló hatékonyan az ügyfélgépek és a Avere vFXT gyorsítótár használatával.

Ha meglévő helyszíni tárolási rendszert használja, hozzá kell adnia azt a vFXT fürt létrehozása után. Olvasási [konfigurálta a tárterületet](avere-vfxt-add-storage.md) hozzáadása egy meglévő tárolórendszer a Avere vFXT fürt kapcsolatos részletes útmutatásért.

## <a name="cluster-access"></a>Fürt hozzáférés 

Az Azure-fürtön a Avere vFXT található egy privát alhálózatra, és a fürtnek nincs nyilvános IP-címet. Rendelkeznie kell valamilyen módszerrel férnek hozzá a felügyeleti fürt és a kapcsolatok a privát alhálózatra. 

Adathozzáférési beállítások a következők:

* Jump host – nyilvános IP-cím hozzárendelése a magánhálózaton belül különálló virtuális Géphez, és ezzel hozzon létre egy SSL-bújtatást a fürt csomópontjaihoz. 

  > [!TIP]
  > Ha a fürt vezérlő állít be egy nyilvános IP-címet, használhatja a jump gazdagépként. Olvasási [fürt vezérlő, jump host](#cluster-controller-as-jump-host) további információt.

* Virtuális magánhálózati (VPN) – a magánhálózaton, pont – hely és a site-to-site VPN konfigurálása.

* Az Azure ExpressRoute - konfigurálása egy privát kapcsolaton keresztül egy ExpressRoute-partner. 

Ezek a beállítások kapcsolatos tudnivalókért olvassa el a [Azure Virtual Network dokumentációjában az internetes kommunikáció](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet).

### <a name="cluster-controller-as-jump-host"></a>Ahogy a jump host fürt vezérlő

Ha a fürt vezérlő állít be egy nyilvános IP-címet, használhatja azt jump-gazdagépként a Avere vFXT fürtöt a saját alhálózatán kívülről kapcsolódni. Azonban a vezérlő fürtcsomópontok módosíthatja a hozzáférési jogosultságokkal rendelkezik, mert ez létrehoz egy kisebb biztonsági kockázatot jelent.  

A nyilvános IP-címmel megnövelt biztonság érdekében a hálózati biztonsági csoport használatával bejövő hozzáférés engedélyezése csak a 22-es porton keresztül. Szükség esetén további védelmet biztosíthat a rendszer zárolja a forrás az IP-címek – való hozzáférés, kapcsolatok engedélyezése csak a fürt hozzáféréshez használni kívánt gépeket.

A fürt létrehozásakor kiválaszthatja-e egy nyilvános IP-cím létrehozása a fürt vezérlőn. 

* Egy új virtuális hálózat vagy egy új alhálózatot hoz létre, ha a fürt-vezérlő kap egy nyilvános IP-címet.
* Ha kiválaszt egy meglévő vnetet és alhálózatot, a fürt vezérlő csak magánhálózati IP-címeket kell. 

## <a name="next-step-understand-the-deployment-process"></a>Következő lépés: Az üzembe helyezési folyamat ismertetése

[Üzembe helyezés – áttekintés](avere-vfxt-deploy-overview.md) kínál átfogó, hozzon létre egy Azure rendszer Avere vFXT és felkészülés Előkészíthetők az adatok szükséges lépéseket.  