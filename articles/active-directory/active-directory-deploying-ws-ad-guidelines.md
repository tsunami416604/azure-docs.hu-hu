---
title: "Windows Server Active Directory az Azure virtuális gépeken telepítési útmutatója |} Microsoft Docs"
description: "Ha ismeri az AD tartományi szolgáltatások és a helyszíni AD összevonási szolgáltatások telepítése, megtudhatja, hogyan működnek az Azure virtuális gépeken."
services: active-directory
documentationcenter: 
author: femila
manager: mtillman
editor: 
ms.assetid: 04df4c46-e6b6-4754-960a-57b823d617fa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/26/2017
ms.author: femila
ms.openlocfilehash: 61f84233b90cb3c82ed928d59ad2a090f7eb50b7
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2018
---
# <a name="guidelines-for-deploying-windows-server-active-directory-on-azure-virtual-machines"></a>Windows Server Active Directory telepítése Azure virtuális gépekre vonatkozó irányelvek
Ez a cikk ismerteti a központi telepítése Windows Server Active Directory tartományi szolgáltatások (AD DS) és az Active Directory összevonási szolgáltatások (AD FS) helyszíni és a Microsoft Azure virtuális gépeken történő üzembe fontos különbségei.

## <a name="scope-and-audience"></a>Hatókör és a célközönség
A cikk célja az azok helyszíni Active Directory üzembe helyezésével járó már észlelt. Az Active Directory telepítése a Microsoft Azure virtuális gépek vagy az Azure virtuális hálózatok és a hagyományos a helyszíni Active Directory-környezetekben közötti különbségek magában foglalja. Az Azure virtuális gépek és az Azure virtuális hálózatok részei egy infrastruktúra-,--szolgáltatás (IaaS) kínáló szervezetek számára a felhőalapú számítási erőforrások használatát.

Azokat, amelyeket nem ismeri a AD telepítés, tekintse meg a [Active Directory tartományi szolgáltatások telepítési útmutatója](https://technet.microsoft.com/library/cc753963) vagy [az AD FS üzembe helyezés megtervezésében](https://technet.microsoft.com/library/dn151324.aspx) szükség szerint.

Ez a cikk feltételezi, hogy az olvasó ismeri a következő fogalmak:

* Windows Server AD DS üzembe helyezése és kezelése
* A Windows Server AD DS-infrastruktúráinak támogatása a DNS telepítését és konfigurálását
* Windows Server AD FS telepítése és kezelése
* Telepítése, konfigurálása és kezelése a függő entitások alkalmazásainak (webhelyek és webalkalmazások szolgáltatások), Windows Server AD FS-jogkivonatokat médiafolyamainak fogadására
* Általános virtuális gép fogalmakkal, mint egy virtuális gépet, a virtuális lemezek és a virtuális hálózatok konfigurálása

Ez a cikk emel ki, melyik Windows Server AD DS vagy AD FS részben telepített a helyszínen, míg részben telepítve az Azure virtuális gépeken egy hibrid környezet-forgatókönyv követelményei. A dokumentum először a Windows Server AD DS és AD FS-hez, és a fontos döntési pontokat, amelyek hatással vannak a tervezési és üzembe helyezési és az Azure virtuális gépeken futó kritikus különbségei ismerteti. A dokumentum többi ismerteti az egyes azon döntési pontokat részletes útmutatást, és alkalmazása az útmutatást a különböző telepítési forgatókönyvek.

Ez a cikk nem tér konfigurációjának [Azure Active Directory](http://azure.microsoft.com/services/active-directory/), ez az identitás kezelése és hozzáférés-vezérlés képességeinek felhőalkalmazásokhoz biztosító REST-alapú szolgáltatás. Az Azure Active Directory (Azure AD) és a Windows Server Active Directory tartományi Szolgáltatásokban, azonban tervezték, hogy együttműködve biztosítják az identitás- és hozzáférés-kezelési megoldás a mai hibrid informatikai környezetekben, és a modern alkalmazások. Megismeréséhez, és a Windows Server Active Directory tartományi szolgáltatások és az Azure AD közötti kapcsolatok különbségeket, vegye figyelembe a következőket:

1. Előfordulhat, hogy futtatja a Windows Server Active Directory tartományi szolgáltatások a felhőben Azure virtuális gépeken futó Azure kiterjesztése a helyszíni adatközpontját a felhőre való használata esetén.
2. Használhatja az Azure AD-,-a-szoftverszolgáltatás (SaaS) alkalmazások a felhasználók egyszeri bejelentkezést biztosítanak. A Microsoft Office 365 ezt a technológiát használja, például és Azure-ban vagy más felhőalapú platformokon futó alkalmazásokhoz is használható.
3. Használhatja az Azure AD (a hozzáférés-vezérlési szolgáltatásban), hogy a felhasználók bejelentkezés Facebook, Google, a Microsoft és egyéb identitás-szolgáltatóktól származó identitásokkal a felhőben, vagy a helyszínen tárolt alkalmazások számára.

Ezek a különbségek kapcsolatos további információkért lásd: [Azure Identity](fundamentals-identity.md).

## <a name="related-resources"></a>Kapcsolódó források (lehet, hogy a cikkek angol nyelvűek)
Előfordulhat, hogy töltse le és futtassa a [Azure virtuális gép Readiness Assessment](https://www.microsoft.com/download/details.aspx?id=40898). Az értékelés automatikusan vizsgálja meg a helyszíni környezetben, és testreszabott jelentést kell készítenie az útmutatóban található ebben a témakörben, amelyik segítségére lehet a környezet az Azure-bA alapján.

Javasoljuk, hogy először is tekintse át az oktatóprogramok útmutatók és videók, amely a következő témákkal:

* [Csak felhőalapú virtuális hálózat konfigurálása az Azure-portálon](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)
* [A telephelyek közötti VPN konfigurálása az Azure-portálon](../vpn-gateway/vpn-gateway-site-to-site-create.md)
* [Egy új Active Directory-erdő telepítése Azure virtuális hálózaton](active-directory-new-forest-virtual-machine.md)
* [Az Active Directory replika tartományvezérlő telepítése az Azure-on](active-directory-install-replica-active-directory-domain-controller.md)
* [A Microsoft Azure informatikai szakemberek IaaS: (01) virtuális gép – alapok](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [A Microsoft Azure informatikai szakemberek IaaS: (05) létrehozása virtuális hálózatok és a létesítmények közötti kapcsolat](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

## <a name="introduction"></a>Bevezetés
A Windows Server Active Directory telepítése Azure virtuális gépeken futó alapvető követelményei eltérőek nagyon kevés a helyszíni virtuális gépek (és egyes mértékben, fizikai gépek) központi telepítése. Például esetre, ha a Windows Server Active Directory tartományi szolgáltatások, ha a tartományvezérlők (DC), amely az Azure virtuális gépeken telepít egy meglévő replikák helyszíni vállalati tartományban/erdőben, akkor az Azure-telepítés nagy mértékben lehet kezelni, ugyanúgy, mint bármely más további Windows Server Active Directory-hely előfordulhat, hogy kezelni. Ez azt jelenti, hogy alhálózatok definiálni kell a Windows Server Active Directory tartományi szolgáltatások, egy webhely hoztak létre, az alhálózatok, adott helyhez kapcsolódik, és kapcsolódik a megfelelő helykapcsolatok segítségével más helyekre. Vannak, azonban néhány különbség az, hogy megegyeznek az összes Azure-környezetekhez, és néhány, az adott környezet függvényében. Két alapvető különbség az alább vázolt:

### <a name="azure-virtual-machines-may-need-connectivity-to-the-on-premises-corporate-network"></a>Azure virtuális gépeken is kell a helyszíni vállalati hálózathoz csatlakoznak.
Azure virtuális hálózat, amely egy pont-pont vagy a helykiszolgáló-pont virtuális magánhálózati (VPN) hálózati összetevőt tartalmaz az Azure virtuális gépek zökkenőmentesen kapcsolódni tud és a helyszíni gépeket az Azure virtuális gépek vissza a egy helyszíni vállalati hálózathoz való csatlakozás igényel. A VPN-összetevő azt is lehetővé teszi a helyi tartomány tagszámítógépeit, amelynek tartományvezérlők kizárólag az Azure virtuális gépeken üzemeltetett egy Windows Server Active Directory-tartomány eléréséhez. Fontos megjegyezni, azonban, hogy ha a VPN nem sikerül, hitelesítés és a Windows Server Active Directory függő egyéb műveletek is sikertelenek lesznek. Amíg a felhasználók tudnak a bejelentkezéshez szükséges meglévő gyorsítótárazott hitelesítő adatok, mind a társ-társ vagy, amelynek jegyek még kiállítását, vagy elavult ügyfél-kiszolgáló hitelesítési kísérlet sikertelen lesz.

Lásd: [virtuális hálózati](http://azure.microsoft.com/documentation/services/virtual-network/) bemutatója videó és lépésről lépésre haladó oktatóprogramot, beleértve a listájának [a telephelyek közötti VPN konfigurálása az Azure portálon](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [!NOTE]
> Egy Azure virtuális hálózaton, egy a helyszíni hálózati kapcsolattal nem rendelkező Windows Server Active Directory is telepítheti. Ebben a témakörben az irányelveket azonban azt feltételezik, hogy egy Azure virtuális hálózat használata IP-címzési alapvető Windows Server-képességeket biztosít.
> 
> 

### <a name="static-ip-addresses-must-be-configured-with-azure-powershell"></a>Statikus IP-címek az Azure PowerShell kell konfigurálni.
Dinamikus címek alapértelmezés szerint kiosztott, de a Set-AzureStaticVNetIP parancsmag a segítségével rendelhet hozzá egy statikus IP-cím helyett. Egy statikus IP-címet, amely szolgáltatásjavításnak és a virtuális gép leállítási vagy újraindítási keresztül egészen addig megmarad állítja. További információkért lásd: [statikus belső IP-címet a virtuális gépek](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/).

## <a name="BKMK_Glossary"></a>Kifejezések és meghatározások
A különböző Azure technológiákhoz, amely ebben a cikkben lesz hivatkozva feltételek nem teljes listáját a következő:

* **Az Azure virtuális gépek**: az IaaS ajánlat, amely lehetővé teszi az ügyfelek központi telepítése a futó virtuális gépeket szinte bármilyen hagyományosan az Azure-ban a helyszíni kiszolgáló munkaterhelését.
* **Azure-beli virtuális hálózat**: A hálózati szolgáltatás, amely lehetővé teszi, hogy a felhasználók létrehozása és kezelése az Azure virtuális hálózatairól és biztonságosan kapcsolja őket a saját Azure-ban a helyszíni hálózati infrastruktúrát a virtuális magánhálózat (VPN) segítségével.
* **Virtuális IP-cím**: az internet felé néző IP-cím, amely nincs kötve egy adott számítógép vagy a hálózati kártya. Felhőszolgáltatások rendelt a hálózati forgalmat, amely egy Azure virtuális Gépen a rendszer átirányítja egy virtuális IP-címet. Egy virtuális IP-cím egy tulajdonságát egy egy vagy több Azure virtuális gépet tartalmazó felhő-szolgáltatás. Ne feledje, hogy az Azure virtuális hálózat legalább egy felhő-szolgáltatások tartalmazhatnak. Virtuális IP-címet adja meg a natív terheléselosztási képességeit.
* **Dinamikus IP-cím**: Ez az az IP-címet, csak belső. Akkor kell beállítani, mint egy statikus IP-címet (a Set-AzureStaticVNetIP parancsmag használatával) virtuális gépekhez, amely a tartományvezérlő/DNS-kiszolgálói szerepköröket üzemeltetnie.
* **Szolgáltatás javító**: A folyamat, amelyben Azure automatikusan visszatér a szolgáltatás fut. újra után azt észleli, hogy a szolgáltatás nem tudta. Javítás szolgáltatás az egyik Azure rugalmasságot és rendelkezésre állást támogató aspektusait. Amíg nem valószínű, az eredmény a következő incidens javító egy virtuális gépen futó tartományvezérlő szolgáltatás egy nem tervezett újraindítás hasonló, de rendelkezik néhány-hatásai:
  
  * A virtuális hálózati adaptert a virtuális gép változik.
  * A virtuális hálózati adapter MAC-cím változik.
  * A virtuális gép processzor/CPU azonosítója változik.
  * A virtuális hálózati adapter IP-konfigurációja nem változik meg, amíg a virtuális gép egy virtuális hálózathoz kapcsolódik, és a virtuális gép IP-cím statikus.
  
  Windows Server Active Directory nincs ezek közül a viselkedésmódok hatással, mert a MAC-cím vagy a processzor/CPU-azonosítója nem függőségi, és minden Windows Server Active Directory-környezetekben Azure ajánlatos, hogy a fent leírt módon futtatható Azure virtuális hálózaton.

## <a name="is-it-safe-to-virtualize-windows-server-active-directory-domain-controllers"></a>Az Windows Server Active Directory-tartományvezérlők virtualizálásához a biztonságos?
A tartományvezérlők helyileg futó virtuális gépen azonos útmutatók az Azure virtuális gépeken futó Windows Server Active Directory-tartományvezérlők telepítése. A biztonságos célszerű futtató virtualizált tartományvezérlők, mindaddig, amíg biztonsági mentése és visszaállítása a tartományvezérlők vonatkozó irányelvek követi. Megkötések és iránymutatásokat futtató virtualizált tartományvezérlők kapcsolatos további információkért lásd: [tartományvezérlők futtatása Hyper-v](https://technet.microsoft.com/library/dd363553).

Hipervizorok nyújtanak, vagy trivialize, amely sok elosztott rendszerek, beleértve a Windows Server Active Directory problémákat okozhat. Például egy fizikai kiszolgálón klónozni egy lemezt, vagy nem támogatott módszerek használatával egy kiszolgáló, beleértve a San-ok használatával és így tovább, állapotának visszaállítása, de sokkal nehezebb, mint a hipervizor a virtuális gép pillanatkép visszaállítása, amely során a fizikai kiszolgálón. Az Azure azonos nemkívánatos állapotban eredményező funkciót kínál. Például nem másolja a tartományvezérlőket VHD-fájlok helyett rendszeres biztonsági mentést végez, mert visszaállítás eredményezhet a hasonló a pillanatkép-visszaállítási funkciókat használ.

Ilyen visszagörgetése vezethet véglegesen eltérő állapotok közötti tartományvezérlők USN-buborékok vezethet. Problémák, mint okozhat, amelyek:

* Fennmaradó objektumokról
* Inkonzisztens jelszavak
* Inkonzisztens attribútumértékek
* Séma nem egyezik meg, ha a sémakezelő főkiszolgáló vissza lesz állítva.

Hogyan tartományvezérlők érintett kapcsolatos további információkért lásd: [USN and USN visszaállítása](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv.aspx#usn_and_usn_rollback).

Windows Server 2012 rendszertől kezdődően [további védelmet az AD DS-ben beépített](https://technet.microsoft.com/library/hh831734.aspx). Ezek a funkciók védelme a fent említett problémák elleni virtualizált tartományvezérlők mindaddig, amíg az alapul szolgáló hipervizor platform támogatja a virtuális gép Generációazonosítóját. Azure virtuális gép Generációazonosítóját, ami azt jelenti, hogy a Windows Server 2012 vagy újabb Azure virtuális gépek futtató tartományvezérlők kell-e a további funkciók támogatja.

> [!NOTE]
> Kell állítsa le és indítsa újra a virtuális gép, amelyen a tartományvezérlői szerepkört az Azure-ban a vendég operációs rendszerben használata helyett a **Leállítás** beállítása az Azure portál. Napjainkban a portál használatával egy virtuális gép leállítása hatására a virtuális gép felszabadítása. A felszabadított virtuális gépek azt az előnyt, nem díjfizetési, de is alaphelyzetbe állítja a virtuális gép Generációazonosítóját, amely nem kívánatos egy tartományvezérlő. Amikor a virtuális gép Generációazonosítóját, az invocationid-t az AD DS-adatbázis is alaphelyzetbe áll, a program elveti a RID-vermet, és SYSVOL nem mérvadó van megjelölve. További információkért lásd: [Bevezetés az Active Directory tartományi szolgáltatások (AD DS) Virtualizálásába](https://technet.microsoft.com/library/hh831734.aspx) és [DFSR biztonságos virtualizálása](http://blogs.technet.com/b/filecab/archive/2013/04/05/safely-virtualizing-dfsr.aspx).
> 
> 

## <a name="why-deploy-windows-server-ad-ds-on-azure-virtual-machines"></a>Miért központi telepítése Windows Server AD DS Azure virtuális gépeken?
Windows Server Active Directory tartományi szolgáltatások számos telepítési forgatókönyv a központi telepítésben lévő Azure virtuális gépek is jól alkalmazható. Tegyük fel például egy vállalat kell hitelesítenie magát a felhasználók egy távoli helyen Ázsiában Európában. A vállalat nem korábban telepítve van a Windows Server Active Directory tartományvezérlők Ázsiában központi telepítése a telepítés utáni kiszolgálók kezeléséhez őket, és korlátozott szakértői költségek miatt. Ennek eredményeképpen Ázsia érkező hitelesítési kérések által kiszolgált tartományvezérlők, amelyek az Európai optimálisnál eredményekkel. Ebben az esetben is telepíthet a tartományvezérlő a virtuális gép által megadott Ázsiában az Azure adatközponton belül kell futtatni. Ennek a Tartományvezérlőnek csatolása egy Azure virtuális hálózatot, amely közvetlenül csatlakozik a távoli hely növelheti a hitelesítés teljesítményét.

Azure a is jól alkalmazható egyéb költséges vész-helyreállítási helyekre helyett. A viszonylag alacsony költségű a tartományvezérlők és az Azure-on egyetlen virtuális hálózat kis számú tárolására vonzó alternatív jelöli.

Végül érdemes lehet az Azure, a SharePoint, a Windows Server Active Directory szükséges, de nem függ a helyi hálózaton vagy a vállalati Windows Server Active Directory például egy hálózati alkalmazás központi telepítése. Ebben az esetben felel meg a SharePoint Azure-on egy elkülönített erdők üzembe helyezése kiszolgáló követelményei az optimális. A helyszíni hálózat és a vállalati Active Directory kapcsolatot igényelnek a hálózati alkalmazások telepítése újra, is támogatott.

> [!NOTE]
> 3. rétegbeli kapcsolatot biztosít, mivel egy Azure virtuális hálózatra és egy a helyszíni hálózat között kapcsolatot biztosít a VPN-összetevő is engedélyezhető a helyi kívánja kiterjeszteni, Azure virtuális gépként az Azure virtuális hálózaton futtató tartományvezérlők tagkiszolgálókon. De ha a VPN nem érhető el, közötti kommunikáció a helyszíni számítógépek és az Azure-alapú tartományvezérlők nem fog működni, így a hitelesítési és egyéb különféle hibák.  
> 
> 

## <a name="contrasts-between-deploying-windows-server-active-directory-domain-controllers-on-azure-virtual-machines-versus-on-premises"></a>Windows Server Active Directory tartományvezérlők Azure virtuális gépeken és a helyszíni üzembe helyezésének közötti nézete
* A Windows Server Active Directory telepítési forgatókönyvhöz, amely tartalmazza az több mint egy virtuális fontos Azure virtuális hálózat használata az IP-cím konzisztenciáját. Vegye figyelembe, hogy ez az útmutató feltételezi, hogy a tartományvezérlők futnak-e az Azure virtuális hálózaton.
* Csakúgy, mint a helyszíni tartományvezérlők, statikus IP-címek használata ajánlott. Egy statikus IP-cím csak Azure PowerShell használatával konfigurálható. Lásd: [statikus belső IP-cím, virtuális gépek](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/) további részleteket. Ha figyelő rendszerek vagy egyéb megoldások, ellenőrizze a statikus IP-címének konfigurációja, a vendég operációs rendszerben, a virtuális hálózati adapter tulajdonságainak hozzárendelheti a azonos statikus IP-címet. De vegye figyelembe, hogy a hálózati adapter elvesznek, ha a virtuális gép megy keresztül szolgáltatásjavításnak, vagy le van állítva a portálon, és rendelkezik a cím nincs lefoglalva. Ebben az esetben a vendégen belül a statikus IP-címet kell állítani.
* Virtuális gépek telepítése a virtuális hálózaton nem utalnak (vagy igényelnek) kapcsolattal egy a helyszíni hálózat; a virtuális hálózati csupán engedélyezi ezt a lehetőséget. Azure és a helyszíni hálózat között titkos kommunikációt egy virtuális hálózatot kell létrehoznia. Kell telepítenie a helyi hálózaton a VPN-végponttal. A VPN nyitja meg az Azure-ból a helyszíni hálózat. További információkért lásd: [virtuális hálózat áttekintése](../virtual-network/virtual-networks-overview.md) és [a telephelyek közötti VPN konfigurálása az Azure portálon](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [!NOTE]
> Egy lehetőség, hogy [hozzon létre egy pont – hely VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md) érhető el az egyes Windows-alapú számítógépek közvetlen csatlakoztatása az Azure virtuális hálózat.
> 
> 

* Függetlenül attól, hogy akkor hozzon létre egy virtuális hálózati vagy nem, a kimenő forgalom, de nem érkező Azure percalapú. Különböző Windows Server Active Directory tervezési döntések ütköznek azokkal hatással lehet a kimenő forgalom hozza létre a központi telepítés. Például egy írásvédett tartományvezérlő telepítéséhez (RODC) korlátozza a kilépő forgalmat, mert nem replikálja kimenő. Írásvédett tartományvezérlő telepítése a döntést kell mérlegelni lehet szükség a írási műveleteket a tartományvezérlő és a [kompatibilitási](https://technet.microsoft.com/library/cc755190) , alkalmazások és szolgáltatások a hely rendelkezik-e az RODC-k. Forgalom költségekkel kapcsolatos további információkért lásd: [Azure díjszabása:-a-áttekintő](http://azure.microsoft.com/pricing/).
* Miközben teljes ellenőrzés milyen kiszolgáló virtuális gépek a helyszíni, például a memória, erőforrásokat lemezméret, és így tovább, az Azure-on ki kell választania egy előre konfigurált kiszolgáló méretének megtekintéséhez. A tartományvezérlő adatlemez van szükség az operációsrendszer-lemez mellett a Windows Server Active Directory-adatbázis tárolásához.

## <a name="can-you-deploy-windows-server-ad-fs-on-azure-virtual-machines"></a>Telepítheti a Windows Server AD FS az Azure virtuális gépeken?
Igen, telepítheti a Windows Server AD FS az Azure virtuális gépeken, és a [ajánlott eljárások az AD FS üzembe](https://technet.microsoft.com/library/dn151324.aspx) helyszíni alkalmazhatók az AD FS üzembe helyezése az Azure-on. Azonban néhány ajánlott eljárás végrehajtását, például az terheléselosztás és magas rendelkezésre állású túl Mi az AD FS kínál maga technológiákra szüksége. Az alkalmazás mögötti infrastruktúra kell megadni. Most tekintse át azokat az ajánlott eljárásokat, és tekintse meg, hogyan azok elérhető Azure virtuális gépek és az Azure virtuális hálózat használatával.

1. **Soha nem fedheti fel a biztonsági jogkivonat-szolgáltatás (STS) kiszolgálók közvetlenül kell az internethez.**
   
    Ez azért fontos, mert az STS biztonsági jogkivonatokat bocsát ki. Ennek eredményeképpen STS-kiszolgálók, például az AD FS-kiszolgáló kezelendő az azonos szintű védelme tartományvezérlőként működik. Ha az STS szolgáltatással biztonsága sérül, rosszindulatú felhasználók eltávolíthatnak-potenciálisan a függő entitás alkalmazásai számára, és a megbízó szervezetek STS kiszolgálóival igénye jogcímeket tartalmazó hozzáférési jogkivonatokat kibocsátani.
2. **A tartományvezérlőket az Active Directory, az AD FS-kiszolgáló ugyanazon a hálózaton lévő összes felhasználói tartományban.**
   
    AD FS-kiszolgálók Active Directory tartományi szolgáltatások segítségével hitelesíti a felhasználókat. Javasoljuk, hogy a tartományvezérlőket, az AD FS-kiszolgáló ugyanazon a hálózaton. Ez biztosítja az üzletmenet folytonosságát abban az esetben az Azure-hálózatot és a helyszíni hálózat közötti kapcsolat megszakad, és kisebb késést biztosít és bejelentkezések jobb teljesítményét.
3. **Telepíthet több AD FS-csomópont a magas rendelkezésre állás és a terheléselosztás.**
   
    A legtöbb esetben egy alkalmazás, amely lehetővé teszi, hogy az AD FS hibája nem fogadható el, mert a biztonsági jogkivonatok gyakran igénylő alkalmazások akkreditált képviseletének kritikus. Ennek eredményeképpen és az AD FS most kritikus elérési útját alapvető fontosságú alkalmazások elérése helyezkedik el, az AD FS szolgáltatás használatával több AD FS-proxyk és AD FS-kiszolgáló magas rendelkezésre állású kell lennie. A kérelmek terjesztési eléréséhez terheléselosztók általában telepített mind az AD FS proxy, és az AD FS-kiszolgálók elé.
4. **Telepítse az internet-hozzáférés egy vagy több webalkalmazás-Proxy-csomópontokat.**
   
    Amikor a felhasználók kell az AD FS szolgáltatás által védett alkalmazások, az AD FS szolgáltatás kell lennie az internetről elérhető. Ez a webalkalmazás-Proxy szolgáltatás telepítésével érhető el. Erősen ajánlott magas rendelkezésre állás céljából egynél több csomópont telepítése és a terheléselosztás.
5. **Belső hálózati erőforrásokhoz való hozzáférés korlátozása a webalkalmazás-Proxy csomópontjából.**
   
    A külső felhasználók Active Directory összevonási szolgáltatások hozzáférni az internetről, is telepíteni kell a webalkalmazás-Proxy csomópontok (vagy AD FS Proxy Windows Server korábbi verzióiban). A webalkalmazás-proxy csomópontok közvetlenül az interneten vannak kitéve. Ezek nem szükségesek a tartományhoz csatlakoztatott lehet, és csak hozzáférésre van szükségük az AD FS-kiszolgálók a 443-as és a 80-as TCP-porton. Erősen ajánlott, hogy a más számítógépeken (különösen tartományvezérlőkön) kommunikációja blokkolva van-e.
   
    Ez az általában elért helyszíni DMZ protokollt. Tűzfalak engedélyezett üzemmódot segítségével korlátozzák a forgalmat a Szegélyhálózaton a a helyi hálózati (Ez azt jelenti, hogy csak a megadott IP-címekről érkező és a megadott porton keresztül forgalom engedélyezve van, és az összes többi forgalom le van tiltva).

Az alábbi ábrán látható, a hagyományos a helyszíni AD FS üzembe helyezése.

![A hagyományos a helyszíni Active Directory összevonási szolgáltatások telepítési ábrája](media/active-directory-deploying-ws-ad-guidelines/ADFS_onprem.png)

Azonban nem biztosít natív Azure, mert teljes körű tűzfal funkció, más beállítások kell korlátozzák a forgalmat is. A következő táblázat minden egyes beállítás, és annak előnyeit és hátrányait.

| Beállítás | Előnye | Hátránya |
| --- | --- | --- |
| [Az Azure hálózati hozzáférés-vezérlési listák](../virtual-network/virtual-networks-acl.md) |Kevésbé költséges és egyszerűbb kezdeti konfigurálása |További hálózati hozzáférés-vezérlési lista konfigurációra szükség, ha bármely új virtuális gépek vannak hozzáadva a központi telepítéshez |
| [Barracuda NG tűzfal](https://www.barracuda.com/products/ngfirewall) |Engedélyezési művelet, és azt módhoz nincs hálózati hozzáférés-vezérlési lista konfigurációja |Nagyobb költségek és a kezdeti telepítés |

A magas szintű lépéseket kell telepíteni az AD FS ebben az esetben a következők:

1. Virtuális hálózat létrehozása közötti kapcsolatot nyújthassanak, segítségével a VPN-en vagy [ExpressRoute](http://azure.microsoft.com/services/expressroute/).
2. A tartományvezérlőket a virtuális hálózaton. Ez a lépés nem kötelező, de ajánlott.
3. Telepítse az AD FS-kiszolgálók tartományhoz a virtuális hálózaton.
4. Hozzon létre egy [belső elosztott terhelésű készlet](http://azure.microsoft.com/blog/internal-load-balancing/) , amely tartalmazza az AD FS-kiszolgáló és egy új titkos IP-címet használ a virtuális hálózat (egy dinamikus IP-cím) belül.
   
   1. Frissítse a DNS mutasson a belső elosztott terhelésű készlet (dinamikus) magánhálózati IP-címét a teljes tartománynév létrehozásához.
5. Hozzon létre egy felhőalapú szolgáltatás (vagy egy különálló virtuális hálózatot) a webalkalmazás-Proxy csomópontok.
6. Telepítse a webalkalmazás-Proxy csomópontokat a felhőalapú szolgáltatás, vagy a virtuális hálózat
   
   1. Hozzon létre egy külső elosztott terhelésű készlet, amely a webalkalmazás-Proxy csomópontot tartalmaz.
   2. Frissítse a külső DNS-név (FQDN), mutasson a felhőalapú szolgáltatás nyilvános IP-cím (a virtuális IP-cím).
   3. Konfigurálja az AD FS proxy az AD FS-kiszolgálók a belső elosztott terhelésű készlet megfelelő teljes Tartománynevet használja.
   4. Frissítse a jogcím-alapú webhelyek használandó külső teljes Tartománynevét a jogcím-szolgáltató.
7. Webalkalmazás-Proxy az AD FS virtuális hálózatban lévő gépi közötti hozzáférés korlátozása.

A forgalom korlátozása érdekében a belső terheléselosztóhoz Azure elosztott terhelésű készlet kell megadni az egyetlen forgalomhoz a 80-as és 443-as TCP-portot, és a belső dinamikus IP-címre az elosztott terhelésű készlet összes többi forgalom megszakad.

![Az AD FS hálózati hozzáférés-vezérlési listák 80-as TCP 443-as kiegészítve ábrája engedélyezett.](media/active-directory-deploying-ws-ad-guidelines/ADFS_ACLs.png)

Az AD FS-kiszolgáló felé irányuló forgalom csak az alábbi források által megengedett lenne:

* Az Azure belső terheléselosztót.
* A rendszergazda a helyszíni hálózat IP-címét.

> [!WARNING]
> A Tervező akadályozza a webalkalmazás-Proxy csomópontok bármely más virtuális gépek az Azure virtuális hálózatban vagy a helyi hálózaton lévő összes helyet elérhesse. Amely végezhető el az Express Route-kapcsolatok a helyszíni készülék vagy a VPN-eszközön, pont-pont a VPN-kapcsolatok a tűzfalszabályok konfigurálása.
> 
> 

Ez a lehetőség hátránya a kell konfigurálnia a hálózati hozzáférés-vezérlési listák több eszközön, beleértve a belső terheléselosztó, az AD FS-kiszolgáló és a virtuális hálózathoz hozzáadott beolvasása más kiszolgálók számára. Ha bármely olyan eszközről, hogy korlátozzák a forgalmat hálózati ACL-ek beállítása nélkül hozzáadva a központi telepítéshez, a teljes telepítést is lehetnek kitéve. Ha valaha is megváltozik az IP-címe a webalkalmazás-Proxy csomópontok, alaphelyzetbe kell állítani a hálózati hozzáférés-vezérlési listák (ami azt jelenti, hogy a proxyk kell használatára kell konfigurálni [statikus dinamikus IP-címek](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/)).

![Az AD FS Azure hálózati hozzáférés-vezérlési LISTÁK.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure.png)

Egy másik lehetőség az [Barracuda NG tűzfal](https://www.barracuda.com/products/ngfirewall) készülék forgalmának ellenőrzésére AD FS-proxy kiszolgáló és az AD FS-kiszolgáló között. Ez a beállítás megfelel az ajánlott eljárások és a magas rendelkezésre állású, és kevesebb felügyeleti a kezdeti telepítés után a követelmény, mivel a Barracuda NG tűzfal készülék tűzfal felügyeleti engedélyezett üzemmódot biztosít, és azt is telepíthető közvetlenül egy Azure virtuális hálózatra. Amely szükségtelenné teszi a hálózati hozzáférés-vezérlési listákat az új kiszolgáló hozzáadása a központi telepítéshez bármikor konfigurálhatja. Azonban ez a beállítás a kezdeti telepítés egyszerűbb és olcsóbb hozzáadja.

Ebben az esetben két virtuális hálózatok helyett egy vannak telepítve. Felhívjuk őket VNet1 és VNet2. VNet1 tartalmazza a proxyk és VNet2 tartalmazza a STSs és a hálózati kapcsolat visszaállítása a vállalati hálózathoz. Fizikailag (ámbár gyakorlatilag) VNet1 van ezért VNet2 és, viszont a vállalati hálózattól elkülönített. VNet1 kapcsolódik VNet2 technológiával különleges bújtatás, átviteli független hálózati architektúra (TINA) néven ismert. Csatolt Barracuda NG tűzfalat használ a virtuális hálózatokat egyenként a TINA alagút – egy Barracuda minden virtuális hálózathoz.  A magas rendelkezésre állású javasoljuk, hogy minden virtuális hálózaton; két kígyókról központi telepítése egy aktív, a más passzív. Rendkívül hatékony firewalling képességeket, amelyek lehetővé teszik a számunkra, hogy az Azure-ban a hagyományos helyszínen DMZ működésének utánozzák kínálnak.

![Az AD FS tűzfal Azure-on.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure_firewall.png)

További információkért lásd: [AD FS: kiterjesztése a helyszíni jogcímbarát előtér-alkalmazás és az Internet](#BKMK_CloudOnlyFed).

### <a name="an-alternative-to-ad-fs-deployment-if-the-goal-is-office-365-sso-alone"></a>Ha a cél az Office 365 egyszeri bejelentkezés önmagában, az AD FS telepítési helyett
Van egy másik alternatíva olyan központi telepítése az AD FS elemet, ha a cél csak bejelentkezés az Office 365 engedélyezése. Ebben az esetben egyszerűen telepítheti a DirSync a jelszó szinkronizálása a helyszíni és az azonos end-eredmények elérése a minimális központi telepítés bonyolultságát, mivel ez a módszer nem szükséges az AD FS- vagy Azure.

Az alábbi táblázat összehasonlítja a bejelentkezési folyamat működése és anélkül, hogy az AD FS telepítése.

| Az Office 365 egyszeri bejelentkezés az AD FS és a DirSync használatával | Office 365 azonos bejelentkezés DirSync + jelszó-szinkronizálás használatával |
| --- | --- |
| 1. A felhasználó bejelentkezik a vállalati hálózathoz, és a Windows Server Active Directory hitelesített. |1. A felhasználó bejelentkezik a vállalati hálózathoz, és a Windows Server Active Directory hitelesített. |
| 2. A felhasználó megpróbál hozzáférni az Office 365 (vagyok @contoso.com). |2. A felhasználó megpróbál hozzáférni az Office 365 (vagyok @contoso.com). |
| 3. Az Office 365 átirányítja a felhasználót az Azure ad Szolgáltatásba. |3. Az Office 365 átirányítja a felhasználót az Azure ad Szolgáltatásba. |
| 4. Mivel az Azure AD nem tudja hitelesíteni a felhasználót, és megértette, hogy van-e a helyszíni Active Directory összevonási szolgáltatások megbízhatóság, AD FS átirányítja a felhasználót. |4. Az Azure AD közvetlenül a Kerberos jegyek nem fogad, és nincs megbízhatósági kapcsolat létezik, így mindent lekér, hogy a felhasználó adja meg a hitelesítő adatokat. |
| 5. A felhasználó Kerberos-jegy küld az AD FS STS. |5. A felhasználó megadja a helyszínen ugyanazt a jelszót, és az Azure AD érvényesíti azokat a felhasználónévvel és jelszóval DirSync által szinkronizált. |
| 6. Az AD FS átalakítja a szükséges jogkivonat formátuma vagy jogcímeket a Kerberos jegy, és átirányítja a felhasználót az Azure ad Szolgáltatásba. |6. Az Azure AD a felhasználónak az Office 365 szolgáltatásra irányítja át. |
| 7. Az Azure AD hitelesíti a felhasználót (egy másik átalakítása következik be). |7. A felhasználó Office 365 és az Azure AD-tokent OWA bejelentkezhet. |
| 8. Az Azure AD a felhasználónak az Office 365 szolgáltatásra irányítja át. | |
| 9. A felhasználói beavatkozás nélkül bejelentkezett az Office 365 szolgáltatásra. | |

A DirSync és jelszó-szinkronizálási forgatókönyv (nem az AD FS) az Office 365 az egyszeri bejelentkezés helyébe "azonos bejelentkezés" hol "azonos" egyszerűen azt jelenti, hogy felhasználók újra meg kell adnia a helyszíni hitelesítő adatokkal való hozzáférés az Office 365 során. Vegye figyelembe, hogy ezeket az adatokat is megjegyezze a felhasználó böngészőben csökkentése érdekében a további utasításokat.

### <a name="additional-food-for-thought"></a>További étele for gondolat
* Ha az AD FS proxy Azure virtuális géphez, és az AD FS-kiszolgáló szükséges. Ha a helyszíni, javasoljuk, hogy használja-e a pont-pont VPN-kapcsolatot a virtuális hálózat engedélyezi a webalkalmazás-Proxy csomópont kommunikáljon az AD FS-kiszolgáló által biztosított.
* Ha telepít egy AD FS-kiszolgáló egy Azure virtuális gépen, a Windows Server Active Directory-tartományvezérlők, Attribútumtárakkal és konfigurációs adatbázis szükséges, és az expressroute-on vagy a pont-pont VPN-kapcsolat az Azure virtuális hálózat és a helyszíni hálózat között is szükség lehet.
* Díjak érvényesek az összes forgalom az Azure virtuális gépeken (kimenő forgalom). Ha költség a vezetői tényező, célszerű telepíteni a webalkalmazás-Proxy csomópontok Azure, az AD FS kiszolgálók helyszíni hagyja. Ha az AD FS-kiszolgálók az Azure virtuális gépeken is van telepítve, további költségek a helyszíni felhasználók hitelesítésére fog fel. Kimenő forgalom költséget egy áll-e az áthaladó meg az expressroute-on vagy a VPN webhelyek közötti kapcsolattal függetlenül.
* Ha Azure natív-kiszolgáló terheléselosztási képességet biztosít a magas rendelkezésre állás, az AD FS-kiszolgálók használata mellett dönt, vegye figyelembe, hogy a felhőszolgáltatás belüli virtuális gépek állapotának meghatározására használt mintavételt terheléselosztás biztosít. Azure virtuális gépeken (szemben a webes vagy feldolgozói szerepkörök), ha egy egyéni mintavételt kell használható, mert, amely az alapértelmezett mintavételt válaszol az ügynök nincs jelen az Azure virtuális gépeken. Az egyszerűség kedvéért egy egyéni TCP-Hálózatfigyelővel használhatja – ehhez csak a TCP-kapcsolat (TCP SZIN szegmens küldött és a TCP SZIN ACK-szegmens válaszolt) sikeresen létrejött a virtuális gép állapotának meghatározására. Az egyéni vizsgálat, amelyhez a virtuális gépek aktívan figyelő bármely TCP-port használatára konfigurálhatja.

> [!NOTE]
> Kell tenni ugyanazokat a portokat közvetlenül az interneten (például a 80-as és 443-as port) a gépeket nem lehet megosztani az ugyanazon a felhőalapú szolgáltatás. Ezért javasoljuk, hogy hozzon létre egy dedikált felhőalapú szolgáltatás lehetséges elkerülése érdekében a Windows Server AD FS-kiszolgálók átfedésben van egy alkalmazást a portokra vonatkozó követelmények és a Windows Server AD FS között.
> 
> 

## <a name="deployment-scenarios"></a>Üzembe helyezési forgatókönyvek
Az alábbi szakasz ismerteti a alkotómunkájának telepítési forgatókönyvek figyelembe kell venni szempontokat emelni. Minden egyes forgatókönyv rendelkezik a döntést és tényezőket kell figyelembe venni kapcsolatos további részleteket mutató hivatkozásokat tartalmaz.

1. [Active Directory tartományi szolgáltatások: Az AD DS-t támogató alkalmazás nem követelmény a vállalati hálózati kapcsolatra központi telepítését.](#BKMK_CloudOnly)
   
    Például egy internetes elérésű SharePoint-szolgáltatás telepítve van egy Azure virtuális gépen. Nincs függőségben van a vállalati hálózati erőforrásokat. Az alkalmazáshoz szükséges a Windows Server Active Directory tartományi szolgáltatások, de nem igényel a vállalati Windows Server Active Directory tartományi Szolgáltatásokban.
2. [Az AD FS: Kiterjesztése a helyszíni jogcímbarát előtér-alkalmazás és az Internet](#BKMK_CloudOnlyFed)
   
    Például egy megjelölt jogcímbarát alkalmazáshoz, amelyeket már sikeresen telepítve a helyi vállalati felhasználók által használt kell lesz elérhető az internetről. Az alkalmazást kell közvetlenül az interneten keresztül érhető el, a saját vállalati identitásokat üzleti partnerek és a meglévő vállalati felhasználók.
3. [Active Directory tartományi szolgáltatások: Használatához a vállalati hálózathoz való hozzáférés szükséges egy Windows Server AD DS-t támogató alkalmazás központi telepítése](#BKMK_HybridExt)
   
    Például egy LDAP-kompatibilis alkalmazás, amely támogatja a Windows-hitelesítést, és a Windows Server Active Directory tartományi szolgáltatások használja, mint a tárház konfigurációs és a felhasználói profil adatainak telepítve van egy Azure virtuális gépen. Az alkalmazás kihasználja a meglévő vállalati Windows Server Active Directory tartományi Szolgáltatásokban, és adja meg az egyszeri bejelentkezés kívánatos. Az alkalmazás nincs jogcímbarát.

### <a name="BKMK_CloudOnly"></a>1. Active Directory tartományi szolgáltatások: Az AD DS-t támogató alkalmazás nem követelmény a vállalati hálózati kapcsolatra központi telepítését.
![Csak felhőalapú Active Directory tartományi szolgáltatások telepítési](media/active-directory-deploying-ws-ad-guidelines/ADDS_cloud.png)
**1. ábra**

#### <a name="description"></a>Leírás
A SharePoint Azure virtuális gépként van telepítve, és nincs függőségben van a vállalati hálózati erőforrásokat. Az alkalmazás Windows Server Active Directory tartományi szolgáltatások nem szükséges *nem* igényelnek a vállalati Windows Server Active Directory tartományi Szolgáltatásokban. Nem Kerberos vagy összevont Megbízhatóságok szükség, mivel a felhasználók az alkalmazást a Windows Server AD DS-tartomány is az Azure virtuális gépeken a felhőben üzemeltetett keresztül önálló kiosztott.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>A forgatókönyv szempontjait és hogyan technológiai területekre vonatkozik-e a forgatókönyv
* [Hálózati topológia](#BKMK_NetworkTopology): hozzon létre egy Azure virtuális hálózatra létesítmények közötti kapcsolathoz (más néven hely-hely kapcsolatot) nélkül.
* [Tartományvezérlő telepítési konfiguráció](#BKMK_DeploymentConfig): üzembe helyezhet egy új tartományvezérlőt egy új, egyetlen tartományból, Windows Server Active Directory-erdőben. Ez a Windows DNS-kiszolgáló együtt kell telepíteni.
* [Windows Server Active Directory-helyek topológiájával](#BKMK_ADSiteTopology): használja (minden számítógép lesz alapértelmezett-First-Site-Name) alapértelmezett Windows Server Active Directory-webhelyen.
* [IP-címzést és a DNS-](#BKMK_IPAddressDNS):
  
  * Egy statikus IP-cím beállítása a tartományvezérlő a Set-AzureStaticVNetIP Azure PowerShell-parancsmag használatával.
  * Telepítse és konfigurálja a Windows Server DNS az Azure x tartományvezérlőn zajlik.
  * Konfigurálja a virtuális hálózati tulajdonságok nevét és IP-címet a virtuális gép, amelyen a tartományvezérlő és DNS-kiszolgálói szerepeknek.
* [Globális katalógus](#BKMK_GC): az erdőben az első tartományvezérlő globáliskatalógus-kiszolgálónak kell lennie. További tartományvezérlők is konfigurálnia kell juthatnak ebbe a generációba, mert egy egyetlen tartományból álló a globális katalógus nem igényel bármit tennie kellene a tartományvezérlőt.
* [A Windows Server AD DS-adatbázis és a SYSVOL helyét](#BKMK_PlaceDB): adatlemezt hozzá ahhoz, hogy a Windows Server Active Directory adatbázis, a naplók és a SYSVOL tároló Azure virtuális gépeket futtató tartományvezérlők.
* [Biztonsági mentése és visszaállítása](#BKMK_BUR): határozza meg, hol szeretné tárolni a rendszerállapot biztonsági mentéseit. Ha szükséges, egy másik adatlemez hozzáadása a DC-VM biztonsági másolatok tárolására.

### <a name="BKMK_CloudOnlyFed"></a>2 AD FS: kiterjesztése a helyszíni jogcímbarát előtér-webalkalmazást az internethez
![Összevonás az közötti kapcsolatot nyújthassanak](media/active-directory-deploying-ws-ad-guidelines/Federation_xprem.png)
**2. ábra**

#### <a name="description"></a>Leírás
A megjelölt jogcímbarát alkalmazáshoz, amelyeket már sikeresen telepítve a helyi vállalati felhasználók által használt kell közvetlenül az internetről elérhető lesz. Az alkalmazás SQL-adatbázishoz pedig tárolja az adatokat egy webes előtér funkcionál. Az alkalmazás által használt SQL-kiszolgálók a vállalati hálózat is található. Két Windows Server AD FS STSs és a terheléselosztó voltak telepített helyi hozzáférést biztosítani a vállalati felhasználók. Emellett közvetlenül az interneten keresztül érhető el a saját vállalati identitásokat üzleti partnerek és a meglévő vállalati felhasználók kell most az alkalmazást.

Annak érdekében, hogy egyszerűbbé és üzembe helyezési és konfigurálási kielégítse az új követelményről, az a döntés született, hogy két további webalkalmazás-frontends és két Windows Server AD FS proxykiszolgálókra telepíthető Azure virtuális gépeken. Minden négy virtuális gépek közvetlenül az internethez számára megjelenik, és biztosítja az Azure Virtual Network pont-pont VPN funkcióval a helyszíni hálózathoz csatlakoznak.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>A forgatókönyv szempontjait és hogyan technológiai területekre vonatkozik-e a forgatókönyv
* [Hálózati topológia](#BKMK_NetworkTopology): hozzon létre egy Azure virtuális hálózatra és [közötti kapcsolatot nyújthassanak konfigurálása](../vpn-gateway/vpn-gateway-site-to-site-create.md).
  
  > [!NOTE]
  > Minden egyes a Windows Server AD FS-tanúsítványok győződjön meg arról, hogy a tanúsítványsablon és a létrehozott tanúsítványok meghatározott URL-CÍMÉT az Azure-on futó Windows Server AD FS-példányok által elérhető. Elképzelhető, hogy szükség közötti kapcsolatot nyújthassanak a nyilvános kulcsokra ÉPÜLŐ infrastruktúra részeit. A példa, ha a CRL-végpontot LDAP-alapú, és kizárólag helyben tárolt, majd létesítmények közötti kapcsolat lesz szükség. Ha ez nem kívánatos, akkor lehet szükség, amelynek a CRL nem érhető el az interneten keresztül egy hitelesítésszolgáltató által kiállított tanúsítványok.
  > 
  > 
* [Felhő konfigurálása](#BKMK_CloudSvcConfig): Ellenőrizze, hogy a két felhőszolgáltatások sorrendben meg két terhelésű virtuális IP-címeket. Az első felhőalapú szolgáltatás virtuális IP-címet a rendszer kéri a két Windows Server AD FS proxy virtuális gépeket a 80-as és 443-as portot. A Windows Server AD FS proxy virtuális gépek lesz konfigurálva, hogy a Windows Server AD FS STSs előlapot a helyszíni terheléselosztó IP-címére mutasson. A rendszer kéri a második felhőszolgáltatás virtuális IP-címet a két virtuális gépeket a webes előtér újra futtatni a 80-as és 443-as portot. Annak érdekében, hogy a terheléselosztó csak arra utasítja a működő Windows Server AD FS proxy- és webes előtér virtuális gépek felé irányuló forgalom egyéni mintavétel konfigurálása.
* [Összevonási kiszolgáló konfigurációja](#BKMK_FedSrvConfig): Windows Server konfigurálása az AD FS összevonási kiszolgálóként (STS) a rendszer a Windows Server Active Directory erdő esetén, a felhőben létrehozott biztonsági jogkivonatokat hoz létre. Összevonási jogcímeket szolgáltató megbízhatósági kapcsolata a másik partnerek fogadhatja el identitásait kíván beállítani, majd konfigurálja függő entitás megbízhatósági kapcsolatokat a különböző alkalmazások esetén jöjjön létre-jogkivonat.
  
    A legtöbb esetben Windows Server AD FS proxykiszolgálókra telepített biztonsági okokból az Internet felé néző minőségben közben, mint a Windows Server AD FS összevonási elkülönül közvetlen internetkapcsolattal. Az üzembe helyezési forgatókönyvnek függetlenül konfigurálnia kell az a felhőalapú szolgáltatás, amely egy nyilvánosan elérhetővé IP-címet és portot, amelyet nem tud terheléselosztásához két Windows Server AD FS STS-példányok vagy a proxy példányok virtuális IP-címmel.
* [Windows Server AD FS magas rendelkezésre állású konfiguráció](#BKMK_ADFSHighAvail): ajánlott telepíteni a Windows Server AD FS-farm legalább két kiszolgáló feladatátvételi és a terheléselosztás. Például, érdemes megfontolni a Windows Server AD FS konfiguráció adatait a belső Windows adatbázist (WID) használja, és az Azure belső terheléselosztási képességet bejövő kérelmek szét a kiszolgálók, a farmban.

További információkért lásd: a [Active Directory tartományi szolgáltatások telepítési útmutatója](https://technet.microsoft.com/library/cc753963).

### <a name="BKMK_HybridExt"></a>3. Active Directory tartományi szolgáltatások: Használatához a vállalati hálózathoz való hozzáférés szükséges egy Windows Server AD DS-t támogató alkalmazás központi telepítése
![Active Directory tartományi szolgáltatások telepítési létesítmények közötti](media/active-directory-deploying-ws-ad-guidelines/ADDS_xprem.png)
**3. ábra**

#### <a name="description"></a>Leírás
Egy LDAP-kompatibilis alkalmazás lett telepítve egy Azure virtuális gépen. Windows-hitelesítést támogatja, és használja a tárház Windows Server Active Directory tartományi szolgáltatások konfigurációs és a felhasználói profil adatok. Az alkalmazás kihasználja a meglévő vállalati Windows Server Active Directory tartományi Szolgáltatásokban, és adja meg az egyszeri bejelentkezés célja. Az alkalmazás nincs jogcímbarát. Felhasználók is kell közvetlenül az internetről az alkalmazás eléréséhez. Optimalizálható a teljesítmény és a költség, a döntés született, hogy a két további tartományvezérlők a vállalati tartomány részét képezik telepíthető-e az alkalmazás Azure-ral.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>A forgatókönyv szempontjait és hogyan technológiai területekre vonatkozik-e a forgatókönyv
* [Hálózati topológia](#BKMK_NetworkTopology): az Azure virtuális hálózat létrehozása [létesítmények közötti kapcsolat](../vpn-gateway/vpn-gateway-site-to-site-create.md).
* [Telepítési módszer](#BKMK_InstallMethod): a vállalati Windows Server Active Directory-tartományból replika tartományvezérlők telepítése. A replika tartományvezérlő a Windows Server Active Directory tartományi szolgáltatások telepítése a virtuális gépen, és opcionálisan a szolgáltatással telepítse az adathordozóról (IFM) replikálható az új tartományvezérlő telepítése során kell adatmennyiség csökkentése érdekében. Az oktatóanyagok esetén lásd: [Active Directory replika tartományvezérlő telepítése Azure](active-directory-install-replica-active-directory-domain-controller.md). Ha adathordozóról történő telepítés is használja, a virtuális tartományvezérlő helyszíni építsenek, és helyezze át a teljes virtuális merevlemez (VHD) ahelyett, hogy a Windows Server AD DS telepítése során a felhőbe hatékonyabb lehet. A biztonsági javasoljuk, hogy törölje a virtuális Merevlemezt a helyi hálózatról után az Azure-bA másolásra került.
* [Windows Server Active Directory-helyek topológiájával](#BKMK_ADSiteTopology): hozzon létre egy új Azure helyet az Active Directory – helyek és szolgáltatások. Hozzon létre egy Windows Server Active Directory alhálózati objektumot az Azure virtuális hálózat és az alhálózat hozzáadása a webhelyhez. Hozzon létre egy új helykapcsolatot, amely tartalmazza az új Azure hely és a helyet, ahol az Azure virtuális hálózat VPN-végpont található ellenőrzése és a és az Azure-ból Windows Server Active Directory-forgalom optimalizálása érdekében.
* [IP-címzést és a DNS-](#BKMK_IPAddressDNS):
  
  * Egy statikus IP-cím beállítása a tartományvezérlő a Set-AzureStaticVNetIP Azure PowerShell-parancsmag használatával.
  * Telepítse és konfigurálja a Windows Server DNS az Azure x tartományvezérlőn zajlik.
  * Konfigurálja a virtuális hálózati tulajdonságok nevét és IP-címet a virtuális gép, amelyen a tartományvezérlő és DNS-kiszolgálói szerepeknek.
* [Földrajzilag elosztott tartományvezérlők](#BKMK_DistributedDCs): igény szerint konfigurálhatja a további virtuális hálózatokat. Ha az Active Directory-helyek topológiáját, hogy különböző Azure-régiók, mint a létrehozandó ennek megfelelően az Active Directory-helyek földrajzi rendszerű tartományvezérlő szükséges.
* [Írásvédett tartományvezérlők](#BKMK_RODC): az Azure site az írásvédett tartományvezérlő központi telepítését, attól függően, a követelmények végrehajtásához írási műveleteket a tartományvezérlő és az alkalmazások és szolgáltatások kompatibilitási az RODC-k a helyen. Alkalmazáskompatibilitás kapcsolatos további információkért tekintse meg a [írásvédett tartományvezérlők kompatibilitási útmutatója](https://technet.microsoft.com/library/cc755190).
* [Globális katalógus](#BKMK_GC): globális katalógusok bejelentkezési szolgáltatáskérések többtartományos erdővel van szükség. Nem kell telepítenie az Azure hely a globális Katalógus, ha, a kimenő forgalom költségek a hitelesítési kérelmek oka lekérdezések juthatnak ebbe a generációba más helyek tájékozódnia. A forgalom minimalizálása érdekében engedélyezheti a univerzális csoporttagság gyorsítótárazását az Active Directory – helyek és szolgáltatások Azure hely.
  
    Ha a globális Katalógus, konfigurálja helykapcsolatok és a hely hivatkozások költségek úgy, hogy a GC-nek az Azure hely nem előnyben részesített szerint más globális katalógusok, amely kell replikálni a azonos részleges tartománypartíciók, mint a forrás-tartományvezérlő.
* [A Windows Server AD DS-adatbázis és a SYSVOL helyét](#BKMK_PlaceDB): adatlemez hozzáadása a Windows Server Active Directory adatbázis, a naplók és a SYSVOL tárolásához Azure virtuális gépeken futó tartományvezérlők.
* [Biztonsági mentése és visszaállítása](#BKMK_BUR): határozza meg, hol szeretné tárolni a rendszerállapot biztonsági mentéseit. Ha szükséges, egy másik adatlemez hozzáadása a DC-VM biztonsági másolatok tárolására.

## <a name="deployment-decisions-and-factors"></a>Telepítési döntések és tényezők
Ez a táblázat a Windows Server Active Directory identitástechnológiai területein a fenti forgatókönyvek és a megfelelő döntéseket kell figyelembe venni, a szükséges további adatokat az alábbi hivatkozások a befolyásolt foglalja össze. Néhány technológiai terület nem feltétlenül alkalmazhatók minden üzembe helyezési forgatókönyv, és előfordulhat, hogy néhány identitástechnológiai területein lényegesebb egy központi telepítési forgatókönyvet, mint más identitástechnológiai területein.

Például ha telepít egy replika tartományvezérlő virtuális hálózaton, és az erdő egyetlen tartományt tartalmaz, majd válassza a globáliskatalógus-kiszolgáló központi telepítése ebben az esetben csak akkor fontos a környezet-forgatókönyv, mert nem hoz létre további replikációs szemben támasztott követelményeket. Másrészről Ha az erdő több tartomány van, majd alkalmazással való központi telepítése a globális katalógus, a virtuális hálózaton befolyásolhatja a rendelkezésre álló sávszélességet, teljesítmény, hitelesítés, directory-keresések és így tovább.

| Windows Server Active Directory technológiai terület | Döntések | Tényezők |
| --- | --- | --- |
| [Hálózati topológia](#BKMK_NetworkTopology) |Virtuális hálózat létrehozása? |<li>Vállalati erőforrások eléréséhez követelmények</li> <li>Hitelesítés</li> <li>Fiókkezelés</li> |
| [Tartományvezérlő telepítési konfiguráció](#BKMK_DeploymentConfig) |<li>Egy másik erdőben, a bizalmi kapcsolatok nélkül telepítéséhez?</li> <li>Az összevonási új erdő telepítéséhez?</li> <li>Windows Server Active Directory erdőszintű megbízhatósággal új erdőt vagy Kerberos telepítése?</li> <li>Corp erdő kiterjesztése a replika tartományvezérlő üzembe helyezésével?</li> <li>Kiterjeszti a Corp erdőben telepít egy új gyermektartomány vagy tartományfa?</li> |<li>Biztonság</li> <li>Megfelelőség</li> <li>Költségek</li> <li>Rugalmasság és a hibatűrés</li> <li>Alkalmazáskompatibilitás</li> |
| [Windows Server Active Directory-helyek topológiájával](#BKMK_ADSiteTopology) |Tegye konfigurálásától webhelyek, helykapcsolatok és alhálózatok az Azure virtuális hálózati forgalom optimalizálása, illetve a költségek csökkentése érdekében? |<li>Alhálózat és a webhely-definíciók</li> <li>A hely tulajdonságai és -módosítási értesítés</li> <li>Replikációs tömörítés</li> |
| [IP-címzést és a DNS-](#BKMK_IPAddressDNS) |Hogyan lehet IP-címek és a névfeloldás? |<li>Hozzárendelhet egy statikus IP-címet a használja a Set-AzureStaticVNetIP parancsmag segítségével</li> <li>Windows Server DNS-kiszolgáló telepítése és konfigurálása a virtuális hálózati tulajdonságok nevét és IP-címet a virtuális gép, amelyen a tartományvezérlő és DNS-kiszolgálói szerepkörök</li> |
| [Földrajzilag elosztott tartományvezérlők](#BKMK_DistributedDCs) |Hogyan lehet külön virtuális hálózatokon lévő tartományvezérlőkre replikálni? |Ha az Active Directory-helyek topológiáját, amely megfelel-e különböző Azure-régiók, mint a létrehozandó ennek megfelelően az Active Directory-helyek földrajzi rendszerű tartományvezérlő szükséges. [Virtuális hálózati kapcsolat a virtuális hálózat konfigurálása](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) replikálása a tartományvezérlők külön virtuális hálózatokon. |
| [Írásvédett tartományvezérlők](#BKMK_RODC) |Írásvédett vagy írható tartományvezérlők használni? |<li>A HBI vagy PII attribútumok szűrése</li> <li>Szűrő titkos kulcsok</li> <li>Kimenő forgalom korlátot</li> |
| [Globális katalógus](#BKMK_GC) |Globális katalógus telepíteni? |<li>Egyetlen tartományból erdő ellenőrizze az összes tartományvezérlőjén juthatnak ebbe a generációba</li> <li>Többtartományos erdő juthatnak ebbe a generációba szükségesek a hitelesítéshez</li> |
| [Telepítési módszer](#BKMK_InstallMethod) |Hogyan kell telepíteni a Tartományvezérlőt az Azure-ban? |Vagy: <li>A Windows PowerShell vagy a Dcpromo Active Directory tartományi szolgáltatások telepítése</li> <li>Helyezze át egy helyi virtuális tartományvezérlő virtuális Merevlemezt</li> |
| [A Windows Server AD DS-adatbázis és a SYSVOL helyét](#BKMK_PlaceDB) |Windows Server AD DS adatbázis, naplófájlok és SYSVOL tárolási helyét? |Módosítsa a Dcpromo.exe alapértékeket. A kritikus Active Directory-fájlok *kell* Azure adatlemezek elhelyezni, amelyek megvalósítják az írási gyorsítótárazást operációsrendszer-lemezek helyett. |
| [Biztonsági mentés és visszaállítás](#BKMK_BUR) |Hogyan és adatok helyreállításához? |-A rendszerállapot biztonsági mentésének létrehozása |
| [Összevonási kiszolgáló konfigurációja](#BKMK_FedSrvConfig) |<li>A felhőben összevonással új erdő telepítéséhez?</li> <li>Helyszíni AD FS telepítéséhez, és tegye elérhetővé a proxy a felhőben?</li> |<li>Biztonság</li> <li>Megfelelőség</li> <li>Költségek</li> <li>Üzleti partnerek alkalmazások eléréséhez</li> |
| [Felhő konfigurálása](#BKMK_CloudSvcConfig) |Egy felhőalapú szolgáltatás implicit módon van telepítve az első alkalommal hoz létre egy virtuális gépet. Szüksége további felhőalapú szolgáltatások telepítését? |<li>Egy virtuális Gépet vagy virtuális gépek szüksége van az Internet közvetlen kitéve?</li> <li> A szolgáltatás igényel a terheléselosztás?</li> |
| [Összevonási kiszolgáló követelményei a nyilvános és magánhálózati IP-címzési (virtuális IP-cím és a dinamikus IP)](#BKMK_FedReqVIPDIP) |<li>A Windows Server AD FS-példányt kell nem érhető el közvetlenül az internetről?</li> <li>Igényelnek-e az alkalmazás üzembe helyezéséhez a felhőben saját Internet felé néző IP-cím és port?</li> |Minden virtuális IP-címet a telepítés során szükséges egy felhőalapú szolgáltatás létrehozása |
| [Windows Server AD FS magas rendelkezésre állás konfigurálása](#BKMK_ADFSHighAvail) |<li>A Windows Server AD FS kiszolgálófarm hány csomópontok?</li> <li>Hány csomópontok központi telepítése a Windows Server AD FS proxy farm?</li> |Rugalmasság és a hibatűrés |

### <a name="BKMK_NetworkTopology"></a>Hálózati topológia
Az IP-címek konzisztenciáját, és a Windows Server Active Directory tartományi szolgáltatások DNS-követelmények kielégítéséhez fontos először létre kell hoznia egy [Azure-beli virtuális hálózat](../virtual-network/virtual-networks-overview.md) és a virtuális gépeket csatlakoztatni. A létrehozás során el kell döntenie, hogy kiterjeszti a helyszíni vállalati hálózatot, amely az Azure virtuális gépek transzparens módon csatlakozik a helyszíni gépeket-e – ez hagyományos VPN technológiák segítségével érhető el, és megköveteli, hogy a VPN-végpontnak a vállalati hálózat széle elérhetővé tehető. Ez azt jelenti, hogy a VPN megkezdődik az Azure-ból a vállalati hálózathoz, nem fordítva.

Vegye figyelembe, hogy további többletköltségeket kiterjesztése a helyszíni hálózat felett a normál díjakat, amelyek érvényesek az egyes virtuális virtuális hálózaton. Pontosabban nincsenek díjak CPU-idő az Azure virtuális hálózati átjáró és az egyes virtuális gépek, amelyek a VPN-Kapcsolaton keresztül kommunikál a helyszíni gépeket által generált kimenő forgalom. További információ a hálózati forgalom díjak: [Azure díjszabása:-a-áttekintő](http://azure.microsoft.com/pricing/).

### <a name="BKMK_DeploymentConfig"></a>Tartományvezérlő telepítési konfiguráció
A tartományvezérlő konfigurálását az Azure-on futtatja a szolgáltatás követelményeitől függ. Például telepíthet egy új erdő, nem a saját vállalati erdő, egy-a-koncepció igazolása, egy új alkalmazást vagy valamilyen más rövid távú igénylő projektek címtárszolgáltatások, de nem kizárólag belső vállalati erőforrások elérését.

Juttatás egy elkülönített erdő tartományvezérlő nem replikálja a vállalati tartományvezérlők, kevesebb kimenő hálózati forgalom magát, a rendszer által előállított eredményezve közvetlenül a költségek csökkentése. További információ a hálózati forgalom díjak: [Azure díjszabása:-a-áttekintő](http://azure.microsoft.com/pricing/).

Másik példaként tegyük fel, hogy egy szolgáltatás adatvédelmi követelményeinek rendelkezik, de a szolgáltatás függ a belső Windows Server Active Directory elérésére. Ha a szolgáltatás a felhőben engedélyezettek adatok tárolására, telepíthet új gyermektartomány a belső erdőnek Azure. Ebben az esetben a tartományvezérlő (nélkül cím adatvédelmi aggályokat segítse a globális katalógussal) az új gyermektartomány telepítheti. Ebben a forgatókönyvben egy replika tartományvezérlő telepítése egy virtuális hálózati kapcsolatot a helyszíni tartományvezérlők igényel.

Ha új erdőt hoz létre, válassza ki, használjon-e [Active Directory bizalmi kapcsolatok](https://technet.microsoft.com/library/cc771397) vagy [összevonási megbízhatósági kapcsolatok](https://technet.microsoft.com/library/dd807036). A kompatibilitási, biztonsági, megfelelőségi, költség és rugalmasság meghatározni egyensúlyba. Ahhoz például, hogy előnyeit [szelektív hitelesítés](https://technet.microsoft.com/library/cc755844) választása Azure új erdő telepítéséhez, és hozzon létre egy Windows Server Active Directory megbízható a helyi erdő és a felhő erdő között. Ha az alkalmazás jogcímbarát, azonban telepíthet összevonási megbízhatósági kapcsolatok Active Directory-erdők helyett. Egy másik tényező további adatok replikálása a helyszíni Windows Server Active Directory a felhőbe történő kiterjesztésével vagy a hitelesítés és a lekérdezés eredményeként több kimenő adatforgalmat generálnak költség lesz.

Rendelkezésre állás és a hibatűrés követelményei is befolyásolják a választott. Például ha a kapcsolat megszakad, alkalmazások, ami a Kerberos megbízhatósági kapcsolat vagy egy összevonási megbízhatósági összes valószínűleg teljesen bontottuk kivéve, ha elegendő Azure-infrastruktúra telepítése. Például a replika tartományvezérlők alternatív telepítési konfigurációk (írható vagy RODC-k) érdekében, hogy az képes elviselni hivatkozás kimaradások esetén.

### <a name="BKMK_ADSiteTopology"></a>Windows Server Active Directory-helyek topológiájával
Helyesen adja meg a helyek és a forgalom optimalizálása és a költségek minimalizálása érdekében helykapcsolatok kell. Helyek, helykapcsolatok és alhálózatok hatással a hitelesítési forgalmat és a tartományvezérlők közötti replikációs topológia. Vegye figyelembe a következő forgalmat díjakat és központi telepítése, és konfigurálja a tartományvezérlők a környezet követelményeinek megfelelően:

* Egy névleges díj óránként magához az átjáróhoz van:
  
  * Is lehet elindul vagy leáll, ahogyan szeretné
  * Ha leállt, Azure virtuális gépek el különítve a vállalati hálózat
* Bejövő forgalom felszabadul.
* Kimenő forgalom fel van töltve, a következők szerint [Azure díjszabása:-a-áttekintő](http://azure.microsoft.com/pricing/). Optimalizálhatja a hely tulajdonságai között a helyszíni és a felhő helyeken az alábbiak szerint:
  
  * Ha több virtuális hálózatot használ, a webhely-hivatkozások és azok költségeinek megfelelő konfigurálása megakadályozhatja, hogy a Windows Server Active Directory tartományi szolgáltatások az Azure site rangsorolása egyik, amely a azonos szintű díjmentesen szolgáltatást nyújt a. Akkor is érdemes letiltása az összes hivatkozás (BASL) beállítás (amely alapértelmezés szerint engedélyezve van) hely híd. Ez biztosítja, hogy csak közvetlenül csatlakoztatott helyek egymással replikálni. Tranzitív csatlakoztatott helyek tartományvezérlők nem tudnak közvetlenül egymással replikálni, de egy közös webhelyet vagy webhelyeket keresztül kell replikálni. A köztes helyek valamilyen okból elérhetetlenné válik, ha azok a tartományvezérlők tranzitív csatlakoztatott helyek közötti replikáció nem történik meg, akkor is, ha a helyek közötti kapcsolat. Végezetül a tranzitív replikáció működését szakaszok továbbra is hasznos, ha webhely létrehozása helykapcsolati hidak, amelyek tartalmazzák a megfelelő helykapcsolatok és a helyen, például a helyszíni vállalati hálózati helyek.
  * [Konfigurálja a helykapcsolatköltségek](https://technet.microsoft.com/library/cc794882) megfelelően nem kívánt forgalom elkerülése érdekében. Például ha **következő legközelebbi helyen próbálja** beállítás engedélyezve van, gondoskodjon arról, hogy a virtuális hálózati telephely nem a következő legközelebb eső az Azure site vissza a vállalati hálózathoz csatlakoztató helykapcsolati objektum költsége növelésével.
  * Konfigurálja a helykapcsolatot [intervallumok](https://technet.microsoft.com/library/cc794878) és [ütemezések](https://technet.microsoft.com/library/cc816906) konzisztencia követelmények és az objektum módosításai arány alapján. Az ütemezésben igazodni késés tolerancia. Azok a tartományvezérlők értéket, csak az utolsó állapotának replikálja, így csökken a replikációs időköztől is költségek csökkentése esetén elegendő objektum adatváltozási sebessége.
* Ha a költségek minimalizálása prioritást, ellenőrizze a replikációs ütemezve van, és értesítést nincs engedélyezve. Ez akkor az alapértelmezett konfiguráció, ha a helyek közti replikálásához. Ez nem fontos, ha telepít írásvédett Tartományvezérlőt egy virtuális hálózaton, mert az írásvédett tartományvezérlő nem replikálja a kimenő módosításokat. De ha telepít egy írható Tartományvezérlőt, meg kell győződnie arról frissítést replikálják szükségtelen gyakorisággal nincs konfigurálva a helykapcsolatot. Ha a globális katalógus (GC-) kiszolgálóra telepíti, győződjön meg arról, hogy minden más olyan webhely, amely tartalmazza a globális Katalógus a forrás tartományvezérlő egy hely egy helykapcsolati vagy helykapcsolatok, amelyek rendelkeznek egy alacsonyabb költségek, mint a GC-nek az Azure site kapcsolódnak a tartománypartíciók replikálja.
* Akkor lehet további továbbra is a módosítása a replikációs tömörítési algoritmusának a helyek közötti replikálás által létrehozott hálózati forgalom csökkentése érdekében. A tömörítési algoritmusának a REG_DWORD beállításjegyzékbeli bejegyzés HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Replicator tömörítési algoritmust határozza meg. Az alapértelmezett érték: 3, amely megfelel a Xpress tömörítési algoritmust. Módosíthatja az érték 2, amely megváltoztatja az algoritmus a MSZip. A legtöbb esetben ez megnöveli a tömörítés, akkor viszont igen, csökkenti a CPU-felhasználást. További információkért lásd: [hogyan Active Directory replikációs topológia works](https://technet.microsoft.com/library/cc755994).

### <a name="BKMK_IPAddressDNS"></a>IP-címzést és a DNS-
Az Azure virtuális gépek alapértelmezés szerint "DHCP-bérelt címek" foglal le. Azure-beli virtuális hálózat dinamikus címek megőrzéséhez a virtuális gépekkel élettartama idején a virtuális gép, mert a Windows Server Active Directory tartományi szolgáltatások teljesülnek.

Ennek eredményeképpen Azure használjon dinamikus címet, ha meg vannak hatással a statikus IP-cím használatával, mert irányítható a címbérlet időtartama, és a címbérlet időtartama megegyezik a felhőalapú szolgáltatás élettartama.

A dinamikus cím felszabadítása azonban, hogy a virtuális gép leállítási. Az IP-cím felszabadítása folyamatban érdekében is [Set-AzureStaticVNetIP a segítségével rendelhet hozzá egy statikus IP-cím](http://social.technet.microsoft.com/wiki/contents/articles/23447.how-to-assign-a-private-static-ip-to-an-azure-vm.aspx).

A névfeloldáshoz, telepítheti a saját (vagy kihasználja a meglévő) DNS-kiszolgálói infrastruktúrában; Azure által biztosított DNS-nem felel meg a Windows Server Active Directory tartományi szolgáltatások speciális névfeloldási szükségleteit. Például hogy nem támogatja a dinamikus SRV-rekordok, és így tovább. Névfeloldás kritikus konfigurációs elem a tartományvezérlők és a tartományhoz csatlakoztatott ügyfelek. Azok a tartományvezérlők erőforrású rekordok regisztrációja és egyéb DC erőforrásrekordok feloldása képesnek kell lennie.
Tartalék tűréshatár és a teljesítmény érdekében a Windows Server DNS-szolgáltatás telepíthető az Azure-on futó tartományvezérlők optimális. Ezután állítsa be az Azure-beli virtuális hálózat tulajdonságai nevét és a DNS-kiszolgáló IP-címét. Indítsa el a virtuális hálózaton lévő más virtuális gépek, a DNS-feloldási beállításokat fogja kell konfigurálni a dinamikus IP-címek hozzárendelését részeként DNS-kiszolgáló.

> [!NOTE]
> A helyi számítógép nem csatlakozik egy Azure-platformon futó, közvetlenül az interneten keresztül a Windows Server Active Directory tartományi szolgáltatások Active Directory tartományhoz. A portokra vonatkozó követelmények az Active Directory és a tartomány-csatlakoztatási művelet megjelenítheti nem célszerű közvetlenül elérhetővé tenni a szükséges portok és a hatás, egy teljes DC az internethez.
> 
> 

Virtuális gépek regisztrálása a DNS-neve automatikusan indítási vagy egy kiszolgálónév-változás esetén.

Ez a példa és egy másik példa bemutatja, hogyan szeretnék telepíteni az első virtuális gép, és az AD DS telepítéséhez kapcsolatos további információkért lásd: [egy új Active Directory-erdő telepítése a Microsoft Azure](active-directory-new-forest-virtual-machine.md). A Windows PowerShell használatával kapcsolatos további információkért lásd: [Azure PowerShell telepítése](/powershell/azureps-cmdlets-docs) és [Azure parancsmagokat](/powershell/module/azurerm.compute/#virtual_machines).

### <a name="BKMK_DistributedDCs"></a>Földrajzilag elosztott tartományvezérlők
Azure előnyöket kínál különböző virtuális hálózatokon lévő több tartományvezérlők esetén:

* Többhelyes hibatűrési
* Fizikai közelében fiókirodák (kisebb késést biztosít)

Virtuális hálózatok közötti közvetlen kapcsolat konfigurálásával kapcsolatos további információkért lásd: [virtuális hálózat konfigurálása a virtuális hálózati kapcsolat](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="BKMK_RODC"></a>Írásvédett tartományvezérlők
Kell-e írásvédett vagy írható tartományvezérlők telepítése. Előfordulhat, hogy dönteni, RODC-k rendszerbe, mert a rendszer nem tudja befolyásolni fizikai őket, de az RODC-k úgy tervezték, hogy a helyeken, ahol a fizikai biztonsági van kitéve, például a fiókirodákban telepíthető.

Azure nem jelent-e a fiókirodában tényleges biztonsági kockázatot jelent, de az RODC-k előfordulhat, hogy továbbra is bizonyulnak költséghatékonyabb megoldás, mert a szolgáltatásokat biztosítanak az Habár nagyon eltérő okokból jól alkalmazható ezekben a környezetekben. Például RODC-k nem kimenő replikációs rendelkezik és és titkos kulcsokat (jelszó) szelektív feltöltéséhez. A hátránya előfordulhat, hogy ezeknek a kulcsoknak hiánya igény szerinti kimenő forgalom felhasználóként érvényesítéséhez, vagy a számítógép azáltal hitelesíti. De titkokat is szelektív előre feltöltve és gyorsítótárazza.

RODC-k és HBI és PII aggályokat környékén További előny adja meg, mert is hozzáadhat, hogy az RODC-re bizalmas adatokat tartalmazó attribútumok szűrt attribútumkészletet (eszközök). Az eszközök olyan attribútumokat, amelyek nem replikálódnak az RODC-k testre szabható készlete. Használhatja az eszközök a biztonságos működés érdekében, abban az esetben nem engedélyezett, vagy nem szeretné, hogy a személyhez köthető adatokat és a fontos dokumentumok tárolására az Azure-on. További információ: [RODC szűrt attribútumkészlet [(https://technet.microsoft.com/library/cc753459)].

Győződjön meg arról, hogy alkalmazás kompatibilis lesz az RODC-k használatát tervezi. Számos, Windows Server Active Directory-kompatibilis alkalmazások működnek jól működő RODC-k, de néhány alkalmazást, hajtsa végre a töredezetté, vagy sikertelen, ha nem rendelkeznek hozzáféréssel felvételére írható Tartományvezérlőkkel. További információkért lásd: [írásvédett tartományvezérlők kompatibilitási útmutatója](https://technet.microsoft.com/library/cc755190).

### <a name="BKMK_GC"></a>Globális katalógus
A globális katalógus (GC) telepíteni kell. Az erdő egyetlen tartományt úgy kell konfigurálni minden tartományvezérlő globáliskatalógus-kiszolgálóként. Mivel nincs további replikációs forgalom nem megnöveli költségeket.

Többtartományos erdőben juthatnak ebbe a generációba szükségesek a bontsa ki az univerzális csoporttagság a hitelesítési folyamat során. Ha nem kell telepítenie a globális Katalógus, a virtuális hálózati hitelesítést, a tartományvezérlő Azure munkaterhelések közvetve hoz létre a kimenő hitelesítési forgalom lekérdezni a globális katalógusok a helyszíni minden hitelesítési kísérlet során.

A globális katalógusok kapcsolódó költségeket nincsenek kevésbé előre jelezhető, mert azok üzemeltetéséhez, minden tartomány (a-része). Ha a munkaterhelés egy internetre irányuló szolgáltatást használja, és hitelesíti a felhasználókat a Windows Server AD DS-en, a költségek lehet teljesen előre nem látható. A hitelesítés során a globális Katalógus lekérdezések kívül a felhő hely csökkentése érdekében is [engedélyezi az univerzális csoporttagság gyorsítótárazását](https://technet.microsoft.com/library/cc816928).

### <a name="BKMK_InstallMethod"></a>Telepítési módszer
Meg kell adnia a tartományvezérlők, amelyek telepítése a virtuális hálózaton:

* Új tartományvezérlők előléptetése. További információkért lásd: [egy új Active Directory-erdő telepítése Azure virtuális hálózatra](active-directory-new-forest-virtual-machine.md).
* Egy helyi virtuális tartományvezérlő virtuális merevlemez áthelyezése a felhőben. Ebben az esetben győződjön meg arról, hogy a helyi virtuális tartományvezérlő "kerül," nem "másolt" vagy "Klónozott."

Csak az Azure virtuális gépek használata tartományvezérlők (szemben Azure "web" vagy "munkavégző" szerepkört virtuális gépeken). Tartós, és a tartós állapot egy tartományvezérlő szükség. Az Azure virtuális gépek tervezett munkaterhelések esetén, mint azok a tartományvezérlők.

Ne használja a SYSPREP központi telepítése, illetve azok a tartományvezérlők klónozásához. A tartományvezérlők klónozásának képességét csak szervizcsomagjától kezdve érhetők el a Windows Server 2012-ben. A Klónozási szolgáltatásának támogatást igényel VMGenerationID az alapul szolgáló hipervizorban. Hyper-V a Windows Server 2012 és az Azure virtuális hálózatok egyaránt használható VMGenerationID, harmadik fél virtualizálási szoftvergyártók.

### <a name="BKMK_PlaceDB"></a>A Windows Server AD DS-adatbázis és a SYSVOL helyét
Válassza ki a megfelelő a Windows Server AD DS-adatbázis, a naplókat, és a SYSVOL mappa helyének. Ezek az Azure-adatlemez kell telepíteni.

> [!NOTE]
> Az Azure data lemezek 4 TB-os korlátozódnak.
> 
> 

Adatok lemezmeghajtók alapértelmezés szerint nem gyorsítótár írási műveleteket hajthatja végre. Egy virtuális géphez csatolt adatok meghajtók használata visszaírt gyorsítótár. Írási gyorsítótárazással teszi, hogy az írási elkötelezte magát a tartós az Azure storage előtt a tranzakció befejeződött a virtuális gép operációs rendszerének szempontjából. Tartósság érdekében rovására némileg lassabban futnak, írások biztosít.

Ez azért fontos a Windows Server Active Directory tartományi szolgáltatások, mert a tartományvezérlő által tett feltételezéseket késleltetve visszaírt lemez-gyorsítótárazási érvénytelenné válik. Windows Server Active Directory tartományi szolgáltatások megpróbálja írási gyorsítótár letiltani, de esetén a lemez IO rendszer tiszteletben azt. Bizonyos körülmények között, tiltsa le a írási gyorsítótárazást elmulasztása fennmaradó objektumok és az egyéb problémákat eredményező USN-visszaállítás vezethetnek.

Ajánlott eljárásként virtuális tartományvezérlők tegye a következőket:

* A gazdagép gyorsítótár beállítás be az Azure adatlemez sem. Ez megakadályozza, hogy problémákat írási gyorsítótárazást Active Directory tartományi szolgáltatások műveletekhez.
* Az adatbázis, a naplókat, és a SYSVOL tárolásához vagy ugyanazon adatlemez vagy külön adatlemezek. Ez általában a lemezen maga az operációs rendszer használt lemezre. A kulcs takeaway, hogy a Windows Server AD DS-adatbázis és a SYSVOL nem tárolható a egy Azure operációsrendszer-lemez típusra. Alapértelmezés szerint az AD DS telepítési folyamata telepíti ezeket az összetevőket a % systemroot % mappában, ez nem ajánlott az Azure-bA.

### <a name="BKMK_BUR"></a>Biztonsági mentés és helyreállítás
Ügyeljen arra, mi, és nem támogatja a biztonsági mentési és visszaállítási tartományvezérlő általában, és pontosabban, a virtuális gépen futó. Lásd: [biztonsági mentése és visszaállítása a virtualizált tartományvezérlők szempontjai](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv#backup_and_restore_considerations_for_virtualized_domain_controllers).

Létrehozni a rendszerállapot biztonsági mentése csak kifejezetten ismeri a biztonsági mentés követelményeinek, a Windows Server Active Directory tartományi szolgáltatások, például a Windows Server biztonsági másolat biztonsági mentési szoftver segítségével.

Ne másolja vagy VHD-fájlokat a tartományvezérlők klónozásához helyett rendszeres biztonsági mentéseket. A visszaállítás legalább egyszer kell szükséges, ennek segítségével klónozott vagy másolt VHD-k, a Windows Server 2012 és a támogatott hipervizor nélkül is bevezeti, USN-buborékok során.

### <a name="BKMK_FedSrvConfig"></a>Összevonási kiszolgáló konfigurációja
A konfiguráció Windows Server AD FS összevonási kiszolgálójára (STSs) függ részben hogy az alkalmazásokat, amelyek az Azure-on telepíteni szeretné a helyszíni hálózati erőforrások eléréséhez szükséges.

Az alkalmazások a következő feltételeknek megfelelő, ha elkülönítve alkalmazásokat telepíthet a helyi hálózatról.

* SAML-alapú biztonsági jogkivonatokat elfogadja őket
* -E az internethez exposable
* Amikor nem férnek hozzá a helyszíni erőforrások

Ebben az esetben konfigurálása Windows Server AD FS STSs az alábbiak szerint:

1. Egy egyetlen tartományból elkülönített erdő konfigurálása az Azure-on.
2. Az erdő összevont hozzáférést biztosítson a Windows Server AD FS összevonási kiszolgálófarm konfigurálásával.
3. A helyi erdőben (összevonási kiszolgálófarm és összevonási kiszolgálóproxy-farmot) Windows Server AD FS konfigurálása
4. A helyszíni és az Azure-példányokon Windows Server Active Directory összevonási szolgáltatások közötti összevonási megbízhatósági kapcsolat létrehozására.

Másrészt az alkalmazások hozzáférést igényelnek a helyszíni erőforrásokhoz, ha konfigurálhatja a Windows Server Active Directory összevonási szolgáltatások Azure alkalmazással az alábbiak szerint:

1. Konfigurálja a helyszíni hálózatokhoz és az Azure közötti kapcsolatot.
2. A helyi erdő egy Windows Server AD FS összevonási kiszolgálófarm konfigurálása
3. A Windows Server AD FS összevonási kiszolgálóproxy-farmot konfigurálása az Azure-on.

Ez a konfiguráció azzal az előnnyel jár, a helyszíni erőforrások, hasonló a Windows Server AD FS-sel való konfigurálásához a szegélyhálózaton lévő alkalmazások kitettségének csökkentését.

Vegye figyelembe, hogy mindkét esetben hozhat létre megbízhatósági kapcsolatok kapcsolatok további identitás-szolgáltatóktól, a vállalatok együttműködés van szükség.

### <a name="BKMK_CloudSvcConfig"></a>Felhő konfigurálása
Cloud services csomag szükség, ha azt szeretné tenni a virtuális gépek közvetlenül az internethez, vagy egy internetre irányuló elosztott terhelésű alkalmazások közzétételét. Ez azért lehetséges, mert minden felhőalapú szolgáltatás kínál egy egyetlen konfigurálható virtuális IP-címet.

### <a name="BKMK_FedReqVIPDIP"></a>Összevonási kiszolgáló követelményei a nyilvános és magánhálózati IP-címzési (virtuális IP-cím és a dinamikus IP)
Minden Azure virtuális gép egy dinamikus IP-címet kap. A dinamikus IP-cím csak Azure-ban elérhető privát cím. A legtöbb esetben azonban, szükség lesz egy virtuális IP-címet a Windows Server AD FS-telepítések konfigurálása. A virtuális IP-cím szükséges – ezek általában a Windows Server AD FS végpontok az internethez, és a hitelesítéshez és folyamatos felügyeletét összevont partnerek és az ügyfelek által használandó. Egy virtuális IP-cím egy felhőalapú szolgáltatás, amely tartalmazza az Azure virtuális gépek egy vagy több tulajdonságát. Ha a telepített Azure és a Windows Server AD FS megjelölt jogcímbarát alkalmazáshoz Internet felé néző, mind a megosztás közös portokon, mindkét egy virtuális IP-címet saját szükséges, és ezért szükség lesz egy felhőalapú szolgáltatás, az alkalmazás és egy második, a Windows Server AD FS létrehozásához.

A feltételek virtuális IP-cím és a dinamikus IP-cím meghatározása, lásd: [szakkifejezéseket és definíciójukat](#BKMK_Glossary).

### <a name="BKMK_ADFSHighAvail"></a>Windows Server AD FS magas rendelkezésre állás konfigurálása
Bár különálló Windows Server AD FS összevonási szolgáltatások telepítéséhez, központi telepítéséhez legalább két csomópont a farm AD FS STS és proxyk éles környezetben ajánlott.

Lásd: [AD FS 2.0-s üzemi topológia szempontjai](https://technet.microsoft.com/library/gg982489) a a [AD FS 2.0 kialakítási útmutató](https://technet.microsoft.com/library/dd807036) döntse el, hogy melyik központi telepítési beállítások ajánlott a konkrét igényeinek.

> [!NOTE]
> Terheléselosztás a Windows Server AD FS-végpontok Azure eléréséhez konfigurálása a Windows Server AD FS-farm összes tagja ugyanazt a felhőszolgáltatásban található, és a betöltés terheléselosztási képességét HTTP (alapértelmezett: 80) az Azure-ral és a HTTPS-portot (alapértelmezett: 443). További információkért lásd: [Azure terheléselosztó mintavételi](https://msdn.microsoft.com/library/azure/jj151530).
> Windows Server hálózati terheléselosztási (NLB) nem támogatott az Azure-on.
> 
> 

