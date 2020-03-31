---
title: Az Azure Cloud Shell hibaelhárítása | Microsoft dokumentumok
description: Az Azure Cloud Shell hibáinak elhárítása
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: 0b1b22095c77344ed71762d3d51b12f19d9f1811
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79458052"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Az Azure Cloud Shell & korlátainak elhárítása

Az Azure Cloud Shell ben a hibaelhárítási problémák ismert megoldásai a következők:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-troubleshooting"></a>Általános hibaelhárítás

### <a name="error-running-azuread-cmdlets-in-powershell"></a>Hiba történt az AzureAD-parancsmagok powershellben való futtatásakor

- **Részletek**: Az AzureAD-parancsmagok `Get-AzureADUser` futtatásakor, például a `You must call the Connect-AzureAD cmdlet before calling any other cmdlets`Cloud Shellben, hibaüzenet jelenhet meg: . 
- **Felbontás**: `Connect-AzureAD` Futtassa a parancsmast. Korábban a Cloud Shell automatikusan futtatta ezt a parancsmast a PowerShell indításakor. Az indítási idő felgyorsítása érdekében a parancsmag már nem fut automatikusan. Dönthet úgy, hogy visszaállítja `Connect-AzureAD` az előző viselkedést a PowerShell ben a $PROFILE fájlhoz való hozzáadással.

### <a name="early-timeouts-in-firefox"></a>Korai időtúlórák a FireFox-ban

- **Részletek: A**Cloud Shell egy nyitott websocketet használ a bemeneti/kimeneti adatok és kimenetek böngészőjének átadására. A FireFox előre beállított szabályzatokkal rendelkezik, amelyek idő előtt bezárhatják a websocketet, ami korai időtúlórákat okoz a Cloud Shellben.
- **Megoldás**: Nyissa meg a FireFox-ot, és keresse meg a "about:config" szót az URL-cím mezőben. Keressen rá a "network.websocket.timeout.ping.request" kifejezésre, és módosítsa az értéket 0-ról 10-re.

### <a name="disabling-cloud-shell-in-a-locked-down-network-environment"></a>A Cloud Shell letiltása zárolt hálózati környezetben

- **Részletek**: Előfordulhat, hogy a rendszergazdák le szeretnék tiltani a Cloud Shell-hez való hozzáférést a felhasználók számára. A Cloud Shell a `ux.console.azure.com` tartományhoz való hozzáférést használja, amely megtagadható, és leállítja a Cloud Shell belépési pontjaihoz való hozzáférést, beleértve a portal.azure.com, a shell.azure.com, a Visual Studio Code Azure-fiókbővítményt és a docs.microsoft.com. Az Egyesült Államok kormányának felhőjében a belépési pont ; `ux.console.azure.us` nincs megfelelő shell.azure.us.
- **Megoldás:** Korlátozza `ux.console.azure.com` a `ux.console.azure.us` hálózati beállításokhoz való hozzáférést, vagy azon keresztül a környezetre. A Cloud Shell ikon továbbra is megmarad az Azure Portalon, de nem lesz sikeresen csatlakozik a szolgáltatáshoz.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Tárolási párbeszédpanel – Hiba: 403 RequestDisallowedByPolicy

- **Részletek:** Amikor a Cloud Shellen keresztül hoz létre tárfiókot, sikertelen lesz a rendszergazda által elhelyezett Azure-szabályzat miatt. A hibaüzenet a következőket tartalmazza:`The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Megoldás:** Lépjen kapcsolatba az Azure-rendszergazdával a tároló létrehozását megtagadó Azure-szabályzat eltávolításához vagy frissítéséhez.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Tárolási párbeszédpanel – Hiba: 400 disallowedOperation

- **Részletek:** Azure Active Directory-előfizetés használata esetén nem hozhat létre tárhelyet.
- **Megoldás:** Használjon olyan Azure-előfizetést, amely képes tárolási erőforrások létrehozására. Az Azure AD-előfizetések nem hozhatnak létre Azure-erőforrásokat.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Terminálkimenet - Hiba: Nem sikerült csatlakozni a terminálhoz: a websocket nem hozható létre. Nyomja `Enter` meg az újracsatlakozáshoz.
- **Részletek: A**Cloud Shell lehetővé teszi websocket-kapcsolat létesítéséhez a Cloud Shell-infrastruktúrához.
- **Megoldás:** Ellenőrizze, hogy konfigurálta-e a hálózati beállításokat, hogy engedélyezze a https-kérelmek és a websocket-kérelmek küldését a *.console.azure.com tartományba.

### <a name="set-your-cloud-shell-connection-to-support-using-tls-12"></a>A Cloud Shell-kapcsolat beállítása a TLS 1.2 használatával támogatotttá
 - **Részletek**: A TLS verziójának meghatározásához a Cloud Shell-hez való csatlakozáshoz böngészőspecifikus beállításokat kell megadnia.
 - **Megoldás:** Keresse meg a böngésző biztonsági beállításait, és jelölje be a "TLS 1.2 használata" jelölőnégyzetet.

## <a name="bash-troubleshooting"></a>Bash hibaelhárítás

### <a name="cannot-run-the-docker-daemon"></a>A docker démon nem futtatható

- **Részletek:** A Cloud Shell egy tárolót használ a rendszerhéj-környezet üzemeltetéséhez, ennek eredményeképpen a démon futtatása nem engedélyezett.
- **Megoldás :** Használja [a docker-machine,](https://docs.docker.com/machine/overview/)amely alapértelmezés szerint telepítve van, a docker-tárolók kezelésére egy távoli Docker-gazdagép.

## <a name="powershell-troubleshooting"></a>PowerShell hibaelhárítása

### <a name="gui-applications-are-not-supported"></a>A GRAFIKUS alkalmazások nem támogatottak

- **Részletek**: Ha a felhasználó elindítja a GRAFIKUS alkalmazást, a kérdés nem ad vissza. Ha például egy klón egy privát GitHub-tárta, amely kéttényezős hitelesítés engedélyezve van, egy párbeszédpanel jelenik meg a kétfaktoros hitelesítés befejezéséhez.
- **Megoldás**: Zárja be és nyissa meg újra a héjat.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Az Azure virtuális gépek távoli kezelésével kapcsolatos hibák elhárítása
> [!NOTE]
> Az Azure-beli virtuális gépeknek nyilvános ip-címmel kell rendelkezniük.

- **Részletek**: A Windows tűzfal WinRM alapértelmezett beállításai miatt a felhasználó a következő hibaüzenetet láthatja:`Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Megoldás**: `Enable-AzVMPSRemoting` Futtassa a PowerShell-távfutás minden aspektusának engedélyezéséhez a célgépen.

### <a name="dir-does-not-update-the-result-in-azure-drive"></a>`dir`nem frissíti az eredményt az Azure drive-ban

- **Részletek**: Alapértelmezés szerint a felhasználói élmény `dir` optimalizálása érdekében az eredmények az Azure drive-on gyorsítótárazott.
- **Megoldás:** Miután létrehozott, frissített vagy eltávolít `dir -force` ott egy Azure-erőforrást, futtassa az eredményeket az Azure-meghajtóban.

## <a name="general-limitations"></a>Általános korlátozások

Az Azure Cloud Shell a következő ismert korlátozásokkal rendelkezik:

### <a name="quota-limitations"></a>Kvótakorlátozások

Az Azure Cloud Shell bérlőnként és régiónként legfeljebb 20 egyidejű felhasználóval rendelkezik. Ha a korlátnál több egyidejű munkamenetet próbál megnyitni, a "Bérlői kvóta felett" hibaüzenet jelenik meg. Ha jogosan van szükség arra, hogy ennél több munkamenetet nyisson meg (például képzések esetén), a kvótanöveléséhez a várt használat előtt vegye fel a kapcsolatot az ügyfélszolgálattal.

A Cloud Shell ingyenes szolgáltatásként érhető el, és az Azure-környezet konfigurálására szolgál, nem pedig általános célú számítástechnikai platformként. A túlzott automatizált használat az Azure szolgáltatási feltételeinek megsértésének minősülhet, és a Cloud Shell-hozzáférés letiltásához vezethet.

### <a name="system-state-and-persistence"></a>A rendszer állapota és perzisztenciája

A Cloud Shell-munkamenetet biztosító gép ideiglenes, és a munkamenet 20 percig tartó inaktívvá vájta. A Cloud Shell csatlakoztatásához Azure-fájlmegosztásra van szükség. Ennek eredményeképpen az előfizetésnek képesnek kell lennie a Cloud Shell eléréséhez szükséges tárolási erőforrások beállítására. Egyéb szempontok közé tartozik:

- Csatlakoztatott tároló esetén csak a `clouddrive` könyvtáron belüli módosítások maradnak meg. A Bash, `$HOME` a könyvtár is megmarad.
- Az Azure-fájlmegosztások csak a [hozzárendelt régióból](persisting-shell-storage.md#mount-a-new-clouddrive)csatlakoztathatók.
  - A Bash, `env` fuss, hogy `ACC_LOCATION`megtalálja a régió meg a .
- Az Azure Files csak helyileg redundáns tárolási és georedundáns tárfiókokat támogat.

### <a name="browser-support"></a>Böngészőtámogatás

A Cloud Shell a következő böngészők legújabb verzióit támogatja:

- Microsoft Edge
- Microsoft Internet Explorer
- Google Chrome
- Mozilla Firefox
- Apple Safari
  - Privát módban a Safari nem támogatott.

### <a name="copy-and-paste"></a>Másolás és beillesztés

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="usage-limits"></a>Használati korlátok

A Cloud Shell interaktív használati esetekre szolgál. Ennek eredményeképpen minden hosszú ideig futó, nem interaktív munkamenet figyelmeztetés nélkül véget ér.

### <a name="user-permissions"></a>Felhasználói engedélyek

Az engedélyek rendszeres felhasználóként vannak beállítva sudo hozzáférés nélkül. A címin `$Home` kívüli telepítés nem marad meg.

## <a name="bash-limitations"></a>Bash korlátozások

### <a name="editing-bashrc"></a>Szerkesztés .bashrc

A .bashrc szerkesztésekor körültekintően járjon el, ezért váratlan hibákat okozhat a Cloud Shellben.

## <a name="powershell-limitations"></a>PowerShell-korlátozások

### <a name="preview-version-of-azuread-module"></a>Az AzureAD-modul előzetes verziója

Jelenleg `AzureAD.Standard.Preview`a . Ez a modul ugyanazokat `AzureAD`a funkciókat biztosítja, mint a.

### <a name="sqlserver-module-functionality"></a>`SqlServer`modul funkció

A `SqlServer` Cloud Shellben található modul csak a PowerShell Core előzetes támogatásával rendelkezik. Különösen még `Invoke-SqlCmd` nem áll rendelkezésre.

### <a name="default-file-location-when-created-from-azure-drive"></a>Alapértelmezett fájlhely az Azure-meghajtóról létrehozva

A PowerShell-parancsmagok használatával a felhasználók nem hozhatnak létre fájlokat az Azure-meghajtó n. Amikor a felhasználók új fájlokat hoznak létre más eszközökkel, `$HOME` például vim vagy nano eszközökkel, a fájlok alapértelmezés szerint a rendszer ébresztésre kerülnek.

### <a name="tab-completion-can-throw-psreadline-exception"></a>A tabulátor kiegészítése a PSReadline kivételt okozhat

Ha a felhasználó PSReadline EditMode van beállítva, hogy Emacs, a felhasználó megpróbálja megjeleníteni az összes lehetőséget keresztül lapon befejezését, és az ablak mérete túl kicsi ahhoz, hogy megjelenjen az összes lehetőséget, PSReadline dobja kezeletlen kivétel.

### <a name="large-gap-after-displaying-progress-bar"></a>Nagy rés a folyamatjelző sáv megjelenítése után

Ha egy parancs vagy felhasználó művelet folyamatjelző sávot `Azure:` jelenít meg, amely a meghajtóban lévő lap befejezése, akkor lehetséges, hogy a kurzor nincs megfelelően beállítva, és egy rés jelenik meg ott, ahol a folyamatjelző sáv korábban volt.

### <a name="random-characters-appear-inline"></a>A véletlenszerű karakterek szövegköziként jelennek meg

A kurzor pozíciószekvencia-kódjai például `5;13R`megjelenhetnek a felhasználói bevitelben. A karakterek manuálisan eltávolíthatók.

## <a name="personal-data-in-cloud-shell"></a>Személyes adatok a Cloud Shellben

Az Azure Cloud Shell komolyan veszi a személyes adatokat, az Azure Cloud Shell szolgáltatás által rögzített és tárolt adatokat olyan felhasználói élmény alapértelmezett adatainak biztosításához használjuk, mint a legutóbb használt rendszerhéj, az előnyben részesített betűméret, az előnyben részesített betűtípus és a fájlmegosztás részletei. hogy vissza felhő meghajtót. Ha exportálni vagy törölni szeretné ezeket az adatokat, kövesse az alábbi utasításokat.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>Exportálás
A felhasználói beállítások **exportálásához** a Cloud Shell ment az Ön számára, például az előnyben részesített rendszerhéj, a betűméret és a betűtípus a következő parancsokat futtatja.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Launch Azure Cloud Shell")](https://shell.azure.com)
2. Futtassa a következő parancsokat a Bash vagy a PowerShell játékban:

Bash:

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  ((Invoke-WebRequest -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}).Content | ConvertFrom-Json).properties | Format-List
```

### <a name="delete"></a>Törlés
A felhasználói beállítások **törléséhez** a Cloud Shell ment az Ön számára, például az előnyben részesített rendszerhéj, a betűméret és a betűtípus a következő parancsokat futtatja. Amikor legközelebb elindítja a Cloud Shellt, a rendszer felkéri, hogy újra bekell szállnia egy fájlmegosztásra. 

>[!Note]
> Ha törli a felhasználói beállításokat, a tényleges Azure Files megosztás nem törlődik. A művelet végrehajtásához nyissa meg az Azure-fájlokat.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Launch Azure Cloud Shell")](https://shell.azure.com)
2. Futtassa a következő parancsokat a Bash vagy a PowerShell játékban:

Bash:

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  Invoke-WebRequest -Method Delete -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}
  ```
## <a name="azure-government-limitations"></a>Az Azure Government korlátozásai
Az Azure Cloud Shell az Azure Government ben csak az Azure Portalon keresztül érhető el.

>[!Note]
> A GCC-high vagy government dod felhőkhöz való csatlakozás az Exchange Online-hoz jelenleg nem támogatott.
