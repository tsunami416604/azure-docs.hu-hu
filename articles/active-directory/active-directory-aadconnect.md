<properties
    pageTitle="Azure AD Connect: Helyszíni identitások integrálása az Azure Active Directoryval | Microsoft Azure"
    description="Az Azure AD Connect integrálja a helyszíni címtárakat az Azure Active Directoryval. Így közös identitást biztosíthat az Azure AD-vel integrált Office 365-, Azure- és SaaS-alkalmazásokhoz."
    keywords="introduction to Azure AD Connect, Azure AD Connect overview, what is Azure AD Connect, install active directory"
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/19/2016"
    ms.author="andkjell;billmath"/>

# Helyszíni identitások integrálása az Azure Active Directoryval
Az Azure AD Connect integrálja a helyszíni címtárakat az Azure Active Directoryval. Így közös identitást biztosíthat a felhasználóinak az Azure AD-vel integrált Office 365-, Azure- és SaaS-alkalmazásokhoz. Ez a témakör végigvezeti a tervezéshez, üzembe helyezéshez és működtetéshez szükséges lépéseken. Az anyag a jelen témakörhöz kapcsolódó hivatkozások gyűjteményét is tartalmazza.

<!-- The hardcoded link is a workaround for campaign ids not working in acom links-->

> [AZURE.IMPORTANT] [Az Azure AD Connect a legjobb megoldás, ha a helyszíni címtárat az Azure AD-hez és az Office 365-höz szeretné csatlakoztatni. Itt az ideje, hogy Azure AD Connectre frissítsen a Microsoft Azure Active Directory Sync (DirSync) vagy az Azure AD Sync eszközről, mivel ezek elavultak, és a támogatásuk 2017. április 13-ától megszűnik.](https://azure.microsoft.com/documentation/articles/active-directory-aadconnect-dirsync-deprecated/?WT.mc_id=DirSyncDepACOM)

![Mi az az Azure AD Connect?](./media/active-directory-aadconnect/arch.png)

## Miért érdemes az Azure AD Connect megoldást használni?
A helyszíni címtárak és az Azure AD integrálása révén a felhasználók munkája hatékonyabbá válik, mivel a felhőalapú és a helyszíni erőforrások hozzáféréséhez közös identitás áll a rendelkezésükre. A felhasználók és a szervezetek az alábbi előnyöket élvezhetik:

- A felhasználók egyetlen identitással férhetnek hozzá olyan helyszíni alkalmazásokhoz és felhőszolgáltatásokhoz, mint például az Office 365.

- Mivel egyetlen eszközről van szó, a szinkronizáláshoz és bejelentkezéshez használt rendszer üzembe helyezése egyszerűen végrehajtható.

- Többféle alkalmazási helyzethez is biztosítja az elérhető legújabb képességeket. Az Azure AD Connect olyan identitásintegrációs eszközök régebbi verzióit váltja fel, mint például a DirSync és az Azure AD Sync. További információk: [Hybrid Identity directory integration tools comparison](active-directory-hybrid-identity-design-considerations-tools-comparison.md) (Hibrid identitás: a címtár-integrációs eszközök összehasonlítása).


### Az Azure AD Connect működése
Az Azure Active Directory Connect három elsődleges összetevőből épül fel: a szinkronizálási szolgáltatásokból, az elhagyható Active Directory összevonási szolgáltatásokból és az [Azure AD Connect Health](active-directory-aadconnect-health.md) nevű megfigyelési összetevőből.

<center>![Az Azure AD Connect-verem](./media/active-directory-aadconnect-how-it-works/AADConnectStack2.png)
</center>

- Szinkronizálás – ez az összetevő a felhasználók, csoportok és egyéb objektumok létrehozásáért felelős. Segítségével arról is meggyőződhet, hogy a helyszíni felhasználókhoz és csoportokhoz tartozó identitásadatok megegyeznek a felhőben található hasonló adatokkal.
- AD FS – az összevonás az Azure AD Connect elhagyható összetevője, amelynek segítségével helyszíni AD FS-infrastruktúrát használó hibrid környezetet konfigurálhat. Ezt a szolgáltatást a szervezetek összetett üzembe helyezések (például tartomány-csatlakoztatási SSO, AD bejelentkezési házirend kényszerítése, intelligens kártya vagy külső féltől származó MFA) kezeléséhez használhatják.
- Állapotfigyelés – az Azure AD Connect Health hatékony megfigyelési képességgel rendelkezik, valamint egy központi helyet biztosít az Azure portálon az ilyen tevékenységek megtekintéséhez. További információk: [Azure Active Directory Connect Health](active-directory-aadconnect-health.md).

## Az Azure AD Connect telepítése

Az Azure AD Connect a [Microsoft letöltőközpontból](http://go.microsoft.com/fwlink/?LinkId=615771) tölthető le.


Megoldás | Forgatókönyv
----- | ----- |
Előkészületek – [Hardverkövetelmények és előfeltételek](active-directory-aadconnect-prerequisites.md) | <li>Az Azure AD Connect telepítése előtt végrehajtandó lépések.</li>
[Gyorsbeállítások](active-directory-aadconnect-get-started-express.md) | <li>Ennek a lehetőségnek a használata akkor ajánlott, ha egyerdős AD-vel rendelkezik.</li> <li>Felhasználói bejelentkezés egyetlen jelszóval, jelszó-szinkronizálás segítségével.</li>
[Testreszabott beállítások](active-directory-aadconnect-get-started-custom.md) | <li>Több erdő megléte esetén használatos. Számos helyszíni [topológiát](active-directory-aadconnect-topologies.md) támogat.</li> <li>Testre szabhatja a bejelentkezést, például ADFS-t állíthat be az összevonáshoz, vagy külső féltől származó identitásszolgáltatót használhat.</li> <li>Testre szabhatja a szinkronizálási funkciókat, például a szűrést és a visszaírást.</li>
[Frissítés a DirSync szolgáltatásról](active-directory-aadconnect-dirsync-upgrade-get-started.md) | <li>Akkor használatos, ha már rendelkezik működő DirSync-kiszolgálóval.</li>
[Frissítés Azure AD Sync-ről vagy Azure AD Connect-ről](active-directory-aadconnect-upgrade-previous-version.md)| <li>Igény szerint számos különböző módszer áll rendelkezésére.</li>


[A telepítést követően](active-directory-aadconnect-whats-next.md) ellenőrizze a megfelelő működést, és rendeljen licenceket a felhasználókhoz.

### Az Azure AD Connect telepítésének következő lépései

Témakör |  
--------- | ---------
Az Azure AD Connect letöltése | [Az Azure AD Connect letöltése](http://go.microsoft.com/fwlink/?LinkId=615771)
Telepítés gyorsbeállítások használatával | [Az Azure AD Connect gyorstelepítése](active-directory-aadconnect-get-started-express.md)
Telepítés testreszabott beállítások használatával | [Az Azure AD Connect testreszabott telepítése](active-directory-aadconnect-get-started-custom.md)
Frissítés a DirSync szolgáltatásról | [Frissítés az Azure AD szinkronizáló eszközéről (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md)
A telepítést követően | [A telepítés ellenőrzése és licencek hozzárendelése ](active-directory-aadconnect-whats-next.md)

### További információk az Azure AD Connect telepítésével kapcsolatban

Az [üzemeltetéssel](active-directory-aadconnectsync-operations.md) kapcsolatban felmerülő kérdések kezelésére is érdemes felkészülni. [Vészhelyzet](active-directory-aadconnectsync-operations.md#disaster-recovery) esetére megfontolhatja egy készenléti kiszolgáló üzembe állítását. Ha gyakori konfigurációs módosításokat tervez, egy [átmeneti üzemmódú](active-directory-aadconnectsync-operations.md#staging-mode) kiszolgáló beállításán is elgondolkodhat.

Témakör |  
--------- | ---------
Támogatott topológiák | [Azure AD Connect-topológiák](active-directory-aadconnect-topologies.md)
Tervezési alapelvek | [Az Azure AD Connect tervezési alapelvei](active-directory-aadconnect-design-concepts.md)
Telepítési fiókok | [További információk az Azure AD Connect hitelesítő adataival és engedélyeivel kapcsolatban](active-directory-aadconnect-accounts-permissions.md)
Az üzemeltetés megtervezése | [Az Azure AD Connect szinkronizálása: üzemeltetési feladatok és szempontok](active-directory-aadconnectsync-operations.md)
A felhasználói bejelentkezés lehetőségei | [A felhasználói bejelentkezés lehetőségei az Azure AD Connectben](active-directory-aadconnect-user-signin.md)

## A szinkronizálási funkciók konfigurálása
Az Azure AD Connect számos, szükség szerint bekapcsolható vagy alapértelmezés szerint engedélyezett funkcióval rendelkezik. Bizonyos forgatókönyvek és topológiák esetén ezen funkciók némelyike további konfigurációs beállítások megadását igényli.

[Szűrés](active-directory-aadconnectsync-configure-filtering.md) használatára akkor van szükség, ha korlátozni kívánja az Azure AD-vel szinkronizálandó objektumok körét. Alapértelmezés szerint valamennyi felhasználó, névjegy és Windows 10 rendszert használó számítógép szinkronizálva van. A szűrési beállítások tartományok, szervezeti egységek vagy attribútumok szerint módosíthatók.

A [jelszó-szinkronizálás](active-directory-aadconnectsync-implement-password-synchronization.md) az Active Directory jelszókivonatát szinkronizálja az Azure AD-vel. A végfelhasználó ugyanazt a jelszót használhatja a helyszíni alkalmazásban és a felhőben, de csak az egyik helyen kezelheti. Mivel ez a funkció szolgáltatóként a helyszíni Active Directoryt használja, saját jelszóházirendjét is alkalmazhatja.

A [jelszóvisszaíró](active-directory-passwords-getting-started.md) szolgáltatás lehetővé teszi a felhasználók számára jelszavak módosítását és visszaállítását a felhőben, valamint a helyszíni jelszóházirend alkalmazását.

Az [eszközvisszaíró](active-directory-aadconnect-feature-device-writeback.md) szolgáltatás lehetővé teszi egy regisztrált Azure AD-eszköz visszaírását a helyszíni Active Directoryra, így ott feltételes hozzáféréssel használható.

A [véletlen törlések megakadályozása](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) szolgáltatás alapértelmezés szerint be van kapcsolva, és a felhőcímtárat védi az egy időben végrehajtott többszörös törlésektől. Alapértelmezés szerint futtatásonként 500 törlést tesz lehetővé. Ezt a beállítást szervezetének mérete alapján módosíthatja.

Az [automatikus frissítés](active-directory-aadconnect-feature-automatic-upgrade.md) szolgáltatás alapértelmezés szerint engedélyezve van a gyorsbeállításokkal végrehajtott telepítéseknél, és gondoskodik arról, hogy az Azure AD Connect mindig a legújabb kiadásra frissüljön.

### A szinkronizálási funkciók konfigurálásának következő lépései

Témakör |  
--------- | --------- |
A szűrés konfigurálása | [Az Azure AD Connect szinkronizálása: a szűrés konfigurálása](active-directory-aadconnectsync-configure-filtering.md)
Jelszó-szinkronizálás | [Az Azure AD Connect szinkronizálása: a jelszó-szinkronizálás megvalósítása](active-directory-aadconnectsync-implement-password-synchronization.md)
Jelszóvisszaíró | [A jelszókezelés első lépései](active-directory-passwords-getting-started.md)
Eszközvisszaíró | [Eszközvisszaírás engedélyezése az Azure AD Connectben](active-directory-aadconnect-feature-device-writeback.md)
Véletlen törlések megakadályozása | [Az Azure AD Connect szinkronizálása: véletlen törlések megakadályozása](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
Automatikus frissítés | [Azure AD Connect: automatikus frissítés](active-directory-aadconnect-feature-automatic-upgrade.md)

## Az Azure AD Connect szinkronizálásának testreszabása
Az Azure AD Connect-szinkronizálás alapértelmezett konfigurációja a legtöbb ügyfél és topológia számára megfelelő. Mindig akadnak azonban olyan helyzetek, amikor az alapértelmezett konfiguráció nem használható, és módosítást igényel. A módosítások végrehajtása a jelen szakaszban és a hivatkozott témakörökben leírtak szerint támogatott.

Amennyiben korábban nem dolgozott szinkronizált topológiákkal, ismerkedjen meg a [technikai kulcsfogalmakkal](active-directory-aadconnectsync-technical-concepts.md) foglalkozó részben leírt alapismeretekkel és kifejezésekkel. Az Azure AD Connect az MIIS2003, az ILM2007 és az FIM2010 továbbfejlesztése. Még ha egyes elemek azonosak is, számos módosításra került sor.

Az [alapértelmezett konfiguráció](active-directory-aadconnectsync-understanding-default-configuration.md) azt feltételezi, hogy egynél több erdő is lehet a konfigurációban. Ezekben a topológiákban a felhasználói objektumok kapcsolattartóként is megjelenhetnek egy másik erdőben. A felhasználók rendelkezhetnek hivatkozott postafiókkal is egy másik erőforráserdőben. Az alapértelmezett konfiguráció viselkedésének leírása a [felhasználókkal és kapcsolattartókkal](active-directory-aadconnectsync-understanding-users-and-contacts.md) foglalkozó részben található meg.

A szinkronizálás során alkalmazott konfigurációs modell neve: [deklaratív kiépítés](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md). A speciális attribútumfolyamok [függvényeket](active-directory-aadconnectsync-functions-reference.md) használnak az attribútumok gyors átalakításához. Az Azure AD Connectben elérhető eszközök segítségével a teljes konfiguráció megtekintésére és vizsgálatára van lehetőség. Amennyiben konfigurációs módosításokat kíván végrehajtani, támaszkodjon az [ajánlott eljárásokra](active-directory-aadconnectsync-best-practices-changing-default-configuration.md), így könnyebben használatba veheti az új kiadásokat.

### Az Azure AD Connect-szinkronizálás testreszabásának következő lépései

Témakör |  
--------- | ---------
Az Azure AD Connect-szinkronizáláshoz kapcsolódó összes cikk | [Az Azure AD Connect szinkronizálása](active-directory-aadconnectsync-whatis.md)
Technikai kulcsfogalmak | [Az Azure AD Connect szinkronizálása: technikai kulcsfogalmak](active-directory-aadconnectsync-technical-concepts.md)
Az alapértelmezett konfiguráció ismertetése | [Az Azure AD Connect szinkronizálása: az alapértelmezett konfiguráció ismertetése](active-directory-aadconnectsync-understanding-default-configuration.md)
A felhasználók és a kapcsolattartók ismertetése | [Az Azure AD Connect szinkronizálása: a felhasználók és a kapcsolattartók ismertetése](active-directory-aadconnectsync-understanding-users-and-contacts.md)
Deklaratív kiépítés | [Az Azure AD Connect szinkronizálása: a deklaratív kiépítés kifejezéseinek ismertetése](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
Az alapértelmezett konfiguráció módosítása | [Az alapértelmezett konfiguráció módosításának ajánlott eljárásai](active-directory-aadconnectsync-best-practices-changing-default-configuration.md)

## Az összevonási funkciók konfigurálása
Az ADFS [több tartomány](active-directory-aadconnect-multiple-domains.md) támogatására is konfigurálható. Előfordulhat például, hogy több legfelső szintű tartományt kell használnia az összevonáshoz.

Ha az ADFS-kiszolgáló még nem lett konfigurálva az Azure AD-tanúsítványok automatikus frissítésére, vagy ha nem ADFS rendszerű megoldást alkalmaz, értesítést kap, amikor [frissítenie kell a tanúsítványokat](active-directory-aadconnect-o365-certs.md).

### Az összevonási funkciók konfigurálásának következő lépései

Témakör |  
--------- | ---------
Az ADFS konfigurálása altartományokkal | [Többtartományos támogatás az Azure AD összevonási szolgáltatásához](active-directory-aadconnect-multiple-domains.md)
AD FS-farm kezelése | [Az AD FS kezelése és testreszabása az Azure AD Connect segítségével](active-directory-aadconnect-federation-management.md)
Összevonási tanúsítványok manuális frissítése | [Az Office 365 és az Azure AD összevonási tanúsítványainak megújítása](active-directory-aadconnect-o365-certs.md)

## További információk és hivatkozások

Témakör |  
--------- | --------- |
Verzióelőzmények | [Verzióelőzmények](active-directory-aadconnect-version-history.md)
A DirSync, az Azure ADSync és az Azure AD Connect összehasonlítása | [Címtár-integrációs eszközök összehasonlítása](active-directory-hybrid-identity-design-considerations-tools-comparison.md)
Az Azure AD nem ADFS-elemekkel fennálló kompatibilitásainak listája | [Az Azure AD összevonás kompatibilitási listája](active-directory-aadconnect-federation-compatibility.md)
Szinkronizált attribútumok | [Szinkronizált attribútumok](active-directory-aadconnectsync-attributes-synchronized.md)
Megfigyelés az Azure AD Connect Health használatával | [Azure AD Connect Health](active-directory-aadconnect-health.md)
Gyakori kérdések | [Azure AD Connect – gyakori kérdések](active-directory-aadconnect-faq.md)


**További források**


Az Ignite 2015 bemutatója a helyszíni címtárak felhőbe történő kiterjesztéséhez.

>[AZURE.VIDEO microsoft-ignite-2015-extending-on-premises-directories-to-the-cloud-made-easy-with-azure-active-directory-connect]



<!--HONumber=Jun16_HO2-->


