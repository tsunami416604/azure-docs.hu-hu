---
title: Sentinel Azure rövid útmutató – Ismerkedés az Azure-on Előzetesben Sentinel-|} A Microsoft Docs
description: Sentinel Azure rövid útmutató – Ismerkedés az Azure-Sentinel
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5a4ae93c-d648-41fb-8fb8-96a025d2f73e
ms.service: sentinel
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/4/2019
ms.author: rkarlin
ms.openlocfilehash: 1d54ac521fb8b81ed5fa135f68af420bda261661
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57246950"
---
# <a name="quickstart-get-started-with-azure-sentinel-preview"></a>Gyors útmutató: Ismerkedés az Azure-Sentinel-on Előzetesben

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Ebben a rövid, megtudhatja, hogyan is gyorsan megtekintheti és figyelheti, mi történik az Azure Sentinel-környezet között. Az adatforrásokhoz csatlakozik az Azure-Sentinel, miután kap az azonnali megjelenítése és elemzése az adatok így tudhatja, mi történik a csatlakoztatott minden adatforrás esetében. Azure Sentinel biztosítja az eszközök már elérhető az Azure, valamint a táblák teljes képességeit biztosító irányítópultok és a biztosít a naplók és a lekérdezések analytics beépített diagramokat. Beépített irányítópultokkal, vagy teljesen új vagy egy meglévő irányítópultot alapján könnyen, hozzon létre egy új irányítópultot. 

## <a name="get-visualization"></a>Vizualizáció beolvasása

Megjelenítése és elemzése, hogy mi történik, az adott környezet, először tekintse meg az áttekintő irányítópult képet kapjon a szervezete biztonsági állapotát. Ezek a csempék lehatolhat a nyers adatokat, amelyből létre minden egyes elem kattintva. Zaj csökkentésére, és tekintse át és vizsgálja meg a riasztások számának minimalizálása érdekében az Azure-Sentinel használ fusion technika, amelyek esetek riasztások. **Esetek** csoportjai, a kapcsolódó riasztásokat, amelyek együtt hozzon létre egy gyakorlatban hasznosítható eseményt, vizsgálja meg és oldja meg.

- Az Azure Portalon válassza ki az Azure-Sentinel, és válassza ki a figyelni kívánt munkaterülethez.

  ![Az Azure Sentinel áttekintése](./media/qs-get-visibility/overview.png)

- A felső eszköztáron jelzi, hogy hány eseményt a kiválasztott időszakra kapott, és a megelőző 24 órához hasonlítja össze. Az eszköztár jelzi, hogy ezeket az eseményeket a, a riasztásokat, amelyek korábban aktivált (a kis számú jelöli változása az elmúlt 24 órában), és ezután Megadja, hogy az események, hány meg nyitva, folyamatban van, és lezárt. Ellenőrizze, hogy nem olyan drámai növelését, vagy dobja el az események száma. Ha egy közvetlen, annak oka az lehet, hogy a kapcsolat az Azure-Sentinel jelentéskészítés kikapcsolva. Ha növelését, valami gyanús azért következhetett be. Ellenőrizze, hogy van-e új riasztásokat.

   ![Az Azure Sentinel tölcsér](./media/qs-get-visibility/funnel.png)

Az Áttekintés oldal törzse betekintést enged egyetlen pillantással a munkaterület biztonsági állapotáról:

- **Események és riasztások adott idő alatt**: Események és ezen események alapján létrehozott hány riasztást számát sorolja fel. Ha olyan kiugrás, amely a szokatlan, kell megjelennie riasztások esetében – ha szokatlan körülményt ahol egy kiugrás tapasztalható események, de nem látja a riasztásokat lát, lehet adhatnak okot.

- **A lehetséges kártevő események**: Ha rosszindulatú is lehet ismert forrásokból származó forgalmat észlelt, Azure Sentinel-riasztást jelenít meg a térképen. Narancssárga megtekintéséhez, hogy a bejövő forgalom: valaki megpróbál hozzáférni a szervezet egy ismert rosszindulatú IP-címről. Ha kimenő (piros) tevékenység, az azt jelenti, hogy a hálózatról, folyamatos adatátvitel kívül a szervezet számára egy ismert rosszindulatú IP-címet.

   ![Azure Sentinel-térkép](./media/qs-get-visibility/map.png)


- **Legutóbbi esetek**: A legutóbbi esetek, súlyosság és az esethez társított riasztások számának megtekintéséhez. Ha egy adott típusú riasztás hirtelen csúcs látható, ez azt jelentheti, hogy nincs-e folyamatban aktív támadás. Például ha rendelkezik egy hirtelen csúcs 20 Pass-the-hash események az Azure ATP, lehetséges, hogy valaki éppen megpróbálja a támadásokkal szemben.

- **Adatforrás-anomáliák**: A Microsoft az adatelemzők létrehozott modellek, amelyek folyamatosan az adatokat az adatforrásból a rendellenességeket. Ha nincs felismerheti a rendellenességeket, semmi nem jelenik meg. Rendellenességek észlelése esetén, a mély kell őket, tekintse meg az ismertetése. Kattintson például az Azure-tevékenység a megnövekedett. Kattintson a **diagram** tekintse meg, amikor a megnövekedett történt, és szűrjön rá az tevékenységeket, hogy mi okozta, hogy a megnövekedett időszak során fellépő.

   ![Azure Sentinel-térkép](./media/qs-get-visibility/anomolies.png)

## Beépített irányítópultokkal<a name="dashboards"></a>

Beépített irányítópultok biztosít, lehetővé teszi, hogy részletes csatlakoztatott adatforrásokból származó integrált adatok ismerje meg, az esemény jön létre ezeket a szolgáltatásokat. A beépített irányítópultok közé tartozik az Azure-azonosító, Azure-tevékenységi események és a helyszínen, amely lehet a kiszolgálók, az első entitás riasztásait, semmilyen harmadik félnek, beleértve a tűzfal forgalmi naplók, az Office 365 és a Windows-alapú nem biztonságos protokollokkal adatait a Windows-események események.

1. A **beállítások**válassza **irányítópultok**. A **telepített**, láthatja, hogy a telepített irányítópultjait. A **összes** láthatja, hogy az egész gyűjtemény legyen elérhető telepítés beépített irányítópultok. 
2. Keresse meg a meghatározott irányítópultra szeretné megtekinteni a teljes listája és leírása, mi minden kínál. 
3. Feltételezve, hogy használja az Azure AD-ben üzembe, és futtat az Azure-Sentinel, javasoljuk, hogy telepítse legalább a következőkben felsorolt irányítópultok:
   - **Az Azure AD**: Használja az egyik vagy mindkét alábbi:
       - **Az Azure AD bejelentkezési** elemzi a bejelentkezések megtekintéséhez, hogy vannak-e rendellenességek időbeli. Ezt az irányítópultot biztosít a sikertelen bejelentkezések alkalmazások, eszközök és a helyek, észlelheti, egyetlen pillantással szokatlan történik, ha. Több sikertelen bejelentkezést figyelmet fordítania. 
       - **Az Azure AD auditnaplóinak** elemzi a felügyeleti tevékenységek, például a felhasználók módosításokat (hozzáadhatja, eltávolíthatja, stb.), csoportok létrehozását és módosítását.  

   - Adjon hozzá egy irányítópultot, a tűzfal. Például adja hozzá a Palo Alto irányítópultot. Az irányítópult a tűzfal forgalmat, hogy a tűzfal adatok és a veszélyforrások elleni események közti korrelációk elemzi, és kiemeli a gyanús eseményeket entitások között. Irányítópultok nyújt információkat a forgalom a trendeket, és lehetővé teszi a feltárásához és szűrheti az eredményeket. 

      ![PAL Alto irányítópult](./media/qs-get-visibility/palo-alto-week-query.png)


Testre szabhatja az irányítópultokat a fő lekérdezés szerkesztésével vagy ![gomb](./media/qs-get-visibility/edit-query-button.png). A gombra ![gomb](./media/qs-get-visibility/go-to-la-button.png) nyissa meg a [van a lekérdezés szerkesztése a Log Analytics](../azure-monitor/log-query/get-started-portal.md), és kattintson a három pontra (...), és válassza ki **testreszabás csempék adatainak**, ami lehetővé teszi a fő Időszűrő szerkesztéséhez, vagy távolítsa el az adott csempét az irányítópulton.

Lekérdezések való használatáról további információkért lásd: [oktatóanyag: A Log Analytics a vizuális adatok](../azure-monitor/learn/tutorial-logs-dashboards.md)

### <a name="add-a-new-tile"></a>Adjon hozzá egy új csempe

Ha szeretne egy új csempe hozzáadása, hozzáadhat egy meglévő irányítópultot, egy Ön által létrehozott és a egy Azure-Sentinel beépített irányítópult. 
1. A Log Analyticsben, hozzon létre egy csempe található utasításokat követve [oktatóanyag: A Log Analytics a vizuális adatok](../azure-monitor/learn/tutorial-logs-dashboards.md). 
2. A csempe létrehozása után a **PIN-kód**, válassza ki a használni kívánt jelennek meg a csempét az irányítópulton.

## <a name="create-new-dashboards"></a>Új irányítópultok létrehozása
Előzmények nélküli új irányítópult létrehozása, vagy egy beépített irányítópult alapjaként használni, az új irányítópulthoz.

1. Sablon nélkül hozzon létre egy új irányítópultot, válassza a **irányítópultok** , majd **+ új irányítópult**.
2. Válassza ki az előfizetést, az irányítópult jön létre, és adjon meg egy leíró nevet. Minden irányítópult egy mint minden más Azure-erőforrás, és hozzárendelheti szerepkörök (RBAC) meghatározásához és a limit, ki férhet hozzá. 
3. Az engedélyezéshez megjelenjen a Vizualizációk rögzítése az irányítópultokon, akkor megoszthatja azt. Kattintson a **megosztás** , majd **felhasználók kezelése**. 
 
1. Használja a **hozzáférés ellenőrzése** és **szerepkör-hozzárendelések** mint bármely más Azure-beli erőforráshoz. További információkért lásd: [megosztás Azure-irányítópultok RBAC használatával](../azure-portal/azure-portal-dashboard-share-access.md).


## <a name="new-dashboard-examples"></a>Új irányítópult-példák

Az alábbi mintalekérdezés lehetővé teszi, hogy a forgalom trendek összehasonlíthasson hét. Eszköz forgalmazói és futtassa a lekérdezést a adatforrás könnyedén válthat. Ez a példa SecurityEvent használja a Windows, a bármely más tűzfalat AzureActivity vagy CommonSecurityLog futtathatja válthat.

     |where DeviceVendor = = "Palo Alto Networks":
      // week over week query
      SecurityEvent
      | where TimeGenerated > ago(14d)
      | summarize count() by bin(TimeGenerated, 1d)
      | extend Week = iff(TimeGenerated>ago(7d), "This Week", "Last Week"), TimeGenerated = iff(TimeGenerated>ago(7d), TimeGenerated, TimeGenerated + 7d)


Előfordulhat, hogy szeretne létrehozni egy lekérdezést, amely magában foglalja a több forrásból származó adatokat. Létrehozhat egy lekérdezést, amely az Azure Active Directory naplóinak újonnan létrehozott új felhasználókat, és ellenőrizze, hogy a felhasználó létrehozásának 24 órán belül hozzárendelési módosítások elvégzése szerepkör elindult naplózza az Azure-ellenőrzések. A gyanús tevékenységek jelennek meg az irányítópulton:

    AuditLogs
    | where OperationName == "Add user"
    | project AddedTime = TimeGenerated, user = tostring(TargetResources[0].userPrincipalName)
    | join (AzureActivity
    | where OperationName == "Create role assignment"
    | project OperationName, RoleAssignmentTime = TimeGenerated, user = Caller) on user
    | project-away user1

Az adatokat, és azokat, amit keres személy szerepkör alapján különböző irányítópultokat hozhat létre. Például a hálózat rendszergazdájával, hogy a tűzfal-adatokat tartalmaz egy irányítópultot hozhat létre. Milyen gyakran szeretné őket, nézze meg szeretné tekinteni a napi dolgot vannak-e azon alapuló irányítópultokat is létrehozhat, és a többi kívánt elemeket ellenőrzése óránként egyszer, például érdemes tekintse meg az Azure AD bejelentkezési óránként anomali keresése es. 


## <a name="next-steps"></a>További lépések
Ebből a gyors útmutatóból megtudhatta, hogyan kezdheti el az Azure-Sentinel. A következő oktatóanyagban [fenyegetések észlelése](tutorial-detect-threats.md).
> [!div class="nextstepaction"]
> [Fenyegetések észlelése](tutorial-detect-threats.md) fenyegetések a válaszok automatizálására.

