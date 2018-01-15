---
title: "Hibaelhárítás a Kerberos által korlátozott delegálás konfigurációi alkalmazásproxy |} Microsoft Docs"
description: "Hibaelhárítás a Kerberos által korlátozott delegálás konfigurációi alkalmazásproxy."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 7b31f53e14e3f9a175e5dda95a18eb89dbca99dc
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2018
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Hibaelhárítás a Kerberos által korlátozott delegálás alkalmazásproxy konfigurációi

Az SSO megvalósításához a közzétett alkalmazásokhoz elérhető módszerek némileg eltérőek lehetnek, egyik alkalmazásból a másikba. A lehetőségek közül, hogy Azure Application Proxy kínál alapértelmezés szerint a Kerberos által korlátozott delegálás (KCD). Konfigurálhatja az összekötő háttér alkalmazásokhoz, a felhasználók nevében korlátozott Kerberos-hitelesítést végezni.

A Kerberos által korlátozott Delegálás engedélyezéséhez a tényleges eljárás viszonylag egyszerű, és általában a nem több, mint a különböző összetevőkkel és hitelesítési folyamat, amely elősegíti a SSO általános ismeretének igényelnek. Jó információforrásokat forgatókönyvek hibaelhárítás elősegítése érdekében ahol Kerberos által korlátozott Delegálás SSO nem működik megfelelően, keresése ritka lehet.

Ez a cikk, megpróbálja, hibaelhárításához és önálló javíthatja a leggyakoribb problémák némelyike segítségül a hivatkozás egyetlen pontot biztosítani. Egy időben az összetettebb és végrehajtási törlesztett diagnosztizálására további útmutatást kínál.

Ez a cikk lehetővé teszi a következő előfeltételek:

-   Egy Azure Application Proxy telepítésének [dokumentáció](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) és nem a Kerberos által korlátozott Delegálás alkalmazások általános hozzáférést a várt módon működik.

-   A közzétett célalkalmazás Kerberos IIS és a Microsoft végrehajtásának alapul.

-   A kiszolgáló és az alkalmazás gazdagépek egyetlen Active Directory-tartományban találhatók. Részletes tájékoztatást közötti tartományok és erdők forgatókönyvek megtalálhatók a [Kerberos által korlátozott Delegálás tanulmány](http://aka.ms/KCDPaper).

-   A tulajdonos alkalmazás közzé van téve egy Azure-ban bérlői előtti hitelesítés engedélyezve van, és a felhasználók-azonosítóknak hitelesítésre űrlapalapú hitelesítésen keresztül. Gazdag ügyfél-hitelesítési forgatókönyvei nem vonatkozik ez a cikk, de egy bizonyos ponton a jövőben hozzáadni.

## <a name="prerequisites"></a>Előfeltételek

Azure alkalmazásproxy telepíthető a számos különböző típusú infrastruktúrák, vagy a architektúrák és környezetben minden bizonnyal eltérő a szervezetben. A Kerberos által korlátozott Delegálás kapcsolatos problémák a leggyakoribb okok egyike nem a környezetekben, de meglehetősen egyszerű hibás konfigurációt vagy általános felügyeletet.

Emiatt érdemes mindig indítása a annak biztosítása, hogy jelenjen meg minden előfeltétel teljesül [Kerberos által korlátozott Delegálás egyszeri bejelentkezés használatával az alkalmazásproxy-cikk](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) elindítása előtt a hibaelhárítás.

Különösen a szakasz a Kerberos által korlátozott Delegálás konfigurálása 2012R2, mivel ez előző verziók Windows de ugyanakkor nem számos szempontot szem előtt tartva a Kerberos által korlátozott Delegálás konfigurálása alapvetően eltérő megközelítést alkalmazza:

-   A rendszer nem ritka, hogy az egy tartományi tagkiszolgáló, egy adott tartományvezérlő biztonságos csatorna párbeszédpanel megnyitásához. Helyezze át egy másik párbeszédpanelt egy adott időpontban, összekötő gazdagépeken nem kell korlátozni csak adott helyi hely tartományvezérlők, amelyek kommunikálni tudnak.

-   Hasonló a fenti pontra a tartományok közötti forgatókönyvek alapulnak, hogy azok a tartományvezérlők, a helyi hálózati határain kívül található egy összekötő gazdagép közvetlen hivatkozásokat. Ebben a forgatókönyvben fontos egyaránt győződjön meg arról is engedélyezi a forgalom kezdődően a tartományvezérlők, amelyek megfelelnek a megfelelő tartományához, vagy más delegálás sikertelen.

-   Ahol lehetséges, lehetőleg ne tároljon minden aktív IP-CÍMEK/Azonosítók eszközök közötti összekötő gazdagépek és -tartományvezérlő, mivel azok néha keresztül zavaró és zavarják a core keresztüli RPC-adatforgalmat

Javasoljuk, hogy delegálás tesztelje a forgatókönyvet a legegyszerűbb. A több változót bevezetése, annál nagyobb lehet, hogy a tartalom. Például korlátozza a tesztelést egyetlen összekötőt értékes időt takaríthat, és további összekötők a problémák megoldása után lehet hozzáadni.

Néhány környezeti tényezők is problémát eredményezhetnek. Tesztelés során minimalizálása érdekében a architektúrát, hogy az operációs rendszer legalább a környezeti tényezők elkerülése érdekében. Például helytelenül konfigurált belső tűzfal hozzáférés-vezérlési listák nem ritka, ezért lehetőség van a tartományvezérlők és a háttéralkalmazás rögtön keresztül engedélyezett összekötő származó összes forgalmat. 

Ténylegesen abszolút legjobb összekötők helyezze helye a célhely lehet közelében. Egy kép tűzfal beágyazott Bár a tesztelés egyszerűen hozzáadja a szükségtelen összetettségét, és csak sikerült meghosszabbítani a vizsgálatok.

Igen milyen számít Kerberos által korlátozott Delegálás probléma ennek ellenére? Klasszikus jelei Kerberos által korlátozott Delegálás SSO sikertelenek lesznek, és az első jelentkezik a probléma általában manifest magukat a böngészőben.

   ![Helytelen a Kerberos által korlátozott konfigurációs hiba](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

   ![Hiányzó jogosultságok miatt nem sikerült engedélyezése](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

minden amelyek viselik azonos tünete, hajtsa végre az egyszeri bejelentkezés sikertelen, és ezért elutasítja az a felhasználó hozzáférni az alkalmazáshoz.

## <a name="troubleshooting"></a>Hibaelhárítás

Hogyan majd hibaelhárítása a problémát és a megfigyelt jelenségek függenek. Mielőtt folytatja a következővel minden további, így megismerkedhet a nem még rendelkezik olyan hasznos információkat tartalmaznak az alábbi hivatkozások:

-   [Alkalmazásproxy problémák és hibaüzenetek hibaelhárítása](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot)

-   [Kerberos-hibákhoz, és a jelenség](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#kerberos-errors)

-   [Egyszeri bejelentkezési modellel működik ha helyszíni és felhőalapú Identitások nem azonosak.](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd#working-with-sso-when-on-premises-and-cloud-identities-are-not-identical)

Ha van a távoli, majd a fő probléma mindenképpen létezik-e. Indítsa el a folyamat három különböző szakaszokat elháríthatja azokat elválasztva.

**Ügyfelek előhitelesítése** – a külső felhasználó hitelesítéséhez az Azure-bA böngésző használatával.

Előtti hitelesítéshez az Azure-bA alatt elengedhetetlen a Kerberos által korlátozott Delegálás SSO függvénynek. Ez kell lehet tesztelni és címzett először, ha probléma merül fel. Az előhitelesítési szakasza nem rendelkezik kapcsolattal a Kerberos által korlátozott vagy a közzétett alkalmazáshoz. Meg kell viszonylag egyszerűen kijavíthatja az esetleges eltéréseket által megerősítést ellenőrzése a tulajdonos fiók létezik-e az Azure-ban, és, hogy nincs letiltva vagy letiltva. A böngészőben a hibaválaszba általában leíró elég okának megértéséhez. Győződjön meg arról, ha nem biztos, hogy más kapcsolatos problémák elhárítása leírásokat is ellenőrizheti.

**Delegálás szolgáltatás** – az Azure Proxy-összekötő Kerberos-szolgáltatásjegyet beszerzése a KDC (Kerberos kulcskiosztó központ), a felhasználók nevében.

A külső kommunikáció az ügyfél és az Azure előtér között kell nincs hatással a Kerberos által korlátozott Delegálás megszüntethesse Önnek, másik győződjön meg arról, hogy működik. Ez a helyzet az Azure Proxy szolgáltatás biztosítható, hogy a Kerberos jegy beszerzéséhez használt érvényes felhasználói azonosítóval. E nélkül a Kerberos által korlátozott Delegálás nincs lehetőség, és sikertelen lesz.

Ahogy korábban említettük, a böngésző hibaüzenetek általában megadja a néhány jó a dolgok miért nem működnek. Győződjön meg arról, jegyezze fel a tevékenység azonosítója és a válaszban szereplő Timestamp típusú, ez lehetővé teszi, hogy az Azure Proxy naplóbeli eseményei a tényleges összefüggéseket.

   ![Helytelen a Kerberos által korlátozott konfigurációs hiba](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

És a megfelelő bejegyzések láthatók az eseménynaplóban lesz látható a 13019 vagy 12027 eseményként is rögzíti. Az összekötő eseménynaplók található **alkalmazási és Szolgáltatásnaplójában** &gt; **Microsoft** &gt; **AadApplicationProxy** &gt; **összekötő**&gt;**Admin**.

   ![Alkalmazásproxy eseménynaplójából 13019 esemény](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

   ![Alkalmazásproxy eseménynaplójából 12027 esemény](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

-   Az A rekord a belső DNS-ben az alkalmazás címének és nem egy olyan CNAME rekordot használhat

-   Megerősítése, hogy az összekötő állomás rendelkezik a megfelelő engedélyekkel a feladat a kijelölt tároló fiókhoz tartozó egyszerű Szolgáltatásnevet, és hogy **bármely hitelesítési protokoll** van kiválasztva. A témakörrel kapcsolatos további információkért lásd: [SSO konfigurációs cikk](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd)

-   Győződjön meg arról, hogy nincs-e az egyszerű Szolgáltatásnevet az Active Directory fennálló csak egyetlen példányát kiállításával egy `setspn -x` bármely tartományi tag gazdagépen cmd parancssorból

-   Ellenőrizze, hogy tartományi házirend érvényesítve van-e korlátozni a [Kerberos kiállított jogkivonatokat mérete legfeljebb](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/), mert ez megakadályozza, hogy az összekötő egy token if megszerezni található túl nagy

A hálózati nyomkövetés rögzítése a connector állomás- és a KDC között cseréjét az beszerzése a problémák alacsony szintű részletesebben a következő ajánlott lépés lesz. További részleteket, [mélyreható kapcsolatos problémák elhárítása papír](https://aka.ms/proxytshootpaper).

Ha jegykezelési megfelelőnek tűnik, meg kell jelennie a naplófájlokban, amely meghatározza, hogy, hogy a hitelesítés a 401-es visszaadó miatt nem sikerült egy eseményt. Ez általában azt jelzi, hogy a célalkalmazás elutasítása a jegyet, ezért folytatásához a következő következő szakaszára.

**A célalkalmazás** -a fogyasztó az összekötő által biztosított Kerberos jegy

Ezen a ponton várható az összekötő elküldött egy Kerberos szolgáltatás a háttér jegy fejlécként belül az első alkalmazás-kérelmekre.

-   Az alkalmazás belső URL-cím segítségével meghatározott a portálon, ellenőrizze, hogy az alkalmazás közvetlenül a böngészőből az összekötő állomáson elérhető. Ezután bejelentkezhet sikeresen megtörtént. Részleteket ezzel az összekötő hibaelhárítása oldalon található.

-   Az összekötő állomáson továbbra is győződjön meg arról, hogy a böngésző és az alkalmazás közötti hitelesítés Kerberos, használja a következő módszerek valamelyikével:

1.  A fejlesztői eszközök futtatása (**F12**) a Internet Explorer vagy [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) a connector gazdagépről. Nyissa meg a belső URL-cím használata alkalmazás, és vizsgálja meg a felajánlott WWW engedélyezési fejléceket a visszatérő válaszban az alkalmazásból, annak érdekében, hogy vagy negotiate vagy Kerberos jelen. Az eredmény abban az ezt követő Kerberos blob az alkalmazás a böngészőből érkező válasz általában megkezdi a **YII**, ezért ez a Kerberos Play alatt jól jelzi. NTLM viszont mindig kezdődik **TlRMTVNTUAAB**, amely szól, amikor a Base64 kódolású anyag dekódolni NTLMSSP. Ha látja **TlRMTVNTUAAB** a blob elején, ez azt jelenti, hogy van-e a Kerberos **nem** érhető el. Ha ez nem látható, a Kerberos valószínű érhető el.
    > [!NOTE]
    > Fiddler használata esetén ez a módszer igényelnének ideiglenesen letilthatja az alkalmazás konfigurációs az IIS-ben a bővített védelmet.

     ![Hálózati hálózatfelügyeleti böngészőablakban](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

    *Ábra:* ez nem TIRMTVNTUAAB kezdődik, mert ez az, hogy rendelkezésre áll-e a Kerberos példa. Ez az YII nem kezdődő Kerberos Blob egy példát.

2.  Ideiglenesen távolítsa el az NTLM IIS helyen, és hozzáférés app IE összekötő gazdagép közvetlenül a szolgáltatók listájából. Már nem a szolgáltatók listája az NTLM, a tudni hozzáférni az alkalmazáshoz, csak Kerberos használatával kell lennie. Ha ez nem sikerül, majd, amelyek arra utalnak, hogy van-e az alkalmazás konfigurációs probléma, és a Kerberos-hitelesítés nem működik.

Ha Kerberos nem érhető el, ellenőrizze az alkalmazás hitelesítési beállításait is győződjön meg arról, hogy az IIS egyezteti NTLM alatta csak a legfelső szerepel. (Nem egyezteti: a kerberos vagy Negotiate: PKU2U). Csak továbbra is, ha Kerberos működőképességét.

   ![Windows-hitelesítésszolgáltatók](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)
   
-   A Kerberos és NTLM helyen lehetővé teszi, hogy most ideiglenesen letilthatja az előhitelesítési az alkalmazás a portálon. Tekintse meg, ha hozzá tud férni az internetről, a külső URL-cím használatával. Hitelesítésére a rendszer kéri, és ugyanazt a fiókot az előző lépésben használt ehhez képesnek kell lennie. Ha nem, ez az a probléma a háttéralkalmazás és nem a Kerberos által korlátozott Delegálás minden jelzi.

-   Most engedélyezze újra a portálon előhitelesítést, és a hitelesítéshez Azure szolgáltatáson keresztül megkísérel csatlakozni a külső URL-CÍMEN keresztül az alkalmazást. Ha az egyszeri bejelentkezés sikertelen volt, majd kell megjelennie a böngészőt, valamint az esemény 13022 a tiltott hibaüzenet:

    *Microsoft AAD alkalmazásproxy-összekötő nem tudja hitelesíteni a felhasználót, mert a háttérkiszolgáló válaszol-e egy HTTP 401 hiba: a Kerberos hitelesítési kísérleteket.*

    ![Tiltott hibaüzenetet HTTTP 401](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

-   Ellenőrizze annak érdekében, hogy a konfigurált alkalmazáskészlet ugyanazt a fiókot az egyszerű szolgáltatásnév konfigurált szemben az ad-ben, lépjen az IIS-ben hasonlóan az alábbi ábrán látható használatára van konfigurálva az IIS-alkalmazás

    ![IIS alkalmazás konfigurációs ablak](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

    Miután eldöntötte, hogy az identitás, probléma az alábbi cmd jelenít meg, győződjön meg arról, hogy ennek a fióknak a mindenképpen van konfigurálva az egyszerű Szolgáltatásnevet. Például`setspn –q http/spn.wacketywack.com`

    ![SetSPN parancssori ablakban](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

-   Ellenőrizze, hogy az alkalmazás beállításait a portálon képest van meghatározva. az egyszerű Szolgáltatásnevet az azonos egyszerű Szolgáltatásnevet a cél AD-fiók alkalmazás alkalmazáskészlet által használt konfigurálva

   ![SPN-konfiguráció Azure-portálon](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)
   
-   Nyissa meg az IIS, és válassza a **Konfigurációszerkesztő** az alkalmazás lehetőséget, és navigáljon a **system.webServer/security/authentication/windowsAuthentication** való győződjön meg arról, hogy az érték **UseAppPoolCredentials** van **igaz**

   ![Az IIS konfigurációs app készletek hitelesítőadat-beállítás](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

Miközben folyamatban elősegíti a Kerberos-művelet is engedélyezve van, a Kernel mód hagyja a teljesítmény fokozása hatására a jegyet a kért szolgáltatás visszafejteni a számítógépfiók számára. Ezt a helyi rendszer, így ez igaz break Kerberos által korlátozott Delegálás értékre, ha az alkalmazás a farmban lévő több kiszolgálón is nevezik.

-   Egy további ellenőrzést, mint is érdemes lehet letiltani a **kiterjesztett** védelmi túl. Észlelt forgatókönyvek, ahol ez bizonyult törés Kerberos által korlátozott Delegálás engedélyezésekor a specifikus konfigurációk, ahol közzé van téve egy alkalmazást, az alapértelmezett webhely almappa törölték. Ez maga a névtelen hitelesítés csak, így a teljes szürkén jelenik meg, amelyek arra utalnak, gyermekobjektum akkor nem kell öröklődés minden aktív beállítások párbeszédpanelek van konfigurálva. De ahol lehetséges volna mindig javasoljuk, ezzel engedélyezve van, ezért feltétlenül tesztelése, de ne feledkezzen visszaállítása a engedélyezve van.

További ellenőrzést kell kell helyezni, nyomon követése a közzétett alkalmazás használatának megkezdéséhez. Lépjen tovább, és további összekötők delegálása is állítottak be léptetési, de ha olyan további majd akkor javasoljuk, hogy a a részletesebb műszaki útmutató olvasási [hibaelhárítása az Azure AD-alkalmazásproxy a teljes útmutató](https://aka.ms/proxytshootpaper)

Ha továbbra sem tudja előrehaladás a problémát, támogatási több mint örömmel segíti, és itt továbbra is lehet. Hozzon létre egy támogatási jegy közvetlenül a portálon (egy mérnököt lesznek elérhetők a akkor) belül.

## <a name="other-scenarios"></a>Egyéb forgatókönyvek

-   Azure-alkalmazásproxy Kerberos-jegy kérelmek egy alkalmazás a kérelem elküldése előtt. Bizonyos külső alkalmazások, például a Tableau kiszolgáló nincs megelégedve a módszert, és inkább a sor több hagyományos egyeztetések vár. Az első kérésre, lehetővé téve az alkalmazást szeretne válaszolni, hogy a 401-es keresztül támogatja a hitelesítési típus névtelen.

-   Kettős Ugrás hitelesítés – általában esetekben használatos, ahol a kérelmet rétegzett, egy háttér és első vége, megkövetelését hitelesítést, például az SQL Reporting Services.

## <a name="next-steps"></a>További lépések
[Kerberos által korlátozott delegálás (KCD) konfigurálása a felügyelt tartományhoz](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-enable-kcd)
