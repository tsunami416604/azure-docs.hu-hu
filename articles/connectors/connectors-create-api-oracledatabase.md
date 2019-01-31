---
title: Oracle-adatbázishoz – Azure Logic Apps |} A Microsoft Docs
description: Helyezze be, és az Oracle Database REST API-k és az Azure Logic Apps-rekordok kezelése
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 03/29/2017
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 9d606dd4faf324d68e4365eae0802de2384471ab
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55295357"
---
# <a name="get-started-with-the-oracle-database-connector"></a>Az Oracle Database-összekötő használatának első lépései

Az Oracle Database-összekötő segítségével, létrehozhat olyan adatokat a meglévő adatbázisban szervezeti munkafolyamatok. Ez az összekötő az Oracle Database telepítve csatlakozhat egy helyszíni Oracle-adatbázishoz, vagy egy Azure virtuális gépen. Ez az összekötő segítségével:

* Létrehozhatja a munkafolyamatot egy új ügyfél hozzáadásával ügyfelek adatbázishoz, vagy a rendelések-adatbázisban egy megrendelés frissítése.
* Műveletek használatával egy sor az adatok lekéréséhez, egy új sor beszúrásához, vagy akár törölhetik. Például egy rekord létrehozásakor a Dynamics CRM Online (eseményindító), majd sor beszúrása egy Oracle Database (a műveletet). 

Ez a cikk bemutatja, hogyan használhatja az Oracle Database-összekötő a logikai alkalmazás.

## <a name="prerequisites"></a>Előfeltételek

* Támogatott Oracle-verziók: 
    * Oracle 9 és újabb verziók
    * Oracle ügyfélszoftver 8.1.7-es és újabb verziók

* A helyszíni adatátjáró telepítése. [Csatlakozás helyszíni adatokhoz logikai alkalmazásokból](../logic-apps/logic-apps-gateway-connection.md) felsorolja azokat a lépéseket. Az átjáró a helyszíni Oracle-adatbázishoz való csatlakozáshoz szükséges, vagy egy Azure virtuális gép, Oracle DB-vel telepítve. 

    > [!NOTE]
    > A helyszíni adatátjáró hídként működnek, és biztosít egy biztonságos adatátvitel helyszíni (, amely nem felhőbeli) adatok és a logic apps között. Az azonos átjáró több szolgáltatást, és több adatforrás használható. Ezért csak szükség lehet az átjáró telepítése után.

* Az Oracle-ügyfél telepítéséhez a számítógépen, amelyre telepítve van a helyszíni adatátjáró. Ügyeljen arra, hogy telepítse a 64 bites Oracle-adatszolgáltató a .NET-hez, az Oracle:  

  [64 bites ODAC 12c Release 4 (12.1.0.2.4) Windows x64](http://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Az Oracle-ügyfél nincs telepítve, ha hiba történik, amikor megpróbálja létrehozni vagy használni a kapcsolatot. A gyakori hibák a cikkben talál.


## <a name="add-the-connector"></a>Az összekötő hozzáadása

> [!IMPORTANT]
> Ez az összekötő nem tartozhat eseményindító. Csak a műveleteket tartalmaz. Ezért a logikai alkalmazást fog létrehozni, amikor egy másik eseményindító hozzáadása a logikai alkalmazás indítására **ütemezés – ismétlődés**, vagy **kérelem / válasz – válasz**. 

1. Az a [az Azure portal](https://portal.azure.com), hozzon létre egy üres logikai alkalmazást.

2. A logikai alkalmazás elején, jelölje be a **kérelem / válasz – kérelem** eseményindító: 

    ![](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. Kattintson a **Mentés** gombra. Amikor menti, a kérelem URL-cím automatikusan jön létre. 

4. Válassza ki **új lépés**, és válassza ki **művelet hozzáadása**. Írja be a `oracle` a rendelkezésre álló műveletek megjelenítéséhez: 

    ![](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > Ez akkor is a leggyorsabb mód az eseményindítók és minden olyan összekötő elérhető műveletek megtekintéséhez. Írja be az összekötő neve része például `oracle`. A Tervező bármely eseményindítók és műveletek listája. 

5. Válassza ki az egyik a műveletek, például **Oracle Database - Get-sor**. Válassza ki **kapcsolódás helyszíni adatátjárón keresztül**. Adja meg az Oracle-kiszolgáló neve, a hitelesítési módszer, a felhasználónév, a jelszó, és válassza ki az átjáró:

    ![](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. A csatlakozás után válasszon ki egy táblát a listából, és adja meg a Sorazonosító hozzá a táblához. Érdemes tudni az azonosító a táblába. Ha nem tudja, Oracle DB-rendszergazdához, és eredményének beolvasása `select * from yourTableName`. Ez lehetővé teszi a azonosításra alkalmas információkat kell folytatnia az eljárást.

    A következő példában a feladat visszaadott adat az emberi erőforrások adatbázisból: 

    ![](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. A következő lépésben segítségével bármely más összekötőt létrehozhatja a munkafolyamatot. Ha szeretné tesztelni Oracle adatainak lekérését, küldhet saját magának egy e-mailt az Oracle adatokkal küldése e-mailek összekötők, például Office 365- vagy Gmail egyikével. Az Oracle-tábla dinamikus jogkivonatok használatával hozhat létre a `Subject` és `Body` e-mail:

    ![](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **Mentés** a logikai alkalmazást, és válassza ki **futtatása**. A tervező bezárásához, és tekintse meg a futtatási előzmények az állapot. Ha sikertelen, jelölje ki a hibás üzenetek. A tervező megnyitása, és amely. lépés: nem sikerült, valamint azt, a hiba adatait jeleníti meg. Ha ez sikeres, majd küld egy e-mailt a hozzáadott információkkal.


### <a name="workflow-ideas"></a>A munkafolyamat ötleteit

* Szeretné figyelni a #oracle hashtaggel, és helyezi a tweeteket egy adatbázisban, így kérdezhető le, és más alkalmazásokban használt. Egy logikai alkalmazást, adja hozzá a `Twitter - When a new tweet is posted` aktiválhat, és adja meg a **#oracle** hashtaggel. Adja hozzá a `Oracle Database - Insert row` műveletet, és válassza ki a táblát:

    ![](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* Üzenetküldés a Service Bus-üzenetsorba. Szeretné ezeket az üzeneteket, és a egy adatbázisban helyezi őket. Egy logikai alkalmazást, adja hozzá a `Service Bus - when a message is received in a queue` aktiválhat, és válassza ki az üzenetsorba. Adja hozzá a `Oracle Database - Insert row` műveletet, és válassza ki a táblát:

    ![](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>Gyakori hibák

#### <a name="error-cannot-reach-the-gateway"></a>**Hiba**: Az átjáró nem érhető el.

**Ok**: A helyszíni adatátjáró nem sikerül csatlakozni a felhőhöz. 

**Kockázatcsökkentési**: Győződjön meg arról, hogy az átjáró fut-e a helyszíni gépen, amelyen telepítette azt, és hogy képes csatlakozni az internethez.  Azt javasoljuk, hogy az átjáró nem telepítése egy számítógépre, amely ki van kapcsolva vagy alvó állapotba lépni. A helyszíni adatátjáró szolgáltatás (PBIEgwService) is újraindíthatja.

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-see-httpsgomicrosoftcomfwlinkplinkid272376httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**Hiba**: A használt szolgáltató elavult: "System.Data.OracleClient szükséges verziójú Oracle ügyfélszoftvert 8.1.7-es vagy újabb.". Lásd: [ https://go.microsoft.com/fwlink/p/?LinkID=272376 ](https://go.microsoft.com/fwlink/p/?LinkID=272376) a hivatalos szolgáltató telepítéséhez.

**Ok**: Az Oracle ügyfél-SDK nincs telepítve a számítógépen, ahol a helyszíni adatátjáró fut.  

**Megoldás**: Töltse le, és az Oracle ügyfél-SDK telepítése a helyszíni adatátjáró ugyanazon a számítógépen.

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**Hiba**: A(z) [táblanév] tábla nem definiál kulcsoszlopot

**Ok**: A tábla nem rendelkezik minden olyan elsődleges kulcsot.  

**Megoldás**: Az Oracle Database-összekötő szükséges, hogy egy elsődlegeskulcs-oszlopot tartalmazó táblát használja.

#### <a name="currently-not-supported"></a>Jelenleg nem támogatott

* Nézetek 
* Minden olyan összetett kulcsokat tartalmazó tábla
* Beágyazott objektumtípusok táblákban
 
## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Megtekintheti a valamennyi eseményindítót és műveletet a swaggerben meghatározott, és emellett a korlátozott a [összekötő részletei](/connectors/oracle/). 

## <a name="get-some-help"></a>Segítséget kapjon

A [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) nagyszerű hely a kérdéseket, válaszolnak kérdéseire, és tekintse meg, mivel foglalkoznak az Logic Apps más felhasználói. 

Segíthet a Logic Apps és összekötők fejlesztését szavazás, és a javaslatait elküldésével [ https://aka.ms/logicapps-wish ](https://aka.ms/logicapps-wish). 


## <a name="next-steps"></a>További lépések
[Hozzon létre egy logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md), és ismerje meg az elérhető összekötők a Logic Apps alkalmazások [API-k listája](apis-list.md).
