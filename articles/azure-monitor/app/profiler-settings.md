---
title: Az Azure Application Insights Profiler-beállítások ablaktábla használata | Microsoft dokumentumok
description: 'Lásd: Profilozó állapota és profilkészítési munkamenetek indítása'
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: f66a23c0562ec9f1987bd119a45b7b767f2dfe46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671630"
---
# <a name="configure-application-insights-profiler"></a>Az Application Insights-profilozó konfigurálása

## <a name="updated-profiler-agent"></a>Frissítve Profiler Ügynök
Az eseményindító funkciók csak a profilozó ügynök 2.6-os vagy újabb verziójával működnek. Ha egy Azure App Service-t futtat, az ügynök automatikusan frissül. Láthatja, hogy az ügynök melyik verzióját futtatja, ha a webhely Kudu URL-címére lép, és https://yourwebsite.scm.azurewebsites.net/diagnosticserviceshozzáfűzi a \DiagnosticServices parancsot a következőhöz: . Az Application Insights Profiler webjoba 2.6-os vagy újabb verziójú. A webalkalmazás újraindításával kényszerítheti a frissítést. 

Ha a profilozót virtuális gépen vagy felhőszolgáltatáson futtatja, telepítenie kell a Windows Azure Diagnostics (WAD) bővítmény 16.0.4-es verzióját vagy újabb verzióját. A WAD verzióját úgy ellenőrizheti, hogy bejelentkezik a virtuális gépre, és megkeresi ezt a könyvtárat: C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.16.0.4. A könyvtárnév a TELEPÍTETT WAD verziója. Az Azure virtuálisgép-ügynök automatikusan frissíti a WAD-t, ha új verziók érhetők el.

## <a name="profiler-settings-page"></a>Profilozó beállításai lap

Az Azure Application Insights Profiler-beállítások ablaktábla megnyitásához nyissa meg az Application Insights teljesítményablaktábláját, és válassza a **Profilkezelő konfigurálása** gombot.

![Hivatkozás a Profilozó beállításai lap megnyitásához][configure-profiler-entry]

Ez megnyit egy oldalt, amely így néz ki:

![Profilozó beállításai lap][configure-profiler-page]

Az **Application Insights Profiler konfigurálása** lap a következő funkciókkal rendelkezik:

| | |
|-|-|
Profil most | Elindítja a profilkészítési munkameneteket az Application Insights ezen példányához kapcsolódó összes alkalmazáshoz.
Eseményindítók | Lehetővé teszi a profilozó futtatását okozó eseményindítók konfigurálását. 
Legutóbbi profilkészítési munkamenetek | A korábbi profilozási munkamenetekkel kapcsolatos információk megjelenítése.

## <a name="profile-now"></a>Profil most
Ez a beállítás lehetővé teszi, hogy igény szerint elindítson egy profilkészítési munkamenetet. Ha erre a hivatkozásra kattint, az összes profilkészítő ügynök, amely adatokat küld erre az Application Insights-példányra, megkezdi a profil rögzítését. 5-10 perc elteltével a profilmunkamenet megjelenik az alábbi listában.

Ahhoz, hogy egy felhasználó manuálisan elindítegy profiler-munkamenetet, legalább "írási" hozzáférésre van szükségük az Application Insights-összetevő szerepköréhez. A legtöbb esetben automatikusan megkapja ezt a hozzáférést, és nincs szükség további munkára. Ha problémákmerülnek fel, az előfizetéshatókör-szerepkör hozzáadása az "Application Insights-összetevő közreműködője" szerepkör lesz. [További információk a szerepkör-hozzáférés-vezérlésről az Azure Monitoring segítségével.](https://docs.microsoft.com/azure/azure-monitor/app/resources-roles-access-control)

## <a name="trigger-settings"></a>Eseményindító beállításai
![Eseményindító beállítások úszó panelje][trigger-settings-flyout]

A menüsor Eseményindítók gombjára kattintva megnyílik az eseményindító beállításai mező. Beállíthatja, hogy az eseményindító elindítsa a profilkészítést, ha a CPU- vagy memóriahasználat százalékos aránya eléri a beállított szintet.

| | |
|-|-|
Be / Ki gomb | On: profiler lehet indítani ezt az eseményindítót; Ki: profiler nem indul el ez az eseményindító.
Memória küszöbértéke | Ha a memória ezen százaléka használatban van, a profilozó elindul.
Időtartam | Beállítja, hogy a profilozó mennyi ideig fog futni az aktiváláskor.
Hűtő | Beállítja, hogy a profilozó mennyi ideig várjon, mielőtt újra ellenőrizné a memória- vagy processzorhasználatot az aktiválás után.

## <a name="recent-profiling-sessions"></a>Legutóbbi profilkészítési munkamenetek
Az oldal ezen része a legutóbbi profilkészítési munkamenetekkel kapcsolatos információkat jeleníti meg. A profilkészítési munkamenet azt az időszakot jelöli, amikor a profilkészítő ügynök az alkalmazást üzemeltető egyik gépen profilt vett fel. A profilokat egy munkamenetből az egyik sorra kattintva nyithatja meg. Minden egyes munkamenethez a következőket mutatjuk be:

| | |
|-|-|
Által kiváltott | Hogyan indult a munkamenet, vagy egy eseményindító, Profil most vagy alapértelmezett mintavételezés. 
Alkalmazásnév | A profilozott alkalmazás neve.
Géppéldány | Annak a gépnek a neve, amelyen a profilügynök futott.
Időbélyeg | A profil rögzítésének időpontja.
Tracee között | Az egyedi kérelmekhez csatolt nyomkövetések száma.
CPU % | A profilozó futása közben használt processzor százalékos aránya.
Memória % | A profilozó futása közben használt memória százaléka.

## <a name="use-web-performance-tests-to-generate-traffic-to-your-application"></a><a id="profileondemand"></a>Webes teljesítménytesztek használata az alkalmazás forgalmának létrehozásához

A Profiler-t manuálisan, egyetlen kattintással aktiválhatja. Tegyük fel, hogy webes teljesítménytesztet futtat. A webalkalmazás terhelés alatt való működésének megértéséhez nyomokra lesz szüksége. A nyomkövetések rögzítése korának szabályozása kulcsfontosságú, mert tudja, hogy mikor fog futni a terhelési teszt. De a véletlenszerű mintavételi időköz talán elmulasztja.

A következő szakaszok bemutatják, hogyan működik ez a forgatókönyv:

### <a name="step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>1. lépés: A webalkalmazás forgalmának generálása webes teljesítményteszt elindításával

Ha a webalkalmazás már rendelkezik bejövő forgalommal, vagy csak manuálisan szeretne forgalmat generálni, hagyja ki ezt a szakaszt, és folytassa a 2.

1. Az Application Insights portálon válassza a**Teljesítménytesztelés** **konfigurálása** > lehetőséget. 

1. Új teljesítményteszt indításához válassza az **Új** gombot.

   ![új teljesítményteszt létrehozása][create-performance-test]

1. Az **Új teljesítményteszt** ablaktáblában konfigurálja a tesztcél URL-címét. Fogadja el az összes alapértelmezett beállítást, majd válassza a **Teszt futtatása** lehetőséget a betöltési teszt futtatásához.

    ![Terhelési teszt konfigurálása][configure-performance-test]

    Először az új teszt kerül várólistára, majd a folyamatban lévő állapot *következik.*

    ![A betöltési teszt elküldésre és várólistára kerül][load-test-queued]

    ![Folyamatban van a betöltési teszt][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>2. lépés: Profilozó igény szerinti munkamenetindítása

1. Amikor a betöltési teszt fut, indítsa el a Profiler-t, hogy rögzítse a nyomkövetéseket a webalkalmazásban, miközben az terhelést kap.

1. Nyissa meg a **Profilkezelő konfigurálása** ablaktáblát.


### <a name="step-3-view-traces"></a>3. lépés: Nyomkövetések megtekintése

Miután a Profiler befejezte a futást, kövesse az értesítésre vonatkozó utasításokat a Teljesítmény ablaktáblára való ugráshoz és a nyomkövetések megtekintéséhez.

## <a name="next-steps"></a>További lépések
[Profilozó engedélyezése és nyomkövetések megtekintése](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
[configure-profiler-page]: ./media/profiler-settings/configureBlade.png
[trigger-settings-flyout]: ./media/profiler-settings/CPUTrigger.png
[create-performance-test]: ./media/profiler-settings/new-performance-test.png
[configure-performance-test]: ./media/profiler-settings/configure-performance-test.png
[load-test-queued]: ./media/profiler-settings/load-test-queued.png
[load-test-in-progress]: ./media/profiler-settings/load-test-inprogress.png
[enable-app-insights]: ./media/profiler-settings/enable-app-insights-blade-01.png
[update-site-extension]: ./media/profiler-settings/update-site-extension-01.png
[change-and-save-appinsights]: ./media/profiler-settings/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/profiler-settings/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/profiler-settings/check-extension-update-01.png
[profiler-timeout]: ./media/profiler-settings/profiler-timeout.png
