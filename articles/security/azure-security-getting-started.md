---
title: "Ismerkedés a Microsoft Azure biztonsági |} Microsoft Docs"
description: "Ez a cikk a Microsoft Azure biztonsági képességei és, hogy az eszközök és a szervezetek számára általános szempontok áttekintése."
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 8d8a0088-c85a-48e7-bd04-2bc7b78b0691
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2017
ms.author: yurid
ms.openlocfilehash: eb53ed852b6175fbc7faea44a243e8c7d5ce1753
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="getting-started-with-microsoft-azure-security"></a>A Microsoft Azure Security használatának első lépései
Build, vagy a felhőbeli szolgáltató telepíthet át informatikai eszközök, az alkalmazások és a szolgáltatások és a biztonsági a felhő alapú eszközök kezelésére adathordozóira vezérlők adatok védelme érdekében, hogy szervezete képességek vannak hagyatkoznia.

Az Azure infrastruktúráját úgy tervezték, hogy képes legyen ügyfelek millióit egyidejűleg kiszolgáló alkalmazásokat üzemeltetni, és olyan megbízható alapot nyújtson, amely megfelel a vállalatok biztonsági igényeinek. Számos konfigurálható biztonsági beállítással is rendelkezik, amelyek testreszabásával a különböző környezetek egyedi követelményeinek megfelelő biztonsági megoldások alakíthatók ki.

Az Azure biztonsági szolgáltatásait áttekintő cikkünkben a következőkkel foglalkozunk:

* Az Azure-szolgáltatások és funkciók segítségével biztonságossá tétele a szolgáltatások és az adatok Azure-ban.
* A Microsoft hogyan segít megvédeni az adatok és alkalmazások az Azure-infrastruktúra biztosítja.

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés
Az informatikai infrastruktúrához, adatokhoz és alkalmazásokhoz való hozzáférés szabályozása alapvető fontosságú. A Microsoft Azure ezeket a képességeket nyújt, például az Azure Active Directory (Azure AD), az Azure Storage és a támogatási szolgáltatások számos szabványok és API-k által.

[Az Azure AD](../active-directory/active-directory-whatis.md) identitás tárház és motor, amely hitelesítési, engedélyezési és hozzáférés-vezérlés biztosít a szervezet felhasználók, csoportok, és objektumokat. Az Azure AD emellett hatékony módszert kínál a fejlesztők számára az identitáskezelésnek az alkalmazásokba való integrálásához. Szabványos protokollok, mint [SAML 2.0](https://en.wikipedia.org/wiki/SAML_2.0), [WS-Federation](https://msdn.microsoft.com/library/bb498017.aspx), és [OpenID Connect](http://openid.net/connect/) bejelentkezési lehetséges adja meg a platformokon, például .NET, Java, Node.js és a PHP.

A REST-alapú Graph API használata bármelyik platformról lehetővé teszi a fejlesztők számára a címtár olvasását és írását. Támogatásával [OAuth 2.0](http://oauth.net/2/), fejlesztők létrehozhatják mobil és webes alkalmazásokhoz, amely integrálható a Microsoft és külső webes API-khoz, és állítsa be a saját biztonságos webes API-k. Nyílt forráskódú klienskódtárak érhetők el .Net, Windows Áruház, iOS és Android platformra, további kódtárak pedig fejlesztés alatt állnak.

### <a name="how-azure-enables-identity-and-access-management"></a>Hogyan biztosítja az Azure az identitás- és hozzáférés-kezelést?
Az Azure AD használható szervezete önálló, felhőbeli címtáraként, illetve a meglévő helyszíni Active Directoryval integrált megoldásként. Az integrációs szolgáltatások közé tartozik például a címtár-szinkronizálás és az egyszeri bejelentkezés (SSO). Ezek révén a meglévő helyszíni identitások az kiterjeszti a felhőbe, és a rendszergazdai és felhasználói élmény javítása.

Az identitás- és hozzáférés-kezelés egyéb funkciói többek között a következők:

* Az Azure AD lehetővé teszi az [SSO](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) bejelentkezést a SaaS-alkalmazások számára a futtatási helyüktől függetlenül. Egyes alkalmazások az Azure AD-vel összevontan működnek, mások jelszavas egyszeri bejelentkezést használnak. Az összevont alkalmazások emellett a felhasználóátadást és a jelszótárolást is támogathatják.
* Az [Azure Storage](https://azure.microsoft.com/services/storage/)-ban tárolt adatokhoz való hozzáférést hitelesítés védi. Minden tárfiók elsődleges kulccsal rendelkezik ([tárfiók kulcsa](https://msdn.microsoft.com/library/azure/ee460785.aspx), vagy SAK) és egy másodlagos titkos kulcs (a közös hozzáférésű jogosultságkódot, vagy SAS).
* Az Azure AD Identity keresztül összevonási szolgáltatás segítségével biztosítja [Active Directory összevonási szolgáltatások](../active-directory/fundamentals-identity.md), szinkronizáláshoz és a replikáció a helyszíni címtárakban.
* [Az Azure multi-factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) a többtényezős hitelesítési szolgáltatás, amely megköveteli a felhasználóktól bejelentkezések ellenőrizheti a mobilalkalmazás, telefonhívással vagy szöveges üzenetben. Használat és az Azure AD segítségével biztonságos a helyszíni erőforrások az Azure multi-factor Authentication kiszolgálóval és az egyéni alkalmazások és könyvtárak a SDK használatával.
* [Azure AD tartományi szolgáltatások](https://azure.microsoft.com/services/active-directory-ds/) lehetővé teszi, hogy az Azure virtuális gépek csatlakoztatása tartományhoz tartományvezérlők üzembe helyezésének nélkül. Ezek a virtuális gépek jelentkezzen be a vállalati Active Directory hitelesítő adataival, és a tartományhoz csatlakozó virtuális gépek felügyelete az Azure virtuális gépeken futó biztonsági alapterveket kényszerítéséhez csoportházirend használatával.
* [Az Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) egy magas rendelkezésre állású globális identitás szolgáltatást biztosít a felhasználók felé néző alkalmazások száz millió identitások alkalmazkodnak. Mobil- és webes platformokba is integrálható. A felhasználók bármikor beléphet, testre szabható felhasználói élmény mellett az alkalmazások új vagy meglévő közösségi fiókjaik használatával.

## <a name="data-access-control-and-encryption"></a>Adatok hozzáférés-vezérlése és titkosítása
A Microsoft a feladatkörök elkülönítésének és a [legkisebb jogosultságnak](https://en.wikipedia.org/wiki/Principle_of_least_privilege) az elvét alkalmazza az Azure üzemeltetése során. Ahhoz, hogy az Azure támogatási személyzete hozzáférhessen az Ön adataihoz, a kifejezett engedélyére van szükség. A naplózott, csak a szükséges időpontban kiadható engedélyt a megbízás befejezését követően visszavonjuk.

Azure is biztosít több lehetőségeket biztosít az átvitel során, és inaktív adatok védelmét. Ez magában foglalja az adatok, fájlok, alkalmazások, szolgáltatások, kommunikációs és meghajtók titkosítását. Információk az Azure-ban helyezés előtt titkosítja, és is a helyszíni adatközpontját a kulcsok tárolása.

![Microsoft Antimalware szolgáltatás az Azure-ban](./media/azure-security-getting-started/sec-azgsfig1.PNG)

### <a name="azure-encryption-technologies"></a>Azure titkosítási technológiák
A rendszergazdai hozzáféréssel az előfizetési környezetben végrehajtott eseményekről az [Azure AD jelentéskészítési funkciójával](../active-directory/active-directory-reporting-audit-events.md) gyűjthet adatokat. Konfigurálható [a BitLocker meghajtótitkosítás](https://technet.microsoft.com/library/cc732774.aspx) Azure bizalmas adatokat tartalmazó virtuális merevlemezen.

Az Azure egyéb, az adatok biztonságát elősegítő funkciói a következők:

* Alkalmazás fejlesztők létrehozhatják azokat az alkalmazásokat, azok telepítését az Azure-ban a Windows titkosítási [CryptoAPI](https://msdn.microsoft.com/library/ms867086.aspx) és a .NET-keretrendszer.
* Teljes ellenőrzés Azure Blob Storage ügyféloldali titkosítás a kulcsok. A tárolószolgáltatás soha nem látja a kulcsokat, és nem képes az adatok visszafejtésére.
* [Az Azure Rights Management (Azure RMS)](https://technet.microsoft.com/library/jj585026.aspx) (az a [RMS SDK](https://msdn.microsoft.com/library/dn758244.aspx)) fájl- és adatok szintű titkosítást és adatvesztés megelőzési keresztül csoportházirend-alapú hozzáférés-felügyeletet biztosít.
* Az Azure által támogatott [tábla- és oszlop titkosítás (TDE/törlése)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database.aspx) az SQL Server virtuális gépeket, és támogatja a külső helyszíni kulcskezelő kiszolgálók adatközpontokban.
* A tárfiókkulcsok, a közös hozzáférésű jogosultságkódok, a felügyeleti tanúsítványok és más kulcsok minden Azure-bérlő esetében egyediek.
* Azure [StorSimple](http://www.microsoft.com/server-cloud/products/storsimple/overview.aspx) hibrid tárolási Azure Storage való feltöltés előtt titkosítja a 128 bites nyilvános/titkos kulcspár keresztül.
* Azure támogatja, és számos titkosítási mechanizmusok, beleértve az SSL/TLS, az IPsec és az AES, attól függően, hogy az adattípusok, tárolók és átviteli módokat használja.

## <a name="virtualization"></a>Virtualizáció
Az Azure platform virtualizált környezetet használ. A felhasználói példányok működhetnek önálló virtuális gépek, amelyek nem rendelkeznek hozzáféréssel a fizikai kiszolgáló, és kikényszeríti a elkülönítés használatával fizikai [processzor (ring-0/ring-3) a jogosultsági szintek](https://en.wikipedia.org/wiki/Protection_ring).

A „Ring 0” a legmagasabb, a „Ring 3” a legalacsonyabb jogosultsági szint. A vendég operációs rendszer fut egy alacsonyabb szintű jogosultságokat igénylő Ring 1, és alkalmazások futnak, a minimális jogosultságokkal rendelkező Ring 3. A fizikai erőforrások ilyen módszerrel történő virtualizálása következtében egyértelműen elkülönül a vendég operációs rendszer és a hipervizor, ez pedig a két rendszer további biztonsági elkülönülését eredményezi.

Az Azure hipervizor úgy viselkedik, mint egy micro kernel és átadja az összes hardver hozzáférési kérelmek a Vendég virtuális gépek feldolgozásra a fogadó egy VMBus nevű megosztott memória felület használatával. Ez megakadályozza, hogy a felhasználók nyers olvasási, írási és végrehajtási hozzáférést szerezzenek a rendszerhez, és csökkenti a rendszererőforrások megosztásának kockázatát.

![Microsoft Antimalware szolgáltatás az Azure-ban](./media/azure-security-getting-started/sec-azgsfig2.PNG)

### <a name="how-azure-implements-virtualization"></a>Hogyan valósítja meg az Azure a virtualizációt?
Azure van megvalósítva a hipervizor és a háló vezérlő ügynök által konfigurált hipervizor tűzfal (csomagszűrők) használja. Ez segít megvédeni a bérlőket a jogosulatlan hozzáféréstől. Alapértelmezés szerint minden forgalmat blokkol, amikor létrejön egy virtuális gép, és ezután a fabric controller ügynök konfigurálja a csomag-szűrő hozzáadása *szabályok és kivételek* engedélyezett forgalom engedélyezésére.

Az itt programozott szabályok két kategóriába sorolhatók:

* **Számítógép-konfiguráció vagy infrastruktúra szabályok**: alapértelmezés szerint minden kommunikációja blokkolva van. A virtuális gép DHCP és DNS- adatforgalmat engedélyezi kivételek vannak. Virtuális gépek is forgalmat küldeni a "nyilvános" internet és más virtuális gépek a fürt és az operációs rendszer aktiválási kiszolgálói forgalmat küldeni. Engedélyezett kimenő célja a virtuális gépek listája nem tartalmazza a Azure útválasztó alhálózatok, az Azure felügyeleti biztonsági vége és más Microsoft tulajdonságai.
* **Szerepkör konfigurációs fájl**: Ez határozza meg, hogy a bejövő hozzáférés-vezérlési listái (ACL) alapján a bérlő szolgáltatásmodellt. Például ha egy bérlőt egy előtér-webkiszolgáló 80-as porton egyes virtuális gépen, majd Azure megnyitja 80-as TCP-portot, az összes IP-cím beállításakor a végpont a [Azure klasszikus üzembe helyezési modellel](../azure-resource-manager/resource-manager-deployment-model.md). Ha a virtuális gép fut, hátsó vége vagy feldolgozói szerepkör ezt követően megnyitja a feldolgozói szerepkör csak azokra a virtuális gép belül ugyanannak a bérlőnek.

## <a name="isolation"></a>Elkülönítés
Egy másik fontos felhőalapú biztonsági követelmény elválasztását, hogy megakadályozza a jogosulatlan és nem szándékos adatátvitel központi telepítések a megosztott, több-bérlős architektúrák közötti.

Az Azure valósít meg [hálózati hozzáférés-vezérlés](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/) és elkülönítése keresztül VLAN-elkülönítéssel, hozzáférés-vezérlési listákat, terheléselosztók, és az IP-szűrők betöltése. Korlátozza a külső forgalom bejövő portok és protokollok a virtuális gépeken, Ön által meghatározott. Azure valósítja meg a hálózati szűrés hamisított forgalom és megbízható webplatform-összetevők bejövő és kimenő forgalom korlátozása érdekében. Olyan forgalmi szabályzatok települnek a határon lévő védelmi eszközökre, amelyek alapértelmezés szerint letiltják a forgalmat.

![Microsoft Antimalware szolgáltatás az Azure-ban](./media/azure-security-getting-started/sec-azgsfig3.PNG)

Hálózati címfordítással (NAT) különítjük el a belső hálózati forgalmat a külső forgalomtól. A belső forgalom kívülről nem irányítható. A kívülről irányítható [virtuális IP-címeket](http://blogs.msdn.com/b/cloud_solution_architect/archive/2014/11/08/vips-dips-and-pips-in-microsoft-azure.aspx) a rendszer [belső dinamikus IP-címekre](http://blogs.msdn.com/b/cloud_solution_architect/archive/2014/11/08/vips-dips-and-pips-in-microsoft-azure.aspx) fordítja, amelyek csak az Azure-on belül irányíthatók.

Az Azure virtuális gépek a külső forgalom hozzáférés-vezérlési listák segítségével tűzfallal útválasztók, terheléselosztók, és a 3. rétegbeli kapcsolók. Csak bizonyos ismert protokollok engedélyezettek. Hozzáférés-vezérlési listák, hogy a többi VLAN hálózattól-kezelésre szolgáló a Vendég virtuális gépekről származó forgalom korlátozása. Emellett a gazdagép operációs rendszer további IP-szűrők keresztül szűrt forgalom korlátozza a forgalom mindkét adatok kapcsolat és hálózati rétegek.

### <a name="how-azure-implements-isolation"></a>Hogyan valósítja meg az Azure az elkülönítést?
Az Azure Fabric Controller bérlői munkaterheléseket infrastruktúrához kapcsolódó erőforrások lefoglalása felelős, és a gazdagép és virtuális gépek egyirányú kommunikációt kezeli. Az Azure hipervizor kikényszeríti a memória és a folyamat elkülönítése virtuális gépeket, és biztonságosan irányítja a hálózati forgalmat a vendég operációs rendszer bérlők számára. Azure is a bérlők számára, a tárolás és a virtuális hálózatok elkülönítési valósítja meg.

* Minden Azure AD-bérlő logikailag elkülönített biztonsági határokat használatával.
* Az Azure storage-fiókok egyedi összes előfizetéshez, és hozzáférést kell hitelesíteni a tárfiók kulcsának használatával.
* Virtuális hálózatok logikailag elszigetelt egyedi magánhálózati IP-címek, a tűzfalak és a hozzáférés-vezérlési listák IP keresztül. A terheléselosztók végpont-definíciók alapján irányítják át a forgalmat a megfelelő bérlőkhöz.

## <a name="virtual-networks-and-firewalls"></a>Virtuális hálózatok és a tűzfalon
A [elosztott és a virtuális hálózatok](http://download.microsoft.com/download/4/3/9/43902EC9-410E-4875-8800-0788BE146A3D/Windows%20Azure%20Network%20Security%20Whitepaper%20-%20FINAL.docx) Azure súgó gondoskodjon arról, hogy a saját hálózati forgalom logikailag más Azure virtuális hálózat adatforgalmának szigetelve.

![Microsoft Antimalware szolgáltatás az Azure-ban](./media/azure-security-getting-started/sec-azgsfig4.PNG)

Az előfizetés is tartalmaz a több elkülönített magánhálózatot (és tűzfal, a terheléselosztás és a hálózati címfordítás).

Az Azure három elsődleges hálózati elkülönítési szintet biztosít minden Azure-fürtben, hogy logikailag elkülönítse a forgalmat. [Virtuális helyi hálózatok](https://azure.microsoft.com/services/virtual-network/) (VLAN-ok) az Azure-hálózat többi részétől felhasználói forgalom elkülönítésére szolgálnak. A fürtön kívülről terheléselosztók korlátozzák az Azure-hálózathoz való hozzáférést.

Virtuális gépek és a hálózati forgalom meg kell felelnie a hipervizor virtuális kapcsolón keresztül. Az operációs rendszer gyökérkönyvtárában IP-szűrő összetevőjét elkülöníti a Vendég virtuális gépek és a Vendég virtuális gépek egymástól a legfelső szintű virtuális géppel. A bérlő-csomópontok és a nyilvános internethez (az ügyfél szolgáltatás konfigurációja alapján), a többi bérlőtől elkülönítése őket közötti kommunikációt forgalom szűrés hajtja végre.

Az IP-szűrő megakadályozza, hogy a Vendég virtuális gépek:

* Hamisított forgalom létrehozásakor.
* Nem intézett forgalom fogadására.
* Forgalom védett infrastruktúra végpontokra irányítja.
* Küldésekor vagy fogadásakor nem megfelelő szórási forgalmat.

A virtuális gépek alakzatot elhelyezhet [Azure virtuális hálózataihoz](https://azure.microsoft.com/documentation/services/virtual-network/). Ezek a virtuális hálózatok hasonlítanak a helyszíni környezetben konfigurált hálózatokhoz, ahol általában egy virtuális kapcsoló is társul hozzájuk. Az azonos virtuális hálózathoz csatlakozó virtuális gépek kommunikálhatnak egymással további konfiguráció nélkül. Beállíthatja úgy is más alhálózatok virtuális hálózaton belül.

A következő Azure virtuális hálózati technológiák használatával biztonságos kommunikáció érdekében a virtuális hálózaton:

* [**Hálózati biztonsági csoportokkal (NSG-k)**](../virtual-network/virtual-networks-nsg.md). A virtuális hálózat egy NSG-t egy vagy több virtuálisgép-példányok forgalmának ellenőrzésére használhatja. A hálózati biztonsági csoport olyan hozzáférés-vezérlési szabályokat tartalmaz, amelyek engedélyezik vagy megtagadják a forgalmat a forgalom iránya, a protokoll, a forrás címe és portja, illetve a cél címe és portja alapján.
* [**Felhasználó által definiált útválasztási**](../virtual-network/virtual-networks-udr-overview.md). A csomagok útválasztását egy virtuális készüléken keresztül, adja meg a következő ugrás a virtuális hálózati biztonsági készülékek gomba adott alhálózatra áramló csomagok létrehozásakor felhasználó által definiált útvonalak által szabályozható.
* [**IP-továbbítás**](../virtual-network/virtual-networks-udr-overview.md). A virtuális hálózat biztonsági eszközének képesnek kell lennie a nem neki címzett bejövő forgalom fogadására. Ahhoz, hogy a virtuális gép címzett más célhelyre irányított forgalom fogadását, engedélyezi a virtuális gép IP-továbbítást.
* [**Alagúthasználat kényszerítése**](../vpn-gateway/vpn-gateway-about-forced-tunneling.md). A kényszerített bújtatás lehetővé átirányítási vagy "kényszerített" az összes internetes-forgalmat a virtuális hálózat a virtuális gépek által generált biztonsági másolatot a helyszíni helyre vizsgálati és naplózási pont-pont VPN-alagúton keresztül
* [**Végponti ACL-eket**](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Azt is szabályozhatja, hogy mely számítógépek engedélyezettek bejövő kapcsolatokat az internetről egy virtuális géphez a virtuális hálózaton végponti ACL-eket meghatározásával.
* [**Partnerhálózati biztonsági megoldások**](https://azure.microsoft.com/marketplace/). Számos hálózati biztonsági partnermegoldások, amely az Azure piactéren érheti el.

### <a name="how-azure-implements-virtual-networks-and-firewalls"></a>Hogyan Azure megvalósítja-e a virtuális hálózatok és a tűzfalon
Azure csomagokat szűrő tűzfalak megvalósítja az összes gazdagép és Vendég virtuális gép alapértelmezés szerint. Windows operációs Rendszeri lemezképek az Azure piactérről is a Windows tűzfal alapértelmezés szerint engedélyezve van. Nyilvánosan elérhető Azure-hálózatok vezérlő kommunikáció a szervezet határain terheléselosztók IP hozzáférés-vezérlési listák felhasználói rendszergazdák által kezelt alapján.

Amikor az Azure a normál működés részeként vagy egy katasztrófa során áthelyezi a felhasználói adatokat, ezt privát, titkosított kommunikációs csatornákon keresztül teszi. A virtuális hálózatok és a tűzfalon használandó Azure által alkalmazott egyéb funkciók a következők:

* **Natív gazdagép tűzfalának**: Azure Service Fabric és az Azure Storage futtatnak egy natív az operációs rendszer, amelyek nem hipervizor rendelkezik. Ezért a windows tűzfal az előző két szabálykészlet van beállítva. A teljesítmény optimalizálása érdekében a tároló natív módban fut.
* **Gazdagép tűzfalának**: A gazdagép tűzfalának a gazdagép operációs rendszere, amely futtatja a hipervizor védelme érdekében. A szabályok vannak programozott engedélyezése csak a Service Fabric controller és irányítják a mezőkbe felvegye a gazda operációs rendszer egy adott portot. A kivételek közé tartozik még a DHCP- és DNS-válaszok engedélyezése. Azure használja a gép konfigurációs fájlját a sablon a tűzfalszabályok a gazda operációs rendszer. A gazdagéphez csak forrásokból ismert, hitelesített kommunikáció beállított Windows tűzfal védi külső támadások elleni.
* **Vendég tűzfal**: replikálja a szabályok azonban programozott különböző szoftverek (például a vendég operációs rendszer a Windows tűzfal darab) a virtuális gép kapcsoló csomag szűrőben. A Vendég virtuális gép tűzfal beállítható úgy, hogy kommunikáció korlátozása, hogy vagy a Vendég virtuális gépen, akkor is, ha a kommunikáció a kiszolgáló IP-szűrő konfigurációja által engedélyezett. Például előfordulhat, hogy kíván használni a Vendég virtuális gép tűzfal két a Vnetek csatlakozni egy másik konfigurált közötti kommunikációt.
* **Tárolási tűzfal (Keretrendszer)**: A tűzfal, a tárolási kezelőfelületre szűrők forgalmat csak a 80-as/443-as és egyéb szükséges segédprogram portokkal on szeretné lebonyolítani. A tűzfal, a tárolási háttérben futó előtér-tárolókiszolgálók származnia kommunikációs korlátozza.
* **Virtuális hálózati átjáró**: A [Azure virtuális hálózati átjáró](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) a munkaterhelés Azure virtuális hálózatra csatlakozik a helyszíni helyek létesítmények közötti átjáróként is szolgál. A helyszíni helyekhez való csatlakozáshoz szükséges [IPsec-helyek VPN-alagutat](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), vagy [ExpressRoute](../expressroute/expressroute-introduction.md) kapcsolatok. Az IPsec/IKE VPN-alagutat az átjárók IKE kézfogások végrehajtani, és létrehozni az IPsec S2S VPN-alagutat a virtuális hálózatok és a helyszíni helyek közötti. Virtuális hálózati átjárók is leáll [pont-pont VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md).

## <a name="secure-remote-access"></a>Biztonságos távoli elérése
A felhőben tárolt adatok esetében megfelelő védelmi eszközöket kell alkalmazni a biztonsági rések elkerülése, illetve az átvitel közben az adatok bizalmas jellegének és integritásának fenntartása érdekében. Ide tartoznak a hálózati vezérlők, amelyek illeszkednek a szervezetek házirendalapú, ellenőrizhető identitás- és hozzáférés-felügyeleti mechanizmusaihoz.

A beépített titkosítási technológia lehetővé teszi a kommunikáció titkosítását az egyes üzemelő példányokon belül és azok között, az Azure-régiók között, valamint az Azure és a helyszíni adatközpontok között. Virtuális gépek keresztül rendszergazdai hozzáférés [távoli asztali munkamenetek](../virtual-machines/windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json), [távoli Windows PowerShell](http://blogs.technet.com/b/heyscriptingguy/archive/2013/09/07/weekend-scripter-remoting-the-cloud-with-windows-azure-and-powershell.aspx), és mindig titkosítja az Azure-portálon.

Biztonságos kiterjesztése a helyszíni adatközpontját a felhőre, Azure biztosít a [telephelyek közötti VPN](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) és [pont – hely típusú VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md), és a dedikált hivatkozások [ExpressRoute](../expressroute/expressroute-introduction.md) (az Azure virtuális hálózataihoz VPN-kapcsolaton keresztül kapcsolatok titkosítása).

### <a name="how-azure-implements-secure-remote-access"></a>Hogyan valósítja meg az Azure a biztonságos távoli hozzáférést?
Az Azure portál mindig kell hitelesíteni, és igényelnek-e az SSL/TLS. A felügyeleti tanúsítványokat konfigurálhatja úgy, hogy engedélyezzék a biztonságos felügyeletet. Például a szabványos biztonsági protokollokat [SSTP](https://technet.microsoft.com/magazine/2007.06.cableguy.aspx) és [IPsec](https://en.wikipedia.org/wiki/IPsec) teljes mértékben támogatottak.

Az [Azure ExpressRoute](../expressroute/expressroute-introduction.md) használatával privát kapcsolatok hozhatók létre az Azure-adatközpontok és a helyszíni vagy a bérelt kiszolgálói környezetben üzemelő infrastruktúra között. Az ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át. További megbízhatóságát, gyorsabb sebességű, kisebb késések és nagyobb biztonságot nyújtana tipikus internetes hivatkozások biztosítanak. Néhány esetben az adatok közötti átviteléhez a helyszíni helyek és Azure ExpressRoute kapcsolattal is partíciónként akár jelentős költségelőnyökhöz juthat.

## <a name="logging-and-monitoring"></a>Naplózás és figyelés
Azure biztonsági-kapcsolódó eseményeket, amelyek létrehozzák elővizsgálati hitelesített naplózása biztosít, és azt kell lennie az illetéktelen módosítás ellen védett fejthetők vissza. Ez magában foglalja a rendszer-információkat, például a virtuális gépek Azure-infrastruktúra és az Azure AD and security event logs. Biztonsági figyelésének tartalmazza, például a DHCP vagy DNS-kiszolgáló IP-címek; változásait események gyűjtése Kísérlet történt a hozzáférési portok, protokollok vagy IP-címek zárolt azért; biztonsági házirend, vagy a tűzfal beállításainak; változásai fiók vagy csoport létrehozása; nem várt folyamatok vagy és illesztőprogram-telepítőfájl.

![Microsoft Antimalware szolgáltatás az Azure-ban](./media/azure-security-getting-started/sec-azgsfig5.PNG)

A naplók rögzítik a rendszerjogosultságú felhasználói hozzáféréseket és tevékenységeket, a hitelesített és jogosulatlan hozzáférési kísérleteket, a rendszerkivételeket, és ezeket az információbiztonsági eseményeket a rendszer a megadott ideig megőrzi. A naplók megőrzési idejét Ön választhatja meg, mivel a naplógyűjtés és -megőrzés konfigurálását a saját igényeihez igazíthatja.

### <a name="how-azure-implements-logging-and-monitoring"></a>Hogyan valósítja meg az Azure a naplózást és a figyelést?
Az Azure felügyeleti ügynököket (MA) és Azure Security Monitor- (ASM-) ügynököket helyez üzembe az összes felügyelt számítási, tárolási vagy hálócsomópontban, legyen az natív vagy virtuális. Minden felügyeleti ügynök úgy van beállítva, hogy az Azure -tanúsítványtárolóból kapott tanúsítvánnyal hitelesítse magát egy szolgáltatási csoport tárfiókján, és előre konfigurált diagnosztikai és eseményadatokat továbbítson a tárfiókba. Ezek az ügynökök nem települnek az ügyfelek virtuális gépeire.

Az Azure rendszergazdák a naplók hitelesített és ellenőrzött elérésére szolgáló webes portálon keresztül férhetnek hozzá a naplókhoz. A rendszergazdák feldolgozhatják, szűrhetik és elemezhetik a naplókat, illetve összefüggéseket kereshetnek közöttük. Az Azure szolgáltatási csoport naplók számára fenntartott tárfiókja védve van a közvetlen rendszergazdai hozzáféréssel szemben, így megakadályozható a naplók illetéktelen módosítása.

Microsoft gyűjti a naplókat, a Syslog-protokollt használó hálózati eszközöket, és a Microsoft naplózási szolgáltatások (ACS) használó kiszolgálók. Ezek a naplók, amelyből gyanús események kapcsolatos riasztások akkor jönnek létre jelentkezzen az adatbázisba kerülnek. A rendszergazda elérheti és elemezheti ezeket a naplókat.

Az [Azure Diagnostics](https://msdn.microsoft.com/library/azure/gg433048.aspx) az Azure diagnosztikai szolgáltatása, amely lehetővé teszi diagnosztikai adatok gyűjtését az Azure-ban futó alkalmazásokról. Ez az a Hibakeresés és hibaelhárítás, méri a teljesítményt, erőforrás-használat, a forgalom elemzése, a kapacitástervezési figyelés és naplózás diagnosztikai adatokat. A diagnosztikai adatok begyűjtését követően az adatok egy Azure-tárfiókba továbbíthatók megőrzés céljából. Átvitel vagy ütemezése vagy az igény szerinti.

## <a name="threat-mitigation"></a>Fenyegetés-megoldás
Az elkülönítés, a titkosítás és a szűrés mellett az Azure számos veszélyelhárítási mechanizmust és folyamatot alkalmaz az infrastruktúra és a szolgáltatások védelme érdekében. Ide tartoznak azok a belső funkciók és technológiák, amelyek a magasabb szintű fenyegetések, mint például a szolgáltatásmegtagadással járó támadás (DDoS), a jogosultságszint-emelés vagy az [OWASP Top-10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project) listán szereplő fenyegetések felismerésére és orvoslására szolgálnak.

Ezeket a biztonsági funkciókat és kockázatkezelési eljárásokat a Microsoft a felhőalapú infrastruktúra biztonságossá tétele és a biztonsági események kockázatának csökkentése érdekében alkalmazza. Abban az esetben, ha az esemény akkor következik be, a biztonsági incidens Management (SIM) csoport a Microsoft Online biztonsági szolgáltatásait és megfelelőségi (OSSC) csapaton belüli készen áll a bármikor válaszol.

### <a name="how-azure-implements-threat-mitigation"></a>Hogyan valósítja meg az Azure a veszélyelhárítást?
Azure rendelkezik biztonsági vezérlők fenyegetés megoldás megvalósításához és ügyfelek csökkentik a környezetükben potenciális fenyegetéseket. Az alábbi lista képességeit összegzi a veszély csökkentése Azure által kínált:

* [Az Azure kártevőirtó](azure-security-antimalware.md) összes infrastruktúra-kiszolgálók alapértelmezés szerint engedélyezve van. Opcionálisan engedélyezheti azt a saját virtuális gépekről.
* A Microsoft folyamatosan figyeli a különböző kiszolgálókat, hálózatokat és alkalmazásokat a fenyegetések és a biztonsági rések felderítése érdekében. Automatikus riasztások értesítik a rendszergazdákat a rendellenes viselkedésekről, ezáltal megtehetik a szükséges korrekciós intézkedéseket mind a külső, mind a belső fenyegetésekkel szemben.
* Külső biztonsági megoldások telepítheti az előfizetések, például a webalkalmazási tűzfalak belül [Barracuda](https://techlib.barracuda.com/ng54/deployonazure).
* A Microsoft megközelítése behatolást vagy a biztonság tesztelés is magában foglalja "[piros-összevonás](http://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)," magában foglalja a Microsoft Információbiztonsági szakemberei valós, elleni védelmet tesztelése az Azure-ban (nem vevő) éles rendszerek támadása speciális, állandó fenyegetések.
* Az integrált központi telepítési rendszerek kezelik a biztonsági javítások terjesztését és telepítését az Azure platformon keresztül.

## <a name="next-steps"></a>Következő lépések
[Azure biztonsági és adatkezelési központ](https://azure.microsoft.com/support/trust-center/)

[Az Azure Security csapat blogja](http://blogs.msdn.com/b/azuresecurity/)

[Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx)

[Active Directory blog](http://blogs.technet.com/b/ad/)
