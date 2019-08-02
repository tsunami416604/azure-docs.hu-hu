---
title: Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon | Microsoft Docs
description: A bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon – bevezetés
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6121ca6c1636c8839110712310a1b94fe7fada49
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619264"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon

A Azure Active Directory (Azure AD) jelentéskészítési architektúrája a következő összetevőkből áll:

- **Tevékenység** 
    - **Bejelentkezések** – információk a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatáról.
    - **A naplók** - naplózása rendszertevékenység-[információkat biztosít a](concept-audit-logs.md) felhasználókról és a csoport kezeléséről, a felügyelt alkalmazásokról és a címtárbeli tevékenységekről.
- **Biztonság** 
    - **Kockázatos bejelentkezések** – a [kockázatos bejelentkezés](concept-risky-sign-ins.md) egy olyan bejelentkezési kísérletre utal, amelyet a felhasználói fiók jogos tulajdonosaként elvégeztek.
    - Kockázatnak kitett **felhasználók** – a [kockázatos felhasználók](concept-user-at-risk.md) egy olyan felhasználói fiókra vonatkozó jelző, amely esetleg sérült.

Ez a témakör áttekintést nyújt a bejelentkezési jelentésről.

## <a name="prerequisites"></a>Előfeltételek

### <a name="who-can-access-the-data"></a>Ki férhet hozzá az adatokhoz?
* A biztonsági rendszergazda, a biztonsági olvasó és a jelentéskészítő olvasó szerepköreinek felhasználói
* Globális rendszergazdák
* Emellett a felhasználók (nem rendszergazdák) is hozzáférhetnek a saját bejelentkezésekhez 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Milyen Azure AD-licencre van szükség a bejelentkezési tevékenységhez való hozzáféréshez?
* A bérlőnek prémium szintű Azure AD licenccel kell rendelkeznie ahhoz, hogy láthassa az összes bejelentkezési tevékenység jelentését. A Azure Active Directory kiadásának frissítéséhez tekintse meg a [prémium szintű Azure Active Directory első lépéseivel foglalkozó](../fundamentals/active-directory-get-started-premium.md) témakört. Vegye figyelembe, hogy ha a frissítés előtt nem rendelkezik tevékenységi adataival, a rendszer több napot is igénybe vesz, hogy az adatai megjelenjenek a jelentésekben a prémium szintű licencre való frissítés után.

## <a name="sign-ins-report"></a>Bejelentkezések jelentés

A felhasználói bejelentkezések jelentése a következő kérdésekre ad választ:

* Milyen egy adott felhasználó bejelentkezési mintázata?
* Hány felhasználó jelentkezett be egy adott héten?
* Milyen állapotúak ezek a bejelentkezések?

A bejelentkezési jelentést a [Azure Portal](https://portal.azure.com) **Azure Active Directory** paneljének **tevékenységek** szakaszában található bejelentkezések lehetőség kiválasztásával érheti el. Vegye figyelembe, hogy egyes bejelentkezési rekordok esetében akár két órát is igénybe vehet, hogy megjelenjenek a portálon.

![Bejelentkezési tevékenység](./media/concept-sign-ins/61.png "Sign-in activity")

> [!IMPORTANT]
> A bejelentkezési jelentés csak azokat az **interaktív** bejelentkezéseket jeleníti meg, amelyek a felhasználók saját felhasználónevével és jelszavával történő manuális bejelentkezéssel jelentkeznek be. A nem interaktív bejelentkezések, mint például a szolgáltatások közötti hitelesítés, nem jelennek meg a bejelentkezési jelentésben. 

A bejelentkezési napló alapértelmezett listanézete az alábbi adatokat jeleníti meg:

- A bejelentkezés dátuma
- A kapcsolódó felhasználó
- Az alkalmazás, amelybe a felhasználó bejelentkezett
- A bejelentkezési állapot
- A kockázatészlelés állapota
- A többtényezős hitelesítési (MFA-) követelmény állapota

![Bejelentkezési tevékenység](./media/concept-sign-ins/01.png "Sign-in activity")

A listanézetet az eszköztár **Oszlopok** elemére kattintva lehet testre szabni.

![Bejelentkezési tevékenység](./media/concept-sign-ins/19.png "Sign-in activity")

További mezőket jeleníthet meg, vagy eltávolíthatja a már megjelenített mezőket.

![Bejelentkezési tevékenység](./media/concept-sign-ins/02.png "Sign-in activity")

Részletesebb információkhoz jelöljön ki egy elemet a listanézet nézetben.

![Bejelentkezési tevékenység](./media/concept-sign-ins/03.png "Sign-in activity")

> [!NOTE]
> Az ügyfelek mostantól a bejelentkezési jelentéseken keresztül is elhárítják a feltételes hozzáférési házirendeket. Ha a bejelentkezési rekord **feltételes hozzáférés** lapjára kattint, az ügyfelek áttekinthetik a feltételes hozzáférési állapotot, és bemutatják a bejelentkezésre alkalmazott szabályzatok részleteit, valamint az egyes házirendek eredményét.
> További információ: a [hitelesítésszolgáltatói információkkal kapcsolatos gyakori kérdések az összes bejelentkezésnél](reports-faq.md#conditional-access).



## <a name="filter-sign-in-activities"></a>A bejelentkezési tevékenységek szűrése

A jelentett adatok leszűkíthető egy olyan szintre, amely az Ön számára működik, a Date (dátum) mező használatával szűrheti a bejelentkezési adatok alapértelmezett szűrőként való szűrését. Az Azure AD emellett számos további szűrőt is beállíthat.

![Bejelentkezési tevékenység](./media/concept-sign-ins/04.png "Sign-in activity")

A **Felhasználó** szűrővel egy konkrét felhasználó nevét vagy egyszerű felhasználónevét (UPN) adhatja meg.

Az **Alkalmazás** szűrővel egy konkrét alkalmazás nevét adhatja meg.

A **Bejelentkezési állapot** szűrővel az alábbi lehetőségek közül választhat:

- Összes
- Sikeres
- Hiba

A **feltételes hozzáférés** szűrővel kiválaszthatja a bejelentkezéshez használt hitelesítésszolgáltatói házirend állapotát:

- Összes
- Nem alkalmazott
- Siker
- Hiba

A **Dátum** szűrővel időkeretet lehet meghatározni a visszaadott adatokhoz.  
Lehetséges értékek a következők:

- 1 hónap
- 7 nap
- 24 óra
- Egyéni időintervallum

Egyéni időkeret kiválasztásakor beállíthatja a kezdő és a záró időpontot.

Ha további mezőket ad hozzá a bejelentkezési nézethez, a rendszer automatikusan hozzáadja a mezőket a szűrőlistához. Például az **Ügyfélalkalmazás** mező listához való hozzáadásával kap egy további szűrőlehetőséget, amellyel a következő szűrőket állíthatja be:  
![Bejelentkezési tevékenység](./media/concept-sign-ins/12.png "Sign-in activity")

- **Böngésző**  
    Ez a szűrő minden olyan eseményt megjelenít, amelyben a bejelentkezési kísérletek a böngészőalapú folyamatokkal lettek elvégezve.
- **Exchange ActiveSync (támogatott)**  
    Ez a szűrő az összes olyan bejelentkezési kísérletet megjeleníti, ahol az Exchange ActiveSync (EAS) protokoll a támogatott platformokon, például az iOS, az Android és a Windows Phone-telefon által lett megkísérelve.
- **Exchange ActiveSync (nem támogatott)**  
    Ez a szűrő az összes olyan bejelentkezési kísérletet megjeleníti, ahol az EAS protokollt a nem támogatott platformok, például a Linux-disztribúciók próbálták meg.
- **Mobile apps és asztali ügyfelek** Ez a szűrő az összes olyan bejelentkezési kísérletet megjeleníti, amely nem használ böngészőalapú folyamatokat. A mobil alkalmazások bármely platformról használhatók bármilyen protokoll vagy asztali ügyfélalkalmazások, például az Office Windows vagy MacOS rendszeren való használatával.
  
- **Egyéb ügyfelek**
    - **IMAP**  
        Egy örökölt levelezési ügyfélprogram, amely az IMAP használatával kéri le az e-maileket.
    - **MAPI**  
        Office 2013, ahol a ADAL engedélyezve van, és a MAPI-t használja.
    - **Régebbi Office-ügyfelek**  
        Az Office 2013 az alapértelmezett konfigurációban, ahol a ADAL nincs engedélyezve, és a MAPI vagy az Office 2016, ahol a ADAL le van tiltva.
    - **POP**  
        Egy örökölt levelezési ügyfélprogram, amely a POP3 használatával kéri le az e-maileket.
    - **SMTP**  
        Egy örökölt levelezési ügyfélprogram, amely az SMTP protokollal küld e-mailt.

## <a name="download-sign-in-activities"></a>Bejelentkezési tevékenységek letöltése

Ha a Azure Portalon kívül szeretné használni, akkor [letöltheti a bejelentkezési](quickstart-download-sign-in-report.md) adatmennyiséget. A **Letöltés** lehetőségre kattintva LÉTREHOZHAT egy CSV-vagy JSON-fájlt a legfrissebb 250 000-rekordokhoz.  

![Letöltés](./media/concept-sign-ins/71.png "Letöltés")

> [!IMPORTANT]
> A letöltendő rekordok számát a [Azure Active Directory jelentés](reference-reports-data-retention.md)adatmegőrzési szabályzata korlátozza.  


## <a name="sign-ins-data-shortcuts"></a>Bejelentkezési adathivatkozások

Az Azure AD mellett a Azure Portal további belépési pontokat biztosít a bejelentkezési adatokhoz:

- Az Identitáskezelés biztonsági védelme – áttekintés
- Felhasználók
- Csoportok
- Vállalati alkalmazások

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Felhasználói bejelentkezések adatai az Identity Security Protectionben

A felhasználó bejelentkezési diagramja az **Identitáskezelés biztonsági védelme** áttekintő oldalon az adott időszakban az összes felhasználóhoz tartozó bejelentkezések heti összesítéseit jeleníti meg. Az alapértelmezett időszak 30 nap.

![Bejelentkezési tevékenység](./media/concept-sign-ins/06.png "Sign-in activity")

A bejelentkezési grafikon egyik napjára kattintva áttekintést kap az adott nap bejelentkezési tevékenységeiről.

A bejelentkezési tevékenységek listájának minden sora a következőkről ad információkat:

* Ki jelentkezett be?
* Melyik alkalmazás volt a bejelentkezés célja?
* Mi a bejelentkezés állapota?
* Mi a bejelentkezés MFA-állapota?

Az elemekre kattintva részletes információk érhetők el a bejelentkezési műveletről:

- Felhasználói azonosító
- Felhasználó
- Felhasználónév
- Alkalmazásazonosító
- Alkalmazás
- Ügyfél
- Location
- IP-cím
- Date
- MFA szükséges
- Bejelentkezési állapot

> [!NOTE]
> Az IP-címek oly módon vannak kiadva, hogy az IP-cím és az azt tartalmazó számítógép fizikailag ne legyen végleges kapcsolat. Az IP-címek leképezése az a tény, hogy a mobilszolgáltatók és a VPN-ek olyan központi készletekből származó IP-címeket adnak ki, amelyek gyakran nagyon távol vannak az ügyfél eszközének tényleges használatáról. Jelenleg az Azure AD-jelentésekben az IP-címek fizikai helyre konvertálása a legjobb megoldás a Nyomkövetések, a beállításjegyzék-adatok, a fordított irányú keresés és egyéb információk alapján.

A **Felhasználók** oldalon teljes körű áttekintést kaphat az összes felhasználói bejelentkezésről a **Tevékenységek** szakaszban található **Bejelentkezések** elemre kattintva.

![Bejelentkezési tevékenység](./media/concept-sign-ins/08.png "Sign-in activity")

## <a name="usage-of-managed-applications"></a>Felügyelt alkalmazások használati adatai

A bejelentkezési információk alkalmazás-központú nézetével az alábbi kérdésekre kaphat választ:

* Ki használja az alkalmazásaimat?
* Melyik a szervezet 3 legnépszerűbb alkalmazása?
* Nemrégiben új alkalmazást adtam ki. Mennyire sikeres?

Az adatok megtekintését a szervezet az elmúlt 30 nap alatt legnépszerűbb 3 alkalmazásáról szóló jelentéssel kezdheti az **Áttekintés** szakaszban, a **Vállalati alkalmazások** területen.

![Bejelentkezési tevékenység](./media/concept-sign-ins/10.png "Sign-in activity")

Az alkalmazás-használati gráf hetente összesíti az első 3 alkalmazáshoz tartozó bejelentkezési adatokat egy adott időszakban. Az alapértelmezett időszak 30 nap.

![Bejelentkezési tevékenység](./media/concept-sign-ins/47.png "Sign-in activity")

Igény esetén egy adott alkalmazást is kiemelhet.

![Jelentéskészítés](./media/concept-sign-ins/single_spp_usage_graph.png "Jelentéskészítés")

Az alkalmazáshasználati grafikon egyik napjára kattintva részletes listát kap a bejelentkezési tevékenységekről.

A **Bejelentkezések** lehetőség az alkalmazások összes bejelentkezési eseményének teljes körű áttekintését biztosítja.

![Bejelentkezési tevékenység](./media/concept-sign-ins/11.png "Sign-in activity")

## <a name="office-365-activity-logs"></a>Office 365-tevékenységek naplói

Az Office 365-tevékenységek naplóit a [Microsoft 365 felügyeleti](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center)központból tekintheti meg. Annak ellenére, hogy az Office 365-tevékenység és az Azure AD-tevékenység naplói nagy mennyiségű címtár-erőforrást osztanak meg, csak a Microsoft 365 felügyeleti központ teljes képet nyújt az Office 365-tevékenységek naplóiról. 

Az Office 365-tevékenység naplóit programozott módon is elérheti az [office 365 felügyeleti API](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview)-k használatával.

## <a name="next-steps"></a>További lépések

* [Bejelentkezési tevékenység jelentésének hibakódja](reference-sign-ins-error-codes.md)
* [Az Azure AD adatmegőrzési szabályzatai](reference-reports-data-retention.md)
* [Azure AD-jelentés késései](reference-reports-latencies.md)

