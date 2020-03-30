---
title: Azure VMware-megoldás a CloudSimple-től – A magánfelhő beállítása vész-helyreállítási helyként a VMware Site Recovery Manager használatával
description: A CloudSimple Private Cloud beállítása a helyszíni VMware-számítási feladatok vész-helyreállítási helyeként
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fa8b2da683d68a337df38e13726f22c5af43540a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565927"
---
# <a name="set-up-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>A Magánfelhő beállítása vész-helyreállítási célként a VMware Site Recovery Manager segítségével

Használhatja a CloudSimple Private Cloud vész-helyreállítási (DR) hely a helyszíni VMware számítási feladatok.

A DR megoldás a vSphere replikáción és a VMware Site Recovery Manageren (SRM) alapul. Hasonló megközelítést lehet követni, hogy a privát felhő elsődleges helyként, amely a helyszíni helyreállítási hely által védett.

A CloudSimple megoldás:

* Szükségtelenné teszi a központi dr-hez tartozó adatközpont beállítását.
* Lehetővé teszi, hogy kihasználja az Azure-helyeket, ahol a CloudSimple telepítve van a globális földrajzi rugalmasság érdekében.
* Lehetőséget ad a telepítési költségek és a teljes tulajdonlási költség csökkentésére a DR létrehozásához.

A CloudSimple megoldás megköveteli, hogy tegye a következőket:

* Telepítse, konfigurálja és kezelje a vSphere replikációt és az SRM-t a magánfelhőben.
* Adja meg saját licenceit az SRM-hez, ha a magánfelhő a védett webhely. Nincs szükség további SRM-licencre a CloudSimple webhelyhez, ha azt a helyreállítási helyként használja.

Ezzel a megoldással teljes mértékben szabályozhatja a vSphere replikációt és az SRM-t. A jól ismert felhasználói felület, API és CLI felületek lehetővé teszik a meglévő parancsfájlok és eszközök használatát.

![A Hely-helyreállítási kezelő telepítése](media/srm-deployment.png)

A vRA és az SRM bármely verzióját használhatja, amelyek kompatibilisek a privát felhőbeli és helyszíni környezetekkel. Az útmutatóban szereplő példák a vRA 6.5 és az SRM 6.5 használatát használják. Ezek a verziók kompatibilisek a vSphere 6.5-tel, amelyet a CloudSimple támogat.

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

Az alábbi szakaszok ismertetik, hogyan telepíthet egy DR-megoldást az SRM használatával a privát felhőben.

1. [Annak ellenőrzése, hogy a VMware termékverziói kompatibilisek-e](#verify-that-vmware-product-versions-are-compatible)
2. [A VÉSZ-környezet méretének becslése](#estimate-the-size-of-your-dr-environment)
3. [Privát felhő létrehozása a környezetéhez](#create-a-private-cloud-for-your-environment)
4. [Privát felhőalapú hálózat beállítása az SRM-megoldáshoz](#set-up-private-cloud-networking-for-the-srm-solution)
5. [Helyről helyekre vpn-kapcsolat beállítása a helyszíni hálózat és a magánfelhő között, valamint a szükséges portok megnyitása](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports)
6. [Infrastruktúra-szolgáltatások beállítása a magánfelhőben](#set-up-infrastructure-services-in-your-private-cloud)
7. [VSphere replikációs készülék telepítése a helyszíni környezetben](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [VSphere replikációs készülék telepítése a privát felhőkörnyezetben](#install-vsphere-replication-appliance-in-your-private-cloud-environment)
9. [SRM-kiszolgáló telepítése a helyszíni környezetben](#install-srm-server-in-your-on-premises-environment)
10. [SRM-kiszolgáló telepítése a magánfelhőben](#install-srm-server-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Annak ellenőrzése, hogy a VMware termékverziói kompatibilisek-e

Az útmutatóban szereplő konfigurációkra a következő kompatibilitási követelmények vonatkoznak:

* Az SRM ugyanazon verzióját kell telepíteni a privát felhőben és a helyszíni környezetben.
* A vSphere replikáció ugyanazon verzióját kell telepíteni a privát felhőben és a helyszíni környezetben.
* A Platform Services Controller (PSC) verzióinak a magánfelhőben és a helyszíni környezetben kompatibilisnek kell lenniük.
* A vCenter verzióinak a privát felhőben és a helyszíni környezetben kompatibilisnek kell lenniük.
* Az SRM és a vSphere replikáció verzióinak kompatibilisnek kell lenniük egymással, valamint a PSC és a vCenter verzióival.

A VMware vonatkozó dokumentációjára és kompatibilitási információira mutató hivatkozásokat a [VMware Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/index.html) dokumentációjában találja.

Ha meg szeretné tudni, hogy a vCenter és a PSC verziói a privát felhőben, nyissa meg a CloudSimple portálon. Nyissa meg az **Erőforrások**lapot, válassza ki a magánfelhőt, és kattintson a **vSphere felügyeleti hálózat** fülre.

![VCenter & PSC-verziók a magánfelhőben](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>A VÉSZ-környezet méretének becslése

1. Ellenőrizze, hogy az azonosított helyszíni konfiguráció támogatott korlátokon belül van-e. Az SRM 6.5 esetében a korlátokat a VMware tudásbázisának a [Site Recovery Manager 6.5 működési korlátairól](https://kb.vmware.com/s/article/2147110)szóló cikke dokumentálja.
2. Győződjön meg arról, hogy elegendő hálózati sávszélességgel rendelkezik a számítási feladatok méretének és az RPO-követelményeknek. A VMware tudásbázis cikke a [vSphere-replikáció sávszélesség-követelményeinek kiszámításáról](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html) útmutatást nyújt a sávszélesség-korlátokhoz.
3. A CloudSimple sizer eszköz segítségével becsülje meg a DR-webhelyen a helyszíni környezet védelméhez szükséges erőforrásokat.

### <a name="create-a-private-cloud-for-your-environment"></a>Privát felhő létrehozása a környezetéhez

Hozzon létre egy privát felhőt a CloudSimple portálról az utasítások követésével és a [Magánfelhő létrehozása](create-private-cloud.md)című részben található méretezési javaslatok alapján.

### <a name="set-up-private-cloud-networking-for-the-srm-solution"></a>Privát felhőalapú hálózat beállítása az SRM-megoldáshoz

A CloudSimple portálon beállíthatja az SRM-megoldás magánfelhő-hálózatát.

Hozzon létre egy VLAN-t az SRM-megoldáshálózathoz, és rendeljen hozzá egy alhálózati CIDR-t. További információt a [VLAN-ok/alhálózatok létrehozása és kezelése című témakörben talál.](create-vlan-subnet.md)

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports"></a>Helyről helyekre vpn-kapcsolat beállítása a helyszíni hálózat és a magánfelhő között, valamint a szükséges portok megnyitása

A helyszíni hálózat és a magánfelhő közötti helyek közötti kapcsolat beállítása. További információt a [VPN-kapcsolat konfigurálása a CloudSimple private cloud szolgáltatással című témakörben talál.](set-up-vpn.md)

### <a name="set-up-infrastructure-services-in-your-private-cloud"></a>Infrastruktúra-szolgáltatások beállítása a magánfelhőben

Konfigurálja az infrastruktúra-szolgáltatásokat a magánfelhőben, hogy megkönnyítse a számítási feladatok és eszközök kezelését.

Ha az alábbiak valamelyikét szeretné megtenni, külső identitásszolgáltatót is hozzáadhat a [Use Azure AD as a vCenter identitásszolgáltatójaként a CloudSimple private cloud szolgáltatásban](azure-ad.md) leírtak szerint:

* Azonosítsa a felhasználókat a helyszíni Active Directoryból (AD) a magánfelhőben.
* Állítson be egy AD-t a privát felhőben az összes felhasználó számára.
* Használja az Azure AD-t.

Ha IP-címkeresési, IP-címkezelési és névfeloldási szolgáltatásokat szeretne biztosítani a magánfelhőben lévő számítási feladatokhoz, állítson be egy DHCP- és DNS-kiszolgálót a [DNS- és DHCP-alkalmazások és -számítási feladatok beállítása a CloudSimple magánfelhőben](dns-dhcp-setup.md)című részen leírtak szerint.

A *.cloudsimple.io tartományt a felügyeleti virtuális gépek és a magánfelhőben lévő gazdagépek használják. A tartományra vonatkozó kérelmek feloldásához konfigurálja a DNS-továbbítást a DNS-kiszolgálón a [Feltételes továbbító létrehozása című](on-premises-dns-setup.md#create-a-conditional-forwarder)részben leírtak szerint.

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>VSphere replikációs berendezés telepítése a helyszíni környezetben

Telepítse a vSphere replikációs berendezést (vRA) a helyszíni környezetben a VMware dokumentációjának követésével. A telepítés a következő magas szintű lépésekből áll:

1. Készítse elő a helyszíni környezetben a vRA telepítését.

2. Telepítse a vRA-t a helyszíni környezetben az OVF használatával a VR ISO-ban vmware.com. A vRA 6.5 esetében [ez a VMware blog](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) rendelkezik a megfelelő információkkal.

3. Regisztrálja a helyszíni vRA-t a vCenter Single Sign-On segítségével a helyszíni webhelyen. A vSphere replication 6.5 részletes utasításait a [VMware vSphere replication 6.5 telepítési és konfigurációs dokumentumában találja.](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)

## <a name="install-vsphere-replication-appliance-in-your-private-cloud-environment"></a>VSphere replikációs készülék telepítése a privát felhőkörnyezetben

Mielőtt elkezdené, ellenőrizze, hogy rendelkezik-e a következőkkel:

* Az IP-elérhetőség a helyszíni környezetalhálózataitól a privát felhő felügyeleti alhálózatához
* Az IP-elérhetőség a helyszíni vSphere környezetreplikációs alhálózatától a privát felhő SRM-megoldásalhálózatába

További információt a [VPN-kapcsolat konfigurálása a CloudSimple private cloud szolgáltatással című témakörben talál.](set-up-vpn.md) A lépések hasonlóak a helyszíni telepítés lépéseihez.

A CloudSimple azt javasolja, hogy a vRA és az SRM telepítése során ip-címek helyett fqdn-eket használjon. Ha meg szeretné tudni, hogy a teljes qdn a vCenter és a PSC a privát felhőben, nyissa meg a CloudSimple portálon. Nyissa meg az **Erőforrások**lapot, válassza ki a magánfelhőt, és kattintson a **vSphere felügyeleti hálózat** fülre.

![A vCenter/PSC teljes tartománynevének keresése a magánfelhőben](media/srm-resources.png)

A CloudSimple megköveteli, hogy ne telepítse a vRA-t és az SRM-t az alapértelmezett "felhőtulajdonos" felhasználóval, hanem hozzon létre egy új felhasználót. Ez a privát felhőbeli vCenter-környezet magas rendelkezésre állásának és rendelkezésre állásának biztosítása érdekében történik. Azonban az alapértelmezett felhőtulajdonos felhasználó a Private Cloud vCenter nem rendelkezik megfelelő jogosultságokkal egy új felhasználó rendszergazdai jogosultságokkal.

A vRA és az SRM telepítése előtt ki kell fokoznia a felhőtulajdonos felhasználó vCenter-jogosultságait, majd létre kell hoznia egy rendszergazdai jogosultságokkal rendelkező felhasználót a vCenter SSO-tartományban. Az alapértelmezett private cloud-felhasználóról és engedélymodellről a [Magánfelhő engedélymodell megismerése című](learn-private-cloud-permissions.md)témakörben olvashat részletesen.

A telepítés a következő magas szintű lépésekből áll:

1. [A jogosultságok eszkalálódása](escalate-private-cloud-privileges.md).
2. Hozzon létre egy felhasználót a privát felhőben a vSphere replikációhoz és az SRM-telepítéshez. Az alábbiakban ismertetjük a [vCenter felhasználói felületén: Hozzon létre egy felhasználót a magánfelhőben a vRA & srm telepítéséhez.](#vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation)
3. Készítse elő a privát felhőkörnyezetet a vRA telepítéséhez.
4. Telepítse a vRA-t a privát felhőben az OVF használatával a vmware.com-től származó VR ISO-ban. A vRA 6.5, [ez a VMware blog](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) releváns információkat.
5. Konfigurálja a tűzfalszabályokat a vRA-hoz. Az alábbiakban a [CloudSimple portálon ismertetett: Tűzfalszabályok konfigurálása a vRA-hoz.](#cloudsimple-portal-configure-firewall-rules-for-vra)
6. Regisztráljon privát felhőbeli vRA-t a vCenter Single Sign-On szolgáltatással a private cloud webhelyen.
7. Konfigurálja a vSphere replikációs kapcsolatait a két készülék között. Győződjön meg arról, hogy a szükséges portok meg vannak nyitva a tűzfalakon keresztül. Tekintse meg [ezt a VMware tudásbáziscikket](https://kb.vmware.com/s/article/2087769) a vSphere replication 6.5 számára nyitott portszámok listájáról.

A vSphere replication 6.5 részletes telepítési utasításait a [VMware vSphere replication 6.5 telepítési és konfigurációs dokumentumában találja.](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation"></a>vCenter felhasználói felület: Felhasználó létrehozása a magánfelhőben a vRA és az SRM telepítéséhez

Jelentkezzen be a vCenter be felhőtulajdonos felhasználói hitelesítő adatok használatával a CloudSimple portálon a jogosultságok eszkalálódása után.

Hozzon létre `srm-soln-admin`egy új felhasználót, a vCenterben, és adja hozzá a rendszergazdák csoport vCenter.
Jelentkezzen ki a vCenter, mint a felhőtulajdonos felhasználó, és jelentkezzen be, mint a *srm-soln-admin* felhasználó.

#### <a name="cloudsimple-portal-configure-firewall-rules-for-vra"></a>CloudSimple portál: Tűzfalszabályok konfigurálása a vRA-hoz

Konfigurálja a tűzfalszabályokat a [Tűzfaltáblák beállítása és](firewall.md) a portok megnyitására vonatkozó szabályok szerint, hogy lehetővé tegye a kommunikációt a következők között:

* vRA az SRM-megoldáshálózatban, valamint a vCenter és ESXi gazdagépei a felügyeleti hálózatban.
* a két telephelyen.

Tekintse meg ezt a [VMware tudásbáziscikket](https://kb.vmware.com/s/article/2087769) a vSphere replication 6.5 számára nyitott portszámok listájáról.

### <a name="install-srm-server-in-your-on-premises-environment"></a>SRM-kiszolgáló telepítése a helyszíni környezetben

Mielőtt elkezdené, ellenőrizze a következőket:

* A vSphere replikációs berendezés a helyszíni és a privát felhőalapú környezetekben van telepítve.
* A vSphere replikációs készülékek mindkét helyen kapcsolódnak egymáshoz.
* Áttekintette a VMware-adatokat az előfeltételekről és az ajánlott eljárásokról. Az SRM 6.5 esetében a VMware dokumentum [előfeltételei és az SRM 6.5 ajánlott eljárások című dokumentumában](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)tájékolhat.

A VMware dokumentációjának követésével végrehajthatja az SRM-kiszolgáló telepítését a "Kéthelyes topológia platformszolgáltatás-vezérlőnként egy vCenter-példánysal" telepítési modellben a [jelen VMWare-dokumentumban](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)leírtak szerint. Az SRM 6.5 telepítési utasításai a VMware dokumentumban [találhatók, a Site Recovery Manager telepítése](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)című dokumentumban.

### <a name="install-srm-server-in-your-private-cloud"></a>SRM-kiszolgáló telepítése a magánfelhőben

Mielőtt elkezdené, ellenőrizze a következőket:

* A vSphere replikációs berendezés a helyszíni és a privát felhőalapú környezetekben van telepítve.
* A vSphere replikációs készülékek mindkét helyen kapcsolódnak egymáshoz.
* Áttekintette a VMware-adatokat az előfeltételekről és az ajánlott eljárásokról. Az SRM 6.5 esetében a [Site Recovery Manager 6.5 Server Installation című dokumentum előfeltételei és gyakorlati tanácsai](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)című dokumentumban található.

A következő lépések a privát felhőbeli srm-telepítést ismertetik.

1. [vCenter felhasználói felület: SRM telepítése](#vcenter-ui-install-srm)
2. [CloudSimple portál: Tűzfalszabályok konfigurálása az SRM-hez](#cloudsimple-portal-configure-firewall-rules-for-srm)
3. [vCenter felhasználói felület: Az SRM konfigurálása](#vcenter-ui-configure-srm)
4. [CloudSimple portál: a jogosultságok eszkalációja](#cloudsimple-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>vCenter felhasználói felület: SRM telepítése

Miután srm-soln-admin hitelesítő adatokkal naplózta a vCenterbe, kövesse a VMware dokumentációját az SRM-kiszolgáló telepítéséhez a "Kéthelyes topológia platformszolgáltatás-vezérlőnként egy vCenter-példánysal" telepítési modellben a [jelen VMWare-dokumentumban](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)leírtak szerint. Az SRM 6.5 telepítési utasításai a VMware dokumentumban [találhatók, a Site Recovery Manager telepítése](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)című dokumentumban.

#### <a name="cloudsimple-portal-configure-firewall-rules-for-srm"></a>CloudSimple portál: Tűzfalszabályok konfigurálása az SRM-hez

A [tűzfaltáblák és a tűzfaltáblák beállítása](firewall.md) című közleményben leírt tűzfalszabályok konfigurálása a következők közötti kommunikáció engedélyezéséhez:

Az SRM-kiszolgáló és a vCenter / PSC a magánfelhőben.
Az SRM-kiszolgálók mindkét helyen

Tekintse meg [ezt a VMware tudásbáziscikket](https://kb.vmware.com/s/article/2087769) a vSphere replication 6.5 számára nyitott portszámok listájáról.

#### <a name="vcenter-ui-configure-srm"></a>vCenter felhasználói felület: Az SRM konfigurálása

Miután az SRM telepítve van a magánfelhőben, hajtsa végre a következő feladatokat a VMware Site Recovery Manager telepítési és konfigurációs útmutatószakaszaiban leírtak szerint. Az SRM 6.5 esetén az utasítások a VMware dokumentumban [találhatók,](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)a Site Recovery Manager telepítése című dokumentumban.

1. Csatlakoztassa a Site Recovery Manager kiszolgálópéldányait a védett és helyreállítási helyeken.
2. Ügyfélkapcsolat létrehozása a távoli hely helyreállítási felelőse kiszolgálói példányával.
3. Telepítse a Site Recovery Manager licenckulcsát.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple portál: A jogosultságok eszkalációja

A jogosultságok eszkalálódásának visszaeséséhez olvassa el a [Jogosultságok eltörlése](escalate-private-cloud-privileges.md#de-escalate-privileges).

## <a name="ongoing-management-of-your-srm-solution"></a>Az SRM-megoldás folyamatos kezelése

Teljes hozzáférése van a vSphere replikációnak és az SRM-szoftvereknek a privát felhőalapú környezetben, és elvárják, hogy végrehajtsa a szükséges szoftveréletciklus-kezelést. A vSphere replikáció vagy az SRM frissítése vagy frissítése előtt győződjön meg arról, hogy a szoftver bármely új verziója kompatibilis a Private Cloud vCenter és a PSC szolgáltatással.

> [!NOTE]
> A CloudSimple jelenleg vizsgálja a felügyelt VÉSZ-szolgáltatás nyújtásának lehetőségeit. 

## <a name="multiple-replication-configuration"></a>Több replikációs konfiguráció

 [A tömbalapú replikációs és a vSphere replikációs technológiák](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication) egy időben az SRM-rel együtt is használhatók. Azonban azokat külön virtuális gépekre kell alkalmazni (egy adott virtuális gép tömbalapú replikációval vagy vSphere-replikációval védhető, de mindkettőnem). Továbbá a CloudSimple hely több védett hely helyreállítási helyként konfigurálható. A többhelyes konfigurációkról további információt az [SRM többhelyes](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/) konfigurációkról talál.

## <a name="references"></a>Referencia

* [VMware webhely-helyreállítási kezelő dokumentációja](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [Működési korlátok a Site Recovery Manager 6.5-höz](https://kb.vmware.com/s/article/2147110)
* [A vSphere-replikáció sávszélesség-követelményeinek kiszámítása](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)
* [OVF választási lehetőségek a vSphere replikáció 6.5 telepítésekor](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [VMware vSphere replikáció 6.5 telepítés és konfigurálás](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [Előfeltételek és bevált gyakorlatok az SRM 6.5-ben](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [Hely-helyreállítási kezelő kétszemélyes topológiában, platformszolgáltatási vezérlőnként egy vCenter-kiszolgálópéldánysal](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [VMware Site Recovery Manager 6.5 telepítési és konfigurációs útmutató](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [VMware Blog srm tömb alapú replikáció vs vSphere replikáció](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [VMware Blog az SRM többoldalas opciókról](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [A vSphere replikációhoz nyitva álló portszámok 5.8.x, 6.x és 8](https://kb.vmware.com/s/article/2147112)
