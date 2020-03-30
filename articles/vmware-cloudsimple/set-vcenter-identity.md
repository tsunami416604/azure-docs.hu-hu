---
title: Azure VMware-megoldás a CloudSimple által – VCenter-identitásforrások beállítása magánfelhőben
description: A szolgáltatás azt ismerteti, hogy miként állítható be a Private Cloud vCenter az Active Directory a VMware-rendszergazdák számára a vCenter eléréséhez való hitelesítéshez
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5355e43ca6ac075e76a76ceb51be135cf4b62b0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564023"
---
# <a name="set-up-vcenter-identity-sources-to-use-active-directory"></a>VCenter-identitásforrások beállítása az Active Directory használatára

## <a name="about-vmware-vcenter-identity-sources"></a>A VMware vCenter identitásforrásai

A VMware vCenter különböző identitásforrásokat támogat a vCenterhez hozzáférő felhasználók hitelesítéséhez.  A CloudSimple Private Cloud vCenter beállítható úgy, hogy hitelesítse magát az Active Directoryval a VMware-rendszergazdák számára a vCenter eléréséhez. Ha a telepítés befejeződött, a **felhőtulajdonos** felhasználó hozzáadhat felhasználókat az identitásforrásból a vCenterhez.  

Az Active Directory tartományt és a tartományvezérlőket az alábbi módokon állíthatja be:

* A helyszínen futó Active Directory-tartomány és tartományvezérlők
* Az Azure-on virtuális gépként futó Active Directory-tartomány- és tartományvezérlők az Azure-előfizetésben
* A magánfelhőben futó új Active Directory-tartomány és tartományvezérlők
* Azure Active Directory szolgáltatás

Ez az útmutató ismerteti az Active Directory-tartomány és a helyszíni vagy virtuális gépként az előfizetésekben futó tartományvezérlők beállításának feladatait.  Ha az Azure AD-t szeretné identitásforrásként használni, tekintse meg az [Azure AD használata a vCenter identitásszolgáltatójaként a CloudSimple private cloud szolgáltatásban](azure-ad.md) című részletes útmutatást az identitásforrás beállításához.

[Identitásforrás hozzáadása](#add-an-identity-source-on-vcenter)előtt ideiglenesen [bővítse a vCenter-jogosultságokat.](escalate-private-cloud-privileges.md)

> [!CAUTION]
> Új felhasználókat csak a *Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group*, *Cloud-Global-Network-Admin-Group* vagy *cloud-global-vm-admin-group csoporthoz*kell hozzáadni.  A *Rendszergazdák* csoportba hozzáadott felhasználók automatikusan törlődnek.  Csak a szolgáltatásfiókokat kell hozzáadni *a Rendszergazdák* csoporthoz, és a szolgáltatásfiókok nem használhatók a vSphere webes felhasználói felületére való bejelentkezéshez.   


## <a name="identity-source-options"></a>Identitásforrás-beállítások

* [Helyszíni Active Directory hozzáadása egyetlen bejelentkezési identitásforrásként](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)
* [Új Active Directory beállítása magánfelhőben](#set-up-new-active-directory-on-a-private-cloud)
* [Az Active Directory beállítása az Azure-ban](#set-up-active-directory-on-azure)

## <a name="add-on-premises-active-directory-as-a-single-sign-on-identity-source"></a>Helyszíni Active Directory hozzáadása egyszeri bejelentkezési identitásforrásként

Ha a helyszíni Active Directoryt egyetlen bejelentkezési identitásforrásként szeretné beállítani, a következőkre van szükség:

* [Helyek közötti VPN-kapcsolat](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) a helyszíni adatközpontból a magánfelhőbe.
* A helyszíni DNS-kiszolgáló IP-címe hozzáadva a vCenter és a Platform Services Controller (PSC) szolgáltatáshoz.

Az Active Directory-tartomány beállításakor használja az alábbi táblázatban található információkat.

| **Beállítás** | **Leírás** |
|------------|-----------------|
| **Név** | Az identitásforrás neve. |
| **Alap DN felhasználók számára** | A felhasználók megkülönböztető neve. |
| **Tartománynév** | A tartomány teljes tartományszáma, például example.com. Ebben a szövegmezőben ne adjon meg IP-címet. |
| **Tartomány aliasa** | A tartomány NetBIOS-neve. SSPI-hitelesítések használata esetén adja hozzá az Active Directory tartomány NetBIOS-nevét az identitásforrás aliasaként. |
| **Csoportok alap DN-jén** | A csoportok alap megkülönböztető neve. |
| **Elsődleges kiszolgáló URL-címe** | Elsődleges tartományvezérlő LDAP-kiszolgálója a tartományhoz.<br><br>Használja a `ldap://hostname:port`  `ldaps://hostname:port`formátumot vagy a lehetőséget. A port általában 389 LDAP-kapcsolatok és 636 LDAPS kapcsolatok. Az Active Directory többtartományvezérlős telepítései esetén a port általában 3268 ldap és 3269 LDAPS esetén.<br><br>Az elsődleges vagy másodlagos LDAP-URL-címhez való használathoz olyan `ldaps://` tanúsítvány szükséges, amely megbízhatóságot hoz létre az Active Directory-kiszolgáló LDAPS-végpontja számára. |
| **Másodlagos kiszolgáló URL-címe** | A feladatátvételhez használt másodlagos tartományvezérlő LDAP-kiszolgálójának címe. |
| **Tanúsítvány kiválasztása** | Ha az LDAPS-t az Active Directory LDAP-kiszolgálóval vagy az OpenLDAP-kiszolgáló `ldaps://` identitásforrásával szeretné használni, az URL-szövegmezőbe történő beírás után megjelenik a Tanúsítvány kiválasztása gomb. Másodlagos URL-cím nem szükséges. |
| **Felhasználónév** | A tartomány azon felhasználójának azonosítója, aki legalább írásvédett hozzáféréssel rendelkezik a felhasználók és csoportok alap dn-jéhez. |
| **Jelszó** | A Felhasználónév által megadott felhasználó jelszava. |

Ha rendelkezik az előző táblában szereplő adatokkal, hozzáadhatja a helyszíni Active Directoryt egyetlen bejelentkezési identitásforrásként a vCenteren.

> [!TIP]
> Az egyszeri bejelentkezés identitásforrásairól a [VMware dokumentációs oldalán](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.psc.doc/GUID-B23B1360-8838-4FF2-B074-71643C4CB040.html)talál további információt.

## <a name="set-up-new-active-directory-on-a-private-cloud"></a>Új Active Directory beállítása magánfelhőben

Beállíthat egy új Active Directory-tartományt a magánfelhőben, és használhatja azt az egyszeri bejelentkezés identitásforrásaként.  Az Active Directory tartomány lehet egy meglévő Active Directory-erdő része, vagy független erdőként is beállítható.

### <a name="new-active-directory-forest-and-domain"></a>Új Active Directory erdő és tartomány

Új Active Directory erdő és tartomány beállításához a következőkre van szükség:

* Egy vagy több, Microsoft Windows Server rendszert futtató virtuális gép az új Active Directory erdő és tartomány tartományvezérlőjeként való használatra.
* Egy vagy több, DNS-szolgáltatást futtató virtuális gép névfeloldásra.

A részletes lépésekről a [Windows Server 2012 Active Directory erdő telepítése](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-) című témakörben található.

> [!TIP]
> A szolgáltatások magas rendelkezésre állása érdekében azt javasoljuk, hogy több tartományvezérlőt és DNS-kiszolgálót is be kell jelölni.

Az Active Directory erdő és tartomány beállítása után [hozzáadhat egy identitásforrást az](#add-an-identity-source-on-vcenter) új Active Directory vCenter szolgáltatásához.

### <a name="new-active-directory-domain-in-an-existing-active-directory-forest"></a>Új Active Directory-tartomány meglévő Active Directory-erdőben

Ha új Active Directory-tartományt szeretne beállítani egy meglévő Active Directory-erdőben, a következőkre van szükség:

* Helyek közötti VPN-kapcsolat az Active Directory erdőhelyével.
* DNS-kiszolgáló a meglévő Active Directory-erdő nevének feloldásához.

A részletes lépésekről a [Windows Server 2012 Active Directory gyermek- vagy fatartományának telepítése](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-child-or-tree-domain--level-200-) című témakörben található.

Az Active Directory tartomány beállítása után [hozzáadhat egy identitásforrást az](#add-an-identity-source-on-vcenter) új Active Directory vCenter szolgáltatásához.

## <a name="set-up-active-directory-on-azure"></a>Az Active Directory beállítása az Azure-ban

Az Azure-ban futó Active Directory hasonló a helyszíni futó Active Directoryhoz.  Az Active Directory azure-beli futtatásához egyetlen bejelentkezési identitásforrásként a vCenter-en, a vCenter-kiszolgáló és a PSC hálózati kapcsolattal kell rendelkeznie az Azure virtuális hálózathoz, ahol az Active Directory-szolgáltatások futnak.  Ezt a kapcsolatot az Azure virtuális hálózati kapcsolat használatával az [ExpressRoute használatával](azure-expressroute-connection.md) hozhatja létre az Azure virtuális hálózatáról, ahol az Active Directory-szolgáltatások a CloudSimple private cloud szolgáltatásra futnak.

A hálózati kapcsolat létrejötte után kövesse a [Helyszíni Active Directory hozzáadása egyetlen bejelentkezési identitásforrásként](#add-on-premises-active-directory-as-a-single-sign-on-identity-source) című, a helyszíni bejelentkezési azonosító forrásként című részben leírt lépéseket, és adja hozzá identitásforrásként.  

## <a name="add-an-identity-source-on-vcenter"></a>Identitásforrás hozzáadása a vCenteren

1. [A jogosultságok továbbmélyítése](escalate-private-cloud-privileges.md) a privát felhőben.

2. Jelentkezzen be a privát felhőhöz való vCenterbe.

3. Válassza **a Kezdőlap > felügyelet lehetőséget.**

    ![Adminisztráció](media/OnPremAD01.png)

4. Válassza **az Egyszeri bejelentkezés > konfiguráció lehetőséget.**

    ![Egyszeri bejelentkezés](media/OnPremAD02.png)

5. Nyissa **meg** az Identitásforrások **+** lapot, és kattintson ide egy új identitásforrás hozzáadásához.

    ![Identitásforrások](media/OnPremAD03.png)

6. Válassza az **Active Directory LDAP-kiszolgálóként** lehetőséget, majd kattintson a **Tovább**gombra.

    ![Active Directory](media/OnPremAD04.png)

7. Adja meg a környezet identitásforrás-paramétereit, majd kattintson a **Tovább**gombra.

    ![Active Directory](media/OnPremAD05.png)

8. Tekintse át a beállításokat, és kattintson a **Befejezés gombra.**
