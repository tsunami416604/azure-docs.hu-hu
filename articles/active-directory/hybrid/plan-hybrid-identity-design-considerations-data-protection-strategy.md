---
title: Hibrid identitás tervezési – data protection stratégia Azure |} A Microsoft Docs
description: Meghatározhatja a data protection stratégiát a hibrid identitáskezelési megoldás, amely meghatározott üzleti igényeknek.
documentationcenter: ''
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: e76fd1f4-340a-492a-84d9-e05f3b7cc396
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2017
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 7999dc15fbe1af9d23b47971db03cf6b197f110f
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46312108"
---
# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>Adja meg a hibrid identitáskezelési megoldás az adatvédelmi stratégiát?
Ebben a feladatban az adatvédelmi stratégiát a hibrid identitáskezelési megoldás az üzleti szükségletek kielégítése céljából, amelyet a megadott fogja definiálni:

* [Az adatvédelmi követelmények meghatározása](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)
* [A Tartalomkezelés követelmények meghatározása](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)
* [Hozzáférés-vezérlési követelményeinek meghatározása](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)
* [Incidensválasz-követelmények meghatározása](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>Adatok védelmi beállítások meghatározása
A [határozza meg a címtár-szinkronizálás követelményei](plan-hybrid-identity-design-considerations-directory-sync-requirements.md), Microsoft Azure ad-ben is szinkronizálni tudja a helyszíni Active Directory Domain Services (AD DS). Ez az integráció lehetővé teszi, hogy a szervezetek használata az Azure AD-felhasználói hitelesítő adatok ellenőrzése a vállalati erőforrások eléréséhez közben. Ehhez mindkét forgatókönyvet támogatja: adatok helyszíni rest és a felhőben. Az Azure AD-adatokhoz való hozzáférést a biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) keresztül felhasználóhitelesítést igényel.

A hitelesítést követően a hitelesítési jogkivonat olvasható az egyszerű felhasználónév (UPN). Ezt követően az engedélyezési rendszer meghatározza, hogy a replikált partíció és a felhasználói tartomány megfelelő tároló. A felhasználó létezik-e, engedélyezett állapotát, és információkat szerepkör majd segít a engedélyezési rendszernek e bérlővel való hozzáférés engedélyezve van a felhasználó a munkamenetben. Bizonyos engedélyezett műveleteket (pontosabban létrehozása felhasználójának és jelszavának alaphelyzetbe állítása), amely a bérlői rendszergazda ezután használatával kezelheti a megfelelőség erőfeszítések vagy vizsgálatok auditnapló létrehozása.

A helyszíni adatközpont Azure Storage-bA internetkapcsolatra keresztül helyez át adatokat nem mindig lehet megvalósítható adatok mennyiségét, a sávszélesség rendelkezésre állási vagy egyéb szempontok miatt. A [Azure Storage Import/Export szolgáltatás](../../storage/common/storage-import-export-service.md) adatokat egy blob storage nagy mennyiségű elhelyezése és beolvasása a hardveres lehetőséget kínál. Lehetővé teszi, hogy küldjön [BitLocker-titkosítású](https://technet.microsoft.com/library/dn306081#BKMK_BL2012R2) merevlemez-meghajtókon közvetlenül az Azure-adatközpont, ahol az felhő üzemeltetői töltse fel a tartalmát a storage-fiók vagy a lemezmeghajtók való visszatéréshez, letölthetik az Azure-beli adatait. Csak titkosított lemezek fogadja a folyamat (a saját maga a szolgáltatás a feladat beállítása során létrehozott egy BitLocker-kulcs használatával). A BitLocker-kulcsot biztosítunk az Azure-bA külön-külön, ezzel biztosítva a kulcs megosztása sávon kívül.

Az átvitt adatokat különböző helyzetekben sor kerülhet, mivel az is fontos tudni, hogy használja-e a Microsoft Azure [virtuális hálózatok](https://azure.microsoft.com/documentation/services/virtual-network/) bérlők érkező forgalmat egy másik, például a gazdagép és vendég szintű mértékek alkalmazó elkülönítése tűzfalak, IP hálózaticsomag-szűrés, blokkolja a port és koncové body protokolu HTTPS. Azonban az Azure belső kommunikációs, beleértve az infrastruktúra-infrastruktúra és az infrastruktúra-ügyfél (helyszíni), a legtöbb is titkosítva van. Egy másik fontos forgatókönyv az Azure-adatközpontok; belüli kommunikáció Ahhoz, hogy biztosítsa, hogy egyetlen virtuális gép megszemélyesíteni, vagy kihallgassa egy másik IP-címet a hálózatokat a Microsoft felügyeli. TLS/SSL szolgál az Azure Storage vagy az SQL-adatbázis elérésekor, vagy a Cloud serviceshez való kapcsolódáskor. Ebben az esetben az ügyfél felelős rendszergazda egy TLS/SSL-tanúsítvány beszerzése és a bérlői infrastruktúrára való telepítése. Adatok forgalom áthelyezése bérlők számára a Microsoft Azure Virtual Network használatával egyetlen központi telepítés vagy az egyazon telepítésben a virtuális gépek közötti titkosított kommunikációs protokollok, mint például a HTTPS, SSL/TLS vagy mások keresztül védhetők.

Attól függően, hogy hogyan válaszolt kérdéseit [az adatvédelmi követelmények meghatározása](plan-hybrid-identity-design-considerations-dataprotection-requirements.md), érdemes, hogy meg tudja határozni, hogyan szeretné megvédeni adatait, és hogyan a hibrid identitáskezelési megoldás is segítséget nyújtanak a folyamat. Az alábbi táblázat az Azure által támogatott rendelkezésre álló lehetőségek minden egyes adatok védelme a forgatókönyvhöz.

| Data protection beállításai | Inaktív állapotban a felhőben | A helyszíni rest | Az átvitel során |
| --- | --- | --- | --- |
| BitLocker meghajtótitkosítás |X |X | |
| SQL Server-adatbázisok titkosítása |X |X | |
| Virtuális gép virtuális gép titkosítása | | |X |
| SSL/TLS | | |X |
| VPN | | |X |

> [!NOTE]
> Olvasási [megfelelőség funkció szerint](https://azure.microsoft.com/support/trust-center/services/) , [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) többet megtudni a tanúsítványok, amely megfelel az egyes Azure-szolgáltatások.
> Az adatvédelmi beállításokat a többrétegű megközelítést használni, mivel ezek a lehetőségek összehasonlítása nem vonatkoznak erre a célra. Győződjön meg arról, hogy minden egyes az adatok állapotát rendelkezésre álló lehetőségek feljebb helyezése.
>
>

## <a name="define-content-management-options"></a>A Tartalomkezelés beállítások definiálása

<a name="one-advantage-of-using-azure-ad-to-manage-a-hybrid-identity-infrastructure-is-that-the-process-is-fully-transparent-from-the-end-users-perspective-the-user-tries-to-access-a-shared-resource-the-resource-requires-authentication-the-user-has-to-send-an-authentication-request-to-azure-ad-in-order-to-obtain-the-token-and-access-the-resource-this-entire-process-happens-in-the-background-without-user-interaction"></a>Hibrid identitás-infrastruktúra kezelése az Azure AD használatának egyik előnye az, hogy teljes mértékben átlátható a felhasználó szemszögéből-e a folyamat. A felhasználó megpróbál hozzáférni egy megosztott erőforráson, az erőforrás-hitelesítés szükséges, a felhasználó rendelkezik a hitelesítési kérést küldhet az Azure ad-ben a jogkivonat beszerzése és az erőforrás eléréséhez. A teljes folyamatot a háttérben, felhasználói beavatkozás nélkül történik. 
=======
Hibrid identitás-infrastruktúra kezelése az Azure AD használatának egyik előnye az, hogy teljes mértékben átlátható a felhasználó szemszögéből-e a folyamat. A felhasználó megpróbál hozzáférni egy megosztott erőforráson, az erőforrás-hitelesítés szükséges, a felhasználó rendelkezik a hitelesítési kérést küldhet az Azure ad-ben a jogkivonat beszerzése és az erőforrás eléréséhez. A teljes folyamatot a háttérben, felhasználói beavatkozás nélkül történik. 

Szervezetek számára, amelyek az adatok adatvédelmi aggályokat rendszerint adatbesorolás csatornáinknak szükséges. Ha a jelenlegi helyszíni infrastruktúrával már használja az adatbesorolás, Azure ad-ben használható a fő tárházban, a felhasználó identitását. Egy ismert eszköz, azaz használható a helyszínen az adatbesorolás neve [adatbesorolási eszközkészlet](https://msdn.microsoft.com/library/Hh204743.aspx) Windows Server 2012 R2. Ez az eszköz segítségével azonosításában, besorolásában és fájlkiszolgálók a privát felhőben tárolt adatok védelmét. Akkor is lehet használni a [automatikus Fájlbesorolás](https://technet.microsoft.com/library/hh831672.aspx) ennek a feladatnak a Windows Server 2012-ben.

Ha a szervezet nem rendelkezik az adatok besorolását helyen, de bizalmas fájlok védelmére új helyszíni hozzáadása nélkül kell, használhatják a Microsoft [Azure Rights Management szolgáltatás](https://technet.microsoft.com/library/JJ585026.aspx).  Az Azure RMS titkosítási, identitáskezelési és engedélyezési házirendeket használ a fájlok és e-mailek védelmének elősegítésére, és több eszközön működik – például telefonokon, táblagépeken és számítógépeken. Mivel az Azure RMS egy felhőszolgáltatás, nincs nincs szükség konkrét bizalmi viszonyok konfigurálására más szervezetekkel őket a védett tartalom megosztása előtt. Ha már van Office 365- vagy Azure AD-címtárral, szervezetek közötti együttműködés automatikusan támogatott. Csak a directory-attribútumok, amelyek az Azure RMS-t kell egy közös identitásának támogatásához a helyszíni Active Directory-fiókok esetében az Azure Active Directory szinkronizálási szolgáltatások (AAD Sync) vagy az Azure AD Connect használatával is szinkronizálhatók.

A Tartalomkezelés alapvető része, hogy ismerje meg, melyik erőforrást hozzáférő, ezért egy részletes naplózási képesség fontos az identitás-kezelési megoldásra vonatkozó. Az Azure AD a több mint 30 napban, beleértve a napló biztosít:

* Szerepkör-tagság változása (például: a globális rendszergazda szerepkörhöz hozzáadott felhasználó)
* Hitelesítőadat-frissítések (például: jelszó-módosítások)
* Tartományok kezelése (Példa: tartomány eltávolítása egyéni tartomány ellenőrzése)
* Hozzáadásával vagy eltávolításával alkalmazások
* Felhasználók kezelése (például: hozzáadás, eltávolítás, a felhasználó frissítése)
* Hozzáadásával vagy eltávolításával licencek

> [!NOTE]
> Olvasási [Microsoft Azure biztonsági és rendszernaplók kezelése az](http://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) többet megtudni az Azure-ban naplózási képességeket.
> Attól függően, hogy hogyan válaszolt kérdéseit [tartalomkezelési követelmények meghatározása](plan-hybrid-identity-design-considerations-contentmgt-requirements.md), érdemes, hogy meg tudja határozni, hogyan szeretné kezelni a hibrid identitáskezelési megoldás a tartalmat. Minden beállítás érhető el a 6. táblázat integrálható az Azure ad-vel, amelyek fontos határozza meg, amely az Ön üzleti igényeinek jobban megfelelő.
>
>

| Tartalomkezelési lehetőségeket | Előnyök | Hátrányai |
| --- | --- | --- |
| Központosított a helyszínen (Active Directory tartalomvédelmi kiszolgáló) |Teljes körűen felügyelve az a feladata az adatok osztályozásához kiszolgálói infrastruktúrát. <br> A beépített funkció a Windows Server, nincs szükség további licencek vagy előfizetések <br> Integrálhatók az Azure AD-t egy hibrid forgatókönyvben <br> Information rights tartalomvédelmi (IRM) funkciókat támogatja a Microsoft Online szolgáltatásaiban, például az Exchange online-hoz és a SharePoint online-hoz, valamint Office 365-höz <br> Támogatja a helyszíni Microsoft kiszolgálói termékeket, például az Exchange Server, SharePoint-kiszolgáló és a Windows Server és a fájlbesorolási infrastruktúrát (FCI) futtató fájlkiszolgálókat. |Nagyobb karbantartási (tartsa be a frissítéseket, konfiguráció és a lehetséges), mert informatikai a kiszolgáló tulajdonosa <br> Helyszíni kiszolgálói infrastruktúrát igényel<br> Natív módon nem használja ki Azure-képességek |
| A felhőben (Azure RMS) központosított |A helyszíni megoldásokhoz képest egyszerűbb kezelése <br> Integrálható legyen az Active Directory tartományi szolgáltatások egy hibrid forgatókönyvben <br>  Teljes mértékben integráltuk az Azure ad-ben <br> Egy helyszíni kiszolgáló annak érdekében, hogy a szolgáltatás üzembe helyezése nem igényel. <br> Támogatja a helyszíni Microsoft kiszolgálói termékeket, például az Exchange Server, a SharePoint, a kiszolgáló és a Windows Server és a Fájlbesorolás vagy infrastruktúra (FCI) futtató fájlkiszolgálókat <br> Informatikai, teljes körű, a bérlői kulcs BYOK képesség is rendelkezhet. |A szervezet RMS-t támogató felhőalapú előfizetéssel kell rendelkeznie. <br> A szervezetnek rendelkeznie kell egy Azure AD-címtár támogatja az RMS felhasználóhitelesítését |
| Hibrid (Azure RMS szolgáltatással integrált, a helyszíni Active Directory tartalomvédelmi kiszolgáló) |Ebben a forgatókönyvben összegzi az előnyöket, központosított a helyszínen és a felhőben. |A szervezet RMS-t támogató felhőalapú előfizetéssel kell rendelkeznie. <br> A szervezetnek rendelkeznie kell a felhasználói hitelesítés támogatásához az RMS-hez készült Azure AD-címtár <br> Azure-felhőszolgáltatás közötti kapcsolat szükséges, a helyszíni infrastruktúra |

## <a name="define-access-control-options"></a>Hozzáférés-vezérlési lehetőségek meghatározása
A hitelesítési, engedélyezési és hozzáférés-vezérlési funkciókkal az Azure ad-ben elérhető kihasználva a vállalat egy központi identitástárház használata közben, így a felhasználók számára engedélyezheti és partnerek használata egyszeri bejelentkezéses (SSO) az alábbi ábrán látható módon:

![](./media/plan-hybrid-identity-design-considerations/centralized-management.png)

Központi felügyeletet, és teljesen más címtár-integráció

Az Azure Active Directory biztosítja az egyszeri bejelentkezés több ezer SaaS-alkalmazásokhoz, és a helyszíni webalkalmazásokat. Tekintse meg a [Azure Active Directory összevonási kompatibilitási lista: harmadik fél Identitásszolgáltatók, amelyek segítségével az egyszeri bejelentkezés megvalósítása](https://msdn.microsoft.com/library/azure/jj679342.aspx) az egyszeri bejelentkezés külső további tudnivalókat a Microsoft által tesztelt cikk. Ez a funkció lehetővé teszi, hogy a szervezet B2B-forgatókönyvekhez számos folyamatos ellenőrzése mellett az identitás- és hozzáférés-kezelés. Azonban során a B2B folyamat tervezése fontos megérteni a partner által használt hitelesítési módszert, és ellenőrizheti, hogy ez a módszer az Azure által támogatott. Az Azure AD jelenleg az alábbi módszereket támogatja:

* Security Assertion Markup Language (SAML)
* OAuth
* Kerberos
* Tokenek
* Tanúsítványok

> [!NOTE]
> olvassa el [Azure Active Directory hitelesítési protokolljait](https://msdn.microsoft.com/library/azure/dn151124.aspx) tudni, hogy minden protokoll, és annak képességeit, az Azure-ban kapcsolatos további részletekért.
>
>

A az Azure AD-támogatás, mobil üzleti alkalmazások használatával az azonos egyszerűen a Mobile Services felhasználói hitelesítés engedélyezése alkalmazott jelentkezzen be a vállalati Active Directory hitelesítő adataikkal a mobilalkalmazások számára. Ezzel a funkcióval az Azure AD Identitásszolgáltatóként, a Mobile Servicesben mellett az egyéb identitás-szolgáltatóktól már támogatott (többek között Microsoft Accounts, a Facebook-azonosítója, a Google-azonosító és a Twitter-azonosítója) használata támogatott. Ha a helyszíni alkalmazások a vállalati Active Directory tartományi Szolgáltatásokban található a felhasználó hitelesítő adatait használja, a partnerek és a felhőből érkező felhasználók hozzáférésének transzparens kell lennie. Felhasználó feltételes hozzáférés-vezérlést (felhőalapú) webalkalmazásokat, webes API, Microsoft-felhőszolgáltatásokhoz, külső SaaS-alkalmazásokhoz és natív ügyfélalkalmazások (mobil), és a biztonság, előnyeit rendelkezik, naplózás, a jelentési minden együtt Helyezze el. Azonban ajánlott a megvalósítás nem éles környezetben vagy a felhasználók egy korlátozott számú ellenőrzése.

> [!TIP]
> Fontos megemlíteni, hogy az Azure AD nem rendelkezik a csoportházirend rendelkezik Active Directory tartományi Szolgáltatásokban. Eszközök az szabályzat kényszerítéséhez kell egy mobileszköz-felügyeleti megoldás például [a Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx).
>
>

Miután a felhasználó hitelesítése az Azure AD-vel, fontos értékelheti ki a hozzáférési szintet, amelyet a felhasználó rendelkezik. A hozzáférési szintet, amelyet a felhasználó rendelkezik az adott erőforráson eltérőek lehetnek. Bár az Azure AD bizonyos erőforrásokhoz való hozzáférés szabályozása is hozzáadhat egy további biztonsági réteget, vegye figyelembe, hogy a erőforrásán is lehet a saját hozzáférés-vezérlési lista külön-külön, például a hozzáférés-vezérlés a fájlkiszolgálón található fájlokat. A következő ábra összefoglalja a szintű hozzáférés-vezérlés, amely egy hibrid forgatókönyvben is rendelkezik:

![](./media/plan-hybrid-identity-design-considerations/accesscontrol.png)

Minden interakció x. ábra bemutatta a diagram egy szerint az Azure AD hozzáférési adatvezérlési forgatókönyvek jelöli. Az alábbiakban az egyes forgatókönyvek leírását rendelkezik:

  1. Feltételes hozzáférés az alkalmazásokhoz a helyszínen üzemeltetett: a Windows Server 2012 R2 AD FS Szolgáltatásának használatához konfigurált alkalmazások hozzáférési házirendekkel regisztrált eszközökön is használhatja.

  2. Hozzáférés-vezérlés az Azure Portal: az Azure emellett lehetővé teszi a portálon való hozzáférés vezérlése szerepköralapú hozzáférés-vezérlés (RBAC) használatával). Ez a módszer lehetővé teszi, hogy a vállalat, amely az Azure Portalon teheti meg egy egyéni műveletek számának korlátozására. Az RBAC használatával férhet hozzá a portálon, a rendszergazdák a következő hozzáférés-felügyeleti módszerek használatával is adhat a hozzáférést:

   - Csoport alapú szerepkör-hozzárendelés: rendelhet hozzáférés az Azure AD-csoportokat, ingyeneseket szinkronizálja a helyi Active Directoryból. Ez lehetővé teszi a meglévő befektetések által a szervezet tett eszközök és csoportok kezelése a folyamatok. Prémium szintű Azure AD a delegált csoport felügyeleti funkciót is használhatja.
   - Beépített szerepkörök használata az Azure-ban: három szerepkört is használhatja – tulajdonos, közreműködő és olvasó, győződjön meg arról, hogy felhasználók és csoportok engedélye csak a feladataik elvégzéséhez szükséges feladatokat végezheti el.
   -  Erőforrásokhoz való hozzáférés részletes: szerepköröket rendelhet felhasználókat és csoportokat egy adott előfizetés, erőforráscsoport vagy például egy webhely vagy az adatbázis egy önálló Azure-erőforrás. Így biztosítható, hogy a felhasználók rendelkeznek minden az erőforrásokhoz való hozzáférés, és nem kell kezelniük, erőforrásokhoz sem fér hozzá.

   > [!NOTE]
   > Ha alkalmazásokat fejleszt, és testre szeretne szabni a hozzáférés-vezérlést a számukra, akkor is az Azure AD alkalmazás-szerepkörök használata a hitelesítéshez. Tekintse át ezt [WebApp-RoleClaims-DotNet-példa](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) hogyan hozhat létre az alkalmazást, ez a funkció használatához.


  3. Feltételes hozzáférés az Office 365-alkalmazások a Microsoft Intune-nal: IT-rendszergazdák feltételes hozzáférési eszközházirendek a vállalati erőforrások biztonságossá tétele, ezzel lehetővé téve az információkkal dolgozó szakemberek a megfelelő eszközök hozzáférését a szolgáltatások egy időben építhető ki. 
  
  4. Feltételes hozzáférés a Saas-alkalmazások: [Ez a funkció](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work/) lehetővé teszi, hogy konfigurálja a multi-factor authentication alkalmazás hozzáférési szabályok és a nem megbízható hálózaton lévő felhasználók hozzáférésének blokkolása. A multi-factor authentication szolgáltatás szabályokat alkalmazhat az alkalmazáshoz, vagy csak a megadott biztonsági csoportokban lévő felhasználók rendelt valamennyi felhasználó. Felhasználók kizárhatók a multi-factor authentication követelményeinek, ha az alkalmazás IP-címek, hogy a szervezeten belüli hálózati érnek el.

A hozzáférés-vezérlés beállításai a többrétegű megközelítést használni, mivel ezek a lehetőségek összehasonlítása nem vonatkoznak erre a célra. Győződjön meg arról, hogy vannak-e támaszkodva minden egyes forgatókönyv, amely megköveteli, hogy az erőforrásokhoz való hozzáférésének rendelkezésre álló lehetőségek.

## <a name="define-incident-response-options"></a>Az incidensmegoldás beállítások definiálása
Segítheti az Azure ad-ben informatikai biztonsági kockázatok identitás felhasználó tevékenységét-figyelési szolgáltatás által a környezetben. Informatikai használhatja az Azure AD hozzáférési és használati jelentések értékes információkhoz juthat az integritásra és a munkahely címtárában biztonságát. Ezekkel az információkkal rendszergazda megadhatja jobban, hogy ahol lehetséges, hogy egészülnek biztonsági kockázatokat, úgy, hogy azok megfelelően tervezi, hogy ezek a kockázatok csökkentése.  [Az Azure AD Premium előfizetéssel](../fundamentals/active-directory-get-started-premium.md) tartozik egy biztonsági jelentések, ami lehetővé teszi, hogy ezeket az adatokat. [Az Azure AD-jelentések](../reports-monitoring/overview-reports.md) kategóriái a következők:

* **Anomáliadetektálási jelentések**: bejelentkezési eseményeket, észlelt a rendellenes kell tartalmaznia. A cél, hogy győződjön meg arról, hogy tisztában az ilyen tevékenység, és lehetővé teszi győződjön meg arról, hogy az esemény gyanúsnak meghatározása.
* **Integrált alkalmazás jelentés**: hogyan használja a szervezet a felhőalapú alkalmazások betekintést nyújt. Az Azure Active Directory több ezer felhőalapú alkalmazások integrációt kínál.
* **Hibajelentések**: a külső alkalmazások fiókok kiépítése során esetlegesen előforduló hibákat jelölnek.
* **Felhasználó-specifikus jelentések**: eszköz/bejelentkezési tevékenység egy adott felhasználó adatainak megjelenítése.
* **A Tevékenységnaplók**: az elmúlt 24 órában, legutóbbi 7 nap, vagy elmúlt 30 napban, valamint a tevékenység módosításainak és jelszó alaphelyzetbe állítása és regisztrációs tevékenység belül az összes naplózott esemény rekordot tartalmaz.

> [!TIP]
> Egy másik jelentést, amely segíthet az Incidensmegoldási csapat dolgozik egy esetet is a [kiszivárgott hitelesítő adatokkal rendelkező felhasználó](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials/) jelentést. Ez a jelentés egyezéseket feltárásával a kiszivárgott hitelesítő adatok listája és a bérlő között.
>


Egyéb fontos beépített jelentéseket, hogy egy incidensre adott válasz vizsgálatának során is használható, és az Azure AD-ben:

* **Jelszó-visszaállítási tevékenység**: Adja meg a rendszergazdai jelszó-visszaállítás a szervezet használja aktívan hogyan ügyei elemzéseit.
* **Jelszó-visszaállítási regisztrációs tevékenység**: nyújt, amelybe a felhasználó regisztrálta a módszereiket, a jelszó-visszaállításhoz, és milyen módszerek van kiválasztva.
* **Tevékenységi csoport**: előzményeit biztosít a csoporthoz (például: felhasználók hozzáadását és eltávolítását), amely a hozzáférési panelen kezdeményezték.

Az alapvető jelentéskészítési képességet, amelyet használhat egy Incidensmegoldási vizsgálati folyamat során a informatikai részleg is az Azure AD Premium mellett előnyeit információkhoz juthat az Auditnaplókat:

* (Például a globális rendszergazda szerepkörhöz hozzáadott felhasználó) szerepkör-tagság változása
* Hitelesítő adatok frissítések (például a jelszó módosítására)
* Tartományok kezelése (például egy egyéni tartományt, a tartomány eltávolításakor ellenőrzése)
* Hozzáadásával vagy eltávolításával alkalmazások
* Felhasználók kezelése (például hozzáadása, eltávolítása, a felhasználó frissítése)
* Hozzáadásával vagy eltávolításával licencek

A beállítások használata incidensek megoldásához egy többrétegű megközelítést használ, mivel ezek a lehetőségek összehasonlítása nem alkalmazható erre a célra. Győződjön meg arról, hogy vannak-e támaszkodva minden egyes forgatókönyv, amely előírja, hogy az Azure AD jelentéskészítési képességet a vállalati incidensek megoldási folyamatának részeként rendelkezésre álló lehetőségek.

## <a name="next-steps"></a>További lépések
[Hibrid identitáskezelési feladatokat meghatározása](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

## <a name="see-also"></a>Lásd még:
[Kialakítási szempontok áttekintése](plan-hybrid-identity-design-considerations-overview.md)
