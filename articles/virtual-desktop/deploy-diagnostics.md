---
title: A Diagnosztikai eszköz telepítése a Windows Virtuális asztalhoz – Azure
description: A Windows Virtual Desktop diagnosztikai ux-eszközének telepítése.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4eb63fe4bd8f8a8b0961aa6a7fccb8de9b7c2f16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123409"
---
# <a name="deploy-the-diagnostics-tool"></a>A diagnosztikai eszköz üzembe helyezése

>[!IMPORTANT]
>2020. március 16-tól ideiglenesen letiltottuk azokat a diagnosztikai lekérdezéseket, amelyek a szolgáltatás iránti megnövekedett igény miatt hatással vannak a felhasználói élményre. Ez azt eredményezi, hogy az eszköz leáll, mert ezekre a lekérdezésekre támaszkodik. Ezt a cikket akkor frissítjük, amikor a diagnosztikai lekérdezések ismét elérhetővé válikk.
>
>Addig is azt javasoljuk, hogy [a Log Analytics folyamatos](diagnostics-log-analytics.md) figyelés.

A Windows Virtual Desktop diagnosztikai eszköze a következőket teheti:

- Keressen diagnosztikai tevékenységeket (kezelés, kapcsolat vagy hírcsatorna) egyetlen felhasználó számára egy hét alatt.
- A Kapcsolati tevékenységek munkamenet-gazdagép-információinak összegyűjtése a Log Analytics-munkaterületről.
- Tekintse át a virtuális gép (VM) teljesítményrészleteit egy adott gazdagép.
- Megtekintheti, hogy mely felhasználók jelentkeznek be a munkamenet-gazdagépbe.
- Üzenet küldése egy adott munkamenet-gazdagép aktív felhasználóinak.
- A felhasználók kijelentkezése a munkamenet-gazdagépből.

## <a name="prerequisites"></a>Előfeltételek

Létre kell hoznia egy Azure Active Directory-alkalmazásregisztrációt és egy Log Analytics-munkaterületet, mielőtt telepítheti az Azure Resource Manager-sablont az eszközhöz. Önnek vagy a rendszergazdának a következő engedélyekre van szüksége:

- Az Azure-előfizetés tulajdonosa
- Engedélyek erőforrások létrehozására az Azure-előfizetésben
- Az Azure AD-alkalmazás létrehozásának engedélyezése
- RdS-tulajdonosi vagy közreműködői jogok

A kezdés előtt telepítenie kell ezt a két PowerShell-modult is:

- [Azure PowerShell-modul](/powershell/azure/install-az-ps?view=azps-2.4.0/)
- [Azure AD modul](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0/)

Győződjön meg arról, hogy előfizetési azonosítója készen áll a bejelentkezéskor.

Miután mindent rendben, létrehozhatja az Azure AD alkalmazás regisztrációját.

## <a name="create-an-azure-active-directory-app-registration"></a>Azure Active Directory-alkalmazásregisztráció létrehozása

Ez a szakasz bemutatja, hogyan használhatja a PowerShellt az Azure Active Directory alkalmazás egyszerű szolgáltatással történő létrehozásához és api-engedélyek behívásához.

>[!NOTE]
>Az API-engedélyek a Windows virtuális asztal, a Log Analytics és a Microsoft Graph API-engedélyek hozzá adódnak az Azure Active Directory alkalmazáshoz.

1. Nyissa meg a PowerShellt rendszergazdaként.
2. Jelentkezzen be az Azure-ba egy olyan fiókkal, amely tulajdonosi vagy közreműködői engedélyekkel rendelkezik a diagnosztikai eszközhöz használni kívánt Azure-előfizetéshez:
   ```powershell
   Login-AzAccount
   ```
3. Jelentkezzen be az Azure AD-be ugyanazzal a fiókkal:
   ```powershell
   Connect-AzureAD
   ```
4. Nyissa meg az [RDS-Sablonok GitHub-tárházát,](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) és futtassa a **CreateADAppRegistrationforDiagnostics.ps1** parancsfájlt a PowerShellben.
5.  Amikor a parancsfájl arra kéri, hogy nevezze el az alkalmazást, adjon meg egy egyedi alkalmazásnevet.


Miután a parancsfájl sikeresen futott, a következő dolgokat kell megjelenítenie a kimenetben:

-  Egy üzenet, amely megerősíti, hogy az alkalmazás most már rendelkezik egy egyszerű szolgáltatás szerepkör-hozzárendeléssel.
-  Az ügyfélazonosító és az ügyféltitkos kulcs, amelya diagnosztikai eszköz telepítésekor szükséges lesz.

Most, hogy regisztrálta az alkalmazást, itt az ideje konfigurálni a Log Analytics-munkaterületet.

## <a name="configure-your-log-analytics-workspace"></a>A Log Analytics-munkaterület konfigurálása

A lehető legjobb élmény érdekében azt javasoljuk, hogy konfigurálja a Log Analytics munkaterületet a következő teljesítményszámlálókkal, amelyek lehetővé teszik a felhasználói élmény kimutatásait egy távoli munkamenetben. A javasolt küszöbértékeket tartalmazó ajánlott számlálók listáját a [Windows teljesítményszámláló-küszöbértékei című témakörben található.](deploy-diagnostics.md#windows-performance-counter-thresholds)

### <a name="create-an-azure-log-analytics-workspace-using-powershell"></a>Azure Log Analytics-munkaterület létrehozása a PowerShell használatával

A PowerShell-parancsfájlok futtatásával létrehozhat egy Log Analytics-munkaterületet, és konfigurálhatja az ajánlott Windows teljesítményszámlálókat a felhasználói élmény és az alkalmazások teljesítményének figyelésére.

>[!NOTE]
>Ha már rendelkezik egy meglévő Log Analytics-munkaterülettel, amelyet a használni kívánt PowerShell-parancsfájl nélkül készített, ugorjon [előre a parancsfájleredmények ellenőrzése az Azure Portalon](#validate-the-script-results-in-the-azure-portal)című munkaterületre.

A PowerShell-parancsfájl futtatása:

1.  Nyissa meg a PowerShellt rendszergazdaként.
2.  Nyissa meg az [RDS-Sablonok GitHub-tárházát,](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) és futtassa a **CreateLogAnalyticsWorkspaceforDiagnostics.ps1** parancsfájlt a PowerShellben.
3. Adja meg a következő értékeket a paraméterekhez:

    - A **ResourceGroupName**mezőbe írja be az erőforráscsoport nevét.
    - A **LogAnalyticsWorkspaceName mezőbe**írja be a Log Analytics-munkaterület egyedi nevét.
    - A **Hely**mezőbe írja be a használt Azure-régiót.
    - Adja meg az **Azure Subscription ID**azonosítóját , amely az Azure Portalon az **Előfizetések**területen található.

4. Adja meg a delegált rendszergazdai hozzáféréssel rendelkező felhasználó hitelesítő adatait.
5. Jelentkezzen be az Azure Portalon ugyanazzal a felhasználó hitelesítő adataival.
6. Írja le vagy jegyezze meg a LogAnalyticsWorkspace-azonosítót későbbi használatra.
7. Ha a Log Analytics-munkaterületet a PowerShell-parancsfájllal állítja be, akkor a teljesítményszámlálókat már konfigurálni kell, és [előreugorhatja a parancsfájleredmények ellenőrzése az Azure Portalon.](#validate-the-script-results-in-the-azure-portal) Ellenkező esetben folytassa a következő szakaszt.

### <a name="configure-windows-performance-counters-in-your-existing-log-analytics-workspace"></a>A Windows teljesítményszámlálóinak konfigurálása a meglévő Log Analytics-munkaterületen

Ez a szakasz azoknak a felhasználóknak szól, akik az előző szakaszban a PowerShell-parancsfájl nélkül létrehozott meglévő Azure Log Analytics-munkaterületet szeretnék használni. Ha még nem használta a parancsfájlt, akkor manuálisan kell konfigurálnia az ajánlott Windows teljesítményszámlálókat.

Az ajánlott teljesítményszámlálók manuális konfigurálása:

1. Nyissa meg az internetböngészőt, és jelentkezzen be az [Azure Portalra](https://portal.azure.com/) a felügyeleti fiókjával.
2. Ezután nyissa meg a **Log Analytics-munkaterületeket** a windows-teljesítményszámlálók konfigurált áttekintéséhez.
3. A **Beállítások** csoportban válassza a **Speciális beállítások lehetőséget.**
4. Ezután nyissa meg **az Adat** > **Windows teljesítményszámlálók at,** és adja hozzá a következő számlálókat:

    -   LogicalDisk(\*\\) %Szabad terület
    -   LogicalDisk(C:)\\lemezvárólista hossza
    -   Memória(\*\\) Elérhető mbyte
    -   Processzoradatok(\*\\) Processzoridő
    -   Felhasználói bevitelkésleltetés\*munkamenetenként( )\\Maximális bemeneti késleltetés

További információ a Windows [és linuxos teljesítményadatforrások](/azure/azure-monitor/platform/data-sources-performance-counters)teljesítményszámlálóiról az Azure Monitorban.

>[!NOTE]
>A konfigurált további számlálók nem jelennek meg magában a diagnosztikai eszközben. Ahhoz, hogy megjelenjen a diagnosztikai eszközben, konfigurálnia kell az eszköz konfigurációs fájlját. Erre vonatkozó utasítások a speciális felügyelettel később lesznek elérhetők a GitHubon.

## <a name="validate-the-script-results-in-the-azure-portal"></a>A parancsfájleredmények ellenőrzése az Azure Portalon

A diagnosztikai eszköz további üzembe helyezése előtt azt javasoljuk, hogy ellenőrizze, hogy az Azure Active Directory-alkalmazás rendelkezik-e API-engedélyekkel, és a Log Analytics-munkaterület rendelkezik az előre konfigurált Windows teljesítményszámlálókkal.

### <a name="review-your-app-registration"></a>Az alkalmazásregisztráció áttekintése

Annak ellenőrzése, hogy az alkalmazásregisztráció rendelkezik-e API-engedélyekkel:

1. Nyisson meg egy böngészőt, és csatlakozzon az [Azure Portalhoz](https://portal.azure.com/) a felügyeleti fiókjával.
2. Nyissa meg az **Azure Active Directoryt.**
3. Nyissa meg **az Alkalmazásregisztrációk lehetőséget,** és válassza **a Minden alkalmazás lehetőséget.**
4. Keresse meg az Azure AD-alkalmazás regisztrációját ugyanazzal az alkalmazásnévvel, amelyet az [Azure Active Directory alkalmazásregisztráció létrehozása](deploy-diagnostics.md#create-an-azure-active-directory-app-registration)5.

### <a name="review-your-log-analytics-workspace"></a>A Log Analytics-munkaterület áttekintése

Annak biztosítása, hogy a Log Analytics-munkaterület előre konfigurált Windows teljesítményszámlálókkal rendelkezik:

1. Az [Azure Portalon](https://portal.azure.com/)tekintse meg a **Log Analytics-munkaterületek** a konfigurált Windows teljesítményszámlálók.
2. A **Beállítások csoportban**válassza a **Speciális beállítások lehetőséget.**
3. Ezt követően nyissa meg **a Data** > **Windows teljesítményszámlálók**.
4. Győződjön meg arról, hogy a következő számlálók előre konfigurálva vannak:

   - LogicalDisk(\*\\) %Szabad terület: A lemezen lévő teljes felhasználható terület szabad területének százalékos mennyiségét jeleníti meg.
   - LogicalDisk(C:)\\Átlagos lemezvárólista hossza: A C meghajtó lemezátviteli kérelemének hossza. Az érték nem haladhatja meg a 2-t egy rövid időalatt.
   - Memória(\*\\) Rendelkezésre álló Mbyte: A rendszer számára rendelkezésre álló memória megabájtban.
   - Processzorinformáció(\*\\) Processzoridő: az az eltelt idő azon százaléka, amelyet a processzor nem tétlen szál végrehajtására fordít.
   - Felhasználói bevitelkésleltetés\*munkamenetenként( )\\Maximális bemeneti késleltetés

### <a name="connect-to-vms-in-your-log-analytics-workspace"></a>Csatlakozás virtuális gépekhez a Log Analytics-munkaterületen

A virtuális gépek állapotának megtekintéséhez engedélyeznie kell a Log Analytics-kapcsolatot. A virtuális gépek csatlakoztatásához kövesse az alábbi lépéseket:

1. Nyisson meg egy böngészőt, és jelentkezzen be az [Azure Portalra](https://portal.azure.com/) a felügyeleti fiókjával.
2. Nyissa meg a Log Analytics-munkaterületet.
3. A bal oldali panelen a Munkaterület-adatforrások csoportban válassza ki a **virtuális gépeket.**
4. Válassza ki annak a virtuális gépnek a nevét, amelyhez csatlakozni kíván.
5. Kattintson a **Csatlakozás** gombra.

## <a name="deploy-the-diagnostics-tool"></a>A diagnosztikai eszköz üzembe helyezése

Az Azure Resource Management sablon üzembe helyezése a diagnosztikai eszközhöz:

1.  Nyissa meg a [GitHub Azure RDS-sablonok lapját.](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy)
2.  Telepítse a sablont az Azure-ba, és kövesse a sablonban található utasításokat. Győződjön meg arról, hogy a következő információk állnak rendelkezésre:

    -   Ügyfél-azonosító
    -   Ügyféltitok
    -   A Log Analytics-munkaterület azonosítója

3.  A bemeneti paraméterek megadása után fogadja el a feltételeket, majd válassza a **Vásárlás**lehetőséget.

A telepítés 2–3 percet vesz igénybe. Sikeres üzembe helyezés után nyissa meg az erőforráscsoportot, és győződjön meg arról, hogy a webalkalmazás és az alkalmazásszolgáltatás-csomag erőforrásai ott vannak.

Ezt követően be kell állítania az átirányítási URI-t.

### <a name="set-the-redirect-uri"></a>Az átirányításURI beállítása

Az átirányítási URI beállítása:

1.  Az [Azure Portalon](https://portal.azure.com/)nyissa meg az **App Services,** és keresse meg a létrehozott alkalmazást.
2.  Lépjen az áttekintő oldalra, és másolja az ott található URL-t.
3.  Nyissa meg az **alkalmazásregisztrációkat,** és válassza ki a telepíteni kívánt alkalmazást.
4.  A bal oldali panel Enkezelés területén válassza a **Hitelesítés**lehetőséget.
5.  Írja be a kívánt átirányítási URI-t az **Átirányítás URI** szövegmezőbe, majd válassza a **Mentés** parancsot a menü bal felső sarkában.
6. Válassza a **Web** lehetőséget a Típus legördülő menüben.
7. Adja meg az URL-címet az alkalmazás áttekintő lapjáról, és adja hozzá a **/security/signin-callback parancsot** annak végéhez. Például: `https://<yourappname>.azurewebsites.net/security/signin-callback`.

   ![Az átirányítási URI lap](media/redirect-uri-page.png)

8. Most nyissa meg az Azure-erőforrásokat, válassza ki az Azure App Services-erőforrást a sablonban megadott névvel, és keresse meg a hozzá társított URL-címet. (Ha például a sablonban használt alkalmazásnév a `contosoapp45`, <https://contosoapp45.azurewebsites.net>akkor a társított URL-cím a. .
9. Jelentkezzen be a megfelelő Azure Active Directory felhasználói fiókkal.
10.   Válassza ki az **Elfogadás** lehetőséget.

## <a name="distribute-the-diagnostics-tool"></a>A diagnosztikai eszköz elosztása

Mielőtt elérhetővé tenné a diagnosztikai eszközt a felhasználók számára, győződjön meg arról, hogy rendelkeznek a következő engedélyekkel:

- A felhasználóknak olvasási hozzáférésre van szükségük a naplóelemzéshez. További információ: [A szerepkörök, engedélyek és biztonság az Azure Monitor használatával](/azure/azure-monitor/platform/roles-permissions-security)című témakörben talál.
-  A felhasználóknak olvasási hozzáférésre is szükségük van a Windows virtuális asztal bérlőjéhez (RDS Reader szerepkör). További információt a [Delegált hozzáférés a Windows virtuális asztalon című témakörben talál.](delegated-access-virtual-desktop.md)

A felhasználóknak a következő információkat is meg kell adnia:

- Az alkalmazás URL-címe
- A bérlői csoport egyéni bérlőinek nevét, amelyhez hozzáférhetnek.

## <a name="use-the-diagnostics-tool"></a>A diagnosztikai eszköz használata

Miután bejelentkezett a fiókjába a szervezettől kapott adatok felhasználásával, készítse fel az upn-t arra a felhasználóra, amiért le szeretné kérdezni a tevékenységeket. A keresés minden olyan tevékenységet megad, amely a megadott tevékenységtípus alatt történt az elmúlt héten.

### <a name="how-to-read-activity-search-results"></a>A tevékenységkeresési eredmények olvasása

A tevékenységek időbélyeg szerint vannak rendezve, először a legújabb tevékenységgel. Ha az eredmények hibát adnak vissza, először ellenőrizze, hogy szolgáltatáshiba-e. Szolgáltatáshibák esetén hozzon létre egy támogatási jegyet a tevékenységadatokkal, hogy segítsen a probléma hibakeresésében. Az összes többi hibatípust általában a felhasználó vagy a rendszergazda oldhatja meg. A leggyakoribb hibaforgatókönyvek és azok megoldásának listáját a [Problémák azonosítása és diagnosztizálása](diagnostics-role-service.md#common-error-scenarios)című témakörben találja.

>[!NOTE]
>A szolgáltatási hibákat a csatolt dokumentációban "külső hibáknak" nevezzük. Ez a PowerShell-hivatkozás frissítésekénk módosul.

A csatlakozási tevékenységekegynél több hibát is tartalmazhatnak. A tevékenységtípus kibontásával megtekintheti a felhasználó által észlelt egyéb hibákat. A párbeszédpanel megnyitásához válassza ki a hibakód nevét, hogy további információkat láthasson róla.

### <a name="investigate-the-session-host"></a>A munkamenet-gazdagép vizsgálata 

A keresési eredmények között keresse meg és jelölje ki azt a munkamenet-állomást, amelyről információt szeretne kapni.

Elemezheti a munkamenet gazdagépének állapotát:

- Egy előre meghatározott küszöbérték alapján lekérheti a munkamenet gazdagép állapotadatait, amelyeket a Log Analytics lekérdezések.
- Ha nincs tevékenység, vagy a munkamenet-gazdagép nem csatlakozik a Log Analytics, az adatok nem lesz elérhető.

A munkamenet-gazdagépen lévő felhasználókkal is kommunikálhat:

- Kijelentkezhet, vagy üzenetet küldhet a bejelentkezett felhasználóknak.
- Az eredetileg keresett felhasználó alapértelmezés szerint ki van jelölve, de további felhasználókat is kijelölhet, akik egyszerre több felhasználót szeretnének kiküldeni.

### <a name="windows-performance-counter-thresholds"></a>A Windows teljesítményszámlálójának küszöbértékei

- LogicalDisk(\*\\) %Szabad terület:

    - A logikai lemezen lévő teljes felhasználható terület szabad százalékát jeleníti meg.
    - Küszöbérték: Kevesebb, mint 20% van jelölve, mint egészségtelen.

- LogicalDisk(C:)\\lemezvárólista hossza:

    - A tárolórendszer feltételeit jelöli.
    - Küszöbérték: Az 5-nél magasabb érték nem kifogástalanállapotúként van megjelölve.

- Memória(\*\\) Rendelkezésre álló mbyte:Memory( ) Available Mbytes:

    - A rendszer rendelkezésre álló memóriája.
    - Küszöbérték: Kevesebb, mint 500 megabájt, amely nem kifogástalanként van megjelölve.

- Processzoradatok(\*\\) Processzoridő:

    - Küszöbérték: A 80%-nál magasabb érték nem kifogástalanállapotúként van megjelölve.

- [Felhasználói bevitel késleltetése munkamenetenként(\*)\\Maximális bemeneti késleltetés:](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)

    - Küszöbérték: A 2000 ms-nál magasabb állapotú állapotúként van megjelölve.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan figyelheti a tevékenységnaplókat a [Diagnosztika használata a Log Analytics szolgáltatással](diagnostics-log-analytics.md)című helyen.
- A gyakori hibaforgatókönyvekről és azok megoldásáról a [Problémák azonosítása és diagnosztizálása](diagnostics-role-service.md)című oldalon olvashat.
