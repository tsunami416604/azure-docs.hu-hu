---
title: "Hozzon létre egy StorSimple támogatási csomagot |} Microsoft Docs"
description: "Megtudhatja, hogyan létrehozása és szerkesztése a StorSimple eszköz támogatási csomag visszafejtésére."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: eac76f5f-5db1-4c92-af8c-54053b91e66c
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: 32d20e7a8adcfc646c592213fe7395b87a93c985
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-a-storsimple-support-package"></a>Létrehozását és kezelését egy StorSimple támogatási csomag
## <a name="overview"></a>Áttekintés
StorSimple támogatási csomag olyan könnyen használható mechanizmus, amely gyűjti az összes vonatkozó naplókat, a StorSimple eszköz problémák elhárítása a Microsoft Support segítésére. A gyűjtött naplók titkosított és tömörített.

Ez az oktatóanyag létrehozásához és kezeléséhez a támogatási csomag részletes útmutatást tartalmaz.

## <a name="create-and-upload-a-support-package-in-the-azure-classic-portal"></a>Hozzon létre, és töltse fel a klasszikus Azure portálon támogatási csomag
Hozzon létre, és töltse fel egy támogatási csomag a Microsoft Support webhelyén keresztül a **karbantartási** a szolgáltatást a klasszikus Azure portálra a lap.

> [!NOTE]
> A feltöltés támogatási hitelesítő kulcs szükséges. A támogatási szakértőhöz biztosítania kell a Önnek e-mailben.
> 
> 

Egy titkosított és tömörített támogatási csomagot (.cab fájl) létrehozása és a támogatási webhely feltöltött. A támogatási szakember majd beolvasni a csomag a támogatási webhelyről a hiba elhárításához.

Hajtsa végre a következő lépéseket a klasszikus portálon hozzon létre egy támogatási csomagot.

#### <a name="to-create-a-support-package-in-the-azure-classic-portal"></a>Támogatási csomag létrehozása a klasszikus Azure portálon
1. Válassza ki **eszközök** > **karbantartási**.
2. Az a **támogatási csomag** szakaszban jelölje be **létrehozása és feltöltése támogatási csomag**.
3. Az a **létrehozása és feltöltése támogatási csomag** párbeszédpanelen tegye a következőket:
   
    ![Támogatási csomag létrehozása](./media/storsimple-create-manage-support-package/IC740923.png)
   
   * Az a **támogatási hozzáférési kulcs** szöveg mezőbe írja be a hozzáférési kulcsot. A Microsoft támogatási szakértőhöz küldjön-e a hitelesítő kulcs Önnek e-mailben.
   * Jelölje be a jelölőnégyzetet a hozzájárulásukat adják meg a támogatási csomag automatikusan feltölteni a Microsoft Support webhelyén.
   * Kattintson a pipa ikonra ![Pipa ikon](./media/storsimple-create-manage-support-package/IC740895.png).

## <a name="manually-create-a-support-package"></a>Manuálisan hozzon létre egy támogatási csomag
Bizonyos esetekben lesz szüksége a StorSimple a támogatási csomag a Windows PowerShell segítségével manuálisan létrehozásához. Példa:

* Ha el kell távolítania a bizalmas adatokat a naplófájlokból Microsoft Support megosztása előtt.
* Ha a csatlakozási problémák miatt a csomag feltöltése.

A manuálisan létrehozott támogatási csomag Microsoft Support keresztül e-mailek megoszthatja. A következő lépésekkel hozzon létre egy támogatási csomag a Windows PowerShell StorSimple.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Egy támogatási csomag létrehozása a Windows PowerShell StorSimple
1. Indítsa el a Windows PowerShell-munkamenetet a távoli számítógépen a StorSimple eszköz való kapcsolódáshoz használt rendszergazdaként, írja be a következő parancsot:
   
    `Start PowerShell`
2. A Windows PowerShell-munkamenetben kapcsolódjon a SSAdmin konzol, az eszköz:
   
   * A parancssorba írja be:
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   * A megnyíló párbeszédpanelen írja be az eszköz rendszergazdai jelszava. Az alapértelmezett jelszava:
     
      `Password1`
     
      ![PowerShell-hitelesítő adat párbeszédpanel](./media/storsimple-create-manage-support-package/IC740962.png)
   * Kattintson az **OK** gombra.
   * A parancssorba írja be:
     
      `Enter-PSSession $MS`
3. A megnyitott munkamenetben adja meg a megfelelő parancsot.
   
   * Jelszóval védett hálózati megosztások adja meg:
     
       `Export-HcsSupportPackage –PackageTag "MySupportPackage" –Credential "Username" -Force`
     
       Kéri a jelszót, a hálózati megosztott mappa, és a titkosítás jelszavát elérési útját (mert a támogatási csomag titkosított). Egy támogatási csomag a megadott mappában létrejön.
   * Az megosztások, amelyek nem jelszóval védett, nem kell a `-Credential` paraméter. Írja be a következőket:
     
       `Export-HcsSupportPackage –PackageTag "MySupportPackage" -Force`
     
       A támogatási csomag mindkét vezérlők, a megadott hálózati megosztott mappában jön létre. Egy titkosított, tömörített fájlt, amely a Microsoft Support hibaelhárítási elküldhető. További információkért lásd: [forduljon a Microsoft ügyfélszolgálatához](storsimple-contact-microsoft-support.md).

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Az Export-HcsSupportPackage parancsmag-paraméterek
A következő paraméterek az Export-HcsSupportPackage parancsmag használható.

| Paraméter | Kötelező/választható | Leírás |
| --- | --- | --- |
| `-Path` |Szükséges |Adja meg a helyét, a hálózati megosztott mappa, amelyben a támogatási csomag kerül segítségével. |
| `-EncryptionPassphrase` |Szükséges |Használja a jelszó segítségével titkosítja a támogatási csomag szükséges. |
| `-Credential` |Optional |Használja a hálózati megosztott mappa, a hozzáférési hitelesítő adatok megadását. |
| `-Force` |Optional |Használja a titkosítási jelszót megerősítési lépés kihagyásához. |
| `-PackageTag` |Optional |Ezzel adhatja meg a könyvtárat *elérési* , amely a támogatási csomag el van helyezve a. Az alapértelmezett érték a [eszköznév]-[aktuális dátumot és time:yyyy-MM-dd-HH-mm-ss]. |
| `-Scope` |Optional |Adja meg a **fürt** (alapértelmezett) hozzon létre egy támogatási csomag mindkét vezérlők. Ha azt szeretné, csak az aktuális tartományvezérlő a csomag létrehozásához, adja meg a **vezérlő**. |

## <a name="edit-a-support-package"></a>Egy támogatási csomag szerkesztése
Miután létrehozta a támogatási csomag, szükség lehet a bizalmas adatokat a csomag szerkesztésével. Ez magában foglalhatja kötetnevek eszköz IP-címek és a naplófájlok biztonsági mentési nevek.

> [!IMPORTANT]
> Csak akkor szerkeszthető egy támogatási csomag, amelyik a Windows PowerShell segítségével a StorSimple jött létre. A StorSimple Manager szolgáltatásban a klasszikus Azure portálon létrehozott csomagot nem szerkeszthetők.
> 
> 

Egy támogatási csomag a Microsoft Support helyen feltöltés előtt szerkesztéséhez először visszafejtése a támogatási csomag, szerkesztheti a fájlokat és újbóli titkosítására a azt. A következő lépésekkel.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>A StorSimple egy támogatási csomag a Windows PowerShell szerkesztése
1. Egy támogatási csomagot leírtak korábban [egy támogatási csomag létrehozása a Windows PowerShell StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).
2. [Töltse le a parancsfájl](http://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) helyileg az ügyfélen.
3. A Windows PowerShell-modul importálásához. Adja meg a helyi mappát, amelybe letöltötte a parancsfájl elérési útja. Importálja a modult, írja be a következőt:
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. A fájlok *.aes* titkosított és tömörített fájlok. Kibontani és fejti vissza a fájlokat, írja be a következőt:
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    Vegye figyelembe, hogy a tényleges kiterjesztések jelennek meg a fájlok.
   
    ![Támogatási csomag szerkesztése](./media/storsimple-create-manage-support-package/IC750706.png)
5. Amikor a rendszer kéri, a titkosítás jelszavát, adja meg a jelszót, amelyet a támogatási csomag létrehozásakor használja.
   
        cmdlet Open-HcsSupportPackage at command pipeline position 1
   
        Supply values for the following parameters:EncryptionPassphrase: ****
6. Tallózással keresse meg a naplófájlokat tartalmazó mappát. Mivel a naplófájlok most kibontása és visszafejtése, ezek lesz eredeti fájlkiterjesztés. Módosíthatja bármely adatok, például a kötet neve és az eszköz IP-címek, távolítsa el ezeket a fájlokat, és menti a fájlokat.
7. Zárja be a fájlok tömörítése a gzip és az AES-256 titkosítani. Ez a sebesség és a biztonság a támogatási csomag átvitele a hálózaton keresztül. Tömöríti, és titkosíthatják a fájlokat, írja be a következőket:
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![Támogatási csomag szerkesztése](./media/storsimple-create-manage-support-package/IC750707.png)
8. Amikor a rendszer kéri, adja meg a módosított támogatási csomag titkosítás jelszavát.
   
        cmdlet Close-HcsSupportPackage at command pipeline position 1
        Supply values for the following parameters:EncryptionPassphrase: ****
9. Írja le az új jelszót, úgy, hogy a Microsoft Support kérésekor megoszthatja azt.

### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Példa: Egy támogatási csomag jelszóval védett megosztott fájlok szerkesztése
A következő példa bemutatja, hogyan visszafejtése, szerkesztése és egy támogatási csomag újbóli titkosítására.

        PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1

        PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Close-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32>

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [támogatási csomag és eszköznaplók segítségével eszköz hibaelhárítás](storsimple-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).
* Megtudhatja, hogyan [felügyelete a StorSimple eszközt a StorSimple Manager szolgáltatás segítségével](storsimple-manager-service-administration.md).

