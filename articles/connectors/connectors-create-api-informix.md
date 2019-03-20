---
title: IBM Informix-adatbázishoz – Azure Logic Apps |} A Microsoft Docs
description: Az IBM Informix REST API-k és az Azure Logic Apps-erőforrások kezelése
author: gplarsen
manager: jeconnoc
ms.author: plarsen
ms.date: 09/26/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 6004c02f190bbfcf374b3b5d2a5c478f0e52c961
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58165900"
---
# <a name="get-started-with-the-informix-connector"></a>Az Informix-összekötő használatának első lépései
Informix-összekötő a Microsoft a Logic Apps csatlakozik IBM Informix-adatbázisban tárolt erőforrásokhoz. Az Informix-összekötő tartalmazza a Microsoft felé történő Informix-kiszolgáló távoli számítógépek egy TCP/IP-hálózaton keresztül. Ez magában foglalja a felhőalapú adatbázisok, például a virtualizálás az Azure-ban futó Windows IBM Informix, és a helyszíni adatbázisok a helyszíni adatátjáró használatával. Tekintse meg a [lista támogatott](connectors-create-api-informix.md#supported-informix-platforms-and-versions) IBM Informix-platformok és verziók (az ebben a témakörben).

Az összekötő támogatja a következő adatbázis-műveletek:

* Lista adatbázistáblák
* SELECT használatával egy sor olvasása
* VÁLASSZA a minden sorok olvasása
* Adjon hozzá egy sort INSERT használatával
* Az ALTER UPDATE használatával egy sor
* Távolítsa el az egyik sor a DELETE használata

Ez a témakör bemutatja, hogyan az összekötő használatára, a folyamat adatbázis-műveletet a logikai alkalmazás.

Logic Apps szolgáltatással kapcsolatos további tudnivalókért lásd: [hozzon létre egy logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="available-actions"></a>Rendelkezésre álló műveletek
Ez az összekötő támogatja a következő logic app-műveletek:

* Getables
* GetRow
* GetRows hívás
* InsertRow
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>Táblák listázása
Minden olyan művelet egy logikai alkalmazás létrehozásának számos, a Microsoft Azure-portálon keresztül végrehajtott lépések áll.

A logikai alkalmazásban is hozzáadhat egy műveletet lista táblák Informix-adatbázisban. Ez a művelet arra utasítja az összekötő egy Informix-schema utasításban, mint például feldolgozni `CALL SYSIBM.SQLTABLES`.

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása
1. Az a **Azure indítsa el a tábla**válassza **+** (plusz) **Web + mobil**, majd **logikai alkalmazás**.
2. Adja meg a **neve**, például `InformixgetTables`, **előfizetés**, **erőforráscsoport**, **hely**, és **App Service-ben Csomag**. Válassza ki **rögzítés az irányítópulton**, majd válassza ki **létrehozás**.

### <a name="add-a-trigger-and-action"></a>Az eseményindító és művelet hozzáadása
1. Az a **Logic Apps Designerben**válassza **üres LogicApp** a a **sablonok** listája.
2. Az a **eseményindítók** listáról válassza ki **ismétlődési**. 
3. Az a **ismétlődési** eseményindító területén válassza **szerkesztése**, jelölje be **gyakorisága** válassza ki a legördülő **nap**, majd válassza ki  **Intervallum** típusra **7**.  
4. Válassza ki a **+ új lépés** mezőbe, majd válassza ki **művelet hozzáadása**.
5. Az a **műveletek** listázása, írja be a **informix** a a **további műveletek keresése** szerkesztési mezőhöz, és válassza ki **Informix - Get-táblák (előzetes verzió)**.
   
   ![](./media/connectors-create-api-informix/InformixconnectorActions.png)  
6. Az a **Informix - Get-táblák** konfigurációs panelen válassza **jelölőnégyzet** engedélyezéséhez **kapcsolódás helyszíni adatátjárón keresztül**. Figyelje meg, hogy a helyszínen a felhőből módosítsa a beállításokat.
   
   * Írja be az értéket **kiszolgáló**, cím vagy alias kettőspont portszám formájában. Írja be például a következőt: `ibmserver01:9089`.
   * Írja be az értéket **adatbázis**. Írja be például a következőt: `nwind`.
   * Válassza ki az értéket **hitelesítési**. Válassza ki például **alapszintű**.
   * Írja be az értéket **felhasználónév**. Írja be például a következőt: `informix`.
   * Írja be az értéket **jelszó**. Írja be például a következőt: `Password1`.
   * Válassza ki az értéket **átjáró**. Válassza ki például **datagateway01**.
7. Válassza ki **létrehozás**, majd válassza ki **mentése**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)
8. Az a **InformixgetTables** panelen, belül a **minden Futtatás** alatt **összefoglalás**, válassza ki az első felsorolt elemet (a legutóbbi futtatás).
9. Az a **a logikai alkalmazás futtatásának** panelen válassza ki **Futtatás részletei**. Belül a **művelet** listáról válassza ki **Get_tables**. Tekintse meg az értékét **állapot**, kell lennie, amely **sikeres**. Válassza ki a **bemenetek hivatkozása** a bemeneti adatok megtekintéséhez. Válassza ki a **kimenetek hivatkozása**, és megtekintheti a kimeneteket; amelynek tartalmaznia kell a táblák listáját.
   
   ![](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>A kapcsolatok létrehozása
Ez az összekötő támogatja a kapcsolatok a helyszíni adatbázis és a felhőben használatával a következő csatlakozási tulajdonságokat. 

| Tulajdonság | Leírás |
| --- | --- |
| kiszolgáló |Kötelező. A TCP/IP-cím vagy aliast IPv4 vagy IPv6 formátumban, majd (pontosvesszővel tagolt) szerint a TCP/IP-port számát jelölő karakterláncot fogad el. |
| adatbázis |Kötelező. Egy karakterláncértéket, amely a DRDA relációs adatbázis nevét (RDBNAM) fogad el. Informix-128 bájt-karakterláncot fogad el (adatbázis-IBM Informix-adatbázis neve (adatbázisnév) nevezik). |
| hitelesítés |Választható. Fogadja el a lista elem értéke, egyszerű vagy Windows (kerberos). |
| felhasználónév |Kötelező. Egy karakterlánc értéket fogad el. |
| jelszó |Kötelező. Egy karakterlánc értéket fogad el. |
| átjáró |Kötelező. Egy lista elem értéket, a helyszíni átjáró az tárolócsoportot a Logic Apps használatával meghatározott jelölő fogad el. |

## <a name="create-the-on-premises-gateway-connection"></a>A helyszíni átjáró kapcsolat létrehozása
Ez az összekötő hozzáférhet a helyszíni átjáró használatával helyszíni Informix-adatbázishoz. Átjáró témakörök további információt. 

1. Az a **átjárók** konfigurációs panelen válassza **jelölőnégyzet** engedélyezéséhez **Connect Gateway**. Tekintse meg a beállításokat, módosítsa a felhőből a helyszínen.
2. Írja be az értéket **kiszolgáló**, cím vagy alias kettőspont portszám formájában. Írja be például a következőt: `ibmserver01:9089`.
3. Írja be az értéket **adatbázis**. Írja be például a következőt: `nwind`.
4. Válassza ki az értéket **hitelesítési**. Válassza ki például **alapszintű**.
5. Írja be az értéket **felhasználónév**. Írja be például a következőt: `informix`.
6. Írja be az értéket **jelszó**. Írja be például a következőt: `Password1`.
7. Válassza ki az értéket **átjáró**. Válassza ki például **datagateway01**.
8. Válassza ki **létrehozás** folytatásához. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>A felhő-kapcsolat létrehozása
Ez az összekötő felhő Informix-adatbázis férhetnek hozzá. 

1. Az a **átjárók** konfiguráció panelen hagyja a **jelölőnégyzet** le van tiltva (rákattintás előtt) **Connect Gateway**. 
2. Írja be az értéket **kapcsolatnevet**. Írja be például a következőt: `hisdemo2`.
3. Írja be az értéket **Informix-kiszolgálónév**, cím vagy alias kettőspont portszám formájában. Írja be például a következőt: `hisdemo2.cloudapp.net:9089`.
4. Írja be az értéket **Informix-adatbázis neve**. Írja be például a következőt: `nwind`.
5. Írja be az értéket **felhasználónév**. Írja be például a következőt: `informix`.
6. Írja be az értéket **jelszó**. Írja be például a következőt: `Password1`.
7. Válassza ki **létrehozás** folytatásához. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>VÁLASSZA a minden sorok beolvasása
Létrehozhat egy logikaialkalmazás-művelet az Informix-tábla összes sorának beolvasása. Ez a művelet arra utasítja az Informix-SELECT utasítás például feldolgozni az összekötő `SELECT * FROM AREA`.

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása
1. Az a **Azure indítsa el a tábla**válassza **+** (plusz) **Web + mobil**, majd **logikai alkalmazás**.
2. Adja meg a **neve** (például) "**InformixgetRows**"), **előfizetés**, **erőforráscsoport**, **hely**, és **App Service-csomag**. Válassza ki **rögzítés az irányítópulton**, majd válassza ki **létrehozás**.

### <a name="add-a-trigger-and-action"></a>Az eseményindító és művelet hozzáadása
1. Az a **Logic Apps Designerben**válassza **üres LogicApp** a a **sablonok** listája.
2. Az a **eseményindítók** listáról válassza ki **ismétlődési**. 
3. Az a **ismétlődési** eseményindító területén válassza **szerkesztése**, jelölje be **gyakorisága** válassza ki a legördülő **nap**, majd válassza ki  **Intervallum** típusra **7**. 
4. Válassza ki a **+ új lépés** mezőbe, majd válassza ki **művelet hozzáadása**.
5. Az a **műveletek** listázása, írja be a **informix** a a **további műveletek keresése** szerkesztési mezőhöz, és válassza ki **Informix - sorok beolvasása (előzetes verzió)**.
6. Az a **(előzetes verzió) sorok beolvasása** műveletet, válassza **kapcsolat módosítása**.
7. Az a **kapcsolatok** konfigurációs panelen válassza **új létrehozása**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorNewConnection.png)
8. Az a **átjárók** konfiguráció panelen hagyja a **jelölőnégyzet** le van tiltva (rákattintás előtt) **Connect Gateway**.
   
   * Írja be az értéket **kapcsolatnevet**. Írja be például a következőt: `HISDEMO2`.
   * Írja be az értéket **Informix-kiszolgálónév**, cím vagy alias kettőspont portszám formájában. Írja be például a következőt: `HISDEMO2.cloudapp.net:9089`.
   * Írja be az értéket **Informix-adatbázis neve**. Írja be például a következőt: `NWIND`.
   * Írja be az értéket **felhasználónév**. Írja be például a következőt: `informix`.
   * Írja be az értéket **jelszó**. Írja be például a következőt: `Password1`.
9. Válassza ki **létrehozás** folytatásához.
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)
10. Az a **táblanév** listáról válassza ki a **lefelé mutató nyíl**, majd válassza ki **terület**.
11. Bejelölheti **speciális beállítások megjelenítése** lekérdezési beállítások megadásához.
12. Kattintson a **Mentés** gombra. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsTableName.png)
13. Az a **InformixgetRows** panelen, belül a **minden Futtatás** alatt **összefoglalás**, válassza ki az első felsorolt elemet (a legutóbbi futtatás).
14. Az a **a logikai alkalmazás futtatásának** panelen válassza ki **Futtatás részletei**. Belül a **művelet** listáról válassza ki **Get_rows**. Tekintse meg az értékét **állapot**, kell lennie, amely **sikeres**. Válassza ki a **bemenetek hivatkozása** a bemeneti adatok megtekintéséhez. Válassza ki a **kimenetek hivatkozása**, és megtekintheti a kimeneteket; amelynek tartalmaznia kell a sorok listáját.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>Adjon hozzá egy sort INSERT használatával
Létrehozhat egy logikaialkalmazás-művelet hozzáadása egy sort egy Informix-táblában. Ez a művelet arra utasítja az összekötő egy Informix INSERT utasítás például feldolgozni `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása
1. Az a **Azure indítsa el a tábla**válassza **+** (plusz) **Web + mobil**, majd **logikai alkalmazás**.
2. Adja meg a **neve**, például `InformixinsertRow`, **előfizetés**, **erőforráscsoport**, **hely**, és **App Service-ben Csomag**. Válassza ki **rögzítés az irányítópulton**, majd válassza ki **létrehozás**.

### <a name="add-a-trigger-and-action"></a>Az eseményindító és művelet hozzáadása
1. Az a **Logic Apps Designerben**válassza **üres LogicApp** a a **sablonok** listája.
2. Az a **eseményindítók** listáról válassza ki **ismétlődési**. 
3. Az a **ismétlődési** eseményindító területén válassza **szerkesztése**, jelölje be **gyakorisága** válassza ki a legördülő **nap**, majd válassza ki  **Intervallum** típusra **7**. 
4. Válassza ki a **+ új lépés** mezőbe, majd válassza ki **művelet hozzáadása**.
5. Az a **műveletek** listázása, írja be a **informix** a a **további műveletek keresése** szerkesztési mezőhöz, és válassza ki **Informix - sor beszúrása (előzetes verzió)**.
6. Az a **(előzetes verzió) sorok beolvasása** műveletet, válassza **kapcsolat módosítása**. 
7. Az a **kapcsolatok** konfiguráció panelen válassza ki, hogy válasszon ki egy kapcsolatot. Válassza ki például **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Az a **táblanév** listáról válassza ki a **lefelé mutató nyíl**, majd válassza ki **terület**.
9. Adja meg az értékeket (lásd a piros csillag) minden szükséges oszlopot. Írja be például `99999` a **AREAID**, típus `Area 99999`, és írja be `102` a **REGIONID**. 
10. Kattintson a **Mentés** gombra.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowValues.png)
11. Az a **InformixinsertRow** panelen, belül a **minden Futtatás** alatt **összefoglalás**, válassza ki az első felsorolt elemet (a legutóbbi futtatás).
12. Az a **a logikai alkalmazás futtatásának** panelen válassza ki **Futtatás részletei**. Belül a **művelet** listáról válassza ki **Get_rows**. Tekintse meg az értékét **állapot**, kell lennie, amely **sikeres**. Válassza ki a **bemenetek hivatkozása** a bemeneti adatok megtekintéséhez. Válassza ki a **kimenetek hivatkozása**, és megtekintheti a kimeneteket; amelynek tartalmaznia kell az új sor.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>Segítségével válassza ki egy sort beolvasni
Létrehozhat egy Informix-táblában egy sor beolvasása egy logikaialkalmazás-művelet. Ez a művelet arra utasítja az összekötő egy Informix, válassza ki utasítás például feldolgozni `SELECT FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása
1. Az a **Azure indítsa el a tábla**válassza **+** (plusz) **Web + mobil**, majd **logikai alkalmazás**.
2. Adja meg a **neve**, például `InformixgetRow`, **előfizetés**, **erőforráscsoport**, **hely**, és **App Service-ben Csomag**. Válassza ki **rögzítés az irányítópulton**, majd válassza ki **létrehozás**.

### <a name="add-a-trigger-and-action"></a>Az eseményindító és művelet hozzáadása
1. Az a **Logic Apps Designerben**válassza **üres LogicApp** a a **sablonok** listája.
2. Az a **eseményindítók** listáról válassza ki **ismétlődési**. 
3. Az a **ismétlődési** eseményindító területén válassza **szerkesztése**, jelölje be **gyakorisága** válassza ki a legördülő **nap**, majd válassza ki  **Intervallum** típusra **7**. 
4. Válassza ki a **+ új lépés** mezőbe, majd válassza ki **művelet hozzáadása**.
5. Az a **műveletek** listázása, írja be a **informix** a a **további műveletek keresése** szerkesztési mezőhöz, és válassza ki **Informix - sorok beolvasása (előzetes verzió)**.
6. Az a **(előzetes verzió) sorok beolvasása** műveletet, válassza **kapcsolat módosítása**. 
7. Az a **kapcsolatok** konfigurációk panelen válassza a válasszon ki egy létező kapcsolatot. Válassza ki például **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Az a **táblanév** listáról válassza ki a **lefelé mutató nyíl**, majd válassza ki **terület**.
9. Adja meg az értékeket (lásd a piros csillag) minden szükséges oszlopot. Írja be például `99999` a **AREAID**. 
10. Bejelölheti **speciális beállítások megjelenítése** lekérdezési beállítások megadásához.
11. Kattintson a **Mentés** gombra. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowValues.png)
12. Az a **InformixgetRow** panelen, belül a **minden Futtatás** alatt **összefoglalás**, válassza ki az első felsorolt elemet (a legutóbbi futtatás).
13. Az a **a logikai alkalmazás futtatásának** panelen válassza ki **Futtatás részletei**. Belül a **művelet** listáról válassza ki **Get_rows**. Tekintse meg az értékét **állapot**, kell lennie, amely **sikeres**. Válassza ki a **bemenetek hivatkozása** a bemeneti adatok megtekintéséhez. Válassza ki a **kimenetek hivatkozása**, és megtekintheti a kimeneteket; amelynek tartalmaznia kell a sor.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>FRISSÍTÉS használatával egy sor módosítása
Létrehozhat egy logikai alkalmazás műveletet, amely egy sort egy Informix-táblában. Ez a művelet arra utasítja az összekötő Informix UPDATE utasításban, mint például feldolgozni `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`.

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása
1. Az a **Azure indítsa el a tábla**válassza **+** (plusz) **Web + mobil**, majd **logikai alkalmazás**.
2. Adja meg a **neve**, például `InformixupdateRow`, **előfizetés**, **erőforráscsoport**, **hely**, és **App Service-ben Csomag**. Válassza ki **rögzítés az irányítópulton**, majd válassza ki **létrehozás**.

### <a name="add-a-trigger-and-action"></a>Az eseményindító és művelet hozzáadása
1. Az a **Logic Apps Designerben**válassza **üres LogicApp** a a **sablonok** listája.
2. Az a **eseményindítók** listáról válassza ki **ismétlődési**. 
3. Az a **ismétlődési** eseményindító területén válassza **szerkesztése**, jelölje be **gyakorisága** válassza ki a legördülő **nap**, majd válassza ki  **Intervallum** típusra **7**. 
4. Válassza ki a **+ új lépés** mezőbe, majd válassza ki **művelet hozzáadása**.
5. Az a **műveletek** listázása, írja be a **informix** a a **további műveletek keresése** szerkesztési mezőhöz, és válassza ki **Informix - sor frissítése (előzetes verzió)**.
6. Az a **(előzetes verzió) sorok beolvasása** műveletet, válassza **kapcsolat módosítása**. 
7. Az a **kapcsolatok** konfigurációk panelen válassza a válasszon ki egy létező kapcsolatot. Válassza ki például **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Az a **táblanév** listáról válassza ki a **lefelé mutató nyíl**, majd válassza ki **terület**.
9. Adja meg az értékeket (lásd a piros csillag) minden szükséges oszlopot. Írja be például `99999` a **AREAID**, típus `Updated 99999`, és írja be `102` a **REGIONID**. 
10. Kattintson a **Mentés** gombra. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowValues.png)
11. Az a **InformixupdateRow** panelen, belül a **minden Futtatás** alatt **összefoglalás**, válassza ki az első felsorolt elemet (a legutóbbi futtatás).
12. Az a **a logikai alkalmazás futtatásának** panelen válassza ki **Futtatás részletei**. Belül a **művelet** listáról válassza ki **Get_rows**. Tekintse meg az értékét **állapot**, kell lennie, amely **sikeres**. Válassza ki a **bemenetek hivatkozása** a bemeneti adatok megtekintéséhez. Válassza ki a **kimenetek hivatkozása**, és megtekintheti a kimeneteket; amelynek tartalmaznia kell az új sor.
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>Távolítsa el az egyik sor a DELETE használata
Létrehozhat egy logikaialkalmazás-művelet eltávolítja egy sort egy Informix-táblában. Ez a művelet arra utasítja az összekötő egy Informix DELETE utasítás például feldolgozni `DELETE FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása
1. Az a **Azure indítsa el a tábla**válassza **+** (plusz) **Web + mobil**, majd **logikai alkalmazás**.
2. Adja meg a **neve**, például `InformixdeleteRow`, **előfizetés**, **erőforráscsoport**, **hely**, és **App Service-ben Csomag**. Válassza ki **rögzítés az irányítópulton**, majd válassza ki **létrehozás**.

### <a name="add-a-trigger-and-action"></a>Az eseményindító és művelet hozzáadása
1. Az a **Logic Apps Designerben**válassza **üres LogicApp** a a **sablonok** listája.
2. Az a **eseményindítók** listáról válassza ki **ismétlődési**. 
3. Az a **ismétlődési** eseményindító területén válassza **szerkesztése**, jelölje be **gyakorisága** válassza ki a legördülő **nap**, majd válassza ki  **Intervallum** típusra **7**. 
4. Válassza ki a **+ új lépés** mezőbe, majd válassza ki **művelet hozzáadása**.
5. Az a **műveletek** listázása, írja be a **informix** a a **további műveletek keresése** szerkesztési mezőhöz, és válassza ki **Informix - sor törlése (előzetes verzió)**.
6. Az a **(előzetes verzió) sorok beolvasása** műveletet, válassza **kapcsolat módosítása**. 
7. Az a **kapcsolatok** konfigurációk panelen válasszon ki egy létező kapcsolatot. Válassza ki például **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Az a **táblanév** listáról válassza ki a **lefelé mutató nyíl**, majd válassza ki **terület**.
9. Adja meg az értékeket (lásd a piros csillag) minden szükséges oszlopot. Írja be például `99999` a **AREAID**. 
10. Kattintson a **Mentés** gombra. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowValues.png)
11. Az a **InformixdeleteRow** panelen, belül a **minden Futtatás** alatt **összefoglalás**, válassza ki az első felsorolt elemet (a legutóbbi futtatás).
12. Az a **a logikai alkalmazás futtatásának** panelen válassza ki **Futtatás részletei**. Belül a **művelet** listáról válassza ki **Get_rows**. Tekintse meg az értékét **állapot**, kell lennie, amely **sikeres**. Válassza ki a **bemenetek hivatkozása** a bemeneti adatok megtekintéséhez. Válassza ki a **kimenetek hivatkozása**, és megtekintheti a kimeneteket; amelynek tartalmaznia kell a törölt sor.
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="supported-informix-platforms-and-versions"></a>Támogatott Informix-platformok és verziók
Ez az összekötő támogatja az alábbi IBM Informix-verziók, elosztott relációs adatbázis architektúra (DRDA) ügyfélkapcsolatok támogatásához konfigurálásakor.

* IBM Informix 12.1
* IBM Informix 11.7

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Megtekintheti a valamennyi eseményindítót és műveletet a swaggerben meghatározott, és emellett a korlátozott a [összekötő részletei](/connectors/informix/). 

## <a name="next-steps"></a>További lépések
[Hozzon létre egy logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ismerje meg az egyéb elérhető összekötők a Logic Apps, a [API-k listája](apis-list.md).

