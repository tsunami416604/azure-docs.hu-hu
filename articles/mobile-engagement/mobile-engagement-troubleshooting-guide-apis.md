---
title: Az Azure Mobile Engagement hibaelhárítási útmutatója - API-k
description: Hibaelhárítás az Azure Mobile Engagement - API-k útmutatók
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 3efc8a52-2b74-4917-b887-815ae8277474
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/04/2016
ms.author: piyushjo
ms.openlocfilehash: 8d51aaf5196eabe18d92b34d97e98f9f665f2527
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="troubleshooting-guide-for-api-issues"></a>Az API-problémák hibaelhárítási útmutató
> [!IMPORTANT]
> Az Azure Mobile Engagement támogatása 2018. március 31-én megszűnik. Az oldal nem sokkal ezután törlésre kerül.
> 

A következőkben lehetséges problémák merülhetnek fel a rendszergazdák hogyan működnek együtt az Azure Mobile Engagement az API-k használatával.

## <a name="syntax-issues"></a>Szintaxis problémák
### <a name="issue"></a>Probléma
* Szintaktikai hibákat a API-t (vagy váratlan viselkedést) használatával.

### <a name="causes"></a>Okok
* Szintaxis problémák:
  * Győződjön meg arról, hogy ellenőrizze a szintaxist, az adott API segítségével győződjön meg arról, hogy a beállítás lesz elérhető.
  * Általános hiba az API-használati hathat Reach API és a leküldéses API-val (a legtöbb feladatot kell elvégezni helyett a leküldéses API Reach API-val). 
  * SDK-integrációval és API-használati egy másik gyakori probléma, hogy megzavarják a SDK és az API-kulcs.
  * Parancsfájlok, amelyek kapcsolódnak az API-kat kell adatküldés legalább 10 percenként, vagy a kapcsolat időtúllépés (különösen a figyelő API parancsfájlok figyeli az adatokat a közös) lesz. Időtúllépések megelőzése érdekében a parancsfájl egy XMPP ping 10 percenként küld a munkamenet életben a kiszolgálóval rendelkezik.

### <a name="see-also"></a>Lásd még
* [API-JÁNAK dokumentációja][Link 4]
* [XMPP protokoll adatai](http://xmpp.org/extensions/xep-0199.html)

## <a name="unable-to-use-the-api-to-perform-the-same-action-available-in-the-azure-mobile-engagement-ui"></a>Nem sikerült az API-t használja ugyanazt a műveletet az Azure Mobile Engagement felhasználói felület érhető el
### <a name="issue"></a>Probléma
* Egy műveletet, amely az Azure Mobile Engagement felhasználói felület működik a kapcsolódó Azure Mobile Engagement API nem működik.

### <a name="causes"></a>Okok
* Erősítse meg, hogy elvégezhető ugyanaz a művelet az Azure Mobile Engagement felhasználói felület jeleníti meg, hogy helyesen integrálva van a szolgáltatás az Azure Mobile Engagement az SDK-t.

### <a name="see-also"></a>Lásd még
* [Felhasználói felületének dokumentációja][Link 1]

## <a name="error-messages"></a>Hibaüzenetek
### <a name="issue"></a>Probléma
* A hibakódok a futási időben, vagy a naplókban megjelenő API használatával.

### <a name="causes"></a>Okok
* Ez egy összetett lista közös API állapot kódok számok hivatkozást és a előzetes hibaelhárítás céljából:
  
        200        Success.
        200        Account updated: device registered, associated, updated, or removed from the current account.
        200        Returns a list of projects as a JSON object or an authentication token generated and returned in the response’s body.
        201        Account created.
        400        Invalid parameter or validation exception (check payload for details). The parameters provided to the API or service are invalid. In this case, the HTTP response will embed more details. Make sure to test for the MIME type of the response as the payload can either be plain text or a JSON object.
        401        Authentication error. No user is currently authenticated or connected (check the AppID and SDK key).
        402        Billing lock. The application is either off its quotas or is currently in a bad billing state.
        403        The application is not enabled or the specific API is disabled for this application.
        403        Unauthorized access to the project or application, invalid access key (the key must match the one provided when created).
        403        Campaign specific errors: campaign must be finished (or has already been activated), the suspend action can only be performed on an scheduled campaign, cannot finish a campaign that is not currently “in progress”, campaign must be “in progress” and the campaign’s property named, manual Push must be set to true.
        403        The email address is already associated to another account (a super user for instance). No authentication token will be generated.
        404        Application, device, campaign, or project identifier not found.
        404        Query parameter is invalid JSON or has a field with an unexpected value.
        404        The email address is not associated with an account. Please create or update the account first.
        405        Invalid HTTP method (GET, POST, etc.) or trying to edit a read only segment (i.e. add or update or delete a criterion). A segment becomes read only after it has been computed for the first time.
        409        Name already associated to a different device ID or campaign.
        413        Too many device identifiers (current limit is 1,000), POST URL encoded entity is over 2MB, or the period is too large to be displayed (the server didn’t retrieve the analytics because the user request is for a period that is too large).
        503        Analytics not available yet (the requested information is not computed yet for an application).
        504        The server was not able to handle your request in a reasonable time (if you make multiple calls to an API very quickly, try to make one call at a time and spread the calls out over time).

### <a name="see-also"></a>Lásd még
* [Részletes hibaüzenetek az egyes adott API - API dokumentációját][Link 4]

## <a name="silent-failures"></a>Csendes hibák
### <a name="issue"></a>Probléma
* Nincs hibaüzenet jelenik meg a futási időben, vagy a naplókban az API-művelet sikertelen lesz.

### <a name="causes"></a>Okok
* Sok elem a rendszer letiltja az Azure Mobile Engagement felhasználói felületén, ha azok nincsenek megfelelően, integrált, de fog csendes az API-n, a, ne felejtse el tesztelni ugyanazokat a funkció a megjelenítéséhez, ha működik a felhasználói Felületről.
* Az Azure Mobile Engagement és az Azure Mobile Engagement próbál használni, számos további beállításokat kell külön-külön integrálni az alkalmazás az SDK-val külön lépéseket, mielőtt azokat.

### <a name="see-also"></a>Lásd még
* [Hibaelhárítási útmutató - SDK][Link 25]

<!--Link references-->
[Link 1]: mobile-engagement-user-interface-home.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md

