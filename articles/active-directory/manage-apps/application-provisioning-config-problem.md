---
title: A probléma az Azure AD katalógusából származó alkalmazásba történő felhasználókiépítés konfigurálása |} A Microsoft Docs
description: Amikor konfigurálása felhasználókiépítés egy alkalmazás már szerepel az Azure AD Alkalmazáskatalógusában megmérkőzött gyakori hibáinak elhárítása
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
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61739d381d5c668b5f1a9467b10398d173601b33
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56178532"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>A probléma az Azure AD katalógusából származó alkalmazásba történő felhasználókiépítés konfigurálása

Konfigurálás [felhasználók automatikus átadása](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning) egy alkalmazás (ha támogatott), megköveteli, hogy a konkrét utasításokat kell követni az alkalmazás automatikus üzembe helyezés előkészítéséhez. Ezután használhatja az Azure Portalon, konfigurálja a kiépítési szolgáltatást, az alkalmazás felhasználói fiókok szinkronizálása.

A telepítő az oktatóanyag az alkalmazáshoz tartozó kiépítés beállítására vonatkozó felderítésével mindig webalkalmazásokba. Ezután kövesse ezeket a lépéseket, alkalmazás és az üzembe helyezési kapcsolat létrehozása az Azure AD konfigurálása. Alkalmazás oktatóanyagok listája található [oktatóanyagok listája SaaS-alkalmazások integrálása az Azure Active Directoryval való](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

## <a name="how-to-see-if-provisioning-is-working"></a>Hogy működik-e a kiépítési megtekintése 

Miután konfigurálta a szolgáltatást, a szolgáltatás működését a legtöbb betekintést két helyen lehet levonni:

-   **Auditnaplók** – a üzembe helyezési naplók rekordot a kiépítési szolgáltatás, többek között az Azure AD-lekérdezés által végrehajtott összes műveletet hozzárendelt felhasználók a kiépítés hatókörébe. A lekérdezés a felhasználókat, a felhasználói objektumok között a rendszer összehasonlítja létezik-e a célalkalmazással. Majd adja hozzá, frissítésére, vagy tiltsa le a felhasználói fiókot a célrendszeren, az összehasonlítás alapján. Az üzembe helyezési naplók elérhetők az Azure Portalon a **Azure Active Directory &gt; vállalati alkalmazások &gt; \[alkalmazásnév\] &gt; Auditnaplók** fülre. A naplók szűrése a **fiók üzembe helyezésének** kategóriát csak az alkalmazás az üzembe helyezési események megtekintéséhez.

-   **Kiépítési állapot –** összegzését az utolsó kiépítés futtatni egy adott alkalmazás látható az **Azure Active Directory &gt; vállalati alkalmazások &gt; \[alkalmazásnév\] &gt;Kiépítés** szakasz alatt a szolgáltatás beállításait a képernyő alján. Ez a szakasz foglalja össze, hogy hány felhasználó (és/vagy csoportok) jelenleg szinkronizálódnak a két rendszer közötti, és ha bármilyen hiba merül fel. A hiba részletei az auditnaplókban lehet. Vegye figyelembe, hogy a kiépítési állapotot nem kell feltöltenie mindaddig, amíg egy teljes kezdeti szinkronizálás befejezése között az Azure AD és az alkalmazás.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Általános problémás területek üzembe megfontolandó szempontok

Az alábbi, az általános problémás területek, amely részletesen is megtekintheti, ha van egy ötlete, hol kell elkezdeni listáját.

* [A kiépítési szolgáltatás nem jelenik meg indítása](#provisioning-service-does-not-appear-to-start)
* Nem lehet menteni a konfiguráció miatt nem működik alkalmazás hitelesítő adatok
* [Auditnaplók tegyük fel, hogy felhasználók "kihagyva", és nincs kiépítve, még akkor is, ha hozzá vannak rendelve](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>A kiépítési szolgáltatás nem jelenik meg indítása

Ha a **üzembe helyezési állapotra** kell **a** a a **Azure Active Directory &gt; vállalati alkalmazások &gt; \[alkalmazásnév\] &gt;Kiépítési** szakaszában az Azure Portalon. Azonban nincsenek más állapot részletei jelennek meg az oldal után az azt követő Újratölti. Valószínű, hogy a szolgáltatás fut, de a kezdeti szinkronizálás még nem fejeződött be. Ellenőrizze a **Auditnaplók** határozza meg, milyen műveleteket hajt végre a szolgáltatás, a fent leírt, és ha bármilyen hiba merül fel.

>[!NOTE]
>Egy kezdeti szinkronizálást is eltarthat, 20 percet vagy akár néhány órát, a kiépítés hatókörébe felhasználók száma és az Azure AD-címtár méretétől függően. A kezdeti szinkronizálást követően ezt követő szinkronizálások esetén gyorsabb, ahogy a kiépítési szolgáltatás tárolja, amely mindkét rendszer állapotát képviselik a ezt követő szinkronizálások teljesítményének növelése a kezdeti szinkronizálást követően a vízjelek.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>Nem lehet menteni a konfiguráció miatt nem működik alkalmazás hitelesítő adatok

Ahhoz, hogy az üzembe helyezés működik az Azure AD használatához szükséges érvényes hitelesítő adatokat, amelyek lehetővé teszik, hogy a felhasználókezelési, amelyet az alkalmazás által biztosított API-hoz csatlakozhat. Ha ezeket a hitelesítő adatokat nem működnek, vagy nem tudja őket, tekintse át a következő oktatóanyagban ezt az alkalmazást a korábban leírt beállítása.

## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Auditnaplók tegyük fel, hogy felhasználók kihagyva, és nincs kiépítve, még akkor is, ha hozzá vannak rendelve

A felhasználó megjelenik, mint "kihagyva" a naplók, esetén nagyon fontos, hogy a kiterjesztett részleteket a naplóüzenet okának megállapításához. Az alábbiakban gyakori okok és a megoldásuk:

-   **Egy hatókörszűrőt konfigurálták** **, amely a kiszűri a felhasználó egy attribútumérték alapján**. A szűrők felmerülő további információkért lásd: <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>.

-   **A felhasználó nem "nem hatékony jogosult".** Ha az adott hibaüzenet jelenik meg, mivel probléma adódott az Azure AD-ben tárolt felhasználó-hozzárendelés bejegyzést. Hárítsa el a problémát, megszüntetése a felhasználó (vagy csoport) az alkalmazásból, és újból rendelje hozzá újra. A hozzárendelés további információkért lásd: <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>.

-   **Egy kötelező attribútum hiányzik vagy nem ki van töltve, egy felhasználó.** Egy lényeges tudnivaló, hogy fontolja meg a kiépítési beállítása során kell, hogy tekintse át, és az attribútumleképezések és a munkafolyamatok, amelyek meghatározzák, mely felhasználó (vagy csoport) tulajdonságai a folyamat az Azure ad-ből az alkalmazás konfigurálása. Ez magában foglalja a "egyező tulajdonság" beállítás, amely egyedileg azonosíthatja és felhasználók/csoportok a két rendszer közötti megfelelő használható. Ez fontos folyamattal kapcsolatban további információkért lásd: <https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings>.

   * **Attribútum-leképezéshez csoportok:** Ha támogatja az egyes alkalmazások kiépítése a csoport nevét és a csoport részletei mellett a tagok. Engedélyezheti vagy letilthatja ezt a funkciót engedélyezésével vagy letiltásával a **leképezési** csoport objektumainak látható a **kiépítési** fülre. Csoportok kiépítése engedélyezve van, ha mindenképpen tekintse át a annak érdekében, hogy a megfelelő mezőben használatos a "egyező ID" attribútum-leképezéshez. Ez lehet a megjelenítési név vagy e-mail-alias), mivel a csoportot és annak tagjait nem építhető ki, ha a megfelelő tulajdonság értéke üres vagy nem feltöltött csoport az Azure ad-ben.

## <a name="next-steps"></a>További lépések
[Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](user-provisioning.md)
