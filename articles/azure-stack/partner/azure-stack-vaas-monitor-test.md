---
title: Egy teszt, az Azure Stack érvényesítési szolgáltatás figyelése |} A Microsoft Docs
description: Egy teszt, az Azure Stack érvényesítési szolgáltatás figyelésére.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 2dc4d3f2855864ff80648b5b9635ff28c0dacbb7
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44163329"
---
# <a name="monitor-a-test-with-azure-stack-validation-as-a-service"></a>A figyelő egy test-szolgáltatásként az Azure Stack-érvényesítéssel

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Egy teszt végrehajtása megtekintésével figyelhető a **műveletek** teszt csomagokat tartalmaz, amelyek folyamatban vannak, vagy completed lapját. Ezen az oldalon részletesen a teszt- és a műveletek állapotát.

## <a name="monitor-a-test"></a>A figyelő egy tesztet

1. Válasszon ki egy megoldást.

2. Válassza ki **kezelés** a munkafolyamat csempékre.

3. Kattintson egy munkafolyamatot a teszt összefoglaló lapjának megnyitásához.

4. Bontsa ki a helyi menü **[...]**  bármely suite példány teszteléséhez.

5. Válassza ki **műveletek megtekintése**

![Helyettesítő szöveg](media\image4.png)

Tartalmazó befejezte a futó tesztek, naplók letölthető a teszt összefoglalás lapon kattintson a **naplók letöltéséhez** egy teszt helyi menüben **[...]** . Az Azure Stack-partnerek ezek a naplók segítségével a sikertelen tesztek kapcsolatos problémák megoldásában.

## <a name="open-the-test-pass-summary"></a>Nyissa meg a tesztelési fázisban összegzése

1. Nyissa meg a portálon. 
2. Válassza ki egy meglévő megoldás, amely tartalmazza a korábban futtatási és ütemezett vizsgálatok nevét.

    ![Teszt pass kezelése](media/managetestpasses.png)

3. Válassza ki **kezelés** a a **teszt pass** panel.
4. Válassza ki a tesztelési fázisban, nyissa meg a teszt összegzése adja át. Áttekintheti a tesztnév, létrehozás dátuma, futtassa, hogy mennyi ideig tartott a teszt, és az eredmény (sikeres vagy sikertelen).
5. Válassza ki [ **...  .** ].

### <a name="test-pass-summary"></a>Teszt pass összegzése

| Oszlop | Leírás |
| --- | --- |
| Teszt neve | A teszt neve. Az érvényesítés szám hivatkozik. |
| Létrehozva | A tesztelési fázisban létrehozásának ideje. |
| Elindítva | Futott az elmúlt teszt ideje. |
| Időtartam | Eltelt idő, hogy mennyi időbe telt a tesztelési fázisban futtatásához. |
| status | Az eredmény (sikeres vagy sikertelen) a rest-fázisához. |
| Ügynök neve | Az ügynököt teljesen minősített tartománynevét. |
| Összes művelet | Kísérlet történt a tesztelési fázisban műveletek teljes száma. |
| Sikeres műveletek | A tesztelési fázis sikeres műveletek száma. |
|  Sikertelen műveletek | A sikertelen műveletek száma. |

### <a name="group-columns-in-the-test-pass-summary"></a>Csoport oszlopok a teszt összegzése adja át

Válassza ki, és a egy olyan oszlop húzza át a csoport létrehozása az oszlop értékét a fejléc.

## <a name="reschedule-a-test"></a>Ütemezze újra a teszt

1. [Nyissa meg a tesztelési fázisban összegzés](#open-the-test-pass-summary).
2. Válassza ki **átütemezése** ütemezni a tesztelési fázisban.
3. Adja meg az Azure Stack-példány a felhőalapú rendszergazdai jelszót.
4. Adja meg a fiók beállításakor megadott diagnosztikai Storage kapcsolati karakterláncát.
5. A teszt le.

## <a name="cancel-a-test"></a>Egy teszt megszakítása

1. [Nyissa meg a tesztelési fázisban összegzés](#open-the-test-pass-summary).
2. Válassza ki **Mégse**.

## <a name="get-test-information"></a>Teszt információk lekérése

1. [Nyissa meg a tesztelési fázisban összegzés](#open-the-test-pass-summary).
2. Válassza ki **információk megtekintéséhez** ütemezni a tesztelési fázisban.

**Információ tesztelése**

| Name (Név) | Leírás |
| -- | -- |
| Teszt neve | A teszt, például az OEM frissítése az Azure Stack 1806 RC neve. |
| Teszt verzió | A teszt, például 5.1.4.0 verziója. |
| Közzétevő | Például a Microsoft a teszt közzétevő. |
| Kategória | A kategória teszt, mint például **funkcionális** vagy **megbízhatóság**. |
| Cél-szolgáltatások | A szolgáltatások tesztelt, például a virtuális gép |
| Leírás | A teszt leírása. |
| Becsült időtartam (perc) | Mennyi ideig a teszt telt percek alatt. |
| Hivatkozások | GitHub-probléma Tracker mutató hivatkozást. |

## <a name="get-test-parameters"></a>Teszt paraméterek lekérése

1. [Nyissa meg a tesztelési fázisban összegzés](#open-the-test-pass-summary).
2. Válassza ki **paraméterek megtekintése** ütemezni a tesztelési fázisban.

**Paraméterek**

| Name (Név) | Leírás |
| -- | -- |
| Teszt neve | A teszt, például oemupdate1806test neve. |
| Teszt verzió | A többi, például 5.1.4.0 verziója. |
| Teszt Példányazonosító | GUID azonosítója, például a teszt meghatározott példányának 20b20645-b400-4f0d-bf6f-1264d866ada9. |
| cloudAdminUser | A felhő rendszergazdája használhat, például a fiók nevére **cloudadmin**. |
| DiagnosticsContainerName | A diagnosztikai tárolót, például azonosítója 04dd3815-5f35-4158-92ea-698027693080. |

## <a name="get-test-operations"></a>Vizsgálati műveletek beolvasása

1. [Nyissa meg a tesztelési fázisban összegzés](#open-the-test-pass-summary).
2. Válassza ki **műveleteinek megtekintéséhez** ütemezni a tesztelési fázisban. A műveletek összefoglalás panel nyílik meg.

## <a name="get-test-logs"></a>Vizsgálati naplók lekérése

1. [Nyissa meg a tesztelési fázisban összegzés](#open-the-test-pass-summary).
2. Válassza ki **naplók letöltéséhez** ütemezni a tesztelési fázisban.  
    A zip-fájlt a naplók letöltéseket tartalmazó ReleaseYYYY-MM-DD.zip nevű.

## <a name="next-steps"></a>További lépések

- Tudjon meg többet a [szolgáltatásként az Azure Stack érvényesítési](https://docs.microsoft.com/azure/azure-stack/partner).
