---
title: Frissítés dinamikus szabályozási megfelelőségfigyelésre az Azure Security Center szabályozási megfelelőségi irányítópultján | Microsoft dokumentumok
description: A jogszabályi megfelelőségi csomagok frissítése (előzetes verzió)
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 4080825bbb1f6c274f5b5aafd28e8c672148b98f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159285"
---
# <a name="update-to-dynamic-compliance-packages-in-your-regulatory-compliance-dashboard-preview"></a>Frissítés dinamikus megfelelőségi csomagokra a szabályozási megfelelőségi irányítópulton (előzetes verzió)

Az Azure Security Center folyamatosan összehasonlítja az erőforrások konfigurációját az iparági szabványok, szabályozások és referenciaértékek követelményeivel. A **jogszabályi megfelelőségi irányítópult** betekintést nyújt a megfelelőségi állapotba annak alapján, hogy ön hogyan felel meg az adott megfelelőségi vezérlőknek és követelményeknek.

Az egyik szabvány, amelynyomon követheti a megfelelőségi állapotot, az [Azure CIS 1.1.0](https://www.cisecurity.org/benchmark/azure/) (hivatalosabban a "CIS Microsoft Azure Foundations Benchmark 1.1.0- s verziója"). 

Az Azure CIS, amely eredetileg megjelenik a megfelelőségi irányítópulton, a Security Center ben található szabályok statikus készletén alapul.

A **dinamikus megfelelőségi csomagok (előzetes verzió)** funkcióval a Security Center automatikusan javítja az iparági szabványok lefedettségét az idő múlásával. A megfelelőségi csomagok lényegében az Azure Policyben meghatározott kezdeményezések. Ezek hozzárendelhetők a kiválasztott hatókörhöz (előfizetés, felügyeleti csoport és így tovább). Ha az irányítópulton leképezett megfelelőségi adatokat szeretne értékelésként leképezve, adjon hozzá egy megfelelőségi csomagot a felügyeleti csoporthoz vagy az előfizetéshez a Biztonsági szabályzatból. A megfelelőségi csomag hozzáadása hatékonyan hozzárendeli a szabályozási megfelelőségi kezdeményezést a kiválasztott hatókörhöz. Ily módon nyomon követheti az irányítópulton az újonnan közzétett szabályozási kezdeményezéseket megfelelőségi szabványként. Amikor a Microsoft új tartalmat ad ki a kezdeményezéshez (új házirendek, amelyek a szabvány több vezérlőjéhez vannak leképezve), a további tartalom automatikusan megjelenik az irányítópulton.

Az Azure CIS benchmark, az **Azure CIS 1.1.0 (új)** dinamikus megfelelőségi csomagja a következővel javítja az eredeti *statikus* verziót:

* További irányelveket is bevonva
* Automatikus frissítés az új lefedettséggel, ahogy hozzáadódik 

Frissítsen az új dinamikus csomagra az alábbiakszerint.

## <a name="adding-a-dynamic-compliance-package"></a>Dinamikus megfelelőségi csomag hozzáadása

Az alábbi lépések bemutatják, hogyan adhat hozzá a dinamikus csomagot az Azure CIS benchmark v1.1.0-s megfelelőségét figyelheti.   

### <a name="update-to-the-azure-cis-110-new-dynamic-compliance-package"></a>Frissítés az Azure CIS 1.1.0 (új) dinamikus megfelelőségi csomagra 

1. Nyissa meg a **Biztonsági házirend** lapot. Ez a lap a felügyeleti csoportok, előfizetések, munkaterületek és a felügyeleti csoport struktúrájának számát mutatja.

1. Válassza ki azt az előfizetést vagy felügyeleti csoportot, amelynek a jogszabályi megfelelőségi állapotát kezelni szeretné. Azt javasoljuk, hogy válassza ki a legmagasabb hatókört, amelyre a szabvány vonatkozik, hogy a megfelelőségi adatok összesítése és nyomon követése az összes beágyazott erőforrások. 

1. Az Iparági & szabályozási szabványok (előzetes verzió) szakaszban láthatja, hogy az Azure CIS 1.1.0 frissíthető az új tartalomhoz. Kattintson **a Frissítés gombra.** 

1. Szükség esetén kattintson a **További szabványok hozzáadása** elemre a Szabályozási **megfelelőségi szabványok hozzáadása** lap megnyitásához. Itt manuálisan kereshet **az Azure CIS 1.1.0 (Új)** és a dinamikus csomagok között más megfelelőségi szabványokhoz, például **a NIST SP 800-53 R4,** **a SWIFT CSP CSCF-v2020**, **az UKO és az Egyesült Királyság NHS**, valamint a Canada **PBMM szabványhoz.**
    
    > [!TIP]
    > Csak a tulajdonos vagy a házirend-közreműködők rendelkeznek a megfelelőségi szabványok hozzáadásához szükséges engedélyekkel. 

    ![Szabályozási csomagok hozzáadása az Azure Security Center szabályozási megfelelőségi irányítópultjához](./media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-additional-standards.png)


1. A Security Center oldalsávján válassza **a Szabályozási megfelelőség** lehetőséget a jogszabályi megfelelőségirányítópult megnyitásához. 
    * Az Azure CIS 1.1.0 (Új) mostantól megjelenik az Ipari & szabályozási szabványok listájában. 
    * Az Azure CIS 1.1.0-s megfelelőség eredeti *statikus* nézete is mellette marad. Előfordulhat, hogy a jövőben automatikusan eltávolítják.

    > [!NOTE]
    > Néhány órát is igénybe vehet, amíg egy újonnan hozzáadott szabvány megjelenik a megfelelőségi irányítópulton.


    [![A régi és az új Azure CIS-t megjelenítő szabályozási megfelelőségi irányítópult](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png)](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png#lightbox)


## <a name="next-steps"></a>További lépések

Ebben a cikkben a következőket tanulta meg:

* A jogszabályi megfelelőségi irányítópulton látható **szabványok frissítése** az új *dinamikus* csomagokra
* Megfelelőségi **csomagok hozzáadása** a további szabványoknak való megfelelés figyeléséhez. 

Egyéb kapcsolódó anyagokról lásd a következő cikkeket: 

- [A Biztonsági központ szabályozási megfelelőségi irányítópultja](security-center-compliance-dashboard.md)
- [Biztonsági szabályzatok használata](tutorial-security-policy.md)
- [Biztonsági javaslatok kezelése az Azure Security Centerben](security-center-recommendations.md) – Ismerje meg, hogyan használhatja az Azure Security Center javaslatait az Azure-erőforrások védelme érdekében.