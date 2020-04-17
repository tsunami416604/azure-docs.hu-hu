---
title: Útmutató a Microsoft biztonsági kódelemzéséhez
description: Ez a cikk a Microsoft Security Code Analysis bővítmény telepítését ismerteti
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/14/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 3ef111817b6351277f975b9b7e454f9a89982451
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460205"
---
# <a name="onboarding-and-installing"></a>Bevezetés és telepítés

A Microsoft biztonsági kódelemzéssel való ismerkedés előfeltételei:

- A Microsoft egységes támogatási ajánlata, a következő szakaszban részletezett módon.
- Egy Azure DevOps-szervezet.
- Az Azure DevOps-szervezet bővítményei telepítésének engedélyezése.
- A felhőben üzemeltetett Azure DevOps-folyamattal szinkronizálható forráskód.

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>A Microsoft biztonsági kódelemzésbővítmény ének beépítése

### <a name="interested-in-purchasing-the-microsoft-security-code-analysis-extension"></a>Szeretné megvásárolni a Microsoft Security Code Analysis bővítményt?

Ha az alábbi támogatási ajánlatok valamelyikével rendelkezik, lépjen kapcsolatba a technikai ügyfélmenedzserrel a meglévő órák megvásárlásához vagy cseréjéhez, hogy hozzáférjen a bővítményhez:

- Egyesített támogatás – Speciális szint
- Egységes támogatási teljesítményszint
- Premier támogatás fejlesztőknek
- Kiemelt támogatás a partnerek számára
- Elsőszámú nagyvállalati támogatás

Ha nem rendelkezik a fent említett támogatási megállapodások egyikével, akkor a bővítményt valamelyik Partnerünktől vásárolhatja meg.

**Következő lépések:**

Lépjen kapcsolatba egy partnerrel az alábbi listából, és kérje meg a Microsoft Security Code Analysis bővítmény megvásárlását.

>**Partnerek:**

- Zónák - Elérhetőségek:cloudsupport@zones.com
- Wortell – Elérhetőségek:info@wortell.nl

### <a name="become-a-partner"></a>Legyen ön is partner

A Microsoft Security Code Analysis csapata a kiemelt támogatási partnerekre vonatkozó megállapodással rendelkező partnereket keres. A partnerek lehetővé teszik az Azure DevOps-ügyfelek számára a biztonságosabb fejlesztést azáltal, hogy eladják a bővítményt azoknak az ügyfeleknek, akik meg kívánják vásárolni, de nem rendelkeznek nagyvállalati támogatási szerződéssel a Microsofttal. Az érdeklődő partnerek [itt](http://www.microsoftpartnersupport.com/msrd/opin)regisztrálhatnak.

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>A Microsoft biztonsági kódelemzésbővítmény telepítése

1. Miután a bővítmény meg lett osztva az Azure DevOps-szervezettel, nyissa meg az Azure DevOps szervezeti lapját. Egy ilyen oldal url-címe példa a `https://dev.azure.com/contoso`.
1. Válassza a bevásárlótáska ikonját a név melletti jobb felső sarokban, majd válassza **a Bővítmények kezelése**lehetőséget.
1. Válassza a **Megosztott**lehetőséget.
1. Válassza ki a Microsoft Security Code Analysis bővítményt, majd a **telepítés**lehetőséget.
1. A legördülő listából válassza ki az Azure DevOps-szervezetet a bővítmény telepítéséhez.
1. Válassza az **Install** (Telepítés) lehetőséget. A telepítés befejezése után elkezdheti használni a bővítményt.

>[!NOTE]
> Még akkor is, ha nincs hozzáférése a bővítmény telepítéséhez, folytassa a telepítési lépésekkel. A telepítési folyamat során hozzáférést kérhet az Azure DevOps-szervezet rendszergazdájától.

A bővítmény telepítése után a biztonságos fejlesztési build feladatok láthatók és elérhetők az Azure-folyamatokhoz.

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>Adott buildfeladatok hozzáadása az Azure DevOps-folyamathoz

1. Az Azure DevOps-szervezetből nyissa meg a csapatprojektjét.
1. Válassza **a Folyamatok** > **buildelése lehetőséget.**
1. Válassza ki azt a folyamatot, amelyhez hozzá szeretné adni a bővítménybuild-feladatokat:
   - Új folyamat: Válassza az **Új** lehetőséget, és kövesse a részletes lépéseket egy új folyamat létrehozásához.
   - Folyamat szerkesztése: Jelöljön ki egy meglévő folyamatot, majd válassza a **Szerkesztés** lehetőséget a folyamat szerkesztésének megkezdéséhez.
1. Jelölje **+** be a Feladatok hozzáadása ablaktáblát, és lépjen a **gombra.**
1. Keresse meg a hozzáadni kívánt buildfeladatot a listában vagy a keresőmező használatával. Válassza a **Hozzáadás** lehetőséget.
1. Adja meg a feladathoz szükséges paramétereket.
1. Új build várólistára állítása.
   >[!NOTE]
   >A fájl- és mappaelérési utak a forrástár gyökeréhez viszonyítva találhatók. Ha a kimeneti fájlokat és mappákat paraméterként adja meg, a rendszer lecseréli őket a buildügynöken definiált közös helyre.

> [!TIP]
>
> - Ha a build et követően elemzésfuttatásához helyezze el a Microsoft security code analysis build feladatokat a buildösszetevők közzététele lépés után. Így a build befejezheti és közzéteheti az eredményeket a statikus elemző eszközök futtatása előtt.
> - A biztonságos fejlesztési buildfeladatokhoz mindig válassza **a Folytatás a hiba után** lehetőséget. Még ha az egyik eszköz meghibásodik is, a többiek futhatnak. Nincsenek kölcsönös függőségek az eszközök között.
> - A Microsoft biztonsági kódelemzés buildelési feladatai csak akkor hibásodnak meg, ha az eszköz futtatása sikertelen. De akkor is sikeresek, ha egy eszköz azonosítja a problémákat a kódban. Az elemzés utáni buildfeladat használatával beállíthatja, hogy a build sikertelen, ha egy eszköz problémákat azonosít a kódban.
> - Egyes Azure DevOps-buildfeladatok nem támogatottak, ha egy kiadási folyamaton keresztül fut. Pontosabban az Azure DevOps nem támogatja azokat a feladatokat, amelyek egy kiadási folyamaton belül teszik közzé az összetevőket.
> - Az Azure DevOps Team Build előre definiált változóinak listáját, amely paraméterként adható meg, lásd: [Azure DevOps Build Variables](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts).

## <a name="next-steps"></a>További lépések

A buildfeladatok konfigurálásáról további információt a [konfigurációs útmutatóban](security-code-analysis-customize.md) vagy a [YAML konfigurációs útmutatóban talál.](yaml-configuration.md)

Ha további kérdése van a kiterjesztéssel és a kínált eszközökkel kapcsolatban, tekintse meg [a GYIK oldalt.](security-code-analysis-faq.md)
