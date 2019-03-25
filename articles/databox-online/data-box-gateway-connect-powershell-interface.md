---
title: Csatlakozhat, és kezelheti a Microsoft Azure Data Box Gateway eszköz a Windows PowerShell felületéről |} A Microsoft Docs
description: Ismerteti, hogyan csatlakozhat, és aztán kezelje a Data Box-átjáró a Windows PowerShell felületéről.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/21/2019
ms.author: alkohli
ms.openlocfilehash: 67caaa2c6c9bd615d0b88bdd5de3442b46aa32cb
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403493"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Kezelheti az Azure Data Box Gateway eszköz Windows PowerShell-lel

Azure Data Box-Gateway-megoldás lehetővé teszi adatok küldése a hálózaton keresztül az Azure-bA. Ez a cikk ismerteti a Data Box-Gateway-eszköz konfigurációs és felügyeleti feladatokat. Használhatja az Azure Portalon, helyi webes felhasználói felületen vagy a Windows PowerShell felületet az eszköz kezelését.

Ez a cikk foglalkozik a feladatok végrehajtásához a PowerShell felület használatával.

Ez a cikk a következő eljárásokat tartalmazza:

- A PowerShell-felületünkön csatlakozni
- Indítson el egy támogatási munkamenetet
- Hozzon létre egy támogatási csomagot
- Tanúsítvány feltöltése
- A-DHCP környezetben indul el
- Eszköz adatainak megtekintése

## <a name="connect-to-the-powershell-interface"></a>A PowerShell-felületünkön csatlakozni

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Hozzon létre egy támogatási csomagot

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Tanúsítvány feltöltése

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>A-DHCP környezetben indul el

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>Eszköz adatainak megtekintése

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>További lépések

- Az [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) üzembe helyezése az Azure Portalon.
