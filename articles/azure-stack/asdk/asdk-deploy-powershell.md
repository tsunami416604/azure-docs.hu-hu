---
title: Az Azure verem - PowerShell telepítése |} Microsoft Docs
description: Ebben a cikkben a ASDK a parancssorból a PowerShell használatával telepítse.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: ''
ms.date: 06/07/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: f0d7daa479f6e6ea345e010962488c1ecad5b7e2
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34849957"
---
# <a name="deploy-the-asdk-from-the-command-line"></a>A parancssorból ASDK telepítése
A ASDK egy tesztelési és fejlesztési környezet értékelje ki, és bemutatja a verem Azure-szolgáltatások és szolgáltatások telepítése. Töltse le innen működik, és szüksége a környezet hardver készít, és néhány (Ez órát fog igénybe venni több) parancsfájlok futtatása. Ezt követően is bejelentkezik a rendszergazdai és felhasználói portálok Azure verem elindítására.

## <a name="prerequisites"></a>Előfeltételek 
Készítse elő a development kit gazdaszámítógépen. Tervezze meg a hardveres, szoftveres és hálózati. A szoftverfejlesztői készlet (development kit fogadó) futtató számítógépen meg kell felelnie hardveres, szoftveres és hálózati követelményeknek. Azure Active Directory (Azure AD) vagy Active Directory összevonási szolgáltatások (AD FS) használata között is ki kell választania. Ne feledje, hogy a telepítési folyamat zökkenőmentesen, a a telepítés elindítása előtt az Előfeltételek ahhoz, hogy. 

A ASDK telepítése, előtt győződjön meg arról, hogy a tervezett development kit fogadó számítógép hardverének, az operációs rendszer, a fiók és a hálózati konfiguráció felel meg a ASDK telepítésének minimális követelményeit.

**[Tekintse át a ASDK telepítés tervezési szempontok](asdk-deploy-considerations.md)**

> [!TIP]
> Használhatja a [Azure verem telepítési követelményeinek ellenőrzése eszköz](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) annak ellenőrzéséhez, hogy a hardver megfelel-e az összes követelménynek az operációs rendszer telepítése után.

## <a name="download-and-extract-the-deployment-package"></a>Töltse le, és bontsa ki a központi telepítési csomag
Győződjön meg arról, hogy a development kit fogadó számítógép megfelel-e a ASDK telepítésének alapvető követelményeit, a következő lépésre letöltéséhez és kibontásához a ASDK központi telepítési csomagot. A központi telepítési csomag tartalmazza a Cloudbuilder.vhdx fájlt, amely a virtuális merevlemez, amely egy rendszerindításra alkalmas operációs rendszer és az Azure-verem fájlokat tartalmazza.

A központi telepítési csomag letöltheti a development kit gazdagépen vagy egy másik számítógépre. A kibontott tároló 60 GB szabad lemezterület, tarthat, ezért egy másik számítógép segítségével csökkentheti a development kit gazdagép hardverkövetelményeinek.

**[Töltse le, és bontsa ki az Azure verem Development Kit (ASDK)](asdk-download.md)**

## <a name="prepare-the-development-kit-host-computer"></a>Készítse elő a development kit gazdaszámítógépen
A ASDK az állomáson telepítése előtt elő kell készíteni a környezetet, és a rendszer rendszerindító virtuális merevlemezről konfigurálva. Elvégezte a lépést a development kit gazdagép fog indulni, a Cloudbuilder.vhdx (egy virtuális merevlemez-meghajtóról egy rendszerindításra alkalmas operációs rendszer és az Azure-verem fájlokat tartalmaz).

A PowerShell szolgáltatás használatával történő rendszerindításra CloudBuilder.vhdx a ASDK gazdaszámítógép konfigurálása. Ezek a parancsok a ASDK fogadó számítógép alá a letöltött és kibontott veremmel Azure virtuális merevlemez (CloudBuilder.vhdx) ból történő rendszerindításra képes konfigurálása. A lépések elvégzése után indítsa újra a ASDK számítógép.

A ASDK gazdaszámítógép CloudBuilder.vhdx rendszerindítás konfigurálása:

  1. Nyissa meg egy parancssort rendszergazdaként.
  2. Futtassa a `bcdedit /copy {current} /d "Azure Stack"` parancsot.
  3. Másolás (CTRL + C) a CLSID értéke ad vissza, beleértve a szükséges {}"s. Ez az érték {CLSID} hivatkozunk, és a további lépéseket a beillesztése (CTRL + V vagy kattintson a jobb gombbal) kell.
  4. Futtassa a `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx` parancsot. 
  5. Futtassa a `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx` parancsot. 
  6. Futtassa a `bcdedit /set {CLSID} detecthal on` parancsot. 
  7. Futtassa a `bcdedit /default {CLSID}` parancsot.
  8. Az indítási beállítások ellenőrzéséhez futtassa `bcdedit`. 
  9. Győződjön meg arról, hogy a fájl átkerült a C:\ meghajtó (C:\CloudBuilder.vhdx) gyökérkönyvtárában, és indítsa újra a development kit gazdaszámítógép CloudBuilder.vhdx. A ASDK számítógép újraindítása után a rendszer a kell rendszerindító a CloudBuilder.vhdx virtuális gép merevlemez-meghajtóról ASDK telepítésének megkezdéséhez. 

> [!IMPORTANT]
> Győződjön meg arról, hogy közvetlen fizikai gépek vagy a development kit gazdaszámítógép KVM a hozzáférést az újraindítás előtt. Indításakor a virtuális Gépet, a rendszer kérni fogja, hogy a Windows Server-telepítés befejezéséhez. Jelentkezzen be a fejlesztési kit gazdaszámítógép használt azonos rendszergazdai hitelesítő adatait adja meg. 

### <a name="prepare-the-development-kit-host-using-powershell"></a>A PowerShell használatával development kit gazdagép előkészítése 
A csomag után gazdaszámítógépen sikeresen indul el, a CloudBuilder.vhdx lemezképpel, jelentkezzen be a azonos helyi rendszergazdai hitelesítő adatokkal jelentkezzen be a fejlesztési kit gazdaszámítógép (és a Windows Server véglegesítése részeként megadott A telepítő a számítógép virtuális merevlemezről indított). 

> [!NOTE]
> Másik lehetőségként beállíthatja úgy is [Azure verem telemetriai beállítások](asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *előtt* a ASDK telepítése.

Nyisson meg egy emelt szintű PowerShell-konzolt, és ez a szakasz a development kit gazdagépen ASDK telepítéséhez futtassa a parancsokat.

> [!IMPORTANT] 
> ASDK támogat pontosan egy hálózati kártya (NIC) a hálózatkezeléshez. Ha több hálózati adapterrel rendelkezik, győződjön meg arról, hogy csak egy engedélyezve van (és összes többi le vannak tiltva) az üzembe helyezési parancsfájl futtatása előtt.

Az Azure ad-val Azure verem telepítése vagy a Windows Server AD FS az identitás-szolgáltatóként. Az Azure-vermet, erőforrás-szolgáltatók és más alkalmazások mindkét ugyanúgy működnek.

> [!TIP]
> A telepítő paramétereket (lásd a választható paraméterek: InstallAzureStackPOC.ps1 és az alábbi példák) ne adja meg, ha a szükséges paraméterek kéri.

### <a name="deploy-azure-stack-using-azure-ad"></a>Azure AD használata ingyenes Azure-verem telepítése 
Azure verem telepítendő **az Azure AD az identitás-szolgáltatóként**, internetkapcsolattal kell rendelkeznie, közvetlenül vagy transzparens proxyra. 

Futtassa a következő PowerShell-parancsokat a Azure AD használata ingyenes csomag telepítése:

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

A telepítési folyamat néhány órára, amely idő alatt a rendszer automatikusan újraindítás után is igénybe vehet. Ha a telepítés sikeres, a PowerShell-konzolon jeleníti meg: **COMPLETE: "Telepítés" művelet**. Ha nem sikerül a telepítés, próbálkozzon újra a parancsfájl futtatása a - Újrafuttatás paramétert. Is [ASDK újratelepíteni](asdk-redeploy.md) teljesen.

> [!IMPORTANT]
> Ha azt szeretné, a központi telepítés előrehaladást mérik a ASDK gazdagép újraindítása után, akkor be kell jelentkeznie, AzureStack\AzureStackAdmin. Ha jelentkezik be, és helyi rendszergazda a számítógép újraindítása (és azurestack.local tartományhoz csatlakozik), akkor nem látható a telepítés előrehaladását. Futtassa újra a központi telepítés, ne helyette bejelentkezés azurestack ellenőrzése, hogy fut-e.


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

Ha a környezet nem rendelkezik engedélyezett DHCP, ezután meg kell adni a következő további paraméterek (például használati megadott) feletti lehetőségek közül: 

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
|Újrafuttatás|Optional|Ez a jelző használatával futtassa újra a központi telepítés. Minden korábbi bevitel szolgál. Korábban megadott újbóli belépés adatokat nem támogatott, mert több egyedi értékek jön létre, és használni a központi telepítéshez.|


## <a name="perform-post-deployment-configurations"></a>Telepítés utáni konfigurációk végrehajtása
Miután telepítette a ASDK, van néhány ajánlott telepítés utáni ellenőrzések és konfigurációs változásokat, mértékét. Ellenőrizheti a telepítés sikeresen megtörtént a test-AzureStack parancsmaggal lett telepítve, és telepítse az Azure PowerShell-verem és a GitHub eszközöket. 

Győződjön meg arról, hogy a nem az értékelés időszak lejárta előtt jár le a development kit gazdagép jelszavát a jelszó-elévülési szabályzatának is visszaállítsa.

> [!NOTE]
> Másik lehetőségként beállíthatja úgy is [Azure verem telemetriai beállítások](asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *után* a ASDK telepítése.

**[Utáni ASDK telepítési feladatok](asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Regisztrálja az Azure-ral
Azure verem regisztrálnia kell az Azure-ral, hogy [töltse le az Azure piactéren elemek](asdk-marketplace-item.md) Azure verem.

**[Azure verem regisztrálni Azure-ral](asdk-register.md)**

## <a name="next-steps"></a>További lépések
Gratulálunk! A lépések elvégzése után kell a csomag környezet mindkét [rendszergazda](https://adminportal.local.azurestack.external) és [felhasználói](https://portal.local.azurestack.external) portálok. 

[ASDK telepítés utáni konfigurációs feladatok](asdk-post-deploy.md)

