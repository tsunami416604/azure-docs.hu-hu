---
title: "PaaS webes és mobilalkalmazások használja az Azure App Service védelmének biztosítása |} Microsoft Docs"
description: " Tudnivalók Azure App Service biztonsági gyakorlati tanácsok a PaaS webes és mobilalkalmazásokhoz biztonságossá tételéhez. "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: dec45d91ad1a73306b3e2656dd9bf7fdbe456720
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2017
---
# <a name="securing-paas-web-and-mobile-applications-using-azure-app-service"></a>A PaaS webes és mobilalkalmazásokhoz, használja az Azure App Service biztonságossá tétele

Ez a cikk arról lesz szó gyűjteménye [Azure App Service](https://azure.microsoft.com/services/app-service/) ajánlott biztonsági eljárások az PaaS webes és mobilalkalmazások védelme. Az alábbi gyakorlati tanácsok az Azure-ral tapasztalatunk és az ügyfelek, például a saját kezűleg feladatait származik.

## <a name="azure-app-service"></a>Azure App Service
[Az Azure App Service](../app-service/app-service-web-overview.md) a Platformszolgáltatási ajánlat, amely lehetővé teszi, hogy hozzon létre a web- és mobilalkalmazásokat bármilyen platformra vagy eszközre, és a felhőben, vagy a helyszíni adatok bárhol, csatlakozzon. Az App Service rendelkezik azon webes és mobil funkciókkal, amelyeket korábban külön kapott Azure Websites és Azure Mobile Services. Ezenkívül új lehetőségek is elérhetők az üzleti folyamatok automatizálásához és felhőalapú API-k üzemeltetéséhez. Egyetlen integrált szolgáltatásként az App Service számos lehetőséget kínál széles választékának képességek webes, mobil és integrációs feladatokhoz.

## <a name="best-practices"></a>Ajánlott eljárások

App Service használata esetén kövesse az alábbi gyakorlati tanácsok:

- [Hitelesítés az Azure Active Directory (AD) révén](../app-service/app-service-authentication-overview.md). App Service az identitásszolgáltató az OAuth 2.0 szolgáltatást biztosít. Ügyfél fejlesztői egyszerűség ugyanakkor biztosítható a megadott engedélyezési flow webes alkalmazásokhoz, az asztali alkalmazások és a mobiltelefonok OAuth 2.0 összpontosít. Az Azure AD OAuth 2.0 használatával lehetővé teszik a mobil hozzáférés és a webes alkalmazások engedélyezéséhez.
- Korlátozhatja a hozzáférést a szükséges mértékű ismeretek és a legalacsonyabb jogosultsági biztonsági alapelvek alapján. Hozzáférés korlátozása elengedhetetlen a szervezeteknek, amelyek az adatok biztonsági házirendek kikényszerítéséhez. Szerepköralapú hozzáférés-vezérlés (RBAC) segítségével engedélyek hozzárendelése a felhasználók, csoportok és alkalmazások egy adott hatókörben. Az alkalmazásokhoz való hozzáférés biztosítása a felhasználók kapcsolatos további információkért lásd: [Ismerkedés a kezelési](../active-directory/role-based-access-control-what-is.md).
- Kulcsok védelmét. Nem számít, mennyire jó a biztonság az Ha elveszíti a előfizetés kulcsokat. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. A Key Vault lehetővé teszi, hogy hardveres biztonsági modulokkal (HSM) védett kulcsokkal titkosítsa a kulcsokat és a titkos kulcsokat (például a hitelesítési kulcsokat, a tárfiókok kulcsait, az adattitkosítási kulcsokat, a PFX-fájlokat és a jelszavakat). A még nagyobb biztonság érdekében lehetőség van arra is, hogy kulcsokat importáljon és generáljon a hardveres biztonsági modulokban. Lásd: [Azure Key Vault](../key-vault/key-vault-whatis.md) további. Key Vault segítségével is kezelheti a TLS-tanúsítványokat az automatikus tartománymegújítási.
- Bejövő forrás IP-címek korlátozása. [App Service Environment-környezet](../app-service/environment/intro.md) egy virtuális hálózati integráció funkció, amely segít a bejövő forrás IP-címek a hálózati biztonsági csoportokkal (NSG-k) korlátozása. Ha ismeri az Azure virtuális hálózatokról (Vnetekről), ez az a képesség, amely lehetővé teszi, hogy helyezze el az Azure-erőforrások számos hozzáférést szabályozó nem internetes, az irányítható hálózaton. További tudnivalókért lásd: [az alkalmazás integrálja az Azure virtuális hálózat](../app-service/web-sites-integrate-with-vnet.md).

## <a name="next-steps"></a>Következő lépések
Ez a cikk bevezetett App Service ajánlott biztonsági eljárások az PaaS webes és mobilalkalmazások védelme gyűjteménye. A PaaS üzemelő példányok biztosításával kapcsolatos további tudnivalókért lásd:

- [PaaS-környezetek védelme](security-paas-deployments.md)
- [PaaS webes és mobilalkalmazások használata az Azure SQL Database és az SQL Data Warehouse védelme](security-paas-applications-using-sql.md)
