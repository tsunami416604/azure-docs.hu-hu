---
title: Excel-bővítmény a webszolgáltatásokhoz
titleSuffix: ML Studio (classic) - Azure
description: Az Azure Machine Learning webszolgáltatások használata közvetlenül az Excelben kód írása nélkül.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: e30103589c1baf9a165839cd041ff511a119c5ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204375"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-classic-web-services"></a>Excel-bővítmény az Azure Machine Learning Studio (klasszikus) webes szolgáltatásaihoz

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
Az Excel megkönnyíti a webes szolgáltatások közvetlen hívását anélkül, hogy bármilyen kódot kellene írnia.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Meglévő webszolgáltatás használatának lépései a munkafüzetben

1. Nyissa meg a [minta Excel-fájlt](https://aka.ms/amlexcel-sample-2), amely az Excel bővítményt és a Titanic utasaira vonatkozó adatokat tartalmazza. 
 
    > [!NOTE]
    > Az "Automatikus előrejelzés" jelölőnégyzet alján megjelenik a fájlhoz kapcsolódó webszolgáltatások listája. Ha engedélyezi az automatikus előrejelzést, az **összes** szolgáltatás előrejelzései minden alkalommal frissülnek, amikor változás történik a bemeneteken. Ha nincs bejelölve, akkor kattintson a "Megjósolni az összes" a frissítés. Az automatikus előrejelzés szolgáltatásszinten történő engedélyezéséhez lépjen a 6.

2. Válassza ki a webszolgáltatást, kattintson rá - "Titanic Survivor Predictor (Excel bővítmény minta) [Score]" ebben a példában.
   
    ![Webszolgáltatás kiválasztása](./media/excel-add-in-for-web-services/image1.png)
3. Ezzel a **Tippelés** szakaszra kerül.  Ez a munkafüzet már tartalmaz mintaadatokat, de egy üres munkafüzet esetében kijelölhet egy cellát az Excelben, és kattintson **a Mintaadatok használata**gombra .
4. Jelölje ki a fejléceket készítő adatokat, és kattintson a bemeneti adattartomány ikonra.  Győződjön meg arról, hogy az "Adataim fejlécekkel rendelkeznek" jelölőnégyzet be van jelölve.
5. A **Kimenet csoportban**adja meg azt a cellaszámot, ahol a kimenetet látni szeretné, például "H1" itt.
6. Kattintson **a Tippelés gombra.** Ha bejelöli az "automatikus előrejelzés" jelölőnégyzetet, a kiválasztott területeken (a bemenetként megadottakon) bekövetkező bármely változás kérést és a kimeneti cellák frissítését indítja el anélkül, hogy meg kellene nyomnia a prediktív gombot.
   
    ![Szakasz előrejelzése](./media/excel-add-in-for-web-services/image1.png)

Webszolgáltatás telepítése vagy meglévő webszolgáltatás használata. A webszolgáltatások üzembe helyezéséről a [3.](tutorial-part3-credit-risk-deploy.md)

A webszolgáltatás API-kulcsának beszereznie. A művelet végrehajtásának helye attól függ, hogy közzétett-e egy Classic Machine Learning webszolgáltatást egy Új Machine Learning-webszolgáltatáshoz.

**Klasszikus webszolgáltatás használata** 

1. A Machine Learning Studio (klasszikus) alkalmazásban kattintson a **webszolgáltatások** szakaszra a bal oldali ablaktáblában, majd válassza ki a webszolgáltatást.
   
    ![A Stúdió webes szolgáltatást választ](./media/excel-add-in-for-web-services/image4.png)
2. Másolja a webszolgáltatás API-kulcsát.
   
    ![Studio API-kulcs](./media/excel-add-in-for-web-services/image5.png)
3. A webszolgáltatás **IRÁNYÍTÓPULT** lapján kattintson a **KÉRELEM/VÁLASZ** hivatkozásra.
4. Keresse meg az **URI kérése** szakaszt.  Másolja és mentse az URL-címet.

> [!NOTE]
> Most már be is jelentkezz az [Azure Machine Learning Web Services](https://services.azureml.net) portálra, és beszerezheti a Klasszikus Machine Learning webszolgáltatás API-kulcsát.
> 
> 

**Új webszolgáltatás használata**

1. Az [Azure Machine Learning Web Services](https://services.azureml.net) portálon kattintson a **Webservices**elemre, majd válassza ki a webszolgáltatást. 
2. Kattintson **a Felhasználás gombra.**
3. Keresse meg az **Alapszintű felhasználási adatok szakaszt.** Másolja és mentse az **elsődleges kulcsot** és a **kérelem-válasz URL-címet.**

## <a name="steps-to-add-a-new-web-service"></a>Új webszolgáltatás hozzáadásának lépései

1. Webszolgáltatás telepítése vagy meglévő webszolgáltatás használata. A webszolgáltatások üzembe helyezéséről a [3.](tutorial-part3-credit-risk-deploy.md)
2. Kattintson **a Felhasználás gombra.**
3. Keresse meg az **Alapszintű felhasználási adatok szakaszt.** Másolja és mentse az **elsődleges kulcsot** és a **kérelem-válasz URL-címet.**
4. Az Excelben nyissa meg a **Webservices** szakaszt (ha a **Prediktrálás** szakaszban van, kattintson a vissza nyílra a webszolgáltatások listájának megugrásához).
   
    ![Ugrás a webszolgáltatás kijelölésére](./media/excel-add-in-for-web-services/image3.png)
5. Kattintson **a Webszolgáltatás hozzáadása gombra.**
6. Illessze be az URL-címet az **URL**Excel url-című szövegmezőjébe.
7. Illessze be az API/Elsődleges kulcsot a szövegdobozba, amelyet **API-kulcs nak címkézett.**
8. Kattintson a **Hozzáadás** gombra.
   
    ![Egy klasszikus webszolgáltatás URL- és API-kulcsa.](./media/excel-add-in-for-web-services/image6.png)
9. A webszolgáltatás használatához kövesse az előző utasításokat, "Egy meglévő webszolgáltatás használatának lépései" című utasításokat.

## <a name="sharing-your-workbook"></a>A munkafüzet megosztása
Ha menti a munkafüzetet, akkor a hozzáadott webszolgáltatások API/elsődleges kulcsa is mentésre kerül. Ez azt jelenti, hogy a munkafüzetet csak megbízható személyekkel kell megosztania.

Tegyen fel kérdéseket a következő megjegyzés rovatban, vagy a [fórumon](https://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).
