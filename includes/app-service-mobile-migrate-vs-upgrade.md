---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 767b7c591928385f6ecaf93db1aca7eaca0205b3
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133825"
---
App Service-ben a Mobile Services áthelyezése két lehetőség van: áttelepítés vagy frissítés. Bármelyik beállítást is választja, hozzáférést biztosít minden olyan App Service-ben, a üzemeltetési képessége:

* *Áttelepítés* szolgáltatás csak a mögöttes környezet, amely futtatja a mobil háttérszolgáltatásban változik. Szükséges **kódmódosítás nélkül** vagy a mobilügyfél, vagy a mobil kiszolgálói projektet. Ha az automatikus migrálási beállítást használja, ez a megőrzi a **service.azure-mobile.net** URL-CÍMÉT. 
* *A frissítés* teszi egy szolgáltatás **kód módosítására** a kiszolgáló és ügyfél-projekt, de lehetővé teszi, hogy, hogy az új mobil SDK funkcióinak kihasználását, például a hitelesítési és rugalmasabb fejlesztései a kiszolgálói projektet. 

