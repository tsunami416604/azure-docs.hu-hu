---
title: "Az Azure verem szoftverfejlesztői készlet telepítése |} Microsoft Docs"
description: "Ismerje meg, készítse elő a Azure verem Development Kit, és a PowerShell-parancsfájl a telepítés futtatása."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 0ad02941-ed14-4888-8695-b82ad6e43c66
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/15/2017
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 470a45aea253e1e238983527427b600117e413fe
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2017
---
# <a name="deploy-the-azure-stack-development-kit"></a>Az Azure verem szoftverfejlesztői készlet telepítése

*A következőkre vonatkozik: Azure szoftverfejlesztői készletet*

Központi telepítése a [Azure verem szoftverfejlesztői készlet](azure-stack-poc.md), hajtsa végre az alábbi lépéseket:

1. [A központi telepítési csomag](https://azure.microsoft.com/overview/azure-stack/try/?v=try) a Cloudbuilder.vhdx eléréséhez.
2. Készítse elő a cloudbuilder.vhdx szoftverfejlesztői készlet telepíteni szeretné a számítógép (development kit fogadó) konfigurálásához. Ez a lépés után a development kit gazdagép fog indulni, a Cloudbuilder.vhdx.
3. A csomag a development kit gazdagépen telepíteni.

> [!NOTE]
> A legjobb eredmények elérése érdekében akkor is, ha szeretné használni az Azure-verem leválasztott környezet esetén célszerű telepíteni, amíg csatlakozik az internethez. Ily módon a development kit telepítés részét képező Windows Server 2016 próbaverzió akkor lehet aktiválni, a központi telepítéskor.

## <a name="download-and-extract-the-development-kit"></a>Töltse le és a csomag kibontása
1. A letöltés megkezdése előtt győződjön meg arról, hogy a számítógép megfelel-e a következő előfeltételek teljesülését:

  - A számítógépnek rendelkeznie kell legalább 60 GB lemezterülettel érhető el a négy különböző, azonos logikai merevlemezek továbbá az operációs rendszer lemezre.
  - [.NET-keretrendszer 4.6 (vagy újabb verzió)](https://aka.ms/r6mkiy) kell telepíteni.

2. [Ugrás az első lépések lap](https://azure.microsoft.com/overview/azure-stack/try/?v=try) ahol töltse le az Azure verem szoftverfejlesztői készlet, adja meg a adatait és kattintson a **Submit**.
3. Töltse le és futtassa a [telepítési Azure verem szoftverfejlesztői készlet-ellenőrzője](https://go.microsoft.com/fwlink/?LinkId=828735&clcid=0x409) előfeltétel-ellenőrző parancsfájl. A különálló parancsprogram végighalad a szükséges előfeltételek ellenőrzése a telepítő az Azure verem szoftverfejlesztői készlet végezhető el. Is biztosítja az Azure verem szoftverfejlesztői készlet a nagyobb csomag letöltése előtt hardver- és szoftverkövetelmények teljesítésének megerősítéséhez.
4. A **a szoftverfrissítések letöltéséhez**, kattintson a **Azure verem szoftverfejlesztői készlet**.

  > [!NOTE]
  > A ASDK letöltés (AzureStackDevelopmentKit.exe) approximiately 10 GB-os önmagában. Ha a Windows Server 2016 értékelési verzió ISO-fájl is letölthető, a letöltési mérete körülbelül 17GB növekszik. Használhatja, hogy ISO-fájl létrehozása és hozzáadása egy Windows Server 2016 virtuálisgép-lemezkép az Azure-verem piactéren ASDK telepítésének befejezése után. Ne feledje, hogy a Windows Server 2016 értékelési lemezkép csak a ASDK használható ASDK licencszerződés feltételei.

5. A letöltés befejezése után kattintson **futtatása** (AzureStackDevelopmentKit.exe) ASDK önkibontó elindításához.
6. Tekintse át és fogadja el a megjelenített licencszerződése a **licencszerződés** a önkibontó varázsló, és kattintson a lap **következő**.
7. Tekintse át az adatvédelmi nyilatkozat információkat jelenik meg a **fontos figyelmeztetés** a önkibontó varázsló, és kattintson a lap **következő**.
8. Válassza ki azt a helyet az Azure-verem telepítőfájlokat a kinyerni a **rendeltetési hely kiválasztása** a önkibontó varázsló, és kattintson a lap **következő**. Az alapértelmezett hely: *aktuális mappa*\Azure verem szoftverfejlesztői készlet. 
9. Tekintse át a összefoglalását a célhelyen a **készen áll a kivonatot** önkibontó varázsló, és kattintson a lap **kibontása** kibontása a CloudBuilder.vhdx (körülbelül 25 GB) és ThirdPartyLicenses.rtf fájlokat. Ez a folyamat eltarthat egy ideig.
10. Másolja, vagy helyezze át a CloudBuilder.vhdx fájlt a C:\ meghajtó (C:\CloudBuilder.vhdx) gyökérkönyvtárában az ASDK állomáson.

> [!NOTE]
> A fájlok kibontása után már törölheti a. A következő EXE és. Merevlemez-terület helyreállítása lévő fájlokhoz. Vagy készíthet biztonsági másolatot készítenie a fájlokról, hogy nem kell újból letöltheti a fájlokat, ha a ASDK telepíteni kell.

## <a name="deploy-the-asdk-using-a-guided-experience"></a>Elősegítheti használatával ASDK telepítése
A ASDK egy grafikus felhasználói felülete (GUI) letöltése és asdk-installer.ps1 PowerShell-parancsfájlok futtatására használatával is telepíthető.

> [!NOTE]
> Az Azure verem szoftverfejlesztői készlet telepítő felhasználói felületének egy megnyitott termékekre parancsfájlt, a WCF és a PowerShell alapján.

### <a name="prepare-the-development-kit-host-using-a-guided-user-experience"></a>Készítse elő a development kit fogadó egy interaktív felhasználói felület használatával
A ASDK az állomáson telepítése előtt elő kell készíteni a ASDK környezet.
1. Jelentkezzen be a helyi rendszergazdájaként a ASDK gazdaszámítógéphez.
2. Győződjön meg arról, hogy a CloudBuilder.vhdx fájlt a C:\ meghajtó (C:\CloudBuilder.vhdx), a legfelső szintű át lett helyezve.
3. Futtassa a következő parancsfájlt a development kit telepítőfájl (asdk-installer.ps1) töltheti le a [Azure verem GitHub-eszközök adattár](https://github.com/Azure/AzureStack-Tools) számára a **C:\AzureStack_Installer** mappájában található a fejlesztői csomag gazdaszámítógépen:

  ```powershell
  # Variables
  $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
  $LocalPath = 'C:\AzureStack_Installer'
  # Create folder
  New-Item $LocalPath -Type directory
  # Download file
  Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
  ```

4. Egy rendszergazda jogú PowerShell-konzolon, indítsa el a **C:\AzureStack_Installer\asdk-installer.ps1** parancsfájlt, és kattintson a **környezet előkészítése**.
5. Az a **Cloudbuilder válasszon vhdx** oldalon a telepítő, keresse meg és válassza ki a **cloudbuilder.vhdx** fájlt, amely a letöltött és kibontott az előző lépésben. Az ezen a lapon, akkor is, ha szükséges, engedélyezze a **illesztőprogramok hozzáadásával** jelölőnégyzetet, ha további illesztőprogramok hozzáadásához a development kit gazdaszámítógépre van szüksége.  
6. Az a **választható beállítások** lapján adja meg a helyi rendszergazdai fiók a development kit gazdaszámítógépen. 

  > [!IMPORTANT]
  > Ha nem ad meg ezeket a hitelesítő adatokat, szüksége lesz közvetlen vagy a gazdagép eléréséhez KVM beállítása a csomag részeként a számítógép újraindítása után.

7. A beállításokkal is megadhatja a **választható beállítások** lap:
    - **Számítógépnév**: Ez a beállítás egy development kit állomás neve. A név FQDN követelményeknek kell megfelelniük, és lehet 15 karakternél rövidebb szerepnevet. Az alapértelmezett érték a Windows által generált véletlenszerű számítógép nevét.
    - **Időzóna**: időzónáját adja meg a development kit gazdagép. Az alapértelmezett érték (UTC-8:00) csendes-óceáni idő (Egyesült Államok és Kanada).
    - **Statikus IP-konfiguráció**: Beállítja azt a környezetet, hogy statikus IP-cím. Ellenkező esetben amikor a telepítő újraindítja a cloudbuilder.vhx, a hálózati adapterek vannak konfigurálva a DHCP.
11. Kattintson a **Tovább** gombra.
12. Ha az előző lépésben a statikus IP-konfigurációt, akkor most le kell:
    - Válasszon olyan hálózati adaptert. Győződjön meg arról is elérheti az adapter kattintás előtt **következő**.
    - Győződjön meg arról, hogy a **IP-cím**, **átjáró**, és **DNS** értékek megfelelőek, és kattintson a **következő**.
13. Kattintson a **következő** az előkészítési folyamat elindításához.
14. Az előkészítési azt jelzi, ha **befejezve**, kattintson a **következő**.
15. Kattintson a **Újraindítás most** a cloudbuilder.vhdx indul, és a telepítési folyamat folytatásához.


### <a name="deploy-the-development-kit-using-a-guided-experience"></a>A szoftverfejlesztői készlet használatával elősegítheti telepítése
A ASDK számítógép előkészítése, miután a ASDK az alábbi lépéseket követve CloudBuilder.vhdx lemezképpel is telepíthető. 
1. Miután a számítógép sikeresen elindul a CloudBuilder.vhdx lemezképpel, jelentkezzen be az előző lépésben megadott rendszergazdai hitelesítő adatokkal. 
2. Nyisson meg egy emelt szintű PowerShell-konzolt, és futtassa a **\AzureStack_Installer\asdk-installer.ps1** parancsfájlt (most lehet egy másik meghajtón a CloudBuilder.vhdx kép). Kattintson az **Install** (Telepítés) gombra.
3. Az a **típus** legördülő mezőben válassza **Azure felhőbe** vagy **AD FS**.
    - **Azure-felhőbe**: konfigurálja az Azure Active Directory (Azure AD) identitás-szolgáltatóként. A funkció használatához szüksége lesz az internethez, a teljes nevet, egy Azure ad directory-bérlő formájában *tartománynév*. onmicrosoft.com vagy egy Azure AD ellenőrizte az egyéni tartomány nevét, és a globális rendszergazdai hitelesítő adatokat a megadott könyvtár. 
    - **Az AD FS**: A címtárszolgáltatás lesz az identitásszolgáltató alapértelmezett stamp. Az alapértelmezett fiók a bejelentkezéshez azurestackadmin@azurestack.local, és a jelszó használata a telepítés részeként megadott.
4. A **helyi rendszergazda jelszavát**, a a **jelszó** mezőbe, írja be a helyi rendszergazda jelszavát (amelynek meg kell egyeznie az aktuális beállított helyi rendszergazdai jelszót), és kattintson a **Következő**.
5. Válasszon olyan hálózati adaptert kíván használni a csomagot, és kattintson a **következő**.
6. Válassza ki a DHCP vagy statikus hálózati BGPNAT01 virtuális gép konfigurációja.
    - **DHCP** (alapértelmezett): A virtuális gép IP-hálózati konfigurációja lekérdezi a DHCP-kiszolgálótól.
    - **Statikus**: csak akkor használja ezt a beállítást, ha a DHCP nem rendelhető hozzá egy érvényes IP-címet az Internet eléréséhez Azure verem. Egy statikus IP-címet meg kell adni, alhálózati maszk CIDR formátumban (például 10.0.0.5/24) hossza.
7. Szükség esetén állítsa be a következő értékeket:
    - **VLAN-azonosító**: Beállítja a VLAN-azonosítót. Csak akkor használja ezt a beállítást, ha a gazdagép és a AzS-BGPNAT01 konfigurálnia kell a VLAN-Azonosítót a fizikai hálózaton (és az internet) elérésére. 
    - **DNS-továbbító**: A DNS-kiszolgáló jön létre az Azure-verem központi telepítésének részeként. Lehetővé teszi a számítógépek belül a megoldás a stamp kívül nevek feloldására, adja meg a meglévő infrastruktúra DNS-kiszolgáló. A stamp a DNS-kiszolgáló ismeretlen névfeloldási erre a kiszolgálóra továbbítja.
    - **Kiszolgálót**: Ez a mező beállítja azt a kiszolgálót használják a csomag szükséges. Ez a paraméter egy érvényes idő kiszolgáló IP-címet meg kell adni. Kiszolgálók nevei nem támogatottak.
  
  > [!TIP]
  > Egy IP-címének, látogasson el a [pool.ntp.org](http:\\pool.ntp.org) vagy time.windows.com pingelését. 
  
8. Kattintson a **Tovább** gombra. 
9. A a **hálózati kártya kártya tulajdonságainak ellenőrzése** lapon egy folyamatjelző sáv megjelenik. 
    - Ha a felirat látható **egy frissítést nem lehet letölteni**, kövesse az utasításokat az oldalon.
    - Ha felirat látható **befejezve**, kattintson a **következő**.
10. A **összegzés** kattintson **telepítés**. Itt is másolhatja a telepítés PowerShell-parancsokat, a csomag telepítéséhez használt.
11. Az Azure AD-telepítés használata, a program megkéri adja meg Azure AD globális rendszergazdai fiók hitelesítő adatait a telepítő indítása után néhány perc múlva.
12. A telepítési folyamat is igénybe vehet néhány órát, amely során a rendszer automatikusan újraindítja a számítógépet egyszer. Ha a telepítés sikeres, a PowerShell-konzolon jeleníti meg: **COMPLETE: "Telepítés" művelet**. Ha a telepítés nem sikerül, akkor is [újratelepíteni](azure-stack-redeploy.md) teljesen új vagy használata a következő PowerShell-parancsok, azonos emelt szintű PowerShell-ablakából, az utolsó sikeres lépésben a központi telepítés újraindítása:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

   > [!IMPORTANT]
   > Ha azt szeretné, a telepítési folyamat figyelésére, jelentkezzen be azurestack\AzureStackAdmin, a telepítés során a development kit gazdagép újraindításakor. Ha jelentkezik be, és egy helyi rendszergazdai után a számítógép csatlakozik a tartományhoz, akkor nem látható a telepítés előrehaladását. Futtassa újra a központi telepítés, ne helyette bejelentkezés azurestack\AzureStackAdmin ellenőrzése, hogy fut-e.
   

## <a name="deploy-the-asdk-using-powershell"></a>A PowerShell használatával ASDK telepítése
Az előző lépések üzembe helyezhet egy interaktív felhasználói felület használatával ASDK telefonon. Azt is megteheti a PowerShell használatával telepítheti a development kit gazdagépen ASDK ebben a szakaszban ismertetett lépések szerint.

### <a name="configure-the-asdk-host-computer-to-boot-from-cloudbuildervhdx"></a>A CloudBuilder.vhdx-ból történő rendszerindításra képes ASDK gazdaszámítógép konfigurálása
Ezek a parancsok alá a letöltött és kibontott veremmel Azure virtuális merevlemez (CloudBuilder.vhdx) virtuális merevlemezről ASDK gazdaszámítógépen konfigurálja. A lépések elvégzése után indítsa újra a ASDK számítógép.

1. Nyissa meg egy parancssort rendszergazdaként.
2. Futtassa a `bcdedit /copy {current} /d "Azure Stack"` parancsot.
3. Másolás (CTRL + C) a CLSID érték lett visszaadva, többek között a szükséges {} "s. Ez az érték {CLSID} hivatkozunk, és a további lépéseket a beillesztése (CTRL + V vagy kattintson a jobb gombbal) kell.
4. Futtassa a `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx` parancsot. 
5. Futtassa a `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx` parancsot. 
6. Futtassa a `bcdedit /set {CLSID} detecthal on` parancsot. 
7. Futtassa a `bcdedit /default {CLSID}` parancsot.
8. Az indítási beállítások ellenőrzéséhez futtassa `bcdedit`. 
9. Győződjön meg arról, hogy a fájl átkerült a C:\ meghajtó (C:\CloudBuilder.vhdx) gyökérkönyvtárában, és indítsa újra a development kit gazdaszámítógép CloudBuilder.vhdx. A ASDK gazdagép újraindítása után a rendszer azt kell most alapértelmezés szerint a CloudBuilder.vhdx virtuális merevlemezről történő rendszerindítás. 

### <a name="prepare-the-development-kit-host-using-powershell"></a>A PowerShell használatával development kit gazdagép előkészítése 
Miután a development kit gazdaszámítógép sikeresen elindul a CloudBuilder.vhdx lemezképpel, nyisson meg egy emelt szintű PowerShell-konzolt, és ez a szakasz a development kit gazdagépen ASDK telepítéséhez futtassa a parancsokat.

> [!IMPORTANT] 
> ASDK támogat pontosan egy hálózati kártya (NIC) a hálózatkezeléshez. Ha több hálózati adapterrel rendelkezik, győződjön meg arról, hogy csak egy engedélyezve van (és összes többi le vannak tiltva) az üzembe helyezési parancsfájl futtatása előtt.

Az identitásszolgáltató az Azure AD vagy AD FS Azure verem telepítheti. Az Azure-vermet, erőforrás-szolgáltatók és más alkalmazások mindkét ugyanúgy működnek. Támogatott források és műveletek Azure verem AD FS-sel kapcsolatos további tudnivalókért tekintse meg a [szolgáltatások és-fogalmak](.\azure-stack-key-features.md) cikk.

> [!TIP]
> Ha nem ad meg (lásd a választható paraméterek: InstallAzureStackPOC.ps1 és az alábbi példák) beállítási paraméterekkel, kérni fogja a szükséges paraméterek.

### <a name="deploy-azure-stack-using-azure-ad"></a>Azure AD használata ingyenes Azure-verem telepítése 
Azure verem telepítendő **az Azure AD az identitás-szolgáltatóként**, internetkapcsolattal kell rendelkeznie, közvetlenül vagy transzparens proxyra. Futtassa a következő PowerShell-parancsokat a Azure AD használata ingyenes csomag telepítése:

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator     
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password
  ```

  Néhány percig, ASDK telepítési kérni fogja az Azure AD hitelesítő adatait. Az Azure AD-bérlő globális rendszergazda hitelesítő adatait kell megadnia. 

### <a name="deploy-azure-stack-using-ad-fs"></a>AD FS segítségével Azure verem telepítése 
A csomag telepítéséhez **AD FS segítségével az identitás-szolgáltatóként**, futtassa a következő PowerShell-parancsokat (egyszerűen adja hozzá a - UseADFS paramétert): 

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator 
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -UseADFS
  ```

Az AD FS-telepítések az alapértelmezett stamp címtárszolgáltatás szolgál az identitás-szolgáltatóként. Az alapértelmezett fiók a bejelentkezéshez azurestackadmin@azurestack.local, és a PowerShell-parancsok telepítés részeként biztosított úgy lesz beállítva, a jelszót.

A telepítési folyamat néhány órára, amely idő alatt a rendszer automatikusan újraindítás után is igénybe vehet. Ha a telepítés sikeres, a PowerShell-konzolon jeleníti meg: **COMPLETE: "Telepítés" művelet**. Ha nem sikerül a telepítés, próbálkozzon újra a parancsfájl futtatása a - Újrafuttatás paramétert. Is [ASDK újratelepíteni](.\azure-stack-redeploy.md) teljesen.
> [!IMPORTANT]
> Ha azt szeretné, a központi telepítés előrehaladást mérik a ASDK gazdagép újraindítása után, akkor be kell jelentkeznie, AzureStack\AzureStackAdmin. Ha jelentkezik be, és helyi rendszergazda a számítógép újraindítása (és azurestack.local tartományhoz csatlakozik), akkor nem látható a telepítés előrehaladását. Futtassa újra a központi telepítés, ne helyette bejelentkezés azurestack ellenőrzése, hogy fut-e.
>

#### <a name="azure-ad-deployment-script-examples"></a>Az Azure AD telepítési parancsfájl példák
Lehet parancsprogramot futtatni a teljes Azure AD telepítés. Az alábbiakban néhány megjegyzésként példa, amely tartalmazza az egyes nem kötelező paraméterek.

Ha az Azure AD identity csak társított **egy** Azure AD-címtár:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 52.168.138.145 #Example time server IP address.
```

Ha az Azure AD identity kapcsolódó **nagyobb, mint egy** Azure AD-címtár:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" #Example: user@AADDirName.onmicrosoft.com 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -InfraAzureDirectoryTenantName "<Azure AD directory in the form of domainname.onmicrosoft.com or an Azure AD verified custom domain name>" -TimeServer 52.168.138.145 #Example time server IP address.
```

Ha a környezet **nem** van DHCP engedélyezve van, akkor meg kell adni a következő további paraméterek (például használati megadott) feletti lehetőségek közül: 

```powershell
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -NatIPv4Subnet 10.10.10.0/24 -NatIPv4Address 10.10.10.3 -NatIPv4DefaultGateway 10.10.10.1 -TimeServer 10.222.112.26
```

### <a name="asdk-installazurestackpocps1-optional-parameters"></a>ASDK InstallAzureStackPOC.ps1 választható paraméterek:
|Paraméter|Kötelező/választható|Leírás|
|-----|-----|-----|
|AdminPassword|Szükséges|A helyi rendszergazda fiók és minden egyéb felhasználói fiók beállítása development kit központi telepítésének részeként létrehozott összes virtuális gépet. Ezt a jelszót meg kell egyeznie az aktuális helyi rendszergazda jelszavát, a gazdagépen.|
|InfraAzureDirectoryTenantName|Szükséges|Beállítja a bérlő címtárát. A paraméter segítségével adjon meg egy adott könyvtár ahol az AAD-fiókba jogosult több könyvtárak kezeléséhez. Teljes név egy AAD Directory bérlő formátumban. onmicrosoft.com vagy egy Azure AD ellenőrizte az egyéni tartomány nevét.|
|TimeServer|Szükséges|Ez a paraméter segítségével adjon meg egy konkrét kiszolgálót. Ez a paraméter egy érvényes idő kiszolgáló IP-címet meg kell adni. Kiszolgálók nevei nem támogatottak.|
|InfraAzureDirectoryTenantAdminCredential|Optional|Beállítja az Azure Active Directory-felhasználónevet és jelszót. Ezek az Azure hitelesítő adatait kell megadni a szervezeti azonosítóval.|
|InfraAzureEnvironment|Optional|Válassza ki az Azure környezetben szeretné regisztrálni a Azure Alkalmazásveremben üzembe. A választható lehetőségek nyilvános Azure-ban Azure - Kína, Azure - Amerikai Egyesült államokbeli kormányzati.|
|DNSForwarder|Optional|A DNS-kiszolgáló jön létre az Azure-verem központi telepítésének részeként. Lehetővé teszi a számítógépek belül a megoldás a stamp kívül nevek feloldására, adja meg a meglévő infrastruktúra DNS-kiszolgáló. A stamp a DNS-kiszolgáló ismeretlen névfeloldási erre a kiszolgálóra továbbítja.|
|NatIPv4Address|A DHCP hálózati Címfordítás támogatása szükséges|Egy statikus IP-cím beállítása MAS-BGPNAT01. Csak akkor használja ezt a paramétert, ha a DHCP nem tud érvényes IP-címet rendelni az internet eléréséhez.|
|NatIPv4Subnet|A DHCP hálózati Címfordítás támogatása szükséges|NAT-támogatás a DHCP használt IP-alhálózat előtag. Csak akkor használja ezt a paramétert, ha a DHCP nem tud érvényes IP-címet rendelni az internet eléréséhez.|
|PublicVlanId|Optional|Beállítja a VLAN-azonosítót. Csak akkor használja ezt a paramétert, ha a gazdagép és a MAS-BGPNAT01 konfigurálnia kell a VLAN-Azonosítót a fizikai hálózaton (és az Internet) elérésére. Például.\InstallAzureStackPOC.ps1-Verbose - PublicVLan 305|
|Futtassa újra a műveletet|Optional|Ez a jelző használatával futtassa újra a központi telepítés. Minden korábbi bevitel szolgál. Korábban megadott újbóli belépés adatokat nem támogatott, mert több egyedi értékek jön létre, és használni a központi telepítéshez.|

## <a name="activate-the-administrator-and-tenant-portals"></a>A rendszergazda és bérlői portálon aktiválása
Után használó központi telepítések az Azure AD aktiválnia kell mindkét az Azure verem rendszergazda és bérlői portálon. Az aktiválás hozzájárul a verem Azure portál és az Azure Resource Manager a megfelelő engedélyeket ad (a hozzájárulási oldalon felsorolt) az összes felhasználó számára a könyvtár.

- A felügyeleti portál https://adminportal.local.azurestack.external/guest/signup navigáljon az információk elolvasásához, és kattintson **elfogadás**. Után elfogadása, a szolgáltatás kevésbé jártas rendszergazdák számára is directory bérlői rendszergazdákat is hozzáadhat.

- A bérlői portál https://portal.local.azurestack.external/guest/signup navigáljon az információk elolvasásához, és kattintson **elfogadás**. Elfogadása után a címtárban szereplő felhasználó bármikor beléphet a bérlői portálra. 

> [!NOTE] 
> Ha nem aktiválta a portálon, a címtár csak a rendszergazda bejelentkezhet és használja a portálok. Semmilyen más felhasználó jelentkezik be, ha azok hibaüzenetet kap, amely meghatározza, hogy a rendszergazda nem adott engedélyek más felhasználók számára. Ha a rendszergazda natív módon nem tartozik Azure verem regisztrálva van a könyvtárra, az Azure-verem directory csatolni kell az aktiválási URL-címet. Például, ha Azure verem van regisztrálva a fabrikam.onmicrosoft.com és a rendszergazdai jogú felhasználó nem admin@contoso.com, keresse meg a portál aktiválásához https://portal.local.azurestack.external/guest/signup/fabrikam.onmicrosoft.com. 

## <a name="reset-the-password-expiration-policy"></a>A jelszó-elévülési szabályzatának alaphelyzetbe állítása 
Győződjön meg arról, hogy a jelszót ahhoz, hogy a development kit gazdagép az értékelés időszak lejárta előtt nem lejár, tegye a következőket a ASDK telepítése után.

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>A jelszó-elévülési szabályzatának módosítása a Powershellből:
Futtassa a parancsot egy rendszergazda jogú Powershell-konzolt:

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>A jelszó-elévülési szabályzatának manuális módosítása:
1. Nyissa meg a development kit gazdagép **csoportházirend-kezelő** , és keresse meg **csoportházirend-kezelő** – **erdő: azurestack.local** – **tartományok** – **azurestack.local**.
2. Kattintson a jobb gombbal **alapértelmezett tartományházirend** kattintson **szerkesztése**.
3. Navigáljon a a Csoportházirendkezelés-szerkesztő, **számítógép konfigurációja** – **házirendek** – **Windows-beállítások** – **biztonsági beállítások**– **Fiókházirendek** – **jelszóházirend**.
4. A jobb oldali ablaktáblában kattintson duplán a **jelszó maximális élettartama**.
5. Az a **jelszó maximális élettartama tulajdonságok** párbeszédpanelen módosítsa a **a jelszó lejár** 180 értékét, majd kattintson az **OK**.


## <a name="next-steps"></a>Következő lépések

[Csatlakozás az Azure Stackhez](azure-stack-connect-azure-stack.md)

[A telepítő PowerShell Azure verem környezetek](azure-stack-powershell-configure-quickstart.md)

[Azure verem regisztrálása az Azure-előfizetéshez](azure-stack-register.md)



