---
title: Identitás és hozzáférés figyelése az Azure Security Centerben | Microsoft Docs
description: Itt megtudhatja, hogyan használható az Azure Security Center identitási és hozzáférési funkciója a felhasználók hozzáférési tevékenységeinek és identitással kapcsolatos problémáinak figyelésére.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2018
ms.author: memildin
ms.openlocfilehash: 8b069bedd3c36f27828e54a1110443ae6dfcdf3f
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268862"
---
# <a name="monitor-identity-and-access-in-azure-security-center-preview"></a>Identitás és hozzáférés figyelése Azure Security Centerban (előzetes verzió)
Ez a cikk útmutatást nyújt a felhasználók identitási és hozzáférési tevékenységeinek az Azure Security Center segítségével történő figyeléséhez.

> [!NOTE]
> Az identitás és a hozzáférés figyelése előzetes verzióban érhető el, és csak a standard szintű Security Center érhető el. A Security Center tarifacsomagjaival kapcsolatos további információért lásd a [díjszabást](security-center-pricing.md).
>

Az identitásnak kell lennie a vállalat vezérlősíkjának, és az identitás védelmének kell kapnia a legmagasabb prioritást. A biztonsági szegély egy hálózati kerületből lett kialakulóban egy identitási peremhálózaton. A biztonság kevesebb információt nyújt a hálózat védelméről és az adatai védelméről, valamint az alkalmazások és a felhasználók biztonságának kezeléséről. Napjainkban egyre több adat és alkalmazás kerül a felhőbe, így az identitás lép a szegélyhálózatok helyére.

Az identitástevékenységek figyelésével proaktív módon tud cselekedni, mielőtt egy incidens bekövetkezne, illetve reaktív tevékenységekkel leállíthatja a támadási kísérleteket. Az Identity & Access irányítópult a következőkhöz nyújt javaslatokat:

- Az MFA engedélyezése az előfizetés kiemelt jogosultságú fiókjaiban
- Írási engedélyekkel rendelkező külső fiókok eltávolítása az előfizetésből
- Kiemelt jogosultságú külső fiókok eltávolítása az előfizetésből

> [!NOTE]
> Ha az előfizetése több mint 600-fiókkal rendelkezik, Security Center nem tudja futtatni az identitással kapcsolatos ajánlásokat az előfizetésében. A nem futtatott javaslatok az alább ismertetett "nem elérhető értékelések" alatt találhatók.
A Security Center nem tudja futtatni az identitásra vonatkozó javaslatokat a Cloud Solution Provider (CSP) partner rendszergazdai ügynökeit.
>

A Security Center által biztosított identitás-és hozzáférési javaslatok listáját a [javaslatok](security-center-identity-access.md#recommendations) részben tekintheti meg.

## <a name="monitoring-security-health"></a>A biztonsági állapot figyelése
Az erőforrások biztonsági állapotát a figyelemmel kísérheti a **Security Center – áttekintés** irányítópultot. Az **erőforrások** szakasz egy állapotjelző, amely az egyes erőforrástípusok súlyosságát mutatja.

Megtekintheti az összes hibáit kiválasztásával **javaslatok**. Az **erőforrások**területen megtekintheti a számítási & alkalmazások, az adatbiztonság, a hálózatkezelés vagy az identitás & elérésére vonatkozó problémák listáját. Javaslatok alkalmazásával kapcsolatos további információkért lásd: [biztonsági javaslatok alkalmazása az Azure Security Center](security-center-recommendations.md).

Az identitás-és hozzáférési javaslatok teljes listáját a [javaslatok](security-center-identity-access.md#recommendations)című részben tekintheti meg.

A folytatáshoz válassza az **identitás & hozzáférés** az **erőforrások** vagy a Security Center főmenü lehetőséget.

![A Security Center irányítópultja][1]

## <a name="monitor-identity-and-access"></a>Identitás és hozzáférés monitorozása
Az **identitás & hozzáférés**alatt két lap található:

- **Áttekintés**: Security Center által azonosított javaslatok.
- Előfizetések: az előfizetések és a jelenlegi biztonsági állapotok listája.

![Identitás és hozzáférés][2]

### <a name="overview-section"></a>Áttekintés szakasz
Az **Áttekintés**területen a javaslatok listája szerepel. Az első oszlop a javaslatokat sorolja fel. A második oszlop az adott javaslat által érintett előfizetések teljes számát jeleníti meg. A harmadik oszlop a probléma súlyosságát mutatja.

1. Válasszon egy javaslatot. Megnyílik a javaslatok ablak, amely a következőket jeleníti meg:

   - A javaslat leírása
   - Sérült és kifogástalan állapotú előfizetések listája
   - A sikertelen értékelés miatt nem ellenőrzött erőforrások listája, vagy az erőforrás az ingyenes szinten futó előfizetéshez tartozik, és nincs értékelve.

   ![Javaslat ablaka][3]

1. További részletekért válasszon egy előfizetést a listában.

### <a name="subscriptions-section"></a>Előfizetések szakasz
Azelőfizetések alatt található az előfizetések listája. Az első oszlop felsorolja az előfizetéseket. A második oszlop az egyes előfizetésekhez tartozó javaslatok teljes számát jeleníti meg. A harmadik oszlop a problémák súlyosságát mutatja.

![Előfizetések lap][4]

1. Válasszon egy előfizetést. Összegzési nézetet megnyílik a három lappal:

   - **Javaslatok**: nem sikerült, a Security Center által végzett alapján.
   - **Sikeres értékelések**: megfelelt a Security Center által végzett listája.
   - Nem **elérhető értékelések**: azon értékelések listája, amelyeket egy hiba miatt nem sikerült futtatni, vagy mert az előfizetés több mint 600 fiókot tartalmaz.

   A **javaslatok** szakaszban a kiválasztott előfizetésre és az egyes javaslatok súlyosságára vonatkozó javaslatok szerepelnek.

   ![Javaslatok az előfizetés kiválasztására][5]

1. Válassza ki a javaslat leírását, a nem kifogástalan állapotú és az egészséges előfizetések listáját, valamint a nem ellenőrzött erőforrások listáját.

   ![Javaslat leírása][6]

   A **értékelések átadott** sikeres értékelések listája.  Ezek az értékelések súlyosságát, mindig zöld.

   ![Sikeres értékelések][7]

1. Válasszon ki egy átadott értékelést a listából az értékelés leírásának és az egészséges előfizetések listájának megtekintéséhez. A nem megfelelő állapotú előfizetések lapján található egy lap, amely felsorolja az összes sikertelen előfizetést.

   ![Sikeres értékelések][8]

## <a name="recommendations"></a>Javaslatok
Az alábbi táblázat hivatkozásként használható, amely segít megérteni az elérhető identitást & hozzáférési javaslatokat, valamint azt, hogy mit tesz, ha alkalmazza.

|Erőforrás típusa|Biztonsági pontszám|Ajánlás|Leírás|
|----|----|----|----|
|Subscription|50|Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon|A többtényezős hitelesítés (MFA) engedélyezése minden olyan előfizetési fióknál, amely rendszergazdai jogosultságokkal rendelkezik a fiókok vagy erőforrások megszegésének megakadályozásához.|
|Subscription|40|Az MFA-t az írási engedélyekkel rendelkező előfizetési fiókokban kell engedélyezni|A többtényezős hitelesítés (MFA) engedélyezése az írási jogosultságokkal rendelkező előfizetési fiókok számára a fiókok vagy erőforrások megszegésének megakadályozása érdekében.|
|Subscription|30|A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből|A nem figyelt hozzáférés megakadályozása érdekében távolítsa el a tulajdonosi engedélyekkel rendelkező külső fiókokat az előfizetésből.|
|Subscription|30|Az MFA-nak engedélyezve kell lennie az előfizetési fiókokban olvasási engedélyekkel|A többtényezős hitelesítés (MFA) engedélyezése az olvasási jogosultságokkal rendelkező előfizetési fiókok esetében a fiókok vagy erőforrások megszegésének megelőzése érdekében.|
|Subscription|25|Írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből|A nem figyelt hozzáférés megakadályozása érdekében távolítsa el az előfizetésből származó írási engedélyekkel rendelkező külső fiókokat. |
|Subscription|20|A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből|A tulajdonosi engedélyekkel rendelkező elavult fiókok eltávolítása az előfizetésből.|
|Subscription|5|Az elavult fiókokat el kell távolítani az előfizetésből|Az elavult fiókok eltávolítása az előfizetésből, hogy csak az aktuális felhasználók férhessenek hozzá. |
|Subscription|5|Az előfizetéshez egynél több tulajdonos rendelhető hozzá|Egynél több előfizetés-tulajdonost jelölhet ki a rendszergazdai hozzáférés redundancia érdekében.|
|Subscription|5|Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni|Kevesebb mint 3 előfizetés-tulajdonost jelölhet ki, hogy csökkentse a feltört tulajdonos általi illetéktelen behatolás lehetőségét.|
|Kulcstartó|5|A Key Vault diagnosztikai naplóit engedélyezni kell|Naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül. |
|Subscription|15|Az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből|A nem figyelt hozzáférés megakadályozása érdekében távolítsa el a külső fiókokat olvasási jogosultságokkal az előfizetésből.| 

> [!NOTE]
> Ha olyan feltételes hozzáférési szabályzatot hozott létre, amely MFA-t igényel, de kizárások vannak beállítva, akkor a Security Center MFA ajánlási felmérése nem megfelelőnek tekinti a szabályzatot, mivel lehetővé teszi egyes felhasználók számára, hogy MFA nélkül jelentkezzenek be az Azure-ba.

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni a más Azure-erőforrásokra vonatkozó javaslatokról, tekintse meg a következő cikkeket:

- [A gépek és alkalmazások védelme az Azure Security Centerben](security-center-virtual-machine-protection.md)
- [Hálózat védelme az Azure Security Centerben](security-center-network-recommendations.md)
- [Az Azure SQL-szolgáltatás és-adatok védelme Azure Security Center](security-center-sql-service-recommendations.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következő cikkeket:
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). A Security Center-riasztások kezelését és a biztonsági eseményekre való válaszadást ismertető útmutató.
* [Az Azure Security Center biztonsági riasztásainak megismerése](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). A különböző típusú biztonsági riasztásokat ismertető útmutató.
* [Azure Security Center – gyakori kérdések](security-center-faq.md) Választ találhat a Security Center használatával kapcsolatos gyakori kérdésekre.


<!--Image references-->
[1]: ./media/security-center-identity-access/overview.png
[2]: ./media/security-center-identity-access/identity-dashboard.png
[3]: ./media/security-center-identity-access/select-subscription.png
[4]: ./media/security-center-identity-access/subscriptions.png
[5]: ./media/security-center-identity-access/recommendations.png
[6]: ./media/security-center-identity-access/designate.png
[7]: ./media/security-center-identity-access/passed-assessments.png
[8]: ./media/security-center-identity-access/remove.png
