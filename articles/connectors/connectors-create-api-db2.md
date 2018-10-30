---
title: Csatlakozhat az IBM DB2-höz – Azure Logic Apps |} A Microsoft Docs
description: Az IBM DB2 REST API-k és az Azure Logic Apps-erőforrások kezelése
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: plarsen, LADocs
ms.suite: integration
ms.topic: article
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: 9ba2476ccf2601f5d7d2c0e93c7661f740f32145
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50231763"
---
# <a name="manage-ibm-db2-resources-with-azure-logic-apps"></a>Az Azure Logic Apps IBM DB2-erőforrások kezelése

Az Azure Logic Apps és az IBM DB2-összekötő automatikus feladatokkal és munkafolyamatokkal, a DB2-adatbázisban tárolt erőforrások alapján is létrehozhat. A munkafolyamatok is csatlakozni az erőforrásokat az adatbázisban, olvassa el és az adatbázis-táblák listázása, sorok hozzáadása, sorok, törölje a sorokat és egyebek módosításához. A logic Apps választ kaphat az adatbázisból, és az egyéb műveletek elérhetővé a kimeneti műveleteket is felvehet. 

Ez a cikk bemutatja, hogyan hozhat létre egy logikai alkalmazást, amely a különböző adatbázis-műveleteket hajt végre. Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="supported-platforms-and-versions"></a>Támogatott platformok és verziók

A DB2-összekötő tartalmazza a Microsoft, amely a TCP/IP-hálózaton keresztül távoli DB2-kiszolgálókkal kommunikál. Felhőalapú adatbázisok, például az IBM Bluemix dashDB vagy az IBM DB2-höz, a virtualizálás az Azure-ban futó Windows eléréséhez ezt az összekötőt is használhatja. Miután is elérheti a helyszíni DB2-adatbázisok [telepítése és beállítása a helyszíni adatátjáró](../logic-apps/logic-apps-gateway-connection.md). 

Az IBM DB2-összekötő támogatja ezeket a IBM DB2-platformok és verziók IBM DB2-höz kompatibilis termékek, például az IBM Bluemix dashDB, elosztott relációs adatbázis architektúra (DRDA) SQL Access Manager (SQLAM) verziók 10-es és 11 támogató együtt:

| Platform | Verzió | 
|----------|---------|
| Z/os IBM DB2-höz | 11.1, 10.1 |
| Az IBM DB2-höz i | 7.3, 7.2, 7.1 |
| IBM DB2-höz tartozó LUW | 11, 10,5 |
|||

## <a name="supported-database-operations"></a>Támogatott adatbázis-műveletek

Az IBM DB2-összekötő ezen adatbázis műveleteket, amelyeket a megfelelő műveleteket az összekötőben leképezése támogatja:

| Adatbázis-művelet | Összekötő-művelet | 
|--------------------|------------------|
| Lista adatbázistáblák | Táblák beolvasása | 
| SELECT használatával egy sor olvasása | Sor beolvasása | 
| VÁLASSZA a minden sorok olvasása | Sorok beolvasása | 
| Adjon hozzá egy sort INSERT használatával | Sor beszúrása | 
| FRISSÍTÉS használatával egy sor szerkesztése | Sor frissítése | 
| Távolítsa el az egyik sor a DELETE használata | Sor törlése | 
|||

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. 

* Az IBM DB2-adatbázis, vagy felhőalapú vagy helyszíni

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazás, ahol szeretné a DB2-adatbázis eléréséhez. Ez az összekötő biztosít csak műveletek, így a logikai alkalmazás indítására jelöljön ki egy külön eseményindítót, például a **ismétlődési** eseményindító.
A példák a jelen cikk használja a **ismétlődési** eseményindító.

<a name="add-db2-action"></a>

## <a name="add-db2-action---get-tables"></a>Adja hozzá a DB2 művelet – Get-táblák

1. Az a [az Azure portal](https://portal.azure.com), nyissa meg a logikai alkalmazás a Logic App Designerben, ha nincs már nyissa meg.

1. Az eseményindító területén válassza a **új lépés**.

1. A Keresés mezőbe írja be szűrőként "db2". Ebben a példában műveletek listája alatt válassza a következő műveletet: **(előzetes verzió) táblák lekérése**
   
   ![Művelet kiválasztása](./media/connectors-create-api-db2/select-db2-action.png)

   Most adja meg a DB2-adatbázis kapcsolati adatait kéri. 

1. Kövesse a kapcsolatok létrehozásának lépései [felhőalapú adatbázisok](#cloud-connection) vagy [a helyszíni adatbázisok](#on-premises-connection).

<a name="cloud-connection"></a>

## <a name="connect-to-cloud-db2"></a>Csatlakozás felhőalapú DB2

A kapcsolat beállításához, adja meg az alábbi kapcsolati adatokat, amikor a rendszer kéri, válassza a **létrehozás**, majd mentse a logikai alkalmazás:

| Tulajdonság | Szükséges | Leírás | 
|----------|----------|-------------| 
| **Csatlakozás helyszíni átjárón keresztül** | Nem | Csak a helyi kapcsolatok vonatkozik. | 
| **Kapcsolat neve** | Igen | A hálózati kapcsolatot, például "MyLogicApp – DB2-kapcsolat" nevét |
| **Kiszolgáló** | Igen | A cím vagy alias kettőspont port számát a DB2-kiszolgáló, például a "myDB2server.cloudapp.net:50000" <p><p>**Megjegyzés:**: Ez az érték egy TCP/IP-címet jelölő vagy alias, vagy IPv4- vagy IPv6 formátumban, majd egy kettőspontot és a egy TCP/IP-port száma. |
| **Adatbázis** | Igen | Az adatbázis neve <p><p>**Megjegyzés:**: Ez az érték a DRDA relációs adatbázis nevét (RDBNAM) jelölő: <p>-DB2 z/os fogad egy 16 bájtos karakterláncot, ahol az adatbázis egy "A – z/os IBM DB2-höz" hely nevezik. <br>-DB2 i-18-többbájtos karakterlánc, ahol az adatbázis nevezik, fogadja el a egy "az IBM DB2-höz i" relációs adatbázis. <br>-DB2 LUW számára egy 8 bájtos-karakterláncot fogad el. |
| **Felhasználónév** | Igen | A felhasználónév, az adatbázis <p><p>**Megjegyzés:**: Ez az érték egy karakterláncot, amelynek hossza alapján az adott adatbázis: <p><p>-DB2 z/os egy 8 bájtos-karakterláncot fogad el. <br>-DB2 i fogad egy 10 – többbájtos karakterlánc esetében. <br>-A Linux vagy UNIX DB2 egy 8 bájtos-karakterláncot fogad el. <br>-DB2 a Windows-30 bájtos-karakterláncot fogad el. | 
| **Jelszó** | Igen | A Database jelszavát | 
|||| 

Példa:

![Felhőalapú adatbázisok esetében a kapcsolat részletei](./media/connectors-create-api-db2/create-db2-cloud-connection.png)

<a name="on-premises-connection"></a>

## <a name="connect-to-on-premises-db2"></a>Csatlakozhat a helyszíni DB2-höz

A kapcsolat létrehozása, előtt már rendelkeznie kell a telepített helyszíni adatátjáróval. Ellenkező esetben a beállítás nem tudta befejezni a kapcsolatot. Ha az átjáró telepítése, folytassa az alábbi kapcsolati adatokat biztosít, és válassza **létrehozás**.

| Tulajdonság | Szükséges | Leírás | 
|----------|----------|-------------| 
| **Csatlakozás helyszíni átjárón keresztül** | Igen | Érvényes, ha azt szeretné, hogy egy helyszíni kapcsolat, és bemutatja a helyi kapcsolat tulajdonságai. | 
| **Kapcsolat neve** | Igen | A hálózati kapcsolatot, például "MyLogicApp – DB2-kapcsolat" nevét | 
| **Kiszolgáló** | Igen | A cím vagy alias kettőspont port számát a DB2-kiszolgáló, például a "myDB2server:50000" <p><p>**Megjegyzés:**: Ez az érték egy TCP/IP-címet jelölő vagy alias, vagy IPv4- vagy IPv6 formátumban, majd egy kettőspontot és a egy TCP/IP-port száma. | 
| **Adatbázis** | Igen | Az adatbázis neve <p><p>**Megjegyzés:**: Ez az érték a DRDA relációs adatbázis nevét (RDBNAM) jelölő: <p>-DB2 z/os fogad egy 16 bájtos karakterláncot, ahol az adatbázis egy "A – z/os IBM DB2-höz" hely nevezik. <br>-DB2 i-18-többbájtos karakterlánc, ahol az adatbázis nevezik, fogadja el a egy "az IBM DB2-höz i" relációs adatbázis. <br>-DB2 LUW számára egy 8 bájtos-karakterláncot fogad el. | 
| **Hitelesítés** | Igen | A hálózati kapcsolatot, például "Alapszintű" hitelesítési típusát <p><p>**Megjegyzés:**: válassza ezt az értéket a listából, amely tartalmazza az alapszintű vagy Windows (Kerberos). | 
| **Felhasználónév** | Igen | A felhasználónév, az adatbázis <p><p>**Megjegyzés:**: Ez az érték egy karakterláncot, amelynek hossza alapján az adott adatbázis: <p><p>-DB2 z/os egy 8 bájtos-karakterláncot fogad el. <br>-DB2 i fogad egy 10 – többbájtos karakterlánc esetében. <br>-A Linux vagy UNIX DB2 egy 8 bájtos-karakterláncot fogad el. <br>-DB2 a Windows-30 bájtos-karakterláncot fogad el. | 
| **Jelszó** | Igen | A Database jelszavát | 
| **Átjáró** | Igen | A telepített helyszíni adatátjáró neve <p><p>**Megjegyzés:**: válassza ezt az értéket a listából, amely tartalmazza az összes telepített átjárók az Azure-előfizetésben és erőforráscsoportban csoporton belül. | 
|||| 

Példa:

![A helyszíni adatbázisok esetében a kapcsolat részletei](./media/connectors-create-api-db2/create-db2-on-premises-connection.png)

### <a name="view-output-tables"></a>Kimeneti táblák megtekintése

A logikai alkalmazás manuálisan futtatásához a Tervező eszköztárán válassza **futtatása**. A logikai alkalmazás futtatásának befejeződése után megtekintheti a Futtatás kimenete.

1. A logikai alkalmazás menüjében válassza **áttekintése**. 

1. A **összefoglalás**, a a **futtatási előzmények** területen válassza ki a legutóbbi futtatás, amely az első elem a listában. 

   ![Futtatási előzmények megtekintése](./media/connectors-create-api-db2/run-history.png)

1. A **a logikai alkalmazás futtatásának**, most megtekintheti az állapotot, bemenetei, és az egyes kimenetek. lépés: a logikai alkalmazásban. Bontsa ki a **táblák lekérése** művelet.

   ![Bontsa ki a művelet](./media/connectors-create-api-db2/expand-action-step.png)

1. A bemeneti adatok megtekintéséhez válassza **nyers bemenetek megjelenítése**. 

1. A kimenetek megtekintéséhez válassza **nyers kimenetek megjelenítése**. 

   A kimenetek közé tartozik a táblák listáját. 
   
   ![Kimeneti táblák megtekintése](./media/connectors-create-api-db2/db2-connector-get-tables-outputs.png)

## <a name="get-row"></a>Sor beolvasása

Beolvasása egy DB2-adatbázis tábla több rekordot, használja a **sor beolvasása** művelet a logikai alkalmazásban. Ez a művelet futtat egy DB2 `SELECT WHERE` utasítással, például `SELECT FROM AREA WHERE AREAID = '99999'`.

1. Ha korábban már DB2 műveletek előtt soha nem használta a logikai alkalmazásban, tekintse át a lépések a [DB2 hozzáadása művelet – Get táblák](#add-db2-action) szakaszt, de a hozzá a **sor beolvasása** művelet ehelyett és követően térjen vissza ide a folytatáshoz. 

   Miután hozzáadta a **sor beolvasása** művelet, hogyan jelenik meg a példa logikai alkalmazás a következő:

   ![Első sor művelet](./media/connectors-create-api-db2/db2-get-row-action.png)

1. Adja meg az összes szükséges tulajdonság (*) értékeket. Miután kiválasztotta a tábla, a művelet a releváns tulajdonságok, konkrétan az adott tábla rekordjait a jeleníti meg.

   | Tulajdonság | Szükséges | Leírás | 
   |----------|----------|-------------| 
   | **Tábla neve** | Igen | A tábla, amely rendelkezik a rekordot szeretne, például az ebben a példában a "terület" | 
   | **Terület azonosítója** | Igen | A rekord azonosítója szeretne, például az ebben a példában a "99999" | 
   |||| 

   ![Tábla kiválasztása](./media/connectors-create-api-db2/db2-get-row-action-select-table.png)

1. Ha elkészült, a Tervező eszköztárán válassza a **mentése**. 

### <a name="view-output-row"></a>Nézet kimeneti sor

A logikai alkalmazás manuálisan futtatásához a Tervező eszköztárán válassza **futtatása**. A logikai alkalmazás futtatásának befejeződése után megtekintheti a Futtatás kimenete.

1. A logikai alkalmazás menüjében válassza **áttekintése**. 

1. A **összefoglalás**, a a **futtatási előzmények** területen válassza ki a legutóbbi futtatás, amely az első elem a listában. 

1. A **a logikai alkalmazás futtatásának**, most megtekintheti az állapotot, bemenetei, és az egyes kimenetek. lépés: a logikai alkalmazásban. Bontsa ki a **sor beolvasása** művelet.

1. A bemeneti adatok megtekintéséhez válassza **nyers bemenetek megjelenítése**. 

1. A kimenetek megtekintéséhez válassza **nyers kimenetek megjelenítése**. 

   A kimenetek közé tartozik a megadott sort. 
   
   ![Nézet kimeneti sor](./media/connectors-create-api-db2/db2-connector-get-row-outputs.png)

## <a name="get-rows"></a>Sorok beolvasása

Beolvasása egy DB2-adatbázis tábla összes rekordot, használja a **sorok beolvasása** művelet a logikai alkalmazásban. Ez a művelet futtat egy DB2 `SELECT` utasítással, például `SELECT * FROM AREA`.

1. Ha korábban már DB2 műveletek előtt soha nem használta a logikai alkalmazásban, tekintse át a lépések a [DB2 hozzáadása művelet – Get táblák](#add-db2-action) szakaszt, de a hozzá a **sorok beolvasása** művelet ehelyett és követően térjen vissza ide a folytatáshoz. 

   Miután hozzáadta a **sorok beolvasása** művelet, hogyan jelenik meg a példa logikai alkalmazás a következő:

   ![Első sorok művelet](./media/connectors-create-api-db2/db2-get-rows-action.png)

1. Nyissa meg a **táblanév** listában, és válassza ki a kívánt, táblát, amely "Terület" Ebben a példában: 

   ![Tábla kiválasztása](./media/connectors-create-api-db2/db2-get-rows-action-select-table.png)

1. Adjon meg egy szűrő vagy a lekérdezési eredményeket, válassza a **speciális beállítások megjelenítése**.

1. Ha elkészült, a Tervező eszköztárán válassza a **mentése**. 

### <a name="view-output-rows"></a>Nézet kimeneti sorok

A logikai alkalmazás manuálisan futtatásához a Tervező eszköztárán válassza **futtatása**. A logikai alkalmazás futtatásának befejeződése után megtekintheti a Futtatás kimenete.

1. A logikai alkalmazás menüjében válassza **áttekintése**. 

1. A **összefoglalás**, a a **futtatási előzmények** területen válassza ki a legutóbbi futtatás, amely az első elem a listában. 

1. A **a logikai alkalmazás futtatásának**, most megtekintheti az állapotot, bemenetei, és az egyes kimenetek. lépés: a logikai alkalmazásban. Bontsa ki a **sorok beolvasása** művelet.

1. A bemeneti adatok megtekintéséhez válassza **nyers bemenetek megjelenítése**. 

1. A kimenetek megtekintéséhez válassza **nyers kimenetek megjelenítése**. 

   A kimenetek a megadott tábla összes rekordját tartalmazza.
   
   ![Nézet kimeneti sorok](./media/connectors-create-api-db2/db2-connector-get-rows-outputs.png)

## <a name="insert-row"></a>Sor beszúrása

Egyetlen rekordot ad hozzá egy DB2-adatbázis táblában, használja a **sor beszúrása** művelet a logikai alkalmazásban. Ez a művelet futtat egy DB2 `INSERT` utasítással, például `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

1. Ha korábban már DB2 műveletek előtt soha nem használta a logikai alkalmazásban, tekintse át a lépések a [DB2 hozzáadása művelet – Get táblák](#add-db2-action) szakaszt, de a hozzá a **sor beszúrása** művelet ehelyett és követően térjen vissza ide a folytatáshoz. 

   Miután hozzáadta a **sor beszúrása** művelet, hogyan jelenik meg a példa logikai alkalmazás a következő:

   ![Sor művelet beszúrása](./media/connectors-create-api-db2/db2-insert-row-action.png)

1. Adja meg az összes szükséges tulajdonság (*) értékeket. Miután kiválasztotta a tábla, a művelet a releváns tulajdonságok, konkrétan az adott tábla rekordjait a jeleníti meg. 

   Ebben a példában az alábbiakban a tulajdonságai:

   | Tulajdonság | Szükséges | Leírás | 
   |----------|----------|-------------| 
   | **Tábla neve** | Igen | A tábla hozzáadása a rekordot, például "Terület" where | 
   | **Terület azonosítója** | Igen | A terület szeretne hozzáadni, például a "99999" azonosítója | 
   | **Terület leírása** | Igen | A terület szeretne hozzáadni, például a "99999 közötti terület" leírása | 
   | **Régió azonosítója** | Igen | A régióban szeretne hozzáadni, például a "102" azonosítója | 
   |||| 

   Példa:

   ![Tábla kiválasztása](./media/connectors-create-api-db2/db2-insert-row-action-select-table.png)

1. Ha elkészült, a Tervező eszköztárán válassza a **mentése**. 

### <a name="view-insert-row-outputs"></a>Nézet kimeneti sor beszúrása

A logikai alkalmazás manuálisan futtatásához a Tervező eszköztárán válassza **futtatása**. A logikai alkalmazás futtatásának befejeződése után megtekintheti a Futtatás kimenete.

1. A logikai alkalmazás menüjében válassza **áttekintése**. 

1. A **összefoglalás**, a a **futtatási előzmények** területen válassza ki a legutóbbi futtatás, amely az első elem a listában. 

1. A **a logikai alkalmazás futtatásának**, most megtekintheti az állapotot, bemenetei, és az egyes kimenetek. lépés: a logikai alkalmazásban. Bontsa ki a **sor beszúrása** művelet.

1. A bemeneti adatok megtekintéséhez válassza **nyers bemenetek megjelenítése**. 

1. A kimenetek megtekintéséhez válassza **nyers kimenetek megjelenítése**. 

   A kimenetek közé tartozik a megadott táblázathoz hozzáadott rekord.
   
   ![Beszúrt sor kimenet megtekintése](./media/connectors-create-api-db2/db2-connector-insert-row-outputs.png)

## <a name="update-row"></a>Sor frissítése

Egyetlen rekordot egy DB2-adatbázis tábla frissítéséhez használja a **sor frissítése** művelet a logikai alkalmazásban. Ez a művelet futtat egy DB2 `UPDATE` utasítással, például `UPDATE AREA SET AREAID = '99999', AREADESC = 'Updated 99999', REGIONID = 102)`.

1. Ha korábban már DB2 műveletek előtt soha nem használta a logikai alkalmazásban, tekintse át a lépések a [DB2 hozzáadása művelet – Get táblák](#add-db2-action) szakaszt, de a hozzá a **sor frissítése** művelet ehelyett és követően térjen vissza ide a folytatáshoz. 

   Miután hozzáadta a **sor frissítése** művelet, hogyan jelenik meg a példa logikai alkalmazás a következő:

   ![Sor művelet frissítése](./media/connectors-create-api-db2/db2-update-row-action.png)

1. Adja meg az összes szükséges tulajdonság (*) értékeket. Miután kiválasztotta a tábla, a művelet a releváns tulajdonságok, konkrétan az adott tábla rekordjait a jeleníti meg. 

   Ebben a példában az alábbiakban a tulajdonságai:

   | Tulajdonság | Szükséges | Leírás | 
   |----------|----------|-------------| 
   | **Tábla neve** | Igen | A tábla hol frissítse a rekordot, például "Terület" | 
   | **Sorazonosító** | Igen | Például a "99999" frissítéséhez a rekord azonosítója | 
   | **Terület azonosítója** | Igen | Az új terület azonosítója, például a "99999" | 
   | **Terület leírása** | Igen | Új terület leírása, például a "99999 frissítve" | 
   | **Régió azonosítója** | Igen | Új régió azonosító, például a "102" | 
   |||| 

   Példa:

   ![Tábla kiválasztása](./media/connectors-create-api-db2/db2-update-row-action-select-table.png)

1. Ha elkészült, a Tervező eszköztárán válassza a **mentése**. 

### <a name="view-update-row-outputs"></a>Megjeleníti a sor a nézet frissítése

A logikai alkalmazás manuálisan futtatásához a Tervező eszköztárán válassza **futtatása**. A logikai alkalmazás futtatásának befejeződése után megtekintheti a Futtatás kimenete.

1. A logikai alkalmazás menüjében válassza **áttekintése**. 

1. A **összefoglalás**, a a **futtatási előzmények** területen válassza ki a legutóbbi futtatás, amely az első elem a listában. 

1. A **a logikai alkalmazás futtatásának**, most megtekintheti az állapotot, bemenetei, és az egyes kimenetek. lépés: a logikai alkalmazásban. Bontsa ki a **sor frissítése** művelet.

1. A bemeneti adatok megtekintéséhez válassza **nyers bemenetek megjelenítése**. 

1. A kimenetek megtekintéséhez válassza **nyers kimenetek megjelenítése**. 

   A kimenetek közé tartozik a megadott táblában frissített rekord.
   
   ![A frissített sort nézet kimeneti](./media/connectors-create-api-db2/db2-connector-update-row-outputs.png)

## <a name="delete-row"></a>Sor törlése

Egyetlen rekord törlése egy DB2-adatbázis táblából, használja a **sor törlése** művelet a logikai alkalmazásban. Ez a művelet futtat egy DB2 `DELETE` utasítással, például `DELETE FROM AREA WHERE AREAID = '99999'`.

1. Ha korábban már DB2 műveletek előtt soha nem használta a logikai alkalmazásban, tekintse át a lépések a [DB2 hozzáadása művelet – Get táblák](#add-db2-action) szakaszt, de a hozzá a **sor törlése** művelet ehelyett és követően térjen vissza ide a folytatáshoz. 

   Miután hozzáadta a **sor törlése** művelet, hogyan jelenik meg a példa logikai alkalmazás a következő:

   ![Sor művelet törlése](./media/connectors-create-api-db2/db2-delete-row-action.png)

1. Adja meg az összes szükséges tulajdonság (*) értékeket. Miután kiválasztotta a tábla, a művelet a releváns tulajdonságok, konkrétan az adott tábla rekordjait a jeleníti meg. 

   Ebben a példában az alábbiakban a tulajdonságai:

   | Tulajdonság | Szükséges | Leírás | 
   |----------|----------|-------------| 
   | **Tábla neve** | Igen | A táblázat helyét, például a "Terület" a-rekord törlése | 
   | **Sorazonosító** | Igen | Szeretné törölni, például a "99999" a rekord azonosítója | 
   |||| 

   Példa:

   ![Tábla kiválasztása](./media/connectors-create-api-db2/db2-delete-row-action-select-table.png)

1. Ha elkészült, a Tervező eszköztárán válassza a **mentése**. 

### <a name="view-delete-row-outputs"></a>Nézet törlése sor kimenetek

A logikai alkalmazás manuálisan futtatásához a Tervező eszköztárán válassza **futtatása**. A logikai alkalmazás futtatásának befejeződése után megtekintheti a Futtatás kimenete.

1. A logikai alkalmazás menüjében válassza **áttekintése**. 

1. A **összefoglalás**, a a **futtatási előzmények** területen válassza ki a legutóbbi futtatás, amely az első elem a listában. 

1. A **a logikai alkalmazás futtatásának**, most megtekintheti az állapotot, bemenetei, és az egyes kimenetek. lépés: a logikai alkalmazásban. Bontsa ki a **sor törlése** művelet.

1. A bemeneti adatok megtekintéséhez válassza **nyers bemenetek megjelenítése**. 

1. A kimenetek megtekintéséhez válassza **nyers kimenetek megjelenítése**. 

   A kimenetek a továbbiakban nem tartalmazza a megadott tábla törölte a rekordot.
   
   ![Nézet kimeneti törölt sor nélkül](./media/connectors-create-api-db2/db2-connector-delete-row-outputs.png)

## <a name="connector-reference"></a>Összekötő-referencia

További technikai részleteket, például a triggereket, műveletek és -korlátok, az összekötő Swagger-fájl által leírt: a [összekötő referencialapja](/connectors/db2/). 

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)
