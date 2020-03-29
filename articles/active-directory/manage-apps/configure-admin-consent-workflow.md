---
title: A rendszergazdai hozzájárulási munkafolyamat konfigurálása – Azure Active Directory | Microsoft dokumentumok
description: Ismerje meg, hogyan állíthatja be, hogyan kérheti a végfelhasználók számára a rendszergazdai jóváhagyást igénylő alkalmazásokhoz való hozzáférést.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83b3f0d97daf0b4ac17f74981119b380d1776d97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430200"
---
# <a name="configure-the-admin-consent-workflow-preview"></a>A rendszergazdai hozzájárulási munkafolyamat konfigurálása (előzetes verzió)

Ez a cikk ismerteti, hogyan engedélyezheti a rendszergazdai hozzájárulásmunkafolyamat (előzetes verzió) funkciót, amely lehetővé teszi a végfelhasználók számára, hogy rendszergazdai jóváhagyást igénylő alkalmazásokhoz kérjenek hozzáférést.

Rendszergazdai hozzájárulási munkafolyamat nélkül a bérlő, ahol a felhasználói hozzájárulás le van tiltva, le lesz tiltva, amikor megpróbál hozzáférni minden olyan alkalmazáshoz, amely a szervezeti adatok eléréséhez szükséges engedélyeket. A felhasználó egy általános hibaüzenetet lát, amely szerint nem fér hozzá az alkalmazáshoz, és kérjen segítséget a rendszergazdától. De gyakran a felhasználó nem tudja, kihez forduljon, ezért vagy feladják, vagy új helyi fiókot hoznak létre az alkalmazásban. Még akkor is, ha egy rendszergazda értesítést kap, nincs mindig egy egyszerűsített folyamat, amely segít a rendszergazdanak a hozzáférés engedélyezésében és a felhasználók értesítésében.
 
A rendszergazdai hozzájárulási munkafolyamat biztonságos hozzáférést biztosít a rendszergazdák jóváhagyását igénylő alkalmazásokhoz. Ha egy felhasználó megpróbál hozzáférni egy alkalmazáshoz, de nem tudja megadni a beleegyezését, rendszergazdai jóváhagyásiránti kérelmet küldhet. A kérést e-mailben küldjük el az ellenőrzőként kijelölt rendszergazdáknak. A véleményező lépéseket tesz a kéréssel, és a felhasználó értesítést kap a műveletről.

A kérelmek jóváhagyásához a véleményezőnek globális rendszergazdának, felhőalkalmazás-rendszergazdának vagy alkalmazás-rendszergazdának kell lennie. A véleményezőnek már rendelkeznie kell egy ilyen rendszergazdai szerepkörrel; Egyszerűen kijelöli őket, mint a recentöltő nem emelik a kiváltságokat.

## <a name="enable-the-admin-consent-workflow"></a>A rendszergazdai hozzájárulási munkafolyamat engedélyezése

A rendszergazdai hozzájárulási munkafolyamat engedélyezése és a véleményezők kiválasztása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) globális rendszergazdaként.
2. Kattintson a bal oldali navigációs menü tetején a Minden **szolgáltatás** elemre. Megnyílik **az Azure Active Directory-bővítmény.**
3. A szűrő keresőmezőjébe írja be az **"Azure Active Directory"** kifejezést, és válassza ki **az Azure Active Directory-elemet.**
4. A navigációs menüben kattintson a **Vállalati alkalmazások parancsra.** 
5. A **Kezelés csoportban**válassza a **Felhasználói beállítások lehetőséget.**
6. A **Rendszergazdai hozzájárulási kérelmek (Előzetes verzió)** csoportban állítsa be a **Felhasználók rendszergazdai beleegyezést kérnek azokhoz az alkalmazásokhoz, amelyekhez nem járulnak hozzá** az Igen **gombra.**

   ![Rendszergazdai hozzájárulási munkafolyamat-beállítások konfigurálása](media/configure-admin-consent-workflow/admin-consent-requests-settings.png)
 
6. Adja meg az alábbi beállításokat:

   * **Válassza ki a felhasználókat a rendszergazdai hozzájárulási kérelmek áttekintéséhez.** Válassza ki a munkafolyamat ellenőrzőit olyan felhasználók ból, akik rendelkeznek a globális rendszergazdával, a felhőalkalmazás-rendszergazdával és az alkalmazásrendszergazdai szerepkörökkal.
   * **A kiválasztott felhasználók e-mailértesítést kapnak a kérésekről.** Kérés esetén engedélyezze vagy tiltsa le az ellenőrzőknek küldött e-mail-értesítéseket.  
   * **A kiválasztott felhasználók lejárati emlékeztetőket kapnak.** Engedélyezze vagy tiltsa le az emlékeztető e-mail értesítéseket a véleményezőknek, amikor egy kérés hamarosan lejár.  
   * **A hozzájárulási kérelem (nap) után lejár.** Adja meg, hogy a kérelmek mennyi ideig maradjanak érvényesek.

7. Kattintson a **Mentés** gombra. A funkció engedélyezése akár egy órát is igénybe vehet.

> [!NOTE]
> A munkafolyamat ellenőrzőinek hozzáadásához vagy eltávolításához módosítsa a **Rendszergazdai hozzájárulási kérelmek ellenőrzőinek** listáját. Vegye figyelembe, hogy a szolgáltatás jelenlegi korlátozása az, hogy az ellenőrzők megőrizhetik a véleményezőként kijelölt kérelmek áttekintésének lehetőségét.

## <a name="how-users-request-admin-consent"></a>Hogyan kérik a felhasználók a rendszergazdai jóváhagyást?

A rendszergazdai hozzájárulási munkafolyamat engedélyezése után a felhasználók rendszergazdai jóváhagyást kérhetnek egy olyan alkalmazáshoz, amelyhez nem járulnak hozzá. A következő lépések a felhasználó által a jóváhagyás kérésekor tapasztalt felhasználói élményt ismertetik. 

1. A felhasználó megpróbál bejelentkezni az alkalmazásba.

2. Megjelenik **a jóváhagyás szükséges** üzenet. A felhasználó beírja az alkalmazáshoz való hozzáférés szükségességét, majd kiválasztja a **Jóváhagyás kérése lehetőséget.**

   ![Rendszergazdai hozzájárulás felhasználói kérelem és indoklás](media/configure-admin-consent-workflow/end-user-justification.png)

3. A **kérelem elküldött** üzenete megerősíti, hogy a kérelmet elküldték a rendszergazdának. Ha a felhasználó több kérelmet küld, csak az első kérés érkezik a rendszergazdához.

   ![Rendszergazdai hozzájárulás felhasználói kérelem és indoklás](media/configure-admin-consent-workflow/end-user-sent-request.png)

 4. A felhasználó e-mailben értesítést kap, ha a kérelmet jóváhagyják, elutasítják vagy letiltják. 

## <a name="review-and-take-action-on-admin-consent-requests"></a>Rendszergazdai hozzájárulási kérelmek áttekintése és művelete

A rendszergazdai hozzájárulási kérelmek áttekintése és művelet megtetézése:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) a rendszergazdai hozzájárulási munkafolyamat egyik regisztrált ellenőrzőjeként.
2. Válassza a Bal oldali navigációs menü tetején található **Minden szolgáltatás** lehetőséget. Megnyílik **az Azure Active Directory-bővítmény.**
3. A szűrő keresőmezőjébe írja be az **"Azure Active Directory"** kifejezést, és válassza ki az **Azure Active Directory-elemet.**
4. A navigációs menüben kattintson a **Vállalati alkalmazások parancsra.**
5. A **Tevékenység csoportban**válassza a **Rendszergazdai hozzájárulási kérelmek (előzetes verzió) lehetőséget.**

   > [!NOTE]
   > Az ellenőrzők csak azátvizsgálóként való kijelölésük után létrehozott rendszergazdai kérelmeket fogják látni.

1. Válassza ki a kért alkalmazást.
2. Tekintse át a kérelem részleteit:  

   * Ha meg szeretné tekinteni, hogy ki kér hozzáférést és miért, válassza a **Kért lapon** lehetőséget.
   * Ha meg szeretné tekinteni, hogy az alkalmazás milyen engedélyeket kér, válassza az **Engedélyek és beleegyezés ek áttekintése**lehetőséget.

8. Értékelje ki a kérelmet, és tegye meg a megfelelő lépéseket:

   * **A kérelem jóváhagyása**. A kérelem jóváhagyásához adjon rendszergazdai hozzájárulást az alkalmazáshoz. A kérelem jóváhagyását követően minden kérelmező értesítést kap arról, hogy hozzáférést kapott.  
   * **A kérés megtagadása**. A kérelem elutasításához meg kell adnia egy indoklást, amelyet minden kérelmező rendelkezésére bocsát. A kérelem megtagadása után minden kérelmező értesítést kap arról, hogy megtagadták tőlük az alkalmazáshoz való hozzáférést. A kérelem megtagadása nem akadályozza meg a felhasználókat abban, hogy a jövőben ismét rendszergazdai hozzájárulást kérjenek az alkalmazáshoz.  
   * **Tiltsa le a kérelmet**. A kérelem letiltásához meg kell adnia egy indoklást, amelyet minden kérelmező nek meg kell adnia. A kérelem letiltása után az összes kérelmező értesítést kap arról, hogy megtagadták az alkalmazáshoz való hozzáférést. A kérelem blokkolása létrehoz egy egyszerű szolgáltatás objektumot az alkalmazás a bérlőben egy letiltott állapotban. A felhasználók a jövőben nem kérhetnek rendszergazdai hozzájárulást az alkalmazáshoz.
 
## <a name="email-notifications"></a>E-mail-értesítések
 
Ha be van állítva, minden véleményező e-mailértesítést kap, ha:

* Új kérelem jött létre
* Egy kérelem lejárt
* Egy kérelem közeledik a lejárati dátumhoz  
 
A kérelmezők e-mailben értesítést kapnak, ha:

* Új hozzáférési kérelmet nyújtanak be
* A kérésük lejárt.
* Kérésüket elutasították vagy letiltották
* Kérelmüket jóváhagyták.
 
## <a name="audit-logs"></a>Naplók 
 
Az alábbi táblázat ismerteti a rendszergazdai hozzájárulási munkafolyamathoz rendelkezésre álló forgatókönyveket és naplózási értékeket. 

> [!NOTE]
> A naplózási szereplő felhasználói környezete jelenleg minden esetben hiányzik. Ez egy ismert korlátozás az előzetes verzióban.


|Forgatókönyv  |Naplózási szolgáltatás  |Naplózási kategória  |Ellenőrzési tevékenység  |Könyvvizsgálati szereplő  |Napló korlátai  |
|---------|---------|---------|---------|---------|---------|
|Rendszergazda a hozzájáruláskérési munkafolyamat engedélyezéséhez        |Hozzáférési felülvizsgálatok           |UserManagement (Felhasználókezelése)           |Cégirányítási sablon létrehozása          |Alkalmazás környezete            |Jelenleg nem találja a felhasználói környezetet            |
|Rendszergazda letiltása a hozzájárulási kérelem munkafolyamat       |Hozzáférési felülvizsgálatok           |UserManagement (Felhasználókezelése)           |Cégirányítási házirendsablon törlése          |Alkalmazás környezete            |Jelenleg nem találja a felhasználói környezetet           |
|Rendszergazda a hozzájárulási munkafolyamat-konfigurációk frissítése        |Hozzáférési felülvizsgálatok           |UserManagement (Felhasználókezelése)           |Cégirányítási házirendsablon frissítése          |Alkalmazás környezete            |Jelenleg nem találja a felhasználói környezetet           |
|A végfelhasználó rendszergazdai hozzájárulási kérelmet hoz létre egy alkalmazáshoz       |Hozzáférési felülvizsgálatok           |Szabályzat         |Kérelem létrehozása           |Alkalmazás környezete            |Jelenleg nem találja a felhasználói környezetet           |
|Rendszergazdai hozzájárulási kérelmet jóváhagyó véleményezők       |Hozzáférési felülvizsgálatok           |UserManagement (Felhasználókezelése)           |Az üzleti folyamatban lévő összes kérelem jóváhagyása          |Alkalmazás környezete            |Jelenleg nem találja a felhasználói környezetet vagy a rendszergazdai jóváhagyást kapott alkalmazásazonosítót.           |
|A rendszergazdai hozzájárulási kérelmet megtagadó ellenőrzők       |Hozzáférési felülvizsgálatok           |UserManagement (Felhasználókezelése)           |Az üzleti folyamatban lévő összes kérelem jóváhagyása          |Alkalmazás környezete            | Jelenleg nem találja annak az aktornak a felhasználói környezetét, amely megtagadta a rendszergazdai hozzájárulási kérelmet          |

## <a name="faq"></a>GYIK 

**Bekapcsoltam ezt a munkafolyamatot, de a funkció tesztelésekor miért nem látom az új "Jóváhagyás szükséges" üzenet, amely lehetővé teszi a hozzáférés kérését?**

A funkció bekapcsolása után akár 60 percig is eltarthat, amíg a végfelhasználók láthatják a frissítést. Ellenőrizheti, hogy a konfiguráció megfelelően lépett-e érvénybe, `https://graph.microsoft.com/beta/settings` ha megtekinti az **EnableAdminConsentRequests** értéket az API-ban.

**Véleményezőként miért nem látom az összes függőben lévő kérelmet?**

Az ellenőrzők csak azátvizsgálóként való kijelölésük után létrehozott rendszergazdai kérelmeket láthatják. Így ha a közelmúltban vették fel véleményezőként, nem fog látni olyan kérelmeket, amelyeket a hozzárendelés előtt hoztak létre.

**Véleményezőként miért látok több kérelmet ugyanarra az alkalmazásra vonatkozóan?**
  
Ha egy alkalmazásfejlesztő úgy konfigurálta az alkalmazást, hogy statikus és dinamikus beleegyezést használjon a végfelhasználó adataihoz való hozzáférés kéréséhez, két rendszergazdai hozzájárulási kérelmet fog látni. Az egyik kérelem a statikus engedélyeket, a másik pedig a dinamikus engedélyeket jelöli.

**Kérelmezőként ellenőrizhetem a kérésem állapotát?**  

Nem, egyelőre a kérelmezők csak e-mailes értesítéseken keresztül kaphatnak frissítéseket.

**Mint véleményező, lehetséges- e a kérelem jóváhagyása, de nem mindenki számára?**
 
Ha aggódik a rendszergazdai hozzájárulás megadása, és lehetővé teszi a bérlő összes felhasználója számára az alkalmazás használatát, azt javasoljuk, hogy tagadja meg a kérelmet. Ezután manuálisan adja meg a rendszergazdai jóváhagyást az alkalmazáshoz való hozzáférés korlátozásával a felhasználói hozzárendelés megkövetelésével, valamint a felhasználók vagy csoportok hozzárendelésével az alkalmazáshoz. További információ: [Metódusok és csoportok hozzárendelése.](methods-for-assigning-users-and-groups.md)

## <a name="next-steps"></a>További lépések

Az alkalmazásokhoz való hozzájárulásról az [Azure Active Directory hozzájárulási keretrendszerben](../develop/consent-framework.md)talál további információt.

[Annak konfigurálása, hogy a végfelhasználók hogyan járuljanak hozzá az alkalmazásokhoz](configure-user-consent.md)

[Bérlői rendszergazdai hozzájárulás megadása egy alkalmazáshoz](grant-admin-consent.md)

[Engedélyek és hozzájárulás a Microsoft identitásplatformján](../develop/active-directory-v2-scopes.md)

[Azure AD a StackOverflow-n](https://stackoverflow.com/questions/tagged/azure-active-directory)
