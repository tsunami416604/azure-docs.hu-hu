---
title: Ismerkedés az Azure Advisor szolgáltatással
description: Ismerkedés az Azure Advisorral.
ms.topic: article
ms.date: 02/01/2019
ms.openlocfilehash: 8c2699030b1a6d428ddc2a4db40a66003824cf10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259629"
---
# <a name="get-started-with-azure-advisor"></a>Ismerkedés az Azure Advisor szolgáltatással

Megtudhatja, hogyan érheti el az Advisort az Azure Portalon keresztül, hogyan kaphat javaslatokat, és valósíthat meg javaslatokat.

> [!NOTE]
> Az Azure Advisor automatikusan fut a háttérben az újonnan létrehozott erőforrások kereséséhez. Az erőforrásokkal kapcsolatos ajánlások akár 24 órába is beletelhet.

## <a name="get-recommendations"></a>Javaslatok beszerezni

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. A bal oldali ablaktáblában kattintson a **Tanácsadó gombra.**  Ha a tanácsadó nem látható a bal oldali ablaktáblában, kattintson a **Minden szolgáltatás gombra.**  A szolgáltatás menüablaktáblájának **Figyelés és kezelés**területén kattintson a **Tanácsadó gombra.** Megjelenik az Advisor irányítópultja.

   ![Access Azure Advisor az Azure Portalhasználatával](./media/advisor-get-started/advisor-portal-menu.png) 

1. Az Advisor irányítópultja a javaslatok összefoglalását jeleníti meg az összes kiválasztott előfizetésre vonatkozóan.  Az előfizetési szűrő legördülő menüje segítségével kiválaszthatja azokat az előfizetéseket, amelyekhez javaslatokat kíván megjeleníteni.

1. Egy adott kategóriára vonatkozó javaslatok lekérni, kattintson az egyik fülre: **Magas rendelkezésre állás,** **Biztonság,** **Teljesítmény**vagy **Költség**. 

   ![Az Azure Advisor irányítópultja](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-recommendation-details-and-implement-a-solution"></a>Az ajánlás részleteinek beszereznie és megvalósítania egy megoldást

Az Advisorban kiválaszthat egy ajánlást további részletek megtekintéséhez – például az ajánlási műveletekhez és az érintett erőforrásokhoz –, és megvalósíthatja a javaslat megoldását.  

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), majd nyissa meg [az Advisor](https://aka.ms/azureadvisordashboard)t.

1. Válasszon egy javaslatkategóriát az adott kategórián belüli javaslatok listájának megjelenítéséhez, vagy kattintson a **Mind** fülre az összes javaslat megtekintéséhez.

1. Kattintson egy részletesen áttekinteni kívánt javaslatra.

1. Tekintse át a javaslatra vonatkozó információkat és azerőforrásokat, amelyekre a javaslat vonatkozik.

1. Kattintson az **ajánlott műveletre** a javaslat végrehajtásához.

## <a name="filter-recommendations"></a>Szűrési javaslatok

A javaslatok szűrése a legfontosabb az Ön számára legfontosabb.  Szűrhet előfizetés, erőforrástípus vagy javaslat állapota szerint.  

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), majd nyissa meg [az Advisor](https://aka.ms/azureadvisordashboard)t.

1. Az Advisor irányítópultján található legördülő menük segítségével előfizetés, erőforrástípus vagy ajánlási állapot szerint szűrhet.

    ![Tanácsadó keresési szűrőjének feltételei](./media/advisor-get-started/advisor-filters.png)

## <a name="postpone-or-dismiss-recommendations"></a>Ajánlások elhalasztása vagy elvetése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), majd nyissa meg [az Advisor](https://aka.ms/azureadvisordashboard)t.

1. Keresse meg az elhalasztani vagy elutasítani kívánt javaslatot.

1. Kattintson a javaslatra.

1. Kattintson **a Halasztás gombra.** 

1. Adjon meg egy elhalasztási időszakot, vagy válassza a **Soha** lehetőséget a javaslat elvetéséhez.

## <a name="exclude-subscriptions-or-resource-groups"></a>Előfizetések vagy erőforráscsoportok kizárása

Előfordulhat, hogy olyan erőforráscsoportokat vagy előfizetéseket szeretne kapni, amelyekre nem szeretne Tanácsadói javaslatokat kapni – például "teszt" erőforrásokat.  Az Advisor konfigurálható úgy, hogy csak adott előfizetésekhez és erőforráscsoportokhoz hozzon létre javaslatokat.

> [!NOTE]
> Ahhoz, hogy egy előfizetést vagy erőforráscsoportot kizárjon az Advisorból, előfizetéstulajdonosnak kell lennie.  Ha nem rendelkezik az előfizetéshez vagy erőforráscsoporthoz szükséges engedélyekkel, a felhasználói felületen le van tiltva a felvétel vagy kizárás lehetősége.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), majd nyissa meg [az Advisor](https://aka.ms/azureadvisordashboard)t.

1. Kattintson a **műveletsáv Konfigurálás** gombjára.

1. Törölje a jelet azon előfizetések vagy erőforráscsoportok jelölőnégyzetéből, amelyekhez nem szeretne Tanácsadói javaslatokat kapni.

    ![Tanácsadó konfigurálja az erőforrásokat példa](./media/advisor-get-started/advisor-configure-resources.png)

1. Kattintson az **Alkalmaz** gombra.

## <a name="configure-low-usage-vm-recommendation"></a>Alacsony használatú virtuálisgép-ajánlás konfigurálása

Ez az eljárás az alacsony használatú virtuálisgép-ajánlás átlagos CPU-kihasználtsági szabályát konfigurálja.

Az Advisor 7 napig figyeli a virtuális gép használatát, majd azonosítja az alacsony kihasználtságú virtuális gépeket. A virtuális gépek akkor minősülnek alacsony kihasználtságúnak, ha a processzorkihasználtságuk 5%, és a hálózati kihasználtságuk kevesebb, mint 2%, vagy ha az aktuális munkaterhelés kisebb virtuálisgép-mérettel is elhelyezhető.

Ha agresszívebb szeretne lenni az alacsony használatú virtuális gépek azonosításában, előfizetésenként módosíthatja az átlagos CPU-kihasználtsági szabályt.  A PROCESSZOR-kihasználtsági szabály 5%, 10%, 15% vagy 20%.

> [!NOTE]
> Az alacsony használatú virtuális gépek azonosítására szolgáló átlagos CPU-kihasználtsági szabály módosításához *előfizetés-tulajdonosnak*kell lennie.  Ha nem rendelkezik az előfizetéshez vagy erőforráscsoporthoz szükséges engedélyekkel, a felhasználói felületen letiltja a felvétel vagy kizárás lehetőségét. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), majd nyissa meg [az Advisor](https://aka.ms/azureadvisordashboard)t.

1. Kattintson a **műveletsáv Konfigurálás** gombjára.

1. Kattintson a **Szabályok** fülre.

1. Válassza ki azokat az előfizetéseket, amelyeknél módosítani szeretné az átlagos CPU-kihasználtsági szabályt, majd kattintson a **Szerkesztés gombra.**

1. Válassza ki a kívánt átlagos CPU-kihasználtsági értéket, és kattintson **az Alkalmaz gombra.**

1. Kattintson **a Javaslatok frissítése** gombra az új átlagos CPU-kihasználtsági szabály használatához a meglévő javaslatok frissítéséhez. 

   ![Az Advisor konfigurálja a javaslati szabályokat példa](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-recommendations"></a>Javaslatok letöltése

Az Advisor lehetővé teszi a javaslatok összegzésének letöltését.  A javaslatokat PDF-fájlként vagy CSV-fájlként töltheti le.  A javaslatok letöltésével könnyedén megoszthatja munkatársaival, vagy elvégezheti saját elemzését a javaslati adatokon felül.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), majd nyissa meg [az Advisor](https://aka.ms/azureadvisordashboard)t.

1. Kattintson a **Letöltés CSV-ként** vagy a **Letöltés PDF formátumban** elemre a műveletsávon.

A letöltési lehetőség tiszteletben tartja az Advisor irányítópultjára alkalmazott szűrőket.  Ha egy adott javaslatkategória vagy javaslat megtekintésekor választja a letöltési lehetőséget, a letöltött összefoglaló csak az adott kategóriára vagy javaslatra vonatkozó információkat tartalmazza. 

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Advisorról, olvassa el az:

- [Bevezetés az Azure Advisorba](advisor-overview.md)
- [Tanácsadó magas rendelkezésre állásra vonatkozó ajánlásai](advisor-high-availability-recommendations.md)
- [Tanácsadó biztonsági ajánlásai](advisor-security-recommendations.md)
- [Tanácsadó teljesítményre vonatkozó ajánlásai](advisor-performance-recommendations.md)
- [Tanácsadói költségajánlások](advisor-performance-recommendations.md)
