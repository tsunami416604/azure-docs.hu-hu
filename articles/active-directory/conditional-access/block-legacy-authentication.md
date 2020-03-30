---
title: Örökölt hitelesítés blokkolása – Azure Active Directory
description: Ismerje meg, hogyan javíthatja a biztonsági testtartást az azure-beli AD feltételes hozzáférés használatával történő örökölt hitelesítés blokkolásával.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76dd07a59a9fa7c0d6231a766ff4090c11f9f5bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331916"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Útmutató: Az örökölt hitelesítés blokkolása az Azure AD-n feltételes hozzáféréssel   

Annak érdekében, hogy a felhasználók könnyen hozzáférhessenek a felhőalapú alkalmazásokhoz, az Azure Active Directory (Azure AD) a hitelesítési protokollok széles skáláját támogatja, beleértve az örökölt hitelesítést is. A régebbi protokollok azonban nem támogatják a többtényezős hitelesítést (MFA). MFA számos környezetben gyakori követelmény, hogy foglalkozzon a személyazonosság-lopás. 

Alex Weinert, a Microsoft identitásbiztonságért és -biztonságért vezető igazgatója [New tools to block legacy authentication in your organization](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302#) 2020.

> Ahhoz, hogy az MFA hatékony legyen, le kell tiltania az örökölt hitelesítést is. Ennek az az oka, hogy az olyan régebbi hitelesítési protokollok, mint a POP, az SMTP, az IMAP és a MAPI nem tudják érvényesíteni az MFA-t, így előnyben részesítik a szervezet támadó ellenfelei számára a belépési pontokat...
> 
>... Az Azure Active Directory (Azure AD) forgalom elemzéséből származó örökölt hitelesítésszámai élesek:
> 
> - A jelszószórásos támadások több mint 99 százaléka örökölt hitelesítési protokollokat használ
> - A hitelesítő adatok kitömési támadásainak több mint 97 százaléka örökölt hitelesítést használ
> - Az Azure AD-fiókok olyan szervezeteknél, amelyek letiltották az örökölt hitelesítést, 67 százalékkal kevesebb kompromisszumot élveznek, mint azok, ahol az örökölt hitelesítés engedélyezve van
>

Ha a környezet készen áll az örökölt hitelesítés blokkolására a bérlő védelmének javítása érdekében, ezt a célt a feltételes hozzáféréssel valósíthatja meg. Ez a cikk bemutatja, hogyan konfigurálhatja a feltételes hozzáférési házirendeket, amelyek blokkolják az örökölt hitelesítést a bérlő számára.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy ismeri a következőket: 

- Az Azure AD feltételes hozzáférés [alapvető fogalmai](overview.md) 
- A feltételes hozzáférési [szabályzatok](best-practices.md) azure portalon történő konfigurálásának ajánlott eljárásai

## <a name="scenario-description"></a>Forgatókönyv leírása

Az Azure AD támogatja a legszélesebb körben használt hitelesítési és engedélyezési protokollok, beleértve az örökölt hitelesítést. Az örökölt hitelesítés olyan protokollokra utal, amelyek alapszintű hitelesítést használnak. Ezek a protokollok általában nem kényszeríthetik ki a második tényezős hitelesítés bármely típusát. Példák az örökölt hitelesítésen alapuló alkalmazásokra:

- Régebbi Microsoft Office-alkalmazások
- Olyan alkalmazások, amelyek olyan levelezési protokollokat használnak, mint a POP, az IMAP és az SMTP

Az egyfaktoros hitelesítés (például a felhasználónév és a jelszó) manapság nem elegendő. Jelszó van rossz mint ők könnyű -hoz hisz és mi ( emberi) van rossz -on kiválasztás jó jelszó. A jelszavak számos támadással is ki vannak téve, mint például az adathalászat és a jelszóspray. Az egyik legegyszerűbb dolog, amit tehetünk, hogy megvédje a jelszó fenyegetések végrehajtása MFA. Az MFA,még akkor is, ha a támadó kap birtokában a felhasználó jelszavát, a jelszó önmagában nem elegendő az adatok sikeres hitelesítéséhez és eléréséhez.

Hogyan akadályozhatja meg, hogy az örökölt hitelesítést használó alkalmazások hozzáférjenek a bérlő erőforrásaihoz? A javaslat az, hogy csak blokkolja őket egy feltételes hozzáférési szabályzattal. Szükség esetén csak bizonyos felhasználók nak és adott hálózati helyeknek engedélyezi az örökölt hitelesítésen alapuló alkalmazások használatát.

A feltételes hozzáférési házirendek az első tényezős hitelesítés befejezése után lépnek érvénybe. Ezért a feltételes hozzáférés nem célja, mint az első vonalvédelmi forgatókönyvek, például a szolgáltatásmegtagadási (DoS) támadások, de használhatja az ilyen események (például a bejelentkezési kockázati szint, a kérelem helye, és így tovább) a hozzáférés meghatározásához.

## <a name="implementation"></a>Megvalósítás

Ez a szakasz bemutatja, hogyan konfigurálhat feltételes hozzáférési házirendet az örökölt hitelesítés blokkolására. 

### <a name="legacy-authentication-protocols"></a>Örökölt hitelesítési protokollok

A következő beállítások örökölt hitelesítési protokollnak minősülnek

- Hitelesített SMTP – A POP és az IMAP-ügyfél által használt e-mail üzenetek küldésére.
- Automatikus észlelés – Az Outlook és az EAS-ügyfelek segítségével kereshet postaládákat az Exchange Online-ban, és csatlakozhat azokhoz.
- Exchange Online PowerShell – Az Exchange Online-hoz távoli PowerShell használatával való csatlakozáshoz használható. Ha letiltja az Exchange Online PowerShell alapszintű hitelesítését, a csatlakozáshoz az Exchange Online PowerShell-modult kell használnia. További információt a [Csatlakozás az Exchange Online PowerShellhez többtényezős hitelesítéssel című](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)témakörben talál.
- Exchange Web Services (EWS) – Az Outlook, a Mac Outlook és a külső alkalmazások által használt programozási felület.
- IMAP4 - Az IMAP levelezőügyfelei használják.
- MAPI HTTP-n keresztül (MAPI/HTTP) – Az Outlook 2010 és újabb verziói használják.
- Kapcsolat nélküli címjegyzék (OAB) – Az Outlook által letöltött és használt címlista-gyűjtemények másolata.
- Outlook Anywhere (HTTP-n keresztüli RPC) – Az Outlook 2016-ban és korábban használt.
- Outlook-szolgáltatás – A Windows 10 Posta és Naptár alkalmazása használja.
- POP3 - A POP e-mail ügyfelek használják.
- Jelentéskészítő webszolgáltatások – jelentésadatok beolvasására szolgál az Exchange Online-ban.
- Egyéb ügyfelek – Az örökölt hitelesítést használóként azonosított egyéb protokollok.

Ezekről a hitelesítési protokollokról és szolgáltatásokról további információt [a Bejelentkezési tevékenységjelentések az Azure Active Directory portálon](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities)című témakörben talál.

### <a name="identify-legacy-authentication-use"></a>Örökölt hitelesítési használat azonosítása

Mielőtt letiltaná az örökölt hitelesítést a címtárban, először meg kell értenie, hogy a felhasználók rendelkeznek-e olyan alkalmazásokkal, amelyek örökölt hitelesítést használnak, és hogy ez hogyan befolyásolja a teljes könyvtárat. Az Azure AD bejelentkezési naplók segítségével megismerheti, ha örökölt hitelesítést használ.

1. Nyissa meg az **Azure** > Active**Directory** > bejelentkezések azure**portalját.**
1. Adja hozzá az Ügyfélalkalmazás oszlopot, ha az nem jelenik meg az **Oszlopok** > **ügyfélalkalmazásra**kattintva.
1. **Adja hozzá a szűrőket,** > **Client App** > jelölje ki az összes örökölt hitelesítési protokollt, majd kattintson az **Alkalmaz gombra.**

A szűrés csak az örökölt hitelesítési protokollok által végrehajtott bejelentkezési kísérleteket jeleníti meg. Az egyes bejelentkezési próbálkozásokra kattintva további részleteket jeleníthet meg. Az **Alapinformáció** lap **Ügyfélalkalmazás** mezője jelzi, hogy melyik örökölt hitelesítési protokollt használták.

Ezek a naplók jelzik, hogy mely felhasználók továbbra is az örökölt hitelesítéstől függően vannak, és mely alkalmazások használnak örökölt protokollokat a hitelesítési kérelmek hez. Azoka felhasználók számára, akik nem jelennek meg ezekben a naplókban, és megerősítést nyer, hogy nem használnak örökölt hitelesítést, csak ezekre a felhasználókra alkalmazzanak feltételes hozzáférési házirendet.

### <a name="block-legacy-authentication"></a>Régi hitelesítési folyamat letiltása 

Feltételes hozzáférési házirendben olyan feltételt állíthat be, amely az erőforrások eléréséhez használt ügyfélalkalmazásokhoz van kötve. Az ügyfélalkalmazások feltétele lehetővé teszi, hogy a hatókört az örökölt hitelesítést használó alkalmazásokra szűkítse a **Mobilalkalmazások és asztali ügyfelek**Egyéb **ügyfelei** nek kiválasztásával.

![Más ügyfelek](./media/block-legacy-authentication/01.png)

Ha le szeretné tiltani a hozzáférést ezekhez az alkalmazásokhoz, válassza a **Hozzáférés blokkolása**lehetőséget.

![Fájlhozzáférés](./media/block-legacy-authentication/02.png)

### <a name="select-users-and-cloud-apps"></a>Felhasználók és felhőalapú alkalmazások kiválasztása

Ha le szeretné tiltani a szervezet örökölt hitelesítését, valószínűleg úgy gondolja, hogy ezt a következő lehetőség kiválasztásával érheti el:

- Minden felhasználó
- Minden felhőalapú alkalmazás
- Fájlhozzáférés

![Hozzárendelések](./media/block-legacy-authentication/03.png)

Az Azure rendelkezik egy biztonsági funkcióval, amely megakadályozza, hogy ilyen szabályzatot hozzon létre, mert ez a konfiguráció sérti a feltételes hozzáférési [szabályzatok ajánlott eljárásait.](best-practices.md)
 
![A házirend-konfiguráció nem támogatott](./media/block-legacy-authentication/04.png)

A biztonsági funkció azért szükséges, mert blokkolja az összes felhasználó és az *összes felhőalapú alkalmazások* rendelkezik azzal a potenciállal, hogy blokkolja a teljes szervezet bejelentkezésa a bérlőbe. Legalább egy felhasználót ki kell zárnia a minimális ajánlott eljárás követelményének teljesítéséhez. A címtárszerepkört is kizárhatja.

![A házirend-konfiguráció nem támogatott](./media/block-legacy-authentication/05.png)

Ezt a biztonsági funkciót úgy elégítheti ki, hogy egy felhasználót kizár a szabályzatból. Ideális esetben meg kell határoznia néhány [vészelérési felügyeleti fiókok az Azure AD-ben,](../users-groups-roles/directory-emergency-access.md) és zárja ki őket a szabályzatból.

A [csak jelentés módban,](concept-conditional-access-report-only.md) ha engedélyezi a szabályzat ot, hogy blokkolja az örökölt hitelesítés tayourt a szervezet lehetőséget arra, hogy figyelje, milyen hatása lenne a házirend.

## <a name="policy-deployment"></a>Szabályzat érvénybe léptetése

Mielőtt a politikát termelésbe helyezné, gondoskodjon a következőkről:
 
- **Szolgáltatásfiókok** – Azonosítsa azokat a felhasználói fiókokat, amelyeket szolgáltatásfiókként vagy eszközként, például konferenciaterem-telefonok használnak. Győződjön meg arról, hogy ezek a fiókok erős jelszavakkal rendelkeznek, és adja hozzá őket egy kizárt csoporthoz.
- **Bejelentkezési jelentések** – Tekintse át a bejelentkezési jelentést, és keressen **más ügyfélforgalmat.** Azonosítsa a legnépszerűbb használatot, és vizsgálja meg, hogy miért van használatban. A forgalmat általában olyan régebbi Office-ügyfelek generálják, amelyek nem használnak modern hitelesítést, vagy néhány külső levelezőalkalmazást. Tervezze meg, hogy a használatot elmozdítja ezekből az alkalmazásokból, vagy ha a hatása alacsony, értesítse a felhasználókat, hogy már nem használhatják ezeket az alkalmazásokat.
 
További információ: [Hogyan kell telepíteni egy új házirendet?](best-practices.md#how-should-you-deploy-a-new-policy).

## <a name="what-you-should-know"></a>Alapismeretek

A hozzáférés **letiltása más ügyfelekkel** is blokkolja az Exchange Online PowerShell és a Dynamics 365 alapvető hitelesítési használatával.

**A házirend konfigurálása más ügyfelek számára** letiltja a teljes szervezetet bizonyos ügyfelektől, például az SPConnect-től. Ez a blokk azért történik, mert az idősebb ügyfelek nem várt módon hitelesítik magukat. A probléma nem vonatkozik a nagyobb Office-alkalmazásokra, például a régebbi Office-ügyfelekre.

A szabályzat hatályba lépése akár 24 órát is igénybe vehet.

Kiválaszthatja az összes rendelkezésre álló támogatási vezérlőt az **Egyéb ügyfelek** feltételhez; azonban a végfelhasználói élmény mindig ugyanaz - blokkolt hozzáférés.

Ha letiltja az örökölt hitelesítést az **Egyéb ügyfelek** feltétellel, beállíthatja az eszköz platformját és helyállapotát is. Ha például csak a mobileszközök örökölt hitelesítését szeretné blokkolni, a következő lehetőség kiválasztásával állítsa be az **eszköz platformok** feltételét:

- Android
- iOS
- Windows Phone

![A házirend-konfiguráció nem támogatott](./media/block-legacy-authentication/06.png)

## <a name="next-steps"></a>További lépések

- [Hatás meghatározása csak feltételes hozzáférésű jelentésmódhasználatával](howto-conditional-access-report-only.md)
- Ha még nem ismeri a feltételes hozzáférési szabályzatok konfigurálását, tekintse meg az [Azure Active Directory feltételes hozzáféréssel rendelkező alkalmazások hoz tartozó mfa-igény behajtása](app-based-mfa.md) című témakört.
- A modern hitelesítési támogatásról további információt a [Modern hitelesítés működése az Office 2013-hoz és az Office 2016-beli ügyfélalkalmazásokhoz című témakörben talál.](/office365/enterprise/modern-auth-for-office-2013-and-2016) 
