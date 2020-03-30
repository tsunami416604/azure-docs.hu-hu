---
title: Azure AD Connect – AD FS-kezelés és testreszabás | Microsoft dokumentumok
description: AD FS-kezelés az Azure AD Connect tel és a felhasználói AD FS bejelentkezési élmény testreszabása az Azure AD Connect és a PowerShell használatával.
keywords: AD FS, ADFS, AD FS-kezelés, AAD Connect, Csatlakozás, bejelentkezés, AD FS testreszabás, javítási megbízhatóság, O365, összevonás, függő entitás
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: fcbeedddc65a916f869a778616779917a9571181
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331984"
---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Az Active Directory összevonási szolgáltatások kezelése és testreszabása az Azure AD Connect használatával
Ez a cikk ismerteti, hogyan kezelheti és testre szabhatja az Active Directory összevonási szolgáltatások (AD FS) segítségével az Azure Active Directory (Azure AD) Connect. Más gyakori AD FS-feladatokat is tartalmaz, amelyeket az AD FS-farm teljes konfigurációjához el kell végeznie.

| Témakör | Mit fed le |
|:--- |:--- |
| **AD FS kezelése** | |
| [A bizalmi kapcsolat javítása](#repairthetrust) |Az összevonási megbízhatóság javítása az Office 365-tel |
| [Federate az Azure AD használatával alternatív bejelentkezési azonosító](#alternateid) | Összevonás konfigurálása alternatív bejelentkezési azonosítóval  |
| [AD FS-kiszolgáló hozzáadása](#addadfsserver) |Az AD FS-farm kibontása egy további AD FS-kiszolgálóval. |
| [AD FS webalkalmazás-proxykiszolgáló hozzáadása](#addwapserver) |Az AD FS-farm kibontása egy további WAP-kiszolgálóval. |
| [Összevont tartomány hozzáadása](#addfeddomain) |Összevont tartomány hozzáadása. |
| [A TLS/SSL tanúsítvány frissítése](how-to-connect-fed-ssl-update.md)| Az AD FS-farm TLS/SSL-tanúsítványának frissítése. |
| **AD FS testreszabása** | |
| [Egyéni vállalati embléma vagy illusztráció hozzáadása](#customlogo) |Az AD FS bejelentkezési oldal testreszabása vállalati emblémával és illusztrációval. |
| [Bejelentkezési leírás hozzáadása](#addsignindescription) |Bejelentkezési oldal leírásának hozzáadása. |
| [AD FS jogcímszabályok módosítása](#modclaims) |Az AD FS-jogcímek módosítása különböző összevonási forgatókönyvekhez. |

## <a name="manage-ad-fs"></a>AD FS kezelése
Az Azure AD Connect varázslóval az Azure AD Connect használatával különböző AD FS-sel kapcsolatos feladatokat hajthat végre az Azure AD Connect szolgáltatásban. Miután befejezte az Azure AD Connect telepítését a varázsló futtatásával, a varázslót újra futtathatja további feladatok végrehajtásához.

## <a name="repair-the-trust"></a><a name="repairthetrust"></a>A bizalmi kapcsolat javítása 
Az Azure AD Connect segítségével ellenőrizheti az AD FS és az Azure AD megbízhatósági állapotának aktuális állapotát, és megteheti a megfelelő lépéseket a megbízhatóság i. Kövesse az alábbi lépéseket az Azure AD és az AD FS megbízhatóságának javításához.

1. Válassza a további feladatok listájában válassza az **AAD és az ADFS megbízhatósági** kapcsolat javítása lehetőséget.
   ![AAD és ADFS megbízhatóságjavítása](./media/how-to-connect-fed-management/RepairADTrust1.PNG)

2. A Csatlakozás az **Azure AD-hez** lapon adja meg az Azure AD globális rendszergazdai hitelesítő adatait, majd kattintson a **Tovább**gombra.
   ![Csatlakozás az Azure AD-hez](./media/how-to-connect-fed-management/RepairADTrust2.PNG)

3. A **Távelérés hitelesítő adatai** lapon adja meg a tartományi rendszergazda hitelesítő adatait.

   ![Távelérési hitelesítő adatok](./media/how-to-connect-fed-management/RepairADTrust3.PNG)

    Miután a **Tovább**gombra kattintott, az Azure AD Connect ellenőrzi a tanúsítványok állapotát, és megjeleníti a problémákat.

    ![A bizonyítványok állapota](./media/how-to-connect-fed-management/RepairADTrust4.PNG)

    A **Konfigurálásra kész** lap a megbízhatósági kapcsolat javítására végrehajtandó műveletek listáját jeleníti meg.

    ![Ready to configure (Konfigurálásra kész)](./media/how-to-connect-fed-management/RepairADTrust5.PNG)

4. A **megbízhatóság idát** kijavítani a Telepítés gombra kattintva.

> [!NOTE]
> Az Azure AD Connect csak az önaláírt tanúsítványokat javíthatja ki, illetve nem tud megfelelően működni. Az Azure AD Connect nem tudja kijavítani a külső tanúsítványokat.

## <a name="federate-with-azure-ad-using-alternateid"></a><a name="alternateid"></a>Federate az Azure AD használatával AlternateID 
Javasoljuk, hogy a helyszíni egyszerű felhasználónév(UPN) és a felhőbeli egyszerű felhasználónév változatlan maradjon. Ha a helyszíni upn nem irányítható tartományt használ (pl. Contoso.local) vagy helyi alkalmazásfüggőségek miatt nem módosítható, ezért azt javasoljuk, hogy alternatív bejelentkezési azonosítót kell létrehozni. Az alternatív bejelentkezési azonosító lehetővé teszi egy bejelentkezési élmény konfigurálását, ahol a felhasználók nem az online felhasználónjuk, például a levelezési attribútumukkal jelentkezhetnek be. Az Azure AD Connect egyszerű felhasználónév választása alapértelmezés szerint az Active Directory userPrincipalName attribútumára vált. Ha az egyszerű felhasználónév hez bármely más attribútumot választ, és az AD FS használatával kap összeegyezget, akkor az Azure AD Connect konfigurálja az AD FS-t az alternatív bejelentkezési azonosítóhoz. Az alábbiakban egy példa látható egy másik attribútum kiválasztására az egyszerű felhasználónévhez:

![Alternatív azonosító attribútumkijelölés](./media/how-to-connect-fed-management/attributeselection.png)

Az Alternatív bejelentkezési azonosító konfigurálása az AD FS-hez két fő lépésből áll:
1. **Konfigurálja a kiállítási jogcímek megfelelő készletét:** Az Azure AD függő entitás megbízhatósági kapcsolatának kiállítási jogcímszabályai úgy módosulnak, hogy a kiválasztott UserPrincipalName attribútumot használják a felhasználó alternatív azonosítójaként.
2. **Alternatív bejelentkezési azonosító engedélyezése az AD FS konfigurációban**: Az AD FS-konfiguráció frissül, hogy az AD FS megtudja keresni a felhasználókat a megfelelő erdőkben az alternatív azonosító használatával. Ez a konfiguráció a Windows Server 2012 R2 (KB2919355) vagy újabb rendszeren futó AD FS szolgáltatás esetén támogatott. Ha az AD FS-kiszolgálók 2012 R2, az Azure AD Connect ellenőrzi a szükséges KB jelenlétét. Ha a kb nem észlelhető, a konfiguráció befejezése után figyelmeztetés jelenik meg, az alábbiak szerint:

    ![Figyelmeztetés a hiányzó KB 2012R2](./media/how-to-connect-fed-management/kbwarning.png)

    Hiányzó KB esetén a konfiguráció helyesbítéséhez telepítse a szükséges [KB2919355-öt,](https://go.microsoft.com/fwlink/?LinkID=396590) majd javítsa ki a megbízhatósági kapcsolatot az [AAD és az AD FS Trust javítása](#repairthetrust)segítségével.

> [!NOTE]
> Az alternatív azonosítóval és a manuális konfigurálás lépéseivel kapcsolatos további információkért olvassa el [az Alternatív bejelentkezési azonosító konfigurálása című témakört.](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configuring-alternate-login-id)

## <a name="add-an-ad-fs-server"></a><a name="addadfsserver"></a>AD FS-kiszolgáló hozzáadása 

> [!NOTE]
> AD FS-kiszolgáló hozzáadásához az Azure AD Connect megköveteli a PFX-tanúsítványt. Ezért ezt a műveletet csak akkor hajthatja végre, ha konfigurálta az AD FS-farm ot az Azure AD Connect használatával.

1. Válassza **a További összevonási kiszolgáló telepítése**lehetőséget, majd kattintson a **Tovább**gombra.

   ![További összevonási kiszolgáló](./media/how-to-connect-fed-management/AddNewADFSServer1.PNG)

2. A Csatlakozás az **Azure AD-hez** lapon adja meg az Azure AD globális rendszergazdai hitelesítő adatait, majd kattintson a **Tovább**gombra.

   ![Csatlakozás az Azure AD szolgáltatáshoz](./media/how-to-connect-fed-management/AddNewADFSServer2.PNG)

3. Adja meg a tartományi rendszergazda hitelesítő adatait.

   ![Tartományi rendszergazdai hitelesítő adatok](./media/how-to-connect-fed-management/AddNewADFSServer3.PNG)

4. Az Azure AD Connect kéri a jelszót a PFX-fájl, amely az új AD FS-farm konfigurálása során az Azure AD Connect. Kattintson a **Jelszó megadása gombra** a PFX-fájl jelszavának megadásához.

   ![Tanúsítvány jelszava](./media/how-to-connect-fed-management/AddNewADFSServer4.PNG)

    ![TLS/SSL-tanúsítvány megadása](./media/how-to-connect-fed-management/AddNewADFSServer5.PNG)

5. Az **AD FS-kiszolgálók** lapon adja meg az AD FS-farmhoz hozzáadni a kiszolgáló nevét vagy IP-címét.

   ![AD FS-kiszolgálók](./media/how-to-connect-fed-management/AddNewADFSServer6.PNG)

6. Kattintson a **Tovább**gombra, és menjen végig az utolsó **Konfigurálás** lapon. Miután az Azure AD Connect befejezte a kiszolgálók hozzáadása az AD FS-farmhoz, lehetősége lesz a kapcsolat ellenőrzésére.

   ![Ready to configure (Konfigurálásra kész)](./media/how-to-connect-fed-management/AddNewADFSServer7.PNG)

    ![Telepítés kész](./media/how-to-connect-fed-management/AddNewADFSServer8.PNG)

## <a name="add-an-ad-fs-wap-server"></a><a name="addwapserver"></a>AD FS WAP-kiszolgáló hozzáadása 

> [!NOTE]
> WAP-kiszolgáló hozzáadásához az Azure AD Connect megköveteli a PFX-tanúsítványt. Ezért csak akkor hajthatja végre ezt a műveletet, ha konfigurálta az AD FS-farm ot az Azure AD Connect használatával.

1. Válassza **a webalkalmazás-proxy telepítése** elemet az elérhető feladatok listájából.

   ![Webalkalmazás-proxy telepítése](./media/how-to-connect-fed-management/WapServer1.PNG)

2. Adja meg az Azure globális rendszergazdai hitelesítő adatait.

   ![Csatlakozás az Azure AD szolgáltatáshoz](./media/how-to-connect-fed-management/wapserver2.PNG)

3. Az **SSL-tanúsítvány megadása** lapon adja meg az AD FS-farm Azure AD Connect tel történtkonfigurálásakor megadott PFX-fájl jelszavát.
   ![Tanúsítvány jelszava](./media/how-to-connect-fed-management/WapServer3.PNG)

    ![TLS/SSL-tanúsítvány megadása](./media/how-to-connect-fed-management/WapServer4.PNG)

4. Adja hozzá a wap-kiszolgálóként hozzáadni szeretne kiszolgálót. Mivel a WAP-kiszolgáló nem csatlakozik a tartományhoz, a varázsló rendszergazdai hitelesítő adatokat kér a hozzáadott kiszolgálóhoz.

   ![Felügyeleti kiszolgáló hitelesítő adatai](./media/how-to-connect-fed-management/WapServer5.PNG)

5. A **Proxy megbízhatósági hitelesítő adatai** lapon adja meg a rendszergazdai hitelesítő adatokat a proxymegbízhatóság konfigurálásához és az AD FS-farm elsődleges kiszolgálójának eléréséhez.

   ![Proxymegbízhatósági hitelesítő adatok](./media/how-to-connect-fed-management/WapServer6.PNG)

6. A **Konfigurálásra kész** lapon a varázsló megjeleníti a végrehajtandó műveletek listáját.

   ![Ready to configure (Konfigurálásra kész)](./media/how-to-connect-fed-management/WapServer7.PNG)

7. A konfiguráció befejezéséhez kattintson a **Telepítés** gombra. A konfiguráció befejezése után a varázsló lehetőséget ad a kiszolgálók közötti kapcsolat ellenőrzésére. Kattintson **az Ellenőrzés** gombra a kapcsolat ellenőrzéséhez.

   ![Telepítés kész](./media/how-to-connect-fed-management/WapServer8.PNG)

## <a name="add-a-federated-domain"></a><a name="addfeddomain"></a>Összevont tartomány hozzáadása 

Az Azure AD Connect használatával egyszerűen hozzáadhat egy azure AD-vel összevont tartományt. Az Azure AD Connect hozzáadja a tartományt az összevonáshoz, és módosítja a jogcímszabályokat, hogy megfelelően tükrözze a kibocsátót, ha több, az Azure AD-vel összevont tartományt rendelkezik.

1. Összevont tartomány hozzáadásához válassza ki a **További Azure AD-tartomány hozzáadása**feladatot.

   ![További Azure AD-tartomány](./media/how-to-connect-fed-management/AdditionalDomain1.PNG)

2. A varázsló következő lapján adja meg az Azure AD globális rendszergazdai hitelesítő adatait.

   ![Csatlakozás az Azure AD szolgáltatáshoz](./media/how-to-connect-fed-management/AdditionalDomain2.PNG)

3. A **Távelérés hitelesítő adatai** lapon adja meg a tartományi rendszergazda hitelesítő adatait.

   ![Távelérési hitelesítő adatok](./media/how-to-connect-fed-management/additionaldomain3.PNG)

4. A következő lapon a varázsló az Azure AD-tartományok listáját tartalmazza, amelyekkel összehasználhatja a helyszíni címtárat. Válassza ki a tartományt a listából.

   ![Azure AD tartomány](./media/how-to-connect-fed-management/AdditionalDomain4.PNG)

    A tartomány kiválasztása után a varázsló megfelelő információkat nyújt a varázsló által végrehajtott további műveletekről és a konfiguráció hatásáról. Bizonyos esetekben, ha olyan tartományt választ, amely még nincs ellenőrizve az Azure AD-ben, a varázsló információkat szolgáltat a tartomány ellenőrzéséhez. További részletekért [lásd: Egyéni tartománynév hozzáadása az Azure Active Directoryhoz](../active-directory-domains-add-azure-portal.md) című témakörben.

5. Kattintson a **Tovább** gombra. A **konfigurálásra kész** lap az Azure AD Connect által végrehajtandó műveletek listáját jeleníti meg. A konfiguráció befejezéséhez kattintson a **Telepítés** gombra.

   ![Ready to configure (Konfigurálásra kész)](./media/how-to-connect-fed-management/AdditionalDomain5.PNG)

> [!NOTE]
> A hozzáadott összevont tartományból származó felhasználókat szinkronizálni kell, mielőtt be tudnak jelentkezni az Azure AD-be.

## <a name="ad-fs-customization"></a>Az AD FS testreszabása
Az alábbi szakaszok az AD FS bejelentkezési lapjának testreszabásakor végrehajtandó gyakori feladatok részleteit ismertetik.

## <a name="add-a-custom-company-logo-or-illustration"></a><a name="customlogo"></a>Egyéni vállalati embléma vagy illusztráció hozzáadása 
A **bejelentkezési** lapon megjelenő vállalat embléma módosításához használja a következő Windows PowerShell-parancsmalapot és szintaxist.

> [!NOTE]
> Az embléma ajánlott méretei 260 x 35 \@ 96 dpi, amelynek fájlmérete nem nagyobb, mint 10 KB.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [!NOTE]
> A *TargetName* paraméter megadása kötelező. Az AD FS szolgáltatással kiadott alapértelmezett téma neve Default.

## <a name="add-a-sign-in-description"></a><a name="addsignindescription"></a>Bejelentkezési leírás hozzáadása 
Ha bejelentkezési lap leírását szeretné hozzáadni a **bejelentkezési laphoz,** használja a következő Windows PowerShell-parancsmalapot és szintaxist.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

## <a name="modify-ad-fs-claim-rules"></a><a name="modclaims"></a>AD FS jogcímszabályok módosítása 
Az AD FS támogatja az egyéni jogcímszabályok létrehozásához használható bővített jogcímnyelvet. További információ: [The Role of the claim rule Language](https://technet.microsoft.com/library/dd807118.aspx).

A következő szakaszok ismertetik, hogyan írhat egyéni szabályokat az Azure AD és az AD FS-összevonáshoz kapcsolódó egyes forgatókönyvekhez.

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>A nem módosítható azonosító attól függ, hogy egy érték szerepel-e az attribútumban
Az Azure AD Connect lehetővé teszi, hogy adjon meg egy attribútumot kell használni, mint a forrás horgony, amikor az objektumok szinkronizálva vannak az Azure AD.Azure AD Connect lets you specify an a tribute to be used as a source anchor when objects are synced to Azure AD. Ha az egyéni attribútum értéke nem üres, érdemes lehet egy nem módosítható azonosító jogcímet kiadni.

Például az **ms-ds-consistencyguid-ot választhatja** a forráshorgony attribútumaként, és az **ImmutableID** probléma az **ms-ds-consistencyguid,ha** az attribútum nak értéke van. Ha nincs érték az attribútummal szemben, akkor a probléma **objektumguid,** mint a nem módosítható azonosító. Az egyéni jogcímszabályok készletét a következő szakaszban leírtak szerint hozhatja létre.

**1. szabály: Lekérdezési attribútumok**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

Ebben a szabályban az **ms-ds-consistencyguid** és **objectGuid** értékeit kérdezi le a felhasználó számára az Active Directoryból. Módosítsa az üzlet nevét egy megfelelő üzletnévre az AD FS központi telepítésében. Módosítsa a jogcímtípusát az összevonás megfelelő jogcímtípusra is, az **objectGuid** és **ms-ds-consistencyguid**meghatározása szerint.

A **hozzáadás** és a nem **kiadás**használatával elkerülheti az entitás kimenő kiadásának hozzáadását, és az értékeket köztes értékként használhatja. A jogcímet egy későbbi szabályban adja ki, miután megállapította, hogy melyik értéket használja nem módosítható azonosítóként.

**2. szabály: Ellenőrizze, hogy létezik-e ms-ds-consistencyguid a felhasználó számára**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Ez a szabály egy **ideiglenes idflag** nevű jelzőt határoz meg, amely **használatguid-ra** van állítva, ha a felhasználó számára nincs **ms-ds-consistencyguid** feltöltve. Ennek logikája az a tény, hogy az AD FS nem engedélyezi az üres jogcímeket. Így amikor jogcímeket `http://contoso.com/ws/2016/02/identity/claims/objectguid` ad hozzá, és **msdsconsistencyguid** `http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid` az 1. Ha nincs feltöltve, az AD FS látja, hogy üres értékkel rendelkezik, és azonnal eldobja. Minden objektumnak **objectGuid azonosítója**lesz, így a jogcím mindig ott lesz az 1.

**3. szabály: Az ms-ds-consistencyguid kiadása nem módosítható azonosítóként, ha jelen van**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"]
    => issue(Type = "http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID", Value = c.Value);

Ez egy implicit **Exist** csekk. Ha a jogcím értéke létezik, akkor ezt adja ki nem módosítható azonosítóként. Az előző példa a **névazonosító** jogcímet használja. Ezt a környezetben a nem módosítható azonosító megfelelő jogcímtípusra kell módosítania.

**4. szabály: Az objektumguid kiadása nem módosítható azonosítóként, ha az ms-ds-consistencyGuid nincs jelen**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID", Value = c2.Value);

Ebben a szabályban egyszerűen csak az ideiglenes **jelzőt**ellenőrzi. Ön dönti el, hogy a jogcímet az értéke alapján adja-e ki.

> [!NOTE]
> Ezeknek a szabályoknak a sorrendje fontos.

### <a name="sso-with-a-subdomain-upn"></a>SSO altartoménnyal UPN

Az Azure AD Connect használatával több tartományt is hozzáadhat, ahogy azt [az Új összevont tartomány hozzáadása](how-to-connect-fed-management.md#addfeddomain)című része ismerteti. Az Azure AD Connect 1.1.553.0-s és legújabb verziója automatikusan létrehozza a megfelelő jogcímszabályt a issuerID számára. Ha nem tudja használni az Azure AD Connect 1.1.553.0-s vagy legújabb verzióját, ajánlott, hogy az [Azure AD RPT jogcímszabály-szabályok](https://aka.ms/aadrptclaimrules) eszköz használatával hozza létre és állítsa be a megfelelő jogcímszabályokat az Azure AD függő entitás megbízhatóságához.

## <a name="next-steps"></a>További lépések
További információ a [felhasználói bejelentkezési lehetőségekről.](plan-connect-user-signin.md)
