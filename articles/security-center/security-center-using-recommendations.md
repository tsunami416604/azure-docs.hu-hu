---
title: A megfelelő biztonság érdekében használata Azure Security Center javaslatait |} Microsoft Docs
description: " Útmutató biztonsági szabályzatok és javaslatok az Azure Security Centerben a biztonsági támadások csökkentése érdekében. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: terrylan
ms.openlocfilehash: 0616f5e501324bfd821c1455ce234602f1fcf1bd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866666"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>A megfelelő biztonság érdekében használja az Azure Security Center javaslatok
Jelentős biztonsági esemény esélyét csökkentheti a biztonsági házirend konfigurálása, majd az Azure Security Center által nyújtott ajánlások végrehajtási. Ez a cikk bemutatja, hogyan biztonsági szabályzatok és javaslatok a Security Center használatához segítséget nyújtanak az olyan biztonsági támadások.

> [!NOTE]
> Ez a cikk épít, a szerepkörök és a Security Center az bemutatott fogalmakkal [tervezési és műveletek útmutató](security-center-planning-and-operations-guide.md). Célszerű folytatás előtt ellenőrizze a tervezési útmutatóban talál.
>
>

## <a name="managing-security-recommendations"></a>Biztonsági javaslatok kezelése
Egy biztonsági házirend határozza meg a megadott előfizetéshez vagy erőforráscsoporthoz erőforrások ajánljuk, hogy meg. A Security Center határozza meg a vállalat biztonsági követelményeinek megfelelően szabályzatokat. További tudnivalókért lásd: [biztonsági házirendek beállítása a Security Center](security-center-policies.md).

Biztonsági házirendek erőforráscsoportok örökölte az előfizetés szintjéről.

![Biztonsági házirend öröklése][1]

Ha egyéni házirendek adott erőforráscsoportban van szüksége, letilthatja öröklési erőforráscsoportban. Letiltásához egyedi öröklési értékűre a biztonsági szabályzat panel, és szabja testre a vezérlők, a Security Center bemutatja a javaslatokat.

Például ha az SQL adatbázis átlátszó Data Encryption (TDE) házirend nem igénylő munkaterhelések, kapcsolja ki a szabályzatot az előfizetés szintjén, és csak azoknál az Erőforráscsoportoknál ahol szükség az engedélyezéshez.

> [!NOTE]
> Az előfizetés szintű és az erőforráscsoport szintű szabályzat közötti ütközés esetén az erőforráscsoport szintű szabályzat élvez elsőbbséget.
>
>

A Security Center elemzi az Azure-erőforrások biztonsági állapotát. Amikor a Security Center a potenciális biztonsági hiányosságokat azonosít, állítsa be a biztonsági szabályzatban vezérlők meg ajánlásainkat hoz létre. A javaslatok végigvezeti Önt a szükséges biztonsági vezérlők folyamatán.

A Security Center arra utalnak, Rendszerfrissítések, operációs rendszer konfigurációja aktuális ajánlások hálózati alhálózatok és virtuális gépek (VM), az SQL Database Auditing, SQL-adatbázis TDE, biztonsági csoportok, és a webalkalmazás tűzfalak. A Security Center javaslatait legfrissebb körét, lásd: [a Security Center biztonsági javaslatok kezelése](security-center-recommendations.md).

## <a name="scenario"></a>Forgatókönyv
Ez a forgatókönyv bemutatja, hogyan jelentős biztonsági incidensek esélyét csökkentheti a Security Center javaslatait figyelése és a művelet a Security Center segítségével. A forgatókönyv használ a fiktív cég, a Contoso és a szerepkörök állnak rendelkezésre, a Security Center [tervezési és műveletek útmutató](security-center-planning-and-operations-guide.md#security-roles-and-access-controls). A szerepkörök olyan, személyek és csoportok, amelyek használhatják a Security Center különböző biztonsági feladatok végrehajtására. A szerepkörök a következők:

![A forgatókönyv szerepkörök][2]

Contoso nemrég telepítettek át a helyszíni erőforrások az Azure-bA. A Contoso bevezetéséhez és karbantartásához, amelyekkel csökkenthető a biztonsági rés a felhőalapú erőforrások biztonsági támadásnak védelmet biztosítani.

## <a name="recommended-solution"></a>Javasolt megoldás
A megoldás, hogy használja a Security Center és biztonsági rések észlelése érdekében. Contoso hozzáféréssel rendelkezik a Security Center az Azure-előfizetés útján. A [ingyenes szint](security-center-pricing.md) a Security Center automatikusan engedélyezve van minden Azure-előfizetés és adatgyűjtés engedélyezve van az előfizetésben szereplő összes virtuális.

David a Contoso informatikai biztonsági, konfigurálja a **biztonsági házirend** biztonsági központ használatával. A Security Center elemzi a Contoso Azure-erőforrások biztonsági állapotát. Amikor a Security Center a potenciális biztonsági hiányosságokat azonosít, az alkalmazás létrehozza **javaslatok** a vezérlők beállítani a biztonsági házirend alapján.

Jeff, a felhő számítási feladatok felelőse, végrehajtási és karbantartása a Contoso-biztonsági házirendek megfelelően védelmet felelős. Jeff figyelheti a javaslatok, a Security Center által létrehozott védelmet alkalmazni. A javaslatok Jeff végigvezeti a szükséges biztonsági vezérlők folyamatán.

Ahhoz, hogy Jeff megvalósításához és fenntartásához védelmet, és biztonsági rés megszüntetéséhez hogy kell megfelelnie:

- A Security Center által biztosított biztonsági javaslatok figyelése
- Értékelje ki a biztonsági javaslatok, és döntse el, ha azt kell alkalmazni, vagy hagyja figyelmen kívül
- Biztonsági javaslatok alkalmazása

Most lépésekkel Jeff tartozó tekintse meg, hogyan használja Security Center javaslatait őt irányítja a folyamatot, amely a biztonsági rés megszüntetéséhez vezérlők konfigurálása.

## <a name="how-to-implement-this-solution"></a>A megoldás megvalósítása
Bejelentkezik Jeff [Azure-portálon](https://azure.microsoft.com/features/azure-portal/) és a Security Center konzol megnyitása. A napi figyelési tevékenységek részeként Phil ellenőrzi, hogy találhatók-e biztonsági javaslatok az alábbi lépések elvégzésével:

1. Jeff kiválasztja a **javaslatok** csempére kattintva nyissa meg **javaslatok**.
   ![A javaslatok csempe kiválasztása][3]
2. Jeff ellenőrzi, hogy a javaslatok listája. Azt látja, hogy a Security Center megadta-e a prioritási sorrendben szerepel, a legalacsonyabb prioritású virtuális gép a legmagasabb prioritású javaslatok listája. Eldönti, hogy a listában egy magas prioritású javaslat megoldása. Kiválasztja **Endpoint Protection telepítése** alatt **javaslatok**.
3. **Az Endpoint Protection telepítése** megnyílik a virtuális gépek listájának megjelenítése nélkül kártevőirtó engedélyezve van. Jeff ellenőrzi, hogy a virtuális gépek listáját, kiválasztja az összes virtuális gépet, és ezután kiválasztja **3 virtuális gép telepítése**.
   ![Végpontvédelem telepítése][4]
4. **Válassza ki az Endpoint Protection** megnyílik, így a Jeff két kártevőirtó megoldások. Jeff kiválasztja a **Microsoft Antimalware** megoldás.
5. A kártevőirtó megoldást további információkat jelenít meg. Jeff kiválasztja **létrehozása**.
   ![A Microsoft antimalware][5]
6. Jeff kerül, a szükséges konfigurációs beállításokat **telepítése** és kijelöli **OK**.

[A Microsoft Antimalware](../security/azure-security-antimalware.md) aktív a kijelölt virtuális gépeken.

Jeff továbbra is a magas prioritású virtuális gép és a közepes prioritású virtuális gép javaslatok végighaladhat döntések végrehajtásáról. Jeff hivatkozik a [biztonsági javaslatok kezelése](security-center-recommendations.md) megtudhatja, hogy a javaslatok és mindegyiket egy funkciója alkalmazza, ha a cikk.

Jeff Tanulja meg, amely [Microsoft biztonsági válasz Center (MSRC)](../security/azure-security-response-center.md) hajt végre, válassza ki a biztonság ellenőrzése az Azure-hálózatot és az infrastruktúra, és fenyegetést eszközintelligencia és visszaélés panaszokat kapott harmadik felek számára. Ha Jeff biztonsági kapcsolattartási részletes adatokat biztosít a Contoso Azure-előfizetéssel, Microsoft-ügyfelek Contoso, ha a MSRC észleli, hogy a Contoso ügyféladatok elérése egy törvénybe ütköző vagy jogosulatlan félnek. Most kövesse Jeff alkalmazza a **adja meg biztonsági kapcsolattartási adatait** javaslat (ajánlást kiegészített közepes a fenti ajánlásokat közül).

1. Jeff kiválasztja **adja meg biztonsági kapcsolattartási adatait** alatt **javaslatok**, ekkor **adja meg biztonsági kapcsolattartási adatait**.
2. Jeff kapcsolattartási adatok megadása az Azure-előfizetést választja ki. Egy második **adja meg biztonsági kapcsolattartási adatait** panel nyílik meg.
   ![Biztonsági adatai][6]
3. A **adja meg biztonsági kapcsolattartási adatait**, Jeff kerül:

  - a biztonsági kapcsolattartási e-mail címek vesszővel (nincs korlátozva, adja meg e-mail címek száma)
  - egy biztonsági telefonszám

4. Jeff is a beállítás bekapcsolása **küldése e-mailt küld kapcsolatos riasztások** kapcsolatos magas súlyosságú riasztások e-mailek fogadásához.
5. Jeff kiválasztja **OK** biztonsági kapcsolattartási adatainak alkalmazhat a Contoso-előfizetés.

Végezetül Jeff ellenőrzi, hogy az alacsony prioritás ajánlás **szervizelje az operációs rendszer biztonsági rések** és megállapítja, hogy ez a javaslat nem alkalmazható. Szeretné elvetni a javaslat. Jeff kiválasztása a jobb oldalon megjelenő három pontra, és ezután kiválasztja **leállítási**.
   ![Hagyja figyelmen kívül a javaslat][7]

## <a name="conclusion"></a>Összegzés
A Security Center javaslatait figyelése segíthet a biztonsági rés megszüntetéséhez előtt a támadás akkor fordul elő. Megakadályozhatja, hogy a biztonsági incidensek megvalósításával és fenntartásával a Security Center biztonsági szabályzatokkal védelmet.

## <a name="next-steps"></a>Következő lépések
Ebben a forgatókönyvben a biztonsági szabályzatok és javaslatok használata a Security Center segítséget nyújtanak az olyan biztonsági támadások bemutatta. Tekintse meg a [incidensválasz-forgatókönyv](security-center-incident-response.md) megtudhatja, hogyan lehet az incidensekre adott reakciók helyen megtervezéséről, mielőtt a támadás akkor történik meg.

Security Centerrel kapcsolatos további tudnivalókért lásd:

* [Biztonsági állapotfigyelés](security-center-monitoring.md) – útmutató az Azure-erőforrások állapotának figyelésére.
* [Kezelése és válaszadás a biztonsági riasztások](security-center-managing-and-responding-alerts.md) – útmutató kezelése és válaszadás a biztonsági riasztásokra.
* [Figyelés és a biztonsági események feldolgozását](security-center-events-dashboard.md) – megtudhatja, hogyan figyelheti, és folyamatosan folyamat biztonsági eseményeket gyűjti.
* [Partnermegoldások figyelése](security-center-partner-solutions.md) – megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.
* [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center: Gyakran ismételt kérdések) – Válaszok a szolgáltatás használatára vonatkozó gyakori kérdésekre.
* [Az Azure biztonsági blog](http://blogs.msdn.com/b/azuresecurity/) – a legújabb Azure biztonsági híreket és információkat.

<!--Image references-->
[1]: ./media/security-center-using-recommendations/security-center-policy-inheritance.png
[2]: ./media/security-center-using-recommendations/scenario-roles.png
[3]: ./media/security-center-using-recommendations/select-recommendations-tile.png
[4]: ./media/security-center-using-recommendations/install-endpoint-protection.png
[5]:./media/security-center-using-recommendations/microsoft-antimalware.png
[6]: ./media/security-center-using-recommendations/provide-security-contact-details.png
[7]: ./media/security-center-using-recommendations/dismiss-recommendation.png
