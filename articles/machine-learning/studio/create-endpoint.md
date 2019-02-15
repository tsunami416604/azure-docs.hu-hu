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
ms.date: 02/12/2019
ms.openlocfilehash: a6945ac7bfb750916e229ae04376f895f2b3d506
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56267268"
---
# <a name="creating-endpoints-for-deployed-azure-machine-learning-studio-web-services"></a>A telepített Azure Machine Learning Studio-webszolgáltatásokhoz végpontok létrehozása

> [!NOTE]
> Ez a témakör ismerteti a technikákat egy **klasszikus** Machine Learning webszolgáltatás.

A webszolgáltatások telepítése után egy alapértelmezett végpont kerül létrehozásra az adott szolgáltatáshoz. Az alapértelmezett végpont az API-kulcs használatával hívható meg. Saját további végpontokat adhat hozzá a Web Services portálon.
A Web service-ben minden végpont egymástól függetlenül foglalkozik, szabályozva, és felügyelt. Minden végpont egy engedélyezési kulcsot az ügyfelek számára terjeszthető egy egyedi URL-CÍMÉT.

## <a name="adding-endpoints-to-a-web-service"></a>Végpontok hozzáadása a webszolgáltatáshoz

A végpont egy webszolgáltatás, az Azure Machine Learning Web Services portál használatával is hozzáadhat. A végpont a létrehozás után szinkron API-k segítségével, a batch API-k, ezért használja fel, és excel-munkalapokat.

> [!NOTE]
> Ha további végpontokat hozzáadta a Web Service, az alapértelmezett végpont nem törölhető.

1. Machine Learning Studio a bal oldali navigációs oszlopban kattintson a Web Services.
2. A webszolgáltatás irányítópultján alján kattintson **Végpontkezelés**. Az Azure Machine Learning Web Services portálon megnyitja a webszolgáltatás a végpontok lapot.
3. Kattintson az **Új** lehetőségre.
4. Írjon be egy nevet és leírást az új végpont. A Végpontnevek 24 karakter vagy kevesebb, hosszúságúnak kell lennie, és kisbetűket betűk vagy számok el kell készíteni. Válassza ki a naplózási szint, és hogy engedélyezve van-e a mintaadatok. A naplózás további információkért lásd: [naplózás engedélyezése a Machine Learning webszolgáltatások](web-services-logging.md).

## <a name="next-steps"></a>További lépések

[Hogyan lehet egy Azure Machine Learning Web service](consume-web-services.md).
