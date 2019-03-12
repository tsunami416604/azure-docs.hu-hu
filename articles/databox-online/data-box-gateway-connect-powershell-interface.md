---
title: Csatlakozhat, és kezelheti a Microsoft Azure Data Box Gateway eszköz a Windows PowerShell felületéről |} A Microsoft Docs
description: Ismerteti, hogyan csatlakozhat, és aztán kezelje a Data Box-átjáró a Windows PowerShell felületéről.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 0ca570235ac2a87b62c5d0fcebbd24dc5186e37d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57555107"
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

> [!IMPORTANT]
> Az Azure Data Box Gateway jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="connect-to-the-powershell-interface"></a>A PowerShell-felületünkön csatlakozni

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="start-a-support-session"></a>Indítson el egy támogatási munkamenetet

[!INCLUDE [Connect to support runspace](../../includes/data-box-edge-gateway-connect-support.md)]

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
