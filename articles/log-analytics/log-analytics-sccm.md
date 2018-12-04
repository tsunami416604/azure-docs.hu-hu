---
title: A Configuration Manager csatlakoztatása a Log Analyticsbe |} A Microsoft Docs
description: Ez a cikk bemutatja a Configuration Manager csatlakoztatása a Log Analytics és az adatok elemzésének megkezdéséhez szükséges lépéseket.
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
ms.date: 03/22/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 8e30b1ba093e38f81c9a09a0b9107e5a3b2eb768
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52847371"
---
# <a name="connect-configuration-manager-to-log-analytics"></a>A Configuration Manager csatlakoztatása a Log Analyticsbe
A System Center Configuration Manager-környezet szinkronizálási eszköz gyűjtemény adatait az Azure Log Analyticshez csatlakozhat, és ezeket a gyűjteményeket, a Log Analytics és Azure Automation hivatkozhat.  

## <a name="prerequisites"></a>Előfeltételek

A log Analytics támogatja a System Center Configuration Manager aktuális fejlesztési ágban, 1606-os vagy újabb verziója.  

## <a name="configuration-overview"></a>Konfiguráció áttekintése
Az alábbi lépéseket a Configuration Manager-integráció konfigurálása a Log Analytics lépéseket foglalja össze.  

1. Az Azure Portalon regisztrálja a Configuration Manager egy webalkalmazás és/vagy webes API-alkalmazást, és gondoskodjon arról, hogy az ügyfél-azonosító és a regisztráció az Azure Active Directoryból az ügyfél titkos kulcsa. Lásd: [Active Directory-alkalmazás és -erőforrások eléréséhez szolgáltatásnév létrehozása a portálon](../active-directory/develop/howto-create-service-principal-portal.md) ehhez a lépéshez részletes tájékoztatást.
2. Az Azure Portalon [adja meg a Configuration Manager (a regisztrált webalkalmazás) hozzáféréssel a Log Analytics-](#grant-configuration-manager-with-permissions-to-log-analytics).
3. A Configuration Managerben [vegyen fel egy kapcsolatot az OMS-kapcsolat hozzáadása varázsló használatával](#add-an-oms-connection-to-configuration-manager).
4. A Configuration Managerben [a kapcsolat tulajdonságainak frissítéséhez](#update-oms-connection-properties) , ha a jelszó vagy az ügyfél titkos kulcsot minden eddiginél jár, vagy megszakad.
5. [Töltse le és telepítse a Microsoft Monitoring Agent](#download-and-install-the-agent) a Configuration Manager szolgáltatási kapcsolódási pont helyrendszerszerepkört futtató számítógépen. Az ügynök Configuration Manager-adatokat küld a Log Analytics-munkaterületet.
6. A Log Analyticsben [gyűjteményeket importálhat a Configuration Manager](#import-collections) számítógép csoportokként.
7. A Log Analyticsben, megtekintheti az adatokat a Configuration Manager alkalmazásból, [számítógépcsoportok](../azure-monitor/platform/computer-groups.md).

További információ a Configuration Manager csatlakoztatása a Log Analytics [szinkronizálja a Microsoft Log Analytics adatokat a Configuration Manager](https://technet.microsoft.com/library/mt757374.aspx).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Támogatás a Configuration Manager a Log Analyticshez való engedélyekkel
A következő eljárásban biztosítson a *közreműködői* szerepkör a Log Analytics-munkaterület az AD-alkalmazás és egyszerű szolgáltatás korábban létrehozott a Configuration Manager.  Ha Ön még nem rendelkezik egy munkaterületet, [az Azure Log Analytics-munkaterület létrehozása](log-analytics-quick-create-workspace.md) a folytatás előtt.  Ez lehetővé teszi a Configuration Manager hitelesítéséhez és a Log Analytics-munkaterülethez csatlakozik.  

> [!NOTE]
> A Log Analytics a Configuration Manager engedélyeket kell adnia. Ellenkező esetben hibaüzenet kap, a konfigurációs varázsló a Configuration Manager használata esetén.
>

1. Az Azure Portal bal felső sarkában kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.<br><br> ![Azure Portal](media/log-analytics-sccm/azure-portal-01.png)<br><br>  
2. A Log Analytics-munkaterületek listájában válassza ki a munkaterület módosításához.
3. A bal oldali panelen válassza ki a **hozzáférés-vezérlés (IAM)**.
4. A hozzáférés-vezérlés (IAM) oldalon kattintson **szerepkör-hozzárendelés hozzáadása** és a **szerepkör-hozzárendelés hozzáadása** ablaktáblán jelenik meg.
5. Az a **szerepkör-hozzárendelés hozzáadása** panel alatt a **szerepkör** legördülő listában válassza ki a **közreműködői** szerepkör.  
6. Alatt a **rendelhet hozzáféréseket** legördülő listában válassza ki a korábban létrehozott AD a Configuration Manager alkalmazást, és kattintson **OK**.  

## <a name="download-and-install-the-agent"></a>Az ügynök letöltése és telepítése
Tekintse át a [a Log Analytics szolgáltatás az Azure-ban való csatlakozáshoz Windows számítógépek](../azure-monitor/platform/agent-windows.md) tudni, hogy a következő módszerek a Configuration Manager szolgáltatást futtató számítógépen a Microsoft Monitoring Agent telepítése Szolgáltatáskapcsolódási pont helyrendszerszerepkört.  

## <a name="add-a-log-analytics-connection-to-configuration-manager"></a>A Log Analytics-kapcsolat hozzáadása a Configuration Managerhez
Annak érdekében, hogy a Log Analytics-kapcsolat hozzáadása, rendelkeznie kell a Configuration Manager-környezet egy [szolgáltatáskapcsolati pont](https://technet.microsoft.com/library/mt627781.aspx) online módra konfigurálni.

1. Az a **felügyeleti** munkaterület a Configuration Manager, válassza ki **OMS-összekötő**. Ekkor megnyílik a **hozzáadása Log Analytics kapcsolat varázsló**. Kattintson a **Tovább** gombra.

   >[!NOTE]
   >OMS most már a Log Analytics nevezik.
   
2. Az a **általános** képernyőjén ellenőrizze, hogy elvégezte-e az alábbi műveleteket, és, hogy rendelkezik az egyes elemek részleteit, majd válassza ki, **tovább**.

   1. Az Azure Portalon regisztrálta a Configuration Manager, egy webalkalmazás és/vagy webes API-alkalmazást, és hogy rendelkezik a [ügyfél-azonosító, a regisztráció](../active-directory/develop/quickstart-v1-add-azure-ad-app.md).
   2. Az Azure Portalon létrehozott egy Azure Active Directoryban regisztrált alkalmazás titkos kulcsa.  
   3. Az Azure Portalon a regisztrált webalkalmazás elküldtük a hozzáféréssel a Log Analytics.  
      ![Kapcsolat a Log Analytics varázsló Általános lapja](./media/log-analytics-sccm/sccm-console-general01.png)
3. Az a **Azure Active Directory** képernyőn, a kapcsolat konfigurálása a Log Analytics azáltal, hogy a **bérlői**, **ügyfél-azonosító**, és **ügyfél A titkos kulcs**, majd **tovább**.  
   ![Log Analytics varázsló az Azure Active Directory oldalon kapcsolat](./media/log-analytics-sccm/sccm-wizard-tenant-filled03.png)
4. Ha meg valósítható meg a többi eljárások sikeresen, majd az adatokat a a **OMS-kapcsolat konfigurációs** képernyő automatikusan megjelenik ezen az oldalon. A kapcsolatbeállítások adatait meg kell jelennie a a **Azure-előfizetés**, **Azure-erőforráscsoport**, és **Operations Management Suite-munkaterület**.  
   ![Kapcsolat a Log Analytics varázsló Log Analytics-kapcsolat lap](./media/log-analytics-sccm/sccm-wizard-configure04.png)
5. A varázsló csatlakozik a Log Analytics szolgáltatás már bemeneti információk segítségével. Válassza ki a eszközgyűjtemények szinkronizálását a szolgáltatással, és kattintson a kívánt **Hozzáadás**.  
   ![Gyűjtemények kiválasztása](./media/log-analytics-sccm/sccm-wizard-add-collections05.png)
6. Ellenőrizze a kapcsolati beállításokat a a **összefoglalás** képernyőt, majd válassza ki **tovább**. A **folyamatban** képernyő a kapcsolat állapotát jeleníti meg, akkor érdemes **Complete**.

> [!NOTE]
> A legfelső szintű helyet a hierarchiában a Log Analytics csatlakoznia kell. Ha egy önálló elsődleges hely csatlakoztatása a Log Analyticshez, és egy központi adminisztrációs hely majd hozzá a környezethez, akkor törölje és hozza létre újból a kapcsolatot az új hierarchiában.
>
>

Miután a Configuration Manager a Log Analyticsbe, adjon hozzá vagy a gyűjteményeket, és a kapcsolat tulajdonságainak megtekintése.

## <a name="update-log-analytics-connection-properties"></a>A Log Analytics-kapcsolat tulajdonságainak frissítése
Ha egy jelszót vagy az ügyfél titkos kulcsot minden eddiginél jár, vagy megszakad, szüksége lesz manuálisan frissíteni a Log Analytics-kapcsolat tulajdonságait.

1. Keresse meg a Configuration Managerben **Cloud Services**, majd **OMS-összekötő** megnyitásához a **OMS-kapcsolat tulajdonságait** lap.
2. Ezen az oldalon kattintson a **Azure Active Directory** fülre kattintva megtekintheti a **bérlői**, **ügyfél-azonosító**, **ügyfél titkos kulcsának lejárati dátuma**. **Győződjön meg arról** a **titkos ügyfélkulcs** , ha már lejárt.

## <a name="import-collections"></a>Gyűjtemények importálása
Miután hozzáadta a Log Analytics-kapcsolat a Configuration Manager és az ügynök telepítve van azon a számítógépen, amelyen a Configuration Manager szolgáltatáskapcsolódási pont helyrendszerszerepkör, a következő lépés az, hogy a gyűjtemények importálása a Configuration Manager naplóban Számítógépcsoportok analyticsbe.

Eszközgyűjtemények importálásához a hierarchia kezdeti konfigurálása után a gyűjtemény tagsági információk 3 óránként rendszer lekéri a tagság naprakészen tartása. Ha szeretné, ezt bármikor letiltható.

1. Az Azure Portal bal felső sarkában kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.
2. A Log Analytics-munkaterületek listájában válassza ki a munkaterületet, a Configuration Manager regisztrálva van.  
3. Válassza ki a **Speciális beállítások** elemet.<br><br> ![A Log Analytics speciális beállításai](media/log-analytics-sccm/log-analytics-advanced-settings-01.png)<br><br>  
4. Válassza ki **számítógépcsoportok** majd **SCCM**.  
5. Válassza ki **importálása a Configuration Manager-gyűjteménytagságok** majd **mentése**.  
   ![Számítógépcsoportok – SCCM lap](./media/log-analytics-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>A Configuration Manager alkalmazásból adatok megtekintése
Miután hozzáadta a Log Analytics-kapcsolat a Configuration Manager és az ügynök telepítése a Configuration Manager szolgáltatási kapcsolódási pont helyrendszerszerepkört futtató számítógépen, az ügynök adatküldést a Log Analytics szolgáltatásba. A Log Analytics, a Configuration Manager-gyűjtemények jelennek meg [számítógépcsoportok](../azure-monitor/platform/computer-groups.md). Megtekintheti a csoportok a **Configuration Manager** lap **Settings\Computer csoportok**.

Miután a gyűjtemények importálása, láthatja, hány csoporttagsággal rendelkező számítógépet észlelt. Megtekintheti az importált gyűjtemények száma is.

![Számítógépcsoportok – SCCM lap](./media/log-analytics-sccm/sccm-computer-groups02.png)

Ha egy gombra kattint, keresési jelenik meg, amelyen az importált csoportok összes vagy egyes csoporthoz tartozó összes számítógép. Használatával [naplóbeli keresés](../azure-monitor/log-query/log-query-overview.md), elkezdheti a Configuration Manager adatok részletes elemzését.

## <a name="next-steps"></a>További lépések
* Használat [naplóbeli keresés](../azure-monitor/log-query/log-query-overview.md) részletes információkat a Configuration Manager adatainak megtekintéséhez.
