---
title: Klasszikus Azure-szolgáltatások figyelmeztetések létrehozása az Azure-portál használatával |} Microsoft Docs
description: Indítás, e-maileket vagy értesítések, vagy a webhely URL-címek (webhookok) vagy az automation hívni a megadott feltételek teljesülnek.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/23/2016
ms.author: robb
ms.component: alerts
ms.openlocfilehash: d0e5512eb3f963898ded6d7155f8b75cfb6ef911
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287429"
---
# <a name="use-the-azure-portal-to-create-classic-metric-alerts-in-azure-monitor-for-azure-services"></a>Az Azure-portálon használja a klasszikus metrika riasztások létrehozása az Azure-figyelő Azure-szolgáltatások 

> [!div class="op_single_selector"]
> * [Portal](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [Parancssori felület](insights-alerts-command-line-interface.md)
>

> [!NOTE]
> Ez a cikk ismerteti, hogyan régebbi klasszikus metrika riasztások létrehozásához. Az Azure figyelő most támogatja [újabb metrika riasztások](monitoring-near-real-time-metric-alerts.md). 


Ez a cikk bemutatja, hogyan klasszikus Azure metrika riasztások beállítása az Azure portál használatával. 

Az Azure-szolgáltatások metrikáját alapuló riasztást kaphat, vagy az Azure-ban is fogadhatja az eseményekre vonatkozó riasztást.

* **Metrika értékek**: A riasztás elindítja a megadott metrika értékét ebbe a küszöbérték, akkor mindkét irányban rendel. Ez azt jelenti, hogy elindítja a mindkét Ha először a feltétel teljesül, majd amikor ez a feltétel már nem van teljesül.    

* **Tevékenység naplóeseményeket**: riasztást aktiválhatók *minden* esemény, vagy amikor bizonyos események következnek be. További információ [napló tevékenységriasztásokat](monitoring-activity-log-alerts.md).

A klasszikus metrika riasztások tegye a következőket, amikor elindítja a konfigurálhatja:

* A szolgáltatás-rendszergazda és a társadminisztrátorok e-mail értesítések küldéséhez.
* E-mail küldése további e-mail-címek.
* A webhook hívja.
* Egy Azure-runbook (csak az Azure portálról) végrehajtásának elindítása.

Beállíthatja, klasszikus metrika riasztási szabályok adatainak beolvasása a következő helyekről: 

* [Azure Portal](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Azure CLI](insights-alerts-command-line-interface.md)
* [Az Azure figyelő REST API-n](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Riasztási szabályt létrehozni a metrika az Azure portállal
1. Az a [portal](https://portal.azure.com/), és keresse meg a figyelni kívánt erőforrás, majd válassza ki azt.

2. Az a **figyelés** szakaszban jelölje be **riasztások (klasszikus)**. A szöveg és ikon kis mértékben eltérhetnek a különböző erőforrásokat. Ha nem találja a **riasztások (klasszikus)** itt, bizonyára hasznosnak találja a **riasztások** vagy **riasztási szabályok**.

    ![Figyelés](./media/insights-alerts-portal/AlertRulesButton.png)

3. Válassza ki a **metrika riasztás hozzáadása (klasszikus)** parancsot, majd töltse ki a mezőket.

    ![Riasztás beállítása](./media/insights-alerts-portal/AddAlertOnlyParamsPage.png)

4. **Név** a riasztási szabályt. Válassza ki a **leírása**, amely is megjelenik az értesítési e-maileket.

5. Válassza ki a **metrika** , amelyet figyelni szeretne. Válassza ki a **feltétel** és **küszöbérték** a mérték értékét. Is választhatja, a **időszak** idő a metrika szabály a riasztási eseményindítók előtt kell biztosítani. Például ha a riasztás keresi a 80 % fölötti CPU-t használ a időszak "az elmúlt 5 percben", a riasztás váltja ki, ha a CPU már következetesen fenti 80 % 5 perc. Miután az első eseményindító történik, akkor váltja ki újra, ha 5 percig 80 % alatt marad a Processzor. A CPU-metrika mérési percenként történik.

6. Válassza ki **E-mail-tulajdonosok...**  Ha azt szeretné, és közös rendszergazdák e-mail értesítéseket a riasztás aktiválódásakor.

7. Ha azt szeretné, az értesítések küldését további e-mail címet, a riasztás aktiválódásakor, adja hozzá a a **további rendszergazda email(s)** mező. Pontosvesszővel több e-maileket, a következő formátumban:  *email@contoso.com; email2@contoso.com*

8. Be egy érvényes URI-azonosító található a **Webhook** mezőben, ha azt szeretné, hogy hívható meg abban az esetben, ha a riasztás akkor következik be.

9. Azure Automation használatakor választhatja futtatja, a riasztás akkor következik be, amikor egy runbookot.

10. Válassza ki **OK** a riasztás létrehozása.   

Néhány percen belül a riasztás aktív, és elindítja a leírt módon.

## <a name="manage-your-alerts"></a>A riasztások kezelése
Miután létrehozott egy riasztást, válassza ki azt, és hajtsa végre az alábbi műveletek közül:

* Diagramját, amelyek mérték küszöbértéke, a tényleges értékek az előző nap megtekintése.
* Szerkesztheti és törölheti azt.
* **Tiltsa le a** vagy **engedélyezése** , ha azt szeretné, ideiglenesen leállítani, vagy folytassa a riasztás-mailjeire.

## <a name="next-steps"></a>További lépések
* [Az Azure Figyelés áttekintése](monitoring-overview.md), beleértve a adattípusok összegyűjtheti, és figyelje.
* További információ a [újabb metrika riasztások](monitoring-near-real-time-metric-alerts.md).
* További információ [konfigurálása webhookokkal a riasztások](insights-webhooks-alerts.md).
* További információ [riasztások konfigurálása a naplózási eseményeket](monitoring-activity-log-alerts.md).
* További információ [Azure Automation-runbook](../automation/automation-starting-a-runbook.md).
* Első egy [diagnosztikai naplók áttekintése](monitoring-overview-of-diagnostic-logs.md), és nagyon gyakori gyűjtéséhez részletes a a szolgáltatásban.
* Első egy [metrikák gyűjtemény áttekintése](insights-how-to-customize-monitoring.md) győződjön meg arról, hogy a szolgáltatás elérhető, és a gyors-e.
