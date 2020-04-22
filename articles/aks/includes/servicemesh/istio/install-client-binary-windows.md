---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: e26a2c214a03243d6507296c1e981706be8c56db
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81735286"
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
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH", "User") + ";C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\Istio\"
```