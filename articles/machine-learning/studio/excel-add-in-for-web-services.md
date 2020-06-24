---
title: Excel-bővítmény webes szolgáltatásokhoz
titleSuffix: ML Studio (classic) - Azure
description: Hogyan használható a Azure Machine Learning webszolgáltatások használata közvetlenül az Excelben anélkül, hogy kódot kellene írnia.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: 8565e3c62ea1b74879f2e127abed772576563886
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85211323"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-classic-web-services"></a>Azure Machine Learning Studio (klasszikus) webszolgáltatások Excel-bővítménye

Az Excel megkönnyíti a webszolgáltatások közvetlen meghívását anélkül, hogy kódot kellene írnia.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>A meglévő webszolgáltatás használatának lépései a munkafüzetben

1. Nyissa meg a [minta Excel-fájlt](https://aka.ms/amlexcel-sample-2), amely tartalmazza az Excel-bővítményt és a Titanic utasainak adatait. 
 
    > [!NOTE]
    > Ekkor megjelenik a fájlhoz kapcsolódó webszolgáltatások listája, valamint az "automatikus előrejelzés" jelölőnégyzet alján. Ha engedélyezi az automatikus előrejelzést, az **összes** szolgáltatás előrejelzését minden alkalommal frissíti a rendszer, amikor módosul a bemenetek. Ha nincs bejelentkezve, kattintson az "összes előrejelzés" gombra a frissítéshez. Ha engedélyezni szeretné az automatikus előrejelzést a szolgáltatási szinten, folytassa a 6. lépéssel.

2. Válassza ki a webszolgáltatást a következőre kattintva: "Titanic Survivor-előrejelző (Excel-bővítmény minta) [score]" ebben a példában.
   
    ![Webszolgáltatás kiválasztása](./media/excel-add-in-for-web-services/image1.png)
3. Ezzel elkerül a **prediktív** szakaszba.  Ez a munkafüzet már tartalmaz mintaadatok, de üres munkafüzeteknél kijelölhet egy cellát az Excelben, és kattintson a **mintaadatok használata**lehetőségre.
4. Válassza ki a fejléceket tartalmazó adatokat, majd kattintson a bemeneti adattartomány ikonra.  Győződjön meg arról, hogy a "saját adatfejlécek" jelölőnégyzet be van jelölve.
5. A **kimenet**alatt adja meg azt a cellát, ahol a kimenetet szeretné megadni, például a "H1" értéket.
6. Kattintson az **Előrejelzés**gombra. Ha bejelöli az "automatikus előrejelzés" jelölőnégyzetet, akkor a kiválasztott területeken (a bemenetként megadottak szerint) a kérést és a kimeneti cellák frissítését a prediktív gomb megnyomása nélkül kell elindítania.
   
    ![Előrejelzési szakasz](./media/excel-add-in-for-web-services/image1.png)

Webszolgáltatások üzembe helyezése vagy meglévő webszolgáltatás használata. Webszolgáltatások üzembe helyezésével kapcsolatos további információkért lásd: [3. Oktatóanyag: hitelkockázat-modell üzembe helyezése](tutorial-part3-credit-risk-deploy.md).

Szerezze be a webszolgáltatás API-kulcsát. A művelet végrehajtása attól függ, hogy egy új Machine Learning webszolgáltatás klasszikus Machine Learning webszolgáltatását közzétette-e.

**Klasszikus webszolgáltatás használata** 

1. Machine Learning Studio (klasszikus) területen kattintson a bal oldali ablaktábla **Web Services (WEBszolgáltatások** ) elemére, majd válassza ki a webszolgáltatást.
   
    ![Studio – webszolgáltatás kiválasztása](./media/excel-add-in-for-web-services/image4.png)
2. Másolja a webszolgáltatás API-kulcsát.
   
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

## <a name="steps-to-add-a-new-web-service"></a>Új webszolgáltatás hozzáadásának lépései

1. Webszolgáltatások üzembe helyezése vagy meglévő webszolgáltatás használata. Webszolgáltatások üzembe helyezésével kapcsolatos további információkért lásd: [3. Oktatóanyag: hitelkockázat-modell üzembe helyezése](tutorial-part3-credit-risk-deploy.md).
2. Kattintson **a**felhasználás gombra.
3. Keresse meg az **alapszintű felhasználás adatai** szakaszt. Másolja és mentse az **elsődleges kulcsot** és a **kérelem-válasz** URL-címet.
4. Az Excelben nyissa meg a **webszolgáltatások** szakaszt (ha az **Előrejelzés** szakaszban látható, kattintson a vissza nyílra a webszolgáltatások listájának megugrásához).
   
    ![Ugrás a webszolgáltatás kiválasztására](./media/excel-add-in-for-web-services/image3.png)
5. Kattintson a **webszolgáltatás hozzáadása**lehetőségre.
6. Illessze be az URL-címet az Excel-bővítmény szövegmezőbe feliratú **URL-címébe**.
7. Illessze be az API-t és az elsődleges kulcsot a szövegmezőbe az **API-kulcs**feliratú mezőbe.
8. Kattintson a **Hozzáadás** parancsra.
   
    ![Egy klasszikus webszolgáltatáshoz tartozó URL-cím és API-kulcs.](./media/excel-add-in-for-web-services/image6.png)
9. A webszolgáltatás használatához kövesse az előző utasításokat, "a meglévő webszolgáltatás használatának lépései" című témakört.

## <a name="sharing-your-workbook"></a>A munkafüzet megosztása
Ha menti a munkafüzetet, a hozzáadott webszolgáltatásokhoz tartozó API/elsődleges kulcs is mentve lesz. Ez azt jelenti, hogy csak a megbízható személyeknek kell megosztania a munkafüzetet.

Tegye fel kérdéseit a következő megjegyzésekkel foglalkozó szakaszban vagy a [fórumunkat](https://docs.microsoft.com/answers/topics/azure-machine-learning.html).
