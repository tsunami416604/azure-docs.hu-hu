---
title: Az Azure IoT Central-alkalmazások eszköz-sablon verziószámozásának ismertetése | Microsoft Docs
description: Új verziók létrehozásával, valamint az élő csatlakoztatott eszközök hatása nélkül megismételheti az eszközök sablonjait
author: sarahhubbard
ms.author: sahubbar
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 37c7bc99881c8d1106c8464cfe18c9e63b8a1b02
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81756725"
---
# <a name="create-a-new-device-template-version"></a>Új sablon-verzió létrehozása

*Ez a cikk a megoldás-építők és az eszközök fejlesztőire vonatkozik.*

Az Azure IoT Central lehetővé teszi a IoT alkalmazások gyors fejlesztését. Az eszköz képességeinek, nézeteinek és testreszabásainak hozzáadásával, szerkesztésével vagy törlésével gyorsan megismételheti az eszközök sablonját. Miután közzétette az eszköz sablonját, az eszköz képességeinek modellje a modell melletti zárolási ikonokkal együtt **jelenik** meg. Ha módosítani szeretné az eszköz képességeinek modelljét, létre kell hoznia az eszköz sablonjának új verzióját. Eközben a felhő tulajdonságai, a testreszabások és a nézetek bármikor szerkeszthetők, anélkül, hogy az eszközt kellene használnia. Miután mentette a módosításokat, közzéteheti az eszköz sablonját, hogy a legutóbbi módosítások elérhetők legyenek az operátor számára a Device Explorerban való megjelenítéshez.

> [!NOTE]
> Az eszközök létrehozásával kapcsolatos további tudnivalókért tekintse meg az [eszköz sablonjának beállítása és kezelése](howto-set-up-template.md) című témakört.

## <a name="add-customizations-to-the-device-template-without-versioning"></a>Testreszabások hozzáadása az eszköz sablonhoz verziószámozás nélkül

Az eszköz képességeinek bizonyos elemei szerkeszthetők, anélkül, hogy az eszköz sablonját és felületét kellene megadnia. Ilyen mezők például a megjelenítendő név, a szemantikai típus, a minimális érték, a maximális érték, a tizedesjegyek, a szín, az egység, a megjelenítési egység, a Megjegyzés és a leírás. A következő testreszabások egyikének hozzáadása:

1. Nyissa meg az **eszközök sablonjai** lapot.
1. Válassza ki a testreszabni kívánt sablont.
1. Válassza a **Testreszabás** lapot.
1. Itt jelennek meg az eszköz képességeinek modelljében meghatározott összes képesség. Az itt szerkeszthető mezők menthetők és felhasználhatók az alkalmazásban, anélkül, hogy az eszköz sablonját kellene használnia. Ha vannak olyan mezők, amelyek csak olvashatók, akkor módosítania kell az eszköz sablonját, hogy megváltoztassa ezeket. Válasszon ki egy szerkeszteni kívánt mezőt, és írjon be minden új értéket.
1. Kattintson a **Save** (Mentés) gombra. Mostantól ezek az értékek felülbírálják az eszköz sablonjában eredetileg mentett bármit, és az alkalmazáson keresztül lesznek használva.

## <a name="versioning-a-device-template"></a>Egy eszköz sablonjának verziószámozása

Az eszköz sablonjának új verziójának létrehozásakor létrejön a sablon piszkozata, amelyben az eszköz képességi modellje szerkeszthető lehet. A közzétett felületek minden esetben közzé lesznek téve, amíg azok nem külön verzióban vannak. A közzétett illesztőfelületek módosításához először létre kell hoznia egy új sablon-verziót.

Az eszköz sablonjának csak akkor kell szerepelnie, ha az eszköz képességeinek modelljét szeretné szerkeszteni, de nem szerkesztheti a testreszabások szakaszban az eszköz sablonjában. 

Eszköz sablonjának verziója:

1. Nyissa meg az **eszközök sablonjai** lapot.
1. Válassza ki azt az eszközt, amelyre a verziót kívánja.
1. Kattintson a lap tetején található **Version (verzió** ) gombra, és adjon meg egy új nevet a sablonnak. Egy új nevet javasoltunk, amely szerkeszthető.
1. Kattintson a **Létrehozás**gombra.
1. Most az eszköz sablonja vázlat módban van. Látni fogja, hogy a felületek továbbra is zárolva vannak, és a szerkesztéshez egyenként kell verziót használnia. 

### <a name="versioning-an-interface"></a>Felület verziószámozása

Az interfészek verziószámozása lehetővé teszi a már létrehozott felületen belüli képességek hozzáadását, frissítését és eltávolítását. 

Egy felület verziójának megrendeléséhez:

1. Nyissa meg az **eszközök sablonjai** lapot.
1. Válassza ki a Piszkozat módban lévő sablont.
1. Válassza ki azt a felületet, amelyet közzé szeretne tenni, és szerkeszteni kívánja a közzétett módban.
1. Kattintson a **verzió** gombra a csatoló oldal tetején. 
1. Kattintson a **Létrehozás**gombra.
1. Most az illesztőfelület vázlat módban van. A meglévő testreszabások és nézetek megszakítása nélkül lehetősége lesz felvenni vagy szerkeszteni a felület képességeit. 

> [!NOTE]
> Az Azure IoT által közzétett standard felületek nem telepíthetők és nem szerkeszthetők. Ezek a standard felületek az eszközök tanúsítására szolgálnak.

> [!NOTE]
> Miután közzétette az illesztőfelületet, nem törölheti a képességeit sem Piszkozat módban. A képességeket csak vázlat módban lehet szerkeszteni vagy felvenni az illesztőfelületbe.


## <a name="migrate-a-device-across-device-template-versions"></a>Eszköz átmigrálása az eszköz sablonjának verziói között

Az eszköz sablonjának több verzióját is létrehozhatja. Idővel több csatlakoztatott eszköz fog rendelkezni ezekkel az eszközökkel. Az eszközöket áttelepítheti az eszköz sablonjának egyik verziójából egy másikba. Az alábbi lépések bemutatják, hogyan telepíthet át egy eszközt:

1. Lépjen a **Device Explorer** lapra.
1. Válassza ki az eszközt, amelyet át kell telepítenie egy másik verzióra.
1. Válassza az **áttelepítés**lehetőséget.
1. Válassza ki azt a verziószámot, amelyen át szeretné telepíteni az eszközt, majd válassza az **áttelepítés**lehetőséget.

![Eszköz áttelepítésének módja](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>További lépések

Ha Ön egy eszköz fejlesztője, a következő lépés az [Azure IoT Edge-eszközök és az Azure-IoT Central](./concepts-iot-edge.md)beolvasása.
