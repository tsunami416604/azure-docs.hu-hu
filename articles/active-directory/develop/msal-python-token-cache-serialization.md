---
title: Egyéni jogkivonat-gyorsítótár szerializálása (MSAL Python) | Azure
titleSuffix: Microsoft identity platform
description: További információ az MSAL python-hoz tokengyorsítótár szerializálásáról
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/13/2019
ms.author: rayluo
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 9c6edd0b3cfd6620f04553f9f6dfe89f1c7b7024
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536199"
---
# <a name="custom-token-cache-serialization-in-msal-for-python"></a>Egyéni tokengyorsítótár-szerializálás az MSAL python-ban

Az MSAL Pythonban az alkalmazásmunkamenet időtartama alatt megmaradó memórián belüli jogkivonat-gyorsítótár alapértelmezés szerint a [ClientApplication](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication)egy példányának létrehozásakor biztosít.

A tokengyorsítótár szerializálása, hogy az alkalmazás különböző munkamenetei elérhessék azt, nem "a dobozból" történik. Ennek az az oka, hogy az MSAL Python olyan alkalmazástípusokban használható, amelyek nem férnek hozzá a fájlrendszerhez – például a webalkalmazásokhoz. Ha egy MSAL Python-alkalmazásban állandó jogkivonat-gyorsítótárat szeretne, egyéni jogkivonat-gyorsítótár-szerializálást kell biztosítania.

A tokengyorsítótár szerializálására vonatkozó stratégiák attól függően változnak, hogy nyilvános ügyfélalkalmazást (Asztalt) vagy bizalmas ügyfélalkalmazást (webalkalmazás, web API vagy démonalkalmazás) ír.

## <a name="token-cache-for-a-public-client-application"></a>Egy nyilvános ügyfélalkalmazás tokengyorsítótára

A nyilvános ügyfélalkalmazások a felhasználó eszközén futnak, és egyetlen felhasználó jogkivonatait kezelik. Ebben az esetben a teljes gyorsítótárat szerializálhatja egy fájlba. Ne felejtsen el fájlzárolást biztosítani, ha az alkalmazás vagy egy másik alkalmazás egyidejűleg hozzáférhet a gyorsítótárhoz. Egy egyszerű példa arra, hogyan szerializálhatja a token cache egy fájl zárolás nélkül, lásd a példát a [SerializableTokenCache](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) osztály referencia dokumentáció.

## <a name="token-cache-for-a-web-app-confidential-client-application"></a>Egy webalkalmazás tokengyorsítótára (bizalmas ügyfélalkalmazás)

Webalkalmazások vagy webes API-k esetén használhatja a munkamenetet, a Redis-gyorsítótárat vagy egy adatbázist a jogkivonat-gyorsítótár tárolására. Felhasználónként (fiókonként) egy tokengyorsítótárnak kell lennie, ezért győződjön meg arról, hogy fiókonként szerializálja a token-gyorsítótárat.

## <a name="next-steps"></a>További lépések

Tekintse meg [az ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py#L64-L72) egy példát, hogyan használhatja a token cache egy Windows vagy Linux web alkalmazás vagy webes API.See ms-identity-python-webapp for a example of how tokent cache for a Windows or Linux Web app or web API. A példa egy webalkalmazás, amely meghívja a Microsoft Graph API-t.
