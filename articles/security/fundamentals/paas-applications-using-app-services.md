---
title: A PaaS webes & mobilalkalmazások biztonságossá tétele
titleSuffix: Azure App Service
description: 'Ismerje meg az Azure App Service biztonsági gyakorlati tanácsait a PaaS-web- és mobilalkalmazások védelméhez. '
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
ms.openlocfilehash: c3f3c7fbaa043a03b70ab770c06e493716c70daf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77500285"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Gyakorlati tanácsok a PaaS-web- és mobilalkalmazások Azure App Service használatával történő védelméhez

Ebben a cikkben az [Azure App Service](/azure/app-service/overview) biztonsági gyakorlati tanácsok a PaaS-web- és mobilalkalmazások védelmére vonatkozó gyakorlati tanácsait tárgyaljuk. Ezek az ajánlott eljárások az Azure-ral kapcsolatos tapasztalatainkból és az önhöz hasonló ügyfelek tapasztalataiból származnak.

Az Azure App Service egy platformszolgáltatásként (PaaS) kínál, amely lehetővé teszi, hogy webes és mobilalkalmazásokat hozzon létre bármilyen platformra vagy eszközre, és csatlakozzon az adatokhoz bárhol, a felhőben vagy a helyszínen. Az App Service tartalmazza azokat a webes és mobilfunkciókat, amelyeket korábban külön szállítottak Azure-webhelyek ként és Az Azure Mobile Services szolgáltatásként. Ezenkívül új lehetőségek is elérhetők az üzleti folyamatok automatizálásához és felhőalapú API-k üzemeltetéséhez. Egyetlen integrált szolgáltatásként az App Service számos funkciót biztosít a webes, mobil- és integrációs forgatókönyvekhez.

## <a name="authenticate-through-azure-active-directory-ad"></a>Hitelesítés az Azure Active Directoryn (AD) keresztül
Az App Service egy OAuth 2.0 szolgáltatást biztosít az identitásszolgáltatószámára. Az OAuth 2.0 az ügyfélfejlesztők egyszerűségére összpontosít, miközben speciális engedélyezési folyamatokat biztosít a webes alkalmazásokhoz, asztali alkalmazásokhoz és mobiltelefonokhoz. Az Azure AD az OAuth 2.0-s használatával engedélyezi a hozzáférést a mobil- és webalkalmazásokhoz. További információ: [Hitelesítés és engedélyezés az Azure App Service szolgáltatásban.](../../app-service/overview-authentication-authorization.md)

## <a name="restrict-access-based-on-role"></a>Hozzáférés korlátozása szerepkör alapján
A hozzáférés korlátozása elengedhetetlen azon szervezetek számára, amelyek az adathozzáférésbiztonsági házirendjeit szeretnék érvényesíteni. A szerepköralapú hozzáférés-vezérlés (RBAC) segítségével engedélyeket rendelhet a felhasználókhoz, csoportokhoz és alkalmazásokhoz egy bizonyos hatókörben, például a szükséges ismeretés a legkisebb jogosultságbiztonsági elvek. Ha többet szeretne tudni arról, hogy mi ként biztosít hozzáférést a felhasználóknak az alkalmazásokhoz, olvassa el [a Mi a szerepköralapú hozzáférés-vezérlés.](/azure/role-based-access-control/overview)

## <a name="protect-your-keys"></a>A kulcsok védelme
Nem számít, mennyire jó a biztonság, ha elveszíti az előfizetési kulcsokat. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. A Key Vault segítségével titkosíthatja a kulcsokat és a titkos kulcsokat (például a hitelesítési kulcsokat, a tárfiók kulcsait, az adattitkosítási kulcsokat, a . PFX fájlok és jelszavak) hardveres biztonsági modulokkal (HSM) védett kulcsokkal. A még nagyobb biztonság érdekében lehetőség van arra is, hogy kulcsokat importáljon és generáljon a hardveres biztonsági modulokban. A Key Vault segítségével is kezelheti a TLS-tanúsítványokat automatikus megújítással. További információért olvassa el [az Azure Key Vault(Mi az Azure Key Vault)](../../key-vault/key-vault-overview.md) ( Mit ismer.

## <a name="restrict-incoming-source-ip-addresses"></a>Bejövő forrás IP-címeinek korlátozása
[Az App Service-környezetek](../../app-service/environment/intro.md) virtuális hálózati integrációs szolgáltatással rendelkezik, amely segít a bejövő forrás IP-címek hálózati biztonsági csoportokon (NSG)-n keresztül történő korlátozásában. Ha nem ismeri az Azure virtuális hálózatok (VNETs), ez a képesség, amely lehetővé teszi, hogy helyezze el az Azure-erőforrások egy nem internetes, irányítható hálózat, amely szabályozza a hozzáférést. További információ: [Az alkalmazás integrálása azure virtuális hálózattal.](../../app-service/web-sites-integrate-with-vnet.md)

A Windows App Service esetén az IP-címeket dinamikusan is korlátozhatja a web.config konfigurálásával. További információ: [Dinamikus IP-biztonság](/iis/configuration/system.webServer/security/dynamicIpSecurity/).


## <a name="next-steps"></a>További lépések
Ez a cikk bemutatta az App Service biztonsági gyakorlati tanácsok a PaaS webes és mobilalkalmazások védelmére. A PaaS-telepítések védelméről a következő témakörökben olvashat bővebben:

- [PaaS-környezetek védelme](paas-deployments.md)
- [PaaS-adatbázisok védelme az Azure-ban](paas-applications-using-sql.md)
