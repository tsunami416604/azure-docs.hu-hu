---
title: Az Azure IoT központi alkalmazások eszköz sablon versioning ismertetése |} Microsoft Docs
description: Az eszköz sablonok ismétlés, hozzon létre új verziókat, és az élő csatlakoztatott eszközök befolyásolása nélkül
author: sandeeppujar
ms.author: sandeepu
ms.date: 01/19/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: b125d822596675b138560c14c76f9a3120ce3424
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628827"
---
# <a name="create-a-new-device-template-version"></a>Hozzon létre egy új eszköz sablon verziója

A Microsoft Azure IoT központi lehetővé teszi, hogy gyors fejlesztésére az IoT-alkalmazásokhoz. Akkor is gyors ismétlésének az eszköz sablon tervek szerint hozzáadása, szerkesztése vagy törlése mérések, beállítások vagy tulajdonságok. Lehet, hogy egyes módosítások zavaró beavatkozás a jelenleg csatlakoztatott eszközökhöz. Az Azure IoT központi azonosítja ezeket jelentős változásokat, és lehetővé teszi a biztonságosan a frissítések központi telepítéséhez, az eszközökön.

Egy eszköz sablonjának egy verziószámot létrehozásakor. Alapértelmezés szerint a verziószáma 1.0.0. Ha egy eszköz sablon szerkesztése, és ez a módosítás hatással lehetett csatlakoztatott eszközök live, Azure IoT központi kéri hozzon létre egy új eszköz verziójának.

> [!NOTE]
> Ismerje meg, hogy egy eszköz sablon létrehozásával kapcsolatos információkért tekintse meg a [eszköz sablon beállítása](howto-set-up-template.md)

## <a name="changes-that-prompt-a-version-change"></a>A verzió kérő megváltoztatása

Általános beállítások vagy az eszköz-sablon tulajdonságainak módosítása kérni a verzió módosítása.

> [!NOTE]
> Az eszköz sablon végzett módosítások ne jelenjen meg újra egy új verziójára, ha nincs eszköz létrehozásához, vagy a legtöbb egy eszköz csatlakoztatva van.

Az alábbi lista ismerteti a felhasználói műveleteket, amely egy új verziója igényel:

* Tulajdonságok (kötelező)
    * Felvételét vagy törlését kötelező tulajdonság
    * A tulajdonság, mező neve üzeneteket küldhet az eszközök által használt mező nevének módosítása.
*  A Tulajdonságok (nem kötelező)
    * Egy nem kötelező tulajdonság törlése
    * A tulajdonság, mező neve üzeneteket küldhet az eszközök által használt mező nevének módosítása.
    * Egy szükséges tulajdonság módosítása a tulajdonságot nem kötelező megadni
*  Beállítások
    * Felvételét vagy törlését beállítás
    * A beállítás, az üzenetek küldése és fogadása az eszközök által használt mező neve mező nevének módosítása.

## <a name="what-happens-on-version-change"></a>Mi történik, az verziója?

Mi történik a szabályok és az eszköz irányítópultokat, a verzió módosítása esetén?

**Szabályok** feltételek tulajdonságainak függő tartalmazhatnak. Ha eltávolított egy vagy több ezeket a tulajdonságokat, ezek a szabályok feltörése sikerült csak az új eszköz sablon verziója. Nyissa meg ezeket a szabályokat, és frissíti a feltételeket, javítsa ki a szabályokat. Szabályokat a korábbi verziójához ne legyen hatással együtt kell működnie.

**Eszköz irányítópultok** csempék több típust is tartalmazhatnak. Egyes a csempék beállítások és a tulajdonságok is tartalmazhat. Tulajdonság vagy beállítás szerepel egy csempe eltávolítása után a csempe megszakad teljesen vagy részben. Ugrás a csempén, és hárítsa el a problémát, a csempe eltávolításával, vagy a csempe tartalmának frissítése.

## <a name="migrate-a-device-across-device-template-versions"></a>Egy eszköz áttelepítése eszköz verziói között

Az eszköz sablon több verziója is létrehozhat. Idővel ezek a sablonok eszköz használatával több csatlakoztatott eszközök fog rendelkezni. Áttelepítheti eszközök az eszköz-sablon egyik verzióról a másikra. A következő lépések azt mutatják be, hogyan telepítheti át az eszköz:

1. Lépjen a **Explorer** lap.
1. Válassza ki az eszközt, egy másik verziójára való áttérést kell.
1. Válasszon **eszköz áttelepítése**.
1. Válassza ki a verziószámot, telepítse át az eszköz számára, és válassza a kívánt **áttelepítése**.

![Egy eszköz áttelepítése](media\howto-version-devicetemplate\pick-version.png)

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a sablonverzióktól eszköz használata az Azure IoT központi alkalmazás rendelkezik, ez a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Telemetria szabályok létrehozása](howto-create-telemetry-rules.md)