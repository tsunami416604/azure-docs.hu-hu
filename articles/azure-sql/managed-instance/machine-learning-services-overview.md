---
title: Machine Learning Services az Azure SQL felügyelt példányában (előzetes verzió)
description: Ez a cikk áttekintést nyújt vagy Machine Learning Services az Azure SQL felügyelt példányain.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: carlrab, davidph
manager: cgronlun
ms.date: 06/03/2020
ms.openlocfilehash: ad25a9a62757e1e031ce8b93a44e6f2ada4d9964
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689507"
---
# <a name="machine-learning-services-in-azure-sql-managed-instance-preview"></a>Machine Learning Services az Azure SQL felügyelt példányában (előzetes verzió)

A Machine Learning Services az Azure SQL felügyelt példányainak (előzetes verzió) egyik funkciója, amely az adatbázison belüli gépi tanulást biztosítja a Python és az R szkriptek támogatásával. A szolgáltatás a Microsoft Python és R csomagokat tartalmazza a nagy teljesítményű prediktív elemzéshez és a gépi tanuláshoz. A kapcsolódó adatokat parancsfájlokban tárolt eljárásokkal, a Python-vagy R-utasításokat tartalmazó T-SQL-szkripttel, illetve a T-SQL-T tartalmazó Python vagy R kód használatával lehet használni.

> [!IMPORTANT]
> A Machine Learning Services a felügyelt Azure SQL-példány egyik szolgáltatása, amely jelenleg nyilvános előzetes verzióként érhető el.
> Ez az előzetes verziójú funkció kezdetben csak korlátozott számú régióban érhető el az Egyesült Államokban, Ázsia-Európában és Ausztráliában, és később további régiók is elérhetők.
>
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> [Regisztráljon az előzetes](#signup) verzióra.

## <a name="what-is-machine-learning-services"></a>Mi az Machine Learning Services?

A felügyelt Azure SQL-példányok Machine Learning Services lehetővé teszi a Python-és R-parancsfájlok futtatását az adatbázisban. Felhasználhatja az adatgyűjtést és-tisztítást, a szolgáltatások fejlesztését, valamint a gépi tanulási modellek adatbázison belüli betanítását, kiértékelését és üzembe helyezését. A szolgáltatás futtatja a parancsfájlokat, ahol az adatok találhatók, és kiküszöböli az adatok átvitelét a hálózaton keresztül egy másik kiszolgálóra.

Machine Learning Services használata R/Python-támogatással az Azure SQL felügyelt példányain:

- **R-és Python-parancsfájlok futtatása az adat-előkészítéshez és az általános célú adatfeldolgozáshoz** : mostantól az r/Python-szkripteket áthelyezheti az Azure SQL felügyelt példányára, ahol az adatai életbe léptek, ahelyett, hogy át kellene helyeznie az adatátvitelt egy másik kiszolgálóra az r-és Python-parancsfájlok A késéssel, a biztonsággal és a megfelelőséggel kapcsolatos adatáthelyezési és kapcsolódó problémák elhárítása nem szükséges.

- **Gépi tanulási modellek betanítása az adatbázisban** – bármely nyílt forráskódú algoritmus használatával betaníthatja a modelleket. A betanítást egyszerűen méretezheti a teljes adathalmazra, nem pedig az adatbázisból kihúzott minta adatkészletekre.

- **Modellek és szkriptek üzembe helyezése tárolt eljárásokban** – a parancsfájlok és a betanított modellek csak a T-SQL tárolt eljárásokban való beágyazással működőképesek. Az Azure SQL felügyelt példányához csatlakozó alkalmazások az ilyen modellek előrejelzéseit és intelligencia előnyeit csak tárolt eljárás meghívásával érhetik el. A natív T-SQL ELŐREJELZÉSi függvényt is használhatja a működővé tenni modellek gyors értékeléséhez a nagy egyidejű valós idejű pontozási forgatókönyvekben.

A Python és az R alapszintű terjesztése a Machine Learning Services része. Olyan nyílt forráskódú csomagokat és keretrendszereket telepíthet és használhat, mint például a PyTorch, a TensorFlow és a scikit-Learn, valamint a Microsoft-csomagok [revoscalepy csomagjai](https://docs.microsoft.com/sql/advanced-analytics/python/ref-py-revoscalepy) és [microsoftml](https://docs.microsoft.com/sql/advanced-analytics/python/ref-py-microsoftml) a Pythonhoz, valamint [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler), [microsoftml](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml), [OLAP](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-olapr)és [sqlrutils](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-sqlrutils) for R.

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Feliratkozás az előzetes verzióra

A korlátozott nyilvános előzetes verzióra az [Azure előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verziójának feltételei vonatkoznak. 

Ha szeretne csatlakozni az előzetes verzióhoz, és elfogadja ezeket a feltételeket, akkor a regisztrációt az Azure támogatási jegyének létrehozásával kérheti le [**https://azure.microsoft.com/support/create-ticket/**](https://azure.microsoft.com/support/create-ticket/) . 

1. Válassza ki a következő beállításokat:
   - Probléma típusa – **technikai**
   - Előfizetés – *válassza ki az előfizetését*
   - Szolgáltatás – **SQL Database felügyelt példány**
   - Összefoglalás – *adja meg a kérelem rövid leírását*
   - Probléma típusa – **Machine learning Services a felügyelt SQL-példányhoz (előzetes verzió)**
   - Probléma altípusa – **egyéb probléma vagy "How to" kérdések**

1. Kattintson a **Tovább gombra: megoldások**.

1. Olvassa el az előzetes verzióra vonatkozó információkat, majd kattintson a **részletek**gombra.

1. A **Leírás**mezőben adja meg a kérés sajátosságait, beleértve az előzetes verzióban regisztrálni kívánt logikai kiszolgáló nevét, régióját és ELŐfizetési azonosítóját. Szükség szerint adja meg a további adatokat.

1. Ha elkészült, kattintson a **Tovább gombra: felülvizsgálat + létrehozás**, majd kattintson a **Létrehozás**gombra.

Miután regisztrált a programban, a Microsoft belépteti Önt a nyilvános előzetes verzióba és engedélyezi a Machine Learning Servicest a meglévő vagy az új adatbázisban.

A felügyelt SQL-példányokban a Machine Learning Services használata nem javasolt éles számítási feladatokhoz a nyilvános előzetes verzióban.

## <a name="next-steps"></a>További lépések

- Tekintse meg [SQL Server Machine learning Services főbb különbségeit](machine-learning-services-differences.md).
- Ha szeretné megtudni, hogyan használható a Python a Machine Learning Servicesban, tekintse meg a [Python-parancsfájlok futtatása](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15)című témakört.
- Az R-Machine Learning Services használatának megismeréséhez tekintse meg az [r-parancsfájlok futtatása](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15)című témakört.
- A más SQL-platformokon futó gépi tanulásról további információt az [SQL Machine learning dokumentációjában](https://docs.microsoft.com/sql/machine-learning/)talál.
