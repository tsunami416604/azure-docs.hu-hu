---
title: Távoli csatlakozás a StorSimple-eszköz |} A Microsoft Docs
description: Ismerteti, hogyan konfigurálta az eszközt, a távfelügyelethez és kapcsolódás HTTP vagy HTTPS használatával a storsimple-höz készült Windows PowerShell.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/02/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 05bec60f4c56c98e9b910b50e858656a2e5554b2
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816492"
---
# <a name="connect-remotely-to-your-storsimple-8000-series-device"></a>Távoli csatlakozás a StorSimple 8000 sorozatú eszköz

## <a name="overview"></a>Áttekintés

Az eszköz Windows PowerShell használatával távolról csatlakozhat. Csatlakozás, ha nem látja el egy menü. (Látni egy menü csak akkor, ha a soros konzol az eszközön való kapcsolódáshoz használ.) A Windows PowerShell-táveléréssel akkor csatlakozik egy adott futási teret is. Adja meg a megjelenítési nyelvét is.

Az eszköz kezelését Windows PowerShell-távelérés használatával kapcsolatos további információkért lépjen [használja a Windows Powershellt a StorSimple-eszköz felügyelete a storsimple](storsimple-8000-windows-powershell-administration.md).

Ez az oktatóanyag azt ismerteti, hogyan konfigurálta az eszközt, a távfelügyelethez, majd hogyan csatlakozhat Windows PowerShell storsimple-höz készült. HTTP- vagy HTTPS használatával távolról csatlakozzon a Windows Powershellen keresztül. Azonban amikor azt fontolgatja, hogyan csatlakozhat Windows PowerShell storsimple-höz készült, vegye figyelembe a következő információkat:

* Az eszköz soros konzoljához való közvetlen csatlakozás lehetőségét biztonságos, de nincs csatlakozás soros konzolon hálózati kapcsolók keresztül. Legyen óvatos az a biztonsági veszély a hálózati kapcsolók keresztül az eszköz soros konzoljához való csatlakozáskor.
* HTTP-munkameneten keresztül csatlakozik, előfordulhat, hogy csatlakozás soros konzolon keresztül, a hálózaton keresztül nagyobb biztonságot kínálnak. Bár ez nem a legbiztonságosabb módszer, egy megbízható hálózatokon feltételei.
* Kapcsolaton keresztül egy HTTPS-KAPCSOLATON keresztül, önaláírt tanúsítvánnyal, a legbiztonságosabb és a javasolt megoldás.

Távolról is csatlakozhasson a Windows PowerShell-felületen. A StorSimple-eszköz a Windows PowerShell felületéről való elérése alapértelmezés szerint azonban nincs engedélyezve. Engedélyeznie kell az eszköz távoli kezeléséhez először, és ezután az ügyfélen használt az eszköz elérésére.

Ebben a cikkben leírt lépéseket a Windows Server 2012 R2 rendszerű gazdagépet rendszerre lettek végrehajtva.

## <a name="connect-through-http"></a>HTTP Protokollon keresztül csatlakozni

Csatlakozás Windows PowerShell storsimple-höz készült egy HTTP-munkameneten keresztül kínál a nagyobb biztonságot, a StorSimple-eszköz soros konzolon keresztül kapcsolódik. Bár ez nem a legbiztonságosabb módszer, egy megbízható hálózatokon feltételei.

Az Azure Portalon vagy a soros konzol segítségével távoli felügyeletének konfigurálása. Az alábbi eljárások közül választhat:

* Az Azure portal használatával HTTP protokollon keresztüli távoli felügyeletének engedélyezése
* [A soros konzol használata a távoli felügyelet engedélyezéséhez a HTTP-n keresztül](#use-the-serial-console-to-enable-remote-management-over-http)

Miután engedélyezte távfelügyeletet, az ügyfélszoftver előkészítése a távoli kapcsolat a következő eljárás használatával.

* [Az ügyfélszoftver előkészítése a távoli kapcsolat](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-portal-to-enable-remote-management-over-http"></a>Az Azure portal használatával HTTP protokollon keresztüli távoli felügyeletének engedélyezése

A következő lépésekkel távoli felügyelet engedélyezéséhez a HTTP-n keresztül az Azure Portalon.

#### <a name="to-enable-remote-management-through-the-azure-portal"></a>Az Azure Portalon keresztül távoli felügyeletének engedélyezése

1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. Válassza ki **eszközök** majd válassza ki, és kattintson a távoli felügyelet a konfigurálni kívánt eszközre. Lépjen a **beállítások > Biztonság**.
2. Az a **biztonsági beállítások** panelen kattintson a **Távfelügyelet**.
3. Az a **Távfelügyelet** panelen adja meg **távoli felügyelet engedélyezése** való **Igen**.
4. Mostantól HTTP-n keresztül is csatlakozhat. (Az alapértelmezett érték HTTPS-kapcsolaton keresztül csatlakozni.) Győződjön meg arról, hogy HTTP van kiválasztva.
   
   > [!NOTE]
   > A HTTP-n keresztüli csatlakozást kizárólag megbízható hálózatokon célszerű engedélyezni.
   
5. Kattintson a **mentése** , amikor a rendszer megerősítést kér, válassza ki **Igen**.

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>A soros konzol használata a távoli felügyelet engedélyezéséhez a HTTP-n keresztül
A következő lépésekkel a távoli felügyeletének engedélyezése az eszköz soros konzoljában.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Az eszköz soros konzolon keresztül távoli felügyeletének engedélyezése
1. A soros konzol menüben válassza az 1. lehetőség. Az eszközön a soros konzol használatával kapcsolatos további információkért lépjen [Windows PowerShell storsimple-eszköz soros konzolon keresztül csatlakozhat](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. Írja be a parancssorba: `Enable-HcsRemoteManagement –AllowHttp`
3. A biztonsági réseket, HTTP protokollon keresztül csatlakozni az eszközhöz kapcsolatos értesítést kap. Amikor a rendszer kéri, erősítse meg, írja be **Y**.
4. Győződjön meg arról, hogy HTTP engedélyezve van-e beírásával: `Get-HcsSystem`
5. Ellenőrizze, hogy a **RemoteManagementMode** mezőben látható **HttpsAndHttpEnabled**. A következő ábrán ezek a beállítások a putty-kapcsolaton keresztül.
   
     ![Soros HTTPS és HTTP engedélyezve](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>Az ügyfélszoftver előkészítése a távoli kapcsolat
A következő lépésekkel távoli felügyeletének engedélyezése az ügyfélen.

#### <a name="to-prepare-the-client-for-remote-connection"></a>Az ügyfélszoftver előkészítése a távoli kapcsolat
1. Indítsa el a Windows PowerShell-munkamenetet rendszergazdaként. Ha a Windows 10-es ügyfél segítségével alapértelmezés szerint a Windows Rendszerfelügyeleti webszolgáltatás manuális értéke. Szükség lehet a szolgáltatás elindításához írja be:

    `Start-Service WinRM`
    
2. Írja be a következő parancsot a StorSimple-eszköz IP-cím hozzáadásához az ügyfél megbízható gazdagépek listájához:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
     Cserélje le <*device_ip*> IP-címét az eszköz; például: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Írja be a következő parancsot a hitelesítő adatai mentése változóként: 
   
    ```
    $cred = Get-Credential
    ```
    
4. A megjelenő párbeszédpanelen:
   
   1. Írja be a felhasználónevet a következő formátumban: *device_ip\SSAdmin*.
   2. Írja be az eszköz rendszergazdai jelszava, amely lett beállítva, amikor az eszköz a telepítővarázsló lett konfigurálva. Az alapértelmezett jelszó az *jelszó1*.
5. Ez a parancs beírásával indítsa el a Windows PowerShell-munkamenetben az eszközön:
   
     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`
   
   > [!NOTE]
   > A StorSimple virtuális eszköz egy Windows PowerShell-munkamenetben használja a létrehozásához, fűzze hozzá a `–Port` paraméter, és adja meg, amely a StorSimple virtuális készülék lévő Remoting konfigurált nyilvános port.
   
   
Ezen a ponton rendelkeznie kell egy aktív távoli Windows PowerShell-munkamenetben az eszközön.
   
![PowerShell távoli eljáráshívás HTTP-n keresztül](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>Csatlakozás a HTTPS-en keresztül

Csatlakozás Windows PowerShell storsimple-höz készült keresztül HTTPS-KAPCSOLATON keresztül a legbiztonságosabb és ajánlott módszer távolról kapcsolódni a Microsoft Azure StorSimple-eszköz. A következő eljárások azt ismertetik, hogyan állíthat be a soros konzol és az ügyfél-számítógépek így HTTPS használatával csatlakozni a Windows PowerShell storsimple-höz készült.

Az Azure Portalon vagy a soros konzol segítségével távoli felügyeletének konfigurálása. Az alábbi eljárások közül választhat:

* Távoli felügyelet engedélyezéséhez a HTTPS-kapcsolaton keresztül az Azure portal használatával
* [A soros konzol használata a távoli felügyelet engedélyezéséhez a HTTPS-kapcsolaton keresztül](#use-the-serial-console-to-enable-remote-management-over-https)

Miután engedélyezte távfelügyeletet, az alábbi eljárásokkal készítheti elő a gazdagép egy távoli felügyeleti és a távoli állomástól csatlakozni az eszközhöz.

* [Készítse elő a gazdagép távoli felügyeletére](#prepare-the-host-for-remote-management)
* [A távoli állomástól csatlakozni az eszközhöz](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-portal-to-enable-remote-management-over-https"></a>Távoli felügyelet engedélyezéséhez a HTTPS-kapcsolaton keresztül az Azure portal használatával

Hajtsa végre az alábbi lépéseket az Azure Portalon a távoli felügyelet engedélyezéséhez a HTTPS-kapcsolaton keresztül.

#### <a name="to-enable-remote-management-over-https-from-the-azure-portal"></a>Távoli felügyelet engedélyezéséhez a HTTPS-kapcsolaton keresztül az Azure Portalról

1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. Válassza ki **eszközök** majd válassza ki, és kattintson a távoli felügyelet a konfigurálni kívánt eszközre. Lépjen a **beállítások > Biztonság**.
2. Az a **biztonsági beállítások** panelen kattintson a **Távfelügyelet**.
3. A **Távoli felügyelet engedélyezése** lehetőségnél válassza az **Igen** beállítást.
4. Ezután eldöntheti, HTTPS-en keresztül csatlakozni. (Az alapértelmezett érték HTTPS-kapcsolaton keresztül csatlakozni.) Győződjön meg arról, hogy HTTPS van-e kiválasztva.
5. ... Elemre majd **távfelügyeleti tanúsítvány letöltése**. Adja meg a fájl mentésének helyét. A tanúsítvány telepítése az ügyfélszámítógépre vagy gazdagépre számítógépen szeretne csatlakozni az eszközhöz használt kell.
6. Kattintson a **mentése** majd **Igen** , amikor a rendszer megerősítést kér.

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>A soros konzol használata a távoli felügyelet engedélyezéséhez a HTTPS-kapcsolaton keresztül

A következő lépésekkel a távoli felügyeletének engedélyezése az eszköz soros konzoljában.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Az eszköz soros konzolon keresztül távoli felügyeletének engedélyezése
1. A soros konzol menüben válassza az 1. lehetőség. Az eszközön a soros konzol használatával kapcsolatos további információkért lépjen [Windows PowerShell storsimple-eszköz soros konzolon keresztül csatlakozhat](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. Írja be a parancssorba:
   
     `Enable-HcsRemoteManagement`
   
    Ez engedélyezze a HTTPS az eszközön.
3. Győződjön meg arról, hogy engedélyezni a HTTPS-beírásával: 
   
     `Get-HcsSystem`
   
    Győződjön meg arról, hogy a **RemoteManagementMode** mezőben látható **HttpsEnabled**. A következő ábrán ezek a beállítások a putty-kapcsolaton keresztül.
   
     ![Soros HTTPS-kompatibilis](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)
4. Kimenetéből származó `Get-HcsSystem`, másolja be az eszköz sorozatszáma, és mentse későbbi használatra.
   
   > [!NOTE]
   > A sorozatszám képez az a tanúsítvány CN-neve.
   
5. Szerezze be a távfelügyeleti tanúsítvány beírásával: 
   
     `Get-HcsRemoteManagementCert`
   
    Egy tanúsítványt, az alábbihoz hasonlóan jelenik meg.
   
    ![Távfelügyeleti tanúsítvány beszerzése](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)
6. Másolja át az adatokat a tanúsítványban lévő **---BEGIN CERTIFICATE---** való **---END CERTIFICATE---** egy szövegszerkesztőbe például a Jegyzettömbben, és mentse azt egy .cer fájlba. (, Másolja ezt a fájlt a távoli állomás amikor előkészíti a gazdagépet.)
   
   > [!NOTE]
   > Létrehoz egy új tanúsítványt, használja a `Set-HcsRemoteManagementCert` parancsmagot.
   
### <a name="prepare-the-host-for-remote-management"></a>Készítse elő a gazdagép távoli felügyeletére

Előkészítheti a számítógép a távoli kapcsolat, amely HTTPS-KAPCSOLATON keresztül használja, hajtsa végre az alábbi eljárásokat:

* [A .cer fájlt importálja a gyökérszintű tárolóban. az ügyfél vagy a távoli állomás](#to-import-the-certificate-on-the-remote-host).
* [Az eszköz-sorozatszámok hozzáadása az állomásleíró fájlhoz, a távoli gazdagépen](#to-add-device-serial-numbers-to-the-remote-host).

A fenti eljárások leírását alább.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>Importálja a tanúsítványt a távoli gazdagépen
1. Kattintson a jobb gombbal a .cer fájlt, és válassza ki **telepítése tanúsítvány**. A Tanúsítványimportáló varázsló elindul.
   
    ![Tanúsítványimportáló varázsló 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)
2. A **hely Store**válassza **helyi gépen**, és kattintson a **tovább**.
3. Válassza ki **minden tanúsítvány tárolása ebben a tárolóban**, és kattintson a **Tallózás**. Keresse meg a gyökérszintű tárolóban. a távoli állomás, és kattintson a **tovább**.
   
    ![Tanúsítványimportáló varázsló 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)
4. Kattintson a **Befejezés** gombra. Megjelenik egy üzenet, amely tájékoztatja, hogy az importálás sikeres volt-e.
   
    ![Tanúsítványimportáló varázsló 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>Az eszközök sorozatszámait a távoli állomás hozzáadása
1. Indítsa el a Jegyzettömböt rendszergazdaként, és nyissa meg a hosts fájl a \Windows\System32\Drivers\etc helyen található.
2. Adja hozzá a következő három bejegyzést az állomásleíró fájlhoz: **DATA 0 IP-cím**, **0. vezérlő rögzített IP-cím**, és **vezérlő 1 fix IP-cím**.
3. Adja meg a korábban mentett sorozatszámát. Képezze le ezt az IP-cím a következő képen látható módon. Vezérlő 0 és a vezérlő 1 hozzáfűzése **Controller0** és **Controller1** a végén a sorozatszám (CN-név).
   
    ![CN-név hozzáadása az állomásleíró fájlhoz](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)
4. Mentse a hosts fájlt.

### <a name="connect-to-the-device-from-the-remote-host"></a>A távoli állomástól csatlakozni az eszközhöz

Windows PowerShell és az SSL használatával adja meg az SSAdmin munkamenet az eszközön a távoli gazdagépen vagy-ügyfélről. A SSAdmin munkamenet képez le az 1. lehetőséget a [soros konzol](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) menü az eszköz.

Az alábbi eljárás végrehajtásához a számítógépen, amelyről szeretné a távoli Windows PowerShell-kapcsolat.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-ssl"></a>A Windows PowerShell és az SSL használatával adja meg az SSAdmin munkamenet az eszközön
1. Indítsa el a Windows PowerShell-munkamenetet rendszergazdaként. Ha a Windows 10-es ügyfél segítségével alapértelmezés szerint a Windows Rendszerfelügyeleti webszolgáltatás manuális értéke. Szükség lehet a szolgáltatás elindításához írja be:

    `Start-Service WinRM`

2. Az ügyfél megbízható gazdagépek írja be az eszköz IP-cím hozzáadása:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
    Ahol <*device_ip*> eszköz IP-cím; például: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Hozzon létre egy új hitelesítő adat, írja be:
   
     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`
   
    Ahol <*IP-címét a céleszközön*> az adatok 0 az eszközéhez; IP-címe például **10.126.173.90** a hosts fájlt az előző képen látható módon. Emellett adja meg az eszköz rendszergazdai jelszavát.
4. Hozzon létre egy munkamenetet beírásával:
   
     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`
   
    A parancsmag a - ComputerName paraméter adja meg a <*target eszköz sorozatszáma*>. Ezzel a sorozatszámmal is hozzá van rendelve a DATA 0 a hosts fájl a távoli gazdagépen; IP-címe Ha például **SHX0991003G44MT** az alábbi képen látható módon.
5. Típus:
   
     `Enter-PSSession $session`
6. Néhány percet várnia kell, és ezután meg csatlakozni fog a HTTPS-kapcsolaton keresztül az eszköz SSL-en keresztül. Megjelenik egy üzenet, amely azt jelzi, hogy az eszköz csatlakozik.
   
    ![PowerShell távoli eljáráshívás a HTTPS és SSL](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [Windows PowerShell-lel történő felügyelete a StorSimple-eszköz](storsimple-8000-windows-powershell-administration.md).
* Tudjon meg többet [a StorSimple-Eszközkezelő szolgáltatás használata a StorSimple-eszköz felügyeletéhez](storsimple-8000-manager-service-administration.md).

