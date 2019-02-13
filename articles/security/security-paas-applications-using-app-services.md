---
title: PaaS web- és mobilalkalmazások az Azure App Service használatával védelme |} A Microsoft Docs
description: 'Ismerje meg az Azure App Service-biztonság ajánlott eljárások a PaaS webes és mobilalkalmazások védelme. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: terrylan
ms.openlocfilehash: 6e5034d0ff8f14a9fc381f6fd1a214a91ad4d1ed
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56107973"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-app-service"></a>PaaS web- és mobilalkalmazások az Azure App Service használatával biztonságossá tételének ajánlott eljárásait

Ebben a cikkben bemutatjuk, gyűjteménye [Azure App Service](../app-service/overview.md) kapcsolódó ajánlott biztonsági eljárások a PaaS webes és mobilalkalmazások védelme. Ajánlott eljárások az funkciót az Azure-ral és a az ügyfelek, például a saját maga származik.

Az Azure App Service-platform--szolgáltatásként (PaaS) ajánlat, amely lehetővé teszi webes és mobilalkalmazásokat bármilyen platformra vagy eszközre létrehozását, és bárhol kapcsolódhatnak az adatokhoz, a felhőben vagy helyszíni. App Service-ben a webes és mobil funkciókkal, amelyeket a rendszer korábban külön-külön tartalmazott Azure-webhelyek és Azure Mobile Services tartalmazza. Ezenkívül új lehetőségek is elérhetők az üzleti folyamatok automatizálásához és felhőalapú API-k üzemeltetéséhez. Egyetlen integrált szolgáltatásként az App Service lehetőségek gazdag tárházaként segíti a lehetőségeket a webes, mobil és integrációs feladatokhoz.

## <a name="authenticate-through-azure-active-directory-ad"></a>Hitelesítés az Azure Active Directory (AD) révén
App Service-ben az identitásszolgáltató az OAuth 2.0 szolgáltatást biztosít. OAuth 2.0 ügyfél fejlesztői egyszerűség művelet során gondoskodik a webalkalmazások, asztali alkalmazások és mobiltelefonon adott engedélyezési folyamatok tárgyalja. Az Azure AD OAuth 2.0 használja ahhoz, hogy engedélyezze a hozzáférést mobil- és webalkalmazásokat. További tudnivalókért lásd: [hitelesítése és engedélyezése Azure App Service-ben](../app-service/overview-authentication-authorization.md).

## <a name="restrict-access-based-on-role"></a>A szerepkör alapú hozzáférés korlátozása 
Hozzáférés korlátozása elengedhetetlen, szervezetek által adatok elérésére vonatkozó biztonsági szabályzatok kikényszerítéséhez. Szerepköralapú hozzáférés-vezérlés (RBAC) segítségével engedélyek hozzárendelése a felhasználók, csoportok és alkalmazások egy bizonyos hatókörben kell tudnia és a legalacsonyabb jogosultsági biztonsági alapelveket például. Felhasználók alkalmazásokhoz való hozzáférésének engedélyezésére vonatkozó további tudnivalókért lásd: [Mi a szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md).

## <a name="protect-your-keys"></a>Ön kulcsainak védelmét
Nem számít, hogy milyen jó van a biztonság az előfizetési kulcsok elvesztése esetén. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. A Key Vault titkosíthatók az kulcsok és titkos kulcsokat (például hitelesítési kulcsokat, a tárfiók kulcsait, az adattitkosítási kulcsokat. PFX-fájlok és jelszavak) hardveres biztonsági modulokban (HSM) által védett kulcsok használatával. A még nagyobb biztonság érdekében lehetőség van arra is, hogy kulcsokat importáljon és generáljon a hardveres biztonsági modulokban. A Key Vault használatával kezelheti a TLS-tanúsítványok az automatikus megújítás. Lásd: [Mi az Azure Key Vault](../key-vault/key-vault-whatis.md) további. 

## <a name="restrict-incoming-source-ip-addresses"></a>Bejövő forrás IP-címek korlátozása
[App Service Environment-környezetek](../app-service/environment/intro.md) rendelkezik, amely segítséget nyújt a bejövő forrás IP-címek a hálózati biztonsági csoportok (NSG-k) korlátozása virtuális hálózati integráció funkcióval. Ha ismeri az Azure-beli virtuális hálózatok (Vnetek), akkor egy olyan funkció, amely lehetővé teszi, hogy ki férhet hozzá az internet, az irányítható hálózatban helyezze számos, az Azure-erőforrások. További tudnivalókért lásd: [az alkalmazás integrálása az Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md).

## <a name="next-steps"></a>További lépések
Ebben a cikkben megismerkedhetett a App Service-ben ajánlott biztonsági eljárások a PaaS webes és mobilalkalmazások védelme gyűjteménye. A PaaS üzemelő példányok védelmével kapcsolatos további tudnivalókért lásd:

- [PaaS-környezetek védelme](security-paas-deployments.md)
- [Az Azure PaaS-adatbázisok védelme](security-paas-applications-using-sql.md)
