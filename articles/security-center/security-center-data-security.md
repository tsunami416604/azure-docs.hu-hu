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
ms.date: 11/28/2018
ms.author: memildin
ms.openlocfilehash: 987cdd76ba533fa0ae4b37c2755fe84a00d14de5
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435848"
---
# <a name="azure-security-center-data-security"></a>Az Azure Security Center által nyújtott adatbiztonság
Az Azure Security Center biztonsággal kapcsolatos adatokat gyűjt és dolgoz fel (például konfigurációs információkat, metaadatokat, eseménynaplókat, összeomlási memóriaképeket és még sok mást), hogy segítsen az ügyfeleknek a fenyegetések megelőzésében, észlelésében és elhárításában. A Microsoft szigorú megfelelőségi és biztonsági szabályokat követ, a kódolástól kezdve egészen a szolgáltatások üzemeltetéséig.

Ez a cikk bemutatja, hogyan kezeli az Azure Security Center az adatokat, és hogyan gondoskodik a védelmükről.

## <a name="data-sources"></a>Adatforrások
Az Azure Security Center az alábbi forrásokból kapott adatok elemzésével biztosítja a biztonsági állapot áttekinthetőségét, azonosítja a biztonsági réseket, javításokat javasolj, valamint észleli az aktív fenyegetéseket:

- Azure Services: az adott erőforrás-szolgáltatóval kommunikálva üzembe helyezett Azure-szolgáltatások konfigurációjával kapcsolatos információkat használja.
- Hálózati forgalom: a hálózati forgalom metaadataiból vett mintát használja a Microsoft infrastruktúrájából, például a forrás és a cél IP-címét és portját, a csomagméretet és a hálózati protokollt.
- Partnermegoldások: Az integrált partnermegoldásoktól, például tűzfalaktól és kártevőirtó megoldásoktól érkező biztonsági riasztásokat használja.
- Saját virtuális gépek és kiszolgálók: Konfigurációs és biztonsági eseményekkel kapcsolatos információkat használ (például Windows-esemény- és auditnaplókat, IIS-naplókat, rendszernapló-üzeneteket és a virtuális gépekről származó összeomlási memóriaképeket). Ezenkívül a riasztások létrejöttekor az Azure Security Center pillanatfelvételt készíthet az érintett virtuálisgép-lemezről, és kinyerheti belőle a riasztással kapcsolatos gépösszetevőket (például a beállításjegyzék-fájlt vizsgálati célokra).


## <a name="data-protection"></a>Adatvédelem
**Az adatok elkülönítése**: az adatok logikailag elkülönítve vannak tárolva a szolgáltatás egyes összetevőiben. Az összes adat szervezet szerint van megcímkézve. Ez a címkézés megmarad az adatok teljes életciklusa alatt, és a szolgáltatás minden rétegében érvényes.

**Adathozzáférés**: Annak érdekében, hogy biztonsági javaslatokat biztosítsanak és megvizsgálják a lehetséges biztonsági fenyegetéseket, a Microsoft munkatársai hozzáférhetnek az Azure-szolgáltatások által gyűjtött vagy elemzett adatokhoz (például összeomlási memóriaképekhez, folyamat-létrehozási eseményekhez, a virtuálisgép-lemez pillanatképeihez és összetevőihez, amelyek véletlenül tartalmazhatnak virtuális gépekről származó ügyfél- és személyes adatokat). Betartjuk a [Microsoft Online Services és az adatvédelmi nyilatkozat](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) feltételeit, amelyek szerint a Microsoft nem használja fel az ügyféladatokat, és nem veszi igénybe azokat hirdetési vagy hasonló kereskedelmi célokra. Az ügyféladatokat szükség esetén csak arra használjuk, hogy biztosítsuk Önnek az Azure-szolgáltatásokat, beleértve a szolgáltatások nyújtásának megfelelő célokat is. Minden, az ügyféladatokhoz fűződő jog az Ön tulajdonában marad.

**Az adatok felhasználása**: a Microsoft a különböző bérlőknél észlelt mintákat és fenyegetésre vonatkozó intelligenciát használ a megelőzési és észlelési funkcióihoz, és ezt az [adatvédelmi nyilatkozatában](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx) ismertetett adatvédelmi kötelezettségeinek megfelelően teszi.

## <a name="data-location"></a>Az adatok helye

**Saját munkaterület(ek)**: Az alábbi geók esetében van meghatározott munkaterület. Az Azure-beli virtuális gépekből összegyűjtött adatokat, beleértve az összeomlási memóriaképeket és bizonyos típusú riasztási adatokat, a legközelebbi munkaterület tárolja.

| Virtuális gép geo                              | Munkaterület geo |
|-------------------------------------|---------------|
| Egyesült Államok, Brazília, Dél-afrikai Köztársaság | Egyesült Államok |
| Kanada                              | Kanada        |
| Európa (az Egyesült Királyság kivételével)   | Európa        |
| Egyesült Királyság                      | Egyesült Királyság |
| Ázsia (India, Japán, Korea, Kína kivételével)   | Ázsia és a Csendes-óceáni térség  |
| Dél-Korea                              | Ázsia és a Csendes-óceáni térség  |
| India                               | India         |
| Japán                               | Japán         |
| Kína                               | Kína         |
| Ausztrália                           | Ausztrália     |


A virtuálisgép-lemez pillanatképeit ugyanaz a tárfiók tárolja, mint a virtuálisgép-lemezt.

A más környezetben futó (pl. helyszíni) virtuális gépek és kiszolgálók esetében megadhatja a munkaterületet és régiót, ahol tárolni kívánja az összegyűjtött adatokat.

**Az Azure Security Center tárhelye**: A biztonsági riasztásokkal (többek között a partnerriasztásokkal) kapcsolatos információk tárolása a kapcsolódó Azure-erőforrás helyétől függően történik, míg a biztonsági állapottal és javaslatokkal kapcsolatos információk tárolása központilag, az ügyfél földrajzi helyétől függően az Egyesült Államokban vagy Európában történik.
Az Azure Security Center ideiglenes másolatokat gyűjt az összeomlási memóriaképek fájljairól, és elemzi ezeket a biztonsági réseket kihasználó támadások és a sikeres feltörések nyomai után kutatva. Az Azure Security Center az elemzéseket ugyanazon a földrajzi helyen végzi el, ahol a munkaterület található, és az elemzés után törli az ideiglenes másolatokat.

A gépösszetevők tárolása központilag történik, ugyanabban a régióban, ahol a virtuális gép is található.


## <a name="managing-data-collection-from-virtual-machines"></a>Az adatgyűjtés kezelése a virtuális gépeken

Ha bekapcsolja a Security Centert az Azure-ban, az adatgyűjtés bekapcsolódik minden előfizetésénél. Az előfizetések adatgyűjtését az Azure Security Center Biztonsági szabályzat részén is bekapcsolhatja. Ha az adatgyűjtés be van kapcsolva, az Azure Security Center a Log Analytics-ügynököt az összes meglévő támogatott Azure virtuális gépen és a létrehozott újgépeken írja elő.
A Log Analytics-ügynök megkeresi a különböző biztonsággal kapcsolatos konfigurációkat és eseményeket [a Windows eseménykövetés](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) nyomkövetésekbe. Az operációs rendszer emellett az eseménynaplóba írandó eseményeket hoz létre a gép futtatása során. A gyűjtött adatok például a következők: az operációs rendszer típusa és verziója, az operációs rendszer naplói (Windows-eseménynaplók), a futó folyamatok, a gép neve, az IP-címek, a bejelentkezett felhasználó és a bérlő azonosítója. A Log Analytics-ügynök beolvassa az eseménynapló-bejegyzéseket, és az ETW nyomon követi őket, és elemzésre átmásolja azokat a munkaterület(ek)re. A Log Analytics-ügynök az összeomlási memóriakép-fájlokat is átmásolja a munkaterület(ek)re, engedélyezi a folyamatlétrehozási eseményeket, és engedélyezi a parancssori naplózást.

Ha az Azure Security Center ingyenes verzióját használja, le is tilthatja a virtuális gépekről történő adatgyűjtést a biztonsági szabályzatban. A Standard szintű előfizetésekhez az adatgyűjtés kötelező. A virtuálisgép-lemez pillanatképeinek és összetevőinek gyűjtése akkor is engedélyezve lesz, ha letiltotta az adatgyűjtést.

## <a name="data-consumption"></a>Adathasználat

Az ügyfelek különböző adatstreamekből származó Security Center-adatokat használhatnak, az alábbiak szerint:

* **Azure Activity:** az összes biztonsági riasztás, a jóváhagyott Security Center [just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) kérelmek és az [adaptív alkalmazásvezérlők](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)által generált összes riasztás.
* **Az Azure Monitor naplói:** az összes biztonsági riasztást.


> [!NOTE]
> A biztonsági javaslatok REST API-n keresztül is feldolgozhatók. További információk: [A biztonsági erőforrás-szolgáltató REST API-ja – referencia](https://msdn.microsoft.com/library/mt704034(Azure.100).aspx).

## <a name="see-also"></a>Lásd még
Ebből a dokumentumból megtudta, hogyan kezeli az Azure Security Center az adatokat, és hogyan gondoskodik azok védelméről. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Azure Security Center tervezési és üzemeltetési útmutató](security-center-planning-and-operations-guide.md) – Ismerje meg, hogyan tervezheti meg és ismerheti meg az Azure Security Center bevezetésének tervezési szempontjait.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) – Ismerje meg, hogyan figyelheti az Azure-erőforrások állapotát
* [Biztonsági riasztások kezelése és megválaszolása az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – További információ a biztonsági riasztások kezeléséről és az azokra való válaszadásról
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Ismerje meg, hogyan figyelheti a partnermegoldások állapotát.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
