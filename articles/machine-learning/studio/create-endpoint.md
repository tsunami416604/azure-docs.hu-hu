---
title: A Machine Learning webszolgáltatás-végpontok létrehozása |} Microsoft Docs
description: Az Azure Machine Learning webszolgáltatás-végpontok létrehozása
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 4657fc1b-5228-4950-a29e-bc709259f728
ms.service: machine-learning
ms.component: studio
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 10/04/2016
ms.openlocfilehash: 8cdf8c5ac3676d8abc9084fc842484aca5b6d1c7
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833546"
---
# <a name="creating-endpoints"></a>Végpontok létrehozása
> [!NOTE]
>  Ez a témakör ismerteti a technikákról egy **klasszikus** Machine Learning webszolgáltatáshoz.
> 
> 

Webalapú alkalmazásokhoz, az ügyfelek számára előre eladásra létrehozásakor meg kell adnia a betanított modellek minden ügyfélnek, amely továbbra is a kísérlet, a webszolgáltatás létrehozásához használt van csatolva. Emellett a kísérletvászonra frissítéseket alkalmazandó szelektív végpont a testreszabások felülírása nélkül.

Ennek megvalósítása érdekében Azure Machine Learning hozhat létre az adott üzembe helyezett webszolgáltatás több végpontot. A webszolgáltatás a végpontok egymástól függetlenül tárgyalt, szabályozva, és felügyelt. Minden egyes végpont egy egyedi URL-cím és az ügyfelek számára terjeszthető hitelesítési kulcsot.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="adding-endpoints-to-a-web-service"></a>Végpontok felvétele egy webszolgáltatás-bővítmény
A végpont hozzáadása egy webszolgáltatás-bővítmény két módja van.

* Programozott módon
* Az Azure Machine Learning webszolgáltatások portálon keresztül

Miután létrehozta a végpontot, szokásokra is szinkron API-k, kötegelt API-k, segítségével, és az excel-munkalapokat. A felhasználói felületen keresztüli végpontokra hozzáadásán is használhatja a végpont felügyeleti API-k szoftveres a végpontok hozzáadása.

> [!NOTE]
> Ha további végpontok hozzáadta a webes szolgáltatás, az alapértelmezett végpont nem törölhető.
> 
> 

## <a name="adding-an-endpoint-programmatically"></a>A végpont programozott módon hozzáadása
A webszolgáltatás programozott módon adhat hozzá a végpont a [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) példakód.

## <a name="adding-an-endpoint-using-the-azure-machine-learning-web-services-portal"></a>Az Azure Machine Learning webszolgáltatások portálon végpont hozzáadása
1. A Machine Learning Studio a bal oldali oszlopban kattintson a webszolgáltatásokat.
2. A webes szolgáltatás irányítópultját alján kattintson **végpontok kezelése**. Az Azure Machine Learning webszolgáltatások portal nyitja meg a webszolgáltatás végpontok oldalon.
3. Kattintson az **Új** lehetőségre.
4. Írja be nevét és leírását, az új végpont. Végpontneveit 24 karakter vagy kevesebb hosszúságúnak kell lennie, és a magyar ábécét kisbetűket és számokat kell készíteni. Válassza ki a naplózási szint, és hogy engedélyezve van-e mintaadatokat. További információt a naplózást, [naplózását a Machine Learning webszolgáltatások](web-services-logging.md).

## <a name="next-steps"></a>További lépések
[Az Azure Machine Learning Web service felhasználásához hogyan](consume-web-services.md).

