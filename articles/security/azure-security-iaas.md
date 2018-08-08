---
title: Ajánlott biztonsági eljárások IaaS számítási feladatokat az Azure-ban |} A Microsoft Docs
description: " Az áttelepítés az Azure IaaS munkaterhelések számos lehetőséget kínál a tervek reményeket lehetőségeket "
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/14/2018
ms.author: barclayn
ms.openlocfilehash: 7c28459aa04c67db8abda54d9f14eb417bd8ed60
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39618597"
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Ajánlott biztonsági eljárások IaaS számítási feladatokhoz az Azure-ban

A legtöbb infrastruktúra-szolgáltatás (IaaS) esetben [Azure-beli virtuális gépek (VM)](https://docs.microsoft.com/azure/virtual-machines/) a fő munkaterhelés olyan szervezeteknek, amelyek használják a felhő számítási rendszer. A tény, különösen egyértelmű az [hibrid forgatókönyvek](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) szervezetek számára, ahová a lassú számítási feladatok migrálása a felhőbe. Ilyen esetben hajtsa végre a [IaaS általános biztonsági szempontjai](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx), és a alkalmazni ajánlott biztonsági eljárások a virtuális gépek.

A biztonsági felelősséget a felhőszolgáltatás típusát alapul. Az alábbi táblázat foglalja össze a Microsoft és az Ön felelőssége egyenleg:

![A felelősségi területeket](./media/azure-security-iaas/sec-cloudstack-new.png)

Biztonsági követelmények számos tényező befolyásolja, többek között a különböző típusú számítási változhat. Ajánlott eljárások valamelyikét biztonságossá teheti a saját maga rendszereit. Mint minden más a biztonság hogy válassza ki a megfelelő beállításokat, és hogyan a megoldások is kiegészíti egymást hézagok kitöltésével.

Ez a cikk ismerteti a különböző virtuális gép ajánlott biztonsági eljárások, minden ügyfél és a Microsoft saját virtuális gépek közvetlen környezeteket származtatva.

Az ajánlott eljárások a vélemény konszenzus alapulnak, és dolgozhat az aktuális Azure platform olyan képességeit, és a szolgáltatáskészletek. Vélemények és technológiák idővel változhat, mert ez a cikk frissülni fog ezekkel a módosításokkal.

## <a name="use-privileged-access-workstations"></a>Emelt hozzáférési szintű munkaállomások használata

Szervezetek gyakran tartoznak kihasználják ellenállóbbak a kibertámadásokkal szemben, mert a rendszergazdák a műveletek emelt szintű jogosultságokkal rendelkező fiókok használata során. Bár ez nem lehetséges a rosszindulatú tevékenység eredményét, történik, mert a meglévő konfigurációt és a folyamatok engedélyezi. Ezek a felhasználók a legtöbb megismerheti ezeket a műveleteket a fogalmi szempontból kockázatát, de továbbra is választani végezheti el.

Ennek során például az e-mailekhez és az internetes böngészéshez elég álcázva tűnik. De esetleg felfedi a emelt szintű veszélyezteti a rosszindulatú fiókokat. Böngészés a tevékenységek, a speciálisan kialakított e-mailek vagy más módszerek segítségével hozzáférhet a vállalati. Erősen ajánlott az összes az Azure felügyeleti feladatok elvégzéséhez biztonságos felügyeleti munkaállomás (saw) használatát. Saw kevésbé legyen kitéve a véletlen biztonsági sérülés módon.

Emelt szintű hozzáféréssel rendelkező munkaállomások (paw-k) a bizalmas feladatokhoz –, amely védett az internetről érkező támadások és fenyegetési vektoroknak adjon meg egy dedikált operációs rendszert. Bizalmas feladatoknak és fiókoknak a napi használati munkaállomások és eszközök szétválasztása erős védelmet biztosít. Ez a fajta elkülönítés korlátozza a adathalász támadások, az alkalmazás és az operációs rendszer biztonsági réseket, különböző megszemélyesítési támadások és hitelesítő adatok ellopására irányuló támadásokkal. (billentyűleütések, naplózás, a Pass-the-Hash és Pass-the-Ticket)

Az emelt hozzáférési szintű munkaállomás megközelítés az kiterjesztése, a jól bevált és ajánlott eljárás egy külön-külön hozzárendelt rendszergazdai fiókot használja. A rendszergazdai fiók nem azonos az egy normál felhasználói fiókot. Egy emelt hozzáférési szintű munkaállomás megbízható munkaállomásokat biztosít ezeknek a bizalmas fiókoknak.

További információk és megvalósítási útmutató: [emelt szintű hozzáféréssel rendelkező munkaállomások](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations).

## <a name="use-multi-factor-authentication"></a>Multi-factor Authentication szolgáltatás használata

A múltban a hálózat szegélyén a céges adatokhoz való hozzáférésének lett megadva. Identitás-és felhőközpontú, mobil-és felhőközpontú világában a a vezérlősík: a bármilyen eszközön az IaaS-szolgáltatások hozzáférésének használja. Akkor is használhatja átláthatja és betekintést, és megismerheti az adatok használatban van-e. Az előfizetések származó adatokkal való visszaélés és más cybercrimes védelme alappillére védelme a digitális identitásokat az Azure-felhasználóval is.

Fiókja védelme érdekében a legtöbb előnyös szükséges egyik kétfaktoros hitelesítés engedélyezéséhez. A kétfaktoros hitelesítés módja a hitelesítése valami mellett jelszó használatával. Ez segít a kockázatát, hogy valaki más jelszavát kezeli, aki hozzáféréssel.

[Az Azure multi-factor Authentication](../active-directory/authentication/multi-factor-authentication.md) segítségével megakadályozhatja a adatokhoz és alkalmazásokhoz való hozzáférés, miközben a felhasználó igény szerint egy egyszerű bejelentkezési folyamat. Erős hitelesítés több egyszerű ellenőrzési lehetőség – a telefonhívás, SMS vagy mobilalkalmazásbeli értesítés kínál. Felhasználók válassza ki a módszert, amelyet a számukra megfelelőt.

A multi-factor Authentication legegyszerűbb módja a Microsoft Authenticator mobilalkalmazással, amely a Windows, iOS és Android rendszerű mobileszközök is használható. Windows 10-es és az integráció a helyszíni Active Directory az Azure Active Directoryval (Azure AD) legújabb kiadása [Windows Hello for Business](../active-directory/active-directory-azureadjoin-passport-deployment.md) használható zökkenőmentes egyszeri bejelentkezést az Azure-erőforrásokhoz. Ebben az esetben a Windows 10 rendszerű eszköz szolgál a második tényezőként a hitelesítéshez.

-Fiókok kezelése az Azure-előfizetés és fiókok, amelyek a virtuális gépek jelentkezhetnek be a multi-factor Authentication szolgáltatás használatával biztosít sokkal nagyobb biztonsági szintet, mint a jelszó csak használatával. Másfajta kétfaktoros hitelesítés ugyanolyan jól működik, de a központi telepítés bonyolult lehet, ha azok még nem éles környezetben.

A következő képernyőképen látható néhány, az Azure multi-factor Authentication elérhető beállításokról:

![Többtényezős hitelesítés beállításai](./media/azure-security-iaas/mfa-options.png)

## <a name="limit-and-constrain-administrative-access"></a>Korlátozza és korlátozhatja a rendszergazdai hozzáférést

Fontos, biztonságossá tétele a fiókokat, amelyek az Azure-előfizetés kezelése céljából. A biztonság sérülésének, amelyek közül bármelyik ellentettjét adja a lépést a védelme érdekében a alkalmazásproblémával értékét, és az adatok sértetlenségét. Nemrég, ábrákkal szemléltetett által a [Edward Snowden](https://en.wikipedia.org/wiki/Edward_Snowden) belső támadások veszélyeztetik a hatalmas bármely szervezet általános biztonságosságát.

Egyéni felhasználók számára a rendszergazdai jogosultságokkal kiértékelése hasonló alábbi szempontok alapján:

- Rendszergazdai jogosultságokat igénylő feladatok azok végrehajtása?
- Milyen gyakran a feladatok végrehajtásának?
- Van valamilyen konkrét érv amellett, hogy miért a feladatok nem hajtható végre egy másik rendszergazda helyettük?

A dokumentum minden egyéb ismert alternatív megközelítés a jogosultsággal, és miért nem elfogadhatók.

Igény szerinti felügyelet megakadályozza, hogy a szükségtelen létezik-e emelt szintű jogosultsággal rendelkező fiókokhoz időszakokban, amikor nincs szükség van a ezeket a jogokat. Fiókok rendelkezik emelt szintű jogosultságokkal korlátozott ideig, hogy a rendszergazdák teheti meg, amelyek a feladataik. Ezt követően ezeket a jogokat törlődnek a shift végén, vagy amikor egy feladat befejeződött.

Használhat [Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) kezeléséhez, monitorozzák és irányítják eléréséhez a szervezet. Ez segít vegye figyelembe a műveletek, amelyek egyéni felhasználók számára a szervezetben. Emellett lehetővé teszi igény szerinti felügyelet az Azure AD a jogosult rendszergazda fogalmát bevezetésével. Ezek a fiókok rendszergazdai engedélyeket a lehetséges rendelkező egyének. Az ilyen típusú felhasználók aktiválási folyamat végigveheti és kell rendszergazdai jogokat kapnak a korlátozott ideig.

## <a name="use-devtest-labs"></a>DevTest Labs használatát

A labs és fejlesztési környezetek Azure elveszi az késleltetések, amely bemutatja a hardver beszerzése. Ez lehetővé teszi a szervezetek számára, hogy a tesztelés és fejlesztés agilitás. Egy Azure-ban vagy az elfogadását meggyorsítása érdekében kívánság ismeretét hiánya, másrészt a rendszergazdát, hogy lehet túlzottan megengedő a jogok kiosztása vezethet. Előfordulhat, hogy a kockázat véletlenül elérhetővé tenni a szervezet belső támadásokkal szemben. Néhány felhasználó előfordulhat, hogy hozzáférést sokkal több mint kell rendelkezniük.

A [Azure DevTest Labs](../devtest-lab/devtest-lab-overview.md) használja [Azure szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md) (RBAC). Az RBAC használatával akkor is feladatköröket a csapaton belül szerepkörökhöz, amelyek csak a felhasználók számára a feladataik elvégzéséhez szükséges hozzáférési szintet biztosítanak. Az RBAC előre definiált szerepkörök (tulajdonos, labor felhasználói és közreműködői) tartalmaz. Ezek a szerepkörök segítségével akár külső partnerekkel jogosultságokat rendelhet hozzá, és jelentősen leegyszerűsítik az együttműködés.

DevTest Labs RBAC használatával történik, mert azt is létrehozhat további, [egyéni szerepkörök](../lab-services/devtest-lab-grant-user-permissions-to-specific-lab-policies.md). DevTest Labs nemcsak leegyszerűsíti az engedélyek kezelésének, egyszerűbbé téve a kiépített környezetek első folyamatát. Azt is segítségével kezelheti a más tipikus kihívást, csoportok, amelyek a fejlesztési-tesztelési környezetet is dolgozunk. Néhány előkészítő igényel, de a hosszú távú, azt fogja egyszerűbbé tételéhez a csapat számára.

Az Azure DevTest Labs-funkciók a következők:

- A felhasználók számára elérhető beállítások felügyeleti szabályozhatja. A rendszergazda központilag kezelheti többek között engedélyezett Virtuálisgép-méretek, maximális számát a virtuális gépek és a virtuális gépek indítása és leállítása.
- Tesztlabor-környezet létrehozása automatizálását.
- Költségek nyomon követése.
- Virtuális gépek egyszerűsített eloszlása által biztosított együttműködési környezettel végzett munkához.
- Önkiszolgáló, amely lehetővé teszi a felhasználók kiépítése a labs-sablonokkal.
- Kezelése, a használat korlátozása.

![DevTest Labs](./media/azure-security-iaas/devtestlabs.png)

További költségek nélkül nem tartozik a DevTest Labs használatát. Laborok, szabályzatok, sablonok és összetevők létrehozásának díjmentes. Csak az Azure-erőforrások használják a Labs-környezetben, például a virtuális gépek, tárfiókok és virtuális hálózatok kell fizetnie.

## <a name="control-and-limit-endpoint-access"></a>Vezérlő és a limit végponti hozzáférés

Labs vagy éles rendszereket az Azure-ban, az azt jelenti, hogy a rendszer elérhetőnek kell lenniük az internetről. Alapértelmezés szerint egy új Windows virtuális gép még elérhető az internetről érkező RDP-portjára, és a Linux rendszerű virtuális gép rendelkezik az SSH-port megnyitásához. Lépések megtételének korlát elérhetővé tett végpontra szükség a jogosulatlan hozzáférés kockázatát.

Az Azure-ban technológiák segítségével korlátozza a hozzáférést a felügyeleti végpontokhoz. Az Azure-ban használható [hálózati biztonsági csoportok](../virtual-network/security-overview.md) (NSG-k). Az üzembe helyezés Azure Resource Manager használatakor az NSG-k korlátozza a hozzáférést csak a felügyeleti végpontok (RDP vagy SSH) minden hálózatból. Úgy gondolja, hogy az NSG-ket, ha úgy gondolja, hogy útválasztó ACL-ek. Szorosan szabályozhatja a különböző szegmensek vagy az Azure-hálózatok közötti hálózati kommunikáció használhatja őket. Ez hasonlít a szegélyhálózaton vagy más elkülönített hálózatok hálózatok létrehozása. Ezek nem megvizsgálhatja a forgalmat, de ezek segítenek hálózati szegmentálást.

Virtuális gépek elérésének korlátozására dinamikusabb úgy, hogy az Azure Security center [igény szerinti felügyelet](../security-center/security-center-just-in-time.md). A Security center zárolhatja az Azure-beli virtuális gépek, és szükség esetén hozzáférést biztosít. A folyamat a hozzáférés egy felhasználónak, miután ellenőrizte, hogy alapján kérő azáltal, hogy működik a [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md) (RBAC) a szükséges engedélyekkel rendelkeznek. Az Azure Security center lesz végezze el a szükséges hálózati biztonsági csoportok (NSG-ket) engedélyezik a bejövő forgalmat.

### <a name="site-to-site-vpnvpn-gatewayvpn-gateway-howto-site-to-site-resource-manager-portalmd"></a>[Site-to-site VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

A site-to-site VPN kiterjeszti a helyszíni hálózatot a felhőre. Ez lehetővé teszi egy másik lehetőség az NSG-k, mivel az NSG-ket nem engedélyezi a hozzáférést bárhonnan más, mint a helyi hálózaton is módosíthatja. Ezután megkövetelheti, hogy felügyeleti először kapcsolódik az Azure-hálózatot VPN-en keresztül történik.

Lehet, hogy a site-to-site VPN lehetőséget legvonzóbb azokban az esetekben, ahol éles rendszereket, amelyek szorosan integrálhatók a helyszíni erőforrások az Azure-ban üzemel.

### <a name="point-to-sitevpn-gatewayvpn-gateway-howto-point-to-site-rm-psmd"></a>[Pont – hely](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

Az olyan helyzetben, amikor a rendszerek, amelyeket nem kell a helyszíni erőforrásokhoz való hozzáférés kezeléséhez. Ezekhez a rendszerekhez is különíthető el a saját Azure virtuális hálózatban. A rendszergazdák is az Azure-ba való VPN üzemeltetett környezetben a felügyeleti munkaállomásról.

>[!NOTE]
>Vagy a VPN-beállítás segítségével konfigurálja újra a hozzáférés-vezérlési listák nem engedélyezi az internetről érkező felügyeleti végpontok való hozzáférést az NSG-ket.

### <a name="remote-desktop-gatewayactive-directoryauthenticationhowto-mfaserver-nps-rdgmd"></a>[Távoli asztali átjáró](../active-directory/authentication/howto-mfaserver-nps-rdg.md)

Távoli asztali átjáró használatával biztonságosan csatlakozhat a távoli asztal-kiszolgálók HTTPS protokollal, ezeket a kapcsolatokat a részletesebb vezérlők alkalmazása közben.

Az funkciók, amelyek, amelyhez hozzáfér, és tartalmazzák:

- Korlátozza a kapcsolatokat a kérelmekre adott rendszerekből a felügyeleti beállításokat.
- Intelligens kártyás hitelesítést vagy az Azure multi-factor Authentication szolgáltatás.
- Szabályozhatja, hogy mely rendszerek keresztül valakinek csatlakozhat az átjárón keresztül.
- Lemez és az eszköz átirányítása felett ellenőrzést.

### <a name="vm-availability"></a>Virtuális gépek rendelkezésre állása

Ha egy virtuális gép fut, szeretné, hogy a magas rendelkezésre állást igénylő kritikus fontosságú alkalmazásai, Határozottan javasoljuk, hogy több virtuális gép használja. A jobb rendelkezésre állás érdekében legalább két virtuális gépet a létrehozása a [rendelkezésre állási csoport](../virtual-machines/windows/tutorial-availability-sets.md).

[Az Azure Load Balancer](../load-balancer/load-balancer-overview.md) is szükséges, hogy elosztott terhelésű virtuális gépek ugyanazon rendelkezésre állási csoporthoz tartozik-e. Ha ezek a virtuális gépek kell elérni az internetről, konfigurálnia kell egy [internetre irányuló terheléselosztó](../load-balancer/load-balancer-internet-overview.md).

## <a name="use-a-key-management-solution"></a>Kulcskezelés megoldással

A biztonságos kulcskezelésnek fontos szerepe van a felhőben tárolt adatok védelmében. A [Azure Key Vault](../key-vault/key-vault-whatis.md), tudja biztonságosan tárolni a titkosítási kulcsokat és a kisebb méretű titkokat – például jelszavakat hardveres biztonsági modulokban (HSM). A még nagyobb biztonság érdekében lehetőség van arra is, hogy kulcsokat importáljon és generáljon a hardveres biztonsági modulokban.

A Microsoft folyamatokat a kulcsokat a FIPS 140-2. szintű 2 ellenőrzött hardveres biztonsági modulokban (hardverek és belső vezérlőprogramot). Az Azure naplózási monitorozhatja és naplózhatja kulcshasználatot: Azure vagy a biztonságiadat- és eseménykezelés (SIEM) rendszerbe további elemzés és veszélyészlelés céljából észlelési szolgáltatása.

Azure-előfizetések bárki létrehozhat és használhat kulcstárolót. Bár a Key Vault elsősorban a fejlesztők és a biztonsági rendszergazdák, megvalósítva, és a szervezet Azure-szolgáltatások kezeléséhez felelős rendszergazda felügyeli.

## <a name="encrypt-virtual-disks-and-disk-storage"></a>Virtuális lemezek és a lemezes tárolás titkosítása

[Az Azure Disk Encryption](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0) címek a fenyegetés, illetve kitettség a jogosulatlan hozzáférés ellen, amelyek egy lemezt úgy valósítható meg. A lemez egy másik rendszer adtuk meg, a rendszer megkerüli az egyéb biztonsági vezérlőt is csatolható. Lemez titkosítása használ [BitLocker](https://technet.microsoft.com/library/hh831713) a Windows és a Linux DM-Crypt az operációs rendszer és az adatmeghajtók titkosítása. Az Azure Disk Encryption vezérlőelemet, és kezelje a titkosítási kulcsokat a Key Vault integrálható. Ez a premium storage standard virtuális gépek és virtuális gépek érhető el.

További információkért lásd: [az Azure Disk Encryption Windows és Linux rendszerű IaaS virtuális gépek](azure-security-disk-encryption.md).

[Az Azure Storage Service Encryption](../storage/common/storage-service-encryption.md) segítséget nyújt az inaktív adatok védelme. Engedélyezve van a storage-fiók szintjén. Azt titkosítani fogja az adatokat adatközpontjainkban van írva, és automatikusan visszafejteni, éri el. A következő forgatókönyveket támogatja:

- Titkosítási annak blokkblobokat, hozzáfűző blobokat és lapblobokat
- Archivált VHD-k és a helyszínről az Azure-bA indított sablonok titkosítása
- IaaS virtuális gépekhez, amely a VHD-k használatával létrehozott alapjául szolgáló operációs rendszer és az adatlemezek titkosítása

Mielőtt továbblép az Azure Storage-titkosítás, két korlátozások figyelembe:

- Nem érhető el a klasszikus tárfiókokat.
- Csak a titkosítás engedélyezése után írt adatok, az titkosítja.

## <a name="use-a-centralized-security-management-system"></a>A központi felügyeleti rendszer használata

A kiszolgálókat kell figyelni a javítás, konfigurációs, események és tevékenységek, előfordulhat, hogy a biztonsági szempontok figyelembe kell venni. Ezek a problémák megoldására használható [a Security Center](https://azure.microsoft.com/services/security-center/) és [Operations Management Suite biztonsági és megfelelőségi](https://azure.microsoft.com/services/security-center/). Mindkét lehetőség nemcsak a konfigurációt az operációs rendszerben. Ezenkívül tartalmaznak az alapul szolgáló infrastruktúrát, például a hálózati konfigurációs és virtuális berendezés használata konfigurációjának ellenőrzése.

## <a name="manage-operating-systems"></a>Operációs rendszerek felügyelete

Egy IaaS-telepítésben, továbbra is Ön felelős a felügyeleti rendszerek üzembe helyezett, csakúgy, mint bármely más kiszolgálóra vagy munkaállomásra a környezetben. Javítás, vezethet be, engedélyek és a rendszer a karbantartás kapcsolatos bármely más tevékenységre program továbbra is Ön felelőssége. A helyszíni erőforrásokkal, szorosan integrált rendszerek esetén előfordulhat, hogy használni kívánt ugyanazokat az eszközöket és eljárásokat, hogy a helyszíni használja például a víruskereső, a kártevőirtó, a javítás és a biztonsági mentés.

### <a name="harden-systems"></a>Rendszerek megerősítése

Azure IaaS virtuális gépek összes kell lennie megerősített, úgy, hogy csak a telepített alkalmazások számára szükséges Szolgáltatásvégpontok szolgáltatnak. Windows virtuális gépek esetében kövesse a Microsoft által közzétett alapterveit, javaslatok a [Security Compliance Manager](https://technet.microsoft.com/solutionaccelerators/cc835245.aspx) megoldás.

Security Compliance Manager egy olyan ingyenes eszköz. Segítségével, gyorsan konfigurálhatja, és az asztali számítógépek, a hagyományos adatközponti és privát és nyilvános felhő kezelése csoportházirend és a System Center Configuration Manager használatával.

Security Compliance Manager házirendek kész a központi telepítése és a Szükségeskonfiguráció-kezelési konfigurációs csomagokat, hogy a tesztelés biztosít. Ezek alaptervek alapuló [a Microsoft biztonsági útmutatója](https://technet.microsoft.com/library/cc184906.aspx) javaslatok és iparági ajánlott eljárások. Ezek segítségével kezelheti a konfigurációs csúszásokat cím megfelelőségi követelményeknek, és csökkentheti a biztonsági fenyegetéseket.

Security Compliance Manager segítségével importálhatja az aktuális konfigurációját, a számítógépek két különböző módszer használatával. Először az Active Directory-alapú csoportházirendek importálhatja. Második, importálhatja a konfigurációs "arany fő" referencia-számítógép segítségével az [LocalGPO eszköz](https://blogs.technet.microsoft.com/secguide/2016/01/21/lgpo-exe-local-group-policy-object-utility-v1-0/) biztonsági mentését a helyi csoportházirendet. Ezután importálhatja a helyi csoportházirend Security Compliance Manager.

Hasonlítsa össze a iparági gyakorlati ajánlások, szabványok, testre is szabhatja őket, és hozzon létre új házirendek és a Szükségeskonfiguráció-kezelési konfigurációs csomagokat. Alapkonfigurációk közzétett összes támogatott operációs rendszerekhez, beleértve a Windows 10 Évfordulós frissítését és a Windows Server 2016-ban.


### <a name="install-and-manage-antimalware"></a>Telepítse és kártevőirtó kezelése

Az éles környezetben a külön-külön üzemeltetett környezetben használhatja a kártevőirtó bővítmény segítségével megvédheti virtuális gépeit és a felhőszolgáltatások. Együttműködik a [az Azure Security Center](../security-center/security-center-intro.md).

[A Microsoft Antimalware](azure-security-antimalware.md) magában foglalja a szolgáltatások, mint például a valós idejű védelem, ütemezett vizsgálatát, kártevő szoftverek eltávolítása, aláírás frissítések, motor frissítéseit, jelentéskészítés, a kizárási eseménygyűjtés, minták és [PowerShell-támogatás](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureserviceantimalwareextension).

![Az Azure kártevőirtó](./media/azure-security-iaas/azantimalware.png)

### <a name="install-the-latest-security-updates"></a>Telepítse a legújabb biztonsági frissítéseket 

Az első számítási feladatokat, hogy ügyfeleink az Azure-bA néhány labs és kívülre irányuló rendszerek. Az Azure-ban üzemeltetett virtuális gépek üzemeltetéséhez, alkalmazásokat vagy szolgáltatásokat, kell lennie az interneten érhető el, ha éberen javítással kapcsolatban. Javítás az operációs rendszer mellett. A harmadik féltől származó alkalmazások veszéllyel biztonsági réseket is vezethet problémákat, amelyek elkerülhető, ha a helyes javításkezelőhöz van beállítva.

### <a name="deploy-and-test-a-backup-solution"></a>Telepítse és tesztelje a biztonsági mentési megoldások

Csakúgy, mint a biztonsági frissítések biztonsági mentést kell kezelni ugyanolyan módon, hogy kezelje-e fel más műveletekhez. Ez a rendszerek, amelyek részei az éles környezet kiterjesztése a felhőbe. Teszt és fejlesztési rendszerek hasonló felhasználók hozzászoktak rendelkezik a helyszíni környezetben szerzett tapasztalataikról alapján visszaállítási képességeket biztosító biztonsági mentési stratégia kell követnie.

Éles számítási feladatok Azure-bA áthelyezni kell integrálható a meglévő biztonsági mentési megoldások, amikor csak lehetséges. Vagy használhat [Azure Backup](../backup/backup-azure-arm-vms.md) segítségünkre a biztonsági mentés követelményeinek.

## <a name="monitor"></a>Figyelés

### <a name="security-centersecurity-centersecurity-center-intromd"></a>[Security Center](../security-center/security-center-intro.md)

A Security center a potenciális biztonsági rések azonosításához az Azure-erőforrások biztonsági állapotának folyamatos értékelését biztosít. A javaslatok listája végigvezeti Önt a szükséges szabályozási folyamatok konfigurálásának folyamatán.

Példák erre vonatkozóan:

- Azonosítsa és eltávolítani a kártevő szoftvereket kártevőirtók kiépítése.
- Hálózati biztonsági csoportok és a virtuális gépek forgalmának ellenőrzésére vonatkozó szabályok konfigurálása.
- Webalkalmazás-tűzfalak érdekében célba támadások elleni védelemre a webes alkalmazások kiépítése.
- Hiányzó rendszerfrissítések telepítése.
- Operációsrendszer-konfigurációkat, nem egyezik a javasolt címzéshez.

Az alábbi képen látható, amely a Security Center engedélyezheti az opciók egy része.

![Az Azure Security Center-szabályzatok](./media/azure-security-iaas/security-center-policies.png)

### <a name="operations-management-suiteoperations-management-suiteoperations-management-suite-overviewmd"></a>[Operations Management Suite](../operations-management-suite/operations-management-suite-overview.md) 

Operation Management Suite a Microsoft felhőalapú informatikai felügyeleti megoldása, amely segít kezelése és védelme a helyszíni és felhőalapú infrastruktúrára. Mivel az Operations Management Suite egy felhőalapú szolgáltatás van megvalósítva, gyorsan és az infrastruktúra-erőforrások fordítandó minimális kiadások mellett telepíthető.

Új funkciók bevezetése automatikus, mentése, a folyamatos karbantartás és frissítés költségei. Az Operations Management Suite is integrálható a System Center Operations Managerrel. Rendelkezik, amelyek segítségével jobban kezelheti az Azure számítási feladatokat, beleértve a különböző összetevőkkel egy [biztonsági és megfelelőségi](../operations-management-suite/oms-security-getting-started.md) modul.

A biztonsági és megfelelőségi szolgáltatást az Operations Management Suite segítségével az erőforrások adatainak megjelenítéséhez. Az információt négy fő kategóriába sorolhatók:

- **Biztonsági tartományok**: részletesebb megismerése a biztonsági rekordok időbeli alakulása. Kártevőfelmérés eléréséhez, biztonsági eseményekkel rendelkező értékelési, hálózati biztonság, identitás- és hozzáférés és adatait, valamint számítógépek frissítéséhez. Kihasználhatja a gyors hozzáférés az Azure Security Center irányítópultján.
- **Jelentős problémák**: gyorsan azonosíthatja az aktív problémák számát, és ezek a problémák súlyosságát.
- **Észlelések (előnézet)**: azonosítására a támadási minták, akkor fordulhat elő, az erőforrásokon, a biztonsági riasztások vizualizációjával.
- **Fenyegetések felderítése**: azonosítására a támadási minták száma a kimenő rosszindulatú IP-forgalom, a rosszindulatú fenyegetések típusának és ahol ezeket az IP-címekről érkeznek egy térképre kiszolgálók vizualizációjával.
- **Gyakori biztonsági lekérdezések**: a leggyakoribb biztonsági lekérdezésekről, amelyek segítségével figyelni a környezetet listájának megtekintéséhez. Ha egy ilyen lekérdezésre kattint a **keresési** panel megnyitja és megjeleníti a lekérdezés eredményeit.

A következő képernyőképen látható egy példa a Operations Management Suite megjelenő információkat.

![Az Operations Management Suite biztonsági előírások](./media/azure-security-iaas/oms-security-baseline.png)

### <a name="monitor-vm-performance"></a>Virtuális gép teljesítményének monitorozása

Erőforráshoz való visszaélés problémát jelenthetnek, ha a virtuális gép folyamat több erőforrást, mint azok kell. Teljesítménnyel kapcsolatos problémák a virtuális gép a szolgáltatáskimaradás, amely megsérti a rendelkezésre állási rendszerbiztonsági tagot vezethet. Ezért elengedhetetlen a probléma jelentkezik, míg nem csak reaktív figyelése a Virtuálisgép-hozzáférés, de proaktív módon is alapteljesítményének a normál működés során mért ellen.

Elemzésével [Azure diagnosztikai naplófájlok](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/), a Virtuálisgép-erőforrások figyeléséhez, és azonosítani a potenciális problémákat, mely veszélyeztetheti a teljesítmény és rendelkezésre állás. Az Azure Diagnostics bővítmény monitorozási és diagnosztikai képességeket biztosít a Windows-alapú virtuális gépeken. Ezek a képességek engedélyezéséhez a kiterjesztéssel együtt részeként a [Azure Resource Manager-sablon](../virtual-machines/windows/extensions-diagnostics-template.md).

Is [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md) számára az erőforrások állapotát.

Szervezetek számára, amelyek a virtuális gép teljesítményének figyelése nem tudnak annak meghatározásához, hogy bizonyos változtatásokat a teljesítmény minták normál és rendellenes. A virtuális gép értéke a szokásosnál több erőforrást is használja, ha egy ilyen anomáliadetektálási jelezheti a potenciális támadások egy külső erőforrás vagy a virtuális gépen futó feltört folyamat.

## <a name="next-steps"></a>További lépések

* [Az Azure Security csapat blogja](https://blogs.msdn.microsoft.com/azuresecurity/)
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx)
* [Az Azure ajánlott biztonsági eljárások és minták](security-best-practices-and-patterns.md)
