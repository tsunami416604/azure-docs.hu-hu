---
title: Excel-bővítmény webes szolgáltatásokhoz
titleSuffix: ML Studio (classic) - Azure
description: Hogyan használható az Azure Machine Learning Web services közvetlenül az Excelben kód írása nélkül.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: e30103589c1baf9a165839cd041ff511a119c5ff
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204375"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-classic-web-services"></a>Azure Machine Learning Studio (klasszikus) webszolgáltatások Excel-bővítménye

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
Az Excel megkönnyíti a webszolgáltatások közvetlenül kód írása nélkül.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Egy meglévő webes szolgáltatás használatához a munkafüzetben lévő lépéseket

1. Nyissa meg a [minta Excel-fájlt](https://aka.ms/amlexcel-sample-2), amely tartalmazza az Excel-bővítményt és a Titanic utasainak adatait. 
 
    > [!NOTE]
    > Látni fogja a webszolgáltatások listája kapcsolódó a fájl és a lap alján egy jelölőnégyzetet az "Automatikus előrejelzése". Ha engedélyezi az automatikus előrejelzést, az **összes** szolgáltatás előrejelzését minden alkalommal frissíti a rendszer, amikor módosul a bemenetek. Ha nincs bejelölve, kell kattintson "Az összes előrejelzése" a frissítéshez. Engedélyezésének automatikus – előrejelzésére egy szolgáltatási szint lépjen a 6. lépés:.

2. Válassza ki a web service rákattintva – "Titanic túlélő előjelző (Excel-bővítményben minta) [Pontszám]" Ebben a példában.
   
    ![Válassza ki a webszolgáltatás](./media/excel-add-in-for-web-services/image1.png)
3. Ezzel elkerül a **prediktív** szakaszba.  Ez a munkafüzet már tartalmaz mintaadatok, de üres munkafüzeteknél kijelölhet egy cellát az Excelben, és kattintson a **mintaadatok használata**lehetőségre.
4. Válassza ki az adatokat a fejlécek, és kattintson a bemeneti adatok tartomány ikonra.  Győződjön meg arról, hogy az "adatok fejléccel rendelkeznek" jelölőnégyzet be van jelölve.
5. A **kimenet**alatt adja meg azt a cellát, ahol a kimenetet szeretné megadni, például a "H1" értéket.
6. Kattintson az **Előrejelzés**gombra. Ha az "auto-predict" jelölőnégyzet bejelölésével bármilyen módosítás a kiválasztott területeken (a bemenetként megadott változóneveket) aktivál egy kérelmet, és a egy frissítést, a kimeneti cellák anélkül, hogy a predict gombra.
   
    ![A szakasz előrejelzése](./media/excel-add-in-for-web-services/image1.png)

Webszolgáltatás üzembe helyezése, vagy egy meglévő webes szolgáltatás használatához. Webszolgáltatások üzembe helyezésével kapcsolatos további információkért lásd: [3. Oktatóanyag: hitelkockázat-modell üzembe helyezése](tutorial-part3-credit-risk-deploy.md).

A webszolgáltatás API-kulcs beszerzése. Hajt végre, ha ez a művelet attól függ, egy új Machine Learning webszolgáltatás klasszikus Machine Learning webszolgáltatás közzététele e.

**Klasszikus webszolgáltatás használata** 

1. Machine Learning Studio (klasszikus) területen kattintson a bal oldali ablaktábla **Web Services (WEBszolgáltatások** ) elemére, majd válassza ki a webszolgáltatást.
   
    ![Studio válassza egy webszolgáltatás](./media/excel-add-in-for-web-services/image4.png)
2. A webszolgáltatás API-kulcs másolása.
   
    ![Studio API-kulcs](./media/excel-add-in-for-web-services/image5.png)
3. A webszolgáltatás **irányítópult** lapján kattintson a **kérelem/válasz** hivatkozásra.
4. Keresse meg a **kérelem URI** szakaszát.  Másolja és mentse az URL-címet.

> [!NOTE]
> Mostantól be lehet jelentkezni a [Azure Machine learning webszolgáltatási](https://services.azureml.net) portálra egy klasszikus Machine learning webszolgáltatás API-kulcsának beszerzéséhez.
> 
> 

**Új webszolgáltatás használata**

1. A [Azure Machine learning webszolgáltatások](https://services.azureml.net) **portálján kattintson a webszolgáltatások**, majd a webszolgáltatás elemre. 
2. Kattintson **a**felhasználás gombra.
3. Keresse meg az **alapszintű felhasználás adatai** szakaszt. Másolja és mentse az **elsődleges kulcsot** és a **kérelem-válasz** URL-címet.

## <a name="steps-to-add-a-new-web-service"></a>Új webes szolgáltatás hozzáadásának lépéseit

1. Webszolgáltatás üzembe helyezése, vagy egy meglévő webes szolgáltatás használatához. Webszolgáltatások üzembe helyezésével kapcsolatos további információkért lásd: [3. Oktatóanyag: hitelkockázat-modell üzembe helyezése](tutorial-part3-credit-risk-deploy.md).
2. Kattintson **a**felhasználás gombra.
3. Keresse meg az **alapszintű felhasználás adatai** szakaszt. Másolja és mentse az **elsődleges kulcsot** és a **kérelem-válasz** URL-címet.
4. Az Excelben nyissa meg a **webszolgáltatások** szakaszt (ha az **Előrejelzés** szakaszban látható, kattintson a vissza nyílra a webszolgáltatások listájának megugrásához).
   
    ![Ugrás a webes szolgáltatás kiválasztása](./media/excel-add-in-for-web-services/image3.png)
5. Kattintson a **webszolgáltatás hozzáadása**lehetőségre.
6. Illessze be az URL-címet az Excel-bővítmény szövegmezőbe feliratú **URL-címébe**.
7. Illessze be az API-t és az elsődleges kulcsot a szövegmezőbe az **API-kulcs**feliratú mezőbe.
8. Kattintson az **Hozzáadás** parancsra.
   
    ![Klasszikus webszolgáltatás URL-CÍMÉT és API-t kulcs.](./media/excel-add-in-for-web-services/image6.png)
9. A web service használatához kövesse az előző ismertető cikk utasításait, "Lépéseket egy meglévő webes szolgáltatás használatára."

## <a name="sharing-your-workbook"></a>A munkafüzet megosztása
Ha mentette a munkafüzetet, a webszolgáltatások hozzáadta az API/elsődleges kulcs is menti. Azt jelenti, hogy a munkafüzet kell csak megosztása, megbízható személlyel.

Tegye fel kérdéseit a következő megjegyzésekkel foglalkozó szakaszban vagy a [fórumunkat](https://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).
