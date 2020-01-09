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
ms.date: 12/19/2019
ms.author: memildin
ms.openlocfilehash: 6b262baddd10c9d0dff4b196b733972b97d99872
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552984"
---
# <a name="monitor-identity-and-access-preview"></a>Identitás és hozzáférés figyelése (előzetes verzió)
Ha Security Center észleli a potenciális biztonsági réseket, javaslatokat hoz létre, amelyek végigvezetik a szükséges vezérlők konfigurálásának lépésein az erőforrások megerősítéséhez és védelméhez.

Ez a cikk a Azure Security Center erőforrás-biztonsági szakaszának **identitás-és elérési** oldalát ismerteti.

Az oldalon megjelenő javaslatok teljes listáját az [identitás-és hozzáférési javaslatok](recommendations-reference.md#recs-identity)című részben tekintheti meg.

> [!NOTE]
> Az identitás és a hozzáférés figyelése előzetes verzióban érhető el, és csak a standard szintű Security Center érhető el. A Security Center tarifacsomagjaival kapcsolatos további információért lásd a [díjszabást](security-center-pricing.md).
>

Az identitásnak a vállalat vezérlő síkja kell lennie, és az identitások védelme elsődleges prioritásnak kell lennie. A biztonsági szegély egy hálózati kerületből lett kialakulóban egy identitási peremhálózaton. A biztonság kevesebb információt nyújt a hálózat védelméről és az adatai védelméről, valamint az alkalmazások és a felhasználók biztonságának kezeléséről. Napjainkban egyre több adat és alkalmazás kerül a felhőbe, így az identitás lép a szegélyhálózatok helyére.

Az identitástevékenységek figyelésével proaktív módon tud cselekedni, mielőtt egy incidens bekövetkezne, illetve reaktív tevékenységekkel leállíthatja a támadási kísérleteket. Az Identity & Access irányítópult a következőkhöz nyújt javaslatokat:

- Az MFA engedélyezése az előfizetés kiemelt jogosultságú fiókjaiban
- Írási engedélyekkel rendelkező külső fiókok eltávolítása az előfizetésből
- Kiemelt jogosultságú külső fiókok eltávolítása az előfizetésből

> [!NOTE]
> Ha az előfizetése több mint 600-fiókkal rendelkezik, Security Center nem tudja futtatni az identitással kapcsolatos ajánlásokat az előfizetésében. A nem futtatott javaslatok az alábbi "nem elérhető értékelések" alatt találhatók.
A Security Center nem tudja futtatni az identitásra vonatkozó javaslatokat a Cloud Solution Provider (CSP) partner rendszergazdai ügynökeit.
>

## <a name="monitor-identity-and-access"></a>Identitás és hozzáférés monitorozása

Nyissa meg az azonosított identitás-és elérési problémák listáját az **identity & Access** ( **erőforrások**alatt) vagy az Áttekintés lapon lévő Security Center oldalsávon. 

Az **identitás & hozzáférés**alatt két lap található:

- **Áttekintés**: Security Center által azonosított javaslatok.
- **Előfizetések**: az előfizetések és a jelenlegi biztonsági állapotok listája.

[![Identity & hozzáférés](./media/security-center-identity-access/identity-dashboard.png)](./media/security-center-identity-access/identity-dashboard.png#lightbox)

### <a name="overview-section"></a>Áttekintő szakasz
Az **Áttekintés**területen a javaslatok listája szerepel. Az első oszlop a javaslatokat sorolja fel. A második oszlop az adott javaslat által érintett előfizetések teljes számát jeleníti meg. A harmadik oszlop a probléma súlyosságát mutatja.

1. Válasszon egy javaslatot. Megnyílik a javaslatok ablak, amely a következőket jeleníti meg:

   - A javaslat leírása
   - Sérült és kifogástalan állapotú előfizetések listája
   - A sikertelen értékelés miatt nem ellenőrzött erőforrások listája, vagy az erőforrás az ingyenes szinten futó előfizetéshez tartozik, és nincs értékelve.

    [![javaslatok ablak](./media/security-center-identity-access/select-subscription.png)](./media/security-center-identity-access/select-subscription.png#lightbox)

1. További részletekért válasszon egy előfizetést a listában.

### <a name="subscriptions-section"></a>Előfizetések szakasz
Az **előfizetések**alatt található az előfizetések listája. Az első oszlop felsorolja az előfizetéseket. A második oszlop az egyes előfizetésekhez tartozó javaslatok teljes számát jeleníti meg. A harmadik oszlop a problémák súlyosságát mutatja.

[![-előfizetések lap](./media/security-center-identity-access/subscriptions.png)](./media/security-center-identity-access/subscriptions.png#lightbox)

1. Válasszon előfizetést. Megnyílik egy összefoglaló nézet három lapból:

   - **Javaslatok**: a sikertelen Security Center által végrehajtott értékelések alapján.
   - **Átadott értékelések**: az átadott Security Center által elvégzett értékelések listája.
   - Nem **elérhető értékelések**: azon értékelések listája, amelyeket egy hiba miatt nem sikerült futtatni, vagy mert az előfizetés több mint 600 fiókot tartalmaz.

   A **javaslatok** szakaszban a kiválasztott előfizetésre és az egyes javaslatok súlyosságára vonatkozó javaslatok szerepelnek.

   [az előfizetés kiválasztására vonatkozó javaslatok ![](./media/security-center-identity-access/recommendations.png)](./media/security-center-identity-access/recommendations.png#lightbox)

1. Válassza ki a javaslat leírását, a nem kifogástalan állapotú és az egészséges előfizetések listáját, valamint a nem ellenőrzött erőforrások listáját.

   [![javaslat leírása](./media/security-center-identity-access/designate.png)](./media/security-center-identity-access/designate.png#lightbox)

   Az **átadott értékelések** területen az átadott értékelések listája szerepel.  Az értékelések súlyossága mindig zöld.

   [![átadott értékelések](./media/security-center-identity-access/passed-assessments.png)](./media/security-center-identity-access/passed-assessments.png#lightbox)

1. Válasszon ki egy átadott értékelést a listából az értékelés leírásának és az egészséges előfizetések listájának megtekintéséhez. A nem megfelelő állapotú előfizetések lapján található egy lap, amely felsorolja az összes sikertelen előfizetést.

   [![átadott értékelések](./media/security-center-identity-access/remove.png)](./media/security-center-identity-access/remove.png#lightbox)

> [!NOTE]
> Ha olyan feltételes hozzáférési szabályzatot hozott létre, amely MFA-t igényel, de kizárások vannak beállítva, akkor a Security Center MFA ajánlási felmérése nem megfelelőnek tekinti a szabályzatot, mivel lehetővé teszi egyes felhasználók számára, hogy MFA nélkül jelentkezzenek be az Azure-ba.

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni a más Azure-erőforrásokra vonatkozó javaslatokról, tekintse meg a következő cikkeket:

- [A gépek és alkalmazások védelme az Azure Security Centerben](security-center-virtual-machine-protection.md)
- [Hálózat védelme az Azure Security Centerben](security-center-network-recommendations.md)
- [Az Azure SQL-szolgáltatás és-adatok védelme Azure Security Center](security-center-sql-service-recommendations.md)