---
title: A felhőszolgáltatások telepítésével kapcsolatos problémák elhárítása | Microsoft dokumentumok
description: Néhány gyakori probléma merülhet fel, amikor egy felhőszolgáltatás azure-ba üzembe helyezésekor. Ez a cikk néhány ra is megoldást kínál.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: a18ae415-0d1c-4bc4-ab6c-c1ddea02c870
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: ccb08f853ae0f941dd5f9c0eca8c77f0f650905a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71122753"
---
# <a name="troubleshoot-cloud-service-deployment-problems"></a>Felhőszolgáltatás-telepítési problémák elhárítása
Amikor üzembe helyez egy felhőszolgáltatási alkalmazáscsomagot az Azure-ba, az Azure Portal **Tulajdonságok** ablaktábláján kaphat információkat a központi telepítésről. Ezen az ablaktáblán található részletek segítségével elháríthatja a felhőszolgáltatással kapcsolatos problémákat, és ezeket az információkat az Azure-támogatás rendelkezésére bocsáthatja egy új támogatási kérelem megnyitásakor.

A **Tulajdonságok** ablaktábla a következőképpen található:

* Az Azure Portalon kattintson a felhőszolgáltatás üzembe helyezésére, kattintson a **Minden beállítás**elemre, majd a **Tulajdonságok**parancsra.

> [!NOTE]
> A **Tulajdonságok** ablaktábla tartalmát a vágólapra másolhatja az ablaktábla jobb felső sarkában lévő ikonra kattintva.
>
>

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Probléma: Nem tudom elérni a webhelyet, de a telepítés elindult, és minden szerepkörpéldány készen áll
A portálon megjelenő webhely URL-hivatkozása nem tartalmazza a portot. A webhelyek alapértelmezett portja 80. Ha az alkalmazás úgy van beállítva, hogy egy másik porton fusson, a webhely elérésekor hozzá kell adnia a megfelelő portszámot az URL-címhez.

1. Az Azure Portalon kattintson a felhőszolgáltatás üzembe helyezésére.
2. Az Azure Portal **Tulajdonságok** ablaktábláján ellenőrizze a szerepkörpéldányok portjait (a **bemeneti végpontok**területen).
3. Ha a port nem 80, adja hozzá a megfelelő port értéket az URL-címet, amikor az alkalmazás eléréséhez. Nem alapértelmezett port megadásához írja be az URL-címet, majd egy kettőspontot (:), majd a portszámot szóközök nélkül.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Probléma: A szerepkörpéldányak at újrahasznosított nélkülem csinál semmit
A szolgáltatás javítása automatikusan történik, amikor az Azure észleli a problémás csomópontokat, és ezért a szerepkörpéldányokat új csomópontokba helyezi át. Ebben az esetben előfordulhat, hogy a szerepkörpéldányok újrahasznosítása automatikusan megtörténik. Annak kiderítése, hogy a szolgáltatás meggyógyítása megtörtént-e:

1. Az Azure Portalon kattintson a felhőszolgáltatás üzembe helyezésére.
2. Az Azure Portal **Tulajdonságok** ablaktábláján tekintse át az információkat, és állapítsa meg, hogy a szolgáltatás gyógyulása történt-e a szerepkörök újrahasznosításának megfigyelése során.

Szerepkörök is újra nagyjából havonta egyszer során host-OS és a vendég-OS frissítések.  
További információt a [blogbejegyzésben található szerepkörpéldány újraindítása az operációs rendszer frissítése miatt](https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Probléma: Nem tudok vip cserét végezni, és hibaüzenetet kapok
A VIP-csere nem engedélyezett, ha egy központi telepítési frissítés folyamatban van. A központi telepítési frissítések automatikusan előfordulhatnak, ha:

* Új vendég operációs rendszer érhető el, és ön automatikus frissítésre van konfigurálva.
* Szolgáltatás gyógyulástörténik.

Annak kiderítése, hogy egy automatikus frissítés megakadályozza-e a VIP-csere letiltását:

1. Az Azure Portalon kattintson a felhőszolgáltatás üzembe helyezésére.
2. Az Azure Portal **Tulajdonságok** ablaktáblájában tekintse meg az **Állapot**értékét. Ha **készen**áll, majd ellenőrizze **az Utolsó művelet,** hogy ha egy közelmúltban történt, amely megakadályozhatja a VIP swap.
3. Ismételje meg az 1.
4. Ha egy automatikus frissítés folyamatban van, várja meg, amíg befejeződik, mielőtt megpróbálna a VIP-csere.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Probléma: Egy szerepkörpéldány hurkolazolás az Indítás, inicializálás, foglaltság és leállított között
Ez az állapot problémára mutathat vissza az alkalmazás kódjában, csomagjában, vagy konfigurációs fájljában. Ebben az esetben látnia kell, hogy az állapot néhány percenként változik, és az Azure Portalon például **újrahasznosítás,** **foglalt**ság vagy **inicializálás jelenik meg.** Ez azt jelzi, hogy valami baj van az alkalmazással, amely megakadályozza a szerepkörpéldány futását.

A probléma elhárításáról az [Azure PaaS számítási diagnosztikai adatok](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx) és a [szerepkörök újrahasznosítását okozó gyakori problémák](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md)című blogbejegyzésben talál további információt.

## <a name="problem-my-application-stopped-working"></a>Probléma: Leállt az alkalmazásom
1. Az Azure Portalon kattintson a szerepkörpéldány.
2. Az Azure Portal **Tulajdonságok** ablaktáblájában vegye figyelembe a probléma megoldásához az alábbi feltételeket:
   * Ha a szerepkörpéldány nemrég leállt (ellenőrizheti a **megszakítások száma**értékét), a központi telepítés frissíthető lehet. Várja meg, hogy a szerepkörpéldány magától is működjön-e.
   * Ha a szerepkörpéldány **foglalt,** ellenőrizze az alkalmazás kódját, hogy a [StatusCheck](/previous-versions/azure/reference/ee758135(v=azure.100)) esemény kezelve van-e. Előfordulhat, hogy hozzá kell adnia vagy ki kell javítania néhány kódot, amely kezeli ezt az eseményt.
   * Az Azure PaaS számítási diagnosztikai adatok című blogbejegyzésben a diagnosztikai adatok és a hibaelhárítási [forgatókönyvek végighaladnak.](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)

> [!WARNING]
> Ha újrahasznosítja a felhőszolgáltatást, alaphelyzetbe állítja a telepítés tulajdonságait, és hatékonyan leteszi az eredeti probléma adatait.
>
>

## <a name="next-steps"></a>További lépések
További [hibaelhárítási cikkek](https://docs.microsoft.com/azure/cloud-services/cloud-services-allocation-failures) a felhőszolgáltatásokhoz.

Ha meg szeretné tudni, hogyan háríthatja el a felhőalapú szolgáltatási szerepkörrel kapcsolatos problémákat az Azure PaaS-számítógép diagnosztikai adatainak használatával, olvassa el [Kevin Williamson blogsorozatát.](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)
