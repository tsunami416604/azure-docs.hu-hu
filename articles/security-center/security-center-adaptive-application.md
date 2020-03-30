---
title: Adaptív alkalmazásvezérlők az Azure Security Centerben
description: Ez a dokumentum segít az Azure Security Center adaptív alkalmazásvezérlésének használatában az Azure-gépeken futó alkalmazások engedélyezési listájának listázásához.
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
ms.date: 12/23/2019
ms.author: memildin
ms.openlocfilehash: 1dc94c5ec08cc27fb1819ccc16fd766c62aad796
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604675"
---
# <a name="adaptive-application-controls"></a>Adaptív alkalmazásvezérlők
Az útmutató azt ismerteti, hogyan konfigurálható az alkalmazásvezérlés az Azure Security Centerben.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>Mire szolgálnak a Security Center adaptív alkalmazásvezérlői?
Az adaptív alkalmazásvezérlés az Azure Security Center intelligens, automatizált, végpontok között futó megoldása, amely segít annak szabályozásában, hogy mely alkalmazások futtathatók azure-beli és nem Azure-os gépeken (Windows és Linux). Egyéb előnyök mellett ez segít megkeményíteni a gépeket a rosszindulatú programokkal szemben. A Security Center gépi tanulással elemzi a gépeken futó alkalmazásokat, és létrehoz egy engedélyezési listát ebből az intelligenciából. Ez a funkció nagymértékben leegyszerűsíti az alkalmazásengedélyezési listaházirendek konfigurálásának és karbantartásának folyamatát, lehetővé téve a következőket:

- Letilthatja vagy riasztást adhat meg a kártékony alkalmazások futtatására tett kísérletekről, beleértve azokat is, amelyeket egyébként a kártevőirtó megoldások nem hiányolhatnak.
- A cég vagy szervezet biztonsági házirendjének való megfelelést, amely csak a licencelt szoftverek használatát engedélyezi.
- A nem kívánt szoftverek adott környezetben való használatának megelőzését.
- Az elavult és nem támogatott alkalmazások futtatásának letiltását.
- A szervezetben nem engedélyezett szoftvereszközök használatának megelőzését.
- Azt, hogy az informatikai részleg szabályozhassa a bizalmas adatokhoz való hozzáférést az alkalmazások használata során.

> [!NOTE]
> Nem Azure- és Linux-alapú gépek esetén az adaptív alkalmazásvezérlők csak vizsgálati módban támogatottak.

## <a name="how-to-enable-adaptive-application-controls"></a>Az adaptív alkalmazásvezérlők engedélyezése

Az adaptív alkalmazásvezérlők segítségével meghatározhatja a konfigurált gépcsoportokon futtatható alkalmazások készletét. Ez a funkció azure-beli és nem Azure-os Windows (összes verzió, klasszikus vagy Azure Resource Manager) és Linux rendszerű gépekhez is elérhető. Az alkalmazásengedélyezési listák konfigurálásához kövesse az alábbi lépéseket:

1. Nyissa meg a **Security Center** irányítópultját.

1. A bal oldali panelen válassza ki az **Advanced cloud defense** (Speciális felhővédelem) szakaszban található **Adaptive application controls** (Adaptív alkalmazásvezérlők) elemet.

    [![Védelem](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png)](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png#lightbox)

Megjelenik az **Adaptív alkalmazásvezérlők** oldal.

![vezérlők](./media/security-center-adaptive-application/security-center-adaptive-application-fig2.png)

A **Virtuális gépek csoportjai** szakaszban három lap található:

* **Configured** (Konfigurált): Azon csoportok listája, amelyeken már konfigurálva van az alkalmazásvezérlés.
* **Recommended** (Ajánlott): Azon csoportok listája, amelyek esetében javasolt az alkalmazásvezérlés használata. A Security Center gépi tanulási módszerekkel azonosítja azokat a virtuális gépeket, amelyeken érdemes alkalmazásvezérlést beállítani, az alapján, hogy az adott virtuális gépek folyamatosan ugyanazokat az alkalmazásokat futtatják-e.
* **No recommendation** (Nincs javaslat): Azon csoportok listája, amelyek olyan virtuális gépeket tartalmaznak, amelyekhez nem tartoznak alkalmazásvezérlési javaslatok. például olyanokat, amelyeken az alkalmazások folyamatosan változnak, és még nem értek el egy stabil állapotot.

> [!NOTE]
> A Security Center saját fürtözési algoritmust használ virtuálisgép-csoportok létrehozásához, ezzel biztosítva, hogy a hasonló virtuális gépek a javasolt optimális alkalmazásvezérlési szabályzatot kapják meg.
>
>

### <a name="configure-a-new-application-control-policy"></a>Új alkalmazásvezérlési szabályzat konfigurálása

1. Válassza az **Ajánlott** lapot az alkalmazásvezérlési javaslatokkal rendelkező csoportok listájához:

   ![Ajánlott](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

   A lista a következőket tartalmazza:

   - **Csoport név**: Az előfizetés és a csoport neve
   - **Virtuális gépek és számítógépek:** A csoportban lévő virtuális gépek száma
   - **Állam**: az ajánlások állapota
   - **Súlyosság:** az ajánlások súlyossági szintje

2. Kattintson egy csoportra az **Alkalmazásvezérlési szabályok létrehozása** beállítás megnyitásához.

   [![Alkalmazásvezérlési szabályok](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png#lightbox)

3. A **virtuális gépek kiválasztása,** tekintse át az ajánlott virtuális gépek listáját, és törölje a jelet azon, hogy nem szeretne alkalmazásengedélyezési házirendet alkalmazni. Ezután két lista jelenik meg:

   - **Ajánlott alkalmazások:** a csoporton belüli virtuális gépeken gyakori alkalmazások listája, amelyek futtatását ajánlott engedélyezni.
   - **További alkalmazások:** az alkalmazások listája, amelyek vagy kevésbé gyakoriak a csoporton belüli virtuális gépeken, vagy amelyek exploitables néven ismertek (lásd alább), és véleményezésre ajánlottak.

4. Tekintse át az egyes listákon található alkalmazásokat, és törölje a jelölést azok mellől, amelyeket nem szeretne alkalmazni. A listák a következőket tartalmazzák:

   - **NÉV**: az alkalmazás tanúsítványadatai vagy teljes elérési útja
   - **FILE TYPES** (Fájltípusok): az alkalmazás fájltípusa. Ez lehet EXE, Script, MSI, vagy ezek a típusok bármely permutációja.
   - **EXPLOITABLE**: figyelmeztető ikon jelzi, ha egy adott alkalmazást a támadó az alkalmazásengedélyezési lista megkerülésére használhat. Érdemes áttekinteni ezeket az alkalmazásokat az engedélyezésük előtt.
   - **USERS** (Felhasználók): azok a felhasználók, akik számára javasolt az alkalmazás futtatásának engedélyezése

5. Ha végzett a kiválasztással, válassza a **Create** (Létrehozás) lehetőséget. <br>
   Miután a Létrehozás lehetőséget választja, az Azure Security Center automatikusan létrehozza a megfelelő szabályokat a Windows-kiszolgálókon (AppLocker) elérhető beépített alkalmazásengedélyezési lista megoldáson felül.

> [!NOTE]
> - A Security Centernek legalább kétheti adatra van szüksége ahhoz, hogy létrehozhassa az alapkonfigurációt, és feltölthesse a virtuálisgép-csoportok szerinti egyéni javaslatokat. A Security Center standard szintű változatának új ügyfelei számíthatnak arra, hogy a virtuálisgép-csoportjaik először a *nincs javaslat* lapon jelennek meg.
> - A Security Center Adaptív alkalmazásvezérlői nem támogatják az olyan virtuális gépeket, amelyekhez egy GPO vagy egy helyi biztonsági szabályzat már engedélyezett AppLocker-szabályzatot.
> -  Biztonsági tanácsként a Security Center mindig megpróbál közzétevői szabályt létrehozni az engedélyezendőként kiválasztott alkalmazásokhoz, és csak akkor, ha egy alkalmazás nem rendelkezik közzétevői adatokkal (más néven nincs aláírva), a program elérési útszabályt hoz létre a program teljes elérési útjához. adott alkalmazás.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Alkalmazásvezérléssel konfigurált csoportok szerkesztése és monitorozása

1. Ha egy alkalmazással konfigurált csoport engedélyezési listaházirenddel szeretne szerkesztést és figyelést, térjen vissza az **Adaptív alkalmazásvezérlők** lapra, és válassza a **KONFIGURÁLVA** lehetőséget **a Virtuálisgép-csoportok csoportban:**

   ![Csoportok](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

   A lista a következőket tartalmazza:

   - **Csoport név**: az előfizetés és a csoport neve
   - **Virtuális gépek és számítógépek:** a csoportban lévő virtuális gépek száma
   - **Mód**: A vizsgálati mód naplózza az engedélyezési listán nem szereplő alkalmazások futtatására tett kísérleteket; Az kényszerítés csak akkor engedélyezi az alkalmazások futtatását, ha szerepelnek az engedélyezési listán
   - **Figyelmeztetések**: az aktuális jogsértések

2. Kattintson egy csoportra az **alkalmazásvezérlési házirend szerkesztése** lapon végzett módosításokhoz.

   ![Védelem](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. A **Protection Mode** (Védelem módja) alatt a következő lehetőségek közül választhat:

   - **Audit** (Felügyelet): ebben a módban az alkalmazásvezérlési megoldás nem kényszeríti ki a szabályokat, és csak naplózza a tevékenységeket a védett virtuális gépeken. A használata olyan forgatókönyvek esetén javasolt, amikor először szeretné megfigyelni az általános működést, mielőtt blokkolná egy adott alkalmazás futtatását a cél virtuális gépen.
   - **Enforce** (Kényszerítés): ebben a módban az alkalmazásvezérlési megoldás kikényszeríti a szabályokat, és gondoskodik róla, hogy blokkolva legyenek azok az alkalmazások, amelyeknek a futtatása nem engedélyezett.

   > [!NOTE]
   > -  A védelmi mód **kényszerítése** további értesítésig le van tiltva.
   > - Amint azt fent említettük, az új alkalmazásvezérlési szabályzatok alapértelmezés szerint minden esetben *Felügyelet* módban lesznek konfigurálva. 
   >

4. A **Házirend bővítmény csoportban**adja meg az engedélyezni kívánt alkalmazáselérési utat. Miután hozzáadja ezeket az elérési utakat, a Security Center frissíti az alkalmazás lehetővé teszi a virtuális gépek listaházirenda virtuális gépek en a kiválasztott vms csoporton belül, és létrehozza a megfelelő szabályokat ezekhez az alkalmazásokhoz, a már meglévő szabályok mellett.

5. Tekintse át a Legutóbbi értesítések szakaszban felsorolt aktuális **szabálysértéseket.** Kattintson az egyes sorokra az Azure Security Center **riasztások** lapjára való átirányításához, és tekintse meg az Azure Security Center által a társított virtuális gépeken észlelt összes riasztást.
   - **Riasztások:** a naplózott szabálysértések.
   - **. virtuális gépek:** az ilyen riasztási típussal rendelkező virtuális gépek száma.

6. A **Közzétevő engedélyezési szabályai**, Path engedélyezési **szabályok**és kivonatengedélyezési szabályok alatt **láthatja,** hogy mely alkalmazásengedélyezési szabályok vannak jelenleg konfigurálva a csoporton belüli virtuális gépeken, a szabálygyűjtemény típusának megfelelően. Minden szabályhoz a következőket láthatja:

   - **Szabály**: Azok a paraméterek, amelyek alapján az AppLocker megvizsgálja az alkalmazást annak megállapítására, hogy egy alkalmazás futtatható-e.
   - **Fájltípus**: Az adott szabály hatálya alá tartozó fájltípusok. Ez a következő lehet: EXE, Script, MSI vagy a fájltípusok bármely permutációja.
   - **Felhasználók**: Azon felhasználók neve vagy száma, akik futtathatnak egy alkalmazásengedélyezési szabály hatálya alá tartozó alkalmazást.

   ![Engedélyezési szabályok](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

7. Kattintson az egyes sorok végén található három ponton, ha törölni szeretné az adott szabályt, vagy szeretné szerkeszteni az engedélyezett felhasználókat.

8. Miután módosította az **Adaptív alkalmazásvezérlők házirendjét,** kattintson a **Mentés gombra.**

### <a name="not-recommended-list"></a>Nem ajánlott gépek listája

A Security Center csak a stabil alkalmazáskészletet futtató virtuális gépek alkalmazásengedélyezési házirendjeit javasolja. Ha a virtuális gépeken az alkalmazások folyamatosan változnak, a rendszer nem hoz létre ajánlásokat.

![Ajánlás](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

A lista a következőket tartalmazza:
- **Csoport név**: az előfizetés és a csoport neve
- **Virtuális gépek és számítógépek:** a csoportban lévő virtuális gépek száma

Az Azure Security Center lehetővé teszi, hogy egy alkalmazás engedélyezési házirend et a virtuális gépek nem ajánlott csoportjain is definiáljon. Kövesse ugyanazokat az elveket, mint korábban leírt, hogy konfigurálja az alkalmazás engedélyezési házirend et ezeken a csoportokon is.

## <a name="move-a-vm-from-one-group-to-another"></a>Virtuális gép áthelyezése egyik csoportból a másikba

 Amikor áthelyez egy virtuális gép egyik csoportból a másikba, az alkalmazásvezérlő házirend, amely et alkalmazott, a csoport beállításaira változik, ahhoz a csoporthoz, ahhoz, amelybe áthelyezte. A virtuális gép egy konfigurált csoportból egy nem konfigurált csoportba is áthelyezhető, ami a virtuális gépre korábban alkalmazott alkalmazásvezérlő házirendek eltávolítását eredményezi.

 1. Az **Adaptív alkalmazásvezérlők** lapon a **CONFIGURED** lapon kattintson arra a csoportra, amelyhez a virtuális gép jelenleg tartozik.
1. Kattintson **a Konfigurált virtuális gépek és számítógépek elemre.**
1. Az áthelyezéshez kattintson a virtuális gép sorában lévő három képre, majd az **Áthelyezés**gombra. Megnyílik **a Számítógép áthelyezése különböző csoportablakba.**

    ![Védelem](./media/security-center-adaptive-application/adaptive-application-move-group.png)

 1. Jelölje ki azt a csoportot, ahhoz, amerre át szeretné helyezni a virtuális gépet, majd kattintson a **Számítógép áthelyezése**gombra, majd a **Mentés**gombra.

    ![Védelem](./media/security-center-adaptive-application/adaptive-application-move-group2.png)

 > [!NOTE]
> A **Számítógép áthelyezése**gombra kattintás után kattintson a **Mentés** gombra. Ha nem kattint a **Mentés**gombra, akkor a számítógép nem kerül áthelyezésre.

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan használhatja az adaptív alkalmazásvezérlést az Azure Security Centerben az Azure-ban és a nem Azure-beli virtuális gépeken futó alkalmazások engedélyezési listájához. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). A Security Center-riasztások kezelését és a biztonsági eseményekre való válaszadást ismertető útmutató.
* [Biztonsági állapotfigyelés az Azure Security Centerben.](security-center-monitoring.md) Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [A biztonsági riasztások ismertetése az Azure Security Centerben.](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) A különböző típusú biztonsági riasztásokat ismertető útmutató.
* [Az Azure Security Center hibaelhárítási útmutatója](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). A Security Center gyakori problémáinak elhárítását ismereti.
* [Az Azure Biztonsági Blog](https://blogs.msdn.com/b/azuresecurity/). Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
