---
title: Az Azure Security Center által nyújtott adatbiztonság | Microsoft Docs
description: Ez a dokumentum bemutatja, hogyan kezeli az Azure Security Center az adatokat, és hogyan gondoskodik a védelmükről.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2020
ms.author: memildin
ms.openlocfilehash: d08984165f59f15e9feb739a719d80e7dc3902a4
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905464"
---
# <a name="azure-security-center-data-security"></a>Az Azure Security Center által nyújtott adatbiztonság
A fenyegetések megelőzése, észlelése és elhárítása érdekében a Azure Security Center gyűjti és dolgozza fel a biztonsággal kapcsolatos adatokat, beleértve a konfigurációs információkat, a metaadatokat, az eseménynaplókat és egyebeket. A Microsoft szigorú megfelelőségi és biztonsági szabályokat követ, a kódolástól kezdve egészen a szolgáltatások üzemeltetéséig.

Ez a cikk bemutatja, hogyan kezeli az Azure Security Center az adatokat, és hogyan gondoskodik a védelmükről.

## <a name="data-sources"></a>Adatforrások
Az Azure Security Center az alábbi forrásokból kapott adatok elemzésével biztosítja a biztonsági állapot áttekinthetőségét, azonosítja a biztonsági réseket, javításokat javasolj, valamint észleli az aktív fenyegetéseket:

- Azure Services: az adott erőforrás-szolgáltatóval kommunikálva üzembe helyezett Azure-szolgáltatások konfigurációjával kapcsolatos információkat használja.
- Hálózati forgalom: a hálózati forgalom metaadataiból vett mintát használja a Microsoft infrastruktúrájából, például a forrás és a cél IP-címét és portját, a csomagméretet és a hálózati protokollt.
- Partnermegoldások: Az integrált partnermegoldásoktól, például tűzfalaktól és kártevőirtó megoldásoktól érkező biztonsági riasztásokat használja.
- A Virtual Machines és a kiszolgálók: konfigurációs adatokat és információkat használ a biztonsági eseményekről, például a Windows-események és-naplók, az IIS-naplók és a virtuális gépek syslog-üzeneteiről. Ezenkívül a riasztások létrejöttekor az Azure Security Center pillanatfelvételt készíthet az érintett virtuálisgép-lemezről, és kinyerheti belőle a riasztással kapcsolatos gépösszetevőket (például a beállításjegyzék-fájlt vizsgálati célokra).


## <a name="data-protection"></a>Adatvédelem
**Az adatok elkülönítése**: az adatok logikailag elkülönítve vannak tárolva a szolgáltatás egyes összetevőiben. Az összes adat szervezet szerint van megcímkézve. Ez a címkézés megmarad az adatok teljes életciklusa alatt, és a szolgáltatás minden rétegében érvényes.

**Adatokhoz való hozzáférés**: a biztonsági javaslatok biztosításához és az esetleges biztonsági fenyegetések kivizsgálásához a Microsoft munkatársai hozzáférhetnek az Azure-szolgáltatások által összegyűjtött vagy elemzett információkhoz, beleértve a folyamat-létrehozási eseményeket, a VM-lemezek pillanatképeit és összetevőit, amelyek véletlenül magukban foglalhatják a virtuális gépekről származó ügyféladatokat vagy személyes adatokat. Betartjuk a [Microsoft Online Services és az adatvédelmi nyilatkozat](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) feltételeit, amelyek szerint a Microsoft nem használja fel az ügyféladatokat, és nem veszi igénybe azokat hirdetési vagy hasonló kereskedelmi célokra. Az ügyféladatokat szükség esetén csak arra használjuk, hogy biztosítsuk Önnek az Azure-szolgáltatásokat, beleértve a szolgáltatások nyújtásának megfelelő célokat is. Minden, az ügyféladatokhoz fűződő jog az Ön tulajdonában marad.

**Az adatok felhasználása**: a Microsoft a különböző bérlőknél észlelt mintákat és fenyegetésre vonatkozó intelligenciát használ a megelőzési és észlelési funkcióihoz, és ezt az [adatvédelmi nyilatkozatában](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx) ismertetett adatvédelmi kötelezettségeinek megfelelően teszi.

## <a name="data-location"></a>Az adatok tárolási helye

**A munkaterület (ok)**: a következő térségek egy munkaterület van megadva, az Azure-beli virtuális gépekről összegyűjtött adatokat, köztük a riasztási adatok bizonyos típusait is, a legközelebbi munkaterületen tároljuk.

| Virtuális gép geo                              | Munkaterület geo |
|-------------------------------------|---------------|
| Egyesült Államok, Brazília, Dél-Afrika | Egyesült Államok |
| Kanada                              | Kanada        |
| Európa (kivéve az Egyesült Királyságot)   | Európa        |
| Egyesült Királyság                      | Egyesült Királyság |
| Ázsia (kivéve India, Japán, Korea, Kína)   | Ázsia és a Csendes-óceáni térség  |
| Dél-Korea                              | Ázsia és a Csendes-óceáni térség  |
| India                               | India         |
| Japán                               | Japán         |
| Kína                               | Kína         |
| Ausztrália                           | Ausztrália     |


A virtuálisgép-lemez pillanatképeit ugyanaz a tárfiók tárolja, mint a virtuálisgép-lemezt.

A más környezetben futó (pl. helyszíni) virtuális gépek és kiszolgálók esetében megadhatja a munkaterületet és régiót, ahol tárolni kívánja az összegyűjtött adatokat.

**Az Azure Security Center tárhelye**: A biztonsági riasztásokkal (többek között a partnerriasztásokkal) kapcsolatos információk tárolása a kapcsolódó Azure-erőforrás helyétől függően történik, míg a biztonsági állapottal és javaslatokkal kapcsolatos információk tárolása központilag, az ügyfél földrajzi helyétől függően az Egyesült Államokban vagy Európában történik. A gépösszetevők tárolása központilag történik, ugyanabban a régióban, ahol a virtuális gép is található.

## <a name="manage-data-collection-from-virtual-machines"></a>Az adatgyűjtés kezelése a virtuális gépekről

Ha bekapcsolja a Security Centert az Azure-ban, az adatgyűjtés bekapcsolódik minden előfizetésénél. Az előfizetések adatgyűjtését az Azure Security Center Biztonsági szabályzat részén is bekapcsolhatja. Amikor az adatgyűjtés be van kapcsolva, Azure Security Center kiépíti az Log Analytics ügynököt az összes meglévő támogatott Azure-beli virtuális gépen és a létrehozott újakon.
A Log Analytics ügynök különböző biztonsággal kapcsolatos konfigurációkat vizsgál, és azokat az eseményeket [Windows esemény-nyomkövetés](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) nyomkövetésbe. Az operációs rendszer emellett az eseménynaplóba írandó eseményeket hoz létre a gép futtatása során. A gyűjtött adatok például a következők: az operációs rendszer típusa és verziója, az operációs rendszer naplói (Windows-eseménynaplók), a futó folyamatok, a gép neve, az IP-címek, a bejelentkezett felhasználó és a bérlő azonosítója. A Log Analytics ügynök beolvassa az Eseménynapló-bejegyzéseket és a ETW-nyomkövetéseket, és átmásolja őket a munkaterületre (ok) elemzésre. A Log Analytics ügynök a folyamat-létrehozási eseményeket és a parancssori naplózást is lehetővé teszi.

Ha nem használja az Azure Defendert, akkor a biztonsági házirendben letilthatja a virtuális gépekről történő adatgyűjtést is. Az Azure Defender által védett előfizetések esetében az adatgyűjtés szükséges. A virtuálisgép-lemez pillanatképeinek és összetevőinek gyűjtése akkor is engedélyezve lesz, ha letiltotta az adatgyűjtést.

## <a name="data-consumption"></a>Adathasználat

Az ügyfelek különböző adatstreamekből származó Security Center-adatokat használhatnak, az alábbiak szerint:

* **Azure-tevékenység**: minden biztonsági riasztás, jóváhagyott [Security Center igény](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) szerinti kérelmek, valamint az [adaptív alkalmazások](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)által létrehozott összes riasztás.
* **Azure monitor naplók**: minden biztonsági riasztás.


> [!NOTE]
> A biztonsági javaslatok REST API-n keresztül is feldolgozhatók. További információk: [A biztonsági erőforrás-szolgáltató REST API-ja – referencia](https://msdn.microsoft.com/library/mt704034(Azure.100).aspx).

## <a name="see-also"></a>Lásd még
Ebből a dokumentumból megtudta, hogyan kezeli az Azure Security Center az adatokat, és hogyan gondoskodik azok védelméről. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Azure Security Center tervezési és üzemeltetési útmutató](security-center-planning-and-operations-guide.md) – megtudhatja, hogyan tervezheti meg és értelmezheti a Azure Security Center elfogadásához szükséges tervezési szempontokat.
* [Biztonsági állapot figyelése Azure Security Centerban](security-center-monitoring.md) – útmutató az Azure-erőforrások állapotának monitorozásához
* [Biztonsági riasztások kezelése és válaszadás a Azure Security Centerban](security-center-managing-and-responding-alerts.md) – útmutató a biztonsági riasztások kezeléséhez és megválaszolásához
* [Partneri megoldások monitorozása Azure Security Centerokkal](security-center-partner-solutions.md) – megtudhatja, hogyan figyelheti a partneri megoldások állapotát.
* [Azure Security blog](https://docs.microsoft.com/archive/blogs/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
