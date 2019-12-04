---
title: Kapcsolódás az IBM Informix-adatbázishoz
description: Erőforrások kezelése az IBM Informix REST API-kkal és Azure Logic Apps
services: logic-apps
ms.suite: integration
author: gplarsen
ms.author: plarsen
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/26/2016
tags: connectors
ms.openlocfilehash: d6f768bc76d19c0aa21a245c008a4b05588f8f43
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789733"
---
# <a name="get-started-with-the-informix-connector"></a>Ismerkedés az Informix-összekötővel
Az Informix-hez készült Microsoft Connector összekapcsolja Logic Apps az IBM Informix-adatbázisban tárolt erőforrásokhoz. Az Informix-összekötő tartalmaz egy Microsoft-ügyfelet, amely TCP/IP-hálózaton keresztül kommunikál a távoli Informix-kiszolgáló számítógépekkel. Ez magában foglalja a felhőalapú adatbázisokat, például az IBM Informix for Windows rendszert az Azure virtualizációs szolgáltatásban, valamint a helyszíni adatátjárót használó helyszíni adatbázisokat. Tekintse meg az IBM Informix platformok és verziók [támogatott listáját](connectors-create-api-informix.md#supported-informix-platforms-and-versions) (ebben a témakörben).

Az összekötő a következő adatbázis-műveleteket támogatja:

* Adatbázis-táblák listázása
* Egy sor beolvasása a SELECT használatával
* Az összes sor olvasása a SELECT paranccsal
* Egy sor hozzáadása a BESZÚRÁSsal
* Egy sor módosítása a FRISSÍTÉSsel
* Egy sor eltávolítása a DELETE használatával

Ebből a témakörből megtudhatja, hogyan használhatja az összekötőt egy logikai alkalmazásban az adatbázis-műveletek feldolgozásához.

További információ a Logic Appsről: [logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="available-actions"></a>Elérhető műveletek
Ez az összekötő a következő Logic app-műveleteket támogatja:

* Getables
* GetRow
* GetRows
* InsertRow
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>Táblák listázása
A logikai alkalmazások bármilyen művelethez való létrehozása a Microsoft Azure Portalon végrehajtott számos lépésből áll.

A logikai alkalmazásban hozzáadhat egy műveletet egy Informix-adatbázisban lévő táblák listázásához. Ez a művelet arra utasítja az összekötőt, hogy dolgozza fel az Informix Schema utasítást, például `CALL SYSIBM.SQLTABLES`.

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása
1. Az Azure-beli **kezdőképernyőn**válassza a **+** (plusz aláírás), **web és mobil**, majd a **logikai alkalmazás**lehetőséget.
2. Adja meg a **nevet**, például `InformixgetTables`, **előfizetés**, **erőforráscsoport**, **hely**és **app Service terv**. Válassza **a rögzítés az irányítópulton**lehetőséget, majd válassza a **Létrehozás**lehetőséget.

### <a name="add-a-trigger-and-action"></a>Trigger és művelet hozzáadása
1. A **Logic apps Designerben**válassza az **üres LogicApp** elemet a **sablonok** listájában.
2. Az **Eseményindítók** listában válassza az **Ismétlődés**lehetőséget. 
3. Az **ismétlődési** Eseményindítóban válassza a **Szerkesztés**lehetőséget, válassza a **gyakoriság** legördülő menüből a **nap**lehetőséget, majd válassza az **intervallum** elemet a **7-es**típushoz.  
4. Válassza az **+ új lépés** mezőt, majd válassza a **művelet hozzáadása**lehetőséget.
5. A **műveletek** listán írja be az **Informix** kifejezést a **További műveletek keresése** szövegmezőbe, majd válassza az **Informix-Get Tables (előzetes verzió)** lehetőséget.
   
   ![](./media/connectors-create-api-informix/InformixconnectorActions.png)  
6. Az **Informix-Get Tables** konfigurációs ablaktáblán jelölje be a **jelölőnégyzetet** a helyszíni **adatátjárón keresztüli kapcsolódás**engedélyezéséhez. Figyelje meg, hogy a beállítások a felhőből a helyszíni környezetbe változnak.
   
   * Adja meg a **kiszolgáló**értékét a név vagy az alias kettőspont portszáma formájában. Írja be például a következőt: `ibmserver01:9089`.
   * Adja meg az **adatbázis**értékét. Írja be például a következőt: `nwind`.
   * Válassza az érték lehetőséget a **hitelesítéshez**. Válassza például az **alapszintű**lehetőséget.
   * Adja meg a **username**értéket. Írja be például a következőt: `informix`.
   * Adja meg a **jelszó**értékét. Írja be például a következőt: `Password1`.
   * Válassza ki az **átjáró**értékét. Válassza például a **datagateway01**lehetőséget.
7. Válassza a **Létrehozás**, majd a **Mentés**lehetőséget. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)
8. Az **InformixgetTables** panelen az **Összefoglalás**alatt található **minden Futtatás** listában válassza ki az elsőként felsorolt elemet (a legutóbbi futtatást).
9. A **logikai alkalmazás futtatása** panelen válassza a **Futtatás részletek**lehetőséget. A **művelet** listán válassza a **Get_tables**lehetőséget. Tekintse meg az **állapot**értéket, amelynek **sikeresnek**kell lennie. A bemenetek megtekintéséhez válassza a **bemenetek hivatkozást** . Válassza ki a **kimenetek hivatkozást**, és tekintse meg a kimeneteket; tartalmaznia kell a táblák listáját.
   
   ![](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>A kapcsolatok létrehozása
Ez az összekötő támogatja a helyszíni és a Felhőbeli adatbázisokhoz való kapcsolódást a következő kapcsolati tulajdonságok használatával. 

| Tulajdonság | Leírás |
| --- | --- |
| kiszolgáló |Kötelező. Egy TCP/IP-címet vagy aliast jelölő karakterlánc-értéket fogad el IPv4-vagy IPv6-formátumban, amelyet egy TCP/IP-portszám követ (kettősponttal tagolt). |
| adatbázis |Kötelező. Egy DRDA-viszonyítási adatbázis nevét (RDBNAM) jelölő karakterlánc-értéket fogad el. Az Informix egy 128 bájtos karakterláncot fogad el (az adatbázist IBM Informix-adatbázis neve (dbname)). |
| hitelesítés |Választható. Az alapszintű vagy a Windows (Kerberos) listaelem értékének elfogadása. |
| felhasználónév |Kötelező. Karakterlánc-értéket fogad el. |
| jelszó |Kötelező. Karakterlánc-értéket fogad el. |
| Átjáró |Kötelező. Elfogad egy listaelem-értéket, amely a tárolási csoporton belül Logic Apps számára meghatározott helyszíni adatátjárót jelöli. |

## <a name="create-the-on-premises-gateway-connection"></a>Helyszíni átjáró-kapcsolatok létrehozása
Ez az összekötő egy helyszíni Informix-adatbázist tud elérni a helyszíni adatátjáró használatával. További információt az átjáróval foglalkozó témakörökben talál. 

1. Az **átjárók** konfigurálása panelen válassza a **jelölőnégyzetet** a **Kapcsolódás átjárón keresztüli**engedélyezéséhez. Tekintse át a beállításokat a felhőből a helyszíni környezetbe.
2. Adja meg a **kiszolgáló**értékét a név vagy az alias kettőspont portszáma formájában. Írja be például a következőt: `ibmserver01:9089`.
3. Adja meg az **adatbázis**értékét. Írja be például a következőt: `nwind`.
4. Válassza az érték lehetőséget a **hitelesítéshez**. Válassza például az **alapszintű**lehetőséget.
5. Adja meg a **username**értéket. Írja be például a következőt: `informix`.
6. Adja meg a **jelszó**értékét. Írja be például a következőt: `Password1`.
7. Válassza ki az **átjáró**értékét. Válassza például a **datagateway01**lehetőséget.
8. A folytatáshoz válassza a **Létrehozás** lehetőséget. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>A felhőalapú kapcsolatok létrehozása
Ez az összekötő elérheti a Cloud Informix-adatbázist. 

1. Az **átjárók** konfigurációja ablaktáblán hagyja letiltva a (nem kattintott) **jelölőnégyzetet** az **átjárón keresztül**. 
2. Adja meg a **kapcsolattípus**értékét. Írja be például a következőt: `hisdemo2`.
3. Adja meg az **Informix-kiszolgáló nevének**értékét a név vagy az alias kettőspont portszáma formájában. Írja be például a következőt: `hisdemo2.cloudapp.net:9089`.
4. Adja meg az **Informix-adatbázis nevének**értékét. Írja be például a következőt: `nwind`.
5. Adja meg a **username**értéket. Írja be például a következőt: `informix`.
6. Adja meg a **jelszó**értékét. Írja be például a következőt: `Password1`.
7. A folytatáshoz válassza a **Létrehozás** lehetőséget. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>Az összes sor beolvasása a SELECT paranccsal
Létrehozhat egy logikai alkalmazási műveletet, amely az Informix-tábla összes sorát beolvassa. Ez a művelet arra utasítja az összekötőt, hogy dolgozza fel az Informix SELECT utasítást, például `SELECT * FROM AREA`.

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása
1. Az Azure-beli **kezdőképernyőn**válassza a **+** (plusz aláírás), **web és mobil**, majd a **logikai alkalmazás**lehetőséget.
2. Adja meg a **nevet** (például "**InformixgetRows**"), **előfizetést**, **erőforráscsoportot**, **helyet**és **app Service csomagot**. Válassza **a rögzítés az irányítópulton**lehetőséget, majd válassza a **Létrehozás**lehetőséget.

### <a name="add-a-trigger-and-action"></a>Trigger és művelet hozzáadása
1. A **Logic apps Designerben**válassza az **üres LogicApp** elemet a **sablonok** listájában.
2. Az **Eseményindítók** listában válassza az **Ismétlődés**lehetőséget. 
3. Az **ismétlődési** Eseményindítóban válassza a **Szerkesztés**lehetőséget, válassza a **gyakoriság** legördülő menüből a **nap**lehetőséget, majd válassza az **intervallum** elemet a **7-es**típushoz. 
4. Válassza az **+ új lépés** mezőt, majd válassza a **művelet hozzáadása**lehetőséget.
5. A **műveletek** listán írja be az **Informix** kifejezést a **További műveletek keresése** szövegmezőbe, majd válassza az **Informix-sorok beolvasása (előzetes verzió)** lehetőséget.
6. A **sorok beolvasása (előzetes verzió)** műveletnél válassza a **kapcsolat módosítása**lehetőséget.
7. A **kapcsolatok** konfiguráció ablaktáblán válassza az **új létrehozása**lehetőséget. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorNewConnection.png)
8. Az **átjárók** konfigurációja ablaktáblán hagyja letiltva a (nem kattintott) **jelölőnégyzetet** az **átjárón keresztül**.
   
   * Adja meg a **kapcsolattípus**értékét. Írja be például a következőt: `HISDEMO2`.
   * Adja meg az **Informix-kiszolgáló nevének**értékét a név vagy az alias kettőspont portszáma formájában. Írja be például a következőt: `HISDEMO2.cloudapp.net:9089`.
   * Adja meg az **Informix-adatbázis nevének**értékét. Írja be például a következőt: `NWIND`.
   * Adja meg a **username**értéket. Írja be például a következőt: `informix`.
   * Adja meg a **jelszó**értékét. Írja be például a következőt: `Password1`.
9. A folytatáshoz válassza a **Létrehozás** lehetőséget.
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)
10. A **tábla neve** listán válassza a **lefelé mutató nyilat**, majd válassza a **térség**lehetőséget.
11. A lekérdezési beállítások megadásához válassza a **Speciális beállítások megjelenítése** lehetőséget.
12. Kattintson a **Mentés** gombra. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsTableName.png)
13. Az **InformixgetRows** panelen az **Összefoglalás**alatt található **minden Futtatás** listában válassza ki az elsőként felsorolt elemet (a legutóbbi futtatást).
14. A **logikai alkalmazás futtatása** panelen válassza a **Futtatás részletek**lehetőséget. A **művelet** listán válassza a **Get_rows**lehetőséget. Tekintse meg az **állapot**értéket, amelynek **sikeresnek**kell lennie. A bemenetek megtekintéséhez válassza a **bemenetek hivatkozást** . Válassza ki a **kimenetek hivatkozást**, és tekintse meg a kimeneteket; tartalmaznia kell a sorok listáját.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>Egy sor hozzáadása a BESZÚRÁSsal
Létrehozhat egy logikai alkalmazás műveletet, amellyel egy sort adhat hozzá egy Informix-táblában. Ez a művelet arra utasítja az összekötőt, hogy dolgozza fel az Informix INSERT utasítást, például `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása
1. Az Azure-beli **kezdőképernyőn**válassza a **+** (plusz aláírás), **web és mobil**, majd a **logikai alkalmazás**lehetőséget.
2. Adja meg a **nevet**, például `InformixinsertRow`, **előfizetés**, **erőforráscsoport**, **hely**és **app Service terv**. Válassza **a rögzítés az irányítópulton**lehetőséget, majd válassza a **Létrehozás**lehetőséget.

### <a name="add-a-trigger-and-action"></a>Trigger és művelet hozzáadása
1. A **Logic apps Designerben**válassza az **üres LogicApp** elemet a **sablonok** listájában.
2. Az **Eseményindítók** listában válassza az **Ismétlődés**lehetőséget. 
3. Az **ismétlődési** Eseményindítóban válassza a **Szerkesztés**lehetőséget, válassza a **gyakoriság** legördülő menüből a **nap**lehetőséget, majd válassza az **intervallum** elemet a **7-es**típushoz. 
4. Válassza az **+ új lépés** mezőt, majd válassza a **művelet hozzáadása**lehetőséget.
5. A **műveletek** listájában írja be az **Informix** **kifejezést a további műveletek keresése** szövegmezőbe, majd válassza az **Informix – sor beszúrása (előzetes verzió)** lehetőséget.
6. A **sorok beolvasása (előzetes verzió)** műveletnél válassza a **kapcsolat módosítása**lehetőséget. 
7. A **kapcsolatok** konfigurációs ablaktábláján válassza ki a kapcsolatot. Válassza például a **hisdemo2**lehetőséget.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. A **tábla neve** listán válassza a **lefelé mutató nyilat**, majd válassza a **térség**lehetőséget.
9. Adja meg az összes szükséges oszlop értékeit (lásd a piros csillagot). Írja be például a következőt: `99999` **AREAID**, írja be a `Area 99999`, és írja be a következőt: `102` **REGIONID**. 
10. Kattintson a **Mentés** gombra.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowValues.png)
11. Az **InformixinsertRow** panelen az **Összefoglalás**alatt található **minden Futtatás** listában válassza ki az elsőként felsorolt elemet (a legutóbbi futtatást).
12. A **logikai alkalmazás futtatása** panelen válassza a **Futtatás részletek**lehetőséget. A **művelet** listán válassza a **Get_rows**lehetőséget. Tekintse meg az **állapot**értéket, amelynek **sikeresnek**kell lennie. A bemenetek megtekintéséhez válassza a **bemenetek hivatkozást** . Válassza ki a **kimenetek hivatkozást**, és tekintse meg a kimeneteket; Ennek tartalmaznia kell az új sort.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>Egy sor beolvasása a SELECT használatával
Létrehozhat egy logikai alkalmazási műveletet, amely egy sort tud beolvasni egy Informix-táblában. Ez a művelet arra utasítja az összekötőt, hogy dolgozza fel az Informix SELECT WHERE utasítást, például `SELECT FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása
1. Az Azure-beli **kezdőképernyőn**válassza a **+** (plusz aláírás), **web és mobil**, majd a **logikai alkalmazás**lehetőséget.
2. Adja meg a **nevet**, például `InformixgetRow`, **előfizetés**, **erőforráscsoport**, **hely**és **app Service terv**. Válassza **a rögzítés az irányítópulton**lehetőséget, majd válassza a **Létrehozás**lehetőséget.

### <a name="add-a-trigger-and-action"></a>Trigger és művelet hozzáadása
1. A **Logic apps Designerben**válassza az **üres LogicApp** elemet a **sablonok** listájában.
2. Az **Eseményindítók** listában válassza az **Ismétlődés**lehetőséget. 
3. Az **ismétlődési** Eseményindítóban válassza a **Szerkesztés**lehetőséget, válassza a **gyakoriság** legördülő menüből a **nap**lehetőséget, majd válassza az **intervallum** elemet a **7-es**típushoz. 
4. Válassza az **+ új lépés** mezőt, majd válassza a **művelet hozzáadása**lehetőséget.
5. A **műveletek** listán írja be az **Informix** kifejezést a **További műveletek keresése** szövegmezőbe, majd válassza az **Informix-sorok beolvasása (előzetes verzió)** lehetőséget.
6. A **sorok beolvasása (előzetes verzió)** műveletnél válassza a **kapcsolat módosítása**lehetőséget. 
7. A **kapcsolatok** konfigurációk ablaktáblán válassza ki a meglévő kapcsolatot. Válassza például a **hisdemo2**lehetőséget.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. A **tábla neve** listán válassza a **lefelé mutató nyilat**, majd válassza a **térség**lehetőséget.
9. Adja meg az összes szükséges oszlop értékeit (lásd a piros csillagot). Írja be például a következőt: `99999` for **AREAID**. 
10. A lekérdezési beállítások megadásához válassza a **Speciális beállítások megjelenítése** lehetőséget.
11. Kattintson a **Mentés** gombra. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowValues.png)
12. Az **InformixgetRow** panelen az **Összefoglalás**alatt található **minden Futtatás** listában válassza ki az elsőként felsorolt elemet (a legutóbbi futtatást).
13. A **logikai alkalmazás futtatása** panelen válassza a **Futtatás részletek**lehetőséget. A **művelet** listán válassza a **Get_rows**lehetőséget. Tekintse meg az **állapot**értéket, amelynek **sikeresnek**kell lennie. A bemenetek megtekintéséhez válassza a **bemenetek hivatkozást** . Válassza ki a **kimenetek hivatkozást**, és tekintse meg a kimeneteket; Ennek tartalmaznia kell a sort.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>Egy sor módosítása a FRISSÍTÉSsel
Létrehozhat egy logikai alkalmazás műveletet, amely az Informix-tábla egy sorát módosítja. Ez a művelet arra utasítja az összekötőt, hogy dolgozza fel az Informix frissítési utasításait, például `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`.

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása
1. Az Azure-beli **kezdőképernyőn**válassza a **+** (plusz aláírás), **web és mobil**, majd a **logikai alkalmazás**lehetőséget.
2. Adja meg a **nevet**, például `InformixupdateRow`, **előfizetés**, **erőforráscsoport**, **hely**és **app Service terv**. Válassza **a rögzítés az irányítópulton**lehetőséget, majd válassza a **Létrehozás**lehetőséget.

### <a name="add-a-trigger-and-action"></a>Trigger és művelet hozzáadása
1. A **Logic apps Designerben**válassza az **üres LogicApp** elemet a **sablonok** listájában.
2. Az **Eseményindítók** listában válassza az **Ismétlődés**lehetőséget. 
3. Az **ismétlődési** Eseményindítóban válassza a **Szerkesztés**lehetőséget, válassza a **gyakoriság** legördülő menüből a **nap**lehetőséget, majd válassza az **intervallum** elemet a **7-es**típushoz. 
4. Válassza az **+ új lépés** mezőt, majd válassza a **művelet hozzáadása**lehetőséget.
5. A **műveletek** listán írja be az **Informix** kifejezést a **További műveletek keresése** szövegmezőbe, majd válassza az **Informix-Update sort (előzetes verzió)** lehetőséget.
6. A **sorok beolvasása (előzetes verzió)** műveletnél válassza a **kapcsolat módosítása**lehetőséget. 
7. A **kapcsolatok** konfigurációk ablaktáblán válassza ki a meglévő kapcsolatot. Válassza például a **hisdemo2**lehetőséget.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. A **tábla neve** listán válassza a **lefelé mutató nyilat**, majd válassza a **térség**lehetőséget.
9. Adja meg az összes szükséges oszlop értékeit (lásd a piros csillagot). Írja be például a következőt: `99999` **AREAID**, írja be a `Updated 99999`, és írja be a következőt: `102` **REGIONID**. 
10. Kattintson a **Mentés** gombra. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowValues.png)
11. Az **InformixupdateRow** panelen az **Összefoglalás**alatt található **minden Futtatás** listában válassza ki az elsőként felsorolt elemet (a legutóbbi futtatást).
12. A **logikai alkalmazás futtatása** panelen válassza a **Futtatás részletek**lehetőséget. A **művelet** listán válassza a **Get_rows**lehetőséget. Tekintse meg az **állapot**értéket, amelynek **sikeresnek**kell lennie. A bemenetek megtekintéséhez válassza a **bemenetek hivatkozást** . Válassza ki a **kimenetek hivatkozást**, és tekintse meg a kimeneteket; Ennek tartalmaznia kell az új sort.
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>Egy sor eltávolítása a DELETE használatával
Létrehozhat egy logikai alkalmazás műveletet egy sor egy Informix-táblában való eltávolításához. Ez a művelet arra utasítja az összekötőt, hogy egy Informix DELETE utasítást, például `DELETE FROM AREA WHERE AREAID = '99999'`dolgozzon fel.

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása
1. Az Azure-beli **kezdőképernyőn**válassza a **+** (plusz aláírás), **web és mobil**, majd a **logikai alkalmazás**lehetőséget.
2. Adja meg a **nevet**, például `InformixdeleteRow`, **előfizetés**, **erőforráscsoport**, **hely**és **app Service terv**. Válassza **a rögzítés az irányítópulton**lehetőséget, majd válassza a **Létrehozás**lehetőséget.

### <a name="add-a-trigger-and-action"></a>Trigger és művelet hozzáadása
1. A **Logic apps Designerben**válassza az **üres LogicApp** elemet a **sablonok** listájában.
2. Az **Eseményindítók** listában válassza az **Ismétlődés**lehetőséget. 
3. Az **ismétlődési** Eseményindítóban válassza a **Szerkesztés**lehetőséget, válassza a **gyakoriság** legördülő menüből a **nap**lehetőséget, majd válassza az **intervallum** elemet a **7-es**típushoz. 
4. Válassza az **+ új lépés** mezőt, majd válassza a **művelet hozzáadása**lehetőséget.
5. A **műveletek** listán írja be az **Informix** kifejezést a **További műveletek keresése** szövegmezőbe, majd válassza az **Informix – sor törlése (előzetes verzió)** lehetőséget.
6. A **sorok beolvasása (előzetes verzió)** műveletnél válassza a **kapcsolat módosítása**lehetőséget. 
7. A **kapcsolatok** konfigurációk ablaktáblán válasszon ki egy meglévő kapcsolatot. Válassza például a **hisdemo2**lehetőséget.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. A **tábla neve** listán válassza a **lefelé mutató nyilat**, majd válassza a **térség**lehetőséget.
9. Adja meg az összes szükséges oszlop értékeit (lásd a piros csillagot). Írja be például a következőt: `99999` for **AREAID**. 
10. Kattintson a **Mentés** gombra. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowValues.png)
11. Az **InformixdeleteRow** panelen az **Összefoglalás**alatt található **minden Futtatás** listában válassza ki az elsőként felsorolt elemet (a legutóbbi futtatást).
12. A **logikai alkalmazás futtatása** panelen válassza a **Futtatás részletek**lehetőséget. A **művelet** listán válassza a **Get_rows**lehetőséget. Tekintse meg az **állapot**értéket, amelynek **sikeresnek**kell lennie. A bemenetek megtekintéséhez válassza a **bemenetek hivatkozást** . Válassza ki a **kimenetek hivatkozást**, és tekintse meg a kimeneteket; amelynek tartalmaznia kell a törölt sort.
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="supported-informix-platforms-and-versions"></a>Támogatott Informix-platformok és-verziók
Ez az összekötő a következő IBM Informix-verziókat támogatja, ha úgy van konfigurálva, hogy támogassa az elosztott kapcsolati adatbázis-architektúrát (DRDA) használó ügyfélkapcsolatokat.

* IBM Informix 12,1
* IBM Informix 11,7

## <a name="connector-specific-details"></a>Összekötő-specifikus részletek

Megtekintheti a hencegés során definiált összes eseményindítót és műveletet, valamint az [összekötő részleteiben](/connectors/informix/)megjelenő korlátokat is. 

## <a name="next-steps"></a>Következő lépések
[Hozzon létre egy logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ismerkedjen meg a többi elérhető összekötővel Logic Apps [API-k listáján](apis-list.md).

