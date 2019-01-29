---
title: Azure Active Directory – Használati feltételek| Microsoft Docs
description: Ismerteti, hogyan kezdheti el az Azure AD használati feltételek a szükséges információkkal az alkalmazottak vagy a Vendégek, mielőtt hozzáférhetne az.
services: active-directory
author: rolyon
manager: mtillman
editor: ''
ms.assetid: d55872ef-7e45-4de5-a9a0-3298e3de3565
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 01/10/2019
ms.author: rolyon
ms.openlocfilehash: be8a6ba83de81c5a82797ecf3d30c7a7fcaa65fa
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55162769"
---
# <a name="azure-active-directory-terms-of-use-feature"></a>Azure Active Directory – Használati feltételek funkció
Az Azure AD Használati feltételek funkcióján keresztül a vállalatok egyszerűen láthatják el a végfelhasználókat a szükséges információkkal. Ez a bemutató gondoskodik arról, hogy a felhasználók megkapják a jogi vagy megfelelőségi követelményekre vonatkozó nyilatkozatokat. Ez a cikk azt ismerteti, hogyan kezdheti el a használati feltételeket tartalmazó fájl.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Áttekintő videók

Az alábbi videó a használati feltételeket tartalmazó fájl gyors áttekintést nyújt.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

További videók lásd:
- [Az Azure Active Directoryban használati feltételek központi telepítése](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Hogyan vezethet be használati feltételeket az Azure Active Directoryban](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>Mire használhatom a Használati feltételek funkciót?
Az Azure AD használati feltételek a következő képességekkel rendelkezik:
- Az alkalmazottak vagy a vendégfelhasználókat arra, hogy fogadja el a használati feltételeket, mielőtt hozzáférhetne van szükség.
- Az alkalmazottak vagy a vendégfelhasználókat arra, hogy fogadja el a használati feltételeket minden eszközön, mielőtt hozzáférhetne van szükség.
- Az alkalmazottak vagy a vendégfelhasználókat arra, hogy fogadja el a használati feltételeket, ismétlődő ütemezés szerint szükséges.
- A vállalat minden felhasználójára vonatkozó általános használati feltételeket nyújt.
- Specifikus használati feltételek biztosít a felhasználó jellemzői alapján (például orvosok kontra nővérek, belföldi kontra nemzetközi alkalmazottak [dinamikus csoportjai](../users-groups-roles/groups-dynamic-membership.md)).
- Specifikus használati feltételeket biztosít a kulcsfontosságú üzleti alkalmazások (például Salesforce) elérése esetén.
- Megjeleníti a használati feltételeket különböző nyelveken.
- Lista, aki, vagy még nem elfogadott a használati feltételeket.
- Segít az adatvédelmi előírások teljesítése.
- Feltételek használata tevékenység megfelelőségi és naplózási naplóját jeleníti meg.
- Létrehozásához és kezeléséhez használja a feltételek [Microsoft Graph API-k](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement) (jelenleg előzetes verzióban érhető el).

## <a name="prerequisites"></a>Előfeltételek
Az Azure AD Használati feltételek funkció konfigurálásához a következők szükségesek:

- Prémium szintű Azure AD P1, P2, EMS E3 vagy EMS E5 előfizetés.
    - Ha még nem rendelkezik ezen előfizetések egyikével sem, [beszerezhet Prémium szintű Azure AD előfizetést](../fundamentals/active-directory-get-started-premium.md), vagy [engedélyezheti a Prémium szintű Azure AD előfizetés próbaverzióját](https://azure.microsoft.com/trial/get-started-active-directory/).
- A következő rendszergazda fiókok egyike a konfigurálni kívánt könyvtárhoz:
    - Globális rendszergazda
    - Biztonsági rendszergazda
    - Feltételes hozzáférésű rendszergazda

## <a name="terms-of-use-document"></a>A használati feltételek dokumentuma

Az Azure AD Használati feltételek PDF formátumot használ a tartalmak megjelenítéséhez. A PDF-fájl bármilyen tartalmat jelenthet, akár meglévő szerződéseket is, így begyűjtheti a végfelhasználói megállapodásokat a felhasználók bejelentkezésekor. Felhasználók a mobileszközök támogatására, a PDF-dokumentumban ajánlott betűméret az 24 pont.

## <a name="add-terms-of-use"></a>Használati feltételek hozzáadása
A használati feltételek dokumentum véglegesítése után a hozzáadáshoz kövesse a következő eljárást.

1. Jelentkezzen be az Azure-ban, egy globális rendszergazdai, biztonsági rendszergazdának vagy feltételes hozzáférésű rendszergazda.

1. Lépjen a **Használati feltételekre** a következőn: [https://aka.ms/catou](https://aka.ms/catou).

    ![Használati feltételek panel](./media/active-directory-tou/tou-blade.png)

1. Kattintson az **Új feltételek** lehetőségre.

    ![Használati feltételek hozzáadása](./media/active-directory-tou/new-tou.png)

1. Az a **neve** mezőbe írjon be egy nevet a használati feltételeket, amely használható az Azure Portalon.

1. Az a **megjelenítendő név** mezőbe írja be a címet látják a felhasználók bejelentkezéskor.

1. A **használati feltételek dokumentuma**, keresse meg a véglegesített használati feltételek PDF-fájlt, és válassza ki azt.

1. A használati feltételek dokumentuma nyelvének kiválasztása. A nyelvi beállítás lehetővé teszi, hogy több használati feltételt töltsön fel különböző nyelveken. A használati feltételek végfelhasználó számára megjelenített verziója a böngésző beállításaitól függ.

1. A végfelhasználóknak megtekinteni a használati feltételeket, mielőtt elfogadhatnák őket, állítsa **bontsa ki a használati feltételeket, hogy a felhasználók** való **a**.

1. A végfelhasználók számára, hogy fogadja el a használati feltételeket minden eszközön érnek el a szükséges, állítsa be **kérése a felhasználóktól minden eszközön jóváhagyást** való **a**. További információkért lásd: [eszközönkénti használati feltételeit](#per-device-terms-of-use).

1. Ha szeretne érvényessége lejár használati feltételeket tartalmazó fájl jóváhagy egy ütemezés szerint, és állítsa **címtárbérlőhöz lejár** való **a**. Érték beállítása a esetén két további beállítások jelennek meg.

    ![A hozzájárulások lejárnak](./media/active-directory-tou/expire-consents.png)

1. Használja a **től elévülési** és **gyakorisága** beállításainak megadásával határozza meg a használati feltételeket az ütemezés használata lejárhat. Az alábbi táblázatban néhány példa beállítások láthatók:

    | Lejárat kezdete | Gyakoriság | Eredmény |
    | --- | --- | --- |
    | A mai napig  | Havi | Kezdve felhasználók minden hónapban majd elfogadást, és kell fogadja el a használati feltételeket. |
    | Jövőbeli dátumot  | Havi | Kezdve felhasználóknak el kell fogadniuk a használati feltételeket. A jövőbeli dátumot esetén a hozzájárulásokat le fog járni, és ezután a felhasználók hatókörébe, havonta kell.  |

    Például, ha a lejárati dátumot, a kezdési **jan. 1** és gyakoriságának **havi**, hogyan lejárhat két olyan felhasználó esetében fordulhat elő, a következő:

    | Felhasználó | Először fogadja el a dátum | Először lejárati dátuma | A második lejárati dátuma | Harmadik lejárati dátuma |
    | --- | --- | --- | --- | --- |
    | Alice | Január 1 | Feb. 1-től | Március 1 | Diagramhalmazban 1 |
    | Bob | Január 15- | Feb. 1-től | Március 1 | Diagramhalmazban 1 |

1. Használja a **időtartam, mielőtt újra-elfogadása szükséges (nap)** beállítást adja meg, hány nap elteltével a felhasználó a használati feltételeket kell hatókörébe. Ez lehetővé teszi a felhasználók a saját ütemezés számára. Ha például azt az időtartamot **30** nap, hogyan lejárhat két olyan felhasználó esetében fordulhat elő, a következő:

    | Felhasználó | Először fogadja el a dátum | Először lejárati dátuma | A második lejárati dátuma | Harmadik lejárati dátuma |
    | --- | --- | --- | --- | --- |
    | Alice | Január 1 | Január 31-ig | Március 2 | Diagramhalmazban 1 |
    | Bob | Január 15- | Február 14 | Március 16 | Diagramhalmazban 15 |

    Lehet használni a **címtárbérlőhöz lejár** és **időtartam, mielőtt újra-elfogadása szükséges (nap)** beállítások együtt, de általában olyan, az egyiket használja.

1. A **feltételes hozzáférési**, használja a **sablonnal feltételes hozzáférési szabályzat kényszerítése** listát használva jelölje ki a sablon kényszeríteni a használati feltételeket.

    ![Feltételes hozzáférés sablonok](./media/active-directory-tou/conditional-access-templates.png)

    | Sablon | Leírás |
    | --- | --- |
    | **Az összes vendégek felhőalkalmazások elérése** | Feltételes hozzáférési szabályzat jön létre minden Vendég és az összes felhőalapú alkalmazásokba. Ez a szabályzat milyen hatással van az Azure Portalon. Miután ez elkészült, akkor szükség lehet kijelentkezés és bejelentkezés. |
    | **Az összes felhasználó számára a felhőalkalmazások elérése** | Feltételes hozzáférési szabályzat jön létre minden felhasználó és az összes felhőalapú alkalmazásokba. Ez a szabályzat milyen hatással van az Azure Portalon. Ennek létrehozása után kijelentkezés és bejelentkezés szükséges fogjuk. |
    | **Egyéni szabályzat** | Jelölje ki azon felhasználókat, csoportokat és alkalmazásokat, amelyekre a használati feltételek alkalmazandók lesznek. |
    | **Feltételes hozzáférési szabályzat létrehozása később** | A használati feltételek az engedélyezési vezérlői listában fognak szerepelni a feltételes hozzáférési szabályzat létrehozásakor. |

    >[!IMPORTANT]
    >A feltételes hozzáférési szabályzat vezérlői (beleértve a használati feltételeket) nem támogatják a szolgáltatásfiókokra irányuló kényszerítést. Javasoljuk, hogy zárjon ki minden szolgáltatásfiókot a feltételes hozzáférési szabályzatból.

     Az egyéni feltételes hozzáférési szabályzat részletes használati feltételek alkalmazását teszi lehetővé, amelyeket akár egy adott felhőalkalmazásra vagy felhasználói csoportra is leszűkíthet. További információkért lásd: [a rövid útmutató: Felhőbeli alkalmazások elérése előtt el kell fogadni a használati feltételeket tartalmazó fájl szükséges](../conditional-access/require-tou.md).

1. Kattintson a **Create** (Létrehozás) gombra.

    Ha egy egyéni feltételes hozzáférési sablont választott, majd egy új képernyő jelenik meg, amely lehetővé teszi, hogy az egyéni feltételes hozzáférési szabályzat létrehozása.

    ![Egyéni szabályzat](./media/active-directory-tou/custom-policy.png)

    Ezután már az új használati feltételek jelennek meg.

    ![Használati feltételek hozzáadása](./media/active-directory-tou/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Jelentés megtekintése, akik elfogadó és elutasító
A használati feltételek panel megjeleníti a feltételeket elfogadó, illetve elutasító felhasználók számát. A rendszer mindaddig megőrzi ezeket az eredményeket, illetve az elfogadó/elutasító felhasználók adatait, amíg a használati feltételek léteznek.

1. Jelentkezzen be az Azure-ba, és lépjen a **Használati feltételekre** a [https://aka.ms/catou](https://aka.ms/catou) címen.

    ![Használati feltételek panel](./media/active-directory-tou/view-tou.png)

1. A használati feltételeket, kattintson a számokat **elfogadva** vagy **elutasítva** a felhasználók aktuális állapotának megtekintéséhez.

    ![Használati feltételek jóváhagyásai](./media/active-directory-tou/accepted-tou.png)

1. Az egyes felhasználók számára az előzmények megtekintéséhez kattintson a három pontra (**...** ), majd **előzményeinek megtekintése**.

    ![Előzménynapló menüje megtekintése](./media/active-directory-tou/view-history-menu.png)

    A korábbi panelen, megjelenik az összes a fogad, csökken, illetve lejárhat.

    ![Panel előzményeinek megtekintése](./media/active-directory-tou/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Naplók megtekintése az Azure AD
Az Azure AD Használati feltételek auditnaplókkal is rendelkezik, ha megtekintené a feltételekkel kapcsolatos további tevékenységeket is. Minden egyes felhasználói beleegyezés elindít egy eseményt az auditnaplókban tárolt a **30 napig**. Ezeket a naplókat megtekintheti a portálon, vagy letöltheti egy .csv fájlban.

Ismerkedés az Azure AD-naplók, kövesse az alábbi eljárást:

1. Jelentkezzen be az Azure-ba, és lépjen a **Használati feltételekre** a [https://aka.ms/catou](https://aka.ms/catou) címen.

1. Válassza ki a használati feltételeket.

1. Kattintson a **Naplók megtekintése** elemre.

    ![Használati feltételek panel](./media/active-directory-tou/audit-tou.png)

1. Az Azure AD naplókat megjelenítő képernyőjén naplózása, szűrheti az adatokat a megadott listákat az adott naplózási információkra cél használatával.

    A **Letöltés** elemre kattintva az információkat helyi felhasználás céljából egy .csv fájlba is mentheti.

    ![Naplók](./media/active-directory-tou/audit-logs-tou.png)

    Ha egy napló gombra kattint, további tevékenység részletei ablaktábla jelenik meg.

    ![Tevékenység részletei](./media/active-directory-tou/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>A használati feltételek megjelenése a felhasználók számára
A használati feltételek létrehozása és kényszerítése után az érintett felhasználók az alábbi képernyőt látják a bejelentkezés során.

![Felhasználói web-bejelentkezés](./media/active-directory-tou/user-tou.png)

Az alábbi képernyő bemutatja a használati feltételek megjelenését a mobileszközökön.

![Felhasználói utazó bejelentkezés](./media/active-directory-tou/mobile-tou.png)

A felhasználóknak csak kell fogadja el a használati feltételeket egyszer, és nem jelenik meg a használati feltételeket újra a későbbi bejelentkezések.

### <a name="how-users-can-review-their-terms-of-use"></a>A használati feltételek áttekintése a felhasználók által
A felhasználók az alábbi eljárással tekinthetik át az általuk elfogadott használati feltételeket.

1. Jelentkezzen be itt: [https://myapps.microsoft.com](https://myapps.microsoft.com).

1. A jobb felső sarokban, kattintson a nevére, és válassza **profil**.

    ![Profil](./media/active-directory-tou/tou14.png)

1. A Profi lapon kattintson a **Használati feltételek áttekintése** elemre.

    ![Profil – használati feltételek áttekintése](./media/active-directory-tou/tou13a.png)

1. Itt áttekintheti az elfogadott használati feltételeket.

## <a name="edit-terms-of-use-details"></a>Használati feltételek részleteinek szerkesztése
Szerkesztheti a használati feltételeket tartalmazó fájl bizonyos részleteit, de nem módosíthatja egy meglévő dokumentumot. Az alábbi eljárás ismerteti, hogyan lehet az adatok szerkesztéséhez.

1. Jelentkezzen be az Azure-ba, és lépjen a **Használati feltételekre** a [https://aka.ms/catou](https://aka.ms/catou) címen.

1. Válassza ki a szerkeszteni kívánt használati feltételeket.

1. Kattintson a **feltételeket tartalmazó fájl szerkesztése**.

1. A használati ablaktábla Szerkesztés feltételeit módosítsa a nevet, megjelenítendő nevet vagy értékek felhasználóknak legyen kötelező.

    Ha más beállítás, amelyet szeretne módosítani, például PDF-dokumentum, kérése a felhasználóktól minden eszközön hozzájárulás, hozzájárulást, hamarosan lejár időtartama reacceptance, vagy a feltételes hozzáférési szabályzat, előtt létre kell hoznia egy új használati feltételek.

    ![Használati feltételeket tartalmazó fájl szerkesztése](./media/active-directory-tou/edit-tou.png)

1. Kattintson a **mentése** a módosítások mentéséhez.

    A módosítások mentése után a felhasználók nem kell ezeket a szerkesztéseket hatókörébe.

## <a name="add-a-terms-of-use-language"></a>A használati feltételek használata nyelv hozzáadása
Az alábbi eljárás ismerteti, hogyan lehet a használati feltételek használata nyelv hozzáadása.

1. Jelentkezzen be az Azure-ba, és lépjen a **Használati feltételekre** a [https://aka.ms/catou](https://aka.ms/catou) címen.

1. Válassza ki a szerkeszteni kívánt használati feltételeket.

1. A részleteket tartalmazó ablaktáblán kattintson a **nyelvek** fülre.

    ![Használati feltételek hozzáadása](./media/active-directory-tou/languages-tou.png)

1. Kattintson a **nyelv hozzáadása**.

1. A honosított PDF-fájl feltöltése a Hozzáadás használati használata nyelvi ablaktáblán, és válassza ki a nyelvet.

    ![Használati feltételek hozzáadása](./media/active-directory-tou/language-add-tou.png)

1. Kattintson a **Hozzáadás** a nyelv hozzáadása.

## <a name="per-device-terms-of-use"></a>Az eszközszintű használati feltételeket

A **kérése a felhasználóktól minden eszközön jóváhagyást** beállítás lehetővé teszi, hogy a végfelhasználók számára, hogy fogadja el a használati feltételeket minden eszközön érnek el a szükséges. A felhasználó köteles csatlakoztassák az eszközüket az Azure ad-ben. Amikor az eszköz tartományhoz csatlakozik, az eszköz azonosítója segítségével kényszerítése a használati feltételeket minden eszközön.

Íme a támogatott platformokról és a szoftverek listáját.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Egyéb |
> | --- | --- | --- | --- | --- |
> | **Natív alkalmazás** | Igen | Igen | Igen |  |
> | **Microsoft Edge** | Igen | Igen | Igen |  |
> | **Az Internet Explorer** | Igen | Igen | Igen |  |
> | **Chrome (kiterjesztésű)** | Igen | Igen | Igen |  |

Az eszközönkénti használati feltételeket tartalmazó fájl a következő korlátozások vonatkoznak:

- Egy eszköz csak egyetlen bérlő lehet csatlakoztatni.
- A felhasználónak jogosultnak kell lennie csatlakoztassák az eszközüket.
- Az Intune regisztrációs alkalmazás nem támogatott.

Ha a felhasználó eszköze nincs tartományhoz csatlakoztatva, egy üzenet csatlakoztassák az eszközüket a kapja meg. Szerzett függ a szoftver és platform lesz.

### <a name="join-a-windows-10-device"></a>Windows 10-es eszköz csatlakoztatása

Ha a felhasználó nem használja a Windows 10-es és a Microsoft Edge, a következőhöz hasonló üzenetet kapja [csatlakoztassák az eszközüket](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device).

![Windows 10-es és a Microsoft Edge - illesztési eszköz kérése](./media/active-directory-tou/per-device-win10-edge.png)

Ha Chrome, kérni fogja telepíteni a [Windows 10-es fiókok bővítmény](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="browsers"></a>Böngészők

Ha egy felhasználó által nem támogatott böngészőt használ, azokat egy másik böngészőben használandó adnia.

![Nem támogatott böngészők](./media/active-directory-tou/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Használati feltételek törlése
A korábbi használati feltételeket az alábbi eljárással törölheti.

1. Jelentkezzen be az Azure-ba, és lépjen a **Használati feltételekre** a [https://aka.ms/catou](https://aka.ms/catou) címen.

1. Válassza ki az eltávolítani kívánt használati feltételeket.

1. Kattintson a **Feltételek törlése** lehetőségre.

1. A megjelenő üzenetben, amely a törlés megerősítését kéri, kattintson az **Igen** lehetőségre.

    ![Használati feltételek törlése](./media/active-directory-tou/delete-tou.png)

    A használati feltételek ezt követően már nem láthatók.

## <a name="deleted-users-and-active-terms-of-use"></a>Törölt felhasználók az aktív használati feltételekben
Alapértelmezés szerint a törölt felhasználók 30 napig még törölt állapotban megmaradnak az Azure AD-ban, amely idő alatt szükség esetén egy rendszergazda visszaállíthatja őket. A felhasználók 30 nap után véglegesen törlődnek. Ezenkívül egy globális rendszergazda az Azure Active Directory portálon explicit módon [véglegesen törölhet egy közelmúltban törölt felhasználót](../fundamentals/active-directory-users-restore.md) az időszak lejárta előtt. Egy felhasználó végleges törlése után a felhasználó későbbi adatai el lesznek távolítva az aktív használati feltételekből. A törölt felhasználókkal kapcsolatos naplózási információk az auditnaplóban maradnak.

## <a name="policy-changes"></a>A szabályzatok változásai
A feltételes hozzáférési szabályzatok azonnal hatályba lépnek. Ha ez történik, a rendszergazda "Szomorú felhők" vagy "A tokenekkel kapcsolatos problémák az Azure AD" indul el. A rendszergazda kell jelentkezzen ki, és jelentkezzen be újra annak érdekében, hogy megfelelhessen az új szabályzatnak.

>[!IMPORTANT]
> Az érintett felhasználóknak újra be kell jelentkezniük, hogy megfelelhessenek az új szabályzatnak, ha:
> - egy feltételes hozzáférési szabályzat van engedélyezve a használati feltételekhez
> - vagy újabb használati feltételek lettek létrehozva

## <a name="b2b-guests-preview"></a>B2B-vendégek (előzetes verzió)

A legtöbb szervezet tartalmaznak egy folyamat a felhasználók számára, hogy engedélyt adjanak a cég használati feltételei és adatvédelmi nyilatkozatok. De hogyan is kényszeríti az azonos hozzájárulást az Azure AD-vállalatok (B2B), amikor hozzáadják a SharePoint vagy a Teams vendégek? Feltételes hozzáférési és használati feltételeket tartalmazó fájl segítségével, akkor olyan szabályzatot alkalmazhatnak közvetlenül a B2B-vendég felhasználók felé. A meghívó érvényesítési folyamat során a felhasználó a használati feltételek egyike. Ez a támogatás jelenleg előzetes verzióban érhető el.

Használati feltételek csak jelenik meg, ha a felhasználó egy vendégfiókba rendelkezik az Azure ad-ben. SharePoint online-ban jelenleg rendelkezik egy [ad hoc külső megosztási címzett felülettel](/sharepoint/what-s-new-in-sharing-in-targeted-release) megosztani egy dokumentumot vagy egy mappát, amely nem igényel a felhasználót, hogy a Vendég fiókkal rendelkezik. Ebben az esetben a használati feltételeket tartalmazó fájl nem jelenik meg.

![Minden vendégfelhasználó](./media/active-directory-tou/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>Felhőbeli alkalmazások (előzetes verzió) támogatása

Másik felhőalapú alkalmazások, például az Azure Information Protection és a Microsoft Intune használati feltételeit is használható. Ez a támogatás jelenleg előzetes verzióban érhető el.

### <a name="azure-information-protection"></a>Azure Information Protection

Az Azure Information Protection alkalmazásra vonatkozó feltételes hozzáférési házirend konfigurálása és a használati feltételek kérése, amikor egy felhasználó hozzáfér egy védett dokumentumot. Ezzel elindítja a használati feltételeket, mielőtt a felhasználó első alkalommal fér hozzá a védett dokumentum.

![Az Azure Information Protection cloud app](./media/active-directory-tou/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>A Microsoft Intune-regisztráció

Feltételes hozzáférési szabályzatot konfigurálni ahhoz a Microsoft Intune regisztrációs alkalmazást, és a használati feltételeket, mielőtt az eszköz Intune-beli regisztrációját megkövetelése. További információkért lásd: az olvasási [kiválasztani a feltételek megoldást kínál a szervezet blogbejegyzés](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

![A Microsoft Intune felhőalapú alkalmazás](./media/active-directory-tou/cloud-app-intune.png)

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**K: Hogyan ellenőrizhetem amikor / felhasználó a használati feltételek elfogadása?**<br />
V: A panel használata a feltételeket, kattintson a számra **elfogadva**. Is megtekintése vagy keressen a elfogadás tevékenység az Azure AD-ben a naplók. További információkért lásd: [megtekintheti a jelentést, akik elfogadó és elutasító](#view-who-has-accepted-and-declined) és [naplók megtekintése az Azure AD](#view-azure-ad-audit-logs).

**K: Mennyi ideig őrzi meg tárolt adatokat?**<br />
V: A felhasználó-adatokra is használja a jelentés és aki elfogadott/nem fogadta el a használati feltételeket élettartama tárolt feltételeit. Az Azure AD naplózási naplót 30 napig tárol.

**K: Miért látok a feltételeit, és az Azure AD használati jelentés címtárbérlőhöz különböző számú naplók?**<br />
V: A használati jelentés használata a használati feltételeket, miközben a naplót 30 napig tárol az Azure AD naplózási élettartama nem történik. A használati jelentés használatát is, csak a felhasználók aktuális jóváhagyási állapotot jeleníti meg. Például ha egy felhasználó elutasítja, és majd elfogadja a használati jelentés használata csak akkor jelenik meg, hogy a felhasználó fogadja el. Ha szeretne látni a korábbi, használhatja az Azure AD auditnaplóinak.

**K: Tudom szerkeszteni a részletek a használati feltételeket, ha nem írja elő a felhasználóknak újra el kell fogadniuk?**<br />
V: Nem, ha egy rendszergazda szerkeszt a részletek a használati feltételek (nevet, megjelenítendő nevet, felhasználóknak legyen kötelező vagy nyelv hozzáadása), nem szükséges a felhasználók számára az új feltételeket hatókörébe.

**K: Frissíti egy meglévő használati feltételek dokumentuma is?**<br />
V: Jelenleg nem lehet frissíteni egy meglévő használati feltételek dokumentuma. Ha módosítani szeretné a használati feltételek dokumentuma, akkor új feltételek használata-példány létrehozása.

**K: Ha a hivatkozások a használati feltételek PDF dokumentuma, a végfelhasználók fogja tudni azokra kattint?**<br />
V: A PDF-fájl jelenik meg alapértelmezés szerint, JPEG, így nem kattintható hivatkozások állnak. Felhasználók rendelkeznek a kívánt **megtekintéssel? Kattintson ide a**, amely a PDF-fájl rendereli natív módon, a hivatkozások támogatottak.

**K: Használati feltételek több nyelv támogatása is?**<br />
V: Igen. Jelenleg nincsenek 108 több különböző nyelvet a rendszergazda konfigurálhatja egy egyetlen használati feltételeit. A rendszergazdák több PDF-dokumentumok feltöltése, és ezeket a dokumentumokat a megfelelő nyelvű (akár 108) címkét. Amikor a végfelhasználók jelentkeznek be, azt tekintse meg a böngésző nyelvi beállításait, és megjeleníti az egyező dokumentumok. Ha nem szerepel, hogy az alapértelmezett dokumentum, amely az első dokumentum feltöltött jelennek meg.

**K: Ha van a használati feltételek?**<br />
V: A használati feltételeket a bejelentkezési élmény során aktiválódik.

**K: Mely alkalmazásokhoz használhatom a használati feltételeket?**<br />
V: Feltételes hozzáférési szabályzatot a modern hitelesítést használó vállalati alkalmazásokhoz hozhat létre. Bővebb információ: [vállalati alkalmazások](./../manage-apps/view-applications-portal.md).

**K: Egy adott felhasználó vagy alkalmazás is hozzáadhatok több használati feltételeket?**<br />
V: Igen, több feltételes hozzáférési szabályzatok létrehozásával adott csoportokhoz vagy alkalmazásokhoz. Ha egy felhasználó több használati hatókörébe esik, el kell fogadniuk egy használati feltételek egyszerre.

**K: Mi történik, ha a felhasználó elutasítja a használati feltételeket?**<br />
V: A felhasználó hozzáférése az alkalmazáshoz való hozzáférés le van tiltva. Jelentkezzen be újra, és fogadja el a feltételeket ahhoz, hogy a hozzáférést a felhasználónak kell.

**K: Az is unaccept a korábban elfogadott használati feltételeket?**<br />
V: Is [felülvizsgálati korábban elfogadott használati feltételeket tartalmazó fájl](#how-users-can-review-their-terms-of-use), de jelenleg nincs unaccept lehetővé.

**K: Mi történik, ha az Intune használati feltételeket is használata?**<br />
V: Ha konfigurálta is az Azure AD használati feltételek és [Intune használati feltételek](/intune/terms-and-conditions-create), a felhasználó köteles fogadnia mindkettőt. További információkért lásd: a [kiválasztani a feltételek megoldást kínál a szervezet blogbejegyzés](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

## <a name="next-steps"></a>További lépések

- [Rövid útmutató: Felhőbeli alkalmazások elérése előtt el kell fogadni a használati feltételeket tartalmazó fájl megkövetelése](../conditional-access/require-tou.md)
- [A feltételes hozzáféréssel kapcsolatos ajánlott eljárások az Azure Active Directoryban](../conditional-access/best-practices.md)
