<properties
    pageTitle="Első lépések: Azure AD-jelszókezelés | Microsoft Azure"
    description="Lehetővé teheti, hogy a felhasználók visszaállíthassák a saját jelszavukat, megismerheti a jelszó-visszaállítás előfeltételeit, és lehetővé teheti a jelszavak visszaírását a helyszíni jelszavak kezeléséhez az Active Directoryban."
    services="active-directory"
    keywords="Active Directory-jelszókezelés, jelszókezelés, Azure AD-jelszó alaphelyzetbe állítása"
    documentationCenter=""
    authors="asteen"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/12/2016"
    ms.author="asteen"/>

# A jelszókezelés első lépései

> [AZURE.IMPORTANT] **Azért van itt, mert problémák merültek fel a bejelentkezéssel kapcsolatban?** Ha igen, [így módosíthatja vagy állíthatja alaphelyzetbe a jelszavát](active-directory-passwords-update-your-own-password.md).

Csupán néhány egyszerű lépéssel lehetővé teheti, hogy a felhasználók kezelhessék a saját felhőalapú Azure Active Directory- vagy helyszíni Active Directory-jelszavaikat. Miután ellenőrizte, hogy megfelel-e néhány egyszerű előfeltételnek, a jelszómódosítás és -visszaállítás hamar engedélyezhetővé tehető a teljes szervezet számára. Ez a cikk a következő fogalmakat ismerteti:

* [**Annak engedélyezése, hogy a felhasználók visszaállíthassák a felhőalapú Azure Active Directory-jelszavaikat**](#enable-users-to-reset-their-azure-ad-passwords)
 - [Az önkiszolgáló jelszó-visszaállítás előfeltételei](#prerequisites)
 - [1. lépés: Jelszó-visszaállítási házirend konfigurálása](#step-1-configure-password-reset-policy)
 - [2. lépés: Kapcsolati adatok megadása a tesztfelhasználónál](#step-2-add-contact-data-for-your-test-user)
 - [3. lépés: Jelszó visszaállítása felhasználóként](#step-3-reset-your-azure-ad-password-as-a-user)
* [**Annak engedélyezése, hogy a felhasználók visszaállíthassák vagy módosíthassák a helyszíni Azure Active Directory-jelszavaikat**](#enable-users-to-reset-or-change-their-ad-passwords)
 - [Jelszóvisszaírás előfeltételei](#writeback-prerequisites)
 - [1. lépés: Az Azure AD Connect legújabb verziójának letöltése](#step-1-download-the-latest-version-of-azure-ad-connect)
 - [2. lépés: A jelszóvisszaírás engedélyezése az Azure AD Connectben a felhasználói felületen vagy a PowerShellen keresztül, és ellenőrzés](#step-2-enable-password-writeback-in-azure-ad-connect)
 - [3. lépés: A tűzfal konfigurálása](#step-3-configure-your-firewall)
 - [4. lépés: A megfelelő engedélyek beállítása](#step-4-set-up-the-appropriate-active-directory-permissions)
 - [5. lépés: Az AD-jelszó visszaállítása felhasználóként, és ellenőrzés](#step-5-reset-your-ad-password-as-a-user)

## Annak engedélyezése, hogy a felhasználók visszaállíthassák az Azure AD-jelszavaikat
Ez a szakasz végigvezeti az önkiszolgáló jelszó-visszaállítás az AAD a felhőalapú címtárához történő engedélyezésén, a felhasználók önkiszolgáló jelszó-visszaállításhoz történő regisztrálásán, majd végül tesztelési célból egy önkiszolgáló jelszó-visszaállítás felhasználóként történő elvégzésén.

- [Az önkiszolgáló jelszó-visszaállítás előfeltételei](#prerequisites)
- [1. lépés: Jelszó-visszaállítási házirend konfigurálása](#step-1-configure-password-reset-policy)
- [2. lépés: Kapcsolati adatok megadása a tesztfelhasználónál](#step-2-add-contact-data-for-your-test-user)
- [3. lépés: Jelszó visszaállítása felhasználóként](#step-3-reset-your-azure-ad-password-as-a-user)


###  Előfeltételek
Mielőtt önkiszolgáló jelszó-visszaállítást engedélyezhetne és használhatna, eleget kell tennie a következő előfeltételeknek:

- Hozzon létre egy AAD-bérlőt. További információ: [Ismerkedés az Azure AD szolgáltatással](https://azure.microsoft.com/trial/get-started-active-directory/)
- Szerezzen be egy Azure-előfizetést. További információért lásd: [Mi az az Azure AD-bérlő?](active-directory-administer.md#what-is-an-azure-ad-tenant).
- Társítsa az AAD-bérlőt az Azure-előfizetéshez. További információkért lásd: [How Azure subscriptions are associated with Azure AD?](https://msdn.microsoft.com/library/azure/dn629581.aspx) (Hogyan kapcsolódnak az Azure-előfizetések az Azure AD-hoz?).
- Frissítsen az Azure AD Prémium vagy Alapszintű kiadásra, vagy használjon fizetős O365-licencet. További információk: [Azure Active Directory editions](https://azure.microsoft.com/pricing/details/active-directory/) (Azure Active Directory-kiadások).

  >[AZURE.NOTE] Ha engedélyezni szeretné az önkiszolgáló jelszó-visszaállítást a felhőbeli felhasználóinak, frissítenie kell az Azure AD Prémium, Azure AD Alapszintű vagy egy fizetős O365-licencre.  Az önkiszolgáló jelszó-visszaállítás helyszíni felhasználók számára történő engedélyezéséhez frissítenie kell az Azure AD Prémium kiadásra. További információk: [Azure Active Directory editions](https://azure.microsoft.com/pricing/details/active-directory/) (Azure Active Directory-kiadások). Az alábbi információkban megtalálja az Azure AD Prémium vagy Alapszintű kiadásra történő regisztráció, a licenccsomag aktiválásának és az Azure AD-hozzáférés aktiválásának, valamint a hozzáférés rendszergazdákhoz és felhasználói fiókokhoz történő hozzárendelésének részletes útmutatásait.

- Hozzon létre legalább egy rendszergazdai fiókot és egy felhasználói fiókot az AAD-címtárában.
- Rendeljen egy AAD Prémium, Alapszintű vagy egy fizetős O365-licencet a létrehozott rendszergazdai és felhasználói fiókhoz.

### 1. lépés: Jelszó-visszaállítási házirend konfigurálása
Felhasználói jelszó-visszaállítási házirend konfigurálásához végezze el a következő lépéseket:

1.  Nyisson meg egy böngészőt, és nyissa meg az [Azure felügyeleti portált](https://manage.windowsazure.com).
2.  Az [Azure felügyeleti portálon](https://manage.windowsazure.com) keresse meg az **Active Directory-bővítményt** a bal oldali navigációs sávon.

    ![Jelszókezelés az Azure AD-ben][001]

3. A **Címtár** lapon kattintson arra a címtárra, amelyben konfigurálni szeretné a jelszó-visszaállítási házirendet, például a Wingtip Toys címtárra.

    ![][002]

4.  Kattintson a **Configure** (Konfigurálás) lapra.

    ![][003]

5.  A **Konfigurálás** lapon görgessen le a **felhasználói jelszó-visszaállítási házirend** szakaszhoz.  Itt konfigurálhatja az adott címtárak jelszó-visszaállítási házirendjének minden szempontját.  

    >[AZURE.NOTE] Ez a **házirend csak a szervezet végfelhasználóira érvényes, a rendszergazdákra nem**. Biztonsági okokból a Microsoft kezeli a rendszergazdák jelszó-visszaállítási házirendjét. Ha nem látja ezt a szakaszt, ellenőrizze, hogy regisztrált-e az Azure Active Directory Prémium vagy Alapszintű kiadásra, és hogy **licencet rendelt-e** a funkciót konfiguráló rendszergazdai fiókhoz.

    ![][004]

6.  A felhasználói jelszó-visszaállítási házirend konfigurálásához állítsa a **jelszó-visszaállításhoz engedélyezett felhasználók** kapcsolót az **igen** beállításra.  Ez több más vezérlőt is megjelenít, amelyekkel konfigurálhatja a funkció működésének módját a címtárban.  Nyugodtan szabja testre a jelszó-visszaállítást, ahogyan szeretné.  Ha további információt szeretne megtudni arról, hogy mire szolgálnak az egyes jelszó-visszaállítási házirendek, lásd: [Testreszabás: Azure AD-jelszókezelés](active-directory-passwords-customize.md).

    ![][005]

7.  A bérlő felhasználói jelszó-visszaállítási házirendjének kívánt módon történő konfigurálása után kattintson a képernyő alján lévő **Mentés** gombra.

  >[AZURE.NOTE] Két ellenőrző felhasználói jelszó-visszaállítási házirend ajánlott, hogy láthassa, hogyan működik a funkció a legösszetettebb esetben.

  ![][006]

### 2. lépés: Kapcsolati adatok megadása a tesztfelhasználónál
Több lehetősége van arra, hogyan adhatja meg a szervezetében lévő felhasználók adatait a jelszó-visszaállításhoz.

-   A felhasználók szerkesztése az [Azure felügyeleti portálon](https://manage.windowsazure.com) vagy az [Office 365 felügyeleti portálon](https://portal.microsoftonline.com)
-   Az AAD Connect használata a felhasználói tulajdonságok a helyszíni Active Directory-tartományból az Azure AD-be történő szinkronizálásához
-   Felhasználói tulajdonságok szerkesztése a Windows PowerShell használatával
-   Ha lehetővé szeretné tenni, hogy a felhasználók regisztrálhassák a saját adataikat, irányítsa őket a [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) címen elérhető regisztrációs portálra
-   Ha azt szeretné, hogy a felhasználóknak regisztrálniuk kelljen a jelszó-visszaállításhoz, amikor bejelentkeznek a Hozzáférési panelre a [http://myapps.microsoft.com](http://myapps.microsoft.com) címen, állítsa **Felhasználói regisztráció megkövetelése a hozzáférési panelen történő bejelentkezéskor** SSPR-konfigurációs beállítást **Igen** értékre.

Ha további információt szeretne megtudni arról, hogy milyen adatokat használ a jelszóvisszaállítás, valamint az adatok formázási követelményeiről, lásd: [Milyen adatokat használ a jelszóvisszaállítás?](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset).

#### Felhasználói kapcsolati adatok hozzáadása a Felhasználói regisztrációs portálon
1.  A jelszó-visszaállítási portál használatához meg kell adnia a szervezetében lévő felhasználóknak ezen oldal hivatkozását ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)), vagy be kell kapcsolnia a felhasználók automatikus regisztrációját megkövetelő beállítást.  Amikor erre a hivatkozásra kattintanak, a rendszer megkéri őket, hogy jelentkezzenek be a szervezeti fiókjukkal.  Ezután a következő oldalt láthatják:

    ![][007]

2.  Itt a felhasználók megadhatják és ellenőriztethetik a mobiltelefonjukat, másodlagos e-mail-címüket vagy biztonsági kérdéseiket.  Így néz ki egy mobiltelefon ellenőriztetése.

    ![][008]

3.  Miután a felhasználó megadja ezeket az információkat, az oldal frissül, hogy jelezze az információk érvényességét (az alábbiakban ezt kitakartuk).  Ha a **Befejezés** vagy a **Mégse** gombra kattint, a felhasználó a Hozzáférési panelre kerül.

    ![][009]

4.  Amikor a felhasználó ellenőriztette mindkét információt, frissül a profilja a megadott adatokkal.  Ebben a példában az **Irodai telefonszám** adatait módosították manuálisan, hogy a felhasználó ezt a kapcsolattartási módot is használhassa a jelszó visszaállítására.

    ![][010]

### 3. lépés: Az AzureAD-jelszó visszaállítása felhasználóként
Most, hogy konfigurált egy felhasználói visszaállítási házirendet, és megadta a felhasználó kapcsolattartási adatait, ez a felhasználó önkiszolgáló jelszó-visszaállítást végezhet.

#### Önkiszolgáló jelszó-visszaállítás elvégzése
1.  Ha például a [**portal.microsoftonline.com**](http://portal.microsoftonline.com) oldalra lép, az alábbihoz hasonló bejelentkezési képernyőt lát.  Kattintson a **Nem tudja elérni a fiókját?** hivatkozásra a jelszó-visszaállítási felhasználói felület teszteléséhez.

    ![][011]

2.  Miután a **Nem tudja elérni a fiókját?** hivatkozásra kattint, egy új lapra kerül, amely bekéri azt a **felhasználói azonosítót**, amelyhez vissza szeretné állítani a jelszót.  Írja be ide a **felhasználói azonosítót**, teljesítse a captcha-tesztet, majd kattintson a **tovább** gombra.

    ![][012]

3.  Mivel a felhasználó ebben az esetben **irodai telefonszámot**, **mobiltelefonszámot** és **másodlagos e-mail-címet** adott meg, láthatja, hogy mindezek lehetőségként megjelennek az első ellenőrzésnél.

    ![][013]

4.  Ebben az esetben először válassza az **irodai telefonszám** **felhívását**.  Vegye figyelembe, hogy amikor telefonalapú módszert választ, a rendszer felkéri a felhasználókat **a telefonszámuk megerősítésére**, mielőtt visszaállíthatnák a jelszavukat.  Ez azért van, hogy a rosszindulatú támadók ne élhessenek vissza a szervezetben lévő felhasználók telefonszámaival.

    ![][014]

5.  Amikor a felhasználó megerősítette a telefonszámát, a hívás gombra kattintva megjelenik egy forgó ábra, és elkezd csengeni a telefonja.  Amikor felveszi a telefont, egy üzenetet hall, és felkérik, hogy a **nyomja le a „#” billentyűt** a fiókja ellenőrzéséhez.  A billentyű lenyomásával automatikusan megerősíti, hogy a felhasználó teljesítette az első ellenőrzést, és a felhasználói felület a második ellenőrzési lépéssel folytatja.

    ![][015]

6.  Miután teljesítette az első ellenőrzést, a felhasználói felület automatikusan frissül, hogy az már ne szerepeljen a lehetőségek listájában.  Ebben az esetben, mivel először az **Irodai telefonszámát** használta, csak a **Mobiltelefonszám** és a **Másodlagos e-mail-cím** marad lehetőségként a második ellenőrzési lépéshez.  Kattintson a **Másodlagos e-mail-cím** lehetőségre.  Miután ezt megtette, az e-mail gombra kattintva a rendszer küld egy e-mailt a tárolt másodlagos e-mail-címre.

    ![][016]

7.  Itt láthat a felhasználók által megkapott e-mailekre egy példát – amelyen létható a bérlői arculat is:

    ![][017]

8.  Amikor megérkezik az e-mail, az oldal frissül, és be tudja írni az e-mailben található ellenőrző adatokat az alábbi beviteli mezőbe.  A megfelelő kód beírása után aktívvá válik a tovább gomb, és teljesítheti a második ellenőrzési lépést.

    ![][018]

9.  Ha teljesítette a szervezeti házirend követelményeit, új jelszót választhat.  A jelszó akkor fogadható el, ha megfelel az AAD „erős” jelszókövetelményeinek (lásd: [Jelszóházirend az Azure AD-ben](https://msdn.microsoft.com/library/azure/jj943764.aspx)), és megjelenik egy jelszóerősség-értékelő annak jelzéséhez, hogy a beírt jelszó megfelel-e a házirendnek.

    ![][019]

10. Miután megadta a szervezeti házirendnek megfelelő jelszavát, a jelszó alaphelyzetbe áll, és azonnal bejelentkezhet az új jelszavával.

    ![][020]


## Annak engedélyezése, hogy a felhasználók visszaállíthassák vagy módosíthassák az AD-jelszavaikat

Ez a szakasz a jelszó-visszaállítás konfigurálásán vezeti végig annak érdekében, hogy jelszavakat írhasson vissza a helyszíni Active Directoryba.

- [Jelszóvisszaírás előfeltételei](#writeback-prerequisites)
- [1. lépés: Az Azure AD Connect legújabb verziójának letöltése](#step-1-download-the-latest-version-of-azure-ad-connect)
- [2. lépés: A jelszóvisszaírás engedélyezése az Azure AD Connectben a felhasználói felületen vagy a PowerShellen keresztül, és ellenőrzés](#step-2-enable-password-writeback-in-azure-ad-connect)
- [3. lépés: A tűzfal konfigurálása](#step-3-configure-your-firewall)
- [4. lépés: A megfelelő engedélyek beállítása](#step-4-set-up-the-appropriate-active-directory-permissions)
- [5. lépés: Az AD-jelszó visszaállítása felhasználóként, és ellenőrzés](#step-5-reset-your-ad-password-as-a-user)


### A visszaírás előfeltételei
Mielőtt engedélyezhetné és használhatná a Jelszóvisszaírót, eleget kell tennie a következő előfeltételeknek:

- Rendelkezik egy Azure AD-bérlővel, és engedélyezve van az Azure AD Prémium.  További információk: [Azure Active Directory editions](active-directory-editions.md) (Azure Active Directory-kiadások).
- A jelszó-visszaállítás konfigurálva van és engedélyezve van a bérlőn.  További információkért lásd: [Annak engedélyezése, hogy a felhasználók visszaállíthassák az Azure AD-jelszavaikat](#enable-users-to-reset-their-azure-ad-passwords)
- Van legalább egy rendszergazdai fiókja és egy tesztfelhasználói fiókja Azure AD Prémium licenccel, amellyel tesztelheti ezt a funkciót.  További információk: [Azure Active Directory editions](active-directory-editions.md) (Azure Active Directory-kiadások).

  > [AZURE.NOTE] Győződjön meg róla, hogy a Jelszóvisszaíró engedélyezéséhez használt rendszergazdai fiók felhőalapú rendszergazdai fiók (amely az Azure AD-ben jött létre), nem pedig összevont fiók (amely helyszíni AD-ben jött létre, és az Azure AD-be lett szinkronizálva).

- Egyetlen vagy több erdőből álló helyszíni AD-környezettel rendelkezik, amelyik a Windows Server 2008, Windows Server 2008 R2, Windows Server 2012 vagy Windows Server 2012 R2 rendszert futtatja a telepített legújabb szervizcsomagokkal.

  > [AZURE.NOTE] Ha a Windows Server 2008 vagy 2008 R2 régebbi verzióját futtatja, továbbra is használhatja ezt a funkciót, de [le kell töltenie és telepítenie kell a KB 2386717 javítást](https://support.microsoft.com/kb/2386717), mielőtt érvénybe léptethetné a helyi AD-jelszóházirendet a felhőben.

- Telepítve van az Azure AD Connect eszköz, és előkészítette az AD-környezetet a felhőbe történő szinkronizáláshoz.  További információért lásd: [A helyszíni identitás-infrastruktúra használata a felhőben](active-directory-aadconnect.md).

  > [AZURE.NOTE] A jelszóvisszaírás tesztelése előtt először végezzen teljes importálást és teljes szinkronizálást az AD-ből és az Azure AD-ből is az Azure AD Connectben.

- Ha az Azure AD Sync vagy az Azure AD Connect eszközt használja, akkor a kimenő **443-as TCP-portnak** (és néhány esetben a kimenő **9350–9354 TCP-portnak**) nyitva kell lennie.  További információért lásd: [3. lépés: A tűzfal konfigurálása](#step-3-configure-your-firewall). Már nem támogatott a DirSync használata ehhez a forgatókönyvhöz.  Ha még mindig a DirSync eszközt használja, frissítsen az Azure AD Connect legújabb verziójára a Jelszóvisszaíró telepítése előtt.

  > [AZURE.NOTE] Különösen ajánlott, hogy az Azure AD Sync vagy a DirSync eszközöket használó minden felhasználó frissítsen az Azure AD Connect legújabb verziójára, hogy a lehető legjobb élményben legyen részük, és elérhetők legyenek számukra az új funkciók azok kiadásakor.


### 1. lépés: Az Azure AD Connect legújabb verziójának letöltése
A Jelszóvisszaíró az Azure AD Connectben vagy az **1.0.0419.0911**-es vagy újabb verziójú Azure AD Sync eszközben érhető el.  A Jelszóvisszaíró automatikus fiókzárolás-feloldással az Azure AD Connectben vagy az **1.0.0485.0222**-es vagy újabb verziójú Azure AD Sync eszközben érhető el. Ha régebbi verziót futtat, frissítsen legalább erre a verzióra a folytatás előtt. [Kattintson ide az Azure AD Connect legújabb verziójának letöltéséhez](active-directory-aadconnect.md#install-azure-ad-connect).

#### Az Azure AD Sync verziójának ellenőrzése
1.  Nyissa meg a **%ProgramFiles%\Azure Active Directory Sync\** könyvtárat.
2.  Keresse meg a **ConfigWizard.exe** végrehajtható fájlt.
3.  Kattintson a jobb gombbal a végrehajtható fájlra, és válassza ki a **Tulajdonságok** elemet a helyi menüből.
4.  Kattintson a **Részletek** lapra.
5.  Keresse meg a **Fájlverzió** mezőt.

    ![][021]

Ha ez a verziószám nagyobb vagy egyenlő, mint **1.0.0419.0911**, vagy ha az Azure AD Connectet telepíti, továbbléphet a [2. lépés: A jelszóvisszaírás engedélyezése az Azure AD Connectben a felhasználói felületen vagy a PowerShellen keresztül, és ellenőrzés](#step-2-enable-password-writeback-in-azure-ad-connect) című szakaszra.

 > [AZURE.NOTE] Ha most telepíti először az Azure AD Connect eszközt, a figyelmébe ajánlunk néhány ajánlott eljárást, hogy előkészítse a környezetét a címtár-szinkronizálásra.  Az Azure AD Connect eszköz telepítése előtt aktiválnia kell a címtár-szinkronizálást az [Office 365 felügyeleti portálon](https://portal.microsoftonline.com) vagy az [Azure felügyeleti portálon](https://manage.windowsazure.com).  További információért lásd: [Az Azure AD Connect kezelése](active-directory-aadconnect-whats-next.md).


### 2. lépés: A jelszóvisszaírás engedélyezése az Azure AD Connectben
Most, hogy letöltötte az Azure AD Connect eszközt, készen áll a jelszóvisszaírás engedélyezésére.  Ezt az alábbi két módszer egyikével teheti meg.  Engedélyezheti a jelszóvisszaírást az Azure AD Connect telepítővarázsló választható funkciókat felsoroló képernyőjén, vagy engedélyezheti a Windows PowerShellen keresztül.

#### A jelszóvisszaírás engedélyezése a konfigurációs varázslóban
1.  A **Directory Sync számítógépén** nyissa meg az **Azure AD Connect** konfigurációs varázslóját.
2.  Kattintsa végig a lépéseket, amíg el nem éri a **választható funkciók** konfigurációs képernyőt.
3.  Jelölje be a **Jelszóvisszaírás** lehetőséget.

    ![][022]

4.  Fejezze be a varázsló lépéseit, és az utolsó oldal tartalmazza a módosításokat és a jelszóvisszaírás konfigurációjának módosítását.

> [AZURE.NOTE] Bármikor letilthatja a jelszóvisszaírást a varázsló ismételt futtatásával és a funkció jelölésének törlésével, vagy a **Jelszavak visszaírása helyszíni címtárba** beállítás **Nem** értékre állításával a címtár **Konfigurálás** lapjának **Felhasználói jelszó-visszaállítási házirend** szakaszában az [Azure felügyeleti portálon](https://manage.windowsazure.com).  További információ a jelszóvisszaállítási felület testreszabásáról: [Testreszabás: Azure AD-jelszókezelés](active-directory-passwords-customize.md).

#### A jelszóvisszaírás engedélyezése a Windows PowerShell használatával
1.  A **Directory Sync számítógépén** nyisson meg egy új **emelt szintű Windows PowerShell-ablakot**.
2.  Ha a modul még nincs betöltve, írja be az `Import-Module ADSync` parancsot, hogy az Azure AD Connect-parancsmagokat betöltse az aktuális munkamenetbe.
3.  A rendszeren lévő AAD-összekötők listájának lekéréséhez futtassa a `Get-ADSyncConnector` parancsmagot, és tárolja az eredményeket a következőben: `$aadConnectorName`
4.  Az aktuális összekötő aktuális visszaírási állapotának lekéréséhez futtassa a következő parancsmagot: `Get-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName`
5.  A jelszóvisszaírás engedélyezéséhez futtassa a következő parancsmagot: `Set-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName –Enable $true`

> [AZURE.NOTE] Ha a rendszer bekéri a hitelesítő adatait, győződjön meg róla, hogy az AzureADCredential hitelesítő adatokhoz megadott rendszergazdai fiók egy **felhőalapú rendszergazdai fiók** legyen (amely az Azure AD-ben jött létre), nem pedig összevont fiók (amely helyszíni AD-ben jött létre, és az Azure AD-be lett szinkronizálva).
> [AZURE.NOTE] Letilthatja a jelszóvisszaírást a PowerShellen keresztül a fenti eljárást megismételve, csak adja meg a `$false` értéket, vagy állítsa a **Jelszavak visszaírása helyszíni címtárba** beállítást **Nem** értékre a címtár **Konfigurálás** lapjának **Felhasználói jelszó-visszaállítási házirend** szakaszában az [Azure felügyeleti portálon](https://manage.windowsazure.com).

#### Ellenőrizze, hogy sikeres volt-e a konfiguráció
Ha a konfiguráció sikeres, a jelszóvisszaállítás engedélyezett állapotáról szóló üzenetet lát a Windows PowerShell-ablakban, vagy a sikerről szóló üzenetet a konfigurációs felhasználói felületen.

A szolgáltatás megfelelő telepítését úgy is ellenőrizheti, ha megnyitja az Eseménynaplót, megkeresi az alkalmazás eseménynaplóját, és megkeresi a **31005 - OnboardingEventSuccess** eseményt a **PasswordResetService** forrásból.

  ![][023]

### 3. lépés: A tűzfal konfigurálása
Miután engedélyezte a jelszóvisszaírást az Azure AD Connect eszközben, ellenőriznie kell, hogy tud-e csatlakozni a felhőhöz a szolgáltatás.

1.  Miután befejeződött a telepítés, ha ismeretlen kimenő kapcsolatokat blokkol a környezetben, a következő szabályokat is hozzá kell adnia a tűzfalhoz. Ellenőrizze, hogy újraindította-e az AAD Connect gépét a módosítások elvégzése után:
   - Kimenő kapcsolatok engedélyezése a 443-as TCP-porton keresztül
   - Kimenő kapcsolatok engedélyezése a https://ssprsbprodncu-sb.accesscontrol.windows.net/ címre
   - Amikor proxyt használ, vagy általános kapcsolati hibákat tapasztal, engedélyezze a kimenő kapcsolatokat a 9350–9354-es és az 5671-es TCP-porton keresztül.

### 4. lépés: A megfelelő Active Directory-engedélyek beállítása
Minden erdő esetén, amely olyan felhasználókat tartalmaz, akiknek a jelszavait vissza fogja állítani, ha X a konfigurációs varázslóban (a kezdeti konfiguráció során) az erdőhöz meghatározott fiók, az X-hez meg kell adnia a **Jelszó alaphelyzetbe állítása**, **Jelszó módosítása**, **Írási engedélyek** kibővített jogot a `lockoutTime` alatt és az **Írási engedélyek** kibővített jogot a `pwdLastSet` alatt az erdőben lévő minden tartomány gyökérobjektumán. A jognak minden felhasználói objektum által örököltként kell megjelölve lennie.  

Ha nem biztos abban, melyik fiókra utalnak a fentiek, nyissa meg az Azure Active Directory Connect konfigurációs felhasználói felületet, és kattintson **A megoldás áttekintése** elemre.  A fiók, amelyhez engedélyeket kell hozzáadnia, vörös színnel van aláhúzva az alábbi képernyőképen.

**<font color="red">Ellenőrizze, hogy beállította-e ezt az engedélyt a rendszeren lévő összes erdő minden tartományánál, különben a jelszóvisszaírás nem fog megfelelően működni.</font>**

  ![][032]

  Ezen engedélyek beállításakor az egyes erdők MA-szolgáltatásfiókja kezelheti a jelszavakat az erdőben lévő felhasználói fiókok nevében. Ha nem rendeli hozzá ezeket az engedélyeket, akkor bár a visszaírás konfigurációja megfelelőnek tűnik, a felhasználók hibákat tapasztalnak, amikor a helyszíni jelszavakat próbálják meg kezelni a felhőből. Itt láthatja ennek részletes lépéseit **Az Active Directory – felhasználók és számítógépek** felügyeleti beépülő modul használatával:

>[AZURE.NOTE] Akár egy órába is telhet, amíg ezen engedélyeket a címtár összes objektumába replikálja.

#### A megfelelő engedélyek beállítása, hogy a visszaírás megtörténjen

1.  Nyissa meg az **Active Directory – felhasználók és számítógépek** modult egy olyan fiókkal, amely megfelelő tartományi rendszergazdai engedélyekkel rendelkezik.
2.  A **Nézet menüben** győződjön meg róla, hogy a **Speciális szolgáltatások** be van kapcsolva.
3.  A bal oldali panelen kattintson a jobb gombbal a tartomány gyökerét jelző objektumra.
4.  Kattintson a **Biztonság** lapra.
5.  Ezután kattintson a **Speciális** elemre.

    ![][024]

6.  Az **Engedélyek** lapon kattintson a **Hozzáadás** elemre.

    ![][025]

7.  Válassza ki azt a fiókot, amelyhez engedélyeket szeretne hozzáadni (ez ugyanaz a fiók, amelyet az erdő szinkronizálásának beállításakor adott meg).
8.  A felül lévő legördülő listából válassza a **Leszármazott felhasználó objektumai** elemet.
9.  A megjelenő **Engedélybejegyzés** párbeszédpanelen jelölje be a **Jelszó alaphelyzetbe állítása**, **Jelszó módosítása**, **Írási engedélyek** jelölőnégyzetet a `lockoutTime` alatt, és az **Írási engedélyek** jelölőnégyzetet a `pwdLastSet` alatt.

    ![][026]
    ![][027]
    ![][028]

10. Ezután kattintson az **Alkalmaz/OK** gombra az összes megnyitott párbeszédpanelen.

### 5. lépés: Az AD-jelszó visszaállítása felhasználóként
Most, hogy engedélyezett a jelszóvisszaírás, tesztelheti a működését, ha visszaállítja a felhőalapú bérlőben szinkronizált fiókkal rendelkező felhasználó jelszavát.

#### A jelszóvisszaírás megfelelő működésének ellenőrzése
1.  Nyissa meg a [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com) oldalt, vagy valamely szervezeti azonosítót kérő bejelentkezési képernyőt, és kattintson a **Nem tudja elérni a fiókját?** hivatkozásra.

    ![][029]

2.  Most egy új lapot kell látnia, amely bekéri azt a felhasználói azonosítót, amelyhez vissza szeretné állítani a jelszót. Írja be a tesztfelhasználó azonosítóját, és folytassa a jelszóvisszaállítási folyamatot.
3.  A jelszó visszaállítása után egy ehhez hasonló képernyőt lát. Ez azt jelenti, hogy sikeresen visszaállította a jelszavát a helyszíni és/vagy a felhőbeli címtárakban.

    ![][030]

4.  A művelet sikerének vagy a hibák diagnosztizálásának érdekében váltson a **Directory Sync számítógépére**, nyissa meg az **Eseménynaplót**, keresse meg az **alkalmazás eseménynaplóját**, és keresse meg a **31002 - PasswordResetSuccess** eseményt a tesztfelhasználó forrás **PasswordResetService** területén.

    ![][031]


<br/>
<br/>
<br/>

## A jelszóvisszaállítási dokumentáció hivatkozásai
Az alábbiakban láthatja az összes Azure AD-jelszóvisszaállítási dokumentációs oldal hivatkozását:

* **Azért van itt, mert problémák merültek fel a bejelentkezéssel kapcsolatban?** Ha igen, [így módosíthatja vagy állíthatja alaphelyzetbe a jelszavát](active-directory-passwords-update-your-own-password.md).
* [**Működés**](active-directory-passwords-how-it-works.md) – megismerheti a szolgáltatás hat különböző összetevőjét és azt, hogy ezek mire valók
* [**Testreszabás**](active-directory-passwords-customize.md) – megtudhatja, hogyan szabhatja testre a szervezet által igényelt szolgáltatás kezelőfelületét és működését
* [**Ajánlott eljárások**](active-directory-passwords-best-practices.md) – megtudhatja, hogyan helyezhet gyorsan üzembe és hogyan kezelhet hatékonyan jelszavakat a szervezetben
* [**Elemzések lekérése**](active-directory-passwords-get-insights.md) – megismerheti az integrált jelentéskészítési képességeinket
* [**Gyakori kérdések**](active-directory-passwords-faq.md) – választ kaphat a gyakori kérdésekre
* [**Hibaelhárítás**](active-directory-passwords-troubleshoot.md) – megtudhatja, hogyan háríthatja el a szolgáltatással kapcsolatos problémákat
* [**További információ**](active-directory-passwords-learn-more.md) – elmerülhet a szolgáltatás működésének műszaki részleteiben



[001]: ./media/active-directory-passwords-getting-started/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-getting-started/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-getting-started/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-getting-started/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-getting-started/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-getting-started/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-getting-started/007.jpg "Image_007.jpg"
[008]: ./media/active-directory-passwords-getting-started/008.jpg "Image_008.jpg"
[009]: ./media/active-directory-passwords-getting-started/009.jpg "Image_009.jpg"
[010]: ./media/active-directory-passwords-getting-started/010.jpg "Image_010.jpg"
[011]: ./media/active-directory-passwords-getting-started/011.jpg "Image_011.jpg"
[012]: ./media/active-directory-passwords-getting-started/012.jpg "Image_012.jpg"
[013]: ./media/active-directory-passwords-getting-started/013.jpg "Image_013.jpg"
[014]: ./media/active-directory-passwords-getting-started/014.jpg "Image_014.jpg"
[015]: ./media/active-directory-passwords-getting-started/015.jpg "Image_015.jpg"
[016]: ./media/active-directory-passwords-getting-started/016.jpg "Image_016.jpg"
[017]: ./media/active-directory-passwords-getting-started/017.jpg "Image_017.jpg"
[018]: ./media/active-directory-passwords-getting-started/018.jpg "Image_018.jpg"
[019]: ./media/active-directory-passwords-getting-started/019.jpg "Image_019.jpg"
[020]: ./media/active-directory-passwords-getting-started/020.jpg "Image_020.jpg"
[021]: ./media/active-directory-passwords-getting-started/021.jpg "Image_021.jpg"
[022]: ./media/active-directory-passwords-getting-started/022.jpg "Image_022.jpg"
[023]: ./media/active-directory-passwords-getting-started/023.jpg "Image_023.jpg"
[024]: ./media/active-directory-passwords-getting-started/024.jpg "Image_024.jpg"
[025]: ./media/active-directory-passwords-getting-started/025.jpg "Image_025.jpg"
[026]: ./media/active-directory-passwords-getting-started/026.jpg "Image_026.jpg"
[027]: ./media/active-directory-passwords-getting-started/027.jpg "Image_027.jpg"
[028]: ./media/active-directory-passwords-getting-started/028.jpg "Image_028.jpg"
[029]: ./media/active-directory-passwords-getting-started/029.jpg "Image_029.jpg"
[030]: ./media/active-directory-passwords-getting-started/030.jpg "Image_030.jpg"
[031]: ./media/active-directory-passwords-getting-started/031.jpg "Image_031.jpg"
[032]: ./media/active-directory-passwords-getting-started/032.jpg "Image_032.jpg"



<!--HONumber=sep16_HO1-->


