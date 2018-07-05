---
title: Csatlakozás az Azure Analysis Services a Power BI |} A Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat az Azure Analysis Services-kiszolgáló a Power BI használatával.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 00a975ae3acb470912c702d8a37717e5c270c9af
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446792"
---
# <a name="connect-with-power-bi"></a>A Power BI

Miután létrehozott egy kiszolgálót az Azure-ban, és üzembe helyezett egy táblázatos modellhez, a szervezet felhasználói csatlakozhat, és megkezdje az adatok készen állnak. 

> [!TIP]
> Ügyeljen arra, hogy a legújabb verzióját használja, [Power BI Desktop](https://powerbi.microsoft.com/desktop/).
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Csatlakozás a Power BI Desktopban

1. A Power BI Desktopban kattintson az **Adatok lekérése** > **Azure** > **Azure Analysis Services-adatbázis** elemre.

2. A **kiszolgáló**, adja meg a kiszolgáló nevét. Ügyeljen arra, hogy a teljes URL-címét; Ha például asazure://westcentralus.asazure.windows.net/advworks.

3. A **adatbázis**, ha ismeri a nevét, a táblázatos modellű adatbázisához vagy perspektíva, amelyhez csatlakozni kíván, illessze be ide. Ellenkező esetben hagyja üresen a mezőt, és válasszon ki egy adatbázist vagy perspektíva később.

4. Válassza ki a kapcsolat lehetőséget, és nyomja le az **Connect**. 

    Mindkét **élő csatlakozás** és **importálás** beállítások támogatottak. Azonban ajánlott, az élő kapcsolatok használja, mivel az importálási mód van bizonyos korlátozások; incidensről a kiszolgáló teljesítményét megváltozhatnak az importálás során. Emellett, ha a modell frissíteni a Power BI szolgáltatásban a **engedélyezi a hozzáférést a Power bi-BÓL** beállítás kiválasztásakor csak érvényes **élő csatlakozás**.

5. Ha a rendszer kéri, adja meg a bejelentkezési hitelesítő adatait. 

6. A **kezelő**, bontsa ki a kiszolgálót, majd válassza ki a modell vagy perspektíva csatlakozhat, és kattintson a kívánt **Connect**. Kattintson egy modellt vagy perspektívát minden objektumot a nézet megjelenítéséhez.

    A modell megnyitása a Power BI Desktopban jelentés nézetben üres jelentéssel. A mezők listában az összes nem rejtett adatmodell-objektumokat jeleníti meg. A csatlakozás állapota a jobb alsó sarokban látható.

## <a name="connect-in-power-bi-service"></a>Csatlakozás a Power bi-ban (szolgáltatás)

1. Hozzon létre egy Power BI Desktop-fájlt, amely a modell élő kapcsolattal rendelkezik a kiszolgálón.
2. A [Power BI](https://powerbi.microsoft.com), kattintson a **adatok lekérése** > **fájlok**, majd keresse meg és válassza ki a .pbix-fájlt.



## <a name="see-also"></a>Lásd még
[Csatlakozás az Azure Analysis Services](analysis-services-connect.md)   
[Ügyfélkódtárak](analysis-services-data-providers.md)

