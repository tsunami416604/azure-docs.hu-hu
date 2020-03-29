---
title: Az Azure IoT Solutions webhely használata - Azure | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogy a AzureIoTSolutions.com webhely használatával hogyan telepítheti a megoldásgyorsítót.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dobett
ms.openlocfilehash: 87f6b9cef50e4b8c388be835b2aa7bed8177ac4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447454"
---
# <a name="use-the-azureiotsolutionscom-site-to-deploy-your-solution-accelerator"></a>A megoldásgyorsító üzembe helyezéséhez használja a azureiotsolutions.com helyet

Az Azure IoT-megoldásgyorsítók üzembe helyezhetők az Azure-előfizetésben [a AzureIoTSolutions.com.](https://www.azureiotsolutions.com/Accelerators) AzureIoTSolutions.com a Microsoft nyílt forráskódú és partnermegoldás-gyorsítóit is üzemelteti. Ezek a megoldásgyorsítók igazodnak az [Azure IoT referenciaarchitektúrához.](https://aka.ms/iotrefarchitecture) A hely segítségével gyorsan üzembe helyezhet egy megoldásgyorsítót demóként vagy éles környezetben.

![AzureIoTSolutions.com](media/iot-accelerators-permissions/iotsolutionscom.png)

> [!TIP]
> Ha több szabályszükséges a telepítési folyamat felett, a CLI segítségével [telepíthet egy megoldásgyorsítót.](iot-accelerators-remote-monitoring-deploy-cli.md)

A megoldásgyorsítók a következő konfigurációkban telepíthetők:

* **Standard**: Bővített infrastruktúra-telepítés éles környezet fejlesztéséhez. Az Azure Container Service több Azure virtuális gépre telepíti a mikroszolgáltatásokat. A Kubernetes koordinálja az egyes mikroszolgáltatásokat üzemeltető Docker-tárolókat.
* **Alapszintű**: Egy bemutató vagy egy központi telepítés teszteléséhez csökkentett költségű verzió. Mindegyik mikroszolgáltatás üzembe helyezhető egy Azure-beli virtuális gépen.
* **Helyi**: A helyi számítógép üzembe helyezése teszteléshez és fejlesztéshez. Ez a megközelítés telepíti a mikroszolgáltatásokat egy helyi Docker-tárolóba, és csatlakozik az IoT Hubhoz, az Azure Cosmos DB-hez és az Azure storage-szolgáltatásokhoz a felhőben.

A megoldásgyorsítók mindegyike az Azure-szolgáltatások, például az IoT Hub, az Azure Stream Analytics és a Cosmos DB különböző kombinációját használja. További információkért látogasson el [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators) és válasszon egy megoldásgyorsítót.

## <a name="sign-in-at-azureiotsolutionscom"></a>Bejelentkezés a azureiotsolutions.com

Megoldásgyorsító üzembe helyezése előtt be kell jelentkeznie AzureIoTSolutions.com egy Azure-előfizetéshez társított hitelesítő adatok használatával. Ha a fiók egynél több Microsoft Azure Active Directory (AD) bérlőhöz van társítva, a **Fiók kiválasztása legördülő menüsegítségével** kiválaszthatja a használni kívánt könyvtárat.

A megoldásgyorsítók üzembe helyezésére, a felhasználók kezelésére és az Azure-szolgáltatások kezelésére vonatkozó engedélyei a kiválasztott címtárban betöltött szerepétől függenek. A megoldásgyorsítókhoz társított gyakori Azure AD-szerepkörök a következők:

* **Globális rendszergazda:** Azure AD-bérlőnként több [globális rendszergazda](../active-directory/users-groups-roles/directory-assign-admin-roles.md) is lehet:

  * Amikor létrehoz egy Azure AD-bérlőt, alapértelmezés szerint az adott bérlő globális rendszergazdája.
  * A globális rendszergazda alapszintű és szabványos megoldásgyorsítókat telepíthet.

* **Tartományi felhasználó:** Azure AD-bérlőnként több tartományi felhasználó is lehet. A tartományi felhasználók üzembe helyezhetnek egy alapszintű megoldásgyorsítót.

* **Vendégfelhasználó:** Azure AD-bérlőnként több vendégfelhasználó is lehet. A vendégfelhasználók nem helyezhetnek üzembe megoldásgyorsítót az Azure AD-bérlőben.

Az Azure AD felhasználóiról és szerepköreiről az alábbi forrásokban talál további információt:

* [Felhasználók létrehozása az Azure Active Directoryban](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
* [Felhasználók hozzárendelése alkalmazásokhoz](../active-directory/manage-apps/assign-user-or-group-access-portal.md)

## <a name="choose-your-device"></a>Válassza ki a készüléket

A AzureIoTSolutions.com helyaz [Azure Certified for IoT eszközkatalógusra](https://catalog.azureiotsolutions.com/)mutató hivatkozásokat tartalmaz.

A katalógus több száz hitelesített IoT-hardvereszközt sorol fel, amelyekhez csatlakozhat a megoldásgyorsítókhoz az IoT-megoldás létrehozásához.

![Eszközkatalógus](media/iot-accelerators-permissions/devicecatalog.png)

Ha Ön hardvergyártó, kattintson a **Partnerré válás** lehetőségre, ha megszeretné tudni, hogy miként lehet partneri kapcsolatban dolgozni a Microsofttal a Certified for IoT programban.

## <a name="next-steps"></a>További lépések

Az IoT-megoldásgyorsítók valamelyikének kipróbálásához tekintse meg a következő rövid útmutatókat:

* [Távoli monitorozási megoldás kipróbálása](quickstart-remote-monitoring-deploy.md)
* [Csatlakoztatottgyár-megoldás kipróbálása](quickstart-connected-factory-deploy.md)
* [Prediktív karbantartási megoldás kipróbálása](quickstart-predictive-maintenance-deploy.md)
* [Eszközszimulációs megoldás kipróbálása](quickstart-device-simulation-deploy.md)
