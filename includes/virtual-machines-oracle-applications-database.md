---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 8106b1a77f11874b1b13f41bfc9e62ea800afc98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361530"
---
### <a name="database-tier"></a>Adatbázisszint

Az adatbázisréteg az alkalmazás adatbázispéldányait tartalmazza. Az adatbázis lehet Oracle DB, Oracle RAC vagy Oracle Exadata Database rendszer. 

Ha az Oracle DB használata, az adatbázispéldány telepíthető az Azure-ban az Azure Piactéren elérhető Oracle DB lemezképeken keresztül. Azt is megteheti, hogy az Azure és az OCI közötti kapcsolatot használja az Oracle DB üzembe helyezéséhez egy PaaS-modellben oci-n.

Az Oracle RAC esetében az Oracle RAC-t az Azure CloudSimple-en telepítheti IaaS-modellben vagy OCI-ben A PaaS-modellben. Javasoljuk, hogy kétcsomópontos RAC rendszert használjon. 

Végül az Exadata-rendszerek esetében használja az OCI-összeköttetést, és telepítse az Exadata rendszert az OCI-ben. Az előző architektúradiagram egy OCI-ben üzembe helyezett Exadata-rendszert jelenít meg két alhálózaton keresztül.

Éles környezetben az adatbázis több példányát helyezheti üzembe két rendelkezésre állási zónában (ha az Azure-ban telepíti) vagy két rendelkezésre állási tartományban (OCI-ben). Az elsődleges és készenléti adatbázisok szinkronizálásához használja az Oracle Active Data Guard segítségével.

Az adatbázisszint csak a középső rétegtől érkező kéréseket fogad. Javasoljuk, hogy állítson be egy hálózati biztonsági csoportot (biztonsági lista, ha az adatbázist oci-ben telepíti), hogy csak a középső rétegről és a 22-es portról a megerősített kiszolgálóról érkező 1521-es porton engedélyezze a kérelmeket.

Az OCI-ben telepített adatbázisok esetében külön virtuális felhőhálózatot kell létrehozni egy dinamikus útválasztási átjáróval (DRG), amely a FastConnect-áramkörhöz csatlakozik.