---
title: Az Azure blob-tárolókban, SQL Server-adatokat, és a Hive táblák |} Microsoft Docs
description: Hogyan különböző Azure enviromnents tárolt adatokba.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 80a9dfae-e3a6-4cfb-aecc-5701cfc7e39d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 73b0d3992627b7787ee46e61f62717a545c5b9e2
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="heading"></a>Az Azure blob-tárolókban, SQL Server-adatokat, és a Hive táblák
Ez a dokumentum a cikkeket a három különböző Azure helyek egyikén tárolt adatok módjának mutató hivatkozásokat tartalmaz:

* **Az Azure blob-tároló adatok** van mintát az programozott módon letöltheti, és majd mintavételi minta Python kóddal.
* **SQL Server-adatok** mintát venni, SQL és a Python programozási nyelv használatával. 
* **Hive tábla adatai** Hive-lekérdezésekkel van mintát.

A következő **menü** az adatokat az Azure storage környezetben minden egyes a leíró témakörök hivatkozásait. 

[!INCLUDE [cap-sample-data-selector](../../../includes/cap-sample-data-selector.md)]

Ez a mintavételi feladat Ez a lépés a [Team adatok tudományos folyamat (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

**Miért érdemes az adatokat?**

Ha azt tervezi, hogy elemezheti az adatkészlet túl nagy, akkor általában down kétmintás az adatokat, hogy az kisebb, de reprezentatív és könnyebben kezelhető méretű jó ötlet. Ez lehetővé teszi az adatok ismertetése, feltárása és a szolgáltatás mérnöki csapathoz. A szerepkör a Cortana Analytics folyamat ahhoz, hogy az adatok feldolgozása funkciók és a gépi tanulási modellek gyors prototípusának.

