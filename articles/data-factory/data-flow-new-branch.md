---
title: Több elágazás az adatfolyam leképezésében
description: Adatfolyamok replikálása több elágazással rendelkező adatfolyamleképezésben
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 71fb9f1ba9952be0e6b3910dd1079aa6d3c0482d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834516"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Új ág létrehozása az adatfolyam leképezésében

Adjon hozzá egy új ágat, amely több művelet- és átalakítást végez ugyanazon adatfolyamon. Új ág hozzáadása akkor hasznos, ha ugyanazt a forrást több fogadóhoz vagy önillesztéshez szeretné használni.

Az átalakításlistából más átalakításokhoz hasonló új ágadható el. **Az új ág** csak akkor lesz elérhető műveletként, ha az elágazni kívánt átalakítást követően már létezik átalakítás.

![Új ág hozzáadása](media/data-flow/new-branch2.png "Új ág hozzáadása")

Az alábbi példában az adatfolyam a taxiút adatait olvassa. A nap és a szállító szerint összesítve a kimenet szükséges. Ahelyett, hogy két különálló adatfolyamot hozna létre, amelyek ugyanabból a forrásból olvasnak, új ágadható el. Így mindkét összesítés ugyanazon adatfolyam részeként hajtható végre. 

![Új ág hozzáadása](media/data-flow/new-branch.png "Új ág hozzáadása")
