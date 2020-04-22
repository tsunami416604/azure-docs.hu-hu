---
title: Az Azure IoT Central-alkalmazások eszközsablon-verziószámozásának ismertetése | Microsoft dokumentumok
description: Az eszközsablonok on-át itegetése új verziók létrehozásával és az élő csatlakoztatott eszközök befolyásolása nélkül
author: sarahhubbard
ms.author: sahubbar
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 37c7bc99881c8d1106c8464cfe18c9e63b8a1b02
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756725"
---
# <a name="create-a-new-device-template-version"></a>Új eszközsablon-verzió létrehozása

*Ez a cikk a megoldáskészítőkre és az eszközfejlesztőkre vonatkozik.*

Az Azure IoT Central lehetővé teszi az IoT-alkalmazások gyors fejlesztését. Az eszközsablon-tervek gyors analfabetizálhatók az eszközképességek, nézetek és testreszabások hozzáadásával, szerkesztésével vagy törlésével. Miután közzétette az eszközsablont, az eszközképességi modell **közzétettként** jelenik meg a modell mellett a zárolási ikonokkal. Az eszközképességi modell módosításához létre kell hoznia az eszközsablon új verzióját. Eközben a felhő tulajdonságait, testreszabások és nézetek mind szerkeszthetők bármikor anélkül, hogy verzióaz eszköz sablon. Miután mentette a módosítások bármelyikét, közzéteheti az eszközsablont, hogy a legutóbbi módosításokat elérhetővé tegye az üzemeltető számára az Eszközkezelőben való megtekintéshez.

> [!NOTE]
> Ha többet szeretne tudni az eszközsablon létrehozásáról, olvassa el az [Eszközsablon beállítása és kezelése című témakört.](howto-set-up-template.md)

## <a name="add-customizations-to-the-device-template-without-versioning"></a>Testreszabások hozzáadása az eszközsablonhoz verziószámozás nélkül

Az eszköz képességeinek bizonyos elemei az eszközsablon és -felületek verziószámozása nélkül szerkeszthetők. Ezek közé a mezők közé tartozik például a megjelenítendő név, a szemantikai típus, a minimális érték, a maximális érték, a tizedesjegyek, a szín, az egység, a megjelenítési egység, a megjegyzés és a leírás. Az alábbi testreszabások egyikének hozzáadása:

1. Nyissa meg az **Eszközsablonok** lapot.
1. Válassza ki a testre szabni kívánt eszközsablont.
1. Válassza a **Testreszabás** lapot.
1. Az eszközképességi modellben definiált összes képesség itt jelenik meg. Az itt szerkeszthető összes mező menthető és használható az alkalmazásban anélkül, hogy az eszközsablont verzióra kellene használnia. Ha vannak olyan mezők, amelyeket csak olvashatóan szeretne módosítani, a módosításhoz az eszközsablont kell verzióznia. Jelölje ki a szerkesztni kívánt mezőt, és írja be az új értékeket.
1. Kattintson a **Save** (Mentés) gombra. Most ezek az értékek felülírnak mindent, ami eredetileg az eszközsablonba lett mentve, és az alkalmazáson keresztül lesznek használva.

## <a name="versioning-a-device-template"></a>Eszközsablon verziószámozása

Az eszközsablon új verziójának létrehozásával létrejön a sablon vázlatverziója, amelyen az eszközképességi modell szerkeszthető. A közzétett felületek mindaddig közzé maradnak, amíg külön-külön verziójúk nem lesznek. A közzétett felület módosításához először létre kell hoznia egy új eszközsablon-verziót.

Az eszközsablont csak akkor kell verziószámba adni, ha az eszközképességi modell egy olyan részét próbálja szerkeszteni, amelyet nem szerkeszthet az eszközsablon testreszabási szakaszában. 

Az eszközsablon verziószámba adása:

1. Nyissa meg az **Eszközsablonok** lapot.
1. Válassza ki a verziót kipróbálandó eszközsablont.
1. Kattintson a lap tetején található **Verzió** gombra, és adjon új nevet a sablonnak. Javasoltunk egy új nevet, amely szerkeszthető.
1. Kattintson **a Létrehozás gombra.**
1. Most az eszközsablon vázlat módban van. Látni fogja, hogy a felületek még mindig zárolva vannak, és a szerkesztéshez külön-külön kell verziót adni. 

### <a name="versioning-an-interface"></a>Kapcsolat verziószámozása

A kapcsolat verziószámozása lehetővé teszi a már létrehozott felületen belüli képességek hozzáadását, frissítését és eltávolítását. 

Annak érdekében, hogy változat egy felület:

1. Nyissa meg az **Eszközsablonok** lapot.
1. Válassza ki a vázlat módban lévő eszközsablont.
1. Válassza ki a közzétenni kívánt módban lévő és szerkesztésre kívánt felületet.
1. Kattintson a **kapcsolatlap** tetején található Verzió gombra. 
1. Kattintson **a Létrehozás gombra.**
1. Most a felület vázlat módban van. A meglévő testreszabások és nézetek megtörése nélkül adhat hozzá vagy szerkeszthet funkciókat a felületen. 

> [!NOTE]
> Az Azure IoT által közzétett szabványos felületek nem lehet verziót verziózott vagy szerkesztett. Ezek a szabványos illesztők eszközminősítéshez használatosak.

> [!NOTE]
> Miután a felület már megjelent, akkor nem lehet törölni sem, hogy a képességek még egy vázlat módban. A képességek csak vázlat módban szerkeszthetők vagy adhatók hozzá a kapcsolathoz.


## <a name="migrate-a-device-across-device-template-versions"></a>Eszköz áttelepítése az eszközsablon-verziók között

Az eszközsablonnak több verzióját is létrehozhatja. Idővel több csatlakoztatott eszköz esablonok használatával fog rendelkezni. Az eszközök áttelepíthetők az eszközsablon egyik verziójából a másikba. Az alábbi lépések az eszközök áttelepítését ismertetik:

1. Nyissa meg az **Eszközkezelő** lapot.
1. Válassza ki azt az eszközt, amelyet át szeretne telepíteni egy másik verzióra.
1. Válassza **az Áttelepítés**lehetőséget.
1. Jelölje ki azt az eszközsablont, amelynek verziószáma az eszköz áttelepítése, és válassza az **Áttelepítés**lehetőséget.

![Eszköz áttelepítése](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>További lépések

Ha Ön eszközfejlesztő, a javasolt következő lépés az [Azure IoT Edge-eszközök és az Azure IoT Central.](./concepts-iot-edge.md)
