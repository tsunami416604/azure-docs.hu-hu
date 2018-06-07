---
title: DB2 - Azure Logic Apps csatlakozni |} Microsoft Docs
description: DB2 REST API-k és az Azure Logic Apps-erőforrások kezelése
author: gplarsen
manager: cfowler
ms.author: plarsen
ms.date: 09/26/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, estfan
ms.suite: integration
tags: connectors
ms.openlocfilehash: 9e86273a8b614098aa77608386ab050c20926d23
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34609749"
---
# <a name="get-started-with-the-db2-connector"></a>Ismerkedjen meg a DB2-összekötő
Microsoft DB2-összekötő Logic Apps csatlakozik egy IBM DB2-adatbázisban tárolt erőforrások. Ez az összekötő tartalmazza a Microsoft TCP/IP-hálózaton keresztül DB2-kiszolgáló távoli számítógépekkel folytatott kommunikációhoz. Ez magában foglalja a felhőalapú adatbázisok, például az IBM Bluemix dashDB vagy a Windows Azure virtualizálási futó IBM DB2 és a helyszíni adatbázisokat az helyszíni átjáró használatával. Tekintse meg a [lista támogatott](connectors-create-api-db2.md#supported-db2-platforms-and-versions) IBM DB2-platformok és-verziói (Ez a témakör).

A DB2-összekötő a következő adatbázis műveleteket támogatja:

* Lista adatbázistáblák
* Válasszon használatával egy sor olvasása
* Válassza ki a összes sorok olvasása
* INSERT használatával egy sort ad hozzá
* Egy sor frissítés használatával frissíthető az ALTER
* Egy sor törlése használatával eltávolítása

Ez a témakör bemutatja, hogyan az összekötő használatára a logikai alkalmazás folyamat adatbázis műveletekhez.

A Logic Apps kapcsolatos további információkért lásd: [logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="available-actions"></a>Rendelkezésre álló műveletek
A DB2-összekötő a következő logic app műveleteket támogatja:

* GetTables
* GetRow
* GetRows hívás
* InsertRow
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>Táblák listázása
Bármely művelet logikai alkalmazás létrehozása a Microsoft Azure portálon keresztül sok lépéseken áll.

A logikai alkalmazásban is hozzáadhat egy művelet listáját táblákat DB2-adatbázisban. A művelet feldolgozására DB2-schema utasításban, például az összekötő utasítja `CALL SYSIBM.SQLTABLES`.

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása
1. Az a **Azure start Bizottsága**, jelölje be **+** (plusz jelre), **Web + mobil**, majd **logikai alkalmazás**.
2. Adja meg a **neve**, például a `Db2getTables`, **előfizetés**, **erőforráscsoport**, **hely**, és **App Service-csomag**. Válassza ki **rögzítés az irányítópulton**, majd válassza ki **létrehozása**.

### <a name="add-a-trigger-and-action"></a>Adja hozzá egy eseményindító és művelet
1. Az a **Logic Apps Designer**, jelölje be **üres LogicApp** a a **sablonok** listája.
2. Az a **eseményindítók** listáról válassza ki **ismétlődési**. 
3. Az a **ismétlődési** eseményindító, jelölje be **szerkesztése**, jelölje be **gyakorisága** legördülő listán válassza ki a **nap**, és utána állítsa be a  **Időköz** be **7**.  
4. Válassza ki a **+ új lépés** mezőbe, majd válassza ki **művelet hozzáadása**.
5. Az a **műveletek** kilistázásához írja be `db2` a a **keresse meg a további műveletek** szerkesztési mezőhöz, és válassza **DB2 - Get táblák (előzetes verzió)**.
   
   ![](./media/connectors-create-api-db2/Db2connectorActions.png)  
6. Az a **DB2 - Get táblák** konfigurációs ablaktáblán válassza előbb **jelölőnégyzet** engedélyezése **keresztül, a helyszíni adatátjáró**. Figyelje meg, hogy a beállítások módosítása a felhőből a helyszínen.
   
   * Írja be a következő **Server**, cím- vagy aliasnév kettőspont portszám formájában. Írja be például `ibmserver01:50000`.
   * Írja be a következő **adatbázis**. Írja be például `nwind`.
   * Válassza ki a következő **hitelesítési**. Válassza például **alapvető**.
   * Írja be a következő **felhasználónév**. Írja be például `db2admin`.
   * Írja be a következő **jelszó**. Írja be például `Password1`.
   * Válassza ki a következő **átjáró**. Válassza például **datagateway01**.
7. Válassza ki **létrehozása**, majd válassza ki **mentése**. 
   
    ![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)
8. Az a **Db2getTables** panelen, melyhez a **minden futtatásakor** listában az **összegzés**, válassza ki az első felsorolt elemet (legutóbbi futtatás).
9. Az a **logikai alkalmazás futtatása** panelen válassza **futtatása részletek**. Belül a **művelet** listáról válassza ki **Get_tables**. Tekintse meg a következő **állapot**, amely kell **sikeres**. Válassza ki a **bemenetek hivatkozás** a bemeneti adatok megtekintéséhez. Válassza ki a **kimenetek hivatkozás**, és megtekintheti a kimenetek; kell tartalmaznia, amely olyan táblák listáját.
   
   ![](./media/connectors-create-api-db2/Db2connectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>A kapcsolatok létrehozása
Ez az összekötő-példányokhoz-adatbázisok védelmét a helyszíni és a felhőben, használja a következő csatlakozási tulajdonságokat. 

| Tulajdonság | Leírás |
| --- | --- |
| kiszolgáló |Kötelező. Egy karakterláncértéket, amely egy TCP/IP-cím vagy az alias, IPv4 vagy IPv6 formátumú, majd (pontosvesszővel elválasztott) egy TCP/IP-portszám fogad el. |
| adatbázis |Kötelező. Fogadja el a DRDA relációs adatbázis nevét (RDBNAM) jelölő karakterlánc-érték. Z/os DB2 fogad el egy 16 bájtos karakterlánc (adatbázis egy IBM DB2 z/OS helyének nevezzük). DB2 i5/os fogad el egy 18-többbájtos karakterlánc (adatbázis egy IBM DB2 a néven relációs i adatbázis). A LUW DB2 egy 8 bájtos karakterlánc fogad el. |
| hitelesítés |Választható. Fogadja el a cikk listaértéket, alapszintű vagy a Windows (kerberos). |
| felhasználónév |Kötelező. Egy karakterláncértéket fogad el. Z/os DB2 egy 8 bájtos karakterlánc fogad el. DB2 az i fogad el egy 10 – többbájtos karakterlánc. A Linux vagy UNIX DB2 egy 8 bájtos karakterlánc fogad el. DB2 Windows 30 bájtos karakterlánc fogad el. |
| jelszó |Kötelező. Egy karakterláncértéket fogad el. |
| átjáró |Kötelező. Elem listaértéket, az a helyszíni átjáró meghatározott a Logic Apps a tárolócsoport képviselő fogad el. |

## <a name="create-the-on-premises-gateway-connection"></a>A helyszíni átjáró kapcsolat létrehozása
Ez az összekötő egy helyszíni DB2-adatbázishoz, a helyszíni átjáró hozzáférhet. Átjáró témakörök további információt. 

1. Az a **átjárók** konfigurációs ablaktáblán válassza előbb **jelölőnégyzet** engedélyezése **Connect átjárón keresztül**. Figyelje meg, hogy a beállítások módosítása a felhőből a helyszínen.
2. Írja be a következő **Server**, cím- vagy aliasnév kettőspont portszám formájában. Írja be például `ibmserver01:50000`.
3. Írja be a következő **adatbázis**. Írja be például `nwind`.
4. Válassza ki a következő **hitelesítési**. Válassza például **alapvető**.
5. Írja be a következő **felhasználónév**. Írja be például `db2admin`.
6. Írja be a következő **jelszó**. Írja be például `Password1`.
7. Válassza ki a következő **átjáró**. Válassza például **datagateway01**.
8. Válassza ki **létrehozása** folytatja. 
   
    ![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>A felhő kapcsolat létrehozása
Az összekötő hozzáférhessen egy felhő DB2-adatbázishoz. 

1. Az a **átjárók** konfigurációs panelen hagyja a **jelölőnégyzet** (rákattintás előtt) le van tiltva **átjárón keresztül Connect**. 
2. Írja be a következő **kapcsolatnév**. Írja be például `hisdemo2`.
3. Írja be a következő **DB2 kiszolgálónév**, cím- vagy aliasnév kettőspont portszám formájában. Írja be például `hisdemo2.cloudapp.net:50000`.
4. Írja be a következő **DB2 adatbázisnév**. Írja be például `nwind`.
5. Írja be a következő **felhasználónév**. Írja be például `db2admin`.
6. Írja be a következő **jelszó**. Írja be például `Password1`.
7. Válassza ki **létrehozása** folytatja. 
   
    ![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>Válassza ki a összes sorok beolvasása
Megadhat egy logic app művelet egy DB2-tábla összes sorának beolvasása. Ez az összekötő egy DB2 SELECT utasítás feldolgozására például arra utasítja `SELECT * FROM AREA`.

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása
1. Az a **Azure start Bizottsága**, jelölje be **+** (plusz jelre), **Web + mobil**, majd **logikai alkalmazás**.
2. Adja meg a **neve**, például a `Db2getRows`, **előfizetés**, **erőforráscsoport**, **hely**, és **App Service-csomag**. Válassza ki **rögzítés az irányítópulton**, majd válassza ki **létrehozása**.

### <a name="add-a-trigger-and-action"></a>Adja hozzá egy eseményindító és művelet
1. Az a **Logic Apps Designer**, jelölje be **üres LogicApp** a a **sablonok** listája.
2. Az a **eseményindítók** listáról válassza ki **ismétlődési**. 
3. A a **ismétlődési** eseményindító, jelölje be **szerkesztése**, jelölje be **gyakoriság** legördülő listán válassza ki a **nap**, majd válassza ki **időköz** be **7**. 
4. Válassza ki a **+ új lépés** mezőbe, majd válassza ki **művelet hozzáadása**.
5. Az a **műveletek** kilistázásához írja be `db2` a a **keresse meg a további műveletek** szerkesztési mezőhöz, és válassza **DB2 - Get sorok (előzetes verzió)**.
6. Az a **első sort (előzetes verzió)** művelet, jelölje be **kapcsolat módosítása**.
7. Az a **kapcsolatok** konfigurációs ablaktáblán válassza előbb **hozzon létre új**. 
   
    ![](./media/connectors-create-api-db2/Db2connectorNewConnection.png)
8. Az a **átjárók** konfigurációs panelen hagyja a **jelölőnégyzet** (rákattintás előtt) le van tiltva **átjárón keresztül Connect**.
   
   * Írja be a következő **kapcsolatnév**. Írja be például `HISDEMO2`.
   * Írja be a következő **DB2 kiszolgálónév**, cím- vagy aliasnév kettőspont portszám formájában. Írja be például `HISDEMO2.cloudapp.net:50000`.
   * Írja be a következő **DB2 adatbázisnév**. Írja be például `NWIND`.
   * Írja be a következő **felhasználónév**. Írja be például `db2admin`.
   * Írja be a következő **jelszó**. Írja be például `Password1`.
9. Válassza ki **létrehozása** folytatja.
   
    ![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)
10. Az a **táblanév** listáról válassza ki a **lefelé mutató nyíl**, majd válassza ki **terület**.
11. Bejelölheti **speciális beállítások megjelenítése** lekérdezés beállításainak megadását.
12. Kattintson a **Mentés** gombra. 
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowsTableName.png)
13. Az a **Db2getRows** panelen, melyhez a **minden futtatásakor** listában az **összegzés**, válassza ki az első felsorolt elemet (legutóbbi futtatás).
14. Az a **logikai alkalmazás futtatása** panelen válassza **futtatása részletek**. Belül a **művelet** listáról válassza ki **Get_rows**. Tekintse meg a következő **állapot**, amely kell **sikeres**. Válassza ki a **bemenetek hivatkozás** a bemeneti adatok megtekintéséhez. Válassza ki a **kimenetek hivatkozás**, és megtekintheti a kimenetek; kell tartalmaznia, amely sorok listája.
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>INSERT használatával egy sort ad hozzá
A logic app művelet egy sort ad hozzá egy DB2-tábla adhat meg. Ez a művelet arra utasítja az összekötő egy DB2 INSERT utasítás feldolgozására például `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása
1. Az a **Azure start Bizottsága**, jelölje be **+** (plusz jelre), **Web + mobil**, majd **logikai alkalmazás**.
2. Adja meg a **neve**, például a `Db2insertRow`, **előfizetés**, **erőforráscsoport**, **hely**, és **App Service-csomag**. Válassza ki **rögzítés az irányítópulton**, majd válassza ki **létrehozása**.

### <a name="add-a-trigger-and-action"></a>Adja hozzá egy eseményindító és művelet
1. Az a **Logic Apps Designer**, jelölje be **üres LogicApp** a a **sablonok** listája.
2. Az a **eseményindítók** listáról válassza ki **ismétlődési**. 
3. A a **ismétlődési** eseményindító, jelölje be **szerkesztése**, jelölje be **gyakoriság** legördülő listán válassza ki a **nap**, majd válassza ki **időköz** be **7**. 
4. Válassza ki a **+ új lépés** mezőbe, majd válassza ki **művelet hozzáadása**.
5. Az a **műveletek** kilistázásához írja be **db2** a a **keresse meg a további műveletek** szerkesztési mezőhöz, és válassza **DB2 - (előzetes verzió) sor beszúrása**.
6. Az a **DB2 - (előzetes verzió) sor beszúrása** művelet, jelölje be **kapcsolat módosítása**. 
7. Az a **kapcsolatok** konfigurációs ablaktáblán válasszon ki egy kapcsolattípust. Válassza például **hisdemo2**.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. Az a **táblanév** listáról válassza ki a **lefelé mutató nyíl**, majd válassza ki **terület**.
9. Adja meg (lásd a piros csillaggal jelölt) minden szükséges oszlopot. Írja be például `99999` a **AREAID**, típus `Area 99999`, és írja be `102` a **REGIONID**. 
10. Kattintson a **Mentés** gombra.
    
    ![](./media/connectors-create-api-db2/Db2connectorInsertRowValues.png)
11. Az a **Db2insertRow** panelen, melyhez a **minden futtatásakor** listában az **összegzés**, válassza ki az első felsorolt elemet (legutóbbi futtatás).
12. Az a **logikai alkalmazás futtatása** panelen válassza **futtatása részletek**. Belül a **művelet** listáról válassza ki **Get_rows**. Tekintse meg a következő **állapot**, amely kell **sikeres**. Válassza ki a **bemenetek hivatkozás** a bemeneti adatok megtekintéséhez. Válassza ki a **kimenetek hivatkozás**, és megtekintheti a kimenetek; kell tartalmaznia, amely az új sor.
    
    ![](./media/connectors-create-api-db2/Db2connectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>Válasszon használatával egy sort lehívni
A logic app művelet egy DB2-táblázat egy sort lehívni adhat meg. Ez a művelet feldolgozására egy DB2 ahol válasszon utasítás, például az összekötő utasítja `SELECT FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása
1. Az a **Azure start Bizottsága**, jelölje be **+** (plusz jelre), **Web + mobil**, majd **logikai alkalmazás**.
2. Adja meg a **neve** (pl. "**Db2getRow**"), **előfizetés**, **erőforráscsoport**, **hely**, és **App Service-csomag**. Válassza ki **rögzítés az irányítópulton**, majd válassza ki **létrehozása**.

### <a name="add-a-trigger-and-action"></a>Adja hozzá egy eseményindító és művelet
1. Az a **Logic Apps Designer**, jelölje be **üres LogicApp** a a **sablonok** listája. 
2. Az a **eseményindítók** listáról válassza ki **ismétlődési**. 
3. A a **ismétlődési** eseményindító, jelölje be **szerkesztése**, jelölje be **gyakoriság** legördülő listán válassza ki a **nap**, majd válassza ki **időköz** be **7**. 
4. Válassza ki a **+ új lépés** mezőbe, majd válassza ki **művelet hozzáadása**.
5. Az a **műveletek** kilistázásához írja be **db2** a a **keresse meg a további műveletek** szerkesztési mezőhöz, és válassza **DB2 - Get sorok (előzetes verzió)**.
6. Az a **első sort (előzetes verzió)** művelet, jelölje be **kapcsolat módosítása**. 
7. Az a **kapcsolatok** konfigurációk ablaktáblán válassza ki egy létező kapcsolatot. Válassza például **hisdemo2**.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. Az a **táblanév** listáról válassza ki a **lefelé mutató nyíl**, majd válassza ki **terület**.
9. Adja meg (lásd a piros csillaggal jelölt) minden szükséges oszlopot. Írja be például `99999` a **AREAID**. 
10. Bejelölheti **speciális beállítások megjelenítése** lekérdezés beállításainak megadását.
11. Kattintson a **Mentés** gombra. 
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowValues.png)
12. Az a **Db2getRow** panelen, melyhez a **minden futtatásakor** listában az **összegzés**, válassza ki az első felsorolt elemet (legutóbbi futtatás).
13. Az a **logikai alkalmazás futtatása** panelen válassza **futtatása részletek**. Belül a **művelet** listáról válassza ki **Get_rows**. Tekintse meg a következő **állapot**, amely kell **sikeres**. Válassza ki a **bemenetek hivatkozás** a bemeneti adatok megtekintéséhez. Válassza ki a **kimenetek hivatkozás**, és megtekintheti a kimenetek; amely sort kell tartalmaznia.
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>FRISSÍTÉS használatával frissíthető egy sor módosítása
A logic app művelet soronként egy DB2-tábla módosítása adhat meg. Ez a művelet feldolgozására DB2 utasítást, például az összekötő utasítja `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`.

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása
1. Az a **Azure start Bizottsága**, jelölje be **+** (plusz jelre), **Web + mobil**, majd **logikai alkalmazás**.
2. Adja meg a **neve**, például a `Db2updateRow`, **előfizetés**, **erőforráscsoport**, **hely**, és **App Service-csomag**. Válassza ki **rögzítés az irányítópulton**, majd válassza ki **létrehozása**.

### <a name="add-a-trigger-and-action"></a>Adja hozzá egy eseményindító és művelet
1. Az a **Logic Apps Designer**, jelölje be **üres LogicApp** a a **sablonok** listája.
2. Az a **eseményindítók** listáról válassza ki **ismétlődési**. 
3. A a **ismétlődési** eseményindító, jelölje be **szerkesztése**, jelölje be **gyakoriság** legördülő listán válassza ki a **nap**, majd válassza ki **időköz** be **7**. 
4. Válassza ki a **+ új lépés** mezőbe, majd válassza ki **művelet hozzáadása**.
5. Az a **műveletek** kilistázásához írja be **db2** a a **keresse meg a további műveletek** szerkesztési mezőhöz, és válassza **DB2 - frissítés sor (előzetes verzió)**.
6. Az a **DB2 - frissítés sor (előzetes verzió)** művelet, jelölje be **kapcsolat módosítása**. 
7. Az a **kapcsolatok** konfigurációk ablaktáblán válassza előbb jelölje be a meglévő kapcsolat. Válassza például **hisdemo2**.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. Az a **táblanév** listáról válassza ki a **lefelé mutató nyíl**, majd válassza ki **terület**.
9. Adja meg (lásd a piros csillaggal jelölt) minden szükséges oszlopot. Írja be például `99999` a **AREAID**, típus `Updated 99999`, és írja be `102` a **REGIONID**. 
10. Kattintson a **Mentés** gombra. 
    
    ![](./media/connectors-create-api-db2/Db2connectorUpdateRowValues.png)
11. Az a **Db2updateRow** panelen, melyhez a **minden futtatásakor** listában az **összegzés**, válassza ki az első felsorolt elemet (legutóbbi futtatás).
12. Az a **logikai alkalmazás futtatása** panelen válassza **futtatása részletek**. Belül a **művelet** listáról válassza ki **Get_rows**. Tekintse meg a következő **állapot**, amely kell **sikeres**. Válassza ki a **bemenetek hivatkozás** a bemeneti adatok megtekintéséhez. Válassza ki a **kimenetek hivatkozás**, és megtekintheti a kimenetek; kell tartalmaznia, amely az új sor.
    
    ![](./media/connectors-create-api-db2/Db2connectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>Egy sor törlése használatával eltávolítása
Megadhatja a logikai alkalmazás művelet egy sor eltávolítása egy DB2-tábla. Ez a művelet arra utasítja az összekötő egy DB2 DELETE utasítás feldolgozására például `DELETE FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása
1. Az a **Azure start Bizottsága**, jelölje be **+** (plusz jelre), **Web + mobil**, majd **logikai alkalmazás**.
2. Adja meg a **neve**, például a `Db2deleteRow`, **előfizetés**, **erőforráscsoport**, **hely**, és **App Service-csomag**. Válassza ki **rögzítés az irányítópulton**, majd válassza ki **létrehozása**.

### <a name="add-a-trigger-and-action"></a>Adja hozzá egy eseményindító és művelet
1. Az a **Logic Apps Designer**, jelölje be **üres LogicApp** a a **sablonok** listája. 
2. Az a **eseményindítók** listáról válassza ki **ismétlődési**. 
3. A a **ismétlődési** eseményindító, jelölje be **szerkesztése**, jelölje be **gyakoriság** legördülő listán válassza ki a **nap**, majd válassza ki **időköz** be **7**. 
4. Válassza ki a **+ új lépés** mezőbe, majd válassza ki **művelet hozzáadása**.
5. Az a **műveletek** listáról válassza ki **db2** a a **keresse meg a további műveletek** szerkesztési mezőhöz, és válassza **DB2 - sor törlése (előzetes verzió)**.
6. Az a **DB2 - sor törlése (előzetes verzió)** művelet, jelölje be **kapcsolat módosítása**. 
7. Az a **kapcsolatok** konfigurációk ablaktáblán válassza ki egy létező kapcsolatot. Válassza például **hisdemo2**.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. Az a **táblanév** listáról válassza ki a **lefelé mutató nyíl**, majd válassza ki **terület**.
9. Adja meg (lásd a piros csillaggal jelölt) minden szükséges oszlopot. Írja be például `99999` a **AREAID**. 
10. Kattintson a **Mentés** gombra. 
    
    ![](./media/connectors-create-api-db2/Db2connectorDeleteRowValues.png)
11. Az a **Db2deleteRow** panelen, melyhez a **minden futtatásakor** listában az **összegzés**, válassza ki az első felsorolt elemet (legutóbbi futtatás).
12. Az a **logikai alkalmazás futtatása** panelen válassza **futtatása részletek**. Belül a **művelet** listáról válassza ki **Get_rows**. Tekintse meg a következő **állapot**, amely kell **sikeres**. Válassza ki a **bemenetek hivatkozás** a bemeneti adatok megtekintéséhez. Válassza ki a **kimenetek hivatkozás**, és megtekintheti a kimenetek; kell tartalmaznia, amely a törölt sor.
    
    ![](./media/connectors-create-api-db2/Db2connectorDeleteRowOutputs.png)

## <a name="supported-db2-platforms-and-versions"></a>Támogatott platformok DB2 és verziók
Ez az összekötő támogatja a következő IBM DB2-platformok és verziók, valamint IBM DB2 kompatibilis termékek (pl. IBM Bluemix dashDB), amely elosztott relációs adatbázis architektúra (DRDA) SQL Access Manager (SQLAM) 10 vagy 11 verziója támogatja:

* IBM DB2 z/os 11.1
* IBM DB2 z/OS 10.1
* Az IBM DB2 i 7.3.
* Az IBM DB2 i 7.2
* Az IBM DB2 i 7.1
* IBM DB2 LUW 11
* IBM DB2 LUW 10.5 számára

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Bármely eseményindítók és a swagger definiált műveletek megtekintése, és semmilyen határnak a Lásd még: a [connector részleteket](/connectors/db2/). 

## <a name="next-steps"></a>További lépések
[Logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md). Az egyéb rendelkezésre álló összekötők Logic Apps, megismerkedhet a [API-k lista](apis-list.md).

