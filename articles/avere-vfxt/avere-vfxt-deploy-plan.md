---
title: A Avere vFXT rendszer – Azure megtervezése
description: Ismerteti a tervezése előtt Avere vFXT telepítése az Azure-hoz
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 3212befac60e3677c0b556825560cc548df42969
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "56990985"
---
# <a name="plan-your-avere-vfxt-system"></a>Az Avere vFXT rendszer megtervezése

Ez a cikk az Azure-fürtön, amely megfelelő a méretezése az igényeinek, és elhelyezni egy új Avere vFXT tervezését ismerteti. 

Ugrás az Azure piactéren, illetve olyan virtuális gépek létrehozása előtt fontolja meg, hogyan működjön együtt az Azure-ban más elemek a fürtben. Tervezze meg, ahol fürterőforrások a magánhálózat és alhálózatokat kell elhelyezni, és döntse el, ahol a háttérbeli storage lesz. Ügyeljen arra, hogy a fürtcsomópontokon, hozzon létre megfelelő teljesítmény, a munkafolyamat támogatásához. 

Látogasson el lapunkra további információért.

## <a name="resource-group-and-network-infrastructure"></a>Erőforrás-csoport és a hálózati infrastruktúra

Fontolja meg, ahol az Azure-telepítéshez Avere vFXT elemeinek lesz. Az alábbi ábrán egy lehetséges elrendezés az Azure-összetevők a Avere vFXT:

![Az ábrán látható a fürt vezérlő és a fürt virtuális gépek egy alhálózaton belül. Az alhálózat körül határ egy virtuális hálózati határ. A virtuális hálózaton belül van egy hatszög jelölő a storage-szolgáltatásvégpontot; csatlakoztatva van egy szaggatott nyíl egy Blob Storage a virtuális hálózaton kívülről.](media/avere-vfxt-components-option.png)

A Avere vFXT rendszer hálózati infrastruktúra tervezése során kövesse az alábbi irányelveket:

* Összes elem a Avere vFXT üzembe helyezéshez létrehozott új előfizetéssel kell kezelni. Az előnyök: 
  * Egyszerűbb költség követési - nézet és a naplózási összes költséget, az erőforrásokat, az infrastruktúra és a számítási ciklusok egy adott előfizetéshez.
  * Könnyebb karbantartás – távolíthatja el a teljes előfizetés, amikor befejeződött a projekthez.
  * Kényelmes particionálását az erőforrás - kvóták elleni lehetséges erőforrás-szabályozási a Avere vFXT ügyfelek és a egy előfizetés fürt elkülönítésével más kritikus fontosságú számítási feladatokhoz. Ezzel elkerülhető ütközés fel egy nagy mennyiségű ügyfelet egy nagy teljesítményű számítástechnikai munkafolyamat állításakor.

* Keresse meg a számítási ügyfélrendszer megközelíti a vFXT fürt. Háttér-tároló több távoli lehet.  

* A vFXT fürt és a fürt vezérlő virtuális Gépet kell elhelyezkedniük ugyanazon a virtuális hálózaton (vnet), az ugyanabban az erőforráscsoportban, és használja ugyanazt a tárfiókot. A fürt automatikus létrehozása sablon kezeli, ez a legtöbb esetben.

* A fürt IP-címütközés elkerülése az ügyfelekkel, vagy a számítási erőforrásokat a saját alhálózatán található kell működnie. 

* A fürt létrehozási sablont hozhat létre a fürtöt, erőforráscsoportok, virtuális hálózatok, alhálózatok és storage-fiókok esetében a szükséges infrastruktúra-erőforrások legnagyobb része. Ha azt szeretné, használja a már meglévő erőforrásait, ellenőrizze azok megfelelnek a táblázatban. 

  | Erőforrás | Meglévő használata? | Követelmények |
  |----------|-----------|----------|
  | Erőforráscsoport | Igen, ha üres | Üresnek kell lennie| 
  | Tárfiók | Igen, ha a fürt létrehozása után összekapcsolása egy meglévő Blob-tároló <br/>  Nem, ha a fürt létrehozásakor egy új Blob-tároló létrehozása | Meglévő Blob-tároló üresnek kell lennie. <br/> &nbsp; |
  | Virtuális hálózat | Igen | A storage-szolgáltatásvégpont tartalmazniuk kell, ha egy új Azure Blob-tároló létrehozása | 
  | Alhálózat | Igen |   |

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

## <a name="vfxt-node-size"></a>vFXT csomópont mérete

A virtuális gépek, amelyek szolgálhat a fürtcsomópontok határozza meg a kérelem adatátviteli és tárolási kapacitás a gyorsítótár. <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

Minden egyes vFXT csomópont azonos lesz. Azt jelenti Ha létrehoz egy három csomópontos fürtben három virtuális gépet azonos típusú és méretű kap. 

| Példány típusa | Virtuális magok | Memory (Memória)  | Helyi SSD-tárolóval  | Adatlemezek max. száma | Nem gyorsítótárazott lemezteljesítmény | Hálózati adapter (darabszám) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51,200 IOPS <br/> 768 MBps | 16000 MB/s (8)  |

Csomópontonként lemezgyorsítótár konfigurálható, 1000 GB-tól a 8000-es GB rage is. 4 TB-os csomópontonként javasolt gyorsítótár mérete Standard_E32s_v3 csomópontok.

Ezek a virtuális gépek kapcsolatos további információkért olvassa el a Microsoft Azure-dokumentáció:

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

Nyilvános IP-címmel rendelkező tartományvezérlő a biztonság növelése érdekében a telepítési parancsfájl automatikusan létrehoz egy hálózati biztonsági csoportot, amely korlátozza a 22-es porton bejövő hozzáférést. További védelmet biztosíthat a rendszer zárolja a forrás az IP-címek – való hozzáférés, kapcsolatok engedélyezése csak a fürt hozzáféréshez használni kívánt gépeket.

A fürt létrehozásakor kiválaszthatja-e egy nyilvános IP-cím létrehozása a fürt vezérlőn. 

* Egy új virtuális hálózat vagy egy új alhálózatot hoz létre, ha a fürt-vezérlő kap egy nyilvános IP-címet.
* Ha kiválaszt egy meglévő vnetet és alhálózatot, a fürt vezérlő csak magánhálózati IP-címeket kell. 

## <a name="next-step-understand-the-deployment-process"></a>Következő lépés: Az üzembe helyezési folyamat ismertetése

[Üzembe helyezés – áttekintés](avere-vfxt-deploy-overview.md) kínál átfogó, hozzon létre egy Azure rendszer Avere vFXT és felkészülés Előkészíthetők az adatok szükséges lépéseket.  