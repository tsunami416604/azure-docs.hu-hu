---
title: A Service Map-integráció a System Center Operations Manager |} A Microsoft Docs
description: A Service Map az Azure egyik megoldása, amely automatikusan felderíti az alkalmazás-összetevőket Windows és Linux rendszereken, és feltérképezi a szolgáltatások közötti kommunikációt. Ez a cikk ismerteti a Service Map használata az Operations Manager elosztottalkalmazás-diagramok automatikus létrehozásához.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: e8614a5a-9cf8-4c81-8931-896d358ad2cb
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: magoedte
ms.openlocfilehash: 878abe79754c3286b77aef744d1bc4973a8af841
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2019
ms.locfileid: "57729402"
---
# <a name="service-map-integration-with-system-center-operations-manager"></a>A Service Map System Center Operations Manager-integráció

A Szolgáltatástérkép automatikusan felderíti az alkalmazás-összetevőket Windows és Linux rendszereken, és feltérképezi a szolgáltatások közötti kommunikációt. A Service Map használatával a kiszolgálók az Ön gondol rájuk, rendszerekként, amelyek kritikus fontosságú szolgáltatásokat módon teszi lehetővé. A Service Map megmutatja a kapcsolatokat kiszolgálók, folyamatok és portok minden olyan TCP-kapcsolattal összekötött architektúrában, nem szükséges az ügynök telepítése mellett konfiguráció között. További információkért lásd: a [Szolgáltatástérkép dokumentációja]( service-map.md).

Ez az integráció a Service Map és a System Center Operations Manager között a dinamikus függőségi térképek, a Service Map alapuló az Operations Manager elosztottalkalmazás-diagramok automatikusan létrehozhat.

## <a name="prerequisites"></a>Előfeltételek
* Az Operations Manager felügyeleti csoport (2012 R2 vagy újabb) kiszolgálók egy csoportja, amelyek kezel.
* A Service Map szolgáltatás engedélyezve van a Log Analytics-munkaterületet.
* Kiszolgálók (legalább egyet), amely az Operations Manager és a Service Map adatokat küldő által kezelt készlete. Windows és Linux-kiszolgálók támogatottak.
* Egyszerű szolgáltatás a Log Analytics-munkaterülethez társított Azure-előfizetés hozzáférést. További információért ugorjon [egyszerű szolgáltatás létrehozása](#create-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>A Service Map felügyeleti csomag telepítése
Az Operations Manager és a Service Map integrációjával engedélyezi a Microsoft.SystemCenter.ServiceMap felügyeleticsomag-köteg (Microsoft.SystemCenter.ServiceMap.mpb) importálásával. A felügyeleticsomag-köteg a letöltheti a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=55763). A csomagot a következő felügyeleti csomagokat tartalmazza:
* Microsoft Service Map Alkalmazásnézetek
* Microsoft System Center Service Map Internal
* A Microsoft System Center Service Map felülbírálások
* Microsoft System Center Service Map

## <a name="configure-the-service-map-integration"></a>A Service Map-integráció konfigurálása
Miután telepítette a Service Map felügyeleti csomagot, egy új csomópont **Service Map**, alatt jelenik meg **Operations Management Suite** a a **felügyeleti** ablaktáblán.

>[!NOTE]
>[Operations Management Suite szolgáltatások gyűjteménye nem](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand) , amely tartalmazza a Log Analytics részét képező, [Azure Monitor](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md).

A Service Map integráció konfigurálásához tegye a következőket:

1. A konfigurációs varázsló megnyitásához a **Service Map áttekintése** ablaktáblán kattintson a **munkaterület hozzáadása**.  

    ![Service Map áttekintő panel](media/service-map-scom/scom-configuration.png)

2. Az a **kapcsolat konfigurációja** ablakban írja be a bérlő neve vagy azonosítója, azonosítója (más néven a felhasználónév vagy a clientID) és az egyszerű szolgáltatás jelszava, és kattintson **tovább**. További információkért nyissa meg a szolgáltatásnév létrehozásához.

    ![A kapcsolat konfigurációs ablaka](media/service-map-scom/scom-config-spn.png)

3. Az a **előfizetés kiválasztására** ablakban válassza ki az Azure-előfizetéssel, Azure-erőforráscsoportot (az egy, a Log Analytics-munkaterületet tartalmazó) és Log Analytics-munkaterületet, és kattintson **tovább**.

    ![Az Operations Manager konfigurációs munkaterület](media/service-map-scom/scom-config-workspace.png)

4. Az a **gép Csoportválasztás** melyik Service Map gépcsoportok szeretné szinkronizálni az Operations Manager választja az ablakban. Kattintson a **gépcsoportok hozzáadása/eltávolítása**, listájából válassza ki a csoportokat **rendelkezésre álló gépcsoportok**, és kattintson a **Hozzáadás**.  Ha befejezte a csoportok kiválasztásával, kattintson a **Ok** befejezéséhez.

    ![Az Operations Manager konfigurációs gépcsoportok](media/service-map-scom/scom-config-machine-groups.png)

5. Az a **kiszolgáló kiválasztása** ablakban konfigurálja a Service Map kiszolgálók csoport az Operations Manager és a Service Map közötti szinkronizálni kívánt kiszolgálón. Kattintson a **kiszolgálók hozzáadása/eltávolítása**.   

    A kiszolgáló egy elosztottalkalmazás-diagram létrehozása az integráció a kiszolgálónak kell lennie:

    * Operations Manager által felügyelt
    * A Service Map által felügyelt
    * A Service Map kiszolgálók csoport szerepel

    ![Az Operations Manager konfigurációs csoport](media/service-map-scom/scom-config-group.png)

6. Nem kötelező: Válassza ki a Log Analytics kommunikálni, és kattintson a felügyeleti kiszolgálókat tartalmazó erőforráskészlet **munkaterület hozzáadása**.

    ![Az Operations Manager konfigurációs erőforráskészlet](media/service-map-scom/scom-config-pool.png)

    Konfigurálása és regisztrálása a Log Analytics-munkaterületet egy percig is eltarthat. Beállítások konfigurálása után az Operations Manager a Service Map első szinkronizálás indítja el.

    ![Az Operations Manager konfigurációs erőforráskészlet](media/service-map-scom/scom-config-success.png)


## <a name="monitor-service-map"></a>A figyelő a Service Map
Miután a Log Analytics-munkaterülethez van csatlakoztatva, egy új mappát, a Service Map, megjelenik a **figyelés** az Operations Manager-konzol ablaktáblában.

![Az Operations Manager figyelés ablaktáblán](media/service-map-scom/scom-monitoring.png)

A Service Map mappa négy csomóponttal rendelkezik:
* **Aktív riasztások**: Az Operations Manager és a Service Map közötti kommunikáció kapcsolatos összes aktív riasztás listája.  Vegye figyelembe, hogy ezek a riasztások nem a Log Analytics-riasztások szinkronizálódik az Operations Manager.

* **Kiszolgálók**: Megjeleníti a figyelt kiszolgálók konfigurált való szinkronizálása a Service Map.

    ![Az Operations Manager figyelési kiszolgálók panel](media/service-map-scom/scom-monitoring-servers.png)

* **Gép függőségi Csoportnézeteket**: A Service Map szinkronizált összes számítógép-csoportok listája. Minden csoport az elosztottalkalmazás-diagram megtekintése gombra.

    ![Az Operations Manager elosztottalkalmazás-diagram](media/service-map-scom/scom-group-dad.png)

* **Kiszolgáló függőségi nézetek**: A Service Map szinkronizált összes kiszolgálók listája. Bármely kiszolgáló az elosztottalkalmazás-diagram megtekintése gombra.

    ![Az Operations Manager elosztottalkalmazás-diagram](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Szerkesztheti vagy törölheti a munkaterületet
Ön módosíthatja és törölheti a konfigurált munkaterületével keresztül a **Service Map áttekintése** ablaktáblán (**felügyeleti** panel > **Operations Management Suite**  >  **Service Map**).

>[!NOTE]
>[Operations Management Suite szolgáltatások gyűjteménye nem](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand) , amely tartalmazza a Log Analytics részét képező, [Azure Monitor](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md).

Most beállíthatja a csak egy Log Analytics-munkaterületet.

![Az Operations Manager szerkesztése munkaterület ablaktáblán](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Szabályok és felülbírálások konfigurálása
Egy szabály _Microsoft.SystemCenter.ServiceMapImport.Rule_, rendszeres időközönként beolvasni az adatokat a Szolgáltatástérkép jön létre. Szinkronizálási időzítésüket módosításához felülbírálások a szabály konfigurálható (**szerzői műveletek** panel > **szabályok** > **Microsoft.SystemCenter.ServiceMapImport.Rule**) .

![Az Operations Manager felülbírálások tulajdonságok ablak](media/service-map-scom/scom-overrides.png)

* **Engedélyezett**: Engedélyezi vagy letiltja az automatikus frissítések.
* **IntervalMinutes**: Állítsa vissza a frissítések közötti idő. Az alapértelmezett érték egy óra. Kiszolgáló maps gyakrabban szinkronizálni szeretné, ha az értéke módosíthatja.
* **TimeoutSeconds**: Állítsa alaphelyzetbe a mennyi ideig, mielőtt a kérés túllépi az időkorlátot.
* **TimeWindowMinutes**: Adatok lekérdezése időtartományából alaphelyzetbe. Alapértelmezett érték 60 perces ablak. A Service Map által engedélyezett maximális értéke 60 perc.

## <a name="known-issues-and-limitations"></a>Ismert problémák és korlátozások

A jelenlegi kialakítás mutat be, a következő problémák és korlátozások:
* Csak egyetlen Log Analytics-munkaterület csatlakozni.
* Bár a kiszolgálók a Service Map kiszolgálók csoport keresztül manuálisan is hozzáadhat a **szerzői műveletek** ablaktáblán, a maps ezekhez a kiszolgálókhoz közvetlenül nem lett szinkronizálva.  Ezek lesznek szinkronizálva a Service Map a következő szinkronizálási ciklus során.
* Ha a felügyeleti csomag által létrehozott elosztott alkalmazás a diagramokra végezze el a módosításokat, ezek a módosítások valószínűleg felülírja a Service Map az a következő szinkronizáláskor.

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása
Hivatalos Azure dokumentációjában az egyszerű szolgáltatás létrehozása lásd:
* [Egyszerű szolgáltatás létrehozása a PowerShell használatával](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Egyszerű szolgáltatás létrehozása az Azure CLI-vel](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Egyszerű szolgáltatás létrehozása az Azure portal használatával](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>Visszajelzés
Van bármilyen visszajelzése szolgáltatást, mert a Service Map vagy ez a dokumentáció? Látogasson el a [User Voice lap](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), ahol javaslattétel szolgáltatásokra, illetve szavazhat a meglévő javaslatokat.
