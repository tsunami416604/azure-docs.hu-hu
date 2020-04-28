---
title: Az alkalmazás lap nem jelenik meg megfelelően az alkalmazásproxy-alkalmazáshoz | Microsoft Docs
description: Útmutatás arra az esetre, ha a lap nem jelenik meg megfelelően az Azure AD-vel integrált alkalmazásproxy-alkalmazásban
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "68381430"
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>Az alkalmazás lap nem jelenik meg megfelelően egy alkalmazásproxy-alkalmazáshoz

Ez a cikk segítséget nyújt a Azure Active Directory Application Proxy-alkalmazásokkal kapcsolatos hibák elhárításához, amikor megnyitja a lapot, de az oldalon található valami nem megfelelő.

## <a name="overview"></a>Áttekintés
Ha egy alkalmazásproxy-alkalmazást tesz közzé, az alkalmazáshoz való hozzáféréskor csak a gyökér alatti lapok érhetők el. Ha a lap nem megfelelően jelenik meg, előfordulhat, hogy az alkalmazáshoz használt legfelső szintű belső URL-cím nem tartalmaz néhány oldal erőforrást. A megoldáshoz győződjön meg arról, hogy az alkalmazás részeként közzétette az oldal *összes* erőforrását.

A probléma megoldásához ellenőrizze, hogy a hiányzó erőforrások problémát a Network Tracker (például a Hegedűs vagy az F12-eszközök Internet Explorerben vagy a Microsoft Edge-ben) megnyitásával, a lap betöltésével és az 404-es hibák keresésével ellenőrizheti. Ez azt jelzi, hogy jelenleg nem találhatók a lapok, és közzé kell tenni őket.

Ebben az esetben tegyük fel, hogy a belső URL-cím `http://myapps/expenses`használatával közzétett egy kiadási alkalmazást, de az alkalmazás a stíluslapot `http://myapps/style.css`használja. Ebben az esetben a stíluslapot a rendszer nem teszi közzé az alkalmazásban, így a költségek alkalmazás betöltésével a Style. css betöltésére tett kísérlet során 404-as hibát jelez. Ebben a példában a problémát úgy oldja meg, hogy közzéteszi az alkalmazást egy belső `http://myapp/`URL-címmel.

## <a name="problems-with-publishing-as-one-application"></a>Egyetlen alkalmazásként való közzététel problémái

Ha nem lehetséges az összes erőforrás közzététele ugyanazon az alkalmazáson belül, közzé kell tennie több alkalmazást, és engedélyeznie kell a közöttük lévő kapcsolatokat.

Ehhez az [Egyéni tartományok](application-proxy-configure-custom-domain.md) megoldás használatát javasoljuk. Ez a megoldás azonban megköveteli, hogy a tartományhoz tartozó tanúsítvány tulajdonosa legyen, és az alkalmazásai teljes tartománynevet (FQDN) használnak. További lehetőségekért tekintse meg a [hibás csatolások dokumentációját](application-proxy-page-links-broken-problem.md).

## <a name="next-steps"></a>További lépések
[Alkalmazások közzététele az Azure AD-alkalmazásproxyval](application-proxy-add-on-premises-application.md)
