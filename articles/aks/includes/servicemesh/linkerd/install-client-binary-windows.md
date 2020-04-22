---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 1a023475de1ce2891916807632d9ee15e382326c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81736277"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>A Linkerd linkerd ügyfél bináris fájljának letöltése és telepítése

A Windows PowerShell-alapú rendszerhéjban a Linkerd kiadásletöltéséhez az alábbiak szerint töltheti `Invoke-WebRequest` le:

```powershell
# Specify the Linkerd version that will be leveraged throughout these instructions
$LINKERD_VERSION="stable-2.6.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-windows.exe" -OutFile "linkerd2-cli-$LINKERD_VERSION-windows.exe"
```

Az `linkerd` ügyfél bináris fut az ügyfélgépen, és lehetővé teszi, hogy a Linkerd szolgáltatás háló. A következő parancsokkal telepítse a `linkerd` Linkerd ügyfél bináris egy PowerShell-alapú rendszerhéj a Windows. Ezek a `linkerd` parancsok másolja az ügyfél bináris egy Linkerd mappába, majd elérhetővé mind azonnal (az `PATH`aktuális shell) és véglegesen (az egész shell újraindul) keresztül. A parancsok futtatásához nincs szükség emelt szintű (rendszergazdai) jogosultságokra, és nem kell újraindítania a rendszerhéjat.

```powershell
# Copy linkerd.exe to C:\Linkerd
New-Item -ItemType Directory -Force -Path "C:\Linkerd"
Copy-Item -Path ".\linkerd2-cli-$LINKERD_VERSION-windows.exe" -Destination "C:\Linkerd\linkerd.exe"

# Add C:\Linkerd to PATH. 
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH", "User") + ";C:\Linkerd\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\Linkerd\"
```