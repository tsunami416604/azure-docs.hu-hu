---
title: Kisegítő lehetőségek használata a Designerben (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Ismerkedjen meg a Designerben elérhető billentyűparancsokkal és képernyőolvasó kisegítő lehetőségekkel.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: peterlu
author: peterclu
ms.date: 01/09/2020
ms.custom: designer
ms.openlocfilehash: af676f128796c762b330d986836ea825a36cb246
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83646293"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer-preview"></a>Billentyűzet használata Azure Machine Learning Designer (előzetes verzió) használatához

Ismerje meg, hogyan használható a billentyűzet és a képernyőolvasó a Azure Machine Learning Designer használatához. A Azure Portalban mindenhol működő billentyűparancsok listáját a [Azure Portal billentyűparancsok](../azure-portal/azure-portal-keyboard-shortcuts.md) című témakörben találhatja meg.

Ezt a munkafolyamatot a [Narrátorral](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator) és az [állkapcsokkal](https://www.freedomscientific.com/products/software/jaws/)tesztelték, de más szabványos képernyőolvasókkal is működnie kell.

## <a name="navigate-the-pipeline-graph"></a>A folyamat gráfjának navigálása

A folyamat gráfja beágyazott listaként van rendszerezve. A külső lista egy modul-lista, amely a folyamat gráfban található összes modult ismerteti. A belső lista egy kapcsolati lista, amely egy adott modul összes kapcsolatát ismerteti.  

1. A modulok listájában a nyíl billentyűt használva válthat ki modulokat.
1. A lap használatával nyissa meg a cél modulhoz tartozó kapcsolatok listáját.
1. Használja a nyíl billentyűt a modulhoz tartozó kapcsolati portok közötti váltáshoz.
1. A "G" használatával lépjen a cél modulra.

## <a name="edit-the-pipeline-graph"></a>A folyamat gráfjának szerkesztése

### <a name="add-a-module-to-the-graph"></a>Modul hozzáadása a gráfhoz

1. A CTRL + F6 billentyűkombinációval válthat fókuszt a vászonról a modul fájára.
1. Keresse meg a kívánt modult a modul fájában a standard TreeView vezérlő használatával.

### <a name="edit-a-module"></a>Modul szerkesztése

Modul összekötése egy másik modulhoz:

1. A kapcsolódási segítő megnyitásához használja a CTRL + SHIFT + H billentyűkombinációt a modul listájának megcélzásához.
1. Szerkessze a modulhoz tartozó kapcsolatok portjait.

A modul tulajdonságainak módosítása:

1. Modul tulajdonságainak megnyitásához használja a CTRL + SHIFT + E billentyűkombinációt.
1. A modul tulajdonságainak szerkesztése.

## <a name="navigation-shortcuts"></a>Navigációs hivatkozások

| Billentyűleütés | Description |
|-|-|
| Ctrl + F6 | A vászon és a modul faszerkezete közötti fókusz váltása |
| CTRL + F1   | Az információs kártya megnyitása a modul fáján lévő csomópontra fókuszálva |
| CTRL + SHIFT + H | A kapcsolódási segítő megnyitása, ha a fókusz egy csomóponton van |
| CTRL + SHIFT + E | Modul tulajdonságainak megnyitása, ha a fókusz egy csomóponton van |
| CTRL + G | Fókusz áthelyezése az első sikertelen csomópontra, ha a folyamat futtatása nem sikerült |

## <a name="action-shortcuts"></a>Művelet parancsikonjai

Használja az alábbi parancsikonokat a hozzáférési kulccsal. A hozzáférési kulcsokkal kapcsolatos további információkért lásd: https://en.wikipedia.org/wiki/Access_key .

| Billentyűleütés | Művelet |
|-|-|
| Elérési kulcs + R | Futtatás |
| Elérési kulcs + P | Közzététel |
| Elérési kulcs + C | Klónozás |
| Elérési kulcs + D | Üzembe helyezés |
| Elérési kulcs + I | Következtetési folyamat létrehozása/frissítése |
| Elérési kulcs + B | Batch-következtetési folyamat létrehozása/frissítése |
| Hozzáférési kulcs + K | A "következtetési folyamat létrehozása" legördülő menü megnyitása |
| Elérési kulcs + U | "A következtetési folyamat frissítése" legördülő menü megnyitása |
| Elérési kulcs + M | Továbbiak (...) legördülő lista megnyitása |

## <a name="next-steps"></a>További lépések

- [Kontrasztos megjelenítés bekapcsolása és téma módosítása](../azure-portal/azure-portal-change-theme-high-contrast.md)
- [Hozzáférhetőséggel kapcsolatos eszközök a Microsoftnál](https://www.microsoft.com/accessibility)
