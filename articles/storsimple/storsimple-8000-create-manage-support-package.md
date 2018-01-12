---
title: "A StorSimple 8000 series támogatási csomag létrehozása |} Microsoft Docs"
description: "Megtudhatja, hogyan létrehozása és szerkesztése a StorSimple 8000 series eszköz támogatási csomag visszafejtésére."
services: storsimple
documentationcenter: 
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: c83caae58e73d6f2d0fe086938e0bbc5cfdf80c4
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2018
---
# <a name="create-and-manage-a-support-package-for-storsimple-8000-series"></a>Hozzon létre és kezelheti a StorSimple 8000 Series támogatási csomag

## <a name="overview"></a>Áttekintés

StorSimple támogatási csomag olyan könnyen használható mechanizmus, amely gyűjti az összes vonatkozó naplókat, a StorSimple eszköz problémák elhárítása a Microsoft Support segítésére. A gyűjtött naplók titkosított és tömörített.

Ez az oktatóanyag létrehozásához és kezeléséhez a StorSimple 8000 series eszköz a támogatási csomag részletes útmutatást tartalmaz. Ha egy StorSimple virtuális tömbbel rendelkező dolgozik, akkor folytassa a [a napló csomagot](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="create-a-support-package"></a>Hozzon létre egy támogatási csomag

Bizonyos esetekben lesz szüksége a StorSimple a támogatási csomag a Windows PowerShell segítségével manuálisan létrehozásához. Példa:

* Ha el kell távolítania a bizalmas adatokat a naplófájlokból Microsoft Support megosztása előtt.
* Ha a csatlakozási problémák miatt a csomag feltöltése.

A manuálisan létrehozott támogatási csomag Microsoft Support keresztül e-mailek megoszthatja. A következő lépésekkel hozzon létre egy támogatási csomag a Windows PowerShell StorSimple.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Egy támogatási csomag létrehozása a Windows PowerShell StorSimple

1. Indítsa el a Windows PowerShell-munkamenetet a távoli számítógépen a StorSimple eszköz való kapcsolódáshoz használt rendszergazdaként, írja be a következő parancsot:
   
    `Start PowerShell`
2. A Windows PowerShell-munkamenetben kapcsolódjon a SSAdmin konzol, az eszköz:
   
   1. A parancssorba írja be:
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   2. A megnyíló párbeszédpanelen írja be az eszköz rendszergazdai jelszava. Az alapértelmezett jelszó _jelszó1_.
     
      ![PowerShell-hitelesítő adat párbeszédpanel](./media/storsimple-8000-create-manage-support-package/IC740962.png)
   3. Kattintson az **OK** gombra.
   4. A parancssorba írja be:
     
      `Enter-PSSession $MS`
3. A megnyitott munkamenetben adja meg a megfelelő parancsot.
   
   * Jelszóval védett hálózati megosztások adja meg:
     
       `Export-HcsSupportPackage -Path <\\IP address\location of the shared folder> -Include Default -Credential domainname\username`
     
       Kéri a jelszót és a titkosítás jelszavát (mert a támogatási csomag titkosított). Egy támogatási csomag létrejön az alapértelmezett mappába (eszköznév kiegészül az aktuális dátum és idő).
   * Az megosztások, amelyek nem jelszóval védett, nem kell a `-Credential` paraméter. Írja be a következőket:
     
       `Export-HcsSupportPackage`
     
       A támogatási csomag mindkét tartományvezérlők, az alapértelmezett mappában jön létre. A csomag egy titkosított, tömörített fájlt, amely a Microsoft Support hibaelhárítási elküldhető. További információkért lásd: [forduljon a Microsoft ügyfélszolgálatához](storsimple-8000-contact-microsoft-support.md).

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
> Csak akkor szerkeszthető egy támogatási csomag, amelyik a Windows PowerShell segítségével a StorSimple jött létre. A StorSimple eszköz-kezelő szolgáltatásban az Azure-portálon létrehozott csomagot nem szerkeszthetők.

Egy támogatási csomag a Microsoft Support helyen feltöltés előtt szerkesztéséhez először visszafejtése a támogatási csomag, szerkesztheti a fájlokat és újbóli titkosítására a azt. A következő lépésekkel.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>A StorSimple egy támogatási csomag a Windows PowerShell szerkesztése

1. Egy támogatási csomagot leírtak korábban [egy támogatási csomag létrehozása a Windows PowerShell StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).
2. [Töltse le a parancsfájl](http://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) helyileg az ügyfélen.
3. A Windows PowerShell-modul importálásához. Adja meg a helyi mappát, amelybe letöltötte a parancsfájl elérési útja. Importálja a modult, írja be a következőt:
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. A fájlok *.aes* titkosított és tömörített fájlok. Kibontani és fejti vissza a fájlokat, írja be a következőt:
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    Vegye figyelembe, hogy a tényleges kiterjesztések jelennek meg a fájlok.
   
    ![Támogatási csomag szerkesztése](./media/storsimple-8000-create-manage-support-package/IC750706.png)
5. Amikor a rendszer kéri, a titkosítás jelszavát, adja meg a jelszót, amelyet a támogatási csomag létrehozásakor használja.
   
        cmdlet Open-HcsSupportPackage at command pipeline position 1
   
        Supply values for the following parameters:EncryptionPassphrase: ****
6. Tallózással keresse meg a naplófájlokat tartalmazó mappát. Mivel a naplófájlok most kibontása és visszafejtése, ezek lesz eredeti fájlkiterjesztés. Módosíthatja bármely adatok, például a kötet neve és az eszköz IP-címek, távolítsa el ezeket a fájlokat, és menti a fájlokat.
7. Zárja be a fájlok tömörítése a gzip és az AES-256 titkosítani. Ez a sebesség és a biztonság a támogatási csomag átvitele a hálózaton keresztül. Tömöríti, és titkosíthatják a fájlokat, írja be a következőket:
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![Támogatási csomag szerkesztése](./media/storsimple-8000-create-manage-support-package/IC750707.png)
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

## <a name="next-steps"></a>További lépések

* További tudnivalók a [gyűjtött információkat a támogatási csomag](https://support.microsoft.com/help/3193606/storsimple-support-packages-and-device-logs)
* Megtudhatja, hogyan [támogatási csomag és eszköznaplók segítségével eszköz hibaelhárítás](storsimple-8000-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).
* Megtudhatja, hogyan [felügyelete a StorSimple eszközt a StorSimple Device Manager szolgáltatással](storsimple-8000-manager-service-administration.md).

