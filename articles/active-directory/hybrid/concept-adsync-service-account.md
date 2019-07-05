---
title: 'Azure AD Connect: ADSync szolgáltatás fiókja |} A Microsoft Docs'
description: Ez a témakör ismerteti az ADSync szolgáltatás fiókja, és gyakorlati tanácsokat a fiókjával kapcsolatban.
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
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478718"
---
# <a name="adsync-service-account"></a>ADSync szolgáltatás fiókja
Az Azure AD Connect telepít egy a helyszíni szolgáltatással, amelyre koordinálja a szinkronizálás az Active Directory és az Azure Active Directory között.  A Microsoft Azure AD Sync szinkronizálási szolgáltatás (ADSync) fut a kiszolgálón, a helyszíni környezetben.  A szolgáltatás hitelesítő adatai az Expressz telepítés esetén alapértelmezés szerint vannak beállítva, de egyénileg beállítható a szervezeti biztonsági követelmények teljesítéséhez.  Ezeket a hitelesítő adatokat nem használja a helyszíni erdők vagy Azure Active Directoryban való csatlakozáshoz.

Kiválasztása az ADSync szolgáltatás fiók egy fontos tervezési döntés, az Azure AD Connect telepítése előtt.  Bármely próbálja meg a hitelesítő adatok módosítása után a telepítés indul el, hogy a szolgáltatás nem eredményez a szinkronizálási adatbázishoz való hozzáférés elvesztése, és a csatlakoztatott címtárak (az Azure és az AD DS) hitelesítése sikertelen.  Szinkronizálás nem fog előfordulni, amíg nem állítja vissza az eredeti hitelesítő.

## <a name="the-default-adsync-service-account"></a>Az alapértelmezett ADSync szolgáltatás fiók

Ha futtatja azon a tagkiszolgálón, egy virtuális szolgáltatás fiókja (Szállítóspecifikus) környezetében fut, az AdSync szolgáltatást.  Termékkel kapcsolatos korlátozás miatt egyéni szolgáltatásfiókot a tartományvezérlőn telepítésekor jön létre.  Ha a fiók Express beállítások nem felel meg a szervezeti biztonsági követelmények, üzembe helyezése az Azure AD Connect a Testreszabás beállítás kiválasztásával.  Ezután válassza ki a szolgáltatás fiók lehetőséget, amely megfelel a szervezet követelményeinek.

>[!NOTE]
>Az alapértelmezett szolgáltatásfiók tartományvezérlőn telepítve van az űrlap Domain\AAD_InstallationIdentifier.  Ez a fiók jelszavát egy véletlenszerűen generált, és a helyreállítási és a jelszó Elforgatás jelentős kihívásokat jelent.  A Microsoft javasolja, hogy egy tartományvezérlőn vagy egy önálló vagy csoportos felügyelt szolgáltatásfiók kezdeti telepítése során a szolgáltatás fiók testreszabása (önállóan felügyelt szolgáltatásfiókot vagy csoportosan felügyelt szolgáltatásfiók)

|Az Azure AD Connect helye|A szolgáltatásfiók létrehozása|
|-----|-----|
|Tagkiszolgáló|NT SERVICE\ADSync|
|Tartományvezérlő|Domain\AAD_74dc30c01e80 (see note)|

## <a name="custom-adsync-service-accounts"></a>Egyéni ADSync szolgáltatás fiókok
A Microsoft javasolja az ADSync futó keretén belül egy virtuális szolgáltatásfiókot vagy egy különálló szolgáltatás, vagy csoportosan felügyelt szolgáltatásfiók.  A tartományi rendszergazda is lehetőség, hogy hozzon létre egy szolgáltatás-fiókot létrehozni, hogy az adott szervezeti biztonsági követelményeknek.   Testre szabhatja a telepítés során használt szolgáltatásfiók, válassza a Gyorsbeállítások oldalon testreszabás lehetőséget.   A következő lehetőségek közül választhat:

- alapértelmezett fiók – az Azure AD Connect kiépíti a fent leírt szolgáltatásfiók
- felügyelt szolgáltatásfiók – önálló vagy csoportos msa-t a rendszergazda által kiosztott
- tartományi fiók – a rendszergazda által üzembe helyezett egy tartomány szolgáltatásfiók használata

![](media/concept-adsync-service-account/adsync1.png)

![](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>Diagnosztizálás ADSync szolgáltatás fiókmódosítások
Az ADSync szolgáltatás hitelesítő adatai követően a telepítés indul el, hogy a szolgáltatás nem eredményez a szinkronizálási adatbázishoz való hozzáférés elvesztése, és a módosítása való (az Azure és az AD DS) a csatlakoztatott címtárakkal való hitelesítése sikertelen volt.  Az ADSync új szolgáltatásfiók való hozzáférést adatbázis nem elegendő a probléma megoldásához. Szinkronizálás nem fog előfordulni, amíg nem állítja vissza az eredeti hitelesítő.

Az ADSync szolgáltatást fog kiadni az Eseménynapló-szolgáltatói hibaüzenet, ha nem tudja elindítani.  Az üzenet tartalmától függ attól függően, hogy a beépített adatbázissal (localdb) vagy a teljes funkciókészletű SQL az használatban van.  A következő példák az eseménynapló-bejegyzéseket, amelyek jelen lehet.

### <a name="example-1"></a>1\. példa

Az AdSync szolgáltatás titkosítási kulcsok nem található, és újból létrejött.  Szinkronizálási probléma kijavításáig nem történik.

Ez a probléma a Microsoft Azure AD Sync hibaelhárítási titkosítási kulcsok elérhetetlenné válik, ha a AdSync szolgáltatás bejelentkezési hitelesítő adatok módosításakor.  Ha a hitelesítő adatok megváltoztak, a szolgáltatások alkalmazás használatával állítsa vissza a bejelentkezési fiókja az eredetileg megadott értékét (például) NT SERVICE\AdSync), és indítsa újra a szolgáltatást.  A művelettel visszaállítja a megfelelő műveletet az AdSync szolgáltatás azonnal.

Tekintse át a következő [cikk](https://go.microsoft.com/fwlink/?linkid=2086764) találhat további információt.

### <a name="example-2"></a>2\. példa

A szolgáltatás nem tudta elindítani, mert nem sikerült létrehozni a helyi adatbázis (localdb) kapcsolatot.

Ez a probléma a Microsoft Azure AD Sync szolgáltatás hibaelhárítása elveszíti a hozzáféréssel a helyi adatbázis-szolgáltató, ha az AdSync szolgáltatás bejelentkezési hitelesítő adatok megváltoznak.  Ha a hitelesítő adatok megváltoztak a szolgáltatások alkalmazás használatával állítsa vissza a bejelentkezési fiókja az eredetileg megadott értékét (például) NT SERVICE\AdSync), és indítsa újra a szolgáltatást.  A művelettel visszaállítja a megfelelő műveletet az AdSync szolgáltatás azonnal.

Tekintse át a következő [cikk](https://go.microsoft.com/fwlink/?linkid=2086764) találhat további információt.

További részletek a szolgáltató által visszaadott hiba a következőket:
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).