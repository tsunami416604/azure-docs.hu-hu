---
title: Azure VMware-megoldás a CloudSimple-től – az Azure AD használata identitásforrásként a magánfelhőben
description: Ez a témakör azt ismerteti, hogy miként veheti fel az Azure AD-t identitásszolgáltatóként a CloudSimple magánfelhőjéhez, hogy hitelesítse a CloudSimple-t az Azure-ból elérőket elérő felhasználókat
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 674ca8bea110d60557d1e50e7b68c9c3f7a92bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564584"
---
# <a name="use-azure-ad-as-an-identity-provider-for-vcenter-on-cloudsimple-private-cloud"></a>Az Azure AD használata identitásszolgáltatóként a vCenterhez a CloudSimple private cloud szolgáltatásban

Beállíthatja a CloudSimple Private Cloud vCenter hitelesítésére az Azure Active Directory (Azure AD) a VMware rendszergazdák számára a vCenter eléréséhez. Az egyszeri bejelentkezési identitásforrás beállítása után a **felhőtulajdonos** felhasználó hozzáadhat felhasználókat az identitásforrásból a vCenterhez.  

Az Active Directory tartományt és a tartományvezérlőket az alábbi módokon állíthatja be:

* A helyszínen futó Active Directory-tartomány és tartományvezérlők
* Az Azure-on virtuális gépként futó Active Directory-tartomány- és tartományvezérlők az Azure-előfizetésben
* A CloudSimple magánfelhőben futó új Active Directory-tartomány- és tartományvezérlők
* Azure Active Directory szolgáltatás

Ez az útmutató ismerteti az Azure AD identitásforrásként való beállításához szükséges feladatokat.  A helyszíni Active Directory vagy az Azure-ban futó Active Directory használatával kapcsolatos további tudnivalókért tekintse meg a [vCenter-identitásforrások beállítása az Active Directory használatához](set-vcenter-identity.md) című részletes útmutatást az identitásforrás beállításához.

## <a name="about-azure-ad"></a>Az Azure AD ismertetése

Az Azure AD a Microsoft több-bérlős, felhőalapú címtár- és identitáskezelési szolgáltatása.  Az Azure AD méretezhető, konzisztens és megbízható hitelesítési mechanizmust biztosít a felhasználók számára az Azure különböző szolgáltatásainak hitelesítéséhez és eléréséhez.  Emellett biztonságos LDAP-szolgáltatásokat is biztosít bármely külső szolgáltatások számára az Azure AD hitelesítési/identitásforrásként való használatához.  Az Azure AD egyesíti az alapvető címtárszolgáltatásokat, a fejlett identitás-szabályozást és az alkalmazáshozzáférés-kezelést, amely a magánfelhőhöz való hozzáférés megadására használható a magánfelhőt felügyelő felhasználók számára.

Az Azure AD identitásforrásként való használatához be kell állítania az Azure AD és az Azure AD tartományi szolgáltatásait. Kövesse az alábbi utasításokat:

1. [Az Azure AD és az Azure AD tartományi szolgáltatásainak beállítása](#set-up-azure-ad-and-azure-ad-domain-services)
2. [Identitásforrás beállítása a Private Cloud vCenter szolgáltatásban](#set-up-an-identity-source-on-your-private-cloud-vcenter)

## <a name="set-up-azure-ad-and-azure-ad-domain-services"></a>Az Azure AD és az Azure AD tartományi szolgáltatásainak beállítása

Mielőtt elkezdene, globális rendszergazdai jogosultságokkal kell hozzáférnie az Azure-előfizetéséhez.  A következő lépések általános irányelveket adnak. A részleteket az Azure dokumentációja tartalmazza.

### <a name="azure-ad"></a>Azure AD

> [!NOTE]
> Ha már rendelkezik az Azure AD-vel, kihagyhatja ezt a szakaszt.

1. Állítsa be az Azure AD-t az előfizetésén az [Azure AD dokumentációjában](../active-directory/fundamentals/get-started-azure-ad.md)leírtak szerint.
2. Engedélyezze az Azure Active Directory Premium szolgáltatást az előfizetésén a [Regisztráció az Azure Active Directory prémium verzióra](../active-directory/fundamentals/active-directory-get-started-premium.md)című dokumentumban leírtak szerint.
3. Állítson be egy egyéni tartománynevet, és ellenőrizze az egyéni tartománynevet az [Egyéni tartománynév hozzáadása az Azure Active Directoryhoz](../active-directory/fundamentals/add-custom-domain.md)című részben leírtak szerint.
    1. Állítson be egy DNS-rekordot a tartományregisztrálón az Azure-ban megadott adatokkal.
    2. Állítsa be, hogy az egyéni tartománynév legyen az elsődleges tartomány.

Igény szerint konfigurálhat más Azure AD-funkciókat is.  Ezek nem szükségesek a vCenter-hitelesítés engedélyezéséhez az Azure AD-vel.

### <a name="azure-ad-domain-services"></a>Azure AD tartományi szolgáltatások

> [!NOTE]
> Ez egy fontos lépés az Azure AD a vCenter identitásforrásaként való engedélyezéséhez.  A problémák elkerülése érdekében győződjön meg arról, hogy minden lépést helyesen hajtott végre.

1. Engedélyezze az Azure AD tartományi szolgáltatásait az [Azure Active Directory tartományi szolgáltatásainak engedélyezése az Azure Portalon.](../active-directory-domain-services/active-directory-ds-getting-started.md)
2. Állítsa be azt a hálózatot, amelyet az Azure AD tartományi szolgáltatásai az [Azure Active Directory tartományi szolgáltatások engedélyezése az Azure Portalon keresztül című](../active-directory-domain-services/active-directory-ds-getting-started-network.md)részben leírtak szerint fognak használni.
3. Konfigurálja a rendszergazdai csoportot az Azure AD tartományi szolgáltatások kezeléséhez [az Azure Active Directory tartományi szolgáltatások engedélyezése az Azure Portalon című](../active-directory-domain-services/active-directory-ds-getting-started-admingroup.md)dokumentumban leírtak szerint.
4. Frissítse az Azure AD tartományi szolgáltatások DNS-beállításait az [Azure Active Directory tartományi szolgáltatások engedélyezése](../active-directory-domain-services/active-directory-ds-getting-started-dns.md)című részben leírtak szerint.  Ha az interneten keresztül szeretne csatlakozni az AD-hez, állítsa be az Azure AD tartományi szolgáltatások nyilvános IP-címének DNS-rekordját a tartománynévhez.
5. A jelszókivonat-szinkronizálás engedélyezése a felhasználók számára.  Ez a lépés lehetővé teszi az NT LAN-kezelőhöz (NTLM) és az Azure AD tartományi szolgáltatásokkerberos-hitelesítéséhez szükséges jelszókivétek szinkronizálását. A jelszókivonat-adatok szinkronizálásának beállítása után a felhasználók a vállalati hitelesítő adataikkal jelentkezhetnek be a felügyelt tartományba. Lásd: [Jelszókivonat-szinkronizálás engedélyezése az Azure Active Directory tartományi szolgáltatásokkal.](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
    1. Ha csak felhőalapú felhasználók vannak jelen, módosítaniuk kell a jelszavukat az <a href="http://myapps.microsoft.com/" target="_blank">Azure AD hozzáférési panelen,</a> hogy a jelszókivét az NTLM vagy a Kerberos által megkövetelt formátumban tárolják.  Kövesse a [Jelszókivonat-szinkronizálás engedélyezése a felügyelt tartománnyal a csak felhőalapú felhasználói fiókok esetén](../active-directory-domain-services/tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)című útmutatóutasításait.  Ezt a lépést az egyes felhasználók és minden olyan új felhasználó esetében kell elvégezni, aki az Azure Portalon vagy az Azure AD PowerShell-parancsmagok használatával jön létre az Azure AD-címtárban. Azoknak a felhasználóknak, akiknek hozzáférésre van szükségük az Azure AD tartományi szolgáltatásokhoz, az <a href="http://myapps.microsoft.com/" target="_blank">Azure AD hozzáférési panelt</a> kell használniuk, és a jelszó módosításához hozzá kell férniük a profiljukhoz.

        > [!NOTE]
        > Ha a szervezet rendelkezik csak felhőalapú felhasználói fiókokkal, az Active Directory Domain Servicest használó összes felhasználónak módosítania kell a jelszavát. A csak felhőalapú felhasználói fiókok olyan fiókok, amelyek az Azure AD-címtárban lettek létrehozva az Azure Portal vagy Azure AD PowerShell-parancsmagok használatával. Az ilyen felhasználói fiókok nem a helyszíni címtárból szinkronizálódnak.

    2. Ha a jelszavakat a helyszíni Active directoryból szinkronizálja, kövesse az [Active Directory dokumentációjában](../active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)leírt lépéseket.

6.  Konfigurálja a biztonságos LDAP-t az Azure Active Directory tartományi szolgáltatásokon az [Azure AD tartományi szolgáltatások által felügyelt tartomány biztonságos ldap-jának konfigurálása című](../active-directory-domain-services/tutorial-configure-ldaps.md)részben leírtak szerint.
    1. Töltsön fel egy tanúsítványt a biztonságos LDAP általi használatra az Azure-témakörben leírtak [szerint, hogy szerezzen be egy tanúsítványt a biztonságos LDAP-hoz.](../active-directory-domain-services/tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap)  A CloudSimple azt javasolja, hogy egy hitelesítésszolgáltató által kiállított aláírt tanúsítványt használjon annak biztosítására, hogy a vCenter megbízik a tanúsítványban.
    2. Biztonságos LDAP engedélyezése a leírtaknak [megfelelően: Biztonságos LDAP (LDAPS) engedélyezése egy Azure AD tartományi szolgáltatások által kezelt tartományban.](../active-directory-domain-services/tutorial-configure-ldaps.md)
    3. Mentse a tanúsítvány nyilvános részét (a személyes kulcs nélkül) .cer formátumban a vCenter rel való használatra az identitásforrás konfigurálása közben.
    4. Ha internet-hozzáférésre van szükség az Azure AD tartományi szolgáltatásokhoz, engedélyezze a "Biztonságos hozzáférés engedélyezése az LDAP-hoz az interneten keresztül" beállítást.
    5. Adja hozzá a bejövő biztonsági szabályt az Azure AD tartományi szolgáltatások NSG a TCP-port 636.

## <a name="set-up-an-identity-source-on-your-private-cloud-vcenter"></a>Identitásforrás beállítása a Private Cloud vCenter en

1. A privát felhőbeli vCenter [jogosultságai eszkalálódnak.](escalate-private-cloud-privileges.md)
2. Gyűjtse össze az identitásforrás beállításához szükséges konfigurációs paramétereket.

    | **Beállítás** | **Leírás** |
    |------------|-----------------|
    | **Név** | Az identitásforrás neve. |
    | **Alap DN felhasználók számára** | A felhasználók megkülönböztető neve.  Az Azure AD `OU=AADDC Users,DC=<domain>,DC=<domain suffix>` esetében `OU=AADDC Users,DC=cloudsimplecustomer,DC=com`használja a következőt: Példa: .|
    | **Tartománynév** | A tartomány teljes tartományszáma, például example.com. Ebben a szövegmezőben ne adjon meg IP-címet. |
    | **Tartomány aliasa** | *(nem kötelező)* A tartomány NetBIOS-neve. SSPI-hitelesítések használata esetén adja hozzá az Active Directory tartomány NetBIOS-nevét az identitásforrás aliasaként. |
    | **Csoportok alap DN-jén** | A csoportok alap megkülönböztető neve. Az Azure AD,use: `OU=AADDC Users,DC=<domain>,DC=<domain suffix>` Példa:`OU=AADDC Users,DC=cloudsimplecustomer,DC=com`|
    | **Elsődleges kiszolgáló URL-címe** | Elsődleges tartományvezérlő LDAP-kiszolgálója a tartományhoz.<br><br>A formátum `ldaps://hostname:port`használata . A port általában 636 LDAPS-kapcsolatok esetén. <br><br>Az elsődleges vagy másodlagos LDAP-URL-címhez való használathoz olyan `ldaps://` tanúsítvány szükséges, amely megbízhatóságot hoz létre az Active Directory-kiszolgáló LDAPS-végpontja számára. |
    | **Másodlagos kiszolgáló URL-címe** | A feladatátvételhez használt másodlagos tartományvezérlő LDAP-kiszolgálójának címe. |
    | **Tanúsítvány kiválasztása** | Ha az LDAPS-t az Active Directory LDAP-kiszolgálóval vagy az OpenLDAP-kiszolgáló `ldaps://` identitásforrásával szeretné használni, az URL-szövegmezőbe történő beírás után megjelenik a Tanúsítvány kiválasztása gomb. Másodlagos URL-cím nem szükséges. |
    | **Felhasználónév** | A tartomány azon felhasználójának azonosítója, aki legalább írásvédett hozzáféréssel rendelkezik a felhasználók és csoportok alap dn-jéhez. |
    | **Jelszó** | A Felhasználónév által megadott felhasználó jelszava. |

3. Jelentkezzen be a privát felhőbeli vCenterbe, miután a jogosultságok eszkalálódnak.
4. Kövesse az utasításokat [egy identitásforrás hozzáadása a vCenter](set-vcenter-identity.md#add-an-identity-source-on-vcenter) az előző lépés ben az Azure Active Directory identitásforrásként való beállításához.
5. Felhasználók/csoportok hozzáadása az Azure AD-ből a vCenter-csoportokhoz a VMware témakörben leírtak [szerint: Tagok hozzáadása egy vCenter single sign-on csoporthoz.](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)

> [!CAUTION]
> Új felhasználókat csak a *Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group*, *Cloud-Global-Network-Admin-Group* vagy *cloud-global-vm-admin-group csoporthoz*kell hozzáadni.  A *Rendszergazdák* csoportba hozzáadott felhasználók automatikusan törlődnek.  Csak szolgáltatásfiókokat kell hozzáadni a *Rendszergazdák* csoporthoz.

## <a name="next-steps"></a>További lépések

* [További információ a magánfelhő engedélymodelljéről](learn-private-cloud-permissions.md)
