---
title: Alkalmazás megjelölése közzétevőként ellenőrzöttként – Microsoft Identity platform | Azure
description: Leírja, hogyan kell megjelölni egy alkalmazást közzétevőként. Ha az alkalmazás közzétevőként van megjelölve, az azt jelenti, hogy a közzétevő ellenőrizte az identitását egy olyan Microsoft Partner Network-fiókkal, amely befejezte az ellenőrzési folyamatot, és társította ezt az MPN-fiókot az alkalmazás regisztrálásával.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: b3c3bed20b5fd60b9323dada617fb1302efc41d2
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "90006944"
---
# <a name="mark-your-app-as-publisher-verified"></a>Alkalmazás ellenőrzött közzétevővel rendelkezőként való megjelölése

Ha az alkalmazás közzétevőként van megjelölve, az azt jelenti, hogy a közzétevő a Microsoft Partner Network (MPN) fiók használatával ellenőrizte az identitását, és az alkalmazás regisztrálásával társította ezt az MPN-fiókot. Ez a cikk a [közzétevői ellenőrzés (előzetes verzió)](publisher-verification-overview.md) folyamatának befejezését ismerteti.

## <a name="quickstart"></a>Első lépések
Ha már regisztrált a Microsoft Partner Network (MPN) szolgáltatásban, és teljesítette az [előfeltételeket](publisher-verification-overview.md#requirements), azonnal megkezdheti az első lépéseket: 

1. Navigáljon az [alkalmazás regisztrációs portálra](https://aka.ms/PublisherVerificationPreview).

1. Válasszon ki egy alkalmazást, és kattintson a **branding**elemre. 

1. Kattintson az **MPN-azonosító hozzáadása elemre a közzétevő ellenőrzéséhez** és a felsorolt követelmények áttekintéséhez.

1. Adja meg az MPN-azonosítót, és kattintson az **ellenőrzés és mentés**gombra.

Az egyes előnyökkel, követelményekkel és gyakori kérdésekkel kapcsolatos további információkért tekintse meg az [áttekintést](publisher-verification-overview.md).


## <a name="mark-your-app-as-publisher-verified"></a>Alkalmazás ellenőrzött közzétevővel rendelkezőként való megjelölése
Győződjön meg arról, hogy teljesítette az [előfeltételeket](publisher-verification-overview.md#requirements), majd kövesse az alábbi lépéseket az alkalmazás (ok) megjelöléséhez a közzétevő által ellenőrzöttként.  

1. Győződjön meg arról, hogy olyan szervezeti (Azure AD-) fiókkal van bejelentkezve, amely jogosult a Közzétevőként megjelölni kívánt alkalmazás (ok) módosítására, valamint a fiókpartner MPN-fiókjára. 

    - Az Azure AD-ben a felhasználónak vagy az alkalmazás tulajdonosának kell lennie, vagy a következő szerepkörök egyikével kell rendelkeznie: Application admin, Cloud Application admin, globális rendszergazda. 

    - A partner Centerben ennek a felhasználónak a következő szerepkörökkel kell rendelkeznie: MPN-rendszergazda, fiókok rendszergazdája vagy globális rendszergazda (ez az Azure AD-ben elsajátított megosztott szerepkör). 

1. Navigáljon az alkalmazás regisztrációs portálján:  

1. Kattintson arra az alkalmazásra, amelyet Közzétevőként szeretne megjelölni, és nyissa meg a branding (védjegyezés) panelt. 

1. Győződjön meg arról, hogy az alkalmazás közzétevői tartománya megfelelően van beállítva. Ennek a tartománynak a következőnek kell lennie: 

    - Az Azure AD-bérlőt DNS-hitelesítésű egyéni tartományként adja hozzá,  

    - Az MPN-fiók ellenőrzési folyamata során használt e-mail-cím tartományának felel meg. 

1. Kattintson az **MPN-azonosító hozzáadása elemre a kiadó ellenőrzéséhez** az oldal alján. 

1. Adja meg az **MPN-azonosítót**. Ennek az MPN-AZONOSÍTÓnak a következőnek kell lennie: 

    - Egy érvényes Microsoft Partner Network-fiók, amely befejezte az ellenőrzési folyamatot.  

    - A szervezethez tartozó partner globális fiók (PGA). 

1. Kattintson **az ellenőrzés és mentés**gombra. 

1. Várjon, amíg a rendszer feldolgozza a kérést, ez eltarthat néhány percig. 

1. Ha az ellenőrzés sikeres volt, a közzétevő ellenőrzési ablaka be lesz zárva, és visszatér a branding (védjegyezés) panelre. Az ellenőrzött **közzétevők megjelenítendő neve**mellett egy kékan ellenőrzött jelvény jelenik meg. 

1. Azok a felhasználók, akik megkérik az alkalmazás belefoglalását, azonnal megkezdik a jelvény megtekintését, miután sikeresen elvégezte a folyamatot, bár ez eltarthat egy ideig, amíg a rendszer replikálja. 

1. Tesztelje ezt a funkciót úgy, hogy bejelentkezik az alkalmazásba, és gondoskodik arról, hogy az ellenőrzött jelvény a beleegyezési képernyőn jelenik meg. Ha olyan felhasználóként jelentkezett be, aki már kapott beleegyezett az alkalmazásba, a *kérések* lekérdezése paraméterrel kényszerítheti a beleegyezés kérését. 

1. Szükség szerint ismételje meg a folyamatot minden olyan alkalmazáshoz, amelyhez meg szeretné jeleníteni a jelvényt. A Microsoft Graph használatával a lehető leggyorsabban elvégezhető tömegesen, a PowerShell-parancsmagok pedig hamarosan elérhetők lesznek. További információért tekintse meg a [Microsoft API Graph-hívások készítése](troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls) című témakört. 

Készen is van. Tudassa velünk, hogy van-e visszajelzése a folyamatról, az eredményekről vagy a szolgáltatásról. 

## <a name="next-steps"></a>Következő lépések
Ha problémákat tapasztal, olvassa el a [hibaelhárítási információkat](troubleshoot-publisher-verification.md).
