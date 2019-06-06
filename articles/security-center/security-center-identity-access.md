---
title: Identitás és hozzáférés figyelése az Azure Security Centerben | Microsoft Docs
description: Itt megtudhatja, hogyan használható az Azure Security Center identitási és hozzáférési funkciója a felhasználók hozzáférési tevékenységeinek és identitással kapcsolatos problémáinak figyelésére.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2018
ms.author: monhaber
ms.openlocfilehash: 16548ae75567fa3ba6f8c9135d61945bd28d2db8
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428418"
---
# <a name="monitor-identity-and-access-in-azure-security-center-preview"></a>Identitás és hozzáférés az Azure Security Centerben (előzetes verzió) figyelése
Ez a cikk útmutatást nyújt a felhasználók identitási és hozzáférési tevékenységeinek az Azure Security Center segítségével történő figyeléséhez.

> [!NOTE]
> A "nézet *klasszikus* identitás és hozzáférés" hivatkozást a 2019. július 31-én kivezetjük. Kattintson a [Itt](security-center-features-retirement-july2019.md#menu_classicidentity) alternatív szolgáltatások további.

> [!NOTE]
> Identitás és hozzáférés figyelése az előzetes verzióban és csak a Security Center Standard szinten érhető el. A Security Center tarifacsomagjaival kapcsolatos további információért lásd a [díjszabást](security-center-pricing.md).
>

Az identitásnak kell lennie a vállalat vezérlősíkjának, és az identitás védelmének kell kapnia a legmagasabb prioritást. A biztonsági határt, egy identitás szegélyhálózat-alapú alakult az a hálózat pereme. Biztonsági kapcsolatban nyújt a hálózat védelmébe, és további információk az adatok védelme, valamint a biztonság, az alkalmazások és felhasználók kezelése válik. Napjainkban egyre több adat és alkalmazás kerül a felhőbe, így az identitás lép a szegélyhálózatok helyére.

Az identitástevékenységek figyelésével proaktív módon tud cselekedni, mielőtt egy incidens bekövetkezne, illetve reaktív tevékenységekkel leállíthatja a támadási kísérleteket. Az identitás és hozzáférés irányítópult nyújt ajánlásokat például:

- Az MFA engedélyezése az előfizetés kiemelt jogosultságú fiókjaiban
- Írási engedélyekkel rendelkező külső fiókok eltávolítása az előfizetésből
- Kiemelt jogosultságú külső fiókok eltávolítása az előfizetésből

> [!NOTE]
> Ha az előfizetés több mint 600 fiókkal rendelkezik, a Security Center nem tudja futtatni az identitás javaslatokat az előfizetésen. Javaslatok, amelyek nem futnak a "értékelések nem érhetők el" amelyet alatt vannak felsorolva.
A Security Center nem tud az identitás javaslatokat futtatásához egy Felhőszolgáltató (CSP) partner által létrehozott felügyeleti ügynökök.
>

Lásd: [javaslatok](security-center-identity-access.md#recommendations) a Security Center által biztosított identitás- és hozzáférés javaslatok listája.

## <a name="monitoring-security-health"></a>A biztonsági állapot figyelése
Az erőforrások biztonsági állapotát a figyelemmel kísérheti a **Security Center – áttekintés** irányítópultot. A **erőforrások** szakaszban az egyes erőforrástípusok súlyossági megjelenítő állapotjelző.

Megtekintheti az összes hibáit kiválasztásával **javaslatok**. A **erőforrások**, megtekintheti az adott számítási & alkalmazások, adatok biztonságát, hálózati, vagy identitás és hozzáférés a problémák listáját. Javaslatok alkalmazásával kapcsolatos további információkért lásd: [biztonsági javaslatok alkalmazása az Azure Security Center](security-center-recommendations.md).

Identitás és hozzáférés ajánlások teljes listáját lásd: [javaslatok](security-center-identity-access.md#recommendations).

A folytatáshoz válasszon ki **identitás és hozzáférés** alatt **erőforrások** vagy a Security Center főmenüjébe.

![A Security Center irányítópultja][1]

## <a name="monitor-identity-and-access"></a>Identitás és hozzáférés monitorozása
A **identitás és hozzáférés**, két lap található:

- **Áttekintés**: a Security Center által azonosított javaslatok.
- **Előfizetések**: az előfizetések és az egyes aktuális biztonsági állapotának listája.

![Identitás és hozzáférés][2]

### <a name="overview-section"></a>A szakasz áttekintése
A **áttekintése**, nincs javaslat listája. Az első oszlop a javaslatokat sorolja fel. A második oszlop az adott javaslat által érintett előfizetések teljes számát jeleníti meg. A harmadik oszlop a probléma súlyosságát mutatja.

1. Válasszon ki egy javaslatot. A javaslatban ablak nyílik meg, és megjeleníti:

   - A javaslat leírása
   - Nem megfelelő állapotú és kifogástalan állapotú előfizetések listáját
   - Lista, amelyek egy hibás értékelése miatt nem vizsgált erőforrások vagy erőforrás-előfizetéshez az ingyenes szinten fut, és nincs értékelve van

   ![A javaslat ablak][3]

1. További részleteket talál a listában, válasszon ki egy előfizetést.

### <a name="subscriptions-section"></a>Előfizetések szakasz
A **előfizetések**, nincs az előfizetések listáját. Az első oszlop az előfizetések listája. A második oszlop az egyes előfizetésekhez javaslatok teljes számát jeleníti meg. A harmadik oszlop tartalmazza a súlyossági szinten pedig a problémák.

![Az előfizetéshez tartozó lap][4]

1. Válasszon egy előfizetést. Összegzési nézetet megnyílik a három lappal:

   - **Javaslatok**: nem sikerült, a Security Center által végzett alapján.
   - **Sikeres értékelések**: megfelelt a Security Center által végzett listája.
   - **Értékelések nem érhetők el**: list-vizsgálat futtatása egy hiba miatt sikertelen volt, vagy mert az előfizetés több mint 600 fiókkal rendelkezik.

   A **javaslatok** minden javaslat súlyosságát és a kiválasztott előfizetéshez tartozó a javaslatok listája.

   ![Javaslatok az előfizetés kiválasztása][5]

1. Válasszon ki egy javaslatot, egy leírást a javaslat, a nem megfelelő állapotú és kifogástalan állapotú előfizetések listáját és a nem vizsgált erőforrások listáját.

   ![Javaslat leírása][6]

   A **értékelések átadott** sikeres értékelések listája.  Ezek az értékelések súlyosságát, mindig zöld.

   ![Sikeres értékelések][7]

1. Jelöljön ki egy átadott értékelés a lista az értékelés leírását és kifogástalan állapotú előfizetések listáját. Nincs egy nem megfelelő állapotú előfizetések szolgáló lap, amely felsorolja az összes olyan előfizetést, amely nem sikerült.

   ![Sikeres értékelések][8]

## <a name="recommendations"></a>Javaslatok
Referenciaként az alábbi táblázat segítségével segítenek megérteni a rendelkezésre álló identitás és hozzáférés ajánlásokat, és mindegyik funkciója alkalmazásuk esetén.

|Erőforrás típusa|Biztonsági pontszám|Ajánlás|Leírás|
|----|----|----|----|
|Előfizetés|50|MFA engedélyezni kell a fiókok az előfizetésben tulajdonosi engedélyekkel rendelkező|A multi-factor Authentication (MFA) engedélyezéséhez a fiókok vagy az erőforrások biztonsági incidenseinek megelőzése rendszergazdai jogosultságokkal rendelkező összes előfizetési fiókban.|
|Előfizetés|40|MFA engedélyezni kell a írási engedéllyel rendelkező előfizetés fiókjának|Engedélyezi a multi-factor Authentication (MFA) a fiókok vagy az erőforrások biztonsági incidenseinek megelőzése írási jogosultsággal rendelkező összes előfizetési fiókban.|
|Előfizetés|30|Tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből|Tulajdonosi engedélyekkel rendelkező külső fiókok eltávolítása az előfizetésből a nem monitorozott hozzáférések megelőzése céljából.|
|Előfizetés|30|MFA a az előfizetés olvasási engedélyekkel rendelkező fiókok engedélyezve kell lennie|A multi-factor Authentication (MFA) engedélyezéséhez a fiókok vagy az erőforrások biztonsági incidenseinek megelőzése olvasási jogosultságokkal rendelkező összes előfizetési fiókban.|
|Előfizetés|25|Írási rendelkező külső fiókok engedélyeit el kell távolítani az előfizetésből|Írási engedélyekkel rendelkező külső fiókok eltávolítása az előfizetésből a nem monitorozott hozzáférések megelőzése céljából. |
|Előfizetés|20|Tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből|Tulajdonosi engedélyekkel rendelkező elavult fiókok eltávolítása az előfizetések közül.|
|Előfizetés|5|Elavult fiókokat el kell távolítani az előfizetésből|Elavult fiókok eltávolítása az csak az aktuális felhasználó hozzáférésének engedélyezése az előfizetések közül. |
|Előfizetés|5|Meg kell adni az előfizetéshez hozzárendelt egynél több tulajdonosa|Több mint egy előfizetés-tulajdonost kijelölni a rendszergazdai hozzáférés redundanciájának biztosításához.|
|Előfizetés|5|Legfeljebb 3 tulajdonosok az előfizetéshez kell kijelölni|Kevesebb mint 3 előfizetés-tulajdonost kijelölni az esetleges illetéktelen behatolás feltört tulajdonosa.|
|Key Vault|5|Engedélyezni kell a Key Vault-diagnosztikai naplók|Naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül. |
|Előfizetés|15|Olvasási jogosultsággal rendelkező külső fiókokat el kell távolítani az előfizetésből|Az olvasási jogosultsággal rendelkező külső fiókok eltávolítása az előfizetésből a nem monitorozott hozzáférések megelőzése céljából.| 

> [!NOTE]
> Ha létrehozott egy feltételes hozzáférési szabályzatot, amely szükségessé teszi a többtényezős hitelesítés, de beállítása kizárásokkal rendelkezik, a Security Center MFA javaslat értékelés figyelembe veszi a szabályzat nem megfelelő, egyes felhasználók az Azure MFA nélkül bejelentkezni, mert.

## <a name="next-steps"></a>További lépések
Javaslatok, amelyek vonatkoznak a többi Azure-erőforrásokkal kapcsolatos további információkért tekintse meg a következőket:

- [A gépek és alkalmazások védelme az Azure Security Centerben](security-center-virtual-machine-recommendations.md)
- [Hálózat védelme az Azure Security Centerben](security-center-network-recommendations.md)
- [Az Azure SQL-szolgáltatás és az adatok az Azure Security Center védelme](security-center-sql-service-recommendations.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:
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
