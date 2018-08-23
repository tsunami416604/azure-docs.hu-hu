---
title: A Visual Studio közzététel az Azure-alkalmazás varázsló |} A Microsoft Docs
description: Ismerje meg, hogyan a különféle beállításainak konfigurálása a Visual Studio Azure alkalmazás közzététele varázsló
services: visual-studio-online
author: ghogen
manager: douge
assetId: 7d8f1ac9-e439-47e0-a183-0642c4ea1920
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: 97d78bd61f7cf2a651fea1ac29e2a952a8f8ced3
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42058433"
---
# <a name="using-the-visual-studio-publish-azure-application-wizard"></a>A Visual Studio közzététel az Azure-alkalmazás varázsló

A Visual Studióban egy webalkalmazás fejlesztése, miután az alkalmazást egy Azure felhőszolgáltatásban közzéteheti használatával a **Azure-alkalmazások közzététele** varázsló.

> [!Note]
> Ez a cikk a cloud Services, websites, a nem helyezhető üzembe. Webhelyek központi telepítésével kapcsolatos információkért lásd: [üzembe helyezése az Azure-webhelyek](https://social.msdn.microsoft.com/Search/windowsazure?query=How%20to%20Deploy%20an%20Azure%20Web%20Site&Refinement=138&ac=4#refinementChanges=117&pageNumber=1&showMore=false).

## <a name="accessing-the-publish-azure-application-wizard"></a>Az Azure-alkalmazás közzététele varázsló elérése

Az Azure-alkalmazás közzététele varázsló rendelkezik Visual Studio-projekt típusától függően kétféleképpen érheti el.

**Ha rendelkezik egy Azure-felhőszolgáltatás-projekt:**

1. Hozzon létre vagy nyisson meg egy Azure-felhőszolgáltatás-projekt a Visual Studióban.

1. A **Megoldáskezelőben**, kattintson a jobb gombbal a projektre, és a helyi menüből válassza ki a **közzététel**.

**Ha rendelkezik egy webalkalmazás-projekt, amely az Azure-ban nincs engedélyezve:**

1. Hozzon létre vagy nyisson meg egy Azure-felhőszolgáltatás-projekt a Visual Studióban.

1. A **Megoldáskezelőben**, kattintson a jobb gombbal a projektre, és a helyi menüből válassza ki a **konvertálása** > **átalakítása az Azure Felhőszolgáltatás-projekt**. 

1. A **Megoldáskezelőben**, kattintson a jobb gombbal az újonnan létrehozott Azure-projektre, és a helyi menüből válassza ki a **közzététel**.

## <a name="sign-in-page"></a>Bejelentkezési oldal

![Bejelentkezési oldal](./media/vs-azure-tools-publish-azure-application-wizard/sign-in.png)

**Fiók** – válassza ki a fiókot, vagy válasszon **vegyen fel egy fiókot** a fiókot a legördülő listában.

**Válassza ki az előfizetését** – válassza ki a az üzembe helyezéshez használni kívánt előfizetést.

## <a name="settings-page---common-settings-tab"></a>Beállítások – közös beállítások lap

![Általános beállítások](./media/vs-azure-tools-publish-azure-application-wizard/settings-common-settings.png)

**Felhőalapú szolgáltatás** – használja a legördülő listából, vagy válasszon egy meglévő felhőalapú szolgáltatás, vagy válassza  **&lt;hozzon létre új >**, és a egy felhőalapú szolgáltatás létrehozása. Az Adatközpont minden felhőszolgáltatás zárójelben jeleníti meg. Javasoljuk, hogy az adatok adatközpont elhelyezkedése határozza meg a felhőszolgáltatás számára adatokat szolgáltató adatközpont elhelyezkedése a tárfiók (Speciális beállítások) azonos legyen.

**Környezet** – ezek közül bármelyikre **éles** vagy **átmeneti**. Az átmeneti környezetben akkor válassza, ha az alkalmazás egy tesztkörnyezetben telepíteni szeretné. 

**Konfigurace buildu** -válassza **Debug** vagy **kiadási**.

**Szolgáltatás konfigurációjának** -válassza **felhőalapú** vagy **helyi**.

**A távoli asztal engedélyezése az összes szerepköre** – válassza ezt a lehetőséget, ha szeretné tudni távolról csatlakozni a szolgáltatáshoz. Ezt a beállítást elsősorban hibakeresési. További információkért lásd: [távoli asztali kapcsolat engedélyezése egy szerepkörhöz az Azure Cloud Services, a Visual Studio használatával](cloud-services/cloud-services-role-enable-remote-desktop-visual-studio.md).

**Engedélyezze a Web Deploy minden webes szerepkörök** – ezt a beállítást a webes telepítése a szolgáltatás engedélyezéséhez. Is be kell jelölnie a **távoli asztal engedélyezése az összes szerepkörhöz** beállítást a funkció használatához. További információkért lásd: [közzététele a felhőalapú szolgáltatás, a Visual Studio használatával](vs-azure-tools-publishing-a-cloud-service.md).

## <a name="settings-page---advanced-settings-tab"></a>Beállítások lap – Speciális beállítások lap

![Speciális beállítások](./media/vs-azure-tools-publish-azure-application-wizard/settings-advanced-settings.png)

**Üzemelő példány címkéje** -vagy fogadja el az alapértelmezett nevet, vagy adjon meg egy tetszőleges nevet. A dátum az üzemelő példány címkéje fűzi, hagyja a jelölőnégyzet be van jelölve. 

**Storage-fiók** – válassza ki a tárfiókot az üzembe helyezéshez használandó **&lt;új létrehozása > storage-fiók létrehozásához. Az Adatközpont megjeleníti minden olyan tárfiókhoz zárójelben. Javasoljuk, hogy a tárfiók adatokat szolgáltató adatközpont elhelyezkedése megegyezik a felhőszolgáltatás (általános beállítások) számára adatokat szolgáltató adatközpont elhelyezkedése.

Az Azure storage-fiók a központi telepítési csomag tárolja. Az alkalmazás telepítése után a csomag törlődik a tárfiókból.

**Hiba esetén a telepítés törlése** – válassza ezt a beállítást, hogy az üzembe helyezési törlése, ha a közzététel során fellépő hibák. Ez nincs bejelölve legyen, ha meg szeretné tartani a felhőszolgáltatásokhoz tartozó állandó virtuális IP-címet.

**Az üzemelő példányok frissítése** – válassza ezt a lehetőséget, ha csak a frissített összetevőket telepíteni kívánja. A központi telepítési típus lehet gyorsabb, mint a teljes körű telepítésére. Ezt érdemes lehet ellenőrizni, ha meg szeretné tartani a felhőszolgáltatásokhoz tartozó állandó virtuális IP-címet. 

**Az üzemelő példányok frissítése - beállítások** – Ez a párbeszédpanel segítségével további adja meg, hogyan frissíthető a szerepköröket. Ha úgy dönt, **növekményes frissítés**, az alkalmazás minden példánya akkor frissül egymás után, így az alkalmazás mindig elérhető. Ha úgy dönt, **egyidejű frissítése**, az alkalmazás összes példányát egyszerre frissülnek. Egyidejű frissítése gyorsabb, de a szolgáltatás nem feltétlenül érhető el a frissítési folyamat alatt.

![Központi telepítési beállítások](./media/vs-azure-tools-publish-azure-application-wizard/deployment-settings.png)

**IntelliTrace engedélyezése** – adja meg, hogy szeretné-e az IntelliTrace engedélyezése. IntelliTrace, a széles körű hibakeresési információkat egy szerepkörpéldány számára az Azure-ban való futáskor jelentkezhet meg. Ha a probléma okának feltárásához van szüksége, az IntelliTrace-naplók segítségével végighaladhat a kód a Visual Studióból, ha azt az Azure-ban futó. IntelliTrace használatával kapcsolatos további információkért lásd: [a Visual Studio és az IntelliTrace közzétett Azure felhőszolgáltatás hibakeresést](./vs-azure-tools-intellitrace-debug-published-cloud-services.md).

**Engedélyezze a profilkészítés** – adja meg, ha szeretné engedélyezni, teljesítmény-adatainak összegyűjtése. A Visual Studio profilerével lehetővé teszi, hogy hogyan a felhőalapú szolgáltatás fut-e a számítási aspektusait részletes elemzését beolvasása. A Visual Studio profilerével használatával kapcsolatos további információkért lásd: [egy Azure-felhőszolgáltatás teljesítményének tesztelése](./vs-azure-tools-performance-profiling-cloud-services.md).

**Engedélyezze a távoli hibakeresőt összes szerepköre** – adja meg, hogy szeretné-e távoli hibakeresés engedélyezése. Hibakeresés a Visual Studio használatával a cloud services további információkért lásd: [egy Azure-felhőszolgáltatás vagy a virtuális gépen a Visual Studio hibakereső](./vs-azure-tools-debug-cloud-services-virtual-machines.md).

## <a name="diagnostics-settings-page"></a>Diagnosztikai beállítások lap

![Diagnosztikai beállítások](./media/vs-azure-tools-publish-azure-application-wizard/diagnostic-settings.png)

Diagnosztikai szolgáltatása lehetővé teszi, hogy egy Azure-felhőszolgáltatásban (vagy Azure virtuális gépen). Diagnosztikai információ: [diagnosztika beállítása az Azure Cloud Services és Virtual Machines](./vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md). Az Application Insights kapcsolatos információkért lásd: [Mi az Application Insights?](./application-insights/app-insights-overview.md).

## <a name="summary-page"></a>Összefoglaló lap

![Összegzés](./media/vs-azure-tools-publish-azure-application-wizard/summary.png)

**Célprofilt** – is szeretne létrehozni egy közzétételi profilt, amely a kiválasztott beállítások alapján. Létrehozhat például egy tesztkörnyezetben egy profilt és egy másikat az éles környezetben. Ez a profil mentéséhez válassza a **mentése** ikonra. A varázsló létrejön a profil, és menti azt a Visual Studio-projekt. Módosításához a profil nevét, nyissa meg a **célprofilt** listában, és válassza a  **&lt;kezelése... &gt;**.

   > [!Note]
   > Közzétételi profil megjelenik a Visual Studio megoldáskezelőjében, és a profilbeállítások írt .azurePubxml kiterjesztésű fájlba. XML-címkék attribútumai menti a beállításokat.

## <a name="publishing-your-application"></a>Az alkalmazás közzététele

Miután a projekt központi telepítés összes beállításokat konfigurál, válassza ki **közzététel** a párbeszédpanel alján. Nyomon követheti a folyamat állapotát a **kimeneti** ablak a Visual Studióban.

## <a name="next-steps"></a>További lépések

- [Telepítse át, és a Visual Studióból az Azure felhőszolgáltatás webalkalmazás közzététele](./vs-azure-tools-migrate-publish-web-app-to-cloud-service.md)

- [Ismerje meg, hogyan közzététele az Azure-felhőszolgáltatás a Visual Studio használatával](./vs-azure-tools-publishing-a-cloud-service.md)

- [A Visual Studio és az IntelliTrace közzétett Azure felhőszolgáltatás hibakeresést](./vs-azure-tools-intellitrace-debug-published-cloud-services.md)

- [Egy Azure-felhőszolgáltatás teljesítményének tesztelése](./vs-azure-tools-performance-profiling-cloud-services.md)

- [Konfiguruje se Diagnostika Pro az Azure Cloud Services és Virtual Machines](./vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md).

- [Mi az Application Insights?](./application-insights/app-insights-overview.md)