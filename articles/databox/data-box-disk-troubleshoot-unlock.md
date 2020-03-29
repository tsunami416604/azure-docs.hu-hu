---
title: Az Azure Data Box Disk hibaelhárítási lemezfeloldási problémák | Microsoft dokumentumok
description: Ez a cikk az Azure Data Box Disk szolgáltatásban jelentkező hibák elhárítását írja le.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: 02cbf64261bbfbf50561e1b7466b46b27b688e0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67148282"
---
# <a name="troubleshoot-disk-unlocking-issues-in-azure-data-box-disk"></a>Lemezfeloldási problémák elhárítása az Azure Data Box Disk-ben

Ez a cikk a Microsoft Azure Data Box Disk lemezre vonatkozik, és ismerteti a feloldási eszköz használatasorán felmerülő problémák elhárításához használt munkafolyamatokat. 


<!--## Query activity logs

Use the activity logs to find who unlocked and accessed the disks. Your Data Box Disk arrive on your premises in a locked state. You can use the device credentials available in the Azure portal for your order to unlock them.  

To figure out who accessed the **Device credentials** blade, you can query the Activity logs.  Any action that involves accessing **Device details > Credentials** blade is logged into the activity logs as `ListCredentials` action.

![Query Activity logs](media/data-box-logs/query-activity-log-1.png)-->


## <a name="data-box-disk-unlock-tool-errors"></a>A Data Box Disk lemezzárolás-feloldó eszközének hibái


| Hibaüzenet/az eszköz viselkedése      | Javaslatok                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| A jelenlegi .NET-keretrendszer nem támogatott. A 4.5-ös és újabb verziók támogatottak.<br><br>Az eszköz hibaüzenettel bezárul.  | A .NET 4.5-ös verziója nincs telepítve. Telepítse a .NET 4.5-ös verzióját a Data Box Disk zárolásának feloldására szolgáló eszközt futtató számítógépre.                                                                            |
| Nem sikerült egyetlen kötetet sem feloldani vagy ellenőrizni. Vegye fel a kapcsolatot a Microsoft támogatási szolgálatával.  <br><br>Az eszköz nem tudott egyetlen zárolt meghajtót sem feloldani vagy ellenőrizni. | Az eszköz egyik zárolt meghajtót sem tudta feloldani a megadott hozzáférési kulccsal. A további lépésekhez kérjen segítséget a Microsoft ügyfélszolgálatától.                                                |
| Az alábbi kötetek lettek feloldva és ellenőrizve. <br>Kötetek meghajtóbetűjele: E:<br>A következő hozzáférési kulcsokkal egyetlen kötetet sem sikerült feloldani: werwerqomnf, qwerwerqwdfda <br><br>Az eszköz felold egyes meghajtókat, és listázza a sikeres és sikertelen meghajtók betűjelét.| Részleges siker. Egyes meghajtókat nem sikerült feloldani a megadott hozzáférési kulccsal. A további lépésekhez kérjen segítséget a Microsoft ügyfélszolgálatától. |
| Az eszköz nem talált zárolt köteteket. Ellenőrizze, hogy a Microsofttól kapott lemez megfelelően csatlakoztatva és zárolt állapotban van-e.          | Az eszköz nem talált egyetlen zárolt meghajtót sem. A meghajtók már fel lettek oldva, vagy a rendszer nem észleli őket. Győződjön meg arról, hogy a meghajtók csatlakoztatva vannak és zároltak.                                                           |
| Végzetes hiba: Érvénytelen paraméter<br>Paraméter neve: invalid_arg<br>HASZNÁLAT:<br>DataBoxDiskUnlock /PassKeys:<passkey_list_separated_by_semicolon><br><br>Példa: DataBoxDiskUnlock /PassKeys:passkey1;passkey2;passkey3<br>Példa: DataBoxDiskUnlock /SystemCheck<br>Példa: DataBoxDiskUnlock /Help<br><br>/PassKeys:       A hozzáférési kulcsot az Azure Data Box Disk-rendelésből töltheti le. A hozzáférési kulccsal oldhatók fel a lemezek.<br>/Help:           Ez a beállítás a parancsmagok használatával kapcsolatos súgóinformációkat és példákat kínál.<br>/SystemCheck:    Ez a beállítás ellenőrzi, hogy a rendszer megfelel-e az eszköz futtatásához szükséges követelményeknek.<br><br>A kilépéshez nyomja le bármelyik billentyűt. | Érvénytelen paraméter lett megadva. Az egyetlen engedélyezett paraméter a /SystemCheck, a /PassKey és a /Help.|


## <a name="unlock-issues-for-disks-when-using-a-windows-client"></a>A lemezekkel kapcsolatos problémák feloldása Windows-ügyfél használata esetén

Ez a szakasz a Data Box Disk telepítése során felmerülő legfontosabb problémákat részletezi, amikor Windows-ügyfelet használ adatmásolásra.

### <a name="issue-could-not-unlock-drive-from-bitlocker"></a>Probléma: Nem oldható fel a meghajtó feloldása a BitLocker-ből
 
**Okoz** 

A jelszót a BitLocker párbeszédpanelen használta, és megpróbálta feloldani a lemez zárolását a BitLocker zárolásfeloldási meghajtók párbeszédpanelen keresztül. Ez nem működne.

**Resolution** (Osztás)

A Data Box Lemezek zárolásának feloldásához a Data Box Disk Unlock eszközt kell használnia, és meg kell adnia a jelszót az Azure Portalról. További információ: [Az Azure Data Box Disk kicsomagolása, csatlakoztatása és zárolásának feloldása](data-box-disk-deploy-set-up.md#connect-to-disks-and-get-the-passkey)című webhelyen talál.
 
### <a name="issue-could-not-unlock-or-verify-some-volumes-contact-microsoft-support"></a>Probléma: Nem oldható fel a kötetek zárolása vagy ellenőrzése. Vegye fel a kapcsolatot a Microsoft támogatási szolgálatával.
 
**Okoz**

A következő hiba jelenhet meg a hibanaplóban, és nem tud feloldani vagy ellenőrizni bizonyos köteteket.

`Exception System.IO.FileNotFoundException: Could not load file or assembly 'Microsoft.Management.Infrastructure, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies. The system cannot find the file specified.`
 
Ez azt jelzi, hogy valószínűleg hiányzik a Windows PowerShell megfelelő verziója a Windows-ügyfélen.

**Resolution** (Osztás)

Telepítheti a [Windows PowerShell 5.0-s és](https://www.microsoft.com/download/details.aspx?id=54616) újra a műveletet.
 
Ha továbbra sem tudja feloldani a kötetek zárolását, másolja a naplókat a Data Box Disk Unlock eszközt tartalmazó mappából, és lépjen kapcsolatba a [Microsoft támogatási szolgálatával.](data-box-disk-contact-microsoft-support.md)

## <a name="next-steps"></a>További lépések

- További információ az [érvényesítési problémák elhárításáról.](data-box-disk-troubleshoot.md)
