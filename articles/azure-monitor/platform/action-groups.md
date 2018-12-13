---
title: Az Azure Portalon a műveleti csoportok létrehozása és kezelése
description: Ismerje meg, hogyan hozhat létre és kezelheti az Azure Portalon Műveletcsoportok.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: d3183353cbadb821ac7f84c81f4da747be823e4f
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53276723"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Az Azure Portalon a műveleti csoportok létrehozása és kezelése
## <a name="overview"></a>Áttekintés ##
Műveletcsoport gyűjteménye, Azure-előfizetés tulajdonosa által megadott értesítési beállításokat. Azure Monitor és a Service Health-riasztások Műveletcsoportok használatával értesítheti a felhasználókat, hogy egy riasztás aktiválódott-e. Az azonos műveletcsoport vagy a felhasználó követelményeitől függően különböző Műveletcsoportok különböző riasztások használhatja.

Amikor egy művelet a konfigurációja szerint egy személy értesítése e-mailben vagy SMS személy kap egy megerősítő, amely azt jelzi, hogy, és ő a műveletcsoport hozzá.

Ez a cikk bemutatja, hogyan hozhat létre és kezelheti az Azure Portalon Műveletcsoportok.

Minden művelet a következő tulajdonságok tevődik össze:

* **Név**: A műveletcsoport belül egyedi azonosítója.  
* **Művelet típusa**: A művelet végrehajtásához. Ilyenek például a voice hívás, SMS, e-mailt küld; vagy automatizált műveletek különböző típusú elindítása. Tekintse meg a cikk későbbi részében típusokat. 
* **Részletek**: A megfelelő részleteket, amely régiónként eltérő *művelettípus*. 

Műveletcsoportok konfigurálása Azure Resource Manager-sablonok használatáról további információért lásd: [műveleti csoport Resource Manager-sablonok](../../monitoring-and-diagnostics/monitoring-create-action-group-with-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Műveletcsoport létrehozása az Azure portal használatával ##
1. Az a [portál](https://portal.azure.com)válassza **figyelő**. A **figyelő** panel összesíti az összes figyelési beállítást és adatokat egyetlen nézetben.

    ![A "Figyelés" szolgáltatás](./media/action-groups/home-monitor.png)
1. Válassza ki **riasztások** majd **Műveletcsoportok kezelése**.

    ![Műveletcsoportok gomb kezelése](./media/action-groups/manage-action-groups.png)
1. Válassza ki **műveleti csoport hozzáadása**, és töltse ki a mezőket.

    ![A "Csoport hozzáadása művelet" parancs](./media/action-groups/add-action-group.png)
1. Adjon meg egy nevet a a **műveletcsoport neve** mezőbe, majd adjon meg egy nevet a a **rövid, nevet** mezőbe. A rendszer a rövid nevet használja a műveletcsoport teljes neve helyett, amikor értesítéseket küld a csoport használatával.

      ![A műveleti csoport hozzáadása"párbeszédpanel](./media/action-groups/action-group-define.png)

1. A **előfizetés** az aktuális előfizetéshez autofills mezőbe. Ebben az előfizetésben a műveletcsoport elmentett.

1. Válassza ki a **erőforráscsoport** a a műveletcsoport mentve.

1. Adja meg a műveleteknek a listája, azáltal, hogy minden egyes művelethez:

    a. **Név**: Adja meg egy egyedi azonosítót ehhez a művelethez.

    b. **Művelet típusa**: Válassza ki az e-mailek és SMS és leküldéses/Hangvétel, a logikai alkalmazás, Webhookot, ITSM vagy Automation-Runbook.

    c. **Részletek**: A művelet típusa alapján, adjon meg egy telefonszám, e-mail címét, webhook URI-t, az Azure app, ITSM-kapcsolat vagy Automation-runbook. ITSM-művelet, továbbá adja meg **munkaelem** és a többi mező az ITSM-eszközhöz van szükség.

1. Válassza ki **OK** a műveletcsoport létrehozásához.

## <a name="manage-your-action-groups"></a>A műveleti csoportok kezelése ##
Miután létrehozta a műveletcsoport, is látható, az a **Műveletcsoportok** szakaszában a **figyelő** panelen. Válassza ki a kezelni kívánt műveletcsoport:

* Adja hozzá, szerkeszthet és eltávolíthat műveleteket.
* A műveletcsoport törlése.

## <a name="action-specific-information"></a>A művelet adott információk
**Azure-alkalmazás leküldéses** -műveletcsoport az Azure app-műveletek legfeljebb 10 lehet. Jelenleg az Azure-alkalmazás a művelet csak ServiceHealth riasztások támogatja. Más riasztási bármikor figyelmen kívül lesz hagyva. Lásd: [riasztások konfigurálása, ha a szolgáltatás állapotával kapcsolatos értesítés közzétételekor](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

**E-mailek** -e-mailt küld a következő e-mail-címekről. Győződjön meg arról, hogy az e-mailek szűrése megfelelően vannak konfigurálva
   - azure-noreply@microsoft.com
   - azureemail-noreply@microsoft.com
   - alerts-noreply@mail.windowsazure.com

Műveletcsoport e-mailes műveletek akár 1000 is lehet. Tekintse meg a [sebessége korlátozza az információk](./../../monitoring-and-diagnostics/monitoring-alerts-rate-limiting.md) cikk

**ITSM** – előfordulhat, hogy legfeljebb 10 műveleti csoport ITSM művelet az ITSM-műveletek egy ITSM-kapcsolatot igényel. Ismerje meg, hogyan hozhat létre egy [ITSM-kapcsolat](../../azure-monitor/platform/itsmc-overview.md).

**Logikai alkalmazás** – előfordulhat, hogy legfeljebb 10 Logic App-műveletek tartalmaz műveletcsoportot

**Alkalmazás függvény** -műveletek a Functions API-val, amelyhez jelenleg szükség van a függvényalkalmazások v2 "AzureWebJobsSecretStorageType" beállítást "fájlok" az alkalmazás konfigurálásához, olvasható konfigurált alkalmazások esetében a funkcióbillentyűket lásd [ Kulcskezelés a Functions V2 vált]( https://aka.ms/funcsecrets) további információt.

**Runbook** – előfordulhat, hogy legfeljebb 10 Runbook műveleteket tartalmaz egy művelet csoport hivatkozik, amely a [az Azure-előfizetési szolgáltatási korlátok](../../azure-subscription-service-limits.md) a korlátok a Runbook is észleltünk adattartalmakat.

**SMS** – előfordulhat, hogy legfeljebb 10 SMS műveleteket tartalmaz egy művelet csoport tekintse meg a [sebessége korlátozza az adatokat](./../../monitoring-and-diagnostics/monitoring-alerts-rate-limiting.md) lásd a cikk a [SMS-riasztás viselkedése](../../monitoring-and-diagnostics/monitoring-sms-alert-behavior.md) cikk

**Beszédfelismerési** – előfordulhat, hogy legfeljebb 10 hangalapú műveleteket tartalmaz műveletcsoportot</dd>
Tekintse meg a [sebessége korlátozza az információk](./../../monitoring-and-diagnostics/monitoring-alerts-rate-limiting.md) cikk</dd>

**Webhook** -műveletcsoport Webhook-műveletek legfeljebb 10 lehet. Újrapróbálkozási logika – az időkorlát választ érték 10 másodperc. A webhook hívása lesz újból legfeljebb 2 idők esetén a következő HTTP-állapotkódok adja vissza: 408, 429-es, 503-as, 504 vagy a HTTP-végpont nem válaszol. Az első újrapróbálkozás 10 másodperc után történik. A második és az utolsó újrapróbálkozási 100 másodperc múlva történik.

Forrás IP-címtartományok
    - 13.106.57.181
    - 13.106.54.3
    - 13.106.54.19
    - 13.106.38.142
    - 13.106.38.148
    - 13.106.57.196

A érintő változásokról ezen IP-címek, javasoljuk, hogy konfigurálja a frissítések fogadásához egy [szolgáltatásállapot-riasztás](./../../monitoring-and-diagnostics/monitoring-service-notifications.md) amely figyeli a Műveletcsoportok szolgáltatással kapcsolatos tájékoztató értesítések.


## <a name="next-steps"></a>További lépések ##
* Tudjon meg többet [SMS-riasztás viselkedése](../../monitoring-and-diagnostics/monitoring-sms-alert-behavior.md).  
* Szerezzen egy [megértése a tevékenység log riasztási webhookséma](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Tudjon meg többet [ITSM-összekötő](../../azure-monitor/platform/itsmc-overview.md)
* Tudjon meg többet [sebességkorlátozással](../../monitoring-and-diagnostics/monitoring-alerts-rate-limiting.md) a riasztásokat.
* Get- [tevékenységnapló-riasztások áttekintése](../../monitoring-and-diagnostics/monitoring-overview-alerts.md), és a riasztások fogadása.  
* Ismerje meg, hogyan [riasztások konfigurálása, ha a szolgáltatás állapotával kapcsolatos értesítés közzétételekor](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
