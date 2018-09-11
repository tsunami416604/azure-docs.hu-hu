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
ms.date: 05/18/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 505b3e7df64ef804448bd5753bdaa6148b22a2c3
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44357269"
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

3.  Ha ezen beállítások egyike sem megvalósítható, megtekintheti az új funkciója, amely URL-cím fordítása/újraírását. Ezzel a funkcióval belső URL-címek vagy az alkalmazások HTML törzsében lévő hivatkozásokat a lefordított, vagy "leképezve", a közzétett alkalmazás külső Proxy URL. A fordítási csak a HTML és CSS, hivatkozások a működik, így nem jön létre a hivatkozás JS keresztül. 

Emiatt erősen javasoljuk a [egyéni tartományok](application-proxy-configure-custom-domain.md) megoldás, ha lehetséges. Ha csatlakozni szeretne az előzetes verzióra, e-mailt <aadapfeedback@microsoft.com> a applicationId(s) együtt.

## <a name="next-steps"></a>További lépések
[Meglévő helyszíni proxykiszolgálók használata](application-proxy-configure-connectors-with-proxy-servers.md)

