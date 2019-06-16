---
title: Az Azure Cosmos DB biztonsági attribútumok
description: Azure Cosmos DB értékelésére biztonsági attribútumok ellenőrzőlista
services: cosmos-db
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: ffeb60f5476a540e3da46a82c240b0dda9aa6be2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66480463"
---
# <a name="security-attributes-for-azure-cosmos-db"></a>Az Azure Cosmos DB biztonsági attribútumok

Ez a cikk a gyakori biztonsági attribútumok az Azure Cosmos DB-be épített dokumentumok.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például a kiszolgálóoldali titkosítás, a kiszolgálóoldali titkosítást az ügyfél által felügyelt kulcsokat és más titkosítási szolgáltatások) | Igen | Cosmos DB-adatbázisok és a biztonsági mentések titkosítottak alapértelmezett; Lásd: [adatok titkosítását az Azure Cosmos DB](database-encryption-at-rest.md). Ügyfél által felügyelt kulcsokkal kiszolgálóoldali titkosítás nem támogatott. |
| Titkosítás az átvitel során (például az ExpressRoute-titkosítás, a virtuális hálózatok közötti titkosítás és a VNet – VNet titkosítási)| Igen | Átvitel titkosítása az összes Azure Cosmos DB-adatai. |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| Nem |  |
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| Igen | Csak az a tábla API Premium. Nem minden API támogatja ezt a szolgáltatást. Lásd: [az Azure Cosmos DB bemutatása: TABLE API](table-introduction.md). |
| Titkosított API-hívások| Igen | Minden kapcsolat az Azure Cosmos DB támogatja a HTTPS. Az Azure Cosmos DB támogatja a TLS 1.2 kapcsolatokat is, de ez még nem lép életbe. Ügyfelek alacsonyabb szintre TLS azok End kikapcsolása, ha azok gondoskodhat a csatlakozás a Cosmos DB-hez.  |

## <a name="network-segmentation"></a>Hálózati szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatásvégpont-támogatás| Igen |  |
| vNET-injektálási támogatás| Igen | Szolgáltatásvégpont, az Azure Cosmos DB-fiókot, engedélyezze a hozzáférést csak a megadott alhálózat virtuális hálózat (VNet) konfigurálható. Virtuális hálózatok közötti hozzáférés tűzfalszabályokkal is kombinálhatók.  Lásd: [hozzáférés az Azure Cosmos DB a virtuális hálózatok](vnet-service-endpoint.md). |
| Hálózatelkülönítés és Firewalling támogatása| Igen | A tűzfaltámogatás engedélyezi a hozzáférést csak a jóváhagyott IP-címeket, IP-címek egy tartománya és/vagy felhőszolgáltatásokra történő fiókját az Azure Cosmos konfigurálhatja. Lásd: [konfigurálása IP-tűzfal az Azure Cosmos DB](how-to-configure-firewall.md).|
| Kényszerített bújtatás támogatása | Igen | A virtuális hálózathoz, ahol a virtuális gépek találhatók meg az ügyfél oldalán konfigurálhatók.   |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Igen | Az Azure Cosmos DB küldött összes kérelem a rendszer naplózza. [Az Azure figyelési](../azure-monitor/overview.md), az Azure-mérőszámok, naplózás az Azure használata támogatott.  Kérelmek bejelentkezhet adatsík kérelmek, a lekérdezések futásidejének statisztikai adatait, a lekérdezés szövegének megfelelő információkat, a mongodb-hez. Riasztásokat is beállíthat. |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | Igen, az adatbázis fiók szintjén; az adatok adatsík szinten a Cosmos DB erőforrás-jogkivonatokról és hozzáférés a kulcshoz használ. |
| Engedélyezés| Igen | A főkulcsok (elsődleges és másodlagos) az Azure Cosmos-fiók és az erőforrás-jogkivonatokat támogatja. Olvasási/írási beolvasása, vagy csak olvasásra jogosító hozzáférés főkulcsok az adatokhoz. Erőforrás-jogkivonatokat erőforrások, például dokumentumok és a tárolók korlátozott ideig elérését teszi lehetővé. |


## <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő/felügyeleti naplózás és a naplózási tervezése| Igen | Azure-tevékenységnapló fiók szintű műveletek, például a tűzfalak, virtuális hálózatok, a hozzáférési kulcsokat és IAM. |
| Adatok naplózása és naplózási adatsík | Igen | Diagnosztika figyelése, például a naplózás a szolgáltatói tárolóművelet hozza létre a tárolót, üzembe helyezése átviteli sebesség, szabályzatok és a CRUD-műveletek dokumentumok indexelése. |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció kezelésével kapcsolatos támogatás (versioning konfiguráció stb.)| Nem  | | 

## <a name="additional-security-attributes-for-cosmos-db"></a>A Cosmos DB további biztonsági attribútumok

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Eltérő eredetű erőforrások megosztása (CORS) közötti | Igen | Lásd: [eltérő eredetű erőforrások megosztása (CORS) konfigurálása](how-to-configure-cross-origin-resource-sharing.md). |
