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
ms.date: 10/28/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 778353621491f912d3237900785e6dee17bf975e
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014493"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon

A Azure Active Directory (Azure AD) jelentéskészítési architektúrája a következő összetevőkből áll:

- **Tevékenység** 
    - **Bejelentkezések** – információk a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatáról.
    - **Naplók** - a [naplók](concept-audit-logs.md) rendszertevékenységi információkat biztosítanak a felhasználókról és a csoport kezeléséről, a felügyelt alkalmazásokról és a címtár-tevékenységekről.
- **Biztonság** 
    - **Kockázatos bejelentkezések** – a [kockázatos bejelentkezés](concept-risky-sign-ins.md) egy olyan bejelentkezési kísérlet, amely nem a felhasználói fiók legitim tulajdonosa.
    - **Kockázatnak** kitett felhasználók – a [kockázatos felhasználók](concept-user-at-risk.md) egy olyan felhasználói fiókra vonatkozó jelző, amely esetleg sérült.

Ez a cikk áttekintést nyújt a bejelentkezési jelentésről.

## <a name="prerequisites"></a>Előfeltételek

### <a name="who-can-access-the-data"></a>Ki férhet hozzá az adatokhoz?

* A biztonsági rendszergazda, a biztonsági olvasó és a jelentéskészítő-olvasó szerepkörrel rendelkező felhasználók
* Globális rendszergazdák
* Bármely (nem rendszergazda jogosultságú) felhasználó hozzáfér a saját bejelentkezéseihez 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Milyen Azure AD-licencre van szükség a bejelentkezési tevékenységhez való hozzáféréshez?

* A bérlőnek prémium szintű Azure AD licenccel kell rendelkeznie ahhoz, hogy láthassa az összes bejelentkezési tevékenység jelentését. A Azure Active Directory kiadásának frissítéséhez tekintse meg a [prémium szintű Azure Active Directory első lépéseivel foglalkozó](../fundamentals/active-directory-get-started-premium.md) témakört. A frissítés előtt néhány napig is eltarthat, hogy az adatai megjelenjenek a jelentésekben, miután a frissítés előtt adattevékenység nélküli prémium szintű licencre frissít.

## <a name="sign-ins-report"></a>Bejelentkezések jelentés

A felhasználói bejelentkezések jelentése a következő kérdésekre ad választ:

* Milyen egy adott felhasználó bejelentkezési mintázata?
* Hány felhasználó jelentkezett be egy adott héten?
* Milyen állapotúak ezek a bejelentkezések?

Kezdje [Azure Portal](https://portal.azure.com). A bejelentkezési jelentés eléréséhez válassza a **bejelentkezések**lehetőséget, folytassa a **figyeléssel.** Néhány bejelentkezési rekord esetében akár két órát is igénybe vehet, hogy megjelenjenek a portálon.

![Bejelentkezési tevékenység](./media/concept-sign-ins/reporting-azure-sign-in-screen.png "Bejelentkezési tevékenység")

> [!IMPORTANT]
> A bejelentkezési jelentés csak azokat az **interaktív** bejelentkezéseket jeleníti meg, amelyek a felhasználók saját felhasználónevével és jelszavával történő manuális bejelentkezéssel jelentkeznek be. A nem interaktív bejelentkezések, mint például a szolgáltatások közötti hitelesítés, nem jelennek meg a bejelentkezési jelentésben. 

A bejelentkezési napló alapértelmezett listanézete az alábbi adatokat jeleníti meg:

- A bejelentkezés dátuma
- A kapcsolódó felhasználó
- Az alkalmazás, amelybe a felhasználó bejelentkezett
- A bejelentkezési állapot
- A kockázatészlelés állapota
- A többtényezős hitelesítési (MFA-) követelmény állapota

![Bejelentkezési tevékenység](./media/concept-sign-ins/sign-in-activity.png "Bejelentkezési tevékenység")

A listanézetet az eszköztár **Oszlopok** elemére kattintva lehet testre szabni.

![Bejelentkezési tevékenység](./media/concept-sign-ins/19.png "Bejelentkezési tevékenység")

További mezőket vagy a már megjelenített mezők eltávolítását jeleníti meg.

![Bejelentkezési tevékenység](./media/concept-sign-ins/02.png "Bejelentkezési tevékenység")

Részletesebb információkhoz jelöljön ki egy elemet a listanézet nézetben.

![Bejelentkezési tevékenység](./media/concept-sign-ins/basic-sign-in.png "Bejelentkezési tevékenység")

> [!NOTE]
> Az ügyfelek mostantól a bejelentkezési jelentéseken keresztül is elhárítják a feltételes hozzáférési házirendeket. Ha a bejelentkezési rekord **feltételes hozzáférés** lapjára kattint, az ügyfelek áttekinthetik a feltételes hozzáférési állapotot, és bemutatják a bejelentkezésre alkalmazott szabályzatok részleteit, valamint az egyes házirendek eredményét.
> További információ: a [hitelesítésszolgáltatói információkkal kapcsolatos gyakori kérdések az összes bejelentkezésnél](reports-faq.md#conditional-access).



## <a name="filter-sign-in-activities"></a>A bejelentkezési tevékenységek szűrése

Először Szűkítse le a jelentett adathalmazt egy olyan szintre, amely az Ön számára működik. Másodszor, a Date mezőt használó bejelentkezési adatok szűrése alapértelmezett szűrőként. Az Azure AD a további beállítható szűrők széles választékát kínálja.

![Bejelentkezési tevékenység](./media/concept-sign-ins/04.png "Bejelentkezési tevékenység")

A **Felhasználó** szűrővel egy konkrét felhasználó nevét vagy egyszerű felhasználónevét (UPN) adhatja meg.

Az **Alkalmazás** szűrővel egy konkrét alkalmazás nevét adhatja meg.

A **Bejelentkezési állapot** szűrővel az alábbi lehetőségek közül választhat:

- Összes
- Sikeres
- Hiba

A **feltételes hozzáférés** szűrővel kiválaszthatja a bejelentkezéshez használt hitelesítésszolgáltatói házirend állapotát:

- Összes
- Nincs alkalmazva
- Sikeres
- Hiba

A **Dátum** szűrővel időkeretet lehet meghatározni a visszaadott adatokhoz.  
Lehetséges értékek:

- Egy hónap
- 7 nap
- 24 óra
- Egyéni időintervallum

Egyéni időkeret kiválasztásakor beállíthatja a kezdő és a záró időpontot.

Ha további mezőket ad hozzá a bejelentkezési nézethez, a rendszer automatikusan hozzáadja a mezőket a szűrőlistához. Például az **Ügyfélalkalmazás** mező listához való hozzáadásával kap egy további szűrőlehetőséget, amellyel a következő szűrőket állíthatja be:  
![Bejelentkezési tevékenység](./media/concept-sign-ins/12.png "Bejelentkezési tevékenység")

- **Böngésző**  
    Ez a szűrő megjeleníti az összes olyan eseményt, amelyben a bejelentkezési kísérletek a böngészőalapú folyamatokkal próbálkoztak.
- **Exchange ActiveSync (támogatott)**  
    Ez a szűrő az összes olyan bejelentkezési kísérletet megjeleníti, ahol az Exchange ActiveSync (EAS) protokoll a támogatott platformokon, például az iOS, az Android és a Windows Phone-telefon által lett megkísérelve.
- **Exchange ActiveSync (nem támogatott)**  
    Ez a szűrő az összes olyan bejelentkezési kísérletet megjeleníti, ahol az EAS protokollt a nem támogatott platformok, például a Linux-disztribúciók próbálták meg.
- **Mobile apps és asztali ügyfelek** A szűrő az összes olyan bejelentkezési kísérletet megjeleníti, amely nem használ böngészőalapú folyamatokat. Például a Mobile apps bármely platformról bármely protokoll használatával vagy asztali ügyfélalkalmazások, például az Office Windows vagy MacOS rendszeren.
  
- **Egyéb ügyfelek**
    - **IMAP**  
        Egy örökölt levelezési ügyfélprogram, amely az IMAP használatával kéri le az e-maileket.
    - **MAPI**  
        Office 2013, ahol a ADAL engedélyezve van, és a MAPI-t használja.
    - **Régi Office-ügyfelek**  
        Az Office 2013 az alapértelmezett konfigurációban, ahol a ADAL nincs engedélyezve, és a MAPI vagy az Office 2016, ahol a ADAL le van tiltva.
    - **POP**  
        Egy örökölt levelezési ügyfélprogram, amely a POP3 használatával kéri le az e-maileket.
    - **SMTP**  
        Egy örökölt levelezési ügyfélprogram, amely az SMTP protokollal küld e-mailt.

## <a name="download-sign-in-activities"></a>Bejelentkezési tevékenységek letöltése

Kattintson a **Letöltés** lehetőségre a legfrissebb 250 000-rekordok CSV-vagy JSON-fájljának létrehozásához. Ha a Azure Portalon kívül szeretne dolgozni vele, kezdje [a bejelentkezési adatszolgáltatások letöltésével](quickstart-download-sign-in-report.md) .  

![Letöltés](./media/concept-sign-ins/71.png "Letöltés")

> [!IMPORTANT]
> A letöltendő rekordok számát a [Azure Active Directory jelentés adatmegőrzési szabályzata](reference-reports-data-retention.md)korlátozza.  


## <a name="sign-ins-data-shortcuts"></a>Bejelentkezési adathivatkozások

Az Azure AD és a Azure Portal egyaránt biztosít további belépési pontokat a bejelentkezési adathoz:

- Az Identitáskezelés biztonsági védelme – áttekintés
- Felhasználók
- Csoportok
- Vállalati alkalmazások

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Felhasználói bejelentkezések adatai az Identity Security Protectionben

A felhasználói bejelentkezési gráf a **személyazonosság biztonsági védelme** áttekintése oldalon a bejelentkezések heti összesítéseit jeleníti meg. Az időtartam alapértelmezett értéke 30 nap.

![Bejelentkezési tevékenység](./media/concept-sign-ins/06.png "Bejelentkezési tevékenység")

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
- Hely
- IP-cím
- Dátum
- Az MFA megadása kötelező
- Bejelentkezési állapot

> [!NOTE]
> Az IP-címek oly módon vannak kiadva, hogy az IP-cím és az azt tartalmazó számítógép fizikailag ne legyen végleges kapcsolat. Az IP-címek leképezése az a tény, hogy a mobilszolgáltatók és a VPN-ek olyan központi készletekből származó IP-címeket adnak ki, amelyek gyakran nagyon távol vannak az ügyfél eszközének tényleges használatáról. Jelenleg az Azure AD-jelentésekben az IP-címek fizikai helyre konvertálása a legjobb megoldás a Nyomkövetések, a beállításjegyzék-adatok, a fordított irányú keresés és egyéb információk alapján.

A **Felhasználók** oldalon teljes körű áttekintést kaphat az összes felhasználói bejelentkezésről a **Tevékenységek** szakaszban található **Bejelentkezések** elemre kattintva.

![Bejelentkezési tevékenység](./media/concept-sign-ins/08.png "Bejelentkezési tevékenység")

## <a name="usage-of-managed-applications"></a>Felügyelt alkalmazások használati adatai

A bejelentkezési információk alkalmazás-központú nézetével az alábbi kérdésekre kaphat választ:

* Ki használja az alkalmazásaimat?
* Mi a szervezet első három alkalmazása?
* Hogyan csinálom a legújabb alkalmazást?

Ezen adat beléptetési pontja a szervezet első három alkalmazása. Az adat a **vállalati alkalmazások** **Áttekintés** szakaszában, az elmúlt 30 nap jelentésében található.

![Bejelentkezési tevékenység](./media/concept-sign-ins/10.png "Bejelentkezési tevékenység")

Az alkalmazás-használati gráfok hetente összesítik az első három alkalmazás bejelentkezéseit egy adott időszakban. Az alapértelmezett időszak 30 nap.

![Bejelentkezési tevékenység](./media/concept-sign-ins/graph-chart.png "Bejelentkezési tevékenység")

Igény esetén egy adott alkalmazást is kiemelhet.

![Jelentéskészítés](./media/concept-sign-ins/single-app-usage-graph.png "Jelentéskészítés")

Az alkalmazáshasználati grafikon egyik napjára kattintva részletes listát kap a bejelentkezési tevékenységekről.

A **Bejelentkezések** lehetőség az alkalmazások összes bejelentkezési eseményének teljes körű áttekintését biztosítja.

## <a name="office-365-activity-logs"></a>Office 365-tevékenységek naplói

Az Office 365-tevékenységek naplóit a [Microsoft 365 felügyeleti központból](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center)tekintheti meg. Vegye figyelembe, hogy az Office 365-tevékenység és az Azure AD-tevékenység naplói jelentős számú címtár-erőforrást osztanak meg. Csak a Microsoft 365 felügyeleti központ teljes képet nyújt az Office 365-tevékenység naplóiról. 

Az Office 365-tevékenység naplóit programozott módon is elérheti az [office 365 felügyeleti API](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview)-k használatával.

## <a name="next-steps"></a>Következő lépések

* [Bejelentkezési tevékenység jelentésének hibakódja](reference-sign-ins-error-codes.md)
* [Az Azure AD adatmegőrzési szabályzatai](reference-reports-data-retention.md)
* [Azure AD-jelentés késései](reference-reports-latencies.md)

