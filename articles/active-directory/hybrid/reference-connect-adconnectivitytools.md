---
title: 'Azure AD Connect: ADConnectivityTools PowerShell-hivatkozás | Microsoft dokumentumok'
description: Ez a dokumentum az ADConnectivityTools.psm1 PowerShell modul referenciainformációit tartalmazza.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6b90ff82601acca1249c7d8c353944e39e89f95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66473782"
---
# <a name="azure-ad-connect--adconnectivitytools-powershell-reference"></a>Azure AD Connect: AdconnectivityTools PowerShell-referencia

A következő dokumentáció az Azure AD Connect részét képező ADConnectivityTools.psm1 PowerShell-modul referenciainformációit tartalmazza.

## <a name="confirm-dnsconnectivity"></a>Megerősítés-Dnsconnectivity

### <a name="synopsis"></a>SZINOPSZIS

Észleli a helyi DNS-problémákat.

### <a name="syntax"></a>SZINTAXIS

```
Confirm-DnsConnectivity [-Forest] <String> [-DCs] <Array> [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

Helyi DNS-kapcsolati teszteket futtat.
Az Active Directory-összekötő konfigurálásához a felhasználónak mindkét névfeloldással rendelkeznie kell ahhoz az erdőhöz, amelyhez csatlakozni próbál, valamint az erdőhöz társított tartományvezérlőkön.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Confirm-DnsConnectivity -Forest "TEST.CONTOSO.COM" -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>2. PÉLDA

```powershell
Confirm-DnsConnectivity -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-forest"></a>-Erdő

Megadja annak az erdőnek a nevét, amelyen tesztelni kell.

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

Adja meg a tesztelni kívánt tartományvezérlőket.

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

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSobject objektum

Ennek a diagnózisnak az eredményét adja eredményül PSObject formájában.
Nem szükséges az eszközzel való kézi beavatkozás során.

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
További információ: about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="confirm-forestexists"></a>Confirm-ForestLétez

### <a name="synopsis"></a>SZINOPSZIS

Azt határozza meg, hogy létezik-e adott erdő.

### <a name="syntax"></a>SZINTAXIS

```
Confirm-ForestExists [-Forest] <String> [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

Lekérdezi a DNS-kiszolgálót az erdőhöz társított IP-címekről.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-forest"></a>-Erdő

Megadja annak az erdőnek a nevét, amelyen tesztelni kell.

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
További információ: about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="confirm-functionallevel"></a>Megerősítés-FunctionalLevel

### <a name="synopsis"></a>SZINOPSZIS

Az AD-erdő működési szintjének ellenőrzése.

### <a name="syntax"></a>SZINTAXIS

#### <a name="samaccount"></a>SamAccount

```
Confirm-FunctionalLevel -Forest <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>Erdőfqdn

```
Confirm-FunctionalLevel -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

Ellenőrzi, hogy az AD erdő működési szintje egyenlő vagy nagyobb-e, mint egy adott MinAdForestVersion (WindowsServer2003).
Fiók (Tartomány\Felhasználónév) és jelszó kérhető.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Confirm-FunctionalLevel -Forest "test.contoso.com"
```

#### <a name="example-2"></a>2. PÉLDA

```powershell
Confirm-FunctionalLevel -Forest "test.contoso.com" -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

#### <a name="example-3"></a>3. PÉLDA

```powershell
Confirm-FunctionalLevel -ForestFQDN $ForestFQDN -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-forest"></a>-Erdő

Célerdő.
Az alapértelmezett érték az aktuálisan bejelentkezett felhasználó erdője.

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

Cél ForestFQDN objektum.

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

A függvény a számítógépbe jelenleg bejelentkezett felhasználó hitelesítő adatait fogja használni, nem pedig egyéni hitelesítő adatokat kér a felhasználótól.

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
További információ: about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="confirm-networkconnectivity"></a>Megerősítés-hálózati kapcsolat

### <a name="synopsis"></a>SZINOPSZIS

A helyi hálózati kapcsolattal kapcsolatos problémák észlelése.

### <a name="syntax"></a>SZINTAXIS

```
Confirm-NetworkConnectivity [-DCs] <Array> [-SkipDnsPort] [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

Helyi hálózati kapcsolati teszteket futtat.

A helyi hálózati tesztekhez az AAD Connectnek képesnek kell lennie kommunikálni az 53 (DNS), 88 (Kerberos) és 389 (LDAP) portok on megnevezett tartományvezérlőivel, ezért van jelenleg integrálva ez a teszt.
Ha egy másik DNS-kiszolgáló t adott meg, az 53-as portot ki kell hagyni.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Confirm-NetworkConnectivity -SkipDnsPort -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>2. PÉLDA

```powershell
Confirm-NetworkConnectivity -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM" -Verbose
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-dcs"></a>-DCs

Adja meg a tesztelni kívánt tartományvezérlőket.

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

#### <a name="-skipdnsport"></a>-Skipdnsport

Ha a felhasználó nem használja az AD Site / Logon DC által biztosított DNS-szolgáltatásokat, akkor érdemes lehet kihagynia az 53-as port ellenőrzését.
A felhasználónak továbbra is képesnek kell lennie a _.ldap._tcp feloldására. \<forestfqdn-t\> az Active Directory-összekötő konfigurációjának sikeressítéséhez.

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

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSobject objektum

Ennek a diagnózisnak az eredményét adja eredményül PSObject formájában.
Nem szükséges az eszközzel való kézi beavatkozás során.

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
További információ: about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="confirm-targetsarereachable"></a>Confirm-TargetsÉrhető el

### <a name="synopsis"></a>SZINOPSZIS

Azt határozza meg, hogy egy adott erdő és a hozzá tartozó tartományvezérlők elérhetők-e.

### <a name="syntax"></a>SZINTAXIS

```
Confirm-TargetsAreReachable [-Forest] <String> [-DCs] <Array> [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

"Ping" teszteket futtat (függetlenül attól, hogy a számítógép el tudja-e érni a célszámítógépet a hálózaton és/vagy az interneten keresztül)

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM" -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>2. PÉLDA

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-forest"></a>-Erdő

Megadja annak az erdőnek a nevét, amelyen tesztelni kell.

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

Adja meg a tesztelni kívánt tartományvezérlőket.

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
További információ: about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="confirm-validdomains"></a>Megerősítés-validdomains

### <a name="synopsis"></a>SZINOPSZIS

Annak ellenőrzése, hogy a beszerzett erdő teljes tartományában lévő tartományok elérhetők-e

### <a name="syntax"></a>SZINTAXIS

#### <a name="samaccount"></a>SamAccount

```
Confirm-ValidDomains [-Forest <String>] [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>Erdőfqdn

```
Confirm-ValidDomains -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

Ellenőrizze, hogy a beszerzett erdő teljes tartományai elérhetők-e a DomainGuid és a DomainDN lekérésének megkísérlésével.
Fiók (Tartomány\Felhasználónév) és jelszó kérhető.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Confirm-ValidDomains -Forest "test.contoso.com" -Verbose
```

#### <a name="example-2"></a>2. PÉLDA

```powershell
Confirm-ValidDomains -Forest "test.contoso.com" -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

#### <a name="example-3"></a>3. PÉLDA

```powershell
Confirm-ValidDomains -ForestFQDN $ForestFQDN -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-forest"></a>-Erdő

Célerdő.

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

Cél ForestFQDN objektum.

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

A függvény a számítógépbe jelenleg bejelentkezett felhasználó hitelesítő adatait fogja használni, nem pedig egyéni hitelesítő adatokat kér a felhasználótól.

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
További információ: about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="confirm-validenterpriseadmincredentials"></a>Valid-ValidEnterpriseAdminCredentials megerősítése

### <a name="synopsis"></a>SZINOPSZIS

Ellenőrzi, hogy a felhasználó rendelkezik-e vállalati rendszergazdai hitelesítő adatokkal.

### <a name="syntax"></a>SZINTAXIS

```
Confirm-ValidEnterpriseAdminCredentials [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

Keresés, ha a megadott felhasználó rendelkezik vállalati rendszergazdai hitelesítő adatokkal.
Fiók (Tartomány\Felhasználónév) és jelszó kérhető.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Confirm-ValidEnterpriseAdminCredentials -DomainName test.contoso.com -Verbose
```

#### <a name="example-2"></a>2. PÉLDA

```powershell
Confirm-ValidEnterpriseAdminCredentials -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

A függvény a számítógépbe jelenleg bejelentkezett felhasználó hitelesítő adatait fogja használni, nem pedig egyéni hitelesítő adatokat kér a felhasználótól.

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
További információ: about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="get-domainfqdndata"></a>Get-DomainFQDNData

### <a name="synopsis"></a>SZINOPSZIS

A DomainFQDN lekérése fiók- és jelszókombinációból.

### <a name="syntax"></a>SZINTAXIS

```
Get-DomainFQDNData [[-DomainFQDNDataType] <String>] [-RunWithCurrentlyLoggedInUserCredentials]
 [-ReturnExceptionOnError] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

Kísérlet egy domainFQDN objektum beszerzésére a megadott hitelesítő adatokból.
Ha a domainFQDN érvényes, a felhasználó választásától függően a rendszer domainfqnname vagy rootdomainname értéket ad vissza.
Fiók (Tartomány\Felhasználónév) és jelszó kérhető.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Get-DomainFQDNData -DomainFQDNDataType DomainFQDNName -Verbose
```

#### <a name="example-2"></a>2. PÉLDA

```powershell
Get-DomainFQDNData -DomainFQDNDataType RootDomainName -RunWithCurrentlyLoggedInUserCredentials
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-domainfqdndatatype"></a>-DomainFQDNDataType

A beolvasni kívánt típusú adatok.
Jelenleg a "DomainFQDNName" vagy a "RootDomainName" volt a következő.

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

A függvény a számítógépbe jelenleg bejelentkezett felhasználó hitelesítő adatait fogja használni, nem pedig egyéni hitelesítő adatokat kér a felhasználótól.

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

A Start-NetworkConnectivityDiagnosisTools függvény által használt kiegészítő paraméter

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
További információ: about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="get-forestfqdn"></a>Get-ForestFQDN

### <a name="synopsis"></a>SZINOPSZIS

ForestFQDN-t kér le egy fiók- és jelszókombinációból.

### <a name="syntax"></a>SZINTAXIS

```
Get-ForestFQDN [-Forest] <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

Megkísérli a ForestFQDN beszerzését a megadott hitelesítő adatokból.
Fiók (Tartomány\Felhasználónév) és jelszó kérhető.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Get-ForestFQDN -Forest CONTOSO.MICROSOFT.COM -Verbose
```

#### <a name="example-2"></a>2. PÉLDA

```powershell
Get-ForestFQDN -Forest CONTOSO.MICROSOFT.COM -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-forest"></a>-Erdő

Célerdő. Az alapértelmezett érték az aktuálisan bejelentkezett felhasználó tartománya.

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

A függvény a számítógépbe jelenleg bejelentkezett felhasználó hitelesítő adatait fogja használni, nem pedig egyéni hitelesítő adatokat kér a felhasználótól.

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
További információ: about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

### <a name="synopsis"></a>SZINOPSZIS

Fő funkció.

### <a name="syntax"></a>SZINTAXIS

```
Start-ConnectivityValidation [-Forest] <String> [-AutoCreateConnectorAccount] <Boolean> [[-UserName] <String>]
 [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

Futtatja az összes rendelkezésre álló mechanizmust, amely ellenőrzi, hogy az AD-hitelesítő adatok érvényesek-e.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Start-ConnectivityValidation -Forest "test.contoso.com" -AutoCreateConnectorAccount $True -Verbose
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-forest"></a>-Erdő

Célerdő.

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

Egyéni telepítés esetén: A $True jelző, ha a felhasználó az AADConnect varázslójának AD erdőfiók ablakában az "Új AD-fiók létrehozása" lehetőséget választotta.
$False, ha a felhasználó a "Meglévő AD-fiók használata" lehetőséget választotta.
Expressz-telepítések esetén: Ennek a változónak az értékét $True kell lennie az Express-telepítések esetében.

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

Az a paraméter, amely a felhasználó hitelesítő adatainak kérésekor előre feltölti a Felhasználónév mezőt.

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
További információ: about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .

## <a name="start-networkconnectivitydiagnosistools"></a>Start-NetworkConnectivityDiagnosisTools

### <a name="synopsis"></a>SZINOPSZIS

A hálózati kapcsolati tesztek fő funkciója.

### <a name="syntax"></a>SZINTAXIS

```
Start-NetworkConnectivityDiagnosisTools [[-Forest] <String>] [-Credentials] <PSCredential>
 [[-LogFileLocation] <String>] [[-DCs] <Array>] [-DisplayInformativeMessage] [-ReturnResultAsPSObject]
 [-ValidCredentials] [<CommonParameters>]
```

### <a name="description"></a>LEÍRÁS

Helyi hálózati kapcsolati teszteket futtat.

### <a name="examples"></a>PÉLDÁK

#### <a name="example-1"></a>1. PÉLDA

```powershell
Start-NetworkConnectivityDiagnosisTools -Forest "TEST.CONTOSO.COM"
```

#### <a name="example-2"></a>2. PÉLDA

```powershell
Start-NetworkConnectivityDiagnosisTools -Forest "TEST.CONTOSO.COM" -DCs "DC1.TEST.CONTOSO.COM", "DC2.TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMÉTEREK

#### <a name="-forest"></a>-Erdő

Megadja a tesztelni a tesztet.

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

#### <a name="-credentials"></a>-Hitelesítő adatok

A tesztet futtató felhasználó felhasználóneve és jelszava.
Az Azure AD Connect varázsló futtatásához ugyanolyan szintű engedélyekre van szükség.

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

Megadja a függvény kimenetét tartalmazó naplófájl helyét.

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

Adja meg a tesztelni kívánt tartományvezérlőket.

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

A függvény céljáról szóló üzenet megjelenítését lehetővé tévő megjelölés.

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

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSobject objektum

Ennek a diagnózisnak az eredményét adja eredményül PSObject formájában.
Nem szükséges megadni az eszközzel való kézi interakció során.

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

Azt jelzi, hogy a felhasználó által megadott hitelesítő adatok érvényesek-e.
Nem szükséges megadni az eszközzel való kézi interakció során.

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
További információ: about_CommonParametershttps://go.microsoft.com/fwlink/?LinkID=113216)( .
