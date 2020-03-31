---
title: 'Rövid útmutató: Első lépések az Azure Sentinelrel'
description: Azure Sentinel– Első lépések az Azure Sentinelrel
services: sentinel
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.custom: mvc, fasttrack-edit
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 95dcc135593c566eb1319ed52df3df6c1ada6609
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80067685"
---
# <a name="quickstart-get-started-with-azure-sentinel"></a>Rövid útmutató: Első lépések az Azure Sentinelrel




Ebben a rövid útmutatóban megtudhatja, hogyan tekintheti meg és figyelheti gyorsan, hogy mi történik a környezetében az Azure Sentinel használatával. Miután csatlakoztatta az adatforrásokat az Azure Sentinelhez, azonnali vizualizációt és adatelemzést kaphat, hogy megtudhassa, mi történik az összes csatlakoztatott adatforrásban. Az Azure Sentinel olyan munkafüzeteket kínál, amelyek az Azure-ban már elérhető eszközök, valamint a beépített táblák és diagramok teljes erejét biztosítják, hogy a naplók és lekérdezések elemzését biztosítsa. Használhat beépített munkafüzeteket, vagy egyszerűen, teljesen új munkafüzetet hozhat létre, vagy meglévő munkafüzet alapján. 

## <a name="get-visualization"></a>Képi megjelenítés

A környezetben zajló események vizualizálásához és elemzéséhez először tekintse meg az áttekintő irányítópultot, és képet kaphat a szervezet biztonsági helyzetéről. Ezeknek a csempéknek az egyes elemeire kattintva leáshat a nyers adatokra, amelyekből létrehozták őket. A zaj csökkentése és a felülvizsgálattal és vizsgálattal kapcsolatos riasztások számának minimalizálása érdekében az Azure Sentinel fúziós technikát használ a riasztások incidensekre való korrelációjára. **az incidensek** kapcsolódó riasztások csoportjai, amelyek együttesen olyan perelhető incidenst hoznak létre, amelyet kivizsgálhat és megoldhat.

- Az Azure Portalon válassza az Azure Sentinel, majd válassza ki a figyelni kívánt munkaterületet.

  ![Azure Sentinel – áttekintés](./media/qs-get-visibility/overview.png)

- A felső eszköztár megmutatja, hogy hány eseményt kapott a kiválasztott időszakban, és összehasonlítja azt az előző 24 órával. Az eszköztár ebből az eseményből megmondja az aktivált riasztásokat (a kis szám az elmúlt 24 óra változását jelzi), majd megmondja, hogy hányan vannak nyitva, folyamatban vannak és vannak bezárva. Ellenőrizze, hogy nincs-e drámai növekedés vagy csökkenés az események számában. Ha van egy csepp, lehet, hogy egy kapcsolat leállt az Azure Sentinel jelentéskészítés. Ha növekszik, valami gyanús történhetett. Ellenőrizze, hogy vannak-e új riasztásai.

   ![Azure Sentinel-csatorna](./media/qs-get-visibility/funnel.png)

Az áttekintő lap fő törzse egy pillantással áttekinti a munkaterület biztonsági állapotát:

- **Események és riasztások idővel**: Felsorolja az események számát, és hány riasztásjött létre ezekből az eseményekből. Ha szokatlan tüske jelenik meg, akkor figyelmeztetéseket kell látnia – ha van valami szokatlan, ahol az események kiugrása van, de nem lát riasztásokat, az aggodalomra adhat okot.

- **Lehetséges rosszindulatú események:** Ha a forgalom észlelése olyan forrásokból, amelyekről ismert, hogy rosszindulatú, az Azure Sentinel figyelmezteti Önt a térképen. Ha narancssárga, az bejövő forgalom: valaki egy ismert rosszindulatú IP-címről próbál hozzáférni a szervezethez. Ha kimenő (piros) tevékenységet lát, az azt jelenti, hogy a hálózatról származó adatok at egy ismert rosszindulatú IP-címre továbbítják a szervezetből.

   ![Azure Sentinel térkép](./media/qs-get-visibility/map.png)


- **Legutóbbi események:** A legutóbbi incidensek, azok súlyosságának és az incidenshez kapcsolódó riasztások számának megtekintéséhez. Ha egy adott típusú riasztás hirtelen csúcsaként jelenik meg, az azt jelentheti, hogy jelenleg aktív támadás fut. Például ha az Azure ATP 20 Pass-the-hash eseményének hirtelen csúcsa van, lehetséges, hogy valaki jelenleg ön ellen próbálja megtámadni.

- **Adatforrás-anomáliák:** A Microsoft adatelemzői olyan modelleket hoztak létre, amelyek folyamatosan keresik az adatforrásokból származó adatokat anomáliák után. Ha nincsenek anomáliák, semmi sem jelenik meg. Ha anomáliákat észlelünk, mélymerülést kell látnod, mi történt. Például kattintson a tüske az Azure-tevékenység. A **Diagram** gombra kattintva megtekintheti, hogy mikor történt a tüske, majd szűrheti az adott időszakban történt tevékenységeket, hogy lássa, mi okozta a csúcsot.

   ![Azure Sentinel térkép](./media/qs-get-visibility/anomolies.png)

## <a name="use-built-in-workbooks"></a>Beépített munkafüzetek használata<a name="dashboards"></a>

A beépített munkafüzetek integrált adatokat biztosítanak a csatlakoztatott adatforrásokból, hogy részletesen átvethesse magát a szolgáltatásokban létrehozott eseményekben. A beépített munkafüzetek közé tartoznak az Azure AD, az Azure-tevékenységesemények és a helyszíni események, amelyek lehetnek a Windows-eseményekből származó adatok kiszolgálókról, külső riasztásokból, bármely külső féltől, beleértve a tűzfalforgalmi naplókat, az Office 365-öt és a Windows on alapuló nem biztonságos protokollokat Események. A munkafüzetek az Azure Monitor-munkafüzeteken alapulnak, így jobb testreszabhatóságot és rugalmasságot biztosítanak saját munkafüzete megtervezéséhez. További információt a Munkafüzetek című [témakörben talál.](../azure-monitor/app/usage-workbooks.md)

1. A **Beállítások csoportban**válassza **a Munkafüzetek**lehetőséget. A **Telepített csoportban**az összes telepített munkafüzet látható. A **Minden**csoportban megtekintheti a telepítésre rendelkezésre álló beépített munkafüzetek teljes gyűjteményét. 
2. Keressen egy adott munkafüzetet, hogy a teljes listát és az egyes ajánlatok leírását láthassa. 
3. Feltéve, hogy az Azure AD-t használja az Azure Sentinel üzembe munkájához, javasoljuk, hogy legalább a következő munkafüzeteket telepítse:
   - **Azure AD:** Használja az alábbi lehetőségek egyikét vagy mindkettőt:
       - **Az Azure AD-bejelentkezések** elemzi bejelentkezések idővel, hogy ha vannak anomáliák. Ez a munkafüzet alkalmazások, eszközök és helyek sikertelen bejelentkezéseit biztosítja, így egy pillantással észreveheti, ha valami szokatlan történik. Figyeljen több sikertelen bejelentkezésre. 
       - **Az Azure AD naplózási naplók** elemzi a rendszergazdai tevékenységek, például a felhasználók változásai (hozzáadása, eltávolítása, stb), a csoport létrehozása és a módosítások.  

   - Munkafüzet hozzáadása a tűzfalhoz. Adja hozzá például a Palo Alto munkafüzetet. A munkafüzet elemzi a tűzfal forgalmát, korrelációkat biztosít a tűzfaladatok és a fenyegetési események között, és kiemeli a gyanús eseményeket az entitások között. A munkafüzetek információt nyújtanak a forgalom trendjeiről, és lehetővé teszik az eredmények részletezését és szűrését. 

      ![Pal Alto műszerfal](./media/qs-get-visibility/palo-alto-week-query.png)


A munkafüzeteket a fő lekérdezésgomb ![](./media/qs-get-visibility/edit-query-button.png)szerkesztésével szabhatjuk testre. ![A gombra](./media/qs-get-visibility/go-to-la-button.png) kattintva a Naplóelemzés gombra kattintva [szerkesztheti a lekérdezést,](../azure-monitor/log-query/get-started-portal.md)és kiválaszthatja a három pontot (...), és kiválaszthatja a **Csempeadatok testreszabása**lehetőséget, amely lehetővé teszi a fő időszűrő szerkesztését, vagy eltávolíthatja az adott csempéket a munkafüzetből.

A lekérdezések működéséről további információt az [Oktatóanyag: Vizuális adatok a Log Analytics szolgáltatásban című témakörben talál.](../azure-monitor/learn/tutorial-logs-dashboards.md)

### <a name="add-a-new-tile"></a>Új csempe hozzáadása

Ha új csempét szeretne hozzáadni, hozzáadhatja azt egy meglévő munkafüzethez, amelyet ön vagy egy Azure Sentinel beépített munkafüzetet hoz létre. 
1. A Log Analytics alkalmazásban hozzon létre egy csempét az [Oktatóanyag: Vizuális adatok](../azure-monitor/learn/tutorial-logs-dashboards.md)a Log Analytics alkalmazásban című dokumentumban található utasítások alapján. 
2. A csempe létrehozása után a **Rögzítés**csoportban jelölje ki azt a munkafüzetet, amelyben meg szeretné jelenjenek a csempével.

## <a name="create-new-workbooks"></a>Új munkafüzetek létrehozása
Létrehozhat új munkafüzetet teljesen új munkafüzetet, vagy használhat beépített munkafüzetet az új munkafüzet alapjaként.

1. Ha teljesen új munkafüzetet szeretne létrehozni, válassza **a Munkafüzetek,** majd **a +Új munkafüzet lehetőséget.**
2. Válassza ki azt az előfizetést, amelyben a munkafüzet létrejön, és adjon neki egy leíró nevet. Minden munkafüzet egy Azure-erőforrás, mint bármely más, és hozzárendelheti azt szerepkörök (RBAC) határozza meg és korlátozza, aki elérheti. 
3. Ahhoz, hogy megjelenjen a munkafüzetekben a vizualizációk rögzítéséhez, meg kell osztania azt. Kattintson **a Megosztás,** majd **a Felhasználók kezelése parancsra.** 
 
1. Használja a **hozzáférés ellenőrzése** és **a szerepkör-hozzárendelések,** mint bármely más Azure-erőforrás. További információt az [Azure-munkafüzetek megosztása az RBAC használatával című](../azure-portal/azure-portal-dashboard-share-access.md)témakörben talál.


## <a name="new-workbook-examples"></a>Új munkafüzetpéldák

A következő mintalekérdezés lehetővé teszi a forgalom hetek közötti trendjeinek összehasonlítását. Könnyedén válthat, hogy melyik eszköz szállítóján és adatforrásán futtatja a lekérdezést. Ebben a példában a SecurityEvent a Windows, átválthat, hogy futtassa az AzureActivity vagy CommonSecurityLog bármely más tűzfalon.

     |where DeviceVendor == "Palo Alto Networks":
      // week over week query
      SecurityEvent
      | where TimeGenerated > ago(14d)
      | summarize count() by bin(TimeGenerated, 1d)
      | extend Week = iff(TimeGenerated>ago(7d), "This Week", "Last Week"), TimeGenerated = iff(TimeGenerated>ago(7d), TimeGenerated, TimeGenerated + 7d)


Előfordulhat, hogy olyan lekérdezést szeretne létrehozni, amely több forrásból származó adatokat tartalmaz. Létrehozhat egy lekérdezést, amely az Azure Active Directory naplóiaz új felhasználók, amelyek csak most jött létre, majd ellenőrzi az Azure-naplók, hogy ha a felhasználó megkezdte a szerepkör-hozzárendelés i változások 24 órán belül létrehozása. Ez a gyanús tevékenység megjelenik ezen az irányítópulton:

    AuditLogs
    | where OperationName == "Add user"
    | project AddedTime = TimeGenerated, user = tostring(TargetResources[0].userPrincipalName)
    | join (AzureActivity
    | where OperationName == "Create role assignment"
    | project OperationName, RoleAssignmentTime = TimeGenerated, user = Caller) on user
    | project-away user1

Különböző munkafüzeteket hozhat létre az adatokat és az adatokat megtekintő személyek szerepköre alapján. Létrehozhat például egy munkafüzetet a hálózati rendszergazdának, amely tartalmazza a tűzfaladatokat. Munkafüzeteket is létrehozhat aszerint, hogy milyen gyakran szeretné megtekinteni őket, hogy vannak-e olyan dolgok, amelyeket naponta szeretne áttekinteni, és más elemeket is, amelyeket óránként egyszer szeretne ellenőrizni, például érdemes óránként megnézni az Azure AD-bejelentkezéseket az anomáliák kereséséhez . 

## <a name="create-new-detections"></a>Új észlelések létrehozása

Az [Azure Sentinelhez csatlakoztatott adatforrások](connect-data-sources.md) észleléseit a szervezetben lévő fenyegetések kivizsgálásához hozza létre.

Új észlelés létrehozásakor használja ki a Microsoft biztonsági kutatói által készített, a csatlakoztatott adatforrásokhoz igazított beépített észleléseket.

Az összes beépített észlelés megtekintéséhez nyissa meg az **Analytics,** majd a **Szabálysablonok című elemet.** Ez a lap tartalmazza az Azure Sentinel beépített szabályait.

   ![Az Azure Sentinel használatával beépített észlelések segítségével kereshetfenyegetéseket](media/tutorial-detect-built-in/view-oob-detections.png)

A beépített észlelések beszerzéséről az [Oktatóanyag: Beépített elemzések beszerzése](tutorial-detect-threats-built-in.md)című témakörben talál további információt.
 
## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban megtanulta, hogyan kezdheti el használni az Azure Sentinelt. Folytassa a bemutató, [hogyan kell felismerni a fenyegetéseket](tutorial-detect-threats-built-in.md).
> [!div class="nextstepaction"]
> [Hozzon létre egyéni fenyegetésészlelési szabályokat](tutorial-detect-threats-custom.md) a fenyegetésekre adott válaszok automatizálásához.

