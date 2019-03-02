---
title: Azure Cloud Shell-korlátozások |} A Microsoft Docs
description: Azure Cloud Shell korlátozások áttekintése
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: damaerte
ms.openlocfilehash: 8fd88221818d28c227c33719c03e522e815a408b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57245743"
---
# <a name="limitations-of-azure-cloud-shell"></a>Az Azure Cloud Shell korlátozásai

Az Azure Cloud Shell a következő ismert korlátozások vonatkoznak:

## <a name="general-limitations"></a>Általános korlátozások

### <a name="system-state-and-persistence"></a>Rendszerállapot és adatmegőrzés

A gép, amely biztosítja a Cloud Shell-munkamenetek ideiglenes, és legyen újrahasznosítása után a munkamenet a 20 percig inaktív. A cloud Shell Azure-fájlmegosztás csatlakoztatása a szükséges. Az előfizetés ennek eredményeképpen a Cloud Shell eléréséhez a tárolási erőforrások beállításához képesnek kell lennie. Egyéb szempontok közé tartoznak:

* Csatlakoztatott tárolóval, csak a módosítások belül a `$Home` könyvtár tárolja.
* Azure-fájlmegosztások csak a csatlakoztathatók a [régió hozzárendelt](persisting-shell-storage.md#mount-a-new-clouddrive).
  * Futtassa a Bash, `env` állítja be a régióban található `ACC_LOCATION`.

### <a name="browser-support"></a>Böngésző támogatása

A cloud Shell támogatja a Microsoft Edge, a Microsoft Internet Explorer, a Google Chrome, a Mozilla Firefox és a Apple Safari legfrissebb verzióit. Safari böngészőt privát üzemmódban nem támogatott.

### <a name="copy-and-paste"></a>Másolás és beillesztés

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Egy adott felhasználó csak egy parancshéj lehet aktív.

Csak indíthatják egyfajta rendszerhéj egyszerre, vagy **Bash** vagy **PowerShell**. Előfordulhat azonban, Bash- vagy PowerShell fut egyszerre több példányát. A menü használatával Bash- vagy PowerShell közötti felcserélése hatására a Cloud Shell újraindítása, amely befejezi a meglévő munkameneteket. Alternatív megoldásként futtathatja a PowerShell belül bash beírásával `bash`, és futtathatja a PowerShell belül bash beírásával `pwsh`.

### <a name="usage-limits"></a>Használati korlátozások

A cloud Shell interaktív használati esetek szól. Ennek eredményeképpen minden olyan hosszan futó nem interaktív munkamenet befejeződik figyelmeztetés nélkül.

## <a name="bash-limitations"></a>Bash-korlátozások

### <a name="user-permissions"></a>Felhasználói engedélyek

Engedélyek beállítása normál felhasználóként sudo hozzáférés nélkül. Minden olyan telepítési kívül a `$Home` directory nincs megőrizve.

### <a name="editing-bashrc-or-profile"></a>.Bashrc vagy $PROFILE szerkesztése

Legyen körültekintő elvégzendő .bashrc vagy a PowerShell $PROFILE fájlt, végrehajtására szerkesztési váratlan hibákat eredményezhet a Cloud Shellben.

## <a name="powershell-limitations"></a>PowerShell-korlátozások

### <a name="azuread-module-name"></a>`AzureAD` a modul neve

A `AzureAD` modulnév jelenleg `AzureAD.Standard.Preview`, a modul adja meg ugyanazokat a funkciókat.

### <a name="sqlserver-module-functionality"></a>`SqlServer` a modul funkció

A `SqlServer` modul tartalmazza a Cloud Shellben a PowerShell Core csak előzetes támogatással rendelkezik. Különösen `Invoke-SqlCmd` még nem áll rendelkezésre.

### <a name="default-file-location-when-created-from-azure-drive"></a>Alapértelmezett helye az Azure-meghajtó létrehozása:

PowerShell-parancsmagok használatával felhasználók nem hozható létre alatt az Azure files: meghajtó. Amikor a felhasználó más eszközökkel, például vim vagy nano, új fájlok létrehozása a fájlok menti, és a `$HOME` alapértelmezés szerint. 

### <a name="gui-applications-are-not-supported"></a>Grafikus felhasználói Felülettel alkalmazások nem támogatottak.

A felhasználó futtat egy parancsot, amely egy Windows párbeszédpanel kell létrehoznia, ha egy kap hibaüzenetet például: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`.

### <a name="large-gap-after-displaying-progress-bar"></a>Miután a folyamatjelző sáv megjelenítése nagy közök

Ha a felhasználó hajt végre egy műveletet, amely megjelenik egy folyamatjelző, ezen a lapon épp, miközben a a `Azure:` meghajtó, akkor lehetséges, hogy a kurzor nincs megfelelően beállítva, és eseményáramlási kimaradást jelenik meg, ahol a folyamatjelző sáv korábban volt.

## <a name="next-steps"></a>További lépések

[A Cloud Shell hibaelhárítása](troubleshooting.md) <br>
[Rövid útmutató a Bash-hez](quickstart.md) <br>
[Rövid útmutató a PowerShellhez](quickstart-powershell.md)
