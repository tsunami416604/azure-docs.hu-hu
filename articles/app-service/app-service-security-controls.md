---
title: Biztonsági vezérlők
description: Keresse meg a biztonsági vezérlők ellenőrzőlistáját az Azure App Service kiértékeléséhez a szervezet számára.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 2586821c4c48f809efb5408c3cdae5e42e3b3fcf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671451"
---
# <a name="security-controls-for-azure-app-service"></a>Az Azure App Service biztonsági vezérlői

Ez a cikk az Azure App Service beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések | Dokumentáció
|---|---|--|
| A szolgáltatás végpontjának támogatása| Igen | Elérhető az App Service számára.| [Az Azure App Service hozzáférési korlátozásai](app-service-ip-restrictions.md)
| A VNet injekciózás támogatása| Igen | Az App Service-környezetek az App Service privát implementációi, amelyek egyetlen ügyfélnek vannak kiosztva, és amelyeket az ügyfél virtuális hálózatába injektálnak. | [Az App Service Environment bemutatása](environment/intro.md)
| Hálózati elkülönítés és tűzfaltámogatás| Igen | Az App Service nyilvános több-bérlős változata esetén az ügyfelek konfigurálhatják a hálózati ACL-eket (IP-korlátozásokat) az engedélyezett bejövő forgalom zárolásához.  Az App Service-környezetek közvetlenül a virtuális hálózatokba vannak telepítve, és így az NSG-kkel biztosíthatók. | [Az Azure App Service hozzáférési korlátozásai](app-service-ip-restrictions.md)
| Kényszerített bújtatástámogatása| Igen | Az App Service-környezetek telepíthetők az ügyfél virtuális hálózatába, ahol a kényszerített bújtatás konfigurálva van. | [Az App Service-környezet konfigurálása kényszerített bújtatással](environment/forced-tunnel-support.md)

## <a name="monitoring--logging"></a>Naplózás & figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések | Dokumentáció
|---|---|--|
| Azure figyelési támogatás (Naplóelemzés, Alkalmazáselemzések stb.)| Igen | Az App Service integrálható az Application Insights alkalmazáselemzési adatokat támogató nyelvekhez (Teljes .  Lásd: [Az Azure App Service teljesítményének figyelése.](../azure-monitor/app/azure-web-apps.md) Az App Service alkalmazásmetrikákat is küld az Azure Monitorba. | [Alkalmazások figyelése az Azure App Service-ben](web-sites-monitor.md)
| Vezérlő és felügyeleti sík naplózása és naplózása| Igen | Az App Service-objektumokon végrehajtott összes felügyeleti művelet az [Azure Resource Manageren](../azure-resource-manager/index.yml)keresztül történik. Ezek a műveletek előzménynaplói mind a portálon, mind a CLI-n keresztül elérhetők. | [Az Azure Resource Manager erőforrás-szolgáltató műveletei](../role-based-access-control/resource-provider-operations.md#microsoftweb), [az figyelési tevékenységnapló](/cli/azure/monitor/activity-log)
| Adatsík naplózása és naplózása | Nem | Az App Service adatsíkja egy távoli fájlmegosztás, amely az ügyfél által telepített webhely tartalmat tartalmazza.  A távoli fájlmegosztás nem naplózva. |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |  Dokumentáció
|---|---|--|
| Hitelesítés| Igen | Az ügyfelek olyan alkalmazásokat hozhatnak létre az App Service szolgáltatásban, amelyek automatikusan integrálódnak [az Azure Active Directoryval (Azure AD)](../active-directory/index.yml) és más OAuth-kompatibilis identitásszolgáltatókkal Az App Service-eszközökhöz való felügyeleti hozzáféréshez minden hozzáférést az Azure AD által hitelesített egyszerű és az Azure Resource Manager RBAC szerepkörök kombinációja szabályoz. | [Hitelesítés és engedélyezés az Azure App Service-ben](overview-authentication-authorization.md)
| Engedélyezés| Igen | Az App Service-eszközökhöz való felügyeleti hozzáférés hez az Azure AD által hitelesített egyszerű és az Azure Resource Manager RBAC szerepkörök kombinációja szabályozza az összes hozzáférést.  | [Hitelesítés és engedélyezés az Azure App Service-ben](overview-authentication-authorization.md)

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések | Dokumentáció
|---|---|--|
| Kiszolgálóoldali titkosítás inaktív állapotban: Microsoft által felügyelt kulcsok | Igen | A webhelyfájl tartalma az Azure Storage-ban tárolódik, amely automatikusan titkosítja a nyugalmi helyen lévő tartalmat. <br><br>Az ügyfél által megadott titkok titkosítva vannak. A titkos kulcsok titkosítva vannak az App Service konfigurációs adatbázisaiban.<br><br>A helyileg csatlakoztatott lemezeket a webhelyek (D:\local és %TMP%) ideiglenes tárolóként is használhatják. A helyileg csatlakoztatott lemezek nem lesznek titkosítva az inkett helyen. | [Az Azure Storage titkosítása az inaktív adatokhoz](../storage/common/storage-service-encryption.md)
| Kiszolgálóoldali titkosítás inaktív állapotban: ügyfél által felügyelt kulcsok (BYOK) | Igen | Az ügyfelek dönthetnek úgy, hogy az alkalmazástitkos kulcsokat a Key Vaultban tárolják, és futásidőben lekérik őket. | [Key Vault-hivatkozások használata az App Service és az Azure Functions szolgáltatáshoz (előzetes verzió)](app-service-key-vault-references.md)
| Oszlopszintű titkosítás (Azure Data Services)| N/A | |
| Titkosítás átvitel közben (például ExpressRoute-titkosítás, virtuális hálózat titkosítása és Virtuálishálózati titkosítás)| Igen | Az ügyfelek beállíthatják, hogy a webhelyek https-t igényeljenek, és https-t használjanak a bejövő forgalomhoz.  | [Csak az Azure App Service HTTPS létrehozása](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/) (blogbejegyzés)
| TITKOSÍTOTT API-hívások| Igen | Az App Service konfigurálásához az App Service konfigurálásához az [Azure Resource Manager-hívások](../azure-resource-manager/index.yml) HTTPS-en keresztül történnek. |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések | Dokumentáció
|---|---|--|
| Konfigurációkezelés támogatása (a konfiguráció verziószámozása stb.)| Igen | Felügyeleti műveletek esetén az App Service-konfiguráció állapota exportálható Azure Resource Manager-sablonként, és idővel verziószámmal verziózható. A futásidejű műveletek hez az ügyfelek az App Service központi telepítési bővítőhely-szolgáltatásával az alkalmazás több különböző élő verzióját is fenntarthatják. | 

## <a name="next-steps"></a>További lépések

- További információ az [Azure-szolgáltatások beépített biztonsági vezérlőiről.](../security/fundamentals/security-controls.md)
