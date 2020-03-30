---
title: Nem érhető el ez a vállalati alkalmazáshiba az App Proxy alkalmazással
description: Gyakori hozzáférési problémák megoldása az Azure AD alkalmazásproxy-alkalmazásokkal.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d61f4b4bce9b8287dc13237f071684ea5d135fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275474"
---
# <a name="cant-access-this-corporate-application-error-when-using-an-application-proxy-application"></a>"Nem érhető el ez a vállalati alkalmazás" hibaüzenet alkalmazásproxy-alkalmazás használata esetén

Ez a cikk segít a "Ez a vállalati alkalmazás nem érhető el" hiba gyakori problémáinak elhárítását egy Azure AD alkalmazásproxy-alkalmazásban.

## <a name="overview"></a>Áttekintés

Ha ezt a hibát látja, keresse meg az állapotkódot a hibaoldalon. Ez a kód valószínűleg az alábbi állapotkódok egyike:

- **Átjáró időmeghosszabbítása**: Az alkalmazásproxy szolgáltatás nem tudja elérni az összekötőt. Ez a hiba általában azt jelzi, hogy probléma van az összekötő hozzárendelésével, magával az összekötővel vagy az összekötő körüli hálózati szabályokkal.
- **Hibás átjáró**: Az összekötő nem tudja elérni a háttéralkalmazás. Ez a hiba az alkalmazás helytelen konfigurációját jelezheti.
- **Tiltott**: A felhasználó nem jogosult az alkalmazás elérésére. Ez a hiba akkor fordulhat elő, ha a felhasználó nincs hozzárendelve az alkalmazáshoz az Azure Active Directoryban, vagy ha a háttérrendszeren a felhasználó nem rendelkezik az alkalmazás eléréséhez szükséges engedéllyel.

A kód megkereséséhez tekintse meg az "Állapotkód" mező hibaüzenetének bal alsó sarkában található szöveget. Is keresse meg a további tippeket az oldal alján.

![Példa: Az átjáró időmeghozási hibája](./media/application-proxy-sign-in-bad-gateway-timeout-error/connection-problem.png)

A hibák kiváltó okainak elhárításáról és a javasolt javításokról az alábbi megfelelő szakaszban olvashat részletesen.

## <a name="gateway-timeout-errors"></a>Átjáró időmegta-elállási hibái

Az átjáró időkiírása akkor következik be, amikor a szolgáltatás megpróbálja elérni az összekötőt, és nem tudja az időkiszolgáló ablakon belül. Ezt a hibát általában egy működő összekötőnélküli összekötőcsoporthoz rendelt alkalmazás okozza, vagy az összekötő által igényelt egyes portok nincsenek megnyitva.

## <a name="bad-gateway-errors"></a>Hibás átjáróhibák

Egy hibás átjáróhiba azt jelzi, hogy az összekötő nem tudja elérni a háttéralkalmazás. ellenőrizze, hogy a megfelelő alkalmazást tette-e közzé. A hibát okozó gyakori hibák a következők:

- Elírás vagy hiba a belső URL-ben
- Nem teszi közzé az alkalmazás gyökerét. Például a <http://expenses/reimbursement> közzététel, de a hozzáférés<http://expenses>
- Problémák a Kerberos korlátozott delegálás (KCD) konfigurációjával
- Problémák a háttéralkalmazással

## <a name="forbidden-errors"></a>Tiltott hibák

Ha tiltott hibát lát, a felhasználó nincs hozzárendelve az alkalmazáshoz. Ez a hiba lehet az Azure Active Directoryban vagy a háttéralkalmazásban.

Ha meg szeretné tudni, hogyan rendelhet felhasználókat az azure-beli alkalmazáshoz, olvassa el a [konfigurációs dokumentációt.](application-proxy-add-on-premises-application.md#test-the-application)

Ha megerősíti, hogy a felhasználó hozzá van rendelve az alkalmazáshoz az Azure-ban, ellenőrizze a felhasználói konfigurációt a háttéralkalmazásban. Kerberos korlátozott delegálás/integrált Windows-hitelesítés használata esetén a KCD hibaelhárítási lapján útmutatást nyújt.

## <a name="check-the-applications-internal-url"></a>Az alkalmazás belső URL-címének ellenőrzése

Első gyorslépésként ellenőrizze és javítsa ki a belső URL-t az alkalmazás **enterprise applications**-en keresztül történő megnyitásával, majd válassza az **Alkalmazásproxy** menüt. Ellenőrizze, hogy a belső URL-cím a helyszíni hálózatból az alkalmazás eléréséhez használt.

## <a name="check-the-application-is-assigned-to-a-working-connector-group"></a>Annak ellenőrzése, hogy az alkalmazás egy működő összekötő csoporthoz van-e rendelve

Annak ellenőrzése, hogy az alkalmazás egy működő összekötő csoporthoz van-e rendelve:

1. Nyissa meg az alkalmazást a portálon az **Azure Active Directory**megnyitásával, kattintson a Vállalati **alkalmazások**, majd az Összes **alkalmazás elemre.** Nyissa meg az alkalmazást, majd válassza a bal oldali menü **Alkalmazásproxy** parancsát.
1. Nézze meg az Összekötőcsoport mezőt. Ha nincsenek aktív összekötők a csoportban, figyelmeztetés jelenik meg. Ha nem lát figyelmeztetést, lépjen tovább, és ellenőrizze, hogy az összes szükséges port engedélyezett-e.
1. Ha nem a megfelelő összekötőcsoport jelenik meg, a legördülő menüsegítségével válassza ki a megfelelő csoportot, és ellenőrizze, hogy a továbbiakban nem jelennek-e figyelmeztetések. Ha a kívánt összekötőcsoport megjelenik, kattintson a figyelmeztető üzenetre a lap összekötőkezeléssel való megnyitásához.
1. Innen, van néhány módja annak, hogy fúrjon tovább:

   - Aktív összekötő áthelyezése a csoportba: Ha van egy aktív összekötő, amely tartozik ehhez a csoporthoz, és a cél háttéralkalmazás, áthelyezheti az összekötő a hozzárendelt csoportba. Ehhez kattintson az összekötőre. Az "Összekötőcsoport" mezőben a legördülő menüben válassza ki a megfelelő csoportot, majd kattintson a Mentés gombra.
   - Új összekötő letöltése az adott csoporthoz: Ezen a lapon kaphatja meg az [új összekötő letöltésére](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/Download)szolgáló hivatkozást. Telepítse a csatlakozót egy olyan gépre, amely közvetlen rálátással rendelkezik a háttéralkalmazásra. Az összekötő általában ugyanarra a kiszolgálóra van telepítve, mint az alkalmazás. A letöltési összekötő hivatkozás segítségével töltse le az összekötőt a célgépre. Ezután kattintson az összekötőre, és az "Összekötőcsoport" legördülő menüsegítségével győződjön meg arról, hogy a megfelelő csoporthoz tartozik.
   - Inaktív összekötő vizsgálata: Ha egy összekötő inaktívként jelenik meg, nem tudja elérni a szolgáltatást. Ez a hiba általában annak köszönhető, hogy néhány szükséges port blokkolva van. A probléma megoldásához lépjen tovább, és ellenőrizze, hogy az összes szükséges port engedélyezett-e.

Miután ezeket a lépéseket, hogy megbizonyosodjon arról, az alkalmazás van rendelve egy csoport működő összekötők, tesztelje újra az alkalmazást. Ha még mindig nem működik, folytassa a következő szakasszal.

## <a name="check-all-required-ports-are-open"></a>Ellenőrizze, hogy minden szükséges port nyitva van-e

Annak ellenőrzéséhez, hogy az összes szükséges port nyitva van-e, olvassa el a portok megnyitásáról szóló dokumentációt. Ha az összes szükséges port nyitva van, lépjen a következő szakaszra.

## <a name="check-for-other-connector-errors"></a>További összekötőhibák ellenőrzése

Ha a fentiek egyike sem oldja meg a problémát, a következő lépés az összekötővel kapcsolatos problémák vagy hibák megkeresése. Néhány gyakori hiba a [Dokumentumelhárításban](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#connector-errors)látható.

A hibák azonosításához közvetlenül az összekötő naplók is megnézheti. A hibaüzenetek nagy része konkrét javaslatokat tartalmaz a javításokhoz. A naplók megtekintéséhez olvassa el az [összekötők dokumentációját.](application-proxy-connectors.md#under-the-hood)

## <a name="additional-resolutions"></a>További állásfoglalások

Ha a fenti nem oldotta meg a problémát, van néhány különböző lehetséges oka. A probléma azonosítása:

Ha az alkalmazás integrált Windows-hitelesítés (IWA) használatára van konfigurálva, tesztelje az alkalmazást egyszeri bejelentkezés nélkül. Ha nem, lépjen a következő bekezdésre. Ha egyszeri bejelentkezés nélkül szeretné ellenőrizni az alkalmazást, nyissa meg az alkalmazást az **Enterprise Applications alkalmazáson keresztül,** és lépjen az **Egyszeri bejelentkezés menübe.** Módosítsa a legördülő menüt "Integrált Windows-hitelesítés" értékről "Az Azure AD egyszeri bejelentkezés letiltva" értékre.

Most nyisson meg egy böngészőt, és próbálja meg újra elérni az alkalmazást. A rendszer kéri a hitelesítést, és lépjen be az alkalmazásba. Ha képes hitelesíteni, a probléma a Kerberos korlátozott delegálás (KCD) konfigurációval van, amely lehetővé teszi az egyszeri bejelentkezést. További információt a KCD hibaelhárítási oldalán talál.

Ha továbbra is megjelenik a hiba, lépjen a géphez, ahol az összekötő telepítve van, nyisson meg egy böngészőt, és próbálja meg elérni az alkalmazáshoz használt belső URL-címet. Az összekötő úgy viselkedik, mint egy másik ügyfél ugyanattól a géptől. Ha nem tudja elérni az alkalmazást, vizsgálja meg, hogy a gép miért nem tudja elérni az alkalmazást, vagy használjon összekötőt egy olyan kiszolgálón, amely képes elérni az alkalmazást.

Ha el tudja érni az alkalmazást arról a gépről, hogy keresse meg a problémákat vagy hibákat az összekötő vel. Néhány gyakori hiba a [Dokumentumelhárításban](application-proxy-troubleshoot.md#connector-errors)látható. A hibák azonosításához közvetlenül az összekötő naplók is megnézheti. Számos hibaüzenetünk képes a javításokra vonatkozó konkrétabb javaslatok megosztására. A naplók megtekintéséről az [összekötők dokumentációjában](application-proxy-connectors.md#under-the-hood)olvashat.

## <a name="next-steps"></a>További lépések

[Az Azure AD alkalmazásproxy-összekötők megismerése](application-proxy-connectors.md)
