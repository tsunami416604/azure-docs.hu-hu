---
title: Csatlakozás távolról a StorSimple eszközhöz
description: A cikk ismerteti, hogyan konfigurálhatja az eszközt a távfelügyelethez, és hogyan csatlakozhat a Windows PowerShell for StorSimple-hez HTTP-n vagy HTTPS-en keresztül.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/02/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 58d61df932da06e32bb4c8f21a3a296b185f02d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80299011"
---
# <a name="connect-remotely-to-your-storsimple-8000-series-device"></a>Csatlakozás távolról a StorSimple 8000 sorozatú eszközhöz

## <a name="overview"></a>Áttekintés

Távolról is csatlakozhat az eszközhöz a Windows PowerShell segítségével. Ha így csatlakozik, nem jelenik meg menü. (Csak akkor jelenik meg a menü, ha az eszköz soros konzolját használja a csatlakozáshoz.) A Windows PowerShell-szolgáltatás sal való kapcsolatának használatával egy adott runspace-hez csatlakozhat. Megadhatja a megjelenítési nyelvet is.

Ha többet szeretne tudni arról, hogy miként használhatja a Windows PowerShell-távközlést az eszköz kezeléséhez, látogasson el a [StorSimple-eszköz használatának a Windows PowerShell használata storSimple-hez című webhelyre.](storsimple-8000-windows-powershell-administration.md)

Ez az oktatóanyag bemutatja, hogyan konfigurálhatja az eszközt a távoli felügyelethez, majd hogyan csatlakozhat a Windows PowerShell storSimple-hez. Http vagy HTTPS használatával távolról csatlakozhat a Windows PowerShellen keresztül. Amikor azonban eldönti, hogyan csatlakozhat a StorSimple-hez készült Windows PowerShellhez, vegye figyelembe a következő információkat:

* Közvetlenül az eszköz soros konzoljához való csatlakozás biztonságos, de a soros konzolhoz való hálózati kapcsolókon keresztül való csatlakozás nem. Legyen óvatos a biztonsági kockázat, amikor csatlakozik az eszköz soros konzol hálózati kapcsolókon keresztül.
* A HTTP-munkameneten keresztül történő csatlakozás nagyobb biztonságot nyújthat, mint a soros konzolon keresztül a hálózaton keresztül történő csatlakozás. Bár ez nem a legbiztonságosabb módszer, megbízható hálózatokon is elfogadható.
* Https-munkameneten keresztül önaláírt tanúsítvánnyal való csatlakozás a legbiztonságosabb és az ajánlott beállítás.

Távolról is csatlakozhat a Windows PowerShell-felülethez. A StorSimple-eszköz windowsPowerShell-felületen keresztüli távoli elérése azonban alapértelmezés szerint nincs engedélyezve. Először engedélyeznie kell a távfelügyeletet az eszközön, majd az eszköz eléréséhez használt ügyfélen.

A cikkben ismertetett lépések Windows Server 2012 R2 rendszert futtató gazdarendszeren történtek.

## <a name="connect-through-http"></a>Csatlakozás HTTP-n keresztül

A Windows PowerShell for StorSimple http-munkameneten keresztül való csatlakozása nagyobb biztonságot nyújt, mint a StorSimple-eszköz soros konzolján keresztül történő csatlakozás. Bár ez nem a legbiztonságosabb módszer, megbízható hálózatokon is elfogadható.

Használhatja az Azure Portalon vagy a soros konzol a távoli felügyelet konfigurálásához. Válasszon az alábbi eljárások közül:

* Az Azure Portal használata a http-n keresztüli távfelügyelet engedélyezéséhez
* [A soros konzol használata a http-n keresztüli távfelügyelet engedélyezéséhez](#use-the-serial-console-to-enable-remote-management-over-http)

A távkezelés engedélyezése után az alábbi eljárással készítse elő az ügyfelet a távoli kapcsolatra.

* [Az ügyfél előkészítése a távoli kapcsolatra](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-portal-to-enable-remote-management-over-http"></a>Az Azure Portal használata a http-n keresztüli távfelügyelet engedélyezéséhez

Hajtsa végre az alábbi lépéseket az Azure Portalon a http-n keresztüli távfelügyelet engedélyezéséhez.

#### <a name="to-enable-remote-management-through-the-azure-portal"></a>Távfelügyelet engedélyezése az Azure Portalon keresztül

1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. Válassza **az Eszközök lehetőséget,** majd jelölje ki és kattintson a távkezeléshez konfigurálni kívánt eszközre. Nyissa meg **az Eszközbeállítások > biztonság lehetőséget.**
2. A **Biztonsági beállítások** panelen kattintson a **Távkezelés gombra.**
3. A **Távoli kezelés** panelen állítsa a **Távkezelés engedélyezése** **Igen (Igen)** beállítást.
4. Mostantól HTTP-n keresztül is csatlakozhat. (Az alapértelmezett beállítás a HTTPS-kapcsolaton keresztüli csatlakozás.) Győződjön meg arról, hogy a HTTP ki van jelölve.
   
   > [!NOTE]
   > A HTTP-n keresztüli csatlakozást kizárólag megbízható hálózatokon célszerű engedélyezni.
   
5. Kattintson a **Mentés** gombra, és amikor megerősítést kér, válassza az **Igen**lehetőséget.

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>A soros konzol használata a http-n keresztüli távfelügyelet engedélyezéséhez
Hajtsa végre az alábbi lépéseket az eszköz soros konzolján a távkezelés engedélyezéséhez.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Távfelügyelet engedélyezése az eszköz soros konzolján keresztül
1. A soros konzol menüjében válassza az 1. A soros konzol eszközén való használatáról a Csatlakozás a [StorSimple hez windowsos PowerShellhez eszközsoros konzolon című](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)oldalán talál.
2. A parancssorba írja be a következőt:`Enable-HcsRemoteManagement –AllowHttp`
3. Értesítést kap a HTTP használatával az eszközhöz való csatlakozás biztonsági réseiről. Amikor a rendszer kéri, erősítse meg az **Y**beírást.
4. Ellenőrizze, hogy a HTTP engedélyezve van-e a következő beírással:`Get-HcsSystem`
5. Ellenőrizze, hogy a **RemoteManagementMode** mezőben **látható-e httpsandhttpEnabled**. A következő ábra ezeket a beállításokat mutatja a PuTTY.The following illustration shows these settings in PuTTY.
   
     ![Soros HTTPS és HTTP engedélyezve](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>Az ügyfél előkészítése a távoli kapcsolatra
Hajtsa végre az alábbi lépéseket az ügyfélen a távkezelés engedélyezéséhez.

#### <a name="to-prepare-the-client-for-remote-connection"></a>Az ügyfél előkészítése a távoli kapcsolatra
1. Windows PowerShell-munkamenet indítása rendszergazdaként. Ha Windows 10-es ügyfelet használ, alapértelmezés szerint a Windows távfelügyeleti szolgáltatása manuálisra van állítva. Előfordulhat, hogy a következő beírással kell elindítania a szolgáltatást:

    `Start-Service WinRM`
    
2. Írja be a következő parancsot, ha hozzá szeretné adni a StorSimple eszköz IP-címét az ügyfél megbízható állomások listájához:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
     Cserélje ki <*device_ip*> a készülék IP-címére; például: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Írja be a következő parancsot az eszköz hitelesítő adatainak egy változóba való mentéséhez: 
   
    ```
    $cred = Get-Credential
    ```
    
4. A megjelenő párbeszédpanelen:
   
   1. Írja be a felhasználónevet a következő formátumban: *device_ip\SSAdmin*.
   2. Írja be az eszköz rendszergazdai jelszavát, amelyet akkor állított be, amikor az eszközt a telepítővarázslóval konfigurálta. Az alapértelmezett jelszó a *Password1*.
5. A következő parancs beírásával indítsa el az eszközön a Windows PowerShell-munkamenetet:
   
     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`
   
   > [!NOTE]
   > A StorSimple virtuális eszközzel használható Windows PowerShell-munkamenet `–Port` létrehozásához fűzze hozzá a paramétert, és adja meg a StorSimple virtuális berendezés remoting szolgáltatásában konfigurált nyilvános portot.
   
   
Ezen a ponton egy aktív távoli Windows PowerShell-munkamenettel kell rendelkeznie az eszközhöz.
   
![PowerShell-távhívás HTTP használatával](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>Csatlakozás HTTPS-en keresztül

A Windows PowerShell for StorSimple https-munkameneten keresztül való csatlakozása a legbiztonságosabb és ajánlott módszer a Microsoft Azure StorSimple-eszközhöz való távoli csatlakozáshoz. Az alábbi eljárások ismertetik, hogyan állíthatja be a soros konzol és az ügyfélszámítógépek, hogy a HTTPS segítségével csatlakozhat a Windows PowerShell storSimple.The following procedures explain how to set up the serial console and client computers so you can use HTTPS to connect to Windows PowerShell for StorSimple.

Használhatja az Azure Portalon vagy a soros konzol a távoli felügyelet konfigurálásához. Válasszon az alábbi eljárások közül:

* Az Azure Portal használata a távfelügyelet HTTPS-en keresztüli engedélyezéséhez
* [A soros konzol használata a távoli felügyelet HTTPS-en keresztüli engedélyezéséhez](#use-the-serial-console-to-enable-remote-management-over-https)

A távoli felügyelet engedélyezése után az alábbi eljárásokkal készítse elő a gazdagép távoli felügyeletét, és csatlakozzon az eszközhöz a távoli állomásról.

* [A gazdagép előkészítése távkezelésre](#prepare-the-host-for-remote-management)
* [Csatlakozás az eszközhöz a távoli állomásról](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-portal-to-enable-remote-management-over-https"></a>Az Azure Portal használata a távfelügyelet HTTPS-en keresztüli engedélyezéséhez

Hajtsa végre az alábbi lépéseket az Azure Portalon a távoli felügyelet HTTPS-en keresztül engedélyezéséhez.

#### <a name="to-enable-remote-management-over-https-from-the-azure-portal"></a>Távfelügyelet https-kapcsolaton keresztüli engedélyezése az Azure Portalról

1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. Válassza **az Eszközök lehetőséget,** majd jelölje ki és kattintson a távkezeléshez konfigurálni kívánt eszközre. Nyissa meg **az Eszközbeállítások > biztonság lehetőséget.**
2. A **Biztonsági beállítások** panelen kattintson a **Távkezelés gombra.**
3. A **Távoli felügyelet engedélyezése** lehetőségnél válassza az **Igen** beállítást.
4. Most már választhatja a HTTPS használatával történő csatlakozást. (Az alapértelmezett beállítás a HTTPS-kapcsolaton keresztüli csatlakozás.) Győződjön meg arról, hogy a HTTPS ki van jelölve.
5. Kattintson... majd kattintson **a Távoli kezelési tanúsítvány letöltése gombra.** Adja meg a fájl mentési helyét. Ezt a tanúsítványt az eszközhöz való csatlakozáshoz használt ügyfél- vagy gazdaszámítógépre kell telepítenie.
6. Kattintson **a Mentés** gombra, majd az **Igen** gombra, amikor megerősítést kér.

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>A soros konzol használata a távoli felügyelet HTTPS-en keresztüli engedélyezéséhez

Hajtsa végre az alábbi lépéseket az eszköz soros konzolján a távkezelés engedélyezéséhez.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Távfelügyelet engedélyezése az eszköz soros konzolján keresztül
1. A soros konzol menüjében válassza az 1. A soros konzol eszközén való használatáról a Csatlakozás a [StorSimple hez windowsos PowerShellhez eszközsoros konzolon című](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)oldalán talál.
2. A parancssorba írja be a következőt:
   
     `Enable-HcsRemoteManagement`
   
    Ez lehetővé teszi a HTTPS az eszközön.
3. Ellenőrizze, hogy a HTTPS engedélyezve van-e a következő beírással: 
   
     `Get-HcsSystem`
   
    Győződjön meg arról, hogy a **RemoteManagementMode** mezőben **httpsenabled**látható. A következő ábra ezeket a beállításokat mutatja a PuTTY.The following illustration shows these settings in PuTTY.
   
     ![Soros HTTPS engedélyezve](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)
4. A kimenetből `Get-HcsSystem`másolja az eszköz sorozatszámát, és mentse későbbi használatra.
   
   > [!NOTE]
   > A sorozatszám a tanúsítványban lévő KN-névre van leképezve.
   
5. Távoli felügyeleti tanúsítvány beszerzése a következő beírással: 
   
     `Get-HcsRemoteManagementCert`
   
    Az alábbihoz hasonló tanúsítvány jelenik meg.
   
    ![Távoli felügyeleti tanúsítvány beszereznie](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)
6. Másolja a tanúsítvány adatait a **-----BEGIN CERTIFICATE-----** **-----END CERTIFICATE-----** fájlba egy szövegszerkesztőbe, például a Jegyzettömbbe, és mentse .cer fájlként. (Ezt a fájlt a távoli állomásra másolja, amikor előkészíti az állomást.)
   
   > [!NOTE]
   > Új tanúsítvány létrehozásához használja `Set-HcsRemoteManagementCert` a parancsmagát.
   
### <a name="prepare-the-host-for-remote-management"></a>A gazdagép előkészítése távkezelésre

Ha fel szeretné készíteni a gazdaszámítógépet egy HTTPS-munkamenetet használó távoli kapcsolatra, hajtsa végre az alábbi eljárásokat:

* [Importálja a .cer fájlt az ügyfél vagy a távoli állomás gyökértárolójába.](#to-import-the-certificate-on-the-remote-host)
* [Adja hozzá az eszköz sorozatszámát a távoli állomás hosts fájljához.](#to-add-device-serial-numbers-to-the-remote-host)

Az előző eljárások mindegyikét az alábbiakban ismertetjük.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>A tanúsítvány importálása a távoli állomásra
1. Kattintson a jobb gombbal a .cer fájlra, és válassza **a Tanúsítvány telepítése parancsot.** Ezzel elindítja a Tanúsítványimportálás varázslót.
   
    ![Tanúsítványimportálás varázsló 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)
2. Az **Üzlet helyéhez**válassza a **Helyi számítógép**lehetőséget, majd kattintson a **Tovább**gombra.
3. Válassza **az Összes tanúsítvány helye a következő tárolóban**lehetőséget, majd kattintson a **Tallózás gombra.** Nyissa meg a távoli állomás gyökértárolóját, majd kattintson a **Tovább**gombra.
   
    ![Tanúsítványimportálás varázsló 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)
4. Kattintson a **Befejezés** gombra. Megjelenik egy üzenet, amely arról tájékoztat, hogy az importálás sikeres volt.
   
    ![Tanúsítványimportálás varázsló 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>Eszköz sorozatszámának hozzáadása a távoli állomáshoz
1. Indítsa el a Jegyzettömböt rendszergazdaként, majd nyissa meg a \Windows\System32\Drivers\etc mappában található hosts fájlt.
2. Adja hozzá a következő három bejegyzést a hosts fájlhoz: **DATA 0 IP-cím**, **Controller 0 Fixed IP-cím**és **Controller 1 Fixed IP-cím**.
3. Adja meg a korábban mentett eszköz sorozatszámát. Rendelje hozzá az IP-címre az alábbi képen látható módon. A 0 vezérlő és az 1-es vezérlő esetében fűzzék hozzá a **Controller0** és a **Controller1-et** a sorozatszám (KN-név) végén.
   
    ![CN-név hozzáadása a gazdagépfájlhoz](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)
4. Mentse a hosts fájlt.

### <a name="connect-to-the-device-from-the-remote-host"></a>Csatlakozás az eszközhöz a távoli állomásról

A Windows PowerShell és a TLS segítségével adjon meg egy SSAdmin munkamenetet az eszközön egy távoli állomásról vagy ügyfélről. Az SSAdmin munkamenet [leképezi](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) az 1.

Hajtsa végre az alábbi eljárást azon a számítógépen, amelyről a távoli Windows PowerShell-kapcsolatot szeretné létesíteni.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-tls"></a>SSAdmin-munkamenet beírása az eszközön a Windows PowerShell és a TLS használatával
1. Windows PowerShell-munkamenet indítása rendszergazdaként. Ha Windows 10-es ügyfelet használ, alapértelmezés szerint a Windows távfelügyeleti szolgáltatása manuálisra van állítva. Előfordulhat, hogy a következő beírással kell elindítania a szolgáltatást:

    `Start-Service WinRM`

2. Adja hozzá az eszköz IP-címét az ügyfél megbízható állomásaihoz a következő beírással:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
    Ahol <*device_ip*> a készülék IP-címe; például: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Új hitelesítő adatok létrehozásához írja be a következőt:
   
     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`
   
    Ahol <*> céleszköz IP-címe* a KÉSZÜLÉK DATA 0 IP-címe; például **10.126.173.90,** ahogy az előző képen a hosts fájl. Adja meg az eszköz rendszergazdai jelszavát is.
4. Munkamenet létrehozása a következő beírással:
   
     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`
   
    A parancsmag -ComputerName paraméteréhez adja meg a céleszköz <*> sorozatszámát.* Ez a sorozatszám a data 0 IP-címére lett leképezve a távoli állomás hosts fájljában; **shx0991003G44MT** például az alábbi képen látható módon.
5. Típus:
   
     `Enter-PSSession $session`
6. You will need to wait a few minutes, and then you will be connected to your device via HTTPS over TLS. Megjelenik egy üzenet, amely jelzi, hogy csatlakozik az eszközhöz.
   
    ![PowerShell-távhasználat HTTPS és TLS használatával](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>További lépések

* További információ a [StorSimple-eszköz felügyeletéhez a Windows PowerShell használatával kapcsolatban.](storsimple-8000-windows-powershell-administration.md)
* További információ [a StorSimple Eszközkezelő szolgáltatás használatáról a StorSimple-eszköz felügyeletéhez.](storsimple-8000-manager-service-administration.md)

