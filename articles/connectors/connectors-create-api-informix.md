---
title: IBM Informix-adatbázishoz – Azure Logic Apps csatlakozni |} Microsoft Docs
description: Az IBM Informix REST API-k és az Azure Logic Apps-erőforrások kezelése
author: gplarsen
manager: cfowler
ms.author: plarsen
ms.date: 09/26/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: c78c6bb669e0945ba1cbacc3ca808a364f3099a5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34609388"
---
# <a name="get-started-with-the-informix-connector"></a>Ismerkedjen meg az Informix-összekötő
Microsoft-összekötő Informix a Logic Apps csatlakozik IBM Informix-adatbázisban tárolt erőforrások. Az Informix-összekötő tartalmazza a Microsoft az Informix-kiszolgáló távoli számítógépeken egy TCP/IP-hálózaton keresztül kommunikálnak. Ez magában foglalja a felhőalapú adatbázisok, például a Windows Azure virtualizálási futó IBM Informix és a helyszíni adatbázisokat az helyszíni átjáró használatával. Tekintse meg a [lista támogatott](connectors-create-api-informix.md#supported-informix-platforms-and-versions) IBM Informix-platformok és-verziói (Ez a témakör).

Az összekötő a következő adatbázis műveleteket támogatja:

* Lista adatbázistáblák
* Válasszon használatával egy sor olvasása
* Válassza ki a összes sorok olvasása
* INSERT használatával egy sort ad hozzá
* Egy sor frissítés használatával frissíthető az ALTER
* Egy sor törlése használatával eltávolítása

Ez a témakör bemutatja, hogyan az összekötő használatára a logikai alkalmazás folyamat adatbázis műveletekhez.

A Logic Apps kapcsolatos további információkért lásd: [logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="available-actions"></a>Rendelkezésre álló műveletek
Ez az összekötő a következő logic app műveleteket támogatja:

* Getables
* GetRow
* GetRows hívás
* InsertRow
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>Táblák listázása
Bármely művelet logikai alkalmazás létrehozása a Microsoft Azure portálon keresztül sok lépéseken áll.

A logikai alkalmazásban is hozzáadhat egy művelet listáját táblákat Informix-adatbázisban. Ez a művelet feldolgozására Informix-schema utasításban, például az összekötő utasítja `CALL SYSIBM.SQLTABLES`.

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása
1. Az a **Azure start Bizottsága**, jelölje be **+** (plusz jelre), **Web + mobil**, majd **logikai alkalmazás**.
2. Adja meg a **neve**, például a `InformixgetTables`, **előfizetés**, **erőforráscsoport**, **hely**, és **App Service-csomag**. Válassza ki **rögzítés az irányítópulton**, majd válassza ki **létrehozása**.

### <a name="add-a-trigger-and-action"></a>Adja hozzá egy eseményindító és művelet
1. Az a **Logic Apps Designer**, jelölje be **üres LogicApp** a a **sablonok** listája.
2. Az a **eseményindítók** listáról válassza ki **ismétlődési**. 
3. A a **ismétlődési** eseményindító, jelölje be **szerkesztése**, jelölje be **gyakoriság** legördülő listán válassza ki a **nap**, majd válassza ki **időköz** be **7**.  
4. Válassza ki a **+ új lépés** mezőbe, majd válassza ki **művelet hozzáadása**.
5. Az a **műveletek** kilistázásához írja be **informix** a a **keresse meg a további műveletek** szerkesztési mezőhöz, és válassza **Informix - Get táblák (előzetes verzió)**.
   
   ![](./media/connectors-create-api-informix/InformixconnectorActions.png)  
6. Az a **Informix - Get táblák** konfigurációs ablaktáblán válassza előbb **jelölőnégyzet** engedélyezése **keresztül, a helyszíni adatátjáró**. Figyelje meg, hogy a beállítások módosítása a felhőből a helyszínen.
   
   * Írja be a következő **Server**, cím- vagy aliasnév kettőspont portszám formájában. Írja be például `ibmserver01:9089`.
   * Írja be a következő **adatbázis**. Írja be például `nwind`.
   * Válassza ki a következő **hitelesítési**. Válassza például **alapvető**.
   * Írja be a következő **felhasználónév**. Írja be például `informix`.
   * Írja be a következő **jelszó**. Írja be például `Password1`.
   * Válassza ki a következő **átjáró**. Válassza például **datagateway01**.
7. Válassza ki **létrehozása**, majd válassza ki **mentése**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)
8. Az a **InformixgetTables** panelen, melyhez a **minden futtatásakor** listában az **összegzés**, válassza ki az első felsorolt elemet (legutóbbi futtatás).
9. Az a **logikai alkalmazás futtatása** panelen válassza **futtatása részletek**. Belül a **művelet** listáról válassza ki **Get_tables**. Tekintse meg a következő **állapot**, amely kell **sikeres**. Válassza ki a **bemenetek hivatkozás** a bemeneti adatok megtekintéséhez. Válassza ki a **kimenetek hivatkozás**, és megtekintheti a kimenetek; kell tartalmaznia, amely olyan táblák listáját.
   
   ![](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>A kapcsolatok létrehozása
Ez az összekötő támogatja a kapcsolatok helyszíni adatbázis és a felhőben, használja a következő csatlakozási tulajdonságokat. 

| Tulajdonság | Leírás |
| --- | --- |
| kiszolgáló |Kötelező. Egy karakterláncértéket, amely a egy TCP/IP-cím vagy egy aliast IPv4 vagy IPv6 formátumú, majd (kettőspont tagolt) egy TCP/IP-portszám fogad el. |
| adatbázis |Kötelező. Egy karakterláncértéket, amely egy DRDA relációs adatbázis nevét (RDBNAM) fogad el. Informix 128 bájtos karakterlánc fogad el (adatbázis az IBM Informix-adatbázis nevének (dbname) nevezik). |
| hitelesítés |Választható. Fogadja el a cikk listaértéket, alapszintű vagy a Windows (kerberos). |
| felhasználónév |Kötelező. Egy karakterláncértéket fogad el. |
| jelszó |Kötelező. Egy karakterláncértéket fogad el. |
| átjáró |Kötelező. Elem listaértéket, az a helyszíni átjáró meghatározott a Logic Apps a tárolócsoport képviselő fogad el. |

## <a name="create-the-on-premises-gateway-connection"></a>A helyszíni átjáró kapcsolat létrehozása
Ez az összekötő hozzáférhet a helyszíni adatok átjáró helyszíni Informix-adatbázishoz. Átjáró témakörök további információt. 

1. Az a **átjárók** konfigurációs ablaktáblán válassza előbb **jelölőnégyzet** engedélyezése **Connect átjárón keresztül**. Tekintse meg a beállításokat a felhőből a helyszíni módosítása.
2. Írja be a következő **Server**, cím- vagy aliasnév kettőspont portszám formájában. Írja be például `ibmserver01:9089`.
3. Írja be a következő **adatbázis**. Írja be például `nwind`.
4. Válassza ki a következő **hitelesítési**. Válassza például **alapvető**.
5. Írja be a következő **felhasználónév**. Írja be például `informix`.
6. Írja be a következő **jelszó**. Írja be például `Password1`.
7. Válassza ki a következő **átjáró**. Válassza például **datagateway01**.
8. Válassza ki **létrehozása** folytatja. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>A felhő kapcsolat létrehozása
Ez az összekötő érhetik el a felhő Informix-adatbázishoz. 

1. Az a **átjárók** konfigurációs panelen hagyja a **jelölőnégyzet** (rákattintás előtt) le van tiltva **átjárón keresztül Connect**. 
2. Írja be a következő **kapcsolatnév**. Írja be például `hisdemo2`.
3. Írja be a következő **Informix kiszolgálónév**, cím- vagy aliasnév kettőspont portszám formájában. Írja be például `hisdemo2.cloudapp.net:9089`.
4. Írja be a következő **Informix adatbázisnév**. Írja be például `nwind`.
5. Írja be a következő **felhasználónév**. Írja be például `informix`.
6. Írja be a következő **jelszó**. Írja be például `Password1`.
7. Válassza ki **létrehozása** folytatja. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>Válassza ki a összes sorok beolvasása
Létrehozhat egy logic app művelet beolvasása az Informix-tábla összes sorát. Ez a művelet arra utasítja az összekötő egy Informix SELECT utasítás feldolgozására például `SELECT * FROM AREA`.

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása
1. Az a **Azure start Bizottsága**, jelölje be **+** (plusz jelre), **Web + mobil**, majd **logikai alkalmazás**.
2. Adja meg a **neve** (pl. "**InformixgetRows**"), **előfizetés**, **erőforráscsoport**, **hely**, és **App Service-csomag**. Válassza ki **rögzítés az irányítópulton**, majd válassza ki **létrehozása**.

### <a name="add-a-trigger-and-action"></a>Adja hozzá egy eseményindító és művelet
1. Az a **Logic Apps Designer**, jelölje be **üres LogicApp** a a **sablonok** listája.
2. Az a **eseményindítók** listáról válassza ki **ismétlődési**. 
3. A a **ismétlődési** eseményindító, jelölje be **szerkesztése**, jelölje be **gyakoriság** legördülő listán válassza ki a **nap**, majd válassza ki **időköz** be **7**. 
4. Válassza ki a **+ új lépés** mezőbe, majd válassza ki **művelet hozzáadása**.
5. Az a **műveletek** kilistázásához írja be **informix** a a **keresse meg a további műveletek** szerkesztési mezőhöz, és válassza **Informix - Get sorok (előzetes verzió)**.
6. Az a **első sort (előzetes verzió)** művelet, jelölje be **kapcsolat módosítása**.
7. Az a **kapcsolatok** konfigurációs ablaktáblán válassza előbb **hozzon létre új**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorNewConnection.png)
8. Az a **átjárók** konfigurációs panelen hagyja a **jelölőnégyzet** (rákattintás előtt) le van tiltva **átjárón keresztül Connect**.
   
   * Írja be a következő **kapcsolatnév**. Írja be például `HISDEMO2`.
   * Írja be a következő **Informix kiszolgálónév**, cím- vagy aliasnév kettőspont portszám formájában. Írja be például `HISDEMO2.cloudapp.net:9089`.
   * Írja be a következő **Informix adatbázisnév**. Írja be például `NWIND`.
   * Írja be a következő **felhasználónév**. Írja be például `informix`.
   * Írja be a következő **jelszó**. Írja be például `Password1`.
9. Válassza ki **létrehozása** folytatja.
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)
10. Az a **táblanév** listáról válassza ki a **lefelé mutató nyíl**, majd válassza ki **terület**.
11. Bejelölheti **speciális beállítások megjelenítése** lekérdezés beállításainak megadását.
12. Kattintson a **Mentés** gombra. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsTableName.png)
13. Az a **InformixgetRows** panelen, melyhez a **minden futtatásakor** listában az **összegzés**, válassza ki az első felsorolt elemet (legutóbbi futtatás).
14. Az a **logikai alkalmazás futtatása** panelen válassza **futtatása részletek**. Belül a **művelet** listáról válassza ki **Get_rows**. Tekintse meg a következő **állapot**, amely kell **sikeres**. Válassza ki a **bemenetek hivatkozás** a bemeneti adatok megtekintéséhez. Válassza ki a **kimenetek hivatkozás**, és megtekintheti a kimenetek; kell tartalmaznia, amely sorok listája.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>INSERT használatával egy sort ad hozzá
Létrehozhat egy sort ad hozzá egy Informix-tábla egy logic app-műveletet. Ez a művelet arra utasítja az összekötő egy Informix INSERT utasítás feldolgozására például `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása
1. Az a **Azure start Bizottsága**, jelölje be **+** (plusz jelre), **Web + mobil**, majd **logikai alkalmazás**.
2. Adja meg a **neve**, például a `InformixinsertRow`, **előfizetés**, **erőforráscsoport**, **hely**, és **App Service-csomag**. Válassza ki **rögzítés az irányítópulton**, majd válassza ki **létrehozása**.

### <a name="add-a-trigger-and-action"></a>Adja hozzá egy eseményindító és művelet
1. Az a **Logic Apps Designer**, jelölje be **üres LogicApp** a a **sablonok** listája.
2. Az a **eseményindítók** listáról válassza ki **ismétlődési**. 
3. A a **ismétlődési** eseményindító, jelölje be **szerkesztése**, jelölje be **gyakoriság** legördülő listán válassza ki a **nap**, majd válassza ki **időköz** be **7**. 
4. Válassza ki a **+ új lépés** mezőbe, majd válassza ki **művelet hozzáadása**.
5. Az a **műveletek** kilistázásához írja be **informix** a a **keresse meg a további műveletek** szerkesztési mezőhöz, és válassza **Informix - (előzetes verzió) sor beszúrása**.
6. Az a **első sort (előzetes verzió)** művelet, jelölje be **kapcsolat módosítása**. 
7. Az a **kapcsolatok** konfigurációs ablaktáblán válassza előbb a válasszon ki egy kapcsolatot. Válassza például **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Az a **táblanév** listáról válassza ki a **lefelé mutató nyíl**, majd válassza ki **terület**.
9. Adja meg (lásd a piros csillaggal jelölt) minden szükséges oszlopot. Írja be például `99999` a **AREAID**, típus `Area 99999`, és írja be `102` a **REGIONID**. 
10. Kattintson a **Mentés** gombra.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowValues.png)
11. Az a **InformixinsertRow** panelen, melyhez a **minden futtatásakor** listában az **összegzés**, válassza ki az első felsorolt elemet (legutóbbi futtatás).
12. Az a **logikai alkalmazás futtatása** panelen válassza **futtatása részletek**. Belül a **művelet** listáról válassza ki **Get_rows**. Tekintse meg a következő **állapot**, amely kell **sikeres**. Válassza ki a **bemenetek hivatkozás** a bemeneti adatok megtekintéséhez. Válassza ki a **kimenetek hivatkozás**, és megtekintheti a kimenetek; kell tartalmaznia, amely az új sor.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>Válasszon használatával egy sort lehívni
A logic app művelet egy Informix táblázat egy sort lehívni hozhat létre. Ez a művelet feldolgozására egy Informix ahol válasszon utasítás, például az összekötő utasítja `SELECT FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása
1. Az a **Azure start Bizottsága**, jelölje be **+** (plusz jelre), **Web + mobil**, majd **logikai alkalmazás**.
2. Adja meg a **neve**, például a `InformixgetRow`, **előfizetés**, **erőforráscsoport**, **hely**, és **App Service-csomag**. Válassza ki **rögzítés az irányítópulton**, majd válassza ki **létrehozása**.

### <a name="add-a-trigger-and-action"></a>Adja hozzá egy eseményindító és művelet
1. Az a **Logic Apps Designer**, jelölje be **üres LogicApp** a a **sablonok** listája.
2. Az a **eseményindítók** listáról válassza ki **ismétlődési**. 
3. A a **ismétlődési** eseményindító, jelölje be **szerkesztése**, jelölje be **gyakoriság** legördülő listán válassza ki a **nap**, majd válassza ki **időköz** be **7**. 
4. Válassza ki a **+ új lépés** mezőbe, majd válassza ki **művelet hozzáadása**.
5. Az a **műveletek** kilistázásához írja be **informix** a a **keresse meg a további műveletek** szerkesztési mezőhöz, és válassza **Informix - Get sorok (előzetes verzió)**.
6. Az a **első sort (előzetes verzió)** művelet, jelölje be **kapcsolat módosítása**. 
7. Az a **kapcsolatok** konfigurációk ablaktáblán válassza előbb jelölje be a meglévő kapcsolat. Válassza például **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Az a **táblanév** listáról válassza ki a **lefelé mutató nyíl**, majd válassza ki **terület**.
9. Adja meg (lásd a piros csillaggal jelölt) minden szükséges oszlopot. Írja be például `99999` a **AREAID**. 
10. Bejelölheti **speciális beállítások megjelenítése** lekérdezés beállításainak megadását.
11. Kattintson a **Mentés** gombra. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowValues.png)
12. Az a **InformixgetRow** panelen, melyhez a **minden futtatásakor** listában az **összegzés**, válassza ki az első felsorolt elemet (legutóbbi futtatás).
13. Az a **logikai alkalmazás futtatása** panelen válassza **futtatása részletek**. Belül a **művelet** listáról válassza ki **Get_rows**. Tekintse meg a következő **állapot**, amely kell **sikeres**. Válassza ki a **bemenetek hivatkozás** a bemeneti adatok megtekintéséhez. Válassza ki a **kimenetek hivatkozás**, és megtekintheti a kimenetek; amely sort kell tartalmaznia.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>FRISSÍTÉS használatával frissíthető egy sor módosítása
Létrehozhat egy logic app művelet soronként egy Informix-tábla módosítása. Ez a művelet feldolgozására Informix utasítást, például az összekötő utasítja `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`.

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása
1. Az a **Azure start Bizottsága**, jelölje be **+** (plusz jelre), **Web + mobil**, majd **logikai alkalmazás**.
2. Adja meg a **neve**, például a `InformixupdateRow`, **előfizetés**, **erőforráscsoport**, **hely**, és **App Service-csomag**. Válassza ki **rögzítés az irányítópulton**, majd válassza ki **létrehozása**.

### <a name="add-a-trigger-and-action"></a>Adja hozzá egy eseményindító és művelet
1. Az a **Logic Apps Designer**, jelölje be **üres LogicApp** a a **sablonok** listája.
2. Az a **eseményindítók** listáról válassza ki **ismétlődési**. 
3. A a **ismétlődési** eseményindító, jelölje be **szerkesztése**, jelölje be **gyakoriság** legördülő listán válassza ki a **nap**, majd válassza ki **időköz** be **7**. 
4. Válassza ki a **+ új lépés** mezőbe, majd válassza ki **művelet hozzáadása**.
5. Az a **műveletek** kilistázásához írja be **informix** a a **keresse meg a további műveletek** szerkesztési mezőhöz, és válassza **Informix - frissítés sor (előzetes verzió)**.
6. Az a **első sort (előzetes verzió)** művelet, jelölje be **kapcsolat módosítása**. 
7. Az a **kapcsolatok** konfigurációk ablaktáblán válassza előbb jelölje be a meglévő kapcsolat. Válassza például **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Az a **táblanév** listáról válassza ki a **lefelé mutató nyíl**, majd válassza ki **terület**.
9. Adja meg (lásd a piros csillaggal jelölt) minden szükséges oszlopot. Írja be például `99999` a **AREAID**, típus `Updated 99999`, és írja be `102` a **REGIONID**. 
10. Kattintson a **Mentés** gombra. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowValues.png)
11. Az a **InformixupdateRow** panelen, melyhez a **minden futtatásakor** listában az **összegzés**, válassza ki az első felsorolt elemet (legutóbbi futtatás).
12. Az a **logikai alkalmazás futtatása** panelen válassza **futtatása részletek**. Belül a **művelet** listáról válassza ki **Get_rows**. Tekintse meg a következő **állapot**, amely kell **sikeres**. Válassza ki a **bemenetek hivatkozás** a bemeneti adatok megtekintéséhez. Válassza ki a **kimenetek hivatkozás**, és megtekintheti a kimenetek; kell tartalmaznia, amely az új sor.
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>Egy sor törlése használatával eltávolítása
Létrehozhat egy logic app művelet egy sor eltávolítása egy Informix-tábla. Ez a művelet arra utasítja az összekötő egy Informix DELETE utasítás feldolgozására például `DELETE FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása
1. Az a **Azure start Bizottsága**, jelölje be **+** (plusz jelre), **Web + mobil**, majd **logikai alkalmazás**.
2. Adja meg a **neve**, például a `InformixdeleteRow`, **előfizetés**, **erőforráscsoport**, **hely**, és **App Service-csomag**. Válassza ki **rögzítés az irányítópulton**, majd válassza ki **létrehozása**.

### <a name="add-a-trigger-and-action"></a>Adja hozzá egy eseményindító és művelet
1. Az a **Logic Apps Designer**, jelölje be **üres LogicApp** a a **sablonok** listája.
2. Az a **eseményindítók** listáról válassza ki **ismétlődési**. 
3. A a **ismétlődési** eseményindító, jelölje be **szerkesztése**, jelölje be **gyakoriság** legördülő listán válassza ki a **nap**, majd válassza ki **időköz** be **7**. 
4. Válassza ki a **+ új lépés** mezőbe, majd válassza ki **művelet hozzáadása**.
5. Az a **műveletek** kilistázásához írja be **informix** a a **keresse meg a további műveletek** szerkesztési mezőhöz, és válassza **Informix - sor törlése (előzetes verzió)**.
6. Az a **első sort (előzetes verzió)** művelet, jelölje be **kapcsolat módosítása**. 
7. Az a **kapcsolatok** konfigurációk ablaktáblán válassza ki egy létező kapcsolatot. Válassza például **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Az a **táblanév** listáról válassza ki a **lefelé mutató nyíl**, majd válassza ki **terület**.
9. Adja meg (lásd a piros csillaggal jelölt) minden szükséges oszlopot. Írja be például `99999` a **AREAID**. 
10. Kattintson a **Mentés** gombra. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowValues.png)
11. Az a **InformixdeleteRow** panelen, melyhez a **minden futtatásakor** listában az **összegzés**, válassza ki az első felsorolt elemet (legutóbbi futtatás).
12. Az a **logikai alkalmazás futtatása** panelen válassza **futtatása részletek**. Belül a **művelet** listáról válassza ki **Get_rows**. Tekintse meg a következő **állapot**, amely kell **sikeres**. Válassza ki a **bemenetek hivatkozás** a bemeneti adatok megtekintéséhez. Válassza ki a **kimenetek hivatkozás**, és megtekintheti a kimenetek; kell tartalmaznia, amely a törölt sor.
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="supported-informix-platforms-and-versions"></a>Támogatott platformok Informix és verziói
Ez az összekötő támogatja az alábbi IBM Informix-verziók, elosztott relációs adatbázis architektúra (DRDA) ügyfélkapcsolatok támogatásához konfigurálásakor.

* IBM Informix 12.1
* IBM Informix 11.7

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Bármely eseményindítók és a swagger definiált műveletek megtekintése, és semmilyen határnak a Lásd még: a [connector részleteket](/connectors/informix/). 

## <a name="next-steps"></a>További lépések
[Logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md). Az egyéb rendelkezésre álló összekötők Logic Apps, megismerkedhet a [API-k lista](apis-list.md).

