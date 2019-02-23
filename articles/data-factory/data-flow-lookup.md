---
title: Az Azure Data Factory-folyamat keresési adatátalakítás leképezése
description: Az Azure Data Factory-folyamat keresési adatátalakítás leképezése
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: b156917a9987b023a9bf94e51c0cc14aebb133c7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56738385"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Az Azure Data Factory-folyamat keresési adatátalakítás leképezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Keresés használatával más forrásból származó referenciaadatokkal hozzáadása az adatfolyam. A keresési átalakító egy meghatározott forrásból, amely a referencia-táblára mutat, és megfelel-e a kulcsmezők van szükség.

![Keresés átalakítása](media/data-flow/lookup1.png "keresése")

Válassza ki a bejövő adatfolyam mezőket és a mezőket a referencia-forrás között egyezés kívánt kulcsmezők. Kell először hozott létre egy új forrás a vásznon adatfolyam tervező használatára, a jobb oldalon a kereséshez.

Egyezés, ha az eredményül kapott sorokat és oszlopokat a referencia-forrásból hozzáadódik az adatfolyam. Kiválaszthatja, hogy a lényeges, az adatfolyam végén található a fogadó szerepeltetni kívánt mezőket.
