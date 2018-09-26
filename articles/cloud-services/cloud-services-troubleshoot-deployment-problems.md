---
title: Cloud Services üzembe helyezési hibáinak elhárítása |} A Microsoft Docs
description: Az Azure-bA egy felhőszolgáltatás telepítésének felmerülhet néhány gyakori problémák vannak. Ez a cikk egy részüket megoldásokat biztosít.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: a18ae415-0d1c-4bc4-ab6c-c1ddea02c870
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 7f0e65b1de1df48603cab29148c7f4c6fb909714
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47094997"
---
# <a name="troubleshoot-cloud-service-deployment-problems"></a>Cloud Services üzembe helyezési hibáinak elhárítása
Egy cloud service alkalmazáscsomagot az Azure-ba történő telepítésekor származó információkat is kaphat a **tulajdonságok** panel az Azure Portalon. Segítségével a részletek panelen a felhőalapú szolgáltatással kapcsolatos problémák elhárításához, és megadhatja ezeket az adatokat az Azure-támogatási egy új támogatási kérelmet megnyitásakor.

Annak a **tulajdonságok** panel az alábbiak szerint:

* Az Azure Portalon kattintson a felhőszolgáltatások telepítését, majd **minden beállítás**, és kattintson a **tulajdonságok**.

> [!NOTE]
> Másolhatja a tartalmát a **tulajdonságok** ablaktábla a panel a jobb felső sarokban lévő ikonra kattintva a vágólapra.
>
>

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Probléma: nem érhető el a webhely, de az üzembe helyezés elindul, és készen áll az összes szerepkörpéldány
A webhely URL-Címhivatkozás jelenik meg a portálon nem tartalmazza a portot. Az alapértelmezett port a webhelyek értéke 80. Ha az alkalmazás futtatása egy másik portra van konfigurálva, hozzá kell adnia a megfelelő port számát a URL-CÍMÉT a webhely elérésekor.

1. Az Azure Portalon kattintson a felhőszolgáltatás üzemelő példányának a.
2. Az a **tulajdonságok** panel az Azure Portal, ellenőrizze a portokat a szerepkörpéldányok (alatt **bemeneti végpontok**).
3. Ha a port nem 80-as, adja hozzá a megfelelő port értékének az URL-címet az alkalmazás eléréséhez. Adjon meg egy nem alapértelmezett portot, írja be az URL-címet, majd kettőspontot (:), és a port számát, szóközök nélküli szövegláncként.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Probléma: A szerepkörpéldányok újrahasznosított me csinál semmit nélkül
A szolgáltatás javítása Azure felismeri a probléma csomópontokat, és ezért szerepkörpéldányok átkerül az új csomópontok automatikusan megtörténik. Ha ez történik, a szerepkörpéldányok újrahasznosítási automatikusan jelenhet meg. Ismerje meg, ha felültelepítés történt: a

1. Az Azure Portalon kattintson a felhőszolgáltatás üzemelő példányának a.
2. Az a **tulajdonságok** panel az Azure Portal, tekintse át az adatokat, és határozza meg, hogy felültelepítés történt az időszakban, a szerepkörök újrahasznosításának megfigyelte.

Szerepkörök fog is újrahasznosítása nagyjából havonta egyszer gazdagép-OS és a Vendég-operációsrendszer-frissítések során.  
További információkért tekintse meg a következő blogbejegyzésben: [szerepkör példány újraindítása miatt az operációs rendszer frissítése](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Probléma: I nem egy virtuális IP-címcsere tegye és a egy hibaüzenet jelenik meg
Egy virtuális IP-címcsere nem engedélyezett, ha a központi telepítés frissítése folyamatban van. Telepítési frissítések automatikusan történik ha:

* Új vendég operációs rendszer érhető el, és konfigurálta az automatikus frissítések.
* Szolgáltatás javítása történik.

Annak megállapítása, hogy az automatikus frissítés nem engedélyezi, hogy egy virtuális IP-címcsere virtuáliskörnyezet:

1. Az Azure Portalon kattintson a felhőszolgáltatás üzemelő példányának a.
2. Az a **tulajdonságok** panel az Azure Portal, tekintse meg az értékét **állapot**. Ha **készen**, majd ellenőrizze **a legutóbbi művelet** megtekintheti, ha egy nemrég történt, amely megakadályozhatja, hogy a virtuális IP-címcsere.
3. Az éles környezet esetében ismételje meg az 1. és 2.
4. Ha az automatikus frissítés van folyamatban, várjon, amíg befejezését, mielőtt a virtuális IP-címcsere végrehajtását.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Probléma: Egy szerepkörpéldány hurkolás van elindítva, inicializálás, foglalt és leállítva között
Ez az állapot problémára mutathat vissza az alkalmazás kódjában, csomagjában, vagy konfigurációs fájljában. Ebben az esetben, megnézheti az állapotukat, módosítás, néhány perces időközönként képesnek kell lennie, és az Azure Portalon lehet, hogy tegyük fel például **újrahasznosítás**, **foglalt**, vagy **inicializálás**. Ez azt jelzi, hogy van valami nem működik az alkalmazás, amely a szerepkörpéldány megakadályozza a futtatását.

A probléma elhárítására vonatkozó további információkért tekintse meg a következő blogbejegyzésben: [Azure PaaS számítási diagnosztikai adatok](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx) és [szerepkör-újrahasznosítást kiváltó gyakori problémák](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

## <a name="problem-my-application-stopped-working"></a>Probléma: Az alkalmazásom működése leállt
1. Az Azure Portalon kattintson a szerepkör-példány.
2. Az a **tulajdonságok** panel az Azure Portal, fontolja meg a probléma megoldása érdekében a következő feltételeknek:
   * Ha a szerepkörpéldány nemrég leállt (értékét ellenőrizheti **megszakítások száma**), az üzembe helyezés sikerült frissülni. Várja meg, hogy ha a szerepkörpéldány folytatja a működik-e a saját.
   * Ha a szerepkör-példány **foglalt**, ellenőrizze az alkalmazás kódjában annak ellenőrzéséhez, hogy a [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck) esemény történik. Előfordulhat, hogy szeretne hozzáadni vagy javítani egy kódrészletet, amely kezeli ezt az eseményt.
   * Haladjon végig a diagnosztikai adatok és az ebben a blogbejegyzésben hibakeresésekhez [Azure PaaS számítási diagnosztikai adatok](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

> [!WARNING]
> A felhőszolgáltatások újrahasznosít, ha alaphelyzetbe állítja a központi telepítés tulajdonságainak hatékonyan kapnak meg a probléma eredeti vonatkozó információkat.
>
>

## <a name="next-steps"></a>További lépések
Továbbiak megtekintése [hibaelhárítási cikkek](https://docs.microsoft.com/azure/cloud-services/cloud-services-allocation-failures) a cloud services.

Cloud service szerepkör kapcsolatos problémák elhárítása Azure PaaS számítógép-diagnosztikai adatok használatával kapcsolatban lásd: [Kevin Williamson blogsorozatot](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
