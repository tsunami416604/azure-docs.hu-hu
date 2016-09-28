<properties
    pageTitle="Az Azure AD-alkalmazásproxy engedélyezése | Microsoft Azure"
    description="A klasszikus Azure portálon kapcsolja be az alkalmazásproxyt, majd telepítse a fordított proxyhoz tartozó összekötőket."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/19/2016"
    ms.author="kgremban"/>


# Alkalmazásproxy engedélyezése az Azure Portalon

Ez a cikk útmutatást nyújt a felhőcímtárhoz tartozó Microsoft Azure AD alkalmazásproxy engedélyezéséhez szükséges lépésekről az Azure AD-ben.

Ha még nem ismerkedett meg az alkalmazásproxy szolgáltatásaival, a következő cikkben talál erre vonatkozó információkat: [How to provide secure remote access to on-premises applications](active-directory-application-proxy-get-started.md) (Helyszíni alkalmazások biztonságos távoli hozzáférése).

## Az alkalmazásproxy használatának előfeltételei
Az alkalmazásproxy szolgáltatásainak engedélyezése és használata előtt a következőkkel kell rendelkeznie:

- [Microsoft Azure AD Prémium vagy Alapszintű előfizetés](active-directory-editions.md) és egy globális rendszergazdaként használt Azure AD-címtár.
- Windows Server 2012 R2, Windows 8.1 vagy újabb rendszert futtató kiszolgáló, amelyre telepítheti az alkalmazásproxy összekötőjét. A kiszolgáló kéréseket küld a felhőben az alkalmazásproxy-szolgáltatásoknak, és HTTP- vagy HTTPS-kapcsolatra van szükség a közzétett alkalmazásokhoz.

    - A közzétett alkalmazásokban elérhető egyszeri bejelentkezéshez ennek a gépnek egy tartományhoz kell csatlakoznia, hogy ugyanabban az AD-tartományban legyen, mint amelyikben az alkalmazást közzéteszi.

- Ha az útvonalon tűzfal található, győződjön meg arról, hogy az nyitva van, így az összekötő el tudja küldeni a HTTPS- (TCP-) kéréseket az alkalmazásproxynak. Az összekötő ezeket a portokat a magas szintű tartományok (msappproxy.net és servicebus.windows.net) részét képező altartományokkal együtt használja. Győződjön meg arról, hogy az alábbi port meg van nyitva a **kimenő** adatforgalom számára:

  	| Portszám | Leírás |
  	| --- | --- |
  	| 80 | Kimenő HTTP-adatforgalom engedélyezése biztonsági ellenőrzés céljából. |
  	| 443 | Felhasználóhitelesítés engedélyezése az Azure AD-ben (csak az összekötő-regisztrációs folyamathoz szükséges) |
  	| 10100–10120 | LOB HTTP-válaszok visszaküldésének engedélyezése a proxyra |
  	| 9352, 5671 | A bejövő kérések kommunikációjának engedélyezése az összekötőtől az Azure-szolgáltatás irányába. |
  	| 9350 | A nagyobb teljesítmény elérése érdekében a bejövő kérések kiszolgálásához (elhagyható). |
  	| 8080 | Az összekötő rendszerindítási műveletsorának és automatikus frissítésének engedélyezése |
  	| 9090 | Az összekötő regisztrálásának engedélyezése (csak az összekötő-regisztrációs folyamathoz szükséges) |
  	| 9091 | Az összekötő megbízható tanúsítványai automatikus megújításának engedélyezése |

    Ha a tűzfal a felhasználók helye szerint szabályozza az adatforgalmat, nyissa meg ezeket a portokat a hálózati szolgáltatásként futó Windows-szolgáltatások adatforgalma számára. Győződjön meg arról is, hogy engedélyezte a 8080-as port használatát az NT Authority\System számára.

- Ha a szervezete proxykiszolgálóval csatlakozik az internethez, tekintse meg a következő blogbejegyzést, amelyből részleteket tudhat meg a proxykiszolgálók beállításáról: [Working with existing on-premises proxy servers](https://blogs.technet.microsoft.com/applicationproxyblog/2016/03/07/working-with-existing-on-prem-proxy-servers-configuration-considerations-for-your-connectors/) (Meglévő helyszíni proxykiszolgálók használata).

## 1. lépés: Alkalmazásproxy engedélyezése az Azure AD-ben
1. Jelentkezzen be rendszergazdaként a [klasszikus Azure portálra](https://manage.windowsazure.com/).
2. Lépjen az Active Directory területre, majd válassza ki azt a címtárat, amelyen az alkalmazásproxyt engedélyezni kívánja.

    ![Active Directory – ikon](./media/active-directory-application-proxy-enable/ad_icon.png)

3. A címtár oldalán válassza a **Konfigurálás** lehetőséget, majd görgessen lefelé az **Alkalmazásproxy** elemhez.
4. Az **Enable Application Proxy Services for this Directory** (Alkalmazásproxy-szolgáltatások engedélyezése ehhez a címtárhoz) beállítása legyen **Enabled** (Engedélyezve).

    ![Alkalmazásproxy engedélyezése](./media/active-directory-application-proxy-enable/app_proxy_enable.png)

5. Válassza a **Download now** (Letöltés most) lehetőséget. A rendszer ekkor a következő helyre irányítja: **Azure AD Application Proxy Connector Download** (Azure AD alkalmazásproxy-összekötő letöltése). Olvassa el és fogadja el a licencfeltételeket, majd kattintson a **Download** (Letöltés) gombra az összekötőhöz tartozó Windows Installer-fájl (.exe) mentéséhez.

## 2. lépés: Az összekötő telepítése és regisztrálása
1. Az előfeltételeknek megfelelően előkészített kiszolgálón futtassa az **AADApplicationProxyConnectorInstaller.exe** programot.
2. A telepítés végrehajtásához kövesse a varázsló utasításait.
3. A telepítés során a rendszer felkéri az összekötő regisztrálására az Azure AD-bérlő alkalmazásproxyjával.

  - Adja meg Azure AD globális rendszergazdai hitelesítő adatait. A globális rendszergazdai bérlő adatai eltérhetnek a Microsoft Azure rendszerre vonatkozó hitelesítő adatoktól.
  - Győződjön meg arról, hogy az összekötőt regisztráló rendszergazda ugyanabban a címtárban található, ahol az alkalmazásproxy-szolgáltatás engedélyezve van. Ha például a bérlő tartománya a contoso.com, a rendszergazda admin@contoso.com vagy más, adott tartománybeli alias lehet.
  - Ha az **Internet Explorer – Fokozott biztonsági beállítások** **Be** vannak kapcsolva azon a kiszolgálón, amelyre az összekötőt telepíti, lehetséges, hogy a regisztrációs képernyő blokkolva van. A hozzáférés biztosításához kövesse a hibaüzenetben közölt utasításokat. Győződjön meg arról, hogy az Internet Explorer fokozott biztonsági beállításai ki vannak kapcsolva.
  - Ha az összekötő regisztrálása meghiúsul, tekintse meg a [Troubleshoot Application Proxy](active-directory-application-proxy-troubleshoot.md) (Alkalmazásproxy hibaelhárítása) című anyagot.  

4. A telepítést követően a kiszolgáló két új szolgáltatással bővül:

    - a **Microsoft AAD alkalmazásproxy-összekötő** a kapcsolódást engedélyezi;
    - A **Microsoft AAD alkalmazásproxyösszekötő-frissítő** egy automatikus frissítési szolgáltatás, amely időszakosan ellenőrzi, hogy elérhető-e az összekötő újabb verziója, és szükség szerint frissíti az eszközt.

    ![Az alkalmazásproxy összekötőjének szolgáltatásai – képernyőfelvétel](./media/active-directory-application-proxy-enable/app_proxy_services.png)

5. Kattintson a **Befejezés** gombra a telepítő ablakában.

A magas szintű rendelkezésre állás érdekében legalább kettő összekötőt üzembe kell helyeznie. További összekötők üzembe helyezéséhez ismételje meg a 2. és a 3. lépésben leírtakat. Minden egyes összekötőt külön kell regisztrálni.

Ha el kívánja távolítani az összekötőt, távolítsa el az összekötő- és a frissítési szolgáltatást is. Indítsa újra a számítógépet a szolgáltatás teljes eltávolításához.


## Következő lépések

Most már készen áll az [alkalmazások közzétételére az alkalmazásproxy használatával](active-directory-application-proxy-publish.md).

Ha vannak olyan alkalmazásai, amelyek egy különálló hálózaton vagy különböző helyen találhatók, használhat összekötőcsoportokat a különböző összekötők logikai egységekbe rendezéséhez. További információ: [Az alkalmazásproxy-összekötők használata](active-directory-application-proxy-connectors.md).



<!--HONumber=Sep16_HO4-->


