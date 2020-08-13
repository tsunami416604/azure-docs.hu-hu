---
title: Használati feltételek – Azure Active Directory | Microsoft Docs
description: Ismerkedjen meg Azure Active Directory használati feltételeivel, hogy a hozzáférés megkezdése előtt adatokat nyújtson az alkalmazottaknak vagy a vendégeknek.
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: how-to
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81f3f771987ce2ff11aab88a4d35df01b6c85941
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88184346"
---
# <a name="azure-active-directory-terms-of-use"></a>Használati feltételek Azure Active Directory

Az Azure AD használati feltételei egyszerű módszert biztosítanak a szervezetek számára, hogy információkat nyújtsanak a végfelhasználóknak. Ez a bemutató gondoskodik arról, hogy a felhasználók megkapják a jogi vagy megfelelőségi követelményekre vonatkozó nyilatkozatokat. Ez a cikk a használati feltételek használatának első lépéseit ismerteti.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Áttekintő videók

A következő videó gyors áttekintést nyújt a használati feltételekről.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

További videók:
- [Használati feltételek üzembe helyezése Azure Active Directory](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Használati feltételek bevezetése Azure Active Directory](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>Mire használhatom a használati feltételeket?

Az Azure AD használati feltételei a következő képességekkel rendelkeznek:

- Az alkalmazottak vagy a vendégek elfogadják a használati feltételeket a hozzáférés megkezdése előtt.
- Az alkalmazottak vagy a vendégek számára a használati feltételek elfogadásának megkövetelése minden eszközön a hozzáférés megkezdése előtt.
- A használati feltételek elfogadásának megkövetelése az alkalmazottak vagy a vendégek számára ismétlődő ütemterv szerint.
- Az Azure Multi-Factor Authentication (MFA) biztonsági adatainak regisztrálása előtt az alkalmazottak vagy a vendégek elfogadják a használati feltételeket.
- Az Azure AD önkiszolgáló jelszó-visszaállítás (SSPR) biztonsági adatainak regisztrálása előtt a használati feltételek elfogadásának megkövetelése az alkalmazottaktól.
- A szervezet összes felhasználójának általános használati feltételeit mutatjuk be.
- A használati feltételek egy felhasználói attribútum alapján jelennek meg (pl.: orvosok kontra nővérek, belföldi kontra nemzetközi alkalmazottak [dinamikus csoportjai](../users-groups-roles/groups-dynamic-membership.md)).
- A magas üzleti hatású alkalmazások, például a Salesforce eléréséhez adott használati feltételek jelennek meg.
- A használati feltételek különböző nyelveken jelennek meg.
- A használati feltételekhez tartozó vagy még nem elfogadott lista.
- Segítségnyújtás az adatvédelmi szabályozásokban.
- A megfelelőség és a naplózás használati feltételeinek naplóját jeleníti meg.
- Használati feltételek létrehozása és kezelése [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement) -kkal (jelenleg előzetes verzióban).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD használati feltételeinek használatához és konfigurálásához a következőket kell tennie:

- Prémium szintű Azure AD P1, P2, EMS E3 vagy EMS E5 előfizetés.
   - Ha még nem rendelkezik ezen előfizetések egyikével sem, [beszerezhet Prémium szintű Azure AD előfizetést](../fundamentals/active-directory-get-started-premium.md), vagy [engedélyezheti a Prémium szintű Azure AD előfizetés próbaverzióját](https://azure.microsoft.com/trial/get-started-active-directory/).
- A következő rendszergazda fiókok egyike a konfigurálni kívánt könyvtárhoz:
   - Globális rendszergazda
   - Biztonsági rendszergazda
   - Feltételes hozzáférésű rendszergazda

## <a name="terms-of-use-document"></a>A használati feltételek dokumentuma

Az Azure AD használati feltételei a PDF formátumot használják a tartalom megjelenítéséhez. A PDF-fájl bármilyen tartalmat jelenthet, akár meglévő szerződéseket is, így begyűjtheti a végfelhasználói megállapodásokat a felhasználók bejelentkezésekor. A mobileszközök felhasználóinak támogatásához a PDF-fájl javasolt betűmérete 24 pont.

## <a name="add-terms-of-use"></a>Használati feltételek hozzáadása

Ha véglegesítette a használati feltételeket tartalmazó dokumentumot, a következő eljárással adhatja hozzá.

1. Jelentkezzen be az Azure-ba globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférésű rendszergazdaként.
1. Lépjen a **Használati feltételekre** a következőn: [https://aka.ms/catou](https://aka.ms/catou).

   ![Feltételes hozzáférés – Használati feltételek panel](./media/terms-of-use/tou-blade.png)

1. Kattintson az **Új feltételek** lehetőségre.

   ![A használati feltételek beállításainak megadására szolgáló új használati időszak](./media/terms-of-use/new-tou.png)

1. A **név** mezőben adja meg a használati feltételek nevét, amelyet a rendszer a Azure Portal fog használni.
1. A **megjelenítendő név** mezőbe írja be azt a címet, amelyet a felhasználók a bejelentkezéskor látnak.
1. **Használati feltételek dokumentum**esetén keresse meg a véglegesített használati feltételeket tartalmazó PDF-fájlt, és válassza ki.
1. Válassza ki a használati feltételek dokumentumának nyelvét. A nyelvi beállítás lehetővé teszi, hogy több használati feltételt töltsön fel, különböző nyelveken. A használati feltételek végfelhasználó által látott verziója a böngésző beállításaitól függ.
1. Ha szeretné megkövetelni, hogy a végfelhasználók megtekintsék a használati feltételeket az elfogadásuk előtt, állítsa be **a felhasználók számára a használati feltételek kibontását** **a következőre:.**
1. Ha meg szeretné követelni, hogy a végfelhasználók elfogadják a használati feltételeit minden olyan eszközön, amelyről hozzáférnek, állítsa be a **felhasználókat, hogy minden eszközön** **a**be legyenek kapcsolva. Ha ez a beállítás engedélyezve van, a felhasználóknak szükségük lehet további alkalmazások telepítésére. További információ: [eszközönkénti használati feltételek](#per-device-terms-of-use).
1. Ha a használati feltételek érvényességét egy adott időpontban kívánja lejárni, állítsa **le** **a**következőt:. Ha be értékre van állítva, két további ütemezett beállítás jelenik meg.

   ![Visszalépési beleegyezési beállítások a kezdő dátum, a gyakoriság és az időtartam beállításához](./media/terms-of-use/expire-consents.png)

1. A **lejárati kezdési** és a **gyakorisági** beállításokkal megadhatja a használati feltételek lejáratának ütemtervét. A következő táblázat az eredményeket mutatja be néhány példa:

   | Lejárat kezdete | Frequency | Eredmény |
   | --- | --- | --- |
   | Mai dátum  | havonta | A mai naptól kezdve a felhasználóknak el kell fogadniuk a használati feltételeket, majd minden hónapban újra el kell fogadniuk azokat. |
   | Jövőbeli dátum  | havonta | A mai naptól kezdve a felhasználóknak el kell fogadniuk a használati feltételeket. A jövőbeli dátum esetén a beleegyezések lejárnak, majd minden hónapban újra el kell fogadniuk a felhasználókat.  |

   Ha például úgy állítja be a lejárat napját, hogy az **1** . és a **havi**gyakorisága is megtörténjen, Itt láthatja, hogyan fordulhat elő lejárat két felhasználó esetében:

   | Felhasználó | Első elfogadás dátuma | Első lejárat dátuma | Második lejárati dátum | Harmadik lejárat dátuma |
   | --- | --- | --- | --- | --- |
   | Alice | Jan. 1. | Feb. 1. | Mar 1 | 1. április |
   | Bob | Jan. 15. | Feb. 1. | Mar 1 | 1. április |

1. Használja az **időtartamot az újraelfogadás előtt (nap)** beállítással határozza meg, hogy hány nap elteltével kell a felhasználónak újra elfogadnia a használati feltételeket. Ez lehetővé teszi a felhasználók számára, hogy kövessék a saját ütemtervet. Ha például az időtartamot **30** napra állítja be, akkor itt láthatja, hogyan fordulhat elő a lejárat két felhasználó esetén:

   | Felhasználó | Első elfogadás dátuma | Első lejárat dátuma | Második lejárati dátum | Harmadik lejárat dátuma |
   | --- | --- | --- | --- | --- |
   | Alice | Jan. 1. | Jan 31 | Március 2. | 1. április |
   | Bob | Jan. 15. | Február 14 | Március 16. | Április 15. |

   Az **újbóli elfogadás előtt** a **lejárati** jóváhagyások és az időtartam is használható, de általában az egyiket vagy a másikat kell használnia.

1. A **feltételes hozzáférés**területen a **kikényszerítés feltételes hozzáférési házirend sablonnal** listával válassza ki a sablont a használati feltételek érvénybe léptetéséhez.

   ![Egy házirend-sablon kiválasztására szolgáló feltételes hozzáférés legördülő lista](./media/terms-of-use/conditional-access-templates.png)

   | Sablon | Description |
   | --- | --- |
   | **Hozzáférés a felhőalapú alkalmazásokhoz az összes vendég számára** | A rendszer létrehoz egy feltételes hozzáférési szabályzatot az összes vendég és minden felhőalapú alkalmazás számára. Ez a házirend hatással van a Azure Portalra. A létrehozást követően szükség lehet a kijelentkezésre és a bejelentkezésre. |
   | **Hozzáférés a felhőalapú alkalmazásokhoz az összes felhasználó számára** | A rendszer létrehoz egy feltételes hozzáférési szabályzatot az összes felhasználó és minden felhőalapú alkalmazás számára. Ez a házirend hatással van a Azure Portalra. A létrehozást követően ki kell jelentkeznie és be kell jelentkeznie. |
   | **Egyéni szabályzat** | Válassza ki azokat a felhasználókat, csoportokat és alkalmazásokat, amelyekre a használati feltételek vonatkoznak lesznek. |
   | **Feltételes hozzáférési szabályzat létrehozása később** | A használati feltételek az engedélyezési vezérlő listában jelennek meg a feltételes hozzáférési szabályzat létrehozásakor. |

   >[!IMPORTANT]
   >A feltételes hozzáférési szabályzatok (beleértve a használati feltételeket is) nem támogatják a szolgáltatásfiókok kényszerítését. Javasoljuk, hogy az összes szolgáltatásfiókot kizárja a feltételes hozzáférési szabályzatból.

    Az egyéni feltételes hozzáférési házirendek lehetővé teszik a részletes használati feltételek használatát egy adott felhőalapú alkalmazás vagy felhasználói csoport számára. További információ: gyors üzembe helyezés – a használati [feltételek elfogadásának megkövetelése a felhőalapú alkalmazások elérése előtt](require-tou.md).

1. Kattintson a **Create** (Létrehozás) gombra.

   Ha egyéni feltételes hozzáférési sablont jelölt ki, megjelenik egy új képernyő, amely lehetővé teszi az egyéni feltételes hozzáférési szabályzat létrehozását.

   ![Új feltételes hozzáférés panel, ha az egyéni feltételes hozzáférési házirend sablont választotta](./media/terms-of-use/custom-policy.png)

   Ekkor megjelenik az új használati feltételek.

   ![A használati feltételek panelen felsorolt új használati feltételek](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Az elfogadott és elutasított jelentések megtekintése

A használati feltételek panel megjeleníti a feltételeket elfogadó, illetve elutasító felhasználók számát. Ezek a számok és az elfogadásra váró/visszautasított adatmennyiség a használati feltételek életében vannak tárolva.

1. Jelentkezzen be az Azure-ba, és lépjen a **Használati feltételekre** a [https://aka.ms/catou](https://aka.ms/catou) címen.

   ![A felhasználó által fogadott és elutasított felhasználók számának listázása Használati feltételek](./media/terms-of-use/view-tou.png)

1. Használati feltételek esetén kattintson az **elfogadva** vagy **elutasítva** értékre a felhasználók aktuális állapotának megtekintéséhez.

   ![Használati feltételek beleegyezési ablaktábla az elfogadott felhasználók listázása](./media/terms-of-use/accepted-tou.png)

1. Egy adott felhasználó előzményeinek megtekintéséhez kattintson a három pontra (**..**.), majd **tekintse meg a korábbi előzményeket**.

   ![Felhasználó előzmények helyi menüjének megtekintése](./media/terms-of-use/view-history-menu.png)

   Az előzmények megtekintése panelen megtekintheti az összes elfogad, elutasítás és lejárat előzményeit.

   ![A History (előzmények) ablaktábla a felhasználók előzményeit sorolja fel, elutasítja és lejáratokat jelenít meg](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Azure AD-naplók megtekintése

Ha további tevékenységet szeretne megtekinteni, az Azure AD használati feltételei naplózási naplókat is tartalmaznak. Minden felhasználó beleegyezik, hogy a naplókban **30 napig**tárolt eseményt indít el. Ezeket a naplókat megtekintheti a portálon, vagy letöltheti egy .csv fájlban.

Az Azure AD-naplók használatának megkezdéséhez kövesse az alábbi eljárást:

1. Jelentkezzen be az Azure-ba, és lépjen a **Használati feltételekre** a [https://aka.ms/catou](https://aka.ms/catou) címen.
1. Válassza ki a használati feltételeket.
1. Kattintson a **Naplók megtekintése** elemre.

   ![Használati feltételek panel a naplózási naplók megtekintése beállítás kijelölve](./media/terms-of-use/audit-tou.png)

1. Az Azure AD-naplók képernyőjén szűrheti az adatokat a megadott listával, hogy konkrét naplózási információkat lehessen megcélozni.

   A **Letöltés** elemre kattintva az információkat helyi felhasználás céljából egy .csv fájlba is mentheti.

   ![Az Azure AD-naplók képernyőjének listázási dátuma, a célként megadott házirend, a kezdeményező és a tevékenység](./media/terms-of-use/audit-logs-tou.png)

   Ha rákattint egy naplóra, egy ablaktábla további tevékenység részleteivel jelenik meg.

   ![A tevékenységgel, a tevékenység állapotával, a kezdeményezővel, a célként megadott házirenddel kapcsolatos tevékenység részletei](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>A használati feltételek a felhasználók számára

Miután létrehozta és érvényesíti a használati feltételeket, a felhasználók, akik hatókörben vannak, a bejelentkezéskor a következő képernyő jelenik meg.

![Használati feltételek, amelyek akkor jelennek meg, amikor a felhasználó bejelentkezik](./media/terms-of-use/user-tou.png)

A felhasználók megtekinthetik a használati feltételeket, és szükség esetén a nagyításhoz és kicsinyítéshez is használhatják a gombokat.

![Használati feltételek megtekintése a nagyítási gombokkal](./media/terms-of-use/zoom-buttons.png)

Az alábbi képernyőn látható, hogyan néz ki a használati feltételek a mobileszközök esetében.

![Használati feltételek, amelyek akkor jelennek meg, amikor egy felhasználó bejelentkezik egy mobileszközön](./media/terms-of-use/mobile-tou.png)

A felhasználóknak csak egyszer kell elfogadniuk a használati feltételeket, és a későbbi bejelentkezések során nem látják újra a használati feltételeket.

### <a name="how-users-can-review-their-terms-of-use"></a>Hogyan tekinthetik meg a felhasználók a használati feltételeket

A felhasználók a következő eljárással tekinthetik meg és tekinthetik meg az általuk elfogadott használati feltételeket.

1. Jelentkezzen be itt: [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. A jobb felső sarokban kattintson a nevére, és válassza a **profil**lehetőséget.

   ![MyApps-hely megnyitása a felhasználó ablaktábláján](./media/terms-of-use/tou14.png)

1. A Profi lapon kattintson a **Használati feltételek áttekintése** elemre.

   ![A használati feltételek áttekintése hivatkozását megjelenítő felhasználó profil lapja](./media/terms-of-use/tou13a.png)

1. Itt áttekintheti az elfogadott használati feltételeket.

## <a name="edit-terms-of-use-details"></a>Használati feltételek részleteinek szerkesztése

Szerkesztheti a használati feltételek részleteit, de nem módosíthatja a meglévő dokumentumokat. Az alábbi eljárás a részletek szerkesztését ismerteti.

1. Jelentkezzen be az Azure-ba, és lépjen a **Használati feltételekre** a [https://aka.ms/catou](https://aka.ms/catou) címen.
1. Válassza ki a szerkeszteni kívánt használati feltételeket.
1. Kattintson a **feltételek szerkesztése**elemre.
1. A használati feltételek szerkesztése panelen módosítsa a nevet, a megjelenítendő nevet, vagy a felhasználókat az értékek kibontásához.

   Ha más beállításokat szeretne módosítani, például a PDF-dokumentumot, a felhasználóknak minden eszközhöz meg kell egyezniük, le kell kötnie az elfogadást, az időtartamot az újbóli elfogadás előtt vagy a feltételes hozzáférési szabályzatot, új használati feltételeket kell létrehoznia.

   ![A használati feltételek ablaktábla neve és kibontása lehetőségek megjelenítése](./media/terms-of-use/edit-tou.png)

1. Kattintson a **Mentés** gombra a módosítások mentéséhez.

   A módosítások mentése után a felhasználóknak nem kell újra elfogadniuk ezeket a módosításokat.

## <a name="add-a-terms-of-use-language"></a>Használati feltételek nyelvének megadása

Az alábbi eljárás a használati feltételek nyelvének hozzáadását ismerteti.

1. Jelentkezzen be az Azure-ba, és lépjen a **Használati feltételekre** a [https://aka.ms/catou](https://aka.ms/catou) címen.
1. Válassza ki a szerkeszteni kívánt használati feltételeket.
1. A részleteket tartalmazó ablaktáblán kattintson a **nyelvek** fülre.

   ![Használati feltételek kiválasztva és a részletek ablaktábla nyelvek lapjának megjelenítése](./media/terms-of-use/languages-tou.png)

1. Kattintson a **nyelv hozzáadása**lehetőségre.
1. A használati feltételek hozzáadása panelen töltse fel a honosított PDF-fájlt, és válassza ki a nyelvet.

   ![Használati feltételek nyelvi paneljének hozzáadása a honosított PDF-fájlok feltöltésének lehetőségeivel](./media/terms-of-use/language-add-tou.png)

1. A nyelv hozzáadásához kattintson a **Hozzáadás** gombra.

## <a name="per-device-terms-of-use"></a>Eszközönkénti használati feltételek

A **felhasználók megkövetelése minden eszköz** beállításának megkövetelése lehetővé teszi, hogy a végfelhasználók elfogadják a használati feltételeket minden olyan eszközön, amelyhez hozzáférnek. A végfelhasználónak regisztrálnia kell az eszközét az Azure AD-ben. Az eszköz regisztrálása után a rendszer az eszköz AZONOSÍTÓját használja az egyes eszközökön a használati feltételek érvénybe léptetéséhez.

A támogatott platformok és szoftverek listáját itt találja.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Egyéb |
> | --- | --- | --- | --- | --- |
> | **Natív alkalmazás** | Igen | Igen | Igen |  |
> | **Microsoft Edge** | Igen | Igen | Igen |  |
> | **Internet Explorer** | Igen | Igen | Igen |  |
> | **Chrome (kiterjesztéssel)** | Igen | Igen | Igen |  |

Az eszközönkénti használati feltételek a következő korlátozásokkal rendelkeznek:

- Egy eszköz csak egy bérlőhöz csatlakoztatható.
- A felhasználónak engedélyekkel kell rendelkeznie az eszközhöz való csatlakozáshoz.
- Az Intune-beléptetési alkalmazás nem támogatott.
- Az Azure AD B2B-felhasználók nem támogatottak.

Ha a felhasználó eszköze nincs csatlakoztatva, egy üzenet jelenik meg, amelyhez csatlakoztatni kell az eszközét. A rendszer a platformtól és a szoftvertől függ.

### <a name="join-a-windows-10-device"></a>Windows 10-es eszköz csatlakoztatása

Ha a felhasználó Windows 10 és Microsoft Edge rendszert használ, a következőhöz hasonló üzenet jelenik meg az [eszköz csatlakoztatásához](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device).

![Windows 10 és Microsoft Edge – üzenet, amely jelzi, hogy az eszközt regisztrálni kell](./media/terms-of-use/per-device-win10-edge.png)

Ha a Chrome-ot használják, a rendszer kérni fogja a [Windows 10-es fiókok bővítmény](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)telepítését.

### <a name="register-an-ios-device"></a>IOS-eszköz regisztrálása

Ha a felhasználó iOS-eszközt használ, a rendszer kérni fogja a [Microsoft Authenticator alkalmazás](https://apps.apple.com/us/app/microsoft-authenticator/id983156458)telepítését.

### <a name="register-an-android-device"></a>Android-eszköz regisztrálása

Ha egy felhasználó Android-eszközt használ, a rendszer kérni fogja a [Microsoft Authenticator alkalmazás](https://play.google.com/store/apps/details?id=com.azure.authenticator)telepítését.

### <a name="browsers"></a>Böngészők

Ha a felhasználó nem támogatott böngészőt használ, a rendszer egy másik böngészőt fog kérni.

![Üzenet, amely jelzi, hogy az eszközt regisztrálni kell, de a böngésző nem támogatott](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Használati feltételek törlése

A régi használati feltételeket az alábbi eljárással törölheti.

1. Jelentkezzen be az Azure-ba, és lépjen a **Használati feltételekre** a [https://aka.ms/catou](https://aka.ms/catou) címen.
1. Válassza ki az eltávolítani kívánt használati feltételeket.
1. Kattintson a **Feltételek törlése** lehetőségre.
1. A megjelenő üzenetben, amely a törlés megerősítését kéri, kattintson az **Igen** lehetőségre.

   ![A használati feltételek törlését kérő üzenet](./media/terms-of-use/delete-tou.png)

   A használati feltételek már nem jelennek meg.

## <a name="deleted-users-and-active-terms-of-use"></a>Törölt felhasználók és az aktív használati feltételek

Alapértelmezés szerint a törölt felhasználók 30 napig még törölt állapotban megmaradnak az Azure AD-ban, amely idő alatt szükség esetén egy rendszergazda visszaállíthatja őket. A felhasználók 30 nap után véglegesen törlődnek. Emellett a Azure Active Directory portál használatával a globális rendszergazda explicit módon [véglegesen törölheti a nemrég törölt felhasználókat](../fundamentals/active-directory-users-restore.md) az adott időszak elérésekor. Az egyik felhasználó véglegesen törölve lett, az adott felhasználóra vonatkozó további adatok el lesznek távolítva az aktív Használati feltételekből. A törölt felhasználókkal kapcsolatos naplózási információk az auditnaplóban maradnak.

## <a name="policy-changes"></a>A szabályzatok változásai

A feltételes hozzáférési szabályzatok azonnal érvénybe lépnek. Ha ez történik, a rendszergazda elkezdi a "szomorú felhők" vagy az "Azure AD-tokenekkel kapcsolatos problémák" megtekintését. A rendszergazdának ki kell jelentkeznie, majd újra be kell jelentkeznie, hogy kielégítse az új szabályzatot.

> [!IMPORTANT]
> Az érintett felhasználóknak újra be kell jelentkezniük, hogy megfelelhessenek az új szabályzatnak, ha:
>
> - a feltételes hozzáférési szabályzat engedélyezett a használati feltételeken
> - létrehoztak újabb használati feltételeket

## <a name="b2b-guests-preview"></a>B2B-vendégek (előzetes verzió)

A legtöbb szervezetnek van egy folyamata, amellyel alkalmazottaik elfogadják a szervezet használati feltételeit és adatvédelmi nyilatkozatait. De hogyan érvényesítheti ugyanezeket az Azure AD vállalatközi (B2B) vendégek számára, amikor a SharePointon vagy a Teams szolgáltatáson keresztül veszik fel? A feltételes hozzáférés és a használati feltételek használata esetén a szabályzatok közvetlenül a B2B vendég felhasználói felé is kikényszeríthető. A meghívó beváltási folyamata során a felhasználó a használati feltételekkel jelenik meg. Ez a támogatás jelenleg előzetes verzióban érhető el.

Használati feltételek csak akkor jelenik meg, ha a felhasználóhoz tartozik egy vendégfiók az Azure AD-ben. A SharePoint Online-ban jelenleg van egy [ad hoc külső megosztási](/sharepoint/what-s-new-in-sharing-in-targeted-release) szolgáltatás, amellyel megosztható egy olyan dokumentum vagy mappa, amelyhez nincs szükség a felhasználónak a vendég fiókra. Ebben az esetben a használati feltételek nem jelennek meg.

![Felhasználók és csoportok ablaktábla – belefoglalási lap – minden vendég felhasználó lehetőség bejelölve](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>Felhőalapú alkalmazások támogatása (előzetes verzió)

A Használati feltételek különböző felhőalapú alkalmazásokhoz, például a Azure Information Protectionhoz és a Microsoft Intunehoz is használható. Ez a támogatás jelenleg előzetes verzióban érhető el.

### <a name="azure-information-protection"></a>Azure Information Protection

Beállíthat egy feltételes hozzáférési szabályzatot a Azure Information Protection alkalmazáshoz, és megkövetelheti a használati feltételeket, amikor egy felhasználó hozzáfér egy védett dokumentumhoz. Ez a használati feltételeket a védett dokumentumot első alkalommal elérő felhasználó előtt indítja el.

![Cloud apps panel Microsoft Azure Information Protection alkalmazás kiválasztva](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Microsoft Intune regisztráció

Beállíthat egy feltételes hozzáférési szabályzatot a Microsoft Intune beléptetési alkalmazáshoz, és az eszköz Intune-beli regisztrálását megelőzően egy használati feltételeket kell megadnia. További információ: a [megfelelő feltételek megoldásának kiválasztása a szervezet blogbejegyzésében](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

![Cloud apps panel Microsoft Intune alkalmazás kiválasztva](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> Az Intune-beléptetési alkalmazás nem támogatott [eszközönkénti használati feltételek](#per-device-terms-of-use)esetén.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**K: Hogyan, hogy mikor/ha a felhasználó elfogadta a használati feltételeket?**<br />
A: a Használati feltételek panelen kattintson az **elfogadva**értékre. Az Accept (elfogadás) tevékenységet az Azure AD-naplókon is megtekintheti vagy keresheti. További információ: a jelentés megtekintése, aki elfogadta és elutasította az [Azure ad-naplókat, és megtekintheti](#view-azure-ad-audit-logs)azokat.

**K: Mennyi ideig tárolódnak az információk?**<br />
A: a felhasználó a használati feltételekben foglalt, és a használati feltételek életében elfogadásra váró/visszautasított adatmennyiséget tárolja. Az Azure AD-naplókat 30 napig tároljuk.

**K: Miért látok eltérő számú beleegyezést a használati feltételek jelentésében és az Azure AD-naplókban?**<br />
A: a használati feltételekről szóló jelentés a használati feltételek élettartama alatt tárolódik, az Azure AD-naplókat pedig 30 napig tárolja a rendszer. Emellett a használati feltételek jelentés csak a felhasználók aktuálisan beleegyező állapotát jeleníti meg. Ha például egy felhasználó elutasítja, majd elfogadja a-t, a használati feltételek jelentés csak azt jeleníti meg, hogy a felhasználó elfogadja. Ha meg kell tekintenie az előzményeket, használhatja az Azure AD-naplókat.

**K: Ha szerkesztem a használati feltételek részleteit, azt kéri, hogy a felhasználók újra fogadjanak el?**<br />
A: nem, ha a rendszergazda a használati feltételek részleteit szerkeszti (név, megjelenítendő név, a felhasználók kibontásának megkövetelése vagy nyelv hozzáadása), nincs szükség arra, hogy a felhasználók újra elfogadják az új feltételeket.

**K: frissíthetek egy meglévő használati feltételeket tartalmazó dokumentumot?**<br />
A: jelenleg nem lehet frissíteni egy meglévő használati feltételeket tartalmazó dokumentumot. A használati feltételek módosításához létre kell hoznia egy új használati feltételt.

**K: Ha a hiperhivatkozások a PDF-dokumentum használati feltételei között szerepelnek, akkor a végfelhasználók rákattintanak rájuk?**<br />
Válasz: igen, a végfelhasználók kiválaszthatják a további lapokra mutató hivatkozásokat, de a dokumentumon belüli hivatkozások nem támogatottak. Emellett a PDF-fájlok használati feltételein belüli hiperhivatkozások nem működnek az Azure AD MyApps/MyAccount portálról való hozzáféréskor.

**K: a használati feltételek több nyelvet is támogatnak?**<br />
V: Igen. Jelenleg 108 különböző nyelven lehet beállítani a rendszergazda egyetlen használati feltételt. A rendszergazdák több PDF-dokumentumot is feltölthetnek, és megcímkézik a dokumentumokat a megfelelő nyelvvel (legfeljebb 108). Amikor a végfelhasználó bejelentkezik, megtekintjük a böngésző nyelvi beállításait, és megjelenítik a megfelelő dokumentumot. Ha nincs egyezés, a rendszer megjeleníti az alapértelmezett dokumentumot, amely az első feltöltött dokumentum.

**K: Mikor aktiválódik a használati feltételek?**<br />
V: A használati feltételek a bejelentkezéskor lépnek életbe.

**K: Mely alkalmazásokhoz használhatom a használati feltételeket?**<br />
A: modern hitelesítést használó feltételes hozzáférési szabályzatot hozhat létre a vállalati alkalmazásokhoz. Bővebb információ: [vállalati alkalmazások](./../manage-apps/view-applications-portal.md).

**K: Több használati feltételt is hozzáadhatok egy adott felhasználóhoz vagy alkalmazáshoz?**<br />
Válasz: igen, ha több feltételes hozzáférési szabályzatot hoz létre, amelyek az adott csoportokat vagy alkalmazásokat célozzák meg. Ha egy felhasználó több használati feltétel hatálya alá esik, egyszerre csak egy használati feltételt fogad el.

**K: Mi történik akkor, ha egy felhasználó nem fogadja el a használati feltételeket?**<br />
V: Ebben az esetben a felhasználón nem kaphat hozzáférést az alkalmazáshoz. A felhasználónak újra be kell jelentkeznie, és el kell fogadnia a feltételeket ahhoz, hogy hozzáférést kapjon.

**K: el lehet fogadniuk a korábban elfogadott használati feltételeket?**<br />
A: [áttekintheti a korábban elfogadott használati feltételeket](#how-users-can-review-their-terms-of-use), de jelenleg nem lehet visszafogadni.

**K: mi történik, ha az Intune használati feltételeit is használom?**<br />
A: Ha az Azure AD használati feltételeit és az [Intune használati](/intune/terms-and-conditions-create)feltételeit is beállította, akkor a felhasználónak mindkettőt el kell fogadnia. További információ: a [megfelelő feltételek megoldásának kiválasztása a szervezet blogbejegyzésében](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

**K: milyen végpontokat használnak a használati feltételek a hitelesítéshez?**<br />
A: a Használati feltételek a következő végpontokat használja a hitelesítéshez: https://tokenprovider.termsofuse.identitygovernance.azure.com és https://account.activedirectory.windowsazure.com . Ha a szervezete rendelkezik a beléptetési URL-címek engedélyezési listájával, ezeket a végpontokat fel kell vennie az engedélyezési listára, valamint a bejelentkezéshez használt Azure AD-végpontokat is.

## <a name="next-steps"></a>Következő lépések

- [Rövid útmutató: a használati feltételek elfogadásának megkövetelése a felhőalapú alkalmazások elérése előtt](require-tou.md)
- [Ajánlott eljárások a feltételes hozzáféréshez Azure Active Directory](best-practices.md)
