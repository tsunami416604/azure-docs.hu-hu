---
title: Örökölt hitelesítés tiltása – Azure Active Directory
description: Ismerje meg, hogyan javíthatja biztonsági állapotát azáltal, hogy blokkolja az örökölt hitelesítést az Azure AD feltételes hozzáférés használatával.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 957aa77e18ea8f910f258d1dc59de0d093b0eab6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80476660"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Útmutató: az Azure AD-val való örökölt hitelesítés letiltása feltételes hozzáféréssel   

Ahhoz, hogy a felhasználók könnyen hozzáférhessenek a felhőalapú alkalmazásokhoz, Azure Active Directory (Azure AD) a hitelesítési protokollok széles választékát támogatja, beleértve az örökölt hitelesítést is. A régi protokollok azonban nem támogatják a többtényezős hitelesítést (MFA). Az MFA számos környezetben gyakori követelmény a személyazonossági lopás kezelése. 

Alex Weinert, a Microsoft Identity Security igazgatója, az 2020-as március 12-én, a [szervezet örökölt hitelesítésének blokkolására szolgáló új eszközöket](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302#) ismertető blogjában hangsúlyozza, hogy miért blokkolja a szervezetek az örökölt hitelesítést, és hogy a Microsoft milyen további eszközöket biztosít a feladat végrehajtásához:

> Ahhoz, hogy az MFA hatékony legyen, le kell tiltania az örökölt hitelesítést is. Ennek az az oka, hogy az olyan örökölt hitelesítési protokollok, mint a POP, az SMTP, az IMAP és a MAPI, nem tudják érvényesíteni az MFA-t, így előnyben részesített belépési pontokat biztosítanak a szervezete
> 
>... Az Azure Active Directory (Azure AD) forgalmának elemzésével kapcsolatos örökölt hitelesítés számai:
> 
> - A jelszó-szórásos támadások több mint 99 százaléka használja az örökölt hitelesítési protokollokat
> - A hitelesítő adatok több mint 97 százaléka örökölt hitelesítést használ
> - Azure AD-fiókok olyan szervezeteknél, amelyek letiltották az örökölt hitelesítési élményt 67 százalékkal kevesebb kompromisszumot, mint az örökölt hitelesítés engedélyezése esetén
>

Ha a környezet készen áll az örökölt hitelesítés letiltására a bérlői védelem javítása érdekében, ezt a célt feltételes hozzáféréssel is elvégezheti. Ez a cikk azt ismerteti, hogyan konfigurálhatja a bérlő örökölt hitelesítését tiltó feltételes hozzáférési szabályzatokat.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy ismeri a következőket: 

- Az Azure AD feltételes hozzáférés [alapvető fogalmai](overview.md) 
- A feltételes hozzáférési házirendek konfigurálásának [ajánlott eljárásai](best-practices.md) a Azure Portal

## <a name="scenario-description"></a>Forgatókönyv leírása

Az Azure AD számos, a legszélesebb körben használt hitelesítési és engedélyezési protokollt támogat, beleértve az örökölt hitelesítést is. Az örökölt hitelesítés az alapszintű hitelesítést használó protokollokra vonatkozik. Ezek a protokollok általában nem kényszerítik ki a második faktoros hitelesítés típusát. Példák az örökölt hitelesítésen alapuló alkalmazásokra:

- Régebbi Microsoft Office alkalmazások
- Levelezési protokollokat (például POP, IMAP és SMTP) használó alkalmazások

Az egytényezős hitelesítés (például Felhasználónév és jelszó) nem elég ezekben a napokban. A jelszavak helytelenek, mert könnyen kitalálhatóak, és mi (emberek) rosszak a jó jelszavak kiválasztásakor. A jelszavakat számos különböző támadás, például az adathalászat és a jelszó-spray is sebezhetővé teszi. A jelszóval kapcsolatos fenyegetések elleni védelem egyik legegyszerűbb módja az MFA implementálása. Az MFA-val még akkor is, ha egy támadó beolvas egy felhasználói jelszót, a jelszó önmagában nem elegendő ahhoz, hogy sikeresen hitelesítse és hozzáférjen az adataihoz.

Hogyan akadályozhatja meg, hogy az örökölt hitelesítést használó alkalmazások hozzáférjenek a bérlő erőforrásaihoz? A javaslat célja, hogy csak feltételes hozzáférési házirenddel blokkolja őket. Ha szükséges, csak bizonyos felhasználóknak és adott hálózati helyszíneknek engedélyezi az örökölt hitelesítésen alapuló alkalmazások használatát.

A feltételes hozzáférési házirendeket az első tényezős hitelesítés befejeződése után kényszeríti ki a rendszer. Ezért a feltételes hozzáférés nem az első vonali védelem, mint például a szolgáltatásmegtagadási (DoS) támadások, de az ilyen eseményekből származó jeleket is használhat (például a bejelentkezési kockázati szint, a kérelem helye stb.) a hozzáférés meghatározásához.

## <a name="implementation"></a>Megvalósítás

Ez a szakasz azt ismerteti, hogyan lehet feltételes hozzáférési szabályzatot konfigurálni a régi hitelesítés blokkolására. 

### <a name="legacy-authentication-protocols"></a>Örökölt hitelesítési protokollok

A következő beállítások tekinthetők örökölt hitelesítési protokolloknak

- Hitelesített SMTP – a POP és az IMAP-ügyfél által használt e-mail üzenetek küldésére használható.
- Automatikus észlelés – az Outlook és az EAS-ügyfelek által használt postaládák keresése és kapcsolódás az Exchange Online-ban.
- Exchange Online PowerShell – az Exchange Online-hoz való kapcsolódáshoz használt távoli PowerShell-lel. Ha letiltja az Exchange Online PowerShell alapszintű hitelesítését, a kapcsolódáshoz az Exchange Online PowerShell-modult kell használnia. Útmutatásért lásd: [Kapcsolódás az Exchange Online powershellhez a multi-Factor Authentication használatával](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).
- Exchange Web Services (EWS) – az Outlook, az Outlook for Mac és a harmadik féltől származó alkalmazások által használt programozási felület.
- IMAP4 – az IMAP e-mail ügyfélprogramok által használt.
- MAPI HTTP-n keresztül (MAPI/HTTP) – az Outlook 2010-es és újabb verzióiban használt.
- Offline címjegyzék (OAB) – az Outlook által letöltött és használt címlista-gyűjtemények másolata.
- Outlook Anywhere (HTTP-n keresztüli RPC) – az Outlook 2016-es és korábbi verzióiban használatos.
- Outlook-szolgáltatás – a Windows 10 rendszerhez készült mail és naptár alkalmazás használja.
- POP3 – a POP e-mail ügyfélprogramok által használt.
- Jelentéskészítési webszolgáltatások – a Jelentésadatok beolvasására szolgálnak az Exchange Online-ban.
- Egyéb ügyfelek – az örökölt hitelesítés használatával azonosított egyéb protokollok.

További információ ezekről a hitelesítési protokollokról és szolgáltatásokról: [bejelentkezési tevékenységek jelentései a Azure Active Directory portálon](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities).

### <a name="identify-legacy-authentication-use"></a>Örökölt hitelesítési használat azonosítása

Mielőtt blokkolni tudja az örökölt hitelesítést a címtárban, először meg kell értenie, hogy a felhasználók rendelkeznek-e örökölt hitelesítést használó alkalmazásokkal, és hogy ez milyen hatással van a teljes címtárra. Az Azure AD bejelentkezési naplói segítségével megtudhatja, hogy örökölt hitelesítést használ-e.

1. Navigáljon a **Azure Portal** > **Azure Active Directory** > a**bejelentkezések**elemre.
1. Ha nem látható, akkor adja hozzá az ügyfélalkalmazás oszlopot, ha az **oszlopok** > **ügyfélalkalmazás**elemre kattint.
1. **Szűrők** > hozzáadása az**ügyfélalkalmazás** > válassza ki az összes örökölt hitelesítési protokollt, majd kattintson az **alkalmaz**gombra.

A szűrés csak az örökölt hitelesítési protokollok által végrehajtott bejelentkezési kísérleteket fogja megjeleníteni. Az egyes bejelentkezési kísérletekre kattintva további részleteket is megtudhat. Az **alapinformációk** lapon található **ügyfélalkalmazás** mező jelzi, hogy melyik örökölt hitelesítési protokollt használta a rendszer.

Ezek a naplók azt jelzik, hogy mely felhasználók maradnak az örökölt hitelesítéstől függően, és mely alkalmazások örökölt protokollokat használnak a hitelesítési kérések elvégzéséhez. Azoknál a felhasználóknál, akik nem jelennek meg a naplókban, és a rendszer megerősíti, hogy nem használ örökölt hitelesítést, csak ezekre a felhasználókra alkalmazza a feltételes hozzáférési szabályzatot.

### <a name="block-legacy-authentication"></a>Régi hitelesítési folyamat letiltása 

A feltételes hozzáférési szabályzatban beállíthat egy olyan feltételt, amely az erőforrásokhoz való hozzáféréshez használt ügyfélalkalmazások számára van kötve. Az ügyfélalkalmazások feltétele lehetővé teszi, hogy az örökölt hitelesítéssel szűkítse a hatókört az alkalmazásokra, ha az **Exchange ActiveSync-ügyfelek** és **más ügyfelek** lehetőséget választja a **Mobile apps és az asztali ügyfelek**területen.

![Más ügyfelek](./media/block-legacy-authentication/01.png)

Az alkalmazások hozzáférésének letiltásához válassza a **hozzáférés letiltása**lehetőséget.

![Hozzáférés letiltása](./media/block-legacy-authentication/02.png)

### <a name="select-users-and-cloud-apps"></a>Felhasználók és felhőalapú alkalmazások kiválasztása

Ha le szeretné tiltani a szervezet örökölt hitelesítését, valószínűleg úgy gondolja, hogy ezt a következő parancs kiválasztásával tudja elvégezni:

- Minden felhasználó
- Minden felhőalapú alkalmazás
- Hozzáférés letiltása

![Hozzárendelések](./media/block-legacy-authentication/03.png)

Az Azure biztonsági funkciója megakadályozza a szabályzatok létrehozását, mivel ez a konfiguráció sérti a feltételes hozzáférési házirendek [ajánlott eljárásait](best-practices.md) .
 
![A házirend konfigurációja nem támogatott](./media/block-legacy-authentication/04.png)

A biztonsági szolgáltatásra azért van szükség, mert az *összes felhasználó és az összes felhőalapú alkalmazás blokkolása* a teljes szervezet számára a bérlőre való bejelentkezéskor is lehetséges. Legalább egy felhasználót ki kell zárnia az ajánlott eljárások minimális követelményének kielégítése érdekében. Emellett kizárhat egy címtárbeli szerepkört is.

![A házirend konfigurációja nem támogatott](./media/block-legacy-authentication/05.png)

Ezt a biztonsági funkciót úgy érheti el, ha kizárja az egyik felhasználót a szabályzatból. Ideális esetben meg kell határoznia néhány [vészhelyzeti hozzáférésű rendszergazdai fiókot az Azure ad-ben](../users-groups-roles/directory-emergency-access.md) , és ki kell zárnia azokat a szabályzatból.

Ha a házirendet az örökölt hitelesítés blokkolására engedélyezi, a [csak jelentési mód](concept-conditional-access-report-only.md) lehetővé teszi a szervezet számára a házirend hatásának figyelését.

## <a name="policy-deployment"></a>Szabályzat érvénybe léptetése

A szabályzat éles környezetben történő üzembe helyezése előtt gondoskodjon a következőről:
 
- **Szolgáltatásfiókok** – a szolgáltatásfiókok vagy eszközök, például a konferenciatermek telefonja által használt felhasználói fiókok azonosítása. Győződjön meg arról, hogy ezek a fiókok erős jelszóval rendelkeznek, és vegye fel őket egy kizárt csoportba.
- **Bejelentkezési jelentések** – tekintse át a bejelentkezési jelentést, és keressen **más ügyfél** -forgalmat. Azonosítsa a leggyakoribb használatot, és vizsgálja meg, hogy miért van használatban. Általában a forgalmat olyan régebbi Office-ügyfelek generálják, amelyek nem használnak modern hitelesítést, vagy valamilyen harmadik féltől származó levelezési alkalmazást. Tervezze meg a használatot az alkalmazásokból, vagy ha a hatás alacsony, értesítse a felhasználókat, hogy többé nem tudják használni ezeket az alkalmazásokat.
 
További információ: [how is hogyan kell telepíteni az új szabályzatot?](best-practices.md#how-should-you-deploy-a-new-policy).

## <a name="what-you-should-know"></a>Alapismeretek

A **más ügyfelekkel** való hozzáférés letiltása az alapszintű hitelesítéssel blokkolja az Exchange Online PowerShell-t és a Dynamics 365-et is.

A házirend konfigurálása **más ügyfelek** számára a teljes szervezetet blokkolja bizonyos ügyfelektől, például a SPConnect. Ez a blokk azért fordul elő, mert a régebbi ügyfelek nem várt módon végzik a hitelesítést. A probléma nem vonatkozik a nagy Office-alkalmazásokra, például a régebbi Office-ügyfelekre.

Akár 24 óráig is eltarthat, amíg a szabályzat érvénybe lép.

A **többi ügyfél** feltételéhez kiválaszthatja az összes elérhető engedélyezési vezérlőt; a végfelhasználói élmény azonban mindig ugyanaz a letiltott hozzáférés.

Ha letiltja az örökölt hitelesítést a **többi ügyfél** feltételének használatával, beállíthatja az eszköz platformját és a hely feltételeit is. Ha például csak a mobileszközök örökölt hitelesítését szeretné letiltani, az **eszköz platformok** feltételét a következő lehetőség kiválasztásával állíthatja be:

- Android
- iOS
- Windows Phone

![A házirend konfigurációja nem támogatott](./media/block-legacy-authentication/06.png)

## <a name="next-steps"></a>További lépések

- [A hatás meghatározása a feltételes hozzáférésről szóló jelentés módban](howto-conditional-access-report-only.md)
- Ha még nem ismeri a feltételes hozzáférési szabályzatok konfigurálását, tekintse meg a többtényezős hitelesítés [megkövetelése adott alkalmazásokhoz Azure Active Directory feltételes hozzáférés](app-based-mfa.md) egy példához című témakört.
- A modern hitelesítés támogatásával kapcsolatos további információkért lásd: [Hogyan működik a modern hitelesítés az office 2013 és az office 2016 ügyfélalkalmazások számára](/office365/enterprise/modern-auth-for-office-2013-and-2016) . 
