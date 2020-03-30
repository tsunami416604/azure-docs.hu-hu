---
title: Surface Hubok figyelése az Azure Monitorsegítségével | Microsoft dokumentumok
description: A Surface Hub-megoldás sal nyomon követheti a Surface Hubok állapotát, és megismerheti azok használatát.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2018
ms.openlocfilehash: 7999735bf2d182b2811d01172adcfc89cba27dc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662501"
---
# <a name="monitor-surface-hubs-with-azure-monitor-to-track-their-health"></a>Surface Hubok figyelése az Azure Monitor segítségével az állapotuk nyomon követéséhez

![A Surface Hub szimbóluma](./media/surface-hubs/surface-hub-symbol.png)

Ez a cikk bemutatja, hogyan használhatja a Surface Hub-megoldást az Azure Monitorban a Microsoft Surface Hub-eszközök figyelésére. A megoldás segít nyomon követni a Surface Hubs állapotát, valamint megérteni, hogyan használják őket.

Minden Surface Hubhoz telepítve van a Microsoft Monitoring Agent. Az ügynökön keresztül, amely adatokat küldhet a Surface Hub egy Log Analytics munkaterület az Azure Monitoron keresztül. A naplófájlokat a Surface Hubs rendszerből olvassa be a rendszer, majd elküldi a rendszer az Azure Monitornak. Problémák, mint a kiszolgálók offline állapotban, a naptár nem szinkronizál, vagy ha az eszköz fiók nem tud bejelentkezni a Skype-on jelennek meg a Surface Hub irányítópultján az Azure Monitor. Az irányítópulton lévő adatok használatával azonosíthatja azokat az eszközöket, amelyek nem futnak, vagy amelyek más problémákkal rendelkeznek, és potenciálisan alkalmazhatja az észlelt problémák megoldását.

## <a name="install-and-configure-the-solution"></a>A megoldás telepítése és konfigurálása
A megoldás telepítésekor és konfigurálásakor vegye figyelembe az alábbi információkat. A Surface Hubs Azure Monitorban való kezeléséhez a következőkre lesz szüksége:

* A [Log Analytics-előfizetési](https://azure.microsoft.com/pricing/details/log-analytics/) szint, amely támogatja a figyelni kívánt eszközök számát. A Log Analytics díjszabása attól függően változik, hogy hány eszköz van regisztrált, és mennyi adatot dolgoz fel. Ezt figyelembe kell vennie a Surface Hub bevezetésének megtervezésekor.

Ezután vagy hozzáad egy meglévő Log Analytics-munkaterületet, vagy létrehoz egy újat. A két módszer használatával kapcsolatos részletes utasítások a [Log Analytics-munkaterület létrehozása az Azure Portalon című.](../learn/quick-create-workspace.md) A Log Analytics-munkaterület konfigurálása után kétféleképpen regisztrálhatja surface hub-eszközeit:

* Automatikusan az Intune-on keresztül
* Manuálisan a Surface Hub-eszköz **beállításai** között.

## <a name="set-up-monitoring"></a>Figyelés beállítása
Az Azure Monitor használatával figyelheti a Surface Hub állapotát és tevékenységét. A Surface Hub regisztrálása az Intune használatával vagy helyileg a Surface Hub **Beállítások** használatával történik.

## <a name="connect-surface-hubs-to-azure-monitor-through-intune"></a>Surface-elosztók csatlakoztatása az Azure Monitorhoz az Intune-on keresztül
Szüksége lesz a munkaterület-azonosítóra és a munkaterület kulcsára a Surface Hubs kezelő Napló-elemzési munkaterületéhez. Ezeket az Azure Portalon a munkaterület-beállításokból szerezheti be.

Az Intune egy Olyan Microsoft-termék, amely lehetővé teszi a Log Analytics munkaterület-konfigurációs beállításainak központi kezelését, amelyek egy vagy több eszközére vonatkoznak. Az alábbi lépésekkel konfigurálhatja eszközeit az Intune-on keresztül:

1. Jelentkezzen be az Intune-ba.
2. Nyissa meg a**Csatlakoztatott források beállításokat.** **Settings** > 
3. Szabályzat létrehozása vagy szerkesztése a Surface Hub sablon alapján.
4. Keresse meg a szabályzat Azure Operational Insights szakaszát, és adja hozzá a Log *Analytics-munkaterület-azonosítót* és a *munkaterületi kulcsot* a szabályzathoz.
5. Mentse a szabályzatot.
6. Társítsa a házirendet a megfelelő eszközcsoporthoz.

   ![Intune-házirend](./media/surface-hubs/intune.png)

Az Intune ezután szinkronizálja a Log Analytics-beállításokat a célcsoportban lévő eszközökkel, és regisztrálja őket a Log Analytics-munkaterületre.

## <a name="connect-surface-hubs-to-azure-monitor-using-the-settings-app"></a>Surface-elosztók csatlakoztatása az Azure Monitorhoz a Beállítások alkalmazás használatával
Szüksége lesz a munkaterület-azonosítóra és a munkaterület kulcsára a Surface Hubs kezelő Napló-elemzési munkaterületéhez. Ezeket az Azure Portalon a Log Analytics-munkaterület beállításaiból szerezheti be.

Ha nem használja az Intune-t a környezet kezelésére, manuálisan is regisztrálhatja az eszközöket az egyes Surface **Hubbeállításokon** keresztül:

1. A Surface Hubról nyissa meg a **Beállítások lehetőséget.**
2. Amikor a rendszer kéri, adja meg az eszköz felügyeleti hitelesítő adatait.
3. Kattintson **az Eszköz**elemre, majd a **Figyelés**csoportban kattintson **a Log Analytics-beállítások konfigurálása parancsra.**
4. Válassza **a Figyelés engedélyezése**lehetőséget.
5. A Naplóelemzésbeállításai párbeszédpanelen írja be a Log **Analytics-munkaterület azonosítóját,** és írja be a **Munkaterület-kulcsot.**  
   ![Beállítások](./media/surface-hubs/settings.png)
6. A konfiguráció befejezéséhez kattintson az **OK** gombra.

Megjelenik egy megerősítés, amely jelzi, hogy a konfiguráció sikeresen alkalmazva lett-e az eszközre. Ha az volt, egy üzenet jelenik meg, amely arról, hogy az ügynök sikeresen csatlakozott az Azure Monitorhoz. Az eszköz ezután elkezdi az adatok küldését az Azure Monitornak, ahol megtekintheti és eljárhat.

## <a name="monitor-surface-hubs"></a>Surface hubok figyelése
A Surface Hubs figyelése az Azure Monitor használatával olyan, mint bármely más regisztrált eszközök figyelése.

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Amikor a Surface Hub csempére kattint, az eszköz állapota megjelenik.

   ![A Surface Hub irányítópultja](./media/surface-hubs/surface-hub-dashboard.png)

Riasztásokat [alerts](../platform/alerts-overview.md) meglévő vagy egyéni naplókeresések alapján hozhat létre. Az Azure Monitor által a Surface Hubs-ból gyűjtött adatok használatával problémákat kereshet, és riasztást adhat meg az eszközökhöz megadott feltételekről.

## <a name="next-steps"></a>További lépések
* Az [Azure Monitor naplólekérdezései](../log-query/log-query-overview.md) segítségével részletes Surface Hub-adatokat tekinthet meg.
* Hozzon létre [riasztásokat, amelyek értesítik,](../platform/alerts-overview.md) ha problémák merülnek fel a Surface Hubs-szal.
