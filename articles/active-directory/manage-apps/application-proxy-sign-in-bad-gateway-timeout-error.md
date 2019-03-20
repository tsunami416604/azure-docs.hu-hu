---
title: Nincs hozzáférése a vállalati alkalmazás hiba Application Proxy-alkalmazások használata során |} A Microsoft Docs
description: Tudnivalók az Azure AD-alkalmazásproxy alkalmazásokkal kapcsolatos gyakori hozzáférési problémák megoldásához.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8db5552ad81a1a47db72a5372e643a6fc167888
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58091205"
---
# <a name="cant-access-this-corporate-application-error-when-using-an-application-proxy-application"></a>Az Application Proxy-alkalmazások használata esetén "Nem tud hozzáférni a vállalati alkalmazás" hiba

Ez a cikk segít az Azure AD-alkalmazásproxy-alkalmazáshoz a "Vállalati alkalmazás nem érhető el" hiba gyakori problémáinak elhárítása.

## <a name="overview"></a>Áttekintés
Ezt a hibaüzenetet, ha a hiba lapon keresse meg az állapotkódot. Ezt a kódot, valószínűleg az egyik, a következő állapotkódok:

-   **Átjáró időtúllépése**: Az alkalmazásproxy-szolgáltatás nem tudja elérni az összekötőt. Ez a hiba általában azt jelzi, hogy az összekötő hozzárendelés, összekötő, a probléma, vagy a hálózati szabályok az összekötő körül.

-   **Hibás átjáróval**: Az összekötő nem tudja elérni a háttéralkalmazás. Ez a hiba oka lehet az alkalmazás egy hibás.

-   **Tiltott**: A felhasználó nem jogosult az alkalmazás eléréséhez. Ez a hiba akkor fordulhat elő, ha a felhasználó nincs hozzárendelve az alkalmazáshoz az Azure Active Directoryban, vagy ha a háttérkiszolgálón a felhasználónak nincs engedélye az alkalmazás eléréséhez.

A kód megkereséséhez tekintse meg a szöveg a "Állapotkód:" mező a hibaüzenet bal alsó. Kereshet is minden további tippek a lap alján.

   ![Átjáró időtúllépése hiba](./media/application-proxy-sign-in-bad-gateway-timeout-error/connection-problem.png)

Ezek a hibák okának elhárítása részleteket és további információt a javasolt javítások tekintse meg a megfelelő szakaszt.

## <a name="gateway-timeout-errors"></a>Átjáró időtúllépése hibái

Egy átjáró időtúllépése akkor fordul elő, ha a szolgáltatás megpróbálja elérni az összekötőt, és nem tudja az időkereten belül. Ez a hiba oka általában egy adott alkalmazás nem működő összekötők olyan Összekötőcsoportot, vagy az összekötő által használt bizonyos portokat nem nyílt.


## <a name="bad-gateway-errors"></a>Hibás átjáróval kapcsolatos hibák

A hibás átjáró hiba azt jelzi, hogy az összekötő nem érhető el a háttéralkalmazás. Győződjön meg arról, hogy közzétette-e a megfelelő alkalmazáshoz. Ezt a hibát okozó gyakori hibák a következők:

-   Egy gépelési vagy a belső URL-cím található hiba

-   Nem teszi közzé az alkalmazás. Például közzétételi <http://expenses/reimbursement> , de elérésére tett <http://expenses>

-   A Kerberos által korlátozott delegálás (KCD) konfigurálásával kapcsolatos problémák

-   A háttéralkalmazás kapcsolatos problémák

## <a name="forbidden-errors"></a>Tiltott hibák

A tiltott hibát látja, ha a felhasználó nincs hozzárendelve az alkalmazáshoz. Ez a hiba lehet az Azure Active Directoryban vagy a háttéralkalmazás.

Felhasználók hozzárendelése az Azure-ban az alkalmazás kapcsolatban lásd: a [konfigurációs dokumentációt](application-proxy-add-on-premises-application.md#test-the-application).

Ha Ön kijelenti, hogy a felhasználó hozzá van rendelve az alkalmazás az Azure-ban, ellenőrizze a háttéralkalmazás az felhasználó konfigurációját. Ha a Kerberos által korlátozott delegálás vagy integrált Windows-hitelesítést használ, lásd a KCD hibaelhárítása vonatkozó irányelveket.

## <a name="check-the-applications-internal-url"></a>Az alkalmazás belső URL-cím ellenőrzése

Első gyors lépésben ellenőrizze és javítsa ki a belső URL-cím megnyitásával az alkalmazást **vállalati alkalmazások**, majd válassza a **alkalmazásproxy** menü. Ellenőrizze, hogy a belső URL-címe, a helyszíni hálózat az alkalmazás eléréséhez használt.

## <a name="check-the-application-is-assigned-to-a-working-connector-group"></a>Ellenőrizze, hogy az alkalmazás hozzá van rendelve egy működő Összekötőcsoport

Ellenőrizze az alkalmazás hozzá van rendelve egy működő Összekötőcsoport:

1. Nyissa meg az alkalmazás a portálon a **Azure Active Directory**, kattintson a **vállalati alkalmazások**, majd **összes alkalmazáshoz.** Nyissa meg az alkalmazást, majd válassza ki **alkalmazásproxy** a bal oldali menüből.

2. Tekintse meg a Összekötőcsoport mező. Ha a csoport nincs aktív összekötő, megjelenik egy figyelmeztetés. Ha nem látja a figyelmeztetéseket, helyezze át a "Ellenőrizze-e minden szükséges port szerepel az engedélyezési listán".

3. Ha nem a megfelelő Összekötőcsoport jelennek meg, használatával a listából válassza ki a megfelelő csoportot, majd erősítse meg, nem lesznek láthatók a figyelmeztetéseket. Ha az importálni kívánt Összekötőcsoport jelenik-e meg, kattintson a figyelmeztető üzenetet, nyissa meg a-összekötő felügyeleti.

4. Itt van néhány módon lehet további:

   * Egy aktív összekötőt áthelyezni a csoportot: Ha egy aktív összekötőt, amely ehhez a csoporthoz kell tartoznia, és a háttérrendszer célalkalmazásnak üzemel, továbbléphet az összekötő a hozzárendelt csoportba. Ehhez kattintson arra az összekötőre. A "Összekötőcsoport" mezőjében használatával a listából válassza ki a megfelelő csoportba, és kattintson a Mentés gombra.

   * Az adott csoporthoz új összekötő letöltéséhez: A hivatkozásra kattintva megjelenik ezen az oldalon [új összekötő letöltéséhez](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/Download). Telepítse az egy gépen a közvetlen üzemel, a háttéralkalmazás. Általában az összekötő telepítve van az alkalmazás ugyanazon a kiszolgálón. A letöltési hivatkozás összekötő használatával a célgépen alakzatot összekötő letöltéséhez. Ezután kattintson az összekötőt, és a "Összekötő csoport" legördülő menü használatával ellenőrizze, hogy a megfelelő csoporthoz tartozik.

   * Az inaktív csatlakozó vizsgálata: Egy összekötő akkor jeleníti meg inaktívként, ha az nem érhető el a szolgáltatást. Ez a hiba általában néhány szükséges portokat nem blokkolja miatt nem lehetséges. A probléma megoldásához, helyezze át a "Ellenőrizze minden szükséges port szerepel az engedélyezési listán."

Használata után ezeket a lépéseket, az alkalmazás hozzá van rendelve egy csoport használata összekötők, hogy tesztelje újból az alkalmazást. Ha nem továbbra is működik, továbbra is a következő szakaszban.

## <a name="check-all-required-ports-are-whitelisted"></a>Ellenőrizze, szerepel az engedélyezési listán-e minden szükséges port

Annak ellenőrzéséhez, hogy minden szükséges portok nyitva, a portok megnyitása a-dokumentációjában talál. Ha a szükséges portok nyitva, helyezze át a következő szakaszban.

## <a name="check-for-other-connector-errors"></a>Más összekötők hibák keresése

Ha a fentiek egyike a problémát, a következő lépés az problémák és hibák az összekötővel saját maga. Láthatja, hogy a gyakran előforduló hibákat a [hibaelhárítás dokumentum](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#connector-errors). 

Kereshet közvetlenül a naplókban összekötő azonosítani az esetleges hibákat is. A hibaüzenetek számos ossza meg a javítások konkrét javaslatokért. A naplók megtekintéséhez lásd a [összekötők dokumentációja](application-proxy-connectors.md#under-the-hood).

## <a name="additional-resolutions"></a>További megoldások

Ha a fenti nem oldja meg a probléma, néhány másik lehetséges oka is van. A probléma azonosításához:

Ha az alkalmazás integrált Windows-hitelesítés (IWA) használatára van konfigurálva, tesztelje az alkalmazás egyszeri bejelentkezés nélkül. Ha nem, a következő bekezdésben helyezze át. Egyszeri bejelentkezés nélkül az alkalmazás ellenőrzéséhez nyissa meg az alkalmazás **, vállalati alkalmazások** és nyissa meg a **egyszeri bejelentkezés** menü. Módosítsa a listában "Integrált Windows-hitelesítés" a "Azure AD egyszeri bejelentkezés letiltva". 

Most nyisson meg egy böngészőt, és próbálja meg újból elérni az alkalmazást. A rendszer kéri a hitelesítéshez és az alkalmazások beolvasása. Ha Ön elvégezheti a hitelesítést, akkor a probléma, a Kerberos által korlátozott delegálás (KCD) konfigurációval, amely lehetővé teszi az egyszeri bejelentkezést. További információkért lásd a kcd Szolgáltatáshoz hibaelhárítása.

Tekintse meg a hiba továbbra is, ha nyissa meg a gép, ahol az összekötő telepítve van-e, nyisson meg egy böngészőt, és próbálják elérni a belső URL-cím az alkalmazáshoz használt. Az összekötő úgy viselkedik, mint egy másik ügyfél ugyanazt a gépet. Ha az alkalmazás nem érhető el, vizsgálja meg, miért, hogy a gép nem tudja elérni az alkalmazást, vagy az összekötő használata a kiszolgálón, amely képes elérni az alkalmazást.

Ha az alkalmazás arról a gépről keresse meg az problémák és hibák az összekötővel saját maga is elérheti. Láthatja, hogy a gyakran előforduló hibákat a [hibaelhárítás dokumentum](application-proxy-troubleshoot.md#connector-errors). Kereshet közvetlenül a naplókban összekötő azonosítani az esetleges hibákat is. A hibaüzenetek számos lehet megosztani a javítások több konkrét javaslatokért. A naplók megtekintéséhez, lásd: [összekötők dokumentációnk](application-proxy-connectors.md#under-the-hood).

## <a name="next-steps"></a>További lépések
[Az Azure AD-alkalmazásproxy-összekötők ismertetése](application-proxy-connectors.md)
