---
title: Az Azure MFA NPS-bővítmény konfigurálása – Azure Active Directory
description: A hálózati házirend-kiszolgáló bővítmény telepítése után az alábbi lépéseket követve speciális konfigurációt használhat, például az IP-címek engedélyezési és UPN-helyettesítését.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34d92af88106151e7efba679c53c5b5bd1c07dcd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80653786"
---
# <a name="advanced-configuration-options-for-the-nps-extension-for-multi-factor-authentication"></a>Speciális konfigurációs beállítások a Multi-Factor Authentication NPS-bővítményéhez

A hálózati házirend-kiszolgáló (NPS) bővítmény kibővíti a felhőalapú Azure Multi-Factor Authentication funkcióit a helyszíni infrastruktúrába. Ez a cikk feltételezi, hogy már telepítette a bővítményt, és most szeretné tudni, hogyan szabhatja testre a szükséges bővítményt. 

## <a name="alternate-login-id"></a>Másodlagos bejelentkezési azonosító

Mivel a hálózati házirend-kiszolgáló bővítmény a helyszíni és a Felhőbeli címtárakhoz is csatlakozik, előfordulhat, hogy olyan problémába ütközik, amelyben a helyszíni felhasználónevek (UPN) nem egyeznek a felhőben található nevekkel. A probléma megoldásához használjon alternatív bejelentkezési azonosítókat. 

A hálózati házirend-kiszolgáló bővítményben kijelölhet egy Active Directory attribútumot, amelyet az egyszerű felhasználónév helyett használni szeretne az Azure Multi-Factor Authenticationhoz. Ez lehetővé teszi a helyszíni erőforrások kétlépéses ellenőrzéssel való ellátását a helyszíni egyszerű felhasználónevek módosítása nélkül. 

Az alternatív bejelentkezési azonosítók konfigurálásához lépjen `HKLM\SOFTWARE\Microsoft\AzureMfa` a (z) elemre, és szerkessze a következő beállításjegyzékbeli értékeket:

| Name (Név) | Típus | Alapértelmezett érték | Leírás |
| ---- | ---- | ------------- | ----------- |
| LDAP_ALTERNATE_LOGINID_ATTRIBUTE | sztring | Üres | Jelölje ki az egyszerű felhasználónév helyett használni kívánt Active Directory attribútum nevét. Ez az attribútum a AlternateLoginId attribútumként szolgál. Ha a beállításazonosító [érvényes Active Directory attribútumra](https://msdn.microsoft.com/library/ms675090.aspx) van beállítva (például E-mail vagy DisplayName), akkor a rendszer az attribútum értékét használja a felhasználó egyszerű felhasználóneve helyett a hitelesítéshez. Ha a beállításazonosító üres vagy nincs konfigurálva, akkor a AlternateLoginId le van tiltva, és a rendszer a felhasználó UPN-azonosítóját használja a hitelesítéshez. |
| LDAP_FORCE_GLOBAL_CATALOG | logikai | False (Hamis) | Ezzel a jelzővel kényszerítheti a globális katalógus használatát az LDAP-keresésekhez, amikor megkeresi a AlternateLoginId. Konfiguráljon egy tartományvezérlőt globális katalógusként, adja hozzá a AlternateLoginId attribútumot a globális katalógushoz, majd engedélyezze ezt a jelzőt. <br><br> Ha LDAP_LOOKUP_FORESTS konfigurálva van (nem üres), **Ez a jelző igaz értékre van kényszerítve**, a beállításjegyzék-beállítás értékétől függetlenül. Ebben az esetben a hálózati házirend-kiszolgáló bővítménye megköveteli, hogy a globális katalógus minden erdő AlternateLoginId attribútumával legyen konfigurálva. |
| LDAP_LOOKUP_FORESTS | sztring | Üres | Adja meg a keresendő erdők pontosvesszővel tagolt listáját. Például: *contoso. com; foobar. com*. Ha a beállításazonosító konfigurálva van, a hálózati házirend-kiszolgáló bővítmény a iteratív megkeresi az összes erdőt a felsorolt sorrendben, és visszaadja az első sikeres AlternateLoginId értéket. Ha a beállításazonosító nincs konfigurálva, a AlternateLoginId-keresés az aktuális tartományra korlátozódik.|

A másodlagos bejelentkezési azonosítókkal kapcsolatos problémák elhárításához használja az [alternatív bejelentkezési azonosítók hibáinak](howto-mfa-nps-extension-errors.md#alternate-login-id-errors)ajánlott lépéseit.

## <a name="ip-exceptions"></a>IP-kivételek

Ha figyelnie kell a kiszolgáló rendelkezésre állását, például ha a terheléselosztó ellenőrzi, hogy mely kiszolgálók futnak a munkaterhelések elküldése előtt, akkor nem szeretné, hogy a rendszer ellenőrzési kérések által blokkolja ezeket az ellenőrzéseket. Ehelyett hozzon létre egy listát az Ön által ismert IP-címekről, és tiltsa le az adott lista Multi-Factor Authentication követelményeit.

Az IP-címek engedélyezési listájának konfigurálásához `HKLM\SOFTWARE\Microsoft\AzureMfa` nyissa meg a következő beállításértéket, és konfigurálja a beállításjegyzéket:

| Name (Név) | Típus | Alapértelmezett érték | Leírás |
| ---- | ---- | ------------- | ----------- |
| IP_WHITELIST | sztring | Üres | Adja meg az IP-címek pontosvesszővel tagolt listáját. Adja meg azoknak a számítógépeknek a IP-címeit, amelyeken a szolgáltatás kérelmek származnak, például a NAS/VPN-kiszolgálótól. Az IP-címtartományok és az alhálózatok nem támogatottak. <br><br> Például: *10.0.0.1; 10.0.0.2; 10.0.0.3*.

> [!NOTE]
> A telepítő alapértelmezés szerint nem hozza létre ezt a beállításkulcsot, és egy hiba jelenik meg a AuthZOptCh-naplóban a szolgáltatás újraindításakor. Ez a hiba a naplóban figyelmen kívül hagyható, de ha a beállításkulcs létrejött, és nem szükséges, üresen marad, a hibaüzenet nem jelenik meg.

Ha egy kérelem olyan IP-címről érkezik, amely létezik a- `IP_WHITELIST`ben, a kétlépéses ellenőrzés kimarad. Az IP-listát a RADIUS-kérelem *ratNASIPAddress* attribútumában megadott IP-címhez hasonlítjuk. Ha egy RADIUS-kérelem a ratNASIPAddress attribútum nélkül érkezik, a rendszer a következő figyelmeztetést naplózza: "P_WHITE_LIST_WARNING:: az IP-engedélyezési lista figyelmen kívül lesz hagyva, mert a forrás IP-cím hiányzik a RADIUS-kérelemben a NasIpAddress attribútumban."

## <a name="next-steps"></a>További lépések

[Hibaüzenetek által jelzett problémák megszüntetése az Azure Multi-Factor Authentication NPS-bővítményéből](howto-mfa-nps-extension-errors.md)
