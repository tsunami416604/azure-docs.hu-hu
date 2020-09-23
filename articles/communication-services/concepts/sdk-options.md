---
title: Ügyféloldali kódtárak és REST API-k az Azure kommunikációs szolgáltatásokhoz
titleSuffix: An Azure Communication Services concept document
description: További információ az Azure kommunikációs szolgáltatások ügyféloldali könyvtárairól és a REST API-król.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/18/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: f39aa76e4bd2ce3d298e555f56b09d0218ef0862
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935339"
---
# <a name="client-libraries-and-rest-apis"></a>Ügyféloldali kódtárak és REST API-k

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Az Azure kommunikációs szolgáltatások funkcióit koncepcionálisan hat területre szervezték. Egyes területek teljesen nyílt forráskódú ügyféloldali kódtárakkal rendelkeznek. A hívó ügyféloldali függvénytár saját hálózati adaptereket használ, és jelenleg zárt forrású, a csevegési függvénytár pedig zárt forráskódú függőséget tartalmaz. Az SDK-ra és a mintákra mutató hivatkozásokat az [Azure kommunikációs szolgáltatások GitHub](https://github.com/Azure/communication)-tárházában őrzi meg.

## <a name="client-libraries"></a>Ügyfélkódtárak

| Szerelvény               | Protokollok             |Nyitott és lezárt forrás| Névterek                          | Képességek                                                      |
| ---------------------- | --------------------- | ---|-------------------------- | --------------------------------------------------------------------------- |
| Azure Resource Manager | REST | Megnyitás            | Azure. erőforráskezelő. Communication | Kommunikációs szolgáltatások erőforrásainak kiépítése és kezelése             |
| Közös                 | REST | Megnyitás               | Azure. Communication. Common          | Alaptípusokat biztosít más ügyféloldali kódtárak számára |
| Felügyelet         | REST |                | Azure. Communication. Administration  | A felhasználók, a hozzáférési tokenek és a telefonszámok kezelése, a szabványoknak megfelelő kábítás és a kiszolgálók kiosztása |
| Csevegés                   | További tulajdonosi jelzéssel | Megnyitás zárt forrású jelző csomaggal    | Azure. Communication. chat            | Valós idejű szöveg-alapú csevegés hozzáadása az alkalmazásokhoz  |
| SMS                    | REST | Megnyitás              | Azure. Communication. SMS             | SMS-üzenetek küldése és fogadása |
| Hívó                | Saját szállítás | Zárt |Azure. Communication. Calling         | A hang-, videó-, képernyő-megosztási és egyéb valós idejű adatkommunikációs képességek kihasználása          |

### <a name="client-library-language-support"></a>Az ügyféloldali kódtár nyelvi támogatása

Az egyes ügyféloldali függvénytár-csomagok elérhetőségi útmutatói és Ütemtervei alább láthatók. Az [Azure-ütemterv](https://azure.microsoft.com/updates/) további információkat tartalmaz a közelgő funkciókról.

| Terület           | JavaScript | .NET | Python | Java | Swift vagy obj-C | Java (Android) | Egyéb                          |
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | ✔️         | ✔️    | ✔️      | -    | -              | *Még nem támogatott*  | A GO és az Azure CLI *még nem támogatott* |
| Közös         | ✔️         | ✔️    | -      | ✔️   | ✔️            | ✔️             | -                              |
| Felügyelet | ✔️         | ✔️    | ✔️      | ✔️   | -              | -              | parancssori felület                            |
| Csevegés           | ✔️         | ✔️    | ✔️      | ✔️   | *Még nem támogatott*  | *Még nem támogatott*  | -                              |
| SMS            | ✔️         | ✔️    | ✔️      | ✔️   | -              | -              | -                              |
| Hívó        | ✔️         | -      | -      | -     | (Obj-C) ✔️     | ✔️            | -                              |

### <a name="client-library-public-repository-support"></a>Az ügyféloldali kódtár nyilvános tárházának támogatása

A kommunikációs szolgáltatások számos nyilvános tárházban teszik közzé a létrehozott kódtárakat.

| Nyelv       | Optimalizálva...                       | Csomagolás |
| -------------- | ------------------------------------ | --------- |
| .NET           | Platformfüggetlen                       | NuGet     |
| Python         | Windows & Linux-kiszolgálók              | PyPI      |
| Java (J2EE)    | JVM Windows vagy Linux rendszerű kiszolgálókon      | Maven     |
| Java (Android) | Android-ügyfélalkalmazások          | Maven     |
| JavaScript     | Böngészőalapú ügyfélalkalmazások és csomópont | NPM       |

## <a name="rest-apis"></a>REST API-k

A kommunikációs szolgáltatások API-jai a [docs.microsoft.com](https://docs.microsoft.com/rest/api/azure/)egyéb Azure REST API-jai mellett is dokumentálva vannak. A dokumentációból megtudhatja, hogyan strukturálhatja HTTP-üzeneteit, és útmutatást nyújt a Poster használatához. Ez a dokumentáció a [githubon](https://github.com/Azure/azure-rest-api-specs)is elérhető a hencegés formátumában.

## <a name="additional-support-details"></a>További támogatási információk

### <a name="ios-and-android-support-details"></a>az iOS és az Android támogatásának részletei

- A kommunikációs szolgáltatások iOS-ügyféloldali kódtárai az iOS-es és a Xcode 11 + verzióját célozzák meg.
- Android Java ügyféloldali kódtárak cél Android API Level 21 + és Android Studio 4.0 +

### <a name="net-support-details"></a>.NET-támogatás részletei

A Calling szolgáltatás kivételével a kommunikációs szolgáltatások a .NET Standard 2,0-es verziójának felelnek meg, amely támogatja az alább felsorolt platformokat.

Támogatás a .NET-keretrendszer 4.6.1-es használatával
- Windows 10, 8,1, 8 és 7
- Windows Server 2012 R2, 2012 és 2008 R2 SP1

Támogatás a .NET Core 2,0-on keresztül:
- Windows 10 (1607 +), 7 SP1 +, 8,1
- Windows Server 2008 R2 SP1 +
- Max OS X 10.12 +
- Linux több verzió/disztribúció
- UWP 10.0.16299 (RS3), szeptember 2017
- Unity 2018,1
- Monó 5,4
- Xamarin iOS 10,14
- Xamarin Mac 3,8

## <a name="api-stability-expectations"></a>API-stabilitási elvárások 

> [!IMPORTANT]
> Ez a szakasz útmutatást nyújt a REST API-kkal és az ügyfél-kódtárak **stabil**jelöléssel. A előzetes kiadást, előzetes verziót vagy bétaverziót jelölt API-k **értesítés nélkül**módosíthatók vagy elavulttá válhatnak. Az Azure kommunikációs szolgáltatás jelenleg **nyilvános előzetes**verzióban érhető el, az API-k pedig ilyenként vannak megjelölve.

A jövőben előfordulhat, hogy kivonják a kommunikációs szolgáltatások ügyféloldali kódtárainak verzióit, és a REST API-k és a kiadott ügyféloldali kódtárak feltörési változásait is bevezetjük. Az Azure kommunikációs szolgáltatásai *általában* két támogatási szabályzatot követnek a szolgáltatási verziók kivonásához:

- A kommunikációs szolgáltatások felületének változása miatt legalább három évig értesítést kap a kód módosításának szükségességéről. Az összes dokumentált REST API és ügyféloldali függvénytár API általában legalább három éves figyelmeztetést biztosít a felületek leszerelése előtt.
- Legalább egy évig értesítést kap, mielőtt frissítenie kell az ügyféloldali függvénytár-szerelvényeket a legújabb alverzióra. Ezeknek a szükséges frissítéseknek semmilyen programkód-módosításra sincs szükségük, mert ugyanazon a főverzión vannak. Ez különösen igaz a hívó és a csevegési kódtárak esetében, amelyek olyan valós idejű összetevőkkel rendelkeznek, amelyek gyakran igényelnek biztonsági és teljesítménybeli frissítéseket. Javasoljuk, hogy frissítse a kommunikációs szolgáltatások ügyféloldali kódtárait.

### <a name="api-and-client-library-decommissioning-examples"></a>Példák az API és az ügyféloldali kódtár leszerelésére

**Integrálta az SMS-REST API V24 verzióját az alkalmazásba. Az Azure kommunikációs kiadásai v25.**

Az API-k futtatása előtt 3 év figyelmeztetést kap, és a v25-ra való frissítésre kényszerülnek. A frissítéshez szükség lehet a kód módosítására.

**Integrálta a hívó ügyféloldali kódtár v 2.02 verzióját az alkalmazásba. Az Azure kommunikációs kiadásai: v 2.05.**

Előfordulhat, hogy frissítenie kell a Called Client Library v 2.05 verziójára a v 2.05 kiadásának 12 hónapon belül. Ez az összetevő egyszerű helyettesítése a kód módosítása nélkül, mert a v 2.05 a v2 főverzióban van, és nem változik a változások megszakítása.

## <a name="next-steps"></a>Következő lépések

További információkért tekintse meg a következő ügyféloldali függvénytár-áttekintéseket:

- [Az ügyféloldali kódtár meghívása – áttekintés](../concepts/voice-video-calling/calling-sdk-features.md)
- [Csevegő ügyféloldali kódtár – áttekintés](../concepts/chat/sdk-features.md)
- [SMS ügyféloldali kódtár – áttekintés](../concepts/telephony-sms/sdk-features.md)

Ismerkedés az Azure kommunikációs szolgáltatásokkal:

- [Azure kommunikációs erőforrások létrehozása](../quickstarts/create-communication-resource.md)
- [Felhasználói hozzáférési tokenek](../quickstarts/access-tokens.md) előállítása
