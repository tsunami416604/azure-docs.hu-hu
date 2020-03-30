---
title: Csatlakozás a Configuration Manager alkalmazáshoz az Azure Monitorhoz | Microsoft dokumentumok
description: Ez a cikk a Configuration Manager és az Azure Monitor munkaterületéhez való csatlakoztatásának lépéseit mutatja be, és megkezdheti az adatok elemzését.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/28/2019
ms.openlocfilehash: 3140c0de6fbe090e3d040202cd581c455f03b6d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655256"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>A Configuration Manager csatlakoztatása az Azure Monitorhoz
A Microsoft Endpoint Configuration Manager környezetét csatlakoztathatja az Azure Monitorhoz az eszközgyűjtési adatok szinkronizálásához és a gyűjtemények hivatkozásához az Azure Monitorban és az Azure Automationben.  

## <a name="prerequisites"></a>Előfeltételek

Az Azure Monitor támogatja a Configuration Manager jelenlegi ága, 1606-os vagy újabb verzió.

>[!NOTE]
>A Configuration Manager és a Log Analytics-munkaterület összekapcsolására szolgáltatás nem kötelező, és alapértelmezés szerint nincs engedélyezve. Használat előtt engedélyeznie kell ezt a szolgáltatást. További információ: [Enable optional features from updates](https://docs.microsoft.com/configmgr/core/servers/manage/install-in-console-updates#bkmk_options).

## <a name="configuration-overview"></a>Konfiguráció áttekintése

A következő lépések összegezik a Configuration Manager-integráció konfigurálásának lépéseit az Azure Monitorral.  

1. Az Azure Active Directoryban regisztrálja a Configuration Managert webalkalmazásként és/vagy webAPI-alkalmazásként, és győződjön meg arról, hogy rendelkezik az Ügyfélazonosítóval és az ügyféltitkos kulccsal az Azure Active Directoryból való regisztrációból. A lépés elvégzésével kapcsolatos részletes információkért olvassa el A [portál használata az erőforrásokhoz hozzáférő Active Directory-alkalmazás és egyszerű szolgáltatás](../../active-directory/develop/howto-create-service-principal-portal.md) használatát című témakört.

2. Az Azure Active Directoryban [adja meg a Configuration Manager (a regisztrált webalkalmazás) számára az Azure Monitor elérésére vonatkozó engedélyt.](#grant-configuration-manager-with-permissions-to-log-analytics)

3. A Configuration Manager ben adjon hozzá egy kapcsolatot az **Azure Services** varázsló használatával.

4. [Töltse le és telepítse a Windows Log Analytics ügynökét](#download-and-install-the-agent) a Configuration Manager szolgáltatás csatlakozási ponthelyrendszerszerepkörét futtató számítógépre. Az ügynök elküldi a Configuration Manager-adatokat az Azure Monitor Log Analytics-munkaterületére.

5. Az Azure Monitorban [importálja a beszedéseket a Configuration Managerből](#import-collections) számítógépcsoportként.

6. Az Azure Monitorban a Configuration Manager adatait [számítógépcsoportként](computer-groups.md)tekintheti meg.

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>A Configuration Manager megadása a Log Analytics szolgáltatásra vonatkozó engedélyekkel

A következő eljárás ban adja meg a *közreműködői* szerepkört a Log Analytics-munkaterületen az AD-alkalmazás és a Configuration Manager korábban létrehozott egyszerű szolgáltatás. Ha még nem rendelkezik munkaterülettel, a folytatás előtt [olvassa el a Munkaterület létrehozása az Azure Monitorban](../../azure-monitor/learn/quick-create-workspace.md) című témakört. Ez lehetővé teszi, hogy a Configuration Manager hitelesítse magát, és csatlakozzon a Log Analytics-munkaterülethez.  

> [!NOTE]
> Meg kell adnia az engedélyeket a Configuration Manager Log Analytics munkaterületén. Ellenkező esetben hibaüzenet jelenik meg, amikor a Configuration Manager konfigurációs varázslóját használja.
>

1. Az Azure Portal bal felső sarkában kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.

2. A Log Analytics-munkaterületek listájában válassza ki a módosítani kívánt munkaterületet.

3. A bal oldali ablaktáblában válassza a **Hozzáférés-vezérlés (IAM) lehetőséget.**

4. A Hozzáférés-vezérlés (IAM) lapon kattintson a **Szerepkör-hozzárendelés hozzáadása** gombra, és megjelenik a **Szerepkör-hozzárendelés hozzáadása** ablaktábla.

5. A **Szerepkör-hozzárendelés hozzáadása** ablaktáblán a **Szerepkör** legördülő listában válassza a **Közreműködői** szerepkört.  

6. A **Hozzáférés hozzárendelése** legördülő listában jelölje ki az AD-ben korábban létrehozott Configuration Manager alkalmazást, majd kattintson az **OK**gombra.  

## <a name="download-and-install-the-agent"></a>Az ügynök letöltése és telepítése

Tekintse át a [Windows-számítógépek csatlakoztatása](agent-windows.md) az Azure Monitor az Azure-ban, hogy megismerhesse a rendelkezésre álló módszerek et a Log Analytics ügynök windows a számítógépen a Configuration Manager szolgáltatás csatlakozási pont helyrendszer szerepkör.  

## <a name="connect-configuration-manager-to-log-analytics-workspace"></a>A Configuration Manager csatlakoztatása a Log Analytics-munkaterülethez

>[!NOTE]
> A Log Analytics-kapcsolat hozzáadásához a Configuration Manager-környezetnek online módra konfigurált [szolgáltatáscsatlakozási ponttal](https://docs.microsoft.com/configmgr/core/servers/deploy/configure/about-the-service-connection-point) kell rendelkeznie.

> [!NOTE]
> A hierarchia legfelső szintű webhelyét csatlakoztatnia kell az Azure Monitorhoz. Ha egy önálló elsődleges hely hez csatlakozik az Azure Monitorhoz, majd központi felügyeleti helyet ad hozzá a környezetéhez, törölnie kell, majd újra létre kell hoznia a kapcsolatot az új hierarchiában.

1. A Configuration Manager **felügyeleti** munkaterületén válassza a Clouds Services ( **Felhők szolgáltatások)** lehetőséget, majd az **Azure Services**lehetőséget. 

2. Kattintson a jobb gombbal az **Azure Services elemre,** majd válassza **az Azure-szolgáltatások konfigurálása parancsot.** Megjelenik **az Azure Services konfigurálása** lap. 
   
3. Az **Általános** képernyőn ellenőrizze, hogy elvégezte-e a következő műveleteket, és hogy minden elemhez rendelkezik-e részlet, majd válassza a **Tovább**gombot.

4. Az Azure Services varázsló Azure Services lapján:

    1. Adja meg az objektum **nevét** a Configuration Manager ben.
    2. Adjon meg egy nem kötelező **leírást** a szolgáltatás azonosításához.
    3. Válassza ki az Azure szolgáltatás **OMS-összekötőjét.**

    >[!NOTE]
    >Az OMS-t most log analytics-nek nevezik, amely az Azure Monitor egyik szolgáltatása.

5. Válassza a **Tovább** lehetőséget az Azure-szolgáltatások varázsló Azure-alkalmazás tulajdonságai lapján való folytatáshoz.

6. Az **Azure** Services varázsló Alkalmazáslapján először válassza ki az Azure-környezetet a listából, majd kattintson az **Importálás gombra.**

7. Az **Alkalmazások importálása** lapon adja meg a következő információkat:

    1. Adja meg az **Azure AD bérlői nevét** az alkalmazáshoz.

    2. Adja meg az **Azure AD-bérlői azonosító** az Azure AD-bérlő. Ezeket az információkat az Azure Active Directory **tulajdonságai** lapon találja. 

    3. Adja meg az **alkalmazáshoz Az** alkalmazás nevének neve.

    4. Adja meg az **ügyfélazonosítót,** a korábban létrehozott Azure AD-alkalmazás alkalmazásazonosítóját.

    5. Adja meg a **titkos kulcs,** a létrehozott Azure AD-alkalmazás ügyféltitkos kulcsa.

    6. Adja meg a **titkos kulcs lejárati**idejét, a kulcs lejárati dátumát.

    7. Adja meg az **App ID URI,** az App ID URI a korábban létrehozott Azure AD-alkalmazás.

    8. Válassza **az Ellenőrzés lehetőséget,** és a jobb oldalon az eredményeknek **sikeresen ellenőrizve kell lenniük!**. .

8. A **Konfiguráció lapon** tekintse át az adatokat az **Azure-előfizetések**, **az Azure erőforráscsoport**és az **Operations Management Suite munkaterület-mezők** előre kitöltött, jelezve, hogy az Azure AD-alkalmazás rendelkezik a megfelelő engedélyekkel az erőforráscsoportban. Ha a mezők üresek, az azt jelzi, hogy az alkalmazás nem rendelkezik a szükséges jogokkal. Jelölje ki az összegyűjtő és továbbítani kívánt eszközgyűjteményeket, majd válassza a **Hozzáadás**lehetőséget.

9. Tekintse át a **beállítások megerősítéséhez** szükséges beállításokat, és a Kapcsolat létrehozásának megkezdéséhez és konfigurálásának megkezdéséhez válassza a **Tovább** gombra.

10. A konfiguráció befejezése után megjelenik a **Befejezés** lap. Kattintson a **Bezárás** gombra. 

Miután összekapcsolta a Configuration Managert az Azure Monitorhoz, hozzáadhat vagy eltávolíthat gyűjteményeket, és megtekintheti a kapcsolat tulajdonságait.

## <a name="update-log-analytics-workspace-connection-properties"></a>A Log Analytics munkaterületi kapcsolat tulajdonságainak frissítése

Ha egy jelszó vagy ügyféltitkos kulcs lejár, vagy megszakad, manuálisan kell frissítenie a Log Analytics-kapcsolat tulajdonságait.

1. A Configuration Manager **felügyeleti** munkaterületén válassza a **Felhőszolgáltatások** lehetőséget, majd az **OMS-összekötő** elemre az **OMS-kapcsolat tulajdonságai** lap megnyitásához.
2. Ezen a lapon kattintson az **Azure Active Directory** fülre a **bérlő**, **ügyfélazonosító**, **ügyféltitkos kulcs lejáratának**megtekintéséhez. **Ellenőrizze** az **ügyfél titkos kulcsát,** ha az lejárt.

## <a name="import-collections"></a>Gyűjtemények importálása

Miután hozzáadta a Log Analytics-kapcsolatot a Configuration Managerhez, és telepítette az ügynököt a Configuration Manager szolgáltatás csatlakozási ponthelyrendszer-szerepkörét futtató számítógépen, a következő lépés a gyűjtemények importálása az Azure-beli Configuration Managerből Monitor számítógépcsoportként.

Miután befejezte a kezdeti konfigurációt az eszközgyűjtemények hierarchiából történő importálásához, a rendszer 3 óránként lekéri a gyűjteményadatokat, hogy a tagság naprakész maradjon. Ezt bármikor letilthatja.

1. Az Azure Portal bal felső sarkában kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza **a Log Analytics-munkaterületek lehetőséget.**
2. A Log Analytics-munkaterületek listájában válassza ki azt a munkaterületet, amelyhez a Configuration Manager regisztrálva van.  
3. Válassza ki a **Speciális beállítások** elemet.
4. Válassza a **Számítógépcsoportok,** majd **az SCCM**lehetőséget.  
5. Válassza **a Configuration Manager importálása gyűjteménytagságok importálása** lehetőséget, majd kattintson a Mentés **gombra.**  
   
    ![Számítógépcsoportok – SCCM lap](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Adatok megtekintése a Configuration Manager ből

Miután hozzáadta a Log Analytics-kapcsolatot a Configuration Managerhez, és telepítette az ügynököt a Configuration Manager szolgáltatás csatlakozási ponthelyrendszeri szerepkörét futtató számítógépre, a rendszer elküldi az ügynök adatait az Azure Monitor Log Analytics-munkaterületére. Az Azure Monitorban a Configuration Manager-gyűjtemények [számítógépcsoportokként](../../azure-monitor/platform/computer-groups.md)jelennek meg. A csoportokat a **Configuration Manager** lapon, a **Beállítások\Számítógépcsoportok**csoportban tekintheti meg.

A gyűjtemények importálása után láthatja, hogy hány gyűjteménytagsággal rendelkező számítógépet észlelt a rendszer. Megtekintheti az importált gyűjtemények számát is.

![Számítógépcsoportok – SCCM lap](./media/collect-sccm/sccm-computer-groups02.png)

Ha valamelyikre kattint, a naplólekérdezés-szerkesztő megnyílik, és megjeleníti az összes importált csoportot vagy az egyes csoportokhoz tartozó összes számítógépet. A [Naplókeresés segítségével](../../azure-monitor/log-query/log-query-overview.md)további mélyreható elemzést végezhet a gyűjteménytagsági adatokról.

## <a name="next-steps"></a>További lépések

A [Naplókeresés segítségével](../../azure-monitor/log-query/log-query-overview.md) részletes információkat tekinthet meg a Configuration Manager adatairól.
