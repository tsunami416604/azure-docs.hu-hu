---
title: Az Azure Cloud Shell korlátozásai | Microsoft dokumentumok
description: Az Azure Cloud Shell korlátainak áttekintése
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
ms.openlocfilehash: 092dccab82326bb9983f11ff64fe50aee7b1084d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74951479"
---
# <a name="limitations-of-azure-cloud-shell"></a>Az Azure Cloud Shell korlátai

Az Azure Cloud Shell a következő ismert korlátozásokkal rendelkezik:

## <a name="general-limitations"></a>Általános korlátozások

### <a name="system-state-and-persistence"></a>A rendszer állapota és perzisztenciája

A Cloud Shell-munkamenetet biztosító gép ideiglenes, és a munkamenet 20 percig tartó inaktívvá vájta. A Cloud Shell csatlakoztatásához Azure-fájlmegosztásra van szükség. Ennek eredményeképpen az előfizetésnek képesnek kell lennie a Cloud Shell eléréséhez szükséges tárolási erőforrások beállítására. Egyéb szempontok közé tartozik:

* Csatlakoztatott tároló esetén csak a `$Home` könyvtáron belüli módosítások maradnak meg.
* Az Azure-fájlmegosztások csak a [hozzárendelt régióból](persisting-shell-storage.md#mount-a-new-clouddrive)csatlakoztathatók.
  * A Bash, `env` fuss, hogy `ACC_LOCATION`megtalálja a régió meg a .

### <a name="browser-support"></a>Böngészőtámogatás

A Cloud Shell támogatja a Microsoft Edge, a Microsoft Internet Explorer, a Google Chrome, a Mozilla Firefox és az Apple Safari legújabb verzióit. Privát módban a Safari nem támogatott.

### <a name="copy-and-paste"></a>Másolás és beillesztés

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Egy adott felhasználó esetében csak egy rendszerhéj lehet aktív

A felhasználók egyszerre csak egy típusú rendszerhéjat indíthatnak el, **bash** vagy **PowerShell**. Azonban előfordulhat, hogy egyszerre több bash vagy PowerShell példánya is fut. A Bash vagy a PowerShell közötti váltás a menü használatával a Cloud Shell újraindítását eredményezi, amely leállítja a meglévő munkameneteket. Másik lehetőségként futtathatja a bash-t `bash`a PowerShell-en belül a `pwsh`beírással, és futtathatja a PowerShell-t a bash-en belül a beírásával.

### <a name="usage-limits"></a>Használati korlátok

A Cloud Shell interaktív használati esetekre szolgál. Ennek eredményeképpen minden hosszú ideig futó, nem interaktív munkamenet figyelmeztetés nélkül véget ér.

## <a name="bash-limitations"></a>Bash korlátozások

### <a name="user-permissions"></a>Felhasználói engedélyek

Az engedélyek rendszeres felhasználóként vannak beállítva sudo hozzáférés nélkül. A címin `$Home` kívüli telepítés nem marad meg.

### <a name="editing-bashrc-or-profile"></a>Szerkesztés .bashrc vagy $PROFILE

Legyen óvatos a .bashrc vagy a PowerShell $PROFILE fájljának szerkesztésekor, így váratlan hibákat okozhat a Cloud Shellben.

## <a name="powershell-limitations"></a>PowerShell-korlátozások

### <a name="azuread-module-name"></a>`AzureAD`modul neve

A `AzureAD` modul neve `AzureAD.Standard.Preview`jelenleg , a modul ugyanazt a funkciót biztosítja.

### <a name="sqlserver-module-functionality"></a>`SqlServer`modul funkció

A `SqlServer` Cloud Shellben található modul csak a PowerShell Core előzetes támogatásával rendelkezik. Különösen még `Invoke-SqlCmd` nem áll rendelkezésre.

### <a name="default-file-location-when-created-from-azure-drive"></a>Alapértelmezett fájlhely az Azure-meghajtóról történő létrehozáskor:

A PowerShell-parancsmagok használatával a felhasználók nem hozhatnak létre fájlokat az Azure: meghajtó alatt. Amikor a felhasználók új fájlokat hoznak létre más eszközökkel, `$HOME` például vim vagy nano eszközökkel, a fájlok alapértelmezés szerint a rendszer ébresztésre kerülnek. 

### <a name="gui-applications-are-not-supported"></a>A GRAFIKUS alkalmazások nem támogatottak

Ha a felhasználó olyan parancsot futtat, amely Windows párbeszédpanelt hoz `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`létre, az egyik a következő hibaüzenetet látja: .

### <a name="large-gap-after-displaying-progress-bar"></a>Nagy rés a folyamatjelző sáv megjelenítése után

Ha a felhasználó olyan műveletet hajt végre, amely egy folyamatjelző sávot jelenít meg, például egy, a `Azure:` meghajtóban lévő lap befejezése, akkor lehetséges, hogy a kurzor nincs megfelelően beállítva, és egy rés jelenik meg ott, ahol a folyamatjelző sáv korábban volt.

## <a name="next-steps"></a>További lépések

[A Cloud Shell hibáinak elhárítása](troubleshooting.md) <br>
[Rövid útmutató a Bash-hez](quickstart.md) <br>
[Rövid útmutató a PowerShellhez](quickstart-powershell.md)
