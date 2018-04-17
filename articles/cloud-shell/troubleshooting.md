---
title: Azure Cloud rendszerhéj hibaelhárítása |} Microsoft Docs
description: Hibaelhárítás az Azure-felhőbe rendszerhéj
services: azure
documentationcenter: ''
author: maertendMSFT
manager: angelc
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: damaerte
ms.openlocfilehash: 7ab344f77ef88ffdc2ff1976d97b0b9aa86aa3fc
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Hibaelhárítás & korlátozások az Azure felhőalapú rendszerhéj

A hibaelhárításhoz Azure Cloud rendszerhéj ismert megoldások a következők:

## <a name="general-troubleshooting"></a>Általános hibaelhárítási

### <a name="early-timeouts-in-firefox"></a>A FireFox korai időtúllépések
- **Részletek**: Felhő rendszerhéj egy bemeneti/kimeneti átadása a böngészőben nyissa meg websocket használja. A FireFox, bezárhatja a websocket túl korán okozó korai időtúllépések felhő rendszerhéj előre megadott házirendek rendelkezik.
- **Megoldási**: Nyissa meg a FireFox, és keresse meg "kapcsolatos: config" az URL-cím mezőbe. Keresse meg a "network.websocket.timeout.ping.request", és módosítsa az értéket 0 és 10.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Tárolási párbeszédpanel – hiba: 403-as RequestDisallowedByPolicy
- **Részletek**: Felhő rendszerhéj keresztül tárfiók létrehozásához esetén sikerült, mert az Azure házirend helyez el a rendszergazdának. Hibaüzenet tartalmazza: `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Megoldási**: a rendszergazdától Azure távolítsa el, vagy a tároló létrehozása megtagadása Azure házirend frissítése.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Tárolási párbeszédpanel – hiba: 400 DisallowedOperation
 - **Részletek**: Ha az Azure Active Directory-előfizetéssel, tárolási nem hozható létre.
 - **Megoldási**: képes a tárolási erőforrások létrehozása az Azure-előfizetést használ. Azure AD-előfizetések nem hozhat létre Azure-erőforrások.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Terminálszolgáltatások kimeneti - hiba: nem sikerült kapcsolódni a Terminálszolgáltatások: websocket nem hozható létre. Nyomja le az `Enter` az újracsatlakozáshoz.
 - **Részletek**: Felhő rendszerhéj megbizonyosodhat websocket kapcsolatot létesíteni a felhő rendszerhéj infrastruktúra.
 - **Megoldási**: Ellenőrizze, hogy konfigurálta a hálózati beállításokat küldő https és a websocket-kérelmeket, tartományokra való engedélyezéséhez *. console.azure.com.

## <a name="bash-troubleshooting"></a>Hibaelhárítási bash

### <a name="cannot-run-az-login"></a>Nem futtatható az bejelentkezés

- **Részletek**: futó `az login` nem fog működni, mint Ön már hitelesítve felhő rendszerhéj vagy Azure portal-ba való bejelentkezéshez használt fiókkal.
- **Megoldási**: a bejelentkezéshez vagy jelentkezzen ki, és a kívánt Azure fiókjához újból hitelesítésre használt fiók használata.

### <a name="cannot-run-the-docker-daemon"></a>A docker démon nem futtatható.

- **Részletek**: Felhő rendszerhéj használja egy tárolót a rendszerhéj környezet üzemeltetéséhez, ezért fut a démon nem engedélyezett.
- **Megoldási**: kihasználhassák [docker-gép](https://docs.docker.com/machine/overview/), amelyiken telepítve van alapértelmezés szerint a docker-tárolók kezelése távoli Docker gazdagépről.

## <a name="powershell-troubleshooting"></a>PowerShell hibaelhárítása

### <a name="no-home-directory-persistence"></a>No $Home directory adatmegőrzési

- **Részletek**: adatokat használó alkalmazást (például: git, vim, megint mások) ír `$Home` nem őrzi meg PowerShell-munkamenetek között.
- **Megoldási**: a PowerShell-profilt, hozzon létre egy alkalmazás adott mappához a szimbolikus hivatkozást `clouddrive` $Home számára.

### <a name="ctrlc-doesnt-exit-out-of-a-cmdlet-prompt"></a>CTRL + C nem lépjen ki a parancsmag adatkérést

- **Részletek**: megkísérlésekor. Lépjen ki a parancsmag parancssorba `Ctrl+C` nem létezik a parancssorba.
- **Megoldási**: való kilépéshez a parancssorba, nyomja le az `Ctrl+C` majd `Enter`.

### <a name="gui-applications-are-not-supported"></a>Grafikus felhasználói Felülettel alkalmazások nem támogatottak.

- **Részletek**: Ha egy felhasználó elindítja egy grafikus felhasználói Felülettel alkalmazást, a kérdés nem ad vissza. Például amikor egy felhasználó klónokat titkos GitHub-tárház kéttényezős hitelesítés engedélyezve van, egy párbeszédpanel a kéttényezős hitelesítéshez befejezésének.  
- **Megoldási**: zárja be és nyissa meg újra a rendszerhéj.

### <a name="get-help--online-does-not-open-the-help-page"></a>Get-Help - online nem nyitható meg a súgólap

- **Részletek**: Ha a `Get-Help Find-Module -online`, egy üzenetet egy hiba, mint: `Starting a browser to display online Help failed. No program or browser is associated to open the URI http://go.microsoft.com/fwlink/?LinkID=398574.`
- **Megoldási**: másolja az URL-címet, majd nyissa meg kézzel a böngésző.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Hibaelhárítás az Azure virtuális gépek távoli felügyelete

- **Részletek**: a Rendszerfelügyeleti webszolgáltatások a Windows tűzfal alapértelmezett beállításai miatt a felhasználó a következő hiba láthat: `Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
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

### <a name="dir-caches-the-result-in-azure-drive"></a>`dir` gyorsítótárba helyezi azt az eredményt az Azure-meghajtó

- **Részletek**: eredményét `dir` tárolja a rendszer az Azure-meghajtó.
- **Megoldási**: Miután hoz létre, vagy eltávolít egy erőforrást az Azure meghajtó nézetben, `dir -force` frissítéséhez.

## <a name="general-limitations"></a>Általános korlátozások
Azure Cloud rendszerhéj ismert korlátai a következők:

### <a name="system-state-and-persistence"></a>Rendszerállapot- és adatmegőrzési

A felhő rendszerhéj munkamenet biztosító a gép csak átmenetileg létezik, és újrahasznosítása után a munkamenet a 20 percig inaktív. Felhő rendszerhéj csatlakoztatni kell egy Azure fájlmegosztás szükséges. Ennek eredményeképpen az előfizetés kell tudni tárolási erőforrások beállítása felhő rendszerhéj eléréséhez. Egyéb szempontok a következők:

* Csatlakoztatott tárolóval, csak azokat a módosításokat, belül a `clouddrive` directory megmaradnak. A Bash a `$Home` directory is megőrződjenek.
* Azure fájlmegosztásokat csak belülről lehet csatlakoztatni az [hozzárendelve régió](persisting-shell-storage.md#mount-a-new-clouddrive).
  * A Bash, futtassa az `env` állítja be a régióban található `ACC_LOCATION`.
* Az Azure Files csak a helyileg redundáns tárolás és a georedundáns tárolás fiókokat támogatja.

### <a name="browser-support"></a>Webböngésző támogatása

Felhő rendszerhéj a Microsoft Edge, a Microsoft Internet Explorer, a Google Chrome, a Mozilla Firefox és az Apple Safari legújabb verzióit támogatja. Safari privát üzemmódban nem támogatott.

### <a name="copy-and-paste"></a>Másolás és beillesztés

[!include [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>A megadott felhasználó csak egy rendszerhéj lehet aktív

Felhasználók is csak elindíthatja egy adott típusú rendszerhéj egyszerre, vagy **Bash** vagy **PowerShell**. Azonban előfordulhat, hogy Bash vagy a PowerShell fut egyszerre több példányát. Csere Bash vagy a PowerShell okok felhő rendszerhéj újraindítására, amely megszakítja a meglévő munkamenetek között.

### <a name="usage-limits"></a>Használati korlátozások

Felhő rendszerhéj készült interaktív használati eseteket. Ennek eredményeképpen a hosszan futó nem interaktív munkamenet befejeződik figyelmeztetés nélkül.

## <a name="bash-limitations"></a>Bash korlátozásai

### <a name="user-permissions"></a>Felhasználói engedélyek

Engedélyek vannak beállítva, normál felhasználóként sudo hozzáférés nélkül. Bármely telepítési kívül a `$Home` könyvtár nem őrzi meg.

### <a name="editing-bashrc"></a>.Bashrc szerkesztése

Szerkesztés .bashrc, így váratlan hibákat okozhat felhő rendszerhéj elvégzendő járjon el.

## <a name="powershell-limitations"></a>PowerShell-korlátozások

### <a name="slow-startup-time"></a>Lassú indítási idő

Azure Cloud rendszerhéj (előzetes verzió) PowerShell előzetes inicializálása 60 másodperc is beletelhet.

### <a name="default-file-location-when-created-from-azure-drive"></a>Alapértelmezett helye az Azure-meghajtóról létrehozásakor:

PowerShell-parancsmagok használatával felhasználók nem hozható létre az Azure meghajtón található fájl. Amikor a felhasználók más eszközökkel, például vim vagy nano, új fájlok létrehozása a fájlok kerülnek C:\Users mappa alapértelmezés szerint. 

### <a name="gui-applications-are-not-supported"></a>Grafikus felhasználói Felülettel alkalmazások nem támogatottak.

Ha a felhasználó hozna létre például egy Windows párbeszédpanelen parancsot futtatja `Connect-AzureAD` vagy `Login-AzureRMAccount`, egy üzenetet egy hiba például: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`.

## <a name="gdpr-compliance-for-cloud-shell"></a>Felhő rendszerhéj GDPR megfelelősége

Azure Cloud rendszerhéj súlyosan veszi a személyes adatokat, rögzített, és az Azure felhőalapú Shell szolgáltatás által tárolt adatok alapértelmezett beállításokat biztosít a felhasználói élmény, például a legutóbb használt rendszerhéj használt, elsődleges betűméret, elsődleges betűtípus, és a fájlmegosztás részletei amely biztonsági clouddrive. Kell kíván exportálni, vagy törölje ezeket az adatokat, az alábbi utasítások alapján vannak megadva.

### <a name="export"></a>Exportálás
Annak érdekében, hogy **exportálása** felhő rendszerhéj menti, például a felhasználói beállítások előnyben részesített rendszerhéj, betűméret és a következő parancsokat betűtípus.

1. Indítsa el a felhő rendszerhéj Bash
2. Futtassa az alábbi parancsot:
```
user@Azure:~$ token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
user@Azure:~$ curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
```

### <a name="delete"></a>Törlés
Annak érdekében, hogy **törlése** a felhő rendszerhéj menti, például a felhasználói beállítások előnyben részesített rendszerhéj, betűméret és a következő parancsokat betűtípus. A felhő rendszerhéj következő indításakor fogja kérni bevezetésében fájlmegosztás újra. 

A tényleges Azure fájlok megosztás nem törlődik, ha törli a felhasználói beállításokat, nyissa meg az Azure Fileshoz, hogy a művelet elvégzéséhez.

1. Indítsa el a felhő rendszerhéj Bash
2. Futtassa az alábbi parancsot:
```
user@Azure:~$ token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
user@Azure:~$ curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
```
