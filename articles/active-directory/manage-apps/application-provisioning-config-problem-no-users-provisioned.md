---
title: Nincsenek felhasználók sem lett létrehozva az Azure AD katalógusából származó alkalmazásba való |} A Microsoft Docs
description: Gyakori hibáinak elhárítása szembesülnek, ha nem lát megjelenjenek a felhasználók olyan Azure ad katalógusából származó alkalmazásba konfigurálta a felhasználókiépítés Azure AD-vel
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/04/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 813dc8686edc94bdaad8067a3395251a91051cb3
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44356664"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Nincsenek felhasználók sem lett létrehozva, egy Azure AD katalógusából származó alkalmazásba

Ha az automatikus üzembe helyezés sikeresen beállítva az alkalmazás (beleértve az ellenőrzése, hogy a alkalmazás Azure ad-hez való csatlakozáshoz az alkalmazás megadott hitelesítő adatok érvényesek). Ezután a felhasználók és csoportok érvénybe lépnek az alkalmazásra, és határozza meg az alábbiakat:

-   Amelyek a felhasználók és csoportok voltak **hozzárendelt** az alkalmazáshoz. A hozzárendelés további információkért lásd: [egy felhasználó vagy csoport hozzárendelése az Azure Active Directory vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

-   E **attribútum-leképezések** engedélyezve van, és úgy konfigurálva, hogy az alkalmazás Azure AD-ből érvényes attribútumok szinkronizálása. Attribútum-leképezéshez további információkért lásd: [testreszabása felhasználói kiépítés attribútum-leképezéshez az SaaS-alkalmazásokhoz az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings).

-   Van-e egy **Hatókörszűrő** jelen, a szűrés a felhasználók adott attribútum értékei alapján. A szűrők felmerülő további információkért lásd: [attribútum-alapú alkalmazások üzembe helyezése és hatókörének beállítása szűrőket](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

Figyelje a felhasználó sem lett létrehozva, amikor tekintse át a naplókat az Azure ad-ben, és keresse meg egy adott felhasználó-bejegyzései.

Az üzembe helyezési naplók elérhetők az Azure Portalon a **Azure Active Directory &gt; vállalati alkalmazások &gt; \[alkalmazásnév\] &gt; Auditnaplók** fülre. A naplók szűrése a **fiók üzembe helyezésének** kategóriát csak az alkalmazás az üzembe helyezési események megtekintéséhez. A felhasználók számára a "egyező azonosító", amelyet a számukra az attribútumleképezések alapján kereshet. Például, ha konfigurálta a "felhasználó egyszerű neve" vagy "e-mail-cím", a megfelelő attribútum az Azure ad-ben oldalán, és a felhasználó nem kiépítése, amelynek értéke "audrey@contoso.com". Ezután a a naplók keresése "audrey@contoso.com" és a tekintse át, akkor a visszaadott bejegyzések.

Az üzembe helyezési naplók a kiépítési szolgáltatás, beleértve a lekérdezése az Azure AD-kiépítés, ezek a felhasználók létezik-e a célalkalmazásnak lekérdezése, a felhasználói objektumok összehasonlítása hatókörébe hozzárendelt felhasználók által végrehajtott összes műveletet rekord között a rendszer. Majd adja hozzá, frissítésére, vagy tiltsa le a felhasználói fiókot a célrendszeren, az összehasonlítás alapján.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Kiépítési kell figyelembe venni az általános problémás területek

Az alábbi, az általános problémás területek, amely részletesen is megtekintheti, ha van egy ötlete, hol kell elkezdeni listáját.

* [A kiépítési szolgáltatás nem jelenik meg indítása](#provisioning-service-does-not-appear-to-start)
* [Auditnaplók tegyük fel, hogy felhasználók kihagyva, és nincs kiépítve, még akkor is, ha hozzá vannak rendelve](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>A kiépítési szolgáltatás nem jelenik meg indítása

Ha a **üzembe helyezési állapotra** kell **a** a a **Azure Active Directory &gt; vállalati alkalmazások &gt; \[alkalmazásnév\] &gt;Kiépítési** szakaszában az Azure Portalon. Azonban nem más állapot részletei jelennek meg az oldal után következő újra betölti, valószínű, hogy a szolgáltatás fut, de a kezdeti szinkronizálás még nem fejeződött be. Ellenőrizze a **Auditnaplók** határozza meg, milyen műveleteket hajt végre a szolgáltatás, a fent leírt, és ha bármilyen hiba merül fel.

>[!NOTE]
>Egy kezdeti szinkronizálást is eltarthat, 20 percet vagy akár néhány órát, a kiépítés hatókörébe felhasználók száma és az Azure AD-címtár méretétől függően. A kezdeti szinkronizálást követően ezt követő szinkronizálások gyorsabb, mert a regisztrációs szolgáltatás, amely a kezdeti szinkronizálást követően a két rendszer állapotát képviselik a vízjelek tárolja. Ez javítja az ezt követő szinkronizálások teljesítményét.
>
>

## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Auditnaplók tegyük fel, hogy felhasználók kihagyva, és nincs kiépítve, még akkor is, ha hozzá vannak rendelve

A felhasználó megjelenik, mint "kihagyva" a naplók, esetén nagyon fontos, hogy a kiterjesztett részleteket a naplóüzenet okának megállapításához. Az alábbiakban gyakori okok és a megoldásuk:

-   **Egy hatókörszűrőt konfigurálták** **, amely a kiszűri a felhasználó egy attribútumérték alapján**. A szűrők felmerülő további információkért lásd: <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>.

-   **A felhasználó nem "nem hatékony jogosult".** Ha az adott hibaüzenet jelenik meg, mivel probléma adódott az Azure AD-ben tárolt felhasználó-hozzárendelés bejegyzést. Hárítsa el a problémát, megszüntetése a felhasználó (vagy csoport) az alkalmazásból, és újból rendelje hozzá újra. A hozzárendelés további információkért lásd: <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>.

-   **Egy kötelező attribútum hiányzik vagy nem ki van töltve, egy felhasználó.** Egy lényeges tudnivaló, hogy fontolja meg a kiépítési beállítása során kell, hogy tekintse át, és az attribútumleképezések és a munkafolyamatok, amelyek meghatározzák, mely felhasználó (vagy csoport) tulajdonságai a folyamat az Azure ad-ből az alkalmazás konfigurálása. Ez magában foglalja a "egyező tulajdonság" beállítás, amely egyedileg azonosíthatja és felhasználók/csoportok a két rendszer közötti megfelelő használható. Ez fontos folyamattal kapcsolatban további információkért lásd: [testreszabása felhasználói kiépítés attribútum-leképezéshez az SaaS-alkalmazásokhoz az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings).

  * **Attribútum-leképezések csoportok:** telepítése a csoport nevének és a csoport részletei mellett a tagokat, ha egyes alkalmazások esetében támogatott. Engedélyezheti vagy letilthatja ezt a funkciót engedélyezésével vagy letiltásával a **leképezési** csoport objektumainak látható a **kiépítési** fülre. Csoportok kiépítése engedélyezve van, ha mindenképpen tekintse át a annak érdekében, hogy a megfelelő mezőben használatos a "egyező ID" attribútum-leképezéshez. Ez lehet a megjelenítési név vagy e-mail-alias), mivel a csoportot és annak tagjait nem építhető ki, ha a megfelelő tulajdonság értéke üres vagy nem feltöltött csoport az Azure ad-ben.

## <a name="next-steps"></a>További lépések
[Az Azure AD Connect szinkronizálása: a deklaratív kiépítés ismertetése](../connect/active-directory-aadconnectsync-understanding-declarative-provisioning.md)

