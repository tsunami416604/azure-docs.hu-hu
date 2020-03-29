---
title: StorSimple 8000 sorozatú támogatási csomag létrehozása
description: Ismerje meg, hogyan hozhat létre, fejthet vissza és szerkesztheti a StorSimple 8000 sorozatú eszköz támogatási csomagját.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 9ca033f6f786c0142261dafa31b93b71a8b3336a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277078"
---
# <a name="create-and-manage-a-support-package-for-storsimple-8000-series"></a>A StorSimple 8000 sorozat támogatási csomagjának létrehozása és kezelése

## <a name="overview"></a>Áttekintés

A StorSimple támogatási csomag egy könnyen használható mechanizmus, amely összegyűjti az összes vonatkozó naplókat, hogy segítse a Microsoft-támogatás a StorSimple-eszközökkel kapcsolatos problémák elhárításában. Az összegyűjtött naplók titkosítottak és tömörített.

Ez az oktatóanyag lépésenkénti utasításokat tartalmaz a StorSimple 8000 sorozatú eszköz támogatási csomagjának létrehozásához és kezeléséhez. Ha egy StorSimple virtuális tömbbel dolgozik, lépjen [naplócsomag létrehozásához.](storsimple-ova-web-ui-admin.md#generate-a-log-package)

## <a name="create-a-support-package"></a>Támogatási csomag létrehozása

Bizonyos esetekben manuálisan kell létrehoznia a támogatási csomagot a Windows PowerShell storSimple-hez. Példa:

* Ha a Microsoft támogatási szolgálatával való megosztás előtt el kell távolítania a bizalmas adatokat a naplófájlokból.
* Ha kapcsolódási problémák miatt nehézségei vannak a csomag feltöltésével.

A manuálisan létrehozott támogatási csomagot megoszthatja a Microsoft támogatási szolgálatával e-mailben. Hajtsa végre az alábbi lépéseket egy támogatási csomag létrehozásához a Windows PowerShell storSimple-hez.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Támogatási csomag létrehozása a StorSimple-hez létrehozott Windows PowerShellben

1. Ha rendszergazdaként szeretne windowsOs PowerShell-munkamenetet indítani a StorSimple-eszközhöz való csatlakozáshoz használt távoli számítógépen, írja be a következő parancsot:
   
    `Start PowerShell`
2. A Windows PowerShell-munkamenetben csatlakozzon az eszköz SSAdmin konzoljához:
   
   1. A parancssorba írja be a következőt:
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   2. A megnyíló párbeszédpanelen adja meg az eszköz rendszergazdai jelszavát. Az alapértelmezett jelszó a _Password1_.
     
      ![A PowerShell hitelesítő adataipárbeszédpanel](./media/storsimple-8000-create-manage-support-package/IC740962.png)
   3. Válassza **az OK gombot.**
   4. A parancssorba írja be a következőt:
     
      `Enter-PSSession $MS`
3. A megnyíló munkamenetbe írja be a megfelelő parancsot.
   
   * A jelszóval védett hálózati megosztások esetén írja be a következőt:
     
       `Export-HcsSupportPackage -Path <\\IP address\location of the shared folder> -Include Default -Credential domainname\username`
     
       A rendszer kérni fogja a jelszót és a titkosítási jelszót (mivel a támogatási csomag titkosítva van). Ezután létrejön egy támogatási csomag az alapértelmezett mappában (az eszközneve az aktuális dátummal és idővel van hozzáfűzve).
   * A nem jelszóval védett megosztások esetében `-Credential` nincs szükség a paraméterre. Adja meg a következőket:
     
       `Export-HcsSupportPackage`
     
       A támogatási csomag az alapértelmezett mappában lévő mindkét vezérlőhöz létrejön. A csomag egy titkosított, tömörített fájl, amely hibaelhárítási célokra elküldhető a Microsoft támogatási szolgálatának. További információt a [Kapcsolatfelvétel a Microsoft támogatási szolgálatával című témakörben talál.](storsimple-8000-contact-microsoft-support.md)

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Az Export-HcsSupportPackage parancsmag paraméterei

A következő paramétereket használhatja az Export-HcsSupportPackage parancsmaggal.

| Paraméter | Kötelező vagy nem kötelező | Leírás |
| --- | --- | --- |
| `-Path` |Kötelező |Itt adja meg annak a megosztott hálózati mappának a helyét, amelyben a támogatási csomag található. |
| `-EncryptionPassphrase` |Kötelező |A támogatási csomag titkosításához használjon jelszót. |
| `-Credential` |Optional |A hálózati megosztott mappa hozzáférési hitelesítő adatainak megadására használható. |
| `-Force` |Optional |Itt hagyhatja ki a titkosítási jelszó-megerősítési lépést. |
| `-PackageTag` |Optional |Ezzel megadhatja azt a könyvtárat az *Elérési út csoportban,* amelyben a támogatási csomag el van helyezve. Az alapértelmezett érték az [eszköznév]-[aktuális dátum és idő:yyy-HH-dd-HH-mm-ss]. |
| `-Scope` |Optional |Adja meg **fürtként** (alapértelmezettként) a két vezérlő támogatási csomagjának létrehozásához. Ha csak az aktuális vezérlőhöz szeretne csomagot létrehozni, adja meg a **Controller (Controller**) értéket. |

## <a name="edit-a-support-package"></a>Támogatási csomag szerkesztése

Miután létrehozott egy támogatási csomagot, előfordulhat, hogy a bizalmas adatok eltávolításához szerkeszteni kell a csomagot. Ez magában foglalhatja a kötetneveket, az eszköz IP-címeit és a naplófájlokból származó biztonsági másolat neveket.

> [!IMPORTANT]
> Csak a StorSimple windows PowerShell használatával létrehozott támogatási csomagot szerkesztheti. Az Azure Portalon a StorSimple Device Manager szolgáltatással létrehozott csomagok nem szerkeszthetők.

Ha a támogatási csomagot a Microsoft támogatási webhelyére való feltöltés előtt szeretné szerkeszteni, először fejtse vissza a támogatási csomagot, szerkesztse a fájlokat, majd titkosítsa újra. Hajtsa végre a következő lépéseket.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Támogatási csomag szerkesztése a StorSimple-hez használható Windows PowerShellben

1. Hozzon létre egy támogatási csomagot a korábban leírtak szerint, a [Támogatási csomag létrehozása a Windows PowerShell storSimple-hez című fájljában.](#to-create-a-support-package-in-windows-powershell-for-storsimple)
2. [Töltse le a parancsfájlt](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) helyileg az ügyfélen.
3. Importálja a Windows PowerShell-modult. Adja meg annak a helyi mappának az elérési útját, amelyben a parancsfájlt letöltötte. A modul importálásához írja be a következőt:
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. Minden fájl *.aes* fájlokat, amelyek tömörített és titkosított. A fájlok kibontásához és visszafejtéséhez írja be a következőt:
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    Ne feledje, hogy a tényleges fájlkiterjesztések mostantól az összes fájlhoz megjelennek.
   
    ![Támogatási csomag szerkesztése](./media/storsimple-8000-create-manage-support-package/IC750706.png)
5. Amikor a rendszer kéri a titkosítási jelszót, írja be a támogatási csomag létrehozásakor használt jelszót.
   
        cmdlet Open-HcsSupportPackage at command pipeline position 1
   
        Supply values for the following parameters:EncryptionPassphrase: ****
6. Tallózással keresse meg a naplófájlokat tartalmazó mappát. Mivel a naplófájlok kivannak bontva és visszafejtve vannak, ezek eredeti fájlkiterjesztéseket tartalmaznak. Módosítsa ezeket a fájlokat, hogy eltávolítsa az ügyfélspecifikus információkat, például a kötetneveket és az eszköz IP-címeit, és mentse a fájlokat.
7. Zárja be a fájlokat tömöríteni őket gzip és titkosítani őket AES-256. Ez a támogatási csomag hálózaton keresztüli átvitelének gyorsasága és biztonsága. A fájlok tömörítéséhez és titkosításához írja be a következőket:
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![Támogatási csomag szerkesztése](./media/storsimple-8000-create-manage-support-package/IC750707.png)
8. Amikor a rendszer kéri, adjon meg egy titkosítási jelszót a módosított támogatási csomaghoz.
   
        cmdlet Close-HcsSupportPackage at command pipeline position 1
        Supply values for the following parameters:EncryptionPassphrase: ****
9. Írja le az új jelszót, hogy kérésre megoszthassa azt a Microsoft támogatási szolgálatával.

### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Példa: Fájlok szerkesztése egy támogatási csomagban jelszóval védett megosztáson

A következő példa bemutatja, hogyan lehet visszafejteni, szerkeszteni és újra titkosítani egy támogatási csomagot.

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

* További információ a [támogatási csomagban gyűjtött adatokról](https://support.microsoft.com/help/3193606/storsimple-support-packages-and-device-logs)
* Megtudhatja, hogy [miként használhatja a támogatási csomagokat és az eszköznaplókat az eszköz telepítésének elhárításához.](storsimple-8000-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting)
* Ismerje meg, hogyan [használhatja a StorSimple Eszközkezelő szolgáltatást a StorSimple-eszköz felügyeletére.](storsimple-8000-manager-service-administration.md)

