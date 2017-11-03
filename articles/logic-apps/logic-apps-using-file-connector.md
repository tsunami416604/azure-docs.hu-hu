---
title: "A helyszíni - Azure Logic Apps fájlrendszerek csatlakozni |} Microsoft Docs"
description: "A helyszíni fájlrendszer csatlakozni erről a logic app munkafolyamatainak a helyszíni az átjáró és a fájlrendszer-összekötő"
keywords: "fájl telephelyükön található rendszerekhez"
services: logic-apps
author: derek1ee
manager: anneta
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: LADocs; deli
ms.openlocfilehash: 7738b3346af49cb8aa811eb17003d1b72b1bbe46
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-on-premises-file-systems-from-logic-apps-with-the-file-system-connector"></a>Csatlakozás helyszíni fájlrendszer a logic Apps alkalmazásokból, a fájlrendszer-összekötőn keresztül

Adatok kezelése és biztonságos hozzáférés a helyszíni erőforrásokhoz, a logic apps segítségével helyszíni data gateway. Ez a cikk bemutatja, hogyan csatlakoztathatja a helyszíni keresztül alapvető ebben a példaforgatókönyvben a fájlrendszer: másolja át a fájlt, amely fájlmegosztásba a dropbox alkalmazásba feltöltött, majd küldje el e-mailt.

## <a name="prerequisites"></a>Előfeltételek

* Töltse le a legújabb [helyszíni adatátjáró](https://www.microsoft.com/download/details.aspx?id=53127).

* Telepítse és állítsa be a legújabb helyszíni data gateway, 1.15.6150.1 verzió vagy újabb. Az útmutató: [kapcsolódás a helyi kiszolgálón tárolt olyan adatforrások](http://aka.ms/logicapps-gateway). Telepítenie kell az átjárót a helyszíni gépen ezeket a lépéseket a folytatás előtt.

* Alapszintű ismerete [logic Apps alkalmazások létrehozása](../logic-apps/logic-apps-create-a-logic-app.md)

## <a name="add-trigger-and-actions-for-connecting-to-your-file-system"></a>Adja hozzá eseményindító és műveleteket a fájlrendszer való kapcsolódáshoz

1. Üres logikai alkalmazás létrehozása. Első lépésként adja hozzá a az eseményindító: **Dropbox - fájl létrehozásakor** 

2. Válassza ki az eseményindító **+ a következő lépés** > **művelet hozzáadása**. 

3. A keresési mezőbe írja be a "file system" szűrőként. Amikor megjelenik a műveleteket a fájlrendszer-összekötőhöz, válassza ki a **fájlrendszer - fájl létrehozása** művelet. 

   ![Keresse meg a fájl összekötő](media/logic-apps-using-file-connector/search-file-connector.png)

4. Ha még nem rendelkezik a kapcsolat a fájlrendszerhez, a program kéri, VPN-kapcsolat létrehozásához. 

5. Válassza ki **keresztül, a helyszíni adatátjáró**. Amikor a kapcsolat tulajdonságai megjelennek, állítsa be a kapcsolatot a táblázatban megadottak.

   ![Kapcsolat beállítása](media/logic-apps-using-file-connector/create-file.png)

   | Beállítás | Leírás |
   | ------- | ----------- |
   | **Gyökérmappa** | Adja meg a legfelső szintű mappát a fájlrendszerhez. Megadhat egy helyi mappába a számítógépen, ahol telepítve van a helyszíni data gateway, vagy a mappa lehet a számítógép által elérhető hálózati megosztáson. <p>**Tipp:** a gyökérmappája a fő szülőmappa, relatív útvonalakat minden fájl kapcsolatos műveletekhez használt. | 
   | **Hitelesítés típusa** | A fájlrendszer által használt hitelesítés típusa | 
   | **Felhasználónév** | Adja meg a felhasználónév {*tartomány*\\*felhasználónév*} a korábban telepített átjáró. | 
   | **Jelszó** | Adja meg a jelszót a korábban telepített átjárót. | 
   | **Átjáró** | Válassza ki a korábban telepített átjárót. | 
   ||| 

6. Miután megadta a kapcsolat adatai, válassza ki a **létrehozása**. 

   A Logic Apps konfigurálja, és teszteli a hálózati kapcsolatot, annak biztosítása, hogy a kapcsolat megfelelően működik-e. 
   Ha a kapcsolat megfelelően van beállítva, a művelet, amely a korábban kiválasztott beállítások jelennek meg. 
   A fájl system-összekötő mostantól készen áll a használatra.

7. Állítsa be a **létrehozás fájl** a fájlok másolását Dropbox a gyökérmappában található, a helyszíni fájlmegosztásba a műveletet.

   ![Fájl művelet létrehozása](media/logic-apps-using-file-connector/create-file-filled.png)

8. Ez a művelet számára a fájlok másolása után egy e-mailt küld, így az új fájl megfelelő felhasználók funkcióját, az Outlook művelet hozzáadása. Adja meg a címzetteket, cím és az e-mail. 

   Az a **dinamikus tartalom** listában adatok kimenetek közül választhat a fájl összekötő, hozzáadhat további részleteket az e-mailt.

   ![Küldjön e-mailek művelet](media/logic-apps-using-file-connector/send-email.png)

9. Mentse a Logic Apps alkalmazást. Tesztelje az alkalmazás feltölteni a fájlt a dropbox alkalmazásba. A helyszíni fájlmegosztáshoz beolvasása átmásolhatja a fájlt, és a műveletekre vonatkozó e-mailt kell kapnia.

Gratulálunk, most már rendelkezik egy működő logikai alkalmazás, amely képes csatlakozni a helyszíni fájlrendszer. 

Próbálja meg az összekötő kínál, például más elavuló felfedezése:

- Fájl létrehozása
- Mappában lévő fájlok listázása
- Fájl hozzáfűzése
- Fájl törlése
- Fájl tartalmának lekérdezése
- Fájl tartalmának beolvasása elérési út segítségével
- Fájl metaadatot beszerezni
- Fájlmetaadatok beolvasása elérési út segítségével
- Gyökérmappában lévő fájlok listázása
- Fájl frissítése

## <a name="view-the-swagger"></a>A swagger megtekintése

Tekintse meg a [részletek swagger](/connectors/fileconnector/). 

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Azure Logic Apps alkalmazások és összekötők tökéletesítése céljából szavazhatnak, vagy küldje el a következő ötleteket a [Azure Logic Apps User Voice hely](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Következő lépések

* [Csatlakozás helyszíni adatokhoz](../logic-apps/logic-apps-gateway-connection.md) 
* [Logikai alkalmazások figyelése](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Vállalati integrációs B2B forgatókönyvek esetén](../logic-apps/logic-apps-enterprise-integration-overview.md)
