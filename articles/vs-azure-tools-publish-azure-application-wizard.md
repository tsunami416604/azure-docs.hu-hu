---
title: "A Visual Studio használatával Azure varázsló közzététele |} Microsoft Docs"
description: "Megtudhatja, hogyan lehet megadni a különböző beállításokat a Visual Studio Azure alkalmazás közzététele varázsló"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 7d8f1ac9-e439-47e0-a183-0642c4ea1920
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2017
ms.author: kraigb
ms.openlocfilehash: d29cae9710cc9c01dae09e8fd8273ac30dd61827
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2018
---
# <a name="using-the-visual-studio-publish-azure-application-wizard"></a>A Visual Studio használatával Azure varázsló közzététele

Után a Visual Studio webalkalmazás fejlesztése, az alkalmazás Azure-felhőszolgáltatás segítségével közzéteheti a **Azure-alkalmazás közzététele** varázsló.

> [!Note]
> Ez a cikk a felhőszolgáltatásokhoz, nem a webhelyek központi telepítésével kapcsolatos van. Webhelyek központi telepítésével kapcsolatos információkért lásd: [központi telepítése egy Azure-webhely](https://social.msdn.microsoft.com/Search/windowsazure?query=How%20to%20Deploy%20an%20Azure%20Web%20Site&Refinement=138&ac=4#refinementChanges=117&pageNumber=1&showMore=false).

## <a name="accessing-the-publish-azure-application-wizard"></a>Az Azure-alkalmazás közzététele varázsló használata

Az Azure-alkalmazás közzététele varázsló rendelkezik Visual Studio-projekt típusától függően két módon érheti el.

**Ha rendelkezik egy Azure-felhőszolgáltatás-projekt:**

1. Hozzon létre, vagy a Visual Studio Azure cloud service projekt megnyitása.

1. A **Megoldáskezelőben**, kattintson jobb gombbal a projektre, és válassza ki a helyi menüből **közzététel**.

**Ha a webalkalmazás projekt, amely nincs engedélyezve az Azure-bA rendelkezik:**

1. Hozzon létre, vagy a Visual Studio Azure cloud service projekt megnyitása.

1. A **Megoldáskezelőben**, kattintson jobb gombbal a projektre, és válassza ki a helyi menüből **átalakítása** > **Azure Cloud Service-projekt átalakítása**. 

1. A **Megoldáskezelőben**, kattintson a jobb gombbal az újonnan létrehozott Azure-projekt, és válassza ki a helyi menüből **közzététel**.

## <a name="sign-in-page"></a>Bejelentkezési oldal

![Bejelentkezési oldal](./media/vs-azure-tools-publish-azure-application-wizard/sign-in.png)

**Fiók** – válassza ki a fiókot, vagy válasszon **vegyen fel egy fiókot** a fiók legördülő listából.

**Válassza ki az előfizetés** -válassza ki az előfizetést, hogy a telepítéshez használni.

## <a name="settings-page---common-settings-tab"></a>Beállítások – Általános beállítások lap

![Általános beállítások](./media/vs-azure-tools-publish-azure-application-wizard/settings-common-settings.png)

**A felhőalapú szolgáltatás** – a legördülő menüből, vagy válassza ki azt egy meglévő felhőalapú szolgáltatás, vagy válassza  **&lt;hozzon létre új >**, és a felhőalapú szolgáltatás létrehozása. Az Adatközpont minden felhőszolgáltatás zárójelben jeleníti meg. Javasoljuk, hogy az Adatközpont-helyre a felhőalapú szolgáltatáshoz azonos legyen a tárfiók (Speciális beállítások) data center helyét.

**Környezet** -válassza **éles** vagy **átmeneti**. Válassza ki az átmeneti, ha azt szeretné, amelyet az alkalmazás egy tesztkörnyezetben üzembe. 

**Konfiguráció** -válassza **Debug** vagy **kiadás**.

**Szolgáltatáskonfiguráció** -válassza **felhő** vagy **helyi**.

**A távoli asztal engedélyezése az összes szerepkör** – válassza ezt a lehetőséget, ha szeretné tudni távolról csatlakozni a szolgáltatáshoz. Ezt a beállítást elsősorban a hibaelhárításhoz. További információkért lásd: [engedélyezése a távoli asztali kapcsolat egy szerepkör esetén az Azure Felhőszolgáltatások Visual Studio használatával](cloud-services/cloud-services-role-enable-remote-desktop-visual-studio.md).

**Lehetővé teszi a Web Deploy minden webes szerepkörök** -ezt a beállítást ahhoz, hogy a szolgáltatás webes központi telepítését. Be kell jelölnie a **távoli asztal engedélyezése az összes szerepkör** lehetőséget a szolgáltatás használatához. További információkért lásd: [közzététele a felhőalapú szolgáltatás, a Visual Studio használatával](vs-azure-tools-publishing-a-cloud-service.md).

## <a name="settings-page---advanced-settings-tab"></a>Beállítások lap - Speciális beállítások lap

![Speciális beállítások](./media/vs-azure-tools-publish-azure-application-wizard/settings-advanced-settings.png)

**Üzemelő példány címkéje** -vagy elfogadhatja az alapértelmezett nevet, vagy adjon meg egy nevet a. A dátum az üzemelő példány címkéje összefűzéséhez hagyja bejelölve a jelölőnégyzetet. 

**A tárfiók** -válassza ki a tárfiókot, a telepítéshez használandó **&lt;hozzon létre új > tárfiók létrehozásához. Az Adatközpont minden tárfiók zárójelben jeleníti meg. Javasoljuk, hogy a data center a tárfiók helye megegyezik az center hely a felhőszolgáltatás (általános beállítások).

Az Azure-tárfiók tárolja a központi telepítési csomag. Az alkalmazás telepítése után a csomag törlődik a tárfiók.

**Törölje a központi telepítési hiba esetén** – ezzel a beállítással kell rendelkeznie a központi telepítés törölve, ha bármilyen hiba közzététele során történik. Ez nem kell bejelölni, ha meg szeretné tartani a felhőalapú szolgáltatás állandó virtuális IP-címet.

**Az üzemelő példányok frissítése** – akkor válassza ezt a beállítást, ha szeretné központilag telepíteni, csak a frissített összetevőket. Lehet, hogy a központi telepítési típus gyorsabb, mint a teljes körű telepítésére. Ez ellenőrzésének, ha meg szeretné tartani a felhőalapú szolgáltatás állandó virtuális IP-címet. 

**Az üzemelő példányok módosítása - beállítások** -ezen a párbeszédpanelen további határozza meg, hogyan kell frissíteni a szerepkörök használatával. Ha úgy dönt, **növekményes frissítés**, frissül, az alkalmazás minden példánya egymás után, így az alkalmazás mindig elérhető. Ha úgy dönt, **egyidejű frissítése**, az alkalmazás összes példányát egyszerre frissülnek. Egyidejű frissítése gyorsabb, de a szolgáltatás nem feltétlenül érhető el a frissítési folyamat alatt.

![Központi telepítési beállítások](./media/vs-azure-tools-publish-azure-application-wizard/deployment-settings.png)

**IntelliTrace engedélyezése** -adja meg, ha később engedélyezni kívánja az IntelliTrace. Intellitrace bejelentkezhet a szerepkör példánya nagy mennyiségű hibakeresési adatok az Azure-ban futtatott. Ha a probléma okát van szüksége, az IntelliTrace-naplók segítségével végighaladhat a kódot a Visual Studio eszközből, mintha csak az Azure-ban futna. IntelliTrace használatával kapcsolatos további információkért lásd: [egy közzétett Azure felhőalapú szolgáltatás, a Visual Studio és az IntelliTrace-hibakeresés](./vs-azure-tools-intellitrace-debug-published-cloud-services.md).

**Adatgyűjtés engedélyezése** -adja meg, ha a teljesítmény adatgyűjtés engedélyezése. A Visual Studio Profilkészítő beolvasni a számítási szempontjait a felhőalapú szolgáltatás működésével részletesebb elemzését teszi lehetővé. A Visual Studio profiler használatával kapcsolatos további információkért lásd: [Azure-felhőszolgáltatás teljesítményének a tesztelésére](./vs-azure-tools-performance-profiling-cloud-services.md).

**Engedélyezze a távoli hibakereső összes szerepkörök** -adja meg, ha távoli hibakeresésének engedélyezése. A Visual Studio használatával felhőszolgáltatások hibakeresés további információkért lásd: [egy Azure-felhőszolgáltatásban vagy a virtuális gép a Visual Studio hibakeresési](./vs-azure-tools-debug-cloud-services-virtual-machines.md).

## <a name="diagnostics-settings-page"></a>Diagnosztikai beállítások lap

![Diagnosztikai beállítások](./media/vs-azure-tools-publish-azure-application-wizard/diagnostic-settings.png)

Diagnosztika lehetővé teszi egy Azure-felhőszolgáltatásban (vagy Azure virtuális gép) hibaelhárítása során. Diagnosztikai információ: [diagnosztika konfigurálása az Azure Cloud Services és a virtuális gépek](./vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md). Application insights szolgáltatással kapcsolatos további információkért lásd: [Mi az Application Insights?](./application-insights/app-insights-overview.md).

## <a name="summary-page"></a>Összefoglaló lap

![Összegzés](./media/vs-azure-tools-publish-azure-application-wizard/summary.png)

**Cél profil** -választhatja a közzétételi profil létrehozása a kiválasztott beállításokkal. Létrehozhat például egy tesztkörnyezetben egy profilt és egy másikat a termelési. Ez a profil mentéséhez válassza a **mentése** ikonra. A varázsló hozza létre a profilt, és menti a Visual Studio-projektet. Módosítsa a profil nevét, nyissa meg a **céloz profil** listában, és válassza  **&lt;kezelése... &gt;**.

   > [!Note]
   > A közzétételi profil jelenik meg a Visual Studio Solution Explorerben, és a profilbeállítások írt .azurePubxml kiterjesztésű fájlba. XML-címkék attribútumai menti a beállításokat.

## <a name="publishing-your-application"></a>Az alkalmazás közzététele

Után a projekt telepítése az összes beállítás konfigurálásához válassza a **közzététel** a párbeszédpanel alján. A folyamat állapota a figyelheti a **kimeneti** ablak a Visual Studióban.

## <a name="next-steps"></a>További lépések

- [Telepítse át, és tegye közzé a Visual Studio egy Azure-felhőszolgáltatásban a webes alkalmazás](./vs-azure-tools-migrate-publish-web-app-to-cloud-service.md)

- [Visual Studio használata közzététele az Azure-felhőszolgáltatás](./vs-azure-tools-publishing-a-cloud-service.md)

- [A közzétett Azure-felhőszolgáltatásban a Visual Studio és az IntelliTrace-hibakeresés](./vs-azure-tools-intellitrace-debug-published-cloud-services.md)

- [Azure-felhőszolgáltatás teljesítményének tesztelése](./vs-azure-tools-performance-profiling-cloud-services.md)

- [Diagnosztika konfigurálása az Azure Felhőszolgáltatások és virtuális gépek](./vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md).

- [Mi az Application Insights?](./application-insights/app-insights-overview.md)