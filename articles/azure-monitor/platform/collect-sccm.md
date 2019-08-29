---
title: Configuration Manager összekötése Azure Monitorhoz | Microsoft Docs
description: Ez a cikk bemutatja, hogyan csatlakozhat Configuration Manager a munkaterülethez Azure Monitor és megkezdheti az adatok elemzését.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: magoedte
ms.openlocfilehash: b4e6cc32b1d4392c63b7b236ab0df297849224c8
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141111"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Configuration Manager összekötése a Azure Monitor
A System Center Configuration Manager-Azure Monitor környezet összekapcsolásával szinkronizálhatja az eszköz-gyűjtemény adatait, és Azure Monitor és Azure Automation is hivatkozhat ezekre a gyűjteményekre.  

## <a name="prerequisites"></a>Előfeltételek

Azure Monitor támogatja System Center Configuration Manager aktuális ág, 1606-es és újabb verzióját.

>[!NOTE]
>A Configuration Manager Log Analytics munkaterülettel való összekapcsolásának funkciója nem kötelező, és alapértelmezés szerint nincs engedélyezve. A funkció használatához engedélyeznie kell ezt a funkciót. További információ: [a választható funkciók engedélyezése a frissítésekben](https://docs.microsoft.com/sccm/core/servers/manage/install-in-console-updates#bkmk_options).

## <a name="configuration-overview"></a>Konfiguráció áttekintése

A következő lépések összefoglalják a Configuration Manager integráció konfigurálásának lépéseit Azure Monitorokkal.  

1. A Azure Active Directoryban regisztráljon Configuration Manager webalkalmazásként és/vagy webes API-alkalmazásként, és győződjön meg arról, hogy az ügyfél-azonosító és az ügyfél titkos kulcsa a regisztrációból a Azure Active Directory. Lásd: [Active Directory-alkalmazás és -erőforrások eléréséhez szolgáltatásnév létrehozása a portálon](../../active-directory/develop/howto-create-service-principal-portal.md) ehhez a lépéshez részletes tájékoztatást.

2. A Azure Active Directoryban [adja meg a Configuration Manager (a regisztrált webalkalmazás) engedélyt a Azure monitor eléréséhez](#grant-configuration-manager-with-permissions-to-log-analytics).

3. A Configuration Managerban vegyen fel egy-kapcsolatokat az **Azure-szolgáltatások** varázslóval.

4. [Töltse le és telepítse a Windows rendszerhez készült log Analytics-ügynököt](#download-and-install-the-agent) a Configuration Manager szolgáltatási kapcsolódási pont helyrendszer-szerepkört futtató számítógépen. Az ügynök Configuration Manager adatokat küld a Azure Monitor Log Analytics munkaterületére.

5. Azure Monitor a [gyűjtemények importálása Configuration Manager](#import-collections) számítógép-csoportokként.

6. A Azure Monitorban az adatok megtekinthetők a Configuration Manager [számítógép](computer-groups.md)-csoportokként.

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Támogatás a Configuration Manager a Log Analyticshez való engedélyekkel

A következő eljárásban biztosítson a *közreműködői* szerepkör a Log Analytics-munkaterület az AD-alkalmazás és egyszerű szolgáltatás korábban létrehozott a Configuration Manager. Ha még nem rendelkezik munkaterülettel, a folytatás előtt tekintse [meg a Munkaterületek létrehozása a Azure monitorban](../../azure-monitor/learn/quick-create-workspace.md) című témakört. Ez lehetővé teszi a Configuration Manager hitelesítéséhez és a Log Analytics-munkaterülethez csatlakozik.  

> [!NOTE]
> Configuration Manager esetében meg kell adnia az engedélyeket a Log Analytics munkaterületen. Ellenkező esetben hibaüzenet kap, a konfigurációs varázsló a Configuration Manager használata esetén.
>

1. Az Azure Portal bal felső sarkában kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.

2. A Log Analytics-munkaterületek listájában válassza ki a munkaterület módosításához.

3. A bal oldali panelen válassza ki a **hozzáférés-vezérlés (IAM)** .

4. A hozzáférés-vezérlés (IAM) oldalon kattintson **szerepkör-hozzárendelés hozzáadása** és a **szerepkör-hozzárendelés hozzáadása** ablaktáblán jelenik meg.

5. Az a **szerepkör-hozzárendelés hozzáadása** panel alatt a **szerepkör** legördülő listában válassza ki a **közreműködői** szerepkör.  

6. Alatt a **rendelhet hozzáféréseket** legördülő listában válassza ki a korábban létrehozott AD a Configuration Manager alkalmazást, és kattintson **OK**.  

## <a name="download-and-install-the-agent"></a>Az ügynök letöltése és telepítése

Tekintse át a [Windows rendszerű Azure monitor számítógépek csatlakoztatása az Azure-ban](agent-windows.md) című cikket, hogy megismerje a log Analytics-ügynök Windows rendszeren való telepítéséhez elérhető módszereket a Configuration Manager Service csatlakozási pontjának helyrendszer-szerepkörét futtató számítógépen.  

## <a name="connect-configuration-manager-to-log-analytics-workspace"></a>Configuration Manager összekötése Log Analytics munkaterülettel

>[!NOTE]
> Log Analytics kapcsolat hozzáadásához a Configuration Manager-környezetnek online módba konfigurált [szolgáltatási kapcsolódási ponttal](https://docs.microsoft.com/sccm/core/servers/deploy/configure/about-the-service-connection-point) kell rendelkeznie.

> [!NOTE]
> A hierarchiában lévő legfelső szintű helyet Azure Monitorra kell kapcsolni. Ha egy önálló elsődleges helyet csatlakoztat Azure Monitor, majd egy központi adminisztrációs helyet ad hozzá a környezethez, törölnie kell, majd újra létre kell hoznia a kapcsolatot az új hierarchián belül.

1. A Configuration Manager **Adminisztráció** munkaterületén válassza a **felhők szolgáltatások** elemet, majd válassza az **Azure-szolgáltatások**elemet. 

2. Kattintson a jobb gombbal az **Azure-szolgáltatások** elemre, majd válassza az **Azure-szolgáltatások konfigurálása**lehetőséget. Megjelenik az **Azure-szolgáltatások konfigurálása** oldal. 
   
3. Az a **általános** képernyőjén ellenőrizze, hogy elvégezte-e az alábbi műveleteket, és, hogy rendelkezik az egyes elemek részleteit, majd válassza ki, **tovább**.

4. Az Azure-szolgáltatások varázsló Azure-szolgáltatások lapján:

    1. Adja meg az objektum **nevét** Configuration Managerban.
    2. A szolgáltatás azonosításának megkönnyítéséhez adja meg a nem kötelező **leírást** .
    3. Válassza ki az Azure Service **OMS**-összekötőt.

    >[!NOTE]
    >A OMS mostantól olyan Log Analytics néven is ismert, amely Azure Monitor egyik funkciója.

5. Kattintson a **tovább** gombra az Azure-szolgáltatások varázsló Azure-alkalmazás tulajdonságai lapjának folytatásához.

6. Az Azure-szolgáltatások varázsló **alkalmazás** lapján először válassza ki az Azure-környezetet a listából, majd kattintson az **Importálás**elemre.

7. Az **alkalmazások importálása** lapon a következő információkat kell megadnia:

    1. Adja meg az alkalmazáshoz tartozó **Azure ad-bérlő nevét** .

    2. Itt adhatja meg az Azure **ad-bérlő azonosítóját** az Azure ad-bérlő számára. Ezeket az információkat a Azure Active Directory **Tulajdonságok** lapján találja. 

    3. Adja meg az **alkalmazás** nevét az alkalmazás nevében.

    4. A korábban létrehozott létrehozott Azure AD-alkalmazáshoz tartozó **ügyfél-azonosító**megadása.

    5. A **titkos kulcs**megadásához a létrehozott Azure ad-alkalmazás ügyfél titkos kulcsa.

    6. Adja meg a **titkos kulcs**lejárati idejét, a kulcs lejárati dátumát.

    7. Az **alkalmazás-azonosító URI**azonosítójának megadása a korábban létrehozott létrehozott Azure ad-alkalmazáshoz tartozó alkalmazás-azonosító URI-ja.

    8. Válassza az **ellenőrzés** lehetőséget, és a jobb oldalon a **sikeres ellenőrzésnek**megfelelő eredményeket kell megjeleníteni!.

8. A **konfiguráció** lapon tekintse át az **Azure**-előfizetések, az **Azure-erőforráscsoport**és az **Operations Management Suite-munkaterület** mezőinek ellenőrzéséhez szükséges információkat, amelyek jelzik, hogy az Azure ad-alkalmazás megfelelő engedélyek az erőforráscsoporthoz. Ha a mezők üresek, azt jelzi, hogy az alkalmazás nem rendelkezik a szükséges jogosultságokkal. Válassza ki a gyűjteni kívánt és a munkaterületre továbbítandó eszközöket, majd válassza a **Hozzáadás**lehetőséget.

9. A kapcsolatok létrehozásának és konfigurálásának megkezdéséhez tekintse át a beállításokat a **Beállítások megerősítése** lapon, majd a **tovább** gombra kattintva.

10. Ha a konfiguráció elkészült, megjelenik a **Befejezés** lap. Válassza a **Bezárás**lehetőséget. 

Miután csatolta Configuration Managert a Azure Monitorhoz, hozzáadhat vagy eltávolíthat gyűjteményeket, és megtekintheti a kapcsolat tulajdonságait.

## <a name="update-log-analytics-workspace-connection-properties"></a>Log Analytics munkaterület-kapcsolatok tulajdonságainak frissítése

Ha a jelszó vagy az ügyfél titkos kulcsa lejár vagy elveszett, manuálisan frissítenie kell a Log Analytics kapcsolati tulajdonságait.

1. A Configuration Manager **Adminisztráció** munkaterületén válassza a **Cloud Services** , majd az **OMS-összekötő** lehetőséget a **OMS-kapcsolatok tulajdonságlapjának** megnyitásához.
2. Ezen az oldalon kattintson a **Azure Active Directory** fülre kattintva megtekintheti a **bérlői**, **ügyfél-azonosító**, **ügyfél titkos kulcsának lejárati dátuma**. **Győződjön meg arról** a **titkos ügyfélkulcs** , ha már lejárt.

## <a name="import-collections"></a>Gyűjtemények importálása

Miután hozzáadta Log Analytics kapcsolatát az Configuration Managerhoz, és telepítette az ügynököt a Configuration Manager Service kapcsolódási pontjának helyrendszer-szerepkörét futtató számítógépen, a következő lépés az Azure-beli Configuration Manager gyűjtemények importálása Számítógép-csoportok figyelése.

Miután befejezte a kezdeti konfigurációt, hogy importálja az eszközöket a hierarchiából, a rendszer 3 óránként beolvassa a gyűjtemény információit a tagság aktuális állapotának megőrzéséhez. Ha szeretné, ezt bármikor letiltható.

1. Az Azure Portal bal felső sarkában kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza **log Analytics**munkaterületek lehetőséget.
2. A Log Analytics-munkaterületek listájában válassza ki a munkaterületet, a Configuration Manager regisztrálva van.  
3. Válassza ki a **Speciális beállítások** elemet.
4. Válassza ki **számítógépcsoportok** majd **SCCM**.  
5. Válassza ki **importálása a Configuration Manager-gyűjteménytagságok** majd **mentése**.  
   
    ![Számítógépcsoportok – SCCM lap](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>A Configuration Manager alkalmazásból adatok megtekintése

Miután hozzáadta Log Analytics kapcsolatát az Configuration Managerhoz, és telepítette az ügynököt a Configuration Manager Service kapcsolódási pont helyrendszer-szerepkört futtató számítógépen, a rendszer elküldi az ügynöktől az Azure Monitor Log Analytics munkaterületére. Azure Monitor a Configuration Manager gyűjtemények számítógépcsoportként jelennek [](../../azure-monitor/platform/computer-groups.md)meg. Megtekintheti a csoportok a **Configuration Manager** lap **Settings\Computer csoportok**.

Miután a gyűjtemények importálása, láthatja, hány csoporttagsággal rendelkező számítógépet észlelt. Megtekintheti az importált gyűjtemények száma is.

![Számítógépcsoportok – SCCM lap](./media/collect-sccm/sccm-computer-groups02.png)

Ha rákattint valamelyikre, a naplófájl-szerkesztő megnyílik az összes importált csoport vagy az egyes csoportokhoz tartozó összes számítógép között. A [naplóbeli keresés](../../azure-monitor/log-query/log-query-overview.md)használatával további részletes elemzéseket végezhet a gyűjtemény tagsági adataiban.

## <a name="next-steps"></a>További lépések

Használat [naplóbeli keresés](../../azure-monitor/log-query/log-query-overview.md) részletes információkat a Configuration Manager adatainak megtekintéséhez.
