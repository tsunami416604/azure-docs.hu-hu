---
title: Hibaelhárítás az Azure AD jelszó protection előzetes verziója
description: Megismerheti az Azure AD jelszó védelmi előzetes gyakori hibáinak elhárítása
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
ms.openlocfilehash: 5727965373752d40e3ce508c1bc79046c2b3b70b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56177751"
---
# <a name="preview-azure-ad-password-protection-troubleshooting"></a>Előzetes verzió: Az Azure AD jelszóvédelem hibáinak elhárítása

|     |
| --- |
| Az Azure AD jelszóvédelem az Azure Active Directory nyilvános előzetes verziójú funkció. Előzetes verziók kapcsolatos további információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Az Azure AD jelszóvédelem üzembe helyezés után hibaelhárítási lehet szükség. Ez a cikk részletezik segítenek megérteni néhány gyakori hibaelhárítási lépéseket.

## <a name="weak-passwords-are-not-getting-rejected-as-expected"></a>A gyenge jelszavakat nem első visszautasítja, várt módon

Ennek több lehetséges oka lehet:

1. A tartományvezérlő ügynökök rendelkezik még nem töltötte le a szabályzatot. A tünete, ez a tartományvezérlő az ügynök felügyeleti eseménynaplójában 30001 események.

    A probléma lehetséges okai a következők:

    1. Erdő még nem regisztrált
    2. Proxy még nem regisztrált
    3. Hálózati problémák léptek fel megakadályozza, hogy a Proxy szolgáltatást az Azure-ral (Ellenőrizze a HTTP-Proxy követelmények) való kommunikációhoz

2. A jelszó a házirend-kényszerítési módban továbbra is be van állítva a naplózási. Ez a helyzet, ha újrakonfigurálása az Azure AD jelszóvédelem Portallal érvényesítése. Lásd: [engedélyezze a jelszavas védelem](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

3. A jelszóházirend le lett tiltva. Ha ez a helyzet, konfigurálja újra, hogy engedélyezve van, az Azure AD jelszóvédelem portal használatával. Lásd: [engedélyezze a jelszavas védelem](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

4. A jelszó adatérvényesítési algoritmust az elvárásoknak megfelelően működik is. Lásd: [hogyan jelszavak értékeli ki a](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="directory-services-repair-mode"></a>Címtárszolgáltatások helyreállító módjában

Ha a tartományvezérlő a Címtárszolgáltatások helyreállító módjában rendszer indult, a tartományvezérlő-ügynökszolgáltatás észleli ezt, és okozhat az összes jelszó érvényesítése vagy végrehajtó tevékenységek le kell tiltani, függetlenül a jelenleg aktív házirend-konfigurációt.

## <a name="emergency-remediation"></a>Sürgős szervizelés

Ha olyan helyzet akkor fordul elő, ha a tartományvezérlő-ügynökszolgáltatás problémákat okoz, a tartományvezérlő-ügynökszolgáltatás előfordulhat, hogy azonnal leáll. A tartományvezérlő ügynök jelszó szűrő dll továbbra is megpróbálja meghívni a nem futó szolgáltatás, és naplózza az figyelmeztetési eseményeket (10012, 10013), de ebben az időszakban minden bejövő jelszót fogad. A tartományvezérlő-ügynökszolgáltatás majd is konfigurálhatók keresztül a Windows szolgáltatásvezérlő indítási típusaként válassza a "Letiltott" igény szerint.

Szervizelési egy új mértéket az Enable-üzemmód beállítása az Azure AD jelszóvédelem portálon nem lehet. A frissített szabályzatot a letöltést követően minden egyes tartományvezérlő agent szolgáltatást egy videokártyának módban, ahol az összes jelszavak ügyfélként fogad el lépnek-van. További információkért lásd: [kényszerítési módban](howto-password-ban-bad-on-premises-operations.md#enforce-mode).

## <a name="domain-controller-demotion"></a>Tartományvezérlő lefokozása

Továbbra is fut a tartományvezérlő ügynökszoftver tartományvezérlő lefokozása támogatott. A rendszergazdák figyelembe kell azonban, hogy a tartományvezérlő ügynök szoftver továbbra is az aktuális jelszóházirend kényszerítése a lefokozási folyamat során. Az új helyi rendszergazdai fiók jelszava (a lefokozás művelet részeként megadott) érvényesítve lesz, mint bármely más jelszót. A Microsoft azt javasolja, hogy biztonságos jelszavakkal a helyi rendszergazdai fiókok választani egy tartományvezérlő lefokozási eljárást; részeként Új helyi rendszergazdai fiók jelszavának a tartományvezérlő ügynök szoftver érvényesítése azonban előre a lefokozás eljárások meglévő zavaró lehet.

A lefokozás sikeres volt, és a tartományvezérlő újraindítását, és újra normál kiszolgáló fut, a tartományvezérlő ügynökszoftver visszatér a passzív üzemmódban futó. Előfordulhat, hogy ezután távolítható bármikor.

## <a name="removal"></a>Eltávolítás

Ha úgy döntenek, hogy távolítsa el a nyilvános előzetes verziójú szoftver és karbantartása minden kapcsolódó állapota a tartomány és erdő, ez a feladat végezhető el az alábbi lépéseket követve:

> [!IMPORTANT]
> Fontos sorrendben hajtsa végre ezeket a lépéseket. Ha a Proxy szolgáltatás minden példányát futtató, rendszeresen újra létrehozza a serviceConnectionPoint objektum. Ha a tartományvezérlő-ügynökszolgáltatás bármelyik példányát futtató, rendszeresen újra létrehozza a serviceConnectionPoint objektum és a sysvol-állapot.

1. A Proxy szoftver eltávolítása a összes gép. Ebben a lépésben does **nem** újraindítása szükséges.
2. Távolítsa el a tartományvezérlő ügynökszoftver az összes tartományvezérlőről. Ebben a lépésben **igényel** újraindítás.
3. Manuálisan távolítsa el a Proxy összes szolgáltatáskapcsolati pontját, az egyes tartományi névhasználati környezeten. Ezek az objektumok helyét is felderítése a következő Active Directory PowerShell-paranccsal:

   ```PowerShell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Nem hagyja ki a csillag ("*") a $keywords változó értékét a végén.

   Az eredményül kapott objektumok található keresztül a `Get-ADObject` parancs majd átadható olyan parancsoknak `Remove-ADObject`, vagy manuálisan törölték. 

4. Manuálisan távolítsa el az összes tartományvezérlő ügynök csatlakozási pontok minden tartományi névhasználati környezetében. Lehet, hogy egy ezeket az objektumokat az erdő, attól függően, hogyan széles körben a nyilvános előzetes verziójú szoftver telepítve lett a tartományvezérlőnkénti. Előfordulhat, hogy felderítése a helyét annak az objektumnak a következő Active Directory PowerShell-paranccsal:

   ```PowerShell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Az eredményül kapott objektumok található keresztül a `Get-ADObject` parancs majd átadható olyan parancsoknak `Remove-ADObject`, vagy manuálisan törölték.

   Nem hagyja ki a csillag ("*") a $keywords változó értékét a végén.

5. Manuálisan távolítsa el az erdő szintű konfigurációs állapotát. Az erdő konfigurációs állapotát egy tárolót az Active Directory konfigurációelnevezési környezetében a változatlan marad. Ez felderíthető és törölni az alábbiak szerint:

   ```PowerShell
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
