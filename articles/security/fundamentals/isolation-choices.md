---
title: Elkülönítés az Azure nyilvános felhőben | Microsoft dokumentumok
description: Ismerje meg, hogy az Azure hogyan biztosítja az elkülönítést a rosszindulatú és nem rosszindulatú felhasználókkal szemben, és különböző elkülönítési lehetőségeket kínál az építészek számára.
services: security
documentationcenter: na
author: UnifyCloud
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: TomSh
ms.openlocfilehash: c6e74e7992326d2a4b8fe24510742422b005c2e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280312"
---
# <a name="isolation-in-the-azure-public-cloud"></a>Elkülönítés az Azure nyilvános felhőben
Az Azure lehetővé teszi, hogy alkalmazásokat és virtuális gépeket (VM-ek) futtasson a megosztott fizikai infrastruktúrán. Az alkalmazások felhőalapú környezetben való futtatásának egyik fő gazdasági motivációja a megosztott erőforrások költségének több ügyfél közötti elosztása. Ez a gyakorlat a több-bérlős javítja a hatékonyságot a multiplex források között különböző ügyfelek alacsony költségek mellett. Sajnos azt is bevezeti a fizikai kiszolgálók és más infrastruktúra-erőforrások megosztásának kockázatát a bizalmas alkalmazások és virtuális gépek futtatásához, amelyek egy tetszőleges és potenciálisan rosszindulatú felhasználóhoz tartozhatnak.

Ez a cikk bemutatja, hogy az Azure hogyan biztosítja a rosszindulatú és nem rosszindulatú felhasználók elkülönítését, és útmutatóként szolgál a felhőalapú megoldások megtervezéséhez azáltal, hogy különböző elkülönítési lehetőségeket kínál az építészeknek.

## <a name="tenant-level-isolation"></a>Bérlői szint elkülönítése
A felhőalapú számítástechnika egyik fő előnye a közös, közös infrastruktúra koncepciója számos ügyfél között egyszerre, ami méretgazdaságosságot okoz. Ezt a fogalmat több-bérlős. A Microsoft folyamatosan dolgozik annak biztosításán, hogy a Microsoft Cloud Azure több-bérlős architektúrája támogassa a biztonsági, titoktartási, adatvédelmi, integritási és rendelkezésre állási szabványokat.

Felhőalapú munkahelyek esetén a bérlő olyan ügyfelet vagy szervezetet jelent, amely a felhőszolgáltatás adott példányát birtokolja és kezeli. A Microsoft Azure által biztosított identitásplatform segítségével a bérlő egyszerűen az Azure Active Directory (Azure AD) egy dedikált példánya, amelyet a szervezet fogad és birtokol, amikor regisztrál egy Microsoft felhőszolgáltatásra.

Mindegyik Azure AD-címtár önálló, és el van választva a többi Azure AD-címtártól. Ahogy a vállalat irodaépülete is egy vállalatspecifikus biztonsági vagyontárgynak tekinthető, az Azure AD-címtár is egy, kizárólag az adott szervezet általi használatra kialakított biztonságos eszköz. Az Azure AD architektúrájával megakadályozható az ügyfél- és identitásadatok keveredése. Ez azt jelenti, hogy az adott Azure AD-címtár felhasználói és rendszergazdái véletlenül vagy kártételi szándékkal nem férhetnek hozzá más címtárak adataihoz.

### <a name="azure-tenancy"></a>Azure-bérleti szerződés
Az Azure-előfizetés (Azure-előfizetés) "ügyfél/számlázás" kapcsolatra és az [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)egyedi [bérlőjének](../../active-directory/develop/quickstart-create-new-tenant.md) utal. A Microsoft Azure-ban a bérlői szintű elkülönítés az Azure Active Directory és az általa kínált [szerepköralapú vezérlők](../../role-based-access-control/overview.md) használatával érhető el. Minden Azure-előfizetés egy Azure Active Directory (AD) könyvtárhoz van társítva.

Az adott címtárból származó felhasználók, csoportok és alkalmazások kezelhetik az Azure-előfizetés erőforrásait. Ezeket a hozzáférési jogokat az Azure Portalon, az Azure parancssori eszközeivel és az Azure Management API-kban rendelheti hozzá. Az Azure AD-bérlő logikailag elkülönített biztonsági határok használatával, így egyetlen ügyfél sem férhet hozzá, vagy veszélyeztetheti a társ-bérlők, akár rosszindulatúan, akár véletlenül. Az Azure AD egy elkülönített hálózati szegmensen elkülönített "csupasz fém" kiszolgálókon fut, ahol a gazdagépszintű csomagszűrés és a Windows tűzfal blokkolja a nem kívánt kapcsolatokat és forgalmat.

- Az Azure AD-ben az adatokhoz való hozzáférés hez felhasználói hitelesítés szükséges egy biztonsági jogkivonat-szolgáltatáson (STS) keresztül. A felhasználó létezésére, engedélyezett állapotára és szerepkörére vonatkozó információkat az engedélyezési rendszer annak meghatározására használja, hogy a kért hozzáférés a célbérlőhöz jogosult-e a felhasználó számára ebben a munkamenetben.

![Azure-bérleti szerződés](./media/isolation-choices/azure-isolation-fig1.png)


- A bérlők különálló tárolók, és ezek között nincs kapcsolat.

- Nincs hozzáférés a bérlők között, kivéve, ha a bérlői rendszergazda összevonási vagy más bérlőktől származó felhasználói fiókok kiépítésén keresztül adja meg.

- Az Azure AD-szolgáltatást alkotó kiszolgálókhoz való fizikai hozzáférés, valamint az Azure AD háttérrendszereihez való közvetlen hozzáférés korlátozott.

- Az Azure AD-felhasználók nem férnek hozzá a fizikai eszközökhöz vagy a helyekhez, ezért nem lehetséges, hogy megkerüljék a logikai RBAC-szabályzat i.

A diagnosztikai és karbantartási igények, egy operatív modell, amely egy just-in-time jogosultság jogosultság emelési rendszer szükséges és használható. Az Azure AD emelt szintű identitáskezelés (PIM) bevezeti a jogosult rendszergazda fogalmát. [A jogosult rendszergazdáknak](../../active-directory/privileged-identity-management/pim-configure.md) olyan felhasználóknak kell lenniük, akiknek mindig jogosultsággal rendelkező hozzáférésre van szükségük, de nem minden nap. A szerepkör inaktív, amíg a felhasználónak nincs szüksége a hozzáférésre, majd szükség esetén a felhasználó egy aktiválási folyamat teljesítésével válhat aktív rendszergazdává egy előre meghatározott időtartamra.

![Azure AD Privileged Identity Management](./media/isolation-choices/azure-isolation-fig2.png)

Az Azure Active Directory minden bérlőt saját védett tárolójában üzemeltet, szabályzatokkal és engedélyekkel a tárolóhoz, és a tárolón belül kizárólag a bérlő tulajdonában és felügyelten.

A bérlői tárolók fogalma mélyen beleivódott a címtárszolgáltatásminden rétegben, a portálok egészen az állandó tárolás.

Még akkor is, ha több Azure Active Directory-bérlők metaadatait tárolja ugyanazon a fizikai lemezen, nincs kapcsolat a tárolók között más, mint amit a címtárszolgáltatás, ami viszont a bérlői rendszergazda által diktált.

### <a name="azure-role-based-access-control-rbac"></a>Azure szerepköralapú hozzáférés-vezérlés (RBAC)
[Az Azure szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/overview.md) segítségével megoszthatja az Azure-előfizetésben elérhető különböző összetevőket azáltal, hogy részletes hozzáférés-kezelést biztosít az Azure-hoz. Az Azure RBAC lehetővé teszi, hogy elkülönítse a feladatokat a szervezeten belül, és hozzáférést biztosít a felhasználók feladataik elvégzéséhez. Ahelyett, hogy mindenkinek korlátlan engedélyeket adna az Azure-előfizetésben vagy -erőforrásokban, csak bizonyos műveleteket engedélyezhet.

Az Azure RBAC három alapvető szerepkörrel rendelkezik, amelyek minden erőforrástípusra vonatkoznak:

- **A tulajdonos** teljes hozzáféréssel rendelkezik az összes erőforráshoz, beleértve a másoknak való hozzáférés delegálásának jogát is.

- **Közreműködő** hozhat létre és kezelhet minden típusú Azure-erőforrások, de nem adhat hozzáférést másoknak.

- **A Reader** megtekintheti a meglévő Azure-erőforrásokat.

![Azure szerepköralapú hozzáférés-vezérlés](./media/isolation-choices/azure-isolation-fig3.png)

Az Azure-ban az RBAC-szerepkörök többi része lehetővé teszi az adott Azure-erőforrások kezelését. Például a Virtuális gépek közreműködője szerepkör virtuális gépek létrehozását és kezelését teszi lehetővé. Nem ad nekik hozzáférést az Azure virtuális hálózathoz vagy az alhálózathoz, amelyhez a virtuális gép csatlakozik.

[Az RBAC beépített szerepkörei az Azure-ban](../../role-based-access-control/built-in-roles.md) elérhető szerepköröket sorolják fel. Meghatározza az egyes beépített szerepkörök által a felhasználóknak nyújtott műveleteket és hatókört. Ha saját szerepköröket szeretne definiálni még több vezérléshez, olvassa el, hogyan hozhat létre [egyéni szerepköröket az Azure RBAC-ban.](../../role-based-access-control/custom-roles.md)

Az Azure Active Directory további lehetőségei a következők:
- Az Azure AD lehetővé teszi az SSO bejelentkezést a SaaS-alkalmazások számára a futtatási helyüktől függetlenül. Egyes alkalmazások az Azure AD-vel összevontan működnek, mások jelszavas egyszeri bejelentkezést használnak. Az összevont alkalmazások is támogatják a felhasználók kiépítését és [a jelszótárolókat.](https://www.techopedia.com/definition/31415/password-vault)

- Az [Azure Storage](https://azure.microsoft.com/services/storage/)-ban tárolt adatokhoz való hozzáférést hitelesítés védi. Minden tárfiók rendelkezik egy elsődleges kulcs[(tárfiók kulcs](../../storage/common/storage-create-storage-account.md), vagy SAK) és egy másodlagos titkos kulcs (a közös hozzáférésű aláírás vagy SAS).

- Az Azure AD az Active Directory [összevonási szolgáltatások,](../../active-directory/hybrid/how-to-connect-fed-azure-adfs.md)a szinkronizálás és a helyszíni könyvtárakkal való replikáció használatával biztosítja az identitás szolgáltatásként történő összevonáson keresztül.

- [Az Azure többtényezős hitelesítés](../../active-directory/authentication/multi-factor-authentication.md) a többtényezős hitelesítési szolgáltatás, amely megköveteli a felhasználóktól, hogy ellenőrizze a bejelentkezések segítségével egy mobilalkalmazás, telefonhívás vagy szöveges üzenet. Az Azure AD-vel az Azure többtényezős hitelesítési kiszolgálóval, valamint az SDK-t használó egyéni alkalmazásokkal és könyvtárakkal biztosíthatja a helyszíni erőforrások biztonságossá tétele.

- [Az Azure AD tartományi szolgáltatások](https://azure.microsoft.com/services/active-directory-ds/) lehetővé teszi, hogy az Azure virtuális gépeket tartományvezérlők telepítése nélkül csatlakozzon egy Active Directory-tartományhoz. Ezekbe a virtuális gépekre a vállalati Active Directory hitelesítő adatokkal jelentkezhet be, és a csoportházirend használatával felügyelheti a tartományhoz kapcsolódó virtuális gépeket az összes Azure-beli virtuális gépen a biztonsági alapkonfigurációk kényszerítéséhez.

- [Az Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) magas rendelkezésre állású globális identitáskezelési szolgáltatást biztosít a több százmillió identitásra skálázható, fogyasztóbarát alkalmazások számára. Mobil- és webes platformokba is integrálható. A fogyasztók a meglévő közösségi fiókjaik használatával vagy hitelesítő adatok létrehozásával bejelentkezhetnek az összes alkalmazásba testre szabható élmények segítségével.

### <a name="isolation-from-microsoft-administrators--data-deletion"></a>Elkülönítés a Microsoft rendszergazdáktól & adattörléstől
A Microsoft határozott intézkedéseket tesz annak érdekében, hogy megvédje adatait a jogosulatlan hozzáféréstől vagy illetéktelen személyek általi felhasználástól. Ezeket az operatív folyamatokat és ellenőrzéseket az [Online Szolgáltatási Feltételek](https://aka.ms/Online-Services-Terms)támasztják alá, amelyek az adatokhoz való hozzáférést szabályozó szerződéses kötelezettségvállalásokat kínálnak.

-   A Microsoft mérnökei nem rendelkeznek alapértelmezett hozzáféréssel az adatokhoz a felhőben. Ehelyett csak szükség esetén kapnak hozzáférést vezetői felügyelet mellett. Ezt a hozzáférést gondosan ellenőrzik és naplózzák, és visszavonják, ha már nincs rá szükség.

-   A Microsoft más vállalatokat is felbérelhet, hogy a nevében korlátozott szolgáltatásokat nyújtsanak. Az alvállalkozók csak azoknak a szolgáltatásoknak a nyújtása érdekében férhetnek hozzá az ügyféladatokhoz, amelyek nyújtására mi béreltük fel őket, és tilos azokat bármilyen más célra felhasználni. Továbbá, szerződésben kötelesek megőrizni ügyfeleink adatainak bizalmas jellegét.

Az Auditált tanúsítványokkal rendelkező üzleti szolgáltatásokat, mint például az ISO/IEC 27001-et a Microsoft és az akkreditált könyvvizsgáló cégek rendszeresen ellenőrzik, amelyek mintaauditokat végeznek, hogy tanúsítsák ezt a hozzáférést, csak törvényes üzleti célokból. Bármikor és bármilyen okból bármikor hozzáférhet saját ügyféladataihoz.

Ha töröl bármilyen adatot, a Microsoft Azure törli az adatokat, beleértve a gyorsítótárazott vagy biztonsági másolatokat is. A hatókörön belüli szolgáltatások esetében ez a törlés a megőrzési időszak végét követő 90 napon belül történik. (A hatókörön átnyúló szolgáltatásokat az Online Szolgáltatási [feltételek](https://aka.ms/Online-Services-Terms)Adatfeldolgozási feltételek szakasza határozza meg.)

Ha a tároláshoz használt lemezmeghajtó hardverhibát szenved, a rendszer biztonságosan [törli vagy megsemmisíti](https://microsoft.com/trustcenter/privacy/you-own-your-data) azt, mielőtt a Microsoft visszaküldi azt a gyártónak csere ként vagy javításra. A meghajtón lévő adatok felülíródnak, hogy az adatok at semmilyen módon ne lehessen visszabiztosítani.

## <a name="compute-isolation"></a>Elkülönítés számítása
A Microsoft Azure különböző felhőalapú számítástechnikai szolgáltatásokat kínál, amelyek számítási példányok & szolgáltatások széles választékát tartalmazzák, amelyek automatikusan skálázhatók az alkalmazás vagy a nagyvállalat igényeinek megfelelően. Ezek a számítási példányok és szolgáltatások több szinten kínálnak elkülönítést az adatok biztonságossá tétele érdekében anélkül, hogy feláldoznák az ügyfelek által igényelt konfigurációs rugalmasságot.

### <a name="isolated-virtual-machine-sizes"></a>Elkülönített virtuális gépméretek

[!INCLUDE [virtual-machines-common-isolation](../../../includes/virtual-machines-common-isolation.md)]

### <a name="dedicated-hosts"></a>Dedikált gazdagépek
Az előző szakaszban ismertetett elkülönített gazdagépekmellett az Azure dedikált gazdagépeket is kínál. Az Azure dedikált állomásai olyan szolgáltatás, amely fizikai kiszolgálókat biztosít, amelyek egy vagy több virtuális gépet üzemeltetnek, és amelyek egyetlen Azure-előfizetéshez vannak szentelve. A dedikált állomások a hardveres elkülönítést biztosítják a fizikai kiszolgáló szintjén. Más virtuális gépek nem kerülnek a gazdagépekre. Dedikált gazdagépek vannak telepítve az azonos adatközpontokban, és ugyanazt a hálózatot és az alapul szolgáló tárolási infrastruktúra, mint más, nem elkülönített gazdagépek. További információt az [Azure dedikált gazdagépeinek](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts)részletes áttekintésében talál.

### <a name="hyper-v--root-os-isolation-between-root-vm--guest-vms"></a>A Hyper-V & gyökéroperációsrendszer elkülönítése a legfelső szintű virtuális gépek & a vendégvirtuális gép e között
Az Azure számítási platformja a gépi virtualizáción alapul, ami azt jelenti, hogy az összes ügyfélkód egy Hyper-V virtuális gépen hajtható végre. Minden Egyes Azure-csomóponton (vagy hálózati végponton) van egy hipervizor, amely közvetlenül fut a hardveren, és egy csomópontot változó számú vendég virtuális gépre (VM) oszt fel.


![A Hyper-V & gyökéroperációsrendszer elkülönítése a legfelső szintű virtuális gépek & a vendégvirtuális gép e között](./media/isolation-choices/azure-isolation-fig4.jpg)


Minden csomópont is rendelkezik egy speciális gyökér virtuális gép, amely a gazdaoperációs rendszer fut. A kritikus határ a gyökér virtuális gép elkülönítése a vendég virtuális gépek és a vendég virtuális gépek egymástól, a hipervizor és a gyökér operációs rendszer által kezelt. A hypervisor/root OS párosítás a Microsoft több évtizedes operációsrendszer-biztonsági élményét és a Microsoft Hyper-V-jének újabb tanulását használja a vendég virtuális gépek erős elkülönítése érdekében.

Az Azure platform virtualizált környezetet használ. A felhasználói példányok önálló virtuális gépként működnek, amelyek nem férnek hozzá a fizikai gazdakiszolgálóhoz.

Az Azure hipervizor mikrokernelként működik, és a vendég virtuális gépektől a gazdagéptől a virtuális gépről a virtuális gépnevű megosztott memória felülethasználatával továbbítja az összes hardveres hozzáférési kérelmet. Ez megakadályozza, hogy a felhasználók nyers olvasási, írási és végrehajtási hozzáférést szerezzenek a rendszerhez, és csökkenti a rendszererőforrások megosztásának kockázatát.

### <a name="advanced-vm-placement-algorithm--protection-from-side-channel-attacks"></a>A speciális virtuálisgép-elhelyezési algoritmus & az oldalcsatorna-támadásokkal szembeni védelemre
Minden virtuális gép közötti támadás két lépésből áll: egy ellenség által ellenőrzött virtuális gép elhelyezése ugyanazon a gazdagépen, mint az áldozat virtuális gépek, majd az elkülönítési határ megsértése, hogy vagy ellopják a bizalmas áldozat adatait, vagy befolyásolja a teljesítményt a kapzsiság vagy vandalizmus. A Microsoft Azure mindkét lépésnél védelmet nyújt egy speciális virtuálisgép-elhelyezési algoritmus használatával, és védelmet nyújt az összes ismert oldalcsatorna-támadással szemben, beleértve a zajos szomszéd virtuális gépeket is.

### <a name="the-azure-fabric-controller"></a>Az Azure Fabric vezérlő
Az Azure Fabric Controller felelős az infrastruktúra-erőforrások felosztása a bérlői számítási feladatok, és kezeli az egyirányú kommunikáció a gazdagép virtuális gépek. Az Azure fabric vezérlő virtuális gép elhelyezési algoritmusa rendkívül kifinomult, és szinte lehetetlen megjósolni, mint a fizikai gazdagép szintjén.

![Az Azure Fabric vezérlő](./media/isolation-choices/azure-isolation-fig5.png)

Az Azure hipervizor kikényszeríti a memória és a folyamat elkülönítése a virtuális gépek között, és biztonságosan irányítja a hálózati forgalmat a vendég operációs rendszer bérlői. Ez kiküszöböli a lehetőségét, és oldalsó csatorna támadás vm szinten.

Az Azure-ban a gyökér virtuális gép különleges: fut egy megerősített operációs rendszer, az úgynevezett gyökér operációs rendszer, amely egy hálóügynök (FA) üzemelteti. A felhasználói gyártók viszont vendégügynökök (GA) kezelésére szolgálnak az ügyfél virtuális gépekvendég operációs in-műveleteken belül. A rendszeraz okat is kezeli a tárolócsomópontokat.

Az Azure hypervisor, a root OS/FA és az ügyfél virtuális gépek/ga-k gyűjteménye egy számítási csomópontot tartalmaz. Az egyébeseket egy olyan hálóvezérlő (FC) kezeli, amely a számítási és tárolócsomópontokon kívül létezik (a számítási és tárolási fürtöket külön FC-k kezelik). Ha egy ügyfél futás közben frissíti az alkalmazás konfigurációs fájlját, az FC kommunikál a BE-vel, amely ezután kapcsolatba lép a ga-kkal, amelyek értesítik az alkalmazást a konfigurációváltozásról. Hardverhiba esetén az FC automatikusan megtalálja a rendelkezésre álló hardvert, és ott újraindítja a virtuális gép.

![Azure Fabric-vezérlő](./media/isolation-choices/azure-isolation-fig6.jpg)

A hálóvezérlő és az ügynök közötti kommunikáció egyirányú. Az ügynök egy SSL-védelemmel ellátott szolgáltatást valósít meg, amely csak a vezérlő től érkező kérésekre válaszol. Nem kezdeményezhet kapcsolatokat a vezérlővel vagy más kiemelt belső csomópontokkal. Az FC minden választ úgy kezel, mintha nem lennének megbízhatóak.


![Szövet vezérlő](./media/isolation-choices/azure-isolation-fig7.png)

Az elkülönítés a legfelső virtuális géptől a vendég virtuális gépektől és a vendég virtuális gépekegymástól való kiterjesztésére terjed ki. A számítási csomópontok is el vannak különítve a tárolócsomópontokból a nagyobb védelem érdekében.


A hipervizor és a gazdaoperációs rendszer hálózati csomagot biztosít – szűrők et biztosít, amelyek biztosítják, hogy a nem megbízható virtuális gépek nem tudnak hamisított forgalmat generálni, vagy nem fogadhatnak olyan forgalmat, amely nem címezi őket, nem irányíthatja a forgalmat a védett infrastruktúra végpontjaira, illetve nem megfelelő szórásos forgalmat küldhet/fogadhat.


### <a name="additional-rules-configured-by-fabric-controller-agent-to-isolate-vm"></a>A virtuális gép elkülönítésére a hálóvezérlő ügynök által konfigurált további szabályok
Alapértelmezés szerint a rendszer minden forgalmat blokkol, amikor egy virtuális gép jön létre, majd a hálóvezérlő ügynök konfigurálja a csomagszűrőt, hogy szabályokat és kivételeket adjon hozzá az engedélyezett forgalom engedélyezéséhez.

A szabályok két kategóriába sorolhatók:

-   **Gépkonfigurációs vagy infrastruktúra-szabályok:** Alapértelmezés szerint minden kommunikáció le van tiltva. Vannak kivételek, amelyek lehetővé teszik a virtuális gépek számára a DHCP- és DNS-forgalom küldését és fogadását. A virtuális gépek is küldhetnek forgalmat a "nyilvános" internetre, és forgalmat küldhetnek más virtuális gépekre ugyanazon az Azure virtuális hálózaton és az operációs rendszer aktiválási kiszolgálón belül. A virtuális gépek engedélyezett kimenő célhelyek listája nem tartalmazza az Azure útválasztó alhálózatait, az Azure-kezelést és más Microsoft-tulajdonságokat.

-   **Szerepkör konfigurációs fájlja:** Ez határozza meg a bejövő hozzáférés-vezérlési listák (ACLs) alapján a bérlő szolgáltatási modell.

### <a name="vlan-isolation"></a>VLAN szigetelés
Minden fürtben három VLAN található:

![VLAN szigetelés](./media/isolation-choices/azure-isolation-fig8.jpg)


-   A fő VLAN – összeköti a nem megbízható ügyfélcsomópontokat

-   Az FC VLAN – megbízható FC-ket és támogató rendszereket tartalmaz

-   Az eszköz VLAN - megbízható hálózati és egyéb infrastrukturális eszközöket tartalmaz

A kommunikáció megengedett az FC VLAN-tól a fő VLAN-ig, de nem kezdeményezhető a fő VLAN-tól az FC VLAN-ig. Kommunikáció is blokkolva van a fő VLAN a készülék VLAN. Ez biztosítja, hogy még akkor is, ha egy ügyfélkódot futtató csomópont biztonsága sérül, nem támadhatja meg a csomópontokat sem az FC, sem az eszköz VLAN-ok on.

## <a name="storage-isolation"></a>Tároló elkülönítése
### <a name="logical-isolation-between-compute-and-storage"></a>Logikai elkülönítés a számítás és a tárolás között
Alapvető kialakításának részeként a Microsoft Azure elválasztja a virtuális gép alapú számítást a tárolótól. Ez a szétválasztás lehetővé teszi a számítást és a tárolást egymástól függetlenül történő méretezésre, megkönnyítve a több-bérlős és elkülönítési környezetezést.

Ezért az Azure Storage külön hardveren fut, és nincs hálózati kapcsolat az Azure Compute-hoz, kivéve logikusan. Ez azt jelenti, hogy a virtuális lemez létrehozásakor a rendszer nem foglal le lemezterületet a teljes kapacitásához. Ehelyett létrejön egy tábla, amely a virtuális lemezen lévő címeket a fizikai lemez területeihez rendeli hozzá, és a tábla kezdetben üres. **Amikor az ügyfél először ír adatokat a virtuális lemezre, a rendszer helyet foglal a fizikai lemezen, és a mutatót a táblába helyezi.**
### <a name="isolation-using-storage-access-control"></a>Elkülönítés a tárolási hozzáférés-vezérlés használatával
**Az Azure Storage hozzáférés-vezérlése** egyszerű hozzáférés-vezérlési modellel rendelkezik. Minden Azure-előfizetés létrehozhat egy vagy több tárfiókot. Minden tárfiók rendelkezik egy titkos kulcs, amely az adott tárfiók összes adathoz való hozzáférés szabályozására szolgál.

![Elkülönítés a tárolási hozzáférés-vezérlés használatával](./media/isolation-choices/azure-isolation-fig9.png)

**Az Azure Storage-adatokhoz való hozzáférés (beleértve a táblákat is)** egy [SAS (megosztott hozzáférésű aláírás)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) jogkivonaton keresztül szabályozható, amely hatókörrel rendelkező hozzáférést biztosít. A SAS egy lekérdezési sablon (URL) segítségével jön létre, amely a [SAK (Tárfiók kulcs)](https://msdn.microsoft.com/library/azure/ee460785.aspx)aláírásával történik. Az [aláírt URL-cím](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) megadható egy másik folyamatnak (azaz delegáltnak), amely ezután kitöltheti a lekérdezés részleteit, és kérheti a tárolási szolgáltatás kérését. A SAS lehetővé teszi, hogy időalapú hozzáférést biztosítson az ügyfeleknek a tárfiók titkos kulcsának felfedése nélkül.

A SAS azt jelenti, hogy korlátozott engedélyeket adhatunk az ügyfélnek a tárfiókunkban lévő objektumokhoz egy meghatározott ideig és egy meghatározott engedélykészlettel. Ezeket a korlátozott engedélyeket anélkül is megadhatjuk, hogy meg kellene osztanunk a fiók hozzáférési kulcsait.

### <a name="ip-level-storage-isolation"></a>IP-szintű tárolóelkülönítés
Tűzfalakat hozhat létre, és ip-címtartományt határozhat meg a megbízható ügyfelek számára. IP-címtartomány esetén csak azok az ügyfelek csatlakozhatnak az [Azure Storage-hoz,](../../storage/blobs/security-recommendations.md)amelyek a megadott tartományon belül IP-címmel rendelkeznek.

Az IP-tárolási adatok védhetők a jogosulatlan felhasználóktól egy olyan hálózati mechanizmuson keresztül, amely egy dedikált vagy dedikált forgalmi alagút IP-tárolóhoz való hozzárendelésére szolgál.

### <a name="encryption"></a>Titkosítás
Az Azure a következő típusú titkosítást kínálja az adatok védelme érdekében:
-   Titkosítás az átvitel során

-   Titkosítás inaktív állapotban

#### <a name="encryption-in-transit"></a>Titkosítás az átvitel során
Az átvitel közbeni titkosítás az adatok védelmének mechanizmusa, ha azokat hálózatokon keresztül továbbítják. Az Azure Storage segítségével a következő kkel biztosíthatja az adatokat:

-   [Átviteli szintű titkosítás](../../storage/blobs/security-recommendations.md), például HTTPS, amikor adatokat továbbít az Azure Storage-ba vagy az Azure Storage-ból.

-   [Vezetékes titkosítás](../../storage/blobs/security-recommendations.md), például Az SMB 3.0 titkosítás az Azure File shares.

-   [Ügyféloldali titkosítás](../../storage/blobs/security-recommendations.md), az adatok titkosításához, mielőtt azok a tárolóba kerülnek, és az adatok visszafejtéséhez, miután az átvitel a tárolóból történt.

#### <a name="encryption-at-rest"></a>Titkosítás nyugalmi nyugalomban
Sok szervezet számára [az inaktív adattitkosítás](isolation-choices.md) kötelező lépés az adatvédelem, a megfelelőség és az adatok szuverenitása felé. Három Azure-szolgáltatás létezik, amelyek az "inaktív" adatok titkosítását biztosítják:

-   [A storage service encryption](../../storage/blobs/security-recommendations.md) lehetővé teszi, hogy kérje, hogy a tárolási szolgáltatás automatikusan titkosítja az adatokat, amikor az Azure Storage-ba írásakor.

-   [Az ügyféloldali titkosítás](../../storage/blobs/security-recommendations.md) az inaktív titkosítás szolgáltatását is biztosítja.

-   [Az Azure Disk Encryption](../azure-security-disk-encryption-overview.md) lehetővé teszi az IaaS virtuális gép által használt operációsrendszer-lemezek és adatlemezek titkosítását.

#### <a name="azure-disk-encryption"></a>Azure Disk Encryption
[Az Azure Disk Encryption](../azure-security-disk-encryption-overview.md) virtuális gépekhez (VM-ek) segít a szervezeti biztonsági és megfelelőségi követelmények teljesítésében azáltal, hogy titkosítja a virtuálisgép-lemezeket (beleértve a rendszerindító és adatlemezeket) az [Azure Key Vaultban](https://azure.microsoft.com/services/key-vault/)felügyelt kulcsokkal és szabályzatokkal.

A Windows lemeztitkosítási megoldása a [Microsoft BitLocker meghajtótitkosításon](https://technet.microsoft.com/library/cc732774.aspx)alapul, a Linux megoldás pedig a [dm-crypt-en.](https://en.wikipedia.org/wiki/Dm-crypt)

A megoldás a következő forgatókönyveket támogatja az IaaS virtuális gépekhez, ha engedélyezve vannak a Microsoft Azure-ban:
-   Integráció az Azure Key Vaultszolgáltatással

-   Standard szintű virtuális gépek: A, D, DS, G, GS és így tovább, sorozati IaaS virtuális gépek

-   Titkosítás engedélyezése Windows és Linux iaaS virtuális gépeken

-   Titkosítás letiltása windowsi iaas virtuális gépek operációs rendszerén és adatmeghajtóin

-   Titkosítás letiltása a Linux IaaS virtuális gépek adatmeghajtóin

-   Titkosítás engedélyezése a Windows ügyféloperációs rendszert futtató IaaS virtuális gépeken

-   Titkosítás engedélyezése csatlakoztatási útvonalakkal rendelkező köteteken

-   Titkosítás engedélyezése a lemezcsíkozással (RAID) konfigurált Linux-virtuális gépeken [az mdadm](https://en.wikipedia.org/wiki/Mdadm) használatával

-   Titkosítás engedélyezése Linuxos virtuális gépeken [LVM(Logikai kötetkezelő)](https://msdn.microsoft.com/library/windows/desktop/bb540532) használatával adatlemezekhez

-   Titkosítás engedélyezése tárolóhelyekkel konfigurált Windows virtuális gépeken

-   Minden Nyilvános Azure-régió támogatott

A megoldás nem támogatja a következő forgatókönyveket, funkciókat és technológiákat a kiadásban:

-   Alapvető szintű IaaS virtuális gépek

-   Titkosítás letiltása Linuxi IaaS virtuális gépekhez való operációsrendszer-meghajtón

-   A klasszikus virtuálisgép-létrehozási módszerrel létrehozott IaaS virtuális gépek

-   Integráció a helyszíni kulcskezelő szolgáltatással

-   Azure Files (megosztott fájlrendszer), hálózati fájlrendszer (NFS), dinamikus kötetek és szoftveralapú RAID-rendszerekkel konfigurált Windows virtuális gépek

## <a name="sql-azure-database-isolation"></a>SQL Azure-adatbázis elkülönítése
Az SQL Database a Microsoft Cloud egy, a piacvezető Microsoft SQL Server motoron alapuló relációs adatbázis-szolgáltatása, amely képes a kritikus fontosságú számítási feladatok kezelésére. Az SQL Database kiszámítható adatelkülönítést kínál a fiók szintjén, földrajzi lag /régió alapú és hálózatkezelésen alapul – mindezt közel nulla felügyelettel.

### <a name="sql-azure-application-model"></a>SQL Azure alkalmazásmodell

[Microsoft SQL Azure](../../sql-database/sql-database-single-database-get-started.md) Az adatbázis egy SQL Server technológiákra épülő, felhőalapú relációs adatbázis-szolgáltatás. Magas rendelkezésre állású, méretezhető, több-bérlős adatbázis-szolgáltatást biztosít a Microsoft felhőben.

Alkalmazásperspektívából az SQL Azure a következő hierarchiát biztosítja: Minden szint egy-a-többhöz elszigeteléssel rendelkezik az alábbi szintekhez.

![SQL Azure alkalmazásmodell](./media/isolation-choices/azure-isolation-fig10.png)

A fiók és az előfizetés a Microsoft Azure platformfogalmai a számlázás és a kezelés társítására.

A logikai kiszolgálók és adatbázisok SQL Azure-specifikus fogalmak, amelyeket az SQL Azure, az OData és a TSQL-felületek vagy az Azure Portalba integrált SQL Azure-portál használatával kezelnek.

Az SQL Azure-kiszolgálók nem fizikai vagy virtuálisgép-példányok, hanem adatbázisok, megosztáskezelési és biztonsági házirendek gyűjteményei, amelyek et úgynevezett "logikai főkiszolgáló" adatbázisban tárolnak.

![SQL Azure](./media/isolation-choices/azure-isolation-fig11.png)

A logikai főadatbázisok a következők:

-   A kiszolgálóhoz való csatlakozáshoz használt SQL-bejelentkezések

-   Tűzfalszabályok

Az azonos logikai kiszolgálóról származó SQL Azure-adatbázisok számlázásával és használattal kapcsolatos információi nem garantáltak, hogy ugyanazon a fizikai példányon vannak az SQL Azure-fürtben, hanem az alkalmazásoknak meg kell adniuk a céladatbázis nevét a csatlakozáskor.

Az ügyfél szempontjából logikai kiszolgáló jön létre egy geografikus régióban, míg a kiszolgáló tényleges létrehozása a régió egyik fürtjében történik.

### <a name="isolation-through-network-topology"></a>Elkülönítés a hálózati topológián keresztül

Logikai kiszolgáló létrehozásakor és DNS-neve regisztrálásakor a DNS-név az úgynevezett "Gateway VIP" címre mutat abban az adatközpontban, ahol a kiszolgálót helyezték.

A VIP (virtuális IP-cím) mögött állapotmentes átjárószolgáltatások gyűjteménye áll. Általánosságban elmondható, hogy az átjárók akkor vesznek részt, ha több adatforrás (főadatbázis, felhasználói adatbázis stb.) között koordinációra van szükség. Az átjárószolgáltatások a következőket valósítják meg:
-   **TDS-kapcsolat proxyja.** Ez magában foglalja a felhasználói adatbázis megkeresését a háttérfürtben, a bejelentkezési sorrend megvalósítását, majd a TDS-csomagok továbbtovábbítását a háttérrendszerbe és vissza.

-   **Adatbázis-kezelés.** Ez magában foglalja a CREATE/ALTER/DROP adatbázis-műveletek hez végzett munkafolyamatok gyűjteményének megvalósítását. Az adatbázis-műveletek meghívhatók a TDS-csomagok szippantásával vagy explicit OData API-kkal.

-   CREATE/ALTER/DROP bejelentkezési/felhasználói műveletek

-   Logikai kiszolgálókezelési műveletek az OData API-n keresztül

![Elkülönítés a hálózati topológián keresztül](./media/isolation-choices/azure-isolation-fig12.png)

Az átjárók mögötti réteget "háttérrendszernek" nevezzük. Ez az a hely, ahol az összes adatot magas rendelkezésre állású módon tárolják. Minden egyes adat azt mondta, hogy tartozik egy "partíció" vagy "feladatátvételi egység", mindegyik rendelkezik legalább három replikák. A replikákat az SQL Server motorja tárolja és replikálja, és a feladatátvételi rendszer kezeli, amelyet gyakran "hálónak" neveznek.

A háttérrendszer általában biztonsági óvintézkedésként nem kommunikál más rendszerekkel. Ez az előtér-(átjáró) rétegben lévő rendszerek számára van fenntartva. Az átjárószintű gépek korlátozott jogosultságokkal rendelkeznek a háttérgépeken, hogy minimálisra csökkentsék a támadási felületet, mint mélyreható védelmi mechanizmust.

### <a name="isolation-by-machine-function-and-access"></a>Elkülönítés gépfüggvény és hozzáférés szerint
SQL Azure (különböző gépi függvényeken futó szolgáltatásokból áll. AZ SQL Azure "háttérrendszer" felhőalapú adatbázisra és "előtér- és "átjáró/kezelés) környezetre van osztva, és a forgalom általános elve csak háttérrendszerbe kerül, nem pedig ki. Az előtér-környezet képes kommunikálni a külvilág más szolgáltatások és általában csak korlátozott engedélyekkel rendelkezik a háttér-rendszer (elég ahhoz, hogy hívja meg a belépési pontokat kell hívni).

## <a name="networking-isolation"></a>Hálózati elkülönítés
Az Azure-telepítés több rétegnyi hálózati elkülönítést tartalmaz. Az alábbi ábrán az Azure által az ügyfeleknek biztosított hálózati elkülönítés különböző rétegei láthatók. Ezek a rétegek natív az Azure platformon is, és az ügyfél által meghatározott funkciók. Az internetről érkező Azure DDoS elkülönítést biztosít az Azure elleni nagyszabású támadások ellen. Az elkülönítés következő rétege az ügyfél által definiált nyilvános IP-címek (végpontok), amelyek annak meghatározására szolgálnak, hogy mely forgalom haladhat át a felhőszolgáltatáson a virtuális hálózatra. Natív Azure virtuális hálózat elkülönítése biztosítja a teljes elkülönítést az összes többi hálózattól, és hogy a forgalom csak a felhasználó által konfigurált elérési utak és módszerek keresztül áramlik. Ezek az elérési utak és módszerek a következő réteg, ahol nsg-k, UDR és hálózati virtuális készülékek segítségével elkülönítési határokat hozhat létre az alkalmazások központi telepítésének védelme érdekében a védett hálózatban.

![Hálózati elkülönítés](./media/isolation-choices/azure-isolation-fig13.png)

**Forgalom elkülönítése:** A [virtuális hálózat](../../virtual-network/virtual-networks-overview.md) az Azure platform forgalomelkülönítési határa. Az egy virtuális hálózatban lévő virtuális gépek nem kommunikálhatnak közvetlenül egy másik virtuális hálózat virtuális gépeivel, még akkor sem, ha mindkét virtuális hálózatot ugyanaz az ügyfél hozta létre. Az elkülönítés egy kritikus tulajdonság, amely biztosítja, hogy az ügyfél virtuális gépek és a kommunikáció továbbra is magán jellegű a virtuális hálózaton belül.

[Az alhálózat](../../virtual-network/virtual-networks-overview.md) egy további elkülönítési réteget kínál a virtuális hálózatban az IP-tartomány alapján. IP-címeket a virtuális hálózatban, a virtuális hálózatot több alhálózatra oszthatja a szervezet és a biztonság érdekében. Egy VNeten belül az alhálózatokra üzembe helyezett virtuális gépek és a PaaS szerepkörpéldányok (ugyanaz vagy különböző) további konfigurálás nélkül is tudnak egymással kommunikálni. A hálózati [biztonsági csoportot (NSG-k) is beállíthatja úgy,](../../virtual-network/virtual-networks-overview.md) hogy az NSG hozzáférés-vezérlési listájában (ACL) konfigurált szabályok alapján engedélyezze vagy megtagadja a virtuálisgép-példány hálózati forgalmát. Az NSG-ket alhálózatokhoz vagy az alhálózaton belüli virtuálisgép-példányokhoz lehet hozzárendelni. Ha az NSG-t hozzárendelik egy alhálózathoz, az ACL-szabályok érvényesek lesznek az alhálózatban lévő összes virtuálisgép-példányra.

## <a name="next-steps"></a>Következő lépések

- Tudnivalók a [Windows Azure virtuális hálózatok gépeihálózati elkülönítési lehetőségeiről.](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/) Ez magában foglalja a klasszikus előtér-és háttér-forgatókönyv, ahol a gépek egy adott háttérhálózat vagy alhálózat csak lehetővé teszi bizonyos ügyfelek vagy más számítógépek csatlakozni egy adott végpont ip-címek engedélyezési listája alapján.

- Ismerje meg [a virtuális gépek elkülönítését az Azure-ban.](../../virtual-machines/windows/isolation.md) Az Azure Compute olyan virtuálisgép-méreteket kínál, amelyek egy adott hardvertípusra vannak elkülönítve, és egyetlen ügyfélnek vannak elkülönítve.
