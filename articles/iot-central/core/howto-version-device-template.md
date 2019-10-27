---
title: Az Azure IoT Central-alkalmazások eszköz-sablon verziószámozásának ismertetése | Microsoft Docs
description: Új verziók létrehozásával, valamint az élő csatlakoztatott eszközök hatása nélkül megismételheti az eszközök sablonjait
author: sandeeppujar
ms.author: sandeepu
ms.date: 07/08/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 8d4e3b304000113e7ecbf748767780a3fcf17bb3
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952601"
---
# <a name="create-a-new-device-template-version"></a>Új sablon-verzió létrehozása

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Az Azure IoT Central lehetővé teszi a IoT alkalmazások gyors fejlesztését. Gyorsan megismételheti az eszköz sablonjának kialakítását a mérések, beállítások vagy tulajdonságok hozzáadásával, szerkesztésével vagy törlésével. Ezen módosítások némelyike zavaró lehet a jelenleg csatlakoztatott eszközökhöz. Az Azure IoT Central azonosítja ezeket a feltörési változásokat, és lehetővé teszi a frissítések biztonságos központi telepítését az eszközökön.

Az eszköz létrehozásakor a rendszer verziószámot tartalmaz. Alapértelmezés szerint a verziószáma a 1.0.0. Ha szerkeszt egy sablont, és ha ez a változás hatással lehet az élő csatlakoztatott eszközökre, az Azure IoT Central megkéri, hogy hozzon létre egy új sablon-verziót.

> [!NOTE]
> Az eszközök létrehozásával kapcsolatos további tudnivalókért tekintse meg az [eszköz sablonjának beállítása](howto-set-up-template.md) című témakört.

## <a name="changes-that-prompt-a-version-change"></a>A verzió módosítását kérő módosítások

A beállítások vagy az eszköz tulajdonságainak általános módosításakor a rendszer megváltoztatja a verziószámot.

> [!NOTE]
> Az eszköz sablonján végrehajtott módosítások nem kérik új verzió létrehozását, ha egyetlen eszköz sincs csatlakoztatva.

Az alábbi lista azokat a felhasználói műveleteket ismerteti, amelyek új verziót igényelhetnek:

* Tulajdonságok (kötelező)
    * Szükséges tulajdonság hozzáadása vagy törlése
    * Az eszközök által az üzenetek küldéséhez használt mező nevének módosítása.
*  Tulajdonságok (nem kötelező)
    * Nem kötelező tulajdonság törlése
    * Az eszközök által az üzenetek küldéséhez használt mező nevének módosítása.
    * Opcionális tulajdonság módosítása kötelező tulajdonságra
*  Beállítások
    * Beállítás hozzáadása vagy törlése
    * Az eszközök által az üzenetek küldéséhez és fogadásához használt mező nevének módosítása.

## <a name="what-happens-on-version-change"></a>Mi történik a verziók változásakor?

Mi történik a szabályokkal és az eszközök irányítópultokkal, ha módosulnak a verziók?

Az eszköz korábbi verziójának **szabályai** változatlanok maradnak. A szabályok nem települnek át automatikusan az új eszköz sablonjának verziójára. A szokásos módon hozhat létre szabályokat az új sablon verziójához. További információt a [telemetria-szabály létrehozása és az értesítések beállítása az Azure IoT Central alkalmazásban](howto-create-telemetry-rules.md) című cikkben talál.

Az **eszköz-irányítópultok** többféle típusú csempét is tartalmazhatnak. A csempék némelyike tartalmazhatja a beállításokat és a tulajdonságokat. Ha a csempén használt tulajdonság vagy beállítás el van távolítva, a csempe teljesen vagy részben megszakad. Nyissa meg a csempét, és javítsa ki a problémát a csempe eltávolításával vagy a csempe tartalmának frissítésével.

## <a name="migrate-a-device-across-device-template-versions"></a>Eszköz átmigrálása az eszköz sablonjának verziói között

Az eszköz sablonjának több verzióját is létrehozhatja. Idővel több csatlakoztatott eszköz fog rendelkezni ezekkel az eszközökkel. Az eszközöket áttelepítheti az eszköz sablonjának egyik verziójából egy másikba. Az alábbi lépések bemutatják, hogyan telepíthet át egy eszközt:

1. Lépjen a **Device Explorer** lapra.
1. Válassza ki az eszközt, amelyet át kell telepítenie egy másik verzióra.
1. Válassza az **eszköz áttelepíteni**lehetőséget.
1. Válassza ki azt a verziószámot, amelyre át szeretné telepíteni az eszközt, majd válassza az **áttelepítés**lehetőséget.

![Eszköz áttelepítésének módja](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan használhatja az eszköz sablonjának verzióit az Azure IoT Central alkalmazásban, a következő lépés a javasolt lépés:

> [!div class="nextstepaction"]
> [Telemetria-szabályok létrehozása](howto-create-telemetry-rules.md)