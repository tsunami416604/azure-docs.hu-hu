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
ms.openlocfilehash: c718794528989fbc46b404617f16d3a91ade6011
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877309"
---
# <a name="create-a-new-device-template-version"></a>Új sablon-verzió létrehozása

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

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

A **szabályok** olyan feltételeket tartalmazhatnak, amelyek a tulajdonságoktól függenek. Ha eltávolította a tulajdonságok közül egyet vagy többet, ezek a szabályok megbonthatók az új sablon-verzióban. Ezeket a konkrét szabályokat megtekintheti, és frissítheti a feltételeket a szabályok kijavításához. Az előző verzióra vonatkozó szabályoknak hatással kell lenniük a működésre.

Az **eszköz** -irányítópultok többféle típusú csempét is tartalmazhatnak. A csempék némelyike tartalmazhatja a beállításokat és a tulajdonságokat. Ha a csempén használt tulajdonság vagy beállítás el van távolítva, a csempe teljesen vagy részben megszakad. Nyissa meg a csempét, és javítsa ki a problémát a csempe eltávolításával vagy a csempe tartalmának frissítésével.

## <a name="migrate-a-device-across-device-template-versions"></a>Eszköz átmigrálása az eszköz sablonjának verziói között

Az eszköz sablonjának több verzióját is létrehozhatja. Idővel több csatlakoztatott eszköz fog rendelkezni ezekkel az eszközökkel. Az eszközöket áttelepítheti az eszköz sablonjának egyik verziójából egy másikba. Az alábbi lépések bemutatják, hogyan telepíthet át egy eszközt:

1. Lépjen a **Device Explorer** lapra.
1. Válassza ki az eszközt, amelyet át kell telepítenie egy másik verzióra.
1. Válassza az **eszköz áttelepíteni**lehetőséget.
1. Válassza ki azt a verziószámot, amelyre át szeretné telepíteni az eszközt, majd válassza az **áttelepítés**lehetőséget.

![Eszköz áttelepítésének módja](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan használhatja az eszköz sablonjának verzióit az Azure IoT Central alkalmazásban, a következő lépés a javasolt lépés:

> [!div class="nextstepaction"]
> [Telemetria-szabályok létrehozása](howto-create-telemetry-rules.md)