---
title: Az Azure MFA NPS-bővítményének konfigurálása |} A Microsoft Docs
description: Miután az NPS-bővítményének telepítése, használata például IP-engedélyezési és UPN csere speciális konfiguráció ezeket a lépéseket.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.openlocfilehash: 5d7b14825b8b34c2ab742febe463ea518209a82f
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55075618"
---
# <a name="advanced-configuration-options-for-the-nps-extension-for-multi-factor-authentication"></a>Speciális konfigurációs beállításait a multi-factor Authentication NPS bővítményével

A hálózati házirend-kiszolgáló (NPS) bővítmény beleér a helyszíni infrastruktúrát a felhőalapú Azure multi-factor Authentication szolgáltatások. Ez a cikk azt feltételezi, hogy már telepítve van a bővítményt, és most szeretné tudni, hogyan szabhatja testre a bővítményt, igényeinek megfelelően. 

## <a name="alternate-login-id"></a>Alternatív bejelentkezési Azonosítóval

Mivel az NPS-bővítményének kapcsolódik a helyszíni és a felhő-címtárak esetén előfordulhat, hogy hibába ütközik, a helyszíni egyszerű felhasználónév (UPN) nem egyezik meg a nevét, a felhőben. A probléma megoldásához használja a másodlagos bejelentkezési azonosítót. 

Az NPS-bővítményének belül az Azure multi-factor Authentication az egyszerű felhasználónév helyett használandó Active Directory attribútumtár is kijelölhet. Ez lehetővé teszi, hogy a helyszíni erőforrások védelme az kétlépéses ellenőrzést a helyszíni UPN-EK módosítása nélkül. 

Másodlagos felhasználói azonosítók konfigurálásához lépjen a `HKLM\SOFTWARE\Microsoft\AzureMfa` , és szerkessze a következő beállításazonosítókat:

| Name (Név) | Típus | Alapértelmezett érték | Leírás |
| ---- | ---- | ------------- | ----------- |
| LDAP_ALTERNATE_LOGINID_ATTRIBUTE | sztring | Üres | Active Directory-attribútumot használja az egyszerű felhasználónév helyett kívánt nevének megadására. Ezt az attribútumot használja AlternateLoginId attribútumként. Ha ez a beállításazonosító értéke egy [érvényes Active Directory-attribútum](https://msdn.microsoft.com/library/ms675090.aspx) (az például e-mail vagy displayName), majd az attribútumérték szerepel helyett a felhasználó egyszerű Felhasználónevét a hitelesítéshez. Ha ez a beállításazonosító nem üres, vagy nincs konfigurálva, majd AlternateLoginId le van tiltva, és a felhasználó egyszerű Felhasználónevét ezt használja hitelesítéshez. |
| LDAP_FORCE_GLOBAL_CATALOG | logikai | False (Hamis) | Ez a jelző használatával LDAP-keresések globális katalógus használatának kényszerítéséhez AlternateLoginId keresésekor. Konfiguráljon egy tartományvezérlőt globális katalógusként, adja hozzá a AlternateLoginId attribútumot a globális katalógusba, és engedélyeznie kell ezt a jelzőt. <br><br> Ha LDAP_LOOKUP_FORESTS van konfigurálva (nem üres) **ezt a jelzőt TRUE kényszerítve**, függetlenül attól, a beállításjegyzék-beállítás értékét. Ebben az esetben az NPS-bővítményt kell a globális katalógus az egyes erdőkhöz AlternateLoginId attribútummal kell konfigurálni. |
| LDAP_LOOKUP_FORESTS | sztring | Üres | Adjon meg egy pontosvesszővel elválasztott listáját az erdők keresésére. Ha például *contoso.com;foobar.com*. Ha ez a beállításazonosító van beállítva, az NPS-bővítményének iteratív keres az összes olyan erdőben, amelyben felsorolt és az első sikeres AlternateLoginId értéket adja vissza. Ez a beállításazonosító nem történik meg, ha az AlternateLoginId keresés az aktuális tartomány korlátozódik.|

Alternatív bejelentkezési azonosítók kapcsolatos problémák megoldásához, használja az alábbiak [alternatív bejelentkezési azonosító hibák](howto-mfa-nps-extension-errors.md#alternate-login-id-errors).

## <a name="ip-exceptions"></a>IP-kivételek

Ha szeretné figyelni a kiszolgáló rendelkezésre állása, például ha terheléselosztók ellenőrizze, fut mely számítási feladatok elküldése előtt nem szeretné ezeket az ellenőrzéseket ellenőrzési kérések le van tiltva. Ehelyett hozzon létre egy ismeri a szolgáltatásfiókok által használt IP-címek, és tiltsa le a lista a multi-factor Authentication követelményeinek. 

Adja meg egy IP-engedélyezési lista, lépjen a `HKLM\SOFTWARE\Microsoft\AzureMfa` és konfigurálja a következő beállításazonosítót: 

| Name (Név) | Típus | Alapértelmezett érték | Leírás |
| ---- | ---- | ------------- | ----------- |
| IP_WHITELIST | sztring | Üres | Adja meg az IP-címek pontosvesszővel elválasztott listáját. Gépek, ahol a szolgáltatáskérések származnak, például a NAS-/ VPN-kiszolgáló IP-címét tartalmazza. IP-címtartományok olyan alhálózatok nem támogatottak. <br><br> Ha például *10.0.0.1;10.0.0.2;10.0.0.3*.

Amikor kérelem érkezik IP-címről, amely szerepel az engedélyezési listán, a kétlépéses ellenőrzés kimarad. Az IP-címek engedélyezési listája megtalálható az IP-címet a rendszer összehasonlítja a *ratNASIPAddress* attribútuma a RADIUS-kérést. Ha a RADIUS-kérést a ratNASIPAddress attribútum nélkül érhető el, a rendszer naplózza a következő figyelmeztetés: "P_WHITE_LIST_WARNING::IP engedélyezési lista van folyamatban figyelmen kívül hagyja, a forrás IP-cím nincs megadva a RADIUS-kérést NasIpAddress attribútumban."

## <a name="next-steps"></a>További lépések

[Hibaüzenetek által jelzett problémák megszüntetése az Azure Multi-Factor Authentication NPS-bővítményéből](howto-mfa-nps-extension-errors.md)
