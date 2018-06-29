---
title: Biztonságos LDAP (LDAPS) konfigurálása az Azure AD tartományi szolgáltatásokban |} Microsoft Docs
description: Biztonságos LDAP (LDAPS) konfigurálása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: maheshu
ms.openlocfilehash: 5838dbefab9f7100ed4776eebef7a1d07d2db1a6
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061045"
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Biztonságos LDAP (LDAPS) használatos az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz tartozó konfigurálása

## <a name="before-you-begin"></a>Előkészületek
Győződjön meg arról, hogy befejezte [2. feladat – a biztonságos LDAP tanúsítvány exportálása a. PFX-fájl](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md).


## <a name="task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal"></a>3. feladat – az Azure portál használatával a felügyelt tartomány számára biztonságos LDAP engedélyezése
Ahhoz, hogy a biztonságos LDAP, hajtsa végre az alábbi konfigurációs lépéseket:

1. Keresse meg a  **[Azure-portálon](https://portal.azure.com)**.

2. Keressen a "tartományi szolgáltatások" kifejezésre a **keresési erőforrások** keresőmezőbe. Válassza ki **Azure AD tartományi szolgáltatások** a keresési eredmény alapján. A **Azure AD tartományi szolgáltatások** lap felsorolja a felügyelt tartományok.

    ![Található felügyelt tartomány telepítése folyamatban](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Kattintson a nevére, a felügyelt tartomány (például "contoso100.com") a tartománnyal kapcsolatos további részletek megtekintéséhez.

    ![Tartományi szolgáltatások - üzembe helyezési állapota](./media/getting-started/domain-services-provisioning-state.png)

3. Kattintson a **biztonságos LDAP** a navigációs ablaktáblán.

    ![Tartományi szolgáltatások - biztonságos LDAP lapozható](./media/active-directory-domain-services-admin-guide/secure-ldap-blade.png)

4. Alapértelmezés szerint le van tiltva a felügyelt tartományra biztonságos LDAP-hozzáférés. Váltás **biztonságos LDAP** való **engedélyezése**.

    ![Biztonságos LDAP engedélyezése](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configure.png)
5. Alapértelmezés szerint le van tiltva az interneten keresztül a felügyelt tartományra biztonságos LDAP-hozzáférés. Váltás **biztonságos LDAP-hozzáférés engedélyezése az interneten keresztül** való **engedélyezése**, ha szükséges.

    > [!WARNING]
    > Biztonságos LDAP-hozzáférés engedélyezésével az interneten keresztül, a tartomány hajlamos jelszó találgatásos támadásokkal szemben az interneten keresztül. Ezért ajánlott egy NSG eléréséhez szükséges forrás IP-címtartományok zárolását beállítását. Az utasításokat lásd: [LDAPS hozzáférés az interneten keresztül a felügyelt tartományra zárolása](#task-5---lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet).
    >

6. Kattintson a mappa ikon következő **. Biztonságos LDAP tanúsítvány PFX-fájl**. Adja meg a PFX-fájl elérési útját a felügyelt tartományra biztonságos LDAP hozzáférést tanúsítványával.

7. Adja meg a **visszafejtéséhez szükséges jelszót. PFX-fájl**. Adja meg a tanúsítványt a PFX-fájlba exportálásakor használja ugyanazt a jelszót.

8. Amikor elkészült, kattintson a **mentése** gombra.

9. Megjelenik egy értesítés, amely arról értesíti, biztonságos LDAP tartozik a felügyelt tartomány számára. Amíg ez a művelet be nem fejeződik, a tartomány számára más beállításokat nem módosíthatja.

    ![A felügyelt tartomány számára biztonságos LDAP beállítása](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configuring.png)

> [!NOTE]
> Ahhoz, hogy a felügyelt tartományok biztonságos LDAP körülbelül 10 – 15 percet vesz igénybe. A megadott biztonságos LDAP-tanúsítvány nem felel meg a szükséges feltételeknek, ha biztonságos LDAP nincs engedélyezve a címtárban, és láthatja a hibát. Ha például a tartomány neve nem megfelelő, a tanúsítvány már lejárt vagy hamarosan lejár. Ebben az esetben próbálkozzon újra egy érvényes tanúsítványt.
>
>

<br>

## <a name="task-4---configure-dns-to-access-the-managed-domain-from-the-internet"></a>4. feladat – az internetről a felügyelt tartomány eléréséhez DNS konfigurálása
> [!NOTE]
> **Nem kötelező** – Ha nem tervezi, hogy a felügyelt tartományra LDAPS az interneten keresztül elérni, hagyja ki a konfigurációs feladat.
>
>

Ez a feladat megkezdése előtt győződjön meg arról, befejezte a leírt lépéseket [3. feladat](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal-preview).

Miután engedélyezte biztonságos LDAP hozzáférést az interneten keresztül a felügyelt tartományok, módosítania DNS, hogy a felügyelt tartomány található ügyfélszámítógépek számára. 3. feladat végén külső IP-cím jelenik meg a **tulajdonságok** lapján **külső IP-cím a LDAPS ELÉRÉST**.

Konfigurálja a külső DNS-szolgáltatónál, hogy a külső IP-címre mutat a felügyelt tartomány (például ldaps.contoso100.com) DNS-nevét. Hozzon létre például a következő DNS-bejegyzést:

    ldaps.contoso100.com  -> 52.165.38.113

Ennyi az egész – most már készen áll a felügyelt tartományra biztonságos LDAP az interneten keresztül csatlakozni.

> [!WARNING]
> Ne feledje, hogy az ügyfél számára megbízhatónak kell lennie a LDAPS tanúsítvány kiállítója tud sikeresen csatlakozni a felügyelt tartományra LDAPS kell. Ha egy nyilvánosan megbízható hitelesítésszolgáltató használ, nem kell semmit, mivel az ügyfélszámítógépek számára megbízhatónak ezek tanúsítványkiállítók. Ha önaláírt tanúsítványt használ, telepítse az önaláírt tanúsítvány nyilvános részét az ügyfélszámítógépen, a megbízható tanúsítványok tárolójába.
>
>


## <a name="task-5---lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet"></a>5. feladat – az interneten keresztül a felügyelt tartományok biztonságos LDAP hozzáférést zárolása
> [!NOTE]
> Ha nem engedélyezte a felügyelt tartományra LDAPS hozzáférés az interneten keresztül, hagyja ki a konfigurációs feladat.
>
>

Ez a feladat megkezdése előtt győződjön meg arról, befejezte a leírt lépéseket [3. feladat](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal-preview).

A felügyelt tartományok LDAPS hozzáférési közzéteszi az interneten keresztül jelenti a biztonsági kockázatot jelentenek. A felügyelt tartományra érhető el az interneten, a biztonságos LDAP használt port (Ez azt jelenti, hogy a 636-os port). Ezért ha szeretné, korlátozza a hozzáférést a felügyelt tartományra ismert IP-címek. A nagyobb biztonság érdekében hozzon létre egy hálózati biztonsági csoportot (NSG), és rendelje hozzá azt az alhálózatot, ha engedélyezte az Azure AD tartományi szolgáltatásokat.

Az alábbi táblázat mutatja be egy minta NSG-t is konfigurálhat, biztonságos LDAP hozzáférést zárolását az interneten keresztül. Az NSG tartalmaz egy TCP-porton keresztül 636 csak egy megadott készletből IP-címek bejövő biztonságos LDAP hozzáférést engedélyező szabályokat. Minden bejövő forgalom az internetről az alapértelmezett "DenyAll" szabály vonatkozik. Az NSG-szabály LDAPS hozzáférést a megadott IP-címeket az interneten keresztül rendelkezik, magasabb prioritású, mint a DenyAll NSG-szabály.

![Minta NSG LDAPS hozzáférés biztonságossá az interneten keresztül](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**További információ** - [hálózati biztonsági csoportok](../virtual-network/security-overview.md).

<br>

## <a name="bind-to-the-managed-domain-over-ldap-using-ldpexe"></a>LDAP használatával LDP.exe keresztül kötést létrehozni a felügyelt tartományhoz
Használhatja az LDP.exe eszközt, amely a Távoli kiszolgálófelügyelet eszközei csomag kötést létrehozni, és a keresés során LDAP tartalmazza.

Első lépésként nyissa meg az LDP, és a felügyelt tartományra kapcsolódjon. Kattintson a **kapcsolat** kattintson **Connect...**  a menüben. Adja meg a felügyelt tartomány DNS-tartomány nevét. Adja meg a kapcsolatokhoz használandó portot. Az LDAP-kapcsolatokhoz 389-es portot használja. LDAPS kapcsolatok 636-os port használatára. Kattintson a **OK** csatlakozás a felügyelt tartományra.

A következő kötési a felügyelt tartományra. Kattintson a **kapcsolat** kattintson **kötési...**  a menüben. Adja meg az "AAD DC rendszergazdák" csoportba tartozó felhasználói fiók hitelesítő adatait.

Válassza ki **nézet**, majd válassza ki **fa** a menüben. Az alap megkülönböztető név mező üresen hagyja, és kattintson az OK gombra. Nyissa meg a tároló, amelyet meg szeretne keresni, kattintson a jobb gombbal a tároló és válassza ki a keresés.

> [!TIP]
> - Felhasználók és az Azure Active Directoryból szinkronizált csoportok tárolódnak a **AADDC felhasználók** tároló. A keresési elérési út a tároló a következőképpen néz ```CN=AADDC\ Users,DC=CONTOSO100,DC=COM```.
> - Számítógépes fiókok a felügyelt tartományhoz csatlakozó számítógépek tárolják a **AADDC számítógépek** tároló. A keresési elérési út a tároló a következőképpen néz ```CN=AADDC\ Computers,DC=CONTOSO100,DC=COM```.
>
>

További információ - [LDAP-lekérdezés alapjai](https://technet.microsoft.com/library/aa996205.aspx)


## <a name="troubleshooting"></a>Hibaelhárítás
Ha sikerült csatlakozni a biztonságos LDAP a felügyelt tartományra, hajtsa végre a következő hibaelhárítási lépéseket:
* Győződjön meg arról, hogy a kibocsátó lánc biztonságos LDAP tanúsítvány megbízható-e az ügyfélen. Választhatja a legfelső szintű hitelesítésszolgáltató hozzáadása a megbízható főtanúsítvány-tárolóba való a megbízhatósági kapcsolat létrehozása az ügyfélen.
* Győződjön meg arról, hogy az LDAP-ügyfél (például az ldp.exe) csatlakozik-e a biztonságos LDAP végpont egy DNS-név, ne az IP-címet.
* Ellenőrizze az LDAP-ügyfél kapcsolódik a nyilvános IP-címre oldja fel a rendszer biztonságos LDAP a felügyelt tartomány DNS-nevét.
* Ellenőrizze, hogy a biztonságos LDAP a felügyelt tartományok tanúsítványnál a tulajdonos vagy a tulajdonos alternatív neve attribútum DNS-nevét.
* Ha az interneten keresztül biztonságos LDAP csatlakozik, győződjön meg arról, a virtuális hálózat NSG beállításainak engedélyezi a forgalom port: 636 az internetről érkező.

Ha továbbra is problémákat tapasztal a felügyelt tartományra biztonságos LDAP kapcsolódás [lépjen kapcsolatba a termékért felelős csoport](active-directory-ds-contact-us.md) segítségét. A probléma diagnosztizálásához jobban segítségével a következő információkat tartalmazza:
* A kapcsolat létrehozása és sikertelen ldp.exe képernyőképe.
* Az Azure AD-bérlő azonosítója, és a felügyelt tartományok DNS-tartomány nevét.
* Felhasználói-kötés kívánt nevet.


## <a name="related-content"></a>Kapcsolódó tartalom
* [Azure AD tartományi szolgáltatások – első lépések útmutató](active-directory-ds-getting-started.md)
* [Azure AD tartományi szolgáltatások által kezelt tartomány felügyelete](active-directory-ds-admin-guide-administer-domain.md)
* [LDAP-lekérdezés alapjai](https://technet.microsoft.com/library/aa996205.aspx)
* [Csoportházirend a egy Azure AD tartományi szolgáltatások által kezelt tartomány felügyelete](active-directory-ds-admin-guide-administer-group-policy.md)
* [Hálózati biztonsági csoportok](../virtual-network/security-overview.md)
* [Hálózati biztonsági csoport létrehozása](../virtual-network/tutorial-filter-network-traffic.md)
