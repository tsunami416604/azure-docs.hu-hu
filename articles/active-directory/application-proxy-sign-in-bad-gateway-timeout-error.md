---
title: Nem tud hozzáférni a vállalati alkalmazás hiba alkalmazásproxy alkalmazás használatakor |} Microsoft Docs
description: Az Azure AD alkalmazásproxy alkalmazásokkal közös hozzáférési problémák elhárításáról.
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
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: f57b470208ab3bd071b83c0736efa739c7e70363
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="cant-access-this-corporate-application-error-when-using-an-application-proxy-application"></a>"Nem tud hozzáférni a vállalati alkalmazás" hiba az alkalmazásproxy alkalmazás használatakor

Ez a cikk segítséget nyújt a "Vállalati alkalmazás nem érhető el" hiba történt az Azure AD-alkalmazásproxy kérelmet a gyakori problémák elhárításához.

## <a name="overview"></a>Áttekintés
Ha ezt a hibaüzenetet látja, keresés az állapotkód: a hiba lapon. Ezt a kódot, valószínűleg egy, a következő állapotkódok:

-   **Átjáró időtúllépése**: az alkalmazásproxy-szolgáltatás nem tudja elérni az összekötőt. Ez a hiba általában a connector hozzárendeléssel, összekötő kapcsolatos problémát jelez, vagy a hálózati szabályok az összekötő körül.

-   **Hibás átjáró**: az összekötő nem tudja elérni a háttéralkalmazás. Ez a hiba oka lehet az alkalmazás helytelen beállítása.

-   **Tiltott**: A felhasználó nem jogosult az alkalmazás eléréséhez. Ez a hiba akkor fordulhat elő, ha a felhasználó nincs hozzárendelve az alkalmazás Azure Active Directoryban, vagy ha a háttérkiszolgálón a felhasználónak nincs engedélye az alkalmazás eléréséhez.

A kód található, tekintse meg a hibaüzenet, a "Állapotkód:" mező a bal alsó szöveg. Bármely további tippek a lap alján is keres.

   ![Átjáró időtúllépési hiba](./media/application-proxy/connection-problem.png)

Megtudhatja, hogyan az alapvető ok, a hibák elhárítása és javasolt tartalmaz további részleteket lásd: az alábbi megfelelő szakaszához.

## <a name="gateway-timeout-errors"></a>Átjáró-időtúllépést

Átjáró időtúllépése következik be, ha a szolgáltatás megpróbálja elérni az összekötő, és nem tudja az időkereten belül. Ez a hiba oka rendszerint egy alkalmazás nem működő összekötőkkel összekötő-csoporthoz hozzárendelt, vagy az összekötő által igényelt bizonyos portok nincsenek megnyitva.


## <a name="bad-gateway-errors"></a>Hibás átjáró hibái

A hibás átjáró hiba azt jelzi, hogy az összekötő nem érhető el a háttéralkalmazás. Győződjön meg arról, hogy miután közzétette a megfelelő alkalmazás. Ezt a hibát okozó gyakori hibák a következők:

-   Egy elírás vagy a belső URL-címben hiba

-   Nem teszik közzé a az alkalmazás gyökérkönyvtárában. Például közzétételi <http://expenses/reimbursement> , de való hozzáférés közben <http://expenses>

-   A Kerberos által korlátozott delegálás (KCD) konfigurációjával kapcsolatos problémák

-   A háttér-alkalmazás

## <a name="forbidden-errors"></a>Tiltott hibák

Ha egy tiltott hibát látja, a felhasználó nincs hozzárendelve az alkalmazáshoz. Ez a hiba lehet, vagy az Azure Active Directoryban, vagy a háttéralkalmazás.

Felhasználók hozzárendelése az Azure-ban az alkalmazás további tudnivalókért lásd: a [konfigurációs dokumentáció](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal#add-a-test-user).

Ha meggyőződött róla, hogy a felhasználó hozzá van rendelve az alkalmazás az Azure-ban, ellenőrizze a háttér-alkalmazás felhasználói konfigurációját. Ha a Kerberos által korlátozott delegálás vagy integrált Windows-hitelesítést használ, lásd a Kerberos által korlátozott Delegálás hibaelhárítása kapcsolatos útmutatást.

## <a name="check-the-applications-internal-url"></a>Az alkalmazás belső URL-cím ellenőrzése

Az első Kész lépés, duplán jelölőnégyzet, és javítsa ki a belső URL-cím nyissa meg az alkalmazás a **vállalati alkalmazások**, jelölje be a **alkalmazásproxy** menü. Ellenőrizze a belső URL-cím a helyszíni hálózatból az alkalmazás eléréséhez használt.

## <a name="check-the-application-is-assigned-to-a-working-connector-group"></a>Ellenőrizze, hogy az alkalmazás hozzá van rendelve egy működő összekötő csoport

Ellenőrizze az alkalmazás hozzá van rendelve egy működő összekötő csoport:

1.  Nyissa meg az alkalmazást a portál a **Azure Active Directory**a gombra, majd **vállalati alkalmazások**, majd **összes alkalmazást.** Indítsa el az alkalmazást, majd válasszon **alkalmazásproxy** a bal oldali menüből.

2.  Tekintse meg az összekötő csoport mező. Ha nincs aktív összekötők a csoportban található, megjelenik egy figyelmeztetés. Ha nem látja a figyelmeztetéseket, helyezze át a "Ellenőrizze az összes szükséges portok szerepel az engedélyezési listán".

3.  Ha nem a megfelelő összekötő csoport jelenik meg, használja a legördülő listán válassza ki a megfelelő csoportot, és győződjön meg róla, többé nem láthatja a figyelmeztetéseket. Ha a kívánt összekötő csoport láthatók, kattintson a figyelmeztető üzenet lapjának megnyitásához az összekötő felügyeleti.

4.  Itt módon néhány további részletek:

  * Az aktív csatlakozó áthelyezi a csoporthoz: Ha az aktív csatlakozó, a csoporthoz kell tartoznia, és a háttér célalkalmazásnak történő sor a láthatáron, az összekötő áthelyezheti a hozzárendelt csoportjához. Ehhez kattintson az összekötőt. "Összekötő csoport" mezőben használja a legördülő listán válassza ki a megfelelő csoportot, és kattintson a Mentés gombra.

  * A csoport új összekötő letöltése: ezen a lapon, a hivatkozásra kattintva kaphat [új összekötő letöltése](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/Download). Telepítse az összekötőt a háttéralkalmazás közvetlen sor a láthatáron virtuális gép. Typicall az összekötő telepítve van az alkalmazás ugyanazon a kiszolgálón. A letöltés összekötő hivatkozás segítségével töltse le egy összekötőt, a célgépen. Ezután kattintson az összekötő, és a "összekötő csoport" legördülő győződjön meg arról, hogy a megfelelő csoporthoz tartozik.

  * Vizsgálja meg az inaktív csatlakozó: összekötő látható inaktívként, ha nem tudja elérni a szolgáltatást. Ezt a hibát általában az oka, hogy néhány szükséges portok blokkolja. A probléma megoldásához helyezze át a "Ellenőrizze az összes szükséges portok szerepel az engedélyezési listán."

Használata után ezeket a lépéseket, annak érdekében, hogy az alkalmazás hozzá van rendelve egy csoport számára, hogy működik-e összekötők, tesztelje újból az alkalmazást. Ha az eszköz még nem működik, folytassa a következő szakaszban.

## <a name="check-all-required-ports-are-whitelisted"></a>Ellenőrizze az összes szükséges portok szerepel az engedélyezési listán

Győződjön meg arról, hogy minden szükséges portok nyitva, olvassa el a dokumentációt a portok megnyitása. Ha a szükséges portok nyitva, helyezze át a következő szakaszban.

## <a name="check-for-other-connector-errors"></a>A többi összekötőt hibák

Ha egyik sem oldja meg a problémát, a következő lépés a problémákat, vagy maga az összekötő hibák kereséséhez. Néhány gyakori hibáinak láthatja a [kapcsolatos problémák elhárítása a dokumentum](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#connector-errors). 

Is megtalálhatja közvetlenül a Connector naplófájljaiban hibák azonosítására. A hibaüzenetek számos ossza meg a javítások konkrét javaslatokért. A naplók megtekintéséhez lásd a [összekötők dokumentációja](application-proxy-understand-connectors.md#under-the-hood).

## <a name="additional-resolutions"></a>További megoldások

Ha a fenti nem segít a problémán, néhány másik lehetséges oka is van. A probléma azonosításához:

Ha az alkalmazás integrált Windows-hitelesítéssel (IWA) használatára van konfigurálva, tesztelje az alkalmazás az egyszeri bejelentkezés nélkül. Ha nem, akkor helyezze át a következő bekezdés. Egyszeri bejelentkezés nélkül az alkalmazás ellenőrzéséhez nyissa meg az alkalmazás keresztül **vállalati alkalmazások** , és navigáljon a **egyszeri bejelentkezés** menü. Módosítsa a legördülő "Integrált Windows-hitelesítés" a "Azure AD az egyszeri bejelentkezés le van tiltva". 

Most nyisson meg egy böngészőt, és próbálja meg újra az alkalmazás eléréséhez. A hitelesítéshez a rendszer kéri, és feltölti az alkalmazást. Ha tudni hitelesítést végezni, a probléma van a Kerberos által korlátozott delegálás (KCD) konfigurációjához, amely lehetővé teszi, hogy az egyszeri bejelentkezést. További információkért lásd a Kerberos által korlátozott Delegálás hibaelhárítása.

Ha továbbra is találkozik a hibával, nyissa meg a gépen, amelyen telepítve van-e az az összekötő, nyisson meg egy böngészőt, és próbálják elérni az alkalmazás használt belső URL-cím. Az összekötő úgy viselkedik, mint egy másik ügyfél azonos gépen. Ha az alkalmazás nem érhető el, vizsgálja meg, miért, hogy a gép nem tudja elérni az alkalmazást, vagy használjon egy összekötő olyan kiszolgálóra, amely képes-e az alkalmazás eléréséhez.

Ha az alkalmazás maga az összekötő a hibák és problémák meg, hogy a gép érhető el. Néhány gyakori hibáinak láthatja a [kapcsolatos problémák elhárítása a dokumentum](active-directory-application-proxy-troubleshoot.md#connector-errors). Is megtalálhatja közvetlenül a Connector naplófájljaiban hibák azonosítására. A hibaüzenetek számos lehet megosztani a javítások több konkrét javaslatokért. A naplók megtekintéséhez, lásd: [összekötők dokumentációban](application-proxy-understand-connectors.md#under-the-hood).

## <a name="next-steps"></a>További lépések
[Az Azure AD-alkalmazásproxy összekötők ismertetése](application-proxy-understand-connectors.md)
