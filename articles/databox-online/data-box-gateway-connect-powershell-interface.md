---
title: Azure Data Box Gateway eszköz csatlakoztatása és kezelése a Windows PowerShell használatával
description: Ismerteti, hogyan csatlakozhat a szolgáltatáshoz, majd hogyan felügyelheti Data Box Gateway a Windows PowerShell felületén keresztül.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 08/02/2019
ms.author: alkohli
ms.openlocfilehash: c071d372ba90d29806fd8a44909e2c803a8d3fa4
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84339280"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Azure Data Box Gateway-eszköz kezelése a Windows PowerShell használatával

Azure Data Box Gateway megoldás lehetővé teszi az adatküldés hálózaton keresztül az Azure-ba. Ez a cikk a Data Box Gateway eszköz konfigurációs és felügyeleti feladatait ismerteti. Az eszköz kezeléséhez használhatja a Azure Portal, a helyi webes felhasználói felületet vagy a Windows PowerShell felületét is.

Ez a cikk a PowerShell-felületet használó feladatokra összpontosít.

Ez a cikk az alábbi eljárásokat tartalmazza:

- Kapcsolódás a PowerShell felületéhez
- Támogatási csomag létrehozása
- Tanúsítvány feltöltése
- Rendszerindítás nem DHCP-környezetben
- Eszköz adatainak megtekintése

## <a name="connect-to-the-powershell-interface"></a>Kapcsolódás a PowerShell felületéhez

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Támogatási csomag létrehozása

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Tanúsítvány feltöltése

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>Rendszerindítás nem DHCP-környezetben

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>Eszköz adatainak megtekintése

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>Következő lépések

- Az [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) üzembe helyezése az Azure Portalon.
