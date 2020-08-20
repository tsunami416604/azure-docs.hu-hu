---
title: FortiGate üzembe helyezési útmutató | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és FortiGate SSL VPN között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 18a3d9d5-d81c-478c-be7e-ef38b574cb88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 999e19ffad1d18e163881c844cbf30f8b7fef574
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88658424"
---
# <a name="fortigate-deployment-guide"></a>FortiGate üzembe helyezési útmutató

## <a name="contents"></a>Tartalom

- A FortiGate-licenc beváltása
- Belső vezérlőprogram letöltése
- A FortiGate virtuális gép üzembe helyezése
   - Statuc nyilvános IP-cím beállítása és teljes tartománynév kiosztása
   - Új bejövő hálózati biztonsági csoportra vonatkozó szabály létrehozása a TCP-porthoz
- Egyéni Azure-alkalmazás létrehozása a FortiGate
- Csoport egyeztetésének előkészítése
   - Csoportok létrehozása felhasználók számára
- A FortiGate virtuális gép konfigurálása
   - A licenc telepítése
   - Belső vezérlőprogram frissítése
   - Felügyeleti port módosítása TCP-re
   - Az Azure Active Directory SAML aláíró tanúsítványának feltöltése
   - Egyéni SSL-tanúsítvány feltöltése és konfigurálása
   - Parancssori konfiguráció végrehajtása
   - VPN-portálok és tűzfal-házirend létrehozása
- Bejelentkezés tesztelése az Azure használatával

## <a name="redeeming-the-fortigate-license"></a>A FortiGate-licenc beváltása

A következő generációs Fortinet FortiGate virtuális gépként érhető el az Azure IaaS-ben. Ehhez a virtuális géphez két licencelési mód létezik –

- Használatalapú fizetés (PAYG)
- Saját licenc használata (BYOL)

A Fortinet és a biztonságos hibrid hozzáférési (SHA) útmutatás biztosításához a Fortinet az Azure AD-t az éles környezetbeli SHA csapatának biztosíthatja a licencekkel. Azokban az esetekben, amikor nincs megadva licenc, a TB üzembe helyezése is működni fog.

Olyan esetekben, amikor licencet adtak ki, a Fortinet olyan regisztrációs kódot biztosít, amelyet online kell beváltani

![FortiGate SSL VPN](registration-code.png)

1. Regisztrálás itt: https://support.fortinet.com/
2. Regisztráció után jelentkezzen be https://support.fortinet.com/
3. Navigáljon **az** adategységhez – > **regisztrálása/aktiválása**
4. Adja meg a Fortinet által megadott regisztrációs kódot
5. Adja meg a regisztrációs kódot, válassza ki **a terméket, amelyet a nem kormányzati felhasználó használ** majd, és kattintson a **tovább** gombra.
6. Adja meg a termék leírását (például FortiGate), állítsa be a Fortinet-partnert **más** -> **Microsoft** számára, és kattintson a **tovább** gombra.
7. Fogadja el a **Fortinet szerződést** , és kattintson a **tovább** gombra.
8. Fogadja el a **feltételeket** , és kattintson a **megerősítés** gombra.
9. Kattintson a **licencfájl letöltése** lehetőségre, és mentse a licencet később


## <a name="download-firmware"></a>Belső vezérlőprogram letöltése

A Fortinet FortiGate Azure-beli virtuális gép az írás időpontjában nem az SAML-hitelesítéshez szükséges belső vezérlőprogram-verziót szállítja. A legújabb verziót a Fortinet-ből kell beszerezni.

1. Bejelentkezés itt: https://support.fortinet.com/
2. Navigáljon a > **firmware-lemezképek** **letöltéséhez**
3. A **kiadási megjegyzések** jobb oldalán kattintson a **Download (Letöltés** ) gombra.
4. Kattintson az v6 elemre **.**
5. Kattintson a **6 gombra.**
6. Kattintson a **6,4 gombra.**
7. Töltse le **FGT_VM64_AZURE-v6-build1637-Fortinet. out** -t úgy, hogy az ugyanazon a sorban lévő **https** -hivatkozásra kattint.
8. A fájl mentése később


## <a name="deploy-the-fortigate-vm"></a>A FortiGate virtuális gép üzembe helyezése

1. Navigáljon, https://portal.azure.com és jelentkezzen be arra az előfizetésbe, amelyre telepíteni kívánja a FortiGate virtuális gépet
2. Hozzon létre egy új erőforráscsoportot, vagy nyissa meg azt az erőforráscsoportot, amelybe telepíteni kívánja a FortiGate virtuális gépet
3. Kattintson a **Hozzáadás** gombra
4. Adja meg a "Forti" **kifejezést a keresés a piactéren** párbeszédpanelen, és válassza a **Fortinet FortiGate következő** **generációs tűzfal** elemet.
5. Válassza ki a szoftvert (BYOL, ha nem rendelkezik licenccel vagy TB), majd kattintson a **Létrehozás** gombra.
6. A virtuális gép konfigurációjának feltöltése

    ![FortiGate SSL VPN](virtual-machine.png)

7. Állítsa be a hitelesítési típust a **jelszó** értékre, és adja meg a virtuális gép rendszergazdai hitelesítő adatait.
8. Kattintson a **felülvizsgálat + létrehozás** gombra.
9. Kattintson a **Létrehozás** gombra
10. Várjon, amíg befejeződik a virtuális gép üzembe helyezése


### <a name="set-a-statuc-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>Statuc nyilvános IP-cím beállítása és teljes tartománynév kiosztása

Az egységes felhasználói élmény érdekében érdemes beállítani a FortiGate virtuális géphez hozzárendelt nyilvános IP-címet statikusan hozzárendelve. Emellett a teljes tartománynévhez való leképezése is hasznos lehet ugyanezen okból.

_Statikus nyilvános IP-cím beállítása_

1. Navigáljon a https://portal.azure.com FortiGate virtuális géphez, és nyissa meg a beállításokat
2. Az **Áttekintés** képernyőn kattintson a nyilvános IP-cím elemre.

    ![FortiGate SSL VPN](public-ip-address.png)

3. Kattintson a **statikus** elemre, majd a **Mentés** gombra.

_Teljes tartománynév kiosztása_

Ha a FortiGate virtuális gép üzembe helyezéséhez nyilvánosan irányítható tartománynév tartozik a környezethez, hozzon létre egy állomás (A) rekordot a virtuális gép számára, amely a fentiekben statikusan hozzárendelt nyilvános IP-címhez van leképezve.

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port"></a>Új bejövő hálózati biztonsági csoportra vonatkozó szabály létrehozása a TCP-porthoz

1. Navigáljon a https://portal.azure.com FortiGate virtuális géphez, és nyissa meg a beállításokat
2. A bal oldali menüben kattintson a **hálózatkezelés** elemre. A hálózati adapter fel lesz sorolva, és megjelenik a bejövő port szabályai
3. Kattintson a **bejövő Port hozzáadása szabály** elemre.
4. Új bejövő portszabály létrehozása a 8443-as TCP-hez

    ![FortiGate SSL VPN](port-rule.png)

5. Kattintson a **Hozzáadás** gombra


## <a name="create-a-custom-azure-app-for-fortigate"></a>Egyéni Azure-alkalmazás létrehozása a FortiGate

1. Navigáljon a Azure Active Directory panelre, https://portal.azure.com és nyissa meg azt a bérlőt, amely a FortiGate-bejelentkezések identitását fogja biztosítani.
2. A bal oldali menüben kattintson a **vállalati alkalmazások** elemre.
3. Kattintson az **új alkalmazás** lehetőségre.
4. Kattintson a **nem Gallery-alkalmazás** lehetőségre
5. Adjon meg egy nevet (például FortiGate), és kattintson a **Hozzáadás** gombra.
6. Kattintson a **felhasználók és csoportok** elemre a bal oldali menüben
7. Adja hozzá a bejelentkezni kívánt felhasználókat, majd kattintson a **hozzárendelés** gombra.
8. Kattintson az **egyszeri bejelentkezés** lehetőségre a bal oldali menüben
9. Kattintson az **SAML** elemre
10. Az **alapszintű SAML-konfiguráció** területen kattintson a ceruza gombra a konfiguráció szerkesztéséhez.
11. Konfigurálás
    - Azonosító (entitás azonosítója) `https://<address>/remote/saml/metadata`
    - Válasz URL-címe (a fogyasztói szolgáltatás URL-címe)  `https://<address>/remote/saml/login`
    - Kijelentkezési URL-cím `https://<address>/remote/saml/logout`

    Hol `address` található a FortiGate virtuális géphez rendelt FQDN vagy nyilvános IP-cím

    Jegyezze fel ezeket az URL-címeket későbbi használatra –

    - Entitás azonosítója
    - Válasz URL-cím
    - Kijelentkezési URL-cím
12. Kattintson a **Mentés** gombra.
13. Az alapszintű SAML-konfiguráció lezárása
14. **3 – SAML aláíró tanúsítvány** , töltse le a **tanúsítványt (Base64)** , és mentse később
15. **4 – beállítás (alkalmazás neve)** alatt másolja az Azure bejelentkezési URL-címét, az Azure ad-azonosítót és az Azure kijelentkezési URL-címét, és mentse őket később
    - Azure bejelentkezési URL-cím
    - Azure AD-azonosító
    - Azure kijelentkezési URL-címe
16. A **2 – felhasználói attribútumok és jogcímek** területen kattintson a ceruzára a konfiguráció szerkesztéséhez.
17. Kattintson az **új jogcím hozzáadása** lehetőségre.
18. Név beállítása **felhasználónévre**
19. Állítsa a Source attribútumot a **User. userPrincipalName** értékre.
20. Kattintson a **Mentés** gombra.
21. Kattintson **a csoportjogcím hozzáadása** elemre.
22. **Minden csoport** kijelölése
23. A **csoportjogcím nevének testreszabása**
24. A név beállítása a **csoportnak**
25. Kattintson a **Mentés** gombra.


## <a name="prepare-for-group-matching"></a>Csoport egyeztetésének előkészítése

A FortiGate lehetővé teszi a különböző felhasználói portálok számára a bejelentkezést a csoporttagság alapján. Előfordulhat például, hogy egy felhasználói élmény van a marketing csoport számára, és egy másik a pénzügyi csoport számára.

Konfigurálja a következőképpen:

### <a name="create-groups-for-users"></a>Csoportok létrehozása felhasználók számára

1. Navigáljon a Azure Active Directory panelre, https://portal.azure.com és nyissa meg azt a bérlőt, amely a FortiGate-bejelentkezések identitását fogja biztosítani.
2. Kattintson a **csoportok** elemre
3. Kattintson az **új csoport** elemre.
4. Csoport létrehozása a
    - Csoport típusa = biztonság
    - Csoport neve = `a meaningful name`
    - Csoport leírása = `a meaningful description for the group`
    - Tagság típusa = hozzárendelve
    - Tagok = `users for the user experience that will map to this group`
5. Ismételje meg a 3. és a 4. lépést minden további felhasználói élményhez
6. A csoportok létrehozása után jelölje ki az egyes csoportokat, és jegyezze fel az objektumazonosítók mindegyikét
7. Az objektumazonosítók és a csoportok nevének mentése később


## <a name="configure-the-fortigate-vm"></a>A FortiGate virtuális gép konfigurálása

### <a name="install-the-license"></a>A licenc telepítése

1. Navigáljon ide: `https://<address>`

    Itt `address` látható a FortiGate virtuális géphez rendelt FQDN vagy nyilvános IP-cím.

2. A tanúsítvány esetleges hibáinak megmaradása
3. Bejelentkezés a FortiGate VM üzembe helyezése során megadott rendszergazdai hitelesítő adatok használatával
4. Ha az üzemelő példány a BYOL modellt használja, a rendszer a licenc feltöltésére vonatkozó kérést jeleníti meg. Válassza ki a korábban létrehozott licencfájl, és töltse fel, kattintson az **OK** gombra, és indítsa újra a FortiGate virtuális gépet –

    ![FortiGate SSL VPN](license.png)

5. Az újraindítás után jelentkezzen be újra a rendszergazdai hitelesítő adatokkal a licenc érvényesítéséhez

### <a name="update-firmware"></a>Belső vezérlőprogram frissítése

1. Navigáljon ide: `https://<address>`

    Itt `address` látható a FortiGate virtuális géphez rendelt FQDN vagy nyilvános IP-cím.

2. A tanúsítvány esetleges hibáinak megmaradása
3. Bejelentkezés a FortiGate VM üzembe helyezése során megadott rendszergazdai hitelesítő adatok használatával
4. A bal oldali menüben kattintson a System ( **rendszeren** ) elemre.
5. A számítógép bal oldali menüjében kattintson a **belső vezérlőprogram** elemre.
6. A belső vezérlőprogram kezelése lapon kattintson a **Tallózás** gombra, és válassza ki a korábban letöltött belső vezérlőprogram-fájlt.
7. Hagyja figyelmen kívül a figyelmeztetést, és kattintson a **Backup config és a upgrade** –

    ![FortiGate SSL VPN](backup-configure-upgrade.png)

8. Kattintson a **Folytatás** gombra.
9. Amikor a rendszer kéri, hogy mentse a FortiGate-konfigurációt (. conf-fájlként), kattintson a **Save (Mentés** ) gombra.
10. Várjon, amíg a belső vezérlőprogram feltöltve lesz, és hogy a FortiGate virtuális gép újrainduljon
11. A FortiGate virtuális gép újraindítása után ismét jelentkezzen be a rendszergazdai hitelesítő adatokkal
12. Amikor a rendszer kéri, hogy végezze el az irányítópult beállítását, kattintson **később**
13. Az oktatóanyag Videójának elkezdése után kattintson **az OK** gombra.

### <a name="change-the-management-port-to-tcp"></a>Felügyeleti port módosítása TCP-re

1. Navigáljon ide: `https://<address>`

    Itt `address` látható a FortiGate virtuális géphez rendelt FQDN vagy nyilvános IP-cím.

2. A tanúsítvány esetleges hibáinak megmaradása
3. Bejelentkezés a FortiGate VM üzembe helyezése során megadott rendszergazdai hitelesítő adatok használatával
4. A bal oldali menüben kattintson a System ( **rendszeren** ) elemre.
5. A felügyeleti beállítások területen módosítsa a HTTPS-portot **8443** -re.
6. Kattintson az **alkalmaz** gombra.
7. A módosítás után a böngésző megkísérli az adminisztrációs oldal újratöltését, de a művelet sikertelen lesz. Mostantól a felügyeleti oldal címe a következő lesz: `https://<address>`

    ![FortiGate SSL VPN](certificate.png)

### <a name="upload-the-azure-active-directory-saml-signing-certificate"></a>Az Azure Active Directory SAML aláíró tanúsítványának feltöltése

1. Navigáljon ide: `https://<address>`

    Itt `address` látható a FortiGate virtuális géphez rendelt FQDN vagy nyilvános IP-cím.

2. A tanúsítvány esetleges hibáinak megmaradása
3. Bejelentkezés a FortiGate VM üzembe helyezése során megadott rendszergazdai hitelesítő adatok használatával
4. A bal oldali menüben kattintson a System ( **rendszeren** ) elemre.
5. A rendszerben kattintson a **tanúsítványok** elemre.
6. Kattintson az **Importálás** – > **távoli tanúsítvány** elemre.
7. Keresse meg az Azure-bérlőben a FortiGate egyéni alkalmazás telepítésével letöltött tanúsítványt, jelölje ki, majd kattintson **az OK** gombra.

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>Egyéni SSL-tanúsítvány feltöltése és konfigurálása

Előfordulhat, hogy a FortiGate virtuális gépet saját SSL-tanúsítvánnyal szeretné konfigurálni, amely támogatja az Ön által használt teljes tartománynevet. Ha van hozzáférése egy titkos kulccsal csomagolt SSL-tanúsítványhoz a alkalmazásban. PFX formátum, erre a célra lehet használni.

1. Navigáljon ide: `https://<address>`

    Itt `address` látható a FortiGate virtuális géphez rendelt FQDN vagy nyilvános IP-cím.

2. A tanúsítvány esetleges hibáinak megmaradása
3. Bejelentkezés a FortiGate VM üzembe helyezése során megadott rendszergazdai hitelesítő adatok használatával
4. A bal oldali menüben kattintson a System ( **rendszeren** ) elemre.
5. A rendszerben kattintson a **tanúsítványok** elemre.
6. Kattintson az **Importálás** – > **helyi tanúsítvány** elemre.
7. Kattintson a **PKCS #12 tanúsítvány** elemre.
8. Tallózással keresse meg a következőt:. Az SSL-tanúsítványt és a titkos kulcsot tartalmazó PFX-fájl
9. Adja meg a. PFX-jelszó
10. Adjon meg egy értelmes nevet a tanúsítvány számára
11. Kattintson **az OK** gombra
12. A bal oldali menüben kattintson a System ( **rendszeren** ) elemre.
13. A rendszerben kattintson a **Beállítások** elemre.
14. Az adminisztráció beállításai alatt bontsa ki a HTTPS-kiszolgálói tanúsítvány melletti legördülő listát, és válassza ki a fent importált SSL-tanúsítványt.
15. Kattintson az **alkalmaz** gombra.
16. Zárja be a böngészőablakot, és navigáljon újra `https://<address>`
17. Jelentkezzen be a FortiGate rendszergazdai hitelesítő adataival, és figyelje meg a használatban lévő megfelelő SSL-tanúsítványt.


### <a name="perform-command-line-configuration"></a>Parancssori konfiguráció végrehajtása

_Az SAML-hitelesítés parancssori konfigurációjának végrehajtása_

1. Navigáljon a https://portal.azure.com FortiGate virtuális géphez, és nyissa meg a beállításokat
2. A bal oldali menüben kattintson a **soros konzol** elemre.
3. Bejelentkezés a soros konzolon a FortiGate VM-rendszergazda hitelesítő adataival

    A következő lépésben a korábban rögzített URL-címek szükségesek lesznek. Nevezetesen

    - Entitás azonosítója
    - Válasz URL-cím
    - Kijelentkezési URL-cím
    - Azure bejelentkezési URL-cím
    - Azure AD-azonosító
    - Azure kijelentkezési URL-címe
4. A soros konzolon hajtsa végre a következő parancsokat:

    ```
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert REMOTE_Cert_
    set user-name username
    set group-name group
    end
    ```
    > [!NOTE]
    > Az Azure kijelentkezési URL-címe tartalmazza? karakter. Ehhez speciális kulcshasználat szükséges ahhoz, hogy megfelelően meg lehessen adni a FortiGate soros konzoljának. Az URL-cím általában-

    `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1`

    Ha ezt a soros konzolon szeretné megadni, folytassa a következő beírásával:

    ```
    set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation
    ```
    Ezután írja be a CTRL + V billentyűkombinációt.

    Ezután illessze be a többi URL-címet a sor befejezéséhez.

    ```
    set idp-single-logout-url
    https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.
    ```

5. A konfiguráció megerősítése érdekében hajtsa végre a következőt:

    ```
    show user saml
    ```

_Parancssori konfiguráció végrehajtása a csoportok egyeztetéséhez_

1. Navigáljon a https://portal.azure.com FortiGate virtuális géphez, és nyissa meg a beállításokat
2. A bal oldali menüben kattintson a **soros konzol** elemre.
3. Bejelentkezés a soros konzolon a FortiGate VM-rendszergazda hitelesítő adataival
4. A soros konzolon hajtsa végre a következő parancsokat:

    ```
    config user group
    edit <group 1 name>
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <group 1 Object Id>
    next
    end
    next
    ```

    Ismételje meg a parancsot a szerkesztéssel, `group 1 name` de minden további olyan csoportnál, amely a FortiGate eltérő portál-felülettel rendelkezik

_Parancssori konfiguráció végrehajtása a hitelesítés időtúllépése esetén_

1. Navigáljon a https://portal.azure.com FortiGate virtuális géphez, és nyissa meg a beállításokat
2. A bal oldali menüben kattintson a **soros konzol** elemre.
3. Bejelentkezés a soros konzolon a FortiGate VM-rendszergazda hitelesítő adataival
4. A soros konzolon hajtsa végre a következő parancsokat:

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```
### <a name="create-vpn-portals-and-firewall-policy"></a>VPN-portálok és tűzfal-házirend létrehozása

1. Navigáljon ide: `https://<address>`

    Itt `address` látható a FortiGate virtuális géphez rendelt FQDN vagy nyilvános IP-cím.

2. Bejelentkezés a FortiGate VM üzembe helyezése során megadott rendszergazdai hitelesítő adatok használatával
3. A bal oldali menüben kattintson a **VPN** elemre.
4. A VPN területen kattintson az **SSL-VPN portálok** elemre.
5. Kattintson az **új létrehozása** lehetőségre.
6. Adjon meg egy nevet (általában az egyéni portál felhasználói élményének biztosításához használt Azure-csoportnak megfelelő)
7. Kattintson a **+** forrás IP-készletek melletti plusz jelre (), válassza ki az alapértelmezett készletet, és kattintson a **Bezárás** gombra.
8. Testreszabhatja a csoport élményét. A teszteléshez a portálon található üzenet és a téma testreszabható. Emellett olyan egyéni könyvjelzőket is létrehozhat, amelyek belső erőforrásokhoz irányítják a felhasználókat
9. Kattintson **az OK** gombra
10. Ismételje meg az 5 – 9. lépést minden olyan Azure-csoport esetében, amely egyéni portált fog tartalmazni
11. A VPN területen kattintson az **SSL-VPN-beállítások** elemre.
12. Kattintson a plusz jelre ( **+** ) a kapcsolat figyelése elem mellett.
13. Válassza a **Port1** elemet, majd kattintson a **Bezárás** gombra.


14. Ha korábban már telepített egyéni SSL-tanúsítványt, módosítsa a kiszolgáló tanúsítványát az egyéni SSL-tanúsítvány használatára a legördülő menüben.
15. A hitelesítés/portál leképezése alatt kattintson az **új létrehozása** lehetőségre.
16. Válassza ki az első Azure-csoportot, és társítsa az azonos nevű portálhoz
17. Kattintson **az OK** gombra
18. Ismételje meg a 15 – 17. lépést minden egyes Azure-csoport/portál pár esetében
19. A hitelesítés/portál leképezése alatt szerkessze az **összes többi felhasználót/csoportot**
20. A portál beállítása **teljes hozzáférésre**
21. Kattintson **az OK** gombra
22. Kattintson az **alkalmaz** gombra.
23. Görgessen az SSL-VPN beállítási oldal tetejére, és kattintson a figyelmeztetés: **nincs SSL-VPN-szabályzatok**elemre 
     **. Ide kattintva új SSL-VPN-szabályzatot hozhat létre ezekkel a beállításokkal**
24. Adjon meg egy nevet, például **VPN GRP**
25. Kimenő csatoló beállítása a **portra**
26. Kattintson a **forrás** lehetőségre
27. A címe területen válassza **az összes** lehetőséget.
28. A felhasználó területen válassza ki az első Azure-csoportot
29. Kattintson a **Bezárás** gombra
30. Kattintson a **célhelyre**
31. A címben ez általában a belső hálózat lesz. Login.microsoft.com kiválasztása teszteléshez
32. Kattintson a **Bezárás** gombra
33. Kattintson a **szolgáltatás** lehetőségre
34. Kattintson **az összes**
35. Kattintson a **Bezárás** gombra
36. Kattintson **az OK** gombra
37. A bal oldali menüben kattintson a **házirend & objektumok** elemre.
38. A házirend & objektumok területen kattintson a **tűzfal házirend** elemre.
39. Az **SSL-VPN-alagút felületének kibontása (SSL. root) – > port**
40. Kattintson a jobb gombbal a korábban létrehozott VPN-házirendre ( **VPN GRP 1** ), és válassza a **Másolás** lehetőséget.
41. Kattintson a jobb gombbal a VPN-házirend alatt, és válassza az **alábbi** **Beillesztés** – > elemet.
42. Szerkessze az új szabályzatot, és adjon meg egy másik nevet (mondjuk a **VPN-Grp2** ), és a csoport módosítása a következőre vonatkozik (egy másik Azure-csoport)
43. Kattintson a jobb gombbal az új házirendre, és állítsa az állapotot **engedélyezve** értékre.


## <a name="test-sign-in-using-azure"></a>Bejelentkezés tesztelése az Azure használatával

1. Egy privát böngészőbeli munkamenet használatával nyissa meg a következőt: `https://<address>` 
2. A bejelentkezés Azure Active Directory átirányítása a bejelentkezéshez
3. Miután megadta a hitelesítő adatokat egy, a FortiGate alkalmazáshoz az Azure-bérlőben hozzárendelt felhasználóhoz, a megfelelő felhasználói portált kell megjeleníteni

    ![FortiGate SSL VPN](test-sign-in.png)
