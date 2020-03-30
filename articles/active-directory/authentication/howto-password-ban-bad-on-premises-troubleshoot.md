---
title: Helyszíni Azure AD jelszóvédelem – problémamegoldás
description: Az Azure AD password protection helyszíni Active Directory tartományi szolgáltatások környezethez való hibaelhárítása
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79ebf543a3880a4f2c8ee8c0d706c268ef3f08d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263646"
---
# <a name="troubleshoot-on-premises-azure-ad-password-protection"></a>Hibaelhárítás: Helyszíni Azure AD jelszóvédelem

Az Azure AD password protection telepítése után hibaelhárításra lehet szükség. Ez a cikk részletesen ismerteti a gyakori hibaelhárítási lépéseket.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>A tartományvezérlő-ügynök nem talál proxyt a címtárban

A probléma fő tünete a tartományvezérlő-ügynök felügyeleti eseménynaplójában szereplő 30017-es események.

A probléma szokásos oka az, hogy a proxy még nincs regisztrálva. Ha egy proxy regisztrálva van, az AD replikációs késése miatt késhet, amíg egy adott tartományvezérlő-ügynök nem látja a proxyt.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>A tartományvezérlő-ügynök nem tud kommunikálni egy proxyval

A probléma fő tünete a 30018-as események a tartományvezérlő-ügynök felügyeleti eseménynaplójában. Ennek a problémának több oka is lehet:

1. A tartományvezérlő-ügynök a hálózat egy olyan elkülönített részén található, amely nem teszi lehetővé a hálózati kapcsolatot a regisztrált proxy(k)kal. Ez a probléma lehet jóindulatú, amíg más tartományvezérlő-ügynökök kommunikálni a proxy(k) annak érdekében, hogy töltse le a jelszó szabályzatok az Azure-ból. A letöltés után ezeket a házirendeket az elkülönített tartományvezérlő a sysvol megosztásban lévő házirendfájlok replikációja révén szerzi be.

1. A proxyállomás-rendszer blokkolja az RPC végpontleképező végpontjához való hozzáférést (135-ös port)

   Az Azure AD password protection proxy telepítő automatikusan létrehoz egy Windows tűzfal bejövő szabályt, amely lehetővé teszi a hozzáférést a 135-ös porthoz. Ha ezt a szabályt később törlik vagy letiltják, a tartományvezérlő-ügynökök nem tudnak kommunikálni a proxyszolgáltatással. Ha a beépített Windows tűzfal le van tiltva egy másik tűzfaltermék helyett, konfigurálnia kell a tűzfalat, hogy lehetővé tegye a hozzáférést a 135-ös porthoz.

1. A proxygazdagép blokkolja a proxyszolgáltatás által figyelt RPC-végponthoz (dinamikus vagy statikus) való hozzáférést

   Az Azure AD password protection proxy telepítő automatikusan létrehoz egy Windows tűzfal bejövő szabályt, amely lehetővé teszi a hozzáférést az Azure AD Password Protection Proxy szolgáltatás által hallgatott bejövő portokhoz. Ha ezt a szabályt később törlik vagy letiltják, a tartományvezérlő-ügynökök nem tudnak kommunikálni a proxyszolgáltatással. Ha a beépített Windows tűzfal le van tiltva egy másik tűzfaltermék helyett, konfigurálnia kell a tűzfalat, hogy az Azure AD Password Protection Proxy szolgáltatás által hallgatott bejövő portokhoz való hozzáférést engedélyezze. Ez a konfiguráció pontosabblehet, ha a proxyszolgáltatás úgy van beállítva, hogy `Set-AzureADPasswordProtectionProxyConfiguration` egy adott statikus RPC-porton (a parancsmag használatával) figyeljen.

1. A proxyállomás-számítógép nincs úgy beállítva, hogy a tartományvezérlők bejelentkezhessenek a számítógépre. Ezt a viselkedést a "Hozzáférés a számítógéphez a hálózatról" felhasználói jogosultság-hozzárendelés szabályozza. Az erdő összes tartományában minden tartományvezérlőnek meg kell adni ezt a jogosultságot. Ez a beállítás gyakran egy nagyobb hálózati megerősítési erőfeszítés részeként korlátozott.

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>A proxyszolgáltatás nem tud kommunikálni az Azure-ral

1. Győződjön meg arról, hogy a proxygép rendelkezik kapcsolattal a [telepítési követelményekben](howto-password-ban-bad-on-premises-deploy.md)felsorolt végpontokkal.

1. Győződjön meg arról, hogy az erdő és az összes proxykiszolgáló ugyanazon az Azure-bérlőn van regisztrálva.

   Ezt a követelményt a `Get-AzureADPasswordProtectionProxy` `Get-AzureADPasswordProtectionDCAgent` powershell-parancsmagok futtatásával `AzureTenant` ellenőrizheti, majd összehasonlíthatja az egyes visszaadott elemek tulajdonságát. A helyes működéshez a jelentett bérlői névnek azonosnak kell lennie az összes tartományvezérlő-ügynök és proxykiszolgáló között.

   Ha egy Azure-beli bérlői regisztrációs eltérés feltétel létezik, `Register-AzureADPasswordProtectionProxy` ez `Register-AzureADPasswordProtectionForest` a probléma megoldható az és/vagy a PowerShell-parancsmagok futtatásával szükség szerint, ügyelve arra, hogy az azonos Azure-bérlő hitelesítő adatait használja az összes regisztrációhoz.

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>A tartományvezérlő-ügynök nem tudja titkosítani vagy visszafejteni a jelszóházirend-fájlokat

Az Azure AD password protection kritikus függ a Microsoft Key Distribution Service által biztosított titkosítási és visszafejtési funkcióktól. A titkosítási vagy visszafejtési hibák számos tünettel jelentkezhetnek, és számos lehetséges oka lehet.

1. Győződjön meg arról, hogy a KDS szolgáltatás engedélyezve van és működőképes a tartomány összes Windows Server 2012-es és újabb tartományvezérlőjén.

   Alapértelmezés szerint a KDS szolgáltatás indítási módja kézi (Trigger Start) néven van konfigurálva. Ez a konfiguráció azt jelenti, hogy amikor egy ügyfél először próbálja használni a szolgáltatást, az igény szerint elindul. Ez az alapértelmezett szolgáltatásindítási mód az Azure AD password protection működéséhez elfogadható.

   Ha a KDS szolgáltatás indítási módja levan tiltva, ezt a konfigurációt ki kell javítani, mielőtt az Azure AD password protection megfelelően fog működni.

   A probléma egyszerű tesztje a KDS szolgáltatás manuális elindítása a Szolgáltatásfelügyeleti MMC konzolon keresztül, vagy más felügyeleti eszközök használatával (például a "net start kdssvc" futtatása parancssori konzolról). A KDS szolgáltatás várhatóan sikeresen elindul, és továbbra is fut.

   A KDS-szolgáltatás nem indítható leggyakoribb kiváltó oka az, hogy az Active Directory tartományvezérlő objektum a tartományvezérlők alapértelmezett szervezeti egységén kívül található. Ezt a konfigurációt a KDS-szolgáltatás nem támogatja, és nem korlátozza az Azure AD password protection által előírt. A feltétel javítása az, hogy a tartományvezérlő-objektumot az alapértelmezett tartományvezérlők szervezeti egysége szerint helyezi át egy helyre.

1. A KDS nem kompatibilis titkosított pufferformátumának módosítása Windows Server 2012 R2-ről Windows Server 2016-ra

   A Windows Server 2016-ban kds biztonsági javítást vezettek be, amely módosítja a KDS titkosított pufferek formátumát; Windows Server 2012 és Windows Server 2012 R2 rendszeren ezek a pufferek néha nem fejthetők vissza. A fordított irány rendben van - a Windows Server 2012 és a Windows Server 2012 R2 rendszeren KDS titkosítású pufferek mindig sikeresen visszafejtik a Windows Server 2016-os és újabb rendszert. Ha az Active Directory-tartományoktartományvezérlői ezen operációs rendszerek vegyesen futnak, az Azure AD password protection időnként visszafejtési hibákat jelenthet. A biztonsági javítás jellege miatt nem lehet pontosan megjósolni a hibák időzítését vagy tüneteit, és mivel nem determininális, hogy melyik Azure AD jelszavas védelem tartományvezérlő-ügynök, amelyen a tartományvezérlő egy adott időpontban titkosítja az adatokat.

   A Microsoft vizsgálja a probléma javítását, de még nem áll rendelkezésre eta. Addig is a probléma nem kerülő megoldásra, csak arra, hogy nem futtatja ezeket az inkompatibilis operációs rendszereket az Active Directory tartomány(ok)on. Más szóval csak Windows Server 2012 és Windows Server 2012 R2 tartományvezérlőket kell futtatni, vagy csak Windows Server 2016 és a fenti tartományvezérlőket kell futtatni.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>Gyenge jelszavakat fogadnak el, de nem szabad

Ennek a problémának több oka is lehet.

1. A tartományvezérlő-ügynök(ek) egy nyilvános előzetes verziójú szoftververziót futtatnak, amely lejárt. Lásd: [A nyilvános előzetes tartományvezérlő-ügynök szoftvere lejárt.](howto-password-ban-bad-on-premises-troubleshoot.md#public-preview-dc-agent-software-has-expired)

1. A tartományvezérlő-ügynök(ek) nem tudnak letölteni egy házirendet, vagy nem tudja visszafejteni a meglévő házirendeket. Ellenőrizze a lehetséges okokat a fenti témakörökben.

1. A jelszóházirend kényszerítési módja továbbra is naplózásra van állítva. Ha ez a konfiguráció érvényben van, konfigurálja újra az Azure AD password protection portál használatával kényszerítése. További információ: [Üzemmódok](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

1. A jelszóházirend le van tiltva. Ha ez a konfiguráció érvényben van, konfigurálja újra az Azure AD password protection portál használatával engedélyezve. További információ: [Üzemmódok](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

1. Nem telepítette a tartományvezérlő-ügynök szoftverét a tartomány összes tartományvezérlőjére. Ebben az esetben nehéz biztosítani, hogy a távoli Windows-ügyfelek egy adott tartományvezérlőt célozzanak meg a jelszómódosítási művelet során. Ha úgy gondolja, hogy sikeresen megcélzott egy adott tartományvezérlőt, ahol a tartományvezérlő-ügynök szoftvertelepítve van, ellenőrizheti a tartományvezérlő-ügynök felügyeleti eseménynaplójának kétszeri ellenőrzésével: az eredménytől függetlenül legalább egy esemény lesz a jelszó eredményének dokumentálására. Érvényesítés. Ha nincs esemény a felhasználó számára, akinek a jelszavát módosították, akkor a jelszómódosítást valószínűleg egy másik tartományvezérlő dolgozta fel.

   Alternatív tesztként próbálja meg a jelszavakat úgy módosítani, hogy közvetlenül egy tartományvezérlőre van bejelentkezve, ahol a tartományvezérlő-ügynök szoftvere telepítve van. Ez a módszer nem ajánlott éles Active Directory tartományokhoz.

   Bár a tartományvezérlő-ügynök szoftverének növekményes telepítését a korlátozások is támogatják, a Microsoft nyomatékosan javasolja, hogy a tartományvezérlő-ügynök szoftvere a lehető leghamarabb települjen a tartomány összes tartományvezérlőjére.

1. Előfordulhat, hogy a jelszóérvényesítési algoritmus a várt módon működik. Lásd: [Hogyan értékelik ki a jelszavakat.](concept-password-ban-bad.md#how-are-passwords-evaluated)

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>Az Ntdsutil.exe nem tudja beállítani a gyenge DSRM-jelszót

Az Active Directory mindig ellenőrzi az új címtárszolgáltatások javítási módjelszavát, hogy megbizonyosodjon arról, hogy az megfelel a tartomány jelszóösszetettségére vonatkozó követelményeinek; ez az ellenőrzés is felhívja a jelszószűrő dlls, például az Azure AD password protection. Ha az új DSRM-jelszót elutasítja, a következő hibaüzenet jelenik meg:

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

Amikor az Azure AD Password Protection naplózza az Active Directory DSRM-jelszó jelszóérvényesítési eseménynapló-eseményeit, várható, hogy az eseménynapló-üzenetek nem tartalmaznak felhasználónevet. Ez a jelenség azért fordul elő, mert a DSRM-fiók olyan helyi fiók, amely nem része a tényleges Active Directory-tartománynak.  

## <a name="domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password"></a>A tartományvezérlő replikaelőléptetése gyenge DSRM-jelszó miatt sikertelen

A tartományvezérlő előléptetési folyamata során az új címtárszolgáltatások javítási módú jelszava a tartomány egy meglévő tartományvezérlőjének lesz elküldve érvényesítéscéljából. Ha az új DSRM-jelszót elutasítja, a következő hibaüzenet jelenik meg:

```powershell
Install-ADDSDomainController : Verification of prerequisites for Domain Controller promotion failed. The Directory Services Restore Mode password does not meet a requirement of the password filter(s). Supply a suitable password.
```

Csakúgy, mint a fenti kérdésben, minden Azure AD password protection jelszóérvényesítési eredményesemény lesz üres felhasználónevek ebben a forgatókönyvben.

## <a name="domain-controller-demotion-fails-due-to-a-weak-local-administrator-password"></a>A tartományvezérlő lefokozása gyenge helyi rendszergazdai jelszó miatt sikertelen

A tartományvezérlő-ügynök szoftverét még mindig futtató tartományvezérlő lefokozására támogatott. A rendszergazdáknak azonban tisztában kell lenniük azzal, hogy a tartományvezérlő-ügynök szoftvere továbbra is érvényesíti az aktuális jelszóházirendet a lefokozási eljárás során. Az új helyi rendszergazdai fiók jelszava (amely a lefokozási művelet részeként van megadva) ugyanúgy érvényesíthető, mint bármely más jelszó. A Microsoft azt javasolja, hogy a tartományvezérlő lefokozása eljárás részeként válasszon biztonságos jelszavakat a helyi rendszergazdai fiókokhoz.

Miután a lefokozás sikeresvolt, és a tartományvezérlő újraindult, és ismét normál tagkiszolgálóként fut, a tartományvezérlő-ügynök szoftvere visszaáll passzív módban. Ezután bármikor eltávolítható.

## <a name="booting-into-directory-services-repair-mode"></a>Rendszerindítás címtárszolgáltatások javítási üzemmódba

Ha a tartományvezérlő címtárszolgáltatások javítása üzemmódba van rendszerezve, a tartományvezérlő-ügynök jelszószűrője észleli ezt a feltételt, és az összes jelszó-érvényesítési vagy kényszerítési tevékenység letiltását eredményezi, függetlenül az aktuálisan aktív házirendtől Konfigurációs. A tartományvezérlő-ügynök jelszószűrője dll egy 10023-as figyelmeztető eseményt naplóz a rendszergazdai eseménynaplóba, például:

```text
The password filter dll is loaded but the machine appears to be a domain controller that has been booted into Directory Services Repair Mode. All password change and set requests will be automatically approved. No further messages will be logged until after the next reboot.
```
## <a name="public-preview-dc-agent-software-has-expired"></a>A nyilvános előzetes tartományvezérlő-ügynök szoftvere lejárt

Az Azure AD Password Protection nyilvános előzetes verzióidőszaka alatt a tartományvezérlő-ügynök szoftverét a következő időpontokban a jelszóérvényesítési kérelmek feldolgozásának leállítására kódolták:

* Az 1.2.65.0-s verzió 2019.
* 2019. július 1-jén az 1.2.25.0-s verzióés a jelszóérvényesítési kérelmek előzetes feldolgozása leállt.

A határidő közeledtével az összes időkorlátos tartományvezérlő-ügynökverzió 10021-es eseményt fog kibocsátani a tartományvezérlő-ügynök felügyeleti eseménynaplójában a rendszerindításkor, amely így néz ki:

```text
The password filter dll has successfully loaded and initialized.

The allowable trial period is nearing expiration. Once the trial period has expired, the password filter dll will no longer process passwords. Please contact Microsoft for an newer supported version of the software.

Expiration date:  9/01/2019 0:00:00 AM

This message will not be repeated until the next reboot.
```

A határidő lejárta után az összes időkorlátos tartományvezérlő-ügynökverzió 10022-es eseményt fog kibocsátani a tartományvezérlő-ügynök felügyeleti eseménynaplójában a rendszerindításkor, amely így néz ki:

```text
The password filter dll is loaded but the allowable trial period has expired. All password change and set requests will be automatically approved. Please contact Microsoft for a newer supported version of the software.

No further messages will be logged until after the next reboot.
```

Mivel a határidő csak a kezdeti rendszerindításkor van ellenőrizve, előfordulhat, hogy ezeket az eseményeket csak a naptári határidő lejárta után látja. A határidő felismerése után sem a tartományvezérlőre, sem a nagyobb környezetre nem kerül sor negatív hatásokra, kivéve az összes jelszót.

> [!IMPORTANT]
> A Microsoft azt javasolja, hogy a lejárt nyilvános előzetes tartományvezérlő-ügynökök azonnal frissítsék a legújabb verzióra.

A tartományvezérlő-ügynökök környezetben való frissítésének egyszerű módja a `Get-AzureADPasswordProtectionDCAgent` parancsmag futtatása, például:

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

Ebben a témakörben a SoftwareVersion mező nyilvánvalóan a legfontosabb tulajdonság, amelyet meg kell nézni. A PowerShell-szűrés segítségével kiszűrheti azokat a tartományvezérlő-ügynököket is, amelyek már a szükséges alapverzióban vagy annál magasabbak, például:

```powershell
PS C:\> $LatestAzureADPasswordProtectionVersion = "1.2.125.0"
PS C:\> Get-AzureADPasswordProtectionDCAgent | Where-Object {$_.SoftwareVersion -lt $LatestAzureADPasswordProtectionVersion}
```

Az Azure AD Password Protection Proxy szoftver nem időkorlátos bármely verzióban. A Microsoft továbbra is azt javasolja, hogy a tartományvezérlőt és a proxyügynököket is frissítse a legújabb verziókra, amint azok kiadásra kerülnek. A `Get-AzureADPasswordProtectionProxy` parancsmag használható a frissítésre szoruló proxyügynökök megkeresésére, hasonlóan a tartományvezérlő-ügynökök fenti példájához.

Az egyes frissítési eljárásokról a [tartományvezérlő-ügynök frissítése](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-dc-agent) és [a proxyszolgáltatás frissítése](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-proxy-service) című dokumentumban talál további részleteket.

## <a name="emergency-remediation"></a>Sürgősségi kármentesítés

Ha olyan helyzet áll elő, amikor a tartományvezérlő-ügynök szolgáltatás problémákat okoz, a tartományvezérlő-ügynök szolgáltatás azonnal leállhat. A tartományvezérlő-ügynök jelszószűrője dll továbbra is megpróbálja meghívni a nem futó szolgáltatást, és naplózza a figyelmeztető eseményeket (10012, 10013), de ez alatt az idő alatt minden bejövő jelszót elfogad. A tartományvezérlő-ügynök szolgáltatás ezután a Windows service Control Manager segítségével is konfigurálható a "Letiltva" indítási típussal.

Egy másik szervizelési intézkedés az lenne, hogy állítsa be az Engedélyezés i mód nem az Azure AD password protection portálon. A frissített házirend letöltése után minden tartományvezérlő-ügynökszolgáltatás nyugalmi módba lép, ahol az összes jelszót a rendszer ugyanúgy fogadja el, ahogy van. További információ: [Üzemmódok](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

## <a name="removal"></a>Eltávolítása

Ha úgy dönt, hogy eltávolítja az Azure AD jelszavas védelmi szoftvert, és törli az összes kapcsolódó állapotot a tartomány(ok) és az erdő, ez a feladat a következő lépésekkel hajtható végre:

> [!IMPORTANT]
> Fontos, hogy ezeket a lépéseket sorrendben hajtsa végre. Ha a proxyszolgáltatás bármely példánya fut, az rendszeres időközönként újra létrehozza a serviceConnectionPoint objektumot. Ha a tartományvezérlő-ügynök szolgáltatás bármely példánya fut, az rendszeres időközönként újra létrehozza a serviceConnectionPoint objektumot és a sysvol állapotot.

1. Távolítsa el a Proxy szoftvert az összes gépről. Ez a lépés **nem** igényel újraindítást.
2. Távolítsa el a tartományvezérlő-ügynök szoftverét az összes tartományvezérlőről. Ez a lépés újraindítást **igényel.**
3. Manuálisan távolítsa el az összes proxyszolgáltatás-csatlakozási pontot az egyes tartománynév-kiosztási környezetekben. Ezeknek az objektumoknak a helyét az Active Directory PowerShell következő paranccsal fedezheti fel a rendszer:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Ne hagyja ki a csillagot ("*") a $keywords változó értékének végén.

   A `Get-ADObject` parancs által talált objektum(ok) ezután `Remove-ADObject`a programba irányíthatók, vagy manuálisan törölhetők.

4. Manuálisan távolítsa el az összes tartományvezérlő-ügynök csatlakozási pontját az egyes tartománynév-kiosztási környezetekben. Tartományvezérlőnként egy ilyen objektum lehet az erdőben, attól függően, hogy milyen széles körben telepítették a szoftvert. Az objektum helyét a következő Active Directory PowerShell-paranccsal fedezheti fel a rendszer:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   A `Get-ADObject` parancs által talált objektum(ok) ezután `Remove-ADObject`a programba irányíthatók, vagy manuálisan törölhetők.

   Ne hagyja ki a csillagot ("*") a $keywords változó értékének végén.

5. Manuálisan távolítsa el az erdőszintű konfigurációs állapotot. Az erdő konfigurációs állapota az Active Directory konfigurációs elnevezési környezetében lévő tárolóban marad meg. A következőképpen fedezhető fel és törölhető:

   ```powershell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. Manuálisan távolítsa el az összes sysvol kapcsolódó állapotot a következő mappa és annak teljes tartalmának manuális törlésével:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   Szükség esetén ez az elérési út helyileg is elérhető egy adott tartományvezérlőn; az alapértelmezett hely a következő elérési úthoz hasonló lenne:

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   Ez az elérési út eltérő, ha a sysvol megosztás nem alapértelmezett helyen van konfigurálva.

## <a name="next-steps"></a>További lépések

[Gyakori kérdések az Azure AD jelszavas védelemmel kapcsolatban](howto-password-ban-bad-on-premises-faq.md)

A globális és egyéni tiltott jelszólistákról a Rossz jelszavak tiltása című cikkben olvashat [bővebben.](concept-password-ban-bad.md)
