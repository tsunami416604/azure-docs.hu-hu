---
title: Az Azure AD Connect – az AD FS kezelése és testreszabása |} A Microsoft Docs
description: AD FS-kezelőben az Azure AD Connect és a felhasználó AD FS bejelentkezési élmény az Azure AD Connect és a PowerShell segítségével testre szabhatja.
keywords: Az AD FS, ADFS, az AD FS felügyeleti, az AAD Connect, csatlakozás, bejelentkezési, az AD FS testreszabás, javítsa ki a megbízhatósági kapcsolat, O365, összevonást, a függő entitás
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 5597d75da50853e85d6e94f1a5c7b5114068f671
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37916996"
---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Kezelés és testreszabás Active Directory összevonási szolgáltatások az Azure AD Connect használatával
Ez a cikk bemutatja, hogyan kezelheti, és testre szabhatja az Active Directory összevonási szolgáltatások (AD FS) Azure Active Directory (Azure AD) Connect használatával. Más általános az AD FS-feladatokat, amelyeket az AD FS-farm teljes konfiguráció megtenni, előfordulhat, hogy is tartalmaz.

| Témakör | Ez fedezi |
|:--- |:--- |
| **Az AD FS kezelése** | |
| [A bizalmi kapcsolat javítása](#repairthetrust) |Hogyan javítsa ki az összevonási megbízhatósági kapcsolat, az Office 365-tel. |
| [Az Azure AD-bA a másodlagos bejelentkezési Azonosítót összevonása ](#alternateid) | Alternatív bejelentkezési azonosítóval összevonás konfigurálása  |
| [Az AD FS-kiszolgáló hozzáadása](#addadfsserver) |Hogyan bővít ki egy további AD FS-kiszolgálót az AD FS farmot. |
| [Az AD FS webalkalmazás-Proxy kiszolgáló hozzáadása](#addwapserver) |Hogyan bővít ki egy AD FS-farm további webalkalmazás-proxykiszolgálóként (WAP)-kiszolgálóról. |
| [Összevont tartomány hozzáadása](#addfeddomain) |Hogyan adható hozzá egy összevont tartomány. |
| [Az SSL-tanúsítvány frissítése](active-directory-aadconnectfed-ssl-update.md)| Hogyan frissíthető egy AD FS-farm SSL-tanúsítványát. |
| **Az AD FS testreszabása** | |
| [Adjon hozzá egy egyéni vállalati emblémát vagy ábra](#customlogo) |Az AD FS bejelentkezési oldal vállalati embléma és illusztráció testreszabásának módját. |
| [A bejelentkezési leírásának hozzáadása](#addsignindescription) |Hogyan bejelentkezésilap-leírást. |
| [Az AD FS-jogcímszabályok módosítása](#modclaims) |Hogyan lehet módosítani az AD FS-jogcímek a különböző összevonásos helyzetekben. |

## <a name="manage-ad-fs"></a>Az AD FS kezelése
Az Azure AD Connect minimális felhasználói beavatkozás különböző AD FS kapcsolatos feladatokat végez az Azure AD Connect varázsló használatával. Ha végzett a varázsló futtatásával az Azure AD Connectet telepíti, a varázsló futtatása újra hajthat végre további feladatokat.

## <a name="repairthetrust"></a>A bizalmi kapcsolat javítása 
Az Azure AD Connect használatával ellenőrizze, az AD FS és az Azure AD megbízik, és a megfelelő műveletet, a bizalmi kapcsolat javítása az aktuális állapotát. Kövesse az alábbi lépéseket az Azure AD-kijavításához és az AD FS-megbízhatóság.

1. Válassza ki **javítási aad-ben és az AD FS-megbízhatóság** további feladatok listájából.
   ![Javítsa ki az aad-ben és az AD FS megbízható](media/active-directory-aadconnect-federation-management/RepairADTrust1.PNG)

2. Az a **az Azure AD Connect** lapon adja meg az Azure AD globális rendszergazdai hitelesítő adataival, és kattintson a **tovább**.
   ![Csatlakozás az Azure AD-hez](media/active-directory-aadconnect-federation-management/RepairADTrust2.PNG)

3. Az a **távelérési hitelesítő adatainak** lap, adja meg a tartományi rendszergazda hitelesítő adatait.

   ![Távoli hozzáférési hitelesítő adatok](media/active-directory-aadconnect-federation-management/RepairADTrust3.PNG)

    Miután rákattintott **tovább**, az Azure AD Connect ellenőrzi a tanúsítvány állapotának és az esetleges problémákat jeleníti meg.

    ![Tanúsítvány állapota](media/active-directory-aadconnect-federation-management/RepairADTrust4.PNG)

    A **konfigurálásra kész** lap kijavításához a bizalmi kapcsolat végrehajtott műveletek listáját jeleníti meg.

    ![Ready to configure (Konfigurálásra kész)](media/active-directory-aadconnect-federation-management/RepairADTrust5.PNG)

4. Kattintson a **telepítése** a megbízhatóság javításához.

> [!NOTE]
> Az Azure AD Connect is csak kijavítása vagy önaláírt tanúsítványokat való reagálás. Az Azure AD Connect nem tudja kijavítani a harmadik féltől származó tanúsítványok.

## <a name="alternateid"></a>Az Azure AD szolgáltatással AlternateID összevonása 
Javasoljuk, hogy a helyszíni felhasználó egyszerű felhasználóneve, és a felhőben, egyszerű felhasználónév is ugyanaz marad. Ha a helyszíni egyszerű Felhasználónévvel használ (például nem átirányítható tartomány Contoso.local), vagy nem módosítható miatt a helyi alkalmazások függőségei, javasoljuk, hogy állítsa be másodlagos bejelentkezési azonosítót. Másodlagos bejelentkezési Azonosítót lehetővé teszi, hogy felhasználói bejelentkezés beállítása ahol a felhasználók nem egyszerű, például a mail attribútum be lehet jelentkezni. A választás egyszerű felhasználónév az Azure AD Connect, az alapértelmezett érték a userPrincipalName attribútum az Active Directoryban. Ha bármely egyéb attribútumuk egyszerű felhasználónév kiválasztása és összevonja a az AD FS használatával, majd az Azure AD Connect lesz az AD FS konfigurálása alternatív bejelentkezési azonosítóval. Alább látható egy példa az egyszerű felhasználónév egy másik attribútum kiválasztása:

![Másik azonosító attribútum kiválasztása](media/active-directory-aadconnect-federation-management/attributeselection.png)

Alternatív bejelentkezési azonosító konfigurálása az AD FS-hez két fő lépésből áll:
1. **Állítsa be az megfelelő kiállítási jogcímek**: A kiállítási jogcímszabályok az az Azure AD függő entitás megbízhatósági módosultak, hogy a másik azonosító a felhasználó a kiválasztott UserPrincipalName attribútum használja.
2. **Engedélyezze a másodlagos bejelentkezési Azonosítót a az AD FS konfigurációt**: az AD FS konfigurációt, hogy az AD FS használatával a másik azonosítót. a megfelelő erdőkben található felhasználókat kereshet frissül. Ez a konfiguráció az AD FS Windows Server 2012 R2 (KB2919355) vagy újabb rendszeren támogatott. Ha az AD FS-kiszolgáló 2012 R2, az Azure AD Connect ellenőrzi, hogy a szükséges KB-os. Ha a KB nem észlel, egy figyelmeztetés fog megjelenni konfiguráció befejezése után alább látható módon:

    ![Hiányzó KB-os 2012R2 kapcsolatos figyelmeztetés](media/active-directory-aadconnect-federation-management/kbwarning.png)

    Több tartomány az Azure AD Connect használatával leírtak szerint adhat hozzá [adjon hozzá egy új összevont tartományt](http://go.microsoft.com/fwlink/?LinkID=396590).

> [!NOTE]
> Az Azure AD Connect verziója 1.1.553.0 és a legújabb automatikusan létrehozza a megfelelő jogcímszabály issuerID számára.](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configuring-alternate-login-id)

## <a name="addadfsserver"></a>Az AD FS-kiszolgáló hozzáadása 

> [!NOTE]
> Ha nem használhatja az Azure AD Connect verziója 1.1.553.0 vagy legújabb, javasoljuk, hogy Azure AD RPT Jogcímszabályok eszköz létrehozásához, és állítsa be az Azure AD függő entitás megbízhatóságához a megfelelő jogcímszabályok segítségével. Tudjon meg többet felhasználói bejelentkezési lehetőségek.

1. Válassza ki **további összevonási kiszolgáló telepítése**, és kattintson a **tovább**.

   ![További összevonási kiszolgáló](media/active-directory-aadconnect-federation-management/AddNewADFSServer1.PNG)

2. Az a **az Azure AD Connect** lapon adja meg az Azure AD globális rendszergazdai hitelesítő adataival, és kattintson a **tovább**.

   ![Csatlakozás az Azure AD szolgáltatáshoz](media/active-directory-aadconnect-federation-management/AddNewADFSServer2.PNG)

3. Adja meg a tartomány rendszergazdai hitelesítő adatait.

   ![Tartományi rendszergazdai hitelesítő adatokkal](media/active-directory-aadconnect-federation-management/AddNewADFSServer3.PNG)

4. Az Azure AD Connect kéri a jelszót az új AD FS-farm konfigurálása az Azure AD Connecttel során megadott PFX-fájl. Kattintson a **jelszó megadása** PFX-fájljának jelszónak a megadását.

   ![Tanúsítványjelszó](media/active-directory-aadconnect-federation-management/AddNewADFSServer4.PNG)

    ![SSL-tanúsítvány megadása](media/active-directory-aadconnect-federation-management/AddNewADFSServer5.PNG)

5. Az a **AD FS-kiszolgálók** lap, adja meg a kiszolgáló nevét vagy IP-cím lehet hozzáadni az AD FS-farm.

   ![AD FS-kiszolgálók](media/active-directory-aadconnect-federation-management/AddNewADFSServer6.PNG)

6. Kattintson a **tovább**, és végezze el a végső **konfigurálása** lapot. Miután az Azure AD Connect befejezte a kiszolgálók hozzáadását az AD FS-farm, egyúttal arra, hogy ellenőrizze a kapcsolatot.

   ![Ready to configure (Konfigurálásra kész)](media/active-directory-aadconnect-federation-management/AddNewADFSServer7.PNG)

    ![A telepítés befejeződött](media/active-directory-aadconnect-federation-management/AddNewADFSServer8.PNG)

## <a name="addwapserver"></a>Az AD FS WAP-kiszolgáló hozzáadása 

> [!NOTE]
> A WAP-kiszolgáló hozzáadása az Azure AD Connect a PFX-tanúsítvány szükséges. Ezért csak ezt a műveletet is végezhet, ha az AD FS-farm konfigurált Azure AD Connect használatával.

1. Válassza ki **webalkalmazás-Proxy üzembe helyezése** elérhető feladatok listájában.

   ![Webalkalmazás-Proxy telepítéséhez](media/active-directory-aadconnect-federation-management/WapServer1.PNG)

2. Adja meg az Azure globális rendszergazdai hitelesítő adatokkal.

   ![Csatlakozás az Azure AD szolgáltatáshoz](media/active-directory-aadconnect-federation-management/wapserver2.PNG)

3. Az a **adja meg az SSL-tanúsítvány** lap, adja meg a jelszót, amely az Azure AD Connect az AD FS-farm konfigurálásakor megadott PFX-fájl.
   ![Tanúsítvány jelszava](media/active-directory-aadconnect-federation-management/WapServer3.PNG)

    ![SSL-tanúsítvány megadása](media/active-directory-aadconnect-federation-management/WapServer4.PNG)

4. Adja hozzá a kiszolgálót a WAP-kiszolgáló számára. Mivel előfordulhat, hogy a WAP-kiszolgáló nem csatlakozik a tartományhoz, a varázsló a hozzáadni kívánt kiszolgálón rendszergazdai hitelesítő adatokat kér.

   ![A felügyeleti kiszolgáló hitelesítő adatai](media/active-directory-aadconnect-federation-management/WapServer5.PNG)

5. Az a **Proxy megbízhatósági hitelesítő adatai** lap, adja meg a rendszergazdai hitelesítő adatokat konfigurálni a proxyt, megbízható, és a az AD FS-farm elsődleges kiszolgálóját eléréséhez.

   ![Proxy megbízhatósági hitelesítő adatai](media/active-directory-aadconnect-federation-management/WapServer6.PNG)

6. Az a **konfigurálásra kész** oldalon, a varázslóban végrehajtott műveletek listáját jeleníti meg.

   ![Ready to configure (Konfigurálásra kész)](media/active-directory-aadconnect-federation-management/WapServer7.PNG)

7. Kattintson a **telepítése** az a konfiguráció befejezéséhez. A konfiguráció befejezését követően a varázslóban teszi, az a-kiszolgálók elérhetőségének ellenőrzése. Kattintson a **ellenőrizze** , ellenőrizze a kapcsolatot.

   ![A telepítés befejeződött](media/active-directory-aadconnect-federation-management/WapServer8.PNG)

## <a name="addfeddomain"></a>Összevont tartomány hozzáadása 

Az Azure AD Connect használatával összevonni az Azure AD-tartomány hozzáadása könnyebbé vált. Az Azure AD Connect a tartományt összevonásra hozzáadja, és módosítja a jogcímszabályok a kibocsátó megfelelően megfelelően, ha az Azure ad-vel összevont több tartomány.

1. Összevont tartomány hozzáadása, válassza ki a feladatot **hozzá egy további Azure AD-tartomány**.

   ![További Azure AD-tartomány](media/active-directory-aadconnect-federation-management/AdditionalDomain1.PNG)

2. A varázsló következő oldalán adja meg Azure ad-ben a globális rendszergazdai hitelesítő adatokkal.

   ![Csatlakozás az Azure AD szolgáltatáshoz](media/active-directory-aadconnect-federation-management/AdditionalDomain2.PNG)

3. Az a **távelérési hitelesítő adatainak** lap, adja meg a tartomány rendszergazdai hitelesítő adatait.

   ![Távoli hozzáférési hitelesítő adatok](media/active-directory-aadconnect-federation-management/additionaldomain3.PNG)

4. A következő oldalon a varázsló akkor is összevonható a helyszíni címtár az Azure AD-tartomány listáját tartalmazza. Válassza ki a tartományt a listából.

   ![Azure AD domain](media/active-directory-aadconnect-federation-management/AdditionalDomain4.PNG)

    Miután kiválasztotta a tartományhoz, a varázsló biztosít további a varázsló által elvégzendő műveleteket és a konfiguráció hatásának megfelelő információt. Bizonyos esetekben egy tartományhoz, amely még nem ellenőrizte az Azure ad-ben, ha a varázsló nyújt segítséget a tartomány ellenőrzéséhez. Lásd: [az egyéni tartománynév hozzáadása az Azure Active Directory](../active-directory-domains-add-azure-portal.md) további részletekért.

5. Kattintson a **Tovább** gombra. A **konfigurálásra kész** lapján láthatja azokat a műveleteket, amelyeket az Azure AD Connect elvégzi. Kattintson a **telepítése** az a konfiguráció befejezéséhez.

   ![Ready to configure (Konfigurálásra kész)](media/active-directory-aadconnect-federation-management/AdditionalDomain5.PNG)

> [!NOTE]
> A hozzáadott összevont tartomány felhasználóinak szinkronizálása előtt fognak tudni bejelentkezni az Azure AD kell.

## <a name="ad-fs-customization"></a>Az AD FS Testreszabás
Az alábbiakban néhány gyakori feladatot, amelyet lehetséges, hogy az AD FS bejelentkezési oldal testreszabása végrehajtásához részleteit.

## <a name="customlogo"></a>Adjon hozzá egy egyéni vállalati emblémát vagy ábra 
A megjelenő vállalatembléma módosításához a **bejelentkezési** lapon, a következő Windows PowerShell-parancsmagot és szintaxist.

> [!NOTE]
> Az emblémát a javasolt dimenziók 260 x 35 pixel, legfeljebb 10 KB-os fájlméretben mérete 96 dpi.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [!NOTE]
> A *TargetName* paraméter megadása kötelező. Az alapértelmezett témájának és az AD FS alapértelmezett neve.

## <a name="addsignindescription"></a>A bejelentkezési leírásának hozzáadása 
A bejelentkezésilap-leírást a **bejelentkezési oldal**, használja a következő Windows PowerShell-parancsmagot és szintaxist.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

## <a name="modclaims"></a>Az AD FS-jogcímszabályok módosítása 
Az AD FS támogatja egy gazdag jogcím nyelvet használó egyéni jogcímszabályok létrehozása. További információkért lásd: [a Jogcímszabály nyelvének szerepe](https://technet.microsoft.com/library/dd807118.aspx).

A következő szakaszok ismertetik, hogyan írhat az egyes forgatókönyvekhez kapcsolódik az Azure ad-ben egyéni szabályok és az AD FS összevonási.

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>Egy érték van jelen, az attribútum az a feltétele nem módosítható azonosító
Az Azure AD Connect teszi lehetővé, a forráshorgony használt objektumok szinkronizálva lesznek az Azure AD-attribútum. Ha az egyéni attribútum értéke nem üres, érdemes egy nem módosítható azonosító jogcímet.

Kiválaszthatja például **ms-ds-consistencyguid** a forráshorgony és a probléma attribútumként **ImmutableID** , **ms-ds-consistencyguid** a használatieset-attribútum értéke alapján. Ha nincs szemben az attribútum értéke, **objectGuid** nem módosítható azonosító néven A következő szakaszban leírtak szerint egyéni jogcímszabályok készlete hozhatnak létre.

**1. szabály: Lekérdezés attribútumok**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

Ez a szabály a értékeit lekérdezésekor **ms-ds-consistencyguid** és **objectGuid** a felhasználó az Active Directoryból. Módosítsa a tároló nevét az AD FS központi telepítésben a megfelelő Tárolónév. Is módosítása a jogcímek írja be a megfelelő jogcím-e az összevonási típust meghatározottak szerint **objectGuid** és **ms-ds-consistencyguid**.

Ezenkívül használatával **hozzáadása** , és nem **probléma**, hozzáadása az entitáshoz tartozó kimenő probléma elkerüléséhez, és az értékeket is használhat, mint köztes értékek. A későbbi szabályokban jogcímet állít ki, melyik értéket használja, mint a nem módosítható azonosítója. a létrehozása után

**2. szabály: Ellenőrizze, hogy a felhasználó létezik-e az ms-ds-consistencyguid**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Ez a szabály meghatározása jelző ideiglenes nevű **idflag** , amely **useguid** esetén nincs **ms-ds-consistencyguid** kitölti a rendszer a felhasználó számára. Ez mögötti logika az a tény, hogy az AD FS nem engedélyezi üres jogcímeket. Igen, jogcímek hozzáadásakor http://contoso.com/ws/2016/02/identity/claims/objectguid és http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid az 1. szabály, hogy a kialakított egy **msdsconsistencyguid** csak jogcímet, ha az érték a felhasználó van feltöltve. Ha ez nincs feltöltve, az AD FS látja, hogy üres értéket fog rendelkezni, és azonnal csökken. Összes objektum lesz **objectGuid**, így a jogcím mindig ott 1. szabály végrehajtása után.

**3. szabály: Nem módosítható azonosító ki az ms-ds-consistencyguid, ha telepítve**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

Ez az egy implicit **Exist** ellenőrzése. Ha a jogcím értéke már létezik, majd adja ki, amely nem módosítható azonosító néven Az előző példában a **nameidentifier** jogcím. A környezetben nem módosítható azonosító módosíthatja a megfelelő jogcím típusa kell.

**4. szabály: ObjectGuid nem módosítható azonosító adja ki, ha ms-ds-consistencyGuid nem található**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

Ebben a szabályban, már egyszerűen ellenőrzése az ideiglenes jelző **idflag**. Eldöntheti, hogy-e a jogcímet ad hozzá tartozó érték alapján.

> [!NOTE]
> Ezek a szabályok sorrendjének fontos.

### <a name="sso-with-a-subdomain-upn"></a>Az SSO UPN altartomány

Több tartomány az Azure AD Connect használatával leírtak szerint adhat hozzá [adjon hozzá egy új összevont tartományt](active-directory-aadconnect-federation-management.md#addfeddomain). Az Azure AD Connect verziója 1.1.553.0 és a legújabb automatikusan létrehozza a megfelelő jogcímszabály issuerID számára. Ha nem használhatja az Azure AD Connect verziója 1.1.553.0 vagy legújabb, javasoljuk, hogy [Azure AD RPT Jogcímszabályok](https://aka.ms/aadrptclaimrules) eszköz létrehozásához, és állítsa be az Azure AD függő entitás megbízhatóságához a megfelelő jogcímszabályok segítségével.

## <a name="next-steps"></a>További lépések
Tudjon meg többet [felhasználói bejelentkezési lehetőségek](active-directory-aadconnect-user-signin.md).
