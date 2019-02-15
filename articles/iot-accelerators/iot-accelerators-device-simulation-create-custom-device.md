---
title: Egyéni szimulált eszköz létrehozása – Azure | Microsoft Docs
description: Ebben az oktatóanyagban a szimulációkban való használathoz létrehozhat egy egyéni szimulált eszközt az Eszközszimuláció használatával.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/25/2018
ms.author: troyhop
ms.openlocfilehash: 302b863e7ad7d6df286adf53342356f279ab92d2
ms.sourcegitcommit: 3dcb1a3993e51963954194ba2a5e42260d0be258
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50756788"
---
# <a name="tutorial-create-a-custom-simulated-device"></a>Oktatóanyag: Egyéni szimulált eszköz létrehozása

Ebben az oktatóanyagban a szimulációkban való használathoz létrehozhat egy egyéni szimulált eszközt az Eszközszimuláció használatával. Az Eszközszimuláció megismeréséhez használhatja az egyik mellékelt szimulált mintaeszközt. Emellett egyéni szimulált eszközöket is létrehozhat a cikkben ismertetett módon. További testreszabási lehetőségekért tekintse meg a [speciális eszközmodell létrehozását](iot-accelerators-device-simulation-advanced-device.md) ismertető cikket.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

>[!div class="checklist"]
> * Szimulált eszközmodellek listájának megtekintése
> * Egyéni szimulált eszköz létrehozása
> * Eszközmodell klónozása
> * Eszközmodellek törlése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag követéséhez rendelkeznie kell az Eszközszimuláció egy üzembe helyezett példányával az Azure-előfizetésében.

Ha még nem helyezte üzembe az Eszközszimulációt, végezze el az [Azure-beli IoT-eszközszimuláció üzembe helyezését és futtatását](quickstart-device-simulation-deploy.md) ismertető rövid útmutatóban leírt lépéseket.

## <a name="open-device-simulation"></a>Eszközszimuláció megnyitása

Az Eszközszimuláció böngészőben történő futtatásához először lépjen a [Microsoft Azure IoT-megoldásgyorsítók](https://www.azureiotsolutions.com) területre.

A rendszer arra kérheti, hogy lépjen be Azure-előfizetésének hitelesítő adataival.

Ezután kattintson az **Indítás** elemre az [Azure-beli IoT-eszközszimuláció üzembe helyezését és futtatását](quickstart-device-simulation-deploy.md) ismertető rövid útmutató során üzembe helyezett Eszközszimuláció csempéjén.

## <a name="view-your-device-models"></a>Eszközmodellek megtekintése

A menüsávon válassza az **Eszközmodellek** gombot. Az **Eszközmodellek** lap felsorolja az Eszközszimuláció ezen példányában található összes elérhető eszközmodellt:

![Eszközmodellek](media/iot-accelerators-device-simulation-create-custom-device/devicemodelnav.png)

## <a name="create-a-device-model"></a>Eszközmodell létrehozása

Kattintson az **+ Eszközmodellek hozzáadása** elemre a lap jobb felső sarkában:

![Eszközmodell hozzáadása](media/iot-accelerators-device-simulation-create-custom-device/devicemodels.png)

Ebben az oktatóanyagban létrehozhat egy szimulált hűtőt, amely hőmérséklet- és páratartalom-adatokat küld.

Adja meg az alábbi adatokat az űrlapon:

| Beállítás             | Érték                                                |
| ------------------- | ---------------------------------------------------- |
| Eszközmodell neve   | Hűtő                                         |
| Modell leírása   | Hőmérséklet- és páratartalom-érzékelőkkel rendelkező hűtő |
| Verzió             | 1.0                                                  |

> [!NOTE]
> Az eszközmodell nevének egyedinek kell lennie.

Kattintson az **+ Adatpont hozzáadása** elemre a hőmérséklet- és páratartalom-adatpontok hozzáadásához az alábbi értékekkel:

| Adatpont          | Viselkedés        | Minimális érték | Maximális érték | Unit (Egység) |
| ------------------- | --------------- | --------- | --------- | ---- |
| Hőmérséklet         | Véletlenszerű          | -50       | 100       | F    |
| Páratartalom            | Véletlenszerű          | 0         | 100       | %    |

Kattintson a **Mentés** gombra az eszközmodell mentéséhez.

![Eszközmodell létrehozása](media/iot-accelerators-device-simulation-create-custom-device/adddevicemodel.png)

A hűtő ezzel szerepel az eszközmodellek listájában. Lehetséges, hogy a **Tovább** gombra kell kattintania, hogy egy másik lapra navigálva láthassa a hűtőt.

## <a name="clone-a-device-model"></a>Eszközmodell klónozása

Az eszközmodellek klónozásával létrehozhatja egy meglévő eszközmodell másolatát. Ezután az igényeinek megfelelően szerkesztheti a másolatot. A klónozással időt takaríthat meg, amikor hasonló eszközmodelleket kell létrehoznia.

Egy eszközmodell klónozásához jelölje be a modell mellett található jelölőnégyzetet, majd kattintson a **Klónozás** gombra a műveletsávon:

![Eszközmodell törlése](media/iot-accelerators-device-simulation-create-custom-device/clonedevice.png)

## <a name="delete-a-device-model"></a>Eszközmodellek törlése

Bármelyik egyéni eszközmodellt törölheti. Egy eszközmodell törléséhez jelölje be a modell mellett található jelölőnégyzetet, majd kattintson a **Törlés** gombra a műveletsávon:

![Eszközmodell törlése](media/iot-accelerators-device-simulation-create-custom-device/deletedevice.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerte, hogyan hozhat létre, klónozhat és törölhet egyéni eszközmodelleket. Az eszközmodellekkel kapcsolatos további információkért tekintse meg az alábbi útmutatót:

> [!div class="nextstepaction"]
> [Speciális eszközmodell létrehozása](iot-accelerators-device-simulation-advanced-device.md)