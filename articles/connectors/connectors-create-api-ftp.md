---
title: FTP-kiszolgálóhoz - Azure Logic Apps csatlakoztatása |} A Microsoft Docs
description: Létrehozása, figyelése és kezelése az Azure Logic Apps egy FTP-kiszolgálón található fájlok
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 10/15/2018
tags: connectors
ms.openlocfilehash: 1e649f21758adedb069b38f64f083ccb85df874d
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2019
ms.locfileid: "54913359"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>Létrehozása, figyelése és kezelése az FTP-fájlok Azure Logic Apps használatával

Az Azure Logic Apps és az FTP-összekötő automatikus feladatokkal és munkafolyamatokkal, létrehozása, figyelése, küldése és például a fájlok az FTP-kiszolgálón más műveletek, valamint a fiókján keresztül kap hozhat létre:

* Ez a figyelő fájlok hozzáadásakor vagy módosítani.
* Beolvasása, létrehozása, másolja, frissítse a listát, és törölje a fájlokat.
* Fájl tartalom és metaadatok beolvasása.
* Mappák archívumok kibontása.

Használhatja az eseményindítókat, amelyek választ kaphat az FTP-kiszolgáló és a kimenetet más műveletek számára elérhetővé tenni. Futtatási műveleteket használhat a logic Apps az FTP-kiszolgálón található fájlok kezeléséhez. FTP-műveleteket a kimenetét használják más műveleteket is rendelkezhet. Például ha rendszeresen fájlok az FTP-kiszolgálóról, elküldheti ezeket a fájlokat és a tartalom kapcsolatos e-mailek az Office 365 Outlook-összekötőt vagy Outlook.com-összekötő használatával. Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> Az FTP-összekötő támogatja a csak olyan fájlok, amelyek 50 MB vagy kisebb, ha nem használ [leskálázási műveletek darabolás üzenet](../logic-apps/logic-apps-handle-large-messages.md). Jelenleg nem használható, az eseményindítók darabolás.
>
> Ezenkívül az FTP-összekötő csak explicit FTP támogatja az SSL feletti (FTPS), és nem kompatibilis az implicit FTPS. 

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. 

* Your FTP host server address and account credentials

  Az FTP-összekötő szükséges, hogy az FTP-kiszolgáló érhető el az interneten, és állítsa be a művelethez használandó *passzív* mód. A hitelesítő adatok lehetővé teszik, hogy a logikai alkalmazás, hozzon létre egy kapcsolatot, és az FTP-fiók eléréséhez.

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazás, ahol szeretné-e az FTP-fiók eléréséhez. Szeretne kezdeni egy FTP-triggert [hozzon létre egy üres logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md). FTP művelet használatához indítsa el a logikai alkalmazás egy másik eseményindítóval, például a **ismétlődési** eseményindító.

## <a name="connect-to-ftp"></a>FTP-csatlakozás

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és nyissa meg a logikai alkalmazás a Logikaialkalmazás-Tervező, ha nem, nyissa meg a már.

1. Üres logic Apps a keresőmezőbe írja be szűrőként "ftp". Eseményindítók listája alatt válassza ki a kívánt az eseményindító. 

   – vagy –

   Meglévő logic Apps alkalmazások, az utolsó lépés, adjon hozzá egy műveletet, amelyre a válasszon **új lépés**, majd válassza ki **művelet hozzáadása**. 
   A keresőmezőbe írja be szűrőként az "ftp". 
   Műveletek listája alatt válassza ki a kívánt művelet.

   Lépések közötti művelet hozzáadása, helyezze az egérmutatót a nyíl lépések között. 
   Válassza a plusz jelre (**+**), amely akkor jelenik meg, és válassza ki **művelet hozzáadása**.

1. Adja meg a szükséges adatokat a kapcsolatot, és válassza a **létrehozás**.

1. Adja meg a szükséges adatokat a kijelölt eseményindítót vagy műveletet, és továbbra is használhatja a logic app-munkafolyamatot.

Fájl tartalmának kérésekor az eseményindító nem fájlok lekérése 50 MB-nál nagyobb. 50 MB-nál nagyobb fájlok lekéréséhez kövesse az ezt a mintát:

* Használjon egy eseményindítót, amely visszaadja a fájl tulajdonságait, például **fájl hozzáadásakor vagy módosításakor (csak tulajdonságok)**.

* Hajtsa végre az eseményindítót egy műveletet, amely beolvassa a teljes fájlt, mint például a **fájl tartalmának beolvasása elérési út segítségével**, és rendelkezik a művelet használata [üzenet darabolás](../logic-apps/logic-apps-handle-large-messages.md).

## <a name="examples"></a>Példák

<a name="file-added-modified"></a>

### <a name="ftp-trigger-when-a-file-is-added-or-modified"></a>FTP-eseményindító: Fájl hozzáadásakor és módosításakor

Ez az eseményindító a logikaialkalmazás-munkafolyamat az eseményindító észleli, ha egy fájl hozzáadásakor vagy módosítani az FTP-kiszolgálón kezdődik. Így például hozzáadhat egy feltételt, amely ellenőrzi a fájl tartalmát, és úgy dönt, hogy kapják meg a tartalmat a e tartalom megfelel-e a megadott feltétel alapján. Végül adjon hozzá egy műveletet, amely a fájl tartalmának beolvasása, és helyezi a tartalmat egy mappába az SFTP-kiszolgáló. 

**Példa vállalati**: Ez az eseményindító használatával figyelheti az FTP-mappába, új fájlok, amelyek ismertetik a vevői rendelések. Ezután használhatja az FTP művelet például **fájl tartalmának beolvasása**, hogy a rendelés tartalmának beolvasása a további feldolgozás céljából, valamint egy rendelési adatbázisba sorrendben tárolja.

Fájl tartalmának kérésekor eseményindítók nem kap fájlok 50 MB-nál nagyobb. 50 MB-nál nagyobb fájlok lekéréséhez kövesse az ezt a mintát: 

* Használjon egy eseményindítót, amely visszaadja a fájl tulajdonságait, például **fájl hozzáadásakor vagy módosításakor (csak tulajdonságok)**.

* Hajtsa végre az eseményindítót egy műveletet, amely beolvassa a teljes fájlt, mint például a **fájl tartalmának beolvasása elérési út segítségével**, és rendelkezik a művelet használata [üzenet darabolás](../logic-apps/logic-apps-handle-large-messages.md).

Egy érvényes és funkcionális logikai alkalmazásnak szüksége van, egy eseményindítót, és legalább egy műveletet. Ezért győződjön meg arról, hogy a művelet hozzáadása, miután hozzáadott egy eseményindítót.

A következő példa bemutatja, ez az eseményindító: **Amikor felvesznek vagy módosítanak egy fájlt**

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és nyissa meg a logikai alkalmazás a Logikaialkalmazás-Tervező, ha nem, nyissa meg a már.

1. Üres logic Apps a keresőmezőbe írja be szűrőként "ftp". Eseményindítók listájában válassza az eseményindító: **Ha egy iktatott hozzáadása vagy módosítása – FTP**

   ![Keresse meg és jelölje be az FTP-eseményindító](./media/connectors-create-api-ftp/select-ftp-trigger.png)  

1. Adja meg a szükséges adatokat a kapcsolatot, és válassza a **létrehozás**.

   Alapértelmezés szerint ez az összekötő a szöveges formátumú fájlok átvitele. 
   Átvitelhez bináris fájlok formátumú, például ha és kódolás használata esetén válassza ki a **bináris átvitelt**.

   ![FTP-kiszolgáló közötti kapcsolat létrehozása](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)  

1. Mellett a **mappa** válassza ki a mappa ikont, így egy lista jelenik meg. Az új vagy módosított fájlokat a figyelni kívánt mappa megkereséséhez a derékszögű nyílra (**>**), és keresse meg azt a mappát, majd válassza ki a mappát.

   ![Keresse meg és válassza ki a mappa figyelése](./media/connectors-create-api-ftp/select-folder.png)  

   A kijelölt mappa megjelenik a **mappa** mezőbe.

   ![Kiválasztott mappa](./media/connectors-create-api-ftp/selected-folder.png)  

Most, hogy a logikai alkalmazás egy eseményindító tartozik, adja hozzá a műveleteket szeretné futtatni, amikor a logikai alkalmazás egy új vagy módosított fájl talál. Ebben a példában egy FTP-műveletet, amely a lekérdezi az új vagy frissített tartalmat is hozzáadhat.

<a name="get-content"></a>

### <a name="ftp-action-get-content"></a>FTP-művelet: Tartalom lekérése

Ez a művelet a tartalom egy fájlt egy FTP-kiszolgálón olvassa be, ha a fájl hozzáadásakor vagy frissítésekor. Így például az előző példában és a egy műveletet, amely a fájl tartalmának beolvasása után ezt a fájlt ad hozzá vagy szerkeszthetők az eseményindító is hozzáadhat. 

Fájl tartalmának kérésekor eseményindítók nem kap fájlok 50 MB-nál nagyobb. 50 MB-nál nagyobb fájlok lekéréséhez kövesse az ezt a mintát: 

* Használjon egy eseményindítót, amely visszaadja a fájl tulajdonságait, például **fájl hozzáadásakor vagy módosításakor (csak tulajdonságok)**.

* Hajtsa végre az eseményindítót egy műveletet, amely beolvassa a teljes fájlt, mint például a **fájl tartalmának beolvasása elérési út segítségével**, és rendelkezik a művelet használata [üzenet darabolás](../logic-apps/logic-apps-handle-large-messages.md).

A következő példa bemutatja, ez a művelet: **Tartalom lekérése**

1. Válassza az eseményindító vagy bármely más műveletek, **új lépés**. 

1. A keresőmezőbe írja be szűrőként az "ftp". Műveletek listája alatt válassza a következő műveletet: **Fájl tartalmának - FTP beolvasása**

   ![FTP-művelet kiválasztása](./media/connectors-create-api-ftp/select-ftp-action.png)  

1. Ha már rendelkezik egy kapcsolatot az FTP-kiszolgáló és a fiók, lépjen a következő lépéssel. Ellenkező esetben adja meg a szükséges adatokat, hogy a kapcsolat, és válassza **létrehozás**. 

   ![FTP-kiszolgáló közötti kapcsolat létrehozása](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. Miután a **fájl tartalmának beolvasása** művelet megnyílik, kattintson a **fájl** mezőre, hogy a dinamikus tartalmak listája jelenik meg. Az előző lépésekből most kiválaszthatja a kimenetek tulajdonságait. A dinamikus tartalmú listából válassza ki a **fájltartalom** tulajdonság, amely rendelkezik a hozzáadott vagy frissített fájl tartalmát.  

   ![Keresse meg és válassza ki a fájlt](./media/connectors-create-api-ftp/ftp-action-get-file-content.png)

   A **fájltartalom** tulajdonság már szerepel a **fájl** mezőbe.

   ![A kijelölt "Fájl tartalma" tulajdonság](./media/connectors-create-api-ftp/ftp-action-selected-file-content-property.png)

1. Mentse a logikai alkalmazást. A munkafolyamat teszteléséhez adjon hozzá egy fájlt az FTP-mappába, amely a logikai alkalmazás most már figyeli.

## <a name="connector-reference"></a>Összekötő-referencia

További technikai részletek korlátok, eseményindítók és műveletek, amely ismerteti az összekötő OpenAPI által (korábbi nevén Swagger) leírását, tekintse át az összekötő [referencialapja](/connectors/ftpconnector/).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)
