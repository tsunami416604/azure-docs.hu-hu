---
title: Csatlakozás SFTP-fiókhoz
description: Az SFTP-kiszolgálók fájljainak SSH-n keresztüli felügyeletére, létrehozására, kezelésére, elküldésére és fogadására szolgáló feladatok és folyamatok automatizálása az Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: divswa, klam, logicappspm
ms.topic: article
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: d0da98070fa8da5403677e1a67bda75456c74d80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789273"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps"></a>SFTP-fájlok monitorozása, létrehozása és kezelése az Azure Logic Apps használatával

> [!IMPORTANT]
> Használja az [SFTP-SSH csatlakozót,](../connectors/connectors-sftp-ssh.md) mert az SFTP-csatlakozó elavult. A Logic App Designerben már nem jelölhet ki SFTP-eseményindítókat és műveleteket.

A [biztonságos fájlátviteli protokoll (SFTP)](https://www.ssh.com/ssh/sftp/) kiszolgálón lévő fájlok at figyelő, létrehozó, küldési és fogadási feladatok automatizálásához az Azure Logic Apps és az SFTP-összekötő használatával integrációs munkafolyamatokat hozhat létre és automatizálhat. Az SFTP olyan hálózati protokoll, amely fájlhozzáférést, fájlátvitelt és fájlfelügyeletet biztosít valamilyen megbízható adatstreamen keresztül. Íme néhány példa, amelyet automatizálhat:

* A fájlok hozzáadásakor vagy módosításának figyelése.
* Fájlok beszerezni, létrehozása, másolása, frissítése, listázása és törlése.
* Fájltartalom és metaadatok beszerezése.
* Bontsa ki az archívumokat mappákba.

Olyan eseményindítókat használhat, amelyek figyelik az SFTP-kiszolgálóeseményeit, és a kimenetet más műveletek számára is elérhetővé teszik. Az SFTP-kiszolgálón különböző feladatokat végrehajtó műveleteket használhat. A logikai alkalmazásban más műveletek is rendelkezhetnek az SFTP-műveletek kimenetének használatával. Ha például rendszeresen lekéri a fájlokat az SFTP-kiszolgálóról, az Office 365 Outlook-összekötő vagy Outlook.com összekötő használatával e-mailben értesítéseket küldhet ezekről a fájlokról és azok tartalmáról. Ha most kezdi meg a logikai alkalmazások, tekintse át [az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>Korlátok

Az SFTP-összekötő csak az *50 MB-os vagy annál kisebb* fájlokat kezeli, és nem támogatja az [üzenetdarabolást.](../logic-apps/logic-apps-handle-large-messages.md) Nagyobb fájlok esetén használja az [SFTP-SSH csatlakozót](../connectors/connectors-sftp-ssh.md). Az SFTP-csatlakozó és az SFTP-SSH-csatlakozó közötti különbségeket lásd: [Az SFTP-SSH és az SFTP összehasonlítása](../connectors/connectors-sftp-ssh.md#comparison) az SFTP-SSH cikkben.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Az SFTP-kiszolgáló címe és a fiók hitelesítő adatai, amelyek lehetővé teszik a logikai alkalmazás számára az SFTP-fiók elérését. A [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/) protokoll használatához hozzá kell férnie egy SSH titkos kulcshoz és az SSH titkos kulcs jelszavához is.

  > [!NOTE]
  >
  > Az SFTP-csatlakozó a következő titkos kulcsformátumokat támogatja: OpenSSH, ssh.com és PuTTY
  >
  > A logikai alkalmazás létrehozásakor, a kívánt SFTP-eseményindító vagy művelet hozzáadása után meg kell adnia az SFTP-kiszolgáló kapcsolati adatait. 
  > Ha SSH-személyes kulcsot használ, győződjön meg arról, hogy ***másolja*** a kulcsot az SSH titkos kulcsfájlból, és ***illessze be*** a kulcsot a kapcsolat részleteibe, ***ne írja be vagy illessze be manuálisan a kulcsot***, ami a kapcsolat sikertelensítéséhez okozhat. 
  > További információt a cikk későbbi lépéseiben talál.

* Alapvető ismeretek [a logikai alkalmazások létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, amelyhez hozzá szeretne férni az SFTP-fiókhoz. Ha egy SFTP-eseményindítóval szeretne [kezdeni, hozzon létre egy üres logikai alkalmazást.](../logic-apps/quickstart-create-first-logic-app-workflow.md) SFTP-művelet használatához indítsa el a logikai alkalmazást egy másik eseményindítóval, például az **Ismétlődés** eseményindítóval.

## <a name="how-sftp-triggers-work"></a>Az SFTP-eseményindítók működése

Az SFTP az SFTP fájlrendszer lekérdezésével és a legutóbbi szavazás óta megváltozott fájlok keresésével indítja el a munkát. Egyes eszközök lehetővé teszik az időbélyeg megőrzését a fájlok módosításakor. Ezekben az esetekben le kell tiltania ezt a funkciót, hogy az eseményindító működhessen. Íme néhány gyakori beállítás:

| SFTP-ügyfél | Műveletek |
|-------------|--------|
| Megnyerő | Ugrás a **Beállítások** > **beállításai hoz** > **Átvitel** > **szerkesztése** > **időbélyeg-megőrzésletiltása** > **Disable** |
| Filezilla | Ugrás az **Átvitt** > **fájlok időbélyegeinek megőrzése** > **című** tovább |
|||

Amikor egy eseményindító új fájlt talál, az eseményindító ellenőrzi, hogy az új fájl teljes, és nem részben írt. Előfordulhat például, hogy egy fájl folyamatban van, amikor az eseményindító ellenőrzi a fájlkiszolgálót. A részlegesen írott fájl visszaadásának elkerülése érdekében az eseményindító megjegyzi a legutóbbi módosításokat tartalmazó fájl időbélyegét, de nem adja vissza azonnal a fájlt. Az eseményindító csak a kiszolgáló ismételt lekérdezésekénekután adja vissza a fájlt. Néha ez a viselkedés az eseményindító lekérdezési időközének akár kétszeresét is okozhatja késleltetést okozhat.

## <a name="connect-to-sftp"></a>Csatlakozás az SFTP-hez

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)és nyissa meg a logikai alkalmazást a Logic App Designerben, ha még nem nyitott.

1. Üres logikai alkalmazások esetén a keresőmezőbe írja be szűrőként az "sftp" kifejezést. Az eseményindítók listában válassza ki a kívánt eseményindítót.

   – vagy –

   Meglévő logikai alkalmazások esetén az utolsó lépésben, ahol műveletet szeretne hozzáadni, válassza az **Új lépés lehetőséget.** A keresőmezőbe írja be szűrőként az "sftp" szót. A műveletek listájában jelölje ki a kívánt műveletet.

   Ha lépéseket szeretne hozzáadni a lépések közé, vigye az egérmutatót a lépések közötti nyíl fölé. Válassza ki a**+** megjelenő pluszjelet ( ), majd válassza **a Művelet hozzáadása**lehetőséget.

1. Adja meg a kapcsolathoz szükséges részleteket.

   > [!IMPORTANT]
   >
   > Amikor megadja az SSH személyes kulcsát az **SSH személyes** kulcstulajdonságában, kövesse az alábbi további lépéseket, amelyek segítenek abban, hogy megadja a tulajdonság teljes és helyes értékét. 
   > Egy érvénytelen kulcs a kapcsolat megszakadását okozza.

   Bár bármilyen szövegszerkesztőt használhat, az alábbi mintalépések bemutatják, hogyan másolhatja és illesztheti be helyesen a kulcsot a Notepad.exe használatával példaként.

   1. Nyissa meg az SSH titkoskulcsfájlt egy szövegszerkesztőben. Ezek a lépések példaként a Jegyzettömböt használják.

   1. A Jegyzettömb **Szerkesztés menüjében** válassza **az Összes kijelölése parancsot.**

   1. Válassza a > **Másolat** **szerkesztése**lehetőséget.

   1. A hozzáadott SFTP eseményindítóba vagy műveletbe illessze be a *teljes* kulcsot, amelyet másolt az **SSH titkoskulcs-tulajdonságába,** amely több sort is támogat. ***Győződjön meg róla, hogy beilleszti*** a kulcsot. ***Ne írja be manuálisan a kulcsot, és ne is szerkeszthesse azt.***

1. Ha végzett a kapcsolat részleteinek megadásával, válassza a **Létrehozás gombot.**

1. Adja meg a kiválasztott eseményindító vagy művelet szükséges adatait, és folytassa a logikai alkalmazás munkafolyamatának kiépítését.

## <a name="examples"></a>Példák

<a name="file-add-modified"></a>

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>SFTP-eseményindító: Fájl hozzáadásakor vagy módosításakor

Ez az eseményindító logikai alkalmazás munkafolyamatát indítja el, amikor egy SFTP-kiszolgálón hozzáadnak vagy módosítanak egy fájlt. Hozzáadhat például egy feltételt, amely ellenőrzi a fájl tartalmát, és lekéri a tartalmat annak alapján, hogy a tartalom megfelel-e egy adott feltételnek. Ezután hozzáadhat egy műveletet, amely beszerzi a fájl tartalmát, és a tartalmat az SFTP-kiszolgáló egyik mappájába helyezi.

**Példa vállalati például**: Ezzel az eseményindítóval figyelheti az Ügyfélrendeléseket képviselő új fájlok SFTP mappáját. Ezután használhat egy SFTP-műveletet, például a **Fájltartalom begetése,** így a rendelés tartalmát további feldolgozásra lekell kérni, és a rendeléseket egy rendelési adatbázisban tárolhatja.

<a name="get-content"></a>

### <a name="sftp-action-get-content"></a>SFTP-művelet: Tartalom bekéselése

Ez a művelet leveszi a tartalmat egy SFTP-kiszolgálón lévő fájlból. Így például hozzáadhatja az előző példából származó eseményindítót, és egy olyan feltételt, amelynek a fájl tartalmának meg kell felelnie. Ha a feltétel igaz, a művelet, amely megkapja a tartalmat futtathatja.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő OpenAPI (korábbi Swagger) leírása által leírt eseményindítók, műveletek és korlátok technikai részleteiért tekintse át az összekötő [referenciaoldalát.](/connectors/sftpconnector/)

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)
