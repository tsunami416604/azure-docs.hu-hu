---
title: Alkalmazás lapján nem jelennek meg helyesen az alkalmazásproxy-alkalmazás |} Microsoft Docs
description: Ha a lap nem megfelelően jelennek meg az alkalmazás Proxy alkalmazás útmutatást integrálva van az Azure AD
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: asteen
ms.openlocfilehash: d15c02d86dc4a61e7e92b4c896cfc13dd0218c61
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>Alkalmazás lapján nem jelennek meg helyesen az alkalmazásproxy-alkalmazáshoz

Ez a cikk segít az Azure Active Directory Alkalmazásproxyjával alkalmazásokkal kapcsolatos problémák hibaelhárítása az lapra lép, de az oldalon valami nem megfelelően jelenik meg helyes.

## <a name="overview"></a>Áttekintés
Alkalmazásproxy alkalmazások közzététele, amikor csak a legfelső szintű lapok érhetők el az alkalmazáshoz való hozzáféréskor. A lapon nem jelennek meg megfelelően, ha lehet, hogy a legfelső szintű belső használt URL-cím az alkalmazás hiányzik néhány lap erőforrást. Elhárítása érdekében ellenőrizze, hogy miután közzétette *összes* az erőforrásokat a lap az alkalmazás részeként.

Hiányzó erőforrások esetén a problémát a hálózati követő megnyitásával ellenőrizheti (például a Fiddler, vagy az F12 eszközök Internet Explorer vagy Edge), betölteni az oldalt, és a 404-es hibákat keres. Amely azt jelzi, hogy a lapok jelenleg nem található, és telepíteni kell, amely közzéteszi.

Ebben az esetben, például azt feltételezik, miután közzétette a belső URL-címet használó költségek alkalmazások http://myapps/expenses, de az alkalmazás használ a stíluslap http://myapps/style.css. Ebben az esetben a stíluslap nincs közzétéve az alkalmazásban, a költségek alkalmazás betöltése kivételt a 404-es hibaüzenetet style.css betöltésére tett kísérlet közben. Ebben a példában a probléma megoldódott, azzal, hogy közzétesz egy belső URL-CÍMÉT az alkalmazás http://myapp/.

## <a name="problems-with-publishing-as-one-application"></a>Egy alkalmazás közzétételi kapcsolatos problémák

Ha nincs lehetőség az ugyanazon alkalmazásban minden erőforrások közzétételét, módosítania több alkalmazások közzététele és a hivatkozások között.

Ha igen, azt javasoljuk, a [egyéni tartományok](manage-apps/application-proxy-configure-custom-domain.md) megoldás. Azonban az ehhez a megoldáshoz szükségesek, hogy Ön a tulajdonosa a tanúsítványt a tartomány és az alkalmazások teljes tartományneveit (FQDN) használja. Egyéb lehetőségek, tekintse meg a [hivatkozások dokumentáció hibaelhárítása](application-proxy-page-links-broken-problem.md).

## <a name="next-steps"></a>További lépések
[Az Azure AD-alkalmazásproxy használó alkalmazások közzététele](manage-apps/application-proxy-publish-azure-portal.md)
