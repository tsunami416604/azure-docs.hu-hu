---
title: Machine Learning Services az R-vel (előzetes verzió)
description: Ez a cikk a Azure SQL Database Machine Learning Services (R) használatát ismerteti, és bemutatja, hogyan működik.
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
ms.openlocfilehash: ca223de2bc0b26e4968d400ea418761a399dacae
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78364598"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Azure SQL Database Machine Learning Services R-vel (előzetes verzió)

A Machine Learning Services az adatbázis-alapú R-parancsfájlok végrehajtásához használt Azure SQL Database szolgáltatása. A szolgáltatás Microsoft R-csomagokat tartalmaz a nagy teljesítményű prediktív elemzéshez és a gépi tanuláshoz. A viszonyítási adatokat az R-parancsfájlokban tárolt eljárások, az R-utasításokat tartalmazó T-SQL-szkript vagy a T-SQL-t tartalmazó R-kód használatával lehet használni.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

> [!NOTE]
> Az előzetes verzió az **általános célú** és az **üzleti szempontból kritikus** szolgáltatási rétegekben az virtuális mag-alapú vásárlási modell használatával érhető el az önálló adatbázisok és a rugalmas készletek számára. Ebben a kezdeti előzetes verzióban a **nagy kapacitású** szolgáltatási szintje és a **felügyelt példány** központi telepítési lehetősége nem támogatott. Jelenleg az R az egyetlen támogatott nyelv. A Python jelenleg nem támogatott.
>
> Az előzetes verzió jelenleg a következő régiókban érhető el: Nyugat-Európa, Észak-Európa, USA 2. nyugati régiója, USA keleti régiója, USA déli középső régiója, északi középső régiója, Közép-Kanada, Délkelet-Ázsia, Dél-India és Délkelet-Ausztrália.

## <a name="what-you-can-do-with-r"></a>Az R használatával

Használja az R nyelv hatékonyságát a fejlett elemzési és gépi tanulási adatbázison. Ez a képesség számításokat és feldolgozást biztosít az adattároláshoz, így nincs szükség a hálózatra irányuló adatkérésre. Emellett kihasználhatja a nagyvállalati R-csomagok hatékonyságát is, így nagy léptékű fejlett analitikai teljesítményt biztosíthat.

A Machine Learning Services az R alapszintű eloszlását tartalmazza, amely a Microsoft vállalati R-csomagjaival van ellátva. A Microsoft R functions és algoritmusai mind a skálázási, mind a segédprogramra, a prediktív elemzések, a statisztikai modellezés, az adatvizualizációk és az élvonalbeli gépi tanulási algoritmusok megvalósítására szolgálnak.

### <a name="r-packages"></a>R-csomagok

A leggyakoribb nyílt forráskódú R-csomagok a Machine Learning Services előre telepítve vannak. A Microsoft alábbi R-csomagjait is tartalmazza:

| R-csomag | Leírás|
|-|-|
| [Microsoft R megnyitva](https://mran.microsoft.com/rro) | A Microsoft R Open az R továbbfejlesztett terjesztése a Microsofttól. Ez egy teljes körű nyílt forráskódú platform a statisztikai elemzéshez és az adatelemzéshez. A szolgáltatás a és a 100%-os kompatibilis az R-vel, és további képességeket tartalmaz a jobb teljesítmény és a reprodukálhatóság érdekében. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | A RevoScaleR az elsődleges könyvtár a méretezhető R-hez. a könyvtárban lévő függvények a legszélesebb körben használatosak. Ezekben a tárakban megtalálhatók az adatátalakítások és-manipulációk, a statisztikai összesítések, a vizualizációk és a modellezés és elemzések számos formája. Emellett az ezekben a tárakban lévő függvények automatikusan terjesztik a számítási feladatokat az elérhető magok között a párhuzamos feldolgozáshoz, és lehetővé teszi a számítási motor által koordinált és kezelt adattömbökön végzett munkát. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | A MicrosoftML a gépi tanulási algoritmusok segítségével egyéni modelleket hozhat létre a szöveges elemzéshez, a képelemzéshez és a hangulat elemzéséhez. |

Az előre telepített csomagok mellett [további csomagokat is telepíthet](sql-database-machine-learning-services-add-r-packages.md).

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Feliratkozás az előzetes verzióra

> [!IMPORTANT]
> Azure SQL Database Machine Learning Services (előzetes verzió) regisztrációja jelenleg be van zárva.

Az előnézetben nem ajánlott az R-vel Machine Learning Services az éles számítási feladatokhoz.

## <a name="next-steps"></a>Következő lépések

- Tekintse meg [SQL Server Machine learning Services főbb különbségeit](sql-database-machine-learning-services-differences.md).
- Ha szeretné megtudni, hogyan használhatja az R-t a Azure SQL Database Machine Learning Services (előzetes verzió) lekérdezéséhez, tekintse meg a gyors [üzembe helyezési útmutatót](sql-database-connect-query-r.md).
- Néhány egyszerű R-szkript megkezdéséhez lásd: [egyszerű r-parancsfájlok létrehozása és futtatása Azure SQL Database Machine learning Servicesban (előzetes verzió)](sql-database-quickstart-r-create-script.md).
