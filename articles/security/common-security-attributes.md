---
title: Az Azure-szolgáltatások biztonsági attribútumai
description: Az Azure-szolgáltatások kiértékelésére szolgáló biztonsági attribútumok ellenőrzőlistája
services: security
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: security
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 0010273d41769c57144fdde63e47c528f313a228
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443344"
---
# <a name="security-attributes-for-azure-services"></a>Az Azure-szolgáltatások biztonsági attribútumai

Ez a cikk a kiválasztott Azure-szolgáltatások biztonsági attribútumait gyűjti. A biztonsági attribútumok az Azure-szolgáltatások minősége vagy funkciói. A szolgáltatás hozzájárul a biztonsági rések megelőzésére, észlelésére és reagálására.

A biztonsági attribútumok a következőképpen vannak kategorizálva:
* Megelőző
* Hálózati szegmentálás
* Észlelés
* Az identitás-és hozzáférés-kezelés támogatása
* Naplózási nyomvonal
* Hozzáférés-vezérlés (ha használatban van)
* Konfiguráció kezelése (ha használatban van)

Az egyes kategóriákban a "yes" vagy a "No" érték jelenik meg, amely jelzi, hogy az attribútum használatban van-e. Egyes szolgáltatások esetében "N/A" (nem alkalmazható) attribútumot mutatunk be. Egy Megjegyzés vagy egy hivatkozás is megadható, amely további információkat tartalmaz egy attribútumról.

## <a name="api-managementapi-managementapi-management-security-attributesmd"></a>[API Management](../api-management/api-management-security-attributes.md)

### <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például kiszolgálóoldali titkosítás, ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás és egyéb titkosítási funkciók) | A bizalmas adatokat, például a tanúsítványokat, a kulcsokat és a titkos névvel ellátott értékeket a szolgáltatás által felügyelt, a szolgáltatási példány kulcsainak megfelelően titkosítja a rendszer. |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás) | Igen | Az [expressz útvonal](../expressroute/index.yml) -és VNet-titkosítást az [Azure Networking](../virtual-network/index.yml)szolgáltatja. |
| Titkosítási kulcsok kezelését (CMK, BYOK stb.)| Nem | Az összes titkosítási kulcs egy szolgáltatási példányon, a szolgáltatás pedig felügyelt. |
| Oszlop szintű titkosítás (Azure Data Services)| – | |
| Titkosított API-hívások| Igen | A felügyeleti sík hívások [Azure Resource Manager](../azure-resource-manager/index.yml) TLS protokollon keresztül történnek. Érvényes JSON webes jogkivonat (JWT) szükséges.  Az adatsík hívások a TLS-vel és az egyik támogatott hitelesítési mechanizmussal (például ügyféltanúsítvány vagy JWT) védhetők.
 |

### <a name="network-segmentation"></a>Hálózati szegmentálás

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Nem | |
| VNet-befecskendezés támogatása| Igen | |
| Hálózati elkülönítés és tűzfalak támogatása| Igen | Hálózati biztonsági csoportok (NSG-EK) és Azure Application Gateway (vagy más szoftveres készülék) használata. |
| Kényszerített bújtatás támogatása| Igen | Az Azure Networking kényszerített bújtatást biztosít. |

### <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | |

### <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen | |
| Authorization| Igen | |


### <a name="audit-trail"></a>Naplózási nyomvonal

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | [Azure Monitor tevékenység naplói](../azure-monitor/platform/activity-logs-overview.md) |
| Adatsíkok naplózása és naplózása| Igen | [Azure monitor diagnosztikai naplók](../azure-monitor/platform/diagnostic-logs-overview.md) és (opcionálisan) [Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  |

### <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen | Az [Azure API Management DevOps Resource Kit](https://aka.ms/apimdevops) használata |

### <a name="vulnerability-scans-false-positives"></a>A biztonsági rés hamis pozitív eredményt vizsgál

Ez a szakasz az Azure API Managementt nem érintő általános biztonsági réseket dokumentálja.

| Biztonsági rés               | Leírás                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | A Ticketbleed a TLS SessionTicket-bővítmény néhány F5-termékben található implementációjában biztonsági réseket tartalmaz. Lehetővé teszi, hogy a kiszivárgás ("vérzés") legfeljebb 31 bájt adat legyen inicializálatlan memóriából. Ezt az okozza, hogy a TLS-verem kitöltése egy munkamenet-azonosító, amely az ügyféltől lett átadva, és az adatok alapján 32 bitet használ. |


## <a name="app-serviceapp-serviceapp-service-security-attributesmd"></a>[APP SERVICE](../app-service/app-service-security-attributes.md)

### <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például kiszolgálóoldali titkosítás, ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás és egyéb titkosítási funkciók) | Igen | A webhely fájljának tartalmát az Azure Storage tárolja, amely automatikusan titkosítja a tartalmat a nyugalmi állapotban. Lásd: [Az Azure Storage titkosítása a nyugalmi](../storage/common/storage-service-encryption.md)állapotban lévő adatokhoz.<br><br>Az ügyfél által megadott titkos kódok inaktívak. A titkos kulcsok titkosítása App Service konfigurációs adatbázisokban történik.<br><br>A helyileg csatlakoztatott lemezek opcionálisan használhatók a webhelyek (D:\Local és% TMP%) ideiglenes tárolóként. A helyileg csatlakoztatott lemezek nincsenek titkosítva a nyugalmi állapotban. |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás) | Igen | Az ügyfelek a HTTPS protokollal konfigurálhatják a bejövő forgalmat, és megkövetelhetik a webhelyek használatát. Tekintse meg a blogbejegyzést [, hogyan teheti meg a csak Azure app Service HTTPS-t](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/). |
| Titkosítási kulcsok kezelését (CMK, BYOK stb.)| Igen | Az ügyfelek dönthetnek úgy, hogy az alkalmazás-titkokat a Key Vaultban tárolják, és futásidőben lekérik őket. Lásd: [Key Vault referenciák használata app Service és Azure functions (előzetes verzió)](../app-service/app-service-key-vault-references.md).|
| Oszlop szintű titkosítás (Azure Data Services)| – | |
| Titkosított API-hívások| Igen | App Service konfigurálására irányuló felügyeleti hívások a HTTPS-en keresztüli [Azure Resource Manager](../azure-resource-manager/index.yml) hívásokon keresztül történnek. |

### <a name="network-segmentation"></a>Hálózati szegmentálás

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Igen | Jelenleg előzetes verzióban érhető el a App Service. Lásd: [Azure app Service hozzáférési korlátozások](../app-service/app-service-ip-restrictions.md). |
| VNet-befecskendezés támogatása| Igen | App Service környezetek a App Service privát implementációi, amelyek egyetlen ügyfél számára lettek befecskendezve az ügyfél virtuális hálózatába. Lásd: [Bevezetés a app Service környezetbe](../app-service/environment/intro.md). |
| Hálózati elkülönítés és tűzfalak támogatása| Igen | A App Service nyilvános, több-bérlős változatához az ügyfelek konfigurálhatják a hálózati ACL-eket (IP-korlátozásokat), hogy zárolják az engedélyezett bejövő forgalmat.  Lásd: [Azure app Service hozzáférési korlátozások](../app-service/app-service-ip-restrictions.md).  App Service környezetek közvetlenül a virtuális hálózatokra vannak telepítve, így a NSG-mel is biztonságossá teheti őket. |
| Kényszerített bújtatás támogatása| Igen | App Service környezetek telepíthetők az ügyfél virtuális hálózatára, ahol a kényszerített bújtatás konfigurálva van. Az ügyfeleknek követniük kell a [app Service Environment konfigurálása kényszerített bújtatással](../app-service/environment/forced-tunnel-support.md)című témakör utasításait. |

### <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | A App Service a Application Insights (teljes .NET-keretrendszer, .NET Core, Java és Node. JS) támogató nyelvekhez integrálható Application Insightsokkal.  Lásd: [Azure app Service teljesítményének figyelése](../azure-monitor/app/azure-web-apps.md). A App Service az alkalmazás metrikáit is elküldi a Azure Monitorba. Lásd: [alkalmazások figyelése Azure app Serviceban](../app-service/web-sites-monitor.md). |

### <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen | Az ügyfelek olyan App Service alkalmazásokat hozhatnak létre, amelyek automatikusan integrálva vannak a [Azure Active Directory (Azure ad)](../active-directory/index.yml) és más OAuth kompatibilis identitás-szolgáltatókkal. Lásd: [hitelesítés és engedélyezés Azure app Serviceban](../app-service/overview-authentication-authorization.md). App Service eszközök felügyeletéhez az összes hozzáférés az Azure AD-beli hitelesített rendszerbiztonsági tag és a Azure Resource Manager RBAC-szerepkörök kombinációjával vezérelhető. |
| Authorization| Igen | App Service eszközök felügyeletéhez az összes hozzáférés az Azure AD-beli hitelesített rendszerbiztonsági tag és a Azure Resource Manager RBAC-szerepkörök kombinációjával vezérelhető.  |


### <a name="audit-trail"></a>Naplózási nyomvonal

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | App Service objektumokon végrehajtott összes felügyeleti művelet Azure Resource Manageron [](../azure-resource-manager/index.yml)keresztül történik. Ezeknek a műveleteknek a korábbi naplói a Portálon és a CLI-n keresztül is elérhetők. Lásd: [Azure Resource Manager erőforrás-szolgáltatói műveletek](../role-based-access-control/resource-provider-operations.md#microsoftweb) és [az monitor Activity-log](/cli/azure/monitor/activity-log). |
| Adatsíkok naplózása és naplózása | Nem | A App Service adatsíkja egy távoli fájlmegosztás, amely az ügyfél telepített webhely-tartalmát tartalmazza.  A távoli fájlmegosztás naplózása nem történik meg. |

### <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen | Felügyeleti műveletek esetében egy App Service konfiguráció állapota Azure Resource Manager sablonként exportálható, és az idő múlásával verzióban is elvégezhető. A futásidejű műveletek esetében az ügyfelek több különböző élő verziót is kezelhetnek az App Service üzembe helyezési pontok funkció használatával. | 



## <a name="azure-resource-managerazure-resource-managerazure-resource-manager-security-attributesmd"></a>[Azure Resource Manager](../azure-resource-manager/azure-resource-manager-security-attributes.md)

### <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például kiszolgálóoldali titkosítás, ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás és egyéb titkosítási funkciók) | Igen |  |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás) | Igen | HTTPS/TLS. |
| Titkosítási kulcsok kezelését (CMK, BYOK stb.)| – | Azure Resource Manager nem tárol felhasználói tartalmat, csak az adatokat vezérli. |
| Oszlop szintű titkosítás (Azure Data Services)| Igen | |
| Titkosított API-hívások| Igen | |

### <a name="network-segmentation"></a>Hálózati szegmentálás

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Nem | |
| VNet-befecskendezés támogatása| Igen | |
| Hálózati elkülönítés és tűzfalak támogatása| Nem |  |
| Kényszerített bújtatás támogatása| Nem |  |

### <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Nem | |

### <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen | [Azure Active Directory](/azure/active-directory) alapján.|
| Authorization| Igen | |


### <a name="audit-trail"></a>Naplózási nyomvonal

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | A Tevékenységnaplók teszik elérhetővé az erőforrásokon végrehajtott összes írási műveletet (PUT, POST, DELETE). Lásd: [tevékenység-naplók megtekintése az erőforrásokon végzett műveletek naplózásához](../azure-resource-manager/resource-group-audit.md). |
| Adatsíkok naplózása és naplózása| – | |

### <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen |  |


## <a name="azure-backupbackupbackup-security-attributesmd"></a>[Azure Backup](../backup/backup-security-attributes.md)

### <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például kiszolgálóoldali titkosítás, ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás és egyéb titkosítási funkciók)| Igen | A Storage szolgáltatás titkosítása a Storage-fiókokhoz. |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás) | Nem | HTTPS használatával. |
| Titkosítási kulcsok kezelését (CMK, BYOK stb.)| Nem |  |
| Oszlop szintű titkosítás (Azure Data Services)| Nem |  |
| Titkosított API-hívások| Igen |  |

### <a name="network-segmentation"></a>Hálózati szegmentálás

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Nem |  |
| VNet-befecskendezés támogatása| Nem |  |
| Hálózati elkülönítés és tűzfalak támogatása| Igen | A virtuális gépek biztonsági mentése a kényszerített bújtatást támogatja. A kényszerített bújtatás nem támogatott a virtuális gépeken belül futó munkaterhelések esetén. |
| Kényszerített bújtatás támogatása| Nem |  |

### <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | A Log Analytics a diagnosztikai naplók használatával támogatott. További információért lásd: [Azure Backup védett munkaterhelések figyelése log Analytics használatával](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) . |

### <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen | A hitelesítés Azure Active Directoryon keresztül történik. |
| Authorization| Igen | A rendszer felhasználja az ügyfél által létrehozott és beépített RBAC-szerepköröket. További információért lásd: [szerepköralapú Access Control használata Azure Backup helyreállítási pontok kezeléséhez](/azure/backup/backup-rbac-rs-vault) . |


### <a name="audit-trail"></a>Naplózási nyomvonal

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | A Azure Portal összes ügyfél által aktivált műveletét a rendszer naplózza a tevékenység naplófájljaiba. |
| Adatsíkok naplózása és naplózása| Nem | Azure Backup az adatsík nem érhető el közvetlenül.  |

### <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen|  |

## <a name="cosmos-dbcosmos-dbcosmos-db-security-attributesmd"></a>[Cosmos DB](../cosmos-db/cosmos-db-security-attributes.md)

### <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például kiszolgálóoldali titkosítás, ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás és egyéb titkosítási funkciók) | Igen | Alapértelmezés szerint az összes Cosmos DB adatbázis és biztonsági másolat titkosítva van; lásd [](../cosmos-db/database-encryption-at-rest.md): adattitkosítás Azure Cosmos DBban. Az ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás nem támogatott. |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Igen | Az összes Azure Cosmos DB-adatforgalom titkosítva van az átvitel során. |
| Titkosítási kulcsok kezelését (CMK, BYOK stb.)| Nem |  |
| Oszlop szintű titkosítás (Azure Data Services)| Igen | Csak a Tables API Premiumban. Nem minden API támogatja ezt a funkciót. Lásd [: a Azure Cosmos db bemutatása: Table API](../cosmos-db/table-introduction.md). |
| Titkosított API-hívások| Igen | A Azure Cosmos DB összes kapcsolata támogatja a HTTPS protokollt. A Azure Cosmos DB a TLS 1,2-kapcsolatokat is támogatja, de ez még nincs érvényesítve. Ha az ügyfelek kikapcsolják az alacsonyabb szintű TLS-t a végére, akkor a Cosmos DBhoz csatlakozhatnak.  |

### <a name="network-segmentation"></a>Hálózati szegmentálás

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Igen |  |
| VNet-befecskendezés támogatása| Igen | A VNet szolgáltatás végpontja lehetővé teszi egy Azure Cosmos DB-fiók konfigurálását, hogy csak a virtuális hálózat (VNet) egy adott alhálózatáról engedélyezze a hozzáférést. A VNet-hozzáférés is kombinálható a tűzfalszabályok használatával.  Lásd: [hozzáférés Azure Cosmos db virtuális hálózatokból](../cosmos-db/VNet-service-endpoint.md). |
| Hálózati elkülönítés és tűzfalak támogatása| Igen | A tűzfal támogatásával beállíthatja az Azure Cosmos-fiókot úgy, hogy csak az IP-címek jóváhagyott készletéről, az IP-címek tartományáról és/vagy a felhőalapú szolgáltatásokból engedélyezze a hozzáférést. Lásd: [az IP-tűzfal konfigurálása Azure Cosmos DBban](../cosmos-db/how-to-configure-firewall.md).|
| Kényszerített bújtatás támogatása| Igen | Konfigurálható az ügyféloldali oldalon azon a VNet, ahol a virtuális gépek találhatók.   |

### <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | A rendszer a Azure Cosmos DB küldött összes kérelmet naplózza. Az Azure [monitoring](../azure-monitor/overview.md), az Azure-metrikák, az Azure audit naplózása támogatott.  Az adatsík-kérelmeknek, a lekérdezési futtatókörnyezet statisztikáinak, a lekérdezési szövegeknek és a MongoDB kérelmeknek megfelelő adatokat naplózhatja. Riasztásokat is beállíthat. |

### <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen | Igen, az adatbázis-fiók szintjén; az adatsík szintjén Cosmos DB erőforrás-tokeneket és kulcs-hozzáférést használ. |
| Authorization| Igen | Támogatott az Azure Cosmos-fiókban főkulcsokkal (elsődleges és másodlagos) és erőforrás-tokenekkel. Olvasási/írási vagy olvasási hozzáférés csak a főkulcsokkal rendelkező adatokhoz. Az erőforrás-tokenek korlátozott ideig férnek hozzá az erőforrásokhoz, például a dokumentumokhoz és a tárolóhoz. |

### <a name="audit-trail"></a>Naplózási nyomvonal

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | Azure-Tevékenységnaplók a fiók szintű műveletekhez, például tűzfalakhoz, virtuális hálózatok, kulcsok eléréséhez és IAM-hoz. |
| Adatsíkok naplózása és naplózása | Igen | Diagnosztika-figyelési naplózás a tárolók szintjén végrehajtott műveletekhez, például tároló létrehozása, átviteli sebesség, indexelési házirendek és szifilisz-műveletek a dokumentumokon. |

### <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Nem  | | 

### <a name="additional-security-attributes-for-cosmos-db"></a>A Cosmos DB további biztonsági attribútumai

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Több eredetű erőforrás-megosztás (CORS) | Igen | Lásd: a [több eredetű erőforrás-megosztás konfigurálása (CORS)](../cosmos-db/how-to-configure-cross-origin-resource-sharing.md). |


## <a name="event-hubsevent-hubsevent-hubs-security-attributesmd"></a>[Event Hubs](../event-hubs/event-hubs-security-attributes.md)

### <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például kiszolgálóoldali titkosítás, ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás és egyéb titkosítási funkciók) |  Igen | |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás) | Igen | |
| Titkosítási kulcsok kezelését (CMK, BYOK stb.)| Nem |  |
| Oszlop szintű titkosítás (Azure Data Services)| – | |
| Titkosított API-hívások| Igen |  |

### <a name="network-segmentation"></a>Hálózati szegmentálás

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Igen |  |
| VNet-befecskendezés támogatása| Nem | |
| Hálózati elkülönítés és tűzfalak támogatása| Igen |  |
| Kényszerített bújtatás támogatása| Nem |  |

### <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | |

### <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen | |
| Authorization|  Igen | |


### <a name="audit-trail"></a>Naplózási nyomvonal

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen |  |
| Adatsíkok naplózása és naplózása| Igen |   |

### <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen | |


## <a name="expressrouteexpressrouteexpressroute-security-attributesmd"></a>[ExpressRoute](../expressroute/expressroute-security-attributes.md)

### <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például kiszolgálóoldali titkosítás, ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás és egyéb titkosítási funkciók) |  – | A ExpressRoute nem tárolja az ügyféladatokat. |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás) | Nem | |
| Titkosítási kulcsok kezelését (CMK, BYOK stb.)| – |  |
| Oszlop szintű titkosítás (Azure Data Services)| – | |
| Titkosított API-hívások| Igen | [Azure Resource Manager](../azure-resource-manager/index.yml) és HTTPS protokollon keresztül. |

### <a name="network-segmentation"></a>Hálózati szegmentálás

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| – |  |
| VNet-befecskendezés támogatása| – | |
| Hálózati elkülönítés és tűzfalak támogatása| Igen | Minden ügyfél saját útválasztási tartományában található, és a saját VNet bújtatott |
| Kényszerített bújtatás támogatása| – | Border Gateway Protocol (BGP) használatával. |

### <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | Lásd: [ExpressRoute-figyelés, mérőszámok és riasztások](../expressroute/expressroute-monitoring-metrics-alerts.md).|

### <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen | Szolgáltatásfiók a Microsoft számára (GWM) (vezérlő); Igény szerinti (JIT) hozzáférés a dev és az OP számára. |
| Authorization|  Igen |Szolgáltatásfiók a Microsoft számára (GWM) (vezérlő); Igény szerinti (JIT) hozzáférés a dev és az OP számára. |


### <a name="audit-trail"></a>Naplózási nyomvonal

| Biztonsági attribútum | Igen/nem | Megjegyzések| 
|---|---|--|
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen |  |
| Adatsíkok naplózása és naplózása| Nem |   |

### <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen | A hálózati erőforrás-szolgáltató (NRP) használatával. |


## <a name="key-vaultkey-vaultkey-vault-security-attributesmd"></a>[Key Vault](../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például kiszolgálóoldali titkosítás, ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás és egyéb titkosítási funkciók) | Igen | Minden objektum titkosítva van. |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás) | Igen | Minden kommunikáció titkosított API-hívásokon keresztül történik |
| Titkosítási kulcsok kezelését (CMK, BYOK stb.)| Igen | Az ügyfél a Key Vault összes kulcsát vezérli. Ha a hardveres biztonsági modul (HSM) által támogatott kulcsok meg vannak adva, akkor a 2. FIPS szintű HSM védi a kulcsot, a tanúsítványt vagy a titkos kulcsot. |
| Oszlop szintű titkosítás (Azure Data Services)| – |  |
| Titkosított API-hívások| Igen | HTTPS használatával. |

### <a name="network-segmentation"></a>Hálózati szegmentálás

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Igen | Virtual Network (VNet) szolgáltatási végpontok használata. |
| VNet-befecskendezés támogatása| Nem |  |
| Hálózati elkülönítés és tűzfalak támogatása| Igen | VNet tűzfalszabályok használata. |
| Kényszerített bújtatás támogatása| Nem |  |

### <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | Log Analytics használata. |

### <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen | A hitelesítés Azure Active Directoryon keresztül történik. |
| Authorization| Igen | Key Vault hozzáférési házirend használatával. |

### <a name="audit-trail"></a>Naplózási nyomvonal

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlési/felügyeleti síkok naplózása és naplózása| Igen | Log Analytics használata. |
| Adatsíkok naplózása és naplózása| Igen | Log Analytics használata. |

### <a name="access-controls"></a>Hozzáférés-szabályozás

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő/felügyeleti sík hozzáférés-vezérlése | Igen | Az Azure Resource Manager szerepköralapú hozzáférés-vezérlése (RBAC) |
| Adatsíkok hozzáférés-vezérlése (minden szolgáltatási szinten) | Igen | Hozzáférési szabályzat Key Vault |

## <a name="load-balancerload-balancerload-balancer-security-attributesmd"></a>[Load Balancer](../load-balancer/load-balancer-security-attributes.md)

### <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például kiszolgálóoldali titkosítás, ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás és egyéb titkosítási funkciók) | – | |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| – | |
| Titkosítási kulcsok kezelését (CMK, BYOK stb.)| – | |
| Oszlop szintű titkosítás (Azure Data Services)| – | |
| Titkosított API-hívások| Igen | A [Azure Resource Manageron](../azure-resource-manager/index.yml)keresztül. |

### <a name="network-segmentation"></a>Hálózati szegmentálás

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| – | |
| VNet-befecskendezés támogatása| – | . |
| Hálózati elkülönítés és tűzfalak támogatása| – |  |
| Kényszerített bújtatás támogatása| – | |

### <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | Lásd: [Azure monitor naplók a nyilvános](../load-balancer/load-balancer-monitor-log.md)alapszintű Load Balancerhoz. |

### <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| – |  |
| Authorization| – |  |

### <a name="audit-trail"></a>Naplózási nyomvonal

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | Lásd: [Azure monitor naplók a nyilvános](../load-balancer/load-balancer-monitor-log.md)alapszintű Load Balancerhoz. |
| Adatsíkok naplózása és naplózása | – |  |

### <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| – |  | 

## <a name="service-bus-messagingservice-bus-messagingservice-bus-messaging-security-attributesmd"></a>[Üzenetküldés Service Bus](../service-bus-messaging/service-bus-messaging-security-attributes.md)

### <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például kiszolgálóoldali titkosítás, ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás és egyéb titkosítási funkciók) |  Igen, alapértelmezés szerint a kiszolgálóoldali titkosításhoz. | Az ügyfél által felügyelt kulcsok és BYOK még nem támogatottak. Az ügyféloldali titkosítás az ügyfél felelőssége |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás) | Igen | Támogatja a szabványos HTTPS/TLS-mechanizmust. |
| Titkosítási kulcsok kezelését (CMK, BYOK stb.)| Nem |   |
| Oszlop szintű titkosítás (Azure Data Services)| – | |
| Titkosított API-hívások| Igen | Az API-hívások [Azure Resource Manager](../azure-resource-manager/index.yml) és HTTPS protokollon keresztül történnek. |

### <a name="network-segmentation"></a>Hálózati szegmentálás

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Igen (csak prémium szint esetén) | A VNet szolgáltatási végpontok csak [Service Bus prémium szint](../service-bus-messaging/service-bus-premium-messaging.md) esetén támogatottak. |
| VNet-befecskendezés támogatása| Nem | |
| Hálózati elkülönítés és tűzfalak támogatása| Igen (csak prémium szint esetén) |  |
| Kényszerített bújtatás támogatása| Nem |  |

### <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | [Azure monitor és riasztások](../service-bus-messaging/service-bus-metrics-azure-monitor.md)használatával támogatott. |

### <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen | Felügyelt [Azure Active Directory Managed Service Identity](../service-bus-messaging/service-bus-managed-service-identity.md); Lásd: [Service Bus hitelesítés és engedélyezés](../service-bus-messaging/service-bus-authentication-and-authorization.md).|
| Authorization| Igen | A [RBAC](../service-bus-messaging/service-bus-role-based-access-control.md) (előzetes verzió) és az SAS-token használatával támogatja a hitelesítést. Lásd: [Service Bus hitelesítés és engedélyezés](../service-bus-messaging/service-bus-authentication-and-authorization.md). |


### <a name="audit-trail"></a>Naplózási nyomvonal

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | Az operatív naplók elérhetők; Lásd: [Service Bus diagnosztikai naplók](../service-bus-messaging/service-bus-diagnostic-logs.md).  |
| Adatsíkok naplózása és naplózása| Nem |  |

### <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen | Támogatja az erőforrás-szolgáltatói verziószámozást a [Azure Resource Manager API](/rest/api/resources/)-n keresztül.|


## <a name="service-bus-relayservice-bus-relayservice-bus-relay-security-attributesmd"></a>[Service Bus Relay](../service-bus-relay/service-bus-relay-security-attributes.md)

### <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például kiszolgálóoldali titkosítás, ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás és egyéb titkosítási funkciók) |  – | A Relay egy webes szoftvercsatorna, és nem őrzi meg az adatmegőrzési időt. |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás) | Igen | A szolgáltatáshoz TLS szükséges. |
| Titkosítási kulcsok kezelését (CMK, BYOK stb.)| Nem | Csak a Microsoft TLS-tanúsítványokat használja.  |
| Oszlop szintű titkosítás (Azure Data Services)| – | |
| Titkosított API-hívások| Igen | HTTPS. |

### <a name="network-segmentation"></a>Hálózati szegmentálás

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Nem |  |
| Hálózati elkülönítés és tűzfalak támogatása| Nem |  |
| Kényszerített bújtatás támogatása| – | A Relay a TLS-alagút  |

### <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | |

### <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen | SAS-n keresztül. |
| Authorization|  Igen | SAS-n keresztül. |

### <a name="audit-trail"></a>Naplózási nyomvonal

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | [Azure Resource Manageron](../azure-resource-manager/index.yml)keresztül. |
| Adatsíkok naplózása és naplózása| Igen | A sikeres és sikertelen kapcsolatok, valamint a naplózott hibák.  |

### <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen | [Azure Resource Manageron](../azure-resource-manager/index.yml)keresztül.|

## <a name="service-fabricservice-fabricservice-fabric-security-attributesmd"></a>[Service Fabric](../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például kiszolgálóoldali titkosítás, ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás és egyéb titkosítási funkciók) | Igen | Az ügyfél tulajdonosa a fürt és a virtuálisgép-méretezési csoport, amelyen a fürt épül. Az Azure Disk Encryption szolgáltatás a virtuálisgép-méretezési csoporton engedélyezhető. |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás) | Igen | Az ügyfél tulajdonosa a fürt és a virtuálisgép-méretezési csoport, amelyen a fürt épül. Az Azure Disk Encryption szolgáltatás a virtuálisgép-méretezési csoporton engedélyezhető. |
| Oszlop szintű titkosítás (Azure Data Services)| – |  |
| Titkosított API-hívások| Igen | Service Fabric API-hívások Azure Resource Manageron keresztül történnek. Érvényes JSON webes jogkivonat (JWT) szükséges. |

### <a name="network-segmentation"></a>Hálózati szegmentálás

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Igen |  |
| VNet-befecskendezés támogatása| Igen |  |
| Hálózati elkülönítés és tűzfalak támogatása| Igen | Hálózati biztonsági csoportok (NSG) használata. |
| Kényszerített bújtatás támogatása| Igen | Az Azure Networking kényszerített bújtatást biztosít. |

### <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | Az Azure monitoring támogatás és a harmadik féltől származó támogatás használata. |

### <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen | A hitelesítés Azure Active Directoryon keresztül történik. |
| Authorization| Igen | Az identitás-és hozzáférés-kezelés (IAM) a SFRP-on keresztüli hívásokhoz. A közvetlenül a fürt végpontja felé irányuló hívások két szerepkört támogatnak: Felhasználó és rendszergazda. Az ügyfél bármely szerepkörhöz hozzárendelheti az API-kat. |

### <a name="audit-trail"></a>Naplózási nyomvonal

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | A vezérlési sík műveletei a naplózási és jóváhagyási folyamatokon keresztül futnak. |
| Adatsíkok naplózása és naplózása| – | Az ügyfél tulajdonosa a fürt.  |

### <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen | |

## <a name="sql-databasesql-databasesql-database-security-attributesmd"></a>[SQL Database](../sql-database/sql-database-security-attributes.md)

SQL Database magában foglalja az [önálló adatbázist](../sql-database/sql-database-single-index.yml) és a [felügyelt példányt](../sql-database/sql-database-managed-instance.md)is. A következő bejegyzések mindkét ajánlatra érvényesek, kivéve ha ez másként nincs jelezve.

### <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például kiszolgálóoldali titkosítás, ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás és egyéb titkosítási funkciók) | Igen | A "titkosítás használaton kívül" kifejezést a [Always encrypted](../sql-database/sql-database-always-encrypted.md)című cikkben leírtak szerint kell elnevezni. A kiszolgálóoldali titkosítás [transzparens](../sql-database/transparent-data-encryption-azure-sql.md)adattitkosítást használ.|
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás) | Igen | HTTPS használatával. |
| Titkosítás – kulcsok kezelését, például CMK vagy BYOK| Igen | A szolgáltatással felügyelt és az ügyfél által felügyelt kulcsok kezelése egyaránt elérhető. Az utóbbit Azure Key Vaulton [](../key-vault/index.yml)keresztül kínáljuk. |
| Az Azure-adatszolgáltatások által biztosított oszlop szintű titkosítás| Igen | [Always Encryptedon](../sql-database/sql-database-always-encrypted.md)keresztül. |
| Titkosított API-hívások| Igen | HTTPS/SSL használatával. |

### <a name="network-segmentation"></a>Hálózati szegmentálás

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Igen | Csak az [önálló adatbázisra](../sql-database/sql-database-single-index.yml) vonatkozik. |
| Azure Virtual Network injekciós támogatás| Igen | Csak a [felügyelt példányra](../sql-database/sql-database-managed-instance.md) vonatkozik. |
| Hálózati elkülönítés és tűzfal-támogatás| Igen | Tűzfal az adatbázis szintjén és a kiszolgáló szintjén is. A hálózati elkülönítés csak [felügyelt példány](../sql-database/sql-database-managed-instance.md) esetén használható. |
| Kényszerített bújtatás támogatása| Igen | [Felügyelt példány](../sql-database/sql-database-managed-instance.md) egy [ExpressRoute](../expressroute/index.yml) VPN-en keresztül. |

### <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás, például Log Analytics vagy Application Insights| Igen | A SecureSphere, a inperverztől származó SIEM-megoldás az [Azure Event Hubs](../event-hubs/index.yml) -integráción keresztül is támogatott az [SQL](../sql-database/sql-database-auditing.md)-naplózással. |

### <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen | Azure Active Directory (Azure AD) |
| Authorization| Igen | Nincsenek |

### <a name="audit-trail"></a>Naplózási nyomvonal

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlés – sík és felügyelet – a sík naplózása és naplózása| Igen | Igen, csak néhány eseménynél |
| Adatsíkok naplózása és naplózása | Igen | [SQL auditon](../sql-database/sql-database-auditing.md) keresztül |

### <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció-felügyeleti támogatás, például a konfiguráció verziószámozása| Nem  | None |

### <a name="additional-security-attributes-for-sql-database"></a>A SQL Database további biztonsági attribútumai

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Megelőző: sebezhetőségi felmérés | Igen | Lásd az [SQL sebezhetőség-felmérési szolgáltatás segítséget nyújt az adatbázis-biztonsági rések azonosításához](../sql-database/sql-vulnerability-assessment.md). |
| Megelőző: adatfelderítés és besorolás  | Igen | Lásd: [Azure SQL Database és SQL Data Warehouse az adatfelderítés & besorolása](../sql-database/sql-database-data-discovery-and-classification.md). |
| Észlelés: fenyegetések észlelése | Igen | Lásd: [a Azure SQL Database összetett veszélyforrások elleni védelme](../sql-database/sql-database-threat-detection-overview.md). |


## <a name="storagestoragecommonstorage-security-attributesmd"></a>[Storage](../storage/common/storage-security-attributes.md)

### <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például kiszolgálóoldali titkosítás, ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás és egyéb titkosítási funkciók) | Igen |  |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás) | Igen | A szabványos HTTPS/TLS-mechanizmusok támogatása.  A felhasználók a szolgáltatásba való továbbítás előtt is titkosítani tudják az adatokat. |
| Titkosítási kulcsok kezelését (CMK, BYOK stb.)| Igen | Lásd: [Storage Service encryption az ügyfél által felügyelt kulcsokkal Azure Key Vaultban](../storage/common/storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Oszlop szintű titkosítás (Azure Data Services)| – |  |
| Titkosított API-hívások| Igen |  |

### <a name="network-segmentation"></a>Hálózati szegmentálás

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Igen |  |
| VNet-befecskendezés támogatása| – |  |
| Hálózati elkülönítés és tűzfalak támogatása| Igen | |
| Kényszerített bújtatás támogatása| – |  |

### <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | Azure Monitor a metrikák mostantól elérhetők, az előzetes verziójú naplók |

### <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen | Azure Active Directory, megosztott kulcs, közös hozzáférési jogkivonat. |
| Authorization| Igen | Támogatás engedélyezése RBAC, POSIX ACL-eken és SAS-tokeneken keresztül |

### <a name="audit-trail"></a>Naplózási nyomvonal

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlési és felügyeleti síkok naplózása és naplózása | Igen | Azure Resource Manager tevékenység naplója |
| Adatsíkok naplózása és naplózása| Igen | Szolgáltatás-diagnosztikai naplók és Azure Monitor naplózás indítása – előzetes verzió  |

### <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen | Azure Resource Manager API-k támogatása az erőforrás-szolgáltatónál |

## <a name="virtual-machines-and-virtual-machine-scale-sets"></a>Virtual Machines és Virtual Machine Scale Sets

[Linuxos virtuális](../virtual-machines/windows/virtual-machines-windows-security-attributes.md)gépek[Windows rendszerű](../virtual-machines/windows/virtual-machines-windows-security-attributes.md) | [virtuális gépek virtuálisgép-méretezési csoportokkal](../virtual-machine-scale-sets/virtual-machine-scale-sets-security-attributes.md)  | 


### <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például kiszolgálóoldali titkosítás, ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás és egyéb titkosítási funkciók) | Igen | Lásd: Linux rendszerű [virtuális gép titkosítása az Azure-ban](/azure/virtual-machines/linux/encrypt-disks) és a [virtuális lemezek titkosítása egy Windowsos](/azure/virtual-machines/windows/encrypt-disks)virtuális gépen. |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Igen | Az Azure Virtual Machines támogatja a [ExpressRoute](/azure/expressroute) és a VNet titkosítást. Lásd: [tranzitraktár titkosítás a virtuális gépeken](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Titkosítási kulcsok kezelését (CMK, BYOK stb.)| Igen | Az ügyfél által felügyelt kulcsok egy támogatott Azure-titkosítási forgatókönyv; Lásd: az [Azure-titkosítás áttekintése](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Oszlop szintű titkosítás (Azure Data Services)| – | |
| Titkosított API-hívások| Igen | HTTPS és SSL használatával. |

### <a name="network-segmentation"></a>Hálózati szegmentálás

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Igen | |
| VNet-befecskendezés támogatása| Igen | . |
| Hálózati elkülönítés és tűzfalak támogatása| Igen |  |
| Kényszerített bújtatás támogatása| Igen | Lásd: [kényszerített bújtatás konfigurálása a Azure Resource Manager üzemi modell használatával](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

### <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | Lásd: Linux rendszerű [virtuális gépek monitorozása és frissítése az Azure-ban](/azure/virtual-machines/linux/tutorial-monitoring) , valamint [Windowsos virtuális gépek monitorozása és frissítése az Azure-ban](/azure/virtual-machines/windows/tutorial-monitoring). |

### <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen |  |
| Authorization| Igen |  |


### <a name="audit-trail"></a>Naplózási nyomvonal

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen |  |
| Adatsíkok naplózása és naplózása | Nem |  |

### <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen |  | 


## <a name="vpn-gatewayvpn-gatewayvpn-gateway-security-attributesmd"></a>[VPN Gateway](../vpn-gateway/vpn-gateway-security-attributes.md)

### <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például kiszolgálóoldali titkosítás, ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás és egyéb titkosítási funkciók) | – | A VPN Gateway tranzit ügyféladatok nem tárolja az ügyféladatokat |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Igen | A VPN-átjáró az ügyfelek csomagjait az Azure VPN-átjárók és az ügyfél helyszíni VPN-eszközei (S2S) vagy a VPN-ügyfelek (P2S-EK) között titkosítja. A VPN-átjárók támogatják a VNet-VNet titkosítást is. |
| Titkosítási kulcsok kezelését (CMK, BYOK stb.)| Nem | Az ügyfél által megadott előmegosztott kulcsok titkosítva vannak a nyugalmi állapotban; de még nincs integrálva a CMK. |
| Oszlop szintű titkosítás (Azure Data Services)| – | |
| Titkosított API-hívások| Igen | [Azure Resource Manager](../azure-resource-manager/index.yml) és HTTPS protokollon keresztül  |

### <a name="network-segmentation"></a>Hálózati szegmentálás

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| – | |
| VNet-befecskendezés támogatása| – | . |
| Hálózati elkülönítés és tűzfalak támogatása| Igen | A VPN-átjárók dedikált virtuálisgép-példányok minden ügyfélnél Virtual Network  |
| Kényszerített bújtatás támogatása| Igen |  |

### <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | Lásd: [Azure monitor diagnosztikai naplók/riasztás](../vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure monitor metrikák/riasztások](../vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |

### <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) a szolgáltatás kezeléséhez és az Azure VPN Gateway konfigurálásához. |
| Authorization| Igen | Támogatás engedélyezése a [RBAC](../role-based-access-control/overview.md)-on keresztül. |

### <a name="audit-trail"></a>Naplózási nyomvonal

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | Azure Resource Manager tevékenység naplója. |
| Adatsíkok naplózása és naplózása | Igen | [Azure monitor diagnosztikai naplók](../azure-resource-manager/resource-group-audit.md) a VPN-kapcsolat naplózásához és naplózásához. |

### <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen | Felügyeleti műveletek esetében az Azure VPN Gateway konfigurációjának állapota exportálható Azure Resource Manager sablonként, és az idő múlásával is elvégezhető. | 

