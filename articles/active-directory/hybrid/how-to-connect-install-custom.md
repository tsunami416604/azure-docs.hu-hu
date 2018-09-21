---
title: 'Azure AD Connect: Testre szabott telepítés | Microsoft Docs'
description: Ez a dokumentum az Azure AD Connect egyéni telepítési beállításait részletezi. Ezeknek az utasításoknak a használatával telepítheti az Active Directoryt az Azure AD Connecten keresztül.
services: active-directory
keywords: mi az Azure AD Connect, az Active Directory telepítése, az Azure AD szükséges összetevői
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 6d42fb79-d9cf-48da-8445-f482c4c536af
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/18/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 9fe18c5e9514d7b8ecc3e38b394ddb4fadcc4393
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46303944"
---
# <a name="custom-installation-of-azure-ad-connect"></a>Az Azure AD Connect testreszabott telepítése
Az Azure AD Connect **Custom settings** (Egyéni beállítások) menüje akkor használható, ha részletesebb beállításokra van szükség a telepítéshez. Akkor van rá szükség, ha több erdővel rendelkezik vagy ha választható szolgáltatásokat kíván konfigurálni, amelyeket a gyorstelepítés nem tartalmaz. Minden olyan esetben szükséges, ahol a [**gyorstelepítés**](how-to-connect-install-express.md) beállítás nem megfelelő az üzemelő példányhoz vagy a topológiához.

Mielőtt elkezdené telepíteni az Azure AD Connect szolgáltatást, bizonyosodjon meg róla, hogy [letöltötte az Azure AD Connectet](http://go.microsoft.com/fwlink/?LinkId=615771), és elvégezte az [Azure AD Connect: Hardware and prerequisites](how-to-connect-install-prerequisites.md) (Azure AD Connect: hardver és előfeltételek) témakörben leírt lépéseket. Emellett győződjön meg róla, hogy a rendelkezésre állnak az [Azure AD Connect-fiókok és -engedélyek](reference-connect-accounts-permissions.md) szakaszban ismertetett fiókok.

Ha az egyedi beállítások nem felelnek meg a topológiának, például a DirSync-frissítésnek, lásd az egyéb forgatókönyvek leírását a [dokumentációban](#related-documentation).

## <a name="custom-settings-installation-of-azure-ad-connect"></a>Az Azure AD Connect telepítése egyéni beállításokkal
### <a name="express-settings"></a>Express Settings (Gyorsbeállítások)
Ezen az oldalon kattintson a **Customize** (Testreszabás) gombra a testre szabott beállításokkal végzett telepítések indításához.

### <a name="install-required-components"></a>Szükséges összetevők telepítése
Amikor a szinkronizálási szolgáltatásokat telepíti, a választható konfiguráció szakaszt bejelöletlenül hagyhatja, és ekkor az Azure AD Connect mindent automatikusan beállít. Beállít egy SQL Server 2012 Express LocalDB példányt, létrehozza a megfelelő csoportokat, és kiosztja az engedélyeket. Ha módosítani kívánja az alapértelmezett beállításokat, az alábbi táblázatban megtekintheti az elérhető választható konfigurációs beállításokat.

![Szükséges összetevők](./media/how-to-connect-install-custom/requiredcomponents.png)

| Választható konfiguráció | Leírás |
| --- | --- |
| Meglévő SQL Server használata |A használatával megadhatja az SQL Server nevét és a példány nevét. Válassza ezt a lehetőséget, ha már rendelkezik adatbázis-kiszolgálóval, amelyet használni kíván. Adja meg a példány nevét, majd vesszővel elválasztva egy portszámot az **Instance Name** (Példány neve) mezőben, ha az SQL Serveren nincs engedélyezve a tallózás. |
| Meglévő szolgáltatásfiók használata |Alapértelmezés szerint az Azure AD Connect egy helyi szolgáltatásfiókot használ, amelyet a szinkronizálási szolgáltatások használhatnak. Amennyiben távoli SQL-kiszolgálót vagy egy hitelesítést igénylő proxyt használ, egy **felügyelt szolgáltatásfiókra** lesz szüksége, vagy egy, a tartományban lévő szolgáltatásfiókra, és ismernie kell a jelszót. Ezekben az esetekben adja meg a használni kívánt fiókot. Bizonyosodjon meg róla, hogy a telepítést futtató felhasználó rendszergazda az SQL Serveren, hogy létre lehessen hozni bejelentkezési adatokat a szolgáltatásfiókhoz.  További információ: [Azure AD Connect-fiókok és -engedélyek](reference-connect-accounts-permissions.md#adsync-service-account). </br></br>A legújabb buildben az SQL-rendszergazda sávon kívül kiépítheti az adatbázist, majd az Azure AD Connect-rendszergazda adatbázis-tulajdonosi jogosultságokkal telepítheti.  További információ: [Az Azure AD Connect telepítése SQL-lel delegált rendszergazdai engedélyekkel](how-to-connect-install-sql-delegation.md).|
| Egyéni szinkronizálási csoportok megadása |Alapértelmezés szerint az Azure AD Connect létrehoz négy helyi csoportot a kiszolgálón a szinkronizálási szolgáltatások telepítésekor. Ezek a csoportok a következők: Administrators (Rendszergazdák) csoport, Operators (Operátorok) csoport, Browse (Tallózás) csoport és Password Reset (Jelszó-visszaállítás) csoport. Itt megadhatja a saját csoportjait. A csoportoknak helyi csoportoknak kell lenniük a kiszolgálón, és nem lehetnek a tartományban. |

### <a name="user-sign-in"></a>Felhasználói bejelentkezés
A szükséges összetevők telepítését követően a rendszer megkéri, hogy válassza ki a felhasználók egyszeri bejelentkezésének módját. Az alábbi táblázat tartalmazza az elérhető beállítások rövid ismertetését. A bejelentkezési módok teljes leírásáért lásd: [Felhasználói bejelentkezés](plan-connect-user-signin.md).

![Felhasználói bejelentkezés](./media/how-to-connect-install-custom/usersignin4.png)

| Egyszeri bejelentkezési beállítás | Leírás |
| --- | --- |
| Jelszókivonat szinkronizálása |A felhasználók a Microsoft felhőszolgáltatásaiba, például az Office 365-be ugyanazzal a jelszóval jelentkezhetnek be, amelyet a helyszíni hálózaton is használnak. A felhasználók jelszavai szinkronizálva vannak az Azure AD szolgáltatásba jelszókivonatként, és a hitelesítés a felhőben történik. További információkért lásd a [jelszókivonat szinkronizálásával](how-to-connect-password-hash-synchronization.md) foglalkozó részt. |
|Átmenő hitelesítés|A felhasználók a Microsoft felhőszolgáltatásaiba, például az Office 365-be ugyanazzal a jelszóval jelentkezhetnek be, amelyet a helyszíni hálózaton is használnak.  A rendszer a felhasználói jelszavakat az őket ellenőrző helyszíni Active Directory-tartományvezérlőn keresztül továbbítja.
| Összevonás az AD FS rendszerrel |A felhasználók a Microsoft felhőszolgáltatásaiba, például az Office 365-be ugyanazzal a jelszóval jelentkezhetnek be, amelyet a helyszíni hálózaton is használnak.  A rendszer átirányítja a felhasználókat helyszíni AD FS-példányukra a bejelentkezéshez, és a hitelesítés a helyszíni rendszeren történik. |
| Összevonás a PingFederate-tel|A felhasználók a Microsoft felhőszolgáltatásaiba, például az Office 365-be ugyanazzal a jelszóval jelentkezhetnek be, amelyet a helyszíni hálózaton is használnak.  A rendszer átirányítja a felhasználókat a helyszíni PingFederate-példányukra a bejelentkezéshez, és a hitelesítés a helyszíni rendszeren történik. |
| Nincs konfigurálás |Nincs telepítve és konfigurálva felhasználói bejelentkezési szolgáltatás. Válassza ezt a lehetőséget, ha már rendelkezik külső fél által biztosított összevonási kiszolgálóval vagy más meglévő megoldással. |
|Egyszeri bejelentkezés engedélyezése|Ez a lehetőség jelszó-szinkronizálás és átmenő hitelesítés mellett is elérhető, és egyszeri bejelentkezést biztosít az asztali gépek felhasználóinak a vállalati hálózaton. További információkért tekintse meg az [egyszeri bejelentkezést](how-to-connect-sso.md) ismertető témakört. </br>Vegye figyelembe, hogy ez a lehetőség az AD FS-ügyfelek számára nem érhető el, mert az AD FS már tartalmaz ennek megfelelő szintű egyszeri bejelentkezési funkciót.</br>

### <a name="connect-to-azure-ad"></a>Csatlakozás az Azure AD szolgáltatáshoz
A Csatlakozás az Azure AD szolgáltatáshoz képernyőn adjon meg egy globális rendszergazdai fiókot és jelszót. Ha az előző oldalon az **Összevonás az AD FS rendszerrel** lehetőséget választotta, ne olyan fiókkal jelentkezzen be, amely egy, az összevonásra engedélyezni kívánt tartományban található. Javasoljuk, hogy egy, az Azure AD címtárral kapott alapértelmezett **onmicrosoft.com** tartományban lévő fiókot használjon.

Ez a fiók kizárólag egy Azure AD-szolgáltatásfiók létrehozására lesz használva, és csak a varázsló befejeztéig.  
![Felhasználói bejelentkezés](./media/how-to-connect-install-custom/connectaad.png)

Ha a globális rendszergazdai fiókon engedélyezve van az MFA, újra meg kell adnia a jelszót a bejelentkezési előugró ablakban, és teljesítenie kell az MFA-kérdést. A kérdés lehet egy ellenőrző kód megadása vagy egy telefonhívás.  
![Felhasználói bejelentkezési MFA](./media/how-to-connect-install-custom/connectaadmfa.png)

A globális rendszergazdai fiókon engedélyezve lehet a [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md) is.

Ha hibaüzenetet kap, és problémák adódnak a kapcsolódással, tekintse meg a [Troubleshoot connectivity problems](tshoot-connect-connectivity.md) (Kapcsolati problémák elhárítása) szakaszt.

## <a name="pages-under-the-sync-section"></a>Oldalak a Sync (Szinkronizálás) szakaszban

### <a name="connect-your-directories"></a>Csatlakoztassa a címtárakat
Az Active Directory tartományi szolgáltatások csatlakoztatásához az Azure AD Connectnek szüksége van az erdő nevére és egy megfelelő engedélyekkel rendelkező fiók hitelesítő adataira.

![Címtár csatlakoztatása](./media/how-to-connect-install-custom/connectdir01.png)

Miután beírta az erdő nevét és a **Címtár hozzáadása** gombra kattintott, megjelenik egy felugró párbeszédpanel a következő lehetőségekkel:

| Beállítás | Leírás |
| --- | --- |
| Új fiók létrehozása | Válassza ezt a lehetőséget, ha az Azure AD Connect varázslójával szeretné létrehozni az AD DS-fiókot az Azure AD Connect számára, amellyel csatlakozhat az AD-erdőhöz a címtár szinkronizálása során. Ha ezt a lehetőséget választja, adja meg egy vállalati rendszergazdai fiók felhasználónevét és jelszavát. Az Azure AD Connect varázsló a megadott vállalati rendszergazdai fiók használatával hozza létre a szükséges AD DS-fiókot. A tartományrészt megadhatja NetBios- vagy FQDN-formátumban, vagyis FABRIKAM\rendszergazda vagy fabrikam.com\rendszergazda alakban. |
| Meglévő fiók használata | Válassza ezt a lehetőséget, ha meglévő AD DS-fiókot szeretne megadni az Azure AD Connect számára, amellyel csatlakozhat az AD-erdőhöz a címtár szinkronizálása során. A tartományrészt megadhatja NetBios- vagy FQDN-formátumban, vagyis FABRIKAM\syncuser vagy fabrikam.com\syncuser alakban. A fiók lehet normál felhasználói fiók is, mivel csupán az alapértelmezett olvasási engedélyek szükségesek. A forgatókönyvtől függően azonban más engedélyekre is szüksége lehet. További információkért lásd: [Azure AD Connect-fiókok és -engedélyek](reference-connect-accounts-permissions.md##create-the-ad-ds-connector-account). |

![Címtár csatlakoztatása](./media/how-to-connect-install-custom/connectdir02.png)

### <a name="azure-ad-sign-in-configuration"></a>Azure AD-bejelentkezés konfigurálása
Ezen az oldalon áttekintheti a helyszíni AD DS rendszerben jelenlévő és az Azure AD szolgáltatásban ellenőrzött UPN-tartományokat. Az oldalon emellett konfigurálhatja a userPrincipalName tulajdonsághoz használt attribútumot is.

![Nem ellenőrzött tartományok](./media/how-to-connect-install-custom/aadsigninconfig2.png)  
Tekintse át az összes **Not Added** (Hozzá nem adott) és **Not Verified** (Nem ellenőrzött) megjelöléssel rendelkező tartományt. Bizonyosodjon meg róla, hogy az Ön által használt tartományok ellenőrizve lettek az Azure AD szolgáltatásban. Miután ellenőrizte a tartományokat, kattintson a Frissítés szimbólumra. További információkért lásd: [A tartomány hozzáadása és ellenőrzése](../active-directory-domains-add-azure-portal.md)

**UserPrincipalName** – A userPrincipalName attribútum a felhasználók által az Azure AD és az Office 365 szolgáltatásokba való bejelentkezéskor látható attribútum. A más néven UPN-utótagként ismert használt tartományokat ellenőrizni kell az Azure AD szolgáltatásban a felhasználók szinkronizálása előtt. A Microsoft javasolja a userPrincipalName alapértelmezett attribútum megtartását. Ha az attribútum nem átirányítható és nem ellenőrizhető, ebben az esetben lehetséges egy másik attribútumot választani. Megjelölheti például az e-mail-címet a bejelentkezési azonosítót hordozó attribútumként. A userPrincipalName attribútumtól eltérő attribútumot más néven **Másik azonosítóként** ismerjük. A Másik azonosító értékének igazodnia kell az RFC822 szabványhoz. A Másik azonosító a jelszó-szinkronizálással és az összevonással egyaránt használható. Az attribútum nem lehet többértékűként megadva az Active Directoryban akkor sem, ha csak egyetlen értékkel rendelkezik.

>[!NOTE]
> Az átmenő hitelesítés engedélyezésekor legalább egy ellenőrzött tartománnyal kell rendelkeznie, hogy továbbléphessen a varázslóban.

> [!WARNING]
> A Másik azonosító használata nem kompatibilis az összes Office 365 számítási feladattal. A további információkat a [Másik bejelentkezési azonosító konfigurálása](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) című rész tartalmazza.
>
>

### <a name="domain-and-ou-filtering"></a>Tartományok és szervezeti egységek szűrése
Alapértelmezés szerint minden tartomány és szervezeti egység szinkronizálva van. Amennyiben vannak olyan tartományok és szervezeti egységek, amelyeket nem kíván szinkronizálni az Azure AD szolgáltatásba, törölheti ezek kijelölését.  
![Domain/OU-szűrés](./media/how-to-connect-install-custom/domainoufiltering.png)  
A varázslónak ezen az oldalán a tartományalapú és szervezeti egység szerinti szűrés konfigurálható. Ha módosításokat tervez, tekintse meg a [tartományalapú szűréssel](how-to-connect-sync-configure-filtering.md#domain-based-filtering) és a [szervezeti egység szerinti szűréssel](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) kapcsolatos témaköröket a módosítások végrehajtása előtt. Egyes szervezeti egységek létfontosságúak a működéshez, és ezek kijelölését nem szabad törölni.

Ha a szervezeti egység szerinti szűrést az Azure AD Connect 1.1.524.0 előtti verziójával használja, a rendszer alapértelmezés szerint szinkronizálja a később hozzáadott új szervezeti egységeket. Ha azt szeretné, hogy a rendszer ne szinkronizálja az új szervezeti egységeket, akkor azt azután állíthatja be, hogy a varázsló a [szervezeti egység szerinti szűrés](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) beállítással befejeződött Az Azure AD Connect 1.1.524.0-s vagy újabb verziója esetén jelezheti, hogy szeretné-e szinkronizálni az új szervezeti egységeket.

Ha [csoportalapú szűrés](#sync-filtering-based-on-groups) használatát tervezi, győződjön meg arról, hogy a csoport szervezeti egysége szerepel, és nincs kiszűrve a szervezeti egység szerinti szűrésben. A rendszer a szervezeti egység szerinti szűrést a csoportalapú szűrés előtt végzi el.

Előfordulhat, hogy egyes tartományok tűzfalkorlátozások miatt nem érhetőek el. Ezeknek a tartományoknak a kijelölése alapértelmezés szerint törölve van, és egy figyelmeztető jelöléssel rendelkeznek.  
![Nem elérhető tartományok](./media/how-to-connect-install-custom/unreachable.png)  
Ha ilyen figyelmeztető jelölést lát, ellenőrizze, hogy ezek a tartományok valóban nem érhetőek el, és a figyelmeztetés jogos.

### <a name="uniquely-identifying-your-users"></a>Felhasználók egyedi azonosítása

#### <a name="select-how-users-should-be-identified-in-your-on-premises-directories"></a>Válassza ki, hogyan történjen a felhasználók azonosítása a helyszíni címtárakban
Az erdők közötti megfeleltetés szolgáltatás segítségével meghatározhatja, hogy az AD DS-erdőkből származó felhasználók miként szerepeljenek az Azure AD szolgáltatásban. A felhasználók vagy egyszer szerepelnek az összes erdőben, vagy engedélyezett és letiltott fiókok kombinációjával rendelkeznek. A felhasználók egyes erdőkben esetleg kapcsolattartóként is szerepelhetnek.

![Egyedi](./media/how-to-connect-install-custom/unique2.png)

| Beállítás | Leírás |
| --- | --- |
| [A felhasználók csak egyszer szerepelnek az összes erdőben](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Minden felhasználó külön objektumként van létrehozva az Azure AD szolgáltatásban. Az objektumok nincsenek összekapcsolva a metaverzumban. |
| [Mail attribútum](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Ez a beállítás összekapcsolja a felhasználókat és a kapcsolattartókat, amennyiben a levél attribútum ugyanazzal az értékkel rendelkezik különböző felhőkben. Használja ezt a beállítást, ha a kapcsolattartók a GALSync használatával lettek létrehozva. Ha ez a beállítás ki van választva, azok a felhasználói objektumok, amelyek Mail attribútuma nincs feltöltve adatokkal, nem lesznek szinkronizálva az Azure AD-be. |
| [ObjectSID és msExchangeMasterAccountSID/ msRTCSIP-OriginatorSid](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Ez a beállítás összeköt egy fiókerdőben található engedélyezett felhasználót egy erőforráserdőben található letiltott felhasználóval. Az Exchange ezt a konfigurációt csatolt postaládaként ismeri. A beállítás akkor is használható, ha csak a Lync szolgáltatást használja, és az Exchange nincs jelen az erőforráserdőben. |
| sAMAccountName and MailNickName (sAMAccountName és MailNickName) |Ez a beállítás azokat az attribútumokat csatlakoztatja, ahol a felhasználó bejelentkezési azonosítója várhatóan megtalálható. |
| A specific attribute (Egy adott attribútum) |Ez a beállítás lehetővé teszi, hogy kiválassza a saját attribútumát. Ha ez a beállítás ki van választva, azok a felhasználói objektumok, amelyek (kiválasztott) attribútuma nincs feltöltve adatokkal, nem lesznek szinkronizálva az Azure AD-be. **Korlátozás:** Mindenképp olyan attribútumot kell választania, amely már megtalálható a metaverzumban. Amennyiben egy egyedi attribútumot választ (amely nem tartozik a metaverzumba), a varázsló nem képes befejezni a feladatot. |

#### <a name="select-how-users-should-be-identified-with-azure-ad---source-anchor"></a>Válassza ki, hogyan történjen a felhasználók azonosítása az Azure AD – Source Anchor (Forráshorgony) használatával
A sourceAnchor egy olyan attribútum, amely a felhasználói objektum élettartama alatt megváltoztathatatlan. Ez a helyszíni felhasználót az Azure AD-felhasználóval összekötő elsődleges kulcs.

| Beállítás | Leírás |
| --- | --- |
| Let Azure manage the source anchor for me (Az Azure kezelje a forráshorgonyt) | Válassza ezt a lehetőséget, ha azt szeretné, hogy az Azure AD válassza ki az attribútumot. Ha ezt a lehetőséget választja, az Azure AD Connect varázsló a sourceAnchor attribútumválasztási logikát alkalmazza, amelyről a cikk [Az Azure AD Connect tervezési alapelvei – Az ms-DS-ConsistencyGuid használata sourceAnchorként](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) szakaszában található leírás. A varázsló értesíti arról, hogy melyik attribútum lett kiválasztva a forráshorgony-attribútumként az egyéni telepítés befejezése után. |
| A specific attribute (Egy adott attribútum) | Válassza ezt a lehetőséget, ha meglévő AD-attribútumot szeretne megadni forráshorgony-attribútumként. |

Mivel az attribútum nem módosítható, megfelelő attribútumot kell választania a tervezés során. Egy jó jelölt az objectGUID. Ez az attribútum nem változik, hacsak a felhasználói fiókot nem helyezi át az erdők/tartományok között. Többerdős környezetben, ahol az erdők közt mozgatja a fiókokat, egy másik attribútumot kell használni, például egy olyat, amelyik az employeeID azonosítót tartalmazza. Ne használjon olyan attribútumokat, amelyek változhatnak, ha a felhasználó megházasodik vagy más pozícióba kerül. Nem használhat olyan attribútumokat, amelyek tartalmazzák a @-sign jelet, így az e-mail-cím vagy a userPrincipalName nem használható. Az attribútum továbbá különbséget tesz a kis- és a nagybetűk között, ezért amikor az objektumokat az erdők között mozgatja, ügyeljen a kis-/nagybetűk megőrzésére. A bináris attribútumok base64-kódolásúak, az egyéb típusú attribútumok azonban megmaradnak kódolatlan állapotban. Összevonási forgatókönyvekben és néhány Azure AD felületen ez az attribútum immutableID néven ismert. A forráshorgonnyal kapcsolatban további információt a [tervezési alapelvek](plan-connect-design-concepts.md#sourceanchor) leírásában talál.

### <a name="sync-filtering-based-on-groups"></a>Szinkronizálás szűrése csoportok alapján
A csoportra szűrés szolgáltatás használatával szinkronizálhatja az objektumok kisebb részhalmazát is próbaüzem esetén. A szolgáltatás használatához hozzon létre egy csoportot e célból a helyszíni Active Directory szolgáltatásban. Ezután adja hozzá a felhasználókat és csoportokat, amelyeket közvetlen tagokként kíván szinkronizálni az Azure AD szolgáltatásba. Később hozzáadhat vagy eltávolíthat felhasználókat a csoportban, és így karbantarthatja az objektumok listáját, amelyeknek az Azure AD szolgáltatásban jelen kell lenniük. Az összes szinkronizálni kívánt objektumnak a csoport közvetlen tagjának kell lennie. A felhasználóknak, csoportoknak, kapcsolattartóknak és számítógépeknek/eszközöknek mind közvetlen tagnak kell lenniük. A beágyazott csoporttagság feloldása nem lehetséges. Ha egy csoportot tagként hozzáad, csak maga a csoport lesz hozzáadva, a tagjai nem.

![Szinkronizálás szűrése](./media/how-to-connect-install-custom/filter2.png)

> [!WARNING]
> Ez a szolgáltatás kizárólag a próbatelepítések támogatásának célját szolgálja. Ne használja éles környezetben üzemelő teljes körű példányok esetében.
>
>

Éles környezetben üzemelő teljes körű példányok esetében nehéz fenntartani egy, az összes szinkronizálni kívánt objektumot tartalmazó csoportot. Ehelyett használja [A szűrés konfigurálása](how-to-connect-sync-configure-filtering.md) részben ismertetett módszerek valamelyikét.

### <a name="optional-features"></a>Optional Features (Választható szolgáltatások)
A képernyő segítségével beállíthatja a választható szolgáltatásokat az egyedi forgatókönyvekhez.

![Választható szolgáltatások](./media/how-to-connect-install-custom/optional2.png)

> [!WARNING]
> Amennyiben a DirSync vagy az Azure AD Sync jelenleg aktív, ne aktiválja az Azure AD Connect egyik visszaíró szolgáltatását sem.
>
>

| Optional Features (Választható szolgáltatások) | Leírás |
| --- | --- |
| Exchange Hybrid Deployment (Exchange hibrid telepítés) |Az Exchange hibrid telepítés lehetővé teszi, hogy az Exchange postafiókok a helyszínen és az Office 365-ben egy időben létezzenek. Az Azure AD Connect visszaszinkronizálja az [attribútumok](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) egy adott halmazát az Azure AD szolgáltatásból a helyszíni címtárba. |
| Exchange Mail Public Folders (Exchange-levelezés – nyilvános mappák) | Az Exchange-levelezés nyilvános mappák funkciójával szinkronizálhatja a levelezési célú nyilvánosmappa-objektumokat a helyszíni Active Directoryból az Azure AD-be. |
| Azure AD app and attribute filtering (Azure AD alkalmazás- és attribútumszűrés) |Az Azure AD alkalmazás- és attribútumszűrés engedélyezésével a szinkronizált attribútumok halmaza testre szabható. Ez a beállítás két további konfigurációs oldallal bővíti a varázslót. További információkért lásd: [Azure AD alkalmazás- és attribútumszűrés](#azure-ad-app-and-attribute-filtering). |
| Jelszókivonat szinkronizálása |Amennyiben az összevonás megoldást választotta a bejelentkezéshez, engedélyezheti ezt a beállítást. A jelszókivonat-szinkronizálás ezt követően használható másodlagos beállításként. További információk: [Jelszókivonat szinkronizálása](how-to-connect-password-hash-synchronization.md). </br></br>Ha az átmenő hitelesítést választotta, ez a beállítás is engedélyezhető, hogy támogassa a régebbi ügyfelek biztonsági mentési lehetőségként történő használatát. További információk: [Jelszókivonat szinkronizálása](how-to-connect-password-hash-synchronization.md).|
| Jelszóvisszaíró |A jelszóvisszaíró engedélyezésével az Azure AD szolgáltatásban végrehajtott jelszómódosítások visszaíródnak a helyszíni címtárba. További részletekért lásd: [A jelszókezelés első lépései](../authentication/quickstart-sspr.md). |
| Group writeback (Csoportvisszaíró) |Amennyiben használja az **Office 365 Csoportok** szolgáltatást, ezeket a csoportokat szerepeltetheti a helyszíni Active Directory szolgáltatásban is. Ez a beállítás kizárólag akkor elérhető, ha az Exchange jelen van a helyszíni Active Directory szolgáltatásban. További információkért lásd: [Csoportvisszaíró](how-to-connect-preview.md#group-writeback). |
| Eszközvisszaíró |A használatával visszaírhatja az Azure AD-eszközobjektumokat a helyszíni Active Directory szolgáltatásba feltételes hozzáférésű forgatókönyvekhez. További információkért lásd: [Eszközvisszaírás engedélyezése az Azure AD Connectben](how-to-connect-device-writeback.md). |
| Directory extension attribute sync (Címtárbővítmény-attribútumok szinkronizálása) |A címtárbővítmény-attribútumok szinkronizálásának engedélyezésével a megadott attribútumok szinkronizálva lesznek az Azure AD szolgáltatásba. További információkért lásd: [Címtárbővítmények](how-to-connect-sync-feature-directory-extensions.md). |

### <a name="azure-ad-app-and-attribute-filtering"></a>Azure AD app and attribute filtering (Azure AD alkalmazás- és attribútumszűrés)
Ha korlátozni szeretné, hogy melyik attribútumok legyenek szinkronizálva az Azure AD szolgáltatásba, kezdetnek válassza ki a használt alkalmazásokat. Amennyiben konfigurációmódosításokat végez ezen az oldalon, kifejezetten egy új szolgáltatást kell választania a telepítővarázsló újrafuttatásával.

![Választható szolgáltatások – Alkalmazások](./media/how-to-connect-install-custom/azureadapps2.png)

Az előző lépésben kiválasztott szolgáltatások alapján ez az oldal azt mutatja, hogy az összes attribútum szinkronizálva van. Ez a lista az összes épp szinkronizált objektumtípus összesítése. Amennyiben bizonyos attribútumokat nem kíván szinkronizálni, törölje azok jelölését.

![Választható szolgáltatások – Attribútumok](./media/how-to-connect-install-custom/azureadattributes2.png)

> [!WARNING]
> Az attribútumok eltávolítása hatással lehet a funkcionalitásra. Az ajánlott eljárásokért és javaslatokért lásd: [szinkronizált attribútumok](reference-connect-sync-attributes-synchronized.md#attributes-to-synchronize).
>
>

### <a name="directory-extension-attribute-sync"></a>Címtárbővítmény-attribútumok szinkronizálása
Az Azure AD szolgáltatásban lévő sémát kiterjesztheti a szervezet által hozzáadott egyedi attribútumokkal vagy az Active Directory szolgáltatásban lévő egyéb attribútumokkal. A szolgáltatás használatához válassza a **Directory Extension attribute sync** (Címtárbővítmény-attribútumok szinkronizálása) szakaszt az **Optional Features** (Választható szolgáltatások) lapon. Több attribútumot is kiválaszthat szinkronizálásra ezen az oldalon.

![Címtárbővítmények](./media/how-to-connect-install-custom/extension2.png)

További információkért lásd: [Címtárbővítmények](how-to-connect-sync-feature-directory-extensions.md).

### <a name="enabling-single-sign-on-sso"></a>Egyszeri bejelentkezés (SSO) engedélyezése
Az egyszeri bejelentkezés jelszó-szinkronizálással vagy átmenő hitelesítéssel történő használatának konfigurálása egyszerű folyamat, amelyet minden, az Azure AD-vel szinkronizált erdő esetében csak egyszer kell elvégezni. A konfiguráció az alábbi két lépésből áll:

1.  A szükséges számítógépfiók létrehozása a helyszíni Active Directoryban.
2.  Az ügyfélgépek intranetes zónájának konfigurálása az egyszeri bejelentkezés támogatására.

#### <a name="create-the-computer-account-in-active-directory"></a>A számítógépfiók létrehozása a helyszíni Active Directoryban
Minden, az Azure AD Connecttel hozzáadott erdőhöz tartományi rendszergazdai hitelesítő adatokat kell megadni, hogy létre lehessen hozni bennük a számítógépfiókot. A hitelesítő adatokat a rendszer csak a fiók létrehozásához használja, azokat nem tárolja, és nem használja fel más műveletekhez. Egyszerűen adja meg a hitelesítő adatokat az Azure AD Connect varázsló **Enable Single sign on** (Egyszeri bejelentkezés engedélyezése) lapján, ahogy az alább is látható:

![Egyszeri bejelentkezés engedélyezése](./media/how-to-connect-install-custom/enablesso.png)

>[!NOTE]
>Egyes erdőket ki is hagyhat, ha nem szeretne az esetükben egyszeri bejelentkezést használni.

#### <a name="configure-the-intranet-zone-for-client-machines"></a>Az intranetes zóna konfigurálása ügyfélgépekre
Annak biztosítása érdekében, hogy az ügyfél automatikusan az intranet zónába jelentkezzen be, gondoskodni kell arról, hogy a két URL-cím az intranet zóna része legyen. Ezzel biztosítható, hogy a tartományhoz csatlakozó számítógép automatikusan Kerberos-jegyet küldjön Azure AD-hez, amikor a vállalati hálózathoz csatlakozik.
Egy számítógépen, amelyen telepítve vannak a csoportházirend-kezelési eszközök:

1.  Nyissa meg a csoportházirend-kezelési eszközöket.
2.  Módosítsa azt a csoportházirendet, amelyik minden felhasználóra vonatkozni fog. Ilyen lehet például az alapértelmezett tartományi házirend.
3.  Lépjen a **Felhasználói konfiguráció\Felügyeleti sablonok\Windows-összetevők\Internet Explorer\Internet vezérlőpult\Biztonság lapra**, és válassza ki a **Helyek zónákhoz való társításának listáját** az alábbi ábra szerint.
4.  Engedélyezze a szabályzatot, és adja meg a következő elemet a párbeszédpanelen.

        Value: `https://autologon.microsoftazuread-sso.com`  
        Data: 1  
    

5.  Ennek a következőképpen kell kinéznie:  
![Intranet zónák](./media/how-to-connect-install-custom/sitezone.png)

6.  Kattintson kétszer az **OK** gombra.

## <a name="configuring-federation-with-ad-fs"></a>AD FS-összevonás konfigurálása
Az AD FS konfigurálása az Azure AD Connecttel egyszerű feladat, és mindössze néhány kattintást igényel. A konfigurálás előtt a következőkre van szükség.

* Egy Windows Server 2012 R2 vagy újabb verziójú kiszolgálóra az összevonási kiszolgálóhoz, amelyen a távoli felügyelet engedélyezve van
* Egy Windows Server 2012 R2 vagy újabb verziójú kiszolgálóra a webalkalmazás-proxyhoz, amelyen a távoli felügyelet engedélyezve van
* Egy SSL-tanúsítványra az összevonási szolgáltatás használni kívánt nevéhez (például sts.contoso.com)

>[!NOTE]
>Az AD FS-farm SSL-tanúsítványát akkor is frissítheti az Azure AD Connect használatával, ha nem azzal kezeli az összevonási megbízhatósági kapcsolatot.

### <a name="ad-fs-configuration-pre-requisites"></a>Az AD FS konfigurálásának előfeltételei
Az AD FS farm konfigurálásához az Azure AD Connect használatával ellenőrizze, hogy a WinRM engedélyezve van-e a távoli kiszolgálókon. Győződjön meg róla, hogy végrehajtotta a többi feladatot az [összevonási előfeltételekben](how-to-connect-install-prerequisites.md#prerequisites-for-federation-installation-and-configuration). Emellett tekintse át a [3. táblázat – Azure AD Connect and Federation Servers/WAP](reference-connect-ports.md#table-3---azure-ad-connect-and-ad-fs-federation-serverswap) (Azure AD Connect és az összevonási kiszolgálók/WAP) listában szereplő portkövetelményeket.

### <a name="create-a-new-ad-fs-farm-or-use-an-existing-ad-fs-farm"></a>Új AD FS farm létrehozása vagy meglévő AD FS farm használata
Használhat egy meglévő AD FS farmot, vagy dönthet úgy, hogy létrehoz egy újat. Amennyiben úgy dönt, hogy újat hoz létre, meg kell adnia az SSL-tanúsítványt. Ha az SSL-tanúsítvány jelszóval védett, a rendszer kéri a jelszót.

![AD FS farm](./media/how-to-connect-install-custom/adfs1.png)

Ha úgy döntött, hogy egy meglévő farmot használ, a következő lépés az AD FS és az Azure AD közti bizalmi viszony konfigurálása a megfelelő képernyőn.

>[!NOTE]
>Az Azure AD Connect segítségével csak egy AD FS-farm kezelhető. Ha rendelkezik meglévő összevonási megbízhatósági kapcsolattal, és a kiválasztott AD FS-farmon konfigurálva van az Azure AD, az Azure AD Connect akkor is teljesen újraépíti majd a megbízhatósági kapcsolatot.

### <a name="specify-the-ad-fs-servers"></a>Az AD FS kiszolgálók megadása
Határozza meg a kiszolgálókat, amelyekre az AD FS szolgáltatást telepíteni kívánja. Egy vagy több kiszolgálót is hozzáadhat kapacitástervezési igényeitől függően. Csatlakoztassa az összes AD FS-kiszolgálót (WAP-kiszolgálók esetén nem szükséges) az Active Directoryhoz, mielőtt elvégezné ezt a konfigurálást. A Microsoft a teszt- és próbatelepítésekhez egyetlen AD FS-kiszolgáló üzembe helyezését javasolja. Később, a kezdeti konfigurációt követően az Azure AD Connect ismételt futtatásával további kiszolgálókat adhat hozzá és helyezhet üzembe méretezési igényeitől függően.

> [!NOTE]
> A konfiguráció végrehajtását megelőzően bizonyosodjon meg róla, hogy mindegyik kiszolgáló csatlakozik egy AD-tartományhoz.
>
>

![AD FS-kiszolgálók](./media/how-to-connect-install-custom/adfs2.png)

### <a name="specify-the-web-application-proxy-servers"></a>A webalkalmazás-proxy kiszolgálók megadása
Határozza meg a webalkalmazás-proxy kiszolgálóként használni kívánt kiszolgálókat. A webalkalmazás-proxy kiszolgáló a szegélyhálózaton (DMZ-ben) lesz üzembe helyezve (az extranet oldalon), és az extranetről érkező hitelesítési kéréseket támogatja. Egy vagy több kiszolgálót is hozzáadhat kapacitástervezési igényeitől függően. A Microsoft a teszt- és próbatelepítésekhez egyetlen webalkalmazás-proxykiszolgáló üzembe helyezését javasolja. Később, a kezdeti konfigurációt követően az Azure AD Connect ismételt futtatásával további kiszolgálókat adhat hozzá és helyezhet üzembe méretezési igényeitől függően. Javasoljuk, hogy ugyanennyi proxykiszolgálóval rendelkezzen az intranetről érkező hitelesítési kérések kiszolgálására.

> [!NOTE]
> <li> Amennyiben a használt fiók nem helyi rendszergazda a WAP-kiszolgálókon, a rendszer rendszergazdai hitelesítő adatokat kér.</li>
> <li> A jelen lépés végrehajtása előtt győződjön meg róla, hogy fennáll a HTTP/HTTPS kapcsolat az Azure AD Connect-kiszolgáló és a webalkalmazás-proxy kiszolgáló közt.</li>
> <li> A hitelesítési kérések zavartalan áramlása érdekében győződjön meg róla, hogy fennáll a HTTP/HTTPS kapcsolat a webalkalmazás-kiszolgáló és az AD FS-kiszolgáló közt.</li>
>

![Webalkalmazás](./media/how-to-connect-install-custom/adfs3.png)

A rendszer kéri a hitelesítő adatok megadását, hogy a webalkalmazás-kiszolgáló létrehozhasson egy biztonságos kapcsolatot az AD FS-kiszolgálóval. Ezeknek helyi rendszergazdai hitelesítő adatoknak kell lenniük az AD FS kiszolgálón.

![Proxy](./media/how-to-connect-install-custom/adfs4.png)

### <a name="specify-the-service-account-for-the-ad-fs-service"></a>Szolgáltatásfiók megadása az AD FS szolgáltatáshoz
Az AD FS szolgáltatás egy tartományi szolgáltatásfiókot igényel a felhasználók hitelesítéséhez és a felhasználók adatainak kikereséséhez az Active Directoryban. A szolgáltatásfiókok két típusát tudja támogatni:

* **Csoportosan felügyelt szolgáltatásfiók** – Ez az Active Directory tartományi szolgáltatásokban a Windows Server 2012 kiadásával lett bevezetve. Az a fióktípus a szolgáltatások, például az AD FS számára egy közös fiókot biztosít, így nem szükséges a jelszót rendszeresen frissíteni. Ezt a lehetőséget akkor alkalmazza, ha Windows Server 2012 tartományvezérlőkkel rendelkezik a tartományban, amelyhez az AD FS-kiszolgálók tartoznak.
* **Tartományi felhasználói fiók** – Az ilyen típusú fiókhoz meg kell adnia egy jelszót, és azt rendszeresen frissíteni, amikor változik vagy lejár. Ezt a lehetőséget kizárólag akkor alkalmazza, ha nem rendelkezik Windows Server 2012 tartományvezérlőkkel a tartományban, amelyhez az AD FS-kiszolgálók tartoznak.

Amennyiben a Csoportosan felügyelt szolgáltatásfiók lehetőséget választotta, és ez a szolgáltatás még nem volt használva az Active Directoryban, a rendszer vállalati rendszergazdai hitelesítő adatokat kér. A hitelesítő adatok a kulcstároló indításához és a szolgáltatás az Active Directoryban való engedélyezéséhez szükségesek.

> [!NOTE]
> Az Azure AD Connect ellenőrzést futtat, hogy észlelje, az AD FS szolgáltatás már regisztrálva van-e a tartományban egyszerű szolgáltatásnévként.  Az AD DS nem engedélyezi az ismétlődő egyszerű szolgáltatásnevek regisztrálását.  Ha a rendszer ismétlődő egyszerű szolgáltatásnevet talál, az eltávolításáig a folyamat nem folytatódik.

![AD FS-szolgáltatásfiók](./media/how-to-connect-install-custom/adfs5.png)

### <a name="select-the-azure-ad-domain-that-you-wish-to-federate"></a>Válassza ki az összevonni kívánt Azure AD-tartományt
Ez a konfiguráció az AD FS és az Azure AD közti összevonási kapcsolat beállítására használatos. Beállítja, hogy az AD FS biztonsági jogkivonatokat adjon ki az Azure AD szolgáltatás számára, valamint hogy az Azure AD megbízzon az adott AD FS-példány által kiadott jogkivonatokban. Ezen az oldalon csak egyetlen tartomány konfigurálható a kezdeti telepítés alkalmával. További kiszolgálókat később, az Azure AD Connect ismételt futtatásával konfigurálhat.

![Azure AD-tartomány](./media/how-to-connect-install-custom/adfs6.png)

### <a name="verify-the-azure-ad-domain-selected-for-federation"></a>Az összevonáshoz kiválasztott Azure AD-tartomány ellenőrzése
Amikor kiválasztja az összevonandó tartományt, az Azure AD Connect megadja a nem ellenőrzött tartományok ellenőrzéséhez szükséges adatokat. Az információk használatáról [a tartomány hozzáadását és ellenőrzését](../active-directory-domains-add-azure-portal.md) ismertető témakörben olvashat.

![Azure AD-tartomány](./media/how-to-connect-install-custom/verifyfeddomain.png)

> [!NOTE]
> Az AD Connect megkísérli ellenőrizni a tartományt a konfigurálási szakasz során. Ha anélkül folytatja a konfigurálást, hogy hozzáadná a szükséges DNS-bejegyzéseket, a varázsló nem tudja majd befejezni a konfigurálást.
>
>

## <a name="configuring-federation-with-pingfederate"></a>PingFederate-összevonás konfigurálása
A PingFederate konfigurálása az Azure AD Connecttel egyszerű feladat, és mindössze néhány kattintást igényel. Azonban a következő előfeltételek megléte szükséges.
- PingFederate 8.4 vagy újabb verzió.  További információk: [PingFederate-integráció az Azure Active Directory és az Office 365 szolgáltatásokkal](https://docs.pingidentity.com/bundle/O365IG20_sm_integrationGuide/page/O365IG_c_integrationGuide.html).
- Egy SSL-tanúsítványra az összevonási szolgáltatás használni kívánt nevéhez (például sts.contoso.com)

### <a name="verify-the-domain"></a>A tartomány hitelesítése
Az Összevonás a PingFederate-tel lehetőség kiválasztása után a rendszer megkéri, hogy erősítse meg az összevonni kívánt tartományt.  Válassza ki a tartományt a legördülő listában.

![Tartomány ellenőrzése](./media/how-to-connect-install-custom/ping1.png)

### <a name="export-the-pingfederate-settings"></a>A PingFederate-beállítások exportálása


A PingFederate-et kell beállítani összevonási kiszolgálóként mindegyik összevont Azure-tartományon.  Kattintson a Beállítások exportálása gombra, és ossza meg ezeket az információkat a PingFederate rendszergazdájával.  Az összevonási kiszolgáló rendszergazdája frissíti a konfigurációt, majd megadja a PingFederate-kiszolgáló URL-címét és portszámát, hogy az Azure AD Connect ellenőrizhesse a metaadat-beállításokat.  

![Tartomány ellenőrzése](./media/how-to-connect-install-custom/ping2.png)

Az érvényesítéssel kapcsolatos problémák megoldásához forduljon a PingFederate-rendszergazdához.  Az alábbiakban egy példa látható egy PingFederate-kiszolgálóra, amely nem rendelkezik érvényes megbízhatósági kapcsolattal az Azure-ral:

![Bizalmi kapcsolat](./media/how-to-connect-install-custom/ping5.png)




### <a name="verify-federation-connectivity"></a>Az összevonási kapcsolat ellenőrzése
Az Azure AD Connect megkísérli érvényesíteni az előző lépésben a PingFederate-metaadatokból lehívott hitelesítési végpontokat.  Az Azure AD Connect először a helyi DNS-kiszolgálók használatával kísérli meg feloldani a végpontokat.  Ezután egy külső DNS-szolgáltató használatával kísérli meg feloldani azokat.  Az érvényesítéssel kapcsolatos problémák megoldásához forduljon a PingFederate-rendszergazdához.  

![A kapcsolat ellenőrzése](./media/how-to-connect-install-custom/ping3.png)

### <a name="verify-federation-login"></a>Az összevonási bejelentkezés ellenőrzése
Végezetül az újonnan konfigurált összevont bejelentkezési folyamat ellenőrzéséhez bejelentkezhet az összevont tartományba. Ha ez sikeres, a PingFederate-összevonás sikeresen konfigurálva lett.
![Bejelentkezés ellenőrzése](./media/how-to-connect-install-custom/ping4.png)

## <a name="configure-and-verify-pages"></a>Configure (Konfigurálás) és Verify (Ellenőrzés) lap
A konfigurálás ezen az oldalon történik.

> [!NOTE]
> Mielőtt folytatná a telepítést, ha konfigurálta az összevonást, ellenőrizze, hogy konfigurálta az [összevonási kiszolgálók névfeloldását is](how-to-connect-install-prerequisites.md#name-resolution-for-federation-servers).
>
>


![Ready to configure (Konfigurálásra kész)](./media/how-to-connect-install-custom/readytoconfigure2.png)

### <a name="staging-mode"></a>Átmeneti mód
Az átmeneti móddal párhuzamosan be lehet állítani egy új szinkronizálási kiszolgálót is. A rendszer egy felhőbeli címtárra csak egyetlen szinkronizálási kiszolgálóról támogatja az exportálást. Ha azonban egy másik kiszolgálóról kíván exportálni, például egy olyanról, amelyen DirSync fut, engedélyezheti az Azure AD Connectet átmeneti módban. Ha engedélyezve van, a szinkronizálási kiszolgáló a szokásos módon importálja és szinkronizálja az adatokat, azonban nem exportál semmit az Azure AD vagy AD szolgáltatásokba. A jelszó-szinkronizálás és jelszóvisszaíró szolgáltatások átmeneti módban le vannak tiltva.

![Átmeneti mód](./media/how-to-connect-install-custom/stagingmode.png)

Átmeneti módban elvégezheti a szükséges módosításokat a szinkronizálási motoron, és áttekintheti az exportálásra váró adatokat. Ha a konfiguráció megfelelőnek tűnik, futtassa le újra a telepítővarázslót, és tiltsa le az átmeneti módot. Az adatok mostantól exportálva lesznek az Azure AD szolgáltatásba erről a kiszolgálóról. Mindenképpen tiltsa le ezzel egy időben a másik kiszolgálót, hogy egyszerre csak egy kiszolgáló exportáljon aktívan.

További információkért lásd: [Átmeneti mód](how-to-connect-sync-operations.md#staging-mode).

### <a name="verify-your-federation-configuration"></a>Összevonási konfiguráció ellenőrzése
Amikor a Verify (Ellenőrzés) gombra kattint, az Azure AD Connect ellenőrzi a DNS-beállításokat.

**Intranetkapcsolat ellenőrzése**

* Összevonási FQDN feloldása: Az Azure AD Connect ellenőrzi, hogy az összevonási FQDN feloldható-e DNS-sel a kapcsolat létrehozásához. Ha az Azure AD Connect nem tudja feloldani az FQDN-t, az ellenőrzés sikertelen lesz. Az ellenőrzés sikeres befejezéséhez biztosítsa egy DNS-rekord meglétét az FQDN összevonási szolgáltatás számára.
* DNS A-rekord: Az Azure AD Connect ellenőrzi, hogy az összevonási szolgáltatáshoz létezik-e egy A-rekord. Az A-rekord hiányában az ellenőrzés sikertelen lesz. Az ellenőrzés sikeres befejezéséhez hozzon létre az összevonási FQDN számára egy A-rekordot (és ne egy CNAME-rekordot).

**Extranet kapcsolat ellenőrzése**

* Összevonási FQDN feloldása: Az Azure AD Connect ellenőrzi, hogy az összevonási FQDN feloldható-e DNS-sel a kapcsolat létrehozásához.

![Befejezve](./media/how-to-connect-install-custom/completed.png)

![Ellenőrzés](./media/how-to-connect-install-custom/adfs7.png)

A teljes körű hitelesítés sikerességének ellenőrzéséhez manuálisan kell végrehajtania egyet vagy többet a következő tesztek közül:

* Miután a szinkronizálás véget ért, az Azure AD Connect Összevont bejelentkezés ellenőrzése további feladatának használatával ellenőrizze egy választott helyszíni felhasználói fiók hitelesítését.
* Ellenőrizze, hogy be tud-e jelentkezni böngészőből egy, a tartományhoz csatlakozó gépről az intraneten: csatlakozzon a https://myapps.microsoft.com helyhez, és ellenőrizze a bejelentkezést a fiókkal, amelybe be van jelentkezve. A beépített AD DS rendszergazdai fiók nincs szinkronizálva, és nem használható az ellenőrzéshez.
* Ellenőrizze, hogy be tud jelentkezni egy, az extraneten lévő eszközről. Egy otthoni gépről vagy mobileszközről csatlakozzon a https://myapps.microsoft.com helyre, és adja meg hitelesítő adatait.
* Ellenőrizze a gazdag ügyféllel való bejelentkezést. Csatlakozzon a https://testconnectivity.microsoft.com helyhez, lépjen az **Office 365** lapra, és válassza az **Office 365 Single Sign-On Test** (Office 365 egyszeri bejelentkezés tesztelése) lehetőséget.

## <a name="troubleshooting"></a>Hibaelhárítás
A következő szakaszban az Azure AD Connect telepítése során felmerülő problémákkal kapcsolatos hibaelhárítási és egyéb információkat talál.

### <a name="the-adsync-database-already-contains-data-and-cannot-be-overwritten"></a>„Az ADSync adatbázis már tartalmaz adatokat, és nem írható felül” 
Az Azure AD Connect egyéni telepítése esetén, ha **A szükséges összetevők telepítése** lapon bejelöli a **Meglévő SQL Server használata** jelölőnégyzetet, előfordulhat, hogy a következő hibaüzenetet kapja: **Az ADSync adatbázis már tartalmaz adatokat, és nem írható felül. Távolítsa el a meglévő adatbázist, és próbálkozzon újra.**

![Hiba](./media/how-to-connect-install-custom/error1.png)

A hiba oka, hogy már létezik egy adatbázis **ADSync** néven az SQL Server SQL-példányán, amelyet a fenti szövegmezőkben megadott.

Ez általában akkor fordul elő, ha eltávolította az Azure AD Connectet.  Az eltávolításkor az adatbázis nem törlődik az SQL Serverről.

A probléma megoldásához először ellenőrizze, hogy az Azure AD Connect által annak eltávolítása előtt használt **ADSync** adatbázis nincs-e továbbra is használatban.

Ezután javasolt az adatbázisról a törlése előtt biztonsági másolatot készíteni. 

Végül törölje az adatbázist.  Ehhez használja a **Microsoft SQL Server Management Studiót,** és csatlakozzon az SQL-példányhoz. Keresse meg az **ADSync** adatbázist, kattintson rá a jobb gombbal, és válassza a **Törlés** lehetőséget a helyi menüből.  Ezután kattintson az **OK** gombra a törléséhez.

![Hiba](./media/how-to-connect-install-custom/error2.png)

Miután törölte az **ADSync** adatbázist, a **Telepítés** gombra kattintva újból megkísérelheti a telepítést.

## <a name="next-steps"></a>További lépések
Miután a telepítés befejeződött, jelentkezzen ki, majd ismét jelentkezzen be a Windowsba, mielőtt a Synchronization Service Managert (Szinkronizálási szolgáltatás kezelőjét) vagy a Synchronization Rule Editort (Szinkronizálási szabályok szerkesztőjét) használná.

Miután az Azure AD Connect telepítése megtörtént, [ellenőrizheti a telepítést, és hozzárendelheti a licenceket](how-to-connect-post-installation.md).

Ismerkedjen meg a következő, a telepítéssel engedélyezett szolgáltatásokkal: a [Véletlen törlések megakadályozása](how-to-connect-sync-feature-prevent-accidental-deletes.md) és az [Azure AD Connect Health](how-to-connect-health-sync.md).

Ismerje meg részletesebben a következő általános témaköröket: [az ütemező és a szinkronizálási események indítása](how-to-connect-sync-feature-scheduler.md).

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
