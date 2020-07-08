---
title: A Azure App Service biztonsági vezérlői
description: Keresse meg a szervezete Azure App Service kiértékeléséhez szükséges biztonsági ellenőrzési ellenőrzőlistát.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 9b0c336d9d9c463a7ed7acf42eaf292ecce4f1bb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85831148"
---
# <a name="security-controls-for-azure-app-service"></a>A Azure App Service biztonsági vezérlői

Ez a cikk a Azure App Service beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek | Dokumentáció
|---|---|--|
| Szolgáltatás végpontjának támogatása| Yes | App Service számára elérhető.| [Hozzáférési korlátozások Azure App Service](app-service-ip-restrictions.md)
| VNet-befecskendezés támogatása| Yes | App Service környezetek a App Service privát implementációi, amelyek egyetlen ügyfél számára lettek befecskendezve az ügyfél virtuális hálózatába. | [Az App Service Environment bemutatása](environment/intro.md)
| Hálózati elkülönítés és tűzfalak támogatása| Yes | A App Service nyilvános, több-bérlős változatához az ügyfelek konfigurálhatják a hálózati ACL-eket (IP-korlátozásokat), hogy zárolják az engedélyezett bejövő forgalmat.  App Service környezetek közvetlenül a virtuális hálózatokra vannak telepítve, így a NSG-mel is biztonságossá teheti őket. | [Hozzáférési korlátozások Azure App Service](app-service-ip-restrictions.md)
| Kényszerített bújtatás támogatása| Yes | App Service környezetek telepíthetők az ügyfél virtuális hálózatára, ahol a kényszerített bújtatás konfigurálva van. | [Az App Service-környezet konfigurálása kényszerített bújtatással](environment/forced-tunnel-support.md)

## <a name="monitoring--logging"></a>& naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek | Dokumentáció
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Yes | A App Service a Application Insights (teljes .NET-keretrendszer, .NET Core, Java és Node.JS) támogató nyelvekhez integrálható Application Insightsokkal.  Lásd: [Azure app Service teljesítményének figyelése](../azure-monitor/app/azure-web-apps.md). A App Service az alkalmazás metrikáit is elküldi a Azure Monitorba. | [Alkalmazások figyelése Azure App Service](web-sites-monitor.md)
| Vezérlési és felügyeleti síkok naplózása és naplózása| Yes | App Service objektumokon végrehajtott összes felügyeleti művelet [Azure Resource Manageron](../azure-resource-manager/index.yml)keresztül történik. Ezeknek a műveleteknek a korábbi naplói a Portálon és a CLI-n keresztül is elérhetők. | [Erőforrás-szolgáltatói műveletek Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftweb) [az az monitor Activity-log](/cli/azure/monitor/activity-log)
| Adatsíkok naplózása és naplózása | No | A App Service adatsíkja egy távoli fájlmegosztás, amely az ügyfél telepített webhely-tartalmát tartalmazza.  A távoli fájlmegosztás naplózása nem történik meg. |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek |  Dokumentáció
|---|---|--|
| Hitelesítés| Yes | Az ügyfelek olyan App Service alkalmazásokat hozhatnak létre, amelyek automatikusan integrálva vannak a [Azure Active Directory (Azure ad)](../active-directory/index.yml) és más OAuth kompatibilis identitás-szolgáltatókkal a app Service eszközökhöz való felügyelethez, az összes hozzáférést az Azure ad hitelesített rendszerbiztonsági tag és az Azure RBAC kombinációja vezérli. | [Hitelesítés és engedélyezés az Azure App Service-ben](overview-authentication-authorization.md)
| Engedélyezés| Yes | App Service eszközök kezeléséhez az összes hozzáférést az Azure AD-beli hitelesített rendszerbiztonsági tag és az Azure RBAC kombinációja vezérli.  | [Hitelesítés és engedélyezés az Azure App Service-ben](overview-authentication-authorization.md)

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek | Dokumentáció
|---|---|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok | Yes | A webhely fájljának tartalmát az Azure Storage tárolja, amely automatikusan titkosítja a tartalmat a nyugalmi állapotban. <br><br>Az ügyfél által megadott titkos kódok inaktívak. A titkos kulcsok titkosítása App Service konfigurációs adatbázisokban történik.<br><br>A helyileg csatlakoztatott lemezek opcionálisan használhatók a webhelyek (D:\Local és% TMP%) ideiglenes tárolóként. A helyileg csatlakoztatott lemezek nincsenek titkosítva a nyugalmi állapotban. | [Inaktív adatok Azure Storage-titkosítása](../storage/common/storage-service-encryption.md)
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | Yes | Az ügyfelek dönthetnek úgy, hogy az alkalmazás-titkokat a Key Vaultban tárolják, és futásidőben lekérik őket. | [Key Vault referenciák használata App Service és Azure Functionshoz (előzetes verzió)](app-service-key-vault-references.md)
| Oszlop szintű titkosítás (Azure Data Services)| N.A. | |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Yes | Az ügyfelek a HTTPS protokollal konfigurálhatják a bejövő forgalmat, és megkövetelhetik a webhelyek használatát.  | [Csak HTTPS-Azure app Service létrehozása](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/) (blogbejegyzés)
| Titkosított API-hívások| Yes | App Service konfigurálására irányuló felügyeleti hívások a HTTPS-en keresztüli [Azure Resource Manager](../azure-resource-manager/index.yml) hívásokon keresztül történnek. |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek | Dokumentáció
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Yes | Felügyeleti műveletek esetében egy App Service konfiguráció állapota Azure Resource Manager sablonként exportálható, és az idő múlásával verzióban is elvégezhető. A futásidejű műveletek esetében az ügyfelek több különböző élő verziót is kezelhetnek az App Service üzembe helyezési pontok funkció használatával. | 

## <a name="next-steps"></a>További lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../security/fundamentals/security-controls.md).
