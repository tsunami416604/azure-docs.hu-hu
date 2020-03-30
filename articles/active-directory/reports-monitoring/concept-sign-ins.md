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
ms.date: 03/24/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2910933e2c57a8bc80a220726462b02915c4a8eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246517"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon

Az Azure Active Directory (Azure AD) jelentéskészítési architektúrája a következő összetevőkből áll:

- **Tevékenység** 
    - **Bejelentkezések** – A felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatával kapcsolatos információk.
    - **Naplók Naplónaplók**rendszertevékenységi információkat nyújtanak a felhasználókról és a csoportkezelésről, a felügyelt alkalmazásokról és a címtártevékenységekről.[Audit logs](concept-audit-logs.md)  - 
- **Biztonság** 
    - **Kockázatos bejelentkezések** – A [kockázatos bejelentkezés](concept-risky-sign-ins.md) egy olyan személy bejelentkezési kísérletének jelzője, aki nem a felhasználói fiók jogos tulajdonosa.
    - **A kockázatra megjelölt felhasználók** – A [kockázatos felhasználó](concept-user-at-risk.md) egy olyan felhasználói fiók jelzője, amely veszélybe kerülhetett.

Ez a cikk áttekintést nyújt a bejelentkezések jelentés.

## <a name="prerequisites"></a>Előfeltételek

### <a name="who-can-access-the-data"></a>Ki férhet hozzá az adatokhoz?

* A Biztonsági rendszergazda, a Biztonsági olvasó, a Globális olvasó és a Jelentésolvasó szerepkörben szereplő felhasználók
* Globális rendszergazdák
* Bármely (nem rendszergazda jogosultságú) felhasználó hozzáfér a saját bejelentkezéseihez 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Milyen Azure AD-licencre van szükség a bejelentkezési tevékenységhez való hozzáféréshez?

- A bejelentkezési tevékenység jelentés az [Azure AD összes kiadásában](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data)elérhető.

- Ha api-val szeretné elérni a bejelentkezési adatokat, a bérlőnek rendelkeznie kell egy [Azure Active Directory Prémium licenccel.](../fundamentals/active-directory-get-started-premium.md)



## <a name="sign-ins-report"></a>Bejelentkezési jelentés

A felhasználói bejelentkezések jelentés választ ad a következő kérdésekre:

* Milyen egy adott felhasználó bejelentkezési mintázata?
* Hány felhasználó jelentkezett be egy adott héten?
* Milyen állapotúak ezek a bejelentkezések?

Az [Azure Portal](https://portal.azure.com) menüben válassza az **Azure Active Directory**t, vagy keresse meg és válassza ki az Azure Active **Directoryt** bármelyik lapról.

![Az Azure Active Directory kiválasztása](./media/concept-sign-ins/select-azure-active-directory.png "Azure Active Directory")

A **Figyelés** **csoportban** válassza a Bejelentkezések lehetőséget a [Bejelentkezések jelentés](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)megnyitásához.

![Bejelentkezési tevékenység](./media/concept-sign-ins/monitoring-sign-ins-in-azure-active-directory.png "Bejelentkezési tevékenység")

A bejelentkezési rekordok megjelenítése akár két órát is igénybe vehet.

> [!IMPORTANT]
> A bejelentkezések jelentés csak az **interaktív** bejelentkezéseket jeleníti meg, azaz azokat a bejelentkezéseket, amelyekben a felhasználó manuálisan jelentkezik be a felhasználónevével és jelszavával. A nem interaktív bejelentkezések, például a szolgáltatás-szolgáltatás hitelesítése, nem jelennek meg a bejelentkezések jelentésben. 

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

Az **Oszlopok** párbeszédpanel hozzáférést biztosít a választható attribútumokhoz. A bejelentkezési jelentésben nem rendelkezhet olyan mezőkkel, amelyek egy adott bejelentkezési kérelemhez egynél több értéket oszlopként használhatnak. Ez például a hitelesítési adatok, a feltételes hozzáférési adatok és a hálózati hely esetén igaz.   

![Bejelentkezési tevékenység](./media/concept-sign-ins/columns.png "Bejelentkezési tevékenység")

A részletesebb információk megtekintéséhez jelöljön ki egy elemet a listanézetben.

![Bejelentkezési tevékenység](./media/concept-sign-ins/basic-sign-in.png "Bejelentkezési tevékenység")

> [!NOTE]
> Az ügyfelek mostantól az összes bejelentkezési jelentésben elháríthatják a feltételes hozzáférési házirendek hibáit. A feltételes **hozzáférés** fülre kattintva az ügyfelek áttekinthetik a feltételes hozzáférés állapotát, és áttekinthetik a bejelentkezésre alkalmazott szabályzatok részleteit és az egyes házirendek eredményét.
> További információt a [hitelesítésakkal kapcsolatos gyakori kérdések](reports-faq.md#conditional-access)ben talál az összes bejelentkezésben.



## <a name="filter-sign-in-activities"></a>A bejelentkezési tevékenységek szűrése

Először is, leszűkíti a jelentett adatokat az Ön számára működő szintre. Másodszor, szűrje a bejelentkezési adatokat a dátummező alapértelmezett szűrőjeként. Az Azure AD a következő további szűrők széles körét kínálja:

![Bejelentkezési tevékenység](./media/concept-sign-ins/04.png "Bejelentkezési tevékenység")

**Kérelem azonosítója** - Az Ön számára fontos kérés azonosítója.

**Felhasználó** – a felhasználó neve vagy egyszerű felhasználóneve (UPN).

**Alkalmazás** - A célalkalmazás neve.
 
**Állapot** – A számára fontos bejelentkezési állapot:

- Sikeres

- Hiba

- Megszakított


**IP-cím** – A bérlőhöz való csatlakozáshoz használt eszköz IP-címe.

A **hely** – az a hely, ahonnan a kapcsolatot kezdeményezték:

- Város

- Állam / tartomány

- Ország/régió


**Erőforrás** – a bejelentkezéshez használt szolgáltatás neve.


**Erőforrás-azonosító** – a bejelentkezéshez használt szolgáltatás azonosítója.


**Ügyfélalkalmazás** – A bérlőhöz való csatlakozáshoz használt ügyfélalkalmazás típusa:

![Ügyfélalkalmazás-szűrő](./media/concept-sign-ins/client-app-filter.png)


|Név|Modern hitelesítés|Leírás|
|---|:-:|---|
|Hitelesített SMTP| |A POP és az IMAP-ügyfél használja e-mailek küldésére.|
|Automatikus észlelés| |Az Outlook és az EAS-ügyfelek segítségével kereshet postaládákat az Exchange Online-ban, és csatlakozhat azokhoz.|
|Exchange ActiveSync| |Ez a szűrő az összes olyan bejelentkezési kísérletet megjeleníti, ahol az EAS protokollt megkísérelték.|
|Böngésző|![Jelölőnégyzet](./media/concept-sign-ins/check.png)|A webböngészőt használó felhasználók bejelentkezési kísérleteinek megjelenítése|
|Exchange ActiveSync| | Az Exchange ActiceSync programot használó ügyfélalkalmazásokkal rendelkező felhasználók összes bejelentkezési kísérletének megjelenítése az Exchange Online-hoz való csatlakozáshoz|
|Exchange Online PowerShell| |Az Exchange Online-hoz távoli PowerShell használatával való csatlakozásra szolgál. Ha letiltja az Exchange Online PowerShell alapszintű hitelesítését, a csatlakozáshoz az Exchange Online PowerShell modult kell használnia. További információt a [Csatlakozás az Exchange Online PowerShellhez többtényezős hitelesítéssel című](https://docs.microsoft.com/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)témakörben talál.|
|Webes Exchange-szolgáltatások| |Az Outlook, a Mac Outlook és a külső alkalmazások által használt programozási felület.|
|IMAP4| |Örökölt levelezőprogram, amely az IMAP protokollt használja az e-mailek beolvasásához.|
|MAPI HTTP-n keresztül| |Az Outlook 2010-es és újabb verziói használják.|
|Mobilalkalmazások és asztali ügyfelek|![Jelölőnégyzet](./media/concept-sign-ins/check.png)|Megjeleníti a mobilalkalmazásokat és asztali ügyfeleket használó felhasználók bejelentkezési kísérleteit.|
|Kapcsolat nélküli címjegyzék| |Az Outlook által letöltött és használt címlista-gyűjtemények másolata.|
|Outlook Anywhere (RPC HTTP-n keresztül)| |Az Outlook 2016-ban és korábbi akta i.|
|Outlook-szolgáltatás| |A Windows 10 Posta és Naptár alkalmazása használja.|
|POP3| |Örökölt levelezőprogram, amely POP3 használatával olvassa be az e-maileket.|
|Webszolgáltatások jelentése| |Jelentésadatok beolvasására szolgál az Exchange Online-ban.|
|Más ügyfelek| |Megjeleníti az összes olyan felhasználó bejelentkezési kísérletét, ahol az ügyfélalkalmazás nem szerepel vagy ismeretlen.|



**Operációs rendszer** – Az eszközön futó operációs rendszer bejelentkezést használt a bérlőhöz. 


**Eszközböngésző** – Ha a kapcsolatot böngészőből kezdeményezte, ez a mező lehetővé teszi a böngésző nevének szűrését.


**Korrelációs azonosító** – a tevékenység korrelációs azonosítója.




**Feltételes hozzáférés** – Az alkalmazott feltételes hozzáférési szabályok állapota

- **Nincs alkalmazva:** Nincs házirend a felhasználóra és az alkalmazásra a bejelentkezés során.

- **Sikeres:** A felhasználóra és az alkalmazásra alkalmazott egy vagy több feltételes hozzáférési szabályzat (de nem feltétlenül a többi feltétel) a bejelentkezés során. 

- **Hiba**: Egy vagy több feltételes hozzáférési szabályzat ot alkalmaztak, és nem teljesültek a bejelentkezés során.









## <a name="download-sign-in-activities"></a>Bejelentkezési tevékenységek letöltése

Kattintson a **Letöltés** gombra a legutóbbi 250 000 rekordból álló CSV- vagy JSON-fájl létrehozásához. Kezdje [a bejelentkezési adatok letöltésével,](quickstart-download-sign-in-report.md) ha az Azure Portalon kívül szeretne dolgozni vele.  

![Letöltés](./media/concept-sign-ins/71.png "Letöltés")

> [!IMPORTANT]
> A letölthető rekordok számát az [Azure Active Directory jelentésmegőrzési szabályzata](reference-reports-data-retention.md)korlátozza.  


## <a name="sign-ins-data-shortcuts"></a>Bejelentkezési adatbillentyűparancsok

Az Azure AD és az Azure Portal egyaránt további belépési pontokat biztosít a bejelentkezési adatokhoz:

- Az identitásbiztonság védelme – áttekintés
- Felhasználók
- Csoportok
- Vállalati alkalmazások

### <a name="users-sign-ins-data-in-identity-security-protection"></a>A felhasználók bejelentkeznek az adatokhoz az identitásbiztonság védelmében

A felhasználói bejelentkezési grafikon az **identitásbiztonsági védelem** áttekintése lapon a bejelentkezések heti összesítéseit jeleníti meg. Az időszak alapértelmezett ideje 30 nap.

![Bejelentkezési tevékenység](./media/concept-sign-ins/06.png "Bejelentkezési tevékenység")

A bejelentkezési grafikon egyik napjára kattintva áttekintést kap az adott nap bejelentkezési tevékenységeiről.

A bejelentkezési tevékenységek listájának minden sora a következőkről ad információkat:

* Ki jelentkezett be?
* Melyik alkalmazás volt a bejelentkezés célja?
* Mi a bejelentkezés állapota?
* Mi a bejelentkezés MFA-állapota?

Az elemekre kattintva részletes információk érhetők el a bejelentkezési műveletről:

- Felhasználóazonosító
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
> Az IP-címek kiadása oly módon történik, hogy nincs végleges kapcsolat az IP-cím és az adott címmel rendelkező számítógép fizikai elhelyezkedése között. Az IP-címek leképezését bonyolítja az a tény, hogy a mobilszolgáltatók és a VPN-ek olyan központi készletekből adnak ki IP-címeket, amelyek gyakran nagyon távol vannak attól, ahol az ügyféleszközt ténylegesen használják. Jelenleg az Azure AD-jelentésekben az IP-cím fizikai helyre való konvertálása a legjobb megoldás a nyomon követések, a beállításjegyzék-adatok, a névfeloldási és egyéb információk alapján.

A **Felhasználók** oldalon teljes körű áttekintést kaphat az összes felhasználói bejelentkezésről a **Tevékenységek** szakaszban található **Bejelentkezések** elemre kattintva.

![Bejelentkezési tevékenység](./media/concept-sign-ins/08.png "Bejelentkezési tevékenység")

## <a name="usage-of-managed-applications"></a>Felügyelt alkalmazások használati adatai

A bejelentkezési információk alkalmazás-központú nézetével az alábbi kérdésekre kaphat választ:

* Ki használja az alkalmazásaimat?
* Melyek a szervezet három legfontosabb alkalmazásai?
* Hogy van a legújabb alkalmazásom?

Az adatok belépési pontja a szervezet három legfontosabb alkalmazása. Az adatok az elmúlt 30 napban a **Vállalati alkalmazások** **áttekintése** szakaszának jelentésében találhatók.

![Bejelentkezési tevékenység](./media/concept-sign-ins/10.png "Bejelentkezési tevékenység")

Az alkalmazáshasználat grafikonok heti összesítései bejelentkezések az első három alkalmazás egy adott időszakban. Az alapértelmezett időszak 30 nap.

![Bejelentkezési tevékenység](./media/concept-sign-ins/graph-chart.png "Bejelentkezési tevékenység")

Igény esetén egy adott alkalmazást is kiemelhet.

![Jelentési](./media/concept-sign-ins/single-app-usage-graph.png "Jelentéskészítés")

Az alkalmazáshasználati grafikon egyik napjára kattintva részletes listát kap a bejelentkezési tevékenységekről.

A **Bejelentkezések** lehetőség az alkalmazások összes bejelentkezési eseményének teljes körű áttekintését biztosítja.

## <a name="office-365-activity-logs"></a>Office 365 tevékenységi naplói

Az Office 365 tevékenységnaplóit a [Microsoft 365 Felügyeleti központból](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center)tekintheti meg. Vegye figyelembe azt a pontot, hogy az Office 365-tevékenység és az Azure AD tevékenységnaplók osztoznak a címtár-erőforrások jelentős részét. Csak a Microsoft 365 Felügyeleti központ nyújt teljes képet az Office 365 tevékenységnaplóiról. 

Az Office 365 tevékenységnaplóit programozott módon is elérheti az [Office 365 Felügyeleti API-k](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview)használatával.

## <a name="next-steps"></a>További lépések

* [Bejelentkezési tevékenység jelentés hibakódjai](reference-sign-ins-error-codes.md)
* [Azure AD adatmegőrzési szabályzatok](reference-reports-data-retention.md)
* [Az Azure AD-jelentés késései](reference-reports-latencies.md)

