---
title: A Kerberos általi korlátozott delegálás konfigurációi alkalmazásproxy hibaelhárítása |} A Microsoft Docs
description: A Kerberos által korlátozott delegálás konfigurációi alkalmazásproxy hibaelhárítása
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: ad2140d9d94cc4655043625200d42485b03c719b
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364291"
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>A Kerberos általi korlátozott delegálás konfigurációi alkalmazásproxy hibaelhárítása

Az SSO megvalósításához a közzétett alkalmazásokhoz elérhető módszerek eltérőek lehetnek, egyik alkalmazásból a másikba. Az egyik lehetőség, amely alapértelmezés szerint biztosít az Azure Active Directory (Azure AD) alkalmazásproxy a Kerberos által korlátozott delegálás (KCD). Konfigurálhat egy összekötőt, a felhasználók számára, a korlátozott Kerberos-hitelesítés háttér-alkalmazások futtatásához.

Az eljárás KCD engedélyezésének rendkívül egyszerű. Nem a több, mint a különböző összetevőkkel és hitelesítési folyamat általános ismerete, amely támogatja az egyszeri Bejelentkezést van szükség. De egyes esetekben KCD egyszeri bejelentkezés nem fog megfelelően működni. Ezek a forgatókönyvek hibaelhárítása információkat kell.

Ez a cikk egy hivatkozást, amellyel a helyi javítani a leggyakoribb problémák és hibáinak elhárítása hibaérzékeny pont. Emellett ismerteti összetettebb megvalósítási problémák diagnosztizálására.

Ez a cikk feltételezésekre a következő:

-   Üzembe helyezés az Azure AD-alkalmazásproxy kiszolgálónként [alkalmazásproxy – első lépések](manage-apps/application-proxy-enable.md) és általános alkalmazás-hozzáférési nem KCD a várt módon működik.

-   A közzétett célalkalmazás Internet Information Services (IIS) és a Microsoft általi implementációja Kerberos alapul.

-   A kiszolgáló- és állomások egy egyetlen Azure Active Directory-tartományban található. A tartományok közötti és erdők forgatókönyvek részletes információkért lásd: a [KCD tanulmány](https://aka.ms/KCDPaper).

-   A figyelt alkalmazást az Azure-ban közzétett előtti hitelesítés engedélyezve van a bérlőt. Az Azure-bA űrlapalapú hitelesítés-on keresztül hitelesítendő felhasználók várható. Ez a cikk gazdag ügyfél-hitelesítési forgatókönyvek nem terjed ki. Ezek hozzáadhatók valamikor a jövőben.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-alkalmazásproxy is telepíthető az infrastruktúra vagy a környezetek számos különböző típusú. Az architektúrák eltérőek lehetnek a szervezetben. A kcd Szolgáltatáshoz kapcsolódó hibák leggyakoribb okai a környezetekben nem. Egyszerű konfigurációs hibák vagy általános hibákat okozhat a legtöbb problémát.

Emiatt tanácsos győződjön meg arról, hogy megfelel-e az összes előfeltétel [KCD egyszeri bejelentkezés használata az alkalmazásproxyval](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md) hibaelhárítás megkezdése előtt. Megjegyzés: a szakasz a Kerberos által korlátozott delegálás konfigurálása a 2012 R2. Ez a folyamat a Kerberos konfigurálása a Windows korábbi verzióin eltérő megközelítést alkalmaz. Azt is szem előtt tartva ezeket a szempontokat:

-   Már nem ritka, hogy egy tartományi tagkiszolgáló, egy adott tartományvezérlővel (DC) a biztonságos csatornához párbeszédpanel megnyitásához. A kiszolgáló előfordulhat, hogy helyezze át egy másik párbeszédpanelen egy adott időpontban. Így az összekötő gazdagépek nem korlátozott csak bizonyos helyi hely DCs folytatott kommunikációra.

-   Tartományok közötti forgatókönyvek támaszkodik, amely egy összekötő gazdagépet a tartományvezérlőket, lehet, hogy a helyi hálózat pereme kívül közvetlen hivatkozásokat. Ezekben az esetekben fontos egyaránt is küldeni a forgalmat kezdve a tartományvezérlők, amelyek a saját tartományához. Ha nem, a delegálás nem sikerül.

-   Ahol lehetséges, helyez minden aktív IP-CÍMEK vagy az Azonosítók eszközök összekötő állomások és a tartományvezérlők között. Ezek az eszközök néha overintrusive és zavarják a core RPC-adatforgalmat.

Delegálás vizsgálat egyszerű forgatókönyvhöz. A több változót használ, előfordulhat, hogy minél ugyanazért a kell. Időt takaríthat meg, a tesztelés egy összekötőt korlátozza. További összekötők hozzáadása a probléma megoldása után.

Néhány környezeti tényezők is hozzájárulhat előfordulhat, hogy egy probléma. Ezek a tényezők elkerülése érdekében minimálisra csökkenthető a tesztelés során, a lehetséges architektúrát. Ha például gyakoriak helytelenül konfigurált belső tűzfal hozzáférés-vezérlési listák. Ha lehetséges küldött összes forgalom összekötő keresztül közvetlenül a tartományvezérlők és a háttéralkalmazás.

A legjobb hely a elhelyezése az összekötők a lehető a tárolókra. Beágyazott helyezkedik el, ha teszteli tűzfal hozzáadja a szükségtelen összetettséget, és is meghosszabbítja a vizsgálatok.

Mi látható KCD probléma? Nincsenek számos gyakori megjelölések KCD SSO nem működik. Az első jelentkezik a probléma jelennek meg a böngészőben.

   ![Helytelen KCD konfigurációs hiba](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

   ![Nem sikerült, mert nem rendelkezik engedéllyel engedélyezési](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

Mindkét ezeket a lemezképeket a azonos tünet megjelenítése: egyszeri bejelentkezés sikertelen. Az alkalmazás felhasználói hozzáférés megtagadva.

## <a name="troubleshooting"></a>Hibaelhárítás

Hogyan háríthatóak attól függ, a problémát, és a tünetek megfigyelte. Előtt minden távolabb, Fedezze fel a következő cikkekben talál. Hasznos információkat nyújtanak:

-   [Az alkalmazásproxy-problémák és hibaüzenetek hibaelhárítása](manage-apps/application-proxy-troubleshoot.md)

-   [A Kerberos hibákat és a tünetek](manage-apps/application-proxy-troubleshoot.md#kerberos-errors)

-   [Egyszeri bejelentkezés használata során a helyszíni és felhőalapú Identitások nem azonos](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

Ha itt van erre a pontra, majd a fő probléma áll fenn. Indításához válassza el a flow-t, amely háríthatóak el a következő három fázisa.

### <a name="client-pre-authentication"></a>Üzem előtti ügyfél-hitelesítés 
A külső felhasználó hitelesítése az Azure-bA egy böngészőből. Előre hitelesíteni tud az Azure-ban KCD SSO működéséhez szükséges. Tesztelése, és oldja ezt a lehetőséget, ha merül fel. Az üzem előtti hitelesítés szakaszban KCD vagy a közzétett alkalmazás nem kapcsolódik. Könnyedék kijavíthatja az esetleges eltéréseket megerősítést ellenőrzésével, hogy a tárgy fiók létezik, az Azure-ban. Ellenőrizze, hogy nem is le van tiltva, vagy letiltott. A hiba válasz a böngészőben elég leíró annak magyarázata, okát. Ha bizonytalan, ellenőrizze az egyéb Microsoft hibaelhárítási cikkek ellenőrzése.

### <a name="delegation-service"></a>Szolgáltatás delegálása 
Az Azure alkalmazásproxy-összekötő, amely egy Kerberos-szolgáltatásjegyet lekérdezi a felhasználók számára egy Kerberos-Key terjesztési Center (KCD) a.

A külső kommunikáció az ügyfél és az Azure előtér között nincs hatással a kcd Szolgáltatáshoz rendelkezik. Ezek a kommunikációk csak győződjön meg arról, hogy működik-e a kcd Szolgáltatáshoz. Az Azure Proxy szolgáltatás biztosítja, hogy egy Kerberos-jegyet használnak érvényes felhasználói azonosító. Ez az azonosító nélkül KCD nem lehetséges, így sikertelen lesz.

Ahogy korábban említettük, a böngésző hibaüzenetek biztosít néhány jó a keresőmotorok arról, hogy miért nem sikerül a dolgokat. Ellenőrizze, hogy jegyezze fel a tevékenység azonosítója és a válaszban szereplő időbélyeget. Ez az információ segít összehasonlíthatja a tényleges eseményekre az Azure Proxy eseménynaplóban viselkedését.

   ![Helytelen KCD konfigurációs hiba](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

A megfelelő bejegyzéseket az Eseménynaplóban látható események 13019 vagy 12027 szabályzatként jelenik meg. Keresse meg az összekötő eseménynaplók **alkalmazások és szolgáltatásnaplók** &gt; **Microsoft** &gt; **AadApplicationProxy** &gt;  **Összekötő** &gt; **rendszergazdai**.

   ![Az alkalmazásproxy az eseménynaplóból 13019 esemény](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

   ![Az alkalmazásproxy az eseménynaplóból 12027 esemény](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

1.   Használja az **A** bejegyzés az alkalmazás-cím, a belső DNS-ben nem egy **CName**.

2.   Újra erősítsék meg, hogy az összekötő állomás kapott a jogot arra, hogy a kijelölt célfiók SPN delegálni. Ismételt megerősítését, amely **bármely hitelesítési protokoll** van kiválasztva. További információkért lásd: a [egyszeri bejelentkezési konfiguráció cikk](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md).

3.   Ellenőrizze, hogy létezik-e az Azure ad-ben az SPN csak egy példánya. A probléma `setspn -x` minden tartományi tag gazdagépen parancsot a parancssorba.

4.   Ellenőrizze, hogy tartományi házirend van érvényben, amely korlátozza a [Kerberos kiállított jogkivonatok maximális mérete](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/). Ez a szabályzat leállítja az összekötő jogkivonatának beolvasása, ha túl nagy megtalálható.

A hálózati nyomkövetés, amely rögzíti a cseréje az összekötő állomás és a egy tartomány KDC között, mint a legjobb részletekért fenn a problémák. További információkért lásd: a [haladóknak hibaelhárítás tanulmány](https://aka.ms/proxytshootpaper).

Ha jegykiadás megfelelőnek tűnik, a naplók, amely megállapítja, hogy a hitelesítés sikertelen volt, mert az alkalmazás adott vissza a 401-es esemény jelenik meg. Az esemény azt jelzi, hogy a célalkalmazás elutasította a jegyet. Nyissa meg a következő szakaszban.

### <a name="target-application"></a>Célalkalmazás 
A fogyasztó az összekötő által biztosított Kerberos jegy. Ezen a ponton várható az összekötő elküldött Kerberos szolgáltatásjegyet a háttérbe. A jegy az alkalmazás első kérés a fejlécet.

1.   Az alkalmazás belső megadott URL-cím a portál használatával, hogy az alkalmazás érhető el közvetlenül a böngészőből, az összekötő gazdagép ellenőrzése. Ezután bejelentkezhet sikeresen megtörtént. Az összekötő részletei találhatók **hibaelhárítás** lapot.

2.   Továbbra is az összekötő állomás erősítse meg, hogy a böngésző és az alkalmazás közötti hitelesítés a Kerberos-t használja. Hajtsa végre az alábbi műveletek egyikét:

3.  Futtatás DevTools (**F12**) a Internet Explorer vagy [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) az összekötő-gazdagépről. Nyissa meg az alkalmazás a belső URL-cím használatával. Vizsgálja meg a felajánlott WWW engedélyezési fejléceket, a visszaadott győződjön meg arról, hogy az alkalmazás válasza vagy egyeztetni vagy Kerberos megtalálható. 

    a. A következő Kerberos blob az alkalmazást a böngészőből érkező válaszban visszaadott kezdődik **YII**. Ezek a betűk jelzi, hogy a Kerberos fut-e. A Microsoft NT LAN Manager (NTLM), másrészt, mindig kezdődik **TlRMTVNTUAAB**, amely beolvassa az NTLM biztonsági támogatási szolgáltatóhoz (NTLMSSP) amikor dekódovat a Base64 kódolású anyag. Ha látja **TlRMTVNTUAAB** a blob elején Kerberos nem érhető el. Ha nem lát **TlRMTVNTUAAB**, Kerberos akkor valószínűséggel érhető el.

       > [!NOTE]
       > Fiddler használatakor ez a metódus szükséges, ideiglenesen letiltani az alkalmazást az IIS-ben a kiterjesztett védelem.

       ![Hálózati ellenőrzési böngészőablakban](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

    b. Ezen az ábrán a blob nem ezzel kezdődik **TIRMTVNTUAAB**. Ezért ebben a példában a Kerberos érhető el, és a Kerberos-blob nem ezzel kezdődik **YII**.

4.  NTLM ideiglenesen eltávolítása a szolgáltatók listája az IIS-webhelyen. Az alkalmazást elérheti a Internet Explorer, az összekötő-állomáson. NTLM már nem szerepel a szolgáltatók listája. Csak Kerberos használatával elérhetik az alkalmazást. Ha a hozzáférés meghiúsul, az alkalmazás konfigurációs probléma lehet. A Kerberos-hitelesítés nem működik.

    a. Ha a Kerberos nem érhető el, ellenőrizze az alkalmazás hitelesítési beállításait az IIS-ben. Győződjön meg arról, hogy **egyeztetés** szerepel a listán a lap tetején az NTLM-hez közvetlenül alatta. Ha látja **egyeztet**, **Kerberos vagy az egyeztetés**, vagy **PKU2U**, csak folytatja, ha a Kerberos egy funkcionális.

       ![Windows-hitelesítési szolgáltatók](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)
   
    b. A Kerberos és NTLM helyen ideiglenesen letilthatja az alkalmazás a portálon előhitelesítés során. Próbálja meg elérni az internetről a külső URL-cím használatával. Hitelesítést kér. Most már képes ehhez az előző lépésben használt ugyanazzal a fiókkal. Ha nem, a háttér-alkalmazással, nem KCD probléma merül fel.

    c. Engedélyezze újra a előtti hitelesítés, a portálon. Azure-on keresztül hitelesítést az alkalmazás a külső URL-CÍMEN keresztül csatlakozni próbál. Egyszeri bejelentkezés nem sikerül, egy a böngésző és az esemény 13022 a tiltott hibaüzenet jelenik meg:

       *Microsoft AAD alkalmazásproxy-összekötő nem tudja hitelesíteni a felhasználót, mert a háttérkiszolgáló válaszol egy HTTP 401-es hiba miatt a Kerberos hitelesítési kísérleteket.*

       ![HTTTP 401-es tiltott hibaüzenet](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

    d. Ellenőrizze az IIS-alkalmazás. Győződjön meg arról, hogy a konfigurált alkalmazáshoz a készletet és az egyszerű szolgáltatásnév használatára vannak konfigurálva ugyanazt a fiókot az Azure ad-ben. Keresse meg az IIS-ben, az alábbi ábrán látható módon:

       ![IIS alkalmazás konfigurációs ablaka](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

       Ha már tudja, hogy az identitás, ellenőrizze, hogy ennek a fióknak az egyszerű szolgáltatásnév van konfigurálva. Például: `setspn –q http/spn.wacketywack.com`. Adja meg a következő szöveget a parancsot a parancssorba:

       ![SetSPN parancssori ablakban](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

    e. Ellenőrizze az egyszerű Szolgáltatásnevet, az alkalmazás beállításait a portálon képest van meghatározva. Győződjön meg arról, hogy az azonos SPN-t a következő célon: Azure AD-fiókot konfigurálni az alkalmazáskészlet alkalmazás használja.

       ![Egyszerű szolgáltatásnév konfigurálása az Azure Portalon](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)
   
    f. Nyissa meg a távoli IIS, és válassza a **Konfigurációszerkesztő** az alkalmazás lehetőséget. Navigáljon a **system.webServer/security/authentication/windowsAuthentication**. Győződjön meg arról, hogy az érték **UseAppPoolCredentials** van **igaz**.

       ![Az IIS konfigurációs alkalmazás készletek hitelesítőadat-beállítás](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

       Módosítsa ezt az értéket **igaz**. A következő parancs futtatásával távolítsa el a gyorsítótárazott Kerberos-jegyet a háttér-kiszolgálóról:

       ```powershell
       Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
       ``` 

További információkért lásd: [végleges törlése a Kerberos jegy ügyfélgyorsítótár összes munkamenetet](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf).



Ha meghagyja a Kernel mód engedélyezve van, ez növeli a Kerberos-műveletek teljesítményét. De jelentkezésekor a jegyet a számítógépfiók használatával vissza kell fejteni a kért szolgáltatáshoz. Ennek a fióknak a helyi rendszer is nevezik. Ez az érték **igaz** KCD kezdetét, amikor az alkalmazás üzemel, a farm egynél több kiszolgáló között.

-   Egy további ellenőrzést, tiltsa le a **Extended** védelmi túl. Bizonyos esetekben **Extended** védelmi KCD érvénytelenítése, amikor az egyes konfigurációk engedélyezve lett. Ezekben az esetekben egy alkalmazás közzé lett téve megegyezik az alapértelmezett webhelyhez. Ez az alkalmazás csak a névtelen hitelesítésre van konfigurálva. A párbeszédpanelek útmutatásait szürkén jelennek meg, ami arra utal az gyermekobjektum nem örökli az aktív beállítások. Azt javasoljuk, hogy tesztelje, de ne felejtse el visszaállítani ezt az értéket **engedélyezve**, ahol csak lehetséges.

    A további ellenőrzést használatával, a közzétett alkalmazás használatának nyomon követése. További összekötők delegálása is konfigurált regisztrálhat. További információkért olvassa el a részletesebb technikai útmutató [hibáinak elhárítása az Azure AD-alkalmazásproxy](https://aka.ms/proxytshootpaper).

Ha még nem lehet a folyamatban, a Microsoft ügyfélszolgálatához a segítségére lehessen. Hozzon létre egy támogatási jegyet közvetlenül a portálon. Mérnökként felveszi Önnel a kapcsolatot.

## <a name="other-scenarios"></a>Egyéb forgatókönyvek

- Az Azure Application Proxy egy Kerberos-jegyet kérelmek egy alkalmazáshoz a kérelem elküldése előtt. Bizonyos külső alkalmazások, például a Tableau Server nem tetszik a rendszerhitelesítési módszert. Ezek az alkalmazások várhatóan a hagyományosabb tárgyalások kerül sor. Az első kérelem szolgáltatás névtelen, amely lehetővé teszi, hogy az alkalmazás számára, hogy a 401-es keresztül támogatja a hitelesítési típusok elhárítását.

- Többugrásos hitelesítési gyakran olyan forgatókönyvekben használatos, a alkalmazás rétegzett, háttér és kezelőfelület, ahol mindkettő hitelesítő adatokat kér, például az SQL Server Reporting Services. A teszi a többszörös ugrásos forgatókönyvet, lásd: támogatási cikk [Kerberos által korlátozott delegálás lehet szükséges Protokollátmenet Többugrásos forgatókönyvekben](https://support.microsoft.com/help/2005838/kerberos-constrained-delegation-may-require-protocol-transition-in-mul).

## <a name="next-steps"></a>További lépések
[Konfigurálja a Kerberos egy felügyelt tartományon](../active-directory-domain-services/active-directory-ds-enable-kcd.md).
