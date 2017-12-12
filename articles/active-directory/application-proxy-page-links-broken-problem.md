---
title: "A lapon lévő hivatkozások nem működnek az alkalmazásproxy alkalmazás |} Microsoft Docs"
description: "Érvénytelen hivatkozásokkal a integrálva van az Azure AD alkalmazásproxy-alkalmazásokkal kapcsolatos problémák elhárítása"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 17f2afb0aaf3b899784a504b77f33a1284f0a232
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>A lapon lévő hivatkozások nem működnek az alkalmazásproxy alkalmazás

Ez a cikk segítséget hibaelhárítása, ezért az Azure Active Directory Alkalmazásproxyjával alkalmazás lévő hivatkozások nem működnek megfelelően.

## <a name="overview"></a>Áttekintés 
Miután közzétette az alkalmazásproxy-alkalmazást, a csak hivatkozások, amelyek az alkalmazás alapértelmezés szerint működnek a célok a közzétett gyökér URL-cím található hivatkozások olyan. Az alkalmazásokon belül a hivatkozások nem működnek, az alkalmazás belső URL-címe valószínűleg nem tartalmaz hivatkozásokat az alkalmazáson belül a célhelyre.

**Miért történik ez?** Amikor egy alkalmazás egy hivatkozásra kattint, a alkalmazásproxy megpróbálja feloldani az URL-címet, vagy belső URL-címként belül ugyanazt az alkalmazást, vagy kívülről elérhető URL-címként. A hivatkozás mutat egy belső URL-CÍMÉT, amely kívül esik az ugyanahhoz az alkalmazáshoz, ha azt nem tartozik sem a gyűjtők és nem talált hibát eredményez.

## <a name="ways-you-can-resolve-broken-links"></a>Hibás hivatkozások oldhatja módjai

A probléma megoldásához három módja van. Az alábbi lehetőségek a felsorolt növekvő összetettségét.

1.  Ellenőrizze, hogy a belső URL-cím a legfelső szintű az alkalmazás összes ide tartozó hivatkozásokat tartalmaz. Ez lehetővé teszi az ugyanazon alkalmazásban közzétett tartalmat oldható mutató összes hivatkozást.

    Ha módosítsa a belső URL-címet, de nem szeretné módosítani a felhasználók kezdőlapja, módosítsa a kezdőlap URL-címet a korábban közzétett belső URL-címre. Ha az "Azure Active Directory" - Ehhez&gt; App regisztrációk -&gt; válassza ki az alkalmazás -&gt; tulajdonságok. A Tulajdonságok lapon tekintse meg a mező "Kezdőlap URL-címe" amely beállíthatja a kívánt kezdőlapja lehet.

2.  Ha az alkalmazások teljes tartományneveit (FQDN) használ, akkor [egyéni tartományok](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) az alkalmazások közzétételét. Ez a funkció lehetővé teszi, hogy az URL-CÍMÉRE kell használni, mind belső és külső beállításait.

    Ez a beállítás biztosítja, hogy az alkalmazás hivatkozását kívülről elérhető proxyn keresztül történő óta a hivatkozások belső URL-címek az alkalmazáson belül kívülről is felismeri. Ne feledje, hogy minden kapcsolat továbbra is a közzétett alkalmazás tartozik. Azonban ez a beállítás a hivatkozások nem kell tartozniuk ugyanazt az alkalmazást, és több alkalmazást is tartozik.

3.  Ha egyik sem valósítható meg, egy új szolgáltatás, amelyek URL-cím fordítása/írja át az előzetes csatlakozik. Ezzel a lehetőséggel belső URL-címeket és az alkalmazások HTML törzsében fennálló lefordítva, vagy "leképezve", a közzétett külső Proxy URL-címek. Ez csak akkor működik, a HTML-vagy CSS hivatkozások, és ez segít a hivatkozás segítségével JS jön létre. 

Ennek eredményeképpen, erősen ajánlott használatával a [egyéni tartományok](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) megoldás Ha lehetséges. Ha csatlakozni szeretne az előzetes, e-mailben < aadapfeedback@microsoft.com > a applicationId(s) együtt.

## <a name="next-steps"></a>Következő lépések
[A meglévő helyszíni proxykiszolgálókkal működik](application-proxy-working-with-proxy-servers.md)

