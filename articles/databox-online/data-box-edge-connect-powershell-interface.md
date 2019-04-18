---
title: Csatlakozhat, és kezelheti a Microsoft Azure Data Box peremhálózati eszköz, a Windows PowerShell felületéről |} A Microsoft Docs
description: Ismerteti, hogyan csatlakozhat, és aztán kezelje a Data Box Edge a Windows PowerShell felületéről.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/29/2019
ms.author: alkohli
ms.openlocfilehash: b4d047f4266d11a5f6b77f33054eb93e31f7090b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58791575"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell"></a>Windows Powershellen keresztül az Azure Data Box peremhálózati eszköz kezelése

Az Azure Data Box peremhálózati megoldás lehetővé teszi az adatok feldolgozásához, és az Azure-bA a hálózaton keresztüli küldéshez. Ez a cikk ismerteti a Data Box Edge-eszköz konfigurációs és felügyeleti feladatokat. Használhatja az Azure Portalon, helyi webes felhasználói felületen vagy a Windows PowerShell felületet az eszköz kezelését.

Ez a cikk foglalkozik a feladatok végrehajtásához a PowerShell felület használatával.

Ez a cikk a következő eljárásokat tartalmazza:

- A PowerShell-felületünkön csatlakozni
- Indítson el egy támogatási munkamenetet
- Hozzon létre egy támogatási csomagot
- Tanúsítvány feltöltése
- Az eszköz alaphelyzetbe állítása
- Eszköz adatainak megtekintése
- Compute-naplók lekérése
- Monitorozás és hibaelhárítás számítási modulok

## <a name="connect-to-the-powershell-interface"></a>A PowerShell-felületünkön csatlakozni

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Hozzon létre egy támogatási csomagot

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Tanúsítvány feltöltése

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

IoT Edge-tanúsítványokat az IoT Edge-eszköz és előfordulhat, hogy csatlakozzon hozzá az alsóbb rétegbeli eszközök közötti biztonságos kapcsolatot is feltölthet. Három IoT Edge-tanúsítvány (*.pem* formátumban), telepítenie kell:

- Legfelső szintű Hitelesítésszolgáltatói tanúsítványt vagy a tulajdonos, CA
- Eszköz Hitelesítésszolgáltatói tanúsítvány
- Eszköz kulcsú tanúsítvány

Az alábbi példa bemutatja a használat, a parancsmag az IoT Edge-tanúsítványok telepítése:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username/password"
```

További információ a tanúsítványok, nyissa meg [Azure IoT Edge-tanúsítványok](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) vagy [telepítse a tanúsítványokat az átjáró a](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway#install-certificates-on-the-gateway).

## <a name="view-device-information"></a>Eszköz adatainak megtekintése

 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>Az eszköz alaphelyzetbe állítása

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Compute-naplók lekérése

Ha a számítási szerepkör konfigurálva van az eszközön, a PowerShell-felületen keresztül is kaphat a számítási naplókat.

1. [Kapcsolódás a PowerShell-felületünkön](#connect-to-the-powershell-interface).
2. Használja a `Get-AzureDataBoxEdgeComputeRoleLogs` számítási naplók lekérése az eszközt.

    Az alábbi példa bemutatja ennek a parancsmagnak a használatát:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username/password" -RoleInstanceName "IotRole" -FullLogCollection
    ```

    Itt látható a parancsmag paraméterei leírása:
    - `Path`: Adja meg a megosztást, ahol szeretné létrehozni a számítási log csomagot egy hálózati elérési útját.
    - `Credential`: Adja meg a felhasználónevet és jelszót a hálózati megosztáson.
    - `RoleInstanceName`: Adja meg ezt a karakterláncot `IotRole` ehhez a paraméterhez.
    - `FullLogCollection`: Ez a paraméter gondoskodik arról, hogy a napló csomag tartalmazza az összes számítási napló. Alapértelmezés szerint a napló csomagot a naplók csak egy részhalmazát tartalmazza.

## <a name="monitor-and-troubleshoot-compute-modules"></a>Monitorozás és hibaelhárítás számítási modulok

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]


## <a name="next-steps"></a>További lépések

- Az [Azure Data Box Edge](data-box-edge-deploy-prep.md) üzembe helyezése az Azure Portalon.