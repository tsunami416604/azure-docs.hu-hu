---
title: Az Azure MFA NPS-bővítmény konfigurálása – Azure Active Directory
description: A hálózati címző bővítmény telepítése után az alábbi lépésekkel speciális konfigurációkat használhat, például az IP-engedélyezési listát és az upn-csere.
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
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653786"
---
# <a name="advanced-configuration-options-for-the-nps-extension-for-multi-factor-authentication"></a>Speciális konfigurációs beállítások a Multi-Factor Authentication NPS-bővítményéhez

A Hálózati házirend-kiszolgáló (NPS) bővítmény kiterjeszti a felhőalapú Azure többtényezős hitelesítési funkciókat a helyszíni infrastruktúrára. Ez a cikk feltételezi, hogy már telepítette a bővítményt, és most szeretné tudni, hogyan szabhatja testre a bővítményt. 

## <a name="alternate-login-id"></a>Alternatív bejelentkezési azonosító

Mivel a nps-bővítmény csatlakozik mind a helyszíni és a felhőbeli könyvtárak, előfordulhat, hogy egy olyan probléma merül fel, ahol a helyszíni felhasználói egyszerű nevek (UPN) nem egyeznek meg a nevek a felhőben. A probléma megoldásához használjon alternatív bejelentkezési azonosítókat. 

A nps-bővítményen belül kijelölhet egy Active Directory-attribútumot, amelyet az Azure többtényezős hitelesítéshez az upn helyett használ. Ez lehetővé teszi, hogy kétlépéses ellenőrzéssel védje a helyszíni erőforrásokat a helyszíni upn-ek módosítása nélkül. 

Az alternatív bejelentkezési azonosítók konfigurálásához nyissa meg `HKLM\SOFTWARE\Microsoft\AzureMfa` és írja be a következő rendszerleíró értékeket:

| Név | Típus | Alapértelmezett érték | Leírás |
| ---- | ---- | ------------- | ----------- |
| LDAP_ALTERNATE_LOGINID_ATTRIBUTE | sztring | Üres | Jelölje meg az upn helyett használni kívánt Active Directory attribútum nevét. Ez az attribútum alternateLoginId attribútumként használatos. Ha ez a beállításjegyzék-érték [érvényes Active Directory attribútumra](https://msdn.microsoft.com/library/ms675090.aspx) van állítva (például mail vagy displayName), akkor az attribútum értékét használja a felhasználó upn hitelesítésére. Ha ez a beállításjegyzék-érték üres vagy nincs konfigurálva, akkor az AlternateLoginId le van tiltva, és a felhasználó upn-je lesz a hitelesítéshez. |
| LDAP_FORCE_GLOBAL_CATALOG | logikai | False (Hamis) | Ezzel a jelzővel kényszerítheti a globális katalógus használatát az LDAP-kereséshez az AlternateLoginId keresésekor. Konfiguráljon tartományvezérlőt globális katalógusként, adja hozzá az AlternateLoginId attribútumot a globális katalógushoz, majd engedélyezze ezt a jelzőt. <br><br> Ha LDAP_LOOKUP_FORESTS konfigurálva van (nem üres), **akkor ez a jelző igaz értékként lesz érvényesítve,** függetlenül a beállításjegyzék-beállítás értékétől. Ebben az esetben a nps-bővítmény megköveteli, hogy a globális katalógus konfigurálva legyen az AlternateLoginId attribútummal az egyes erdőkhöz. |
| LDAP_LOOKUP_FORESTS | sztring | Üres | Adja meg a keresendő erdők pontosvesszővel elválasztott listáját. Például *contoso.com;foobar.com*. Ha ez a beállításjegyzék-érték konfigurálva van, a nps bővítmény iteratív módon keresi az összes erdőt a felsorolás sorrendjében, és visszaadja az első sikeres AlternateLoginId értéket. Ha ez a beállítási érték nincs konfigurálva, az AlternateLoginId-név az aktuális tartományra korlátozódik.|

Az alternatív bejelentkezési azonosítókkal kapcsolatos problémák elhárításához kövesse az [Alternatív bejelentkezési azonosítóval kapcsolatos hibák ajánlott lépéseit.](howto-mfa-nps-extension-errors.md#alternate-login-id-errors)

## <a name="ip-exceptions"></a>IP-kivételek

Ha figyelnie kell a kiszolgáló kondiállapotát, például ha a terheléselosztók ellenőrzik, hogy mely kiszolgálók futnak a számítási feladatok elküldése előtt, nem szeretné, hogy ezeket az ellenőrzéseket az ellenőrzési kérelmek blokkolják. Ehelyett hozzon létre egy listát azOKRÓL az IP-címekről, amelyekről tudja, hogy a szolgáltatásfiókok használják őket, és tiltsa le a többtényezős hitelesítéskövetelményeit a listához.

Az engedélyezett IP-címlista `HKLM\SOFTWARE\Microsoft\AzureMfa` konfigurálásához nyissa meg a következő beállításjegyzéket, és állítsa be az alábbi rendszerleíró értéket:

| Név | Típus | Alapértelmezett érték | Leírás |
| ---- | ---- | ------------- | ----------- |
| IP_WHITELIST | sztring | Üres | Adja meg az IP-címek pontosvesszővel elválasztott listáját. Adja meg azoknak a gépeknek az IP-címét, amelyekből a szolgáltatáskérések származnak, például a NAS/VPN-kiszolgálót. Az IP-tartományok és alhálózatok nem támogatottak. <br><br> Például *10.0.0.1;10.0.0.2;10.0.0.3*.

> [!NOTE]
> Ezt a beállításkulcsot alapértelmezés szerint nem a telepítő hozta létre, és hiba jelenik meg az AuthZOptCh naplóban a szolgáltatás újraindításakor. Ez a hiba a naplóban figyelmen kívül hagyható, de ha ez a beállításkulcs jön létre, és üresen marad, ha nem szükséges, akkor a hibaüzenet nem ad vissza.

Amikor egy kérelem érkezik egy IP-cím, amely létezik a, a `IP_WHITELIST`kétlépéses ellenőrzés kimarad. A program összehasonlítja az IP-listát a RADIUS-kérelem *ratNASIPAddress* attribútumában megadott IP-címmel. Ha egy RADIUS-kérelem ratNASIPAddress attribútum nélkül érkezik, a rendszer a következő figyelmeztetést naplózza: "P_WHITE_LIST_WARNING::IP whitelist a rendszer figyelmen kívül marad, mivel a forrás IP-cím hiányzik a NasIpAddress attribútum RADIUS-kérelméből."

## <a name="next-steps"></a>További lépések

[Hibaüzenetek által jelzett problémák megszüntetése az Azure Multi-Factor Authentication NPS-bővítményéből](howto-mfa-nps-extension-errors.md)
