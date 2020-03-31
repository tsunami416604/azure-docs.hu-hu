---
title: 'Azure AD Connect: Felhőalapú hitelesítés szakaszos bevezetéssel | Microsoft dokumentumok'
description: Ez a cikk bemutatja, hogyan lehet áttelepíteni az összevont hitelesítésről a felhőalapú hitelesítésre egy szakaszos bevezetés használatával.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3044ebdd716eb85dc63d3a77089912d0d51d8b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74915233"
---
# <a name="migrate-to-cloud-authentication-by-using-staged-rollout-preview"></a>Átköltözött a felhőalapú hitelesítésre szakaszos közzétételhasználatával (előzetes verzió)

Szakaszos bevezetési módszer használatával áttelepítheti az összevont hitelesítésről a felhőalapú hitelesítésre. Ez a cikk a váltás módját ismerteti. A szakaszos bevezetés megkezdése előtt azonban figyelembe kell vennie a következményeket, ha az alábbi feltételek közül egy vagy több teljesül:
    
-  Jelenleg egy helyszíni többtényezős hitelesítési kiszolgálót használ. 
-  Intelligens kártyákat használ a hitelesítéshez. 
-  A jelenlegi kiszolgáló csak összevonási szolgáltatásokat kínál.

Mielőtt kipróbálná ezt a funkciót, javasoljuk, hogy tekintse át a megfelelő hitelesítési módszer kiválasztásáról szóló útmutatónkat. További információt az Azure Active Directory [hibrid identitáskezelési megoldásának megfelelő hitelesítési módszer kiválasztása](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn#comparing-methods)című témakör "Módszerek összehasonlítása" című táblázatában talál.

A szolgáltatás áttekintéséhez tekintse meg ezt az "Azure Active Directory: Mi a szakaszos bevezetés?" című témakörben talál áttekintést. Videóinak:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Előfeltételek

-   Egy Azure Active Directory (Azure AD) bérlő összevont tartományok.

-   Úgy döntött, hogy két lehetőség közül választhat:
    - **A. lehetőség***jelszókivonat-szinkronizálás (szinkronizálás)* + *zökkenőmentes egyszeri bejelentkezés (SSO)*  - 
    - **B.** - lehetőség*áthaladási hitelesítése* + *zökkenőmentes egyszeri bejelentkezés*
    
    Bár *a zökkenőmentes egyszeri bejelentkezés* nem kötelező, azt javasoljuk, hogy lehetővé tegye a csendes bejelentkezési élményt a felhasználók számára, akik tartományhoz csatlakozó gépeket futtatnak a vállalati hálózaton belülről.

-   Beállította az összes megfelelő bérlői márkajelzési és feltételes hozzáférési szabályzatot, amely a felhőalapú hitelesítésre áttelepíti a szükséges felhasználókat.

-   Ha az Azure többtényezős hitelesítést kíván használni, azt javasoljuk, hogy [az önkiszolgáló jelszó-visszaállítás (SSPR) és a többtényezős hitelesítés konvergens regisztrációját](../authentication/concept-registration-mfa-sspr-combined.md) használja, hogy a felhasználók egyszer regisztrálhassák a hitelesítési módszereiket.

-   A szakaszos bevezetési funkció használatához globális rendszergazdának kell lennie a bérlőn.

-   Ahhoz, hogy egy adott Active Directory erdőben *zökkenőmentes egyszeri bejelentkezés* legyen, tartományi rendszergazdának kell lennie.

## <a name="supported-scenarios"></a>Támogatott esetek

A következő forgatókönyvek támogatottak a szakaszos bevezetéshez. A funkció csak a következő célokra működik:

- Azok a felhasználók, akik az Azure AD-be vannak kiépítve az Azure AD-hez az Azure AD Connect használatával. Nem vonatkozik a csak felhőalapú felhasználókra.

- Felhasználói bejelentkezési forgalom a böngészőkben és a *modern hitelesítési* ügyfeleken. Az örökölt hitelesítést használó alkalmazások vagy felhőszolgáltatások az összevont hitelesítési folyamatokra fognak visszaesni. Ilyen lehet például az Exchange online, amelynek korszerű hitelesítése ki van kapcsolva, vagy az Outlook 2010, amely nem támogatja a modern hitelesítést.

## <a name="unsupported-scenarios"></a>Nem támogatott forgatókönyvek

A következő esetek nem támogatottak a szakaszos bevezetéshez:

- Bizonyos alkalmazások a hitelesítés során elküldik a "domain_hint" lekérdezési paramétert az Azure AD-nek. Ezek a folyamatok folytatódnak, és a szakaszos bevezetésre engedélyezett felhasználók továbbra is az összevonást fogják használni a hitelesítéshez.

<!-- -->

- A rendszergazdák biztonsági csoportok használatával is bevezethetik a felhőalapú hitelesítést. A szinkronizálási késés elkerülése érdekében, ha helyszíni Active Directory biztonsági csoportokat használ, javasoljuk, hogy használjon felhőalapú biztonsági csoportokat. A következő feltételek vonatkoznak:

    - Szolgáltatásonként legfeljebb 10 csoportot használhat. Ez azt, hogy 10 csoportot használhat *a jelszókivonat-szinkronizáláshoz*, *az átmenő hitelesítéshez*és a *zökkenőmentes Egyszeri bejelentkezéshez.*
    - A beágyazott csoportok *nem támogatottak.* Ez a hatókör a nyilvános előzetes verzióra is vonatkozik.
    - A dinamikus csoportok *nem támogatottak* a szakaszos bevezetéshez.
    - A csoporton belüli kapcsolattartó objektumok megakadályozzák a csoport hozzáadását.

- Továbbra is meg kell tennie a végső kivágást az összevont felhőalapú hitelesítésről az Azure AD Connect vagy a PowerShell használatával. A szakaszos bevezetés nem vált tartományokat összevontról felügyeltre.

- Amikor először ad hozzá biztonsági csoportot a szakaszos bevezetéshez, legfeljebb 200 felhasználóra van korlátozva, hogy elkerülje a felhasználói időtúltöltést. Miután hozzáadta a csoportot, szükség szerint további felhasználókat is hozzáadhat hozzá közvetlenül.

## <a name="get-started-with-staged-rollout"></a>Első lépések a szakaszos bevezetéssel

A *jelszókivonat-szinkronizálási* bejelentkezés szakaszos bevezetéssel történő teszteléséhez kövesse a következő szakaszban található munka előtti utasításokat.

A használandó PowerShell-parancsmagokról az [Azure AD 2.0 előzetes verziójában](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)talál tájékoztatást.

## <a name="pre-work-for-password-hash-sync"></a>A jelszókivonat-szinkronizálás előmunkálata

1. Engedélyezze a *jelszókivonat-szinkronizálást* az Azure AD Connect [választható szolgáltatások](how-to-connect-install-custom.md#optional-features) lapjáról. 

   ![Képernyőkép az Azure Active Directory Connect "Választható szolgáltatások" lapjáról](media/how-to-connect-staged-rollout/sr1.png)

1. Győződjön meg arról, hogy a teljes *jelszókivonat-szinkronizálási* ciklus fut, hogy a felhasználók jelszókivonatai szinkronizálva legyenek az Azure AD-vel. A *jelszókivonat-szinkronizálás*állapotának ellenőrzéséhez használhatja a PowerShell-diagnosztikát az [Azure AD Connect szinkronizálásával való jelszókivonat-szinkronizálás hibaelhárítása](tshoot-connect-password-hash-synchronization.md)című témakörben.

   ![Képernyőkép az AADConnect hibaelhárítási naplójáról](./media/how-to-connect-staged-rollout/sr2.png)

Ha azt szeretné, hogy tesztelje *az átmenő hitelesítési* bejelentkezés szakaszos bevezetés használatával, engedélyezze azt a következő szakaszban található munka előtti utasításokat követve.

## <a name="pre-work-for-pass-through-authentication"></a>Előmunka az átmenő hitelesítéshez

1. Azonosítsa azt a kiszolgálót, amelyen Windows Server 2012 R2 vagy újabb rendszer fut, és ahol futtatni szeretné az *átmenő hitelesítési* ügynököt. 

   *Ne* válassza az Azure AD Connect-kiszolgálót.Győződjön meg arról, hogy a kiszolgáló tartományhoz csatlakozik, hitelesítheti a kijelölt felhasználókat az Active Directoryval, és kommunikálhat az Azure AD-vel a kimenő portokon és URL-címeken. További információ: "1. lépés: Az előfeltételek ellenőrzése" című rövid [útmutató: Azure AD zökkenőmentes egyszeri bejelentkezés](how-to-connect-sso-quick-start.md)című témakörben talál.

1. [Töltse le az Azure AD Connect hitelesítési ügynököt](https://aka.ms/getauthagent), és telepítse a kiszolgálóra. 

1. A [magas rendelkezésre állás](how-to-connect-sso-quick-start.md)engedélyezéséhez telepítsen további hitelesítési ügynököket más kiszolgálókra.

1. Ellenőrizze, hogy megfelelően konfigurálta-e az [intelligens zárolási beállításokat.](../authentication/howto-password-smart-lockout.md) Ezzel biztosíthatja, hogy a felhasználók helyszíni Active Directory-fiókjai ne zárják ki a rossz szereplők.

Javasoljuk, hogy engedélyezze a *zökkenőmentes egyszeri bejelentkezést,* függetlenül a bejelentkezési módszertől *(jelszókivonat-szinkronizálás* vagy *átadó hitelesítés),* amelyet a szakaszos bevezetéshez választ. A *zökkenőmentes egyszeri bejelentkezés*engedélyezéséhez kövesse a következő szakaszban található munka előtti utasításokat.

## <a name="pre-work-for-seamless-sso"></a>Munka előtti megoldás a zökkenőmentes egyszeri bejelentkezéshez

Zökkenőmentes *egyszeri bejelentkezés* engedélyezése az Active Directory-erdőkben a PowerShell használatával. Ha egynél több Active Directory-erdővel rendelkezik, engedélyezze azt minden egyes erdőhöz külön-külön.  *A zökkenőmentes egyszeri bejelentkezés* csak a szakaszos bevezetésre kijelölt felhasználók számára aktiválódik. Nincs hatással a meglévő összevonási beállításokra.

*A zökkenőmentes egyszeri bejelentkezés* engedélyezése az alábbi módon:

1. Jelentkezzen be az Azure AD Connect server be.

2. Nyissa meg a *%programfiles%\\Microsoft Azure Active Directory Connect* mappát.

3. Importálja a *zökkenőmentes SSO* PowerShell modult a következő parancs futtatásával: 

   `Import-Module .\AzureADSSO.psd1`

4. Futtassa a PowerShellt rendszergazdaként. A PowerShellben `New-AzureADSSOAuthenticationContext`hívja a . Ez a parancs megnyit egy ablaktáblát, ahol megadhatja a bérlő globális rendszergazdai hitelesítő adatait.

5. Hívja `Get-AzureADSSOStatus | ConvertFrom-Json`a hívást. Ez a parancs azoknak az Active Directory erdőknek a listáját jeleníti meg (lásd a "Tartományok" listát), amelyeken ez a szolgáltatás engedélyezve van. Alapértelmezés szerint a bérlői szinten hamis értékre van állítva.

   ![Példa a Windows PowerShell-kimenetre](./media/how-to-connect-staged-rollout/sr3.png)

6. Hívja `$creds = Get-Credential`a hívást. A kérdésben adja meg a kívánt Active Directory-erdő tartományi rendszergazdai hitelesítő adatait.

7. Hívja `Enable-AzureADSSOForest -OnPremCredentials $creds`a hívást. Ez a parancs létrehozza az AZUREADSSOACC számítógépfiókot a helyszíni tartományvezérlőről az Active Directory erdőszámára, amely a *zökkenőmentes egyszeri bejelentkezéshez*szükséges.

8. *A zökkenőmentes egyszeri bejelentkezéshez* az URL-címeknek az intranetzónában kell lenniük. Ha ezeket az URL-címeket csoportházirendekkel szeretné telepíteni, olvassa [el a Rövid útmutató: Azure AD zökkenőmentes egyszeri bejelentkezés című témakört.](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature)

9. A teljes forgatókönyv, akkor is letölthető [a telepítési tervek](https://aka.ms/SeamlessSSODPDownload) zökkenőmentes Egyszeri *bejelentkezés.*

## <a name="enable-staged-rollout"></a>Szakaszos bevezetés engedélyezése

Ha egy adott szolgáltatást *(átadó hitelesítés,* *jelszókivonat-szinkronizálás*vagy *zökkenőmentes egyszeri bejelentkezést*) szeretne a csoport egy kiválasztott felhasználói csoportjának, kövesse a következő szakaszokutasításait.

### <a name="enable-a-staged-rollout-of-a-specific-feature-on-your-tenant"></a>Adott szolgáltatás szakaszos bevezetésének engedélyezése a bérlőn

Az alábbi lehetőségek közül választhat:

- **A. lehetőség***jelszókivonat-szinkronizálás* + *zökkenőmentes egyszeri bejelentkezés*  - 
- **B.** - lehetőség*áthaladási hitelesítése* + *zökkenőmentes egyszeri bejelentkezés*
- **Nem támogatott** - *jelszókivonat-szinkronizálási* + *átmenő hitelesítés* + *zökkenőmentes egyszeri bejelentkezés*

Tegye a következőket:

1. Az előzetes felhasználói felület eléréséhez jelentkezzen be az [Azure AD-portálra.](https://aka.ms/stagedrolloutux)

2. Válassza a **Szakaszos bevezetés engedélyezése felügyelt felhasználói bejelentkezési (előzetes) hivatkozáshoz.**

   Ha például engedélyezni szeretné *az A lehetőséget,* csúsztassa a **Jelszókivonat-szinkronizálást** és a **Zökkenőmentes egyszeri bejelentkezés** vezérlőket a **Be**mezőre, ahogy az az alábbi képeken látható.

   ![Az Azure AD Connect lap](./media/how-to-connect-staged-rollout/sr4.png)

   ![A "Szakaszos bevezetési funkciók engedélyezése (előzetes verzió) " lap](./media/how-to-connect-staged-rollout/sr5.png)

3. Adja hozzá a csoportokat a szolgáltatáshoz az *átmenő hitelesítés* és a *zökkenőmentes egyszeri bejelentkezés engedélyezéséhez.* A felhasználói élmény időmeghosszabbítása érdekében győződjön meg arról, hogy a biztonsági csoportok kezdetben legfeljebb 200 tagot tartalmaznak.

   ![A "Csoportok kezelése a jelszókivonat-szinkronizáláshoz (előzetes verzió) " lap](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >A csoport tagjai automatikusan engedélyezve vannak a szakaszos bevezetéshez. A beágyazott és a dinamikus csoportok nem támogatottak a szakaszos bevezetéshez.

## <a name="auditing"></a>Naplózás

Engedélyeztük a naplózási eseményeket a szakaszos bevezetéshez végrehajtott különböző műveletekhez:

- Naplózási esemény , ha engedélyezi a *jelszókivonat-szinkronizálás,* *az átmenő hitelesítés*vagy a *zökkenőmentes egyszeri bejelentkezés szakaszos bevezetését.*

  >[!NOTE]
  >Egy naplózási esemény naplózva, ha *a zökkenőmentes egyszeri bejelentkezés* be van kapcsolva a szakaszos bevezetés használatával.

  ![A "Bevezetési szabályzat létrehozása a szolgáltatáshoz" ablaktábla – Tevékenység lap](./media/how-to-connect-staged-rollout/sr7.png)

  ![A "Bevezetési házirend létrehozása a szolgáltatáshoz" ablaktábla – Módosított tulajdonságok lap](./media/how-to-connect-staged-rollout/sr8.png)

- Naplózási esemény , ha egy csoportot hozzáadnak a *jelszókivonat-szinkronizáláshoz*, *az átmenő hitelesítéshez*vagy *a zökkenőmentes egyszeri bejelentkezéshez*.

  >[!NOTE]
  >A rendszer naplóz egy naplózási eseményt, amikor egy csoportot hozzáad a *jelszókivonat-szinkronizáláshoz* a szakaszos bevezetéshez.

  ![A "Csoport hozzáadása a szolgáltatás bevezetéséhez" ablaktábla – Tevékenység lap](./media/how-to-connect-staged-rollout/sr9.png)

  ![A "Csoport hozzáadása a szolgáltatás bevezetéséhez" ablaktábla – Módosított tulajdonságok lap](./media/how-to-connect-staged-rollout/sr10.png)

- Naplózási esemény, ha a csoporthoz hozzáadott felhasználó engedélyezve van a szakaszos bevezetéshez.

  ![A "Felhasználó hozzáadása a szolgáltatás bevezetéséhez" ablaktábla – Tevékenység lap](media/how-to-connect-staged-rollout/sr11.png)

  ![A "Felhasználó hozzáadása a szolgáltatás bevezetéséhez" ablaktábla – Cél(ok) lap](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>Ellenőrzés

A bejelentkezés *jelszókivonat-szinkronizálással* vagy *átadó hitelesítéssel* (felhasználónév és jelszó bejelentkezés) való teszteléséhez tegye a következőket:

1. Az extraneten lépjen az [Alkalmazások lapra](https://myapps.microsoft.com) egy privát böngészőmunkamenetben, majd adja meg a szakaszos bevezetésre kiválasztott felhasználói fiók UserPrincipalName (UPN) elemét.

   Azok a felhasználók, akiket szakaszos közzétételre céloztak, nem lesznek átirányítva az összevont bejelentkezési oldalra. Ehelyett a rendszer arra kéri őket, hogy jelentkezzenek be az Azure AD-bérlői márkájú bejelentkezési lapon.

1. Győződjön meg arról, hogy a bejelentkezés sikeresen megjelenik az [Azure AD bejelentkezési tevékenység jelentésben](../reports-monitoring/concept-sign-ins.md) a UserPrincipalName szűréssel.

A bejelentkezés *tesztelése zökkenőmentes egyszeri bejelentkezéssel:*

1. Az intraneten lépjen az [Alkalmazások lapra](https://myapps.microsoft.com) egy privát böngészőmunkamenetben, majd írja be a szakaszos bevezetésre kijelölt felhasználói fiók UserPrincipalName (UPN) elemét.

   Azok a felhasználók, akiket a *zökkenőmentes Egyszeri bejelentkezés* szakaszos bevezetésére céloztak meg, egy "Kipróbálás-bejelentkezés..." üzenetben, mielőtt csendben bejelentkeznének.

1. Győződjön meg arról, hogy a bejelentkezés sikeresen megjelenik az [Azure AD bejelentkezési tevékenység jelentésben](../reports-monitoring/concept-sign-ins.md) a UserPrincipalName szűréssel.

   Az Active Directory összevonási szolgáltatásokban (AD FS) továbbra is előforduló felhasználói bejelentkezések nyomon követéséhez kövesse az [AD FS hibaelhárítási témakörének utasításait: Események és naplózás](https://docs.microsoft.com/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events). Ellenőrizze a szállítói dokumentációt arról, hogyan ellenőrizheti ezt a külső összevonási szolgáltatóknál.

## <a name="remove-a-user-from-staged-rollout"></a>Felhasználó eltávolítása a szakaszos bevezetésből

Ha eltávolít egy felhasználót a csoportból, letiltja az adott felhasználó szakaszos bevezetését. A szakaszos bevezetési funkció letiltásához csúsztassa vissza a vezérlőt Off .To disable the staged rollout feature, slide the control back to **Off**.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**K: Használhatom ezt a képességet éles környezetben?**

A: Igen, használhatja ezt a funkciót az éles környezetben, de azt javasoljuk, hogy először próbálja ki a teszt bérlő.

**K: Használható ez a funkció az állandó "együttélés" fenntartására, ahol egyes felhasználók összevont hitelesítést használnak, míg mások felhőalapú hitelesítést?**

A: Nem, ez a funkció az összevont ról a felhőalapú hitelesítésre való áttérést szakaszokban, majd végül a felhőalapú hitelesítésre való áttérésre tervezték. Nem javasoljuk, hogy állandó vegyes állapotban, mert ez a megközelítés nem várt hitelesítési folyamatokhoz vezethet.

**K: Használhatom a PowerShellt szakaszos bevezetésre?**

V: Igen. Ha meg szeretné tudni, hogyan használhatja a PowerShellt a szakaszos bevezetés végrehajtásához, olvassa el az [Azure AD előzetes verzió.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)

## <a name="next-steps"></a>További lépések
- [Az Azure AD 2.0 előzetes verzió](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
