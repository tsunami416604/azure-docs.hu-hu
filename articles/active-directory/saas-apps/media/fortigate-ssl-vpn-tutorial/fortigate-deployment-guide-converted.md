---
title: FortiGate üzembe helyezési útmutató | Microsoft Docs
description: Állítsa be és működjön együtt a Fortinet FortiGate következő generációs tűzfallal.
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
ms.openlocfilehash: 357eb0a60e6246996de9ab75337ecc213d845ae7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91273330"
---
# <a name="fortigate-deployment-guide"></a>FortiGate üzembe helyezési útmutató

Ennek a telepítési útmutatónak a segítségével megtudhatja, hogyan állíthatja be és használhatja a Fortinet-FortiGate a következő generációs tűzfal-termékkel.

## <a name="redeem-the-fortigate-license"></a>A FortiGate-licenc beváltása

A következő generációs Fortinet FortiGate virtuális gépként érhető el az Azure-infrastruktúra szolgáltatásként (IaaS). Ehhez a virtuális géphez két licencelési mód van: utólagos elszámolású és saját licenc.

Előfordulhat, hogy a Fortinet tagjai a Azure Active Directory (Azure AD) "get to Product Secure Hybrid Access (SHA)" csapatának biztosítanak licenceket. Ha nem adta meg a licencet, az utólagos elszámolású üzemelő példány is működni fog.

Ha egy licencet adtak ki, a Fortinet olyan regisztrációs kódot biztosít, amelyet online kell beváltani.

![Képernyőkép a FortiGate SSL VPN regisztrációs kódjáról.](registration-code.png)

1. Regisztráljon itt: https://support.fortinet.com/ .
2. Regisztráció után jelentkezzen be a következő címen: https://support.fortinet.com/ .
3. Válassza az **eszköz**  >  **regisztrálása/aktiválása**lehetőséget.
4. Adja meg a Fortinet által megadott regisztrációs kódot.
5. Adja meg a regisztrációs kódot, válassza ki **a terméket, amelyet a nem kormányzati felhasználó használ**majd, majd válassza a **tovább**lehetőséget.
6. Adja meg a termék leírását (például FortiGate), állítsa be a Fortinet-partnert **más**  >  **Microsoft**-ként, és kattintson a **Tovább gombra**.
7. Fogadja el a **Fortinet szerződést**, és válassza a **tovább**lehetőséget.
8. Fogadja el a **feltételeket** , és válassza a **megerősítés**lehetőséget.
9. Válassza ki a **licencfájl letöltését**, és mentse el később a licencet.


## <a name="download-firmware"></a>Belső vezérlőprogram letöltése

Az Azure-beli virtuális gép Fortinet FortiGate jelenleg nem az SAML-hitelesítéshez szükséges belső vezérlőprogram-verziót szállítja. A legújabb verziót a Fortinet-ből kell beszerezni.

1. Jelentkezzen be a következő címen: https://support.fortinet.com/ .
2. Válassza a **Download**  >  **belső vezérlőprogram lemezképének**letöltése lehetőséget.
3. A **kibocsátási megjegyzések**jobb oldalán válassza a **Letöltés**lehetőséget.
4. Válassza az **v6 lehetőséget.** > **6.** > **6,4.**.
5. Töltse le **FGT_VM64_AZURE-v6-build1637-Fortinet. out** -t úgy, hogy kijelöli a **https** -hivatkozást ugyanazon a sorban.
6. Mentse a fájlt később.


## <a name="deploy-the-fortigate-vm"></a>A FortiGate virtuális gép üzembe helyezése

1. Lépjen https://portal.azure.com a elemre, és jelentkezzen be abba az előfizetésbe, amelyre telepíteni szeretné a FortiGate virtuális gépet.
2. Hozzon létre egy új erőforráscsoportot, vagy nyissa meg azt az erőforráscsoportot, amelybe telepíteni kívánja a FortiGate virtuális gépet.
3. Válassza a **Hozzáadás** lehetőséget.
4. A **Keresés a piactéren**mezőbe írja be a *Forti*. Válassza a **Fortinet FortiGate következő generációs tűzfal**lehetőséget.
5. Válassza ki a szoftvercsomagot (bring-your-own-License, ha rendelkezik licenccel vagy utólagos elszámolással). Kattintson a **Létrehozás** gombra.
6. Töltse fel a virtuális gép konfigurációját.

    ![Képernyőkép a virtuális gép létrehozásáról.](virtual-machine.png)

7. Állítsa be a **hitelesítési típust** a **jelszó**értékre, és adja meg a virtuális gép rendszergazdai hitelesítő adatait.
8. Válassza a **Felülvizsgálat + létrehozás** > **Létrehozás** lehetőséget.
9. Várjon, amíg befejeződik a virtuális gép üzembe helyezése.


### <a name="set-a-static-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>Adjon meg egy statikus nyilvános IP-címet, és rendeljen hozzá egy teljesen minősített tartománynevet

Az egységes felhasználói élmény érdekében állítsa a FortiGate virtuális géphez hozzárendelt nyilvános IP-címet statikusan hozzárendelt értékre. Emellett a teljes tartománynevet (FQDN) is leképezheti.

1. Lépjen a https://portal.azure.com mezőbe, és nyissa meg a FortiGate virtuális gép beállításait.
2. Az **Áttekintés** képernyőn válassza ki a nyilvános IP-címet.

    ![Képernyőkép a FortiGate SSL VPN-ről.](public-ip-address.png)

3. Válassza a **statikus**  >  **Mentés**lehetőséget.

Ha a FortiGate virtuális gép üzembe helyezéséhez nyilvánosan irányítható tartománynév tartozik a környezethez, hozzon létre egy állomás (A) rekordot a virtuális géphez. Ez a rekord az előző, statikusan hozzárendelt nyilvános IP-címhez van leképezve.

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port"></a>Új bejövő hálózati biztonsági csoportra vonatkozó szabály létrehozása a TCP-porthoz

1. Lépjen a https://portal.azure.com mezőbe, és nyissa meg a FortiGate virtuális gép beállításait.
2. A bal oldali menüben válassza a **hálózatkezelés**lehetőséget. Megjelenik a hálózati adapter a listáról, és megjelennek a bejövő portok szabályai.
3. Válassza a **bejövő Port hozzáadása szabály**lehetőséget.
4. Hozzon létre egy új bejövő portszabály-szabályt a 8443-es TCP-porthoz.

    ![Képernyőkép a bejövő biztonsági szabály hozzáadásáról.](port-rule.png)

5. Válassza a **Hozzáadás** lehetőséget.


## <a name="create-a-custom-azure-app-for-fortigate"></a>Egyéni Azure-alkalmazás létrehozása a FortiGate

1. Lépjen a https://portal.azure.com elemre, és nyissa meg a bérlőhöz tartozó Azure ad-ablaktáblát, amely megadja a FortiGate-bejelentkezések identitását.
2. A bal oldali menüben válassza a **vállalati alkalmazások**lehetőséget.
3. Válassza az **új alkalmazás**  >  **nem**katalógusbeli alkalmazás lehetőséget.
4. Adjon meg egy nevet (például FortiGate), és válassza a **Hozzáadás**lehetőséget.
5. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.
6. Adja hozzá azokat a felhasználókat, akik be tudnak jelentkezni, majd válassza a **hozzárendelés**lehetőséget.
7. A bal oldali menüben válassza az **egyszeri bejelentkezés**lehetőséget.
8. Válassza az **SAML**lehetőséget.
9. Az **alapszintű SAML-konfiguráció**területen válassza a ceruza ikont a konfiguráció szerkesztéséhez.
10. Konfigurálja az alábbiakat:
    - Az **azonosítónak (Entity ID)** kell lennie `https://<address>/remote/saml/metadata` .
    - **Válasz URL-címe (a fogyasztói szolgáltatás URL-címe)**  `https://<address>/remote/saml/login` .
    - A **kijelentkezési URL-cím** legyen `https://<address>/remote/saml/logout` .

    `<address>` a FortiGate virtuális géphez rendelt FQDN vagy nyilvános IP-cím.

11. Jegyezze fel ezeket az URL-címeket későbbi használatra: az entitás azonosítója, a válasz URL-címe és a kijelentkezési URL-cím.
12. Válassza a **Mentés**lehetőséget, majd az **alapszintű SAML-konfigurációt**.
13. **3 – SAML aláíró tanúsítvány**, Letöltés **tanúsítványa (Base64)** , és mentse később.
14. A **4 – set (alkalmazás neve)** alatt másolja az Azure bejelentkezési URL-címét, az Azure ad-azonosítót és az Azure kijelentkezési URL-címét, majd mentse őket később.
15. A **2 – felhasználói attribútumok és jogcímek**területen válassza a ceruza ikont a konfiguráció szerkesztéséhez.
16. Válassza az **új jogcím hozzáadása**lehetőséget, és adja meg a **felhasználónevet a Felhasználónév**mezőben.
17. Állítsa a Source attribútumot a **User. userPrincipalName**értékre.
18. Válassza a **Mentés**lehetőséget, majd az összes csoport  >  **hozzáadása**elemet  >  **All groups**.
19. Válassza a **csoport nevének testreszabása**lehetőséget, és állítsa a nevet a **csoportnak**.
20. Kattintson a **Mentés** gombra.


## <a name="prepare-for-group-matching"></a>Csoport egyeztetésének előkészítése

A FortiGate lehetővé teszi a különböző felhasználói portálok számára a bejelentkezést a csoporttagság alapján. Előfordulhat például, hogy egy felhasználói élmény van a marketing csoport számára, és egy másik a pénzügyi csoport számára. A következő módon hozhat létre csoportokat a felhasználók számára:

1. Lépjen a https://portal.azure.com elemre, és nyissa meg a bérlőhöz tartozó Azure ad-ablaktáblát, amely megadja a FortiGate-bejelentkezések identitását.
2. Válassza a **csoportok**  >  **új csoport**lehetőséget.
3. Hozzon létre egy csoportot a következő részletekkel:
    - Csoport típusa = biztonság
    - Csoport neve = `a meaningful name`
    - Csoport leírása = `a meaningful description for the group`
    - Tagság típusa = hozzárendelve
    - Tagok = `users for the user experience that will map to this group`
4. Ismételje meg a 3. és a 4. lépést a további felhasználói élményekhez.
5. A csoportok létrehozása után válassza ki az egyes csoportokat, és jegyezze fel az **objektumazonosítók** mindegyikét.
6. Mentse ezeket az objektumazonosítók és a csoportok nevét később.


## <a name="configure-the-fortigate-vm"></a>A FortiGate virtuális gép konfigurálása

Az alábbi szakaszokból megtudhatja, hogyan állíthatja be a FortiGate virtuális gépet.

### <a name="install-the-license"></a>A licenc telepítése

1. Nyissa meg a következőt: `https://<address>`. Itt a `<address>` FortiGate virtuális géphez hozzárendelt teljes tartománynév vagy nyilvános IP-cím.

2. Folytassa a tanúsítvány esetleges hibáit.
3. Jelentkezzen be a FortiGate virtuális gép telepítése során megadott rendszergazdai hitelesítő adatok használatával.
4. Ha a telepítés a saját licencet használó modellt használja, megjelenik egy, a licenc feltöltésére vonatkozó kérdés. Válassza ki a korábban létrehozott licencfájl, és töltse fel a fájlt. Kattintson **az OK gombra** , és indítsa újra a FortiGate virtuális gépet.

    ![Képernyőkép a FortiGate VM-licencről.](license.png)

5. Az újraindítás után jelentkezzen be újra a rendszergazdai hitelesítő adatokkal a licenc érvényesítéséhez.

### <a name="update-firmware"></a>Belső vezérlőprogram frissítése

1. Nyissa meg a következőt: `https://<address>`. Itt a `<address>` FortiGate virtuális géphez hozzárendelt teljes tartománynév vagy nyilvános IP-cím.

2. Folytassa a tanúsítvány esetleges hibáit.
3. Jelentkezzen be a FortiGate virtuális gép telepítése során megadott rendszergazdai hitelesítő adatok használatával.
4. A bal oldali **menüben válassza a**  >  **rendszerbelső vezérlőprogram**elemet.
5. A **belső vezérlőprogram felügyelete**területen válassza a **Tallózás**lehetőséget, majd válassza ki a korábban letöltött belső vezérlőprogram-fájlt.
6. Hagyja figyelmen kívül a figyelmeztetést, és válassza a **biztonsági mentés konfiguráció és frissítés**lehetőséget.

    ![Képernyőfelvétel a belső vezérlőprogram felügyeletéről.](backup-configure-upgrade.png)

7. Válassza a **Folytatás**lehetőséget.
8. Amikor a rendszer kéri, hogy mentse a FortiGate-konfigurációt (. conf-fájlként), válassza a **Mentés**lehetőséget.
9. Várja meg a belső vezérlőprogram feltöltését és alkalmazásának megvárniát. Várjon, amíg a FortiGate virtuális gép újraindul.
10. A FortiGate virtuális gép újraindítása után jelentkezzen be újra a rendszergazdai hitelesítő adatokkal.
11. Amikor a rendszer felszólítja az irányítópult beállítására, válassza a **később**lehetőséget.
12. Az oktatóanyag-videó elkezdése után válassza **az OK gombot**.

### <a name="change-the-management-port-to-tcp"></a>Felügyeleti port módosítása TCP-re

1. Nyissa meg a következőt: `https://<address>`. Itt a `<address>` FortiGate virtuális géphez hozzárendelt teljes tartománynév vagy nyilvános IP-cím.

2. Folytassa a tanúsítvány esetleges hibáit.
3. Jelentkezzen be a FortiGate virtuális gép telepítése során megadott rendszergazdai hitelesítő adatok használatával.
4. A bal oldali menüben válassza a **System (rendszeren**) lehetőséget.
5. Az **Adminisztráció beállításai**területen módosítsa a https-portot a **8443**értékre, majd kattintson az **alkalmaz**gombra.
6. A módosítás után a böngésző megkísérli az adminisztrációs oldal újratöltését, de az sikertelen lesz. Mostantól a felügyeleti oldal címe a következő: `https://<address>` .

    ![Képernyőkép a távoli tanúsítvány feltöltéséről.](certificate.png)

### <a name="upload-the-azure-ad-saml-signing-certificate"></a>Az Azure AD SAML aláíró tanúsítványának feltöltése

1. Nyissa meg a következőt: `https://<address>`. Itt a `<address>` FortiGate virtuális géphez hozzárendelt teljes tartománynév vagy nyilvános IP-cím.

2. Folytassa a tanúsítvány esetleges hibáit.
3. Jelentkezzen be a FortiGate virtuális gép telepítése során megadott rendszergazdai hitelesítő adatok használatával.
4. A bal oldali **menüben válassza a**  >  **rendszertanúsítványok**lehetőséget.
5. Válassza **Import**a  >  **távoli tanúsítvány**importálása lehetőséget.
6. Tallózással keresse meg a FortiGate egyéni alkalmazás telepítése az Azure-bérlőben letöltött tanúsítványát. Jelölje ki, majd kattintson **az OK gombra**.

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>Egyéni SSL-tanúsítvány feltöltése és konfigurálása

Előfordulhat, hogy a FortiGate virtuális gépet saját SSL-tanúsítvánnyal szeretné konfigurálni, amely támogatja az Ön által használt teljes tartománynevet. Ha olyan SSL-tanúsítványhoz fér hozzá, amely PFX formátumban van csomagolva a titkos kulccsal, akkor erre a célra használható.

1. Nyissa meg a következőt: `https://<address>`. Itt a `<address>` FortiGate virtuális géphez hozzárendelt teljes tartománynév vagy nyilvános IP-cím.

2. Folytassa a tanúsítvány esetleges hibáit.
3. Jelentkezzen be a FortiGate virtuális gép telepítése során megadott rendszergazdai hitelesítő adatok használatával.
4. A bal oldali **menüben válassza a**  >  **rendszertanúsítványok**lehetőséget.
5. Válassza **Import**a  >  **helyi tanúsítvány**  >  **PKCS #12 tanúsítvány**importálása lehetőséget.
6. Tallózással keresse meg a következőt:. Az SSL-tanúsítványt és a titkos kulcsot tartalmazó PFX-fájl.
7. Adja meg a. PFX-jelszó és a tanúsítvány kifejező neve. Ezután válassza az **OK** gombot.
8. A bal oldali **menüben válassza a**  >  **Rendszerbeállítások**elemet.
9. Az **Adminisztráció beállításai**területen bontsa ki a **https-kiszolgálói tanúsítvány**melletti listát, és válassza ki a korábban importált SSL-tanúsítványt.
10. Kattintson az **Alkalmaz** gombra.
11. Zárjuk be a böngészőablakot, és nyissa meg a következőt: `https://<address>` .
12. Jelentkezzen be a FortiGate rendszergazdai hitelesítő adataival. Ekkor látnia kell a megfelelő SSL-tanúsítványt a használatban.


### <a name="perform-command-line-configuration"></a>Parancssori konfiguráció végrehajtása

A következő szakaszokban ismertetjük a különböző konfigurációk lépéseit a parancssor használatával.

#### <a name="for-saml-authentication"></a>SAML-hitelesítéshez

1. Lépjen a https://portal.azure.com mezőbe, és nyissa meg a FortiGate virtuális gép beállításait.
2. A bal oldali menüben válassza a **soros konzol**lehetőséget.
3. Jelentkezzen be a soros konzolon a FortiGate VM-rendszergazda hitelesítő adataival. A következő lépésben a korábban rögzített URL-címek szükségesek:
    - Entitás azonosítója
    - Válasz URL-cím
    - Kijelentkezési URL-cím
    - Azure bejelentkezési URL-cím
    - Azure AD-azonosító
    - Azure kijelentkezési URL-címe
4. A soros konzolon futtassa a következő parancsokat:

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
    > Az Azure kijelentkezési URL-címe tartalmaz egy `?` karaktert. Ehhez speciális kulcshasználat szükséges ahhoz, hogy megfelelően meg lehessen adni a FortiGate soros konzoljának. Az URL-cím általában `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1` . A soros konzolon való megadásához írja be a következőt:
        ```
        set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation
        ```.
    Ezután írja be a CTRL + V billentyűkombinációt, majd illessze be a többi URL-címet a sor befejezéséhez: 
        ```
        set idp-single-logout-url
        https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.
        ```

5. A konfiguráció megerősítéséhez futtassa a következőt:

    ```
    show user saml
    ```

#### <a name="for-group-matching"></a>Csoport egyeztetése

1. Lépjen a https://portal.azure.com mezőbe, és nyissa meg a FortiGate virtuális gép beállításait.
2. A bal oldali menüben válassza a **soros konzol**lehetőséget.
3. Jelentkezzen be a soros konzolon a FortiGate VM-rendszergazda hitelesítő adataival.
4. A soros konzolon futtassa a következő parancsokat:

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

    Minden olyan további csoportnál, amely a FortiGate különböző portál-felülettel rendelkezik, ismételje meg ezeket a parancsokat (a kód második sorától kezdődően).

#### <a name="for-authentication-timeout"></a>Hitelesítési időtúllépés esetén

1. Lépjen a https://portal.azure.com mezőbe, és nyissa meg a FortiGate virtuális gép beállításait.
2. A bal oldali menüben válassza a **soros konzol**lehetőséget.
3. Jelentkezzen be a soros konzolon a FortiGate VM-rendszergazda hitelesítő adataival.
4. A soros konzolon futtassa a következő parancsokat:

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```
### <a name="create-vpn-portals-and-firewall-policy"></a>VPN-portálok és tűzfal-házirend létrehozása

1. Nyissa meg a következőt: `https://<address>`. Itt a `<address>` FortiGate virtuális géphez hozzárendelt teljes tartománynév vagy nyilvános IP-cím.

2. Jelentkezzen be a FortiGate virtuális gép telepítése során megadott rendszergazdai hitelesítő adatok használatával.
3. A bal oldali menüben válassza a **VPN**  >  **SSL-VPN-portálok**  >  **új létrehozása**elemet.
6. Adjon meg egy nevet (ez általában az egyéni portál felhasználói élményének biztosításához használt Azure-csoportnak felel meg).
7. Válassza ki a **+** **forrás IP-készletek**melletti pluszjelet (), jelölje ki az alapértelmezett készletet, majd kattintson a **Bezárás**gombra.
8. Testreszabhatja a csoport élményét. A teszteléshez a portálon található üzenet és a téma testreszabható. Emellett olyan egyéni könyvjelzőket is létrehozhat, amelyek belső erőforrásokhoz irányítják a felhasználókat.
9. Válassza az **OK** lehetőséget.
10. Ismételje meg az 5-9-os lépést minden olyan Azure-csoport esetében, amely egyéni portált fog tartalmazni.
11. A VPN területen válassza az **SSL-VPN-beállítások**elemet.
12. Válassza ki a **+** **kapcsolat figyelése**elem melletti pluszjelet (), válassza a **Port1**lehetőséget, majd kattintson a **Bezárás**gombra.
14. Ha korábban már telepített egy egyéni SSL-tanúsítványt, módosítsa a **kiszolgáló tanúsítványát** az egyéni SSL-tanúsítvány használatára a legördülő menüben.
15. A **hitelesítés/portál leképezése**területen válassza az **új létrehozása**lehetőséget. Válassza ki az első Azure-csoportot, és egyezzen meg az azonos nevű portálon. Ezután válassza az **OK** gombot.
18. Ismételje meg a 15-17 lépést az Azure-csoportok és a portálok minden egyes párosítása esetén.
19. A **hitelesítés/portál leképezése**alatt szerkessze az **összes többi felhasználót/csoportot**.
20. Állítsa a portált **teljes hozzáférésre**, majd kattintson **az OK gombra**  >  **Apply**.
23. Görgessen az **SSL-VPN-beállítási** oldal tetejére, és válassza az **SSL-VPN-házirendek nem léteznek elemet. Ide kattintva új SSL-VPN-házirendet hozhat létre ezekkel a beállításokkal.**
24. Adjon meg egy nevet, például: **VPN GRP**. Ezután állítsa be a **kimenő felületet** a **port**értékre, és válassza a **forrás**lehetőséget.
27. A **címe**területen válassza **az összes**lehetőséget.
28. A **felhasználó**területen válassza ki az első Azure-csoportot.
29. Válassza a cél **lezárása**lehetőséget  >  **Destination**. A **címben**ez általában a belső hálózat. Teszteléshez válassza a **login.microsoft.com** lehetőséget.
32. Válassza **Close**  >  **Service**  >  **az összes**szolgáltatás lezárása lehetőséget. Ezután kattintson a **Bezárás**  >  **OK gombra**.
37. A bal oldali menüben válassza a **házirend & objektumok**  >  **tűzfal házirend**elemet.
39. Bontsa ki az **SSL-VPN Tunnel Interface (SSL. root)**  >  **portot**.
40. Kattintson a jobb gombbal a korábban létrehozott VPN-házirendre (**VPN GRP 1**), és válassza a **Másolás**lehetőséget.
41. Kattintson a jobb gombbal a VPN-házirend elemre **Paste**, majd válassza az  >  **alábbi**beillesztés lehetőséget.
42. Szerkessze az új szabályzatot, és adjon meg egy másik nevet (például **VPN-Grp2**). Módosítsa a csoportot is (egy másik Azure-csoportra).
43. Kattintson a jobb gombbal az új házirendre, és állítsa az állapotot **engedélyezve**értékre.


## <a name="test-sign-in-by-using-azure"></a>Bejelentkezés tesztelése az Azure használatával

1. A saját böngészős munkamenet használatával nyissa meg a következőt: `https://<address>` .  
2. A bejelentkezéshez át kell irányítani az Azure AD-t a bejelentkezéshez.
3. Miután megadta a hitelesítő adatokat az Azure-bérlőben a FortiGate alkalmazáshoz hozzárendelt felhasználóhoz, meg kell jelennie a megfelelő felhasználói portálnak.

    ![Képernyőkép a FortiGate SSL VPN-ről](test-sign-in.png)
