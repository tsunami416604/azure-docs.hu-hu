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
ms.openlocfilehash: af356641b4588529aea25826ff180707ff1ef4e2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413604"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Új ág létrehozása az adatfolyam leképezésében

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Adjon hozzá egy új ágat, amely több művelet- és átalakítást végez ugyanazon adatfolyamon. Új ág hozzáadása akkor hasznos, ha ugyanazt a forrást több fogadóhoz vagy önillesztéshez szeretné használni.

Az átalakításlistából más átalakításokhoz hasonló új ágadható el. **Az új ág** csak akkor lesz elérhető műveletként, ha az elágazni kívánt átalakítást követően már létezik átalakítás.

![Új ág hozzáadása](media/data-flow/new-branch2.png "Új ág hozzáadása")

Az alábbi példában az adatfolyam a taxiút adatait olvassa. A nap és a szállító szerint összesítve a kimenet szükséges. Ahelyett, hogy két különálló adatfolyamot hozna létre, amelyek ugyanabból a forrásból olvasnak, új ágadható el. Így mindkét összesítés ugyanazon adatfolyam részeként hajtható végre. 

![Új ág hozzáadása](media/data-flow/new-branch.png "Új ág hozzáadása")
