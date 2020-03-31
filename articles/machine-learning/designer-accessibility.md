---
title: A kisegítő lehetőségek használata a tervezőben (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Ismerje meg a tervezőben elérhető billentyűparancsokat és képernyőolvasó-kisegítő lehetőségeket.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: peterlu
author: peterclu
ms.date: 01/09/2020
ms.openlocfilehash: 59199291589a81d0a0d96b7867078b8196be086f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77366194"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer-preview"></a>Billentyűzet használata az Azure Machine Learning designer használatához (előzetes verzió)

Ismerje meg, hogyan használhatja a billentyűzetet és a képernyőolvasót az Azure Machine Learning tervezőjének használatához. Az Azure Portalon mindenütt használható billentyűparancsok listáját az [Azure Portalon található billentyűparancsok című témakörben található.](../azure-portal/azure-portal-keyboard-shortcuts.md)

Ezt a munkafolyamatot a [Narrátor](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator) és a [JAWS](https://www.freedomscientific.com/products/software/jaws/)tesztelte, de más szabványos képernyőolvasókkal is működnie kell.

## <a name="navigate-the-pipeline-graph"></a>Navigálás a folyamatdiagramon

A folyamatdiagram beágyazott listaként van rendezve. A külső lista egy modullista, amely leírja a folyamatdiagram összes modulját. A belső lista egy kapcsolatlista, amely egy adott modul összes kapcsolatát leírja.  

1. A modullistában a nyílbillentyűvel válthat a modulok között.
1. A lapon nyissa meg a célmodul kapcsolatlistáját.
1. A nyílbillentyűvel válthat a modul csatlakozási portjai között.
1. Használja a "G" a célmodul.

## <a name="edit-the-pipeline-graph"></a>A folyamatdiagram szerkesztése

### <a name="add-a-module-to-the-graph"></a>Modul hozzáadása a diagramhoz

1. A Ctrl+F6 billentyűkombinációval váltson a fókuszt a vászonról a modulfára.
1. Keresse meg a kívánt modult a modulfában a szabványos famegtekintési vezérlővel.

### <a name="edit-a-module"></a>Modul szerkesztése

Modul csatlakoztatása másik modulhoz:

1. A modullistájában lévő modul célzásakor használja a Ctrl + Shift + H billentyűkombinációt a kapcsolatsegítő megnyitásához.
1. A modul csatlakozási portjainak szerkesztése.

A modul tulajdonságainak beállítása:

1. A modul tulajdonságainak megnyitásához használja a Ctrl + Shift + E billentyűkombinációt a modul célzásához.
1. A modul tulajdonságainak szerkesztése.

## <a name="navigation-shortcuts"></a>Navigációs billentyűparancsok

| Billentyűleütés | Leírás |
|-|-|
| Ctrl + F6 | Fókusz váltása a vászon és a modulfa között |
| Ctrl + F1   | Az információs kártya megnyitása, amikor egy csomópontra fókuszál a modulfában |
| Ctrl + Shift + H | A kapcsolatsegítő megnyitása, ha a fókusz egy csomóponton van |
| Ctrl + Shift + E | Modultulajdonságok megnyitása, ha a fókusz egy csomóponton van |
| Ctrl + G | A fókusz áthelyezése az első sikertelen csomópontra, ha a folyamat futtatása sikertelen |

## <a name="action-shortcuts"></a>Műveletbillentyűparancsok

Használja az alábbi billentyűparancsokat a hozzáférési kulccsal. A hozzáférési kulcsokról további https://en.wikipedia.org/wiki/Access_keyinformációt a .

| Billentyűleütés | Műveletek |
|-|-|
| Hozzáférési kulcs + R | Futtassa a következőt: |
| Hozzáférési kulcs + P | Közzététel |
| Hozzáférési kulcs + C | Klónozás |
| Hozzáférési kulcs + D | Üzembe helyezés |
| Hozzáférési kulcs + I | Következtetési folyamat létrehozása/frissítése |
| Hozzáférési kulcs + B | Kötegkövetkeztetési folyamat létrehozása/frissítése |
| Hozzáférési kulcs + K | "Következtetéslétrehozása folyamat létrehozása" legördülő menü |
| Hozzáférési kulcs + U | "A következtetési folyamat frissítése" legördülő menü |
| Hozzáférési kulcs + M | Több(...) legördülő menü megnyitása |

## <a name="next-steps"></a>További lépések

- [Kontrasztos megjelenítés bekapcsolása és téma módosítása](../azure-portal/azure-portal-change-theme-high-contrast.md)
- [Kisegítő lehetőségekkel kapcsolatos eszközök a Microsoftnál](https://www.microsoft.com/accessibility)
