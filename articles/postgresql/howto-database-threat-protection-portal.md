---
title: Komplex veszélyforrások elleni védelem – Azure Database for postgresql-hez
description: Veszélyforrások elleni védelem észleli a rendellenes adatbázis-tevékenységek utaló esetleges biztonsági fenyegetések az adatbázishoz.
author: bolzmj
ms.author: mbolz
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: bbc0eadebec849dde47cecaf6e3718eb6673a22f
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2019
ms.locfileid: "54912816"
---
# <a name="advanced-threat-protection-for-azure-database-for-postgresql"></a>Komplex veszélyforrások elleni védelem az Azure Database for postgresql-hez

Az Azure Database for PostgreSQL-hez készült Komplex veszélyforrások elleni védelem észleli az adatbázisok hozzáférésére és az adatbázisok biztonságának megsértésére tett szokatlan és potenciálisan kártevő szándékú kísérleteket.

Komplex veszélyforrások elleni védelem a speciális adatbiztonság ajánlat, amely egy fejlett biztonsági képességek az egyesített csomag részét képezi. Komplex veszélyforrások elleni védelem érheti el és keresztül kezeli a [az Azure portal](https://portal.azure.com) és jelenleg előzetes verzióban érhető el.

> [!NOTE]
> A komplex veszélyforrások elleni védelem funkció **nem** a következő az Azure government és szuverén felhő-régiók: US Gov Texas, USA beli államigazgatás – Arizona, USA beli államigazgatás – Iowa, USA, beli államigazgatás – Virginia, USA védelmi Minisztériuma – keleti régiója, USA védelmi Minisztériuma – középső régiója, Németország közép-India, Németország Észak, kelet-Kína, kelet-Kína 2. Látogasson el [elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/) általános rendelkezésre állás érdekében.
>

> [!NOTE]
> Ez a funkció érhető el minden régióban az Azure, Azure Database for PostgreSQL általános célú és memóriahasználatra optimalizált kiszolgálók telepítési helyét.

## <a name="set-up-threat-detection"></a>Állítsa be a fenyegetések észlelése
1. Indítsa el az Azure Portalra a [ https://portal.azure.com ](https://portal.azure.com).
2. Keresse meg az Azure Database for PostgreSQL-kiszolgáló védeni kívánt konfiguráció lapján. Válassza ki a biztonsági beállítások **komplex veszélyforrások elleni védelem (előzetes verzió)**.
3. Az a **komplex veszélyforrások elleni védelem (előzetes verzió)** konfigurációs lapon:

   - Komplex veszélyforrások elleni védelem engedélyezése a kiszolgálón.
   - A **komplex veszélyforrások elleni védelmi beállításait**, a a **küldje a riasztásokat** szöveget adja meg a rendellenes adatbázis-tevékenységek észlelésekor biztonsági riasztást küld e-mailek listáját.
  
   ![Állítsa be a fenyegetések észlelése](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>Ismerkedés a rendellenes adatbázis-tevékenységek

Rendellenes adatbázis-tevékenységek észlelésekor e-mail-értesítést kap. Az e-mailt a gyanús biztonsági eseményről, beleértve az esetleges rendellenes tevékenységekről, adatbázisnevet, kiszolgáló neve, alkalmazás neve és az esemény időpontját jellege nyújt tájékoztatást. Emellett az e-mailt információkat biztosít a lehetséges okokat, és javasolt műveleteket is vizsgálata és enyhítése érdekében az esetleges adatbázis-fenyegetések.
    
1. Kattintson a **legutóbbi riasztás megtekintése** hivatkozásra az e-mailben, megnyílik az Azure portal és az aktív fenyegetéseket észlel áttekintést nyújt az SQL database az Azure Security Center riasztások lap megjelenítése.
    
    ![Rendellenes tevékenység jelentés](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    Aktív fenyegetések megtekintése:

    ![Aktív fenyegetések](./media/howto-database-threat-protection-portal/active-threats.png)

2. Kattintson egy adott riasztás kivizsgálása a fenyegetés és a jövőbeli fenyegetésekkel szembeni szervizelését további részletek és műveletek beolvasásához.
    
    ![Adott riasztás](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>Ismerkedés a fenyegetésészlelés riasztásai

Komplex veszélyforrások elleni védelem integrálható a riasztásokat az [az Azure Security Center](https://azure.microsoft.com/services/security-center/). 

Kattintson a **biztonsági riasztások** alatt **veszélyforrások elleni védelem** indítsa el az Azure Security Center a riasztások lapon, és áttekintheti a aktív SQL észlelt veszélyforrások alapján az adatbázis.

  ![Veszélyforrások elleni protectoin asc](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Díjszabási információkért tekintse meg a [, Azure Database for PostgreSQL-díjszabás](https://azure.microsoft.com/pricing/details/postgresql/)  
