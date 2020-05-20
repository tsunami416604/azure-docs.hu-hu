---
title: Auth0 partneri témakörök Azure Event Grid
description: Események küldése a Auth0-ből az Azure-szolgáltatásokba Azure Event Grid használatával.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: babanisa
ms.openlocfilehash: f96aa4d75f85aea046981641f84fd2b2c3078bd1
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83691214"
---
# <a name="auth0-partner-topics"></a>Auth0-partneri témakörök
![A Auth0 logo ](./media/auth0-overview/auth0-logo.png) Auth0, az alkalmazás-építők számára készült Identity platform a fejlesztőket és a vállalatokat az alkalmazásai védelméhez szükséges építőelemeket biztosítja.

A Auth0-partneri témakör lehetővé teszi, hogy a Auth0's rendszer által kibocsátott eseményeket több feladat elvégzéséhez használja. A hitelesítés vagy a biztonsági és infrastrukturális feladatok automatizálása után a felhasználóknak értelmes módon kell foglalkoznia a felhasználókkal.

Az integráció lehetővé teszi, hogy magas szintű megbízhatósággal továbbítsa a Auth0-naplózási eseményeket az Azure-ba. Ott felhasználhatja az eseményeket kedvenc Azure-erőforrásaival. Ez az integráció lehetővé teszi az események megválaszolása, az elemzések, a biztonsági problémák figyelése és a más, hatékony adatfolyamatokkal való interakciót.

Az Auth0 és az Azure-t használó szervezetek esetében ez az integráció lehetővé teszi, hogy zökkenőmentesen integrálja az adatait a teljes verembe. 
 
## <a name="available-event-types"></a>Elérhető események típusai
Az elérhető Auth0-események teljes listája és azok leírása [itt](https://auth0.com/docs/logs/references/log-event-type-codes)érhető el.

## <a name="use-cases"></a>Használati esetek

### <a name="engage-with-your-users"></a>Felhasználói részvétel
Az erős felhasználói élmény biztosítása kritikus fontosságú a forgalom csökkentése és a felhasználók megtartása érdekében. A Auth0-események használatával Azure Functions és Logic Apps segítségével testre szabható alkalmazási élményeket biztosíthat. 

### <a name="understand-user-behavior"></a>A felhasználó viselkedésének megértése
Ismerje meg, hogy mikor férhetnek hozzá a felhasználók a termékhez, hol vannak bejelentkezve, és milyen eszközöket használnak. A fenti jelek nyomon követésével megismerheti a leginkább fontos terméktartományoknak. Ezek a jelek segítenek eldönteni, hogy mely böngészők és eszközök támogatják a támogatást, a nyelveket, hogy az alkalmazást a alkalmazásban lokalizálják, és a maximális forgalmi idő szerint. 

### <a name="manage-user-data"></a>Felhasználói adatkezelés
A felhasználói műveletek megőrzése és naplózása létfontosságú a biztonság fenntartásához és az iparági szabályozások követéséhez. A felhasználói adatok szerkesztésének, eltávolításának vagy exportálásának lehetősége egyre fontosabbá válik az adatvédelmi törvények, például az Európai Unió Általános adatvédelmi rendelet (GDPR) követése érdekében.

### <a name="secure-your-application"></a>Az alkalmazás biztonságossá tétele
Az elosztott rendszerek védelme fontos a biztonsági monitorozás és az incidensek reagálási eljárásainak kombinálásával. Ezért fontos, hogy az összes adathalmazt egy helyen tárolja, és figyelje a teljes veremet. 

## <a name="next-steps"></a>További lépések

- [A partneri témakörök áttekintése](partner-topics-overview.md)
- [Az Auth0-partneri témakör használata](auth0-how-to.md)
- [A Auth0 dokumentációja](https://auth0.com/docs/logs/streams/azure-eventgrid)
- [Legyen Event Grid partner](partner-onboarding-overview.md)

