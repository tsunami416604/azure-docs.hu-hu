---
title: Jelentéskészítés az Azure Active Directory automatikus felhasználói fiók kiépítése SaaS-alkalmazásokhoz |} A Microsoft Docs
description: Ismerje meg, hogyan automatikus felhasználói fiók kiépítése a feladatok állapotát, és az egyes felhasználók átadásának hibaelhárítása.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: barbkess
ms.reviewer: asmalser
ms.openlocfilehash: af5d7174a2726a6ff8a62477149606ec5d43e94e
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44357208"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Oktatóanyag: Jelentések automatikus felhasználói fiók kiépítése


Az Azure Active Directory tartalmazza egy [létesítési szolgáltatás felhasználói fiók](user-provisioning.md) , amelynek segítségével automatizálhatja a kiépítési megszüntetést felhasználói fiókok a SaaS-alkalmazások és más rendszerek, teljes körű identitás-életciklus céljából felügyeleti. Az Azure AD támogatja az összes, az alkalmazások és rendszerek "Kiemelt" szakaszában összekötők előre integrált felhasználókiépítés a [Azure AD alkalmazáskatalógusában](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured).

Ez a cikk bemutatja, hogyan ellenőrizheti az üzembe helyezés állapotát az feladatok után azok hoztak létre, és hibaelhárítási információkat ismertetni az egyes felhasználók és csoportok üzembe helyezése.

## <a name="overview"></a>Áttekintés

Üzembe helyezési összekötők beállítása és konfiguráltak a [az Azure portal](https://portal.azure.com), a következő a [biztosított dokumentáció](../saas-apps/tutorial-list.md) a támogatott alkalmazás. Miután konfigurálva és fusson, kiépítés feladatok jelenteni lehet a két módszer egyikével:

* **Azure felügyeleti portálján** – Ez a cikk elsősorban ismerteti a jelentés adatainak beolvasása a [az Azure portal](https://portal.azure.com), amely biztosít egy összesítő jelentés kiépítés és is részletes kiépítés auditnaplók egy az adott alkalmazáshoz.

* **API naplózása** – Azure Active Directory is kínál, amely lehetővé teszi a programozott lekéréséhez a részletes üzembe helyezési naplók naplózási API. Lásd: [API-referencia az Azure Active Directory naplózási](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) az adott használatával az API dokumentációjában. Ez a cikk nem tárgyalja kifejezetten az API használatát, amíg azt a kiépítés események, které jsou zaznamenány v auditovacím protokolu típusú részletesen.

### <a name="definitions"></a>Meghatározások

Ebben a cikkben alább meghatározott, az alábbi feltételek:

* **Forrás-rendszer** -, akik az Azure AD létesítési szolgáltatás szinkronizálja a tárházban. Az Azure Active Directory a forrásrendszerben a legtöbb előre integrált összekötők kiépítése, azonban vannak kivételek (Példa: Workday bejövő szinkronizálási).

* **Cél rendszer** -, akik az Azure AD létesítési szolgáltatás szinkronizálja a tárházban. Ez általában az SaaS-alkalmazás (példák: Salesforce, ServiceNow, a Google Apps, a Dropbox Business), de néhány esetben lehet egy helyszíni rendszer, például az Active Directory (Példa: bejövő szinkronizálás Workday az Active Directory).


## <a name="getting-provisioning-reports-from-the-azure-management-portal"></a>Bevezetés a jelentések az Azure felügyeleti portálján a kiépítés

Jelentési adatok egy adott alkalmazás első üzembe helyezés esetében először indítása a [Azure felügyeleti portálján](https://portal.azure.com) , és keresse meg a vállalati alkalmazás, amelynek kiépítés van konfigurálva. Például ha a felhasználók a LinkedIn jogosultságszint-emelés szeretne kiépíteni, a navigációs az alkalmazás részletes útvonala:

**Az Azure Active Directory > Vállalati alkalmazások > minden alkalmazás > LinkedIn szintjének emelése**

Itt is elérheti a kiépítési összefoglaló jelentés és az üzembe helyezési naplók, mind az alábbiakban.


## <a name="provisioning-summary-report"></a>Üzembe helyezési összesítő jelentés

Az üzembe helyezési összefoglaló jelentés jelenik meg a a **kiépítési** megadott alkalmazás lapján. Található a **szinkronizálás részleteivel** szakasz alatt **beállítások**, és a következő információkat tartalmazza:

* Felhasználók száma és a csoportok, amely nincs szinkronizálva, és jelenleg a forrás és a cél rendszer közötti kiépítés hatókörébe /

* A szinkronizálás a legutóbbi alkalommal futott. Szinkronizálás általában után fordulhat elő, percenként 20 – 40- [a kezdeti szinkronizálás](user-provisioning.md#what-happens-during-provisioning) befejeződött.

* E- [a kezdeti szinkronizálás](user-provisioning.md#what-happens-during-provisioning) befejezése

* -E a kiépítési folyamat karanténba került-e, és mi az a karanténba helyezett állapot oka (például nem sikerült kommunikálni a célrendszer érvénytelen rendszergazdai hitelesítő adatok miatt)

Az üzembe helyezési összefoglaló jelentés kell lennie a helyi rendszergazdák áttekintés a létesítési feladat működési állapotának az ellenőrzéséhez.

 ![Összefoglaló jelentés](./media/check-status-user-account-provisioning/summary_report.PNG)

## <a name="provisioning-audit-logs"></a>Üzembe helyezési naplók
A kiépítési szolgáltatás által végzett tevékenységek tárolja, amely az Azure AD naplóit, amely lehet megtekinteni a **Auditnaplók** lapjára az **fiók üzembe helyezésének** kategória. Esemény típusú naplózott tevékenységek a következők:

* **Események importálása** – az "import" esemény minden alkalommal, amikor az Azure AD létesítési szolgáltatás egy adott felhasználó vagy csoport adatait, a forrás vagy cél rendszer kérdezi le. a rendszer rögzíti. A szinkronizálás során felhasználók lekért a forrásrendszerben először rögzíteni, "importálása" események eredményeivel. Az egyező azonosítók a lekért felhasználók majd megkérdezi a ellen annak ellenőrzéséhez, hogy azok léteznek, a "importálása" eseményként is rögzíti az eredményeket a célrendszeren. Ezeket az eseményeket rögzíteni, csatlakoztatott felhasználói attribútumok és azok értékeit, amely az Azure AD létesítési szolgáltatás az esemény időpontjában által is látható. 

* **Szinkronizálási szabály események** – ezeket az eseményeket az eredményeket az attribútum-leképezési szabályok jelentést és az esetleges Hatókörszűrő, felhasználói adatok importált és a forrás és cél rendszerekből kiértékelése után. Például ha egy felhasználó a forrásrendszerben a kiépítés hatókörébe kell tekinteni, és tekinteni, hogy nem létezik a célrendszeren, majd ezt az eseményt, amely rögzíti a felhasználó lesznek üzembe helyezve a célrendszeren. 

* **Események exportálását** – az "export" esemény minden alkalommal, amikor az Azure AD létesítési szolgáltatás egy felhasználói fiókot vagy csoportot objektum ír a rendszer rögzíti. Ezeket az eseményeket rögzíteni a felhasználói attribútumok és azok értékeit, hogy az írt által az Azure AD létesítési szolgáltatás az esemény időpontjában. Hiba történt a felhasználói fiók vagy csoport objektum írását a célrendszeren, ha megjelenik itt.

* **Dolgozza fel letéti** -folyamat escrows fordulhat elő, ha a kiépítési szolgáltatás közben egy művelet egy hiba lép fel, és próbálja megismételni a műveletet egy visszatartási időköz elteltéig kezd. Egy "letéti" esemény minden alkalommal, amikor egy üzembe helyezési művelet volt elavult rögzíti.

Ha megnézzük a kiépítés események az egyes felhasználók számára, az események általában történnek az itt látható sorrendben:

1. Importálás esemény: felhasználói veszi át a forrásrendszerben.

2. Importálás esemény: célrendszer lekérik a lekért felhasználói meglétének ellenőrzése.

3. Szinkronizálási szabály esemény: rendszerekből a forrás- és felhasználói adatok értékeli ki a konfigurált attribútum-leképezési szabályok és meghatározni, hogy milyen műveletet, ha vannak szabad elvégezni Hatókörszűrő szemben.

4. Esemény exportálása: Ha a szinkronizálási szabály esemény során, hogy a művelet legyen-e végre (Hozzáadás, Update, Delete), majd a művelet eredményeit rögzíti az exportálási esemény.

![Az Azure ad-ben tesztfelhasználó létrehozása](./media/check-status-user-account-provisioning/audit_logs.PNG)


### <a name="looking-up-provisioning-events-for-a-specific-user"></a>Kiépítés egy adott felhasználó események keresése

A leggyakoribb üzembe helyezési Naplók funkcióban egyedi felhasználói fiók kiépítési állapotának ellenőrzéséhez. Keresse ki az utolsó kiépítési események egy adott felhasználó:

1. Nyissa meg a **Auditnaplók** szakaszban.

2. Az a **kategória** menüjében válassza **fiók üzembe helyezésének**.

3. Az a **dátumtartomány** menüben válassza ki a dátumtartományt szeretne keresni,

4. Az a **keresési** sáv, adja meg a keresni kívánt felhasználó felhasználói azonosítója. Azonosító értékének formátuma meg kell egyeznie a függetlenül van kiválasztva, mint az elsődleges egyező azonosító attribútumleképezés konfigurációjában (például userPrincipalName vagy az alkalmazott azonosítója szám). A szükséges azonosító értéke meg fognak jelenni a cél(ok) oszlop.

5. Nyomja le az Enter billentyűt a keresés. A legutóbbi kiépítési események először vissza kell.

6. Ha eseményeket ad vissza, vegye figyelembe a tevékenység típusát, és hogy azok sikeres vagy sikertelen volt. Ha nem jár eredménnyel, majd azt azt jelenti, hogy a felhasználó nem létezik vagy nem még észlelt a kiépítési folyamat által, ha a teljes szinkronizálás még nem fejeződött be.

7. Kattintson az egyes események megtekintéséhez kiterjesztett adatait, például az összes felhasználói tulajdonságok, amelyek lekérni, értékeli ki, vagy az esemény részeként írt.

A bemutató a vizsgálati naplók használatáról tekintse meg az alábbi videó. A vizsgálati naplók jelennek meg az 5:30 körül jelölje meg:

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

### <a name="tips-for-viewing-the-provisioning-audit-logs"></a>Tippek az üzembe helyezési naplók megtekintése

Az olvashatóság érdekében ajánlott az Azure Portalon, válassza ki a **oszlopok** gombra, és válassza ki ezeket az oszlopokat:

* **Dátum** -jeleníti meg a dátumot, az esemény történt.
* **Cél(ok)** – az alkalmazás neve és felhasználói azonosítója, amelyek az esemény tárgya jeleníti meg.
* **Tevékenység** – a tevékenység típusa, az előzőekben leírtak szerint.
* **Állapot** - e az esemény sikeres volt-e.
* **Állapot oka** – Mi történt a kiépítési esemény összegzését.


## <a name="troubleshooting"></a>Hibaelhárítás

A kiépítési összefoglaló jelentés- és vizsgálati naplókat útmutatás nyújtása a különböző felhasználói fiók kiépítéssel kapcsolatos problémák elhárítása rendszergazdák kulcsfontosságú szerepet játszanak.

Forgatókönyv-alapú hogyan háríthatók el a felhasználók automatikus átadása, tekintse át [konfigurálásával és a felhasználók alkalmazásban való létrehozásával kapcsolatos problémák](application-provisioning-config-problem.md).


## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](what-is-single-sign-on.md)
