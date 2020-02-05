---
title: Azure VMware Solutions (AVS) – vCenter-identitások beállítása az AVS Private Cloud szolgáltatásban
description: Leírja, hogyan állíthatja be az AVS Private Cloud vCenter, hogy hitelesítse magát Active Directory a VMware-rendszergazdák számára a vCenter eléréséhez
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ad4a7b2bc67b7d50d9e9a5f8337a09dbe77366ea
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014215"
---
# <a name="set-up-vcenter-identity-sources-to-use-active-directory"></a>VCenter-identitások beállítása a Active Directory használatára

## <a name="about-vmware-vcenter-identity-sources"></a>A VMware vCenter Identity sources

A VMware vCenter a vCenter-hez hozzáférő felhasználók hitelesítéséhez különböző identitás-forrásokat támogat. Az AVS Private Cloud-vCenter beállítható úgy, hogy a VMware-rendszergazdák Active Directory a vCenter eléréséhez. Ha a telepítés befejeződött, a **cloudowner** felhasználó hozzáadhat felhasználókat az Identity forrásból a vCenter. 

A Active Directory tartománya és tartományvezérlői a következő módokon állíthatók be:

* A helyszínen futó tartomány-és tartományvezérlők Active Directory
* Az Azure-ban virtuális gépekként működő tartomány-és tartományvezérlők Active Directory Azure-előfizetésében
* Új Active Directory tartomány-és tartományvezérlők, amelyek az AVS Private Cloud-ban futnak
* Azure Active Directory szolgáltatás

Ez az útmutató ismerteti azokat a feladatokat, amelyekkel Active Directory tartomány és tartományvezérlők állíthatók be a helyszíni vagy virtuális gépeken az előfizetésekben. Ha az Azure AD-t személyazonossági forrásként szeretné használni, tekintse [meg az Azure ad-t identitás-szolgáltatóként az vCenter az AVS Private Cloud](azure-ad.md) szolgáltatásban című témakört, amely részletes útmutatást nyújt az identitás forrásának beállításához.

[Az Identity forrás hozzáadása előtt átmenetileg megnövelheti](#add-an-identity-source-on-vcenter) [a vCenter-jogosultságokat](escalate-private-cloud-privileges.md).

> [!CAUTION]
> Az új felhasználókat csak a *Cloud-Owner-Group*, a *Cloud-Global-cluster-admin-Group*, a *Cloud-Global-Storage-admin-Group*, a *Cloud-Global-Network-admin-Group* vagy a *Cloud-Global-VM-admin-Group*szolgáltatáshoz kell hozzáadni.  A *rendszergazdák* csoportba felvett felhasználók automatikusan el lesznek távolítva.  A vSphere webes felhasználói felületén csak a szolgáltatási fiókokat kell felvenni a *rendszergazdák* csoportjába, és a szolgáltatásfiókok nem használhatók.   


## <a name="identity-source-options"></a>Személyazonossági forrás beállításai

* [Helyszíni Active Directory hozzáadása egyszeri bejelentkezési identitás forrásaként](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)
* [Új Active Directory beállítása egy AVS privát felhőben](#set-up-new-active-directory-on-an-avs-private-cloud)
* [Active Directory beállítása az Azure-ban](#set-up-active-directory-on-azure)

## <a name="add-on-premises-active-directory-as-a-single-sign-on-identity-source"></a>Helyszíni Active Directory hozzáadása egyszeri bejelentkezési identitás forrásaként

Ha a helyszíni Active Directory egyszeri bejelentkezési identitás forrásaként szeretné beállítani, a következőkre lesz szüksége:

* [Helyek közötti VPN-kapcsolat](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) a helyszíni adatközpontból az AVS Private-felhőbe.
* A helyszíni DNS-kiszolgáló IP-címe a vCenter és a platform Services-vezérlőhöz (PSC) lett hozzáadva.

A Active Directory tartományának beállításakor használja az alábbi táblázatban szereplő információkat.

| **Beállítás** | **Leírás** |
|------------|-----------------|
| **Name (Név)** | Az Identity forrás neve. |
| **A felhasználók alapszintű megkülönböztető neve** | A felhasználók alapszintű megkülönböztető neve. |
| **Tartománynév** | A tartomány FQDN, például example.com. Ne adjon meg IP-címet ebben a szövegmezőben. |
| **Tartomány aliasa** | A tartomány NetBIOS-neve. Adja hozzá a Active Directory tartomány NetBIOS-nevét az Identity forrás aliasként, ha az SSPI-hitelesítést használja. |
| **A csoportok alapszintű megkülönböztető neve** | A csoportok alapszintű megkülönböztető neve. |
| **Elsődleges kiszolgáló URL-címe** | A tartomány elsődleges tartományvezérlője LDAP-kiszolgálója.<br><br>Használja a `ldap://hostname:port` vagy `ldaps://hostname:port`formátumot. A port általában a 389 LDAP-kapcsolatokhoz és 636 for LDAPs-kapcsolatokhoz. Active Directory többtartományos tartományvezérlő üzembe helyezése esetén a port általában az LDAP-hez és a 3269-hoz 3268.<br><br>A Active Directory kiszolgáló LDAPs-végpontjának megbízhatóságát kiépítő tanúsítványra akkor van szükség, ha az elsődleges vagy a másodlagos LDAP URL-címben `ldaps://` használ. |
| **Másodlagos kiszolgáló URL-címe** | A feladatátvételhez használt másodlagos tartományvezérlői LDAP-kiszolgáló címe. |
| **Tanúsítvány kiválasztása** | Ha LDAPs-t szeretne használni a Active Directory LDAP-kiszolgálóval vagy a OpenLDAP-kiszolgáló identitási forrásával, akkor az URL-cím szövegmezőben a `ldaps://` beírása után a tanúsítvány választása gomb jelenik meg. Másodlagos URL-cím megadása nem kötelező. |
| **Felhasználónév** | Azon felhasználó azonosítója, aki legalább olvasási hozzáféréssel rendelkezik a felhasználók és csoportok alapszintű DN-hez. |
| **Jelszó** | A Felhasználónév által megadott felhasználó jelszava. |

Ha az előző táblázatban szereplő adatokkal rendelkezik, a helyszíni Active Directory egyszeri bejelentkezési identitásként adhatja hozzá a vCenter-on.

> [!TIP]
> A [VMware dokumentációs oldalán](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.psc.doc/GUID-B23B1360-8838-4FF2-B074-71643C4CB040.html)találhat további információt az egyszeri bejelentkezési azonosítók forrásairól.

## <a name="set-up-new-active-directory-on-an-avs-private-cloud"></a>Új Active Directory beállítása egy AVS privát felhőben

Beállíthat egy új Active Directory tartományt az AVS Private-felhőben, és az egyszeri bejelentkezéshez használható identitási forrásként. A Active Directory tartomány lehet egy meglévő Active Directory erdő része, vagy önálló erdőként is beállítható.

### <a name="new-active-directory-forest-and-domain"></a>Új Active Directory erdő és tartomány

Új Active Directory erdő és tartomány beállításához a következők szükségesek:

* Egy vagy több, a Microsoft Windows Servert futtató virtuális gép tartományvezérlőként való használatára az új Active Directory erdő és tartomány számára.
* Egy vagy több DNS-szolgáltatást futtató virtuális gép névfeloldáshoz.

A részletes lépésekért lásd: [új Windows Server 2012 Active Directory erdő telepítése](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-) .

> [!TIP]
> A szolgáltatások magas rendelkezésre állása érdekében ajánlott több tartományvezérlő és DNS-kiszolgáló beállítása.

A Active Directory erdő és tartomány beállítása után az új Active Directoryhoz [hozzáadhat egy vCenter az azonosítóhoz](#add-an-identity-source-on-vcenter) .

### <a name="new-active-directory-domain-in-an-existing-active-directory-forest"></a>Új Active Directory tartomány egy meglévő Active Directory erdőben

Új Active Directory tartomány meglévő Active Directory erdőben való beállításához a következőkre lesz szüksége:

* Helyek közötti VPN-kapcsolat a Active Directory erdő helyére.
* DNS-kiszolgáló a meglévő Active Directory erdő nevének feloldásához.

A részletes lépésekért lásd: [új Windows Server 2012 Active Directory gyermek-vagy fatartomány telepítése](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-child-or-tree-domain--level-200-) .

A Active Directory tartomány beállítása után az új Active Directoryhoz [hozzáadhat egy vCenter az identitás forrásához](#add-an-identity-source-on-vcenter) .

## <a name="set-up-active-directory-on-azure"></a>Active Directory beállítása az Azure-ban

Az Azure-on futó Active Directory hasonló a helyileg futó Active Directoryhoz. Ha az Azure-ban futó Active Directoryt egyszeri bejelentkezési vCenter szeretné beállítani, akkor a vCenter-kiszolgálónak és a PSC-nek hálózati kapcsolattal kell rendelkeznie az Azure Virtual Network, ahol Active Directory szolgáltatások futnak. Ezt a kapcsolatot az [azure Virtual Network-kapcsolattal](azure-expressroute-connection.md) is létrehozhatja az Azure Virtual Network ExpressRoute használatával, ahol a Active Directory szolgáltatások az AVS Private Cloud-hoz futnak.

A hálózati kapcsolatok létrejötte után kövesse a helyszíni [Active Directory hozzáadása egyszeri bejelentkezési identitásként](#add-on-premises-active-directory-as-a-single-sign-on-identity-source) című témakör lépéseit, és adja hozzá az azonosító forrásként. 

## <a name="add-an-identity-source-on-vcenter"></a>Identity forrás hozzáadása a vCenter

1. A [jogosultságok kiterjesztését](escalate-private-cloud-privileges.md) az AVS Private Cloud-ra.

2. Jelentkezzen be az AVS Private Cloud-vCenter.

3. Válassza a **kezdőlap > felügyelet**lehetőséget.

    ![Felügyelet](media/OnPremAD01.png)

4. Válassza **az egyszeri bejelentkezés > konfiguráció**lehetőséget.

    ![Egyszeri bejelentkezés](media/OnPremAD02.png)

5. Nyissa meg az **Identitáskezelés** lapot, és kattintson a **+** elemre egy új Identity forrás hozzáadásához.

    ![Azonosító források](media/OnPremAD03.png)

6. Válassza ki a **Active Directory LDAP-kiszolgálóként** , majd kattintson a **tovább**gombra.

    ![Active Directory](media/OnPremAD04.png)

7. Adja meg a környezet Identity Source paramétereit, majd kattintson a **tovább**gombra.

    ![Active Directory](media/OnPremAD05.png)

8. Tekintse át a beállításokat, majd kattintson a **Befejezés**gombra.
