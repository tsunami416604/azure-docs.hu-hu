---
title: Azure VMware-megoldások (AVS) – az AVS Private Cloud beállítása vész-helyreállítási helyként a VMware Site Recovery Manager használatával
description: Leírja, hogyan állítható be az AVS Private Cloud vész-helyreállítási webhelyként a helyszíni VMware-alapú számítási feladatokhoz
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ccdf385d2581923e1fad4fa5c42f351c4f0947ca
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083151"
---
# <a name="set-up-avs-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>Az AVS Private Cloud beállítása vész-helyreállítási célként a VMware Site Recovery Managerrel

Az AVS saját Felhőjét vész-helyreállítási (DR) webhelyként használhatja a helyszíni VMware-munkaterhelésekhez.

A DR megoldás a vSphere Replication és a VMware Site Recovery Manager (SRM) szolgáltatáson alapul. Hasonló módszer követhető, ha az AVS Private-felhőt olyan elsődleges helyként engedélyezi, amelyet a helyszíni helyreállítási hely véd.

Az AVS-megoldás:

* Nem kell kifejezetten DR-hez beállítania az adatközpontot.
* Lehetővé teszi, hogy kihasználja azokat az Azure-telephelyeket, amelyeken az AVS a globális földrajzi rugalmasság érdekében telepítve van.
* Lehetőséget biztosít a központi telepítési költségek és a DR. alapításának teljes tulajdonlási költségeinek csökkentésére.

Az AVS-megoldáshoz az alábbiakra van szükség:

* A vSphere-replikáció és az SRM telepítése, konfigurálása és kezelése az AVS Private Cloud-ban.
* Saját licencek megadása az SRM számára, ha az AVS Private Cloud a védett hely. Ha helyreállítási helyként használja, nincs szükség további SRM-licencekre az AVS-helyhez.

Ezzel a megoldással teljes mértékben szabályozhatja a vSphere-replikációt és az SRM-t. Az ismerős felhasználói felület, API és CLI felületek lehetővé teszik a meglévő parancsfájlok és eszközök használatát.

![Site Recovery Manager üzembe helyezése](media/srm-deployment.png)

Használhatja a vRA és az SRM bármely olyan verzióját, amely kompatibilis az AVS Private Cloud és a helyszíni környezetekkel. Az útmutatóban szereplő példák a vRA 6,5 és az SRM 6,5 használatát használják. Ezek a verziók kompatibilisek az AVS által támogatott vSphere 6,5-mel.

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

A következő szakaszok azt ismertetik, hogyan helyezhet üzembe egy DR-megoldást SRM használatával az AVS Private Cloud-ban.

1. [Annak ellenőrzése, hogy kompatibilisek-e a VMware-termékek](#verify-that-vmware-product-versions-are-compatible)
2. [A DR környezet méretének becslése](#estimate-the-size-of-your-dr-environment)
3. [AVS Private Cloud létrehozása a környezethez](#create-an-avs-private-cloud-for-your-environment)
4. [Az AVS Private Cloud Networking beállítása az SRM-megoldáshoz](#set-up-avs-private-cloud-networking-for-the-srm-solution)
5. [Helyek közötti VPN-kapcsolat beállítása a helyszíni hálózat és az AVS Private Cloud között és a szükséges portok megnyitása](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-avs-private-cloud-and-open-required-ports)
6. [Infrastruktúra-szolgáltatások beállítása az AVS Private Cloud-ban](#set-up-infrastructure-services-in-your-avs-private-cloud)
7. [A vSphere replikációs berendezés telepítése a helyszíni környezetben](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [A vSphere-replikációs berendezés telepítése az AVS Private Cloud Environment-ban](#install-vsphere-replication-appliance-in-your-avs-private-cloud-environment)
9. [Az SRM-kiszolgáló telepítése a helyszíni környezetben](#install-srm-server-in-your-on-premises-environment)
10. [Az SRM-kiszolgáló telepítése az AVS Private Cloud-ban](#install-srm-server-in-your-avs-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Annak ellenőrzése, hogy kompatibilisek-e a VMware-termékek

Az útmutatóban szereplő konfigurációkra az alábbi kompatibilitási követelmények vonatkoznak:

* Az összes SRM-verziót telepíteni kell az AVS Private-felhőben és a helyszíni környezetben.
* A vSphere-replikáció ugyanazon verzióját kell telepítenie az AVS Private-felhőben és a helyszíni környezetben.
* Az AVS Private Cloud-beli platform Services-vezérlő (PSC) verziójának és a helyszíni környezetnek kompatibilisnek kell lennie.
* A vCenter az AVS Private-felhőben és a helyszíni környezetben is kompatibilisnek kell lennie.
* Az SRM-és vSphere-replikáció verziójának kompatibilisnek kell lennie egymással, valamint a PSC és a vCenter verziójával.

A kapcsolódó VMware-dokumentációra és a kompatibilitási információkra mutató hivatkozásokat a [vmware site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/index.html) dokumentációjában talál.

A vCenter és a PSC verziójának az AVS Private-felhőben való megkereséséhez nyissa meg az AVS-portált. Válassza az **erőforrások**, majd az AVS Private Cloud lehetőséget, és kattintson a **vSphere felügyeleti hálózat** fülre.

![vCenter & PSC-verziók az AVS Private Cloud-ban](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>A DR környezet méretének becslése

1. Ellenőrizze, hogy az azonosított helyszíni konfiguráció a támogatott határértékeken belül van-e. A 6,5-es SRM esetében a határértékeket a VMware Tudásbázis a [site Recovery Manager 6,5 működési korlátairól](https://kb.vmware.com/s/article/2147110)szóló cikkében ismertetjük.
2. Győződjön meg arról, hogy megfelelő hálózati sávszélességgel rendelkezik a számítási feladatok méretének és a RPO követelményeinek megfelelően. A [vSphere replikálásához szükséges sávszélesség-követelmények kiszámítására vonatkozó,](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html) a VMware Tudásbázisról szóló cikk útmutatást nyújt a sávszélesség korlátozásához.
3. Az AVS-méretezési eszköz segítségével megbecsülheti a DR-helyen szükséges erőforrásokat a helyszíni környezete elleni védelem érdekében.

### <a name="create-an-avs-private-cloud-for-your-environment"></a>AVS Private Cloud létrehozása a környezethez

Hozzon létre egy AVS-beli privát felhőt az AVS-portálon, és kövesse az [AVS Private Cloud létrehozása](create-private-cloud.md)című témakör útmutatásait és méretezési javaslatait.

### <a name="set-up-avs-private-cloud-networking-for-the-srm-solution"></a>Az AVS Private Cloud Networking beállítása az SRM-megoldáshoz

Nyissa meg az AVS-portált, és állítsa be az AVS Private Cloud Networking szolgáltatást az SRM-megoldáshoz.

Hozzon létre egy VLAN-t az SRM-megoldási hálózat számára, és rendeljen hozzá egy alhálózati CIDR. Útmutatásért lásd: [VLAN-ok/alhálózatok létrehozása és kezelése](create-vlan-subnet.md).

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-avs-private-cloud-and-open-required-ports"></a>Helyek közötti VPN-kapcsolat beállítása a helyszíni hálózat és az AVS Private Cloud között és a szükséges portok megnyitása

Helyek közötti kapcsolat beállítása a helyszíni hálózat és az AVS Private Cloud között. Útmutatásért lásd: [VPN-kapcsolat konfigurálása az AVS Private Cloud](set-up-vpn.md)szolgáltatáshoz.

### <a name="set-up-infrastructure-services-in-your-avs-private-cloud"></a>Infrastruktúra-szolgáltatások beállítása az AVS Private Cloud-ban

Konfigurálja úgy az infrastruktúra-szolgáltatásokat az AVS privát felhőben, hogy könnyen kezelhető legyen a számítási feladatok és eszközök.

Ha az alábbi műveletek egyikét szeretné elvégezni, vegyen fel egy külső Identitáskezelő-szolgáltatót az [AVS Private Cloud-vCenter az Azure ad használata identitás-szolgáltatóként](azure-ad.md) című részben leírtak szerint:

* Azonosítsa a helyszíni Active Directory (AD) felhasználóit az AVS Private Cloud-ban.
* Hozzon létre egy AD-t az AVS Private Cloud-ban az összes felhasználó számára.
* Használja az Azure AD-t.

Az IP-címek keresésének, az IP-címek kezelésének és a névfeloldási szolgáltatásoknak az AVS Private Cloud-ban való megadásához állítson be egy DHCP-és DNS-kiszolgálót a [DNS-és DHCP-alkalmazások és-munkaterhelések beállítása az AVS Private Cloud](dns-dhcp-setup.md)-ban című részben leírtak szerint.

A *. cloudsimple.io tartományt a felügyeleti virtuális gépek és a gazdagépek használják az AVS Private Cloud-ban. A tartományra irányuló kérelmek feloldásához konfigurálja a DNS-továbbítást a DNS-kiszolgálón a [feltételes továbbító létrehozása](on-premises-dns-setup.md#create-a-conditional-forwarder)című témakörben leírtak szerint.

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>A vSphere replikációs berendezés telepítése a helyszíni környezetben

Telepítse a vSphere-replikációs készüléket (vRA) a helyszíni környezetbe a VMware dokumentációjának követésével. A telepítés a következő magas szintű lépésekből áll:

1. Készítse elő a helyszíni környezetet a vRA telepítéséhez.

2. Telepítse a vRA a helyszíni környezetben a VR ISO-OVF a vmware.com-ből. A vRA 6,5 esetében [Ez a VMware-blog](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) rendelkezik a vonatkozó információkkal.

3. Regisztrálja helyszíni vRA a vCenter-alapú egyszeri bejelentkezéssel a helyszíni helyen. A vSphere replikálás 6,5-es verziójának részletes ismertetését lásd: VMware-dokumentum [VMware vSphere replikáció 6,5 telepítés és konfigurálás](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

## <a name="install-vsphere-replication-appliance-in-your-avs-private-cloud-environment"></a>A vSphere-replikációs berendezés telepítése az AVS Private Cloud Environment-ban

Mielőtt elkezdené, ellenőrizze, hogy rendelkezik-e a következőkkel:

* IP-elérhetőség a helyszíni környezetben lévő alhálózatokról az AVS Private Cloud felügyeleti alhálózatára
* IP-elérhetőség a helyszíni vSphere-környezetben lévő replikációs alhálózatról az AVS Private Cloud SRM-megoldás alhálózatára

Útmutatásért lásd: [VPN-kapcsolat konfigurálása az AVS Private Cloud](set-up-vpn.md)szolgáltatáshoz. A lépések hasonlóak a helyszíni telepítéshez.

Az AVS azt javasolja, hogy az IP-címek helyett a teljes tartománynevet használja a vRA és az SRM telepítésekor. A vCenter és a PSC teljes tartománynevének az AVS Private-felhőben való megkereséséhez nyissa meg az AVS-portált. Válassza az **erőforrások**, majd az AVS Private Cloud lehetőséget, és kattintson a **vSphere felügyeleti hálózat** fülre.

![VCenter/PSC teljes tartománynevek megkeresése az AVS Private Cloud-ban](media/srm-resources.png)

Az AVS megköveteli, hogy ne telepítse a vRA és az SRM-t az alapértelmezett "cloudowner" felhasználóval, hanem hozzon létre egy új felhasználót. Ennek segítségével biztosítható az AVS Private Cloud vCenter-környezet magas rendelkezésre állásának és elérhetőségének biztosítása. Az AVS Private Cloud vCenter alapértelmezett cloudowner-felhasználója azonban nem rendelkezik megfelelő jogosultsággal ahhoz, hogy új felhasználót hozzon létre rendszergazdai jogosultságokkal.

A vRA és az SRM telepítése előtt ki kell bővíteni a cloudowner-felhasználó vCenter-jogosultságait, majd létre kell hoznia egy rendszergazdai jogosultságokkal rendelkező felhasználót a vCenter SSO-tartományban. Az alapértelmezett AVS Private Cloud felhasználói és engedélyezési modell részletes ismertetését lásd: [az AVS Private Cloud engedélyezési modell megismerése](learn-private-cloud-permissions.md).

A telepítés a következő magas szintű lépésekből áll:

1. [Kiterjesztési jogosultságok](escalate-private-cloud-privileges.md).
2. Hozzon létre egy felhasználót az AVS Private-felhőben a vSphere replikálás és az SRM telepítéséhez. Az alábbiakban ismertetett [vCenter felhasználói felület: hozzon létre egy felhasználót az AVS Private Cloud-ban a vRA &AMP; SRM telepítéséhez](#vcenter-ui-create-a-user-in-the-avs-private-cloud-for-vra-and-srm-installation).
3. Készítse elő az AVS saját felhőalapú környezetét a vRA telepítéséhez.
4. Telepítse a vRA-t az AVS Private Cloud-ban a VR ISO-OVF a vmware.com-ből. A vRA 6,5 esetében [Ez a VMware blog](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) releváns információkat tartalmaz.
5. A vRA vonatkozó tűzfalszabályok konfigurálása. Az alábbi lépések az [AVS Portalon: a vRA vonatkozó tűzfalszabályok konfigurálása](#avs-portal-configure-firewall-rules-for-vra).
6. Regisztrálja az AVS Private Cloud vRA az vCenter egyszeri bejelentkezéssel az AVS Private Cloud webhelyén.
7. Konfigurálja a vSphere replikálási kapcsolatait a két készülék között. Győződjön meg arról, hogy a szükséges portok meg vannak nyitva a tűzfalak között. Tekintse meg [ezt a VMware Tudásbázis-cikket](https://kb.vmware.com/s/article/2087769) azon portszámok listájáért, amelyeknek nyitva kell lenniük a vSphere-replikáció 6,5-es számú replikálásához.

A vSphere-replikáció 6,5-es verziójának részletes telepítési utasításait a VMware-dokumentum [VMware vSphere a replikáció 6,5 telepítés és konfigurálás](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)című cikkben tekintheti meg.

#### <a name="vcenter-ui-create-a-user-in-the-avs-private-cloud-for-vra-and-srm-installation"></a>vCenter felhasználói felület: hozzon létre egy felhasználót az AVS Private Cloud-ban a vRA és az SRM telepítéséhez

Jelentkezzen be a vCenter-be a cloudowner felhasználói hitelesítő adataival az AVS-portálra vonatkozó jogosultságok kiterjesztését követően.

Hozzon létre egy új felhasználót, `srm-soln-admin`a vCenter, és adja hozzá a rendszergazdák csoporthoz a vCenter-ben.
Jelentkezzen ki a vCenter cloudowner-felhasználóként, és jelentkezzen be az *SRM-soln-admin* felhasználóként.

#### <a name="avs-portal-configure-firewall-rules-for-vra"></a>AVS-portál: a vRA vonatkozó tűzfalszabályok konfigurálása

Konfigurálja a tűzfalszabályok beállításait a következő témakörben leírtak szerint: a [tűzfalak és szabályok beállítása](firewall.md) a portok megnyitásához a következők közötti kommunikáció engedélyezéséhez:

* vRA az SRM-megoldás hálózati és vCenter-és ESXi-gazdagépei a felügyeleti hálózaton.
* vRA készülékeket a két helyen.

Tekintse meg ezt a [VMware Tudásbázis-cikket](https://kb.vmware.com/s/article/2087769) azon portszámok listájáért, amelyeknek nyitva kell lenniük a vSphere-replikáció 6,5-es számú replikálásához.

### <a name="install-srm-server-in-your-on-premises-environment"></a>Az SRM-kiszolgáló telepítése a helyszíni környezetben

Mielőtt elkezdené, ellenőrizze a következőket:

* a vSphere replikációs berendezést a helyszíni és az AVS Private Cloud Environments környezetbe telepíti a rendszer.
* A vSphere replikációs berendezések mindkét helyen csatlakoznak egymáshoz.
* Áttekintette a VMware-információkat az előfeltételekkel és az ajánlott eljárásokkal kapcsolatban. Az 6,5-es SRM esetében a VMware-dokumentumok [előfeltételei és ajánlott eljárásai](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)a következő témakörben találhatók: SRM 6,5.

Kövesse [a VMware-](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)dokumentációt az SRM-kiszolgálók telepítésének elvégzéséhez a "Kéthelyes topológia egy vCenter-példánnyal egy platform Services-vezérlőn" című témakörben leírtak szerint. Az SRM 6,5-es verziójának telepítési utasításai a [site Recovery Manager telepítését](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)végző VMware-dokumentumban találhatók.

### <a name="install-srm-server-in-your-avs-private-cloud"></a>Az SRM-kiszolgáló telepítése az AVS Private Cloud-ban

Mielőtt elkezdené, ellenőrizze a következőket:

* a vSphere replikációs berendezést a helyszíni és az AVS Private Cloud Environments környezetbe telepíti a rendszer.
* A vSphere replikációs berendezések mindkét helyen csatlakoznak egymáshoz.
* Áttekintette a VMware-információkat az előfeltételekkel és az ajánlott eljárásokkal kapcsolatban. Az SRM 6,5- [es verziójában a site Recovery Manager 6,5 Server telepítésének előfeltételeit és ajánlott eljárásait](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)tekintheti át.

A következő lépések az AVS Private Cloud SRM telepítését ismertetik.

1. [vCenter felhasználói felület: SRM telepítése](#vcenter-ui-install-srm)
2. [AVS-portál: az SRM tűzfalszabály-szabályainak konfigurálása](#avs-portal-configure-firewall-rules-for-srm)
3. [vCenter felhasználói felület: az SRM konfigurálása](#vcenter-ui-configure-srm)
4. [AVS-portál: jogosultságok kiterjesztésének megszüntetése](#avs-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>vCenter felhasználói felület: SRM telepítése

Miután bejelentkezett a vCenter az SRM-soln-admin hitelesítő adatok használatával, kövesse a VMware dokumentációt az SRM-kiszolgáló telepítésének elvégzéséhez a "Kéthelyes topológia egy vCenter-példánnyal a platform Services-vezérlőkkel" című cikkben leírtak [szerint.](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html) Az SRM 6,5-es verziójának telepítési utasításai a [site Recovery Manager telepítését](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)végző VMware-dokumentumban találhatók.

#### <a name="avs-portal-configure-firewall-rules-for-srm"></a>AVS-portál: az SRM tűzfalszabály-szabályainak konfigurálása

Konfigurálja a tűzfalszabályok beállításait a következő témakörben leírtak szerint: a [tűzfalak és szabályok beállítása](firewall.md) a közötti kommunikáció engedélyezéséhez:

Az SRM-kiszolgáló és a vCenter/PSC az AVS Private Cloud-ban.
Az SRM-kiszolgálók mindkét helyen

Tekintse meg [ezt a VMware Tudásbázis-cikket](https://kb.vmware.com/s/article/2087769) azon portszámok listájáért, amelyeknek nyitva kell lenniük a vSphere-replikáció 6,5-es számú replikálásához.

#### <a name="vcenter-ui-configure-srm"></a>vCenter felhasználói felület: az SRM konfigurálása

Miután telepítette a SRM-t az AVS Private-felhőbe, hajtsa végre a következő feladatokat a VMware Site Recovery Manager telepítési és konfigurációs útmutatójának szakaszában leírtak szerint. Az 6,5-es SRM esetében az utasítások a [site Recovery Manager telepítését](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)végző VMware-dokumentumban találhatók.

1. Kapcsolja össze a Site Recovery Manager Server-példányokat a védett és a helyreállítási helyeken.
2. Hozzon létre egy ügyfélkapcsolatot a távoli Site Recovery Manager Server-példányhoz.
3. Telepítse a Site Recovery Manager licenckulcs.

#### <a name="avs-portal-de-escalate-privileges"></a>AVS-portál: jogosultságok kiterjesztésének megszüntetése

A jogosultságok megszüntetéséhez tekintse meg a [jogosultságok dekiterjesztését](escalate-private-cloud-privileges.md#de-escalate-privileges)ismertető témakört.

## <a name="ongoing-management-of-your-srm-solution"></a>Az SRM-megoldás folyamatos felügyelete

Teljes körűen szabályozhatja az vSphere-replikációs és az SRM szoftvereket az AVS Private Cloud-környezetben, és el kell végeznie a szükséges szoftverek életciklus-felügyeletét. A vSphere-replikáció vagy az SRM frissítése vagy frissítése előtt győződjön meg arról, hogy a szoftver minden új verziója kompatibilis az AVS Private Cloud vCenter és a PSC-vel.

> [!NOTE]
> Az AVS jelenleg a felügyelt DR szolgáltatást kínáló lehetőségeket vizsgálja. 

## <a name="multiple-replication-configuration"></a>Több replikációs konfiguráció

 A [Array-alapú replikáció és a vSphere replikációs technológiák egyszerre is használhatók az SRM](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication) szolgáltatással együtt. Ezeket azonban a virtuális gépek elkülönített készletére kell alkalmazni (az adott virtuális gép a tömb alapú replikálás vagy a vSphere replikálás, de nem mindkettő) számára is védhető. Emellett az AVS-hely több védett helyhez is konfigurálható helyreállítási helyként. További információ a többhelyes konfigurációkról: [SRM többhelyes beállításai](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/) .

## <a name="references"></a>Referencia

* [A VMware Site Recovery Manager dokumentációja](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [A Site Recovery Manager 6,5 működési korlátai](https://kb.vmware.com/s/article/2147110)
* [A vSphere-replikáció sávszélesség-követelményének kiszámítása](https://kb.vmware.com/s/article/2037268)
* [OVF-beállítások az vSphere-replikáció telepítésekor 6,5](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [VMware vSphere replikáció 6,5 telepítés és konfigurálás](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [Előfeltételek és ajánlott eljárások az SRM 6,5-hez](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [Site Recovery Manager egy Kéthelyes topológiában a platform Services-vezérlő egy vCenter Server példányával](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [VMware Site Recovery Manager 6,5 telepítési és konfigurációs útmutató](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [VMware blog az SRM-ben tömb alapú replikációval és vSphere-replikációval](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [VMware blog az SRM többhelyes beállításaiban](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [A vSphere replikálás 5,8. x, 6. x és 8. között nyitva lévő portszámok](https://kb.vmware.com/s/article/2147112)
