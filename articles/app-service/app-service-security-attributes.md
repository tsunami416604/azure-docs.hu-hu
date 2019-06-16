---
title: Az Azure App Service biztonsági attribútumok
description: A biztonsági attribútumok értékeléséhez, az Azure App Service egy ellenőrzőlista
services: app-service
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: e38e1479c1e094f691e8f22f0a48f9342cae5cf3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66475075"
---
# <a name="security-attributes-for-azure-app-service"></a>Az Azure App Service biztonsági attribútumok

Ez a cikk a gyakori biztonsági attribútumok az Azure App Service beépített dokumentumok.

[!INCLUDE [Security attributes header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például a kiszolgálóoldali titkosítás, a kiszolgálóoldali titkosítást az ügyfél által felügyelt kulcsokat és más titkosítási szolgáltatások) | Igen | Azure Storage, amely automatikusan titkosítja az inaktív a tartalmat a webhely fájl tartalmat tárolja. Lásd: [inaktív adatok titkosítása az Azure Storage](../storage/common/storage-service-encryption.md).<br><br>A megadott ügyfél titkos kulcsok vannak titkosítása. A titkos kulcsok vannak titkosítva, míg az App Service-konfiguráció adatbázisokban tárolt inaktív.<br><br>Helyileg csatlakoztatott lemezek igény szerint használható ideiglenes tárolóként webhelyek (D:\local és a % TMP %). Helyileg csatlakoztatott lemezek inaktív nincsenek titkosítva. |
| Titkosítás az átvitel során (például az ExpressRoute-titkosítás, a virtuális hálózatok közötti titkosítás és a VNet – VNet titkosítási)| Igen | Ügyfelek konfigurálhatják a webhelyek igényelnek, és a bejövő forgalmat a HTTPS PROTOKOLLT használja. Tekintse meg a következő blogbejegyzésben: [biztosítása az Azure App Service csak HTTPS](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/). |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| Igen | Ügyfelek kiválaszthatják a titkos alkalmazáskulcsok tárolása a Key Vaultban, és kérheti le azokat a futásidőben. Lásd: [a Key Vault használata az App Service-ben és az Azure Functions (előzetes verzió) hivatkozik](app-service-key-vault-references.md).|
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| – | |
| Titkosított API-hívások| Igen | Felügyeleti hívások konfigurálásához az App Service-n keresztül történik [Azure Resource Manager](../azure-resource-manager/index.yml) hívások HTTPS-kapcsolaton keresztül. |

## <a name="network-segmentation"></a>Hálózati szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatási végpont támogatás| Igen | Jelenleg az App Service előzetes verzióban érhető el. Lásd: [korlátozza a hozzáférést az Azure App Service](app-service-ip-restrictions.md). |
| VNet-injektálási támogatás| Igen | App Service Environment-környezetek megvalósításai privát kártevő program férkőzik be egy ügyfél virtuális hálózatán egyetlen ügyfél számára dedikált App Service-ben. Lásd: [az App Service Environment bemutatása](environment/intro.md). |
| Hálózatelkülönítés és Firewalling támogatása| Igen | Az App Service-ben nyilvános több-bérlős változata a felhasználók beállíthatják a hálózati hozzáférés-vezérlési listák (IP-korlátozások) való zárolását, így az engedélyezett bejövő forgalom.  Lásd: [korlátozza a hozzáférést az Azure App Service](app-service-ip-restrictions.md).  App Service Environment-környezetek közvetlenül virtuális hálózatokra vannak telepítve, és ezáltal védve legyenek az NSG-ket. |
| Kényszerített bújtatás támogatása| Igen | App Service Environment-környezetek is telepíthető, ahol kényszerített bújtatás konfigurálása az ügyfél virtuális hálózatban. Kövesse az utasításokat kell [az App Service Environment konfigurálása kényszerített bújtatással](environment/forced-tunnel-support.md). |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Igen | Az App Service az Application Insights nyelveket, amelyek támogatják az Application Insights (teljes .NET-keretrendszert, .NET Core, Java és Node.JS) együttműködik.  Lásd: [figyelése az Azure App Service teljesítményének](../azure-monitor/app/azure-web-apps.md). App Service-ben az alkalmazásmetrikák is küld az Azure Monitor szolgáltatásba. Lásd: [alkalmazások figyelése az Azure App Service-ben](web-sites-monitor.md). |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | Ügyfeleink App Service-ben, amely automatikusan integrálható alkalmazásokat hozhat létre [Azure Active Directory (Azure AD)](../active-directory/index.yml) és más OAuth kompatibilis identitásszolgáltatókkal; lásd: [hitelesítés és engedélyezés Az Azure App Service](overview-authentication-authorization.md). Felügyeleti hozzáférés az App Service-eszközöket minden hozzáférés hitelesítése az Azure AD egyszerű és Azure Resource Manager RBAC-szerepkörök kombinációja vezérlik majd. |
| Engedélyezés| Igen | Felügyeleti hozzáférés az App Service-eszközöket minden hozzáférés hitelesítése az Azure AD egyszerű és Azure Resource Manager RBAC-szerepkörök kombinációja vezérlik majd.  |


## <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő és a felügyeleti sík naplózási és naplózása| Igen | App Service-ben az objektumokon végrehajtott összes felügyeleti műveletet keresztül történnek [Azure Resource Manager](../azure-resource-manager/index.yml). Korábbi műveletek érhetők el naplók a portálon és a parancssori felületén; Lásd: [Azure Resource Manager erőforrás-szolgáltatói műveletek](../role-based-access-control/resource-provider-operations.md#microsoftweb) és [az monitor tevékenységnapló](/cli/azure/monitor/activity-log). |
| Adatsík naplózása és naplózása | Nem | Az adatsík az App Service-ügyfél üzembe helyezett webhely tartalmát tartalmazó távoli fájlmegosztásban.  Nincs nem kerülnek naplózásra a távoli fájlmegosztásban. |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció kezelésével kapcsolatos támogatás (versioning konfiguráció stb.)| Igen | A felügyeleti műveletek esetében egy App Service-ben konfigurációs állapotát exportálhatók rendszerverzióval ellátott és Azure Resource Manager-sablonok idővel. Ügyfeleink a futásidejű műveletek egy alkalmazást az App Service üzembe helyezési pontok funkció használatával több különböző élő verzióit fenntartására. | 

