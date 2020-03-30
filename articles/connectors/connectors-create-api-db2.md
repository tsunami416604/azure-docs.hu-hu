---
title: Az IBM DB2 erőforrásainak elérése és kezelése
description: IBM DB2-erőforrások olvasása, szerkesztése, frissítése és kezelése automatizált munkafolyamatok azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: plarsen, logicappspm
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: 32b482607827ee4420e39b1936586d64f9ea3139
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651381"
---
# <a name="access-and-manage-ibm-db2-resources-by-using-azure-logic-apps"></a>AZ IBM DB2-erőforrások elérése és kezelése az Azure Logic Apps használatával

Az [Azure Logic Apps](../logic-apps/logic-apps-overview.md) és az IBM [DB2-összekötő](/connectors/db2/)segítségével automatizált feladatokat és munkafolyamatokat hozhat létre a DB2-adatbázisban tárolt erőforrások alapján. A munkafolyamatok kapcsolódhatnak az adatbázis erőforrásaihoz, elolvashatják és listázhatják az adatbázistáblákat, sorokat adhatnak hozzá, sorokat válthatnak be, sorokat törölhetnek stb. A logikai alkalmazásokban olyan műveleteket is beilleszthet, amelyek válaszokat kapnak az adatbázisból, és elérhetővé teszik a kimenetet más műveletek számára.

Ez a cikk bemutatja, hogyan hozhat létre egy logikai alkalmazást, amely különböző adatbázis-műveleteket hajt végre. Ha most kezdi meg a logikai alkalmazások, tekintse át [az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="supported-platforms-and-versions"></a>Támogatott platformok és verziók

A DB2-összekötő tartalmaz egy Microsoft-ügyfelet, amely tcp/IP-hálózaton keresztül kommunikál a távoli DB2 kiszolgálókkal. Ezt az összekötőt felhőalapú adatbázisok, például az IBM DB2 azure-virtualizációs futó eléréséhez használhatja. A [helyszíni adatátjáró telepítése és beállítása](../logic-apps/logic-apps-gateway-connection.md)után a helyszíni DB2 adatbázisokat is elérheti.

Az IBM DB2 csatlakozó támogatja ezeket az IBM DB2 platformokat és verziókat, valamint az IBM DB2-kompatibilis termékeket, amelyek támogatják a Distributed Relational Database Architecture (DRDA) SQL Access Manager (SQLAM) 10-es és 11-es verzióit:

| Platform | Verzió | 
|----------|---------|
| IBM DB2 z/OS-hez | 11.1, 10.1 |
| IBM DB2 az i-hez | 7.3, 7.2, 7.1 |
| IBM DB2 a LUW-hoz | 11, 10.5 |
|||

## <a name="supported-database-operations"></a>Támogatott adatbázis-műveletek

Az IBM DB2 összekötő támogatja ezeket az adatbázis-műveleteket, amelyek leképezik az összekötő megfelelő műveleteit:

| Adatbázis-művelet | Összekötő művelet |
|--------------------|------------------|
| Adatbázistáblák listázása | Táblák beszerezhetése |
| Egy sor olvasása a SELECT használatával | Sor bekerülése |
| Az összes sor olvasása a SELECT használatával | Sorok bekerülése |
| Egy sor hozzáadása az INSERT használatával | Sor beszúrása |
| Egy sor szerkesztése az UPDATE használatával | Sor frissítése |
| Egy sor eltávolítása a DELETE használatával | Sor törlése |
|||

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* IBM DB2 adatbázis, akár felhőalapú, akár helyszíni

* Alapvető ismeretek [a logikai alkalmazások létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazás, ahol a DB2-adatbázis eléréséhez hozzá szeretne férni. Ez az összekötő csak műveleteket biztosít, így a logikai alkalmazás elindításához válasszon ki egy külön eseményindítót, például az **Ismétlődés eseményindítót.**
Ebben a cikkben szereplő példák az **Ismétlődés eseményindítót** használják.

<a name="add-db2-action"></a>

## <a name="add-db2-action---get-tables"></a>DB2-művelet hozzáadása – Táblák leése

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic App Designerben, ha még nem nyitott.

1. Az eseményindító alatt válassza az **Új lépés lehetőséget.**

1. A keresőmezőbe írja be szűrőként a "db2" értéket. Ebben a példában a műveletek listájában válassza a következő műveletet: **Táblák beszerezni (Előzetes verzió)**

   ![Művelet kijelölése](./media/connectors-create-api-db2/select-db2-action.png)

   A rendszer most kéri, hogy adja meg a DB2-adatbázis kapcsolatadatait.

1. Kövesse a [felhőalapú adatbázisok](#cloud-connection) vagy [helyszíni adatbázisok](#on-premises-connection)kapcsolatainak létrehozásához szükséges lépéseket.

<a name="cloud-connection"></a>

## <a name="connect-to-cloud-db2"></a>Csatlakozás a felhőalapú DB2-hez

A kapcsolat beállításához adja meg ezeket a kapcsolatadatait, amikor a rendszer kéri, válassza **a Létrehozás**lehetőséget, majd mentse a logikai alkalmazást:

| Tulajdonság | Kötelező | Leírás |
|----------|----------|-------------|
| **Csatlakozás helyszíni átjárón keresztül** | Nem | Csak a helyszíni kapcsolatokra vonatkozik. |
| **Kapcsolat neve** | Igen | A kapcsolat neve, például "MyLogicApp-DB2-connection" |
| **Kiszolgáló** | Igen | A DB2-kiszolgáló cím- vagy aliaskettősi portszáma, például "myDB2server.cloudapp.net:50000" <p><p>**Megjegyzés:** Ez az érték egy TCP/IP-címet vagy aliast jelöl, iPv4 vagy IPv6 formátumban, amelyet kettőspont és TCP/IP-portszám követ. |
| **Adatbázis** | Igen | Az adatbázis neve <p><p>**Megjegyzés:** Ez az érték egy DRDA relációs adatbázisnevet (RDBNAM) jelölő karakterlánc: <p>- Db2 z/OS elfogadja a 16 bájtos karakterláncot, ahol az adatbázis az úgynevezett "IBM DB2 z / OS" helyen. <br>- DB2 i fogad el egy 18 bájtos karakterláncot, ahol az adatbázis az úgynevezett "IBM DB2 for i" relációs adatbázis. <br>- DB2 a LUW elfogadja a 8 bájtos karakterláncot. |
| **Felhasználónév** | Igen | Az adatbázis felhasználóneve <p><p>**Megjegyzés:** Ez az érték egy karakterlánc, amelynek hossza az adott adatbázison alapul: <p><p>- Db2 z/OS elfogadja a 8 bájtos karakterláncot. <br>- DB2 i fogad el egy 10 byte string. <br>- Db2 Linux vagy UNIX elfogadja a 8 bájtos karakterláncot. <br>- Db2 for Windows elfogad egy 30 bájtos karakterláncot. |
| **Jelszó** | Igen | Az adatbázis jelszava |
||||

Példa:

![A felhőalapú adatbázisok csatlakozási adatai](./media/connectors-create-api-db2/create-db2-cloud-connection.png)

<a name="on-premises-connection"></a>

## <a name="connect-to-on-premises-db2"></a>Csatlakozás a helyszíni DB2-hez

A kapcsolat létrehozása előtt már telepítve kell lennie a helyszíni adatátjárónak. Ellenkező esetben nem tudja befejezni a kapcsolat beállítását. Ha az átjáró telepítése, folytassa a kapcsolat részleteinek megadását, majd válassza a **Létrehozás gombot.**

| Tulajdonság | Kötelező | Leírás |
|----------|----------|-------------|
| **Csatlakozás helyszíni átjárón keresztül** | Igen | Akkor érvényes, ha helyszíni kapcsolatot szeretne létesíteni, és megjeleníti a helyszíni kapcsolat tulajdonságait. |
| **Kapcsolat neve** | Igen | A kapcsolat neve, például "MyLogicApp-DB2-connection" | 
| **Kiszolgáló** | Igen | A DB2-kiszolgáló cím- vagy aliaskettősi portszáma, például "myDB2server:50000" <p><p>**Megjegyzés:** Ez az érték egy TCP/IP-címet vagy aliast jelöl, iPv4 vagy IPv6 formátumban, amelyet kettőspont és TCP/IP-portszám követ. |
| **Adatbázis** | Igen | Az adatbázis neve <p><p>**Megjegyzés:** Ez az érték egy DRDA relációs adatbázisnevet (RDBNAM) jelölő karakterlánc: <p>- Db2 z/OS elfogadja a 16 bájtos karakterláncot, ahol az adatbázis az úgynevezett "IBM DB2 z / OS" helyen. <br>- DB2 i fogad el egy 18 bájtos karakterláncot, ahol az adatbázis az úgynevezett "IBM DB2 for i" relációs adatbázis. <br>- DB2 a LUW elfogadja a 8 bájtos karakterláncot. |
| **Hitelesítés** | Igen | A kapcsolat hitelesítési típusa, például "Alapszintű" <p><p>**Megjegyzés:** Válassza ki ezt az értéket a listából, amely tartalmazza az Alapszintű vagy a Windows (Kerberos) elemet. |
| **Felhasználónév** | Igen | Az adatbázis felhasználóneve <p><p>**Megjegyzés:** Ez az érték egy karakterlánc, amelynek hossza az adott adatbázison alapul: <p><p>- Db2 z/OS elfogadja a 8 bájtos karakterláncot. <br>- DB2 i fogad el egy 10 byte string. <br>- Db2 Linux vagy UNIX elfogadja a 8 bájtos karakterláncot. <br>- Db2 for Windows elfogad egy 30 bájtos karakterláncot. |
| **Jelszó** | Igen | Az adatbázis jelszava |
| **Átjáró** | Igen | A telepített helyszíni adatátjáró neve <p><p>**Megjegyzés:** Válassza ki ezt az értéket a listából, amely tartalmazza az Azure-előfizetésés erőforráscsoport on belül telepített összes telepített adatátjárók. |
||||

Példa:

![A helyszíni adatbázisok csatlakozási adatai](./media/connectors-create-api-db2/create-db2-on-premises-connection.png)

### <a name="view-output-tables"></a>Kimeneti táblák megtekintése

A logikai alkalmazás manuális futtatásához a tervező eszköztárán válassza a **Futtatás lehetőséget.** Miután a logikai alkalmazás futása befejeződik, megtekintheti a kimenetet a futtatás.

1. A logikai alkalmazás menüjében válassza **az Áttekintés**lehetőséget.

1. Az **Összegzés csoportBan** **válassza** ki a legutóbbi futtatást, amely a lista első eleme.

   ![Futtatási előzmények megtekintése](./media/connectors-create-api-db2/run-history.png)

1. A **Logikai alkalmazás futtatása**csoportban most már megtekintheti az állapotot, a bemeneteket és a kimeneteket a logikai alkalmazás minden egyes lépéséhez.
Bontsa ki a **Táblák beszedése** műveletet.

   ![Művelet kibontása](./media/connectors-create-api-db2/expand-action-step.png)

1. A bemenetek megtekintéséhez válassza a **Nyers bemenetek megjelenítése**lehetőséget.

1. A kimenetek megtekintéséhez válassza a **Nyers kimenetek megjelenítése**lehetőséget.

   A kimenetek tartalmazzák a táblák listáját.

   ![Kimeneti táblák megtekintése](./media/connectors-create-api-db2/db2-connector-get-tables-outputs.png)

## <a name="get-row"></a>Sor bekerülése

Egy rekord lekéréséhez egy DB2 adatbázistábla, használja a **Get sor** művelet a logikai alkalmazásban. Ez a művelet `SELECT WHERE` egy DB2 `SELECT FROM AREA WHERE AREAID = '99999'`utasítást futtat, például .

1. Ha még soha nem használt DB2-műveleteket a logikai alkalmazásban, tekintse át a [DB2 hozzáadása művelet – Táblák leküldése szakaszlépéseit,](#add-db2-action) de adja hozzá a **Sor leküldéses** műveletet, majd térjen vissza ide a folytatáshoz.

   A Sor **legete** után a következőképpen jelenik meg a példalogikai alkalmazás:

   ![Sorművelet beszereznie](./media/connectors-create-api-db2/db2-get-row-action.png)

1. Adja meg az összes szükséges tulajdonság (*) értékeit. Miután kijelölt egy táblát, a művelet a tábla rekordjaira jellemző tulajdonságokat jeleníti meg.

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Tábla neve** | Igen | A kívánt rekordot tartalmazó tábla, például a példában a "TERÜLET" |
   | **Területazonosító** | Igen | A kívánt rekord azonosítója, például "99999" ebben a példában |
   ||||

   ![Táblázat kijelölése](./media/connectors-create-api-db2/db2-get-row-action-select-table.png)

1. Ha elkészült, a tervező eszköztárán válassza a **Mentés gombot.**

### <a name="view-output-row"></a>Kimeneti sor megtekintése

A logikai alkalmazás manuális futtatásához a tervező eszköztárán válassza a **Futtatás lehetőséget.** Miután a logikai alkalmazás futása befejeződik, megtekintheti a kimenetet a futtatás.

1. A logikai alkalmazás menüjében válassza **az Áttekintés**lehetőséget.

1. Az **Összegzés csoportBan** **válassza** ki a legutóbbi futtatást, amely a lista első eleme.

1. A **Logikai alkalmazás futtatása**csoportban most már megtekintheti az állapotot, a bemeneteket és a kimeneteket a logikai alkalmazás minden egyes lépéséhez.
Bontsa ki a **Sor bekerülése** műveletet.

1. A bemenetek megtekintéséhez válassza a **Nyers bemenetek megjelenítése**lehetőséget.

1. A kimenetek megtekintéséhez válassza a **Nyers kimenetek megjelenítése**lehetőséget.

   A kimenetek tartalmazzák a megadott sort.

   ![Kimeneti sor megtekintése](./media/connectors-create-api-db2/db2-connector-get-row-outputs.png)

## <a name="get-rows"></a>Sorok bekerülése

A DB2 adatbázistábla összes rekordjának lekéréséhez használja a **Sorok beolvasása** műveletet a logikai alkalmazásban. Ez a művelet `SELECT` egy DB2 `SELECT * FROM AREA`utasítást futtat, például .

1. Ha még soha nem használt DB2-műveleteket a logikai alkalmazásban, tekintse át a [DB2 hozzáadása művelet – Táblák leküldése szakaszlépéseit,](#add-db2-action) de adja hozzá a **Sorok leküldéseműveletet,** majd térjen vissza ide a folytatáshoz.

   A Sorok **leadása** művelet hozzáadása után a példalogikai alkalmazás a következőképpen jelenik meg:

   ![Sorok beszerezése művelet](./media/connectors-create-api-db2/db2-get-rows-action.png)

1. Nyissa meg a **Táblanév** listát, majd jelölje ki a kívánt táblát, amely a példában a "TERÜLET" :

   ![Táblázat kijelölése](./media/connectors-create-api-db2/db2-get-rows-action-select-table.png)

1. Ha szűrőt vagy lekérdezést szeretne megadni az eredményekhez, válassza a **Speciális beállítások megjelenítése lehetőséget.**

1. Ha elkészült, a tervező eszköztárán válassza a **Mentés gombot.**

### <a name="view-output-rows"></a>Kimeneti sorok megtekintése

A logikai alkalmazás manuális futtatásához a tervező eszköztárán válassza a **Futtatás lehetőséget.** Miután a logikai alkalmazás futása befejeződik, megtekintheti a kimenetet a futtatás.

1. A logikai alkalmazás menüjében válassza **az Áttekintés**lehetőséget.

1. Az **Összegzés csoportBan** **válassza** ki a legutóbbi futtatást, amely a lista első eleme.

1. A **Logikai alkalmazás futtatása**csoportban most már megtekintheti az állapotot, a bemeneteket és a kimeneteket a logikai alkalmazás minden egyes lépéséhez.
Bontsa ki a **Sorok beszerezése** műveletet.

1. A bemenetek megtekintéséhez válassza a **Nyers bemenetek megjelenítése**lehetőséget.

1. A kimenetek megtekintéséhez válassza a **Nyers kimenetek megjelenítése**lehetőséget.

   A kimenetek tartalmazzák a megadott tábla összes rekordját.

   ![Kimeneti sorok megtekintése](./media/connectors-create-api-db2/db2-connector-get-rows-outputs.png)

## <a name="insert-row"></a>Sor beszúrása

Ha egyetlen rekordot szeretne hozzáadni egy DB2 adatbázistáblához, használja a **Sor beszúrása** műveletet a logikai alkalmazásban. Ez a művelet `INSERT` egy DB2 `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`utasítást futtat, például .

1. Ha még soha nem használt DB2-műveleteket a logikai alkalmazásban, tekintse át a [DB2 hozzáadása művelet – Táblák leküldése szakasz lépéseit,](#add-db2-action) de adja hozzá a **Sor beszúrása** műveletet, majd térjen vissza ide a folytatáshoz.

   A Sor **beszúrása** művelet hozzáadása után a példalogikai alkalmazás a következőképpen jelenik meg:

   ![Sorbeszúrási művelet beszúrása](./media/connectors-create-api-db2/db2-insert-row-action.png)

1. Adja meg az összes szükséges tulajdonság (*) értékeit. Miután kijelölt egy táblát, a művelet a tábla rekordjaira jellemző tulajdonságokat jeleníti meg.

   Ebben a példában a következő tulajdonságok vannak:

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Tábla neve** | Igen | A rekord hozzáadásának táblája, például "AREA" |
   | **Területazonosító** | Igen | A hozzáadni hozandó terület azonosítója, például "99999" |
   | **Terület leírása** | Igen | A hozzáadni hozandó terület leírása, például "Terület: 99999" |
   | **Régió azonosítója** | Igen | A hozzáadni hozandó régió azonosítója, például "102" |
   |||| 

   Példa:

   ![Táblázat kijelölése](./media/connectors-create-api-db2/db2-insert-row-action-select-table.png)

1. Ha elkészült, a tervező eszköztárán válassza a **Mentés gombot.**

### <a name="view-insert-row-outputs"></a>Beszúrási sor kimenetek megtekintése

A logikai alkalmazás manuális futtatásához a tervező eszköztárán válassza a **Futtatás lehetőséget.** Miután a logikai alkalmazás futása befejeződik, megtekintheti a kimenetet a futtatás.

1. A logikai alkalmazás menüjében válassza **az Áttekintés**lehetőséget.

1. Az **Összegzés csoportBan** **válassza** ki a legutóbbi futtatást, amely a lista első eleme.

1. A **Logikai alkalmazás futtatása**csoportban most már megtekintheti az állapotot, a bemeneteket és a kimeneteket a logikai alkalmazás minden egyes lépéséhez.
Bontsa ki a **Sor beszúrása** műveletet.

1. A bemenetek megtekintéséhez válassza a **Nyers bemenetek megjelenítése**lehetőséget.

1. A kimenetek megtekintéséhez válassza a **Nyers kimenetek megjelenítése**lehetőséget.

   A kimenetek tartalmazzák a megadott táblához hozzáadott rekordot.

   ![Kimenet megtekintése beszúrt sorral](./media/connectors-create-api-db2/db2-connector-insert-row-outputs.png)

## <a name="update-row"></a>Sor frissítése

A DB2 adatbázistábla egyetlen rekordjának frissítéséhez használja a Sor **frissítése** műveletet a logikai alkalmazásban. Ez a művelet `UPDATE` egy DB2 `UPDATE AREA SET AREAID = '99999', AREADESC = 'Updated 99999', REGIONID = 102)`utasítást futtat, például .

1. Ha még soha nem használt DB2-műveleteket a logikai alkalmazásban, tekintse át a [DB2 hozzáadása művelet – Táblák leküldése szakasz lépéseit,](#add-db2-action) de adja hozzá helyette a **Sor frissítése** műveletet, majd térjen vissza ide a folytatáshoz.

   A Sor **frissítése** művelet hozzáadása után a példalogikai alkalmazás a következőképpen jelenik meg:

   ![Sorművelet frissítése](./media/connectors-create-api-db2/db2-update-row-action.png)

1. Adja meg az összes szükséges tulajdonság (*) értékeit. Miután kijelölt egy táblát, a művelet a tábla rekordjaira jellemző tulajdonságokat jeleníti meg.

   Ebben a példában a következő tulajdonságok vannak:

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Tábla neve** | Igen | A rekord frissítését jelző tábla, például "AREA" |
   | **Sorazonosító** | Igen | A rekord frissítéséhez azonosító, például "99999" |
   | **Területazonosító** | Igen | Az új területazonosító, például a "99999" |
   | **Terület leírása** | Igen | Az új terület leírása, például "Frissítve: 99999" |
   | **Régió azonosítója** | Igen | Az új régióazonosító, például a "102" |
   ||||

   Példa:

   ![Táblázat kijelölése](./media/connectors-create-api-db2/db2-update-row-action-select-table.png)

1. Ha elkészült, a tervező eszköztárán válassza a **Mentés gombot.**

### <a name="view-update-row-outputs"></a>Frissítési sor kimenetének megtekintése

A logikai alkalmazás manuális futtatásához a tervező eszköztárán válassza a **Futtatás lehetőséget.** Miután a logikai alkalmazás futása befejeződik, megtekintheti a kimenetet a futtatás.

1. A logikai alkalmazás menüjében válassza **az Áttekintés**lehetőséget.

1. Az **Összegzés csoportBan** **válassza** ki a legutóbbi futtatást, amely a lista első eleme.

1. A **Logikai alkalmazás futtatása**csoportban most már megtekintheti az állapotot, a bemeneteket és a kimeneteket a logikai alkalmazás minden egyes lépéséhez.
Bontsa ki a **Sor frissítése** műveletet.

1. A bemenetek megtekintéséhez válassza a **Nyers bemenetek megjelenítése**lehetőséget.

1. A kimenetek megtekintéséhez válassza a **Nyers kimenetek megjelenítése**lehetőséget.

   A kimenetek tartalmazzák a megadott táblában frissített rekordot.

   ![Kimenet megtekintése frissített sorral](./media/connectors-create-api-db2/db2-connector-update-row-outputs.png)

## <a name="delete-row"></a>Sor törlése

Ha egyetlen rekordot szeretne törölni egy DB2-adatbázistáblából, használja a **Sor törlése** műveletet a logikai alkalmazásban. Ez a művelet `DELETE` egy DB2 `DELETE FROM AREA WHERE AREAID = '99999'`utasítást futtat, például .

1. Ha még soha nem használt DB2-műveleteket a logikai alkalmazásban, tekintse át a [DB2 hozzáadása művelet – Táblák leküldése szakasz lépéseit,](#add-db2-action) de adja hozzá helyette a **Sor törlése** műveletet, majd térjen vissza ide a folytatáshoz.

   A Sor **törlése** művelet hozzáadása után a példalogikai alkalmazás a következőképpen jelenik meg:

   ![Sorművelet törlése](./media/connectors-create-api-db2/db2-delete-row-action.png)

1. Adja meg az összes szükséges tulajdonság (*) értékeit. Miután kijelölt egy táblát, a művelet a tábla rekordjaira jellemző tulajdonságokat jeleníti meg.

   Ebben a példában a következő tulajdonságok vannak:

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Tábla neve** | Igen | Az a tábla, amelyből a rekord törleszthető, például "AREA" |
   | **Sorazonosító** | Igen | A törlandó rekord azonosítója, például "99999" |
   ||||

   Példa:

   ![Táblázat kijelölése](./media/connectors-create-api-db2/db2-delete-row-action-select-table.png)

1. Ha elkészült, a tervező eszköztárán válassza a **Mentés gombot.**

### <a name="view-delete-row-outputs"></a>Törlési sorkimenetek megtekintése

A logikai alkalmazás manuális futtatásához a tervező eszköztárán válassza a **Futtatás lehetőséget.** Miután a logikai alkalmazás futása befejeződik, megtekintheti a kimenetet a futtatás.

1. A logikai alkalmazás menüjében válassza **az Áttekintés**lehetőséget.

1. Az **Összegzés csoportBan** **válassza** ki a legutóbbi futtatást, amely a lista első eleme.

1. A **Logikai alkalmazás futtatása**csoportban most már megtekintheti az állapotot, a bemeneteket és a kimeneteket a logikai alkalmazás minden egyes lépéséhez.
Bontsa ki a **Sor törlése** műveletet.

1. A bemenetek megtekintéséhez válassza a **Nyers bemenetek megjelenítése**lehetőséget.

1. A kimenetek megtekintéséhez válassza a **Nyers kimenetek megjelenítése**lehetőséget.

   A kimenetek már nem tartalmazzák a megadott táblából törölt rekordot.

   ![Kimenet megtekintése törölt sor nélkül](./media/connectors-create-api-db2/db2-connector-delete-row-outputs.png)

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötővel kapcsolatos további technikai részleteket, például az eseményindítókat, műveleteket és korlátokat az összekötő Swagger-fájlja szerint lásd az [összekötő referencialapján.](https://docs.microsoft.com/connectors/db2/)

> [!NOTE]
> [Az integrációs szolgáltatási környezetben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)lévő logikai alkalmazások esetében az összekötő ISE-címkével ellátott verziója az [ISE-üzenetkorlátokat](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) használja.

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)
