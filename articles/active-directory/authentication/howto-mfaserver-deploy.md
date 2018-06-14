---
title: Azure Multi-Factor Authentication-kiszolgáló – első lépések | Microsoft Docs
description: Részletes bevezetés az Azure MFA Server a helyszínen történő használatába
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: get-started-article
ms.date: 10/02/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: bd1b0f21162978496750886d32e7166c3a90922d
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33868296"
---
# <a name="getting-started-with-the-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication-kiszolgáló – első lépések

<center>![Helyszíni MFA](./media/howto-mfaserver-deploy/server2.png)</center>

Most, hogy a helyszíni Multi-Factor Authentication-kiszolgáló használata mellett döntöttünk, lássunk neki. Ezen a lapon a kiszolgáló új telepítését és helyszíni Active Directoryval való beállítását ismertetjük. Ha már telepített MFA-kiszolgálót és frissíteni szeretné, tekintse meg [a legújabb Azure Multi-Factor Authentication-kiszolgálóra való frissítéssel kapcsolatos](howto-mfaserver-deploy-upgrade.md) témakört. Ha csak a webszolgáltatás telepítésével kapcsolatban keres információt, tekintse meg [Az Azure Multi-Factor Authentication-kiszolgáló Mobile App Web Service szolgáltatásának telepítése](howto-mfaserver-deploy-mobileapp.md) című témakört.

## <a name="plan-your-deployment"></a>Az üzembe helyezés megtervezése

Az Azure Multi-Factor Authentication-kiszolgáló letöltése előtt fontolja meg, milyen terhelési és magas rendelkezésre állási követelményekkel rendelkezik. Ezen információ alapján döntse el, hogyan és hol szeretné üzembe helyezni.

A szükséges memória mennyiségének meghatározásához jó útmutató azoknak a felhasználóknak a száma, akik várhatóan rendszeresen hitelesítést fognak végezni.

| Felhasználók | RAM |
| ----- | --- |
| 1–10 000 | 4 GB |
| 10 001–50 000 | 8 GB |
| 50 001–100 000 | 12 GB |
| 100 000–200 001 | 16 GB |
| 200 001+ | 32 GB |

Szüksége van több kiszolgáló beállítására magas rendelkezésre álláshoz vagy terheléselosztáshoz? Az Azure MFA-kiszolgálóval több módon is beállíthatja ezt a konfigurációt. Az elsőként telepített Azure MFA-kiszolgálót lesz a főkiszolgáló. A további kiszolgálók alárendelt kiszolgálókká válnak, és automatikusan szinkronizálják a felhasználókat és a konfigurációt a főkiszolgálóval. Ekkor konfigurálhat egy elsődleges kiszolgálót, a többit pedig használhatja biztonsági másolathoz, vagy beállíthat terheléselosztást a kiszolgálók között.

Amikor egy fő Azure MFA-kiszolgálónak megszakad az internetes kapcsolata, az alárendelt kiszolgálók továbbra is feldolgozhatják a kétlépéses ellenőrzési kérelmeket. Új felhasználókat azonban nem vehet fel, és a meglévő felhasználók nem frissíthetik a beállításaikat, amíg a főkiszolgáló megint elérhetővé nem válik, vagy egy alárendelt kiszolgálót elő nem léptet.

### <a name="prepare-your-environment"></a>A környezet előkészítése

Győződjön meg róla, hogy az Azure Multi-Factor Authenticationhöz használt kiszolgáló megfelel a következő követelményeknek:

| Az Azure Multi-Factor Authentication-kiszolgáló követelményei | Leírás |
|:--- |:--- |
| Hardver |<li>200 MB merevlemez-terület</li><li>x32-es vagy x64-es verzió futtatására képes processzor</li><li>Legalább 1 GB RAM</li> |
| Szoftver |<li>Windows Server 2016</li><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2</li><li>Windows Server 2008, SP1, SP2</li><li>Windows Server 2003 R2</li><li>Windows Server 2003, SP1, SP2</li><li>Windows 10</li><li>Windows 8.1, minden kiadás</li><li>Windows 8, minden kiadás</li><li>Windows 7, minden kiadás</li><li>Windows Vista, minden kiadás, SP1, SP2</li><li>Microsoft .NET-keretrendszer 4.0</li><li>IIS 7.0 vagy újabb a felhasználói portál vagy a web service SDK telepítésekor</li> |

### <a name="azure-mfa-server-components"></a>Az Azure MFA-kiszolgáló összetevői

Az Azure MFA-kiszolgáló három webösszetevőt tartalmaz:

* Web Service SDK – Lehetővé teszi a kommunikációt a többi összetevővel, és az Azure MFA-alkalmazáskiszolgálón van telepítve
* Felhasználói portál – egy olyan IIS-webhely, ahol a felhasználók regisztrálhatnak az Azure Multi-Factor Authenticationre (MFA), és karbantarthatják a fiókjaikat.
* Mobile App Web Service – Lehetővé teszi a Microsoft Authenticatorhoz hasonló mobilalkalmazások használatát a kétlépéses ellenőrzéshez.

Ha az adott kiszolgáló rendelkezik internetkapcsolattal, mindhárom összetevő telepíthető ugyanarra a kiszolgálóra. Ha szétválasztja az összetevőket, a Web Service SDK az Azure MFA-alkalmazáskiszolgálón, a Felhasználói portál és a Mobile App Web Service pedig az internetkapcsolattal rendelkező kiszolgálón lesz telepítve.

### <a name="azure-multi-factor-authentication-server-firewall-requirements"></a>Az Azure Multi-Factor Authentication-kiszolgáló tűzfalkövetelményei

Mindegyik MFA-kiszolgálónak képesnek kell lennie kommunikálni a 443-as kimenő porton a következő címekkel:

* https://pfd.phonefactor.net
* https://pfd2.phonefactor.net
* https://css.phonefactor.net

Ha a kimenő tűzfalak korlátozva vannak a 443-as porton, nyissa meg a következő IP-címtartományokat:

| IP-alhálózat | Hálózati maszk | IP-címtartomány |
|:---: |:---: |:---: |
| 134.170.116.0/25 |255.255.255.128 |134.170.116.1 – 134.170.116.126 |
| 134.170.165.0/25 |255.255.255.128 |134.170.165.1 – 134.170.165.126 |
| 70.37.154.128/25 |255.255.255.128 |70.37.154.129 – 70.37.154.254 |

Ha nem használja az eseménymegerősítési szolgáltatást, a felhasználók pedig nem használnak mobilalkalmazásokat az ellenőrzéshez a vállalati hálózaton található eszközökön, csak a következő tartományokra van szüksége:

| IP-alhálózat | Hálózati maszk | IP-címtartomány |
|:---: |:---: |:---: |
| 134.170.116.72/29 |255.255.255.248 |134.170.116.72 – 134.170.116.79 |
| 134.170.165.72/29 |255.255.255.248 |134.170.165.72 – 134.170.165.79 |
| 70.37.154.200/29 |255.255.255.248 |70.37.154.201 – 70.37.154.206 |

## <a name="download-the-mfa-server"></a>Az MFA-kiszolgáló letöltése

Az alábbi lépéseket követve töltse le az Azure Multi-Factor Authentication-kiszolgálót az Azure Portalról:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Válassza az **Azure Active Directory** > **MFA-kiszolgáló** lehetőséget.
3. Válassza a **Kiszolgálóbeállítások** lehetőséget.
4. Válassza a **Letöltés** lehetőséget, és kövesse a letöltési oldalon megjelenő utasításokat a telepítő mentéséhez. 

   ![Az MFA-kiszolgáló letöltése](./media/howto-mfaserver-deploy/downloadportal.png)

5. Hagyja megnyitva ezt az oldalt, mivel a telepítő futtatása után még hivatkozni fogunk rá.

## <a name="install-and-configure-the-mfa-server"></a>Az MFA-kiszolgáló telepítése és konfigurálása

Most, hogy letöltötte a kiszolgálót, telepítse és konfigurálja azt. Győződjön meg róla, hogy az a kiszolgáló, amelyikre telepíti, megfelel-e a tervezési szakaszban felsorolt követelményeknek.

1. Kattintson duplán a végrehajtható fájlra.
2. A Telepítési mappa kijelölése képernyőn győződjön meg arról, hogy megfelelő mappát választott ki, és kattintson a **Tovább** gombra.
3. A telepítés befejezése után kattintson a **Befejezés** gombra.  Ekkor elindul a konfigurációs varázsló.
4. A konfigurációs varázsló üdvözlőképernyőjén jelölje be **A hitelesítéskonfiguráló varázsló használatának kihagyása** jelölőnégyzetet, és kattintson a **Tovább** gombra.  Bezáródik a varázsló, és elindul a kiszolgáló.

   ![Felhő](./media/howto-mfaserver-deploy/skip2.png)

5. Azon az oldalon, ahonnan letöltötte a kiszolgálót, kattintson az **Aktiváló hitelesítő adatok előállítása** gombra. Másolja ezt az információt az Azure MFA-kiszolgálón a megfelelő mezőkbe, majd kattintson az **Aktiválás** lehetőségre.

## <a name="send-users-an-email"></a>E-mail küldése a felhasználóknak

A bevezetés megkönnyítése érdekében engedélyezze, hogy az MFA-kiszolgáló kommunikálhasson a felhasználóival. Az MFA-kiszolgáló tájékoztató e-mailt küld a felhasználóknak arról, hogy regisztrálta őket a kétlépéses ellenőrzési szolgáltatásban.

Az elküldött e-mail tartalmát az határozza meg, hogyan konfigurálja a felhasználókat a kétlépéses ellenőrzéshez. Ha például tud telefonszámokat importálni a vállalat címtárából, az e-mailnek tartalmaznia kell az alapértelmezett telefonszámokat, így a felhasználók tudni fogják, hogy mire számítsanak. Ha nem importál telefonszámokat, vagy ha a felhasználói a mobilalkalmazást fogják használni, olyan e-mailt küldjön a felhasználóknak, amely a fiók regisztrációjának befejezésére kéri őket. Az e-mail tartalmazzon egy hivatkozást az Azure Multi-Factor Authentication felhasználói portáljára.

Az e-mail tartalma a felhasználóhoz beállított ellenőrzési módszertől is függ (telefonhívás, SMS vagy mobilalkalmazás).  Ha például a felhasználónak PIN-kódot kell használnia a hitelesítéskor, az e-mail tartalmazza a kezdeti PIN-kódot.  A felhasználóknak általában az első ellenőrzéskor módosítaniuk kell a PIN-kódot.

### <a name="configure-email-and-email-templates"></a>E-mailek és e-mail-sablonok konfigurálása

Kattintson a bal oldalon található e-mail ikonra az e-mailek küldésére vonatkozó beállítások megadásához. Ezen az oldalon adhatja meg a levelezési kiszolgáló SMTP-adatait, és innen küldhet e-maileket az **E-mailek küldése a felhasználóknak** jelölőnégyzet bejelölésével.

![MFA-kiszolgáló – E-mail-konfiguráció](./media/howto-mfaserver-deploy/email1.png)

Az E-mail tartalma lapon áttekintheti a választható e-mail-sablonokat. Válassza a leginkább megfelelő sablont attól függően, hogyan konfigurálta a felhasználókat a kétlépéses ellenőrzés végrehajtására.

![MFA-kiszolgáló – E-mail-sablonok](./media/howto-mfaserver-deploy/email2.png)

## <a name="import-users-from-active-directory"></a>Felhasználók importálása az Active Directoryból

A kiszolgáló telepítése után a felhasználók hozzáadása következik. Létrehozhatja őket manuálisan, importálhatja az Active Directory-felhasználókat, vagy beállíthatja az Active Directoryval történő automatikus szinkronizálást.

### <a name="manual-import-from-active-directory"></a>Manuális importálás az Active Directoryból

1. Az Azure MFA-kiszolgálón a bal oldalon válassza a **Felhasználók** elemet.
2. Alul válassza az **Importálás Active Directoryból** lehetőséget.
3. Most kereshet egyes felhasználókat vagy kereshet az AD címtárban felhasználókat tartalmazó szervezeti egységeket.  Ebben az esetben meg kell adni a felhasználók szervezeti egységét.
4. Jelölje ki az összes felhasználót a jobb oldalon, és kattintson az **Importálás** gombra.  Megjelenik egy előugró ablak, amely a művelet sikerességéről értesít.  Zárja be az importálási ablakot.

   ![MFA-kiszolgáló – Felhasználók importálása](./media/howto-mfaserver-deploy/import2.png)

### <a name="automated-synchronization-with-active-directory"></a>Automatikus szinkronizálás az Active Directoryval

1. Az Azure MFA-kiszolgálón válassza a **Címtár-integráció** elemet a bal oldalon.
2. Lépjen a **Szinkronizálás** lapra.
3. Válassza az alul lévő **Hozzáadás** lehetőséget
4. A megjelenő **Szinkronizálási elem hozzáadása** mezőben válassza az ehhez a szinkronizálási feladathoz tartozó Tartomány, Szervezeti egység **vagy** biztonsági csoport, Beállítások, Módszer alapértelmezett beállításai vagy Nyelv alapértelmezett beállításai lehetőséget, és kattintson a **Hozzáadás** gombra.
5. Jelölje be a **Active Directory-szinkronizálás engedélyezése** jelölőnégyzetet, majd válassza ki a **Szinkronizálási időköz** értékét egy perc és 24 óra között.

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

## <a name="back-up-and-restore-azure-mfa-server"></a>Az Azure MFA-kiszolgáló biztonsági mentése és visszaállítása

A biztonsági mentés megfelelőségének ellenőrzése minden rendszeren fontos lépés.

Az Azure MFA-kiszolgáló biztonsági mentéséhez győződjön meg arról, hogy rendelkezik másolati példánnyal a **C:\Program Files\Multi-Factor Authentication Server\Data** mappáról, beleértve a **PhoneFactor.pfdata** fájlt is. 

Ha visszaállításra lenne szükség, a következő lépésekkel hajthatja végre:

1. Telepítse újra az Azure MFA-kiszolgálót egy új kiszolgálón.
2. Aktiválja az új Azure MFA-kiszolgálót.
3. Állítsa le a **MultiFactorAuth** szolgáltatást.
4. Írja felül a **PhoneFactor.pfdata** fájlt a biztonsági másolati példánnyal.
5. Indítsa el a **MultiFactorAuth** szolgáltatást.

Az új kiszolgáló ezzel használatra kész az eredeti biztonsági másolatban található konfigurációval és felhasználói adatokkal.

## <a name="next-steps"></a>További lépések

- A [felhasználói portál](howto-mfaserver-deploy-userportal.md) telepítése és konfigurálása a felhasználói önkiszolgáló funkciókhoz.
- Az Azure MFA-kiszolgáló telepítése és konfigurálása [Active Directory összevonási szolgáltatásokkal](multi-factor-authentication-get-started-adfs.md), [RADIUS-hitelesítéssel](howto-mfaserver-dir-radius.md) vagy [LDAP-hitelesítéssel](howto-mfaserver-dir-ldap.md).
- [Távoli asztali átjáró és RADIUS-t használó Azure Multi-Factor Authentication-kiszolgáló](howto-mfaserver-nps-rdg.md) telepítése és konfigurálása.
- [Az Azure Multi-Factor Authentication-kiszolgáló Mobile App Web Service szolgáltatásának telepítése](howto-mfaserver-deploy-mobileapp.md).
- [Speciális, az Azure Multi-Factor Authenticationre és külső VPN-ekre vonatkozó forgatókönyvek](howto-mfaserver-nps-vpn.md).
