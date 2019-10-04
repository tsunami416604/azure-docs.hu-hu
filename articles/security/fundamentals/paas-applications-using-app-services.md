---
title: A Pásti web-és Mobile-alkalmazások biztonságossá tétele a Azure App Service használatával | Microsoft Docs
description: 'Ismerkedjen meg Azure App Service biztonsági bevált gyakorlattal a Pásti web-és Mobile-alkalmazások biztonságossá tételéhez. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2019
ms.author: terrylan
ms.openlocfilehash: 4967f6f7831f1f09a502b935342fcd752af72fce
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2019
ms.locfileid: "70999154"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Ajánlott eljárások a Pásti webes és mobil alkalmazások biztonságossá tételéhez Azure App Service használatával

Ebben a cikkben [Azure app Service](/azure/app-service/overview) biztonsági bevált eljárások gyűjteményét tárgyaljuk a Pásti web-és Mobile-alkalmazások biztonságossá tételéhez. Ezek az ajánlott eljárások az Azure tapasztalataiból és az ügyfelek, például saját tapasztalataiból származnak.

A Azure App Service egy szolgáltatásként nyújtott platform (Pásti), amely lehetővé teszi webes és mobil alkalmazások létrehozását bármilyen platformra vagy eszközre, és bárhol, a felhőben vagy a helyszínen csatlakozik az adatforrásokhoz. A App Service tartalmazza azokat a webes és mobil képességeket, amelyeket korábban az Azure websites és az Azure Mobile Services külön szállítottak el. Ezenkívül új lehetőségek is elérhetők az üzleti folyamatok automatizálásához és felhőalapú API-k üzemeltetéséhez. Egyetlen integrált szolgáltatásként a App Service számos lehetőséget kínál webes, mobil-és integrációs forgatókönyvek kialakítására.

## <a name="authenticate-through-azure-active-directory-ad"></a>Hitelesítés Azure Active Directory (AD) használatával
A App Service OAuth 2,0 szolgáltatást biztosít az identitás-szolgáltató számára. A OAuth 2,0 a webalkalmazások, asztali alkalmazások és mobiltelefonok speciális engedélyezési folyamatainak biztosítása mellett az ügyfelek fejlesztői egyszerűségére koncentrál. Az Azure AD OAuth 2,0-et használ a mobil-és webalkalmazásokhoz való hozzáférés engedélyezéséhez. További információ: [hitelesítés és engedélyezés Azure app Serviceban](../../app-service/overview-authentication-authorization.md).

## <a name="restrict-access-based-on-role"></a>Hozzáférés korlátozása a szerepkör alapján
A hozzáférés korlátozása elengedhetetlen azon szervezetek számára, akik biztonsági házirendeket kívánnak kikényszeríteni az adateléréshez. A szerepköralapú hozzáférés-vezérlés (RBAC) segítségével engedélyeket rendelhet hozzá a felhasználókhoz, csoportokhoz és alkalmazásokhoz egy bizonyos hatókörben, például a szükséges ismeretet és a legalacsonyabb szintű biztonsági alapelveket. Ha többet szeretne megtudni a felhasználók alkalmazásokhoz való hozzáférésének biztosításáról, tekintse meg a [Mi a szerepköralapú hozzáférés-vezérlés](/azure/role-based-access-control/overview)című témakört.

## <a name="protect-your-keys"></a>A kulcsok megóvása
Nem számít, hogy mennyire jó a biztonsága, ha elveszíti az előfizetési kulcsokat. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. A Key Vault segítségével titkosíthatja a kulcsokat és a titkos kulcsokat (például a hitelesítési kulcsokat, a Storage-fiók kulcsait, az adattitkosítási kulcsokat). PFX-fájlok és jelszavak) a hardveres biztonsági modulok (HSM-EK) által védett kulcsok használatával. A még nagyobb biztonság érdekében lehetőség van arra is, hogy kulcsokat importáljon és generáljon a hardveres biztonsági modulokban. A TLS-tanúsítványok automatikus megújítással történő kezeléséhez Key Vault is használhatja. További információ: [Mi a Azure Key Vault](../../key-vault/key-vault-overview.md) .

## <a name="restrict-incoming-source-ip-addresses"></a>Bejövő forrás IP-címeinek korlátozása
[App Service környezetek](../../app-service/environment/intro.md) virtuális hálózati integrációs funkciója segítségével a bejövő forrás IP-címeket hálózati biztonsági csoportokkal (NSG) korlátozhatja. Ha nem ismeri az Azure Virtual Networks (virtuális hálózatok) szolgáltatást, ez egy olyan képesség, amely lehetővé teszi, hogy számos Azure-erőforrást egy nem internetes, átirányítható hálózaton helyezzen el, amely a hozzáférését szabályozza. További információ: [az alkalmazás integrálása Azure-Virtual Networkokkal](../../app-service/web-sites-integrate-with-vnet.md).

A Windows App Service esetében a web. config fájl konfigurálásával dinamikusan is korlátozhatja az IP-címeket. További információ: [dinamikus IP-biztonság](/iis/configuration/system.webServer/security/dynamicIpSecurity/).


## <a name="next-steps"></a>További lépések
Ez a cikk App Service biztonsági bevált eljárások gyűjteményét mutatta be a Pásti web-és Mobile-alkalmazások biztonságossá tételéhez. További információ a Pásti-telepítések biztonságossá tételéről:

- [PaaS-környezetek védelme](paas-deployments.md)
- [A Péter-adatbázisok védelme az Azure-ban](paas-applications-using-sql.md)
