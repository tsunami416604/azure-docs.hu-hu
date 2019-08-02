---
title: Azure App Service biztonsági attribútumai
description: A Azure App Service értékelésére szolgáló biztonsági attribútumok ellenőrzőlistája
services: app-service
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: 299262610c027529749840720f46d6dc97a07b21
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442271"
---
# <a name="security-attributes-for-azure-app-service"></a>Azure App Service biztonsági attribútumai

Ez a cikk a Azure App Service beépített biztonsági attribútumokat dokumentálja.

[!INCLUDE [Security attributes header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például kiszolgálóoldali titkosítás, ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás és egyéb titkosítási funkciók) | Igen | A webhely fájljának tartalmát az Azure Storage tárolja, amely automatikusan titkosítja a tartalmat a nyugalmi állapotban. Lásd: [Az Azure Storage titkosítása a nyugalmi](../storage/common/storage-service-encryption.md)állapotban lévő adatokhoz.<br><br>Az ügyfél által megadott titkos kódok inaktívak. A titkos kulcsok titkosítása App Service konfigurációs adatbázisokban történik.<br><br>A helyileg csatlakoztatott lemezek opcionálisan használhatók a webhelyek (D:\Local és% TMP%) ideiglenes tárolóként. A helyileg csatlakoztatott lemezek nincsenek titkosítva a nyugalmi állapotban. |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Igen | Az ügyfelek a HTTPS protokollal konfigurálhatják a bejövő forgalmat, és megkövetelhetik a webhelyek használatát. Tekintse meg a blogbejegyzést [, hogyan teheti meg a csak Azure app Service HTTPS-t](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/). |
| Titkosítási kulcsok kezelését (CMK, BYOK stb.)| Igen | Az ügyfelek dönthetnek úgy, hogy az alkalmazás-titkokat a Key Vaultban tárolják, és futásidőben lekérik őket. Lásd: [Key Vault referenciák használata app Service és Azure functions (előzetes verzió)](app-service-key-vault-references.md).|
| Oszlop szintű titkosítás (Azure Data Services)| – | |
| Titkosított API-hívások| Igen | App Service konfigurálására irányuló felügyeleti hívások a HTTPS-en keresztüli [Azure Resource Manager](../azure-resource-manager/index.yml) hívásokon keresztül történnek. |

## <a name="network-segmentation"></a>Hálózati szegmentálás

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Igen | Jelenleg előzetes verzióban érhető el a App Service. Lásd: [Azure app Service hozzáférési korlátozások](app-service-ip-restrictions.md). |
| VNet-befecskendezés támogatása| Igen | App Service környezetek a App Service privát implementációi, amelyek egyetlen ügyfél számára lettek befecskendezve az ügyfél virtuális hálózatába. Lásd: [Bevezetés a app Service környezetbe](environment/intro.md). |
| Hálózati elkülönítés és tűzfalak támogatása| Igen | A App Service nyilvános, több-bérlős változatához az ügyfelek konfigurálhatják a hálózati ACL-eket (IP-korlátozásokat), hogy zárolják az engedélyezett bejövő forgalmat.  Lásd: [Azure app Service hozzáférési korlátozások](app-service-ip-restrictions.md).  App Service környezetek közvetlenül a virtuális hálózatokra vannak telepítve, így a NSG-mel is biztonságossá teheti őket. |
| Kényszerített bújtatás támogatása| Igen | App Service környezetek telepíthetők az ügyfél virtuális hálózatára, ahol a kényszerített bújtatás konfigurálva van. Az ügyfeleknek követniük kell a [app Service Environment konfigurálása kényszerített bújtatással](environment/forced-tunnel-support.md)című témakör utasításait. |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | A App Service a Application Insights (teljes .NET-keretrendszer, .NET Core, Java és Node. JS) támogató nyelvekhez integrálható Application Insightsokkal.  Lásd: [Azure app Service teljesítményének figyelése](../azure-monitor/app/azure-web-apps.md). A App Service az alkalmazás metrikáit is elküldi a Azure Monitorba. Lásd: [alkalmazások figyelése Azure app Serviceban](web-sites-monitor.md). |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen | Az ügyfelek olyan App Service alkalmazásokat hozhatnak létre, amelyek automatikusan integrálva vannak a [Azure Active Directory (Azure ad)](../active-directory/index.yml) és más OAuth kompatibilis identitás-szolgáltatókkal. Lásd: [hitelesítés és engedélyezés Azure app Serviceban](overview-authentication-authorization.md). App Service eszközök felügyeletéhez az összes hozzáférés az Azure AD-beli hitelesített rendszerbiztonsági tag és a Azure Resource Manager RBAC-szerepkörök kombinációjával vezérelhető. |
| Authorization| Igen | App Service eszközök felügyeletéhez az összes hozzáférés az Azure AD-beli hitelesített rendszerbiztonsági tag és a Azure Resource Manager RBAC-szerepkörök kombinációjával vezérelhető.  |


## <a name="audit-trail"></a>Naplózási nyomvonal

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | App Service objektumokon végrehajtott összes felügyeleti művelet Azure Resource Manageron [](../azure-resource-manager/index.yml)keresztül történik. Ezeknek a műveleteknek a korábbi naplói a Portálon és a CLI-n keresztül is elérhetők. Lásd: [Azure Resource Manager erőforrás-szolgáltatói műveletek](../role-based-access-control/resource-provider-operations.md#microsoftweb) és [az monitor Activity-log](/cli/azure/monitor/activity-log). |
| Adatsíkok naplózása és naplózása | Nem | A App Service adatsíkja egy távoli fájlmegosztás, amely az ügyfél telepített webhely-tartalmát tartalmazza.  A távoli fájlmegosztás naplózása nem történik meg. |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen | Felügyeleti műveletek esetében egy App Service konfiguráció állapota Azure Resource Manager sablonként exportálható, és az idő múlásával verzióban is elvégezhető. A futásidejű műveletek esetében az ügyfelek több különböző élő verziót is kezelhetnek az App Service üzembe helyezési pontok funkció használatával. | 

