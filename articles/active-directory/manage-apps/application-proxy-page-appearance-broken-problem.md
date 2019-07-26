---
title: Az alkalmazás lap nem jelenik meg megfelelően az alkalmazásproxy-alkalmazáshoz | Microsoft Docs
description: Az Azure ad-vel integrált útmutatást, ha az oldal nem megfelelően jelennek meg az egy alkalmazásproxy-alkalmazás
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
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381430"
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>Alkalmazás lapján nem jelennek meg megfelelően az alkalmazásproxy-alkalmazáshoz

Ez a cikk segít az Azure Active Directory Application Proxy alkalmazásai hibák elhárításához, ha, nyissa meg a lapot, de az oldal hibásnak tűnik megfelelő.

## <a name="overview"></a>Áttekintés
Amikor közzétesz egy alkalmazásproxy-alkalmazást, csak a legfelső szintű alatt lapok érhetők el az alkalmazás elérésekor. Ha az oldal nem megfelelően jelenik meg, a legfelső szintű belső URL-cím az alkalmazáshoz használt esetleg hiányzik a lap források. Oldja meg, győződjön meg arról, miután közzétette *összes* az erőforrásokat az oldal az alkalmazás részeként.

A probléma megoldásához ellenőrizze, hogy a hiányzó erőforrások problémát a Network Tracker (például a Hegedűs vagy az F12-eszközök Internet Explorerben vagy a Microsoft Edge-ben) megnyitásával, a lap betöltésével és az 404-es hibák keresésével ellenőrizheti. Azt jelzi, az oldalak jelenleg nem találhatók, és van szükség, ha közzétesszük őket.

Ebben az esetben a példaként tegyük fel, a belső URL-címet használó költségek alkalmazások közzétételét `http://myapps/expenses`, de az alkalmazás használ a stíluslap `http://myapps/style.css`. Ebben az esetben a stíluslap nincs közzétéve az alkalmazásban, így a költségek alkalmazás betöltése throw 404-es hiba style.css betöltése közben. Ebben a példában a probléma megoldódott, tegye közzé az alkalmazást a belső URL-címének `http://myapp/`.

## <a name="problems-with-publishing-as-one-application"></a>Egy alkalmazás közzétételi kapcsolatos problémák

Ha nem lehet ugyanaz az alkalmazás összes erőforrásának közzététele, meg kell több alkalmazások közzététele és a hivatkozások között.

Ha igen, azt javasoljuk, a [egyéni tartományok](application-proxy-configure-custom-domain.md) megoldás. Ez a megoldás azonban megköveteli, hogy saját a tanúsítványt a tartomány és az alkalmazások teljes tartománynevek (FQDN) használja. További beállításokért lásd: a [hibaelhárítása a megszakadt hivatkozások dokumentáció](application-proxy-page-links-broken-problem.md).

## <a name="next-steps"></a>További lépések
[Alkalmazások közzététele az Azure AD-alkalmazásproxy használatával](application-proxy-add-on-premises-application.md)
