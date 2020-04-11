---
title: Csatlakozás a helyszíni fájlrendszerhez
description: Automatizálhatja azokat a feladatokat és munkafolyamatokat, amelyek a fájlrendszer-összekötővel a helyszíni adatátjárón keresztül kapcsolódnak a helyszíni adatátjáróhoz az Azure Logic Apps-ben
services: logic-apps
ms.suite: integration
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 01/13/2019
ms.openlocfilehash: ab17137f162b893b54942d870b07a36f87d1b71d
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115075"
---
# <a name="connect-to-on-premises-file-systems-with-azure-logic-apps"></a>Csatlakozás helyszíni fájlrendszerekhez az Azure Logic Appsből

Az Azure Logic Apps és a Fájlrendszer-összekötő segítségével automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek például helyszíni fájlmegosztáson hoznak létre és kezelnek fájlokat:

- Fájlok létrehozása, bekerülése, hozzáfűzése, frissítése és törlése.
- Fájlok listázása mappákban vagy gyökérmappákban.
- Fájltartalom és metaadatok beszerezése.

Ez a cikk bemutatja, hogyan csatlakozhat egy helyszíni fájlrendszerhez a példa forgatókönyve szerint: másolja a Dropboxba feltöltött fájlt egy fájlmegosztásra, majd küldjön egy e-mailt. A helyszíni rendszerek biztonságos csatlakoztatásához és eléréséhez a logikai alkalmazások a [helyszíni adatátjárót](../logic-apps/logic-apps-gateway-connection.md)használják. Ha most kezdi meg a logikai alkalmazásokat, tekintse át [a Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md). Az összekötő-specifikus műszaki információkat a [Fájlrendszer összekötő hivatkozási útmutatójában talál.](/connectors/filesystem/)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Mielőtt a logikai alkalmazásokat a helyszíni rendszerekhez, például a fájlrendszer-kiszolgálóhoz csatlakoztathatja, telepítenie kell és be kell [állítania egy helyszíni adatátjárót.](../logic-apps/logic-apps-gateway-install.md) Így megadhatja, hogy az átjáró telepítése, amikor létrehozza a fájlrendszer-kapcsolat a logikai alkalmazásból.

* A [Dropbox számla](https://www.dropbox.com/), amely akkor iratkozzon fel ingyen. A fiók hitelesítő adatai szükségesek a logikai alkalmazás és a Dropbox-fiók közötti kapcsolat létrehozásához.

* Hozzáférés a használni kívánt fájlrendszert tartalmazó számítógéphez. Ha például az adatátjárót ugyanarra a számítógépre telepíti, mint a fájlrendszert, akkor az adott számítógép fiókhitelesítő adataira van szükség.

* A Logic Apps által támogatott szolgáltatótól, például az Office 365 Outlook, a Outlook.com vagy a Gmail által támogatott e-mail fiók. Más szolgáltatók esetén [tekintse át az itt felsorolt összekötőket](https://docs.microsoft.com/connectors/). Ez a logikai alkalmazás Office 365 Outlook-fiókot használ. Ha más e-mail-fiókot használ, a lépések ugyanazok, de a felhasználói felület kissé eltérhet.

* Alapvető ismeretek [a logikai alkalmazások létrehozásához.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Ebben a példában egy üres logikai alkalmazásra van szükség.

## <a name="add-trigger"></a>Trigger hozzáadása

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)és nyissa meg a logikai alkalmazást a Logic App Designerben, ha még nem nyitott.

1. A keresőmezőbe írja be szűrőként a "dropbox" kifejezést. Az eseményindítók listájában válassza ezt az eseményindítót: **Fájl létrehozásakor**

   ![Dropbox-eseményindító kiválasztása](media/logic-apps-using-file-connector/select-dropbox-trigger.png)

1. Jelentkezzen be Dropbox-fiókjának hitelesítő adataival, és engedélyezze a hozzáférést a Dropbox-adatokhoz az Azure Logic Apps számára.

1. Adja meg az eseményindítóhoz szükséges információkat.

   ![Dropbox-eseményindító](media/logic-apps-using-file-connector/dropbox-trigger.png)

## <a name="add-actions"></a>Műveletek hozzáadása

1. Az eseményindító alatt válassza a **Következő lépés lehetőséget.** A keresőmezőbe írja be szűrőként a "fájlrendszer" kifejezést. A műveletlistából válassza a következő műveletet: **Fájl létrehozása**

   ![Fájlrendszer-összekötő keresése](media/logic-apps-using-file-connector/find-file-system-action.png)

1. Ha még nem rendelkezik kapcsolattal a fájlrendszerhez, a rendszer kéri, hogy hozzon létre egy kapcsolatot.

   ![Kapcsolat létrehozása](media/logic-apps-using-file-connector/file-system-connection.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   | -------- | -------- | ----- | ----------- |
   | **Kapcsolat neve** | Igen | <*kapcsolat neve*> | A kapcsolathoz kívánt név |
   | **Gyökérmappa** | Igen | <*gyökér-mappa-név*> | A fájlrendszer gyökérmappája például ha telepítette a helyszíni adatátjárót, például egy helyi mappát azon a számítógépen, amelyen a helyszíni adatátjáró telepítve van, vagy egy hálózati megosztás mappáját, amelyhez a számítógép hozzáférhet. <p>Például:`\\PublicShare\\DropboxFiles` <p>A gyökérmappa a fő szülőmappa, amely az összes fájlhoz kapcsolódó művelet relatív elérési útjaihoz használatos. |
   | **Hitelesítés típusa** | Nem | <*auth-típusú*> | A fájlrendszer által használt hitelesítés típusa: **Windows** |
   | **Felhasználónév** | Igen | <*domain*>tartomány\\<*felhasználóneve*> | Annak a számítógépnek a felhasználóneve, amelyen a fájlrendszer található |
   | **Jelszó** | Igen | <*a jelszó*> | Annak a számítógépnek a jelszava, amelyen a fájlrendszer található |
   | **Átjáró** | Igen | <*telepített átjáró-név*> | A korábban telepített átjáró neve |
   |||||

1. Ha elkészült, kattintson a **Létrehozás** gombra.

   A Logic Apps konfigurálja és teszteli a kapcsolatot, így meggyőződve arról, hogy a kapcsolat megfelelően működik. Ha a kapcsolat megfelelően van beállítva, a korábban kiválasztott művelet beállításai jelennek meg.

1. A **Fájl létrehozása** műveletben adja meg a fájlok Dropboxból a helyszíni fájlmegosztás gyökérmappájába másolásának részleteit. Ha az előző lépésekből szeretne kimeneteket hozzáadni, kattintson a mezőkbe, és válasszon a rendelkezésre álló mezők közül, amikor a dinamikus tartalomlista megjelenik.

   ![Fájlművelet létrehozása](media/logic-apps-using-file-connector/create-file-filled.png)

1. Most adjon hozzá egy Outlook-műveletet, amely e-mailt küld, hogy a megfelelő felhasználók tudjanak az új fájlról. Adja meg az e-mail címzettjeit, címét és törzsét. A teszteléshez használhatja a saját e-mail címét.

   ![E-mail küldése művelet](media/logic-apps-using-file-connector/send-email.png)

1. Mentse a logikai alkalmazást. Tesztelje az alkalmazást egy fájl Dropboxba való feltöltésével.

   A logikai alkalmazásnak át kell másolnia a fájlt a helyszíni fájlmegosztásba, és e-mailt kell küldenie a címzetteknek a másolt fájlról.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötővel kapcsolatos további technikai részleteket, például az eseményindítókat, műveleteket és korlátokat az összekötő Swagger-fájlja szerint lásd az [összekötő referencialapján.](https://docs.microsoft.com/connectors/fileconnector/)

> [!NOTE]
> [Az integrációs szolgáltatási környezetben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)lévő logikai alkalmazások esetében az összekötő ISE-címkével ellátott verziója az [ISE-üzenetkorlátokat](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) használja.

## <a name="next-steps"></a>További lépések

* További információ a [helyszíni adatokhoz való csatlakozásról](../logic-apps/logic-apps-gateway-connection.md) 
* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)
