---
title: 'Azure AD Connect: felhőalapú hitelesítés – szakaszos bevezetés | Microsoft Docs'
description: Ismerteti, hogyan lehet áttelepíteni az összevont hitelesítésből a felhőbe történő áttelepítést egy előkészített bevezetéssel.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/28/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c8f4250d2df7703ad2960c2d68cd015cb6ec2bc
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73052878"
---
# <a name="cloud-authentication-staged-rollout-public-preview"></a>Felhőalapú hitelesítés: szakaszos bevezetés (nyilvános előzetes verzió)

Ez a funkció lehetővé teszi az összevont hitelesítésből a felhőalapú hitelesítésre való átállítást egy előkészített megközelítés használatával.

Az összevont hitelesítéstől való elmozdulás következményei vannak. Ha például a következők valamelyike van:
    
-  helyszíni MFA-kiszolgáló 
-  intelligens kártyákat használ a hitelesítéshez 
-  más összevonási funkciók

Ezeket a funkciókat a felhőalapú hitelesítésre való áttérés előtt figyelembe kell venni.  A szolgáltatás kipróbálása előtt javasoljuk, hogy tekintse át az útmutatót a megfelelő hitelesítési módszer kiválasztásához. További részletekért tekintse meg [ezt a táblázatot](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn#comparing-methods) .

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Előfeltételek

-   Rendelkezik egy összevont tartománnyal rendelkező Azure AD-Bérlővel.

-   Úgy döntött, hogy áthelyezi a jelszó-kivonat szinkronizálása + zökkenőmentes egyszeri bejelentkezés **(A kapcsoló)** vagy az átmenő hitelesítés + zökkenőmentes egyszeri bejelentkezés **(B. lehetőség)** egyikét. Habár a zökkenőmentes egyszeri bejelentkezés nem kötelező, javasoljuk, hogy a zökkenőmentes SSO-t úgy engedélyezze, hogy a tartományhoz csatlakoztatott gépeket használó felhasználók számára csendes bejelentkezési élményt biztosítson a vállalati hálózaton belül.

-   Konfigurálta az összes olyan megfelelő bérlői arculatot és feltételes hozzáférési szabályzatot, amelyre a rendszer a Felhőbeli hitelesítésre áttelepített felhasználók számára szükséges.

-   Ha az Azure Multi-Factor Authentication használatát tervezi, javasoljuk, hogy hozzon létre [konvergens regisztrációt az önkiszolgáló jelszó-visszaállítás (SSPR) és az Azure MFA](../authentication/concept-registration-mfa-sspr-combined.md) használatával, hogy a felhasználók csak egyszer regisztrálják a hitelesítési módszereiket.

-   A szolgáltatás használatához globális rendszergazdának kell lennie a bérlőn.

-   Ha a zökkenőmentes SSO-t egy adott AD-erdőben szeretné engedélyezni, tartományi rendszergazdának kell lennie.

## <a name="supported-scenarios"></a>Támogatott esetek

Ezek a forgatókönyvek a szakaszos bevezetésnél támogatottak:

- Ez a funkció csak az Azure AD-hez Azure AD Connect használatával kiépített felhasználók számára működik, és nem alkalmazható kizárólag felhőalapú felhasználók számára.

- Ez a funkció csak a böngészőkben és a modern hitelesítési ügyfeleken lévő felhasználói bejelentkezési forgalom esetében működik. Az örökölt hitelesítést használó alkalmazások vagy felhőalapú szolgáltatások visszatérnek az összevont hitelesítési folyamatokhoz. (Példa: az Exchange Online-ban a modern hitelesítés ki van kapcsolva, vagy az Outlook 2010, amely nem támogatja a modern hitelesítést.)

## <a name="unsupported-scenarios"></a>Nem támogatott forgatókönyvek

A következő forgatókönyvek nem támogatottak a szakaszos bevezetésnél:

- Bizonyos alkalmazások a hitelesítés során elküldik a "tartomány\_hint" lekérdezési paramétert az Azure AD-nek. Ezek a folyamatok továbbra is folytatódnak, és az előkészített bevezetéshez engedélyezett felhasználók továbbra is az összevonás-hitelesítést használják.

<!-- -->

- A rendszergazda biztonsági csoportok használatával képes kideríteni a Felhőbeli hitelesítést. (A Felhőbeli biztonsági csoportok használata ajánlott, hogy elkerülje a szinkronizálás késését a helyszíni AD biztonsági csoportok használatakor.)

    - **Szolgáltatásként legfeljebb 10 csoportot**használhat; azaz a jelszó-kivonatok szinkronizálási/átmenő hitelesítése/zökkenőmentes egyszeri bejelentkezés esetén.

    - Beágyazott csoportok **nem támogatottak**. Ez a nyilvános előzetes verzió hatóköre.

    - A szakaszos bevezetéshez **nem használhatók** dinamikus csoportok.

    - A csoporton belüli kapcsolattartási objektumok letiltják a hozzáadásra kerülő űrlapokat.

- Az összevont és a felhőalapú hitelesítés utolsó átváltás továbbra is a Azure AD Connect vagy a PowerShell használatával kell történnie. Ez a szolgáltatás nem vált át tartományokat a összevonása-ből a felügyelt értékekre.

- Amikor először ad hozzá egy biztonsági csoportot a szakaszos bevezetéshez, az a 200 felhasználók számára korlátozott, hogy elkerülje az UX időtúllépését. Miután hozzáadta a csoportot az UX-hez, szükség szerint további felhasználókat adhat hozzá közvetlenül a csoporthoz.

## <a name="get-started-with-staged-rollout"></a>Ismerkedés a lépcsőzetes bevezetéssel

Ha a jelszó-kivonatolási szinkronizálást (PHS) a szakaszos bevezetéssel szeretné tesztelni, végezze el az alábbi előtti lépéseket a jelszó-kivonat szinkronizálásának szakaszos bevezetésének engedélyezéséhez.

A használt PowerShell-parancsmagokkal kapcsolatos további információkért lásd: [AzureAD 2,0 előzetes](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout) verzió

## <a name="pre-work-for-password-hash-sync"></a>A jelszó-kivonat szinkronizálásának előzetes munkája

1. Engedélyezze a jelszó-kivonatok szinkronizálását a Azure AD Connect [választható szolgáltatások](how-to-connect-install-custom.md#optional-features) lapján. 

   ![Képernyőkép a Azure Active Directory Connect választható szolgáltatások oldaláról](media/how-to-connect-staged-rollout/sr1.png)

1. Győződjön meg arról, hogy a jelszó kivonatának teljes szinkronizálási ciklusa fut, így minden felhasználó jelszavas kivonata szinkronizálva lett az Azure AD-vel. Az [itt](tshoot-connect-password-hash-synchronization.md) található PowerShell-diagnosztika használatával ellenőrizhető a jelszó-kivonatolási szinkronizálás állapota.

   ![Képernyőkép a AADConnect hibaelhárítási naplójáról](./media/how-to-connect-staged-rollout/sr2.png)

Ha tesztelni kívánja a lépcsőzetes bevezetéssel való átmenő hitelesítésre (PTA ESP) való bejelentkezést, végezze el az alábbi előtti lépéseket a PTA szakaszos bevezetés engedélyezéséhez.

## <a name="pre-work-for-pass-through-authentication"></a>Az átmenő hitelesítés előzetes munkája

1. Azonosítson egy Windows Server 2012 R2 vagy újabb rendszert futtató kiszolgálót, amelyen át szeretné futtatni a pass Authentication agentet (**ne válassza a Azure ad Connect-kiszolgálót**). Győződjön meg arról, hogy a kiszolgáló tartományhoz van csatlakoztatva, a kijelölt felhasználók hitelesítését Active Directory, és képes kommunikálni az Azure AD-vel a kimenő portokon/URL-címeken (lásd a részletes [előfeltételeket](how-to-connect-sso-quick-start.md)).

1. [Töltse le](https://aka.ms/getauthagent) & telepítse a Microsoft Azure ad csatlakozási hitelesítési ügynököt a kiszolgálón. 

1. A [magas rendelkezésre állás](how-to-connect-sso-quick-start.md)engedélyezéséhez telepítsen további hitelesítési ügynököket más kiszolgálókra.

1. Győződjön meg arról, hogy megfelelően konfigurálta az [intelligens zárolás beállításait](../authentication/howto-password-smart-lockout.md) . Ezzel biztosíthatja, hogy a felhasználók helyszíni Active Directory fiókjai ne legyenek kizárva a rossz szereplőkkel.

Javasoljuk, hogy engedélyezze a zökkenőmentes SSO-t, függetlenül az előkészített bevezetéshez kiválasztott bejelentkezési módszertől (PHS vagy PTA). A szakaszos bevezetéshez a zökkenőmentes egyszeri bejelentkezés engedélyezéséhez végezze el az alábbi előzetes munkát.

## <a name="pre-work-for-seamless-sso"></a>A zökkenőmentes egyszeri bejelentkezéshez szükséges előzetes munka

Engedélyezze a zökkenőmentes egyszeri bejelentkezést az AD-erdőkön a PowerShell használatával. Ha több AD-erdővel rendelkezik, minden egyes erdőhöz engedélyezze ugyanezt. A zökkenőmentes egyszeri bejelentkezés csak a szakaszos bevezetéshez kiválasztott felhasználók esetében aktiválódik, és nem érinti a meglévő összevonási beállításokat.

**A lépések összefoglalása**

1. Először jelentkezzen be Azure AD Connect kiszolgálóra.

2. Navigáljon a (z)% ProgramFiles%\\Microsoft Azure Active Directory Connect mappába.

3. Importálja a zökkenőmentes SSO PowerShell-modult a következő parancs használatával: `Import-Module .\\AzureADSSO.psd1`.

4. Futtassa a PowerShellt rendszergazdaként. A PowerShellben hívja meg a `New-AzureADSSOAuthenticationContext`. Ehhez a parancshoz meg kell adni egy párbeszédpanelt, amelyen megadhatja a bérlő globális rendszergazdai hitelesítő adatait.

5. Hívja meg `Get-AzureADSSOStatus \| ConvertFrom-Json`. Ez a parancs az Active Directory-erdők listáját tartalmazza (tekintse meg a \"tartományok\" listáját), amelyen a funkció engedélyezve van. Alapértelmezés szerint a a bérlő szintjén hamis értékre van állítva.

   > **Példa:** 
   > ![példa a Windows PowerShell kimenetére](./media/how-to-connect-staged-rollout/sr3.png)

6. Hívja meg `\$creds = Get-Credential`. Ha a rendszer kéri, adja meg a kívánt Active Directory-erdő tartományi rendszergazdai hitelesítő adatait.

7. Hívja meg `Enable-AzureADSSOForest -OnPremCredentials \$creds`. Ez a parancs létrehoz egy AZUREADSSOACC számítógépfiókot a helyszíni tartományvezérlőről ehhez az adott Active Directory erdőhöz, amely szükséges a zökkenőmentes egyszeri bejelentkezéshez.

8. A zökkenőmentes egyszeri bejelentkezéshez az intranet zónában URL-címek szükségesek. Az URL-címek Csoportházirendek használatával történő üzembe helyezéséhez tekintse meg a [zökkenőmentes egyszeri bejelentkezési](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature) útmutatót.

9.  Emellett letöltheti a zökkenőmentes egyszeri bejelentkezéshez készült [üzembe helyezési terveket](https://aka.ms/SeamlessSSODPDownload) a teljes útmutatóhoz.

## <a name="enable-staged-rollout"></a>Szakaszos bevezetés engedélyezése

A következő lépésekkel egy adott szolgáltatást (áteresztő hitelesítés/jelszó-kivonat szinkronizálása/zökkenőmentes SSO) hozhat létre egy csoportba tartozó felhasználók közül:

### <a name="enable-the-staged-rollout-of-a-specific-feature-on-your-tenant"></a>Egy adott szolgáltatás lépcsőzetes bevezetésének engedélyezése a bérlőn

Az alábbi lehetőségek közül választhat

-   Jelszó-kivonat szinkronizálása + zökkenőmentes egyszeri bejelentkezés **(A kapcsoló)**

-   Átmenő hitelesítés + zökkenőmentes egyszeri bejelentkezés **(B lehetőség)**

-   Jelszó-kivonat szinkronizálása + átmenő hitelesítés + zökkenőmentes SSO **-\>** ***nem támogatott***

Hajtsa végre a következő lépéseket:

1. Jelentkezzen be az Azure AD-portálra az alábbi URL-cím használatával az előnézet UX eléréséhez.

   > <https://aka.ms/stagedrolloutux>

2. Kattintson a lépcsőzetes bevezetésének engedélyezése a felügyelt felhasználói bejelentkezéshez (előzetes verzió) elemre.

   *Például:* (**B. lehetőség**) Ha engedélyezni szeretné a jelszó-kivonatok szinkronizálását és a zökkenőmentes egyszeri bejelentkezést, a jelszó-kivonatolási szinkronizálást és a zökkenőmentes egyszeri bejelentkezési funkciókat a lent látható módon csúsztassa a **"on"** értékre.

   ![](./media/how-to-connect-staged-rollout/sr4.png)

   ![](./media/how-to-connect-staged-rollout/sr5.png)

3. Adja hozzá a megfelelő csoportokat a szolgáltatáshoz az átmenő hitelesítés és a zökkenőmentes egyszeri bejelentkezés engedélyezéséhez. Győződjön meg arról, hogy a biztonsági csoportok nem rendelkeznek több mint 200 taggal az UX-időtúllépés elkerüléséhez.

   ![](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >A csoport tagjai automatikusan engedélyezve vannak az előkészített bevezetéshez. A beágyazott és a dinamikus csoportok nem támogatottak a szakaszos bevezetéshez.

## <a name="auditing"></a>Naplózás

Engedélyezte a naplózási eseményeket a szakaszos bevezetéshez végrehajtott különböző műveletekhez.

- Naplózási esemény, ha engedélyezi az előkészített bevezetést a jelszó-kivonat szinkronizálása/átmenő hitelesítés/zökkenőmentes egyszeri bejelentkezés esetén.

  >[!NOTE]
  >Naplózási esemény, amely akkor jelentkezett be, amikor a SeamlessSSO be van kapcsolva **a** StagedRollout használatával.

  ![](./media/how-to-connect-staged-rollout/sr7.png)

  ![](./media/how-to-connect-staged-rollout/sr8.png)

- Naplózási esemény, ha egy csoport hozzá lett adva a jelszó kivonatának szinkronizálásához/átmenő hitelesítéshez/zökkenőmentes egyszeri bejelentkezéshez.

  >[!NOTE]
  >Naplózási esemény, ha a rendszer hozzáad egy csoportot a jelszó-kivonatolási szinkronizáláshoz a szakaszos bevezetéshez

  ![](./media/how-to-connect-staged-rollout/sr9.png)

  ![](./media/how-to-connect-staged-rollout/sr10.png)

- Naplózási esemény, ha a csoportba felvett felhasználó engedélyezve van a szakaszos bevezetéshez

  ![](media/how-to-connect-staged-rollout/sr11.png)

  ![](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>Ellenőrzés

A bejelentkezés jelszavas kivonatoló szinkronizálással vagy átmenő hitelesítéssel történő teszteléséhez (felhasználónév/jelszó bejelentkezés):

1. Tallózással keresse meg <https://myapps.microsoft.com> egy privát böngésző-munkamenetet az extranetről, és adja meg a UserPrincipalName (UPN) a Fázisos kivezetéshez kiválasztott felhasználói fiókhoz.

1. Ha a felhasználó a szakaszos bevezetést célozta meg, a felhasználó nem lesz átirányítva az összevont bejelentkezési oldalra, és a rendszer kérni fogja, hogy jelentkezzen be az Azure AD bérlői bejelentkezési oldalára.

1. Győződjön meg arról, hogy a bejelentkezés sikeresen megtörtént az [Azure ad bejelentkezési tevékenység jelentésében](../reports-monitoring/concept-sign-ins.md) a userPrincipalName való szűréssel.

A bejelentkezés zökkenőmentes egyszeri bejelentkezéssel történő tesztelése:

1. Tallózással <https://myapps.microsoft.com>keresse meg az intraneten található privát böngésző-munkamenetet, és adja meg a UserPrincipalName (UPN) beállítást a Fázisos kivezetéshez.

1. Ha a felhasználó a zökkenőmentes SSO szakaszos bevezetését célozza meg, a felhasználó a következőt fogja látni: "kísérlet a bejelentkezésre..." üzenet a beavatkozás nélküli bejelentkezés előtt.

1. Győződjön meg arról, hogy a bejelentkezés sikeresen megtörtént az [Azure ad bejelentkezési tevékenység jelentésében](../reports-monitoring/concept-sign-ins.md) a userPrincipalName való szűréssel.

Ha szeretné, hogy a felhasználói bejelentkezések még mindig megtörténjenek az összevonási szolgáltatókon:

Az alábbi lépésekkel követheti nyomon, hogy a felhasználói bejelentkezések továbbra is a AD FSon történnek-e a kiválasztott szakaszos bevezetési felhasználók számára [ezen utasítások](https://docs.microsoft.com/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events)használatával. A gyártói dokumentációban megtudhatja, hogyan kell ezt megnéznie a harmadik féltől származó összevonási szolgáltatókon.

## <a name="roll-back"></a>Visszaállítás

### <a name="remove-a-user-from-staged-rollout"></a>Felhasználó eltávolítása az előkészített bevezetésből

1.  Ha eltávolítja a felhasználót a csoportból, letiltja a felhasználó szakaszos bevezetését.

2.  Ha le szeretné tiltani a szakaszos bevezetési funkciót, a szakaszos bevezetés kikapcsolásához csúsztassa vissza a funkciót **"off"** állapotba.


## <a name="frequently-asked-questions"></a>Gyakori kérdések

-   **K: az ügyfél használhatja ezt a képességet éles környezetben?**

-   A: igen, ez a funkció használható az üzemi bérlőben, de javasoljuk, hogy először próbálja ki ezt a funkciót a tesztelési bérlőben.

-   **K: használhatja ezt a szolgáltatást egy állandó "közös létezés" fenntartására, ahol egyes felhasználók összevont hitelesítést használnak, és más felhőalapú hitelesítésre is használhatók?**

-   A: nem, ez a funkció az összevontról a felhőbe való Migrálás fázisokban való áttelepítésére, majd a Felhőbeli hitelesítésre való áttérésre lett tervezve. Nem ajánlott állandó vegyes állapotot javasolni, mivel ez váratlan hitelesítési folyamatokhoz vezethet.

-   **K: használhatom a PowerShellt a szakaszos bevezetéshez?**

-   Válasz: igen, keresse meg a dokumentációt, hogy a PowerShell használatával hajtsa végre a szakaszos bevezetést itt.

## <a name="next-steps"></a>Következő lépések
- [AzureAD 2,0 előzetes verzió](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
