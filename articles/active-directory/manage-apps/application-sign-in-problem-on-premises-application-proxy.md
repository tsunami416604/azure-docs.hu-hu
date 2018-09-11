---
title: Az Azure AD-alkalmazásproxy használatával a helyszíni alkalmazásokhoz való bejelentkezésnél |} A Microsoft Docs
description: Az Azure AD-alkalmazásproxy használatával az Azure AD-vel integrált szembesülnek, ha nem sikerül bejelentkezni egy helyszíni alkalmazásba gyakori hibák elhárítása
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 6f34945750d8f37a175e0b500a0596de08cf8ce4
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44357151"
---
# <a name="problems-signing-in-to-an-on-premises-application-using-the-azure-ad-application-proxy"></a>Az Azure AD-alkalmazásproxy használatával a helyszíni alkalmazásokhoz való bejelentkezésnél

Jelentkezzen be egy helyszíni alkalmazás problémák merülnek fel, ha az alábbi lépéseket a probléma megoldása után próbálkozzon.

## <a name="i-can-load-my-application-but-something-on-the-page-looks-broken"></a>Az alkalmazásom betöltődik, de az oldal hibásnak tűnik

Az alábbi dokumentum segíthet a kategóriába tartozó leggyakoribb problémák megoldásában.

  * [Hozzáférek az alkalmazáshoz, de az alkalmazás oldala nem megfelelően jelenik meg](application-proxy-page-appearance-broken-problem.md)
  * [Hozzáférek az alkalmazáshoz, de az alkalmazás betöltése túl sokáig tart](application-proxy-page-load-speed-problem.md)
  * [Hozzáférek az alkalmazáshoz, de az alkalmazás oldalán található hivatkozások nem működnek](application-proxy-page-links-broken-problem.md)

## <a name="im-having-a-connectivity-problem-my-application"></a>A csatlakozási problémát tapasztalok az alkalmazásom
  Az alábbi dokumentum segíthet a kategóriába tartozó leggyakoribb problémák megoldásában.
  * [Nem tudom, milyen portokat nyissak meg az alkalmazáshoz](application-proxy-connectivity-ports-how-to.md)
  * [Problémát tapasztalok, mert az alkalmazáshoz tartozó összekötőcsoportban nem volt működő összekötő](application-proxy-connectivity-no-working-connector.md)

## <a name="im-having-a-problem-configuring-the-azure-ad-application-proxy-in-the-admin-portal"></a>Problémát tapasztalok a felügyeleti portálon az Azure AD-alkalmazásproxy konfigurálása során
  Az alábbi dokumentum segíthet a kategóriába tartozó leggyakoribb problémák megoldásában.
  * [Problémát tapasztalok egy alkalmazásproxy-alkalmazás konfigurálásánál](application-proxy-config-how-to.md)
  * [Nem tudom, hogy kell beállítani az egyszeri bejelentkezést az alkalmazásproxy-alkalmazáshoz](application-proxy-config-sso-how-to.md)
  * [Problémát tapasztalok, amikor alkalmazást hozok létre a felügyeleti portálon](application-proxy-config-problem.md)

## <a name="im-having-a-problem-setting-up-back-end-authentication-to-my-application"></a>Problémát tapasztalok egy tudja beállítani a háttér-hitelesítést az alkalmazáshoz
  Az alábbi dokumentum segíthet a kategóriába tartozó leggyakoribb problémák megoldásában.
  * [Nem tudom, hogy kell konfigurálni a Kerberos általi korlátozott delegálást](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
  * [Nem tudom, hogy kell konfigurálni az alkalmazást a PingAccess segítségével ](application-proxy-back-end-ping-access-how-to.md)

## <a name="im-having-a-problem-when-signing-in-to-my-application"></a>Olyan problémát tapasztalok, amikor bejelentkezik az alkalmazásom
  Az alábbi dokumentum segíthet a kategóriába tartozó leggyakoribb problémák megoldásában.
  * ["Can't Access this Corporate Application" (Nem lehet hozzáférni ehhez a céges alkalmazáshoz) hibaüzenet jelenik meg](application-proxy-sign-in-bad-gateway-timeout-error.md)

## <a name="im-having-a-problem-with-the-application-proxy-agent-connector"></a>Problémát tapasztalok az alkalmazásproxy-ügynök Összekötőjével probléma
  Az alábbi dokumentum segíthet a kategóriába tartozó leggyakoribb problémák megoldásában.
  * [Problémát tapasztalok az alkalmazásproxy-ügynök összekötőjének telepítésénél](application-proxy-connector-installation-problem.md)

## <a name="next-steps"></a>További lépések
[Helyszíni alkalmazások biztonságos távoli elérése](application-proxy.md)
