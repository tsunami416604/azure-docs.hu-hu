---
title: Oktatóanyag – Az LDAPS konfigurálása az Azure Active Directory tartományi szolgáltatásokhoz | Microsoft dokumentumok
description: Ebben az oktatóanyagban megtudhatja, hogyan konfigurálhatja a biztonságos lightweight Directory access protokollt (LDAPS) egy Azure Active Directory tartományi szolgáltatások felügyelt tartományához.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 636f2e6139ad081d1e2fc67462a74cb7e18e3ff0
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475859"
---
# <a name="tutorial-configure-secure-ldap-for-an-azure-active-directory-domain-services-managed-domain"></a>Oktatóanyag: Biztonságos LDAP konfigurálása egy Azure Active Directory tartományi szolgáltatások felügyelt tartományához

Az Azure Active Directory tartományi szolgáltatásokkal (Azure AD DS) felügyelt tartománnyal való kommunikációhoz a Lightweight Directory Access Protocol (LDAP) használatos. Alapértelmezés szerint az LDAP-forgalom nincs titkosítva, ami számos környezetben biztonsági szempont. Az Azure AD DS segítségével konfigurálhatja a felügyelt tartományt a biztonságos Lightweight Directory Access Protocol (LDAPS) használatára. Biztonságos LDAP használata esetén a forgalom titkosítva lesz. A Secure LDAP más néven LDAP over Secure Sockets Layer (SSL) / Transport Layer Security (TLS).

Ez az oktatóanyag bemutatja, hogyan konfigurálhatja az LDAPS-t egy Azure AD DS felügyelt tartományhoz.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Digitális tanúsítvány létrehozása az Azure AD DS szolgáltatással való használatra
> * Biztonságos LDAP engedélyezése az Azure AD DS szolgáltatáshoz
> * Biztonságos LDAP konfigurálása nyilvános interneten keresztüli használatra
> * Biztonságos LDAP kötése és tesztelése Egy Azure AD DS felügyelt tartományhoz

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag végrehajtásához a következő erőforrásokra és jogosultságokra van szükség:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Az előfizetéshez társított Azure Active Directory-bérlő, amely et egy helyszíni könyvtárral vagy egy csak felhőbeli könyvtárral szinkronizált.
    * Szükség esetén [hozzon létre egy Azure Active Directory-bérlőt,][create-azure-ad-tenant] vagy [társítson egy Azure-előfizetést a fiókjához.][associate-azure-ad-tenant]
* Az Azure Active Directory tartományi szolgáltatások felügyelt tartomány a konfigurált és konfigurált az Azure AD-bérlő.
    * Szükség esetén [hozzon létre és konfiguráljon egy Azure Active Directory tartományi szolgáltatások példányát.][create-azure-ad-ds-instance]
* A számítógépre telepített *LDP.exe* eszköz.
    * Szükség esetén telepítse az Active Directory tartományi szolgáltatások hoz és az LDAP szolgáltatáshoz szükséges [RSAT][rsat] *-t.*

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Ebben az oktatóanyagban konfigurálja a biztonságos LDAP az Azure AD DS felügyelt tartomány az Azure Portalon keresztül. Első lépésekhez először jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

## <a name="create-a-certificate-for-secure-ldap"></a>Tanúsítvány létrehozása a biztonságos LDAP-hoz

A biztonságos LDAP használatához a kommunikáció titkosításához digitális tanúsítvány t használunk. Ez a digitális tanúsítvány az Azure AD DS felügyelt tartományára vonatkozik, és lehetővé teszi, hogy az Olyan eszközök, mint az *LDP.exe* biztonságos titkosított kommunikációt használjanak az adatok lekérdezésekekén. A felügyelt tartományhoz való biztonságos LDAP-hozzáféréshez kétféleképpen hozhat létre tanúsítványt:

* Nyilvános hitelesítésszolgáltató (CA) vagy vállalati hitelesítésszolgáltató tanúsítványa.
    * Ha a szervezet tanúsítványokat kap egy nyilvános hitelesítésszolgáltatótól, a biztonságos LDAP-tanúsítványt az adott nyilvános hitelesítésszolgáltatótól szerezheti be. Ha vállalati hitelesítésszolgáltatót használ a szervezetben, a biztonságos LDAP-tanúsítványt a vállalati hitelesítésszolgáltatótól szerezheti be.
    * A nyilvános hitelesítésszolgáltató csak akkor működik, ha egyéni DNS-nevet használ az Azure AD DS felügyelt tartományával. Ha a felügyelt tartomány DNS-tartományneve *.onmicrosoft.com*végződik, nem hozhat létre digitális tanúsítványt az alapértelmezett tartománnyal való kapcsolat védelmére. A Microsoft rendelkezik a *.onmicrosoft.com* tartománnyal, így a nyilvános hitelesítésszolgáltató nem állít ki tanúsítványt. Ebben az esetben hozzon létre egy önaláírt tanúsítványt, és használja, hogy a biztonságos LDAP konfigurálásához.
* Ön által aláírt tanúsítvány, amelyet saját maga hoz létre.
    * Ez a megközelítés jó tesztelési célokra, és ez az, amit ez a bemutató mutatja.

A kért vagy létrehozott tanúsítványnak meg kell felelnie az alábbi követelményeknek. A felügyelt tartomány problémákba ütközik, ha érvénytelen tanúsítvánnyal engedélyezi a biztonságos LDAP-t:

* **Megbízható kibocsátó** – A tanúsítványt a felügyelt tartományhoz biztonságos LDAP-val csatlakozó számítógépek által megbízhatónak helyezett hatóságnak kell kiállítania. Ez a jogosultsági szolgáltató lehet nyilvános hitelesítésszolgáltató vagy a számítógépek által megbízhatónak nevezett vállalati hitelesítésszolgáltató.
* **Élettartam** - A tanúsítványnak legalább a következő 3-6 hónapig érvényesnek kell lennie. A felügyelt tartományhoz való biztonságos LDAP-hozzáférés megszakad, amikor a tanúsítvány lejár.
* **Tulajdonos neve** – A tanúsítványon lévő tulajdonos névnek a felügyelt tartománynak kell lennie. Ha például a tartomány neve *aaddscontoso.com,* a tanúsítvány tulajdonosának **.aaddscontoso.com*-nek kell lennie.
    * A DNS-név vagy a tulajdonos alternatív neve a tanúsítvány kell egy helyettesítő tanúsítványt annak érdekében, hogy a biztonságos LDAP megfelelően működik az Azure AD tartományi szolgáltatások. A tartományvezérlők véletlenszerű neveket használnak, és eltávolíthatók vagy hozzáadhatók, hogy a szolgáltatás elérhető maradjon.
* **Kulcshasználat** – A tanúsítványt digitális aláírásokhoz és *kulcstitkosításhoz kell konfigurálni.* *digital signatures*
* **Tanúsítvány célja** – A tanúsítványnak érvényesnek kell lennie a TLS-kiszolgáló hitelesítéséhez.

Számos eszköz áll rendelkezésre, hogy hozzon létre önaláírt tanúsítvány, mint például OpenSSL, Keytool, MakeCert, [New-SelfSignedCertificate][New-SelfSignedCertificate] parancsmag stb. Ebben az oktatóanyagban hozzon létre egy önaláírt tanúsítványt a biztonságos LDAP-hoz az [Új-SelfSignedCertificate][New-SelfSignedCertificate] parancsmag használatával. Nyisson meg egy PowerShell-ablakot **rendszergazdaként,** és futtassa a következő parancsokat. Cserélje le a *$dnsName* változót a saját felügyelt tartománya által használt DNS-névre, például *a aaddscontoso.com:*

```powershell
# Define your own DNS name used by your Azure AD DS managed domain
$dnsName="aaddscontoso.com"

# Get the current date to set a one-year expiration
$lifetime=Get-Date

# Create a self-signed certificate for use with Azure AD DS
New-SelfSignedCertificate -Subject *.$dnsName `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName
```

A következő példa kimeneti kimenetazt mutatja, hogy a tanúsítvány sikeresen létrejött, és a helyi tanúsítványtárolóban *(LocalMachine\MY):*

```output
PS C:\WINDOWS\system32> New-SelfSignedCertificate -Subject *.$dnsName `
>>   -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
>>   -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName.com

   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\MY

Thumbprint                                Subject
----------                                -------
959BD1531A1E674EB09E13BD8534B2C76A45B3E6  CN=aaddscontoso.com
```

## <a name="understand-and-export-required-certificates"></a>A szükséges tanúsítványok ismertetése és exportálása

A biztonságos LDAP használatához a hálózati forgalom nyilvános kulcsú infrastruktúra (PKI) használatával titkosítva van.

* Egy **személyes** kulcs az Azure AD DS felügyelt tartományban alkalmazott.
    * Ez a személyes kulcs a biztonságos LDAP-forgalom *visszafejtésére* szolgál. A személyes kulcsot csak az Azure AD DS felügyelt tartományra kell alkalmazni, és nem kell széles körben terjeszteni az ügyfélszámítógépekre.
    * A titkos kulcsot tartalmazó tanúsítvány *a. PFX* fájlformátum.
* A **rendszer nyilvános** kulcsot alkalmaz az ügyfélszámítógépekre.
    * Ez a nyilvános kulcs a biztonságos LDAP-forgalom *titkosítására* szolgál. A nyilvános kulcs terjeszthető az ügyfélszámítógépekre.
    * A személyes kulcs nélküli tanúsítványok *a. CER* fájlformátumot.

Ez a két kulcs, a *személyes* és *a nyilvános* kulcsok biztosítják, hogy csak a megfelelő számítógépek tudjanak sikeresen kommunikálni egymással. Ha nyilvános hitelesítésszolgáltatót vagy vállalati hitelesítésszolgáltatót használ, olyan tanúsítványt kap, amely tartalmazza a személyes kulcsot, és alkalmazható egy Azure AD DS felügyelt tartományra. A nyilvános kulcsot már ismerniük kell és megbízhatónak kell lenniük az ügyfélszámítógépeknek. Ebben az oktatóanyagban önaláírt tanúsítványt hozott létre a személyes kulccsal, ezért exportálnia kell a megfelelő magán- és nyilvános összetevőket.

### <a name="export-a-certificate-for-azure-ad-ds"></a>Tanúsítvány exportálása az Azure AD DS szolgáltatáshoz

Mielőtt az előző lépésben létrehozott digitális tanúsítványt az Azure AD DS felügyelt tartományával használhatna, exportálja a tanúsítványt egy *alkalmazásba. A* személyes kulcsot tartalmazó PFX-tanúsítványfájl.

1. A *Futtatás* párbeszédpanel megnyitásához jelölje ki a **Windows** + **R** billentyűket.
1. Nyissa meg a Microsoft Management Console (MMC) konzolt úgy, hogy beírja az **mmc parancsot** a *Futtatás* párbeszédpanelre, majd kattintson az **OK gombra.**
1. A **Felhasználói fiókok felügyelete** parancsában válassza az **Igen** lehetőséget az MMC rendszergazdaként való elindításához.
1. A **Fájl** menüben válassza a **Beépülő modul hozzáadása/eltávolítása parancsot...**
1. A **Tanúsítványok beépülő modul varázslóban** válassza a **Számítógépfiók (Számítógép fiók)** lehetőséget, majd a **Tovább**gombot.
1. A **Számítógép kiválasztása** lapon válassza a **Helyi számítógép: (a konzolon futó számítógép)** lehetőséget, majd válassza a **Befejezés gombot.**
1. A **Beépülő modulok hozzáadása vagy eltávolítása** párbeszédpanelen válassza az **OK** gombot a tanúsítványbeépülő modul MMC-hez való hozzáadásához.
1. Az MMC ablakban bontsa ki a **Konzolgyökér csomópontot.** Válassza **a Tanúsítványok (helyi számítógép)** lehetőséget, majd bontsa ki a **Személyes** csomópontot, majd a **Tanúsítványok** csomópontot.

    ![A személyes tanúsítványok tárolójának megnyitása a Microsoft Management Console-ban](./media/tutorial-configure-ldaps/open-personal-store.png)

1. Megjelenik az előző lépésben létrehozott önaláírt tanúsítvány, például *a aaddscontoso.com.* Jelölje ki jobb oldali ezt a tanúsítványt, majd válassza **az Összes feladat > exportálása parancsot...**

    ![Tanúsítvány exportálása a Microsoft Management Console-ban](./media/tutorial-configure-ldaps/export-cert.png)

1. A **Tanúsítványexportáló varázslóban**válassza a **Tovább**lehetőséget.
1. A tanúsítvány személyes kulcsát exportálni kell. Ha a személyes kulcs nem szerepel az exportált tanúsítványban, a felügyelt tartomány biztonságos LDAP-jának engedélyezését lehetővé tevő művelet sikertelen lesz.

    A **Személyes kulcs exportálása** lapon válassza az **Igen, exportálja a személyes kulcsot**, majd kattintson a **Tovább**gombra.
1. Az Azure AD DS által kezelt tartományok csak *a. PFX* tanúsítványfájl formátuma, amely tartalmazza a személyes kulcsot. Ne exportálja a tanúsítványt *a következőképpen. CER* tanúsítványfájl formátuma a személyes kulcs nélkül.

    A **Fájlformátum exportálása** lapon válassza a **Személyes adatok cseréje - PKCS #12 (. PFX),** mint az exportált tanúsítvány fájlformátuma. Jelölje be az *Összes tanúsítvány felvétele a tanúsítványláncba jelölőnégyzetet, ha lehetséges:*

    ![Válassza a tanúsítvány exportálási lehetőségét a PKCS 12 (. PFX) fájlformátum](./media/tutorial-configure-ldaps/export-cert-to-pfx.png)

1. Mivel ez a tanúsítvány az adatok visszafejtésére szolgál, gondosan szabályozhatja a hozzáférést. A tanúsítvány használatának védelmére jelszó használható. A megfelelő jelszó nélkül a tanúsítvány nem alkalmazható egy szolgáltatásra.

    A **Biztonság** lapon válassza a **Jelszó** lehetőséget a *védelméhez. PFX* tanúsítványfájl. Írja be és erősítse meg a jelszót, majd válassza a **Tovább gombot.** Ez a jelszó a következő szakaszban az Azure AD DS felügyelt tartomány biztonságos LDAP-engedélyezéséhez használható.
1. Az **Exportáláshoz fájl** lapon adja meg azt a fájlnevet és helyet, ahová exportálni szeretné a tanúsítványt, például *C:\Users\accountname\azure-ad-ds.pfx*. Jegyezze fel a jelszavát és a *helyét. PFX* fájlt, mivel erre az információra a következő lépésekben lenne szükség.
1. A véleményezési lapon válassza a **Befejezés** lehetőséget a tanúsítvány *exportálásához. PFX* tanúsítványfájl. A tanúsítvány sikeres exportálásakor egy megerősítő párbeszédpanel jelenik meg.
1. Hagyja nyitva az MMC-t a következő szakaszban való használatra.

### <a name="export-a-certificate-for-client-computers"></a>Tanúsítvány exportálása ügyfélszámítógépekhez

Az ügyfélszámítógépeknek meg kell bízniuk a biztonságos LDAP-tanúsítvány kibocsátójában ahhoz, hogy sikeresen csatlakozhassanak a felügyelt tartományhoz az LDAPS használatával. Az ügyfélszámítógépeknek tanúsítványra van szükségük az Azure AD DS által visszafejtett adatok sikeres titkosításához. Ha nyilvános hitelesítésszolgáltatót használ, a számítógépnek automatikusan meg kell bíznia ezekben a tanúsítványkiállítókban, és rendelkeznie kell a megfelelő tanúsítvánnyal. Ebben az oktatóanyagban önaláírt tanúsítványt használ, és létrehozott egy tanúsítványt, amely tartalmazza a személyes kulcsot az előző lépésben. Most exportáljuk, majd telepítsük az önaláírt tanúsítványt az ügyfélszámítógép megbízható tanúsítványtárolójába:

1. Lépjen vissza az MMC *tanúsítványok (helyi számítógép) > a személyes > tanúsítványok* tárolójára. Megjelenik az előző lépésben létrehozott önaláírt tanúsítvány, például *aaddscontoso.com.* Jelölje ki jobb oldali ezt a tanúsítványt, majd válassza **az Összes feladat > exportálása parancsot...**
1. A **Tanúsítványexportáló varázslóban**válassza a **Tovább**lehetőséget.
1. Mivel nincs szüksége az ügyfelek személyes kulcsára, a **Személyes kulcs exportálása** lapon válassza a **Nem lehetőséget, ne exportálja a személyes kulcsot**, majd válassza a **Tovább**gombot.
1. A **Fájlformátum exportálása** lapon válassza **a Base-64 kódolású X.509 (. CER)** az exportált tanúsítvány fájlformátumaként:

    ![Válassza a tanúsítvány exportálásának lehetőségét a Base-64 kódolású X.509 (. CER) fájlformátum](./media/tutorial-configure-ldaps/export-cert-to-cer-file.png)

1. Az **Exportáláshoz fájl** lapon adja meg azt a fájlnevet és helyet, ahová exportálni szeretné a tanúsítványt, például *C:\Users\accountname\azure-ad-ds-client.cer*.
1. A véleményezési lapon válassza a **Befejezés** lehetőséget a tanúsítvány *exportálásához. CER* tanúsítványfájl. A tanúsítvány sikeres exportálásakor egy megerősítő párbeszédpanel jelenik meg.

A *. A CER* tanúsítványfájl most már terjeszthető olyan ügyfélszámítógépekre, amelyeknek meg kell bízniuk a biztonságos LDAP-kapcsolatban az Azure AD DS felügyelt tartományával. Telepítsük a tanúsítványt a helyi számítógépre.

1. Nyissa meg a Fájlkezelőt, és keresse meg azt a helyet, ahová a fájlt *mentette. CER* tanúsítványfájl, például *C:\Users\accountname\azure-ad-ds-client.cer*.
1. Válassza ki a jobb oldali *lehetőséget. CER* tanúsítványfájl, majd válassza **a Tanúsítvány telepítése lehetőséget.**
1. A **Tanúsítványimportálás varázslóban**válassza a tanúsítvány *helyi számítógépen*való tárolását, majd válassza a **Tovább**gombot:

    ![Válassza a tanúsítvány importálásának lehetőségét a helyi számítógéptárolóba](./media/tutorial-configure-ldaps/import-cer-file.png)

1. Amikor a rendszer kéri, válassza az **Igen** lehetőséget, hogy a számítógép módosításokat hajtson végre.
1. Válassza a **tanúsítványtároló automatikus kiválasztását a tanúsítvány típusa alapján,** majd válassza a **Tovább**gombot.
1. A véleményezési lapon válassza a **Befejezés** lehetőséget a lehetőség *importálásához. CER* tanúsítvány. fájl Egy megerősítő párbeszédpanel jelenik meg, ha a tanúsítvány importálása sikeresen megtörtént.

## <a name="enable-secure-ldap-for-azure-ad-ds"></a>Biztonságos LDAP engedélyezése az Azure AD DS szolgáltatáshoz

A létrehozott és exportált digitális tanúsítvány, amely tartalmazza a személyes kulcsot, és az ügyfélszámítógép beállítása, hogy megbízik a kapcsolat, most már biztonságos LDAP az Azure AD DS felügyelt tartományban. A biztonságos LDAP engedélyezéséhez egy Azure AD DS felügyelt tartományban hajtsa végre a következő konfigurációs lépéseket:

1. Az [Azure Portalon](https://portal.azure.com)adja meg a *tartományi szolgáltatásokat* az **erőforrások keresése** mezőbe. Válassza ki az **Azure AD tartományi szolgáltatások** a keresési eredményből.
1. Válassza ki a felügyelt tartományt, például *a aaddscontoso.com.*
1. Az Azure AD DS ablak bal oldalán válassza a **Biztonságos LDAP**lehetőséget.
1. Alapértelmezés szerint a felügyelt tartományhoz való biztonságos LDAP-hozzáférés le van tiltva. Kapcsolja be a **biztonságos LDAP-t** az **engedélyezéshez.**
1. A felügyelt tartomány biztonságos LDAP-hozzáférése az interneten keresztül alapértelmezés szerint le van tiltva. Ha engedélyezi a nyilvános biztonságos LDAP-hozzáférést, a tartomány ki van téve a jelszó brute force támadások az interneten keresztül. A következő lépésben egy hálózati biztonsági csoport úgy van beállítva, hogy csak a szükséges forrás IP-címtartományokhoz zárolja a hozzáférést.

    Váltás: **Biztonságos LDAP-hozzáférés engedélyezése az interneten keresztül az** **engedélyezéshez.**

1. Jelölje ki a mappaikont a **mellett. PFX fájl biztonságos LDAP tanúsítvánnyal.** Tallózással keresse meg a elérési *útját. PFX-fájlt,* majd jelölje ki a személyes kulcsot tartalmazó előző lépésben létrehozott tanúsítványt.

    Atanúsítvány-követelményekről szóló előző szakaszban látható módon nem használható az alapértelmezett *.onmicrosoft.com* tartománysal rendelkező nyilvános hitelesítésszolgáltató tanúsítványa. A Microsoft rendelkezik a *.onmicrosoft.com* tartománnyal, így a nyilvános hitelesítésszolgáltató nem állít ki tanúsítványt. Ellenőrizze, hogy a tanúsítvány megfelelő formátumban van-e. Ha nem, az Azure platform tanúsítványérvényesítési hibákat hoz létre, ha engedélyezi a biztonságos LDAP.If it't,if it't, the Azure platform generates certificate validation errors when you enable secure LDAP.

1. Adja meg a **visszafejtéshez szükséges jelszót. A PFX-fájl** egy előző lépésben lett beállítva, amikor a tanúsítványt egy *. PFX* fájlt.
1. A biztonságos LDAP engedélyezéséhez válassza a **Mentés** lehetőséget.

    ![Biztonságos LDAP engedélyezése egy Azure AD DS által felügyelt tartományhoz az Azure Portalon](./media/tutorial-configure-ldaps/enable-ldaps.png)

Megjelenik egy értesítés arról, hogy a felügyelt tartományhoz biztonságos LDAP van konfigurálva. A felügyelt tartomány egyéb beállításai nem módosíthatók, amíg ez a művelet be nem fejeződik.

A felügyelt tartomány biztonságos LDAP-jának engedélyezése néhány percet vesz igénybe. Ha a megadott biztonságos LDAP-tanúsítvány nem felel meg a szükséges feltételeknek, a felügyelt tartomány biztonságos LDAP-jának engedélyezését lehetővé tevő művelet sikertelen lesz. A hiba néhány gyakori oka az, ha a tartománynév helytelen, vagy ha a tanúsítvány hamarosan lejár, vagy már lejárt. A tanúsítványt újra létrehozhatja érvényes paraméterekkel, majd engedélyezheti a biztonságos LDAP-t ezzel a frissített tanúsítvánnyal.

## <a name="lock-down-secure-ldap-access-over-the-internet"></a>Biztonságos LDAP-hozzáférés zárolása az interneten keresztül

Ha engedélyezi a biztonságos LDAP-hozzáférést az interneten keresztül az Azure AD DS felügyelt tartományához, biztonsági fenyegetést hoz létre. A felügyelt tartomány a 636-os TCP-porton érhető el az internetről. Javasoljuk, hogy korlátozza a felügyelt tartományhoz való hozzáférést a környezetadott ismert IP-címekre. Az Azure hálózati biztonsági csoport szabály segítségével korlátozhatja a biztonságos LDAP-hoz való hozzáférés korlátozására.

Hozzunk létre egy szabályt, amely lehetővé teszi a bejövő biztonságos LDAP-hozzáférést a 636-os TCP-porton keresztül egy adott IP-címcsoportból. Az alapértelmezett *DenyAll* szabály alacsonyabb prioritású vonatkozik az összes többi bejövő forgalmat az internetről, így csak a megadott címek érhetik el az Azure AD DS felügyelt tartomány biztonságos LDAP használatával.

1. Az Azure Portalon válassza *erőforráscsoportok* a bal oldali navigációs.
1. Válassza ki az erőforráscsoportot, például *a myResourceGroup*csoportot, majd válassza ki a hálózati biztonsági csoportot, például *az aaads-nsg.*
1. Megjelenik a meglévő bejövő és kimenő biztonsági szabályok listája. A hálózati biztonsági csoport ablakainak bal oldalán válassza a **Beállítások > Bejövő biztonsági szabályok lehetőséget.**
1. Válassza **a Hozzáadás**lehetőséget, majd hozzon létre egy szabályt a *636-os* *TCP-port* engedélyezéséhez. A nagyobb biztonság érdekében válassza ki a *forrást IP-címként,* majd adja meg a szervezet saját érvényes IP-címét vagy tartományát.

    | Beállítás                           | Érték        |
    |-----------------------------------|--------------|
    | Forrás                            | IP-címek |
    | Forrás IP-címek / CIDR-tartományok | Érvényes IP-cím vagy -tartomány a környezethez |
    | Forrásporttartományok                | *            |
    | Cél                       | Bármelyik          |
    | Célporttartományok           | 636          |
    | Protocol (Protokoll)                          | TCP          |
    | Műveletek                            | Engedélyezés        |
    | Prioritás                          | 401          |
    | Név                              | AllowLDAPS   |

1. Ha készen áll, válassza a **Hozzáadás** lehetőséget a szabály mentéséhez és alkalmazásához.

    ![Hálózati biztonsági csoportszabály létrehozása az LDAPS-hozzáférés interneten keresztüli védelméhez](./media/tutorial-configure-ldaps/create-inbound-nsg-rule-for-ldaps.png)

## <a name="configure-dns-zone-for-external-access"></a>DNS-zóna konfigurálása külső hozzáféréshez

Ha biztonságos LDAP-hozzáférés engedélyezve van az interneten keresztül, frissítse a DNS-zónát, hogy az ügyfélszámítógépek megtalálják ezt a felügyelt tartományt. A *biztonságos LDAP külső IP-cím* az Azure AD DS felügyelt **tartományának Tulajdonságok** lapján található:

![Az Azure AD DS által felügyelt tartomány biztonságos LDAP-külső IP-címének megtekintése az Azure Portalon](./media/tutorial-configure-ldaps/ldaps-external-ip-address.png)

Állítsa be a külső DNS-szolgáltatót úgy, hogy hozzon létre egy állomásrekordot, például *az ldaps-t,* hogy feloldja ezt a külső IP-címet. Ha először helyileg szeretné tesztelni a számítógépet, létrehozhat egy bejegyzést a Windows hosts fájlban. A hosts fájl helyi számítógépen való sikeres szerkesztéséhez nyissa meg rendszergazdaként a *Jegyzettömböt,* majd nyissa meg a *C:\Windows\System32\drivers\etc fájlt.*

A következő példa a DNS-bejegyzés , akár a külső DNS-szolgáltató, vagy a helyi hosts fájlban, feloldja a forgalmat *ldaps.aaddscontoso.com* a külső IP-cím *168.62.205.103*:

```
168.62.205.103    ldaps.aaddscontoso.com
```

## <a name="test-queries-to-the-managed-domain"></a>Lekérdezések tesztelése a felügyelt tartományba

Az Azure AD DS felügyelt tartományához való csatlakozáshoz és az LDAP-n való kereséshez használja az *LDP.exe* eszközt. Ez az eszköz a Távoli kiszolgálófelügyeleti eszközök (RSAT) csomag részét képezi. További információt a Távoli kiszolgálófelügyeleti eszközök telepítése című [témakörben talál.][rsat]

1. Nyissa meg *az LDP.exe-t,* és csatlakozzon a felügyelt tartományhoz. Válassza **a Kapcsolat**lehetőséget, majd a **Csatlakozás...** lehetőséget.
1. Adja meg az előző lépésben létrehozott felügyelt tartomány biztonságos LDAP DNS-tartománynevét, például *a ldaps.aaddscontoso.com.* A biztonságos LDAP használatához állítsa a **Port** *636-ra,* majd jelölje be az **SSL**jelölőnégyzetet.
1. A felügyelt tartományhoz való csatlakozáshoz válassza az **OK gombot.**

Ezután kösse össze az Azure AD DS felügyelt tartományához. A felhasználók (és a szolgáltatásfiókok) nem hajthatnak végre LDAP-alapú egyszerű kötéseket, ha letiltotta az NTLM jelszókivonat-szinkronizálást az Azure AD DS-példányon. Az NTLM jelszókivonat-szinkronizálás letiltásáról az [Azure AD DS által felügyelt tartomány biztonságossá tétele című][secure-domain]témakörben talál további információt.

1. Válassza a **Kapcsolat** menüt, majd a **Kötés...** parancsot.
1. Adja meg az *AAD tartományvezérlő rendszergazdái* csoporthoz tartozó felhasználói fiók hitelesítő adatait, például *a contosoadmin.* Írja be a felhasználói fiók jelszavát, majd írja be a tartományt, például *aaddscontoso.com*.
1. A **Kötés típus mezőben**válassza a *Kötés hitelesítő adatokkal*lehetőséget.
1. Válassza **az OK lehetőséget** az Azure AD DS felügyelt tartományához való kötéshez.

Az Azure AD DS felügyelt tartományában tárolt objektumok megtekintése:

1. Válassza a **Nézet** menüt, majd a **Fa**lehetőséget.
1. Hagyja üresen a *BaseDN* mezőt, majd kattintson **az OK gombra.**
1. Válasszon egy tárolót, például *az AADDC-felhasználók at,* majd a jobb szélen jelölje ki a tárolót, és válassza a **Keresés parancsot.**
1. Hagyja meg az előre kitöltött mezőket, majd válassza a **Futtatás lehetőséget.** A lekérdezés eredményei a jobb oldali ablakban jelennek meg, ahogy az a következő példa kimenetben látható:

    ![Objektumok keresése az Azure AD DS felügyelt tartományában az LDP.exe használatával](./media/tutorial-configure-ldaps/ldp-query.png)

Ha közvetlenül le szeretne kérdezni egy adott tárolót, a **> fa megtekintése** menüből megadhat egy **BaseDN-t,** például *OU=AADDC Users,DC=AADDSCONTOSO,DC=COM* vagy *OU=AADDC Computers,DC=AADDSCONTOSO,DC=COM*. A lekérdezések formázásáról és létrehozásáról az [LDAP-lekérdezés alapjai][ldap-query-basics]című témakörben talál további információt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha dns-bejegyzést adott hozzá a számítógép helyi hosts fájljához az oktatóanyag hoz való kapcsolódás teszteléséhez, távolítsa el ezt a bejegyzést, és adjon hozzá egy hivatalos rekordot a DNS-zónában. Ha el szeretné távolítani a bejegyzést a helyi hosts fájlból, hajtsa végre az alábbi lépéseket:

1. A helyi számítógépen nyissa meg a *Jegyzettömbet* rendszergazdaként
1. Tallózással keresse meg és nyissa meg a *C:\Windows\System32\drivers\etc* fájlt
1. A hozzáadott rekord sorának törlése, például`168.62.205.103    ldaps.aaddscontoso.com`

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Digitális tanúsítvány létrehozása az Azure AD DS szolgáltatással való használatra
> * Biztonságos LDAP engedélyezése az Azure AD DS szolgáltatáshoz
> * Biztonságos LDAP konfigurálása nyilvános interneten keresztüli használatra
> * Biztonságos LDAP kötése és tesztelése Egy Azure AD DS felügyelt tartományhoz

> [!div class="nextstepaction"]
> [Jelszókivonat-szinkronizálás konfigurálása hibrid Azure AD-környezethez](tutorial-configure-password-hash-sync.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[secure-domain]: secure-your-domain.md

<!-- EXTERNAL LINKS -->
[rsat]: /windows-server/remote/remote-server-administration-tools
[ldap-query-basics]: /windows/desktop/ad/creating-a-query-filter
[New-SelfSignedCertificate]: /powershell/module/pkiclient/new-selfsignedcertificate
