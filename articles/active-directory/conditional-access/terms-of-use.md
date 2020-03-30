---
title: Használati feltételek - Azure Active Directory | Microsoft dokumentumok
description: Az Azure Active Directory használati feltételeinek használatának első lépései az alkalmazottak vagy a vendégek tájékoztatására, mielőtt elérne.
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2f06a7c88a7c17f5f93201192664c2d4a97564e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480963"
---
# <a name="azure-active-directory-terms-of-use"></a>Az Azure Active Directory használati feltételei

Az Azure AD használati feltételei egy egyszerű módszert biztosít, amelyet a szervezetek a végfelhasználóknak történő információszolgáltatásként használhatnak. Ez a bemutató gondoskodik arról, hogy a felhasználók megkapják a jogi vagy megfelelőségi követelményekre vonatkozó nyilatkozatokat. Ez a cikk a használati feltételek használatának megkezdését ismerteti.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Áttekintő videók

Az alábbi videó gyors áttekintést nyújt a használati feltételekről.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

További videók:
- [Használati feltételek üzembe helyezése az Azure Active Directoryban](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Használati feltételek bevezetése az Azure Active Directoryban](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>Mire használható a használati feltételek?

Az Azure AD használati feltételei a következő képességekkel rendelkeznek:

- A hozzáférés megszerzése előtt az alkalmazottaknak vagy a vendégeknek el kell fogadniuk a használati feltételeket.
- A hozzáférés megszerzése előtt az alkalmazottaknak vagy a vendégeknek minden eszközön el kell fogadniuk az Ön használati feltételeit.
- Az alkalmazottaknak vagy vendégeknek ismétlődő ütemezés szerint el kell fogadniuk a használati feltételeket.
- A biztonsági adatok Azure multifaktorhitelesítésben (MFA) történő regisztrálása előtt az alkalmazottaknak vagy a vendégeknek el kell fogadniuk a használati feltételeket.
- Az Azure AD önkiszolgáló jelszó-visszaállítás (SSPR) biztonsági adatainak regisztrálása előtt az alkalmazottaktól megkövetelheti a használati feltételek elfogadását.
- Általános használati feltételek bemutatása a szervezet összes felhasználója számára.
- Konkrét használati feltételek bemutatása a felhasználói attribútumok alapján (pl. orvosok kontra nővérek, belföldi kontra nemzetközi alkalmazottak [dinamikus csoportjai](../users-groups-roles/groups-dynamic-membership.md)).
- Mutasson be konkrét használati feltételeket a nagy üzleti hatású alkalmazások, például a Salesforce elérésekor.
- Jelen használati feltételek különböző nyelveken.
- Azok nak a listája, akik elfogadták vagy nem fogadták el a használati feltételeket.
- Segítséget nyújt az adatvédelmi előírások betartásában.
- A megfelelőségi és naplózási használati feltételeknaplójának megjelenítése.
- A [Microsoft Graph API-k](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement) használatával hozzon létre és kezelje a használati feltételeket (jelenleg előzetes verzióban).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD használati feltételeinek használatához és konfigurálásához a következőkre van szükség:

- Prémium szintű Azure AD P1, P2, EMS E3 vagy EMS E5 előfizetés.
   - Ha még nem rendelkezik ezen előfizetések egyikével sem, [beszerezhet Prémium szintű Azure AD előfizetést](../fundamentals/active-directory-get-started-premium.md), vagy [engedélyezheti a Prémium szintű Azure AD előfizetés próbaverzióját](https://azure.microsoft.com/trial/get-started-active-directory/).
- A következő rendszergazda fiókok egyike a konfigurálni kívánt könyvtárhoz:
   - Globális rendszergazda
   - Biztonsági rendszergazda
   - Feltételes hozzáférés-rendszergazda

## <a name="terms-of-use-document"></a>A használati feltételek dokumentuma

Az Azure AD használati feltételei a PDF-formátumot használják a tartalom bemutatásához. A PDF-fájl bármilyen tartalmat jelenthet, akár meglévő szerződéseket is, így begyűjtheti a végfelhasználói megállapodásokat a felhasználók bejelentkezésekor. A mobileszközökön lévő felhasználók támogatása érdekében a PDF-dokumentumban ajánlott betűméret 24 pont.

## <a name="add-terms-of-use"></a>Használati feltételek hozzáadása

Miután véglegesítette a használati feltételeket, adja hozzá az alábbi eljárást.

1. Jelentkezzen be az Azure-ba globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférés-rendszergazdaként.
1. Lépjen a **Használati feltételekre** a következőn: [https://aka.ms/catou](https://aka.ms/catou).

   ![Feltételes hozzáférés – Használati feltételek panel](./media/terms-of-use/tou-blade.png)

1. Kattintson az **Új feltételek** lehetőségre.

   ![Új használati feltétel ablaktábla a használati feltételek beállításainak megadásához](./media/terms-of-use/new-tou.png)

1. A **Név** mezőbe írja be az Azure Portalon használt használati feltételek nevét.
1. A **Megjelenítendő név** mezőbe írja be azt a címet, amelyet a felhasználók a bejelentkezéskor látnak.
1. A **Használati feltételek dokumentumban**keresse meg a véglegesített használati feltételekET PDF-fájlban, és jelölje ki.
1. Válassza ki a használati feltételek dokumentumának nyelvét. A nyelvi beállítás lehetővé teszi, hogy több használati feltételt töltsön fel, különböző nyelveken. A használati feltételek végfelhasználó által látott verziója a böngésző beállításaitól függ.
1. Ha azt szeretné, hogy a végfelhasználók nak az elfogadásuk előtt meg kell tekinteniük a használati feltételeket, állítsa be a **Felhasználók számára a használati feltételek kibontásának megkövetelése** Beállítás **bekapcsolva**értékre.
1. Ha azt szeretné, hogy a végfelhasználók nak el kell fogadniuk az Ön használati feltételeit minden olyan eszközön, amelyről hozzáférnek, állítsa be a **Felhasználók beleegyezésének megkövetelése minden eszközön** a **Be**értékre. A felhasználóknak további alkalmazásokat kell telepíteniük, ha ez a beállítás engedélyezve van. További információ: [Per-device terms of use](#per-device-terms-of-use).
1. Ha a használati feltételeket ütemezés szerint szeretné lejárni, állítsa **a Expire consents (Be)** **értékre.** Ha be van kapcsolva, két további ütemezési beállítás jelenik meg.

   ![A kezdési dátum, gyakoriság és időtartam beállításához szükséges engedélybeállítások lejárata](./media/terms-of-use/expire-consents.png)

1. A **Lejáratás kezdete** és a **Gyakoriság** beállításokkal adja meg a használati feltételek lejárati ütemezését. Az alábbi táblázat néhány példabeállítás eredményét mutatja be:

   | Lejárat kezdete: | Frequency | Eredmény |
   | --- | --- | --- |
   | Mai dátum  | Havonta | A mai naptól kezdve a felhasználóknak el kell fogadniuk a használati feltételeket, majd minden hónapban újra el kell fogadniuk. |
   | Dátum a jövőben  | Havonta | A mai naptól a felhasználóknak el kell fogadniuk a használati feltételeket. A jövőbeli dátum bekövetkeztekor a jóváhagyások lejárnak, majd a felhasználóknak minden hónapban újra el kell fogadniuk.  |

   Ha például a lejárati dátumot január 1-re, a gyakoriságot pedig **havira** állítja, az alábbiak szerint két felhasználó esetében a lejáratok bekövetkezése történhet: **Monthly**

   | Felhasználó | Első elfogadás dátuma | Első lejárat dátuma | Második lejárat dátuma | Harmadik lejárat dátuma |
   | --- | --- | --- | --- | --- |
   | Alice | Jan. 1. | Február 1. | Már 1 | április 1. |
   | Bob | Jan. 15. | Február 1. | Már 1 | április 1. |

1. Az **Újraelfogadás előtti időtartam beállítás (nap)** beállítással adja meg, hogy a felhasználónak hány napot kell újra elfogadnia a használati feltételeknek. Ez lehetővé teszi a felhasználók számára, hogy kövessék a saját ütemezésüket. Ha például az időtartamot **30** napra állítja be, a következőképpen fordulhat elő két felhasználó lejárata:

   | Felhasználó | Első elfogadás dátuma | Első lejárat dátuma | Második lejárat dátuma | Harmadik lejárat dátuma |
   | --- | --- | --- | --- | --- |
   | Alice | Jan. 1. | Január 31. | Már 2. | április 1. |
   | Bob | Jan. 15. | Február 14. | Már 16 | április 15. |

   A **Lejárató jóváhagyások** és az Időtartam használata lehetséges, mielőtt az **újraelfogadáshoz (nap)** beállításokat együtt kell használni, de általában az egyiket vagy a másikat használja.

1. A **Feltételes hozzáférés**csoportban a **Kényszerítés feltételes hozzáféréssel házirendsablon-sablonhasználatával** jelölje ki a sablont a használati feltételek érvényesítéséhez.

   ![Feltételes hozzáférés legördülő lista a házirendsablon kiválasztásához](./media/terms-of-use/conditional-access-templates.png)

   | Sablon | Leírás |
   | --- | --- |
   | **Hozzáférés a felhőalapú alkalmazásokhoz minden vendég számára** | A feltételes hozzáférésre vonatkozó szabályzat minden vendég és az összes felhőalapú alkalmazás számára létrejön. Ez a szabályzat hatással van az Azure Portalon. Miután létrehozta, előfordulhat, hogy ki kell jelentkeznie, és ki kell jelentkeznie. |
   | **Hozzáférés a felhőalapú alkalmazásokhoz minden felhasználó számára** | A feltételes hozzáférési szabályzat jön létre az összes felhasználó és az összes felhőalapú alkalmazások. Ez a szabályzat hatással van az Azure Portalon. Miután létrehozta, ki kell jelentkeznie, és ki kell jelentkeznie. |
   | **Egyéni házirend** | Válassza ki azokat a felhasználókat, csoportokat és alkalmazásokat, amelyekre ez a használati feltételek vonatkoznak. |
   | **Feltételes hozzáférési házirend létrehozása később** | Ez a használati feltételek a feltételes hozzáférési házirend létrehozásakor megjelennek a támogatásvezérlő listában. |

   >[!IMPORTANT]
   >A feltételes hozzáférés házirend-szabályozásának szabályozása (beleértve a használati feltételeket is) nem támogatja a szolgáltatásfiókok on-kontusait. Azt javasoljuk, hogy zárja ki az összes szolgáltatásfiókok a feltételes hozzáférési szabályzat.

    Egyéni feltételes hozzáférési szabályzatok lehetővé teszik a részletes használati feltételek, le egy adott felhőalapú alkalmazás vagy felhasználói csoport. További információ: [Rövid útmutató: A használati feltételek elfogadásának megkövetelése a felhőalapú alkalmazások elérése előtt.](require-tou.md)

1. Kattintson **a Létrehozás gombra.**

   Ha egyéni feltételes hozzáféréssablont választott ki, akkor egy új képernyő jelenik meg, amely lehetővé teszi az egyéni feltételes hozzáférési házirend létrehozását.

   ![Új feltételes hozzáférés ablaktábla, ha az egyéni feltételes hozzáférés házirendsablont választotta](./media/terms-of-use/custom-policy.png)

   Most látnia kell az új használati feltételeket.

   ![Új használati feltételek a használati feltételekben, a panelen](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Jelentés megtekintése arról, hogy ki fogadta el és utasította el

A használati feltételek panel megjeleníti a feltételeket elfogadó, illetve elutasító felhasználók számát. Ezek a számok, és aki elfogadta / elutasította tárolják az élet a használati feltételeket.

1. Jelentkezzen be az Azure-ba, és lépjen a **Használati feltételekre** a [https://aka.ms/catou](https://aka.ms/catou) címen.

   ![Használati feltételek blade felsorolva a felhasználói show száma elfogadta és elutasította](./media/terms-of-use/view-tou.png)

1. Használati feltételek esetén kattintson az **Elfogadva** vagy **elutasítva** csoportban lévő számokra a felhasználók aktuális állapotának megtekintéséhez.

   ![Használati feltételek jóváhagyások ablaktábla, amely felsorolja az okat a felhasználókat, akik elfogadták](./media/terms-of-use/accepted-tou.png)

1. Az egyes felhasználók előzményeinek megtekintéséhez kattintson a három pontra (**...**), majd **az Előzmények megtekintése parancsra.**

   ![Felhasználó Előzmények helyi menüjének megtekintése](./media/terms-of-use/view-history-menu.png)

   A nézetelőzmények ablaktáblán az összes elfogadás, elutasítás és lejárat előzménye látható.

   ![Az Előzmények megtekintése ablaktábla felsorolja a felhasználó előzményeit, elutasításait és lejáratait](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Az Azure AD naplózási naplóinak megtekintése

Ha további tevékenységeket szeretne megtekinteni, az Azure AD használati feltételei naplónaplókat is tartalmaznak. Minden felhasználó hozzájárulása egy **30 napig**tárolt naplóban lévő eseményt indít el. Ezeket a naplókat megtekintheti a portálon, vagy letöltheti egy .csv fájlban.

Az Azure AD naplózási naplóinak első lépései az alábbi eljárással kezdődnek:

1. Jelentkezzen be az Azure-ba, és lépjen a **Használati feltételekre** a [https://aka.ms/catou](https://aka.ms/catou) címen.
1. Válasszon használati feltételeket.
1. Kattintson a **Naplók megtekintése** elemre.

   ![Használati feltételek panel a Naplónaplók megtekintése lehetőség kiemelve](./media/terms-of-use/audit-tou.png)

1. Az Azure AD naplózási naplók képernyőn szűrheti az információkat a megadott listák segítségével konkrét naplóadatok célzásához.

   A **Letöltés** elemre kattintva az információkat helyi felhasználás céljából egy .csv fájlba is mentheti.

   ![Az Azure AD naplózási naplói nak képernyőlista dátuma, célházirendje, által kezdeményezett és tevékenység](./media/terms-of-use/audit-logs-tou.png)

   Ha egy naplóra kattint, egy ablaktábla jelenik meg további tevékenységadatokkal.

   ![Tevékenység részletei a tevékenységet, a tevékenység állapotát, a célházirendet megjelenítő naplóhoz](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Hogyan néz ki a használati feltételek a felhasználók számára?

A használati feltételek létrehozása és kényszerítése után a hatókörben lévő felhasználók a bejelentkezés során a következő képernyőt fogják látni.

![Példa használati feltételekre, amelyek akkor jelennek meg, amikor a felhasználó bejelentkezik](./media/terms-of-use/user-tou.png)

A felhasználók megtekinthetik a használati feltételeket, és szükség esetén gombok segítségével nagyíthatják és kicsinyíthetik.

![Használati feltételek nézete nagyítási gombokkal](./media/terms-of-use/zoom-buttons.png)

A következő képernyőn látható, hogyan jelennek meg a használati feltételek a mobileszközökön.

![Példa használati feltételekre, amelyek akkor jelennek meg, amikor a felhasználó bejelentkezik egy mobileszközön](./media/terms-of-use/mobile-tou.png)

A felhasználóknak csak egyszer kell elfogadniuk a használati feltételeket, és a későbbi bejelentkezéseksorán nem fogják látni a használati feltételeket.

### <a name="how-users-can-review-their-terms-of-use"></a>Hogyan tekinthetik át a felhasználók a használati feltételeket?

A felhasználók az alábbi eljárással tekinthetik át és tekinthetik meg az általuk elfogadott használati feltételeket.

1. Jelentkezzen be [https://myapps.microsoft.com](https://myapps.microsoft.com)a ikonra.
1. A jobb felső sarokban kattintson a nevére, és válassza a **Profil lehetőséget.**

   ![MyApps-webhely megnyitott felhasználóablaktáblával](./media/terms-of-use/tou14.png)

1. A Profi lapon kattintson a **Használati feltételek áttekintése** elemre.

   ![Profillap egy felhasználó számára, amely a Felhasználási feltételek áttekintése hivatkozást jeleníti meg](./media/terms-of-use/tou13a.png)

1. Itt áttekintheti az elfogadott használati feltételeket.

## <a name="edit-terms-of-use-details"></a>Használati feltételek részleteinek szerkesztése

A használati feltételek bizonyos részleteit szerkesztheti, de meglévő dokumentumokat nem. Az alábbi eljárás a részletek szerkesztését ismerteti.

1. Jelentkezzen be az Azure-ba, és lépjen a **Használati feltételekre** a [https://aka.ms/catou](https://aka.ms/catou) címen.
1. Válassza ki a szerkesztni kívánt használati feltételeket.
1. Kattintson **a Kifejezések szerkesztése gombra.**
1. A Használati feltételek szerkesztése ablaktáblában módosítsa a nevet, a megjelenítendő nevet, vagy írja elő a felhasználókszámára az értékek kibontását.

   Ha vannak más beállítások, amelyeket módosítani szeretne, például a PDF-dokumentum, amely megköveteli a felhasználók beleegyezését minden eszközön, lejár a jóváhagyások, az időtartam az újraelfogadás előtt vagy a feltételes hozzáférési szabályzat, új használati feltételeket kell létrehoznia.

   ![Használati feltételek szerkesztése ablaktábla a név és a kibontási beállításokkal](./media/terms-of-use/edit-tou.png)

1. A módosítások mentéséhez kattintson a **Mentés** gombra.

   A módosítások mentése után a felhasználóknak nem kell újra elfogadniuk ezeket a módosításokat.

## <a name="add-a-terms-of-use-language"></a>Használati feltételek hozzáadása nyelv

Az alábbi eljárás a használati kifejezések nyelvének hozzáadását ismerteti.

1. Jelentkezzen be az Azure-ba, és lépjen a **Használati feltételekre** a [https://aka.ms/catou](https://aka.ms/catou) címen.
1. Válassza ki a szerkesztni kívánt használati feltételeket.
1. A részletek ablaktáblán kattintson a **Nyelvek** fülre.

   ![A kiválasztott használati feltételek és a Nyelvek lap megjelenítése a részletek ablaktáblán](./media/terms-of-use/languages-tou.png)

1. Kattintson **a Nyelv hozzáadása gombra.**
1. A Használati feltételek hozzáadása nyelv ablaktáblán töltse fel a honosított PDF-fájlt, és válassza ki a nyelvet.

   ![Használati kifejezések hozzáadása nyelvi ablaktábla honosított PDF-dokumentumok feltöltési beállításaival](./media/terms-of-use/language-add-tou.png)

1. A nyelv hozzáadásához kattintson a **Hozzáadás** gombra.

## <a name="per-device-terms-of-use"></a>Eszközönkénti használati feltételek

A **Felhasználók hozzájárulásának megkövetelése minden eszközbeállítással** lehetővé teszi, hogy a végfelhasználóktól megkövetelje, hogy fogadják el a használati feltételeket minden eszközön, amelyről hozzáférnek. A végfelhasználónak regisztrálnia kell az eszközét az Azure AD-ben. Az eszköz regisztrálása után az eszközazonosító valamezenis érvényesíti a használati feltételeket az egyes eszközökön.

Itt van egy lista a támogatott platformok és szoftverek.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Egyéb |
> | --- | --- | --- | --- | --- |
> | **Natív alkalmazás** | Igen | Igen | Igen |  |
> | **Microsoft Edge** | Igen | Igen | Igen |  |
> | **Internet Explorer** | Igen | Igen | Igen |  |
> | **Chrome (kiterjesztéssel)** | Igen | Igen | Igen |  |

Az eszközönkénti használati feltételek a következő korlátozásokkal rendelkeznek:

- Egy eszköz csak egy bérlőhöz csatlakoztatható.
- A felhasználónak engedéllyel kell rendelkeznie az eszközhöz való csatlakozáshoz.
- Az Intune Enrollment alkalmazás nem támogatott.
- Az Azure AD B2B-felhasználók nem támogatottak.

Ha a felhasználó eszköze nincs csatlakoztatva, üzenetet kap, hogy csatlakoznia kell az eszközhöz. Tapasztalataik a platformtól és a szoftvertől függenek.

### <a name="join-a-windows-10-device"></a>Windows 10-es eszköz csatlakoztatása

Ha egy felhasználó Windows 10-et és Microsoft Edge-et használ, az alábbihoz hasonló üzenetet kap, hogy [csatlakozzon az eszközéhez.](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device)

![Windows 10 és Microsoft Edge - Az eszközt jelző üzenet regisztrálva kell lennie](./media/terms-of-use/per-device-win10-edge.png)

Ha Chrome-ot használnak, a rendszer kérni fogja a [Windows 10-fiókok bővítmény](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)telepítését.

### <a name="register-an-ios-device"></a>IOS-eszköz regisztrálása

Ha egy felhasználó iOS-eszközt használ, a rendszer a [Microsoft Authenticator alkalmazás](https://apps.apple.com/us/app/microsoft-authenticator/id983156458)telepítését kéri.

### <a name="register-an-android-device"></a>Android-eszköz regisztrálása

Ha egy felhasználó Android-eszközt használ, a rendszer a [Microsoft Authenticator alkalmazás](https://play.google.com/store/apps/details?id=com.azure.authenticator)telepítését kéri.

### <a name="browsers"></a>Böngészők

Ha egy felhasználó nem támogatott böngészőt használ, a rendszer megkéri, hogy használjon egy másik böngészőt.

![Az eszközt jelző üzenetnek regisztrálnia kell, de a böngésző nem támogatott](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Használati feltételek törlése

A régi használati feltételeket az alábbi eljárással törölheti.

1. Jelentkezzen be az Azure-ba, és lépjen a **Használati feltételekre** a [https://aka.ms/catou](https://aka.ms/catou) címen.
1. Válassza ki az eltávolítani kívánt használati feltételeket.
1. Kattintson a **Feltételek törlése** lehetőségre.
1. A megjelenő üzenetben, amely a törlés megerősítését kéri, kattintson az **Igen** lehetőségre.

   ![A használati feltételek törléséhez megerősítést kérő üzenet](./media/terms-of-use/delete-tou.png)

   A továbbiakban nem láthatja a használati feltételeket.

## <a name="deleted-users-and-active-terms-of-use"></a>Törölt felhasználók és aktív használati feltételek

Alapértelmezés szerint a törölt felhasználók 30 napig még törölt állapotban megmaradnak az Azure AD-ban, amely idő alatt szükség esetén egy rendszergazda visszaállíthatja őket. A felhasználók 30 nap után véglegesen törlődnek. Emellett az Azure Active Directory portál használatával a globális rendszergazda explicit módon [véglegesen törölheti a nemrég törölt felhasználót,](../fundamentals/active-directory-users-restore.md) mielőtt ezt az időszakot elérné. Az egyik felhasználó véglegesen törölt, a további adatokat, hogy a felhasználó törlődik az aktív használati feltételeket. A törölt felhasználókkal kapcsolatos naplózási információk az auditnaplóban maradnak.

## <a name="policy-changes"></a>A szabályzatok változásai

A feltételes hozzáférési házirendek azonnal érvénybe lépnek. Ha ez megtörténik, a rendszergazda "szomorú felhők" vagy "Azure AD-jogkivonat-problémák" jelenik meg. Az új házirend teljesítése érdekében a rendszergazdának ki kell jelentkeznie, és újra be kell jelentkeznie.

> [!IMPORTANT]
> Az érintett felhasználóknak újra be kell jelentkezniük, hogy megfelelhessenek az új szabályzatnak, ha:
>
> - a feltételes hozzáférési házirend engedélyezett a használati feltételek
> - létrehoztak újabb használati feltételeket

## <a name="b2b-guests-preview"></a>B2B vendégek (Előzetes verzió)

A legtöbb szervezet rendelkezik egy folyamattal, amelylenem járul hozzá alkalmazottaik számára, hogy hozzájáruljanak a szervezet használati feltételeihez és adatvédelmi nyilatkozataihoz. De hogyan kényszerítheti ki ugyanazokat a beleegyezéseket az Azure AD-s vállalkozások közötti (B2B) vendégek számára, amikor sharepoint-on vagy teams-en keresztül kerülnek hozzáadásra? Feltételes hozzáférés és használati feltételek használatával közvetlenül a B2B vendégfelhasználók felé kényszerítheti a szabályzatot. A meghívásbeváltási folyamat során a felhasználó megjelenik a használati feltételekkel. Ez a támogatás jelenleg előzetes verzióban érhető el.

A használati feltételek csak akkor jelennek meg, ha a felhasználó rendelkezik egy vendégfiókkal az Azure AD-ben. A SharePoint Online jelenleg [ad hoc külső megosztási címzettek számára](/sharepoint/what-s-new-in-sharing-in-targeted-release) rendelkezik egy olyan dokumentum vagy mappa megosztásához, amelyhez nincs szükség a felhasználó vendégfiókjára. Ebben az esetben a használati feltételek nem jelennek meg.

![Felhasználók és csoportok ablaktábla – A lap felvétele a Minden vendégfelhasználó jelölőnégyzet bejelölve](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>Felhőalapú alkalmazások támogatása (előzetes verzió)

Használati feltételek használhatók a különböző felhőalapú alkalmazások, például az Azure Information Protection és a Microsoft Intune. Ez a támogatás jelenleg előzetes verzióban érhető el.

### <a name="azure-information-protection"></a>Azure Information Protection

Feltételes hozzáférési szabályzatot konfigurálhat az Azure Information Protection alkalmazáshoz, és használati feltételeket kérhet, amikor egy felhasználó egy védett dokumentumhoz fér hozzá. Ez a használati feltételeket vált ki, mielőtt a felhasználó először férne hozzá egy védett dokumentumhoz.

![Felhőalkalmazások ablaktáblája a kiválasztott Microsoft Azure Information Protection alkalmazással](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Microsoft Intune-regisztráció

Feltételes hozzáférési szabályzatot konfigurálhat a Microsoft Intune-regisztrációs alkalmazáshoz, és használati feltételeket kérhet az intune-ba való regisztráció előtt. További információt az Olvasás [a megfelelő feltételek kiválasztása című témakörben talál a szervezet blogbejegyzésében.](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409)

![Felhőalkalmazások ablaktáblája a kijelölt Microsoft Intune alkalmazással](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> Az Intune Enrollment alkalmazás nem támogatott [az eszközönkénti használati feltételek hez.](#per-device-terms-of-use)

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**K: Hogyan láthatom, hogy a felhasználó mikor/ha elfogadta a használati feltételeket?**<br />
A: A Használati feltételek panelen kattintson az **Elfogadva**csoportban lévő számra. Megtekintheti vagy kereshet az elfogadási tevékenységaz Azure AD naplózási naplókban. További információ: Jelentés megtekintése arról, hogy ki fogadta el és utasította el, valamint [Az Azure AD naplózási naplóinak megtekintése](#view-azure-ad-audit-logs)című témakörben talál.

**K: Mennyi ideig tárolódnak az információk?**<br />
A: A felhasználó számít a használati feltételek jelentés, és aki elfogadta / elutasította tárolja az élettartama a használati feltételeket. Az Azure AD naplózási naplók 30 napig tárolják.

**K: Miért jelenik meg a használati feltételek jelentés és az Azure AD naplózási naplók eltérő számú hozzájárulása?**<br />
A: A használati feltételek jelentés tárolja az adott használati feltételek élettartama alatt, míg az Azure AD naplózási naplók 30 napig tárolja. Emellett a használati feltételek jelentés csak a felhasználók aktuális hozzájárulási állapotát jeleníti meg. Ha például egy felhasználó elutasítja, majd elfogadja, a használati feltételekről szóló jelentés csak azt mutatja, hogy a felhasználó elfogadja. Ha meg kell tekinteni az előzményeket, használhatja az Azure AD naplózási naplók.

**K: Ha módosítom a használati feltételek részleteit, megköveteli-e a felhasználóktól, hogy újra elfogadják?**<br />
A: Nem, ha a rendszergazda szerkeszti a használati feltételek részleteit (név, megjelenítendő név, a felhasználók kibontása vagy nyelv hozzáadása), nem szükséges, hogy a felhasználók újra elfogadják az új feltételeket.

**K: Frissíthetek egy meglévő használati feltételt?**<br />
A: Jelenleg nem frissíthet meglévő használati feltételeket tartalmazó dokumentumot. A használati feltételek módosításához új használati feltételeket kell létrehoznia.

**K: Ha a hivatkozások a használati feltételek PDF-dokumentum, a végfelhasználók képesek lesznek rájuk kattintani?**<br />
V: Igen, a végfelhasználók további oldalakra mutató hivatkozásokat is kijelölhetnek, de a dokumentum on belüli szakaszokra mutató hivatkozások nem támogatottak.

**K: A használati feltételek több nyelvet is támogathatnak?**<br />
V: Igen. Jelenleg 108 különböző nyelvet konfigurálhat a rendszergazda egyetlen használati feltételhez. A rendszergazda több PDF-dokumentumot is feltölthet, és megjelölheti ezeket a dokumentumokat a megfelelő nyelvvel (legfeljebb 108). Amikor a végfelhasználók bejelentkeznek, megnézzük a böngésző nyelvi beállításait, és megjelenítjük a megfelelő dokumentumot. Ha nincs egyezés, akkor az alapértelmezett dokumentumot jelenítjük meg, amely az első feltöltött dokumentum.

**K: Mikor aktiválódnak a használati feltételek?**<br />
V: A használati feltételek a bejelentkezéskor lépnek életbe.

**K: Mely alkalmazásokhoz használhatom a használati feltételeket?**<br />
A: A vállalati alkalmazásokhoz létrehozhat egy feltételes hozzáférési házirendet a modern hitelesítés használatával. Bővebb információ: [vállalati alkalmazások](./../manage-apps/view-applications-portal.md).

**K: Több használati feltételt is hozzáadhatok egy adott felhasználóhoz vagy alkalmazáshoz?**<br />
A: Igen, ha több feltételes hozzáférési házirendet hoz létre, amelyek ezeket a csoportokat vagy alkalmazásokat célozzák meg. Ha egy felhasználó több használati feltétel hatálya alá tartozik, egyszerre csak egy használati feltételt fogad el.

**K: Mi történik akkor, ha egy felhasználó nem fogadja el a használati feltételeket?**<br />
V: Ebben az esetben a felhasználón nem kaphat hozzáférést az alkalmazáshoz. A felhasználónak újra be kell jelentkeznie, és el kell fogadnia a feltételeket, hogy hozzáférjen.

**K: Lehet-e nem elfogadni a korábban elfogadott használati feltételeket?**<br />
A: [Áttekintheti a korábban elfogadott használati feltételeket,](#how-users-can-review-their-terms-of-use)de jelenleg nincs módja az elfogadás nak.

**K: Mi történik, ha az Intune általános szerződési feltételeit is használom?**<br />
A: Ha konfigurálta az Azure AD használati feltételeit és az [Intune-feltételeket is,](/intune/terms-and-conditions-create)a felhasználónak mindkettőt el kell fogadnia. További információt a [Megfelelő feltételek kiválasztása a szervezetblogbejegyzésben című témakörben talál.](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409)

**K: Milyen végpontokat használ a használati feltételek szolgáltatás hitelesítéshez?**<br />
A: A használati feltételek a következő végpontokat használják a hitelesítéshez: https://tokenprovider.termsofuse.identitygovernance.azure.com és https://account.activedirectory.windowsazure.com. Ha a szervezet rendelkezik egy engedélyezési url-címek a regisztrációhoz, hozzá kell adnia ezeket a végpontokat az engedélyezési listához, valamint az Azure AD-végpontok a bejelentkezéshez.

## <a name="next-steps"></a>További lépések

- [Rövid útmutató: A felhőalapú alkalmazások elérése előtt el kell fogadni a használati feltételeket](require-tou.md)
- [Gyakorlati tanácsok a feltételes hozzáféréshez az Azure Active Directoryban](best-practices.md)
