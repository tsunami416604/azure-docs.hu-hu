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
ms.date: 03/01/2019
ms.openlocfilehash: a2af1fdd1ee461e3b3db613ff4a575649da2dfdc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827439"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Azure SQL Database Machine Learning Services R-vel (előzetes verzió)

A Machine Learning Services az adatbázis-alapú R-parancsfájlok végrehajtásához használt Azure SQL Database szolgáltatása. A szolgáltatás Microsoft R-csomagokat tartalmaz a nagy teljesítményű prediktív elemzéshez és a gépi tanuláshoz. A viszonyítási adatokat az R-parancsfájlokban tárolt eljárások, az R-utasításokat tartalmazó T-SQL-szkript vagy a T-SQL-t tartalmazó R-kód használatával lehet használni.

> [!IMPORTANT]
> A Azure SQL Database Machine Learning Services (R) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> A nyilvános előzetes verzió a virtuális mag-alapú vásárlási modellt használó önálló adatbázisok és rugalmas készletek számára érhető el az **általános célú** és az **üzleti szempontból kritikus** szolgáltatási rétegekben. Ebben a kezdeti nyilvános előzetes verzióban a **nagy kapacitású** szolgáltatási szintje és a **felügyelt példányok** központi telepítési lehetősége nem támogatott. Jelenleg az R az egyetlen támogatott nyelv. A Python jelenleg nem támogatott.
>
> Az előzetes verzió jelenleg a következő régiókban érhető el: Nyugat-Európa, Észak-Európa, USA 2. nyugati régiója, USA keleti régiója, USA déli középső régiója, északi középső régiója, Közép-Kanada, Délkelet-Ázsia, Dél-India és Délkelet-Ausztrália.
>
> [Regisztráljon az előzetes](#signup) verzióra.

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

A nyilvános előzetes verzióra való feliratkozáshoz kövesse az alábbi lépéseket:

1. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy fiókot](https://azure.microsoft.com/free/) .

2. Küldjön egy e-mailt a Microsoftnak [sqldbml@microsoft.com](mailto:sqldbml@microsoft.com) a nyilvános előzetes verzióra való feliratkozáshoz. A Machine Learning Services (with R) nyilvános előzetes verziójának használata az SQL Database-ben alapértelmezés szerint nem engedélyezett.

Miután regisztrált a programban, a Microsoft bevezeti Önt a nyilvános előzetes verzióra, és engedélyezi az R-t a meglévő vagy az új adatbázishoz.

Az R-vel való Machine Learning Services a nyilvános előzetes verzióban nem ajánlott éles számítási feladatokhoz.

## <a name="next-steps"></a>További lépések

- Tekintse meg [SQL Server Machine learning Services főbb különbségeit](sql-database-machine-learning-services-differences.md).
- Ha szeretné megtudni, hogyan használhatja az R-t a Azure SQL Database Machine Learning Services (előzetes verzió) lekérdezéséhez, tekintse meg a gyors [üzembe helyezési útmutatót](sql-database-connect-query-r.md).
- Néhány egyszerű R-szkript megkezdéséhez lásd: [egyszerű r-parancsfájlok létrehozása és futtatása Azure SQL Database Machine learning Servicesban (előzetes verzió)](sql-database-quickstart-r-create-script.md).
