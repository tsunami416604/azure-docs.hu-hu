---
title: "Távoli csatlakozás a StorSimple eszköz |} Microsoft Docs"
description: "Távoli kezelésére szolgál az eszköz konfigurálása és a StorSimple HTTP vagy HTTPS PROTOKOLLON keresztül és a Windows PowerShell összekapcsolása mutatja be."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/07/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ff76884f020a0fb8a1b48bd371c419bd65e85fd3
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="connect-remotely-to-your-storsimple-8000-series-device"></a>Távoli csatlakozás a StorSimple 8000 series eszköz

## <a name="overview"></a>Áttekintés

Az eszköz Windows PowerShell használatával távolról csatlakozhat. Ezzel a módszerrel csatlakoztatásakor nem látja a menüben. (Megjelenik a menü csak akkor, ha a soros konzol az eszközön való csatlakozáshoz használ.) A Windows PowerShell-távelérést csatlakozhat egy adott futási teret. A megjelenítési nyelv is megadható.

Az eszköz kezelését Windows PowerShell távoli eljáráshívás használatával kapcsolatos további információkért látogasson el [használata a Windows PowerShell-lel felügyelete a StorSimple eszköz](storsimple-8000-windows-powershell-administration.md).

Ez az oktatóanyag azt ismerteti, az eszköz a távoli felügyelet beállítása, majd a StorSimple és a Windows PowerShell összekapcsolása. HTTP vagy HTTPS használatával távoli csatlakozás a Windows PowerShell használatával. Azonban a StorSimple és a Windows PowerShell összekapcsolása dönt, vegye figyelembe a következőket:

* Az eszköz soros konzoljához való közvetlen csatlakozás biztonságos, de nincs csatlakozás soros konzolon keresztül hálózati kapcsolók. Legyen óvatos, a biztonsági kockázat, hálózati kapcsolók keresztül az eszköz soros konzoljához való kapcsolódáskor.
* Egy HTTP-kapcsolaton keresztül csatlakozó felajánlhatja a nagyobb biztonság, mint a hálózaton keresztül a soros konzolon keresztül kapcsolódik. Bár ez nem a legbiztonságosabb módszer, akkor elfogadható megbízható hálózatokon.
* Keresztül egy önaláírt tanúsítványt a HTTPS-KAPCSOLATON keresztül csatlakozó értéke a legbiztonságosabb és ajánlott.

Kapcsolódás távolról a Windows PowerShell-felületet. Azonban a Windows PowerShell felületén keresztül a StorSimple eszközhöz való távoli hozzáféréssel nem alapértelmezés szerint engedélyezve van. Először engedélyezze a távoli felügyeleti az eszközön, és ezután az ügyfélen, amelynek használatával az eszközhöz való hozzáféréshez.

Ebben a cikkben leírt lépéseket a Windows Server 2012 R2 rendszerű gazdarendszer végrehajtott.

## <a name="connect-through-http"></a>Kapcsolódás HTTP Protokollon keresztül

Csatlakozás a Windows PowerShell StorSimple egy HTTP-kapcsolaton keresztül a StorSimple eszköz soros konzolon keresztül csatlakozó-nál nagyobb védelmet kínál. Bár ez nem a legbiztonságosabb módszer, akkor elfogadható megbízható hálózatokon.

Az Azure-portálon vagy a soros konzol segítségével távoli felügyeletének konfigurálása. Az alábbi eljárások közül választhat:

* [Az Azure portál segítségével távoli felügyelet engedélyezéséhez a HTTP-kapcsolaton keresztül](#use-the-azure-classic-portal-to-enable-remote-management-over-http)
* [A soros konzol használatával engedélyezze a távoli felügyeleti HTTP Protokollon keresztül](#use-the-serial-console-to-enable-remote-management-over-http)

Miután engedélyezte távfelügyeletet, a következő eljárással az ügyfélszoftver előkészítése a távoli kapcsolat.

* [Az ügyfélszoftver előkészítése a távoli kapcsolat](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-portal-to-enable-remote-management-over-http"></a>Az Azure portál segítségével távoli felügyelet engedélyezéséhez a HTTP-kapcsolaton keresztül

Hajtsa végre az alábbi lépéseket az Azure-portálon távoli felügyelet engedélyezéséhez a HTTP Protokollon keresztül.

#### <a name="to-enable-remote-management-through-the-azure-portal"></a>Az Azure portálon keresztül távoli felügyeletének engedélyezése

1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. Válassza ki **eszközök** majd válassza ki, és kattintson a konfigurálni kívánt távoli kezelési eszköz. Ugrás a **eszközbeállítások > biztonsági**.
2. Az a **biztonsági beállítások** panelen kattintson a **Távfelügyelet**.
3. Az a **Távfelügyelet** panelen állítsa **távoli felügyelet engedélyezése** való **Igen**.
4. Mostantól HTTP-n keresztül is csatlakozhat. (Az alapértelmezett érték a HTTPS-KAPCSOLATON keresztül csatlakozni.) Győződjön meg arról, hogy a HTTP van kiválasztva.
   
   > [!NOTE]
   > A HTTP-n keresztüli csatlakozást kizárólag megbízható hálózatokon célszerű engedélyezni.
   
5. Kattintson a **mentése** és megerősítést kér, amikor **Igen**.

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>A soros konzol használatával engedélyezze a távoli felügyeleti HTTP Protokollon keresztül
Hajtsa végre a következő lépéseket távoli felügyeletének engedélyezése az eszköz soros konzoljához.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Az eszköz soros konzolon keresztül távoli felügyeletének engedélyezése
1. A soros konzol menüben válassza az 1. lehetőség. Az eszközön a soros konzol használatával kapcsolatos további információkért látogasson el [csatlakozás Windows PowerShell-lel eszköz soros konzolon keresztül](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. A parancssorba írja be:`Enable-HcsRemoteManagement –AllowHttp`
3. A HTTP protokollal csatlakozni az eszköz biztonsági rések kapcsolatos értesítést kap. Amikor a rendszer kéri, erősítse meg, írja be a **Y**.
4. Győződjön meg arról, hogy a HTTP engedélyezett beírásával:`Get-HcsSystem`
5. Ellenőrizze, hogy a **RemoteManagementMode** mezőben látható **HttpsAndHttpEnabled**. A következő ábrán ezek a beállítások a PuTTY.
   
     ![Soros HTTPS- és HTTP engedélyezve](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>Az ügyfélszoftver előkészítése a távoli kapcsolat
A következő lépésekkel távoli felügyeletének engedélyezése az ügyfélen.

#### <a name="to-prepare-the-client-for-remote-connection"></a>Az ügyfélszoftver előkészítése a távoli kapcsolat
1. Indítsa el a Windows PowerShell-munkamenetet rendszergazdaként.
2. Írja be a következő parancs futtatásával adja hozzá a StorSimple eszköz IP-címét az ügyfél megbízható állomások listájához:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
     Cserélje le <*device_ip*> az IP-cím, az eszköz; például: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Írja be a következő parancsot a hitelesítő adatai mentése változóként: 
   
    ```
    $cred = Get-Credential
    ```
    
4. A megjelenő párbeszédpanelen:
   
   1. Írja be a felhasználónevet a következő formátumban: *device_ip\SSAdmin*.
   2. Írja be az eszköz rendszergazdai jelszava, amely lett beállítva, amikor az eszköz a telepítővarázsló lett konfigurálva. Az alapértelmezett jelszó *jelszó1*.
5. Ez a parancs beírásával indítsa el a Windows PowerShell-munkamenetet az eszközön:
   
     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`
   
   > [!NOTE]
   > A StorSimple virtuális eszköz létrehozásához használja a Windows PowerShell-munkamenetet, hozzáfűzése a `–Port` paraméter, és adja meg a nyilvános port a StorSimple virtuális készülék távoli eljáráshívási konfigurált.
   
   
Ezen a ponton rendelkeznie kell egy aktív távoli Windows PowerShell-munkamenetben az eszközre.
   
![PowerShell távvezérlése HTTP-n keresztül](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>HTTPS keresztül csatlakozni

Csatlakozás a Windows PowerShell StorSimple egy HTTPS-kapcsolaton keresztül a legbiztonságosabb és ajánlott mód távolról kapcsolódni a Microsoft Azure StorSimple eszközt. Az alábbi eljárások azt ismertetik, hogy a Windows PowerShell csatlakoztatni a StorSimple is a HTTPS PROTOKOLLT használja a soros konzol és az ügyfél számítógépek beállítása.

Az Azure-portálon vagy a soros konzol segítségével távoli felügyeletének konfigurálása. Az alábbi eljárások közül választhat:

* [Az Azure portál segítségével távoli felügyeletének engedélyezése a HTTPS-KAPCSOLATON keresztül](#use-the-azure-classic-portal-to-enable-remote-management-over-https)
* [A soros konzol használatával engedélyezze a távoli felügyeleti HTTPS-KAPCSOLATON keresztül](#use-the-serial-console-to-enable-remote-management-over-https)

Miután engedélyezte távfelügyeletet, az alábbi eljárásokkal egy távoli kezeléshez való előkészítése a gazdagép és az eszköz csatlakozzon a távoli állomástól.

* [A gazdagép Felkészülés a távfelügyeletre](#prepare-the-host-for-remote-management)
* [Az eszköz csatlakozzon a távoli gazdagépen](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-portal-to-enable-remote-management-over-https"></a>Az Azure portál segítségével távoli felügyeletének engedélyezése a HTTPS-KAPCSOLATON keresztül

Hajtsa végre az alábbi lépéseket az Azure-portálon távoli felügyeletének engedélyezése a HTTPS-KAPCSOLATON keresztül.

#### <a name="to-enable-remote-management-over-https-from-the-azure-portal"></a>HTTPS-KAPCSOLATON keresztül távoli felügyeletének engedélyezése az Azure-portálon

1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. Válassza ki **eszközök** majd válassza ki, és kattintson a konfigurálni kívánt távoli kezelési eszköz. Ugrás a **eszközbeállítások > biztonsági**.
2. Az a **biztonsági beállítások** panelen kattintson a **Távfelügyelet**.
3. A **Távoli felügyelet engedélyezése** lehetőségnél válassza az **Igen** beállítást.
4. Ha most szeretné HTTPS protokoll használatával kapcsolódó levelezőprogramokkal. (Az alapértelmezett érték a HTTPS-KAPCSOLATON keresztül csatlakozni.) Győződjön meg arról, hogy HTTPS van-e kiválasztva.
5. Kattintson... majd **távfelügyeleti tanúsítvány letöltése**. Adjon meg egy helyet a fájl mentéséhez. A tanúsítvány telepítése a számítógépen az ügyfélszámítógépre vagy állomásra, amely az eszköz való kapcsolódáshoz használandó kell.
6. Kattintson a **mentése** majd **Igen** során a rendszer megerősítést kér.

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>A soros konzol használatával engedélyezze a távoli felügyeleti HTTPS-KAPCSOLATON keresztül

Hajtsa végre a következő lépéseket távoli felügyeletének engedélyezése az eszköz soros konzoljához.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Az eszköz soros konzolon keresztül távoli felügyeletének engedélyezése
1. A soros konzol menüben válassza az 1. lehetőség. Az eszközön a soros konzol használatával kapcsolatos további információkért látogasson el [csatlakozás Windows PowerShell-lel eszköz soros konzolon keresztül](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. A parancssorba írja be:
   
     `Enable-HcsRemoteManagement`
   
    Ez engedélyezze a HTTPS-eszközön.
3. Ellenőrizze, hogy engedélyezték-e a HTTPS beírásával: 
   
     `Get-HcsSystem`
   
    Győződjön meg arról, hogy a **RemoteManagementMode** mezőben látható **HttpsEnabled**. A következő ábrán ezek a beállítások a PuTTY.
   
     ![Soros HTTPS-kompatibilis](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)
4. Kimenetében `Get-HcsSystem`, másolja a sorozatszámot az eszköz, és mentse azokat a későbbi használatra.
   
   > [!NOTE]
   > A sorozatszám van leképezve a tanúsítvány CN-név.
   
5. Írja be a távoli felügyeleti tanúsítvány beszerzése: 
   
     `Get-HcsRemoteManagementCert`
   
    Egy tanúsítványt a következőhöz hasonló jelenik meg.
   
    ![Távfelügyeleti tanúsítvány beszerzése](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)
6. Másolja az adatokat a tanúsítványban lévő **---BEGIN CERTIFICATE---** való **---vége tanúsítvány---** egy szövegszerkesztőbe például a Jegyzettömbben, és mentse azt egy .cer fájlba. (Ön másolja ezt a fájlt a távoli állomás a gazdagép előkészítésekor.)
   
   > [!NOTE]
   > Új tanúsítvány létrehozásához használja a `Set-HcsRemoteManagementCert` parancsmag.
   
### <a name="prepare-the-host-for-remote-management"></a>A gazdagép Felkészülés a távfelügyeletre

Egy távoli kapcsolathoz, amely HTTPS-KAPCSOLATON keresztül használja a számítógép előkészítéséhez hajtsa végre az alábbi eljárásokat:

* [A .cer fájlt importálja a gyökérszintű tárolóban. az ügyfél vagy a távoli állomás](#to-import-the-certificate-on-the-remote-host).
* [Az eszközök sorozatszámait hozzáadása az állomásleíró fájlhoz, a távoli állomáson levő](#to-add-device-serial-numbers-to-the-remote-host).

A fenti eljárások leírását alább.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>A távoli állomáson levő tanúsítvány importálása
1. Kattintson a jobb gombbal a .cer fájlt, és válassza ki **telepítés tanúsítvány**. A Tanúsítványimportáló varázsló elindul.
   
    ![Tanúsítványimportáló varázsló 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)
2. A **tárolóhelyére**, jelölje be **helyi számítógép**, és kattintson a **következő**.
3. Válassza ki **minden tanúsítvány tárolása ebben a tárolóban**, és kattintson a **Tallózás**. Keresse meg a gyökérszintű tárolóban. a távoli állomás, és kattintson a **következő**.
   
    ![Tanúsítványimportáló varázsló 2. régiója](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)
4. Kattintson a **Befejezés** gombra. Megjelenik egy üzenet, amely közli, hogy az importálás sikeres volt-e.
   
    ![Tanúsítványimportáló varázsló 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>Az eszközök sorozatszámait hozzáadása a távoli állomás
1. Rendszergazdaként a Jegyzettömb, és nyissa meg a \Windows\System32\Drivers\etc helyen található gazdafájlt.
2. Adja hozzá a következő három bejegyzéseket a hosts fájl: **DATA 0 IP-cím**, **0 vezérlő rögzített IP-cím**, és **vezérlő 1 fix IP-cím**.
3. Adja meg a korábban mentett eszköz sorozatszámát. Képezze le az ezt az IP-cím a következő ábrán látható módon. A vezérlő 0 és a vezérlő 1 hozzáfűzése **Controller0** és **Controller1** a sorozatszám (CN-név) végén.
   
    ![CN-név felvétele a hosts fájl](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)
4. Mentse a hosts fájlt.

### <a name="connect-to-the-device-from-the-remote-host"></a>Az eszköz csatlakozzon a távoli gazdagépen

A Windows PowerShell és az SSL segítségével adjon meg egy SSAdmin munkamenet az eszköz az ügyfél vagy egy távoli állomástól. Az 1. lehetőség van leképezve a SSAdmin munkamenet a [soros konzol](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) menü az eszköz.

Az alábbi eljárás végrehajtásához a számítógépen, amelyből el kívánja a távoli Windows PowerShell-kapcsolatot.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-ssl"></a>Adjon meg egy SSAdmin munkamenet az eszközön a Windows PowerShell és az SSL használatával történő
1. Indítsa el a Windows PowerShell-munkamenetet rendszergazdaként.
2. Az ügyfél megbízható állomások írja be az eszköz IP-cím hozzáadása:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
    Ahol <*device_ip*> az eszköz IP-címét, például: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Új hitelesítő adatokat írja be a következőt:
   
     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`
   
    Ahol <*IP-címe céleszközön*> 0 adatok az eszköz; IP-címe például **10.126.173.90** a hosts fájl az előző ábrán látható módon. Továbbá adja meg az eszköz rendszergazdai jelszava.
4. Írja be a munkamenet létrehozása:
   
     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`
   
    A parancsmag - ComputerName paraméter adja meg a <*figyelt eszköz sorozatszámát*>. Ezzel a sorozatszámmal leképezve a következőre: DATA 0 a hosts fájl, a távoli állomáson; IP-címe például **SHX0991003G44MT** a következő ábrán látható módon.
5. Típus:
   
     `Enter-PSSession $session`
6. Várjon néhány percet kell, és majd meg fog csatlakozni az eszköz keresztül HTTPS SSL-en keresztül. Megjelenik egy üzenet, amely jelzi, hogy az eszköz csatlakozik.
   
    ![PowerShell távvezérlése HTTPS és az SSL használata](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>Következő lépések

* További információ [a StorSimple eszköz felügyelete a Windows PowerShell használatával](storsimple-8000-windows-powershell-administration.md).
* További információ [felügyelete a StorSimple eszközt a StorSimple Device Manager szolgáltatással](storsimple-8000-manager-service-administration.md).

