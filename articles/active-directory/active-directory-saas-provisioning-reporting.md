---
title: "Az Azure Active Directory automatikus felhasználói fiók kiépítése SaaS-alkalmazásokhoz való Reporting |} Microsoft Docs"
description: "Útmutató: automatikus felhasználói fiók kiépítése feladatok állapotának ellenőrzését, és egyéni felhasználók számára a kiépítés elhárítása."
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: mtillman
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/12/2017
ms.author: asmalser-msft
ms.openlocfilehash: d2ec1b25e39717fb2411d35e3b0a9f5eb3a977be
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Oktatóanyag: Jelentések automatikus felhasználói fiók kiépítése


Az Azure Active Directory tartalmaz egy [szolgáltatás kiépítését felhasználói fiók](active-directory-saas-app-provisioning.md) , amelynek segítségével automatizálhatja az üzembe helyezési megszüntetést felhasználói fiókok SaaS-alkalmazásokhoz és más rendszerek végpont identitás életciklus-kezelési célból. Az Azure AD az összes, az alkalmazások és a "Kiemelt" szakaszában rendszerek összekötők kiépítés előre integrált felhasználói támogatja a [az Azure AD application gallery](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured).

Ez a cikk ismerteti, hogyan feladatok kiépítése után azok beállított állapotának ellenőrzése és hibaelhárítása az egyes felhasználók és csoportok kiépítését.

## <a name="overview"></a>Áttekintés

Üzembe helyezési összekötők elsősorban állíthatók be, és a használatával konfigurálhatók a [Azure felügyeleti portálján](https://portal.azure.com), követve a [biztosított dokumentáció](active-directory-saas-tutorial-list.md) az alkalmazáshoz, ahol felhasználói fiók kiépítése van szükség. Konfigurálni és futtatni, ha egy alkalmazás feladatok kiépítés jelenthetők a két módszer egyikével:

* **Azure felügyeleti portálján** -Ez a cikk elsődlegesen ismerteti a jelentés-adatok beolvasása a [Azure felügyeleti portálján](https://portal.azure.com), amely biztosítja, mind a kiépítés összefoglaló jelentést, valamint részletes kiépítés naplók egy adott alkalmazáshoz.

* **Naplózási API** -Azure Active Directory is biztosít egy naplózási API-t, amely lehetővé teszi a programozott lekérése a részletes telepítési naplókat. Lásd: [Azure Active Directory naplózási API-referencia](active-directory-reporting-api-audit-reference.md) az API-jával vonatkozó dokumentációt. Amíg ez a cikk kifejezetten foglalkozik az API-t használja, azt a biztonsági naplóban rögzített események kiépítés típusú adatok találhatók.

### <a name="definitions"></a>Meghatározások

Ebben a cikkben az alábbiakban meghatározott a következő feltételeket:

* **Forrás-rendszer** -a tárház, amely szinkronizálja az Azure AD szolgáltatás kiépítését. Az Azure Active Directory a forrásrendszerben többségének előre integrálva az összekötők kiépítés, azonban bizonyos kivételek vannak (Példa: Workday bejövő szinkronizálás).

* **Cél rendszer** -a tárház, amely szinkronizálja az Azure AD szolgáltatás kiépítését. Ez általában az SaaS-alkalmazás (példák: Salesforce, a ServiceNow, a Google Apps, a Dropbox vállalati), azonban bizonyos esetekben lehet például az Active Directory egy a helyszíni rendszer (Példa: Workday bejövő szinkronizálás Active Directory).


## <a name="getting-provisioning-reports-from-the-azure-management-portal"></a>Jelentéseit az Azure felügyeleti portálján a kiépítés beolvasása

Az beszerzése kiépítésére jelentési adatok egy adott alkalmazáshoz, indítsa el, indítsa el a [Azure felügyeleti portálján](https://portal.azure.com) és a vállalati alkalmazás, amelynek kiépítés van konfigurálva alkulcsot. Például ha a felhasználók számára LinkedIn jogosultságszint-emelés kiépíteni, a navigációs mutató az alkalmazás részletes adatainak elérési út:

**Az Azure Active Directory > Vállalati alkalmazások > összes alkalmazás > LinkedIn jogosultságszint-emelés**

Itt végezheti el a kiépítési összefoglaló jelentést és a telepítési naplók, mind az alábbiakban.


### <a name="provisioning-summary-report"></a>Üzembe helyezési összegzési jelentése

A létesítési összefoglaló jelentést is elérhetővé válik a a **kiépítési** megadott alkalmazás lapján. A szinkronizálás részletei szakasz alatt található **beállítások**, és a következő adatokat tartalmazza:

* A felhasználók száma összesen és a / csoportok, amely nincs szinkronizálva, és jelenleg a hatókör között a forrás és a cél rendszerhez történő üzembe helyezéséhez.

* A szinkronizálás a legutóbbi alkalommal volt futtatva. Minden 20-40 perc, a teljes szinkronizálás befejeződése után általában történjen szinkronizálás.

* Egy kezdeti teljes szinkronizálás befejeződött-e.

* -E a telepítési folyamat karanténba helyezés került-e, és mi az a karanténba helyezett állapotának oka, hogy pl. (nem sikerült kommunikálni a célrendszeren érvénytelen rendszergazdai hitelesítő adatok miatt)

A létesítési összefoglaló jelentést kell lennie az első hely rendszergazdák tekintse meg a kiosztási feladatának működési állapotának az ellenőrzéséhez.

 ![Összegzési jelentése](./media/active-directory-saas-provisioning-reporting/summary_report.PNG)

### <a name="provisioning-audit-logs"></a>Telepítési naplófájlok
A létesítési szolgáltatás által végzett tevékenységek tárolja, amely az Azure AD naplókat, amely tekintheti meg a **naplók** lap a **fiók** kategóriát. Naplózott esemény tevékenységtípusok a következők:

* **Események importálása** -"importálás" esemény rögzítése minden alkalommal, amikor az Azure AD szolgáltatás kiépítését kapcsolatos egyéni felhasználó vagy csoport adatait kérdezi le a forrásrendszerben, vagy a célrendszeren. A szinkronizálás során felhasználókat a rendszer beolvassa az a forrásrendszerben először "importálása" események rögzített eredményekkel. A lekérdezett felhasználók egyező azonosítójú majd megkérdezi a szemben a célrendszeren ellenőrizze, hogy léteznek, a eredményekkel "importálása" eseményként is rögzíti. Ezeket az eseményeket rögzítse az összes csatlakoztatott felhasználói attribútumokat és azok értékei, az Azure AD szolgáltatás kiépítését az esemény időpontjában által látott volt. 

* **Szinkronizálási szabály események** - attribútum leképezési szabályok eredményeit jelentés ezeket az eseményeket, és minden konfigurált hatókörének meghatározásához szűrők, felhasználói adatok importálása és a forrás és cél rendszerekből kiértékelése után. Például ha egy felhasználó a forrásrendszerben kialakítási hatókörben kell tekinteni, és nem megfelelőnek nem létezik a célrendszeren, akkor ezt az eseményt, amely rögzíti a felhasználó megkapják a célrendszeren. 

* **Események exportálását** -"export" esemény minden alkalommal, amikor az Azure AD szolgáltatás kiépítését egy felhasználói fiókot vagy csoportot objektum ír a rendszer rögzíti. Ezeket az eseményeket rögzítse az összes felhasználói attribútumokat és azok értékei, írt az Azure ad szolgáltatás kiépítését az esemény időpontjában. Hiba történt a felhasználói fiók vagy csoport objektum írása a célrendszeren, ha megjelenik itt.

* **Feldolgozni az eseményeket a letéti** -folyamat escrows fordulhat elő, ha a létesítési szolgáltatás közben művelet hiba lép fel, és próbálja megismételni a műveletet időt időközönkénti biztonsági ki elkezdi. Egy "letéti" esemény rögzítése minden alkalommal, amikor a telepítési művelet lett visszavonva.

Ha egyéni események kiépítés, az események általában fordulhat elő, az itt megadott sorrendben:

1. Importálás esemény: felhasználói a forrásrendszerben kéri le a rendszer.

2. Importálás esemény: célrendszeren le kell kérdezni a lekért felhasználói meglétének ellenőrzése.

3. Szinkronizálási szabály esemény: a forrás-és cél felhasználó adatait rendszer összehasonlítja a megadott attribútum leképezési szabályok és hatókörére szűrő segítségével meghatározhatja, mi a teendő, ha van ilyen kell elvégezni.

4. Esemény exportálása: Ha a szinkronizálási szabály eseményt meghatározni, hogy egy műveletet kell végrehajtani (pl. hozzáadás, frissítés, Törlés), majd a művelet eredményét tárolja, amely az Exportálás esemény.

![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-provisioning-reporting/audit_logs.PNG)


### <a name="looking-up-provisioning-events-for-a-specific-user"></a>Kiépítés események egy adott felhasználó keresése

A telepítési naplók a leggyakrabban használt használati eset egyedi felhasználói fiókot a kiépítési állapotának. Kereshet egy adott felhasználó utolsó létesítési események:

1. Lépjen a **naplók** szakasz.

2. Az a **kategória** menü **fiók**.

3. Az a **dátumtartomány** menüben válassza ki a keresendő, dátumtartomány

4. Az a **keresési** sáv, adja meg a keresni kívánt felhasználó felhasználói Azonosítóját. Azonosító érték formátuma függetlenül van kiválasztva, mint az elsődleges egyező azonosító attribútum leképezési konfigurációjában (pl. userPrincipalName vagy alkalmazott azonosítószámát) meg kell egyeznie. Az azonosító értékének megadása kötelező a cél(ok) oszlopban látható lesz.

5. Nyomja le az Enter kereséséhez. Először az eredmény a legutóbbi létesítési események.

6. Ha események ad vissza, vegye figyelembe a tevékenység típusát, és hogy azok sikeres vagy sikertelen volt. Ha nincs találat, majd az azt jelenti, hogy a felhasználó nem létezik, vagy nem még felderítette a telepítési folyamatot, ha a teljes szinkronizálás még nem fejeződött be.

7. Kattintson az egyes események megtekintéséhez kiterjesztett adatait, többek között az összes felhasználó tulajdonságai beolvasni, értékeli ki, vagy az esemény-ként.


### <a name="tips-for-viewing-the-provisioning-audit-logs"></a>Tippek a telepítési naplók megtekintése

Az olvashatóság érdekében ajánlott az Azure portálon, válassza ki a **oszlopok** gombra, majd válassza ki a következő oszlopot:

* **Dátum** -dátumát jeleníti meg az esemény történt.
* **Cél(ok)** -jeleníti meg az alkalmazás és egy felhasználói Azonosítót, amely az esemény vonatkoznak.
* **Tevékenység** -tevékenységtípus, korábban leírt módon.
* **Állapot** - e az esemény sikeres volt-e.
* **Állapotok** – Mi történt az üzembe helyezési eseményben összegzését.


## <a name="troubleshooting"></a>Hibaelhárítás

Az üzembe helyezési összegző jelentés és naplózási naplókat segíti a rendszergazdák különböző felhasználói fiók kiépítése kapcsolatos problémák elhárítása kulcsfontosságú szerepet játszanak.

Forgatókönyv-alapú bemutató elhárításáról automatikus a felhasználók átadása, lásd: [problémák konfigurálása, és üzembe helyezését a felhasználók számára az alkalmazás](active-directory-application-provisioning-content-map.md).


## <a name="additional-resources"></a>További erőforrások

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](active-directory-enterprise-apps-manage-provisioning.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)
