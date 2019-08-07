---
title: Hibaelhárítás az Azure AD jelszavas védelmében – Azure Active Directory
description: Az Azure AD jelszavas védelem gyakori hibaelhárítása
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07c035f4823ea8c8eaa96ca9bda22450246811cd
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779627"
---
# <a name="azure-ad-password-protection-troubleshooting"></a>Azure AD jelszavas védelem – hibaelhárítás

Az Azure AD jelszavas védelem üzembe helyezése után szükség lehet a hibaelhárításra. Ez a cikk részletesen ismerteti a gyakori hibaelhárítási lépéseket.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>A tartományvezérlő ügynöke nem talál proxyt a címtárban.

Ennek a problémának a fő tünete a DC-ügynök rendszergazdai eseménynaplójának 30017 eseménye.

A probléma leggyakoribb oka, hogy a proxy még nincs regisztrálva. Ha egy proxy regisztrálva van, előfordulhat, hogy az AD-replikáció késése miatt némi késés történt, amíg egy adott tartományvezérlő ügynöke meg nem látja a proxyt.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>A DC-ügynök nem tud kommunikálni a proxyval

Ennek a problémának a fő tünete a DC-ügynök rendszergazdai eseménynaplójának 30018 eseménye. A probléma több lehetséges oka lehet:

1. A DC-ügynök a hálózat egy elkülönített részében található, amely nem engedélyezi a hálózati kapcsolatot a regisztrált proxy (ka) t. Ez a probléma akkor lehet jóindulatú, ha más TARTOMÁNYVEZÉRLŐk nem tudnak kommunikálni a proxy (k) vel az Azure-beli jelszóházirendek letöltése érdekében. A letöltés után ezeket a házirendeket a rendszer ezután az elkülönített tartományvezérlő szerzi be a SYSVOL-megosztásban lévő házirend-fájlok replikálásával.

1. A proxykiszolgáló blokkolja az RPC Endpoint Mapper-végpont elérését (135-es port).

   Az Azure AD jelszavas védelem proxy telepítője automatikusan létrehoz egy Windows tűzfal bejövő szabályt, amely engedélyezi a hozzáférést az 135-es porthoz. Ha ezt a szabályt később törli vagy letiltja, a DC-ügynökök nem tudnak kommunikálni a proxy szolgáltatással. Ha a beépített Windows tűzfal egy másik tűzfal-termék helyett le van tiltva, akkor a tűzfalat úgy kell konfigurálnia, hogy engedélyezze a hozzáférést a 135-es porthoz.

1. A proxykiszolgáló blokkolja a proxy szolgáltatás által figyelt RPC-végponthoz (dinamikus vagy statikus) való hozzáférést.

   Az Azure AD jelszavas védelmi proxy telepítője automatikusan létrehoz egy Windows tűzfal bejövő szabályt, amely lehetővé teszi az Azure AD jelszavas védelmi proxy szolgáltatás által figyelt bejövő portok elérését. Ha ezt a szabályt később törli vagy letiltja, a DC-ügynökök nem tudnak kommunikálni a proxy szolgáltatással. Ha a beépített Windows tűzfal egy másik tűzfal-termék helyett le van tiltva, akkor a tűzfalat úgy kell beállítania, hogy engedélyezze a hozzáférést az Azure AD jelszavas védelmi proxy szolgáltatás által figyelt bejövő portokhoz. Ez a konfiguráció konkrétabb lehet, ha a proxy szolgáltatás úgy van konfigurálva, hogy egy adott statikus RPC-portot figyeljen (a `Set-AzureADPasswordProtectionProxyConfiguration` parancsmag használatával).

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>A proxy szolgáltatás nem tud kommunikálni az Azure-ban

1. Győződjön meg arról, hogy a proxykiszolgáló kapcsolódik a [központi telepítési követelményekben](howto-password-ban-bad-on-premises-deploy.md)felsorolt végpontokhoz.

1. Győződjön meg arról, hogy az erdő és az összes proxykiszolgáló regisztrálva van ugyanazon az Azure-bérlőn.

   Ezt a követelményt a és `Get-AzureADPasswordProtectionProxy` `Get-AzureADPasswordProtectionDCAgent` a PowerShell-parancsmagok futtatásával, majd az `AzureTenant` egyes visszaadott elemek tulajdonságának összehasonlításával is megtekintheti. A megfelelő működés érdekében a jelentett bérlő nevének meg kell egyeznie az összes tartományvezérlő-ügynök és proxykiszolgáló között.

   Ha egy Azure-bérlő regisztrációjának eltérési feltétele létezik, ezt a `Register-AzureADPasswordProtectionProxy` problémát a és/vagy `Register-AzureADPasswordProtectionForest` a PowerShell-parancsmagok igény szerint történő futtatásával lehet megállapítani, hogy az összes regisztrációhoz ugyanazt az Azure-bérlő hitelesítő adatait használja.

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>A tartományvezérlő ügynöke nem tudja titkosítani vagy visszafejteni a jelszóházirend fájljait

Ez a probléma számos tünettel rendelkezhet, de általában gyakori a kiváltó oka.

Az Azure AD jelszavas védelme kritikus függőséggel rendelkezik a Microsoft Key Distribution szolgáltatás által biztosított titkosítási és visszafejtési funkcióktól, amely a Windows Server 2012-es és újabb rendszerű tartományvezérlőkön érhető el. A KDS szolgáltatásnak engedélyezve és működőképesnek kell lennie minden Windows Server 2012-es és újabb tartományvezérlőn egy tartományban.

Alapértelmezés szerint a KDS szolgáltatás szolgáltatás indítási módja manuálisan (trigger Start) van konfigurálva. Ez a konfiguráció azt jelenti, hogy az ügyfél első alkalommal próbálja meg használni a szolgáltatást, igény szerint elindítva. Ez az alapértelmezett szolgáltatás indítási módja elfogadható az Azure AD jelszavas védelem működéséhez.

Ha a KDS szolgáltatás indítási módja le van tiltva, akkor ezt a konfigurációt meg kell oldani az Azure AD jelszavas védelem megfelelő működéséhez.

A probléma egyszerű tesztelése a KDS szolgáltatás manuális elindítása a Service Management MMC konzolon vagy más felügyeleti eszközök használatával (például "net start kdssvc" futtatása egy parancssori konzolról). A KDS szolgáltatás várhatóan sikeresen elindul és fut.

A KDS szolgáltatás nem indítható el a leggyakoribb alapvető oka az, hogy az Active Directory tartományvezérlő objektum az alapértelmezett tartományvezérlők szervezeti egységén kívül található. Ezt a konfigurációt a KDS szolgáltatás nem támogatja, és az Azure AD jelszavas védelme nem kényszeríti. Ennek a feltételnek a kijavítása a tartományvezérlő objektum áthelyezése egy helyre az alapértelmezett tartományvezérlők szervezeti egységben.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>A rendszer nem fogadja el a gyenge jelszavakat, de nem feltétlenül kell

A probléma több oka is lehet.

1. A tartományvezérlő ügynöke a nyilvános előzetes verziót futtatja, amely lejárt. [A nyilvános előzetes VERZIÓJÚ DC-ügynök szoftvere lejárt](howto-password-ban-bad-on-premises-troubleshoot.md#public-preview-dc-agent-software-has-expired).

1. A tartományvezérlő ügynöke nem tud letölteni egy házirendet, vagy nem tudja visszafejteni a meglévő szabályzatokat. A fenti témakörökben a lehetséges okokat érdemes megkeresni.

1. A jelszóházirend kényszerített módja továbbra is naplózásra van beállítva. Ha ez a konfiguráció van érvényben, konfigurálja újra úgy, hogy az az Azure AD jelszavas védelmi portál használatával érvénybe lépjen. Lásd a [jelszavas védelem engedélyezése](howto-password-ban-bad-on-premises-operations.md#enable-password-protection)című témakört.

1. A jelszó-házirend le van tiltva. Ha ez a konfiguráció van érvényben, konfigurálja újra a beállítást az Azure AD jelszavas védelem portál használatával. Lásd a [jelszavas védelem engedélyezése](howto-password-ban-bad-on-premises-operations.md#enable-password-protection)című témakört.

1. Nem telepítette a tartományvezérlő-ügynök szoftverét a tartomány összes tartományvezérlőjén. Ebben az esetben nehéz biztosítani, hogy a távoli Windows-ügyfelek egy adott tartományvezérlőt célozzanak meg a jelszó-módosítási művelet során. Ha úgy gondolja, hogy sikeresen megcélozta azt a TARTOMÁNYVEZÉRLŐt, ahol a DC-ügynök szoftvere telepítve van, akkor a tartományvezérlő ügynök rendszergazdai eseménynaplójának dupla ellenőrzésével ellenőrizheti: az eredménytől függetlenül legalább egy esemény lesz a jelszó eredményének dokumentálása. érvényesítés. Ha nincs olyan esemény a felhasználó számára, akinek a jelszava megváltozik, a jelszó módosítását valószínűleg egy másik tartományvezérlő dolgozza fel.

   Alternatív tesztként próbálja meg setting\changing a jelszavakat, miközben közvetlenül egy olyan TARTOMÁNYVEZÉRLŐre jelentkezett be, amelyen telepítve van a DC Agent szoftver. Ez a technika éles Active Directory-tartományokhoz nem ajánlott.

   A DC-ügynök szoftverének növekményes üzembe helyezése a korlátozások hatálya alá esik, a Microsoft határozottan azt javasolja, hogy a tartományvezérlő ügynök szoftverét a lehető leghamarabb telepítse a tartomány összes tartományvezérlőjén.

1. Előfordulhat, hogy a jelszó-ellenőrzési algoritmus ténylegesen a várt módon működik. Tekintse meg [, hogyan történik a jelszavak](concept-password-ban-bad.md#how-are-passwords-evaluated)kiértékelése.

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>Az Ntdsutil. exe nem tudja beállítani a gyenge címtárszolgáltatások helyreállító módjának jelszavát

A Active Directory mindig érvényesíti az új címtárszolgáltatások helyreállító módjának jelszavát, hogy az megfeleljen a tartomány jelszó-bonyolultsági követelményeinek. Ez az ellenőrzés a jelszó-szűrési DLL-eket, például az Azure AD jelszavas védelmet is kéri. Ha a rendszer visszautasítja az új címtárszolgáltatások helyreállító módjának jelszavát, a következő hibaüzenet jelenik meg:

```text
C:\>ntdsutil.exe
ntdsutil: set dsrm password
Reset DSRM Administrator Password: reset password on server null
Please type password for DS Restore Mode Administrator Account: ********
Please confirm new password: ********
Setting password failed.
        WIN32 Error Code: 0xa91
        Error Message: Password doesn't meet the requirements of the filter dll's
```

Ha az Azure AD jelszavas védelem egy Active Directory címtárszolgáltatások helyreállító módjához tartozó jelszó-ellenőrzési esemény (eke) t naplóz, akkor az Eseménynapló üzenetei nem tartalmazzák a felhasználónevet. Ez a viselkedés azért fordul elő, mert a Címtárszolgáltatások helyreállító fiókja olyan helyi fiók, amely nem része a tényleges Active Directory tartománynak.  

## <a name="domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password"></a>A tartományvezérlő replikájának előléptetése sikertelen, mert gyenge címtárszolgáltatások helyreállító jelszava

A tartományvezérlő-előléptetési folyamat során az új címtárszolgáltatások helyreállító módjának jelszava a tartomány egy meglévő TARTOMÁNYVEZÉRLŐje számára lesz elküldve érvényesítésre. Ha a rendszer visszautasítja az új címtárszolgáltatások helyreállító módjának jelszavát, a következő hibaüzenet jelenik meg:

```powershell
Install-ADDSDomainController : Verification of prerequisites for Domain Controller promotion failed. The Directory Services Restore Mode password does not meet a requirement of the password filter(s). Supply a suitable password.
```

A fenti probléma megoldásához hasonlóan az Azure AD jelszavas védelemhez tartozó jelszó-ellenőrzés eredményének eseménye üres felhasználóneveket fog tartalmazni ehhez a forgatókönyvhöz.

## <a name="domain-controller-demotion-fails-due-to-a-weak-local-administrator-password"></a>A tartományvezérlő lefokozása nem sikerült, mert gyenge a helyi rendszergazda jelszava

Ez a tartományvezérlő lefokozását támogatja, amely még mindig futtatja a tartományvezérlő-ügynök szoftverét. A rendszergazdáknak tisztában kell lenniük azzal, hogy a tartományvezérlő ügynöke továbbra is érvényesíti a jelenlegi jelszóházirend-házirendet a lefokozási eljárás során. A rendszer az új helyi rendszergazdai fiók jelszavát (a lefokozási művelet részeként adja meg) érvényesíti, mint bármely más jelszó. A Microsoft azt javasolja, hogy a tartományvezérlő lefokozási eljárásának részeként a helyi rendszergazdai fiókok számára a biztonságos jelszavakat kell kiválasztani.

Ha a lefokozás sikeres volt, és a tartományvezérlőt újraindították, és a rendszer újraindul, és ismét normál tagkiszolgálóként fut, a tartományvezérlői ügynök szoftvere visszavált a passzív módban történő futtatásra. Ezt követően bármikor el lehet távolítani.

## <a name="booting-into-directory-services-repair-mode"></a>A Címtárszolgáltatások helyreállító módjának indítása

Ha a tartományvezérlő címtárszolgáltatás-javító módban van elindítva, a tartományvezérlői ügynök jelszavas szűrője dll észleli ezt a feltételt, és az összes jelszó-ellenőrzési vagy kényszerítési tevékenység le lesz tiltva, az aktuálisan aktív házirendtől függetlenül. Configuration. A DC Agent jelszavas szűrő DLL-je egy 10023-es figyelmeztetési eseményt naplóz a felügyeleti eseménynaplóba, például:

```text
The password filter dll is loaded but the machine appears to be a domain controller that has been booted into Directory Services Repair Mode. All password change and set requests will be automatically approved. No further messages will be logged until after the next reboot.
```
## <a name="public-preview-dc-agent-software-has-expired"></a>A nyilvános előzetes verziójú DC-ügynök szoftvere lejárt

Az Azure AD jelszavas védelem nyilvános előzetes verziójában a DC-ügynök szoftverét nem lehetett a következő dátumokon leállítani a jelszó-ellenőrzési kérelmek feldolgozásának leállítására:

* A 1.2.65.0 verziója leállítja a jelszó-ellenőrzési kérelmek feldolgozását szeptember 1 2019-én.
* A 1.2.25.0 verzió és a korábban leállított jelszó-ellenőrzési kérések feldolgozása július 1 2019-én.

A határidő megközelítése szerint az összes időkorlátos DC-ügynök verziója 10021 eseményt bocsát ki a DC-ügynök rendszergazdai eseménynaplójában a következőhöz hasonló rendszerindítási időpontban:

```text
The password filter dll has successfully loaded and initialized.

The allowable trial period is nearing expiration. Once the trial period has expired, the password filter dll will no longer process passwords. Please contact Microsoft for an newer supported version of the software.

Expiration date:  9/01/2019 0:00:00 AM

This message will not be repeated until the next reboot.
```

A határidő lejárta után az összes időkorlátos DC-ügynök verziója 10022 eseményt bocsát ki a DC-ügynök rendszergazdai eseménynaplójában a következőhöz hasonló rendszerindítási időpontban:

```text
The password filter dll is loaded but the allowable trial period has expired. All password change and set requests will be automatically approved. Please contact Microsoft for a newer supported version of the software.

No further messages will be logged until after the next reboot.
```

Mivel a határidőt csak a kezdeti rendszerindítás során ellenőrzi a rendszer, előfordulhat, hogy a naptári határidő lejárta után nem látja ezeket az eseményeket. A határidő felismerése után a rendszer nem gyakorol negatív hatást a tartományvezérlőre vagy a nagyobb környezetre, mint az összes jelszó, automatikusan jóvá lesz hagyva.

> [!IMPORTANT]
> A Microsoft azt javasolja, hogy a lejárt nyilvános előzetes tartományvezérlő ügynökök azonnal frissítve legyenek a legújabb verzióra.

A parancsmag futtatásával egyszerűen felderítheti az olyan tartományvezérlői ügynököket, amelyeket frissíteni kell, `Get-AzureADPasswordProtectionDCAgent` például a következő módon:

```powershell
PS C:\> Get-AzureADPasswordProtectionDCAgent

ServerFQDN            : bpl1.bpl.com
SoftwareVersion       : 1.2.125.0
Domain                : bpl.com
Forest                : bpl.com
PasswordPolicyDateUTC : 8/1/2019 9:18:05 PM
HeartbeatUTC          : 8/1/2019 10:00:00 PM
AzureTenant           : bpltest.onmicrosoft.com
```

Ebben a témakörben a SoftwareVersion mező nyilvánvalóan a legfontosabb tulajdonság, amellyel megtekintheti. A PowerShell-szűrés használatával kiszűrheti az olyan egyenáramú ügynököket, amelyek már megtalálhatók a szükséges alapkonfiguráció-verziónál, például:

```powershell
PS C:\> $LatestAzureADPasswordProtectionVersion = "1.2.125.0"
PS C:\> Get-AzureADPasswordProtectionDCAgent | Where-Object {$_.SoftwareVersion -lt $LatestAzureADPasswordProtectionVersion}
```

Az Azure AD jelszavas védelmi proxy szoftver semmilyen verzióban nem korlátozott. A Microsoft továbbra is javasolja a DC és a proxy ügynökök frissítését a legújabb verzióra, amint azok megjelentek. A `Get-AzureADPasswordProtectionProxy` parancsmagot a DC-ügynököknél a fenti példában szereplő, frissítést igénylő proxy ügynökök keresésére lehet használni.

Az adott frissítési eljárásokkal kapcsolatos további részletekért tekintse meg a [tartományvezérlő-ügynök frissítését](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-dc-agent) és [a proxy ügynök frissítését](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-proxy-agent) ismertető témakört.

## <a name="emergency-remediation"></a>Vészhelyzeti szervizelés

Ha olyan helyzet történik, ahol a tartományvezérlő ügynöke problémát okoz, előfordulhat, hogy a tartományvezérlő ügynök szolgáltatása azonnal leáll. A DC Agent jelszó-szűrő DLL-fájlja továbbra is megkísérli hívni a nem futó szolgáltatást, és naplózza a figyelmeztetési eseményeket (10012, 10013), de az összes bejövő jelszót elfogadja a rendszer. A DC Agent szolgáltatás ezután a Windows szolgáltatásvezérlő kezelőjén keresztül is konfigurálható a "Letiltva" indítási típussal, igény szerint.

Egy másik szervizelési mérték az engedélyezés mód beállítása a nem értékre az Azure AD jelszavas védelem portálon. Miután letöltötte a frissített szabályzatot, minden egyes tartományvezérlő ügynök-szolgáltatás nyugalmi módba kerül, ahol az összes jelszó elfogadva van. További információ: érvényesítési [mód](howto-password-ban-bad-on-premises-operations.md#enforce-mode).

## <a name="removal"></a>Eltávolítás

Ha úgy döntött, hogy eltávolítja az Azure AD jelszavas védelem szoftverét, és az összes kapcsolódó állapotot a tartomány (ok) és az erdő alapján végzi el, ez a feladat a következő lépések végrehajtásával valósítható meg:

> [!IMPORTANT]
> Fontos, hogy ezeket a lépéseket sorrendben hajtsa végre. Ha a proxy szolgáltatás bármelyik példánya fut, akkor a rendszer rendszeres időközönként újra létrehozza a serviceConnectionPoint objektumot. Ha a DC Agent szolgáltatás bármelyik példánya fut, a rendszer rendszeres időközönként újra létrehozza a serviceConnectionPoint-objektumot és a SYSVOL-állapotot.

1. Távolítsa el a proxy szoftvert az összes gépről. Ez a lépés **nem** igényel újraindítást.
2. Távolítsa el a DC Agent szoftverét az összes tartományvezérlőről. Ehhez a lépéshez újraindítás **szükséges** .
3. Manuálisan távolítsa el az összes proxy Service-csatlakozási pontot az egyes tartománynév-névhasználati környezetekben. Az objektumok helye a következő Active Directory PowerShell-paranccsal deríthető fel:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Ne hagyja ki a csillagot ("*") a $keywords változó értékének végén.

   Az eredményül kapott objektum (ok) megtalálhatók a `Get-ADObject` parancs `Remove-ADObject`segítségével, vagy manuálisan is törölhetők.

4. Manuálisan távolítsa el az összes tartományvezérlői ügynök csatlakoztatási pontját az egyes tartománynév-névhasználati környezetekben. A szoftver központi telepítésének módjától függően előfordulhat, hogy az erdőn belül egy adott objektum egy tartományvezérlőn található. Az objektum helye a következő Active Directory PowerShell-paranccsal deríthető fel:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Az eredményül kapott objektum (ok) megtalálhatók a `Get-ADObject` parancs `Remove-ADObject`segítségével, vagy manuálisan is törölhetők.

   Ne hagyja ki a csillagot ("*") a $keywords változó értékének végén.

5. Távolítsa el manuálisan az erdő szintű konfigurációs állapotot. Az erdő konfigurációs állapotát a rendszer a Active Directory konfigurációjának névhasználati környezetében lévő tárolóban tartja karban. A következő módon deríthető fel és törölhető:

   ```powershell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. Manuálisan távolítsa el az összes SYSVOL-hez kapcsolódó állapotot úgy, hogy manuálisan törli a következő mappát és annak tartalmát:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   Ha szükséges, az elérési út helyileg is elérhető egy adott tartományvezérlőn. az alapértelmezett hely a következő elérési úthoz hasonló:

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   Ez az elérési út eltérő, ha a SYSVOL-megosztás nem alapértelmezett helyen van konfigurálva.

## <a name="next-steps"></a>További lépések

[Gyakori kérdések az Azure AD jelszavas védelméről](howto-password-ban-bad-on-premises-faq.md)

A globális és az egyéni tiltott jelszavakkal kapcsolatos további információkért tekintse meg a [helytelen jelszavakat](concept-password-ban-bad.md) tartalmazó cikket.
