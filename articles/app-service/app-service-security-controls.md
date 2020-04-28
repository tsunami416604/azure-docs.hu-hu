---
title: Biztonsági vezérlők
description: Keresse meg a szervezete Azure App Service kiértékeléséhez szükséges biztonsági ellenőrzési ellenőrzőlistát.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 2586821c4c48f809efb5408c3cdae5e42e3b3fcf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74671451"
---
# <a name="security-controls-for-azure-app-service"></a>A Azure App Service biztonsági vezérlői

Ez a cikk a Azure App Service beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések | Dokumentáció
|---|---|--|
| Szolgáltatás végpontjának támogatása| Igen | App Service számára elérhető.| [Hozzáférési korlátozások Azure App Service](app-service-ip-restrictions.md)
| VNet-befecskendezés támogatása| Igen | App Service környezetek a App Service privát implementációi, amelyek egyetlen ügyfél számára lettek befecskendezve az ügyfél virtuális hálózatába. | [Az App Service Environment bemutatása](environment/intro.md)
| Hálózati elkülönítés és tűzfalak támogatása| Igen | A App Service nyilvános, több-bérlős változatához az ügyfelek konfigurálhatják a hálózati ACL-eket (IP-korlátozásokat), hogy zárolják az engedélyezett bejövő forgalmat.  App Service környezetek közvetlenül a virtuális hálózatokra vannak telepítve, így a NSG-mel is biztonságossá teheti őket. | [Hozzáférési korlátozások Azure App Service](app-service-ip-restrictions.md)
| Kényszerített bújtatás támogatása| Igen | App Service környezetek telepíthetők az ügyfél virtuális hálózatára, ahol a kényszerített bújtatás konfigurálva van. | [Az App Service-környezet konfigurálása kényszerített bújtatással](environment/forced-tunnel-support.md)

## <a name="monitoring--logging"></a>& naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések | Dokumentáció
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | A App Service a Application Insights (teljes .NET-keretrendszer, .NET Core, Java és Node. JS) támogató nyelvekhez integrálható Application Insightsokkal.  Lásd: [Azure app Service teljesítményének figyelése](../azure-monitor/app/azure-web-apps.md). A App Service az alkalmazás metrikáit is elküldi a Azure Monitorba. | [Alkalmazások figyelése Azure App Service](web-sites-monitor.md)
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | App Service objektumokon végrehajtott összes felügyeleti művelet [Azure Resource Manageron](../azure-resource-manager/index.yml)keresztül történik. Ezeknek a műveleteknek a korábbi naplói a Portálon és a CLI-n keresztül is elérhetők. | [Erőforrás-szolgáltatói műveletek Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftweb) [az az monitor Activity-log](/cli/azure/monitor/activity-log)
| Adatsíkok naplózása és naplózása | Nem | A App Service adatsíkja egy távoli fájlmegosztás, amely az ügyfél telepített webhely-tartalmát tartalmazza.  A távoli fájlmegosztás naplózása nem történik meg. |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |  Dokumentáció
|---|---|--|
| Hitelesítés| Igen | Az ügyfelek olyan App Service alkalmazásokat hozhatnak létre, amelyek automatikusan integrálva vannak a [Azure Active Directory (Azure ad)](../active-directory/index.yml) és az egyéb OAuth kompatibilis identitás-szolgáltatókkal az App Service eszközökhöz való felügyeleti hozzáféréshez, az összes hozzáférést az Azure ad hitelesített rendszerbiztonsági és Azure Resource Manager RBAC-szerepkörök kombinációja vezérli. | [Hitelesítés és engedélyezés az Azure App Service-ben](overview-authentication-authorization.md)
| Engedélyezés| Igen | App Service eszközök felügyeletéhez az összes hozzáférés az Azure AD-beli hitelesített rendszerbiztonsági tag és a Azure Resource Manager RBAC-szerepkörök kombinációjával vezérelhető.  | [Hitelesítés és engedélyezés az Azure App Service-ben](overview-authentication-authorization.md)

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések | Dokumentáció
|---|---|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok | Igen | A webhely fájljának tartalmát az Azure Storage tárolja, amely automatikusan titkosítja a tartalmat a nyugalmi állapotban. <br><br>Az ügyfél által megadott titkos kódok inaktívak. A titkos kulcsok titkosítása App Service konfigurációs adatbázisokban történik.<br><br>A helyileg csatlakoztatott lemezek opcionálisan használhatók a webhelyek (D:\Local és% TMP%) ideiglenes tárolóként. A helyileg csatlakoztatott lemezek nincsenek titkosítva a nyugalmi állapotban. | [Azure Storage-titkosítás a REST-adatokhoz](../storage/common/storage-service-encryption.md)
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | Igen | Az ügyfelek dönthetnek úgy, hogy az alkalmazás-titkokat a Key Vaultban tárolják, és futásidőben lekérik őket. | [Key Vault referenciák használata App Service és Azure Functionshoz (előzetes verzió)](app-service-key-vault-references.md)
| Oszlop szintű titkosítás (Azure Data Services)| N/A | |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Igen | Az ügyfelek a HTTPS protokollal konfigurálhatják a bejövő forgalmat, és megkövetelhetik a webhelyek használatát.  | [Csak HTTPS-Azure app Service létrehozása](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/) (blogbejegyzés)
| Titkosított API-hívások| Igen | App Service konfigurálására irányuló felügyeleti hívások a HTTPS-en keresztüli [Azure Resource Manager](../azure-resource-manager/index.yml) hívásokon keresztül történnek. |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések | Dokumentáció
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen | Felügyeleti műveletek esetében egy App Service konfiguráció állapota Azure Resource Manager sablonként exportálható, és az idő múlásával verzióban is elvégezhető. A futásidejű műveletek esetében az ügyfelek több különböző élő verziót is kezelhetnek az App Service üzembe helyezési pontok funkció használatával. | 

## <a name="next-steps"></a>További lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../security/fundamentals/security-controls.md).
