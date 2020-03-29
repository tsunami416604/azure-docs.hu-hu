---
title: Csatlakozás Oracle-adatbázishoz
description: Rekordok beszúrása és kezelése Oracle Database REST API-kkal és Azure Logic Apps alkalmazásokkal
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/29/2017
tags: connectors
ms.openlocfilehash: 99abd48bde97c2a2e085688cdfbb365e5e4cfd56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789426"
---
# <a name="get-started-with-the-oracle-database-connector"></a>Első lépések az Oracle Database összekötővel

Az Oracle Database összekötő használatával olyan szervezeti munkafolyamatokat hozhat létre, amelyek a meglévő adatbázis ban adatokat használnak. Ez az összekötő csatlakozhat egy helyszíni Oracle-adatbázishoz, vagy egy Azure virtuális géphez, amelyen telepítve van az Oracle Database. Ezzel a csatlakozóval a következőkre tehet:

* A munkafolyamatot úgy építheti ki, hogy új ügyfelet ad hozzá egy ügyféladatbázishoz, vagy frissít egy rendelési adatbázisban lévő rendelést.
* A műveletek segítségével lekaphat egy adatsort, új sort szúrhat be, és akár törölheti is. Ha például egy rekordot létrehoz a Dynamics CRM Online programban (eseményindító), akkor szúrjon be egy sort egy Oracle Database (művelet) sorába. 

Ez a cikk bemutatja, hogyan használhatja az Oracle Database-összekötőt egy logikai alkalmazásban.

## <a name="prerequisites"></a>Előfeltételek

* Támogatott Oracle-verziók: 
    * Oracle 9-es és újabb verziók
    * 8.1.7-es vagy újabb Oracle-ügyfélszoftver

* Telepítse a helyszíni adatátjárót. [A helyszíni adatokhoz a logikai alkalmazásokból származó](../logic-apps/logic-apps-gateway-connection.md) adatok listája a lépéseket. Az átjáró szükséges egy helyszíni Oracle-adatbázishoz vagy egy Azure-beli virtuális géphez való csatlakozáshoz, amelyen telepítve van az Oracle DB. 

    > [!NOTE]
    > A helyszíni adatátjáró hídként működik, és biztonságos adatátvitelt biztosít a helyszíni adatok (a nem a felhőben lévő adatok) és a logikai alkalmazások között. Ugyanaz az átjáró több szolgáltatással és több adatforrással is használható.Így előfordulhat, hogy csak egyszer kell telepítenie az átjárót.

* Telepítse az Oracle Client alkalmazást arra a számítógépre, amelyen a helyszíni adatátjárót telepítette.Mindenképpen telepítse a 64 bites Oracle Data Provider for .NET szolgáltatót az Oracle-től:  

  [64 bites ODAC 12c 4-es kiadás (12.1.0.2.4) Windows x64 rendszerhez](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Ha az Oracle ügyfél nincs telepítve, hiba történik a kapcsolat létrehozásakor vagy használatakor. Tekintse meg a cikk gyakori hibáit.


## <a name="add-the-connector"></a>Az összekötő hozzáadása

> [!IMPORTANT]
> Ez az összekötő nem rendelkezik eseményindítókkal. Csak akciói vannak. Így amikor létrehozza a logikai alkalmazást, adjon hozzá egy másik eseményindítót a logikai alkalmazás elindításához, például **a Schedule - Recurrence**vagy a Request / Response - **Response**. 

1. Az [Azure Portalon](https://portal.azure.com)hozzon létre egy üres logikai alkalmazást.

2. A logikai alkalmazás kezdetén válassza a **Kérés/Válasz – Kéréseseményindító** t: 

    ![](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. Kattintson a **Mentés** gombra. Mentéskor a rendszer automatikusan létrehoz egy kérelem URL-címét. 

4. Jelölje ki az **Új lépés**, majd a **Művelet hozzáadása** lehetőséget. Írja `oracle` be a rendelkezésre álló műveletek megtekintéséhez: 

    ![](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > Ez is a leggyorsabb módja annak, hogy az eseményindítók és műveletek bármely összekötők elérhető. Írja be az összekötő nevének `oracle`egy részét, például . A tervező felsorolja az eseményindítókat és a műveleteket. 

5. Válasszon egyet a műveletek közül, például **oracle adatbázis – get sor**. Válassza a **Csatlakozás a helyszíni adatátjárón keresztül**lehetőséget. Adja meg az Oracle kiszolgáló nevét, hitelesítési módját, felhasználónevét, jelszavát, és válassza ki az átjárót:

    ![](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. Miután csatlakozott, jelöljön ki egy táblázatot a listából, és adja meg a tábla sorazonosítóját. Ismernie kell a tábla azonosítóját. Ha nem tudja, forduljon az Oracle DB rendszergazdájához, és a kimenetet a alkalmazásból `select * from yourTableName`kapja meg. Ez megadja a folytatáshoz szükséges azonosítható információkat.

    A következő példában a feladatadatok at egy humánerőforrás-adatbázisból adja vissza a rendszer: 

    ![](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. Ebben a következő lépésben a munkafolyamat létrehozásához használhatja a többi összekötőt. Ha tesztelni szeretné az Oracle-től származó adatok beszerzését, akkor küldjön magának egy e-mailt az Oracle-adatokkal az egyik küldési e-mail-összekötő, például az Office 365 vagy a Gmail használatával. Használja az Oracle tábla dinamikus tokenjeit `Body` az e-mailek és az `Subject` e-mailek létrehozásához:

    ![](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **Mentse** a logikai alkalmazást, és válassza a **Futtatás lehetőséget.** Zárja be a tervezőt, és tekintse meg az állapot futtatási előzményeit. Ha nem sikerül, jelölje ki a sikertelen üzenetsort. Megnyílik a tervező, és megmutatja, hogy melyik lépés nem sikerült, és megjeleníti a hibainformációkat is. Ha ez sikerül, akkor meg kell kapnia egy e-mailt a hozzáadott információkkal.


### <a name="workflow-ideas"></a>Munkafolyamat-ötletek

* Figyelni szeretné a #oracle hashtaget, és a tweeteket egy adatbázisba szeretné helyezni, hogy lekérdezhetők legyenek, és más alkalmazásokban is használhatók legyenek. Egy logikai alkalmazásban `Twitter - When a new tweet is posted` adja hozzá az eseményindítót, és adja meg a **#oracle** hashtag. Ezután adja `Oracle Database - Insert row` hozzá a műveletet, és jelölje ki a táblázatot:

    ![](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* Az üzenetek et a service bus várólistába küldi a rendszer. Meg szeretné kapni ezeket az üzeneteket, és bekell helyeznie őket egy adatbázisba. Egy logikai alkalmazásban `Service Bus - when a message is received in a queue` adja hozzá az eseményindítót, és jelölje ki a várólistát. Ezután adja `Oracle Database - Insert row` hozzá a műveletet, és jelölje ki a táblázatot:

    ![](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>Gyakori hibák

#### <a name="error-cannot-reach-the-gateway"></a>**Hiba**: Nem érhető el az átjáró

**Ok:** A helyszíni adatátjáró nem tud csatlakozni a felhőhöz. 

**Kockázatcsökkentés:** Győződjön meg arról, hogy az átjáró azon a helyszíni gépen fut, ahol telepítette, és hogy képes csatlakozni az internethez.Javasoljuk, hogy ne telepítse az átjárót olyan számítógépre, amely ki van kapcsolva vagy alvó állapotban van.A helyszíni adatátjáró-szolgáltatás (PBIEgwService) újraindítása is.

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-see-httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**Hiba**: A használt szolgáltató elavult: "System.Data.OracleClient 8.1.7-es vagy újabb verziójú Oracle-ügyfélszoftvert igényel.". Lásd [https://go.microsoft.com/fwlink/p/?LinkID=272376](https://go.microsoft.com/fwlink/p/?LinkID=272376) a hivatalos szolgáltató telepítését.

**Ok**: Az Oracle ügyfél SDK nincs telepítve arra a számítógépre, ahol a helyszíni adatátjáró fut.  

**Megoldás**: Töltse le és telepítse az Oracle client SDK-t ugyanarra a számítógépre, mint a helyszíni adatátjáró.

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**Hiba:** A(z) '[Táblanév]" tábla nem határoz meg kulcsoszlopokat

**Ok**: A táblában nincs elsődleges kulcs.  

**Megoldás**: Az Oracle Database-összekötő használatához elsődleges kulcsoszlopot tartalmazó táblát kell használni.

#### <a name="currently-not-supported"></a>Jelenleg nem támogatott

* Nézetek 
* Bármilyen táblázat kompozit billentyűkkel
* Beágyazott objektumtípusok táblázatokban
 
## <a name="connector-specific-details"></a>Összekötő-specifikus részletek

Tekintse meg a swaggerben definiált eseményindítókat és műveleteket, és tekintse meg a korlátokat az [összekötő részleteiben.](/connectors/oracle/) 

## <a name="get-some-help"></a>Segítség

Az [Azure Logic Apps fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) remek hely a kérdések feltehetésére, a kérdések megválaszolására és a többi Logic Apps-felhasználó által végzett egyéb felhasználók megtekintésére. 

A logikai alkalmazások és az összekötők fejlesztésében a [https://aka.ms/logicapps-wish](https://aka.ms/logicapps-wish)szavazással és az ötletei elküldésével a . 


## <a name="next-steps"></a>További lépések
[Hozzon létre egy logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md), és fedezze fel az elérhető összekötőket a Logic Apps [API-k listájában.](apis-list.md)
