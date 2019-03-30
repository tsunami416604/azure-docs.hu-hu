---
title: 'Azure AD Connect: ADConnectivityTools PowerShell-referencia |} A Microsoft Docs'
description: Ebben a dokumentumban részletes információ a ADConnectivityTools.psm1 PowerShell-modult.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 10/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40b4be9aca5243b80151afac0ae221f0d44509c5
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58650513"
---
# <a name="azure-ad-connect--adconnectivitytools-powershell-reference"></a>Azure AD Connect:  ADConnectivityTools PowerShell-referencia

A következő dokumentáció arról nyújt a ADConnectivityTools.psm1 PowerShell-modult, amely tartalmazza az Azure AD Connecttel kapcsolatos referenciainformációk.

## <a name="confirm-dnsconnectivity"></a>Confirm-DnsConnectivity

### <a name="synopsis"></a>SZINOPSZIS

Észleli a helyi Dns-probléma.

### <a name="syntax"></a>SZINTAXIS

```
Confirm-DnsConnectivity [-Forest] <String> [-DCs] <Array> [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

Helyi Dns-kapcsolat teszteket futtat.
Az Active Directory-összekötő konfigurálásához felhasználónak rendelkeznie kell mindkét névfeloldás esetében az erdő he\she hasonlóan a tartományvezérlők, az erdőhöz tartozó, valamint csatlakozni próbál.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Confirm-DnsConnectivity -Forest "TEST.CONTOSO.COM" -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>EXAMPLE 2

```powershell
Confirm-DnsConnectivity -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-forest"></a>-Erdő

Adja meg az összehasonlítandó az erdő nevére.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-DCs

Adja meg az összehasonlítandó tartományvezérlők.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

A diagnosztikai eredményét adja vissza egy PSObject formájában.
Ezt az eszközt a manuális beavatkozás során nem szükséges.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információk: about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-forestexists"></a>Győződjön meg róla ForestExists

### <a name="synopsis"></a>SZINOPSZIS

Meghatározza, hogy létezik-e a megadott erdő.

### <a name="syntax"></a>SZINTAXIS

```
Confirm-ForestExists [-Forest] <String> [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

Lekérdezi egy erdő társított IP-cím DNS-kiszolgáló.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-forest"></a>-Erdő

Adja meg az összehasonlítandó az erdő nevére.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információk: about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-functionallevel"></a>Confirm-FunctionalLevel

### <a name="synopsis"></a>SZINOPSZIS

Ellenőrzi az AD-erdő működési szintje.

### <a name="syntax"></a>SZINTAXIS

#### <a name="samaccount"></a>SamAccount

```
Confirm-FunctionalLevel -Forest <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>ForestFQDN

```
Confirm-FunctionalLevel -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

Ellenőrzi, hogy az AD-erdő működési szintjét egy adott MinAdForestVersion (WindowsServer2003) nagyobb vagy azzal egyenlőnek.
(Tartomány\felhasználónév) fiókot és jelszót kell kérni.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Confirm-FunctionalLevel -Forest "test.contoso.com"
```

#### <a name="example-2"></a>EXAMPLE 2

```powershell
Confirm-FunctionalLevel -Forest "test.contoso.com" -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

#### <a name="example-3"></a>3. PÉLDA

```powershell
Confirm-FunctionalLevel -ForestFQDN $ForestFQDN -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-forest"></a>-Erdő

Cél-erdő.
Alapértelmezett értéke az erdőben az aktuálisan bejelentkezett felhasználó.

```yml
Type: String
Parameter Sets: SamAccount
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-forestfqdn"></a>-ForestFQDN

Célobjektum ForestFQDN.

```yml
Type: Forest
Parameter Sets: ForestFQDN
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

A függvény, amely a számítógép éppen bejelentkezett felhasználó hitelesítő adatait, nem pedig a felhasználó egyéni hitelesítő adatokat kérő fogja használni.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információk: about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-networkconnectivity"></a>Confirm-NetworkConnectivity

### <a name="synopsis"></a>SZINOPSZIS

Észleli a helyi hálózati problémák léptek fel.

### <a name="syntax"></a>SZINTAXIS

```
Confirm-NetworkConnectivity [-DCs] <Array> [-SkipDnsPort] [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

Helyi hálózati kapcsolati tesztek futtatja.

A helyi hálózati teszteket az AAD Connect képesnek kell lennie kommunikálni a névvel ellátott tartományvezérlők porton 53-as (DNS), a 88 (Kerberos) és a 389-(LDAP), a legtöbb szervezet futtassa a tartományvezérlők, DNS, ezért ez a vizsgálat jelenleg integrált.
53-as port kell hagyni, ha egy másik DNS-kiszolgáló lett megadva.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Confirm-NetworkConnectivity -SkipDnsPort -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>EXAMPLE 2

```powershell
Confirm-NetworkConnectivity -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM" -Verbose
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-dcs"></a>-DCs

Adja meg az összehasonlítandó tartományvezérlők.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipdnsport"></a>-SkipDnsPort

Ha a felhasználó nem használja az Active Directory-hely által biztosított DNS-szolgáltatások / bejelentkezési DC, majd he\she előfordulhat, hogy ki szeretné hagyni 53-as port ellenőrzése.
Felhasználó továbbra is tudja feloldani az _.ldap._tcp kell lennie. \<forestfqdn\> ahhoz, hogy az Active Directory-összekötő konfigurálása sikeres.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

A diagnosztikai eredményét adja vissza egy PSObject formájában.
Ezt az eszközt a manuális beavatkozás során nem szükséges.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információk: about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-targetsarereachable"></a>Confirm-TargetsAreReachable

### <a name="synopsis"></a>SZINOPSZIS

Azt határozza meg, ha a megadott erdő és a társított tartományvezérlők érhetők el.

### <a name="syntax"></a>SZINTAXIS

```
Confirm-TargetsAreReachable [-Forest] <String> [-DCs] <Array> [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

Futtatások "pingelje" (e számítógép segítségével érik el a célszámítógépet a hálózati és/vagy az interneten keresztül) tesztek

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM" -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>EXAMPLE 2

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-forest"></a>-Erdő

Adja meg az összehasonlítandó az erdő nevére.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-DCs

Adja meg az összehasonlítandó tartományvezérlők.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információk: about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-validdomains"></a>Confirm-ValidDomains

### <a name="synopsis"></a>SZINOPSZIS

Ellenőrizze a érhetők el, hogy a kapott erdő teljes Tartományneve tartományban

### <a name="syntax"></a>SZINTAXIS

#### <a name="samaccount"></a>SamAccount

```
Confirm-ValidDomains [-Forest <String>] [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>ForestFQDN

```
Confirm-ValidDomains -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

Ellenőrizze, hogy minden a tartománynak a kapott erdő teljes Tartományneve érhetők el a DomainGuid és DomainDN beolvasására tett kísérlet.
(Tartomány\felhasználónév) fiókot és jelszót kell kérni.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Confirm-ValidDomains -Forest "test.contoso.com" -Verbose
```

#### <a name="example-2"></a>EXAMPLE 2

```powershell
Confirm-ValidDomains -Forest "test.contoso.com" -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

#### <a name="example-3"></a>3. PÉLDA

```powershell
Confirm-ValidDomains -ForestFQDN $ForestFQDN -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-forest"></a>-Erdő

Cél-erdő.

```yml
Type: String
Parameter Sets: SamAccount
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-forestfqdn"></a>-ForestFQDN

Célobjektum ForestFQDN.

```yml
Type: Forest
Parameter Sets: ForestFQDN
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

A függvény, amely a számítógép éppen bejelentkezett felhasználó hitelesítő adatait, nem pedig a felhasználó egyéni hitelesítő adatokat kérő fogja használni.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információk: about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-validenterpriseadmincredentials"></a>Confirm-ValidEnterpriseAdminCredentials

### <a name="synopsis"></a>SZINOPSZIS

Ellenőrzi, ha a felhasználó rendelkezik-e a vállalati rendszergazdai hitelesítő adatokat.

### <a name="syntax"></a>SZINTAXIS

```
Confirm-ValidEnterpriseAdminCredentials [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

Ha a felhasználó rendelkezik vállalati rendszergazdai hitelesítő adatokat a megadott keres.
(Tartomány\felhasználónév) fiókot és jelszót kell kérni.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Confirm-ValidEnterpriseAdminCredentials -DomainName test.contoso.com -Verbose
```

#### <a name="example-2"></a>EXAMPLE 2

```powershell
Confirm-ValidEnterpriseAdminCredentials -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

A függvény, amely a számítógép éppen bejelentkezett felhasználó hitelesítő adatait, nem pedig a felhasználó egyéni hitelesítő adatokat kérő fogja használni.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információk: about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-domainfqdndata"></a>Get-DomainFQDNData

### <a name="synopsis"></a>SZINOPSZIS

Lekéri egy DomainFQDN kívül egy fiók és jelszó kombinációjával.

### <a name="syntax"></a>SZINTAXIS

```
Get-DomainFQDNData [[-DomainFQDNDataType] <String>] [-RunWithCurrentlyLoggedInUserCredentials]
 [-ReturnExceptionOnError] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

Próbálja ki a hitelesítő adatokat egy domainFQDN objektum lekérdezni.
Ha a domainFQDN érvényes, egy DomainFQDNName vagy RootDomainName adja vissza, a felhasználó által választott függően.
(Tartomány\felhasználónév) fiókot és jelszót kell kérni.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Get-DomainFQDNData -DomainFQDNDataType DomainFQDNName -Verbose
```

#### <a name="example-2"></a>EXAMPLE 2

```powershell
Get-DomainFQDNData -DomainFQDNDataType RootDomainName -RunWithCurrentlyLoggedInUserCredentials
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-domainfqdndatatype"></a>-DomainFQDNDataType

A kívánt típusú, a rendszer lekéri az adatokat.
Egyelőre csak "DomainFQDNName" vagy "RootDomainName".

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

A függvény, amely a számítógép éppen bejelentkezett felhasználó hitelesítő adatait, nem pedig a felhasználó egyéni hitelesítő adatokat kérő fogja használni.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnexceptiononerror"></a>-ReturnExceptionOnError

Start-NetworkConnectivityDiagnosisTools függvény által használt kiegészítő paraméterrel

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információk: about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-forestfqdn"></a>Get-ForestFQDN

### <a name="synopsis"></a>SZINOPSZIS

Lekéri egy ForestFQDN kívül egy fiók és jelszó kombinációjával.

### <a name="syntax"></a>SZINTAXIS

```
Get-ForestFQDN [-Forest] <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

Próbálja ki a megadott hitelesítő adatok egy ForestFQDN lekérdezni.
(Tartomány\felhasználónév) fiókot és jelszót kell kérni.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Get-ForestFQDN -Forest CONTOSO.MICROSOFT.COM -Verbose
```

#### <a name="example-2"></a>EXAMPLE 2

```powershell
Get-ForestFQDN -Forest CONTOSO.MICROSOFT.COM -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-forest"></a>-Erdő

Cél-erdő. Alapértelmezett értéke az aktuálisan bejelentkezett felhasználó tartományát.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

A függvény, amely a számítógép éppen bejelentkezett felhasználó hitelesítő adatait, nem pedig a felhasználó egyéni hitelesítő adatokat kérő fogja használni.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információk: about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

### <a name="synopsis"></a>SZINOPSZIS

Fő függvényt.

### <a name="syntax"></a>SZINTAXIS

```
Start-ConnectivityValidation [-Forest] <String> [-AutoCreateConnectorAccount] <Boolean> [[-UserName] <String>]
 [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

Futtatások AD hitelesítő adatok ellenőrzéséhez minden elérhető mechanizmusok érvényesek.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Start-ConnectivityValidation -Forest "test.contoso.com" -AutoCreateConnectorAccount $True -Verbose
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-forest"></a>-Erdő

Cél-erdő.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-autocreateconnectoraccount"></a>-AutoCreateConnectorAccount

Egyéni-telepítések: Azt a jelzőt, amely értéke $True, ha a felhasználó a "Létrehozás új AD-fiók" az AD-Erdőfiók ablak az aad Connect varázsló a választotta.
Ha a felhasználó kiválasztott "Használja meglévő AD-fiók" $False.
Express-telepítések: Ez a változó értéke $True Express-telepítések kell lennie.

```yml
Type: Boolean
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-username"></a>-UserName

A paraméter, amely előre tölti fel a felhasználónév mező, ha a felhasználói hitelesítő adatokat kérnek.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információk: about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="start-networkconnectivitydiagnosistools"></a>Start-NetworkConnectivityDiagnosisTools

### <a name="synopsis"></a>SZINOPSZIS

Hálózati kapcsolat a fő függvény azt teszteli.

### <a name="syntax"></a>SZINTAXIS

```
Start-NetworkConnectivityDiagnosisTools [[-Forest] <String>] [-Credentials] <PSCredential>
 [[-LogFileLocation] <String>] [[-DCs] <Array>] [-DisplayInformativeMessage] [-ReturnResultAsPSObject]
 [-ValidCredentials] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

Helyi hálózati kapcsolati tesztek futtatja.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Start-NetworkConnectivityDiagnosisTools -Forest "TEST.CONTOSO.COM"
```

#### <a name="example-2"></a>EXAMPLE 2

```powershell
Start-NetworkConnectivityDiagnosisTools -Forest "TEST.CONTOSO.COM" -DCs "DC1.TEST.CONTOSO.COM", "DC2.TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-forest"></a>-Erdő

Adja meg az összehasonlítandó erdő neve.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-credentials"></a>– Hitelesítő adatok

A felhasználónév és jelszó a tesztet futtató felhasználó.
Az azonos szintű engedélyeket, amelyek az Azure AD Connect varázsló futtatásához szükséges van szükség.

```yml
Type: PSCredential
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-logfilelocation"></a>-LogFileLocation

Adja meg a függvény kimenete tartalmazni fog egy naplófájl helye.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-DCs

Adja meg az összehasonlítandó tartományvezérlők.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-displayinformativemessage"></a>-DisplayInformativeMessage

Azt a jelzőt, amely lehetővé teszi, hogy ez a függvény céljáról szóló üzenet megjelenítése.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

A diagnosztikai eredményét adja vissza egy PSObject formájában.
Nem szükséges, adja meg ezt az eszközt a manuális beavatkozás során.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-validcredentials"></a>-ValidCredentials

Azt jelzi, ha a felhasználó által beírt hitelesítő adatok érvényesek.
Nem szükséges, adja meg ezt az eszközt a manuális beavatkozás során.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Ez a parancsmag a következő általános paramétereket támogatja: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction és -WarningVariable.
További információk: about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
