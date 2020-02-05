---
title: Azure IoT Central-alkalmazás kezelése | Microsoft Docs
description: Rendszergazdaként az alkalmazás nevének, URL-címének, feltöltésének, másolásának és törlésének megváltoztatásával hogyan kezelheti Azure IoT Central alkalmazását.
author: viv-liu
ms.author: viviali
ms.date: 08/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 89c4dd294cbbf0953545e1055e32adfc5f7cce28
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990793"
---
# <a name="manage-your-iot-central-application"></a>IoT Central alkalmazás kezelése

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Ez a cikk azt ismerteti, hogyan kezelheti az alkalmazást rendszergazdaként az alkalmazás nevének és URL-címének módosításával, valamint a rendszerkép feltöltésével, valamint megtudhatja, hogyan másolhat és törölhet egy alkalmazást az Azure IoT Central alkalmazásban.

Az **Adminisztráció** szakasz eléréséhez és használatához **rendszergazdai** szerepkörrel kell rendelkeznie egy Azure IoT Central-alkalmazáshoz. Ha létrehoz egy Azure IoT Central alkalmazást, a rendszer automatikusan hozzárendeli az adott alkalmazáshoz tartozó **rendszergazdai** szerepkörhöz. 

## <a name="change-application-name-and-url"></a>Alkalmazás nevének és URL-címének módosítása

Az **Alkalmazásbeállítások** lapon módosíthatja az alkalmazás nevét és URL-címét, majd válassza a **Mentés**lehetőséget.

![Alkalmazásbeállítások lap](media/howto-administer/image0-a.png)

Ha a rendszergazda egyéni témát hoz létre az alkalmazáshoz, ez a lap tartalmaz egy lehetőséget az **alkalmazás nevének** elrejtésére a felhasználói felületen. Ez akkor hasznos, ha az egyéni téma alkalmazásának emblémája tartalmazza az alkalmazás nevét. További információ: [Az Azure IoT Central felhasználói felületének testreszabása](./howto-customize-ui.md).

> [!Note]
> Ha megváltoztatja az URL-címet, a régi URL-címet egy másik Azure IoT Central ügyfél is elvégezheti. Ha ez történik, már nem használható. Ha megváltoztatja az URL-címet, a régi URL-cím már nem működik, és értesítenie kell a felhasználókat a használni kívánt új URL-címről.

## <a name="prepare-and-upload-image"></a>Kép előkészítése és feltöltése

Az alkalmazás lemezképének módosításához lásd: [lemezképek előkészítése és feltöltése az Azure IoT Central alkalmazásba](howto-prepare-images.md).

## <a name="copy-an-application"></a>Alkalmazás másolása

Létrehozhat egy másolatot bármely alkalmazásról, levonva az eszköz példányait, az eszköz adatelőzményeit és a felhasználói adatmennyiségeket. A másolás egy standard díjszabási csomagot használ, amelyért díjat kell fizetnie. Így nem hozhat létre olyan alkalmazást, amely az ingyenes díjszabási csomagot használja.

Válassza a **Másolás**lehetőséget. A párbeszédpanelen adja meg az új alkalmazás részleteit. Ezután a **Másolás** gombra kattintva erősítse meg, hogy folytatni kívánja. További információ az űrlap mezőiről az [alkalmazás létrehozása](quick-deploy-iot-central.md) rövid útmutatóban.

![Alkalmazásbeállítások lap](media/howto-administer/appcopy2.png)

Az alkalmazás másolási műveletének sikerességét követően az új alkalmazásra kattintva megnyithatja a hivatkozást.

![Alkalmazásbeállítások lap](media/howto-administer/appcopy3a.png)

Az alkalmazások másolása a szabályok és az e-mail művelet definícióját is másolja. Egyes műveletek, például a flow, a Logic Apps stb. a szabály-AZONOSÍTÓn keresztül vannak társítva meghatározott szabályokhoz. Ha egy szabályt egy másik alkalmazásba másolnak, az a saját szabály AZONOSÍTÓját kapja meg. Ebben az esetben a felhasználóknak új műveletet kell létrehozniuk, majd hozzá kell rendelniük az új szabályt. Általában érdemes ellenőrizni a szabályokat és a műveleteket, hogy biztosan naprakészek legyenek az új alkalmazásban.

> [!WARNING]
> Ha az irányítópult olyan csempéket tartalmaz, amelyek adott eszközökre vonatkozó információkat jelenítenek meg, akkor ezek a csempék **a kért erőforrást nem találhatók** az új alkalmazásban. Ezeket a csempéket újra kell konfigurálnia az új alkalmazás eszközeivel kapcsolatos információk megjelenítéséhez.

## <a name="delete-an-application"></a>Alkalmazás törlése

Az IoT Central alkalmazás végleges törléséhez használja a **delete (Törlés** ) gombot. Ez a művelet véglegesen törli az alkalmazáshoz társított összes adatmennyiséget.

> [!Note]
> Egy alkalmazás törléséhez az alkalmazás létrehozásakor kiválasztott Azure-előfizetésben is rendelkeznie kell az erőforrások törléséhez szükséges engedélyekkel. További információ: [szerepköralapú hozzáférés-vezérlés használata az Azure-előfizetések erőforrásaihoz való hozzáférés kezeléséhez](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).


## <a name="manage-programatically"></a>Programozott módon kezelése

IoT Central Azure Resource Manager SDK-csomagok a Node, a Python, C#a Ruby, a Java és a go esetében érhetők el. Ezeket a csomagokat IoT Central-alkalmazások létrehozásához, listázásához, frissítéséhez vagy törléséhez használhatja. A csomagok közé tartoznak a hitelesítés és a hibakezelés kezeléséhez szükséges segítők.

Példákat talál arra, hogyan használhatja a Azure Resource Manager SDK-kat a [https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples).

További információért lásd a következő GitHub-adattárakat és-csomagokat:

| Nyelv | Tárház | Csomag |
| ---------| ---------- | ------- |
| Csomópont | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Indítás | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Következő lépések
 
Most, hogy megismerte az Azure IoT Central-alkalmazás felügyeletének módját, a javasolt következő lépés a [felhasználók és szerepkörök kezelése](howto-manage-users-roles.md) az Azure IoT Central-ban című témakörben.