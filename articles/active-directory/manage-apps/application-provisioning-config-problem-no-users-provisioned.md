---
title: Nincsenek felhasználók sem lett létrehozva az Azure AD katalógusából származó alkalmazásba való |} A Microsoft Docs
description: Szembesülnek, ha nem lát megjelenjenek az Azure AD katalógusából származó alkalmazásba konfigurálta a felhasználókiépítés Azure AD-val a felhasználók gyakori hibáinak elhárítása
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 181f8488255bca67131ed32474bb05a9d6d082aa
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55154983"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Nincsenek felhasználók sem lett létrehozva, egy Azure AD katalógusából származó alkalmazásba
Miután az Automatikus kiépítés konfigurálva lett az alkalmazás (beleértve az ellenőrzése, hogy a alkalmazás Azure ad-hez való csatlakozáshoz az alkalmazás megadott hitelesítő adatok érvényesek), majd felhasználók és csoportok lesznek kiépítve, az alkalmazást. Kiépítés határozza meg az alábbiakat:

-   Amelyek a felhasználók és csoportok voltak **hozzárendelt** az alkalmazáshoz. A hozzárendelés további információkért lásd: [egy felhasználó vagy csoport hozzárendelése az Azure Active Directory vállalati alkalmazás](assign-user-or-group-access-portal.md).
-   E **attribútum-leképezések** engedélyezve van, és úgy konfigurálva, hogy az alkalmazás Azure AD-ből érvényes attribútumok szinkronizálása. Attribútum-leképezéshez további információkért lásd: [testreszabása felhasználói kiépítés attribútum-leképezéshez az SaaS-alkalmazásokhoz az Azure Active Directoryban](customize-application-attributes.md).
-   Van-e egy **Hatókörszűrő** jelen, a szűrés a felhasználók adott attribútum értékei alapján. A szűrők felmerülő további információkért lásd: [attribútum-alapú alkalmazások üzembe helyezése és hatókörének beállítása szűrőket](define-conditional-rules-for-provisioning-user-accounts.md).
  
Ha a megfigyelte felhasználó sem lett létrehozva, tekintse meg a naplók az Azure ad-ben. Keressen rá egy adott felhasználó-bejegyzései.

Az üzembe helyezési naplók elérhetők az Azure Portalon a **Azure Active Directory &gt; vállalati alkalmazások &gt; \[alkalmazásnév\] &gt; Auditnaplók** fülre. A naplók szűrése a **fiók üzembe helyezésének** kategóriát csak az alkalmazás az üzembe helyezési események megtekintéséhez. A felhasználók számára a "egyező azonosító", amelyet a számukra az attribútumleképezések alapján kereshet. Például, ha konfigurálta a "felhasználó egyszerű neve" vagy "e-mail-cím", a megfelelő attribútum az Azure ad-ben oldalán, és a felhasználó nem kiépítése, amelynek értéke "audrey@contoso.com", majd keresse meg a naplófájlok "audrey@contoso.com", és tekintse át a bejegyzéseket adott vissza.

Az üzembe helyezési naplók a kiépítési szolgáltatás, beleértve a lekérdezése az Azure AD-kiépítés, ezek a felhasználók létezik-e a célalkalmazásnak lekérdezése, a felhasználói objektumok összehasonlítása hatókörébe hozzárendelt felhasználók által végrehajtott összes műveletet rekord között a rendszer. Majd adja hozzá, frissítésére, vagy tiltsa le a felhasználói fiókot a célrendszeren, az összehasonlítás alapján.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Kiépítési kell figyelembe venni az általános problémás területek
Az alábbi, az általános problémás területek, amely részletesen is megtekintheti, ha van egy ötlete, hol kell elkezdeni listáját.

- [A kiépítési szolgáltatás nem jelenik meg indítása](#provisioning-service-does-not-appear-to-start)
- [Auditnaplók tegyük fel, hogy felhasználók kihagyva, és nincs kiépítve, még akkor is, ha hozzá vannak rendelve](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>A kiépítési szolgáltatás nem jelenik meg indítása
Ha a **üzembe helyezési állapotra** kell **a** a a **Azure Active Directory &gt; vállalati alkalmazások &gt; \[alkalmazásnév\] &gt;Kiépítési** szakaszában az Azure Portalon. Azonban nem más állapot részletei jelennek meg az oldal után következő újra betölti, valószínű, hogy a szolgáltatás fut, de a kezdeti szinkronizálás még nem fejeződött be. Ellenőrizze a **Auditnaplók** határozza meg, milyen műveleteket hajt végre a szolgáltatás, a fent leírt, és ha bármilyen hiba merül fel.

>[!NOTE]
>Egy kezdeti szinkronizálást is eltarthat, 20 percet vagy akár néhány órát, a kiépítés hatókörébe felhasználók száma és az Azure AD-címtár méretétől függően. A kezdeti szinkronizálást követően ezt követő szinkronizálások gyorsabb, mert a regisztrációs szolgáltatás, amely a kezdeti szinkronizálást követően a két rendszer állapotát képviselik a vízjelek tárolja. A kezdeti szinkronizálás javítja a teljesítményt az ezt követő szinkronizálások.
>


## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Auditnaplók tegyük fel, hogy felhasználók kihagyva, és nincs kiépítve, még akkor is, ha hozzá vannak rendelve

Amikor megjelenik a felhasználó-e, a "kihagyva" a naplók, fontos a kiterjesztett részleteket a naplóüzenet okának megállapításához. Az alábbiakban gyakori okok és a megoldásuk:

- **Egy hatókörszűrőt konfigurálták** **, amely a kiszűri a felhasználó egy attribútumérték alapján**. A szűrők felmerülő további információkért lásd: [hatókörének beállítása szűrőket](define-conditional-rules-for-provisioning-user-accounts.md).
- **A felhasználó nem "nem hatékony jogosult".** Ha az adott hibaüzenet jelenik meg, mivel probléma adódott az Azure AD-ben tárolt felhasználó-hozzárendelés bejegyzést. A probléma megoldásához, hozzárendelésének megszüntetése a felhasználó (vagy csoport) az alkalmazásból, és kiossza újra. A hozzárendelés további információkért lásd: [felhasználó vagy csoport hozzáférés hozzárendelése](assign-user-or-group-access-portal.md).
- **Egy kötelező attribútum hiányzik vagy nem ki van töltve, egy felhasználó.** Egy lényeges tudnivaló, hogy beállítása a kiépítés során vegye figyelembe, hogy tekintse át, és az attribútumleképezések és a munkafolyamatok, amelyek meghatározzák, mely felhasználó (vagy csoport) tulajdonságai a folyamat az Azure ad-ből az alkalmazás konfigurálása. A jelen konfiguráció tartalmazza a beállítás a "egyező tulajdonság", amely egyedileg azonosít, és a megfelelő felhasználók vagy csoportok a két rendszer közötti szolgál. Ez fontos folyamattal kapcsolatban további információkért lásd: [testreszabása felhasználói kiépítés attribútum-leképezéshez az SaaS-alkalmazásokhoz az Azure Active Directoryban](customize-application-attributes.md).
- **Attribútum-leképezéshez csoportok:** Ha támogatja az egyes alkalmazások kiépítése a csoport nevét és a csoport részletei mellett a tagok. Engedélyezheti vagy letilthatja ezt a funkciót engedélyezésével vagy letiltásával a **leképezési** csoport objektumainak látható a **kiépítési** fülre. Csoportok kiépítése engedélyezve van, ha mindenképpen tekintse át a annak érdekében, hogy a megfelelő mezőben használatos a "egyező ID" attribútum-leképezéshez. A megfelelő azonosító a megjelenítési név vagy e-mail-alias lehet. A csoportot és annak tagjait kiépítése nem történt-e a megfelelő tulajdonság üres vagy nem feltöltött csoport az Azure ad-ben.

## <a name="next-steps"></a>További lépések

[Az Azure AD Connect szinkronizálása: Deklaratív kiépítés ismertetése](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
