---
title: 
description: 
services: site-recovery
documentationcenter: 
author: 
manager: 
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: 4871e5959483058aa957e3373ddc5121b398e1d9
ms.openlocfilehash: 1d6d595bf788fc3fdba0bbc98887614e17c791dc


---



### <a name="install-the-mobility-service"></a>A mobilitási szolgáltatás telepítése
A virtuális gépek és fizikai kiszolgálók védelmének engedélyezése során az első lépés a mobilitási szolgáltatás telepítése. Ezt több módon végrehajthatja:

* **Leküldés folyamatkiszolgálóról**: Ha engedélyezi a replikálást egy gépen, leküldheti és telepítheti a mobilitási szolgáltatási összetevőt a folyamatkiszolgálóról. 
*Megjegyzés*: a leküldéses telepítés nem megy végbe, ha a gépeken már fut az összetevő naprakész verziója.
* **Vállalati leküldés**: Telepítheti automatikusan az összetevőt a vállalati leküldéses folyamattal (például a WSUS vagy a System Center Configuration Manager) vagy az [Azure Automation és a célállapot-konfiguráció](site-recovery-automate-mobility-service-install.md) segítségével. Ennek végrehajtása előtt állítsa be a konfigurációs kiszolgálót.
* **Manuális telepítés**: Az összetevőt telepítheti manuálisan minden replikálni kívánt gépen. Ennek végrehajtása előtt állítsa be a konfigurációs kiszolgálót.

#### <a name="prepare-for-automatic-push-on-windows-machines"></a>Az automatikus leküldés előkészületei Windows-gépeken
Ebből a szakaszból megtudhatja, hogyan készítse elő a Windows-gépeket arra, hogy a folyamatkiszolgáló automatikusan telepíthesse a mobilitási szolgáltatást.

1. Hozzon létre egy fiókot, amelyen keresztül a folyamatkiszolgáló hozzáférhet a géphez. A fióknak rendelkeznie kell rendszergazdai jogosultságokkal (helyi vagy tartományi), és csak a leküldéses telepítéshez használható.

   > [!NOTE]
   > Ha nem használ tartományi fiókot, le kell tiltania a távoli felhasználói hozzáférés-vezérlést a helyi számítógépen. Ehhez adja hozzá a LocalAccountTokenFilterPolicy DWORD bejegyzést 1 értékkel a HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System helyen található beállításjegyzékhez. A beállításjegyzék-bejegyzés hozzáadásához egy parancssori felületen írja be a következőt: **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.
   >
   >
2. A védelemmel ellátni kívánt gép Windows tűzfalán válassza ki az **Alkalmazás vagy szolgáltatás engedélyezése a tűzfalon keresztül** beállítást. Engedélyezze a **Fájl- és nyomtatómegosztás**, valamint a **Windows Management Instrumentation** használatát. Tartományi számítógépek esetében a tűzfal beállításai csoportházirend-objektumok használatával is konfigurálhatók.

   ![Tűzfalbeállítások](./media/site-recovery-vmware-to-azure/mobility1.png)
3. Adja hozzá a létrehozott fiókot:

   * Nyissa meg a következőt: **cspsconfigtool**. A parancsikonja elérhető az asztalon, az alkalmazás pedig a [TELEPÍTÉSI HELY]\home\svsystems\bin mappában található.
   * A **Fiókok kezelése** lapon kattintson a **Fiók hozzáadása** lehetőségre.
   * Adja hozzá a létrehozott fiókot. A fiók hozzáadása után meg kell adnia a hitelesítő adatokat, amikor engedélyezi a replikálást egy géphez.

#### <a name="prepare-for-automatic-push-on-linux-servers"></a>Az automatikus leküldés előkészületei Linux-gépeken
1. Győződjön meg arról, hogy a védelemmel ellátni kívánt Linux-gép támogatott [a védett gépekre vonatkozó előfeltételek](#protected-machine-prerequisites) szakaszban leírtak alapján. Gondoskodjon róla, hogy legyen hálózati kapcsolat a Linux-gép és a folyamatkiszolgáló közt.
2. Hozzon létre egy fiókot, amelyen keresztül a folyamatkiszolgáló hozzáférhet a géphez. A fióknak gyökér szintű felhasználónak kell lennie a forrás Linux-kiszolgálón, és csak a leküldéses telepítéshez használható.

   * Nyissa meg a következőt: **cspsconfigtool**. A parancsikonja elérhető az asztalon, az alkalmazás pedig a [TELEPÍTÉSI HELY]\home\svsystems\bin mappában található.
   * A **Fiókok kezelése** lapon kattintson a **Fiók hozzáadása** lehetőségre.
   * Adja hozzá a létrehozott fiókot. A fiók hozzáadása után meg kell adnia a hitelesítő adatokat, amikor engedélyezi a replikálást egy géphez.
3. Ellenőrizze, hogy a forrás Linux-kiszolgálón található /etc/hosts fájl tartalmaz-e olyan bejegyzéseket, amelyek a helyi gazdanevet az összes hálózati adapterhez társított IP-címekké képezik le.
4. Telepítse a legfrissebb openssh, openssh-server és openssl csomagokat a replikálni kívánt gépen.
5. Ügyeljen arra, hogy az SSH engedélyezve legyen, és a 22-es porton fusson.
6. A következő módon engedélyezze az SFTP alrendszer és a jelszó hitelesítését az sshd_config fájlban:

   * Jelentkezzen be gyökér szintű felhasználóként.
   * Az /etc/ssh/sshd_config fájlban keresse meg azt a sort, amelynek a kezdete **PasswordAuthentication**.
   * Állítsa vissza a sort, és módosítsa a **no** értéket **yes** értékre.
   * Keresse meg a sort, amelynek a kezdete **Subsystem**, és állítsa vissza.

     ![Linux](./media/site-recovery-vmware-to-azure/mobility2.png)

### <a name="install-the-mobility-service-manually"></a>A mobilitási szolgáltatás telepítése manuálisan
A telepítők a konfigurációs kiszolgálón a következő helyen érhetőek el: **C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**.

| Forrás operációs rendszer | Mobilitási szolgáltatás telepítőfájlja |
| --- | --- |
| Windows Server (csak&64; bites) |Microsoft-ASR_UA_9.*.0.0_Windows_* release.exe |
| CentOS 6.4, 6.5, 6.6 (csak 64 bites) |Microsoft-ASR_UA_9.*.0.0_RHEL6-64_*release.tar.gz |
| SUSE Linux Enterprise Server 11 SP3 (csak 64 bites) |Microsoft-ASR_UA_9.*.0.0_SLES11-SP3-64_*release.tar.gz |
| Oracle Enterprise Linux 6.4, 6.5 (csak 64 bites) |Microsoft-ASR_UA_9.*.0.0_OL6-64_*release.tar.gz |

#### <a name="install-mobility-service-on-a-windows-server"></a>A mobilitási szolgáltatás telepítése Windows Serveren
1. Töltse le és futtassa a megfelelő telepítőt.
2. Az **Előkészületek** területen válassza a **Mobilitási szolgáltatás** lehetőséget.

    ![Mobilitási szolgáltatás](./media/site-recovery-vmware-to-azure/mobility3.png)
3. A **Konfigurációs kiszolgáló részletei** területen adja meg a konfigurációs kiszolgáló IP-címét és az egyesített telepítő futtatásakor létrejött hozzáférési kódot. A hozzáférési kód lekéréséhez futtassa a **<SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe –v** parancsot a konfigurációs kiszolgálón.

    ![Mobilitási szolgáltatás](./media/site-recovery-vmware-to-azure/mobility6.png)
4. A **Telepítés helye** mezőben fogadja el az alapértelmezett értéket, és kattintson a **Tovább** gombra a telepítés megkezdéséhez.
5. A **Telepítési folyamat** ablakban figyelje a telepítést, és indítsa újra a gépet, ha a rendszer kéri. A szolgáltatás telepítése után a portálon az állapot frissítése hozzávetőlegesen 15 percet vesz igénybe.

#### <a name="install-mobility-service-on-a-windows-server-using-the-command-prompt"></a>A mobilitási szolgáltatás telepítése Windows Serveren a parancssor használatával
1. Másolja a telepítőt egy helyi mappába (pl.: C:\Temp) a kiszolgálón, amely számára védelmet kíván beállítani. A telepítő a konfigurációs kiszolgálón található a következő helyen: **[Telepítés helye]\home\svsystems\pushinstallsvc\repository**. A Windows operációs rendszerekhez használható csomag neve a következőhöz hasonló: Microsoft-ASR_UA_9.3.0.0_Windows_GA_17thAug2016_release.exe
2. Nevezze át ezt a fájlt MobilitySvcInstaller.exe névre.
3. Az MSI-telepítő kibontásához futtassa a következő parancsot:

    ``C:\> cd C:\Tempww
    ``C:\Temp> MobilitySvcInstaller.exe /q /xC:\Temp\Extracted``
    ``C:\Temp> cd Extracted``
    ``C:\Temp\Extracted> UnifiedAgent.exe /Role "Agent" /CSEndpoint "IP Address of Configuration Server" /PassphraseFilePath <Full path to the passphrase file>``

##### <a name="full-command-line-syntax"></a>Teljes parancssori szintaxis
    UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]<br/>

**Paraméterek**

* **/Role:** Kötelező. Megadja, hogy a mobilitási szolgáltatást kell-e telepíteni. Bemeneti értékek: Agent|MasterTarget
* **/InstallLocation:** Kötelező. Megadja a szolgáltatás telepítésének helyét.
* **/PassphraseFilePath:** Kötelező. A konfigurációs kiszolgáló hozzáférési kódja.
* **/LogFilePath:** Kötelező. Az a hely, ahol a telepítési naplófájlok létrejönnek.

#### <a name="uninstall-the-mobility-service-manually"></a>A mobilitási szolgáltatás eltávolítása manuálisan
A mobilitási szolgáltatás a Vezérlőpult Programok telepítése és törlése területén vagy az MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} parancssori utasítással távolítható el.

#### <a name="install-the-mobility-service-on-a-linux-server"></a>A mobilitási szolgáltatás telepítése Linux-kiszolgálón
1. Másolja a fenti táblázat alapján a megfelelő tar archívumot a replikálni kívánt Linux-gépre.
2. Nyisson meg egy felületprogramot, és bontsa ki a tömörített tar archívumot egy helyi útvonalra a következő parancs futtatásával: `tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. Hozzon létre egy passphrase.txt fájlt abban a helyi könyvtárban, ahová kibontotta a tar archívum tartalmát. Ehhez másolja a hozzáférési kódot a C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase helyről a konfigurációs kiszolgálóra, majd mentse a passphrase.txt fájlban az *`echo <passphrase> >passphrase.txt`* parancsot a felületen futtatva.
4. A mobilitási szolgáltatás telepítéséhez futtassa a következőt: *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Adja meg a konfigurációs kiszolgáló belső IP-címét, és győződjön meg arról, hogy a 443-as port van kiválasztva. A szolgáltatás telepítése után a portálon az állapot frissítése hozzávetőlegesen 15 percet vesz igénybe.

**A telepítést elvégezheti a parancssorból is**:

Másolja a hozzáférési kódot a konfigurációs kiszolgáló C:\Program Files (x86)\InMage Systems\private\connection helyéről, majd mentse „passphrase.txt” néven a konfigurációs kiszolgálón. Ezután futtassa ezeket a parancsokat. A példánkban a konfigurációs kiszolgáló IP-címe 104.40.75.37, a HTTPS-port értéke pedig legyen 443:


Telepítés éles kiszolgálón:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

Telepítés a fő célkiszolgálón:

    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt






<!--HONumber=Feb17_HO3-->


