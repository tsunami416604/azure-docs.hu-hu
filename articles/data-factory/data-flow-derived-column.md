---
title: Származtatott oszlop átalakítása a leképezési adatfolyam - Azure Data Factory |} A Microsoft Docs
description: Útmutató az Azure Data Factoryban a leképezési Flow származtatott oszlop átalakítása a nagy mennyiségű adat átalakítására.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 941c629fd8359edc7fc1cf364a6735314044d95e
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312207"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Az adatfolyam-leképezés származtatott oszlop átalakítása

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A származtatott oszlop átalakítása használja, az adatfolyam létrehozni az új oszlopok vagy a meglévő mezők módosítása.

## <a name="derived-column-settings"></a>Származtatott oszlop beállításai

Bírálja felül egy létező oszlop, válassza ki az oszlop legördülő lista használatával. Ellenkező esetben az oszlop kijelölési mezőt használja egy szövegmező, és írja be az új oszlop neve. "Adja meg a kifejezés" párbeszédpanel megnyitásához kattintson a származtatott oszlop kifejezésének összeállítása, a [Data Flow Kifejezésszerkesztő](concepts-data-flow-expression-builder.md).

![Oszlopbeállítások származtatott](media/data-flow/dc1.png "származtatott oszlop beállításai")

További származtatott oszlopok hozzáadásához, vigye a kurzort egy meglévő származtatott oszlop, és kattintson a "+". Ezután válassza a "Oszlop hozzáadása" vagy "Hozzáadás oszlop minta". Oszlop mintákat is származhatnak hasznos, ha az oszlopneveket változó a forrásból. További információkért lásd: [oszlop minták](concepts-data-flow-column-pattern.md).

![Új származtatott Oszlopválasztás](media/data-flow/columnpattern.png "új származtatott oszlop kiválasztása")

## <a name="next-steps"></a>További lépések

- Tudjon meg többet a [kifejezésnyelveket adatfolyam leképezési](data-flow-expression-functions.md).
