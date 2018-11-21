---
title: Webszolgáltatás-végpontok létrehozása a Machine Learning |} A Microsoft Docs
description: Az Azure Machine Learning webszolgáltatás-végpontok létrehozása
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=yahajiza, author=YasinMSFT)
ms.author: amlstudiodocs
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
ms.openlocfilehash: f046db11bf3c04c9ea15e759b4e0080cab4f71d5
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52265066"
---
# <a name="creating-endpoints"></a>Végpontok létrehozása
> [!NOTE]
>  Ez a témakör ismerteti a technikákat egy **klasszikus** Machine Learning webszolgáltatás.
> 
> 

Ön előre értékesít az ügyfelek futó webes szolgáltatás létrehozásakor meg kell adnia a betanított modellek minden ügyfélnek, amely továbbra is a kísérletet, amelyről a Web service létrejött van csatolva. Emellett a kísérletvászonra frissítéseket kell alkalmazni szelektív egy végpontot a testreszabások felülírása nélkül.

Ehhez Azure Machine Learning lehetővé teszi, hogy hozzon létre egy üzembe helyezett webszolgáltatáshoz több végpontot. A Web service-ben minden végpont egymástól függetlenül foglalkozik, szabályozva, és felügyelt. Minden végpont egy egyedi URL-cím és a hitelesítési kulcsot az ügyfelek számára terjeszthető.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="adding-endpoints-to-a-web-service"></a>Végpontok hozzáadása a webszolgáltatáshoz
Két módon egy webszolgáltatás-végpont hozzáadása.

* Programozott módon
* Az Azure Machine Learning webszolgáltatások portálján

A végpont a létrehozás után szinkron API-k segítségével, a batch API-k, ezért használja fel, és excel-munkalapokat. A felhasználói felületen keresztüli végpontokra hozzáadásán is használhatja a végpont felügyeleti API-k programozott módon végpontjaira.

> [!NOTE]
> Ha további végpontokat hozzáadta a Web Service, az alapértelmezett végpont nem törölhető.
> 
> 

## <a name="adding-an-endpoint-programmatically"></a>A végpont programozott módon hozzáadása
A webszolgáltatás programozással használatával adhat hozzá egy végpontot a [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) mintakódot.

## <a name="adding-an-endpoint-using-the-azure-machine-learning-web-services-portal"></a>Az Azure Machine Learning Web Services portál használata a végpont hozzáadása
1. Machine Learning Studio a bal oldali navigációs oszlopban kattintson a Web Services.
2. A webszolgáltatás irányítópultján alján kattintson **Végpontkezelés**. Az Azure Machine Learning Web Services portálon megnyitja a webszolgáltatás a végpontok lapot.
3. Kattintson az **Új** lehetőségre.
4. Írjon be egy nevet és leírást az új végpont. A Végpontnevek 24 karakter vagy kevesebb, hosszúságúnak kell lennie, és kisbetűket betűk vagy számok el kell készíteni. Válassza ki a naplózási szint, és hogy engedélyezve van-e a mintaadatok. A naplózás további információkért lásd: [naplózás engedélyezése a Machine Learning webszolgáltatások](web-services-logging.md).

## <a name="next-steps"></a>További lépések
[Hogyan lehet egy Azure Machine Learning Web service](consume-web-services.md).

