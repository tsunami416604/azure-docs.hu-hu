---
title: Kerberos korlátozott delegálás – Alkalmazásproxy – Problémamegoldás –
description: Az alkalmazásproxy Kerberos-korlátozott delegálási konfigurációinak – problémamegoldás
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5e866f61409960447e17ecb50b035eabd53dc38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275681"
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Az alkalmazásproxy Kerberos-korlátozott delegálási konfigurációinak hibaelhárítása

Az egyszeri eladósodás közzétett alkalmazásokhoz való elérésére rendelkezésre álló módszerek alkalmazásonként eltérőek lehetnek. Az egyik lehetőség, amely az Azure Active Directory (Azure AD) alkalmazásproxy alapértelmezés szerint kerberos korlátozott delegálás (KCD) kínál. A felhasználók számára konfigurálhatja az összekötőt, hogy a korlátozott Kerberos-hitelesítést háttéralkalmazásokra futtassa.

A KCD engedélyezésének eljárása egyszerű. Nem igényel többet, mint az SSO-t támogató különböző összetevők és hitelesítési folyamat általános megértését. De néha a KCD SSO nem a várt módon működik. A forgatókönyvek elhárításához jó információforrásokra van szükség.

Ez a cikk egyetlen hivatkozási pontot tartalmaz, amely segít a leggyakoribb problémák elhárításában és öngyógyításában. Foglalkozik továbbá az összetettebb végrehajtási problémák diagnózisával is.

Ez a cikk a következő feltételezéseket teszi:

- Az Azure AD-alkalmazásproxy üzembe helyezése az [alkalmazásproxy használatával való ismerkedés és](application-proxy-add-on-premises-application.md) a nem KCD-alkalmazások hoz való általános hozzáférés a várt módon működik.
- A közzétett célalkalmazás az Internet Information Services (IIS) szolgáltatáson és a Kerberos Microsoft általi megvalósításán alapul.
- A kiszolgáló- és alkalmazásállomások egyetlen Azure Active Directory-tartományban találhatók. A tartományok közötti és az erdők közötti forgatókönyvekről a [KCD tanulmányában](https://aka.ms/KCDPaper)talál részletes információkat.
- A tulajdonosalkalmazás egy Azure-bérlőben van közzétéve, ahol engedélyezve van az előhitelesítés. A felhasználóknak az űrlapalapú hitelesítésen keresztül kell hitelesíteniük magukat az Azure-ban. A cikk nem terjed ki a gazdag ügyfél-hitelesítési forgatókönyvekre. Lehet, hogy hozzá valamikor a jövőben.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD alkalmazásproxy számos típusú infrastruktúrába vagy környezetbe telepíthető. Az architektúrák szervezetenként változnak. A KCD-vel kapcsolatos problémák leggyakoribb okai nem a környezet. A legtöbb problémát egyszerű helytelen konfigurációk vagy általános hibák okozzák.

Ezért a hibaelhárítás megkezdése előtt győződjön meg arról, hogy a [KCD-sSO használata az alkalmazásproxyval](application-proxy-configure-single-sign-on-with-kcd.md) című témakör összes előfeltételének megfelelt. Vegye figyelembe a Kerberos korlátozott delegálásának konfigurálása című szakaszt a 2012R2-en. Ez a folyamat más megközelítést alkalmaz a KCD konfigurálására a Windows korábbi verzióiban. Is, szem előtt tartva ezeket a szempontokat:

- Nem ritka, hogy a tartományi tagkiszolgáló biztonságos csatornapárbeszédet nyit meg egy adott tartományvezérlővel.It't not uncommon for a domain member server to open a secure channel dialog with a specific domain controller (DC). Ezután előfordulhat, hogy a kiszolgáló egy adott időpontban egy másik párbeszédablakba költözik. Így összekötő gazdagépek nem korlátozódik a kommunikáció csak adott helyi tartományvezérlők.
- A tartományok közötti forgatókönyvek olyan átirányításokon alapulnak, amelyek egy összekötő állomást a helyi hálózaton kívül lévő tartományvezérlőkre irányítanak. Ezekben az esetekben ugyanilyen fontos, hogy a forgalmat tovább küldjék a tartományvezérlőknek, amelyek más megfelelő tartományokat képviselnek. Ha nem, a delegálás sikertelen.
- Ahol lehetséges, ne helyezzen aktív IPS- vagy IDS-eszközöket az összekötő állomások és a tartományvezérlők között. Ezek az eszközök néha túl tolakodóak, és zavarják az alapvető RPC-forgalmat.

Tesztdelegálás egyszerű forgatókönyvekben. Minél több változót vezet sző, annál többet kell megküzdenie. Az idő megtakarítása érdekében korlátozza a tesztelést egyetlen összekötőre. A probléma megoldása után adjon hozzá további összekötőket.

Egyes környezeti tényezők is hozzájárulhatnak egy kérdéshez. Ezeknek a tényezőknek a elkerülése érdekében a tesztelés során a lehető legnagyobb mértékben minimalizálja az architektúrát. A helytelenül konfigurált belső tűzfal ancövek például gyakoriak. Ha lehetséges, küldje el az összes forgalmat egy összekötőegyenesen keresztül a tartományvezérlők és a háttér-alkalmazás.

A csatlakozók elhelyezésének legjobb helye a lehető legközelebb van a céljaikhoz. A tesztelés során beépítve lévő tűzfal szükségtelen bonyolultságot ad hozzá, és meghosszabbíthatja a vizsgálatokat.

Mi mutatja a KCD probléma? Számos gyakori jele van annak, hogy a KCD SSO nem működik. A probléma első jelei megjelennek a böngészőben.

![Példa: Helytelen KCD konfigurációs hiba](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

![Példa: Az engedélyezés nem sikerült, mert hiányoznak az engedélyek](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

Mindkét kép ugyanazt a tünetet jeleníti meg: SSO-hiba. Az alkalmazáshoz való felhasználói hozzáférés megtagadva.

## <a name="troubleshooting"></a>Hibaelhárítás

A hibaelhárítás módja a problémától és a megfigyelt tünetektől függ. Mielőtt tovább menne, fedezze fel a következő cikkeket. Hasznos hibaelhárítási információkat nyújtanak:

- [Alkalmazásproxyval kapcsolatos problémák és hibaüzenetek elhárítása](application-proxy-troubleshoot.md)
- [Kerberos hibák és tünetek](application-proxy-troubleshoot.md#kerberos-errors)
- [Az egyszeri szolgáltatás sal való együttműködés, ha a helyszíni és a felhőalapú identitások nem azonosak](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

Ha eljutottál erre a pontra, akkor a fő probléma létezik. A kezdéshez válassza szét a folyamatot a következő három fázisba, amelyek hibaelhárításához használható.

### <a name="client-pre-authentication"></a>Ügyfél előhitelesítése

Az Azure-ban böngészőn keresztül hitelesítő külső felhasználó. A KCD egyszeri szolgáltatás működéséhez az Azure-ba való előzetes hitelesítés e-képesség szükséges. Tesztelje és kezelje ezt a képességet, ha bármilyen probléma merül fel. Az előhitelesítési szakasz nem kapcsolódik a KCD-hez vagy a közzétett alkalmazáshoz. Az eltérések kijavítása egyszerű, ha a józan ság ellenőrzi, hogy a tulajdonosfiók létezik-e az Azure-ban. Ellenőrizze azt is, hogy nincs-e letiltva vagy blokkolva. A böngészőben a hibaválasz elég leíró ahhoz, hogy megmagyarázza az okát. Ha bizonytalan, ellenőrizze a Microsoft egyéb hibaelhárítási cikkeit.

### <a name="delegation-service"></a>Delegálási szolgáltatás

Az Azure Proxy-összekötő, amely kerberos-szolgáltatásjegyet kap a kerberos-kulcsszolgáltató (KCD) felhasználói számára.

Az ügyfél és az Azure előtér közötti külső kommunikáció nincs hatással a KCD-re. Ezek a kommunikációcsak győződjön meg arról, hogy a KCD működik. Az Azure Proxy szolgáltatás egy érvényes felhasználói azonosítót kap, amely kerberos-jegy leválasztásához használatos. Enélkül az azonosító nélkül a KCD nem lehetséges és sikertelen.

Mint már említettük, a böngésző hibaüzenetek nyújt néhány jó nyomokat, hogy miért a dolgok nem. Győződjön meg arról, hogy vegye figyelembe a tevékenységazonosítót és az időbélyeget a válaszban. Ez az információ segít a viselkedés és a tényleges események az Azure Proxy eseménynaplóban.

![Példa: Helytelen KCD konfigurációs hiba](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

Az eseménynaplóban látható megfelelő bejegyzések 13019-es vagy 12027-es eseményekként jelennek meg. Keresse meg az összekötő eseménynaplóit az **Alkalmazások és szolgáltatások naplók** &gt; **Microsoft** &gt; **AadApplicationProxy** &gt; **Connector** &gt; **Admin**.

![13019-es esemény az alkalmazásproxy eseménynaplójából](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

![12027-es esemény az alkalmazásproxy eseménynaplójából](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

1. Az alkalmazás címéhez használjon **A** rekordot a belső DNS-ben, ne **cname-t.**
1. Erősítse meg újra, hogy az összekötő állomás megkapta a jogot, hogy delegálja a kijelölt célfiók SPN.Re confirm that the connector host has granted the right to dedelegate to the designated target account's SPN. Annak megerősítése, hogy **a hitelesítési protokoll használata** be van jelölve. További információt az [SSO konfigurációs cikkében](application-proxy-configure-single-sign-on-with-kcd.md)talál.
1. Ellenőrizze, hogy az Azure AD-ben az SPN-nek csak egy példánya van-e. Kérdés `setspn -x` a parancssorból bármely tartománytag-állomáson.
1. Ellenőrizze, hogy a rendszer olyan tartományi házirendet alkalmaz-e, amely korlátozza a [kiadott Kerberos-tokenek maximális méretét.](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/) Ez a szabályzat megakadályozza, hogy az összekötő egy jogkivonatot kapjon, ha azt túlzottnak találja.

A hálózati nyomkövetés, amely rögzíti az összekötő állomás és a tartomány KDC közötti cserék a következő legjobb lépés, hogy minél több alacsony szintű részleteket a problémákat. További információt a [mélymerülési hibaelhárítási dokumentumban talál.](https://aka.ms/proxytshootpaper)

Ha a jegykiállítás jól néz ki, megjelenik egy esemény a naplókban, amely szerint a hitelesítés sikertelen volt, mert az alkalmazás egy 401-es értéket adott vissza. Ez az esemény azt jelzi, hogy a célalkalmazás elutasította a jegyet. Menj a következő szakaszba.

### <a name="target-application"></a>Célalkalmazás

Az összekötő által biztosított Kerberos-jegy fogyasztója. Ebben a szakaszban várható, hogy az összekötő kerberos-szolgáltatásjegyet küldött a háttérrendszernek. Ez a jegy egy fejléc az első alkalmazáskérelemben.

1. Az alkalmazás portálon megadott belső URL-címének használatával ellenőrizze, hogy az alkalmazás közvetlenül az összekötő gazdagépen lévő böngészőből érhető-e el. Ezután sikeresen bejelentkezhet. A részletek az összekötő **hibaelhárítási** oldalán találhatók.
1. Továbbra is az összekötő gazdagépen, ellenőrizze, hogy a böngésző és az alkalmazás közötti hitelesítés kerberos-t használ.Still on the connector host, confirm that the authentication between the browser and the application uses Kerberos. Válasszon a következő lehetőségek közül:
1. Futtassa a DevTools (**F12**) alkalmazást az Internet Explorer programban, vagy használja a [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) alkalmazást az összekötő állomásról. Lépjen az alkalmazáshoz a belső URL-cím használatával. Ellenőrizze az alkalmazás válaszában visszaadott ajánlott WWW-engedélyezési fejléceket, és győződjön meg arról, hogy az egyeztetés vagy a Kerberos jelen van.

   - A következő Kerberos blob, amely a böngésző től az alkalmazásra adott válaszban kerül vissza, **a YII-vel**kezdődik. Ezek a levelek azt mondják, hogy a Kerberos fut. A Microsoft NT LAN Manager (NTLM) ezzel szemben mindig a **TlRMTVNTUAAB**programmal kezdődik, amely a Base64-ből dekódolva az NTLM biztonsági támogatási szolgáltatót (NTLMSSP) olvassa. Ha a Blob elején a **TlRMTVNTUAAB jelenik** meg, a Kerberos nem érhető el. Ha nem látja a **TlRMTVNTUAAB-ot,** akkor a Kerberos valószínűleg elérhető.

      > [!NOTE]
      > Ha fiddler, ez a módszer megköveteli, hogy ideiglenesen tiltsa le a kiterjesztett védelmet az alkalmazás konfigurációját az IIS.If you use Fiddler, this method requires to you temporarydisable extended protection on the application's configuration in IIS.

      ![Böngészőhálózat ellenőrző ablaka](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

   - A blob ebben az ábrán nem kezdődik **TIRMTVNTUAAB**. Ebben a példában a Kerberos érhető el, és a Kerberos blob nem a **YII-vel**kezdődik.

1. Ideiglenesen távolítsa el az NTLM-et az IIS-webhely szolgáltatói listájáról. Az alkalmazás közvetlenül az Internet Explorerből érhető el az összekötő gazdagépen. Az NTLM már nem szerepel a szolgáltatók listáján. Az alkalmazás csak a Kerberos használatával érhető el. Ha a hozzáférés sikertelen, lehet, hogy probléma van az alkalmazás konfigurációjával. A Kerberos-hitelesítés nem működik.

   - Ha a Kerberos nem érhető el, ellenőrizze az alkalmazás hitelesítési beállításait az IIS-ben. Győződjön meg **róla, hogy** a Negotiate a tetején van felsorolva, alatta pedig az NTLM. Ha a Nincs egyeztetés , Kerberos vagy Egyeztetés ( Egyeztetés ) vagy PKU2U (Nem egyeztetés , Kerberos) vagy PKU2U (Nem egyeztetés, **Kerberos vagy Egyeztetés)** vagy PKU2U (Nem egyeztetés) vagy PKU2U (Nem egyeztetés) ( Egyeztetés ) vagy **PKU2U (Nem** **egyeztetés)**( Nem egyeztetés ) vagy PKU2U

     ![Windows-hitelesítésszolgáltatók](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)

   - A Kerberos és az NTLM helyben ideiglenesen tiltsa le az előhitelesítést az alkalmazáshoz a portálon. Próbálja meg elérni az internetről a külső URL-t. A rendszer kéri a hitelesítést. Ezt az előző lépésben használt fiókkal is megteheti. Ha nem, akkor a háttéralkalmazással van probléma, nem a KCD-vel.
   - Engedélyezze újra az előhitelesítést a portálon. Hitelesítse magát az Azure-on keresztül, ha a külső URL-címén keresztül próbál csatlakozni az alkalmazáshoz. Ha az SSO sikertelen, a böngészőben tiltott hibaüzenet jelenik meg, és az 13022-es esemény a naplóban:

     *A Microsoft AAD alkalmazásproxy-összekötő nem tudja hitelesíteni a felhasználót, mert a háttérkiszolgáló HTTP 401-es hibával válaszol a Kerberos hitelesítési kísérleteire.*

      ![A HTTTP 401 tiltott hibája](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

   - Ellenőrizze az IIS alkalmazást. Győződjön meg arról, hogy a konfigurált alkalmazáskészlet és az SPN úgy vannak konfigurálva, hogy ugyanazt a fiókot használják az Azure AD-ben. Navigálás az IIS-ben az alábbi ábrán látható módon:

      ![Az IIS alkalmazás konfigurációs ablaka](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

      Miután ismeri az identitást, győződjön meg arról, hogy ez a fiók a kérdéses spn-nel van konfigurálva. Például: `setspn –q http/spn.wacketywack.com`. Írja be a következő szöveget a parancssorba:

      ![A SetSPN parancsablak megjelenítése](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

   - Ellenőrizze a definiált spn-t az alkalmazás a portálon megadott beállításokkal szemben. Győződjön meg arról, hogy ugyanazt az SPN-t a cél Azure AD-fiókhoz konfigurált az alkalmazás alkalmazáskészlete használja.

      ![SPN-konfiguráció az Azure Portalon](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)

   - Lépjen be az IIS-be, és válassza az alkalmazás **Konfigurációszerkesztő** beállítását. Keresse meg a **system.webServer/security/authentication/windowsAuthentication webhelyet.** Győződjön meg arról, hogy a **UseAppPoolCredentials** érték **igaz.**

      ![Az IIS konfigurációs alkalmazáskészletei hitelesítő adatainak beállítása](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

      Módosítsa ezt az értéket **Igaz**értékre. Az összes gyorsítótárazott Kerberos-jegy eltávolítása a háttérkiszolgálóról a következő parancs futtatásával:

      ```powershell
      Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
      ```

További információt [a Kerberos ügyféljegy-gyorsítótárának kiürítése az összes munkamenethez](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf)című témakörben talál.

Ha engedélyezi a Kernel módot, az javítja a Kerberos-műveletek teljesítményét. De azt is okozza, hogy a kért szolgáltatás jegyét a számítógépfiók használatával visszafejtik. Ezt a fiókot helyi rendszernek is nevezik. Állítsa ezt az értéket **Igaz** értékre a KCD megszakításához, ha az alkalmazás egy farm több kiszolgálóján található.

- További ellenőrzésként tiltsa le **a kiterjesztett** védelmet is. Bizonyos esetekben **a kiterjesztett** védelem megtörte a KCD-t, amikor engedélyezve volt bizonyos konfigurációkban. Ezekben az esetekben egy alkalmazás az alapértelmezett webhely almappájaként lett közzétéve. Ez az alkalmazás csak névtelen hitelesítésre van konfigurálva. Az összes párbeszéd szürkén jelenik meg, ami azt sugallja, hogy a gyermekobjektumok nem örökölnek aktív beállításokat. Azt javasoljuk, hogy tesztelje, de ne felejtse el visszaállítani ezt az értéket **engedélyezve,** ahol lehetséges.

  Ez a további ellenőrzés jó úton halad a közzétett alkalmazás használata felé. További összekötőket is felpörgethet, amelyek delegálásra is konfigurálva vannak. További információkért olvassa el a részletesebb technikai útmutatót, [az Azure AD alkalmazásproxy hibaelhárítása című témakört.](https://aka.ms/proxytshootpaper)

Ha továbbra sem tud előrelépést elérni, a Microsoft támogatási szolgálata segítséget nyújt. Hozzon létre egy támogatási jegyet közvetlenül a portálon belül. Egy mérnök felveszi önnel a kapcsolatot.

## <a name="other-scenarios"></a>Egyéb forgatókönyvek

- Az Azure-alkalmazásproxy Kerberos-jegyet kér, mielőtt elküldené a kérelmet egy alkalmazásnak. Egyes külső alkalmazások nem szeretik ezt a hitelesítési módszert. Ezek az alkalmazások arra számítanak, hogy a hagyományosabb tárgyalásokra sor kerül. Az első kérelem névtelen, amely lehetővé teszi, hogy az alkalmazás a 401-es en keresztül támogatott hitelesítési típusokkal válaszoljon.
- A többugrásos hitelesítést gyakran használják olyan esetekben, amikor egy alkalmazás rétegzett, háttér- és előtér-alapú, ahol mindkettő hitelesítést igényel, például az SQL Server Reporting Services. A többugrásos forgatókönyv konfigurálásához olvassa el a [Kerberos korlátozott delegálásra vonatkozó támogatási cikket, amely protokollváltást igényelhet többugrásos forgatókönyvekben.](https://support.microsoft.com/help/2005838/kerberos-constrained-delegation-may-require-protocol-transition-in-mul)

## <a name="next-steps"></a>További lépések

[Konfigurálja a KCD-t felügyelt tartományban](../../active-directory-domain-services/deploy-kcd.md).
