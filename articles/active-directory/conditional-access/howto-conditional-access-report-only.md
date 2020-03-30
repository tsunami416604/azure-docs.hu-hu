---
title: Feltételes hozzáférési házirend konfigurálása csak jelentésmódban – Azure Active Directory
description: Csak jelentés módban a feltételes hozzáférés támogatás elfogadása kor
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d9bec829d7fb3e76eb243bda73423303670585e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295117"
---
# <a name="configure-a-conditional-access-policy-in-report-only-mode-preview"></a>Feltételes hozzáférési házirend konfigurálása csak jelentés módban (előzetes verzió)

Feltételes hozzáférési házirend konfigurálása csak jelentésmódban:

> [!IMPORTANT]
> Ha a szervezet még nem, állítsa be az [Azure Monitor integráció az Azure AD.](#set-up-azure-monitor-integration-with-azure-ad) Ennek a folyamatnak azelőtt kell megtelnie, hogy az adatok áttekinthetők lennének.

1. Jelentkezzen be az **Azure Portalon** feltételes hozzáférés-rendszergazdaként, biztonsági rendszergazdaként vagy globális rendszergazdaként.
1. Tallózással keresse meg az **Azure Active Directory** > **biztonsági** > **feltételes hozzáférését.**
1. Válassza az **Új házirend lehetőséget.**
1. Szükség szerint konfigurálja a házirend-feltételeket és a szükséges támogatási vezérlőket.
1. A **Házirend engedélyezése** csoportban állítsa a kapcsolót csak **jelentés** módba.
1. **Mentés** kiválasztása

> [!TIP]
> Egy meglévő házirend **házirendállapotának engedélyezése** **bekapcsolva** **a csak jelentésig,** de ezzel letiltja a házirend-kényszerítést. 

Tekintse meg a csak jelentés eredménye az Azure AD bejelentkezési naplókban.

Egy adott bejelentkezéshez tartozó, csak jelentésre vonatkozó házirend eredményének megtekintése:

1. Jelentkezzen be az **Azure Portalon** jelentésolvasóként, biztonsági olvasóként, biztonsági rendszergazdaként vagy globális rendszergazdaként.
1. Tallózással keresse meg az **Azure Active Directory** > **bejelentkezéseit.**
1. Jelöljön ki egy bejelentkezést, vagy adjon hozzá szűrőket az eredmények szűkítéséhez.
1. A **Részletek** fiókban válassza a **Csak jelentés (előzetes verzió)** lapot a bejelentkezés során kiértékelt szabályzatok megtekintéséhez.

> [!NOTE]
> A bejelentkezési naplók letöltésekor válassza a JSON formátumot a csak feltételes hozzáférésű jelentés eredményadatainak felvételéhez.

## <a name="set-up-azure-monitor-integration-with-azure-ad"></a>Az Azure Monitor integrációjának beállítása az Azure AD-vel

A feltételes hozzáférés-szabályzatok összesített hatásának megtekintéséhez az új feltételes hozzáférés-insights-munkafüzet használatával integrálnia kell az Azure Monitort az Azure AD-vel, és exportálnia kell a bejelentkezési naplókat. Az integráció beállításához két lépés ből áll: 

1. [Regisztráljon egy Azure Monitor-előfizetésre, és hozzon létre egy munkaterületet.](/azure/azure-monitor/learn/quick-create-workspace)
1. [Exportálja a bejelentkezési naplókat az Azure AD-ből az Azure Monitorba.](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

Az Azure Monitor díjszabásáról az [Azure Monitor díjszabási lapján](https://azure.microsoft.com/pricing/details/monitor/)talál további információt. A költségek becsléséhez, a napi korlát beállításához vagy az adatmegőrzési időszak testreszabásához szükséges erőforrások a Használat és a költségek kezelése az [Azure Monitor naplókkal](../../azure-monitor/platform/manage-cost-storage.md#estimating-the-costs-to-manage-your-environment)című cikkben találhatók.

## <a name="view-conditional-access-insights-workbook"></a>Feltételes hozzáférés-elemzési munkafüzet megtekintése

Miután integrálta az Azure AD-naplókat az Azure Monitorral, figyelheti a feltételes hozzáférési szabályzatok hatását az új feltételes hozzáférés-insights-munkafüzetek használatával.

1. Jelentkezzen be az **Azure Portalon** biztonsági rendszergazdaként vagy globális rendszergazdaként.
1. Tallózással keresse meg az Azure Active > **Directory-munkafüzeteket.** **Azure Active Directory**
1. Válassza a **Feltételes hozzáférés-elemzési adatok (előzetes verzió) lehetőséget.**
1. Jelöljön ki egy vagy több házirendet a **feltételes hozzáférési házirend** legördülő menüből. Alapértelmezés szerint az összes engedélyezett házirend ki van jelölve.
1. Jelöljön ki egy időtartományt (ha az időtartomány meghaladja a rendelkezésre álló adatkészletet, a jelentés az összes rendelkezésre álló adatot jeleníti meg). Miután beállította a **feltételes hozzáférési szabályzat** és az **időtartomány** paramétereit, a jelentés betöltődik.
   1. Szükség esetén az egyes **felhasználók** vagy **alkalmazások** keresése a jelentés hatókörének szűkítéséhez.
1. Válassza az időtartományban lévő adatok megtekintése a felhasználók száma vagy a bejelentkezések száma szerint.
1. Az Adat **nézettől**függően a **Hatásösszegzés** megjeleníti a felhasználók vagy bejelentkezések számát a kiválasztott paraméterek hatókörében, összesen, **Sikeres,** **Sikertelen**, **Felhasználói művelet szükséges**és Nincs **alkalmazva.** Jelöljön ki egy csempét egy adott eredménytípus bejelentkezéseinek vizsgálatához. 
   1. Ha módosította a munkafüzet paramétereit, mentheti a másolatot későbbi használatra. Válassza a jelentés tetején látható Mentés ikont, és adja meg a mentés helyét és helyét.
1. Görgessen le az egyes feltételek bejelentkezéseinek bontásának megtekintéséhez.
1. Tekintse meg a **bejelentkezési részletek** alján a jelentés, hogy vizsgálja meg az egyes bejelentkezési események szűrt a fenti kijelölések.

> [!TIP] 
> Le kell ásnia egy adott lekérdezést, vagy exportálnia kell a bejelentkezési adatokat? Válassza ki a lekérdezés jobb gombját a lekérdezés megnyitása a Log Analytics-ben. Válassza az Exportálás lehetőséget a CSV-re vagy a Power BI-ba való exportáláshoz.

## <a name="common-problems-and-solutions"></a>Gyakori problémák és megoldások

### <a name="why-are-the-queries-in-the-workbook-failing"></a>Miért nem sikerül a munkafüzetben lévő lekérdezések?

Az ügyfelek észrevették, hogy a lekérdezések néha sikertelenek, ha a munkafüzethez rossz vagy több munkaterület van társítva. A probléma megoldásához kattintson a munkafüzet tetején található **Szerkesztés** gombra, majd a Beállítások eszközre. Jelölje ki, majd távolítsa el a munkafüzethez nem társított munkaterületeket. Minden munkafüzethez csak egy munkaterület tartozhat.

### <a name="why-doesnt-the-conditional-access-policies-dropdown-parameter-contain-my-policies"></a>Miért nem tartalmazza a feltételes hozzáférési házirendek legördülő paraméter a szabályzataimat?

A feltételes hozzáférési házirendek legördülő feltöltésével a legutóbbi bejelentkezések lekérdezése 4 órán keresztül. Ha a bérlő nem rendelkezik bejelentkezések az elmúlt 4 órában, lehetséges, hogy a legördülő üres lesz. Ha ez a késleltetés állandó probléma, például a ritkán bejelentkező kis bérlőknél, a rendszergazdák szerkeszthetik a feltételes hozzáférési házirendek legördülő lekérdezését, és a lekérdezés idejét 4 óránál hosszabb időre hosszabbíthatják meg.

## <a name="next-steps"></a>További lépések

[Feltételes hozzáférés közös házirendjei](concept-conditional-access-policy-common.md)

Az Azure AD-munkafüzetekről további információt a ["Használat az Azure Monitor munkafüzetek azure Active Directory-jelentésekhez"](../reports-monitoring/howto-use-azure-monitor-workbooks.md)című cikkben talál.
