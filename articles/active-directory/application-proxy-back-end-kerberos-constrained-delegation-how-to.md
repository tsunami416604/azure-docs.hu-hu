---
title: "Az alkalmazásproxy alkalmazás Kerberos által korlátozott delegálás konfigurálása |} Microsoft Docs"
description: "Kerberos által korlátozott delegálás konfigurálása az Azure AD alkalmazásproxy alkalmazás módjáról."
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 3a768c30cb874d42d7b4fbd2eeaa6c0e23904e10
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-an-application-proxy-application-to-use-kerberos-constrained-delegation"></a>Az alkalmazásproxy alkalmazás Kerberos által korlátozott delegálás konfigurálása

A módszer áll rendelkezésre a közzétett alkalmazásokhoz SSO némileg eltérőek lehetnek alkalmazások elérésére, és az, hogy a Azure Application Proxy első használatkor jobb kínál lehetőségek közül a Kerberos által korlátozott delegálás (KCD). Ez azért, ahol egy összekötő állomás háttér alkalmazásokhoz, a felhasználók nevében korlátozott kerberos-hitelesítés elvégzéséhez van konfigurálva.

A Kerberos által korlátozott Delegálás engedélyezéséhez a tényleges eljárás viszonylag egyszerű, és általában a nem több, mint a különböző összetevőkkel és hitelesítési folyamat, amely elősegíti a SSO általános ismeretének igényelnek. Jó információforrásokat forgatókönyvek hibaelhárítás elősegítése érdekében ahol Kerberos által korlátozott Delegálás SSO nem működik megfelelően, keresése ritka lehet.

Ez a cikk, megpróbálja, hibaelhárításához és önálló szervizelése látható kapcsolatos leggyakoribb hibák némelyike segítségül a hivatkozás egyetlen pontot biztosítani. Egy időben az összetettebb és végrehajtási törlesztett diagnosztizálására további útmutatást kínál.

Vegye figyelembe, hogy ez a cikk esetén a következő előfeltételek:

-   Azure-alkalmazásproxy megfelelően van telepítve a [dokumentáció](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) és nem a Kerberos által korlátozott Delegálás alkalmazások általános hozzáférést a várt módon működik.

-   A közzétett célalkalmazás kerberos IIS és a Microsoft végrehajtásának alapul.

-   A kiszolgáló és az alkalmazás gazdagépek egyetlen Active Directory-tartományban találhatók. Részletes tájékoztatást közötti tartományok és erdők forgatókönyvek megtalálhatók a [Kerberos által korlátozott Delegálás tanulmány](http://aka.ms/KCDPaper).

-   A tulajdonos alkalmazás közzé van téve egy Azure-ban bérlői előtti hitelesítés engedélyezve van, és a felhasználók-azonosítóknak hitelesítésre űrlap alapú hitelesítéssel. Gazdag ügyfél-hitelesítési forgatókönyvei nem vonatkozik ez a cikk, de egy bizonyos ponton a jövőben hozzáadni.

## <a name="prerequisites"></a>Előfeltételek

Azure-alkalmazásproxy lényegében bármilyen típusú infrastruktúra vagy a környezet is telepíthető, és a architektúrák minden bizonnyal eltérők lehetnek a szervezetben. A Kerberos által korlátozott Delegálás a leggyakoribb okok egyike kapcsolatos problémák nincsenek-e a környezetekben, de meglehetősen egyszerű hibás konfigurációt vagy általános felügyeletet.

Ezért azt tanácsoljuk, mindig indítása a annak biztosítása, hogy a fő jelenjen meg minden előfeltétel teljesül [Kerberos által korlátozott Delegálás egyszeri bejelentkezés használatával az alkalmazásproxy-cikk](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) elindítása előtt a hibaelhárítás.

Különösen a szakasz a Kerberos által korlátozott Delegálás konfigurálása 2012R2, mivel ez előző verziók Windows de ugyanakkor nem számos szempontot szem előtt tartva a Kerberos által korlátozott Delegálás konfigurálása alapvetően eltérő megközelítést alkalmazza:

-   A rendszer nem ritka, hogy az egy tartományi tagkiszolgáló, egy adott tartományvezérlő biztonságos csatorna párbeszédpanel megnyitásához. Helyezze át egy másik párbeszédpanelt egy adott időpontban, összekötő állomások általában nem kell korlátozni csak adott helyi hely tartományvezérlők, amelyek kommunikálni tudnak.

-   Hasonló a fenti pontra, a tartományok közötti forgatókönyvek alapulnak, hogy azok a tartományvezérlők, a helyi hálózati határain kívül található egy összekötő gazdagép közvetlen hivatkozásokat. Ebben a forgatókönyvben egyaránt van győződjön meg arról is engedélyezi a forgalom kezdődően a tartományvezérlők, amelyek megfelelnek a megfelelő tartományához, vagy más delegálás sikertelen.

-   Ahol lehetséges, lehetőleg ne tároljon minden aktív IP-CÍMEK/Azonosítók eszközök közötti összekötő gazdagépek és -tartományvezérlő, mivel azok néha keresztül zavaró és zavarják a core keresztüli RPC-adatforgalmat

Mint amennyit szeretnénk problémák gyors megoldása és hatékonyan, időbe telhet, ezért lehetőség szerint érdemes próbálja és a delegálás a legegyszerűbb forgatókönyvek. A több változót bevezetése, annál nagyobb lehet, hogy a tartalom. Például korlátozza a tesztelést egyetlen összekötőt értékes időt takaríthat, és további összekötők a problémák megoldása után lehet hozzáadni.

Néhány környezeti tényezők is eredményezhetnek problémát, újra, ha lehetséges próbálja, és az operációs rendszer minimális tesztelési architektúra minimalizálása érdekében. Például helytelenül konfigurált belső tűzfal hozzáférés-vezérlési listák nem ritka, ezért lehetőség van a tartományvezérlők és a háttéralkalmazás rögtön keresztül engedélyezett összekötő származó összes forgalmat. 

Ténylegesen abszolút legjobb összekötők helyezze helye a célhely lehet közelében. Egy kép tűzfal beágyazott Bár a tesztelés egyszerűen hozzáadja a szükségtelen összetettségét, és csak sikerült meghosszabbítani a vizsgálatok.

Igen milyen számít Kerberos által korlátozott Delegálás probléma ennek ellenére? Nincs több klasszikus jelzésének hiányában a Kerberos által korlátozott SSO és az első a problémát általában manifest magukat a böngészőben.

   ![Helytelen a Kerberos által korlátozott konfigurációs hiba](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

   ![Hiányzó jogosultságok miatt nem sikerült engedélyezése](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

minden amelyek viselik azonos tünete, hajtsa végre az egyszeri bejelentkezés sikertelen, és ezért elutasítja az a felhasználó hozzáférni az alkalmazáshoz.

## <a name="troubleshooting"></a>Hibaelhárítás

Hogyan majd hibaelhárítása a problémát és a megfigyelt jelenségek függenek. Előtt fog minden további akkor javasoljuk, hogy az alábbi hivatkozások nem még rendelkezik olyan hasznos információkat tartalmaznak, mint:

-   [Alkalmazásproxy problémák és hibaüzenetek hibaelhárítása](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot)

-   [Kerberos-hibákhoz, és a jelenség](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#kerberos-errors)

-   [Egyszeri bejelentkezési modellel működik ha helyszíni és felhőalapú Identitások nem azonosak.](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd#working-with-sso-when-on-premises-and-cloud-identities-are-not-identical)

Ha van a távoli, majd a fő probléma mindenképpen létezik-e. Meg kell mélyebben elmerülne a rendszer, így indítsa el a folyamat három különböző szakaszokat elháríthatja az elválasztó.

**Ügyfelek előhitelesítése** – a külső felhasználó hitelesítéséhez az Azure-bA böngésző használatával.

Előtti hitelesítéshez az Azure-bA alatt elengedhetetlen a Kerberos által korlátozott Delegálás SSO függvénynek. Ez kell lehet tesztelni és címzett először, ha probléma merül fel. Vegye figyelembe, hogy az előhitelesítési szakasza nincs kapcsolat a Kerberos által korlátozott vagy a közzétett alkalmazáshoz. Meg kell viszonylag egyszerűen kijavíthatja az esetleges eltéréseket által megerősítést ellenőrzése a tulajdonos fiók létezik-e az Azure-ban, és, hogy nincs letiltva vagy letiltva. A böngészőben a hibaválaszba általában leíró elég okának megértéséhez. Győződjön meg arról, ha nem biztos, hogy más kapcsolatos problémák elhárítása leírásokat is ellenőrizheti.

**Delegálás szolgáltatás** – az Azure Proxy connector megszerezni a kerberos szolgáltatásjegy a KDC (Kerberos kulcskiosztó központ), a felhasználók nevében.

A külső kommunikáció az ügyfél és az Azure előtér között kell nincs hatással a Kerberos által korlátozott Delegálás megszüntethesse Önnek, másik győződjön meg arról, hogy működik. Ez a helyzet az Azure Proxy szolgáltatás megadható érvényes felhasználói azonosítóval, amely használható a kerberos jegy beszerzéséhez. Nélkül a Kerberos által korlátozott Delegálás nincs lehetőség, és sikertelen lesz.

Ahogy korábban említettük, a böngésző hibaüzenetek általában megadja a néhány jó a dolgok miért nem működnek. Győződjön meg arról, jegyezze fel a tevékenység azonosítója és a válaszban szereplő Timestamp típusú, ez lehetővé teszi, hogy az Azure Proxy naplóbeli eseményei a tényleges el összefüggéseket.

   ![Helytelen a Kerberos által korlátozott konfigurációs hiba](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

És a megfelelő bejegyzések láthatók az eseménynaplóban lesz látható a 13019 vagy 12027 eseményként is rögzíti. Az összekötő eseménynaplók található **alkalmazási és Szolgáltatásnaplójában** &gt; **Microsoft** &gt; **AadApplicationProxy** &gt; **összekötő**&gt;**Admin**.

   ![Alkalmazásproxy eseménynaplójából 13019 esemény](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

   ![Alkalmazásproxy eseménynaplójából 12027 esemény](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

-   Az A rekord a belső DNS-ben az alkalmazás címének és nem egy olyan CNAME rekordot használhat

-   Újra győződjön meg arról, hogy az összekötő állomás rendelkezik a megfelelő engedélyekkel a feladat a kijelölt tároló fiókhoz tartozó egyszerű Szolgáltatásnevet, és hogy **bármely hitelesítési protokoll** van kiválasztva. Ez a fő tárgyalja [SSO konfigurációs cikk](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd)

-   Győződjön meg arról, hogy nincs-e az egyszerű Szolgáltatásnevet az Active Directory fennálló csak egyetlen példányát kiállításával egy **setspn - x** bármely tartományi tag gazdagépen cmd parancssorból

-   Ellenőrizze, hogy ha tartományi házirend van érvényben korlátozni a [kerberos kiállított jogkivonatokat mérete legfeljebb](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/), mert ez akadályozni, hogy az összekötő egy token if található túl nagy

A hálózati nyomkövetés rögzítése a connector állomás- és a KDC között cseréjét lesz a következő ajánlott lépés több alacsony szintű részletes a problémák az beszerzése. Ezt a található [mélyreható kapcsolatos problémák elhárítása papír](https://aka.ms/proxytshootpaper).

Ha jegykezelési megfelelőnek tűnik, meg kell jelennie a naplófájlokban, amely meghatározza, hogy, hogy a hitelesítés a 401-es visszaadó miatt nem sikerült egy eseményt. Ez általában azt jelzi, hogy a célalkalmazás elutasítása a jegyet, ezért folytatásához a következő következő szakaszára.

**A célalkalmazás** -a fogyasztó az összekötő által biztosított kerberos jegy

Ebben a szakaszban az összekötő várhatóan küldött egy kerberos szolgáltatásjegyet a háttér fejlécként belül az első alkalmazás-kérelmekre.

-   Az alkalmazás belső URL-cím segítségével meghatározott a portálon, ellenőrizze, hogy az alkalmazás közvetlenül a böngészőből az összekötő állomáson elérhető. Ezután bejelentkezhet sikeresen megtörtént. Részleteket ezzel az összekötő hibaelhárítása oldalon található.

-   Az összekötő állomáson továbbra is győződjön meg arról, hogy a böngésző és az alkalmazás közötti hitelesítés kerberos, használja a következő módszerek valamelyikével:

1.  A fejlesztői eszközök futtatása (**F12**) a Internet Explorer vagy [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) a connector gazdagépről. Nyissa meg a belső URL-cím használata alkalmazás, és vizsgálja meg a felajánlott WWW engedélyezési fejléceket a visszatérő válaszban az alkalmazásból, annak érdekében, hogy vagy negotiate vagy kerberos jelen. Az eredmény abban az ezt követő kerberos blob az alkalmazás a böngészőből érkező válasz általában megkezdi a **YII**, ezért ez a kerberos Play alatt jól jelzi. NTLM viszont mindig kezdődnie **TlRMTVNTUAAB**, amely szól, amikor a Base64 kódolású anyag dekódolni NTLMSSP. Ha látja **TlRMTVNTUAAB** a blob elején, ez azt jelenti, hogy van-e a Kerberos **nem** érhető el. Ha ez nem látható, a Kerberos valószínű érhető el.

  * Vegye figyelembe, ha a Fiddler használatával az ezt a módszert igényelnének, ideiglenesen letilthatja az alkalmazás konfigurációs az IIS-ben a bővített védelmet.

     ![Hálózati hálózatfelügyeleti böngészőablakban](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

    *Ábra:* ez nem TIRMTVNTUAAB kezdődik, mert ez az, hogy rendelkezésre áll-e a Kerberos példa. Ez az YII nem kezdődő Kerberos Blob egy példát.

2.  Ideiglenesen távolítsa el az NTLM IIS helyen, és hozzáférés app IE összekötő gazdagép közvetlenül a szolgáltatók listájából. Már nem a szolgáltatók listája az NTLM, a tudni hozzáférni az alkalmazáshoz, csak Kerberos használatával kell lennie. Ha ez nem sikerül, majd, amelyek arra utalnak, hogy van-e az alkalmazás konfigurációs probléma, és a Kerberos-hitelesítés nem működik.

Ha Kerberos nem érhető el, ellenőrizze az alkalmazás hitelesítési beállításait is győződjön meg arról, hogy az IIS egyezteti NTLM alatta csak a legfelső szerepel. (Nem Negotiate: kerberos vagy Negotiate: PKU2U). Csak továbbra is, ha Kerberos működőképességét.

   ![Windows-hitelesítésszolgáltatók](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)
   
-   A Kerberos és NTLM helyen lehetővé teszi, hogy most ideiglenesen letilthatja az előhitelesítési az alkalmazás a portálon. Tekintse meg, ha hozzá tud férni az internetről, a külső URL-cím használatával. Hitelesítésére a rendszer kéri, és ugyanazt a fiókot az előző lépésben használt ehhez képesnek kell lennie. Ha nem, ez az a probléma a háttéralkalmazás és nem a Kerberos által korlátozott Delegálás minden jelzi.

-   Most engedélyezze újra a portálon előhitelesítést, és a hitelesítéshez Azure szolgáltatáson keresztül próbálnak meg külső URL-CÍMEN keresztül az alkalmazás csatlakozni. Ha az egyszeri bejelentkezés sikertelen volt, majd kell megjelennie a böngészőt, valamint az esemény 13022 a tiltott hibaüzenet:

    *Microsoft AAD alkalmazásproxy-összekötő nem tudja hitelesíteni a felhasználót, mert a háttérkiszolgáló válaszol-e egy HTTP 401 hiba: a Kerberos hitelesítési kísérleteket.*

    ![Tiltott hibaüzenetet HTTTP 401](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

-   Ellenőrizze, hogy az IIS-alkalmazás konfigurált alkalmazáskészlet biztosításához ugyanazt a fiókot az egyszerű szolgáltatásnév konfigurált szemben az ad-ben, lépjen az alább bemutatott IIS használatára van konfigurálva

    ![IIS alkalmazás konfigurációs ablak](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

    Miután eldöntötte, hogy az identitás, probléma az alábbi cmd jelenít meg, győződjön meg arról, hogy ennek a fióknak a mindenképpen van konfigurálva az egyszerű Szolgáltatásnevet. Például **setspn – q http/spn.wacketywack.com**

    ![SetSPN parancssori ablakban](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

-   Ellenőrizze, hogy az alkalmazás beállításait a portálon képest van meghatározva. az egyszerű Szolgáltatásnevet az azonos egyszerű Szolgáltatásnevet, az alkalmazáskészlet alkalmazást használja a cél AD fiók van konfigurálva

   ![SPN-konfiguráció Azure-portálon](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)
   
-   Nyissa meg az IIS, és válassza a **Konfigurációszerkesztő** az alkalmazás lehetőséget, és navigáljon a **system.webServer/security/authentication/windowsAuthentication** és győződjön meg arról, hogy **UseAppPoolCredentials** értéke igaz értékre

   ![Az IIS konfigurációs app készletek hitelesítőadat-beállítás](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

Miközben folyamatban elősegíti a Kerberos-művelet is engedélyezve van, a Kernel mód hagyja a teljesítmény fokozása hatására a jegyet a kért szolgáltatás visszafejteni a számítógépfiók számára. Ezt a helyi rendszer, így ez igaz break Kerberos által korlátozott Delegálás értékre, ha az alkalmazás a farmban lévő több kiszolgálón is nevezik.

-   Egy további ellenőrzést, mint is érdemes lehet letiltani a **kiterjesztett** védelmi túl. Észlelt forgatókönyvek, ahol ez bizonyult hibájához Kerberos által korlátozott Delegálás engedélyezésekor a meghatározott konfigurációk, ahol közzé van téve egy alkalmazást, az alapértelmezett webhely almappa törölték. Ez maga a névtelen hitelesítés csak, így a teljes szürkén jelenik meg, amelyek arra utalnak, gyermekobjektum akkor nem kell öröklődés minden aktív beállítások párbeszédpanelek van konfigurálva. De ahol lehetséges volna mindig javasoljuk, ezzel engedélyezve van, ezért feltétlenül tesztelése, de ne feledkezzen visszaállítása a engedélyezve van.

További ellenőrzést kell kell helyezni, nyomon követése a közzétett alkalmazás használatának megkezdéséhez. Lépjen tovább, és további összekötők delegálása is állítottak be léptetési, de ha olyan további majd akkor javasoljuk, hogy a a részletesebb műszaki útmutató olvasási [hibaelhárítása az Azure AD-alkalmazásproxy a teljes útmutató](https://aka.ms/proxytshootpaper)

Ha továbbra sem tudja előrehaladás a problémát, támogatási több mint örömmel segíti, és itt továbbra is lehet. Hozzon létre egy támogatási jegy közvetlenül a portálon belül, és a mérnökök lesznek elérhetők a Önnek.

## <a name="other-scenarios"></a>Egyéb forgatókönyvek

-   Azure-alkalmazásproxy Kerberos-jegy kérelmek egy alkalmazás a kérelem elküldése előtt. 3. fél alkalmazásmódjai például Tableau Server nincs megelégedve a módszert, és inkább a kerül sor, több hagyományos egyeztetések vár. Az első kérésre, lehetővé téve az alkalmazást szeretne válaszolni, hogy a 401-es keresztül támogatja a hitelesítési típus névtelen.

-   Kettős Ugrás hitelesítés – általában esetekben használatos, ahol a kérelmet rétegzett, egy háttér és első vége, megkövetelését hitelesítést, például az SQL Reporting Services.

## <a name="next-steps"></a>Következő lépések
[Kerberos által korlátozott delegálás (KCD) konfigurálása a felügyelt tartományhoz](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-enable-kcd)
