---
title: Az Azure nyilvános Felhőjében elszigeteléséről |} Microsoft Docs
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
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 6f01c2938462f3912928e183fcec215a52a3ee48
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "34010880"
---
# <a name="isolation-in-the-azure-public-cloud"></a>Az Azure nyilvános Felhőjében elszigeteléséről
##  <a name="introduction"></a>Bevezetés
### <a name="overview"></a>Áttekintés
Segít a jelenlegi és jövőbeli Azure ügyfelek ismertetése és használják a különböző biztonsági lehetőségeinek és körülvevő az Azure platformon, Microsoft kifejlesztette egy sorozatát tanulmányok, biztonsági áttekintése, ajánlott eljárások és Ellenőrzőlisták.
A témakörök között körének és mélysége, és rendszeresen frissülnek. Ez a dokumentum a sorozat része az alábbiakban absztrakt szakaszban foglaltak szerint.

### <a name="azure-platform"></a>Az Azure Platform
Azure olyan nyitott és rugalmas felhőszolgáltatási platform, amely támogatja a operációs rendszerek, programozási nyelvek, keretrendszerek, eszközök, adatbázisok és eszközök legszélesebb kiválasztása. Megteheti például a következőt:
- Futtassa a Linux-tárolók Docker integrációs;
- Alkalmazások, JavaScript, Python, .NET, PHP, Java és Node.js; és
- Build vissza-végpontok iOS, Android és Windows eszközökhöz.

Microsoft Azure támogatja a fejlesztők milliói technológiát és informatikai szakemberek számára már alapulnak, és megbízható.

Létrehozása vagy áttelepítése informatikai eszközök, a nyilvános felhőbeli szolgáltatás szolgáltatója, akkor vannak támaszkodva az alkalmazások és a szolgáltatások és a biztonsági a felhő alapú eszközök kezelésére adathordozóira vezérlők adatok védelme érdekében, hogy szervezete képességek.

Az Azure infrastruktúráját úgy tervezték, hogy képes legyen ügyfelek millióit egyidejűleg kiszolgáló alkalmazásokat üzemeltetni, és olyan megbízható alapot nyújtson, amely megfelel a vállalatok biztonsági igényeinek. Számos konfigurálható biztonsági beállítással is rendelkezik, amelyek testreszabásával a különböző környezetek egyedi követelményeinek megfelelő biztonsági megoldások alakíthatók ki. Ez a dokumentum segít az elvárásoknak.

### <a name="abstract"></a>Absztrakt

A Microsoft Azure lehetővé teszi, hogy a megosztott fizikai infrastruktúra alkalmazások és a virtuális gépek (VM) futtathatja. Az elsődleges gazdasági igénybevételéről fontosságú egy felhőalapú környezetben futó alkalmazások közül azt a képességet terjesztheti a több felhasználó között megosztott erőforrások költségét. Ez az eljárás a több-bérlős multiplex erőforrások közötti kis költségek különböző ügyfelek révén növeli a hatékonyságot. Sajnos egyben bevezeti a fizikai kiszolgálók és a bizalmas alkalmazások és a virtuális gépek, előfordulhat, hogy egy tetszőleges és az esetlegesen kártékony felhasználóhoz tartozó futtatásához más infrastruktúrához kapcsolódó erőforrások megosztása kockázatát.

Ez a cikk ismerteti, hogyan Microsoft Azure el vannak különítve a rosszindulatú és nem rosszindulatú felhasználók ellen, és a megoldások különböző elkülönítési döntések kínál a fejlesztők által újratervezni útmutatóként szolgál. Ez a dokumentum elsősorban az Azure platform és ügyfélkapcsolati biztonsági vezérlők technológia, és nem kísérli meg cím SLA-k, árképzési modellt és DevOps eljárásokkal kapcsolatos szempontok.

## <a name="tenant-level-isolation"></a>Bérlő szintű elkülönítés
Az egyik elsődleges előny a felhőalapú informatika egy megosztott, közös infrastruktúra fogalma egyszerre egy között számos olyan ügyfél, ami méretgazdaságossági. Ez a több bérlős nevezik. Microsoft folyamatosan működik, győződjön meg arról, hogy a több-bérlős architektúrák a Microsoft felhőalapú Azure biztonsági, titkosítás, adatvédelmi, integritás és rendelkezésre állás szabványok támogatja-e.

Felhőalapú munkahelyek esetén a bérlő olyan ügyfelet vagy szervezetet jelent, amely a felhőszolgáltatás adott példányát birtokolja és kezeli. A Microsoft Azure által biztosított identitásplatformok a bérlő egyszerűen egy Azure Active Directory (Azure AD), amely a szervezet megkap és a tulajdonában áll, amikor regisztrálnak egy Microsoft felhőszolgáltatásra dedikált példánya esetén.

Mindegyik Azure AD-címtár önálló, és el van választva a többi Azure AD-címtártól. Ahogy a vállalat irodaépülete is egy vállalatspecifikus biztonsági vagyontárgynak tekinthető, az Azure AD-címtár is egy, kizárólag az adott szervezet általi használatra kialakított biztonságos eszköz. Az Azure AD architektúrájával megakadályozható az ügyfél- és identitásadatok keveredése. Ez azt jelenti, hogy az adott Azure AD-címtár felhasználói és rendszergazdái véletlenül vagy kártételi szándékkal nem férhetnek hozzá más címtárak adataihoz.

### <a name="azure-tenancy"></a>Az Azure-Bérlőhöz
Az Azure-bérlőhöz (Azure-előfizetéshez) hivatkozik a "felhasználói/billing" kapcsolat és egy egyedi [bérlői](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) a [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis). Bérlő szintű elkülönítés a Microsoft Azure-ban érhető el, az Azure Active Directoryval és [szerepköralapú vezérlők](https://docs.microsoft.com/azure/role-based-access-control/overview) által kínált. Minden Azure-előfizetés tartozik egy Azure Active Directory (AD) címtárban.

Felhasználók, csoportok és alkalmazások tartalmazza a kezelhetik az erőforrásokat az Azure-előfizetésben. Az Azure portál, az Azure parancssori eszközök és az Azure szolgáltatásfelügyeleti API használatával a következő hozzáférési jogokat rendelhet hozzá. Az Azure AD-bérlő elkülönül logikailag biztonsági határokat használja, így nem az ügyfél hozzáférési vagy veszélyeztetheti a közös bérlők szándékosan vagy véletlenül. Az Azure AD fut egy elkülönített hálózati szegmensen elkülönített "operációs rendszer nélküli" kiszolgálókon, ahol gazdaszintű hálózaticsomag-szűrés és a Windows tűzfal blokkolja kéretlen kapcsolatokat és a forgalom.

- Az Azure AD-adatokhoz való hozzáférés csak a felhasználó hitelesítése biztonságijogkivonat-szolgáltatás (STS) keresztül. A felhasználó fenntartása, engedélyezett állapotát, és a szerepkör az engedélyezési rendszert használják annak meghatározásához, hogy a kért cél bérlő van engedélyezve a felhasználó ebben a munkamenetben.

![Az Azure-Bérlőhöz](./media/azure-isolation/azure-isolation-fig1.png)


- Bérlők diszkrét tárolói, és ezek között nincs kapcsolat van.

- Nem lehet hozzáférni között bérlők csak a bérlői rendszergazda összevonási vagy a többi bérlőtől felhasználói fiókok létesítését keresztül biztosít.

- Fizikai hozzáférés az Azure AD szolgáltatás alkotó kiszolgálók és az Azure AD háttér-rendszereket, a közvetlen hozzáférést korlátozódik.

- Az Azure Active Directory-felhasználók hozzáférése nincs fizikai eszközök és helyét, és ezért nincs lehetőség, hogy a logikai RBAC házirend ellenőrzések is olvasható a következő megkerülése.

A diagnosztika és karbantartási igényeire az operatív modell just-in-time jogosultság jogosultságszint-emelés rendszer által szükséges, és használja. Az Azure AD Privileged Identity Management (PIM) jelenik meg az egy jogosult rendszergazdának. [Jogosult rendszergazdák](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) szükséges jogosultsággal rendelkező felhasználók férhetnek hozzá a most majd, de nem minden nap lehet. A szerepkör nem aktív, amíg a felhasználó kell elérnie, akkor az aktiválási folyamat befejezéséhez, és egy előre meghatározott időn egy aktív felügyeleti válik.

![Azure AD Privileged Identity Management](./media/azure-isolation/azure-isolation-fig2.png)

Az Azure Active Directory mindegyik bérlő saját védett tárolóban, szabályzatokat és engedélyeket a, és kizárólag által birtokolt vagy kezelt a bérlő tárolóban levő üzemelteti.

A bérlői tárolók fogalmát mélyen ingrained összes rétegek, egészen az állandó tároló portálok: a címtárszolgáltatásban.

Akkor is, ha több Azure Active Directory-bérlő metaadatok ugyanazon a fizikai lemezen tárolja, nincs kapcsolat a tárolók a címtárszolgáltatás, amely viszont határozza meg a bérlői rendszergazda által meghatározott eltérő között.

### <a name="azure-role-based-access-control-rbac"></a>Azure szerepköralapú hozzáférés-vezérlés (RBAC)
[Azure szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) segítséget nyújt az Azure-előfizetés belül elérhető különböző összetevőket fájlmegosztás, adja meg a részletes hozzáféréskezelést az Azure. Az Azure RBAC lehetővé teszi, hogy elkülönítse a feladatokat a szervezeten belül, és hozzáférést biztosíthat a felhasználóknak kell elvégezni a munkájukat alapján. Jogosultságot ad a Mindenki helyett korlátlan engedélyeket a Azure-előfizetés vagy erőforrásokhoz, engedélyezheti a csak bizonyos műveleteket.

Az Azure RBAC rendelkezik, amelyek érvényesek az összes erőforrástípus három alapvető szerepkörök:

- **Tulajdonos** minden erőforrást, beleértve a jogot, hogy mások számára delegálása teljes hozzáféréssel rendelkezik.

- **A közreműködői** is létrehozása és kezelése az Azure-erőforrások minden típusú, de nem tud hozzáférést biztosítani, mások számára.

- **Olvasó** megtekintheti a meglévő Azure-erőforrások.

![Azure szerepköralapú hozzáférés-vezérlés](./media/azure-isolation/azure-isolation-fig3.png)

A többi Azure RBAC szerepkört az adott Azure-erőforrások felügyelet lehetővé tételéhez. Például a virtuális gép közreműködői szerepkör lehetővé teszi, hogy a felhasználó számára a virtuális gépek létrehozására és kezelésére. Azt nem betekintést biztosít az Azure virtuális hálózat vagy az alhálózatot, amely a virtuális géphez csatlakozik.

[Beépített RBAC-szerepkörök](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) össze az Azure-ban érhető el a szerepkörök listáját. Azt adja meg az operatív és hatókörhöz, amely minden beépített szerepkörök a felhasználók számára. Ha még nagyobb mértékben vezérelheti a saját szerepköröket definiál, lásd: hogyan hozhat létre [egyéni szerepkörök az Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).

Az Azure Active Directory néhány más képességet a következők:
- Az Azure AD lehetővé teszi, hogy egyszeri bejelentkezés az SaaS-alkalmazásokhoz, függetlenül attól, hogy a rendszer hol tárolja. Egyes alkalmazások az Azure AD-vel összevontan működnek, mások jelszavas egyszeri bejelentkezést használnak. Összevont alkalmazásokat is képes támogatni a felhasználók átadása és [jelszótárolást](https://www.techopedia.com/definition/31415/password-vault).

- Az [Azure Storage](https://azure.microsoft.com/services/storage/)-ban tárolt adatokhoz való hozzáférést hitelesítés védi. Minden tárfiók elsődleges kulccsal rendelkezik ([tárfiók kulcsa](https://docs.microsoft.com/azure/storage/storage-create-storage-account), vagy SAK) és egy másodlagos titkos kulcs (a közös hozzáférésű jogosultságkódot, vagy SAS).

- Az Azure AD Identity keresztül összevonási szolgáltatás segítségével biztosítja [Active Directory összevonási szolgáltatások](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-azure-adfs), szinkronizáláshoz és a replikáció a helyszíni címtárakban.

- [Az Azure multi-factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) a többtényezős hitelesítési szolgáltatás, amely megköveteli a felhasználóktól bejelentkezések ellenőrizheti a mobilalkalmazás, telefonhívással vagy szöveges üzenetben. Használat és az Azure AD segítségével biztonságos a helyszíni erőforrások az Azure multi-factor Authentication kiszolgálóval és az egyéni alkalmazások és könyvtárak a SDK használatával.

- [Azure AD tartományi szolgáltatások](https://azure.microsoft.com/services/active-directory-ds/) lehetővé teszi, hogy az Azure virtuális gépek csatlakoztatása egy Active Directory-tartomány tartományvezérlők üzembe helyezésének nélkül. Ezek a virtuális gépek jelentkezzen be a vállalati Active Directory hitelesítő adataival, és a tartományhoz csatlakozó virtuális gépek felügyelete az Azure virtuális gépeken futó biztonsági alapterveket kényszerítéséhez csoportházirend használatával.

- [Az Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) egy magas rendelkezésre állású globális identitás szolgáltatást biztosít a felhasználók felé néző alkalmazások száz millió identitások alkalmazkodnak. Mobil- és webes platformokba is integrálható. A felhasználók bejelentkezhetnek, testre szabható felhasználói élmény mellett az alkalmazások hitelesítő adatok létrehozásával vagy meglévő közösségi fiókjaik használatával.

### <a name="isolation-from-microsoft-administrators--data-deletion"></a>A Microsoft rendszergazdák & Adattörlés elkülönítési
Microsoft erős intézkedések megóvhatja adatait egy nem megfelelő hozzáférési vagy illetéktelen személyek általi vesz igénybe. Ezeket a működési folyamatok és vezérlőelemek üzemelnek a [Online szolgáltatási feltételek](http://aka.ms/Online-Services-Terms), melyik ajánlattal szerződéses kötelezettségek adataihoz való hozzáférés szabályozásához.

-   A Microsoft szakemberei nincs alapértelmezett hozzáférési az adatok a felhőben. Ehelyett kapnak hozzáférést, csak akkor, ha szükséges, a felügyeleti felügyelet alatt. Elérheti gondosan ellenőrzött és naplózott, és visszavonva, amikor már nincs szükség.

-   Microsoft felvételi előfordulhat, hogy más vállalatokkal, hogy a nevében korlátozott szolgáltatásokat nyújtsanak. Alvállalkozók elérhessék az ügyféladatok csak biztosítanak, amelynek a szolgáltatások em bíztunk azokat, és nem használhatják más célra használja azt. Továbbá azok szerződésben kötött bizalmasan kezeljék a felhasználók információkat.

Üzleti szolgáltatásokat, például az ISO/IEC 27001 ellenőrzött minősítései közül rendszeresen ellenőrizni Microsoft és akkreditált naplózási cégek, amelyek minta naplózás igazolják, hogy hozzáférést, csak a valódi üzleti célok végrehajtására. A saját felhasználói adatok bármikor, bármilyen okból mindig elérhető.

Ha törli az adatokat, a Microsoft Azure törli az adatokat, beleértve a gyorsítótárazott vagy biztonsági másolatok. A hatókör szolgáltatások esetén törlése a megőrzési időszak lejárta után 90 napon belül esedékes. (Az adatok feldolgozása feltételek részében meghatározott hatókör szolgáltatások a [Online szolgáltatási feltételek](http://aka.ms/Online-Services-Terms).)

Ha a meghajtó tárolására használt hardver meghibásodása esetén a rendszer biztonságosan [törlik vagy megsemmisítik](https://www.microsoft.com/trustcenter/Privacy/You-own-your-data) előtt Microsoft visszaadja a gyártó cseréje vagy a javítás. Győződjön meg arról, hogy az adatok nem állítható helyre bármilyen módon történő felülírja a meghajtón található adatokat.

## <a name="compute-isolation"></a>Számítási elkülönítési
Microsoft Azure biztosít a különböző felhőalapú számítási szolgáltatás, amely tartalmazza az számítási példányokért széles köre és a szolgáltatások, amely is fel-le automatikusan az alkalmazás vagy a vállalat igényeinek. A számítási példány és a szolgáltatás kínál az elkülönítési biztosítani az adatokat a konfigurációs rugalmasságot feláldozása nélkül, hogy az ügyfelek igény szerint több szinten.

### <a name="isolated-virtual-machine-sizes"></a>Elszigetelt virtuálisgép-méretek
Az Azure Compute kínál a virtuális gép méretét, amely a adott hardverekhez típusra, és egyetlen ügyfél számára kijelölt elszigetelt vannak.  A virtuális gép használata esetén például a megfelelőségi és szabályozási követelmények elemek munkaterhelések nagyfokú elkülönítési más felhasználóktól igénylő munkaterhelések legmegfelelőbb értékek.  Az ügyfelek is beállíthatja a további fel az erőforrások ezek elszigetelt virtuális gépek használatával [beágyazott virtuális gépek az Azure támogatási](https://azure.microsoft.com/en-us/blog/nested-virtualization-in-azure/).

Egy elkülönített mérete használata biztosítja, hogy az, hogy a virtuális gépet, hogy adott server-példány csak egy futó lesz.  Az aktuális elszigetelt virtuális gép ajánlatokat a következők:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard szintű, G5
* Standard_DS15_v2
* Standard_D15_v2

Minden elérhető elkülönített méretére vonatkozó részletesebb [Itt](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes-memory).

### <a name="hyper-v--root-os-isolation-between-root-vm--guest-vms"></a>A Hyper-V & legfelső szintű OS elkülönítési legfelső szintű VM & Vendég virtuális gépek között
Azure compute platform gép virtualizálási alapul – tehát az összes felhasználói kód végrehajtása a Hyper-V virtuális gépen. Minden Azure csomópont (vagy a hálózati végpont) nincs olyan Hipervizorra, amely közvetlenül a hardver felett fut, és egy csomópont felosztja a változó a Vendég virtuális gépek (VM) számát.


![A Hyper-V & legfelső szintű OS elkülönítési legfelső szintű VM & Vendég virtuális gépek között](./media/azure-isolation/azure-isolation-fig4.jpg)


Minden csomópont is rendelkezik egy különleges legfelső szintű virtuális gép, az állomás operációs rendszer futtató. A kritikus határ elkülönítését a legfelső szintű a Vendég virtuális gépek és a Vendég virtuális gépeket egy másik, a hipervizor és a gyökér operációs rendszer kezeli a virtuális gép. A hipervizor/root az operációs rendszer jelenlegi párosítását a Microsoft évtizedekben operációs rendszer biztonsági élményt, és a Microsoft Hyper-V, a Vendég virtuális gépek erős elkülönítést biztosítani újabb tanulva kihasználja.

Az Azure platform virtualizált környezetet használ. A felhasználói példányok működhetnek önálló virtuális gépek, amelyek nem rendelkeznek hozzáféréssel a fizikai kiszolgáló, és az elkülönítési kikényszeríti a fizikai processzor (ring-0/ring-3) a jogosultsági szintek segítségével.

A „Ring 0” a legmagasabb, a „Ring 3” a legalacsonyabb jogosultsági szint. A vendég operációs rendszer fut egy alacsonyabb szintű jogosultságokat igénylő Ring 1, és alkalmazások futnak, a minimális jogosultságokkal rendelkező Ring 3. A fizikai erőforrások ilyen módszerrel történő virtualizálása következtében egyértelműen elkülönül a vendég operációs rendszer és a hipervizor, ez pedig a két rendszer további biztonsági elkülönülését eredményezi.

Az Azure hipervizor úgy viselkedik, mint egy micro kernel és átadja az összes hardver hozzáférési kérelmek a Vendég virtuális gépek feldolgozásra a fogadó egy VMBus nevű megosztott memória felület használatával. Ez megakadályozza, hogy a felhasználók nyers olvasási, írási és végrehajtási hozzáférést szerezzenek a rendszerhez, és csökkenti a rendszererőforrások megosztásának kockázatát.

### <a name="advanced-vm-placement-algorithm--protection-from-side-channel-attacks"></a>Speciális virtuális gép elhelyezési algoritmus & ügyféloldali csatorna támadások elleni védelem
A kereszt-VM támadás két lépésből áll: az ellenfél által felügyelt virtuális gép ugyanazon a gazdagépen rendelkezésre álló az áldozat virtuális gépek elhelyezése, és az elkülönítési határt alkotnak áldozata bizalmas információk ellopására vagy befolyásolja a teljesítményt greed vagy vandalizmus majd megsértése. Microsoft Azure két lépést védelmet biztosít egy speciális virtuális gép elhelyezési algoritmus és az összes ismert ügyféloldali csatorna támadások beleértve zajos szomszédos virtuális gépek védelmi használatával.

### <a name="the-azure-fabric-controller"></a>Az Azure Fabric Controller
Az Azure Fabric Controller bérlői munkaterheléseket infrastruktúrához kapcsolódó erőforrások lefoglalása felelős, és a gazdagép és virtuális gépek egyirányú kommunikációt kezeli. A virtuális gép az Azure fabric controller forgalomba hozatalára algoritmusa magas kifinomult és majdnem lehetetlen – fizikai állomás szint.

![Az Azure Fabric Controller](./media/azure-isolation/azure-isolation-fig5.png)

Az Azure hipervizor kikényszeríti a memória és a folyamat elkülönítése virtuális gépeket, és biztonságosan irányítja a hálózati forgalmat a vendég operációs rendszer bérlők számára. Ezzel a megoldással lehetőségét, és az ügyféloldali csatorna támadás VM szinten.

Az Azure, a legfelső szintű virtuális gép működése speciális: az a megerősített operációs rendszert futtat a gyökér operációs rendszer neve, amelyen a háló ügynök (be). Eszközök segítségével pedig vendégügynökök (GA) kezelése belül a vendég operációs rendszer a felhasználói virtuális gépeket. Eszközök kezelésére is tárhely csomópontot.

Azure hipervizor gyűjteménye a gyökérkönyvtár OS/be, és az ügyfél virtuális gépek/gáz magában foglalja a számítási csomópont. A háló vezérlő (FC), mely kívül (számítási és tárolófürtöket kezeli külön FCs) számítási és tárolási csomópont már eszközök felügyeletét. Az ügyfél frissíti a az alkalmazás konfigurációs fájljának futtatása, ha az FC kommunikál a be, amely ezután csatlakozik a gáz, amely értesíti, hogy a konfigurációs módosítás alkalmazása. A hardver meghibásodása esetén a FC automatikusan található rendelkezésre álló hardver és indítsa újra a virtuális gép van.

![Az Azure Fabric Controller](./media/azure-isolation/azure-isolation-fig6.jpg)

Az ügynök és a Fabric Controller közötti kommunikáció egyirányú. Az ügynök valósítja meg az SSL-védelemmel ellátott szolgáltatás, amely csak válaszol a kérelmekre a tartományvezérlőről. A vezérlő vagy más kiemelt belső csomópontok kapcsolatok nem indítható el. Az FC kezeli az összes, mintha nem megbízható.


![Fabric-vezérlő](./media/azure-isolation/azure-isolation-fig7.png)

Elkülönítési bővíti a Vendég virtuális gépek és a Vendég virtuális gépek egymástól a legfelső szintű virtuális gépről. A számítási csomópontok is el különítve a nagyobb védelme tárolócsomópontok.


A hipervizor és a gazdagép operációs rendszer adja meg a hálózati csomag - szűrők érdekében, hogy biztosítsa, hogy a nem megbízható virtuális gépek nem hamisított adatforgalmat generálnak vagy forgalom nem fogadását, végpontok védett infrastruktúra-forgalom közvetlen vagy küldés/fogadás nem megfelelő szórási forgalmat.


### <a name="additional-rules-configured-by-fabric-controller-agent-to-isolate-vm"></a>További Fabric Controller ügynök elkülöníteni a virtuális gép által beállított szabályok
Alapértelmezés szerint minden forgalmat blokkol, amikor létrejön egy virtuális gép, és ezután a fabric controller ügynök konfigurálja a csomag-szűrő hozzáadása a szabályok és kivételek engedélyezett forgalom engedélyezésére.

Szabályok, amelyek program két kategóriába sorolhatók:

-   **Számítógép-konfiguráció vagy infrastruktúra szabályok:** alapértelmezés szerint minden kommunikációja blokkolva van. A virtuális gép DHCP és DNS- adatforgalmat engedélyezi kivételek vannak. Virtuális gépek is forgalmat küldeni a "nyilvános" internet és más virtuális gépek belül az azonos Azure virtuális hálózat és az operációs rendszer aktiválási kiszolgálói forgalmat küldeni. Engedélyezett kimenő célja a virtuális gépek listája nem tartalmazza az Azure útválasztó alhálózatok, az Azure felügyeleti és más Microsoft tulajdonságai.

-   **Szerepkör konfigurációs fájl:** Ez határozza meg, hogy a bejövő hozzáférés-vezérlési listái (ACL) alapján a bérlő szolgáltatásmodellt.

### <a name="vlan-isolation"></a>VLAN elkülönítése
Az egyes csoportokban vannak a három VLAN-ok:

![VLAN elkülönítése](./media/azure-isolation/azure-isolation-fig8.jpg)


-   A fő VLAN – összeköttetések használatára a nem megbízható felhasználói csomópontok

-   Az FC VLAN – tartalmazza a megbízható FCs és a támogató rendszerek

-   Az eszköz VLAN – tartalmazza a megbízható hálózatok és egyéb eszközök

Kommunikáció a fő VLAN-hoz az FC VLAN engedélyezett, de nem indítható el a fő VLAN az FC VLAN-hoz. Kommunikációs is blokkolva lett a fő VLAN az eszközre VLAN. Ez biztosítja, hogy akkor is, ha egy felhasználói kód futtatásával csomópont biztonsága sérül, akkor nem támadás csomópontok az FC vagy a VLAN-OK eszköz.

## <a name="storage-isolation"></a>Tárolási elkülönítési
### <a name="logical-isolation-between-compute-and-storage"></a>Számítási és tárolási közötti logikai elkülönítés
Az alapvető tervezési részeként a Microsoft Azure elválasztja a Virtuálisgép-alapú számítási tárolóból. Ez az elkülönítés lehetővé teszi, hogy számítási és tárolási méretezését, így könnyebben adja meg a több-bérlős és elkülönítés.

Ezért Azure Storage futtatásával külön hardverre nincs hálózati kapcsolattal rendelkező Azure számítási kivéve logikailag. [Ez](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf) azt jelenti, hogy a virtuális lemez létrehozása, amikor lemez ne legyen lefoglalva terület esetében a teljes kapacitás. Ehelyett egy táblát hoz létre, amely a címek a virtuális lemezre van leképezve a fizikai lemezen területek és a tábla kezdetben üres. **Az ügyfél írja az adatokat a virtuális lemezen, először legyen lefoglalva terület a fizikai lemezen, és a rá mutató el van helyezve a táblázatban.**
### <a name="isolation-using-storage-access-control"></a>Elkülönítés használatával tároló hozzáférés-vezérlés
**Hozzáférés-vezérlés az Azure Storage** egy egyszerű hozzáférés-vezérlési modell tartalmaz. Minden Azure-előfizetés egy vagy több Storage-fiókokat hozhat létre. Minden Tárfiók, amellyel szabályozhatja, hogy a Tárfiókban lévő adatokat egy titkos kulccsal rendelkezik.

![Elkülönítés használatával tároló hozzáférés-vezérlés](./media/azure-isolation/azure-isolation-fig9.png)

**Azure Storage-adatokkal (ideértve a táblák) való hozzáférés** szabályozható a [SAS (közös hozzáférésű Jogosultságkód)](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) jogkivonatot, amely hatókörű hozzáférést. A SAS létrehozása révén aláírva lekérdezés sablon (URL), a [SAK (Tárfiók kulcsa)](https://msdn.microsoft.com/library/azure/ee460785.aspx). Amely [URL-cím aláírt](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) adható meg egy másik folyamat (, van meghatalmazva), amely ezután töltse ki a lekérdezés részleteit és a tárolás szolgáltatás kérés. SAS-kód lehetővé teszi a hozzáférést időalapú az ügyfelek számára anélkül hogy felfedné titkos kulcsot a tárfiók.

A biztonsági Társítások azt jelenti, hogy azt is adja meg egy ügyfél korlátozott engedélyekkel, az egy adott időszakban, és meghatározott engedélyekkel vannak beállítva a tárfiókban lévő objektumokhoz. Azt is ezen engedélyek korlátozott anélkül, hogy a fiók hozzáférési kulcsait megosztásához.

### <a name="ip-level-storage-isolation"></a>IP-szintű tárolási elkülönítési
Állítson be tűzfalak, és az IP-címtartományok definiálása a megbízható ügyfeleket. Az IP-címtartományok, csak a definiált tartományon belüli IP-cím ügyfelek csatlakozhatnak [Azure Storage](https://docs.microsoft.com/azure/storage/storage-security-guide).

IP-tárolási adatok védelme a jogosulatlan felhasználók egy hálózati mechanizmus, amellyel lefoglalni egy dedikált vagy dedikált IP-tárolási forgalom-alagúton keresztül.

### <a name="encryption"></a>Titkosítás
Az Azure a következő típusú adatok védelme érdekében titkosítási kínál:
-   Az átvitel során titkosítás

-   Titkosítás inaktív állapotban

#### <a name="encryption-in-transit"></a>Az átvitel során titkosítás
Titkosítás az átvitel során, akkor egy eszköz adatok védelmének hálózatok közötti átvitelekor. Az Azure Storage segítségével védetté tehet adatokat:

-   [Átviteli szintű titkosítást](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-in-transit), például a HTTPS vagy abból az Azure Storage-adatok átvitel során.

-   [Hozzá kell fűznie titkosítási](../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), például az Azure fájlmegosztások SMB 3.0 titkosítást.

-   [Ügyféloldali titkosítás](https://docs.microsoft.com/azure/storage/storage-security-guide#using-client-side-encryption-to-secure-data-that-you-send-to-storage), az adatok titkosítása a tároló előtt, és az adatok visszafejtéséhez után tárolási kivitt.

#### <a name="encryption-at-rest"></a>Aktívan titkosítása
A legtöbb szervezet számára [adatok titkosítását](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) kötelező lépés adatvédelmi, megfelelőségi és az adatok közös joghatóság alá felé. Nincsenek adatok, amelyek "Inaktív" titkosítás három Azure funkciókat:

-   [Storage szolgáltatás titkosítási](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-at-rest) lehetővé teszi, hogy a társzolgáltatás automatikusan adatok titkosítása az Azure Storage írásakor.

-   [Ügyféloldali titkosítás](https://docs.microsoft.com/azure/storage/storage-security-guide#client-side-encryption) aktívan titkosítási funkcióval is rendelkezik.

-   [Az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) lehetővé teszi az operációs rendszer és az infrastruktúra-szolgáltatási virtuális gép által használt adatok lemezek titkosításához.

#### <a name="azure-disk-encryption"></a>Azure Disk Encryption
[Az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) a virtuális gépek (VM) segít cím szervezeti biztonsági és megfelelőségi követelményeknek a Virtuálisgép-lemezek (beleértve a rendszerindító és adatlemezek) titkosítja a kulcsokat és szabályozhatja a házirendek [Azure Key Tároló](https://azure.microsoft.com/services/key-vault/).

A lemez titkosítása megoldást a Windows alapuló [Microsoft BitLocker meghajtótitkosítás](https://technet.microsoft.com/library/cc732774.aspx), és a Linux-megoldás alapul [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

A megoldás a következő szituációkat IaaS virtuális gépek Microsoft Azure-ban engedélyezésekor:
-   Az Azure Key Vault integrációja

-   Standard szint virtuális gépek: A, D, DS, G, GS és így tovább, adatsorozat IaaS virtuális gépeket

-   A Windows és Linux IaaS virtuális gépeket titkosítás engedélyezése

-   Az operációs rendszer és az adatok titkosításának letiltása Windows IaaS virtuális meghajtók

-   Linux IaaS virtuális gépeket az adatmeghajtók titkosításának letiltása

-   Az infrastruktúra-szolgáltatási virtuális gépeken futó Windows-ügyfél operációs rendszert futtató titkosítás engedélyezése

-   Csatlakoztatási elérési utakat tartalmazó kötetek titkosítás engedélyezése

-   A Linux virtuális gépeken (RAID) szétosztott lemezzel konfigurált titkosítás engedélyezése használatával [mdadm](https://en.wikipedia.org/wiki/Mdadm)

-   A Linux virtuális gépeken titkosítás engedélyezése [LVM (logikai kötetkezelő)](https://msdn.microsoft.com/library/windows/desktop/bb540532) adatlemezek

-   A Windows virtuális gépeken, a tárolóhelyek használatával konfigurált titkosítás engedélyezése

-   Minden Azure-nyilvános régió támogatottak.

A megoldás nem támogatja a következő forgatókönyvek, szolgáltatások és technológia a kiadásban:

-   Az alapszintű csomag IaaS virtuális gépeket

-   Linux IaaS virtuális gépeket egy operációs rendszer meghajtóján titkosításának letiltása

-   Infrastruktúra-szolgáltatási virtuális gépeket, a klasszikus virtuális gép létrehozási módszer használatával létrehozott

-   Integráció a helyszíni kulcskezelő szolgáltatás

-   Az Azure Files (megosztott fájlrendszer), a hálózati fájlrendszer (NFS), a dinamikus köteteket és a Windows alapú szoftveres RAID-rendszerek használatára konfigurált virtuális gépek

## <a name="sql-azure-database-isolation"></a>Az SQL Azure Database elkülönítési
Az SQL Database a Microsoft Cloud egy, a piacvezető Microsoft SQL Server motoron alapuló relációs adatbázis-szolgáltatása, amely képes a kritikus fontosságú számítási feladatok kezelésére. SQL-adatbázis kínál előre jelezhető adatok elkülönítési szinten fiók geográfiai / régió alapján, és a hálózat alapján – mindezt felügyelet.

### <a name="sql-azure-application-model"></a>Az SQL Azure-alkalmazásokban modell

[Microsoft SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started) adatbázis az SQL Server technológiáira felhőalapú relációs adatbázis-szolgáltatás. Egy magas rendelkezésre állású, méretezhető, több-bérlős dokumentumadatbázis-szolgáltatás, a felhő Microsoft által üzemeltetett biztosít.

Az alkalmazás szempontjából SQL Azure biztosít a következő hierarchia: minden szinthez tartozik egy-a-többhöz elszigetelési szintek az alábbi.

![Az SQL Azure-alkalmazásokban modell](./media/azure-isolation/azure-isolation-fig10.png)

A fiókja és -előfizetése a Microsoft Azure platform fogalmak rendelje hozzá a számlázási és kezelése.

Logikai kiszolgálók és adatbázisok SQL Azure-specifikus fogalmak és kezelt SQL Azure-ban megadott OData és TSQL-felületek használatával vagy az SQL Azure-portál, Azure-portál integrálva.

SQL Azure-kiszolgálók nem fizikai gép vagy Virtuálisgép-példányok, ehelyett olyan adatbázisok, felügyeleti és biztonsági szabályzataival, az úgynevezett "logikai master" tárolt megosztása gyűjteményei adatbázis.

![SQL Azure](./media/azure-isolation/azure-isolation-fig11.png)

Logikai fő adatbázisok a következők:

-   A kiszolgálóhoz való csatlakozáshoz használt SQL-bejelentkezésekben

-   Tűzfalszabályok

Számlázási és használati adatokat az SQL Azure az azonos logikai kiszolgáló-adatbázisok nem garantált, hogy az SQL Azure fürt egyazon fizikai példányán kell helyette alkalmazások kapcsolódáskor biztosítania kell a célként megadott adatbázis nevét.

Az ügyfél szempontjából, egy logikai kiszolgáló akkor jön létre egy földrajzi grafikus régióban, míg a tényleges kiszolgáló létrehozása történik, a fürtök régióban egyikében.

### <a name="isolation-through-network-topology"></a>Hálózati topológia elkülönítését

Egy logikai kiszolgáló akkor jön létre, és a DNS-névvel regisztrálva van, a DNS-beli név pontok az úgynevezett "átjáró" címéhez az adott adatközpontban, ahol a kiszolgáló lett helyezve.

(Virtuális IP-cím) VIP mögött átjáró állapotmentes szolgáltatások gyűjteménye van. Általánosságban elmondható átjárók beolvasása érintett koordinációs több adatforrást (master adatbázis, felhasználói adatbázis stb.) között szükség esetén. Átjáró szolgáltatások valósítja meg a következő:
-   **TDS kapcsolat proxy használatát az ügynökökön.** Ez magában foglalja a háttér-fürt felhasználói adatbázis keresése. a bejelentkezési folyamat végrehajtása, majd továbbítja a a TDS-csomagokat a háttér és vissza.

-   **Adatbázis-kezelő.** Ez magában foglalja a CREATE/ALTER/DROP database műveletek elvégzéséhez munkafolyamatok gyűjteményét végrehajtási. A Helyadatbázis-műveletekhez elemzés TDS-csomagokat, vagy explicit OData API-kat is elindítható.

-   CREATE/ALTER/DROP bejelentkezési vagy felhasználói műveletek

-   Logikai kiszolgáló felügyeleti műveletei OData API-n keresztül

![Hálózati topológia elkülönítését](./media/azure-isolation/azure-isolation-fig12.png)

A réteg mögött az átjárók "Háttér" nevezik. Ez az összes adat tárolására egy magas rendelkezésre állású módon. Minden adat, különállónak tartozik egy "partíció" vagy "failover unit", azok legalább három replikák rendelkezik. Replikák tárolása és replikálása SQL Server-motor és gyakran nevezik "fabric" feladatátvevő rendszer által felügyelt.

Általában a háttér-rendszer nem kommunikál a más rendszerekkel kimenő biztonsági okokból. Ez a rendszer az előtér-(átjáró) tartozó van fenntartva. Az átjáró réteg gépein korlátozott jogosultságokkal védelemmel az olyan jellegű mechanizmusaként támadási felületének minimalizálása érdekében a háttér-gépeken.

### <a name="isolation-by-machine-function-and-access"></a>Elkülönítési funkcióval és hozzáférés
Az SQL Azure (áll másik gépre funkciók futó szolgáltatásokat. SQL Azure oszlik "Háttér" felhő adatbázis és az "előtér" (átjáró vagy felügyeleti) környezetek, a forgalom csak folyamatos háttér-be és ki nem általános elvet. Az előtér-környezet képes kommunikálni a külvilág egyéb szolgáltatások, és általában csak korlátozott mértékben engedélyeket a háttér-(elegendő hívni a belépési pontok meghívásához szükséges).

## <a name="networking-isolation"></a>Hálózati elkülönítési
Az Azure-telepítés hálózati elkülönítési rétege rendelkezik. Az alábbi ábrán látható Azure biztosít az ügyfelek hálózati elkülönítési különböző rétegek. Ezek a rétegek az Azure platformon, maga a natív és a felhasználó által meghatározott funkciókat. Bejövő az internetről, Azure DDoS el vannak különítve a nagyméretű támadások elleni Azure. A következő elkülönítési réteg az ügyfél által megadott nyilvános IP-címek (végpont), amelyek segítségével meghatározhatja, melyik forgalmat is továbbítása a felhőszolgáltatás a virtuális hálózathoz. Natív Azure virtuális hálózatelkülönítés biztosítja minden más hálózati teljes elkülönítést, valamint, hogy csak a forgalom metódusok és konfigurált felhasználói elérési útját. Ezek elérési útja és módszerek a következő rétege, NSG-ket, UDR és virtuális hálózati berendezések helyének elkülönítési határokat védelme érdekében az alkalmazások központi telepítéseit a védett hálózat létrehozásához.

![Hálózati elkülönítési](./media/azure-isolation/azure-isolation-fig13.png)

**A forgalom elkülönítése:** A [virtuális hálózati](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) van a forgalom elkülönítési határt alkotnak, az Azure platformon. Virtuális gépek (VM) több virtuális hálózat nem kommunikálnak közvetlenül a másik virtuális hálózatot, virtuális gépek akkor is, ha mindkét virtuális hálózat az azonos ügyfél jönnek létre. Elkülönítési kritikus tulajdonság, amely biztosítja a felhasználói virtuális gépeket, kommunikációs titkos virtuális hálózaton belül marad.

[Alhálózati](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview#subnets) elkülönítési a virtuális hálózat IP-címtartomány alapján további réteget biztosít. IP-címtartomány a virtuális hálózaton, a virtuális hálózatot lehet osztani érdekében több alhálózatra a szervezet és a biztonság. Egy VNeten belül az alhálózatokra üzembe helyezett virtuális gépek és a PaaS szerepkörpéldányok (ugyanaz vagy különböző) további konfigurálás nélkül is tudnak egymással kommunikálni. Beállíthatja úgy is [hálózati biztonsági csoport (NSG-k)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview#network-security-groups-nsg) a hozzáférési lista (ACL) a NSG beállított szabályok alapján Virtuálisgép-példány a hálózati adatforgalom engedélyezéséhez vagy letiltásához. Az NSG-ket alhálózatokhoz vagy az alhálózaton belüli virtuálisgép-példányokhoz lehet hozzárendelni. Ha az NSG-t hozzárendelik egy alhálózathoz, az ACL-szabályok érvényesek lesznek az alhálózatban lévő összes virtuálisgép-példányra.

## <a name="next-steps"></a>További lépések

- [Hálózati elkülönítési beállítások gépek vannak a Windows Azure virtuális hálózatok](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/)

Ez magában foglalja a klasszikus előtér- és a forgatókönyvben egy adott háttér-hálózat vagy az alhálózat gépek ahol csak engedélyezhetik bizonyos ügyfelek vagy más számítógépek egy adott végpont is egy IP-címek engedélyezési listája alapján való kapcsolódáshoz.

- [Számítási elkülönítési](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)

A Microsoft Azure biztosít a különböző számítási felhőszolgáltatásokhoz, amelyek tartalmazzák a számítási példányokért széles kijelölt & szolgáltatások, amely is fel-le automatikusan az alkalmazás vagy a vállalat igényeinek.

- [Tárolási elkülönítési](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)

A Microsoft Azure elválasztja az ügyfél Virtuálisgép-alapú számítási tárolóból. Ez az elkülönítés lehetővé teszi, hogy számítási és tárolási méretezését, így könnyebben adja meg a több-bérlős és elkülönítés. Ezért Azure Storage futtatásával külön hardverre nincs hálózati kapcsolattal rendelkező Azure számítási kivéve logikailag. Összes kérelem futtatása, HTTP vagy HTTPS ügyfél beállítása alapján.

