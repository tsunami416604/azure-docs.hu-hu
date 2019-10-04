---
title: Adaptív alkalmazásvezérlők az Azure Security Centerben | Microsoft Docs
description: Ebből a dokumentumból megismerheti, hogyan használható az adaptív alkalmazásvezérlés az Azure Security Centerben az Azure-beli virtuális gépeken futó alkalmazások engedélyezési listákra való felvételéhez.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/02/2019
ms.author: memildin
ms.openlocfilehash: 46ab2fc5c796d960de8b1c5e3391a6356563b50a
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202814"
---
# <a name="adaptive-application-controls-in-azure-security-center"></a>Adaptív alkalmazásvezérlők az Azure Security Centerben
Az útmutató azt ismerteti, hogyan konfigurálható az alkalmazásvezérlés az Azure Security Centerben.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>Mire szolgálnak a Security Center adaptív alkalmazásvezérlői?
Az adaptív alkalmazás-vezérlés intelligens, automatizált, teljes körű megoldás a Azure Security Center, amely segítségével szabályozhatja, hogy mely alkalmazások futhatnak az Azure-ban és a nem Azure-beli virtuális gépeken (Windows és Linux). Más előnyök mellett ez segít megerősíteni a virtuális gépeket a kártevők ellen. Security Center a gépi tanulás használatával elemzi a virtuális gépeken futó alkalmazásokat, és létrehoz egy engedélyezési listát ebből az intelligenciával. Ez a funkció nagy mértékben leegyszerűsíti az alkalmazások engedélyezési listájának szabályzatának konfigurálását és karbantartását, így lehetővé teszi a következőket:

- A kártékony alkalmazások futtatására tett kísérletek blokkolása vagy riasztása, beleértve azokat is, amelyeket antimalware-megoldások okozhatnak.
- A cég vagy szervezet biztonsági házirendjének való megfelelést, amely csak a licencelt szoftverek használatát engedélyezi.
- A nem kívánt szoftverek adott környezetben való használatának megelőzését.
- Az elavult és nem támogatott alkalmazások futtatásának letiltását.
- A szervezetben nem engedélyezett szoftvereszközök használatának megelőzését.
- Azt, hogy az informatikai részleg szabályozhassa a bizalmas adatokhoz való hozzáférést az alkalmazások használata során.

> [!NOTE]
> Nem Azure-és Linux-alapú virtuális gépek esetén az adaptív alkalmazás-vezérlőelemek csak naplózási módban támogatottak.

## <a name="how-to-enable-adaptive-application-controls"></a>Az adaptív alkalmazásvezérlők engedélyezése
Az adaptív alkalmazás-vezérlők segítségével meghatározhatja azokat az alkalmazásokat, amelyek számára engedélyezett a virtuális gépek konfigurált csoportjain való futtatás. Ez a funkció az Azure és a nem Azure-beli Windows (minden verzió, klasszikus vagy Azure Resource Manager), valamint a Linux rendszerű virtuális gépek és kiszolgálók esetében érhető el. Az alkalmazás engedélyezési listája a következő lépésekkel konfigurálható:

1. Nyissa meg a **Security Center** irányítópultját.
2. A bal oldali panelen válassza ki az **Advanced cloud defense** (Speciális felhővédelem) szakaszban található **Adaptive application controls** (Adaptív alkalmazásvezérlők) elemet.

    ![Védelem](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png)

Megjelenik az **Adaptív alkalmazásvezérlők** oldal.

![controls](./media/security-center-adaptive-application/security-center-adaptive-application-fig2.png)

A **Virtuális gépek csoportjai** szakaszban három lap található:

* **Configured** (Konfigurált): Azon csoportok listája, amelyeken már konfigurálva van az alkalmazásvezérlés.
* **Recommended** (Ajánlott): Azon csoportok listája, amelyek esetében javasolt az alkalmazásvezérlés használata. A Security Center gépi tanulási módszerekkel azonosítja azokat a virtuális gépeket, amelyeken érdemes alkalmazásvezérlést beállítani, az alapján, hogy az adott virtuális gépek folyamatosan ugyanazokat az alkalmazásokat futtatják-e.
* **No recommendation** (Nincs javaslat): Azon csoportok listája, amelyek olyan virtuális gépeket tartalmaznak, amelyekhez nem tartoznak alkalmazásvezérlési javaslatok. például olyanokat, amelyeken az alkalmazások folyamatosan változnak, és még nem értek el egy stabil állapotot.

> [!NOTE]
> A Security Center saját fürtözési algoritmust használ virtuálisgép-csoportok létrehozásához, ezzel biztosítva, hogy a hasonló virtuális gépek a javasolt optimális alkalmazásvezérlési szabályzatot kapják meg.
>
>

### <a name="configure-a-new-application-control-policy"></a>Új alkalmazásvezérlési szabályzat konfigurálása
1. Kattintson a **Recommended** (Ajánlott) lapra azon csoportok listájáért, amelyeken javasolt bevezetni az alkalmazásvezérlést:

   ![Ajánlott](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

   A lista a következőket tartalmazza:

   - **Csoport neve**: Az előfizetés és a csoport neve
   - **Virtuális gépek és számítógépek**: A csoportban lévő virtuális gépek száma
   - **Állapot**: a javaslatok állapota
   - **Súlyosság**: a javaslatok súlyossági szintje

2. Kattintson egy csoportra az alkalmazás- **vezérlési szabályok létrehozása** lehetőség megnyitásához.

   ![Alkalmazásvezérlési szabályok](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)

3. A **virtuális gépek kiválasztása lapon**tekintse át az ajánlott virtuális gépek listáját, és törölje a jelölést, ha nem kívánja alkalmazni az alkalmazás engedélyezési szabályzatát a következőre:. Ezután két lista jelenik meg:

   - **Ajánlott alkalmazások**: azoknak az alkalmazásoknak a listája, amelyek gyakoriak a csoporton belüli virtuális gépeken, és amelyeket ajánlott engedélyezni a futtatáshoz.
   - **További alkalmazások**: azoknak az alkalmazásoknak a listája, amelyek kevésbé gyakoriak a csoporton belüli virtuális gépeken, vagy amelyek Exploitables néven ismertek (lásd alább), és felülvizsgálatra ajánlott.

4. Tekintse át az egyes listákon található alkalmazásokat, és törölje a jelölést azok mellől, amelyeket nem szeretne alkalmazni. A listák a következőket tartalmazzák:

   - **Név**: a tanúsítvány adatai vagy egy alkalmazás teljes elérési útja
   - **FILE TYPES** (Fájltípusok): az alkalmazás fájltípusa. Ez lehet az EXE, a script, az MSI vagy bármely ilyen típusú permutáció.
   - **Kihasználható**: a figyelmeztető ikon azt jelzi, hogy egy adott alkalmazást használhat-e egy támadó egy alkalmazás engedélyezési listájának megkerülésére. Érdemes áttekinteni ezeket az alkalmazásokat az engedélyezésük előtt.
   - **USERS** (Felhasználók): azok a felhasználók, akik számára javasolt az alkalmazás futtatásának engedélyezése

5. Ha végzett a kiválasztással, válassza a **Create** (Létrehozás) lehetőséget. <br>
   A Létrehozás gombra kattintva a Azure Security Center automatikusan létrehozza a megfelelő szabályokat a Windows-kiszolgálókon (AppLocker) elérhető beépített alkalmazás engedélyezési listájának megoldásához.

> [!NOTE]
> - A Security Centernek legalább kétheti adatra van szüksége ahhoz, hogy létrehozhassa az alapkonfigurációt, és feltölthesse a virtuálisgép-csoportok szerinti egyéni javaslatokat. A Security Center standard szintű változatának új ügyfelei számíthatnak arra, hogy a virtuálisgép-csoportjaik először a *nincs javaslat* lapon jelennek meg.
> - A Security Center Adaptív alkalmazásvezérlői nem támogatják az olyan virtuális gépeket, amelyekhez egy GPO vagy egy helyi biztonsági szabályzat már engedélyezett AppLocker-szabályzatot.
> -  Ajánlott biztonsági eljárásként a Security Center mindig létrehoz egy közzétevői szabályt az engedélyezett alkalmazások számára, és csak akkor, ha egy alkalmazás nem rendelkezik közzétevői adatokkal (azaz nincs aláírva), a rendszer létrehoz egy elérésiút-szabályt a teljes elérési úthoz. adott alkalmazás.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Alkalmazásvezérléssel konfigurált csoportok szerkesztése és monitorozása

1. Egy alkalmazás engedélyezési listájának használatára konfigurált csoport szerkesztéséhez és figyeléséhez térjen vissza az **adaptív alkalmazás-vezérlők** lapra, és válassza a **Konfigurálás** a **virtuális gépek csoportjai között**:

   ![Csoportok](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

   A lista a következőket tartalmazza:

   - **Csoport neve**: az előfizetés és a csoport neve
   - Virtuális gépek **és számítógépek**: a csoportban lévő virtuális gépek száma
   - **Mód**: A naplózási mód naplózza az engedélyezési listán nem szereplő alkalmazások futtatására tett kísérleteket; A kikényszerítés nem teszi lehetővé az alkalmazások futtatását, hacsak nem az engedélyezési listán vannak
   - **Riasztások**: az aktuális szabálysértések

2. Kattintson egy csoportra az **alkalmazás-ellenőrzési házirend szerkesztése** lapon végzett módosítások végrehajtásához.

   ![Védelem](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. A **Protection Mode** (Védelem módja) alatt a következő lehetőségek közül választhat:

   - **Audit** (Felügyelet): ebben a módban az alkalmazásvezérlési megoldás nem kényszeríti ki a szabályokat, és csak naplózza a tevékenységeket a védett virtuális gépeken. A használata olyan forgatókönyvek esetén javasolt, amikor először szeretné megfigyelni az általános működést, mielőtt blokkolná egy adott alkalmazás futtatását a cél virtuális gépen.
   - **Enforce** (Kényszerítés): ebben a módban az alkalmazásvezérlési megoldás kikényszeríti a szabályokat, és gondoskodik róla, hogy blokkolva legyenek azok az alkalmazások, amelyeknek a futtatása nem engedélyezett.

   > [!NOTE]
   > -  A védelmi mód betartatása a további értesítésig le van tiltva.
   > - Amint azt fent említettük, az új alkalmazásvezérlési szabályzatok alapértelmezés szerint minden esetben *Felügyelet* módban lesznek konfigurálva. 
   >

4. A **házirend-bővítmény**területen adja meg az összes olyan alkalmazás elérési útját, amelyet engedélyezni szeretne. Az elérési utak hozzáadása után Security Center frissíti az alkalmazás engedélyezési listájának házirendjét a virtuális gépek kiválasztott csoportján belüli virtuális gépeken, és létrehozza a megfelelő szabályokat ezeknek az alkalmazásoknak a már érvényben lévő szabályok mellett.

5. Tekintse át a **legutóbbi riasztások** szakaszban felsorolt jelenlegi szabálysértéseket. Kattintson az egyes sorokra, hogy átirányítsa a **riasztások** lapra Azure Security Centeron belül, és megtekintse a társított virtuális gépeken Azure Security Center által észlelt összes riasztást.
   - **Riasztások**: a naplózott szabálysértések.
   - **Nem. of VMS**: a riasztási típussal rendelkező virtuális gépek száma.

6. A **kiadói engedélyezési szabályok**, az **elérésiút-engedélyezési szabályok**és a **kivonatoló engedélyezési** szabályok szakaszban megtekintheti, hogy mely alkalmazás-engedélyezési szabályok vannak konfigurálva a csoporton belüli virtuális gépeken a szabály gyűjtemény típusa alapján. Az egyes szabályokhoz a következőket láthatja:

   - **Szabály**: Azok a paraméterek, amelyek alapján az AppLocker megvizsgálja az alkalmazást, hogy az alkalmazás futtatható-e.
   - **Fájl típusa**: Egy adott szabály által lefedett fájltípusok. Ez a következők bármelyike lehet: EXE, parancsfájl, MSI vagy bármely ilyen fájltípus.
   - **Felhasználók**: Azon felhasználók neve vagy száma, akik számára engedélyezett az alkalmazás engedélyezési szabálya által érintett alkalmazás futtatása.

   ![Engedélyezési szabályok](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

7. Az egyes sorok végén található három pontra kattintva törölheti az adott szabályt, vagy szerkesztheti az engedélyezett felhasználókat.

8. Az **adaptív alkalmazás-vezérlő** házirend módosítása után kattintson a **Mentés**gombra.

### <a name="not-recommended-list"></a>Nem ajánlott gépek listája

Security Center csak az alkalmazás-engedélyezési szabályzatokat javasolja a virtuális gépek számára, amelyek az alkalmazások stabil készleteit futtatják. Ha a virtuális gépeken az alkalmazások folyamatosan változnak, a rendszer nem hoz létre ajánlásokat.

![Ajánlás](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

A lista a következőket tartalmazza:
- **Csoport neve**: az előfizetés és a csoport neve
- Virtuális gépek **és számítógépek**: a csoportban lévő virtuális gépek száma

Azure Security Center lehetővé teszi az alkalmazás engedélyezési szabályzatának meghatározását a nem ajánlott virtuális gépeken is. Kövesse a korábban leírtaknak megfelelő elveket, és konfigurálja az alkalmazás engedélyezési szabályzatát ezeken a csoportokon is.

## <a name="move-a-vm-from-one-group-to-another"></a>Virtuális gép áthelyezése egyik csoportból a másikba

 Ha egy virtuális gépet egy csoportból egy másikba helyez át, a rendszer az alkalmazás-vezérlési házirendet annak a csoportnak a beállításaira módosítja, amelyre áthelyezte. A virtuális gépeket egy konfigurált csoportból nem konfigurált csoportba is áthelyezheti, amely a korábban a virtuális gépre alkalmazott alkalmazás-ellenőrzési házirend eltávolítását eredményezi.

 1. Az **adaptív alkalmazás vezérlői** lapon, a **konfigurált** lapon kattintson arra a csoportra, amelybe a jelenleg áthelyezni kívánt virtuális gép tartozik.
1. Kattintson **a konfigurált virtuális gépek és számítógépek**elemre.
1. Kattintson a virtuális gép sorában lévő három pontra az áthelyezéshez, majd kattintson az **Áthelyezés**elemre. Megnyílik a **számítógép áthelyezése másik csoportba** ablak.

    ![Védelem](./media/security-center-adaptive-application/adaptive-application-move-group.png)

 1. Válassza ki azt a csoportot, amelyre át szeretné helyezni a virtuális gépet, majd kattintson a **számítógép áthelyezése**gombra, és kattintson a **Mentés**gombra.

    ![Védelem](./media/security-center-adaptive-application/adaptive-application-move-group2.png)

 > [!NOTE]
> A **számítógép áthelyezése**gombra kattintás után kattintson a **Mentés** gombra. Ha nem kattint a **Save (Mentés**) gombra, akkor a számítógép nem lesz áthelyezve.

## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtudhatta, hogyan használhatja az adaptív alkalmazások vezérlését Azure Security Center az Azure-ban és a nem Azure-beli virtuális gépeken futó alkalmazások engedélyezési listájának használatához. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). A Security Center-riasztások kezelését és a biztonsági eseményekre való válaszadást ismertető útmutató.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md). Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [Az Azure Security Center biztonsági riasztásainak megismerése](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). A különböző típusú biztonsági riasztásokat ismertető útmutató.
* [Azure Security Center – Hibaelhárítási útmutató](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). A Security Center gyakori problémáinak elhárítását ismereti.
* [Azure Security Center – gyakori kérdések](security-center-faq.md) Gyakori kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
