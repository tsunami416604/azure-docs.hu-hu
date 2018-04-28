---
title: Az Azure MFA NPS-kiterjesztés konfigurálása |} Microsoft Docs
description: Az NPS-bővítmény telepítése után ezeket a lépéseket használhatja a Speciális konfiguráció például IP engedélyezése és az egyszerű Felhasználónevük cseréje.
services: multi-factor-authentication
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: ''
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: d028be90f3a8c06b319faa442f77ab3e5c24dcb7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="advanced-configuration-options-for-the-nps-extension-for-multi-factor-authentication"></a>A hálózati házirend-kiszolgáló bővítmény a multi-factor Authentication speciális konfigurációs beállításai

A hálózati házirend-kiszolgáló (NPS) bővítményt a felhőalapú Azure multi-factor Authentication szolgáltatások átnyúlik a helyszíni infrastruktúrával. Ez a cikk feltételezi, hogy már rendelkezik a bővítmények vannak telepítve, és most szeretnék tudni, hogyan szabhatja testre a bővítményt az Ön igényeinek. 

## <a name="alternate-login-id"></a>Másodlagos bejelentkezési Azonosítóval

Mivel a hálózati házirend-kiszolgáló bővítmény csatlakozik a helyszíni és a felhő könyvtárak, ahol a helyszíni egyszerű felhasználónév (UPN) nem egyeznek meg a neveket a felhőben problémát előforduló. A probléma megoldásához, használja a másodlagos bejelentkezési azonosító. 

A hálózati házirend-kiszolgáló bővítmény belül az egyszerű Felhasználónevük helyett az Azure multi-factor Authentication használható Active Directory attribútumtár is kijelölhet. Ez lehetővé teszi, hogy a helyszíni erőforrásokat a kétlépéses ellenőrzéshez használttal védelme a helyszíni UPN-EK módosítása nélkül. 

Másodlagos felhasználói azonosítók konfigurálásához Ugrás `HKLM\SOFTWARE\Microsoft\AzureMfa` és szerkesztése a következő beállításazonosítókat:

| Name (Név) | Típus | Alapértelmezett érték | Leírás |
| ---- | ---- | ------------- | ----------- |
| LDAP_ALTERNATE_LOGINID_ATTRIBUTE | karakterlánc | Üres | Jelölje ki, hogy az egyszerű Felhasználónevük helyett használni kívánt Active Directory-attribútum neve. Ez az attribútum van megadva a AlternateLoginId attribútumaként. Ha ez a beállításazonosító értéke egy [érvényes Active Directory-attribútumot](https://msdn.microsoft.com/library/ms675090.aspx) (a példában, levelezési vagy displayName), majd az attribútumérték helyett a felhasználói UPN-hitelesítéshez használt. Ha ez a beállításazonosító nem üres, vagy nincs konfigurálva, majd AlternateLoginId le van tiltva, és a felhasználói UPN-hitelesítéshez használt. |
| LDAP_FORCE_GLOBAL_CATALOG | logikai | False (Hamis) | Ez a jelző használatával kényszerítheti a globális katalógus az LDAP-keresésekhez használatát AlternateLoginId keresésekor. A tartományvezérlő beállítása a globális katalógus, a AlternateLoginId attribútum hozzáadása a globális katalógus és engedélyeznie kell ezt a jelzőt. <br><br> Ha LDAP_LOOKUP_FORESTS van konfigurálva (nem üres), **Ez a jelző IGAZ van kényszerítve**, függetlenül attól, a beállításjegyzék-beállítás értékét. Ebben az esetben a hálózati házirend-kiszolgáló-bővítményhez olyan a globális katalógus az egyes erdőkhöz AlternateLoginId attribútummal kell konfigurálni. |
| LDAP_LOOKUP_FORESTS | karakterlánc | Üres | Adja meg a kereséshez erdők pontosvesszővel elválasztott listája. Például *contoso.com;foobar.com*. Ha ez a beállításazonosító van beállítva, a hálózati házirend-kiszolgáló bővítmény ismételt keres az erdők a sorrendet, amelyben szereplő és az első sikeres AlternateLoginId értéket adja vissza. Ha ez a beállításazonosító nincs konfigurálva, a AlternateLoginId keresési korlátozódik az aktuális tartományban.|

Problémák megoldása a másodlagos bejelentkezési azonosítókat, használja az alábbiak [másodlagos bejelentkezési azonosító hibák](howto-mfa-nps-extension-errors.md#alternate-login-id-errors).

## <a name="ip-exceptions"></a>IP-kivételek

Ha szeretné figyelni a kiszolgáló rendelkezésre állása, például ha egy terheléselosztó azok a kiszolgálók ellenőrizze fut-e a munkaterhelések, elküldése előtt nem szeretné, hogy a hitelesítési kérelem letiltása ellenőrzést. Ehelyett hozzon létre, amelyek biztosan szolgáltatásfiókok által használt IP-címek listáját, és tiltsa le a multi-factor Authentication követelményeinek, hogy a lista. 

Adja meg egy IP-engedélyezési lista, keresse fel `HKLM\SOFTWARE\Microsoft\AzureMfa` , és konfigurálja a következő beállításazonosítót: 

| Name (Név) | Típus | Alapértelmezett érték | Leírás |
| ---- | ---- | ------------- | ----------- |
| IP_WHITELIST | karakterlánc | Üres | Adjon meg egy IP-címek pontosvesszővel elválasztott listája. Gépek, ahol a szolgáltatáskérések származnak, például a NAS és a VPN-kiszolgáló IP-címét tartalmazza. IP-címtartományok olyan alhálózatok nem támogatottak. <br><br> Például *10.0.0.1;10.0.0.2;10.0.0.3*.

Ha a kérelem érkezik létezik-e az engedélyezett IP-címről, kétlépéses ellenőrzés kimarad. A megadott IP-címet az IP-engedélyezési lista a rendszer összehasonlítja a *ratNASIPAddress* attribútuma a RADIUS-kérelmet. Ha egy RADIUS-kérelmet a ratNASIPAddress attribútum nélkül érkezik, a következő figyelmeztetést naplóz: "P_WHITE_LIST_WARNING::IP engedélyezett van mellőzve forrás IP-cím a RADIUS-kérelmet NasIpAddress attribútum hiányzik."

## <a name="next-steps"></a>További lépések

[Hibaüzenetek által jelzett problémák megszüntetése az Azure Multi-Factor Authentication NPS-bővítményéből](howto-mfa-nps-extension-errors.md)