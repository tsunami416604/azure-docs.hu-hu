---
title: Az Azure-szolgáltatások biztonsági attribútumok
description: Az Azure Service Fabric értékelésére közös biztonsági attribútumok ellenőrzőlista
services: security
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: security
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 64accb70561d4c0282b3ee45935d955dba1c67c4
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474539"
---
# <a name="security-attributes-for-azure-services"></a>Az Azure-szolgáltatások biztonsági attribútumok

Ez a cikk összegyűjti a kiválasztott Azure-szolgáltatások általános biztonsági attribútumait. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="azure-api-managementapi-managementapi-management-security-attributesmd"></a>[Azure API Management](../api-management/api-management-security-attributes.md)

### <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban:<ul><li>Kiszolgálóoldali titkosítás</li><li>Kiszolgálóoldali titkosítás a felhasználó által kezelt kulcsok</li><li>Más titkosítási funkciók (például az ügyféloldali, mindig titkosított, stb.)</ul>| Igen (csak a Szolgáltatásoldali titkosítás) | Bizalmas adatok, például a tanúsítványok, kulcsok és titkos kulcs nevű értékek van titkosítva, szolgáltatás által kezelt, kiszolgálónként kulcsai példánya. |
| Titkosítás az átvitel során:<ul><li>Express route-titkosítás</li><li>A VNet-titkosítás</li><li>Hálózatok titkosítása</ul>| Igen | [Express Route](../expressroute/index.yml) és a virtuális hálózat a titkosítást [az Azure-hálózatok](../virtual-network/index.yml). |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| Nem | Az összes titkosítási kulcs szolgáltatás példányonként és -szolgáltatások által. |
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| – | |
| Titkosított API-hívások| Igen | Felügyeleti sík hívások használatával jönnek létre [Azure Resource Manager](../azure-resource-manager/index.yml) TLS protokollon keresztüli. Egy érvényes JSON webes jogkivonat (JWT) megadása kötelező.  Adatsík-hívások adatok védve legyenek a TLS és a egy támogatott hitelesítési mechanizmusok (pl. ügyféltanúsítványt vagy JWT).
 |

### <a name="network-segmentation"></a>Hálózati szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatási végpont támogatás| Nem | |
| VNet-injektálási támogatás| Igen | |
| Hálózatelkülönítés és támogatási optimalizálóként működik| Igen | Hálózati biztonsági csoportokkal (NSG) és az Azure Application Gateway (vagy más szoftveralapú berendezés) jelölik. |
| Kényszerített bújtatás támogatása| Igen | Azure-hálózatok biztosítják a kényszerített bújtatás. |

### <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Igen | |

### <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | |
| Engedélyezés| Igen | |


### <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő és a felügyeleti sík naplózási és naplózása| Igen | [Az Azure Monitor-Tevékenységnaplók](../azure-monitor/platform/activity-logs-overview.md) |
| Adatsík naplózása és naplózása| Igen | [Az Azure Monitor-diagnosztikai naplók](../azure-monitor/platform/diagnostic-logs-overview.md) és (opcionálisan) [Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  |

### <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció kezelésével kapcsolatos támogatás (versioning konfiguráció stb.)| Igen | Használatával a [az Azure API Management fejlesztési és üzemeltetési Resource Kit](https://aka.ms/apimdevops) |


## <a name="azure-app-serviceapp-serviceapp-service-security-attributesmd"></a>[Azure App Service](../app-service/app-service-security-attributes.md)

### <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például a kiszolgálóoldali titkosítás, a kiszolgálóoldali titkosítást az ügyfél által felügyelt kulcsokat és más titkosítási szolgáltatások) | Igen | Azure Storage, amely automatikusan titkosítja az inaktív a tartalmat a webhely fájl tartalmat tárolja. Lásd: [inaktív adatok titkosítása az Azure Storage](../storage/common/storage-service-encryption.md).<br><br>A megadott ügyfél titkos kulcsok vannak titkosítása. A titkos kulcsok vannak titkosítva, míg az App Service-konfiguráció adatbázisokban tárolt inaktív.<br><br>Helyileg csatlakoztatott lemezek igény szerint használható ideiglenes tárolóként webhelyek (D:\local és a % TMP %). Helyileg csatlakoztatott lemezek inaktív nincsenek titkosítva. |
| Titkosítás az átvitel során (például az ExpressRoute-titkosítás, a virtuális hálózatok közötti titkosítás és a VNet – VNet titkosítási)| Igen | Ügyfelek konfigurálhatják a webhelyek igényelnek, és a bejövő forgalmat a HTTPS PROTOKOLLT használja. Tekintse meg a következő blogbejegyzésben: [biztosítása az Azure App Service csak HTTPS](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/). |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| Igen | Ügyfelek kiválaszthatják a titkos alkalmazáskulcsok tárolása a Key Vaultban, és kérheti le azokat a futásidőben. Lásd: [a Key Vault használata az App Service-ben és az Azure Functions (előzetes verzió) hivatkozik](../app-service/app-service-key-vault-references.md).|
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| – | |
| Titkosított API-hívások| Igen | Felügyeleti hívások konfigurálásához az App Service-n keresztül történik [Azure Resource Manager](../azure-resource-manager/index.yml) hívások HTTPS-kapcsolaton keresztül. |

### <a name="network-segmentation"></a>Hálózati szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatási végpont támogatás| Igen | Jelenleg az App Service előzetes verzióban érhető el. Lásd: [korlátozza a hozzáférést az Azure App Service](../app-service/app-service-ip-restrictions.md). |
| VNet-injektálási támogatás| Igen | App Service Environment-környezetek megvalósításai privát kártevő program férkőzik be egy ügyfél virtuális hálózatán egyetlen ügyfél számára dedikált App Service-ben. Lásd: [az App Service Environment bemutatása](../app-service/environment/intro.md). |
| Hálózatelkülönítés és Firewalling támogatása| Igen | Az App Service-ben nyilvános több-bérlős változata a felhasználók beállíthatják a hálózati hozzáférés-vezérlési listák (IP-korlátozások) való zárolását, így az engedélyezett bejövő forgalom.  Lásd: [korlátozza a hozzáférést az Azure App Service](../app-service/app-service-ip-restrictions.md).  App Service Environment-környezetek közvetlenül virtuális hálózatokra vannak telepítve, és ezáltal védve legyenek az NSG-ket. |
| Kényszerített bújtatás támogatása| Igen | App Service Environment-környezetek is telepíthető, ahol kényszerített bújtatás konfigurálása az ügyfél virtuális hálózatban. Kövesse az utasításokat kell [az App Service Environment konfigurálása kényszerített bújtatással](../app-service/environment/forced-tunnel-support.md). |

### <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Igen | Az App Service az Application Insights nyelveket, amelyek támogatják az Application Insights (teljes .NET-keretrendszert, .NET Core, Java és Node.JS) együttműködik.  Lásd: [figyelése az Azure App Service teljesítményének](../azure-monitor/app/azure-web-apps.md). App Service-ben az alkalmazásmetrikák is küld az Azure Monitor szolgáltatásba. Lásd: [alkalmazások figyelése az Azure App Service-ben](../app-service/web-sites-monitor.md). |

### <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | Ügyfeleink App Service-ben, amely automatikusan integrálható alkalmazásokat hozhat létre [Azure Active Directory (Azure AD)](../active-directory/index.yml) és más OAuth kompatibilis identitásszolgáltatókkal; lásd: [hitelesítés és engedélyezés Az Azure App Service](../app-service/overview-authentication-authorization.md). Felügyeleti hozzáférés az App Service-eszközöket minden hozzáférés hitelesítése az Azure AD egyszerű és Azure Resource Manager RBAC-szerepkörök kombinációja vezérlik majd. |
| Engedélyezés| Igen | Felügyeleti hozzáférés az App Service-eszközöket minden hozzáférés hitelesítése az Azure AD egyszerű és Azure Resource Manager RBAC-szerepkörök kombinációja vezérlik majd.  |


### <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő és a felügyeleti sík naplózási és naplózása| Igen | App Service-ben az objektumokon végrehajtott összes felügyeleti műveletet keresztül történnek [Azure Resource Manager](../azure-resource-manager/index.yml). Korábbi műveletek érhetők el naplók a portálon és a parancssori felületén; Lásd: [Azure Resource Manager erőforrás-szolgáltatói műveletek](../role-based-access-control/resource-provider-operations.md#microsoftweb) és [az monitor tevékenységnapló](/cli/azure/monitor/activity-log). |
| Adatsík naplózása és naplózása | Nem | Az adatsík az App Service-ben egy ügyfél s üzembe helyezett webhely tartalom tartalmazó távoli fájlmegosztásban.  Nincs nem kerülnek naplózásra a távoli fájlmegosztásban. |

### <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció kezelésével kapcsolatos támogatás (versioning konfiguráció stb.)| Igen | A felügyeleti műveletek esetében egy App Service-ben konfigurációs állapotát exportálhatók rendszerverzióval ellátott és Azure Resource Manager-sablonok idővel. Ügyfeleink a futásidejű műveletek egy alkalmazást az App Service üzembe helyezési pontok funkció használatával több különböző élő verzióit fenntartására. | 


## <a name="azure-resource-managerazure-resource-managerazure-resource-manager-security-attributesmd"></a>[Azure Resource Manager](../azure-resource-manager/azure-resource-manager-security-attributes.md)

### <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban:<ul><li>Kiszolgálóoldali titkosítás</li><li>Kiszolgálóoldali titkosítás a felhasználó által kezelt kulcsok</li><li>Más titkosítási funkciók (például az ügyféloldali, mindig titkosított, stb.)</ul>| Igen |  |
| Titkosítás az átvitel során:<ul><li>Express route-titkosítás</li><li>A VNet-titkosítás</li><li>Hálózatok titkosítása</ul>| Igen | HTTPS/TLS. |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| – | Az Azure Resource Manager nincs ügyfél tartalom, csak a vezérlő adatokat tárolja. |
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| Igen | |
| Titkosított API-hívások| Igen | |

### <a name="network-segmentation"></a>Hálózati szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatási végpont támogatás| Nem | |
| VNet-injektálási támogatás| Igen | |
| Hálózatelkülönítés és támogatási optimalizálóként működik| Nem |  |
| Kényszerített bújtatás támogatása| Nem |  |

### <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Nem | |

### <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | [Az Azure Active Directory](/azure/active-directory) alapján.|
| Engedélyezés| Igen | |


### <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő és a felügyeleti sík naplózási és naplózása| Igen | Tevékenységnaplók elérhetővé tétele minden írási az erőforrások; a végrehajtott műveletek (PUT, POST, DELETE) Lásd: [megtekintése az erőforrásokon végzett műveletek naplózásához tevékenységi naplóit](../azure-resource-manager/resource-group-audit.md). |
| Adatsík naplózása és naplózása| – | |

### <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció kezelésével kapcsolatos támogatás (versioning konfiguráció stb.)| Igen |  |


## <a name="azure-backupbackupbackup-security-attributesmd"></a>[Azure Backup](../backup/backup-security-attributes.md)

### <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban:<ul><li>Kiszolgálóoldali titkosítás</li><li>Kiszolgálóoldali titkosítás a felhasználó által kezelt kulcsok</li><li>Más titkosítási funkciók (például az ügyféloldali, mindig titkosított, stb.)</ul>| Igen | A storage service encryption segítségével a storage-fiókok. |
| Titkosítás az átvitel során:<ul><li>Express route-titkosítás</li><li>A VNet-titkosítás</li><li>Hálózatok titkosítása</ul>| Nem | HTTPS-en keresztül. |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| Nem |  |
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| Nem |  |
| Titkosított API-hívások| Igen |  |

### <a name="network-segmentation"></a>Hálózati szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatási végpont támogatás| Nem |  |
| VNet-injektálási támogatás| Nem |  |
| Hálózatelkülönítés és támogatási optimalizálóként működik| Igen | Kényszerített bújtatás támogatott virtuális gépek biztonsági mentését. Kényszerített bújtatás virtuális gépeken belül futó munkaterhelések esetében nem támogatott. |
| Kényszerített bújtatás támogatása| Nem |  |

### <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Igen | A log Analytics diagnosztikai naplóinak keresztül támogatott. Tekintse meg a figyelő az Azure Backup-alapú számítási feladatokat a Log Analytics által védett (https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) további információt. |

### <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | Hitelesítés az Azure Active Directoryn keresztül. |
| Engedélyezés| Igen | Létrehozott ügyfél és a beépített RBAC-szerepkör használja. Tekintse meg a Use Role-Based hozzáférés-vezérlés kezelése az Azure Backup helyreállítási pontok (/ azure/biztonsági mentés/backup-rbac-rs-tároló) további információt. |


### <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő és a felügyeleti sík naplózási és naplózása| Igen | Tevékenységnaplók az Azure Portalról az összes aktivált ügyfelek műveletek naplózása. |
| Adatsík naplózása és naplózása| Nem | Az Azure Backup az adatsík nem közvetlenül érhető el.  |

### <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció kezelésével kapcsolatos támogatás (versioning konfiguráció stb.)| Igen|  |


## <a name="azure-key-vaultkey-vaultkey-vault-security-attributesmd"></a>[Azure Key Vault](../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban:<ul><li>Kiszolgálóoldali titkosítás</li><li>Kiszolgálóoldali titkosítás a felhasználó által kezelt kulcsok</li><li>Más titkosítási funkciók (például az ügyféloldali, mindig titkosított, stb.)</ul>| Igen | Összes objektum titkosított. |
| Titkosítás az átvitel során:<ul><li>Express route-titkosítás</li><li>A VNet-titkosítás</li><li>Hálózatok titkosítása</ul>| Igen | Minden kommunikáció titkosított API-hívásokon keresztül van |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| Igen | Ügyfél szabályozza az összes kulcsok a Key vaultban. Ha hardveres biztonsági modul (HSM) a biztonsági kulcsok meg van adva, a FIPS-szint 2 HSM védi a kulcsot, a tanúsítvány vagy a titkos kulcs. |
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| – |  |
| Titkosított API-hívások| Igen | HTTPS-en keresztül. |

### <a name="network-segmentation"></a>Hálózati szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatási végpont támogatás| Igen | Virtuális hálózat (VNet) Szolgáltatásvégpontok használatával. |
| VNet-injektálási támogatás| Nem |  |
| Hálózatelkülönítés és támogatási optimalizálóként működik| Igen | Virtuális hálózatok közötti tűzfalszabályok használatával. |
| Kényszerített bújtatás támogatása| Nem |  |

### <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Igen | A Log Analytics használatával. |

### <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | Hitelesítés az Azure Active Directoryn keresztül. |
| Engedélyezés| Igen | Kulcstartó hozzáférési házirendjének használatával. |


### <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Ellenőrzés/felügyeleti naplózás és a naplózási adatsík| Igen | A Log Analytics használatával. |
| Adatsík naplózása és naplózása| Igen | A Log Analytics használatával. |

### <a name="access-controls"></a>Hozzáférés-vezérlés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Ellenőrzés/felügyeleti sík hozzáférés-vezérlés | Igen | Az Azure Resource Manager szerepköralapú hozzáférés-vezérlése (RBAC) |
| Adatsík hozzáférés-vezérlést (szinten minden szolgáltatás) | Igen | Kulcstartó-hozzáférési házirend |

## <a name="azure-service-bus-messagingservice-bus-messagingservice-bus-messaging-security-attributesmd"></a>[Az Azure Service Bus-üzenetkezelés](../service-bus-messaging/service-bus-messaging-security-attributes.md)

### <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban:<ul><li>Kiszolgálóoldali titkosítás</li><li>Kiszolgálóoldali titkosítás a felhasználó által kezelt kulcsok</li><li>Más titkosítási funkciók (például az ügyféloldali, mindig titkosított, stb.)</ul>|  Kiszolgálóoldali titkosítás inaktív alapértelmezés szerint az Igen gombra. | Az ügyfél által felügyelt kulcsokat és a BYOK még nem támogatottak. Az ügyfél feladata ügyféloldali titkosítása |
| Titkosítás az átvitel során:<ul><li>Express route-titkosítás</li><li>A VNet-titkosítás</li><li>Hálózatok titkosítása</ul>| Igen | Támogatja a szabványos HTTPS/TLS-mechanizmust. |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| Nem |   |
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| – | |
| Titkosított API-hívások| Igen | API-hívás használatával jönnek létre [Azure Resource Manager](../azure-resource-manager/index.yml) és a HTTPS. |

### <a name="network-segmentation"></a>Hálózati szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatási végpont támogatás| Igen (csak prémium szintű) | Virtuális hálózati Szolgáltatásvégpontok támogatottak [Service Bus prémium szintű](../service-bus-messaging/service-bus-premium-messaging.md) csak. |
| VNet-injektálási támogatás| Nem | |
| Hálózatelkülönítés és támogatási optimalizálóként működik| Igen (csak prémium szintű) |  |
| Kényszerített bújtatás támogatása| Nem |  |

### <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Igen | Támogatott [az Azure Monitor és a riasztások](../service-bus-messaging/service-bus-metrics-azure-monitor.md). |

### <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | Kezelhető [Azure Active Directory Felügyeltszolgáltatás-identitás](../service-bus-messaging/service-bus-managed-service-identity.md); lásd: [Service Bus-hitelesítés és engedélyezés](../service-bus-messaging/service-bus-authentication-and-authorization.md).|
| Engedélyezés| Igen | Engedélyezés a következővel támogatja [RBAC](../service-bus-messaging/service-bus-role-based-access-control.md) (előzetes verzió) és a SAS jogkivonat-; lásd: [Service Bus-hitelesítés és engedélyezés](../service-bus-messaging/service-bus-authentication-and-authorization.md). |

### <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő és a felügyeleti sík naplózási és naplózása| Igen | Műveleti naplók érhetők el; Lásd: [a Service Bus-diagnosztikai naplók](../service-bus-messaging/service-bus-diagnostic-logs.md).  |
| Adatsík naplózása és naplózása| Nem |  |

### <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció kezelésével kapcsolatos támogatás (versioning konfiguráció stb.)| Igen | Erőforrás-szolgáltató versioning keresztül támogatja a [Azure Resource Manager API](/rest/api/resources/).|


## <a name="azure-service-bus-relayservice-bus-relayservice-bus-relay-security-attributesmd"></a>[Az Azure Service Bus Relay használatával](../service-bus-relay/service-bus-relay-security-attributes.md)

### <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban:<ul><li>Kiszolgálóoldali titkosítás</li><li>Kiszolgálóoldali titkosítás a felhasználó által kezelt kulcsok</li><li>Más titkosítási funkciók (például az ügyféloldali, mindig titkosított, stb.)</ul>|  – | Relay egy web socket, és nem marad meg adatokat. |
| Titkosítás az átvitel során:<ul><li>Express route-titkosítás</li><li>A VNet-titkosítás</li><li>Hálózatok titkosítása</ul>| Igen | Szolgáltatás szükséges a TLS. |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| Nem | Csak a Microsoft a TLS-tanúsítványokat használ.  |
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| – | |
| Titkosított API-hívások| Igen | HTTPS. |

### <a name="network-segmentation"></a>Hálózati szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatási végpont támogatás| Nem |  |
| Hálózatelkülönítés és támogatási optimalizálóként működik| Nem |  |
| Kényszerített bújtatás támogatása| – | -Továbbító egy olyan, a TLS-alagút  |

### <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Igen | |

### <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | Via SAS. |
| Engedélyezés|  Igen | Via SAS. |


### <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő és a felügyeleti sík naplózási és naplózása| Igen | Keresztül [az Azure Resource Manager](../azure-resource-manager/index.yml). |
| Adatsík naplózása és naplózása| Igen | Kapcsolat sikeres / sikertelen és a hibák és a naplóba.  |

### <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció kezelésével kapcsolatos támogatás (versioning konfiguráció stb.)| Igen | Keresztül [az Azure Resource Manager](../azure-resource-manager/index.yml).|


## <a name="azure-service-fabricservice-fabricservice-fabric-security-attributesmd"></a>[Azure Service Fabric](../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban:<ul><li>Kiszolgálóoldali titkosítás</li><li>Kiszolgálóoldali titkosítás a felhasználó által kezelt kulcsok</li><li>Más titkosítási funkciók (például az ügyféloldali, mindig titkosított, stb.)</ul>| Igen | Az ügyfél a tulajdonosa a fürt és a virtuális gép (VM) méretezési, a fürt épül. A virtuálisgép-méretezési csoportot az Azure disk encryption is engedélyezhetők. |
| Titkosítás az átvitel során:<ul><li>Express route-titkosítás</li><li>A VNet-titkosítás</li><li>Hálózatok titkosítása</ul>| Igen |  |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| Igen | Az ügyfél a tulajdonosa a fürt és a virtuális gép (VM) méretezési, a fürt épül. A virtuálisgép-méretezési csoportot az Azure disk encryption is engedélyezhetők. |
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| – |  |
| Titkosított API-hívások| Igen | Service Fabric API-hívások Azure Resource Manageren keresztül történik. Egy érvényes JSON webes jogkivonat (JWT) megadása kötelező. |

### <a name="network-segmentation"></a>Hálózati szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatási végpont támogatás| Igen |  |
| VNet-injektálási támogatás| Igen |  |
| Hálózatelkülönítés és támogatási optimalizálóként működik| Igen | Hálózati biztonsági csoportok (NSG) használatával. |
| Kényszerített bújtatás támogatása| Igen | Azure-hálózatok biztosítják a kényszerített bújtatás. |

### <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Igen | Figyelés és külső támogatást az Azure használatával. |

### <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | Hitelesítés az Azure Active Directoryn keresztül. |
| Engedélyezés| Igen | Identitás és hozzáférés-kezelés (IAM) keresztül SFRP-hívásokhoz. Közvetlenül a fürt végpontja hívások két szerepkör támogatja: Felhasználó és rendszergazda. Az ügyfél leképezheti az API-k bármelyik szerepkörhöz. |

### <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő és a felügyeleti sík naplózási és naplózása| Igen | Az összes vezérlési síkjával végzett műveletek haladjon végig a naplózási és jóváhagyási folyamatok. |
| Adatsík naplózása és naplózása| – | Ügyfél a tulajdonosa a fürtöt.  |

### <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció kezelésével kapcsolatos támogatás (versioning konfiguráció stb.)| Igen | A szolgáltatás konfigurációs, verzióval ellátott és üzembe helyezett üzembe az Azure használatával. A kód (alkalmazás- és futásidejű) rendszerverzióval ellátott Azure Build használatával.
 |

## <a name="azure-sql-databasesql-databasesql-database-security-attributesmd"></a>[Azure SQL Database](../sql-database/sql-database-security-attributes.md)

### <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban:<ul><li>Kiszolgálóoldali titkosítás</li><li>Kiszolgálóoldali titkosítás a felhasználó által kezelt kulcsok</li><li>Más titkosítási funkciók (például az ügyféloldali, mindig titkosított, stb.)</ul>| Igen | A továbbiakban "titkosítási-használatban", a cikkben leírtak szerint [Always Encrypted](../sql-database/sql-database-always-encrypted.md). Szolgáltatásoldali titkosítás [transzparens adattitkosítás](../sql-database/transparent-data-encryption-azure-sql.md) (TDE).|
| Titkosítás az átvitel során:<ul><li>Az ExpressRoute-titkosítás</li><li>A VNet-titkosítás</li><li>Hálózatok titkosítása</ul>| Igen | HTTPS-en keresztül. |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| Igen | Szolgáltatás által kezelt, mind az ügyfél által felügyelt kulcs kezelése érhetők el (az utóbbi keresztül [Azure Key Vault](../key-vault/index.yml). |
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| Igen | Keresztül [Always Encrypted](../sql-database/sql-database-always-encrypted.md). |
| Titkosított API-hívások| Igen | HTTPS-/ SSL segítségével. |

### <a name="network-segmentation"></a>Hálózati szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatási végpont támogatás| Igen | Érvényes [önálló adatbázis](../sql-database/sql-database-single-index.yml) csak. |
| VNet-injektálási támogatás| Igen | Érvényes [felügyelt példány](../sql-database/sql-database-managed-instance.md) csak. |
| Hálózatelkülönítés és támogatási optimalizálóként működik| Igen | A tűzfal, mindkét adatbázis - és kiszolgálói szintű; a hálózatelkülönítés [felügyelt példány](../sql-database/sql-database-managed-instance.md) csak |
| Kényszerített bújtatás támogatása| Igen | [felügyelt példány](../sql-database/sql-database-managed-instance.md) keresztül [Azure ExpressRoute](../expressroute/index.yml) VPN |

### <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Igen | A külső SIEM megoldások az Impervától (SecureSphere) is támogatják, keresztül [Azure Event Hubs](../event-hubs/index.yml) integration via [SQL audit](../sql-database/sql-database-auditing.md). |

### <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | Azure Active Directory. |
| Engedélyezés| Igen |  |


### <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő és a felügyeleti sík naplózási és naplózása| Igen | Igen, csak bizonyos események. |
| Adatsík naplózása és naplózása | Igen | Keresztül [SQL audit](../sql-database/sql-database-auditing.md). |

### <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció kezelésével kapcsolatos támogatás (versioning konfiguráció stb.)| Nem  | | 

### <a name="additional-security-attributes-for-sql-database"></a>További biztonsági attribútumok SQL Database-hez

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Megelőző: a biztonságirés-értékelési | Igen | Lásd: [SQL-sebezhetőségi felmérés szolgáltatás segít azonosítani az adatbázis biztonsági rések](../sql-database/sql-vulnerability-assessment.md). |
| Megelőző: az adatfelderítés és besorolás  | Igen | Lásd: [Azure SQL Database és az SQL Data Warehouse adatfelderítés és besorolás](../sql-database/sql-database-data-discovery-and-classification.md). |
| Észlelés: fenyegetések észlelése | Igen | Lásd: [komplex veszélyforrások elleni védelem az Azure SQL Database](../sql-database/sql-database-threat-detection-overview.md). |

## <a name="azure-storagestoragecommonstorage-security-attributesmd"></a>[Azure Storage](../storage/common/storage-security-attributes.md)

### <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban:<ul><li>Kiszolgálóoldali titkosítás</li><li>Kiszolgálóoldali titkosítás a felhasználó által kezelt kulcsok</li><li>Más titkosítási funkciók (például az ügyféloldali, mindig titkosított, stb.)</ul>| Igen |  |
| Titkosítás az átvitel során:<ul><li>Express route-titkosítás</li><li>A VNet-titkosítás</li><li>Hálózatok titkosítása</ul>| Igen | Standard szintű HTTPS/TLS mechanizmusokat támogatja.  Felhasználók is titkosíthatja adatokat, mielőtt azt a szolgáltatás. |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| Igen | Lásd: [ügyfél által kezelt kulcsok használata az Azure Key Vaultban a Storage Service Encryption](../storage/common/storage-service-encryption-customer-managed-keys.md).|
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| – |  |
| Titkosított API-hívások| Igen |  |

### <a name="network-segmentation"></a>Hálózati szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatási végpont támogatás| Igen |  |
| VNet-injektálási támogatás| – |  |
| Hálózatelkülönítés és támogatási optimalizálóként működik| Igen | |
| Kényszerített bújtatás támogatása| – |  |

### <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Igen | Az Azure Monitor metrikák elérhető, naplózza a kezdeti előzetes verzió |

### <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | Az Azure Active Directory, a megosztott kulcsot, a megosztott hozzáférési jogkivonatot. |
| Engedélyezés| Igen | Engedélyezés a következővel RBAC, a POSIX ACL-EK és a SAS-tokeneket támogatja |


### <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő és a felügyeleti sík naplózási és naplózása | Igen | Az Azure Resource Manager-tevékenységnapló |
| Adatsík naplózása és naplózása| Igen | Diagnosztikai naplók szolgáltatás és az Azure Monitor Naplózás kezdési előzetes verzió  |

### <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció kezelésével kapcsolatos támogatás (versioning konfiguráció stb.)| Igen | Támogatja az erőforrás-szolgáltató versioning Azure Resource Manager API-kon keresztül |