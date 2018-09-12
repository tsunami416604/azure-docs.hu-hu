---
title: A lapon lévő hivatkozások nem működnek az alkalmazásproxy-alkalmazáshoz |} A Microsoft Docs
description: Integrálva van az Azure AD Application Proxy alkalmazásai lévő megszakadt hivatkozások kapcsolatos problémák elhárítása
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 382b484f5ed1e45863d24635554cd7c3a55176a4
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44378414"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>A lapon lévő hivatkozások nem működnek az alkalmazásproxy-alkalmazáshoz

Ez a cikk segít az Azure Active Directory Application Proxy alkalmazás lévő hivatkozások nem működnek megfelelően okozó hibák elhárításához.

## <a name="overview"></a>Áttekintés 
Miután közzétett egy alkalmazásproxy-alkalmazást, csak hivatkozásokat, amelyek az alkalmazás alapértelmezés szerint a közzétett gyökér URL-cím található célok mutató hivatkozásokat. Az alkalmazásokban a hivatkozások nem működnek, az alkalmazás belső URL-cím valószínűleg nem tartalmazza az alkalmazásban lévő hivatkozások a célhelyre.

**Miért jelentkezik?** Amikor egy alkalmazás egy hivatkozásra kattint, az alkalmazásproxy megpróbálja feloldani az URL-címet, vagy egy belső URL-CÍMBEN belül ugyanazt az alkalmazást, vagy egy kívülről elérhető URL-CÍMÉT. Ha a hivatkozás, amely nem az azonos alkalmazáshoz belső URL-címre mutat, ne ezeket a gyűjtőket valamelyikéhez tartozik, majd egy nem talált hibát eredményez.

## <a name="ways-you-can-resolve-broken-links"></a>Módon oldható meg a megszakadt hivatkozások

A probléma megoldásához három módja van. Az alábbi lehetőségek a felsorolt egyre összetettebb.

1.  Ellenőrizze, hogy a belső URL-cím egy legfelső szintű, amely tartalmazza az ide tartozó hivatkozásokat az alkalmazáshoz. Ez lehetővé teszi az ugyanazon az alkalmazáson belüli közzétett tartalom oldható mutató összes hivatkozást.

    Ha a belső URL-Címének módosítása, de nem szeretné módosítani a felhasználók számára a kezdőlapját, módosítsa a kezdőlap URL-címe a korábban közzétett belső URL-cím. Ezt megteheti úgy az "Azure Active Directory" -&gt; Alkalmazásregisztrációk -&gt; válassza ki az alkalmazás -&gt; tulajdonságait. A Tulajdonságok lapon láthatja a mezőben "Kezdőlap URL-címe", amely a kívánt kezdőlapját is módosíthatja.

2.  Ha az alkalmazások teljes tartománynevek (FQDN) használ, akkor [egyéni tartományok](application-proxy-configure-custom-domain.md) , közzéteheti az alkalmazásait. Ez a funkció lehetővé teszi, hogy az azonos URL-cím, mind belső és külső megosztása.

    Ez a beállítás biztosítja, hogy az alkalmazás hivatkozásai proxyn keresztül történő alkalmazás kívülről hozzáférhetővé, mivel a belső URL-címek az alkalmazásban lévő hivatkozások kívülről is felismeri. Minden hivatkozásnak kell elvégeznie a közzétett alkalmazás tartozik. Azonban ez a beállítás a hivatkozások nem kell tartozniuk ugyanazt az alkalmazást, és több alkalmazás is tartozik.

3.  Ha ezen beállítások egyike sem megvalósítható, több módon beágyazott hivatkozást fordítási engedélyezéséhez. A lehetőségek közé tartozik, az Intune Managed Browser és a saját alkalmazások kiterjesztése, vagy az alkalmazás a hivatkozás fordítási beállítás használatával. Minden egyes ezeket a beállításokat és azok engedélyezéséről kapcsolatos további információkért lásd: [szoftveresen kötött mutató hivatkozásokat az Azure AD-alkalmazásproxyval közzétett alkalmazások átirányítási](application-proxy-configure-hard-coded-link-translation.md).

## <a name="next-steps"></a>További lépések
[Meglévő helyszíni proxykiszolgálók használata](application-proxy-configure-connectors-with-proxy-servers.md)

