---
title: "Operations Management Suite biztonsági és auditálási megoldás alapkonfigurációja| Microsoft Docs"
description: "Ez a dokumentum ismerteti, hogyan lehet használni az OMS biztonsági és auditálási megoldást az összes figyelt számítógép alapkonfigurációjának megfelelőségi és biztonsági célú értékelésére."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 17837c8b-3e79-47c0-9b83-a51c6ca44ca6
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/05/2016
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: f0a512fb0684b1db25353bf4bbd35bdf2a07e1b2
ms.openlocfilehash: f29b20ba11b030d98a7a5574f816ea132edc3f6d


---
# <a name="baseline-assessment-in-operations-management-suite-security-and-audit-solution"></a>Az alapkonfiguráció értékelése az Operations Management Suite biztonsági és auditálási megoldásában
Ez a dokumentum segít az [Operations Management Suite (OMS) biztonsági és auditálási megoldás](operations-management-suite-overview.md) alapkonfiguráció-értékelési képességeinek használatában, hogy hozzáférhessen a figyelt erőforrások biztonsági állapotához.

## <a name="what-is-baseline-assessment"></a>Mi az az alapkonfiguráció-értékelés?
A Microsoft a világszerte elhelyezkedő iparági és kormányzati szervezetekkel közösen olyan Windows-konfigurációt határoz meg, amely nagy biztonságú kiszolgálók üzembe helyezését teszi lehetővé. Ez a konfiguráció beállításkulcsokból, auditálási szabályzatok beállításaiból és biztonsági szabályzatok beállításaiból áll, valamint a Microsoft ajánlott értékeit tartalmazza ezekhez a beállításokhoz. Ez a szabályzathalmaz biztonsági alapkonfigurációként ismert. Az OMS biztonság és auditálás alapkonfiguráció-értékelési képességével megfelelőség szempontjából zökkenőmentesen átvizsgálható az összes számítógépe. 

Három szabálytípus áll rendelkezésre:

* **Beállításjegyzék-szabályok**: a beállításkulcsok megfelelő beállításának ellenőrzéséhez.
* **Auditálási házirend-szabályok**: az auditálási házirendre vonatkozó szabályok.
* **Biztonságiházirend-szabályok**: a számítógépen a felhasználói engedélyekre vonatkozó szabályok.

> [!NOTE]
> A funkció rövid áttekintéséhez olvassa el a [Use OMS Security to assess the Security Configuration Baseline](https://blogs.technet.microsoft.com/msoms/2016/08/12/use-oms-security-to-assess-the-security-configuration-baseline/) (Az OMS-biztonság használata a biztonsági alapkonfiguráció értékelésére) című cikket.
> 
> 

## <a name="security-baseline-assessment"></a>A biztonsági alapkonfiguráció értékelése
Az irányítópult használatával áttekintheti az OMS biztonsági és auditálási megoldás által figyelt összes számítógépére vonatkozó jelenlegi alapkonfiguráció-értékelést.  A biztonsági alapkonfiguráció-értékelési irányítópult eléréséhez hajtsa végre az alábbi lépéseket:

1. A **Microsoft Operations Management Suite** fő irányítópultján kattintson a **Biztonság és auditálás** csempére.
2. A **Biztonság és Audit** irányítópulton kattintson az **Alapkonfiguráció értékelése** lehetőségre a **Biztonsági tartományok** alatt. A **Biztonsági alapkonfiguráció értékelése** irányítópult a következő ábrának megfelelően jelenik meg:
   
    ![Az OMS biztonsági és auditálási alapkonfiguráció értékelése](./media/oms-security-baseline/oms-security-baseline-fig1.png)

Ez az irányítópult három fő területet oszlik:

* **Az alapkonfigurációval összevetett számítógépek**: ebben a szakaszban jelenik meg azon számítógépek számának az összegzése, amelyekhez hozzáfértek, valamint az értékelésen megfelelt számítógépek százalékos aránya. A legelső 10 számítógép, valamint az értékelés százalékos eredményei szintén itt láthatók.
* **Kötelező szabályok állapota**: ez a szakasz hívja fel a figyelmet a nem teljesített szabályokra, súlyosság és típus szerint csoportosítva. Az első diagramra pillantva gyorsan azonosíthatja, hogy a nem teljesített szabályok nagy része kritikus fontosságú-e vagy sem. A 10 leggyakrabban nem teljesített szabály és a súlyosságuk listája szintén itt jelenik meg. A második diagram az értékelés során sikertelen szabály típusát jeleníti meg. 
* **Az alapkonfiguráció értékeléséből kimaradt számítógépek**: ez a szakasz azokat a számítógépeket listázza, amelyekhez az operációs rendszer inkompatibilitása vagy hibák miatt nem történt hozzáférés. 

### <a name="accessing-computers-compared-to-baseline"></a>Számítógépek elérése az alapkonfigurációval való összehasonlításhoz
Ideális esetben minden számítógépe kompatibilis a biztonsági alapkonfiguráció értékelésével. Azonban várható, hogy bizonyos körülmények között ez nem valósul meg. A biztonsági felügyeleti folyamat részeként fontos azoknak a számítógépeknek a felülvizsgálata is, amelyek nem feleltek meg az összes biztonsági értékelési teszten. Ennek gyors megjelenítéséhez válassza az **Elért számítógépek** lehetőséget, amely **Az alapkonfigurációval összevetett számítógépek** szakaszban található. Meg kell jelennie a naplózott keresési eredményeknek a számítógépeket tartalmazó listával, a következő képernyőn látható módon:

![Az elért számítógépeket tartalmazó eredmények](./media/oms-security-baseline/oms-security-baseline-fig2.png)

A keresési eredmények táblázatos formátumban jelennek meg, ahol az első oszlop a számítógép nevét, a második pedig a sikertelen szabályok számát tartalmazza. A sikertelen szabályok típusának lekéréséhez kattintson a sikertelen szabályok számára a számítógép neve mellett. Az eredmény a következő ábrához hasonló módon jelenik meg:

![Az elért számítógépeket tartalmazó eredmények részletei](./media/oms-security-baseline/oms-security-baseline-fig3.png)

Ebben a keresési eredményben szerepel az elért szabályok teljes száma, a sikertelen kritikus szabályok száma, a figyelmeztetési szabályok és a sikertelen információt tartalmazó szabályok.

### <a name="accessing-required-rules-status"></a>A kötelező szabályok állapotának elérése
Miután beszerezte az értékelésen megfelelt számítógépek számának százalékos arányára vonatkozó információt, lehet, hogy szeretne további információt arról, hogy mely szabályok sikertelenek kritikusság szempontjából. Ez a képi megjelenítés segít meghatározni a prioritást, hogy mely számítógépekkel kell elsőként foglalkozni a megfelelőségük biztosításához a következő értékelés során. Vigye a kurzort a diagram Kritikus része fölé a **Nem teljesített szabályok súlyosság szerint** csempén a **Kötelező szabályok állapota** alatt, és kattintson rá. Az eredmény a következő képernyőhöz hasonló módon jelenik meg:

![A Nem teljesített szabályok súlyosság szerint részletei](./media/oms-security-baseline/oms-security-baseline-fig4.png) 

Ez a naplóeredmény tartalmazza a nem teljesített alapkonfigurációs szabály típusát, a szabály leírását és a biztonsági szabály Common Configuration Enumeration (CCE) azonosítóját. Ezeknek az attribútumoknak elegendőknek kell lenniük javítási művelet végrehajtásához a célszámítógépen a probléma megoldása érdekében.

> [!NOTE]
> A CCE-re vonatkozó további információkért látogasson el a [National Vulnerability Database](https://nvd.nist.gov/cce/index.cfm) webhelyére.
> 
> 

### <a name="accessing-computers-missing-baseline-assessment"></a>Az alapkonfiguráció értékeléséből kimaradt számítógépek elérése
Az OMS a tartományi tag és a tartományvezérlő alapkonfigurációjának profilját a Windows Server 2008 R2 verziótól a Windows Server 2012 R2 verzióig támogatja. A Windows Server 2016 alapkonfigurációja még nem végleges, és közzététele után azonnal megtörténik a hozzáadása. Minden más operációs rendszer, amelyet az OMS biztonság és audit alapkonfiguráció értékelése megvizsgált **Az alapkonfiguráció értékeléséből kimaradt számítógépek** szakaszban fognak megjelenni.

## <a name="see-also"></a>Lásd még:
Ebben a dokumentumban az OMS biztonság és audit alapkonfigurációs értékeléséről olvashatott. Az OMS által nyújtott védelemmel kapcsolatos további információkat a következő cikkek tartalmaznak:

* [Az Operations Management Suite (OMS) áttekintése](operations-management-suite-overview.md)
* [A biztonsági riasztások figyelése és megválaszolása az Operations Management Suite biztonsági és auditálási megoldásban](oms-security-responding-alerts.md)
* [Az erőforrások figyelése az Operations Management Suite biztonsági és auditálási megoldásban](oms-security-monitoring-resources.md)




<!--HONumber=Dec16_HO1-->


