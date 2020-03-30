---
title: Azure API Management-példány automatikus skálázásának konfigurálása | Microsoft dokumentumok
description: Ez a témakör ismerteti, hogyan állíthatja be az automatikus skálázási viselkedést egy Azure API Management-példányhoz.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 8c1c96fdb1f4f42c7592791881b855f74d411171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70018283"
---
# <a name="automatically-scale-an-azure-api-management-instance"></a>Az Azure API Management-példány automatikus skálázása  

Az Azure API Management szolgáltatáspéldány automatikusan skálázható szabályok alapján. Ez a viselkedés engedélyezhető és konfigurálható az Azure Monitoron keresztül, és csak az Azure API Management szolgáltatás **standard** és **prémium** szintjei támogatják.

A cikk végigvezeti az automatikus skálázás konfigurálásának folyamatán, és az automatikus skálázási szabályok optimális konfigurálását javasolja.

> [!NOTE]
> API Management szolgáltatás a **felhasználási** szint skálák automatikusan a forgalom alapján - anélkül, hogy további konfigurációra van szükség.

## <a name="prerequisites"></a>Előfeltételek

A cikk ben leírt lépések követéséhez a következőket kell tennie:

+ Aktív Azure-előfizetéssel rendelkezik.
+ Rendelkezik egy Azure API Management-példány. További információ: [Create an Azure API Management instance](get-started-create-service-instance.md).
+ Ismerje meg az [Azure API Management-példány kapacitása fogalmát.](api-management-capacity.md)
+ Az [Azure API Management-példány manuális skálázási folyamatának ismertetése,](upgrade-and-scale.md)beleértve a költségkövetkezményeket is.

[!INCLUDE [premium-standard.md](../../includes/api-management-availability-premium-standard.md)]

## <a name="azure-api-management-autoscale-limitations"></a>Az Azure API Management automatikus skálázási korlátai

Az automatikus skálázási viselkedés konfigurálása előtt figyelembe kell venni a skálázási döntések bizonyos korlátait és következményeit.

+ Az automatikus skálázás csak az Azure API Management szolgáltatás **standard** és **prémium** szintjeihez engedélyezhető.
+ A tarifacsomagok azt is meghatározzák, hogy egy szolgáltatáspéldány hoz-hány egységet.
+ A méretezési folyamat legalább 20 percet vesz igénybe.
+ Ha a szolgáltatást egy másik művelet zárolja, a skálázási kérelem sikertelen lesz, és automatikusan újrapróbálkozik.
+ Többrégiós üzembe helyezésekkel rendelkező szolgáltatás esetén csak az **Elsődleges helyen** lévő egységek méretezhetők. Más helyeken lévő egységek nem méretezhetők.

## <a name="enable-and-configure-autoscale-for-azure-api-management-service"></a>Automatikus skálázás engedélyezése és konfigurálása az Azure API Management szolgáltatáshoz

Az alábbi lépéseket követve konfigurálhatja az automatikus skálázást egy Azure API Management szolgáltatáshoz:

1. Keresse meg a **Példány figyelése** az Azure Portalon.

    ![Azure Monitor](media/api-management-howto-autoscale/01.png)

2. Válassza a bal oldali menü **Automatikus méretezés** parancsát.

    ![Az Azure Monitor automatikus skálázási erőforrása](media/api-management-howto-autoscale/02.png)

3. Keresse meg az Azure API Management szolgáltatást a legördülő menük szűrői alapján.
4. Válassza ki a kívánt Azure API Management szolgáltatáspéldányt.
5. Az újonnan megnyitott szakaszban kattintson az **Automatikus skálázás engedélyezése** gombra.

    ![Az Azure Monitor automatikus skálázási engedélyezése](media/api-management-howto-autoscale/03.png)

6. A **Szabályok csoportban** kattintson a **+ Szabály hozzáadása gombra.**

    ![Az Azure Monitor automatikus skálázási kiegészítése szabály](media/api-management-howto-autoscale/04.png)

7. Új horizontális skálázási szabály definiálása.

   Például egy horizontális felskálázási szabály egy Azure API Management-egység hozzáadását válthatja ki, ha az átlagos kapacitásmetrika az elmúlt 30 percben meghaladja a 80%-ot. Az alábbi táblázat egy ilyen szabály konfigurációját tartalmazza.

    | Paraméter             | Érték             | Megjegyzések                                                                                                                                                                                                                                                                           |
    |-----------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Metrikaforrás         | Jelenlegi erőforrás  | Határozza meg a szabályt az aktuális Azure API-felügyeleti erőforrás-metrikák alapján.                                                                                                                                                                                                     |
    | *Feltételek*            |                   |                                                                                                                                                                                                                                                                                 |
    | Idő összesítése      | Átlag           |                                                                                                                                                                                                                                                                                 |
    | Metrika neve           | Kapacitás          | A kapacitásmetrika egy Azure API Management-metrika, amely egy Azure API-felügyeleti példány erőforrásainak használatát tükrözi.                                                                                                                                                            |
    | Időfelbontási szint statisztikája  | Átlag           |                                                                                                                                                                                                                                                                                 |
    | Művelet              | Nagyobb, mint      |                                                                                                                                                                                                                                                                                 |
    | Küszöbérték             | 80%               | Az átlagban megadott kapacitásmetrika küszöbértéke.                                                                                                                                                                                                                                 |
    | Időtartam (perc) | 30                | A kapacitásmetrika átlaga a használati mintákra jellemző időtartomány. Minél hosszabb az időtartam, annál simább lesz a reakció - az időszakos tüskék kevésbé lesznek hatással a horizontális felskálázási döntésre. Azonban ez is késlelteti a kibővített eseményindító. |
    | *Művelet*              |                   |                                                                                                                                                                                                                                                                                 |
    | Művelet             | Szám növelése a következővel |                                                                                                                                                                                                                                                                                 |
    | Példányok száma        | 1                 | Az Azure API Management-példány t1 egységgel horizontálisfelskálázása.                                                                                                                                                                                                                          |
    | Lehűlés (perc)   | 60                | Legalább 20 percet vesz igénybe az Azure API Management szolgáltatás horizontális felskálázása. A legtöbb esetben a 60 perces lehűlési időszak megakadályozza, hogy sok horizontális felskálázást indítson el.                                                                                                  |

8. A szabály mentéséhez kattintson a **Hozzáadás** gombra.

    ![Az Azure Monitor horizontális felskálázási szabálya](media/api-management-howto-autoscale/05.png)

9. Kattintson ismét **a + Adj egy szabályt**.

    Ez úttal meg kell határozni a szabály egy léptékét. Ez biztosítja, hogy az erőforrások nem vész kárba, ha az API-k használata csökken.

10. Új lépték definiálása a szabályban.

    Például egy skálázási szabály kiválthatja egy Azure API Management-egység eltávolítását, ha az átlagos kapacitásmetrika az elmúlt 30 percben alacsonyabb volt, mint 35%. Az alábbi táblázat egy ilyen szabály konfigurációját tartalmazza.

    | Paraméter             | Érték             | Megjegyzések                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
    |-----------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Metrikaforrás         | Jelenlegi erőforrás  | Határozza meg a szabályt az aktuális Azure API-felügyeleti erőforrás-metrikák alapján.                                                                                                                                                                                                                                                                                                                                                                                                                         |
    | *Feltételek*            |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Idő összesítése      | Átlag           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Metrika neve           | Kapacitás          | Ugyanaz a metrika, mint a horizontális felskálázási szabályhoz.                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    | Időfelbontási szint statisztikája  | Átlag           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Művelet              | Kisebb mint         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Küszöbérték             | 35%               | A horizontális felskálázási szabályhoz hasonlóan ez az érték nagymértékben függ az Azure API-kezelés használati mintáitól. |
    | Időtartam (perc) | 30                | Ugyanaz az érték, mint a horizontális felskálázási szabályhoz.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | *Művelet*              |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Művelet             | Szám csökkentése a következővel | Ellentétben azzal, amit a kiskálázási szabályhoz használtak.                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | Példányok száma        | 1                 | Ugyanaz az érték, mint a horizontális felskálázási szabályhoz.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Lehűlés (perc)   | 90                | Skála kell konzervatívabb, mint a skála ki, így a lehűlési időszak hosszabbnak kell lennie.                                                                                                                                                                                                                                                                                                                                                                                                    |

11. A szabály mentéséhez kattintson a **Hozzáadás** gombra.

    ![Az Azure Monitor méretezése a szabályban](media/api-management-howto-autoscale/06.png)

12. Állítsa be az Azure API Management egységek **maximális** számát.

    > [!NOTE]
    > Az Azure API Management egy példány példánylimittel rendelkezik. A korlát egy szolgáltatási szinttől függ.

    ![Az Azure Monitor méretezése a szabályban](media/api-management-howto-autoscale/07.png)

13. Kattintson a **Mentés** gombra. Az automatikus skálázás konfigurálva van.

## <a name="next-steps"></a>További lépések

+ [Azure API Management-szolgáltatáspéldány üzembe helyezése több Azure-régióban](api-management-howto-deploy-multi-region.md)
