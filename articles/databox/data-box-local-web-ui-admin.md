---
title: A Microsoft Azure Data Box felügyelete a helyi webes felhasználói felülettel | Microsoft Docs in data
description: A cikk azt ismerteti, hogyan használhatja a helyi webes felhasználói felületet a Data Box-eszköz felügyeletére.
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: overview
ms.custom: ''
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/11/2018
ms.author: alkohli
ms.openlocfilehash: a5f3f1b4d0d67a12ee0ebaa40935b8494e53dab3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956302"
---
# <a name="use-the-local-web-ui-to-administer-your-data-box"></a>A Data Box felügyelete a helyi webes felhasználói felülettel

A cikk a Data Boxon végrehajtható konfigurációs és felügyeleti feladatokat ismerteti. A Data Box az Azure Portal felhasználói felületével és az eszköz helyi webes felhasználói felületével felügyelhető. A cikk a helyi webes felhasználói felülettel végrehajtható feladatokra összpontosít.

A Data Box helyi webes felhasználói felülete az eszköz kezdeti konfigurálására használható. A helyi webes felhasználói felülettel emellett leállíthatja és újraindíthatja a Data Boxot, diagnosztikai teszteket futtathat, szoftvereket frissíthet, másolási naplókat tekinthet meg, valamint naplócsomagot hozhat létre a Microsoft támogatási szolgálata számára.

A cikk az alábbi oktatóanyagokat tartalmazza:

- Támogatási csomag létrehozása
- Az eszköz leállítása és újraindítása
- Adatok biztonságos törlése az eszközön
- Az eszköz rendelkezésre álló kapacitásának monitorozása
- Ellenőrzőösszeg-érvényesítés kihagyása 

## <a name="generate-support-package"></a>Támogatási csomag létrehozása

Ha eszközproblémákat tapasztal, létrehozhat egy támogatási csomagot a rendszernaplókból. A Microsoft támogatási szolgálata a csomag segítségével hárítja el a problémát. Támogatási csomag létrehozásához hajtsa végre az alábbi lépéseket:

1. A helyi webes felhasználói felületen nyissa meg a **Contact Support** (Kapcsolatfelvétel a támogatási szolgálattal) lapot, és kattintson a **Create Support package** (Támogatási csomag létrehozása) elemre.

    ![Támogatási csomag létrehozása – 1](media/data-box-local-web-ui-admin/create-support-package-1.png)

2. A rendszer összeállítja a támogatási csomagot. A művelet pár percet vesz igénybe.

    ![Támogatási csomag létrehozása – 2](media/data-box-local-web-ui-admin/create-support-package-2.png)

3. A támogatási csomag létrehozásának befejezését követően kattintson a **Download Support package** (Támogatási csomag letöltése) elemre. 

    ![Támogatási csomag létrehozása – 4](media/data-box-local-web-ui-admin/create-support-package-4.png)

4. Tallózással válassza ki a letöltés helyét. Nyissa meg a mappát a tartalmak megtekintéséhez.

    ![Támogatási csomag létrehozása – 5](media/data-box-local-web-ui-admin/create-support-package-5.png)


## <a name="shut-down-or-restart-your-device"></a>Az eszköz leállítása és újraindítása

A Data Box a helyi webes felhasználói felülettel állítható le vagy indítható újra. Az újraindítás előtt ajánlott offline állapotba állítani a gazdagépen található megosztásokat, majd magát az eszközt is. Ezzel a lehető legkisebbre csökkenti az adatsérülés esélyét. Az eszköz leállításakor ellenőrizze, hogy nincs-e folyamatban adatmásolás.

A Data Box leállításához hajtsa végre az alábbi lépéseket.

1. A helyi webes felhasználói felületen lépjen a **Shut down or restart** (Leállítás vagy újraindítás) lapra.
2. Kattintson a **Shut down** (Leállítás) elemre.

    ![A Data Box leállítása – 1](media/data-box-local-web-ui-admin/shut-down-local-web-ui-1.png)

3. Ha a rendszer megerősítést kér, kattintson az **OK** gombra a folytatáshoz.

    ![A Data Box leállítása – 2](media/data-box-local-web-ui-admin/shut-down-local-web-ui-2.png)

Az eszköz leállítását követően az előlapon található bekapcsoló gombbal kapcsolhatja be az eszközt.

A Data Box újraindításához hajtsa végre az alábbi lépéseket.

1. A helyi webes felhasználói felületen lépjen a **Shut down or restart** (Leállítás vagy újraindítás) lapra.
2. Kattintson a **Restart** (Újraindítás) elemre.

    ![A Data Box újraindítása – 1](media/data-box-local-web-ui-admin/restart-local-web-ui-1.png)

3. Ha a rendszer megerősítést kér, kattintson az **OK** gombra a folytatáshoz.

   Az eszköz leáll, majd újraindul.

## <a name="view-available-capacity-of-the-device"></a>Az eszköz rendelkezésre álló kapacitásának megtekintése

Az eszköz irányítópultján tekintheti meg az eszköz rendelkezésre álló és felhasznált kapacitását. 

1. A helyi webes felhasználói felületen lépjen a **View dashboard** (Irányítópult megtekintése) lapra.
2. A **Connect and copy** (Kapcsolódás és másolás) részen az eszköz szabad és felhasznált területe látható.

    ![Rendelkezésre álló kapacitás megtekintése](media/data-box-local-web-ui-admin/verify-used-space-dashboard.png)


## <a name="skip-checksum-validation"></a>Ellenőrzőösszeg-érvényesítés kihagyása

A rendszer alapértelmezés szerint létrehoz egy ellenőrzőösszeget az adatokra vonatkozóan, amikor Ön felkészül az adatok átvitelére. Bizonyos ritka esetekben, az adattípustól függően (kis méretű fájlok) a teljesítmény nagyon lassú lehet. Ilyen esetben kihagyhatja az ellenőrzőösszeg létrehozását. 

Határozottan javasoljuk, hogy csak akkor tiltsa le az ellenőrző létrehozását, ha komoly hatással lenne a teljesítményre.

1. A helyi webes felhasználói felület jobb felső sarkában nyissa meg a Settings (Beállítások) lapot.

    ![Ellenőrzőösszeg letiltása](media/data-box-local-web-ui-admin/disable-checksum.png)

2. Tiltsa le az ellenőrzőösszeg-érvényesítést a **Disable** (Letiltás) paranccsal.
3. Kattintson az **Alkalmaz** gombra.

## <a name="next-steps"></a>További lépések

- Megismerheti, hogyan [kezelheti a Data Boxot az Azure Portalon keresztül](data-box-portal-admin.md).

