---
title: Kapcsolódás FTP-kiszolgálóhoz – Azure Logic Apps
description: FTP-kiszolgálón található fájlok létrehozása, figyelése és kezelése Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: divswa, klam, LADocs
ms.topic: conceptual
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: ac6ae1a3b00a4e7568bd7967105f202fbf2e4f9b
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547491"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>FTP-fájlok létrehozása, figyelése és kezelése Azure Logic Apps használatával

A Azure Logic Apps és az FTP-összekötővel olyan automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek fájlok létrehozására, figyelésére, küldésére és fogadására használhatók a fiókjában egy FTP-kiszolgálón, más műveletekkel együtt, például:

* A fájlok hozzáadásának vagy módosításának figyelése.
* Fájlok lekérése, létrehozása, másolása, frissítése, listázása és törlése.
* Fájl tartalmának és metaadatainak beolvasása.
* Archívumok kinyerése mappákba.

Használhat olyan eseményindítókat, amelyek válaszokat kapnak az FTP-kiszolgálóról, és más műveletek számára elérhetővé teszik a kimenetet. Az FTP-kiszolgálón található fájlok kezelésére a Logic apps futtatási műveletei használhatók. Más műveletek is használhatók az FTP-műveletek kimenetének használatával. Ha például rendszeresen letölti a fájlokat az FTP-kiszolgálóról, e-mailt küldhet a fájlokról és azok tartalmáról az Office 365 Outlook Connector vagy a Outlook.com Connector használatával. Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>Korlátok

* Az FTP-összekötő csak az SSL-en keresztüli explicit FTP-t (FTPS) támogatja, és nem kompatibilis az implicit FTPS.

* Alapértelmezés szerint az FTP-műveletek a *50 MB vagy annál kisebb*fájlokat képesek olvasni vagy írni. Az 50 MB-nál nagyobb fájlok kezeléséhez az FTP-műveletek támogatják az [üzenetek darabolását](../logic-apps/logic-apps-handle-large-messages.md). A **fájl tartalmának beolvasása** művelet implicit módon adatdarabolást használ.

* Az FTP-eseményindítók nem támogatják a darabolást. Fájl tartalmának kérésekor a triggerek csak 50 MB vagy annál kisebb fájlokat választanak ki. A 50 MB-nál nagyobb fájlok lekéréséhez kövesse az alábbi mintát:

  * Használjon olyan FTP-triggert, amely a fájl tulajdonságait adja vissza, például **egy fájl hozzáadásakor vagy módosításakor (csak tulajdonságok)** .

  * Hajtsa végre az aktiválást az FTP- **Fájl letöltése** művelettel, amely beolvassa a teljes fájlt, és implicit módon használja a darabolást.

## <a name="how-ftp-triggers-work"></a>Az FTP-eseményindítók működése

Az FTP-eseményindítók az FTP fájlrendszer lekérdezésével és a legutóbbi lekérdezés óta módosult fájlok keresésével működnek. Egyes eszközök lehetővé teszik, hogy a fájlok változásakor őrizze meg az időbélyeget. Ezekben az esetekben le kell tiltania ezt a funkciót, így az trigger működhet. Íme néhány gyakori beállítás:

| SFTP-ügyfél | Műveletek |
|-------------|--------|
| WinSCP | Lépjen a **beállítások** > **beállítások** > **átvitel** > **Szerkesztés** > **megőrzése időbélyeg** > **Letiltás** |
| Filezillát | Ugrás az **átvitel** > **az átvitt fájlok időbélyegének megőrzése** > **Letiltás** |
|||

Ha egy trigger új fájlt talál, az trigger ellenőrzi, hogy az új fájl elkészült-e, és nem részlegesen van-e írva. Előfordulhat például, hogy egy fájl változása folyamatban van, amikor az trigger ellenőrzi a fájlkiszolgálón. Egy részlegesen megírt fájl visszaadásának elkerüléséhez az trigger megállapítja a legutóbbi módosításokat tartalmazó fájl időbélyegét, de nem adja vissza azonnal a fájlt. Az trigger csak akkor adja vissza a fájlt, ha újra kérdezi le a kiszolgálót. Előfordulhat, hogy ez a viselkedés egy késleltetést okoz, amely akár kétszer is meghaladhatja az aktiválás lekérdezési időközét.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Az FTP-gazda kiszolgálójának címe és a fiók hitelesítő adatai

  Az FTP-összekötő megköveteli, hogy az FTP-kiszolgáló elérhető legyen az internetről, és *passzív* üzemmódban működjön. A hitelesítő adatai lehetővé teszik a logikai alkalmazás számára a kapcsolat létrehozását és az FTP-fiók elérését.

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, ahová el szeretné érni az FTP-fiókját. Ha egy FTP-triggert szeretne kezdeni, [hozzon létre egy üres logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). FTP-művelet használatához indítsa el a logikai alkalmazást egy másik eseményindítóval, például az **ismétlődési** eseményindítóval.

## <a name="connect-to-ftp"></a>Kapcsolódás FTP-hez

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és nyissa meg a logikai alkalmazást a Logic app Designerben, ha már nincs megnyitva.

1. Üres logikai alkalmazások esetén a keresőmezőbe írja be szűrőként az "FTP" kifejezést. Válassza ki a kívánt eseményindítót az eseményindítók listából.

   – vagy –

   Meglévő logikai alkalmazások esetében az utolsó lépésben, amelyhez műveletet szeretne hozzáadni, válassza az **új lépés**, majd a **művelet hozzáadása**lehetőséget. A keresőmezőbe írja be szűrőként az "FTP" kifejezést. A műveletek listában válassza ki a kívánt műveletet.

   A lépések közötti művelet hozzáadásához vigye a mutatót a lépések közötti nyíl fölé. Válassza ki a megjelenő pluszjelet ( **+** ), majd válassza a **művelet hozzáadása**lehetőséget.

1. Adja meg a kapcsolathoz szükséges adatokat, majd válassza a **Létrehozás**lehetőséget.

1. Adja meg a kiválasztott trigger vagy művelet szükséges adatait, és folytassa a logikai alkalmazás munkafolyamatának összeállítását.

## <a name="examples"></a>Példák

<a name="file-added-modified"></a>

### <a name="ftp-trigger-when-a-file-is-added-or-modified"></a>FTP-trigger: fájl hozzáadásakor vagy módosításakor

Ez az aktiválás egy logikai alkalmazás munkafolyamatát indítja el, ha az trigger észleli, ha egy fájlt hozzáadnak vagy módosítanak egy FTP-kiszolgálón. Így például hozzáadhat egy olyan feltételt, amely ellenőrzi a fájl tartalmát, és eldönti, hogy szeretné-e lekérni a tartalmat, attól függően, hogy az adott tartalom megfelel-e a megadott feltételnek. Végül hozzáadhat egy olyan műveletet, amely beolvassa a fájl tartalmát, és az SFTP-kiszolgáló egy mappájába helyezi a tartalmat.

**Vállalati példa**: ezt az triggert használhatja az ügyfél-megrendeléseket leíró új fájlok FTP-mappájának figyelésére. Ezután használhat egy FTP-műveletet, például a **fájlok beolvasása**lehetőséget, így a rendelés tartalmát megtekintheti további feldolgozás céljából, és tárolhatja az Orders adatbázisban.

Íme egy példa, amely megjeleníti ezt az triggert: **fájl hozzáadásakor vagy módosításakor**

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és nyissa meg a logikai alkalmazást a Logic app Designerben, ha már nincs megnyitva.

1. Üres logikai alkalmazások esetén a keresőmezőbe írja be szűrőként az "FTP" kifejezést. Az eseményindítók listájában válassza ki ezt az eseményindítót: **Ha egy iktatott vagy módosított-FTP**

   ![FTP-trigger keresése és kiválasztása](./media/connectors-create-api-ftp/select-ftp-trigger.png)  

1. Adja meg a kapcsolathoz szükséges adatokat, majd válassza a **Létrehozás**lehetőséget.

   Alapértelmezés szerint ez az összekötő szöveges formátumban továbbítja a fájlokat. Ha bináris formátumban kívánja átvinni a fájlokat, például ha a kódolást használja, válassza a **bináris átvitel**lehetőséget.

   ![FTP-kiszolgáló kapcsolat létrehozása](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)  

1. A **mappa** mező mellett válassza a mappa ikont, hogy megjelenjen a lista. Az új vagy szerkesztett fájlokhoz figyelni kívánt mappa megkereséséhez válassza a derékszög nyilat ( **>** ), keresse meg a mappát, majd válassza ki a mappát.

   ![A figyelni kívánt mappa megkeresése és kiválasztása](./media/connectors-create-api-ftp/select-folder.png)  

   A kiválasztott mappa a **mappa** mezőben jelenik meg.

   ![Kiválasztott mappa](./media/connectors-create-api-ftp/selected-folder.png)  

Most, hogy a logikai alkalmazás rendelkezik triggerrel, adja hozzá a futtatni kívánt műveleteket, amikor a logikai alkalmazás új vagy szerkesztett fájlt talál. Ebben a példában hozzáadhat egy olyan FTP-műveletet, amely az új vagy frissített tartalmat kéri le.

<a name="get-content"></a>

### <a name="ftp-action-get-content"></a>FTP-művelet: tartalom lekérése

Ez a művelet beolvassa a tartalmat egy FTP-kiszolgálón lévő fájlból a fájl hozzáadásakor vagy frissítésekor. Így például hozzáadhat egy triggert az előző példához, és egy olyan műveletet, amely a fájl hozzáadását vagy szerkesztését követően lekéri a fájl tartalmát.

Íme egy példa, amely megjeleníti ezt a műveletet: **tartalom lekérése**

1. Az trigger vagy bármely egyéb művelet alatt válassza az **új lépés**lehetőséget.

1. A keresőmezőbe írja be szűrőként az "FTP" kifejezést. A műveletek listában válassza ki ezt a műveletet: **fájl tartalmának beolvasása – FTP**

   ![FTP-művelet kiválasztása](./media/connectors-create-api-ftp/select-ftp-action.png)  

1. Ha már rendelkezik kapcsolattal az FTP-kiszolgálóval és-fiókkal, folytassa a következő lépéssel. Ellenkező esetben adja meg a kapcsolathoz szükséges adatokat, majd válassza a **Létrehozás**lehetőséget.

   ![FTP-kiszolgáló kapcsolat létrehozása](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. Amikor megnyílik a **fájl beolvasása** művelet, kattintson a **fájl** szövegmezőbe, hogy megjelenjen a dinamikus tartalmak listája. Mostantól kiválaszthatja az előző lépésekből származó kimenetek tulajdonságait. A dinamikus tartalom listából válassza ki a **fájl tartalma** tulajdonságot, amely tartalmazza a hozzáadott vagy frissített fájl tartalmát.  

   ![Fájl keresése és kiválasztása](./media/connectors-create-api-ftp/ftp-action-get-file-content.png)

   A **fájl tartalma** tulajdonság most megjelenik a **fájl** mezőben.

   ![A fájl tartalmának kiválasztott tulajdonsága](./media/connectors-create-api-ftp/ftp-action-selected-file-content-property.png)

1. Mentse a logikai alkalmazást. A munkafolyamat teszteléséhez adjon hozzá egy fájlt a logikai alkalmazás által jelenleg figyelt FTP-mappához.

## <a name="connector-reference"></a>Összekötő-referencia

Az eseményindítókkal, műveletekkel és korlátokkal kapcsolatos technikai részletekért lásd az összekötő OpenAPI (korábban: hencegés) leírását, tekintse át az [összekötő hivatkozási oldalát](/connectors/ftpconnector/).

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése