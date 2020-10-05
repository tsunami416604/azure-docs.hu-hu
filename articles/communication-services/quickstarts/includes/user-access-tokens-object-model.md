---
title: fájlbefoglalás
description: fájlbefoglalás
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: c0ba8e76e069bf9dc1c96aecdc670699c32b3c96
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "90947218"
---
## <a name="object-model"></a>Objektummodell

A felhasználói hozzáférési tokenek lehetővé teszik az alkalmazás felhasználói számára, hogy közvetlenül kapcsolódjanak az Azure kommunikációs szolgáltatások erőforrásaihoz. Ezeket a tokeneket létrehozhatja a kiszolgálón, visszaküldheti az ügyfélalkalmazás számára, majd a kommunikációs szolgáltatások ügyféloldali kódtárainak inicializálásához használhatja őket. Ennek eléréséhez hozzon létre egy megbízható szolgáltatási végpontot, amely képes hitelesíteni a felhasználókat, és kiadni felhasználói hozzáférési jogkivonatokat. A szolgáltatásnak állandó leképezést kell fenntartania az alkalmazás felhasználói identitásai és az Azure kommunikációs szolgáltatások felhasználói között.

A CommunicationIdentityClient osztály a felhasználói és hozzáférési jogkivonat összes kapcsolódó funkcióját biztosítja. Létrehoz egy kapcsolati karakterláncot, majd felhasználja a felhasználók kezelésére és a hozzáférési jogkivonatok kibocsátására.

> [!WARNING]
> A tokenek bizalmas adatok, mivel hozzáférést biztosítanak a felhasználók erőforrásaihoz. Ezért fontos, hogy a jogkivonatok biztonsága illetéktelenek legyenek. Létre kell hoznia egy megbízható szolgáltatási végpontot, amely csak hozzáférési jogkivonatokat bocsát ki az alkalmazás jogosult felhasználói számára. Ha a felhasználói hozzáférési jogkivonatokat egy tárolóba gyorsítótárazza, erősen ajánlott a titkosítás használata.

### <a name="access-token-scopes"></a>Hozzáférési jogkivonat hatókörei

A hatókörök lehetővé teszik az Azure kommunikációs szolgáltatások pontos funkcióinak megadását, amelyekkel a felhasználói hozzáférési tokenek képesek lesznek engedélyezni. A hatóköröket az egyes felhasználói hozzáférési jogkivonatokra alkalmazza a rendszer. Az Azure kommunikációs szolgáltatások a következő hatóköröket támogatják a felhasználói hozzáférési tokenek esetében:

| Név   | Leírás                                                                         |
| ------ | ----------------------------------------------------------------------------------- |
| `Chat` | Lehetőséget biztosít csevegésben való részvételre                                         |
| `VoIP` | Lehetővé teszi, hogy VOIP-hívásokat lehessen kezdeményezni és fogadni a hívó ügyféloldali kódtár használatával * |
| `Pstn` | Lehetővé teszi PSTN-hívások kezdeményezését a hívó ügyféloldali kódtár használatával *           |

\* A funkció még nem támogatott, és hamarosan elérhető lesz

Ha el szeretné távolítani egy felhasználó hozzáférését bizonyos funkciókhoz, először [vissza kell vonnia azokat a meglévő hozzáférési jogkivonatokat](#revoke-user-access-tokens) , amelyek tartalmazhatnak nemkívánatos hatóköröket, mielőtt új jogkivonatot adna ki egy korlátozottabb hatókörű készlettel.
