---
title: A lapon lévő hivatkozások nem működnek az alkalmazásproxy-alkalmazáshoz |} A Microsoft Docs
description: Integrálva van az Azure AD Application Proxy alkalmazásai lévő megszakadt hivatkozások kapcsolatos problémák elhárítása
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: celested
ms.reviewer: asteen
ms.openlocfilehash: d426a0185d7425a7e32699dce511bbafa7c0e623
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55961516"
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

