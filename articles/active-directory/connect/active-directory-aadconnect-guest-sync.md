---
title: E-mail cím használata a bejelentkezés az Azure vendég felhasználók felhasználói fiókok szinkronizálása |} Microsoft Docs
description: Ez a cikk ismerteti a Vendég felhasználói fiókok, amelyek a másik azonosító bejelentkezés az alkalmazások szinkronizálása.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/19/2018
ms.author: billmath
ms.openlocfilehash: f450747cac06a416950efc6c37c1058c1cb40775
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="synchronizing-guest-user-accounts-that-use-email-for-sign-in-preview"></a>E-mailt a bejelentkezéshez (előzetes verzió) használó Vendég felhasználói fiókok szinkronizálása

>[!NOTE]
> Ez a funkció jelenleg nyilvános előzetes verziójához.

Az alábbi forgatókönyvet cím a helyzet, ahol előfordulhat, hogy külső felhasználók számára a helyszíni AD-környezetben, például a partnerek, egy alternatív bejelentkezési mód használata.

Az előző példában Nina Morin működik, a Fabrikam, és hogy legyen, a következő e-mail cím: nmorin@fabrikam.com. Nina Contoso partnert, és ő bizonyos alkalmazásokhoz, amely a Contoso rendelkezik engedéllyel kell rendelkeznie. Contoso Nina a létrehozott-e fiókot, és e-mail címet segítségével bejelentkezés az alkalmazások Nina van irányítva.

A helyszíni alkalmazások ebben a forgatókönyvben a nagy dolgozott. Azonban, a Contoso van áthelyezése ezeket az alkalmazásokat a felhőbe, és rendelkezik a felhasználói élmény ugyanaz a partnerek számára szeretné.

Ez a forgatókönyv ebben a helyzetben megoldást.


## <a name="pre-requisites-and-assumptions"></a>Szükséges előfeltételeket és Előfeltételek
Ez a szakasz a szükséges előfeltételeket és állíthatja be a forgatókönyv megkezdése előtt tisztában kell feltételezéseket listáját tartalmazza.

### <a name="pre-requisites"></a>Előfeltételek
- Globális rendszergazdai hitelesítő adatokat az Azure AD Connect konfigurálásához ellenőrizze a tartományok, és tartomány-összevonási beállításainak konfigurálása
- Az Azure AD Connect 1.1.524.0 verzió vagy újabb
- A felhő külső felhasználók egyszerű Felhasználónevük beállítása ellenőrzött tartomány (például: bmcontoso.com).
- Összevonási szolgáltatás a külső felhasználók hitelesítésére. Active Directory összevonási szolgáltatások használatakor kell lennie a 2012 R2 vagy újabb
- MSOL PowerShell v1.1 telepítve van a gép összevonási beállításainak ellenőrzése. További információkért lásd: [Azure Active Directoryban (MSOnline)](https://docs.microsoft.com/en-us/powershell/azure/active-directory/overview?view=azureadps-1.0).


### <a name="assumptions"></a>Előfeltételek 
- Az Azure AD Connect már beállítása és telepítése sikeresen befejeződött. Az Azure AD Connect telepítésével információkért lásd: [az Azure AD Connect és összevonási](active-directory-aadconnectfed-whatis.md).
Ez a dokumentum lehetővé teszi a következő előfeltételek:
- hogy rendelkezik beállítása összevonási szolgáltatás és az általa sikeresen hitelesíti magát felhasználók.
- külső felhasználók hitelesítheti használt külső e-mail címüket.
- - A másik azonosító használata bejelentkezés beállítása és konfigurálása megtörtént. Felhasználók képes hitelesíteni a másik azonosítójával. Az AD FS a másik azonosító beállításával kapcsolatos további információkért lásd: [másodlagos bejelentkezési azonosító konfigurálása](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configuring-alternate-login-id).

## <a name="task-1--prepare-the-environment"></a>1. feladat: A környezet előkészítése
A következő feladat több olyan tájékoztató, úgy, hogy készen áll a szinkronizálás a külső fiókjait, hogy azok is jelentkezzen be használatával alternatív indítása i, például a mail attribútum.

Határozzák meg az alábbi táblázatban a második feladatütemezési lépés előtt.

![Architektúra](media/active-directory-aadconnect-guest-sync/guest2.png)

|Környezet aspektusa|Az alkalmazott?|A környezetben végrehajtása|
|-----|-----|-----|
|Felhő UPN attribútum|Az attribútum, amely feltölti az egyszerű Felhasználónevet, a külső felhasználói objektum, a felhőben. Az egyszerű Felhasználónévi utótagot a külső fiók megadva a szükséges előfeltételek kell lennie. Ez az ellenőrzött tartomány.|* Példa: UserPrincipalName (nmorin@bmcontoso.com)|
|Bejelentkezési címe|Az attribútum, amely a külső felhasználók be kell írnia bejelentkezéskor. Ez az attribútum rendelkeznie kell egy e-mail cím formátumú, és a legtöbb esetben egybevág a külső felhasználó tényleges e-mail címét.|* Példa: az e-mail (nmorin@fabrikam.com)|
|Az Azure AD Connect hatókörön belüli szűrő|A szűrő, amely lehetővé teszi, hogy a külső identitások, a szinkronizálási szabályok az útmutató későbbi részében definiált hatókörének célzó. Hatókör szokásos módjai a következők: egy előre definiált szervezeti Egységnek a szervezet bizonyos elnevezési szabályokat alkalmaz, egy adott tartományban, stb.|* Példa: A szervezeti egység tartalmaz összesen|
|Az Azure AD-bérlő|Az Azure AD Connect, mert az Azure AD-bérlő neve jelenik meg.|Példa: contoso.onmicrosoft.com|

Az alábbi képernyőfelvételen rendelkezik leírt három jelölőnégyzetéből.
- A **Externals** szervezeti egység, amely az Azure AD Connect hatókörű szűrő szerepel, és a külső felhasználók helyét.
- A **mail** attribútum, amely segítségével a külső felhasználók bejelentkezési.
- A **userPrincipalName** attribútum, amely a helyszíni környezetben össze van vonva az ellenőrzött tartomány.

![Felhasználó](media/active-directory-aadconnect-guest-sync/guest1.png)

## <a name="task-2--configure-azure-ad-connect"></a>2. feladat: Konfigurálása az Azure AD Connect
Ha elvégezte a fenti adatokat definiált, jelenleg áthelyezheti beállítása az Azure AD Connect szinkronizálási szabályait. A szabályok beállítása, a szerkesztővel az Azure AD Connect szinkronizálási szabályokat. A szerkesztő további információkért lásd: [Declaritive kiépítés](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

### <a name="how-to-configure-the-synchronization-rule"></a>A szinkronizálási szabály konfigurálása
A következő eljárással konfigurálhatja az Azure AD Connect.

1. Nyissa meg az Azure AD Connect szinkronizálási szabály szerkesztőt címen **Start - az Azure AD Connect - szinkronizálás szabályok szerkesztő**.
2. Az a **szinkronizálási szabályok szerkesztő** képernyőn, győződjön meg arról, a iránya **bejövő** , és kattintson a jobb oldali **új szabály hozzáadása**.
3. Az a **leírás** lapon konfigurálja a következőket, majd kattintson **következő**.
    - **Név** -adja meg a szabály nevét 
    - **Csatlakoztatott rendszer:** -a helyszíni AD-környezet
    - **Csatlakoztatott rendszer objektumtípus:** -felhasználó
    - **Metaverzum-objektum típusa:** -személy
    - **Kapcsolat típusa:** -csatlakozás
    - **Sorrend:** – 90
    - 
![](media/active-directory-aadconnect-guest-sync/guest3.png)

4. Az a **hatókör-beállítási szűrője** kattintson **csoport hozzáadása**.
5. A legördülő listákat segítségével konfigurálhatja a szűrőt. Írja be a következőt, és kattintson a **következő**. Ez a művelet létrehoz egy szűrő, amely csak a külső szervezeti egységben található objektumokra vonatkozik.
    - **Attribútum** -megkülönböztető név
    - **Operátor** -tartalmaz
    - **Érték** -összesen
 
 ![Szűrés](media/active-directory-aadconnect-guest-sync/guest4.png)

6. Az a **csatlakozás szabályok** kattintson **következő**.
7. Az a **átalakítások** kattintson **hozzáadása átalakítása**. Adja meg a következő információkat:
    - **FlowType** - konstans
    - **Cél attribútum** -userType
    - **Forrás** - vendégek
8. Az a **átalakítások** kattintson **hozzáadása átalakítása**. Adja meg a következő információkat:
    - **FlowType** – közvetlen
    - **Cél attribútum** -onPremisesUserPrincipalName
    - **Forrás** -mail
9. Az a **átalakítások** kattintson **hozzáadása átalakítása**. Adja meg a következő információkat:
    - **FlowType** – közvetlen
    - **Cél attribútum** -userPrincipalName
    - **Forrás** -userPrincipalName ![átalakítása](media/active-directory-aadconnect-guest-sync/guest5.png)
10. Kattintson a **Hozzáadás** parancsra. 
11. Az a **szinkronizálási szabályok szerkesztő** képernyőn, győződjön meg arról, a iránya **kimenő** , és kattintson a jobb oldali **új szabály hozzáadása**.
12. Az a **leírás** lapon konfigurálja a következőket, majd kattintson **következő**.
    - **Név** -adja meg a szabály nevét 
    - **Csatlakoztatott rendszer:** – az Azure AD-bérlő
    - **Csatlakoztatott rendszer objektumtípus:** -felhasználó
    - **Metaverzum-objektum típusa:** -személy
    - **Kapcsolattípus** -csatlakozás
    - **Sorrend:** – 90
    - 
![Szabály](media/active-directory-aadconnect-guest-sync/guest6.png)

13. Az a **Scoping szűrő** kattintson **következő**.
14. Az a **csatlakozás szabályok** kattintson **következő**.
15. Az a **átalakítások** kattintson **hozzáadása átalakítása**.  Adja meg a következő információkat:
    - **FlowType** – közvetlen
    - **Cél attribútum** -userType
    - **Forrás** -userType
9. Az a **átalakítások** kattintson **hozzáadása átalakítása**. Adja meg a következő információkat:
    - **FlowType** – közvetlen
    - **Cél attribútum** -onPremisesUserPrincipalName
    - **Forrás** -onPremisesUserPrincipalName ![átalakítása](media/active-directory-aadconnect-guest-sync/guest7.png)
10. Kattintson a **Hozzáadás** parancsra. 
11. Ezek a szabályok konfigurálása után teljes szinkronizálást futtatásához szükséges. A PowerShell szolgáltatás használatával teljes szinkronizálást indítani. A szinkronizálás befejeződése után folytathatja a következő lépéssel.

``` powershell
    Start-ADSyncSyncCycle -PolicyType Initial
```

## <a name="task-3--federation"></a>3. feladat: összevonási
A következő feladatot egy tájékoztató a szempontokat kell rendelkeznie a helyi ahhoz, hogy a forgatókönyv működjön.

A Azure-ban az Azure AD PowerShell segítségével ellenőrizheti a összevonási beállításait. Ez a dokumentum MSOL PowerShell v1.1 használja. Ebben a verzióban telepítése [Itt](https://docs.microsoft.com/en-us/powershell/azure/active-directory/overview?view=azureadps-1.0).

### <a name="verify-the-federation-settings"></a>Az összevonási beállításainak ellenőrzése
A következő eljárással összevonási beállításainak ellenőrzése.
1. Nyissa meg a Windows Powershellt, és csatlakoztatása az Azure AD a következő parancsot:
``` powershell
      Connect-MSOLservice
```
2.  Adja meg a globális rendszergazdai hitelesítő adatokat
3.  Most adja meg a következő parancsot:
  ``` powershell
      Get-MSOLDomainFederationSettings
  ```
4.   Figyelje meg, az összevonási adatokat vissza kell adni. Megjegyzés: a **ActiveLogonUri** az összevonási kiszolgáló URL-címe.

  ![Összevonás](media/active-directory-aadconnect-guest-sync/guest8.png)

### <a name="verify-alternate-login-id"></a>Ellenőrizze a másodlagos bejelentkezési Azonosítóval
Ez a dokumentum az AD FS használja, mint az identitásszolgáltató (Idp). Ha egy másik Idp használ, ezeket a lépéseket nagyon is.

1. Nyissa meg a Windows Powershellt, és adja meg a következő parancsot:
   ```powershell
    Get-ADFSClaimsProviderTrust
   ```
2. Az AD FS információkat kell megjelennie.  Megjegyzés: a **AlternateLoginID** és **LookupForests**.

![ADFS](media/active-directory-aadconnect-guest-sync/guest9.png)

## <a name="task-4--testing"></a>4. feladat: tesztelése
Győződjön meg arról, hogy ez megfelelően működik, kell jelentkezzen be egy végpontot, amely a kiállító terjesztési hely hitelesítést konfigurálták. Ennek teszteléséhez azt egy webhely, az Azure-ban telepítve, és használja a következő URL-cím: contososampapp.azurewebsites.net

### <a name="verify-that-you-can-sign-in-with-the-alternate-id"></a>Győződjön meg arról, hogy akkor is jelentkezzen be a másik azonosító
1. Jelentkezzen be a végponthoz.</br>
![végpont](media/active-directory-aadconnect-guest-sync/guest10.png)
1. Megadja a felhasználónevét, és a rendszer újra átirányítja az összevonási bejelentkezési oldalára.
![Összevonási](media/active-directory-aadconnect-guest-sync/guest11.png)
1. Adja meg a hitelesítő adatokat.
2. Meg kell sikeres bejelentkezés megtörtént.
![Sikeres](media/active-directory-aadconnect-guest-sync/guest12.png)

## <a name="next-steps"></a>További lépések
- [Egy Azure Active Directory B2B együttműködés felhasználó tulajdonságai](../../active-directory/active-directory-b2b-user-properties.md#key-properties-of-the-azure-ad-b2b-collaboration-user)
- [Másodlagos bejelentkezési azonosító konfigurálása](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)
- [Az Azure AD Connect verziókiadásai](active-directory-aadconnect-version-history.md)
