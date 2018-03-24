---
title: Excel-bővítmény a Machine Learning webszolgáltatások |} Microsoft Docs
description: Hogyan használható az Azure Machine Learning webszolgáltatások közvetlenül az Excel programban programozás nélkül.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 9618079d-502f-4974-a3e2-8f924042a23f
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 2/1/2018
ms.openlocfilehash: 6610777ff4ad3a04f9d0d5b47f402aea7db59d9b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="excel-add-in-for-azure-machine-learning-web-services"></a>Excel-bővítmény az Azure Machine Learning webszolgáltatásokhoz
Excel megkönnyíti a webszolgáltatások közvetlenül a szükséges kód írása nélkül.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>A munkafüzet egy meglévő webes szolgáltatás használatának lépéseit

1. Nyissa meg a [minta Excel-fájl](http://aka.ms/amlexcel-sample-2), amely Excel-bővítmény és a Titanic utasok adatait tartalmazza.
2. Válassza ki a webszolgáltatás rákattintva-"Titanic túlélő Előrejelzőjének (Excel-bővítmény minta) [Pontszám]" Ebben a példában.
   
    ![Válassza ki a webszolgáltatás][01]
3. Ehhez szükséges, hogy a **Predict** szakasz.  Ez a munkafüzet már tartalmaz adatot, de üres a munkafüzet az Excel programban a cella kijelöléséhez és kattintson a **mintaadatokkal**.
4. Válassza ki az adatokat a fejlécek és a bemeneti adatok tartomány ikonra.  Győződjön meg arról, hogy a "adataimat fejlécekkel rendelkezik" jelölőnégyzet be van jelölve.
5. A **kimeneti**, adja meg a cella számát, ahol azt szeretné, hogy a kimenet, például "H1" Itt.
6. Kattintson a **előrejelzése**.
   
    ![A szakasz előrejelzése][02]

A webes szolgáltatás, vagy használja a meglévő webszolgáltatás. Egy webszolgáltatás-bővítmény telepítésével kapcsolatos további információkért lásd: [útmutató 5. lépés: központi telepítése az Azure Machine Learning Web service](walkthrough-5-publish-web-service.md).

A webszolgáltatáshoz API-kulcs beszerzése. Ahol elvégezhető ez a művelet függ a klasszikus Machine Learning webszolgáltatás egy új Machine Learning-webszolgáltatás közzététele e.

**A klasszikus webszolgáltatás** 

1. A Machine Learning Studióban, kattintson a **WEBSZOLGÁLTATÁSOK** szakaszt a bal oldali ablaktáblán, és válassza ki a webszolgáltatás.
   
    ![Studio válasszon egy webszolgáltatás-bővítmény][04]
2. Másolja az API-kulcsot a webszolgáltatáshoz.
   
    ![Studio API-kulcs][05]
3. Az a **IRÁNYÍTÓPULT** a webszolgáltatás lapra, majd a **kérelem/válasz** hivatkozásra.
4. Keresse meg a **kérelem URI-azonosítója** szakasz.  Másolja ki és mentse az URL-címet.

> [!NOTE]
> Már lehetséges a jelentkezzen be a [Azure Machine Learning webszolgáltatások](https://services.azureml.net) portálra, ahol az adott klasszikus Machine Learning webszolgáltatás API-kulcs beszerzése.
> 
> 

**Új webszolgáltatás**

1. Az a [Azure Machine Learning webszolgáltatások](https://services.azureml.net) portálon kattintson **webszolgáltatások**, majd válassza a webes szolgáltatás. 
2. Kattintson a **felhasználásához**.
3. Keresse meg a **alapvető fogyasztási adatai** szakasz. Másolja ki és mentse a **elsődleges kulcs** és a **kérés-válasz** URL-CÍMÉT.

## <a name="steps-to-add-a-new-web-service"></a>Lépések végrehajtásával adja hozzá egy új webszolgáltatás-bővítmény

1. A webes szolgáltatás, vagy használja a meglévő webszolgáltatás. Egy webszolgáltatás-bővítmény telepítésével kapcsolatos további információkért lásd: [útmutató 5. lépés: központi telepítése az Azure Machine Learning Web service](walkthrough-5-publish-web-service.md).
2. Kattintson a **felhasználásához**.
3. Keresse meg a **alapvető fogyasztási adatai** szakasz. Másolja ki és mentse a **elsődleges kulcs** és a **kérés-válasz** URL-CÍMÉT.
4. Az Excel programban, lépjen a **webszolgáltatások** szakasz (Ha a **Predict** területen kattintson a Vissza gombra kattintva a webes szolgáltatások listáját).
   
    ![Ugrás a webes szolgáltatás kiválasztása][03]
5. Kattintson a **webszolgáltatás hozzáadása**.
6. Az URL-cím illessze be az Excel-bővítmény szövegmező feliratú **URL-cím**.
7. Az API/elsődleges kulcs illessze be a mezőbe **API-kulcs**.
8. Kattintson a **Hozzáadás** parancsra.
   
    ![URL-CÍMÉT és API-kulcsát egy klasszikus webszolgáltatáshoz.][06]
9. A webszolgáltatás hajtsa végre az előző szakasz utasításait, "Lépéseket egy meglévő webes szolgáltatás használatára."

## <a name="sharing-your-workbook"></a>A munkafüzet megosztása
Mentse a munkafüzetet, ha a hozzáadott webszolgáltatások API/elsődleges kulcsa is menti. Ez azt jelenti, hogy csak ossza meg a munkafüzetet megbízható személlyel.

Kérje meg a következő szakaszban megjegyzés vagy a kérdéseket a [fórum](http://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).

[01]: ./media/excel-add-in-for-web-services/image1.png
[02]: ./media/excel-add-in-for-web-services/image2.png
[03]: ./media/excel-add-in-for-web-services/image3.png
[04]: ./media/excel-add-in-for-web-services/image4.png
[05]: ./media/excel-add-in-for-web-services/image5.png
[06]: ./media/excel-add-in-for-web-services/image6.png
