---
title: 'Azure AD Connect: ADSync szolgáltatásfiók | Microsoft dokumentumok'
description: Ez a témakör az ADSync szolgáltatásfiókot ismerteti, és gyakorlati tanácsokat tartalmaz a fiókkal kapcsolatban.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 06/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f228da5afc5998d8fa59ce2d720cec4c9f955b67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67478718"
---
# <a name="adsync-service-account"></a>ADSync-szolgáltatásfiók
Az Azure AD Connect egy helyszíni szolgáltatást telepít, amely vezényli az Active Directory és az Azure Active Directory közötti szinkronizálást.  A Microsoft Azure AD Sync szinkronizálási szolgáltatás (ADSync) a helyszíni környezetben egy kiszolgálón fut.  A szolgáltatás hitelesítő adatai alapértelmezés szerint az Express telepítésekben vannak beállítva, de a szervezeti biztonsági követelményeknek megfelelően testreszabhatók.  Ezek a hitelesítő adatok nem használják a helyszíni erdőkhöz vagy az Azure Active Directoryhoz való csatlakozáshoz.

Az ADSync szolgáltatásfiók kiválasztása fontos tervezési döntés az Azure AD Connect telepítése előtt.  A hitelesítő adatok telepítés utáni módosítására tett minden kísérlet azt eredményezi, hogy a szolgáltatás nem indul el, elveszíti a hozzáférést a szinkronizálási adatbázishoz, és nem hitelesíti a csatlakoztatott könyvtárakat (Azure és AD DS).  Nem történik szinkronizálás, amíg az eredeti hitelesítő adatok vissza nem állnak.

## <a name="the-default-adsync-service-account"></a>Az alapértelmezett ADSync szolgáltatásfiók

Ha tagkiszolgálón fut, az AdSync szolgáltatás egy virtuális szolgáltatásfiók (VSA) környezetében fut.  Termékkorlát miatt a rendszer egyéni szolgáltatásfiókot hoz létre, ha tartományvezérlőre telepíti.  Ha az Express settings szolgáltatásfiók nem felel meg a szervezeti biztonsági követelményeknek, telepítse az Azure AD Connectet a Testreszabás lehetőség kiválasztásával.  Ezután válassza ki a szolgáltatásfiók lehetőséget, amely megfelel a szervezet követelményeinek.

>[!NOTE]
>A tartományvezérlőre telepített alapértelmezett szolgáltatásfiók a Tartomány\AAD_InstallationIdentifier.  A fiók jelszava véletlenszerűen jön létre, és jelentős kihívást jelent a helyreállítás és a jelszó elforgatása számára.  A Microsoft azt javasolja, hogy a szolgáltatásfiókot a tartományvezérlőn történő első telepítés során testre szabják önálló vagy csoport felügyelt szolgáltatásfiók (sMSA / gMSA) használatához.

|Az Azure AD Connect helye|Létrehozott szolgáltatásfiók|
|-----|-----|
|Tagkiszolgáló|Nt SZOLGÁLTATÁS\ADSync|
|Tartományvezérlő|Tartomány\AAD_74dc30c01e80 (lásd a megjegyzést)|

## <a name="custom-adsync-service-accounts"></a>Egyéni ADSync szolgáltatásfiókok
A Microsoft azt javasolja, hogy az ADSync szolgáltatást virtuális szolgáltatásfiók vagy önálló vagy csoportfelügyelt szolgáltatásfiók környezetében futassa.  A tartományi rendszergazda dönthet úgy is, hogy létrehoz egy szolgáltatási fiókot, amely megfelel az adott szervezeti biztonsági követelményeknek.   A telepítés során használt szolgáltatásfiók testreszabásához válassza az alábbi Gyorsbeállítások lap Testreszabás jelölőnégyzetét.   A következő beállítások érhetők el:

- alapértelmezett fiók – Az Azure AD Connect a fent leírt módon építi ki a szolgáltatási fiókot
- kezelt szolgáltatásfiók – a rendszergazda által kiépített önálló vagy csoportos MSA-t használja
- tartományi fiók – a rendszergazda által kiépített tartományszolgáltatás-fiók használata

![](media/concept-adsync-service-account/adsync1.png)

![](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>Az ADSync szolgáltatásfiók változásainak diagnosztizálása
Az ADSync szolgáltatás hitelesítő adatainak módosítása a telepítés után azt eredményezi, hogy a szolgáltatás nem indul el, elveszíti a hozzáférést a szinkronizálási adatbázishoz, és nem hitelesíti a csatlakoztatott könyvtárakat (Azure és AD DS).  Az új ADSync szolgáltatásfiók hoz való adatbázis-hozzáférés megadása nem elegendő a probléma helyreállításához. Nem történik szinkronizálás, amíg az eredeti hitelesítő adatok vissza nem állnak.

Az ADSync szolgáltatás hibaszintű üzenetet küld az eseménynaplónak, ha nem tud elindulni.  Az üzenet tartalma attól függően változik, hogy a beépített adatbázis (localdb) vagy a teljes SQL használatban van-e.  Az alábbi példák példákat mutatjuk be az eseménynapló-bejegyzésekre, amelyek jelen lehetnek.

### <a name="example-1"></a>1. példa

Az AdSync szolgáltatás titkosítási kulcsai nem találhatók, és újra létrehozták őket.  A probléma kijavításáig nem történik szinkronizálás.

A probléma elhárításával kapcsolatos hiba elhárítása A Microsoft Azure AD Sync titkosítási kulcsai elérhetetlenné válnak, ha az AdSync szolgáltatás bejelentkezési hitelesítő adatai megváltoznak.  Ha a hitelesítő adatok megváltoztak, a Szolgáltatások alkalmazás segítségével módosítsa a Bejelentkezési fiókot az eredetileg beállított értékre (pl. NT SERVICE\AdSync) és indítsa újra a szolgáltatást.  Ez azonnal visszaállítja az AdSync szolgáltatás helyes működését.

További információt a következő [cikkben](https://go.microsoft.com/fwlink/?linkid=2086764) talál.

### <a name="example-2"></a>2. példa

A szolgáltatás nem tudott elindulni, mert nem sikerült kapcsolatot létesíteni a helyi adatbázissal (localdb).

A probléma elhárításának elhárítása A Microsoft Azure AD Sync szolgáltatás elveszíti a helyi adatbázis-szolgáltató elérésére vonatkozó engedélyét, ha az AdSync szolgáltatás bejelentkezési hitelesítő adatai megváltoznak.  Ha a hitelesítő adatok megváltoztak, használja a Szolgáltatások alkalmazást, hogy módosítsa a Bejelentkezési fiókot az eredetileg beállított értékre (pl. NT SERVICE\AdSync) és indítsa újra a szolgáltatást.  Ez azonnal visszaállítja az AdSync szolgáltatás helyes működését.

További információt a következő [cikkben](https://go.microsoft.com/fwlink/?linkid=2086764) talál.

További részletek A szolgáltató a következő hibainformációt adta vissza:
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).