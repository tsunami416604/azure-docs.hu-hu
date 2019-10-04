---
title: Csempék használata az Azure IoT központi alkalmazás irányítópult |} A Microsoft Docs
description: Mint szerkesztő megtudhatja, hogyan használja a csempék az alkalmazás irányítópultokon, az eszköz beállítása az irányítópultok és a eszköz irányítópultok.
author: v-krghan
ms.author: v-krghan
ms.date: 06/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 26322461e4f53bffff2fb182df6c45ed3b83c4bf
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503525"
---
# <a name="how-to-use-tiles"></a>Csempék használata
Testre szabhatja az alkalmazás az irányítópultok, eszköz-irányítópultok, csempék is használhatja, és irányítópultok beállítva. Több csempék hozzáadása egy irányítópulthoz, és testre szabhatja azokat a csempéket, hogy az alkalmazáshoz kapcsolódó adatok megjelenítése. Átméretezheti az egyes csempéket, és minden Irányítópulton az elrendezés testreszabása is. Az alábbi képernyőfelvétel bemutatja az irányítópult-csempe.

![Alkalmazás irányítópultja](media/howto-use-tiles/image1a.png)


A következő táblázat összefoglalja az Azure IoT Central csempék használata:

 
| csempe | Irányítópult | Leírás
| ----------- | ------- | ------- |
| Hivatkozás | Alkalmazás és az eszköz beállítása az irányítópultok |Hivatkozás csempék kattintható csempék, fejléc és leírás megjelenítő. Hivatkozás csempe használatával engedélyezze a felhasználó az egy URL-címet, amely kapcsolódik az alkalmazás. |
| Image | Alkalmazás és az eszköz beállítása az irányítópultok |Képcsempék megjelenítéséhez egyéni rendszerkép és kattintható. Egy képcsempe segítségével grafikus hozzáadása egy irányítópulthoz, és szükség esetén engedélyezi egy felhasználó az egy URL-címet az alkalmazás számára.|
| Címke | Alkalmazások irányítópultok |Címke csempék egyéni szöveg megjelenítése az irányítópulton. Kiválaszthatja, hogy a szöveg méretét. Használatával egy címke csempére vonatkozó információk hozzáadása az irányítópulthoz ilyen leírása, a kapcsolattartási adatait vagy a Súgó|
| Térkép | Alkalmazás és az eszköz beállítása az irányítópultok |Csempék megjelenítése a hely és a egy eszköz állapotát a térképen. Megjelenítheti például, ahol egy eszköz, és e van kapcsolva a ventilátor.|
| Vonaldiagram | Alkalmazások és -irányítópultok |Csempéin eszköz egy adott időszakra vonatkozó összesített mérési diagram megjelenítése. Ha például megjelenítheti egy vonaldiagramot, amely bemutatja az átlagos hőmérséklet és nyomás az eszközön az elmúlt egy óra|
| Sávdiagram | Alkalmazások és -irányítópultok |Sávdiagram csempék eszköz összesített mérések diagram megjelenítése egy adott időszakban. Ha például megjelenítheti a sávdiagramot oszlopdiagramra cseréli, amely az átlagos hőmérséklet és nyomás megjeleníti az eszközön az elmúlt egy óra |
| Eseményelőzmények. | Alkalmazások és -irányítópultok |Esemény előzmények csempe az események egy eszköz egy időszakra jeleníti meg. Például használhatja a hőmérséklet-változás történt az eszköz az elmúlt egy óra alatt megjelenítéséhez. |
| Állapotelőzmények | Alkalmazások és -irányítópultok |Állapotelőzmények csempe a határolt értéket egy adott időszakban jeleníti meg. Például használhatja az eszköz hőmérsékletértékeket megjeleníthető az elmúlt egy óra alatt.|
| KPI | Alkalmazások és -irányítópultok | KPI csempe megjeleníti az összesített telemetria- vagy event mérési egy adott időszakban. Például használhatja az elmúlt egy óra alatt az eszköz elérte a maximális hőmérséklet megjelenítéséhez.|
| Utolsó ismert érték | Alkalmazások és -irányítópultok |Utolsó ismert értékkel a csempe a legújabb értékeket egy telemetria- vagy állam mérték jeleníti meg. Például használhatja ezt a csempét a hőmérséklet, nyomás és páratartalom egy eszköz legutóbbi mérések megjelenítéséhez.|
| Eszköz beállítása rács | Alkalmazás és az eszköz beállítása az irányítópultok | Eszköz beállítása az eszköz beállítása információt rácsban jelenít meg. Eszköz beállítása rács csempe segítségével adatokat, például nevét, helyét és az összes eszköz modell jelennek meg az eszköz beállítása.|


Az Azure IoT központi alkalmazás irányítópult konfigurálásával kapcsolatos további tudnivalókért lásd: [konfigurálása alkalmazás-irányítópult](howto-configure-homepage.md).
