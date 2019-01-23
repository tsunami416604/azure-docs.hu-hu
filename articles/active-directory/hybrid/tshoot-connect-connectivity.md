---
title: 'Azure AD Connect: Csatlakozási problémák elhárítása |} A Microsoft Docs'
description: Az Azure AD Connect kapcsolati problémáinak hibaelhárítását mutatja be.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 3aa41bb5-6fcb-49da-9747-e7a3bd780e64
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 85c60cf25cd00826df6b48ed6714a646fa44a962
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54474880"
---
# <a name="troubleshoot-connectivity-issues-with-azure-ad-connect"></a>Az Azure AD Connect kapcsolati problémáinak hibaelhárítása
Ez a cikk azt ismerteti, hogyan működik az Azure AD Connect és az Azure AD közötti kapcsolat és a kapcsolódási problémák elhárítása. Ezek olyan problémák, nagy valószínűséggel olyan környezetben, egy proxykiszolgáló láthatók legyenek.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>A telepítővarázsló a kapcsolati hibák elhárításához
Az Azure AD Connect (az ADAL-könyvtár használatával) a Modern hitelesítést használ a hitelesítéshez. A telepítési varázsló és a megfelelő szinkronizálási motor kell a machine.config megfelelően kell konfigurálni, mivel ez a két .NET-alkalmazások.

Ebben a cikkben bemutatjuk, hogyan csatlakozzon a Fabrikam az Azure AD a proxyn keresztül. A proxykiszolgáló neve fabrikamproxy, és a 8080-as portot használja.

Először győződjön meg arról, hogy kell [ **machine.config** ](how-to-connect-install-prerequisites.md#connectivity) megfelelően van konfigurálva.  
![machineconfig](./media/tshoot-connect-connectivity/machineconfig.png)

> [!NOTE]
> Az egyes-Microsoft blogok ismertetését, hogy a módosításokat inkább a miiserver.exe.config kell végezni. Azonban ezt a fájlt, felülírja a minden frissítés, még akkor is működik kezdeti telepítése alatt, ha a rendszer az első frissítés nem működik. Éppen ezért a javaslatot, hogy frissítse inkább a machine.config.
>
>

A proxykiszolgáló is rendelkeznie kell a szükséges URL-címek megnyitása. A hivatalos listában ismertetett [Office 365 URL-címei és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Ezen URL-címek a következő táblázat az csatlakozni az Azure AD minden, az abszolút legalább. Ez a lista nem tartalmaz minden választható funkciók, például a jelszóvisszaírás, vagy az Azure AD Connect Health. Azt fel van tüntetve Itt a kezdeti konfiguráció elhárításához.

| URL-cím | Port | Leírás |
| --- | --- | --- |
| mscrl.microsoft.com |HTTP/80 |Töltse le a Visszavonási listák segítségével. |
| \*.verisign.com |HTTP/80 |Töltse le a Visszavonási listák segítségével. |
| \*.entrust.net |HTTP/80 |Töltse le a Visszavonási listák a multi-factor Authentication segítségével. |
| \*.windows.net |HTTPS/443 |Jelentkezzen be az Azure AD segítségével. |
| secure.aadcdn.microsoftonline-p.com |HTTPS/443 |A multi-factor Authentication használja. |
| \*.microsoftonline.com |HTTPS/443 |Segítségével a konfigurálása az Azure AD-címtár és az adatok importálása és exportálása. |

## <a name="errors-in-the-wizard"></a>Hibák a varázsló
A telepítővarázsló használata két eltérő biztonsági környezetben. Az oldalon **az Azure AD Connect**, használja a jelenleg bejelentkezett felhasználót. Az oldalon **konfigurálása**, vált át a [a szinkronizálási motor a szolgáltatást futtató fiók](reference-connect-accounts-permissions.md#adsync-service-account). Ha probléma van, akkor jelenik meg valószínűleg már található a **az Azure AD Connect** a varázslóban, mivel a proxykonfiguráció globális lap.

A következő problémák a leggyakoribb hibákat tapasztal, a telepítési varázsló.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>A telepítővarázsló nincs megfelelően konfigurálva
Ez a hiba akkor jelenik meg, ha maga a varázsló nem tudja elérni a proxy.  
![nomachineconfig](./media/tshoot-connect-connectivity/nomachineconfig.png)

* Ha ezt a hibát látja, ellenőrizze a [machine.config](how-to-connect-install-prerequisites.md#connectivity) megfelelően van konfigurálva.
* Ha megfelelő néz ki, kövesse a [ellenőrizze a kapcsolatot. proxy](#verify-proxy-connectivity) megtekintheti, ha a probléma megtalálható-e a varázslót.

### <a name="a-microsoft-account-is-used"></a>Microsoft-fiók szolgál.
Ha egy **Microsoft-fiók** helyett egy **iskolai vagy a szervezet** fiók, egy általános hibaüzenetet.  
![Microsoft-Account szolgál.](./media/tshoot-connect-connectivity/unknownerror.png)

### <a name="the-mfa-endpoint-cannot-be-reached"></a>Az MFA-végpont nem érhető el
Ez a hiba akkor jelenik meg, ha a végpont **https://secure.aadcdn.microsoftonline-p.com** nem érhető el, és a globális rendszergazda engedélyezve van az MFA.  
![nomachineconfig](./media/tshoot-connect-connectivity/nomicrosoftonlinep.png)

* Ha ezt a hibát látja, ellenőrizze, hogy a végpont **secure.aadcdn.microsoftonline-p.com** hozzá van adva a proxy.

### <a name="the-password-cannot-be-verified"></a>A jelszó nem lehet ellenőrizni.
Ha a telepítővarázsló sikeres a Kapcsolódás az Azure ad-ben, de maga a jelszó nem ellenőrizhető, hogy ezt a hibaüzenetet:  
![badpassword](./media/tshoot-connect-connectivity/badpassword.png)

* Az a jelszó egy ideiglenes jelszót, és kötelező megváltoztatni? Ennyi az egész ténylegesen a helyes jelszót? Próbáljon meg bejelentkezni https://login.microsoftonline.com (egy másik számítógépre, mint az Azure AD Connect-kiszolgáló) és ellenőrizze, hogy a fiók használható.

### <a name="verify-proxy-connectivity"></a>Ellenőrizze a proxy-kapcsolatot
Győződjön meg arról, ha az Azure AD Connect-kiszolgáló rendelkezik-e a Proxy- és Internet tényleges kapcsolattal, tekintse meg, ha a proxykiszolgáló engedélyezi-e a webes kérelmeket, vagy nem egy PowerShell használatával. Egy PowerShell-parancssorban futtassa `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc`. (Az első hívás technikailag az, hogy https://login.microsoftonline.com és ezt az URI is működik, de más URI gyorsabban válaszolni.)

PowerShell-konfigurációt használja az a Machine.config fájlban a proxy kapcsolódni. A beállítások a winhttp/Netsh-ban nem érinti ezeket a parancsmagokat.

Ha a proxykiszolgáló megfelelően van konfigurálva, egy sikeres állapotnak kell kapnia: ![proxy200](./media/tshoot-connect-connectivity/invokewebrequest200.png)

Ha **nem lehet kapcsolódni a távoli kiszolgáló**, majd PowerShell próbál közvetlen hívás kezdeményezése a proxy használata nélkül, vagy a DNS nem megfelelően van konfigurálva. Győződjön meg arról, hogy a **machine.config** fájl helyesen van konfigurálva.
![unabletoconnect](./media/tshoot-connect-connectivity/invokewebrequestunable.png)

Ha a proxy nem megfelelően van konfigurálva, hibaüzenetet kap: ![proxy200](./media/tshoot-connect-connectivity/invokewebrequest403.png)
![proxy407](./media/tshoot-connect-connectivity/invokewebrequest407.png)

| Hiba | Hiba szövege | Megjegyzés |
| --- | --- | --- |
| 403 |Tiltott |A proxy nem lett megnyitva a kért URL-címéhez. Nyissa meg újra a proxykonfigurációt, és ellenőrizze, hogy a [URL-címek](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) meg vannak nyitva. |
| 407 |Proxy Authentication Required |A proxykiszolgáló-bejelentkezés szükséges, és nem. Ha a proxykiszolgáló hitelesítést igényel, győződjön meg róla, hogy ez a beállítás a Machine.config fájlban konfigurálva van. Ügyeljen arra, hogy a felhasználó a varázsló futtatása és a szolgáltatás fiók tartományi fiókokat használ. |

### <a name="proxy-idle-timeout-setting"></a>Üresjárati időkorlát proxybeállítása
Az Azure AD Connect exportálási kérelmet küld az Azure AD, ha az Azure AD választ létrehozása előtt a kérelem feldolgozását akár 5 percet is igénybe vehet. Ez akkor fordulhat elő, különösen akkor, ha egy csoport objektumok exportálása a kérésben szereplő nagy csoporttagsággal rendelkező számú. Ellenőrizze, a Proxy üresjárati időkorlát 5 perccel későbbinek kell lennie. Ellenkező esetben nem állandó hálózati kapcsolat a probléma az Azure ad-vel az Azure AD Connect-kiszolgálón lehet figyelni.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>A kommunikációs mintát között az Azure AD Connect és az Azure ad-ben
Ha követte a fenti lépéseket, és továbbra sem tud csatlakozni, akkor előfordulhat, hogy ezen a ponton elkezdhessük hálózati naplók. Ebben a szakaszban van dokumentálja a normál és a sikeres kapcsolat mintát. A közös red hering figyelmen kívül hagyható a hálózati napló olvasásakor listázása is van.

* Nincsenek hívásainak https://dc.services.visualstudio.com. Nem kötelező megadni a sikeres a telepítéshez a proxy URL-cím a megnyitási és hívásokat a figyelmen kívül hagyható.
* Láthatja, hogy a dns-feloldás a tényleges gazdagépek kell lennie a DNS-név terület nsatc.net és egyéb névterével microsoftonline.com alatt nem sorolja fel. Azonban nem áll semmilyen a webszolgáltatási kérelmeket a kiszolgáló tényleges nevét, és nem rendelkezik URL-címek hozzáadása a proxy.
* A végpontok adminwebservice provisioningapi felderítési végpontok és a tényleges végpont használata kereséséhez használt. Ezeket a végpontokat a régiójától függően eltérőek.

### <a name="reference-proxy-logs"></a>Hivatkozás proxy naplók
Íme egy memóriakép egy tényleges proxy naplóból származó és a telepítési varázsló lapja, ahol hibaállapota (egyazon végpont ismétlődő bejegyzések eltávolításra kerültek). Ebben a szakaszban egy saját proxy- és hálózati naplók referenciaként használható. A tényleges végpontok eltérhet a környezetben (különösen az URL-címeket a *dőlt*).

**Csatlakozás az Azure AD-hez**

| Time | URL-cím |
| --- | --- |
| 1/11/2016 8:31 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:31 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://*bba800-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://*bwsc02-relay*.microsoftonline.com:443 |

**Konfigurálás**

| Time | URL-cím |
| --- | --- |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://*bba800-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba900-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba800-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://*bwsc02-relay*.microsoftonline.com:443 |

**Kezdeti szinkronizálás**

| Time | URL-cím |
| --- | --- |
| 1/11/2016 8:48 |connect://login.windows.net:443 |
| 1/11/2016 8:49 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba900-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba800-anchor*.microsoftonline.com:443 |

## <a name="authentication-errors"></a>Hitelesítési hibák
Ez a szakasz ismerteti a hibákat, az adal-t (az Azure AD Connect által használt hitelesítési tár) és a PowerShell-lel adhatók vissza. A hiba azt segítenek a Ismerkedjen meg a következő lépésekkel.

### <a name="invalid-grant"></a>Érvénytelen engedélyezési
Érvénytelen felhasználónév vagy jelszó. További információkért lásd: [a jelszó nem lehet ellenőrizni](#the-password-cannot-be-verified).

### <a name="unknown-user-type"></a>Ismeretlen felhasználó típusa
Az Azure AD-címtár nem található vagy feloldása. Talán próbál jelentkezzen be egy felhasználónevet, a nem ellenőrzött tartományt?

### <a name="user-realm-discovery-failed"></a>Nem sikerült a felhasználó Kezdőtartomány-felderítés
Hálózati vagy a proxy konfigurációs problémákat. A hálózat nem érhető el. Lásd: [kapcsolati hibák elhárításához, a telepítési varázslóban](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### <a name="user-password-expired"></a>Felhasználó jelszava lejárt
A hitelesítő adatok lejártak. Módosítsa a jelszót.

### <a name="authorization-failure"></a>Engedélyezési hiba
Nem sikerült engedélyezni a felhasználót, hogy az Azure ad-ben a műveletre.

### <a name="authentication-cancelled"></a>Hitelesítési megszakítva
A többtényezős hitelesítés (MFA) kihívás meg lett szakítva.

### <a name="connect-to-ms-online-failed"></a>Az MS Online nem sikerült csatlakozni
A hitelesítés sikerült, de az Azure AD PowerShell rendelkezik egy hitelesítési probléma.

### <a name="azure-ad-global-admin-role-needed"></a>Az Azure AD globális rendszergazdai szerepkör szükséges
Felhasználó hitelesítése sikeresen megtörtént. Felhasználói azonban nincs hozzárendelt globális rendszergazdai szerepkört. Ez a [globális rendszergazdai szerepkör hozzárendelése](../users-groups-roles/directory-assign-admin-roles.md) a felhasználó számára. 

### <a name="privileged-identity-management-enabled"></a>Privileged Identity Management engedélyezve
A hitelesítés sikerült. Privileged identity management engedélyezve van, és jelenleg nem globális rendszergazda. További információkért lásd: [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md).

### <a name="company-information-unavailable"></a>Vállalati adatok nem érhető el
A hitelesítés sikerült. Nem sikerült beolvasni a vállalati adatokat az Azure ad-ből.

### <a name="domain-information-unavailable"></a>Tartományadatokat nem érhető el
A hitelesítés sikerült. Nem sikerült beolvasni a tartományadatokat az Azure ad-ből.

### <a name="unspecified-authentication-failure"></a>Nincs megadva hitelesítési hiba
A telepítési varázslóban nem várt hiba jelenik meg. Előfordulhat, ha megpróbálja használni egy **Microsoft Account** helyett egy **iskola vagy szervezeti fiókjával**.

## <a name="troubleshooting-steps-for-previous-releases"></a>Korábbi kiadások hibaelhárítási lépéseit.
A kiadásokban kezdve buildszám 1.1.105.0 (kiadás dátuma: 2016. február), a bejelentkezési segéd volt elavult. Ebben a szakaszban, és a konfiguráció már nem szükséges, de hivatkozásként van listázva marad.

A single-bejelentkezési segéd működjön a winhttp kell konfigurálni. Ez a konfiguráció végezheti [ **netsh**](how-to-connect-install-prerequisites.md#connectivity).  
![netsh](./media/tshoot-connect-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>A bejelentkezési segéd nincs megfelelően konfigurálva
Ez a hiba jelenik meg, ha a proxy nem érhető el a bejelentkezési segéd vagy a proxy nem engedélyezi a kérelmet.
![nonetsh](./media/tshoot-connect-connectivity/nonetsh.png)

* Ha ezt a hibát látja, tekintse meg a proxykiszolgáló-konfigurációt [netsh](how-to-connect-install-prerequisites.md#connectivity) és helyességének ellenőrzéséhez.
  ![netshshow](./media/tshoot-connect-connectivity/netshshow.png)
* Ha megfelelő néz ki, kövesse a [ellenőrizze a kapcsolatot. proxy](#verify-proxy-connectivity) megtekintheti, ha a probléma megtalálható-e a varázslót.

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
