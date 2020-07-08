---
title: Feltételes hozzáférési szabályzat konfigurálása csak jelentési módban – Azure Active Directory
description: A csak jelentési mód használata feltételes hozzáféréssel a támogatáshoz az elfogadás során
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46a00d55c58992be1009da1de5441ebe4e589a70
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83994969"
---
# <a name="configure-a-conditional-access-policy-in-report-only-mode"></a>Feltételes hozzáférési szabályzat konfigurálása csak jelentési módban

Feltételes hozzáférési szabályzat konfigurálása csak jelentési módban:

> [!IMPORTANT]
> Ha a szervezet még nem tette [meg, állítsa be Azure monitor integrációt az Azure ad-vel](#set-up-azure-monitor-integration-with-azure-ad). Ezt a folyamatot el kell végezni ahhoz, hogy az adatellenőrzés megtörténjen.

1. Jelentkezzen be a **Azure Portalba** feltételes hozzáférési rendszergazdaként, biztonsági rendszergazdaként vagy globális rendszergazdaként.
1. Keresse meg **Azure Active Directory**  >  **biztonsági**  >  **feltételes hozzáférését**.
1. Válassza az **új szabályzat**lehetőséget.
1. Szükség szerint konfigurálja a házirend feltételeit és a szükséges engedélyezési vezérlőket.
1. A **házirend engedélyezése** beállításnál állítsa be a váltás **csak jelentés** módba beállítást.
1. Válassza a **Mentés** lehetőséget.

> [!TIP]
> Szerkesztheti a meglévő szabályzatok engedélyezési **házirendjét** **a** (z) rendszerről **csak jelentésre** , de ezzel letiltja a házirendek betartatását. 

Jelentés megtekintése – csak az Azure AD bejelentkezési naplóiban található eredmény.

Egy adott bejelentkezéshez tartozó jelentés-házirend eredményének megtekintéséhez:

1. Jelentkezzen be a **Azure Portal** jelentés-olvasó, biztonsági olvasó, biztonsági rendszergazda vagy globális rendszergazdaként.
1. Tallózással keresse meg **Azure Active Directory**a  >  **bejelentkezéseket**.
1. Válasszon ki egy bejelentkezést, vagy adjon hozzá szűrőket a szűkített eredményekhez.
1. A **részletek** legördülő menüben válassza a **csak jelentés** lapot a bejelentkezés során kiértékelt szabályzatok megtekintéséhez.

> [!NOTE]
> A bejelentkezési naplók letöltésekor válassza a JSON-formátum lehetőséget a feltételes hozzáférés jelentési eredményeinek befoglalásához.

## <a name="set-up-azure-monitor-integration-with-azure-ad"></a>Azure Monitor integráció beállítása az Azure AD-vel

Ha szeretné megtekinteni a feltételes hozzáférési szabályzatok összesített hatását az új feltételes hozzáférési elemzések használatával, integrálnia kell Azure Monitor az Azure AD-vel, és exportálnia kell a bejelentkezési naplókat. Az integráció beállítása két lépésből áll: 

1. [Regisztráljon Azure monitor előfizetésre, és hozzon létre egy munkaterületet](/azure/azure-monitor/learn/quick-create-workspace).
1. [Exportálja a bejelentkezési naplókat az Azure ad](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)-ből a Azure monitorba.

A Azure Monitor díjszabásával kapcsolatos további információkért tekintse meg a [Azure monitor díjszabási oldalát](https://azure.microsoft.com/pricing/details/monitor/). A költségeket megbecsülő erőforrások, a napi korlát beállítása vagy az adatmegőrzési időszak testreszabása a cikkben található, a [használat és a költségek kezelése Azure monitor naplók](../../azure-monitor/platform/manage-cost-storage.md#estimating-the-costs-to-manage-your-environment)használatával.

## <a name="view-conditional-access-insights-workbook"></a>Feltételes hozzáférési adatáttekintési munkafüzet megtekintése

Miután Azure Monitorrel integrálta Azure AD-naplóit, a feltételes hozzáférési szabályzatok hatásait az új feltételes hozzáférési elemzések használatával figyelheti.

1. Jelentkezzen be a **Azure Portal** biztonsági rendszergazdaként vagy globális rendszergazdaként.
1. Tallózással keresse meg **Azure Active Directory**  >  **munkafüzeteket**.
1. Válassza a **feltételes hozzáférési ismeretek**lehetőséget.
1. Válasszon ki egy vagy több szabályzatot a **feltételes hozzáférési** szabályzat legördülő listából. Alapértelmezés szerint az összes engedélyezett házirend ki van választva.
1. Válasszon időtartományt (ha az időtartomány meghaladja a rendelkezésre álló adatkészletet, a jelentés megjeleníti az összes elérhető adatokat). Miután beállította a **feltételes hozzáférési házirendet** és az **időtartomány** paramétereit, a rendszer betölti a jelentést.
   1. Ha szeretné, megkeresheti az egyes **felhasználókat** vagy **alkalmazásokat** , hogy szűkítse a jelentés hatókörét.
1. Válassza ki az időtartományban lévő, a felhasználók számával vagy a bejelentkezések számával megtekintett értékeit.
1. Az **adatnézettől**függően a **hatás összegzése** a kiválasztott paraméterek hatókörében lévő felhasználók vagy bejelentkezések számát jeleníti meg, a teljes szám, a **siker**, a **hiba**, a **szükséges felhasználói beavatkozás**és a **nem alkalmazott**beállítások szerint csoportosítva. Válasszon ki egy csempét egy adott eredményhalmaz bejelentkezésének vizsgálatához. 
   1. Ha módosította a munkafüzet paramétereit, dönthet úgy, hogy a későbbi használat érdekében menti a másolatokat. Válassza a Mentés ikont a jelentés tetején, és adjon meg egy nevet és egy helyet a mentéshez.
1. Görgessen lefelé az egyes feltételekhez tartozó bejelentkezések részletezésének megtekintéséhez.
1. Tekintse meg a jelentés alján található **bejelentkezési adatokat** , és vizsgálja meg az egyéni bejelentkezési eseményeket, amelyeket a fenti beállítások alapján szűr.

> [!TIP] 
> Le kell részletezni egy adott lekérdezést, vagy exportálnia kell a bejelentkezési adatokat? Válassza ki a lekérdezéstől jobbra található gombot a lekérdezés megnyitásához Log Analyticsban. Válassza az Exportálás lehetőséget a CSV-be vagy a Power BIba való exportáláshoz.

## <a name="common-problems-and-solutions"></a>Gyakori problémák és megoldások

### <a name="why-are-the-queries-in-the-workbook-failing"></a>Miért nem sikerül a lekérdezés a munkafüzetben?

Az ügyfelek észrevették, hogy a lekérdezések néha meghiúsulnak, ha a hibás vagy több munkaterület van társítva a munkafüzethez. A probléma megoldásához kattintson a munkafüzet tetején található **Szerkesztés** gombra, majd a beállítások fogaskerékre. Válassza ki, majd távolítsa el a munkafüzethez nem társított munkaterületeket. Az egyes munkafüzetekhez csak egy munkaterület társítható.

### <a name="why-doesnt-the-conditional-access-policies-dropdown-parameter-contain-my-policies"></a>Miért nem tartalmazza a feltételes hozzáférési szabályzatok legördülő paraméterét a szabályzatom?

A feltételes hozzáférési szabályzatok legördülő lista a legutóbbi bejelentkezések 4 órás időszakon keresztüli lekérdezésével töltődik fel. Ha a bérlő nem rendelkezik bejelentkezéssel az elmúlt 4 órában, akkor előfordulhat, hogy a legördülő lista üres lesz. Ha ez a késés állandó probléma, például a ritkán előforduló kis-és nagyvállalati bejelentkezések esetében, a rendszergazdák szerkeszthetik a feltételes hozzáférési házirendek legördülő lekérdezését, és a lekérdezés időtartamát 4 óránál hosszabb ideig meghosszabbítják.

## <a name="next-steps"></a>További lépések

[Feltételes hozzáférés – közös szabályzatok](concept-conditional-access-policy-common.md)

További információ az Azure AD-munkafüzetekről: [Azure monitor munkafüzetek használata Azure Active Directory jelentésekhez](../reports-monitoring/howto-use-azure-monitor-workbooks.md).
