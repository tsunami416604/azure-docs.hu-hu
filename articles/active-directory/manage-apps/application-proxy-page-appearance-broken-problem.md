---
title: Az alkalmazáslap nem jelenik meg megfelelően az Alkalmazásproxy alkalmazáshoz | Microsoft dokumentumok
description: Útmutató, ha az oldal nem jelenik meg megfelelően az Azure AD-vel integrált alkalmazásproxy-alkalmazásban
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13e73f0ed56648ce162f00d6df5e7b86a922ca01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68381430"
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>Az alkalmazáslap nem jelenik meg megfelelően egy alkalmazásproxy-alkalmazáshoz

Ez a cikk segít az Azure Active Directory alkalmazásproxy-alkalmazásalkalmazás-alkalmazásokkal kapcsolatos problémák elhárításában, amikor a lapra navigál, de a lapon valami nem tűnik megfelelőnek.

## <a name="overview"></a>Áttekintés
Alkalmazásproxy-alkalmazás közzétételekor csak a gyökér alatti lapok érhetők el az alkalmazás elérésekor. Ha az oldal nem megfelelően jelenik meg, előfordulhat, hogy az alkalmazáshoz használt belső gyökér-URL-címből hiányzik néhány oldalerőforrás. A feloldáshoz győződjön meg arról, hogy az alkalmazás részeként közzétette az oldal *összes* erőforrását.

Ellenőrizheti, hogy a hiányzó erőforrások jelentik-e a problémát, ha megnyitja a hálózati nyomkövetőt (például a Fiddler vagy az F12 eszközöket az Internet Explorer/Microsoft Edge böngészőben), betölti a lapot, és 404-es hibákat keres. Ez azt jelzi, hogy a lapok jelenleg nem találhatók, és közzé kell tenni őket.

Az eset példaként tegyük fel, hogy a belső `http://myapps/expenses`URL-cím használatával közzétett `http://myapps/style.css`egy költségalkalmazást, de az alkalmazás a stíluslapot használja. Ebben az esetben a stíluslap nem jelenik meg az alkalmazásban, így a költségek alkalmazás betöltése 404-es hibát okoz a style.css betöltése közben. Ebben a példában a probléma megoldódik azáltal, `http://myapp/`hogy az alkalmazást belső URL-címmel teszi közzé.

## <a name="problems-with-publishing-as-one-application"></a>Problémák az egy alkalmazásként történő közzététellel kapcsolatban

Ha nem lehet az összes erőforrást közzétenni ugyanazon az alkalmazáson belül, több alkalmazást kell közzétennie, és engedélyeznie kell a köztük lévő hivatkozásokat.

Ehhez azt javasoljuk, hogy az [egyéni tartományok](application-proxy-configure-custom-domain.md) megoldás. Ez a megoldás azonban megköveteli, hogy ön birtokolja a tartomány tanúsítványát, és az alkalmazások teljesen minősített tartományneveket (FQDNs) használjanak. További lehetőségeket a [hibás hivatkozásokhiba-elhárításdokumentációjában](application-proxy-page-links-broken-problem.md)talál.

## <a name="next-steps"></a>További lépések
[Alkalmazások közzététele az Azure AD-alkalmazásproxyval](application-proxy-add-on-premises-application.md)
