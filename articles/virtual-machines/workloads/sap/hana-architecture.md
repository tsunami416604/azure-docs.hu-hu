---
title: Az SAP HANA architektúrája az Azure-on (nagy példányok) | Microsoft dokumentumok
description: SAP HANA üzembe helyezése az Azure-ban (nagy példányok) architektúrájának architektúrája.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 041da4198b0bdd040a4916008a1135aa2e2a5f7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614522"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>SAP HANA (nagy példányok) architektúrája az Azure-ban

Magas szinten az SAP HANA az Azure -on (nagy példányok) megoldás rendelkezik az SAP-alkalmazásréteg virtuális gépekben található. Az adatbázisréteg az SAP TDI által konfigurált hardveren található, amely ugyanabban az Azure-régióban található, amely az Azure IaaS-hez csatlakozik.

> [!NOTE]
> Telepítse az SAP-alkalmazásréteget ugyanabban az Azure-régióban, mint az SAP DBMS-réteg. Ez a szabály jól dokumentált közzétett információk az Sap-számítási feladatok az Azure-ban. 

Az SAP HANA általános architektúrája az Azure-ban (nagy példányok) egy SAP TDI-tanúsítvánnyal rendelkező hardverkonfigurációt biztosít, amely egy nem virtualizált, csupasz fém, nagy teljesítményű kiszolgáló az SAP HANA-adatbázishoz. Emellett az Azure-ban lehetővé és rugalmasan skálázhatja az SAP alkalmazásréteg erőforrásait az igényeinek megfelelően.

![Az SAP HANA architekturális áttekintése az Azure-ban (nagy példányok)](./media/hana-overview-architecture/image1-architecture.png)

A bemutatott architektúra három részből áll:

- **Jobbra:** Egy helyszíni infrastruktúrát jelenít meg, amely különböző alkalmazásokat futtat az adatközpontokban, hogy a végfelhasználók hozzáférhessenek a LOB-alkalmazásokhoz, például az SAP-hoz. Ideális esetben ez a helyszíni infrastruktúra az [ExpressRoute](https://azure.microsoft.com/services/expressroute/)segítségével csatlakozik az Azure-hoz.

- **Központ:** Az Azure IaaS megjelenítése, és ebben az esetben a virtuális gépek használata sap vagy más alkalmazások üzemeltetéséhez, amelyek az SAP HANA-t DBMS-rendszerként használják. Kisebb HANA-példányok, amelyek a virtuális gépek által biztosított memóriával függvényt, az alkalmazásréteggel együtt vannak telepítve a virtuális gépeken. A virtuális gépekről további információt a Virtuális gépek című [témakörben talál.](https://azure.microsoft.com/services/virtual-machines/)

   Az Azure hálózati szolgáltatásai az SAP-rendszerek más alkalmazásokkal való csoportosítására szolgálnak virtuális hálózatokba. Ezek a virtuális hálózatok a helyszíni rendszerekhez, valamint az Azure-beli SAP HANA-hoz (nagy példányok) csatlakoznak.

   Az Azure-ban futtatható SAP NetWeaver-alkalmazások és adatbázisok esetében olvassa el az [SAP-támogatási megjegyzés #1928533 – SAP-alkalmazások az Azure-ban: Támogatott termékek és Az Azure virtuálisgép-típusok](https://launchpad.support.sap.com/#/notes/1928533)című témakört. Az SAP-megoldások Azure-beli üzembe helyezéséről a következő témakörben olvashat dokumentációt:

  -  [Az SAP használata Windows rendszerű virtuális gépeken](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [SAP-megoldások használata Azure-beli virtuális gépeken](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **Balra**: Az SAP HANA TDI-tanúsítvánnyal rendelkező hardver megjelenítése az Azure nagy példány bélyegzőjében. A HANA nagypéldány-egységek az Azure-előfizetés virtuális hálózataihoz kapcsolódnak, ugyanazt a technológiát használva, mint a helyszíni és az Azure közötti kapcsolat. 2019 májusátantól egy optimalizálás tanévben vezetett be egy optimalizálást, amely lehetővé teszi a HANA nagypéldány-egységek és az Azure-beli virtuális gépek közötti kommunikációt az ExpressRoute-átjáró bevonása nélkül. Ez az ExpressRoute gyorselérési út nevű optimalizálás ebben az architektúrában (piros vonalak) jelenik meg. 

Maga az Azure Large Instance bélyegző je a következő összetevőket egyesíti:

- **Számítástechnika:** Olyan kiszolgálók, amelyek az Intel Xeon processzorok különböző generációján alapulnak, amelyek biztosítják a szükséges számítási képességet, és SAP HANA tanúsítvánnyal rendelkeznek.
- **Hálózat**: Egységes, nagy sebességű hálózati háló, amely összeköti a számítástechnikai, tárolási és LAN-összetevőket.
- **Tárolás**: Egységes hálózati hálón keresztül elérhető tárolási infrastruktúra. A megadott tárolási kapacitás az Azure (Nagy példányok) konfigurációján üzembe helyezett adott SAP HANA-tól függ. Több tárolókapacitás áll rendelkezésre felár ellenében.

A nagy példány bélyegző több-bérlős infrastruktúráján belül az ügyfelek elszigetelt bérlőkként vannak üzembe helyezve. A bérlő üzembe helyezésekor elnevezhet egy Azure-előfizetést az Azure-regisztráción belül. Ez az Azure-előfizetés az, amely a HANA nagy példány számlázása. Ezek a bérlők 1:1 kapcsolattal rendelkeznek az Azure-előfizetéssel. Egy hálózat esetében egy hana nagy példány egység egy Azure-régió egyik bérlőjében üzembe helyezhető különböző virtuális hálózatok, amelyek különböző Azure-előfizetések. Ezeknek az Azure-előfizetéseknek ugyanahhoz az Azure-regisztrációhoz kell tartozniuk. 

A virtuális gépekhez csakúgy, mint a virtuális gépek, az SAP HANA az Azure-ban (nagy példányok) több Azure-régióban érhető el. A vész-helyreállítási képességek, választhat, hogy engedélyezés. Egy geopolitikai régión belül különböző nagypéldány-bélyegzők kapcsolódnak egymáshoz. Például hana nagy példány bélyegzők az USA nyugati és az USA keleti csatlakozik egy dedikált hálózati kapcsolat on vész-helyreállítási replikáció. 

Csakúgy, mint az Azure virtuális gépek különböző virtuális géptípusok közül választhat, a HANA nagy példány különböző SAP HANA-típusokhoz szabott termékkészletek közül választhat. Az SAP memória-processzor-szoftvercsatorna-arányokat alkalmaz az Intel processzorgenerációkon alapuló különböző munkaterhelések esetén. Az alábbi táblázat a felajánlott termékváltozat-típusokat mutatja be.

A [HLI-hez elérhető, rendelkezésre álló ska-k](hana-available-skus.md)at talál.

**További lépések**
- SAP [HANA (nagy példányok) hálózati architektúrájának hivatkozása](hana-network-architecture.md)