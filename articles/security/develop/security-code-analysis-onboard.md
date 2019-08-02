---
title: Microsoft Azure biztonsági kód elemzésének előkészítési útmutatója
description: Ez a cikk a biztonsági kód elemzési bővítményének telepítését ismerteti
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 8a8eca73205d4f8f33d4d069fda72638af61d355
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718300"
---
# <a name="how-to-onboarding-and-installing"></a>kézikönyv: Előkészítés és telepítés

Előfeltételek a Microsoft biztonsági kód elemzésének megkezdéséhez
  - Jogosult Microsoft Egyesített támogatás szolgáltatások ajánlata (részletek alább)
  - Azure DevOps-szervezet
  - Engedélyek az Azure DevOps-szervezet bővítményeinek telepítéséhez
  - A felhőben üzemeltetett Azure DevOps-folyamattal szinkronizálható forráskód


## <a name="onboarding-microsoft-security-code-analysis-extension"></a>A Microsoft biztonsági kód elemzési bővítményének bevezetése

- Ha már rendelkezik az alábbi támogatási ajánlatok valamelyikével, egyszerűen lépjen kapcsolatba a technikai fiókkezelő és a purchase\swap meglévő órájával, hogy hozzáférjen a bővítményhez.
   - Egységes támogatás – speciális és teljesítményszint, Premier szintű támogatás fejlesztők számára, Premier szintű támogatás a partnerek számára, vagy a nagyvállalati Premier szintű támogatás.
- Ha a következő támogatási szolgáltatások valamelyikével rendelkezik, vagy nem rendelkezik támogatási csomaggal a Microsofttal, frissítenie kell egy jogosult támogatási ajánlatra:
   - Azure-támogatás partnerek számára, Azure alapszintű, Azure-fejlesztő, Azure standard, Azure Professional Direct vagy Unified support – Core szint
- Egy jogosult támogatási ajánlat megvásárlásához tekintse meg a [támogatási szolgáltatások kezdőlapját](https://www.microsoft.com/enterprise/services/support)
- A támogatási szerződés beszerzése után lépjen kapcsolatba a technikai fiók kezelőjével, amely segítséget nyújt az első lépésekhez és az összes szükséges adat összegyűjtéséhez.
 
>[!NOTE]
> Csak a Microsoft partneri hálózatban regisztrált partnerek vásárolhatnak Premier szintű támogatás partnereknek, ellenkező esetben a korábban említett jogosult támogatási ajánlatok egyikét vásárolhatja meg.

## <a name="installing-microsoft-security-code-analysis-extension"></a>A Microsoft biztonsági kód Analysis Extension telepítése

1. Miután megtörtént a bővítmény megosztása az Azure DevOps-szervezettel, navigáljon az Azure DevOps-szervezet lapjára (például: http://dev.azure.com/contoso)
2. Kattintson a Shopping Bag ikonra a jobb felső sarokban a neve mellett, majd kattintson a Bővítmények kezelése lehetőségre. 
3. Kattintson a Microsoft biztonsági kód elemzése bővítményre, és indítsa el az Azure DevOps UI varázslót a telepítés megkezdéséhez.
4. Válassza ki az Azure DevOps-szervezetet, hogy a bővítményt a legördülő menüből telepítse.
5. Kattintson a telepítés gombra. Ha befejeződik, folytathatja a bővítmény használatát

>[!NOTE]
> Még ha nincs hozzáférése, folytassa a telepítési lépéseket, és a folyamat során az Azure DevOps-szervezet rendszergazdájától kérhet hozzáférést.
>
A bővítmény telepítése után a biztonságos fejlesztési felépítési feladatok láthatók és elérhetők lesznek az Azure-folyamatokhoz való hozzáadáshoz.

## <a name="adding-specific-build-tasks-to-your-devops-pipeline"></a>Konkrét Build-feladatok hozzáadása a DevOps-folyamathoz

1. Nyissa meg a csapat projektjét az Azure DevOps-szervezetből.
2. Navigáljon a **buildek** lapra a **folyamatok** területen 
3. Válassza ki azt a folyamatot, amelyben hozzá szeretné adni a bővítmény felépítési feladatait. 
   - Új – kattintson az **új** elemre, és kövesse az új folyamat létrehozásához szükséges lépéseket.
   - Szerkesztés – válassza ki a folyamatot, és kattintson a **Szerkesztés** gombra egy meglévő folyamat szerkesztésének megkezdéséhez.
4. Kattintson a + gombra a **feladatok hozzáadása** panel megnyitásához.
5. Keresse meg azt a felépítési feladatot, amelyet hozzá szeretne adni a listából, vagy használja a keresőmezőt, majd kattintson a **Hozzáadás**gombra. 
6. Most már továbbra is megadhatja a feladathoz szükséges paramétereket.
>[!NOTE]
>A fájl-vagy könyvtár elérési útjai a forrás-tárház gyökeréhez képest, a kimeneti mappát/fájlokat meghatározó paraméterek pedig a Build ügynökön definiált közös hellyel lesznek lecserélve.

7. Új Build várólistára helyezése.
> [!TIP]
>  - Ha a Build után szeretné futtatni az elemzést, helyezze el a Microsoft biztonsági kód elemzésének felépítése feladatot a Build-összeállítási összetevők közzététele lépés után. Így a Build befejezhető, és a statikus elemzési eszközök futtatása előtt is közzéteheti az eredményeket.
>  - Mindig győződjön meg arról, hogy a biztonságos fejlesztési felépítési feladatok **"Folytatás hiba esetén"** lehetőség van. Még ha az egyik eszköz meghibásodik, a többiek futhatnak. Nincsenek egymásrautaltságok.
>  - A Microsoft biztonsági kód elemzésekor felmerülő feladatok csak akkor lesznek sikertelenek, ha az eszköz sikeres futtatása sikertelen, de nem fog sikerülni, ha és amikor az eszköz a kódban felmerülő problémákat azonosítja. A buildet beállíthatja úgy, hogy megszakítsa, ha egy eszköz a kódban felmerülő problémákat azonosítja, az **elemzés utáni** Build feladat használatával.
>  - Egyes Azure DevOps-felépítési feladatok nem támogatottak, ha "kiadás" folyamaton keresztül futnak. Ez az Azure-DevOps korlátozása. Nem támogatják azokat a feladatokat, amelyek a kiadási folyamaton belül teszik közzé az összetevőket.
>  - Az Azure DevOps Team buildben előre definiált változók listáját, amelyeket paraméterekként adhat meg, lásd: [Azure DevOps Build változók](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts)

## <a name="next-steps"></a>További lépések

A Build-feladatok konfigurálásával kapcsolatos további információkért tekintse meg a [konfigurációs útmutatót](security-code-analysis-customize.md)

Ha további kérdései vannak a bővítményről és a rendelkezésre álló eszközökről, tekintse meg a [Gyakori kérdések oldalát.](security-code-analysis-faq.md)


