---
title: Azure-szolgáltatások - figyelmeztetések létrehozása az Azure portálon |} Microsoft Docs
description: Eseményindító e-mailek, értesítések, a megadott feltételek teljesülnek webhely URL-címek (webhookok), vagy az automation hívni.
author: rboucher
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: f7457655-ced6-4102-a9dd-7ddf2265c0e2
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2016
ms.author: robb
ms.openlocfilehash: 4acf1f549a6c901fb0b772c4591f1f35d61365ad
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="create-classic-metric-alerts-in-azure-monitor-for-azure-services---azure-portal"></a>Hozzon létre klasszikus metrika riasztások Azure figyelése az Azure-szolgáltatások - Azure-portálon
> [!div class="op_single_selector"]
> * [Portal](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [Parancssori felület](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>Áttekintés

> [!NOTE]
> Ez a cikk ismerteti, hogyan régebbi metrika riasztások létrehozásához. Az Azure figyelő most már támogatja az újabb, jobb metrika riasztásokat. Ezek a riasztások több metrikát, és lehetővé teszik a dimenzionális metrikák riasztást küld. További információ [közel valós idejű metrika riasztások](monitoring-near-real-time-metric-alerts.md).
>
>

Ez a cikk bemutatja, hogyan használja az Azure-portált Azure metrika riasztások beállítása. 

A figyelési metrikákat, vagy események, az Azure-szolgáltatások alapuló riasztást kaphat.

* **Metrika értékek** -a riasztás elindítja a megadott metrika értékét ebbe a küszöbérték mindkét irányban rendel. Ez azt jelenti, hogy elindítja a mindkét Ha először a feltétel teljesül, és majd ezt követően, hogy a feltétel mikor van már nem teljesül.    
* **Tevékenység naplóeseményeket** -riasztást aktiválhatók *minden* esemény, vagy csak akkor, ha bizonyos események megtörténtekor. További információ [napló tevékenységriasztásokat](monitoring-activity-log-alerts.md).

A metrika riasztások tegye a következőket, amikor elindítja a konfigurálhatja:

* e-mail értesítések küldéséhez a szolgáltatás-rendszergazda és a társadminisztrátorok
* e-mail küldéséhez megadott további e-maileket.
* A webhook hívása
* egy Azure-runbook (csak az Azure portálról) végrehajtásának elindítása

Konfigurálhatja és metrika riasztási szabályok adatainak beolvasása

* [Azure Portal](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Parancssori felület (CLI)](insights-alerts-command-line-interface.md)
* [Az Azure figyelő REST API-n](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Riasztási szabályt létrehozni a metrika az Azure portállal
1. Az a [portal](https://portal.azure.com/), keresse meg az erőforrás figyelési érdekli, és válassza ki azt.

2. Válassza ki **riasztások (klasszikus)** a figyelés szakaszban. A szöveg és ikon eltérő lehet attól függően némileg különböző erőforrások. Ha nem talál **riasztások (klasszikus)**, előfordulhat, hogy megtalálja a **riasztások** vagy **riasztási szabályok**

    ![Figyelés](./media/insights-alerts-portal/AlertRulesButton.png)

3. Válassza ki a **metrika riasztás hozzáadása** parancsot, és töltse ki a mezőket.

    ![Riasztás beállítása](./media/insights-alerts-portal/AddAlertOnlyParamsPage.png)

4. **Név** a riasztás szabályt, majd válassza ki a **leírása**, amely értesítési e-mailt is mutatja.

5. Válassza ki a **metrika** szeretne figyelni, majd kattintson egy **feltétel** és **küszöbérték** a mérték értékét. Is választhatja, a **időszak** idő a metrika szabály a riasztási eseményindítók előtt kell biztosítani. Így például ha a riasztás 80 % fölötti keresi CPU-t használ a időszak "az elmúlt 5 percben", a riasztás váltja ki, ha a CPU már következetesen fenti 80 % 5 perc. Akkor következik be, az első eseményindító, amennyiben azt újra váltja ki, ha 5 percig 80 % alatt marad a Processzor. A CPU-metrika mérési egy percenként történik.

6. Ellenőrizze **E-mail-tulajdonosok...**  Ha azt szeretné, hogy a rendszergazdák és a társadminisztrátorok e-mailben a riasztás aktiválódásakor.

7. Ha azt szeretné, hogy további az e-maileket kap értesítést, a riasztás aktiválódásakor, adja hozzá a a **további rendszergazda email(s)** mező. Több e-mailek külön és pontosvesszővel kell elválasztani -  *email@contoso.com;email2@contoso.com*

8. Be egy érvényes URI-azonosító található a **Webhook** mezőben, ha azt szeretné, hogy a riasztás aktiválódásakor meghívta.

9. Azure Automation használatakor választhatja futtatja, a riasztás akkor következik be, amikor egy Runbookot.

10. Válassza ki **OK** végzett a riasztás létrehozása.   

Néhány percen belül a riasztás aktív, és elindítja a leírt módon.

## <a name="managing-your-alerts"></a>A riasztások kezelése
Miután létrehozott egy riasztást, kijelölheti azt és:

* A metrika küszöbérték és a tényleges értékek az előző nap egy grafikonon megtekintése.
* Szerkesztheti és törölheti azt.
* **Tiltsa le a** vagy **engedélyezése** , ha azt szeretné, ideiglenesen leállítani, vagy folytassa a riasztás-mailjeire.

## <a name="next-steps"></a>További lépések
* [Az Azure Figyelés áttekintése](monitoring-overview.md) többek között a adattípusok összegyűjtheti, és figyelje.
* További információ a [újabb metrika riasztások](monitoring-near-real-time-metric-alerts.md)
* További információ [konfigurálása webhookokkal a riasztások](insights-webhooks-alerts.md).
* További információ [riasztások konfigurálása a naplózási eseményeket](monitoring-activity-log-alerts.md).
* További információ [Azure Automation-forgatókönyveket](../automation/automation-starting-a-runbook.md).
* Első egy [diagnosztikai naplók áttekintése](monitoring-overview-of-diagnostic-logs.md) és begyűjtése részletes nagyon gyakori a szolgáltatásban.
* Első egy [metrikák gyűjtemény áttekintése](insights-how-to-customize-monitoring.md) ellenőrizze, hogy a szolgáltatás elérhető, és a gyors.
