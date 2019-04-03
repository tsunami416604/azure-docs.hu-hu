---
title: Hibaelhárítás az Azure ad-ben jelszavas védelem – Azure Active Directory
description: Megismerheti az Azure AD jelszó védelmi gyakori hibáinak elhárítása
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
ms.openlocfilehash: 108ead982529d2ac6549cceffd9d2177ab6456bf
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2019
ms.locfileid: "58863176"
---
# <a name="azure-ad-password-protection-troubleshooting"></a>Az Azure AD jelszóvédelem hibáinak elhárítása

Az Azure AD jelszóvédelem üzembe helyezés után hibaelhárítási lehet szükség. Ez a cikk részletezik segítenek megérteni néhány gyakori hibaelhárítási lépéseket.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>A tartományvezérlő agent proxy nem található a címtárban

A probléma tünete fő 30017 események a tartományvezérlő az ügynök felügyeleti eseménynaplójában.

A szokásos a probléma oka, hogy egy proxykiszolgáló van még nem regisztrált. Proxy regisztrálva van, ha előfordulhat némi késleltetés AD replikációs késés miatt amíg egy adott tartományvezérlő ügynök láthatja azt a proxyt.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>A tartományvezérlő ügynök addig nem tud kommunikálni a proxy

A probléma tünete fő 30018 események a tartományvezérlő az ügynök felügyeleti eseménynaplójában. Ennek több lehetséges oka lehet:

1. A hálózat nem teszi lehetővé a hálózati kapcsolat és a regisztrált proxy(s) egy elkülönített része a tartományvezérlő ügynök található. Ez a probléma lehet expected\benign mindaddig, amíg más DC ügynökök kommunikálni tudjanak a proxy(s) szükséges Azure-t, amely majd szerzi be a házirend fájloknak a sysvol-megosztás replikációval elkülönített tartományvezérlő által a jelszóházirendek letöltéséhez.

1. A proxy gazdagépen blokkolja az RPC-végpont eseményleképező végpont (135-ös port)

   Az Azure AD-jelszó védelme Proxy telepítő automatikusan létrehozza a Windows tűzfalhoz bejövő szabályt, amely lehetővé teszi, hogy a 135-ös porthoz való hozzáférés. Ha ez a szabály nem található, vagy később le van tiltva, DC-ügynökök nem tudja a Proxy szolgáltatással való kommunikációra. Ha a beépített Windows tűzfala le van tiltva helyett egy másik tűzfal termék, konfigurálnia kell a 135-ös porthoz való hozzáférés engedélyezéséhez, hogy a tűzfal.

1. A proxy gazdagépen blokkolja az RPC-végpont (dinamikus vagy statikus) a Proxy szolgáltatás által a kérésüket

   Az Azure AD-jelszó védelme Proxy telepítő automatikusan létrehozza a Windows tűzfal az Azure AD-jelszó védelme Proxy szolgáltatás által figyelt bejövő szabályt, amely lehetővé teszi a bejövő portra a hozzáférést. Ha ez a szabály nem található, vagy később le van tiltva, DC-ügynökök nem tudja a Proxy szolgáltatással való kommunikációra. Ha a beépített Windows tűzfala le van tiltva helyett egy másik tűzfal termék, konfigurálnia kell, hogy az Azure AD-jelszó védelme Proxy szolgáltatás által figyelt tűzfalat, hogy engedélyezze a bejövő portra a hozzáférést. Ez a konfiguráció lehet végezni pontosabb, ha a proxykiszolgáló konfigurálva van egy adott statikus RPC-port figyelésére (használatával a `Set-AzureADPasswordProtectionProxyConfiguration` parancsmag).

## <a name="the-proxy-service-can-receive-calls-from-dc-agents-in-the-domain-but-is-unable-to-communicate-with-azure"></a>A Proxy szolgáltatás hívást is fogadhat DC ügynököktől a tartományban, de nem tud kommunikálni az Azure-ral

1. Ellenőrizze a proxy gép rendelkezik-e a felsorolt végpontokra irányuló a [központi telepítésére vonatkozó követelmények](howto-password-ban-bad-on-premises-deploy.md).

1. Győződjön meg arról, hogy az erdő és kiszolgálók ellen ugyanahhoz az Azure-bérlőhöz regisztrált összes proxy.

   Futtatásával ellenőrizheti ezt az `Get-AzureADPasswordProtectionProxy` és `Get-AzureADPasswordProtectionDCAgent` PowerShell-parancsmagok, majd hasonlítsa össze a `AzureTenant` minden egyes tulajdonság elemet adott vissza. Helyes működéséhez ezeket azonosnak kell lennie az erdőn belül, az összes tartományvezérlő ügynökök és a proxykiszolgálókon keresztül.

   Ha egy Azure-bérlő regisztrációs eltérés feltétel létezik, ez futtatásával javítható a `Register-AzureADPasswordProtectionProxy` és/vagy `Register-AzureADPasswordProtectionForest` PowerShell-parancsmagok szükség esetén gondoskodik róla, hogy az összes regisztrációs hitelesítő adatait az Azure-ugyanazt bérlőhöz használandó.

## <a name="the-dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files-and-other-state"></a>A tartományvezérlő-ügynök nem tudja titkosítása és visszafejtése a jelszóval házirendfájljait és más állapot

Ez a probléma a tünetek különböző manifest is, de általában rendelkezik egy közös alapvető ok.

Az Azure AD jelszóvédelem kritikus függőségi rendelkezik a titkosítási és visszafejtési funkciót, a Microsoft kulcsszolgáltató szolgáltatás, által biztosított, vagyis a Windows Server 2012 rendszert futtató tartományvezérlőkön és újabb verziói. A KDS engedélyezve van és működőképes a minden Windows Server 2012 és újabb rendszerű tartományvezérlők olyan tartományban kell lennie.

A KDS alapértelmezés szerint a szolgáltatás a szolgáltatás indítási módja manuális (eseményindító indítás) van konfigurálva. Ez a konfiguráció azt jelenti, hogy egy ügyfél megpróbálja a szolgáltatás használatához először elindul igény szerinti. Az alapértelmezett szolgáltatás indítási módja elfogadható-e az Azure AD-jelszó védelmi működjön.

Ha le van tiltva a KDS-szolgáltatás indítási módja van konfigurálva, ez a konfiguráció előtt fog megfelelően működni az Azure AD jelszóvédelem kell rögzíteni.

Egy egyszerű tesztelési erre a problémára az, hogy manuálisan indítsa el a KDS, vagy a szolgáltatás az MMC-kezelőkonzolon keresztül, vagy más szolgáltatás-felügyeleti eszközök használata (például futtassa a "net start kdssvc" parancssort konzolon). Sikeresen elindul-e és fut a KDS várt.

A KDS éppen nem sikerült elindítani a leggyakrabban használt fő okát, hogy az Active Directory tartományvezérlő-objektum kívül az alapértelmezett tartományi szervezeti Egységbe. Ez a konfiguráció nem támogatott, amelyet a KDS, és nem egy Azure AD jelszóvédelem által meghatározott korlátozás. A probléma javítása, hogy a tartományvezérlő-objektum az alapértelmezett tartományi szervezeti Egységbe alatt.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>A gyenge jelszavakat megnyílt, de nem lehet

Ez a probléma számos oka lehet.

1. A tartományvezérlő-ügynökök nem tudja letölteni egy szabályzatot, vagy nem tudja visszafejteni a meglévő szabályzatokat. Ellenőrizze a lehetséges okokért a fenti témakörökben.

1. A jelszó a házirend-kényszerítési módban továbbra is be van állítva a naplózási. Ebben a konfigurációban van érvényben, újrakonfigurálása az Azure AD jelszóvédelem Portallal érvényesítése. Lásd: [engedélyezze a jelszavas védelem](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

1. A jelszóházirend le lett tiltva. Ha ez a konfiguráció van érvényben, konfigurálja újra, hogy engedélyezve van, az Azure AD jelszóvédelem portal használatával. Lásd: [engedélyezze a jelszavas védelem](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

1. A tartományvezérlő ügynökszoftver nem telepítette a tartomány összes tartományvezérlőn. Ebben a helyzetben meglehetősen nehéz győződjön meg arról, hogy a távoli Windows-ügyfelek egy adott tartományvezérlő célként egy jelszó-változtatási művelet során. Ha úgy véli, hogy sikeresen célzott egy adott tartományvezérlő, ahol a tartományvezérlő ügynök szoftver telepítve van, ellenőrizheti a tartományvezérlő az ügynök felügyeleti Eseménynapló kettős: serkenti az eredményt, függetlenül kell legalább egy eseményt a jelszó eredményét dokumentálása az érvényesítés. Ha nem található olyan esemény, a felhasználó, akinek jelszava megváltozott található, majd a jelszó módosítása valószínűleg dolgozta fel egy másik tartományvezérlő.

   Alternatív vizsgálat próbálja setting\changing jelszavak közvetlenül egy tartományvezérlő, ahol a tartományvezérlő ügynök szoftver telepítve van a bejelentkezés. Ez a módszer nem ajánlott éles Active Directory-tartományok.

   Míg a tartományvezérlő ügynökszoftver növekményes üzembe helyezést támogatják ezek a korlátozások vonatkoznak, a Microsoft azt javasolja, hogy a tartományvezérlő ügynök szoftver telepítve van egy tartomány összes tartományvezérlőjére minél hamarabb.

1. A jelszó adatérvényesítési algoritmust előfordulhat, hogy ténylegesen az elvárásoknak megfelelően működik. Lásd: [hogyan jelszavak értékeli ki a](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="directory-services-repair-mode"></a>Címtárszolgáltatások helyreállító módjában

Ha a tartományvezérlő a Címtárszolgáltatások helyreállító módjában rendszer indult, a tartományvezérlő-ügynökszolgáltatás észleli ezt az állapotot, és okozhat az összes jelszó érvényesítése vagy végrehajtó tevékenységek le kell tiltani, függetlenül a jelenleg aktív házirend-konfigurációt.

## <a name="emergency-remediation"></a>Sürgős szervizelés

Ha olyan helyzet akkor fordul elő, ha a tartományvezérlő-ügynökszolgáltatás problémákat okoz, a tartományvezérlő-ügynökszolgáltatás előfordulhat, hogy azonnal leáll. A tartományvezérlő ügynök jelszó szűrő dll továbbra is megpróbálja meghívni a nem futó szolgáltatás, és naplózza az figyelmeztetési eseményeket (10012, 10013), de ebben az időszakban minden bejövő jelszót fogad. A tartományvezérlő-ügynökszolgáltatás majd is konfigurálhatók keresztül a Windows szolgáltatásvezérlő indítási típusaként válassza a "Letiltott" igény szerint.

Szervizelési egy új mértéket az Enable-üzemmód beállítása az Azure AD jelszóvédelem portálon nem lehet. A frissített szabályzatot a letöltést követően minden egyes tartományvezérlő agent szolgáltatást egy videokártyának módban, ahol az összes jelszavak ügyfélként fogad el lépnek-van. További információkért lásd: [kényszerítési módban](howto-password-ban-bad-on-premises-operations.md#enforce-mode).

## <a name="domain-controller-demotion"></a>Tartományvezérlő lefokozása

Továbbra is fut a tartományvezérlő ügynökszoftver tartományvezérlő lefokozása támogatott. A rendszergazdák figyelembe kell azonban, hogy a tartományvezérlő ügynök szoftver továbbra is az aktuális jelszóházirend kényszerítése a lefokozási folyamat során. Az új helyi rendszergazdai fiók jelszava (a lefokozás művelet részeként megadott) érvényesítve lesz, mint bármely más jelszót. A Microsoft azt javasolja, hogy biztonságos jelszavakkal a helyi rendszergazdai fiókok választani egy tartományvezérlő lefokozási eljárást; részeként Új helyi rendszergazdai fiók jelszavának a tartományvezérlő ügynök szoftver érvényesítése azonban előre a lefokozás eljárások meglévő zavaró lehet.

A lefokozás sikeres volt, és a tartományvezérlő újraindítását, és újra normál kiszolgáló fut, a tartományvezérlő ügynökszoftver visszatér a passzív üzemmódban futó. Előfordulhat, hogy ezután távolítható bármikor.

## <a name="removal"></a>Eltávolítás

Ha úgy döntenek, hogy távolítsa el az a szoftver az Azure AD jelszó és a tisztítás minden kapcsolódó állapot tartomány és erdő, ez a feladat végezhető el az alábbi lépéseket követve:

> [!IMPORTANT]
> Fontos sorrendben hajtsa végre ezeket a lépéseket. Ha a Proxy szolgáltatás minden példányát futtató, rendszeresen újra létrehozza a serviceConnectionPoint objektum. Ha a tartományvezérlő-ügynökszolgáltatás bármelyik példányát futtató, rendszeresen újra létrehozza a serviceConnectionPoint objektum és a sysvol-állapot.

1. A Proxy szoftver eltávolítása a összes gép. Ebben a lépésben does **nem** újraindítása szükséges.
2. Távolítsa el a tartományvezérlő ügynökszoftver az összes tartományvezérlőről. Ebben a lépésben **igényel** újraindítás.
3. Manuálisan távolítsa el a Proxy összes szolgáltatáskapcsolati pontját, az egyes tartományi névhasználati környezeten. Ezek az objektumok helyét is felderítése a következő Active Directory PowerShell-paranccsal:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Nem hagyja ki a csillag ("*") a $keywords változó értékét a végén.

   Az eredményül kapott objektumok található keresztül a `Get-ADObject` parancs majd átadható olyan parancsoknak `Remove-ADObject`, vagy manuálisan törölték.

4. Manuálisan távolítsa el az összes tartományvezérlő ügynök csatlakozási pontok minden tartományi névhasználati környezetében. Lehet, hogy egy ezeket az objektumokat az erdő, attól függően, hogyan széles körben a szoftver telepítve lett a tartományvezérlőnkénti. Előfordulhat, hogy felderítése a helyét annak az objektumnak a következő Active Directory PowerShell-paranccsal:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Az eredményül kapott objektumok található keresztül a `Get-ADObject` parancs majd átadható olyan parancsoknak `Remove-ADObject`, vagy manuálisan törölték.

   Nem hagyja ki a csillag ("*") a $keywords változó értékét a végén.

5. Manuálisan távolítsa el az erdő szintű konfigurációs állapotát. Az erdő konfigurációs állapotát egy tárolót az Active Directory konfigurációelnevezési környezetében a változatlan marad. Ez felderíthető és törölni az alábbiak szerint:

   ```powershell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. Manuálisan távolítsa el az összes sysvol-állapot manuálisan kapcsolódó törlése a következő mappához és annak teljes tartalmát:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   Ha szükséges, ezt az elérési utat is is elérhető, helyileg egy adott tartományvezérlőn; az alapértelmezett hely a következő elérési út hasonló lenne:

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   Ez az elérési út eltérő, ha a sysvol-megosztás egy nem alapértelmezett helyen lett konfigurálva.

## <a name="next-steps"></a>További lépések

[Gyakori kérdések az Azure AD jelszóvédelem](howto-password-ban-bad-on-premises-faq.md)

A globális és egyéni letiltott jelszavak listáit további információkért tekintse meg a cikket [rossz jelszavak letiltása](concept-password-ban-bad.md)
