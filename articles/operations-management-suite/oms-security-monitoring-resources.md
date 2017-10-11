---
title: "Erőforrások figyelése az Operations Management Suite biztonsági és naplózási megoldás |} Microsoft Docs"
description: "Ez a dokumentum segít az OMS biztonsági és naplózási képességek az erőforrások figyelése és biztonsági problémákat azonosítása."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: d6752120-821f-4aa7-a049-25bf5a653b95
ms.service: operations-management-suite
ms.custom: oms-security
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: yurid
ms.openlocfilehash: 2f73266b65a4eda6c8751a2d56bc3f11bf4e6a57
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="monitoring-resources-in-operations-management-suite-security-and-audit-solution"></a>Az Operations Management Suite biztonsági és naplózási megoldás erőforrások figyelése
Ez a dokumentum segít az OMS biztonsági és naplózási képességek segítségével figyelheti az erőforrások és a biztonsági problémák azonosításához.

## <a name="what-is-oms"></a>Mi az az OMS?
A Microsoft Operations Management Suite (OMS), a Microsoft felhő alapú informatikai felügyeleti megoldás, amely segít a kezelése és védelme a helyszíni és felhőalapú infrastruktúra. Az OMS termékkel kapcsolatos további információkért tekintse meg az [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx) című cikket.

## <a name="monitoring-resources"></a>Erőforrások figyelése
Amikor csak lehetséges, ezáltal megakadályozhatja, hogy a biztonsági események történik az elsőként érdemes. Azonban lehetetlen minden biztonsági incidens megelőzése érdekében. Egy biztonsági incidens fordulhat elő, ha szüksége lesz a győződjön meg arról, hogy a hatása másodpercekre csökken.  Nincsenek három fontos javaslatok számát és a biztonsági események hatásának minimalizálása érdekében használható:

* Rendszeresen felmérheti a biztonsági rések a környezetben.
* Rendszeresen ellenőrzi az összes számítógép rendszerek és hálózati eszközöket annak érdekében, hogy azok rendelkeznek a legújabb javítások vannak telepítve.
* Rendszeresen ellenőrzi a naplók és a naplózás mechanizmusok, beleértve az operációs rendszerek eseménynaplóit, az adott alkalmazásnaplók és a behatolás-észlelés rendszernaplókat.

OMS biztonsági és naplózási megoldás lehetővé teszi, hogy informatikai aktívan figyelheti az összes erőforrást, amely segítségével a biztonsági események gyakorolt hatásának minimalizálása érdekében. OMS biztonsági és naplózási rendelkezik biztonsági tartományok erőforrások figyeléséhez használható. A biztonsági tartományok biztonsági figyelés további részletek szerepelnek a következő tartományokkal a beállítások gyors hozzáférést biztosít:

* kártevő szoftver értékelése
* Frissítések felmérése
* Identitás és hozzáférés

> [!NOTE]
> Ezek a beállítások áttekintéséhez olvassa el a [Ismerkedés az Operations Management Suite biztonsági és naplózási megoldás](oms-security-getting-started.md).
> 
> 

### <a name="monitoring-system-protection"></a>Figyelési Rendszervédelem
A mélység megközelítés védelmi minden védelmi réteget fontos az objektum általános biztonsági állapotát. Észlelt fenyegetéseket tartalmazó számítógépek és a nem megfelelő védelemmel ellátott számítógépek jelennek meg a biztonsági tartományok kártevő Assessment csempéjén. A kártevő szoftverek Assessment szereplő információk segítségével azonosíthatja a terv úgy védelmet alkalmazzon azokra a kiszolgálókat, szükség lenne rá. Ez a beállítás eléréséhez kövesse az alábbi lépéseket:

1. A **Microsoft Operations Management Suite** fő irányítópultján kattintson a **Biztonság és auditálás** csempére.
   
    ![Biztonsági és naplózása](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)
2. A a **biztonsági és naplózási** irányítópultján kattintson **kártevőirtó Assessment** alatt **biztonsági tartományok**. A **kártevőirtó Assessment** irányítópult az alábbihoz hasonló:

![kártevő szoftver értékelése](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig2-ga.png)

Használhatja a **kártevő Assessment** irányítópult a következő biztonsági problémák azonosítása érdekében:

* **Aktív fenyegetések**: feltörték, és aktív fenyegetések a rendszer a számítógépekre.
* **Fenyegetések szervizelt**: szervizelt számítógépek feltörték, de a fenyegetések forrását.
* **Lejárt aláírás**: számítógépek, amelyeken engedélyezve van, a kártevők elleni védelem, de az aláírás elavult.
* **Nincs valós idejű védelem**: számítógépek, amelyek nem rendelkeznek telepített kártevőirtó.

### <a name="monitoring-updates"></a>frissítések ellenőrzése
Biztonsági szempontból ajánlott a legfrissebb biztonsági frissítések alkalmazása, és ez a frissítés-kezelési stratégia kell foglalni. A Microsoft Monitoring Agent szolgáltatása (HealthService.exe) frissítés eszközadatokat olvas be a figyelt számítógépekről, és ezután elküldi a frissített információ feldolgozásra a felhőben az OMS szolgáltatáshoz. A Microsoft Monitoring Agent szolgáltatás egy olyan automatikus szolgáltatás van konfigurálva, és azt mindig futnia kell a célszámítógépen.

![frissítések ellenőrzése](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig3.png)

Logika vonatkozik a frissítés adatokat, és a felhőszolgáltatás-adatait rögzíti. Ha hiányzó frissítések találhatók, ezek jelennek meg a **frissítések** irányítópult. Használhatja a **frissítések** irányítópult hiányzó frissítések és alkalmazni azokat a kiszolgálókat, amelyeket szüksége van rájuk a tervének kialakításához. Kövesse az alábbi lépéseket eléréséhez a **frissítések** irányítópult:

1. A **Microsoft Operations Management Suite** fő irányítópultján kattintson a **Biztonság és auditálás** csempére.
2. A a **biztonsági és naplózási** irányítópultján kattintson **frissítések értékelését** alatt **biztonsági tartományok**. A frissítés irányítópult az alábbihoz hasonló:

![Frissítések értékelését](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig4.png)

Az irányítópulton végezheti el az aktuális állapotát a számítógépek megértéséhez és kezeléséhez a kritikus fontosságú fenyegetések frissítések értékelését. Használatával a **kritikus vagy biztonsági frissítések** csempe, a rendszergazdák érhessék el az alább látható módon hiányzó frissítések részletes információkat lesz:

![keresési eredmény](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig5.png)

Ez a jelentés tartalmazza a rendszer ki van téve a, mely tartalmazza a Microsoft KB cikkeket társított biztonsági frissítés és a további információt a biztonsági rés rendelkező MS Bulletin fenyegetés típusú azonosításához használható kritikus információkat.

### <a name="monitoring-identity-and-access"></a>Identitások és hozzáférések figyelése
A felhasználók helyfüggetlen munkavégzéséből és különböző eszközök használatával és a felhőalapú és helyszíni alkalmazásokat, hatalmas mennyiségű elérése rendkívül fontos a hitelesítő adatok védelmének biztosításához. Hitelesítő adatok ellopását támadások megegyeznek, amelyben a támadó eredetileg hozzáférést kap azokhoz a normál felhasználói hitelesítő adatokat a rendszer a hálózaton belül. Számos esetben a kezdeti támadás csak úgy is elérheti a hálózathoz, a végleges célja annak jogosultságú fiókok felderítésére. 

A támadók marad a hálózati ingyenesen elérhető, a hitelesítő adatok kinyerése a munkamenetek más bejelentkezett fiókok tooling eszköz használatával. Attól függően, hogy a rendszer-konfiguráció ezeket a hitelesítő adatokat kinyerhetők, jegyek vagy a még akkor is, titkosítatlan szöveges jelszó formájában.  

> [!NOTE]
> gépek, amelyek közvetlenül kommunikál az internettel jelenik meg, amelyek használatával az összes típusú jól ismert felhasználónevek (pl. rendszergazda) sok sikertelen kísérlet. A legtöbb esetben célszerű OK gombra, ha a jól ismert felhasználónevek nincsenek használatban, és ha a jelszó nem elég erős.
> 
> 

Akkor lehet e támadóknak ahhoz, azok veszélyeztetheti a jogosultságú fiók azonosításához. Kihasználhatja **OMS biztonsági és naplózási megoldás** figyelő identitás és hozzáférés. Kövesse az alábbi lépéseket eléréséhez a **identitások és hozzáférések** irányítópult:

1. Az a **a Microsoft Operations Management Suite** fő irányítópult kattintson a biztonsági, naplózási csempére.
2. A a **biztonsági és naplózási** irányítópultján kattintson **identitás- és hozzáférés** alatt **biztonsági tartományok**. A **identitások és hozzáférések** irányítópult az alábbihoz hasonló:

![identitás és hozzáférés](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig6-ga.png)

A rendszeres felügyeleti stratégia részeként meg kell adnia identitásának ellenőrzésére. Rendszergazda, ha egy adott érvényes felhasználónevet, amelynek sokszor kell kinéznie. Ez előfordulhat, hogy vagy a valós felhasználónév szerzett olyan támadó jelzi, majd próbálja meg találgatásos vagy egy automatikus eszközzel, használja, kódolt jelszó lejárt.

Az irányítópult engedélyezése informatikai részleg gyorsan azonosíthatja a potenciális fenyegetések kapcsolódó identitás- és hozzáférés a vállalati erőforrásokhoz. Különösen akkor fontos, hogy is azonosíthatja a potenciális trendeket, például a bejelentkezések keresztül idő csempe látható meghatározott időtartamra vonatkozóan hányszor történt meg a sikertelen bejelentkezési kísérletek. Ebben az esetben a számítógép **fájlkiszolgáló** bejelentkezések 35 kapott. Azt is ismerheti meg a számítógép további részleteit. 

![További részletekért](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig7-new.png)

A jelentés a számítógépen létrehozott során az ebben a mintában értékes részleteit. Első fellépése, amelyek a **fiók** oszlop próbál meg elérni a rendszer használt felhasználói fiók lehetővé teszi a **TIMEGENERATED** oszlop lehetővé teszi az az időtartam alatt ami a kísérlet történt, és a **LOGONTYPENAME** oszlop lehetővé teszi az a hely, ahol ez a kísérlet történt. Ha ezeket a kísérleteket forráskörnyezetben végzett helyileg a rendszer egy program a **folyamat** volna oszlop megjeleníti a folyamat neve. Olyan esetekben, ahol a bejelentkezési kísérlet egy program érkezik már elérhető a folyamat neve, és most hajthat végre további vizsgálat a célrendszeren.

## <a name="see-also"></a>Lásd még:
Ebben a dokumentumban megismerkedhetett OMS biztonság használatára és az erőforrások figyelése megoldás naplózására. Az OMS által nyújtott védelemmel kapcsolatos további információkat a következő cikkek tartalmaznak:

* [Az Operations Management Suite (OMS) áttekintése](operations-management-suite-overview.md)
* [Ismerkedés az Operations Management Suite biztonsági és naplózási megoldás](oms-security-getting-started.md)
* [A biztonsági riasztások figyelése és megválaszolása az Operations Management Suite biztonsági és auditálási megoldásban](oms-security-responding-alerts.md)

