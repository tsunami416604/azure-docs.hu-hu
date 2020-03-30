---
title: Csatlakozás a Microsoft Azure Data Box Edge eszközhöz és annak kezelése a Windows PowerShell felületen keresztül | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogy miként csatlakozhat a Data Box Edge-hez, majd hogyan kezelhető a Windows PowerShell-felületen keresztül.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/25/2019
ms.author: alkohli
ms.openlocfilehash: f49396331a31f7ca9eaf453dc8bf6880da2e0da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265479"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell"></a>Azure Data Box Edge-eszköz kezelése a Windows PowerShell használatával

Az Azure Data Box Edge megoldás lehetővé teszi az adatok feldolgozását és a hálózaton keresztül az Azure-ba való elküldést. Ez a cikk a Data Box Edge-eszköz konfigurációs és felügyeleti feladatait ismerteti. Használhatja az Azure Portalon, a helyi webes felhasználói felület, vagy a Windows PowerShell-felület en kezelheti az eszközt.

Ez a cikk a PowerShell-felület használatával végzett feladatokra összpontosít.

Ez a cikk a következő eljárásokat tartalmazza:

- Csatlakozás a PowerShell-kapcsolathoz
- Támogatási csomag létrehozása
- Tanúsítvány feltöltése
- Az eszköz alaphelyzetbe állítása
- Eszközadatok megtekintése
- Számítási naplók beszereznie
- Számítási modulok figyelése és hibaelhárítása

## <a name="connect-to-the-powershell-interface"></a>Csatlakozás a PowerShell-kapcsolathoz

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Támogatási csomag létrehozása

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Tanúsítvány feltöltése

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

IoT Edge-tanúsítványokat is feltölthet az IoT Edge-eszköz és a hozzá esetleg csatlakozó alsóbb rétegbeli eszközök közötti biztonságos kapcsolat engedélyezéséhez. Három IoT Edge-tanúsítványt *(.pem* formátum) kell telepítenie:

- Legfelső szintű hitelesítésszolgáltatói tanúsítvány vagy a tulajdonos hitelesítésszolgáltató
- Eszközhitelesítéstanúsítvány
- Eszközkulcs-tanúsítvány

A következő példa az IoT Edge-tanúsítványok telepítéséhez használt parancsmagot mutatja be:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
A parancsmag futtatásakor a rendszer kéri a hálózati megosztás jelszavának megadását.

A tanúsítványokról az [Azure IoT Edge-tanúsítványok](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) ról vagy [a Tanúsítványok telepítése átjárón című témakörben](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway)talál további információt.

## <a name="view-device-information"></a>Eszközadatok megtekintése
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>Eszköz alaphelyzetbe állítása

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Számítási naplók beszereznie

Ha a számítási szerepkör konfigurálva van az eszközön, a számítási naplók at a PowerShell-felületen keresztül is lejuthat.

1. [Csatlakozzon a PowerShell-kapcsolathoz.](#connect-to-the-powershell-interface)
2. Használja `Get-AzureDataBoxEdgeComputeRoleLogs` a számítási naplók behívása az eszközhöz.

    A következő példa a parancsmag használatát mutatja be:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection
    ```

    Itt van egy leírás a paramétereket a parancsmag:
    - `Path`: Adjon meg egy hálózati elérési utat ahhoz a megosztáshoz, amelyen létre szeretné hozni a számítási naplócsomagot.
    - `Credential`: Adja meg a hálózati megosztás felhasználónevét. A parancsmag futtatásakor meg kell adnia a megosztási jelszót.
    - `FullLogCollection`: Ez a paraméter biztosítja, hogy a naplócsomag tartalmazza az összes számítási naplót. Alapértelmezés szerint a naplócsomag csak a naplók egy részét tartalmazza.

## <a name="monitor-and-troubleshoot-compute-modules"></a>Számítási modulok figyelése és hibaelhárítása

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>Kilépés a távoli munkamenetből

A távoli PowerShell-munkamenetből való kilépéshez zárja be a PowerShell-ablakot.

## <a name="next-steps"></a>További lépések

- Az [Azure Data Box Edge](data-box-edge-deploy-prep.md) üzembe helyezése az Azure Portalon.
