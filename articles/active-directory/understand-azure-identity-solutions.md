---
title: "Azure-identitás megértése |} Microsoft Docs"
description: "Ismerkedjen meg a Microsoft Azure identitáskezelési megoldás feltételeket, fogalmak és javaslatok csak akkor lehet a legjobb identitás irányítás döntés a szervezete."
keywords: 
author: jeffgilb
manager: femila
ms.reviewer: jsnow
ms.author: jeffgilb
ms.date: 7/17/2017
ms.topic: article
ms.prod: 
ms.service: azure
ms.technology: 
ms.assetid: 
ms.custom: it-pro
ms.openlocfilehash: e368f14638c480a632afa7c17023aa8ae4c8833f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="understand-azure-identity-solutions"></a>Az Azure identitáskezelési megoldásairól ismertetése
A Microsoft Azure Active Directory (Azure AD) egy identitás- és hozzáférés kezelési felhőalapú megoldás, amely címtárszolgáltatások, identitás-irányítás és alkalmazáshozzáférés-kezeléshez. Az Azure AD gyorsan [lehetővé teszi az egyszeri bejelentkezés (SSO)](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso) 1, 000 a kereskedelmi és egyéni előre integrált alkalmazások által a [az Azure AD application gallery](https://azure.microsoft.com/marketplace/active-directory/all/). Ezekhez az alkalmazásokhoz számos valószínűleg már használhatja például az Office 365, a Salesforce.com, a mezőbe, a ServiceNow és a Workday.

Egyetlen Azure AD-címtár létrehozásakor automatikusan az Azure-előfizetéssel társítva. Az identitás szolgáltatásként az Azure-ban majd az Azure AD felhőalapú erőforrások Identitáskezelés és a hozzáférés-vezérlési funkciók biztosít. Ezeket az erőforrásokat például felhasználók, az alkalmazások és a csoportok az egy adott bérlő (szervezet) a következő ábrán látható módon:

![Azure Active Directory](./media/understand-azure-identity-solutions/azure-ad.png)

A Microsoft Azure szolgáltatásként (IDaaS) identitás kihasználhatják számos lehetőséget kínál különböző szintű összetettség egyedi szervezete igényeinek. Ez a cikk többi segít megérteni a alapszintű Azure-identitás terminológia és a fogalmakat, valamint a javaslatok csak akkor lehet a legjobb megoldás az elérhető lehetőségek közül.

## <a name="terms-to-know"></a>Tudni feltételek

Dönthet úgy is egy Azure-identitás megoldás a szervezete számára, amikor Azure-identitás szolgáltatások van szó gyakran használt kapcsolatos alapvető ismeretekkel kell.

|Tudni kifejezés| Leírás|
|-----|-----|
|Azure-előfizetés |Előfizetések használatával kell fizetnie Azure felhőszolgáltatások, általában hitelkártya van csatolva. Több előfizetéssel is rendelkezik, de az előfizetések közötti erőforrások megosztása nehézkes lehet.|
|Azure-bérlőhöz | Az Azure AD-bérlő egyetlen szervezet képviselője. Az Azure AD, amely automatikusan jön létre, amikor egy szervezet előfizet a Microsoft felhőalapú szolgáltatás előfizetésének például Azure, az Intune-ban vagy az Office 365 dedikált, megbízható példánya. Bérlők szolgáltatások az egyik egy dedikált környezetben (egybérlős) vagy más szervezetekkel (több-bérlős) megosztott környezetben is hozzáférhetnek.|
|Azure AD-címtár | Minden Azure-bérlőhöz tartozik egy dedikált, megbízható Azure AD-címtár, amely tartalmazza a bérlő felhasználók, csoportok és alkalmazások. Identitás és felügyeleti funkciók bérlői erőforrások eléréséhez szolgál. Mivel egy egyedi Azure AD-címtár automatikusan ki van építve a szervezet képviseli, amikor regisztrál egy Microsoft felhőszolgáltatásra, például az Azure, a Microsoft Intune, illetve az Office 365, látni fogja, esetenként a feltételek *bérlői*, *az Azure AD*, és *Azure AD-címtár* jelentése megegyezik. |
|Egyéni tartomány | Amikor először iratkozik fel a Microsoft felhőalapú szolgáltatás előfizetési, a bérlő (szervezet) használ egy *. onmicrosoft.com* tartomány nevét. Azonban a legtöbb szervezet egy vagy több üzleti és a végfelhasználók használt tartománynevek használatával a vállalati erőforrások eléréséhez. Az egyéni tartománynevet adhat hozzá az Azure AD, így például a felhasználók számára a tartománynév  *alice@contoso.com*  helyett  *alice@contoso.onmicrosoft.com* . |
|Azure AD-fiók | Ezek a identitásokat tartalmaz, amelyek az Azure AD használatával létrehozott vagy egy másik Microsoft felhőszolgáltatásra, például az Office 365. Az Azure ad-ben tárolt és a szervezet felhőszolgáltatási előfizetésein bármelyikét elérhetővé válnak. |
|Azure-előfizetési rendszergazda| A fiókadminisztrátor az a személy, aki regisztrált a vagy vásárolt Azure-előfizetést. Használhatnak a [Account Center](https://account.azure.com/Subscriptions) például előfizetések létrehozása különböző felügyeleti feladatok elvégzésére, előfizetések megszakítja, módosíthatja az előfizetés számlázási, vagy a szolgáltatás rendszergazdájával. |
|Az Azure AD globális rendszergazda | Az Azure AD globális rendszergazdák rendelkezik teljes körű hozzáférés az Azure AD az összes felügyeleti funkcióhoz. A személy, aki előfizet egy felhőalapú Microsoft-szolgáltatás előfizetésének automatikusan alapértelmezés szerint a globális rendszergazdája lesz. Egynél több globális rendszergazda is rendelkezik, de csak globális rendszergazdák rendelhetik hozzá bármelyik [az egyéb rendszergazdai szerepköröket](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) a felhasználók számára. |
|Microsoft-fiók | Microsoft-fiókok (létrehozott Ön által személyes használatra) Microsoft-termékek kereskedelmi célú hozzáférést biztosítanak, és a felhőalapú szolgáltatások, mint például az Outlook (Hotmail), OneDrive, Xbox LIVE, vagy az Office 365. Az identitások létrehozására és a Microsoft fiókkal futtatott végfelhasználói identitásrendszer Microsoft tárolja.|
|Munkahelyi vagy iskolai fiókok | Munkahelyi vagy iskolai fiókját (üzleti vagy oktatási használatra rendszergazdája által kibocsátott) a vállalati szintű Microsoft felhőszolgáltatások, például Azure, az Intune-ban vagy az Office 365 vállalati hozzáférést biztosítanak.|


## <a name="concepts-to-understand"></a>Megértendő koncepciókról

Most, hogy tudja, hogy az alapszintű Azure-identitás feltételeket, akkor kell tudhat meg többet a Azure-identitás fogalmakat, amelyek segítenek a döntést egy tájékozott Azure-identitás szolgáltatás.

|Fogalom megértéséhez |Leírás|
|-----|-----|
|[How Azure subscriptions are associated with Active Directory? (Hogyan kapcsolódnak az Azure-előfizetések az Azure Active Directory-hoz?)](https://docs.microsoft.com/azure/active-directory/active-directory-how-subscriptions-associated-directory) |Minden Azure-előfizetéssel rendelkező felhasználók, szolgáltatások és eszközök hitelesítéséhez az Azure AD-címtár megbízhatósági kapcsolatban áll. *Több előfizetés is megbízhat ugyanabban az Azure AD-címtárban, de egy előfizetés csak egyetlen megbízik az Azure AD-címtár*. A bizalmi kapcsolat más Azure-erőforrások (webhelyek, adatbázisok és így tovább), amelyek több mint egy előfizetés tartozik előfizetés gyermekerőforrásainak van. Ha egy előfizetés lejár, majd az Azure AD kívül az előfizetéshez tartozó erőforrásokhoz való hozzáférés is leáll. Azonban az Azure AD-címtár marad, az Azure-ban, hogy más előfizetéseket társíthat a könyvtárhoz, és kezelheti a bérlői erőforrásokhoz.|
|[Licencelés működéséről az Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-get-started-azure-portal) | Vásároljon, vagy a nagyvállalati mobilitási csomag, a prémium szintű Azure AD és a Azure AD alapvető aktiválása, a címtár frissül az előfizetést, beleértve a érvényességi időtartam és a fizetett licenccel. Amint az előfizetése aktív, a szolgáltatás az Azure AD globális rendszergazdák által kezelhető és licenccel rendelkező felhasználók által használt. Az előfizetési adatai, beleértve a hozzárendelt vagy elérhető licencek száma érhető el az Azure-portálon a **Azure Active Directory** > **licencek** panelen. Ez az a legjobb hely a licenc-hozzárendelések kezelésére is.|
|[Szerepköralapú hozzáférés-vezérlés az Azure-portálon](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is)|Azure szerepköralapú hozzáférés-vezérlés (RBAC) segítségével adja meg a részletes hozzáféréskezelést az Azure-erőforrások. Túl sok engedélyek teszi közzé, és a támadók fiókot. Túl kevés engedélyek, az azt jelenti, hogy az alkalmazottak nem munkavégzéséhez hatékony. RBAC használata, biztosíthat az alkalmazottak a pontos engedélyek van szükségük, amelyek érvényesek az összes erőforráscsoport három alapvető szerepkörök alapján: tulajdonos, közreműködő, olvasó. Is létrehozhat saját legfeljebb 2000 [egyéni RBAC-szerepkörök](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) konkrét igényeinek. |
|[Hibrid identitás](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)|Hibrid identitás úgy érhető el a helyszíni Windows Server Active Directory (AD DS) integrálása az Azure AD használatával [az Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect). Ez lehetővé teszi, hogy egy közös identitás biztosítása a felhasználók számára az Office 365, Azure-ban és a helyszíni alkalmazások vagy az Azure ad-vel integrált Szolgáltatottszoftver-alkalmazáshoz. Hibrid identitás, az gyakorlatilag terjesztheti ki a felhőbe az identitás- és hozzáférés a helyszíni környezetben.|

### <a name="the-difference-between-windows-server-ad-ds-and-azure-ad"></a>Windows Server Active Directory tartományi szolgáltatások és az Azure AD közötti különbség
Azure Active Directory (Azure AD), mind a helyszíni Active Directory (Active Directory tartományi szolgáltatások vagy az AD DS) a rendszerek, a címtáradatok tárolására és kezelésére a felhasználókat és erőforrásokat, például a bejelentkezési folyamatot, a hitelesítés és a directory-keresések közötti kommunikációt.

Ha már ismeri a helyszíni Windows Server Active Directory tartományi szolgáltatások (AD DS), első bevezetése Windows 2000 Server, akkor valószínűleg megismerte az Alkalmazásidentitás szolgáltatás alapvető fogalma. Azonban fontos is tisztában lenni azzal, hogy az Azure AD nem csak egy tartományvezérlő a felhőben. A program egy teljesen új arra identitás biztosító szolgáltatásként (IDaaS), amelyhez egy teljesen új módon teljes mértékben támogató felhőalapú képességek és a szervezet modern fenyegetésekkel szembeni hatékony védelmét végezni az Azure-ban. 

Active Directory tartományi szolgáltatások egy kiszolgálói szerepkör, Windows Server, ami azt jelenti, hogy központilag telepíthető fizikai vagy virtuális gépeken. Van olyan hierarchikus struktúra X.500 alapján. DNS-objektumok, keresése képes kezelni az LDAP segítségével, és elsősorban használ Kerberos hitelesítéshez használ. Az Active Directory lehetővé teszi a szervezeti egységekhez (OU-k) és csoportházirend-objektumok (GPO) mellett gépek csatlakoztatása a tartományhoz, és bizalmi kapcsolat jön létre a tartományok között.

IT-védelemmel látta el a biztonsági szegélyhálózati évre Active Directory tartományi szolgáltatások használatával, de identitás igények támogatása az alkalmazottak, ügyfelek és partnerek szükséges egy új vezérlő vezérlősík modern, a peremhálózati nélküli vállalatok. Az Azure AD, hogy identitás vezérlő vezérlősík. Biztonsági túljutott azon a vállalati tűzfalon a felhőbe, amelyben az Azure AD védelemmel látja el a vállalati erőforrásokhoz és a hozzáférés egy közös identitás biztosítása a felhasználók által (a helyszínen vagy a felhőben). Így a felhasználók biztonságosan elérni az alkalmazások munkavégzéséhez szinte bármilyen eszközről van szükségük a rugalmasságot. Zökkenőmentes kockázat-alapú védelmi adatvezérlők, biztonsági gépi tanulásra képességek, valamint részletes jelentés, feltéve, hogy az informatikai részlegnek a vállalati adatok védelmét is vannak.

Az Azure AD egy több ügyfél nyilvános címtárszolgáltatás, ami azt jelenti, hogy az Azure AD belül hozhat létre a bérlőt, a felhő kiszolgálókon és az alkalmazások, mint az Office 365. Felhasználók és csoportok jönnek létre egy egyszerű struktúra nélkül szervezeti egységek vagy csoportházirend-objektumokat. Történik hitelesítés, SAML, például protokollokon keresztül WS-Federation és OAuth. Lehet lekérdezni az Azure AD, de az LDAP használata helyett a REST API hívása AD Graph API-t kell használnia. A HTTP és HTTPS protokollt használó összes működik.

### <a name="extend-office-365-management-and-security-capabilities"></a>Office 365 felügyeleti és biztonsági lehetőségek bővítése
Már az Office 365-tel? Felgyorsítható a digitális átalakításában, a erőforrások biztonságossá beépített Office 365-képességek az Azure AD által biztonságos termelékenység engedélyezése a teljes munkaerő igényeire. Az Azure AD, Office 365 képességek mellett használatakor biztonságossá teheti a teljes alkalmazás-portfóliót, amely lehetővé teszi, hogy egyszeri bejelentkezést az összes olyan alkalmazáshoz egy identitással. A feltételes hozzáférési képességeket, ne csak az eszköz állapotát, de a felhasználó helyét, az alkalmazás és a kockázatot is alapú bővítheti. Többtényezős hitelesítés (MFA) képességek még több védelmet nyújt, ha esetleg szükség lenne rá. További felügyelet felhasználói jogosultságokat kapnak lesz, és igény szerint közvetlenül az időponthoz kötött rendszergazdai hozzáférést biztosítanak. A felhasználó hatékonyabban dolgozhasson, és hoznak létre kevesebb segélyszolgálat, például a rendszer visszaállítja az Elfelejtett jelszó, alkalmazás-hozzáférési kérelmeket, és a csoportok létrehozása és kezelése az Azure AD biztosít önkiszolgálói képességeit környezetnek köszönhetően.

> [!TIP]
> További információ az Azure AD identity management és az Office 365 szeretné? [Az e-könyv beolvasása](https://info.microsoft.com/Extend-Office-365-security-with-EMS.html).

## <a name="microsoft-azure-identity-solutions"></a>A Microsoft Azure identitáskezelési megoldásairól

A Microsoft Azure kezelheti a felhasználók identitását, hogy a teljes kezelt számos lehetőséget kínál a helyi, csak a felhőben, vagy még valahol az között. Ezek a lehetőségek a következők: Azure, az Azure Active Directory (Azure AD), a hibrid identitás és a Azure AD tartományi szolgáltatások saját munka (DIY) AD DS.

### <a name="do-it-yourself-diy-ad-ds"></a>Saját munka (DIY) Active Directory tartományi szolgáltatások
A vállalatok számára a felhőben, csak kevés erőforrást igénylő **saját munka (DIY) Active Directory tartományi szolgáltatások** az Azure-ban is használható. Ez a beállítás szituációkat számos Windows Server Active Directory tartományi szolgáltatások, Azure virtuális gépek (VM) központi telepítés jól alkalmazható. Például egy Azure virtuális gép is létrehozhat, amely csatlakozik a távoli hálózati közösségihez adatközpontban fut tartományvezérlőként működik. Ott a virtuális gép tudná a távoli felhasználók érkező hitelesítési kéréseket támogatja, és növelheti a hitelesítés teljesítményét. Ez a beállítás akkor is jól alkalmazható egyéb költséges vész helyreállítási webhelyek viszonylag alacsony költségű helyettesítésére tartományvezérlők és az Azure-on egyetlen virtuális hálózat kis számú üzemeltetnie. Végül szükség lehet az Azure, a SharePoint, a Windows Server Active Directory tartományi szolgáltatások igényel, de nem függ a helyi hálózaton vagy a vállalati Windows Server Active Directory például alkalmazás központi telepítését. Ebben az esetben telepíthet egy elkülönített erdő Azure a SharePoint server farm követelményeinek megfelelően. A helyszíni hálózat és a helyszíni Active Directory kapcsolatot igényelnek a hálózati alkalmazások telepítéséhez is támogatott.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
**Az Azure AD önálló** van egy teljes mértékben felhőalapú identitás és hozzáférés-kezelés szolgáltatás (IDaaS) megoldás. Az Azure AD lehetővé teszi egy robusztus szolgáltatásaira felhasználók és csoportok kezelése. és biztonságosabbá teszi a helyszíni és a felhőbeli alkalmazásokhoz, így a Microsoft webszolgáltatásaihoz, például az Office 365-höz, illetve számos más, nem a Microsoft által készített SaaS-alkalmazáshoz való hozzáférést. Az Azure AD három kiadásai származik: ingyenes, a Basic és a prémium szintű. Az Azure AD növekedhet szervezeti hatékonyságát és kiterjesztik a peremhálózati tűzfal védi az Azure machine learning és más új vezérlő vezérlősík túli biztonsági speciális biztonsági szolgáltatásokat.

### <a name="hybrid-identity"></a>Hibrid identitás
Ahelyett, hogy válassza ki a helyszíni vagy felhőalapú identitás-megoldások között, mint számos előre-számbavétele igazgatók és vállalatok számára, amely a vállalat távon előrejelző megkezdődött, kiterjesztése a helyszíni címtárak felhőbe keresztül **hibrid identitás** megoldásokat. Hibrid identitás, az valóban globális identitás kap, és hozzáférés-kezelési megoldás, amely hatékony és biztonságos hozzáférést biztosít az alkalmazások felhasználók számára van szükség a munkájukat.

> [!TIP]
> Hogyan igazgatók rendelkezik készült Azure Active Directory központi az IT-stratégiákat kapcsolatos további információkért töltse le a [CIO meg az útmutató az Azure Active Directory](https://aka.ms/AzureADCIOGuide).

### <a name="azure-ad-domain-services"></a>Azure AD Domain Services
**Azure AD tartományi szolgáltatások** egy Active Directory tartományi szolgáltatások egyszerűsített Azure virtuális gép konfigurációs ellenőrzésére és a helyszíni identitás követelményeinek megfelelően, a hálózati alkalmazások fejlesztését és tesztelését úgy használandó felhőalapú lehetőséget biztosít. Azure AD tartományi szolgáltatások nem célja, hogy a növekedési és az eltolás mértékét megadó a helyszíni Active Directory tartományi szolgáltatások infrastruktúrát Azure virtuális gépek Azure AD tartományi szolgáltatások által felügyelt. Ehelyett a felügyelt tartományok Azure virtuális gépek használatával támogatja a fejlesztési, tesztelési és Active Directory tartományi szolgáltatások hitelesítési módszerek a felhőbe a helyszíni alkalmazások mozgása.

## <a name="common-scenarios-and-recommendations"></a>Gyakori forgatókönyvek és javaslatok

Az alábbiakban néhány gyakori identitások és hozzáférések forgatókönyvek, hogy melyik Azure identitás beállítás akkor lehet leginkább megfelelő, az egyes javaslatok.

|Identitás használatára| Ajánlás|
|-----|-----|
|A szervezet helyszíni Windows Server Active Directory által végrehajtott nagy beruházások értékét, de biztosak szeretnénk kiterjesztése a felhőbe identitás.| A legszélesebb körben használt Azure-identitás megoldás [hibrid identitás](https://docs.microsoft.com/azure/active-directory/active-directory-hybrid-identity-design-considerations-overview). Ha már végrehajtott beruházások a helyszíni Active Directory tartományi Szolgáltatásokban, könnyen kiterjesztheti a felhőbe az Azure AD Connect identitáskezelési.|
|A saját üzleti született a felhőben, és nem beruházások értékét kell a helyszíni identitáskezelési megoldások.| [Az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) nélküli csak felhőalapú vállalatok számára a legjobb választás a helyszínen beruházások értékét.|
|Egyszerűsített Azure Virtuálisgép-konfiguráció szükséges és vezérlő teljesítéséhez a helyszíni identitáskövetelmények alkalmazások fejlesztésére és tesztelésére.|[Azure AD tartományi szolgáltatások](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) akkor hasznos, ha szeretné használni az Active Directory tartományi szolgáltatások egyszerűsített Azure virtuális gép konfigurációs vezérlő vagy fejlesztése vagy örökölt, directory-t támogató helyszíni alkalmazásokat a felhőbe át szeretné.|  
|Néhány virtuális gépek támogatják az Azure-ban kell, de a vállalatom továbbra is erősen fektetett a helyszíni Active Directory (AD DS).|Használjon [DIY Active Directory tartományi szolgáltatások](https://msdn.microsoft.com/library/azure/jj156090.aspx) Azure virtuális gépek használata, ha néhány virtuális gép támogatása, és nagy Active Directory tartományi szolgáltatások beruházások rendelkeznie kell a helyszíni. |

## <a name="where-can-i-learn-more"></a>Hol kaphatok további?
Van egy tonna kiváló erőforrások online részletes tudnivalók az Azure AD segítséget. Az első lépésekhez kiváló cikkek listája itt található:

* [A címtár az Azure AD Connect hibrid Management engedélyezése](active-directory-aadconnect.md)
* [Egy legalább egyszer világgal további biztonsági](../multi-factor-authentication/multi-factor-authentication.md)
* [Felhasználói kiépítésének és megszüntetésének biztosítása SaaS-alkalmazásokhoz az Azure Active Directoryval történő automatizálásához](active-directory-saas-app-provisioning.md)
* [Ismerkedés az Azure AD Reporting](active-directory-reporting-getting-started.md)
* [A jelszavak kezelése bárhonnan](active-directory-passwords.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)
* [Felhasználói kiépítésének és megszüntetésének biztosítása SaaS-alkalmazásokhoz az Azure Active Directoryval történő automatizálásához](active-directory-saas-app-provisioning.md)
* [Útmutató a helyszíni alkalmazások biztonságos távoli hozzáférést biztosítanak](active-directory-application-proxy-get-started.md)
* [Erőforráshozzáférés-kezelés Azure Active Directory-csoportokkal](active-directory-manage-groups.md)
* [Mi az a Microsoft Azure Active Directory licencelésének ismertetése](active-directory-licensing-what-is.md)
* [Hogyan lehet használt, jóvá nem hagyott felhőalkalmazások felderítése a szervezeten belül](active-directory-cloudappdiscovery-whatis.md)

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az Azure-identitás fogalmakat és a lehetőségeket, a következő források segítségével első lépések végrehajtása a kiválasztott beállítás:

[További tudnivalók az Azure hibrid identitáskezelési megoldások](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution)

[További információ a koncepció igazolása Azure környezetben:](https://aka.ms/aad-poc)

[Azure AD-t éles központi telepítése](https://aka.ms/aad-onboard)
