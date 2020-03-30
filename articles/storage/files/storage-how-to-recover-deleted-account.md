---
title: Törölt tárfiók helyreállítása
description: További információ a törölt tárfiók helyreállításáról
author: todmccoy
manager: dcscontentpm
ms.service: storage
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: rogarana
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: 05465d4a03335ac607ba8981116c66fd6dac9416
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252634"
---
# <a name="how-to-recover-a-deleted-storage-account"></a>Törölt tárfiók helyreállítása

Az Azure Storage automatikus replikákon keresztül biztosítja az adatok rugalmasságát, de nem akadályozza meg, hogy a felhasználók vagy az alkalmazáskód megsérüljön az adatokban, akár véletlenül, akár rosszindulatúan. Az adatok hűségének fenntartása alkalmazás- vagy felhasználói hibapéldányok esetén fejlettebb technikákat igényel, például az adatok másolását egy másodlagos tárolóhelyre egy naplóval.

Az alábbi táblázat áttekintést nyújt a tárfiók-helyreállítás hatóköréről a replikációs stratégiától függően.

| |LRS|ZRS|GRS|RA - GRS|
|---|---|---|---|---|
|Tárfiók Azure Erőforrás-kezelő|Igen|Igen|Igen|Igen|
|Klasszikus tárfiók|Igen|Igen|Igen|Igen|

Gyűjtse össze a következő információkat, és nyújtson be támogatási kérelmet a Microsoft támogatási szolgálatához:

* Storage account name (Tárfiók neve)
* Törlés dátuma
* Tárfiók régiója
* Hogyan törölték a tárfiókot?
* Milyen módszer törölte a tárfiókot? (Portál, PowerShell stb.)

Fontos pontok

* Általában a törlés időpontjától a storage service szemétgyűjtés végrehajtásához, így a tárfiókok helyreállítása nem lehet helyreállítanak egy SLA-val.
* A Microsoft támogatási szolgálata a lehető legjobb anam-es módon próbálja meg helyreállítani a tárolót/fiókot, és nem tudja garantálni a helyreállítást.

> [!NOTE]
> Előfordulhat, hogy a helyreállítás nem lesz sikeres, ha a fiókot újra létrehozták. Ha már újra létrehozta a fiókot, először törölnie kell azt, mielőtt a helyreállítást megkísérelheti.
