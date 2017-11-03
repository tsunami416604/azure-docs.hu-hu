---
title: "Adja hozzá az Oracle-adatbázishoz összekötő az Azure Logic Apps |} Microsoft Docs"
description: "A logikai alkalmazás az Oracle-adatbázishoz összekötő használatára"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/29/2017
ms.author: mandia; ladocs
ms.openlocfilehash: cc64441617eb5e7d5e70c1cf5c491a672428bc51
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-oracle-database-connector"></a>Az Oracle-adatbázishoz összekötő az első lépései

Az Oracle adatbázis-összekötő segítségével hoz létre a meglévő adatbázis adatokat használó szervezeti munkafolyamatok. Az összekötő telepítve Oracle-adatbázishoz csatlakozhat helyszíni Oracle-adatbázishoz, vagy egy Azure virtuális gépen. Ez az összekötő segítségével:

* A munkafolyamat egy új ügyfél ügyfelek adatbázishoz, vagy frissítésétől egészen a rendelések adatbázisban egy rendelés hozhat létre.
* Műveletek segítségével az adatok egy sor lekéréséhez, új sor beszúrására, és akkor is törli. Például egy rekord létrehozásakor a Dynamics CRM Online (trigger), majd sor beszúrása az Oracle-adatbázisban (művelet). 

Ez a témakör bemutatja, hogyan az Oracle-adatbázishoz összekötő használatára a logikai alkalmazás.

## <a name="prerequisites"></a>Előfeltételek

* Oracle támogatott verziók: 
    * Oracle 9-es és újabb verziók
    * Oracle ügyfélszoftvert 8.1.7-es vagy újabb

* Telepítse a helyszíni adatátjáró. [A helyszíni adatokhoz csatlakozva a logic Apps alkalmazásokból](../logic-apps/logic-apps-gateway-connection.md) lépéseit sorolja fel. Az átjáró egy helyszíni Oracle-adatbázishoz való kapcsolódáshoz szükséges, vagy egy Azure virtuális Gépen az Oracle-adatbázis telepítve. 

    > [!NOTE]
    > Az a helyszíni átjáró egy hidat és biztonságos helyszíni (adatokat, amely nem a felhőben) és a logic Apps alkalmazások közötti adatátvitel tartalmaz. Több szolgáltatásra, és több adatforrást ugyanahhoz az átjáróhoz használható. Igen csak szükség lehet az átjáró egyszer telepítéséhez.

* Az Oracle-ügyfél telepítéséhez a számítógépen, amelyre telepítette a helyszíni adatátjáró. Győződjön meg arról, a 64 bites Oracle-adatszolgáltatóban telepítéséhez a .NET-hez, az Oracle:  

  [64 bites ODAC Windows x64 12c kiadás 4 (12.1.0.2.4)](http://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Az Oracle-ügyfél nincs telepítve, ha hiba lép fel, amikor megpróbálja létrehozni, vagy a kapcsolat. Tekintse meg a közös hibákat ebben a témakörben.


## <a name="add-the-connector"></a>Az összekötő hozzáadása

> [!IMPORTANT]
> Ez az összekötő nem rendelkezik a eseményindítókat. Csak a műveleteket tartalmaz. Ezért a logikai alkalmazás létrehozásakor hozzá indítsa el a logikai alkalmazás, például egy másik eseményindító **ütemezés - ismétlődési**, vagy **kérelem / válasz - válasz**. 

1. Az a [Azure-portálon](https://portal.azure.com), egy üres logikai alkalmazás létrehozása.

2. A Logic Apps alkalmazást elején, jelölje ki a **kérelem / válasz - kérelem** eseményindító: 

    ![](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. Kattintson a **Mentés** gombra. Mentésekor, a rendszer automatikusan előállítja a kérelem URL-CÍMÉT. 

4. Válassza ki **új lépés**, és válassza ki **művelet hozzáadása**. Írja be a `oracle` a rendelkezésre álló műveletek megjelenítéséhez: 

    ![](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > Ez egyben a leggyorsabban úgy tudja megtekinteni, az eseményindítók és műveletek a csatlakozókat érhető el. Írja be az összekötő neve részeként például `oracle`. A Tervező bármely eseményindítók és műveletek sorolja fel. 

5. Válasszon egyet a a műveletek, például a **Oracle adatbázis - Get sor**. Válassza ki **keresztül, a helyszíni adatátjáró**. Adja meg az Oracle-kiszolgáló nevét, a hitelesítési módszert, a felhasználónév, a jelszó, és válassza ki az átjáró:

    ![](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. Miután csatlakozott, válasszon ki egy táblát a listából, és adja meg a Sorazonosító a táblában. Meg kell tudni, hogy a tábla azonosítója. Ha nem ismeri, a Oracle adatbázis rendszergazdájától, és eredményének beolvasása `select * from yourTableName`. Ez lehetővé teszi a kell folytatnia azonosításra alkalmas adatokat.

    A következő példában feladatadatok egy az emberi erőforrások adatbázisból ad vissza: 

    ![](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. A következő lépésben a munkafolyamat létrehozásához használhatja a többi összekötőt bármelyikét. Ha azt szeretné, az Oracle beolvasásakor adatok tesztelésére, majd küldhet magának egy e-mailek küldése e-mailek összekötők, például Office 365 vagy Gmailes egyikével Oracle-adatokkal. A dinamikus jogkivonatok az Oracle-táblából az lehetővé teszi a `Subject` és `Body` az e-mail:

    ![](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **Mentés** a Logic Apps alkalmazást, és válassza **futtatása**. A Tervező bezárása, és tekintse meg az állapot fut előzményeit. Ha sikertelen, jelölje ki a hibás üzenetek. A Tervező megnyílik, és mutatja meg, amely lépés sikertelen volt, és a hiba információk is láthatók. Ha ez sikeres, akkor a hozzáadott információkat e-mailt kell kapnia.


### <a name="workflow-ideas"></a>Munkafolyamat ötletek

* Szeretné a #oracle hashtaggel történő figyelése és a Twitter-üzeneteket be egy adatbázist, így kérdezhetők le, és más alkalmazásokban használt. A logikai alkalmazás, adja hozzá a `Twitter - When a new tweet is posted` indítható el, és írja be a **#oracle** hashtaggel történő. Adja hozzá a `Oracle Database - Insert row` művelet, és válassza ki a táblázatban:

    ![](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* Üzenetek küldése a Service Bus-üzenetsorba. Szeretné ezeket az üzeneteket, és azokat egy adatbázisban. A logikai alkalmazás, adja hozzá a `Service Bus - when a message is received in a queue` aktiválhatja, és jelölje ki a várólistát. Adja hozzá a `Oracle Database - Insert row` művelet, és válassza ki a táblázatban:

    ![](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>Gyakori hibák

#### <a name="error-cannot-reach-the-gateway"></a>**Hiba**: az átjáró nem érhető el.

**OK**: a helyszíni az adatátjáró nem tud csatlakozni a felhőben. 

**Megoldás**: Győződjön meg arról, hogy az átjáró fut a helyi számítógépen, amelyen telepítették őket, és, hogy kapcsolódni tud az internethez.  Azt javasoljuk, hogy nem telepíti az átjáró a számítógépre, amely ki van kapcsolva vagy alvó. A helyszíni átjáró szolgáltatás (PBIEgwService) is újraindíthatja.

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-please-visit-httpsgomicrosoftcomfwlinkplinkid272376httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**Hiba**: A használt szolgáltató elavult: "System.Data.OracleClient szükséges verziójú Oracle ügyfélszoftvert 8.1.7-es vagy újabb.". Látogasson el a [https://go.microsoft.com/fwlink/p/?LinkID=272376](https://go.microsoft.com/fwlink/p/?LinkID=272376) a hivatalos szolgáltató telepítéséhez.

**OK**: az Oracle ügyfél SDK a helyszíni adatok átjárót futtató gépen nincs telepítve.  

**Megoldási**: Töltse le és telepítse az Oracle ügyfél SDK az helyszíni átjáró ugyanazon a számítógépen.

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**Hiba**: "[Tablename]" tábla nem definiál bármely Kulcsoszlopok

**OK**: A táblának nincs elsődleges kulcs.  

**Megoldási**: az Oracle-adatbázishoz connector megköveteli, hogy a tábla elsődleges kulcsként megadott oszlop használható.

#### <a name="currently-not-supported"></a>Jelenleg nem támogatott

* Nézetek és tárolt eljárások 
* A tábla az összetett kulcsok
* Táblák egymásba ágyazott objektumtípusok
 
## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Bármely eseményindítók és a swagger definiált műveletek megtekintése, és semmilyen határnak a Lásd még: a [connector részleteket](/connectors/oracle/). 

## <a name="get-some-help"></a>Segítséget

A [Azure Logic Apps-fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) mutassa kérdései vannak, kérdést, és tekintse meg a többi Logic Apps felhasználók tevékenységeit. 

Növelheti Logic Apps alkalmazások és összekötők szavazás, és a következő ötletek elküldésével [http://aka.ms/logicapps-wish](http://aka.ms/logicapps-wish). 


## <a name="next-steps"></a>Következő lépések
[Logikai alkalmazás létrehozása](../logic-apps/logic-apps-create-a-logic-app.md), és vizsgálja meg a rendelkezésre álló összekötők Logic Apps, az [API-k lista](apis-list.md).
