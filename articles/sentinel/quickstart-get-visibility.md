---
title: Azure Sentinel rövid útmutató – első lépések az Azure Sentinel előzetes verziójával | Microsoft Docs
description: Azure Sentinel rövid útmutató – az Azure Sentinel használatának első lépései
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5a4ae93c-d648-41fb-8fb8-96a025d2f73e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: rkarlin
ms.openlocfilehash: c6434b4f5808c349d5e0ab04dafc5233f6ec63ac
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780468"
---
# <a name="quickstart-get-started-with-azure-sentinel-preview"></a>Gyors útmutató: Ismerkedés az Azure Sentinel előzetes verziójával

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Ebből a rövid útmutatóból megtudhatja, hogyan lehet gyorsan megtekinteni és figyelni, hogy mi történik a környezetben az Azure Sentinel használatával. Miután csatlakoztatta az adatforrásokat az Azure Sentinelhez, azonnal megjelenítheti és elemezheti az adatait, hogy tudja, mi történik az összes csatlakoztatott adatforráson. Az Azure Sentinel olyan irányítópultokat biztosít, amelyek az Azure-ban már elérhető eszközök teljes körű hatékonyságát, valamint a naplókat és a lekérdezéseket is lehetővé teszik a beépített táblázatokhoz és diagramokhoz. Használhatja a beépített irányítópultokat, vagy létrehozhat egy új irányítópultot egyszerűen, akár teljesen, akár egy meglévő irányítópulton. 

## <a name="get-visualization"></a>Vizualizáció beolvasása

A környezettel kapcsolatos események megjelenítéséhez és elemzéséhez először tekintse meg az Áttekintés irányítópultot, és kérje meg a szervezete biztonsági helyzetének a megismerését. A csempék minden elemére kattintva megtekintheti azokat a nyers adatforrásokat, amelyekről létrehozták őket. A zaj csökkentése és a riasztások számának csökkentése érdekében az Azure Sentinel fúziós technikát használ a riasztások incidensekkel való összekapcsolásához. az incidensek a kapcsolódó riasztások csoportjai, amelyek együttesen megvizsgálható és elhárítható, végrehajtható incidenseket hoznak létre.

- A Azure Portal válassza az Azure Sentinel lehetőséget, majd válassza ki a figyelni kívánt munkaterületet.

  ![Az Azure Sentinel áttekintése](./media/qs-get-visibility/overview.png)

- A felső eszköztáron látható, hogy hány esemény van kiválasztva a kiválasztott időszakban, és összehasonlítja azt az előző 24 órában. Az eszköztár tájékoztat ezekről az eseményekről, az aktivált riasztásokról (a kis szám az elmúlt 24 órában változik), majd elmondja Önnek, hogy hányan nyitott, folyamatban és lezárult. Ellenőrizze, hogy az események száma nem drámai növekedést vagy csökkenést eredményez-e. Ha van eldobás, lehetséges, hogy a kapcsolatok leállították a jelentést az Azure Sentinelnek. Ha nő a növekedés, előfordulhat, hogy gyanús esemény történt. Ellenőrizze, hogy vannak-e új riasztások.

   ![Azure Sentinel tölcsér](./media/qs-get-visibility/funnel.png)

Az áttekintő lap fő törzse áttekintést nyújt a munkaterület biztonsági állapotáról:

- **Események és riasztások az idő múlásával**: Felsorolja az események számát, valamint azt, hogy az események hány riasztást hoztak létre. Ha olyan tüske jelenik meg, amely szokatlan, akkor riasztásokat kell látnia – ha van valami szokatlan, hogy van-e olyan esemény, amely az események között van, de nem látja a riasztásokat, aggodalomra adhat okot.

- **Lehetséges kártékony események**: Ha a rendszer forgalmat észlel a kártékonyként ismert forrásokból, az Azure Sentinel riasztást küld a térképen. Ha a narancssárga látható, akkor bejövő forgalom: valaki megpróbál hozzáférni a szervezethez egy ismert kártékony IP-címről. Ha a kimenő (vörös) tevékenységet látja, az azt jelenti, hogy a hálózatról származó adatok a szervezetből egy ismert kártékony IP-címről áramlanak.

   ![Azure Sentinel-Térkép](./media/qs-get-visibility/map.png)


- **Legutóbbi incidensek**: A legutóbbi incidensek, súlyosságuk és az incidenshez kapcsolódó riasztások számának megtekintéséhez. Ha egy adott típusú riasztás hirtelen csúcsát látja, az azt jelentheti, hogy jelenleg fut aktív támadás. Ha például hirtelen 20 Pass-The-hash típusú eseményt használ az Azure ATP-ből, lehetséges, hogy valaki megpróbálja megtámadni Önt.

- **Adatforrás-rendellenességek**: A Microsoft adatelemzői olyan modelleket hoztak létre, amelyek folyamatosan keresik az adatforrásokból származó adatvesztési rendellenességeket. Ha nincs rendellenesség, semmi sem jelenik meg. Ha rendellenességek észlelhetők, érdemes részletesen bemutatni, hogy mi történt. Kattintson például az Azure-tevékenység csúcsára. A diagramra kattintva megtekintheti, hogy mikor történt a nyárs, majd szűrheti az adott időszakban bekövetkezett tevékenységeket, hogy megtudja, mi okozta a nyársat.

   ![Azure Sentinel-Térkép](./media/qs-get-visibility/anomolies.png)

## Beépített irányítópultok használata<a name="dashboards"></a>

A beépített irányítópultok integrált adatokkal szolgálnak a csatlakoztatott adatforrásokból, így részletesen megismerheti az ezekben a szolgáltatásokban létrehozott eseményeket. A beépített irányítópultok közé tartozik az Azure-azonosító, az Azure-tevékenység eseményei és a helyszíni adatok, amelyek a kiszolgálókról származó Windows-eseményektől, az első féltől származó riasztásokból, a tűzfalakon, az Office 365-ből és a Windowson alapuló nem biztonságos protokollokból származó adatokat tartalmazhatnak. események.

1. A **Beállítások**területen válasszaaz irányítópultok lehetőséget. A **telepítés**alatt láthatja az összes telepített irányítópultot. Az **összes** lehetőség alatt megtekintheti a telepítéshez elérhető beépített irányítópultok teljes katalógusát. 
2. Egy adott irányítópult megkeresésével megtekintheti az egyes ajánlatok teljes listáját és leírását. 
3. Feltételezve, hogy az Azure AD-t használja az Azure Sentinel használatának megkezdéséhez, javasoljuk, hogy legalább a következő irányítópultokat telepítse:
   - **Azure ad**: Használja az alábbiak egyikét vagy mindkettőt:
       - Az **Azure ad-bejelentkezések** időben elemzik a bejelentkezéseket, hogy megjelenjenek-e anomáliák. Ez az irányítópult lehetővé teszi, hogy az alkalmazások, az eszközök és a helyszínek nem tudnak bejelentkezni, így észreveheti, ha valami szokatlan történik. Figyeljen a több sikertelen bejelentkezésre. 
       - Az **Azure ad-naplók** elemzik a rendszergazdai tevékenységeket, például a felhasználók változásait (Hozzáadás, eltávolítás stb.), a csoportok létrehozását és a módosításokat.  

   - Adjon hozzá egy irányítópultot a tűzfalhoz. Adja meg például a Palo Alto irányítópultot. Az irányítópult elemzi a tűzfal forgalmát, és összekapcsolja a tűzfal adatai és a veszélyforrás események közötti összefüggéseket, és kiemeli a gyanús eseményeket az entitások között. Az irányítópultok információt biztosítanak a forgalom trendjeiről, és lehetővé teszi az eredmények részletezését és szűrését. 

      ![Pal Alto-irányítópult](./media/qs-get-visibility/palo-alto-week-query.png)


Az irányítópultokat a főlekérdezés ![gomb](./media/qs-get-visibility/edit-query-button.png)szerkesztésével is testreszabhatja. ![A gombra](./media/qs-get-visibility/go-to-la-button.png) kattintva megtekintheti a log Analytics a [lekérdezés szerkesztéséhez](../azure-monitor/log-query/get-started-portal.md), és kiválaszthatja a három pontot (...), és kiválaszthatja a csempék **testreszabása**lehetőséget, amely lehetővé teszi a Főidő szűrő szerkesztését, vagy a Az irányítópult adott csempéi.

További információ a lekérdezések [használatáról: oktatóanyag: Vizualizációs adatLog Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md)

### <a name="add-a-new-tile"></a>Új csempe hozzáadása

Ha új csempét szeretne felvenni, azt hozzáadhatja egy meglévő irányítópulthoz, vagy létrehozhatja azt, vagy egy beépített Azure Sentinel-irányítópultot is. 
1. A log Analytics-ban hozzon létre egy csempét a [következő oktatóanyagban található utasítások használatával: Vizualizációs adatLog Analyticsek](../azure-monitor/learn/tutorial-logs-dashboards.md). 
2. Miután létrehozta a csempét, a **PIN**elemnél válassza ki azt az irányítópultot, amelyben meg szeretné jeleníteni a csempét.

## <a name="create-new-dashboards"></a>Új irányítópultok létrehozása
Létrehozhat egy új irányítópultot a semmiből, vagy használhat egy beépített irányítópultot az új irányítópult alapjaként.

1. Új irányítópult létrehozásához válassza az irányítópultok, majd az **+ új irányítópult**elemet.
2. Válassza ki azt az előfizetést, amelyben az irányítópult létre van hozva, és adjon meg egy leíró nevet. Minden irányítópult egy olyan Azure-erőforrás, mint bármely más, és hozzárendelheti az RBAC-szerepköröket, és korlátozhatja, hogy ki férhet hozzá. 
3. Annak engedélyezéséhez, hogy az irányítópulton megjelenjenek a vizualizációk, meg kell osztania azt. Kattintson a **megosztás** , majd a **felhasználók kezelése**lehetőségre. 
 
1. A **hozzáférés** -és **szerepkör** -hozzárendeléseket a többi Azure-erőforráshoz hasonlóan használhatja. További információ: Azure- [irányítópultok megosztása RBAC használatával](../azure-portal/azure-portal-dashboard-share-access.md).


## <a name="new-dashboard-examples"></a>Új irányítópult-példák

A következő minta lekérdezéssel összehasonlíthatja a forgalom trendjeinek hetek közötti összehasonlítását. Egyszerűen átválthatja, hogy melyik eszköz gyártója és adatforrás futtatja a lekérdezést. Ez a példa a Windows SecurityEvent használja, átválthatja a AzureActivity-on vagy a CommonSecurityLog-on való futtatásra bármely más tűzfalon.

     |where DeviceVendor = = "Palo Alto Networks":
      // week over week query
      SecurityEvent
      | where TimeGenerated > ago(14d)
      | summarize count() by bin(TimeGenerated, 1d)
      | extend Week = iff(TimeGenerated>ago(7d), "This Week", "Last Week"), TimeGenerated = iff(TimeGenerated>ago(7d), TimeGenerated, TimeGenerated + 7d)


Előfordulhat, hogy olyan lekérdezést szeretne létrehozni, amely több forrásból származó adatokkal rendelkezik. Létrehozhat egy olyan lekérdezést, amely megkeresi Azure Active Directory naplókat az imént létrehozott új felhasználók számára, majd ellenőrzi az Azure-naplókat, hogy a felhasználó elindította-e a szerepkör-hozzárendelés változásait 24 órán belül. Ez az irányítópulton a gyanús tevékenység jelenik meg:

    AuditLogs
    | where OperationName == "Add user"
    | project AddedTime = TimeGenerated, user = tostring(TargetResources[0].userPrincipalName)
    | join (AzureActivity
    | where OperationName == "Create role assignment"
    | project OperationName, RoleAssignmentTime = TimeGenerated, user = Caller) on user
    | project-away user1

Különböző irányítópultokat hozhat létre az adatelemzést végző személy és a keresett személyek szerepköre alapján. Létrehozhat például egy irányítópultot a hálózati rendszergazdának, amely magában foglalja a tűzfal adatait is. Irányítópultokat is létrehozhat, attól függően, hogy milyen gyakran szeretné megtekinteni őket, hogy vannak-e olyan dolgok, melyeket naponta szeretne ellenőrizni, és ha szeretné, hogy a rendszer óránként ellenőrizze az Azure AD-bejelentkezéseket, érdemes megkeresni a anomali es. 

## <a name="create-new-detections"></a>Új észlelések létrehozása

Az [Azure sentinelhez csatlakoztatott](connect-data-sources.md) adatforrások észlelésének előállítása a szervezeten belüli fenyegetések kivizsgálásához.

Új észlelés létrehozásakor kihasználhatja a Microsoft biztonsági kutatói által készített beépített észleléseket, amelyek a csatlakoztatott adatforrásokhoz vannak igazítva.

1. [A GitHub-Közösségben](https://github.com/Azure/Azure-Sentinel/tree/master/Detections) lépjen az **észlelések** mappára, és válassza ki a megfelelő mappákat.
   ![kapcsolódó mappák](./media/qs-get-visibility/detection-folders.png)
 
3.  Lépjen az **elemzés** lapra, és válassza a **Hozzáadás**lehetőséget.
   ![szabály létrehozása a Log Analyticsban](./media/qs-get-visibility/query-params.png)

3.  Másolja az összes paramétert a szabályba, és kattintson a **Létrehozás**gombra.
   ![riasztási szabály létrehozása](./media/qs-get-visibility/create-alert-rule.png)

 
## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban megtanulta, hogyan kezdheti el az Azure Sentinel használatát. Folytassa az Oktatóanyaggal a [fenyegetések észleléséhez](tutorial-detect-threats.md).
> [!div class="nextstepaction"]
> [Észlelheti](tutorial-detect-threats.md) a fenyegetésekre adott válaszok automatizálását.

