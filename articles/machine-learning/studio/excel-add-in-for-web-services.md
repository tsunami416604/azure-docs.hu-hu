---
title: Excel-bővítmény a Machine Learning webszolgáltatások |} A Microsoft Docs
description: Hogyan használható az Azure Machine Learning Web services közvetlenül az Excelben kód írása nélkül.
services: machine-learning
documentationcenter: ''
author: marthalc
ms.author: marthalc
ms.assetid: 9618079d-502f-4974-a3e2-8f924042a23f
ms.service: machine-learning
ms.component: studio
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 2/1/2018
ms.openlocfilehash: ed3fc6d1d2a4b674e2866d2e168fb72490575869
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51281833"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-web-services"></a>Excel-bővítmény az Azure Machine Learning Studio-webszolgáltatások
Az Excel megkönnyíti a webszolgáltatások közvetlenül kód írása nélkül.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Egy meglévő webes szolgáltatás használatához a munkafüzetben lévő lépéseket

1. Nyissa meg a [minta Excel-fájl](https://aka.ms/amlexcel-sample-2), amely Excel-bővítmény és a titanic Katasztrófáját az utasok adatait tartalmazza. 
 
> [!NOTE]
> Látni fogja a webszolgáltatások listája kapcsolódó a fájl és a lap alján egy jelölőnégyzetet az "Automatikus előrejelzése". Ha engedélyezi az automatikus – előre jelezni az előrejelzések **összes** minden alkalommal, amikor megváltozik a bemeneti adatok az frissíti a szolgáltatásokat. Ha nincs bejelölve, kell kattintson "Az összes előrejelzése" a frissítéshez. Engedélyezésének automatikus – előrejelzésére egy szolgáltatási szint lépjen a 6. lépés:.

2. Válassza ki a web service rákattintva – "Titanic túlélő előjelző (Excel-bővítményben minta) [Pontszám]" Ebben a példában.
   
    ![Válassza ki a webszolgáltatás][01]
3. Ezzel továbblép a **Predict** szakaszban.  Ez a munkafüzet már mintaadatokat tartalmaz, de egy üres munkafüzetet az Excelben jelöljön ki egy cellát és kattintson a **használja a mintaadatok**.
4. Válassza ki az adatokat a fejlécek, és kattintson a bemeneti adatok tartomány ikonra.  Győződjön meg arról, hogy az "adatok fejléccel rendelkeznek" jelölőnégyzet be van jelölve.
5. A **kimeneti**, adja meg a cella számát, ha azt szeretné, hogy a kimenetet, például "H1" Itt meg.
6. Kattintson a **előrejelzése**. Ha az "auto-predict" jelölőnégyzet bejelölésével bármilyen módosítás a kiválasztott területeken (a bemenetként megadott változóneveket) aktivál egy kérelmet, és a egy frissítést, a kimeneti cellák anélkül, hogy a predict gombra.
   
    ![A szakasz előrejelzése][02]

Webszolgáltatás üzembe helyezése, vagy egy meglévő webes szolgáltatás használatához. A web Service szolgáltatásának telepítése További információkért lásd: [útmutató 5. lépés: az Azure Machine Learning Web service telepítése](walkthrough-5-publish-web-service.md).

A webszolgáltatás API-kulcs beszerzése. Hajt végre, ha ez a művelet attól függ, egy új Machine Learning webszolgáltatás klasszikus Machine Learning webszolgáltatás közzététele e.

**Klasszikus webszolgáltatás használata** 

1. A Machine Learning Studióban, kattintson a **WEBSZOLGÁLTATÁSOK** szakaszt a bal oldali panelen, és válassza ki a web service.
   
    ![Studio válassza egy webszolgáltatás][04]
2. A webszolgáltatás API-kulcs másolása.
   
    ![Studio API-kulcs][05]
3. Az a **IRÁNYÍTÓPULT** a webszolgáltatás lapra, majd a **KÉRÉS/válasz** hivatkozásra.
4. Keresse meg a **Request URI** szakaszban.  Másolja és mentse az URL-címet.

> [!NOTE]
> Már lehetséges a jelentkezzen be a [Azure Machine Learning webszolgáltatások](https://services.azureml.net) portal a klasszikus Machine Learning webszolgáltatás API-kulcs beszerzése.
> 
> 

**Új webszolgáltatás használata**

1. Az a [Azure Machine Learning webszolgáltatások](https://services.azureml.net) portálon kattintson **webszolgáltatások**, majd válassza ki a webszolgáltatást. 
2. Kattintson a **felhasználása**.
3. Keresse meg a **alapvető fogyasztási adatai** szakaszban. Másolja ki és mentse a **elsődleges kulcs** és a **kérés-válasz** URL-CÍMÉT.

## <a name="steps-to-add-a-new-web-service"></a>Új webes szolgáltatás hozzáadásának lépéseit

1. Webszolgáltatás üzembe helyezése, vagy egy meglévő webes szolgáltatás használatához. A web Service szolgáltatásának telepítése További információkért lásd: [útmutató 5. lépés: az Azure Machine Learning Web service telepítése](walkthrough-5-publish-web-service.md).
2. Kattintson a **felhasználása**.
3. Keresse meg a **alapvető fogyasztási adatai** szakaszban. Másolja ki és mentse a **elsődleges kulcs** és a **kérés-válasz** URL-CÍMÉT.
4. Az Excelben, nyissa meg a **webszolgáltatások** szakaszban (Ha a **Predict** területén kattintson a vissza nyílra, hogy nyissa meg a webes szolgáltatások listája).
   
    ![Ugrás a webes szolgáltatás kiválasztása][03]
5. Kattintson a **webszolgáltatás hozzáadása**.
6. Illessze be az URL-címet az Excel-bővítmény szövegmező feliratú **URL-cím**.
7. Illessze be az API/elsődleges kulcs szövegmező **API-kulcs**.
8. Kattintson a **Hozzáadás** parancsra.
   
    ![Klasszikus webszolgáltatás URL-CÍMÉT és API-t kulcs.][06]
9. A web service használatához kövesse az előző ismertető cikk utasításait, "Lépéseket egy meglévő webes szolgáltatás használatára."

## <a name="sharing-your-workbook"></a>A munkafüzet megosztása
Ha mentette a munkafüzetet, a webszolgáltatások hozzáadta az API/elsődleges kulcs is menti. Azt jelenti, hogy a munkafüzet kell csak megosztása, megbízható személlyel.

Kérje meg a következő megjegyzéseket tartalmazó részében vagy a felmerülő kérdéseket a [fórum](https://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).

[01]: ./media/excel-add-in-for-web-services/image1.png
[02]: ./media/excel-add-in-for-web-services/image2.png
[03]: ./media/excel-add-in-for-web-services/image3.png
[04]: ./media/excel-add-in-for-web-services/image4.png
[05]: ./media/excel-add-in-for-web-services/image5.png
[06]: ./media/excel-add-in-for-web-services/image6.png
