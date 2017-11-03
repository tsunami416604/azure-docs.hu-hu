---
title: "Figyelés és az Operations Management Suite biztonsági és naplózási megoldás a biztonsági riasztásokra való Reagálásról |} Microsoft Docs"
description: "Ez a dokumentum segít az OMS biztonsági és naplózási fenyegetés eszközintelligencia lehetőség használatával megfigyelése és válaszadás a biztonsági riasztások."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 7d45a32b-1341-4bb5-a436-1f42a8a2590a
ms.service: operations-management-suite
ms.custom: oms-security
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/13/2017
ms.author: yurid
ms.openlocfilehash: df82afab2c38431e134146143524edc080ee38f9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-and-responding-to-security-alerts-in-operations-management-suite-security-and-audit-solution"></a>Figyelés és válaszadás a biztonsági riasztásokat az Operations Management Suite biztonsági és naplózási megoldás
Ez a dokumentum segít az OMS biztonsági és naplózási fenyegetés eszközintelligencia lehetőség használatával megfigyelése és válaszadás a biztonsági riasztások.

## <a name="what-is-oms"></a>Mi az az OMS?
A Microsoft Operations Management Suite (OMS), a Microsoft felhő alapú informatikai felügyeleti megoldás, amely segít a kezelése és védelme a helyszíni és felhőalapú infrastruktúra. Az OMS termékkel kapcsolatos további információkért tekintse meg az [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx) című cikket.

## <a name="threat-intelligence"></a>Fenyegetésészlelési intelligencia
Egy vállalati környezetben, ahol a felhasználók széleskörű férhet hozzá a hálózathoz, és különböző eszközök használatával csatlakozni a vállalati adatok rendkívül fontos, hogy az erőforrások aktív figyelését, és gyors válaszadás a biztonsági eseményekre. Ez az adott biztonsági életciklus szempontjából fontos, mert bizonyos fenyegetések nem vehet fel számítógépes riasztásokat vagy a gyanús tevékenységeket, amely segítségével azonosítható hagyományos biztonsági technikai vezérlők. 

Használatával a **Fenyegetésfelderítési adataival** lehetőség elérhető OMS biztonsági és a naplózási, a rendszergazdák is biztonsági fenyegetések ellen a környezetben, például azonosíthatja, azonosítja, hogy egy adott számítógép része egy [ botnet](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection). A számítógépek akkor válhatnak egy botnet csomópontjává, ha a támadók illetéktelenül kártevőket telepítenek a számítógépére, amelyek titokban csatlakoztatják a gépet a vezérlőrendszerhez. Azt is megállapíthatja potenciális fenyegetések, például a föld kommunikációs csatornákon érkező [darknet](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection_honeypots_darkents). 

Build a fenyegetésfelderítési adataival, hogy az OMS biztonsági és hitelesítési megoldás a Microsofton belül különböző forrásokból származó adatok használja. OMS biztonsági és naplózási fogja használni, ezeket az adatokat a környezet ellen potenciális fenyegetések azonosítására.

A Fenyegetésfelderítés panel három fő területet tartalmaz:

* Kártékony kimenő forgalmat bonyolító kiszolgálók
* Észlelt fenyegetéseket típusok
* Fenyegetésfelderítési térkép

> [!NOTE]
> Ezek a beállítások áttekintéséhez olvassa el a [Ismerkedés az Operations Management Suite biztonsági és naplózási megoldás](oms-security-getting-started.md).
> 
> 

### <a name="responding-to-security-alerts"></a>Biztonsági riasztásokra való reagálásról
A lépések egyikét a [biztonsági incidensekre adott reakciók](https://technet.microsoft.com/library/cc512623.aspx) folyamat az, hogy a biztonsági sérülés rendszer(ek) súlyossága azonosításához. Ebben a fázisban végre kell hajtania a következő feladatokat:

* A támadás természetének megállapítása
* A támadás kiindulópontjának meghatározása
* A támadás szándékának azonosítása. A támadás célzottan az Ön szervezete ellen irányult adott információk megszerzése céljából, vagy csak véletlen támadásról van szó?
* A sérült rendszerek azonosítása
* A fájlok elérése rendelkezik, és határozza meg azokat a fájlokat érzékenységének azonosításához

Kihasználhatja **Fenyegetésfelderítési adataival** információkat az OMS biztonsági és naplózási megoldásban a feladatok érdekében. Ezek eléréséhez az alábbi lépésekkel **Fenyegetésfelderítési adataival** beállítások:

1. A **Microsoft Operations Management Suite** fő irányítópultján kattintson a **Biztonság és auditálás** csempére.
   
    ![Biztonsági és naplózása](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)
2. Az a **biztonsági és naplózási** irányítópult, látni fogja a **Fenyegetésfelderítési adataival** beállításai a jobb oldali alább látható módon:
   
    ![Fenyegetés intel](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig2-ga.png)

Ezek három csempék Erre azért van szükség az aktuális fenyegetéseket áttekintését. Az a **Server kártékony kimenő forgalmat bonyolító** lesz, ha a figyelt számítógépek (belül vagy kívül a hálózat), amely rosszindulatú forgalmat küld az internethez. 

A **észlelt fenyegetés típusok** csempe a fenyegetéseket, amelyek aktuális "a helyettesítő" összegzését jeleníti meg, ha erre a csempére kattintva akkor jelenik meg ezek a fenyegetések további információt az alábbi megjelenítése as:

![Felderített fenyegetéstípusok](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig3.png)

További információ az egyes fenyegetésekre azt nyerhet ki. Az alábbi példában látható Botnet kapcsolatos további részletek:

![További információt a fenyegetést](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig4.png)

Ez a szakasz elején leírtak ezt az információt nagyon hasznosak lehetnek az incidensekre adott reakciók esetben során. Azt is fontos lehet a törvényszéki nyomozások során kell a támadás, mely a rendszer biztonsági szempontból sérült és az ütemterv található. Ebben a jelentésben könnyen azonosíthatja, néhány főbb részleteinek a támadás, például: a támadás, a helyi IP-cím biztonsági sérülés és a jelenlegi munkamenet-állapot, a kapcsolat forrása. 

A **fenyegetés eszközintelligencia térkép** könnyebben azonosítsa az aktuális a világ minden táján rendelkező rosszindulatú forgalmat. Narancssárga (bejövő) és a piros (kimenő) nyilak ezen a térképen, amelyek azonosítják a forgalom irányát, ha valamelyik e nyíl gombra kattint, meg fog jelenni a típusú fenyegetés és a forgalom irányát alább látható módon:

![fenyegetésfelderítési leképezés](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig5.png)

> [!NOTE]
> A bemutató látható, hogyan használhatják ezt a funkciót az incidensválasz-folyamata során, amelyet figyeli a bemutató [datacenter biztonsági fenyegetések mérséklésére az Operations Management Suite használata interaktív vizsgálat](https://myignite.microsoft.com/videos/5000) a Microsoft Ignite-i.
> 

### <a name="responding-to-distinct-malicious-ip-accessed"></a>Válaszol a különböző rosszindulatú IP érhető el
Bizonyos esetekben észlelhet egy potenciálisan kártékony IP-címet, amelyhez az egyik felügyelt számítógépről fértek hozzá:

![fenyegetésfelderítési leképezés](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig6.PNG)

Ezt és a kategóriába tartozó többi riasztást az OMS Security hozta létre a [Microsoft fenyegetésfelderítő](https://youtu.be/O4WtxgUrDc8) segítségével. A Fenyegetésfelderítő adatait a Microsoft maga gyűjti össze, illetve a vezető fenyegetésfelderítési szolgáltatóktól vásárolja. Ezeket az adatokat rendszeresen frissítik és a gyorsan változó fenyegetésekhez igazítják. A biztonsági riasztások [vizsgálata](https://blogs.technet.microsoft.com/msoms/2016/12/08/investigating-suspicious-activity-in-a-hybrid-cloud-with-oms-security/) közben az adatokat jellegükből adódóan érdemes összevonni más biztonságinformáció-forrásokkal. 

## <a name="customize-alerts-received-via-e-mail"></a>Figyelmeztetés érkezett e-mail testreszabása

Testre szabhatja, mely a szervezet felhasználói rendszer értesíti, ha a biztonsági figyelmeztetést OMS biztonsági. Ez a beállítás érhető el – áttekintés csoportban vagy az OMS-irányítópult-beállításokat:

![E-mail cím](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig7.png)

## <a name="see-also"></a>Lásd még:
Ebből a dokumentumból megtanulta, hogyan használható a **Fenyegetésfelderítési adataival** OMS biztonsági, naplózási megoldást beállítás válaszoljon a biztonsági riasztásokat. Az OMS által nyújtott védelemmel kapcsolatos további információkat a következő cikkek tartalmaznak:

* [Az Operations Management Suite (OMS) áttekintése](operations-management-suite-overview.md)
* [Ismerkedés az Operations Management Suite biztonsági és naplózási megoldás](oms-security-getting-started.md)
* [Az erőforrások figyelése az Operations Management Suite biztonsági és auditálási megoldásban](oms-security-monitoring-resources.md)

