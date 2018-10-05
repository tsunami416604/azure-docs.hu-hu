---
title: Az Active Directory csatlakoztatása az Azure Active Directoryhoz | Microsoft Docs
description: Az Azure AD Connect integrálja a helyszíni címtárakat az Azure Active Directoryval. Így közös identitást biztosíthat az Azure AD-vel integrált Office 365-, Azure- és SaaS-alkalmazásokhoz.
keywords: az Azure AD Connect bemutatása, az Azure AD Connect áttekintése, mi az Azure AD Connect, az Active Directory telepítése
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/18/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 40ac3ca92c65607df056b883608dde60d816143e
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47181777"
---
# <a name="hybrid-identity-and-microsofts-identity-solutions"></a>Hibrid identitáskezelési és Microsoft identitáskezelési megoldások
Napjainkban a vállalkozások és a vállalatok egyre inkább vegyesen használnak helyszíni és felhőalapú alkalmazásokat.  Így megjelentek olyan alkalmazások és felhasználók, akiknek a helyszíni és a felhőalapú alkalmazásokhoz is hozzáférés szükséges, és ez jelentős kihívás.

A Microsoft identitáskezelési megoldásai kiterjednek a helyszíni és felhőalapú képességekre is, így egyetlen felhasználói identitás jön létre hitelesítés és engedélyezés céljából az összes erőforráshoz, helytől függetlenül. Ezt hibrid identitásnak nevezzük.

## <a name="what-is-azure-ad-connect"></a>Mi az Azure AD Connect?

A Microsoft Azure AD Connect eszköze segítségével teljesítheti a hibrid identitáskezelési célokat.  Így közös identitást biztosíthat a felhasználóinak az Azure AD-vel integrált Office 365-, Azure- és SaaS-alkalmazásokhoz.  Ez a tevékenység a következő jellemzőkkel bír:
    
- [Szinkronizálás](how-to-connect-sync-whatis.md) – ez az összetevő a felhasználók, csoportok és egyéb objektumok létrehozásáért felelős. Segítségével arról is meggyőződhet, hogy a helyszíni felhasználókhoz és csoportokhoz tartozó identitásadatok megegyeznek a felhőben található hasonló adatokkal.  Emellett a jelszókivonatok Azure AD-vel történő szinkronizálását is elvégzi.
-   [AD FS és összevonás-integráció](how-to-connect-fed-whatis.md) – az összevonás az Azure AD Connect nem kötelező összetevője, amelynek segítségével helyszíni AD FS-infrastruktúrát használó hibrid környezetet konfigurálhat. AD FS-kezelési képességeket is biztosít, például tanúsítványmegújítást és további AD FS-kiszolgálók üzembe helyezését.
-   [Átmenő hitelesítés](how-to-connect-pta.md) – Egy másik nem kötelező összetevő, amely lehetővé teszi a felhasználóknak, hogy ugyanazt a jelszót használják a helyszínen és a felhőben, de nem igényli az összevont környezet infrastruktúráját.
-   [Állapotfigyelés](whatis-hybrid-identity-health.md) – Az Azure AD Connect Health hatékony monitorozási képességgel rendelkezik, valamint egy központi helyet biztosít az Azure Portalon az ilyen tevékenységek megtekintéséhez. 


![Mi az az Azure AD Connect?](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>Mi az Azure AD Connect Health?

Az Azure Active Directory (Azure AD) Connect Health segít megfigyelni a helyszíni identitás-infrastruktúrát és a szinkronizálási szolgáltatásokat, és további betekintést nyújt a működésükbe. Megbízható Office 365- és Microsoft Online Services-kapcsolat fenntartását teszi lehetővé olyan fontos identitás-összetevők megfigyelésével, mint például az Active Directory összevonási szolgáltatások (AD FS) kiszolgálói, az Azure AD Connect-kiszolgálók (más néven a szinkronizálási motor) vagy az Active Directory-tartományvezérlők stb. Ezen összetevők legfontosabb adatpontjait is könnyen hozzáférhetővé teszi, így lekérdezhetővé válnak a használatra és egyéb lényeges jellemzőkre vonatkozó adatok, hogy megalapozott döntéseket hozhasson.

Ez az információ az [Azure AD Connect Health portálon](https://aka.ms/aadconnecthealth) található meg. Az Azure AD Connect Health portálon riasztásokat, teljesítményfigyelési adatokat, használatelemzési információkat és egyéb információkat tekinthet meg. Az Azure AD Connect Health egyetlen helyre gyűjti a legfontosabb identitás-összetevők állapotadatait.

![Mi az az Azure AD Connect Health?](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)


Az Azure AD Connect Health funkcióinak bővülésével a portál egy identitásalapú irányítópultot kínál. Ezáltal egy nagy teljesítményű, kifogástalan állapotú és integrált környezetet biztosít, ahol a felhasználók még hatékonyabban dolgozhatnak.


## <a name="why-use-azure-ad-connect"></a>Miért érdemes az Azure AD Connect megoldást használni?
A helyszíni címtárak és az Azure AD integrálása révén a felhasználók munkája hatékonyabbá válik, mivel a felhőalapú és a helyszíni erőforrások hozzáféréséhez közös identitás áll a rendelkezésükre. A felhasználók és a szervezetek az alábbi előnyöket élvezhetik:

* A felhasználók egyetlen identitással férhetnek hozzá olyan helyszíni alkalmazásokhoz és felhőszolgáltatásokhoz, mint például az Office 365.
* Mivel egyetlen eszközről van szó, a szinkronizáláshoz és bejelentkezéshez használt rendszer üzembe helyezése egyszerűen végrehajtható.
* Többféle alkalmazási helyzethez is biztosítja az elérhető legújabb képességeket. Az Azure AD Connect olyan identitásintegrációs eszközök régebbi verzióit váltja fel, mint például a DirSync és az Azure AD Sync. További információk: [Hybrid Identity directory integration tools comparison](plan-hybrid-identity-design-considerations-tools-comparison.md) (Hibrid identitás: a címtár-integrációs eszközök összehasonlítása).

## <a name="why-use-azure-ad-connect-health"></a>Miért érdemes az Azure AD Connect Health eszközt használni?
A helyszíni címtárak és az Azure AD integrálása révén a felhasználók munkája hatékonyabbá válik, mivel a felhőalapú és a helyszíni erőforrások eléréséhez közös identitás áll a rendelkezésükre. Ez az integráció azonban szükségessé teszi a környezet megfelelő állapotának biztosítását, hogy a felhasználók bármilyen eszközről megbízhatóan hozzáférhessenek a helyszíni és a felhőalapú erőforrásokhoz is. Az Azure AD Connect Health segít megfigyelni az Office 365 és más Azure AD-alkalmazások eléréséhez használt helyszíni identitás-infrastruktúrát, és további betekintést nyújt annak működésébe. Használata éppolyan egyszerű, mintha egy-egy ügynököt telepítene a helyszíni identitás-kiszolgálókra.

### <a name="azure-ad-connect-health-for-ad-fshow-to-connect-health-adfsmd"></a>[Azure AD Connect Health for AD FS](how-to-connect-health-adfs.md)
Az AD FS-hez készült Azure AD Connect Health a Windows Server 2008 R2, a Windows Server 2012, a Windows Server 2012 R2 és a Windows Server 2016 rendszeren támogatja az AD FS 2.0-s verzióját. Ezen kívül támogatja az AD FS proxy- vagy webalkalmazás-proxykiszolgálók figyelését is, amelyek az extranetes hozzáféréshez biztosítanak hitelesítési támogatást. Az állapotügynök egyszerűen és gyorsan végrehajtható telepítésével elérhetővé válnak az Azure AD Connect Health for AD FS legfontosabb képességei.

#### <a name="key-benefits-and-best-practices"></a>Főbb előnyök és ajánlott eljárások

- *Fokozott biztonság*
  - [Extranet zárolási tendenciák](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)
  - [Jelentések a sikertelen bejelentkezésekről](how-to-connect-health-adfs.md#risky-ip-report-public-preview) 
  - [Adatvédelmi megfelelőség](reference-connect-health-user-privacy.md)    
- *Riasztás az összes [kritikus ADFS-rendszerhibáról](how-to-connect-health-alert-catalog.md#alerts-for-active-directory-federation-services)*
    - Kiszolgálók konfigurálása és rendelkezésre állása 
    - [Teljesítmény és csatlakozási lehetőségek](how-to-connect-health-adfs.md#performance-monitoring-for-ad-fs) 
  - Rendszeres karbantartás    
- *Könnyen üzembe helyezhető és kezelhető*
  - Gyors [ügynöktelepítés](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs) 
  - Az ügynökök automatikus frissítése a legújabb verzióra 
  - Az adatok perceken belül elérhetők a portálon    
- *Részletes [használati metrikák](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)* 
  - A leggyakrabban használt alkalmazások
  - Hálózati helyek és TCP-kapcsolat
  - Jogkivonat-kérelmek kiszolgálónként    
- *Nagyszerű felhasználói élmény* 
  - Az Azure Portal irányítópult stílusa
  - [Riasztások e-mailben](how-to-connect-health-adfs.md#alerts-for-ad-fs)    

#### <a name="feature-highlight"></a>Kiemelt funkciók

*   Riasztásokkal kiegészített megfigyelés az AD FS és az AD FS-proxykiszolgálók nem megfelelő állapotának észleléséhez;
*   kritikus riasztásokhoz kapcsolódó e-mail értesítések;
*   Teljesítményadat-tendenciák, amelyek hasznosak az AD FS kapacitástervezéséhez;
*   Az AD FS használatának különböző területekre (például alkalmazásokra, felhasználókra vagy hálózati helyekre) kiterjedő elemzései
*   AD FS-jelentések, például a rossz felhasználónév/jelszó párossal legtöbbet próbálkozó 50 felhasználó, az utolsó IP-címükkel együtt
*   Kockázatos IP jelentés meghiúsult AD FS-bejelentkezésekhez

Itt olvashat többet [az Azure AD Connect Health használatáról az AD FS szolgáltatással](how-to-connect-health-adfs.md)

### <a name="azure-ad-connect-health-for-synchow-to-connect-health-syncmd"></a>[Azure AD Connect Health szinkronizálási szolgáltatás](how-to-connect-health-sync.md)
Az Azure AD Connect Health szinkronizálási szolgáltatás figyelemmel kíséri a helyszíni Active Directory és az Azure AD közötti szinkronizálásokat, és információkat biztosít róluk. Az Azure AD Connect Health for Sync legfontosabb képességei a következők:

* Riasztásokkal kiegészített monitorozás az Azure AD Connect-kiszolgáló (más néven a szinkronizálási motor) nem megfelelő állapotának észleléséhez;
* kritikus riasztásokhoz kapcsolódó e-mail értesítések;
* A szinkronizálási műveletek elemzései, beleértve a rájuk vonatkozó késési diagramokat és olyan különböző műveletek tendenciáit, mint például a hozzáadások, a frissítések vagy a törlések;
* A szinkronizálási tulajdonságok és az Azure AD-re történt legutóbbi sikeres exportálási művelet gyors áttekintése
* Az objektumszintű szinkronizációs hibákról való jelentésekhez \(nem szükséges Prémium szintű Microsoft Azure AD\)

Itt olvashat többet [az Azure AD Connect Health szinkronizálási szolgáltatás használatáról](how-to-connect-health-sync.md)

### <a name="azure-ad-connect-health-for-ad-dshow-to-connect-health-addsmd"></a>[Azure AD Connect Health for AD DS](how-to-connect-health-adds.md)
Az Active Directory Domain Serviceshez (AD DS) készült Azure AD Connect Health a Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 vagy Windows Server 2016 rendszeren telepített tartományvezérlők figyelését teszi lehetővé. Az állapotügynök telepítése lehetővé teszi, hogy a helyszíni AD DS-környezetet a felhőből figyelje meg. Az Azure AD Connect Health for AD DS legfontosabb képességei a következők:

* Riasztások figyelése a tartományvezérlő megfelelő állapotának észlelése érdekében, valamint e-mailes értesítések küldése a kritikus riasztásokról
* A tartományvezérlő irányítópultja gyors betekintést biztosít a tartományvezérlők állapotába és üzemeltetési állapotába
* A Replikáció állapota irányítópult, amely a legújabb replikációs adatokkal rendelkezik, a hibák észlelésekor hibaelhárítási útmutatókra mutató hivatkozásokkal szolgál
* Gyors és helyfüggetlen hozzáférést biztosít a népszerű teljesítményszámlálók teljesítményadat-grafikonjaihoz, melyek hibaelhárítási és figyelési célból szükségesek

Itt olvashat többet [az Azure AD Connect Health használatáról az AD DS szolgáltatással](how-to-connect-health-adds.md)

## <a name="next-steps"></a>További lépések


- [Hardver és előfeltételek](how-to-connect-install-prerequisites.md) 
- [Gyorsbeállítások](how-to-connect-install-express.md)
- [Testreszabott beállítások](how-to-connect-install-custom.md)
- [Jelszókivonat szinkronizálása](how-to-connect-password-hash-synchronization.md)|
- [Átmenő hitelesítés](how-to-connect-pta.md)
- [Azure AD Connect és összevonás](how-to-connect-fed-whatis.md)
- [Az Azure AD Connect Health-ügynökök telepítése](how-to-connect-health-agent-install.md) 
- [Az Azure AD Connect szinkronizálása](how-to-connect-sync-whatis.md)
- [Verzióelőzmények](reference-connect-version-history.md)
- [Címtár-integrációs eszközök összehasonlítása](plan-hybrid-identity-design-considerations-tools-comparison.md)
- [Azure AD Connect – gyakori kérdések](reference-connect-faq.md)









