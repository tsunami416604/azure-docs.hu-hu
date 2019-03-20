---
title: Az Azure Portalon a műveleti csoportok létrehozása és kezelése
description: Ismerje meg, hogyan hozhat létre és kezelheti az Azure Portalon Műveletcsoportok.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 1/29/2019
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 29db8c8b51082016d413c66c6abfdb1c518c9329
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58117965"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Az Azure Portalon a műveleti csoportok létrehozása és kezelése
## <a name="overview"></a>Áttekintés ##
Műveletcsoport gyűjteménye, Azure-előfizetés tulajdonosa által megadott értesítési beállításokat. Azure Monitor és a Service Health-riasztások Műveletcsoportok használatával értesítheti a felhasználókat, hogy egy riasztás aktiválódott-e. Az azonos műveletcsoport vagy a felhasználó követelményeitől függően különböző Műveletcsoportok különböző riasztások használhatja. Az előfizetéshez legfeljebb 2000 Műveletcsoportok konfigurálásával.

Egy olyan műveletet, kapnak egy megerősítő jelző műveleti csoporthoz hozzáadott egy személy e-mailben vagy SMS-értesítés megadása

Ez a cikk bemutatja, hogyan hozhat létre és kezelheti az Azure Portalon Műveletcsoportok.

Minden művelet a következő tulajdonságok tevődik össze:

* **Név**: A műveletcsoport belül egyedi azonosítója.  
* **Művelet típusa**: A művelet végre. Ilyenek például a voice hívás, SMS, e-mailt küld; vagy automatizált műveletek különböző típusú elindítása. Tekintse meg a cikk későbbi részében típusokat. 
* **Részletek**: A vonatkozó részletekkel, végezhet *művelettípus*. 

Műveletcsoportok konfigurálása Azure Resource Manager-sablonok használatáról további információért lásd: [műveleti csoport Resource Manager-sablonok](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Műveletcsoport létrehozása az Azure portal használatával ##
1. Az a [portál](https://portal.azure.com)válassza **figyelő**. A **figyelő** ablaktábla összesíti az összes figyelési beállítást és adatokat egyetlen nézetben.

    ![A "Figyelés" szolgáltatás](./media/action-groups/home-monitor.png)
1. Válassza ki **riasztások** majd **Műveletcsoportok kezelése**.

    ![Műveletcsoportok gomb kezelése](./media/action-groups/manage-action-groups.png)
1. Válassza ki **műveleti csoport hozzáadása**, és töltse ki a mezőket.

    ![A "Csoport hozzáadása művelet" parancs](./media/action-groups/add-action-group.png)
1. Adjon meg egy nevet a a **műveletcsoport neve** mezőbe, majd adjon meg egy nevet a a **rövid, nevet** mezőbe. A rendszer a rövid nevet használja a műveletcsoport teljes neve helyett, amikor értesítéseket küld a csoport használatával.

      ![A műveleti csoport hozzáadása"párbeszédpanel](./media/action-groups/action-group-define.png)

1. A **előfizetés** az aktuális előfizetéshez autofills mezőbe. Ebben az előfizetésben a műveletcsoport elmentett.

1. Válassza ki a **erőforráscsoport** a a műveletcsoport mentve.

1. Műveletek listájának meghatározását. Adja meg a következő műveletek:

    a. **Név**: Adja meg egy egyedi azonosítót ehhez a művelethez.

    b. **Művelet típusa**: Válassza ki az e-mailek és SMS és leküldéses/Hangvétel, a logikai alkalmazás, Webhookot, ITSM vagy Automation-Runbook.

    c. **Részletek**: A művelet típusa alapján, adjon meg egy telefonszám, e-mail címét, webhook URI-t, az Azure app, ITSM-kapcsolat vagy Automation-runbook. ITSM-művelet, továbbá adja meg **munkaelem** és a többi mező az ITSM-eszközhöz van szükség.

1. Válassza ki **OK** a műveletcsoport létrehozásához.

## <a name="manage-your-action-groups"></a>A műveleti csoportok kezelése ##
Miután létrehozta a műveletcsoport, is látható, az a **Műveletcsoportok** szakaszában a **figyelő** ablaktáblán. Válassza ki a kezelni kívánt műveletcsoport:

* Adja hozzá, szerkeszthet és eltávolíthat műveleteket.
* A műveletcsoport törlése.

## <a name="action-specific-information"></a>A művelet adott információk
> [!NOTE]
> Lásd: [előfizetési szolgáltatási korlátok a figyelés](https://docs.microsoft.com/azure/azure-subscription-service-limits#monitor-limits) numerikus korlátait az alábbi elemek mindegyike számára.  

**Azure-alkalmazás leküldéses** – az Azure app-műveletek csak korlátozott számú műveletcsoport használhat. Jelenleg az Azure-alkalmazás a művelet csak ServiceHealth riasztások támogatja. Bármely más riasztástípus figyelmen kívül lesz hagyva. Lásd: [riasztások konfigurálása, ha a szolgáltatás állapotával kapcsolatos értesítés közzétételekor](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

**E-mailek** -e-mailt küld a következő e-mail-címekről. Győződjön meg arról, hogy az e-mailek szűrése megfelelően vannak konfigurálva
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Előfordulhat, hogy e-mailes műveletek csak korlátozott számú tartalmaz műveletcsoport. Tekintse meg a [sebessége korlátozza az információk](./../../azure-monitor/platform/alerts-rate-limiting.md) cikk

**ITSM** -műveletcsoport ITSM-műveletek korlátozott számú korlátozott számú lehet. ITSM-művelethez egy ITSM-kapcsolat szükséges. Ismerje meg, hogyan hozhat létre egy [ITSM-kapcsolat](../../azure-monitor/platform/itsmc-overview.md).

**Logikai alkalmazás** -Logic App-műveletek csak korlátozott számú műveletcsoport lehet.

**Alkalmazás függvény** – a Functions API-val, amelyhez jelenleg a függvényalkalmazások v2 "AzureWebJobsSecretStorageType" beállítást az alkalmazás konfigurálásához szükséges műveletek olvasnak konfigurált alkalmazások esetében a "fájlok" kulcsok a függvény. További információkért lásd: [kulcskezelés a Functions V2 vált]( https://aka.ms/funcsecrets).

**Runbook** – előfordulhat, hogy a Runbook-műveletek csak korlátozott számú tartalmaz műveletcsoport. Tekintse meg a [az Azure-előfizetési szolgáltatási korlátok](../../azure-subscription-service-limits.md) a korlátok a Runbook is észleltünk adattartalmakat.

**SMS** – előfordulhat, hogy az SMS-műveletek csak korlátozott számú tartalmaz műveletcsoport. Is megtekintheti a [sebessége korlátozza az információk](./../../azure-monitor/platform/alerts-rate-limiting.md) és [SMS-riasztás viselkedése](../../azure-monitor/platform/alerts-sms-behavior.md) további fontos információkat. 

**Beszédfelismerési** -Voice-műveletek csak korlátozott számú műveletcsoport lehet. Tekintse meg a [sebessége korlátozza az információk](./../../azure-monitor/platform/alerts-rate-limiting.md) cikk.

**Webhook** -Webhook-műveletek csak korlátozott számú műveletcsoport lehet. Webhookok a rendszer újra próbálkozik a következő szabályok alkalmazásával. A webhook hívása rendszer legfeljebb 2 idők esetén a következő HTTP-állapotkódok adja vissza: 408, 429-es, 503-as, 504 vagy a HTTP-végpont nem válaszol. Az első újrapróbálkozás 10 másodperc után történik. A második újrapróbálkozási 100 másodperc múlva történik. Két hiba után nincs műveletcsoport meghívja a végpont 30 percig. 

Forrás IP-címtartományok
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 52.244.68.117

Fogadni ezen IP-címek, azt javasoljuk, hogy a változásokat frissítések konfigurálása [Service állapotriasztás, amely figyeli a Műveletcsoportok szolgáltatással kapcsolatos tájékoztató értesítések.


## <a name="next-steps"></a>További lépések ##

* Tudjon meg többet [SMS-riasztás viselkedése](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Szerezzen egy [megértése a tevékenység log riasztási webhookséma](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Tudjon meg többet [ITSM-összekötő](../../azure-monitor/platform/itsmc-overview.md)
* Tudjon meg többet [sebességkorlátozással](../../azure-monitor/platform/alerts-rate-limiting.md) a riasztásokat.
* Get- [tevékenységnapló-riasztások áttekintése](../../azure-monitor/platform/alerts-overview.md), és a riasztások fogadása.  
* Ismerje meg, hogyan [riasztások konfigurálása, ha a szolgáltatás állapotával kapcsolatos értesítés közzétételekor](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

