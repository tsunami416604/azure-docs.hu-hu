---
title: Hálózattervezés és kapcsolatok az Azure AD tartományi szolgáltatásokhoz | Microsoft dokumentumok
description: Ismerje meg a virtuális hálózat tervezési szempontjait és a kapcsolathoz használt erőforrásokat az Azure Active Directory tartományi szolgáltatások futtatásakor.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: e00ec8448739ac30950877a2ae196aa78cde750c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264192"
---
# <a name="virtual-network-design-considerations-and-configuration-options-for-azure-ad-domain-services"></a>Az Azure AD tartományi szolgáltatások virtuális hálózattervezési szempontjai és konfigurációs beállításai

Mivel az Azure Active Directory tartományi szolgáltatások (AD DS) hitelesítési és felügyeleti szolgáltatásokat nyújt más alkalmazások és munkaterhelések számára, a hálózati kapcsolat kulcsfontosságú összetevő. A megfelelően konfigurált virtuális hálózati erőforrások nélkül az alkalmazások és a munkaterhelések nem tudnak kommunikálni és nem használhatják az Azure AD DS által biztosított funkciókat. Tervezze meg a virtuális hálózati követelményeket, hogy az Azure AD DS szükség szerint ki tudja szolgálni az alkalmazásokat és a számítási feladatokat.

Ez a cikk ismerteti az Azure AD DS támogatásához az Azure virtuális hálózat tervezési szempontjait és követelményeit.

## <a name="azure-virtual-network-design"></a>Az Azure virtuális hálózatának kialakítása

Hálózati kapcsolat biztosításához, valamint az alkalmazások és szolgáltatások Azure AD DS-en keresztüli hitelesítésének engedélyezéséhez azure-beli virtuális hálózatot és alhálózatot kell használnia. Ideális esetben az Azure AD DS-t a saját virtuális hálózatába kell telepíteni. A felügyeleti virtuális gép vagy a könnyű alkalmazás-munkaterhelések üzemeltetéséhez egy külön alkalmazás-alhálózatot is felvehet ugyanabban a virtuális hálózatban. Egy külön virtuális hálózat nagyobb vagy összetett alkalmazás számítási feladatok, társviszonyviszonyt az Azure AD DS virtuális hálózat, általában a legmegfelelőbb design. Más formatervezési minták is érvényesek, feltéve, hogy megfelel a virtuális hálózat ra és alhálózatra vonatkozó következő szakaszokban ismertetett követelményeknek.

Az Azure AD DS virtuális hálózatának tervezésekor a következő szempontok érvényesek:

* Az Azure AD DS-t ugyanabban az Azure-régióban kell telepíteni, mint a virtuális hálózat.
    * Jelenleg csak egy Azure AD DS-felügyelt tartományt telepíthet Azure AD-bérlőnként. Az Azure AD DS felügyelt tartomány egyetlen régióra van telepítve. Győződjön meg arról, hogy az [Azure AD DS szolgáltatást támogató régióban](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all)hoz létre vagy választ virtuális hálózatot.
* Vegye figyelembe más Azure-régiók és az alkalmazásszámítási feladatokat üzemeltető virtuális hálózatok közelségét.
    * A késés minimalizálása érdekében tartsa az alapvető alkalmazások közel, vagy ugyanabban a régióban, mint az Azure AD DS felügyelt tartomány virtuális hálózati alhálózat. Használhatja a virtuális hálózati társviszony-létesítés i vagy virtuális magánhálózati (VPN) kapcsolatok az Azure virtuális hálózatok között. Ezeket a csatlakozási lehetőségeket a következő szakasz tárgyalja.
* A virtuális hálózat nem támaszkodhat az Azure AD DS által nyújtott DNS-szolgáltatásokon kívül.
    * Az Azure AD DS saját DNS-szolgáltatást biztosít. A virtuális hálózatot úgy kell konfigurálni, hogy ezeket a DNS-szolgáltatáscímeket használja. A további névterek névfeloldása feltételes továbbítók használatával is elvégezhető.
    * Az egyéni DNS-kiszolgáló beállításai nem használhatók más DNS-kiszolgálókról, többek között virtuális gépekről érkező lekérdezések irányításához. A virtuális hálózat erőforrásainak az Azure AD DS által biztosított DNS-szolgáltatást kell használniuk.

> [!IMPORTANT]
> Az Azure AD DS nem helyezhető át egy másik virtuális hálózatra, miután engedélyezte a szolgáltatást.

Az Azure AD DS által felügyelt tartomány egy Azure virtuális hálózat alhálózatához csatlakozik. Tervezze meg ezt az alhálózatot az Azure AD DS szolgáltatáshoz a következő szempontokat figyelembe véve:

* Az Azure AD DS-t saját alhálózatában kell telepíteni. Ne használjon meglévő alhálózatot vagy átjáró-alhálózatot.
* Egy hálózati biztonsági csoport jön létre az Azure AD DS felügyelt tartomány üzembe helyezése során. Ez a hálózati biztonsági csoport tartalmazza a megfelelő szolgáltatáskommunikációhoz szükséges szabályokat.
    * Ne hozzon létre és ne használjon meglévő hálózati biztonsági csoportot saját egyéni szabályokkal.
* Az Azure AD DS 3-5 IP-címet igényel. Győződjön meg arról, hogy az alhálózati IP-címtartomány ennyi címszámot tud megadni.
    * A rendelkezésre álló IP-címek korlátozása megakadályozhatja, hogy az Azure AD tartományi szolgáltatások két tartományvezérlőkarbantartását.

A következő példadiagram egy érvényes tervet vázol fel, ahol az Azure AD DS saját alhálózattal rendelkezik, van egy átjáró-alhálózat a külső kapcsolathoz, és az alkalmazás-munkaterhelések a virtuális hálózaton belül egy csatlakoztatott alhálózatban vannak:

![Ajánlott alhálózati kialakítás](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

## <a name="connections-to-the-azure-ad-ds-virtual-network"></a>Kapcsolatok az Azure AD DS virtuális hálózatával

Amint azt az előző szakaszban megjegyeztük, csak egy Azure AD tartományi szolgáltatások által felügyelt tartományt hozhat létre egyetlen virtuális hálózatban az Azure-ban, és az Azure AD-bérlőnként csak egy felügyelt tartomány hozható létre. Ezen architektúra alapján előfordulhat, hogy csatlakoztatnia kell egy vagy több virtuális hálózatokat, amelyek az alkalmazás számítási feladatok at üzemelteti az Azure AD DS virtuális hálózat.

A más Azure virtuális hálózatokban üzemeltetett alkalmazás-munkaterheléseket az alábbi módszerek egyikével csatlakoztathatja:

* Társviszony létesítése virtuális hálózatok között
* Virtuális magánhálózat (VPN)

### <a name="virtual-network-peering"></a>Társviszony létesítése virtuális hálózatok között

A virtuális hálózati társviszony-létesítés egy olyan mechanizmus, amely két virtuális hálózatot köt össze ugyanabban a régióban az Azure gerinchálózaton keresztül. A globális virtuális hálózati társviszony-létesítés az Azure-régiók ban is képes csatlakozni a virtuális hálózathoz. A társviszony-létesítést követően a két virtuális hálózat lehetővé teszi az erőforrások, például a virtuális gépek, kommunikálnak egymással közvetlenül a magánhálózati IP-címek használatával. A virtuális hálózati társviszony-létesítés lehetővé teszi, hogy egy Azure AD DS felügyelt tartomány üzembe helyezését az alkalmazás számítási feladatok más virtuális hálózatokban telepített.

![Virtuális hálózati kapcsolat társviszony-létesítéssel](./media/active-directory-domain-services-design-guide/vnet-peering.png)

További információ: [Azure virtuális hálózati társviszony-létesítési áttekintés.](../virtual-network/virtual-network-peering-overview.md)

### <a name="virtual-private-networking-vpn"></a>Virtuális magánhálózat (VPN)

A virtuális hálózatot ugyanúgy csatlakoztathatja egy másik virtuális hálózathoz (VNet-vnet), mint ahogy a virtuális hálózatot egy helyszíni helyhelyre konfigurálhatja. Mindkét kapcsolat VPN-átjárót használ egy biztonságos alagút létrehozásához IPsec/IKE használatával. Ez a kapcsolatmodell lehetővé teszi az Azure AD DS üzembe helyezését egy Azure virtuális hálózatba, majd a helyszíni helyeket vagy más felhőket.

![Virtuális hálózati kapcsolat VPN-átjáró használatával](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

A virtuális magánhálózatok használatáról további információt a Virtuális hálózat és [virtuális hálózat VPN-átjárókapcsolatának konfigurálása az Azure Portal használatával című](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal)olvassa el.

## <a name="name-resolution-when-connecting-virtual-networks"></a>Névfeloldás virtuális hálózatok csatlakoztatásakor

Az Azure AD tartományi szolgáltatások virtuális hálózatához kapcsolódó virtuális hálózatok általában saját DNS-beállításokkal rendelkeznek. Virtuális hálózatok csatlakoztatásakor nem konfigurálja automatikusan a névfeloldást a csatlakozó virtuális hálózathoz az Azure AD DS felügyelt tartomány által nyújtott szolgáltatások feloldásához. A csatlakozó virtuális hálózatok névfeloldását úgy kell konfigurálni, hogy az alkalmazás-munkaterhelések megkereshessék az Azure AD tartományi szolgáltatásokat.

A névfeloldást feltételes DNS-továbbítókkal engedélyezheti a virtuális hálózatok csatlakoztatását támogató DNS-kiszolgálón, vagy az Azure AD tartományszolgáltatás virtuális hálózatából származó azonos DNS-IP-címeket használva.

## <a name="network-resources-used-by-azure-ad-ds"></a>Az Azure AD DS által használt hálózati erőforrások

Egy Azure AD DS felügyelt tartomány hoz létre néhány hálózati erőforrások üzembe helyezés során. Ezek az erőforrások szükségesek az Azure AD DS felügyelt tartomány ának sikeres kezeléséhez és kezeléséhez, és nem kell manuálisan konfigurálni.

| Azure-erőforrás                          | Leírás |
|:----------------------------------------|:---|
| Hálózati kártya                  | Az Azure AD DS két tartományvezérlőn (DCs) üzemelteti a felügyelt tartományt, amelyek A Windows Server en Azure virtuális gépként futnak. Minden virtuális gép rendelkezik egy virtuális hálózati adapterrel, amely csatlakozik a virtuális hálózati alhálózathoz. |
| Dinamikus szabványos nyilvános IP-cím      | Az Azure AD DS kommunikál a szinkronizálási és felügyeleti szolgáltatás egy szabványos termékváltozat nyilvános IP-címet használ. A nyilvános IP-címekről az [Azure IP-címtípusai és foglalási módszerei](../virtual-network/virtual-network-ip-addresses-overview-arm.md)című témakörben talál további információt. |
| Az Azure szabványos terheléselosztója            | Az Azure AD DS egy szabványos termékváltozat-terheléselosztót használ a hálózati címfordításhoz (NAT) és a terheléselosztáshoz (biztonságos LDAP-val való használat esetén). Az Azure terheléselosztókról a [Mi az Azure Load Balancer című](../load-balancer/load-balancer-overview.md) témakörben talál további információt? |
| Hálózati címfordítási (NAT) szabályok | Az Azure AD DS három NAT-szabályt hoz létre és használ a terheléselosztón – egy szabályt a biztonságos HTTP-forgalomhoz, és két szabályt a biztonságos PowerShell-távhíváshoz. |
| Terheléselosztói szabályok                     | Ha egy Azure AD DS felügyelt tartomány van konfigurálva a biztonságos LDAP a TCP-porton 636, három szabály jön létre, és egy terheléselosztó a forgalom elosztására. |

> [!WARNING]
> Ne törölje az Azure AD DS által létrehozott hálózati erőforrást. Ha törli a hálózati erőforrások bármelyikét, az Azure AD DS szolgáltatás kimaradástörténik.

## <a name="network-security-groups-and-required-ports"></a>Hálózati biztonsági csoportok és kötelező portok

A [hálózati biztonsági csoport (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) olyan szabályok listáját tartalmazza, amelyek engedélyezik vagy megtagadják a hálózati forgalmat az Azure virtuális hálózat ban. Egy hálózati biztonsági csoport jön létre, amikor telepíti az Azure AD DS, amely olyan szabályokat tartalmaz, amelyek lehetővé teszik a szolgáltatás hitelesítési és felügyeleti funkciókat. Ez az alapértelmezett hálózati biztonsági csoport társítva van a virtuális hálózati alhálózat az Azure AD DS felügyelt tartományban telepítve van.

A következő hálózati biztonsági csoport szabályok szükségesek az Azure AD DS hitelesítési és felügyeleti szolgáltatások biztosításához. Ne szerkessze vagy törölje ezeket a hálózati biztonsági csoportszabályokat az Azure AD DS által felügyelt tartományban üzembe helyezett virtuális hálózati alhálózathoz.

| Portszám | Protocol (Protokoll) | Forrás                             | Cél | Műveletek | Kötelező | Cél |
|:-----------:|:--------:|:----------------------------------:|:-----------:|:------:|:--------:|:--------|
| 443         | TCP      | AzureActiveDirectoryDomainServices | Bármelyik         | Engedélyezés  | Igen      | Szinkronizálás az Azure AD-bérlővel. |
| 3389        | TCP      | CorpNetSaw között                         | Bármelyik         | Engedélyezés  | Igen      | A tartomány kezelése. |
| 5986        | TCP      | AzureActiveDirectoryDomainServices | Bármelyik         | Engedélyezés  | Igen      | A tartomány kezelése. |
| 636         | TCP      | Bármelyik                                | Bármelyik         | Engedélyezés  | Nem       | Csak akkor engedélyezve van, ha biztonságos LDAP (LDAPS) beállítást konfigurál. |

> [!WARNING]
> Ne manuálisan edzse ezeket a hálózati erőforrásokat és konfigurációkat. Ha egy helytelenül konfigurált hálózati biztonsági csoportot vagy egy felhasználó által definiált útvonaltáblát társít azzal az alhálózattal, amelyben az Azure AD DS telepítve van, megzavarhatja a Microsoft képességét a tartomány kiszolgálására és kezelésére. Szinkronizálás az Azure AD-bérlő és az Azure AD DS felügyelt tartomány is megszakadt.
>
> *Az AllowVnetInBound*, *Az AllowAzureLoadBalancerInBound*, *a DenyAllInBound*, *az AllowVnetOutBound*, *az AllowInternetOutBound*és a *DenyAllOutBound* alapértelmezett szabályai is léteznek a hálózati biztonsági csoporthoz. Ne írja és ne törölje ezeket az alapértelmezett szabályokat.
>
> Az Azure SLA nem vonatkozik azokra a központi telepítésekre, ahol egy nem megfelelően konfigurált hálózati biztonsági csoport és/vagy a felhasználó által definiált útvonaltáblák at alkalmaztak, amelyek blokkolják az Azure AD DS frissítését és kezelését.

### <a name="port-443---synchronization-with-azure-ad"></a>443-as port – szinkronizálás az Azure AD-vel

* Az Azure AD-bérlő szinkronizálására szolgál az Azure AD DS felügyelt tartományával.
* A porthoz való hozzáférés nélkül az Azure AD DS felügyelt tartománya nem szinkronizálható az Azure AD-bérlővel. Előfordulhat, hogy a felhasználók nem tudnak bejelentkezni, mert a jelszavak módosításai nem lesznek szinkronizálva az Azure AD DS felügyelt tartományával.
* Az IP-címekhez való bejövő hozzáférés alapértelmezés szerint korlátozott az **AzureActiveDirectoryDomainServices** szolgáltatáscímkéjével.
* Ne korlátozza a kimenő hozzáférést erről a portról.

### <a name="port-3389---management-using-remote-desktop"></a>Port 3389 - kezelés távoli asztal használatával

* Az Azure AD DS által felügyelt tartománytartományvezérlőkhöz való távoli asztali kapcsolatokhoz használható.
* Az alapértelmezett hálózati biztonsági csoportszabály a *CorpNetSaw* szolgáltatáscímkét használja a forgalom további korlátozására.
    * Ez a szolgáltatáscímke csak biztonságos hozzáférést biztosít a Microsoft vállalati hálózatának biztonságos hozzáférési munkaállomásai számára, hogy távoli asztalt használjanak az Azure AD DS felügyelt tartományához.
    * Az access csak üzleti indoklással engedélyezett, például felügyeleti vagy hibaelhárítási forgatókönyvekhez.
* Ez a szabály beállítható *Megtagadás*, és csak akkor állítható *be engedélyezésre,* ha szükséges. A legtöbb felügyeleti és figyelési feladatok powershell-távirányítással történik. Az RDP csak abban a ritka esetben használatos, amikor a Microsoftnak távolról kell csatlakoznia a felügyelt tartományhoz a speciális hibaelhárításhoz.

> [!NOTE]
> A *corpnetsaw* szolgáltatáscímke manuálisan nem választható ki a portálról, ha megpróbálja módosítani ezt a hálózati biztonsági csoportszabályt. Az Azure PowerShell vagy az Azure CLI használatával manuálisan kell konfigurálnia egy szabályt, amely a *CorpNetSaw* szolgáltatáscímkét használja.

### <a name="port-5986---management-using-powershell-remoting"></a>5986-os port – felügyelet a PowerShell-távirányítás használatával

* Felügyeleti feladatok végrehajtására használható powershell-távirányítással az Azure AD DS felügyelt tartományban.
* A porthoz való hozzáférés nélkül az Azure AD DS felügyelt tartománya nem frissíthető, konfigurálható, nem lehet biztonsági másolatot kapni vagy figyelni.
* Az Azure AD DS által felügyelt tartományok, amelyek egy Resource Manager-alapú virtuális hálózat, korlátozhatja a bejövő hozzáférést a port az *AzureActiveDirectoryDomainServices* szolgáltatáscímke.
    * Az Azure AD DS által felügyelt, klasszikus alapú virtuális hálózatot használó korlátozhatja a bejövő hozzáférést a következő forrás IP-címekre: *52.180.183.8*, *23.101.0.70*, *52.225.184.198*, *52.179.126.223*, *13.74.249.156*, *52.187.117.83*, *52.161.13.95*, *104.40.156.18*, és *104.40.87.209*.

    > [!NOTE]
    > 2017-ben az Azure AD tartományi szolgáltatások elérhetővé vált az Azure Resource Manager-hálózatban való üzemeltetéshez. Azóta egy biztonságosabb szolgáltatást tudtunk készíteni az Azure Resource Manager modern képességeinek használatával. Mivel az Azure Resource Manager központi telepítései teljes mértékben helyettesítik a klasszikus üzemelő példányokat, az Azure AD DS klasszikus virtuális hálózati telepítései 2023.
    >
    > További információkért lásd a [hivatalos epreprecation](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="user-defined-routes"></a>Felhasználó által megadott útvonalak

A felhasználó által definiált útvonalak alapértelmezés szerint nem jönnek létre, és nem szükségesek az Azure AD DS megfelelő működéséhez. Ha útvonaltáblákat kell használnia, ne módosítsa a *0.0.0.0* útvonalat. Az útvonal módosításai megzavarják az Azure AD tartományi szolgáltatásokat, és a felügyelt tartományt nem támogatott állapotba helyezik.

A megfelelő Azure-szolgáltatáscímkékben szereplő IP-címekről érkező bejövő forgalmat is át kell irányítania az Azure AD tartományi szolgáltatások alhálózatába. A szolgáltatáscímkékről és a hozzájuk tartozó IP-címről az [Azure IP-tartományok és szolgáltatáscímkék – nyilvános felhő](https://www.microsoft.com/en-us/download/details.aspx?id=56519)című témakörben talál további információt.

> [!CAUTION]
> Ezek az Azure-adatközpont IP-tartományok értesítés nélkül változhatnak. Győződjön meg arról, hogy a legújabb IP-címekkel rendelkezik.

## <a name="next-steps"></a>További lépések

Az Azure AD DS által használt hálózati erőforrásokról és csatlakozási lehetőségekről az alábbi cikkekben talál további információt:

* [Az Azure virtuális hálózati társviszony-létesítése](../virtual-network/virtual-network-peering-overview.md)
* [Azure VPN-átjárók](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)
* [Azure-beli hálózati biztonsági csoportok](../virtual-network/security-overview.md)
