---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 8106b1a77f11874b1b13f41bfc9e62ea800afc98
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361530"
---
### <a name="database-tier"></a>Adatbázis-szint

Az adatbázis szintje tartalmazza az alkalmazás adatbázis-példányait. Az adatbázis lehet Oracle DB, Oracle RAC vagy Oracle Exadata Database rendszer. 

Ha a választás a Oracle DB használata, az adatbázis-példány az Azure piactéren elérhető Oracle DB rendszerképeken keresztül is üzembe helyezhető az Azure-ban. Azt is megteheti, hogy az összekötőt az Azure és a OCI között használja, hogy a Oracle DBt a OCI-on található egy Pásti modellben telepítse.

Az Oracle RAC esetében az Oracle RAC üzembe helyezése az Azure CloudSimple-ben az IaaS-modellben vagy a OCI-ben a Pásti modellben. Ajánlott két csomópontos RAC-rendszer használata. 

Végül a Exadata rendszerek esetében használja a OCI interconnectt, és telepítse a Exadata rendszert a OCI-ben. A fenti architektúra-diagramon egy, a OCI-ben üzembe helyezett Exadata-rendszer látható két alhálózaton belül.

Éles forgatókönyvek esetén az adatbázis több példányát is üzembe helyezheti két rendelkezésre állási zónában (ha az Azure-ban üzembe helyezhető), vagy két rendelkezésre állási tartomány (a OCI-ben). Az Oracle Active Database Guard használatával szinkronizálhatja az elsődleges és a készenléti adatbázist.

Az adatbázis-rétegek csak a középső szintű kérelmeket kapják meg. Javasoljuk, hogy hozzon létre egy hálózati biztonsági csoportot (biztonsági lista, ha az adatbázist a OCI-ben telepíti), hogy csak a középső szinten és a 22-es porton engedélyezze a kérelmeket rendszergazdai okokból a megerősített kiszolgálóról a 1521-as porton.

A OCI-ben üzembe helyezett adatbázisok esetében egy különálló virtuális felhőalapú hálózatot kell beállítani egy olyan dinamikus útválasztási átjáróval (DRG), amely a FastConnect-áramkörhöz csatlakozik.