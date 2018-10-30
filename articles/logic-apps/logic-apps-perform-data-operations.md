---
title: Műveletek végrehajtása a data – Azure Logic Apps |} A Microsoft Docs
description: Átalakítás, kezelheti és módosíthatja a kimeneti adatok és az Azure Logic Appsben formátumok
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/30/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 93c24f88fcd6a002493933ef71c5c80bd2ff8c10
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50231644"
---
# <a name="perform-data-operations-in-azure-logic-apps"></a>Adatok műveletek végrehajtása az Azure Logic Appsben

Ez a cikk bemutatja, hogyan működhet együtt adatokat a logic Apps ezeket a feladatokat, és több művelet hozzáadásával:

* Táblázatok létrehozása tömböket.
* Hozzon létre a tömbök más tömböket egy feltétel alapján.
* A JavaScript Object Notation (JSON) objektum tulajdonságai felhasználóbarát-jogkivonat készítése esetleges így könnyen használhatja azokat a tulajdonságokat a munkafolyamatban.

Ha nem találja a használni kívánt művelet, próbálkozzon böngészés számos különböző [adatkezelési függvények](../logic-apps/workflow-definition-language-functions-reference.md) , amely a Logic Apps biztosít. 

Ezek a táblák a forrás, amely a műveletek a működhet, azonban egyes leírás betűrend szerint jelenik meg az adattípusok alapján vannak rendezve, és használhatja az operations összefoglalásához.

**Tömb műveletek** 

Ezek a műveletek segítségével tömbök adatokkal.

| Műveletek | Leírás | 
|--------|-------------| 
| [**CSV-táblázat létrehozása**](#create-csv-table-action) | Hozzon létre egy vesszővel tagolt (CSV) tábla egy tömb. | 
| [**HTML-táblázat létrehozása**](#create-html-table-action) | Hozzon létre egy HTML-táblázat egy tömb. | 
| [**Tömb szűrése**](#filter-array-action) | Hozzon létre egy tömb részhalmazát egy tömb a megadott szűrő vagy a feltétel alapján. | 
| [**Csatlakozás**](#join-action) | Hozzon létre egy karakterláncot a tömb összes eleme, egymástól a következő megadott karakterrel. | 
| [**Válassza ki**](#select-action) | Hozzon létre egy másik tömb összes eleme megadott tulajdonságainak egy tömb. | 
||| 

**JSON-műveletek**

Ezek a műveletek segítségével JavaScript Object Notation (JSON) formátumú adatok.

| Műveletek | Leírás | 
|--------|-------------| 
| [**Compose**](#compose-action) | Hozzon létre egy üzenetet, vagy karakterlánc, több bemenet, amelyeken különböző adattípusokhoz. Ezután használhatja ezt a karakterláncot egyetlen bemeneti helyett ismételt megadása az ugyanazon a forráson. Például egy JSON üzenet létrehozhat különböző bemenetei között. | 
| [**JSON elemzése**](#parse-json-action) | Hozzon létre felhasználóbarát adatjogkivonatok tulajdonságok JSON-tartalom így könnyebben használhatja a tulajdonságok a logic Apps. | 
||| 

Összetettebb JSON-átalakításokat létrehozásával kapcsolatban lásd: [végezze el a speciális Liquid sablonok JSON-átalakításokat](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md).

## <a name="prerequisites"></a>Előfeltételek

Kövesse a cikkben szereplő példákat, ezek az elemek szükségesek:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetésem, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókkal</a>.

* A logikai alkalmazás, ahol van szükség az adatok használata a művelet 

  Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps](../logic-apps/logic-apps-overview.md) és [a rövid útmutató: az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* A [eseményindító](../logic-apps/logic-apps-overview.md#logic-app-concepts) a logikai alkalmazás első lépéseként 

  Adatműveletek csak a műveletek, mint érhető el, így használata előtt ezeket a műveleteket a logikai alkalmazást egy trigger indít el, és tartalmazzák a kívánt kimenetek létrehozása szükséges bármely más műveletet.

<a name="compose-action"></a>

## <a name="compose-action"></a>Összeállítás műveletet

Hozza létre például egy JSON-objektum származó több bemenet egy egyetlen kimeneti, használhatja a **Adatműveletek – összeállítás** művelet. A bemeneteket különféle rendelkezhet, például az egész szám, logikai értékek, tömbök, JSON-objektumok és bármely más natív, írja be, hogy az Azure Logic Apps által támogatott, például bináris- és XML. Ezután használhatja a kimeneti műveleteket, amelyeket után kövesse a a **összeállítás** művelet. A **összeállítás** művelet is mentheti, az ismételt megadása ugyanazokkal a be, amíg hoz létre a logikai alkalmazás munkafolyamat. 

Ha például hozhatnak létre több változókat, például az emberek keresztnevét és vezetéknevét tároló karakterlánc típusú változót, és a egy egész szám típusú változó, amely tárolja az emberek életkorának JSON üzenetét. Itt a **összeállítás** művelet elfogadja ezeket a bemeneteket:

`{ "age": <ageVar>, "fullName": "<lastNameVar>, <firstNameVar>" }`

és ez a kimenet létrehozása:

`{"age":35,"fullName":"Owens,Sophie"}`

Példa kipróbálásához, kövesse az alábbi lépéseket a Logikaialkalmazás-Tervező használatával. Vagy, ha inkább a kódszerkesztőben nézetben dolgozik, másolja a példa **összeállítás** és **változó inicializálása** művelet definíciók, ez a cikk azokat a saját logikai alkalmazás munkafolyamat alapjául szolgáló definíció: [adatok művelet hitelesítésikód-példák - összeállítás](../logic-apps/logic-apps-data-operations-code-samples.md#compose-action-example) 

1. Az a <a href="https://portal.azure.com" target="_blank">az Azure portal</a> vagy a Visual Studio, a logikai alkalmazás megnyitása a Logikaialkalmazás-tervezőben. 

   Ebben a példában az Azure portal és a egy logikai alkalmazást egy **ismétlődési** eseményindító, és néhány **változó inicializálása** műveleteket. 
   Ezek a műveletek két karakterlánc típusú változót és a egy egész szám típusú változó beállítása. Ha később a logikai alkalmazás, manuálisan futtathatja az alkalmazás az eseményindító aktiválódik, várakozás nélkül.

   ![Kezdő minta logikai alkalmazás](./media/logic-apps-perform-data-operations/sample-starting-logic-app-compose-action.png)

2. A logikai alkalmazást, ahol szeretné létrehozni a kimenetet kövesse az alábbi lépéseket: 

   * Válassza ki az utolsó lépés a művelet hozzáadása, **új lépés** > **művelet hozzáadása**.

     ![Művelet hozzáadása](./media/logic-apps-perform-data-operations/add-compose-action.png)

   * Lépések közötti művelet hozzáadása, vigye az egérmutatót a csatlakozó mutató nyílra, megjelenik a plusz jelre (+). 
   Válassza a plusz jelre, majd válassza ki **művelet hozzáadása**.

3. A Keresés mezőbe írja be "összeállítás" szűrőként. Válassza ezt a műveletet a műveletek listájának: **összeállítás**

   !["Összeállítás" művelet kiválasztása](./media/logic-apps-perform-data-operations/select-compose-action.png)

4. Az a **bemenetek** adja meg a bemeneti adatok létrehozása a kimenet használni szeretne. 

   Ebben a példában, amikor kattint a **bemenetek** box, a dinamikus tartalmak listája jelenik meg választhatja ki a korábban létrehozott változókat:

   ![Válassza ki a bemeneti adatok írása](./media/logic-apps-perform-data-operations/configure-compose-action.png)

   A befejezett példa **összeállítás** művelet: 

   ![Befejezett "Összeállítás" művelet](./media/logic-apps-perform-data-operations/finished-compose-action.png)

5. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** parancsot.

Ez a művelet az alapul szolgáló munkafolyamat-definíció a kapcsolatos további információkért lásd: a [összeállítás műveletet](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action). 

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

Győződjön meg arról, hogy e a **összeállítás** művelet a várt eredményt hoz, küldhet saját magának egy értesítés, hogy a kimenet tartalmazza a **összeállítás** művelet.

1. A logikai alkalmazást, adja hozzá egy műveletet, amely küldheti el Önnek eredményeinek a **összeállítás** művelet.

2. A művelet kattintson bárhol azt szeretné, hogy az eredmények jelenjenek meg. A dinamikus tartalmú listából megnyitásakor, a a **összeállítás** műveletet, válassza **kimeneti**. 

   Ez a példa a **Office 365 Outlook – e-mail küldése** művelet, és tartalmazza a **kimeneti** az e-mailben törzsére és tárgyára illesztett mezők:

   ![Az "E-mail küldése" művelet "Kimeneti" mezője](./media/logic-apps-perform-data-operations/send-email-compose-action.png)

3. Most futtassa kézzel a logikai alkalmazást. A Tervező eszköztárán válassza **futtatása**. 

   Alapján használt e-mail-összekötőt, az alábbiakban az eredményeket kap:

   ![E-mailt az "Összeállítás" műveleti eredmények](./media/logic-apps-perform-data-operations/compose-email-results.png)

<a name="create-csv-table-action"></a>

## <a name="create-csv-table-action"></a>CSV-táblázat művelet létrehozása

Hozzon létre egy vesszővel tagolt (CSV) tábla, amelynek a tulajdonságok és a JavaScript Object Notation (JSON) objektumok értékeit a tömbben, használja a **Adatműveletek – CSV-táblázat létrehozása** művelet. Ezután használhatja az eredményül kapott tábla az alábbi műveleteket a **CSV-táblázat létrehozása** művelet. 

Ha inkább a kódszerkesztőben nézetben dolgozik, másolja a példa **CSV-táblázat létrehozása** és **változó inicializálása** művelet definíciók, ez a cikk azokat a saját logikai alkalmazás munkafolyamat alapjául szolgáló definíció: [adatok művelet hitelesítésikód-példák – CSV-táblázat létrehozása](../logic-apps/logic-apps-data-operations-code-samples.md#create-csv-table-action-example) 

1. Az a <a href="https://portal.azure.com" target="_blank">az Azure portal</a> vagy a Visual Studio, a logikai alkalmazás megnyitása a Logikaialkalmazás-tervezőben. 

   Ebben a példában az Azure portal és a egy logikai alkalmazást egy **ismétlődési** eseményindító és a egy **változó inicializálása** művelet. 
   A művelet létrehozásához egy változót, amelynek kezdeti értéke egy tömb, amely rendelkezik az egyes tulajdonságok és értékek JSON formátumban van beállítva. 
   Ha később a logikai alkalmazás, manuálisan futtathatja az alkalmazás az eseményindító aktiválódik, várakozás nélkül.

   ![Kezdő minta logikai alkalmazás](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

2. A logikai alkalmazást, ahol szeretné a CSV-táblázat létrehozása kövesse az alábbi lépéseket: 

   * Válassza ki az utolsó lépés a művelet hozzáadása, **új lépés** > **művelet hozzáadása**.

     ![Művelet hozzáadása](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Lépések közötti művelet hozzáadása, vigye az egérmutatót a csatlakozó mutató nyílra, megjelenik a plusz jelre (+). 
   Válassza a plusz jelre, majd válassza ki **művelet hozzáadása**.

3. A Keresés mezőbe írja be a "csv-táblázat létrehozása" szűrőként. Válassza ezt a műveletet a műveletek listájának: **CSV-táblázat létrehozása**

   ![Jelölje be "Create CSV table" művelet](./media/logic-apps-perform-data-operations/select-create-csv-table-action.png)

4. Az a **a** adja meg a tömböt, vagy a kifejezés a tábla létrehozásához használni szeretne. 

   Ebben a példában, amikor kattint a **a** mezőben a dinamikus tartalmak listája jelenik meg, ezért kiválaszthatja, hogy a korábban létrehozott változó:

   ![Válassza ki a kimenete tömb CSV-táblázat létrehozása](./media/logic-apps-perform-data-operations/configure-create-csv-table-action.png)

   A befejezett példa **CSV-táblázat létrehozása** művelet: 

   ![Befejeződött a "Create CSV table" művelet](./media/logic-apps-perform-data-operations/finished-create-csv-table-action.png)

   Alapértelmezés szerint ez a művelet automatikusan létrehoz az oszlopokat a tömb cikkek alapján. 
   Az oszlopfejléceket és az értékek manuális létrehozásához válassza a **speciális beállítások megjelenítése**. 
   Csak egyéni értékeket ad meg, módosítsa **oszlopok** való **egyéni**. 
   Egyéni oszlopazonosítók túl megadásához módosítsa **közé tartoznak a fejlécek** való **Igen**. 

   > [!TIP]
   > A JSON-objektumok a Tulajdonságok felhasználóbarát jogkivonatok létrehozásához, ezért kiválaszthatja azokat a tulajdonságokat bemenetként, használja a [JSON elemzése](#parse-json-action) hívása előtt a **CSV-táblázat létrehozása** művelet.

5. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** parancsot.

Ez a művelet az alapul szolgáló munkafolyamat-definíció a kapcsolatos további információkért lásd: a [táblájának művelete](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

Ellenőrizze-e a **CSV-táblázat létrehozása** művelet a várt eredményt hoz, küldhet saját magának egy értesítés, hogy a kimenet tartalmazza a **CSV-táblázat létrehozása** művelet.

1. A logikai alkalmazást, adja hozzá egy műveletet, amely küldheti el Önnek eredményeinek a **CSV-táblázat létrehozása** művelet.

2. A művelet kattintson bárhol azt szeretné, hogy az eredmények jelenjenek meg. A dinamikus tartalmú listából megnyitásakor, a a **CSV-táblázat létrehozása** műveletet, válassza **kimeneti**. 

   Ez a példa a **Office 365 Outlook – e-mail küldése** művelet, és tartalmazza a **kimeneti** az e-mail törzsének mezőbe:

   ![Az "E-mail küldése" művelet "Kimeneti" mezője](./media/logic-apps-perform-data-operations/send-email-create-csv-table-action.png)

3. Most futtassa kézzel a logikai alkalmazást. A Tervező eszköztárán válassza **futtatása**. 

   Alapján használt e-mail-összekötőt, az alábbiakban az eredményeket kap:

   ![E-mailen keresztül "Create CSV table" műveleti eredmények](./media/logic-apps-perform-data-operations/create-csv-table-email-results.png)

<a name="create-html-table-action"></a>

## <a name="create-html-table-action"></a>HTML-táblázat művelet létrehozása

Hozzon létre egy HTML-táblázat, amelynek a tulajdonságok és a JavaScript Object Notation (JSON) objektumok értékeit a tömbben, használja a **Adatműveletek – HTML-táblázat létrehozása** művelet. Ezután használhatja az eredményül kapott tábla az alábbi műveleteket a **HTML-táblázat létrehozása** művelet.

Ha inkább a kódszerkesztőben nézetben dolgozik, másolja a példa **HTML-táblázat létrehozása** és **változó inicializálása** művelet definíciók, ez a cikk azokat a saját logikai alkalmazás munkafolyamat alapjául szolgáló definíció: [adatok művelet hitelesítésikód-példák – HTML-táblázat létrehozása](../logic-apps/logic-apps-data-operations-code-samples.md#create-html-table-action-example) 

1. Az a <a href="https://portal.azure.com" target="_blank">az Azure portal</a> vagy a Visual Studio, a logikai alkalmazás megnyitása a Logikaialkalmazás-tervezőben. 

   Ebben a példában az Azure portal és a egy logikai alkalmazást egy **ismétlődési** eseményindító és a egy **változó inicializálása** művelet. 
   A művelet létrehozásához egy változót, amelynek kezdeti értéke egy tömb, amely rendelkezik az egyes tulajdonságok és értékek JSON formátumban van beállítva. 
   Ha később a logikai alkalmazás, manuálisan futtathatja az alkalmazás az eseményindító aktiválódik, várakozás nélkül.

   ![Kezdő minta logikai alkalmazás](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

2. A logikai alkalmazást, ahol szeretné egy HTML-táblázat létrehozása kövesse az alábbi lépéseket: 

   * Válassza ki az utolsó lépés a művelet hozzáadása, **új lépés** > **művelet hozzáadása**.

     ![Művelet hozzáadása](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Lépések közötti művelet hozzáadása, vigye az egérmutatót a csatlakozó mutató nyílra, megjelenik a plusz jelre (+). 
   Válassza a plusz jelre, majd válassza ki **művelet hozzáadása**.

3. A Keresés mezőbe írja be a "html-táblázat létrehozása" szűrőként. Válassza ezt a műveletet a műveletek listájának: **HTML-táblázat létrehozása**

   ![Válassza ki a "Create HTML table" műveletet](./media/logic-apps-perform-data-operations/select-create-html-table-action.png)

4. Az a **a** adja meg a tömböt, vagy a kifejezés a tábla létrehozásához használni szeretne. 

   Ebben a példában, amikor kattint a **a** mezőben a dinamikus tartalmak listája jelenik meg, ezért kiválaszthatja, hogy a korábban létrehozott változó:

   ![Válassza ki a kimenete tömb HTML-táblázat létrehozása](./media/logic-apps-perform-data-operations/configure-create-html-table-action.png)

   A befejezett példa **HTML-táblázat létrehozása** művelet: 

   ![A "Create HTML table" művelet befejeződött](./media/logic-apps-perform-data-operations/finished-create-html-table-action.png)

   Alapértelmezés szerint ez a művelet automatikusan létrehoz az oszlopokat a tömb cikkek alapján. 
   Az oszlopfejléceket és az értékek manuális létrehozásához válassza a **speciális beállítások megjelenítése**. 
   Csak egyéni értékeket ad meg, módosítsa **oszlopok** való **egyéni**. 
   Egyéni oszlopazonosítók túl megadásához módosítsa **közé tartoznak a fejlécek** való **Igen**. 

   > [!TIP]
   > A JSON-objektumok a Tulajdonságok felhasználóbarát jogkivonatok létrehozásához, ezért kiválaszthatja azokat a tulajdonságokat bemenetként, használja a [JSON elemzése](#parse-json-action) hívása előtt a **HTML-táblázat létrehozása** művelet.

5. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** parancsot.

Ez a művelet az alapul szolgáló munkafolyamat-definíció a kapcsolatos további információkért lásd: a [táblájának művelete](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

Ellenőrizze-e a **HTML-táblázat létrehozása** művelet a várt eredményt hoz, küldhet saját magának egy értesítés, hogy a kimenet tartalmazza a **HTML-táblázat létrehozása** művelet.

1. A logikai alkalmazást, adja hozzá egy műveletet, amely küldheti el Önnek eredményeinek a **HTML-táblázat létrehozása** művelet.

2. A művelet kattintson bárhol azt szeretné, hogy az eredmények jelenjenek meg. A dinamikus tartalmú listából megnyitásakor, a a **HTML-táblázat létrehozása** műveletet, válassza **kimeneti**. 

   Ez a példa a **Office 365 Outlook – e-mail küldése** művelet, és tartalmazza a **kimeneti** az e-mail törzsének mezőbe:

   ![Az "E-mail küldése" művelet "Kimeneti" mezője](./media/logic-apps-perform-data-operations/send-email-create-html-table-action.png)
   
   > [!NOTE]
   > E-mail-művelet a kimenetet a HTML-táblázat például, ha győződjön meg arról, hogy állítsa a **HTML** tulajdonságot **Igen** az e-mailben művelet speciális beállítások. Ezzel a módszerrel az e-mail-művelet a HTML-táblázat megfelelően formázza.

3. Most futtassa kézzel a logikai alkalmazást. A Tervező eszköztárán válassza **futtatása**. 

   Alapján használt e-mail-összekötőt, az alábbiakban az eredményeket kap:

   ![E-mailt a "Create HTML table" műveleti eredmények](./media/logic-apps-perform-data-operations/create-html-table-email-results.png)

<a name="filter-array-action"></a>

## <a name="filter-array-action"></a>Tömb szűrése művelet

Hozzon létre egy kisebb tömb, amely rendelkezik a cikkek, amelyek az adott feltételnek, a meglévő tömböt, használja a **Adatműveletek – tömb szűrése** művelet. Ezután használhatja a szűrt tömb után az alábbi műveleteket a **tömb szűrése** művelet. 

> [!NOTE]
> A feltételben használt szűrő szövege nem kis-és nagybetűket. Ez a művelet is, formázását vagy összetevői a tömbben szereplő elemek nem módosítható. 
> 
> A műveletek kimenete tömb használata a **tömb szűrése** műveletet, vagy a műveletek el kell fogadnia tömbök bemenetként, vagy lehetséges, hogy átalakítja a kimeneti tömbben kompatibilis egy másik formátumba. 

Ha inkább a kódszerkesztőben nézetben dolgozik, másolja a példa **tömb szűrése** és **változó inicializálása** művelet definíciók, ez a cikk azokat a saját logikai alkalmazás munkafolyamat alapjául szolgáló definíció: [adatok művelet hitelesítésikód-példák – tömb szűrése](../logic-apps/logic-apps-data-operations-code-samples.md#filter-array-action-example) 

1. Az a <a href="https://portal.azure.com" target="_blank">az Azure portal</a> vagy a Visual Studio, a logikai alkalmazás megnyitása a Logikaialkalmazás-tervezőben. 

   Ebben a példában az Azure portal és a egy logikai alkalmazást egy **ismétlődési** eseményindító és a egy **változó inicializálása** művelet. 
   A művelet egy változót, amelynek kezdeti értéke egy tömb, amely rendelkezik néhány minta egész számok létrehozására van beállítva. Ha később a logikai alkalmazás, manuálisan futtathatja az alkalmazás az eseményindító aktiválódik, várakozás nélkül.

   > [!NOTE]
   > Bár ebben a példában egy egyszerű egész számok tömbje, ez a művelet különösen hasznos az objektumok tulajdonságok és értékek alapján szűrheti, tömbök JSON-objektum.

   ![Kezdő minta logikai alkalmazás](./media/logic-apps-perform-data-operations/sample-starting-logic-app-filter-array-action.png)

2. A logikai alkalmazást, ahol szeretné létrehozni a szűrt tömb kövesse az alábbi lépéseket: 

   * Válassza ki az utolsó lépés a művelet hozzáadása, **új lépés** > **művelet hozzáadása**.

     ![Művelet hozzáadása](./media/logic-apps-perform-data-operations/add-filter-array-action.png)

   * Lépések közötti művelet hozzáadása, vigye az egérmutatót a csatlakozó mutató nyílra, megjelenik a plusz jelre (+). 
   Válassza a plusz jelre, majd válassza ki **művelet hozzáadása**.

3. A Keresés mezőbe írja be a "tömb szűrése" szűrőként. Válassza ezt a műveletet a műveletek listájának: **tömb szűrése**

   !["A tömb szűrése" művelet kiválasztása](./media/logic-apps-perform-data-operations/select-filter-array-action.png)

4. Az a **a** adja meg a tömböt vagy szűrni szeretné kifejezés. 

   Ebben a példában, amikor kattint a **a** mezőben a dinamikus tartalmak listája jelenik meg, ezért kiválaszthatja, hogy a korábban létrehozott változó:

   ![Válassza ki a kimenete tömb szűrt tömb létrehozása](./media/logic-apps-perform-data-operations/configure-filter-array-action.png)

5. A feltételhez adja meg, hasonlítsa össze, válassza ki az összehasonlító operátor a tömb elemet, és adja meg az összehasonlítási érték.

   Ez a példa a **item()** függvény közben a tömbben lévő egyes elemek eléréséhez a **tömb szűrése** a művelet megkeresi a tömb elemei, amelynek az értéke az 1-nél nagyobb:
   
   !["A tömb szűrése" művelet befejeződött](./media/logic-apps-perform-data-operations/finished-filter-array-action.png)

6. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** parancsot.

Ez a művelet az alapul szolgáló munkafolyamat-definíció a kapcsolatos további információkért lásd: [lekérdezési művelet](../logic-apps/logic-apps-workflow-actions-triggers.md#query-action).

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

Győződjön meg arról, hogy e **tömb szűrése** művelet a várt eredményt hoz, küldhet saját magának egy értesítés, hogy a kimenet tartalmazza a **tömb szűrése** művelet.

1. A logikai alkalmazást, adja hozzá egy műveletet, amely küldheti el Önnek eredményeinek a **tömb szűrése** művelet.

2. A művelet kattintson bárhol azt szeretné, hogy az eredmények jelenjenek meg. Amikor megnyílik a dinamikus tartalmú listából, válassza ki a **kifejezés**. A tömb eredményének a **tömb szűrése** művelet, adja meg a kifejezés, amely tartalmazza a **tömb szűrése** művelet neve:

   ```
   @actionBody('Filter_array')
   ```

   Ez a példa a **Office 365 Outlook – e-mail küldése** művelet és a kimenő adatait tartalmazza a **actionBody('Filter_array')** az e-mail törzsének kifejezés:

   ![A művelet a "E-mail küldése" művelet kimenete](./media/logic-apps-perform-data-operations/send-email-filter-array-action.png)

3. Most futtassa kézzel a logikai alkalmazást. A Tervező eszköztárán válassza **futtatása**. 

   Alapján használt e-mail-összekötőt, az alábbiakban az eredményeket kap:

   ![E-mailt "Tömb szűrése" műveleti eredmények](./media/logic-apps-perform-data-operations/filter-array-email-results.png)

<a name="join-action"></a>

## <a name="join-action"></a>JOIN művelet

Hozzon létre egy karakterláncot, amely rendelkezik egy tömb összes elemét, és azok az elemek elválasztására meghatározott elválasztó karakterrel, használja a **Adatműveletek – összekapcsolás** művelet. Ezután használhatja a karakterlánc a műveleteket, amelyeket után kövesse a **csatlakozzon** művelet.

Ha inkább a kódszerkesztőben nézetben dolgozik, másolja a példa **csatlakozzon** és **változó inicializálása** művelet definíciók Ez a cikk a saját logikai alkalmazás be tartozó munkafolyamat-definíció: [ Adatok művelet hitelesítésikód-példák – csatlakozás](../logic-apps/logic-apps-data-operations-code-samples.md#join-action-example) 

1. Az a <a href="https://portal.azure.com" target="_blank">az Azure portal</a> vagy a Visual Studio, a logikai alkalmazás megnyitása a Logikaialkalmazás-tervezőben. 

   Ebben a példában az Azure portal és a egy logikai alkalmazást egy **ismétlődési** eseményindító és a egy **változó inicializálása** művelet. 
   Ez a művelet egy változót, amelynek kezdeti értéke egy tömb, amely rendelkezik néhány minta egész számok létrehozására van beállítva. 
   Ha a logikai alkalmazás újabb teszteléséhez manuálisan futtathatja az alkalmazás az eseményindító aktiválódik, várakozás nélkül.

   ![Kezdő minta logikai alkalmazás](./media/logic-apps-perform-data-operations/sample-starting-logic-app-join-action.png)

2. A logikai alkalmazást, ahol szeretné a karakterlánc létrehozása a tömbben kövesse az alábbi lépéseket: 

   * Válassza ki az utolsó lépés a művelet hozzáadása, **új lépés** > **művelet hozzáadása**.

     ![Művelet hozzáadása](./media/logic-apps-perform-data-operations/add-join-action.png)

   * Lépések közötti művelet hozzáadása, vigye az egérmutatót a csatlakozó mutató nyílra, megjelenik a plusz jelre (+). 
   Válassza a plusz jelre, majd válassza ki **művelet hozzáadása**.

3. A Keresés mezőbe írja be "Csatlakozás" szűrőként. Válassza ezt a műveletet a műveletek listájának: **csatlakozás**

   !["Adatok Operations – csatlakoztatás" művelet kiválasztása](./media/logic-apps-perform-data-operations/select-join-action.png)

4. Az a **a** adja meg a tömb, amely rendelkezik a cikkek karakterláncként csatlakozni szeretne. 

   Ebben a példában, amikor kattint a **a** mezőt, a dinamikus tartalmak listája jelenik meg, ezért kiválaszthatja, hogy a korábban létrehozott változó:  

   ![Válassza ki a tömb kimeneti karakterlánc létrehozásához](./media/logic-apps-perform-data-operations/configure-join-action.png)

5. Az a **összekapcsolás a következővel** mezőbe írja be a kívánt elkülöníthető a tömb mindegyik elemén karaktert. 

   Ez a példa egy kettőspontot (:) használ elválasztóként.

   ![Adja meg az elválasztó karaktert.](./media/logic-apps-perform-data-operations/finished-join-action.png)

6. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** parancsot.

Ez a művelet az alapul szolgáló munkafolyamat-definíció a kapcsolatos további információkért lásd: a [Join művelet](../logic-apps/logic-apps-workflow-actions-triggers.md#join-action).

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

Győződjön meg arról, hogy e a **csatlakozzon** művelet a várt eredményt hoz, küldhet saját magának egy értesítés, hogy a kimenet tartalmazza a **csatlakozzon** művelet. 

1. A logikai alkalmazást, adja hozzá egy műveletet, amely küldheti el Önnek eredményeinek a **csatlakozzon** művelet.

2. A művelet kattintson bárhol azt szeretné, hogy az eredmények jelenjenek meg. A dinamikus tartalmú listából megnyitásakor, a a **csatlakozzon** műveletet, válassza **kimeneti**. 

   Ez a példa a **Office 365 Outlook – e-mail küldése** művelet, és tartalmazza a **kimeneti** az e-mail törzsének mezőbe:

   ![Az "E-mail küldése" művelet "Kimeneti" mezője](./media/logic-apps-perform-data-operations/send-email-join-action.png)

3. Most futtassa kézzel a logikai alkalmazást. A Tervező eszköztárán válassza **futtatása**. 

   Alapján használt e-mail-összekötőt, az alábbiakban az eredményeket kap:

   ![E-mailt "Csatlakozás" műveleti eredmények](./media/logic-apps-perform-data-operations/join-email-results.png)

<a name="parse-json-action"></a>

## <a name="parse-json-action"></a>A művelet JSON elemzése

Hivatkozás vagy a hozzáférés tulajdonságainak a JavaScript Object Notation (JSON) tartalom használatával hozhat létre felhasználóbarát mezőket és a jogkivonatok ezeknél a tulajdonságoknál a **Adatműveletek – JSON elemzése** művelet.
Ezzel a módszerrel kiválaszthatja azokat a tulajdonságokat a dinamikus tartalmú listából bemenetek megadása a logikai alkalmazás esetén. Ez a művelet egy JSON-sémát adjon meg vagy is létre egy JSON-séma a JSON-tartalmak minta vagy adattartalom.

Ha inkább a kódszerkesztőben nézetben dolgozik, másolja a példa **JSON elemzése** és **változó inicializálása** művelet definíciók Ez a cikk a saját logikai alkalmazás be tartozó munkafolyamat-definíció : [Adatok művelet hitelesítésikód-példák – JSON elemzése](../logic-apps/logic-apps-data-operations-code-samples.md#parse-json-action-example) 

1. Az a <a href="https://portal.azure.com" target="_blank">az Azure portal</a> vagy a Visual Studio, a logikai alkalmazás megnyitása a Logikaialkalmazás-tervezőben. 

   Ebben a példában az Azure portal és a egy logikai alkalmazást egy **ismétlődési** eseményindító és a egy **változó inicializálása** művelet. 
   A művelet egy változót, amelynek kezdeti értéke, amelynek a tulajdonságait és értékeit egy JSON-objektum létrehozásának van beállítva. 
   Ha később a logikai alkalmazás, manuálisan futtathatja az alkalmazás az eseményindító aktiválódik, várakozás nélkül.

   ![Kezdő minta logikai alkalmazás](./media/logic-apps-perform-data-operations/sample-starting-logic-app-parse-json-action.png)

2. A logikai alkalmazásban kívánja elemezni a JSON-tartalmak kövesse az alábbi lépéseket: 

   * Válassza ki az utolsó lépés a művelet hozzáadása, **új lépés** > **művelet hozzáadása**.

     ![Művelet hozzáadása](./media/logic-apps-perform-data-operations/add-parse-json-action.png)

   * Lépések közötti művelet hozzáadása, vigye az egérmutatót a csatlakozó mutató nyílra, megjelenik a plusz jelre (+). 
   Válassza a plusz jelre, majd válassza ki **művelet hozzáadása**.

3. A Keresés mezőbe írja be a "json elemzése" szűrőként. Válassza ezt a műveletet a műveletek listájának: **JSON elemzése**

   ![Válassza ki a "JSON elemzése" műveletet](./media/logic-apps-perform-data-operations/select-parse-json-action.png)

4. Az a **tartalom** adja meg a JSON-tartalmak elemezni szeretné. 

   Ebben a példában, amikor kattint a **tartalom** mezőben a dinamikus tartalmak listája jelenik meg, ezért kiválaszthatja, hogy a korábban létrehozott változó:

   ![JSON elemzése művelet a JSON-objektum kiválasztása](./media/logic-apps-perform-data-operations/configure-parse-json-action.png)

5. Adja meg az éppen elemzés JSON-tartalmak leíró JSON-sémájában. 

   Ebben a példában a következő JSON-sémája:

   ![Adja meg az elemezni kívánt JSON-objektum JSON-sémája](./media/logic-apps-perform-data-operations/provide-schema-parse-json-action.png)

   Ha nem rendelkezik a séma, is létrehozhat, hogy a séma a JSON-tartalmak a vagy *adattartalom*, éppen elemzés. 
   
   1. Az a **JSON elemzése** műveletet, válassza **Mintaadat használata séma létrehozásához**.

   2. A **írja vagy illessze be a JSON hasznosadat-minta**, adja meg a JSON-tartalmak, és válassza **kész**.

      ![Adja meg a JSON-tartalmak a séma generálásához](./media/logic-apps-perform-data-operations/generate-schema-parse-json-action.png)

6. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** parancsot.

Ez a művelet az alapul szolgáló munkafolyamat-definíció a kapcsolatos további információkért lásd: [JSON elemzése művelet](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

Ellenőrizze-e a **JSON elemzése** művelet a várt eredményt hoz, küldhet saját magának egy értesítés, hogy a kimenet tartalmazza a **JSON elemzése** művelet.

1. A logikai alkalmazást, adja hozzá egy műveletet, amely küldheti el Önnek eredményeinek a **JSON elemzése** művelet.

2. A művelet kattintson bárhol azt szeretné, hogy az eredmények jelenjenek meg. A dinamikus tartalmú listából megnyitásakor, a a **JSON elemzése** művelet, most kiválaszthatja a tulajdonságokat az elemzett JSON-tartalom.

   Ez a példa a **Office 365 Outlook – e-mail küldése** művelet, és tartalmazza a **FirstName**, **LastName**, és **E-mail** mezőt a e-mail törzse:

   ![Az "E-mail küldése" műveletet a JSON-tulajdonságokkal](./media/logic-apps-perform-data-operations/send-email-parse-json-action.png)

   A befejezett e-mail-művelet a következő:

   ![Befejezett e-mail-művelet](./media/logic-apps-perform-data-operations/send-email-parse-json-action-2.png)

3. Most futtassa kézzel a logikai alkalmazást. A Tervező eszköztárán válassza **futtatása**. 

   Alapján használt e-mail-összekötőt, az alábbiakban az eredményeket kap:

   ![E-mailt "Csatlakozás" műveleti eredmények](./media/logic-apps-perform-data-operations/parse-json-email-results.png)

<a name="select-action"></a>

## <a name="select-action"></a>Művelet kiválasztása

Egy tömb, amely rendelkezik egy meglévő tömbben szereplő értékek alapján összeállított JSON-objektumok létrehozásához használja a **Adatműveletek – kiválasztás** művelet. Például létrehozhat egy JSON-objektum minden egyes érték az egész számok tömbje úgy, hogy az egyes JSON-objektum tulajdonságait, és hogyan képezhet le az értékeket a forrástömb azokat a tulajdonságokat. És jóllehet módosíthatja azokat az objektumokat a JSON-összetevők, a kimeneti tömbben mindig a forrástömb azonos számú elemet.

> [!NOTE]
> A műveletek kimenete tömb használata a **válassza ki** műveletet, vagy a műveletek el kell fogadnia tömbök bemenetként, vagy lehetséges, hogy átalakítja a kimeneti tömbben kompatibilis egy másik formátumba. 

Ha inkább a kódszerkesztőben nézetben dolgozik, másolja a példa **kiválasztása** és **változó inicializálása** művelet definíciók Ez a cikk a saját logikai alkalmazás be tartozó munkafolyamat-definíció: [Adatok művelet hitelesítésikód-példák – kiválasztása](../logic-apps/logic-apps-data-operations-code-samples.md#select-action-example) 

1. Az a <a href="https://portal.azure.com" target="_blank">az Azure portal</a> vagy a Visual Studio, a logikai alkalmazás megnyitása a Logikaialkalmazás-tervezőben. 

   Ebben a példában az Azure portal és a egy logikai alkalmazást egy **ismétlődési** eseményindító és a egy **változó inicializálása** művelet. 
   A művelet egy változót, amelynek kezdeti értéke egy tömb, amely rendelkezik néhány minta egész számok létrehozására van beállítva. 
   Ha később a logikai alkalmazás, manuálisan futtathatja az alkalmazás az eseményindító aktiválódik, várakozás nélkül.

   ![Kezdő minta logikai alkalmazás](./media/logic-apps-perform-data-operations/sample-starting-logic-app-select-action.png)

2. A logikai alkalmazást, ahol szeretné létrehozni a tömbhöz kövesse az alábbi lépéseket: 

   * Válassza ki az utolsó lépés a művelet hozzáadása, **új lépés** > **művelet hozzáadása**.

     ![Művelet hozzáadása](./media/logic-apps-perform-data-operations/add-select-action.png)

   * Lépések közötti művelet hozzáadása, vigye az egérmutatót a csatlakozó mutató nyílra, megjelenik a plusz jelre (+). 
   Válassza a plusz jelre, majd válassza ki **művelet hozzáadása**.

3. A Keresés mezőbe írja be "kijelölése" szűrőként. Válassza ezt a műveletet a műveletek listájának: **kiválasztása**

   ![A "Kiválasztás" művelet kiválasztása](./media/logic-apps-perform-data-operations/select-select-action.png)

4. Az a **a** adja meg a kívánt forrástömb.

   Ebben a példában, amikor kattint a **a** mezőben a dinamikus tartalmak listája jelenik meg, ezért kiválaszthatja, hogy a korábban létrehozott változó:

   ![Válassza ki a Kiválasztás művelet forrástömb](./media/logic-apps-perform-data-operations/configure-select-action.png)

5. Az a **térkép** mező a bal oldali oszlopban, adja meg a forrás tömbben szereplő összes értékhez hozzárendelni kívánt tulajdonság nevét. A jobb oldali oszlopban adja meg egy kifejezés, amely kíván rendelni a tulajdonság értékét jelöli.

   Ebben a példában "Product_ID" hozzárendelése a egésztömb minden érték használatával a tulajdonság nevét határozza meg a **item()** függvény, amely hozzáfér a tömb mindegyik elemén kifejezésben. 

   ![Adja meg a JSON-objektumtulajdonság és az értékek a létrehozni kívánt tömb](./media/logic-apps-perform-data-operations/configure-select-action-2.png)

   A befejezett művelet a következő:

   ![Válassza a művelet befejeződött](./media/logic-apps-perform-data-operations/finished-select-action.png)

6. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** parancsot.

Ez a művelet az alapul szolgáló munkafolyamat-definíció a kapcsolatos további információkért lásd: [művelet kiválasztása](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

Győződjön meg arról, hogy e a **válassza** művelet a várt eredményt hoz, küldhet saját magának egy értesítés, hogy a kimenet tartalmazza a **válassza ki** művelet.

1. A logikai alkalmazást, adja hozzá egy műveletet, amely küldheti el Önnek eredményeinek a **kiválasztása** művelet.

2. A művelet kattintson bárhol azt szeretné, hogy az eredmények jelenjenek meg. Amikor megnyílik a dinamikus tartalmú listából, válassza ki a **kifejezés**. Megszerezni a kimenete tömb a **válassza** művelet, adja meg a kifejezés, amely tartalmazza a **válassza** művelet neve:

   ```
   @actionBody('Select')
   ```

   Ez a példa a **Office 365 Outlook – e-mail küldése** művelet és a kimenő adatait tartalmazza a **actionBody('Select')** az e-mail törzsének kifejezés:

   ![A művelet a "E-mail küldése" művelet kimenete](./media/logic-apps-perform-data-operations/send-email-select-action.png)

3. Most futtassa kézzel a logikai alkalmazást. A Tervező eszköztárán válassza **futtatása**. 

   Alapján használt e-mail-összekötőt, az alábbiakban az eredményeket kap:

   ![E-mailt a "Kiválasztás" művelet eredménye](./media/logic-apps-perform-data-operations/select-email-results.png)

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* Ismerje meg [Logic Apps-összekötők](../connectors/apis-list.md)
