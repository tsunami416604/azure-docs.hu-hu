---
title: Azure VMware Solutions (AVS) – az AVS Private Cloud site használatával virtuális asztali infrastruktúrát futtathat a VMware Horizon használatával
description: Leírja, hogyan használhatja az AVS Private Cloud-webhelyet a virtuális asztali infrastruktúra VMware Horizon használatával történő üzemeltetéséhez
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f909ce297344e80ed6004631d5218d6a7fcd5085
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025248"
---
# <a name="use-avs-private-cloud-site-to-host-a-virtual-desktop-infrastructure-using-vmware-horizon"></a>Az AVS Private Cloud site használatával virtuális asztali infrastruktúrát futtathat a VMware Horizon használatával

A virtuális asztali infrastruktúrát (VDI) a VMware Horizon 7. x használatával futtathatja az AVS Private Cloud-webhelyén. Az alábbi ábrán a VDI logikai megoldási architektúrája látható.

![Horizont üzembe helyezése](media/horizon-deployment.png)

Ezzel a megoldással teljes mértékben szabályozhatja a Horizon View Manager és az alkalmazás kötetét. Az ismerős felhasználói felület, API és CLI felületek lehetővé teszik a meglévő parancsfájlok és eszközök használatát.

Az AVS-megoldáshoz az alábbiakra van szükség:

* A VMware Horizon 7. x telepítése, konfigurálása és kezelése az AVS Private Cloud-ban.
* Adja meg saját Horizon-licenceit.

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

A következő szakaszok azt ismertetik, hogyan helyezhet üzembe egy VDI-megoldást az AVS Private-felhőben a horizont használatával.

1. [Annak ellenőrzése, hogy kompatibilisek-e a VMware-termékek](#verify-that-vmware-product-versions-are-compatible)
2. [Az asztali környezet méretének becslése](#estimate-the-size-of-your-desktop-environment)
3. [AVS Private Cloud létrehozása a környezethez](#create-an-avs-private-cloud-for-your-environment)
4. [A VMware Horizon telepítése az AVS Private Cloud-ban](#install-vmware-horizon-in-your-avs-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Annak ellenőrzése, hogy kompatibilisek-e a VMware-termékek

* Győződjön meg arról, hogy a horizont, az alkalmazás kötetei, az egyesített hozzáférési átjáró és a felhasználói környezet kezelője aktuális és tervezett verziói kompatibilisek egymással, valamint a vCenter és a PSC-mel az AVS Private Cloud-ban. A kompatibilitási információk: [VMware kompatibilitási mátrix a Horizon 7,5-hez](https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&260=2877&0=).
* Ha szeretné megtudni a vCenter és a PSC aktuális verzióját az AVS Private Cloud-ban, az [AVS Portalon](access-cloudsimple-portal.md)kattintson az **erőforrások** elemre, válassza ki a saját AVS-felhőjét, majd kattintson a **vSphere felügyeleti hálózat** fülre.

![vCenter és PSC verziók](media/private-cloud-vsphere-versions.png)

### <a name="estimate-the-size-of-your-desktop-environment"></a>Az asztali környezet méretének becslése

* Ellenőrizze, hogy az azonosított konfiguráció VMware működési korlátokon belül van-e.
* Becsülje meg az összes asztali számítógép és a horizont Management-összetevői számára szükséges erőforrásokat.

### <a name="create-an-avs-private-cloud-for-your-environment"></a>AVS Private Cloud létrehozása a környezethez

1. Hozzon létre egy AVS-beli privát felhőt az AVS-portálon az [AVS Private Cloud-környezet konfigurálása](quickstart-create-private-cloud.md)című részben ismertetett utasítások alapján. Az AVS létrehoz egy "cloudowner" nevű alapértelmezett vCenter-felhasználót minden újonnan létrehozott AVS Private-felhőben. Az alapértelmezett AVS Private Cloud User és Permission modell részletes ismertetését lásd: [az AVS Private Cloud-engedélyek modell megismerése](learn-private-cloud-permissions.md).
2. Hozzon létre egy VLAN-t az AVS Private-felhőben a horizont felügyeleti síkon, és rendeljen hozzá egy alhálózati CIDR. Útmutatásért lásd: [VLAN-ok/alhálózatok létrehozása és kezelése](create-vlan-subnet.md). Ez az a hálózat, ahol az összes megoldás-összetevő (egyesített hozzáférési átjáró, kapcsolati kiszolgáló, alkalmazás-kötet kiszolgáló és felhasználói környezet-kezelő kiszolgáló) telepítve lesz.
3. Döntse el, hogy szeretne-e külső identitás-szolgáltatót használni az AVS Private Cloud vCenter. Ha igen, válasszon egyet a következő lehetőségek közül:
    * Külső identitás-szolgáltatóként használhatja a helyszíni Active Directory. Útmutatásért lásd: [vCenter Identity sources](set-vcenter-identity.md).
    * Hozzon létre egy Active Directory kiszolgálót az AVS Private Cloud-ban a Horizon felügyeleti sík VLAN-ban, hogy külső identitás-szolgáltatóként használhassa. Útmutatásért lásd: [vCenter Identity sources](set-vcenter-identity.md).
    * Állítsa be a DHCP-és DNS-kiszolgálót a Horizon felügyeleti sík VLAN-ban az AVS Private Cloud-ban. Útmutatásért lásd: [DNS-és DHCP-alkalmazások és-munkaterhelések beállítása az AVS Private Cloud-ban](dns-dhcp-setup.md).
4. Konfigurálja a DNS-továbbítást az AVS Private Cloud-ban telepített DNS-kiszolgálón. Útmutatásért lásd: [feltételes továbbító létrehozása](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vmware-horizon-in-your-avs-private-cloud"></a>A VMware Horizon telepítése az AVS Private Cloud-ban

A következő üzembe helyezési diagram egy AVS Private-felhőben üzembe helyezett Horizon-megoldást ábrázol. Az egyesített hozzáférési átjáró, az AD/DC, a View és az App Volume Server telepítve van a felhasználó által létrehozott VLAN 234-ben. Az egyesített hozzáférési átjáró rendelkezik egy olyan hozzárendelt nyilvános IP-címmel, amely elérhető az internetről. A Horizon asztali készlet virtuális gépei a VLAN 235-ben vannak üzembe helyezve, hogy további elkülönítést és biztonságot nyújtsanak.

![Horizon üzembe helyezés az AVS Private Cloud-ban](media/horizon-private-cloud.png)

Az alábbi részekben az ábrán láthatóhoz hasonló központi telepítés beállítására vonatkozó utasításokat vázoljuk. Mielőtt elkezdené, ellenőrizze, hogy rendelkezik-e a következőkkel:

* Egy, az AVS-portál használatával létrehozott AVS Private Cloud, amely elegendő kapacitással rendelkezik az asztali készletek futtatásához.
* Elegendő sávszélesség a helyszíni környezet és az AVS Private Cloud Environment között az asztali számítógépek hálózati forgalmának támogatásához.
* Helyek közötti VPN-alagút, amely a helyszíni adatközpont és az AVS Private Cloud között állítható be.
* A helyszíni környezetben lévő végfelhasználói alhálózatok IP-elérhetősége az AVS Private Cloud alhálózatokra.
* AD/DHCP/DNS telepítve az AVS Private Cloud-hoz.

#### <a name="avs-portal-create-a-dedicated-vlansubnet-for-desktop-pools"></a>AVS-portál: dedikált VLAN/alhálózat létrehozása asztali készletekhez

Hozzon létre egy VLAN-t a Horizon Desktop-készletek számára, és rendeljen hozzá egy alhálózati CIDR. Útmutatásért lásd: [VLAN-ok/alhálózatok létrehozása és kezelése](create-vlan-subnet.md). Ez az a hálózat, ahol az összes asztali virtuális gép futni fog.

A horizonton üzemelő példányának biztonságossá tételéhez kövesse az általános biztonsági eljárásokat:

* Csak az asztali RDP-forgalom/SSH-forgalom engedélyezése az asztali virtuális gépeken.
* Csak a Horizon felügyeleti sík VLAN és az asztali készlet VLAN közötti felügyeleti forgalom engedélyezése.
* Csak a helyszíni hálózatról származó felügyeleti forgalom engedélyezése.

Az ajánlott eljárásokat a [Tűzfalszabályok](firewall.md) az AVS-portálról történő konfigurálásával kényszerítheti ki.

#### <a name="avs-portal-configure-firewall-rules-to-secure-horizon-management-plane"></a>AVS-portál: tűzfalszabályok konfigurálása a horizont felügyeleti síkja biztonságossá tételéhez

Állítsa be az alábbi szabályokat az AVS-portálon. Útmutatásért tekintse [meg a tűzfalak és szabályok beállítása](firewall.md)című témakört.

1. Konfigurálja a tűzfalszabályok az AVS N-S tűzfalon, hogy lehetővé tegye a helyi alhálózatok és a horizont-felügyeleti VLAN közötti kommunikációt, hogy csak a VMware Document [Horizon port listán](https://docs.vmware.com/en/VMware-Horizon-7/7.1/com.vmware.horizon-client-agent.security.doc/GUID-52807839-6BB0-4727-A9C7-EA73DE61ADAB.html) felsorolt hálózati portok engedélyezettek legyenek.

2. Hozzon létre E-W tűzfalszabályok a Horizon Management VLAN és az asztali készlet VLAN között az AVS Private Cloud-ban.

#### <a name="avs-portal-create-a-public-ip-address-for-unified-access-gateway"></a>AVS-portál: nyilvános IP-cím létrehozása az egyesített hozzáférésű átjáróhoz

Hozzon létre egy nyilvános IP-címet az egyesített hozzáférésű átjáró berendezés számára, amely lehetővé teszi az internetes asztali ügyfélkapcsolatok használatát. Útmutatásért lásd: [nyilvános IP-címek lefoglalása](public-ips.md).

Ha a telepítés befejeződött, a nyilvános IP-cím hozzárendelése és listázása a nyilvános IP-címek lapon történik.

#### <a name="avs-portal-escalate-privileges"></a>AVS-portál: jogosultságok kiterjesztésének megvonása

Az alapértelmezett "cloudowner" felhasználó nem rendelkezik megfelelő jogosultsággal az AVS Private Cloud vCenter a horizont telepítéséhez, így a felhasználó vCenter jogosultságait ki kell bővíteni. További információ: a [jogosultságok kiterjesztésének](escalate-private-cloud-privileges.md)megemelése.

#### <a name="vcenter-ui-create-a-user-in-avs-private-cloud-for-horizon-installation"></a>vCenter felhasználói felület: hozzon létre egy felhasználót az AVS Private Cloud for Horizon telepítéséhez

1. Jelentkezzen be a vCenter a "cloudowner" felhasználói hitelesítő adatok használatával.
2. Hozzon létre egy új felhasználót, "Horizon-soln-admin"-t a vCenter-ben, és vegye fel a felhasználót a vCenter rendszergazdák csoportjába.
3. Jelentkezzen ki a vCenter "cloudowner" felhasználóként, és jelentkezzen be "Horizon-soln-admin" felhasználóként.

#### <a name="vcenter-ui-install-vmware-horizon"></a>vCenter felhasználói felület: VMware Horizon telepítése

Ahogy azt a korábbi logikai architektúra szakaszban is említettük, a Horizon megoldás a következő összetevőket tartalmazza:

* VMware Horizon nézet
* VMware Unified Access Gateway
* VMware app Volume Manager
* VMware felhasználói környezet kezelője

Az összetevőket a következőképpen telepítheti:

1. Az egyesített hozzáférési átjáró telepítése és konfigurálása a VMware [Unified Access Gateway üzembe helyezésére és konfigurálására](https://docs.vmware.com/en/Unified-Access-Gateway/3.3.1/com.vmware.uag-331-deploy-config.doc/GUID-F5CE0D5E-BE85-4FA5-BBCF-0F86C9AB8A70.html)szolgáló VMware-dokumentum utasításait követve.

2. Telepítse a Horizon nézetet az AVS Private Cloud-ba a [telepítési útmutató megtekintése](https://docs.vmware.com/en/VMware-Horizon-7/7.4/horizon-installation/GUID-37D39B4F-5870-4188-8B11-B6C41AE9133C.html)című részben leírtak szerint.

3. Telepítse az App Volume Managert a VMware- [alkalmazások telepítése és konfigurálása](https://docs.vmware.com/en/VMware-App-Volumes/2.10/com.vmware.appvolumes.user.doc/GUID-5E8BAF8C-F5A6-412C-9424-266BA7109BA4.html)című témakör utasításait követve.

4. Telepítse és konfigurálja a felhasználói környezet kezelőjét a [VMware User Environment Manager telepítésének és konfigurálásának](https://docs.vmware.com/en/VMware-User-Environment-Manager/9.4/com.vmware.user.environment.manager-install-config/GUID-DBBC82E4-483F-4B28-9D49-4D28E08715BC.html)utasításait követve.

#### <a name="file-a-support-request-to-upload-vmware-horizon-pre-packaged-app-volumes"></a>Támogatási kérelem a VMware Horizon előre csomagolt alkalmazások köteteinek feltöltéséhez

A telepítési folyamat részeként az App Volume Manager előre csomagolt köteteket használ az alkalmazások és az írható kötetek kiépítéséhez. Ezek a kötetek sablonként szolgálnak az alkalmazások és az írható kötetek számára.

A kötetek az AVS Private Cloud adattárhoz való feltöltéséhez az ESXi-gyökér jelszava szükséges. Segítségért küldje el a [támogatási kérelmet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Csatolja a AppVolumes Installer-csomagot, hogy az AVS-támogatási munkatársak fel tudják tölteni a sablonokat az AVS Private Cloud Environment-ba.

#### <a name="avs-portal-de-escalate-privileges"></a>AVS-portál: jogosultságok kiterjesztésének megszüntetése

Mostantól megadhatja a "cloudowner" felhasználó [jogosultságait](escalate-private-cloud-privileges.md#de-escalate-privileges) .

## <a name="ongoing-management-of-your-horizon-solution"></a>A Horizon-megoldás folyamatos felügyelete

Az AVS Private Cloud Environment teljes körű vezérléssel rendelkezik a Horizon és az App Volume Manager szoftverrel kapcsolatban, és a szükséges szoftveres életciklus-kezelést kell végrehajtania. A horizont vagy az alkalmazás kötetének frissítése vagy frissítése előtt győződjön meg arról, hogy a szoftver minden új verziója kompatibilis az AVS Private Cloud vCenter és a PSC-vel.
