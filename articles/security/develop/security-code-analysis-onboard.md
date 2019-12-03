---
title: Microsoft biztonsági kód elemzése – bevezetési útmutató
description: Ez a cikk a Microsoft biztonsági kód elemzése bővítmény telepítését ismerteti.
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
ms.openlocfilehash: 77be6d8518774a1e2656e23024490dd9c1431865
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707255"
---
# <a name="onboarding-and-installing"></a>Előkészítés és telepítés

A Microsoft biztonsági kód elemzésének megkezdéséhez szükséges előfeltételek:

- A következő szakaszban részletezett, jogosult Microsoft Egyesített támogatás-ajánlat.
- Egy Azure DevOps-szervezet.
- Engedélyt ad a bővítmények telepítésére az Azure DevOps-szervezet számára.
- A felhőben üzemeltetett Azure DevOps-folyamattal szinkronizálható forráskód.

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>A Microsoft biztonsági kód elemzési bővítményének bevezetése

- Ha a következő támogatási ajánlatok valamelyikével rendelkezik, forduljon a technikai menedzserhez a meglévő órák megvásárlásához vagy lecserélésekor a bővítményhez való hozzáféréshez:
   - Egységes támogatás – speciális szintű
   - Egyesített támogatás teljesítményi szintje
   - Premier szintű támogatás fejlesztők számára
   - Premier szintű támogatás partnerek számára
   - Nagyvállalati Premier szintű támogatás
- Ha a következő támogatási szolgáltatások valamelyikével rendelkezik, vagy nem rendelkezik Microsoft támogatási csomaggal, akkor a jogosult támogatási ajánlatra kell frissítenie:
   - Azure-támogatás partnereknek
   - Azure Alapszintű támogatás
   - Azure-Fejlesztői támogatás
   - Azure-Standard szintű támogatás
   - Közvetlen Azure Professional
   - Egységesített támogatás – alapszintű
- A jogosult támogatási ajánlat megvásárlásához lépjen a [támogatási szolgáltatások kezdőlapjára](https://www.microsoft.com/enterprise/services/support).
- A támogatási szerződés beszerzése után lépjen kapcsolatba a technikai fiók kezelőjével, és segítsen a szükséges adatok gyűjtésében.

>[!NOTE]
> Ha Ön a Microsoft partnerek hálózatában regisztrált partner, akkor jogosult Premier szintű támogatás megvásárlására partnereink számára. Ellenkező esetben a korábban említett jogosult támogatási ajánlatok egyikét kell megvásárolnia.

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>A Microsoft biztonsági kód elemzése bővítmény telepítése

1. A bővítmény Azure DevOps-szervezettel való megosztása után nyissa meg az Azure DevOps-szervezet lapját. Egy ilyen oldal URL-címe `https://dev.azure.com/contoso`.
1. Válassza a bevásárló táska ikont a neve melletti jobb felső sarokban, majd válassza a **Bővítmények kezelése**lehetőséget.
1. Válassza a **megosztott**lehetőséget.
1. Válassza ki a Microsoft biztonsági kód elemzése bővítményt, majd válassza a **telepítés**lehetőséget.
1. A legördülő listában válassza ki az Azure DevOps-szervezetet a bővítmény telepítéséhez.
1. Válassza az **Install** (Telepítés) lehetőséget. A telepítés befejezése után elkezdheti használni a bővítményt.

>[!NOTE]
> Még ha nincs hozzáférése a bővítmény telepítéséhez, folytassa a telepítési lépésekkel. A telepítési folyamat során az Azure DevOps-szervezet rendszergazdájától kérhet hozzáférést.

A bővítmény telepítése után a biztonságos fejlesztési felépítési feladatok láthatók és elérhetők az Azure-folyamatokhoz való hozzáadáshoz.

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>Konkrét felépítési feladatok hozzáadása az Azure DevOps-folyamathoz

1. Az Azure DevOps-szervezetből nyissa meg a csapat projektjét.
1. Válassza a **folyamatok** > **buildek**lehetőséget.
1. Válassza ki azt a folyamatot, amelyben hozzá szeretné adni a bővítmény felépítési feladatait:
   - Új folyamat: válassza az **új** lehetőséget, majd az új folyamat létrehozásához kövesse a részletes lépéseket.
   - Folyamat szerkesztése: válasszon ki egy meglévő folyamatot, majd válassza a **Szerkesztés** lehetőséget a folyamat szerkesztésének megkezdéséhez.
1. Válassza a **+** lehetőséget, és lépjen a **feladatok hozzáadása** ablaktáblára.
1. A listából vagy a keresőmező használatával keresse meg a felvenni kívánt felépítési feladatot. Válassza a **Hozzáadás** lehetőséget.
1. Határozza meg a feladathoz szükséges paramétereket.
1. Új Build várólistára helyezése.
   >[!NOTE]
   >A fájlok és mappák elérési útjai a forrás tárház gyökeréhez képest relatívak. Ha paraméterként adja meg a kimeneti fájlokat és mappákat, azokat a rendszer a Build ügynökön definiált közös helyre cseréli.

> [!TIP]
>
> - Ha a Build után szeretne elemzést futtatni, helyezze el a Microsoft biztonsági kód elemzése felépítési feladatokat a Build-összeállítási összetevők közzététele lépés után. Így a Build befejezhető, és a statikus elemzési eszközök futtatása előtt is közzéteheti az eredményeket.
> - A biztonságos fejlesztési felépítési feladatokhoz mindig válassza **a folytatás hiba** esetén lehetőséget. Még ha egy eszköz meghibásodik, a többi futtatható. Nincsenek összefüggőségek az eszközök között.
> - A Microsoft biztonsági kód elemzésekor felépítési feladatok csak akkor működnek, ha egy eszköz nem tud sikeresen futni. De akkor is sikeresek lesznek, ha az eszköz hibákat azonosít a kódban. Az elemzés utáni Build feladattal beállíthatja, hogy a Build működése meghiúsuljon, ha egy eszköz hibákat azonosít a kódban.
> - Egyes Azure DevOps-Build feladatok nem támogatottak, ha kiadási folyamaton keresztül futnak. Konkrétabban az Azure DevOps nem támogatja azokat a feladatokat, amelyek egy kiadási folyamaton belül teszik közzé az összetevőket.
> - Az Azure DevOps Team buildben paraméterként megadható előre definiált változók listáját az [Azure DevOps Build változói](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts)című témakörben tekintheti meg.

## <a name="next-steps"></a>Következő lépések

A Build-feladatok konfigurálásával kapcsolatos további információkért tekintse meg a [konfigurációs útmutatót](security-code-analysis-customize.md).

Ha további kérdései vannak a bővítményről és a rendelkezésre álló eszközökről, tekintse meg a [Gyakori kérdések oldalát](security-code-analysis-faq.md).
