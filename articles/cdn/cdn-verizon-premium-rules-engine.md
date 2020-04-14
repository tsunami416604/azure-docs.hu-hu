---
title: A HTTP-viselkedés felülbírálása az Azure CDN - Verizon Premium szabályok motorral
description: A szabálymotor lehetővé teszi a HTTP-kérelmek Verizon Premium-tól történő kezelésének testreszabását, például bizonyos típusú tartalmak kézbesítésének letiltását, gyorsítótárazási házirend definiálását és a HTTP-fejlécek módosítását.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: 2e5e4265f30631f8e68d8f9e7156ea578ae85e7a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253475"
---
# <a name="override-http-behavior-using-the-azure-cdn-from-verizon-premium-rules-engine"></a>A HTTP-viselkedés felülbírálása az Azure CDN használatával a Verizon Premium-szabályok motorjáról

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Áttekintés

Az Azure CDN-szabálymotor lehetővé teszi a HTTP-kérelmek kezelésének testreszabását. Például bizonyos tartalomtípusok kézbesítésének letiltása, gyorsítótárazási házirend definiálása vagy HTTP-fejléc módosítása. Ez az oktatóanyag bemutatja, hogyan hozhat létre olyan szabályt, amely módosítja a CDN-eszközök gyorsítótárazási viselkedését. A szabályok motorszintaxisáról az [Azure CDN-szabályok motorjának referencia című témaköre](cdn-verizon-premium-rules-engine-reference.md)talál további információt.

## <a name="access"></a>Hozzáférés

A szabálymotor eléréséhez először válassza **a Kezelés** lehetőséget a **CDN-profillap** tetején az Azure CDN felügyeleti lap eléréséhez. Attól függően, hogy a végpont dinamikus helygyorsításra (DSA) van-e optimalizálva, a szabálymotort a végponttípusnak megfelelő szabálykészlettel érheti el:

- Általános webes megjelenítésre vagy más, nem DSA-optimalizálásra optimalizált végpontok:
    
    Válassza a **HTTP Large** lapot, majd a **Szabálymotor**lehetőséget.

    ![Http-szabályok motorja](./media/cdn-rules-engine/cdn-http-rules-engine.png)

- DSA-ra optimalizált végpontok:
    
    Válassza az **ADN** lapot, majd a **Szabálymotor**lehetőséget.
    
    Az ADN a Verizon által a DSA-tartalom megadására használt kifejezés. Az itt létrehozott szabályokat a profil minden olyan végpontja figyelmen kívül hagyja, amely nincs DSA-ra optimalizálva.

    ![Szabályok motor DSA](./media/cdn-rules-engine/cdn-dsa-rules-engine.png)

## <a name="tutorial"></a>Oktatóanyag

1. A **CDN-profil** lapon válassza a **Kezelés lehetőséget.**
   
    ![CDN-profil kezelés gombja](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    Megnyílik a CDN felügyeleti portál.

2. Válassza a **HTTP Large** lapot, majd a **Szabálymotor**lehetőséget.
   
    Megjelennek az új szabályok beállításai.
   
    ![A CDN új szabálybeállításai](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > A több szabály felsorolásának sorrendje befolyásolja azok kezelését. Egy későbbi szabály felülbírálhatja az előző szabály által meghatározott műveleteket.
   >

3. Írjon be egy nevet a **Név / Leírás** mezőbe.

4. Azonosítsa, hogy a szabály milyen típusú kérelmekre vonatkozik. Használja az alapértelmezett egyezési feltételt, **mindig**.
   
   ![CDN-szabály egyezési feltétele](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!NOTE]
   > A legördülő listában több egyezési feltétel is elérhető. Az aktuálisan kiválasztott egyezési feltételről a bal oldalán lévő kék információs ikonra van kijelölve.
   >
   >  A feltételes kifejezések részletes listáját a [Szabálymotor feltételes kifejezései című témakörben található.](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
   >  
   > Az egyezési feltételek részletes listáját a [Szabályok motoregyezési feltételei című témakörben található.](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
   >
   >

5. Új funkció hozzáadásához jelölje **+** be a **Szolgáltatások**gomb elemet.  A bal oldali legördülő menüben válassza a **Belső max-age kényszerítése**lehetőséget.  A megjelenő szövegmezőbe írja be a **300**értéket. Ne módosítsa a fennmaradó alapértelmezett értékeket.
   
   ![CDN-szabályszolgáltatás](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > A legördülő listában több funkció is elérhető. Az aktuálisan kijelölt funkcióval kapcsolatos információkért válassza a bal oldalán lévő kék információs ikont.
   >
   > A **Force Internal Max-Age**esetében `Cache-Control` `Expires` az eszköz és a fejlécek felülbírálva szabályozhatók, amikor a CDN peremhálózati csomópont frissíti az eszközt az eredetitől. Ebben a példában a CDN peremhálózati csomópont gyorsítótárazza az eszközt 300 másodpercig, vagy 5 percig, mielőtt frissíti az eszközt az eredetétől.
   >
   > A szolgáltatások részletes listáját a Szabályok motorfunkciók című [témakörben található.](cdn-verizon-premium-rules-engine-reference-features.md)
   >
   >

6. Az új szabály mentéséhez válassza a **Hozzáadás** lehetőséget.  Az új szabály jóváhagyásra vár. A jóváhagyást követően az állapot **függő xml-ről** aktív **XML-re**változik.
   
   > [!IMPORTANT]
   > A szabályok módosítása akár 10 percet is igénybe vehet az Azure CDN-en keresztül történő propagálása.
   >
   >

## <a name="see-also"></a>Lásd még

- [Az Azure CDN – áttekintés](cdn-overview.md)
- [Szabályok motorjának hivatkozása](cdn-verizon-premium-rules-engine-reference.md)
- [A szabálymotor megfelel a feltételeknek](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Szabálymotor feltételes kifejezései](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Szabályok motor jellemzői](cdn-verizon-premium-rules-engine-reference-features.md)
- [Azure-pénteken: Az Azure CDN hatékony új prémium funkciók](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (videó)