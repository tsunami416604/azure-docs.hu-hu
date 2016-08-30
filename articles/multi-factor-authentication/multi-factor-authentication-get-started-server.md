<properties 
    pageTitle="Azure Multi-Factor Authentication-kiszolgáló – első lépések" 
    description="Ez az Azure Multi-Factor Authentication-oldal leírja, hogyan kezdheti el az Azure MFA-kiszolgáló használatát." 
    services="multi-factor-authentication"
    keywords="authentication server, azure multi factor authentication app activation page, authentication server download" 
    documentationCenter="" 
    authors="billmath" 
    manager="stevenpo" 
    editor="curtand"/>

<tags 
    ms.service="multi-factor-authentication" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="05/12/2016" 
    ms.author="billmath"/>

# Azure Multi-Factor Authentication-kiszolgáló – első lépések




<center>![Felhő](./media/multi-factor-authentication-get-started-server/server2.png)</center>

Most, hogy eldöntöttük, helyszíni többtényezős hitelesítést kívánunk-e használni, lássunk neki. Ezen a lapon a kiszolgáló új telepítését és helyszíni Active Directoryval való beállítását ismertetjük. Ha már telepített PhoneFactor-kiszolgálót és frissíteni szeretné, tekintse meg a [Frissítés Azure Multi-Factor-kiszolgálóra](multi-factor-authentication-get-started-server-upgrade.md) című témakört, vagy ha csak a webszolgáltatás telepítéséről keres információkat, tekintse meg [Az Azure Multi-Factor Authentication-kiszolgáló Mobile App Web Service szolgáltatásának telepítése](multi-factor-authentication-get-started-server-webservice.md) című témakört.


## Az Azure Multi-Factor Authentication-kiszolgáló letöltése



Az Azure Multi-Factor Authentication-kiszolgálót kétféle módszerrel töltheti le. Mindkettő az Azure portálról végezhető el. Az első a Multi-Factor Auth-szolgáltató közvetlen kezelése. A második a szolgáltatásbeállításokon keresztül érhető el. A második lehetőséghez szükséges egy Multi-Factor Authentication-szolgáltató, vagy egy Azure MFA, Azure AD Prémium vagy Nagyvállalati mobilitási csomag licenc.


### Az Azure Multi-Factor Authentication-kiszolgáló letöltése az Azure portálról
--------------------------------------------------------------------------------

1. Jelentkezzen be az Azure portálra rendszergazdaként.
2. A bal oldalon válassza az Active Directory elemet.
3. Az Active Directory oldalon felül kattintson a **Multi-Factor Auth-szolgáltatók** elemre
4. Alul kattintson a **Kezelés** elemre
5. Ez új lapot nyit meg.  Kattintson a **Letöltések** lehetőségre.
![Letöltés](./media/multi-factor-authentication-sdk/download.png)
6. Az **Aktiváló hitelesítő adatok előállítása** gomb fölött kattintson a **Letöltés** lehetőségre.
![Letöltés](./media/multi-factor-authentication-get-started-server/download4.png)
7. Mentse a letöltött fájlt.



### Az Azure Multi-Factor Authentication-kiszolgáló letöltése a szolgáltatásbeállításokon keresztül


1. Jelentkezzen be az Azure portálra rendszergazdaként.
2. A bal oldalon válassza az Active Directory elemet.
3. Kattintson duplán az Azure AD-példányra.
4. A lap tetején kattintson a **Konfigurálás** elemre.
![Letöltés](./media/multi-factor-authentication-sdk/download2.png)
5. A Multi-Factor Authentication szakaszban kattintson a **Szolgáltatásbeállítások kezelése** elemre.
6. A Szolgáltatásbeállítások lapon, a képernyő alsó részén kattintson az **Ugrás a portálra** elemre.
![Letöltés](./media/multi-factor-authentication-get-started-server/servicesettings.png)
7. Ez új lapot nyit meg. Kattintson a **Letöltések** lehetőségre.
8. Az **Aktiváló hitelesítő adatok előállítása** gomb fölött kattintson a **Letöltés** lehetőségre.
9. Mentse a letöltött fájlt.




## Az Azure Multi-Factor Authentication-kiszolgáló telepítése és konfigurálása
Most, hogy letöltötte a kiszolgálót, telepítse és konfigurálja azt.  Győződjön meg arról, hogy a kiszolgáló, amelyre telepíti, megfelel a következő követelményeknek:



Az Azure Multi-Factor Authentication-kiszolgáló követelményei|Leírás|
:------------- | :------------- | 
Hardver|<li>200 MB merevlemez-terület</li><li>x32-es vagy x64-es verzió futtatására képes processzor</li><li>Legalább 1 GB RAM</li>
Szoftver|<li>Windows Server 2008 vagy újabb, ha a gazdagép egy kiszolgálói operációs rendszer</li><li>Windows 7 vagy újabb, ha a gazdagép egy ügyfél operációs rendszer</li><li>Microsoft .NET-keretrendszer 4.0</li><li>IIS 7.0 vagy újabb a felhasználói portál vagy a web service SDK telepítésekor</li>

### Az Azure Multi-Factor Authentication-kiszolgáló tűzfalkövetelményei
--------------------------------------------------------------------------------
Mindegyik MFA-kiszolgálónak képesnek kell lennie kommunikálni a 443-as kimenő porton a következőkkel:

- https://pfd.phonefactor.net
- https://pfd2.phonefactor.net
- https://css.phonefactor.net

Ha a kimenő tűzfalak korlátozva vannak a 443-as porton, a következő IP-címtartományokat kell megnyitni:

IP-alhálózat|Hálózati maszk|IP-címtartomány
:------------- | :------------- | :------------- |
134.170.116.0/25|255.255.255.128|134.170.116.1 – 134.170.116.126
134.170.165.0/25|255.255.255.128|134.170.165.1 – 134.170.165.126
70.37.154.128/25|255.255.255.128|70.37.154.129 – 70.37.154.254

Ha nem használja az Azure Multi-Factor Authentication eseménymegerősítési szolgáltatásait, és a felhasználók nem végeznek hitelesítést a Multi-Factor Auth-mobilalkalmazásokban a vállalati hálózaton található eszközökről, az IP-címtartományokat a következőkre lehet szűkíteni:


IP-alhálózat|Hálózati maszk|IP-címtartomány
:------------- | :------------- | :------------- |
134.170.116.72/29|255.255.255.248|134.170.116.72 – 134.170.116.79
134.170.165.72/29|255.255.255.248|134.170.165.72 – 134.170.165.79
70.37.154.200/29|255.255.255.248|70.37.154.201 – 70.37.154.206


### Az Azure Multi-Factor Authentication-kiszolgáló telepítése és konfigurálása
--------------------------------------------------------------------------------


1. Kattintson duplán a végrehajtható fájlra. Ez elindítja a telepítést.
2. A Telepítési mappa kijelölése képernyőn győződjön meg arról, hogy megfelelő mappát választott ki, és kattintson a Tovább gombra.
3. A telepítés befejezése után kattintson a Befejezés gombra.  Ez elindítja a konfigurációs varázslót.
4. A konfigurációs varázsló üdvözlőoldalán jelölje be **A hitelesítéskonfiguráló varázsló használatának kihagyása** jelölőnégyzetet, és kattintson a **Tovább** gombra.  Ez bezárja a varázslót, majd elindítja a kiszolgálót.
![Felhő](./media/multi-factor-authentication-get-started-server/skip2.png)

5. Azon az oldalon, ahonnan letöltötte a kiszolgálót, kattintson az **Aktiváló hitelesítő adatok előállítása** gombra.  Másolja ezt az információt az Azure MFA-kiszolgálón a megfelelő mezőkbe, majd kattintson az **Aktiválás** lehetőségre.


A fenti lépések a konfigurációs varázsló segítségével végrehajtott gyorstelepítést mutatják be.  A hitelesítési varázslót ismét futtathatja a kiszolgálón az Eszközök menüből kiválasztva.



##Felhasználók importálása az Active Directoryból

Most, hogy a kiszolgáló telepítve és konfigurálva van, a felhasználókat gyorsan importálhatja az Azure MFA-kiszolgálóra. 

### Felhasználók importálása az Active Directoryból
--------------------------------------------------------------------------------


1. Az Azure MFA-kiszolgálón a bal oldalon válassza a **Felhasználók** elemet.
2. Alul válassza az **Importálás Active Directoryból** lehetőséget.
3. Most kereshet egyes felhasználókat vagy kereshet az AD címtárban felhasználókat tartalmazó szervezeti egységeket.  Ebben az esetben megadjuk a felhasználók szervezeti egységét.
4. Jelölje ki az összes felhasználót a jobb oldalon, és kattintson az **Importálás** elemre.  Megjelenik egy előugró ablak, amely a művelet sikerességéről értesít.  Zárja be az importálási ablakot.

![Felhő](./media/multi-factor-authentication-get-started-server/import2.png)

## E-mail küldése a felhasználóknak
Most, hogy importálta a felhasználóit az Azure Multi-Factor Authentication-kiszolgálóra, érdemes küldeni nekik egy tájékoztató e-mailt arról, hogy regisztrálta őket a többtényezős hitelesítési szolgáltatásban.

Az Azure Multi-Factor Authentication-kiszolgálóval számos különféle módon konfigurálhatja a felhasználókat a többtényezős hitelesítés használatára.  Ha például ismeri a felhasználók telefonszámait, vagy importálta a telefonszámokat az Azure Multi-Factor Authentication-kiszolgálóra a vállalati címtárból, az e-mail értesíti a felhasználókat, hogy fiókjuk konfigurálva lett az Azure Multi-Factor Authentication használatára, rövid útmutatást biztosít az Azure Multi-Factor Authentication használatához, valamint tájékoztatja a felhasználót a telefonszámról, amelyen a hitelesítéseket fogja kapni.  

Az e-mail tartalma a felhasználókhoz beállított hitelesítési módszertől függ (például telefonhívás, SMS, mobilalkalmazás).  Ha például a felhasználónak PIN-kódot kell használnia a hitelesítéskor, az e-mail tartalmazza a beállított kezdeti PIN-kódot.  A felhasználóknak általában az első hitelesítéskor módosítaniuk kell a PIN-kódot.

Ha a felhasználók telefonszámait nem konfigurálta vagy importálta az Azure Multi-Factor Authentication-kiszolgálóra, vagy a felhasználók számára előre konfigurálva van a mobilalkalmazásos hitelesítés használata, küldhet nekik egy e-mailt, amely értesíti őket, hogy a rendszer konfigurálta számukra az Azure Multi-Factor Authentication használatát, illetve az Azure Multi-Factor Authentication felhasználói portálra irányítja őket a fiókjuk regisztrációjának befejezéséhez.  Az e-mail tartalmaz egy hiperhivatkozást, amelyre kattintva a felhasználó hozzáférhet a felhasználói portálhoz. Amikor a felhasználó a hiperhivatkozásra kattint, megnyílik a webböngésző, és megjelenik a vállalat Azure Multi-Factor Authentication felhasználói portálja.   


### E-mailek és e-mail-sablonok konfigurálása

A bal oldalon található e-mail ikonra kattintva megadhatja az e-mailek küldésének beállításait.  Itt adhatja meg a levelezési kiszolgáló SMTP-információit, és innen küldhet rendszerszintű e-maileket a Levelek küldése felhasználóknak jelölőnégyzet bejelölésével.

![E-mail-beállítások](./media/multi-factor-authentication-get-started-server/email1.png)

Az E-mail tartalma lapon láthatja az összes elérhető e-mail-sablont, amelyek közül választhat.  Így kiválaszthatja a legmegfelelőbb sablont attól függően, hogyan konfigurálta a felhasználókat a többtényezős hitelesítés használatára.

![E-mail-sablonok](./media/multi-factor-authentication-get-started-server/email2.png)

## A felhasználói adatok kezelése az Azure Multi-Factor Authentication-kiszolgálón

Ha a Multi-Factor Authentication- (MFA-) kiszolgálót a helyszínen használja, a felhasználók adatait a helyszíni kiszolgálók tárolják. A felhőben nincsenek állandó felhasználói adatok. Amikor a felhasználó kéttényezős hitelesítést végez, az MFA-kiszolgáló elküldi az adatokat az Azure MFA-felhőszolgáltatásnak a hitelesítés végrehajtásához. Amikor a rendszer ezeket a hitelesítési kéréseket elküldi a felhőszolgáltatásnak, a kérések és a naplók a következő mezőket tartalmazzák, hogy azok elérhetők legyenek az ügyfél hitelesítési/használati jelentéseiben. Egyes mezők megadása nem kötelező, így engedélyezhetők vagy letilthatók a Multi-Factor Authentication-kiszolgálón. Az MFA-kiszolgáló és az MFA-felhőszolgáltatás közötti kommunikáció SSL-/TLS-titkosítást használ a 443-as kimenő porton. Ezek a mezők a következők:

- Egyedi azonosító – felhasználónév vagy belső MFA-kiszolgálói azonosító
- Utónév és vezetéknév – nem kötelező
- E-mail-cím – nem kötelező
- Telefonszám – hanghívások vagy SMS-hitelesítés esetén
- Eszköztoken – mobilalkalmazásos hitelesítés esetén
- Hitelesítési mód 
- Hitelesítés eredménye 
- MFA-kiszolgáló neve 
- MFA-kiszolgáló IP-címe 
- Ügyfél IP-címe – ha elérhető



A fenti mezőkön kívül a hitelesítési eredményeket (sikeres/elutasítva) és az elutasítások okait is tárolja a rendszer a hitelesítési adatokkal, amelyek a hitelesítési/használati jelentésekben érhetők el.


## Speciális Azure Multi-Factor Authentication-kiszolgálókonfigurációk
A speciális telepítési és konfigurációs adatokról további információkat az alábbi táblázatban talál.

Módszer|Leírás
:------------- | :------------- | 
[Felhasználói portál](multi-factor-authentication-get-started-portal.md)|  Információk a felhasználói portál telepítéséről és konfigurálásáról, beleértve az üzemelő példányt és a felhasználói önkiszolgáló funkciókat.
[Active Directory összevonási szolgáltatás](multi-factor-authentication-get-started-adfs.md)|Információk az Azure Multi-Factor Authentication az AD FS-sel való beállításáról.
[RADIUS-hitelesítés](multi-factor-authentication-get-started-server-radius.md)|  Információk az Azure MFA-kiszolgáló telepítéséről és konfigurálásáról a RADIUS-szal.
[IIS-hitelesítés](multi-factor-authentication-get-started-server-iis.md)|Információk az Azure MFA-kiszolgáló telepítéséről és konfigurálásáról az IIS-szel.
[Windows-hitelesítés](multi-factor-authentication-get-started-server-windows.md)|  Információk az Azure MFA-kiszolgáló telepítéséről és konfigurálásáról Windows-hitelesítéssel.
[LDAP-hitelesítés](multi-factor-authentication-get-started-server-ldap.md)|Információk az Azure MFA-kiszolgáló telepítéséről és konfigurálásáról LDAP-hitelesítéssel.
[Távoli asztali átjáró és RADIUS-t használó Azure Multi-Factor Authentication-kiszolgáló](multi-factor-authentication-get-started-server-rdg.md)|  Információk az Azure MFA-kiszolgáló telepítéséről és konfigurálásáról a Távoli asztali átjáróval, amely a RADIUS-t használja.
[Szinkronizálás a Windows Server Active Directoryval](multi-factor-authentication-get-started-server-dirint.md)|Információk az Active Directory és az Azure MFA-kiszolgáló közötti szinkronizálás létesítéséről és konfigurálásáról.
[Az Azure Multi-Factor Authentication-kiszolgáló Mobile App Web Service szolgáltatásának telepítése](multi-factor-authentication-get-started-server-webservice.md)|Információk az Azure MFA-kiszolgáló webszolgáltatásának telepítéséről és konfigurálásáról.



<!--HONumber=jun16_HO2-->


