---
title: Gépi tanulási szolgáltatások R-rel (előzetes verzió)
description: Ez a cikk ismerteti az Azure SQL Database Machine Learning Services (R) és ismerteti, hogyan működik.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 11/20/2019
ms.openlocfilehash: 2a2cd4bfc3d393543b41eea776f02723d94054b1
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345831"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Azure SQL Database Machine Learning Services R-rel (előzetes verzió)

A Machine Learning Services az Azure SQL Database egyik szolgáltatása, amely az adatbázison belüli R-parancsfájlok végrehajtására szolgál. A szolgáltatás microsoft R-csomagokat tartalmaz a nagy teljesítményű prediktív elemzésekhez és a gépi tanuláshoz. A relációs adatok az R parancsfájlokban tárolhatók, az R utasításokat tartalmazó T-SQL parancsfájlok vagy a T-SQL-t tartalmazó R-kód segítségével.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

> [!NOTE]
> Az előzetes verzió egyetlen adatbázisokhoz és rugalmas készletekhez érhető el a virtuálismag-alapú vásárlási modell használatával az **általános célú** és az **üzleti legkritikusabb** szolgáltatási szinteken. Ebben a kezdeti előzetes verzióban a **nagy kapacitású** szolgáltatási szint és a **felügyelt példány** üzembe helyezése beállítás nem támogatott. Jelenleg az R az egyetlen támogatott nyelv. A Python jelenleg nem támogatott.
>
> Az előzetes verzió jelenleg a következő régiókban érhető el: Nyugat-Európa, Észak-Európa, USA nyugati régiója 2, USA keleti régiója, USA déli középső régiója, USA északi középső régiója, Kanada középső része, Délkelet-Ázsia, India déli része és Délkelet-Ausztrália.

## <a name="what-you-can-do-with-r"></a>Mit lehet tenni az R

Használja az R nyelv erejét a fejlett elemzések és a gépi tanulás adatbázison belüli biztosításához. Ez a képesség a számításokat és a feldolgozást az adatok tartózkodási helyéhez viszi, így nincs szükség az adatok hálózaton keresztüli lekérése. Emellett kihasználhatja a vállalati R-csomagok erejét, hogy nagy léptékű fejlett elemzéseket nyújtson.

A Machine Learning Services tartalmazza az R alapeloszlását, amelyet a Microsoft vállalati R-csomagjai foglalnak el. A Microsoft R-funkcióit és algoritmusait mind a méretezési, mind a segédprogramok számára tervezték, amelyek prediktív elemzéseket, statisztikai modellezést, adatvizualizációkat és élvonalbeli gépi tanulási algoritmusokat biztosítanak.

### <a name="r-packages"></a>R csomagok

A leggyakoribb nyílt forráskódú R-csomagok előre telepítve vannak a Machine Learning Servicesszolgáltatásban. A Microsoft következő R-csomagjait is tartalmazza:

| R csomag | Leírás|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | A Microsoft R Open az R továbbfejlesztett terjesztése a Microsofttól. Ez egy teljes nyílt forráskódú platform a statisztikai elemzés és az adattudomány számára. Ez alapján, és 100%-ban kompatibilis az R, és magában foglalja a további képességek a jobb teljesítmény és reprodukálhatóság. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | A RevoScaleR a méretezhető R elsődleges függvénytár. Ezekben a könyvtárakban adatátalakítások és manipulációk, statisztikai összegzések, vizualizációk, valamint a modellezés és elemzések számos formája található. Emellett a függvényekben ezek a függvények automatikusan elosztja a számítási feladatok között a rendelkezésre álló magok párhuzamos feldolgozás, azzal a képességgel, hogy a számítási motor által koordinált és felügyelt adattömbök. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | A MicrosoftML gépi tanulási algoritmusokat ad hozzá a szövegelemzéshez, a képelemzéshez és a hangulatelemzéshez egyéni modellek létrehozásához. |

Az előre telepített csomagok mellett további [csomagokat is telepíthet.](sql-database-machine-learning-services-add-r-packages.md)

<a name="signup"></a>

## <a name="sign-up-for-the-preview-closed"></a>Feliratkozás az előzetes verzióra (lezárva)

> [!IMPORTANT]
> Az Azure SQL Database Machine Learning Services szolgáltatásra való feliratkozás (előzetes verzió) jelenleg **le van zárva.**

## <a name="next-steps"></a>További lépések

- Tekintse meg az [SQL Server Machine Learning Services legfontosabb különbségeit.](sql-database-machine-learning-services-differences.md)
- Ha meg szeretné tudni, hogyan lehet az R használatával lekérdezni az Azure SQL Database Machine Learning Services -t (előzetes verzió), olvassa el a [rövid útmutatót.](sql-database-connect-query-r.md)
- Néhány egyszerű R-parancsfájl létrehozásáról [és futtatásáról az Azure SQL Database Machine Learning Services szolgáltatásban (előzetes verzió) című témakörben található egyszerű R-parancsfájlok létrehozása és futtatása.](sql-database-quickstart-r-create-script.md)
