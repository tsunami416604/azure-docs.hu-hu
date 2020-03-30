---
title: Az Azure IoT Central alkalmazásbeállításainak módosítása | Microsoft dokumentumok
description: Rendszergazdaként az Azure IoT Central alkalmazás kezelése alkalmazásnév, URL-cím módosításával, kép feltöltésével és alkalmazás törlésével
author: viv-liu
ms.author: viviali
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 7b77ea9e0e1f322cb5ef0bc63885c3ccce1b76f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158681"
---
# <a name="change-iot-central-application-settings"></a>IoT Central alkalmazásbeállításainak módosítása



Ez a cikk ismerteti, hogyan, rendszergazdaként kezelheti az alkalmazás nevét és URL-címét, a kép feltöltése, és egy alkalmazás törlése az Azure IoT Central-alkalmazásban.

A **felügyeleti** szakasz eléréséhez és használatához egy Azure IoT Central alkalmazás **rendszergazdai** szerepkörben kell lennie. Ha létrehoz egy Azure IoT Central-alkalmazást, a rendszer automatikusan hozzárendeli az adott alkalmazás **rendszergazdai** szerepköréhez.

## <a name="change-application-name-and-url"></a>Alkalmazás névnek és URL-címnek módosítása

Az **Alkalmazásbeállítások** lapon módosíthatja az alkalmazás nevét és URL-címét, majd válassza a **Mentés lehetőséget.**

![Alkalmazásbeállítások lap](media/howto-administer/image0-a.png)

Ha a rendszergazda egyéni témát hoz létre az alkalmazáshoz, ez a lap tartalmaz egy lehetőséget az **alkalmazásnevének** elrejtésére a felhasználói felületen. Ez a beállítás akkor hasznos, ha az egyéni téma alkalmazásemblémája tartalmazza az alkalmazás nevét. További információ: [Az Azure IoT központi felhasználói felületének testreszabása.](./howto-customize-ui.md)

> [!Note]
> Ha módosítja az URL-címet, a régi URL-t egy másik Azure IoT Central-ügyfél is megveheti. Ha ez megtörténik, akkor már nem használható. Az URL-cím módosításakor a régi URL-cím már nem működik, és értesítenie kell a felhasználókat a használandó új URL-címről.

## <a name="delete-an-application"></a>Alkalmazás törlése

A **Törlés** gombbal véglegesen törölheti az IoT Central alkalmazást. Ez a művelet véglegesen törli az alkalmazáshoz társított összes adatot.

> [!Note]
> Egy alkalmazás törléséhez is rendelkeznie kell engedélyekkel az azure-előfizetésben kiválasztott erőforrások törléséhez, amikor létrehozta az alkalmazást. További információ: [Szerepköralapú hozzáférés-vezérlés használata az Azure-előfizetési erőforrásokhoz való hozzáférés kezeléséhez.](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)

## <a name="manage-programmatically"></a>Programozott kezelés

Az IoT Central Azure Resource Manager SDK-csomagok node, Python, C#, Ruby, Java és Go esetén érhetők el. Ezekkel a csomagokkal hozhat létre, listázhat, frissíthet vagy törölhet IoT Central-alkalmazásokat. A csomagok a hitelesítés és a hibakezelés kezeléséhez segítőket tartalmaznak.

Az Azure Resource Manager SDK-k használatára [https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples)vonatkozó példákat a.

További információ: a következő GitHub-adattárak és -csomagok:

| Nyelv | Adattár | Csomag |
| ---------| ---------- | ------- |
| Csomópont | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Indítás | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>További lépések

Most, hogy megismerkedett az Azure IoT Central alkalmazás felügyeletével, a javasolt következő lépés a [Felhasználók és szerepkörök kezelése](howto-manage-users-roles.md) az Azure IoT Centralban.
