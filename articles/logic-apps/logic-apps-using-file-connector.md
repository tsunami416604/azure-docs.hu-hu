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
ms.date: 01/13/2019
ms.openlocfilehash: c5128e904e540deeb3293fb687da4e8cafcfa1e0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57870994"
---
# <a name="connect-to-on-premises-file-systems-with-azure-logic-apps"></a>Csatlakozhat a helyszíni fájlrendszereket az Azure Logic Apps

A fájlrendszer-összekötő és az Azure Logic Apps automatizált feladatokat hozhat létre, és a munkafolyamatok, amelyek hozzon létre és kezelhet fájlokat egy helyi fájlt oszt meg, például:  

- Hozzon létre, beolvasása, hozzáfűzése, frissítése és törlése a fájlokat.
- Fájlok a mappákat vagy a gyökérmappák listája.
- Fájl tartalom és metaadatok beolvasása.

Ez a cikk bemutatja, hogyan csatlakoztathatja egy helyszíni fájlrendszerhez leírtak szerint ebben a példaforgatókönyvben: azt a fájlmegosztást a dropbox alkalmazásba feltöltött fájl másolása, és küldje el e-mailt. Biztonságos kapcsolódást és a helyszíni rendszerek, a logic apps használata eléréséhez a [a helyszíni adatátjáró](../logic-apps/logic-apps-gateway-connection.md). Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

Kövesse a példát, ezek az elemek szükségesek:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. 

* A logic apps csatlakozhat a helyszíni rendszerek, például a fájlkiszolgáló-rendszer, mielőtt kell [telepítése és beállítása egy helyszíni adatátjárót](../logic-apps/logic-apps-gateway-install.md). Ezzel a módszerrel megadhatja, hogy az átjáró telepítése használja, a logikai alkalmazás a file system-kapcsolat létrehozásakor.

* A [Dropbox-fiókjának](https://www.dropbox.com/), amely feliratkozhat az ingyenes. A fiók hitelesítő adatai szükségesek a logikai alkalmazás és a Dropbox-fiókjában közötti kapcsolat létrehozásához. 

* A számítógép, amelyen a fájlrendszer is használni szeretné a hozzáférést. Például ha a fájlrendszer ugyanazon a számítógépen telepíti az átjárót, szüksége a fiók hitelesítő adatait az adott számítógépen. 

* A szolgáltatói, például az Office 365 Outlook, Outlook.com vagy Gmail a Logic Apps által támogatott e-mail-fiók. Más szolgáltatók esetén [tekintse át az itt felsorolt összekötőket](https://docs.microsoft.com/connectors/). Ez a logikai alkalmazás Office 365 Outlook-fiókot használ. Ha más e-mail-fiókot használ, a lépések ugyanazok, de a felhasználói felület kissé eltérhet. 

* Alapvető ismeretek szerezhetők [létrehozása a logic apps](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ebben a példában szüksége lesz egy üres logikai alkalmazás.

## <a name="add-trigger"></a>Eseményindító hozzáadása

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és nyissa meg a logikai alkalmazás a Logikaialkalmazás-Tervező, ha nem, nyissa meg a már.

1. A keresőmezőbe írja be szűrőként "dropbox". Az eseményindítók listában jelölje ki az eseményindító: **Amikor létrejön egy fájl** 

   ![Dropbox-trigger kiválasztása](media/logic-apps-using-file-connector/select-dropbox-trigger.png)

1. Jelentkezzen be a Dropbox-fiókja hitelesítő adatait, és a Dropbox-adatokhoz való hozzáférés engedélyezése az Azure Logic Apps. 

1. Adja meg a szükséges információkat az eseményindító.

   ![Dropbox-eseményindító](media/logic-apps-using-file-connector/dropbox-trigger.png)

## <a name="add-actions"></a>Műveletek hozzáadása

1. Az eseményindító területén válassza a **következő lépés**. A Keresés mezőbe írja be a "file system" szűrőként. A műveletek listából válassza a következő műveletet: **Hozzon létre fájlt - fájlrendszer**

   ![Keresse meg a fájlrendszer-összekötő](media/logic-apps-using-file-connector/find-file-system-action.png)

1. Ha még nem rendelkezik egy kapcsolattal a fájlrendszerhez, kapcsolatot kér.

   ![Kapcsolat létrehozása](media/logic-apps-using-file-connector/file-system-connection.png)

   | Tulajdonság | Szükséges | Value | Leírás | 
   | -------- | -------- | ----- | ----------- | 
   | **Kapcsolat neve** | Igen | <*kapcsolat neve*> | A kapcsolat nevét | 
   | **Gyökérmappa** | Igen | <*root-folder-name*> | A fájlrendszer, például ha telepítette a helyszíni adatátjárót, például egy helyi mappába a számítógépen, ahol a helyszíni átjáró telepítve van, amely a gyökérmappában található vagy a mappát a számítógép által elérhető hálózati megosztásra. <p>Például:`\\PublicShare\\DropboxFiles` <p>A legfelső szintű mappa nem a fő szülőmappa, amely relatív elérési utakat az összes fájl kapcsolatos műveletekhez használható. | 
   | **Hitelesítés típusa** | Nem | <*a hitelesítési-típus*> | A fájl rendszer által használt, például hitelesítési típust **Windows** | 
   | **Felhasználónév** | Igen | <*tartomány*>\\<*felhasználónév*> | A számítógép, amelyekben a fájlrendszer tartozó felhasználónév | 
   | **Jelszó** | Igen | <*a jelszó*> | A jelszó a számítógép, amelyekben a fájlrendszer | 
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

* Szeretné segíteni az Azure Logic Apps és összekötők fejlesztését, szavazzon vagy küldje el javaslatait a [Azure Logic Apps felhasználói visszajelzési webhelyen](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [csatlakozás helyszíni adatokhoz](../logic-apps/logic-apps-gateway-connection.md) 
* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)
