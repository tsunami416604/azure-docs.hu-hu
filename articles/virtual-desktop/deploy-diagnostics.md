---
title: A Windows rendszerű virtuális asztali diagnosztika eszközének üzembe helyezése – Azure
description: A diagnosztikai UX eszköz üzembe helyezése a Windows rendszerű virtuális asztali számítógépeken.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/14/2019
ms.author: helohr
ms.openlocfilehash: 625515223da12751b7765baa795bc68d2a7b46b4
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70233252"
---
# <a name="deploy-the-diagnostics-tool"></a>A diagnosztikai eszköz üzembe helyezése

A Windows rendszerű virtuális asztal diagnosztikai eszköze a következő műveleteket hajthatja végre:

- Egyetlen felhasználóhoz tartozó diagnosztikai tevékenységeket (felügyelet, kapcsolat vagy hírcsatorna) kereshet egy hét alatt.
- Gyűjtse össze a munkamenet-gazdagép adatait a Log Analytics munkaterületről származó kapcsolódási tevékenységekhez.
- Tekintse át a virtuális gép (VM) teljesítményének részleteit egy adott gazdagépen.
- Megtekintheti, hogy mely felhasználók jelentkeznek be a munkamenet-gazdagépre.
- Üzenet küldése az aktív felhasználóknak egy adott munkamenet-állomáson.
- Felhasználók aláírása a munkamenet-gazdagépről.

## <a name="prerequisites"></a>Előfeltételek

Az eszközhöz tartozó Azure Resource Manager sablon üzembe helyezéséhez létre kell hoznia egy Azure Active Directory alkalmazás regisztrációját és egy Log Analytics munkaterületet. Önnek vagy a rendszergazdának szüksége van ezekre az engedélyekre:

- Az Azure-előfizetés tulajdonosa
- Engedély erőforrások létrehozásához az Azure-előfizetésben
- Engedély Azure AD-alkalmazás létrehozásához
- RDS tulajdonosi vagy közreműködői jogosultságok

Ezt a két PowerShell-modult is telepítenie kell az első lépések előtt:

- [Azure PowerShell modul](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.4.0)
- [Azure AD-modul](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)

Győződjön meg arról, hogy az előfizetés-azonosítója készen áll a bejelentkezéskor.

Az Azure AD-alkalmazás regisztrálásának megkezdése után megteheti, hogy minden rendben van.

## <a name="create-an-azure-active-directory-app-registration"></a>Azure Active Directory alkalmazás regisztrációjának létrehozása

Ez a szakasz bemutatja, hogyan hozhatja létre a Azure Active Directory alkalmazást egy egyszerű szolgáltatással, és hogyan kérhet hozzá API-engedélyeket a PowerShell használatával.

>[!NOTE]
>Az API-engedélyek a Windows rendszerű virtuális asztali gépek, a Log Analytics és az Microsoft Graph API-engedélyek hozzáadódnak a Azure Active Directory alkalmazáshoz.

1. Nyissa meg a PowerShellt rendszergazdaként.
2. Nyissa meg az [RDS-templates GitHub-](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) tárházat, és futtassa az **ad-alkalmazás regisztrációjának létrehozása diagnosztikai. ps1** parancsfájlhoz a PowerShellben.
3.  Ha a parancsfájl megkéri az alkalmazás nevének megadására, adjon meg egy egyedi nevet.
4.  A szkript ezután megkéri, hogy jelentkezzen be egy rendszergazdai fiókkal. Adja meg a [delegált rendszergazdai hozzáféréssel](delegated-access-virtual-desktop.md)rendelkező felhasználó hitelesítő adatait. A rendszergazdának rendelkeznie kell az RDS tulajdonos vagy a közreműködő jogosultsággal.

A szkript sikeres futtatása után a kimenetében a következő dolgokat kell megjeleníteni:

-  Egy üzenet, amely megerősíti, hogy az alkalmazás most már rendelkezik egy egyszerű szolgáltatás szerepkör-hozzárendeléssel.
-  A nyomtató ügyfél-azonosítója és az ügyfél titkos kulcsa, amelyre szüksége lesz a diagnosztikai eszköz telepítésekor.

Most, hogy regisztrálta az alkalmazást, itt az ideje, hogy konfigurálja a Log Analytics munkaterületet.

## <a name="configure-your-log-analytics-workspace"></a>A Log Analytics munkaterület konfigurálása

A lehető legjobb megoldás érdekében javasoljuk, hogy a Log Analytics munkaterületet a következő teljesítményszámlálók segítségével konfigurálja, amelyek lehetővé teszik a felhasználói élmény utasításait egy távoli munkamenetben. A javasolt küszöbértékekkel rendelkező javasolt számlálók listáját a [Windows teljesítményszámláló küszöbértékei](deploy-diagnostics.md#windows-performance-counter-thresholds)című témakörben tekintheti meg.

### <a name="create-an-azure-log-analytics-workspace-using-powershell"></a>Azure Log Analytics-munkaterület létrehozása a PowerShell használatával

PowerShell-parancsfájl futtatásával Log Analytics munkaterületet hozhat létre, és a javasolt Windows-teljesítményszámlálók konfigurálásával figyelheti a felhasználói élményt és az alkalmazások teljesítményét.

>[!NOTE]
>Ha már rendelkezik olyan meglévő Log Analytics munkaterülettel, amelyet a használni kívánt PowerShell-parancsfájl nélkül hozott létre, ugorjon tovább a [szkript eredményeinek érvényesítéséhez a Azure Portal](#validate-the-script-results-in-the-azure-portal).

A PowerShell-parancsfájl futtatása:

1.  Nyissa meg a PowerShellt rendszergazdaként.
2.  Nyissa meg az [RDS-templates GitHub-](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) tárházat, és futtassa a **LogAnalyticsWorkspace létrehozása a Diagnostics. ps1** parancsfájlhoz a PowerShellben.
3. Adja meg a következő értékeket a paraméterekhez:

    - A **ResourceGroupName**mezőben adja meg az erőforráscsoport nevét.
    - A **LogAnalyticsWorkspaceName**mezőben adjon meg egy egyedi nevet a log Analytics munkaterülethez.
    - A **hely**mezőben adja meg az Ön által használt Azure-régiót.
    - Adja meg az **Azure-előfizetés azonosítóját**, amelyet a Azure Portal az előfizetések területen talál.

4. Adja meg a delegált rendszergazdai hozzáféréssel rendelkező felhasználó hitelesítő adatait.
5. Jelentkezzen be a Azure Portalba ugyanazzal a felhasználó hitelesítő adataival.
6. Jegyezze fel vagy jegyezze fel a LogAnalyticsWorkspace AZONOSÍTÓját később.
7. Ha a Log Analytics munkaterületet a PowerShell-parancsfájllal állítja be, akkor a teljesítményszámlálók konfigurálása már megtörtént, és a kihagyható, hogy [ellenőrizze a parancsfájl eredményét a Azure Portal](#validate-the-script-results-in-the-azure-portal). Ellenkező esetben folytassa a következő szakasszal.

### <a name="configure-windows-performance-counters-in-your-existing-log-analytics-workspace"></a>Windows-teljesítményszámlálók konfigurálása meglévő Log Analytics-munkaterületen

Ez a szakasz azokra a felhasználókra vonatkozik, akik az előző szakaszban PowerShell-parancsfájl nélkül létrehozott meglévő Azure Log Analytics-munkaterületet szeretnék használni. Ha még nem használta a parancsfájlt, akkor manuálisan kell konfigurálnia a javasolt Windows-teljesítményszámlálókat.

Az ajánlott teljesítményszámlálók manuális konfigurálását a következőképpen végezheti el:

1. Nyissa meg az Internet böngészőt, és [](https://portal.azure.com/) jelentkezzen be a Azure Portalba a rendszergazdai fiókjával.
2. Ezután lépjen **log Analytics** munkaterületek elemre a konfigurált Windows-teljesítményszámlálók áttekintéséhez.
3. A **Beállítások** szakaszban válassza a **Speciális beállítások**lehetőséget.
4. Ezt követően navigáljon az > adatwindows-**teljesítményszámlálók** elemhez, és adja hozzá a következő számlálókat:

    -   LogicalDisk (\*)\|% szabad terület
    -   LogicalDisk (C:)\\átlag Lemezvárólista hossza
    -   Memória (\*)\\elérhető MB-ban
    -   Processzor adatai (\*)\\processzoridő
    -   Felhasználói bemeneti késleltetés/munkamenet (\*)\\maximális bemeneti késleltetése

További információ a teljesítményszámlálók a [Azure monitor Windows-és Linux](/azure/azure-monitor/platform/data-sources-performance-counters)-teljesítményű adatforrásaiban.

>[!NOTE]
>A konfigurált további számlálók nem jelennek meg a diagnosztikai eszközben. Ahhoz, hogy megjelenjen a diagnosztika eszközben, konfigurálnia kell az eszköz konfigurációs fájlját. A speciális felügyelettel kapcsolatos utasítások később a GitHubon lesznek elérhetők.

## <a name="validate-the-script-results-in-the-azure-portal"></a>A parancsfájl eredményeinek ellenőrzése a Azure Portal

Mielőtt folytatná a diagnosztika eszköz telepítését, javasoljuk, hogy ellenőrizze, hogy a Azure Active Directory alkalmazás rendelkezik-e API-engedélyekkel, és hogy a Log Analytics munkaterület rendelkezik-e az előre konfigurált Windows-teljesítményszámlálók használatával.

### <a name="review-your-app-registration"></a>Az alkalmazás regisztrációjának áttekintése

Győződjön meg arról, hogy az alkalmazás regisztrációja API-engedélyekkel rendelkezik:

1. Nyisson meg egy böngészőt, és [](https://portal.azure.com/) kapcsolódjon a Azure Portalhoz a rendszergazdai fiókjával.
2. Lépjen a **Alkalmazásregisztrációkra** , és keresse meg a Azure ad alkalmazás regisztrációját.

      ![Az API-engedélyek lap.](media/api-permissions-page.png)


### <a name="review-your-log-analytics-workspace"></a>Tekintse át Log Analytics munkaterületét

Győződjön meg arról, hogy a Log Analytics munkaterület előre konfigurált Windows-teljesítményszámlálókat tartalmaz:

1. A [Azure Portal](https://portal.azure.com/)válassza a **log Analytics** munkaterületek lehetőséget a konfigurált Windows-teljesítményszámlálók áttekintéséhez.
2. A **Beállítások**területen válassza a **Speciális beállítások**lehetőséget.
3. Ezután nyissa meg a > következőt: adatwindows-**teljesítményszámlálók**.
4. Győződjön meg arról, hogy a következő számlálók előre vannak konfigurálva:

   - LogicalDisk (\*)\|% szabad terület: A lemezen lévő teljes felhasználható terület szabad területének százalékos értékét jeleníti meg.
   - LogicalDisk (C:)\\átlag Lemezvezérlő-várólista hossza: A C meghajtóhoz tartozó lemezes átviteli kérelem hossza. Az érték nem haladhatja meg a 2 értéket a rövid idő alatt.
   - Memória (\*)\\rendelkezésre álló MB-ban: A rendszer számára rendelkezésre álló memória megabájtban.
   - Processzor adatai (\*)\\processzoridő: az eltelt idő százalékos aránya, ameddig a processzor nem üresjárati szálat hajt végre.
   - Felhasználói bemeneti késleltetés/munkamenet (\*)\\maximális bemeneti késleltetése

### <a name="connect-to-vms-in-your-log-analytics-workspace"></a>Kapcsolódás virtuális gépekhez a Log Analytics munkaterületen

A virtuális gépek állapotának megtekintéséhez engedélyeznie kell a Log Analytics-kapcsolatokat. A virtuális gépek összekapcsolásához kövesse az alábbi lépéseket:

1. Nyisson meg egy böngészőt, és jelentkezzen be a [Azure Portalba](https://portal.azure.com/) a rendszergazdai fiókjával.
2. Lépjen a Log Analytics munkaterületre.
3. A bal oldali panel munkaterület-adatforrások területén válassza a **virtuális gépek**lehetőséget.
4. Válassza ki annak a virtuális gépnek a nevét, amelyhez csatlakozni szeretne.
5. Kattintson a **Csatlakozás** gombra.

## <a name="deploy-the-diagnostics-tool"></a>A diagnosztikai eszköz üzembe helyezése

Az Azure Resource Management-sablon üzembe helyezése a diagnosztikai eszközön:

1.  Nyissa meg a [GitHub Azure RDS-templates oldalt](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy).
2.  Telepítse a sablont az Azure-ba, és kövesse a sablon utasításait. Győződjön meg arról, hogy rendelkezésre áll a következő információk:

    -   Ügyfél-azonosító
    -   Ügyfél – titok
    -   A Log Analytics-munkaterület azonosítója

3.  Ha megadja a bemeneti paramétereket, fogadja el a használati feltételeket, majd válassza a **vásárlás**lehetőséget.

Az üzembe helyezés 2 – 3 percet vesz igénybe. A sikeres telepítést követően nyissa meg az erőforráscsoportot, és győződjön meg arról, hogy a webalkalmazás és az App Service-csomag erőforrásai vannak.

Ezt követően be kell állítania az átirányítási URI-t.

### <a name="set-the-redirect-uri"></a>Az átirányítási URI beállítása

Az átirányítási URI beállítása:

1.  A [Azure Portal](https://portal.azure.com/)lépjen a **app Services** elemre, és keresse meg a létrehozott alkalmazást.
2.  Lépjen az Áttekintés lapra, és másolja az ott található URL-címet.
3.  Navigáljon az **alkalmazások regisztrálásához** , és válassza ki a telepíteni kívánt alkalmazást.
4.  A bal oldali panel kezelés területén válassza a **hitelesítés**lehetőséget.
5.  Adja meg a kívánt átirányítási URI-t az **átirányítási URI** szövegmezőbe, majd kattintson a menü bal felső sarkában található **Mentés** elemre.
6. A típus alatt válassza a **web** lehetőséget a legördülő menüben.
7. Adja meg az URL-címet az alkalmazás áttekintés lapjáról, és adja hozzá a **/Security/signin-callback** a végéhez. Például: `https://<yourappname>.azurewebsites.net/security/signin-callback`.

   ![Az átirányítási URI-lap](media/redirect-uri-page.png)

8. Most nyissa meg az Azure-erőforrásokat, válassza ki az Azure App Services erőforrást a sablonban megadott névvel, és keresse meg a hozzá társított URL-címet. (Ha például a sablonban `contosoapp45`használt alkalmazás neve, akkor a társított <https://contosoapp45.azurewebsites.net>URL-cím:).
9. Jelentkezzen be a megfelelő Azure Active Directory felhasználói fiókkal.
10.   Válassza ki az **Elfogadás** lehetőséget.

## <a name="distribute-the-diagnostics-tool"></a>A diagnosztika eszköz terjesztése

Mielőtt a diagnosztikai eszközt elérhetővé tenné a felhasználók számára, győződjön meg arról, hogy a következő engedélyek vannak:

- A felhasználóknak olvasási hozzáféréssel kell rendelkezniük a log analyticshez. További információ: Ismerkedés [a szerepkörökkel, az engedélyekkel és a biztonsággal a Azure monitor](/azure/azure-monitor/platform/roles-permissions-security).
-  A felhasználóknak olvasási hozzáférésre is szükségük van a Windows rendszerű virtuális asztali bérlőhöz (RDS olvasó szerepkör). További információ: delegált [hozzáférés a Windows rendszerű virtuális asztal előzetes](delegated-access-virtual-desktop.md)verziójában.

A következő információkat is meg kell adnia a felhasználóknak:

- Az alkalmazás URL-címe
- Az egyes bérlők által elérhető bérlői csoport neve.

## <a name="use-the-diagnostics-tool"></a>A diagnosztikai eszköz használata

Miután bejelentkezett a fiókjába a szervezettől kapott információk alapján, rendelkeznie kell az egyszerű felhasználónévvel arra a felhasználóra, akinek a tevékenységeit le szeretné kérdezni. A keresés minden olyan tevékenységet megad, amely az elmúlt hét során megjelenő megadott tevékenységtípus alatt található.

### <a name="how-to-read-activity-search-results"></a>Tevékenységek keresési eredményeinek olvasása

A tevékenységek timestamp szerint vannak rendezve, a legújabb tevékenységgel. Ha az eredmények hibát adnak vissza, először ellenőrizze, hogy a szolgáltatás hiba-e. Szolgáltatási hibák esetén hozzon létre egy támogatási jegyet a tevékenység adataival, hogy segítsen a probléma hibakeresésében. A felhasználó vagy a rendszergazda a többi típusú hibát általában megoldhatja. A leggyakoribb hibák listáját és azok megoldását lásd: [a diagnosztikai szolgáltatással kapcsolatos problémák azonosítása](diagnostics-role-service.md#common-error-scenarios).

>[!NOTE]
>A szolgáltatási hibák a csatolt dokumentációban "külső hibák" néven jelennek meg. Ez módosítva lesz a PowerShell-hivatkozás frissítésekor.

A kapcsolódási tevékenységek több hibával is rendelkezhetnek. A tevékenységtípus kibontásával megtekintheti, hogy a felhasználó milyen hibákat észlelt. Válassza ki a hibakód nevét egy párbeszédpanel megnyitásához, ahol további információkat tekinthet meg róla.

### <a name="investigate-the-session-host"></a>A munkamenet-gazdagép vizsgálata 

A keresési eredmények között keresse meg és válassza ki azt a munkamenet-gazdagépet, amelyről adatokat szeretne használni.

A munkamenet-gazdagép állapotát elemezheti:

- Egy előre meghatározott küszöbérték alapján lekérheti a munkamenet-gazdagép állapotának adatait Log Analytics lekérdezéseket.
- Ha nincs tevékenység, vagy a munkamenet-gazdagép nem csatlakozik Log Analyticshoz, az információ nem lesz elérhető.

A munkamenet-gazdagépen lévő felhasználókkal is dolgozhat:

- Kijelentkezhet, vagy elküldheti a bejelentkezett felhasználóknak küldött üzenetet is.
- Az eredetileg keresett felhasználó alapértelmezés szerint ki van választva, de további felhasználóknak is kiválaszthatja az üzenetek küldését vagy egyszerre több felhasználó kijelentkezését.

### <a name="windows-performance-counter-thresholds"></a>Windows teljesítményszámláló küszöbértékei

- LogicalDisk (\*)\|% szabad terület:

    - A logikai lemez szabad területének teljes felhasználható területének százalékos arányát jeleníti meg.
    - Küszöb Kevesebb mint 20% van megjelölve sérültként.

- LogicalDisk (C:)\\átlag Lemezvezérlő-várólista hossza:

    - A tárolási rendszerfeltételeket jelöli.
    - Küszöb Az 5-nél nagyobb jelölés nem kifogástalan állapotú.

- Memória (\*)\\rendelkezésre álló MB-ban:

    - A rendszer számára rendelkezésre álló memória.
    - Küszöb Kevesebb mint 500 megabájt van megjelölve sérültként.

- Processzor adatai (\*)\\processzoridő:

    - Küszöb A 80%-nál nagyobb érték van megjelölve sérültként.

- [Felhasználói bemeneti késleltetés/munkamenet (\*)\\maximális bemeneti késleltetés](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters):

    - Küszöb Az 2000 MS-nál nagyobb érték nem megfelelőként van megjelölve.
