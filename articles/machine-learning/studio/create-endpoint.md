---
title: "A Machine Learning webszolgáltatás-végpontok létrehozása |} Microsoft Docs"
description: "Az Azure Machine Learning webszolgáltatás-végpontok létrehozása"
services: machine-learning
documentationcenter: 
author: hiteshmadan
manager: padou
editor: cgronlun
ms.assetid: 4657fc1b-5228-4950-a29e-bc709259f728
ms.service: machine-learning
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 10/04/2016
ms.author: himad
ms.openlocfilehash: 821ad87fc10b2380e5ed89c037c335bc7747009e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
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
A végpont hozzáadása egy webszolgáltatás-bővítmény három módja van.

* Automatizáltan
* Az Azure Machine Learning webszolgáltatások portálon keresztül
* Bár a klasszikus Azure portálon

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

## <a name="adding-an-endpoint-using-the-azure-classic-portal"></a>A klasszikus Azure portál használatával a végpont hozzáadása
1. Jelentkezzen be a [a klasszikus Azure portálon](http://manage.windowsazure.com), kattintson a **Machine Learning** a bal oldali oszlopban. Kattintson a munkaterületet, amely tartalmazza a webes szolgáltatás, amelyben érdekli.
   
    ![Navigáljon a munkaterület](./media/create-endpoint/figure-1.png)
2. Kattintson a **webszolgáltatások**.
   
    ![Navigáljon a webes szolgáltatások](./media/create-endpoint/figure-2.png)
3. Kattintson a webszolgáltatás számára elérhető végpontok listájának kíváncsiak vagyunk.
   
    ![Navigáljon a végpont](./media/create-endpoint/figure-3.png)
4. Kattintson a lap alján **végpont hozzáadása**. Írja be a nevet és leírást, ellenőrizze, hogy nincsenek a webszolgáltatás azonos nevű nincsenek végpontok. Amennyiben nincsenek különleges követelmények a késleltetési szintjét hagyja az alapértelmezett értékre. Sávszélesség-szabályozás kapcsolatos további információkért lásd: [API-végpontok méretezése](scaling-webservice.md).
   
    ![-Végpont létrehozása](./media/create-endpoint/figure-4.png)

## <a name="next-steps"></a>Következő lépések
[Az Azure Machine Learning Web service felhasználásához hogyan](consume-web-services.md).

