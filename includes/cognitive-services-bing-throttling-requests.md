---
author: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/09/2018
ms.author: nitinme
ms.openlocfilehash: 1d8340054ace25a0cdc36eef9c3d5b4238a6f99b
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572242"
---
A szolgáltatás és az előfizetés típusa határozza meg a másodpercenként futtatható lekérdezések számát (QPS). Mindenképpen gondoskodjon arról, hogy az alkalmazás tartalmazza a kvótán belül maradáshoz szükséges logikát. Ha eléri vagy túllépi a QPS határértékét, a kérés meghiúsul, és a rendszer HTTP 429-es állapotkódot ad vissza. A válasz a `Retry-After` fejlécet is tartalmazza, amely azt adja meg, hogy mennyi ideig kell várnia egy újabb kérés elküldése előtt.

## <a name="denial-of-service-versus-throttling"></a>Szolgáltatásmegtagadás kontra szabályozás

A szolgáltatás különbséget tesz a szolgáltatásmegtagadásos (DoS-) támadások és a QPS-eltérések között. Ha DoS-támadást feltételez, a kérés sikeres lesz (200 OK HTTP-állapotkóddal). A választörzs azonban üresen marad.