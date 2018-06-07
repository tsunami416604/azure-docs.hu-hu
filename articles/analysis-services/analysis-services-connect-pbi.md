---
title: Kapcsolódás Azure Analysis Services a Power BI |} Microsoft Docs
description: Tudnivalók a Power BI használatával egy Azure Analysis Services-kiszolgálóhoz csatlakoznak.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2ab13c0d36102c5cd75a5b297f77b23cae40b530
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596678"
---
# <a name="connect-with-power-bi"></a>Csatlakozás a Power BI

Miután elkészítette a kiszolgáló Azure-ban, és telepített egy táblázatos modell, a szervezet felhasználói csatlakozás és újra kell kezdenie a adatok készen áll. 

> [!TIP]
> Ügyeljen arra, hogy a legújabb verzióját használja [Power BI Desktop](https://powerbi.microsoft.com/desktop/).
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Csatlakozás a Power BI Desktop

1. A Power BI Desktopban, kattintson a **adatok beolvasása** > **Azure** > **Azure Analysis Services-adatbázis**.

2. A **Server**, adja meg a kiszolgáló nevét. Ügyeljen arra, hogy a teljes URL-címmel; például asazure://westcentralus.asazure.windows.net/advworks.

3. A **adatbázis**, ha ismeri a nevét, táblázatos modellű adatbázisnál vagy perspektíva, amelyhez csatlakozni kíván, illessze be ide. Ellenkező esetben hagyja üresen a mezőt, és jelöljön ki egy adatbázist vagy perspektíva később.

4. Jelöljön ki egy kapcsolat beállítást, és nyomja le az **Connect**. 

    Mindkét **élő csatlakozás** és **importálási** beállítások támogatottak. Azonban ajánlott az élő kapcsolatok használja, mivel importálás módra rendelkezik néhány korlátozást; kiszolgáló teljesítményének legjelentősebb, megváltozhatnak importálása során. Is, ha a modell a Power BI-ban, frissíteni kell a **hozzáférés engedélyezése a Power BI-ból** beállítás kiválasztásakor csak érvényes **élő csatlakozás**.

5. Ha a rendszer kéri, adja meg a bejelentkezési hitelesítő adatait. 

6. A **Navigator**, bontsa ki a kiszolgálót, majd válassza ki a modell vagy perspektíva csatlakozni, és kattintson a kívánt **Connect**. Kattintson a modell vagy perspektíva, ha a nézet az összes objektum megjelenítése.

    A modell a Power BI Desktop nyílik meg egy üres jelentést jelentéskészítő nézetben. A mezők listája nem rejtett modell objektumait. A kapcsolat állapota a jobb alsó sarkában jelenik meg.

## <a name="connect-in-power-bi-service"></a>Csatlakozás a Power bi-ban (szolgáltatás)

1. Hozzon létre egy Power BI Desktop-fájl, amely a modellhez élő kapcsolattal rendelkezik a kiszolgálón.
2. A [Power BI](https://powerbi.microsoft.com), kattintson a **adatok beolvasása** > **fájlok**, majd keresse meg és válassza ki a .pbix-fájlt.



## <a name="see-also"></a>Lásd még
[Kapcsolódás Azure Analysis Services](analysis-services-connect.md)   
[Ügyfél-függvénytárak](analysis-services-data-providers.md)

