---
title: Titkos kulcsok az Azure Stackben rotálása |} A Microsoft Docs
description: Ismerje meg az Azure Stack titkos kulcsainak rotálása.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: cc7b1b9e96e32b090c0ec9ec9ab029588e5ec4ce
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166967"
---
# <a name="rotate-secrets-in-azure-stack"></a>Azure Stack titkos kulcsainak rotálása

*Ezek az utasítások csak az Azure Stack integrált rendszerek verzió 1803, és később érvényesek. Ne kísérelje meg az 1802-előtti Azure Stack-verzión titkos rotációja*

Az Azure Stack különböző titkos kódok kezelése az Azure Stack-infrastruktúra-erőforrások és szolgáltatások közötti biztonságos kommunikációhoz használ.

- **Belső titkos kulcsok**  
Az összes a tanúsítványok, jelszavak, biztonságos karakterláncok és kulcsok az Azure Stack-infrastruktúra az Azure Stack-operátorokról beavatkozása nélkül használják. 

- **Külső titkos kódok**  
Infrastruktúra-szolgáltatás tanúsítványok kívülre irányuló szolgáltatások, az Azure Stack – operátor által biztosított. Ez magában foglalja a tanúsítványokat, a következő szolgáltatásokat: 
    - Adminisztrátori portál 
    - Nyilvános portálra 
    - Rendszergazda az Azure Resource Manager 
    - Globális Azure Resource Manager 
    - Rendszergazdai Keyvault 
    - Keyvault 
    - Az ACS-(beleértve a blob, table és queue storage) 
    - ADFS<sup>*</sup>
    - Gráf<sup>*</sup>

   <sup>*</sup> Csak alkalmazható, ha a környezet identitásszolgáltató Active Directory összevonási szolgáltatásokban (AD FS).

> [!NOTE]
> Bármely más biztonságos kulcsokat és a karakterláncokat, beleértve a BMC, és váltson a jelszavak, a rendszergazda manuálisan is frissíti a felhasználói és rendszergazdai fiókok jelszavait. 

Az Azure Stack-infrastruktúra biztonságának fenntartása, operátorok rendszeres időközönként elforgatásának az infrastruktúra titkos kódok, amelyek a szervezeti biztonsági követelményeknek való következetes frekvencia kell.

### <a name="rotating-secrets-with-external-certificates-from-a-new-certificate-authority"></a>Egy új hitelesítésszolgáltató tanúsítványokat külső titkos kódok elforgatása

Az Azure Stack egy új tanúsítvány hitelesítésszolgáltató (CA) származó külső tanúsítványaival titkos Elforgatás a következő kontextusban támogatja:

|Telepített tanúsítvány Hitelesítésszolgáltatói|Hitelesítésszolgáltató elforgatása|Támogatott|Támogatott az Azure Stack-verziók|
|-----|-----|-----|-----|
|Az önaláírt|Enterprise|Nem támogatott||
|Az önaláírt|Az önaláírt|Nem támogatott||
|Az önaláírt|Nyilvános<sup>*</sup>|Támogatott|1803 és újabb verziók|
|Enterprise|Enterprise|Mindaddig, amíg az ügyfelek használják a azonos vállalati hitelesítésszolgáltató központi telepítéskor használt támogatott|1803 és újabb verziók|
|Enterprise|Az önaláírt|Nem támogatott||
|Enterprise|Nyilvános<sup>*</sup>|Támogatott|1803 és újabb verziók|
|A nyilvános<sup>*</sup>|Enterprise|Nem támogatott|1803 és újabb verziók|
|A nyilvános<sup>*</sup>|Az önaláírt|Nem támogatott||
|A nyilvános<sup>*</sup>|Nyilvános<sup>*</sup>|Támogatott|1803 és újabb verziók|

<sup>*</sup> Nyilvános hitelesítésszolgáltatók az alábbiakban azokat, amelyek a Windows megbízható legfelső szintű programhoz. A teljes listát megtalálja [a Microsoft megbízható Root Certificate Program: (2017. június 27.,) résztvevők](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).

## <a name="alert-remediation"></a>Riasztás szervizelése

Ha a titkos kulcsok érvényessége 30 napon belül, a következő riasztások jönnek létre a felügyeleti portálon: 

- Függőben levő fiók jelszólejárat 
- Függőben lévő belső tanúsítvány lejárata 
- Függőben lévő külső tanúsítvány lejárata 

Az alábbi utasításokat követve titkos Elforgatás futó fog kijavítani ezeket a riasztásokat.

## <a name="pre-steps-for-secret-rotation"></a>Titkos elforgatás előtti lépések

   > [!IMPORTANT]  
   > Győződjön meg arról, titkos Elforgatás még nem lett sikeresen végrehajtva a környezetében. Ha titkos Elforgatás már hajtja végre, frissítse az Azure Stack 1807 vagy újabb verziójára titkos Elforgatás végrehajtása előtt. 
1.  Operátorok Észreveheti a riasztások megnyitásához, és automatikusan zárja be az Azure Stack titkos kódok rotációja során.  Ez az elvárt viselkedés, és a riasztások figyelmen kívül hagyható.  Operátorok érvényességét, ezek a riasztások ellenőrzéséhez futtassa a Test-AzureStack.  Az operátorok figyelése SCOM használatával Azure Stack-rendszereket, karbantartási módba helyezi el a rendszer megakadályozza, hogy ezek a riasztások éri el az ITSM-rendszerekkel, de továbbra is riasztás, ha az Azure Stack rendszer elérhetetlenné válik. 
2. A felhasználók értesítése a karbantartási műveleteket. Normál karbantartási időszakokat, a lehető legnagyobb mértékben, során munkaidőn kívüli ütemezése. Karbantartási műveletek hatással lehet a felhasználó számítási feladatok és a webportálos műveletek.
    > [!note]  
    > A következő lépések csak akkor alkalmazható, ha az Azure Stack külső titkos kódok elforgatása.
3. Készítse el új helyettesítési külső tanúsítványok. Az új készlet a tanúsítvány előírásoknak megfelel a [Azure Stack PKI-tanúsítványkövetelmények](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs).
4.  A biztonsági mentés rotációs biztonsági mentési biztonságos helyen használt tanúsítványok Store. Az elforgatás fut, és akkor sikertelen lesz, ha cserélje le a tanúsítványokat a fájlmegosztásban a biztonsági másolatokat előtt, futtassa újra a elforgatási. Vegye figyelembe, a biztonsági másolatok megőrzése a biztonsági mentési biztonságos helyen.
5.  Hozzon létre egy fájlmegosztást a ERCS virtuális gépeken érhető el. A fájlmegosztás olvasható és írható a kell lennie. a **CloudAdmin** identitás.
6.  Nyisson meg egy PowerShell ISE-ben konzolt egy olyan számítógépről, ahol hozzáférhet a fájlmegosztáson. Keresse meg a fájlmegosztás. 
7.  Futtatás **[CertDirectoryMaker.ps1](http://www.aka.ms/azssecretrotationhelper)** a szükséges könyvtárak a külső tanúsítványok létrehozásához.

## <a name="rotating-external-and-internal-secrets"></a>Belső és külső titkos kódok elforgatása

Mind a külső egy belső titkos kulcs rotálása:

1. Az újonnan létrehozott belül **/tanúsítványok** az üzem előtti lépésben létrehozott directory helyezze a külső tanúsítványok cseréje új készletét a könyvtárstruktúra szerint kötelező tanúsítványok szakaszában formátuma az a [Azure Stack PKI-tanúsítványkövetelmények](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates).
2. Hozzon létre egy PowerShell-munkamenetet a [kiemelt végponthoz](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint) használatával a **CloudAdmin** fiókját, és tárolja a munkamenetek változóként. Ezt a változót a következő lépésben paraméterként fogja használni.

    > [!IMPORTANT]  
    > Adja meg a munkamenet, ne tárolja a munkamenet változóként.
    
3. Futtatás  **[invoke-command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-5.1)**. Adja át, a kiemelt végponthoz PowerShell munkamenet-változó a **munkamenet** paraméter. 
4. Futtatás **Start-SecretRotation** a következő paraméterekkel:
    - **PfxFilesPath**  
    Adja meg a korábban létrehozott tanúsítványok címtár hálózati elérési útját.  
    - **PathAccessCredential**  
    Egy PSCredential objektumot, a hitelesítő adatokat a megosztáshoz. 
    - **CertificatePassword**  
    A jelszót az összes létrehozott pfx-tanúsítvány fájlokat egy biztonságos karakterláncot.
4. Várjon, amíg a titkos kulcsok elforgatása.  
Titkos kód Elforgatás sikeres befejeződése után a konzol megjeleníti **összesített művelet állapota: sikeres**. 
    > [!note]  
    > Ha titkos elforgatás nem sikerül, kövesse a hibaüzenet a, és futtassa újra a start-secretrotation az a **-ismétlés** paraméter. Forduljon az ügyfélszolgálathoz tapasztal titkos Elforgatás hibák ismétlődik. 
5. Titkos elforgatásának szögét a sikeres telepítést követően távolítsa el a tanúsítványokat az üzem előtti. lépésében létrehozott megosztás, és azok biztonságos biztonsági mentési helyen tárolja őket. 

## <a name="walkthrough-of-secret-rotation"></a>Titkos kód elforgatásának szögét forgatókönyv

A következő PowerShell-példa bemutatja a parancsmagokat és futtatni kell a titkos kulcsok elforgatása paraméterek.

```powershell
#Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IPofERCSMachine>"}'
$PEPCreds = Get-Credential 
$PEPsession = New-PSSession -computername <IPofERCSMachine> -Credential $PEPCreds -ConfigurationName PrivilegedEndpoint 

#Run Secret Rotation
$CertPassword = ConvertTo-SecureString "Certpasswordhere" -AsPlainText -Force
$CertShareCred = Get-Credential 
$CertSharePath = "<NetworkPathofCertShare>"
Invoke-Command -session $PEPsession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword }
Remove-PSSession -Session $PEPSession
```
## <a name="rotating-only-internal-secrets"></a>Csak a belső titkok elforgatása

Csak az Azure Stack belső titkos kulcsok rotálására:

1. Hozzon létre egy PowerShell-munkamenetet a [kiemelt végponthoz](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).
2. A kiemelt végponthoz-munkamenetben futtassa **Start-SecretRotation** argumentumok nélkül.
3. Várjon, amíg a titkos kulcsok elforgatása.  
Titkos kód Elforgatás sikeres befejeződése után a konzol megjeleníti **összesített művelet állapota: sikeres**. 
    > [!note]  
    > Ha titkos elforgatás nem sikerül, kövesse a hibaüzenet a, és futtassa újra a start-secretrotation az a **-futtassa újra a** paraméter. Forduljon az ügyfélszolgálathoz tapasztal titkos Elforgatás hibák ismétlődik. 

## <a name="start-secretrotation-reference"></a>Start-SecretRotation referencia

A titkos kulcsok az Azure Stack rendszer forog. Az Azure Stack kiemelt végponthoz csak hajtott végre.

### <a name="syntax"></a>Szintaxis

Elérési út (alapértelmezett)

```powershell
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential] <PSCredential> [-CertificatePassword <SecureString>]  
```

### <a name="description"></a>Leírás

A Start-SecretRotation parancsmag elforgatása az infrastruktúra titkos kulcsok Azure Stack a rendszer. Ez a belső infrastruktúra-hálózathoz elérhetővé tett összes titkos forog alapértelmezés szerint felhasználói bevitellel azt is forog minden külső hálózati infrastruktúra végpontok tanúsítványait. Elforgatás külső hálózati infrastruktúra-végpontokra, ha Start-SecretRotation végrehajtja az Invoke-Command parancsfájlblokkban keresztül az Azure Stack-környezet kiemelt végponthoz munkamenetbe a paraméterként a a munkamenet.
 
### <a name="parameters"></a>Paraméterek

| Paraméter | Típus | Szükséges | Pozíció | Alapértelmezett | Leírás |
| -- | -- | -- | -- | -- | -- |
| PfxFilesPath | Sztring  | False (Hamis)  | nevű  | None  | A fájlmegosztás elérési útját a **\Certificates** könyvtárra, amelyben minden külső hálózati végpont tanúsítványokat. Csak akkor szükséges, ha külső titkos kódok és az összes titkos elforgatása. Záró könyvtárnak kell lennie **\Certificates**. |
| CertificatePassword | SecureString | False (Hamis)  | nevű  | None  | A jelszó - PfXFilesPath megadott összes tanúsítvány esetében. Kötelező érték, ha PfxFilesPath biztosított belső és külső titkos kódok vannak-e forgatni. |
| PathAccessCredential | PSCredential | False (Hamis)  | nevű  | None  | A fájlmegosztáson az PowerShell hitelesítő adatait a **\Certificates** könyvtárra, amelyben minden külső hálózati végpont tanúsítványokat. Csak akkor szükséges, ha külső titkos kódok és az összes titkos elforgatása.  |
| Újrafuttatás | SwitchParameter | False (Hamis)  | nevű  | None  | Futtassa újra a titkos rotáció újbóli kísérlete sikertelen próbálkozások után bármikor kell használni. |

### <a name="examples"></a>Példák
 
**Csak a belső infrastruktúra titkok elforgatása**

```powershell  
PS C:\> Start-SecretRotation  
```

Ezzel a paranccsal az összes Azure Stack belső hálózaton vannak kitéve, az infrastruktúra titkos kulcsok forog. Start-SecretRotation forog verem által létrehozott összes titkos, de mivel nincsenek megadott tanúsítványok, külső végpont tanúsítványok nem rotációja.  

**Belső és külső infrastruktúra titkok elforgatása**
  
```powershell
PS C:\> Invoke-Command -session $PEPSession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword } 
Remove-PSSession -Session $PEPSession
```

Ezzel a paranccsal az összes Azure Stack belső hálózaton vannak kitéve, az infrastruktúra titkos kulcsok, valamint az Azure Stack külső hálózati infrastruktúra-végpontokra használt TLS-tanúsítványok elforgatása. Start-SecretRotation forog verem által létrehozott összes titkos, és a tanúsítványok szolgálnak, mert külső végpont tanúsítványokat is rotációja.  


## <a name="update-the-baseboard-management-controller-bmc-password"></a>Az alaplapi felügyeleti vezérlőnek (BMC) jelszó frissítése

Az alaplapi felügyeleti vezérlőnek (BMC) figyeli a fizikai kiszolgálókhoz. Az előírások és az utasításokat a BMC-jelszófrissítés eredeti hardvergyártó (OEM) hardvergyártójához megoldástól. Frissítenie kell az Azure Stack-összetevők, egy bevezetésére jelszavakat.

1. Frissítés az Azure Stack fizikai kiszolgálókon futó bmc-k az OEM utasításai szerint. A jelszót a környezetben minden egyes bmc azonosnak kell lennie.
2. Nyisson meg egy emelt szintű végpontot az Azure Stack-munkamenetekben. További útmutatást lásd: [a rendszerjogosultságú végpont használata az Azure Stackben](azure-stack-privileged-endpoint.md).
3. Után a PowerShell parancssort változott **[IP-cím vagy ERCS virtuális gép neve]: PS >** vagy **[azs-ercs01]: PS >**, attól függően, a környezet futtatása `Set-BmcPassword` futtatásával `invoke-command`. Paraméterként adja át a kiemelt végponthoz munkamenet-változó. Példa:

    ```powershell
    # Interactive Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PECred = Get-Credential "<Domain>\CloudAdmin" -Message "PE Credentials" 
    $NewBMCpwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    Remove-PSSession -Session $PEPSession
    ```
    
    A statikus PowerShell-verzió adódnak a Jelszavaival, kódsorok is használhatja:
    
    ```powershell
    # Static Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEUser = "<Privileged Endpoint user for exmaple Domain\CloudAdmin>"
    $PEpwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PECred = New-Object System.Management.Automation.PSCredential ($PEUser, $PEpwd) 
    $NewBMCpwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    Remove-PSSession -Session $PEPSession
    ```

## <a name="next-steps"></a>További lépések

[További információ az Azure Stack-biztonság](azure-stack-security-foundations.md)
