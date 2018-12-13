---
title: Elkülönítés az Azure nyilvános felhő |} A Microsoft Docs
description: Ismerje meg a felhőalapú számítástechnikai szolgáltatások széles választékával számítási példányokat tartalmazó & szolgáltatások, amelyek méretezhető felfelé és lefelé automatikusan az alkalmazás vagy a vállalat igényeinek.
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
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 4ef312ebd6c329028a556778c24c5e0e41706056
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310997"
---
# <a name="isolation-in-the-azure-public-cloud"></a>Elkülönítés az Azure nyilvános felhő
##  <a name="introduction"></a>Bevezetés
### <a name="overview"></a>Áttekintés
Segít a jelenlegi és jövőbeli Azure ügyfelek ismertetése, és felhasználja az elérhető különböző biztonsági funkciókat és körülvevő az Azure platform, Microsoft kifejlesztett egy sorozatát tanulmányok, biztonsági áttekintéseket, ajánlott eljárások és Ellenőrzőlistákat.
A témakörök tartomány és teljes mértékű tekintetében, és rendszeresen frissül. Ez a dokumentum a sorozat része, a következő absztrakt szakaszban foglaltak szerint.

### <a name="azure-platform"></a>Azure-Platform
Az Azure egy nyílt és rugalmas felhőszolgáltatás-platform, amely a platform támogatja az operációs rendszerek, programozási nyelvek, keretrendszerek, eszközök, adatbázisok és eszközök. Megteheti például a következőt:
- Linux-tárolókat futtathat Docker-integrációval;
- A JavaScript, Python, .NET, PHP, Java és Node.js-alkalmazások készítéséhez és
- Háttérrendszereket készíthet iOS, Android és Windows eszközökhöz.

A Microsoft Azure-fejlesztők milliói technológiákat támogatja, és informatikai szakemberek számára már elnyerték.

Amikor épülnek, vagy át az IT-eszközeivel, nyilvános felhőszolgáltató, hagyatkoznia a annak a szervezetnek képességek az alkalmazások és a szolgáltatások és a vezérlőket, kezelheti a felhőalapú eszközeinek biztonságát biztosítanak az adatok védelme érdekében.

Az Azure infrastruktúráját úgy tervezték, hogy képes legyen ügyfelek millióit egyidejűleg kiszolgáló alkalmazásokat üzemeltetni, és olyan megbízható alapot nyújtson, amely megfelel a vállalatok biztonsági igényeinek. Számos konfigurálható biztonsági beállítással is rendelkezik, amelyek testreszabásával a különböző környezetek egyedi követelményeinek megfelelő biztonsági megoldások alakíthatók ki. Ez a dokumentum segít a fenti követelményeknek megfelelő.

### <a name="abstract"></a>Absztrakt

A Microsoft Azure segítségével az alkalmazások és a virtuális gépek (VM) futtatását a megosztott fizikai infrastruktúrán. A felhőalapú környezetben futó alkalmazások elsődleges gazdasági eleve egyik képes elosztani a több ügyfél között megosztott erőforrások költségét. Ez az eljárás a több-bérlős alacsony költségek különböző ügyfelek körében multiplex erőforrások révén növeli a hatékonyságot. Sajnos ez azzal a kockázattal is megosztási fizikai kiszolgálók és egyéb infrastrukturális erőforrásokat a bizalmas alkalmazások és a egy olyan tetszőleges és potenciálisan rosszindulatú felhasználó tartozó virtuális gépek futtatásához.

Ez a cikk ismerteti, hogyan a Microsoft Azure el vannak különítve kártékony és a nem rosszindulatú felhasználókkal szemben, és a felhőalapú megoldások kiépítése különböző elkülönítési lehetőségek felajánlásával, tervezők útmutatóként szolgál. Ez a tanulmány az Azure-platform és az ügyfelek által használt biztonsági vezérlők technológia összpontosít, és nem próbál meg címet SLA-k, díjszabási modelleket és DevOps-eljárásokkal kapcsolatos szempontok.

## <a name="tenant-level-isolation"></a>Bérlő szintű elkülönítés
Az egyik a elsődleges felhő-számítástechnika egy megosztott, közös infrastruktúra fogalma között számos olyan ügyfél egyszerre vezető méretgazdaságossági előnyeit. A fogalom a több-bérlős nevezzük. A Microsoft folyamatosan együttműködik annak érdekében, hogy a több-bérlős architektúra a Microsoft Cloud Azure biztonsági, bizalmas, adatvédelmi, integritás és rendelkezésre állási szabványok támogatja-e.

Felhőalapú munkahelyek esetén a bérlő olyan ügyfelet vagy szervezetet jelent, amely a felhőszolgáltatás adott példányát birtokolja és kezeli. A Microsoft Azure által biztosított identitásplatformok egy bérlő Azure Active Directory (Azure AD), amely a szervezet megkap és a tulajdonukban áll, amikor regisztrál egy Microsoft felhőszolgáltatásra egyszerűen egy dedikált példánya.

Mindegyik Azure AD-címtár önálló, és el van választva a többi Azure AD-címtártól. Ahogy a vállalat irodaépülete is egy vállalatspecifikus biztonsági vagyontárgynak tekinthető, az Azure AD-címtár is egy, kizárólag az adott szervezet általi használatra kialakított biztonságos eszköz. Az Azure AD architektúrájával megakadályozható az ügyfél- és identitásadatok keveredése. Ez azt jelenti, hogy az adott Azure AD-címtár felhasználói és rendszergazdái véletlenül vagy kártételi szándékkal nem férhetnek hozzá más címtárak adataihoz.

### <a name="azure-tenancy"></a>Az Azure Bérlős
Az Azure bérleti (az Azure-előfizetés) hivatkozik egy "customer/billing" kapcsolat és a egy egyedi [bérlői](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) a [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis). Bérlő szintű elkülönítés, Microsoft Azure-ban érhető el az Azure Active Directoryval és [szerepköralapú vezérlőkkel](https://docs.microsoft.com/azure/role-based-access-control/overview) azt ajánlja. Minden Azure-előfizetés társítva egy Azure Active Directory (AD) címtárban.

Felhasználók, csoportok és alkalmazások adott címtárból az Azure-előfizetésében lévő erőforrások is kezelhetők. Hozzárendelheti a hozzáférési jogosultságok az Azure portal, az Azure parancssori eszközeivel és az Azure felügyeleti API-k használatával. Az Azure AD-bérlő logikailag el különítve a biztonsági határoknak köszönhetően, hogy az ügyfél nem férhetnek hozzá, vagy veszélyeztetheti a közös bérlők véletlenül vagy kártételi. Az Azure AD egy elkülönített hálózati szegmensen elszigetelt "operációs rendszer nélküli" kiszolgálóján fut, ahol gazdagépszintű hálózaticsomag-szűrés és a Windows tűzfal blokkolja kéretlen kapcsolatokat és a forgalom.

- Az Azure AD-adatokhoz való hozzáférést a biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) keresztül felhasználóhitelesítést igényel. Információ a felhasználó létezik-e, a engedélyezett állapotát és a szerepkör segítségével az engedélyezési rendszer határozza meg, hogy a kért cél bérlőhöz való hozzáférés jogosult-e a felhasználó ebben a munkamenetben.

![Az Azure Bérlős](./media/azure-isolation/azure-isolation-fig1.png)


- Bérlők különálló tárolókat, és nincs ezek között kapcsolat.

- Nincs hozzáférés között bérlők, kivéve, ha a bérlői rendszergazda összevonási vagy más bérlőktől származó felhasználói fiókok kiépítése keresztül biztosít.

- Fizikai hozzáférés az Azure AD szolgáltatás alkotó kiszolgálók és az Azure AD háttérrendszerekhez, közvetlen hozzáférést a rendszer korlátozott.

- Az Azure AD-felhasználók nem férhetnek fizikai eszközök vagy a helyek, és ezért már nem lehetséges, hogy a logikai RBAC szabályzat ellenőrzéseibe conditions stated above következő megkerülése.

Diagnosztika és a karbantartási igényeket az operatív modell, amely egy jogosultság igény jogosultságszint-emelési rendszert használ szükséges, és használja. Az Azure AD Privileged Identity Management (PIM) bevezeti a jogosult rendszergazda fogalmát [Jogosult rendszergazdák](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) kell lennie, amely a szükséges jogosultsággal rendelkező felhasználók eléréséhez most majd, de nem minden nap. A szerepkör inaktív, amíg a felhasználónak nincs szüksége a hozzáférésre, majd szükség esetén a felhasználó egy aktiválási folyamat teljesítésével válhat aktív rendszergazdává egy előre meghatározott időtartamra.

![Azure AD Privileged Identity Management](./media/azure-isolation/azure-isolation-fig2.png)

Az Azure Active Directory minden bérlő saját védett tárolót, szabályzatokat és engedélyeket, és a tárolóban, kizárólag a tulajdonosa, és a bérlő által felügyelt üzemelteti.

A tárolókkal. bérlő fogalmát mélyen ingrained, a címtárszolgáltatás portálok egészen az állandó tárolóba érkező minden rétegen.

Akkor is, ha több Azure Active Directory-bérlő metaadataiból ugyanazon a fizikai lemezen tárolja, nincs kapcsolat a címtárszolgáltatás, amely viszont szabja meg a bérlői rendszergazda által meghatározott eltérő a tárolók között.

### <a name="azure-role-based-access-control-rbac"></a>Azure szerepköralapú hozzáférés-vezérlés (RBAC)
[Az Azure szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) a különböző összetevők elérhető az Azure-előfizetésen belül megoszthatók azáltal, hogy hozzáféréskezelést az Azure segítségével. Az Azure RBAC lehetővé teszi, hogy elkülönítse a szervezeten belül, és hozzáférést biztosít a felhasználóknak kell feladataik elvégzéséhez alapján. Helyett, így mindenki korlátozás nélküli engedélyeket az Azure-előfizetés vagy erőforrásokhoz, engedélyezheti csak bizonyos műveleteket.

Az Azure RBAC rendelkezik, amelyek érvényesek az összes erőforrástípus három alapvető szerepkörök:

- **Tulajdonos** az összes olyan erőforrásokhoz, beleértve a jogot arra, hogy mások való hozzáférés delegálására teljes hozzáféréssel rendelkezik.

- **Közreműködői** is létrehozása és kezelése minden típusú Azure-erőforrások, de nem adhat hozzáférést másoknak.

- **Olvasó** megtekintheti a meglévő Azure-erőforrások.

![Azure szerepköralapú hozzáférés-vezérlés](./media/azure-isolation/azure-isolation-fig3.png)

A többi Azure-beli RBAC-szerepkörök lehetővé teszik az adott Azure-erőforrások kezelése. A virtuális gépek Közreműködője szerepkör például lehetővé teszi, hogy a felhasználó számára a virtuális gépek létrehozása és kezelése. Ez nem ad neki hozzáférést az Azure virtuális hálózat vagy az alhálózatot, amelyet a virtuális gép csatlakozik.

[Beépített RBAC-szerepkörök](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) listázása az Azure-ban elérhető szerepköröket. Azt adja meg az operatív és a hatókör, amely minden egyes beépített szerepkört biztosít a felhasználók számára. Ha még több vezérlő saját szerepköröket definiál, megtudhatja, hogyan hozhat létre [egyéni szerepkörök az Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).

Azure Active Directory egyéb funkciói a következők:
- Az Azure AD egyszeri bejelentkezés SaaS-alkalmazásokhoz, helyüktől függetlenül lehetővé teszi. Egyes alkalmazások az Azure AD-vel összevontan működnek, mások jelszavas egyszeri bejelentkezést használnak. Összevont alkalmazás is képes támogatni a felhasználókiépítés és [jelszótárolást](https://www.techopedia.com/definition/31415/password-vault).

- Az [Azure Storage](https://azure.microsoft.com/services/storage/)-ban tárolt adatokhoz való hozzáférést hitelesítés védi. Minden tárfióknak van egy elsődleges kulcs ([tárfiókkulcs](https://docs.microsoft.com/azure/storage/storage-create-storage-account), vagy SAK) és a egy másodlagos titkos kulcsot (a közös hozzáférésű jogosultságkód vagy SAS).

- Az Azure AD biztosítja az identitáskezelési keresztül összevonási szolgáltatás használatával [Active Directory összevonási szolgáltatások](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-azure-adfs), szinkronizáláshoz és a helyszíni címtárak replikációjával.

- [Az Azure multi-factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) a multi-factor authentication szolgáltatás, amely megköveteli a felhasználóktól bejelentkezések ellenőrzése egy mobilalkalmazás, telefonhívás vagy SMS-üzenet használatával. Használat az Azure ad-vel biztonságos helyszíni erőforrásait az Azure multi-factor Authentication-kiszolgálóval és az egyéni alkalmazások és az SDK-val könyvtárak segítségével.

- [Az Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/) Azure-beli virtuális gépek csatlakoztatása az Active Directory tartományhoz tartományvezérlők üzembe helyezése nélkül teszi lehetővé. Ezek a virtuális gépek jelentkezzen be a vállalati Active Directory hitelesítő adataival, és a tartományhoz csatlakoztatott virtuális gépek felügyelete az Azure-beli virtuális gépek a biztonsági előírások kényszerítéséhez csoportházirend használatával.

- [Az Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) magas rendelkezésre állású globális identity management-szolgáltatás felé néző alkalmazások esetén, amely több százmillió identitás kezelésére méretezhető. Mobil- és webes platformokba is integrálható. A felhasználók jelentkezhetnek be minden a testreszabható felületeken, meglévő közösségi hálózati fiókjaikkal vagy hitelesítő adatok létrehozásával.

### <a name="isolation-from-microsoft-administrators--data-deletion"></a>Elkülönítés a Microsoft a rendszergazdák & adatok törlése
A Microsoft védi adatait az illetéktelen hozzáférés, vagy illetéktelen személyek általi használata erős mértékek vesz igénybe. Ezek üzemeltetési folyamatokat és élvezik a [Online szolgáltatások használati feltételeit](https://aka.ms/Online-Services-Terms), amely szerződéses kötelezettségvállalás ad az adatok elérését szabályozó kínálnak.

-   A Microsoft szakemberei nem rendelkezik alapértelmezett hozzáférést az adataihoz a felhőben. Ehelyett kaptak hozzáférést, a felügyeleti felügyeletet, csak szükség esetén. A hozzáférés alaposan ellenőrzött és naplózott, és visszavonja, amikor már nincs rá szükség.

-   A Microsoft megbízhat más cégeket azzal, hogy korlátozott szolgáltatást nyújtsanak a nevében. Alvállalkozók el az ügyféladatokat csak, hogy a szolgáltatások, amelynek em megbíztuk őket biztosít, és azokat nem használhatják más célra használja. További azok szerződésben kötött bizalmasan ügyféladatokat.

Például az ISO/IEC 27001 rendszervizsgálati módban levő minősítések az üzleti szolgáltatás rendszeresen ellenőrzi a Microsoft és akkreditált auditáló cégekkel, amely auditorai emellett mintavételi auditokkal, hogy a hozzáférés csak jogszerű üzleti célokra. Saját ügyféladataihoz bármikor és bármilyen okból mindig elérheti.

Ha törli az adatokat, a Microsoft Azure törli az adatokat, beleértve a gyorsítótárazott vagy a biztonsági másolatok. A releváns szolgáltatások esetében, amelyek törlése a megőrzési időszak vége után 90 napon belül történik. (A releváns szolgáltatások adatfeldolgozási feltételek szakaszában meghatározott a [Online szolgáltatások használati feltételeit](https://aka.ms/Online-Services-Terms).)

Ha a meghajtó tárolására használt romlik egy hardverhiba, biztonságosan van-e [törölve lesz, vagy megsemmisül](https://microsoft.com/en-us/trustcenter/privacy/you-own-your-data) Microsoft visszaadja a gyártó cseréje vagy javítása előtt. A meghajtón található adatokat a rendszer felülírja annak érdekében, hogy az adatok semmilyen módon nem lehet helyreállítani.

## <a name="compute-isolation"></a>Elkülönítés COMPUTE
Microsoft Azure lehetőséget kínál a különböző felhőalapú számítástechnikai szolgáltatások széles választékával számítási példányokat tartalmazó és a szolgáltatások, amelyek méretezhető felfelé és lefelé automatikusan az alkalmazás vagy a vállalat igényeinek. Ezek számítási példány és a szolgáltatás kínálnak az elkülönítési adatok védelmét a rugalmasságot, a konfiguráció feláldozása nélkül a felhasználók igény szerint több szinten.

### <a name="isolated-virtual-machine-sizes"></a>Elkülönített virtuálisgép-méretek
Az Azure Compute olyan virtuálisgép-méreteket kínál, amelyek egy meghatározott hardvertípusban vannak elkülönítve, és egyetlen ügyfél számára vannak fenntartva.  Ezek a virtuálisgép-méretek olyan számítási feladatokhoz megfelelőek, amelyeket magas szintű izolációval szükséges elkülöníteni más ügyfelek számítási feladataitól, beleértve olyan elemeket is mint a megfelelőség vagy a jogszabályi előírások betartása.  Ügyfelek is kiválaszthatják a ezek elkülönített virtuálisgép-erőforrások vertikálisan tovább particionálhatja a [beágyazott virtuális gépek Azure-támogatás](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).

Kész üzletifolyamat-elkülönített mérete garantálja, hogy a virtuális gép lesz a megadott kiszolgálópéldány csak egy futó.  Az aktuális elkülönített virtuálisgép-ajánlatok a következők:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard m 128 MS
* Például a Standard_GS5
* Standard G5
* Standard_DS15_v2
* Standard_D15_v2

További információ érhető el minden egyes elkülönített mérete [Itt](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory).

### <a name="hyper-v--root-os-isolation-between-root-vm--guest-vms"></a>Legfelső szintű virtuális gép és a Vendég virtuális gépek közötti elkülönítés a Hyper-V és a legfelső szintű operációs rendszer
Az Azure számítási platformra alapuló gép virtualizációs – jelentése, amely végrehajtja az összes felhasználói kód a Hyper-V virtuális gépen. Minden olyan Azure-csomópont (vagy a hálózati végpont) nincs, amely közvetlenül a hardver felett fut, és a egy csomópontra osztja a Vendég virtuális gépek (VM) változó számú Hipervizoron.


![Legfelső szintű virtuális gép és a Vendég virtuális gépek közötti elkülönítés a Hyper-V és a legfelső szintű operációs rendszer](./media/azure-isolation/azure-isolation-fig4.jpg)


Minden egyes csomópontot egy speciális legfelső szintű virtuális gép, amely a gazda operációs rendszer fut is tartalmaz. Kritikus fontosságú a határok elkülönítése a legfelső szintű a Vendég virtuális gépek és a Vendég virtuális gépeket egy másik, a hipervizor és a gyökér operációs rendszer kezeli a virtuális gép. Az operációs rendszer hipervizor/root párosítás használ az operációs rendszer felhasználói élmény és a Microsoft Hyper-v, a Vendég virtuális gépek erős elkülönítést biztosító újabb tanulás a Microsoft évtizedes.

Az Azure platform virtualizált környezetet használ. A felhasználói példányok működnek, önálló virtuális gépekről, amelyek nem rendelkeznek fizikai gazdagép-kiszolgálóhoz való hozzáférést.

Az Azure hipervizor úgy viselkedik, mint a micro-kernel és az összes hardver hozzáférési kéréseket továbbít a Vendég virtuális gépek a gazdagépnek feldolgozásra a VMBus nevű megosztottmemória-felületen. Ez megakadályozza, hogy a felhasználók nyers olvasási, írási és végrehajtási hozzáférést szerezzenek a rendszerhez, és csökkenti a rendszererőforrások megosztásának kockázatát.

### <a name="advanced-vm-placement-algorithm--protection-from-side-channel-attacks"></a>Speciális Virtuálisgép-elhelyezési algoritmus & oldalsó csatorna támadások elleni védelem
Kereszt-VM támadás két lépésből áll: egy támadó által felügyelt virtuális gép elhelyezése egy, az áldozat virtuális gépek ugyanazon a gazdagépen, és az elkülönítési határt kártevők victim bizalmas adatokat lophatnak el, vagy befolyásolja a teljesítményt greed vagy vandalizmus majd megsértése. A Microsoft Azure, mindkét lépést védelmet biztosít egy speciális Virtuálisgép-elhelyezési algoritmus és az összes ismert ügyféloldali csatorna támadásoktól, többek között a zajos szomszédok virtuális gépek védelmét.

### <a name="the-azure-fabric-controller"></a>Az Azure Fabric Controller
Az Azure-Hálóvezérlő felelős az infrastruktúra-erőforrások bérlői számítási feladatok, és a gazdagép és virtuális gépek egyirányú kommunikációt kezeli. A virtuális gép az Azure fabric controller forgalomba hozatalára algoritmusa rendkívül kifinomult és szinte lehetetlen előre, mint a fizikai gazdagép szintjén.

![Az Azure Fabric Controller](./media/azure-isolation/azure-isolation-fig5.png)

Az Azure hipervizor előírja a memóriák és a folyamatok elkülönítését virtuális gépek között, és biztonságosan irányítja a hálózati forgalmat a vendég operációs rendszerek bérlőihez. Ezzel elkerülhető a lehetőségét, és a kiszolgálóoldali csatorna támadás VM-szintjén.

Az Azure-ban, a legfelső szintű virtuális gép különleges: azt a megerősített operációs rendszert futtat a gyökér operációs rendszer neve, amelyen a háló ügynök (be). FAs viszont kezelésére használhatók vendégügynökeit (elérhetővé tétel GA) belül az ügyfél virtuális gépek vendég operációs rendszerek. FAs tárolási csomópontok is kezelheti.

Az Azure hipervizor gyűjteménye gyökér operációs rendszer/be, és az ügyfél virtuális gépek/gáz magában foglalja a számítási csomópontok. FAs kezeli a hálóvezérlő (FC), amely kívül esik (számítási és tárolási fürtöket különálló FCs felügyelik) számítási és tárolási csomópontok létezik. Ha egy ügyfél-frissítéseket az alkalmazás konfigurációs fájljának futás közben, az FC kommunikál be, amely ezután csatlakozik a gáz, amely értesíti, hogy a konfigurációs módosítás alkalmazása. Hardverhiba esetén az FC automatikusan keresse meg a rendelkezésre álló hardverek és indítsa újra a virtuális gép létezik.

![Az Azure Fabric Controller](./media/azure-isolation/azure-isolation-fig6.jpg)

A Hálóvezérlő ügynök a kommunikációt az egyirányú. Az ügynök valósítja meg az SSL-védelemmel ellátott szolgáltatása, amely csak válaszol a kérelmekre a vezérlőről. Ez nem kezdeményeznek kapcsolatokat a vezérlő vagy más kiemelt jogosultságú belső csomópontok. Az FC minden válasz kezeli, mintha nem megbízható.


![Hálóvezérlő](./media/azure-isolation/azure-isolation-fig7.png)

Elkülönítés a legfelső szintű virtuális gépről a Vendég virtuális gépek és a Vendég virtuális gépeket egy másik terjeszti ki. A számítási csomópontok is el különítve a nagyobb védelme tárolási csomópontok.


A hipervizor és az operációs rendszer hálózati csomag - szűrőket, hogy biztosítsa, hogy a nem megbízható virtuális gépek nem identitáshamisításos forgalom létrehozása vagy őket, nem címzett forgalom fogadása érdekében adja meg a gazdagép védett infrastruktúra-végpontokra irányuló adatforgalmat, vagy küldése/fogadása nem megfelelő szórásos forgalom.


### <a name="additional-rules-configured-by-fabric-controller-agent-to-isolate-vm"></a>További szabályok elkülöníteni a virtuális gép Hálóvezérlő ügynök által konfigurált
Alapértelmezés szerint minden forgalmat blokkol, amikor egy virtuális gépet hoz létre, és ezután a a hálóvezérlő ügynök a csomagszűrőt szabályok és kivételek engedélyezett forgalom engedélyezésére konfigurálja.

Programozott szabályok két kategóriába sorolhatók:

-   **Gép konfigurációs vagy infrastrukturális szabályok:** Alapértelmezés szerint minden kommunikáció blokkolva van. Nincsenek a kivételeket úgy, hogy engedélyezi egy virtuális gép DHCP és DNS-forgalmat küldjön és fogadjon. Virtuális gépek küldhetnek forgalmat a "nyilvános" internetre és az azonos Azure Virtual Network és az operációs rendszer aktiválási kiszolgáló belüli más virtuális gépek küldhetnek forgalmat is. Engedélyezett kimenő célok a virtuális gépek listája nem tartalmazza az Azure útválasztó-alhálózatok, az Azure felügyeleti és más Microsoft-tulajdonságok.

-   **Szerepkör-konfigurációs fájl:** A bejövő hozzáférés-vezérlési listák (ACL) a bérlő szolgáltatási modell alapján határozza meg.

### <a name="vlan-isolation"></a>VLAN elkülönítése
Minden egyes fürt három VLAN-OK szerepelnek:

![VLAN elkülönítése](./media/azure-isolation/azure-isolation-fig8.jpg)


-   A fő VLAN – amellyel az ügyfél nem megbízható csomópontokon

-   Az FC VLAN – tartalmazza a megbízható FCs és a támogató rendszerek

-   Az eszköz VLAN – tartalmazza a megbízható hálózatok és egyéb infrastrukturális eszközök

Kommunikáció engedélyezett a Szálcsatornás VLAN a fő VLAN-hoz, de nem kezdeményezhető, a fő VLAN az FC-VLAN-hoz. Kommunikáció is blokkolva van a fő VLAN az eszközre VLAN. Ez biztosítja, hogy akkor is, ha egy ügyfél kódot futtató csomópont biztonsága sérül, akkor nem támadási FC vagy VLAN-OK eszköz lévő csomópontok.

## <a name="storage-isolation"></a>Tárolási elkülönítés
### <a name="logical-isolation-between-compute-and-storage"></a>Számítási és tárolási logikai elkülönítését
Az alapvető tervezési részeként a Microsoft Azure storage-ból Virtuálisgép-alapú számítási osztja szét. Ez a fajta elkülönítés lehetővé teszi a számítási és tárolási skálázását, ami megkönnyíti a több-bérlős és elszigeteltséget biztosít.

Ezért Azure Storage futtat külön hardvert nem hálózati kapcsolattal rendelkező, kivéve az Azure Compute logikailag. [Ez](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf) azt jelenti, hogy a virtuális lemez létrehozásakor a lemezterület nincs lefoglalva a teljes kapacitás. Ehelyett egy tábla jön létre, amely címek a virtuális lemezen területek a fizikai lemezen van leképezve, és, hogy a táblázat kezdetben üres. **Először egy ügyfél a virtuális lemezen, írja az adatokat a fizikai lemezen lefoglalt, és egy rá mutató kerül a táblázatban.**
### <a name="isolation-using-storage-access-control"></a>Elkülönítés használatával tárolási hozzáférés-vezérlés
**Hozzáférés-vezérlés az Azure Storage-ban** rendelkezik egy egyszerű hozzáférés-vezérlési modellből. Minden Azure-előfizetés egy vagy több Tárfiókot is létrehozhat. Minden Tárfióknak van egy egyetlen titkos kulcsot, amellyel kezelheti a Storage-fiókban lévő összes adatokhoz való hozzáférést.

![Elkülönítés használatával tárolási hozzáférés-vezérlés](./media/azure-isolation/azure-isolation-fig9.png)

**(Beleértve a táblák) Azure Storage-adatokkal való hozzáférés** szabályozható a [SAS (közös hozzáférésű Jogosultságkód)](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) jogkivonatot, amely hozzáférési hatókörrel rendelkező. SAS létrehozása révén aláírt lekérdezés sablon (URL), a [SAK (Tárfiókkulcs)](https://msdn.microsoft.com/library/azure/ee460785.aspx). Amely [URL-cím aláírt](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) adható meg egy másik folyamat (amely van, vagy delegálás útján), amely ezután adja meg az adatokat a lekérdezés és a kérés, a storage szolgáltatás. SAS Időalapú hozzáférés biztosítása az ügyfeleknek nem fedi fel a tárfiók a titkos kulcs teszi lehetővé.

Az SAS, az azt jelenti, hogy mi is az ügyfeleknek, a storage-fiókját egy megadott időszakban, és az engedélyek bizonyos készletét lévő objektumokra vonatkozó. Ezekkel a korlátozott engedélyekkel a hozzáférési kulcsainak megosztása nélkül is kínálunk.

### <a name="ip-level-storage-isolation"></a>IP-szintű tárolási elkülönítés
Tűzfalak létrehozása és a egy IP-címtartományt definiálása a megbízható ügyfeleket. IP-címtartomány csak a definiált tartományon belüli IP-cím rendelkező ügyfelek csatlakozhat [Azure Storage](https://docs.microsoft.com/azure/storage/storage-security-guide).

IP-storage-adatok védelme biztosítható legyen a jogosulatlan felhasználóktól egy hálózati mechanizmust, amely foglal le egy dedikált vagy dedikált IP-tárolási forgalom-alagúton keresztül.

### <a name="encryption"></a>Titkosítás
Az Azure a következő típusú adatok védelme érdekében titkosítási kínál:
-   Titkosítás az átvitel során

-   Titkosítás inaktív állapotban

#### <a name="encryption-in-transit"></a>Titkosítás az átvitel során
Titkosítás az átvitel során egy mechanizmust, az adatok védelme, amikor azok elküldése hálózatokon keresztül. Az Azure Storage segítségével gondoskodhat adatok használatával:

-   [Átviteli szintű titkosítást](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-in-transit), például a HTTPS vagy onnan máshová az Azure Storage-adatok átvitel során.

-   [Vezetékes titkosítás](../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), például az Azure-fájlmegosztások az SMB 3.0 titkosítás.

-   [Ügyféloldali titkosítás](https://docs.microsoft.com/azure/storage/storage-security-guide#using-client-side-encryption-to-secure-data-that-you-send-to-storage), a storage-bA továbbított előtt titkosíthatja az adatokat, és az adatok visszafejtéséhez követően elfogyott a tárterület.

#### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban
A legtöbb szervezet számára [adattitkosítás inaktív](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) kötelező lépés az adatok adatvédelmi, megfelelőségi és az adatok elkülönítése felé. Nincsenek három Azure-funkciók adja meg az "Inaktív" adatok titkosítása:

-   [A Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-at-rest) lehetővé teszi, hogy a storage szolgáltatás automatikusan adatok titkosításához, az Azure Storage írásakor.

-   [Ügyféloldali titkosítás](https://docs.microsoft.com/azure/storage/storage-security-guide#client-side-encryption) az inaktív adatok titkosítását a szolgáltatást is nyújt.

-   [Az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) lehetővé teszi, hogy az operációsrendszer-lemezek és a egy IaaS virtuális gép által használt adatlemezek titkosítása.

#### <a name="azure-disk-encryption"></a>Azure Disk Encryption
[Az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) virtuális gépek (VM) segítségével szervezeti biztonsági és megfelelőségi követelmények (beleértve a rendszerindító és az adatlemezek) a Virtuálisgép-lemezek titkosítása a kulcsok és szabályzatok, hogy [Azure Key Tároló](https://azure.microsoft.com/services/key-vault/).

A Disk Encryption megoldás a Windows alapján [Microsoft BitLocker Meghajtótitkosításon](https://technet.microsoft.com/library/cc732774.aspx), és a Linux-megoldás alapján [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

A megoldás a következő esetekben ha engedélyezve vannak a Microsoft Azure IaaS virtuális gépeket támogatja:
-   Az Azure Key Vault-integráció

-   Standard szintű virtuális gépek: A, D, DS, G, GS és egyebektől sorozat IaaS virtuális gépek

-   A Windows és Linux rendszerű IaaS virtuális gépek titkosításának engedélyezése

-   A Windows IaaS virtuális gépek operációsrendszer- és a titkosítás letiltása meghajtók

-   Linux rendszerű IaaS virtuális gépek adatmeghajtók titkosításának letiltása

-   A Windows ügyfél operációs rendszer futtató IaaS virtuális gépek titkosításának engedélyezése

-   Csatlakoztatási elérési úttal köteteken titkosításának engedélyezése

-   Linux rendszerű virtuális gépek, amelyeken a lemez összevonása (RAID) titkosításának engedélyezése használatával [mdadm](https://en.wikipedia.org/wiki/Mdadm)

-   A Linux rendszerű virtuális gépek titkosításának engedélyezése [LVM (a Logical Volume Manager)](https://msdn.microsoft.com/library/windows/desktop/bb540532) adatlemezek

-   A tárolóhelyek segítségével konfigurált Windows virtuális gépek titkosításának engedélyezése

-   Minden nyilvános Azure-régióban támogatott

A megoldás nem támogatja a következő forgatókönyvek, szolgáltatások és technológiák a kiadásban:

-   Alapszintű csomag IaaS virtuális gépek

-   Linux rendszerű IaaS virtuális gépek egy operációs rendszer meghajtójának titkosításának letiltása

-   A klasszikus virtuális gép létrehozási módszer használatával létrehozott IaaS virtuális gépeken

-   Integráció a helyszíni kulcskezelő szolgáltatás

-   Az Azure Files (megosztott fájlrendszert), a hálózati fájlrendszer (NFS), a dinamikus köteteket és a Windows virtuális gépek, amelyeken a szoftveres RAID-rendszerek

## <a name="sql-azure-database-isolation"></a>SQL Azure adatbázis-elkülönítés
Az SQL Database a Microsoft Cloud egy, a piacvezető Microsoft SQL Server motoron alapuló relációs adatbázis-szolgáltatása, amely képes a kritikus fontosságú számítási feladatok kezelésére. SQL Database által nyújtott előre jelezhető adatok elkülönítése a fiók szintjén a földrajzi régióban és a hálózat alapú / – mindezt szinte Adminisztráció.

### <a name="sql-azure-application-model"></a>SQL Azure alkalmazásmodell

[A Microsoft SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started) adatbázis az SQL Server-technológiákra épülő felhőalapú relációsadatbázis-szolgáltatás. Egy magas rendelkezésre állású, méretezhető, több-bérlős adatbázis-szolgáltatás a felhőben a Microsoft által üzemeltetett biztosít.

Egy alkalmazás perspektíva SQL Azure biztosít a következő hierarchia: Minden egyes szintjét egy-a-többhöz tartalmazottsági szintek az alábbi rendelkezik.

![SQL Azure alkalmazásmodell](./media/azure-isolation/azure-isolation-fig10.png)

A fiók és -előfizetés a Microsoft Azure platform fogalmak felügyeleti és számlázási társítása.

Logikai kiszolgálóiról és adatbázisairól SQL Azure-specifikus fogalmakat és SQL Azure, a megadott OData- és TSQL-felületek használatával vagy az Azure portal integrálva SQL Azure-portálon keresztül történik.

SQL Azure-kiszolgálók nem fizikai gép vagy Virtuálisgép-példányok, ehelyett azok az adatbázisok, felügyeleti és biztonsági szabályzataival, amelyeket a rendszer az úgynevezett "a logikai master" megosztás gyűjtemények adatbázis.

![SQL Azure](./media/azure-isolation/azure-isolation-fig11.png)

Logikai master adatbázis a következők:

-   A kiszolgálóhoz való csatlakozáshoz használt SQL-bejelentkezésekben.

-   Tűzfalszabályok

Számlázási és használati adatokat az SQL Azure az egyazon logikai kiszolgálón adatbázisok nem biztos, hogy az SQL Azure-fürt ugyanazon a fizikai példányon kell helyette alkalmazások kell adnia a céladatbázis neve, ha kapcsolódik.

Az ügyfél szempontjából, egy logikai kiszolgáló létrehozása egy földrajzi grafikus régióban, amíg a kiszolgáló tényleges létrehozása történik az egyik a fürtök a régióban.

### <a name="isolation-through-network-topology"></a>Hálózati topológia elkülönítését

Ha egy logikai kiszolgáló létrehozása, és a DNS-név regisztrálva van, a DNS-név az adott adatközpont, ahol a kiszolgáló alatt állnak az úgynevezett "Átjáró VIP" cím mutat.

A virtuális IP-cím (virtuális IP-cím), mögött van egy átjáró állapotmentes szolgáltatások gyűjteménye. Nincs szükség több adatforráson (master adatbázishoz, felhasználói adatbázist, stb.) közötti koordinációt általában átjárók vesz első részt. Átjárószolgáltatásokat megvalósításához a következők:
-   **TDS-kapcsolati proxyhasználat.** Ez magában foglalja a felhasználói adatbázis megkeresése a háttér-fürtben. a bejelentkezési folyamat végrehajtására, majd továbbítása a TDS-csomagok a háttérbeli és vissza.

-   **Adatbázis-kezelés.** Ide tartoznak a gyűjteménye, munkafolyamatok, hajtsa végre a CREATE/ALTER/DROP database műveletek végrehajtására. Az adatbázis-műveletek vagy elemzés TDS-csomagokat, vagy explicit OData API-k is hivatkozhat.

-   CREATE/ALTER/DROP login/felhasználói műveletek

-   A logikai kiszolgáló felügyeleti műveletek OData API-n keresztül

![Hálózati topológia elkülönítését](./media/azure-isolation/azure-isolation-fig12.png)

A réteg mögött az átjárók "háttérbeli" nevezzük. Ez a magas rendelkezésre állású módon összes adat tárolására. Minden adat van szólt tartozik egy "partíció" vagy "feladatátvételi egység", azok legalább három replika kellene. Replikák tárolja és replikálja az SQL Server-motor és egy feladatátvételi rendszer, más néven "fabric" kezeli.

Általában a háttér-rendszer nem képes kimenő kommunikációra más rendszerek biztonsági okokból. Ez a rendszerek az előtér (átjáró) szinten van fenntartva. Az átjáró réteg gépei korlátozott jogosultságokkal jellegű defense mechanizmusként a támadási felület minimalizálása érdekében a háttér-gépeken.

### <a name="isolation-by-machine-function-and-access"></a>Elkülönítés funkcióval és hozzáférés
SQL Azure (szintből áll, a másik gépet funkciók futó szolgáltatásokat. SQL Azure oszlik "Háttér" felhőalapú adatbázis és a "előtérbeli" (átjáró/felügyelet), az általános elvet forgalmat csak folyamatos háttér-be és ki nem rendelkező környezetek. Az előtér-környezet kommunikálhatnak a külvilág más szolgáltatások számára, és általában csak korlátozott engedélyekkel a háttér-(elegendő hívja a belépési pontok meghívásához szükséges).

## <a name="networking-isolation"></a>Hálózati elkülönítés
Azure-beli hálózati elkülönítési több réteget tartalmaz. Az alábbi ábrán látható a különböző rétegeket, a hálózatok elkülönítéséhez az Azure biztosít az ügyfelek számára. Ezek a rétegek, mind a natív az Azure platformon, magát, és a felhasználó által meghatározott szolgáltatások. Bejövő az internetről, Azure DDoS biztosít a nagy méretű támadások ellen az Azure-elkülönítést. A következő elkülönítési réteg a felhasználó által meghatározott nyilvános IP-címek (végpontok), amelyek segítségével meghatározhatja, mely forgalom tevékenységeken keresztül a felhőalapú szolgáltatás, a virtuális hálózathoz. Natív Azure-beli virtuális hálózatok elkülönítését biztosítja a többi teljes elkülönítés és, hogy csak a forgalom, aki elérési utak és módszerek segítségével. Ezen elérési utakat és módszerek a következő réteg, ahol az NSG-k, az udr-t és a hálózati virtuális berendezések használható védelme érdekében az alkalmazások központi telepítésének a védett hálózati elkülönítési határokat kíván létrehozni.

![Hálózati elkülönítés](./media/azure-isolation/azure-isolation-fig13.png)

**Forgalom elkülönítése:** A [virtuális hálózat](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) van a forgalomelkülönítési határok az Azure platformon. Virtuális gépek (VM) egy virtuális hálózat nem útján kommunikálnak közvetlenül egy másik virtuális hálózatot, virtuális gépek akkor is, ha az adott ügyfél által létrehozott két virtuális hálózatnak. Elkülönítés kritikus tulajdonság, amely biztosítja az ügyfél virtuális gépei, kommunikációs privát virtuális hálózaton belül marad.

[Alhálózat](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview#subnets) további, a virtuális hálózati IP-címtartomány alapján az elkülönítési réteget biztosít. IP-címek a virtuális hálózatban, és biztonsági több alhálózatra is eloszthatja egy virtuális hálózatot. Egy VNeten belül az alhálózatokra üzembe helyezett virtuális gépek és a PaaS szerepkörpéldányok (ugyanaz vagy különböző) további konfigurálás nélkül is tudnak egymással kommunikálni. Beállíthatja úgy is [hálózati biztonsági csoport (NSG-k)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview#network-security-groups-nsg) engedélyezéséhez vagy letiltásához a hálózati forgalmat a Virtuálisgép-példányhoz NSG hozzáférés-vezérlési lista (ACL) a konfigurált szabályok alapján. Az NSG-ket alhálózatokhoz vagy az alhálózaton belüli virtuálisgép-példányokhoz lehet hozzárendelni. Ha az NSG-t hozzárendelik egy alhálózathoz, az ACL-szabályok érvényesek lesznek az alhálózatban lévő összes virtuálisgép-példányra.

## <a name="next-steps"></a>További lépések

- [Gépek, a Windows Azure virtuális hálózatok hálózati elkülönítési beállításai](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/)

Ez magában foglalja a klasszikus előtérbeli és háttérbeli forgatókönyv ahol egy adott háttér-hálózat vagy alhálózat gépek előfordulhat, hogy engedélyezése csak egyes ügyfelek vagy más számítógépek engedélyezett IP-cím alapján egy adott végponthoz csatlakozik.

- [Elkülönítés COMPUTE](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)

A Microsoft Azure biztosít a különböző számítástechnikai felhőszolgáltatásokhoz, amely tartalmazza a számítási példányok széles választékával & méretezhetők felfelé és lefelé automatikusan, ha az alkalmazás vagy a vállalati szolgáltatások.

- [Tárolási elkülönítés](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)

A Microsoft Azure storage-ból felhasználói Virtuálisgép-alapú számítási osztja szét. Ez a fajta elkülönítés lehetővé teszi a számítási és tárolási skálázását, ami megkönnyíti a több-bérlős és elszigeteltséget biztosít. Ezért Azure Storage futtat külön hardvert nem hálózati kapcsolattal rendelkező, kivéve az Azure Compute logikailag. Összes kérelem futtatása HTTP vagy HTTPS, az ügyfél által választott alapján.

