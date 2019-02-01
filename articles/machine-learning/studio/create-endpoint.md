---
title: Webszolgáltatás-végpontok létrehozása
titleSuffix: Azure Machine Learning Studio
description: Az Azure Machine Learning webszolgáltatás-végpontok létrehozása A Web service-ben minden végpont egymástól függetlenül foglalkozik, szabályozva, és felügyelt.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 10/04/2016
ms.openlocfilehash: fc3a92aaf13f13682cfc56333618436ffe3d65ef
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55493371"
---
# <a name="creating-endpoints-for-deployed-azure-machine-learning-studio-web-services"></a>A telepített Azure Machine Learning Studio-webszolgáltatásokhoz végpontok létrehozása
> [!NOTE]
>  Ez a témakör ismerteti a technikákat egy **klasszikus** Machine Learning webszolgáltatás.
> 
> 

Ön előre értékesít az ügyfelek futó webes szolgáltatás létrehozásakor meg kell adnia a betanított modellek minden ügyfélnek, amely továbbra is a kísérletet, amelyről a Web service létrejött van csatolva. Emellett a kísérletvászonra frissítéseket kell alkalmazni szelektív egy végpontot a testreszabások felülírása nélkül.

Ehhez Azure Machine Learning Studio lehetővé teszi, hogy hozzon létre egy üzembe helyezett webszolgáltatáshoz több végpontot. A Web service-ben minden végpont egymástól függetlenül foglalkozik, szabályozva, és felügyelt. Minden végpont egy egyedi URL-cím és a hitelesítési kulcsot az ügyfelek számára terjeszthető.



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

