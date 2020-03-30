---
title: 'Azure AD Connect: Az Azure AD kapcsolódási problémáinak elhárítása | Microsoft dokumentumok'
description: A cikk ből megtudhatja, hogyan háríthatja el az Azure AD Connect kapcsolódási problémáit.
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
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72dbb404d1b4d3618909e0233f332d2f98b51516
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049733"
---
# <a name="troubleshoot-azure-ad-connectivity"></a>Az Azure AD-kapcsolat – problémamegoldás
Ez a cikk bemutatja, hogyan működik az Azure AD Connect és az Azure AD közötti kapcsolat, és hogyan hárítható el a kapcsolódási problémák. Ezek a problémák a legvalószínűbb, hogy egy proxykiszolgálóval rendelkező környezetben láthatók.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>Kapcsolódási problémák elhárítása a telepítővarázslóban
Az Azure AD Connect modern hitelesítést használ (az ADAL-kódtár használatával) a hitelesítéshez. A telepítővarázsló és a megfelelő szinkronizálási motor megköveteli a machine.config megfelelő konfigurálását, mivel ez a kettő .NET alkalmazás.

Ebben a cikkben bemutatjuk, hogyan Fabrikam csatlakozik az Azure AD-hez a proxyn keresztül. A proxykiszolgáló neve fabrikamproxy, és a 8080-as portot használja.

Először meg kell győződnie [**arról, machine.config**](how-to-connect-install-prerequisites.md#connectivity) megfelelően van konfigurálva.  
![gépconfig](./media/tshoot-connect-connectivity/machineconfig.png)

> [!NOTE]
> Egyes nem microsoftos blogokban dokumentált, hogy a miiserver.exe.config-on kell módosítani. Azonban ez a fájl felülírja minden frissítés, így még akkor is, ha működik a kezdeti telepítés során, a rendszer nem működik az első frissítés. Emiatt a javaslat a machine.config frissítése.
>
>

A proxykiszolgálónak meg kell nyitnia a szükséges URL-címeket is. A hivatalos listát az [Office 365 URL-címei és IP-címtartományai dokumentálják.](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)

Ezek közül az URL-címek közül az alábbi táblázat az abszolút minimális, hogy egyáltalán csatlakozni lehessen az Azure AD-hez. Ez a lista nem tartalmaz olyan választható funkciókat, például a jelszó-visszaírást vagy az Azure AD Connect Health szolgáltatást. Itt van dokumentálva, hogy segítsen a kezdeti konfiguráció hibaelhárításában.

| URL-cím | Port | Leírás |
| --- | --- | --- |
| mscrl.microsoft.com |HTTP/80 |A visszavont tanúsítványok listájának letöltésére szolgál. |
| \*verisign.com |HTTP/80 |A visszavont tanúsítványok listájának letöltésére szolgál. |
| \*entrust.net |HTTP/80 |A visszavont tanúsítványok listájának letöltésére szolgál az MFA-hoz. |
| \*.windows.net |HTTPS/443 |Az Azure AD-be való bejelentkezéshez használható. |
| secure.aadcdn.microsoftonline-p.com |HTTPS/443 |Az MFA-hoz használható. |
| \*.microsoftonline.com |HTTPS/443 |Az Azure AD-címtár konfigurálására és az adatok importálására/exportálására szolgál. |

## <a name="errors-in-the-wizard"></a>Hibák a varázslóban
A telepítővarázsló két különböző biztonsági környezetet használ. Az **Azure AD-hez való csatlakozás**lapon az aktuálisan bejelentkezett felhasználót használja. A **Konfigurálás**lapon a [szinkronizálási motor szolgáltatását futtató fiókra](reference-connect-accounts-permissions.md#adsync-service-account)vált. Ha probléma merül fel, valószínűleg már a varázsló **Connect to Azure AD** lapján jelenik meg, mivel a proxykonfiguráció globális.

A telepítővarázslóban leggyakrabban előforduló hibák a következő problémák.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>A telepítővarázsló nincs megfelelően konfigurálva
Ez a hiba akkor jelenik meg, ha maga a varázsló nem tudja elérni a proxyt.  
![nomachineconfig között](./media/tshoot-connect-connectivity/nomachineconfig.png)

* Ha ez a hiba jelenik meg, ellenőrizze, hogy a [machine.config](how-to-connect-install-prerequisites.md#connectivity) megfelelően van-e konfigurálva.
* Ha ez helyesnek tűnik, kövesse a [Proxy-kapcsolat ellenőrzése](#verify-proxy-connectivity) című témakör lépéseit, és ellenőrizze, hogy a probléma a varázslón kívül is fennáll-e.

### <a name="a-microsoft-account-is-used"></a>Microsoft-fiók használatban van
Ha **iskolai vagy szervezeti** fiók helyett **Microsoft-fiókot** használ, általános hibaüzenet jelenik meg.  
![Microsoft-fiók használatban van](./media/tshoot-connect-connectivity/unknownerror.png)

### <a name="the-mfa-endpoint-cannot-be-reached"></a>Az MFA-végpont nem érhető el
Ez a hiba akkor **https://secure.aadcdn.microsoftonline-p.com** jelenik meg, ha a végpont nem érhető el, és a globális rendszergazda engedélyezte az MFA-t.  
![nomachineconfig között](./media/tshoot-connect-connectivity/nomicrosoftonlinep.png)

* Ha ez a hiba jelenik meg, ellenőrizze, hogy a végpont **secure.aadcdn.microsoftonline-p.com** hozzá lett-e adva a proxyhoz.

### <a name="the-password-cannot-be-verified"></a>A jelszó nem ellenőrizhető
Ha a telepítővarázsló sikeresen csatlakozik az Azure AD-hez, de maga a jelszó nem ellenőrizhető, akkor a következő hibaüzenet jelenik meg:  
![Rossz jelszó.](./media/tshoot-connect-connectivity/badpassword.png)

* A jelszó ideiglenes jelszó, és meg kell változtatni? Ez valójában a helyes jelszót? Próbáljon meg `https://login.microsoftonline.com` bejelentkezni (az Azure AD Connect-kiszolgálótól eltérő számítógépen), és ellenőrizze, hogy a fiók használható-e.

### <a name="verify-proxy-connectivity"></a>Proxykapcsolat ellenőrzése
Annak ellenőrzéséhez, hogy az Azure AD Connect-kiszolgáló rendelkezik-e tényleges kapcsolattal a proxyval és az internettel, használjon néhány PowerShellt, és ellenőrizze, hogy a proxy engedélyezi-e a webes kérelmeket, vagy sem. A PowerShell-parancssorban futtassa a futtassa a parancs. `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc` (Technikailag az első hívás, `https://login.microsoftonline.com` és ez az URI is működik, de a másik URI gyorsabb válaszolni.)

A PowerShell a machine.config konfigurációja segítségével lépjen kapcsolatba a proxyval. A winhttp/netsh beállításai nem befolyásolhatják ezeket a parancsmagokat.

Ha a proxy megfelelően van konfigurálva, ![sikeres állapotot kell kapnia: proxy200](./media/tshoot-connect-connectivity/invokewebrequest200.png)

Ha a **nem lehet csatlakozni a távoli kiszolgálóhoz,** akkor a PowerShell megpróbál közvetlen hívást kezdeményezni a proxy használata nélkül, vagy a DNS nincs megfelelően konfigurálva. Ellenőrizze, hogy a **machine.config** fájl megfelelően van-e konfigurálva.
![nem lehet csatlakozni](./media/tshoot-connect-connectivity/invokewebrequestunable.png)

Ha a proxy nincs megfelelően konfigurálva, ![hibaüzenetet](./media/tshoot-connect-connectivity/invokewebrequest403.png)
![kap: proxy200 proxy407](./media/tshoot-connect-connectivity/invokewebrequest407.png)

| Hiba | Hiba szöveg | Megjegyzés |
| --- | --- | --- |
| 403 |Forbidden |A proxy nincs megnyitva a kért URL-címhez. Vizsgálja felül a proxykonfigurációt, és győződjön meg arról, hogy az [URL-címek](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) meg vannak nyitva. |
| 407 |Proxyhitelesítés szükséges |A proxykiszolgálóbejelentkezést igényelt, de egyik sem volt megadva. Ha a proxykiszolgáló hitelesítést igényel, győződjön meg arról, hogy ez a beállítás konfigurálva van a machine.config fájlban. Győződjön meg arról is, hogy a varázslót futtató felhasználó és a szolgáltatásfiók tartományi fiókjait használja. |

### <a name="proxy-idle-timeout-setting"></a>Proxy tétlen időmegállítási beállítása
Amikor az Azure AD Connect exportálási kérelmet küld az Azure AD-nek, az Azure AD-nek akár 5 perc is igénybe vehet a kérés feldolgozása a válasz létrehozása előtt. Ez különösen akkor fordulhat elő, ha ugyanabban az exportálási kérelemben több olyan csoportobjektum szerepel, amelyek nagy csoporttagsággal vannak elhancesztal. Győződjön meg arról, hogy a proxy tétlen időkorlátja 5 percnél nagyobbra van konfigurálva. Ellenkező esetben az Azure AD időszakos kapcsolódási problémája figyelhető meg az Azure AD Connect kiszolgálón.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>Az Azure AD Connect és az Azure AD közötti kommunikációs minta
Ha követte az összes fenti lépést, és továbbra sem tud csatlakozni, akkor ezen a ponton elkezdheti a hálózati naplók keresését. Ez a szakasz egy normál és sikeres kapcsolódási mintát dokumentál. Ez is felsorolás mindennapi piros hering amit lehet nem vesz tudomásul mikor ön olvasó a hálózat tuskók.

* Vannak hívások `https://dc.services.visualstudio.com`. A telepítés sikeres megoldásához nem szükséges, hogy ez az URL-cím meg legyen nyitva a proxyban, és ezeket a hívásokat figyelmen kívül lehet hagyni.
* Láthatja, hogy a DNS-feloldás felsorolja azokat a tényleges állomásokat, amelyek a DNS-névtérben nsatc.net és más, microsoftonline.com alatt nem található névterekben. A tényleges kiszolgálóneveken azonban nincsenek webszolgáltatás-kérelmek, és nem kell hozzáadnia ezeket az URL-címeket a proxyhoz.
* A végpontok adminwebservice és provisioningapi felderítési végpontok, és a tényleges en használható végpont megkereséséhez használt. Ezek a végpontok a régiótól függően eltérőek.

### <a name="reference-proxy-logs"></a>Referenciaproxy-naplók
Itt van egy memóriakép egy tényleges proxynaplóból, és a telepítő varázsló lapja, ahonnan készült (az ismétlődő bejegyzéseket ugyanabba a végpontra eltávolították). Ez a szakasz referenciaként használható a saját proxy- és hálózati naplóihoz. A tényleges végpontok eltérőek lehetnek a környezetben (különösen a *dőlt*URL-ek).

**Csatlakozás az Azure AD szolgáltatáshoz**

| Time | URL-cím |
| --- | --- |
| 1/11/2016 8:31 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:31 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://*bba800-horgony*.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://*bwsc02-relé*.microsoftonline.com:443 |

**Konfigurálás**

| Time | URL-cím |
| --- | --- |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://*bba800-horgony*.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba900-horgony*.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba800-horgony*.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://*bwsc02-relé*.microsoftonline.com:443 |

**Kezdeti szinkronizálás**

| Time | URL-cím |
| --- | --- |
| 1/11/2016 8:48 |connect://login.windows.net:443 |
| 1/11/2016 8:49 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba900-horgony*.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba800-horgony*.microsoftonline.com:443 |

## <a name="authentication-errors"></a>Hitelesítési hibák
Ez a szakasz az ADAL -ból (az Azure AD Connect által használt hitelesítési kódtár) és a PowerShell által visszaadható hibákat ismerteti. A leírt hiba segít megérteni a következő lépéseket.

### <a name="invalid-grant"></a>Érvénytelen támogatás
Érvénytelen felhasználónév vagy jelszó. További információ: [A jelszó nem ellenőrizhető.](#the-password-cannot-be-verified)

### <a name="unknown-user-type"></a>Ismeretlen felhasználótípus
Az Azure AD-címtár nem található, vagy feloldható. Lehet, hogy megpróbál bejelentkezni egy felhasználónevet egy ellenőrizetlen domain?

### <a name="user-realm-discovery-failed"></a>A felhasználó felderítése sikertelen
Hálózati vagy proxykonfigurációs problémák. A hálózat nem érhető el. Lásd: [Kapcsolódási problémák elhárítása a telepítővarázslóban](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### <a name="user-password-expired"></a>Lejárt felhasználói jelszó
A hitelesítő adatai lejártak. Változtassa meg a jelszavát.

### <a name="authorization-failure"></a>Engedélyezési hiba
Nem sikerült engedélyezni a felhasználó számára a művelet et az Azure AD-ben.

### <a name="authentication-canceled"></a>Hitelesítés megszakítva
A többtényezős hitelesítési (MFA) kihívás megszakadt.

<div id="connect-msolservice-failed">
<!--
  Empty div just to act as an alias for the "Connect To MS Online Failed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="connect-to-ms-online-failed"></a>Nem sikerült csatlakozni az MS Online szolgáltatáshoz
A hitelesítés sikeres volt, de az Azure AD PowerShell hitelesítési problémával rendelkezik.

<div id="get-msoluserrole-failed">
<!--
  Empty div just to act as an alias for the "Azure AD Global Admin Role Needed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="azure-ad-global-admin-role-needed"></a>Az Azure AD globális rendszergazdai szerepköre szükséges
A felhasználó hitelesítése sikeresen megtörtént. A felhasználó azonban nincs hozzárendelve globális rendszergazdai szerepkörhöz. Így [rendelhet globális rendszergazdai szerepkört](../users-groups-roles/directory-assign-admin-roles.md) a felhasználóhoz. 

<div id="privileged-identity-management">
<!--
  Empty div just to act as an alias for the "Privileged Identity Management Enabled" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="privileged-identity-management-enabled"></a>Kiemelt identitáskezelés engedélyezve
A hitelesítés sikeres volt. A kiemelt identitáskezelés engedélyezve van, és ön jelenleg nem globális rendszergazda. További információ: [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md).

<div id="get-msolcompanyinformation-failed">
<!--
  Empty div just to act as an alias for the "Company Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="company-information-unavailable"></a>A vállalati adatok nem érhetők el
A hitelesítés sikeres volt. Nem sikerült beolvasni a vállalati adatokat az Azure AD-ből.

<div id="get-msoldomain-failed">
<!--
  Empty div just to act as an alias for the "Domain Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="domain-information-unavailable"></a>A tartományinformáció nem érhető el
A hitelesítés sikeres volt. Nem sikerült lekérni a tartományadatokat az Azure AD-ből.

### <a name="unspecified-authentication-failure"></a>Meghatározatlan hitelesítési hiba
Váratlan hibaként jelenik meg a telepítővarázslóban. Akkor fordulhat elő, ha iskolai vagy szervezeti fiók helyett **Microsoft-fiókot** próbál **használni.**

## <a name="troubleshooting-steps-for-previous-releases"></a>A korábbi kiadások kal kapcsolatos hibaelhárítási lépések.
Az 1.1.105.0(2016 februárjában kiadott) buildszámmal kezdődő kiadásokkal a bejelentkezési asszisztens torkig lett a kiadással. Ez a szakasz és a konfiguráció már nem szükséges, de referenciaként megmarad.

Az egyszeri bejelentkezési asszisztens működéséhez konfigurálni kell a winhttp-t. Ez a konfiguráció lehet tenni [**netsh**](how-to-connect-install-prerequisites.md#connectivity).  
![Netsh](./media/tshoot-connect-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>A bejelentkezési segéd nincs megfelelően konfigurálva
Ez a hiba akkor jelenik meg, ha a bejelentkezési segéd nem tudja elérni a proxyt, vagy a proxy nem engedélyezi a kérelmet.
![nonetsh](./media/tshoot-connect-connectivity/nonetsh.png)

* Ha ezt a hibát látja, nézze meg a [netsh proxykonfigurációját,](how-to-connect-install-prerequisites.md#connectivity) és ellenőrizze, hogy helyes-e.
  ![netshshow](./media/tshoot-connect-connectivity/netshshow.png)
* Ha ez helyesnek tűnik, kövesse a [Proxy-kapcsolat ellenőrzése](#verify-proxy-connectivity) című témakör lépéseit, és ellenőrizze, hogy a probléma a varázslón kívül is fennáll-e.

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
