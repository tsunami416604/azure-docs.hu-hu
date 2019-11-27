---
title: Azure-fájlmegosztás használata az Azure Storage szolgáltatással | Microsoft Docs
description: Az Azure-fájlmegosztások használata Windowson és Windows Serveren.
author: todmccoy
manager: dcscontentpm
ms.service: storage
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: rogarana
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: e1e581134b4e3821659c43bb7f55003239594bd9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233791"
---
# <a name="how-to-recover-a-deleted-storage-account"></a>Törölt Storage-fiók helyreállítása

Az Azure Storage adatrugalmasságot biztosít az automatizált replikák használatával, de nem akadályozza meg, hogy a felhasználók vagy az alkalmazások kódja véletlenül vagy rosszindulatúan sérült legyen. Az alkalmazás-vagy felhasználói hiba példányaiban az adathűség fenntartása fejlettebb technikákat igényel, például az adatok egy másodlagos tárolóhelyre való másolását egy naplóval.

Az alábbi táblázat a Storage-fiók helyreállításának hatókörét mutatja be a replikációs stratégiától függően.

| |LRS|ZRS|GRS|RA-GRS|
|---|---|---|---|---|
|Storage-fiók Azure Resource Manager|Igen|Igen|Igen|Igen|
|Klasszikus Storage-fiók|Igen|Igen|Igen|Igen|

Gyűjtse össze a következő információkat, és küldjön egy támogatási kérést Microsoft ügyfélszolgálata:

* Storage account name (Tárfiók neve)
* Törlés dátuma
* Storage-fiók régiója
* Hogyan törölte a Storage-fiókot?
* Milyen módszerrel törölte a Storage-fiókot? (Portál, PowerShell stb.)

Fontos pontok

* Általában akár 15 napig is eltarthat, amíg a tárolási szolgáltatás nem végez adatgyűjtést, ezért előfordulhat, hogy a Storage-fiókok helyreállítása nem állítható vissza SLA-val.
* Microsoft ügyfélszolgálata megpróbálja helyreállítani a tárolót/fiókot a legjobb erőfeszítés alapján, és nem tudja garantálni a helyreállítást.

> [!NOTE]
> Előfordulhat, hogy a helyreállítás nem lesz sikeres, ha a fiók újra lett létrehozva. Ha már újra létrehozta a fiókot, először törölnie kell azt a helyreállítás megkísérlése előtt.
