---
title: Az Azure biztonsági műszaki képességei |} Microsoft Docs
description: További információk a felhőalapú számítási szolgáltatás, amely tartalmazza a számítási példányokért széles kijelölt & szolgáltatások, amely is fel-le automatikusan az alkalmazás vagy a vállalat igényeinek.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: 6643db7b732cc5b01ce7602eb3d679c130c46720
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895292"
---
# <a name="azure-security-technical-capabilities"></a>Az Azure biztonsági technikai lehetőségeit

Segít a jelenlegi és jövőbeli Azure ügyfelek ismertetése és a különböző biztonsági lehetőségeinek használják, és az Azure platformra körülvevő, Microsoft fejlesztett ki több tanulmányok, biztonsági áttekintése, ajánlott eljárások és Ellenőrzőlisták. A témakörök között körének és mélysége, és rendszeresen frissülnek. Ez a dokumentum a sorozat része az alábbi absztrakt szakaszban foglaltak szerint. További információk az Azure biztonsági adatsorozat (URL) helyen találhatók meg.

## <a name="azure-platform"></a>Azure-platform

[A Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) a felhőplatform magában foglalja az infrastruktúra és a nyilvános felhő adatokat a Microsoft adatközpontjaiban gazdája alkalmazásszolgáltatások, integrált adatszolgáltatások és a speciális elemzés, és a fejlesztői eszközök és a szolgáltatások,. Az ügyfelek Azure számos különböző kapacitások és forgatókönyvek, az alapvető számítási, hálózati és tárolási, mobil és web app Service szolgáltatások, a teljes felhő helyzetekben, például az eszközök internetes hálózatát, és is kell használt nyílt forráskódú technológiákkal és hibrid felhős központilag telepített vagy üzemeltetett ügyfél adatközponton belül. Azure biztosít felhőtechnológia építőelemeket, a vállalatoknak, költségek csökkentése érdekében innovációját gyorsan, és rendszerek proaktív kezeléséhez. Létrehozásához, vagy a felhőbeli szolgáltató telepíthet át informatikai eszközök, az alkalmazások és a szolgáltatások és a biztonsági a felhő alapú eszközök kezelésére adathordozóira vezérlők adatok védelme érdekében, hogy szervezete képességek vannak hagyatkoznia.

Microsoft Azure az egyetlen olyan felhőalapú informatikai szolgáltató egy biztonságos, egységes platform által és infrastruktúra,--szolgáltatás működéséhez belül a különböző felhőalapú skillsets és a projekt összetettségét, integrált adatszolgáltatások és elemzés, amely bárhol létezik, mind a Microsoft, mind a nem Microsoft-platformokat, fedik le az eszközintelligencia adatokból szintű csoportjai nyissa meg a keretrendszerek és az eszközök, biztosítva, hogy a választott felhő integrálása a helyszíni, valamint telepítése az Azure felhőszolgáltatások belül a helyszíni adatközpontokkal. A Microsoft megbízható felhő részeként ügyfelek támaszkodnak Azure iparágvezető biztonsági, megbízhatósági, megfelelőségi, adatvédelmi és a nagy hálózati felhasználók, partnerek és folyamatok szervezetek támogatásához a felhőben.

A Microsoft Azure-ban a következőket teheti:

- A felhő innováció érdekében.

- Energiagazdálkodási üzleti döntéseket &, amelyen az alkalmazásokat.

- Szabadon és üzembe helyezheti tetszőleges helyre.

- Az üzleti védelme.

## <a name="scope"></a>Hatókör

A központi eleme e tanulmány vonatkozik, biztonsági szolgáltatásokat és funkciókat támogatja a Microsoft Azure alapvető összetevői, nevezetesen [Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-introduction), [Microsoft Azure SQL-adatbázisok](https://docs.microsoft.com/azure/sql-database/), [Microsoft Azure virtuális gép modell](https://docs.microsoft.com/azure/virtual-machines/), és az eszközök és infrastruktúra, amely az adatbázis felügyeletét az összes. E tanulmány arra utalnak, Microsoft Azure műszaki elérhető lehetőségeket, az ügyfelek biztonsági és adatvédelmi az adatok védelmének szerepkörük teljesítése érdekében.

A megosztott felelősségi modell ismertetése fontosságát elengedhetetlen az ügyfelek, akik át a felhőbe. Felhőbeli szolgáltatók biztonsági és megfelelőségi erőfeszítéseket jelentős előnyeit, de ezeket az előnyöket nem absolve az ügyfél a felhasználók, az alkalmazások és a szolgáltatásajánlatok védjék.

IaaS-megoldásokat az ügyfél felelős, vagy védelme és kezelése az operációs rendszer, hálózati konfiguráció, alkalmazások, identitás, ügyfelek és az adatok megosztott felelős.  A PaaS megoldások build IaaS üzemelő példányok esetében, az ügyfél továbbra is felelős, vagy megosztott felelős biztonságossá tétele és az alkalmazások, identitás, az ügyfelek és adatok kezelése. A Szolgáltatottszoftver-megoldások, Nonetheless, az ügyfél továbbra is felelős. Ezek győződjön meg arról, hogy az adatok besorolásának megfelelően, és osztoznak egy felelőssége, hogy a felhasználók és a végpont eszközök kezelésére.

Ez a dokumentum nem biztosít részletes bármely a kapcsolódó Microsoft Azure platform összetevők, például Azure-webhelyek, Azure Active Directory, HDInsight, a Media Services, és más szolgáltatásokat az alapvető összetevői elveire réteges vannak. Bár a legkisebb általános információkat, olvasók ismeri az Azure rendszerhez kapcsolódó alapfogalommal mutató egyéb hivatkozásokat a Microsoft által biztosított, és ez a dokumentum a megadott hivatkozások szerepelnek a rendszer feltételezi.

## <a name="available-security-technical-capabilities-to-fulfil-user-customer-responsibility---big-picture"></a>Biztonsági technikai képességekkel (ügyfél) felhasználó felelőssége - nagy vonalakban tekinti teljesítéséhez

A Microsoft Azure biztosít, szolgáltatásokat, amelyek segítségével az ügyfelek igényeihez biztonsági, adatvédelmi és megfelelőségi. A következő kép segítségével különböző Azure-szolgáltatások a felhasználók egy iparági normák biztonságosságának és alkalmazás-infrastruktúra felépítéséhez ismertetik.

![Biztonsági technikai képességekkel-nagy kép](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig1.png)

## <a name="manage-and-control-identity-and-user-access-protect"></a>Kezelhetik és szabályozhatják az identitás- és felhasználói hozzáférést (védelem)

Azure segítséget nyújt a vállalati és személyes adatok védelmét azáltal, hogy engedélyezi a felhasználói identitások és a hitelesítő adatok kezelése és hozzáférés szabályozása.

### <a name="azure-active-directory"></a>Azure Active Directory

Microsoft identitások és hozzáférések felügyeleti megoldások Súgó informatikai védelme alkalmazásokhoz és erőforrásokhoz való hozzáférést az vállalati adatközpontban és a felhőben, például a többtényezős hitelesítés és a feltételes hozzáférési házirendek ellenőrzéséhez további szintek engedélyezése. Gyanús tevékenységek figyelése keresztül speciális biztonsági jelentések, a naplózás és a riasztási segít mérsékelni a potenciális biztonsági problémákat. [Az Azure Active Directory Premium](https://docs.microsoft.com/azure/active-directory/active-directory-editions) biztosítja az egyszeri bejelentkezés felhő több ezer (SaaS) alkalmazások és webalkalmazások hozzáférést a helyszíni futtatása.

Biztonsági szempontból előnyökkel járhat az Azure Active Directory (Azure AD) megadhatják a:

- Hozzon létre, és egy egyetlen identitást az egyes felhasználók kezeléséhez a hibrid vállalati, a felhasználók, csoportok és az eszközök szinkronban tartja.

- Adja meg az egyszeri bejelentkezéses hozzáférést több ezer előre integrált Szolgáltatottszoftver-alkalmazásoknál, beleértve az alkalmazások.

- Engedélyezze a hozzáférést alkalmazásbiztonsági mind a helyszíni szabályalapú többtényezős hitelesítés kényszerítése, és a felhőalapú alkalmazásokhoz.

- A helyszíni webalkalmazások Azure AD alkalmazásproxy segítségével biztonságos távoli hozzáférés kiépítéséhez.

A [Azure Active Directory portálon](http://aad.portal.azure.com/) érhető el az Azure-portálon részét. Erről az irányítópultról áttekintheti a szervezet állapotát, és könnyen alaposabban tanulmányozhatja a könyvtárat, a felhasználók vagy az alkalmazás-hozzáférés kezelése.

![Azure Active Directory](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig2.png)

Core Azure identitáskezelési funkciói a következők:

- Egyszeri bejelentkezés

- Multi-Factor Authentication

- Biztonsági figyelést, a riasztások és a machine learning-alapú jelentések

- A felhasználói identitások és hozzáférés kezelése

- Eszközregisztráció

- Privileged identity management

- Identity Protection

#### <a name="single-sign-on"></a>Egyszeri bejelentkezés

[Egyszeri bejelentkezés (SSO)](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) azt jelenti, hogy nem tudnak hozzáférni az alkalmazások és az üzleti tevékenységet, egyszer csak egyetlen felhasználói fiókkal a bejelentkezéssel szükséges erőforrások. Miután bejelentkezett, van-e hozzáférési összes szolgáltatásbeli hitelesítéshez szükséges anélkül, hogy szüksége alkalmazások (például adjon meg egy jelszót) még egyszer.

Számos szervezet alkalmazásokként egy szolgáltatott szoftverként (SaaS) például Office 365, a mezőben és a Salesforce a végfelhasználói termelékenység szoftver támaszkodnak. Hagyományosan informatikai munkatársak külön-külön létrehozásához, és minden SaaS-alkalmazás a felhasználói fiókok frissítése szükséges, és a felhasználóknak kellett jegyezze meg a jelszót az egyes SaaS-alkalmazáshoz.

[Az Azure AD a helyszíni Active Directory kiterjeszti a felhőbe](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis), lehetővé teszi nemcsak az elsődleges szervezeti fiókkal jelentkeznek be a tartományhoz csatlakoztatott eszközökre és a vállalati erőforrások, de szükséges is minden a web- és SaaS-alkalmazásokhoz a feladat.

Nem csak felhasználóknak nem kell több példányban felhasználónevek és jelszavak kezelése, alkalmazás-hozzáférés csak alapján automatikusan kiosztott vagy vonja kiosztott szervezeti csoportok és az állapotuk egy alkalmazott. [Az Azure AD vezet be a biztonsági és irányítási vezérlők](https://docs.microsoft.com/azure/active-directory/active-directory-sso-integrate-saas-apps) , amelyek lehetővé teszik a központilag kezelheti a felhasználói hozzáférés SaaS-alkalmazások között.

#### <a name="multi-factor-authentication"></a>Multi-Factor Authentication

[Az Azure multi-factor Authentication (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) egynél több ellenőrzési módszer használatát igényli, és a kritikus fontosságú második biztonsági réteget ad hozzá felhasználói bejelentkezéseket és tranzakciókat hitelesítési módszer. [Többtényezős hitelesítés segítségével a biztonságos működés érdekében](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works) adat és alkalmazás megfelel a felhasználó igény szerint egy egyszerű bejelentkezési folyamat során a hozzáférést. Erős hitelesítés, ellenőrzési lehetőségek széles keresztül biztosítja – a telefonhívás, szöveges üzenet vagy mobilalkalmazás értesítés vagy ellenőrző kód és a külső OAuth jogkivonatokat.

#### <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Biztonsági figyelést, a riasztások és a machine learning-alapú jelentések

A biztonság ellenőrzése és a riasztások és a machine learning-alapú jelentések, amelyek azonosítják az inkonzisztens hozzáférési mintázatokat segítségével védelmet az üzleti. Azure Active Directory hozzáférési és használati jelentések segítségével hogy lássák az integritásra és a munkahely címtárában biztonságát. Ezt az információt a directory-rendszergazda is jobban meghatározhatja, ahol lehetséges biztonsági kockázatokat a vizsgálandó, hogy azok megfelelően megtervezheti kockázatok csökkentésének lehetőségeit.

Az Azure portálon vagy a a [Azure Active Directory portálon](http://aad.portal.azure.com/), [jelentések](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) szerint vannak kategóriába sorolva a következőképpen:

- Az anomáliadetektálási jelentések – eseményeket, amelyek jelenleg található a rendellenes bejelentkezési tartalmaznak. Célunk, ellenőrizze, hogy tisztában legyen ilyen tevékenység, és lehetővé teszik a arról, hogy az esemény gyanúsnak eldöntése.

- Integrált alkalmazás jelentések – betekintést, hogyan használja a szervezet a felhőalapú alkalmazásokhoz. Az Azure Active Directory integrálható a felhőalapú alkalmazások ezer.

- Hibajelentések – azt jelzi, hogy a külső alkalmazásokba fiókok létesítésekor előforduló hibákat.

- Felhasználó-specifikus jelentései – eszköz/sign tevékenységek adatai egy adott felhasználó jelenítenek meg.

- Tevékenység – tartalmaznak minden naplózott eseményeket rögzíti a elmúlt 24 óra, a legutóbbi 7 nap, vagy a utolsó 30 nap során, és a csoport tevékenység módosításainak és a jelszó alaphelyzetbe állítása és nyilvántartási tevékenység belül.

#### <a name="consumer-identity-and-access-management"></a>A felhasználói identitások és hozzáférés kezelése

[Az Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) egy magas rendelkezésre állású, globális, identitás szolgáltatás felhasználók felé néző alkalmazásokhoz, száz millió identitások alkalmazkodnak. Mobil- és webes platformokba is integrálható. A felhasználók testreszabható felületeken, meglévő közösségi hálózati fiókjaikkal vagy új hitelesítő adatok létrehozásával jelentkezhetnek be minden alkalmazásába.

A korábbi, alkalmazásfejlesztők számára a [és a felhasználók bejelentkezését](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview) alkalmazásokba integrálhassák volna írt saját kódot. Ennek keretében általában helyszíni adatbázisokat vagy rendszereket használtak a felhasználónevek és jelszavak tárolására. Az Azure Active Directory B2C integrálniuk a felhasználói Identitáskezelés alkalmazások segítségével biztonságos, szabványokon alapuló platformja és bővíthető szabályzatainak számos fejlettebb módszert kínál a szervezete.

Azure Active Directory B2C használata esetén a felhasználók regisztrálhatnak az alkalmazások új hitelesítő adatok (e-mail címet és jelszót, vagy felhasználónév és jelszó) létrehozásával vagy meglévő közösségi fiókjaik használatával (Facebook, Google, Amazon, LinkedIn).

#### <a name="device-registration"></a>Eszközregisztráció

[Az Azure AD eszközregisztrációval](https://docs.microsoft.com/azure/active-directory/device-management-introduction) az alapja eszközalapú [feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/active-directory-device-registration-on-premises-setup) forgatókönyvek. Amikor regisztrál egy eszközt, az Azure AD eszközregisztrációval látja el az eszközt, amikor a felhasználó bejelentkezik az eszköz hitelesítésére használt identitással. A hitelesített eszköz és az eszköz attribútumai ezután a feltételes hozzáférési házirendek betartatásához használhatók a felhőben és a helyszínen tárolt alkalmazások esetében.

Együtt egy [mobileszköz-kezelés (MDM)](https://www.microsoft.com/itshowcase/Article/Content/588/Mobile-device-management-at-Microsoft) megoldással, például az Intune-ban, az eszközattribútumokon az Azure Active Directoryban frissítődik az eszközzel kapcsolatos további információk. Ez lehetővé teszi további feltételes hozzáférési szabályok létrehozását, amelyek arra kényszerítik az eszközhozzáféréseket, hogy megfeleljenek a biztonsági és megfelelőségi szabványoknak.

#### <a name="privileged-identity-management"></a>Privileged identity management

[Az Azure Active Directory (AD) Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) segítségével kezelheti, szabályozhatja, és az emelt szintű identitások figyeléséhez, valamint elérhető erőforrások az Azure ad-ben, valamint egyéb Microsoft online szolgáltatások, például az Office 365-öt vagy a Microsoft Intune.

Néha felhasználók el kell végezniük a jogosultságokhoz kötött műveletek Azure vagy az Office 365 erőforrásokat, vagy más SaaS-alkalmazásokhoz. Ez gyakran azt jelenti, hogy a szervezetek meg kell adni nekik az Azure AD állandó privilegizált hozzáférési jogosultsága. Ez a felhőben üzemeltetett erőforrásokhoz az egyre növekvő biztonsági kockázatot jelent, mert a szervezeteknek elég nem tud figyelni, ezek a felhasználók tevékenységeit a rendszergazda jogosultságokkal. Továbbá ha jogosultsági szintű hozzáféréssel rendelkező felhasználói fiók biztonsága sérül, egy megsértésének jelentős hatással lehet a felhő átfogó biztonsági. Az Azure AD Privileged Identity Management segít a kockázat megoldásához.

Az Azure AD Privileged Identity Management lehetővé teszi:

- Mely felhasználók vannak-e az Azure AD-rendszergazdák

- Engedélyezze az igény, "igény szerint" a Microsoft Online Services rendszergazdai hozzáféréssel, például Office 365 és az Intune-ban

- Rendszergazda-hozzárendelések beolvasása a rendszergazdai hozzáférési műveleteiről és a változások

- Egy kiemelt szerepkörhöz való hozzáféréssel kapcsolatos riasztásokat kaphat

#### <a name="identity-protection"></a>Identity Protection

[Az Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) biztonsági szolgáltatás, amely a kockázati eseményekről és a szervezet identitásait érintő lehetséges biztonsági rések egyesített nézetét biztosítja. Azonosító adatok védelmét a meglévő Azure Active Directory anomáliadetektálási az észlelési képességek (az Azure AD rendellenes Tevékenységjelentések keresztül érhető el) használ, és vezet be az új kockázat típusait, amely valós idejű rendellenességek észlelését.

## <a name="secured-resource-access-in-azure"></a>Védett erőforrások elérése az Azure-ban

Hozzáférés-vezérlés az Azure-ban elindul egy számlázási szempontjából. Azure-fiók esetén érhető el, látogasson el a tulajdonosa a [Azure Account Center](https://account.windowsazure.com/subscriptions), a fiók rendszergazdai AA van. Előfizetések számlázási tárolója, de úgy is működnek, mint a biztonsági határ: minden előfizetés rendelkezik a szolgáltatás rendszergazdai (SA) akik hozzáadása, eltávolítása és módosíthatja az adott előfizetés Azure-erőforrások az Azure portál használatával. Az alapértelmezett biztonsági Társítás az új előfizetés az AA, de az AA módosíthatja a biztonsági Társítás az az Azure Account Center.

![Védett erőforrások elérése az Azure-ban](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig3.png)

Előfizetések is könyvtár van társítva. A directory egy felhasználók csoportját határozza meg. Ezek lehetnek a felhasználók a munkahelyi vagy iskolai, hozott létre a könyvtár, vagy külső felhasználók számára (Ez azt jelenti, hogy a Microsoft Accounts) is lehetnek. Az előfizetések által hozzárendelt szolgáltatás-rendszergazdai (SA) vagy Társadminisztrátorának (CA); directory felhasználók egy részhalmazát érhetők el az egyetlen kivétel, hogy az örökölt összetevők miatt, Microsoft Accounts (korábbi nevén Windows Live ID Azonosítóval) rendelhető rendszergazdai (SA) vagy a CA anélkül, hogy a címtárban található.

A vállalat biztonsági célú van szükségük a pontos engedélyeket ad az alkalmazottak kell összpontosítania. Túl sok engedélyeket is elérhetővé teheti a támadásokkal fiókkal. Túl kevés engedélyek jelenti azt, hogy az alkalmazottak nem munkavégzéséhez hatékony. [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) nyújt segítséget nyújtó részletes hozzáféréskezelést az Azure által oldja meg a problémát.

![Védett erőforrásokhoz való hozzáférést ](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig4.png)

Az RBAC használata, feladatokat elkülönítse a munkacsoporton belül, és csak olyan mértékű hozzáférést biztosítania a felhasználóknak a feladataik elvégzéséhez szükséges. Jogosultságot ad a Mindenki helyett korlátozás engedélyek az Ön Azure-előfizetés vagy erőforrásokhoz, engedélyezheti a csak bizonyos műveleteket. Például, hogy egy előfizetésben található virtuális gépek kezeléséhez, miközben egy másik kezelheti az SQL-adatbázisok egyazon előfizetésen belül egy alkalmazott RBAC használatát.

![Védett erőforrásokhoz való hozzáférést az Azure (RBAC)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig5.png)

## <a name="azure-data-security-and-encryption-protect"></a>Az Azure data biztonsági és a titkosítás (védelem)

A kulcsokat a felhőben az adatvédelem egyik elszámolása van a lehetséges állapotok az adatok akkor fordulhat elő, és milyen vezérlők érhetők el az adott állapotban. Az Azure data biztonsági és a titkosítás gyakorlati tanácsok az ajánlások kell a következő adatok állapotok körül.

- Nyugalmi: Ez magában foglalja a tárolási objektum, a tárolók és a fizikai adathordozó statikusan létező típusok kell azt mágneses vagy optikai lemez összes információt.

- Az átvitel közbeni: Ha adatátvitel közötti összetevők, a helyek vagy a programok, többek között a hálózaton keresztül egy service bus (a felhőbe helyszíni és fordítva, beleértve a hibrid kapcsolatok, például az ExpressRoute), vagy egy bemeneti/kimeneti folyamat során, akkor-re, hogy a mozgásérzékelési.

### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban

Elérése titkosítását, tegye a következők mindegyikét:

Támogatja az adatok titkosítása az alábbi táblázatban részletes ajánlott titkosítási modellek legalább egyike.

| Titkosítási modellek |  |  |  |
| ----------------  | ----------------- | ----------------- | --------------- |
| Server Encryption | Server Encryption | Server Encryption | Ügyfél-titkosítás
| Kiszolgálóoldali titkosítás segítségével felügyelt kulcsai | Kiszolgálóoldali titkosítás Customer-Managed kulcsok Azure Key Vault használatával | Kiszolgálóoldali titkosítás a helyszínen felügyelt ügyfél kulcsok használata |
| • Az azure erőforrás-szolgáltatók titkosítási és visszafejtési műveletek végrehajtása <br> • A Microsoft kezeli a kulcsokat <br>• Teljes felhő funkció | • Az azure erőforrás-szolgáltatók titkosítási és visszafejtési műveletek végrehajtása<br>• Felhasználói vezérlők kulcsok Azure Key Vault keresztül<br>• Teljes felhő funkció | • Az azure erőforrás-szolgáltatók titkosítási és visszafejtési műveletek végrehajtása <br>• Felhasználói vezérlők helyszíni kulcsok <br> • Teljes felhő funkció| • Azure-szolgáltatások visszafejtett adatait nem látja. <br>• Ügyfelek megőrizheti a kulcsokat a helyi (vagy más biztonságos tárolók). Kulcsok nem érhetők el az Azure-szolgáltatások <br>• Csökkenteni felhő funkció|

### <a name="enabling-encryption-at-rest"></a>Inaktív titkosítás engedélyezése

**Az összes hely a tároló adatok azonosítása**

A titkosítási aktívan célja összes adatot titkosítják. Ezzel megszünteti a hiányzó fontos adatokat, vagy az összes megőrzött helyét lehetőségét. Az alkalmazás által tárolt összes adat számbavétele. 

> [!Note] 
> Nem csak "alkalmazásadatok" vagy "PII", de bármely alkalmazás beleértve a vonatkozó adatok fiók metaadatok (előfizetés hozzárendelések, szerződésadatok, PII).

Fontolja meg, milyen adatok tárolására használ tárolókat. Példa:

- A külső tárhelyen (például az SQL Azure Document DB rendszerbe, HDInsights, Data Lake, stb.)

- Ideiglenes tárhely (bármely helyi gyorsítótár, amely tartalmazza a bérlő adatok)

- A memória gyorsítótárának (léptethetnek be az oldal fájlja.)

### <a name="leverage-the-existing-encryption-at-rest-support-in-azure"></a>Kihasználhatja a meglévő titkosítási rest terméktámogatási az Azure-ban

A különféle áruházakból használja használja a többi terméktámogatási meglévő titkosítási ki.

- Az Azure Storage: Lásd: [az inaktív adatok Azure Storage szolgáltatás titkosítási](https://docs.microsoft.com/azure/storage/storage-service-encryption),

- Az SQL Azure: Lásd: [átlátható adattitkosítás (TDE) mindig titkosítja SQL](https://msdn.microsoft.com/library/mt163865.aspx)

- Virtuális gép & helyi lemez tárolási ([Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption))

A virtuális gép és a helyi lemez tárolásához használja az Azure Disk Encryption ahol támogatott:

#### <a name="iaas"></a>IaaS

A szolgáltatásokat, az infrastruktúra-szolgáltatási virtuális gép (Windows vagy Linux) kell használnia [Azure Disk Encryption](https://microsoft.sharepoint.com/teams/AzureSecurityCompliance/Security/SitePages/Azure%20Disk%20Encryption.aspx) titkosíthatja a felhasználói adatokat tartalmazó kötetet.

#### <a name="paas-v2"></a>PaaS v2

Futó szolgáltatások a PaaS v2 a Service Fabric használatával Azure disk encryption a virtuálisgép-méretezési csoport [VMSS] az PaaS v2 virtuális gép titkosítására.

#### <a name="paas-v1"></a>A PaaS 1-es verzió

Az Azure Disk Encryption jelenleg nem támogatott a PaaS 1-es verzió. Ezért aktívan megőrzött adatok titkosítása alkalmazás a blokkszintű titkosítás kell használnia.  Ez magában foglalja, de nem korlátozódik az alkalmazásadatok, ideiglenes fájlok, naplók és összeomlási memóriaképeket.

A legtöbb szolgáltatások megkíséreljék-e használni a titkosítást egy tárolási erőforrás-szolgáltató. Egyes szolgáltatások kell explicit titkosítási, például a megőrzött kulcsokat tárol (tanúsítványok, a legfelső szintű / fő kulcsok) Key Vault kell tárolni.

Ha támogatja a kiszolgálóoldali titkosítást ügyfél által felügyelt kulcsokkal kell adni a kulcs eléréséhez, az ügyfél úgy. A támogatott és ajánlott módja integrálja az Azure Key Vault (AKV). Ebben az esetben az ügyfelek hozzáadása és kezelése az Azure Key Vault a kulcsok. Az ügyfél megtudhatja, hogyan használja az AKV keresztül [Ismerkedés a Key Vault](http://go.microsoft.com/fwlink/?linkid=521402).

Integrálása az Azure Key Vault, hozzá kellene AKV visszafejtéshez szükség esetén egy kulcs kérhet kódot.

- Lásd: [Azure Key Vault – részletes](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step/) az AKV integrációjával kapcsolatos információk.

Ha támogatja az ügyfél által felügyelt kulcsokat, meg kell adni egy UX mely Key Vault (vagy kulcsot tároló URI) használatához adja meg az ügyfél.

Állomás, az infrastrukturális és bérlői adatokat, a rendszer hibája miatt a kulcsok elvesztését titkosítási titkosításnál aktívan vagy rosszindulatú tevékenységhez azt is jelentheti, hogy a titkosított adatok nem vesztek. Éppen ezért fontos, hogy rendelkezik-e a titkosítás Rest megoldást egy átfogó vész helyreállítási szövegegység rugalmas rendszerhibák és a rosszindulatú tevékenységhez.

Szolgáltatások, amelyek megvalósítják az aktívan titkosítása általában továbbra is ki vannak téve a titkosítási kulcsok vagy hagyja adatok titkosítatlanul megtalálhatóak a gazdameghajtó (például a a gazda operációs rendszer oldal fájlja.) Ezért szolgáltatások kell ellenőrizze, hogy a gazdakötet szolgáltatásaik titkosítva van. Lehetővé teszi a számítási team engedélyezte az állomás titkosítási, amely használja a központi telepítés [Bitlocker](https://technet.microsoft.com/library/dn306081.aspx) NKP és a bővítmények számára, a DCM-szolgáltatás és az ügynök a gazdakötet titkosításához.

A legtöbb szolgáltatások szabványos Azure virtuális gépeken van megvalósítva. Ezek a szolgáltatások kapja meg [állomás titkosítási](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) automatikusan amikor számítási lehetővé teszi. Tartozó számítási szolgáltatások felügyelt fürtöt állomás titkosítási automatikusan engedélyezve lesz, Windows Server 2016 lesz állítva.

### <a name="encryption-in-transit"></a>Titkosítás az átvitel

Az átvitel során az adatok védelme a data protection stratégia nagyon fontos részét kell lennie. Adatok átvitele oda-vissza pedig a több helyről, mert az általános ajánlás az, hogy mindig SSL/TLS protokollokat használ az exchange-adatok különböző helyek között. Bizonyos esetekben érdemes lehet különítheti el a teljes kommunikációs csatornát a helyszíni és a felhő közötti virtuális magánhálózati (VPN) segítségével infrastruktúra.

Az adatok áthelyezése a helyszíni infrastruktúra és az Azure között megfelelő védelmi funkciók, például a HTTPS- vagy VPN-érdemes lehet.

Használja a szervezet számára több található munkaállomások helyszíni Azure hozzáférést igénylő [Azure telephelyek közötti VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-site-to-site-create).

A szervezet számára, hogy a biztonságos hozzáférés a helyszíni található egy munkaállomás Azure kell használni [pont-pont VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-point-to-site-create).

Nagyobb, mint egy dedikált nagy sebességű WAN-kapcsolaton keresztül anélkül áthelyezhetők [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Ha ExpressRoute használatát választja, a alkalmazás szintű az adatok is titkosíthatók [SSL/TLS](https://support.microsoft.com/kb/257591) vagy egyéb protokollok hozzáadott védelemre.

Az Azure Storage az Azure-portálon való interakció, minden tranzakció történik meg HTTPS használatával. [Storage REST API felülete](https://msdn.microsoft.com/library/azure/dd179355.aspx) over HTTPS is használható, amellyel kommunikálni tud [Azure Storage](https://azure.microsoft.com/services/storage/) és [Azure SQL Database](https://azure.microsoft.com/services/sql-database/).

Adatok védelmére átvitel nem válaszoló szervezetek jobban ki vannak téve a rendszer [-átjárójának](https://technet.microsoft.com/library/gg195821.aspx), [lehallgatás](https://technet.microsoft.com/library/gg195641.aspx), és a munkamenet-eltérítés. Ilyen támadások lehet az első lépés a bizalmas adatok elérésekor.

További Azure VPN lehetőségekről a cikk olvasásával [tervezése és kialakítása VPN-átjáró](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

### <a name="enforce-file-level-data-encryption"></a>Fájl szintű adatok titkosításának kényszerítése

[Az Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) titkosítási, identitáskezelési és engedélyezési házirendeket használ a fájlok és e-mailek biztonságossá. Több eszközön működik az Azure RMS-telefonokon, táblagépeken és számítógépeken megvédi a szervezeten belül, mind a szervezeten kívülről. Ez a lehetőség azért lehetséges, mert az Azure RMS biztosítja, ami az adatok maradnak, még akkor is, ha elhagyják a szervezet területét.

Ha Azure RMS használatával a fájlok védelme, teljes körű támogatása szabványos kriptográfia használ [FIPS 140-2](http://csrc.nist.gov/groups/STM/cmvp/standards.html). A data protection is használja az Azure RMS, ha akkor is, ha nem az ellenőrzése alatt álló tárolók másolódik lehet, hogy a fájl védelme mindig fennmarad benne informatikai, például egy felhőalapú tárolási szolgáltatásba. Az egyetlen különbség a keresztül e-mailben megosztott fájlok a fájl védett e-mailt, utasításokkal mellékletként nyitni a védett mellékletet.
Azure RMS bevezetését tervezésekor a következőket javasoljuk:

- Telepítse a [RMS-megosztó alkalmazás](https://technet.microsoft.com/library/dn339006.aspx). Ez az alkalmazás integrálja az Office-alkalmazások telepítésekor egy Office-bővítmény, hogy a felhasználók egyszerűen védhetik a fájlok közvetlenül.

- Az Azure RMS-t támogató alkalmazások és szolgáltatások konfigurálása

- Hozzon létre [egyéni sablonok](https://technet.microsoft.com/library/dn642472.aspx) , amely az üzleti igényeknek megfelelően. Például: felső titkos adatok, az összes felső titkos alkalmazni kívánt sablont kapcsolódó e-maileket.

A szervezetek, amelyek a gyenge [adatbesorolást](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) és lehet, hogy a fájl védelem jobban ki vannak téve az adatok kiszivárgásának. Megfelelő fájl védelem nélkül a szervezet nem fogja tudni üzleti elemzések készítése beszerzése, figyelje a visszaélés, és hogy rosszindulatú férhessenek hozzá a fájlok.

> [!Note]
> További tudnivalók az Azure RMS által a cikk elolvasása [Ismerkedés az Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx).

## <a name="secure-your-application-protect"></a>Az alkalmazás biztonságos (védelem)
Az infrastruktúra és a platform, amely az alkalmazás fut, a történő biztonságba helyezéséért felelős Azure pedig feladata a biztonságos magának az alkalmazásnak. Más szóval kell történő fejlesztéséhez, telepítheti és kezelheti az alkalmazás kódjában és a tartalom biztonságos módon. E nélkül az alkalmazás kódja vagy a tartalom továbbra is történhet fenyegetések kitéve.

### <a name="web-application-firewall-waf"></a>Webalkalmazási tűzfal (WAF)
[Webalkalmazási tűzfal (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) szolgáltatása [Alkalmazásátjáró](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) , amely a webalkalmazások a gyakori biztonsági réseket és biztonsági rések központosított védelmet biztosít.

A webalkalmazási tűzfal az [alapvető OWASP-szabálykészletek](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0-s vagy 2.2.9-es verzióinak szabályai alapján működik. A webalkalmazások egyre inkább ki vannak téve rosszindulatú támadásoknak, amelyek az ismert biztonsági réseket használják ki. Az ilyen jellegű támadások között például gyakoriak az SQL-injektálásos és a webhelyek közötti, parancsprogramot alkalmazó támadások. Az ilyen támadások megakadályozása az alkalmazás kódjában kihívást jelenthet, és szigorú felügyeletet, javítást és megfigyelést igényelhet az alkalmazás topológiájának több rétegén. A központosított webalkalmazási tűzfal egyszerűbbé teszi a biztonságfelügyeletet, és segít az alkalmazás-rendszergazdáknak a fenyegetések vagy a behatolások elleni védekezésben. Emellett a WAF-megoldás gyorsabban képes kezelni a biztonsági fenyegetéseket azáltal, hogy kijavítja az ismert biztonsági réseket egy központi helyen, ahelyett hogy az egyes webalkalmazások védelmét biztosítaná. A meglévő alkalmazásátjárókat egyszerűen át lehet alakítani webalkalmazási tűzfallal rendelkező alkalmazásátjárókká.

A webalkalmazási tűzfal többek között a következő gyakori internetes biztonsági rések ellen nyújt védelmet:

- SQL-injektálás elleni védelem

- Webhelyek közötti, parancsprogramot alkalmazó támadások elleni védelem

- Gyakori webes támadások (például parancsinjektálás, HTTP-kéréscsempészet, HTTP-válaszfelosztás és távolifájl-beszúrásos támadás) elleni védelem

- HTTP protokoll megsértése elleni védelem

- HTTP protokollanomáliák (például hiányzó gazdagép-felhasználói ügynök és Accept (Elfogadás) fejlécek) elleni védelem

- Robotprogramok, webbejárók és képolvasók elleni védelem

- A gyakori alkalmazás konfigurációs hibák (Ez azt jelenti, hogy Apache, az IIS, stb.) észlelése

> [!Note]
> A szabályok részletes listáját és azok védelmét lásd a következő [szabálykészletek alapvető](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview#core-rule-sets):

Azure emellett több könnyen használható szolgáltatásokat biztosítja az alkalmazás a bejövő és kimenő forgalmának biztonságossá tétele érdekében. Az Azure is az ügyfelek az alkalmazás kódjában biztonságos külsőleg biztosításával segíti a funkciókat biztosította a webes alkalmazás a biztonsági réseket vizsgálata.

- [Az alkalmazás Azure Active Directory-hitelesítés beállítása](https://azure.microsoft.com/blog/azure-websites-authentication-authorization/)

- [Transport Layer Security (TLS/SSL) - HTTPS engedélyezése az alkalmazás forgalmának biztonságossá tétele](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl)

  - [Minden bejövő forgalom kényszerítheti a HTTPS-kapcsolaton keresztül](http://microsoftazurewebsitescheatsheet.info/)

  - [Szigorú a Transport Security (HSTS) engedélyezése](http://microsoftazurewebsitescheatsheet.info/#enable-http-strict-transport-security-hsts)

- [Az alkalmazáshoz való hozzáférés korlátozása a ügyfél IP-cím](http://microsoftazurewebsitescheatsheet.info/#filtering-traffic-by-ip)

- [Az alkalmazás ügyfél viselkedése - kérelem gyakoriságának és az egyidejű hozzáférés korlátozása](http://microsoftazurewebsitescheatsheet.info/#dynamic-ip-restrictions)

- [A webes alkalmazás kódja a Tinfoil Security Scanning eszköz használatával biztonsági réseket vizsgálata](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)

- [A webalkalmazás csatlakozni ügyféltanúsítványok megköveteléséhez kölcsönös hitelesítést TLS konfigurálása](https://docs.microsoft.com/azure/app-service/app-service-web-configure-tls-mutual-auth)

- [Az alkalmazásnak, hogy biztonságosan kapcsolódjanak a külső erőforrások használatra ügyfél tanúsítvány konfigurálása](https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/)

- [Általános jogú kiszolgálói fejlécek elkerülése érdekében az alkalmazás-ujjlenyomat-eszközök eltávolítása](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

- [Az alkalmazás biztonságos kapcsolódni egy pont – hely típusú VPN használatával magánhálózati erőforrásokat](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)

- [Az alkalmazás biztonságos kapcsolódni az erőforrásokkal egy magánhálózaton hibrid kapcsolatok használata](https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections)

Az Azure App Service ugyanaz a kártevővédelmi megoldás Azure Cloud Services és a virtuális gépek által használt használja. Ismerje meg, ez bővebben lásd a [kártevőirtó dokumentáció](https://docs.microsoft.com/azure/security/azure-security-antimalware).

## <a name="secure-your-network-protect"></a>A hálózat biztonságáról (védelem)
A Microsoft Azure tartalmazza az alkalmazás és szolgáltatás hálózati kapcsolati követelményeinek támogatására robusztus hálózati infrastruktúrát. Helyszíni között, az Azure-ban lévő erőforrások közötti hálózati kapcsolatot, és Azure üzemeltetett erőforrásokhoz, és hogy, és az internetről és az Azure.

A [Azure hálózati infrastruktúra](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines) lehetővé teszi a biztonságos kapcsolódás egymáshoz az Azure-erőforrások [virtuális hálózatokról (Vnetekről)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). A virtuális hálózat a felhőben saját hálózati ábrázolása. A virtuális hálózat a hálózat az előfizetésre kijelölt Azure-felhőbe hálózati logikai elkülönítése. A helyszíni hálózatokhoz Vnetek csatlakozhatnak.

![A hálózat biztonságáról (védelem)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig6.png)

Ha szüksége alapvető hálózati szintű hozzáférés-vezérlés (az IP-cím és a TCP vagy UDP protokoll alapján), akkor is használhatja [hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg). A hálózati biztonsági csoport (NSG) egy tűzfal alapszintű csomag, és lehetővé teszi a alapuló hozzáférés-vezérlésének egy [5 rekordos](https://www.techopedia.com/definition/28190/5-tuple).

Azure hálózatkezelés támogatja a hálózati forgalmat az Azure virtuális hálózat az útválasztási viselkedés testreszabása. Ehhez konfigurálásával [felhaszn útvonalak](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) az Azure-ban.

[A kényszerített bújtatás](https://www.petri.com/azure-forced-tunneling) egy mechanizmus segítségével győződjön meg arról, hogy a szolgáltatások használata nem engedélyezett a kezdeményezzen kapcsolatot az internethez csatlakozó eszközökről.

Az Azure által támogatott dedikált WAN-kapcsolaton kapcsolat a helyszíni hálózat és az Azure virtuális hálózat [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction). A hely és az Azure közötti kapcsolat, amely nem halad át a nyilvános internethez dedikált kapcsolatot használ. Ha több adatközpontot az Azure alkalmazás fut, akkor használhatja [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) a felhasználók ezután több példányát kérelmek. Is irányíthatja a forgalmat a szolgáltatás nem fut az Azure-ban, ha azok elérhetők az internetről.

## <a name="virtual-machine-security-protect"></a>Virtuális gép biztonsági (védelem)

[Az Azure virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/) lehetővé teszi, hogy számos számítógép-használatról megoldások egy gyors módja a telepítése. Számítási feladatait a Windows-, Linux-, SQL Server-, Oracle-, IBM-, SAP- és Azure BizTalk Services-támogatás révén mérettől és programnyelvtől függetlenül, szinte bármely operációs rendszerben üzembe helyezheti.

Használhatja az Azure- [kártevőirtó szoftver](https://docs.microsoft.com/azure/security/azure-security-antimalware) biztonsági szállítók, például Microsoft Symantec, Trend Micro vagy Kaspersky a virtuális gépek védelmét a rosszindulatú fájlok, hirdetéseket és más fenyegetésekkel szemben.

Azure Cloud Services és a virtuális gépek Microsoft Antimalware a valós idejű védelem képessége, amelyik segít azonosításához és eltávolításához a vírusok, kémprogramok és más, kártevő szoftverek. A Microsoft Antimalware biztosít konfigurálható riasztást küld, ha ismert kártevő vagy nemkívánatos szoftverek próbálják telepíteni magukat az Azure rendszeren.

[Azure biztonsági mentés](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) van méretezhető megoldás, amely megvédi az alkalmazásadatok nulla tőkebefektetés szükségessége, és minimális üzemeltetési költségek. Az alkalmazáshibák adatai sérülését okozhatják, az emberi hibák pedig az alkalmazások meghibásodásához vezethetnek. Az Azure Backup szolgáltatással a Windows és Linux rendszerű virtuális gépek védelmének.

[Az Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) segít replikáció, feladatátvétel és helyreállítás munkaterhelések és alkalmazások téve, hogy azok elérhetők a másodlagos helyről, ha az elsődleges hely leáll.

## <a name="ensure-compliance-cloud-services-due-diligence-checklist-protect"></a>A megfelelőség biztosítása: a felhőalapú szolgáltatások megfelelő gondossággal ellenőrzőlista (védelem)

Microsoft fejlesztett [a felhőalapú szolgáltatások esedékes gondossággal ellenőrzőlista](https://aka.ms/cloudchecklist.download) gyakorolja szervezetek számára megfelelő gondossággal szerint egy helyezze át a felhőbe. Szerkezetet biztosít bármely méretét, és írja be a szervezet – titkos üzleteknek és nyilvános szektorokkal rendelkező szervezeteknek, többek között a szintek és a nonprofit kormányzati – saját teljesítmény, a szolgáltatás, a adatkezelés, és a cégirányítási célkitűzések és a követelmények azonosításához. Ez lehetővé teszi, hogy azokat a végső soron a egy felhőalapú szolgáltatási szerződés alapját képező különböző felhőalapú szolgáltatók által kínált összehasonlítani.

A feladatlista keretrendszerében záradék-által-záradékot egy új nemzetközi szabványa felhőalapú szolgáltatási szerződések, ISO/IEC 19086 igazodik. Ezzel a szabvánnyal szempontok a szervezetek számára, hogy segítséget nyújthatnak megalapozott döntéseket felhő bevezetését, és hozzon létre egy közös ground a felhő szolgáltatásajánlatok összehasonlítása a látványelemek kínál.

A feladatlista elősegíti a alaposan vetted helyezze át a felhőben, így strukturált útmutatást és egy egységes, ismételhető megközelítést felhő szolgáltatót.

Felhő elfogadása most már egyszerűen technológia döntést hoznak. Ellenőrzőlista követelmények minden szempontját szervezet érintik, mert azok osztja ki az összes fő belső – döntéshozók hívja össze – a CIO adatvédelmi felelős, valamint jogi, kezelési, a beszerzési és a megfelelőségi szakemberek kockázatát. Ez növeli a döntéshozatali folyamat és a hang indoklást, ezáltal csökkenti a előre nem látható roadblocks valószínűségét tényezői ground szempontjait hatékonyságát.

Emellett az Ellenőrzőlista:

- Elérhetővé teszi a fő ismertető témakörök a döntéshozók a felhő elfogadási folyamat elején.

- Alapos üzleti beszélgetéseket szabályozások és a szervezet saját célok támogatja az adatok biztonsági, adatvédelmi és személyes azonosításra alkalmas adatokat (PII).

- Segítségével a szervezetek azonosítani a potenciális problémákat, amely hatással lehet egy felhőalapú projektet.

- A konzisztens kérdésekre, az azonos feltételeket, a definíciók, a metrikák és a termékek esetében az egyes szolgáltatók számára a különböző felhőszolgáltatók ajánlatok összehasonlítása egyszerűbbé biztosít.

## <a name="azure-infrastructure-and-application-security-validation-detect"></a>Azure infrastruktúra és az alkalmazás biztonsági ellenőrzés (észlelni)

[Az Azure Operational biztonsági](https://docs.microsoft.com/azure/security/azure-operational-security) védelmére az adatok, alkalmazások és egyéb eszközök a Microsoft Azure-ban a szolgáltatások, a vezérlők és a felhasználók számára elérhető szolgáltatások hivatkozik.

![biztonsági érvényesítési (észlelni)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig7.png)

Az Azure Operational biztonsági egy keretrendszer, amely magában foglalja a tapasztalatok teszik ki egy egyedi, a Microsoftnak, beleértve a Microsoft biztonsági fejlesztési életciklus (SDL), a Microsoft biztonsági válasz Center program és részletes tájékoztatást nyújthatnak a számítógépes veszélyforrásainak tükrében megfigyelhető a különböző képességeket keresztül épül.

### <a name="microsoft-operations-management-suiteoms"></a>A Microsoft operations management suite(OMS)

[A Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) a hibrid felhőalapú informatikai felügyeleti megoldás. Önmagában vagy a meglévő System Center telepítési kiterjesztéséhez OMS lehetővé teszi a maximális rugalmasságot és a felhő alapú felügyeleti infrastruktúra.

![A Microsoft operations management suite(OMS)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig8.png)

Az OMS-ben a felhő, beleértve a helyszíni, Azure, AWS, Windows Server, Linux, VMware és OpenStack, mint versenyképes megoldások alacsonyabb költségekkel szereplő bármely példány kezelheti. A felhő-első globális készült, OMS biztosít egy új megközelítés a vállalat, amely a lehető leggyorsabb és legköltséghatékonyabb módon új üzleti kihívást és olyan új munkaterhelések, alkalmazások és a felhő környezeteiben.

### <a name="log-analytics"></a>Log Analytics

A [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) figyelési szolgáltatásokat biztosít az OMS számára a felügyelt erőforrások adatainak egy központi tárházba gyűjtésével. Ezek az adatok lehetnek események, teljesítményadatok vagy az API segítségével biztosított egyéni adatok. Az összegyűjtésüket követően az adatok használhatók riasztáshoz, elemzéshez vagy exportáláshoz.

![Log Analytics](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig9.png)

Ez a módszer lehetővé teszi a különböző forrásokból származó adatokat, így kombinálhatja az Azure-szolgáltatások és a meglévő adatokat a helyszíni környezet. Továbbá egyértelműen elkülöníti az adatok gyűjtését az adatokon végzett műveletektől, így az összes művelet végrehajtható a különféle adatokon.

### <a name="azure-security-center"></a>Azure Security Center

[Az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) az Azure-erőforrások biztonsági felügyeletének átláthatóbbá és szabályozhatóbbá tételével megkönnyíti a fenyegetések megelőzését, észlelését és elhárítását. Az ügyfél összes előfizetésére kiterjedő, integrált biztonsági monitorozást és szabályzatkezelést biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását, és számos biztonsági megoldással együttműködik.

A Security Center a potenciális biztonsági hiányosságok azonosítása érdekében elemzi az Azure-erőforrások biztonsági állapotát. A javaslatok listája végigvezeti Önt a szükséges szabályozási folyamatok konfigurálásának folyamatán.

Példák erre vonatkozóan:

- Kártevőirtók kiépítése a kártékony szoftverek azonosításához és eltávolításához

- Hálózati biztonsági csoportok és a virtuális gépek forgalmának ellenőrzésére vonatkozó szabályok konfigurálása

- Webalkalmazási tűzfalak kiépítése a webalkalmazások ellen irányuló támadások elleni védelemre

- Hiányzó rendszerfrissítések telepítése

- Az operációs rendszer azon konfigurációinak kezelése, amelyek nem felelnek meg a javasolt alapkonfigurációknak

A Security Center automatikusan gyűjti, elemzi és integrálja az Azure-erőforrások, a hálózat és az olyan partnermegoldások, mint például a kártevőirtó-programok és a tűzfalak naplóadatait. Fenyegetések észlelése esetén a központ biztonsági riasztást hoz létre. Példák fenyegetés észlelésére:

- Feltört virtuális gépek kommunikál az ismert kártékony IP-címek

- Windows hibajelentés által észlelt speciális kártevő

- Virtuális gépek elleni találgatásos támadások ellen

- Az integrált kártevőirtó programok és a tűzfalak biztonsági riasztásai

### <a name="azure-monitor"></a>Az Azure-figyelő

[Az Azure figyelő](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) adott típusú erőforrás mutatókat biztosít információkat biztosít. A képi megjelenítés, lekérdezés, útválasztási, riasztások, automatikus méretezési, és kínál automation adatokhoz az Azure-infrastruktúra (tevékenységnapló) és minden egyes Azure-erőforrás (diagnosztikai naplók) a.

Sok áthelyezése alkotórészek összetettek a felhőalapú alkalmazásokhoz. Győződjön meg arról, hogy az alkalmazás marad be adatokat, és megfelelő állapotban fut figyelés nyújt. Emellett segít, hogy ki a lehetséges problémák stave és a múltbeli kiépítettektől eltérő hibakeresést.

![Az Azure figyelő](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig10.png) emellett segítségével figyelési adatok az alkalmazással kapcsolatos átfogó megismerésében. Ezt az információt nyújt segítséget az alkalmazások teljesítményének vagy karbantartási követelmények, vagy, amelyek egyébként kézi beavatkozás műveletek automatizálására.

A hálózati biztonsági naplózás létfontosságú a hálózati biztonsági rések észlelése és az IT-biztonsági és szabályozási irányítás modell betartását. Biztonsági csoport nézet a konfigurált hálózati biztonsági csoport és a vonatkozó biztonsági szabályokat, valamint a hatékony biztonsági szabályok kérheti le. A szabályok alkalmazása listáját azt is meghatározhatja a megnyitott portok és ss hálózati biztonsági rések.

### <a name="network-watcher"></a>Hálózati figyelő

[Hálózati figyelő](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher) regionális szolgáltatás, amely lehetővé teszi figyelése és diagnosztizálása feltételek hálózati szintű az, hogy és az Azure-ból. Hálózati diagnosztika és a képi megjelenítés eszközök is elérhetők a hálózati figyelőt segítenek megérteni, diagnosztizálása és információt kaphat a hálózathoz, az Azure-ban. A szolgáltatás része a csomagrögzítéssel, a következő ugrás, az IP-adatfolyam győződjön meg arról, biztonsági csoport megtekintése, NSG folyamata naplókat. Forgatókönyv szintű figyelési jeleníti meg egy végpontok közötti hálózati erőforrások ellentétben egyes hálózati erőforrás-figyelése.

### <a name="storage-analytics"></a>Storage Analytics

[Tárolási analitika](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) tárolhatja, amely tartalmazza az összevont tranzakció statisztikák és a kapacitás adatok tárolási szolgáltatás kérelmekkel kapcsolatos metrikákat. Tranzakciók jelentett, a API művelet szinten, valamint a tárolás szolgáltatás szintjén, és a kapacitás jelenti a tárolás szolgáltatás szintjén. Metrikai adatok használható tárolási szolgáltatás használati elemzése, eseményadatokat az által, a tárolás szolgáltatás ellen, valamint egy szolgáltatást használó alkalmazások teljesítményének javítása érdekében.

### <a name="application-insights"></a>Application Insights

[Az Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) egy bővíthető alkalmazásteljesítmény-felügyeleti (APM) szolgáltatás webfejlesztőknek, több platformon. Az élő webalkalmazásának figyelésére használhatja. Automatikusan felismeri a teljesítményanomáliákat. Ez magában foglalja a hatékony elemzőeszközök segítséget nyújt a problémák diagnosztizálásához és megérteni a felhasználók mit az alkalmazással. Úgy tervezték, hogy használatával folyamatosan javíthassa a teljesítményt és a használhatóságot. Sokféle platform alkalmazásaival együttműködik, ilyen többek között a .NET, a Node.js és a J2EE platform helyszíni vagy felhőbeli tárolás esetén is. Integrálható a devOps folyamat, és a csatlakozási pont is a különböző fejlesztői eszközök.

A szolgáltatás az alábbiakat figyeli:

- **Kérések sebessége, válaszidők és hibaarányok** – megtudhatja, hogy mely lapok, mely napszakokban a legnépszerűbbek, és hol találhatók a felhasználók. Megtekintheti, hogy mely lapok teljesítenek a legjobban. Ha több kérés esetén a válaszidők és a hibaarányok értéke megnő, valószínűleg erőforrás-gazdálkodási hibáról van szó.

- **Függőségi értékek, válaszidők és hibaarányok** – megtudhatja, hogy mely külső szolgáltatások okoznak lassulást.

- **Kivételek** - elemezheti az összesített statisztikák, vagy válasszon olyan specifikus példányai, és elemezze a veremkivonatot és a kapcsolódó kérések. A kiszolgálói és a böngészői kivételekről egyaránt készül jelentés.

- **Lapmegtekintések és betöltési teljesítmény** – a felhasználói böngészők jelentése alapján készül.

- **AJAX-hívások weblapokról** -sebességét, a válaszidőt és a hiba sebességét.

- **Felhasználó és a munkamenet számát.**

- Windows vagy Linux rendszerű kiszolgálói gépekről származó **teljesítményszámlálók**, például processzor-, memória- és hálózathasználat.

- Dockerből vagy Azure-ból származó **gazdadiagnosztika**.

- Alkalmazásból származó **nyomkövetési naplók diagnosztikája** – megállapíthatja a nyomkövetési események és a kérések korrelációját.

- **Egyéni események és metrikák** hogy írni magát az ügyfél vagy kiszolgáló kódot, nyomon követheti az üzleti eseményeket például értékesített cikkek, vagy nyert játékok.

Az alkalmazás infrastruktúrája általában számos összetevőből áll – például egy virtuális gépből, tárfiókból és virtuális hálózatból, vagy egy webalkalmazásból, adatbázisból, adatbázis-kiszolgálóból és harmadik féltől származó szolgáltatásokból. Ezeket az összetevőket nem külön entitásokként látja, hanem egyetlen entitás kapcsolódó és egymással összefüggő részeiként. Csoportként érdemes telepíteni, kezelni és megfigyelni őket. [Az Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) lehetővé teszi, hogy az alkalmazásában lévő erőforrásokat csoportként a megoldásban.

A megoldás összes erőforrását egyetlen, koordinált műveletben telepítheti, frissítheti vagy törölheti. A telepítéshez egy sablon használatos, amely különböző, például tesztelési, átmeneti és üzemi környezetben is képes működni. A Resource Manager biztonsági, naplózási és címkézési szolgáltatásokat biztosít, hogy segítsen az erőforrások kezelésében a telepítést követően.

**A Resource Manager használatának előnyei**

A Resource Manager számos előnyt kínál:

- A megoldás összes erőforrását egy csoportként telepítheti, felügyelheti és figyelheti meg az erőforrások különálló kezelése helyett.

- A megoldást ismételten telepítheti a fejlesztési életciklus során, és biztos lehet abban, hogy az erőforrások telepítése konzisztens lesz.

- Az infrastruktúrát szkriptek helyett deklaratív sablonok segítségével is kezelheti.

- Meghatározhatja az erőforrások, hogy azok a megfelelő sorrendben legyenek telepítve közti függőségeket.

- Hozzáférés-vezérlést alkalmazhat az összes szolgáltatásra az erőforráscsoportban, mivel a szerepköralapú hozzáférés-vezérlés (RBAC) natív módon integrálva van a felügyeleti platformba.

- Címkékkel láthatja el az erőforrásokat, így logikusan rendszerezhető az előfizetés összes erőforrása.

- Az azonos címkén osztozó erőforrások csoportjának költségeit megtekintve jól átláthatók a szervezet számlái.

> [!Note]
> A Resource Manager egy új módot kínál a megoldások telepítésére és kezelésére. Ha a korábbi telepítési modellt, és szeretné a változásokról, lásd: [Understanding Resource Manager üzembe helyezési és a klasszikus üzembe helyezési](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

## <a name="next-steps"></a>További lépések

Többet szeretne tudni biztonsági ehhez beolvassa a részletes biztonsági témakörök:

- [Audit és naplózás](https://www.microsoft.com/en-us/trustcenter/security/auditingandlogging)

- [Kiberbűnözés mértéke](https://www.microsoft.com/en-us/trustcenter/security/cybercrime)

- [Tervezési és a működési biztonság](https://www.microsoft.com/en-us/trustcenter/security/designopsecurity)

- [Titkosítás](https://www.microsoft.com/en-us/trustcenter/security/encryption)

- [Identitás és hozzáférés-kezelés](https://www.microsoft.com/en-us/trustcenter/security/identity)

- [Hálózati biztonság](https://www.microsoft.com/en-us/trustcenter/security/networksecurity)

- [Fenyegetések kezelése](https://www.microsoft.com/en-us/trustcenter/security/threatmanagement)
