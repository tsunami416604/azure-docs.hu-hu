---
title: Biztonsági technikai képességek az Azure-ban – Microsoft Azure
description: Bevezetés az Azure biztonsági szolgáltatásaiba, amelyek segítenek az adatok, az erőforrások és az alkalmazások védelmében a felhőben.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2019
ms.author: TomSh
ms.openlocfilehash: 61afad1d9994fd703bd8df047d1861baddeae997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845344"
---
# <a name="azure-security-technical-capabilities"></a>Az Azure biztonságtechnikai képességei
Ez a cikk bemutatja az Azure biztonsági szolgáltatásait, amelyek segítenek megvédeni az adatokat, az erőforrásokat és az alkalmazásokat a felhőben, és megfelelnek a vállalkozás biztonsági igényeinek.

## <a name="azure-platform"></a>Azure-platform

[A Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) egy infrastruktúra- és alkalmazásszolgáltatásokból álló felhőplatform, amely integrált adatszolgáltatásokat és fejlett elemzéseket, valamint fejlesztői eszközöket és szolgáltatásokat tartalmaz, amelyeket a Microsoft nyilvános felhőalapú adatközpontjaiban üzemeltetnek. Az ügyfelek az Azure-t számos különböző kapacitáshoz és forgatókönyvhöz használják, az alapvető számítási, hálózati és tárolási lehetőségektől kezdve a mobil- és webalkalmazás-szolgáltatásokig, a teljes felhőalapú forgatókönyvekig, például a tárgyak internetén, és nyílt forráskódú technológiákkal használhatók, és hibrid felhőként telepíthetők. vagy az ügyfél adatközpontjában tárolják. Az Azure felhőalapú technológiát biztosít építőelemekként, hogy a vállalatok megtakaríthassák a költségeket, gyorsan újíthassanak, és proaktív módon kezelhesd a rendszereket. Amikor informatikai eszközökre épít, vagy informatikai eszközöket telepít át egy felhőszolgáltatóba, az adott szervezet azon képességére támaszkodik, amely az alkalmazások és az adatok védelmére szolgál a felhőalapú eszközök biztonságának és az általuk biztosított vezérlőknek köszönhetően.

A Microsoft Azure az egyetlen olyan felhőalapú számítástechnikai szolgáltató, amely biztonságos, konzisztens alkalmazásplatformot és infrastruktúra-szolgáltatásként kínál a csapatok számára, hogy a különböző felhőalapú képességeken és a projektösszetettség szintjén belül dolgozjanak integrált adatszolgáltatásokkal és elemzések, amelyek az adatokból származó intelligenciát tárnak fel, bárhol is legyenek, mind a Microsoft, mind a nem microsoftos platformokon, nyílt keretrendszereken és eszközökön, lehetőséget biztosítva a felhő helyszíni integrációjára, valamint az Azure felhőszolgáltatásainak üzembe helyezésére helyszíni adatközpontok. A Microsoft Trusted Cloud részeként az ügyfelek az Azure-ra támaszkodnak az iparág vezető biztonsága, megbízhatósága, megfelelősége, adatvédelem, valamint a felhőben lévő szervezetek támogatása érdekében az emberek, partnerek és folyamatok hatalmas hálózata érdekében.

A Microsoft Azure segítségével a következőkre tehet:

- Gyorsítsa fel az innovációt a felhővel.

- Üzleti döntéseket & alkalmazásokat elemzésekkel.

- Építsen szabadon és bárhol üzembe helyezhető.

- Védjék meg az üzletüket.

## <a name="security-technical-capabilities-to-fulfil-your-responsibility"></a>Biztonsági technikai képességek az Ön felelősségének teljesítéséhez

A Microsoft Azure olyan szolgáltatásokat nyújt, amelyek segítenek a biztonsági, adatvédelmi és megfelelőségi igények kielégítésében. Az alábbi kép ismerteti a különböző Azure-szolgáltatások áll rendelkezésre, hogy hozzon létre egy biztonságos és megfelelő alkalmazás infrastruktúra alapján az iparági szabványoknak.

![Elérhető biztonsági technikai képességek- Nagy kép](./media/technical-capabilities/azure-security-technical-capabilities-fig1.png)

## <a name="manage-and-control-identity-and-user-access"></a>Identitás és felhasználói hozzáférés kezelése és szabályozása

Az Azure segít megvédeni az üzleti és személyes adatokat azáltal, hogy lehetővé teszi a felhasználói identitások és hitelesítő adatok kezelését, valamint a hozzáférés szabályozását.

### <a name="azure-active-directory"></a>Azure Active Directory

A Microsoft identitás- és hozzáférés-kezelési megoldásai segítenek az informatikai szolgáltatásoknak az alkalmazásokhoz és erőforrásokhoz való hozzáférés védelmében a vállalati adatközpontban és a felhőben, lehetővé téve további szintű érvényesítést, például a többtényezős hitelesítést és a feltételes hozzáférést Politikák. A gyanús tevékenységek monitorozása speciális biztonsági jelentések kiadásán, naplózáson és riasztásokon keresztül valósul meg, így csökkenthető a potenciális biztonsági problémák kockázata. [Az Azure Active Directory Premium](../../active-directory/active-directory-whatis.md) egyszeri bejelentkezést biztosít több ezer felhőalapú alkalmazáshoz, és hozzáférést biztosít a helyszíni enfuttatott webalkalmazásokhoz.

Az Azure Active Directory (Azure AD) biztonsági előnyei közé tartozik a következők:

- A hibrid vállalat minden felhasználójához létrehozhat és kezelhet egy-egy identitást, így a felhasználók, csoportok és eszközök folyamatosan szinkronizálva lesznek.

- Egyszeri bejelentkezési hozzáférést biztosíthat alkalmazásaihoz, köztük több ezer előre integrált SaaS-alkalmazáshoz.

- Szabályalapú többtényezős hitelesítés kikényszerítésével fokozhatja az alkalmazás-hozzáférés biztonságát a helyszíni és a felhőalkalmazásokban.

- Biztonságos távoli hozzáférést biztosít a helyszíni webalkalmazásokhoz az Azure AD alkalmazásproxyn keresztül.

Az [Azure Active Directory-portál](https://aad.portal.azure.com/) az Azure Portal részeként érhető el. Ezen az irányítópulton áttekintést kaphat a szervezet állapotáról, és egyszerűen kezelheti a címtárat, a felhasználókat vagy az alkalmazás-hozzáférést.

![Azure Active Directory](./media/technical-capabilities/azure-security-technical-capabilities-fig2.png)

Az alábbiakban az Azure-identitáskezelési alapvető képességeket kínálják:

- Egyszeri bejelentkezés

- Multi-Factor Authentication

- Biztonsági figyelési, riasztások és gépi tanuláson alapuló jelentések

- A fogyasztók identitásának és hozzáférésének kezelése

- Eszközregisztráció

- Privileged Identity Management

- Identitásvédelem

#### <a name="single-sign-on"></a>Egyszeri bejelentkezés

[Egyszeri bejelentkezés (SSO)](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) azt jelenti, hogy képes elérni az összes alkalmazást és erőforrást, hogy meg kell üzleti, bejelentkezik csak egyszer egyetlen felhasználói fiók használatával. Miután bejelentkezett, hozzáférhet az összes szükséges alkalmazáshoz anélkül, hogy másodszor is hitelesítenie kellene (például be kell írnia egy jelszót).

Számos szervezet támaszkodik a szoftverszolgáltatás (SaaS) alkalmazásokra, például az Office 365, a Box és a Salesforce alkalmazásokra a végfelhasználói hatékonyság érdekében. Korábban az informatikai személyzetnek egyénileg kellett létrehoznia és frissítenie a felhasználói fiókokat az egyes SaaS-alkalmazásokban, és a felhasználóknak minden Egyes SaaS-alkalmazáshoz meg kellett emlékezniük egy jelszóra.

[Az Azure AD kiterjeszti a helyszíni Active Directory a felhőbe,](../../active-directory/manage-apps/what-is-single-sign-on.md)amely lehetővé teszi a felhasználók számára, hogy az elsődleges szervezeti fiók nem csak jelentkezzen be a tartományhoz csatlakozott eszközök és a vállalati erőforrások, hanem az összes webes és SaaS-alkalmazások szükségesek a feladathoz.

A felhasználóknak nem csak több felhasználónevet és jelszót kell kezelniük, az alkalmazás-hozzáférés automatikusan kiépíthető vagy megszüntethető a szervezeti csoportok és az alkalmazotti állapotuk alapján. [Az Azure AD bevezeti a biztonsági és hozzáférés-irányítási vezérlőket,](../../active-directory/active-directory-enterprise-apps-manage-sso.md) amelyek lehetővé teszik a felhasználók hozzáférésének központi kezelését az SaaS-alkalmazások között.

#### <a name="multi-factor-authentication"></a>Multi-Factor Authentication

[Az Azure multi-factor authentication (MFA)](../../active-directory/authentication/multi-factor-authentication.md) egy olyan hitelesítési módszer, amely több ellenőrzési módszert igényel, és egy kritikus második biztonsági réteget ad hozzá a felhasználói bejelentkezésekhez és tranzakciókhoz. [Az MFA segít az](../../active-directory/authentication/concept-mfa-howitworks.md) adatokhoz és alkalmazásokhoz való hozzáférés biztosításában, miközben kielégíti a felhasználói igényeket egy egyszerű bejelentkezési folyamathoz. Erős hitelesítést biztosít számos ellenőrzési lehetőségen keresztül – telefonhívás, szöveges üzenet vagy mobilalkalmazás-értesítés vagy ellenőrző kód és külső OAuth-tokenek.

#### <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Biztonsági figyelési, riasztások és gépi tanuláson alapuló jelentések

A biztonsági figyelésés és riasztások, valamint a gépi tanuláson alapuló jelentések, amelyek inkonzisztens hozzáférési mintákat azonosítanak, segíthetnek a vállalkozás védelmében. Az Azure Active Directory hozzáférési és használati jelentései segítségével betekintést nyerhet a szervezet címtárának integritásába és biztonságába. Ezzel az információval a címtár adminisztrátora jobban meghatározhatja, hogy hol lehetnek a lehetséges biztonsági kockázatok, hogy megfelelően tervezhessék meg ezeket a kockázatokat.

Az Azure Portalon vagy az [Azure Active Directory portálon](https://aad.portal.azure.com/)keresztül a [jelentések](../../active-directory/active-directory-reporting-azure-portal.md) a következő módon vannak kategorizálva:

- Anomáliajelentések – tartalmazzák a bejelentkezési eseményeket, amelyekről megállapították, hogy rendellenesek. Célunk, hogy tudatosítsuk Önben az ilyen tevékenységeket, és lehetővé tegyük, hogy eldönthesse, hogy egy esemény gyanús-e.

- Integrált alkalmazásjelentések – betekintést nyújtanak a felhőalkalmazások szervezetben való használatba kerülésébe. Az Azure Active Directory több ezer felhőalkalmazással kínál integrációt.

- Hibajelentések – jelzik azokat a hibákat, amelyek a fiókok külső alkalmazásokba történő kiépítésekor fordulhatnak elő.

- Felhasználóspecifikus jelentések – eszköz megjelenítése és bejelentkezési tevékenységadatai egy adott felhasználó számára.

- Tevékenységnaplók – az elmúlt 24 órában, az elmúlt 7 napban vagy az elmúlt 30 napban naplózott összes ellenőrzött esemény, valamint a csoporttevékenység-módosítások, valamint a jelszó-visszaállítási és regisztrációs tevékenység rekordját tartalmazzák.

#### <a name="consumer-identity-and-access-management"></a>A fogyasztók identitásának és hozzáférésének kezelése

[Az Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) egy magas rendelkezésre állású, globális identitáskezelési szolgáltatás a több százmillió identitásra skálázható, fogyasztói alkalmazások számára. Mobil- és webes platformokba is integrálható. A felhasználók testreszabható felületeken, meglévő közösségi hálózati fiókjaikkal vagy új hitelesítő adatok létrehozásával jelentkezhetnek be minden alkalmazásába.

A múltban az alkalmazásfejlesztők, akik fel akartak [iratkozni, és be akartak jelentkezni](../../active-directory-b2c/overview.md) a fogyasztókba az alkalmazásaikba, saját kódot írtak volna. Ennek keretében általában helyszíni adatbázisokat vagy rendszereket használtak a felhasználónevek és jelszavak tárolására. Az Azure Active Directory B2C egy biztonságos, szabványokon alapuló platform és bővíthető szabályzatok széles választéka segítségével jobb an-felelek a szervezet számára, hogy integrálja a fogyasztói identitáskezelést az alkalmazásokba.

Az Azure Active Directory B2C használatakor a fogyasztók a meglévő közösségi fiókjaik (Facebook, Google, Amazon, LinkedIn) vagy új hitelesítő adatok (e-mail cím és jelszó, felhasználónév és jelszó) használatával regisztrálhatnak az alkalmazásokra.

#### <a name="device-registration"></a>Eszközregisztráció

[Az Azure AD-eszköz regisztrációja](../../active-directory/devices/overview.md) az eszközalapú [feltételes](../../active-directory/devices/overview.md) hozzáférés-forgatókönyvek alapja. Ha egy eszköz regisztrálva van, az Azure AD-eszköz regisztrációja biztosítja az eszköz egy identitást, amely az eszköz hitelesítésére szolgál, amikor a felhasználó bejelentkezik. A hitelesített eszköz és az eszköz attribútumai ezután feltételes hozzáférési szabályzatok kényszerítésére használhatók a felhőben és a helyszíni üzemeltetésben üzemeltetett alkalmazásokhoz.

[Mobileszköz-felügyeleti (MDM)](https://www.microsoft.com/itshowcase/Article/Content/588/Mobile-device-management-at-Microsoft) megoldással, például az Intune-nal kombinálva az Azure Active Directoryban lévő eszközattribútumok frissülnek az eszközre vonatkozó további információkkal. Ez lehetővé teszi, hogy feltételes hozzáférési szabályokat hozzon létre, amelyek kényszerítik az eszközökről való hozzáférést a biztonsági és megfelelőségi szabványoknak való megfelelés érdekében.

#### <a name="privileged-identity-management"></a>Privileged Identity Management

[Az Azure Active Directory (AD) kiemelt identitáskezelés](../../active-directory/privileged-identity-management/pim-configure.md) lehetővé teszi a kiemelt identitások és az erőforrásokhoz való hozzáférés kezelését, felügyeletét és figyelését az Azure AD-ben, valamint más Microsoft online szolgáltatásokban, például az Office 365-ben vagy a Microsoft Intune-ban.

Néha a felhasználóknak kiemelt műveleteket kell végrehajtaniuk az Azure-ban vagy az Office 365-erőforrásokban vagy más SaaS-alkalmazásokban. Ez gyakran azt jelenti, hogy a szervezeteknek állandó kiemelt hozzáférést kell biztosítaniuk számukra az Azure AD-ben. Ez egyre nagyobb biztonsági kockázatot jelent a felhőben üzemeltetett erőforrások számára, mivel a szervezetek nem tudják megfelelően figyelni, hogy ezek a felhasználók mit csinálnak a rendszergazdai jogosultságaikkal. Emellett ha egy kiemelt hozzáféréssel rendelkező felhasználói fiók biztonsága sérül, hogy egy megsértése hatással lehet a teljes felhőbiztonság. Az Azure AD kiemelt identitáskezelés segít megoldani ezt a kockázatot.

Az Azure AD kiemelt identitáskezelés lehetővé teszi:

- Tekintse meg, hogy mely felhasználók az Azure AD-rendszergazdák

- Igény szerinti, "éppen időben" elérhető rendszergazdai hozzáférés engedélyezése a Microsoft Online Services,például az Office 365 és az Intune használatához

- Jelentések beszerezése a rendszergazdai hozzáférési előzményekről és a rendszergazdai hozzárendelések változásairól

- Jogosultsággal rendelkező szerepkörhöz való hozzáféréssel kapcsolatos riasztások

#### <a name="identity-protection"></a>Identitásvédelem

[Az Azure AD Identity Protection](../../active-directory/identity-protection/overview.md) egy biztonsági szolgáltatás, amely a szervezet identitásait érintő kockázatészlelések és potenciális biztonsági rések összevont nézetét biztosítja. Az Identity Protection a meglévő Azure Active Directory anomáliadetektálási képességeit használja (amely az Azure AD rendellenes tevékenységjelentéseiben érhető el), és új kockázatészlelési típusokat vezet be, amelyek valós időben észlelik az anomáliákat.

## <a name="secure-resource-access"></a>Biztonságos erőforrás-hozzáférés

Az Azure-ban a hozzáférés-vezérlés számlázási szempontból kezdődik. Az [Azure-fiókközpont](https://account.windowsazure.com/subscriptions)ba látogatva elérhető Azure-fiók tulajdonosa a fiókfelügyelő (AA). Az előfizetések a számlázás tárolója, de biztonsági határként is működnek: minden előfizetés rendelkezik egy szolgáltatásrendszergazdával, aki hozzáadhat, eltávolíthat és módosíthat Azure-erőforrásokat az adott előfizetésben az Azure Portal használatával. Az új előfizetés alapértelmezett nagy rajongójasa az AA, de az AA módosíthatja a sa az Azure Account Centerben.

![Biztonságos erőforrás-hozzáférés az Azure-ban](./media/technical-capabilities/azure-security-technical-capabilities-fig3.png)

Az előfizetések egy könyvtárral is társítva vannak. A könyvtár a felhasználók egy készletét határozza meg. Ezek lehetnek a címtárat létrehozó munkahelyi vagy iskolai felhasználók, vagy külső felhasználók (azaz Microsoft-fiókok). Az előfizetéseket azoknak a címtárfelhasználóknak egy részhalmaza férhet hozzá, akik szolgáltatás-rendszergazdaként (SA) vagy társadminisztrátorként (CA) vannak hozzárendelve; az egyetlen kivétel az, hogy örökölt okokból a Microsoft-fiókok (korábban Windows Live ID) biztonsági társadatként vagy hitelesítésbiztosítási rendszerként rendelhetők hozzá anélkül, hogy a címtárban lennének.

A biztonságorientált vállalatoknak arra kell összpontosítaniuk, hogy pontosan megadják az alkalmazottaknak a szükséges engedélyeket. A túl sok engedély kiteheti a fiókot a támadók számára. A túl kevés engedély azt jelenti, hogy az alkalmazottak nem tudják hatékonyan elvégezni a munkájukat. [Az Azure szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/overview.md) részletes hozzáférés-kezelés sel oldja meg a problémát az Azure-hoz.

![Biztonságos erőforrás-hozzáférés](./media/technical-capabilities/azure-security-technical-capabilities-fig4.png)

Az RBAC használata lehetővé teszi, hogy elkülönítse a kötelességeket a csapaton belül, valamint csak olyan mértékű hozzáférést biztosítson, amelyre a felhasználóknak a feladataik elvégzéséhez szüksége van. Ahelyett, hogy mindenkinek korlátlan engedélyeket adna az Azure-előfizetésében vagy az erőforrásokban, csak bizonyos műveleteket engedélyezhet. Például az RBAC használatával lehetővé teheti, hogy egy alkalmazott kezelje a virtuális gépeket egy előfizetésben, míg egy másik kezelheti az SQL-adatbázisokat ugyanazon az előfizetésen belül.

![Biztonságos erőforrás-hozzáférés az Azure-ban (RBAC)](./media/technical-capabilities/azure-security-technical-capabilities-fig5.png)

## <a name="data-security-and-encryption"></a>Adatbiztonság és titkosítás

A felhőben az adatvédelem egyik kulcsa az adatok előfordulásának lehetséges állapotainak és az adott állapothoz tartozó vezérlők nek a számlázása. Az Azure-adatok biztonsága és titkosítási gyakorlati tanácsok a javaslatok körül a következő adatok állapotai.

- In-in: Ez magában foglalja az összes információtároló objektumok, tárolók és típusok, amelyek statikusan léteznek a fizikai adathordozón, legyen az mágneses vagy optikai lemez.

- Átvitel közbeni: Amikor az adatok átvitele összetevők, helyek vagy programok között történik, például a hálózaton keresztül, egy szolgáltatási buszon keresztül (a helyszíni felhőből és fordítva, beleértve a hibrid kapcsolatokat, például az ExpressRoute-ot), vagy egy bemeneti/kimeneti folyamat során, mozgásban lévőnek tekintik.

### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban

Az inaktív titkosítás eléréséhez tegye az alábbiak mindegyikét:

Az alábbi táblázatban részletezett ajánlott titkosítási modellek legalább egyikének támogatása az adatok titkosításához.

| Titkosítási modellek |  |  |  |
| ----------------  | ----------------- | ----------------- | --------------- |
| Kiszolgáló titkosítása | Kiszolgáló titkosítása | Kiszolgáló titkosítása | Ügyfél titkosítása
| Kiszolgálóoldali titkosítás szolgáltatás kezelt kulcsaival | Kiszolgálóoldali titkosítás ügyféláltal kezelt kulcsokkal az Azure Key Vaultban | Kiszolgálóoldali titkosítás a helyszíni ügyfél által kezelt kulcsok használatával |
| • Az Azure Resource Providers elvégzi a titkosítási és visszafejtési műveleteket <br> • A Microsoft kezeli a kulcsokat <br>• Teljes felhőfunkció | • Az Azure Resource Providers elvégzi a titkosítási és visszafejtési műveleteket<br>• Az ügyfél az Azure Key Vault on keresztül vezérli a kulcsokat<br>• Teljes felhőfunkció | • Az Azure Resource Providers elvégzi a titkosítási és visszafejtési műveleteket <br>• Az ügyfél vezérli a kulcsokat a helyszínen <br> • Teljes felhőfunkció| • Az Azure-szolgáltatások nem látják a visszafejtett adatokat <br>• Az ügyfelek a kulcsokat a helyszínen (vagy más biztonságos üzletekben) tartják. A kulcsok nem érhetők el az Azure-szolgáltatások számára <br>• Csökkentett felhőfunkció|

### <a name="enabling-encryption-at-rest"></a>Titkosítás engedélyezése inaktív helyen

**Az üzletek adatainak azonosítása**

Az inaktív titkosítás célja az összes adat titkosítása. Ezzel kiküszöböli a fontos adatok vagy az összes megőrzött hely hiányának lehetőségét. Az alkalmazás által tárolt összes adat számbavétele.

> [!Note]
> Nem csak a "jelentkezési adatok" vagy a "személyazonosításra képes tanusító", hanem az alkalmazással kapcsolatos adatok, beleértve a fiók metaadatait (előfizetés-leképezések, szerződésadatok, személyazonosításra képes tanusító).

Gondolja át, hogy milyen üzleteket használ az adatok tárolásához. Példa:

- Külső tároló (például SQL Azure, Document DB, HDInsights, Data Lake stb.)

- Ideiglenes tárolás (bármely helyi gyorsítótár, amely bérlői adatokat tartalmaz)

- A memórián belüli gyorsítótár (a lapozófájlba helyezhető.)

### <a name="leverage-the-existing-encryption-at-rest-support-in-azure"></a>Használja ki a meglévő titkosítási inaktív támogatást az Azure-ban

Minden egyes használt üzlet, használja ki a meglévő titkosítási inaktív támogatás.

- Azure Storage: Lásd: [Az Azure Storage Service titkosítása inaktív adatokért](../../storage/common/storage-service-encryption.md),

- SQL Azure: [Lásd: Transzparens adattitkosítás (TDE), SQL Mindig titkosított](https://msdn.microsoft.com/library/mt163865.aspx)

- Virtuális gép & helyi lemeztároló[(Azure Disk Encryption)](../azure-security-disk-encryption-overview.md)

A virtuális gép és a helyi lemeztároló használata Azure Disk Encryption, ahol támogatott:

#### <a name="iaas"></a>IaaS

Az IaaS virtuális gépekkel (Windows vagy Linux) rendelkező szolgáltatásoknak [az Azure Disk Encryption](https://microsoft.sharepoint.com/teams/AzureSecurityCompliance/Security/SitePages/Azure%20Disk%20Encryption.aspx) használatával kell titkosítaniuk az ügyféladatokat tartalmazó köteteket.

#### <a name="paas-v2"></a>PaaS v2

A Service Fabric használatával paaS v2-en futó szolgáltatások azure-lemeztitkosítást használhatnak a Virtuálisgép-méretezési csoport [VMSS] számára a PaaS virtuális gépek titkosításához.

#### <a name="paas-v1"></a>PaaS v1

Az Azure Disk Encryption jelenleg nem támogatott a PaaS v1. Ezért alkalmazásszintű titkosítást kell használnia az inaktív adatok titkosításához.  Ez magában foglalja, de nem kizárólagosan, az alkalmazásadatokat, az ideiglenes fájlokat, a naplókat és az összeomlási memóriaképeket.

A legtöbb szolgáltatásnak meg kell próbálnia kihasználni a tárolóerőforrás-szolgáltató titkosítását. Egyes szolgáltatások explicit titkosítást kell végezniük, például minden megőrzött kulcsanyagot (tanúsítványok, gyökér / főkulcsok) a Key Vaultban kell tárolni.

Ha támogatja a szolgáltatás oldali titkosítást az ügyfél által felügyelt kulcsokkal, akkor az ügyfélnek módot kell adnia a kulcs behelyezésére. A támogatott és ajánlott módja ennek az Azure Key Vault (AKV) integrálásával. Ebben az esetben az ügyfelek hozzáadhatják és kezelhetik kulcsaikat az Azure Key Vaultban. Az ügyfelek az AKV használatát a [Key Vault használatának első lépései segítségével ismerhetik](https://go.microsoft.com/fwlink/?linkid=521402)meg.

Az Azure Key Vault integrálásához adjon hozzá kódot, amely kulcs kérése az AKV-tól, ha szükséges a visszafejtéshez.

- Tekintse meg [az Azure Key Vault – Lépésről lépésre](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step/) az AKV-val való integrációval kapcsolatos információkért.

Ha támogatja az ügyfél által felügyelt kulcsokat, meg kell adnia egy felhasználói felületet az ügyfél számára, amely meghatározza, hogy melyik Key Vault (vagy Key Vault URI) melyik et használja.

Mivel az inaktív titkosítás magában foglalja az állomás, az infrastruktúra és a bérlői adatok titkosítását, a kulcsok rendszerhiba vagy rosszindulatú tevékenység miatti elvesztése azt jelentheti, hogy az összes titkosított adat elvész. Ezért fontos, hogy a titkosítás i inaktív megoldás egy átfogó vész-helyreállítási történet rugalmas rendszerhibák és a rosszindulatú tevékenységek.

Az inaktív titkosítást megvalósító szolgáltatások általában továbbra is ki vannak téve a titkosítási kulcsoknak vagy a gazdameghajtón (például a gazdaoperációs rendszer lapfájljában maradó adatoknak).) Ezért a szolgáltatásoknak biztosítaniuk kell, hogy a szolgáltatásaik gazdagépe titkosítva legyen. A számítási csapat megkönnyítése érdekében engedélyezte a Host Encryption telepítését, amely a [BitLocker](https://technet.microsoft.com/library/dn306081.aspx) NKP-t, a DCM-szolgáltatás és az ügynök bővítményeit használja a gazdakötet titkosításához.

A legtöbb szolgáltatás szabványos Azure-virtuális gépeken van megvalósítva. Az ilyen szolgáltatások automatikusan megkapják [a gazdatitkosítást,](../azure-security-disk-encryption-overview.md) amikor a Compute engedélyezi. A számítási felügyelt fürtökben futó szolgáltatások esetében a titkosítás automatikusan engedélyezve van a Windows Server 2016 bevezetésével.

### <a name="encryption-in-transit"></a>Titkosítás átvitel közben

Az adattovábbítás során az adatok védelme az adatvédelmi stratégia alapvető részét kell, hogy képezi. Mivel az adatok sok helyről térnek át oda-vissza, az általános javaslat az, hogy mindig SSL/TLS protokollokat használjon az adatok cseréjére különböző helyeken. Bizonyos körülmények között előfordulhat, hogy a teljes kommunikációs csatornát a helyszíni és a felhőalapú infrastruktúra között egy virtuális magánhálózat (VPN) használatával szeretné elkülöníteni.

A helyszíni infrastruktúra és az Azure között történő adatáthelyezéshez vegye figyelembe a megfelelő biztosítékokat, például a HTTPS-t vagy a VPN-t.

Azon szervezetek számára, amelyeknek a helyszínen található több munkaállomásról kell biztonságos hozzáférést biztosítaniuk az Azure-hoz, használják az [Azure helyek közötti VPN-t.](../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)

Azon szervezetek számára, amelyeknek a helyszíni Azure-beli munkaállomásról kell biztonságos hozzáférést biztosítaniuk, használja [a pont-hely VPN-t.](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md)

A nagyobb adatkészletek áthelyezhetők egy dedikált nagy sebességű WAN-kapcsolaton, például [az ExpressRoute-on](https://azure.microsoft.com/services/expressroute/)keresztül. Ha úgy dönt, hogy az ExpressRoute, akkor is titkosítja az adatokat az alkalmazás szintjén [ssl/TLS](https://support.microsoft.com/kb/257591) vagy más protokollok további védelmet.

Ha az Azure Portalon keresztül kommunikál az Azure Storage-szal, az összes tranzakció HTTPS-en keresztül történik. [A Storage REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) HTTPS-en keresztül is használható az [Azure Storage](https://azure.microsoft.com/services/storage/) és az Azure [SQL Database](https://azure.microsoft.com/services/sql-database/)használatával.

Azok a szervezetek, amelyek nem védik az adatokat az átvitel során, érzékenyebbek [a köztes műveletekre, a](https://technet.microsoft.com/library/gg195821.aspx) [lehallgatásra](https://technet.microsoft.com/library/gg195641.aspx)és a munkamenet-eltérítésre. Ezek a támadások a bizalmas adatokhoz való hozzáférés első lépései lehetnek.

Az Azure VPN-beállításáról a [VPN-átjáró tervezése és tervezése](../../vpn-gateway/vpn-gateway-about-vpngateways.md)című cikkben olvashat bővebben.

### <a name="enforce-file-level-data-encryption"></a>Fájlszintű adattitkosítás kényszerítése

[Az Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) titkosítási, identitás- és engedélyezési szabályzatokat használ a fájlok és az e-mailek védelméhez. Az Azure RMS több eszközön – telefonokon, táblagépeken és számítógépeken – is működik, mivel a szervezeten belül és a szervezeten kívül is védelmet nyújt. Ez a képesség azért lehetséges, mert az Azure RMS olyan szintű védelmet ad hozzá, amely az adatokkal együtt marad, még akkor is, ha elhagyja a szervezet határait.

Ha az Azure RMS-t használja a fájlok védelmére, az iparági szabványnak megfelelő kriptográfia teljes támogatásával [fips 140-2](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf). Ha az Azure RMS-t használja az adatvédelemhez, akkor biztosítja, hogy a védelem a fájlnál marad, még akkor is, ha az informatikai felügyelet alatt nem álló tárolóba, például egy felhőalapú tárolási szolgáltatásba másolja. Ugyanez történik az e-mailben megosztott fájlok esetében is, a fájl egy e-mail mellékleteként védett, és a védett melléklet megnyitásának módjára vonatkozó utasításokat tartalmaz.
Az Azure RMS bevezetésének tervezésekor a következőket javasoljuk:

- Telepítse az [RMS megosztási alkalmazást](https://technet.microsoft.com/library/dn339006.aspx). Ez az alkalmazás integrálható az Office-alkalmazásokkal egy Office-bővítmény telepítésével, hogy a felhasználók könnyen megvédhessék a fájlokat közvetlenül.

- Alkalmazások és szolgáltatások konfigurálása az Azure RMS támogatásához

- Hozzon létre [egyéni sablonokat,](https://technet.microsoft.com/library/dn642472.aspx) amelyek tükrözik az üzleti követelményeket. Például: egy sablon tetszetős adatokhoz, amelyeket minden szigorúan titkos kapcsolódó e-mailben alkalmazni kell.

Az [adatbesorolás](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) és a fájlvédelem terén gyengék szervezetek érzékenyebbek lehetnek az adatszivárgásra. Megfelelő fájlvédelem nélkül a szervezetek nem kaphatnak üzleti elemzéseket, nem figyelhetik a visszaéléseket, és nem akadályozhatják meg a fájlokhoz való rosszindulatú hozzáférést.

> [!Note]
> Az Azure RMS szolgáltatásról az Azure Rights Management – Első lépések című cikkben olvashat [bővebben.](https://technet.microsoft.com/library/jj585016.aspx)

## <a name="secure-your-application"></a>Az alkalmazás védelme
Bár az Azure felelős az infrastruktúra és a platform, amelyen az alkalmazás fut, az Ön felelőssége, hogy biztosítsa az alkalmazás maga. Más szóval az alkalmazáskód és -tartalom biztonságos fejlesztésére, üzembe helyezésére és kezelésére van szükség. Enélkül az alkalmazáskód vagy a tartalom továbbra is ki van téve a fenyegetéseknek.

### <a name="web-application-firewall"></a>Web application firewall (Webalkalmazási tűzfal)
[A webalkalmazás-tűzfal (WAF)](../../application-gateway/waf-overview.md) az [Application Gateway](../../application-gateway/overview.md) egyik szolgáltatása, amely központosított védelmet nyújt a webalkalmazásoknak a gyakori biztonsági rések és biztonsági rések elleni védelemben.

A webalkalmazási tűzfal az [alapvető OWASP-szabálykészletek](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0-s vagy 2.2.9-es verzióinak szabályai alapján működik. A webalkalmazások egyre inkább ki vannak téve rosszindulatú támadásoknak, amelyek az ismert biztonsági réseket használják ki. Az ilyen jellegű támadások között például gyakoriak az SQL-injektálásos és a webhelyek közötti, parancsprogramot alkalmazó támadások. Az ilyen támadások megakadályozása az alkalmazás kódjában kihívást jelenthet, és szigorú felügyeletet, javítást és megfigyelést igényelhet az alkalmazás topológiájának több rétegén. A központosított webalkalmazási tűzfal egyszerűbbé teszi a biztonságfelügyeletet, és segít az alkalmazás-rendszergazdáknak a fenyegetések vagy a behatolások elleni védekezésben. Emellett a WAF-megoldás gyorsabban képes kezelni a biztonsági fenyegetéseket azáltal, hogy kijavítja az ismert biztonsági réseket egy központi helyen, ahelyett hogy az egyes webalkalmazások védelmét biztosítaná. A meglévő alkalmazásátjárókat egyszerűen át lehet alakítani webalkalmazási tűzfallal rendelkező alkalmazásátjárókká.

A webalkalmazási tűzfal többek között a következő gyakori internetes biztonsági rések ellen nyújt védelmet:

- SQL-injektálás elleni védelem

- Webhelyek közötti, parancsprogramot alkalmazó támadások elleni védelem

- Gyakori webes támadások (például parancsinjektálás, HTTP-kéréscsempészet, HTTP-válaszfelosztás és távolifájl-beszúrásos támadás) elleni védelem

- HTTP protokoll megsértése elleni védelem

- HTTP protokollanomáliák (például hiányzó gazdagép-felhasználói ügynök és Accept (Elfogadás) fejlécek) elleni védelem

- Robotprogramok, webbejárók és képolvasók elleni védelem

- Gyakori alkalmazáshelytelen konfigurációk észlelése (azaz Apache, IIS stb.)

> [!Note]
> A szabályok és azok védelmének részletesebb listáját a következő [alapvető szabálykészletekben láthatja:](../../application-gateway/waf-overview.md)

Az Azure számos könnyen használható funkciót is biztosít az alkalmazás bejövő és kimenő forgalmának biztonságossá tétele érdekében. Az Azure is segít az ügyfeleknek az alkalmazáskód védelmében azáltal, hogy külsőleg biztosított funkciókat biztosít a webes alkalmazás biztonsági rései szempontjából.

- [Az Azure Active Directory-hitelesítés beállítása az alkalmazáshoz](https://azure.microsoft.com/blog/azure-websites-authentication-authorization/)

- [Az alkalmazás ba irányuló forgalom biztonságossá tétele a Transport Layer Security (TLS/SSL) – HTTPS engedélyezésével](../../app-service/configure-ssl-bindings.md)

  - [Az összes bejövő forgalom kényszerítése HTTPS-kapcsolaton keresztül](http://microsoftazurewebsitescheatsheet.info/)

  - [Szigorú közlekedésbiztonság engedélyezése (HSTS)](http://microsoftazurewebsitescheatsheet.info/#enable-http-strict-transport-security-hsts)

- [Az alkalmazáshoz való hozzáférés korlátozása az ügyfél IP-címe szerint](http://microsoftazurewebsitescheatsheet.info/#filtering-traffic-by-ip)

- [Az alkalmazáshoz való hozzáférés korlátozása az ügyfél viselkedése szerint – a kérelmek gyakorisága és az egyidejűség](http://microsoftazurewebsitescheatsheet.info/#dynamic-ip-restrictions)

- [A Tinfoil Security Scanning használatával a webalkalmazás kódjának biztonsági rései](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)

- [A TLS kölcsönös hitelesítéskonfigurálása a webalkalmazáshoz való csatlakozáshoz szükséges ügyféltanúsítványok megkövetelésére](../../app-service/app-service-web-configure-tls-mutual-auth.md)

- [Ügyféltanúsítvány konfigurálása az alkalmazásból a külső erőforrásokhoz való biztonságos csatlakozáshoz](https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/)

- [A szabványos kiszolgálófejlécek eltávolítása, hogy az eszközök ne vegyenek ujjlenyomatot az alkalmazástól](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

- [Az alkalmazás biztonságos csatlakoztatása magánhálózat erőforrásaival a helyek közötti VPN használatával](../../app-service/web-sites-integrate-with-vnet.md)

- [Az alkalmazás biztonságos csatlakoztatása magánhálózat erőforrásaival hibrid kapcsolatok használatával](../../app-service/app-service-hybrid-connections.md)

Az Azure App Service ugyanazt a kártevőirtó megoldást használja, amelyet az Azure Cloud Services és a virtuális gépek használnak. Ha többet szeretne megtudni erről olvassa el [a kártevőirtó dokumentációnkat.](antimalware.md)

## <a name="secure-your-network"></a>A hálózat védelme
A Microsoft Azure egy robusztus hálózati infrastruktúrát is tartalmaz az alkalmazás- és szolgáltatáscsatlakozási követelmények támogatásához. A hálózati kapcsolat az Azure-ban található erőforrások, a helyszíni és az Azure által üzemeltetett erőforrások között, valamint az internet és az Azure között lehetséges.

Az [Azure hálózati infrastruktúra](../../virtual-machines/windows/infrastructure-example.md) lehetővé teszi, hogy biztonságosan csatlakoztassa az Azure-erőforrásokat egymáshoz a virtuális hálózatok [(VNets)](../../virtual-network/virtual-networks-overview.md). A virtuális hálózat a saját hálózatának a felhőben reprezentációja. A virtuális hálózat az azure-beli felhőhálózat előfizetésének dedikált logikai elkülönítése. Virtuális hálózatok csatlakoztathatók a helyszíni hálózatokhoz.

![A hálózat védelme (védelem)](./media/technical-capabilities/azure-security-technical-capabilities-fig6.png)

Ha alapvető hálózati szintű hozzáférés-vezérlésre van szüksége (az IP-cím és a TCP- vagy UDP protokollok alapján), akkor használhatja a [hálózati biztonsági csoportokat.](../../virtual-network/virtual-network-vnet-plan-design-arm.md) A hálózati biztonsági csoport (NSG) egy alapvető állapotalapú csomagszűrő tűzfal, amely lehetővé teszi a hozzáférés [5-t.](https://www.techopedia.com/definition/28190/5-tuple)

Az Azure networking támogatja az Azure virtuális hálózatok hálózati forgalmának útválasztási viselkedését. Ezt a [felhasználó által definiált útvonalak azure-ban](../../virtual-network/virtual-networks-udr-overview.md) konfigurálásával teheti meg.

[A kényszerített bújtatás](https://www.petri.com/azure-forced-tunneling) olyan mechanizmus, amelynek segítségével biztosíthatja, hogy a szolgáltatások ne kezdeményezhessenek kapcsolatot az interneten lévő eszközökkel.

Az Azure támogatja a dedikált WAN-kapcsolat kapcsolatot a helyszíni hálózattal és az Azure virtuális hálózattal az [ExpressRoute](../../expressroute/expressroute-introduction.md)segítségével. Az Azure és a webhely közötti kapcsolat olyan dedikált kapcsolatot használ, amely nem megy át a nyilvános interneten. Ha az Azure-alkalmazás több adatközpontban fut, az [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) segítségével intelligens módon továbbíthatja a felhasználóktól érkező kérelmeket az alkalmazás példányai között. A forgalmat az Azure-ban nem futó szolgáltatásokra is irányíthatja, ha azok elérhetők az internetről.

## <a name="virtual-machine-security"></a>Virtuális gépek biztonsága

[Az Azure virtuális gépek](../../virtual-machines/index.yml) lehetővé teszik a számítástechnikai megoldások széles körének agilis módon történő üzembe helyezését. A Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP és Azure BizTalk Services támogatásával bármilyen számítási feladatot és bármilyen nyelvet telepíthet szinte bármilyen operációs rendszeren.

Az Azure-ral a biztonsági gyártók, például a Microsoft, a Symantec, a Trend Micro és a Kaspersky [kártevőirtó szoftvereit](antimalware.md) használhatja a virtuális gépek rosszindulatú fájlokkal, adware-ekkel és egyéb fenyegetésekkel szembeni védelmére.

A Microsoft Antimalware for Azure Cloud Services and Virtual Machines egy valós idejű védelmi képesség, amely segít azonosítani és eltávolítani a vírusokat, kémprogramokat és egyéb rosszindulatú szoftvereket. A Microsoft Antimalware konfigurálható riasztásokat biztosít, ha ismert rosszindulatú vagy nemkívánatos szoftverek próbálnak telepíteni magukat vagy futtatni az Azure-rendszereken.

[Az Azure Backup](../../backup/backup-overview.md) egy méretezhető megoldás, amely nulla tőkebefektetéssel és minimális működési költségekkel védi az alkalmazásadatokat. Az alkalmazáshibák megsérülhetnek az adatokban, és az emberi hibák hibákat okozhatnak az alkalmazásokban. Az Azure Backup segítségével a Windows és Linux rendszert futtató virtuális gépek védettek.

[Az Azure Site Recovery](../../site-recovery/site-recovery-overview.md) segít a számítási feladatok és alkalmazások replikációjának, feladatátvételének és helyreállításának koordinálásában, hogy azok másodlagos helyről is elérhetők, ha az elsődleges hely leáll.

## <a name="ensure-compliance-cloud-services-due-diligence-checklist"></a>Megfelelőség biztosítása: Felhőszolgáltatások átvilágítási ellenőrzőlistája

A Microsoft azért fejlesztette ki [a Cloud Services átvilágítási ellenőrzőlistáját,](https://aka.ms/cloudchecklist.download) hogy segítse a szervezeteket a kellő gondosság gyakorlásában, mivel a felhőre való áttérést mérlegelik. Olyan struktúrát biztosít, amely bármilyen méretű és típusú szervezet számára biztosított – magánvállalkozások és állami szervezetek, beleértve a kormányzatot minden szinten és nonprofit szervezetnél –, hogy azonosítsa saját teljesítmény-, szolgáltatás-, adatkezelési és irányítási célkitűzéseit, és Követelmények. Ez lehetővé teszi számukra, hogy összehasonlítsák a különböző felhőszolgáltatók ajánlatait, és végső soron egy felhőszolgáltatási szerződés alapját képezzék.

Az ellenőrzőlista olyan keretrendszert biztosít, amely a záradékot összehangolja a felhőszolgáltatási megállapodások új nemzetközi szabványával, az ISO/IEC 19086-tal. Ez a szabvány egységes szempontokat kínál a szervezetek számára, hogy segítsen nekik döntéseket hozni a felhő bevezetéséről, és közös alapot hozzon létre a felhőalapú szolgáltatásajánlatok összehasonlításához.

Az ellenőrzőlista elősegíti a felhőbe való, alaposan ellenőrzött áthelyezést, strukturált útmutatást és egységes, megismételhető megközelítést biztosítva a felhőszolgáltató kiválasztásához.

A felhő bevezetése már nem egyszerűen technológiai döntés. Mivel az ellenőrzőlista-követelmények a szervezet minden aspektusát érintik, az összes kulcsfontosságú belső döntéshozó – a CIO és a CISO, valamint a jogi, kockázatkezelési, beszerzési és megfelelőségi szakemberek – összehívását szolgálják. Ez növeli a döntéshozatali folyamat hatékonyságát és a megalapozott érvelésben hozott döntéseket, ezáltal csökkentve annak valószínűségét, hogy az elfogadáshoz előre nem látható akadályok attanak.

Ezen túlmenően, az ellenőrző lista:

- A felhőbevezetési folyamat kezdetén a döntéshozók számára elérhetővé teszi a legfontosabb vitatémákat.

- Támogatja a szabályozásokkal és a szervezet saját adatvédelmi céljaival, a személyazonosításra alkalmas adatokkal és az adatbiztonsággal kapcsolatos alapos üzleti megbeszéléseket.

- Segít a szervezeteknek azonosítani a felhőalapú projekteket esetlegesen érintő esetleges problémákat.

- Konzisztens kérdéseket biztosít, azonos kifejezésekkel, definíciókkal, mutatókkal és eredményekkel az egyes szolgáltatók számára, hogy egyszerűsítse a különböző felhőszolgáltatók ajánlatainak összehasonlítását.

## <a name="azure-infrastructure-and-application-security-validation"></a>Az Azure-infrastruktúra és az alkalmazások biztonságának ellenőrzése

[Az Azure Operational Security](operational-security.md) a felhasználók számára elérhető szolgáltatásokra, vezérlőkre és funkciókra vonatkozik adataik, alkalmazásaik és egyéb eszközeik védelmére a Microsoft Azure-ban.

![biztonsági ellenőrzés (észlelés)](./media/technical-capabilities/azure-security-technical-capabilities-fig7.png)

Az Azure Operational Security egy olyan keretrendszerre épül, amely a Microsoft számára egyedi különböző képességek révén szerzett ismereteket tartalmazza, beleértve a Microsoft Security Development Lifecycle (SDL), a Microsoft Security Response Center programot , valamint a kiberbiztonsági fenyegetési környezet mély tudatosítása.

### <a name="microsoft-azure-monitor"></a>Microsoft Azure-figyelő

[Az Azure Monitor](../../azure-monitor/index.yml) a hibrid felhő informatikai felügyeleti megoldása. Az Azure Monitor-naplók a meglévő System Center-telepítés kiterjesztéséhez vagy a meglévő System Center-telepítés kiterjesztéséhez maximális rugalmasságot és felügyeletet biztosítanak az infrastruktúra felhőalapú felügyeletéhez.

![Azure Monitor](./media/technical-capabilities/azure-security-technical-capabilities-fig8.png)

Az Azure Monitor segítségével bármely felhőben kezelheti bármely példányát, beleértve a helyszíni, az Azure, az AWS, a Windows Server, a Linux, a VMware és az OpenStack példányokat, alacsonyabb költséggel, mint a versenyképes megoldások. A felhőalapú világszámára épített Azure Monitor új megközelítést kínál a vállalat kezeléséhez, amely a leggyorsabb és leginkább költséghatékony módja az új üzleti kihívásoknak való megfeleltetésnek, valamint az új számítási feladatok, alkalmazások és felhőkörnyezetek kezelésére.

### <a name="azure-monitor-logs"></a>Azure Monitor-naplók

[Az Azure Monitor naplók figyelési](https://azure.microsoft.com/documentation/services/log-analytics) szolgáltatásokat biztosít a felügyelt erőforrásokból származó adatok központi tárházba gyűjtésével. Ezek az adatok lehetnek események, teljesítményadatok vagy az API segítségével biztosított egyéni adatok. Az összegyűjtésüket követően az adatok használhatók riasztáshoz, elemzéshez vagy exportáláshoz.

![Azure Monitor-naplók](./media/technical-capabilities/azure-security-technical-capabilities-fig9.png)

Ez a módszer lehetővé teszi a különböző forrásokból származó adatok konszolidálását, így kombinálhatja az Azure-szolgáltatásokból származó adatokat a meglévő helyszíni környezettel. Továbbá egyértelműen elkülöníti az adatok gyűjtését az adatokon végzett műveletektől, így az összes művelet végrehajtható a különféle adatokon.

### <a name="azure-security-center"></a>Azure Security Center

[Az Azure Security Center](../../security-center/security-center-intro.md) az Azure-erőforrások biztonsági felügyeletének átláthatóbbá és szabályozhatóbbá tételével megkönnyíti a fenyegetések megelőzését, észlelését és elhárítását. Az ügyfél összes előfizetésére kiterjedő, integrált biztonsági monitorozást és szabályzatkezelést biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását, és számos biztonsági megoldással együttműködik.

A Security Center a potenciális biztonsági hiányosságok azonosítása érdekében elemzi az Azure-erőforrások biztonsági állapotát. A javaslatok listája végigvezeti Önt a szükséges szabályozási folyamatok konfigurálásának folyamatán.

Példák erre vonatkozóan:

- Kártevőirtók kiépítése a kártékony szoftverek azonosításához és eltávolításához

- Hálózati biztonsági csoportok és szabályok konfigurálása a virtuális gépek forgalmának szabályozására

- Webalkalmazási tűzfalak kiépítése a webalkalmazások ellen irányuló támadások elleni védelemre

- Hiányzó rendszerfrissítések telepítése

- Az operációs rendszer azon konfigurációinak kezelése, amelyek nem felelnek meg a javasolt alapkonfigurációknak

A Security Center automatikusan gyűjti, elemzi és integrálja az Azure-erőforrások, a hálózat és az olyan partnermegoldások, mint például a kártevőirtó-programok és a tűzfalak naplóadatait. Fenyegetések észlelése esetén a központ biztonsági riasztást hoz létre. Példák fenyegetés észlelésére:

- Ismert rosszindulatú IP-címekkel kommunikáló sérült virtuális gépek

- Windows hibajelentés által észlelt speciális kártevő

- Brute force támadások a virtuális gépek ellen

- Az integrált kártevőirtó programok és a tűzfalak biztonsági riasztásai

### <a name="azure-monitor"></a>Azure-figyelő

[Az Azure Monitor](../../azure-monitor/overview.md) az erőforrások adott típusaira vonatkozó információkra mutató mutatókat biztosít. Vizualizációt, lekérdezést, útválasztást, riasztást, automatikus méretezést és automatizálást kínál az Azure-infrastruktúrából (activity log) és az egyes Azure-erőforrásokból (diagnosztikai naplók) származó adatokon.

A felhőalapú alkalmazások összetettek, sok mozgó alkatrészrel. Figyelés adatokat biztosít annak érdekében, hogy az alkalmazás kifogástalan állapotban marad. Ez is segít, hogy elhárít a lehetséges problémákat, vagy hibaelhárítási múlt is.

![Azure](./media/technical-capabilities/azure-security-technical-capabilities-fig10.png) Monitor Emellett figyelési adatok segítségével mély betekintést nyerhet az alkalmazásról. Ez a tudás segíthet az alkalmazások teljesítményének vagy karbantarthatóságának javításában, illetve az egyébként manuális beavatkozást igénylő műveletek automatizálásában.

A hálózati biztonság naplózása létfontosságú a hálózati biztonsági rések észleléséhez, valamint az informatikai biztonsági és szabályozási irányítási modellnek való megfelelés biztosításához. A Biztonsági csoport nézetben lekérheti a konfigurált hálózati biztonsági csoportot és biztonsági szabályokat, valamint a hatályos biztonsági szabályokat. Az alkalmazott szabályok listájával meghatározhatja a nyitott portokat és az ss hálózati biztonsági rést.

### <a name="network-watcher"></a>Hálózati figyelő

[A Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) egy regionális szolgáltatás, amely lehetővé teszi a feltételek figyelése és diagnosztizálása hálózati szinten az Azure-ban, az Azure-ban és az Azure-ból. A Network Watcher segítségével a Network Watcher segítségével megismerheti, diagnosztizálhatja és betekintést nyerhet a hálózatába az Azure-ban. Ez a szolgáltatás tartalmazza a csomagrögzítést, a következő ugrást, az IP-folyamat ellenőrzését, a biztonsági csoport nézetét, az NSG folyamatnaplóit. A forgatókönyvszint-figyelés a hálózati erőforrások teljes nézetét biztosítja, ellentétben az egyes hálózati erőforrások figyelésével.

### <a name="storage-analytics"></a>Storage Analytics

[A Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) olyan metrikákat tárolhat, amelyek összesített tranzakcióstatisztikákat és a tárolási szolgáltatásnak küldött kérelmekkapacitási adatait tartalmazzák. A tranzakciók mind az API-művelet szintjén, mind a tárolási szolgáltatás szintjén, a kapacitás pedig a tárolási szolgáltatás szintjén vannak jelentve. Metrikák adatok segítségével elemezheti a tárolási szolgáltatás használatát, diagnosztizálhatja a rendszerkérésekkel kapcsolatos problémákat a tárolási szolgáltatás, és a szolgáltatás thasználó alkalmazások teljesítményének javítása.

### <a name="application-insights"></a>Application Insights

[Az Application Insights](../../azure-monitor/app/app-insights-overview.md) egy bővíthető alkalmazásteljesítmény-kezelési (APM) szolgáltatás több platformon a webfejlesztők számára. Az élő webalkalmazásának figyelésére használhatja. Automatikusan felismeri a teljesítményanomáliákat. Hatékony elemzési eszközöket tartalmaz a problémák diagnosztizálásához és annak megértéséhez, hogy a felhasználók mit csinálnak az alkalmazással. Úgy tervezték, hogy használatával folyamatosan javíthassa a teljesítményt és a használhatóságot. A .NET, a Node.js és a Java EE platformok széles skáláján működik, amelyeket a helyszínen vagy a felhőben üzemeltet. Integrálja a devOps-folyamatot, és csatlakozási pontokat tartalmaz egy különböző fejlesztői eszközökhöz.

A szolgáltatás az alábbiakat figyeli:

- **Kérések sebessége, válaszidők és hibaarányok** – megtudhatja, hogy mely lapok, mely napszakokban a legnépszerűbbek, és hol találhatók a felhasználók. Megtekintheti, hogy mely lapok teljesítenek a legjobban. Ha több kérés esetén a válaszidők és a hibaarányok értéke megnő, valószínűleg erőforrás-gazdálkodási hibáról van szó.

- **Függőségi értékek, válaszidők és hibaarányok** – megtudhatja, hogy mely külső szolgáltatások okoznak lassulást.

- **Kivételek** – Elemezze az összesített statisztikákat, vagy válasszon ki bizonyos példányokat, és részletezze a veremnyomkövetést és a kapcsolódó kérelmeket. A kiszolgálói és a böngészői kivételekről egyaránt készül jelentés.

- **Lapmegtekintések és betöltési teljesítmény** – a felhasználói böngészők jelentése alapján készül.

- **AJAX hívások weboldalak -** árak, válaszidő, és a hiba aránya.

- **A felhasználók és a munkamenetek száma.**

- Windows vagy Linux rendszerű kiszolgálói gépekről származó **teljesítményszámlálók**, például processzor-, memória- és hálózathasználat.

- Dockerből vagy Azure-ból származó **gazdadiagnosztika**.

- Alkalmazásból származó **nyomkövetési naplók diagnosztikája** – megállapíthatja a nyomkövetési események és a kérések korrelációját.

- **Egyéni események és mérőszámok,** amelyeket saját maga ír az ügyfél- vagy kiszolgálókódba, hogy nyomon kövesse az üzleti eseményeket, például az eladott elemeket vagy a megnyert játékokat.

Az alkalmazás infrastruktúrája általában számos összetevőből áll – például egy virtuális gépből, tárfiókból és virtuális hálózatból, vagy egy webalkalmazásból, adatbázisból, adatbázis-kiszolgálóból és harmadik féltől származó szolgáltatásokból. Ezeket az összetevőket nem külön entitásokként látja, hanem egyetlen entitás kapcsolódó és egymással összefüggő részeiként. Csoportként érdemes telepíteni, kezelni és megfigyelni őket. [Az Azure Resource Manager](../../azure-resource-manager/management/overview.md) lehetővé teszi, hogy a megoldás erőforrásait csoportként dolgozzon.

A megoldás összes erőforrását egyetlen, koordinált műveletben telepítheti, frissítheti vagy törölheti. A telepítéshez egy sablon használatos, amely különböző, például tesztelési, átmeneti és üzemi környezetben is képes működni. A Resource Manager biztonsági, naplózási és címkézési szolgáltatásokat biztosít, hogy segítsen az erőforrások kezelésében a telepítést követően.

**A Resource Manager használatának előnyei**

A Resource Manager számos előnyt kínál:

- A megoldás összes erőforrását egy csoportként telepítheti, felügyelheti és figyelheti meg az erőforrások különálló kezelése helyett.

- A megoldást ismételten telepítheti a fejlesztési életciklus során, és biztos lehet abban, hogy az erőforrások telepítése konzisztens lesz.

- Az infrastruktúrát szkriptek helyett deklaratív sablonok segítségével is kezelheti.

- Megadhatja az erőforrások közötti függőségeket, így azok a megfelelő sorrendben vannak telepítve.

- Hozzáférés-vezérlést alkalmazhat az összes szolgáltatásra az erőforráscsoportban, mivel a szerepköralapú hozzáférés-vezérlés (RBAC) natív módon integrálva van a felügyeleti platformba.

- Címkékkel láthatja el az erőforrásokat, így logikusan rendszerezhető az előfizetés összes erőforrása.

- Az azonos címkén osztozó erőforrások csoportjának költségeit megtekintve jól átláthatók a szervezet számlái.

> [!Note]
> A Resource Manager egy új módot kínál a megoldások telepítésére és kezelésére. Ha a korábbi telepítési modellt használta, és szeretne többet megtudni a változásokról, olvassa el [az Erőforrás-kezelő telepítésének és a klasszikus telepítésnek a megértése című témakört.](../../azure-resource-manager/management/deployment-models.md)

## <a name="next-steps"></a>További lépések

Tudjon meg többet a biztonságról, ha elolvassa néhány részletes biztonsági témakörünket:

- [Audit és naplózás](https://www.microsoft.com/en-us/trustcenter/security/auditingandlogging)

- [Számítástechnikai bűnözés](https://www.microsoft.com/en-us/trustcenter/security/cybercrime)

- [Tervezés és üzembiztonság](https://www.microsoft.com/en-us/trustcenter/security/designopsecurity)

- [Titkosítás](https://www.microsoft.com/en-us/trustcenter/security/encryption)

- [Identitás- és hozzáférés-kezelés](https://www.microsoft.com/en-us/trustcenter/security/identity)

- [Hálózati biztonság](https://www.microsoft.com/en-us/trustcenter/security/networksecurity)

- [Fenyegetések kezelése](https://www.microsoft.com/en-us/trustcenter/security/threatmanagement)
