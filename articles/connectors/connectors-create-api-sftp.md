---
title: Csatlakozhat SFTP-fiókjához, az Azure Logic Apps |} A Microsoft Docs
description: Automatizálhatja a feladatokat és a munkafolyamatok, amelyek figyelése, létrehozása, kezelése, küldése és fogadása fájlok SFTP-kiszolgálóra az Azure Logic Apps használatával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 697eb8b0-4a66-40c7-be7b-6aa6b131c7ad
ms.topic: article
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 8f430477883543aa8f87eb3fb0fb49ab31e2d723
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43042038"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps"></a>Figyelése, létrehozása és kezelése az SFTP-fájlok Azure Logic Apps használatával

Az Azure Logic Apps és az SFTP-összekötővel, automatizált feladatokat és figyelése, létrehozása, küldése és fájlokat a fiókján keresztül fogad a munkafolyamatokat hozhat létre egy [SFTP](https://www.ssh.com/ssh/sftp/) kiszolgáló, valamint más műveletek, például:

* Ez a figyelő fájlok hozzáadásakor vagy módosítani.
* Beolvasása, létrehozása, másolja, frissítse a listát, és törölje a fájlokat.
* Fájl tartalom és metaadatok beolvasása.
* Mappák archívumok kibontása.

Használhatja az eseményindítókat, amelyek választ kaphat az SFTP-kiszolgáló és a kimenetet más műveletek számára elérhetővé tenni. Műveletek a logic Apps segítségével az SFTP-kiszolgálón lévő fájlok feladatokat. Az SFTP-műveletek kimenetét használják más műveleteket is rendelkezhet. Például ha rendszeresen kérnek le fájlok az SFTP-kiszolgálóról, elküldheti e-mailek ezeket a fájlokat és a tartalom az Office 365 Outlook-összekötőt vagy Outlook.com-összekötő használatával.
Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. 

* Az SFTP gazdagép címét és a fiók hitelesítő adatait

   A hitelesítő adatok engedélyezik a logikai alkalmazás, hozzon létre egy kapcsolatot, és az SFTP-fiók eléréséhez.

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazás, ahol szeretné elérni az SFTP-fiókjához. Szeretne kezdeni egy SFTP-triggert [hozzon létre egy üres logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md). SFTP-művelet használatához indítsa el a logikai alkalmazás egy másik eseményindítóval, például a **ismétlődési** eseményindító.

## <a name="connect-to-sftp"></a>Csatlakozhat SFTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és nyissa meg a logikai alkalmazás a Logikaialkalmazás-Tervező, ha nem, nyissa meg a már.

1. Üres logic Apps a keresőmezőbe írja be szűrőként "sftp". Eseményindítók listája alatt válassza ki a kívánt az eseményindító. 

   – vagy –

   Meglévő logic Apps alkalmazások, az utolsó lépés, adjon hozzá egy műveletet, amelyre a válasszon **új lépés**. 
   A Keresés mezőbe írja be szűrőként "sftp". 
   Műveletek listája alatt válassza ki a kívánt művelet.

   Lépések közötti művelet hozzáadása, helyezze az egérmutatót a nyíl lépések között. 
   Válassza a plusz jelre (**+**), amely akkor jelenik meg, és válassza ki **művelet hozzáadása**.

1. Adja meg a szükséges adatokat a kapcsolatot, és válassza a **létrehozás**.

1. Adja meg a szükséges adatokat a kijelölt eseményindítót vagy műveletet, és továbbra is használhatja a logic app-munkafolyamatot.

## <a name="examples"></a>Példák

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>Az SFTP-eseményindító: amikor felvesznek vagy módosítanak egy fájlt

Ez az eseményindító egy logikai alkalmazás munkafolyamatának kezdődik meg az eseményindító észleli, ha egy fájl hozzáadásakor vagy módosítja a SFTP-kiszolgálóra. Így például hozzáadhat egy feltételt, amely ellenőrzi a fájl tartalmát, és úgy dönt, hogy kapják meg a tartalmat a e tartalom megfelel-e a megadott feltétel alapján. Végül adjon hozzá egy műveletet, amely a fájl tartalmának beolvasása, és helyezi a tartalmat egy mappába az SFTP-kiszolgáló. 

**Példa vállalati**: a trigger használatával figyelheti a egy új megrendelések képviselő fájlok SFTP-mappába. Ezután használhatja az SFTP-művelet például **fájl tartalmának beolvasása**, hogy a rendelés tartalmának beolvasása a további feldolgozás céljából, valamint egy rendelési adatbázisba sorrendben tárolja.

### <a name="sftp-action-get-content"></a>SFTP-művelet: tartalom lekérése

Ez a művelet a tartalom olvas be egy fájl az SFTP-kiszolgálóra. Így például az előző példában és a egy feltételt, amely a fájl tartalmának meg kell felelnie az eseményindító is hozzáadhat. Ha a feltétel teljesül, a műveletet, amely lekérdezi a tartalmat futtathatja. 

## <a name="connector-reference"></a>Összekötő-referencia

További technikai részletek korlátok, eseményindítók és műveletek, amely ismerteti az összekötő OpenAPI által (korábbi nevén Swagger) leírását, tekintse át az összekötő [referencialapja](/connectors/sftpconnector/).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)