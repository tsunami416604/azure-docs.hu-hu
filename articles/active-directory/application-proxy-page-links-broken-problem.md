---
title: A lapon lévő hivatkozások nem működnek az alkalmazásproxy alkalmazás |} Microsoft Docs
description: Érvénytelen hivatkozásokkal a integrálva van az Azure AD alkalmazásproxy-alkalmazásokkal kapcsolatos problémák elhárítása
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
ms.topic: article
ms.date: 05/18/2018
ms.author: barbkess
ms.reviewer: harshja
ms.openlocfilehash: 3e356beda3e95748cbee64a180612dd183a7ce0e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34592296"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>A lapon lévő hivatkozások nem működnek az alkalmazásproxy alkalmazás

Ez a cikk segít hibaelhárítása, ezért az Azure Active Directory Alkalmazásproxyjával alkalmazás lévő hivatkozások nem működnek megfelelően.

## <a name="overview"></a>Áttekintés 
Miután közzétette az alkalmazásproxy-alkalmazást, a csak hivatkozások, amelyek az alkalmazás alapértelmezés szerint működnek a célok a közzétett gyökér URL-cím található hivatkozások olyan. Az alkalmazásokon belül a hivatkozások nem működnek, az alkalmazás belső URL-címe valószínűleg nem tartalmaz hivatkozásokat az alkalmazáson belül a célhelyre.

**Miért történik ez?** Amikor egy alkalmazás egy hivatkozásra kattint, a alkalmazásproxy megpróbálja feloldani az URL-címet, vagy belső URL-címként belül ugyanazt az alkalmazást, vagy kívülről elérhető URL-címként. A hivatkozás mutat egy belső URL-CÍMÉT, amely kívül esik az ugyanahhoz az alkalmazáshoz, ha azt nem tartozik sem a gyűjtők és nem talált hibát eredményez.

## <a name="ways-you-can-resolve-broken-links"></a>Hibás hivatkozások oldhatja módjai

A probléma megoldásához három módja van. Az alábbi lehetőségek a felsorolt növekvő összetettségét.

1.  Ellenőrizze, hogy a belső URL-cím a legfelső szintű az alkalmazás összes ide tartozó hivatkozásokat tartalmaz. Ez lehetővé teszi az ugyanazon alkalmazásban közzétett tartalmat oldható mutató összes hivatkozást.

    Ha módosítsa a belső URL-címet, de nem szeretné módosítani a felhasználók kezdőlapja, módosítsa a kezdőlap URL-címet a korábban közzétett belső URL-címre. Ha az "Azure Active Directory" - Ehhez&gt; App regisztrációk -&gt; válassza ki az alkalmazás -&gt; tulajdonságok. A Tulajdonságok lapon tekintse meg a mező "Kezdőlap URL-címe", amelyen beállíthatja a kívánt kezdőlapja lehet.

2.  Ha az alkalmazások teljes tartományneveit (FQDN) használ, akkor [egyéni tartományok](manage-apps/application-proxy-configure-custom-domain.md) az alkalmazások közzétételét. Ez a funkció lehetővé teszi, hogy az URL-CÍMÉRE kell használni, mind belső és külső beállításait.

    Ez a beállítás biztosítja, hogy az alkalmazás hivatkozását kívülről elérhető proxyn keresztül történő óta a hivatkozások belső URL-címek az alkalmazáson belül kívülről is felismeri. Minden kapcsolat továbbra is szeretné a közzétett alkalmazás tartozik. Azonban ez a beállítás a hivatkozások nem kell tartozniuk ugyanazt az alkalmazást, és több alkalmazást is tartozik.

3.  Ha egyik sem valósítható meg, megtekintheti az új szolgáltatása, amelyet URL-cím fordítása/újraírását. Ez a szolgáltatás belső URL-címeket és az alkalmazások HTML törzsében fennálló lefordítva, vagy "leképezve", a közzétett külső Proxy URL-címek. Ezt a fordítást csak akkor működik a hivatkozások a HTML- vagy CSS, és nem oldották a hivatkozás segítségével JS jön létre. 

Ennek eredményeképpen, erősen ajánlott használatával a [egyéni tartományok](manage-apps/application-proxy-configure-custom-domain.md) megoldás Ha lehetséges. Ha csatlakozni szeretne az előzetes, e-mailben <aadapfeedback@microsoft.com> a applicationId(s) együtt.

## <a name="next-steps"></a>További lépések
[A meglévő helyszíni proxykiszolgálókkal működik](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)

