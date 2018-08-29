---
title: Csatlakozhat a helyszíni – Azure Logic Apps fájlrendszerek |} A Microsoft Docs
description: Feladatok és a helyi fájlrendszer területhasználatát a fájlrendszer-összekötő az Azure Logic Appsben a helyszíni adatátjárón keresztül csatlakozó munkafolyamatok automatizálása
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan, LADocs
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: 41dd8ad721329c4c4d2761c9e4a37c640251dac3
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43125278"
---
# <a name="connect-to-on-premises-file-systems-with-azure-logic-apps"></a>Csatlakozhat a helyszíni fájlrendszereket az Azure Logic Apps

A fájlrendszer-összekötő és az Azure Logic Apps automatizált feladatokat hozhat létre, és a munkafolyamatok, amelyek hozzon létre és kezelhet fájlokat egy helyi fájlt oszt meg, például:  

- Hozzon létre, beolvasása, Hozzáfűzés, frissítése és fájlok törlése
- Fájlok a mappákat vagy a gyökérmappák listája.
- Fájl tartalom és metaadatok beolvasása.

Ez a cikk bemutatja, hogyan csatlakoztathatja egy helyszíni fájlrendszerhez leírtak szerint ebben a példaforgatókönyvben: azt a fájlmegosztást a dropbox alkalmazásba feltöltött fájl másolása, és küldje el e-mailt. Biztonságos kapcsolódást és a helyszíni rendszerek, a logic apps használata eléréséhez a [a helyszíni adatátjáró](../logic-apps/logic-apps-gateway-connection.md). Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. 

* A logic apps csatlakozhat a helyszíni rendszerek, például a fájlkiszolgáló-rendszer, mielőtt kell [telepítése és beállítása egy helyszíni adatátjárót](../logic-apps/logic-apps-gateway-install.md). Ezzel a módszerrel megadhatja, hogy az átjáró telepítése használja, a logikai alkalmazás a file system-kapcsolat létrehozásakor.

* A [Drobox fiók](https://www.dropbox.com/) és a felhasználói hitelesítő adatait

  A hitelesítő adatok engedélyezik a logikai alkalmazás, hozzon létre egy kapcsolatot, és a Drobox fiókjába. 

* Alapvető ismeretek szerezhetők [létrehozása a logic apps](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ebben a példában szüksége lesz egy üres logikai alkalmazás.

## <a name="add-trigger"></a>Trigger hozzáadása

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és nyissa meg a logikai alkalmazás a Logikaialkalmazás-Tervező, ha nem, nyissa meg a már.

1. A keresőmezőbe írja be szűrőként "dropbox". A eseményindítók listáról válassza ki a következő eseményindítót: **egy fájl létrehozásakor** 

   ![Dropbox-trigger kiválasztása](media/logic-apps-using-file-connector/select-dropbox-trigger.png)

1. Jelentkezzen be a Dropbox-fiókja hitelesítő adatait, és a Dropbox-adatokhoz való hozzáférés engedélyezése az Azure Logic Apps. 

1. Adja meg a szükséges információkat az eseményindító.

   ![Dropbox-eseményindító](media/logic-apps-using-file-connector/dropbox-trigger.png)

## <a name="add-actions"></a>Műveletek hozzáadása

1. Az eseményindító területén válassza a **következő lépés**. A Keresés mezőbe írja be a "file system" szűrőként. Válassza ezt a műveletet a műveletek listájának: **fájl - fájlrendszer létrehozása**

   ![Keresse meg a fájlrendszer-összekötő](media/logic-apps-using-file-connector/find-file-system-action.png)

1. Ha még nem rendelkezik egy kapcsolattal a fájlrendszerhez, kapcsolatot kér.

   ![Kapcsolat létrehozása](media/logic-apps-using-file-connector/file-system-connection.png)

   | Tulajdonság | Szükséges | Érték | Leírás | 
   | -------- | -------- | ----- | ----------- | 
   | **Kapcsolat neve** | Igen | <*kapcsolat neve*> | A kapcsolat nevét | 
   | **Gyökérmappa** | Igen | <*legfelső szintű mappanév*> | Az a fájlrendszerben, például helyi mappába a számítógépen, ahol a helyszíni átjáró telepítve van, vagy egy hálózati megosztásra, amely a számítógép hozzáférhessen a mappa gyökérmappájába. <p>Például:`\\PublicShare\\DropboxFiles` <p>A legfelső szintű mappa nem a fő szülőmappa, amely relatív elérési utakat az összes fájl kapcsolatos műveletekhez használható. | 
   | **Hitelesítés típusa** | Nem | <*a hitelesítési-típus*> | A fájl rendszer által használt, például hitelesítési típust **Windows** | 
   | **Felhasználónév** | Igen | <*tartomány*>\\<*felhasználónév*> | A korábban telepített data gateway tartozó felhasználónév | 
   | **Jelszó** | Igen | <*a jelszó*> | A korábban telepített data gateway jelszava | 
   | **Átjáró** | Igen | <*telepített átjáró-neve*> | A korábban telepített átjáró neve | 
   ||| 

1. Ha elkészült, kattintson a **Létrehozás** gombra. 

   A Logic Apps konfigurálja, és teszteli a kapcsolatot, és gondoskodik róla, hogy, hogy a kapcsolat megfelelően működik-e. 
   Ha a kapcsolat megfelelően van beállítva, a művelet, amely a korábban kiválasztott beállítások jelennek meg. 

1. Az a **fájl létrehozása** művelet, adja meg a részleteket a fájlok másolása a Dropboxból a helyszíni-fájlmegosztás gyökérmappájában. Az előző lépésekből kimenetek hozzáadásához kattintson a mezők, és válassza ki az elérhető mezők, amikor a dinamikus tartalmak listája jelenik meg.

   ![Fájl művelet létrehozása](media/logic-apps-using-file-connector/create-file-filled.png)

1. Most, hogy egy e-mailt küld, hogy a megfelelő felhasználók tudják, az új fájl kapcsolatos Outlook művelet hozzáadása lehetőséget. Adja meg a címzetteket, cím és az e-mail törzse. A vizsgálat, használhatja a saját e-mail-címét.

   ![E-mail küldése műveletet](media/logic-apps-using-file-connector/send-email.png)

1. Mentse a logikai alkalmazást. Tesztelje az alkalmazás egy fájl feltöltése a Dropboxba. 

   A logikai alkalmazás kell a helyszíni fájlmegosztási másolja a fájlt, és a másolt fájl e-mail küldése a címzetteknek.

## <a name="connector-reference"></a>Összekötő-referencia

További technikai részletek korlátok, eseményindítók és műveletek, amely ismerteti az összekötő OpenAPI által (korábbi nevén Swagger) leírását, tekintse át az összekötő [referencialapja](/connectors/fileconnector/).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Szeretné segíteni az Azure Logic Apps és összekötők fejlesztését, szavazzon vagy küldje el javaslatait a [Azure Logic Apps felhasználói visszajelzési webhelyen](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [csatlakozás helyszíni adatokhoz](../logic-apps/logic-apps-gateway-connection.md) 
* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)
