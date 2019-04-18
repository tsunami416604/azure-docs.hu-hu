---
title: Az Azure IoT Central alkalmazások eszköz sablon versioning ismertetése |} A Microsoft Docs
description: Az eszköz sablonok ciklustevékenység, hozzon létre új verziókat, és az élő csatlakoztatott eszközök befolyásolása nélkül
author: sandeeppujar
ms.author: sandeepu
ms.date: 03/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: d4f9617a5c2ba6f6cf8dc261845aa98e33d70a55
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59281777"
---
# <a name="create-a-new-device-template-version"></a>Hozzon létre egy új eszköz sablon verziója

Az Azure IoT Central lehetővé teszi az IoT-alkalmazások gyors fejlesztése. Akkor is gyorsan ciklustevékenység az eszköz sablon tervek hozzáadása, szerkesztése vagy törlése a mérések, beállítások és tulajdonságok szerint. Egyes módosítások a jelenleg csatlakoztatott eszközök zavaró lehet. Az Azure IoT Central azonosítja ezeket kompatibilitástörő változásokat, és lehetővé teszi a biztonságos üzembe ezeket a frissítéseket az eszközökre.

Egy eszköz sablonjának egy verziószámot létrehozásakor. Alapértelmezés szerint a verziószáma 1.0.0. Ha eszköz sablon szerkesztése, és ezt a módosítást ronthatja az élő csatlakoztatott eszközök, Azure IoT Central felszólítja, hogy hozzon létre egy új eszköz sablon verziója.

> [!NOTE]
> Ismerje meg, hogy egy eszköz sablon létrehozásával kapcsolatos további információkért tekintse meg a [eszköz sablon beállítása](howto-set-up-template.md)

## <a name="changes-that-prompt-a-version-change"></a>Módosítások kérése verzió módosítása

Az általános beállítások és az eszköz-sablon tulajdonságainak módosítása kérése verzió módosítása.

> [!NOTE]
> Az eszköz sablon végzett módosítások ne jelenjen meg újra egy új verziójára, ha nincs eszköz létrehozásához, vagy a legtöbb egy eszköz van csatlakoztatva.

Az alábbi lista ismerteti a felhasználói műveleteket, hogy egy új verziója:

* Tulajdonságok (kötelező)
    * Felvételét vagy törlését kötelező tulajdonság
    * A tulajdonság, az eszközök által üzenetek küldéséhez használt mező neve mező nevének módosítása.
*  Tulajdonságok (nem kötelező)
    * Egy nem kötelező tulajdonsággal törlése
    * A tulajdonság, az eszközök által üzenetek küldéséhez használt mező neve mező nevének módosítása.
    * Egy kötelező tulajdonság az egy nem kötelező tulajdonság módosítása
*  Beállítások
    * Hozzáadásával vagy egy beállítás törlése
    * A beállítás, az üzenetek küldése és fogadása az eszközök által használt mező neve mező nevének módosítása.

## <a name="what-happens-on-version-change"></a>Mi történik, a verzió módosításakor?

Mi történik a szabályok és az eszköz irányítópultok, a verzió módosítása esetén?

**Szabályok** tartalmazhatnak a feltételeket, amelyek tulajdonságok függ. Ha eltávolított egy vagy több ezeket a tulajdonságokat, ezek a szabályok működésképtelenné tehet az új eszköz sablon verziója. Nyissa meg ezeket a szabályokat, és frissítse a feltételeket, javítsa ki a szabályokat. Az előző verzió szabályainak befolyásolása nélkül működnek.

**Eszköz irányítópultok** számos különböző típusú csempék is tartalmazhat. A csempék némelyike tartalmazhat beállításait és tulajdonságait. Vlastnost nebo egy csempéhez használt beállítás törlődik, ha a csempe nem teljesen vagy részben működik. Nyissa meg a csempét, és hárítsa el a problémát, a csempe eltávolításával, vagy frissíti a csempe tartalmát.

## <a name="migrate-a-device-across-device-template-versions"></a>Eszközök migrálása eszköz verziói között

Az eszköz sablon több verzióját is létrehozhat. Az idő múlásával kell ezen eszköz-sablonokkal több csatlakoztatott eszközön. Az eszköz sablon egyik verziójának eszközök a másikba telepíthet át. Az alábbi lépések bemutatják, hogyan telepíthet át egy eszköz:

1. Nyissa meg a **Device Explorer** lapot.
1. Válassza ki az eszközt kell migrálnia, egy másik verziója.
1. Válasszon **eszköz áttelepítése**.
1. Válassza ki a verziószámot, telepítse át az eszköz számára, és válassza ki a kívánt **áttelepítése**.

![Eszközök migrálása](media/howto-version-devicetemplate/pick-version.png)

## <a name="next-steps"></a>További lépések

Most, hogy, hogyan sablonverzióktól eszköz használata az Azure IoT Central alkalmazáshoz, Íme a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Telemetria szabályok létrehozása](howto-create-telemetry-rules.md)