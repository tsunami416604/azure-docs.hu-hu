---
title: "Azure Cloud rendszerhéj hibaelhárítása |} Microsoft Docs"
description: "Hibaelhárítás az Azure-felhőbe rendszerhéj"
services: azure
documentationcenter: 
author: maertendMSFT
manager: angelc
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/2/2017
ms.author: damaerte
ms.openlocfilehash: 59aec1cb6433e90caf683b4bce98c92e9f79fd18
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="troubleshooting-azure-cloud-shell"></a>Hibaelhárítás az Azure-felhőbe rendszerhéj

Ismert problémák Azure Cloud rendszerhéj megoldások:

## <a name="general-resolutions"></a>Általános megoldások

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Tárolási párbeszédpanel – hiba: 403-as RequestDisallowedByPolicy
- **Részletek**: Felhő rendszerhéj keresztül tárfiók létrehozásához esetén sikerült, mert az Azure házirend helyez el a rendszergazdának. Hibaüzenet tartalmazza:`The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Megoldási**: a rendszergazdától Azure távolítsa el, vagy a tároló létrehozása megtagadása Azure házirend frissítése.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Tárolási párbeszédpanel – hiba: 400 DisallowedOperation
 - **Részletek**: Ha az Azure Active Directory-előfizetéssel, tárolási nem hozható létre.
 - **Megoldási**: képes a tárolási erőforrások létrehozása az Azure-előfizetést használ. Azure AD-előfizetések nem hozhat létre Azure-erőforrások.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Terminálszolgáltatások kimeneti - hiba: nem sikerült kapcsolódni a Terminálszolgáltatások: websocket nem hozható létre. Nyomja le az `Enter` az újracsatlakozáshoz.
 - **Részletek**: Felhő rendszerhéj megbizonyosodhat websocket kapcsolatot létesíteni a felhő rendszerhéj infrastruktúra.
 - **Megoldási**: Ellenőrizze, hogy konfigurálta a hálózati beállításokat küldő https és a websocket-kérelmeket, tartományokra való engedélyezéséhez *. console.azure.com.

## <a name="bash-resolutions"></a>Bash megoldások

### <a name="cannot-run-az-login"></a>Nem futtatható az bejelentkezés

- **Részletek**: futó `az login` nem fog működni, mint Ön már hitelesítve felhő rendszerhéj vagy Azure portal-ba való bejelentkezéshez használt fiókkal.
- **Megoldási**: a bejelentkezéshez vagy jelentkezzen ki, és a kívánt Azure fiókjához újból hitelesítésre használt fiók használata.

### <a name="cannot-run-the-docker-daemon"></a>A docker démon nem futtatható.

- **Részletek**: Felhő rendszerhéj használja egy tárolót a rendszerhéj környezet üzemeltetéséhez, ezért fut a démon nem engedélyezett.
- **Megoldási**: kihasználhassák [docker-gép](https://docs.docker.com/machine/overview/), amelyiken telepítve van alapértelmezés szerint a docker-tárolók kezelése távoli Docker gazdagépről.

## <a name="powershell-resolutions"></a>PowerShell-megoldások

### <a name="no-home-directory-persistence"></a>No $Home directory adatmegőrzési

- **Részletek**: adatokat használó alkalmazást (például: git, vim, megint mások) ír `$Home` nem őrzi meg PowerShell-munkamenetek között.
- **Megoldási**: a PowerShell-profilt, hozzon létre egy alkalmazás adott mappához a szimbolikus hivatkozást `clouddrive` $Home számára.

### <a name="ctrlc-doesnt-exit-out-of-a-cmdlet-prompt"></a>CTRL + C nem lépjen ki a parancsmag adatkérést

- **Részletek**: megkísérlésekor. Lépjen ki a parancsmag parancssorba `Ctrl+C` nem létezik a parancssorba.
- **Megoldási**: való kilépéshez a parancssorba, nyomja le az `Ctrl+C` majd `Enter`.

### <a name="gui-applications-are-not-supported"></a>Grafikus felhasználói Felülettel alkalmazások nem támogatottak.

- **Részletek**: Ha egy felhasználó elindítja egy grafikus felhasználói Felülettel alkalmazást, a kérdés nem ad vissza. Például amikor egy felhasználó klónokat titkos GitHub-tárház kéttényezős hitelesítés engedélyezve van, egy párbeszédpanel a kéttényezős hitelesítéshez befejezésének.
- **Megoldási**: `Ctrl+C` való kilépéshez a parancsot.

### <a name="get-help--online-does-not-open-the-help-page"></a>Get-Help - online nem nyitható meg a súgólap

- **Részletek**: Ha a `Get-Help Find-Module -online`, egy üzenetet egy hiba, mint:`Starting a browser to display online Help failed. No program or browser is associated to open the URI http://go.microsoft.com/fwlink/?LinkID=398574.`
- **Megoldási**: másolja az URL-címet, majd nyissa meg kézzel a böngésző.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Hibaelhárítás az Azure virtuális gépek távoli felügyelete

- **Részletek**: a Rendszerfelügyeleti webszolgáltatások a Windows tűzfal alapértelmezett beállításai miatt a felhasználó a következő hiba láthat:`Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Megoldási**: Győződjön meg arról, hogy a virtuális gép fut-e. Futtathat `Get-AzureRmVM -Status` a virtuális gép állapotának megállapítása.  Ezt követően adja hozzá egy új tűzfalszabály például egyetlen alhálózatának sem, a Rendszerfelügyeleti webszolgáltatások kapcsolatok engedélyezése a távoli virtuális Gépen

 ``` Powershell
 New-NetFirewallRule -Name 'WINRM-HTTP-In-TCP-PSCloudShell' -Group 'Windows Remote Management' -Enabled True -Protocol TCP -LocalPort 5985 -Direction Inbound -Action Allow -DisplayName 'Windows Remote Management - PSCloud (HTTP-In)' -Profile Public
 ```
 Használhat [Azure egyéni parancsprogramok futtatására szolgáló bővítmény](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript) elkerülése érdekében jelentkezzen be a távoli virtuális Gépet az új szabály felvételéhez.
 Az előző parancsfájl menthető egy fájlba, mondja ki `addfirerule.ps1`, és töltse fel az Azure storage tárolót.
 Próbálja a következő parancsot:

 ``` Powershell
 Get-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup | Set-AzureRmVMCustomScriptExtension -VMName MyVM1 -FileUri https://mystorageaccount.blob.core.windows.net/mycontainer/addfirerule.ps1 -Run 'addfirerule.ps1' -Name myextension
 ```

### <a name="dir-caches-the-result-in-azure-drive"></a>`dir`gyorsítótárba helyezi azt az eredményt az Azure-meghajtó

- **Részletek**: eredményét `dir` tárolja a rendszer az Azure-meghajtó.
- **Megoldási**: Miután hoz létre, vagy eltávolít egy erőforrást az Azure meghajtó nézetben, `dir -force` frissítéséhez.
