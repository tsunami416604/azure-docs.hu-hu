---
title: Az Azure API Management-példány automatikus skálázás konfigurálása |} A Microsoft Docs
description: Ez a témakör ismerteti az Azure API Management-példány automatikus skálázási viselkedés beállítása.
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
ms.openlocfilehash: a01e50debf11daf2f1163a56726f5574f7e3e379
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52444802"
---
# <a name="automatically-scale-an-azure-api-management-instance"></a>Automatikus skálázása az Azure API Management-példány  

Az Azure API Management szolgáltatáspéldány automatikusan skálázhatja a szabályok alapján. Ez a viselkedés is engedélyezni és konfigurálni az Azure monitoron keresztül, és csak a támogatott **Standard** és **prémium** az Azure API Management szolgáltatási szint.

A cikk végigvezeti az automatikus skálázási konfigurálásának folyamatán, és az automatikus skálázási szabályok optimális konfigurációt javasol.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk a lépések követéséhez a következőket kell tennie:

+ Aktív Azure-előfizetéssel rendelkezik.
+ Az Azure API Management-példány van. További információkért lásd: [Azure API Management szolgáltatáspéldány létrehozása](get-started-create-service-instance.md).
+ Megismerése fogalmat [az Azure API Management-példány kapacitása](api-management-capacity.md).
+ Megismerheti [manuális skálázása az Azure API Management-példány folyamatán](upgrade-and-scale.md), többek között a költségek következményeit.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="azure-api-management-autoscale-limitations"></a>Az Azure API Management automatikus skálázási korlátozások

Bizonyos korlátozások és méretezési döntések következményeinek figyelembe kell venni az automatikus skálázási viselkedés beállítása előtt.

+ Az automatikus méretezés csak akkor engedélyezhető **Standard** és **prémium** Azure API Management szolgáltatási szint.
+ Tarifacsomag is adjon meg egy szolgáltatáspéldány egységek maximális száma.
+ Méretezés a folyamat legalább 20 percet vesz igénybe.
+ Ha a szolgáltatás zárolva van egy másik művelet, méretezésére vonatkozó kérés meghiúsul, és próbálkozzon újra automatikusan.
+ A többszörös regionálisan üzemelő példányokba, a csak az egység egy szolgáltatás esetén a **elsődleges hely** skálázhatók. Más helyeken egység nem lehet méretezni.

## <a name="enable-and-configure-autoscale-for-azure-api-management-service"></a>Engedélyezze és konfigurálja az automatikus méretezés az Azure API Management szolgáltatás

Automatikus skálázás az Azure API Management szolgáltatás konfigurálása az alábbi lépésekkel:

1. Navigáljon a **figyelő** példány az Azure Portalon.

    ![Azure Monitor](media/api-management-howto-autoscale/01.png)

2. Válassza ki **automatikus skálázási** a bal oldali menüből.

    ![Az Azure Monitor automatikus méretezés erőforrás](media/api-management-howto-autoscale/02.png)

3. Keresse meg az Azure API Management szolgáltatás a legördülő menük szűrők alapján.
4. Válassza ki a kívánt Azure API Management-szolgáltatáspéldányt.
5. Kattintson az újonnan létrehozott csoport a **automatikus skálázás engedélyezése** gombra.

    ![Az Azure Monitor automatikus méretezés engedélyezése](media/api-management-howto-autoscale/03.png)

6. Az a **szabályok** területén kattintson **és a egy szabály hozzáadásához**.

    ![Az Azure Monitor automatikus skálázási szabály hozzáadása](media/api-management-howto-autoscale/04.png)

7. Adjon meg egy új horizontális felskálázási szabály.

   Egy horizontális felskálázási szabály például elindíthat egy egy Azure API Management egységek hozzáadásával, amikor az átlagos kapacitás a metrika az elmúlt 30 perc meghaladja a 80 %-os. Az alábbi táblázat az ilyen szabályok konfigurációja.

    | Paraméter             | Érték             | Megjegyzések                                                                                                                                                                                                                                                                           |
    |-----------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Metrikaforrás         | Aktuális erőforrás  | Adja meg a szabály az Azure API Management aktuális erőforrás-metrikák alapján.                                                                                                                                                                                                     |
    | *Feltételek*            |                   |                                                                                                                                                                                                                                                                                 |
    | Idő összesítése      | Átlag           |                                                                                                                                                                                                                                                                                 |
    | Metrika neve           | Kapacitás          | A kapacitás a metrika egy Azure API Management-metrika tükröző erőforrások az Azure API Management-példány.                                                                                                                                                            |
    | Időfelbontási szint statisztikája  | Átlag           |                                                                                                                                                                                                                                                                                 |
    | Művelet              | Nagyobb, mint      |                                                                                                                                                                                                                                                                                 |
    | Küszöbérték             | 80 %-os               | A kapacitás átlagolt mérőszám a küszöbértéket.                                                                                                                                                                                                                                 |
    | Időtartam (perc) | 30                | Az időtartomány, a kapacitás metrika átlagos csak használati mintákat. Minél hosszabb az adott időszakban, a egyenletesebb lesz reakciója – időnként ugrásszerűen lesz kisebb hatással a horizontális felskálázás döntést. Azonban azt is késleltetni fogja a horizontális felskálázás eseményindító. |
    | *Művelet*              |                   |                                                                                                                                                                                                                                                                                 |
    | Művelet             | Mennyiség növelése a következővel: |                                                                                                                                                                                                                                                                                 |
    | Példányszám        | 1                 | Horizontális felskálázás az Azure API Management-példány által 1 egység.                                                                                                                                                                                                                          |
    | Lehűlés (percben)   | 60                | Az Azure API Management szolgáltatás horizontális felskálázási legalább 20 percet vesz igénybe. A legtöbb esetben a nyugalomba kerülési időszakban 60 percet megakadályozza, hogy sok méretezési mintáit elindítása.                                                                                                  |

8. Kattintson a **Hozzáadás** a szabály mentéséhez.

    ![Az Azure Monitor horizontális felskálázási szabály](media/api-management-howto-autoscale/05.png)

9. Kattintson ismét a **és a egy szabály hozzáadásához**.

    Ennek során egy horizontális leskálázási szabály kell definiálni. Erőforrások vannak nem folyamatban adattisztítást, amikor az API-k használata csökkenti a biztosítja.

10. Adjon meg egy új méretezési a szabályt.

    Például egy horizontális leskálázási szabály válthatja ki egy eltávolító az Azure API Management egység, amikor az átlagos kapacitás a metrika az elmúlt 30 perc volt 35 %-nál kevesebb. Az alábbi táblázat az ilyen szabályok konfigurációja.

    | Paraméter             | Érték             | Megjegyzések                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
    |-----------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Metrikaforrás         | Aktuális erőforrás  | Adja meg a szabály az Azure API Management aktuális erőforrás-metrikák alapján.                                                                                                                                                                                                                                                                                                                                                                                                                         |
    | *Feltételek*            |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Idő összesítése      | Átlag           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Metrika neve           | Kapacitás          | Ez a metrika azonos, amelyet a horizontális felskálázási szabályt használ.                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    | Időfelbontási szint statisztikája  | Átlag           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Művelet              | Kisebb mint         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Küszöbérték             | 35 %-a               | Hasonlóképpen, a horizontális felskálázási szabály, ezt az értéket az erősen attól függ, a használati minták az Azure API Management. |
    | Időtartam (perc) | 30                | Ugyanazt az értéket, amelyet a horizontális felskálázási szabályt használ.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | *Művelet*              |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Művelet             | Mennyiség csökkentése a következővel: | Milyen leváló használták a horizontális felskálázási szabály.                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | Példányszám        | 1                 | Ugyanazt az értéket, amelyet a horizontális felskálázási szabályt használ.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Lehűlés (percben)   | 90                | A nyugalomba kerülési időszakban hosszabbnak kell lennie, hogy a méretezési csoport korlátozóbb, mint a horizontális felskálázás, kell lennie.                                                                                                                                                                                                                                                                                                                                                                                                    |

11. Kattintson a **Hozzáadás** a szabály mentéséhez.

    ![Az Azure Monitor horizontális leskálázási szabály](media/api-management-howto-autoscale/06.png)

12. Állítsa be a **maximális** Azure API Management-egységek számát.

    > [!NOTE]
    > Az Azure API Management-példányt lehet horizontálisan egységek esetében. A korlát szolgáltatásréteg függ.

    ![Az Azure Monitor horizontális leskálázási szabály](media/api-management-howto-autoscale/07.png)

13. Kattintson a **Save** (Mentés) gombra. Az automatikus skálázási lett konfigurálva.

## <a name="next-steps"></a>További lépések

+ [Az Azure API Management-szolgáltatáspéldány üzembe helyezése több Azure-régióban](api-management-howto-deploy-multi-region.md)
