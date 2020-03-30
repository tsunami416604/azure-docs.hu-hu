---
title: Azure VMware-megoldás a CloudSimple-től – A Magánfelhő webhely használata virtuális asztali infrastruktúra üzemeltetéséhez a VMware Horizon használatával
description: Ez a témakör azt ismerteti, hogy a CloudSimple private cloud webhelyhasználatával hogyan üzemeltethet virtuális asztali infrastruktúrát a VMware Horizon használatával
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 723821a78ecae308443c93567402e3b232c036f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025248"
---
# <a name="use-cloudsimple-private-cloud-site-to-host-a-virtual-desktop-infrastructure-using-vmware-horizon"></a>Virtuális asztali infrastruktúra üzemeltetéséhez a CloudSimple private cloud webhelyhasználatával a VMware Horizon használatával

A CloudSimple private cloud webhelyével virtuális asztali infrastruktúrát (VDI) üzemeltethet a VMware Horizon 7.x használatával. Az alábbi ábra a VDI logikai megoldásarchitektúráját mutatja be.

![A Horizont telepítése](media/horizon-deployment.png)

Ezzel a megoldással teljes mértékben szabályozhatja a Horizon View Manager t és az alkalmazáskötetet. A jól ismert felhasználói felület, API és CLI felületek lehetővé teszik a meglévő parancsfájlok és eszközök használatát.

A CloudSimple megoldás megköveteli, hogy tegye a következőket:

* Telepítse, konfigurálja és kezelje a VMware Horizon 7.x alkalmazást a magánfelhőben.
* Adja meg saját Horizon-licenceit.

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

Az alábbi szakaszok ismertetik, hogyan telepíthet egy VDI-megoldást a Horizon használatával a magánfelhőben.

1. [Annak ellenőrzése, hogy a VMware termékverziói kompatibilisek-e](#verify-that-vmware-product-versions-are-compatible)
2. [Az asztali környezet méretének becslése](#estimate-the-size-of-your-desktop-environment)
3. [Privát felhő létrehozása a környezetéhez](#create-a-private-cloud-for-your-environment)
4. [Telepítse a VMware Horizon alkalmazást a magánfelhőbe](#install-vmware-horizon-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Annak ellenőrzése, hogy a VMware termékverziói kompatibilisek-e

* Ellenőrizze, hogy a Horizon, az App Volumes, az Unified Access Gateway és a User Environment Manager jelenlegi és tervezett verziói kompatibilisek-e egymással, valamint a magánfelhőben lévő vCenter és PSC szolgáltatással. A kompatibilitásról a [Horizont 7.5 VMware kompatibilitási mátrixában talál.](https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&260=2877&0=)
* Ha meg szeretné tudni a vCenter és a PSC aktuális verzióit a magánfelhőben, nyissa meg a [CloudSimple portál erőforrásait,](access-cloudsimple-portal.md)válassza ki a magánfelhőt, és kattintson a **vSphere Felügyeleti hálózat** fülre. **Resources**

![a vCenter- és a PSC-verziók](media/private-cloud-vsphere-versions.png)

### <a name="estimate-the-size-of-your-desktop-environment"></a>Az asztali környezet méretének becslése

* Ellenőrizze, hogy az azonosított konfiguráció a VMware működési határain belül van-e.
* Becsülje meg az összes asztalhoz és a Horizon felügyeleti összetevőhöz szükséges erőforrásokat.

### <a name="create-a-private-cloud-for-your-environment"></a>Privát felhő létrehozása a környezetéhez

1. Hozzon létre egy magánfelhőt a CloudSimple portálról a [Magánfelhő környezet konfigurálása](quickstart-create-private-cloud.md)című útmutató utasításait követve.  A CloudSimple létrehoz egy alapértelmezett "felhőtulajdonos" nevű vCenter-felhasználót minden újonnan létrehozott magánfelhőben. Az alapértelmezett private cloud-felhasználóról és engedélymodellről a [Magánfelhő-engedélyek modell megismerése című témakörben](learn-private-cloud-permissions.md)olvashat részletesen.
2. Hozzon létre egy VLAN-t a privát felhőben a Horizon felügyeleti síkhoz, és rendeljen hozzá egy alhálózatCIDR-t. További információt a [VLAN-ok/alhálózatok létrehozása és kezelése című témakörben talál.](create-vlan-subnet.md) Ez az a hálózat, ahol az összes megoldás-összetevő (Unified Access Gateway, Connection Server, App Volume Server és User Environment Manager kiszolgáló) telepítve lesz.
3. Döntse el, hogy külső identitásszolgáltatót kíván-e használni a privát felhőbeli vCenterrel. Ha igen, válasszon az alábbi lehetőségek közül:
    * Használja a helyszíni Active Directoryt külső identitásszolgáltatóként. További információt a [vCenter identitásforrások ban](set-vcenter-identity.md)talál.
    * Állítson be egy Active Directory-kiszolgálót a Magánfelhőben a Horizon felügyeleti síkVLAN-ban, amelyet külső identitásszolgáltatóként használhat. További információt a [vCenter identitásforrások ban](set-vcenter-identity.md)talál.
    * Állítson be egy DHCP- és DNS-kiszolgálót a Horizon felügyeleti sík VLAN szolgáltatásában a magánfelhőben. További információt a [DNS- és DHCP-alkalmazások és számítási feladatok beállítása a CloudSimple magánfelhőben című témakörben talál.](dns-dhcp-setup.md)
4. Konfigurálja a DNS-továbbítást a magánfelhőben telepített DNS-kiszolgálón. További információt a [Feltételes továbbító létrehozása](on-premises-dns-setup.md#create-a-conditional-forwarder)című témakörben talál.

### <a name="install-vmware-horizon-in-your-private-cloud"></a>Telepítse a VMware Horizon alkalmazást a magánfelhőbe

Az alábbi üzembe helyezési diagram egy privát felhőben üzembe helyezett Horizon-megoldást ábrázol. Az egyesített hozzáférésátjáró, az AD/DC, a Nézet és az Alkalmazáskötet-kiszolgáló a felhasználó által létrehozott VLAN 234-ben van telepítve. Az Egyesített hozzáférési átjáró hoz egy hozzárendelt nyilvános IP-címet, amely elérhető az internetről. A Horizon asztali medencés virtuális gépek a VLAN 235-ben vannak telepítve, hogy további elkülönítést és biztonságot nyújtson.

![A Horizon üzembe helyezése a magánfelhőben](media/horizon-private-cloud.png)

A következő szakaszok ismertetik az utasításokat, hogy hozzanak létre egy központi telepítés hasonló az ábrán látható. Mielőtt elkezdené, ellenőrizze, hogy rendelkezik-e a következőkkel:

* A CloudSimple portál használatával létrehozott magánfelhő, amely elegendő kapacitással rendelkezik az asztali készletek futtatásához.
* Elegendő sávszélesség a helyszíni környezet és a privát felhő környezet között az asztali számítógépek hálózati forgalmának támogatásához.
* A helyszíni adatközpont és a magánfelhő között létrehozott helyek közötti VPN-alagút.
* AZ IP-elérhetőség a helyszíni környezetben lévő végfelhasználói alhálózatoktól a CloudSimple private cloud alhálózatokig.
* AD/DHCP/DNS telepítve a magánfelhőhöz.

#### <a name="cloudsimple-portal-create-a-dedicated-vlansubnet-for-desktop-pools"></a>CloudSimple portál: Hozzon létre egy dedikált VLAN/alhálózatot az asztali medencék számára

Hozzon létre egy VLAN-t a Horizon asztali készletekhez, és rendeljen hozzá egy alhálózatCIDR-t. További információt a [VLAN-ok/alhálózatok létrehozása és kezelése című témakörben talál.](create-vlan-subnet.md) Ez az a hálózat, ahol az összes asztali virtuális gép futni fog.

A Horizon telepítésének biztonságossá tétele érdekében kövesse a szokásos biztonsági gyakorlati tanácsokat:

* Csak asztali RDP-forgalom / SSH-forgalom engedélyezése az asztali virtuális gépekre.
* Csak felügyeleti forgalmat engedélyezhet a Horizon felügyeleti sík VLAN és az asztali VLAN medence között.
* Csak a helyszíni hálózatról engedélyezi a felügyeleti forgalmat.

Ezeket az ajánlott eljárásokat a CloudSimple [portáltűzfal-szabályok](firewall.md) konfigurálásával kényszerítheti ki.

#### <a name="cloudsimple-portal-configure-firewall-rules-to-secure-horizon-management-plane"></a>CloudSimple portál: Tűzfalszabályok konfigurálása a Horizon felügyeleti sík védelmére

Állítsa be a következő szabályokat a CloudSimple portálon. További információt a [Tűzfaltáblák és -szabályok beállítása (Tűzfaltáblák és -szabályok) beállítása témakörben talál.](firewall.md)

1. Konfigurálja a tűzfalszabályokat a CloudSimple N-S tűzfalban, hogy lehetővé tegye a helyszíni alhálózatok és a Horizon felügyeleti VLAN közötti kommunikációt, hogy csak a VMware dokumentum [Horizon portlistájában](https://docs.vmware.com/en/VMware-Horizon-7/7.1/com.vmware.horizon-client-agent.security.doc/GUID-52807839-6BB0-4727-A9C7-EA73DE61ADAB.html) felsorolt hálózati portok engedélyezettek.

2. Hozzon létre e-W tűzfalszabályokat a Horizon felügyeleti VLAN és az asztali VLAN között a privát felhőben.

#### <a name="cloudsimple-portal-create-a-public-ip-address-for-unified-access-gateway"></a>CloudSimple portál: Hozzon létre egy nyilvános IP-címet az egyesített hozzáférési átjáróhoz

Hozzon létre egy nyilvános IP-címet az Egyesített hozzáférési átjáró készülékhez, hogy engedélyezze az asztali ügyfélkapcsolatokat az internetről. További információt a [Nyilvános IP-címek lefoglalása](public-ips.md)című témakörben talál.

Ha a beállítás befejeződött, a nyilvános IP-cím hozzá van rendelve, és megjelenik a Nyilvános IP-oldalakon.

#### <a name="cloudsimple-portal-escalate-privileges"></a>CloudSimple portál: A jogosultságok eszkalációja

Az alapértelmezett "felhőtulajdonos" felhasználó nem rendelkezik megfelelő jogosultságokkal a Private Cloud vCenter ben a Horizon telepítéséhez, ezért a felhasználó vCenter-jogosultságait fokozni kell. További információ: [Jogosultságok elmélyítése](escalate-private-cloud-privileges.md).

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-horizon-installation"></a>vCenter felhasználói felület: Felhasználó létrehozása a Magánfelhőben a Horizon telepítéséhez

1. Jelentkezzen be a vCenterbe a "felhőtulajdonos" felhasználói hitelesítő adataival.
2. Hozzon létre egy új felhasználót, a "horizon-soln-admin"-t a vCenterben, és adja hozzá a felhasználót a rendszergazdák csoportjához a vCenterben.
3. Jelentkezzen ki a vCenter,mint a "felhőtulajdonos" felhasználó, és jelentkezzen be, mint a "horizon-soln-admin" felhasználó.

#### <a name="vcenter-ui-install-vmware-horizon"></a>vCenter UI: VMware Horizon telepítése

Akorábbi logikai architektúra szakaszban említettek szerint a Horizon-megoldás a következő összetevőkkel rendelkezik:

* VMware Horizon nézet
* VMware egyesített hozzáférési átjáró
* VMware alkalmazás kötetkezelője
* VMware felhasználói környezetkezelő

Telepítse az összetevőket az alábbiak szerint:

1. Telepítse és konfigurálja az Egyesített hozzáférési átjárót a VMware-dokumentumban található utasítások [szerint, a VMware unified Access Gateway telepítése és konfigurálása című dokumentumban.](https://docs.vmware.com/en/Unified-Access-Gateway/3.3.1/com.vmware.uag-331-deploy-config.doc/GUID-F5CE0D5E-BE85-4FA5-BBCF-0F86C9AB8A70.html)

2. Telepítse a Horizon View alkalmazást a magánfelhőben a [Telepítési útmutató megtekintése](https://docs.vmware.com/en/VMware-Horizon-7/7.4/horizon-installation/GUID-37D39B4F-5870-4188-8B11-B6C41AE9133C.html)című témakör utasításait követve.

3. Telepítse az Alkalmazáskötet-kezelőt a [VMware alkalmazáskötetek telepítése és konfigurálása](https://docs.vmware.com/en/VMware-App-Volumes/2.10/com.vmware.appvolumes.user.doc/GUID-5E8BAF8C-F5A6-412C-9424-266BA7109BA4.html)című témakör utasításainak követésével.

4. Telepítse és konfigurálja a Felhasználói környezetkezelőt a [VMware felhasználói környezetkezelő telepítéséről és konfigurálásáról című](https://docs.vmware.com/en/VMware-User-Environment-Manager/9.4/com.vmware.user.environment.manager-install-config/GUID-DBBC82E4-483F-4B28-9D49-4D28E08715BC.html)témakör utasításainak követésével.

#### <a name="file-a-support-request-to-upload-vmware-horizon-pre-packaged-app-volumes"></a>Nyújtson be támogatási kérelmet a VMware Horizon előre csomagolt alkalmazáskötetek feltöltéséhez

A telepítési folyamat részeként az App Volume Manager előre csomagolt köteteket használ az alkalmazáshalmok és írható kötetek kiépítéséhez. Ezek a kötetek az alkalmazáshalmok és írható kötetek sablonjaiként szolgálnak.

A kötetek feltöltése a Private Cloud adattárba az ESXi root jelszót igényel. Segítségért nyújtson be [támogatási kérelmet.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) Csatolja az AppVolumes telepítő csomagját, hogy a CloudSimple támogatási személyzete feltölthesse a sablonokat a privát felhőkörnyezetbe.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple portál: A jogosultságok eszkalációja

Most már csökkentheti a "felhőtulajdonos" felhasználó [jogosultságait.](escalate-private-cloud-privileges.md#de-escalate-privileges)

## <a name="ongoing-management-of-your-horizon-solution"></a>A Horizon megoldás folyamatos kezelése

Teljes hozzáféréssel rendelkezik a Horizon és az App Volume Manager szoftverei felett a privát felhőkörnyezetében, és elvárják, hogy elvégezze a szükséges szoftveréletciklus-kezelést. A Horizon vagy az Alkalmazáskötet frissítése vagy frissítése előtt győződjön meg arról, hogy a szoftver új verziói kompatibilisek a Private Cloud vCenter rel és a PSC-vel.
