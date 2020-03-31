---
title: Csatlakozás IBM Informix adatbázishoz
description: Az IBM Informix ben tárolt erőforrásokat kezelő feladatok és munkafolyamatok automatizálása az Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
author: gplarsen
ms.author: plarsen
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/07/2020
tags: connectors
ms.openlocfilehash: dccb715c974037b4e3080f3e51576feae34c03df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76757968"
---
# <a name="manage-ibm-informix-database-resources-by-using-azure-logic-apps"></a>IBM Informix adatbázis-erőforrások kezelése az Azure Logic Apps használatával

Az [Azure Logic Apps](../logic-apps/logic-apps-overview.md) és az [Informix-összekötő](/connectors/informix/)segítségével automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek az IBM Informix-adatbázis erőforrásait kezelik. Ez az összekötő egy Microsoft-ügyfelet tartalmaz, amely a távoli Informix-kiszolgálószámítógépekkel kommunikál tcp/IP-hálózaton keresztül, beleértve a felhőalapú adatbázisokat, például az Ibm Informix for Windows-t, amely az Azure virtualizációban fut, és a helyszíni adatbázisokat a [helyszíni adatátjáró](../logic-apps/logic-apps-gateway-connection.md)használatakor. Ezekhez az Informix-platformokhoz és -verziókhoz akkor csatlakozhat, ha úgy vannak beállítva, hogy támogassák a DRDA ügyfélkapcsolatokat:

* IBM Informix 12.1
* IBM Informix 11,7

Ez a témakör bemutatja, hogyan használhatja az összekötőt egy logikai alkalmazásban az adatbázis-műveletek feldolgozásához.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Helyszíni adatbázisok esetén töltse [le és telepítse a helyszíni adatátjárót](../logic-apps/logic-apps-gateway-install.md) egy helyi számítógépre, majd [hozzon létre egy Azure-adatátjáró-erőforrást az Azure Portalon.](../logic-apps/logic-apps-gateway-connection.md)

* A logikai alkalmazás, ahol az Informix-adatbázishoz kell hozzáférnie. Ez az összekötő csak műveleteket biztosít, így a logikai alkalmazásnak már el kell kezdenie egy eseményindítóval, például az [Ismétlődés eseményindítóval.](../connectors/connectors-native-recurrence.md) 

## <a name="add-an-informix-action"></a>Informix művelet hozzáadása

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic App Designerben, ha még nem nyitott.

1. Az Informix művelet hozzáadásának lépése alatt válassza az **Új lépés**lehetőséget.

   Ha műveletet szeretne hozzáadni a meglévő lépések közé, vigye az egeret a csatlakozó nyíl fölé. Jelölje ki a**+** megjelenő pluszjelet ( ), majd kattintson **a Művelet hozzáadása gombra.**

1. A keresőmezőbe írja `informix` be szűrőként. A műveletek listájában jelölje ki a kívánt műveletet, például:

   ![Válassza ki a futtatni kívánt Informix műveletet](./media/connectors-create-api-informix/select-informix-connector-action.png)

   Az összekötő ezeket a műveleteket biztosítja, amelyek a megfelelő adatbázis-műveleteket futtatják:

   * Táblák beszereznie – Adatbázistáblák listázása `CALL` utasítás használatával
   * Sorok beolvasása – Az `SELECT *` összes sor beolvasása utasítás használatával
   * Sor beolvasása – Sor `SELECT WHERE` olvasása utasítás használatával
   * Sor hozzáadása `INSERT` utasítás használatával
   * Sor szerkesztése `UPDATE` utasítás használatával
   * Sor törlése `DELETE` utasítás használatával

1. Ha a rendszer kéri az Informix-adatbázis kapcsolatadatainak megadását, kövesse [a kapcsolat létrehozásához szükséges lépéseket,](#create-connection)majd folytassa a következő lépéssel.

1. Adja meg a kiválasztott művelet adatait:

   | Műveletek | Leírás | Tulajdonságok és leírások |
   |--------|-------------|-----------------------------|
   | **Táblák beszerezhetése** | Az adatbázistáblák listázása Informix CALL utasítás futtatásával. | None |
   | **Sorok bekerülése** | A megadott tábla összes sorának beolvasása `SELECT *` Informix utasítás futtatásával. | **Táblaneve**: A kívánt Informix-tábla neve <p><p>Ha további tulajdonságokat szeretne hozzáadni ehhez a művelethez, jelölje ki őket az **Új paraméter hozzáadása** listából. További információt az [összekötő referenciatémaköre](/connectors/informix/)tartalmaz. |
   | **Sor bekerülése** | Egy informix `SELECT WHERE` utasítás futtatásával lehív egy sort a megadott táblából. | - **Táblaneve**: A kívánt Informix-tábla neve <br>- **Sorazonosító**: A sor egyedi azonosítója, például:`9999` |
   | **Sor beszúrása** | Adjon hozzá egy sort a megadott Informix táblához egy Informix `INSERT` utasítás futtatásával. | - **Táblaneve**: A kívánt Informix-tábla neve <br>- **elem**: A hozzáadni kívánt értékeket felsorakó sor |
   | **Sor frissítése** | Módosítsa a megadott Informix-tábla egy sorát `UPDATE` egy Informix utasítás futtatásával. | - **Táblaneve**: A kívánt Informix-tábla neve <br>- **Sorazonosító**: A frissítandó sor egyedi azonosítója, például:`9999` <br>- **Sor**: A frissített értékekkel rendelkező sor, például:`102` |
   | **Sor törlése** | Egy Informix utasítás futtatásával távolítson el `DELETE` egy sort a megadott Informix táblából. | - **Táblaneve**: A kívánt Informix-tábla neve <br>- **Sorazonosító**: A törlandó sor egyedi azonosítója, például:`9999` |
   ||||

1. Mentse a logikai alkalmazást. Most [tesztelje a logikai alkalmazást,](#test-logic-app) vagy folytassa a logikai alkalmazás létrehozásának folytatását.

<a name="create-connection"></a>

## <a name="connect-to-informix"></a>Csatlakozás az Informixhez

1. Ha a logikai alkalmazás egy helyszíni adatbázishoz csatlakozik, válassza a **Csatlakozás a helyszíni adatátjárón keresztül**lehetőséget.

1. Adja meg ezt a kapcsolati információt, majd válassza **a Létrehozás lehetőséget.**

   | Tulajdonság | JSON-tulajdonság | Kötelező | Példaérték | Leírás |
   |----------|---------------|----------|---------------|-------------|
   | Kapcsolat neve | `name` | Igen | `informix-demo-connection` | Az Informix adatbázissal való kapcsolathoz használandó név |
   | Kiszolgáló | `server` | Igen | - Felhő:`informixdemo.cloudapp.net:9089` <br>- A helyszínen:`informixdemo:9089` | Az IPv4 vagy IPv6 formátumú TCP/IP-cím vagy alias, amelyet kettőspont és TCP/IP-portszám követ |
   | Adatbázis | `database` | Igen | `nwind` | A DRDA relációs adatbázisneve (RDBNAM) vagy az Informix adatbázis neve (dbname). Az Informix elfogad egy 128 bájtos karakterláncot. |
   | Hitelesítés | `authentication` | Csak a helyszínen | **Alapszintű** vagy **Windows** (kerberos) | Az Informix-adatbázis által megkövetelt hitelesítési típus. Ez a tulajdonság csak akkor jelenik meg, ha a **Csatlakozás a helyszíni adatátjárón keresztül lehetőséget választja.** |
   | Felhasználónév | `username` | Nem | <*adatbázis-felhasználó név*> | Az adatbázis felhasználóneve |
   | Jelszó | `password` | Nem | <*adatbázis-jelszó*> | Az adatbázis jelszava |
   | Átjáró | `gateway` | Csak a helyszínen | - <*Azure-előfizetés*> <br>- <*Azure-on-premises-data-gateway-erőforrás*> | Az Azure-előfizetés és az Azure-erőforrás neve az Azure Portalon létrehozott helyszíni adatátjáróhoz. Az **Átjáró** tulajdonság és altulajdonságok csak akkor jelennek meg, ha a **Csatlakozás a helyszíni adatátjárón keresztül lehetőséget választja.** |
   ||||||

   Példa:

   * **Felhőalapú adatbázis**

     ![Felhőalapú adatbázis-kapcsolat adatai](./media/connectors-create-api-informix/informix-cloud-connection.png)

   * **Helyszíni adatbázis**

     ![Helyszíni adatbázis-kapcsolat adatai](./media/connectors-create-api-informix/informix-on-premises-connection.png)

1. Mentse a logikai alkalmazást.

<a name="test-logic-app"></a>

## <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

1. A Logic App Designer **eszközsorfuttatása**gombolása. A logikai alkalmazás futtatása után megtekintheti a kimeneteket, hogy a futtatás.

1. A logikai alkalmazás menüjében válassza **az Áttekintés**lehetőséget. Az áttekintő ablaktáblán az **Összegzés futtatási** > **előzmények csoportban**válassza ki a legutóbbi futtatást.

1. A **Logikai alkalmazás futtatása**csoportban válassza **a Részletek futtatása**lehetőséget.

1. A műveletek listájában jelölje ki a megtekinteni kívánt kimeneteket tartalmazó műveletet, például **Get_tables.**

   Ha a művelet sikeres volt, **állapottulajdonságuk** Sikeresként van **megjelölve.**

1. A bemenetek megtekintéséhez a **Bemeneti hivatkozás csoportban**jelölje ki az URL-hivatkozást. A kimenetek megtekintéséhez a **Kimeneti hivatkozás** hivatkozás csoportban jelölje ki az URL-hivatkozást. Íme néhány példa kimenetek:

   * **Get_tables** a táblázatok listáját jeleníti meg:

     ![Kimenetek a "Táblák bekerülése" műveletből](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

   * **Get_rows** sorok listáját jeleníti meg:

     ![Kimenetek a "Sorok bekerülése" műveletből](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

   * **Get_row** a megadott sort jeleníti meg:

     ![Kimenetek a "Sor bekerülése" műveletből](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

   * **Insert_row** az új sort jeleníti meg:

     ![Kimenetek a "Sor beszúrása" műveletből](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

   * **Update_row** a frissített sort jeleníti meg:

     ![Kimenetek a "Sor frissítése" műveletből](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

   * **Delete_row** a törölt sor látható:

     ![Kimenetek a "Sor törlése" műveletből](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="connector-specific-details"></a>Összekötő-specifikus részletek

Az összekötő Swagger-leírása által leírt eseményindítók, műveletek és korlátok technikai részleteiért tekintse át az [összekötő referenciaoldalát.](/connectors/informix/)

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](apis-list.md)
