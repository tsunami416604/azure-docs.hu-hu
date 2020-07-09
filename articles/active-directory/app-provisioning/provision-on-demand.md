---
title: Igény szerinti felhasználó kiépítése Azure Active Directory használatával
description: Szinkronizálás kényszerítése
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 06/23/2020
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: 78a56b6a848139c47d7934a47decb126afe00b7a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85297528"
---
# <a name="on-demand-provisioning"></a>Igény szerinti kiépítés
Az igény szerinti kiépítés lehetővé teszi, hogy másodpercek alatt kiépítse a felhasználót egy alkalmazásba. A funkció segítségével gyorsan elháríthatja a konfigurációs problémákat, ellenőrizheti a definiált kifejezéseket, tesztelheti a hatóköri szűrőket, és még sok más funkciót is. 

## <a name="how-to-use-on-demand-provisioning"></a>Igény szerinti kiépítés használata 

1. Jelentkezzen be a **Azure Portalba**.
2. Navigáljon a **vállalati alkalmazásokhoz**.
3. Válassza ki az alkalmazást, és navigáljon a létesítési konfiguráció lapra.
4. Konfigurálja úgy a kiépítés konfigurálását, hogy megadja a rendszergazdai hitelesítő adatait.
5. Kattintson **az igény szerinti kiépítés**elemre.
6. Keressen egy felhasználót az utónév, a vezetéknév, a megjelenítendő név, a felhasználó egyszerű neve vagy az e-mail cím alapján.
7. Válassza a kiépítés elemet az oldal alján.

## <a name="understanding-the-provisioning-steps"></a>A kiépítési lépések ismertetése
Az igény szerinti kiépítési képesség megkísérli megjeleníteni a kiépítési szolgáltatás által a felhasználó kiépítéséhez szükséges lépéseket. A felhasználók üzembe helyezése általában öt lépésből áll, az alábbi lépések közül egy vagy több az igény szerinti üzembe helyezési élményben jelenik meg.

### <a name="step-1-test-connection"></a>1. lépés: a kapcsolatok tesztelése
A kiépítési szolgáltatás megkísérli engedélyezni a hozzáférést a célalkalmazás számára egy "tesztelési felhasználó" kérésének megtételével. A kiépítési szolgáltatás egy választ vár, amely azt jelzi, hogy a kiépítési lépésekkel való folytatásra engedélyt kapott. Ez a lépés csak akkor jelenik meg, ha hiba történt a lépésben. Az igény szerinti üzembe helyezési felület nem jelenik meg, ha a lépés sikeres. 

**Hibaelhárítási tippek**
* Győződjön meg arról, hogy érvényes hitelesítő adatokat adott meg a célalkalmazás számára, például a titkos jogkivonatot és a bérlői URL-címet. A szükséges hitelesítő adatok az alkalmazástól függően változnak. A részletes konfigurációs oktatóanyagok [itt](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)találhatók. 
* Győződjön meg arról, hogy a célalkalmazás támogatja a szűrést az attribútum-hozzárendelések panelen definiált egyező attribútum (ok) ban. Előfordulhat, hogy ellenőriznie kell az alkalmazás fejlesztője által biztosított API-dokumentációt az általa támogatott szűrők megismeréséhez.  
* A SCIM alkalmazások esetében olyan eszközt is használhat, mint például a Poster, hogy az alkalmazás válaszoljon az engedélyezési kérelmekre, mivel az Azure AD kiépítési szolgáltatás elvárja. [Itt](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#request-3)található egy példa erre a kérelemre.

### <a name="step-2-import-user"></a>2. lépés: felhasználó importálása
Ezután a kiépítési szolgáltatás lekéri a felhasználót a forrás rendszerből. A szolgáltatás által lekérdezett felhasználói attribútumok később kiértékelik, hogy a felhasználó a kiépítés hatókörében van-e, ellenőrzi a meglévő felhasználó célját, és meghatározza, hogy a rendszer milyen felhasználói attribútumokat exportáljon a célhelyre. 

**Részletek megtekintése**

A részletek megtekintése szakasz a forrás rendszerből (például az Azure AD-ből) importált felhasználó tulajdonságait jeleníti meg.

**Hibaelhárítási tippek**
* A felhasználó importálása sikertelen lehet, ha a forrásoldali rendszer felhasználói objektumában hiányzik a megfelelő attribútum. A hiba megoldásához frissítse a felhasználói objektumot a megfelelő attribútum értékével, vagy módosítsa a megfelelő attribútumot a létesítési konfigurációban.  
* Ha hiányzik egy várt attribútum az importált listából, győződjön meg arról, hogy az attribútum értéke a forrásoldali rendszer felhasználói objektumában van. A kiépítési szolgáltatás jelenleg nem támogatja a null attribútumok üzembe helyezését. 
* Győződjön meg arról, hogy a kiépítési konfigurációs attribútum leképezése lap tartalmazza a várt attribútumot. 

### <a name="step-3-determine-if-user-is-in-scope"></a>3. lépés: annak megállapítása, hogy a felhasználó hatókörben van-e
Ezután a kiépítési szolgáltatás határozza meg, hogy a felhasználó a kiépítés [hatókörében](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#scoping) van-e. A szolgáltatás olyan szempontokat vizsgál, mint például az, hogy a felhasználó hozzá van-e rendelve az alkalmazáshoz, függetlenül attól, hogy a hatókör szinkronizálva van-e az összes hozzárendelve vagy szinkronizálva értékre, valamint a létesítési konfigurációban definiált hatóköri  

**Részletek megtekintése**

A részletek megtekintése szakasz a kiértékelt hatóköri feltételeket mutatja. A következő tulajdonságok közül választhat:
* Az **aktív a forrásrendszer** azt jelzi, hogy a felhasználó rendelkezik a tulajdonsággal, hogy az aktív érték TRUE (igaz) értékű az Azure ad-ben.
* Az **alkalmazáshoz hozzárendelt** érték azt jelzi, hogy a felhasználó hozzá van rendelve az alkalmazáshoz az Azure ad-ben
* **Hatókör-szinkronizálás az összes** érték azt jelzi, hogy a hatókör beállítás lehetővé teszi a bérlő összes felhasználóját és csoportját.
* A **felhasználó rendelkezik a szükséges szerepkörrel** , amely azt jelzi, hogy a felhasználó rendelkezik az alkalmazásba való kiosztáshoz szükséges szerepkörökkel. 
* A **hatóköri szűrők** akkor is megjelennek, ha meghatározta az alkalmazáshoz tartozó hatóköri szűrőket. A szűrő a következő formátumban jelenik meg: {hatóköri szűrő címe} {hatókör szűrő attribútum} {hatókör-szűrő operátor} {hatóköri szűrő értéke}. 

**Hibaelhárítási tippek**
* Győződjön meg arról, hogy érvényes hatóköri szerepkört adott meg. Ne használja például a ["nagyobb, mint"](https://docs.microsoft.com/azure/active-directory/app-provisioning/define-conditional-rules-for-provisioning-user-accounts#create-a-scoping-filter) operátort nem egész értékkel. 
* Ha a felhasználó nem rendelkezik a szükséges szerepkörrel, tekintse át az [itt](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned#provisioning-users-assigned-to-the-default-access-role)ismertetett tippeket. 

### <a name="step-4-match-user-between-source-and-target"></a>4. lépés: a felhasználó egyeztetése a forrás és a cél között
Ebben a lépésben. a szolgáltatás megpróbálja egyeztetni az importálási lépésben beolvasott felhasználót a célszámítógépen lévő felhasználóval. 

**Részletek megtekintése**

A részletek megtekintése lapok a célként megadott felhasználó (k) tulajdonságait jelenítik meg. A környezeti ablaktáblán látható tulajdonságok a következők szerint változnak:
* Ha nincsenek olyan felhasználók, akik nem felelnek meg a célként megadott rendszernek, akkor nem fog tudni tulajdonságokat látni.
* Ha egy felhasználó megfelel a célként megadott rendszernek, akkor a rendszer a megfeleltetett felhasználó tulajdonságait fogja látni.
* Ha több felhasználó is megegyeznek, a rendszer a megfeleltetett felhasználók tulajdonságait fogja látni.
* Ha több egyező attribútum szerepel az attribútum-hozzárendelésekben, a rendszer az összes egyező attribútumot egymás után kiértékeli, és megjeleníti az egyeztetett felhasználókat. 

**Hibaelhárítás részletei**
* A kiépítési szolgáltatás nem képes egyedi módon egyeztetni a forrás felhasználóját a célként megadott felhasználóval. Ez úgy oldható meg, hogy a megfelelő attribútum egyedi. 
* Győződjön meg arról, hogy a célalkalmazás támogatja a megfelelő attribútumként definiált attribútum szűrését.  

### <a name="step-5-perform-action"></a>5. lépés: művelet végrehajtása
Végül a kiépítési szolgáltatás olyan műveletet hajt végre, mint például a felhasználó létrehozása, frissítése, törlése vagy kihagyása. 

**Részletek megtekintése**

A részletek megtekintése szakasz a célalkalmazás alkalmazásban módosított attribútumokat jeleníti meg. Ez a kiépítési szolgáltatás tevékenységének végső kimenetét és az exportált attribútumok számát jelöli. Ha ez a lépés meghiúsul, a megjelenített attribútumok a kiépítési szolgáltatás által a módosításra megkísérelt attribútumokat jelölik.  

**Hibaelhárítási tippek**
* A módosítások exportálásának hibái nagyban változhatnak. A gyakori hibákért tekintse meg a kiépítési naplók [dokumentációját](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs#error-codes) .


## <a name="frequently-asked-questions"></a>Gyakori kérdések
**Ki kell kapcsolni az üzembe helyezést az igény szerinti kiépítés igénybe vételéhez?** A hosszú élettartamú tulajdonosi jogkivonatot vagy felhasználónevet és jelszót használó alkalmazások engedélyezéséhez nincs szükség további lépésekre. Az OAuth-t használó alkalmazások jelenleg megkövetelik, hogy a kiépítési feladatok leállnak az igény szerinti kiépítés használata előtt. A G Suite, a Box, a munkahely a Facebook és a Slack alkalmazások ebbe a kategóriába tartoznak. A munka folyamatban van az igény szerinti kiépítés engedélyezéséhez az összes alkalmazáshoz anélkül, hogy le kellene állítania a létesítést. 

**Mennyi ideig tart az igény szerinti kiépítés?** Általában kevesebb mint 30 másodpercet vesz igénybe. 

## <a name="known-limitations"></a>Ismert korlátozások
Ma már van néhány ismert korlátozás. Kérjük, tegye közzé a [UserVoice](https://aka.ms/appprovisioningfeaturerequest) , hogy jobban rangsorolja a következő fejlesztéseket. Vegye figyelembe, hogy ezek a korlátozások az igény szerinti kiépítési képességre vonatkoznak. azokról a részletekről, amelyekkel az alkalmazás támogatja a kiépítési csoportokat, a törléseket stb., keresse meg az alkalmazás oktatóanyagát. 

* A munkanap, az AWS és a SuccessFactors alkalmazások nem támogatják az igény szerinti üzembe helyezést.
* A kiépítési csoportok és az igény szerinti szerepkörök nem támogatottak.
* A felhasználók és csoportok letiltása vagy törlése nem támogatott.

## <a name="next-steps"></a>Következő lépések

* [A kiépítés hibaelhárítása](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem)
