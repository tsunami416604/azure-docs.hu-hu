---
title: A helyszíni Azure AD jelszó-védelmi ügynök verziókiadások – Azure Active Directory
description: Dokumentumok verzió kiadását és viselkedésének módosítási előzmények
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd1a96393f3dd9481705e3de8cc42375de7869e7
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311551"
---
# <a name="azure-ad-password-protection-agent-version-history"></a>Az Azure AD jelszóvédelem ügynök verzióelőzményei

## <a name="121160"></a>1.2.116.0

Kiadás dátuma: 3/13/2019

* A Get-AzureADPasswordProtectionProxy és Get-AzureADPasswordProtectionDCAgent parancsmagok most jelentést szoftver verziója és az aktuális Azure-bérlőben a következő korlátozások vonatkoznak:
  * Szoftver verziója és az Azure-bérlő adatait is csak DC ügynökök és a proxyk 1.2.116.0 verziójú vagy újabb.
  * Az Azure-bérlő adatai nem jelenthető a proxy Újraregisztrálás (vagy megújítás), vagy az erdő történt.
* A Proxy szolgáltatás megköveteli, hogy telepítve van-e a .NET 4.7.
  * .NET 4.7 egy teljesen új Windows Server már telepíthető. Ha nem ez a helyzet, töltse le és futtassa a telepítőt címen található [a .NET-keretrendszer 4.7 offline Windows installer](https://support.microsoft.com/en-us/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
  * A Server Core rendszereken, szükség lehet a /q jelző átadása a .NET 4.7 telepítő sikeres beszerzéséhez.
* A Proxy szolgáltatás már támogatja az automatikus frissítés. Automatikus frissítés a Microsoft Azure AD Connect az ügynök frissítési szolgáltatás, amelynek van telepített egymás mellett a Proxy szolgáltatással használja. Automatikus frissítés alapértelmezés szerint be van kapcsolva.
* Automatikus frissítés is engedélyezhető, vagy le van tiltva, a Set-AzureADPasswordProtectionProxyConfiguration parancsmag használatával. A jelenlegi beállítás a Get-AzureADPasswordProtectionProxyConfiguration parancsmaggal lehet lekérdezni.
* A szolgáltatás bináris a tartományvezérlő-ügynök szolgáltatás AzureADPasswordProtectionDCAgent.exe kapott.
* A szolgáltatás bináris a Proxy szolgáltatás AzureADPasswordProtectionProxy.exe kapott. Tűzfalszabályok szükség lehet ennek megfelelően módosítható, ha egy külső tűzfal használatban.
  * Megjegyzés: Ha egy http-proxy konfigurációs fájlban használt a korábbi proxykiszolgáló telepítse, át kell nevezni kell (a *proxyservice.exe.config* való *AzureADPasswordProtectionProxy.exe.config*) Ez után a frissítés.
* A tartományvezérlő ügynök ellenőrzésnek időben korlátozott funkciók el lettek távolítva.
* Javítottunk néhány apróbb hibát javításokat és a naplózás fejlesztései.

## <a name="12650"></a>1.2.65.0

Kiadás dátuma: 2/1/2019

Változások:

* DC-ügynök és a proxy szolgáltatás mostantól támogatottak Server Core-on. Minimális Verziójára vonatkozó követelményeknek mielőtt megismerttel: A Windows Server 2012 tartományvezérlő ügynökök és a Windows Server 2012 R2 a proxyk számára.
* A Register-AzureADPasswordProtectionProxy és a Register-AzureADPasswordProtectionForest parancsmagok mostantól támogatják az eszköz-kód-alapú Azure hitelesítési módot.
* A Get-AzureADPasswordProtectionDCAgent parancsmag összekeveredett és/vagy érvénytelen szolgáltatáscsatlakozási pontokat figyelmen kívül hagyja. Ez javítja a bejelentett hiba ahol tartományvezérlők néha jelennek meg többször a kimenetben.
* A Get-AzureADPasswordProtectionSummaryReport parancsmag összekeveredett és/vagy érvénytelen szolgáltatáscsatlakozási pontokat figyelmen kívül hagyja. Ez javítja a bejelentett hiba ahol tartományvezérlők néha jelennek meg többször a kimenetben.
* A Proxy powershell-modul % ProgramFiles%\WindowsPowerShell\Modules most már regisztrálva van. A gép PSModulePath környezeti változó már nem lehet módosítani.
* Új Get-AzureADPasswordProtectionProxy parancsmag bővült, ezzel elősegítve az egy erdőben vagy tartományban regisztrált proxyk felderítése.
* A tartományvezérlő-ügynök egy új mappát a sysvol-megosztás replikálásához jelszóházirendek és egyéb fájlokat használja.

   Régi mappájának helye:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Új mappa helye:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   (A módosítás elkerülése érdekében a hamis pozitív "árva csoportházirend-objektum" figyelmeztetés.)

   > [!NOTE]
   > Nincs áttelepítésével vagy az adatok megosztását a régi és az új mappát között történik. DC-ügynök korábbi verzióival továbbra is a régi helyet, amíg nem frissítik őket, ez a verzió vagy újabb. Miután az összes tartományvezérlő ügynökök 1.2.65.0 verziót futtat, vagy később, a régi sysvol mappa előfordulhat, hogy kézzel kell törölni.

* A DC ügynök és a proxy szolgáltatás mostantól észleli és törli a megfelelő szolgáltatási csatlakozási pontok összekeveredett példányait.
* Minden tartományvezérlő ügynök rendszeres időközönként törli összekeveredett és elavult szolgáltatáskapcsolódási pontjait a tartományban, a mindkét DC-ügynök és a proxy szolgáltatáskapcsolati pontját. Mindkét DC-ügynök és a proxy szolgáltatáskapcsolódási pontjait minősülnek elavulttá, ha a szívverés időbélyeg: 7 napnál régebbi.
* A tartományvezérlő ügynök most fogja a erdő tanúsítvány megújításához, igény szerint.
* A Proxy szolgáltatást most fogja a proxy tanúsítvány megújítása, igény szerint.
* Jelszó-ellenőrzési algoritmus frissítései: a globális letiltott jelszavak és (Ha be van állítva) ügyfél-specifikus letiltott jelszavak listában mostantól jelszó ellenőrzés előtt. Előfordulhat, hogy most már elutasítja a megadott jelszó (sikertelen vagy csak naplózási) Ha a globális és ügyfél-specifikus listában tokeneket tartalmaz. Az Eseménynapló dokumentáció; ennek megfelelően frissítve lett Lásd: [figyelő Azure AD jelszóvédelem](howto-password-ban-bad-on-premises-monitor.md).
* Teljesítmény- és háttértárat javításai
* A továbbfejlesztett naplózás

> [!WARNING]
> Időben korlátozott funkciók: a tartományvezérlő-ügynökszolgáltatás ebben a kiadásban (1.2.65.0) le fog állni. szeptember 1-től a 2019-tól jelszó ellenőrzési kérések feldolgozásáért.  DC-ügynök szolgáltatást a korábbi verziókban feldolgozása. július 1-től a 2019-től (lásd alább) leáll. A tartományvezérlő vendégügynök-szolgáltatását az összes verzió naplózza az 10021 eseményeket a rendszergazda eseménynaplójába a fenti határidők vezető két hónapon belülre. Az összes határidőn korlátozást megszüntet a soron következő kiadásban általánosan elérhető. A Proxy agent szolgáltatás nem időkorlátos bármelyik verziója, de továbbra is kell frissíteni a legújabb verziót az összes későbbi hibajavításokat tartalmaz, és az egyéb fejlesztések kihasználása érdekében.

## <a name="12250"></a>1.2.25.0

Kiadás dátuma: 11/01/2018

Javításokat tartalmaz:

* DC-ügynök és a proxy szolgáltatás már nem kell sikertelen a tanúsítvány megbízhatósági hibák miatt.
* DC-ügynök és a proxy szolgáltatás rendelkezik a FIPS előírásainak megfelelő gépek további javításokra.
* Proxy szolgáltatás mostantól fog megfelelően működni a TLS 1.2-es csak hálózati környezetben.
* Kisebb teljesítményt és háttértárat javításai
* A továbbfejlesztett naplózás

Változások:

* A minimálisan szükséges operációs rendszer szintjén a Proxy szolgáltatás most már a Windows Server 2012 R2. A tartományvezérlő-ügynök szolgáltatás minimálisan szükséges operációs rendszer szintjén, a Windows Server 2012 marad.
* A Proxy szolgáltatás mostantól csak a .NET 4.6.2-es verziójára.
* A jelszó adatérvényesítési algoritmust egy kibontott karakter normalizálási táblát használja. Emiatt előfordulhat, hogy a korábbi verziók elfogadott el lettek utasítva, jelszavakat.

## <a name="12100"></a>1.2.10.0

Kiadás dátuma: 8/17/2018

Javításokat tartalmaz:

* Register-AzureADPasswordProtectionProxy és a Register-AzureADPasswordProtectionForest mostantól támogatja a többtényezős hitelesítés
* Register-AzureADPasswordProtectionProxy WS2012 vagy újabb tartományvezérlő a tartományban titkosítási hibák elkerülése érdekében igényel.
* DC-ügynökszolgáltatás megbízhatóbb kapcsolatos új jelszóházirend az Azure-ból az indításakor.
* DC-ügynökszolgáltatás kérelmezi egy új jelszóházirend az Azure-ból minden órában, ha szükséges, de fog most ehhez egy véletlenszerűen kiválasztott kezdési időpontban.
* DC-ügynök szolgáltatás már nem az új tartományvezérlő hirdetményt replikaként az előléptetés előtt egy kiszolgálón telepítve egy befejezésére való határozatlan idejű késedelem miatt.
* DC-ügynökszolgáltatás most tartsa tiszteletben a "A Windows Server Active Directory jelszavas védelem engedélyezése" konfigurációs beállítás
* Mindkét DC-ügynök és a proxy telepítők mostantól támogatja a helyben frissítés verzióra való későbbi verziókban.

> [!WARNING]
> 1.1.10.3 verzióból a helybeni frissítést nem támogatott, és a egy telepítési hibát eredményez. A frissítési verzióra 1.2.10 vagy újabb, kell először teljesen távolítsa el a tartományvezérlő-ügynök és a proxy szolgáltatás szoftvert, majd teljesen új az új verzió telepítése. Az Azure AD jelszóvédelem proxyszolgáltatás Újraregisztrálás megadása kötelező.  Regisztrálja újra az erdő nem szükséges.

> [!NOTE]
> Helyben végzett frissítések DC ügynökszoftver követően újra kell indítani.

* DC-ügynök és a proxy szolgáltatás mostantól támogatja a csak a FIPS előírásainak megfelelő algoritmusok használatára konfigurált kiszolgálón futó.
* Kisebb teljesítményt és háttértárat javításai
* A továbbfejlesztett naplózás

## <a name="11103"></a>1.1.10.3

Kiadás dátuma: 6/15/2018

Kezdeti nyilvános előzetes kiadás

## <a name="next-steps"></a>További lépések

[Üzembe helyezése az Azure AD jelszóvédelem](howto-password-ban-bad-on-premises-deploy.md)
