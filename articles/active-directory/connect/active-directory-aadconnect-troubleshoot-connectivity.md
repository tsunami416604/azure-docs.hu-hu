---
title: 'Az Azure AD Connect: Csatlakozási problémák elhárítása |} Microsoft Docs'
description: Ismerteti az Azure AD Connect csatlakozási problémák.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 3aa41bb5-6fcb-49da-9747-e7a3bd780e64
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 2c12630deb45fd057537c42157d88fdeef22d18b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593013"
---
# <a name="troubleshoot-connectivity-issues-with-azure-ad-connect"></a>Az Azure AD Connect csatlakozási problémák
Ez a cikk ismerteti az Azure AD Connect és az Azure AD közötti kapcsolat megfelelő működésének és a problémák elhárításáról. A problémát valószínűleg a proxykiszolgálóval környezetben kell vizsgálni.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>A telepítővarázslóban csatlakozási problémák
Az Azure AD Connect (az ADAL könyvtárat használja) Modern hitelesítést használ a hitelesítéshez. A telepítési varázsló és a megfelelő szinkronizálási motor kell a machine.config megfelelően kell konfigurálni, mivel ez a két .NET-alkalmazások.

Ebben a cikkben megmutatjuk, hogyan csatlakozzon a Fabrikam az Azure AD a proxyn keresztül. A proxykiszolgáló neve fabrikamproxy, és a 8080-as portot használja.

Először meg kell győződnünk arról [ **machine.config** ](active-directory-aadconnect-prerequisites.md#connectivity) megfelelően van beállítva.  
![machineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/machineconfig.png)

> [!NOTE]
> Az egyes nem a Microsofttól blogok ismerteti, hogy módosítani kell a miiserver.exe.config helyette. Azonban ez a fájl nem minden frissítés, még akkor is működik kezdeti telepítése alatt, ha a rendszer nem működik az első frissítés felülírja. Éppen ezért a ajánljuk, hogy a machine.config sorkészletének frissítéséhez.
>
>

A proxykiszolgáló is rendelkeznie kell a szükséges URL-címek megnyitása. A hivatalos lista részletes ismertetését lásd: [Office 365 URL-címei és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

URL-címek a következő táblázat a abszolút operációs rendszer minimálisan csatlakozni az Azure AD minden. Ez a lista nem tartalmaz semmilyen választható funkciók, például a jelszóvisszaírás, vagy az Azure AD Connect Health. Azt az itt dokumentált lehetőségektől a kezdeti konfiguráció elhárításához.

| URL-cím | Port | Leírás |
| --- | --- | --- |
| mscrl.microsoft.com |A HTTP/80 |Töltse le a Visszavonási listák segítségével. |
| \*.verisign.com |A HTTP/80 |Töltse le a Visszavonási listák segítségével. |
| \*. entrust.com |A HTTP/80 |Töltse le a Visszavonási listák a multi-factor Authentication használatával. |
| \*.windows.net |HTTPS/443 |Az Azure AD-bejelentkezéshez használt. |
| secure.aadcdn.microsoftonline-p.com |HTTPS/443 |Használja az MFA szolgáltatásra. |
| \*.microsoftonline.com |HTTPS/443 |Konfigurálja az Azure AD-címtár és az importálási/exportálási adatok szolgál. |

## <a name="errors-in-the-wizard"></a>A varázsló hibák
A varázsló által használt két eltérő biztonsági környezetben. Az oldalon **az Azure AD Connect**, akkor a jelenleg bejelentkezett felhasználó nevében használ. Az oldalon **konfigurálása**, vált át a [a szinkronizálási motor szolgáltatást futtató fiók](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account). Ha probléma van, úgy tűnik, már valószínűleg a **az Azure AD Connect** , a varázsló, mivel a proxy konfigurációs globális lapján.

Az alábbi problémák az telepítővarázsló előforduló leggyakoribb hibák.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>A telepítővarázsló nincs megfelelően konfigurálva
Ez a hiba akkor jelenik meg, amikor maga a varázsló nem tudja elérni a proxy.  
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomachineconfig.png)

* Ha ezt a hibaüzenetet látja, ellenőrizze a [machine.config](active-directory-aadconnect-prerequisites.md#connectivity) megfelelően van konfigurálva.
* Ha meg, hogy helyes-e, kövesse a lépéseket [ellenőrizze a proxy kapcsolatát](#verify-proxy-connectivity) megjelenítéséhez, ha a probléma a varázslót.

### <a name="a-microsoft-account-is-used"></a>A Microsoft-fiók használata
Ha egy **Microsoft-fiók** helyett egy **iskolai vagy a szervezet** fiók, megjelenik egy általános hiba.  
![A Microsoft Account történik](./media/active-directory-aadconnect-troubleshoot-connectivity/unknownerror.png)

### <a name="the-mfa-endpoint-cannot-be-reached"></a>A többtényezős hitelesítés nem sikerült elérni
Ez a hiba akkor jelenik meg, ha a végpont **https://secure.aadcdn.microsoftonline-p.com** nem érhető el, és a globális rendszergazda engedélyezve van az MFA.  
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomicrosoftonlinep.png)

* Ha megjelenik ez a hiba, ellenőrizze, hogy a végpont **secure.aadcdn.microsoftonline-p.com** a proxy hozzá lett adva.

### <a name="the-password-cannot-be-verified"></a>A jelszó nem lehet ellenőrizni.
Ha a telepítési varázsló az Azure AD-csatlakozás sikeres, de maga a jelszó nem ellenőrizhető, hogy ezt a hibaüzenetet látja:  
![badpassword](./media/active-directory-aadconnect-troubleshoot-connectivity/badpassword.png)

* A jelszó ideiglenes jelszót és meg kell változtatni? Ennyi az egész ténylegesen a helyes jelszót? Próbáljon újból bejelentkezni https://login.microsoftonline.com (azon a számítógépen egy másik az Azure AD Connect-kiszolgáló), és ellenőrizze, hogy a fiók nem használható.

### <a name="verify-proxy-connectivity"></a>Proxy-kapcsolat ellenőrzése
Győződjön meg arról, hogy az Azure AD Connect-kiszolgáló rendelkezik-e a Proxy- és Internet tényleges kapcsolattal, tekintse meg, ha a proxy engedélyezi, hogy a webes kérések vagy nem egy PowerShell segítségével. A PowerShell-parancssorból, futtassa az `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc`. (Technikailag az első hívás, hogy https://login.microsoftonline.com és ezt az URI is működik, de a többi URI gyorsabban válaszolni.)

PowerShell Machine.config fájlban a konfigurációt használja a proxy kapcsolódni. A beállítások, a winhttp/netsh kell befolyásolja a parancsmagok használatával.

Ha a proxy megfelelően van beállítva, szerezheti be egy sikeres állapotnak: ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest200.png)

Ha **nem lehet kapcsolódni a távoli kiszolgáló**, majd PowerShell próbál közvetlen hívás kezdeményezése a proxy használata nélkül, vagy a DNS nem megfelelően van beállítva. Győződjön meg arról, hogy a **machine.config** fájl megfelelően van beállítva.
![unabletoconnect](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequestunable.png)

Ha a proxy nem megfelelően van beállítva, hibaüzenetet kap: ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest403.png)
![proxy407](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest407.png)

| Hiba | Hiba szövege | Megjegyzés |
| --- | --- | --- |
| 403 |Tiltott |A proxy nem lett megnyitva a kért URL-címhez. A proxykonfiguráció le újra, és győződjön meg arról, hogy a [URL-címek](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) lett megnyitva. |
| 407 |Proxyhitelesítés szükséges |A proxykiszolgáló szükséges, a bejelentkezés, és nincs megadva. Ha a proxykiszolgálóhoz hitelesítés szükséges, biztosítsa, hogy ez a beállítás a Machine.config fájlban konfigurálva legyen-e. Győződjön meg arról is tartományi fiókokat használ a felhasználó, a varázsló futtatása és a szolgáltatási fiókhoz. |

### <a name="proxy-idle-timeout-setting"></a>Üresjárati időtúllépés proxybeállítása
Az Azure AD Connect exportálási kérelmet küld az Azure AD, ha az Azure AD választ létrehozása előtt a kérelem feldolgozása akár 5 percet is igénybe vehet. Ez akkor fordulhat elő, különösen akkor, ha az Exportálás kérésben szereplő nagy csoporttagsággal rendelkező objektumok több. Ügyeljen arra a Proxy időtúllépést 5 perccel későbbinek kell lennie. Ellenkező esetben átmeneti hálózati probléma az Azure AD meg az Azure AD Connect-kiszolgáló lehet figyelni.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>Az Azure AD Connect és az Azure AD közötti kommunikáció minta
Ha követte a fenti lépéseket, és továbbra sem sikerül kapcsolódni, akkor előfordulhat, hogy ezen a ponton start hálózati naplók megnézi. Ez a szakasz a normál és a sikeres kapcsolat minta van dokumentálása. Azt a közös piros hering figyelmen kívül hagyható a hálózati naplók olvasásakor listázása is van.

* Nincsenek a hívások https://dc.services.visualstudio.com. Nem kell az URL-címet a megnyitási engedélyek a proxy a telepítés sikeres, az adott hívások figyelmen kívül hagyható.
* Láthatja, hogy a DNS-feloldás sorolja fel a tényleges állomások a DNS-név terület nsatc.net és egyéb névterek nem microsoftonline.com alatt találhatók. Azonban nincsenek a webszolgáltatási kérelmeket a kiszolgáló tényleges nevét, és nem kell az alábbi URL-címek hozzáadása a proxy.
* A végpontok adminwebservice provisioningapi felderítési végpontok és használandó tényleges végpontja kereséséhez használt. Ezeket a végpontokat a régiójától függően eltérőek.

### <a name="reference-proxy-logs"></a>Hivatkozás proxy naplók
Ez egy biztonsági másolat egy tényleges proxy naplóból és a telepítési varázsló oldal ahol készült (az azonos végponthoz ismétlődő bejegyzések eltávolítva). Ez a szakasz a saját proxy- és hálózati naplók referenciaként használható. A tényleges végpontok eltérhet a környezetben (különösen az URL-címeket a *dőlt*).

**Csatlakozás az Azure AD-hez**

| Time | URL-cím |
| --- | --- |
| 1/11/2016 8:31 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:31 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:32 |Csatlakozás: / /*bba800-rögzítési*. microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://*bwsc02-relay*.microsoftonline.com:443 |

**Konfigurálás**

| Time | URL-cím |
| --- | --- |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:43 |Csatlakozás: / /*bba800-rögzítési*. microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |Csatlakozás: / /*bba900-rögzítési*. microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |Csatlakozás: / /*bba800-rögzítési*. microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://*bwsc02-relay*.microsoftonline.com:443 |

**Kezdeti szinkronizálás**

| Time | URL-cím |
| --- | --- |
| 1/11/2016 8:48 |connect://login.windows.net:443 |
| 1/11/2016 8:49 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:49 |Csatlakozás: / /*bba900-rögzítési*. microsoftonline.com:443 |
| 1/11/2016 8:49 |Csatlakozás: / /*bba800-rögzítési*. microsoftonline.com:443 |

## <a name="authentication-errors"></a>Hitelesítési hibák
Ez a fejezet az adal-t (az Azure AD Connect által használt hitelesítési tár) és PowerShell visszaadható hibák. A hiba azt kell megismerheti a a következő lépéseket.

### <a name="invalid-grant"></a>Érvénytelen támogatás
Érvénytelen felhasználónév vagy jelszó. További információkért lásd: [a jelszó nem lehet ellenőrizni](#the-password-cannot-be-verified).

### <a name="unknown-user-type"></a>Ismeretlen felhasználó típusa
Az Azure AD-címtár nem található vagy nem megoldott. Lehet, hogy próbál egy felhasználónévvel nem ellenőrzött tartományt a bejelentkezéshez?

### <a name="user-realm-discovery-failed"></a>Nem sikerült felhasználói feltárásának
Hálózati vagy a proxy konfigurációs problémák. A hálózat nem érhető el. Lásd: [csatlakozási problémák a telepítővarázslóban](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### <a name="user-password-expired"></a>A felhasználói jelszó lejárt
A hitelesítő adatok lejártak. Módosítsa a jelszavát.

### <a name="authorizationfailure"></a>AuthorizationFailure
Ismeretlen hiba.

### <a name="authentication-cancelled"></a>Hitelesítési megszakítva
A többtényezős hitelesítés (MFA) feladat meg lett szakítva.

### <a name="connecttomsonline"></a>ConnectToMSOnline
A hitelesítés sikerült, de az Azure AD PowerShell rendelkezik egy hitelesítési probléma.

### <a name="azurerolemissing"></a>AzureRoleMissing
Hitelesítés sikeres volt. A globális rendszergazda tartoznak.

### <a name="privilegedidentitymanagement"></a>PrivilegedIdentityManagement
Hitelesítés sikeres volt. Privileged identity management engedélyezve van, és jelenleg nincs a globális rendszergazdája. További információkért lásd: [Privileged Identity Management](../active-directory-privileged-identity-management-getting-started.md).

### <a name="companyinfounavailable"></a>CompanyInfoUnavailable
Hitelesítés sikeres volt. Nem tudta beolvasni a vállalat adatait az Azure AD.

### <a name="retrievedomains"></a>RetrieveDomains
Hitelesítés sikeres volt. Nem sikerült lekérdezni az adatokat az Azure AD.

### <a name="unexpected-exception"></a>Váratlan kivétel
Nem várt hiba történt a telepítési varázsló jelenik meg. Akkor fordulhat elő, ha próbálja használni a **Microsoft Account** helyett egy **iskolai vagy a szervezeti fiók**.

## <a name="troubleshooting-steps-for-previous-releases"></a>Hibaelhárítási lépéseket a korábbi kiadásokban.
Kiadásainak kezdve buildszáma 1.1.105.0 (dátuma: 2016. februári), a bejelentkezési segéd lett visszavonva. Ez a szakasz és a konfiguráció már nem szükséges, de hivatkozásként maradnak.

A single-bejelentkezési segéd működjön a winhttp kell konfigurálni. Ez a konfiguráció nem végezhető [ **netsh**](active-directory-aadconnect-prerequisites.md#connectivity).  
![netsh](./media/active-directory-aadconnect-troubleshoot-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>A bejelentkezési segéd nincs megfelelően konfigurálva
Ez a hiba akkor jelenik meg, amikor a bejelentkezési segéd nem érhető el a proxy vagy a proxykiszolgáló nem engedélyezi, hogy a kérelmet.
![nonetsh](./media/active-directory-aadconnect-troubleshoot-connectivity/nonetsh.png)

* Ha ezt a hibaüzenetet látja, nézze meg a proxy konfigurációját, a [netsh](active-directory-aadconnect-prerequisites.md#connectivity) , és ellenőrizze, helyes.
  ![netshshow](./media/active-directory-aadconnect-troubleshoot-connectivity/netshshow.png)
* Ha meg, hogy helyes-e, kövesse a lépéseket [ellenőrizze a proxy kapcsolatát](#verify-proxy-connectivity) megjelenítéséhez, ha a probléma a varázslót.

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).
