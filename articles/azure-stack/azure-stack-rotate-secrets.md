---
title: Forgassa el a titkos kulcsok Azure verem |} Microsoft Docs
description: Megtudhatja, hogyan forgassa el a titkos kulcsok Azure-készletben.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 49071044-6767-4041-9EDD-6132295FA551
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: a158da6fb397b864a439e067ca99d79814e2b8d2
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="rotate-secrets-in-azure-stack"></a>Forgassa el a titkos kulcsok Azure verem

*Ezek az utasítások csak Azure verem integrált rendszerek verzió 1803 és később vonatkoznak. Ne kísérelje meg előre-1802 Azure verem verzióin titkos Elforgatás*

Azure verem különböző titkos kulcsok kezelése az Azure-verem infrastruktúrához kapcsolódó erőforrások és szolgáltatások közötti biztonságos kommunikációhoz használ.

- **Belső titkos kulcsok**  
Minden tanúsítványok, jelszavak, biztonságos karakterláncot, és az Azure-verem üzemeltető beavatkozás nélkül a verem Azure infrastruktúra által használt kulcsok. 

- **Külső kulcsok**  
Infrastruktúra-szolgáltatási tanúsítványok kívülre irányuló szolgáltatások, az Azure-verem operátor által biztosított. Ez magában foglalja a tanúsítványokat, a következő szolgáltatásokhoz: 
    - Felügyeleti portál 
    - Nyilvános portál 
    - Az Azure erőforrás-kezelő rendszergazda 
    - Globális az Azure Resource Manager 
    - Rendszergazda Keyvault 
    - Keyvault 
    - ACS (beleértve a blob, table és a queue storage) 
    - ADFS<sup>*</sup>
    - Diagram<sup>*</sup>

    > <sup>*</sup> Csak alkalmazható, ha a környezet identitásszolgáltató Active Directory összevont szolgáltatások (AD FS).

> [!NOTE]
> Bármely más biztonságos kulcsok és a karakterláncokat, beleértve a bmc-n, és váltson a jelszavak, a rendszergazda manuálisan is frissíti a felhasználói és rendszergazdai fiókok jelszavait. 

Az Azure-verem infrastruktúra integritásának fenntartása operátorok kell rendszeresen elforgatásának az infrastruktúra titkok frekvencia, amelyek a szervezet biztonsági követelményekkel összhangban.

## <a name="alert-remediation"></a>Riasztási szervizelés

Ha a titkos kulcsok lejárati számított 30 napon belül, a következő riasztások jönnek létre a felügyeleti portálon: 

- Függőben lévő fiók jelszó lejárata 
- Függőben lévő belső tanúsítvány lejárta 
- Függőben lévő külső tanúsítvány lejárta 

Az alábbi utasításokat követve titkos Elforgatás futtató fogja szervizelni ezeket a riasztásokat.

## <a name="pre-steps-for-secret-rotation"></a>A titkos elforgatás előtti lépések

1.  Értesítse a felhasználókat a karbantartási műveleteket. Normál karbantartási időszakokat, amennyire csak lehetséges, során munkaidőn kívüli ütemezni. A karbantartási műveleteket hatással lehet a felhasználó munkaterhelések és a portál műveletek.

    > [!note]  
    > A következő lépések csak akkor alkalmazható, ha a külső kulcsok Azure verem elforgatása.

2.  Készítse el új helyettesítő külső tanúsítványok. Az új készlet megfelel az előírásoknak tanúsítványt a [Azure verem PKI-tanúsítványkövetelmények](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs).
3.  A biztonsági mentés Elforgatás biztonsági mentési biztonságos helyen használt tanúsítványok tárolása. A forgatási fut, és akkor sikertelen lesz, ha cserélje le a tanúsítványokat a fájlmegosztás a biztonsági másolatok előtt futtassa újra a elforgatási. Jegyezze fel, a biztonsági mentési biztonságos helyre a biztonsági másolatok megőrzése.
3.  Hozzon létre egy fájlmegosztás a ERCS virtuális gépek érhető el. A fájlmegosztás kell lennie az írható és olvasható a **CloudAdmin** identitás.
4.  Nyissa meg a PowerShell ISE konzoljába egy olyan számítógépről, melyekben a fájlmegosztáshoz való hozzáférést. Nyissa meg a fájlmegosztás. 
5.  Futtatás **[CertDirectoryMaker.ps1](http://www.aka.ms/azssecretrotationhelper)** a külső tanúsítványok szükséges könyvtárak létrehozása.

## <a name="rotating-external-and-internal-secrets"></a>Belső és külső kulcsok elforgatása

Mindkét külső egy belső titkos kulcsok megváltoztatása:

1. Az újonnan létrehozott belül **/tanúsítványok** a előtti lépések alapján létrehozott directory helyezze el a külső tanúsítványok cseréje új készletét a könyvtárstruktúra szerint formátuma megfelel a kötelező tanúsítványok csoportban az a [Azure verem PKI-tanúsítványkövetelmények](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates).
2. Hozzon létre egy PowerShell-munkamenetet a [kiemelt végpont](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint) használatával a **CloudAdmin** fiókot, és tárolja a munkamenetek változóként. Ez a változó a következő lépésben paraméterként fogja használni.

    > [!IMPORTANT]  
    > Adja meg a munkamenet, ne tárolja a munkamenet változóként.
    
3. Futtatás  **[invoke-Command parancsot](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-5.1)**. Adjon át, a kiemelt végpont PowerShell munkamenet-változó a **munkamenet** paraméter. 
4. Futtatás **Start-SecretRotation** a következő paraméterekkel:
    - **PfxFilesPath**  
    Adja meg a hálózati könyvtár elérési útját a tanúsítványok a korábban létrehozott.  
    - **PathAccessCredential**  
    Egy PSCredential objektumot, a hitelesítő adatokat a megosztáshoz. 
    - **CertificatePassword**  
    A jelszó, az összes létrehozott pfx-tanúsítvány fájlok egy biztonságos karakterláncot.
4. Várjon, amíg a titkos kulcsok elforgatása.  
Ha titkos Elforgatás sikeresen befejeződött, a konzol megjeleníti **összesített művelet állapota: sikeres**. 
5. Titkos Elforgatás sikeres befejezése után távolítsa el a tanúsítványok a előtti lépésében létrehozott megosztás, és a biztonsági mentési biztonságos helyen tárolja őket. 

## <a name="walkthrough-of-secret-rotation"></a>A titkos Forgatás forgatókönyv

A következő PowerShell-példa azt mutatja be, a parancsmagok és paraméterek futtatni kell a titkos kulcsok elforgatása.

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
## <a name="rotating-only-internal-secrets"></a>Csak belső titkok elforgatása

Csak Azure verem belső titkos kulcsok megváltoztatása:

1. Hozzon létre egy PowerShell-munkamenetet a [kiemelt végpont](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).
2. A privilegizált végpont-munkamenetben futtassa **Start-SecretRotation** argumentum nélkül.

## <a name="start-secretrotation-reference"></a>Start-SecretRotation referencia

A titkos kulcsok Azure verem rendszer forog. Csak végre az Azure-beli verem kiemelt végpont ellen.

### <a name="syntax"></a>Szintaxis

Elérési út (alapértelmezett)

```powershell
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential] <PSCredential> [-CertificatePassword <SecureString>]  
```

### <a name="description"></a>Leírás

A Start-SecretRotation parancsmag az infrastruktúra titkokat az Azure-verem rendszer forog. Az összes titkos kulcsot, a belső infrastruktúra-hálózathoz kapcsolódik forog alapértelmezés szerint a felhasználó által megadott azt is forog minden külső hálózati infrastruktúra végpontok tanúsítványait. Külső hálózati infrastruktúra végpontok cserélni, amikor Start-SecretRotation úgy kell végrehajtani az Invoke-Command parancsfájlblokk keresztül, az Azure-verem környezet kiemelt végpont munkamenet munkamenet paraméterként átadott.
 
### <a name="parameters"></a>Paraméterek

| Paraméter | Típus | Szükséges | Beosztás | Alapértelmezett | Leírás |
| -- | -- | -- | -- | -- | -- |
| PfxFilesPath | Karakterlánc  | False (Hamis)  | nevű  | None  | A fájlmegosztási elérési útját a **\Certificates** tartalmazó minden külső hálózati végpont tanúsítványokat. Csak akkor kötelező, ha a belső és külső kulcsok elforgatása. Záró könyvtárnak kell lennie **\Certificates**. |
| CertificatePassword | SecureString | False (Hamis)  | nevű  | None  | A jelszó - PfXFilesPath megadott tanúsítvány esetében. Ha PfxFilesPath áll rendelkezésre, ha a külső és belső titkok legyenek-e elforgatva szükséges érték. |
|

### <a name="examples"></a>Példák
 
**Csak a belső infrastruktúra titkok elforgatása**

```powershell  
PS C:\> Start-SecretRotation  
```

Ez a parancs forog összes az infrastruktúra titkos kulcsok Azure verem belső hálózathoz kapcsolódik. Start-SecretRotation forog verem által létrehozott összes titkos, de mivel nincs megadott tanúsítványok, külső végpont tanúsítványok nem forgatható.  

**Belső és külső infrastruktúra titkok elforgatása**
  
```powershell
PS C:\> Invoke-Command -session $PEPSession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword } 
Remove-PSSession -Session $PEPSession
```

Ez a parancs minden, az infrastruktúra titkos kulcsok Azure verem belső hálózathoz kapcsolódik, valamint a használt Azure verem külső hálózati infrastruktúra végpontok TLS-tanúsítványok forog. Start-SecretRotation forog összes verem által létrehozott titkos kulcsot, és tanúsítványokat kapnak, mert külső végpont tanúsítványokat is forgatható.  


## <a name="update-the-baseboard-management-controller-bmc-password"></a>Frissítés az alaplapi felügyeleti vezérlővel (BMC) jelszava

Az alaplapi felügyeleti vezérlővel (BMC) a kiszolgálók fizikai állapotát figyeli. A paramétereknek, valamint útmutatást a gazdagépben található bmc jelszófrissítés számítógépgyártó (OEM) hardvergyártójához függően változhat. A jelszavak Azure verem összetevők egy rendszeres ütemben történik, frissítenie kell.

1. Frissítse a bmc-hez, az Azure-verem fizikai kiszolgálókon az OEM utasításai szerint. A környezetben minden BMC jelszavát azonosnak kell lennie.
2. Nyissa meg a kiemelt végpont Azure verem munkamenetek. Utasítás, lásd: [használatával a privilegizált végpont Azure verem](azure-stack-privileged-endpoint.md).
3. Után a PowerShell parancssorba változott **[IP-cím vagy ERCS virtuális gép neve]: PS >** vagy **[azs-ercs01]: PS >** a környezet futtatása attól függően, hogy `Set-BmcPassword` futtatásával `invoke-command`. A privilegizált végpont munkamenet-változó továbbítsa paraméterként. Példa:

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
    
    A jelszavak kód vonalakként is használható a statikus PowerShell-verzió:
    
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

[További tudnivalók az Azure-verem biztonsági](azure-stack-security-foundations.md)
