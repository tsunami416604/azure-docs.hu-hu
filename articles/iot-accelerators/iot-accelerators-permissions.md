---
title: Használja az Azure IoT-megoldások hely – Azure |} A Microsoft Docs
description: Ismerteti, hogyan lehet a megoldásgyorsító üzembe helyezése a AzureIoTSolutions.com webhely használatával.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dobett
ms.openlocfilehash: 87f6b9cef50e4b8c388be835b2aa7bed8177ac4b
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53601083"
---
# <a name="use-the-azureiotsolutionscom-site-to-deploy-your-solution-accelerator"></a>A megoldásgyorsító üzembe helyezése a azureiotsolutions.com webhely használatával

Az Azure-előfizetéshez az Azure IoT-megoldásgyorsítók telepíthet [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators). AzureIoTSolutions.com üzemelteti a Microsoft nyílt forráskódú és a partner megoldásgyorsítók. A megoldásgyorsítók igazodva a [Azure IoT-Referenciaarchitektúra](https://aka.ms/iotrefarchitecture). A webhely segítségével gyorsan megoldásgyorsító üzembe helyezése egy bemutatót vagy az éles környezetet.

![AzureIoTSolutions.com](media/iot-accelerators-permissions/iotsolutionscom.png)

> [!TIP]
> Ha jobban szabályozhatja az üzembe helyezési folyamat van szüksége, használhatja a [megoldásgyorsító üzembe helyezése a CLI](iot-accelerators-remote-monitoring-deploy-cli.md).

A következő konfigurációk a megoldásgyorsítók helyezhető üzembe:

* **Standard szintű**: Egy bővített infrastruktúra üzembe éles környezet fejlesztéséhez. Az Azure Container Service üzembe helyezi a mikroszolgáltatásokat több Azure-beli virtuális gépek. A Kubernetes koordinálja az egyes mikroszolgáltatásokat üzemeltető Docker-tárolókat.
* **Alapszintű**: Egy alacsonyabb költségek a verzió bemutató célokra vagy a telepítés tesztelésére. Mindegyik mikroszolgáltatás üzembe helyezhető egy Azure-beli virtuális gépen.
* **Helyi**: Egy helyi számítógépre telepítése teszteléshez és fejlesztéshez. Ez a megközelítés egy helyi Docker-tárolót üzembe helyezi a mikroszolgáltatásokat, és csatlakozik az IoT Hub, az Azure Cosmos DB és az Azure storage-szolgáltatások a felhőben.

A megoldásgyorsítók mindegyike használ, például az IoT Hub, az Azure Stream Analytics és a Cosmos DB Azure-szolgáltatások különböző kombinációja. További információért látogasson el [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators) , és válassza ki a megoldásgyorsítók.

## <a name="sign-in-at-azureiotsolutionscom"></a>Jelentkezzen be a következő azureiotsolutions.com

A megoldásgyorsító központi telepítése, jelentkezzen be Azure-előfizetéssel társított hitelesítő adatok használatával AzureIoTSolutions.com. Ha a fiókja egynél több Microsoft Azure Active Directory (AD) bérlőhöz kapcsolódó hozzáférésiszabályzat, használhatja a **fiók kiválasztása legördülő** kiválasztása a könyvtárat.

A megoldásgyorsítók üzembe helyezése, felhasználók kezelése és kezelése az Azure-szolgáltatások függnek a szerepkör a kijelölt könyvtárban. A megoldásgyorsítók társított gyakori Azure AD-szerepkörök a következők:

* **Globális rendszergazda**: Is lehet számos [globális rendszergazdák](../active-directory/users-groups-roles/directory-assign-admin-roles.md) / Azure AD-bérlő:

  * Amikor létrehoz egy Azure AD-bérlővel, Ön alapértelmezés szerint a bérlő globális rendszergazdája.
  * A globális rendszergazda alapszintű és standard megoldásgyorsítók üzembe helyezhet.

* **Tartományi felhasználó**: Számos tartományi felhasználók Azure AD-bérlő kiszolgálónként lehet. Tartományi felhasználó telepítheti egy alapszintű megoldásgyorsító.

* **A vendégfelhasználók**: Egy Azure AD-bérlő számos vendég felhasználók is lehet. Vendégfelhasználók nem megoldásgyorsító üzembe helyezése az Azure AD-bérlőben.

Az Azure AD-felhasználók és szerepkörök kapcsolatos további információkért lásd a következőket:

* [Felhasználók létrehozása az Azure Active Directoryban](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
* [Felhasználók hozzárendelése alkalmazások](../active-directory/manage-apps/assign-user-or-group-access-portal.md)

## <a name="choose-your-device"></a>Válassza ki az eszközét

A AzureIoTSolutions.com webhelye hivatkozik a [Azure Certified for IoT eszközkatalógus](https://catalog.azureiotsolutions.com/).

A katalógus IoT-hardveres tanúsított eszközök saját megoldásgyorsítók az IoT-megoldás kiépítésének megkezdésére csatlakozhat több száz sorolja fel.

![Eszközkatalógus](media/iot-accelerators-permissions/devicecatalog.png)

Ha a hardver gyártójával, kattintson a **partnernek** megismerheti a Microsoft Certified for IoT programban a partneri.

## <a name="next-steps"></a>További lépések

Az IoT-megoldásgyorsítók valamelyikének kipróbálásához tekintse meg a következő rövid útmutatókat:

* [Távoli monitorozási megoldás kipróbálása](quickstart-remote-monitoring-deploy.md)
* [Csatlakoztatottgyár-megoldás kipróbálása](quickstart-connected-factory-deploy.md)
* [Prediktív karbantartási megoldás kipróbálása](quickstart-predictive-maintenance-deploy.md)
* [Eszközszimulációs megoldás kipróbálása](quickstart-device-simulation-deploy.md)
