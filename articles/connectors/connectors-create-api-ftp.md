---
title: Csatlakozás FTP-kiszolgálóhoz
description: Az FTP-kiszolgálón lévő fájlokat létrehozó, figyelő és kezelő feladatok és munkafolyamatok automatizálása az Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 12/15/2019
tags: connectors
ms.openlocfilehash: 5b61b51e79c71736e18aaa63ab032c05c512c8d7
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656336"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>FTP-fájlok létrehozása, figyelése és kezelése az Azure Logic Apps használatával

Az Azure Logic Apps és az FTP-összekötő segítségével automatizált feladatokat és munkafolyamatokat hozhat létre, figyelhet, küldhet és fogadhat fájlokat a fiókján keresztül egy FTP-kiszolgálón, valamint más műveleteket, például:

* A fájlok hozzáadásakor vagy módosításának figyelése.
* Fájlok beszerezni, létrehozása, másolása, frissítése, listázása és törlése.
* Fájltartalom és metaadatok beszerezése.
* Bontsa ki az archívumokat mappákba.

Olyan eseményindítókat használhat, amelyek válaszokat kapnak az FTP-kiszolgálóról, és a kimenetet más műveletek számára is elérhetővé teszik. A logikai alkalmazásokban futtathat műveleteket az FTP-kiszolgálón lévő fájlok kezeléséhez. Más műveletek is használhatják az FTP-műveletek kimenetét. Ha például rendszeresen kap fájlokat az FTP-kiszolgálóról, az Office 365 Outlook-összekötő vel vagy Outlook.com összekötővel e-mailt küldhet ezekről a fájlokról és azok tartalmáról. Ha most kezdi a logikai alkalmazásokat, tekintse át [az Azure Logic Apps.](../logic-apps/logic-apps-overview.md)

## <a name="limitations"></a>Korlátozások

* Az FTP-csatlakozó csak explicit FTP-t támogat TLS/SSL (FTPS) kapcsolatban, és nem kompatibilis az implicit FTPS-sel.

* Alapértelmezés szerint az FTP-műveletek *50 MB vagy kisebb*fájlokat tudnak olvasni vagy írni. Az 50 MB-nál nagyobb fájlok kezeléséhez az FTP-műveletek támogatják [az üzenetdarabolást.](../logic-apps/logic-apps-handle-large-messages.md) A **Fájltartalom beszerezni** művelete implicit módon adattömböt használ.

* Az FTP-eseményindítók nem támogatják a darabolást. Fájltartalom igénylésekor az eseményindítók csak az 50 MB-os vagy kisebb fájlokat jelölik ki. Az 50 MB-nál nagyobb fájlok beletöltéséhez kövesse az alábbi mintát:

  * Használjon olyan FTP-eseményindítót, amely fájltulajdonságokat ad vissza, például **fájl hozzáadásakor vagy módosításakor (csak tulajdonságok esetén).**

  * Kövesse az eseményindítót az FTP **Fájltartalom beolvasása** művelettel, amely beolvassa a teljes fájlt, és implicit módon adattömböt használ.

* Ha helyszíni FTP-kiszolgálóval rendelkezik, fontolja meg egy [integrációs szolgáltatási környezet (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) létrehozását vagy [az Azure App Service Hybrid-kapcsolatok](../app-service/app-service-hybrid-connections.md)használatát, amelyek lehetővé teszik a helyszíni adatforrások elérését a helyszíni adatátjáró használata nélkül.

## <a name="how-ftp-triggers-work"></a>Az FTP-eseményindítók működése

FTP kiváltó munka a közvélemény-kutatás az FTP fájlrendszer, és keresi a fájlt, hogy megváltozott, mivel a legutóbbi szavazás. Egyes eszközök lehetővé teszik az időbélyeg megőrzését a fájlok módosításakor. Ezekben az esetekben le kell tiltania ezt a funkciót, hogy az eseményindító működhessen. Íme néhány gyakori beállítás:

| SFTP-ügyfél | Műveletek |
|-------------|--------|
| Megnyerő | Ugrás a **Beállítások** > **beállításai hoz** > **Átvitel** > **szerkesztése** > **időbélyeg-megőrzésletiltása** > **Disable** |
| Filezilla | Ugrás az **Átvitt** > **fájlok időbélyegeinek megőrzése** > **című** tovább |
|||

Amikor egy eseményindító új fájlt talál, az eseményindító ellenőrzi, hogy az új fájl teljes, és nem részben írt. Előfordulhat például, hogy egy fájl folyamatban van, amikor az eseményindító ellenőrzi a fájlkiszolgálót. A részlegesen írott fájl visszaadásának elkerülése érdekében az eseményindító megjegyzi a legutóbbi módosításokat tartalmazó fájl időbélyegét, de nem adja vissza azonnal a fájlt. Az eseményindító csak a kiszolgáló ismételt lekérdezésekénekután adja vissza a fájlt. Néha ez a viselkedés az eseményindító lekérdezési időközének akár kétszeresét is okozhatja késleltetést okozhat.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Az FTP-állomás kiszolgálójának címe és a fiók hitelesítő adatai

  Az FTP-csatlakozó megköveteli, hogy az FTP-kiszolgáló elérhető legyen az internetről, és úgy legyen beállítva, hogy *passzív* módban működjön. A hitelesítő adatok lehetővé teszik a logikai alkalmazás számára, hogy kapcsolatot hozzon létre, és hozzáférjen az FTP-fiókhoz.

* Alapvető ismeretek [a logikai alkalmazások létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, amelyhez hozzá szeretne férni az FTP-fiókjához. Az FTP-eseményindítóval való kezdéshez [hozzon létre egy üres logikai alkalmazást.](../logic-apps/quickstart-create-first-logic-app-workflow.md) FTP-művelet használatához indítsa el a logikai alkalmazást egy másik eseményindítóval, például az **Ismétlődés** eseményindítóval.

## <a name="connect-to-ftp"></a>Csatlakozás FTP-hez

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)és nyissa meg a logikai alkalmazást a Logic App Designerben.

1. Üres logikai alkalmazások esetén a `ftp` keresőmezőbe írja be szűrőként. Az **Eseményindítók** listában válassza ki a kívánt eseményindítót.

   – vagy –

   Meglévő logikai alkalmazások esetén az utolsó lépésben, ahol műveletet szeretne hozzáadni, válassza az **Új lépés**lehetőséget, majd a **Művelet hozzáadása**lehetőséget. A keresőmezőbe írja `ftp` be szűrőként. A **Műveletek** listában válassza ki a kívánt műveletet.

   Ha lépéseket szeretne hozzáadni a lépések közé, vigye az egérmutatót a lépések közötti nyíl fölé. Jelölje ki a**+** megjelenő pluszjelet ( ), majd kattintson **a Művelet hozzáadása gombra.**

1. Adja meg a kapcsolatadatait, és válassza a **Létrehozás gombot.**

1. Adja meg a kiválasztott eseményindító vagy művelet adatait, és folytassa a logikai alkalmazás munkafolyamatának kiépítését.

## <a name="examples"></a>Példák

<a name="file-added-modified"></a>

### <a name="add-ftp-trigger"></a>FTP-eseményindító hozzáadása

A **Fájl hozzáadásakor vagy módosításakor (csak tulajdonságok esetén)** logikai alkalmazás-munkafolyamatot indít el, amikor az eseményindító azt észleli, hogy egy fájlt egy FTP-kiszolgálón adnak hozzá vagy módosítanak. Hozzáadhat például egy feltételt, amely ellenőrzi a fájl tartalmát, és eldönti, hogy lekéri-e a tartalmat, attól függően, hogy a tartalom megfelel-e egy adott feltételnek. Végül hozzáadhat egy műveletet, amely beszerzi a fájl tartalmát, és a tartalmat az SFTP-kiszolgáló egy másik mappájába helyezheti.

Ezzel az eseményindítóval például figyelheti az ügyfélrendeléseket leíró új fájlok FTP-mappáját. Ezután egy FTP-művelettel, például **a Fájl metaadatainak betárolásával** lejuthat az új fájl tulajdonságaira, majd a **Fájltartalom betárolása** segítségével további feldolgozásra és a rendelések adatbázisában tárolhatja a rendeléseket.

Íme egy példa, amely bemutatja, hogyan kell használni a **Fájl hozzáadásakor vagy módosításakor (csak tulajdonságok)** eseményindító.

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)és nyissa meg a logikai alkalmazást a Logic App Designerben, ha még nem nyitott.

1. Üres logikai alkalmazások esetén a `ftp` keresőmezőbe írja be szűrőként. Az eseményindítók listában válassza ezt az eseményindítót: **Ha iktatott elemet adnak hozzá vagy módosítanak (csak tulajdonságok)**

   ![Az FTP-eseményindító megkeresése és kijelölése](./media/connectors-create-api-ftp/select-ftp-trigger-logic-app.png)

1. Adja meg a kapcsolathoz szükséges részleteket, majd válassza a **Létrehozás gombot.**

   Alapértelmezés szerint ez az összekötő szöveges formátumban továbbítja a fájlokat. Ha bináris formátumú fájlokat szeretne átvinni, például a kódolás helyével és amikor használja, válassza a **Bináris átvitel**lehetőséget.

   ![FTP-kiszolgálóval való kapcsolat létrehozása](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)

1. A **Mappa mezőben** jelölje ki a mappa ikonját, hogy megjelenjen a lista. Az új vagy szerkesztett fájlok at figyelni kívánt mappa megkereséséhez jelölje**>** ki a derékszögű nyilat ( ), keresse meg a mappát, majd jelölje ki a mappát.

   ![A figyelni kívánt mappa megkeresése és kijelölése](./media/connectors-create-api-ftp/select-folder-ftp-trigger.png)

   A kijelölt mappa megjelenik a **Mappa** mezőben.

   ![A kijelölt mappa megjelenik a "Mappa" tulajdonságban](./media/connectors-create-api-ftp/selected-folder-ftp-trigger.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés gombot.**

Most, hogy a logikai alkalmazás rendelkezik egy eseményindítóval, adja hozzá a futtatni kívánt műveleteket, amikor a logikai alkalmazás új vagy szerkesztett fájlt talál. Ebben a példában hozzáadhat egy FTP-műveletet, amely beszerzi az új vagy frissített tartalmat.

<a name="get-content"></a>

### <a name="add-ftp-action"></a>FTP hozzáadása művelet

A **Fájl metaadat-beszerezése** művelet lekéri az FTP-kiszolgálón lévő fájl tulajdonságait, és a **Fájltartalom begetése** művelet az FTP-kiszolgálón lévő fájladatai alapján kapja meg a fájl tartalmát. Hozzáadhatja például az előző példában lévő eseményindítót, és ezeket a műveleteket a fájl tartalmának beidézéséhez a fájl hozzáadása vagy szerkesztése után.

1. Az eseményindító vagy bármely más művelet alatt válassza az **Új lépés lehetőséget.**

1. A keresőmezőbe írja `ftp` be szűrőként. A műveletek listájában jelölje be ezt a műveletet: **Fájlmetaadatok beszereznie**

   ![Válassza a "Fájlmetaadatok bekerülése" műveletet](./media/connectors-create-api-ftp/select-get-file-metadata-ftp-action.png)

1. Ha már van kapcsolata az FTP-kiszolgálóval és fiókkal, folytassa a következő lépéssel. Ellenkező esetben adja meg a kapcsolathoz szükséges részleteket, majd válassza a **Létrehozás gombot.**

   ![FTP-kiszolgálókapcsolat létrehozása](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. A **Fájl metaadatának bekerülése** művelet után kattintson a **Fájl** mezőben, hogy megjelenjen a dinamikus tartalomlista. Most már kiválaszthatja az előző lépések kimeneteinek tulajdonságait. A dinamikus tartalomlistában a **Fájlmetaadatok begyűjtése**csoportban jelölje ki a **Fájlazonosító listája** tulajdonságot, amely arra a gyűjteményre hivatkozik, amelyben a fájlt hozzáadták vagy frissítették.

   ![A "Fájlazonosító listája" tulajdonság megkeresése és kijelölése](./media/connectors-create-api-ftp/select-list-of-files-id-output.png)

   A **Fájlazonosító listája** tulajdonság most megjelenik a **Fájl** mezőben.

   ![Kijelölt "Fájlazonosító" tulajdonság](./media/connectors-create-api-ftp/selected-list-file-ids-ftp-action.png)

1. Most add hozzá ezt az FTP-műveletet: **Fájltartalom beszerezni**

   ![A "Fájltartalom bekerülése" művelet megkeresése és kijelölése](./media/connectors-create-api-ftp/select-get-file-content-ftp-action.png)

1. A **Fájltartalom bekerülése** művelet után kattintson a **Fájl** mezőben, hogy megjelenjen a dinamikus tartalomlista. Most már kiválaszthatja az előző lépések kimeneteinek tulajdonságait. A dinamikus tartalomlistában a **Fájlmetaadatok begete**csoportban jelölje ki az **Id** tulajdonságot, amely a hozzáadott vagy frissített fájlra hivatkozik.

   ![Az "Id" tulajdonság megkeresése és kiválasztása](./media/connectors-create-api-ftp/get-file-content-id-output.png)

   Az **Id** tulajdonság most megjelenik a **Fájl** mezőben.

   ![Kijelölt "Id" tulajdonság](./media/connectors-create-api-ftp/selected-get-file-content-id-ftp-action.png)

1. Mentse a logikai alkalmazást.

## <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

Annak ellenőrzéséhez, hogy a munkafolyamat a várt tartalmat adja-e vissza, adjon hozzá egy másik műveletet, amely elküldi a tartalmat a feltöltött vagy frissített fájlból.

1. A **Fájltartalom beküldése** művelet csoportban adjon hozzá egy műveletet, amely elküldheti a fájl tartalmát. Ez a példa hozzáadja az **E-mail küldése** műveletet az Office 365 Outlookhoz.

   ![Művelet hozzáadása e-mail küldéséhez](./media/connectors-create-api-ftp/select-send-email-action.png)

1. A művelet meglépése után adja meg az információkat, és adja meg a tesztelni kívánt tulajdonságokat. Vegye fel például a **Fájltartalom** tulajdonságot, amely a dinamikus tartalomlistában jelenik meg, miután a **Fájltartalom bekerülése** szakaszban a **Továbbiak** lehetőséget választja.

   ![Az e-mailművelettel kapcsolatos információk biztosítása](./media/connectors-create-api-ftp/selected-send-email-action.png)

1. Mentse a logikai alkalmazást. A logikai alkalmazás futtatásához és aktiválásához az eszköztáron válassza a **Futtatás**lehetőséget, majd vegyen fel egy fájlt a logikai alkalmazás által most figyelt FTP-mappába.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötővel kapcsolatos további technikai részleteket, például az eseményindítókat, műveleteket és korlátokat az összekötő Swagger-fájlja szerint lásd az [összekötő referencialapján.](https://docs.microsoft.com/connectors/ftpconnector/)

> [!NOTE]
> [Az integrációs szolgáltatási környezetben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)lévő logikai alkalmazások esetében az összekötő ISE-címkével ellátott verziója az [ISE-üzenetkorlátokat](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) használja.

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)
