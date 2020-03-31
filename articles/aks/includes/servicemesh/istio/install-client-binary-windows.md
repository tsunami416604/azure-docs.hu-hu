---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: 562382cc1cfb6adb7e65d76e717df4c4e2962ba7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593967"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Az Istio istioctl kliens bináris letöltése és telepítése

A Windows PowerShell-alapú rendszerhéjában töltse `Invoke-WebRequest` le az Istio-kiadást, majd bontsa ki `Expand-Archive` az alábbi módon:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.4.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

Az `istioctl` ügyfél bináris fut az ügyfélgépen, és lehetővé teszi, hogy az Istio szolgáltatás hálója. A következő parancsokkal telepítse az `istioctl` Istio-ügyfél bináris egy PowerShell-alapú rendszerhéj a Windows. Ezek a `istioctl` parancsok másolja az ügyfél bináris egy Istio mappába, majd elérhetővé mind azonnal (az `PATH`aktuális shell) és véglegesen (az egész shell újraindul) keresztül. A parancsok futtatásához nincs szükség emelt szintű (rendszergazdai) jogosultságokra, és nem kell újraindítania a rendszerhéjat.

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