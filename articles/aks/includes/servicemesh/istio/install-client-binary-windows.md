---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: a1a608b4f4e1afe4a3cf99229561a91cc3f9fa96
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170825"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Töltse le és telepítse a Istio istioctl-ügyfél bináris fájlját

A Windows PowerShell-alapú rendszerhéjában a `Invoke-WebRequest` használatával töltse le a Istio-kiadást, majd bontsa ki a `Expand-Archive` a következő módon:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.4.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

A `istioctl`-ügyfél bináris fájlja fut az ügyfélszámítógépen, és lehetővé teszi a Istio szolgáltatás hálójának kezelését. A következő parancsokkal telepítheti a Istio `istioctl`-ügyfél bináris fájlját PowerShell-alapú rendszerhéjba Windows rendszeren. Ezek a parancsok a `istioctl`-ügyfél bináris fájljait egy Istio mappába másolják, majd azonnal elérhetővé teszik (a jelenlegi rendszerhéjban) és véglegesen (a rendszerhéj újraindításakor) a `PATH`keresztül. A parancsok futtatásához nincs szükség emelt szintű (rendszergazdai) jogosultságokra, és nem kell újraindítani a rendszerhéjat.

```powershell
# Copy istioctl.exe to C:\Istio
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```