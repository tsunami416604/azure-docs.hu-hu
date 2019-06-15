---
title: Az Azure AD-alkalmazásproxy használatával a helyszíni alkalmazásokhoz való bejelentkezésnél |} A Microsoft Docs
description: Az Azure AD-alkalmazásproxy használatával az Azure AD-vel integrált szembesülnek, ha nem sikerül bejelentkezni egy helyszíni alkalmazásba gyakori hibák elhárítása
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
ms.openlocfilehash: 0adc2effb27ffcd870ec543ee7b353a092808797
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65825257"
---
# <a name="problems-signing-in-to-an-on-premises-application-using-the-azure-ad-application-proxy"></a>Az Azure AD-alkalmazásproxy használatával a helyszíni alkalmazásokhoz való bejelentkezésnél

Jelentkezzen be egy helyszíni alkalmazás problémák merülnek fel, ha az alábbi lépéseket a probléma megoldása után próbálkozzon.

## <a name="i-can-load-my-application-but-something-on-the-page-looks-broken"></a>Az alkalmazásom betöltődik, de az oldal hibásnak tűnik

Az alábbi dokumentum segíthet a kategóriába tartozó leggyakoribb problémák megoldásában.

  * [Hozzáférek az alkalmazáshoz, de az alkalmazás oldala nem megfelelően jelenik meg](application-proxy-page-appearance-broken-problem.md)
  * [Hozzáférek az alkalmazáshoz, de az alkalmazás betöltése túl sokáig tart](application-proxy-page-load-speed-problem.md)
  * [Hozzáférek az alkalmazáshoz, de az alkalmazás oldalán található hivatkozások nem működnek](application-proxy-page-links-broken-problem.md)

## <a name="im-having-a-connectivity-problem-my-application"></a>Kapcsolati probléma van az alkalmazásommal
  Az alábbi dokumentum segíthet a kategóriába tartozó leggyakoribb problémák megoldásában.
  * [Nem tudom, milyen portokat nyissak meg az alkalmazáshoz](application-proxy-connectivity-ports-how-to.md)
  * [Problémát tapasztalok, mert az alkalmazáshoz tartozó összekötőcsoportban nem volt működő összekötő](application-proxy-connectivity-no-working-connector.md)

## <a name="im-having-a-problem-configuring-the-azure-ad-application-proxy-in-the-admin-portal"></a>Probléma lépett fel az Azure AD-alkalmazásproxy konfigurálásakor a rendszergazdai portálon
  Az alábbi dokumentum segíthet a kategóriába tartozó leggyakoribb problémák megoldásában.
  * [Problémát tapasztalok egy alkalmazásproxy-alkalmazás konfigurálásánál](application-proxy-config-how-to.md)
  * [Nem tudom, hogy kell beállítani az egyszeri bejelentkezést az alkalmazásproxy-alkalmazáshoz](application-proxy-config-sso-how-to.md)
  * [Problémát tapasztalok, amikor alkalmazást hozok létre a felügyeleti portálon](application-proxy-config-problem.md)

## <a name="im-having-a-problem-setting-up-back-end-authentication-to-my-application"></a>Probléma lépett fel az alkalmazásomhoz való háttér-hitelesítés beállításakor
  Az alábbi dokumentum segíthet a kategóriába tartozó leggyakoribb problémák megoldásában.
  * [Nem tudom, hogy kell konfigurálni a Kerberos általi korlátozott delegálást](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
  * [Nem tudom, hogy kell konfigurálni az alkalmazást a PingAccess segítségével ](application-proxy-back-end-ping-access-how-to.md)

## <a name="im-having-a-problem-when-signing-in-to-my-application"></a>Probléma lépett fel az alkalmazásomba történő bejelentkezés során
  Az alábbi dokumentum segíthet a kategóriába tartozó leggyakoribb problémák megoldásában.
  * ["Can't Access this Corporate Application" (Nem lehet hozzáférni ehhez a céges alkalmazáshoz) hibaüzenet jelenik meg](application-proxy-sign-in-bad-gateway-timeout-error.md)

## <a name="im-having-a-problem-with-the-application-proxy-agent-connector"></a>Problémát tapasztalok az alkalmazásproxy-ügynök összekötőjével
  Az alábbi dokumentum segíthet a kategóriába tartozó leggyakoribb problémák megoldásában.
  * [Problémát tapasztalok az alkalmazásproxy-ügynök Összekötőjével telepítésével kapcsolatos problémák](application-proxy-connector-installation-problem.md)

## <a name="next-steps"></a>További lépések
[Helyszíni alkalmazások biztonságos távoli elérése](application-proxy.md)
