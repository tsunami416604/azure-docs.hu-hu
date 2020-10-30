---
title: Több ág a leképezési adatfolyamban
description: Adatfolyamok replikálása a leképezési adatfolyamban több ág
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: a1dd4baecd0e1f817c93652fbc0766069ccf5583
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040132"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Új ág létrehozása a leképezési adatfolyamban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Egy új ág hozzáadásával több művelet és átalakítás végezhető el ugyanazon adatfolyamon. Az új ág hozzáadása akkor lehet hasznos, ha ugyanazt a forrást szeretné használni több mosogatóhoz, vagy az önkiszolgáló adategyesítéshez.

Az átalakítási listáról más átalakításokhoz hasonló új ág is felvehető. Az **új ág** csak akkor érhető el műveletként, ha már van egy meglévő átalakítás, amely azt követi, hogy milyen átalakítást végez az elágazásban.

![Képernyőfelvétel: az új ág lehetőség a több bemenet/kimenet menüben.](media/data-flow/new-branch2.png "Új ág hozzáadása")

Az alábbi példában az adatfolyam beolvassa a taxi-adatforgalmat. A napi és a szállító által összesített kimenet megadása kötelező. Ahelyett, hogy két különálló adatfolyamatot hozna létre ugyanabból a forrásból, új ágat adhat hozzá. Így az összesítések ugyanúgy hajthatók végre, mint az adatfolyamatok részeként. 

![A képernyőfelvételen a forrás két ága látható az adatfolyam.](media/data-flow/new-branch.png "Új ág hozzáadása")
