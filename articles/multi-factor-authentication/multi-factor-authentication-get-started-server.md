---
title: "Azure Multi-Factor Authentication-kiszolgáló – első lépések | Microsoft Docs"
description: "Ez az Azure Multi-Factor Authentication-oldal leírja, hogyan kezdheti el az Azure MFA-kiszolgáló használatát."
services: multi-factor-authentication
keywords: "hitelesítési kiszolgáló, azure multi factor authentication alkalmazásaktiválási oldal, hitelesítési kiszolgáló letöltése"
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: e94120e4-ed77-44b8-84e4-1c5f7e186a6b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/26/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: c5a26a17ab50993f8b57c8868b02541251de1cb1
ms.lasthandoff: 03/06/2017

---

# <a name="getting-started-with-the-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication-kiszolgáló – első lépések

<center>![Helyszíni MFA](./media/multi-factor-authentication-get-started-server/server2.png)</center>

Most, hogy a helyszíni Multi-Factor Authentication-kiszolgáló használata mellett döntöttünk, lássunk neki. Ezen a lapon a kiszolgáló új telepítését és helyszíni Active Directoryval való beállítását ismertetjük. Ha már telepített MFA-kiszolgálót és frissíteni szeretné, tekintse meg [a legújabb Azure Multi-Factor Authentication-kiszolgálóra való frissítéssel kapcsolatos](multi-factor-authentication-server-upgrade.md) témakört. Ha csak a webszolgáltatás telepítésével kapcsolatban keres információt, tekintse meg [Az Azure Multi-Factor Authentication-kiszolgáló Mobile App Web Service szolgáltatásának telepítése](multi-factor-authentication-get-started-server-webservice.md) című témakört.
 

## <a name="download-the-azure-multi-factor-authentication-server"></a>Az Azure Multi-Factor Authentication-kiszolgáló letöltése
Az Azure Multi-Factor Authentication-kiszolgálót kétféle módszerrel töltheti le. Mindkettő az Azure portálról végezhető el. Az első a Multi-Factor Auth-szolgáltató közvetlen kezelése. A második a szolgáltatásbeállításokon keresztül érhető el. A második lehetőség használatához egy Multi-Factor Authentication-szolgáltatóra vagy egy Azure MFA, Azure AD Prémium vagy Nagyvállalati mobilitási csomag licencre van szükség.

> [!Important]
> A két lehetőség hasonlónak tűnik, de fontos tudni, hogy mikor melyiket érdemes használni. Ha a felhasználók az MFA-n keresztül elérhető licencekkel rendelkeznek (Azure MFA, Azure AD Premium vagy Enterprise Mobility + Security), a kiszolgáló letöltéséhez ne hozzon létre Multi-Factor Authentication-szolgáltatót. Inkább használja a 2. lehetőséget, és töltse le a kiszolgálót a szolgáltatásbeállítások oldaláról. 

### <a name="option-1-download-azure-multi-factor-authentication-server-from-the-azure-classic-portal"></a>1. lehetőség: Az Azure Multi-Factor Authentication-kiszolgáló letöltése a klasszikus Azure portálról

Ezt a letöltési lehetőséget akkor használja, ha már rendelkezik Multi-Factor Authentication-szolgáltatóval, mivel engedélyezett felhasználónk vagy hitelesítések alapján fizet az MFA használatáért. 

1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com) rendszergazdaként.
2. A bal oldalon válassza az **Active Directory** elemet.
3. Az Active Directory oldalon kattintson a **Multi-Factor Auth-szolgáltatók**
    ![Multi-Factor Auth-szolgáltatók](./media/multi-factor-authentication-get-started-server/authproviders.png) elemre.
4. Alul kattintson a **Kezelés** parancsra. Megnyílik egy új lap.
5. Kattintson a **Letöltések** elemre.
6. Kattintson a **Letöltés** hivatkozásra az **Aktiváló hitelesítő adatok előállítása** fölött.
   ![Letöltés](./media/multi-factor-authentication-get-started-server/download4.png)
7. Mentse a letöltött fájlt.

### <a name="option-2-download-azure-multi-factor-authentication-server-from-the-service-settings"></a>2. lehetőség: Az Azure Multi-Factor Authentication-kiszolgáló letöltése a szolgáltatásbeállításokból

Akkor használja ezt a letöltési lehetőséget, ha Nagyvállalati mobilitási csomag, Azure AD Prémium vagy Nagyvállalati felhőcsomag licenccel rendelkezik. 

1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com) rendszergazdaként.
2. A bal oldalon válassza az **Active Directory** elemet.
3. Kattintson duplán az Azure AD-példányra.
4. A lap tetején kattintson a **Konfigurálás** elemre.
5. Görgessen le a **Multi-Factor Authentication** szakaszig, és kattintson a **Szolgáltatásbeállítások kezelése** elemre.
6. A Szolgáltatásbeállítások lapon, a képernyő alsó részén kattintson az **Ugrás a portálra** elemre. Megnyílik egy új lap.
   ![Letöltés](./media/multi-factor-authentication-get-started-server/servicesettings.png)
7. Kattintson a **Letöltések** lehetőségre.
8. Kattintson a **Letöltés** hivatkozásra az **Aktiváló hitelesítő adatok előállítása** fölött.
    ![Letöltés](./media/multi-factor-authentication-get-started-server/download4.png)
9. Mentse a letöltött fájlt.

## <a name="install-and-configure-the-azure-multi-factor-authentication-server"></a>Az Azure Multi-Factor Authentication-kiszolgáló telepítése és konfigurálása
Most, hogy letöltötte a kiszolgálót, telepítse és konfigurálja azt.  Győződjön meg arról, hogy a kiszolgáló, amelyre telepíti, megfelel a következő követelményeknek:

| Az Azure Multi-Factor Authentication-kiszolgáló követelményei | Leírás |
|:--- |:--- |
| Hardver |<li>200 MB merevlemez-terület</li><li>x32-es vagy x64-es verzió futtatására képes processzor</li><li>Legalább&1; GB RAM</li> |
| Szoftver |<li>Windows Server 2008 vagy újabb, ha a gazdagép egy kiszolgálói operációs rendszer</li><li>Windows 7 vagy újabb, ha a gazdagép egy ügyfél operációs rendszer</li><li>Microsoft .NET-keretrendszer 4.0</li><li>IIS 7.0 vagy újabb a felhasználói portál vagy a web service SDK telepítésekor</li> |

### <a name="azure-multi-factor-authentication-server-firewall-requirements"></a>Az Azure Multi-Factor Authentication-kiszolgáló tűzfalkövetelményei
- - -
Mindegyik MFA-kiszolgálónak képesnek kell lennie kommunikálni a 443-as kimenő porton a következő címekkel:

* https://pfd.phonefactor.net
* https://pfd2.phonefactor.net
* https://css.phonefactor.net

Ha a kimenő tűzfalak korlátozva vannak a 443-as porton, nyissa meg a következő IP-címtartományokat:

| IP-alhálózat | Hálózati maszk | IP-címtartomány |
|:--- |:--- |:--- |
| 134.170.116.0/25 |255.255.255.128 |134.170.116.1 – 134.170.116.126 |
| 134.170.165.0/25 |255.255.255.128 |134.170.165.1 – 134.170.165.126 |
| 70.37.154.128/25 |255.255.255.128 |70.37.154.129 – 70.37.154.254 |

Ha nem használja az eseménymegerősítési szolgáltatást, a felhasználók pedig nem használnak mobilalkalmazásokat az ellenőrzéshez a vállalati hálózaton található eszközökön, csak a következő tartományokra van szüksége:

| IP-alhálózat | Hálózati maszk | IP-címtartomány |
|:--- |:--- |:--- |
| 134.170.116.72/29 |255.255.255.248 |134.170.116.72 – 134.170.116.79 |
| 134.170.165.72/29 |255.255.255.248 |134.170.165.72 – 134.170.165.79 |
| 70.37.154.200/29 |255.255.255.248 |70.37.154.201 – 70.37.154.206 |

### <a name="to-install-and-configure-the-azure-multi-factor-authentication-server"></a>Az Azure Multi-Factor Authentication-kiszolgáló telepítése és konfigurálása

Ezek a lépések a konfigurációs varázsló segítségével végrehajtott gyorstelepítés folyamatát ismertették. Ha nem jelenik meg a varázsló, vagy újból szeretné futtatni, válassza ki a kiszolgáló **Eszközök** menüjében.

1. Kattintson duplán a végrehajtható fájlra. 
2. A Telepítési mappa kijelölése képernyőn győződjön meg arról, hogy megfelelő mappát választott ki, és kattintson a **Tovább** gombra.
3. A telepítés befejezése után kattintson a **Befejezés** gombra.  Ekkor elindul a konfigurációs varázsló.
4. A konfigurációs varázsló üdvözlőképernyőjén jelölje be **A hitelesítéskonfiguráló varázsló használatának kihagyása** jelölőnégyzetet, és kattintson a **Tovább** gombra.  Bezáródik a varázsló, és elindul a kiszolgáló.
    ![Felhő](./media/multi-factor-authentication-get-started-server/skip2.png)
5. Azon az oldalon, ahonnan letöltötte a kiszolgálót, kattintson az **Aktiváló hitelesítő adatok előállítása** gombra. Másolja ezt az információt az Azure MFA-kiszolgálón a megfelelő mezőkbe, majd kattintson az **Aktiválás** lehetőségre.

## <a name="import-users-from-active-directory"></a>Felhasználók importálása az Active Directoryból
Most, hogy a kiszolgáló telepítve és konfigurálva van, a felhasználókat gyorsan importálhatja az Azure MFA-kiszolgálóra.

1. Az Azure MFA-kiszolgálón a bal oldalon válassza a **Felhasználók** elemet.
2. Alul válassza az **Importálás Active Directoryból** lehetőséget.
3. Most kereshet egyes felhasználókat vagy kereshet az AD címtárban felhasználókat tartalmazó szervezeti egységeket.  Ebben az esetben meg kell adni a felhasználók szervezeti egységét.
4. Jelölje ki az összes felhasználót a jobb oldalon, és kattintson az **Importálás** gombra.  Megjelenik egy előugró ablak, amely a művelet sikerességéről értesít.  Zárja be az importálási ablakot.

![Felhő](./media/multi-factor-authentication-get-started-server/import2.png)

## <a name="send-users-an-email"></a>E-mail küldése a felhasználóknak
Most, hogy importálta a felhasználóit az MFA-kiszolgálóra, küldjön nekik egy tájékoztató e-mailt arról, hogy regisztrálta őket a kétlépéses ellenőrzési szolgáltatásban.

Az elküldött e-mail tartalmát az határozza meg, hogyan konfigurálta a felhasználókat a kétlépéses ellenőrzéshez. Ha például importált telefonszámokat a vállalat címtárából, az e-mailnek tartalmaznia kell az alapértelmezett telefonszámokat, így a felhasználók tudni fogják, hogy mire számítsanak. Ha nem importált telefonszámokat, vagy ha a felhasználók a mobilalkalmazást fogják használni, olyan e-mailt küldjön a felhasználóknak, amely a fiók regisztrációjának befejezésére szólítja fel őket az Azure Multi-Factor Authentication felhasználói portálra mutató hivatkozás segítségével.

Az e-mail tartalma a felhasználóhoz beállított ellenőrzési módszertől is függ (telefonhívás, SMS vagy mobilalkalmazás).  Ha például a felhasználónak PIN-kódot kell használnia a hitelesítéskor, az e-mail tartalmazza a kezdeti PIN-kódot.  A felhasználóknak általában az első ellenőrzéskor módosítaniuk kell a PIN-kódot.


### <a name="configure-email-and-email-templates"></a>E-mailek és e-mail-sablonok konfigurálása
Kattintson a bal oldalon található e-mail ikonra az e-mailek küldésére vonatkozó beállítások megadásához. Itt adhatja meg a levelezési kiszolgáló SMTP-információit, és innen küldhet e-maileket az **E-mailek küldése a felhasználóknak** jelölőnégyzet bejelölésével.

![E-mail-beállítások](./media/multi-factor-authentication-get-started-server/email1.png)

Az E-mail tartalma lapon áttekintheti a választható e-mail-sablonokat. Válassza a leginkább megfelelő sablont attól függően, hogyan konfigurálta a felhasználókat a kétlépéses ellenőrzés végrehajtására.

![E-mail-sablonok](./media/multi-factor-authentication-get-started-server/email2.png)

## <a name="how-the-azure-multi-factor-authentication-server-handles-user-data"></a>A felhasználói adatok kezelése az Azure Multi-Factor Authentication-kiszolgálón
Ha a Multi-Factor Authentication- (MFA-) kiszolgálót a helyszínen használja, a felhasználók adatait a helyszíni kiszolgálók tárolják. A felhőben nincsenek állandó felhasználói adatok. Amikor a felhasználó kétlépéses ellenőrzést végez, az MFA-kiszolgáló elküldi az adatokat az Azure MFA-felhőszolgáltatásnak az ellenőrzés végrehajtásához. Amikor a rendszer ezeket a hitelesítési kéréseket elküldi a felhőszolgáltatásnak, a kérések és a naplók a következő mezőket tartalmazzák, hogy azok elérhetők legyenek az ügyfél hitelesítési/használati jelentéseiben. Egyes mezők megadása nem kötelező, így engedélyezhetők vagy letilthatók a Multi-Factor Authentication-kiszolgálón. Az MFA-kiszolgáló és az MFA-felhőszolgáltatás közötti kommunikáció SSL-/TLS-titkosítást használ a 443-as kimenő porton. Ezek a mezők a következők:

* Egyedi azonosító – felhasználónév vagy belső MFA-kiszolgálói azonosító
* Utónév és vezetéknév (nem kötelező)
* E-mail-cím (nem kötelező)
* Telefonszám – hanghívások vagy SMS-hitelesítés esetén
* Eszköztoken – mobilalkalmazásos hitelesítés esetén
* Hitelesítési módszer
* Hitelesítés eredménye
* MFA-kiszolgáló neve
* MFA-kiszolgáló IP-címe
* Ügyfél IP-címe – ha elérhető

A fenti mezőkön kívül a rendszer az ellenőrzési eredményeket (sikeres/elutasítva) és az elutasítások okait is tárolja a hitelesítési adatokkal együtt. Ezek az adatok a hitelesítési/használati jelentésekben érhetők el.

## <a name="next-steps"></a>Következő lépések

- A [felhasználói portál](multi-factor-authentication-get-started-portal.md) telepítése és konfigurálása a felhasználói önkiszolgáló funkciókhoz.

- Az Azure Multi-Factor Authentication telepítése az [Active Directory összevonási szolgáltatással](multi-factor-authentication-get-started-adfs.md).

- Az Azure MFA-kiszolgáló telepítése és konfigurálása [RADIUS-hitelesítéssel](multi-factor-authentication-get-started-server-radius.md). A RADIUS lehetővé teszi különböző külső rendszerek integrálását az Azure MFA-kiszolgálóval. 

- Az Azure MFA-kiszolgáló telepítése és konfigurálása [Windows-hitelesítéssel](multi-factor-authentication-get-started-server-windows.md).

- [Távoli asztali átjáró és RADIUS-t használó Azure Multi-Factor Authentication-kiszolgáló](multi-factor-authentication-get-started-server-rdg.md) telepítése és konfigurálása. 

- [Az Azure Multi-Factor Authentication-kiszolgáló Mobile App Web Service szolgáltatásának telepítése](multi-factor-authentication-get-started-server-webservice.md).

- [Speciális, az Azure Multi-Factor Authenticationre és külső VPN-ekre vonatkozó forgatókönyvek](multi-factor-authentication-advanced-vpn-configurations.md).

