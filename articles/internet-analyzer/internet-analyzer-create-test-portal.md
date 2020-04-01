---
title: Internetes elemzőteszt létrehozása a Portál használatával | Microsoft dokumentumok
description: Ebből a cikkből megtudhatja, hogyan hozhat létre az első Internet Analyzer tesztet.
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 64bbed9b558d4c20889b28a5247e2113d20daa77
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73501770"
---
# <a name="create-an-internet-analyzer-test-using-portal-preview"></a>Internetes elemzőteszt létrehozása a Portál használatával (előzetes verzió)

Az Azure Portalon vagy a [CLI](internet-analyzer-cli.md)használatával kétféleképpen hozhat létre internetes elemzőerőforrást. Ez a szakasz segít létrehozni egy új Azure Internet Analyzer erőforrás portálos élményünk használatával.

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Előkészületek

A nyilvános előzetes verzió globálisan használható; azonban az adattárolás az *USA West 2-re* korlátozódik az előzetes verzió során.

## <a name="basics"></a>Alapvető beállítások

1. Az Internet Analyzer előzetes hozzáférése a **Hogyan vehetek részt az előzetes verzióban?** [Azure Internet Analyzer FAQ](internet-analyzer-faq.md)
2. Az [Azure Portal](https://preview.portal.azure.com)kezdőlapján kattintson a + **Erőforrás létrehozása**elemre. Az Internet Analyzer jelenleg csak az Azure Portal előzetes verziójából érhető el.
3. Az **Új** lapon keressen rá az "Internet Analyzer" kifejezésre *a Keresés a piactéren* mezőben.
4. Kattintson **az Internet Analyzer (előnézet) elemre.** Győződjön meg arról, hogy a közzétevő a *Microsoft,* és a kategória *a Hálózat*.
5. Az **Internetes elemző (előnézet)** lapon kattintson a **Létrehozás** gombra az **Internetes elemző létrehozása** lap megnyitásához.
6. Adja meg az Internet Analyzer erőforrás következő konfigurációs beállításait:

    * **Előfizetés:** Az Azure-előfizetés az új Internet Analyzer erőforrás üzemeltetéséhez. ***Használja ugyanazt az előfizetési azonosítót, amely az előzetes hozzáférés kéréséhez használt.***
    * **Erőforráscsoport:** Az Azure erőforráscsoport, amelyben az új Internet Analyzer erőforrás jön létre. Ha nem rendelkezik meglévő erőforráscsoporttal, létrehozhat egy újat.
    * **Név:** Az új Internet Analyzer erőforrásprofil neve.
    * **Régió:** Az Azure nyilvános régió, amelyben az erőforrás jön létre. Az előzetes verzió során csak *az US West 2* érhető el.

7. Ha befejezte a profilbeállítások megadását, kattintson a **Véleményezés + létrehozás gombra.**

## <a name="configuration"></a>Konfiguráció

Az alapvető lépések végrehajtása előfeltétele a teszt konfigurálásának és a JavaScript-ügyfél beágyazásának. Miután létrehozott egy profilt, az első teszt konfigurálásához nyissa meg **a Beállítások > konfigurációja** lapot.

1. Adjon nevet a **tesztnév mezőben.**
2. Adjon meg egy leírást a teszthez a **Leírás** mezőben.
3. Kattintson **a Végpont konfigurálása** gombra – egy lap jelenik meg a jobb oldalról. Válassza ki a konfigurálni kívánt végpont típusát – egyetlen Azure-régiót, több Azure-régiót vagy egy egyéni végpontot.

    >
    ***Előre konfigurált végpontok: egy- és több Azure-régiókombináció***
    * Válasszon ki egy régiót vagy régiók egy [készletét az Azure-végpontok előre konfigurált listájából.](internet-analyzer-faq.md)
    * Ezután válassza ki a kiértékelni kívánt alkalmazás- vagy tartalomkézbesítési architektúra típusát.
        * Egyetlen Azure-régió: Webhely gyorsítása[(Azure bejárati ajtajának),](https://azure.microsoft.com/services/frontdoor/)statikus tartalom-gyorsítótárazás[(Azure CDN a Microsoft számára)](https://azure.microsoft.com/services/cdn/)vagy nincs.
        * Több Azure-régió: Webhely gyorsítása[(Azure bejárati ajtó),](https://azure.microsoft.com/services/frontdoor/)DNS-kormányzás ([Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/))  

    ***Egyéni végpontok***
    * Kövesse az [Egyéni végpont létrehozása](internet-analyzer-custom-endpoint.md) lapon található utasításokat.
    * Illessze be az egy képpontnyi kép HTTPS-URL-címét a portálra.
    >

4. Kattintson a **Hozzáadás** gombra, ha hozzá szeretné adni a végpontot a teszthez.
5. Ismételje meg az 1-4. A B végpont mindig az A végponthoz viszonyítva mérhető – a végpontok konfigurálásakor vegye figyelembe, hogy melyik végpont legyen a tesztvezérlő.
6. A teszt mentéséhez kattintson a **Mentés** gombra. A teszt mentése után már nem szerkesztheti egy adott teszt végpontjait.
7. Jelölje ki az elindítani kívánt teszt(eke)t, és kattintson a **Teszt indítása gombra.** Ez a teszt(ek) ***állapotát*** Futás állapotra ***módosítja.*** Bármikor elkezdheti a teszteket, de a JavaScript-ügyfélnek be kell ágyaznia ahhoz, hogy a teszt megkezdhesse a mérések gyűjtését.
8. Bármikor további vizsgálatokat adhat hozzá. Vegye figyelembe, hogy az egyedi JavaScript-ügyfél csak egy teszt létrehozása után jön létre.

## <a name="embed-client"></a>Ügyfél beágyazása

Bármely teszt megkezdéséhez a JavaScript-ügyfelet be kell ágyazni a webalkalmazásba. Legalább egy teszt konfigurálása után kattintson a **Véleményezés + create**gombra, nyissa meg a Beállítások > **konfiguráció**, és másolja a JavaScript-ügyfelet. A konkrét utasítások az [Internet Analyzer Client beágyazása](internet-analyzer-embed-client.md) lapon találhatók.  

## <a name="next-steps"></a>További lépések

* Olvassa el az [Internet Analyzer GYIK-et](internet-analyzer-faq.md)
* További információ az [Internet Analyzer Client](internet-analyzer-embed-client.md) beágyazásáról és [az egyéni végpont létrehozásáról.](internet-analyzer-custom-endpoint.md)
