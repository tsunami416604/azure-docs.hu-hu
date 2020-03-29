---
title: A lapon lévő hivatkozások nem működnek alkalmazásproxy-alkalmazások esetén
description: Az Azure AD-vel integrált alkalmazásproxy-alkalmazásokhibás hivatkozásaival kapcsolatos problémák elhárítása
services: active-directory
author: msmimart
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca782b5a190704911472d70c414d12afa7e558f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430245"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>A lapon lévő hivatkozások nem működnek alkalmazásproxy-alkalmazások esetén

Ez a cikk segít elhárítani, hogy az Azure Active Directory alkalmazásproxy-alkalmazás hivatkozásai miért nem működnek megfelelően.

## <a name="overview"></a>Áttekintés 
Az alkalmazásproxy-alkalmazás közzététele után az alkalmazásban alapértelmezés szerint csak a közzétett gyökér URL-címben található célhelyekre mutató hivatkozások működnek. Az alkalmazásokon belüli hivatkozások nem működnek, az alkalmazás belső URL-címe valószínűleg nem tartalmazza az alkalmazáson belüli hivatkozások összes célját.

**Miért történik ez?** Amikor egy alkalmazásban lévő hivatkozásra kattint, az alkalmazásproxy megpróbálja feloldani az URL-címet egy belső URL-címként ugyanazon az alkalmazáson belül, vagy külsőleg elérhető URL-címként. Ha a hivatkozás olyan belső URL-címre mutat, amely nem ugyanabban az alkalmazásban található, akkor egyik gyűjtőhöz sem tartozik, és nem talált hibát eredményez.

## <a name="ways-you-can-resolve-broken-links"></a>A hibás hivatkozások megoldásának módjai

A probléma háromféleképpen oldható meg. Az alábbi lehetőségek a felsorolt növekvő összetettsége.

1.  Győződjön meg arról, hogy a belső URL-cím egy gyökér, amely tartalmazza az alkalmazás hoz tartozó összes releváns hivatkozást. Ez lehetővé teszi, hogy az összes hivatkozás támlap-közzétett ként oldódjon fel az ugyanazon alkalmazáson belül közzétett tartalomként.

    Ha módosítja a belső URL-címet, de nem szeretné módosítani a felhasználók céllapját, módosítsa a kezdőlap URL-címét a korábban közzétett belső URL-re. Ezt úgy teheti meg, hogy az&gt; "Azure Active&gt; Directory"&gt; – Alkalmazásregisztrációk – válassza ki az alkalmazást – márkajelzés. A márkajelzési szakaszban a "Kezdőlap URL-címe" mező látható, amelyet a kívánt céloldalnak állíthat be. Ha továbbra is használja az örökölt alkalmazásregisztrációk tapasztalat a tulajdonságok lapon jelenik meg a "Kezdőlap URL-jét" részleteket. 
    
    > [!IMPORTANT]
    > A fenti módosítások elvégzéséhez az Azure AD-ben az alkalmazásobjektumok módosításához jogokra van szükség. A felhasználónak [alkalmazás-rendszergazdai](../users-groups-roles/roles-delegate-app-roles.md#assign-built-in-application-admin-roles) szerepkört kell rendelnie, amely alkalmazásmódosítási jogokat biztosít az Azure AD-ben a felhasználónak.
    >

2.  Ha az alkalmazások teljesen minősített tartományneveket (FQDNs) használnak, [egyéni tartományok](application-proxy-configure-custom-domain.md) használatával tegye közzé az alkalmazásokat. Ez a funkció lehetővé teszi, hogy ugyanazt az URL-címet belsőleg és külsőleg is használják.

    Ez a beállítás biztosítja, hogy az alkalmazás hivatkozásai külsőleg elérhetők legyenek az alkalmazásproxyn keresztül, mivel az alkalmazáson belüli belső URL-címekre mutató hivatkozásokat is külsőleg ismeri fel a rendszer. Minden hivatkozásnak továbbra is egy közzétett alkalmazáshoz kell tartoznia. Ezzel a beállítással azonban a hivatkozásoknak nem kell ugyanahhoz az alkalmazáshoz tartozniuk, és több alkalmazáshoz is tartozhatnak.

3.  Ha egyik lehetőség sem valósítható meg, több lehetőség is van a szövegközi hivatkozások fordításának engedélyezésére. Ezek a lehetőségek közé tartozik az Intune felügyelt böngésző, a Saját alkalmazások bővítmény vagy az alkalmazás hivatkozásfordítási beállításának használata. Ha többet szeretne megtudni az egyes beállításokról és azok engedélyezéséről, olvassa [el a Hardcoded linkek átirányítása az Azure AD alkalmazásproxyval közzétett alkalmazásokhoz című témakört.](application-proxy-configure-hard-coded-link-translation.md)

## <a name="next-steps"></a>További lépések
[Meglévő helyszíni proxykiszolgálók használata](application-proxy-configure-connectors-with-proxy-servers.md)

