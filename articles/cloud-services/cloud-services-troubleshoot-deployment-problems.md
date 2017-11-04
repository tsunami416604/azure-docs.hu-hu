---
title: "Felhőalapú szolgáltatás telepítésével kapcsolatos problémák elhárítása |} Microsoft Docs"
description: "Előfordulhat, hogy futtatja az Azure felhőalapú szolgáltatás telepítése néhány gyakori problémák vannak. Ez a cikk némelyikük megoldásokat tartalmazza."
services: cloud-services
documentationcenter: 
author: simonxjx
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: a18ae415-0d1c-4bc4-ab6c-c1ddea02c870
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/03/2017
ms.author: v-six
ms.openlocfilehash: 944a29aebf7abfe32a7789ab239718b1cd2d7b15
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-cloud-service-deployment-problems"></a>Felhőalapú szolgáltatás telepítésével kapcsolatos problémák elhárítása
Amikor telepít egy felhőalapú szolgáltatás alkalmazáscsomagot az Azure-ba, ezt úgy szerezheti be a telepítéssel kapcsolatos információk a **tulajdonságok** ablaktáblán az Azure portálon. Használhatja a részletek panelen segítséget nyújtanak a felhőalapú szolgáltatással kapcsolatos hibák elhárítása, és megadhatja ezt az információt Azure támogatásához új támogatási kérelem megnyitásakor.

Megtalálhatja az **tulajdonságok** ablaktáblán az alábbiak szerint:

* Az Azure portálon kattintson a felhőalapú szolgáltatás telepítését, majd **összes beállítás**, és kattintson a **tulajdonságok**.
* A klasszikus Azure portálon, kattintson a felhőalapú szolgáltatás telepítését, majd **IRÁNYÍTÓPULT**, a lap jobb alsó sarkában található (alatt **gyors áttekintő**). Vegye figyelembe, hogy nincs nincs "Tulajdonságok" címke ezen az ablaktáblán.

> [!NOTE]
> Másolhatja a tartalmát a **tulajdonságok** ablaktáblán a jobb felső sarokban található az ikonra kattintva a vágólapra.
>
>

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Probléma: nem érhető el a webhely, de a telepítés nincs elindítva, készen áll az összes szerepkörpéldányt
A webhely URL-cím hivatkozás jelenik meg a portálon nem tartalmazza a port. Webhelyekhez az alapértelmezett port a 80-as. Ha az alkalmazás futtatásához egy másik portra van konfigurálva, hozzá kell adnia a megfelelő port számát az URL-címet a webhelyhez való hozzáféréskor.

1. Az Azure portálon kattintson a felhőalapú szolgáltatás telepítését.
2. Az a **tulajdonságok** ablaktábláján az Azure portálon ellenőrizze a szerepkör-példányok által használt portokat (alatt **bemeneti végpontok**).
3. Ha a port nincs 80-as, adja hozzá a megfelelő port értékének az URL-cím hozzáférni az alkalmazáshoz. Adjon meg egy nem alapértelmezett portot, írja be az URL-címet, majd egy kettőspontot (:), és a port számát, szóközök nélkül.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Probléma: Az a szerepkörpéldányok nélkül me bármi felhasználását.
Javítás szolgáltatás Azure csomópontok problémát észlel, és ezért szerepkörpéldányokat áthelyezése új csomópontok automatikusan megtörténik. Ha ez történik, a szerepkörpéldányok automatikusan újrahasznosítási jelenhet meg. Ha szeretné tudni, ha szolgáltatásjavításnak történt:

1. Az Azure portálon kattintson a felhőalapú szolgáltatás telepítését.
2. Az a **tulajdonságok** ablaktáblán az Azure portál, tekintse át az adatokat, és határozza meg, hogy szolgáltatásjavításnak történt, akkor a szerepkörök újrahasznosítását megfigyelt ideje alatt.

Szerepkörök is indul újra nagyjából havonta egyszer gazdagép-OS és a vendég-operációs rendszer frissítése során.  
További információkért lásd a következő blogbejegyzésben [szerepkör példány újraindítása miatt az operációs rendszer frissítései](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Probléma: I nem hajtsa végre a virtuális IP-címcsere és olyan hibaüzenetet kap
A virtuális IP-címcsere nem engedélyezett, ha a központi telepítés frissítése folyamatban van. Telepítési frissítések automatikusan történik ha:

* Új vendég operációs rendszer és az automatikus frissítések konfigurált.
* Szolgáltatás javító következik be.

Annak ellenőrzése, hogy az automatikus frissítés megakadályozza ennek a virtuális IP-címcsere során:

1. Az Azure portálon kattintson a felhőalapú szolgáltatás telepítését.
2. Az a **tulajdonságok** ablaktáblán az Azure portál, keresse meg az értéket **állapot**. Ha **készen**, majd ellenőrizze **a legutóbbi művelet** megjelenítéséhez, ha egy nemrég történt, amely megakadályozhatja, hogy a virtuális IP-címcsere.
3. Az éles környezet esetében ismételje meg az 1 – 2.
4. Ha egy automatikus frissítési folyamat során, várjon, amíg az befejeződik a virtuális IP-címcsere végrehajtását megkísérlő előtt.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Probléma: Egy szerepkörpéldányt ismétlési van elindítva, inicializálás, foglalt és leállítva között
Ez az állapot problémára mutathat vissza az alkalmazás kódjában, csomagjában, vagy konfigurációs fájljában. Ebben az esetben érdemes, módosítás, néhány percenként állapotát tekintheti meg és az Azure-portálon vagy hasonlót **Recycling**, **foglalt**, vagy **inicializálás**. Ez azt jelenti, hogy valami nem működik, az alkalmazással, amely a szerepkör példánya megakadályozza a futtatását.

További információ a probléma elhárításáról lásd a következő blogbejegyzésben [Azure PaaS számítási diagnosztikai adatainak](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx) és [gyakori hibák, melyek a szerepkörök újrahasznosítását okozzák](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

## <a name="problem-my-application-stopped-working"></a>Probléma: Az alkalmazás nem működik
1. Az Azure portálon kattintson a szerepkör példánya.
2. Az a **tulajdonságok** ablaktáblán az Azure portál, fontolja meg a probléma megoldásához az alábbi feltételek:
   * Ha a szerepkör példánya nemrég leállt (ellenőrizheti, hogy értékének **megszakítások száma**), a telepítés sikerült frissítése. Várja meg, hogy ha a szerepkör példánya folytatja működik-e a saját.
   * Ha a szerepkör-példány **foglalt**, ellenőrizze, hogy az alkalmazás kódjában a [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck) esemény. Szükség lehet hozzáadni vagy javítani néhány kódot, amely kezeli ezt az eseményt.
   * A diagnosztikai adatok keresztül halad, és hibaelhárítás esetén ajánlott, a blogbejegyzés [Azure PaaS számítási diagnosztikai adatainak](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

> [!WARNING]
> Ha újrahasznosítja a felhőalapú szolgáltatás, alaphelyzetbe állítja a központi telepítés tulajdonságainak hatékonyan törlése az eredeti problémára vonatkozó információkat.
>
>

## <a name="next-steps"></a>Következő lépések
További [cikkek hibaelhárítási](https://docs.microsoft.com/en-us/azure/cloud-services/cloud-services-allocation-failures) felhőszolgáltatásai számára.

Felhőalapú szolgáltatás szerepkör kapcsolatos problémák elhárítása az Azure PaaS diagnosztikai adatainak használatával kapcsolatban lásd: [Kevin Williamson blog adatsorozat](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
