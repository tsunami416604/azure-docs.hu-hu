---
title: Automatikus felhasználói fiók kiépítés az SaaS-alkalmazásokba | Microsoft Docs
description: Megtudhatja, hogyan ellenőrizhető a felhasználói fiókok automatikus kiépítési feladatainak állapota, és hogyan lehet elhárítani az egyes felhasználók kiépítési feladatait.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: fda7654ca2d825ae4112dd06021c7e83ed6867cd
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381260"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Oktatóanyag: Jelentéskészítés a felhasználói fiókok automatikus üzembe helyezéséről

Azure Active Directory (Azure AD) olyan [felhasználói fiók létesítési szolgáltatását](user-provisioning.md) tartalmazza, amely segít automatizálni az SaaS-alkalmazásokban és más rendszerekben lévő felhasználói fiókok üzembe helyezését a teljes körű identitások életciklusának kezelése céljából. Az Azure AD az [Azure ad Application Gallery](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured)"Kiemelt" szakaszában található összes alkalmazáshoz és rendszerhez támogatja az előre integrált felhasználó-kiépítési összekötőket.

Ez a cikk azt ismerteti, hogyan ellenőrizhető a kiépítési feladatok állapota a beállításuk után, valamint az egyes felhasználók és csoportok üzembe helyezésének hibaelhárítása.

## <a name="overview"></a>Áttekintés

A kiépítési összekötők a [Azure Portal](https://portal.azure.com)használatával állíthatók be és konfigurálhatók, a támogatott alkalmazáshoz mellékelt [dokumentáció](../saas-apps/tutorial-list.md) alapján. A konfigurálás és a Futtatás után a kiépítési feladatok a következő két módszer egyikének használatával adhatók meg:

* **Azure Portal** – ez a cikk elsősorban azt ismerteti, hogyan kell beolvasni a jelentési adatokat a [Azure Portalból](https://portal.azure.com), amely egyszerre egy kiépítési összegző jelentést is biztosít, valamint egy adott alkalmazás részletes kiépítési naplóit.
* A **audit API** -Azure Active Directory egy olyan naplózási API-t is biztosít, amely lehetővé teszi a részletes kiépítési naplók programozott lekérését. Tekintse meg az API használatára vonatkozó dokumentáció [Azure Active Directory naplózási API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) -referenciát. Habár ez a cikk nem fedi le az API használatát, részletesen ismerteti a naplóban rögzített kiépítési események típusait.

### <a name="definitions"></a>Definíciók

Ez a cikk az alábbi, az alábbiakban meghatározott kifejezéseket használja:

* **Forrásoldali rendszer** – az Azure ad kiépítési szolgáltatás által szinkronizált felhasználók tárháza. A Azure Active Directory az előre integrált létesítési összekötők többségének a forrása, azonban bizonyos kivételek (például: Munkanap bejövő szinkronizálása).
* **Célrendszer** – az Azure ad-kiépítési szolgáltatás által szinkronizált felhasználók tárháza. Ez általában egy SaaS-alkalmazás (például: Salesforce, ServiceNow, G Suite, Dropbox for Business), de bizonyos esetekben lehet egy helyszíni rendszer, például Active Directory (például: A munkanap bejövő szinkronizálása Active Directoryre).

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>Kiépítési jelentések beolvasása a Azure Portalból

Ha egy adott alkalmazáshoz szeretne kiépíteni egy jelentést, először indítsa el a [Azure Portalt](https://portal.azure.com) , és tallózással keresse meg azt a vállalati alkalmazást, amelyhez a kiépítés konfigurálva van. Ha például a felhasználókat a LinkedIn jogosultságszint-emeléssel kívánja kiépíteni, az alkalmazás adatainak navigációs útvonala a következő:

**Azure Active Directory > vállalati alkalmazások > minden alkalmazás > LinkedIn emelt szintű**

Innen elérheti a kiépítési összesítő jelentést és a kiépítési naplókat is, amelyeket az alábbiakban ismertetünk.

## <a name="provisioning-summary-report"></a>Kiépítési összegző jelentés

A kiépítési összegző jelentés az adott alkalmazás **létesítés** lapján látható. A **Beállítások**alatt a **szinkronizálás részletei** szakaszban található, és a következő információkat biztosítja:

* A szinkronizált felhasználók és/csoportok teljes száma, amelyek jelenleg a forrásrendszer és a célként megadott rendszer közötti kiépítés hatókörében vannak.
* A szinkronizálás legutóbbi futtatásakor. A szinkronizálások jellemzően 20-40 percenként történnek, a [kezdeti szinkronizálás](user-provisioning.md#what-happens-during-provisioning) befejeződése után.
* Azt határozza meg, hogy befejeződött-e a [kezdeti szinkronizálás](user-provisioning.md#what-happens-during-provisioning) .
* Azt határozza meg, hogy a kiépítési folyamat karanténba helyezése megtörtént-e, és hogy a karantén milyen okból áll fenn (például érvénytelen rendszergazdai hitelesítő adatok miatt nem lehet kommunikálni a megcélzott rendszerrel).

A kiépítési összefoglaló jelentésnek az első hely rendszergazdáinak kell megkeresnie a kiépítési feladatok működési állapotát.

 ![Összefoglaló jelentés](./media/check-status-user-account-provisioning/summary_report.PNG)

## <a name="provisioning-audit-logs"></a>Naplózási naplók kiépítés

A kiépítési szolgáltatás által végrehajtott összes tevékenységet az Azure AD-naplók rögzítik, amelyek a **fiók létesítési** kategóriájának **naplók lapján** tekinthetők meg. Naplózott tevékenység típusú események a következők:

* **Események importálása** – az "import" esemény minden alkalommal rögzítésre kerül, amikor az Azure ad-létesítési szolgáltatás egy adott felhasználóra vagy csoportra vonatkozó információt kér le egy forrásoldali rendszerből vagy egy célként megadott rendszerből. A szinkronizálás során először a rendszer beolvassa a felhasználókat a forrásrendszer, és az eredményeket "importálási" eseményként rögzíti. A rendszer lekérdezi a beolvasott felhasználók egyező azonosítóit, hogy ellenőrizze, hogy léteznek-e, és hogy az eredmények "importálási" eseményként is szerepelnek-e. Ezek az események rögzítik az összes hozzárendelt felhasználói attribútumot és azok értékeit, amelyeket az Azure AD-kiépítési szolgáltatás észlelt az esemény időpontjában.
* **Szinkronizálási szabály eseményei** – ezek az események az attribútum-hozzárendelési szabályok és bármely konfigurált hatókör-szűrő eredményeiről számoltak be, miután a felhasználói adatok importálása és kiértékelése a forrás-és a célszámítógépeken történt. Ha például a forrásrendszer egyik felhasználójának a kiépítés hatókörében kell lennie, és úgy kell tekinteni, hogy nem létezik a célszámítógépen, akkor ez az esemény rögzíti, hogy a felhasználó kiépítve lesz a megcélzott rendszeren.
* **Események exportálása** – az "export" esemény minden alkalommal rögzítésre kerül, amikor az Azure ad-kiépítési szolgáltatás egy felhasználói fiókot vagy egy csoport objektumot ír egy célként megadott rendszerbe. Ezek az események rögzítik az Azure AD-kiépítési szolgáltatás által az esemény időpontjában írt összes felhasználói attribútumot és azok értékeit. Ha hiba történt a felhasználói fiók vagy a csoport objektumának a célként megadott rendszerbe való írásakor, akkor itt jelenik meg.
* **Letétbe helyezendő események feldolgozása** – a folyamat akkor fordul elő, ha a létesítési szolgáltatás hibát észlel a művelet megkísérlése közben, és megkezdi a műveletet a visszalépési időintervallumon belül. A rendszer minden alkalommal rögzíti a "letéti" eseményt, amikor újrapróbálták a kiépítési műveletet.

Ha egy adott felhasználó kiépítési eseményeit keresi, az események általában ebben a sorrendben történnek:

1. Importálási esemény: A felhasználó beolvasása a forrásoldali rendszerből történik.
1. Importálási esemény: A rendszer lekérdezi a lekért felhasználó létezését.
1. Szinkronizálási szabály eseménye: A forrás-és a célszámítógép felhasználói adatait a rendszer kiértékeli a konfigurált attribútum-leképezési szabályok és a hatóköri szűrők alapján, hogy meghatározza, milyen műveleteket kell végrehajtani.
1. Esemény exportálása: Ha a szinkronizálási szabály eseménye azt állította be, hogy műveletet kell végrehajtani (Hozzáadás, frissítés, törlés), akkor a művelet eredményeit egy exportálási esemény rögzíti.

   ![Példa: A naplózási napló lapja, amely a tevékenységeket és az állapotot jeleníti meg](./media/check-status-user-account-provisioning/audit_logs.PNG)

### <a name="looking-up-provisioning-events-for-a-specific-user"></a>Adott felhasználó kiépítési eseményeinek keresése

A kiépítési naplók leggyakoribb felhasználási esete egy adott felhasználói fiók kiépítési állapotának ellenőrzése. Egy adott felhasználó utolsó kiépítési eseményeinek megkeresése:

1. Lépjen a **naplók** szakaszra.
1. A **Kategória** menüben válassza a **fiók**kiépítés lehetőséget.
1. A **dátumtartomány** menüben válassza ki a keresni kívánt dátumtartományt.
1. A keresősáv  mezőben adja meg a keresendő felhasználó felhasználói azonosítóját. Az azonosító érték formátumának egyeznie kell azzal, amit az attribútum-hozzárendelési konfigurációban elsődleges megfeleltetési AZONOSÍTÓként választott (például userPrincipalName vagy alkalmazotti azonosító száma). A kötelező azonosító érték a cél (ok) oszlopban látható lesz.
1. A kereséshez nyomja le az ENTER billentyűt. A rendszer először a legutóbbi kiépítési eseményeket adja vissza.
1. Ha a rendszer visszaadja az eseményeket, jegyezze fel a tevékenységek típusát, valamint azt, hogy sikeresek vagy sikertelenek voltak-e. Ha a rendszer nem ad vissza eredményt, az azt jelenti, hogy a felhasználó vagy nem létezik, vagy a kiépítési folyamat még nem észlelte, ha a teljes szinkronizálás még nem fejeződött be.
1. Az egyes eseményekre kattintva megtekintheti a részletes részleteket, beleértve az esemény részeként beolvasott, kiértékelt vagy írt felhasználói tulajdonságokat is.

A naplók használatáról az alábbi videóban talál további információt. A naplók a 5:30-es jel körül jelennek meg:

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

### <a name="tips-for-viewing-the-provisioning-audit-logs"></a>Tippek a kiépítési naplók megtekintéséhez

A Azure Portal legjobb olvashatósága érdekében válassza az **oszlopok** gombot, és válassza ki az alábbi oszlopokat:

* **Date (dátum** ) – az esemény időpontját jeleníti meg.
* **Cél (ok)** – megjeleníti az esemény tárgyát képező alkalmazás nevét és felhasználói azonosítóját.
* **Tevékenység** – a tevékenység típusa a korábban leírtak szerint.
* **Állapot** – azt jelzi, hogy az esemény sikeres volt-e.
* **Állapot oka** – összefoglalás arról, hogy mi történt a kiépítési eseményben.

## <a name="troubleshooting"></a>Hibaelhárítás

A kiépítési összegző jelentés és a naplók kulcsszerepet játszanak a rendszergazdáknak a különböző felhasználói fiókok üzembe helyezésével kapcsolatos problémák elhárításában.

Az automatikus felhasználó-kiépítés hibaelhárításával kapcsolatos forgatókönyv-alapú útmutatásért tekintse meg a [felhasználók egy alkalmazáshoz való konfigurálásával és](application-provisioning-config-problem.md)kikapcsolásával kapcsolatos problémákat.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](what-is-single-sign-on.md)
