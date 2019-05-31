---
title: Az SAP HANA az Azure-ban (nagyméretű példányok) architektúrája |} A Microsoft Docs
description: Az Azure-ban (nagyméretű példányok) SAP HANA üzembe helyezése architektúráját.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d077487f85c789bcdfea3d91e29ee0d44ce82de0
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239443"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>Azure-beli SAP HANA (nagyméretű példányok) architektúra

Magas szintű az SAP HANA az Azure-ban (nagyméretű példányok) megoldás rendelkezik az SAP alkalmazásrétegre szereplő virtuális gépeket. Az adatbázis réteg található egy nagyméretű szolgáltatáspéldányban ugyanazon Azure-régióban, amely csatlakozik az Azure IaaS SAP TDI-konfigurált hardveren található.

> [!NOTE]
> Helyezze üzembe az SAP alkalmazásrétegre ugyanabban a régióban az Azure az SAP DBMS-összetevővel. Ez a szabály megfelelően legyen dokumentálva az SAP számítási feladatok Azure-on közzétett információt. 

SAP Hana az Azure-ban (nagyméretű példányok) az általános architektúrát biztosít egy SAP TDI-tanúsítvánnyal rendelkező hardverkonfiguráció, amely egy nem virtualizált, operációs rendszer nélküli számítógépen, a nagy teljesítményű kiszolgáló, az SAP HANA-adatbázis. A lehetőség és az erőforrások igény szerint, a SAP alkalmazás réteg skálázása az Azure rugalmasságot is biztosít.

![SAP Hana az Azure-ban (nagyméretű példányok) az architektúra áttekintése](./media/hana-overview-architecture/image1-architecture.png)

Az architektúra látható három részből áll:

- **jobb oldali**: A LOB-alkalmazások, például SAP mutat be egy helyszíni infrastruktúra, amely futtatja a különböző alkalmazások az adatokat, hogy a végfelhasználók számára elérhető erőforrások. Ideális esetben ez a helyszíni infrastruktúra csatlakoztatva van az Azure-bA [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Center**: Azure IaaS megjeleníti, és ebben az esetben használja a virtuális gépek SAP vagy más SAP HANA DBMS rendszerként használó alkalmazások üzemeltetésére. Virtuális gépek kisebb HANA-példányok függvény, amely a virtuális gépek adja meg a memória és az alkalmazási rétegre együtt helyezik üzembe. További információ a virtuális gépek: [virtuális gépek](https://azure.microsoft.com/services/virtual-machines/).

   Az Azure hálózati szolgáltatások használhatók együtt más alkalmazások virtuális hálózatokra SAP-rendszereinket. Ezek a virtuális hálózatok csatlakozhat a helyszíni rendszerek, valamint hogy az SAP HANA az Azure-ban (nagyméretű példányok).

   SAP NetWeaver-alkalmazásaihoz és az Azure-ban támogatott adatbázisok: [SAP támogatási Megjegyzés #1928533 – SAP alkalmazások az Azure-ban: Támogatott termékek és Azure-beli Virtuálisgép-típusok](https://launchpad.support.sap.com/#/notes/1928533). Dokumentáció az Azure-beli SAP-megoldások üzembe helyezése lásd:

  -  [SAP használata Windows virtuális gépeken](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Azure virtuális gépeken futó SAP-megoldások használata](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **Balra**: Az SAP HANA TDI-tanúsítvánnyal rendelkező hardverek jeleníti meg az Azure-beli nagyméretű példány stamp. A nagyméretű HANA-példány egységek csatlakozik a virtuális hálózatok az Azure-előfizetés ugyanazt a technológiát használják, mint a kapcsolat a helyszínről az Azure-bA. 2019. május, kezdődően az optimalizálás van bevezetni, amely lehetővé teszi, hogy a nagyméretű HANA-példány egységek és az ExpressRoute-átjárót bevonása nélkül az Azure virtuális gépek közötti kommunikációhoz. Az ExpressRoute gyors útvonal elnevezésű optimalizálás ebben az architektúrában (piros vonalak) jelenik meg. 

Az Azure-beli nagyméretű példány stamp magát a következő összetevőket kombinálja:

- **Számítástechnika**: Kiszolgálók, különböző generációja, amely a szükséges számítási képességeket biztosítanak, és az SAP HANA certified Intel Xeon processzorok alapulnak.
- **Hálózati**: Egy egységes nagy sebességű hálózati háló kapcsolja össze a számítási, tárolási és hálózati Ébresztés összetevő.
- **Tárolási**: A tárolási infrastruktúra, amely egy egységes hálózati háló keresztül érhető el. Az adott tárolási kapacitás biztosított attól függ, hogy az adott SAP HANA, az üzembe helyezett Azure-ban (nagyméretű példányok) konfigurációja. További tárolási kapacitás további havi költségek érhető el.

A nagyméretű szolgáltatáspéldányban több-bérlős infrastruktúrájában ügyfelek elkülönített bérlők üzemelnek. : Üzembe helyezés a bérlő nevezze el az Azure-előfizetés belül az Azure-regisztrációjában. Az Azure-előfizetés akkor azt, amelyik a nagyméretű HANA-példányt kell kiegyenlíteni. Ezek a bérlők számára az Azure-előfizetéshez 1:1 kapcsolat áll fenn. A hálózat egy nagyméretű HANA-példány egység több bérlőhöz tartozó Azure-előfizetések különböző virtuális hálózatokból egy Azure-régióban üzembe helyezett eléréséhez. Ezeket az Azure-előfizetések az azonos Azure-regisztrációjához kell tartoznia. 

Virtuális gépek, az SAP HANA az Azure-ban (nagyméretű példányok) több Azure-régióban érhető el. A vész helyreállítási funkciókat kínálnak, kiválaszthatja, elvégezni a beléptetést. Egy földrajzi politikai régión belül különböző nagyméretű példány blokkokban kapcsolódnak egymáshoz. Például a HANA nagyméretű példány Bélyegekre az USA nyugati régiója és USA keleti régiójában vész-helyreállítási replikáció dedikált hálózati kapcsolaton keresztül csatlakoznak. 

Ugyanúgy, mint a virtuális gépek különböző típusairól az Azure Virtual Machines közül választhat, választhat a különböző SKU-k a nagyméretű HANA-példányt, amely a különböző számítási feladatok esetében az SAP Hana vannak igazítva. Az SAP az Intel processzorral generációk alapján különböző számítási feladatokhoz a memória, processzor szoftvercsatorna arányok vonatkozik. Az alábbi táblázat a Termékváltozat típusa érhető el.

A rendelkezésre álló termékváltozatok annak [HLI rendelkezésre álló termékváltozatok](hana-available-skus.md).

**Következő lépések**
- Tekintse meg [SAP HANA (nagyméretű példányok) hálózati architektúra](hana-network-architecture.md)