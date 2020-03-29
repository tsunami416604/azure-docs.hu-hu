---
title: Jelszavas védelmi ügynök kiadási előzményei – Azure Active Directory
description: Dokumentumok verziókiadási és viselkedésváltozási előzményei
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71fd33388cb1bdf7c87c44fb3273c6850122a0cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74847849"
---
# <a name="azure-ad-password-protection-agent-version-history"></a>Az Azure AD password protection ügynök verzióelőzményei

## <a name="121250"></a>1.2.125.0

Megjelenési dátum: 3/22/2019

* Kisebb elírási hibák kijavítása az eseménynapló-üzenetekben
* A licenciaszerződés frissítése az általános rendelkezésre állás végleges verziójára

> [!NOTE]
> Build 1.2.125.0 az általános rendelkezésre állás ibuild. Még egyszer köszönöm mindenkinek, hogy visszajelzést adott a termékről!

## <a name="121160"></a>1.2.116.0

Megjelenési dátum: 3/13/2019

* A Get-AzureADPasswordProtectionProxy és a Get-AzureADPasswordProtectionDCAgent parancsmagok most antól jelentik a szoftververzióját és a jelenlegi Azure-bérlőt a következő korlátozásokkal:
  * A szoftververzió és az Azure-bérlői adatok csak az 1.2.116.0-s vagy újabb verziót futtató tartományvezérlő-ügynökök és proxyk számára érhetők el.
  * Az Azure-bérlői adatok nem jelenthetők, amíg a proxy vagy erdő újbóli regisztrálása (vagy megújítása) nem történt meg.
* A proxyszolgáltatás most már megköveteli a .NET 4.7 telepítését.
  * A .NET 4.7-et már telepíteni kell egy teljesen frissített Windows Server rendszerre. Ha nem ez a helyzet, töltse le és futtassa a [.NET Framework 4.7 offline telepítőben található telepítőt.](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows)
  * Server Core rendszereken szükség lehet a /q jelző átadására a .NET 4.7 telepítőnek a sikerhez.
* A proxyszolgáltatás mostantól támogatja az automatikus frissítést. Az automatikus frissítés a Microsoft Azure AD Connect Agent Updater szolgáltatást használja, amely a proxyszolgáltatással együtt van telepítve. Az automatikus frissítés alapértelmezés szerint be van kapcsolva.
* Az automatikus frissítés engedélyezhető vagy letiltható a Set-AzureADPasswordProtectionProxyConfiguration parancsmag használatával. Az aktuális beállítás lekérdezhető a Get-AzureADPasswordProtectionProxyConfiguration parancsmag használatával.
* A tartományvezérlő-ügynök szolgáltatás kiszolgálója átnevezve AzureADPasswordProtectionDCAgent.exe névre.
* A proxyszolgáltatás bináris fájlja átnevezve AzureADPasswordProtectionProxy.exe névre. Előfordulhat, hogy a tűzfalszabályokat ennek megfelelően módosítani kell, ha egy külső gyártótól származó tűzfal használatban van.
  * MEGJEGYZÉS: ha egy korábbi proxytelepítésben http-proxy konfigurációs fájlt használt, a frissítés után át kell nevezni *(proxyservice.exe.config-ról* *AzureADPasswordProtectionProxy.exe.config-ra).*
* Minden korlátozott ideig korlátozott funkcióellenőrzések el lettek távolítva a tartományvezérlő-ügynök.
* Kisebb hibák javítások és naplózási fejlesztések.

## <a name="12650"></a>1.2.65.0

Megjelenési dátum: 2/1/2019

Változások:

* A DC-ügynök és a proxyszolgáltatás mostantól támogatott a Server Core szolgáltatásban. A mininimum operációs rendszer követelményei nem változnak a korábhoz képest: Windows Server 2012 tartományvezérlő-ügynökökhöz és Windows Server 2012 R2 proxykhoz.
* A Register-AzureADPasswordProtectionProxy és register-AzureADPasswordProtectionForest parancsmagok mostantól támogatják az eszközkód-alapú Azure-hitelesítési módokat.
* A Get-AzureADPasswordProtectionDCAgent parancsmag figyelmen kívül hagyja a csonka és/vagy érvénytelen szolgáltatáscsatlakozási pontokat. Ez javítja a hibát, ahol a tartományvezérlők néha többször is megjelennek a kimenetben.
* A Get-AzureADPasswordProtectionSummaryReport parancsmag figyelmen kívül hagyja a csonka és/vagy érvénytelen szolgáltatáscsatlakozási pontokat. Ez javítja a hibát, ahol a tartományvezérlők néha többször is megjelennek a kimenetben.
* A Proxy powershell modul regisztrálva van a %ProgramFiles%\WindowsPowerShell\Modules mappából. A készülék PSModulePath környezeti változója már nem módosul.
* Egy új Get-AzureADPasswordProtectionProxy parancsmag lett hozzáadva a regisztrált proxyk felderítéséhez egy erdőben vagy tartományban.
* A tartományvezérlő-ügynök egy új mappát használ a sysvol megosztásban a jelszóházirendek és más fájlok replikálásához.

   Régi mappa helye:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Új mappa helye:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   (Ez a módosítás a hamis pozitív "árva gpo" figyelmeztetések elkerülése érdekében történt.)

   > [!NOTE]
   > A régi mappa és az új mappa között nem történik áttelepítés vagy adatmegosztás. A tartományvezérlő-ügynök régebbi verziói továbbra is a régi helyet fogják használni, amíg erre a verzióra vagy újabb verzióra nem frissítenek. Miután az összes tartományvezérlő-ügynök az 1.2.65.0-s vagy újabb verziót futtatta, a régi sysvol mappa manuálisan törölhető.

* A tartományvezérlő-ügynök és a proxyszolgáltatás mostantól észleli és törli a megfelelő szolgáltatáscsatlakozási pontok megcsonkított példányait.
* Minden tartományvezérlő-ügynök rendszeresen törli a tartományában lévő megcsonkított és elavult szolgáltatáscsatlakozási pontokat a tartományvezérlő-ügynök és a proxyszolgáltatás csatlakozási pontjai ra. A tartományvezérlő-ügynök és a proxyszolgáltatás csatlakozási pontjai is elavultnak számítanak, ha a szívverési időbélyeg hét napnál régebbi.
* A tartományvezérlő-ügynök most szükség szerint megújítja az erdőtanúsítványt.
* A proxyszolgáltatás mostantól szükség szerint megújítja a proxytanúsítványt.
* A jelszó-érvényesítési algoritmus frissítései: a globális tiltott jelszólista és az ügyfélspecifikus tiltott jelszólista (ha be van állítva) a jelszó-érvényesítés előtt egyesül. Egy adott jelszó most már elutasítható (sikertelen vagy csak naplózás), ha a globális és az ügyfélspecifikus listából származó jogkivonatokat tartalmaz. Az eseménynapló dokumentációja ennek megfelelően frissült; kérjük, olvassa el [az Azure AD jelszóvédelem figyelése című témakört.](howto-password-ban-bad-on-premises-monitor.md)
* Teljesítmény- és robusztussági javítások
* Továbbfejlesztett naplózás

> [!WARNING]
> Korlátozott idejű funkciók: a dc-ügynök szolgáltatás ebben a kiadásban (1.2.65.0) leállítja a jelszóérvényesítési kérelmek feldolgozását 2019.  2019. július 1-jétől leáll a feldolgozás a korábbi kiadásokban (lásd az alábbi listát). A tartományvezérlő-ügynök szolgáltatás az összes verzióban naplózza az 10021-es eseményeket a felügyeleti eseménynaplóba a határidőket megelőző két hónapban. Minden időkorlát korlátozás a következő GA kiadásban megszűnik. A Proxy ügynök szolgáltatás nem időben korlátozott bármely verzióban, de továbbra is frissíteni kell a legújabb verzióra annak érdekében, hogy kihasználják az összes későbbi hibajavítások és egyéb fejlesztések.

## <a name="12250"></a>1.2.25.0

Megjelenési dátum: 11/01/2018

Javítások:

* A tartományvezérlő-ügynök és a proxyszolgáltatás nem hibásodhat meg a tanúsítványmegbízhatósági hibák miatt.
* A DC-ügynök és a proxyszolgáltatás további javításokat is rendelkezik a FIPS-kompatibilis gépekhez.
* A proxyszolgáltatás mostantól megfelelően fog működni a csak TLS 1.2 hálózati környezetben.
* Kisebb teljesítmény- és robusztussági javítások
* Továbbfejlesztett naplózás

Változások:

* A proxyszolgáltatás minimálisan szükséges operációsrendszer-szintje mostantól Windows Server 2012 R2. A tartományvezérlő-ügynök szolgáltatás minimálisan szükséges operációsrendszer-szintje a Windows Server 2012 rendszerben marad.
* A proxyszolgáltatáshoz most .NET 4.6.2-es verzió szükséges.
* A jelszó-érvényesítési algoritmus egy bővített karakternormalizálási táblát használ. Ez azt eredményezheti, hogy a korábbi verziókban elfogadott jelszavakat elutasítják.

## <a name="12100"></a>1.2.10.0

Megjelenési dátum: 8/17/2018

Javítások:

* Register-AzureADPasswordProtectionProxy és Register-AzureADPasswordProtectionForest mostmár támogatja a többtényezős hitelesítést
* Register-AzureADPasswordProtectionProxy szükséges egy WS2012 vagy újabb tartományvezérlő a tartományban a titkosítási hibák elkerülése érdekében.
* A DC-ügynökszolgáltatás megbízhatóbb az azure-beli új jelszóházirend indításkor történő kérésével kapcsolatban.
* A DC-ügynökszolgáltatás szükség esetén óránként új jelszóházirendet kér az Azure-tól, de most egy véletlenszerűen kiválasztott kezdési időpontban.
* A dc-ügynökszolgáltatás a továbbiakban nem okoz határozatlan idejű késedelmet az új tartományvezérlő-hirdetésben, ha a kiszolgálóra van telepítve, mielőtt a replika előléptetése lenne.
* A DC-ügynökszolgáltatás mostantól tiszteletben tartja a "Jelszavas védelem engedélyezése a Windows Server Active Directoryrendszeren" konfigurációs beállítást
* A tartományvezérlő-ügynök és a proxytelepítők is támogatják a helybeni frissítést a későbbi verziókra való frissítéskor.

> [!WARNING]
> Az 1.1.10.3-as verzióról történő helybeni frissítés nem támogatott, és telepítési hibát eredményez. Az 1.2.10-es vagy újabb verzióra való frissítéshez először teljesen el kell távolítania a tartományvezérlő-ügynök és a proxyszolgáltatás szoftverét, majd telepítenie kell az új verziót a semmiből. Az Azure AD jelszavas védelem proxy szolgáltatás újra regisztrálása szükséges.  Nem szükséges újra regisztrálni az erdőt.

> [!NOTE]
> A tartományvezérlő-ügynök szoftverének helybeni frissítése újraindítást igényel.

* A tartományvezérlő-ügynök és a proxyszolgáltatás mostantól támogatja a csak FIPS-kompatibilis algoritmusok használatára konfigurált kiszolgálón való futtatást.
* Kisebb teljesítmény- és robusztussági javítások
* Továbbfejlesztett naplózás

## <a name="11103"></a>1.1.10.3

Megjelenési dátum: 6/15/2018

Első nyilvános előzetes kiadás

## <a name="next-steps"></a>További lépések

[Az Azure AD jelszavas védelmének telepítése](howto-password-ban-bad-on-premises-deploy.md)
